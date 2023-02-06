## 📕 오늘 읽은 내용, 이런 식으로 정리해 봅시다. ✒

**TIL(Today I learn) 기록일** : 2022.02.06

**오늘 읽은 범위** : 31. 타입 주변에 null 값 배치하기

# Item 31. 타입 주변에 null 값 배치하기


stricNullChecks 설정을 처음 켜면, null이나 undefined 값 관련된 오류들이 갑자기 나타나기 때문에, 오류를 걸러내는 if 구문을 코드 전체에 추가해야 한다고 생각할 수도 있다.   

   
값이 전부 null이거나 전부 null이 아닌 경우로 분병히 구분된다면, 값이 섞여 있을 때보다 다루기 쉽다.   
타입에 null을 추가하는 방식으로 이러한 경우를 모델링할 수 있다.   
   
>숫자들의 최솟값과 최댓값을 계산하는 extent 함수를 가정해 보자.   
```ts
function extent(nums: number[]) {
  let min, max;
  for (const num of nums) {
    if (!min) {
      min = num;
      max = num;
    } else {
      min = Math.min(min, num);
      max = Math.max(max, num);
    }
  }
  return [min, max];
}
```

이 코드에는 버그와 함께 설계쩍 결함이 있다.  
- 최솟값이나 최댓값이 0인 경우, 값이 덧씌워져 버린다.   예를 들어, extent([0,1,2])의 결과는 [0, 2]가 아니라 [1,2]가 된다.
- nums 배열이 비어 있다면 함수는 [undefined, undefined]를 반환한다.

undefined를 포함하는 객체는 다루기 어렵고 절대 권장하지 않는다.   
코드를 살펴보면 min과 max가 동시에 둘 다 undefined이거나 둘 다 undefined가 아니라는 것을 알 수 있지만, 이러한 정보는 타입 시스템에서 표현할 수 없다.   
   
strictNullChecks 설정을 켜면 앞의 두 가지 문제점이 드러난다.   
   
 ```ts
 function extent(nums: number[]) {
  let min, max;
  for (const num of nums) {
    if (!min) {
      min = num;
      max = num;
    } else {
      min = Math.min(min, num);
      max = Math.max(max, num);
                  // ~~~ Argument of type 'number | undefined' is not
                  //     assignable to parameter of type 'number'
    }
  }
  return [min, max];
}
 ```
 
 extent 함수의 오류는 undefined를 min에서만 제외했고 max에서는 제외하지 않았기 때문에 발생했다.    
 두 개의 변수는 동시에 초기화 되지만, 이러한 정보는 타입 시스템에서 표현할 수 없다.   
 max에 대한 체크를 추가해서 오류를 해결할 수도 있지만 버그가 두 배로 늘어난다.   
    
   
더 나은 해법을 찾아보자   
>min과 max를 한 객체 안에 넣고 null이거나 null이 아니게 하면 된다.   
```ts

function extent(nums: number[]) {
  let result: [number, number] | null = null;
  for (const num of nums) {
    if (!result) {
      result = [num, num];
    } else {
      result = [Math.min(num, result[0]), Math.max(num, result[1])];
    }
  }
  return result;
}
// 이제는 반환 타입이 [number, number] | null이 되어서 사용하기가 더 수월해 졌다.
// null 아님 단언(!)을 사용하면 min과 max를 얻을 수 있다.
const [min, max] = extent([0, 1, 2])!;
const span = max - min;  // OK
```
> null아님 단언 대신 단순 if 구문으로 체크할 수도 있다.
```ts
const range = extent([0, 1, 2]);
if (range) {
  const [min, max] = range;
  const span = max - min;  // OK
}
```


<br>

null과 null이 아닌 값을 섞어서 사용하면 클래스에서도 문제가 생긴다.   
> 예를 들어 사용자와 그 사용자의 포럼 게시글을 나타내는 클래스를 가정해보자
```ts
interface UserInfo { name: string }
interface Post { post: string }
declare function fetchUser(userId: string): Promise<UserInfo>;
declare function fetchPostsForUser(userId: string): Promise<Post[]>;
class UserPosts {
  user: UserInfo | null;
  posts: Post[] | null;

  constructor() {
    this.user = null;
    this.posts = null;
  }

  async init(userId: string) {
    return Promise.all([
      async () => this.user = await fetchUser(userId),
      async () => this.posts = await fetchPostsForUser(userId)
    ]);
  }

  getUserName() {
    // ...?
  }
}
```

두 번의 네트워크 요청이 로드되는 동안 user와 posts 속성은 null 상태다.   
어떤 시점에는 둘 다 null이거나, 둘 중 하나만 null이거나 둘 다 null이 아닐 것이다.   
총 네 가지 경우가 존재한다.   
속성값의 불확실성이 클래스의 모든 메서드에 나쁜 영향을 미친다.   
결국 null 체크가 난무하고 버그를 양산하게된다.   
     
설계를 개선해 보자   
>필요한 데이터가 모두 준비된 후에 클래스를 만들도록 바꿔보자.   
```ts
interface UserInfo { name: string }
interface Post { post: string }
declare function fetchUser(userId: string): Promise<UserInfo>;
declare function fetchPostsForUser(userId: string): Promise<Post[]>;
class UserPosts {
  user: UserInfo;
  posts: Post[];

  constructor(user: UserInfo, posts: Post[]) {
    this.user = user;
    this.posts = posts;
  }

  static async init(userId: string): Promise<UserPosts> {
    const [user, posts] = await Promise.all([
      fetchUser(userId),
      fetchPostsForUser(userId)
    ]);
    return new UserPosts(user, posts);
  }

  getUserName() {
    return this.user.name;
  }
}
```
이제 UserPosts 클래스는 완전히 null이 아니게 되었고, 메서드를 작성하기 쉬워졌다.   
물론 이 경우에도 데이터가 부분적으로 준비되었을 때 작업을 시작해야 한다면 null과 null이 아닌 경우의 상태를 다루어야 한다.   
(null인 경우가 필요한 속성은 프로미스로 바꾸면 안된다. 코드가 매우 복잡해지며 모든 메서드가 비동기로 바뀌어야 한다. 프로미스는 데이터를 로드하는 코드를 단순하게 만들어 주지만, 데이터를 사용하는 클래스에서는 반대로 코드가 복잡해지는 효과를 내기도 한다)   

## 요약

- 한 값의 null 여부가 다른 값의 null 여부에 암시적으로 관련되도록 설계하면 안된다.   
- API 작성 시에는 반환 타입을 큰 객체로 만들고 반환 타입 전체가 null이거나 null이 아니게 만들어야 한다. 사람과 타입 체커 모두에게 명료한 코드가 될 것이다. 
- 클래스를 만들 때는 필요한 모든 값이 준비되었을 때 생성하여 null이 존재하지 않도록 하는 것이 좋다.   
- strictNullChecks를 설정하면 코드에 많은 오류가 표시되겠지만, null 값과 관련된 문제점을 찾아낼 수 있기 때문에 반드시 필요하다.

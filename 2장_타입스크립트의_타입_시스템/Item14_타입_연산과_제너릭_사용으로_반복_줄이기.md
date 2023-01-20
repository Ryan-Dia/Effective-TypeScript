## 📕 오늘 읽은 내용, 이런 식으로 정리해 봅시다. ✒

**TIL(Today I learn) 기록일** : 2022.12.19

**오늘 읽은 범위** : 14. 타인 연산과 제너릭 사용으로 반복 줄이기


# Item 14. 타인 연산과 제너릭 사용으로 반복 줄이기

다음은 원기둥(cylinder)의 반지름과 높이, 표면적, 부피를 출력하는 코드이다.   
```ts
console.log('Cylinder 1 x 1 ',
  'Surface area:', 6.283185 * 1 * 1 + 6.283185 * 1 * 1,
  'Volume:', 3.14159 * 1 * 1 * 1);
console.log('Cylinder 1 x 2 ',
  'Surface area:', 6.283185 * 1 * 1 + 6.283185 * 2 * 1,
  'Volume:', 3.14159 * 1 * 2 * 1);
console.log('Cylinder 2 x 1 ',
  'Surface area:', 6.283185 * 2 * 1 + 6.283185 * 2 * 1,
  'Volume:', 3.14159 * 2 * 2 * 1);
```
비슷한 코드가 반복되어 있어 보기 불편하다.   
값과 상수가 반복되는 바람에 드러나지 않은 오류까지 가지고 있다.   
>이 코드에서 함수, 상수, 루프의 반복을 제거해보면 아래와 같다.
```ts
const surfaceArea = (r, h) => 2*Math.PI * r (r+h);
const volume = (r, h) => Math.PI * r * r * h;
for (const [r, h] of [[1, 1],[1, 2],[2, 1]]) {
  console.log(
    `Cylinder ${r} x ${h}`,
    `Surface area: ${surfaceArea(r, h)}`,
    `Volume: ${volume(r, h)}`);
}
```

이게 바로 같은 코드를 반복하지 말라는 DRY(don't repeat yourself)원칙이다.   
그런데 반복된 코드를 열심히 제거하며 DRY 원칙을 지켜왔던 개발자라도 타입에 대해서는 간과했을지 모른다.  
```ts
interface Person {
  firstName: string;
  lastName: string;
}

interface PersonWithBirthDate {
  firstName: string;
  lastName: string;
  birth: Date;
}
```
타입 중복은 코드 중복만큼 많은 문제를 발생시킨다.   
예를 들어 선택적 필드인 middleName을 person에 추가한다고 가정해보면 Person과 PersonWithBirthDate는 다른 타입이 된다.   
   
타입에서 중복이 더 흔학 이유 중 하나는 공유된 패턴을 제거하는 메커니즘이 기존 코드에서 하던 것과 비교해 덜 익숙하기 때문이다.   
헬퍼 함수 중복 제거와 동일한 활동이 타입 시스템에서는 어떤 것에 해당할지 상상이 잘 되지 않는다.   
그러나 타입 간에 매핑하는 방법을 익히면, 타입 정의에서도 DRY의 장점을 적용할 수 있습니다.   
반복을 줄이는 가장 간단한 방법은 타입에 이름을 붙이는 것이다.   
>다음 예제의 거리 계산 함수에는 타입이 반복적으로 등장한다.
```ts
function distance(a: {x: number, y: number}, b: {x: number, y: number}) {
  return Math.sqrt(Math.pow(a.x - b.x, 2) + Math.pow(a.y - b.y, 2));
}
```
> 코드를 수정해 타입에 이름을 붙여 본다면 아래와 같다.
```ts
interface Point2D {
  x: number;
  y: number;
}
function distance(a: Point2D, b: Point2D) { /* ... */ }
```
이 코드는 상수를 사용해서 반복을 줄이는 기법을 동힐하게 타입 시스템에 적용한 것이다.   
중복된 타입 찾기가 항상 쉬운 일은 아니다.   
중복된 타입은 종종 문법에 의해서 가려지기도 한다.   
예를 들어, 몇몇 함수가 같은 타입 시그니처를 공유한다고 해보자.
```ts
// HIDE
interface Options {}
// END
function get(url: string, opts: Options): Promise<Response> { /* COMPRESS */ return Promise.resolve(new Response()); /* END */ }
function post(url: string, opts: Options): Promise<Response> { /* COMPRESS */ return Promise.resolve(new Response()); /* END */ }
```
>그러면 해당 시그니처를 명명된 타입으로 분리해 낼 수 있다.   
```ts
// HIDE
interface Options {}
// END
type HTTPFunction = (url: string, options: Options) => Promise<Response>;
const get: HTTPFunction = (url, options) => { /* COMPRESS */ return Promise.resolve(new Response()); /* END */ };
const post: HTTPFunction = (url, options) => { /* COMPRESS */ return Promise.resolve(new Response()); /* END */ };
```
>Person/PersonWithBirthDate 예제에서는 한 인터페이스가 다른 인터페이스를 확장하게 해서 반복을 제거할 수 있다.   
```ts
interface Person {
  firstName: string;
  lastName: string;
}

interface PersonWithBirthDate extends Person {
  birth: Date;
}
```
이제 추가적인 필드만 작성하면 된다.   
만약 두 인터페이스가 필드의 부분 집합을 공유한다면, 공통 필드만 골라서 기반 클래스로 분리해 낼 수 있다.   
코드 중복의 경우와 비교하면, 3.141593과 6.283185 대신에 PI와 2*PI로 작성하는 것과 비슷한 이치이다.   
  
>이미 존재하는 타입을 확장하는 경우에, 일반적이지 않지만 인터섹션 연산자(&)를 쓸 수도 있다.   
type PersonWithBirthDate = Person & { birth: Date };
이런 기법은 유니온 타입(확장할 수 없는)에 속성을 추가하려고 할 때 특히 유용하다.   
    
다른 측면을 생각해 보자.   
>전체 애플리케이션의 상태를 표현하는 State 타입과 단지 부분만 표현하는 TopNavState가 있는 경우
```ts

interface State {
  userId: string;
  pageTitle: string;
  recentFiles: string[];
  pageContents: string;
}
interface TopNavState {
  userId: string;
  pageTitle: string;
  recentFiles: string[];
}
```

TopNavState를 확장하여 State를 구성하기보다, State의 부분 집합으로 TopNavState를 정의하는 것이 바람직해 보인다.   
이 방법이 전체 앱의 상태를 하나의 인터페이스로 유지할 수 있게 해준다.   
    
    
>State를 인덱싱하여 속성의 타입에서 중복을 제거할 수 있다.   
```ts
interface State {
  userId: string;
  pageTitle: string;
  recentFiles: string[];
  pageContents: string;
}
type TopNavState = {
  userId: State['userId'];
  pageTitle: State['pageTitle'];
  recentFiles: State['recentFiles'];
};
```

중복 제거가 아직 끝나지 않았다. State 내의 pageTitle의 타입이 바뀌면 TopNavState에도 반영된다.   
그러나 여전히 반복되는 코드가 존재합니다.   
>이때 '매핑된 타입'을 사용하면 좀 더 나아진다.
```ts
interface State {
  userId: string;
  pageTitle: string;
  recentFiles: string[];
  pageContents: string;
}
type TopNavState = {
  [k in 'userId' | 'pageTitle' | 'recentFiles']: State[k]
};
```
TopNavState에 마우스를 올리면 정의가 표시되는데, 이 정의는 앞의 예제와 완전히 동일하다.   
매핑된 타입은 배열의 필드를 루프 도는 것과 같은 방식이다.   
이 패턴은 표준 라이브러리에서도 일반적으로 찾을 수 있으며, Pick이라고 한다.   
```ts
type Pick<T, K> = { [k in K] : T[k] };
```
>정의가 완전하지는 않지만 다음과 같이 사용할 수 있다.
```ts
interface State {
  userId: string;
  pageTitle: string;
  recentFiles: string[];
  pageContents: string;
}
type TopNavState = Pick<State, 'userId' | 'pageTitle' | 'recentFiles'>;
```
여기서 Pick은 제너릭 타입이다.   
중복된 코드를 없앤다는 관점으로 , Pick을 사용하는 것은 함수를 호출하는 것에 비유할 수 잇다.   
마치 함수에서 두 개의 매개변수 값을 받아서 결과값을 반환하는 것처럼, Pick은 T와 K 두 가지 타입을 받아서 결과 타입을 반환한다.   
   
태그된 유니온에서도 다른 형태의 중복이 발생할 수 있다.   
그런데 단순히 태그를 붙이기 위해서 타입을 사용한다면 어떨지 생각해보자.   
```ts
interface SaveAction {
  type: 'save';
  // ...
}
interface LoadAction {
  type: 'load';
  // ...
}
type Action = SaveAction | LoadAction;
type ActionType = 'save' | 'load';  // Repeated types!
```
Action 유니온을 인덱싱하면 타입 반복 없이 ActionType을 정의할 수 있다.   
```ts
type ActionType = Action['type']; // 타입은 "save" | "load"
```
Action 유니온에 타입을 더 추가하면 ActionType은 자동적으로 그 타입을 포함한다.   
ActionType은 Pick을 사용하여 얻게 되는, type 속성을 가지는 인터페이스와는 다르다.   
```ts
type ActionRec = Pick<Action, 'type'>; // {type: "save | "load" }
```


---

생성하고 난 다음에 업데이트가 되는 클래스를 정의한다면, update 메서드 매개변수의 타입은 생성자와 동일한 매개변수이면서,   
타입 대부분이 선택적 필드가 된다.
```ts
interface Options {
  width: number;
  height: number;
  color: string;
  label: string;
}
interface OptionsUpdate {
  width?: number;
  height?: number;
  color?: string;
  label?: string;
}
class UIWidget {
  constructor(init: Options) { /* ... */ }
  update(options: OptionsUpdate) { /* ... */ }
}
```
>매핑된 타입과 keyof를 사용하면 Options으로부터 OptionsUpdate를 만들 수 있다.   
```ts
interface Options {
  width: number;
  height: number;
  color: string;
  label: string;
}
type OptionsUpdate = {[k in keyof Options]?: Options[k]};


type OptionsKeys = keyof Options;
// Type is "width" | "height" | "color" | "label"
```
- keyof는 타입을 받아서 속성 타입의 유니온을 반환한다.   
`매핑된 타입([k in keyof Options])`은 순회하며 `Options` 내 `k`값에 해당하는 속성이 있는지 찾는다.   
?는 각 속성을 선택적으로 만든다.   
>이 패턴 역시 아주 일반적이며 표준 라이브러리에 Partial이라는 이름으로 포함되어 있다.   
```ts
interface Options {
  width: number;
  height: number;
  color: string;
  label: string;
}
class UIWidget {
  constructor(init: Options) { /* ... */ }
  update(options: Partial<Options>) { /* ... */ }
}
```

    
## 값의 형태에 해당하는 타입을 정의하고 싶을 때

```ts
const INIT_OPTIONS = {
  width: 640,
  height: 480,
  color: '#00FF00',
  label: 'VGA',
};
interface Options {
  width: number;
  height: number;
  color: string;
  label: string;
}
```
>이런 경우 `typeof`를 사용하면 된다.
```ts
type Options = typeof INIT_OPTIONS;
```
이 코드는 자바스크립트의 런타임 연산자 typeof를 사용한 것처럼 보이지만, 실제로는 타입스크립트 단계에서 연산되며 훨씬 더 정확하게 타입을 표현한다.   
그런데 값으로부터 타입을 만들어 낼 때는 선언의 순서에 주의해야한다.   
타입 정의를 먼저하고 값이 그 타입에 할당 가능하다고 선언하는 것이 좋다.   
그렇게 해야 타입이 더 명확해지고, 예상하기 어려운 타입 변동을 방지할 수 있다.   

---

## 함수나 메서드의 반환값에 명명된 타입을 만들고 싶을 때  
```ts
function getUserInfo(userId: string) {
  // COMPRESS
  const name = 'Bob';
  const age = 12;
  const height = 48;
  const weight = 70;
  const favoriteColor = 'blue';
  // END
  return {
    userId,
    name,
    age,
    height,
    weight,
    favoriteColor,
  };
}
// 추론된 반환 타입은 { userId: string; name: string; age: number, ... }
```

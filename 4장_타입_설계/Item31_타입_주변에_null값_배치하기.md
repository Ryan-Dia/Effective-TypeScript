## ๐ ์ค๋ ์ฝ์ ๋ด์ฉ, ์ด๋ฐ ์์ผ๋ก ์ ๋ฆฌํด ๋ด์๋ค. โ

**TIL(Today I learn) ๊ธฐ๋ก์ผ** : 2022.02.06

**์ค๋ ์ฝ์ ๋ฒ์** : 31. ํ์ ์ฃผ๋ณ์ null ๊ฐ ๋ฐฐ์นํ๊ธฐ

# Item 31. ํ์ ์ฃผ๋ณ์ null ๊ฐ ๋ฐฐ์นํ๊ธฐ


stricNullChecks ์ค์ ์ ์ฒ์ ์ผ๋ฉด, null์ด๋ undefined ๊ฐ ๊ด๋ จ๋ ์ค๋ฅ๋ค์ด ๊ฐ์๊ธฐ ๋ํ๋๊ธฐ ๋๋ฌธ์, ์ค๋ฅ๋ฅผ ๊ฑธ๋ฌ๋ด๋ if ๊ตฌ๋ฌธ์ ์ฝ๋ ์ ์ฒด์ ์ถ๊ฐํด์ผ ํ๋ค๊ณ  ์๊ฐํ  ์๋ ์๋ค.   

   
๊ฐ์ด ์ ๋ถ null์ด๊ฑฐ๋ ์ ๋ถ null์ด ์๋ ๊ฒฝ์ฐ๋ก ๋ถ๋ณํ ๊ตฌ๋ถ๋๋ค๋ฉด, ๊ฐ์ด ์์ฌ ์์ ๋๋ณด๋ค ๋ค๋ฃจ๊ธฐ ์ฝ๋ค.   
ํ์์ null์ ์ถ๊ฐํ๋ ๋ฐฉ์์ผ๋ก ์ด๋ฌํ ๊ฒฝ์ฐ๋ฅผ ๋ชจ๋ธ๋งํ  ์ ์๋ค.   
   
>์ซ์๋ค์ ์ต์๊ฐ๊ณผ ์ต๋๊ฐ์ ๊ณ์ฐํ๋ extent ํจ์๋ฅผ ๊ฐ์ ํด ๋ณด์.   
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

์ด ์ฝ๋์๋ ๋ฒ๊ทธ์ ํจ๊ป ์ค๊ณ์ฉ ๊ฒฐํจ์ด ์๋ค.  
- ์ต์๊ฐ์ด๋ ์ต๋๊ฐ์ด 0์ธ ๊ฒฝ์ฐ, ๊ฐ์ด ๋ง์์์ ธ ๋ฒ๋ฆฐ๋ค.   ์๋ฅผ ๋ค์ด, extent([0,1,2])์ ๊ฒฐ๊ณผ๋ [0, 2]๊ฐ ์๋๋ผ [1,2]๊ฐ ๋๋ค.
- nums ๋ฐฐ์ด์ด ๋น์ด ์๋ค๋ฉด ํจ์๋ [undefined, undefined]๋ฅผ ๋ฐํํ๋ค.

undefined๋ฅผ ํฌํจํ๋ ๊ฐ์ฒด๋ ๋ค๋ฃจ๊ธฐ ์ด๋ ต๊ณ  ์ ๋ ๊ถ์ฅํ์ง ์๋๋ค.   
์ฝ๋๋ฅผ ์ดํด๋ณด๋ฉด min๊ณผ max๊ฐ ๋์์ ๋ ๋ค undefined์ด๊ฑฐ๋ ๋ ๋ค undefined๊ฐ ์๋๋ผ๋ ๊ฒ์ ์ ์ ์์ง๋ง, ์ด๋ฌํ ์ ๋ณด๋ ํ์ ์์คํ์์ ํํํ  ์ ์๋ค.   
   
strictNullChecks ์ค์ ์ ์ผ๋ฉด ์์ ๋ ๊ฐ์ง ๋ฌธ์ ์ ์ด ๋๋ฌ๋๋ค.   
   
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
 
 extent ํจ์์ ์ค๋ฅ๋ undefined๋ฅผ min์์๋ง ์ ์ธํ๊ณ  max์์๋ ์ ์ธํ์ง ์์๊ธฐ ๋๋ฌธ์ ๋ฐ์ํ๋ค.    
 ๋ ๊ฐ์ ๋ณ์๋ ๋์์ ์ด๊ธฐํ ๋์ง๋ง, ์ด๋ฌํ ์ ๋ณด๋ ํ์ ์์คํ์์ ํํํ  ์ ์๋ค.   
 max์ ๋ํ ์ฒดํฌ๋ฅผ ์ถ๊ฐํด์ ์ค๋ฅ๋ฅผ ํด๊ฒฐํ  ์๋ ์์ง๋ง ๋ฒ๊ทธ๊ฐ ๋ ๋ฐฐ๋ก ๋์ด๋๋ค.   
    
   
๋ ๋์ ํด๋ฒ์ ์ฐพ์๋ณด์   
>min๊ณผ max๋ฅผ ํ ๊ฐ์ฒด ์์ ๋ฃ๊ณ  null์ด๊ฑฐ๋ null์ด ์๋๊ฒ ํ๋ฉด ๋๋ค.   
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
// ์ด์ ๋ ๋ฐํ ํ์์ด [number, number] | null์ด ๋์ด์ ์ฌ์ฉํ๊ธฐ๊ฐ ๋ ์์ํด ์ก๋ค.
// null ์๋ ๋จ์ธ(!)์ ์ฌ์ฉํ๋ฉด min๊ณผ max๋ฅผ ์ป์ ์ ์๋ค.
const [min, max] = extent([0, 1, 2])!;
const span = max - min;  // OK
```
> null์๋ ๋จ์ธ ๋์  ๋จ์ if ๊ตฌ๋ฌธ์ผ๋ก ์ฒดํฌํ  ์๋ ์๋ค.
```ts
const range = extent([0, 1, 2]);
if (range) {
  const [min, max] = range;
  const span = max - min;  // OK
}
```


<br>

null๊ณผ null์ด ์๋ ๊ฐ์ ์์ด์ ์ฌ์ฉํ๋ฉด ํด๋์ค์์๋ ๋ฌธ์ ๊ฐ ์๊ธด๋ค.   
> ์๋ฅผ ๋ค์ด ์ฌ์ฉ์์ ๊ทธ ์ฌ์ฉ์์ ํฌ๋ผ ๊ฒ์๊ธ์ ๋ํ๋ด๋ ํด๋์ค๋ฅผ ๊ฐ์ ํด๋ณด์
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

๋ ๋ฒ์ ๋คํธ์ํฌ ์์ฒญ์ด ๋ก๋๋๋ ๋์ user์ posts ์์ฑ์ null ์ํ๋ค.   
์ด๋ค ์์ ์๋ ๋ ๋ค null์ด๊ฑฐ๋, ๋ ์ค ํ๋๋ง null์ด๊ฑฐ๋ ๋ ๋ค null์ด ์๋ ๊ฒ์ด๋ค.   
์ด ๋ค ๊ฐ์ง ๊ฒฝ์ฐ๊ฐ ์กด์ฌํ๋ค.   
์์ฑ๊ฐ์ ๋ถํ์ค์ฑ์ด ํด๋์ค์ ๋ชจ๋  ๋ฉ์๋์ ๋์ ์ํฅ์ ๋ฏธ์น๋ค.   
๊ฒฐ๊ตญ null ์ฒดํฌ๊ฐ ๋๋ฌดํ๊ณ  ๋ฒ๊ทธ๋ฅผ ์์ฐํ๊ฒ๋๋ค.   
     
์ค๊ณ๋ฅผ ๊ฐ์ ํด ๋ณด์   
>ํ์ํ ๋ฐ์ดํฐ๊ฐ ๋ชจ๋ ์ค๋น๋ ํ์ ํด๋์ค๋ฅผ ๋ง๋ค๋๋ก ๋ฐ๊ฟ๋ณด์.   
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
์ด์  UserPosts ํด๋์ค๋ ์์ ํ null์ด ์๋๊ฒ ๋์๊ณ , ๋ฉ์๋๋ฅผ ์์ฑํ๊ธฐ ์ฌ์์ก๋ค.   
๋ฌผ๋ก  ์ด ๊ฒฝ์ฐ์๋ ๋ฐ์ดํฐ๊ฐ ๋ถ๋ถ์ ์ผ๋ก ์ค๋น๋์์ ๋ ์์์ ์์ํด์ผ ํ๋ค๋ฉด null๊ณผ null์ด ์๋ ๊ฒฝ์ฐ์ ์ํ๋ฅผ ๋ค๋ฃจ์ด์ผ ํ๋ค.   
(null์ธ ๊ฒฝ์ฐ๊ฐ ํ์ํ ์์ฑ์ ํ๋ก๋ฏธ์ค๋ก ๋ฐ๊พธ๋ฉด ์๋๋ค. ์ฝ๋๊ฐ ๋งค์ฐ ๋ณต์กํด์ง๋ฉฐ ๋ชจ๋  ๋ฉ์๋๊ฐ ๋น๋๊ธฐ๋ก ๋ฐ๋์ด์ผ ํ๋ค. ํ๋ก๋ฏธ์ค๋ ๋ฐ์ดํฐ๋ฅผ ๋ก๋ํ๋ ์ฝ๋๋ฅผ ๋จ์ํ๊ฒ ๋ง๋ค์ด ์ฃผ์ง๋ง, ๋ฐ์ดํฐ๋ฅผ ์ฌ์ฉํ๋ ํด๋์ค์์๋ ๋ฐ๋๋ก ์ฝ๋๊ฐ ๋ณต์กํด์ง๋ ํจ๊ณผ๋ฅผ ๋ด๊ธฐ๋ ํ๋ค)   

## ์์ฝ

- ํ ๊ฐ์ null ์ฌ๋ถ๊ฐ ๋ค๋ฅธ ๊ฐ์ null ์ฌ๋ถ์ ์์์ ์ผ๋ก ๊ด๋ จ๋๋๋ก ์ค๊ณํ๋ฉด ์๋๋ค.   
- API ์์ฑ ์์๋ ๋ฐํ ํ์์ ํฐ ๊ฐ์ฒด๋ก ๋ง๋ค๊ณ  ๋ฐํ ํ์ ์ ์ฒด๊ฐ null์ด๊ฑฐ๋ null์ด ์๋๊ฒ ๋ง๋ค์ด์ผ ํ๋ค. ์ฌ๋๊ณผ ํ์ ์ฒด์ปค ๋ชจ๋์๊ฒ ๋ช๋ฃํ ์ฝ๋๊ฐ ๋  ๊ฒ์ด๋ค. 
- ํด๋์ค๋ฅผ ๋ง๋ค ๋๋ ํ์ํ ๋ชจ๋  ๊ฐ์ด ์ค๋น๋์์ ๋ ์์ฑํ์ฌ null์ด ์กด์ฌํ์ง ์๋๋ก ํ๋ ๊ฒ์ด ์ข๋ค.   
- strictNullChecks๋ฅผ ์ค์ ํ๋ฉด ์ฝ๋์ ๋ง์ ์ค๋ฅ๊ฐ ํ์๋๊ฒ ์ง๋ง, null ๊ฐ๊ณผ ๊ด๋ จ๋ ๋ฌธ์ ์ ์ ์ฐพ์๋ผ ์ ์๊ธฐ ๋๋ฌธ์ ๋ฐ๋์ ํ์ํ๋ค.

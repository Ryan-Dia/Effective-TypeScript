## ๐ ์ค๋ ์ฝ์ ๋ด์ฉ, ์ด๋ฐ ์์ผ๋ก ์ ๋ฆฌํด ๋ด์๋ค. โ

**TIL(Today I learn) ๊ธฐ๋ก์ผ** : 2022.12.19

**์ค๋ ์ฝ์ ๋ฒ์** : 14. ํ์ธ ์ฐ์ฐ๊ณผ ์ ๋๋ฆญ ์ฌ์ฉ์ผ๋ก ๋ฐ๋ณต ์ค์ด๊ธฐ


# Item 14. ํ์ธ ์ฐ์ฐ๊ณผ ์ ๋๋ฆญ ์ฌ์ฉ์ผ๋ก ๋ฐ๋ณต ์ค์ด๊ธฐ

๋ค์์ ์๊ธฐ๋ฅ(cylinder)์ ๋ฐ์ง๋ฆ๊ณผ ๋์ด, ํ๋ฉด์ , ๋ถํผ๋ฅผ ์ถ๋ ฅํ๋ ์ฝ๋์ด๋ค.   
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
๋น์ทํ ์ฝ๋๊ฐ ๋ฐ๋ณต๋์ด ์์ด ๋ณด๊ธฐ ๋ถํธํ๋ค.   
๊ฐ๊ณผ ์์๊ฐ ๋ฐ๋ณต๋๋ ๋ฐ๋์ ๋๋ฌ๋์ง ์์ ์ค๋ฅ๊น์ง ๊ฐ์ง๊ณ  ์๋ค.   
>์ด ์ฝ๋์์ ํจ์, ์์, ๋ฃจํ์ ๋ฐ๋ณต์ ์ ๊ฑฐํด๋ณด๋ฉด ์๋์ ๊ฐ๋ค.
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

์ด๊ฒ ๋ฐ๋ก ๊ฐ์ ์ฝ๋๋ฅผ ๋ฐ๋ณตํ์ง ๋ง๋ผ๋ DRY(don't repeat yourself)์์น์ด๋ค.   
๊ทธ๋ฐ๋ฐ ๋ฐ๋ณต๋ ์ฝ๋๋ฅผ ์ด์ฌํ ์ ๊ฑฐํ๋ฉฐ DRY ์์น์ ์ง์ผ์๋ ๊ฐ๋ฐ์๋ผ๋ ํ์์ ๋ํด์๋ ๊ฐ๊ณผํ์์ง ๋ชจ๋ฅธ๋ค.  
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
ํ์ ์ค๋ณต์ ์ฝ๋ ์ค๋ณต๋งํผ ๋ง์ ๋ฌธ์ ๋ฅผ ๋ฐ์์ํจ๋ค.   
์๋ฅผ ๋ค์ด ์ ํ์  ํ๋์ธ middleName์ person์ ์ถ๊ฐํ๋ค๊ณ  ๊ฐ์ ํด๋ณด๋ฉด Person๊ณผ PersonWithBirthDate๋ ๋ค๋ฅธ ํ์์ด ๋๋ค.   
   
ํ์์์ ์ค๋ณต์ด ๋ ํํ ์ด์  ์ค ํ๋๋ ๊ณต์ ๋ ํจํด์ ์ ๊ฑฐํ๋ ๋ฉ์ปค๋์ฆ์ด ๊ธฐ์กด ์ฝ๋์์ ํ๋ ๊ฒ๊ณผ ๋น๊ตํด ๋ ์ต์ํ๊ธฐ ๋๋ฌธ์ด๋ค.   
ํฌํผ ํจ์ ์ค๋ณต ์ ๊ฑฐ์ ๋์ผํ ํ๋์ด ํ์ ์์คํ์์๋ ์ด๋ค ๊ฒ์ ํด๋นํ ์ง ์์์ด ์ ๋์ง ์๋๋ค.   
๊ทธ๋ฌ๋ ํ์ ๊ฐ์ ๋งคํํ๋ ๋ฐฉ๋ฒ์ ์ตํ๋ฉด, ํ์ ์ ์์์๋ DRY์ ์ฅ์ ์ ์ ์ฉํ  ์ ์์ต๋๋ค.   
๋ฐ๋ณต์ ์ค์ด๋ ๊ฐ์ฅ ๊ฐ๋จํ ๋ฐฉ๋ฒ์ ํ์์ ์ด๋ฆ์ ๋ถ์ด๋ ๊ฒ์ด๋ค.   
>๋ค์ ์์ ์ ๊ฑฐ๋ฆฌ ๊ณ์ฐ ํจ์์๋ ํ์์ด ๋ฐ๋ณต์ ์ผ๋ก ๋ฑ์ฅํ๋ค.
```ts
function distance(a: {x: number, y: number}, b: {x: number, y: number}) {
  return Math.sqrt(Math.pow(a.x - b.x, 2) + Math.pow(a.y - b.y, 2));
}
```
> ์ฝ๋๋ฅผ ์์ ํด ํ์์ ์ด๋ฆ์ ๋ถ์ฌ ๋ณธ๋ค๋ฉด ์๋์ ๊ฐ๋ค.
```ts
interface Point2D {
  x: number;
  y: number;
}
function distance(a: Point2D, b: Point2D) { /* ... */ }
```
์ด ์ฝ๋๋ ์์๋ฅผ ์ฌ์ฉํด์ ๋ฐ๋ณต์ ์ค์ด๋ ๊ธฐ๋ฒ์ ๋ํํ๊ฒ ํ์ ์์คํ์ ์ ์ฉํ ๊ฒ์ด๋ค.   
์ค๋ณต๋ ํ์ ์ฐพ๊ธฐ๊ฐ ํญ์ ์ฌ์ด ์ผ์ ์๋๋ค.   
์ค๋ณต๋ ํ์์ ์ข์ข ๋ฌธ๋ฒ์ ์ํด์ ๊ฐ๋ ค์ง๊ธฐ๋ ํ๋ค.   
์๋ฅผ ๋ค์ด, ๋ช๋ช ํจ์๊ฐ ๊ฐ์ ํ์ ์๊ทธ๋์ฒ๋ฅผ ๊ณต์ ํ๋ค๊ณ  ํด๋ณด์.
```ts
// HIDE
interface Options {}
// END
function get(url: string, opts: Options): Promise<Response> { /* COMPRESS */ return Promise.resolve(new Response()); /* END */ }
function post(url: string, opts: Options): Promise<Response> { /* COMPRESS */ return Promise.resolve(new Response()); /* END */ }
```
>๊ทธ๋ฌ๋ฉด ํด๋น ์๊ทธ๋์ฒ๋ฅผ ๋ช๋ช๋ ํ์์ผ๋ก ๋ถ๋ฆฌํด ๋ผ ์ ์๋ค.   
```ts
// HIDE
interface Options {}
// END
type HTTPFunction = (url: string, options: Options) => Promise<Response>;
const get: HTTPFunction = (url, options) => { /* COMPRESS */ return Promise.resolve(new Response()); /* END */ };
const post: HTTPFunction = (url, options) => { /* COMPRESS */ return Promise.resolve(new Response()); /* END */ };
```
>Person/PersonWithBirthDate ์์ ์์๋ ํ ์ธํฐํ์ด์ค๊ฐ ๋ค๋ฅธ ์ธํฐํ์ด์ค๋ฅผ ํ์ฅํ๊ฒ ํด์ ๋ฐ๋ณต์ ์ ๊ฑฐํ  ์ ์๋ค.   
```ts
interface Person {
  firstName: string;
  lastName: string;
}

interface PersonWithBirthDate extends Person {
  birth: Date;
}
```
์ด์  ์ถ๊ฐ์ ์ธ ํ๋๋ง ์์ฑํ๋ฉด ๋๋ค.   
๋ง์ฝ ๋ ์ธํฐํ์ด์ค๊ฐ ํ๋์ ๋ถ๋ถ ์งํฉ์ ๊ณต์ ํ๋ค๋ฉด, ๊ณตํต ํ๋๋ง ๊ณจ๋ผ์ ๊ธฐ๋ฐ ํด๋์ค๋ก ๋ถ๋ฆฌํด ๋ผ ์ ์๋ค.   
์ฝ๋ ์ค๋ณต์ ๊ฒฝ์ฐ์ ๋น๊ตํ๋ฉด, 3.141593๊ณผ 6.283185 ๋์ ์ PI์ 2*PI๋ก ์์ฑํ๋ ๊ฒ๊ณผ ๋น์ทํ ์ด์น์ด๋ค.   
  
>์ด๋ฏธ ์กด์ฌํ๋ ํ์์ ํ์ฅํ๋ ๊ฒฝ์ฐ์, ์ผ๋ฐ์ ์ด์ง ์์ง๋ง ์ธํฐ์น์ ์ฐ์ฐ์(&)๋ฅผ ์ธ ์๋ ์๋ค.   
type PersonWithBirthDate = Person & { birth: Date };
์ด๋ฐ ๊ธฐ๋ฒ์ ์ ๋์จ ํ์(ํ์ฅํ  ์ ์๋)์ ์์ฑ์ ์ถ๊ฐํ๋ ค๊ณ  ํ  ๋ ํนํ ์ ์ฉํ๋ค.   
    
๋ค๋ฅธ ์ธก๋ฉด์ ์๊ฐํด ๋ณด์.   
>์ ์ฒด ์ ํ๋ฆฌ์ผ์ด์์ ์ํ๋ฅผ ํํํ๋ State ํ์๊ณผ ๋จ์ง ๋ถ๋ถ๋ง ํํํ๋ TopNavState๊ฐ ์๋ ๊ฒฝ์ฐ
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

TopNavState๋ฅผ ํ์ฅํ์ฌ State๋ฅผ ๊ตฌ์ฑํ๊ธฐ๋ณด๋ค, State์ ๋ถ๋ถ ์งํฉ์ผ๋ก TopNavState๋ฅผ ์ ์ํ๋ ๊ฒ์ด ๋ฐ๋์งํด ๋ณด์ธ๋ค.   
์ด ๋ฐฉ๋ฒ์ด ์ ์ฒด ์ฑ์ ์ํ๋ฅผ ํ๋์ ์ธํฐํ์ด์ค๋ก ์ ์งํ  ์ ์๊ฒ ํด์ค๋ค.   
    
    
>State๋ฅผ ์ธ๋ฑ์ฑํ์ฌ ์์ฑ์ ํ์์์ ์ค๋ณต์ ์ ๊ฑฐํ  ์ ์๋ค.   
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

์ค๋ณต ์ ๊ฑฐ๊ฐ ์์ง ๋๋์ง ์์๋ค. State ๋ด์ pageTitle์ ํ์์ด ๋ฐ๋๋ฉด TopNavState์๋ ๋ฐ์๋๋ค.   
๊ทธ๋ฌ๋ ์ฌ์ ํ ๋ฐ๋ณต๋๋ ์ฝ๋๊ฐ ์กด์ฌํฉ๋๋ค.   
>์ด๋ '๋งคํ๋ ํ์'์ ์ฌ์ฉํ๋ฉด ์ข ๋ ๋์์ง๋ค.
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
TopNavState์ ๋ง์ฐ์ค๋ฅผ ์ฌ๋ฆฌ๋ฉด ์ ์๊ฐ ํ์๋๋๋ฐ, ์ด ์ ์๋ ์์ ์์ ์ ์์ ํ ๋์ผํ๋ค.   
๋งคํ๋ ํ์์ ๋ฐฐ์ด์ ํ๋๋ฅผ ๋ฃจํ ๋๋ ๊ฒ๊ณผ ๊ฐ์ ๋ฐฉ์์ด๋ค.   
์ด ํจํด์ ํ์ค ๋ผ์ด๋ธ๋ฌ๋ฆฌ์์๋ ์ผ๋ฐ์ ์ผ๋ก ์ฐพ์ ์ ์์ผ๋ฉฐ, Pick์ด๋ผ๊ณ  ํ๋ค.   
```ts
type Pick<T, K> = { [k in K] : T[k] };
```
>์ ์๊ฐ ์์ ํ์ง๋ ์์ง๋ง ๋ค์๊ณผ ๊ฐ์ด ์ฌ์ฉํ  ์ ์๋ค.
```ts
interface State {
  userId: string;
  pageTitle: string;
  recentFiles: string[];
  pageContents: string;
}
type TopNavState = Pick<State, 'userId' | 'pageTitle' | 'recentFiles'>;
```
์ฌ๊ธฐ์ Pick์ ์ ๋๋ฆญ ํ์์ด๋ค.   
์ค๋ณต๋ ์ฝ๋๋ฅผ ์์ค๋ค๋ ๊ด์ ์ผ๋ก , Pick์ ์ฌ์ฉํ๋ ๊ฒ์ ํจ์๋ฅผ ํธ์ถํ๋ ๊ฒ์ ๋น์ ํ  ์ ์๋ค.   
๋ง์น ํจ์์์ ๋ ๊ฐ์ ๋งค๊ฐ๋ณ์ ๊ฐ์ ๋ฐ์์ ๊ฒฐ๊ณผ๊ฐ์ ๋ฐํํ๋ ๊ฒ์ฒ๋ผ, Pick์ T์ K ๋ ๊ฐ์ง ํ์์ ๋ฐ์์ ๊ฒฐ๊ณผ ํ์์ ๋ฐํํ๋ค.   
   
ํ๊ทธ๋ ์ ๋์จ์์๋ ๋ค๋ฅธ ํํ์ ์ค๋ณต์ด ๋ฐ์ํ  ์ ์๋ค.   
๊ทธ๋ฐ๋ฐ ๋จ์ํ ํ๊ทธ๋ฅผ ๋ถ์ด๊ธฐ ์ํด์ ํ์์ ์ฌ์ฉํ๋ค๋ฉด ์ด๋จ์ง ์๊ฐํด๋ณด์.   
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
Action ์ ๋์จ์ ์ธ๋ฑ์ฑํ๋ฉด ํ์ ๋ฐ๋ณต ์์ด ActionType์ ์ ์ํ  ์ ์๋ค.   
```ts
type ActionType = Action['type']; // ํ์์ "save" | "load"
```
Action ์ ๋์จ์ ํ์์ ๋ ์ถ๊ฐํ๋ฉด ActionType์ ์๋์ ์ผ๋ก ๊ทธ ํ์์ ํฌํจํ๋ค.   
ActionType์ Pick์ ์ฌ์ฉํ์ฌ ์ป๊ฒ ๋๋, type ์์ฑ์ ๊ฐ์ง๋ ์ธํฐํ์ด์ค์๋ ๋ค๋ฅด๋ค.   
```ts
type ActionRec = Pick<Action, 'type'>; // {type: "save | "load" }
```


---

์์ฑํ๊ณ  ๋ ๋ค์์ ์๋ฐ์ดํธ๊ฐ ๋๋ ํด๋์ค๋ฅผ ์ ์ํ๋ค๋ฉด, update ๋ฉ์๋ ๋งค๊ฐ๋ณ์์ ํ์์ ์์ฑ์์ ๋์ผํ ๋งค๊ฐ๋ณ์์ด๋ฉด์,   
ํ์ ๋๋ถ๋ถ์ด ์ ํ์  ํ๋๊ฐ ๋๋ค.
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
>๋งคํ๋ ํ์๊ณผ keyof๋ฅผ ์ฌ์ฉํ๋ฉด Options์ผ๋ก๋ถํฐ OptionsUpdate๋ฅผ ๋ง๋ค ์ ์๋ค.   
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
- keyof๋ ํ์์ ๋ฐ์์ ์์ฑ ํ์์ ์ ๋์จ์ ๋ฐํํ๋ค.   
`๋งคํ๋ ํ์([k in keyof Options])`์ ์ํํ๋ฉฐ `Options` ๋ด `k`๊ฐ์ ํด๋นํ๋ ์์ฑ์ด ์๋์ง ์ฐพ๋๋ค.   
?๋ ๊ฐ ์์ฑ์ ์ ํ์ ์ผ๋ก ๋ง๋ ๋ค.   
>์ด ํจํด ์ญ์ ์์ฃผ ์ผ๋ฐ์ ์ด๋ฉฐ ํ์ค ๋ผ์ด๋ธ๋ฌ๋ฆฌ์ Partial์ด๋ผ๋ ์ด๋ฆ์ผ๋ก ํฌํจ๋์ด ์๋ค.   
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

    
## ๊ฐ์ ํํ์ ํด๋นํ๋ ํ์์ ์ ์ํ๊ณ  ์ถ์ ๋

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
>์ด๋ฐ ๊ฒฝ์ฐ `typeof`๋ฅผ ์ฌ์ฉํ๋ฉด ๋๋ค.
```ts
type Options = typeof INIT_OPTIONS;
```
์ด ์ฝ๋๋ ์๋ฐ์คํฌ๋ฆฝํธ์ ๋ฐํ์ ์ฐ์ฐ์ typeof๋ฅผ ์ฌ์ฉํ ๊ฒ์ฒ๋ผ ๋ณด์ด์ง๋ง, ์ค์ ๋ก๋ ํ์์คํฌ๋ฆฝํธ ๋จ๊ณ์์ ์ฐ์ฐ๋๋ฉฐ ํจ์ฌ ๋ ์ ํํ๊ฒ ํ์์ ํํํ๋ค.   
๊ทธ๋ฐ๋ฐ ๊ฐ์ผ๋ก๋ถํฐ ํ์์ ๋ง๋ค์ด ๋ผ ๋๋ ์ ์ธ์ ์์์ ์ฃผ์ํด์ผํ๋ค.   
ํ์ ์ ์๋ฅผ ๋จผ์ ํ๊ณ  ๊ฐ์ด ๊ทธ ํ์์ ํ ๋น ๊ฐ๋ฅํ๋ค๊ณ  ์ ์ธํ๋ ๊ฒ์ด ์ข๋ค.   
๊ทธ๋ ๊ฒ ํด์ผ ํ์์ด ๋ ๋ชํํด์ง๊ณ , ์์ํ๊ธฐ ์ด๋ ค์ด ํ์ ๋ณ๋์ ๋ฐฉ์งํ  ์ ์๋ค.   

---

## ํจ์๋ ๋ฉ์๋์ ๋ฐํ๊ฐ์ ๋ช๋ช๋ ํ์์ ๋ง๋ค๊ณ  ์ถ์ ๋  
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
// ์ถ๋ก ๋ ๋ฐํ ํ์์ { userId: string; name: string; age: number, ... }
```

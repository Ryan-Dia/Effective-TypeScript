## π μ€λ μ½μ λ΄μ©, μ΄λ° μμΌλ‘ μ λ¦¬ν΄ λ΄μλ€. β

**TIL(Today I learn) κΈ°λ‘μΌ** : 2022.01.24

**μ€λ μ½μ λ²μ** : 18. λ§€νλ νμμ μ¬μ©νμ¬ κ°μ λκΈ°ννκΈ°

# Item 18 λ§€νλ νμμ μ¬μ©νμ¬ κ°μ λκΈ°ννκΈ°


μ°μ λ(scatter plot)λ₯Ό κ·Έλ¦¬κ³  μν UIμ»΄ν¬λνΈλ₯Ό μμ±νλ€κ³  κ°μ ν΄λ³΄μ   
μ¬κΈ°μλ λμ€νλ μ΄μ λμμ μ μ΄νκΈ° μν λͺ κ°μ§ λ€λ₯Έ νμμ μμ±μ΄ ν¬ν¨λλ€.   
```ts
interface ScatterProps {
  // The data
  xs: number[];
  ys: number[];

  // Display
  xRange: [number, number];
  yRange: [number, number];
  color: string;

  // Events
  onClick: (x: number, y: number, index: number) => void;
}
```
λΆνμν μμμ νΌνκΈ° μν΄, νμν  λμλ§ μ°¨νΈλ₯Ό λ€μ κ·Έλ¦΄ μ μλ€.   
λ°μ΄ν°λ λμ€νλ μ΄ μμ±μ΄ λ³κ²½λλ©΄ λ€μ κ·Έλ €μΌ νμ§λ§, μ΄λ²€νΈ νΈλ€λ¬κ° λ³κ²½λλ©΄ λ€μ κ·Έλ¦΄ νμκ° μλ€.   
μ΄λ° μ’λ₯μ μ΅μ νλ λ¦¬μ‘νΈ μ»΄ν¬λνΈμμλ μΌλ°μ μΈ μΌμΈλ°, λ λλ§ν  λλ§λ€ μ΄λ²€νΈ νΈλ€λ¬ Propμ΄ μ νμ΄ν ν¨μλ‘ μ€μ λλ€.    
   
μ΅μ νλ₯Ό λ κ°μ§ λ°©λ²μΌλ‘ κ΅¬νν΄λ³΄μ   
> μ²« λ²μ§Έ λ°©λ²
```ts

interface ScatterProps {
  // The data
  xs: number[];
  ys: number[];

  // Display
  xRange: [number, number];
  yRange: [number, number];
  color: string;

  // Events
  onClick: (x: number, y: number, index: number) => void;
}
function shouldUpdate(
  oldProps: ScatterProps,
  newProps: ScatterProps
) {
  let k: keyof ScatterProps;
  for (k in oldProps) {
    if (oldProps[k] !== newProps[k]) {
      if (k !== 'onClick') return true;
    }
  }
  return false;
}
```
λ§μ½ μλ‘μ΄ μμ±μ΄ μΆκ°λλ©΄ shouldUpdate ν¨μλ κ°μ΄ λ³κ²½λ  λλ§λ€ μ°¨νΈλ₯Ό λ€μ κ·Έλ¦΄ κ²μ΄λ€.   
μ΄λ κ² μ²λ¦¬νλ κ²μ 'λ³΄μμ  μ κ·Όλ²' Ehsms 'μ€ν¨μ λ«ν μ κ·Όλ²'μ΄λΌκ³  νλ€.   
μ΄ μ κ·Όλ²μ μ΄μ©νλ©΄ μ°¨νΈκ° μ ννμ§λ§ λλ¬΄ μμ£Ό κ·Έλ €μ§ κ°λ₯μ±μ΄ μλ€.   
   
>λ λ²μ§Έ μ΅μ ν λ°©λ²μ λ€μκ³Ό κ°λ€ ('μ€ν¨μ μ΄λ¦°' μ κ·Όλ² μ¬μ©)
```ts
interface ScatterProps {
  // The data
  xs: number[];
  ys: number[];

  // Display
  xRange: [number, number];
  yRange: [number, number];
  color: string;

  // Events
  onClick: (x: number, y: number, index: number) => void;
}
function shouldUpdate(
  oldProps: ScatterProps,
  newProps: ScatterProps
) {
  return (
    oldProps.xs !== newProps.xs ||
    oldProps.ys !== newProps.ys ||
    oldProps.xRange !== newProps.xRange ||
    oldProps.yRange !== newProps.yRange ||
    oldProps.color !== newProps.color
    // (no check for onClick)
  );
}
```
μ΄ μ½λλ μ°¨νΈλ₯Ό λΆνμνκ² λ€μ κ·Έλ¦¬λ λ¨μ μ ν΄κ²°νλ€.   
νμ§λ§ μ€μ λ‘ μ°¨νΈλ₯Ό λ€μ κ·Έλ €μΌ ν  κ²½μ°μ λλ½λλ μΌμ΄ μκΈΈ μ μλ€.   
μ΄λ νν¬ν¬λΌνμ€ μ μ§μ λμ€λ μμΉ μ€ νλμΈ 'μ°μ , λ§μΉμ§ λ§κ²'μ μ΄κΈ°κΈ° λλ¬Έμ μΌλ°μ μΈ κ²½μ°μ μ°μ΄λ λ°©λ²μ μλλ€.   
   
μμ  λ κ°μ§ μ΅μ ν λ°©λ² λͺ¨λ μ΄μμ μ΄μ§ μλ€.   
μλ‘μ΄ μμ±μ΄ μΆκ°λ  λ μ§μ  `shouldUpdate`λ₯Ό κ³ μΉλλ‘ νλ κ² λ«λ€.   
μ΄ λ΄μ©μ μ£ΌμμΌλ‘ μΆκ°ν΄ λ³΄μ
```ts
interface ScatterProps {
  // The data
  xs: number[];
  ys: number[];

  // Display
  xRange: [number, number];
  yRange: [number, number];
  color: string;

  // Events
  onClick: (x: number, y: number, index: number) => void;
}
interface ScatterProps {
  xs: number[];
  ys: number[];
  // ...
  onClick: (x: number, y: number, index: number) => void;

  // Note: μ¬κΈ°μ μμ±μ μΆκ°νλ €λ©΄, shouldUpdateλ₯Ό κ³ μΉμΈμ!
}
```
κ·Έλ¬λ μ΄ λ°©λ² μ­μ μ΅μ μ΄ μλλ©°, νμ μ²΄μ»€κ° λμ ν  μ μκ² νλ κ²μ΄ μ’λ€.   
   
 λ€μμ νμ μ²΄μ»€κ° λμνλλ‘ κ°μ ν μ½λμ΄λ€.   
 ν΅μ¬μ λ§€νλ νμκ° κ°μ²΄λ₯Ό μ¬μ©νλ κ²μ΄λ€.   
 ```ts
 interface ScatterProps {
  // The data
  xs: number[];
  ys: number[];

  // Display
  xRange: [number, number];
  yRange: [number, number];
  color: string;

  // Events
  onClick: (x: number, y: number, index: number) => void;
}
const REQUIRES_UPDATE: {[k in keyof ScatterProps]: boolean} = {
  xs: true,
  ys: true,
  xRange: true,
  yRange: true,
  color: true,
  onClick: false,
};

function shouldUpdate(
  oldProps: ScatterProps,
  newProps: ScatterProps
) {
  let k: keyof ScatterProps;
  for (k in oldProps) {
    if (oldProps[k] !== newProps[k] && REQUIRES_UPDATE[k]) {
      return true;
    }
  }
  return false;
}
 ```
 `[k in keyof ScatterProps]`μ νμ μ²΄μ»€μκ² `REQUIRES_UPDAE`κ° `ScatterProps`κ³Ό λμΌν μμ±μ κ°μ ΈμΌ νλ€λ μ λ³΄λ₯Ό μ κ³΅νλ€.   
 λμ€μ `ScatterProps`μ μλ‘μ΄ μμ±μ μΆκ°νλ κ²½μ° λ€μ μ½λμ κ°μ ννκ° λ  κ²μ΄λ€.   
 ```ts
 interface ScatterProps {
  // The data
  xs: number[];
  ys: number[];

  // Display
  xRange: [number, number];
  yRange: [number, number];
  color: string;

  // Events
  onClick: (x: number, y: number, index: number) => void;
}
interface ScatterProps {
  // ...
  onDoubleClick: () => void;
}

// κ·Έλ¦¬κ³  REQUIRES_UPDATEμ μ μμ μ€λ₯κ° λ°μνλ€.

const REQUIRES_UPDATE: {[k in keyof ScatterProps] : boolean} = {
  // ~~~~~~~~ 'onDoubleClick' μμ±μ΄ νμμ μμ΅λλ€.
  // ...
};
```
μ΄λ° λ°©μμ μ€λ₯λ₯Ό μ νν μ‘μ λΈλ€.   
μμ±μ μ­μ νκ±°λ μ΄λ¦μ λ°κΎΈμ΄λ λΉμ·ν μ€λ₯κ° λ°μνλ€.   
   
 μ¬κΈ°μ boolean κ°μ κ°μ§ κ°μ²΄λ₯Ό μ¬μ©νλ€λ μ μ΄ μ€μνλ€.   
 λ°°μ΄μ μ¬μ©νλ€λ©΄ λ€μκ³Ό κ°μ μ½λκ° λλ€.   
```ts
const PROPS_REQUIRING_UPDATE: (keyof ScatterProps)[] = [
  'xs',
  'ys',
  // ...
];
 ```
μ¬κΈ°μ μ°λ¦¬λ μμμ λ€λ£¨μλ μ΅μ ν μμ μμμ²λΌ μ€ν¨μ μ΄λ¦° λ°©λ²μ μ νν μ§, λ«ν λ°©λ²μ μ νν μ§ μ ν΄μΌνλ€.   
   
λ§€νλ νμμ ν κ°μ²΄κ° λ λ€λ₯Έ κ°μ²΄μ μ νν κ°μ μμ±μ κ°μ§κ² ν  λ μ΄μμ μ΄λ€.   
μ΄λ² μμ μ²λΌ λ§€νλ νμμ μ¬μ©ν΄ νμμ€ν¬λ¦½νΈκ° μ½λμ μ μ½μ κ°μ νλλ‘ ν  μ μλ€.   
   
## μμ½

- λ§€νλ νμμ μ¬μ©ν΄μ κ΄λ ¨λ κ°κ³Ό νμμ λκΈ°ννλλ‘ νλ€.   
- μΈν°νμ΄μ€μ μλ‘μ΄ μμ±μ μΆκ°ν  λ, μ νμ κ°μ νλλ‘ λ§€νλ νμμ κ³ λ €ν΄μΌ νλ€.   


 

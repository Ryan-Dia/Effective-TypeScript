## π μ€λ μ½μ λ΄μ©, μ΄λ° μμΌλ‘ μ λ¦¬ν΄ λ΄μλ€. β

**TIL(Today I learn) κΈ°λ‘μΌ** : 2022.02.07

**μ€λ μ½μ λ²μ** : 32. μ λμ¨μ μΈν°νμ΄μ€λ³΄λ€λ μΈν°νμ΄μ€μ μ λμ¨μ μ¬μ©νκΈ°

# Item 32. μ λμ¨μ μΈν°νμ΄μ€λ³΄λ€λ μΈν°νμ΄μ€μ μ λμ¨μ μ¬μ©νκΈ°

μ λμ¨ νμμ μμ±μ κ°μ§λ μΈν°νμ΄μ€λ₯Ό μμ± μ€μ΄λΌλ©΄, νΉμ μΈν°νμ΄μ€μ μ λμ¨ νμμ μ¬μ©νλ κ² λ μλ§μ§λ μμμ§ κ²ν ν΄ λ΄μΌ νλ€.   
>λ°±ν°λ₯Ό κ·Έλ¦¬λ νλ‘κ·Έλ¨μ μμ± μ€μ΄κ³ , νΉμ ν κΈ°ννμ  νμμ κ°μ§λ κ³μΈ΅μ μΈν°νμ΄μ€λ₯Ό μ μνλ€κ³  κ°μ ν΄λ³΄μ
```ts
type FillPaint = unknown;
type LinePaint = unknown;
type PointPaint = unknown;
type FillLayout = unknown;
type LineLayout = unknown;
type PointLayout = unknown;
interface Layer {
  layout: FillLayout | LineLayout | PointLayout;
  paint: FillPaint | LinePaint | PointPaint;
}
```
μ¬κΈ°μ layoutμ΄ LineLayout νμμ΄λ©΄μ paint μμ±μ΄ FillPaint νμμΈ κ²μ λ§μ΄ λμ§ μλλ€.   
μ΄λ° μ‘°ν©μ νμ©νλ€λ©΄ λΌμ΄λΈλ¬λ¦¬μμλ μ€λ₯κ° λ°μνκΈ° μ­μμ΄κ³  μΈν°νμ΄μ€λ₯Ό λ€λ£¨κΈ°λ μ΄λ €μΈ κ²μ΄λ€.   
    
>λ λμ λ°©λ²μΌλ‘ λͺ¨λΈλ§νλ €λ©΄ κ°κ° νμμ κ³μΈ΅μ λΆλ¦¬λ μΈν°νμ΄μ€λ‘ λ¬μΌ νλ€.   
```ts
interface FillLayer {
  layout: FillLayout;
  paint: FillPaint;
}
interface LineLayer {
  layout: LineLayout;
  paint: LinePaint;
}
interface PointLayer {
  layout: PointLayout;
  paint: PointPaint;
}
type Layer = FillLayer | LineLayer | PointLayer;
```

μ΄λ° ννλ‘ Layerλ₯Ό μ μνλ©΄ layoutκ³Ό paint μμ±μ΄ μλͺ»λ μ‘°ν©μΌλ‘ μμ΄λ κ²½μ°λ₯Ό λ°©μ§ν  μ μλ€.   
μ΄ μ½λμμλ μμ΄ν 28μ μ‘°μΈμ λ°λΌ μ ν¨ν μνλ§μ νννλλ‘ νμμ μ μνλ€.   
   
μ΄λ¬ν ν¨ν΄μ κ°μ₯ μΌλ°μ μΈ μμλ νκ·Έλ μ λμ¨μ΄λ€.   
Layerμ κ²½μ° μμ± μ€μ νλλ λ¬Έμμ΄ λ¦¬ν°λ΄ νμμ μ λμ¨μ΄ λλ€.   
```ts
type FillPaint = unknown;
type LinePaint = unknown;
type PointPaint = unknown;
type FillLayout = unknown;
type LineLayout = unknown;
type PointLayout = unknown;
interface Layer {
  type: 'fill' | 'line' | 'point';
  layout: FillLayout | LineLayout | PointLayout;
  paint: FillPaint | LinePaint | PointPaint;
}

// type: 'fill'κ³Ό ν¨κ» LineLayoutκ³Ό PointPaint νμμ΄ μ°μ΄λ κ²μ λ§μ΄ λμ§ μλλ€.
// μ΄λ¬ν κ²½μ°λ₯Ό λ°©μ§νκΈ° μν΄ Layerλ₯Ό μΈν°νμ΄μ€μ μ λμ¨μΌλ‘ λ³νν΄ λ³΄μ.

interface FillLayer {
  type: 'fill';
  layout: FillLayout;
  paint: FillPaint;
}
interface LineLayer {
  type: 'line';
  layout: LineLayout;
  paint: LinePaint;
}
interface PointLayer {
  type: 'paint';
  layout: PointLayout;
  paint: PointPaint;
}
type Layer = FillLayer | LineLayer | PointLayer;
```
type μμ±μ 'νκ·Έ'μ΄λ©° λ°νμμ μ΄λ€ νμμ Layerκ° μ¬μ©λλμ§ νλ¨νλλ° μ°μΈλ€.
νμμ€ν¬λ¦½νΈλ νκ·Έλ₯Ό μ°Έκ³ νμ¬ Layerμ νμμ λ²μλ₯Ό μ’ν μλ μλ€.
```ts
function drawLayer(layer: Layer) {
  if (layer.type === 'fill') {
    const {paint} = layer;  // Type is FillPaint
    const {layout} = layer;  // Type is FillLayout
  } else if (layer.type === 'line') {
    const {paint} = layer;  // Type is LinePaint
    const {layout} = layer;  // Type is LineLayout
  } else {
    const {paint} = layer;  // Type is PointPaint
    const {layout} = layer;  // Type is PointLayout
  }
}

```
κ° νμμ μμ±λ€ κ°μ κ΄κ³λ₯Ό μ λλ‘ λͺ¨λΈλ§νλ©΄, νμμ€ν¬λ¦½νΈκ° μ½λμ μ νμ±μ μ²΄ν¬νλ λ° λμμ΄ λλ€.   
λ€λ§ νμ λΆκΈ° ν layerκ° ν¬ν¨λ λμΌν μ½λκ° λ°λ³΅λλ κ²μ΄ μ΄μμ ν΄ λ³΄μΈλ€.   
    
νκ·Έλ μ λμ¨μ νμμ€ν¬λ¦½νΈ νμ μ²΄μ»€μ μ λ§κΈ° λλ¬Έμ νμμ€ν¬λ¦½νΈ μ½λ μ΄λμμλ μ°Ύμ μ μλ€.   
μ΄ ν¨ν΄μ μ κΈ°μ΅ν΄μ νμν  λ μ μ©ν  μ μλλ‘ ν΄μΌ νλ€.   
μ΄λ€ λ°μ΄ν° νμμ νκ·Έλ μ λμ¨μΌλ‘ ννν  μ μλ€λ©΄,   
λ³΄ν΅μ κ·Έλ κ² νλ κ²μ΄ μ’λ€.   
λλ μ¬λ¬ κ°μ μ νμ  νλκ° λμμ κ°μ΄ μκ±°λ λμμ undefinedμΈ κ²½μ°λ νκ·Έλ μ λμ¨ ν¨ν΄μ΄ μ λ§λλ€.   
   
>μ½λμ νμμ λ³΄μ
```ts
interface Person {
  name: string;
  // λ€μμ λ λ€ λμμ μκ±°λ λμμ μλ€.
  placeOfBirth?: string;
  dateOfBirth?: Date;
}
```

νμ μ λ³΄λ₯Ό λ΄κ³  μλ μ£Όμμ λ¬Έμ κ° λ  μμ§κ° λ§€μ° λλ€.   
placeOfBirthμ dateOfBirth νλλ μ€μ λ‘ κ΄λ ¨λμ΄ μμ§λ§, νμ μ λ³΄μλ μ΄λ ν κ΄κ³λ ννλμ§ μμλ€.   
   
λ κ°μ μμ±μ νλμ κ°μ²΄λ‘ λͺ¨μΌλ κ²μ΄ λ λμ μ€κ³μ΄λ€.   
μ΄ λ°©λ²μ null κ°μ κ²½κ³λ‘ λλ λ°©λ²κ³Ό λΉμ·νλ€.   
```ts
interface Person {
  name: string;
  birth?: {
    place: string;
    date: Date;
  }
}
```
μ΄μ  placeλ§ μκ³  dateκ° μλ κ²½μ°μλ μ€λ₯κ° λ°μνλ€.   
```ts
const alanT: Person = {
  name: 'Alan Turing',
  birth: {
// ~~~~ Property 'date' is missing in type
//      '{ place: string; }' but required in type
//      '{ place: string; date: Date; }'
    place: 'London'
  }
}
```
>Person κ°μ²΄λ₯Ό λ§€κ°λ³μλ‘ λ°λ ν¨μλ birth νλλ§ μ²΄ν¬νλ©΄ λλ€.
```ts
function eulogize(p: Person) {
  console.log(p.name);
  const {birth} = p;
  if (birth) {
    console.log(`was born on ${birth.date} in ${birth.place}.`);
  }
}
```

<br>

νμμ κ΅¬μ‘°λ₯Ό μ λ μ μλ μν©(μλ₯Ό λ€μ΄ APIμ κ²°κ³Ό)μ΄λ©΄, μμ λ€λ£¬ μΈν°νμ΄μ€μ μ λμ¨μ μ¬μ©ν΄μ μμ± μ¬μ΄μ κ΄κ³λ₯Ό λͺ¨λΈλ§ν  μ μλ€.   
```ts
interface Name {
  name: string;
}

interface PersonWithBirth extends Name {
  placeOfBirth: string;
  dateOfBirth: Date;
}

type Person = Name | PersonWithBirth;
```
μ΄μ  μ€μ²©λ κ°μ²΄μμλ λμΌν ν¨κ³Όλ₯Ό λ³Ό μ μλ€.   
```ts
function eulogize(p: Person) {
  if ('placeOfBirth' in p) {
    p // Type is PersonWithBirth
    const {dateOfBirth} = p  // OK, type is Date
  }
}
```
μμ λ κ²½μ° λͺ¨λ νμ μ μλ₯Ό ν΅ν΄ μμ± κ°μ κ΄κ³λ₯Ό λ λͺννκ² λ§λ€ μ μλ€.   

## μμ½

- μ λμ¨ νμμ μμ±μ μ¬λ¬ κ° κ°μ§λ μΈν°νμ΄μ€μμλ μμ± κ°μ κ΄κ³κ° λΆλͺνμ§ μκΈ° λλ¬Έμ μ€μκ° μμ£Ό λ°μνλ―λ‘ μ£Όμν΄μΌ νλ€.   
- μ λμ¨μ μΈν°νμ΄μ€λ³΄λ€ μΈν°νμ΄μ€μ μ λμ¨μ΄ λ μ ννκ³  νμμ€ν¬λ¦½νΈκ° μ΄ν΄νκΈ°λ μ’λ€.
- νμμ€ν¬λ¦½νΈκ° μ μ΄ νλ¦μ λΆμν  μ μλλ‘ νμμ νκ·Έλ₯Ό λ£λ κ²μ κ³ λ €ν΄μΌ νλ€. νκ·Έλ μ λμ¨μ νμμ€ν¬λ¦½νΈμ λ§€μ° μ λ§κΈ° λλ¬Έμ μμ£Ό λ³Ό μ μλ ν¨ν΄μ΄λ€.

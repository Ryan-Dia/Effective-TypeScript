## π μ€λ μ½μ λ΄μ©, μ΄λ° μμΌλ‘ μ λ¦¬ν΄ λ΄μλ€. β

**TIL(Today I learn) κΈ°λ‘μΌ** : 2022.01.29

**μ€λ μ½μ λ²μ** : 24. μΌκ΄μ± μλ λ³μΉ­ μ¬μ©νκΈ°

# Item 24. μΌκ΄μ± μλ λ³μΉ­ μ¬μ©νκΈ°

>μ΄λ€ κ°μ μ μ΄λ¦μ ν λΉνλ μμ λ₯Ό λ³΄μ
```ts
const borough = {name: 'Brooklyn', location: [40.688, -73.979]};
const loc = borough.location;
```

`borough.location` λ°°μ΄μ `loc`μ΄λΌλ λ³μΉ­(alias)μ λ§λ€μλ€.   
>λ³μΉ­μ κ°μ λ³κ²½νλ©΄ μλ μμ±κ°μμλ λ³κ²½λλ€.   
```ts
>loc[0] = 0;
>borough.location
[0, -73.979]
```

κ·Έλ°λ° λ³μΉ­μ λ¨λ°ν΄μ μ¬μ©νλ©΄ μ μ΄ νλ¦μ λΆμνκΈ° μ΄λ ΅λ€.   
λͺ¨λ  μΈμ΄μ μ»΄νμΌλ¬ κ°λ°μλ€μ λ¬΄λΆλ³ν λ³μΉ­ μ¬μ©μΌλ‘ κ³¨μΉλ₯Ό μ©κ³  μλ€.   
νμμ€ν¬λ¦½νΈμμλ λ§μ°¬κ°μ§λ‘ λ³μΉ­μ μ μ€νκ² μ¬μ©ν΄μΌ νλ€.   
κ·ΈλμΌ μ½λλ₯Ό μ μ΄ν΄ν  μ μκ³ , μ€λ₯λ μ½κ² μ°Ύμ μ μλ€.   
   
>λ€κ°νμ νννλ μλ£κ΅¬μ‘°λ₯Ό κ°μ ν΄ λ³΄μ.
```ts
interface Coordinate {
  x: number;
  y: number;
}

interface BoundingBox {
  x: [number, number];
  y: [number, number];
}

interface Polygon {
  exterior: Coordinate[];
  holes: Coordinate[][];
  bbox?: BoundingBox;
}
```
λ€κ°νμ κΈ°ννμ  κ΅¬μ‘°λ exteriorμ holes μμ±μΌλ‘ μ μλλ€.   
bboxλ νμκ° μλ μ΅μ ν μμ±μ΄λ€.   
bbox μμ±μ μ¬μ©νλ©΄ μ΄λ€ μ μ΄ λ€κ°νμ ν¬ν¨λλμ§ λΉ λ₯΄κ² μ²΄ν¬ν  μ μλ€.   
   
```ts
interface Coordinate {
  x: number;
  y: number;
}

interface BoundingBox {
  x: [number, number];
  y: [number, number];
}

interface Polygon {
  exterior: Coordinate[];
  holes: Coordinate[][];
  bbox?: BoundingBox;
}
function isPointInPolygon(polygon: Polygon, pt: Coordinate) {
  if (polygon.bbox) {
    if (pt.x < polygon.bbox.x[0] || pt.x > polygon.bbox.x[1] ||
        pt.y < polygon.bbox.y[1] || pt.y > polygon.bbox.y[1]) {
      return false;
    }
  }

  // ... more complex check
}
```

μ΄ μ½λλ μ λμνμ§λ§ λ°λ³΅λλ λΆλΆμ΄ μ‘΄μ¬νλ€.   
νΉν ploygon.bboxλ 3μ€μ κ±Έμ³ 5λ²μ΄λ λ±μ₯νλ€.   
λ€μ μ½λλ μ€λ³΅μ μ€μ΄κΈ° μν΄ μμ λ³μλ₯Ό λ½μλΈ λͺ¨μ΅μ΄λ€.   
```ts


interface Coordinate {
  x: number;
  y: number;
}

interface BoundingBox {
  x: [number, number];
  y: [number, number];
}

interface Polygon {
  exterior: Coordinate[];
  holes: Coordinate[][];
  bbox?: BoundingBox;
}
function isPointInPolygon(polygon: Polygon, pt: Coordinate) {
  const box = polygon.bbox;
  if (polygon.bbox) {
    if (pt.x < box.x[0] || pt.x > box.x[1] ||
        //     ~~~                ~~~  Object is possibly 'undefined'
        pt.y < box.y[1] || pt.y > box.y[1]) {
        //     ~~~                ~~~  Object is possibly 'undefined'
      return false;
    }
  }
  // ...
}
```
μ΄ μ½λλ λμνμ§λ§ νΈμ§κΈ°μμ μ€λ₯λ‘ νμλλ€.   
κ·Έ μ΄μ λ polygon.bboxλ₯Ό λ³λμ boxλΌλ λ³μΉ­μ λ§λ€μκ³ , μ²« λ²μ§Έ μμ μμλ μ λμνλ μ μ΄ νλ¦ λΆμμ λ°©ν΄νκΈ° λλ¬Έμ΄λ€.   
   
---

κ°μ²΄ λΉκ΅¬μ‘°νλ₯Ό μ΄μ©ν  λλ λ κ°μ§λ₯Ό μ£Όμν΄μΌ νλ€.   

- μ μ²΄ bbox μμ±μ΄ μλλΌ xμ yκ° μ νμ  μμ±μΌ κ²½μ°μ μμ± μ²΄ν¬κ° λ νμνλ€. λ°λΌμ νμμ κ²½κ³μ null κ°μ μΆκ°νλ κ²μ΄ μ’λ€.   
- bboxμλ μ νμ  μμ±μ΄ μ ν©νμ§λ§ holesλ κ·Έλ μ§ μλ€. holesκ° μ νμ μ΄λΌλ©΄, κ°μ΄ μκ±°λ λΉ λ°°μ΄μ΄μμ κ²μ΄λ€. μ°¨μ΄κ° μλλ° μ΄λ¦μ κ΅¬λ³ν κ²μ΄λ€. λΉ λ°°μ΄μ'holes μμ'μ λνλ΄λ μ’μ λ°©λ²μ΄λ€.

λ³μΉ­μ νμ μ²΄μ»€λΏλ§ μλλΌ λ°νμμλ νΌλμ μΌκΈ°ν  μ μλ€.   
```ts
interface Coordinate {
  x: number;
  y: number;
}

interface BoundingBox {
  x: [number, number];
  y: [number, number];
}

interface Polygon {
  exterior: Coordinate[];
  holes: Coordinate[][];
  bbox?: BoundingBox;
}
// HIDE
const polygon: Polygon = { exterior: [], holes: [] };
function calculatePolygonBbox(polygon: Polygon) {}
// END
const {bbox} = polygon;
if (!bbox) {
  calculatePolygonBbox(polygon);  // Fills in polygon.bbox
  // Now polygon.bbox and bbox refer to different values!
}
```


## μμ½

- λ³μΉ­μ νμμ€ν¬λ¦½νΈκ° νμμ μ’νλ κ²μ λ°©ν΄νλ€. λ°λΌμ λ³μμ λ³μΉ­μ μ¬μ©ν  λλ μΌκ΄λκ² μ¬μ©ν΄μΌ νλ€.
- λΉκ΅¬μ‘°ν λ¬Έλ²μ μ¬μ©ν΄μ μΌκ΄λ μ΄λ¦μ μ¬μ©νλ κ²μ΄ μ’λ€.
- ν¨μ νΈμΆμ΄ κ°μ²΄ μμ±μ νμ μ μ²΄λ₯Ό λ¬΄ν¨νν  μ μλ€λ μ μ μ£Όμν΄μΌ νλ€. μμ±λ³΄λ€ μ§μ­ λ³μλ₯Ό μ¬μ©νλ©΄ νμ μ μ λ₯Ό λ―Ώμ μ μλ€.

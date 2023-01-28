## 📕 오늘 읽은 내용, 이런 식으로 정리해 봅시다. ✒

**TIL(Today I learn) 기록일** : 2022.01.29

**오늘 읽은 범위** : 24. 일관성 있는 별칭 사용하기

# Item 24. 일관성 있는 별칭 사용하기

>어떤 값에 새 이름을 할당하는 예제를 보자
```ts
const borough = {name: 'Brooklyn', location: [40.688, -73.979]};
const loc = borough.location;
```

`borough.location` 배열에 `loc`이라는 별칭(alias)을 만들었다.   
>별칭의 값을 변경하면 원래 속성값에서도 변경된다.   
```ts
>loc[0] = 0;
>borough.location
[0, -73.979]
```

그런데 별칭을 남발해서 사용하면 제어 흐름을 분석하기 어렵다.   
모든 언어의 컴파일러 개발자들은 무분별한 별칭 사용으로 골치를 썩고 있다.   
타입스크립트에서도 마찬가지로 별칭을 신중하게 사용해야 한다.   
그래야 코드를 잘 이해할 수 있고, 오류도 쉽게 찾을 수 있다.   
   
>다각형을 표현하는 자료구조를 가정해 보자.
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
다각형의 기하학적 구조는 exterior와 holes 속성으로 정의된다.   
bbox는 필수가 아닌 최적화 속성이다.   
bbox 속성을 사용하면 어떤 점이 다각형에 포함되는지 빠르게 체크할 수 있다.   
   
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

이 코드는 잘 동작하지만 반복되는 부분이 존재한다.   
특히 ploygon.bbox는 3줄에 걸쳐 5번이나 등장한다.   
다음 코드는 중복을 줄이기 위해 임시 변수를 뽑아낸 모습이다.   
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
이 코드는 동작하지만 편집기에서 오류로 표시된다.   
그 이유는 polygon.bbox를 별도의 box라는 별칭을 만들었고, 첫 번째 예제에서는 잘 동작했던 제어 흐름 분석을 방해했기 때문이다.   
   
---

객체 비구조화를 이용할 때는 두 가지를 주의해야 한다.   

- 전체 bbox 속성이 아니라 x와 y가 선택적 속성일 경우에 속성 체크가 더 필요하다. 따라서 타입의 경계에 null 값을 추가하는 것이 좋다.   
- bbox에는 선택적 속성이 적합했지만 holes는 그렇지 않다. holes가 선택적이라면, 값이 없거나 빈 배열이었을 것이다. 차이가 없는데 이름을 구별한 것이다. 빈 배열은'holes 없음'을 나타내는 좋은 방법이다.

별칭은 타입 체커뿐만 아니라 런타임에도 혼동을 야기할 수 있다.   
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


## 요약

- 별칭은 타입스크립트가 타입을 좁히는 것을 방해한다. 따라서 변수에 별칭을 사용할 때는 일관되게 사용해야 한다.
- 비구조화 문법을 사용해서 일관된 이름을 사용하는 것이 좋다.
- 함수 호출이 객체 속성의 타입 정체를 무효화할 수 있다는 점을 주의해야 한다. 속성보다 지역 변수를 사용하면 타입 정제를 믿을 수 있다.

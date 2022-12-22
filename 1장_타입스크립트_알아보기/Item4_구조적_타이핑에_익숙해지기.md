# 📕 읽은내용 정리해보기


# 📚 Item 4 구조적 타이핑에 익숙해지기

```
자바스크립트는 본질적으로 기반이다.   
만약 어떤 함수의 매개변수 값이 모두 제대로 주어진다면, 그 값이 어떻게 만들어졌는지 신경 쓰지 않고 사용합니다.   
타입스크립트는 이런 동작, 즉 매개변수 값이 요구사항을 만족한다면 타입이 무엇인지 신경 쓰지 않는 동작을 그대로 모델링합니다.  
그런데 타입 체커의 타입에 대한 이해도가 사람과 조금 다르기 때문에 가끔 예상치 못한 결과가 나오기도 합니다.
```
구조적 타이핑을 제대로 이해한다면 오류인 경우와 오류가 아닌 경우의 차이를 알 수 있고, 더욱 견고한 코드를 작성할 수 있습니다.

> 물리 라이브러리와 2D 백터 타입을 다루는 경우를 가정해 보겠습니다.
```ts
interface Vector2D {
  x : number;
  y : number;
}

function calculateLength(v: Vector2D) {
  return Math.sqrt(v.x * v.x + v.y * v.y);
}

interface NamedVector {
  name: string;
  x : number;
  y : number;
}

const v: NamedVector = { x: 3, y: 4, name: 'Zee'};
caclulateLength(v); // 정상, 결과는 5
```
여기서 흥미로운점
- Vector2D와 NamedVector의 관계를 전혀 선언하지 않았다.
- NamedVector를 위한 별도의 calculateLength를 구현할 필요도 없었다.

타입스크립트 타입 시스템은 자바스크립트의 런타임 동작을 모델링한다.   
따라서 NamedVector의 구조가 Vector2D와 호환되기 때문에 calculateLength 호출이 가능하다.   
그리고 이것을 '[구조적 타이핑](https://vallista.kr/%EB%8D%95-%ED%83%80%EC%9D%B4%ED%95%91%EA%B3%BC-%EA%B5%AC%EC%A1%B0%EC%A0%81-%ED%83%80%EC%9D%B4%ED%95%91/)'이라고 한다.   
```
위 예제는 명목적 타입 시스템을 기준으로 Vector2D를 사용하도록 의도한 코드일 것이다. 
하지만 구조적 타이핑 언어에서는 전혀 다른 개념으로 이해해야 한다. 
Vector2D의 속성에 해당하는 값이 값을 넣는 타입에 속성으로 존재하는가가 로 이해해야 한다. 
이로인한 단점으로는 의도하지 않았지만 동일한 타입을 가지는 경우 의도치 않게 동일한 유형으로 간주될 수 있다.
```

## 구조적 타이핑 때문에 발생하는 문제

 > 벡터의 길이를 1로 만드는 정규화 함수를 작성합니다. 그러나 이 함수는 1보다 조금 더 긴 길이를 가진 결과를 출력한다. 
```ts
interface Vector3D {
  x: number;
  y: number;
  z: number;
}

function normalize(v: Vector3D) {
  const length = calculateLength(v);
  return {
    x: v.x / length,
    y: v.y / length,
    z: v.z / length,
  };
}

normalize({x: 3, y:4, z: 5) // {x: 0.6, y: 0.8, z: 1}
```
> 왜 타입스크립트는 오류를 잡지 못했을까?
```
calculateLength는 2D 백터를 기반으로 연산하는데, 버그로 인해 normalize가 3D 벡터로 연산되었습니다. Z가 정규화에서 무시된 것이다.
그런데 타입 체커가 이 문제를 잡아내지 못했다.
calculateLength가 2D백터를 받도록 선언되었음에도 불구하고 3D 백터를 받는 데 문제가 없었던 이유는 무엇일까?

Vector3D와 호환되는 {x, y, z} 객체로 calculateLength를 호출하면 구조적 타이핑 관점에서 x와 y가 있어서 Vector2D와 호환된다.
따라서 오류가 발생하지 않았고, 타입 체커가 문제로 인식하지 않았다. ( 이런 경우를 오류로 처리하기 위한 설정이 존재한다.)

```

함수를 작성할 때, 호출에 사용되는 매개변수의 속성들이 매개변수의 타입에 선언된 속성만을 가질거라 생각하기 쉽다.  
이러한 타입은 '봉인된(sealed)' 또는 '정확한(precise)' 타입이라고 불리며, 타입스크립트 타입 시스템에서는 표현할 수 없다.  
좋든 싫든 타입은 열려(open)있다.

> 이러한 특성 때문에 가끔 당활스러운 결과가 발생한다.
```ts
for (const axis of Object.keys(v)) {
  const coord = v[axis];
      // ~~~ 'string'은 'Vector3D'의 인덱스로 사용할 수 없기에
      //     엘리먼트는 암시적으로 'any'타입이다.
      length += Math.abs(coord);
      
    }
    return length;
}
```
- 왜 오류가 발생하는 것인가?
  - axis는 Vector3D 타입인 v의 키 중 하나이기 때문에 "x", "y", "z"중 하나여야 한다.
  - 그리고 Vector3D의 선언에 따르면, 이들은 모두 number이므로 coord의 타입이 number가 되어야 할 것으로 예상된다.
  - 그러나 사실은 타입스크립트가 오류를 정확히 찾아낸 것이 맞다.
  - 앞에서 Vector3D는 봉인(다른 속성이 없다)되었다고 가정했다. 그런데 다음 코드 처럼 작성할 수도 있다.
  ```ts
  const vec3D = {x : 3, y: 4, z: 1, address: '123 Broadway'};
  calculateLengthL1(vec3D);  // 정상, NaN을 반환합니다.
  ```
  v는 어떤 속성이든 가질 수 있기 때문에, axis의 타입은 string이 될 수도 있다.   
  그러므로 앞서 본 것처럼 타입스크립트는 v[axis]가 어떤 속성이 될지 알 수 없기 때문에 number라고 확정할 수 없다.
  이번 경우의 결론은 루프보다는 모든 속성을 각각 더하는 구현이 더 좋다.
  ```
  function calculateLengthL1(v: Vector3D) {
    return Math.abs(v.x) + Math.abs(v.y) + Math.abs(v.z)
  }
  ```


## 요약

- 자바스크립트가 '덕 타이핑'기반이고 타입스크립트가 이를 모델링하기 위해 구조적 타이핑을 사용함을 이해해야 한다.   
  어떤 인터페이스에 할당 가능한 값이라면 타입 선언에 명시적으로 나열된 속성들을 가지고 있다. 타입은 '봉인'되어 있지 않다
- 클래스 역시 구조적 타이핑 규칙을 따른다.(클래스의 인스턴스가 예상과 다를 수 있다)
- 구조적 타이핑을 사용하면 유닛 테스팅을 손쉽게 할 수 있다.


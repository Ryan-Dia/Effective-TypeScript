# 📕 읽은내용 정리해보기


## 📚 Item 7 타입이 값들의 집합이라고 생각하기


- '할당 가능한 값들의 집합'이 타입이라고 생각하면 된다. 이 집합은 타입의 '범위'라고 부르기도 한다
  - 예를 들어, 모든 숫자값의 집합을 number 타입이라고 생각할 수 있다. 

  > 가장 작은 집합은 아무 값도 포함하지 않는 공집합이며, 타입스크립트에서는 never 타입이다.
  > never 타입으로 선언된 변수의 범위는 공집합이기 때문에 아무런 값도 할당할 수 없다.
  ```ts
  const x: never = 12;
    // ~ '12' 형식은 'never' 형식에 할당할 수 없습니다.
  ```
  > 그 다음으로 작은 집합은 한 가지 값만 포함하는 타입이다. 이들은 타입스크립트에서 유닛타입이라고도 불리는 리터럴타입이다.
  ```ts
  type A = 'A';
  type B = 'B';
  type Tweleve = 12;
  ```
  
  >두 개 혹은 세 개로 묶으려면 유니온타입을 사용한다.
  ```ts
  type AB = "A" | "B";
  type AB12 = "A" | "B" | 12;
  ```
  - 세 개 이상의 타입을 묶을 때도 동일하게 |로 이어주면 된다.
  - 유니온 타입은 값 집합들의 합집합을 일컫는다.

---

### 1) 확장
```ts
  interface Person {
    name: string;
  }
  interface PersonSpan extends Person {
    birth: Date;
    death? : Date;
  }  
```
  
- 타입이 집합이라는 관점에서 extends의 의미는 '~에 할당 가능한'과 비슷하게, '~의 부분 집합'이라는 의미로 받아들일 수 있다
- personSpan 타입의 모든 값은 문자열 name 속성을 가져야 한다. 그리고 birth 속성을 가져야 제대로된 부분 집합이 된다.

### 2) 서브타입

  어떤 집합이 다른 집합의 부분 집합이라는 의미이다. 
  
  - 예
  ```ts
  interface Vector1D {x: number;}
  interface Vector2D extends Vector1D { y: number;}
  interface Vector3D extends Vector2D { z: number;}
  ```
  
  - Vector3D는 Vector2D의 서브타입이고 Vector2D는 Vector1D의 서브타입니다.
  - 보통 이 관계는 상속 관계로 그려지지만, 집합의 관점에서는 벤 다이어그램으로 그리는 게 더욱 적절하다.
  
  >extends 키워드는 제너릭 타입에서 한정자로도 쓰이며, 이 문맥에서는 '~의 부분 집합'을 의미하기도 합니다.
  ```ts
  function getKey<K extends string>(val: any, key:K) {
    // ...
  }
  ```
  > string을 상속한다는 의미를 집합의 관점으로 생각해보면 쉽게 이해할 수 있다. 
   >string의 부분 집합 범위를 가지는 어떠한 타입이 된다.
   >이 타입은 string 리터럴 타입, string 리터럴 타입의 유니온, string 자신을 포함합니다.
  ```ts
  getKey({}, 'x');                                // 정상, 'x'는 string을 상속
  getKey({}, Math.rando() < 0.5 ? 'a' : 'b');     // 정상, 'a' |'b'는 string을 상속
  getKey({}, document.title);                     // 정상, string은 string을 상속
  getKey({}, 12);         
    // ~~ '12' 형식의 인수는 'string' 형식의 매개변수에 할당될 수 없습니다.
  ```
  - 마지막 오류에서 '할당될 수 없습니다'는 상속의 관점에서 '상속할 수 없습니다'로 바꿀 수 있고, 두 표현 모두 '~의 부분 집합'의 의미로 받아들인다면 문제가 없다.
  
  > 위와 같이 할당과 상속의 관점을 전환해 보면, 객체의 키 타입을 반환하는 keyof T를 이해하기 수월합니다.
  ```ts
  interface Point {
    x: number;
    y: number;
  }
  type PointKeys = keyof Point; // 타입은 'x' | 'y'
  
  function sortBy<K extends keyof T, T>(vals: T[], key: K): T[] {
    // ...
  }
  const pts: Point[] = [{x: 1, y: 1}, {x: 2, y: 0}];
  sortBy(pts, 'x'); // 정상, 'x'는 'x'|'y'를 상속 (즉, keyof T)
  sortBy(pts, 'y'); // 정상, 'y'는 'x'|'y'를 상속 (즉, keyof T)
  sortBy(pts, Math.random() < 0.5 ? 'x' : 'y'); 
                      // 정상, 'x' | 'y'는 'x'|'y'를 상속 (즉, keyof T)
  sortBy(pts, 'z'); 
        // ~~ 정상, '"z" 형식의 인수는 '"x" | "y"' 형식의 매개변수에 할당될 수 없습니다.
  ```
  - 타입들이 엄격한 상속 관계가 아닐 때는 집합 스타일이 더욱 바람직합니다.
    - 예를 들어, string | number 와 string | Date 사이의 인터섹션은 공집합이 아니며 서로의 부분 집합도 아니다.
  
  > 타입이 집합이라는 관점은 배열과 튜플의 관계 역시 명확하게 만듭니다.
  ```ts
    const list = [1, 2] ; // 타입은 number[]
    const tuple: [number, number] = list;
        // ~~~ 'number[]' 타입은 '[number, number]' 타입의 0, 1 속성에 없습니다.
  ```
  - 이 코드에서 숫자 배열을 숫자들의 쌍이라고 할 수 없다.
  - 빈 리스트와 [1]이 그 반례이다.
  - number[]는 [number, number]의 부분 집합이 아니기 때문에 할당할 수 없다. ( 그 반대로 할당하면 동작)
  

# 📕 읽은내용 정리해보기


# 📚 Item 3 코드 생성과 타입이 관계없음을 이해하기

타입스크립트 컴파일러는 두 가지 역할을 수행합니다.

- 최신 타입스크립트/자바스크립트를 브라우저에서 동작할 수 있도록 구버전의 자바스크립트로 트랜스파일합니다.
- 코드의 타입오류를 체크합니다.

이 두 가지는 완벽히 독립적이다.   
다시말해서, 타입스크립트가 자바스크립트로 변환될 때 코드 내의 타입에는 영향을 주지 않습니다.

## 📖 타입 오류가 있는 코드도 컴파일이 가능하다.

컴파일은 타입 체크와 독립적으로 동작하기 때문에, 타입 오류가 있는 코드도 컴파일이 가능하다.

```ts
// cat test.ts
let x = 'hello';
x = 1234;

// tsc test.ts
test.ts:2:1 - error TS2322: '1234' 형식은 'string' 형식에 할당할 수 없습니다.

2 x = 1234;
~

// cat test.js
var x = 'hello';
x = 1234;
```

타입 체크와 컴파일이 동시에 이루어지는 C나 자바 같은 언어를 사용하던 사람이라면 이러한 상황이 매우 황당하게 느껴질 것이다.
타입스크립트 오류는 C나 자바 같은 언어들의 경고와 비슷하다.    
문제가 될 만한 부분을 알려주지만, 그렇다고 빌드를 멈추지는 않는다.   
```txt
컴파일과 타입체크
코드에 오류가 있을 때 "컴파일에 문제가 있다"고 말하는 경우를 보았을 것이다.
그러나 이는 기술적으로 틀린말이다. 엄밀히 말하면 오직 코드 생성만이 '컴파일'이라고 할 수 있기 때문이다.
작성한 타입스크립트가 유효한 자바스크립트라면 타입스크립트 컴파일러는 컴파일을 해 낸다.
그러므로 코드에 오류가 있을 때 "타입체크에 문제가 있다"고 말하는 것이 더 정확한 표현이다.
```
- 만약 오류가 있을 때 컴파일하지 않으려면, tsconfig.json에 noEmitOnError를 설정하거나 빌드 도구에 동일하게 적용하면 된다.

<br>

---

## 📖 런타임에는 타입 체크가 불가능하다.

```ts
interface Square {
  width: number;
}
interface Rectangle extends Square {
  height: number;
}
type Shpe = Square | Rectangle;

function calculateArea(shape: Shape) {
  if (shape instanceof Rectangle) {
    // ~~~~ 'Rectangle'은(는) 형식만 참조하지만,
    //      여기서는 값으로 사용되고 있습니다.
    return shape.width * shape.height;
        // ~~~ 'Shape' 형식에 'height' 속성이 없습니다.
    } else {
      return shape.width * shape.width;
    }
 } 
```
instanceof 체크는 런타임에 일어나지만, Rectangle은 타입이기 때문에 런타임 시점에 아무런 역할을 할 수 없다.   
타입스크립트의 타입은 '제거 가능'하다.   
실제로 자바스크립트로 컴파일되는 과정에서 모든 인터페이스, 타입, 타입 구문은 그냥 제거되어 버린다.   

```ts
function calculateArea(shape:Shape) {
  if ('height' in shape) {
    shape; // 타입이 Rectangle
    return shape.width * shape.height;
    } else {
      shape; // 타입이 Square
      return shape.width * shape.width;
    }
}
```
속성 체크는 런타임에 접근 가능한 값에만 관련되지만, 타입 체커 역시도 shape의 타입을 Rectangle로 보정해 주기 때문에 오류가 사라진다.

---

## 📖 타입 연산은 런타임에 영향을 주지 않는다.

- string 또는 number 타입인 값을 항상 number로 정제하는 경우 다음 코드는 타입 체커를 통과하지만 잘못된 방법으로 써졌다.
```ts
function asNumber(val: number | string) : number {
  return val as number;
}
```

> 위 코드가 자바스크립트로 변환된 코드
```js
function asNumber(val) {
  return val;
}
```
코드에 아무런 정제 과정이 없다.   
as number는 타입 연산이고 런타임 동작에는 아무런 영향을 미치지 않는다.   
값을 정제하기 위해서는 런타임의 타입을 체크해야 하고 자바스크립트 연산을 통해 변환을 수행해야 한다.
```ts
function asNumber(val: number | string) :number {
  return typeof(val) === 'string' ? Number(val) : val;
}

(as number는 '타입 단언문'이다.)
```

## 📖 런타임 타입은 선언된 타입과 다를 수 있다.

다음 함수를 보고 마지막의 console.log까지 실행될 수 있을지 생각해 보자
```ts
function setLightSwitch(value: boolean) {
  switch (value) {
    case true:
      turnLightOn();
      break;
    case false:
      turnLightOff();
      break;
    default:
      console.log(`실행되지 않을까 봐 걱정됩니다.`);
  }
}
```
여기서 마지막 부분을 실행할 수 잇는 경우는 무엇일까?   
: boolean이 타입 선언문이라는 것에 주목해야한다.   
타입스크립트의 타입이기 때문에 : boolean은 런타임에 제거된다.   
자바스크립트였다면 실수로 setLightSwitch를  "ON"으로 호출할 수도 있었을 것이다.
순수 타입스크립트에서도 마지막 코드를 실행하는 방법이 존재한다.   
예를들어, 네트워크 호출로부터 받아온 값으로 함수를 실행하는 경우가 있다.   
<br>
코드가 배포된 후 API가 변경되어 인수가 boolean이었는데 string으로 변경되는 경우도 있다.   
이처럼 타입스크립트에서는 런타임 타입과 선언된 타입이 맞지 않을 수 있다.   
선언된 타입이 언제든지 달라질 수 있다는 것을 명심해야 한다.  
<br>

---

## 📖 타입스크립트 타입으로는 함수를 오버로드할 수 없다.

C++ 같은 언어는 동일한 이름에 매개변수만 다른 여러 버전의 함수를 허용한다.   
이를 '함수 오버로딩'이라고 한다.   
그러나 타입스크립트에서는 타입과 런타임의 동작이 무관하기 때문에, 함수 오버로딩은 불가능하다.
```ts
function add (a:number, b: number) {return a + b; }
  // ~~~ 중복된 함수 구현입니다.
function add(a: string, b : string) {return a + b; }
  // ~~~ 중복된 함수 구현입니다. 
```
타입스크립트가 함수 오버로딩 기능을 지원하기는 하지만, 온전히 타입 수준에서만 동작합니다.   
하나의 함수에 대해 여러 개의 선언문을 작성할 수 있지만, 구현체는 오직 하나뿐입니다.

```ts
function add(a: number, b: number): number;
function add(a: string, b: string): string;

function add(a,b) {
  return a + b;
}

const three = add(1,2); //타입이 number
const twelve = add('1', '2'); // 타입이 string
```
add에 대한 처음 두 개의 선언문은 타입 정보를 제공할 뿐입니다.   
이 두 선언문은 타입스크립트가 자바스크립트로 변환되면서 제거되며, 구현체만 남게됩니다.

<br>

---

## 📖 타입스크립트 타입은 런타임 성능에 영향을 주지 않습니다.

타입과 타입 연산자는 자바스크립트 변환 시점에 제거되기 때문에, 런타임에 성능에 아무런 영향을 주지 않습니다.

<br>

---

## 🔖 요약
- 코드 생성은 타입 시스템과 무관합니다. 타입스크립트 타입은 런타임 동작이나 성능에 영향을 주지 않습니다.
- 타입 오류가 존재하더라도 코드 생성(컴파일)은 가능합니다.
- 타입스크립트 타입은 런타임에 사용할 수 없습니다.
  - 런타임에 타입을 지정하려면, 타입 정보 유지를 위한 별도의 방법이 필요합니다.
  - 일반적으로는 태그된 유니온과 속성 체크 방법을 사용합니다.
  - 또는 클래스 같이 타입스크립트 타입과 런타임 값, 둘 다 제공하는 방법이 있습니다. 

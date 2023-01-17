# 📕 읽은내용 정리해보기


#<img width="865" alt="image" src="https://user-images.githubusercontent.com/76567238/212522240-661fc4f6-fefa-4328-a41e-2e7b0ea9d99a.png">
 📚 Item 8 타입 공간과 값 공간의 심벌 구분하기
- 타입스크립트의 심벌은 타입 공간이나 값 공간 중의 한 곳에 존재한다.

> 심벌은 이름이 같더라도 속하는 공간에 따라 다른 것을 나타낼 수 있기 때문에 혼란스러울 수 있다.
```ts
// Cylinder은 타입으로 사용
interface Cylinder {
  radius: number;
  height: number;
}
// Cylinder로 이름은 같지만 값으로 사용
const Cylinder = (radius: number, height: number) => ({radius, height});
```
- interface Cylinder에서 Cylinder는 타입으로 쓰이는 중이다. const  Cylinder에서 Cylinder와 이름은 같지만 값으로 쓰이며, 서로 아무런 관련도 없다.
>상황에 따라서 Cylinder는 타입으로 쓰일 수도 있고, 값으로 쓰일 수도 있다. (이런 점이 가끔 오류를 야기한다.)
```ts
function calculateVolume(shape: unknown) {
  if (shape instanceof Cylinder) {
    shape.radius
      // ~~~~ '{}' 형식에 'radius' 속성이 없습니다.
  }
}
```
오류를 살펴보면 instanceof를 이용해 shape가 Cylinder 타입인지 체크하려고 했을 것이다.   
그러나 instanceof는 자바스크립트의 런타임 연산자이고, 값에 대해서 연산을 한다.   
그래서 instanceof Cylinder는 타입이 아니라 함수를 참조한다.   
<br>

## 그렇다면 타입과 값은 어떻게 구분할 수 있을까?

> 한 심벌이 타입인지 값인지는 언뜻 봐서 알 수 없다.  어떤 형태로 쓰이는지 문맥을 살펴 알아내야 한다.   
```ts
type T1 = 'string literal';
type T2 = 123;
const v1 = 'string literal';
const v2 = 123;
```
일반적으로 type이나 interface 다음에 나오는 심벌은 타입인 반면, const나 let 선언에 쓰이는 것은 값이다.   
<br>

> 타입스크립트 코드에서 타입과 값은 번갈아 나올 수 있다.   
> 타입 선언(:) 또는 단언문(as) 다음에 나오는 심벌은 타입인 반면, = 다음에 나오는 모든 것은 값이다.
```ts
interface Person {
  first: string;
  last: string;
}
const p: Person = { first: 'Jane', last: 'Jacobs'};
//    -           --------------------------------- 값
         -------                                    타입
```
<br>

> 일부 함수에서는 타입과 값이 반복적으로 번갈아 가며 나올 수도 있다.
```ts
interface Person {
  first: string;
  last: string;
}

function email(p: Person, subject: string, body: string): Response {
  //     ----- -          -------          ----  Values
  //              ------           ------        ------   -------- Types
  // COMPRESS
  return new Response();
  // END

```

<br>

>class와 enum은 상황에 따라 타입과 값 두 가지 모두 가능한 예약어다.
>다음 예제에서 Cylinder 클래스는 타입으로 쓰였다.
```ts
class Cylinder {
  radius=1;
  height=1;
}

function calculateVolume(shape: unknown) {
  if (shape instanceof Cylinder) {
    shape  // OK, type is Cylinder
    shape.radius  // OK, type is number
  }
}
```
클래스가 타입으로 쓰일 때는 형태(속성과 메서드)가 사용되는 반면, 값으로 쓰일 때는 생성자가 사용됩니다.   

<br>

> 연산자 중에서도 타입에서 쓰일 때와 값에서 쓰일 때 다른 기능을 하는 것들이 있다.   
> 그 예 중 하나로 typeof를 들 수 있다.
```ts
type T1 = typeof p;  // Type is Person
type T2 = typeof email;
    // Type is (p: Person, subject: string, body: string) => Response

const v1 = typeof p;  // Value is "object"
const v2 = typeof email;  // Value is "function"
```
타입의 관점에서, typeof는 값을 읽어서 타입스크립트 타입을 반환한다.   
타입 공간의 typeof는 보다 큰 타입의 일부분으로 사용할 수 있고, type 구문으로 이름을 붙이는 용도로도 사용할 수 있다.   
  값의 관점에서는 typeof는 자바스크립트 런타임의 typeof 연산자가 된다.    
값 공간의 typeof는 대상 심벌의 런타임 타입을 가리키는 문자열을 반환하며, 타입스크립트 타입과는 다르다.   
자바스크립트의 런타임 타입 시스템은 타입스크립트의 정적 타입 시스템보다 훨씬 간단하다.    
타입스크립트 타입의 종류가 무수히 많은 반면, 자바스크립트에는 과거부터 지금까지 단 6개(string, number, boolean, undefined, object, function)의 런타임만이 존재한다.  




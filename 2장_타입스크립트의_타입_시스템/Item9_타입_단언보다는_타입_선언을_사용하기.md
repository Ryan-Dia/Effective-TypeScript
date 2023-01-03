# 📕 읽은내용 정리해보기


## 📚 Item 9 타입 단언보다는 타입 선언을 사용하기


> 타입스크립트에서 변수에 값을 할당하고 타입을 부여하는 방법은 두 가지 이다.
```ts
interface Person { name: string };

const alice: Person = { name: 'Alice' };  // Type is Person
const bob = { name: 'Bob' } as Person;  // Type is Person
```

- 이 두 가지 방법은 결과가 같아 보이지만 그렇지 않다. 
  - 첫 번째 alice:Person은 변수에 `타입 선언`을 붙여서 그 값이 선언된 타입임을 명시한다.
  - 두 번째 as Person은 `타입 단언`을 수행한다. 그러면 타입스크립트가 추론한 타입이 있더라도 Person 타입으로 간주한다.

<br>

> 타입 단언보다 타입 선언을 사용하는 게 더 좋다 
```ts
interface Person { name: string };
const alice: Person = {};
   // ~~~~~ Property 'name' is missing in type '{}'
   //       but required in type 'Person'
const bob = {} as Person;  // No error
```
- `타입 선언`은 할당되는 값이 해당 인터페이스를 만족하는지 검사한다.  ( 오류 잡아냄 )
- `타입 단언`은 강제로 타입을 지정했으니 타입 체커에게 오류를 무시하라고 하는 것 이다. ( 오류 무시 )

<br>

> 타입 선언과 단언의 차이는 속성을 추가할 때도 마찬가지다.
```ts
interface Person { name: string };
const alice: Person = {
  name: 'Alice',
  occupation: 'TypeScript developer'
// ~~~~~~~~~ Object literal may only specify known properties
//           and 'occupation' does not exist in type 'Person'
};
const bob = {
  name: 'Bob',
  occupation: 'JavaScript developer'
} as Person;  // No error
```
- `타입 선언`에서는 잉여 속성 체크가 동작하였다.
- `타입 단언`에서는 적용되지 않았다.    


❗️**타입 단언이 꼭 필요한 경우가 아니라면, 안전성 체크도 되는 `타입 선언`을 사용하는 것이 좋다.**

```
기존에 const bob = <Person>{} 같은 코드를 본적이 있을 것이다.
이 코드는 단언문의 원래 문법이며 {} as Person과 동일하다. 
그러나 const bob = <person>{} 같은 코드는 <Person>이 .tsx파일(TS + react)에서 컴포넌트 태그로 인식되기 때문에 현재는 잘 사용되지 않는다.
```

### 1) 화살표 함수의 타입 선언은 추론된 타입이 모호할 때가 있다.

> 다음 코드에서 Person 인터페이스를 사용하고 싶다고 가정해보자
```ts
const people = ['alice', 'bob', 'jan'].map(name => ({name}));
// Peerson[]을 원했지만 결과는 { name : string;}[]...

// {name}에 타입 단언을 쓰면 문제가 해결되는 것처럼 보인다. 
const people = ['alice', 'bob', 'jan'].map(name => ({} as Person)); // 오류 없음
```

> 그러나 `타입 단언`을 사용하면 앞선 예제들처럼 런타임에 문제가 발생하게 된다.
```ts
const people = ['alice', 'bob', 'jan'].map(name => {
  const eprson : Person = {name};
  return person
  }); // 타입은 Person[] 
}
```

> 그러나 원래 코드에 비해 꽤나 번잡하게 보인다.
> 코드를 좀 더 간결하게 보이기 위해 변수 대신 화살표 함수의 반환 타입을 선언해 보자
```ts
const people = ['alice', 'bob', 'jan'].map(
  (name): Person => ({name})
) // 타입은 Person[]
```
이 코드는 바로 앞의 번잡한 버전과 동일한 체크를 수행합니다.   
여기서 소괄호는 매우 중요한 의미를 지닙니다.   
(nmae): Person은 name의 타입이 없고, 반환 타입이 Person이라고 명시합니다.   
그러나 `name: Person)`은 `name`의 타입이 `Person`임을 명시하고 반환 타입은 없기 때문에 오류가 발생합니다.    

>다음 코드는 최종적으로 원하는 타입을 직접 명시하고, 타입스크립트가 할당문의 유효성을 검사하게 한다.   
```ts
const people : Person[] = ['alice', 'bob', 'jan'].map(
  (name):Person => ({name})
);

```
- 그러나 함수 호출 체이닝이 연속되는 곳에서는 체이닝 시작에서부터 명명된 타입을 가져야 한다.
- 그래야 정확한 곳에 오류가 표시된다.

> ✅ `타입 단언`은 타입 체커가 추론한 타입보다 여러분이 판단하는 타입이 더 정확할 때 의미가 있다.
> 예를 들어, DOM엘리먼트에 대해서는 타입스크립트보다 여러분이 더 정확히 알고 있을 것이다.
```ts
document.querySelector('#myButton').addEventListener('click', e => {
  e.currentTarget // Type is EventTarget
  const button = e.currentTarget as HTMLButtonElement;
  button // Type is HTMLButtonElement
});
```
타입스크립트는 DOM에 접근할 수 없기 때문에 #myButton이 버튼 엘리먼트인지 알지 못한다.   
그리고 이벤트의 currentTarget이 같은 버튼이어야 하는것도 알지 못한다.
우리는 타입스크립트가 알지 못하는 정보를 가지고 있기 때문에 여기서는 타입 단언문을 쓰는 것이 타당하다.  


### 2) 접미사로 쓰이는 `!`
> 접미사로 쓰이는 `!`을 사용하서 null이 아님을 단언할 수 있다.
```ts
const elNull = document.getElementById('foo');  // Type is HTMLElement | null
const el = document.getElementById('foo')!; // Type is HTMLElement
```
접미사로 쓰인 !는 그 값이 null이 아니라는 단언문으로 해석된다.   
단언문은 컴파일 과정 중에 제거되므로, 타입 체커는 알지 못하지만 그 값이 null이 아니라고 확신할 수 있을 때 사용해야 한다.  
만약 그렇지 않다면, null인 경우를 체크하는 조건문을 사용해야 한다.   

### 3) 타입 단언문으로 임의의 타입 간에 변환을 할 수 없다.

A가 B의 부분 집합인 경우에 타입 단언문을 사용해 변환할 수 있다.   
`HTMLElement`는 HTMLElement | null의 서브타입이기 때문에 이러한 타입 단언은 동작한다.   
`HTMLButtonElement`는 EventTarget의 서브타입이기 때문에 역시 동작한다.   
그리고 `Person`은 {}의 서브타입이므로 동작한다.    
> 그러나 Person과 HTMLElement는 서로의 서브타입이 아니기 때문에 변환이 불가능하다.
```ts
interface Person { name: string; }
const body = document.body;
const el = body as Person;
        // ~~~~~~~~~~~~~~ Conversion of type 'HTMLElement' to type 'Person'
        //                may be a mistake because neither type sufficiently
        //                overlaps with the other. If this was intentional,
        //                convert the expression to 'unknown' first
```
- 이 오류를 해결하려면 unknown 타입을 사용해야 한다.   
- 모든 타입은 unknown의 서브타입이기 때문에 unknown이 포함된 단언문은 항상 동작한다.   

<br>

>unknown 단언은 임의의 타입 간에 변환을 가능케 하지만, unknown을 사용한이상 적어도 무언가 위험한 동작을 하고 있다는 걸 알 수 있다.
```ts
interface Person { name: string; }
const el = document.body as unknown as Person;  // OK
```

## 📚 요약

 - 1. `타입 단언(as Type)`보다 `타입 선언(: Type)`을 사용해야 한다.   
 - 2. 화살표 함수의 반환 타입을 명시하는 방법을 터득해야 한다.
 - 3. 타입스크립트보다 타입 정보를 더 잘 알고 있는 상황에서는 타입 단언문과 null 아님 단언문을 사용하면 된다.

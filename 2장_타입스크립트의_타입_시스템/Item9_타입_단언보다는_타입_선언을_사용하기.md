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

화살표 함수의 타입 선언은 추론된 타입이 모호할 때가 있다.


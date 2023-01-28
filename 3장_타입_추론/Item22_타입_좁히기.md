## 📕 오늘 읽은 내용, 이런 식으로 정리해 봅시다. ✒

**TIL(Today I learn) 기록일** : 2022.01.28

**오늘 읽은 범위** : 22. 타입 좁히기

# Item 22. 타입 좁히기

타입 좁히기는 타입스크립트가 넓은 타입으로부터 좁은 타입으로 진행하는 과정을 말한다.   
아마도 가장 일반적인 예시는 null 체크일 것이다.   
```ts
const el = document.getElementById('foo'); // Type is HTMLElement | null
if (el) {
  el                                       // Type is HTMLElement
  el.innerHTML = 'Party Time'.blink();
} else {
  el                                       // Type is null
  alert('No element #foo');
}
```

만약 el이 null이라면, 분기문의 첫 번째 블록이 실행되지 않는다.   
즉, 첫 번째 블록에서 HTMLElement | null 타입의 null을 제외하므로, 더 좁은 타입이되어 작업이 훨씬 쉬워진다.    
타입 체커는 일반적으로 이러한 조건문에서 타입 좁히기를 잘 해내지만, 타입 별칭이 존재한다면 그러지 못할 수도 있다. (타입 별칭에 대한 내용은 아이템 24에서 다룬다)   
   
분기문에서 예외를 던지거나 함수를 반환하여 블록의 나머지 부분에서 변수의 타입을 좁힐 수도 있다.   
>예를 들어보자.
```ts
const el = document.getElementById('foo');        // Type is HTMLElement | null
if (!el) throw new Error('Unable to find #foo');
el;                                             // 
el.innerHTML = 'Party Time'.blink();
```
이 외에도 타입을 좁히는 방법은 많이 있다.   
다음은 instanceof를 사용해서 타입을 좁히는 예제다.   
   
```ts
function contains(text: string, search: string|RegExp) {
  if (search instanceof RegExp) {
    search  // Type is RegExp
    return !!search.exec(text);
  }
  search  // Type is string
  return text.includes(search);
}
```
>속성 체크로도 타입을 좁힐 수 있다.   
```ts
interface A { a: number }
interface B { b: number }
function pickAB(ab: A | B) {
  if ('a' in ab) {
    ab // Type is A
  } else {
    ab // Type is B
  }
  ab // Type is A | B
}
```

>Array.isArray 같은 일부 내장 함수로도 타입을 좁힐 수 있다.
```ts
function contains(text: string, terms: string|string[]) {
  const termList = Array.isArray(terms) ? terms : [terms];
  termList // Type is string[]
  // ...
}
```

타입스크립트는 일반적으로 조건문에서 타입을 좁히는 데 매우 능숙하다.   
그러나 타입을 섣불리 판단하는 실수를 저지르기 쉬우므로 다시 한번 꼼꼼히 따져 봐야 한다.   
>예를 들어, 다음 예제는 유니온 타입에서 null을 제외하기 위해 잘못된 방법을 사용했다.
```ts
const el = document.getElementById('foo'); // type is HTMLElement | null
if (typeof el === 'object') {
  el;  // Type is HTMLElement | null
}
```
>자바스크립트에서 typeof null이 "object"이기 때문에, if구문에서 null이 제외되지 않았습니다.   
>또한 기본형 값이 잘못되어도 비슷한 사례가 발생한다.   
```ts
function foo(x?: number|string|null) {
  if (!x) {
    x;  // Type is string | number | null | undefined
  }
}
```

빈 문자열 ''과 0 모두 false가 되기 때문에, 타입은 전혀 좁혀지지 않았고 x는 여전히 블록 내에서 string 또는 number가 된다.   
>타입을 좁히는 또 다른 일반적인 방법은 명시적 '태그'를 붙이는 것이다.   
```ts
interface UploadEvent { type: 'upload'; filename: string; contents: string }
interface DownloadEvent { type: 'download'; filename: string; }
type AppEvent = UploadEvent | DownloadEvent;

function handleEvent(e: AppEvent) {
  switch (e.type) {
    case 'download':
      e  // Type is DownloadEvent
      break;
    case 'upload':
      e;  // Type is UploadEvent
      break;
  }
}
```
이 패턴은 '태그된 유니온' 또는 '구별된 유니온'이라고 불리며, 타입스크립트 어디에서나 찾아볼 수 있다.   
   
>만약 타입스크립트가 타입을 식별하지 못한다면, 식별을 돕기 위해 커스텀 함수를 도입할 수 있다.   
```ts
function isInputElement(el: HTMLElement): el is HTMLInputElement {
  return 'value' in el;
}

function getElementContent(el: HTMLElement) {
  if (isInputElement(el)) {
    el; // Type is HTMLInputElement
    return el.value;
  }
  el; // Type is HTMLElement
  return el.textContent;
}
```

이러한 기법을 '사용자 정의 타입 가드'라고 한다.     
반환 타입의 el is HTMLInputElement는 함수의 반환이 true인 경우, 타입 체커에게 매개변수의 타입을 좁힐 수 있다고 알려준다.    
   
어떤 함수들은 타입 가드를 사용하여 배열과 객체의 타입 좁히기를 할 수 있다.   
예를 들어, 배열에서 어떤 탐색을 수행할 때 undefined가 될 수 있는 타입을 사용할 수 있다.   
```ts
const jackson5 = ['Jackie', 'Tito', 'Jermaine', 'Marlon', 'Michael'];
const members = ['Janet', 'Michael'].map(
  who => jackson5.find(n => n === who)
);  // Type is (string | undefined)[]
```
> filter 함수를 사용해 undefined를 걸러 내려고 해도 잘 동작하지 않을 것이다.
```ts
const jackson5 = ['Jackie', 'Tito', 'Jermaine', 'Marlon', 'Michael'];
const members = ['Janet', 'Michael'].map(
  who => jackson5.find(n => n === who)
).filter(who => who !== undefined);  // Type is (string | undefined)[]
```
> 이럴 때 타입 가드를 사용하면 타입을 좁힐 수 있다.
```ts
const jackson5 = ['Jackie', 'Tito', 'Jermaine', 'Marlon', 'Michael'];
function isDefined<T>(x: T | undefined): x is T {
  return x !== undefined;
}
const members = ['Janet', 'Michael'].map(
  who => jackson5.find(n => n === who)
).filter(isDefined);  // Type is string[]
```

<br>

편집기에서 타입을 조사하는 습관을 가지면 타입 좁히기가 어떻게 동작하는지 자연스레 익힐 수 있다.   
타입스크립트에서 타입이 어떻게 좁혀지는지 이해한다면 타입 추론에 대한 개념을 잡을 수 있고, 오류 발생의 원인을 알 수 있으며, 타입 체커를 더 효율적으로 이용할 수 있다.   
   
<br>

## 요약
- 분기문 외에도 여러 종류의 제어 흐름을 살펴보며 타입스크립트가 타입을 좁히는 과정을 이해해야 한다.   
- 태그된/구별된 유니온과 사용자 정의 타입 가드를 사용하여 타입 좁히기 과정을 원활하게 만들 수 있다.

## 📕 오늘 읽은 내용, 이런 식으로 정리해 봅시다. ✒

**TIL(Today I learn) 기록일** : 2022.02.01

**오늘 읽은 범위** : 26. 타입 추론에 문맥이 어떻게 사용되는지 이해하기

# Item 26. 타입 추론에 문맥이 어떻게 사용되는지 이해하기


타입스크립트는 타입을 추론할 때 단순히 값만 고려하지 않는다.   
값이 존재하는 곳의 문맥까지도 살핀다.   
그런데 문맥을 고려해 타입을 추론하면 가끔 이상한 결과가 나온다.   
이때 타입 추론에 문맥이 어떻게 사용되는지 이해하고 있다면 제대로 대처할 수 있다.   
     
자바스크립트는 코드의 동작과 실행순서를 바꾸지 않으면서 표현식을 상수로 분리해 낼 수 있다.   
>다음 예제를 보자
```js

// 인라인 형태
setLanguage('JavaScript');

// 참조 형태
let language = 'JavaScript';
setLanguage(language);
```
>타입 스크립트에서는 다음 리팩터링이 여전히 동작한다.
```ts
function setLanguage(language: string) { /* ... */ }

setLanguage('JavaScript');  // OK

let language = 'JavaScript';
setLanguage(language);  // OK
```

>이제 문자열 타입을 더 특정해서 문자열 리터럴 타입의 유니온으로 바꾼다고 가정해 보자
```ts
type Language = 'JavaScript' | 'TypeScript' | 'Python';
function setLanguage(language: Language) { /* ... */ }

setLanguage('JavaScript');  // OK

let language = 'JavaScript';
setLanguage(language);
         // ~~~~~~~~ Argument of type 'string' is not assignable
         //          to parameter of type 'Language'
```

이런 문제를 해결하는 두 가지 방법이 있다.   
>첫 번째 해법은 타입 선언에서 language의 가능한 값을 제한하는 것이다.
```ts
let language: Language = 'JavaScript';
setLanguage(language); // 정상

```
만약 language의 값에 typescript 같은 오타가 있었다면 오류를 표시해 주는 장점도 있다.   
   
>두 번째 해법은 language를 상수로 만드는 것이다.
```ts
const language= "JavaScript";
setLanguage(language); // 정상
```

그런데 이 광정에서 사용되는 문맥으로부터 값을 분리했다.   
문맥과 값을 분리하면 추후에 근본적인 문제를 발생시킬 수 있다.   
이제부터 이러한 문맥의 소실로 인해 오류가 발생하는 몇 가지 경우와, 이를 어떻게 해결하는지 하나하나 살펴보자.

### 튜플 사용시 주의점

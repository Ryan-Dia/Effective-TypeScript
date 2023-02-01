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

문자열 리터럴 타입과 마찬가지로 튜플 타입에서도 문제가 발생한다.   
>이동이 가능한 지도를 보여주는 프로그램을 작성한다고 생각해 보자
```ts
type Language = 'JavaScript' | 'TypeScript' | 'Python';
function setLanguage(language: Language) { /* ... */ }
// Parameter is a (latitude, longitude) pair.
function panTo(where: [number, number]) { /* ... */ }

panTo([10, 20]);  // OK

const loc = [10, 20];
panTo(loc);
//    ~~~ Argument of type 'number[]' is not assignable to
//        parameter of type '[number, number]'
```
loc 타입을 number[]로 추론한다.   
많은 배열이 이와 맞지 않는 수의 요소를 가지므로 튜플 타입에 할당할 수 없다.   
   
그러면 any를 사용하지 않고 오류를 고칠 수 있는 방법을 생각해 보자.   
   
타입스크립트가 의도를 정확히 파악할 수 있도록 타입 선언을 제공하는 방법을 시도해보자.   
```ts
const loc: [number, number] = [10, 20];
panTo(loc); // 정상
```

any를 사용하지 않고 또 고칠 수 있는 방법은 '상수 문맥'을 제공하는 것이다.   
const는 단지 값이 가리키는 참조가 변하지 않는 얕은 상수인 반면, as const는 그 값이 내부까지 상수라는 사실을 타입스크립트에게 알려준다.   
```ts
const loc = [10, 20] as const;
panTo(loc);
   // ~~~ Type 'readonly [10, 20]' is 'readonly'
   //     and cannot be assigned to the mutable type '[number, number]'
```
이 추론은 너무 과하게 정확하다.   
   
any를 사용하지 않고 오류를 고칠 수 잇는 최선의 해결책은 panTo 함수에 readonly 구문을 추가하는 것이다.   
```ts
function panTo(where: readonly [number, number]) { /* ... */ }
const loc = [10, 20] as const;
panTo(loc);  // OK
```

### 객체 사용 시 주의점


문맥에서 값을 분리하는 문제는 문자열 리터럴이나 튜플을 포함하는 큰 객체에서 상수를 뽑아낼 때도 발생한다.
```ts
type Language = 'JavaScript' | 'TypeScript' | 'Python';
interface GovernedLanguage {
  language: Language;
  organization: string;
}

function complain(language: GovernedLanguage) { /* ... */ }

complain({ language: 'TypeScript', organization: 'Microsoft' });  // OK

const ts = {
  language: 'TypeScript',
  organization: 'Microsoft',
};
complain(ts);
//       ~~ Argument of type '{ language: string; organization: string; }'
//            is not assignable to parameter of type 'GovernedLanguage'
//          Types of property 'language' are incompatible
//            Type 'string' is not assignable to type 'Language'
```
ts 객체에서 language의 타입은 string으로 추론된다.   
이 문제는 타입 선언을 추가하거나(const ts: GovernedLanguage = ...) 상수 단언(as const)을 사용해 해결하자.

## 요약

- 타입 추론에서 문맥이 어떻게 쓰이는지 주의해서 살펴보자
- 변수를 뽑아서 별도로 선언했을 때 오류가 발생한다면 타입 선언을 추가해야 한다.
- 변수가 정말로 상수라면 상수 단언(as const)을 사용해야 한다. 그러나 상수 단언을 사용하면 정의한 곳이 아니라 사용한 곳에서 오류가 발생하므로 주의해야 한다.

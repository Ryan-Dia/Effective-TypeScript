# 📕읽은내용 정리해보기


## 📚 Item 1 타입스크립트와 자바스크립트의 관계 이해하기

- 1. 타입스크립트는 문법적으로도 자바스크립트의 상위집합이다.
- 2. 타입스크립트는 자바스크립트와 다르게 .ts(또는 .tsx) 확장자를 사용한다.
- 3. 타입스크립트는 자바스크립트의 상위집합이기 때문에.js파일에 있는 코드는 이미 타입스크립트라고 할 수 있다.
  - 이러한 특성은 기족에 존재하는 자바스크립트 코드를 타입스크립트로 마이그레이션하는데 엄청난 이점이 된다.
  - 기존 코드를 그대로 유지하면서 일부분에만 타입스크립트 적용가능
- 4. 모든 자바스크립트 프로그램이 타입스크립트라는 명제는 참이지만, 그 반대는 성립하지 않는다. (TS 프로그램이지만 JS가 아닌 프로그램이 존재한다.)
  ```
  // 유효한 타입스크립트 코드
  function greet(who: string) {
    console.log('Hello', who);
  }
  
  // 위 코드를 자바스크립트로 구동하는 노드 같은 프로그램으로는 오류 발생
  function greet(who: string) {
  
  SyntaxError: Unexpected token :  
  ```
  ![image](https://user-images.githubusercontent.com/76567238/208400199-b332b694-6766-443e-add7-1fbdc9940193.png)

- 5. 타입스크립트 컴파일러는 타입스크립트뿐만 아니라 일반 자바스크립트 프로그램에도 유용하다. 
> 자바스크립트로 구동
```
let city = 'new york city';
console.log(city.toUppercase());

TypeError: city.toUppercase is not a function
```
> 타입스크립트로 구동
```
let city = 'new york city';
console.log(city.toUppercase());
 // 'toUppercase' 속성이 'string' 형식에 없습니다.
 // 'toUpperCase'을(를) 사용하시겠습니까?
```
- city 변수가 문자열이라는 것을 알려 주지 않아도 타입스크립트는 초깃값으로부터 타입을 추론합니다. 
- 타입 시스템의 목표 중 하나는 런타임에 오류를 발생시킬 코드를 미리 찾아내는 것입니다. (TS가 '정적'타입 시스템이라는 것은 바로 이런 특징을 말하는 것입니다.)

- 6. 타입 체커가 모든 오류를 찾아내지는 않는다.   

```
// 자바스크립트 프로그램
const states = [
{name: 'Alabama', capital: 'Montgomery'},
{name: 'Alaska',  capital: 'Juneau'},
{name: 'Arizona', capital: 'Phoenix'},
// ...
];
for (const state of states) {
  console.log(state.capitol);
}
// 결과 출력
// undefined
// undefined
// undefined

```
---
 - 위 코드는 유효한 자바스크립트(TS)이며 어떠한 오류도 없이 실행된다.   
 - 그러나 루프 내의 state.capitol은 의도한 코드가 아닌게 분명하다. 
 - 바로 이때 타입스크립트 타입 체커는 추가적인 타입 구문 없이도 오류를 찾아낸다.
```
// 타입스크립트 프로그램
for (const state of states) {
  console.log(state.capitol);
}
  // 'capitol' 속성이 ... 형식에 없습니다.
  // 'capital' 을(를) 사용하시겠습니까?
```

 - 타입스크립트는 타입 구문 없이도 오류를 잡을 수 있지만, 타입 구문을 추가한다면 훨씬 더 많은 오류를 찾아낼 수 있다.
 - 코드의 '의도'가 무엇인지 타입구문을 통해 타입스크립트에게 알려줄 수 있기 때문에 코드의 동작과 의도가 다른 부분을 찾을 수 있습니다.
```
// 타입스크립트 프로그램
const states = [
{name: 'Alabama', capitol: 'Montgomery'},
{name: 'Alaska',  capitol: 'Juneau'},
{name: 'Arizona', capitol: 'Phoenix'},
// ...
];
for (const state of states) {
  console.log(state.capital);
}
  // 'capital' 속성이 ... 형식에 없습니다.
  // 'capitol' 을(를) 사용하시겠습니까?
```
하지만 위 코드에서 타입스크립트가 제시한 해결책은 잘못되었다.     
한곳에서는 capital로, 다른 한곳에서는 capitol로 다르게 타이핑했지만, 타입스크립트는 어느 쪽이 오타인지 판단하지 못한다.   
오류의 원인을 추축할 수는 있지만 정확하지는 않다.   
따라서 명시적으로 states를 선언하여 의도를 분명하게 하는 것이 좋다.   

```
// TS
interface State {
  name: string;
  capital: string;
}

const states : state[] = [
  {name: 'Alabama', capitol: 'Montgomery'},
  {name: 'Alaska',  capitol: 'Juneau'},
  {name: 'Arizona', capitol: 'Phoenix'},
  
          // 개체 리터럴은 알려진 속성만 지정할 수 있지만
          // 'State' 형식에 'capitol'이(가) 없습니다.
          // 'capital'을(를) 쓰려고 했습니까?
  // ...
];
for (const state of states) {
  console.log(state.capital);
}
```
의도를 명확히 해서 타입스크립트가 잠재적 문제점을 찾을 수 있게 했다.

![image](https://user-images.githubusercontent.com/76567238/208406959-3e308e91-0abd-4316-a28b-52048d923497.png)

- 모든 자바스크립트는 타입스크립트지만, 일부 자바스크립트(그리고 타입스크립트)만이 타입 체크를 통과한다.

- 7. 타입스크립트 타입 시스템은 자바스크립트의 런타임 동작을 '모델링'한다.
```
const x = 2 + '3'; // 정상, string 타입입니다.
const y = '2' + 3; // 정상, string 타입입니다.
``` 
- 이 예제는 다른 언어였다면 런타임 오류가 될 만한 코드이다. 하지만 타입스크립트의 타입 체커는 정삭으로 인식한다.
- 반대로 정상 동작하는 코드에 오류를 표시하기도 한다.
```
// 다음은 런타임 오류가 발생하지 않는 코드인데, 타입 체커는 문제점을 표시한다.
```

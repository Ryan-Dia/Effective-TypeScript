# 📕읽은내용 정리해보기

```
타입스크립트의 가장 중요한 역할은 타입 시스템에 있다.
2장에서는 타입 시스템의 기초부터 살펴본다.
타입 시스템이란 무엇인지,
어떻게 사용해야 하는지,
가급적 사용하지 말아야 할 기능은 무엇인지 알아본다.

```

## 📚 Item 6 편집기를 사용하여 타입 시스템 탐색하기
타입스크립트를 설치하면, 다음 두 가지를 실행할 수 있습니다.
- 타입스크립트 컴파일러(tsc)
- 단독으로 실행할 수 있는 타입스크립트 서버(tsserver)

보통 타입스크립트 컴파일러를 실행하는 것이 주된 목적이지만, 타입스크립트 서버 또한 '언어 서비스'를 제공한다는 점에서 중요하다.   
언어 서비스에는 다음와 같은 기능이 존재한다.
- 코드 자동 완성
- 명세(사양) 검사
- 검색
- 리팩터링

### 1) 편집기상의 타입 오류 살피기
> 다음은 id에 해당하거나 기본값인 HTMLElement를 반환하는 함수이다.
> 타입스크립트는 두 곳에서 오류를 발생시킨다.
```ts
function getElement(elOrId: string|HTMLElement|null): HTMLElement {
  if (typeof elOrId === 'object') {
    return elOrId;
 // ~~~~~~~~~~~~~~ 'HTMLElement | null' is not assignable to 'HTMLElement'
  } else if (elOrId === null) {
    return document.body;
  } else {
    const el = document.getElementById(elOrId);
    return el;
 // ~~~~~~~~~~ 'HTMLElement | null' is not assignable to 'HTMLElement'
  }
}
```

첫 번째 if분기문의 의도는 단지 HTMLElement라는 객체를 골라내는 것이었다.   
그러나 **자바스크립트에서 typeof null은 "object"이므로, elOrId는 여전히 분기문 내에서 null일 가능성이 있다.**   
그러므로 처음에 null 체크를 추가해서 바로잡는다.   
<br>
두 번째 오류는 document.getElementById가 null을 반환할 가능성이 있어서 발생했고, 첫 번째 오류와 동일하게 null 체크를 추가하고 예외를 던져야 한다.

### 2) 언어 서비스

> 언어 서비스는 라이브러리와 라이브러리의 타입 선언을 탐색할 때 도움이 된다.   
> 코드 내에서 fetch 함수가 호출되고, 이 함수를 더 알아보길 원한다고 가정해 보자
- 편집기는 'Go to Definition(정의로 이동)' 옵션을 제공한다.
이 옵션을 선택하면 타입스크립트에 포함되어 있는 DOM 타입 선언인 lib.dom.d.ts로 이동한다.
```ts
declare function fetch(
  input: RequestInfo, init?: RequestInit
): Promise<Response>;
```
- fetch가 Promise를 반환하고 두 개의 매개변수를 받는 것을 볼 수 있다.   
>RequestInfo를 클릭하면 다음으로 이동한다.
```ts
declare var Request: {
    prototype: Request;
    new(input: RequestInfo, init?: RequestInit): Request;
};
```
- 여기서 Request 타입과 값은 분리되어 모델링되어 있다.   
>Request Info는 이미 살펴보았고, RequestInit를 클릭하면 Request를 생성할 때 사용할 수 있는 모든 옵션이 나타난다.
```ts
interface RequestInit {
    body?: BodyInit | null;
    cache?: RequestCache;
    credentials?: RequestCredentials;
    headers?: HeadersInit;
    // ...
}
```

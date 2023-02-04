## 📕 오늘 읽은 내용, 이런 식으로 정리해 봅시다. ✒

**TIL(Today I learn) 기록일** : 2022.02.04

**오늘 읽은 범위** : 28. 함수형 기법과 라이브러리로 타입 흐름 유지기

# Item 28. 함수형 기법과 라이브러리로 타입 흐름 유지기

타입을 잘 설계하면 코드는 직관적으로 작성할 수 있다.   
그러나 타입 설계가 엉망이라면 어떠한 기억이나 문서도 도움이 되지 못한다.   
   
효과적으로 타입을 설계하려면, 유효한 상태만 표현할 수 있는 타입을 만들어 내는 것이 가장 중요하다.   
아이템 28은 이런 관점에서 타입 설계가 잘못된 상황을 알아보고, 예제를 통해 잘못된 설계를 바로잡아 볼 것이다.   
    
웹 애플리케이션을 만든다고 가정해보자   
애플리케이션에서 페이지를 선택하면 페이지의 내용을 로드하고 화면을 표시한다.   
페이지의 상태는 다음처럼 설계하였다.   
```ts
interface State {
  pageText: string;
  isLoading: boolean;
  error?: string;
}
```
>페이지를 그리느 renderPage 함수를 작성할 때는 상태 객체의 필드를 전부 고려해서 상태 표시를 분기해야 한다.   
```ts
function renderPage(state: State) {
  if (state.error) {
    return `Error! Unable to load ${currentPage}: ${state.error}`;
  } else if (state.isLoading) {
    return `Loading ${currentPage}...`;
  }
  return `<h1>${currentPage}</h1>\n${state.pageText}`;
}
```
이 코드는 조건이 명확히 분리되어 있지 않다.   
`isLoading`이 `true`이고 동시에 `error`값이 존재하면 로딩 중인 상태인지 오류가 발생한 상태인지 명확히 구분할 수 없다.   
   
      
>한편 페이지를 전환하는 changePage 함수는 다음과 같다.
```ts
async function changePage(state: State, newPage: string) {
  state.isLoading = true;
  try {
    const response = await fetch(getUrlForPage(newPage));
    if (!response.ok) {
      throw new Error(`Unable to load ${newPage}: ${response.statusText}`);
    }
    const text = await response.text();
    state.isLoading = false;
    state.pageText = text;
  } catch (e) {
    state.error = '' + e;
  }
}
```
여기에도 많은 문제가 존재한다.   
- 오류가 발생했을 때 state.isLoading을 flase로 설정한는 로직이 빠져있다.
- state.error를 초기화하지 않았기 때문에, 페이지 전환 중에 로딩 메시지 대신 과거의 오류 메시지를 보여 주게 된다.   
- 페이지 로딩 중에 사용자가 페이지를 바꿔 버리면 어떤 일이 벌어질지 예상하기 어렵다. 새 페이지에 오류가 뜨거나, 응답이 오는 순서에 따라 두 번째 페이지가 아닌 첫 번째 페이지로 전환될 수도 있다.

이와 같이 문제는 바로 상태 값의 두 가지 속성이 동시에 정보가 부족하거나(요청이 실패한 것인지 여전히 로딩 중인지 알 수 없다),    
두 가지 속성이 충돌(오류이면서 동시에 로딩 중일 수 있다)   
State 타입은 isLoading이 true이면서 동시에 error 값이 설정되는 무효한 상태를 허용한다.   
무효한 상태가 존재하면 render()와 changePage() 둘 다 제대로 구현할 수 없게 된다.   
   
위 코드를 조금 더 수정해보자
```ts

interface RequestPending {
  state: 'pending';
}
interface RequestError {
  state: 'error';
  error: string;
}
interface RequestSuccess {
  state: 'ok';
  pageText: string;
}
type RequestState = RequestPending | RequestError | RequestSuccess;

interface State {
  currentPage: string;
  requests: {[page: string]: RequestState};
}
```
무효한 상태를 허용하지 않도록 크게 개선되었다.   
현재 페이지는 발생하는 모든 요청의 상태로서, 명시적으로 모델링되었다.   
>그 결과 개선된 renderPage와 changePage 함수는 쉽게 구현할 수 있다.   
```ts
function renderPage(state: State) {
  const {currentPage} = state;
  const requestState = state.requests[currentPage];
  switch (requestState.state) {
    case 'pending':
      return `Loading ${currentPage}...`;
    case 'error':
      return `Error! Unable to load ${currentPage}: ${requestState.error}`;
    case 'ok':
      return `<h1>${currentPage}</h1>\n${requestState.pageText}`;
  }
}

async function changePage(state: State, newPage: string) {
  state.requests[newPage] = {state: 'pending'};
  state.currentPage = newPage;
  try {
    const response = await fetch(getUrlForPage(newPage));
    if (!response.ok) {
      throw new Error(`Unable to load ${newPage}: ${response.statusText}`);
    }
    const pageText = await response.text();
    state.requests[newPage] = {state: 'ok', pageText};
  } catch (e) {
    state.requests[newPage] = {state: 'error', error: '' + e};
  }
}
```
이번 아이템의 처음에 등장했던 renderPage와 changePage의 모호함은 완전히 사라졌다.   


---

조종사 예제

```ts
interface RequestPending {
  state: 'pending';
}
interface RequestError {
  state: 'error';
  error: string;
}
interface RequestSuccess {
  state: 'ok';
  pageText: string;
}
type RequestState = RequestPending | RequestError | RequestSuccess;

interface State {
  currentPage: string;
  requests: {[page: string]: RequestState};
}
function getUrlForPage(p: string) { return ''; }
interface CockpitControls {
  /** Angle of the left side stick in degrees, 0 = neutral, + = forward */
  leftSideStick: number;
  /** Angle of the right side stick in degrees, 0 = neutral, + = forward */
  rightSideStick: number;
}
function getStickSetting(controls: CockpitControls) {
  return controls.leftSideStick;
}
```
> 부기장(오른쪽 스틱)이 제어하고 있는 상태라면 기장의 스틱 상태는 중립일 것이다.   
> 결과적으로 기장이든 부기장이든 둥 중 하나의 스틱 값 중에서 중립이 아닌 값을 사용해야 한다.
```ts
function getStickSetting(controls: CockpitControls) {
  const {leftSideStick, rightSideStick} = controls;
  if (leftSideStick === 0) {
    return rightSideStick;
  }
  return leftSideStick;
}
```
그러나 이 코드에는 문제가 있다.
왼쪽 스틱의 로직과 동일하게 오른쪽 스틱이 중립일 때 왼쪽 스틱 값을 사용해야 한다.   
그러므로 오른쪽 스틱에 대한 체크를 코드에 추가해야 한다.
```ts
function getStickSetting(controls: CockpitControls) {
  const {leftSideStick, rightSideStick} = controls;
  if (leftSideStick === 0) {
    return rightSideStick;
  } else if (rightSideStick === 0) {
    return leftSideStick;
  }
  // ???
}
```
>두 스틱이 모두 중립이 아닌 경우를 고려해 보자.   
>다행히 두 스틱이 비슷한 값이라면 스틱의 각도를 평균해서 계산할 수 있다.
```ts
function getStickSetting(controls: CockpitControls) {
  const {leftSideStick, rightSideStick} = controls;
  if (leftSideStick === 0) {
    return rightSideStick;
  } else if (rightSideStick === 0) {
    return leftSideStick;
  }
  if (Math.abs(leftSideStick - rightSideStick) < 5) {
    return (leftSideStick + rightSideStick) / 2;
  }
  // ???
}
```


## 요약

- 유효한 상태와 무효한 상태를 둘 다 표현하는 타입은 혼란을 초래하기 쉽고 오류를 유발하게 된다.  
- 유효한 상태만 표현하는 타입을 지향해야 한다. 코드가 길어지거나 표현하기 어렵지만 결국은 시간을 절약하고 고통을 줄일 수 있다.

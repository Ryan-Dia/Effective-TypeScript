## π μ€λ μ½μ λ΄μ©, μ΄λ° μμΌλ‘ μ λ¦¬ν΄ λ΄μλ€. β

**TIL(Today I learn) κΈ°λ‘μΌ** : 2022.02.04

**μ€λ μ½μ λ²μ** : 28. μ ν¨ν μνλ§ νννλ νμ μ§ν₯νκΈ°

# Item 28. μ ν¨ν μνλ§ νννλ νμ μ§ν₯νκΈ°

νμμ μ μ€κ³νλ©΄ μ½λλ μ§κ΄μ μΌλ‘ μμ±ν  μ μλ€.   
κ·Έλ¬λ νμ μ€κ³κ° μλ§μ΄λΌλ©΄ μ΄λ ν κΈ°μ΅μ΄λ λ¬Έμλ λμμ΄ λμ§ λͺ»νλ€.   
   
ν¨κ³Όμ μΌλ‘ νμμ μ€κ³νλ €λ©΄, μ ν¨ν μνλ§ ννν  μ μλ νμμ λ§λ€μ΄ λ΄λ κ²μ΄ κ°μ₯ μ€μνλ€.   
μμ΄ν 28μ μ΄λ° κ΄μ μμ νμ μ€κ³κ° μλͺ»λ μν©μ μμλ³΄κ³ , μμ λ₯Ό ν΅ν΄ μλͺ»λ μ€κ³λ₯Ό λ°λ‘μ‘μ λ³Ό κ²μ΄λ€.   
    
μΉ μ νλ¦¬μΌμ΄μμ λ§λ λ€κ³  κ°μ ν΄λ³΄μ   
μ νλ¦¬μΌμ΄μμμ νμ΄μ§λ₯Ό μ ννλ©΄ νμ΄μ§μ λ΄μ©μ λ‘λνκ³  νλ©΄μ νμνλ€.   
νμ΄μ§μ μνλ λ€μμ²λΌ μ€κ³νμλ€.   
```ts
interface State {
  pageText: string;
  isLoading: boolean;
  error?: string;
}
```
>νμ΄μ§λ₯Ό κ·Έλ¦¬λ renderPage ν¨μλ₯Ό μμ±ν  λλ μν κ°μ²΄μ νλλ₯Ό μ λΆ κ³ λ €ν΄μ μν νμλ₯Ό λΆκΈ°ν΄μΌ νλ€.   
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
μ΄ μ½λλ μ‘°κ±΄μ΄ λͺνν λΆλ¦¬λμ΄ μμ§ μλ€.   
`isLoading`μ΄ `true`μ΄κ³  λμμ `error`κ°μ΄ μ‘΄μ¬νλ©΄ λ‘λ© μ€μΈ μνμΈμ§ μ€λ₯κ° λ°μν μνμΈμ§ λͺνν κ΅¬λΆν  μ μλ€.   
   
      
>ννΈ νμ΄μ§λ₯Ό μ ννλ changePage ν¨μλ λ€μκ³Ό κ°λ€.
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
μ¬κΈ°μλ λ§μ λ¬Έμ κ° μ‘΄μ¬νλ€.   
- μ€λ₯κ° λ°μνμ λ state.isLoadingμ flaseλ‘ μ€μ νλ λ‘μ§μ΄ λΉ μ Έμλ€.
- state.errorλ₯Ό μ΄κΈ°ννμ§ μμκΈ° λλ¬Έμ, νμ΄μ§ μ ν μ€μ λ‘λ© λ©μμ§ λμ  κ³Όκ±°μ μ€λ₯ λ©μμ§λ₯Ό λ³΄μ¬ μ£Όκ² λλ€.   
- νμ΄μ§ λ‘λ© μ€μ μ¬μ©μκ° νμ΄μ§λ₯Ό λ°κΏ λ²λ¦¬λ©΄ μ΄λ€ μΌμ΄ λ²μ΄μ§μ§ μμνκΈ° μ΄λ ΅λ€. μ νμ΄μ§μ μ€λ₯κ° λ¨κ±°λ, μλ΅μ΄ μ€λ μμμ λ°λΌ λ λ²μ§Έ νμ΄μ§κ° μλ μ²« λ²μ§Έ νμ΄μ§λ‘ μ νλ  μλ μλ€.

μ΄μ κ°μ΄ λ¬Έμ λ λ°λ‘ μν κ°μ λ κ°μ§ μμ±μ΄ λμμ μ λ³΄κ° λΆμ‘±νκ±°λ(μμ²­μ΄ μ€ν¨ν κ²μΈμ§ μ¬μ ν λ‘λ© μ€μΈμ§ μ μ μλ€),    
λ κ°μ§ μμ±μ΄ μΆ©λ(μ€λ₯μ΄λ©΄μ λμμ λ‘λ© μ€μΌ μ μλ€)   
State νμμ isLoadingμ΄ trueμ΄λ©΄μ λμμ error κ°μ΄ μ€μ λλ λ¬΄ν¨ν μνλ₯Ό νμ©νλ€.   
λ¬΄ν¨ν μνκ° μ‘΄μ¬νλ©΄ render()μ changePage() λ λ€ μ λλ‘ κ΅¬νν  μ μκ² λλ€.   
   
μ μ½λλ₯Ό μ‘°κΈ λ μμ ν΄λ³΄μ
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
λ¬΄ν¨ν μνλ₯Ό νμ©νμ§ μλλ‘ ν¬κ² κ°μ λμλ€.   
νμ¬ νμ΄μ§λ λ°μνλ λͺ¨λ  μμ²­μ μνλ‘μ, λͺμμ μΌλ‘ λͺ¨λΈλ§λμλ€.   
>κ·Έ κ²°κ³Ό κ°μ λ renderPageμ changePage ν¨μλ μ½κ² κ΅¬νν  μ μλ€.   
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
μ΄λ² μμ΄νμ μ²μμ λ±μ₯νλ renderPageμ changePageμ λͺ¨νΈν¨μ μμ ν μ¬λΌμ‘λ€.   


---

μ‘°μ’μ¬ μμ 

```ts

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
> λΆκΈ°μ₯(μ€λ₯Έμͺ½ μ€ν±)μ΄ μ μ΄νκ³  μλ μνλΌλ©΄ κΈ°μ₯μ μ€ν± μνλ μ€λ¦½μΌ κ²μ΄λ€.   
> κ²°κ³Όμ μΌλ‘ κΈ°μ₯μ΄λ  λΆκΈ°μ₯μ΄λ  λ₯ μ€ νλμ μ€ν± κ° μ€μμ μ€λ¦½μ΄ μλ κ°μ μ¬μ©ν΄μΌ νλ€.
```ts
function getStickSetting(controls: CockpitControls) {
  const {leftSideStick, rightSideStick} = controls;
  if (leftSideStick === 0) {
    return rightSideStick;
  }
  return leftSideStick;
}
```
κ·Έλ¬λ μ΄ μ½λμλ λ¬Έμ κ° μλ€.
μΌμͺ½ μ€ν±μ λ‘μ§κ³Ό λμΌνκ² μ€λ₯Έμͺ½ μ€ν±μ΄ μ€λ¦½μΌ λ μΌμͺ½ μ€ν± κ°μ μ¬μ©ν΄μΌ νλ€.   
κ·Έλ¬λ―λ‘ μ€λ₯Έμͺ½ μ€ν±μ λν μ²΄ν¬λ₯Ό μ½λμ μΆκ°ν΄μΌ νλ€.
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
>λ μ€ν±μ΄ λͺ¨λ μ€λ¦½μ΄ μλ κ²½μ°λ₯Ό κ³ λ €ν΄ λ³΄μ.   
>λ€νν λ μ€ν±μ΄ λΉμ·ν κ°μ΄λΌλ©΄ μ€ν±μ κ°λλ₯Ό νκ· ν΄μ κ³μ°ν  μ μλ€.
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


## μμ½

- μ ν¨ν μνμ λ¬΄ν¨ν μνλ₯Ό λ λ€ νννλ νμμ νΌλμ μ΄λνκΈ° μ½κ³  μ€λ₯λ₯Ό μ λ°νκ² λλ€.  
- μ ν¨ν μνλ§ νννλ νμμ μ§ν₯ν΄μΌ νλ€. μ½λκ° κΈΈμ΄μ§κ±°λ νννκΈ° μ΄λ ΅μ§λ§ κ²°κ΅­μ μκ°μ μ μ½νκ³  κ³ ν΅μ μ€μΌ μ μλ€.



--- 

μ½λ μμ μ μ₯
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

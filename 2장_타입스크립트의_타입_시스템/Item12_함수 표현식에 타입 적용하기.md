# 📕읽은내용 정리해보기



# 📚 Item 12 함수 표현식에 타입 적용하기

>자바스크립트(그리고 타입스크립트)에서는 함수 '문장(statement)'과 함수 '표현식(expression)'을 다르게 인식 한다.
```ts
function rollDice1(sides: number): number { /* COMPRESS */ return 0; /* END */ }  // 문장
const rollDice2 = function(sides: number): number { /* COMPRESS */ return 0; /* END */ };  // 표현식
const rollDice3 = (sides: number): number => { /* COMPRESS */ return 0; /* END */ };  // 표현식
```
타입스크립트에서는 `함수 표현식`을 사용하는 것이 좋다.   
>함수의 매개변수부터 반환값까지 함수 타입으로 선언하여 함수 표현식에 재사용할 수 있다는 장점이 있기 때문이다.
```ts
type DiceRollFn = (sides: number) => number;
const rollDice: DiceRollFn = sides => { /* COMPRESS */ return 0; /* END */ };
```
편집기에서 sides에 마우스를 올려 보면, 타입스크립트에서는 이미 sides의 타입을 number로 인식하고 있다는 걸 알 수 있다.   
함수 타입 선언의 장점을 조금 더 알아 보자   
   
## 함수 타입의 선언은 불필요한 코드의 반복을 줄여준다.   
   >사칙연산을 하는 함수 네 개는 다음과 같이 작성할 수 있다.   
   ```ts
    function add(a: number, b: number) { return a + b; }
    function sub(a: number, b: number) { return a - b; }
    function mul(a: number, b: number) { return a * b; }
    function div(a: number, b: number) { return a / b; }
   ```
   >반복되는 함수 시그니처를 하나의 함수 타입으로 통합할 수도 있다.   
   ```ts
      type BinaryFn = (a: number, b: number) => number;
      const add: BinaryFn = (a, b) => a + b;
      const sub: BinaryFn = (a, b) => a - b;
      const mul: BinaryFn = (a, b) => a * b;
      const div: BinaryFn = (a, b) => a / b;
   ```
   
   ### 시그니처가 일치하는 다른 함수가 있을 때도 함수 표현식에 타입을 적용해볼 만하다. (이해 잘 x)  
   >예를 들어 웹브라우저에서 fetch 함수는 특정 리소스에 HTTP요청을 보낸다.
   ```ts
   const responseP = fetch('/quote?by=Mark+Twain');  // 타입이 Promise<Response>
   ```
   >그리고 response.json() 또는 response.text()를 사용해 응답의 데이터를 추출한다.  
   ```ts
       async function getQuote() {
      const response = await fetch('/quote?by=Mark+Twain');
      const quote = await response.json();
      return quote;
    }
    // {
    //   "quote": "If you tell the truth, you don't have to remember anything.",
    //   "source": "notebook",
    //   "date": "1894"
    // }
   ```
   여기에 버그가 있다.   
   /quote가 존재하지 않는 API라면, '404 Not Found'가 포함된 내용을 응답한다.   
   응답은 JSON형식이 아닐 수 있다.   
   response.json()은 JSON형식이 아니라는 새로운 오류 메시지를 담아 거절된 프로미스를 반환한다.   
   호출한 곳에서는 새로운 오류 메시지가 전달되어 실제 오류인 404가 감추어진다.   
   또한 fetch가 실패하면 거절된 프로미스를 응답하지는 않는다는 걸 간과하기 쉽다.   
   그러니 상태 체크를 수행해 줄 checkedFetch 함수를 작성해 작성해 본다면 아래와 같다.
   >fetch의 타입 선언은 lib.dom.d.ts에 있으며 다음과 같다.
   ```ts
     declare function fetch(
      input: RequestInfo, init?: RequestInit
      ): Promise<Response>;
   ```
   >checkdFetch는 다음처럼 작성할 수 있다.
   ```ts  
      async function checkedFetch(input: RequestInfo, init?: RequestInit) {
        const response = await fetch(input, init);
        if (!response.ok) {
          // 비동기 함수 내에서는 거절된 프로미스로 반환한다.
          throw new Error('Request failed: ' + response.status);
        }
        return response;
      }
   ```
   > 이 코드도 잘 동작하지만 다음과 같이 더 간결하게 작성할 수 있다.   
   ```ts
       const checkedFetch: typeof fetch = async (input, init) => {
          const response = await fetch(input, init);
          if (!response.ok) {
            throw new Error('Request failed: ' + response.status);
          }
          return response;
        }
   ```
   함수 문장을 함수 표현식으로 바꿨고 함수 전체에 타입(typeof fetch)을 적용했다.   
   이는 타입스크립트가 input과 init의 타입을 추론할 수 있게 해준다.
      
  타입 구문은 또한 checkedFetch의 반환 타입을 보장하며, fetch와 동일하다.   
  >예를 들어 throw 대신 return을 사용했다면, 타입스크립트는 그 실수를 잡아낸다.   
  ```ts
          const checkedFetch: typeof fetch = async (input, init) => {
            //  ~~~~~~~~~~~~   Type 'Promise<Response | HTTPError>' 형식은
            //                     'Promise<Response>' 형식에 할당할 수 없다.
            //                   Type 'Response | HTTPError' is not assignable
            //                       to type 'Response'
            const response = await fetch(input, init);
            if (!response.ok) {
              return new Error('Request failed: ' + response.status);
            }
            return response;
          }
  ```
      
      checkedFetch를 함수 문장으로 작성한 예제에서도 throw가 아니라 return을 사용할 경우 오류가 발생한다.   
      그러나 오류는 첫 번째 예제와 달리 checkdFetch 구현체가 아닌, 함수를 호출한 위치에서 발생한다.   
         
   **함수 매개변수에 타입 선언을 하는 것보다 함수 표현식 전체 타입을 정의하는 것이 코드도 간결하고 안전하다.**   
   다른 함수의 시그니처와 동일한 타입을 가지는 새 함수를 작성하거나, 동일한 타입 시그니처를 가지는 여러 개의 함수를 작성할 때는 매개변수의 타입과 반환 타입을 반복해서 작성하지 말고 함수전체의 타입 선언을 적용해야 한다.   
   
   ## 요약
   - 매개변수나 반환 값에 타입을 명시하기보다는 함수 표현식 전체에 타입 구문을 적용하는 것이 좋다.   
   - 만약 같은 타입 시그니처를 반복적으로 작성한 코드가 있다면 함수 타입을 분리해 내거나 이미 존재하는 타입을 찾아보도록 한다. 라이브러리를 직접 만든다면 공통 콜백에 타입을 제공해야 한다.   
   - 다른 함수의 시그니처를 참조하려면 typeof fn을 사용하면 된다.

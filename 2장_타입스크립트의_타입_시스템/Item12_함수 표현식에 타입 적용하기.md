# πμ½μλ΄μ© μ λ¦¬ν΄λ³΄κΈ°



# π Item 12 ν¨μ ννμμ νμ μ μ©νκΈ°

>μλ°μ€ν¬λ¦½νΈ(κ·Έλ¦¬κ³  νμμ€ν¬λ¦½νΈ)μμλ ν¨μ 'λ¬Έμ₯(statement)'κ³Ό ν¨μ 'ννμ(expression)'μ λ€λ₯΄κ² μΈμ νλ€.
```ts
function rollDice1(sides: number): number { /* COMPRESS */ return 0; /* END */ }  // λ¬Έμ₯
const rollDice2 = function(sides: number): number { /* COMPRESS */ return 0; /* END */ };  // ννμ
const rollDice3 = (sides: number): number => { /* COMPRESS */ return 0; /* END */ };  // ννμ
```
νμμ€ν¬λ¦½νΈμμλ `ν¨μ ννμ`μ μ¬μ©νλ κ²μ΄ μ’λ€.   
>ν¨μμ λ§€κ°λ³μλΆν° λ°νκ°κΉμ§ ν¨μ νμμΌλ‘ μ μΈνμ¬ ν¨μ ννμμ μ¬μ¬μ©ν  μ μλ€λ μ₯μ μ΄ μκΈ° λλ¬Έμ΄λ€.
```ts
type DiceRollFn = (sides: number) => number;
const rollDice: DiceRollFn = sides => { /* COMPRESS */ return 0; /* END */ };
```
νΈμ§κΈ°μμ sidesμ λ§μ°μ€λ₯Ό μ¬λ € λ³΄λ©΄, νμμ€ν¬λ¦½νΈμμλ μ΄λ―Έ sidesμ νμμ numberλ‘ μΈμνκ³  μλ€λ κ±Έ μ μ μλ€.   
ν¨μ νμ μ μΈμ μ₯μ μ μ‘°κΈ λ μμ λ³΄μ   
   
## ν¨μ νμμ μ μΈμ λΆνμν μ½λμ λ°λ³΅μ μ€μ¬μ€λ€.   
   >μ¬μΉμ°μ°μ νλ ν¨μ λ€ κ°λ λ€μκ³Ό κ°μ΄ μμ±ν  μ μλ€.   
   ```ts
    function add(a: number, b: number) { return a + b; }
    function sub(a: number, b: number) { return a - b; }
    function mul(a: number, b: number) { return a * b; }
    function div(a: number, b: number) { return a / b; }
   ```
   >λ°λ³΅λλ ν¨μ μκ·Έλμ²λ₯Ό νλμ ν¨μ νμμΌλ‘ ν΅ν©ν  μλ μλ€.   
   ```ts
      type BinaryFn = (a: number, b: number) => number;
      const add: BinaryFn = (a, b) => a + b;
      const sub: BinaryFn = (a, b) => a - b;
      const mul: BinaryFn = (a, b) => a * b;
      const div: BinaryFn = (a, b) => a / b;
   ```
   
   ### μκ·Έλμ²κ° μΌμΉνλ λ€λ₯Έ ν¨μκ° μμ λλ ν¨μ ννμμ νμμ μ μ©ν΄λ³Ό λ§νλ€. (μ΄ν΄ μ x)  
   >μλ₯Ό λ€μ΄ μΉλΈλΌμ°μ μμ fetch ν¨μλ νΉμ  λ¦¬μμ€μ HTTPμμ²­μ λ³΄λΈλ€.
   ```ts
   const responseP = fetch('/quote?by=Mark+Twain');  // νμμ΄ Promise<Response>
   ```
   >κ·Έλ¦¬κ³  response.json() λλ response.text()λ₯Ό μ¬μ©ν΄ μλ΅μ λ°μ΄ν°λ₯Ό μΆμΆνλ€.  
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
   μ¬κΈ°μ λ²κ·Έκ° μλ€.   
   /quoteκ° μ‘΄μ¬νμ§ μλ APIλΌλ©΄, '404 Not Found'κ° ν¬ν¨λ λ΄μ©μ μλ΅νλ€.   
   μλ΅μ JSONνμμ΄ μλ μ μλ€.   
   response.json()μ JSONνμμ΄ μλλΌλ μλ‘μ΄ μ€λ₯ λ©μμ§λ₯Ό λ΄μ κ±°μ λ νλ‘λ―Έμ€λ₯Ό λ°ννλ€.   
   νΈμΆν κ³³μμλ μλ‘μ΄ μ€λ₯ λ©μμ§κ° μ λ¬λμ΄ μ€μ  μ€λ₯μΈ 404κ° κ°μΆμ΄μ§λ€.   
   λν fetchκ° μ€ν¨νλ©΄ κ±°μ λ νλ‘λ―Έμ€λ₯Ό μλ΅νμ§λ μλλ€λ κ±Έ κ°κ³ΌνκΈ° μ½λ€.   
   κ·Έλ¬λ μν μ²΄ν¬λ₯Ό μνν΄ μ€ checkedFetch ν¨μλ₯Ό μμ±ν΄ μμ±ν΄ λ³Έλ€λ©΄ μλμ κ°λ€.
   >fetchμ νμ μ μΈμ lib.dom.d.tsμ μμΌλ©° λ€μκ³Ό κ°λ€.
   ```ts
     declare function fetch(
      input: RequestInfo, init?: RequestInit
      ): Promise<Response>;
   ```
   >checkdFetchλ λ€μμ²λΌ μμ±ν  μ μλ€.
   ```ts  
      async function checkedFetch(input: RequestInfo, init?: RequestInit) {
        const response = await fetch(input, init);
        if (!response.ok) {
          // λΉλκΈ° ν¨μ λ΄μμλ κ±°μ λ νλ‘λ―Έμ€λ‘ λ°ννλ€.
          throw new Error('Request failed: ' + response.status);
        }
        return response;
      }
   ```
   > μ΄ μ½λλ μ λμνμ§λ§ λ€μκ³Ό κ°μ΄ λ κ°κ²°νκ² μμ±ν  μ μλ€.   
   ```ts
       const checkedFetch: typeof fetch = async (input, init) => {
          const response = await fetch(input, init);
          if (!response.ok) {
            throw new Error('Request failed: ' + response.status);
          }
          return response;
        }
   ```
   ν¨μ λ¬Έμ₯μ ν¨μ ννμμΌλ‘ λ°κΏ¨κ³  ν¨μ μ μ²΄μ νμ(typeof fetch)μ μ μ©νλ€.   
   μ΄λ νμμ€ν¬λ¦½νΈκ° inputκ³Ό initμ νμμ μΆλ‘ ν  μ μκ² ν΄μ€λ€.
      
  νμ κ΅¬λ¬Έμ λν checkedFetchμ λ°ν νμμ λ³΄μ₯νλ©°, fetchμ λμΌνλ€.   
  >μλ₯Ό λ€μ΄ throw λμ  returnμ μ¬μ©νλ€λ©΄, νμμ€ν¬λ¦½νΈλ κ·Έ μ€μλ₯Ό μ‘μλΈλ€.   
  ```ts
          const checkedFetch: typeof fetch = async (input, init) => {
            //  ~~~~~~~~~~~~   Type 'Promise<Response | HTTPError>' νμμ
            //                     'Promise<Response>' νμμ ν λΉν  μ μλ€.
            //                   Type 'Response | HTTPError' is not assignable
            //                       to type 'Response'
            const response = await fetch(input, init);
            if (!response.ok) {
              return new Error('Request failed: ' + response.status);
            }
            return response;
          }
  ```
      
      checkedFetchλ₯Ό ν¨μ λ¬Έμ₯μΌλ‘ μμ±ν μμ μμλ throwκ° μλλΌ returnμ μ¬μ©ν  κ²½μ° μ€λ₯κ° λ°μνλ€.   
      κ·Έλ¬λ μ€λ₯λ μ²« λ²μ§Έ μμ μ λ¬λ¦¬ checkdFetch κ΅¬νμ²΄κ° μλ, ν¨μλ₯Ό νΈμΆν μμΉμμ λ°μνλ€.   
         
   **ν¨μ λ§€κ°λ³μμ νμ μ μΈμ νλ κ²λ³΄λ€ ν¨μ ννμ μ μ²΄ νμμ μ μνλ κ²μ΄ μ½λλ κ°κ²°νκ³  μμ νλ€.**   
   λ€λ₯Έ ν¨μμ μκ·Έλμ²μ λμΌν νμμ κ°μ§λ μ ν¨μλ₯Ό μμ±νκ±°λ, λμΌν νμ μκ·Έλμ²λ₯Ό κ°μ§λ μ¬λ¬ κ°μ ν¨μλ₯Ό μμ±ν  λλ λ§€κ°λ³μμ νμκ³Ό λ°ν νμμ λ°λ³΅ν΄μ μμ±νμ§ λ§κ³  ν¨μμ μ²΄μ νμ μ μΈμ μ μ©ν΄μΌ νλ€.   
   
   ## μμ½
   - λ§€κ°λ³μλ λ°ν κ°μ νμμ λͺμνκΈ°λ³΄λ€λ ν¨μ ννμ μ μ²΄μ νμ κ΅¬λ¬Έμ μ μ©νλ κ²μ΄ μ’λ€.   
   - λ§μ½ κ°μ νμ μκ·Έλμ²λ₯Ό λ°λ³΅μ μΌλ‘ μμ±ν μ½λκ° μλ€λ©΄ ν¨μ νμμ λΆλ¦¬ν΄ λ΄κ±°λ μ΄λ―Έ μ‘΄μ¬νλ νμμ μ°Ύμλ³΄λλ‘ νλ€. λΌμ΄λΈλ¬λ¦¬λ₯Ό μ§μ  λ§λ λ€λ©΄ κ³΅ν΅ μ½λ°±μ νμμ μ κ³΅ν΄μΌ νλ€.   
   - λ€λ₯Έ ν¨μμ μκ·Έλμ²λ₯Ό μ°Έμ‘°νλ €λ©΄ typeof fnμ μ¬μ©νλ©΄ λλ€.

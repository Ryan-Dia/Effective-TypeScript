# ๐์ฝ์๋ด์ฉ ์ ๋ฆฌํด๋ณด๊ธฐ



## ๐ Item 10 ๊ฐ์ฒด ๋ํผ ํ์ ํผํ๊ธฐ


์๋ฐ์คํฌ๋ฆฝํธ์๋ ๊ฐ์ฒด ์ด์ธ์๋ ๊ธฐ๋ณธํ ๊ฐ๋ค์ ๋ํ ์๊ณฑ๊ฐ์ง ํ์์ด ์๋ค.
  - string
  - number
  - boolean
  - null
  - undefined
  - symbol
  - bigint
symbol ๊ธฐ๋ณธํ์ ES2015์์ ์ถ๊ฐ๋์๊ณ , bigint๋ ์ต์ข ํ์  ๋จ๊ณ์ ์๋ค.   
๊ธฐ๋ณธํ๋ค์ ๋ถ๋ณ์ด๋ฉฐ ๋ฉ์๋๋ฅผ ๊ฐ์ง์ง ์๋๋ค๋ ์ ์์ ๊ฐ์ฒด์ ๊ตฌ๋ถ๋ธ๋๋ค.   
๊ทธ๋ฐ๋ฐ ๊ธฐ๋ณธํ์ธ string์ ๊ฒฝ์ฐ ๋ฉ์๋๋ฅผ ๊ฐ์ง๊ณ  ์๋ ๊ฒ์ฒ๋ผ ๋ณด์ธ๋ค.   
```ts
primitive.charAt(3) // "m"
```
ํ์ง๋ง ์ฌ์ค charAt์ string์ ๋ฉ์๋๊ฐ ์๋๋ฉฐ, string์ ์ฌ์ฉํ  ๋ ์๋ฐ์คํฌ๋ฆฝํธ ๋ด๋ถ์ ์ผ๋ก ๋ง์ ๋์์ด ์ผ์ด๋๋ค.   
string '๊ธฐ๋ณธํ'์๋ ๋ฉ์๋๊ฐ ์์ง๋ง, ์๋ฐ์คํฌ๋ฆฝํธ์๋ ๋ฉ์๋๋ฅผ ๊ฐ์ง๋ String "๊ฐ์ฒด"ํ์์ด ์ ์๋์ด ์๋ค.    
์๋ฐ์คํฌ๋ฆฝํธ๋ ๊ธฐ๋ณธํ๊ณผ ๊ฐ์ฒด ํ์์ ์๋ก ์์ ๋กญ๊ฒ ๋ณํํ๋ค.   
string ๊ธฐ๋ณธํ์ charAt๊ฐ์ ๋ฉ์๋๋ฅผ ์ฌ์ฉํ  ๋, ์๋ฐ์คํฌ๋ฆฝํธ๋ ๊ธฐ๋ณธํ์ String ๊ธฐ๋ณธํ์ charAt ๊ฐ์ ๋ฉ์๋๋ฅผ ์ฌ์ฉํ  ๋,   
์๋ฐ์คํฌ๋ฆฝํธ๋ ๊ธฐ๋ณธํ์ String ๊ฐ์ฒด๋ก ๋ํํ๊ณ , ๋ฉ์๋๋ฅผ ํธ์ถํ๊ณ , ๋ง์ง๋ง์ ๋ํํ ๊ฐ์ฒด๋ฅผ ๋ฒ๋ฆฐ๋ค.   


```ts
// Don't do this!
const originalCharAt = String.prototype.charAt;
String.prototype.charAt = function(pos) {
  console.log(this, typeof this, pos);
  return originalCharAt.call(this, pos);
};
console.log('primitive'.charAt(3)); // [String: 'primitive'] 'object' 3
// m
```


  ๋ฉ์๋ ๋ด์ this๋ string ๊ธฐ๋ณธํ์ด ์๋ String ๊ฐ์ฒด ๋ํผ์ด๋ค.   
  String ๊ฐ์ฒด๋ฅผ ์ง์  ์์ฑํ  ์๋ ์์ผ๋ฉฐ, string ๊ธฐ๋ณธํ์ฒ๋ผ ๋์ํ๋ค.   
  ๊ทธ๋ฌ๋ string ๊ธฐ๋ณธํ๊ณผ String ๊ฐ์ฒด ๋ํผ๊ฐ ํญ์ ๋์ผํ๊ฒ ๋์ํ๋ ๊ฒ์ ์๋๋ค.   
  >String ๊ฐ์ฒด๋ ์ค์ง ์๊ธฐ ์์ ํ๊ณ ๋ง ๋์ผํ๋ค.
  ```ts
  > "hello" === new String("hello")
  // false
  > new String("hello") === new String("hello")
  // false
  ```
  ๊ฐ์ฒด ๋ํผ ํ์์ ์๋ ๋ณํ์ ์ข์ข ๋นํฉ์ค๋ฌ์ด ๋์์ ๋ณด์ผ ๋๊ฐ ์๋ค.
  > ์๋ฅผ ๋ค์ด ์ด๋ค ์์ฑ์ ๊ธฐ๋ณธํ์ ํ ๋นํ๋ค๋ฉด ๊ทธ ์์ฑ์ด ์ฌ๋ผ์ง๋ค.
  ```ts
  > x = "hello"
  > x.language= 'English'
  // 'English'
  > x.language
  // undefined
  ```
  ์ค์ ๋ก๋ x๊ฐ String ๊ฐ์ฒด๋ก ๋ณํํ ํ language ์์ฑ์ด ์ถ๊ฐ๋์๊ณ , language ์์ฑ์ด ์ถ๊ฐ๋ ๊ฐ์ฒด๋ ๋ฒ๋ ค์ง ๊ฒ์ด๋ค.    
     
     ๋ค๋ฅธ ๊ธฐ๋ณธํ์๋ ๋์ผํ๊ฒ ๊ฐ์ฒด ๋ํผ ํ์์ด ์กด์ฌํ๋ค.   
     number์๋ Number, booleand์๋ Boolean, symbol์๋ Symbol, bigint์๋ BigInt๊ฐ ์กด์ฌํ๋ค. (null๊ณผ undefined์๋ ๊ฐ์ฒด ๋ํผ๊ฐ ์๋ค)   
     ์ด ๋ํผ ํ์๋ค ๋๋ถ์ ๊ธฐ๋ณธํ ๊ฐ์ ๋ฉ์๋๋ฅผ ์ฌ์ฉํ  ์ ์๊ณ , ์ ์  ๋ฉ์๋(String.fromCharCode ๊ฐ์)๋ ์ฌ์ฉํ  ์ ์๋ค.   
     ๊ทธ๋ฌ๋ ๋ณดํต์ ๋ํผ ๊ฐ์ฒด๋ฅผ ์ง์  ์์ฑํ  ํ์๊ฐ ์๋ค.   
     ํ์์คํฌ๋ฆฝํธ๋ ๊ธฐ๋ณธํ์ค๊ฐ ๊ฐ์ฒด ๋ํผ ํ์์ ๋ณ๋๋ก ๋ชจ๋ธ๋งํ๋ค.   
     ๊ทธ๋ฐ๋ฐ string์ ์ฌ์ฉํ  ๋๋ ํนํ ์ ์ํด์ผ ํ๋ค.   
     >string์ String์ด๋ผ๊ณ  ์๋ชป ํ์ดํํ๊ธฐ ์ฝ๊ณ  ์ค์๋ฅผ ํ๋๋ผ๋ ์ฒ์์๋ ์ ๋์ํ๋ ๊ฒ์ฒ๋ผ ๋ณด์ด๊ธฐ ๋๋ฌธ์ด๋ค.   
     ```ts
      function getStringLen(foo: String) {
        return foo.length;
      }

      getStringLen("hello");  // OK
      getStringLen(new String("hello"));  // OK
     ```
     
     >๊ทธ๋ฌ๋ string์ ๋งค๊ฐ๋ณ์๋ก ๋ฐ๋ ๋ฉ์๋์ String๊ฐ์ฒด๋ฅผ ์ ๋ฌํ๋ ์๊ฐ ๋ฌธ์ ๊ฐ ๋ฐ์ํ๋ค.   
     ```ts
      function isGreeting(phrase: String) {
        return [
          'hello',
          'good day'
        ].includes(phrase);
                // ~~~~~~
                // Argument of type 'String' is not assignable to parameter
                // of type 'string'.
                // 'string' is a primitive, but 'String' is a wrapper object;
                // prefer using 'string' when possible
      }
     ```
     
     string์ String์ ํ ๋นํ   ์ ์์ง๋ง String์ string์ ํ ๋นํ  ์ ์๋ค.   
     ์ค๋ฅ ๋ฉ์์ง๋๋ก string ํ์์ ์ฌ์ฉํด์ผ ํ๋ค.   
     ๋๋ถ๋ถ์ ๋ผ์ด๋ธ๋ฌ๋ฆฌ์ ๋ง์ฐฌ๊ฐ์ง๋ก ํ์์คํฌ๋ฆฝํธ๊ฐ ์ ๊ณตํ๋ ํ์ ์ ์ธ์ ์ ๋ถ ๊ธฐ๋ณธํ ํ์์ผ๋ก ๋์ด์๋ค.   
     ๋ํผ ๊ฐ์ฒด๋ ํ์ ๊ตฌ๋ฌธ์ ์ฒซ ๊ธ์๋ฅผ ๋๋ฌธ์๋ก ํ๊ธฐํ๋ ๋ฐฉ๋ฒ์ผ๋ก๋ ์ฌ์ฉํ  ์ ์๋ค.   
     ```ts
      const s: String = "primitive";
      const n: Number = 12;
      const b: Boolean = true;
     ```
     
 ## ์์ฝ
  - ๊ธฐ๋ณธํ ๊ฐ์ ๋ฉ์๋๋ฅผ ์ ๊ณตํ๊ธฐ ์ํด ๊ฐ์ฒด ๋ํผ ํ์์ด ์ด๋ป๊ฒ ์ฐ์ด๋์ง ์ดํดํ๊ณ  ์ง์  ์ฌ์ฉํ๊ฑฐ๋ ์ธ์คํด์ค๋ฅผ ์์ฑํ๋ ๊ฒ์ ํผํด์ผ ํ๋ค.
  - ํ์์คํฌ๋ฆฝํธ ๊ฐ์ฒด ๋ํผ ํ์์ ์ง์ํ๊ณ , ๋์  ๊ธฐ๋ณธํ ํ์์ ์ฌ์ฉํด์ผ ํ๋ค.
    - String ๋์  string
    - Number ๋์  number
    - Boolean ๋์  boolean
    - Symbol ๋์  symbol,
    - BigInt ๋์  bigint    

  

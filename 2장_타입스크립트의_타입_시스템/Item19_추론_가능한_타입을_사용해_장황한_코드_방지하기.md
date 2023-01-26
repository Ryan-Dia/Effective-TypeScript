## 📕 오늘 읽은 내용, 이런 식으로 정리해 봅시다. ✒

**TIL(Today I learn) 기록일** : 2022.01.25

**오늘 읽은 범위** : 19. 추론 가능한 타입을 사용해 장황한 코드 방지하기

# Item 19. 추론 가능한 타입을 사용해 장황한 코드 방지하기


타입스크립트를 처음 접한 개발자가 자바스크립트 코드를 포팅할 때 가장 먼저 하는 일은 타입 구문을 넣는 것이다.   
타입스크립트가 결국 타입을 위한 언어이기 때문에, 변수를 선언할 때마다 타입을 명시해야 한다고 생각하기 때문이다.   
그러나 타입스크립트의 많은 타입 구문은 사실 불필요하다.   
다음과 같이 코드의 모든 변수에 타입을 선언하는 것은 비생상적이며 형편없는 스타일로 여겨진다.   
   
    
```ts
let x:number = 12;

// 다음처럼만 해도 충분하다.

let x = 12;
```
편집기에서 x에 마우스를 올려보면, 타입이 number로 이미 추론되어 있음을 확인할 수 있다.   
   
타입 추론이 된다면 명시적 타입 구문은 필요하지 않다.   
오히려 방해가 될뿐이다.   
만약 타입을 확신하지 못한다면 편집기를 통해 체크하면 된다.   

<br>
   
> 타입스크립트는 더 복잡한 객체도 추론할 수 있다. 
> 다음 예제를 보자
```ts
const person: {
  name: string;
  born: {
    where: string;
    when: string;
  };
  died: {
    where: string;
    when: string;
  }
} = {
  name: 'Sojourner Truth',
  born: {
    where: 'Swartekill, NY',
    when: 'c.1797',
  },
  died: {
    where: 'Battle Creek, MI',
    when: 'Nov. 26, 1883'
  }
};
```
<br>

>타입을 생략하고 다음처럼 작성해도 된다.
```ts

const person = {
  name: 'Sojourner Truth',
  born: {
    where: 'Swartekill, NY',
    when: 'c.1797',
  },
  died: {
    where: 'Battle Creek, MI',
    when: 'Nov. 26, 1883'
  }
};
```
두 예제에서 `person`의 타입은 동일합니다.   
값에 추가로 타입을 작성하는 것은 거추장스러울 뿐이다.   

<br>

다음 예제처럼 배열의 경우도 객체와 마찬가지이다.   
타입스크립트는 입력 받아 연산을 하는 함수가 어떤 타입을 반환하는지 정확히 알고 있다.   
```ts
function square(nums: number[]) {
  return nums.map(x => x * x);
}
const squares = square([1, 2, 3, 4]); // Type is number[]
```
타입스크립트는 여러분이 예상한 것보다 더 정확하게 추론하기도 한다.   
>예를 들어 다음 코드를 보자
```ts
const axis1: string = 'x';  // Type is string
const axis2 = 'y';  // Type is "y"
```
axis2 변수를 string으로 예상하기 쉽지만 타입스크립트가 추론한`y`가 더 정확한 타입이다.   
이러한 추론이 어떻게 타입 오류를 방지하는지 아이템21에서 다룬다.   

<br>

타입이 추론되면 리팩터링 역시 용이해진다.   
Product 타입과 기록을 위한 함수를 가정해 보자.
```ts
interface Product {
  id: number;
  name: string;
  price: number;
}

function logProduct(product: Product) {
  const id: number = product.id;
  const name: string = product.name;
  const price: number = product.price;
  console.log(id, name, price);
}
```
그런데 id에 문자도 들어 있을 수 있음을 나중에 알게 되었다고 가정해 보자.
그래서 Product 내의 id의 타입을 변경한다.
그러면 logProduct 내의 id 변수 선언에 있는 타입과 맞이 않기 때문에 오류가 발생한다.
```ts
interface Product {
  id: string;
  name: string;
  price: number;
}

function logProduct(product: Product) {
  const id: number = product.id;
     // ~~ Type 'string' is not assignable to type 'number'
  const name: string = product.name;
  const price: number = product.price;
  console.log(id, name, price);
}
```
logProduct 함수 내의 명시적 타입 고문이 없다면, 코드는 아무런 수정 없이도 타입 체커를 통과했을 것이다.   
logProduct는 비구조화 할당문을 사용해 구현하는 게 낫다.   
```ts
interface Product {
  id: string;
  name: string;
  price: number;
}
function logProduct(product: Product) {
  const {id, name, price} = product;
  console.log(id, name, price);
}
```

<br>

비구조화 할당문은 모두 지역 변수의 타입이 추론되도록 한다.   
여기에 추가로 며시적 타입 구문을 넣는다면 불필요한 타입 선언으로 인해 코드가 번잡해진다.   
```ts
function logProduct(product: Product) {
  const {id, name, price}: {id: string; name: string; price: number } = product;
  console.log(id, name, price);
}
```
정보가 부족해서 타입스크립트가 스스로 타입을 판단하기 어려운 상황도 일부있다.   
그럴 때는 명시적 타입 구문이 필요하다.   
logProduct 함수에서 매개변수 타입을 product로 명시한 경우가 그 예이다.   

<br>

어떤 언어들은 매개변수의 최종 사용처까지 참고하여 타입을 추론하지만, 타입스크립트는 최종 사용처까지 고려하지 않는다.   
타입스크립트에서 변수의 타입은 일반적으로 처음 등장할 때 결정된다.   
       
이상적인 타입스크립트 코드는 함수/메서드 시그니처에 타입 구문을 포함하지만, 함수 내에서 생성된 지역 변수에는 타입 구문을 넣지 않는다.   
타입 구문을 생략하여 방해되는 것들을 최소화하고 코드를 읽는 사람이 구현 로직에 집중할 수 있게 하는 것이 좋다.   
   
 함수 매개변수에 타입 구문을 생략하는 경우도 간혹 존재한다.   
 >기본값이 있는 경우를 예로 들어보자.
 ```ts
 function parseNumber(str: string, base=10) {
  // ...
}
야기서 기본값이 10이기 때문에 base의 타입은 number로 추론된다.   
보통 타입 정보가 있는 라이브러리에서, 콜백 함수의 매개변수 타입은 자동으로 추론된다.   
>다음 예제에서 express HTTP 서버 라이브러리를 사용하는 request와 response의 타입 선언을 필요하지 않다.
```ts
// HIDE
namespace express {
  export interface Request {}
  export interface Response {
    send(text: string): void;
  }
}
interface App {
  get(path: string, cb: (request: express.Request, response: express.Response) => void): void;
}
const app: App = null!;
// END

// 이렇게 하지 말자
app.get('/health', (request: express.Request, response: express.Response) => {
  response.send('OK');
});

// 이렇게 하자
app.get('/health', (request, response) => {
  response.send('OK');
});
```
문맥이 타입 추론을 위해 어떻게 쓰이는지 아이템26에서 더 자세히 다룬다.   
타입이 추론될 수 있음에도 여전히 타입을 명시하고 싶은 몇 가지 상황이 있다.   
>그중 하나는 객체 리터럴을 정의할 때이다.
```ts
interface Product {
  id: string;
  name: string;
  price: number;
}

const elmo: Product = {
  name: 'Tickle Me Elmo',
  id: '048188 627152',
  price: 28.99,
};
```
이런 정의에 타입을 명시하면, 잉여 속성 체크가 동작한다.   
잉여속성 체크는 특히 선택적 속성이 있는 타입의 오타 같은 오류를 잡는 데 효과적이다.   
그리고 변수가 사용되는 순간이 아닌 할당하는 시점에 오류가 표시되도록 해준다.   
   
만약 타입 구문을 제거한다면 잉여 속성 체크가 동작하지 않고, 객체를 선언 한 곳이 아니라 객체가 사용되는 곳에서 타입 오류가 발생한다.   
```ts
const furby = {
  name: 'Furby',
  id: 630509430963,
  price: 35,
};
logProduct(furby);
        // ~~~~~ Argument .. is not assignable to parameter of type 'Product'
        //         Types of property 'id' are incompatible
        //         Type 'number' is not assignable to type 'string'
```

<br>

>그러나 타입 구문을 제대로 명시한다면, 실제로 실수가 발생한 부분에 오류를 표시해 준다.
```ts
interface Product {
  id: string;
  name: string;
  price: number;
}

function logProduct(product: Product) {
  const id: number = product.id;
     // ~~ Type 'string' is not assignable to type 'number'
  const name: string = product.name;
  const price: number = product.price;
  console.log(id, name, price);
}
 const furby: Product = {
   name: 'Furby',
   id: 630509430963,
// ~~ 'number' 형식은 'string' 형식에 할당할 수 없습니다.
   price: 35,
 };
 logProduct(furby);
```
마찬가지로 함수의 반환에도 타입을 명시하여 오류를 방지할 수 있다.   
타입 추론이 가능할지라도 구현상의 오류가 함수를 호출한 곳까지 영향을 미치지 않도록 하기 위해 타입 구문을 명시하는 게 좋다.   
>주식 시세를 조회하는 함수를 작성했다고 가정해 보자.
```ts
function getQuote(ticker: string) {
  return fetch(`https://quotes.example.com/?q=${ticker}`)
      .then(response => response.json());
}
```

> 이미 조회한 종목을 다시 요청하지 않도록 캐시를 추가한다.
```ts
const cache: {[ticker: string]: number} = {};
function getQuote(ticker: string) {
  if (ticker in cache) {
    return cache[ticker];
  }
  return fetch(`https://quotes.example.com/?q=${ticker}`)
      .then(response => response.json())
      .then(quote => {
        cache[ticker] = quote;
        return quote;
      });
}
```
그런데 이 코드에는 오류가 있다.   
getQuote는 항상 Promise를 반환하므로 if 구문에는 cache[ticker]가 아니라 Promise.resolve(cache[ticker])가 반환되로록 해야 한다.   
실행해 보면 오류는 getQuote 내부가 아닌 getQuote를 호출한 코드에서 발생한다.
```ts
getQuote('MSFT').then(considerBuying);
          // ~~~ 'number | Promise<any>' 형식에 'then' 속성이 없습니다.
          //     'number' 형식에 'then' 속성이 없습니다.
```
>이때 의도된 반환 타입(Promise<number>)을 명시한다면, 정확한 위치에 오류가 표시된다.
```ts
  const cache: {[ticker: string]: number} = {};
  function getQuote(ticker: string):Promise<number> {
    if(ticker in cache) {
      return cache[ticker];
        // ~~~~~~ 'number' 형식은 'Promise<number>'형식에 할당할 수 없습니다.
    }
    // ...
  }
```
반환 타입을 명시하면, 구현상의 오류가 사용자 코드의 오류로 표시되지 않습니다.   
오류의 위치를 제대로 표시해 주는 이점외에도, 반환 타입을 명시해야 하는 이유가 두 가지 더 있다.   

  <br>
  
첫 번째는 반환 타입을 명시하면 함수에 대해 더욱 명확하게 알 수 있기 때문이다.   
반환 타입을 명시하려면 구현하기 전에 입력 타입과 출력 타입이 무엇인지 알아야 한다.   
추후에 코드가 조금 변경되어도 그 함수의 시그니처는 쉽게 바뀌지 않는다.   
미리 타입을 명시하는 방법은, 함수를 구현하기 전에 테스트를 먼저 작성하는 테스트 주도 개발과 비슷하다.   
전체 타입 시그니처를 먼저 작성하면 구현에 맞추어 주먹구구식으로 시그니처가 작성하는 것을 방지하고 제대로 원하는 모양을 얻게 된다.   
   
        
반환값의 타입을 명시해야 하는 두 번째 이유는 명명된 타입을 사용하기 위해서이다.   
예를 들어, 다음 함수에서는 반환 타입을 명시하지 않기로 했다.   
```ts
interface Vector2D { x: number; y: number; }
function add(a: Vector2D, b: Vector2D) {
  return { x: a.x + b.x, y: a.y + b.y };
}
```
타입스크립트는 반환 타입을 { x:number; y: number; }로 추론했다.   
이런 경우 Vector2D와 호환되지만, 입력이 Vector2D인데 반해 출력은 Vector2D가 아니기 때문에 사용자 입장에서 당황스러울 수 있다.
  
<br>
  
반환 타입을 명시하면 더욱 직관적인 표현이 된다.
  

## 요약

- 타입스크립트가 타입을 추론할 수 있다면 타입 구문을 작성하지 않는 게 좋다.
- 이상적인 경우 함수/메서드의 시그니처에는 타입 구문이 있지만, 함수 내의 지역 변수에는 타입 구문이 없다.
- 추론될 수 있는 경우라도 객체 리터럴과 함수 반환에는 타입 명시를 고려해야 한다. 이는 내부 구현의 오류가 사용자 코드 위치에 나타나는 것을 방지해 준다.

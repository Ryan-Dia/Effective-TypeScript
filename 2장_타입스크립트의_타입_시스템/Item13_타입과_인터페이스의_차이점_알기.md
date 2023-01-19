## 📕 오늘 읽은 내용, 이런 식으로 정리해 봅시다. ✒

**TIL(Today I learn) 기록일** : 2022.12.18

**오늘 읽은 범위** : 13 타입과 인터페이스의 차이점 알기


# Item 13. 타입과 인터페이스의 차이점 알기

타입스크립트에서 명명된 타입(named type)을 정의하는 방법은 두 가지가 있다.   
>다음처럼 타입을 사용할 수 있다.
```ts
type TState = {
  name: string;
  capital: string;
}
```
>또는 인터페이스를 사용해도 된다.
```ts
  nmae: string;
  capital: string;
```

대부분의 경우에는 타입을 사용해도 되고 인터페이스를 사용해도 된다.   
그러나 타입과 인터페이스 사이에 존재하는 차이를 분명하게 알고, 같은 상황에서는 동일한 방법으로 명명된 타입을 정의해 일관성을 유지해야 한다.   
그러려면 하나의 타입에 대해 두 가지 방법을 모두 사용해서 정의할 줄 알아야 한다.   
   
먼저, 인터페이스 선언과 타입 선언의 비슷한 점에 대해 알아보자.   
명명된 타입은 인터페이스로 정의하든 타입으로 정의하든 상태에는 차이가 없다.   
>만약 IState와 TState를 추가 속성과 함께 할당한다면 동일한 오류가 발생한다.   
```ts


type TState = {
  name: string;
  capital: string;
}
interface IState {
  name: string;
  capital: string;
}
const wyoming: TState = {
  name: 'Wyoming',
  capital: 'Cheyenne',
  population: 500_000
// ~~~~~~~~~~~~~~~~~~ ... 형식은 'TState' 형식에 할당할 수 없다.
//                    개체 리터럴은 알려진 속성만 지정할 수 있으며
//                    'TState' 형식에 'population'이(가) 없습니다.
};
```

>인덱스 시그니처는 인터페이스와 타입에서 모두 사용할 수 있다.
```ts
type TDict = { [key : string]: string};
interface IDict {
  [key: string]: string
}
```
> 또한 함수 타입도 인터페이스나 타입으로 정의할 수 있다.    
```ts
type TState = {
  name: string;
  capital: string;
}
interface IState {
  name: string;
  capital: string;
}
type TFn = (x: number) => string;
interface IFn {
  (x: number): string;
}

const toStrT: TFn = x => '' + x;  // OK
const toStrI: IFn = x => '' + x;  // OK
```
>이런 단순한 함수 타입에는 타입 별칭(alias)이 더 나은 선택이겠지만, 함수 타입에 추가적인 속성이 있다면 타입이나 인터페이스 어떤 것을 선택하든 차이가 없다.   
```ts
type TState = {
  name: string;
  capital: string;
}
interface IState {
  name: string;
  capital: string;
}
type TFnWithProperties = {
  (x: number): number;
  prop: string;
}
interface IFnWithProperties {
  (x: number): number;
  prop: string;
}
```
문법이 생소할 수도 있지만 자바스크립트에서 함수는 호출 가능한 객체라는 것을 떠올려 보면 납득할 수 있는 코드이다.   
> 타입 별칭과 인터페이스는 모두 제너릭이 가능하다.
```ts
type TState = {
  name: string;
  capital: string;
}
interface IState {
  name: string;
  capital: string;
}
type TPair<T> = {
  first: T;
  second: T;
}
interface IPair<T> {
  first: T;
  second: T;
}
```
>인터페이스는 타입을 확장할 수 있으며 타입은 인터페이스를 확장할 수 있다.   
```ts
type TState = {
  name: string;
  capital: string;
}
interface IState {
  name: string;
  capital: string;
}
interface IStateWithPop extends TState {
  population: number;
}
type TStateWithPop = IState & { population: number; };
```
IStateWithPop과 TStateWithPop은 동일하다.   
여기서 주의할 점은 인터페이스는 유니온 타입 같은 복잡한 타입을 확장하지는 못한다는 것이다.  
복잡한 타입을 확장하고 싶다면 타입과 &를 사용해야 한다.   
   
>한편 클래스를 구현(implements)할 때는, 타입(TState)과 인터페이스(IState)둘 다 사용할 수 있다.   
```ts
type TState = {
  name: string;
  capital: string;
}
interface IState {
  name: string;
  capital: string;
}
class StateT implements TState {
  name: string = '';
  capital: string = '';
}
class StateI implements IState {
  name: string = '';
  capital: string = '';
}
```
지금까지 타입과 인터페이스의 비슷한 점들을 살펴보았다.   
이제부터는 타입과 인터페이스의 다른점을 알아보자.

---

## Type 과 Interface의 차이점   

유니온 타입은 있지만 유니온 인터페이스라는 개념은 없다.   
```ts
type AorB = 'a' | 'b';
```

인터페이스는 타입을 확장할 수 있지만, 유니온은 할 수 없다.   
그런데 유니온 타입을 확장하는 게 필요할 때가 있다.   
>Input과 Output은 별도의 타입이며 이 둘을 하나의 변수명으로 매핑하는 VariableMap 인터페이스를 만들 수 있다.   
```ts
type Input = { /* ... */ };
type Output = { /* ... */ };
interface VariableMap {
  [name: string]: Input | Output;
}
```
>또는 유니온 타입에 name 속성을 붙인 타입을 만들 수도 있다.   
>다음과 같다.
```ts
type NamedVariable = (Input | Output) & { name: string };
```
이 타입은 인터페이스로 표현할 수 없다.   
type 키워드는 일반적으로 interface보다 쓰임새가 많다.   
type 키워드는 유니온이 될 수도 있고, 매핑된 타입 또는 조건부 타입 같은 고급 기능에 활용되기도 한다.   
>튜플과 배열 타입도 type 키워드를 이용해 더 간결하게 표현할 수 있다.
```ts
type Pair = [number, number]
type StringList = string[];
type NamedNums = [string, ...number[]];
```
>인터페이스로도 튜플과 비슷하게 구현할 수 있기는 하다.   
```ts
interface Tuple{
  0: number;
  1: number;
  length: 2
}
const t: Tuple = [10, 20]; // 정상
```

그러나 인터페이스로 튜플과 비슷하게 구현하면 튜플에서 사용할 수 있는 concat 같은 메서드들을 사용할 수 없습니다.   
그러므로 튜플은 type 키워드로 구현하는 것이 낫다.   
   
반면 인터페이스는 타입에 없는 몇 가지 기능이 있다. 
그중 하나는 바로 `보강(augment)`이 가능하다는 것이다.   
이번 아이템 처음에 등장했던 State예제에 population 필드를 추가할 때 보강 기법을 사용할 수 있다.   
```ts
interface IState {
  name: string;
  capital: string;
}
interface IState {
  population: number;
}
const wyoming: IState = {
  name: 'Wyoming',
  capital: 'Cheyenne',
  population: 500_000
};  // OK
```
이 예제처럼 속성을 확장하는 것을 `선언 병합(declaration merging)`이라고 한다.   
`선언 병합`을 본 적이 없다면 매우 생소하게 느껴질 것이다.   
`선언 병합`은 주로 타입 선언 파일에서 사용된다. 따라서 타입 선언 파일을 작성할 때는 선언 병합을 지원하기 위해 반드시 인터페이스를 사용해야 하며 표준을 따라야한다.   
타입 선언에는 사용자가 채워야 하는 빈틈이 있을 수 있는데, 바로 이 `선언 병합`이 그렇다.   
   
타입스크립트 여러 버전의 자바스크립트 표준 라이브러리에서 여러 타입을 모아 병합한다.


  

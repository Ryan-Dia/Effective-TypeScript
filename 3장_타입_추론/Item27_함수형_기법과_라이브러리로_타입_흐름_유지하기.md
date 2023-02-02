## 📕 오늘 읽은 내용, 이런 식으로 정리해 봅시다. ✒

**TIL(Today I learn) 기록일** : 2022.02.02

**오늘 읽은 범위** : 27. 함수형 기법과 라이브러리로 타입 흐름 유지기

# Item 27. 함수형 기법과 라이브러리로 타입 흐름 유지기

파이썬, C, 자바 등에서 볼 수 있는 표준 라이브러리가 자바스크립트에는 포함 되어 있지 않다.   
수년간 많은 라이브러리들은 표준 라이브러리의 역할을 대신하기 위해 노력해봤다.   
람다 같은 최근의 라이브러리는 함수형 프로그래밍의 개념을 자바스크립트 세계에 도입하고 있다.   
   
라이브러리들의 일부 기능(map, flatMap, filter, reduce 등)은 순수 자바스크립트로 구현되어 있습니다.   
이러한 기법은 루프를 대체할 수 있기 때문에 자바스크립트에서 유용하게 사용되는데, 타입스크립트와 조합하여 사용하면 더욱 빛을 발한다.   
그 이유는 타입 정보가 그대로 유지되면서 타입 흐름이 계속 전달되도록 하기 때문이다.   
반면에 직접 루프를 구현하면 타입 체크에 대한 관리도 직접 해야 한다.   
예를 들어, 어떤 CSV 데이터를 파싱한다고 생각해보면 순수 자바스크립트에서는 절차형 프로그래밍 형태로 구현할 수 있다.   
```ts
const csvData = "...";
const rawRows = csvData.split('\n');
const headers = rawRows[0].split(',');

const rows = rawRows.slice(1).map(rowStr => {
  const row = {};
  rowStr.split(',').forEach((val, j) => {
    row[headers[j]] = val;
  });
  return row;
});
```
>함수형 마인드를 조금이라도 가진 자바스크립트 개발자라면 reduce를 사용해서 행 객체를 만드는 방법을 선호할 수도 있다.   
```ts
const rows = rawRows.slice(1)
   .map(rowStr => rowStr.split(',').reduce(
    (row, val, i) => (row[headers[i]] = val, row),
    {}));
```
>키와 값 배열로 취합해서 객체로 만들어 주는, 로대시의 zipObject 함수를 이용하면 코드를 더욱 짧게 만들 수 있다.   
```ts
import _ from 'lodash';
const rows = rawRows.slice(1)
  .map(rowStr => _.zipObject(headers, rowStr,split(',')));
```
코드가 매우 짧아졌다.   
그런데 자바스크립트에서는 프로젝트에 서드파이 라이브러리 종속성을 추가할 때 신중해야 한다.   
만약 서드파티 라이브러리 기반으로 코드를 짧게 줄이는 데 시간이 많이 든다면, 서드파티 라이브러리를 사용하지 않는 게 낫기 때문이다.   
   
그러나 같은 코드를 타입스크립트로 작성하면 서드파티 라이브러리를 사용하는 것이 무조건 유리하다.   
타입 정보를 참고하며 작업할 수 있기 때문에 서드파티 라이브러리 기반으로 바꾸는 데 시간이 훨씬 단축된다.    
      
>CSV 파서의 절차형 버전과 함수형 버전 모두 같은 오류를 발생시킨다.
```ts
// requires node modules: @types/lodash

const csvData = "...";
const rawRows = csvData.split('\n');
const headers = rawRows[0].split(',');
import _ from 'lodash';
const rowsA = rawRows.slice(1).map(rowStr => {
  const row = {};
  rowStr.split(',').forEach((val, j) => {
    row[headers[j]] = val;
 // ~~~~~~~~~~~~~~~ No index signature with a parameter of
 //                 type 'string' was found on type '{}'
  });
  return row;
});
const rowsB = rawRows.slice(1)
  .map(rowStr => rowStr.split(',').reduce(
      (row, val, i) => (row[headers[i]] = val, row),
                     // ~~~~~~~~~~~~~~~ No index signature with a parameter of
                     //                 type 'string' was found on type '{}'
      {}));
```
두 번전 모두 {}의 타입으로 {[column: string]: string} 또는 Record<string, string>을 제공하면 오류가 해결된다.   
    
반면 로대시 버전은 별도의 수정 없이도 타입 체커를 통과한다.   
```ts
// requires node modules: @types/lodash

const csvData = "...";
const rawRows = csvData.split('\n');
const headers = rawRows[0].split(',');
import _ from 'lodash';
const rows = rawRows.slice(1)
    .map(rowStr => _.zipObject(headers, rowStr.split(',')));
    // Type is _.Dictionary<string>[]
```
Dictrionary는 로대시의 타입 별칭이다.   
Dictionary<string>은 {[key:string]: string} 또는 Record<string, string>과 동일하다.   
여기서 중요한점은 타입 구문이 없어도 rows의 타입이 정확하다는 것이다.   
   
데이터의 가공이 정교해질수록 이러한 장점은 더욱 분명해진다.   
>예를 들어, 모든 NBA팀의 선수 명단을 가지고 있다고 가정해보자.
```ts

// requires node modules: @types/lodash

const csvData = "...";
const rawRows = csvData.split('\n');
const headers = rawRows[0].split(',');
import _ from 'lodash';
interface BasketballPlayer {
  name: string;
  team: string;
  salary: number;
}
declare const rosters: {[team: string]: BasketballPlayer[]};  
```
루프를 사용해 단순 목록을 만들려면 배열 concat을 사용해야 한다.   
다음 코드는 동작이 되지만 타입 체크는 되지 않는다.
```ts
  declare const rosters: {[team: string]: BasketballPlayer[]};
let allPlayers = [];
 // ~~~~~~~~~~ Variable 'allPlayers' implicitly has type 'any[]'
 //            in some locations where its type cannot be determined
for (const players of Object.values(rosters)) {
  allPlayers = allPlayers.concat(players);
            // ~~~~~~~~~~ Variable 'allPlayers' implicitly has an 'any[]' type
}
```
이 오류를 고치려면 apllPlayers에 타입 구문을 추가해야 한다.
```ts
  let allPlayers: BasketballPlayer[] = [];
  for (const players of Object.values(rosters)) {
    allPlayers = allPlayers.concat(players); // 정상
  ```
그러나 더 나은 해법은 Array.prototype.flat을 사용하는 것이다.
```ts
  const allPlayers = Object.values(rosters).flat();
// OK, type is BasketballPlayer[]
  ```
flat 메서드는 다차원 배열을 평탄화해준다.   
  타입 시그니처는 T[][] -> T[] 같은 형태이다.   
  이 버전이 가장 간결하고 타입 구문도 필요 없다.   
  또한 allPlayers 변수가 향후에 변경되지 않도록 let대신 const를 사용할 수 있다.   
  
  
## 요약
  
타임 흐름을 개선하고, 가독성을 높이고, 명시적인 타입 구문의 필요성을 줄이기 위해 직접 구현하기보다는 내장된 함수형 기법과 로대시 같은 유틸리티 라이브러리를 사용하는 것이 좋다.

## π μ€λ μ½μ λ΄μ©, μ΄λ° μμΌλ‘ μ λ¦¬ν΄ λ΄μλ€. β

**TIL(Today I learn) κΈ°λ‘μΌ** : 2022.02.02

**μ€λ μ½μ λ²μ** : 27. ν¨μν κΈ°λ²κ³Ό λΌμ΄λΈλ¬λ¦¬λ‘ νμ νλ¦ μ μ§κΈ°

# Item 27. ν¨μν κΈ°λ²κ³Ό λΌμ΄λΈλ¬λ¦¬λ‘ νμ νλ¦ μ μ§κΈ°

νμ΄μ¬, C, μλ° λ±μμ λ³Ό μ μλ νμ€ λΌμ΄λΈλ¬λ¦¬κ° μλ°μ€ν¬λ¦½νΈμλ ν¬ν¨ λμ΄ μμ§ μλ€.   
μλκ° λ§μ λΌμ΄λΈλ¬λ¦¬λ€μ νμ€ λΌμ΄λΈλ¬λ¦¬μ μ­ν μ λμ νκΈ° μν΄ λΈλ ₯ν΄λ΄€λ€.   
λλ€ κ°μ μ΅κ·Όμ λΌμ΄λΈλ¬λ¦¬λ ν¨μν νλ‘κ·Έλλ°μ κ°λμ μλ°μ€ν¬λ¦½νΈ μΈκ³μ λμνκ³  μλ€.   
   
λΌμ΄λΈλ¬λ¦¬λ€μ μΌλΆ κΈ°λ₯(map, flatMap, filter, reduce λ±)μ μμ μλ°μ€ν¬λ¦½νΈλ‘ κ΅¬νλμ΄ μμ΅λλ€.   
μ΄λ¬ν κΈ°λ²μ λ£¨νλ₯Ό λμ²΄ν  μ μκΈ° λλ¬Έμ μλ°μ€ν¬λ¦½νΈμμ μ μ©νκ² μ¬μ©λλλ°, νμμ€ν¬λ¦½νΈμ μ‘°ν©νμ¬ μ¬μ©νλ©΄ λμ± λΉμ λ°νλ€.   
κ·Έ μ΄μ λ νμ μ λ³΄κ° κ·Έλλ‘ μ μ§λλ©΄μ νμ νλ¦μ΄ κ³μ μ λ¬λλλ‘ νκΈ° λλ¬Έμ΄λ€.   
λ°λ©΄μ μ§μ  λ£¨νλ₯Ό κ΅¬ννλ©΄ νμ μ²΄ν¬μ λν κ΄λ¦¬λ μ§μ  ν΄μΌ νλ€.   
μλ₯Ό λ€μ΄, μ΄λ€ CSV λ°μ΄ν°λ₯Ό νμ±νλ€κ³  μκ°ν΄λ³΄λ©΄ μμ μλ°μ€ν¬λ¦½νΈμμλ μ μ°¨ν νλ‘κ·Έλλ° ννλ‘ κ΅¬νν  μ μλ€.   
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
>ν¨μν λ§μΈλλ₯Ό μ‘°κΈμ΄λΌλ κ°μ§ μλ°μ€ν¬λ¦½νΈ κ°λ°μλΌλ©΄ reduceλ₯Ό μ¬μ©ν΄μ ν κ°μ²΄λ₯Ό λ§λλ λ°©λ²μ μ νΈν  μλ μλ€.   
```ts
const rows = rawRows.slice(1)
   .map(rowStr => rowStr.split(',').reduce(
    (row, val, i) => (row[headers[i]] = val, row),
    {}));
```
>ν€μ κ° λ°°μ΄λ‘ μ·¨ν©ν΄μ κ°μ²΄λ‘ λ§λ€μ΄ μ£Όλ, λ‘λμμ zipObject ν¨μλ₯Ό μ΄μ©νλ©΄ μ½λλ₯Ό λμ± μ§§κ² λ§λ€ μ μλ€.   
```ts
import _ from 'lodash';
const rows = rawRows.slice(1)
  .map(rowStr => _.zipObject(headers, rowStr,split(',')));
```
μ½λκ° λ§€μ° μ§§μμ‘λ€.   
κ·Έλ°λ° μλ°μ€ν¬λ¦½νΈμμλ νλ‘μ νΈμ μλνμ΄ λΌμ΄λΈλ¬λ¦¬ μ’μμ±μ μΆκ°ν  λ μ μ€ν΄μΌ νλ€.   
λ§μ½ μλνν° λΌμ΄λΈλ¬λ¦¬ κΈ°λ°μΌλ‘ μ½λλ₯Ό μ§§κ² μ€μ΄λ λ° μκ°μ΄ λ§μ΄ λ λ€λ©΄, μλνν° λΌμ΄λΈλ¬λ¦¬λ₯Ό μ¬μ©νμ§ μλ κ² λ«κΈ° λλ¬Έμ΄λ€.   
   
κ·Έλ¬λ κ°μ μ½λλ₯Ό νμμ€ν¬λ¦½νΈλ‘ μμ±νλ©΄ μλνν° λΌμ΄λΈλ¬λ¦¬λ₯Ό μ¬μ©νλ κ²μ΄ λ¬΄μ‘°κ±΄ μ λ¦¬νλ€.   
νμ μ λ³΄λ₯Ό μ°Έκ³ νλ©° μμν  μ μκΈ° λλ¬Έμ μλνν° λΌμ΄λΈλ¬λ¦¬ κΈ°λ°μΌλ‘ λ°κΎΈλ λ° μκ°μ΄ ν¨μ¬ λ¨μΆλλ€.    
      
>CSV νμμ μ μ°¨ν λ²μ κ³Ό ν¨μν λ²μ  λͺ¨λ κ°μ μ€λ₯λ₯Ό λ°μμν¨λ€.
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
λ λ²μ  λͺ¨λ {}μ νμμΌλ‘ {[column: string]: string} λλ Record<string, string>μ μ κ³΅νλ©΄ μ€λ₯κ° ν΄κ²°λλ€.   
    
λ°λ©΄ λ‘λμ λ²μ μ λ³λμ μμ  μμ΄λ νμ μ²΄μ»€λ₯Ό ν΅κ³Όνλ€.   
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
Dictrionaryλ λ‘λμμ νμ λ³μΉ­μ΄λ€.   
Dictionary<string>μ {[key:string]: string} λλ Record<string, string>κ³Ό λμΌνλ€.   
μ¬κΈ°μ μ€μνμ μ νμ κ΅¬λ¬Έμ΄ μμ΄λ rowsμ νμμ΄ μ ννλ€λ κ²μ΄λ€.   
   
λ°μ΄ν°μ κ°κ³΅μ΄ μ κ΅ν΄μ§μλ‘ μ΄λ¬ν μ₯μ μ λμ± λΆλͺν΄μ§λ€.   
>μλ₯Ό λ€μ΄, λͺ¨λ  NBAνμ μ μ λͺλ¨μ κ°μ§κ³  μλ€κ³  κ°μ ν΄λ³΄μ.
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
λ£¨νλ₯Ό μ¬μ©ν΄ λ¨μ λͺ©λ‘μ λ§λ€λ €λ©΄ λ°°μ΄ concatμ μ¬μ©ν΄μΌ νλ€.   
λ€μ μ½λλ λμμ΄ λμ§λ§ νμ μ²΄ν¬λ λμ§ μλλ€.
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
μ΄ μ€λ₯λ₯Ό κ³ μΉλ €λ©΄ apllPlayersμ νμ κ΅¬λ¬Έμ μΆκ°ν΄μΌ νλ€.
```ts
  let allPlayers: BasketballPlayer[] = [];
  for (const players of Object.values(rosters)) {
    allPlayers = allPlayers.concat(players); // μ μ
  ```
κ·Έλ¬λ λ λμ ν΄λ²μ Array.prototype.flatμ μ¬μ©νλ κ²μ΄λ€.
```ts
  const allPlayers = Object.values(rosters).flat();
// OK, type is BasketballPlayer[]
  ```
flat λ©μλλ λ€μ°¨μ λ°°μ΄μ νννν΄μ€λ€.   
  νμ μκ·Έλμ²λ T[][] -> T[] κ°μ ννμ΄λ€.   
  μ΄ λ²μ μ΄ κ°μ₯ κ°κ²°νκ³  νμ κ΅¬λ¬Έλ νμ μλ€.   
  λν allPlayers λ³μκ° ν₯νμ λ³κ²½λμ§ μλλ‘ letλμ  constλ₯Ό μ¬μ©ν  μ μλ€.   
  
  
## μμ½
  
νμ νλ¦μ κ°μ νκ³ , κ°λμ±μ λμ΄κ³ , λͺμμ μΈ νμ κ΅¬λ¬Έμ νμμ±μ μ€μ΄κΈ° μν΄ μ§μ  κ΅¬ννκΈ°λ³΄λ€λ λ΄μ₯λ ν¨μν κΈ°λ²κ³Ό λ‘λμ κ°μ μ νΈλ¦¬ν° λΌμ΄λΈλ¬λ¦¬λ₯Ό μ¬μ©νλ κ²μ΄ μ’λ€.

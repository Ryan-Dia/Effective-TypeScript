## π μ€λ μ½μ λ΄μ©, μ΄λ° μμΌλ‘ μ λ¦¬ν΄ λ΄μλ€. β

**TIL(Today I learn) κΈ°λ‘μΌ** : 2022.02.08

**μ€λ μ½μ λ²μ** : 33. string νμλ³΄λ€ λ κ΅¬μ²΄μ μΈ νμ μ¬μ©νκΈ°

# Item 33. string νμλ³΄λ€ λ κ΅¬μ²΄μ μΈ νμ μ¬μ©νκΈ°

string νμμ λ²μλ λ§€μ° λλ€. "x"λ "y" κ°μ νκΈμλ μμ²­λ κΈμμ μμ€μ μ μ²΄ λ΄μ©λ string νμλλ€.   
string νμμΌλ‘ λ³μλ₯Ό μ μΈνλ € νλ€λ©΄, νΉμ κ·Έλ³΄λ€ λ μ’μ νμμ΄ μ μ νμ§ μμμ§ κ²ν ν΄ λ³΄μμΌ νλ€.   
   
>μμ μ»¬λ μμ λ§λ€κΈ° μν΄ μ¨λ²μ νμμ μ μνλ€κ³  κ°μ ν΄ λ³΄μ.
```ts
interface Album {
  artist: string;
  title: string;
  releaseDate: string;  // YYYY-MM-DD
  recordingType: string;  // E.g., "live" or "studio"
}
```
string νμμ΄ λ¨λ°λ λͺ¨μ΅μ΄λ€.   
κ²λ€κ° μ£Όμμ νμ μ λ³΄λ₯Ό μ μ΄ λ κ±Έ λ³΄λ©΄ νμ¬ μΈν°νμ΄μ€κ° μλͺ»λμλ€λ κ²μ μ μ μλ€.   
   
```ts
const kindOfBlue: Album = {
  artist: 'Miles Davis',
  title: 'Kind of Blue',
  releaseDate: 'August 17th, 1959',  // Oops!
  recordingType: 'Studio',  // Oops!
};  // OK
```
μ΄λ°μμΌλ‘ μλͺ» μλ ₯ν΄λ μ€νμ΄ μ λλ€.   
   
artistλ title κ°μ νλμλ string νμμ΄ μ μ ν΄λ³΄μΈλ€.   
κ·Έλ¬λ releaseDate νλλ Date κ°μ²΄λ₯Ό μ¬μ©ν΄μ λ μ§ νμμΌλ‘λ§ μ ννλ κ²μ΄ μ’λ€.   
recordingType νλλ 'live'μ 'studio', λ¨ λ κ°μ κ°μΌλ‘ μ λμ¨ νμμ μ μν  μ μλ€.   
```ts
type RecordingType = 'studio' | 'live';

interface Album {
  artist: string;
  title: string;
  releaseDate: Date;
  recordingType: RecordingType;
}

```
μ΄λ°μμΌλ‘ μ½λλ₯Ό μμ±νλ€λ©΄ μμ μ½λλ³΄λ€ μ€λ₯λ₯Ό λ μΈλ°νκ² μ²΄ν¬νλ€.   
   
μ΄λ κ² string νμλ³΄λ€ λ κ΅¬μ²΄μ μΈ νμμ μ¬μ©νλ©΄ μΈ κ°μ§ μ₯μ μ΄ μλ€.
   
μ²« λ²μ§Έ, νμμ λͺμμ μΌλ‘ μ μν¨μΌλ‘μ¨ λ€λ₯Έ κ³³μΌλ‘ κ°μ΄ μ λ¬λμ΄λ νμ μ λ³΄κ° μ μ§λλ€.   
    
μλ₯Ό λ€μ΄, νΉμ  λ μ½λ© νμμ μ¨λ²μ μ°Ύλ ν¨μλ₯Ό μμ±νλ€λ©΄ λ€μμ²λΌ μ μν  μ μλ€.   
   
```ts
function getAlbumsOfType(recordingType: string): Album[] {
  // COMPRESS
  return [];
  // END
}
```
μ΄λ°μμΌλ‘ stringμΌλ‘ μλ ₯νκ³  λ€λ₯Έ κ³³μμ νΈμΆν  λ recordingTypeμ΄ λ¬΄μμΈμ§ μ μ μλ€. 

νμ§λ§ `getAlbumsOfType(recording: RecordingType): Album[]`μ΄λ κ² νμμ λͺμν΄μ£Όλ©΄ νμμ λ―Έλ¦¬ μ μ μλ€.
<img width="713" alt="image" src="https://user-images.githubusercontent.com/76567238/217662385-3a21be73-8139-4024-8e14-916dd5c7eab5.png">

   
λ λ²μ§Έ, νμμ λͺμμ μΌλ‘ μ μνκ³  ν΄λΉ νμμ μλ―Έλ₯Ό μ€λͺνλ μ£Όμμ λΆμ¬λ£μ μ μλ€.   
```ts
/** μ΄ λΉμμ μ΄λ€ νκ²½μμ μ΄λ£¨μ΄μ‘λμ§? */
type RecordingType = 'live' | 'studio';
```
getAlbumsOfTypeμ΄ λ°λ λ§€κ°λ³μλ₯Ό string λμ  RecordingTypeμΌλ‘ λ°κΎΈλ©΄,   
ν¨μλ₯Ό μ¬μ©νλ κ³³μμ RecordingTypeμ μ€λͺμ λ³Ό μ μλ€.   
    
μΈ λ²μ§Έ, keyof μ°μ°μλ‘ λμ± μΈλ°νκ² κ°μ²΄μ μμ± μ²΄ν¬κ° κ°λ₯ν΄μ§λ€.   

    
ν¨μμ λ§€κ°λ³μμ stringμ μλͺ» μ¬μ©νλ μΌμ ννλ€.   
μ€μ λ‘ μΈλμ€μ½μ΄λΌμ΄λΈλ¬λ¦¬μλ pluckμ΄λΌλ ν¨μκ° μλ€.   
```ts
function pluck(record, key) {
  return record.map(r => r[key]);
}
```

pluck ν¨μμ μκ·Έλμ²λ₯Ό λ€μμ²λΌ μμ±ν  μ μλ€.   
```ts
function pluck(record: any[], key: string): any[] {
  return record.map(r => r[key]);
}
```
νμ μ²΄ν¬κ° λκΈ΄ νμ§λ§ any νμμ΄ μμ΄μ μ λ°νμ§ λͺ»νλ€.   
νΉν λ°ν κ°μ anyλ₯Ό μ¬μ©νλ κ²μ λ§€μ° μ’μ§ μμ μ€κ³μ΄λ€.   
λ¨Όμ  νμ μκ·Έλμ²λ₯Ό κ°μ νλ μ²« λ¨κ³λ‘ μ λλ¦­ νμμ λμν΄ λ³΄μ   
```ts
function pluck<T>(record: T[], key: string): any[] {
  return record.map(r => r[key]);
                      // ~~~~~~ '{}' νμμ μΈλ±μ€ μκ·Έλμ²κ° μμΌλ―λ‘
                      //        μμμ μμμ μΌλ‘ 'any' νμμ΄ μλ€.
}
```

μ΄μ  νμμ€ν¬λ¦½νΈλ keyμ νμμ΄ stringμ΄κΈ° λλ¬Έμ λ²μκ° λλ¬΄ λλ€λ μ€λ₯λ₯Ό λ°μμν¨λ€.   
Albumμ λ°°μ΄μ λ§€κ°λ³μλ‘ μ λ¬νλ©΄ κΈ°μ‘΄μ stringνμμ λμ λ²μμ λ°λλ‘ , keyλ λ¨ λ€ κ°μ κ°("artist", "title", "releaseDate", "recordingType")λ§μ΄ μ ν¨νλ€.    
λ€μ μμλ keyof Album νμμΌλ‘ μ»κ² λλ κ²°κ³Όμ΄λ€.   
```ts
interface Album {
  artist: string;
  title: string;
  releaseDate: Date;
  recordingType: RecordingType;
}
type K = keyof Album;
// Type is "artist" | "title" | "releaseDate" | "recordingType"

//κ·Έλ¬λ―λ‘ stringμ keyof Tλ‘ λ°κΎΈλ©΄ λλ€.   

function pluck<T>(record: T[], key: keyof T) {
  return record.map(r => r[key]);
}
```


```ts
function pluck<T>(record: T[], key: K): T[K][] {
  return record.map(r => r[key]);
}
```
T[keyof T]λ T κ°μ²΄ λ΄μ κ°λ₯ν λͺ¨λ  κ°μ νμμ΄λ€.   
κ·Έλ°λ° keyμ κ°μΌλ‘ νλμ λ¬Έμμ΄μ λ£κ² λλ©΄, κ·Έ λ²μκ° λλ¬΄ λμ΄μ μ μ ν νμμ΄λΌκ³  λ³΄κΈ° μ΄λ ΅λ€.    
μλ₯Ό λ€μ΄λ³΄μ   
```ts
function pluck<T>(record: T[], key: keyof T) {
  return record.map(r => r[key]);
}
declare let albums: Album[];
const releaseDates = pluck(albums, 'releaseDate'); // Type is (string | Date)[]
```
releaseDatesμ νμμ (string | Date)[]κ° μλλΌ Date[]μ΄μ΄μΌ νλ€.   
keyof Tλ stringμ λΉνλ©΄ ν¨μ¬ λ²μκ° μ’κΈ°λ νμ§λ§ κ·Έλλ μ¬μ ν λλ€.   
λ°λΌμ λ²μλ₯Ό λ μ’νκΈ° μν΄μ, keyof Tμ λΆλΆ μ§ν©μΌλ‘ λ λ²μ§Έ μ λλ¦­ λ§€κ°λ³μλ₯Ό λμν΄μΌ νλ€.   
```ts
function pluck<T, K extends keyof T>(record: T[], key: K): T[K][] {
  return record.map(r => r[key]);
}
```
    
μ΄μ  νμ μκ·Έλμ²κ° μλ³ν΄μ‘λ€.   
pluckμ μ¬λ¬ κ°μ§ λ°©λ²μΌλ‘ νΈμΆνλ©΄μ μ λλ‘ λ°ν νμμ΄ μΆλ‘ λλμ§, λ¬΄ν¨ν λ§€κ°λ³μλ₯Ό λ°©μ§ν  μ μλμ§ νμΈν΄ λ³Ό μ μλ€.    
```ts
pluck(albums, 'releaseDate'); // Type is Date[]
pluck(albums, 'artist');  // Type is string[]
pluck(albums, 'recordingType');  // Type is RecordingType[]
pluck(albums, 'recordingDate');
           // ~~~~~~~~~~~~~~~ Argument of type '"recordingDate"' is not
           //                 assignable to parameter of type ...
```
λ§€κ°λ³μ νμμ΄ μ λ°ν΄μ§ λλΆμ μΈμ΄ μλΉμ€λ Albumμ ν€μ μλ μμ± κΈ°λ₯μ μ κ³΅ν  μ μκ² ν΄μ€λ€.   
<img width="486" alt="image" src="https://user-images.githubusercontent.com/76567238/217654772-7bc08db3-ebaa-4eb5-ae08-26e59761deae.png">
νμμ stringμΌλ‘ νμΌλ©΄ μ΄λ κ² μλμμ±μ΄ λ¨μ§ μλλ€.
    
stringμ anyμ λΉμ·ν λ¬Έμ λ₯Ό κ°μ§κ³  μλ€.   
λ°λΌμ μλͺ» μ¬μ©νκ² λλ©΄ λ¬΄ν¨ν κ°μ νμ©νκ³  νμ κ°μ κ΄κ³λ κ°μΆμ΄ λ²λ¦°λ€.   
μ΄λ¬ν λ¬Έμ μ μ νμ μ²΄μ»€λ₯Ό λ°©ν΄νκ³  μ€μ  λ²κ·Έλ₯Ό μ°Ύμ§ λͺ»νκ² λ§λ λ€.   
νμμ€ν¬λ¦½νΈμμ stringμ λΆλΆ μ§ν©μ μ μν  μ μλ κΈ°λ₯μ μλ°μ€ν¬λ¦½νΈ μ½λμ νμ μμ μ±μ ν¬κ² λμΈλ€.   
λ³΄λ€ μ νν νμμ μ¬μ©νλ©΄ μ€λ₯λ₯Ό λ°©μ§νκ³  μ½λμ κ°λμ±λ ν₯μμν¬ μ μλ€.   

<br>

## μμ½
- λ¬Έμμ΄μ λ¨λ°νμ¬ μ μΈλ μ½λλ₯Ό νΌνμ λͺ¨λ  λ¬Έμμ΄μ ν λΉν  μ μλ string νμλ³΄λ€λ λ κ΅¬μ²΄μ μΈ νμμ μ¬μ©νλ κ²μ΄ μ’λ€.   
- λ³μμ λ²μλ₯Ό λ³΄λ€ μ ννκ² νννκ³  μΆλ€λ©΄ string νμλ³΄λ€λ λ¬Έμμ΄ λ¦¬ν°λ΄ νμμ μ λμ¨μ μ¬μ©νλ©΄ λλ€. νμ μ²΄ν¬λ₯Ό λ μκ²©ν ν  μ μκ³  μμ°μ±μ ν₯μμν¬ μ μλ€.   
- κ°μ²΄μ μμ± μ΄λ¦μ ν¨μ λ§€κ°λ³μλ‘ λ°μ λλ stringλ³΄λ€λ keyof Tλ₯Ό μ¬μ©νλ κ²μ΄ μ’λ€.

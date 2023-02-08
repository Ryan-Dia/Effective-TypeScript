## 📕 오늘 읽은 내용, 이런 식으로 정리해 봅시다. ✒

**TIL(Today I learn) 기록일** : 2022.02.08

**오늘 읽은 범위** : 33. string 타입보다 더 구체적인 타입 사용하기

# Item 33. string 타입보다 더 구체적인 타입 사용하기

string 타입의 범위는 매우 넓다. "x"나 "y" 같은 한글자도 엄청난 글자의 소설의 전체 내용도 string 타입니다.   
string 타입으로 변수를 선언하려 한다면, 혹시 그보다 더 좁은 타입이 적절하지 않은지 검토해 보아야 한다.   
   
>음악 컬렉션을 만들기 위해 앨범의 타입을 정의한다고 가정해 보자.
```ts
interface Album {
  artist: string;
  title: string;
  releaseDate: string;  // YYYY-MM-DD
  recordingType: string;  // E.g., "live" or "studio"
}
```
string 타입이 남발된 모습이다.   
게다가 주석에 타입 정보를 적어 둔 걸 보면 현재 인터페이스가 잘못되었다는 것을 알 수 있다.   
   
```ts
const kindOfBlue: Album = {
  artist: 'Miles Davis',
  title: 'Kind of Blue',
  releaseDate: 'August 17th, 1959',  // Oops!
  recordingType: 'Studio',  // Oops!
};  // OK
```
이런식으로 잘못 입력해도 실행이 잘 된다.   
   
artist나 title 같은 필드에는 string 타입이 적절해보인다.   
그러나 releaseDate 필드는 Date 객체를 사용해서 날짜 형식으로만 제한하는 것이 좋다.   
recordingType 필드는 'live'와 'studio', 단 두 개의 값으로 유니온 타입을 정의할 수 있다.   
```ts
type RecordingType = 'studio' | 'live';

interface Album {
  artist: string;
  title: string;
  releaseDate: Date;
  recordingType: RecordingType;
}

```
이런식으로 코드를 작성한다면 앞의 코드보다 오류를 더 세밀하게 체크한다.   
   
이렇게 string 타입보다 더 구체적인 타입을 사용하면 세 가지 장점이 있다.
   
첫 번째, 타입을 명시적으로 정의함으로써 다른 곳으로 값이 전달되어도 타입 정보가 유지된다.   
    
예를 들어, 특정 레코딩 타입의 앨범을 찾는 함수를 작성한다면 다음처럼 정의할 수 있다.   
   
```ts
function getAlbumsOfType(recordingType: string): Album[] {
  // COMPRESS
  return [];
  // END
}
```
이런식으로 string으로 입력하고 다른 곳에서 호출할 때 recordingType이 무엇인지 알 수 없다. 

하지만 `getAlbumsOfType(recording: RecordingType): Album[]`이렇게 타입을 명시해주면 타입을 미리 알 수 있다.
<img width="713" alt="image" src="https://user-images.githubusercontent.com/76567238/217662385-3a21be73-8139-4024-8e14-916dd5c7eab5.png">

   
두 번째, 타입을 명시적으로 정의하고 해당 타입의 의미를 설명하는 주석을 붙여넣을 수 있다.   
```ts
/** 이 녹음은 어떤 환경에서 이루어졌는지? */
type RecordingType = 'live' | 'studio';
```
getAlbumsOfType이 받는 매개변수를 string 대신 RecordingType으로 바꾸면,   
함수를 사용하는 곳에서 RecordingType의 설명을 볼 수 있다.   
    
세 번째, keyof 연산자로 더욱 세밀하게 객체의 속성 체크가 가능해진다.   

    
함수의 매개변수에 string을 잘못 사용하는 일은 흔하다.   
실제로 언더스코어라이브러리에는 pluck이라는 함수가 있다.   
```ts
function pluck(record, key) {
  return record.map(r => r[key]);
}
```

pluck 함수의 시그니처를 다음처럼 작성할 수 있다.   
```ts
function pluck(record: any[], key: string): any[] {
  return record.map(r => r[key]);
}
```
타입 체크가 되긴 하지만 any 타입이 있어서 정밀하지 못한다.   
특히 반환 값에 any를 사용하는 것은 매우 좋지 않은 설계이다.   
먼저 타입 시그니처를 개선하는 첫 단계로 제너릭 타입을 도입해 보자   
```ts
function pluck<T>(record: T[], key: string): any[] {
  return record.map(r => r[key]);
                      // ~~~~~~ '{}' 형식에 인덱스 시그니처가 없으므로
                      //        요소에 암시적으로 'any' 형식이 있다.
}
```

이제 타입스크립트는 key의 타입이 string이기 때문에 범위가 너무 넓다는 오류를 발생시킨다.   
Album의 배열을 매개변수로 전달하면 기존의 string타입의 넓은 범위와 반대로 , key는 단 네 개의 값("artist", "title", "releaseDate", "recordingType")만이 유효하다.    
다음 예시는 keyof Album 타입으로 얻게 되는 결과이다.   
```ts
interface Album {
  artist: string;
  title: string;
  releaseDate: Date;
  recordingType: RecordingType;
}
type K = keyof Album;
// Type is "artist" | "title" | "releaseDate" | "recordingType"

//그러므로 string을 keyof T로 바꾸면 된다.   

function pluck<T>(record: T[], key: keyof T) {
  return record.map(r => r[key]);
}
```


```ts
function pluck<T>(record: T[], key: K): T[K][] {
  return record.map(r => r[key]);
}
```
T[keyof T]는 T 객체 내의 가능한 모든 값의 타입이다.   
그런데 key의 값으로 하나의 문자열을 넣게 되면, 그 범위가 너무 넓어서 적절한 타입이라고 보기 어렵다.    
예를 들어보자   
```ts
function pluck<T>(record: T[], key: keyof T) {
  return record.map(r => r[key]);
}
declare let albums: Album[];
const releaseDates = pluck(albums, 'releaseDate'); // Type is (string | Date)[]
```
releaseDates의 타입은 (string | Date)[]가 아니라 Date[]이어야 한다.   
keyof T는 string에 비하면 훨씬 범위가 좁기는 하지만 그래도 여전히 넓다.   
따라서 범위를 더 좁히기 위해서, keyof T의 부분 집합으로 두 번째 제너릭 매개변수를 도입해야 한다.   
```ts
function pluck<T, K extends keyof T>(record: T[], key: K): T[K][] {
  return record.map(r => r[key]);
}
```
    
이제 타입 시그니처가 완변해졌다.   
pluck을 여러 가지 방법으로 호출하면서 제대로 반환 타입이 추론되는지, 무효한 매개변수를 방지할 수 있는지 확인해 볼 수 있다.    
```ts
pluck(albums, 'releaseDate'); // Type is Date[]
pluck(albums, 'artist');  // Type is string[]
pluck(albums, 'recordingType');  // Type is RecordingType[]
pluck(albums, 'recordingDate');
           // ~~~~~~~~~~~~~~~ Argument of type '"recordingDate"' is not
           //                 assignable to parameter of type ...
```
매개변수 타입이 정밀해진 덕분에 언어 서비스는 Album의 키에 자동 완성 기능을 제공할 수 있게 해준다.   
<img width="486" alt="image" src="https://user-images.githubusercontent.com/76567238/217654772-7bc08db3-ebaa-4eb5-ae08-26e59761deae.png">
타입을 string으로 했으면 이렇게 자동완성이 뜨지 않는다.
    
string은 any와 비슷한 문제를 가지고 있다.   
따라서 잘못 사용하게 되면 무효한 값을 허용하고 타입 간의 관계도 감추어 버린다.   
이러한 문제점은 타입 체커를 방해하고 실제 버그를 찾지 못하게 만든다.   
타입스크립트에서 string의 부분 집합을 정의할 수 있는 기능은 자바스크립트 코드에 타입 안전성을 크게 높인다.   
보다 정확한 타입을 사용하면 오류를 방지하고 코드의 가독성도 향상시킬 수 있다.   

<br>

## 요약
- 문자열을 남발하여 선언된 코드를 피하자 모든 문자열을 할당할 수 있는 string 타입보다는 더 구체적인 타입을 사용하는 것이 좋다.   
- 변수의 범위를 보다 정확하게 표현하고 싶다면 string 타입보다는 문자열 리터럴 타입의 유니온을 사용하면 된다. 타입 체크를 더 엄격히 할 수 있고 생산성을 향상시킬 수 있다.   
- 객체의 속성 이름을 함수 매개변수로 받을 때는 string보다는 keyof T를 사용하는 것이 좋다.

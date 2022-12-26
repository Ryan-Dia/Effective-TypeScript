# 📕 읽은내용 정리해보기


# 📚 Item5. 　 any 타입 지양하기


타입스크립트는 코드에 타입을 조금씩 추가할 수 있기 때문에 점진적이고   
언제든지 타입 체커를 해제할 수 있기 때문에 선택적이다.   

```ts
let age : number;
age = '12';
// ~~ "12" 형식은 'number' 형식에 할당할 수 없습니다.
age = '12' as any; // ok
```

이 오류는 as any를 추가해 해결할 수 있다.    
우리는 타입 선언을 추가하는 데에 시간을 쏟고 싶지 않거나 오류가 이해되지 않거나 타입 체커가 틀렸다고 생각할 때   
any  타입이나 타입 단언문(as any)을 사용하고 싶을 때가 있다.   
하지만 이렇게 하면 특별한 경우를 제외하고 any를 사용하면 타입스크립트의 수많은 장점을 누릴 수 없게 된다.   
부득이하게 any를 사용하더라도 그 위험성을 알고 있어야한다.    

## 1) any 타입에는 타입 안정성이 없습니다. 

>as any를 사용하면 string 타입을 할당할 수 있게 됩니다.
>타입 체커는 선언에 따라 number타입으로 판단할 것이고 혼돈은 걷잡을 수 없게 됩니다.
```ts
let age: number;
age = "12";

age += 1; // 런타임에 정상, age는 "121"
```

## 2) any는 함수 시그니처를 무시해 버립니다.

>함수를 작성할 때는 시그니처를 명시해야 합니다.
>호출하는 쪽은 약속된 타입의 입력을 제공하고, 함수는 약속된 타입의 출력을 반환합니다.
>그러나 any타입을 사용하면 이런 약속을 어길 수 있습니다.

```ts
function calculateAge(birthDate: Date): number {
 // ...
}

let birthDate: any = '1990-01-19';
calculateAge(birthDate); // 정상
```

birthDate 매개변수는 string이 아닌 Date 타입이어야 한다.   
any타입을 사용하면 calculateAge의 시그니처를 무시하게 된다.   
자바스크립트에서는 종종 암시적으로 타입이 변환되기 때문에 이런 경우 특히 문제가 될 수 있습니다.   
string 타입은 number 타입이 필요한 곳에서 오류 없이 실행될 때가 있고, 그럴 럴경우 다른 곳에서 문제를 일으키게 된다.   

## 3) any 타입에는 언어 서비스가 적용되지 않습니다.
 

### (1) 자동 완성 기능

>어떤 심벌에 타입이 있다면 타입스크립트 언어 서비스는 자동완성 기능과 적절한 도움말을 제공한다. 

![image](https://user-images.githubusercontent.com/76567238/209485288-c3eb56a3-af0d-4d33-b072-f63006a4185f.png)

>그러나 any 타입인 심벌을 사용하면 아무런 도움을 받지 못한다. 

![image](https://user-images.githubusercontent.com/76567238/209486088-d1b1bea9-9133-4c30-9c5b-0e03b4c469a1.png)
### (2) 이름 변경 기능
> 이름 변경 기능은 또 다른 언어 서비스이다.  
> 다음 코드는 Person 타입과 이름을 포매팅해 주는 함수이다.
```ts
interface Person {
  first : string;
  last : string;
}

const formatName = (p : Person) => `${p.first} ${p.last}`;
const formatName = (p : any) => `${p.first} ${p.last}`;

```

>편집기에서 앞 코드의 first를 선택하고, 'Rename Symbol'을 클릭해, firstName으로 바꿀 수 있다.


![image](https://user-images.githubusercontent.com/76567238/209485470-928f7480-d3b8-4a84-b061-07032f174949.png)

>예를 들어 formatName 함수 내의 first가 firstName으로 바뀝니다.   
>하지만 any타입의 심벌은 바뀌지 않습니다.   

```ts
interface Person{
  firstName : string;
  last : string;
}
const formatName = (p: Person) => `${p.firstName} ${p.last}`;
const formatNameAny = (p: any) => `${p.first} ${p.last}`;
```


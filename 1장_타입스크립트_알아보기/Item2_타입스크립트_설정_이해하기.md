# 📕 읽은내용 정리해보기


## 📚 Item 2 타입스크립트 설정 이해하기

- 타입스크립트 컴파일러는 매우 많은 설정을 가지고 있습니다. (현재 시점에는 설정이 거의 100개에 이른다.)
- 가급적 설정 파일을 사용하는 것이 좋다. 그래야만 타입스크립트를 어떻게 사용할 계획인지 동료들이나 다른 도구들이 알 수 있다.
- 설정 파일은 tsc --init만 실행하면 간단히 생성된다.

```
타입스크립트의 설정들은 어디서 소스 파일을 찾을지, 어떤 종류의 출력을 생성할지 제어하는 내용이 대부분이다.
그런데 언어 자체의 핵심 요소들을 제어하는 설정도 있다.
대부분의 언어에서는 허용하지 않는 고수준 설계의 설정이다.
타입스크립트는 어떻게 설정하느냐에 따라 완전히 다른 언어처럼 느껴질 수 있다.
설정을 제대로 사용하려면, noImplicitAny와 strictNullChecks를 이해해야한다.
```

---

### 📖 noImplicitAny

- noImplicitany는 변수들이 미리 정의된 타입을 가져야 하는지 여부를 제어한다.

> 다음 코드는 noImplicitAny가 해제되어 있을 때에는 유효하다
```
function add(a, b){
  return a + b;
}

// add에 마우스를 올려 보면 타입스크립트가 추론한 함수의 타입을 알 수 있다.
function add(a: any, b: any): any
```
any 타입을 매개변수에 사용하면 타입 체커는 무력해진다.   
any는 유용하지만 매우 주의해서 사용해야한다.

> 다음 코드는 noImplicitAny가 설정되어있어 any로 하면 오류가 발생하기에 타입을 명확하게 정해주었다.
```
function(a:number, b:number) {
  return a + b;
}
```
타입스크립트는 타입 정보를 가질 때 가장 효과적이기 때문에, 되로록이면 **noImplicitAny를 설정해야 한다.**
- noImplicitAny의 장점
  1. 문제발견 쉬워진다.
  2. 코드의 가독성이 좋아진다.
  3. 개발자의 샐산성이 향상된다. 
<br>

---

### 📖 strictNullChecks

- strictNullChecks는 null과 undefined가 모든 타입에서 허용되는지 확인하는 설정이다.

> 다음은 strictNullChecks가 해제되었을 때 유효한 코드이다.
```
const x : number = null; // 정상, null은 유효한 값입니다.
```
> 다음은 strictNullChecks가 설정되었을 때 입니다.
```
const x: number =null;
//   ~ 'null 형식은 'number' 형식에 할당할 수 없습니다.
```
null 대신 undefined를 써도 같은 오류가 난다.   
>만약 null을 허용하려고 한다면, 의도를 명시적으로 드러냄으로써 오류를 고칠 수 있다.
```
const x: number | null = null;
```


## 🔖 요약
- 타입스크립트 컴파일러는 언어의 핵심 요소에 영향을 미치는 몇 가지 설정을 포함하고 있다.
- 타입스크립트 설정은 커맨드 라인을 이용하기보다는 tsconfig.json을 사용하는 것이 좋다.
- 자바스크립트 프로젝트를 타입스크립트로 전환하는 게 아니라면 noImplicitAny를 설정하는 것이 좋다.
- 'undefined는 객체가 아닙니다' 같은 런타임 오류를 방지하기 위해 strictNullChecks를 설정하는 것이 좋다.
- 타입스크립트에서 엄격한 체크를 하고 싶다면 strict 설정을 고려해야 한다.

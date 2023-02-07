## 📕 오늘 읽은 내용, 이런 식으로 정리해 봅시다. ✒

**TIL(Today I learn) 기록일** : 2022.02.07

**오늘 읽은 범위** : 32. 유니온의 인터페이스보다는 인터페이스의 유니온을 사용하기

# Item 32. 유니온의 인터페이스보다는 인터페이스의 유니온을 사용하기

유니온 타입의 속성을 가지는 인터페이스를 작성 중이라면, 혹시 인터페이스의 유니온 타입을 사용하는 게 더 알맞지는 않을지 검토해 봐야 한다.   
>백터를 그리는 프로그램을 작성 중이고, 특정한 기하학적 타입을 가지는 계층의 인터페이스를 정의한다고 가정해보자
```ts
type FillPaint = unknown;
type LinePaint = unknown;
type PointPaint = unknown;
type FillLayout = unknown;
type LineLayout = unknown;
type PointLayout = unknown;
interface Layer {
  layout: FillLayout | LineLayout | PointLayout;
  paint: FillPaint | LinePaint | PointPaint;
}
```
여기서 layout이 LineLayout 타입이면서 paint 속성이 FillPaint 타입인 것은 말이 되지 않는다.   
이런 조합을 허용한다면 라이브러리에서는 오류가 발생하기 십상이고 인터페이스를 다루기도 어려울 것이다.   
    
>더 나은 방법으로 모델링하려면 각각 타입의 계층을 분리된 인터페이스로 둬야 한다.   
```ts
interface FillLayer {
  layout: FillLayout;
  paint: FillPaint;
}
interface LineLayer {
  layout: LineLayout;
  paint: LinePaint;
}
interface PointLayer {
  layout: PointLayout;
  paint: PointPaint;
}
type Layer = FillLayer | LineLayer | PointLayer;
```

이런 형태로 Layer를 정의하면 layout과 paint 속성이 잘못된 조합으로 섞이는 경우를 방지할 수 있다.   
이 코드에서는 아이템 28의 조언에 따라 유효한 상태만을 표현하도록 타입을 정의했다.   
   
이러한 패턴의 가장 일반적인 예시는 태그된 유니온이다.   
Layer의 경우 속성 중의 하나는 문자열 리터럴 타입의 유니온이 된다.   

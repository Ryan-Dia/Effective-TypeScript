## π μ€λ μ½μ λ΄μ©, μ΄λ° μμΌλ‘ μ λ¦¬ν΄ λ΄μλ€. β

**TIL(Today I learn) κΈ°λ‘μΌ** : 2022.01.28

**μ€λ μ½μ λ²μ** : 22. νμ μ’νκΈ°

# Item 22. νμ μ’νκΈ°

νμ μ’νκΈ°λ νμμ€ν¬λ¦½νΈκ° λμ νμμΌλ‘λΆν° μ’μ νμμΌλ‘ μ§ννλ κ³Όμ μ λ§νλ€.   
μλ§λ κ°μ₯ μΌλ°μ μΈ μμλ null μ²΄ν¬μΌ κ²μ΄λ€.   
```ts
const el = document.getElementById('foo'); // Type is HTMLElement | null
if (el) {
  el                                       // Type is HTMLElement
  el.innerHTML = 'Party Time'.blink();
} else {
  el                                       // Type is null
  alert('No element #foo');
}
```

λ§μ½ elμ΄ nullμ΄λΌλ©΄, λΆκΈ°λ¬Έμ μ²« λ²μ§Έ λΈλ‘μ΄ μ€νλμ§ μλλ€.   
μ¦, μ²« λ²μ§Έ λΈλ‘μμ HTMLElement | null νμμ nullμ μ μΈνλ―λ‘, λ μ’μ νμμ΄λμ΄ μμμ΄ ν¨μ¬ μ¬μμ§λ€.    
νμ μ²΄μ»€λ μΌλ°μ μΌλ‘ μ΄λ¬ν μ‘°κ±΄λ¬Έμμ νμ μ’νκΈ°λ₯Ό μ ν΄λ΄μ§λ§, νμ λ³μΉ­μ΄ μ‘΄μ¬νλ€λ©΄ κ·Έλ¬μ§ λͺ»ν  μλ μλ€. (νμ λ³μΉ­μ λν λ΄μ©μ μμ΄ν 24μμ λ€λ£¬λ€)   
   
λΆκΈ°λ¬Έμμ μμΈλ₯Ό λμ§κ±°λ ν¨μλ₯Ό λ°ννμ¬ λΈλ‘μ λλ¨Έμ§ λΆλΆμμ λ³μμ νμμ μ’ν μλ μλ€.   
>μλ₯Ό λ€μ΄λ³΄μ.
```ts
const el = document.getElementById('foo');        // Type is HTMLElement | null
if (!el) throw new Error('Unable to find #foo');
el;                                             // 
el.innerHTML = 'Party Time'.blink();
```
μ΄ μΈμλ νμμ μ’νλ λ°©λ²μ λ§μ΄ μλ€.   
λ€μμ instanceofλ₯Ό μ¬μ©ν΄μ νμμ μ’νλ μμ λ€.   
   
```ts
function contains(text: string, search: string|RegExp) {
  if (search instanceof RegExp) {
    search  // Type is RegExp
    return !!search.exec(text);
  }
  search  // Type is string
  return text.includes(search);
}
```
>μμ± μ²΄ν¬λ‘λ νμμ μ’ν μ μλ€.   
```ts
interface A { a: number }
interface B { b: number }
function pickAB(ab: A | B) {
  if ('a' in ab) {
    ab // Type is A
  } else {
    ab // Type is B
  }
  ab // Type is A | B
}
```

>Array.isArray κ°μ μΌλΆ λ΄μ₯ ν¨μλ‘λ νμμ μ’ν μ μλ€.
```ts
function contains(text: string, terms: string|string[]) {
  const termList = Array.isArray(terms) ? terms : [terms];
  termList // Type is string[]
  // ...
}
```

νμμ€ν¬λ¦½νΈλ μΌλ°μ μΌλ‘ μ‘°κ±΄λ¬Έμμ νμμ μ’νλ λ° λ§€μ° λ₯μνλ€.   
κ·Έλ¬λ νμμ μ£λΆλ¦¬ νλ¨νλ μ€μλ₯Ό μ μ§λ₯΄κΈ° μ¬μ°λ―λ‘ λ€μ νλ² κΌΌκΌΌν λ°μ Έ λ΄μΌ νλ€.   
>μλ₯Ό λ€μ΄, λ€μ μμ λ μ λμ¨ νμμμ nullμ μ μΈνκΈ° μν΄ μλͺ»λ λ°©λ²μ μ¬μ©νλ€.
```ts
const el = document.getElementById('foo'); // type is HTMLElement | null
if (typeof el === 'object') {
  el;  // Type is HTMLElement | null
}
```
>μλ°μ€ν¬λ¦½νΈμμ typeof nullμ΄ "object"μ΄κΈ° λλ¬Έμ, ifκ΅¬λ¬Έμμ nullμ΄ μ μΈλμ§ μμμ΅λλ€.   
>λν κΈ°λ³Έν κ°μ΄ μλͺ»λμ΄λ λΉμ·ν μ¬λ‘κ° λ°μνλ€.   
```ts
function foo(x?: number|string|null) {
  if (!x) {
    x;  // Type is string | number | null | undefined
  }
}
```

λΉ λ¬Έμμ΄ ''κ³Ό 0 λͺ¨λ falseκ° λκΈ° λλ¬Έμ, νμμ μ ν μ’νμ§μ§ μμκ³  xλ μ¬μ ν λΈλ‘ λ΄μμ string λλ numberκ° λλ€.   
>νμμ μ’νλ λ λ€λ₯Έ μΌλ°μ μΈ λ°©λ²μ λͺμμ  'νκ·Έ'λ₯Ό λΆμ΄λ κ²μ΄λ€.   
```ts
interface UploadEvent { type: 'upload'; filename: string; contents: string }
interface DownloadEvent { type: 'download'; filename: string; }
type AppEvent = UploadEvent | DownloadEvent;

function handleEvent(e: AppEvent) {
  switch (e.type) {
    case 'download':
      e  // Type is DownloadEvent
      break;
    case 'upload':
      e;  // Type is UploadEvent
      break;
  }
}
```
μ΄ ν¨ν΄μ 'νκ·Έλ μ λμ¨' λλ 'κ΅¬λ³λ μ λμ¨'μ΄λΌκ³  λΆλ¦¬λ©°, νμμ€ν¬λ¦½νΈ μ΄λμμλ μ°Ύμλ³Ό μ μλ€.   
   
>λ§μ½ νμμ€ν¬λ¦½νΈκ° νμμ μλ³νμ§ λͺ»νλ€λ©΄, μλ³μ λκΈ° μν΄ μ»€μ€ν ν¨μλ₯Ό λμν  μ μλ€.   
```ts
function isInputElement(el: HTMLElement): el is HTMLInputElement {
  return 'value' in el;
}

function getElementContent(el: HTMLElement) {
  if (isInputElement(el)) {
    el; // Type is HTMLInputElement
    return el.value;
  }
  el; // Type is HTMLElement
  return el.textContent;
}
```

μ΄λ¬ν κΈ°λ²μ 'μ¬μ©μ μ μ νμ κ°λ'λΌκ³  νλ€.     
λ°ν νμμ el is HTMLInputElementλ ν¨μμ λ°νμ΄ trueμΈ κ²½μ°, νμ μ²΄μ»€μκ² λ§€κ°λ³μμ νμμ μ’ν μ μλ€κ³  μλ €μ€λ€.    
   
μ΄λ€ ν¨μλ€μ νμ κ°λλ₯Ό μ¬μ©νμ¬ λ°°μ΄κ³Ό κ°μ²΄μ νμ μ’νκΈ°λ₯Ό ν  μ μλ€.   
μλ₯Ό λ€μ΄, λ°°μ΄μμ μ΄λ€ νμμ μνν  λ undefinedκ° λ  μ μλ νμμ μ¬μ©ν  μ μλ€.   
```ts
const jackson5 = ['Jackie', 'Tito', 'Jermaine', 'Marlon', 'Michael'];
const members = ['Janet', 'Michael'].map(
  who => jackson5.find(n => n === who)
);  // Type is (string | undefined)[]
```
> filter ν¨μλ₯Ό μ¬μ©ν΄ undefinedλ₯Ό κ±Έλ¬ λ΄λ €κ³  ν΄λ μ λμνμ§ μμ κ²μ΄λ€.
```ts
const jackson5 = ['Jackie', 'Tito', 'Jermaine', 'Marlon', 'Michael'];
const members = ['Janet', 'Michael'].map(
  who => jackson5.find(n => n === who)
).filter(who => who !== undefined);  // Type is (string | undefined)[]
```
> μ΄λ΄ λ νμ κ°λλ₯Ό μ¬μ©νλ©΄ νμμ μ’ν μ μλ€.
```ts
const jackson5 = ['Jackie', 'Tito', 'Jermaine', 'Marlon', 'Michael'];
function isDefined<T>(x: T | undefined): x is T {
  return x !== undefined;
}
const members = ['Janet', 'Michael'].map(
  who => jackson5.find(n => n === who)
).filter(isDefined);  // Type is string[]
```

<br>

νΈμ§κΈ°μμ νμμ μ‘°μ¬νλ μ΅κ΄μ κ°μ§λ©΄ νμ μ’νκΈ°κ° μ΄λ»κ² λμνλμ§ μμ°μ€λ  μ΅ν μ μλ€.   
νμμ€ν¬λ¦½νΈμμ νμμ΄ μ΄λ»κ² μ’νμ§λμ§ μ΄ν΄νλ€λ©΄ νμ μΆλ‘ μ λν κ°λμ μ‘μ μ μκ³ , μ€λ₯ λ°μμ μμΈμ μ μ μμΌλ©°, νμ μ²΄μ»€λ₯Ό λ ν¨μ¨μ μΌλ‘ μ΄μ©ν  μ μλ€.   
   
<br>

## μμ½
- λΆκΈ°λ¬Έ μΈμλ μ¬λ¬ μ’λ₯μ μ μ΄ νλ¦μ μ΄ν΄λ³΄λ©° νμμ€ν¬λ¦½νΈκ° νμμ μ’νλ κ³Όμ μ μ΄ν΄ν΄μΌ νλ€.   
- νκ·Έλ/κ΅¬λ³λ μ λμ¨κ³Ό μ¬μ©μ μ μ νμ κ°λλ₯Ό μ¬μ©νμ¬ νμ μ’νκΈ° κ³Όμ μ μννκ² λ§λ€ μ μλ€.

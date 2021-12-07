---
title: "모던 자바스크립트 딥 다이브 20장 strict mode"
excerpt: "[javascript] 모던 자바스크립트 Deep Dive 20장 strict mode"
categories:
 - Javascript
tags:
 - Javascript
toc: true
toc_sticky: true
last_modified_at: 2021-12-06
---

# 20장 strict mode

## 1.strict mode란?
자바스크립트 언어의 문법을 좀 더 엄격히 적용하여 오류를 발생시킬 가능성이 높거나 자바스크립트 엔진의 최적화 작업에 **문제를 일으킬 수 있는 코드에 대한 명시적인 에러를 발생**시킨다.  

```javascript
function foo() {
  x = 10;
}
foo();

console.log(x); // ?
```

위 예제를 실행하면 결과는 `10`이 출력된다.  
`x = 10` 코드를 만나면 자바스크립트 엔진은 `foo`함수 스코프에서 `x`식별자를 검색한다.  
`foo`함수 스코프에 없으면 스코프 체인을 통해 상위 스코프에서 `x` 변수를 검색한다. 즉, 전역 스코프에서 검색하는데, 전역 스코프에 없으면 `RefferenceError`가 발생해야 하는데 자바스크립트 엔진은 암묵적으로 전역 객체에 `x`프로퍼티를 동적으로 생성한다. 이를 **암묵적 전역**`implicit global`이라 한다.  

이런 현상은 개발자의 의도와 다르게 동작할 우려가 있어 좋지 않다.  
따라서 반드시 `var`, `let`, `const`키워드를 사용하여 변수를 선언한 다음 사용해야 한다. (키워드만 적어줘도 암묵적 전역은 발생하지 않는다.)   

혹은 ESLint 린트 도구를 사용하자.
참고: [https://poiemaweb.com/eslint](https://poiemaweb.com/eslint)  

## 2.strict mode의 적용
`strict mode`를 적용하려면 전역의 선두 또는 함수 몸체의 선두에 `'use strict';`를 추가한다. 전역의 선두에 추가하면 스크립트 전체에 `strict mode`가 적용된다.  

전역 선두에 선언
```javascript
'use strict';

function foo() {
  x = 10; // ReferenceError: x is not defined
}
foo();
```

함수 몸체 선두에 선언  
```javascript
function foo() {
  'use strict';

  x = 10; // ReferenceError: x is not defined
}
foo();
```

`'use strict';`를 선두에 위치하지 않으면 제대로 동작하지 않는다.
```javascript
function foo() {
  x = 10; // 에러를 발생시키지 않는다.
  'use strict';
}
foo();
```


## 3.전역에 strict mode를 적용하는 것은 피하자
전역에 적용한 `strict mode`는 스크립트 단위로 적용된다.
```javascript
<!DOCTYPE html>
<html>
<body>
  <script>
    'use strict';
  </script>
  <script>
    x = 1; // 에러가 발생하지 않는다.
    console.log(x); // 1
  </script>
  <script>
    'use strict';

    y = 1; // ReferenceError: y is not defined
    console.log(y);
  </script>
</body>
</html>
```
위와 같이 스크립트 단위로 적용된 `strict mode`는 다른 스크립트에 영향을 주지 않고 해당 스크립트에 한정되어 적용된다.  

`strict mode` 와 `non-strict mode`를 혼용해서 사용하면 오류를 발생시킬 수 있다. 특히 외부 서드파티 라이브러리를 사용하는 경우 라이브러리가 `non-strict mode`인 경우도 있기 때문에 전역에 `strict mode`를 적용하는 것은 바람직하지 않다.  

즉시 실행 함수의 선두에 `strict mode`를 적용한다.
```javascript
// 즉시 실행 함수의 선두에 strict mode 적용
(function () {
  'use strict';

  // Do something...
}());
```


## 4.함수 단위로 strict mode를 적용하는 것도 피하자
함수 단위로 `strict mode`를 적용하게되면 모든 함수마다 일일이 `strict mode`를 적용해야 하는데 이는 매우 번거로운 일이다.  

그리고 `strict mode`가 적용된 함수가 참조할 함수 외부의 컨텍스트에 `strict mode`를 적용하지 않는다면 문제가 발생할 수 있다.  

```javascript
(function () {
  // non-strict mode
  var lеt = 10; // 에러가 발생하지 않는다.

  function foo() {
    'use strict';

    let = 20; // SyntaxError: Unexpected strict mode reserved word
  }
  foo();
}());
```

## 5.strict mode가 발생시키는 에러
`strict mode`를 적용시켰을 때 발생하는 에러는 다음과 같다.

- 암묵적 전역
- 변수, 함수, 매개변수의 삭제(`delete`)
- 매개변수 이름의 중복
- with문의 사용

### 5.1 암묵적 전역
선언하지 않은 변수를 참조하면 `ReferenceError`가 발생한다.
```javascript
(function () {
  'use strict';

  x = 1;
  console.log(x); // ReferenceError: x is not defined
}());
```

### 5.2 변수, 함수, 매개변수의 삭제
`delete`연산자로 변수, 함수, 매개변수를 삭제하면 `SyntaxError`가 발생한다.  
```javascript
(function () {
  'use strict';

  var x = 1;
  delete x;
  // SyntaxError: Delete of an unqualified identifier in strict mode.

  function foo(a) {
    delete a;
    // SyntaxError: Delete of an unqualified identifier in strict mode.
  }
  delete foo;
  // SyntaxError: Delete of an unqualified identifier in strict mode.
}());
```

### 5.3 매개변수 이름의 중복
중복된 매개변수 이름을 사용하면 `SyntaxError`가 발생한다.
```javascript
(function () {
  'use strict';

  //SyntaxError: Duplicate parameter name not allowed in this context
  function foo(x, x) {
    return x + x;
  }
  console.log(foo(1, 2));
}());
```

### 5.4 with문의 사용
`with`문을 사용하면 `SyntaxError`가 발생한다.  

`with`문은 전달된 객체를 스코프 체인에 추가한다. `with`문은 동일한 객체의 프로퍼티를 반복해서 사용할 때 객체 이름을 생략할 수 있어서 코드가 간단해지는 효과가 있지만 성능과 가독성이 나빠져 사용하지 않는것이 좋다.
```javascript
(function () {
  'use strict';

  // SyntaxError: Strict mode code may not include a with statement
  with({ x: 1 }) {
    console.log(x);
  }
}());
```

## 6.strict mode 적용에 의한 변화
### 6.1 일반 함수의 this
`strict mode`에서 함수를 일반함수로 호출하면 `this`에 `undefined`가 바인딩된다.  

생성자 함수가 아닌 일반 함수 내부에서는 `this`를 사용할 필요가 없기 때문이다.  

```javascript
(function () {
  'use strict';

  function foo() {
    console.log(this); // undefined
  }
  foo();

  function Foo() {
    console.log(this); // Foo
  }
  new Foo();
}());
```
만약 `strict mode`가 아닌 경우 `window`전역 객체가 바인딩된다.  

### 6.2 arguments 객체
`strict mode`에서 매개변수에 전달된 인수를 재할당하여 변경해도 arguments 객체에 반영되지 않는다.  
```javascript
(function (a) {
  'use strict';
  // 매개변수에 전달된 인수를 재할당하여 변경
  a = 2;

  // 변경된 인수가 arguments 객체에 반영되지 않는다.
  console.log(arguments); // { 0: 1, length: 1 }
}(1));
```

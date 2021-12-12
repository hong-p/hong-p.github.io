---
title: "모던 자바스크립트 딥 다이브 24장 클로저"
excerpt: "[javascript] 모던 자바스크립트 Deep Dive 24장 클로저"
categories:
 - Javascript
tags:
 - Javascript
 - Closure
toc: true
toc_sticky: true
last_modified_at: 2021-12-12
---

# 24장 클로저
클로저(`closure`)란? 함수와 그 함수가 선언된 렉시컬 환경과의 조합이다.  

클로저는 자바스크립트 고유의 개념이 아니다. 함수를 일급 객체로 취급하는 함수형 프로그래밍 언에(하스켈`Haskell`, 리스프`Lisp`, 얼랭`Erlag`, 스칼라`Scala`등)에서 사용되는 중요한 특성이다.  

클로저는 자바스크립 고유의 개념이 아니므로 ECMAScript 사양에 등장하지 않는다.  

클로저를 이해하려면 먼저 렉시컬 환경, 즉 렉시컬 스코프를 이해해야 한다.  

아래 두 가지 예제를 비교 해 보자.
```javascript
const x = 1;

function outerFunc() {
  const x = 10;

  function innerFunc() {
    console.log(x); // 10
  }

  innerFunc();
}

outerFunc();
```
```javascript
const x = 1;

function outerFunc() {
  const x = 10;
  innerFunc();
}

function innerFunc() {
  console.log(x); // 1
}

outerFunc();
```
함수의 스코프는 호출하는 위치로 정해지는 것이 아니고, **함수가 선언된 위치**, 즉 **렉시컬 환경**에 따라 결정된다.

## 1.렉시컬 스코프
이미 [13.5절의 렉시컬 스코프](https://hong-p.github.io/javascript/javascript-deepdive-ch13/#5%EB%A0%89%EC%8B%9C%EC%BB%AC-%EC%8A%A4%EC%BD%94%ED%94%84)에서 살펴보았다.  

자바스크립트 엔진은 함수를 어디서 호출하는지가 아니라 **어디에 정의 했는지에 따라 상위 스코프가 결정**된다.  

그리고 객체가 생성되는 시점에 상위 스코프가 결정된다.(`[[Environment]]` 내부 슬롯에 저장)  
이후 함수가 호출되고 함수 몸체 코드가 평가되는 시점에 **외부 렉시컬 환경에 대한 참조**에 객체가 생성될때 저장한 상위 스코프를 참조값을 저장한다.  

> ※ 함수 객체가 생성되는 시점  
> 함수 선언문은 코드(함수 선언문을 포함한) 평가 단계에서 함수 객체 생성  
> 함수 표현식은 코드(함수 표현식을 포함한) 실행 단계에서 함수 객체 생성  

## 2.함수 객체의 내부 슬롯`[[Environment]]`
함수 정의가 평가되어 **함수 객체를 생성할 때** 자신이 정의된 환경(위치)에 의해 결정된 **상위 스코프의 참조**를 함수 객체 자신의 내부 슬롯`[[Environment]]`에 저장한다.  
`[[Environment]]`내부 슬롯에 저장된 상위 스코프의 참조는 현재 실행 중인 실행 컨텍스트의 렉시컬 환경을 가리킨다.  

함수 객체의 `[[Environment]]` 내부 슬롯에 저장된 상위 스코프는 자신이 호출되었을 때 생성될 함수 렉시컬 환경의 **외부 렉시컬 환경에 대한 참조**에 저장될 참조 값이다.  

```javascript
const x = 1;

function foo() {
  const x = 10;

  // 상위 스코프는 함수 정의 환경(위치)에 따라 결정된다.
  // 함수 호출 위치와 상위 스코프는 아무런 관계가 없다.
  bar();
}

// 함수 bar는 자신의 상위 스코프, 즉 전역 렉시컬 환경을 [[Environment]]에 저장하여 기억한다.
function bar() {
  console.log(x);
}

foo(); // ?
bar(); // ?
```

![image](https://user-images.githubusercontent.com/80154058/145714893-fd3be59d-6adf-4dad-b59b-a6643e422fd3.png)


그림의 ①은 전역 코드 평가 시점에서 `foo`와 `bar`함수 객체가 생성되며 각 함수 객체의 내부 슬롯 `[[Environment]]`에 상위 스코프가 저장된다.  

`foo`함수가 호출되면 ② `foo`함수의 렉시컬 환경이 생성되며 외부 렉시컬 환경 환경에 대한 참조에 전역 렉시컬 환경의 참조(함수 객체의 내부 슬롯 `[[Environment]]`에 저장된)가 저장된다.  

`bar`함수가 호출되면 ③ `bar`함수의 렉시컬 환경이 생성되며 외부 렉시컬 환경 환경에 대한 참조에 전역 렉시컬 환경의 참조(함수 객체의 내부 슬롯 `[[Environment]]`에 저장된)가 저장된다.  

## 3.클로저와 렉시컬 환경
외부 함수보다 중첩 함수가 더 오래 유지되는 경우, 중첩 함수는 이미 생명 주기가 종료한 외부 함수의 변수를 참조할 수 있다. 이러한 **중첩 함수**를 **클로저**(`closure`)라고 부른다.  

```javascript
const x = 1;

// ①
function outer() {
  const x = 10;
  const inner = function () { console.log(x); }; // ②
  return inner;
}

// outer 함수를 호출하면 중첩 함수 inner를 반환한다.
// 그리고 outer 함수의 실행 컨텍스트는 실행 컨텍스트 스택에서 팝되어 제거된다.
const innerFunc = outer(); // ③
innerFunc(); // ④ 10
```

③에서 `outer`함수를 호출하면 `outer`함수는 중첩 함수`inner`를 반환하고 생명 주기를 마감한다. 즉, `outer`함수의 실행 컨텍스트는 실행 컨텍스트 스택에서 제거된다.  
이때 `outer`함수의 지역 변수 `x`또한 생명 주기를 마감한다.  

그러나 ④에서 `inner`함수 호출 결과는 `outer`함수의 지역 변수 `x`의 값인 `10`이다. 이미 생명 주기가 종료되어 실행 컨텍스트 스택에서 제거된 `outer`함수의 지역 변수 `x`가 다시 부활이라도 한듯 동작한다.  

1.전역 코드 실행(`outer`객체 생성)  
![image](https://user-images.githubusercontent.com/80154058/145715519-adbe730b-d994-433f-87f5-b302bb1088e1.png)

2.`outer`함수 호출(`inner`객체 생성 및 반환)  
![image](https://user-images.githubusercontent.com/80154058/145715630-383b0fbf-8fb5-445e-b3de-7948722138b1.png)

3.`innerFunc` 전역 변수에 `inner`함수 참조 할당  

2번에서 `outer`함수 호출되고 `outer`함수의 실행이 종료되어 `outer`함수의 실행 컨텍스트는 실행 컨텍스트 스택에서 제거되었지만, `outer`함수의 렉시컬 환경까지 소멸하는 것은 아니다.  

다른 누군가 해당 함수를 계속 참조하고 있다면 가비지 컬렉션의 대상이 되지 않기 때문이다.
![image](https://user-images.githubusercontent.com/80154058/145715680-f2f08ce5-eaa1-413c-8c0f-83876316eac5.png)

4\.`innerFunc`호출로 `inner`함수의 렉시컬 환경 생성(`inner`함수 객체의 `[[Environment]]`내부 슬롯 참조)  
![image](https://user-images.githubusercontent.com/80154058/145715821-6104d2bc-25ae-42e1-ada3-69f968067728.png)

```html
<!DOCTYPE html>
<html>
<body>
  <script>
    function foo() {
      const x = 1;
      const y = 2;

      // 일반적으로 클로저라고 하지 않는다.
      function bar() {
        const z = 3;

        debugger;
        // 상위 스코프의 식별자를 참조하지 않는다.
        console.log(z);
      }

      return bar;
    }

    const bar = foo();
    bar();
  </script>
</body>
</html>
```
상위 스코프의 식별자`x`, `y`를 하나도 참조하지 않아 일반적으로 클로저로 보지 않는다.  

![image](https://user-images.githubusercontent.com/80154058/145716224-09b89e48-2a4e-4c40-82ac-5078a01767d8.png)

```html
<!DOCTYPE html>
<html>
<body>
  <script>
    function foo() {
      const x = 1;

      // 일반적으로 클로저라고 하지 않는다.
      // bar 함수는 클로저였지만 곧바로 소멸한다.
      function bar() {
        debugger;
        // 상위 스코프의 식별자를 참조한다.
        console.log(x);
      }
      bar();
    }

    foo();
  </script>
</body>
</html>
```
상위 스코프의 식별자인 `x`를 참조하기 때문에 클로저로 본다.  

![image](https://user-images.githubusercontent.com/80154058/145716267-8be51f68-3cea-453e-9c07-ead18b35dda0.png)

## 4.클로저의 활용
클로저는 상태`state`를 안전하게 변경하고 유지하기 위해 사용된다.  

상태를 안전하게 은닉(`information hiding`)하고 특정 함수에게만 상태 변경을 허용하기 위해 사용된다.  

1.전역 변수로 관리  
```javascript
// 카운트 상태 변수
let num = 0;

// 카운트 상태 변경 함수
const increase = function () {
  // 카운트 상태를 1만큼 증가 시킨다.
  return ++num;
};

console.log(increase()); // 1
console.log(increase()); // 2
console.log(increase()); // 3
```

2.함수의 지역 변수로 관리  
```javascript
// 카운트 상태 변경 함수
const increase = function () {
  // 카운트 상태 변수
  let num = 0;

  // 카운트 상태를 1만큼 증가 시킨다.
  return ++num;
};

// 이전 상태를 유지하지 못한다.
console.log(increase()); // 1
console.log(increase()); // 1
console.log(increase()); // 1
```

3.클로저 활용  
```javascript
// 카운트 상태 변경 함수
const increase = (function () {
  // 카운트 상태 변수
  let num = 0;

  // 클로저
  return function () {
    // 카운트 상태를 1만큼 증가 시킨다.
    return ++num;
  };
}());

console.log(increase()); // 1
console.log(increase()); // 2
console.log(increase()); // 3
```

클로저로 `num`의 상태`state`를 은닉하여 의도치 않게 변경되지 않도록 안전하게 관리하고 특정 함수에게만 상태 변경을 허용하여 상태를 안전하게 변경하고 유지한다.  

4.감소 기능 추가  
```javascript
const counter = (function () {
  // 카운트 상태 변수
  let num = 0;

  // 클로저인 메서드를 갖는 객체를 반환한다.
  // 객체 리터럴은 스코프를 만들지 않는다.
  // 따라서 아래 메서드들의 상위 스코프는 즉시 실행 함수의 렉시컬 환경이다.
  return {
    // num: 0, // 프로퍼티는 public하므로 은닉되지 않는다.
    increase() {
      return ++num;
    },
    decrease() {
      return num > 0 ? --num : 0;
    }
  };
}());

console.log(counter.increase()); // 1
console.log(counter.increase()); // 2

console.log(counter.decrease()); // 1
console.log(counter.decrease()); // 0
```

5.생성자 함수로 표현  
`increase`, `decrease` 메서드는 프로토타입 메서드이다.  
이 메서드들이 평가되어 함수 객체가 생성될 때 실행 중인 실행 컨텍스트는 **즉시 실행 함수의 실행 컨텍스트**이다.  따라서 `increase`, `decrease`메서드는 즉시 실행 함수의 실행 컨텍스트의 렉시컬 환경을 기억하는 클로저 이다.  

다시 말하면 `num`변수의 값은 `increase`와 `decrease`메서드만이 변경할 수 있다.  

```javascript
const Counter = (function () {
  // ① 카운트 상태 변수
  let num = 0;

  function Counter() {
    // this.num = 0; // ② 프로퍼티는 public하므로 은닉되지 않는다.
  }

  Counter.prototype.increase = function () {
    return ++num;
  };

  Counter.prototype.decrease = function () {
    return num > 0 ? --num : 0;
  };

  return Counter;
}());

const counter = new Counter();

console.log(counter.increase()); // 1
console.log(counter.increase()); // 2

console.log(counter.decrease()); // 1
console.log(counter.decrease()); // 0
```

6.고차 함수 활용한 클로저  
> ※ 고차 함수란  
> 보조 함수를 인자로 전달받고 함수를 반환하는 함수

```javascript
// 함수를 인수로 전달받고 함수를 반환하는 고차 함수
// 이 함수는 카운트 상태를 유지하기 위한 자유 변수 counter를 기억하는 클로저를 반환한다.
function makeCounter(aux) {
  // 카운트 상태를 유지하기 위한 자유 변수
  let counter = 0;

  // 클로저를 반환
  return function () {
    // 인수로 전달 받은 보조 함수에 상태 변경을 위임한다.
    counter = aux(counter);
    return counter;
  };
}

// 보조 함수
function increase(n) {
  return ++n;
}

// 보조 함수
function decrease(n) {
  return --n;
}

// 함수로 함수를 생성한다.
// makeCounter 함수는 보조 함수를 인수로 전달받아 함수를 반환한다
const increaser = makeCounter(increase); // ①
console.log(increaser()); // 1
console.log(increaser()); // 2

// increaser 함수와는 별개의 독립된 렉시컬 환경을 갖기 때문에 카운터 상태가 연동하지 않는다.
const decreaser = makeCounter(decrease); // ②
console.log(decreaser()); // -1
console.log(decreaser()); // -2
```
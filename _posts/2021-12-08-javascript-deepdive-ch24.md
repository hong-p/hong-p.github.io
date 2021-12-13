---
title: "모던 자바스크립트 딥 다이브 24장 클로저"
excerpt: "[javascript] 모던 자바스크립트 Deep Dive 24장 클로저"
categories:
 - Javascript
tags:
 - Javascript
 - Closure
 - Array.from
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

클로저에 의해 참조되는 상위 스코프의 변수 (`foo`함수의 `x`변수)를 **자유 변수**(`free variable`)라고 부른다.  

클로저란 함수가 자유 변수에 대해 닫혀있다(`closed`)라는 의미이다. "자유 변수에 묶여있는 함수"라고 할 수 있다.  

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

`const increaser = makeCounter(increase); // ①`에서  
`makeCounter`가 처음 호출되었을 때  
![image](https://user-images.githubusercontent.com/80154058/145717064-1287ba78-003a-4ea5-ac2d-1ccc5d4750b5.png)

`const decreaser = makeCounter(decrease); // ②`에서  
`makeCounter`가 두 번째 호출되었을 때  
![image](https://user-images.githubusercontent.com/80154058/145717080-118b25d1-261d-4b17-bba3-b91816c2a228.png)

즉 `makeCounter`함수를 호출할 때 마다 새로운 독립된 렉시컬 환경을 갖기 때문에 이런 경우 자유 변수 `counter`를 공유하지 못해 카운터의 증감이 연동되지 않는다.  

아래 예제처럼 해야 한다.
```javascript
// 함수를 반환하는 고차 함수
// 이 함수는 카운트 상태를 유지하기 위한 자유 변수 counter를 기억하는 클로저를 반환한다.
const counter = (function () {
  // 카운트 상태를 유지하기 위한 자유 변수
  let counter = 0;

  // 함수를 인수로 전달받는 클로저를 반환
  return function (aux) {
    // 인수로 전달 받은 보조 함수에 상태 변경을 위임한다.
    counter = aux(counter);
    return counter;
  };
}());

// 보조 함수
function increase(n) {
  return ++n;
}

// 보조 함수
function decrease(n) {
  return --n;
}

// 보조 함수를 전달하여 호출
console.log(counter(increase)); // 1
console.log(counter(increase)); // 2

// 자유 변수를 공유한다.
console.log(counter(decrease)); // 1
console.log(counter(decrease)); // 0
```

## 5.캡슐화와 정보 은닉
**캡슐화**(`encapsulation`)는 객체의 상태(`state`)를 나타내는 프로퍼티와 프로퍼티를 참조하고 조작할 수 있는 동작인 메서드를 하나로 묶는 것을 말한다.  

**캡슐화**는 객체의 특정 프로퍼티나 메서드를 감출 목적으로 사용하기도 하는데 이를 **정보 은닉**(`information hiding`)이라 한다.  

**정보 은닉**은 외부에 공개할 필요가 없는 구현의 일부를 외부에 공개되지 않도록 감추어 적절치 못한 접근으로부터 객체의 상태가 변경되는 것을 방지해 **정보를 보호**하고, **객체 간의 상호 의존성**, 즉 **결합도**(`coupling`)를 **낮추는 효과**가 있다.  

대부분의 객체지향 프로그래밍 언어는 `public`, `private`, `protected`와 같은 접근 제한자(`access modifier`)를 제공하지만 자바스크립트는 제공하지 않는다. 즉 모든 프로퍼티와 메서드는 `public`이다.  

**1.인스턴스 메서드에서 지역 변수 참조**  
```javascript
function Person(name, age) {
  this.name = name; // public
  let _age = age;   // private

  // 인스턴스 메서드
  this.sayHi = function () {
    console.log(`Hi! My name is ${this.name}. I am ${_age}.`);
  };
}

const me = new Person('Lee', 20);
me.sayHi(); // Hi! My name is Lee. I am 20.
console.log(me.name); // Lee
console.log(me._age); // undefined

const you = new Person('Kim', 30);
you.sayHi(); // Hi! My name is Kim. I am 30.
console.log(you.name); // Kim
console.log(you._age); // undefined
```

**2.프로토타입 메서드에서 지역 변수 참조**  
프로토타입 메서드 내에서는 지역 변수인 `_age`를 참조할 수 없다.
```javascript
function Person(name, age) {
  this.name = name; // public
  let _age = age;   // private
}

// 프로토타입 메서드
Person.prototype.sayHi = function () {
  // Person 생성자 함수의 지역 변수 _age를 참조할 수 없다
  console.log(`Hi! My name is ${this.name}. I am ${_age}.`);
};
```

**3.프로토타입 메서드를 클로저로 활용**  
즉시 실행 함수로 감싸서 그 안에 지역 변수 `_age`를 선언하고 그 안에 `Person`생성자 함수와 프로토타입 메서드를 선언하고 생성자 함수를 반환하도록 한다.  

그렇게되면 즉시 실행 함수는 종료되었지만 반환된 `Person`생성자 함수는 여전히 지역 변수`_age`를 참조할 수 있는 클로저이다.
```javascript
const Person = (function () {
  let _age = 0; // private

  // 생성자 함수
  function Person(name, age) {
    this.name = name; // public
    _age = age;
  }

  // 프로토타입 메서드
  Person.prototype.sayHi = function () {
    console.log(`Hi! My name is ${this.name}. I am ${_age}.`);
  };

  // 생성자 함수를 반환
  return Person;
}());

const me = new Person('Lee', 20);
me.sayHi(); // Hi! My name is Lee. I am 20.
console.log(me.name); // Lee
console.log(me._age); // undefined

const you = new Person('Kim', 30);
you.sayHi(); // Hi! My name is Kim. I am 30.
console.log(you.name); // Kim
console.log(you._age); // undefined
```

하지만 위 예제도 완벽하진 않다.  
`Person`생성자 함수가 여러 개의 인스턴스를 생성할 경우 `_age`변수의 상태가 유지되지 않는다.
```javascript
const me = new Person('Lee', 20);
me.sayHi(); // Hi! My name is Lee. I am 20.

const you = new Person('Kim', 30);
you.sayHi(); // Hi! My name is Kim. I am 30.

// _age 변수 값이 변경된다!
me.sayHi(); // Hi! My name is Lee. I am 30.
```

## 6.자주 발생하는 실수
`for`문을 사용할 때 `for`문 내부에 함수를 선언하는 경우 기대하지 않은 결과가 나타난다.
```javascript
var funcs = [];

for (var i = 0; i < 3; i++) {
  funcs[i] = function () { return i; }; // ①
}

for (var j = 0; j < funcs.length; j++) {
  console.log(funcs[j]()); // ②
}
```
`0 1 2`가 출력될 것을 기대하였지만 `3 3 3`이 출력된다.  
이유는 `var`로 선언된 `i`변수는 전역 변수로 선언되었기 때문에 함수 선언문에 `i`값을 전달하였더라도, 호출하는 시점에는 `i`값이 `3`이기 때문에 `3 3 3`이 출력된다.  

위 예제를 클로저를 사용해 바르게 고치면 아래와 같다.
```javascript
var funcs = [];

for (var i = 0; i < 3; i++){
  funcs[i] = (function (id) { // ①
    return function () {
      return id;
    };
  }(i));
}

for (var j = 0; j < funcs.length; j++) {
  console.log(funcs[j]());
}
```
`for`문 내부에 함수를 선언할 때 즉시 실행 함수로 감싸고 `i`값을 전달해 주도록 수정하였다.  
이렇게 하면 새로운 함수 레벨 스코프가 반복문을 반복하면서 계속 생겨 각각의 스코프에 `i`값이 `id` 매개변수에 저장되게 된다.  

즉 `id`는 즉시 실행 함수가 반환한 중첩 함수에 묶여있는 자유 변수가 되어 그 값이 유지된다.  

위 예제 보다 더욱 간단하게 하는 방법은 **ES6의** `let`**키워드를 사용하는 것**이다.
```javascript
const funcs = [];

for (let i = 0; i < 3; i++) {
  funcs[i] = function () { return i; };
}

for (let i = 0; i < funcs.length; i++) {
  console.log(funcs[i]()); // 0 1 2
```
`let` 키워드는 블록 레벨 스코프를 따르기 때문에 `for`문이 반복될 때 마다 새로운 렉시컬 환경이 생성된다.
![image](https://user-images.githubusercontent.com/80154058/145822703-2df5655e-3803-4aa5-80f8-54cc91246cd1.png)

※ 반복문의 코드 블록 내부에서 함수를 정의할 때에만 의미가 있다.  
반복문의 코드 블록 내부에 함수 정의가 없는 경우 참조할 대상이 없기 때문에 가비지 컬렉션의 대상이 된다. (따라서 함수 정의가 없는 경우 그냥 `var`를 사용하는 것이 메모리 관점에서는 좋으려나?)  

**함수형 프로그래밍 기법인 고차 함수를 사용하는 방법도 있다.**
```javascript
// 요소가 3개인 배열을 생성하고 배열의 인덱스를 반환하는 함수를 요소로 추가한다.
// 배열의 요소로 추가된 함수들은 모두 클로저다.
const funcs = Array.from(new Array(3), (_, i) => () => i); // (3) [ƒ, ƒ, ƒ]

// 배열의 요소로 추가된 함수 들을 순차적으로 호출한다.
funcs.forEach(f => console.log(f())); // 0 1 2
```
[MDN Array.from](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/from)을 참고하자  

![image](https://user-images.githubusercontent.com/80154058/145823600-72cd6e18-51aa-4e3d-bf24-831e875719c9.png)

`Array.from`메서드의 첫 번째 파라미터에는 `iterable` 혹은 유사배열이 오고, 두 번째 파라미터에는 `callback`함수를 넣어준다.  
`callback`함수를 통해 배열을 하나씩 순회하면서 반환 값으로 새로운 배열을 반환한다.  
`callback`함수의 첫 번째 파라미터는 `element`값, 두 번째 파라미터는 `index`이다.
```javascript
Array.from([1,2,3], (v,i)=>v*2)
// [2, 4, 6]

// 'foo'는 유사배열
Array.from('foo', (v,i)=>v)
// ['f', 'o', 'o']

Array.from('foo', (v,i)=>v+i)
// ['f0', 'o1', 'o2']

// 함수를 반환
Array.from('foo', (v,i)=>()=>v)
// [ƒ, ƒ, ƒ]
```

```javascript
Array.from(new Array(3), (_, i) => () => i)
```
따라서 위 예제 `_`는 `element`이고 `i`는 `index`이고, 각 배열을 순회하면서 `()=>i` 함수를 할당한 것이고, `i`값은 클로저로 인해 `0`,`1`,`2`값이 저장되어 있는 것이다.  

결국 아래와 같이 배열의 각 요소에 할당된 함수를 호출하면 `0 1 2`가 출력되는 것이다.
```javascript
// 배열의 요소로 추가된 함수 들을 순차적으로 호출한다.
funcs.forEach(f => console.log(f())); // 0 1 2
```

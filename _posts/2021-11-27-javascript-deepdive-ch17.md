---
title: "모던 자바스크립트 딥 다이브 17장 생성자 함수에 의한 객체 생성"
excerpt: "[javascript] 모던 자바스크립트 Deep Dive 17장 생성자 함수에 의한 객체 생성"
categories:
 - Javascript
tags:
 - Javascript
toc: true
toc_sticky: true
last_modified_at: 2021-11-27
---

# 17장 생성자 함수에 의한 객체 생성
**객체 리터럴을 사용하여 객체를 생성하는 방식**과 **생성자 함수를 사용하여 객체를 생성하는 방식**과의 장단점을 살펴보자.

## 1.Object 생성자 함수
`new`연산자와 함께 `Object`생성자 함수를 호출하면 빈 객체를 생성하여 반환한다. 빈 객체에 프로퍼티나 메서드를 추가할 수 있다.
```javascript
// 빈 객체의 생성
const person = new Object();

// 프로퍼티 추가
person.name = 'Lee';
person.sayHello = function () {
  console.log('Hi! My name is ' + this.name);
};

console.log(person); // {name: "Lee", sayHello: ƒ}
person.sayHello(); // Hi! My name is Lee
```

>*※ 생성자 함수(constructor)*  
new 연산자와 함께 호출하여 객체(인스턴스)를 생성하는 함수.  
생성자 함수에 의해 생성된 객체를 인스턴스(instance)라고 한다.  
Object 생성자 함수 외에도 String, Number, Boolean, Function, Array, Date, RegExp, Promise등의 빌트인 생성자 함수를 제공한다.
```javascript
// String 생성자 함수에 의한 String 객체 생성
const strObj = new String('Lee');
console.log(typeof strObj); // object
console.log(strObj);        // String {"Lee"}

// Number 생성자 함수에 의한 Number 객체 생성
const numObj = new Number(123);
console.log(typeof numObj); // object
console.log(numObj);        // Number {123}

// Boolean 생성자 함수에 의한 Boolean 객체 생성
const boolObj= new Boolean(true);
console.log(typeof boolObj); // object
console.log(boolObj);        // Boolean {true}

// Function 생성자 함수에 의한 Function 객체(함수) 생성
const func = new Function('x', 'return x * x');
console.log(typeof func); // function
console.dir(func);        // ƒ anonymous(x)

// Array 생성자 함수에 의한 Array 객체(배열) 생성
const arr = new Array(1, 2, 3);
console.log(typeof arr); // object
console.log(arr);        // [1, 2, 3]

// RegExp 생성자 함수에 의한 RegExp 객체(정규 표현식) 생성
const regExp = new RegExp(/ab+c/i);
console.log(typeof regExp); // object
console.log(regExp);        // /ab+c/i

// Date 생성자 함수에 의한 Date 객체 생성
const date = new Date();
console.log(typeof date); // object
console.log(date);        // Mon May 04 2020 08:36:33 GMT+0900 (대한민국 표준시)
```
Object 생성자 함수보다 [객체 리터럴](https://hong-p.github.io/javascript/javascript-deepdive-ch10/#2%EA%B0%9D%EC%B2%B4-%EB%A6%AC%ED%84%B0%EB%9F%B4%EC%97%90-%EC%9D%98%ED%95%9C-%EA%B0%9D%EC%B2%B4-%EC%83%9D%EC%84%B1)을 사용하는 것이 더 간편한다.

## 2.생성자 함수
### 2.1 객체 리터럴에 의한 객체 생성 방식의 문제점
객체 리터럴에 의한 객체 생성 방식은 직관적이고 간편하다. 하지만 단 하나의 객체만 생성할 수 있다.  
따라서 동일한 프로퍼티를 갖는 객체를 여러개 생성해야 하는 경우 객체 리터럴로 생성하는 방식은 비효율 적이다.

```javascript
const circle1 = {
  radius: 5,
  getDiameter() {
    return 2 * this.radius;
  }
};

console.log(circle1.getDiameter()); // 10

const circle2 = {
  radius: 10,
  getDiameter() {
    return 2 * this.radius;
  }
};

console.log(circle2.getDiameter()); // 20
```

### 2.2 생성자 함수에 의한 객체 생성 방식의 장점
생성자 함수를 사용하면 템플릿(클래스)처럼 프로퍼티가 동일한 구조의 객체를 여러개 생성할 수 있다. 이런 경우 생성자 함수를 사용하는 것이 효율적이다.
```javascript
// 생성자 함수
function Circle(radius) {
  // 생성자 함수 내부의 this는 생성자 함수가 생성할 인스턴스를 가리킨다.
  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };
}

// 인스턴스의 생성
const circle1 = new Circle(5);  // 반지름이 5인 Circle 객체를 생성
const circle2 = new Circle(10); // 반지름이 10인 Circle 객체를 생성

console.log(circle1.getDiameter()); // 10
console.log(circle2.getDiameter()); // 20
```
<br>

*※ this*  
`this`는 객체 자신의 프로퍼티나 메서드를 참조하기 위한 자기 참조 변수(self-referencing variable)다. this가 가리키는 값, 즉 this바인딩은 함수 호출 방식에 따라 동적으로 결정된다.

함수 호출 방식 | `this`가 가리키는 값(`this` 바인딩)
:---|:---
일반 함수로서 호출|전역 객체
메서드로서 호출|메서드를 호출한 객체(마침표 앞의 객체)
생성자 함수로서 호출|생성자 함수가 생성할 인스턴스

```javascript
// 함수는 다양한 방식으로 호출될 수 있다.
function foo() {
  console.log(this);
}

// 일반적인 함수로서 호출
// 전역 객체는 브라우저 환경에서는 window, Node.js 환경에서는 global을 가리킨다.
foo(); // window

// 메서드로서 호출
const obj = { foo }; // ES6 프로퍼티 축약 표현
obj.foo(); // obj => {foo: f}

// 생성자 함수로서 호출
const inst = new foo(); // inst => foo {}
```

<br>

생성자 함수는 객체를 생성하는 함수다. 하지만 `java`와 같은 클래스 기반 객체지향 언어의 생성자와는 다르게 그 형식이 정해져 있는 것이 아니라 **일반 함수와 동일한 방법으로 생성자 함수를 정의**하고 `new`**연산자와 함께 호출하면 해당 함수는 생성자 함수로 동작**한다.

```javascript
// 생성자 함수
function Circle(radius) {
  // 생성자 함수 내부의 this는 생성자 함수가 생성할 인스턴스를 가리킨다.
  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };
}
// new 연산자와 함께 호출하지 않으면 생성자 함수로 동작하지 않는다.
// 즉, 일반 함수로서 호출된다.
const circle3 = Circle(15);

// 일반 함수로서 호출된 Circle은 반환문이 없으므로 암묵적으로 undefined를 반환한다.
console.log(circle3); // undefined

// 일반 함수로서 호출된 Circle내의 this는 전역 객체를 가리킨다.
console.log(radius); // 15
```

### 2.3 생성자 함수의 인스턴스 생성 과정
생성자 함수의 역할은 인스턴스를 생성하는 것과 생성된 인스턴스를 초기화 하는 것이다.  

`new`연산자와 함께 생성자 함수를 호출하면 다음과 같은 과정을 거쳐 암묵적으로 인스턴스를 생성하고 초기화한뒤 반환한다.

**1.인스턴스 생성과 this 바인딩**  
암묵적으로 빈 객체가 생성된다. 그리고 암묵적으로 생성된 빈 객체는 `this`에 바인딩된다.  
이 처리는 함수 몸체의 코드가 한 줄씩 실행되는 런타임 이전에 실행된다.
```javascript
function Circle(radius) {
  // 1. 암묵적으로 빈 객체가 생성되고 this에 바인딩된다.
  console.log(this); // Circle {}

  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };
}
```

**2.인스턴스 초기화**   
생성자 함수에 기술되어 있는 코드가 한 줄씩 실행되어 `this`에 바인딩되어 있는 인스턴스를 초기화한다.  
인스턴스에 프로퍼티나 메서드를 추가하고 생성자 함수가 인수로 전달받은 초기값을 할당한다.
```javascript
function Circle(radius) {
  // 1. 암묵적으로 인스턴스가 생성되고 this에 바인딩된다.

  // 2. this에 바인딩되어 있는 인스턴스를 초기화한다.
  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };
}
```

**3.인스턴스 반환**  
생성자 함수 내부의 모든 처리가 끝나면 완성된 인스턴스가 바인딩된 `this`가 암묵적으로 반환된다.
```javascript
function Circle(radius) {
  // 1. 암묵적으로 인스턴스가 생성되고 this에 바인딩된다.

  // 2. this에 바인딩되어 있는 인스턴스를 초기화한다.
  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };

  // 3. 완성된 인스턴스가 바인딩된 this가 암묵적으로 반환된다
}

// 인스턴스 생성. Circle 생성자 함수는 암묵적으로 this를 반환한다.
const circle = new Circle(1);
console.log(circle); // Circle {radius: 1, getDiameter: ƒ}
```

<br>

만약 `this`가 아닌 다른 객체를 명시적으로 반환하면 `this`가 반환되지 못하고 `return`문에 명시한 객체가 반환된다.
```javascript
function Circle(radius) {
  // 1. 암묵적으로 인스턴스가 생성되고 this에 바인딩된다.

  // 2. this에 바인딩되어 있는 인스턴스를 초기화한다.
  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };

  // 3. 암묵적으로 this를 반환한다.
  // 명시적으로 객체를 반환하면 암묵적인 this 반환이 무시된다.
  return {};
}

// 인스턴스 생성. Circle 생성자 함수는 명시적으로 반환한 객체를 반환한다.
const circle = new Circle(1);
console.log(circle); // {}
```
`return {};`으로 인해 다른 객체가 반환된다.  

하지만 명시적으로 원시 값을 반환하면 원시 값 반환은 무시되고 암묵적으로 `this`가 반환된다.
```javascript
function Circle(radius) {
  // 1. 암묵적으로 인스턴스가 생성되고 this에 바인딩된다.

  // 2. this에 바인딩되어 있는 인스턴스를 초기화한다.
  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };

  // 3. 암묵적으로 this를 반환한다.
  // 명시적으로 원시값을 반환하면 원시값 반환은 무시되고 암묵적으로 this가 반환된다.
  return 100;
}

// 인스턴스 생성. Circle 생성자 함수는 명시적으로 반환한 객체를 반환한다.
const circle = new Circle(1);
console.log(circle); // Circle {radius: 1, getDiameter: ƒ}
```
`return 100;`이 있어도 원시값이라 무시되고 `this`객체가 리턴된다.

### 2.4 내부 메서드 `[[Call]]`과 `[[Construct]]`
함수는 일반함수로도 생성자 함수로도 호출할 수 있다.  
함수는 객체이므로 일반객체와 동일하게 동작할 수 있다. 함수 객체는 일반 객체가 가지고 있는 내부 슬롯과 내부 메서드를 모두 가지고 있기 때문이다.  
```javascript
// 함수는 객체다.
function foo() {}

// 함수는 객체이므로 프로퍼티를 소유할 수 있다.
foo.prop = 10;

// 함수는 객체이므로 메서드를 소유할 수 있다.
foo.method = function () {
  console.log(this.prop);
};

foo.method(); // 10
```

함수는 객체이지만 일반 객체와는 다르다.  
**일반 객체는 호출할 수 없지만 함수는 호출할 수 있다.**  

함수가 일반 함수로서 호출되면 `[[Call]]`이 호출되고, `new`연산자와 함께 생성자 함수로서 호출되면 `[[Construct]]`가 호출된다.
```javascript
function foo() {}

// 일반적인 함수로서 호출: [[Call]]이 호출된다.
foo();

// 생성자 함수로서 호출: [[Construct]]가 호출된다.
new foo();
```

내부 메서드|함수 객체
:---:|:---:|
`[[Call]]` O | `callable`
`[[Construct]]` O | `constructor`
`[[Construct]]` X | `non-constructor`

함수 객체는 반드시 `callable`이어야 한다. 따라서 모든 함수 객체는 내부 메서드 `[[Call]]`을 갖고 있으므로 호출할 수 있다.  
하지만 모든 함수 객체가 `[[Construct]]`를 갖는 것은 아니다. 함수 객체는 `constructor`일 수도 있고 `non-constructor`일 수도 있다.

### 2.5 constructor와 non-constructor의 구분
`non-constructor`는 `[[Construct]]` 내부 메서드가 없는 함수 객체 즉, `new`연산자와 함께 생성자 함수로 호출할 수 없는 함수 객체이다.

구분 | 예
:---:|:---
`constructor`|함수 선언문, 함수 표현식, 클래스(클래스도 함수)
`non-constructor`|메서드(ES6 메서드 축약 표현), 화살표 함수

```javascript
// 일반 함수 정의: 함수 선언문, 함수 표현식
function foo() {}
const bar = function () {};
// 프로퍼티 x의 값으로 할당된 것은 일반 함수로 정의된 함수다. 이는 메서드로 인정하지 않는다.
const baz = {
  x: function () {}
};

// 일반 함수로 정의된 함수만이 constructor이다.
new foo();   // -> foo {}
new bar();   // -> bar {}
new baz.x(); // -> x {}

// 화살표 함수 정의
const arrow = () => {};

new arrow(); // TypeError: arrow is not a constructor

// 메서드 정의: ES6의 메서드 축약 표현만을 메서드로 인정한다.
const obj = {
  x() {}
};

new obj.x(); // TypeError: obj.x is not a constructor
```

### 2.6 new 연산자
일반 함수와 생성자 함수에 특별한 형식적 차이는 없다. `new`연산자와 함께 함수를 호출하면 해당 함수는 생성자 함수로 동작한다.  
함수 객체의 내부 메서드 `[[Call]]`이 호출되는 것이 아니라 `[[Construct]]`가 호출된다. 단 함수는 `constructor`이어야 한다. `non-constructor`는 안된다.
```javascript
// 생성자 함수로서 정의하지 않은 일반 함수
function add(x, y) {
  return x + y;
}

// 생성자 함수로서 정의하지 않은 일반 함수를 new 연산자와 함께 호출
let inst = new add();
// 함수가 객체를 반환하지 않았으므로 반환문이 무시된다. 따라서 빈 객체가 생성되어 반환된다.
console.log(inst); // {}

// 객체를 반환하는 일반 함수
function createUser(name, role) {
  return { name, role };
}

// 생성자 함수로서 정의하지 않은 일반 함수를 new 연산자와 함께 호출
inst = new createUser('Lee', 'admin');
// 함수가 생성한 객체를 반환한다.
console.log(inst); // {name: "Lee", role: "admin"}
```

반대로 `new`연산자 없이 호출하면 일반 함수로 호출된다.
```javascript
// 생성자 함수
function Circle(radius) {
  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };
}

// new 연산자 없이 생성자 함수 호출하면 일반 함수로서 호출된다.
const circle = Circle(5);
console.log(circle); // undefined

// 일반 함수 내부의 this는 전역 객체 window를 가리킨다.
console.log(radius); // 5
console.log(getDiameter()); // 10

circle.getDiameter();
// TypeError: Cannot read property 'getDiameter' of undefined
```

`Circle`함수를 `new`연산자와 함께 생성자 함수로서 호출하면 함수 내부의 `this`는 `Circle` 인스턴스를 가리킨다.  
하지만 `Circle`함수를 일반 함수로 호출하면 함수 내부의 `this`는 `window`를 가리킨다.

### 2.7 new.target
ES6에서는 `new.target`을 지원한다.   
생성자 함수가 `new`연산자 없이 호출되는 것을 방지하기 위해 파스칼 케이스 컨벤션을 사용하지만, 그래도 실수는 발생할 수 있다.  

`new.target`연산자는 `this`와 유사하게 `constructor`인 모든 함수 내부에서 암묵적인 지역변수와 같이 사용되며 **메타 프로퍼티**라고 부른다.(IE는 지원안함)  

`new`연산자와 함께 생성자 함수로서 호출되면 함수 내부의 `new.target`은 함수 자신을 가리킨다. `new`연산자 없이 일반 함수로 호출되면 `undefined`를 반환한다.
```javascript
// 생성자 함수
function Circle(radius) {
  // 이 함수가 new 연산자와 함께 호출되지 않았다면 new.target은 undefined다.
  if (!new.target) {
    // new 연산자와 함께 생성자 함수를 재귀 호출하여 생성된 인스턴스를 반환한다.
    return new Circle(radius);
  }

  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };
}

// new 연산자 없이 생성자 함수를 호출하여도 new.target을 통해 생성자 함수로서 호출된다.
const circle = Circle(5);
console.log(circle.getDiameter());
```

※ 스코프 세이프 생정자 패턴`scope-safe constructor`
`new.target`을 사용할 수 없는 상황이라면 스코프 세이프 생성자 패턴을 사용할 수 있다.
`instanceof` 연산자를 활용한다.
```javascript
// Scope-Safe Constructor Pattern
function Circle(radius) {
  // 생성자 함수가 new 연산자와 함께 호출되면 함수의 선두에서 빈 객체를 생성하고
  // this에 바인딩한다. 이때 this와 Circle은 프로토타입에 의해 연결된다.

  // 이 함수가 new 연산자와 함께 호출되지 않았다면 이 시점의 this는 전역 객체 window를 가리킨다.
  // 즉, this와 Circle은 프로토타입에 의해 연결되지 않는다.
  if (!(this instanceof Circle)) {
    // new 연산자와 함께 호출하여 생성된 인스턴스를 반환한다.
    return new Circle(radius);
  }

  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };
}

// new 연산자 없이 생성자 함수를 호출하여도 생성자 함수로서 호출된다.
const circle = Circle(5);
console.log(circle.getDiameter()); // 10
```

<br>

※ 빌트인 생성자 함수
참고로 대부분의 빌트인 생성자 함수(`Object`, `String`, `Number`, `Boolean`, `Function`, `Array`, `Date`, `RegExp`, `Promise`등)는 `new`연산자와 함께 호출되었는지를 확인한 후 적절한 값을 반환한다.  
예를들어 `Object`와 `Function`함수는 `new`연산자 없이 호출해도 `new`연산자와 함께 호출한 경우와 동일하게 동작한다.  

`Object`, `Function`
```javascript
let obj = new Object();
console.log(obj); // {}

obj = Object();
console.log(obj); // {}

let f = new Function('x', 'return x ** x');
console.log(f); // ƒ anonymous(x) { return x ** x }

f = Function('x', 'return x ** x');
console.log(f); // ƒ anonymous(x) { return x ** x }
```

반면 `String`, `Number`, `Boolean`은 `new`연산자와 함께 호출했을 때 객체를 생성하여 반환하지만,  
`new`연산자 없이 호출하면 문자열, 숫자, 불리언 원시 값을 반환한다.  

`String`, `Number`, `Boolean`
```javascript
const str = String(123);
console.log(str, typeof str); // 123 string

const num = Number('123');
console.log(num, typeof num); // 123 number

const bool = Boolean('true');
console.log(bool, typeof bool); // true boolean
```

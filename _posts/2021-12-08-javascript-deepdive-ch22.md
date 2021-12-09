---
title: "모던 자바스크립트 딥 다이브 22장 this"
excerpt: "[javascript] 모던 자바스크립트 Deep Dive 22장 this"
categories:
 - Javascript
tags:
 - Javascript
toc: true
toc_sticky: true
last_modified_at: 2021-12-08
---

# 22장 this

## 1.this 키워드
[19.1절 객체지향 프로그래밍](https://hong-p.github.io/javascript/javascript-deepdive-ch19/#1-%EA%B0%9D%EC%B2%B4-%EC%A7%80%ED%96%A5-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D)에서 봤듯이 객체는 상태(`state`)를 나타내 프로퍼티와 동작(`behavior`)를 나타내는 메서드를 하나의 논리적인 단위로 묶은 복합적인 자료구조다.  

동작을 나타내는 메서드는 자신이 속한 객체의 상태, 즉 프로퍼티를 참조하고 변경할 수 있어야 한다. 이때 자신이 속한 객체를 가리키는 식별자를 참조할 수 있어야 하는데, 이때 `this`를 사용한다.  

```javascript
const circle = {
  // 프로퍼티: 객체 고유의 상태 데이터
  radius: 5,
  // 메서드: 상태 데이터를 참조하고 조작하는 동작
  getDiameter() {
    // 이 메서드가 자신이 속한 객체의 프로퍼티나 다른 메서드를 참조하려면
    // 자신이 속한 객체인 circle을 참조할 수 있어야 한다.
    return 2 * circle.radius;
  }
};

console.log(circle.getDiameter()); // 10
```
`getDiameter`메서드 내에서 메서드 자신이 속한 객체를 가리키는 식별자 `circle`을 참조하고 있다.  

실행되는 순서
1. 변수 호이스팅으로 `circle` 변수 생성(초기화는X)
2. `circle`변수에 할당되기 직전에 객체 리터럴을 평가
3. `circle`객체 생성
4. `circle.getDiameter();`호출시 `getDiameter`메서드가 호출되어 함수 몸체 평가

`circle.getDiameter();`호출하는 시점에는 이미 `circle`객체가 생성되어 있으므로 메서느 내부에서 `circle`식별자를 참조할 수 있다.  

하지만 자신이 속한 객체를 재귀적으로 참조하는 방식은 바람직하지 않다.  

생성자 함수 방식으로 인스턴스를 생성하는 경우를 보자.
```javascript
function Circle(radius) {
  // 이 시점에는 생성자 함수 자신이 생성할 인스턴스를 가리키는 식별자를 알 수 없다.
  ????.radius = radius;
}

Circle.prototype.getDiameter = function () {
  // 이 시점에는 생성자 함수 자신이 생성할 인스턴스를 가리키는 식별자를 알 수 없다.
  return 2 * ????.radius;
};

// 생성자 함수로 인스턴스를 생성하려면 먼저 생성자 함수를 정의해야 한다.
const circle = new Circle(5);
```
함수를 선언하는 시점에는 자신이 생성할 인스턴스를 가리키는 식별자를 알 수 없다.  

생성자 함수로 객체를 생성하려면 생성자 함수를 먼저 정의한 후 `new`연산자와 함께 생성자 함수를 호출해야 한다. 즉, 생성자 함수로 인스턴스를 생성하려면 먼저 생성자 함수가 존재해야 한다.  

생성자 함수를 정의하는 시점에는 인스턴스를 가리키는 식별자를 알 수 없기 때문에 특수한 식별자를 사용한다. 바로 `this`이다.  

`this`는 자신이 속한 객체 또는 자신이 생성할 인스턴스를 가리키는 자기 참조 변수(`self-referenceing variable`)다.  

`this`는 자바스크립트 엔진에 의해 암묵적으로 생성되며, 코드 어디서든 참조할 수 있다. 함수를 호출하면 `arguments`객체와 `this`가 암묵적으로 함수 내부에 전달된다.  

`this`가 가리키는 값, 즉 `this`바인딩은 **함수 호출 방식에 의해 동적으로 결정**된다.  

객체 리터럴에서 `this`사용하는 예제
```javascript
// 객체 리터럴
const circle = {
  radius: 5,
  getDiameter() {
    // this는 메서드를 호출한 객체를 가리킨다.
    return 2 * this.radius;
  }
};

console.log(circle.getDiameter()); // 10
```

생성자 함수에서 `this`사용하는 예제
```javascript
// 생성자 함수
function Circle(radius) {
  // this는 생성자 함수가 생성할 인스턴스를 가리킨다.
  this.radius = radius;
}

Circle.prototype.getDiameter = function () {
  // this는 생성자 함수가 생성할 인스턴스를 가리킨다.
  return 2 * this.radius;
};

// 인스턴스 생성
const circle = new Circle(5);
console.log(circle.getDiameter()); // 10
```

`java`나 `c++`같은 클래스 기반 언어에서 `this`는 항상 클래스가 생성하는 인스턴스를 가리킨다.  

자바스크립트의 `this`는 함수가 호출되는 방식에 따라 `this`바인딩될 값이 동적으로 결정된다.  

```javascript
// this는 어디서든지 참조 가능하다.
// 전역에서 this는 전역 객체 window를 가리킨다.
console.log(this); // window

function square(number) {
  // 일반 함수 내부에서 this는 전역 객체 window를 가리킨다.
  console.log(this); // window
  return number * number;
}
square(2);

const person = {
  name: 'Lee',
  getName() {
    // 메서드 내부에서 this는 메서드를 호출한 객체를 가리킨다.
    console.log(this); // {name: "Lee", getName: ƒ}
    return this.name;
  }
};
console.log(person.getName()); // Lee

function Person(name) {
  this.name = name;
  // 생성자 함수 내부에서 this는 생성자 함수가 생성할 인스턴스를 가리킨다.
  console.log(this); // Person {name: "Lee"}
}

const me = new Person('Lee');
```

`this`는 객체의 메서드 내부 또는 생성자 함수 내부에서만 의미가 있다.  

일반 함수 내부의 `this`는 `window`가 바인딩되고, `strict mode`에서는 `undefined`가 바인딩된다.
```javascript
(function(){
    'use strict';
    function print(){
      console.log(this);
    }
    print();  // undefined
}())
```
하지만 아래 처럼 객체의 프로퍼티로 함수를 할당하면 메서드가 되어 객체가 `this`에는 객체가 바인딩 된다.
```javascript
(function(){
    'use strict';
    function printThis(){
      console.log(this);
    }
    const obj = {};
    obj.print = printThis;
    obj.print(); // obj: {print: ƒ}
}())
```

## 2.함수 호출 방식과 this 바인딩
`this`바인딩은 함수 호출 방식, 즉 함수가 어떻게 호출되었는지에 따라 동적으로 결정된다.  

> ※ 렉시컬 스코프와 this 바인딩은 결정 시기가 다르다.  
> 렉시컬 스코프는 함수 정의가 평가되어 함수 객체가 생성되는 시점에 결정된다.  
> `this`는 함수가 호출시에 결정된다.  

함수를 호출하는 다양한 방식
- 일반 함수 호출
- 메서드 호출
- 생성자 함수 호출
- `Function.prototype.apply/call/bind`메서드에 의한 간접 호출

```javascript
// this 바인딩은 함수 호출 방식에 따라 동적으로 결정된다.
const foo = function () {
  console.dir(this);
};

// 동일한 함수도 다양한 방식으로 호출할 수 있다.

// 1. 일반 함수 호출
// foo 함수를 일반적인 방식으로 호출
// foo 함수 내부의 this는 전역 객체 window를 가리킨다.
foo(); // window

// 2. 메서드 호출
// foo 함수를 프로퍼티 값으로 할당하여 호출
// foo 함수 내부의 this는 메서드를 호출한 객체 obj를 가리킨다.
const obj = { foo };
obj.foo(); // obj

// 3. 생성자 함수 호출
// foo 함수를 new 연산자와 함께 생성자 함수로 호출
// foo 함수 내부의 this는 생성자 함수가 생성한 인스턴스를 가리킨다.
new foo(); // foo {}

// 4. Function.prototype.apply/call/bind 메서드에 의한 간접 호출
// foo 함수 내부의 this는 인수에 의해 결정된다.
const bar = { name: 'bar' };

foo.call(bar);   // bar
foo.apply(bar);  // bar
foo.bind(bar)(); // bar
```

### 2.1 일반 함수 호출
일반 함수로 호출하는 경우 기본적으로 `this`에는 전역 객체가 바인딩된다.
```javascript
function foo() {
  console.log("foo's this: ", this);  // window
  function bar() {
    console.log("bar's this: ", this); // window
  }
  bar();
}
foo();
```
`strict mode`에서는 `this`에 `undefined`가 바인딩된다.

```javascript
function foo() {
  'use strict';

  console.log("foo's this: ", this);  // undefined
  function bar() {
    console.log("bar's this: ", this); // undefined
  }
  bar();
}
foo();
```

메서드 내의 중첩 함수라도 일반 함수로 호출되면 함수 내부의 `this`에는 전역 객체가 바인딩된다.
```javascript
// var 키워드로 선언한 전역 변수 value는 전역 객체의 프로퍼티다.
var value = 1;
// const 키워드로 선언한 전역 변수 value는 전역 객체의 프로퍼티가 아니다.
// const value = 1;

const obj = {
  value: 100,
  foo() {
    console.log("foo's this: ", this);  // {value: 100, foo: ƒ}
    console.log("foo's this.value: ", this.value); // 100

    // 메서드 내에서 정의한 중첩 함수
    function bar() {
      console.log("bar's this: ", this); // window
      console.log("bar's this.value: ", this.value); // 1
    }

    // 메서드 내에서 정의한 중첩 함수도 일반 함수로 호출되면 중첩 함수 내부의 this에는 전역 객체가 바인딩된다.
    bar();
  }
};

obj.foo();
```

콜백 함수도 일반 함수로 호출되면 `this`에 전역 객체가 바인딩된다. 
```javascript
var value = 1;

const obj = {
  value: 100,
  foo() {
    console.log("foo's this: ", this); // {value: 100, foo: ƒ}
    // 콜백 함수 내부의 this에는 전역 객체가 바인딩된다.
    setTimeout(function () {
      console.log("callback's this: ", this); // window
      console.log("callback's this.value: ", this.value); // 1
    }, 100);
  }
};

obj.foo();
```

메서드 내부 중첩 함수나 콜백 함수에 `this`바인딩을 메서드의 `this`와 일치 시키기 위한 방법은 아래 3가지가 있다.  

**1)**`that`**사용**  
```javascript
var value = 1;

const obj = {
  value: 100,
  foo() {
    // this 바인딩(obj)을 변수 that에 할당한다.
    const that = this;

    // 콜백 함수 내부에서 this 대신 that을 참조한다.
    setTimeout(function () {
      console.log(that.value); // 100
    }, 100);
  }
};

obj.foo();
``` 

**2)**`Function.prototype.apply/call/bind`**메서드 활용**  
```javascript
var value = 1;

const obj = {
  value: 100,
  foo() {
    // 콜백 함수에 명시적으로 this를 바인딩한다.
    setTimeout(function () {
      console.log(this.value); // 100
    }.bind(this), 100);
  }
};

obj.foo();
```

**3)화살표 함수 활용**
```javascript
var value = 1;

const obj = {
  value: 100,
  foo() {
    // 화살표 함수 내부의 this는 상위 스코프의 this를 가리킨다.
    setTimeout(() => console.log(this.value), 100); // 100
  }
};

obj.foo();
```

### 2.2 메서드 호출
메서드 내부의 `this`에는 메서드를 호출한 객체, 즉 메서드를 호출할 때 메서드 이름 앞의 마침표`.` 연산자 앞에 기술한 객체가 바인딩된다.  

주의할 것은 메서드 내부의 `this`는 메서드를 소유한 객체가 아닌 **메서드를 호출한 객체에 바인딩** 된다는 것이다.  

```javascript
const person = {
  name: 'Lee',
  getName() {
    // 메서드 내부의 this는 메서드를 호출한 객체에 바인딩된다.
    return this.name;
  }
};

// 메서드 getName을 호출한 객체는 person이다.
console.log(person.getName()); // Lee
```
만약 `getName`메서드를 일반 변수에 할당하여 호출하면 일반 함수로 호출된다.
```javascript
const person = {
  name: 'Lee',
  getName() {
    return this.name;
  }
};

const anotherPerson = {
  name: 'Kim'
};
// getName 메서드를 anotherPerson 객체의 메서드로 할당
anotherPerson.getName = person.getName;

// getName 메서드를 호출한 객체는 anotherPerson이다.
console.log(anotherPerson.getName()); // Kim

// getName 메서드를 변수에 할당
const getName = person.getName;

// getName 메서드를 일반 함수로 호출
console.log(getName()); // ''
// 일반 함수로 호출된 getName 함수 내부의 this.name은 브라우저 환경에서 window.name과 같다.
// 브라우저 환경에서 window.name은 브라우저 창의 이름을 나타내는 빌트인 프로퍼티이며 기본값은 ''이다.
// Node.js 환경에서 this.name은 undefined다.
```

프로토타입 메서드 내부에서 사용된 `this`도 일반 메서드와 동일하게 해당 메서드를 호출한 객체에 바인딩 된다.
```javascript
function Person(name) {
  this.name = name;
}

Person.prototype.getName = function () {
  return this.name;
};

const me = new Person('Lee');

// getName 메서드를 호출한 객체는 me다.
console.log(me.getName()); // ① Lee

Person.prototype.name = 'Kim';

// getName 메서드를 호출한 객체는 Person.prototype이다.
console.log(Person.prototype.getName()); // ② Kim
```
①의 경우 `getName`메서드를 호출한 객체는 `me`이다. 따라서 `getName`메서드 내부의 `this`는 `me`를 가리키며 `this.name`값은 `Lee`다.  

②의 경우 `getName`메서드를 호출한 객체는 `Person.prototype`이고, 객체이므로 직접 메서드를 호출할 수 있다. 따라서 `getName`메서드 내부의 `this`는 `Person.prototype`을 가리키며 `this.name`값은 `Kim`이다.  

### 2.3 생성자 함수 호출

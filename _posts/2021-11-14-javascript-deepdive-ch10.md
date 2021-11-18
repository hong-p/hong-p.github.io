---
title: "모던 자바스크립트 딥 다이브 10장 객체 리터럴"
excerpt: "[javascript] 모던 자바스크립트 Deep Dive 10장 객체 리터럴"
categories:
 - Javascript
tags:
 - Javascript
toc: true
toc_sticky: true
last_modified_at: 2021-11-14
---

# 객체 리터럴

## 1.객체란?
자바스크립트는 `객체 Object`기반의 프로그래밍 언어이며, 자바스크립트를 구성하는 거의 "모든 것"이 객체이다. 원시 값을 제외한 나머지 값(함수, 배열, 정규표현식등)은 모두 객체이다.  
<br>

원시 타입은 단 하나의 값만 나타내지만,  
객체 타입은(`object type` / `reference type`)은 다양한 타입의 값(원시 값 또는 다른 객체)을 하나의 단위로 구성한 복합적인 자료구조`data structure`이다.  

<br>

원시 타입의 값, 즉 원시 값은 변경 불가능한 `immutable value`이지만,  
객체 타입의 값, 즉 객체는 변경 가능한 값 `mutable value`이다.  

<br>

객체는 0개 이상의 프로퍼티로 구성된 집합이며, 프로퍼티는 키`key`와 값`value`으로 구성된다.  

<br>

참고로, 프로퍼티 값이 함수일 경우, 일반 함수와 구분하기 위해 **메서드`method`**라고 부른다.

> -프로퍼티:  객체의 상태를 나타내는 값(data)  
> -메서드: 프로퍼티(상태 데이터)를 참조하고 조작할 수 있는 동작(behavior)

## 2.객체 리터럴에 의한 객체 생성
`c++`이나 `java`같은 클래스 기반 객체지향 언어는 클래스를 사전에 정의하고 필요한 시점에 new 연산자와 함께 생성자(`constructor`)를 호출하여 인스턴스를 생성하는 방식으로 객체를 생성한다.  
하지만, 자바스크립트는 프로토타입 기반 객체지향 언어로서 클래스 기반 객체지향 언어와는 달리 다양한 객체 생성 방법이 있다.  

※객체를 생성하는 방법  
- 객체 리터럴
- `object` 생성자 함수
- 생성자 함수
- `Object.create` 메서드
- 클래스(ES6)

이 중 가장 일반적이고 간단한 방법은 객체 리터럴이다.  
중괄호 `{...}`내에 0개 이상의 프로퍼티를 정의하고, 변수에 할당되는 시점에 자바스크립트 엔진이 해석해 객체를 생성한다.  

```javascript
var person = {
  name: 'Lee',
  sayHello: function () {
    console.log(`Hello! My name is ${this.name}.`);
  }
};

console.log(typeof person); // object
console.log(person); // {name: "Lee", sayHello: ƒ}

// 중괄호 내에 프로퍼티를 정의하지 않으면 빈 객체가 생성된다.
var empty = {}; // 빈 객체
console.log(typeof empty); // object
```
객체 리터럴의 중괄호는 **코드블록**을 의미하지 않는다!!  
객체 리터럴은 값으로 평가되는 표현식이므로, 객체 리터럴의 닫는 중괄호 뒤에 세미콜론`;`을 붙인다.  
<br>

객체 리터럴 외의 객체 생성방식은 모두 함수를 사용한다.

## 3.프로퍼티
객체는 프로퍼티의 집합이며, 프로퍼티는 키`key`와 값`value`으로 구성된다.

```javascript
var person = {
  // 프로퍼티 키는 name, 프로퍼티 값은 'Lee'
  name: 'Lee',
  // 프로퍼티 키는 age, 프로퍼티 값은 20
  age: 20
};
```
프로퍼티를 나열할 때는 쉼표`,`로 구분한다. 일반적으로 마지막 프로퍼티 뒤에는 쉼표`,`를 사용하지 않으나 사용해도 상관없다.  

> 프로퍼티 키: 빈 문자열을 포함하는 모든 문자열 또는 심벌 값  
> 프로퍼티 값: 자바스크립트에서 사용할 수 있는 모든 값

프로퍼티 키는 문자열이므로 따옴표`''`, `""` 로 묶여야 한다. 하지만 [식별자 네이밍 규칙](https://hong-p.github.io/javascript/javascript-deepdive-ch04/#3%EC%8B%9D%EB%B3%84%EC%9E%90-%EB%84%A4%EC%9D%B4%EB%B0%8D-%EA%B7%9C%EC%B9%99)을 준수한다면 따옴표는 생략 가능하다.
```javascript
var person = {
  firstName: 'Ung-mo', // 식별자 네이밍 규칙을 준수하는 프로퍼티 키
  'last-name': 'Lee'   // 식별자 네이밍 규칙을 준수하지 않는 프로퍼티 키
};

console.log(person); // {firstName: "Ung-mo", last-name: "Lee"}
```
<br>

네이밍 규칙을 지키지 않으면 `SyntaxError` 발생
```javascript
var person = {
  firstName: 'Ung-mo',
  last-name: 'Lee' // SyntaxError: Unexpected token -
};
```

<br>

프로퍼티 키를 동적으로 생성 가능하다.
```javascript
var obj = {};
var key = 'hello';

// ES5: 프로퍼티 키 동적 생성
obj[key] = 'world';
// ES6: 계산된 프로퍼티 이름
// var obj = { [key]: 'world' };

console.log(obj); // {hello: "world"}
```

<br>

빈 문자열도 가능하나, 키로서의 의미를 갖지 못한다.
```javascript
var foo = {
  '': ''  // 빈 문자열도 프로퍼티 키로 사용할 수 있다.
};

console.log(foo); // {"": ""}
console.log(foo['']) // '' 접근할때는 []로만 가능
```

<br>

프로퍼티 키에 문자열이나 심벌 값 외의 값을 사용하면 암묵적 타입 변환을 통해 문자열이 된다.
```javascript
var foo = {
  0: 1,
  1: 2,
  2: 3
};

console.log(foo); // {0: 1, 1: 2, 2: 3}
```

<br>

`var`, `function`과 같은 예약어도 가능하지만 에러가 발생할 여지가 있으므로 권장하지 않는다.
```javascript
var foo = {
  var: '',
  function: ''
};

console.log(foo); // {var: "", function: ""}
```

<br>

이미 존재하는 프로퍼티 키를 중복 선언하면 나중에 선언한 프로퍼티 가 덮어쓴다.
```javascript
var foo = {
  name: 'Lee',
  name: 'Kim'
};

console.log(foo); // {name: "Kim"}
```

## 4.메서드
자바스크립트의 함수는 객체`일급 객체`이기 때문에 값으로 취급되어 프로퍼티 값으로 사용할 수 있다.  
이러한 경우 메서드`method`라고 부른다.

```javascript
var circle = {
  radius: 5, // ← 프로퍼티

  // 원의 지름
  getDiameter: function () { // ← 메서드
    return 2 * this.radius; // this는 circle을 가리킨다.
  }
};

console.log(circle.getDiameter()); // 10
```

## 5.프로퍼티 접근
프로퍼티에 접근하는 방법은 두 가지다.
- 마침표 프로퍼티 접근 연산자`.`를 사용하는 마침표 표기법`dot notation`
- 대괄호 프로퍼티 접근 연산자`[]`를 사용하는 대괄호 표기법`bracket notation`

```javascript
var person = {
  name: 'Lee'
};

// 마침표 표기법에 의한 프로퍼티 접근
console.log(person.name); // Lee

// 대괄호 표기법에 의한 프로퍼티 접근
console.log(person['name']); // Lee
```
대괄호 프로퍼티 접근 연산자`[]` 내부에 지정하는 프로퍼티 키는 반드시 따옴표(`''`, `""`)로 감싼 문자열이어야 한다. 만약 따옴표로 감싸지 않으면 자바스크립트 엔진은 식별자로 해석한다.  
```javascript
var person = {
  name: 'Lee'
};

console.log(person[name]); // ReferenceError: name is not defined
```

<br>

객체에 존재하지 않는 프로퍼티에 접근하면 `undefined`를 반환한다. `ReferenceError`가 발생되지 않음!!
```javascript
var person = {
  name: 'Lee'
};

console.log(person.age); // undefined
```

```javascript
var person = {
  'last-name': 'Lee',
  1: 10
};

person.'last-name';  // -> SyntaxError: Unexpected string
person.last-name;    // -> 브라우저 환경: NaN
                     // -> Node.js 환경: ReferenceError: name is not defined
person[last-name];   // -> ReferenceError: last is not defined
person['last-name']; // -> Lee

// 프로퍼티 키가 숫자로 이뤄진 문자열인 경우 따옴표를 생략할 수 있다.
person.1;     // -> SyntaxError: Unexpected number
person.'1';   // -> SyntaxError: Unexpected string
person[1];    // -> 10 : person[1] -> person['1']
person['1'];  // -> 10
```
위 예제에서 `person.last-name`의 실행결과가 브라우저와 Node.js 환경이 다른 이유는??  
`person.last-name`을 평가할 때 자바스크립트 엔진은 `person.last`를 먼저 평가하는데,  
브라우저 환경에는 `window`객체에 `name`의 프로퍼티가 암묵적으로 전재하고 기본값은 빈 문자열이다.  
이에 반해 Node.js 환경에서는 전역변수 name이 존재하지 않기 대문에 `ReferenceError`가 발생한다.  

## 6.프로퍼티 값 갱신
이미 존재하는 프로퍼티에 값을 할당하면 프로퍼티 값이 갱신된다.
```javascript
var person = {
  name: 'Lee'
};

// person 객체에 name 프로퍼티가 존재하므로 name 프로퍼티의 값이 갱신된다.
person.name = 'Kim';

console.log(person);  // {name: "Kim"}
```

## 7.프로퍼티 동적 생성
존재하지 않는 프로퍼티에 값을 할당하면 동적으로 생성되어 추가되고 프로퍼티 값이 할당된다.
```javascript
var person = {
  name: 'Lee'
};

// person 객체에는 age 프로퍼티가 존재하지 않는다.
// 따라서 person 객체에 age 프로퍼티가 동적으로 생성되고 값이 할당된다.
person.age = 20;

console.log(person); // {name: "Lee", age: 20}
```

## 8.프로퍼티 삭제
`delete`연산자를 활용해 객체의 프로퍼티를 삭제한다. 이때 `delete`연산자의 피연산자는 프로퍼티 값에 접글할 수 있는 표현식이어야 한다. 만약 존재하지 않는 프로퍼티인 경우 아무런 에러 없이 무시된다.
```javascript
var person = {
  name: 'Lee'
};

// 프로퍼티 동적 생성
person.age = 20;

// person 객체에 age 프로퍼티가 존재한다.
// 따라서 delete 연산자로 age 프로퍼티를 삭제할 수 있다.
delete person.age;

// person 객체에 address 프로퍼티가 존재하지 않는다.
// 따라서 delete 연산자로 address 프로퍼티를 삭제할 수 없다. 이때 에러가 발생하지 않는다.
delete person.address;

console.log(person); // {name: "Lee"}


// 프로퍼티를 지울 수 없게 생성하기
Object.defineProperty(person, 'age', {value:20, configurable:false}) 
delete person.age // false
console.log(person); // {name: "Lee", age: 20}
```

전역 객체로 생성하는 경우 특이한 점
```javascript
// window 객체에 프로퍼티를 추가하는 경우
window.a = 10
console.log(window.a) // 10

delete window.a // true
console.log(window.a) // undefined


// var 키워드로 생성하는 경우
var a = 10
console.log(window.a) // 10

delete window.a // false
console.log(window.a) // 10
```
var 키워드로 전역변수를 생성하는 경우  
위에서 확인한 `Object.defineProperty()` 함수로 `configurable:false`를 준것과 동일하게 프로퍼티를 생성하는 듯!  

<br>

## 9.ES6에서 추가된 객체 리터럴의 확장 기능
ES6에서 추가된 객체 리터럴 확장기능
- 프로퍼티 축약 표현
- 계산된 프로퍼티 이름
- 메서드 축약 표현

### 9.1 프로퍼티 축약 표현
변수 이름과 프로퍼티 키가 동일한 이름인 경우 프로퍼티 키를 생략`property shorthand`할 수 있다. 이때 프로퍼티 키는 변수 이름으로 자동 생성된다.
```javascript
// ES5
var x = 1, y = 2;

var obj = {
  x: x,
  y: y
};

console.log(obj); // {x: 1, y: 2}


// ES6
let x = 1, y = 2;

// 프로퍼티 축약 표현
const obj = { x, y };

console.log(obj); // {x: 1, y: 2}
```

### 9.2 계산된 프로퍼티 이름
값으로 평가되는 표현식을 사용해 프로퍼티 키를 동적으로 생성 할 수 있다. 단 대괄호`[]`로 묶어 사용해야 한다. 이를 `computed property name`이라고 한다.

<br>
ES5에서는 객체 리터럴 외부에서 대괄호`[]` 표기법을 사용해야 하고,  
ES6에서는 객체 리터럴 내부에서도 생성할 수 있다.

```javascript
// ES5
var prefix = 'prop';
var i = 0;

var obj = {};

// 계산된 프로퍼티 이름으로 프로퍼티 키 동적 생성
obj[prefix + '-' + ++i] = i;
obj[prefix + '-' + ++i] = i;
obj[prefix + '-' + ++i] = i;

console.log(obj); // {prop-1: 1, prop-2: 2, prop-3: 3}


// ES6
const prefix = 'prop';
let i = 0;

// 객체 리터럴 내부에서 계산된 프로퍼티 이름으로 프로퍼티 키 동적 생성
const obj = {
  [`${prefix}-${++i}`]: i,
  [`${prefix}-${++i}`]: i,
  [`${prefix}-${++i}`]: i
};

console.log(obj); // {prop-1: 1, prop-2: 2, prop-3: 3}
```


### 9.3 메서드 축약 표현
ES5에서는 아래와 같이 콜론`:`과 `function`키워드로 객체 리터럴 내부에서 생성하지만,  
ES6에서는 콜론`:`과 `function` 키워드를 생략한 축약 표현을 사용할 수 있다.
```javascript
// ES5
var obj = {
  name: 'Lee',
  sayHi: function() {
    console.log('Hi! ' + this.name);
  }
};

obj.sayHi(); // Hi! Lee


// ES6
const obj = {
  name: 'Lee',
  // 메서드 축약 표현
  sayHi() {
    console.log('Hi! ' + this.name);
  }
};

obj.sayHi(); // Hi! Lee
```

<br>

프로퍼티의 일반 함수와 메서드(메서드 축약 표현) 차이점

일반 함수 | 메서드 
:--:|:--:
인스턴스 생성 O | 인스턴스 생성 X
생성자 함수로서 호출 O | 생성자 함수로서 호출 X
`new` O | `new` X
prototype 프로퍼티 O | prototype 프로퍼티 X
`[[HomeObject]]` X | `[[HomeObject]]` O
`super` 사용 X | `super` 사용 O

```javascript
const obj = {
  x: 1,
  // foo는 메서드이다.
  foo() { return this.x; },
  // bar에 바인딩된 함수는 메서드가 아닌 일반 함수이다.
  bar: function() { return this.x; }
};

console.log(obj.foo()); // 1
console.log(obj.bar()); // 1


new obj.foo(); // -> TypeError: obj.foo is not a constructor
new obj.bar(); // -> bar {}


// obj.foo는 constructor가 아닌 ES6 메서드이므로 prototype 프로퍼티가 없다.
obj.foo.hasOwnProperty('prototype'); // -> false

// obj.bar는 constructor인 일반 함수이므로 prototype 프로퍼티가 있다.
obj.bar.hasOwnProperty('prototype'); // -> true
```

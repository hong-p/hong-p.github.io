---
title: "모던 자바스크립트 딥 다이브 21장 빌트인 객체"
excerpt: "[javascript] 모던 자바스크립트 Deep Dive 21장 빌트인 객체"
categories:
 - Javascript
tags:
 - Javascript
toc: true
toc_sticky: true
last_modified_at: 2021-12-07
---

# 21장 빌트인 객체

## 1.자바스크립트 객체의 분류
- 표준 빌트인 객체(Standard built-in objects)
표준 빌트인 객체는 ECMAScript 사양에 정의된 객체를 말하며, 애플리케이션 전역의 공통 기능을 제공한다. `Object`, `String`, `Number`, `Boolean`, `Symbol`, `Date`, `Math`, `RegExp`, `Array`, `Map/Set`, `WeakMap`, `WeakSet`, `Function`, `Promise`, `Reflect`, `Proxy`, `JSON`, `Error`등 40여개 객체를 제공한다.  

- 호스트 객체(host objects)
호스트 객체는 ECMAScript 사양에 정의되어 있지 않지만 자바스크립트 실행 환경(브라우저 환경 또는 Node.js환경)에서 추라고 제공하는 객체를 말한다.  
브라우저 환경에서는 `DOM`, `BOM`, `Canvas`, `XMLHttpRequest`, `fetch`, `requestAnimationFrame`, `SVG`, `Web Storage`, `Web Component`, `Web Worker`와 같은 [클라이언트 사이드 Web API](https://developer.mozilla.org/ko/docs/Web/API)를 호스트 객체로 제공하고,  
Node.js환경에서는 [Node.js고유의 API](https://nodejs.org/api/)를 호스트 객체로 제공한다.  

## 2.표준 빌트인 객체
`Object`, `String`, `Number`, `Boolean`, `Symbol`, `Date`, `Math`, `RegExp`, `Array`, `Map/Set`, `WeakMap`, `WeakSet`, `Function`, `Promise`, `Reflect`, `Proxy`, `JSON`, `Error`  

`Math`, `Reflect`, `JSON`을 제외한 표준 빌트인 객체는 모두 인스턴스를 생성할 수 있는 생성자 함수 객체다.  
생성자 함수 객체인 표준 빌트인 객체는 프로토타입 메서드(`Object.prototype.toString`)와 정적 메서드(`Object.create`등)를 제공하고,  
생성자 함수 객체가 아닌 표준 빌트인 객체는 정적 메서드만 제공한다.

`console.dir(Obejct)`  
![image](https://user-images.githubusercontent.com/80154058/145033694-1e961f95-8a2b-436b-8642-2f292ccd2c1a.png)

`console.dir(Math)`  
![image](https://user-images.githubusercontent.com/80154058/145033851-50b003c8-1281-4890-8a97-5d0a0a6abdc1.png)

`String`, `Number`, `Boolean`, `Function`, `Array`, `Date`는 생성자 함수로 호출하여 인스턴스를 생성할 수 있다.

```javascript
// String 생성자 함수에 의한 String 객체 생성
const strObj = new String('Lee'); // String {"Lee"}
console.log(typeof strObj);       // object

// Number 생성자 함수에 의한 Number 객체 생성
const numObj = new Number(123); // Number {123}
console.log(typeof numObj);     // object

// Boolean 생성자 함수에 의한 Boolean 객체 생성
const boolObj= new Boolean(true); // Boolean {true}
console.log(typeof boolObj);      // object

// Function 생성자 함수에 의한 Function 객체(함수) 생성
const func = new Function('x', 'return x * x'); // ƒ anonymous(x )
console.log(typeof func);                       // function

// Array 생성자 함수에 의한 Array 객체(배열) 생성
const arr = new Array(1, 2, 3); // (3) [1, 2, 3]
console.log(typeof arr);        // object

// RegExp 생성자 함수에 의한 RegExp 객체(정규 표현식) 생성
const regExp = new RegExp(/ab+c/i); // /ab+c/i
console.log(typeof regExp);         // object

// Date 생성자 함수에 의한 Date 객체 생성
const date = new Date();  // Fri May 08 2020 10:43:25 GMT+0900 (대한민국 표준시)
console.log(typeof date); // object
```

생성자 함수인 표준 빌트인 객체로 생성한 인스턴스의 프로토타입은 표준 빌트인 객체의 prototype 프로퍼티에 바인딩된다.
```javascript
// String 생성자 함수에 의한 String 객체 생성
const strObj = new String('Lee'); // String {"Lee"}

// String 생성자 함수를 통해 생성한 strObj 객체의 프로토타입은 String.prototype이다.
console.log(Object.getPrototypeOf(strObj) === String.prototype); // true
```

인스턴스는 프로토타입 메서드를 사용할 수 있다.  

`Number` 빌트인 객체는 인스턴스 없이 정적으로 호출할 수 있는 정적 메서드도 제공한다.
```javascript
// Number 생성자 함수에 의한 Number 객체 생성
const numObj = new Number(1.5); // Number {1.5}

// toFixed는 Number.prototype의 프로토타입 메서드다.
// Number.prototype.toFixed는 소수점 자리를 반올림하여 문자열로 반환한다.
console.log(numObj.toFixed()); // 2

// isInteger는 Number의 정적 메서드다.
// Number.isInteger는 인수가 정수(integer)인지 검사하여 그 결과를 Boolean으로 반환한다.
console.log(Number.isInteger(0.5)); // false
```


## 3.원시값과 래퍼 객체
문자열, 숫자, 불리언 등의 원시값이 있는데도 `String`, `Number`, `Boolean`등의 표준 빌트인 생성자 함수가 존재하는 이유가 뭘까?  

```javascript
const str = 'hello';

// 원시 타입인 문자열이 프로퍼티와 메서드를 갖고 있는 객체처럼 동작한다.
console.log(str.length); // 5
console.log(str.toUpperCase()); // HELLO
```
위 예제와 같이 `str`은 원시값인 문자열을 담은 변수인데, `str.length`, `str.toUpperCase()`와 같이 프로퍼티와 메서드를 호출할 수 있다.  

이는 원시값에 객체처럼 **마침표 표기법**으로 접근하면 자바스크립트 엔진이 **일시적으로 원시값을 연관된 객체로 변환**해 주기 때문이다.  

즉, 원시값을 객체처럼 사용하면 자바스크립트 엔진은 암묵적으로 연관된 객체를 생성하여 생성된 객체로 프로퍼티에 접근하거나 메서드를 호출하고 다시 원시값으로 되돌린다.  

이러한 임시 객체를 래퍼 객체(`wrapper object`)라고 한다.  
```javascript
const str = 'hi';

// 원시 타입인 문자열이 래퍼 객체인 String 인스턴스로 변환된다.
console.log(str.length); // 2
console.log(str.toUpperCase()); // HI

// 래퍼 객체로 프로퍼티에 접근하거나 메서드를 호출한 후, 다시 원시값으로 되돌린다.
console.log(typeof str); // string
```
래퍼 객체가 생성되고 사용된 후 객체의 처리가 종료되면 래퍼 객체의 `[[StringData]]` 내부 슬롯에 할당된 원시값으로 원래의 상태로(원시 값)되돌리고,  
래퍼 객체는 가비지 컬렉션의 대상이 되어 사라진다.  

숫자 값도 마찬가지다. 래퍼 객체의 `[[NumberData]]`내부 슬롯에 할당되고, 처리가 종료되면 원시값으로 되돌리고 래퍼 객체는 가비지 컬렉션의 대상이 된다.  
```javascript
const num = 1.5;

// 원시 타입인 숫자가 래퍼 객체인 Number 객체로 변환된다.
console.log(num.toFixed()); // 2

// 래퍼 객체로 프로퍼티에 접근하거나 메서드를 호출한 후, 다시 원시값으로 되돌린다.
console.log(typeof num, num); // number 1.5
```

`Symbol`함수도 마찬가지로 래퍼 객체를 생성한다.  

※ `null`, `undefined`는 래퍼 객체를 생성하지 않는다. 따라서 `null`과 `undefined`값을 객체처럼 사용하면 에러가 발생한다.

## 4.전역 객체
전역 객체(`global object`)는 코드가 실행되기 이전 단계에 자바스크립트 엔진에 의해 어떤 객체보다도 먼저 생성되는 특수한 객체이며, 어떤 객체에도 속하지 않은 최상위 객체다.  

브라우저 환경에서는 `window`, Node.js 환경에서는 `global`이다.  

<br>

전역 객체의 프로퍼티
 - 표준 빌트인 객체(`Object`, `String`, `Number`, `Function`, `Array`등)
 - 환경에 따른 호스트 객체(클라이언트 Web API, Node.js의 호스트 API)
 - `var`키워드로 선언한 전역 변수
 - 전역 함수

<br>

전역 객체의 특징
- 전역 객체는 개발자가 의도적으로 생성할 수 없다. 즉, 전역 객체를 생성할 수 있는 생성자 함수가 제공되지 않는다.
- 전역 객체의 프로퍼티를 참조할 때 `window`(또는 `global`)를 생략할 수 있다.
```javascript
window.parseInt === parseInt; // -> true
```
- 전역 객체는 `Object`, `String`, `Number`, `Boolean`등 모든 표준 빌트인 객체를 프로퍼티로 가지고 있다.
- 자바스크립트 실행환경(브라우저 또는 Node.js)에 따라 추가적인 프로퍼티와 메서드를 갖는다.(브라우저: DOM, BOM, Canvas, XMLHttpRequest등 / Node.js: Node.js 고유의 API)
- `var`키워드로 선언한 전역 변수, 선언하지 않은 변수에 값을 할당한 암묵적 전역, 전역 함수는 전역 객체의 프로퍼티가 된다.
```javascript
// var 키워드로 선언한 전역 변수
var foo = 1;
console.log(window.foo); // 1

// 선언하지 않은 변수에 값을 암묵적 전역. bar는 전역 변수가 아니라 전역 객체의 프로퍼티다.
bar = 2; // window.bar = 2
console.log(window.bar); // 2

// 전역 함수
function baz() { return 3; }
console.log(window.baz()); // 3
```
- `let`이나 `const`키워드로 선언한 전역 변수는 전역 객체의 프로퍼티가 아니다.
```javascript
let foo = 123;
console.log(window.foo); // undefined
```
- 브라우저 환경의 모든 자바스크립트 코드는 하나의 전역 객체 `window`를 공유한다.

### 4.1 빌트인 전역 프로퍼티
빌트인 전역 프로퍼티는 전역 객체의 프로퍼티를 의미한다.  

<br>

**Infinity**  
`Infinity`프로퍼티는 무한대를 나타내는 숫자값 `Infinity`를 갖는다.
```javascript
// 전역 프로퍼티는 window를 생략하고 참조할 수 있다.
console.log(window.Infinity === Infinity); // true

// 양의 무한대
console.log(3/0);  // Infinity
// 음의 무한대
console.log(-3/0); // -Infinity
// Infinity는 숫자값이다.
console.log(typeof Infinity); // number
```

<br>

**NaN**  
`NaN`프로퍼티는 숫자가 아님(`Not a Number`)을 나타내는 숫자값 `NaN`을 갖는다.  
`NaN`프로퍼티는 `Number.NaN`프로퍼티와 같다.  

```javascript
console.log(window.NaN); // NaN

console.log(Number('xyz')); // NaN
console.log(1 * 'string');  // NaN
console.log(typeof NaN);    // number
```

<br>

**undefined**  
`undefined`프로퍼티는 원시 타입 `undefined`를 값으로 갖는다.

```javascript
console.log(window.undefined); // undefined

var foo;
console.log(foo); // undefined
console.log(typeof undefined); // undefined
```

### 4.2 빌트인 전역 함수
빌트인 전역 함수는 애플리케이션 전역에서 호추할 수 있는 빌트인 함수로서 전역 객체의 메서드다.  

<br>

**eval**  
`eval`함수는 자바스크립트 코드를 나타내는 문자열을 인수로 전달받아, 전달받은 문자열 코드가 표현식 이라면 `eval`함수는 문자열 코드를 런타임에 평가하여 값을 생성하고,  
전달받은 인수가 표현식이 아닌 문이라면 `eval`함수는 문자열 코드를 런타임에 실행한다.  
```javascript
/**
 * 주어진 문자열 코드를 런타임에 평가 또는 실행한다.
 * @param {string} code - 코드를 나타내는 문자열
 * @return {*} 문자열 코드를 평가/실행한 결과값
 */
eval(code)
```
```javascript
// 표현식인 문
eval('1 + 2;'); // -> 3

// 표현식이 아닌 문
eval('var x = 5;'); // -> undefined

// eval 함수에 의해 런타임에 변수 선언문이 실행되어 x 변수가 선언되었다.
console.log(x); // 5

// 객체 리터럴은 반드시 괄호로 둘러싼다.
const o = eval('({ a: 1 })');
console.log(o); // {a: 1}

// 함수 리터럴은 반드시 괄호로 둘러싼다.
const f = eval('(function() { return 1; })');
console.log(f()); // 1
```

여러개의 문으로 이루어져 있다면 모든 문을 실행한 다음, 마지막 결과값을 반환한다.
```javascript
console.log(eval('1 + 2; 3 + 4;')); // 7
```

`eval`함수는 자신이 호출된 위치에 해당하는 기존의 스코프를 런타임에 동적으로 수정한다.
```javascript
const x = 1;

function foo() {
  // eval 함수는 런타임에 foo 함수의 스코프를 동적으로 수정한다.
  eval('var x = 2;');
  console.log(x); // 2
}

foo();
console.log(x); // 1
```

`strict mode`에서는 `eval`함수는 기존의 스코프를 수정하지 않고 `eval`함수 자신의 자체적인 스코프를 생성한다.
```javascript
const x = 1;

function foo() {
  'use strict';

  // strict mode에서 eval 함수는 기존의 스코프를 수정하지 않고 eval 함수 자신의 자체적인 스코프를 생성한다.
  eval('var x = 2; console.log(x);'); // 2
  console.log(x); // 1
}

foo();
console.log(x); // 1
```

인수로 전달받은 문자열 코드가 `let`, `const` 키워드를 사요한 변수 선언문이라면 암묵적으로 `strict mode`가 적용된다.
```javascript
const x = 1;

function foo() {
  eval('var x = 2; console.log(x);'); // 2
  // let, const 키워드를 사용한 변수 선언문은 strict mode가 적용된다.
  eval('const x = 3; console.log(x);'); // 3
  console.log(x); // 2
}

foo();
console.log(x); // 1
```

※ `eval`함수는 보안에 매우 취약하다.  
또한, 최적화가 수행되지 않아 일반적인 코드 실행에 비해 느리다.  
따라서 `eval`**함수의 사용은 금지해야 한다.**

<br>

**isFinite**  
전달받은 인수가 정상적인 유한수 인지 검사하여 유한수이면 `true`, 무한수이면 `false`를 반환한다.
```javascript
// 인수가 유한수이면 true를 반환한다.
isFinite(0);    // -> true
isFinite(2e64); // -> true
isFinite('10'); // -> true: '10' → 10(문자열->숫자)
isFinite(null); // -> true: null → 0(null->0)

// 인수가 무한수 또는 NaN으로 평가되는 값이라면 false를 반환한다.
isFinite(Infinity);  // -> false
isFinite(-Infinity); // -> false

// 인수가 NaN으로 평가되는 값이라면 false를 반환한다.
isFinite(NaN);     // -> false
isFinite('Hello'); // -> false
isFinite('2005/12/12'); // -> false
```

<br>

**isNaN**  
전달받은 인수가 `NaN`인지 검사하여 그 결과를 불리언 타입으로 반환한다. 전달받은 인수의 타입이 숫자가 아닌경우 숫자로 타입을 변환한 후 검사를 수행한다.  
```javascript
// 숫자
isNaN(NaN); // -> true
isNaN(10);  // -> false

// 문자열
isNaN('blabla'); // -> true: 'blabla' => NaN
isNaN('10');     // -> false: '10' => 10
isNaN('10.12');  // -> false: '10.12' => 10.12
isNaN('');       // -> false: '' => 0
isNaN(' ');      // -> false: ' ' => 0

// 불리언
isNaN(true); // -> false: true → 1
isNaN(null); // -> false: null → 0

// undefined
isNaN(undefined); // -> true: undefined => NaN

// 객체
isNaN({}); // -> true: {} => NaN

// date
isNaN(new Date());            // -> false: new Date() => Number
isNaN(new Date().toString()); // -> true:  String => NaN
```

<br>

**parseFloat**  
전달받은 문자열 인수를 부동 소수점 숫자(`floating point number`)즉, 실수로 해석하여 반환한다.
```javascript
// 문자열을 실수로 해석하여 반환한다.
parseFloat('3.14');  // -> 3.14
parseFloat('10.00'); // -> 10

// 공백으로 구분된 문자열은 첫 번째 문자열만 변환한다.
parseFloat('34 45 66'); // -> 34
parseFloat('40 years'); // -> 40

// 첫 번째 문자열을 숫자로 변환할 수 없다면 NaN을 반환한다.
parseFloat('He was 40'); // -> NaN

// 앞뒤 공백은 무시된다.
parseFloat(' 60 '); // -> 60
```

<br>

**parseInt**  
전달받은 문자열 인수를 정수(`integer`)로 해석하여 반환한다.  
두 번째 인수로 진법을 나타내는 기수(2~36)을 전달할 수 있다.

```javascript
// 문자열을 정수로 해석하여 반환한다.
parseInt('10');     // -> 10
parseInt('10.123'); // -> 10

parseInt(10);     // -> 10
parseInt(10.123); // -> 10

// 10'을 10진수로 해석하고 그 결과를 10진수 정수로 반환한다
parseInt('10'); // -> 10
// '10'을 2진수로 해석하고 그 결과를 10진수 정수로 반환한다
parseInt('10', 2); // -> 2
// '10'을 8진수로 해석하고 그 결과를 10진수 정수로 반환한다
parseInt('10', 8); // -> 8
// '10'을 16진수로 해석하고 그 결과를 10진수 정수로 반환한다
parseInt('10', 16); // -> 16
```

기수를 지정하여 10진수 숫자를 문자열로 변환하여 반환하려면 `Number.prototype.toString`메서드를 사용한다.
```javascript
const x = 15;

// 10진수 15를 2진수로 변환하여 그 결과를 문자열로 반환한다.
x.toString(2); // -> '1111'
// 문자열 '1111'을 2진수로 해석하고 그 결과를 10진수 정수로 반환한다
parseInt(x.toString(2), 2); // -> 15

// 10진수 15를 8진수로 변환하여 그 결과를 문자열로 반환한다.
x.toString(8); // -> '17'
// 문자열 '17'을 8진수로 해석하고 그 결과를 10진수 정수로 반환한다
parseInt(x.toString(8), 8); // -> 15

// 10진수 15를 16진수로 변환하여 그 결과를 문자열로 반환한다.
x.toString(16); // -> 'f'
// 문자열 'f'를 16진수로 해석하고 그 결과를 10진수 정수로 반환한다
parseInt(x.toString(8), 8); // -> 15

// 숫자값을 문자열로 변환한다.
x.toString(); // -> '15'
// 문자열 '15'를 10진수로 해석하고 그 결과를 10진수 정수로 반환한다
parseInt(x.toString()); // -> 15
```

문자열이 16진수 리터럴(`0x`또는 `0X`로 시작)이면 16진수로 해석하여 10진수 정수로 반환한다.
```javascript
// 16진수 리터럴 '0xf'를 16진수로 해석하고 10진수 정수로 그 결과를 반환한다.
parseInt('0xf'); // -> 15
// 위 코드와 같다.
parseInt('f', 16); // -> 15
```

2진수, 8진수 리터럴은 안된다.  
0으로 시작하는 숫자로 인식해 버린다.
```javascript
// 2진수 리터럴(0b로 시작)은 제대로 해석하지 못한다. 0 이후가 무시된다.
parseInt('0b10'); // -> 0
// 8진수 리터럴(ES6에서 도입. 0o로 시작)은 제대로 해석하지 못한다. 0 이후가 무시된다.
parseInt('0o10'); // -> 0
```
두 번째 인수로 명확하게 알려줘야 한다.
```javascript
// 문자열 '10'을 2진수로 해석한다.
parseInt('10', 2); // -> 2
// 문자열 '10'을 8진수로 해석한다.
parseInt('10', 8); // -> 8
```

아래 참고
```javascript
// 'A'는 10진수로 해석할 수 없다.
parseInt('A0'); // -> NaN
// '2'는 2진수로 해석할 수 없다.
parseInt('20', 2); // -> NaN

// 10진수로 해석할 수 없는 'A' 이후의 문자는 모두 무시된다.
parseInt('1A0'); // -> 1
// 2진수로 해석할 수 없는 '2' 이후의 문자는 모두 무시된다.
parseInt('102', 2); // -> 2
// 8진수로 해석할 수 없는 '8' 이후의 문자는 모두 무시된다.
parseInt('58', 8); // -> 5
// 16진수로 해석할 수 없는 'G' 이후의 문자는 모두 무시된다.
parseInt('FG', 16); // -> 15

// 공백으로 구분된 문자열은 첫 번째 문자열만 변환한다.
parseInt('34 45 66'); // -> 34
parseInt('40 years'); // -> 40
// 첫 번째 문자열을 숫자로 변환할 수 없다면 NaN을 반환한다.
parseInt('He was 40'); // -> NaN
// 앞뒤 공백은 무시된다.
parseInt(' 60 '); // -> 60
```

<br>

**encodeURI / decodeURI**  
`encodeURI`함수는 완전한 `URI Uniform Resource Identifier`를 문자열로 전달받아 이스케이프 처리를 위해 인코딩한다.  
`URI`는 인터넷에 있는 자원을 나타내는 유일한 주소를 말한다.

![image](https://user-images.githubusercontent.com/80154058/145043419-e34d09f0-64a0-4ea2-b0bc-f14c6519dc01.png)

> ※ *이스케이프 처리*  
> 이스케이프 처리란 네트워크를 통해 정보를 공유할 때 어떤 시스템에서도 읽을 수 있는 **아스키 문자 셋**으로 변환하는 것이다. 예를들면 한글'가'는 %EC%9E%90 으로 인코딩된다.

```javascript
// 완전한 URI
const uri = 'http://example.com?name=이웅모&job=programmer&teacher';

// encodeURI 함수는 완전한 URI를 전달받아 이스케이프 처리를 위해 인코딩한다.
const enc = encodeURI(uri);
console.log(enc);
// http://example.com?name=%EC%9D%B4%EC%9B%85%EB%AA%A8&job=programmer&teacher
```

`decodeURI`함수는 인코딩된 `URI`를 인수로 전달받아 이스케이프 처리 이전으로 디코딩한다.
```javascript
const uri = 'http://example.com?name=이웅모&job=programmer&teacher';

// encodeURI 함수는 완전한 URI를 전달받아 이스케이프 처리를 위해 인코딩한다.
const enc = encodeURI(uri);
console.log(enc);
// http://example.com?name=%EC%9D%B4%EC%9B%85%EB%AA%A8&job=programmer&teacher

// decodeURI 함수는 인코딩된 완전한 URI를 전달받아 이스케이프 처리 이전으로 디코딩한다.
const dec = decodeURI(enc);
console.log(dec);
// http://example.com?name=이웅모&job=programmer&teacher
```

<br>

**encodeURIComponent / decodeURIComponent**  
`encodeURIComponent`함수는 `URI`구성요소를 인수로 전달받아 인코딩한다.(이스케이프 처리)  
`decodeURIComponent`함수는 매개변수로 전달된 `URI`구성요소를 디코딩한다.  

`encodeURIComponent`함수는 인수로 전달된 문자열을 URI의 구성요소인 쿼리 스트링의 일부로 간주한다. 따라서 쿼리스트링 구분자로 사용되는 `=`, `?`, `&`까지 인코딩한다.  

반면 `endcodeURI`함수는 매개변수로 전달된 문자열을 완전한 URI전체라고 간주한다. 따라서 `=`, `?`, `&`은 이코딩하지 않는다.

```javascript
// URI의 쿼리 스트링
const uriComp = 'name=이웅모&job=programmer&teacher';

// encodeURIComponent 함수는 인수로 전달받은 문자열을 URI의 구성요소인 쿼리 스트링의 일부로 간주한다.
// 따라서 쿼리 스트링 구분자로 사용되는 =, ?, &까지 인코딩한다.
let enc = encodeURIComponent(uriComp);
console.log(enc);
// name%3D%EC%9D%B4%EC%9B%85%EB%AA%A8%26job%3Dprogrammer%26teacher

let dec = decodeURIComponent(enc);
console.log(dec);
// 이웅모&job=programmer&teacher

// encodeURI 함수는 인수로 전달받은 문자열을 완전한 URI로 간주한다.
// 따라서 쿼리 스트링 구분자로 사용되는 =, ?, &를 인코딩하지 않는다.
enc = encodeURI(uriComp);
console.log(enc);
// name=%EC%9D%B4%EC%9B%85%EB%AA%A8&job=programmer&teacher

dec = decodeURI(enc);
console.log(dec);
// name=이웅모&job=programmer&teacher
```

![image](https://user-images.githubusercontent.com/80154058/145044892-2e61223b-e71e-45c0-950d-9549bead4c06.png)


### 4.3 암묵적 전역
선언하지 않은 식별자에 값을 할당하면 전역 객체의 프로퍼티로 등록된다. 이를 암묵적 전역(`implicit global`)이라 한다.  

```javascript
var x = 10; // 전역 변수

function foo () {
  // 선언하지 않은 식별자에 값을 할당
  y = 20; // window.y = 20;
}
foo();

// 선언하지 않은 식별자 y를 전역에서 참조할 수 있다.
console.log(x + y); // 30
```
`foo`함수 내부에 `y = 20;`이 실행되면 참조 에러가 발생하지 않고, 전역 변수처럼 사용된다.  
전역 객체의 프로퍼티로 등록되어 참조되기 때문이다.  

하지만 `y`는 변수 선언 없이 **단지 전역 객체의 프로퍼티로 추가되었을 뿐**이다.  
`y`**는 변수가 아니다.** 따라서 **변수 호이스팅도 발생하지 않는다.**  

```javascript
// 전역 변수 x는 호이스팅이 발생한다.
console.log(x); // undefined
// 전역 변수가 아니라 단지 전역 객체의 프로퍼티인 y는 호이스팅이 발생하지 않는다.
console.log(y); // ReferenceError: y is not defined

var x = 10; // 전역 변수

function foo () {
  // 선언하지 않은 식별자에 값을 할당
  y = 20; // window.y = 20;
}
foo();

// 선언하지 않은 식별자 y를 전역에서 참조할 수 있다.
console.log(x + y); // 30
```

또한 `delete`연산자로 삭제할수 있다. 변수가 아니기 때문에!!  

전역 변수는 프로퍼티이지만 `delete`연산자로 삭제할 수 없다.
```javascript
var x = 10; // 전역 변수

function foo () {
  // 선언하지 않은 식별자에 값을 할당
  y = 20; // window.y = 20;
  console.log(x + y);
}

foo(); // 30

console.log(window.x); // 10
console.log(window.y); // 20

delete x; // 전역 변수는 삭제되지 않는다.
delete y; // 프로퍼티는 삭제된다.

console.log(window.x); // 10
console.log(window.y); // undefined
```

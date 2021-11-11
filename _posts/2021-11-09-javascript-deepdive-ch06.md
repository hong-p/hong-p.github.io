---
title: "[javascript] 모던 자바스크립트 Deep Dive ch06 데이터 타입"
excerpt: "모던 자바스크립트 딥다이브 ch06 데이터 타입"
categories:
 - Javascript
tags:
 - Javascript
toc: true
toc_sticky: true
last_modified_at: 2021-11-10
---

# 데이터 타입
데이터 타입 종류

구분 | 데이터 타입 | 설명
:---:|:---:|:---
원시 타입 | 숫자 타입 | 숫자, 정수와 실수 구분 없이 하나의 숫자 타입만 존재
 | 문자열 타입 | 문자열 
 | 불리언 타입 | 논리적 참(true)와 거짓(false)
 | undefined 타입 | var 키워드로 선언된 변수에 암묵적으로 할당되는 값
 | null 타입 | 값이 없다는 것을 의도적으로 명시할 때 사용하는 값
 | 심벌 타입 | ES6에서 추가된 7번째 타입
객체 타입 | |객체, 함수, 배열 등

<br/>

## 숫자 타입
`C`나 `java`의 경우 숫자도 int, long, float, double 등 과 같은 다양한 숫자 타입을 제공한다.  
하지만 자바스크립트는 독특하게 하나의 숫자 타입만 존재한다.  
ECMAScript 사양에 따르면 숫자 타입의 값은 배정밀도 64비트 부동소수점 형식을 따른다.  
즉, 모든 수를 실수로 처리하며, 정수만 표현하기 위한 데이터 타입이 별도로 
존재하지 않는다.

```javascript
  // 모두 숫자 타입이다.
var integer = 10;    // 정수
var double = 10.12;  // 실수
var negative = -20;  // 음의 정수
```

```javascript
var binary = 0b01000001; // 2진수
var octal = 0o101;       // 8진수
var hex = 0x41;          // 16진수

// 표기법만 다를 뿐 모두 같은 값이다.
console.log(binary); // 65
console.log(octal);  // 65
console.log(hex);    // 65
console.log(binary === octal); // true
console.log(octal === hex);    // true
```
<br/>
정수처럼 보이지만 실수로 처리된다.
```javascript
// 숫자 타입은 모두 실수로 처리된다.
console.log(1 === 1.0); // true
console.log(4 / 2);     // 2
console.log(3 / 2);     // 1.5
```

```javascript
// 숫자 타입의 세 가지 특별한 값
console.log(10 / 0);       // Infinity
console.log(10 / -0);      // -Infinity
console.log(1 * 'String'); // NaN
```
<br/>

## 문자열 타입
문자열 타입`string`은 텍스트 데이터를 나타내는데 사용한다.  
문자열은 0개 이상의 16비트 유니코드 문자(UTF-16)의 집합으로 전 세계 대부분의 문자를 표현할수 있다.  
문자열은 작은따옴표(`''`), 큰따옴표(`""`), 백틱(` `` `)으로 텍스트를 감싼다.  
자바스크립트에서는 일반적으로 작은따옴표(`''`)를 사용한다.  
>참고: [에어비엔비 JavaScript Style Guide](https://github.com/airbnb/javascript#strings)

```javascript
// 문자열 타입
var string;
string = '문자열'; // 작은따옴표
string = "문자열"; // 큰따옴표
string = `문자열`; // 백틱 (ES6)

string = '작은따옴표로 감싼 문자열 내의 "큰따옴표"는 문자열로 인식된다.';
string = "큰따옴표로 감싼 문자열 내의 '작은따옴표'는 문자열로 인식된다.";
```
참고
`C`는 문자열 타입을 제공하지 않고 문자의 배열로 문자열을 표현
자바는 문자열을 객체로 표현
자바스크립트는 문자열은 원시타입이며, 변경 불가능한 값`immutable value`이다

<br/>

### 템플릿 리터럴
ES6부터 도입된 표기법 `template literal`  
템플릿 리터럴은 멀티라인 문자열`multi-line string`, 표현식 삽입`expression interpolation`, 태그드 템플릿`tagged template` 등 편리한 문자열 처리기능을 제공한다.  
백틱(` `` `)을 사용해 표현한다.
```javascript
var template = `Template literal`;
console.log(template); // Template literal
```
<br/>

#### 멀티라인 문자열
일반 문자열 내에서는 줄바꿈(개행)이 허용되지 않는다.  
하지만 템플릿 리터럴을 활용하면 줄바꿈도 포함할수 있다.
```javascript
var str = 'Hello
world.';
// SyntaxError: Invalid or unexpected token

var template = `Hello
world`;
console.log(template);
// Hello
// world
```

#### 표현식 삽입
문자열은 `+` 연산자를 사용해 연결 할 수 있는데,  
템플릿 리터럴에 표현식 삽입을 통해 `+` 연산자를 사용하지 않고도 간단히 문자열을 연결 할 수 있다.  
표현식을 삽입하려면 `${ }` 으로 표현식을 감싼다. 이때 표현식의 평가 결과가 문자열이 아니더라도 문자열로 타입이 강제로 변환되어 삽입된다.
```javascript
var first = 'Ung-mo';
var last = 'Lee';

// ES5: 문자열 연결
console.log('My name is ' + first + ' ' + last + '.'); // My name is Ung-mo Lee.

// ES6: 표현식 삽입
console.log(`My name is ${first} ${last}.`); // My name is Ung-mo Lee.

// 템플릿 리터럴 표현식 강제 형 변환
console.log(`1 + 2 = ${1 + 2}`); // 1 + 2 = 3

// 일반 문자열로 작성시 그냥 문자로 출력
console.log('1 + 2 = ${1 + 2}'); // 1 + 2 = ${1 + 2}
```
<br/>

## 불리언 타입
불리언 타입의 값은 true, false 뿐이다.
```javascript
var foo = true;
console.log(foo); // true

foo = false;
console.log(foo); // false
```
<br/>

## undefined 타입
undefined 타입의 값은 `undefined`가 유일하다.  
`var`키워드로 선언한 변수는 암묵적으로 `undefined`로 초기화 된다.  
변수를 선언한 이후 아무것도 할당하지 않은 변수를 참조하면 `undefined`가 반환된다.  
```javascript
var foo;
console.log(foo); // undefined
```
<br/>

## null 타입
`undefined`는 초기화 값이므로 의도적으로 `undefined`값을 할당한다면 본래 취지와 어긋나고 혼란을 줄 수 있으므로 권장하지 않는다.  
따라서 변수에 값이 없다는 것을 명시하고 싶을 때는 `null`을 할당한다.  
`null`을 할당하는 것은 변수가 이전에 참조하던 값을 더 이상 참조하지 않겠다는 의미이다.
```javascript
var foo = 'Lee';

// 이전에 할당되어 있던 값에 대한 참조를 제거. foo 변수는 더 이상 'Lee'를 참조하지 않는다.
// 유용해 보이지는 않는다. 변수의 스코프를 좁게 만들어 변수 자체를 재빨리 소멸시키는 편이 낫다.
foo = null;
```
<br/>

## 심벌 타입
ES6에 새롭게 추가된 7번째 타입으로, 변경 불가능한 원시 타입의 값이다.  
심벌 값은 다른 값과 중복되지 않는 유일 무이한 값이다.  
그래서 보통 이름이 충돌할 위험이 없는 객체의 유일한 프로퍼티 키를 만들기 위해 사용된다.  
`Symbol`함수를 호출해 생성한다. 이때 생성된 심벌값은 외부에 노출되지 않으면, 다른값과 절대 중복되지 않는 유일무이한 값이다.
```javascript
// 심벌 값 생성
var key = Symbol('key');
console.log(typeof key); // symbol

// 객체 생성
var obj = {};

// 이름이 충돌할 위험이 없는 유일무이한 값인 심벌을 프로퍼티 키로 사용한다.
obj[key] = 'value';
console.log(obj[key]); // value

// 같은 형태로 생성해도 다른 값이다
Symbol('a') === Symbol('a') // false
```
<br/>

## 객체 타입
지금까지 살펴본 6가지 타입 이외의 값은 모두 객체 타입이다.

<br/>

## 데이터 타입의 필요성
 - 값을 저장할 때 확보해야 하는 **메모리 공간의 크기**를 결정하기 위해
 - 값을 참조할 때 한 번에 읽어 들여야 할 **메모리 공간의 크기**를 결정하기 위해
 - 메모리에서 읽어 들인 **2진수를 어떻게 해석**할지 결정하기 위해

<br/>

## 동적 타이핑

###  정적 타입 언어
`C`나 `java`는 정적타입(`static type`)언어 이다. 따라서 변수를 선언할 때 어떤 타입인지 사전에 지정해 줘야 한다. 이를 명시적 타입 선언(`explicit type declaration`)이라 한다.  
정적 타입 언어는 컴파일 시점에 타입체크를 수행한다. 만약 타입체크를 통과하지 못하면 에러를 발생시키고 컴파일이 안된다.  
```c
// c 변수에는 1바이트 정수 타입의 값(-128 ~ 127)만을 할당할 수 있다.
char c;

// num 변수에는 4바이트 정수 타입의 값(-2,124,483,648 ~ 2,124,483,647)만을 할당할 수 있다.
int num;
```

### 동적 타입 언어
이와 반대로 자바스크립트 언어는 변수를 선언할 때 타입을 선언하지 않는다. 다만 `var`, `let`, `const` 키워드를 사용해 변수를 선언 할 뿐이다.  
자바스크립트의 변수는 정적 타입 언어와 같이 미리 선언한 데이터 타입의 값만 할당할 수 있는 것이 아니고, 어떤 데이터 타입이라도 자유롭게 할당할 수 있다.
아래 예제는 `var`로 선언한 `foo`변수에 어떤 타입이든 자유롭게 할당하는 것을 보여준다.
```javascript
var foo;
console.log(typeof foo);  // undefined

foo = 3;
console.log(typeof foo);  // number

foo = 'Hello';
console.log(typeof foo);  // string

foo = true;
console.log(typeof foo);  // boolean

foo = null;
console.log(typeof foo);  // object

foo = Symbol(); // 심벌
console.log(typeof foo);  // symbol

foo = {}; // 객체
console.log(typeof foo);  // object

foo = []; // 배열
console.log(typeof foo);  // object

foo = function () {}; // 함수
console.log(typeof foo);  // function
```

> 타입 추론(type inference) : 자바스크립트의 변수는 선언이 아닌 할당에 의해 타입이 결정 된다.

### 동적 타입 언어 장단점
동적 타입 언어는 변수에 어떤 데이터 타입의 값이라도 자유롭게 할당할 수 있어 데이터 타입에 대해 무감각해질 정도로 편리하다.  
하지만 이로 인해 복잡한 프로그램에서는 변수 값을 추적하기 어렵다. 따라서 변수값을 확인하기 전에는 타입을 확신할 수 없다.  
게다가 자바스크립트 엔진에 의해 암묵적으로 타입이 자동 변환되는 `암묵적 타입 변환`이 있어 개발자의 의도와 상관없이 타입이 바뀌어 프로그램이 오류를 뿜어 낼 수 도 있다.

> 동적 타입 언어 사용시 주의사항
> - 변수는 꼭 필요한 경우에 한해 제한적으로 사용한다.
> - 변수의 유효 범위는 최대한 좁게 만들어 변수의 부작용을 억제해야 한다.
> - 전역 변수는 최대한 사용하지 않도록 한다.
> - 변수보다는 상수를 사용해 값의 변경을 억제한다.
> - 변수 이름은 변수의 목적이나 의미를 파악할 수 있도록 네이밍한다.

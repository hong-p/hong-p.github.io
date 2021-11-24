---
title: "모던 자바스크립트 딥 다이브 13장 스코프"
excerpt: "[javascript] 모던 자바스크립트 Deep Dive 13장 스코프"
categories:
 - Javascript
tags:
 - Javascript
toc: true
toc_sticky: true
last_modified_at: 2021-11-21
---

# 스코프

## 1.스코프란?
식별자가 유효한 범위  
식별자 자신이 선언된 위치에 의해 다른 코드가 자신을 참조할 수 있는 유효 범위  

```javascript
var var1 = 1; // 코드의 가장 바깥 영역에서 선언한 변수

if (true) {
  var var2 = 2; // 코드 블록 내에서 선언한 변수
  if (true) {
    var var3 = 3; // 중첩된 코드 블록 내에서 선언한 변수
  }
}

function foo() {
  var var4 = 4; // 함수 내에서 선언한 변수

  function bar() {
    var var5 = 5; // 중첩된 함수 내에서 선언한 변수
  }
}

console.log(var1); // 1
console.log(var2); // 2
console.log(var3); // 3
console.log(var4); // ReferenceError: var4 is not defined
console.log(var5); // ReferenceError: var5 is not defined
```

<br>

```javascript
var x = 'global';

function foo() {
  var x = 'local';
  console.log(x); // local
}

foo();

console.log(x); // global
```
전역에 선언된 `var x = 'global';`과 `foo`함수 내에 선언된 `var x = 'local';`은 식별자 이름은 같지만 스코프는 다르다.  
자바스크립트 엔진은 식별자를 결정할 때 "식별자를 검색할 때 사용하는 규칙"이 있다.  
이때 코드의 문맥과 환경을 고려한다.

>*※코드의 문맥과 환경*  
렉시컬 환경: 코드가 어디서 실행되며 어떤 코드가 있는지 코드의 문맥을 이루는 환경  
실행 컨텍스트: 렉시컬 환경을 구현한 것. 모든 코드를 평가하고 실행한다.

식별자의 이름은 유일해야 하지만 스코프를 통해 식별자 이름의 충돌을 방지할 수 있다.  

`var`키워드로 선언된 변수는 같은 스코프 내에서 중복 선언이 허용된다.
```javascript
function foo() {
  var x = 1;
  // var 키워드로 선언된 변수는 같은 스코프 내에서 중복 선언을 허용한다.
  // 아래 변수 선언문은 자바스크립트 엔진에 의해 var 키워드가 없는 것처럼 동작한다.
  var x = 2;
  console.log(x); // 2
}
foo();
```
반면 `let`이나 `const`는 중복 선언을 허용하지 않는다.
```javascript
function bar() {
  let x = 1;
  // let이나 const 키워드로 선언된 변수는 같은 스코프 내에서 중복 선언을 허용하지 않는다.
  let x = 2; // SyntaxError: Identifier 'x' has already been declared
}
bar();
```

<br>

## 2.스코프의 종류

구분|설명|스코프|변수
:---:|:---:|:---:|:---:
전역|코드의 가장 바깥 영역|전역 스코프|전역 변수
지역|함수 몸체 내부|지역 스코프|지역 변수

### 2.1 전역과 전역 스코프 
전역이란 코드의 가장 바깥 영역  
전역은 전역 스코프`global scope`를 만든다.  
전역 변수`global variable`는 어디서든 참조할 수 있다.  

### 2.2 지역과 지역 스코프
지역이란 함수 몸체 내부를 말한다.(자바스크립트는 함수 스코프를 따르기 때문)  
지역은 지역 스코프를(`local scope`) 만든다.  
지역 변수는 자신의 지역 스코프와 하위 지역 스코프에서 유효하다.  

<br>

## 3.스코프 체인
함수는 중첩될 수 있어 함수의 지역 스코프도 중첩될 수 있다.  
함수가 중첩되면 **스코프도 계층적인 구조**를 갖는다.  

변수를 참조할 때 자바스크립트 엔진은 소코프 체인을 통해 변수를 참조하는 코드의 스코프에서 시작해 상위 스코프의 방향으로 이동하며 선언된 변수를 검색`identifier resolution` 한다.  

스코프 체인은 실행 컨텍스트의 렉시컬 환경을 단방향으로 연결한 것이다. 자세한 사항은 [렉시컬 환경](https://hong-p.github.io/javascript/javascript-deepdive-ch23/#5%EB%A0%89%EC%8B%9C%EC%BB%AC-%ED%99%98%EA%B2%BD)을 참고하자.


```javascript
// 전역 함수
function foo() {
  console.log('global function foo');
}

function bar() {
  // 중첩 함수
  function foo() {
    console.log('local function foo');
  }

  foo(); // ①
}

bar();
```
①에서 호출한 `foo`함수는 전역에 선언한 `foo`함수가 아니라 `bar`함수 내부에 선언한 `foo`함수를 호출 하는 것이다.  
①에서 `foo();`를 호출하면 현재 실행 중인 실행컨텍스트(`bar`함수)의 렉시컬 환경에서 `foo`식별자를 먼저 검색한다. 있으면 그 식별자를 참조하고, 없는경우 외부 렉시컬환경(상위 렉시컬 환경)으로 가서 찾기를 반복한다.  
이를 **식별자를 검색하는 규칙**이라고 표현한다.  

<br>

## 4.함수 레벨 스코프
지역은 함수 몸체 내부를 말하고 지역은 지역 스코프를 만든다. 지역 스코프는 함수에 의해서 생성된 스코프이다.  
if, for, while try/catch 등의 코드 블록은 블록 레벨 스코프를 만든다.  

```javascript
var x = 1;

if (true) {
  // var 키워드로 선언된 변수는 함수의 코드 블록(함수 몸체)만을 지역 스코프로 인정한다.
  // 함수 밖에서 var 키워드로 선언된 변수는 코드 블록 내에서 선언되었다 할지라도 모두 전역 변수다.
  // 따라서 x는 전역 변수다. 이미 선언된 전역 변수 x가 있으므로 x 변수는 중복 선언된다.
  // 이는 의도치 않게 변수 값이 변경되는 부작용을 발생시킨다.
  var x = 10;
}

console.log(x); // 10
```
위 예제에서 처음에 선언한 `var x = 1;`의 `x`변수는 전역변수이고, `if`문 안에 선언된 `var x = 10`또한 전역 변수로 등록된다.  
`var`키워드는 함수 레벨 스코프만 인정하기 때문이다.  
따라서 위 코드는 `x`가 선언되고 `1`이 할당된뒤 `if`문 내의 `var x = 10;`을 만나면 `var`선언은 무시되고 `x = 10` 할당 표현식만 실행되어 `x`의 값에 `10`이 할당된다.  

```javascript
var i = 10;

// for 문에서 선언한 i는 전역 변수다. 이미 선언된 전역 변수 i가 있으므로 중복 선언된다.
for (var i = 0; i < 5; i++) {
  console.log(i); // 0 1 2 3 4
}

// 의도치 않게 변수의 값이 변경되었다.
console.log(i); // 5
```
위 예제에서도 마찬가지로 `for`문 안에에서 `var i = 0`으로 `i`변수를 선언했지만 전역 변수로 등록되어 `for`문 바깥에서도 참조할 수 있다.

<br>

## 5.렉시컬 스코프

```javascript
var x = 1;

function foo() {
  var x = 10;
  bar();
}

function bar() {
  console.log(x);
}

foo(); // ?
bar(); // ?
```
결과는 `foo() // 1`,`bar() // 1` 둘다 1이 출력된다.  
이유는 함수를 어디서 호출하는지에 따라 상위 스코프가 결정되는 것 이아니고,  
**함수를 어디에 정의했는지에 따라 함수의 상위 스코프가 결정**되기 때문이다.  

함수가 호출된 위치는 상위 스코프 결정에 어떠한 영향도 주지 않는다.  
언제나 함수가 정의된 위치가 중요하다. 즉, 렉시컬 스코프를 따른다는 것이다.

> ※ 렉시컬(lexical) : 어휘의, 사전의

<br>

만약 `bar`함수를 호출했을 때 `10`을 출력하고 싶으면 아래와 같이 `foo`함수안에 `bar`함수를 선언하여 중첩 함수를 만들고 `foo`안에서 `bar`를 호출하면 `bar`함수의 상위 스코프인 `foo`함수에 선언된 `x`가 참조되어 `10`이 출력된다.(클로저 개념)  
다만 `bar`함수를 `foo`함수 바깥에서는 호출할 수 없다. 그럴경우 `Uncaught ReferenceError: bar is not defined` 에러가 발생한다.
```javascript
var x = 1;

function foo() {
  var x = 10;
  function bar() {
    console.log(x);
  }
  bar();
}

foo(); // ?
bar(); // ?
```

<br>

퀴즈?
```javascript
var x = 1;

function foo() {
  var x = 10;
  bar();
}

foo(); // ?

bar = function() {
  console.log(x);
}

bar(); // ?
```

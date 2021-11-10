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
원시 타입 | 숫자 타입 | 숫자. 정수와 실수 구분 없이 하나의 숫자 타입만 존재
  <br> | 문자열 타입 | 문자열
  <br> | 불리언 타입 | 논리적 참(true)와 거짓(false)
  <br> | undefined 타입 | var 키워드로 선언된 변수에 암묵적으로 할당되는 값
  <br> | null 타입 | 값이 없다는 것을 의도적으로 명시할 때 사용하는 값
  <br> | 심벌 타입 | ES6에서 추가된 7번째 타입
  객체 타입 | |객체, 함수, 배열 등
<br/>
## 숫자 타입
C나 자바의 경우 숫자도 int, long, float, double 등 과 같은 다양한 숫자 타입을 제공한다.  
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

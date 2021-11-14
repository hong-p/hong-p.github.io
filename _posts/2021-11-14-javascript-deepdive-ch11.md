---
title: "모던 자바스크립트 딥 다이브 ch11 원시 값과 객체의 비교"
excerpt: "[javascript] 모던 자바스크립트 Deep Dive ch11 원시 값과 객체의 비교"
categories:
 - Javascript
tags:
 - Javascript
toc: true
toc_sticky: true
last_modified_at: 2021-11-14
---

# 타입 변환과 단축 평가


원시값과 래퍼 객체
마침표 표기법(또는 대괄호 표기법)으로 접근하면 자바스크립트 엔진이 일시적으로 원시값을 연관된 객체로 변환해 준다. 즉, 원시값을 객체 처럼 사용하면 자바스크립트 엔진이 암묵적으로 연관된 객체를 생성하여 생성된 객체로 프로퍼티에 접근하거나 메서드를 호출한다. 작업을 마친 후 다시 원시값으로 되돌린다.  

문자열, 숫자, 불리언 값에 대해 객체처럼 접근하면 생성되는 임시 객체를 `래퍼 객체(wrapper object)`라고 한다.

```javascript
const str = 'hello';

// 원시 타입인 문자열이 프로퍼티와 메서드를 갖고 있는 객체처럼 동작한다.
console.log(str.length); // 5
console.log(str.toUpperCase()); // HELLO
```

```javascript
const str = 'hi';

// 원시 타입인 문자열이 래퍼 객체인 String 인스턴스로 변환된다.
console.log(str.length); // 2
console.log(str.toUpperCase()); // HI

// 래퍼 객체로 프로퍼티에 접근하거나 메서드를 호출한 후, 다시 원시값으로 되돌린다.
console.log(typeof str); // string
```

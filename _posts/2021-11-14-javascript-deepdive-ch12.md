---
title: "[javascript] 모던 자바스크립트 Deep Dive ch12 함수"
excerpt: "모던 자바스크립트 딥다이브 ch12 함수"
categories:
 - Javascript
tags:
 - Javascript
toc: true
toc_sticky: true
last_modified_at: 2021-11-14
---

# 타입 변환과 단축 평가

12-08
```javascript
// 기명 함수 리터럴을 단독으로 사용하면 함수 선언문으로 해석된다.
// 함수 선언문에서는 함수 이름을 생략할 수 없다.
function foo() { console.log('foo'); }
foo(); // foo

// 함수 리터럴을 피연산자로 사용하면 함수 선언문이 아니라 함수 리터럴 표현식으로 해석된다.
// 함수 리터럴에서는 함수 이름을 생략할 수 있다.
(function bar() { console.log('bar'); });
bar(); // ReferenceError: bar is not defined

// 아래와 같이 작성해야 호출 가능하다.
bar = (function bar(){ console.log('bar'); });
bar(); // bar
```
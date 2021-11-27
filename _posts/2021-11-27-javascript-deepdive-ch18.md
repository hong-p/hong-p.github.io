---
title: "모던 자바스크립트 딥 다이브 18장 함수와 일급 객체"
excerpt: "[javascript] 모던 자바스크립트 Deep Dive 18장 함수와 일급 객체"
categories:
 - Javascript
tags:
 - Javascript
toc: true
toc_sticky: true
last_modified_at: 2021-11-27
---

# 18장 함수와 일급 객체

`arguments`객체는 유사 배열 객체이면서 동시에 이터러블`iterable`이다.

`iterable`객체는 `for...in`, `for...of`문을 사용할 수 있다.
```javascript
function foo(){
    for(let a in arguments){
        console.log(a);
    }
}
foo(1,2,3);
// 1
// 2
// 3
```

---
title: "모던 자바스크립트 딥 다이브 23장 실행 컨텍스트"
excerpt: "[javascript] 모던 자바스크립트 Deep Dive 23장 실행 컨텍스트"
categories:
 - Javascript
tags:
 - Javascript
toc: true
toc_sticky: true
last_modified_at: 2021-11-21
---

```javascript
for(var i=0;i <5; i++){
    setTimeout(function(){console.log(i)},1)
}
// 5
// 5
// 5
// 5
// 5
```
```javascript
for(let i=0;i <5; i++){
    setTimeout(function(){console.log(i)},1)
}
// 0
// 1
// 2
// 3
// 4
```

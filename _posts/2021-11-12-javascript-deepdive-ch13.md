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

```javascript
var x = 1;

function foo() {
  var x = 10;
  bar();
}
foo();
bar =function() {
  console.log(x);
}

// foo(); // ?
bar(); // ?
```

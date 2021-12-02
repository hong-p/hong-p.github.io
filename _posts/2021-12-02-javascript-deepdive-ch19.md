---
title: "모던 자바스크립트 딥 다이브 19장 프로토타입"
excerpt: "[javascript] 모던 자바스크립트 Deep Dive 19장 프로토타입"
categories:
 - Javascript
tags:
 - Javascript
toc: true
toc_sticky: true
last_modified_at: 2021-12-02
---

# 19장 프로토타입

## 14. 프로퍼티 열거
```javascript
function Person(){

}
Person.prototype.name = 'inyong';

var p = new Person();
p.age = 10;
for(v in p) console.log(v);
// age
// name

// 본인 것만 출력하려면"
for(v in p){ 
    if(p.hasOwnProperty(v))
        console.log(v)
}
// age


Object.getOwnPropertyDescriptor(Object.prototype, "toString")
// {writable: true, enumerable: false, configurable: true, value: ƒ}


Object.defineProperty(Person.prototype, 'firstName', {
  value: 'Inyong',
  writable: true,
  enumerable: false,
  configurable: true
});

Object.defineProperty(Person.prototype, 'lastName', {
  value: 'Hong',
  writable: true,
  enumerable: true,
  configurable: true
});

for(v in p) console.log(v);
// age
// name
// lastName

```
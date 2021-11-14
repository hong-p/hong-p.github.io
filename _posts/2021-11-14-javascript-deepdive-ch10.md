---
title: "[javascript] 모던 자바스크립트 Deep Dive ch10 객체 리터럴"
excerpt: "모던 자바스크립트 딥다이브 ch10 객체 리터럴"
categories:
 - Javascript
tags:
 - Javascript
toc: true
toc_sticky: true
last_modified_at: 2021-11-14
---

# 타입 변환과 단축 평가


```javascript
class Animal {
  constructor(name) {
    this.speed = 0;
    this.name = name;
  }
  run(speed) {
    this.speed = speed;
    alert(`${this.name} 은/는 속도 ${this.speed}로 달립니다.`);
  }
  stop() {
    this.speed = 0;
    alert(`${this.name} 이/가 멈췄습니다.`);
  }
}
class Rabbit extends Animal {
  hide() {
    alert(`${this.name} 이/가 숨었습니다!`);
  }
}
let animal = new Animal("동물");
let rabbit = new Rabbit("흰 토끼");
```
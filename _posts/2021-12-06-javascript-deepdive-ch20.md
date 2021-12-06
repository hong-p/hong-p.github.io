---
title: "모던 자바스크립트 딥 다이브 20장 strict mode"
excerpt: "[javascript] 모던 자바스크립트 Deep Dive 20장 strict mode"
categories:
 - Javascript
tags:
 - Javascript
toc: true
toc_sticky: true
last_modified_at: 2021-12-06
---

# 20장 strict mode

## 1.strict mode란?
자바스크립트 언어의 문법을 좀 더 엄격히 적용하여 오류를 발생시킬 가능성이 높거나 자바스크립트 엔진의 최적화 작업에 **문제를 일으킬 수 있는 코드에 대한 명시적인 에러를 발생**시킨다.  

```javascript
function foo() {
  x = 10;
}
foo();

console.log(x); // ?
```

위 예제를 실행하면 결과는 `10`이 출력된다.  
`x = 10` 코드를 만나면 자바스크립트 엔진은 `foo`함수 스코프에서 `x`식별자를 검색한다.  
`foo`함수 스코프에 없으면 스코프 체인을 통해 상위 스코프에서 `x` 변수를 검색한다. 즉, 전역 스코프에서 검색하는데, 전역 스코프에 없으면 `RefferenceError`가 발생해야 하는데 자바스크립트 엔진은 암묵적으로 전역 객체에 `x`프로퍼티를 동적으로 생성한다. 이를 **암묵적 전역**`implicit global`이라 한다.  

이런 현상은 개발자의 의도와 다르게 동작할 우려가 있어 좋지 않다.  
따라서 반드시 `var`, `let`, `const`키워드를 사용하여 변수를 선언한 다음 사용해야 한다. (키워드만 적어줘도 암묵적 전역은 발생하지 않는다.)   


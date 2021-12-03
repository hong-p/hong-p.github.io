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

## 1.일급 객체

※ 일급 객체의 조건
1. 무명 리터럴로 생성할 수 있다. 즉, 런타임에 생성이 가능하다.
2. 변수나 자료구조(객체, 배열)등에 저장할 수 있다.
3. 함수의 매개변수에 전달할 수 있다.
4. 함수의 반환값으로 사용할 수 있다.

```javascript
// 1. 함수는 무명의 리터럴로 생성할 수 있다.
// 2. 함수는 변수에 저장할 수 있다.
// 런타임(할당 단계)에 함수 리터럴이 평가되어 함수 객체가 생성되고 변수에 할당된다.
const increase = function (num) {
  return ++num;
};

const decrease = function (num) {
  return --num;
};

// 2. 함수는 객체에 저장할 수 있다.
const auxs = { increase, decrease };

// 3. 함수의 매개변수에게 전달할 수 있다.
// 4. 함수의 반환값으로 사용할 수 있다.
function makeCounter(aux) {
  let num = 0;

  return function () {
    num = aux(num);
    return num;
  };
}

// 3. 함수는 매개변수에게 함수를 전달할 수 있다.
const increaser = makeCounter(auxs.increase);
console.log(increaser()); // 1
console.log(increaser()); // 2

// 3. 함수는 매개변수에게 함수를 전달할 수 있다.
const decreaser = makeCounter(auxs.decrease);
console.log(decreaser()); // -1
console.log(decreaser()); // -2
```

- 함수를 객체와 동일하게 사용할 수 있다.
- 객체는 값이므로 함수도 값으로 동일하게 취급할 수 있다.
- 변수 할당문, 객체의 프로퍼티 값, 배열의 요소, 함수 호출의 인수, 함수 반환문에 리터럴로 정의할 수 있다.
- 함수의 매개변수에 전달할 수 있다.
- 함수의 반환값으로 사용할 수 있다.
- 반면, 일반 객체는 호출할 수 없지만, 함수 객체는 호출할 수 없다.

## 2.함수 객체의 프로퍼티
함수도 객체이기 때문에 프로퍼티를 가질 수 있다.  
`console.dir`로 함수 내부를 볼 수 있다.  

```javascript
function square(number) {
  return number * number;
}

console.dir(square);
```

`Object.getOwnPropertyDescriptors`메서드로 함수의 모든 프로퍼티의 프로퍼티 어트리뷰트를 확인할 수 있다.

```javascript
function square(number) {
  return number * number;
}

console.log(Object.getOwnPropertyDescriptors(square));
/*
{
  length: {value: 1, writable: false, enumerable: false, configurable: true},
  name: {value: "square", writable: false, enumerable: false, configurable: true},
  arguments: {value: null, writable: false, enumerable: false, configurable: false},
  caller: {value: null, writable: false, enumerable: false, configurable: false},
  prototype: {value: {...}, writable: true, enumerable: false, configurable: false}
}
*/

// __proto__는 square 함수의 프로퍼티가 아니다.
console.log(Object.getOwnPropertyDescriptor(square, '__proto__')); // undefined

// __proto__는 Object.prototype 객체의 접근자 프로퍼티다.
// square 함수는 Object.prototype 객체로부터 __proto__ 접근자 프로퍼티를 상속받는다.
console.log(Object.getOwnPropertyDescriptor(Object.prototype, '__proto__'));
// {get: ƒ, set: ƒ, enumerable: false, configurable: true}
```

`__proto__`는 접근자 프로퍼티이며, 함수 객체 고유의 프로퍼티가 아니라 `Object.prototype`객체의 프로퍼티를 상속받은 것이다.

### 2.1 arguments 프로퍼티
함수 객체의 `arguments`프로퍼티 값은 `arguments`객체다.  
`arguments`객체는 함수 호출 시 전달된 인수(`arguments`)들의 정보를 담고 있는 순회 가능한(`iterable`)유사 배열 객체이다.  
함수 내부에서 지역 변수처럼 사용된다.  

`arguments`프로퍼티는 ES3부터 폐지되어 일부 브라우저에서만 지원하고 있다.  
따라서 `Function.arguments`와 같은 사용법은 권장지 않음.  

자바스크립트는 매개변수와 인수의 개수가 일치하는지 확인하지 않는다.  
따라서 함수 호출시 매개변수 개수만큼 인수를 전달하지 않아도 에러가 발생하지 않는다.  
선언된 매개변수의 개수보다 인수를 적게 전달한경우 `undefined`으로 초기화된 상태로 유지된다.  
초과된 인수는 무시된다.(but, `arguments`객체에는 보관됨)  


```javascript
function multiply(x, y) {
  console.log(arguments);
  return x * y;
}

console.log(multiply());        // NaN
console.log(multiply(1));       // NaN
console.log(multiply(1, 2));    // 2
console.log(multiply(1, 2, 3)); // 2
```
![이미지 1](https://user-images.githubusercontent.com/80154058/144547000-c1131d91-5c40-490a-b977-af58cafaa2e9.png)

- 프로퍼티 키는 인수의 순서를 나타낸다(`0: 1`, `1: 2`, `2: 3`)
- `callee`프로퍼티는 `arguments`객체를 생성한 함수, 즉 함수 자신을 가리킨다.
- `length`프로퍼티는 인수의 개수를 가리킨다.

<br>

※ **`arguments` 객체의 `Symbol(Symbol.iterator)`프로퍼티**  
`Symbol(Symbol.iterator)`프로퍼티는 `arguments`객체를 순회 가능한 자료구조인 이터러블(`iterable`)로 만들어 주는 프로퍼티다.

```javascript
function multiply(x, y) {
  // 이터레이터
  const iterator = arguments[Symbol.iterator]();

  // 이터레이터의 next 메서드를 호출하여 이터러블 객체 arguments를 순회
  console.log(iterator.next()); // {value: 1, done: false}
  console.log(iterator.next()); // {value: 2, done: false}
  console.log(iterator.next()); // {value: 3, done: false}
  console.log(iterator.next()); // {value: undefined, done: true}

  return x * y;
}

multiply(1, 2, 3);
```

`arguments`객체는 매개변수 개수를 확정할 수 없는 가변 인자 함수를 구현할 때 유용하다.  
```javascript
function sum() {
  let res = 0;

  // arguments 객체는 length 프로퍼티가 있는 유사 배열 객체이므로 for 문으로 순회할 수 있다.
  for (let i = 0; i < arguments.length; i++) {
    res += arguments[i];
  }

  return res;
}

console.log(sum());        // 0
console.log(sum(1, 2));    // 3
console.log(sum(1, 2, 3)); // 6
```



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

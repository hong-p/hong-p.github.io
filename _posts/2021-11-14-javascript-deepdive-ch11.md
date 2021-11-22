---
title: "모던 자바스크립트 딥 다이브 11장 원시 값과 객체의 비교"
excerpt: "[javascript] 모던 자바스크립트 Deep Dive 11장 원시 값과 객체의 비교"
categories:
 - Javascript
tags:
 - Javascript
toc: true
toc_sticky: true
last_modified_at: 2021-11-14
---

# 원시 값과 객체의 비교

데이터 타입

` ` |원시 타입(`primitive type`) | 객체 타입(`object/reference type`)
:---:|:---:|:---:
값|변경 불가능한 값`immutable value` | 변경 가능한 값`mutable value`
할당 시|변수에는 실제 값이 저장 | 변수에는 참조 값이 저장
다른 변수에 할당시 | 원시 값이 복사 되어 전달 | 참조 값이 복사되어 전달

## 1.원시 값
### 1.1 변경 불가능한 값
원시 타입`primitive type`의 값은 변경 불가능한 값`immutable value`이다. 한번 생성된 원시값은 읽기 전용`read only`값으로서 변경할 수 없다.  
변경 불가능 하다는 것은 변수가 아니라 값에 대한 진술이다.    
변수는 언제든지 재할당을 통해 변수 값을 변경할 수 있다.  
변수 값을 변경하기 위해 원시 값을 재할당하면 새로운 메모리 공간을 확보하고 재할당한 값을 저장한 후, 변수가 참조하던 메모리 공간의 주소를 변경한다. 값의 이러한 특성을 **불변성**`immutability`라고 한다.

### 1.2 문자열과 불변성

> ECMAScript 사양에 따르면 원시 타입의 크기를 아래와 같이 규정하고 있다.  
> 문자열 타입: 2바이트  
> 숫자 타입: 8바이트  
> 이외의 원시 타입은 규정되어 있지 않아 브라우저 제조사의 구현에 따라 다를 수 있다.

```javascript
// 문자열은 0개 이상의 문자들로 이뤄진 집합이다.
var str1 = '';      // 0개의 문자로 이뤄진 문자열(빈 문자열)
var str2 = 'Hello'; // 5개의 문자로 이뤄진 문자열
```

```javascript
var str = 'Hello';
str = 'world';
```

첫 번째 문이 실행되면 문자열 `'Hello'`가 생성되고 식별자 `str`은 문자열 `'Hello'`가 저장된 메모리 공간의 첫 번째 메모리 셀 주소를 가리킨다.  
그리고 두 번째 문이 실행되면 이전에 생성된 문자열 `Hello`를 수정하는 것이 아니라 새로운 문자열 `world`를 메모리에 생성하고 식별자 `str`은 이것을 가리킨다. 이때 문자열 `'Hello'`와 `'world'`는 모두 메모리에 존재한다.  
식별자 `str`은 문자열 `'Hello'`를 가리키고 있다가 문자열 `'world'`를 가키리도록 변경되었을 뿐이다.  

<br>

*※유사 배열 객체*  
유사 배열 객체란 마치 배열처럼 인덱스로 프로퍼티 값에 접근할 수 있고 `length` 프로퍼티를 갖는 객체를 말한다.  
문자열은 마치 배열처럼 인덱스를 통해 각 문자에 접글할 수 있으며, `length` 프로퍼티를 갖기 때문에 유사 배열 객체이고 `for`문으로 순회할 수 도 있다.

```javascript
var str = 'string';

// 문자열은 유사 배열이므로 배열과 유사하게 인덱스를 사용해 각 문자에 접근할 수 있다.
console.log(str[0]); // s

// 원시 값인 문자열이 객체처럼 동작한다.
console.log(str.length); // 6
console.log(str.toUpperCase()); // STRING
```

원시값인 문자열이 객체처럼 동작한다는 것은 래퍼 객체(`wrapper object`)로 자동 변환되기 때문인데 아래를 참고하자.

<br>

*※원시값과 래퍼 객체*  
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

위 예제에서 `str.toUpperCase()`이 부분을 풀어서 보면 아래와 같이 temp객체를 만들어서  
`String` 생성자 함수로 임시 객체를 만들고 `String`객체가 가지고 있는 `toUpperCase()`함수를 사용해 그 결과를 리턴해 주는식으로 동작한다.
```javascript
const str = 'hello';
// str.toUpperCase()
const temp = new String(str);
temp.toUpperCase()
```


### 1.3 값에 의한 전달
변수`copy`에 원시 값을 갖는 변수`score`를 할당하면 할당받는 변수`copy`에는 할당되는 변수`score`의 원시 값이 복사되어 전달된다.

```javascript
var score = 80;
var copy = score;

console.log(score); // 80
console.log(copy);  // 80
```

```javascript
var score = 80;

// copy 변수에는 score 변수의 값 80이 복사되어 할당된다.
var copy = score;

console.log(score, copy); // 80  80
console.log(score === copy); // true
```
![스크린샷 2021-11-14 오후 4 30 30](https://user-images.githubusercontent.com/80154058/141671974-8a439716-5a17-4443-9e7b-65db3d130670.png)

`score`변수와 `copy`변수의 값 80은 다른 메모리 공간에 저장된 별개의 값이다.

<br>

```javascript
var score = 80;

// copy 변수에는 score 변수의 값 80이 복사되어 할당된다.
var copy = score;

console.log(score, copy);    // 80  80
console.log(score === copy); // true

// score 변수와 copy 변수의 값은 다른 메모리 공간에 저장된 별개의 값이다.
// 따라서 score 변수의 값을 변경해도 copy 변수의 값에는 어떠한 영향도 주지 않는다.
score = 100;

console.log(score, copy);    // 100  80
console.log(score === copy); // false
```
`score`변수의 값을 100으로 변경하더라도 `copy`변수에는 어떠한 영향도 주지 않는다.

<br>

## 2.객체
자바스크립트 객체는 프로퍼티의 개수가 정해져 있지 않고, 동적으로 추가되고 삭제 가능하다. 또한 프로퍼티의 값에도 제약이 없다.  
원시 값과 같이 확보해야 할 메모리 공간의 크기를 사전에 정해 둘 수 없다.

>*※객체의 관리 방식*  
`java`, `c++`같은 클래스 기반 객체지향 프로그래밍 언어는 사전에 정의된 클래스를 기반으로 객체를 생성한다. 객체가 생성된 이후에는 프로퍼티를 삭제하거나 추가할 수 없다.  
하지만 자바스크립트는 클래스 없이 객체를 생성할수 있으며, 생성된 이후에도 동적으로 프로퍼티와 메서드를 추가할 수 있다.  
이는 매우 편리하지만 성능 면에서는 이론적으로 클래스 기반 언어 보다 생성과 접근에 비용이 더 많이 드는 비효율적인 방식이다.  
v8 자바스크립트 엔진에서는 히든 클래스`hidden class`라는 방식을 사용해 `c++`객체의 프로퍼티에 접근하는 정도의 성능을 보장한다.

### 2.1 변경 가능한 값
객체 타입의 값, 즉 객체는 변경 가능한 값`mutable vlaue`이다. 
```javascript
var person = {
  name: 'Lee'
};
```
`person` 변수는 참조 값(`reference value`)에 접근할 수 있는 메모리 주소를 기억하고 있다.

![스크린샷 2021-11-14 오후 4 00 05](https://user-images.githubusercontent.com/80154058/141670958-33f78d5d-e625-4079-8815-cadf3d8c8ac0.png)

원시 값을 할당한 변수를 참조하면 메모리에 저장되어 있는 원시 값에 접근한다.  
하지만 객체를 할당한 변수`person`를 참조하면 메모리에 저장되어 있는 참조 값(`0x00...1`)을 통해 실제 객체`{name: 'LEE'}`에 접근한다.

```javascript
// 할당이 이뤄지는 시점에 객체 리터럴이 해석되고, 그 결과 객체가 생성된다.
var person = {
  name: 'Lee'
};

// person 변수에 저장되어 있는 참조값으로 실제 객체에 접근해서 그 객체를 반환한다.
console.log(person); // {name: "Lee"}
```
<br>

```javascript
var person = {
  name: 'Lee'
};

// 프로퍼티 값 갱신
person.name = 'Kim';

// 프로퍼티 동적 생성
person.address = 'Seoul';

console.log(person); // {name: "Kim", address: "Seoul"}
```
![스크린샷 2021-11-14 오후 4 04 56](https://user-images.githubusercontent.com/80154058/141671078-cde1501b-ae06-4d53-bc73-dc28a4551781.png)

객체는 변경 가능한 값이므로 메모리에 저장된 객체를 직접 수정할 수 있다.  
재할당이 필요없다.  
프로퍼티를 동적으로 추가할 수 있고, 갱신할 수 있다.

<br>

**객체를 복사해 성성하는 비용을 절약하여 성능을 향상시키기 위해 객체는 변경 가능한 값으로 설계되어 있다.**  
하지만 이러한 구조적 단점에 따른 부작용이 있다. 그것은 원시 값과는 다르게 **여러개의 식별자가 하나의 객체를 공유할 수 있다**는 것이다.

*※얕은 복사`shallow copy`와 깊은 복사`deep copy`*  
얕은 복사는 객체의 한 단계까지만 복사하는 것을 말하고,  
깊은 복사는 객체에 중첩되어 있는 객체까지 모두 복사하는 것을 말한다.

```javascript
const o = { x: { y: 1 } };

// 얕은 복사
const c1 = { ...o }; // 35장 "스프레드 문법" 참고
console.log(c1 === o); // false
console.log(c1.x === o.x); // true

// lodash의 cloneDeep을 사용한 깊은 복사
// "npm install lodash"로 lodash를 설치한 후, Node.js 환경에서 실행
const _ = require('lodash');
// 깊은 복사
const c2 = _.cloneDeep(o);
console.log(c2 === o); // false
console.log(c2.x === o.x); // false
```

<br>

브라우저 환경에서는 `JSON` 객체를 활용해서 깊은 복사`deep copy`를 할 수 있다.
```javascript
const o = { x: { y: 1 } };

// 얕은 복사
const c1 = { ...o }; // 35장 "스프레드 문법" 참고
console.log(c1 === o); // false
console.log(c1.x === o.x); // true

// 깊은 복사
const c2 = JSON.parse(JSON.stringify(c1))
console.log(c2 === o); // false
console.log(c2.x === o.x); // false
```
얕은 복사를 했을 때 `x`프로퍼티의 값을 확인해 보면 `true`가 나오는데 이러한 이유는 값을 복사한것이 아니고 참조 값만 복사해 같은 주소를 참조하고 있기 때문이다.  
`JSON` 객체를 사용해 복사하는 경우 원시값 처럼 완전한 복사본을 만들기 때문에 비교시 `false`가 반환된다.

### 2.2 참조에 의한 전달
여러개의 식별자가 하나의 객체를 공유할 수 있다는 것은 객체를 가리키는 변수`person`를 다른 변수`copy`에 할당하면 원본의 참조 값이 복사되어 전달된다는 것이다.
```javascript
var person = {
  name: 'Lee'
};

// 참조값을 복사(얕은 복사)
var copy = person;
```
즉 `person`과 `copy`의 메모리 주소는 다르지만 그 주소 안에는 동일한 참조 값(주소)를 갖는다. 다시 말해 `person`과 `copy`는 모두 동일한 객체를 가리킨다.  
그래서 둘 중 하나를 변경(변수에 새로운 객체를 재 할당하는 것이 아니라, 프로퍼티 값을 변경하거나, 추가, 삭제)하면 서로 영향을 주고 받는다.

```javascript
var person = {
  name: 'Lee'
};

// 참조값을 복사(얕은 복사). copy와 person은 동일한 참조값을 갖는다.
var copy = person;

// copy와 person은 동일한 객체를 참조한다.
console.log(copy === person); // true

// copy를 통해 객체를 변경한다.
copy.name = 'Kim';

// person을 통해 객체를 변경한다.
person.address = 'Seoul';

// copy와 person은 동일한 객체를 가리킨다.
// 따라서 어느 한쪽에서 객체를 변경하면 서로 영향을 주고 받는다.
console.log(person); // {name: "Kim", address: "Seoul"}
console.log(copy);   // {name: "Kim", address: "Seoul"}
```

<br>

## 3.결론

`값에 의한 전달`과 `참조에 의한 전달`은 식별자가 기억하는 **메모리 공간에 저장되어 있는 값을 복사해서 전달**한다는 면에서 동일하다.  
다만, 식별자가 기억하는 메모리 공간, 즉 **변수에 저장되어 있는 값이 원시 값이냐, 참조 값이냐의 차이**로 동작이 다른 것이다.


---

## 참조

[이미지 출처](https://velog.io/@niyu/11%EC%9E%A5-%EC%9B%90%EC%8B%9C-%EA%B0%92%EA%B3%BC-%EA%B0%9D%EC%B2%B4%EC%9D%98-%EB%B9%84%EA%B5%90-%EB%AA%A8%EB%8D%98-%EC%9E%90%EB%B0%94%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-Deep-Dive)

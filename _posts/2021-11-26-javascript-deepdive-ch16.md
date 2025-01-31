---
title: "모던 자바스크립트 딥 다이브 16장 프로퍼티 어트리뷰트"
excerpt: "[javascript] 모던 자바스크립트 Deep Dive 16장 프로퍼티 어트리뷰트"
categories:
 - Javascript
tags:
 - Javascript
toc: true
toc_sticky: true
last_modified_at: 2021-11-26
---

# 16장 프로퍼티 어트리뷰트

## 1.내부 슬롯과 내부 메서드
내부 슬롯과 내부 메서드는 자바스크립트 엔진의 구현 알고리즘을 설명하기 위해 ECMAScript 사양에서 사용하는 의사 프로퍼티`pseudo property`와 의사 메서드`pseudo method`다.  
ECAMScript 사양에 등장하는 이중 대괄호 `[[...]]`로 감싼 이름들이 내부 슬롯과 내부 메서드다.  

내부 슬롯으로 직접적인 접근은 불가능하다. `[[Prototyp]]`의 경우 `__proto__`를 통해 간접적으로 접근가능하다.

```javascript
const o = {};

// 내부 슬롯은 자바스크립트 엔진의 내부 로직이므로 직접 접근할 수 없다.
o.[[Prototype]] // -> Uncaught SyntaxError: Unexpected token '['
// 단, 일부 내부 슬롯과 내부 메서드에 한하여 간접적으로 접근할 수 있는 수단을 제공하기는 한다.
o.__proto__ // -> Object.prototype
```

## 2.프로퍼티 어트리뷰트와 프로퍼티 디스크립터 객체
자바스크립트 엔진은 프로퍼티를 생성할 때 프로퍼티의 상태를 나타내는 프로퍼티 어트리뷰터를 기본값으로 자동 정의한다.
- 값`value` `[[Value]]`
- 갱신 가능 여부`writable` `[[Writable]]`
- 열거 가능 여부`enumerable` `[[Enumerable]]`
- 재정의 가능 여부`configurable` `[[Configurable]]`



이름 | 설명
:---:|:---:
프로퍼티 어트리뷰트 | 프로퍼티의 상태를 나타내는 내부 슬롯들
프로퍼티 디스크립터 | 프로퍼티의 어트리뷰트 정보를 제공하는 객체


프로퍼티 어트리뷰트를 확인하기 위해서는 `Object.getOwnPropertyDescriptor`메서드를 사용해 간접적으로 확인할 수 있다.
```javascript
const person = {
  name: 'Lee'
};

// 프로퍼티 어트리뷰트 정보를 제공하는 프로퍼티 디스크립터 객체를 반환한다.
console.log(Object.getOwnPropertyDescriptor(person, 'name'));
// {value: "Lee", writable: true, enumerable: true, configurable: true}
```
`Object.getOwnPropertyDescriptor` 메서드는 하나의 프로퍼티에 대한 정보만 볼 수 있고 프로퍼티 디스크립터`Property Descriptor`객체를 반환한다. 만약 존재하지 않으면 `undefined`가 반환된다.  
`Object.getOwnPropertyDescriptor`는 하나의 프로퍼티에 대한 결과만 반환하지만 ES8에 추가된 `Object.getOwnPropertyDescriptors`메서드는 객체의 모든 프로퍼티 어트리뷰트정보를 제공하는 프로퍼티 디스크립터 객체들을 반환한다.  

```javascript
const person = {
  name: 'Lee'
};

// 프로퍼티 동적 생성
person.age = 20;

// 모든 프로퍼티의 프로퍼티 어트리뷰트 정보를 제공하는 프로퍼티 디스크립터 객체들을 반환한다.
console.log(Object.getOwnPropertyDescriptors(person));
/*
{
  name: {value: "Lee", writable: true, enumerable: true, configurable: true},
  age: {value: 20, writable: true, enumerable: true, configurable: true}
}
*/
```

## 3.데이터 프로퍼티와 접근자 프로퍼티
프로퍼티는 **데이터 프로퍼티**와 **접근자 프로퍼티**로 구분할 수 있다.

구분|설명
:---:|:---
데이터 프로퍼티<br>`data property` | 키와 값으로 구성된 일반적인 프로퍼티 <br> 지금까지 살펴본 프로퍼티
접근자 프로퍼티<br>`accessor property` | 자체적으로는 값을 갖지 않고 다른 데이터 프로퍼티의 값을 읽거나 저장할 때 호출되는 접근자 함수`accessor function`로 구성된 프로퍼티(get, set)

### 3.1 데이터 프로퍼티
데이터 프로퍼티는 앞서 말한 4가지 어트리뷰트를 가진다. 이 어트리뷰트는 따로 지정해 주지않으면 기본값으로 자동 정의된다.

프로퍼티 어트리뷰트 | 프로퍼티 디스크립터 객체의 프로퍼티 | 설명
:---|:---|:---
`[[Value]]` | `value` | -프로퍼티 키를 통해 프로퍼티 값에 접근하면 반환되는 값<br> -프로퍼티 키를 통해 프로퍼티 값을 변경하면 `[[Value]]`에 값을 재할당한다.<br>-이때 프로퍼티 가 없으면 동적으로 생성하고 생성된 프로퍼티의`[[Value]]`에 값을 저장한다.
`[[Writable]]` | `writable`| -프로퍼티 값의 변경 가능 여부를 나타내며 불리언 값을 갖는다. <br> -`[[Writable]]`의 값이 `false`인 경우 해당 프로퍼티의 `[[Value]]`의 값을 변경할 수 없는 읽기 전용 프로퍼티가 된다.
`[[Enumerable]]` | `enumerable` | -프로퍼티의 열거 가능 여부를 나타내며 불리언 값을 갖는다. <br> -`[[Enumerable]]`의 값이 `false`인 경우 해당 프로퍼티는 `for...in`문이나 `Object.keys`메서드 등으로 열거할 수 없다.
`[[Configurable]]` | `configurable`| -프로퍼티의 재정의 가능 여부를 나타내며 불리언 값을 갖는다. <br> -`[[Configurable]]`의 값이 `false`인 경우 해당 프로퍼티의 삭제, 어트리뷰트 값의 변경이 금지된다. <br> -단, `[[Writable]]`이 `true`인 경우 `[[Value]]`의 변경과 `[[Writable]]`을 `false`로 변경하는 것은 허용된다.

### 3.2 접근자 프로퍼티
접근자 프로퍼티`accessor property`는 자체적으로 값을 갖지 않고 다른 데이터 프로퍼티의 값을 읽거나 저장할 때 사용하는 접근자 함수`accessor function`로 구성된 프로퍼티다.

프로퍼티 어트리뷰트 | 프로퍼티 디스크립터 객체의 프로퍼티 | 설명
:---|:---|:---
`[[Get]]` | `get` | 접근자 프로퍼티를 통해 데이터 프로퍼티의 값을 읽을 때 호출되는 접근자 함수다. <br> 즉, 접근자 프로퍼티 키로 프로퍼티 값에 접근하면 프로퍼티 어트리뷰트`[[Get]]`의 값, 즉 `getter`함수가 호출되고 그 결과가 반환된다.
`[[Set]]` | `set` | 접근자 프로퍼티를 통해 데이터 프로퍼티의 값을 저장할 때 호출되는 접근자 함수다. <br> 즉, 접근자 프로퍼티 키로 프로퍼티 값을 저장하면 프로퍼티 어트리뷰트`[[Set]]`의 값, 즉 `setter`함수가 호출되고 그 결과가 프로퍼티 값으로 저장된다.
`[[Enumerable]]` | `enumerable` | 데이터 프로퍼티의 `[[Enumerable]]`과 같다.
`[[Configurable]]` | `configurable`| 데이터 프로퍼티의 `[[Configurable]]`과 같다.


```javascript
const person = {
  // 데이터 프로퍼티
  firstName: 'Ungmo',
  lastName: 'Lee',

  // fullName은 접근자 함수로 구성된 접근자 프로퍼티다.
  // getter 함수
  get fullName() {
    return `${this.firstName} ${this.lastName}`;
  },
  // setter 함수
  set fullName(name) {
    // 배열 디스트럭처링 할당: "31.1 배열 디스트럭처링 할당" 참고
    [this.firstName, this.lastName] = name.split(' ');
  }
};

// 데이터 프로퍼티를 통한 프로퍼티 값의 참조.
console.log(person.firstName + ' ' + person.lastName); // Ungmo Lee

// 접근자 프로퍼티를 통한 프로퍼티 값의 저장
// 접근자 프로퍼티 fullName에 값을 저장하면 setter 함수가 호출된다.
person.fullName = 'Heegun Lee';
console.log(person); // {firstName: "Heegun", lastName: "Lee"}

// 접근자 프로퍼티를 통한 프로퍼티 값의 참조
// 접근자 프로퍼티 fullName에 접근하면 getter 함수가 호출된다.
console.log(person.fullName); // Heegun Lee

// firstName은 데이터 프로퍼티다.
// 데이터 프로퍼티는 [[Value]], [[Writable]], [[Enumerable]], [[Configurable]] 프로퍼티 어트리뷰트를 갖는다.
let descriptor = Object.getOwnPropertyDescriptor(person, 'firstName');
console.log(descriptor);
// {value: "Heegun", writable: true, enumerable: true, configurable: true}

// fullName은 접근자 프로퍼티다.
// 접근자 프로퍼티는 [[Get]], [[Set]], [[Enumerable]], [[Configurable]] 프로퍼티 어트리뷰트를 갖는다.
descriptor = Object.getOwnPropertyDescriptor(person, 'fullName');
console.log(descriptor);
// {get: ƒ, set: ƒ, enumerable: true, configurable: true}
```
위 예제에서 접근자 프로퍼티 `fullName`으로 프로퍼티 값에 접근하면 내부적으로 `[[Get]]`내부 메서드가 호출되어 다음과 같이 동작한다.
1. 프로퍼티 키가 유효한지 확인한다.(프로퍼티 키는 문자열 또는 심벌이어야 한다.)
2. 프로토타입 체인에서 프로퍼티를 검색한다.(`person`객체에 `fullName`프로퍼티가 존재한다.)
3. 검색된 `fullName`프로퍼티가 데이터 프로퍼티인지 접근자 프로퍼티인지 확인한다.(접근자 프로퍼티임)
4. 접근자 프로퍼티`fullName`의 프로퍼티 어트리뷰트`[[Get]]`의 값, 즉 `getter`함수를 호출하여 그 결과를 반환한다.  

접근자 프로퍼티와 데이터 프로퍼티는 `Object.getOwnPropertyDescriptor`의 결과로 구분 가능하다.
```javascript
// 일반 객체의 __proto__는 접근자 프로퍼티다.
Object.getOwnPropertyDescriptor(Object.prototype, '__proto__');
// {get: ƒ, set: ƒ, enumerable: false, configurable: true}

// 함수 객체의 prototype은 데이터 프로퍼티다.
Object.getOwnPropertyDescriptor(function() {}, 'prototype');
// {value: {...}, writable: true, enumerable: false, configurable: false}
```


## 4.프로퍼티 정의
새로운 프로퍼티를 추가할 때 프로퍼티 어트리뷰트를 명시적으로 정의하거나, 기존 프로퍼티의 프로퍼티 어트리뷰트를 재정의할 수 있다.  
`Object.defineProperty`메서드를 사용해 가능하다.
```javascript
const person = {};

// 데이터 프로퍼티 정의
Object.defineProperty(person, 'firstName', {
  value: 'Ungmo',
  writable: true,
  enumerable: true,
  configurable: true
});

Object.defineProperty(person, 'lastName', {
  value: 'Lee'
});

let descriptor = Object.getOwnPropertyDescriptor(person, 'firstName');
console.log('firstName', descriptor);
// firstName {value: "Ungmo", writable: true, enumerable: true, configurable: true}

// 디스크립터 객체의 프로퍼티를 누락시키면 undefined, false가 기본값이다.
descriptor = Object.getOwnPropertyDescriptor(person, 'lastName');
console.log('lastName', descriptor);
// lastName {value: "Lee", writable: false, enumerable: false, configurable: false}

// [[Enumerable]]의 값이 false인 경우
// 해당 프로퍼티는 for...in 문이나 Object.keys 등으로 열거할 수 없다.
// lastName 프로퍼티는 [[Enumerable]]의 값이 false이므로 열거되지 않는다.
console.log(Object.keys(person)); // ["firstName"]

// [[Writable]]의 값이 false인 경우 해당 프로퍼티의 [[Value]]의 값을 변경할 수 없다.
// lastName 프로퍼티는 [[Writable]]의 값이 false이므로 값을 변경할 수 없다.
// 이때 값을 변경하면 에러는 발생하지 않고 무시된다.
person.lastName = 'Kim';

// [[Configurable]]의 값이 false인 경우 해당 프로퍼티를 삭제할 수 없다.
// lastName 프로퍼티는 [[Configurable]]의 값이 false이므로 삭제할 수 없다.
// 이때 프로퍼티를 삭제하면 에러는 발생하지 않고 무시된다.
delete person.lastName;

// [[Configurable]]의 값이 false인 경우 해당 프로퍼티를 재정의할 수 없다.
// Object.defineProperty(person, 'lastName', { enumerable: true });
// Uncaught TypeError: Cannot redefine property: lastName

descriptor = Object.getOwnPropertyDescriptor(person, 'lastName');
console.log('lastName', descriptor);
// lastName {value: "Lee", writable: false, enumerable: false, configurable: false}

// 접근자 프로퍼티 정의
Object.defineProperty(person, 'fullName', {
  // getter 함수
  get() {
    return `${this.firstName} ${this.lastName}`;
  },
  // setter 함수
  set(name) {
    [this.firstName, this.lastName] = name.split(' ');
  },
  enumerable: true,
  configurable: true
});

descriptor = Object.getOwnPropertyDescriptor(person, 'fullName');
console.log('fullName', descriptor);
// fullName {get: ƒ, set: ƒ, enumerable: true, configurable: true}

person.fullName = 'Heegun Lee';
console.log(person); // {firstName: "Heegun", lastName: "Lee"}
```

`Object.defineProperty`메서드는 한번에 하나의 프로퍼티만 정의할 수 있다. 
`Object.defineProperties`메서드를 사용하면 여러개의 프로퍼티를 한 번에 정의할 수 있다.
```javascript
const person = {};

Object.defineProperties(person, {
  // 데이터 프로퍼티 정의
  firstName: {
    value: 'Ungmo',
    writable: true,
    enumerable: true,
    configurable: true
  },
  lastName: {
    value: 'Lee',
    writable: true,
    enumerable: true,
    configurable: true
  },
  // 접근자 프로퍼티 정의
  fullName: {
    // getter 함수
    get() {
      return `${this.firstName} ${this.lastName}`;
    },
    // setter 함수
    set(name) {
      [this.firstName, this.lastName] = name.split(' ');
    },
    enumerable: true,
    configurable: true
  }
});

person.fullName = 'Heegun Lee';
console.log(person); // {firstName: "Heegun", lastName: "Lee"}
```

## 5.객체 변경 방지
객체는 변경 가능한 값`mutable value`이므로 재할당 없이 직접 변경할 수 있다. 즉, 프로퍼티를 추가하거나 삭제할 수 있고, 갱신할 수 있으며, `Object.defineProperty`나 `Object.defineProperties`메서드를 사용하여 프로퍼티 어트리뷰트를 재정의할 수 있다.  
자바스크립트는 이런 객체의 변경을 방지하는 메서드들을 제공한다.

구분|메서드|프로퍼티 추가|프로퍼티 삭제|프로퍼티 값 읽기|프로퍼티값 쓰기|프로퍼티 어트리뷰트 재정의
:---|:---|:---:|:---:|:---:|:---:|:---:|
객체 확장 금지|`Object.preventExtensions`|X|O|O|O|O
객체 밀봉|`Object.seal`|X|X|O|O|X
객체 동결|`Object.freeze`|X|X|O|X|X

### 5.1 객체 확장 금지
`Object.preventExtensions`메서드  
객체 확장 금지란 프로퍼티 추가를 금지한다는 의미다.  
프로퍼티는 동적 추가와 `Object.defineProperty`메서드로 추가할 수 있는데 이 두가지 방법이 금지된다.
확장 가능 여부는 `Object.isExtensible`메서드로 확인 가능하다.
```javascript
const person = { name: 'Lee' };

// person 객체는 확장이 금지된 객체가 아니다.
console.log(Object.isExtensible(person)); // true

// person 객체의 확장을 금지하여 프로퍼티 추가를 금지한다.
Object.preventExtensions(person);

// person 객체는 확장이 금지된 객체다.
console.log(Object.isExtensible(person)); // false

// 프로퍼티 추가가 금지된다.
person.age = 20; // 무시. strict mode에서는 에러
console.log(person); // {name: "Lee"}

// 프로퍼티 추가는 금지되지만 삭제는 가능하다.
delete person.name;
console.log(person); // {}

// 프로퍼티 정의에 의한 프로퍼티 추가도 금지된다.
Object.defineProperty(person, 'age', { value: 20 });
// TypeError: Cannot define property age, object is not extensible
```

### 5.2 객체 밀봉
`Object.seal`메서드  
객체 밀봉은 프로퍼티 추가 및 삭제와 프로퍼티 어트리뷰트 재정의를 금지한다. **읽기와 쓰기만 가능한 상태이다.**  
밀봉된 객체 여부는 `Object.isSealed`메서드로 확인 가능하다.
```javascript
const person = { name: 'Lee' };

// person 객체는 밀봉(seal)된 객체가 아니다.
console.log(Object.isSealed(person)); // false

// person 객체를 밀봉(seal)하여 프로퍼티 추가, 삭제, 재정의를 금지한다.
Object.seal(person);

// person 객체는 밀봉(seal)된 객체다.
console.log(Object.isSealed(person)); // true

// 밀봉(seal)된 객체는 configurable이 false다.
console.log(Object.getOwnPropertyDescriptors(person));
/*
{
  name: {value: "Lee", writable: true, enumerable: true, configurable: false},
}
*/

// 프로퍼티 추가가 금지된다.
person.age = 20; // 무시. strict mode에서는 에러
console.log(person); // {name: "Lee"}

// 프로퍼티 삭제가 금지된다.
delete person.name; // 무시. strict mode에서는 에러
console.log(person); // {name: "Lee"}

// 프로퍼티 값 갱신은 가능하다.
person.name = 'Kim';
console.log(person); // {name: "Kim"}

// 프로퍼티 어트리뷰트 재정의가 금지된다.
Object.defineProperty(person, 'name', { configurable: true });
// TypeError: Cannot redefine property: name
```

### 5.3 객체 동결
`Object.freeze`메서드  
객체 동결은 프로퍼티 추가, 삭제, 어트리뷰트 재정의, 프로퍼티 값 갱신을 모두 금지한다. 즉, **읽기만 가능하다.**  
동결 여부는 `Object.isFrozen`으로 확인 가능하다.
```javascript
const person = { name: 'Lee' };

// person 객체는 동결(freeze)된 객체가 아니다.
console.log(Object.isFrozen(person)); // false

// person 객체를 동결(freeze)하여 프로퍼티 추가, 삭제, 재정의, 쓰기를 금지한다.
Object.freeze(person);

// person 객체는 동결(freeze)된 객체다.
console.log(Object.isFrozen(person)); // true

// 동결(freeze)된 객체는 writable과 configurable이 false다.
console.log(Object.getOwnPropertyDescriptors(person));
/*
{
  name: {value: "Lee", writable: false, enumerable: true, configurable: false},
}
*/

// 프로퍼티 추가가 금지된다.
person.age = 20; // 무시. strict mode에서는 에러
console.log(person); // {name: "Lee"}

// 프로퍼티 삭제가 금지된다.
delete person.name; // 무시. strict mode에서는 에러
console.log(person); // {name: "Lee"}

// 프로퍼티 값 갱신이 금지된다.
person.name = 'Kim'; // 무시. strict mode에서는 에러
console.log(person); // {name: "Lee"}

// 프로퍼티 어트리뷰트 재정의가 금지된다.
Object.defineProperty(person, 'name', { configurable: true });
// TypeError: Cannot redefine property: name
```

### 5.4 불변 객체
지금까지 살펴본 변경 방지 메서드들(`Object.preventExtensions`, `Object.seal`, `Object.freeze`)은 얕은 변경 방지`shallow only`이기 때문에 직속 프로퍼티만 변경이 방지되고 중첩 객체까지는 영향을 주지 못한다.

```javascript
const person = {
  name: 'Lee',
  address: { city: 'Seoul' }
};

// 얕은 객체 동결
Object.freeze(person);

// 직속 프로퍼티만 동결한다.
console.log(Object.isFrozen(person)); // true
// 중첩 객체까지 동결하지 못한다.
console.log(Object.isFrozen(person.address)); // false

person.address.city = 'Busan';
console.log(person); // {name: "Lee", address: {city: "Busan"}}
```

중첩 객체까지 불변 객체로 구현하려면 재귀적으로 `Object.freeze`메서드를 호출해 변경 불가능하도록 해줘야 한다.
```javascript
function deepFreeze(target) {
  // 객체가 아니거나 동결된 객체는 무시하고 객체이고 동결되지 않은 객체만 동결한다.
  if (target && typeof target === 'object' && !Object.isFrozen(target)) {
    Object.freeze(target);
    /*
      모든 프로퍼티를 순회하며 재귀적으로 동결한다.
      Object.keys 메서드는 객체 자신의 열거 가능한 프로퍼티 키를 배열로 반환한다.
      ("19.15.2. Object.keys/values/entries 메서드" 참고)
      forEach 메서드는 배열을 순회하며 배열의 각 요소에 대하여 콜백 함수를 실행한다.
      ("27.9.2. Array.prototype.forEach" 참고)
    */
    Object.keys(target).forEach(key => deepFreeze(target[key]));
  }
  return target;
}

const person = {
  name: 'Lee',
  address: { city: 'Seoul' }
};

// 깊은 객체 동결
deepFreeze(person);

console.log(Object.isFrozen(person)); // true
// 중첩 객체까지 동결한다.
console.log(Object.isFrozen(person.address)); // true

person.address.city = 'Busan';
console.log(person); // {name: "Lee", address: {city: "Seoul"}}
```

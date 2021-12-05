---
title: "모던 자바스크립트 딥 다이브 19장 프로토타입"
excerpt: "[javascript] 모던 자바스크립트 Deep Dive 19장 프로토타입"
categories:
 - Javascript
tags:
 - Javascript
toc: true
toc_sticky: true
last_modified_at: 2021-12-05
---

# 19장 프로토타입
자바스크립트는 멀티 **패러다임 프로그래밍 언어**다.  
명령형`imperative`, 함수형`functional`, 프로토타입 기반`prototype-based`, 객체지향 프로그래밍`OOP Obejct Oriented Programming`을 지원한다.  

> ※ 클래스(class)
> ES6에서 클래스가 도입되었다.  
> 클래스도 함수이며, 기존 프로토타입 기반 패턴의 문법적 설탕(syntatic sugar)이라고 볼 수 있다.  
> 클래스와 생성자 함수는 모두 프로토타입 기반의 인스턴스를 생성하지만 정확히 동일하게 동작하지는 않는다.
> 클래스는 생성자 함수보다 엄격하며 생성자 함수에서 제공하지 않는 기능도 제공한다.  
> 따라서, 클래스를 프로토타입 기반 객체 생성 패턴의 단순한 문법적 설탕으로 보기보다는 새로운 객체 생성 메커니즘으로 보는것이 좀 더 합당하다.

자바스크립트는 객체 기반의 프로그래밍 언어이며 자바스크립트를 이루고 있는 **거의 모든 것이 객체다.** 원시 타입(`primitive type`)의 값을 제외한 나머지 값들(함수, 배열, 정규 표현식등)은 모두 객체다.  

## 1. 객체 지향 프로그래밍
객체지향 프로그래밍은 프로그램을 명렁어 또는 함수의 목록으로 보는 전통적인 명령형 프로그래밍(`imperative programming`)의 절차지향적 관점에서 벗어나, 여러개의 독립적 단위인 객체(`object`)의 집합으로 프로그램을 표현하려는 프로그래밍 패러다임을 말한다.  

"사람"에게는 다양한 속성이 있으나 우리가 구현하려는 프로그램에서는 사람의 "이름"과 "주소"라는 속성에만 관심이 있다고 가정하자. 이처럼 다양한 속성 중에서 프로그램에 필요한 속성만 간추려 내어 표현하는 것을 **추상화**`abstraction`라 한다.  

```javascript
// 이름과 주소 속성을 갖는 객체
const person = {
  name: 'Lee',
  address: 'Seoul'
};

console.log(person); // {name: "Lee", address: "Seoul"}
```
이름과 주소 속성으로 표현된 객체(`object`)인 `person`을 다른 객체와 구별하여 인식할 수 있다.
이처럼 속성을 통해 여러 개의 값을 하나의 단위로 구성한 복합적인 자료구조를 객체라고 하며, 이러한 객체의 집합으로 프로그램을 표현하려는 것이 객체지향 프로그래밍 패러다임이다.  

객체는 **상태**(`state`)를 나타내는 데이터와 상태 데이터를 조작할 수 있는 **동작**(`behavior`)을 하나의 논리적인 단위로 구성한 것이다.
이때 객체의 상태 데이터를 **프로퍼티**(`property`), 동작을 **메서드**(`method`)라고 부른다.  
```javascript
const circle = {
  // property
  radius: 5, // 반지름

  // method
  // 원의 지름: 2r
  getDiameter() {
    return 2 * this.radius;
  },

  // method
  // 원의 둘레: 2πr
  getPerimeter() {
    return 2 * Math.PI * this.radius;
  },

  // method
  // 원의 넓이: πrr
  getArea() {
    return Math.PI * this.radius ** 2;
  }
};

console.log(circle);
// {radius: 5, getDiameter: ƒ, getPerimeter: ƒ, getArea: ƒ}

console.log(circle.getDiameter());  // 10
console.log(circle.getPerimeter()); // 31.41592653589793
console.log(circle.getArea());      // 78.53981633974483
```

## 2.상속과 프로토타입
상속(`inheritance`)은 객체지향 프로그래밍의 핵심 개념이다.  
어떤 객체의 프로퍼티 또는 메서드를 다른 객체가 상속받아 그대로 사용할 수 있는 것을 말한다.  
상속을 구현하여 불필요한 중복을 제거한다.  

메서드를 중복으로 생성하고 중복으로 소유하는 예제
```javascript
// 생성자 함수
function Circle(radius) {
  this.radius = radius;
  this.getArea = function () {
    // Math.PI는 원주율을 나타내는 상수다.
    return Math.PI * this.radius ** 2;
  };
}

// 반지름이 1인 인스턴스 생성
const circle1 = new Circle(1);
// 반지름이 2인 인스턴스 생성
const circle2 = new Circle(2);

// Circle 생성자 함수는 인스턴스를 생성할 때마다 동일한 동작을 하는
// getArea 메서드를 중복 생성하고 모든 인스턴스가 중복 소유한다.
// getArea 메서드는 하나만 생성하여 모든 인스턴스가 공유해서 사용하는 것이 바람직하다.
console.log(circle1.getArea === circle2.getArea); // false

console.log(circle1.getArea()); // 3.141592653589793
console.log(circle2.getArea()); // 12.566370614359172
```
getArea 메서드를 중복 생성하고 모든 인스턴스가 중복 소유한다.  

프로토타입을 기반으로 상속을 구현한 예제
```javascript
// 생성자 함수
function Circle(radius) {
  this.radius = radius;
}

// Circle 생성자 함수가 생성한 모든 인스턴스가 getArea 메서드를
// 공유해서 사용할 수 있도록 프로토타입에 추가한다.
// 프로토타입은 Circle 생성자 함수의 prototype 프로퍼티에 바인딩되어 있다.
Circle.prototype.getArea = function () {
  return Math.PI * this.radius ** 2;
};

// 인스턴스 생성
const circle1 = new Circle(1);
const circle2 = new Circle(2);

// Circle 생성자 함수가 생성한 모든 인스턴스는 부모 객체의 역할을 하는
// 프로토타입 Circle.prototype으로부터 getArea 메서드를 상속받는다.
// 즉, Circle 생성자 함수가 생성하는 모든 인스턴스는 하나의 getArea 메서드를 공유한다.
console.log(circle1.getArea === circle2.getArea); // true

console.log(circle1.getArea()); // 3.141592653589793
console.log(circle2.getArea()); // 12.566370614359172
```

`Circle.prototype.getArea = function () {...}`를 보면 `Circle`생성자 함수의  `prototype`프로퍼티에 바인딩되어 있다. 즉, 생성자 함수의 프로토타입 객체에 `getArea`메서드를 생성해 놓아서 생성되는 인스턴스들이 중복으로 소유할 필요없이 사용할 수 있게 한다.  
`Circle`생성자 함수가 생성한 모든 인스턴스의 상위(부모) 객체 역하을 하는 `Circle.prototype`의 모든 프로퍼티와 메서드를 상속받는다.  

## 3.프로토타입 객체
모든 객체는 `[[Prototype]]`이라는 내부 슬롯을 가진다.  
이 내부 슬롯의 값은 프로토타입의 참조다. `[[Prototype]]`에 저장되는 프로토타입은 객체 생성 방식에 의해 결정되고 저장된다.  

`[[Prototype]]` 내부슬롯은 `__proto__`접근자 프로퍼티로 접근할 수 있다.  

생성자 함수 객체의 `[[Prototype]]`  
![image](https://user-images.githubusercontent.com/80154058/144704408-3ca28d63-43a4-4341-aa21-32b57f3667b9.png)

생성자 함수로 생성된 객체의 `[[Prototype]]`  
![image](https://user-images.githubusercontent.com/80154058/144704432-ea7401db-6b9f-4b29-8645-6e316f571134.png)

일반 객체의 `[[Prototype]]`  
![image](https://user-images.githubusercontent.com/80154058/144704459-62f28989-c755-4136-b882-463c894bf85e.png)

모든 객체는 하나의 프로토타입을 갖는다. 그리고 모든 프로토타입은 생성자 함수와 연결되어 있다. 즉, 객체와 프로토타입과 생성자 함수는 다임 그림과 같이 서로 연결되어 있다.  

![image](https://user-images.githubusercontent.com/80154058/144704014-2f03263c-d4cc-4704-84cb-b1650afd5d48.png)

`[[Prototype]]`내부 슬롯에는 직접 접근할 수 없지만, 위 그림처럼 `__proto__` 접근자 프로퍼티를 통해 자신의 프로토타입에 접근할 수 있다.  

그리고 프로토타입은 자신의 `constructor`프로퍼티를 통해 생성자 함수에 접근할 수 있고,  
생성자 함수는 자신의 `prototype`프로퍼티를 통해 프로토타입에 접근할 수 있다.  

### 3.1 __proto__ 접근자 프로퍼티
모든 객체는 `__proto__`접근자 프로퍼티를 통해 자신의 프로토타입, 즉 `[[Prototype]]`내부 슬롯에 간접적으로 접근할 수 있다.  

<br>

**1)**`__proto__`**는 접근자 프로퍼티다.**  
[16장 3.2 절의 접근자 프로퍼티](https://hong-p.github.io/javascript/javascript-deepdive-ch16/#3%EB%8D%B0%EC%9D%B4%ED%84%B0-%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0%EC%99%80-%EC%A0%91%EA%B7%BC%EC%9E%90-%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0)에서 살펴본 것처럼 접근자 프로퍼티는 자체적인 값`[[Value]]`을 갖지 않고, 접근자 함수`[[Get]]`, `[[Set]]` 프로퍼티 어트리뷰트로 구성된 프로퍼티다.  

![image](https://user-images.githubusercontent.com/80154058/144704599-862519bd-50a6-4522-bd0d-334ebc8a17b4.png)

```javascript
const obj = {};
const parent = { x: 1 };

// getter 함수인 get __proto__가 호출되어 obj 객체의 프로토타입을 취득
obj.__proto__;
// setter함수인 set __proto__가 호출되어 obj 객체의 프로토타입을 교체
obj.__proto__ = parent;

console.log(obj.x); // 1
```

<br>

**2)**`__proto__`**접근자 프로퍼티는 상속을 통해 사용된다.**  
`__proto__` 접근자 프로퍼티는 객체가 직접 소유하는 프로퍼티가 아니라 `Object.prototype`의 프로퍼티이다. 상속을 통해 `Object.prototype.__proto__` 접근자 프로퍼티를 사용할 수 있다.
```javascript
const person = { name: 'Lee' };

// person 객체는 __proto__ 프로퍼티를 소유하지 않는다.
console.log(person.hasOwnProperty('__proto__')); // false

// __proto__ 프로퍼티는 모든 객체의 프로토타입 객체인 Object.prototype의 접근자 프로퍼티다.
console.log(Object.getOwnPropertyDescriptor(Object.prototype, '__proto__'));
// {get: ƒ, set: ƒ, enumerable: false, configurable: true}

// 모든 객체는 Object.prototype의 접근자 프로퍼티 __proto__를 상속받아 사용할 수 있다.
console.log({}.__proto__ === Object.prototype); // true
```

<br>

**3)**`__proto__`**접근자 프로퍼티를 통해 프로토타입에 접근하는 이유**  
`[[Prototype]]`내부 슬롯의 값에 접근할때 접근자 프로퍼티를 사용하는 이유는 상호 참조에 의해 프로토 타입 체인이 생성되는 것을 방지하기 위해서다.  

상호 참조(순환 참조)를 시도하면 `TypeError`를 발생시킨다.  
단방향 링크드 리스트로 구현되어야 한다. 
```javascript
const parent = {};
const child = {};

// child의 프로토타입을 parent로 설정
child.__proto__ = parent;
// parent의 프로토타입을 child로 설정
parent.__proto__ = child; // TypeError: Cyclic __proto__ value
```

<br>

**4)**`__proto__` **접근자 프로퍼티를 코드 내에서 직접 사용하는 것은 권장하지 않는다.**  
ES5까지는 비표준이었다. 하지만 일부 브라우저에서 `__proto__`를 지원해 호환성을 위해 ES6에 표준으로 채택하였다.  

하지만 `__proto__`접근자 프로퍼티를 코드내에서 직접 사용하는 것은 권장하지 않는다. 모든 객체가 `__proto__`접근자 프로퍼티를 사용하는 것은 아니기 때문이다.  
직접 상속을 통해 `Object.prototype`을 상속받지 않는 객체를 생성할 수 있어 `__proto__`접근자 프로퍼티를 사용할 수 없는 경오가 있다.

```javascript
// obj는 프로토타입 체인의 종점이다. 따라서 Object.__proto__를 상속받을 수 없다.
const obj = Object.create(null);

// obj는 Object.__proto__를 상속받을 수 없다.
console.log(obj.__proto__); // undefined

// 따라서 Object.getPrototypeOf 메서드를 사용하는 편이 좋다.
console.log(Object.getPrototypeOf(obj)); // null
```

`__proto__`접근자 프로퍼티 대신 프로토타입의 참조를 취득하고 싶은경우 `Object.getPrototypeOf`메서드를 사용하고,  
프로토타입을 교체하고 싶은 경우 `Obejct.setPrototypeOf`메서드를 사용할 것을 권장한다.  

```javascript
const obj = {};
const parent = { x: 1 };

// obj 객체의 프로토타입을 취득
Object.getPrototypeOf(obj); // obj.__proto__;
// obj 객체의 프로토타입을 교체
Object.setPrototypeOf(obj, parent); // obj.__proto__ = parent;

console.log(obj.x); // 1
```
`Object.getPrototypeOf`는 `get Object.prototype.__proto__`와 일치
`Object.setPrototypeOf`는 `set Object.prototype.__proto__`와 일치


### 3.2 함수 객체의 prototype 프로퍼티
함수 객체만이 소유하는 `prototype`프로퍼티는 생성자 함수가 생성할 인스턴스의 프로토타입을 가리킨다.  
```javascript
// 함수 객체는 prototype 프로퍼티를 소유한다.
(function () {}).hasOwnProperty('prototype'); // -> true

// 일반 객체는 prototype 프로퍼티를 소유하지 않는다.
({}).hasOwnProperty('prototype'); // -> false
```

함수객체
![image](https://user-images.githubusercontent.com/80154058/144734127-465d45a6-3085-44d2-9706-96b4dcd0354c.png)

일반객체
![image](https://user-images.githubusercontent.com/80154058/144734111-c128639f-d443-4eb2-827d-8c1fa54266a2.png)

`prototype`프로퍼티는 생성자 함수가 생성할 객체(인스턴스)의 프로토타입을 가리킨다.  
따라서 생성자 함수로 호출할 수 없는 함수, 즉 `non-constructor`인 **화살표함수**, **ES6 메서드 축약 표현으로 정의한 메서드**는 `prototype`프로퍼티를 소유하지 않으며 프로토타입도 생성하지 않는다.  
```javascript
// 화살표 함수는 non-constructor다.
const Person = name => {
  this.name = name;
};

// non-constructor는 prototype 프로퍼티를 소유하지 않는다.
console.log(Person.hasOwnProperty('prototype')); // false

// non-constructor는 프로토타입을 생성하지 않는다.
console.log(Person.prototype); // undefined

// ES6의 메서드 축약 표현으로 정의한 메서드는 non-constructor다.
const obj = {
  foo() {}
};

// non-constructor는 prototype 프로퍼티를 소유하지 않는다.
console.log(obj.foo.hasOwnProperty('prototype')); // false

// non-constructor는 프로토타입을 생성하지 않는다.
console.log(obj.foo.prototype); // undefined
```
  

화살표 함수(`prototype`프로퍼티 없음)
![image](https://user-images.githubusercontent.com/80154058/144734205-81124ffd-e766-451d-aefa-d5ce5e0b0de0.png)

  

ES6 메서드(`prototype`프로퍼티 없음)
![image](https://user-images.githubusercontent.com/80154058/144734227-e9ef3b6b-eb2f-4bb1-a5c4-f4608e17cc61.png)

구분|소유|값|사용 주체|사용 목적
:---:|:---:|:---:|:---:|:---:
`__proto__`<br>접근자 프로퍼티|모든 객체|프로토타입의 참조|모든 객체|객체가 자신의 프로토타입에 접근 또는 교체하기 위해 사용
`prototype`<br>프로퍼티|`constructor`|프로토타입의 참조|생성자 함수|생성자 함수가 자신이 생성할 객체(인스턴스)의 프로토타입을 할당하기 위해 사용

  

※ `__proto__`접근자 프로퍼티 === 함수 객체의 `prototype`프로퍼티

```javascript
// 생성자 함수
function Person(name) {
  this.name = name;
}

const me = new Person('Lee');

// 결국 Person.prototype과 me.__proto__는 결국 동일한 프로토타입을 가리킨다.
console.log(Person.prototype === me.__proto__);  // true
```

![image](https://user-images.githubusercontent.com/80154058/144734375-2548e2c4-ec06-4b55-853b-73e25de75a82.png)


### 3.3 프로토타입의 constructor 프로퍼티와 생성자 함수
모든 프로토타입은 `constructor`프로퍼티를 갖는다.  
이 `constructor`프로퍼티는 `prototype`프로퍼티로 자신을 참조하고 있는 생성자 함수를 가리킨다.

```javascript
// 생성자 함수
function Person(name) {
  this.name = name;
}

const me = new Person('Lee');

// me 객체의 생성자 함수는 Person이다.
console.log(me.constructor === Person);  // true
```
`me`객체에는 `constructor`프로퍼티가 없다.  
`me`객체의 프로토타입인 `me.__proto__` 즉, `Person.prototype`에는 `constructor`프로퍼티가 있어 이를 상속받아 사용할 수 있다.  

## 4.리터럴 표기법에 의해 생성된 객체의 생성자 함수와 프로토타입
`new`연산자와 함께 생성자 함수를 호출하여 인스턴스를 생성하지 않고 **리터럴 표기법에 의한 객체 생성 방식**이 있다.  

리터럴로 생성하는 방식
```javascript
// 객체 리터럴
const obj = {};

// 함수 리터럴
const add = function (a, b) { return a + b; };

// 배열 리터럴
const arr = [1, 2, 3];

// 정규표현식 리터럴
const regexp = /is/ig;
```

리터럴 표기법에 의해 생성된 객체도 물론 프로토타입이 존재한다. 하지만 이런 객체는 `constructor`프로퍼티가 가리키는 생성자 함수가 반드시 객체를 생성한 함수라고 단정할 수 없다.  

```javascript
// obj 객체는 Object 생성자 함수로 생성한 객체가 아니라 객체 리터럴로 생성했다.
const obj = {};

// 하지만 obj 객체의 생성자 함수는 Object 생성자 함수다.
console.log(obj.constructor === Object); // true
```
위 예제같은 경우 객체 리터럴로 생성한 객체의 `constructor`프로퍼티는 `Object`생성자 함수와 연결되어 있다.  

결과적으로 리터럴로 생성한 객체와 생성자 함수로 생성한 객체의 `constructor`프로퍼티를 확인하면 동일해 보이지만 객체가 생성되는 방식은 다르다.  

아래 두가지 방식을 비교해 보며 확인해보자.  

※ `Object`생성자 함수로 객체 생성  
https://262.ecma-international.org/11.0/#sec-object-value

![image](https://user-images.githubusercontent.com/80154058/144734898-2920dbe0-071e-4cc6-aca7-f1900f89b325.png)

2번에서 생성자 함수에 인수를 전달하지 않고나 `undefined`, `null`을 인수로 전달하여 호출하면 추상연산`OrdinaryObjectCreate`를 호출하여 `Object.prototype`을 프로토타입으로 갖는 빈 객체를 생성한다.  

```javascript
// 1. new.target이 undefined나 Object가 아닌 경우
// 인스턴스 -> Foo.prototype -> Object.prototype 순으로 프로토타입 체인이 생성된다.
class Foo extends Object {}
new Foo(); // Foo {}

// 2. Object 생성자 함수에 의한 객체 생성
// Object 생성자 함수는 new 연산자와 함께 호출하지 않아도 new 연산자와 함께 호출한 것과 동일하게 동작한다.
// 인수가 전달되지 않았을 때 추상 연산 OrdinaryObjectCreate를 호출하여 빈 객체를 생성한다.
let obj = new Object();
console.log(obj); // {}

// 3. 인수가 전달된 경우에는 인수를 객체로 변환한다.
// Number 객체 생성
obj = new Object(123);
console.log(obj); // Number {123}

// String  객체 생성
obj = new Object('123');
console.log(obj); // String {"123"}
```

※ 객체리터럴로 객체 생성  
https://262.ecma-international.org/11.0/#sec-object-initializer-runtime-semantics-evaluation

![image](https://user-images.githubusercontent.com/80154058/144735008-ac359ed9-a694-4f32-b19d-d2b58f0ac156.png)

객체 리터럴이 평가될 때에도 위와 같이 `OrdinaryObjectCreate`를 호출하여 빈 객체를 생성하고 프로퍼티를 추가하도록 정의되어 있다.  

이처럼 `Object`생성자 함수 호출과 객체 리터럴의 평가는 추상연산`OrdinaryObjectCreate`를 호출하여 빈 객체를 생성하는 점에서 동일하나, `new.target`의 확인이나 프로퍼티를 추가하는 처리 등 세부내용은 다르다.  

따라서 **객체 리터럴에 의해 생성된 객체는** `Object`**생성자 함수가 생성한 객체가 아니다.**  

이런 차이는 함수 객체의 경우 더 명확하다.  
[12.4.4절 Function 생성자 함수](https://hong-p.github.io/javascript/javascript-deepdive-ch12/#44-function-%EC%83%9D%EC%84%B1%EC%9E%90-%ED%95%A8%EC%88%98)에서 보았듯이 `Function`생성자 함수를 호출해 생성한 함수는 렉시컬 스코프를 만들지 않고 전역함수인 것처럼 스코프를 생성하며, 클로저도 만들지 않는다.  

함수 선언문과 함수 표현식을 평가하여 함수 객체를 생성한 것은 `Function`생성자 함수가 아니다! 하지만 `constructor`프로퍼티를 통해 확인해 보면 `foo`함수의 생성자 하뭇는 `Function`생성자 함수다.
```javascript
// foo 함수는 Function 생성자 함수로 생성한 함수 객체가 아니라 함수 선언문으로 생성했다.
function foo() {}

// 하지만 constructor 프로퍼티를 통해 확인해보면 함수 foo의 생성자 함수는 Function 생성자 함수다.
console.log(foo.constructor === Function); // true
```

리터럴 표기법|생성자 함수|프로토타입
:---:|:---:|:---:
객체 리터럴|`Object`|`Object.prototype`
함수 리터럴|`Function`|`Function.prototype`
배열 리터럴|`Array`|`Array.prototype`
정규식 리터럴|`RegExp`|`RegExp.prototype`

## 5.프로토타입의 생성 시점
**프로토타입은 생성자 함수가 생성되는 시점에 더불어 생성된다.**  
프로토타입과 생성자 함수는 단독으로 존재할 수 없고 항상 쌍으로 존재한다.  


### 5.1 사용자 정의 생성자 함수와 프로토타입 생성 시점
생성자 함수로서 호출이 가능한 함수. 즉 `constructor`는 함수 정의가 평가되어 함수 객체를 생성하는 시점에 프로토타입도 더불어 생성된다.

```javascript
// 호이스팅으로 선언문에 도달하기 전에 함수 객체가 생성된다.
// 함수 정의(constructor)가 평가되어 함수 객체를 생성하는 시점에 프로토타입도 더불어 생성된다.
console.log(Person.prototype); // {constructor: ƒ}

// 생성자 함수
function Person(name) {
  this.name = name;
}
```
![image](https://user-images.githubusercontent.com/80154058/144735530-4518b1fd-585e-429b-b705-beba1ef03d0a.png)


사용자 정의 생성자 함수로 생성된 프로토타입의 프로토타입은 언제나 `Object.prototype`이다.

### 5.2 빌트인 생성자 함수와 프로토타입 생성 시점
`Object`, `Striong`, `Number`, `Function`, `Array`, `RegExp`, `Date`, `Promise`등과 같은 빌트인 생성자 함수도 일반 함수와 마찬가지로 빌트인 생성자 함수가 생성되는 시점에 프로토타입이 생성된다.  

**모든 빌트인 생성자 함수는 전역 객체가 생성되는 시점에 생성**된다.  

객체가 생성되기 이전에 생성자 함수와 프로토타입은 이미 객체화 되어 존재한다.  
이후 생성자 함수 또는 리터럴 표기법으로 객체를 생성하면 프로토타입은 생성된 객체의 `[[Prototype]]`내부 슬롯에 할당된다.

## 6.객체 생성 방식과 프로토타입의 결정
- 객체 리터럴
- `Object`생성자 함수
- 생성자 함수
- `Object.create`메서드
- 클래스(ES6)

위와 같이 여러가지 객체 생성 방식이 있으나 모두 추상연산`OrdinaryObjectCreate`에 의해 생성된다.  

※ 추상연산`OrdinaryObjectCreate`에 의한 객체 생성 순서
1. 생성할 객체의 프로토타입을 인수로 전달 받는다.
2. 자신이 생성할 객체에 추가할 프로퍼티 목록을 옵션으로 전달한다.
3. 빈 객체를 생성한다.
4. 객체에 추가할 프로퍼티 목록이 인수로 전달된 경우 객체에 프로퍼티를 추가한다.
5. 인수로 전달받은 프로토타입을 생성한 객체의 `[[Prototype]]`내부 슬롯에 할당한다.
6. 생성한 객체를 반환한다.

### 6.1 객체 리터럴에 의해 생성된 객체의 프로토타입

```javascript
const obj = { x: 1 };
```
위 예제의 경우  
- 객체 리터럴을 평가한다.
- `OrdinaryObjectCreate`를 호출한다.
- 이때 전달되는 프로토타입은 `Object.prototype`이다

결국 `Object.prototype`을 프로토타입으로 갖으므로 `obj`객체는 `constructor`, `hasOwnProperty`메서드 등을 소유하지 않지만 사용할 수 있다.

```javascript
const obj = { x: 1 };

// 객체 리터럴에 의해 생성된 obj 객체는 Object.prototype을 상속받는다.
console.log(obj.constructor === Object); // true
console.log(obj.hasOwnProperty('x'));    // true
```

### 6.2 Object 생성자 함수에 의해 생성된 객체의 프로토타입
```javascript
const obj = new Object();
obj.x = 1;

// Object 생성자 함수에 의해 생성된 obj 객체는 Object.prototype을 상속받는다.
console.log(obj.constructor === Object); // true
console.log(obj.hasOwnProperty('x'));    // true
```

객체 리터럴과 `Object`생성자 함수에 의한 객체 생성 결과는 동일하다.  
하지만 프로퍼티를 추가하는 방식이 차이점이다.  
객체 리터럴은 리터럴 내부에 프로퍼티를 추가하지만,  
`Object`생성자 함수 방식은 일단 빈 객체를 생성한 이후 프로퍼티를 추가해야 한다.

### 6.3 생성자 함수에 의해 생성된 객체의 프로토타입
`new`연산자와 함께 생성자 함수를 호출하여 객체를 생성하는 방식도 마찬가지로 추상연산`OrdinaryObjectCreate`가 호출된다.  

```javascript
function Person(name) {
  this.name = name;
}

const me = new Person('Lee');
```

![image](https://user-images.githubusercontent.com/80154058/144736362-51214ff5-6420-4d1b-9ffd-141eb6a82326.png)

객체의 프로토타입은 생성자 함수와 생성자 함수에 의해 생성된 객체 사이에 생성되여 둘 사이를 연결한다.

## 7. 프로토타입 체인

자바스크립트 엔진은 프로토타입 체인을 따라 프로퍼티/메서드를 검색한다.  
객체간의 상속관계로 이루어진 프로토타입의 계층적인 구조에서 객체의 프로퍼티를 검색한다.  

**프로토타입 체인은 상속과 프로퍼티 검색을 위한 메커니즘이다.**  

이에 반해 식별자는 스코프 체인에서 검색한다.  
함수의 중첩 관계로 이루어진 스코프의 계층적 구조에서 식별자를 검색한다.  

**스코프 체인은 식별자 검색을 위한 메커니즘이다.**  

```javascript
function Person(name) {
  this.name = name;
}

// 프로토타입 메서드
Person.prototype.sayHello = function () {
  console.log(`Hi! My name is ${this.name}`);
};

const me = new Person('Lee');

// hasOwnProperty는 Object.prototype의 메서드다.
console.log(me.hasOwnProperty('name')); // true
```
```javascript
Object.getPrototypeOf(me) === Person.prototype; // -> true

Object.getPrototypeOf(Person.prototype) === Object.prototype; // -> true
```
![image](https://user-images.githubusercontent.com/80154058/144736989-31aff537-7390-48c4-8e29-109522337825.png)


## 8.오버라이딩과 프로퍼티 섀도잉

**오버라이딩**(`overriding`)은 프로토타입에 메서드가 존재하는데, 인스턴스에 같은 메서드를 추가해 재정의하여 사용하는 방식이다.  
인스턴스에서 오버라이딩하여 사용하면 프로토타입 메서드는 가려지게 되는데 이러한 현상을 **섀도잉**(`property shadowing`)이라고 한다.  

> ※ 오버라이딩(`overriding`)  
> 상위 클래스가 가지고 있는 메서드를 하위 클래스가 **재정의**하여 사용하는 방식

> ※ 오버로딩(`overloading`)  
> 함수의 이름은 동일하지만 매개변수의 타입 또는 개수가 다른 메서드를 구현하고,  
> 매개변수에 의해 메서드를 구별하여 호출하는 방식이다.  
> 자바스크립트는 오버로딩을 지원하지 않지만 `arguments`객체를 사용하여 구현할 수는 있다.

```javascript
const Person = (function () {
  // 생성자 함수
  function Person(name) {
    this.name = name;
  }

  // 프로토타입 메서드
  Person.prototype.sayHello = function () {
    console.log(`Hi! My name is ${this.name}`);
  };

  // 생성자 함수를 반환
  return Person;
}());

const me = new Person('Lee');

// 인스턴스 메서드
me.sayHello = function () {
  console.log(`Hey! My name is ${this.name}`);
};

// 인스턴스 메서드가 호출된다. 프로토타입 메서드는 인스턴스 메서드에 의해 가려진다.
me.sayHello(); // Hey! My name is Lee
```
위 예제에서 `me.sayHello = funciton() {...}`로 오버라이딩(재정의)해서 사용하게 되면 `Person.prototype.sayHello`메서드가 가려져 섀도잉이 일어나게 된다.  

만약 프로퍼티를 삭제하는 경우  
인스턴스 메서드는 아래와 같이 삭제한다.  
```javascript
// 인스턴스 메서드를 삭제한다.
delete me.sayHello;
// 인스턴스에는 sayHello 메서드가 없으므로 프로토타입 메서드가 호출된다.
me.sayHello(); // Hi! My name is Lee
```

프로토타입 프로퍼티(메서드)를 변경, 삭제하는 경우
```javascript
// 프로토타입 메서드 변경
Person.prototype.sayHello = function () {
  console.log(`Hey! My name is ${this.name}`);
};
me.sayHello(); // Hey! My name is Lee

// 프로토타입 메서드 삭제
delete Person.prototype.sayHello;
me.sayHello(); // TypeError: me.sayHello is not a function
```

## 9.프로토타입의 교체
프로토타입은 임의의 다른 객체로 변경할 수 있다. 이것은 부모 객체인 프로토타입을 동적으로 변경할 수 있다는 의미다. 즉, 객체 간의 상속 관계를 동적으로 변경할 수 있다는 뜻이다.  

### 9.1 생성자 함수에 의한 프로토타입의 교체
```javascript
const Person = (function () {
  function Person(name) {
    this.name = name;
  }

  // ① 생성자 함수의 prototype 프로퍼티를 통해 프로토타입을 교체
  Person.prototype = {
    sayHello() {
      console.log(`Hi! My name is ${this.name}`);
    }
  };

  return Person;
}());

const me = new Person('Lee');
```
①에서 `Person.prototype`에 객체 리터럴을 할당했다.  

![image](https://user-images.githubusercontent.com/80154058/144737566-ea6db17c-7ed0-461d-8f82-c37a85b792f9.png)

객체 리터럴에는 `constructor`프로퍼티가 없다. 따라서 `me`객체의 생성자함수`constructor`를 검색하면 `Person`이 아니라 `Object`가 나온다.

```javascript
// 프로토타입을 교체하면 constructor 프로퍼티와 생성자 함수 간의 연결이 파괴된다.
console.log(me.constructor === Person); // false
// 프로토타입 체인을 따라 Object.prototype의 constructor 프로퍼티가 검색된다.
console.log(me.constructor === Object); // true
```

이러면 `constructor`프로퍼티와 생성자 함수간의 연결이 파괴된다. 그러므로 아래와 같이 ②의 `constructor: Person,`을 추가해 줘 연결을 되살린다.
```javascript
const Person = (function () {
  function Person(name) {
    this.name = name;
  }

  // 생성자 함수의 prototype 프로퍼티를 통해 프로토타입을 교체
  Person.prototype = {
    // ② constructor 프로퍼티와 생성자 함수 간의 연결을 설정
    constructor: Person,
    sayHello() {
      console.log(`Hi! My name is ${this.name}`);
    }
  };

  return Person;
}());

const me = new Person('Lee');

// constructor 프로퍼티가 생성자 함수를 가리킨다.
console.log(me.constructor === Person); // true
console.log(me.constructor === Object); // false
```

### 9.2 인스턴스에 의한 프로토타입의 교체
**프로토 타입**은 생성자 함수의 `prototype`프로퍼티 뿐만 아니라 **인스턴스의** `__proto__`**접근자 프로퍼티**를 통해서도 접근할수 있다.  

생성할때 변경하는 것이 아니고 이미 생성된 객체의 프로토타입을 교체한다.

```javascript
function Person(name) {
  this.name = name;
}

const me = new Person('Lee');

// 프로토타입으로 교체할 객체
const parent = {
  sayHello() {
    console.log(`Hi! My name is ${this.name}`);
  }
};

// ① me 객체의 프로토타입을 parent 객체로 교체한다.
Object.setPrototypeOf(me, parent);
// 위 코드는 아래의 코드와 동일하게 동작한다.
// me.__proto__ = parent;

me.sayHello(); // Hi! My name is Lee
```

이를 그림으로 나타내면 이렇게 된다.
![image](https://user-images.githubusercontent.com/80154058/144737804-22b6ac7a-5e25-4380-8de1-aaef6de30b3f.png)

교체한 객체에는 `constructor`프로퍼티가 없어 생성자 함수간의 연결이 파괴된다.  

```javascript
// 프로토타입을 교체하면 constructor 프로퍼티와 생성자 함수 간의 연결이 파괴된다.
console.log(me.constructor === Person); // false
// 프로토타입 체인을 따라 Object.prototype의 constructor 프로퍼티가 검색된다.
console.log(me.constructor === Object); // true
```

※ 생성자 함수에 의한 프로토타입 교체 vs 인스턴스에 의한 프로토타입 교체
![image](https://user-images.githubusercontent.com/80154058/144737905-52ffeb25-abc1-4339-a76d-8046c8a85cbc.png)

인스턴스에 의한 프로토타입 교체하는 경우 생성자 함수와의 연결까지 살리려면 아래와 같이 해야 한다.

```javascript
function Person(name) {
  this.name = name;
}

const me = new Person('Lee');

// 프로토타입으로 교체할 객체
const parent = {
  // ① constructor 프로퍼티와 생성자 함수 간의 연결을 설정
  constructor: Person,
  sayHello() {
    console.log(`Hi! My name is ${this.name}`);
  }
};

// ② 생성자 함수의 prototype 프로퍼티와 프로토타입 간의 연결을 설정
Person.prototype = parent;

// ③ me 객체의 프로토타입을 parent 객체로 교체한다.
Object.setPrototypeOf(me, parent);
// 위 코드는 아래의 코드와 동일하게 동작한다.
// me.__proto__ = parent;

me.sayHello(); // Hi! My name is Lee

// constructor 프로퍼티가 생성자 함수를 가리킨다.
console.log(me.constructor === Person); // true
console.log(me.constructor === Object); // false

// 생성자 함수의 prototype 프로퍼티가 교체된 프로토타입을 가리킨다.
console.log(Person.prototype === Object.getPrototypeOf(me)); // true
```
① `parent`객체에 `constructor`프로퍼티와 `Person`생성자 함수를 연결  
② `Person`생성자 함수의 `prototype`프로퍼티를 바꿀 프로토타입으로 연결  
③ `me`인스턴스의 프로토타입을 `parent`로 변경  

위와 같은 작업으로 상속 관계를 동적으로 바꾸는 것은 번거롭다.  
그러므로 ES6에서 도입된 클래스를 사용해 간편하고 직관적으로 상속관계를 구현하는 편이 낫다.

## 10.instanceof 연산자
`instanceof`연산자는 이항 연산자로  
좌변에 **객체를 가리키는 식별자**, 우변에 **생성자 함수를 가리키는 식별자**를  
피연산자로 받는다.  

만약 우변의 피연산자가 함수가 아니면 `TypeError`가 발생한다.  

```javascript
객체 instanceof 생성자 함수
```

우변의 생성자 함수의 `prototype`에 바인딩된 객체가 좌변의 객체의 **프로토타입 체인** 상에 존재하면 `true`, 존재하지 않으면 `false`가 평가된다.  

```javascript
// 생성자 함수
function Person(name) {
  this.name = name;
}

const me = new Person('Lee');

// Person.prototype이 me 객체의 프로토타입 체인 상에 존재하므로 true로 평가된다.
console.log(me instanceof Person); // true

// Object.prototype이 me 객체의 프로토타입 체인 상에 존재하므로 true로 평가된다.
console.log(me instanceof Object); // true
```
![image](https://user-images.githubusercontent.com/80154058/144738236-95e1f30a-9570-43d4-9daf-f2e911378e21.png)

`constructor` 프로퍼티와 생성자 함수간의 연결이 파괴되어도 `instanceof`연산에는 아무런 영향을 받지 않는다. (**단지 프로토타입 체인만 보기 때문에**)

```javascript
const Person = (function () {
  function Person(name) {
    this.name = name;
  }

  // 생성자 함수의 prototype 프로퍼티를 통해 프로토타입을 교체
  Person.prototype = {
    sayHello() {
      console.log(`Hi! My name is ${this.name}`);
    }
  };

  return Person;
}());

const me = new Person('Lee');

// constructor 프로퍼티와 생성자 함수 간의 연결은 파괴되어도 instanceof는 아무런 영향을 받지 않는다.
console.log(me.constructor === Person); // false

// Person.prototype이 me 객체의 프로토타입 체인 상에 존재하므로 true로 평가된다.
console.log(me instanceof Person); // true
// Object.prototype이 me 객체의 프로토타입 체인 상에 존재하므로 true로 평가된다.
console.log(me instanceof Object); // true
```

## 11. 직접 상속
### 11.1 Object.create에 의한 직접 상속
`Object.create`메서드는 명시적으로 **프로토타입을 지정**하여 새로운 객체를 생성한다.  
마찬가지로 추상연산`OrdinaryObjectCreate`를 호출하여 객체를 생성한다.  

`Object.create`메서드로 객체를 생성할 때 장점
- `new`연산자 없이도 객체 생성
- 프로토타입을 지정하면서 객체 생성
- 객체 리터럴에 의해 생성된 객체도 상속 가능

```javascript
// 프로토타입이 null인 객체를 생성한다. 생성된 객체는 프로토타입 체인의 종점에 위치한다.
// obj → null
let obj = Object.create(null);
console.log(Object.getPrototypeOf(obj) === null); // true
// Object.prototype을 상속받지 못한다.
console.log(obj.toString()); // TypeError: obj.toString is not a function

// obj → Object.prototype → null
// obj = {};와 동일하다.
obj = Object.create(Object.prototype);
console.log(Object.getPrototypeOf(obj) === Object.prototype); // true

// obj → Object.prototype → null
// obj = { x: 1 };와 동일하다.
obj = Object.create(Object.prototype, {
  x: { value: 1, writable: true, enumerable: true, configurable: true }
});
// 위 코드는 다음과 동일하다.
// obj = Object.create(Object.prototype);
// obj.x = 1;
console.log(obj.x); // 1
console.log(Object.getPrototypeOf(obj) === Object.prototype); // true

const myProto = { x: 10 };
// 임의의 객체를 직접 상속받는다.
// obj → myProto → Object.prototype → null
obj = Object.create(myProto);
console.log(obj.x); // 10
console.log(Object.getPrototypeOf(obj) === myProto); // true

// 생성자 함수
function Person(name) {
  this.name = name;
}

// obj → Person.prototype → Object.prototype → null
// obj = new Person('Lee')와 동일하다.
obj = Object.create(Person.prototype);
obj.name = 'Lee';
console.log(obj.name); // Lee
console.log(Object.getPrototypeOf(obj) === Person.prototype); // true
```

`Object.prototype`의 빌트인 메서드를 객체가 직접 호출하는 것을 권장하지 않는다.  
`Object.create`메서드를 통해 프로토타입 체인의 종점에 위치하는 객체를 생성할 수 있기 때문이다.(`Obejct.create(null);`로 생성하는 경우)

![image](https://user-images.githubusercontent.com/80154058/144738618-087a7559-f1bd-433b-ae7c-c2dcbdd69c0e.png)

프로토타입이 `null`이어서 아무것도 없다. (`__proto__`프로퍼티도 없음.)

```javascript
// 프로토타입이 null인 객체, 즉 프로토타입 체인의 종점에 위치하는 객체를 생성한다.
const obj = Object.create(null);
obj.a = 1;

console.log(Object.getPrototypeOf(obj) === null); // true

// obj는 Object.prototype의 빌트인 메서드를 사용할 수 없다.
console.log(obj.hasOwnProperty('a')); // TypeError: obj.hasOwnProperty is not a function
```

객체가 직접 호출하지 않고 `Object.prototype.hasOwnProperty.call()`메서드를 활용해 호출한다.  

```javascript
// 프로토타입이 null인 객체를 생성한다.
const obj = Object.create(null);
obj.a = 1;

// console.log(obj.hasOwnProperty('a')); // TypeError: obj.hasOwnProperty is not a function

// Object.prototype의 빌트인 메서드는 객체로 직접 호출하지 않는다.
console.log(Object.prototype.hasOwnProperty.call(obj, 'a')); // true
```

### 11.2 객체 리터럴 내부에서 __proto__에 의한 직접 상속
ES6에서는 객체 리터럴 내부에서 `__proto__` 접근자 프로퍼티를 사용해 직접 상속을 구현할 수 있다.

```javascript
const myProto = { x: 10 };

// 객체 리터럴에 의해 객체를 생성하면서 프로토타입을 지정하여 직접 상속받을 수 있다.
const obj = {
  y: 20,
  // 객체를 직접 상속받는다.
  // obj → myProto → Object.prototype → null
  __proto__: myProto
};
/* 위 코드는 아래와 동일하다.
const obj = Object.create(myProto, {
  y: { value: 20, writable: true, enumerable: true, configurable: true }
});
*/

console.log(obj.x, obj.y); // 10 20
console.log(Object.getPrototypeOf(obj) === myProto); // true
```

## 12.정적 프로퍼티/메서드
정적(`static`)프로퍼티/메서드는 생성자 함수로 인스턴스를 생성하지 않아도 참조/호출할 수 있는 프로퍼티/메서드이다.  

```javascript
// 생성자 함수
function Person(name) {
  this.name = name;
}

// 프로토타입 메서드
Person.prototype.sayHello = function () {
  console.log(`Hi! My name is ${this.name}`);
};

// ① 정적 프로퍼티
Person.staticProp = 'static prop';

// ② 정적 메서드
Person.staticMethod = function () {
  console.log('staticMethod');
};

const me = new Person('Lee');

// 생성자 함수에 추가한 정적 프로퍼티/메서드는 생성자 함수로 참조/호출한다.
Person.staticMethod(); // staticMethod

// ③ 정적 프로퍼티/메서드는 생성자 함수가 생성한 인스턴스로 참조/호출할 수 없다.
// 인스턴스로 참조/호출할 수 있는 프로퍼티/메서드는 프로토타입 체인 상에 존재해야 한다.
me.staticMethod(); // TypeError: me.staticMethod is not a function
```

③에서 보면 정적 프로퍼티/메서드는 생성자 함수가 생성한 **인스턴스**로 참조/호출할 수 없다.  

![image](https://user-images.githubusercontent.com/80154058/144738874-7f2805cf-aaad-43e5-a5b2-130eec3a0499.png)

  

`Object.create` 같은 메서드는 **정적 메서드**이고,  
`Object.prototype.hasOwnProperty`같은 메서드는 **프로토타입 메서드**이다.  

프로토타입 메서드를 호출하려면 인스턴스를 생성해야 하지만,  
정적 메서드는 인스턴스를 생성하지 않아도 호출할 수 있다.

```javascript
function Foo() {}

// 프로토타입 메서드
// this를 참조하지 않는 프로토타입 메소드는 정적 메서드로 변경해도 동일한 효과를 얻을 수 있다.
Foo.prototype.x = function () {
  console.log('x');
};

const foo = new Foo();
// 프로토타입 메서드를 호출하려면 인스턴스를 생성해야 한다.
foo.x(); // x

// 정적 메서드
Foo.x = function () {
  console.log('x');
};

// 정적 메서드는 인스턴스를 생성하지 않아도 호출할 수 있다.
Foo.x(); // x
```


아래 MDN사이트에 가면 자세히 나온다.
https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object

![image](https://user-images.githubusercontent.com/80154058/144739067-db36167a-bd70-48f1-8517-08a2bec3efb1.png)


## 13.프로퍼티 존재 확인
### 13.1 in 연산자
`in`연산자는 객체 내에 특정 프로퍼티가 존재하는지 여부를 확인한다.
```javascript
/**
 * key: 프로퍼티 키를 나타내는 문자열
 * object: 객체로 평가되는 표현식
 */
key in object
```

```javascript
const person = {
  name: 'Lee',
  address: 'Seoul'
};

// person 객체에 name 프로퍼티가 존재한다.
console.log('name' in person);    // true
// person 객체에 address 프로퍼티가 존재한다.
console.log('address' in person); // true
// person 객체에 age 프로퍼티가 존재하지 않는다.
console.log('age' in person);     // false
```

`in`대신 ES6에서 도입된 `Reflect.has`메서드도 사용할 수 있다. `in`과 동일하게 동작한다.
```javascript
const person = { name: 'Lee' };

console.log(Reflect.has(person, 'name'));     // true
console.log(Reflect.has(person, 'toString')); // true
```

### 13.2 Object.prototype.hasOwnProperty 메서드
`Object.prototype.hasOwnProperty`메서드를 사용해 객체에 특정 프로퍼티가 존재하는지 확인할 수 있다.

```javascript
console.log(person.hasOwnProperty('name')); // true
console.log(person.hasOwnProperty('age'));  // false
```
객체 고유의 프로퍼티인 경우에만 `true`를 반환한다.  
만약 프로퍼티키가 객체 고유의 프로퍼티가 아니고 상속 받은 경우 `false`를 반환한다.  

```javascript
console.log(person.hasOwnProperty('toString')); // false
```


## 14. 프로퍼티 열거
### 14.1 for...in 문
객체의 모든 프로퍼티를 순회하며 열거(`enumeration`)하려면 `for...in`문을 사용한다.  

- 프로퍼티 키를 순회한다.
- 프로토타입 체인을 모두 순회한다.
- `[[Enumerable]]`값을 참조한다.
- 심벌을 열거하지 않는다.
- 순서를 보장하지 않는다.(하지만 모던 브라우저는 순서를 보장한다.)

```javascript
for (변수 선언문 in 객체) {...}
```

```javascript
const person = {
  name: 'Lee',
  address: 'Seoul'
};

// for...in 문의 변수 prop에 person 객체의 프로퍼티 키가 할당된다.
for (const key in person) {
  console.log(key + ': ' + person[key]);
}
// name: Lee
// address: Seoul
```

`for...in`문은 객체의 프로토타입 체인 상에 존재하는 모든 프로퍼티 중에서 프로퍼티 어트리뷰트`[[Enumerable]]`값이 `true`인 프로퍼티만 열거(`enumeration`)한다.  

`toString` 같은 경우 프로토타입 체인 상에 존재하지만 `[[Enumerable]]`값이 `false`이기 때문에 `for...in`문에서 열거되지 않는다.  

```javascript
Object.getOwnPropertyDescriptor(Object.prototype, "toString")
// {writable: true, enumerable: false, configurable: true, value: ƒ}
```

또한 `for...in`문은 프로퍼티가 심벌인 경우에도 열거하지 않는다.
```javascript
const sym = Symbol();
const obj = {
  a: 1,
  [sym]: 10
};

for (const key in obj) {
  console.log(key + ': ' + obj[key]);
}
// a: 1
```

  

※ `hasOwnProperty`, `enumerable` 예제
```javascript
function Person(){

}
Person.prototype.name = 'inyong';

var p = new Person();
p.age = 10;
for(v in p) console.log(v);
// age
// name

// 만약 본인 것만 출력하려면 hasOwnProperty 메서드를 활용한다.
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

// firstName은 enumerable 값이 false이기 때문에 출력되지 않는다.
for(v in p) console.log(v);
// age
// name
// lastName
```

  

배열에서는 `for...in`보다는 일반적인 `for`문이나 `for...of`문이나 `Array.prototype.forEach`메서드를 사용하기를 권장한다.   

배열도 객체이기 때문에 자신의 프로퍼티나 상속받은 프로퍼티가 포함될 수 있다.

```javascript
const arr = [1, 2, 3];
arr.x = 10; // 배열도 객체이므로 프로퍼티를 가질 수 있다.

for (const i in arr) {
  // 프로퍼티 x도 출력된다.
  console.log(arr[i]); // 1 2 3 10
};

// arr.length는 3이다.
for (let i = 0; i < arr.length; i++) {
  console.log(arr[i]); // 1 2 3
}

// forEach 메서드는 요소가 아닌 프로퍼티는 제외한다.
arr.forEach(v => console.log(v)); // 1 2 3

// for...of는 변수 선언문에서 선언한 변수에 키가 아닌 값을 할당한다.
for (const value of arr) {
  console.log(value); // 1 2 3
};
```

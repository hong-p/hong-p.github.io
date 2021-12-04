---
title: "모던 자바스크립트 딥 다이브 19장 프로토타입"
excerpt: "[javascript] 모던 자바스크립트 Deep Dive 19장 프로토타입"
categories:
 - Javascript
tags:
 - Javascript
toc: true
toc_sticky: true
last_modified_at: 2021-12-04
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
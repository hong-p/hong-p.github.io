---
title: "모던 자바스크립트 딥 다이브 25장 클래스"
excerpt: "[javascript] 모던 자바스크립트 Deep Dive 25장 클래스"
categories:
 - Javascript
tags:
 - Javascript
toc: true
toc_sticky: true
last_modified_at: 2021-12-14
---

# 25장 클래스

## 1.클래스는 프로토타입의 문법적 설탕인가?
자바스크립트는 프로토타입 기반 객체지향 언어다.  
프로토타입 기반 객체지향 언어는 클래스가 필요 없는(`class free`)객체지향 프로그래밍 언어다.  
ES5에서는 클래스 없이도 아래와 같이 생성자 함수와 프로토타입을 통해 객체지향 언어의 상속을 구현할 수 있다.

```javascript
// ES5 생성자 함수
var Person = (function () {
  // 생성자 함수
  function Person(name) {
    this.name = name;
  }

  // 프로토타입 메서드
  Person.prototype.sayHi = function () {
    console.log('Hi! My name is ' + this.name);
  };

  // 생성자 함수 반환
  return Person;
}());

// 인스턴스 생성
var me = new Person('Lee');
me.sayHi(); // Hi! My name is Lee
```

ES6에서 추가된 클래스가 기존의 프로토타입 기반 객체지향 모델을 폐지하고 새롭게 클래스 기반 객체지향 모델을 제공하는 것은 아니다.  
클래스는 함수이며 기존 프로토타입 기반 패턴을 클래스 기반 패턴처럼 사용할 수 있도록 하는 문법적 설탕(`syntactic sugar`)이라고 볼 수도 있다.  

클래스와 생성자 함수는 모두 프로토타입 기반의 인스턴스를 생성하지만 정확히 동일하게 동작하지는 않는다.  

차이점은 아래와 같다.
1. 클래스를 `new`연산자 없이 호출하면 에러가 발생한다.(생성자 함수를 `new`연산자 없이 호출하면 일반 함수로 호출된다.)
2. 클래스는 상속을 지원하는 `extends`와 `super`키워드를 제공한다.(생성자 함수는 지원하지 않는다.)
3. 클래스는 호이스팅이 발생하지 않는 것처럼 동작한다.(함수 선언문은 함수 호이스팅, 함수 표현식은 변수 호이스팅이 발생한다.)
4. 클래스 내의 모든 코드에는 암묵적으로 `strict mode`가 지정되어 실행되며 해제할 수 없다.(생성자 함수는 지정되지 않는다.)
5. 클래스의 `constructor`, 프로토타입 메서드, 정적 메서드는 열거되지 않는다.(모두 프로퍼티 어트리뷰트 `[[Enumerable]]`의 값이 `false`다.)


## 2.클래스 정의
- 클래스는 `class`키워드를 사용항여 정의한다.  
- 클래스 이름은 생성자 함수와 마찬가지로 **파스칼 케이스**를 사용하는 것이 일반적이다. (하지만 사용하지 않아도 에러가 발생하지는 않는다.)  

```javascript
// 클래스 선언문
class Person {}
```

- 표현식으로 클래스를 정의할 수도 있다.(일반적이지 않음, 익명 혹은 기명 모두 가능하다.)  

```javascript
// 익명 클래스 표현식
const Person = class {};

// 기명 클래스 표현식
const Person = class MyClass {};
```
- 클래스는 일급 객체이다.(클래스를 표현식으로 정의할 수 있다는 것이 일급 객체임을 의미한다.)

> ※ 일급 객체의 특징  
> -무명의 리터럴로 생성할 수 있다.(즉, 런타임에 생성이 가능하다.)  
> -변수나 자료구조(객체, 배열등)에 저장할 수 있다.  
> -함수의 매개변수에게 전달할 수 있다.  
> -함수의 반환값으로 사용할 수 있다.

**클래스는 함수**다. 따라서 클래스는 **값처럼 사용할 수 있는 일급 객체**다.  

클래스 몸체는 0개 이상의 메서드만 정의할 수 있다.  
클래스 몸체에서 정의할 수 있는 메서드는 `constructor`, **프로토타입 메서드**, **정적 메서드** 세 가지가 있다.  

```javascript
// 클래스 선언문
class Person {
  // 생성자
  constructor(name) {
    // 인스턴스 생성 및 초기화
    this.name = name; // name 프로퍼티는 public하다.
  }

  // 프로토타입 메서드
  sayHi() {
    console.log(`Hi! My name is ${this.name}`);
  }

  // 정적 메서드
  static sayHello() {
    console.log('Hello!');
  }
}

// 인스턴스 생성
const me = new Person('Lee');

// 인스턴스의 프로퍼티 참조
console.log(me.name); // Lee
// 프로토타입 메서드 호출
me.sayHi(); // Hi! My name is Lee
// 정적 메서드 호출
Person.sayHello(); // Hello!
```

생성자 함수와 클래스를 비교해 보면  
![image](https://user-images.githubusercontent.com/80154058/146196443-6180771f-6296-4ec6-8d21-cb8cf8c44393.png)

## 3.클래스 호이스팅
클래스는 함수로 평가된다.  
```javascript
// 클래스 선언문
class Person {}

console.log(typeof Person); // function
```

클래스 선언문으로 정의한 클래스는 함수 선언문과 같이 **소스코드 평가 과정**, 즉 런타임 이전에 먼저 평가되어 **함수 객체를 생성**한다.  

클래스 선언문은 `const`, `let`과 같이 호이스팅이 발생되지만 발생하지 않는 것처럼 보인다.
```javascript
const Person = '';

{
  // 호이스팅이 발생하지 않는다면 ''이 출력되어야 한다.
  console.log(Person);
  // ReferenceError: Cannot access 'Person' before initialization

  // 클래스 선언문
  class Person {}
}
```

즉, `var`, `let`, `const`, `function`, `function*`, `class`키워드를 사용하여 선언된 모든 식별자는 호이스팅된다. 모든 선언문은 런타임 이전에 먼저 실행되기 때문이다.  

## 4.인스턴스 생성
클래스는 생성자 함수이며 `new`연산자와 함께 호출되어 인스턴스를 생성한다.  

클래스는 `new`연산자와 함께 생성자 함수로 호출되어야만 인스턴스를 생성할 수 있다.(다른방법 없음)

```javascript
class Person {}

// 인스턴스 생성
const me = new Person();
console.log(me); // Person {}
```

`new`연산자 없이 호출하면 에러가 발생한다.

```javascript
class Person {}

// 클래스를 new 연산자 없이 호출하면 타입 에러가 발생한다.
const me = Person();
// TypeError: Class constructor Foo cannot be invoked without 'new'
```

클래스 표현식으로 정의된 클래스의 경우 식별자(`Person`)를 사용해야한다.  
기명 클래스의 클래스 이름(`MyClass`)을 사용해 생성하려고 하면 에러가 발생한다.  
```javascript
const Person = class MyClass {};

// 함수 표현식과 마찬가지로 클래스를 가리키는 식별자로 인스턴스를 생성해야 한다.
const me = new Person();

// 클래스 이름 MyClass는 함수와 동일하게 클래스 몸체 내부에서만 유효한 식별자다.
console.log(MyClass); // ReferenceError: MyClass is not defined

const you = new MyClass(); // ReferenceError: MyClass is not defined
```

## 5.메서드
클래스 몸체에 정의할 수 있는 메서드
- `constructor`(생성자)
- 프로토타입 메서드
- 정적 메서드

> ※ 클래스 정의에 대한 새로운 제안  
> -ECMAScript 사양에 따르면 인스턴스 프로퍼티는 반드시 `constructor`내부에서 정의해야 한다.  
> -하지만 클래스 몸체에 메서드뿐만이 아니라 **프로퍼티도 정의할 수 있게 새로운 표준 사양이 제안되어 있다.**  
> -최신 모던 브라우저에는 이미 반영되어 사용이 가능하다.


### 5.1 constructor

**1.**`constructor`**는 인스턴스를 생성하고 초기화하기 위한 특수한 메서드다.**  
```javascript
class Person {
  // 생성자
  constructor(name) {
    // 인스턴스 생성 및 초기화
    this.name = name;
  }
}
```

**2.**`constructor`**는 이름을 변경할 수 없다.**  
**3.클래스가 평가되어 생성된 함수 객체나 클래스가 생성한 인스턴스에는** `constructor`**메서드가 존재하지 않는다.**(단순한 메서드가 아님)  
**4.**`constructor`**는 메서드로 해석되지 않고 클래스가 평가되어 생성한 함수 객체 코드의 일부가 된다.**  
```javascript
class Person {
  // 생성자
  constructor(name) {
    // 인스턴스 생성 및 초기화
    this.name = name;
  }
}

// 클래스는 함수다.
console.log(typeof Person); // function
console.dir(Person);

// 인스턴스 생성
const me = new Person('Lee');
console.log(me);
```

클래스가 평가되어 생성된 함수 객체(`constructor`프로퍼티 없음)  
![image](https://user-images.githubusercontent.com/80154058/146127155-2296b9e6-1923-4d22-80c0-36184ca6a6f1.png)

클래스가 생성한 인스턴스(`constructor`프로퍼티 없음)  
![image](https://user-images.githubusercontent.com/80154058/146127214-a68f5187-967f-4da9-a642-e744a2ab9c25.png)

> ※ 클래스`constructor` vs 프로토타입`constructor`
> 클래스`constructor` | 프로토타입`constructor`
> :---|:---
> 메서드 | 프로퍼티
> 메서드로 해석되지 않고 클래스가 평가되어 생성한 함수 객체 코드의 일부가 된다. | 모든 프로토타입이 가지고 있는 프로퍼티, 생성자 함수를 가리킨다.

**5.**`constructor`**는 생략 가능하다.**(인스턴스를 초기화하려면 생략하면 안된다.)  
**6.클래스에 2개 이상의** `constructor`**를 포함하면 문법 에러가 발생한다.**  

```javascript
class Person {
  constructor() {}
  constructor() {}
}
// SyntaxError: A class may only have one constructor
```
생략하면 빈`constructor`가 암묵적으로 정의된다.
```javascript
class Person {
  // constructor를 생략하면 다음과 같이 빈 constructor가 암묵적으로 정의된다.
  constructor() {}
}

// 빈 객체가 생성된다.
const me = new Person();
console.log(me); // Person {}
```

**8.프로퍼티가 추가되어 초기화된 인스턴스를 생성하려면** `construcotr`내부에서 `this`에 인스턴스 프로퍼티를 추가한다.(이때는  `constructor`를 생략하면 안된다.)  
```javascript
class Person {
  constructor(name, address) {
    // 인수로 인스턴스 초기화
    this.name = name;
    this.address = address;
  }
}

// 인수로 초기값을 전달한다. 초기값은 constructor에 전달된다.
const me = new Person('Lee', 'Seoul');
console.log(me); // Person {name: "Lee", address: "Seoul"}
```
**9.**`constructor` **내부에서 생성자 함수와 동일하게 암묵적으로** `this`**를 반환한다.**(즉 다른 객체를 명시적으로 반환하면 안됨. 생성자 함수와 동일)  


클래스와 생성자 함수 비교
```javascript
// 클래스
class Person {
  // 생성자
  constructor(name) {
    // 인스턴스 생성 및 초기화
    this.name = name;
  }
}

// 생성자 함수
function Person(name) {
  // 인스턴스 생성 및 초기화
  this.name = name;
}
```

### 5.2 프로토타입 메서드
**클래스 몸체에 정의한 메서드**는 기본적으로 **프로토타입 메서드**가 된다.(생성자 함수에 의한 객체 생성 방식과는 다르게 `prototype`프로퍼티에 메서드를 추가하지 않아도 된다.)  

생성자 함수의 프로토타입 메서드 추가 방식
```javascript
// 생성자 함수
function Person(name) {
  this.name = name;
}

// 프로토타입 메서드
Person.prototype.sayHi = function () {
  console.log(`Hi! My name is ${this.name}`);
};

const me = new Person('Lee');
me.sayHi(); // Hi! My name is Lee
```

클래스의 메서드 추가 방식  
```javascript
class Person {
  // 생성자
  constructor(name) {
    // 인스턴스 생성 및 초기화
    this.name = name;
  }

  // 프로토타입 메서드
  sayHi() {
    console.log(`Hi! My name is ${this.name}`);
  }
}

const me = new Person('Lee');
me.sayHi(); // Hi! My name is Lee
```

### 5.3 정적 메서드
정적(`static`)메서드는 인스턴스를 생성하지 않아도 호출할 수 있는 메서드를 말한다.  

생성자 함수의 경우 다음과 같이 명시적으로 생성자 함수에 메서드를 추가해 정적 메서드를 생성한다. 
```javascript
// 생성자 함수
function Person(name) {
  this.name = name;
}

// 정적 메서드
Person.sayHi = function () {
  console.log('Hi!');
};

// 정적 메서드 호출
Person.sayHi(); // Hi!
```

클래스에서는 `static`키워드를 붙이면 정적 메서드(클래스 메서드)가 된다.
```javascript
class Person {
  // 생성자
  constructor(name) {
    // 인스턴스 생성 및 초기화
    this.name = name;
  }

  // 정적 메서드
  static sayHi() {
    console.log('Hi!');
  }
}
```

정적 메서드는 클래스에 바인딩된 메서드가 된다.  
클래스는 함수 객체로 평가되므로 자신의 프로퍼티/메서드를 소유할 수 있다.  

클래스는 클래스 정의(클래스 선언문, 클래스 표현식)가 평가되는 시점에 함수 객체가 되므로 인스턴스와 달리 별다른 생성 과정이 필요 없다.

```javascript
// 정적 메서드는 클래스로 호출한다.
// 정적 메서드는 인스턴스 없이도 호출할 수 있다.
Person.sayHi(); // Hi!
```

정적 메서드는 인스턴스로 호출할 수 없다.(정적 메서드가 바인딩된 클래스는 인스턴스의 프로토타입 체인상에 존재하지 않는다.)  
```javascript
// 인스턴스 생성
const me = new Person('Lee');
me.sayHi(); // TypeError: me.sayHi is not a function
```

### 5.4 정적 메서드와 프로토타입 메서드의 차이

### 5.5 클래스에서 정의한 메서드의 특징


## 6.클래스의 인스턴스 생성 과정


## 7.프로퍼티

### 7.1 인스턴스 프로퍼티

### 7.2 접근자 프로퍼티

### 7.3 클래스 필드 정의 제안

### 7.4 private 필드 정의 제안

### 7.5 static 필드 정의 제안


## 8.상속에 의한 클래스 확장

### 8.1 클래스 상속과 생성자 함수 상속

### 8.2 extends 키워드

### 8.2 동적 상속

### 8.4 서브클래스의 constructor

### 8.5 super 키워드

### 8.6 상속 클래스의 인스턴스 생성 과정

### 8.7 표준 빌트인 생성자 함수 확장
 

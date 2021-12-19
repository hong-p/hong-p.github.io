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

1. 정적 메서드와 프로토타입 메서드는 자신이 속해있는 프로토타입 체인이 다르다.
2. 정적 메서드는 클래스로 호출하고 프로토타입 메서드는 인스턴스로 호출한다.
3. 정적 메서드는 인스턴스 프로퍼티를 참조할 수 없지만 프로토타입 메서드는 인스턴스 프로퍼티를 참조할 수 있다.  

정적 메서드 예제  
```javascript
class Square {
  // 정적 메서드
  static area(width, height) {
    return width * height;
  }
}

console.log(Square.area(10, 10)); // 100
```

프로토타입 메서드 예제  
```javascript
class Square {
  constructor(width, height) {
    this.width = width;
    this.height = height;
  }

  // 프로토타입 메서드
  area() {
    return this.width * this.height;
  }
}

const square = new Square(10, 10);
console.log(square.area()); // 100
```

> ※ 표준 빌트인 객체의 정적 메서드
> ```javascript
> // 표준 빌트인 객체의 정적 메서드
> Math.max(1, 2, 3);          // -> 3
> Number.isNaN(NaN);          // -> true
> JSON.stringify({ a: 1 });   // -> "{"a":1}"
> Object.is({}, {});          // -> false
> Reflect.has({ a: 1 }, 'a'); // -> true
> ```

### 5.5 클래스에서 정의한 메서드의 특징
- `function`키워드를 생략한 메서드 축약 표현을 사용한다.
- 객체 리터럴과는 다르게 클래스에 메서드를 정의할 때는 콤마가 필요 없다.
- 암묵적으로 `strict mode`로 실행된다.
- `for...in`문이나 `Object.keys`메서드 등으로 열거할 수 없다.(`[[Enumerable]]`값이 `false`다.)
- 내부 메서드 `[[Construct]]`를 갖지 않는 `non-constructor`다.(`new`연산자와 함께 호출할 수 없다.)


## 6.클래스의 인스턴스 생성 과정
**1.인스턴스 생성과** `this`**바인딩**  
- `new`연산자와 함께 클래스를 호출하면 암묵적으로 빈 객체(인스턴스)가 생성된다.
- 빈 객체는 `this`에 바인딩된다.

**2.인스턴스 초기화**  
- `constructor` 내부의 코드가 실행되며 `this`에 바인딩되어 있는 인스턴스를 초기화한다.
- 인스턴스에 프로퍼티 추가하고 초기화한다.

**3.인스턴스 반환**  
- 클래스의 모든 처리가 끝나면 완성된 인스턴스가 바인딩된 `this`가 암묵적으로 반환된다.
```javascript
class Person {
  // 생성자
  constructor(name) {
    // 1. 암묵적으로 인스턴스가 생성되고 this에 바인딩된다.
    console.log(this); // Person {}
    console.log(Object.getPrototypeOf(this) === Person.prototype); // true

    // 2. this에 바인딩되어 있는 인스턴스를 초기화한다.
    this.name = name;

    // 3. 완성된 인스턴스가 바인딩된 this가 암묵적으로 반환된다.
  }
}
```

## 7.프로퍼티
### 7.1 인스턴스 프로퍼티
`constructor` 내부의 `this`는 앞서 봤듯이 클래스가 생성한 빈 객체(인스턴스)가 바인딩 되어있다.  

따라서 `this.name = name;`코드가 실행되면 `this`에 인스턴스 프로퍼티를 추가하고 초기화한다.  

```javascript
class Person {
  constructor(name) {
    // 인스턴스 프로퍼티
    this.name = name;
  }
}

const me = new Person('Lee');
console.log(me); // Person {name: "Lee"}
```

### 7.2 접근자 프로퍼티
[16.3.2절 접근자 프로퍼티](https://hong-p.github.io/javascript/javascript-deepdive-ch16/#32-%EC%A0%91%EA%B7%BC%EC%9E%90-%ED%94%84%EB%A1%9C%ED%8D%BC%ED%8B%B0) 에서 보았듯이 접근자 프로퍼티는 자체적으로는 값(`[[Value]]`내부슬롯)을 갖지 않고 다른 데이터 프로퍼티의 값을 읽거나 저장할 때 사용하는 **접근자 함수**(아래 예제에서 `fullName` 프로퍼티)로 구성된 프로퍼티다.  

- 접근자 프로퍼티는 클래스에서도 사용할 수 있다.
- 접근자 프로퍼티는 자체적으로는 값을 갖지 않고 읽거나 저장할 때 사용하는 접근자 함수(`getter`, `setter`)로 구성되어 있다.
- `getter`는 메서드 이름 앞에 `get`키워드를 사용해 정의한다.
- `setter`는 메서드 이름 앞에 `set`키워드를 사용해 정의한다.
- `getter`와 `setter` 이름은 인스턴스 프로퍼티처럼 사용된다.(ex `me.fullName = 'Heegun Lee';`, `console.log(me.fullName);`)
- `getter`는 무언가를 취들할 때 사용하므로 반드시 `return`이 있어야 한다.
- `setter`는 무언가를 프로퍼티에 할당할 때 사용하므로 반드시 매개변수가 있어야한다.
- 접근자 프로퍼티는 인스턴스의 프로로타입이 아니라 **프로토타입의 프로퍼티**가 된다.


```javascript
class Person {
  constructor(firstName, lastName) {
    this.firstName = firstName;
    this.lastName = lastName;
  }

  // fullName은 접근자 함수로 구성된 접근자 프로퍼티다.
  // getter 함수
  get fullName() {
    return `${this.firstName} ${this.lastName}`;
  }

  // setter 함수
  set fullName(name) {
    [this.firstName, this.lastName] = name.split(' ');
  }
}

const me = new Person('Ungmo', 'Lee');

// 데이터 프로퍼티를 통한 프로퍼티 값의 참조.
console.log(`${me.firstName} ${me.lastName}`); // Ungmo Lee

// 접근자 프로퍼티를 통한 프로퍼티 값의 저장
// 접근자 프로퍼티 fullName에 값을 저장하면 setter 함수가 호출된다.
me.fullName = 'Heegun Lee';
console.log(me); // {firstName: "Heegun", lastName: "Lee"}

// 접근자 프로퍼티를 통한 프로퍼티 값의 참조
// 접근자 프로퍼티 fullName에 접근하면 getter 함수가 호출된다.
console.log(me.fullName); // Heegun Lee

// fullName은 접근자 프로퍼티다.
// 접근자 프로퍼티는 get, set, enumerable, configurable 프로퍼티 어트리뷰트를 갖는다.
console.log(Object.getOwnPropertyDescriptor(Person.prototype, 'fullName'));
// {get: ƒ, set: ƒ, enumerable: false, configurable: true}
```


### 7.3 클래스 필드 정의 제안
자바스크립트의 클래스 몸체에는 메서드만 선언할 수 있다. 클래스 몸체에 클래스 필드를 선언하면 문법 에러(`SyntaxError`)가 발생한다.  

하지만 최신브라우저(Chrome72이상) 또는 Node.js(버전 12이상)에서는 정상 동작한다.  
그 이유는 자바스크립트에서도 인스턴스 프로퍼티를 마치 클래스 기반 객체지향 언어의 클래스 필드처럼 정의할 수 있는 새로운 표준 사양인 "[Class field declarations](https://github.com/tc39/proposal-class-fields)"가 TC39프로세스의 stage 3(candidate)에 제안되어 있기 때문이다.  

> ※ [TC39프로세스](https://tc39.es/process-document/)  
> ECMA-262 사양에 새로운 표준 사양을 추가하기 위해 공식적으로 명문화해 놓은 과정이다.  
> 0단계 부터 4단계까지 총 5단계로 구성되어 있다.  
> stage 0:starwman -> stage 1: proposal -> stage 2: draft -> stage 3: candidate -> stage 4: finished  
> stage 3(candidate) 까지 승급한 제안은 심각한 문제가 없는 한 변경되지 않고 대부분 stage 4로 승급되고,  
> stage 4(finished)까지 승급한 제안은 큰 이변이 없는 이상 차기 ECMAScript 버전에 포함된다.

```javascript
class Person {
  // 클래스 필드 정의
  name = 'Lee';
}

const me = new Person();
console.log(me); // Person {name: "Lee"}
```
- 최신 브라우저(chrome 72이상), Node.js(버전 12이상)에서만 사용가능하다.
- 클래스 필드를 정의하는 경우 `this`에 클래스 필드를 바인딩해서는 안된다.

```javascript
class Person {
  // this에 클래스 필드를 바인딩해서는 안된다.
  this.name = ''; // SyntaxError: Unexpected token '.'
}
```
- 참조하는 경우 `this`를 반드시 사용해야 한다.

```javascript
class Person {
  // 클래스 필드
  name = 'Lee';

  constructor() {
    // 참조할때는 this를 반드시 사용해야 한다.
    console.log(name); // ReferenceError: name is not defined
  }
}

new Person();
```
- 초기값을 할당하지 않으면 `undefined`를 갖는다.
- 초기화는 `constructor`에서 해야 한다.

```javascript
class Person {
  name;

  constructor(name) {
    // 초기화할 때는 constructor에서 해야한다.
    // 클래스 필드 초기화.
    this.name = name;
  }
}

const me = new Person('Lee');
console.log(me); // Person {name: "Lee"}
```
- 함수는 일급 객체이므로 함수를 클래스 필드에 할당할 수 있다. 즉 클래스 필드를 통해 메서드를 정의할 수도 있다.(이경우 프로토타입 메서드가 아닌 **인스턴스 메서드**가 된다. **권장하지 않음**)

```javascript
class Person {
  // 클래스 필드에 문자열을 할당
  name = 'Lee';

  // 클래스 필드에 함수를 할당
  getName = function () {
    return this.name;
  }
  // 화살표 함수로 정의할 수도 있다.
  // getName = () => this.name;
}

const me = new Person();
console.log(me); // Person {name: "Lee", getName: ƒ}
console.log(me.getName()); // Lee
```


### 7.4 private 필드 정의 제안
`private`필드도 TC39 프로세스의 stage 3(candidate)로 제안되어있다.([class-private-fields](https://github.com/tc39/proposal-class-fields#private-fields))  
최신브라우저(chrome 74이상)와 Node.js(버전 12이상)에 이미 구현되어있다.  

- `private`필드는 이름 앞에 `#`을 붙여준다. 참조할 때도 `#`을 붙여주어야 한다.

```javascript
class Person {
  // private 필드 정의
  #name = '';

  constructor(name) {
    // private 필드 참조
    this.#name = name;
  }
}

const me = new Person('Lee');

// private 필드 #name은 클래스 외부에서 참조할 수 없다.
console.log(me.#name);
// SyntaxError: Private field '#name' must be declared in an enclosing class
```
- `private`필드에 직접 접근할 수 있는 방법은 없다. 다만 접근자 프로퍼티를 통해 간접적으로 가능하다.

```javascript
class Person {
  // private 필드 정의
  #name = '';

  constructor(name) {
    this.#name = name;
  }

  // name은 접근자 프로퍼티다.
  get name() {
    // private 필드를 참조하여 trim한 다음 반환한다.
    return this.#name.trim();
  }
}

const me = new Person(' Lee ');
console.log(me.name); // Lee
```
- `private` 필드는 반드시 클래스 몸체에 정의해야 한다. `constructor`에 정의하면 에러가 발생한다.

```javascript
class Person {
  constructor(name) {
    // private 필드는 클래스 몸체에서 정의해야 한다.
    this.#name = name;
    // SyntaxError: Private field '#name' must be declared in an enclosing class
  }
}
```


### 7.5 static 필드 정의 제안
`static`키워드를 사용하여 `static public field`, `static private field`, `static private 메서드`를 정의할 수 있는 새로운 표준 사양인 "Static class features"가 TC39 프로세스의 stage 3(candidate)에 제안되어 있다.([static-class-features](https://github.com/tc39/proposal-static-class-features))  
최신 브라우저(chrome 72이상)와 Node.js(버전 12 이상)에 이미 구현되어 있다.  

```javascript
class MyMath {
  // static public 필드 정의
  static PI = 22 / 7;

  // static private 필드 정의
  static #num = 10;

  // static 메서드
  static increment() {
    return ++MyMath.#num;
  }
}

console.log(MyMath.PI); // 3.142857142857143
console.log(MyMath.increment()); // 11
```

## 8.상속에 의한 클래스 확장
### 8.1 클래스 상속과 생성자 함수 상속
상속에 의한 클래스 확장은 프로토타입 기반 상속과는 다른 개념이다.  
프로토타입 기반 상속|상속에 의한 클래스 확장
:---|:---
프로토타입 체인을 통해 다른 객체의 자산을 상속받는 개념 | 기존 클래스를 상속받아 새로운 클래스를 확장(`extends`)하여 정의하는 것

![image](https://user-images.githubusercontent.com/22947274/146665979-364ce758-7330-4391-8b6b-c7372e92f9e4.png)

클래스는 상속을 통해 기존 클래스를 확장할 수 있는 문법이 기본적으로 제공되지만 생성자 함수는 그렇지 않다.  

```javascript
class Animal {
  constructor(age, weight) {
    this.age = age;
    this.weight = weight;
  }

  eat() { return 'eat'; }

  move() { return 'move'; }
}

// 상속을 통해 Animal 클래스를 확장한 Bird 클래스
class Bird extends Animal {
  fly() { return 'fly'; }
}

const bird = new Bird(1, 5);

console.log(bird); // Bird {age: 1, weight: 5}
console.log(bird instanceof Bird); // true
console.log(bird instanceof Animal); // true

console.log(bird.eat());  // eat
console.log(bird.move()); // move
console.log(bird.fly());  // fly
```

상속에 의해 확장된 클래스 `Bird`를 통해 생성된 인스턴스의 프로토타입 체인은 아래와 같다.  
![image](https://user-images.githubusercontent.com/22947274/146666004-8fbf22ba-c7f7-4ffc-b0a5-d52771a91bbe.png)

### 8.2 extends 키워드
상속을 통해 클래스를 확장하려면 `extends`키워드를 사용하여 상속받을 클래스를 정의한다.  
```javascript
// 수퍼(베이스/부모)클래스
class Base {}

// 서브(파생/자식)클래스
class Derived extends Base {}
```

수퍼클래스와 서브클래스는 인스턴스의 프로토타입 체인뿐 아니라 클래스 간의 프로토타입 체인도 생성한다. 이를 통해 **프로토타입 메서드**, **정적 메서드** 모두 **상속이 가능**하다.

### 8.2 동적 상속
`extends`키워드는 클래스뿐 아니라 **생성자 함수를 상속받아 클래스를 확장**할 수도 있다.(`extends`키워드 앞에는 반드시 클래스가 와야한다.)  
```javascript
// 생성자 함수
function Base(a) {
  this.a = a;
}

// 생성자 함수를 상속받는 서브클래스
class Derived extends Base {}

const derived = new Derived(1);
console.log(derived); // Derived {a: 1}
```
또한 `extends`키워드 다음에는 클래스뿐만 아니라 `[[Construct]]`내부 메서드를 갖는 함수 객체로 평가될수 있는 **모든 표현식**을 사용할 수 있다.(이를 통해 동적으로 상속가능)  
```javascript
function Base1() {}

class Base2 {}

let condition = true;

// 조건에 따라 동적으로 상속 대상을 결정하는 서브클래스
class Derived extends (condition ? Base1 : Base2) {}

const derived = new Derived();
console.log(derived); // Derived {}

console.log(derived instanceof Base1); // true
console.log(derived instanceof Base2); // false
```

### 8.4 서브클래스의 constructor 
서브클래스에서 `constructor`를 생략하면 암묵적으로 정의된다.  
```javascript
// 수퍼클래스
class Base {}

// 서브클래스
class Derived extends Base {}
```
위와 같이 생략한 경우 아래와 같이 암묵적으로 생성된다.
```javascript
// 수퍼클래스
class Base {
  constructor() {}
}

// 서브클래스
class Derived extends Base {
  constructor() { super(); }
}

const derived = new Derived();
console.log(derived); // Derived {}
```

### 8.5 super 키워드
`super`키워드는 함수처럼 호출할 수도 있고 `this`와 같이 식별자처럼 참조할 수 있는 특수한 키워드다.  
- `super`를 호출하면 수퍼클래스의 `constructor`를 호출한다.
- `super`를 참조하면 수퍼클래스의 메서드를 호출할 수 있다.

<br>

**1.**`super` **호출**  
`super`를 호출하면 수퍼클래스의 `constuctor`를 호출한다.  
수퍼클래스의 `constructor`내부에서 추가한 프로퍼티를 그대로 갖는 인스턴스를 생성한다면 서브클래스의 `constructor`를 생략할 수 있다. 이때 `new`연산자와 함께 서브클래스를 호출하면서 전달한 인수는 모두 서브클래스에 암무적으로 정의된 `cosntructor`의 `super`호출을 통해 수퍼클래스의 `constructor`에 전달된다.  
```javascript
// 수퍼클래스
class Base {
  constructor(a, b) {
    this.a = a;
    this.b = b;
  }
}

// 서브클래스
class Derived extends Base {
  // 다음과 같이 암묵적으로 constructor가 정의된다.
  // constructor(...args) { super(...args); }
}

const derived = new Derived(1, 2);
console.log(derived); // Derived {a: 1, b: 2}
```

- 서브클래스에서 `constructor`를 생략하지 않는 경우 서브클래스의 `constructor`에서는 반드시 `super`를 호출해야 한다.
  
```javascript
class Base {}

class Derived extends Base {
  constructor() {
    // ReferenceError: Must call super constructor in derived class before accessing 'this' or returning from derived constructor
    console.log('constructor call');
  }
}

const derived = new Derived();
```
- 서브클래스의 `constructor`에서 `super`를 호출하기 전에는 `this`를 참조할 수 없다.(인스턴스 생성과 `this`바인딩은 수퍼클래스에서 하기 때문이다.)

```javascript
class Base {}

class Derived extends Base {
  constructor() {
    // ReferenceError: Must call super constructor in derived class before accessing 'this' or returning from derived constructor
    this.a = 1;
    super();
  }
}

const derived = new Derived(1);
```
- `super`는 반드시 서브클래스의 `constructor`에서만 호출한다. 서브클래스가 아닌 클래스의 `consturcotr`나 함수에서 `super`를 호출하면 에러가 발생한다.

```javascript
class Base {
  constructor() {
    super(); // SyntaxError: 'super' keyword unexpected here
  }
}

function Foo() {
  super(); // SyntaxError: 'super' keyword unexpected here
}
```

<br>

**2.**`super`**참조**  
메서드 내에서 `super`를 참조하면 수퍼클래스의 메서드를 호출할 수 있다.  

- 서브클래스의 프로토타입 메서드 내에서 `super.sayHi`는 수퍼클래스의 프로토타입 메서드 `sayHi`를 가리킨다.

```javascript
// 수퍼클래스
class Base {
  constructor(name) {
    this.name = name;
  }

  sayHi() {
    return `Hi! ${this.name}`;
  }
}

// 서브클래스
class Derived extends Base {
  sayHi() {
    // super.sayHi는 수퍼클래스의 프로토타입 메서드를 가리킨다.
    return `${super.sayHi()}. how are you doing?`;
  }
}

const derived = new Derived('Lee');
console.log(derived.sayHi()); // Hi! Lee. how are you doing?
```
`super.syaHi`는 `Base.prototype.sayHi`를 가리킨다.  
즉 `super`는 **자신을 참조하고 있는 메서드가 바인딩 되어있는 객체의 프로토타입**을 가리킨다.(`Derived`의 `sayHi` 메서드가 바인딩 되어있는 객체 `derived`의 프로토타입인 `Base.prototype`을 가리킨다.)  

`super`참조의 동작은 `Derived`클래스의 `sayHi`메서드의 `[[HomeObject]]`내부 슬롯을 통해 자신을 바인딩하는 객체를 알수있다.  
자신을 바인딩하는 객체를 알면 프로토타입 체인을 통해 `super`를 참조할 수 있게된다.  

주의할 점은 ES6의 메서드 축약 표현으로 정의된 함수만 `[[HomeObject]]`를 갖는다는 것이다.
```javascript
const obj = {
  // foo는 ES6의 메서드 축약 표현으로 정의한 메서드다. 따라서 [[HomeObject]]를 갖는다.
  foo() {},

  // bar는 ES6의 메서드 축약 표현으로 정의한 메서드가 아니라 일반 함수다.
  // 따라서 [[HomeObject]]를 갖지 않는다.
  bar: function () {}
};
```
`super`참조는 객체 리터럴에서도 할수 있다.(단, ES6의 메서드 축약표현으로 정의된 함수만 가능하다.)

```javascript
const base = {
  name: 'Lee',
  sayHi() {
    return `Hi! ${this.name}`;
  }
};

const derived = {
  __proto__: base,
  // ES6 메서드 축약 표현으로 정의한 메서드다. 따라서 [[HomeObject]]를 갖는다.
  sayHi() {
    return `${super.sayHi()}. how are you doing?`;
  }
};

console.log(derived.sayHi()); // Hi! Lee. how are you doing?
```

- 서브클래스의 정적 메서드 내에서 `super.sayHi`는 수퍼클래스의 정적 메서드 `sayHi`를 가리킨다.

```javascript
// 수퍼클래스
class Base {
  static sayHi() {
    return 'Hi!';
  }
}

// 서브클래스
class Derived extends Base {
  static sayHi() {
    // super.sayHi는 수퍼클래스의 정적 메서드를 가리킨다.
    return `${super.sayHi()} how are you doing?`;
  }
}

console.log(Derived.sayHi()); // Hi! how are you doing?
```

### 8.6 상속 클래스의 인스턴스 생성 과정
상속 관계에 있는 두 클래스가 어떻게 협력하며 인스턴스를 생성하는지 살펴보자.

```javascript
// 수퍼클래스
class Rectangle {
  constructor(width, height) {
    this.width = width;
    this.height = height;
  }

  getArea() {
    return this.width * this.height;
  }

  toString() {
    return `width = ${this.width}, height = ${this.height}`;
  }
}

// 서브클래스
class ColorRectangle extends Rectangle {
  constructor(width, height, color) {
    super(width, height);
    this.color = color;
  }

  // 메서드 오버라이딩
  toString() {
    return super.toString() + `, color = ${this.color}`;
  }
}

const colorRectangle = new ColorRectangle(2, 4, 'red');
console.log(colorRectangle); // ColorRectangle {width: 2, height: 4, color: "red"}

// 상속을 통해 getArea 메서드를 호출
console.log(colorRectangle.getArea()); // 8
// 오버라이딩된 toString 메서드를 호출
console.log(colorRectangle.toString()); // width = 2, height = 4, color = red
```

![image](https://user-images.githubusercontent.com/22947274/146668079-e77a78dd-2532-4b55-8f53-c3b25e37f78c.png)

<br>

**1.서브클래스의** `super`**호출**  
자바스크립트 엔진은 클래스를 평가할 때 수퍼클래스와 서브클래스를 구분하기 위해 "base" 또는 "derive"를 값으로 하는 내부 슬롯`[[ConstructorKind]]`를 갖는다.  

**서브클래스**는 **자신이 직접 인스턴스를 생성하지 않고**, **수퍼클래스**에게 **인스턴스 생성을 위임**한다.  

서브클래스가 `new`연산자와 함께 호출되면 서브클래스 `constructor`내부의 `super`키워드가 함수처럼 호출된다.(수퍼클래스가 평가되어 생성된 함수 객체의 코드가 실행되기 시작한다.)  

만약 서브클래스 `constructor` 내부에 `super`호출이 없으면 에러가 발생한다. 실제 인스턴스를 생성하는 주체는 수퍼클래스이므로 수퍼클래스의 `constructor`를 호출하는 `super`가 호출되지 않으면 인스턴스를 생성할 수 없기 때문이다.  

<br>

**2.수퍼클래스의 인스턴스 생성과** `this`**바인딩**  
수퍼클래스의 `constructor`내부의 코드가 실행되기 이전에 암묵적으로 빈 객체를 생성한다.  
생성된 빈 객체는 `this`에 바인딩된다.  

```javascript
// 수퍼클래스
class Rectangle {
  constructor(width, height) {
    // 암묵적으로 빈 객체, 즉 인스턴스가 생성되고 this에 바인딩된다.
    console.log(this); // ColorRectangle {}
    // new 연산자와 함께 호출된 함수, 즉 new.target은 ColorRectangle이다.
    console.log(new.target); // ColorRectangle
...
```

이때 인스턴스는 수퍼클래스가 생성한 것이다.  
하지만 `new`연산자와 함께 호출된 함수를 가리키는 `new.target`은 서브클래스를 가리킨다.  
따라서 **인스턴스**는 `new.target`이 가리키는 **서브클래스가 생성한 것으로 처리**된다.  

```javascript
// 수퍼클래스
class Rectangle {
  constructor(width, height) {
    // 암묵적으로 빈 객체, 즉 인스턴스가 생성되고 this에 바인딩된다.
    console.log(this); // ColorRectangle {}
    
    // new 연산자와 함께 호출된 함수, 즉 new.target은 ColorRectangle이다.
    console.log(new.target); // ColorRectangle

    // 생성된 인스턴스의 프로토타입으로 ColorRectangle.prototype이 설정된다.
    console.log(Object.getPrototypeOf(this) === ColorRectangle.prototype); // true
    console.log(this instanceof ColorRectangle); // true
    console.log(this instanceof Rectangle); // true
...
```
생성된 인스턴스는 **서브클래스**의 `prototype` **프로퍼티가 가리키는 객체**(`ColorRectangle.prototype`)이다.

<br>

**3.수퍼클래스의 인스턴스 초기화**  
수퍼클래스의 `constructor`가 실행되어 `this`에 바인딩되어 있는 인스턴스를 초기화한다.

```javascript
// 수퍼클래스
class Rectangle {
  constructor(width, height) {
    // 암묵적으로 빈 객체, 즉 인스턴스가 생성되고 this에 바인딩된다.
    console.log(this); // ColorRectangle {}
    // new 연산자와 함께 호출된 함수, 즉 new.target은 ColorRectangle이다.
    console.log(new.target); // ColorRectangle

    // 생성된 인스턴스의 프로토타입으로 ColorRectangle.prototype이 설정된다.
    console.log(Object.getPrototypeOf(this) === ColorRectangle.prototype); // true
    console.log(this instanceof ColorRectangle); // true
    console.log(this instanceof Rectangle); // true

    // 인스턴스 초기화
    this.width = width;
    this.height = height;

    console.log(this); // ColorRectangle {width: 2, height: 4}
  }
...
```

<br>

**4.서브클래스** `constructor`**로의 복귀와** `this`**바인딩**  
`super`의 호출이 종료되고 제어 흐름이 서브클래스 `construcotr`로 돌아온다.  
이때 `super`**가 반환한 인스턴스가** `this`**바인딩된다.**  

서브클래스는 별도의 인스턴스를 생성하지 않고 `super`가 반환한 인스턴스를 그대로 사용한다.  

```javascript
// 서브클래스
class ColorRectangle extends Rectangle {
  constructor(width, height, color) {
    super(width, height);

    // super가 반환한 인스턴스가 this에 바인딩된다.
    console.log(this); // ColorRectangle {width: 2, height: 4}
...
```

<br>

**5.서브클래스의 인스턴스 초기화**  
`super`호출 이후 서브클래스의 `constructor`에 기술되어 있는 인스턴스 초기화가 실행된다.  

<br>

**6.인스턴스 반환**  
클래스의 모든 처리가 끝나면 완성된 인스턴스가 바인딩된 `this`가 암묵적으로 반환된다.
```javascript
// 서브클래스
class ColorRectangle extends Rectangle {
  constructor(width, height, color) {
    super(width, height);

    // super가 반환한 인스턴스가 this에 바인딩된다.
    console.log(this); // ColorRectangle {width: 2, height: 4}

    // 인스턴스 초기화
    this.color = color;

    // 완성된 인스턴스가 바인딩된 this가 암묵적으로 반환된다.
    console.log(this); // ColorRectangle {width: 2, height: 4, color: "red"}
  }
...
```

### 8.7 표준 빌트인 생성자 함수 확장
`extends` 키워드 다음에는 클래스뿐만이 아니라 `[[Construct]]내부 메서드를 갖는 함수 객체로 평가될수 있는 모든 표현식을 사용할 수 있다.  

`String`, `Number`, `Array`같은 표준 빌트인 객체도 `[[Construct]]`내부 메서드를 갖는 생성자 함수이므로 `extends`키워드를 사용하여 확장할 수 있다.
```javascript
// Array 생성자 함수를 상속받아 확장한 MyArray
class MyArray extends Array {
  // 중복된 배열 요소를 제거하고 반환한다: [1, 1, 2, 3] => [1, 2, 3]
  uniq() {
    return this.filter((v, i, self) => self.indexOf(v) === i);
  }

  // 모든 배열 요소의 평균을 구한다: [1, 2, 3] => 2
  average() {
    return this.reduce((pre, cur) => pre + cur, 0) / this.length;
  }
}

const myArray = new MyArray(1, 1, 2, 3);
console.log(myArray); // MyArray(4) [1, 1, 2, 3]

// MyArray.prototype.uniq 호출
console.log(myArray.uniq()); // MyArray(3) [1, 2, 3]
// MyArray.prototype.average 호출
console.log(myArray.average()); // 1.75
```

`Array`생성자 함수를 상속받아 확장한 `MyArray`클래스가 생성한 인스턴스는 `Array.prototype`과 `MyArray.prototype`의 모든 메서드를 사용할 수 있다.  

이때 주의할 것은 `Array.prototype`의 메서드 중에서 `map`, `filter`와 같이 새로운 배열을 반환하는 메서드가 `MyArray`클래스의 인스턴스를 반환한다는 것이다.  

```javascript
console.log(myArray.filter(v => v % 2) instanceof MyArray); // true
```

만약 `MyArray`클래스의 인스턴스를 반환하지 않으면 메서드 체이닝이 불가능하다.(`uniq`, `average`와 같이 `MyArray`에 선언한 메서드들을 사용할 수 없다.)
```javascript
// 메서드 체이닝
// [1, 1, 2, 3] => [ 1, 1, 3 ] => [ 1, 3 ] => 2
console.log(myArray.filter(v => v % 2).uniq().average()); // 2
```

<br>

만약 `MyArray`클래스의 메서드가 `MyArray`클래스가 생성한 인스턴스가 아닌 `Array`가 생성한 인스턴스를 반환하게 하려면 `Symbol.species`를 사용하여 정적 접근자 프로퍼티를 추가한다. 
```javascript
// Array 생성자 함수를 상속받아 확장한 MyArray
class MyArray extends Array {
  // 모든 메서드가 Array 타입의 인스턴스를 반환하도록 한다.
  static get [Symbol.species]() { return Array; }

  // 중복된 배열 요소를 제거하고 반환한다: [1, 1, 2, 3] => [1, 2, 3]
  uniq() {
    return this.filter((v, i, self) => self.indexOf(v) === i);
  }

  // 모든 배열 요소의 평균을 구한다: [1, 2, 3] => 2
  average() {
    return this.reduce((pre, cur) => pre + cur, 0) / this.length;
  }
}

const myArray = new MyArray(1, 1, 2, 3);

console.log(myArray.uniq() instanceof MyArray); // false
console.log(myArray.uniq() instanceof Array); // true

// 메서드 체이닝
// uniq 메서드는 Array 인스턴스를 반환하므로 average 메서드를 호출할 수 없다.
console.log(myArray.uniq().average());
// TypeError: myArray.uniq(...).average is not a function
```


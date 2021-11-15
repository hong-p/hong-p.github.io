---
title: "모던 자바스크립트 딥 다이브 12장 함수"
excerpt: "[javascript] 모던 자바스크립트 Deep Dive 12장 함수"
categories:
 - Javascript
tags:
 - Javascript
toc: true
toc_sticky: true
last_modified_at: 2021-11-15
---

# 함수

## 1.함수란?
함수는 자바스크립트에서 가장 중요한 핵심 개념이다.  
`스코프`, `실행 컨텍스트`, `클로저`, `생성자 함수에 의한 객체 생성`, `메서드`, `this`, `프로토타입`, `모듈화`등이 모두 함수와 깊은 관련이 있다.  

<br>

수학의 함수는 `입력`을 받아 `출력`을 내보내는 일련의 과정을 정의한 것이다.  
프로그래밍 언어의 함수도 수학의 함수와 같은 개념이다.  
`f(x,y) = x + y`를 자바스크립트의 함수로 표현해 보자
```javascript
// f(x, y) = x + y
function add(x, y) {
  return x + y;
}

// f(2, 5) = 7
add(2, 5); // 7
```
프로그래밍 언어의 함수는 **일련의 과정을 문`statement`으로 구현하고 코드 블록으로 감싸서 하나의 실행 단위로 정의한 것이다.**  
함수는 아래 3가지로 이루어져 있다.
- 매개변수 `parameter`
- 인수 `argument`
- 반환값 `return value`

<br>

함수는 함수 정의`function definition`를 통해 생성한다.
```javascript
// 함수 정의
function add(x, y) {
  return x + y;
}
```
함수를 정의만으로 실행되지 않는다.  
인수`parameter`를 매개변수르 ㄹ통해 함수에 전달하면서 함수의 실행을 명시적으로 지시해야 한다.  
이를 함수 호출`function call/invoke`라고 한다.  
```javascript
// 함수 호출
var result = add(2, 5);

// 함수 add에 인수 2, 5를 전달하면서 호출하면 반환값 7을 반환한다.
console.log(result); // 7
```

<br>

## 2.함수를 사용하는 이유

- 코드의 재사용성에 유용
- 유지보수의 편의성을 높임
- 코드의 신뢰성을 높임
- 코드의 가독성을 향상

<br>

## 3.함수 리터럴
자바스크립트의 **함수는 객체 타입의 값**이다.  
따라서 숫자 값을 숫자 리터럴로 생성하고, 객체를 객체 리터럴로 생성하는 것처럼 함수도 함수 리터럴로 생성할 수 있다.  
(함수 리터럴 === 함수 표현식)

```javascript
// 변수에 함수 리터럴을 할당
var f = function add(x, y) {
  return x + y;
};
```

<br>

*※함수 리터럴의 구성*

 - `function`키워드
 - 함수 이름
 - 매개 변수 목록
 - 함수 몸체

구성요소|설명
:---:|:---
함수 이름 | - 함수 이름은 식별자다<br> - 함수이름은 함수 몸체 내에서만 참조할 수 있는 식별자다. <br> - 함수 이름은 생략할 수 있다.(익명함수)
매개 변수 목록 | - 0개 이상의 매개변수를 소괄호로 감싸고 쉼표로 구분 <br> - 각 매개변수에는 함수를 호출할 때 지정한 인수가 순서대로 할당된다. <br> - 매개변수는 함수 몸체 내에서 변수와 동일, 식별자 네이밍 규칙을 준수 해야한다.
함수 몸체 | - 함수가 호출되었을 때 실행될 문들을 하나의 실행 단위로 정의한 코드 블록 <br> - 함수 호출에 의해 실행된다.

<br>

함수 리터럴은 값을 생성하기 위한 표기법이다.  
함수 리터럴은 평가되어 값을 생성하고, 이 값은 객체다.  
즉, **함수는 객체다.**  

<br>

하지만 함수와 일반 객체는 다르다.  
일반 객체는 호출할 수 없지만, 함수는 호출할 수 있다.

<br>

## 4.함수 정의
함수 정의란 함수를 호출하기 이전에 인수를 전달받을 매개변수와 실행할 문들, 그리고 반환할 값을 지정하는 것을 말한다.  
정의된 함수는 평가되어 함수 객체가 된다.  

*※함수를 정의하는 4가지 방법*  

함수 정의 방식 | 예시
---|---
함수 선언문| <code>function add(x,y){<br>return x+y;<br>}</code>
함수 표현식 | <code>var add = function(x,y){<br>return x+y;<br>};</code>
Function 생성자 함수 | `var add = new Function('x','y','x+y');`
화살표 함수(ES6)|`var add = (x,y)=>x+y`

>*※변수 선언과 함수 정의*  
변수는 선언`declaration`한다고 했지만 함수는 정의`definition`한다고 표현한다.  
함수 선언문이 평가되면 식별자가 암묵적으로 생성되고 함수 객체가 할당된다.  

### 4.1 함수 선언문

```javascript
// 함수 선언문
function add(x, y) {
  return x + y;
}

// 함수 참조
// console.dir은 console.log와는 달리 함수 객체의 프로퍼티까지 출력한다.
// 단, Node.js 환경에서는 console.log와 같은 결과가 출력된다.
console.dir(add); // ƒ add(x, y)

// 함수 호출
console.log(add(2, 5)); // 7
```
함수 선언문은 함수 리터럴과 형태가 동일하다.  
함수 리터럴은 함수 이름을 생략할 수 있으나 **함수 선언문은 함수 이름을 생략할 수 없다.**  

<br>

함수 선언문은 표현식이 아닌 **문**이다.  
크롬 개발자 도구의 콘솔에서 함수 선언문을 실행하면 완료값 `undefined`가 출력된다.  
함수 표현식은 표현식이 평가되어 생성된 함수가 출력된다.

![l](https://user-images.githubusercontent.com/80154058/141789502-1d2d85cf-04e9-43e9-bfd7-65c2835bcc7a.png)


<br>

```javascript
// 함수 선언문은 표현식이 아닌 문이므로 변수에 할당할 수 없다.
// 하지만 함수 선언문이 변수에 할당되는 것처럼 보인다.
var add = function add(x, y) {
  return x + y;
};

// 함수 호출
console.log(add(2, 5)); // 7
```
위 예제는 함수 선언문을 변수에 할당하는 것처럼 보이는데, 사실 객체 리터럴 즉, 함수 표현식으로 해석한다.  
자바스크립트 엔진은 코드의 문맥에 따라 해석하는데, 위 예제는 좌변의 변수에 할당 연산자를 통해 우변의 함수 선언문이 피연산자로 사용되면 자바스크립트 엔진은 `{ }`를 객체 리터럴로 해석한다.  

<br>

>*※함수 선언문과 함수 표현식(함수 리터럴) 차이*  
함수 선언문은 함수 이름을 생략할 수 없다.  
자바스크립트 엔진은 함수 선언문을 해석해 함수 이름과 동일한 이름의 식별자를 암묵적으로 생성하고, 거기에 함수 객체를 할당한다.  
<br>
반면 함수 표현식(함수 리터럴)은 "함수 이름은 함수 몸체 내에서만 참조 할수 있는 식별자"라는 특성이 있어, 별도로 변수에 할당받지 않은 이상 호출할수 있는 식별자가 없다.

```javascript
// 기명 함수 리터럴을 단독으로 사용하면 함수 선언문으로 해석된다.
// 함수 선언문에서는 함수 이름을 생략할 수 없다.
function foo() { console.log('foo'); }
foo(); // foo

// 함수 리터럴을 피연산자로 사용하면 함수 선언문이 아니라 함수 리터럴 표현식으로 해석된다.
// 함수 리터럴에서는 함수 이름을 생략할 수 있다.
(function bar() { console.log('bar'); });
bar(); // ReferenceError: bar is not defined

// 아래와 같이 작성해야 호출 가능하다.
bar = (function bar(){ console.log('bar'); });
bar(); // bar
```

<br>

그룹 연산자`()`로 묶은 경우 함수 선언문으로 해석되지 않고 리터럴 표현식으로 해석된다.
```javascript
// 그룹 연산자로 함수를 묶은 경우
(function a(){console.log('a')})() // a

// 논리합 연산자로 단축평가를 사용한 경우
(false||function(){console.log('a')})() // a
false||function(){console.log('a')}() // a
```

함수는 함수 이름을 호출하는 것이 아니라 함수 객체를 가리키는 식별자로 호출한다.
```javascript
var add = function add(x,y){
  return x+y;
};
console.log(add(2,5)); // 7
```
이 예제에서 `add(2,5)`는 `var`키워드 다음에 선언한 `add`식별자로 호출한 것이다.  
`function`키워드 뒤에 있는 함수 이름이 호출된 것이 아니다.  
![image](https://user-images.githubusercontent.com/80154058/141794626-50a9a400-136f-424a-b2ad-85ec26e89fcf.png)

함수이름 `addFunc`를 호출하면 `ReferenceError`가 난다.  
`add`식별자로 호출해야 된다.

### 4.2 함수 표현식(=함수 리터럴)
자바스크립트의 함수는 일급 객체이다.  
일급 객체는 값처럼 변수에 할당할 수 있고, 프로퍼티 값이 될수도 있으며, 배열의 요소도 될 수 있다.  
이러한 함수의 정의 방식을 `함수 표현식 function expressiont`이라고 한다.
```javascript
// 함수 표현식
var add = function (x, y) {
  return x + y;
};

console.log(add(2, 5)); // 7
```

<br>

함수 리터럴의 함수 이름은 생략할 수 있다. 이러한 함수를 익명 함수라 한다.  
함수 표현식의 함수 리터럴은 함수 이름을 생략하는 것이 일반적이다.

```javascript
// 기명 함수 표현식
var add = function foo (x, y) {
  return x + y;
};

// 함수 객체를 가리키는 식별자로 호출
console.log(add(2, 5)); // 7

// 함수 이름으로 호출하면 ReferenceError가 발생한다.
// 함수 이름은 함수 몸체 내부에서만 유효한 식별자다.
console.log(foo(2, 5)); // ReferenceError: foo is not defined
```

### 4.3 함수 생성 시점과 함수 호이스팅

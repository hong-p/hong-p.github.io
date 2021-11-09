---
title: "[javascript] 모던 자바스크립트 Deep Dive ch04 변수"
excerpt: "모던 자바스크립트 딥다이브 ch04 변수"
categories:
 - Javascript
tags:
 - Javascript
toc: true
toc_sticky: true
last_modified_at: 2021-11-09
---

# 변수(variable)

## 변수란 무엇인가?
변수는 프로그래밍 언어에서 데이터를 관리하기 위한 핵심 개념이다.  
변수는 하나의 값을 저장하기 위해 확보한 메모리 공간 자체 또는 그 메모리 공간을 식별하기 위해 붙인 이름을 말한다.  
메모리에 저장된 값의 위치를 가리키는 상징적인 이름이다.  
```javascript
var result = 10 + 20;
```
위 코드는 10과 20을 메모리공간에서 가져와 + 연산을 하고 30의 값을 다시 메모리에 넣는데,  
result라는 변수에 30이라는 값이 넣은곳의 주소를 카리키고 있다.  
변수명을 **식별자**라고 부른다. 위 코드에서 **result**가 **변수명**이자 **식별자**이다. 
  
## 변수 선언
```javascript
var score; // 변수 선언(변수 선언문)
```
var는 **키워드**로 자바스크립트 엔진이 var키워드를 만나면 뒤에오는 변수 이름으로 새로운 변수를 선언한다.  
>키워드란 자바스크립트 엔진이 수행할 동작을 미리 규정한 일종의 명령

### 변수 선언과 초기화 단계
```javascript
var score // 변수 선언
score = 10; // 값의 할당
```
위와 같이 작성한 경우 
1. 변수 선언 단계에서 변수 이름 score를 등록한다.
2. 초기화 단계에서 undefined로 암묵적인 초기화가 자동으로 수행된다.
3. 런타임 시점에 undefined로 초기화 되어있는 메모리에 값 10을 할당한다.

### 호이스팅
```javascript
console.log(score); // undefined
var score; // 변수 선언문
```
위와 같은 코드는 선언문이 호출문보다 아래에 있음에도 불구하고
참조에러가 발생되지 않고 undefined가 출력된다.  
그 이유는 변수 선언이 소스코드가 한 줄씩 순차적으로 실행되는 시점, 즉 런타임이 아니라 그 이전 단계에서 먼저 실행되기 때문이다.  
  
자바스크립트 엔진은 변수 선언을 포함한 모든 선언문(변수, 함수 등)을 소스코드에서 찾아내 먼저 실행한다. 그리고 소스코드의 평가과정이 모두 끝나면 비로소 선언문을 제외한 소스코드를 한 줄씩 순차적으로 실행한다.  

## 식별자 네이밍 규칙
- 식별자는 특수문자를 제외한 문자, 숫자, 언더스코어(_), 달러기호($)를 포함할 수 있다.
- 단, 식별자는 특수문자를 제외한 문자, 숫자, 언더스코어(_), 달러기호($)로 시작해야 한다.
- 숫자로 시작하는 것은 허용하지 않는다.
- 예약어는 식별자로 사용할 수 없다.
  
다음과 같은 식별자는 변수 이름으로 사용할 수 있다.
```javascript
var person, $elem, _name, first_name, vla1;
```
ES5부터 식별자를 유니코드 문자로도 만들수 있어, 알파벳 외에도 한글, 일본어 등을 사용할 수 있다. 하지만 권장하지 않는다.
```javascript
var 이름;
```
  
명명규칙에 위배되는 식별자
```javascript
var first-name;
var 1st;
var this;
```

식별자는 대소문자를 구분한다. 아래 3개 변수는 모두 별개이다.
```javascript
var firstname;
var firstName;
var FIRSTNAME;
```

식별자는 존재 목적을 쉽게 이해할 수 있도록 의미를 명확히 표현해야 한다.
좋은 변수 이름은 가독성을 높인다.
```javascript
var x = 3; // x의 의미를 할수 없다.
var score = 100; // 점수를 의미한다는 의도가 명확하다.
```
```javascript
// 경과 시간. 단위는 날짜다
var d;                 // NG
var elapsedTimeInDays; // OK
```

변수나 함수의 이름에는 카멜 케이스를 사용하고
생성자 함수, 클래스의 이름에는 파스칼 케이스를 사용한다.
```javascript
// 카멜 케이스 (camelCase)
var firstName;

// 스네이크 케이스 (snake_case)
var first_name;

// 파스칼 케이스 (PascalCase)
var FirstName;

// 헝가리언 케이스 (typeHungarianCase)
var strFirstName; // type + identifier
var $elem = document.getElementById('myId'); // DOM 노드
var observable$ = fromEvent(document, 'click'); // RxJS 옵저버블
```
  
  
---
키워드
> - break 
> - case 
> - catch 
> - continue 
> - debugger* 
> - default 
> - delete 
> - do 
> - else 
> - finally 
> - for 
> - function 
> - if 
> - in 
> - instanceof 
> - new 
> - return 
> - switch 
> - this 
> - throw 
> - try 
> - typeof 
> - var 
> - void 
> - while 
> - with
  

---
예약어
> - abstract
> - arguments
> - boolean
> - break
> - byte
> - case
> - catch
> - char
> - class*
> - const
> - continue
> - debugger
> - default
> - delete
> - do
> - double
> - else
> - enum*
> - eval
> - export*
> - extends*
> - false
> - final
> - finally
> - float
> - for
> - function
> - goto
> - if
> - implements
> - import*
> - in
> - instanceof
> - int
> - interface
> - let
> - long
> - native
> - new
> - null
> - package
> - private
> - protected
> - public
> - return
> - short
> - static
> - super*
> - switch
> - synchronized
> - this
> - throw
> - throws
> - transient
> - true
> - try
> - typeof
> - var
> - void
> - volatile
> - while
> - with
> - yield


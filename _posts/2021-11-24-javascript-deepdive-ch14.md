---
title: "모던 자바스크립트 딥 다이브 14장 전역 변수의 문제점"
excerpt: "[javascript] 모던 자바스크립트 Deep Dive 14장 전역 변수의 문제점"
categories:
 - Javascript
tags:
 - Javascript
toc: true
toc_sticky: true
last_modified_at: 2021-11-24
---

# 14장 전역 변수의 문제점

## 1.변수의 생명 주기

### 1.1 지역 변수의 생명 주기
변수는 선언에 의해 성성되고 할당을 통해 값을 갖는다. 그리고 언젠가 소멸한다. 즉, 변수는 생성되고 소멸되는 생명주기`life cycle`을 갖는다.  
만약 생명주기가 없다면 프로그램이 종료되지 않는한 영원히 메모리 공간을 점유하게 된다.

전역 변수 선언문은 어디에 있든 상관없이 가장 먼저 실행된다.  
반면 지역 변수(함수 내부의 변수)는 함수가 호출되면 다른 문들이 실행되기 이전에 선언문들이 실행되면서 변수가 생성된다. 그리고 함수가 종료되면 변수도 소멸되어 생명주기가 종료된다.  
즉, 지역 변수의 생명 주기는 함수의 생명 주기와 일치한다.  

보통 지역 변수의 생명 주기와 함수의 생명 주기는 일치하지만, **지역변수가 함수보다 오래 생존하는 경우도 있다.**  
아래와 같은 경우이다(클로저)
```javascript
function foo(){
    var x = 10;
    function bar(){
        console.log(x);
    }
    return bar;
}
var bar = foo(); // 이미 foo함수는 호출되어 bar함수를 리턴하고 끝났지만
                 // 아래에서 bar 함수를 리턴받아 호출해도 x가 잘 참조되어 10이 출력된다.
bar(); // 10
```

### 1.2 전역 변수의 생명 주기
함수와 달리 전역 코드는 명시적인 호출없이 실행된다. 즉 함수 호출과 같이 전역 코드를 실행하는 진입점이 없고 코드가 로드되자마마 곧바로 해석되고 실행된다.  
따라서 전역 변수의 생명 주기는 전역 코드의 마지막 문이 실행되어 더 이상 실행할 문이 없을 때 종료되고 전역 변수도 소멸된다.  

`var`키워드로 선언한 전역 변수는 전역 객체의 프로퍼티가 된다. 이는 전역 변수의 생명 주기는 전역 객체의 생명 주기와 일치한다는 것이다.  

브라우저 환경에서는 `window`가 전역 객체이므로 `var`키워드로 선언한 전역 변수는 `window`객체의 프로퍼티이다. 브라우저 환경에서는 `var`키워드로 선언한 전역 변수는 웹페이지를 닫을 때까지 유효하다.  

<br>

## 2.전역 변수의 문제점

**암묵적 결합**
전역 변수는 코드 어디서든 참조하고 할당할 수 있는 변수이기 때문에 모든 코드가 참조하고 변경할 수 있는 암묵적 결합`implicit coupling`을 허용하는 것이다.  
변수의 유효 범위가 클수록 코드의 가독성이 나빠지고 의도치 않게 상태가 변경될 수 있는 위험성도 높아진다.  

**긴 생명 주기**
전역 변수는 생명 주기가 길다. 따라서 메모리 리소스도 오랫동안 점유한다.  
또한 `var`키워드는 변수의 중복 선언을 허용하기 때문에 변수를 중복으로 선언할 수 있고, 이로 인해 의도치 않은 재할당이 이뤄질수 있다.  

```javascript
var x = 1;

// ...

// 변수의 중복 선언. 기존 변수에 값을 재할당한다.
var x = 100;
console.log(x); // 100
```

**스코프 체인 상에서 종점에 존재**
전역 변수는 스코프 체인 상에서 가장 끝점에 위치하기 때문에, 변수를 검색할때 가장 마지막에 검색된다. 즉, **변수 검색 속도가 가장 느리다.** 검색 속도의 차이가 크진 않지만 차이는 분명히 있다.  

**네임스페이스 오염**
자바스크립트의 가장 큰 문제점 중 하나는 **파일이 분리되어 있어도 하나의 전역 스코프를 공유한다**는 것이다.  
다른 파일 내에서 동일한 이름으로 전역 변수나 전역 함수가 존재하는 경우 예상치 못한 결과를 가져올 수 있다.  
이는 뒤에 나오는 모듈 패턴이나 ES6 모듈을 활용하여 해결할 수 있다.

## 3.전역 변수의 사용을 억제하는 방법
전역 변수의 무분별한 사용은 많은 위험을 초래할 수 있다.  
전역 변수의 사용을 억제하는 방법을 알아보자.  

### 3.1 즉시 실행 함수
**모든 코드를 즉시 실행 함수로 감싸면 모든 변수는 즉시 실행 함수의 지역 변수가 된다.**
```javascript
(function () {
  var foo = 10; // 즉시 실행 함수의 지역 변수
  // ...
}());

console.log(foo); // ReferenceError: foo is not defined
// foo는 지역 변수이기 때문에 바깥에서 참조할 수 없음
```

### 3.2 네임스페이스 객체
전역 네임스페이스 객체를 만들어 프로퍼티로 관리하는 방법이다. 어차피 전역 객체이기 때문에 그닥 좋은 방법은 아니다.
```javascript
var MYAPP = {}; // 전역 네임스페이스 객체

MYAPP.name = 'Lee';

console.log(MYAPP.name); // Lee
```

```javascript
var MYAPP = {}; // 전역 네임스페이스 객체

MYAPP.person = {
  name: 'Lee',
  address: 'Seoul'
};

console.log(MYAPP.person.name); // Lee
```

### 3.3 모듈 패턴
클로저를 활용해 클래스를 모방해서 만든 패턴이다.  
전역 변수의 억제와 캡슐화까지 구현할 수 있다.

```javascript
var Counter = (function () {
  // private 변수
  var num = 0;

  // 외부로 공개할 데이터나 메서드를 프로퍼티로 추가한 객체를 반환한다.
  return {
    increase() {
      return ++num;
    },
    decrease() {
      return --num;
    },
    getNum() {
      return num;
    }
  };
}());

// private 변수는 외부로 노출되지 않는다.
console.log(Counter.num); // undefined
console.log(Counter.getNum());   // 0
console.log(Counter.increase()); // 1
console.log(Counter.getNum());   // 1
console.log(Counter.increase()); // 2
console.log(Counter.getNum());   // 2
console.log(Counter.decrease()); // 1
console.log(Counter.decrease()); // 0
```

### 3.4 ES6 모듈
ES6 모듈을 사용하면 전역 변수를 사용할 수 없다. **ES6 모듈은 파일 자체의 독자적인 모듈 스코프를 제공한다.**  
모듈 내에서 `var`키워드로 선언하더라도 전역 변수가 아니며 `window`객체의 프로퍼티도 아니다.  
`script`태그에 `type="module"`어트리뷰트를 추가하면 로드된 자바스크립트 파일은 모듈로 동작한다.

```javascript
<script type="module" src="lib.mjs"></script>
<script type="module" src="app.mjs"></script>
```

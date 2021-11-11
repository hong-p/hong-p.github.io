---
title: "[javascript] 모던 자바스크립트 Deep Dive ch07 연산자"
excerpt: "모던 자바스크립트 딥다이브 ch07 연산자"
categories:
 - Javascript
tags:
 - Javascript
toc: true
toc_sticky: true
last_modified_at: 2021-11-10
---

# 연산자
연산자`operator`는 하나 이상의 표현식을 대상으로 산술, 할당, 비교, 논리, 타입, 지수 연산 등을 수행해 하나의 값을 만든다.  
피연산자는`operand` 값으로 평가될 수 있는 표현식 이어야 한다.  
그리고 피연산자와 연산자의 조합으로 이뤄진 연산자 표현식도 값으로 평가될 수 있는 표현식이다.
```javascript
// 산술 연산자
5 * 4 // -> 20
// 문자열 연결 연산자
'My name is ' + 'Lee' // -> 'My name is Lee'
// 할당 연산자
color = 'red' // -> 'red'
// 비교 연산자
3 > 5 // -> false
// 논리 연산자
true && false // -> false
// 타입 연산자
typeof 'Hi' // -> string
```

<br/>

## 산술 연산자
### 이항 산술 연산자
이항`binary`산술 연산자는 2개의 피연산자를 산술 연산하여 값을 만든다.  
모든 이항 산술 연산자는 피연산자의 값을 변경하는 부수효과`side effect`가 없다.
이항 산술 연산자 | 의미 | 부수 효과
:--:|:--:|:--:
`+` | 덧셈 | X
`-` | 뺄셈 | X
`*` | 곱셈 | X
`/` | 나눗셈 | X
`%` | 나머지 | X
```javascript
5 + 2; // -> 7
5 - 2; // -> 3
5 * 2; // -> 10
5 / 2; // -> 2.5
5 % 2; // -> 1
```

### 단항 산술 연산자
단항`unary` 산술 연산자는 1개의 피연산자를 산술 연산하여 숫자 값을 만든다.
단항 산술 연산자 | 의미 | 부수 효과
:--:|:--:|:--:
`++` | 증가 | O
`--` | 감소 | O
`+` | 어떠한 효과도 없다. 음수를 양수로 반전하지도 않는다. | X
`-` | 양수를 음수로, 음수를 양수로 반전한 값을 반환한다. | X
이항 산술 연산자와 다르게 증가/감소(`++`/`--`) 연산자는 피연산자의 값을 변경하는 부수효과가 있다.
```javascript
var x = 1;

// ++ 연산자는 피연산자의 값을 변경하는 암묵적 할당이 이뤄진다.
x++; // x = x + 1;
console.log(x); // 2

// -- 연산자는 피연산자의 값을 변경하는 암묵적 할당이 이뤄진다.
x--; // x = x - 1;
console.log(x); // 1
```
<br/>

증가/감소 연산자가 피연산자의 앞에 있느냐 뒤에 있느냐에 따라 연산을 수행하는 시점이 다르다
```javascript
var x = 5, result;

// 선할당 후증가(postfix increment operator)
result = x++;
console.log(result, x); // 5 6

// 선증가 후할당(prefix increment operator)
result = ++x;
console.log(result, x); // 7 7

// 선할당 후감소(postfix decrement operator)
result = x--;
console.log(result, x); // 7 6

// 선감소 후할당 (prefix decrement operator)
result = --x;
console.log(result, x); // 5 5
```
숫자 타입에는 `+`단항 연산자를 사용하는 경우 아무런 효과가 없다.
```javascript
// 아무런 효과가 없다.
+10;    // -> 10
+(-10); // -> -10
```
<br/>

하지만 숫자 타입이 아닌 피연산자에 `+`연산을 사용하면 숫자 타입으로 변환된 값을 반환한다.(부수효과는 없음)
```javascript
var x  = '1';

// 문자열을 숫자로 타입 변환한다.
console.log(+x); // 1
// 부수 효과는 없다.
console.log(x);  // "1"

// 불리언 값을 숫자로 타입 변환한다.
x = true;
console.log(+x); // 1
// 부수 효과는 없다.
console.log(x);  // true

// 불리언 값을 숫자로 타입 변환한다.
x = false;
console.log(+x); // 0
// 부수 효과는 없다.
console.log(x);  // false

// 문자열을 숫자로 타입 변환할 수 없으므로 NaN을 반환한다.
x = 'Hello';
console.log(+x); // NaN
// 부수 효과는 없다.
console.log(x);  // "Hello"
```
<br/>

`-`단항 연산자는 피연산자의 부호를 반전한 값을 반환한다. `+`단항 연산자와 같이 숫자 타입이 아닌 피연산자에 사용하면 숫자 타입으로 변환하여 반환한다.
```javascript
// 부호를 반전한다.
-(-10); // -> 10

// 문자열을 숫자로 타입 변환한다.
-'10'; // -> -10

// 불리언 값을 숫자로 타입 변환한다.
-true; // -> -1

// 문자열은 숫자로 타입 변환할 수 없으므로 NaN을 반환한다.
-'Hello'; // -> NaN
```
<br/>

### 문자열 연결 연산자
`+`연산자는 피연산자 중 하나 이상이 문자열인 경우 문자열 연결 연산자로 동작한다.

---
title: "모던 자바스크립트 딥 다이브 ch08 제어문"
excerpt: "[javascript] 모던 자바스크립트 Deep Dive ch08 제어문"
categories:
 - Javascript
tags:
 - Javascript
toc: true
toc_sticky: true
last_modified_at: 2021-11-10
---

# 제어문
제어문(`control flow statement`)은 조건에 따라 코드 블록을 실행(조건문)하거나 반복실행(반복문)할 때 사용한다. 일반적인 코드는 위에서 아래로 순차적으로 실행하지만, 제어문을 사용하면 코드의 실행 흐름을 인위적으로 제어할 수 있다.

## 1.블록문
블록문(`block statement`)은 0개 이상의 문을 중괄호로 묶은 것으로, 코드 블록 또는 블록이라고 부르기도 한다. 블록문은 하나의 실행 단위로 취급하며, 단독으로 사용하기도 하나 일반적으로 제어문이나 함수를 정의할 때 사용 한다.

**문의 끝에는 세미콜론을 붙이는 것이 일반적이다.** 하지만 블록문은 언제나 문의 종료를 의미하는 자체 종결성을 갖기 때문에 **블록문의 끝에는 세미콜론을 붙이지 않는다.**

```javascript
// 블록문
{
  var foo = 10;
}

// 제어문
var x = 1;
if (x < 10) {
  x++;
}

// 함수 선언문
function sum(a, b) {
  return a + b;
}
```
<br>

## 2.조건문
조건문(`conditioonal statement`)은 주어진 조건식(`condition expression`)의 평가결과에 따라 코드블록(블록문의 실행을 결정한다. 조건식은 불리언 값으로 평가될수 있는 표현식이다.

### 2.1 if...else문
```javascript
var num = 2;
var kind;

// if 문
if (num > 0) {
  kind = '양수'; // 음수를 구별할 수 없다
}
console.log(kind); // 양수

// if...else 문
if (num > 0) {
  kind = '양수';
} else {
  kind = '음수'; // 0은 음수가 아니다.
}
console.log(kind); // 양수

// if...else if 문
if (num > 0) {
  kind = '양수';
} else if (num < 0) {
  kind = '음수';
} else {
  kind = '영';
}
console.log(kind); // 양수
```
`if...else`문은 [7장 삼항 조건 연산자](https://hong-p.github.io/javascript/javascript-deepdive-ch07/#%EC%82%BC%ED%95%AD-%EC%A1%B0%EA%B1%B4-%EC%97%B0%EC%82%B0%EC%9E%90)에서 본 삼항 조건 연산자로 바꿔 쓸수 있다.

<br>

### 2.2 switch문
`switch`문은 주어진 표현식을 평가하여 그 값과 일치하는 표현식을 갖는 `case`문으로 실행 흐름을 옮긴다.
`switch`문의 표현식과 일치하는 `case`문이 없다면 실행 순서는 `default`문으로 이동한다.
```javascript
// 월을 영어로 변환한다. (11 → 'November')
var month = 11;
var monthName;

switch (month) {
  case 1: monthName = 'January';
  case 2: monthName = 'February';
  case 3: monthName = 'March';
  case 4: monthName = 'April';
  case 5: monthName = 'May';
  case 6: monthName = 'June';
  case 7: monthName = 'July';
  case 8: monthName = 'August';
  case 9: monthName = 'September';
  case 10: monthName = 'October';
  case 11: monthName = 'November';
  case 12: monthName = 'December';
  default: monthName = 'Invalid month';
}

console.log(monthName); // Invalid month
```
위 예제는 `November`가 출력되지 않고 `Invalid month`가 출력된다 이는 평가 결과와 일치하는 `case`문으로 실행 흐름이 이동하여 문을 실행한 것은 맞지만 폴스루(`fall through`)가 발생되어 이후의 모든 `case`문을 실행하고 `default`문까지 실행한다. 그래서 마지막 `default`에서 `Invalid month`가 할당되어 출력된 것이다.  
이러한 문제는 `break`문을 사용하면 해결된다.
```javascript
// 월을 영어로 변환한다. (11 → 'November')
var month = 11;
var monthName;

switch (month) {
  case 1: monthName = 'January'; break;
  case 2: monthName = 'February'; break;
  case 3: monthName = 'March'; break;
  case 4: monthName = 'April'; break;
  case 5: monthName = 'May'; break;
  case 6: monthName = 'June'; break;
  case 7: monthName = 'July'; break;
  case 8: monthName = 'August'; break;
  case 9: monthName = 'September'; break;
  case 10: monthName = 'October'; break;
  case 11: monthName = 'November'; break;
  case 12: monthName = 'December'; break;
  default: monthName = 'Invalid month'; break;
}

console.log(monthName); // November
```
일부러 폴스루(`fall through`)를 활용하는 경우도 있다.
```javascript
var year = 2000; // 2000년은 윤년으로 2월이 29일이다.
var month = 2;
var days = 0;

switch (month) {
  case 1: case 3: case 5: case 7: case 8: case 10: case 12:
    days = 31;
    break;
  case 4: case 6: case 9: case 11:
    days = 30;
    break;
  case 2:
    // 윤년 계산 알고리즘
    // 1. 연도가 4로 나누어떨어지는 해(2000, 2004, 2008, 2012, 2016, 2020...)는 윤년이다.
    // 2. 연도가 4로 나누어떨어지더라도 연도가 100으로 나누어떨어지는 해(2000, 2100, 2200...)는 평년이다.
    // 3. 연도가 400으로 나누어떨어지는 해(2000, 2400, 2800...)는 윤년이다.
    days = ((year % 4 === 0 && year % 100 !== 0) || (year % 400 === 0)) ? 29 : 28;
    break;
  default:
    console.log('Invalid month');
}

console.log(days); // 29
```

<br>

## 3.반복문
반복문(`loop statement`)은 조건식의 평가 결과가 참인 경우 코드 블록을 실행한다. 그 후 조건식을 다시 평가하여 여전히 참인 경우 코드 블록을 다시 실행한다. 거짓일 때까지 반복한다.

### 3.1 for문
```javascript
for(변수 선언문 또는 할당문; 조건식; 증감식){
    조건식이 참인 경우 반복 실행될 문;
}

for (var i = 0; i < 2; i++) {
  console.log(i);
}
```

<br>

### 3.2 while문
`while`문은 주어진 조건식의 평가 결과가 참이면 코드 블록을 계속 해서 반복 실행한다. `for`문은 반복 횟수가 명확할 때 주로 사용하고 `while`문은 반복횟수가 불명확할 때 주로 사용한다.

```javascript
var count = 0;

// count가 3보다 작을 때까지 코드 블록을 계속 반복 실행한다.
while (count < 3) {
  console.log(count); // 0 1 2
  count++;
}
```
```javascript
var count = 0;

// 무한루프
while (true) {
  console.log(count);
  count++;
  // count가 3이면 코드 블록을 탈출한다.
  if (count === 3) break;
} // 0 1 2
```

### 3.3 do...while문
`do...while`문은 코드 블록을 먼저 실행하고 조건식을 평가한다. 코드 블록은 무조건 한 번 이상 실행된다.
```javascript
var count = 0;

// count가 3보다 작을 때까지 코드 블록을 계속 반복 실행한다.
do {
  console.log(count);
  count++;
} while (count < 3); // 0 1 2
```

<br>

## 4.break문
`switch`문과 `while`문에서 보았듯이 `break`문을 사용하면 코드 블록을 탈출 한다. 정확히 말하자면 코드 블록을 탈출하는 것이 아니라 레이블 문, 반복문 또는 `switch`문의 코드 블록을 탈출한다.
```javascript
if (true) {
  break; // Uncaught SyntaxError: Illegal break statement
}
```
레이블 문(`label statement`)이란 식별자가 붙은 문을 말한다.
```javascript
// foo라는 레이블 식별자가 붙은 레이블 문
foo: console.log('foo');
```
```javascript
// foo라는 식별자가 붙은 레이블 블록문
foo: {
  console.log(1);
  break foo; // foo 레이블 블록문을 탈출한다.
  console.log(2);
}

console.log('Done!');
```
보통 이중 포문을 탈출하기 위해 종종 사용된다.
```javascript
// outer라는 식별자가 붙은 레이블 for 문
outer: for (var i = 0; i < 3; i++) {
  for (var j = 0; j < 3; j++) {
    // i + j === 3이면 outer라는 식별자가 붙은 레이블 for 문을 탈출한다.
    if (i + j === 3) break outer;
    console.log(`inner [${i}, ${j}]`);
  }
}

console.log('Done!');
```
레이블 문의 사용은 `for`문을 탈출할때 많이 사용되지만 그 밖의 경우에는 일반적으로 권장하지 않는다. 레이블 문을 사용하면 프로그램의 흐름이 복잡해져 가독성이 나빠지고 오류를 발생시킬 가능성이 높아지기 때문이다.

## 5.continue문
`continue`문은 반복문의 코드 블록 실행을 현 지점에서 중단하고 반복문의 증감식으로 실행 흐름을 이동시킨다. `break`문 처럼 반복문을 탈출하지는 않는다.
```javascript
var string = 'Hello World.';
var search = 'l';
var count = 0;

// 문자열은 유사배열이므로 for 문으로 순회할 수 있다.
for (var i = 0; i < string.length; i++) {
  // 'l'이 아니면 현 지점에서 실행을 중단하고 반복문의 증감식으로 이동한다.
  if (string[i] !== search) continue;
  count++; // continue 문이 실행되면 이 문은 실행되지 않는다.
}

console.log(count); // 3

// 참고로 String.prototype.match 메서드를 사용해도 같은 동작을 한다.
const regexp = new RegExp(search, 'g');
console.log(string.match(regexp).length); // 3
```

```javascript
// continue 문을 사용하지 않으면 if 문 내에 코드를 작성해야 한다.
for (var i = 0; i < string.length; i++) {
  // 'l'이면 카운트를 증가시킨다.
  if (string[i] === search) {
    count++;
    // code
    // code
    // code
  }
}

// continue 문을 사용하면 if 문 밖에 코드를 작성할 수 있다.
for (var i = 0; i < string.length; i++) {
  // 'l'이 아니면 카운트를 증가시키지 않는다.
  if (string[i] !== search) continue;

  count++;
  // code
  // code
  // code
}
```

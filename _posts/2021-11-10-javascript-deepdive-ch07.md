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

## 1.산술 연산자
### 1.1.이항 산술 연산자
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

### 1.2.단항 산술 연산자
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
<br/>

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

### 1.3.문자열 연결 연산자
`+`연산자는 피연산자 중 하나 이상이 문자열인 경우 문자열 연결 연산자로 동작한다.
```javascript
// 문자열 연결 연산자
'1' + 2; // -> '12'
1 + '2'; // -> '12'

// 산술 연산자
1 + 2; // -> 3

// true는 1로 타입 변환된다.
1 + true; // -> 2

// false는 0으로 타입 변환된다.
1 + false; // -> 1

// null은 0으로 타입 변환된다.
1 + null; // -> 1

// undefined는 숫자로 타입 변환되지 않는다.
+undefined;    // -> NaN
1 + undefined; // -> NaN


// 반대로 문자열앞에 +를 붙이면 숫자 타입으로 변환된다.
2+ +'1'; // 3
```
위 예제에서 `1 + true`를 연산하면 자바스크립트 엔진은 암묵적으로 `true`를 숫자 타입인 1로 타입을 강제 변환 후 연산을 수행한다.
<br/>

## 2.할당 연산자
할당 연산자`assignment operator`는 우항에 있는 피연산자의 평가 결과를 좌항에 있는 변수에 할당한다. 좌항의 변수에 값을 할당 하므로 부수효과가 있다.

단항 산술 연산자 | 예 | 동일 표현 | 부수 효과 
:--:|:--:|:--:|:--:
`=` | x = 5 | x = 5 | O
`+=` | x += 5 | x = x + 5 | O
`-=` | x -= 5 | x = x - 5 | O
`*=` | x *= 5 | x = x * 5 | O
`/=` | x /= 5 | x = x / 5 | O
`%=` | x %= 5 | x = x % 5 | O
```javascript
var x;

x = 10;
console.log(x); // 10

x += 5; // x = x + 5;
console.log(x); // 15

x -= 5; // x = x - 5;
console.log(x); // 10

x *= 5; // x = x * 5;
console.log(x); // 50

x /= 5; // x = x / 5;
console.log(x); // 10

x %= 5; // x = x % 5;
console.log(x); // 0

var str = 'My name is ';

// 문자열 연결 연산자
str += 'Lee'; // str = str + 'Lee';
console.log(str); // 'My name is Lee'
```
<br/>

할당문은 표현식 일까 아닐까?
할당문은 값으로 평가되는 표현식인 문으로서 할당된 값으로 평가된다.
```javascript
var x;

// 할당문은 표현식인 문이다.
console.log(x = 10); // 10
```
```javascript
var a, b, c;

// 연쇄 할당. 오른쪽에서 왼쪽으로 진행.
// ① c = 0 : 0으로 평가된다
// ② b = 0 : 0으로 평가된다
// ③ a = 0 : 0으로 평가된다
a = b = c = 0;

console.log(a, b, c); // 0 0 0
```
위 예제와 같이 연쇄 할당이 가능하다.

<br/>

## 3.비교 연산자
좌항과 우항의 피연산자를 비교한 다음 그 결과를 불리언 값으로 반환한다.

### 3.1.동등/일치 비교 연산자
비교 연산자|의미|사례|설명|부수효과
:---:|:---:|:---:|:---:|:---:
`==`|동등비교| `x == y`|` x와 y의 값이 같음|X
`===`|일치비교|`x === y`| x와 y의 값과 타입이 같음|X
`!=`|부동등 비교| `x != y`|x와 y의 값이 다름|X
`!==`|불일치 비교| `x !== y`|x와 y의 값과 타입이 다름|X

비교연산자에서도 **암묵적 타입변환**이 일어난다.
동등비교(`==`) 연산자는 좌항과 우항의 피연산자를 비교할 때 먼저 암묵적 타입 변환을 통해 타입을 일치시킨 후 같은 값인지 비교한다.
```javascript
// 동등 비교
5 == 5; // -> true

// 타입은 다르지만 암묵적 타입 변환을 통해 타입을 일치시키면 동등하다.
5 == '5'; // -> true
```

다음 예제는 결과예측이 어려워 실수하기 쉬운 안티패턴이므로 참고만 하자.
```javascript
// 동등 비교. 결과를 예측하기 어렵다.
'0' == ''; // -> false
0 == '';   // -> true
0 == '0';  // -> true
false == 'false';   // -> false
false == '0';       // -> true
false == null;      // -> false
false == undefined; // -> false
```

동등비교(`==`)는 예측하기 어려운 결과를 만들어 내니 일치비교(`===`)를 사용하는 것을 권장한다.  
일치비교(`===`) 연산자는 좌항과 우항의 피연산자가 타입도 같고 값도 같은 경우에 한하여 true를 반환한다.  
```javascript
// 일치 비교
5 === 5; // -> true

// 암묵적 타입 변환을 하지 않고 값을 비교한다.
// 즉, 값과 타입이 모두 같은 경우만 true를 반환한다.
5 === '5'; // -> false
```
암묵적 타입 변환을 하지 않고 값을 비교한다.
>[일치비교 연산자의 비교 알고리즘](https://262.ecma-international.org/5.1/#sec-11.9.6)

<br/>
아래 예제는 자바스크립트 버그이다.

```javascript
// NaN은 자신과 일치하지 않는 유일한 값이다.
NaN === NaN; // -> false
```
그래서 `isNaN()`함수,`Number.isNaN()`메서드를 이용해 비교하는 것이 좋다.

```javascript
// isNaN 함수는 지정한 값이 NaN인지 확인하고 그 결과를 불리언 값으로 반환한다.
isNaN(NaN); // -> true
isNaN(10);  // -> false
isNaN(1 + undefined); // -> true

// 그냥 문자열인데도 isNaN을 하면 true가 나온다(이상함)
isNaN('d') // -> true
isNaN('1') // -> false

// 그래서 더 정확한 Number.isNaN을 사용하는 것이 좋다.
Number.isNaN(+'1') // -> false
Number.isNaN(+'dd') // -> true
```
<br/>

숫자 `0`을 비교하는 것도 버그이다.
```javascript
// 양의 0과 음의 0의 비교. 일치 비교/동등 비교 모두 결과는 true이다.
0 === -0; // -> true
0 == -0;  // -> true
```
<br/>

그래서 가장 정확한것은 `object.is()`메서드를 활용하는 것이다.
```javascript
-0 === +0;         // -> true
Object.is(-0, +0); // -> false

NaN === NaN;         // -> false
Object.is(NaN, NaN); // -> true
```

<br/>

부동등 비교 연산자(`!=`)와 불일치 비교 연산자(`!==`)는 각각 동등비교(`==`) 일치비교(`===`) 연산자의 반대 개념이다.
```javascript
// 부동등 비교
5 != 8;   // -> true
5 != 5;   // -> false
5 != '5'; // -> false

// 불일치 비교
5 !== 8;   // -> true
5 !== 5;   // -> false
5 !== '5'; // -> true
```

### 3.2.대소 관계 비교 연산자
대소관계 비교 연산자|예제|설명|부수효과
:---:|:---:|:---:|:---:
`>`| `x > y`|x가 y보다 크다|X
`<`|`x < y`|x가 y보다 작다|X
`>=`|`x >= y`|x가 y보다 크거나 같다|X
`<=`|`x <= y`|x가 y보다 작거나 같다|X

<br/>

## 4.삼항 조건 연산자
삼항 조건 연산자`ternary operator`는 조건식의 평가 결과에 따라 반환할 값을 결정한다. 부수효과는 없다.
```javascript
var x = 2;

// 2 % 2는 0이고 0은 false로 암묵적 타입 변환된다.
var result = x % 2 ? '홀수' : '짝수';

console.log(result); // 짝수
```
삼항 조건 연산자는 첫 번째 피 연산자가 `true`로 평가되면 두 번째 피 연산자를 반환하고, 첫 번째 피연산자가 `false`로 평가되면 세 번째 피연산자를 반환한다.  
물음표(`?`)앞의 첫 번째 피연산자는 조건식, 즉 불리언 타입의 값으로 평가될 표현식이다.  
만약 평가 결과가 불리언 값이 아니면 불리언 값으로 암묵적 타입 변환된다.
<br/>

아래와 같이 `if...else`문으로 삼항 조건 연산자 표현식과 유사하게 처리할 수 있다.
```javascript
var x = 2, result;

// 2 % 2는 0이고 0은 false로 암묵적 타입 변환된다.
if (x % 2) result = '홀수';
else       result = '짝수';

console.log(result); // 짝수
```
`if...else`문은 표현식이 아니고 문이기 때문에 값처럼 사용할 수 없다.
```javascript
var x = 10;

// if...else 문은 표현식이 아닌 문이다. 따라서 값처럼 사용할 수 없다.
var result = if (x % 2) { result = '홀수'; } else { result = '짝수'; };
// SyntaxError: Unexpected token if
```

## 5.논리 연산자
논리 연산자`logical operator`는 우항과 좌항의 피연산자(부정 논리 연산자의 경우 우항의 피연산자)를 논리 연산한다.
논리연산자|의미|부수효과
:---:|:---:|:---:
`||`|논리합(OR)|X
`&&`|논리곱(AND)|X
`!`|부정(NOT)|X

```javascript
// 논리합(||) 연산자
true || true;   // -> true
true || false;  // -> true
false || true;  // -> true
false || false; // -> false

// 논리곱(&&) 연산자
true && true;   // -> true
true && false;  // -> false
false && true;  // -> false
false && false; // -> false

// 논리 부정(!) 연산자
!true;  // -> false
!false; // -> true
```
피연산자가 불리언 타입이 아닌경우 암묵적 타입 변환되어 평가된다.
```javascript
// 암묵적 타입 변환
!0;       // -> true
!'Hello'; // -> false
```
또한 평가 결과가 불리언 타입 값이 아닐 수도 있따.
논리합(`||`) 또는 논리곱(`&&`) 연산자 표현식은 언제나 2개의 피연산자 중 어느 한쪽으로 평가된다.
```javascript
// 단축 평가
'Cat' && 'Dog'; // -> 'Dog'
```

<br/>

## 6.쉼표 연산자
쉼표(`,`)연산자는 왼쪽 피연산자부터 차례대로 피연산자를 평가하고 마지막 피연산자의 평가가 끝나면 마지막 피연산자의 평가 결과를 반환한다.
```javascript
var x, y, z;
x = 1, y = 2, z = 3; // 3

// 이를 응용하여 함수를 만들어보자면
function add(){
    return a=1,b=2,console.log(a+b),a+b;
}
add(); // 3 3
```
<br>

## 7.그룹 연산자
소괄호(`()`)로 피연산자를 감싸는 그룹 연산자는 자신의 피연산자인 표현식을 가장 먼저 평가한다.
```javascript
10 * 2 + 3; // -> 23

// 그룹 연산자를 사용하여 우선순위를 조절
10 * (2 + 3); // -> 50
```

<br>

## 8.typeof 연산자
`typeof`연산자는 피연산자의 데이터 타입을 문자열로 반환한다.
```javascript
typeof ''              // -> "string"
typeof 1               // -> "number"
typeof NaN             // -> "number"
typeof true            // -> "boolean"
typeof undefined       // -> "undefined"
typeof Symbol()        // -> "symbol"
typeof null            // -> "object"
typeof []              // -> "object"
typeof {}              // -> "object"
typeof new Date()      // -> "object"
typeof /test/gi        // -> "object"
typeof function () {}  // -> "function"
```
`typeof`연산자로 `null`값을 연산해 보면 `null`이 아닌 `object`를 반환한다는 것을 주의하자!!
`null`을 확인할 때는 `typeof`대신 일치연산자(`===`)를 사용하는것이 좋다.
```javascript
var foo = null;

typeof foo === null; // -> false
foo === null;        // -> true
```

<br>

## 9.지수 연산자
ES7에서 도입된 지수 연산자는 좌항의 피연산자를 밑으로, 우항의 피연산자를 지수로 거틉 제곱하여 숫자 값을 반환한다.  
`Math.pow()`와 동일하다.
```javascript
2 ** 2;   // -> 4
2 ** 2.5; // -> 5.65685424949238
2 ** 0;   // -> 1
2 ** -2;  // -> 0.25

Math.pow(2, 2);   // -> 4
Math.pow(2, 2.5); // -> 5.65685424949238
Math.pow(2, 0);   // -> 1
Math.pow(2, -2);  // -> 0.25
```

## 10.그 외의 연산자
연산자|설명
:---:|:---
`?.`| 옵셔널 체이닝 연산자
`??`|null 병합 연산자
`delete`|프로퍼티 삭제
`new`|생성자 함수를 호출할 때 사용하여 인스턴스를 생성
`instanceof`|좌변의 객체가 우변의 생성자 함수와 연결된 인스턴스인지 판별
`in`| 프로퍼티 존재 확인

<br>

## 11.연산자의 부수 효과
대부분의 연산자는 다른 코드에 영향을 주지 않는다.  
`1 * 2`같은 경우 코드에 어떠한 영향도 주지 않고 단지 새로운 값 2를 생성할 뿐이다.  
하지만 할당 연산자(`=`), 증가/감소 연산자(`++`/`--`), `delete`연사자는 부수효과가 있다.  


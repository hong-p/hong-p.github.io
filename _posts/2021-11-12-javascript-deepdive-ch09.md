---
title: "모던 자바스크립트 딥 다이브 ch09 타입 변환과 단축 평가"
excerpt: "[javascript] 모던 자바스크립트 Deep Dive ch09 타입 변환과 단축 평가"
categories:
 - Javascript
tags:
 - Javascript
toc: true
toc_sticky: true
last_modified_at: 2021-11-12
---

# 타입 변환과 단축 평가
## 1.타입 변환이란?
자바스크립트의 모든 값은 타입이 있다.  
개발자가 의도적으로 값의 타입을 변환하는 `명시적 타입변환(explicit coercion)`과 의도와 상관없이 자바스크립트 엔진에 의해 변환되는 `암묵적 타입변환(implicit coercion)`이 있다.

<br/>

명시적 타입변환(explicit coercion)은 `타입 캐스팅(type casting)`이라고도 한다.
```javascript
var x = 10;

// 명시적 타입 변환
// 숫자를 문자열로 타입 캐스팅한다.
var str = x.toString();
console.log(typeof str, str); // string 10

// x 변수의 값이 변경된 것은 아니다.
console.log(typeof x, x); // number 10
```

<br/>

암묵적 타입변환(implicit coercion)은 `타입 강제 변환(type coercion)`이라고도 한다.
```javascript
var x = 10;

// 암묵적 타입 변환
// 문자열 연결 연산자는 숫자 타입 x의 값을 바탕으로 새로운 문자열을 생성한다.
var str = x + '';
console.log(typeof str, str); // string 10

// x 변수의 값이 변경된 것은 아니다.
console.log(typeof x, x); // number 10
```

명시적 타입변환이나 암묵적 타입변환은 기존 원시 값을 직접 변경하는 것은 아니다.  
원시 값은 변경 불가능한 값(`immutable value`)이므로 변경할 수 없다.  
타입 변환이란 기존 원시 값을 사용해 다른 타입의 새로운 원시 값을 생성하는 것이다.  
<br>
위 예제에서 `x + ''` 표현식은 평가하기 위해 `x`변수의 숫자 값을 바탕으로 새로운 문자열 값 `'10'`을 생성하고 이것으로 표현식 `'10' + ''`를 평가한다.  
<br>
명시적 타입변환은 타입을 변경하겠다는 개발자의 의지가 코드에 드러나지만,  
암묵적 타입 강제변환은 자바스크립트 엔진에 의해 암묵적으로 드러나지 않게 변환되기 때문에 코드에 명백히 나타나지 않는다.  
그렇기 때문에 주의해서 사용해야 한다.  

<br>

## 2.암묵적 타입 변환
자바스크립트 엔진은 표현식을 평가할 대 개발자의 의도와는 상관없이 코드의 문맥을 고려해 암묵적으로 데이터 타입을 강제 변환할 때가 있다.
```javascript
// 피연산자가 모두 문자열 타입이어야 하는 문맥
'10' + 2 // -> '102'

// 피연산자가 모두 숫자 타입이어야 하는 문맥
5 * '10' // -> 50

// 피연산자 또는 표현식이 불리언 타입이어야 하는 문맥
!0 // -> true
if (1) { }
```

<br>

### 2.1 문자열 타입으로 변환
`+` 연산자를 사용하면 문자열 타입으로 변환된다.
`+` 연산자는 피연산자 중 하나 이상이 문자열이면 문자열 연결 연산자로 동작한다.

```javascript
1 + '2' // -> "12"
```

<br>

ES6에 도입된 템플릿 리터럴의 표현식 삽입도 평가 결과를 문자열 타입으로 암묵적 변환한다.
```javascript
`1 + 1 = ${1 + 1}` // -> "1 + 1 = 2"
```

<br>
문자열 타입으로 암묵적 타입변환 되는 예

```javascript
// 숫자 타입
0 + ''         // -> "0"
-0 + ''        // -> "0"
1 + ''         // -> "1"
-1 + ''        // -> "-1"
NaN + ''       // -> "NaN"
Infinity + ''  // -> "Infinity"
-Infinity + '' // -> "-Infinity"

// 불리언 타입
true + ''  // -> "true"
false + '' // -> "false"

// null 타입
null + '' // -> "null"

// undefined 타입
undefined + '' // -> "undefined"

// 심벌 타입
(Symbol()) + '' // -> TypeError: Cannot convert a Symbol value to a string

// 객체 타입
({}) + ''           // -> "[object Object]"
Math + ''           // -> "[object Math]"
[] + ''             // -> ""
[10, 20] + ''       // -> "10,20"
(function(){}) + '' // -> "function(){}"
Array + ''          // -> "function Array() { [native code] }"
```

### 2.2 숫자 타입으로 변환
산술 연산자중 `+`제외한 연산자 `-`, `*`, `/`, `%` 를 사용하면 숫자 타입으로 변환된다.
```javascript
1 - '1'   // -> 0
1 * '10'  // -> 10
1 / 'one' // -> NaN
13 % '5' // -> 3
```

<br>

또한 비교 연산자도 숫자 타입으로 변환한다.
```javascript
'1' > 0  // -> true
```

<br>

`+`단항 연산자를 문자열 앞에 사용하면 숫자 타입의 값으로 변환한다.
```javascript
// 문자열 타입
+''       // -> 0
+'0'      // -> 0
+'1'      // -> 1
+'string' // -> NaN

// 불리언 타입
+true     // -> 1
+false    // -> 0

// null 타입
+null     // -> 0

// undefined 타입
+undefined // -> NaN

// 심벌 타입
+Symbol() // -> ypeError: Cannot convert a Symbol value to a number

// 객체 타입
+{}             // -> NaN
+[]             // -> 0
+[10, 20]       // -> NaN
+(function(){}) // -> NaN
```

### 2.3 불리언 타입으로 변환
`if`문의 조건식은 불리언 타입으로 암묵적 타입 변환 한다.
```javascript
if ('')    console.log('1');
if (true)  console.log('2');
if (0)     console.log('3');
if ('str') console.log('4');
if (null)  console.log('5');

// 2 4
```

<br>

자바스크립트 엔진은 불리언 타입이 아닌 값을 `Truthy`값(참으로 평가되는 값) 또는 `Falsy`값(거짓으로 평가되는 값)으로 구분한다.

<br>

Falsy 값
 - `false`  
 - `undefined`
 - `null`
 - `0`
 - `-0`
 - `NaN`
 - `''`(빈 문자열)

```javascript
 // 아래의 조건문은 모두 코드 블록을 실행한다.
if (!false)     console.log(false + ' is falsy value');
if (!undefined) console.log(undefined + ' is falsy value');
if (!null)      console.log(null + ' is falsy value');
if (!0)         console.log(0 + ' is falsy value');
if (!NaN)       console.log(NaN + ' is falsy value');
if (!'')        console.log('' + ' is falsy value');
```

`Falsy`값 이외의 모든 값은 `true`로 평가되는 `Truthy`값이다.
```javascript
// 전달받은 인수가 Falsy 값이면 true, Truthy 값이면 false를 반환한다.
function isFalsy(v) {
  return !v;
}

// 전달받은 인수가 Truthy 값이면 true, Falsy 값이면 false를 반환한다.
function isTruthy(v) {
  return !!v;
}

// 모두 true를 반환한다.
isFalsy(false);
isFalsy(undefined);
isFalsy(null);
isFalsy(0);
isFalsy(NaN);
isFalsy('');

// 모두 true를 반환한다.
isTruthy(true);
isTruthy('0'); // 빈 문자열이 아닌 문자열은 Truthy 값이다.
isTruthy({});
isTruthy([]);
```

<br>

## 3.명시적 타입 변환
개발자의 의도에 따라 명시적으로 타입을 변경하는 방법이다.  
표준 빌트인 생성자 함수(`String`, `Number`, `Boolean`)를 `new`연산자 없이 호출하는 방법 이 있고,  
빌트인 메서드를 사용하는 방법이 있다.  

### 3.1 문자열 타입으로 변환
문자열 타입이 아닌 값을 문자열 타입으로 변환하는 방법
 - `String` 생성자 함수를 `new` 연산자 없이 호출하는 방법
 - `Object.prototype.toString` 메서드를 사용하는 방법
 - 문자열 연결 연산자를 이용하는 방법

```javascript
// 1. String 생성자 함수를 new 연산자 없이 호출하는 방법
// 숫자 타입 => 문자열 타입
String(1);        // -> "1"
String(NaN);      // -> "NaN"
String(Infinity); // -> "Infinity"
// 불리언 타입 => 문자열 타입
String(true);     // -> "true"
String(false);    // -> "false"

// 2. Object.prototype.toString 메서드를 사용하는 방법
// 숫자 타입 => 문자열 타입
(1).toString();        // -> "1"
(NaN).toString();      // -> "NaN"
(Infinity).toString(); // -> "Infinity"
// 불리언 타입 => 문자열 타입
(true).toString();     // -> "true"
(false).toString();    // -> "false"

// 3. 문자열 연결 연산자를 이용하는 방법
// 숫자 타입 => 문자열 타입
1 + '';        // -> "1"
NaN + '';      // -> "NaN"
Infinity + ''; // -> "Infinity"
// 불리언 타입 => 문자열 타입
true + '';     // -> "true"
false + '';    // -> "false"
```

<br>

※ 참고(new 연산자 사용 여부에 따라 생성 타입이 달라진다.)
```javascript
// new 연산자를 사용하지 않으면 원시값이 생성된다.
var primitive = String('a');
typeof primitive // 'string'

// new 연산자와 함께 사용하면 object 타입이 생성된다.
var obj = new String('a');
typeof obj // 'object'
```
<br>

### 3.2 숫자 타입으로 변환
숫자 타입이 아닌 값을 숫자 타입으로 변환 하는 방법
 - `Number` 생성자 함수를 `new`연산자 없이 호출하는 방법
 - `parseInt, parseFloat` 함수를 사용하는 방법(문자열만 숫자 타입으로 변환 가능)
 - `+` 단항 산술 연산자를 이용하는 방법
 - `*` 산술 연산자를 이용하는 방법

```javascript
 // 1. Number 생성자 함수를 new 연산자 없이 호출하는 방법
// 문자열 타입 => 숫자 타입
Number('0');     // -> 0
Number('-1');    // -> -1
Number('10.53'); // -> 10.53
// 불리언 타입 => 숫자 타입
Number(true);    // -> 1
Number(false);   // -> 0

// 2. parseInt, parseFloat 함수를 사용하는 방법(문자열만 변환 가능)
// 문자열 타입 => 숫자 타입
parseInt('0');       // -> 0
parseInt('-1');      // -> -1
parseFloat('10.53'); // -> 10.53

// 3. + 단항 산술 연산자를 이용하는 방법
// 문자열 타입 => 숫자 타입
+'0';     // -> 0
+'-1';    // -> -1
+'10.53'; // -> 10.53
// 불리언 타입 => 숫자 타입
+true;    // -> 1
+false;   // -> 0

// 4. * 산술 연산자를 이용하는 방법
// 문자열 타입 => 숫자 타입
'0' * 1;     // -> 0
'-1' * 1;    // -> -1
'10.53' * 1; // -> 10.53
// 불리언 타입 => 숫자 타입
true * 1;    // -> 1
false * 1;   // -> 0
```
<br>

### 3.3 불리언 타입으로 변환
불리언 타입이 아닌 값을 불리언 타입으로 변환
 - `Boolean`생성자 함수를 `new`연산자 없이 호출하는 방법
 - `!`부정 논리 연산자를 두 번 사용하는 방법

```javascript
// 1. Boolean 생성자 함수를 new 연산자 없이 호출하는 방법
// 문자열 타입 => 불리언 타입
Boolean('x');       // -> true
Boolean('');        // -> false
Boolean('false');   // -> true
// 숫자 타입 => 불리언 타입
Boolean(0);         // -> false
Boolean(1);         // -> true
Boolean(NaN);       // -> false
Boolean(Infinity);  // -> true
// null 타입 => 불리언 타입
Boolean(null);      // -> false
// undefined 타입 => 불리언 타입
Boolean(undefined); // -> false
// 객체 타입 => 불리언 타입
Boolean({});        // -> true
Boolean([]);        // -> true

// 2. ! 부정 논리 연산자를 두번 사용하는 방법
// 문자열 타입 => 불리언 타입
!!'x';       // -> true
!!'';        // -> false
!!'false';   // -> true
// 숫자 타입 => 불리언 타입
!!0;         // -> false
!!1;         // -> true
!!NaN;       // -> false
!!Infinity;  // -> true
// null 타입 => 불리언 타입
!!null;      // -> false
// undefined 타입 => 불리언 타입
!!undefined; // -> false
// 객체 타입 => 불리언 타입
!!{};        // -> true
!![];        // -> true
```

<br>

## 4.단축 평가
### 4.1 논리 연산자를 사용한 단축 평가
논리합`||` 또는 논리곱`&&`연산자 표현식의 평가 결과는 불리언 값이 아닐 수도 있다.

```javascript
1===1 && 'a'==='a' // true
```
```javascript
'Cat' && 'Dog' // -> "Dog"
```
논리곱`&&`연산자는 두 개의 피연산자가 모두 `true`로 평가될 때 `true`를 반환한다.  
좌항에서 우항으로 평가가 진행된다.  
피 연산자가 불리언 타입이 아닐경우 값이 평가된다.  

<br>

위 예제에서 첫 번재 피연산자 `'Cat'`은 `Truthy`값이므로 `true`로 평가된다. 하지만 이 시점까지는 위 표현식을 평가할 수 없다. 두 번째 피연산자 까지 평가해야 표현식을 평가할 수 있다.  
따라서 두 번째 피연산자, 즉 `'Dog'`까지 평가하고 평가 결과인 `'Dog'`문자열을 그대로 반환한다.

<br>

논리합`||`연산자는 두 개의 피연산자 중 하나만 `true`로 평가되어도 `true`를 반환한다.  
좌항에서 우항으로 평가를 진행한다.

```javascript
'Cat' || 'Dog' // -> "Cat"
```

첫 번째 피연산자 `'Cat'`은 `Truthy`값이므로 `true`로 평가되고, 이 시점에서는 두 번째 피연산자까지 평가 할 필요없이 표현식이 평가되기 때문에 `'Cat'`문자열을 그대로 반환한다.

<br/>

단축 평가 표현식 | 평가 결과
:---:|:---:
 <code>true || anything</code> | true
 <code>false || anything </code> | anything
 <code>true && anything</code> | anything
 <code>false && anything</code> | true

```javascript
// 논리합(||) 연산자
'Cat' || 'Dog'  // -> "Cat"
false || 'Dog'  // -> "Dog"
'Cat' || false  // -> "Cat"

// 논리곱(&&) 연산자
'Cat' && 'Dog'  // -> "Dog"
false && 'Dog'  // -> false
'Cat' && false  // -> false
```

<br>

어떤 조건이 `Truthy`값인 경우 논리곱`&&`연산자 표현식으로 `if`문을 대체할 수 있다.

```javascript
var done = true;
var message = '';

// 주어진 조건이 true일 때
if (done) message = '완료';

// if 문은 단축 평가로 대체 가능하다.
// done이 true라면 message에 '완료'를 할당
message = done && '완료';
console.log(message); // 완료
```
<br>

조건이 `Falsy`값 인경우 논리합`||`연산자 표현식으로 `if`문을 대체할 수 있다.

```javascript
var done = false;
var message = '';

// 주어진 조건이 false일 때
if (!done) message = '미완료';

// if 문은 단축 평가로 대체 가능하다.
// done이 false라면 message에 '미완료'를 할당
message = done || '미완료';
console.log(message); // 미완료
```

<br>

삼항 조건 연산자는 `if...else`문을 대체 할 수 있다.

```javascript
var done = true;
var message = '';

// if...else 문
if (done) message = '완료';
else      message = '미완료';
console.log(message); // 완료

// if...else 문은 삼항 조건 연산자로 대체 가능하다.
message = done ? '완료' : '미완료';
console.log(message); // 완료
```

<br>

객체를 가리키기를 기대하는 변수가 `null`또는 `undefined`가 아닌지 확인하고 프로퍼티를 참조할 때  
단축 평가를 사용하면 에러없이 사용할 수 있다.

```javascript
// 그냥 참조
var elem = null;
var value = elem.value; // TypeError: Cannot read property 'value' of null


// 단축 평가 사용
var elem = null;
// elem이 null이나 undefined와 같은 Falsy 값이면 elem으로 평가되고
// elem이 Truthy 값이면 elem.value로 평가된다.
var value = elem && elem.value; // -> null
```

<br>

함수 매개변수에 기본값을 설정할 때  
함수를 호출할 때 인수를 전달하지 않으면 매개변수에는 `undefined`가 할당되는데, 단축 평가를 사용하면 이를 방지할 수 있다.

```javascript
// 단축 평가를 사용한 매개변수의 기본값 설정
function getStringLength(str) {
  str = str || '';  // 단축 평가
  return str.length;
}

getStringLength();     // -> 0
getStringLength('hi'); // -> 2


// ES6의 매개변수의 기본값 설정
function getStringLength(str = '') {
  return str.length;
}

getStringLength();     // -> 0
getStringLength('hi'); // -> 2
```

<br>

### 4.2 옵셔널 체이닝 연산자
ES11(ECMAScript2020)에 도입된 `옵셔널 체이닝(optional chaining)` 연산자 `?.`는 좌항의 피연산자가 `null`또는 `undefined`인 경우 `undefined`를 반환하고, 그렇지 않으면 우항의 프로퍼티 참조를 이어간다.

```javascript
var elem = null;

// elem이 null 또는 undefined이면 undefined를 반환하고, 그렇지 않으면 우항의 프로퍼티 참조를 이어간다.
var value = elem?.value;
console.log(value); // undefined
```
<br>

옵셔널 체이닝 연산자가 도입되기 전에는 논리곱`&&`을 사용했었다.  
하지만  논리곱`&&`연산자는 옵셔널 체이닝 연산자와는 다르게 `Falsy`(`false`, `undefined`, `0`, `NaN`, `' '`)값이면 좌항의 피연산자를 반환 해버리기 때문에 조금 문제가 있다.

```javascript
var elem = null;

// elem이 Falsy 값이면 elem으로 평가되고 elem이 Truthy 값이면 elem.value로 평가된다.
var value = elem && elem.value;
console.log(value); // null



var str = '';

// 문자열의 길이(length)를 참조한다.
var length = str && str.length;

// 문자열의 길이(length)를 참조하지 못한다.
console.log(length); // ''
```

옵셔널 체이닝 연산자`?.`는 좌항의 피연산자가 `false`로 평가되는 `Falsy`(`false`, `undefined`, `0`, `NaN`, `' '`)값이라도 `null` 또는 `undefined`가 아니면 우항의 프로퍼티 참조를 이어간다.

```javascript
var str = '';

// 문자열의 길이(length)를 참조한다. 이때 좌항 피연산자가 false로 평가되는 Falsy 값이라도
// null 또는 undefined가 아니면 우항의 프로퍼티 참조를 이어간다.
var length = str?.length;
console.log(length); // 0
```

<br>

### 4.3 null 병합 연산자
ES11(ECMAScript2020)에 도입된 `null 병합(nullish coalescing)`연산자 `??`는 좌항의 피연산자가 `null` 또는 `undefined`인 경우 우항의 피연산자를 반환하고, 그렇지 않으면 좌항의 피연산자를 반환한다.  

```javascript
// 좌항의 피연산자가 null 또는 undefined이면 우항의 피연산자를 반환하고, 그렇지 않으면 좌항의 피연산자를 반환한다.
var foo = null ?? 'default string';
console.log(foo); // "default string"
```

null 병합 연산자도 도입되기 이전에는 논리합`||`연산자를 사용했는데 논리곱`&&`연산자와 동일하게 모든 좌항의 피연산자가 `Falsy`(`false`, `undefined`, `0`, `NaN`, `' '`)값인 경우 우항의 피연산자를 반환하여 예기치 않은 동작이 발생할 수 있다.

```javascript
// Falsy 값인 0이나 ''도 기본값으로서 유효하다면 예기치 않은 동작이 발생할 수 있다.
var foo = '' || 'default string';
console.log(foo); // "default string"


// 좌항의 피연산자가 Falsy 값이라도 null 또는 undefined이 아니면 좌항의 피연산자를 반환한다.
var foo = '' ?? 'default string';
console.log(foo); // ""
```

옵셔널 체이닝과 마찬가지로 null 병합 연산자 `??`는 좌항의 피연산자가 `false`로 평가되는 `Falsy`(`false`, `undefined`, `0`, `NaN`, `' '`)값이라도 `null` 또는 `undefined`가 아니면 우항의 프로퍼티 참조를 이어간다.

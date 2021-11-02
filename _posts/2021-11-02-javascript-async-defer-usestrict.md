---
title: "[javascript] async, defer, use strict 알아보기"
excerpt: "javasciprt의 script 태그 속성인 async, defer을 알아보고, use strict에 대해 설명합니다. "

categories:
 - Javascript
tags:
 - Javascript
toc: true
toc_sticky: true
last_modified_at: 2021-11-02
---

# async, defer
 > html을 작성하고 javascript를 추가할때 script 태그에 어떤 속성을 주느냐에 따라 js파일을 다운로드하거나 실행하는 타이밍을 조절 할수 있다.
 
 > 기본적으로 브라우저에서 html을 해석하는 경우 라인을 한줄 한줄 읽으면서 내려오기 때문에 script 태그를 어느 위치에 놓느냐에 따라 js 파일을 다운받고 실행하는 시점이 달라진다.

## head 태그 내

아래와 같은 소스의 경우 head 태그안에 있어 사용자가 화면 내용을 보기 전에 js파일을 다운받고  실행하게 된다.

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <script src="main.js"></script> 
</head>
<body>
    <div>
        hello
    </div>
</body>
</html>
```
![code](https://hong-p.github.io/assets/images/async-defer-1.png "브라우저가 html 파일 해석하는 순서") 

HTML을 parsing(해석) 하다가 작업을 멈추고 js파일을 다운받고 실행하고 이를 완료하면 다시 HTML을 parsing(해석) 하고 사용자에게 보여준다.
만약 js파일이 엄청 크고 무겁다면 사용자에게는 한동안 하얀 화면만 보여주다가 한참 뒤에 데이터를 볼수 있게 된다.


## body 태그 내
만약 script 태그를 아래와 같이 body 맨 밑에 놓는다면 어떨까?

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
</head>
<body>
    <div>
        hello
    </div>
    <script src="main.js"></script> 
</body>
</html>
```
![code](https://hong-p.github.io/assets/images/async-defer-2.png "브라우저가 html 파일 해석하는 순서")

HTML 해석을 먼저 완료하고 마지막에 js파일을 다운로드 받고 실행하게 되어 사용자에게 먼저 정보를 제공해 주고, js파일을 실행하여 부가적인 작업을 진행 할수 있게된다.

​
## head + async

그렇다면 async 속성은 뭘까?
haed 태그안에 script 태그를 넣으면서 async 속성을 넣어 비동기로 js파일의 다운로드를 진행하는것이다.
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <script async src="main.js"></script> 
</head>
<body>
    <div>
        hello
    </div>
</body>
</html>
```
![code](https://hong-p.github.io/assets/images/async-defer-3.png "브라우저가 html 파일 해석하는 순서")

위 이미지와 같이 HTML을 parsing 하다가 script 태그를 만나면 fetching(다운로드) 한다.
그러다가 fetching 이 끝나면 HTML parsing을 멈추고 js파일을 실행한다.
실행이 끝나면 나머지 HTML파일을 parsing해 사용자에게 보여준다.

## head + defer
이와 비슷한 defer 속성은 async와 조금 다른데, fetching(다운로드)는 async와 비슷하게 비동기 진행하지만 executing(실행)은 HTML parsing을 모두 마친 뒤 에 진행하는 점이 다르다.

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Document</title>
    <script defer src="main.js"></script> 
</head>
<body>
    <div>
        hello
    </div>
</body>
</html>
```

![code](https://hong-p.github.io/assets/images/async-defer-4.png "브라우저가 html 파일 해석하는 순서")

HTML을 parsing 하다가 script 태그를 만나면 fetching(다운로드)를 비동기로 진행하고, fetching이 끝나더라도 HTML parsing을 멈추지 않고 끝까지 진행 한 다음에 js 파일을 executing한다.

​
# use strict
use strict 이란 ECMAScript 5 부터 추가된 것이다.
js(javascript) 소스에 명시 해주면 원래 유연하게 사용할 수 있었던 javascript를 좀더 엄격하게 하겠다는 의미이다.
예를들면 아래와 같은 js 파일을 실행하면 아무런 오류도 보이지 않는데

```javascript
console.log('hello world');

a=2;
```

![code](https://hong-p.github.io/assets/images/usestrict-1.png "결과")

똑같은 소스의 맨 위에 use strict 를 선언해 주면 아래와 같이 에러를 보여주게 된다.

```javascript
'use strict';

console.log('hello world');

a=2;
```
![code](https://hong-p.github.io/assets/images/usestrict-2.png "결과")

위 에러는 선언되지 않은 변수가 있다는 에러이므로
let a; 를 선언해 주면 에러는 사라진다.

```javascript
'use strict';

console.log('hello world');

let a;
a=2;
```
![code](https://hong-p.github.io/assets/images/usestrict-3.png "결과")
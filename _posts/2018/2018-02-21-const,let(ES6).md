---
layout: post
title: (javascript) const, let
categories: [javascript, const, let]
tags: [javascript, const, let]
#fullview: true
description: 자바스크립트 const, let.
comments: true
published: true
---

const, let
===

`타자치는 곰 - `
<br/>


### const, let
* ES6 이전의 변수 선언시에 `var`를 이용해 선언을 했다.<br/>
ES6에서 새로 추가된 `const` 와 `let`을 알아보며,<br/>
기존의 `var`과의 차이점을 알아보자.
  * 현재까지 발표된 버전은 `ES8`이다.

* 우선, `var`과의 가장 큰 차이점은 `Scope` 이다.<br/>
`var`은 `함수스코프`이고, `const`와`let`은 `블록스코프`이다.
  * `함수스코프`의 경우 함수내의 어느곳에서 호출해도 접근이 가능하지만,<br/>
  `블록스코프`의 경우 블록내에서만 호출이 가능하며, 해당 블록이 아닌곳 에서는 호출이 불가능하다.
  * 예로, if문 내에서 사용한 `var` 변수는 if문 외부에서도 호출이 가능하지만, `const`와`let`은 불가능하다.

```javascript
if(true){
    var i = 5;
}
console.log(i); // 5
```
```javascript
if(true){
    const i = 5;
}
console.log(i); // Uncaught ReferenceError: i is not defined
```
```javascript
if(true){
    let i = 5;
}
console.log(i); // Uncaught ReferenceError: i is not defined
```

* `var`에는 아래와 같은 문제가 존재 한다.

```javascript
var a = "AAA";
var a = "BBB"; // 이미 생성된 변수명으로 재선언 했으나, 아무런 문제가 발생하지 않는다.

c = "CCC";
var c; // hoisting으로 인해 ReferenceError에러가 나지 않는다.

console.log(c) // CCC
```

* `const`와`let`은 `var`과 다르게 변수 재선언이 불가능하다.<br/>
`const`와`let`의 차이점은 변수의 재할당 여부이다.<br/>
`const`는 재할당이 불가능하지만, `let`은 재할당이 가능하다.

```javascript
let a = "AAA";
let a = "BBB";  // 에러
a = "CCC";  // 정상

console.log(a) // CCC
```
```javascript
const a = "AAA";
const a = "BBB";  // 에러
a = "CCC";  // 에러
```

* `const`와`let`의 한가지 더 다른점은,<br/>
`let`은 변수 선언 후에 값을 할당 해도 되지만,<br/>
`const`는 변수 선언시 값을 할당 해야 한다.

```javascript
let a;
a = "AAA"; // 정상
```
```javascript
const a; // 에러
a = "AAA"; // 에러

const a = "AAA"; // 정상
```

* 하지만, `const`는 새로운 값을 대입하는것을 막을뿐, 할당된 객체나 배열의 요소를 바꾸는것은 가능하다. 즉, 데이터의 주소값만 고정하는 것 이다.

```javascript
const arr = [1,2,3];
c[0] = 5;
console.log(c); // [5,2,3]
```
```javascript
const obj = {id: "woong"};
obj.id = "sik";
console.log(obj); // {id: "sik"}
```
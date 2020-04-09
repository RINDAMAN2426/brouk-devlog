---
title: Javascript(1)-Arrow Function
date: 2020-04-09 14:04:04
category: javascript
draft: false
---

# Arrow Function

Arrow Function은 ES6에서 등장한 문법입니다. 기존의 function과 어떠한 차이가 있는지 알아보았습니다.

## 기본문법

```js
var foo = () => console.log('bar') // bar

var foo = x => console.log(x)
foo('bar') // bar

var foo = (a, b) => a + b
foo(1, 2) // 3

var foo = (a, b) => {
  a + b
} // undefined

var foo = (a, b) => {
  return a + b
}
foo(1, 2) // 3

//객체
var foo = a => ({ b: a })
```

위와 같이 표현할 수 있습니다. 주의하셔야 할 점은 `{} (Block)`을 사용하실 경우는 return을 사용해야 하는 점입니다.

## ES5 vs ES6

```js
//es5
function double(params) {
  return params * 2
}
double(4) // 8

//es 6
const double = params => params * 2
double(4) // 8
```

## bind

### es5

> <b>기존 es5에서 함수의 내부 콜백 함수에서 this는 window입니다.</b>

그로 인해 아래 코드와 같이

```js
var brouk = {
  age: '28',
  counter: function counter() {
    setTimeout(function() {
      console.log(this.age)
      // console.log(this) // window
    }, 1000)
  },
}

brouk.counter() // undefined
```

내부함수 setTimeout의 this는 window 이기때문에 undefined가 출력되게 됩니다.
문제 해결을 위해선

```js
var brouk = {
  age: '28',
  counter: function counter() {
    setTimeout(
      function() {
        console.log(this.age)
      }.bind(this),
      1000
    )
  },
}

brouk.counter() // 28
```

아래와 같이 bind처리를 해주어야 합니다.

### es6 (Arrow Function)

Arrow Function의 경우

```js
var brouk = {
  age: '28',
  counter: function counter() {
    setTimeout(() => {
      console.log(this.age)
    }, 1000)
  },
}

brouk.counter() // 28
```

위와 같이 바깥 함수에 적용 되는 것을 확인 하실 수 있습니다.

this의 scope에 대해서는 따로 한번 다시 정리를 해야할 필요가 있을 것 같습니다.

> Arrow Function의 기본 문법 및 ES5와의 Scope차이를 인지해둡시다.

# Reference

- https://www.freecodecamp.org/news/when-and-why-you-should-use-es6-arrow-functions-and-when-you-shouldnt-3d851d7f0b26/
- https://velog.io/@ki_blank/JavaScript-%ED%99%94%EC%82%B4%ED%91%9C-%ED%95%A8%EC%88%98Arrow-function#2-%ED%99%94%EC%82%B4%ED%91%9C-%ED%95%A8%EC%88%98-this

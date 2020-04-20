---
title: javascript(2)-this
date: 2020-04-20 12:04:17
category: javascript
draft: false
---

# Reference

[bono's blog - this는 어렵지 않습니다](https://blueshw.github.io/2018/03/12/this/)

위 링크의 게시물을 꼭 봐주시면 감사하겠습니다.

## This

```js
alert(this === window) // true

const caller = {
  call: function() {
    alert(this === window)
  },
}

caller.call() // false, this = caller
```

위 코드처럼 this는 호출자를 가르키고 있습니다.\
첫번째 코드의 경우는 `window.alert(this === window)`와 동일합니다.

### Strict Mode

```js
function nonStrictMode() {
  return this
}
function strictMode() {
  'use strict'
  return this
}
console.log(nonStrictMode()) // window
console.log(strictMode()) // undefined
console.log(window.strictMode()) // window
```

### 생성자(new) 함수/객체

```js
function Object(name, color) {
  this.name = name
  this.color = color
  this.isWindow = function() {
    return this === window
  }
}

const newObj = new Object('anna', 'red')
console.log(newObj.name) // anna
console.log(newObj.color) // red
console.log(newObj.isWindow()) // false

// new를 빼먹는 경우
const newObj2 = Object('bruce', 'orange')
console.log(newObj2.name) // error
console.log(newObj2.color) // error
console.log(newObj2.isWindow()) // error
```

### 일반 객체

```js
const person = {
  name: 'chris',
  age: 15,
  getName: function() {
    return this.name
  },
}

console.log(person.getName()) // chris

const david = person
david.name = 'David'
console.log(david.getName()) // David
console.log(person.getName()) // David
```

david는 person의 레퍼런스 변수이기 때문에 `david.name`이 바뀐다면 `person.name`도 바뀌게 됩니다. 이를 방지하기 위해선 `ES6`의 `Object.assin()` 메소드를 사용하셔야 합니다.

> const david = Object.assign({}, person);

### Arrow Function

```js
function Family(firstName) {
  this.firstName = firstName
  const names = ['eric', 'fabian', 'gary']
  names.map(function(lastName, index) {
    console.log(`${lastName} ${this.firstName}`)
    console.log(this)
  })
}

const kims = new Family('kim')
// eric undefined
// Window
// fabian undefined
// Window
// gary undefined
// Window
```

[javascript(1)-arrow-function](<https://brouk-devlog.netlify.com/javascript/javascript(1)-arrow-function/>)에서 언급했던 내용입니다. 함수의 내부 콜백함수에서의 this는 window를 가르키게 됩니다. \
해결방법에 대해서 다시 설명을 드리자면 두가지 방법이 있습니다.

```js
// bind
function Family(firstName) {
  this.firstName = firstName
  const names = ['eric', 'fabian', 'gary']
  names
    .map(function(lastName, index) {
      console.log(`${lastName} ${this.firstName}`)
      console.log(this)
    })
    .bind(this) // binding
}

// arrow function
function Family(firstName) {
  this.firstName = firstName
  const names = ['eric', 'fabian', 'gary']
  names.map((lastName, index) => {
    console.log(`${lastName} ${this.firstName}`)
  })
}
```

Arrow Function의 원리는 다음과 같습니다.

```js
function Family(firstName) {
  var _this = this
  this.firstName = firstName
  var names = ['eric', 'fabian', 'gary']
  names.map(function(lastName, index) {
    console.log(lastName + ' ' + _this.firstName)
  })
}
```

즉 별도의 상수 \_this를 통해서 this를 명시 해주는 작업을 해주신다면 bind없이 this의 문제를 해결하실 수 있습니다.

> 전체적인 내용은 저번 포스팅에 언급한 내용과 비슷하지만 this의 여러 사례에 대한 좋은 글이 있어서 이렇게 옮겨 적게 되었습니다. 함수 내부의 콜백 함수에서 this를 사용하시게 될 경우에 주의하시면 될 것 같습니다

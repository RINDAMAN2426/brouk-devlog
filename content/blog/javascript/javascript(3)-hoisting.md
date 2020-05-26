---
title: javascript(3)-hoisting
date: 2020-05-26 21:05:55
category: javascript
draft: false
---

# Hoisting

hoisting이란 javascript에서 변수의 정의가 선언/초기화/할당이 분리되는 것을 말합니다.\
어떠한 변수가 정의 된다면 그 스코프내의 최상단에서 선언이 이루어 집니다.

```js
console.log(name) // undefined
var name = 'brouk'

const hoisting = () => {
  console.log(name)
  var name = 'brouk' // undefined
}
hoisting()
```

위와 같이 전역 컨텍스트, 혹은 함수범위 내에서 정의를 참조하기 전에 하더라도 선언이 최상단으로 이동하여 `undefined` 처리가 되는 것을 볼 수 있습니다.

하지만 `let`, `const`의 경우는 다릅니다.

```js
console.log(name)
let name = 'brouk' // ReferenceError: Cannot access 'name' before initialization

const hoisting = () => {
  console.log(name)
  let name = 'brouk'
}
hoisting() // ReferenceError: Cannot access 'name' before initialization
```

`let`과 `const`의 경우에는 `ReferenceError`가 발생하게 됩니다.\
이는 `TDZ`로 인해 발생하는데 간단하게 설명드리면 `let`, `const`의 경우에도 똑같이 스코프에 진입할때 `hositing`이 되어 변수를 생성을 하고 `TDZ`가 생성되지만 실제 변수가 정의되어있는 부분으로 오기 전까지는 `access`가 불가능합니다.

`let`, `const`와 `var`의 차이에 대해서는 다음 포스팅에서 다루도록 하겠습니다.

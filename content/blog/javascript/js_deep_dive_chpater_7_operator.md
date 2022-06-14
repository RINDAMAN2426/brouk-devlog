---
title: js_deep_dive_chpater_7_operator
date: 2022-06-15 03:06:84
category: javascript
draft: false
---

# Chapter 7. Operator

- `operand`는 값으로 평가될 수 있어야 하며, 마찬가지로 연산자 표현식도 값으로 평가될 수 있는 표현식이다.

## Arithmetic operators

- 수학적 계산을 수행하여 새로운 숫자 값을 생성
- 산술 연산이 불가능하다면 `NaN`을 반환

### binary arithmetic operators

- 2개의 `operand`를 산술 연산하여 숫자 값을 만듬
- side effect는 없음 (`operand`가 변하는 일은 없고 새로운 값을 만듦)

### unary arithmetic operators

- 1개의 `operand`를 산술 연산하여 숫자값을 만듦
- `++`,`--` 연산자는 `operand`의 값을 변경하는 side effect가 있음

  ````js
  var x = '1';
  console.log(+x); // 1
  console.log(x); // '1'

  x = true;
  console.log(+x); // 1
  console.log(x); // true

  x = false
  console.log(+x); // 0
  console.log(x); // false

  x = 'Hello';
  console.log(+x); // NaN
  console.log(x); // 'Hello'
      	```
  ````

### 문자열 연결 연산자

- `+`는 `operand` 중 하나 이상이 문자열인 경우 문자열 연결 연산자로 동작
  `js '1' + 2; // -> '12' 1 + '2'; // -> '12' 1 + true; // 2, true는 1로 타입 변환 1 + false; // 1, false는 0으로 타입 변환 1 + null; // 1, null은 0으로 타입 변환 +undefined; // NaN 1 + undefined; // NaN`
- 자바스크립트 엔진에 의한 압묵적인 타입 변환. `Implicit coercion`, `type coercion`
- 앞선 `unary` 경우도 이와 같음

## assignment operator

- 우항에 있는 `operand`의 평가 결과를 좌항의 변수에 할당
- 할당문 또한 값으로 평가되는 표현식인 문.
- 따라서 `a = b = c = 0;`과 같은 연쇄할당이 가능

## comparison operator

- 좌, 우항의 `operand` 비교 후 결과를 `boolean`값으로 반환

### 동등/일치 비교 연산자 (loose/strict equality operator)

- `strict`는 값 뿐만 아니라 타입도 비교함
  `js NaN === NaN // false , 자신과 일치하지 않는 유일한 값 Number.isNaN(NaN); // true Number.isNaN(1 + undefined); // true`
- 양의 0 과 음의 0 또한 서로 비교 시 true

#### Object.is

```js
Object.is(-0, +0) // false
Object.is(NaN, NaN) // false
```

### 대소 관계 비교 연산자

- `operand`들의 크기를 비교하여 `boolean` 리턴

## Ternary operator

- 조건식의 평가 결과가 `boolean`이 아니면 암묵적으로 `boolean` 값으로 타입 변환됨
  `js var x = 2; var result = x % 2 ? 'odd' : 'even'; // x % 2 = 0 -> false 암묵적 타입 변환`
- 값으로 평가할 수 있는 표현식의 문

## Logical operator

- 우항과 자항의 `operand`를 논리연산함
- 논리 부정 연산자(!)는 항상 `boolean` 값을 리턴. `boolean` 값이 아니라면 암묵적 타입 변환

  ```js
  !0 // true
  !'Hello' // false

  // 단축 평가
  'Cat' && 'Dog' // 'Dog'
  ```

* 논리합, 논리곱 연산자 표현식은 `boolean` 값이 아닌 경우 2개의 `operand` 중 어느 한쪽으로 평가됨.

## Comma operator

- 좌측부터 차례대로 평가, 마지막 `operand`의 결과를 반환
  `js var x,y,z; x = 1, y = 2, z = 3; // 3`

## Group operator

- 우선순위 조절 가능

## typeof operator

- `"string"`,`"number"`,`"boolean"`,`"undefined"`,`"symbol"`,`"object"`,`"function"` 7가지 중 하나를 리턴
  `js typeof '' // "string" typeof 1 // "number" typeof NaN // "number" typeof true // "boolean" typeof undefined // "undefined" typeof Symbol() // "symbol" typeof null // "object" typeof [] // "object" typeof {} // "object" typeof new Date() // "object" typeof /test/gi // "object" typeof function() {} // "function"`
- `null`이 `object`인 이유는 자바스크립트 첫 번째 버전의 버그. 기존 코드에 영향을 줄 수 있어 수정되지 못하고 있음. 따라서 `null` 체크는 일치 연산자를 사용
- 선언하지 않은 식별자를 `typeof` 연산을 하면 `undefined`가 반환됨.

## exponent operator `**`\*\* - ES7에서 도입. 좌항은 `base`, 우항은 `exponent`로 거듭 제곱

- 이전에는 `Math.pow` 사용
- 음수가 `base`가 되려면 괄호를 사용해야함
  `js (-5) ** 2; // 25`
- `binary`연산자 중 가장 우선순위 높음

## 그 외

- `?.`: optional chaining
- `??`: nullish coalescing operator
- `delete`: property 삭제
- `new`: 생성자 함수를 호출할 때 사용하여 instance 생성
- `instance of`: 좌변의 객체가 우변의 생성자 함수와 연결된 instance인지 판별
- `in`: property 존재 확인

## side effects

- `=`, `++`,`--`, `delete`

```js
var x
x = 1
console.log(x) // 1, 변수의 값이 변하므로 x를 참조하는 다른 코드에 영향을 줌

x++
console.log(x) // 2, x의 값이 재할당되어 마찬가지로 영향을 줌

var obj = { a: 1 }
delete o.a
console.log(o) // {}, obj 객체의 property가 삭제됨. 따라서 obj 객체를 참조하는 코드에 영향을 줌
```

## operator precedence

- 생략;

## operator associativity

- 생략;

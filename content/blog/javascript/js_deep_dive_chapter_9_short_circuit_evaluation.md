---
title: js_deep_dive_chapter_9_short_circuit_evaluation
date: 2022-06-16 23:06:68
category: javascript
draft: false
---

# Chapter 9. 타입 변환과 단축 평가(Short Circuit Evaluation)

## 타입 변환

- 의도적으로 변환하는 경우를 `explicit coercion`, `type casting`
  ```js
  var x = 10;

      	var str = x.toString();
      	console.log(typeof str, str); // string 10

      	console.log(typeof x, x); // number 10
      	```

- 의도와 상관 없이 암묵적으로 자동 변환 될 경우. `implicit coercion`, `type coercion`
  ```js
  var x = 10;

      	var str = x + '';
      	console.log(typeof str, str); // string 10

      	console.log(typeof x, x); // number 10
      	```

- 기존 원시값은 변경 불가능하기 때문에 `x + ''`를 평가하기 위해 `x`를 문자열 10으로 새로 생성하고 평가함. 이 때 평가에 사용된 문자열 10은 새로운 타입의 값을 생성하는데 사용하고 버리게 됨.

## 암묵적 타입 변환

```js
console.log('10' + 2) // '102'

console.log(5 * '10') // '50'

console.log(!0) // true

if (1) {
  console.log(true)
}
```

### 문자열 타입 변환

```js
1 + '2'
```

- `operand` 중 하나 이상의 문자열이 문자열 연결 연산자로 동작. 문자열 연결 연산자의 역할은 문자열 값을 만드는 것이기 때문에 `operand`는 문맥상 모두 문자열이여야함. 따라서 암묵적 타입 변환이 발생함

### 숫자 타입 변환

```js
console.log(1 - '1') // 0

console.log(1 * '10') // 10

console.log(1 / 'one') // NaN
```

- 산술 연산자는 숫자를 만들어내는 것. 따라서 모든 `operand`는 숫자여야함.
- 숫자 타입으로 변환이 불가능한 경우 평가결과는 `NaN`이 됨.
- `'1' > 0`과 같은 비교 연산자 또한 크기 비교이기 때문에 숫자타입으로 압묵적 타입 변환이 발생함
- `+`를 단항 연산자로 쓴 다면 이 또한 숫자 타입으로 암묵적 타입 변환이 발생함

#### 각종 타입 변환

```js
console.log(+'') // 0
console.log(+[]) // 0
console.log(+null) // 0
console.log(+false) // 0

console.log(+undefined) // NaN
console.log(+{}) // NaN
console.log(+[10, 20]) // NaN
console.log(+function() {}) // NaN

console.log(+Symbol()) // TypeError: Cannot convert a Symbol value to a number
```

### 불리언 타입 변환

```js
if ('') console.log('1')
if (true) console.log('2')
if (0) console.log('3')
if ('str') console.log('4')
if (null) console.log('5')

// 2 4
```

- 조건식의 평가 결과를 `Truthy`, `Falsy`로 구분함. `Truthy`는 `true`, `Falsy`는 `false`로 암묵적 타입 변환함.
- `Falsy` 값의 종류는 아래와 같음. 이 외에는 모두 `Truthy` - `false` - `undefined` - `null` - `0, -0` - `NaN` - `''(empty string)`

## 명시적 타입 변환

### 문자열 타입으로 변환

- `String` 생성자 함수를 `new` 키워드 없이 호출
- `Object.prototype.toString`
- 문자열 연결 연산자 (`+`)

### 숫자 타입으로 변환

- `Number` 생성자 함수를 `new` 키워드 없이 호출
- `parseInt`,`parseFloat`. 문자열만 숫자 타입으로 변환 가능한 함수
- `+` 단항 산술 연산자
- `*` 산술 연산자

### 불리언 타입으로 변환

- `Boolean` 생성자 함수를 `new` 키워드 없이 호출
- `!` 부정 논리 연산자 두번 사용 (`!!`)

## 단축평가

### 논리 연산자를 사용한 단축 평가

- `||`, `&&` 표현식의 평가 결과는 `boolean`이 아닐 수도 있음. 2개의 `operand` 중 한 쪽으로 평가됨.
  `js console.log('Cat' && 'Dog'); // 'Dog'`
- 첫 번째 `operand`인 `'Cat`은 `Truthy` 값이므로 `true`로 평가됨. 두번 째 `operand`인 `'Dog'` 또한 마찬가지이므로 평가 결과는 `true`가 됨. 이 때 연산의 결과를 결정한 `Dog`를 그대로 반환하게 됨
- 논리합/곱 연산자는 연산의 결과를 결정한 `operand`를 타입 변환 없이 그대로 반환함. 이를 단축평가`short-circuit-evaluation`이라고 함. 평가 중 결과가 확정된 경우 나머지 평가 과정을 생략하는 것을 의미함.

#### 객체를 가리키기를 기대하는 변수가 `null` 또는 `undefined`가 아닌지 확인하고 property를 참조할 때

- 변수의 값이 객체가 아닌 `null` 또는 `undefined`인 경우 참조시 `TypeError` 발생. `short-circuit-evaluation`을 사용하면 에러가 발생하지 않음
  `js var elem = null; var value = elem.value; // TypeError var value = elem && elem.value; console.log(value); // null`

#### 함수 매개변수에 기본 값을 설정할 때

- `short-circuit-evaluation`을 이용해 매개변수의 기본값을 설정하면 `undefined`으로 인한 에러를 방지할 수 있음
  ```js
  function getStringLength(str) {
  str = str || '';
  return str.length;
  }

      	console.log(getStringLength()); // 0
      	console.log(getStringLength('hi')); // 2

      	// es6
      	function getStringLength(str = '') {
      	  return str.length;
      	}

      	console.log(getStringLength()); // 0
      	console.log(getStringLength('hi')); // 2

      	```

### 옵셔널 체이닝 연산자

- `es11(ECMAScript2020)`에서 도입. 좌항의 `operand`가 `null` 혹은 `undefined`인 경우 `undefined`를 반환, 아닌 경우 우항의 프로퍼티 참조를 이어감
- `&&`의 연산자로 체크할 경우 아래와 같은 문제가 발생할 수 있음
  ```js
  var str = '';

      	// ''가 Falsy하기 때문에 좌항의 값이 그대로 반환됨.
      	var length = str && str.length;
      	console.log(length); // ''

      	// 좌항의 값이 Falsy하더라도 null 혹은 undefined이 아니기 때문에 참조를 이어갈 수 있음
      	var length = str?.length;
      	console.log(length);
      	```

### null 병합 연산자

- `es11(ECMAScript2020)`에서 도입. `nulllish coalescing`은 좌항의 `operand`가 `null` 혹은 `undefined`인 경우 우항의 `operand`를 반환, 그렇지 않다면 좌항을 반환.
- 변수에 기본값을 설정할 때 유용

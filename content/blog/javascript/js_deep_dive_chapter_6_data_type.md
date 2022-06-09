---
title: js_deep_dive_chapter_6_data_type
date: 2022-06-10 00:06:56
category: javascript
draft: false
---

# Chapter 6. Data Type

- es6에서는 7개의 data type (primitive + object/reference) 제공
  > es11에서 `BigInt`추가

## primitive type

`number, string, boolean, undefined, null, symbol`

## Object/Reference

객체, 함수, 배열 등

## number type

- `javascript`에서는 하나의 숫자 타입만이 존재. 모두 실수로 처리함
  - double precision floating point number
  ```js
  console.log(1 === 1.0) // true
  ```
- 아래 3가지 값도 표시 가능
  - `Infinity`, `-Infinity`, `NaN`
  ```js
    console.log(10 / 0); // Infinity
    console.log(10 / -0); // -Infinity
    console.log(1 \* 'string') // NaN
  ```

## string type

- `UTF-16` 의 집합
- single quote, double quote, backtick 모두 사용 가능
- primitive type이며 immutable value

## template literal

- es6에서 추가
- `multi-line string`, `expression interpolation`, `tagged template` 등 편리한 문자열 처리 기능 제공
- 런타임 시 일반 문자열로 변환되어 처리
- backtick을 이용해 표현

### multi-line

- 일반 문자열에서는 `escape sequence`를 통해서 개행, 들여쓰끼 등을 적용해야 하지만 `template literal`내에서는 사용하지 않아도 적용 됨

### expression

```js
const first = 'hello'
const last = 'world'
console.log(`${first} ${last}!!`) // hello world!!

console.log(`1 + 2 = ${1 + 2}`) // 1 + 2 = 3
```

## boolean type

- `true`, `false`

## undefined type

- `undefined`
- 변수 선언에 의해 확보된 메모리 공간이 처음 할당이 이뤄지기 전까지 빈 상태로 두지 않고, `undefined`로 초기화 함
- `javascript` 엔진이 변수를 초기화 할 때 사용하는 값
- 따라서 개발자가 의도적으로 할당하는 것은 본래 취지에 어긋나므로 권장하지 않음

## null type

- `null`
- 변수에 값이 없다는 것을 의도적으로 명시 `intentional absense`
- 변수가 참조하던 값을 더 이상 참조하지 않겠다는 것을 의미
- 유용하지 않은 변수는 변수의 스코프를 좁게 만들어서 재빨리 소멸시키는 편이 좋음

## symbol type

- `es6`에서 추가된 타입
- 다른 값과 중복되지 않은 유일무이한 값

```js
const key = Symbol('key')
console.log(typeof key) // symbol

const obj = {}

obj[key] = 'value'
console.log(obj[key]) // value
```

## object type

- `javascript`는 객체 기반 언어, 이루고 있는 거의 모든 것이 객체

## data type의 필요성

### data type에 의한 메모리 공간의 확보와 참조

- 값의 종류에 따라 정해진 크기의 메모리 공간을 확보

#### data type에 따라 확보되는 메모리 공간의 크기

- `ECMAScript` 사양은 `string`과 `number` 외에 데이터 타입의 크기를 명시적으로 규정하지 않음. 따라서 엔진 제조사의 구현에 따라 다를 수 있음
- 단 `number` 타입은 `double precision floating point number`로 명시. 이는 8바이트로 숫자를 표현함

### data type에 의한 값의 해석

- 예를 들어 `0100 0001`의 해석의 경우 `number`라면 65지만, `string`으로는 `A`이다. 따라서 올바른 해석을 위해 필요

## 동적 타이핑

- `static/strong type`의 언어는 변수를 선언할 때 data type을 사전에 선언해야함 `explicit type declaration`
- `javascript`는 선언 시 타입 선언은 하지 않음
- 선언이 아닌 할당에 의해 타입 추론, 또한 재할당에 의해 변할 수 있음 `dynamic typing`, `dynamic/weak type`
- 변수는 타입을 갖지 않는다. 값은 타입을 갖는다. 따라서 할당된 값에 의해 변수의 타입이 동적으로 결정된다 라는 표현이 적절

### trade off

- 개발자의 의도와는 상관 없이 엔진에 의해 자동적으로 변환되기도 함
- flexibility 함. 그러나 reliability는 떨어짐
- 이를 위해선
  - 무분별한 변수 남발은 금물
  - 변수의 스코프를 최대한 좁게하여 부작용 억제
  - 전역 변수는 최대한 자제
  - 상수를 이용한 변경 억제
  - 목적과 의미 파악을 위한 네이밍

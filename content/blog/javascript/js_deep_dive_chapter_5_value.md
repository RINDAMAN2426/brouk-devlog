---
title: js_deep_dive_chapter_5_value
date: 2022-06-01 18:06:59
category: javascript
draft: false
---

# Chapter 5. Value

## 값`Value`

`expression` 이 `evaluate` 된 결과.
메모리에 `bit` 의 나열로 저장.

```js
var sum = 10 + 20
```

저장되는 것은 `30` 즉 연산의 결과 값은 할당 이전에 `evaluate` 되어야 함

## Literal

사람이 이해할 수 있는 문자 또는 약속된 기호를 사용해 값을 생성하는 표기법`notation`

## Expression

`expression` 은 값으로 평가될 수 있는 `statement`. 즉, 표현식이 평가되면 새로운 값을 생성하거나 기존 값을 참조.

```js
var score = 100
```

이 코드 또한 리터럴 100은 값을 생성하므로 리터럴 그 자체로 표현식

## Statement

`statement`는 프로그램을 구성하는 기본 단위이자 최소 실행 단위

`token`이란 문법적으로 더 이상 나눌 수 없는 코드의 기본 요소

## Semicolon과 자동 삽입 기능

`semicolon`은 `statement`의 종료를 표현

`javascript`에서는 끝이라고 예측되는 지점에 자동으로 붙여주는 `Automatic Semicolon Insertion`이 암묵적으로 수행되기 때문에 생략이 가능

단, 개발자의 예측과 일치하지 않는 경우가 간혹 있음

## 표현식인 문과 아닌 문

표현식은 값으로 평가될 수 있으며, 표현식이 아니면 값으로 평가될 수 없음

예제 1

```js
var x; // 선언문은 표현식이 아님
var foo = var x; // SyntaxError: Unexpected token var
```

예제 2

```js
var x // 선언문
x = 100 // 할당문

var foo = (x = 100) // x = 100 은 할당문이면서 표현식임 따라서 이와 같은 문이 가능함
```

크롬 개발자 도구에서 표현식이 아닌 문을 실행할 때 출력되는 `undefined`는 완료 값`completion value`

표현식을 실행할 때는 평가된 값을 반환

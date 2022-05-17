---
title: js_deep_dive_chapter_4_variable
date: 2022-05-17 21:05:91
category: javascript
draft: false
---

Chapter 4. 변수(Variable)

## 변수(variable)란

- `javascript` 엔진이 코드를 `evaluation`해야함. 이를 위해선 `operator`와 `literal`의 의미를 알고 있어야 하며, `expression`의 의미를 `parsing`할 줄 알아야 함.
- 연산의 결과값을 사용하기 위해선 메모리에 저장이 필요. 이 때 `javascript`는 직접 메모리에 제어를 허용하지 않음. 허용한다 한들, 메모리의 주소는 매번 실행될 때마다 달라질 것이기 때문에 메모리 주소를 매번 변경할 수 없는 코드에서는 문제가 발생함.
- 특정 값을 저장할 메모리의 공간, 혹은 메모리 공간을 식별하기 위한 이름을 `Variable`이라고 함.
- `Array`, `Object` 같은 자료 구조를 이용한다면, 관련있는 여러 변수들을 그룹화할 수 있음
- 변수 이름은 `identifier` 라고도 함. 함수와 클래스 이름도 마찬가지
- `identifier`는 값이 저장된 memory address를 기억. `identifier`와 memory address 간의 매핑 관계도 저장되어야 함.
- declaration에 의해 `javascript engine`에게 `identifier`의 존재를 알림

## 변수 선언(variable declaration)

- 메모리의 공간을 `allocate`하고 `identifier`와 memory address를 name binding. 해당 공간은 `release`전까지는 사용할 수 없도록 보호됨

```js
var score
```

- 선언 이후 값의 할당이 없으면 `javascript engine`에 의해서 `undefined`가 할당됨. 이 때는 아래 두 단계가 실행됨 - 선언: 변수 이름을 등록해 `javascript engine`에게 변수의 존재를 알림 - 초기화: 메모리 공간을 확보하고, `undefined`를 할당해서 초기화
  > 변수 이름의 등록이란?
  >
  > - 모든 식별자는 `execution context`에 등록
  > - `javascript engine`이 코드를 평가, 실행하기 위한 환경을 제공. 실행 결과를 관리
  > - `identifier`와 `scope` 관리
  > - `key/value` 형태인 객체로 관리함
- `initialize`란 선언 이후의 최초 할당
- 초기화를 하지 않으면 다른 어플리케이션에서 사용하고 남은 `garbage value`가 남아 사용될 수 있음

## 선언의 실행 시점과 호이스팅

```js
console.log(score)
var score
```

- `javascript`는 `interpreter`에 의해 한 줄씩 순차 실행. 그러나 실제로 코드를 돌려보면 `ReferenceError`가 발생하지는 않음
- 변수 선언은 runtime 이전 단계에서 실행되기 때문
- `javascript engine`은 한 줄씩 순차적 실행에 앞서 `evaluate`를 수행. 모든 선언을 찾아 먼저 실행한 후 선언문을 제외하고 런타임에서 실행함.
- 선언문이 마치 끌어 올려진 것처럼 동작하는 이것을 `variable hoisting`이라고함

## 값의 할당

```js
var score // declaration
score = 80 // assignment

var score = 80 // declaration & assignment
```

- 한 줄인 syntax로 작성을 해도 실행은 `declaration`과 `assignment` 2개의 문으로 나누어서 실행함
- 앞서 얘기했듯이 `declaration`은 런타임 이전 수행, `assignment`는 런타임에 실행
- `declaration`에 `undefined`값을 메모리에 저장. `assignment` 시에 해당 값을 80으로 새롭게 저장하는 것이 아닌, 새로운 메모리 공간을 확보하고 그곳에 80을 저장하는 동작 방식.

```js
console.log(score) // ?

score = 80
var score

console.log(score) // ?
```

- 위의 코드의 결과는 `undefined, 80` 이다. `var score`가 먼저 `evaluate`단계에서 `undefined`로 할당. 따라서 첫 번째 로그에는 해당 값으로 할당. 그 다음 줄인 할당을 수행하고 그 다음줄인 선언은 생략. 따라서 마지막 로그는 80을 찍게 됨.

## 값의 재할당

```js
var score = 80 // declaration & assignment
score = 90 // re-assignment
```

- `var` 키워드로 선언한 변수는 재할당이 가능.
- 재할당이란 값을 버리고 새로운 값을 저장. 엄밀하게는 첫번째 선언, 할당문도 재할당을 수행하는 것.
- 재할당 또한 기존 메모리 주소의 값을 바꾸는 것이 아닌 새로운 메모리 공간을 확보하고 값을 저장하는 것.
- 따라서 첫 번째 줄에서 사용한 값인 `undefined`와 80은 어떠한 `identifier`와도 연결되지 않음. 이러한 값들은 `garbage collector`에 의해 메모리에서 `release`됨. `release`시점은 예측할 수 없음.

## 식별자의 네이밍 규칙

- 식별자는 comma를 이용해 하나의 statement에서 여러개 선언 가능. (권장x)
- 유니코드 문자 사용 가능(권장x)
- 예약어, 첫번째 숫자 사용 불가
- 일반적으론 변수나 함수는 camelCase를 사용. 생성자 함수나 클래스는 PascalCase를 사용.

---
title: Javascript(0) - Promise
date: 2020-04-07 12:04:37
category: javascript
draft: false
---

# Promise

`Promise`를 설명 하기에 앞 서 `Javascript`의 동작을 이해할 필요가 있습니다.\
`Javascript`는 `SingleThread`이므로 두 가지 작업을 동시에 진행 할 수 없습니다.\
따라서 `Javscript`는 비동기 처리를 가능하게 하도록 설계 되었습니다.

이러한 비동기 처리로 인하여 이것을 동기적으로 처리하기 위해 `Promise`가 생기게 되었습니다. \
`Promise`는 성공 혹은 실패에 대한 값을 핸들링 할 수 있게 해주는 기능을 하고 있습니다.

`Promise`가 리턴하는 성공과 실패를 이해하기 위해서 Promise를 조금 더 알아보도록 하겠습니다.

## States

프로미스는 생성되고 종료될 때 까지 3가지의 상태를 가지고 있습니다.

- Pending
- Fullfilled
- Rejected

`pending`상태는 프로미스의 초기 상태로서 `fullfilled`도 `rejected`도 아닌 상태 입니다. \
이 상태에서 어떠한 value를 가진 `fullfilled` 상태 혹은 에러가 발생하여 그 reason을 가지고 있는 `rejected` 상태로 될 수 있습니다.
pending 상태에서 처리가 끝나면 아래 코드처럼 `then()` 혹은 `catch()` 메소드를 통해 `value`나 `reason`에 대한 처리를 할 수 있게 됩니다. \
두 가지 메소드 모두 연결 될 때 `resolved` 혹은 `rejected` 된 경우에 호출 되므로 비동기 작업이 완료 되는 것과 관련하여 충돌이 없습니다.

<b>다음은 기초적인 Promise 코드입니다.</b>

```js
new Promise((resolve, reject) => {
  console.log('프로미스 실행')
  resolve()
})
  .then(() => {
    throw new Error('실패')

    console.log('실패 한 후')
  })
  .catch(() => {
    console.error('실패 되었습니다')
  })
  .then(() => {
    console.log('이전과 상관없이 실행합니다.')
  })
```

```console
프로미스 실행
실패 되었습니다.
이전과 상관없이 실행합니다.
```

위의 코드에서 `실패 한 후`는 에러가 발생된 후 `catch()`메소드로 연결되기 때문에 실행 되지 않는 것을 알 수 있습니다.

`Promise`를 어느 정도 이해하였으니 다음은 `async/await` 문법을 알아 보겠습니다. 추후에 기회가 된다면 이 문법에 대해 다시 공부할 예정이기에 간략하게 사용법만 알아보겠습니다.

## async/await

```js
async function foo() {
  try {
    const result = await first()
    const secondResult = await second(result)
    const thirdResult = await third(secondResult)
    console.log(`third is ${thirdResult}`)
  } catch (error) {
    failCallback(error)
  }
}
```

`Promise`객체를 리턴하는 각각의 first, second, third 메소드를 await를 통해서 동기적으로 처리하게 할 수 있는 문법입니다. \
 Arrow function 의 경우

```js
const function = async () => {....}
```

이러한 형태로 사용하시면 되겠습니다.

# Reference

- https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Using_promises
- https://joshua1988.github.io/web-development/javascript/promise-for-beginners/

---
title: function vs class
date: 2020-04-09 15:04:45
category: react
draft: false
---

# 함수형 컴포넌트와 클래스의 차이

최근 이런 저런 (예를 들면 이직면접...)의 상황에서 제 코드를 보시곤 \
가장 많이 물어보시던 질문 중 하나가 function과 class의 차이에 대해 물어보시곤 하셨는데... \
아마도 Hooks로만 짜여져있어서 그런 질문을 주셨던 것 같다.

사실 이유는 명쾌했다. 내가 React를 입문할 때는 19년 초였으니까.. 한창 hooks가 나오고 class를 function으로 바꾸던, 심지어 CRA Template마저도 function으로 나오고 있었기 때문에 함수형으로 짜는 걸 공부했기 때문이다.
그래도 차이점에 대해 잘 알고 있어야 하기 때문에 이렇게 정리를 하는 글을 써봅니다.

> 아래 내용은 [hackernoon](https://hackernoon.com/react-stateless-functional-components-nine-wins-you-might-have-overlooked-997b0d933dbc) 포스트의 내용 중 제 생각에 중요한 포인트만 적어놨습니다. 꼭 가서 읽어보시면 좋을 것 같습니다!

다들 하시다시피 React 뿐만아니라 javascript내에서 `this`가 주는 혼란은 참 골치 아프다는 걸 아실 겁니다. \
하지만 함수형에서는 `this` 키워드를 사용하지 않기 때문에 조금 더 props의 사용에 대해서 편해지실 수 있습니다.

아래 예시코드를 보면 더 잘 이해하실 수 있습니다.

```js
// class
class Hello extends React.Component {
  constructor(props) {
    super(props)
  }

  sayHello() {
    alert(`Hi ${this.props.name}`)
  }

  render() {
    return (
      <div>
        <a href="#" onClick={this.sayHello.bind(this)}>
          Hello
        </a>
      </div>
    )
  }
}
```

```js
// function
const Hello = ({ name }) => {
  const sayHello = () => alert(`Hi ${name}`)

  return (
    <div>
      <a href="#" onClick={sayHello}>
        Hello
      </a>
    </div>
  )
}
```

당연히 코드가 간결해진다는 것은 지겹도록 들어보셨을테니 넘어가고
내부 함수에 대해서 함수형 컴포넌트의 경우는 bind처리를 하지 않아도 되기 때문에 코드가 더 알아보기 쉽고 간결하다는 것을 아실 수 있습니다.

---

> 또 하나 명확하게 다른 점이 있습니다!

이 또한 `this`로 야기되는 문제입니다. \
`props`는 React에서 불변하는 값이지만 `this`의 경우 다를 수 있습니다.

이 내용에 관해서는 너무나도 잘 정리 되어 있는 글이 있기 때문에 \
아래 링크를 꼭 가셔서 읽어보시길 너무 나도 추천드립니다! \
[함수형 컴포넌트와 클래스](https://overreacted.io/ko/how-are-function-components-different-from-classes/) \
props와 this에 대해서 정말 잘 정리 되어 있기 때문에 꼭 읽어보시는 것을 추천드립니다!

---

React Hooks가 등장하며, 함수형 컴포넌트에서도 Life Cycle, Local State, Redux 등을 유연하게 대처할 수 있기 때문에, 또한 React에서도 Functional Component를 지향하고 있기 때문에 구지 불편한 this를 써가면서까지 class를 운용해야 할지는 의문점입니다.

# Reference

- https://hackernoon.com/react-stateless-functional-components-nine-wins-you-might-have-overlooked-997b0d933dbc
- https://overreacted.io/ko/how-are-function-components-different-from-classes/

리액트 컴포넌트는 [class component와 functional component](https://enhanced.kr/postviewer/118)로 나뉜다. 기본적으로 functional component를 쓰는 게 결과파일의 크기도 줄일 수 있고, 메모리도 덜 잡아먹기 때문에 좋지만, class component를 써야만 하는 상황이 있었다. 바로 [state](https://enhanced.kr/postviewer/11)를 써야 할 때나, [life cylce API](https://enhanced.kr/postviewer/39)가 필요할 때다. `setState` 함수와 life cycle API가 `React.Component`에 정의돼 있었기 때문이다.

React 16.8 버전부터는 **hook**이라고 하는 새로운 문법이 지원된다. 클래스 컴포넌트에서만 할 수 있었던 일들을 함수형 컴포넌트에서도 할 수 있도록 도와주는 문법이다. 이번 글에서는 hook에 대해 알아보는 시간을 갖도록 하자. 참고로, 이번 글은 다소 길다.



## Hooking

React의 hook이 아니더라도, 개발자라면 한 번쯤 hook이라는 단어를 어디선가 들어봤음직하다. 아직 무경력의 애송이지만 나 역시 [GitHub의 webhook](https://developer.github.com/webhooks/)에 대해 들어본 적이 있다. 일반적으로 [소프트웨어공학에서의 hooking](https://ko.wikipedia.org/wiki/%ED%9B%84%ED%82%B9)이란, 메시지나 이벤트 등을 가로채 변경하는 것을 의미한다.

리액트의 hooks 역시 비슷한 의미에서 붙여진 이름이다. 함수 컴포넌트가 실행되는 도중 hooking하여 부수효과를 일으키기 위한 API라는 뜻이다.



## React Hooks

### Basic Hooks

다음 3개만 알아도 당장 hook을 써보는 데는 지장이 없을 것이다. 다른 hook들은 차차 코딩하면서 필요해졌을 때 찾아보도록 하자...

#### 1. React.useState

`useState` 함수는 class component가 아니더라도, component에서 `state`를 쓸 수 있게 해준다. `useState`는 **초기 state 값**을 인자로 받아, **현재 state 값**과 **state의 값을 바꿀 수 있는 함수**를 리턴한다. 다음 예를 보자.

```javascript
import React, { useState } from 'react';

function Example() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}
```

여기서 `count`는 기존 문법의 `this.state.count`와 대응되고, `setCount(value)`는 `this.setState({ count: value })`와 같다고 볼 수 있다.

`useState`는 기존의 state 문법과는 달리, state 객체 전체를 다루지 않고 하나하나의 값을 다룬다. 따라서 여러 state를 다뤄야 한다면 다음처럼 `useState`를 여러번 써줘야 한다.

```javascript
const [count, setCount] = useState(0);
const [name, setName] = useState('');
```

#### 2. React.useEffect

React는 기본적으로 함수형 프로그래밍을 지향한다. 하지만, 항상 이상과 현실 사이에는 괴리가 존재하듯, 모든 컴포넌트가 함수형 프로그래밍의 원칙을 따를 수는 없었다. Ajax call을 통한 data fetching이나 subscription에 의한 state의 수정 등이 그 대표적 예로, 이런 동작들은 mount가 끝난 뒤에 하는 것이 안전하다. 따라서 React에서는 FP의 원칙대로 확그냥막그냥 리턴해서 부모 컴포넌트에게 후처리를 위임하기보다는 [lifecycle API](https://reactjs.org/docs/react-component.html#the-component-lifecycle)를 통해 부수효과를 감수하는 쪽을 택했다.

`useEffect`는 컴포넌트 함수에 이런 (side) effect를 가져오는 함수다. [React 도큐먼트](https://reactjs.org/docs/hooks-overview.html#effect-hook)에 따르면 이전에 부수효과를 제어하던 `componentDidMount`, `componentDidUpdate`, `componentWillUnmound`를 하나의 API로 합친 것이라고 봐도 된다.

> It serves the same purpose as `componentDidMount`, `componentDidUpdate`, and `componentWillUnmount` in React classes, but unified into a single API.

- `componentDidMount` + `componentDidUpdate` 대신하기

    예시를 먼저 보자.

    ```javascript
    import React, { useState, useEffect } from 'react';
    
    Example = () => {
      const [count, setCount] = useState(0);
    
      useEffect(() => {
        document.title = `You clicked ${count} times`;
      });
    
      return (
        <div>
          <p>You clicked {count} times</p>
          <button onClick={() => setCount(count + 1)}>
            Click me
          </button>
        </div>
      );
    }
    ```

    위 코드에서 `useEffect`는 콜백함수(= effect)를 하나 인자로 받는다. 이 콜백은 매 렌더링이 끝날 때마다 실행된다. 즉, `componentDidMount`와 `componentDidUpdate`를 합친 것과 같다.

- `componentDidUpdate` 대신하기

    특정 state가 업데이트 될 때만 호출하고 싶을 수 있다. 그럴 때는 다음처럼 두번 째 인자에 구독할 state들을 배열로 넣어주면 된다.

    ```javascript
    useEffect(() => {
      document.title = `You clicked ${count} times`;
    }, [count]);
    ```

- `componentDidMount` 대신하기

    그냥 최초 mount될 때만 실행시키고 싶으면 요로코롬 두번 째 인자에 빈 배열을 넣어주면 된다.

    ```javascript
    useEffect(() => {
      document.title = 'You haven\'t clicked yet';
    }, []);
    ```

- `componentWillUpdate` 대신하기 (a.k.a. [Cleanup](https://reactjs.org/docs/hooks-effect.html#effects-with-cleanup))

    `useEffect`의 첫번째 인자로 넣어준 콜백은 함수를 리턴할 수 있다. 이 때 리턴하는 함수는 **Cleanup 함수**라 부르며, 이 Cleanup 함수는 매번 effect가 끝난 뒤에 실행된다.

    ```javascript
    useEffect(() => {
      document.title = `You clicked ${count} times`;
      return () => setCount(0);
    });
    ```

    정확히 말해서, Cleanup은 `componentWillUpdate`와는 호출시점이 좀 다르다. Cleanup 쪽이 더 자주 불린다. 이는 unmount 시점이 아니더라도 후처리 로직이 필요하여 `componenetDidUpdate`에 작성해줘야 하는 경우가 있는데, [이걸 빼먹어서 버그가 종종 생기더라하는 페이스북 측의 경험에서 비롯된 설계](https://reactjs.org/docs/hooks-effect.html#explanation-why-effects-run-on-each-update)라고 한다.

#### 3. React.useContext

그냥 `Class.contextType`으로 `Context` 객체를 넣어주고, `this.context`로 받던 걸 함수형 컴포넌트에서도 쓸 수 있게 만들어놓은 API다.

```javascript
const value = useContext(MyContext);
```



### Additional Hooks

> The following Hooks are either variants of the basic ones from the previous section, or only needed for specific edge cases. Don’t stress about learning them up front.

킹갓 페이스북의 조언에 따라 난 스트레스 받지 않으려 한다. 궁금하다면 [문서](https://reactjs.org/docs/hooks-reference.html#additional-hooks)를 읽어보자.



## Rules of Hooks

Hook을 사용할 때 지켜야 하는 규칙이 2가지 있다.

1. React 안에서만 쓸 것

    state와 가상돔 렌더링 등이 React의 개념이기 때문에 당연하다. 그럼에도 불구하고 이런 규칙이 있는 이유는 hook 자체만으로 보면 그냥 자바스크립트 함수일 뿐이기 때문이다. Vue.js 같은 애들이랑 React hook을 같이 써서는 안된다.

2. top level에서만 쓸 것

    top level이 아닌 것의 예로는 반복문, 조건절, nested function 등이 있다. 왜 저런데서 쓰지 말라는걸까? 우리는 `setState`를 여러번 써도 알아서 다른 state를 만들어낸다는 것을 안다. 그런데 리액트가 여러 hook을 서로 구분하는 기준이 '각 hook의 실행순서'이란 사실을 알아야 한다.

    ```javascript
    import React from 'react';
    
    function Form() {
      const [name, setName] = React.useState('Mary'); // 1
    
      if (name !== 'Sejin') {
        React.useEffect(() => console.log('formData', name)); // 2: 규칙 위반!
      }
    
      const [surname, setSurname] = React.useState('Poppins'); // 3
    
      React.useEffect(() => console.log(name + ' ' + surname)); // 4
    
      return (
        <div>
          <p>{name} {surname}</p>
          <button onClick={() => setName('Sejin')}>Set name to Sejin!</button>
        </div>
      );
    };
    
    ReactDOM.render(
      <Form />,
      document.getElementById('root'),
    );
    ```

    위 예시를 처음 실행시키면 아주 잘 동작한다. 하지만 버튼을 클릭하는 순간 `name`이 'Sejin'으로 바뀌고, 조건절에서 쓰인 `useEffect`가 실행흐름에서 빠지게 된다. 즉,  3의 `useState` 대신, 2의 `useEffect`를 실행하고, 배열 대신 `undefined`를 받게 되고, `surname`을 못 찾아 죽는 것이다.



## Custom Hooks

> Custom Hooks are more of a convention than a feature. If a function’s name starts with ”`use`” and it calls other Hooks, we say it is a custom Hook.

커스텀 훅은 문법이 아니다. **빌트인 훅을 포함하는 로직을 재사용할 수 있도록 함수로 분리할 때 지켜야 할 컨벤션** 정도라고 볼 수 있다. 커스텀 훅이라고 부를 수 있는 것들은 다음의 조건을 따른다.

- 함수다.
- 내부에서 빌트인 훅을 호출한다. (내부에서 호출하므로 함수 외부와는 state가 격리된다.)
- built-in hook들과 마찬가지로 이름이 'use'로 시작한다. 이는 rules of hooks에 대한 위반여부를 확인하기 위함이다.

다음은 [공식 문서](https://reactjs.org/docs/hooks-overview.html#building-your-own-hooks)에 나와있는 예시다. 

```javascript
import React, { useState, useEffect } from 'react';

function useFriendStatus(friendID) {
  const [isOnline, setIsOnline] = useState(null);

  function handleStatusChange(status) {
    setIsOnline(status.isOnline);
  }

  useEffect(() => {
    ChatAPI.subscribeToFriendStatus(friendID, handleStatusChange);
    return () => {
      ChatAPI.unsubscribeFromFriendStatus(friendID, handleStatusChange);
    };
  });

  return isOnline;
}
```

이런 식으로 훅이 포함된 자주 쓰는 로직을 함수로 분리하고, 함수 이름 앞에 'use'를 붙여줘서 'hook이 타고 있어요'하고 알려주면 된다.



## 이제 Class Component는 레거시인가

일단 페이스북의 리액트 핵심 개발자 중 한 명인 Dan Abramov는 [자신의 블로그](https://medium.com/@dan_abramov/making-sense-of-react-hooks-fdbde8803889#f7df)에서 다음과 같은 말을 남겼다.

>  Hooks can cover all use cases for classes while providing more flexibility in extracting, testing, and reusing code

즉, hook이 등장한 이상 Class를 쓸 이유는 없는 것이다. Class component에 비해 hook을 사용하는 functional component가 갖는 이점은 다음과 같다.

- 보통 Class에 비해 함수가 조금이나마 더 가볍다. Class는 쓸 기능 안 쓸 기능 안 가리고 `React.Component`로부터 바리바리 상속받아 오기 때문에 번들파일의 크기도 커지고 로딩속도도 더 걸릴 수밖에 없다.
- 재사용성을 위해 component tree를 더럽히지 않아도 된다. 즉, 실제 component가 아닌, 공통요소만 갖는 HOC와 같은 컴포넌트를 만들지 않아도 된다는 뜻이다.
- 짜기도 쉽고 읽기도 쉽다. 솔직히 HOC나 render props 같은 방법은 알고 있어도 내가 애송이라 그런지 막상 적용하려면 엄두가 안 났다. 일단 컴포넌트 구조 자체부터 뜯어고쳐야 하니 부담스럽기도 했다. 하지만 hook은 간단히 함수(custom hook)로 빼서 갖다 쓰면 된다.
- [Class의 lifecycle API에 비해 실수할 여지가 적다](https://reactjs.org/docs/hooks-effect.html#explanation-why-effects-run-on-each-update).

이 외에도 이점이 더 있을 수 있다. 그리고 단점은 사실상 없는 셈이다.

이와 동시에 그는 다음의 말도 덧붙였다.

> We have no plans to deprecate classes. At Facebook we have tens of thousands of class components and, like you, we have no intention of rewriting them. 

대부분의 리액트 앱이 클래스 컴포넌트를 사용하고 있고, 컴포넌트가 한 두개가 아닌 마당에 이걸 다 바꾸라고 권고하는 건 에바참치니 deprecated시키지는 않겠다는 뜻이다. 그러니까 **옛날에 짜놓은 코드 굳이 hook 쓰도록 리팩토링하지도 말고, 앞으로 짜는 코드에 굳이 class component를 쓰지도 말자**.
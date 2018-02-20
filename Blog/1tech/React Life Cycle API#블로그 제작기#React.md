[블로그 제작기](http://enhanced.kr/postviewer/7) 시리즈의 글입니다.

React는 프론트엔드 라이브러리이므로 백엔드 쪽에 전혀 관여하지 못한다. 말인즉슨, React는 요청에 따라 다른 뷰를 보여주기 위해 html 페이지를 통째로 갈아치우는 게 아니라 변화가 필요한 부분만을 갈아끼운다는 뜻이다. 이 작업을 **렌더링(rendering)**이라 부르며, 하나의 html 파일만을 이용하여 모든 페이지를 보여주는 웹 어플리케이션을 **SPA(Single Page Application)**이라 부른다.


| <img src="https://raw.githubusercontent.com/42deSix/Images/master/hulk-buster.gif" width="80%"/> |
| :----------------------------------------------------------: |
| 리액트는 이런 식으로 필요한 부분만 갈아끼워(렌더링하여) 사용한다. |

따라서 리액트를 잘 다루기 위해서는 각 컴포넌트 및 그 안의 엘리먼트들이 렌더링되는 타이밍을 정확히 알고 있어야 한다. 그렇지 않으면 앱이 조금만 복잡해져도 디버깅이 거의 불가능한 수준에 이른다. 컴포넌트의 렌더링 순서 및 타이밍을 **React Life Cycle**이라 하고(안드로이드의 라이프 사이클이랑 비슷한 맥락이다) React에서는 이를 제어할 수 있는 메소드들을 제공하는데 이걸 **React Life Cycle API**라 한다. 지금부터 알아보도록 하자.





## React.Component

React Life Cycle API method들은 `React` 모듈의 `Component` 추상클래스 안에 추상메소드로서 존재한다. 모든 리액트 컴포넌트는 `React.Component`를 상속받아 만들어지며 그 안에는 필수적으로 `render`함수가 존재하여 컴포넌트의 기본적인 구조를 만든다. 그리고 필요에 따라 렌더링을 제어하기 위해 적절한 Life Cycle API 메소드를 호출한다. 다음은 기본적인 React Component의 모습이다.

```javascript
import React, {Component} from 'react';

class VerseStarter extends React.Component {  
  render() {
    return (
      <h1>
        Unseen or heard. A king with words.
      </h1>
    )
  }
};
```

뒤에서 설명할 Life Cycle API 함수들 외에 `setState()`와 `forceUpdate()` 함수도 있다. 이 글에서는 설명하지 않겠다.





## React Life Cycle API Methods

React Life Cycle API 함수들은 유저가 직접 호출하는 게 아니라 특정한 상황이 되면 자동으로 호출된다. 이 '상황'은 mount를 기준으로 하여 크게 4가지로 분류된다. mount란, React의 [가상돔](http://enhanced.kr/postviewer/27#virtualdom)에 있던 요소들이 실제 DOM으로 넘어가 렌더링되는 것을 의미한다. ([참고](https://stackoverflow.com/questions/31556450/what-is-mounting-in-react-js))

| <img src="https://raw.githubusercontent.com/42deSix/Images/master/react-rendering2.png" width="80%"/> |
| :----------------------------------------------------------: |
|     이미지 출처: https://d2.naver.com/helloworld/9297403     |



### 1. 컴포넌트 생성시 (Mounting)

#### 1-1. constructor(props)

말 그대로 생성자다. 컴포넌트가 새로 만들어졌을 때 가장 먼저 호출되는 함수다. `props`를 인자로 받아 `super(props)`를 호출하는데, 이 작업을 거치지 않으면 `this.props`가 `undefined` 상태가 되기 때문에 (사견이지만) props를 안 쓰더라도 `super(props)`를 호출해주는 게 좋겠다.

#### 1-2. ~~UNSAFE_componentWillMount()~~

컴포넌트가 마운트 되기 직전에 호출되는 함수다. 이 블로그를 짜면서 느꼈던 거지만 이 함수는 `constructor`와 `componentDidMount`에 역할을 뺏겨서 도대체가 쓸 일이 없는 애매한 함수다. 게다가 대문자로 눈에 딱 띄게 박아놓은 저 UNSAFE에서도 느껴지듯 사용할 때는 조심해야 한다. 렌더링 전이기 때문에 props나 state값을 잘못 바꿨다간 무한루프에 빠져버리는 수가 있다.

version 16.3부터 deprecated되니 쓰지 않도록 하자.

#### 1-3. render()

XML을 return한다. 

#### 1-4. componentDidMount()

컴포넌트가 마운트 되고 나면 바로 호출된다. 주로 외부로부터 받은 Promise를 처리하기 위해 많이 사용한다.



### 2. 컴포넌트 변경시 (Updating)

#### 2-1. ~~UNSAFE_componentWillReceiveProps(nextProps)~~ 

역시 16.3 버전에 들어오면서 deprecated된 함수다. 

props 변경이 있을 때 이전의 props를 this.props에, 변경될 props를 nextProps에 담아둔 채로 호출된다.

#### 2-1'. getDrivedStateFromProps(nextProps, prevState)

`componentWillReceiveProps` 대신 이 친구를 쓰면 된다. 함수가 변경되면서 사용법도 약간 달라졌다. 바뀐 props로 할 일이 사실상 state의 업데이트밖에 없다고 생각한 모양이다. 바꾸고 싶은 state값을 객체로 만들어 리턴하면 된다.

[혹자](https://github.com/torvalds)는 "Talk is cheap, show me the code"라 했으니, 코드를 보고 이해하자.

```javascript
static getDerivedStateFromProps(nextProps, prevState) {
  if (nextProps.value !== prevState.value) {
    return { value: nextProps.value };
  }
  return null;
}
```

#### 2-2. shouldComponentUpdate(nextProps, nextState)

최적화 작업을 위해 사용한다. 기본적으로 props 값에 변화가 생기면 리렌더링 작업이 주루룩 진행되는데, props의 값은 변경했지만 바로 뒤에 더 변경할 props가 남아있거나 하여 렌더링을 하지 않아도 되는 경우가 있다. 그런 경우 false를 return하게 하여 리렌더링을 막을 수 있다.

```javascript
shouldComponentUpdate(nextProps, nextState) {
  return this.props.loading !== nextProps.loading;	// 보통 이런 식으로 많이 쓴다.
}
```



#### ~~2-3. UNSAFE_componentWillUpdate(nextProps, nextState)~~

~~아니 뭐 이렇게 deprecated 된 게 많아..~~ 



#### 2-4. getSnapshotBeforeUpdate(prevProps, prevState)

렌더링 직전에 호출되며 `this.props`와 `this.state`는 이미 바뀌어 있다. 마지막으로 렌더링 되기 전에 스냅샷을 만들어 두는 데 쓴다. 리턴한 값이 스냅샷이 되며 스냅샷은 후술할 `componentDidUpdate`에서 세 번째 파라미터로 받을 수 있다.

쓸 일이 별로 없어보이는데 [세상에나, 공식문서에서 쓸 일이 별로 없다고 인정했다](https://reactjs.org/blog/2018/03/27/update-on-async-rendering.html#new-lifecycle-getsnapshotbeforeupdate).



#### 2-5. componentDidUpdate(prevProps, prevState, snapshot)

렌더링 직후에 호출된다. `getSnapshotBeforeUpdate`와 연계해서 다음처럼 쓴다.

```javascript
getSnapshotBeforeUpdate(prevProps, prevState) {
  // Are we adding new items to the list?
  // Capture the scroll position so we can adjust scroll later.
  if (prevProps.list.length < this.props.list.length) {
    const list = this.listRef.current;
    return list.scrollHeight - list.scrollTop;
  }
  return null;
}

componentDidUpdate(prevProps, prevState, snapshot) {
  // If we have a snapshot value, we've just added new items.
  // Adjust scroll so these new items don't push the old ones out of view.
  // (snapshot here is the value returned from getSnapshotBeforeUpdate)
  if (snapshot !== null) {
    const list = this.listRef.current;
    list.scrollTop = list.scrollHeight - snapshot;
  }
}
```



### 3. 컴포넌트 제거시 (Unmounting)

#### 3-1. componentWillUnmount

언마운트될 때 호출되는 API는 얘 하나 뿐이다. 이벤트, setTimeout, 외부 라이브러리 인스턴스 등을 제거한다.

 

### 4. (Error Handling)

#### 4-1. componentDidCatch(error, info)

렌더링 과정에서 에러가 발생하면 호출된다고 한다. 표준문서에 따르면 이 함수에 대해 자세히 알고 싶으면 [여기](https://reactjs.org/blog/2017/07/26/error-handling-in-react-16.html)를 참조하라고 한다.
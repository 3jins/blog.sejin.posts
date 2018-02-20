[블로그 제작기](http://enhanced.kr/postviewer/7) 시리즈의 글입니다.

[공식 문서](https://reactjs.org/docs/react-component.html)에서 어떻게 설명하고 있는지 먼저 보자.

>   [Components](https://reactjs.org/docs/components-and-props.html) let you split the UI into independent, reusable pieces, and think about each piece in isolation. `React.Component` is provided by [`React`](https://reactjs.org/docs/react-api.html).

즉, UI를 독립적인 여러 모듈로 쪼갠 게 컴포넌트다. 다음 코드를 보자.

```react
import React, {Component} from 'react';

class VerseStarter extends React.Component {
  render() {
    return (
      <h1>
        Yo, listen to ma verse!
        {/* 이봐, 내 벌스를 들어봐!*/}
      </h1>
    )
  }
};

class FirstVerse extends React.Component {
  render() {
    return (
      <p>나는 힙찔이가 이니에요우</p>
    )
  }
};

class SecondVerse extends React.Component {
  render() {
    return (
      <p>드립 치려고 했는데 생각이 안 난다.</p>
    )
  }
};

class App extends React.Component {
  render() {
    return (
      <div>
        <VerseStarter/>
        <FirstVerse/>
        <SecondVerse/>
      </div>
    )
  }
}

ReactDOM.render(<App/>, document.getElementById("root"));
```

위의 코드에서 `VerseStarter`, `FirstVerse`, `SecondVerse`, 그리고 `App`이 모두 컴포넌트다. 위 코드가 표현하고자 한 UI의 코드는 다음과 동일하다.

```html
<div>
  <h1>
    Yo, listen to ma verse!
    {/* 이봐, 내 벌스를 들어봐!*/}
  </h1>
  <p>나는 힙찔이가 이니에요우</p>
  <p>드립 치려고 했는데 생각이 안 난다.</p>
</div>
```

이걸 독립적인 세개의 객체와 그 객체들을 로드하는 다른 객체, 이렇게 4개의 객체로 분리했을 때 그 객체 각각을 컴포넌트라 한다.

`React.Component`는 [`render`](https://reactjs.org/docs/react-component.html#render)라는 함수를 갖고 있다. 현재 객체에서의 [`props`와 `state`](#props-&-state)를 검사하고 DOM 같은 애들을 반환한다.

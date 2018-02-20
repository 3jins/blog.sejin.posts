[블로그 제작기](http://enhanced.kr/postviewer/5) 시리즈의 글입니다.

리액트 어플리케이션에서 컴포넌트간에 데이터를 전달하기 위해서는 `props` 또는 `state`를 써야한다. 둘은 혼동하기 쉬워서, 처음 리액트에 입문한 사람들이 전체적인 개념 잡고 코드 짜기 시작하다가 나중에 잊어먹고 다시 헷갈려하곤 한다. ~~내가 그랬다는 것은 절대 아니다.~~ 



## props

먼저 `props`에 대해서 알아보도록 하자.

### props의 특징

- `props`는 상위 컴포넌트로부터 내려온다.

    미개한 하위 컴포넌트는 상위 컴포넌트로부터 props를 마치 함수의 parameter처럼 받아서 사용한다.

- `props`는 수정할 수 없다.

    미개한 하위 컴포넌트는 상위 컴포넌트께서 하사하신 props를 수정할 수 없다. 구버전에서는 가능했다고 하나 페이스북께서 노하셔서 이 기능을 막아버렸다고 전해진다.

    장난스럽게 썼지만 사실 이건 **리액트 컴포넌트가 함수형 프로그래밍을 표방한다**는 중요한 사실을 담고 있는 내용이다. [state를 사용하지 않는 컴포넌트라면 클래스 구조보다는 함수형 컴포넌트로 만드는 게 좋다](https://enhanced.kr/postviewer/118)는 내용과도 일맥상통한다.

### 용례

```javascript
import React from 'react';
import ReactDOM from 'react-dom';

class FirstVerse extends Component {
  render() {
    return (
      <p>나는 {this.props.word}야</p>
    )
  }
};

class App extends Component {
  render() {
    const words=['힙찔이', '애송이', '백수'];
    const idx = Math.floor(Math.random() * 3);
    return (
      <div>
        <p>[verse 1]</p>
        <FirstVerse word={words[idx]}/>
      </div>
    )
  }
}

ReactDOM.render(<App/>, document.getElementById("root"));
```

Verse1 컴포넌트를 호출할 때마다 스스로를 랜덤하게 힙찔이, 애송이, 백수 중 하나라고 소개하는 벌스가 출력된다.

참고로, 여기서는 뒤에 설명할 `state`의 예제와 구조를 유사하게 만들기 위해 클래스 문법을 사용했지만, `FirstVerse`처럼 `state`가 없는 컴포넌트는 사실 클래스 형태보다는 함수 형태로 구현하는 것이 유리하다. 



## state

이제 `state`에 대해 알아보도록 하자. React를 쓴다면 앞으로 `state`에 대해서는 지겹도록 듣게 될 것이므로 개념을 잘 잡아두도록 하자.

### state의 특징

- `state`는 각 컴포넌트에서 만들어진다.

    자바 클래스의 멤버변수와 비슷한 개념으로 보면 된다. 자바 클래스에서 멤버변수는 직접 고유값을 갖기도 했지만(`private String verse1 = "나는 힙찔이야";`), 생성자에서 받아온 매개변수로 초기화하기도 했다(`this.verse1 = verse1;`). 마찬가지로, `state` 역시 직접 선언하거나, 또는 `props`의 값을 대입해 초기화할 수 있다.

- `state`는 변경 가능하다.

    단, 직접 변경해선 안 된다. 반드시 `setState` 함수를 써서 변경해줘야 한다. 그 이유는...

- `state`가 변경되면 컴포넌트를 다시 렌더링한다.

    물론, 리액트는 마법이 아니기 때문에, `this.state = anotherValue;`와 같은 코드가 리렌더링을 유발하지는 않는다. `setState`를 써서 변경했을 때만 리렌더링이 이루어진다. 따라서 **`state` 값을 바꿀 때에는 반드시 `setState`를 써서 변경**해야 한다.

### 사용법

```javascript
import React, {Component} from 'react';
import ReactDOM from 'react-dom';

class FirstVerse extends Component {
  constructor(props) {
    super(props);
    this.state = {
      word: props.words[0],
    };
  }
  
  changeWord(words) {
    const idx = Math.floor(Math.random() * 3);
    this.setState({
      word: words[idx],
    });
  }
  
  render() {
    setTimeout(() => this.changeWord(this.props.words), 1000);
    return (
      <p>나는 {this.state.word}야</p>
    );
  }
};

class App extends Component {
  render() {
   	const words=['힙찔이', '애송이', '백수'];
    return (
      <div>
        <p>[verse 1]</p>
        <FirstVerse words={words}/>
      </div>
    );
  }
}

ReactDOM.render(<App/>, document.getElementById('root'));
```

`constructor`는 [React 생명주기](https://enhanced.kr/postviewer/39)에서 가장 먼저 실행되는 함수다. 여기서 `state` 값을 초기화한다.

이후 `render()`에서 매초 `setState`를 사용하여 랜덤하게 verse에 사용할 word를 바꿔준다. 그 때마다 페이지는 리렌더링되고 '힙찔이', '애송이', '백수' 중 하나가 매초 렌더링된다.
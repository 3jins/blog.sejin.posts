[블로그 제작기](http://enhanced.kr/postviewer/7) 시리즈의 글입니다.

리액트의 특징은 보통 크게 3가지(Just the UI, Virtual DOM, Data Flow)로 나누어 설명한다. 아마 [리액트 공식 홈페이지](http://reactjs.org)에서 예전에 그렇게 설명해두었던 듯하다만 아무리 찾아도 저런 표현이 없었다. 어쨌든 널리 쓰이는 표현들이고 LGTM하므로 이 글에서는 그 표현을 그대로 차용하겠다. 그리고 거기에 하나의 특징을 더 덧붙여 설명하고자 한다.



## Library (O), Framework (X)

React의 선배격인 Backbone.js와 Angular.js는 프레임워크인데 반해 React는 라이브러리다. 프레임워크라 함은 틀이 완전히 잡혀져 있어서 그 틀을 벗어나면 코딩이 안되는 도구를 말하며, 프레임워크는 한 프로젝트당 하나만 사용할 수 있다.

대표적인 프레임워크로는 Java 진영의 Spring이나 Python 진영의 Django를 들 수 있다. Spring은 안 써봤으니 넘어가고 Django를 예로 들면 urls.py를 만들어야만 경로 관리가 되고 models.py를 만들어야만 DB와 연결할 수 있고 settings.py를 만들어야만 환경변수 등을 세팅할 수 있는 등 강제성이 크다.

반면, 라이브러리는 그 사용이 자유롭다. 가져다놓고 필요한 함수만 필요할 때 맘대로 쓰면 된다. 또한, 한 프로젝트 내에서 여러 라이브러리를 사용해도 되고 심지어 프레임워크와 라이브러리를 같이 써도 무방하다. 만화 원피스의 세계관에 비유하자면, 프레임워크는 일단 먹었으면 좋든 싫든 그 특징이 무조건 나타나고 둘 이상을 먹을 수 없는 악마의 열매와 같고, 라이브러리는 여럿을 배워도 되고 배워놓고 쓰든 말든 자유인 검술, 격투술, 패기, 육식 등에 해당한다. (원피스는 명작이다. 안 봤으면 한 번쯤 꼭 보자.)



## Just the UI

이 특징은 앞서 말한 라이브러리로서의 특징을 어느 정도 포함한다. React는 UI 외에는 전혀 관심이 없는, 철저하게 프론트엔드만을 위한 라이브러리다. React 자체만으로는 서버를 구축하거나 DBMS와 연결할 수 없다(물론 순수 Javascript로 Express나 Koa 같은 백엔드 프레임워크를 손수 구현한다면 되긴 하겠다). 따라서 가볍고 유연하게 사용할 수 있지만 그만큼 React를 쓰기 위해 주변의 다른 기술을 공부하기 위한 러닝커브가 상당히 높아지게 된다.



## Virtual DOM

React의 가장 큰 특징이다. ~~Vue.js도 가상돔 쓰는 건 함정.~~ 자바스크립트가 DOM에서 10개의 element를 수정한다고 하자. 그럼 내부적으로 자바스크립트는 DOM을 10번씩 수정하고 렌더링하는 과정을 거친다. (어떤 과정에 의해 수정되는지는 [Velopert님의 블로그 게시물](https://velopert.com/3236)을 참조하기 바란다.) 반면 가상돔을 사용한다면 10개 element의 수정 내역을 모아서 한 번에 적용하고 이를 한 번만 렌더링한다. 

### JSX

React에서는 Virtual DOM 객체를 직관적으로 표현하기 위해 JSX라는 문법을 사용한다...고들 하는데 사실 정확히는 Babel에서 JSX를 사용한다. ~~React는 사실 X신이라 혼자서 저런 문법을 컴파일하지 못한다.~~ 

```react
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

class

class App extends React.Component {
  render() {
    return (
      <div>
        <VerseStarter/>
      </div>
    )
  }
}

ReactDOM.render(<App/>, document.getElementById("root"));
```

React에서는 위와 같은 형태로 JSX를 사용한다. class와 extends는 ES6에서 ~~객체지향언어인 것처럼 흉내내려고~~ 추가한 문법이므로 생소할 수도 있다.

중요한 건 `return` 뒤의 html 같이 생긴 코드인데 걔네가 JSX이다. 저 문법이 Babel이라는 모듈에 의해 재해석되면 `React.createElement()`라는 Javascript 객체를 반환하는 React 라이브러리의 자바스크립트 함수 형태로 변환된다([참고](https://reactjs.org/docs/introducing-jsx.html#jsx-represents-objects)).

자세한 문법은 [이 블로그](https://velopert.com/867)나 [표준문서](https://reactjs.org/docs/introducing-jsx.html)를 참고하자.



## Data Flow

[누군가](https://poqw.github.io)가 말하길 프로그램을 만드는 데 있어서 데이터의 흐름을 완벽히 통제하는 것은 매우 중요한 일이라 했다.

양방향 데이터 바인딩을 쓰는 Angular.js와는 다르게 React는 단방향 데이터 바인딩만을 사용한다. 이로 인해 코드의 간결성을 조금 손해보는 대신 대규모 애플리케이션이 되었을 때 데이터의 흐름을 파악하는 것이 용이하다고 한다~~는데 Angular.js 안써봐서 양방향 데이터 바인딩이 뭔지 모른다~~.
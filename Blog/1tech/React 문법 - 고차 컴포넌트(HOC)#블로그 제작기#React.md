세부분야에 상관없이 소프트웨어 개발에는 DRY(Don't Repeat Yourself)라는 원칙이 있다. '같은 코드 여러 번 짜기 귀찮으니까 반복되는 건 한 번만 짜놓고 필요할 때 갖다 쓰라'는 원칙이다. 

리액트에서도 물론 같은 코드가 반복되는 경우가 있다. 특히 비슷한 생김새의 컴포넌트를 여러 개 만들어야 할 때, JSX를 통째로 반복해야 하기 때문에 반복이 극심하다. 이번 글에서 설명할 고차 컴포넌트(HOC, High Order Component)는 DRY한 코드를 짜기 위한 해결책이 될 수 있다.



## 고차 컴포넌트 (HOC, High Order Component)

[리액트는 함수형 프로그래밍을 지향](https://reactjs.org/docs/design-principles.html)하는 라이브러리라고 한다. 이런 특징은 곳곳에서 나타나는데 [컴포넌트가 클래스일 이유가 없을 때는 함수 형태로 만들 수 있다](https://enhanced.kr/postviewer/118)는 것이 그 예가 되겠다. 

고차 컴포넌트 역시 마찬가지로 함수형 프로그래밍의 철학을 반영하려는 개념이다. 함수형 프로그래밍에는 **고차함수**라는 개념이 있다. 별 건 아니고, 함수가 다른 함수를 인자로 받는 걸 가리키는 말이다. (사실 우리 모두는 고등학교 1학년 2학기 때 $f(f(x))$ 같은 수식과 함께 고차 함수에 대해 배운 적이 있다.) 고차 컴포넌트는 이 개념을 유사하게 사용하여, **다른 컴포넌트를 props로 받는다**. 고차 컴포넌트는 반복되는 부분을 담고, 매번 바뀌는 부분은 props로 받는다. 

### 코드, 코드를 보자!

코드를 보면서 이해하는 게 가장 빠를 것이다. 간단한 예시를 통해 이해해보도록 하자.

```javascript
import React from 'react';
import ReactDOM from 'react-dom';

const FirstVerse = props => (
  <div className="verse">
    <h3>[Verse 1]</h3>
    <p>나는 {props.word}야</p>
  </div>
);

const SecondVerse = props => (
  <div className="verse">
    <h3>[Verse 2]</h3>
    <p>나는 {props.word}야</p>
  </div>
);

const ThirdVerse = props => (
  <div className="verse">
    <h3>[Verse 3]</h3>
    <p>나는 {props.word}야</p>
  </div>
);

const Hook = props => (
  <div className="verse">
    <h3>[Hook]</h3>
    <p>훅 훅 훅</p>
  </div>
);

const App = () => {
  const words = ['힙찔이', '애송이', '백수'];
  return (
    <div>
      <FirstVerse word={words[0]} />
      <Hook />
      <SecondVerse word={words[1]} />
      <Hook />
      <ThirdVerse word={words[2]} />
      <Hook />
      <Hook />
    </div>
  );
};

ReactDOM.render(<App />, document.getElementById("root"));
```

대충 위와 같이 개똥같은 가사를 보여주는 리액트 앱이 있다고 하자. `App`에서 사용하는 컴포넌트는 `FirstVerse`, `SecondVerse`, `ThirdVerse`, `Hook`의 4개다. 자세히 보면 4개의 컴포넌트가 모두 모양새가 비슷함을 알 수 있다. 이 유사한 부분을 HOC로 만들어서 빼버리도록 하자.

```javascript
/* withVerse */
import React from 'react';

const withVerse = (VerseBody, verseTitle) => props => (
  <div className="verse">
    <h3>{verseTitle}</h3>
    <VerseBody {...props} />
  </div>
);

export default withVerse;
```

고차 컴포넌트는 위와 같이 생겼다. (`with~`이라는 이름은 [리액트 공식문서](https://reactjs.org/docs/higher-order-components.html)에서 예시로 든 걸 따라한 것이다. `~Wrapper` 형태로 쓰는 사람들도 많이 있는 것 같으니 팀의 컨벤션을 따르면 되겠다.) 별 거 없다. 컴포넌트(`VerseBody`)와 다른 값들(`verseTitle`)을 인자로 받아서 컴포넌트를 반환하는 함수일 뿐이다. 이제 각 벌스 컴포넌트에서 다음처럼 `withVerse` HOC를 사용해주면 된다.

```javascript
import React from 'react';
import withVerse from './withVerse';

const verseTitle = '[Verse 1]';
const FirstVerse = props => <p>나는 {props.word}야</p>;

export default withVerse(FirstVerse, verseTitle);
```

```javascript
import React from 'react';
import withVerse from './withVerse';

const verseTitle = '[Hook]';
const Hook = props => <p>훅 훅 훅</p>;

export default withVerse(Hook, verseTitle);
```


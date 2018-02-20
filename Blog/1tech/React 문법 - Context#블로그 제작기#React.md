React의 핵심은 **컴포넌트**다. 컴포넌트는 렌더링 결과물이 될 **가상돔**, 가상돔을 만드는데 쓰이는 재료인 **props**, 가상돔을 제어하기 위한 상태값인 **state**로 이루어진다고 볼 수 있다. 이 중 state는 각 컴포넌트의 상태를 의미하기 때문에 체계적인 관리가 필요하다. 다음과 같은 컴포넌트 구조가 있다고 하자.

<img src="https://raw.githubusercontent.com/3jins/Images/master/tree.png" width="40%"/>

F 컴포넌트의 상태변화가 C 컴포넌트에도 영향을 미쳐야 한다면 기존의 리액트에서는 F에서 A로부터 전달받은 함수를 이용해서 A 컨텍스트의 state를 수정하고 이 state를 A에서 B로, 다시 B에서 C로 props를 이용해 전달해줘야 했다. 이 작업을 좀 더 간단하게 만들기 위해 flux 구조를 적용하거나, 이를 좀 더 편하게 도와주는 redux 라이브러리, decorator 패턴을 활용하는 mobx 등 여러 방법이 고안되었다. [리액트 공식 문서](https://reactjs.org/docs/context.html#before-you-use-context)에서는 이 문제를 제어의 역전을 이용해서 해결하는 방법도 나와있다.

React 16.3에 들어오면서 state의 관리를 위한 새로운 문법이 등장했다(정확히는 [실험적으로 만들어뒀던 Context API](https://reactjs.org/docs/legacy-context.html)가 있었는데 이를 리뉴얼했다고 한다). 바로 Redux에서 사용하던 store와 유사한 개념이자 이번 글의 주제인 **Context**다. 아마 개발 방식에 큰 변화를 가져올 것으로 보이는 이 개념에 대해 알아보는 시간을 갖도록 하자.



## Context API란

아주 옛날(2018년 초), 지금도 애송이지만 그보다도 더 애송이라 리액트와 리덕스도 제대로 구분 못하던 시절에 [스택오버플로우에 올린 질문](https://stackoverflow.com/questions/48899856/using-global-variables-in-redux-app)이 하나 있다. 리액트에서 전역변수를 쓰기 위해 어떤 방법을 쓰면 좋겠냐는 질문이다. 여기서 나는 당시 experimental feature였던 `Context`를 언급하고 있다.

React의 `Context`는 같은 문맥(context) 안에 있는 모든 컴포넌트가 접근할 수 있는 state 정도로 이해하면 된다. 최상위 컴포넌트로 쓰이는 `<App />`에 context를 걸어주면 해당 context는 전역 state가 되는 식이다.



## Syntax

`Context` 객체에는 `Provider`와 `Consumer`가 들어있다. 우리는 네 가지만 알면 된다 : `Context` 객체를 생성하는 `createContext`, `Context` 객체 안에 들어 있는 `Provier`와 `Consumer`, 그리고 `contextType`.

### React.createContext

Context 객체를 생성해주는 함수다. 다음과 같이 사용한다.

```javascript
const MyContext = React.createContext(defaultValue);
```

실제로 사용할 때는 context를 별도의 파일로 만들어서 생성하고 export시킨다. 다음은 예시다.

```javascript
import React from 'react';

const darkTheme = {
  foreground: '#ffffff',
  background: '#222222',
};

export default React.createContext(
  darkTheme // default value
);
```

### Class.contextType

다음과 같은 방법으로 Component가 `Context`를 구독하도록 만들 수 있다. 해당 클래스 안에서 `this.context`를 통해 `Context` 객체의 값에 접근할 수 있다.

```javascript
class MyClass extends React.Component {
  componentDidMount() {
    const value = this.context
  }
}
MyClass.contextType = MyContext;
```

실제라면 다음처럼 쓸 수 있다.

```javascript
// ThemeContext.js

import React from 'react';

export const ThemeContext = React.createContext({
  foreground: '#ffffff',
  background: '#222222',
});
```

```javascript
// ThemedButton.js

import React from 'react';
import ThemeContext from './theme-context';

class ThemedButton extends React.Component {
  render() {
    let theme = this.context;
    return <button style={{backgroundColor: theme.background}} />;
  }
}
ThemedButton.contextType = ThemeContext;

export default ThemedButton;
```

### Context.Provider

`contextType`을 이용한 구독으로는 1:1로밖에 매칭되지 않는다. 한 컴포넌트가 여러 `Context`를 구독하게 하려면 `Context.Provider`를 사용해야 한다.

```xml
<MyContext.Provider value={/* some value */}>
  <SomeComponent />
</MyContext.Provider>
```

`Provider`에 의해 제공된 `value`는 `Provider` 하위의 Component에서 `Consumer`를 이용해 사용할 수 있다.

### Context.Consumer

다음처럼 `Provider`에서 제공받은 값을 구독하여 사용할 수 있다.

```xml
<MyContext.Consumer>
	{ value => /* render something based on the context value */ }
</MyContext.Consumer>
```



## Context API는 Redux를 대체할 수 있는가

음... 일단 내 블로그에서는 대체 가능할 것 같다. Redux를 쓰는 이유가 단지 store를 이용한 state 관리 뿐이라면 `Context`로 대체하는 게 낫다. 하지만 실제로 Redux가 할 수 있는 일은 [그 정도로 간단한 일에 그치지 않는다고 한다](https://medium.com/lunit-engineering/당신에게-redux는-필요-없을지도-모릅니다-b88dcd175754). Redux를 쓰면 다음의 일들을 할 수 있다고 한다. (~~더 있는데 내가 이해를 못해서 못 쓴다.~~)

- 사용자가 생성한 액션으로부터 state를 재현할 수 있다. 이는 디버깅에 매우 효과적이다.
- SSR에 유리하다(서버에서 store만 갈아치우면 된다).
- middleware를 만들어 쓸 수 있다.


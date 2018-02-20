[블로그 제작기](http://enhanced.kr/postviewer/7) 시리즈의 글입니다. 

1년 전에 블로그를 만들기 시작했을 땐 아무것도 모르는 상태였기 때문에 리액트 예제코드를 여기저기서 주워다가 덕지덕지 기워서 만들었었다. 조금 지식이 쌓이고 내 스스로 이 누더기 코드가 부끄럽다는 생각이 들어 코드 좀 정리해보겠다고 [ESLint](https://enhanced.kr/postviewer/2804)를 적용했다. ESLint가 잡아낸 사항들의 대부분은 쉽게 납득할 수 있는 부분들이었다. 하지만 그렇지 않은 부분들도 몇 가지 있었는데 그 중 하나가 이번 포스트의 주제에 해당하는 내용이다. 



## react/prefer-stateless-function

다음은 문제가 된 ESLint rule의 내용이다.

> ESLint: Component should be written as a pure function (react/prefer-stateless-function)

그리고 이 위반이 발견된 코드는 다음과 같다.

```javascript
import React, { Component } from 'react';

class NoMatch extends Component {
  render() {
    return (
      <div className="no-match">
        <h1>Not found</h1>
        <h2>just like my girlfriend</h2>
      </div>
    );
  }
}

export default NoMatch;
```

이 코드를 다음처럼 바꾸라는 뜻이다.

```javascript
import React from 'react';

const NoMatch = () => (
  <div className="no-match">
    <h1>Not found</h1>
    <h2>just like my girlfriend</h2>
  </div>
);

export default NoMatch;
```



## Difference between class components and functional components

Class component의 경우 `Component`라는 클래스를 상속받고 있다. `react/Component`가 제공하는 주된 기능은 JSX를 리턴하고 [life cycle](https://enhanced.kr/postviewer/328)에 따라 이 JSX를 수정하여 다시 렌더링하는 것과 `state`를 제공해주는 것이다. 이 사실이 의미하는 바는 두 가지다.

1. 객체 초기화시 생성자에서 life cycle에 관련된 모든 함수를 실행한다.
2. Life cycle에 관련된 코드가 포함되면서 코드의 양이 늘어난다.

1번으로 인한 성능차이는 솔직히 한 번에 불러오는 컴포넌트가 많아야 10개 정도일테니 크지 않을 수 있다. 하지만 2번으로 인한 성능차이는 모듈번들러를 사용하는 경우 모든 컴포넌트가 한 파일에 때려박히게 되므로 극명하게 커진다. 실제로 위의 두 코드를 바벨을 이용해 ES5 문법으로 트랜스파일해보면 다음과 같이 크기 차이가 꽤 나는 것을 볼 수 있다.

```javascript
"use strict";

Object.defineProperty(exports, "__esModule", {
  value: true
});

var _react = require("react");

var _react2 = _interopRequireDefault(_react);

function _interopRequireDefault(obj) { return obj && obj.__esModule ? obj : { default: obj }; }

var NoMatch = function NoMatch() {
  return _react2.default.createElement(
    "div",
    { className: "no-match" },
    _react2.default.createElement(
      "h1",
      null,
      "Not found"
    ),
    _react2.default.createElement(
      "h2",
      null,
      "just like my girlfriend"
    )
  );
};

exports.default = NoMatch;
```

```javascript
"use strict";

Object.defineProperty(exports, "__esModule", {
  value: true
});

var _createClass = function () { function defineProperties(target, props) { for (var i = 0; i < props.length; i++) { var descriptor = props[i]; descriptor.enumerable = descriptor.enumerable || false; descriptor.configurable = true; if ("value" in descriptor) descriptor.writable = true; Object.defineProperty(target, descriptor.key, descriptor); } } return function (Constructor, protoProps, staticProps) { if (protoProps) defineProperties(Constructor.prototype, protoProps); if (staticProps) defineProperties(Constructor, staticProps); return Constructor; }; }();

var _react = require("react");

var _react2 = _interopRequireDefault(_react);

function _interopRequireDefault(obj) { return obj && obj.__esModule ? obj : { default: obj }; }

function _classCallCheck(instance, Constructor) { if (!(instance instanceof Constructor)) { throw new TypeError("Cannot call a class as a function"); } }

function _possibleConstructorReturn(self, call) { if (!self) { throw new ReferenceError("this hasn't been initialised - super() hasn't been called"); } return call && (typeof call === "object" || typeof call === "function") ? call : self; }

function _inherits(subClass, superClass) { if (typeof superClass !== "function" && superClass !== null) { throw new TypeError("Super expression must either be null or a function, not " + typeof superClass); } subClass.prototype = Object.create(superClass && superClass.prototype, { constructor: { value: subClass, enumerable: false, writable: true, configurable: true } }); if (superClass) Object.setPrototypeOf ? Object.setPrototypeOf(subClass, superClass) : subClass.__proto__ = superClass; }

var NoMatch = function (_Component) {
  _inherits(NoMatch, _Component);

  function NoMatch() {
    _classCallCheck(this, NoMatch);

    return _possibleConstructorReturn(this, (NoMatch.__proto__ || Object.getPrototypeOf(NoMatch)).apply(this, arguments));
  }

  _createClass(NoMatch, [{
    key: "render",
    value: function render() {
      return _react2.default.createElement(
        "div",
        { className: "no-match" },
        _react2.default.createElement(
          "h1",
          null,
          "Not found"
        ),
        _react2.default.createElement(
          "h2",
          null,
          "just like my girlfriend"
        )
      );
    }
  }]);

  return NoMatch;
}(_react.Component);

exports.default = NoMatch;
```

Functional component를 사용한 경우의 파일크기는 591bytes인 반면, class component를 사용한 경우의 파일크기는 2459byes로 4배가 넘는 차이가 나는 것을 확인할 수 있다. 이는 웹페이지의 초기 로딩속도에 안 좋은 영향을 미칠 수 있다.



## Conclusion

단순히 모듈 분리를 위해 별다른 기능 없이 DOM만 리턴하는 컴포넌트가 있다면, 이 때는 성능을 위해 class 문법을 지양하는 것이 좋다. 또한, class component의 수가 적을 수록 좋으므로 가급적이면 `state`를 쓰는 로직이나 렌더링 타이밍을 제어하는 로직을 적은 수의 파일에 모으는 게 성능상의 이점이 있지 않을까 한다.

본 포스트를 쓰면서 주로 참고한 글은 [여기](https://itnext.io/react-component-class-vs-stateless-component-e3797c7d23ab)이므로 뭔가 부족함을 느끼는 사람들은 저 글도 읽어보자.
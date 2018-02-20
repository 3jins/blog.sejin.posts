[블로그 제작기](http://enhanced.kr/postviewer/7) 시리즈의 글입니다.

Node.js와 npm은 ~~심의삭제~~ 두 쪽 마냥 항상 붙어다니는 개념이므로 같이 설명하도록 하겠다.





## Node.js

자바스크립트의 구동을 위해서는 자바스크립트 엔진이 필요하다. 각 브라우저에서 자바스크립트 코드가 자동으로 구동되는 것은 브라우저마다 각기 다른 엔진을 탑재하고 있기 때문이다. 크롬은 V8, 파이어폭스는 SpiderMonkey, 사파리는 Webkit, IE 및 Edge는 Chakra를 쓰고 있으며, 오페라는 Carakan을 쓰다가 최근 V8로 전환했다. 그 외에 Mozila 재단에서 파폭 이외에 자바 플랫폼에서 쓰기 위한 목적으로 만든 Rhino 등이 있다. Node.js는 그 중 V8 엔진 기반으로 동작한다. 

>   "Node.js®는 [Chrome V8 JavaScript 엔진](https://developers.google.com/v8/)으로 빌드된 JavaScript 런타임입니다." - Node.js 공식 홈페이지

본래 자바스크립트는 인터프리터 언어지만 V8 엔진은 자바스크립트를 좀 더 빠르게 동작하게끔 만들자는 목표를 가지고 [JIT 컴파일러](https://ko.wikipedia.org/wiki/JIT_%EC%BB%B4%ED%8C%8C%EC%9D%BC)처럼 만들어졌다. 그래서 인터프리터와 컴파일러의 장점을 모두 취한 모습을 보인다. 이 부분에 대한 좀 더 자세한 설명은 [여기](https://engineering.huiseoul.com/자바스크립트는-어떻게-작동하는가-v8-엔진의-내부-최적화된-코드를-작성을-위한-다섯-가지-팁-6c6f9832c1d9?refer=개발자스럽다)를 참고하면 좋을 것 같다.

### Web Server?

Node.js에 대한 가장 흔한 오해가 하나 있다면 흔히들 Node.js가 그 자체로 웹서버라고 생각한다는 것이다. 하지만 Node.js는 사실 그저 브라우저 바깥에서 자바스크립트가 동작할 수 있게끔 만들어주는 **V8 자바스크립트 엔진** 기반 플랫폼일 뿐이다.

실제로 Node.js는 아파치나 nginx 등의 웹서버와는 달리 서버역할을 하기 위해서는 개발자가 직접 서버코드를 짜야만 한다. 다만, Node.js가 웹서버인 것처럼 널리 알려진 이유는 웹서버 제작에 편리한 API를 제공하고 있기 때문이다.

```javascript
const http = require('http');

const hostname = '127.0.0.1';
const port = 3000;

const server = http.createServer((req, res) => {
  res.statusCode = 200;
  res.setHeader('Content-Type', 'text/plain');
  res.end('Hello World\n');
});

server.listen(port, hostname, () => {
  console.log(`Server running at http://${hostname}:${port}/`);
});
```

위 코드는 Node.js 공식 홈페이지에 나와 있는 [Getting Started Guide](https://nodejs.org/en/docs/guides/getting-started-guide/) 코드다. 첫 줄에 보면 http라는 모듈을 가져오며, 웹서버로서의 기능을 제공하는 함수는 모두 저 `http`에서 나온다. 실제로 [문서](https://nodejs.org/api/http.html)를 들어가 보면 왼쪽에 모듈이 굉장히 많은 것을 알 수 있고, http 외에도 console이나 debugger 같은 모듈도 보인다. 즉, 웹서버로만 쓰라고 만든 엔진이 아니라는 뜻이다.



## npm(node package manager)

npm은 이름 그대로 **N**ode.js 기반 어플리케이션에 **P**ackage를 설치하고 설치된 패키지들의 버전과 호환성을 **M**anage하는 툴이다. 이 말이 어렵다면 그냥 Node.js에서 라이브러리나 모듈 가져다쓸거면 npm 깔아야 된다고 생각해도 된다. 사실 전통적인 방법대로 html에 직접 js파일을 박아넣어도 되지만 업데이트를 일일이 수작업으로 해줘야 하기 때문에 심히 번거롭다.

과거에는 프론트엔드 환경이 매우 단순해서 'HTML = 뼈, CSS = 피부, Javascript = 근육'과 같이 이해하고 나면 끝이었다. 자바스크립트 파일들을 모듈로 관리하는 일 따위는 상상할 수도 없었고 JS는 그저 HTML 폼 데이터를 백 단으로 넘기기 전에 미리 검사하고 약간의 DOM 조작이나 해주면 그만이었다. HTML 페이지 하나에 JS 파일은 하나 정도 있으면 충분했고 외부 모듈을 쓴다 해봤자 jQuery파일 하나 정도만 `<script>` 코드로 html에 추가해주는 정도였다.

하지만 Backbone.js, Angular.js 등 현대 프론트엔드의 시작을 알리는 프레임워크가 등장하고, 브라우저들이 미처 적응하기도 전에 ES6, ES7이 나타나 이전과는 완전히 다른 문법으로 개발자들을 열광시키면서 상황이 완전히 달라졌다. 개발자들은 ES6로 코드를 작성하고 싶어했고 이를 위해서는 바벨 모듈이 ES의 세부버전이나 기능별로 필요했다. React를 쓰려면 react 모듈이 추가돼야 했고, React가 SPA 붐을 일으키면서 웹팩과 같은 모듈번들러가 있어야 했다. 

이 많은 모듈을 함께 쓰다 보면 의존성이 서로 얽히고 일일이 업데이트를 해주는 것 또한 일이다. 이러한 일들을 맡아 알아서 해주는 프로그램이 바로 npm이다. ~~그런데 요즘은 npm 대신 yarn을 많이 쓴다고 한다.~~



### 환경 구성

운영체제나 플랫폼에 따라 방법이 다양하므로 자세히 설명하지는 않겠다. [docker](https://www.docker.com/what-docker) 위에서 node라는 image를 이용하여 컨테이너를 구축해도 좋고, 그냥 운영체제에 생으로 깔아도 된다. 



### 패키지 관리 체계

npm은 **node_modules**라는 디렉토리를 두고 이 안에 패키지들 파일들을 겨울잠 준비하는 다람쥐마냥 모아둔다. node_modules 디렉토리는 프로젝트별로 하나씩 존재하며, 모든 프로젝트에서 공통으로 사용하는 글로벌 모듈을 관리하는 node_modules 디렉토리가 또 별도로 있다. `npm root -g` 명령어를 입력하면 글로벌 node_modules 디렉토리의 위치를 확인할 수 있다.

node 프로젝트라면 **[package.json](http://enhanced.kr/postviewer/12)** 파일을 하나씩 갖고 있는데 이 파일에서 해당 프로젝트가 필요로 하는 모듈이 무엇이 있는지를 명시한다. 프로그램이 실행될 때, (정확히 npm이 하는 일인지 node가 하는 일인지는 모르겠는데) package.json을 참조하여 필요한 모듈을 해당 프로젝트의 node_modules, 또는 global node_modules에서 가져오게 된다.



### 명령어

#### 프로젝트 생성

```bash
npm init
```

위 명령어를 통해 프로젝트 폴더 내에 package.json을 생성할 수 있다. 일단 명령어를 치면 질문들이 우르르 쏟아지는데 나중에 json 파일을 직접 수정해도 전혀 상관없으니 무시하고 다 엔터 치면 된다. Git에서 `git init` 명령으로 .git 디렉토리가 생기고 나면 그 때부터 해당 디렉토리가 Git 프로젝트가 되듯이, package.json이 생기면 그 순간부터 해당 디렉토리가 node 프로젝트가 된다고 생각하면 된다.

#### 패키지 설치

예를 들어, express 프레임워크를 설치한다고 하면

```bash
npm install -g express
```

처럼 쓰면 된다.

##### options

* `-g`

  global의 약자로 특정 프로젝트에 종속되지 않게끔 패키지를 설치한다는 의미다. express나 webpack처럼 프로젝트의 외부에서 프로젝트를 생성하거나 동작시키는 데에 관련된 패키지에는 `-g` 옵션을 주면 된다.

* `--save`

  body-parser처럼 프로젝트 내부에서만 쓰이는 패키지에는 `--save` 옵션을 준다. 이 옵션을 줘서 설치하게 되면 모듈은 프로젝트 경로의 node_modules 디렉토리에 다운되고, package.json 파일의 `dependencies` 항목에 해당 모듈이 추가된다.

* `--save-dev`

  save 옵션과 비슷한데 프로그램을 실행하는 데는 필요 없고, 개발할 때만 필요한 모듈은 이 옵션을 줘서 설치한다. 보통 babel 같은 모듈을 설치할 때 쓰인다. 

##### 일괄설치

```bash
npm install
```

package.json에 명시된 모듈들 중 시스템에 없는 모듈을 모두 일괄적으로 다운받는 명령이다. 프로젝트를 처음 만드는 경우에는 필요 없는 작업이지만 깃 등에서 다른 사람의 프로젝트를 다운받아 실행시켜야 하는 경우에는 유용하다. 이 명령 덕택에 node.js 기반 프로젝트들은 깃에 모듈파일들까지 업로드할 필요가 없다.

#### 모듈 업데이트

```bash
npm update 모듈명
```

뭐... 보시다시피... 더 설명하진 않겠다.

##### 모듈 일괄 업데이트

```bash
npm update
```

install과 마찬가지로 update도 일괄적으로 해줄 수 있다. 알아서 의존성을 검사해서 업데이트를 시켜주는데 가끔씩 뭔가 잘 안되기도 한다.
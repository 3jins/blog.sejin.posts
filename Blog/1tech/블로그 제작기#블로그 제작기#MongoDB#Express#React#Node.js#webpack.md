이 블로그는 [MERN 스택](http://mern.io/)을 기반으로 하여 내 손으로 직접 만들었다. MERN 스택이란 MongoDB, Express, React, Node.js, 이 4개의 기술스택의 앞글자를 따서 만들어진 말이다. 

사실 처음부터 MERN 스택으로 만들려고 한 건 아니었다. 맨 처음에는 APM과 Python3 + Django로 각각 웹앱을 어설프게나마 하나씩 만들어 봤으니 ~~그리고 그 기술들이 고인물이 되어가니~~ 이번에는 node.js 기반의 웹앱을 하나 만들어보자는 데에서 시작했다. 여기에 필요에 따라 하나씩 살을 붙여나갔고 완성된 결과물을 놓고 보니 MERN 스택을 따르고 있었다.

나는 npm을 이번에 처음 접했고 그에 따라 당연히 이 블로그를 만드는 동안 사용한 기술스택들은 ES4 시대의 javascript 정도를 제외하고는 전부 내게 새로운 것들이었다. 블로그 제작과정동안 수많은 블로그들과 스택오버플로우, 깃헙에 올라와있는 오픈소스 등을 통해 많은 것을 배웠으니 이제는 내가 그 지식을 가공하여 나누고자 한다.

#### tl;dr:

1.  블로그에 MERN 스택 들어감.
2.  처음부터 MERN 스택 쓰려고 한 거 아니고 그냥 어쩌다보니 그렇게 됨.
3.  이제부터 블로그 만들면서 공부한거 정리해서 포스팅할거임.



## Refactoring

블로그를 처음 만들기 시작한지로부터 1년여가 지났다. 하지만 그간 6전공의 늪에 허덕이고, [졸업작품](https://enhanced.kr/postviewer/93)에 매진하고, 취업준비를 위해 [알고리즘](https://enhanced.kr/postviewer/107)만 주구장창 파는 등 여러 일로 바쁜 한 해를 보내다보니 블로그에 소홀할 수밖에 없었다. 현 시점에서 이 블로그는 다음과 같은 문제점을 안고 있다.

1. 성능
    * SPA 구조로 인한 느린 초기 로딩속도
2. 코드품질
    * 깔끔하지 못한 프로젝트 구조
    * 따르다 만 스타일가이드
3. 기능 / UI
    * 너무 많은 태그로 인한 비직관적인 포스트 분류체계
    * 검색기능의 부재
    * 일부 markdown과 LaTeX 문법의 렌더링 오작동
    * 페이스북 댓글 API의 오작동
    * 데이터베이스 관리 코드의 일부 오류
    * 그 외 잔버그들...

현재 이런 문제들 때문에 블로그를 미완이라고 보고 별다른 홍보를 하지 않았다. 위 문제들을 개선하고 블로그 제작기가 완성되는 날 본격적으로 블로그를 세상에 내놓고 알리고자 한다.



## Index

### Javascript

### Node.js

1.  [node & npm](http://enhanced.kr/postviewer/6)
2.  [package.json](http://enhanced.kr/postviewer/7)

### Babel

### Express

1. [Express vs Koa](https://enhanced.kr/postviewer/88)
2. [Express 서버에 SSL(HTTPS) 연결 설정하기](https://enhanced.kr/postviewer/53)

### webpack

1. [Javascript module bundler](https://enhanced.kr/postviewer/111)
2. webpack configuration
3. webpack & React
4. SSR (Server Side Rendering)

### React

1. [React 특징](http://enhanced.kr/postviewer/9)

2. 기본 문법

   2-1. [React 문법 - Component](http://enhanced.kr/postviewer/10)

   2-2. [React 문법 - props & state](http://enhanced.kr/postviewer/11)

3. Rendering

   3-1. [React Life Cycle API](http://enhanced.kr/postviewer/39)

   3-2. [React hooks](http://enhanced.kr/postviewer/137)

4. State 관리하기

   4-1. Flux, Redux, Mobx

   4-2. [React Context](http://enhanced.kr/postviewer/136)

   4-3. 

5. 컴포넌트 관리하기

   5-1. [class component vs functional component](http://enhanced.kr/postviewer/118)

   5-2. [고차 컴포넌트(HOC)](http://enhanced.kr/postviewer/124)

### MongoDB

### 제작 후기

1. [blog.sejin](https://enhanced.kr/postviewer/23)


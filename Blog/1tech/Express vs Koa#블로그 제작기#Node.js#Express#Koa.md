[블로그 제작기](http://enhanced.kr/postviewer/7) 시리즈의 글입니다. 

이 글은 주로 [여기](https://raygun.com/blog/koa-vs-express-2018/)를 많이 참고했습니다.

Node.js의 `http`나 `https` 모듈의 `createServer` 함수를 사용하면 Node.js만으로도 충분히 요청을 받아 처리하고 응답을 보내주는 서버의 역할을 수행하게 할 수 있다. 하지만 [여기](https://www.zerocho.com/category/NodeJS/post/57774a8eacbd2e9803de0195)서 볼 수 있다시피, 다른 기능 아무것도 없이 요청 받은 그대로 돌려주는 것뿐인데도 코드가 결코 간단하지 않다. Node.js에는 Express, Koa, Hapi 등 이런 작업을 간단히 할 수 있게 도와주는 여러 프레임워크가 있다. Hapi는 비교적 신생 프레임워크라 상대적으로 불안정하고 커뮤니티도 작으므로 일단 다루지 않기로 하고, Express와 Koa를 비교해보도록 하겠다.



## 간략한 배경

#### Express

역사가 짧은 node.js 생태계지만, 그 안에서 나름 유서가 깊은 편에 속하는 서버 프레임워크다. 그만큼 타 프레임워크에 비해 안정적이고 커뮤니티가 크다.

Express의 철학은 다음과 같다.

> The Express philosophy is to provide small, robust tooling for HTTP servers, making it a great solution for single page applications, web sites, hybrids, or public HTTP APIs.
>
> Express does not force you to use any specific ORM or template engine. With support for over 14 template engines via [Consolidate.js](https://github.com/tj/consolidate.js), you can quickly craft your perfect framework.

대충 해석해보면 다른 모듈에 대한 의존 없이 설치만 하면 빠르게 SPA, 웹사이트, HTTP API를 만들 수 있게 해주겠다는 뜻이다. 

#### Koa

Express를 만든 바로 그 팀에서 "next generation web framework for node.js"를 표방하며 디자인한 프레임워크다. 이전에는 Express에 비해 성숙도가 떨어지고 커뮤니티가 작은 게 치명적인 문제였지만 지금은 많이 발전하여 그 부분이 문제시되지는 않는다.

Koa의 철학은 아래와 같다.

> Koa is a new web framework designed by the team behind Express, which aims to be a smaller, more expressive, and more robust foundation for web applications and APIs. By leveraging async functions, Koa allows you to ditch callbacks and greatly increase error-handling. Koa does not bundle any middleware within its core, and it provides an elegant suite of methods that make writing servers fast and enjoyable. 



## 비교

### 모듈 분리

* Express

  Express는 제공하는 모든 기능이 하나의 모듈로 묶여있어서 일부 기능만 사용하더라도 전체를 포함시켜야 한다. 달리 보면 하나의 프레임워크 설치로 모든 게 해결되는 것이기도 하니 "you can quickly craft your perfect framework"라는 철학에 들어맞는 부분이다.

* Koa

  Koa의 철학 중에는 "aims to be a smaller"라는 부분이 있다. 이를 반영하듯 Koa는 필요한 모듈만 설치해서 사용하는 게 가능하다.

### async/await의 지원

* Express

  Express에서는 기본적으로 async/await을 사용할 수 없다. [아예 불가능한 건 아니지만](http://programmingsummaries.tistory.com/399) 프레임워크에서 공식적으로 지원하는 건 아니다. 

* Koa

  Node.js의 http 모듈에서는 클라이언트와 데이터를 주고 받기 위해 `req`와 `res`라는 변수를 사용한다. Express 역시 이 변수를 그대로 사용한다. 하지만 async/await 문법을 지원하기 위해서는 이 방식이 적절하지 않았는지 Koa에서는 `req`/`res` 대신 `ctx`라는 하나의 변수를 사용한다.

### 성능

저 멀리 바다 건너 뉴질랜드에 계신 어떤 멋진 분께서 [테스트벤치](https://github.com/PlainSight/NodeRestPerfTest3)를 작성해놓으셨고 또한 어떤 갓갓한 인도개발자분께서 [이 레포를 포크 떠서](https://github.com/amitguptagwl/NodeRestPerfTest3) 관리하고 계시다. 그 분들에게 감사하는 마음을 품고 도커 컨테이너에서 테스트벤치를 돌려봤다.

```bash
> git clone https://github.com/amitguptagwl/NodeRestPerfTest3.git
> cd ./NodeRestPerfTest3
> sh install.sh
> sh run.sh
```

해당 테스트벤치에서는 50000개의 요청을 100개씩 동시에 보내서 초당 처리량을 보여준다. 내 로컬 환경에서 직접 두 번에 걸쳐 돌려본 결과는 다음과 같다.

| 회차 | vanilla Node | Express |  Koa2   |
| :--: | :----------: | :-----: | :-----: |
|  1   |   3691.76    | 2752.68 | 2720.05 |
|  2   |   3327.45    | 2742.41 | 3126.61 |

큰 차이는 안 나지만 Koa가 Express보다 살짝 빠르다는 것을 볼 수 있었다.



## 결론

이젠 특별한 이유가 없으면 Express보다는 Koa를 쓰는 게 합리적인 것으로 보인다. 여러가지 특징들을 종합해볼 때, Koa는 Express 개발팀에서 **성격이 다른 프레임워크를 만들었다기 보다는 Express의 단점을 개선해서 다시 내놓은 프레임워크에 가깝다**고 생각된다. 다만, 기존에 진행 중이던 프로젝트가 Express로 짜여졌다면, Koa로 바꾸기 위해서는 서버와 통신하는 함수의 외형이 다르기 때문에 호환성 문제가 크다고 한다. 



## 사족

내 블로그에는 Express가 쓰였다. 그 이유는 1. 당시에 Koa에 대해 들어본 바가 없었고, 2. 당시 자바스크립트 및 node 생태계 전반에 대한 이해가 부족해서 알아본다 한들 제대로 이해할 수 없었기 때문이다. [다음 프로젝트](https://enhanced.kr/postviewer/1551)에는 Koa를 써봐야겠다.


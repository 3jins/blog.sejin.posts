[3편](https://enhanced.kr/postviewer/141)에서 Serverless 프레임워크의 사용법을 알아보았다. 이제 얻은 지식을 바탕으로 채용공고 클로러 잡식을 AWS Lambda에 업로드해볼 차례다. `serverless.yml`을 다음처럼 만들어보았다.

```yml
service: job-seek-crawler

provider:
  name: aws
  runtime: nodejs10.x
  stage: dev
  region: ap-southeast-1

functions:
  crawlAndSave:
    handler: handler.crawlAndSave
    events:
      - http:
          path: crawler
          method: get
```

그리고 서버 코드를 AWS Lambda 형식에 맞게 적당히 수정해서 배포를 시도해봤다. Serverless는 다음과 같은 에러로 나를 반겨주었다.

```
Serverless Error ---------------------------------------

  An error occurred: CrawlAndSaveLambdaFunction - Unzipped size must be smaller than 262144000 bytes (Service: AWSLambdaInternal; Status Code: 400; Error Code: InvalidParameterValueException; Request ID: feef5253-9d8c-11e9-af7a-65f3680748e7).

  Get Support --------------------------------------------
     Docs:          docs.serverless.com
     Bugs:          github.com/serverless/serverless/issues
     Issues:        forum.serverless.com

  Your Environment Information ---------------------------
     OS:                     win32
     Node Version:           10.13.0
     Serverless Version:     1.46.0
```

원인은 250MB를 초과하는 프로젝트 사이즈. 그리고 용량의 대부분을 차지하는 건 [Puppeteer](https://github.com/GoogleChrome/puppeteer)라는 라이브러리였다.

| <img src="https://raw.githubusercontent.com/3jins/Images/master/puppeteer-too-large.png" width="50%" alt="321MB of Puppteer library"/> |
| :----------------------------------------------------------: |
| [250MB까지는 어떻게 해보겠는데 역시 321MB는 좀...](https://comic.naver.com/webtoon/detail.nhn?titleId=119874&no=489) |

채용공고 좀 편하게 보려고 코드 짜기 시작했는데 삽질의 규모가 커진다. 그냥 일일이 들어가서 보는 게 더 이득이었지 싶다. ㅎㅎㅎ



## Puppeteer를 써야만 했던 이유

Puppeteer는 [headless Chrome](https://developers.google.com/web/updates/2017/04/headless-chrome)을 Node.js에서 구동하고 조작할 수 있도록 도와주는 라이브러리다. 따라서 이 라이브러리 안에는 headless Chrome이 통째로 들어가며, 그에 따라서 용량이 엄청나게 커진 것이다.

고작 크롤링 정도 작업하는데 headless Chrome까지 써야 하는 이유가 있을까 싶을 수 있다. 기존의 웹페이지라면 저렇게 무거운 라이브러리를 써야 할 이유가 전혀 없다. 하지만 요즘은 SPA가 점점 많아지는 추세고, 서버에서 자바스크립트 코드만 보내주고, Ajax 요청으로 내용을 나중에 로딩하는 웹페이지가 많다. 따라서 SPA 웹페이지를 기존 방식대로 크롤링을 하게 되면 빈 html 밖에 얻을 수가 없다.

뭐 이 정도는 방법을 잘 찾아보면 어떻게 우회할 수 있을지도 모른다. 하지만 모든 정보를 보기 위해서는 사용자 인터랙션이 필요한 경우가 있다. 예를 들어, 공고의 페이지네이션이 더보기 버튼이나 무한스크롤로 lazy하게 로딩되는 경우, 버튼을 누르거나 스크롤을 특정 위치로 내리는 액션이 필요하다.



## First Trial : Serverless Chrome

AWS Lambda에서 puppeteer를 쓸 수 있도록 도와주는 라이브러리가 있을 것이라 생각했다. [Serverless Chrome](https://github.com/adieuadieu/serverless-chrome)은 Node.js가 실행된 환경에서 크로미움을 찾아 실행할 수 있게끔 도와주는 라이브러리 모음으로, Serverless와의 연결을 도와주는 [serverless-plugin-chrome](https://github.com/adieuadieu/serverless-chrome/blob/master/packages/serverless-plugin)과 [@serverless-chrome/lambda](https://github.com/adieuadieu/serverless-chrome/blob/master/packages/lambda)를 포함한다. [친절한 설명이 있는 블로그 게시물](https://nadeesha.github.io/headless-chrome-puppeteer-lambda-servelerless/)이 있어서 따라해보기로 했다. 대강 다음과 비슷하게 `serverless.yml` 파일을 만들어주도록 하자.

```yml
service: job-seek-crawler

plugins:
  - serverless-plugin-chrome

package:
  exclude:
    - node_modules/puppeteer/.local-chromium/**
    - __tests__/**
    - src/**
    - dist/**
    - images/**
    - build/client
    - build/public
    - build/webServer
    - build/access.log

provider:
  name: aws
  runtime: nodejs8.10
  stage: dev
  region: ap-southeast-1

functions:
  crawlAndSave:
    handler: build/batchServer/serverless/handler.crawl
    events:
      - http:
          path: crawler
          method: get
```

잡식은 배치서버, 웹서버, 프론트엔드가 mongoDB나 util 등의 일부 소스코드를 공유하기 때문에 이 셋이 하나의 프로젝트 안에 모두 들어있다. 따라서 batchServer 입장에서 필요없는 소스코드는 모두 제외해줘야 하기 때문에 위의 yml 파일에서 `exclude` 부분이 길어지게 되었다.

이제 배포를 시도해보도록 하자. 하지만 그 결과는...

| <img src="https://raw.githubusercontent.com/3jins/Images/master/serverless-chrome-not-support-node-10.png" width="100%" alt=""/> |
| :----------------------------------------------------------: |
|                          Oh, shit!                           |

Serverless Chrome은 Node.js 10버전을 지원하지 않는다고 한다. 사실 10 버전 고유의 뭔가를 쓴 건 아니지만, 8버전으로 내리자니 뭔가 퇴보하는 기분이라 내키지 않는다. 다른 방법이 있지는 않을까...



## Second Trial : AWS Lambda Layer

Serverless Chrome의 이슈에서 'node 10'을 키워드로 검색했더니 [Lambda Layer를 두는 게 해결책](https://github.com/adieuadieu/serverless-chrome/issues/203#issuecomment-497040728)이라는 댓글이 있었다. [AWS Lambda Layer](https://aws.amazon.com/ko/blogs/korea/new-for-aws-lambda-use-any-programming-language-and-share-common-components/)는 2018년 12월에 새로 출시된 기능으로, 여러 Lambda가 공유하는 라이브러리를 올려둘 수 있는 공간이다. 하지만 AWS 공식문서에 따르면, [Lambda의 용량제한 250MB는 Lambda 자체와 Lambda Layer의 용량을 합산해서 계산되는 값이라 한다](https://docs.aws.amazon.com/ko_kr/lambda/latest/dg/limits.html). 즉, 단일 라이브러리 크기가 300 MB가 넘는 이 상황에서는 별로 도움이 되지 않는다는 뜻이다.



## Deep Dive into Serverless Chrome

다른 방법이 없는 것 같으니 일단 다시 첫 번째 시도로 돌아가보기로 했다.

Serverless Chrome 제작자의 블로그에 가보면 [Serverless Chrome을 왜 짜기 시작했고 어떤 식으로 동작하는지에 대해 자세히 설명한 글](https://medium.com/@marco.luethy/running-headless-chrome-on-aws-lambda-fa82ad33a9eb)이 있다. Puppeteer에서 빌드한 local Chromium 대신, 직접 빌드한 Chromium을 쓰겠다는 게 요지이고, Amazon Linux에서 실행을 시켜야 하니 이에 대한 가상환경을 만들어서 어찌어찌 빌드해야 한다는 내용이 나온다.

직접 빌드했을 때 용량이 왜 작아지는가에 대한 설명은 딱 한 줄로 다음과 같다.

> Compiling a non-debug build of the headless Chromium shell yields a binary that’s ~125 MB and just under 44 MB when gzipped.

[크로미움 공식문서](https://www.chromium.org/developers/gn-build-configuration)를 열어보면 `is_debug` 플래그에 대한 설명이 있는데 이 플래그를 끄고 빌드를 하면 용량이 확 줄어드는 것으로 보인다. 아주 단순하지만, 내가 직접 headless Chrome을 빌드하고 있기는 너무 부담스러우니 다른 방법을 찾아보도록 하자.



## Third Trial : 크로미움 빌드결과물에서 일부 파일 지우기

먼저 드는 생각은 `puppeteer`에 내장된 headless Chrome에서 debug 모듈을 제거하면 어떨까 하는 부분이다. `node_modules/puppeteer/.local-chromium`에 들어가서 파일을 뒤져보면 딱 봐도 테스트할 때만 쓰게 생긴 거대한 실행파일이 있다.

<img src="https://raw.githubusercontent.com/3jins/Images/master/chromium-interactive-ui-tests-exe.png" width="50%" alt=""/>

일단 AWS Linux에서 돌아가는 크로미움이어야 하기 때문에 사실 리눅스환경에서 빌드를 해야 하지만 Puppeteer의 리드미를 읽어보면 다음과 같은 내용이 있다.

> Note: When you install Puppeteer, it downloads a recent version of Chromium (~170MB Mac, ~282MB Linux, ~280MB Win) that is guaranteed to work with the API.

즉, 리눅스 버전이나 윈도우 버전이나 별 용량차이가 없으므로, 윈도우 버전에서 배포가 되면 리눅스 버전에서도 배포가 될 것이라는 뜻이다. 얘를 지우고 배포해보면...

```
  Serverless Error ---------------------------------------

  An error occurred: CrawlAndSaveLambdaFunction - Unzipped size must be smaller than 262144000 bytes (Service: AWSLambdaInternal; Status Code: 400; Error Code: InvalidParameterValueException; Request ID: 0c9de302-5d16-4fc0-82ae-2e8a5795625b).
```

여전히 커서 실패한다. ㅎㅎ... 파일들이 죄다 실행파일 등의 바이너리라 이 이상 줄이는 건 불가능하고 결국 빌드를 다시 해야 한다는 결론에 이른다.



## Fourth Trial : Puppeteer Installation Options

Puppeteer에는 `PUPPETEER_SKIP_CHROMIUM_DOWNLOAD`이라는 로컬 크로미움을 제외하고 설치할 수 있는 옵션이 있다. 그럼 논디버그 모드로 설치할 수 있는 옵션도 있지 않을까 싶어 [문서](https://github.com/GoogleChrome/puppeteer/blob/v1.18.1/docs/api.md#environment-variables)를 찾아봤다.

> ### Environment Variables
>
> Puppeteer looks for certain [environment variables](https://en.wikipedia.org/wiki/Environment_variable) to aid its operations. If Puppeteer doesn't find them in the environment during the installation step, a lowercased variant of these variables will be used from the [npm config](https://docs.npmjs.com/cli/config).
>
> - `HTTP_PROXY`, `HTTPS_PROXY`, `NO_PROXY` - defines HTTP proxy settings that are used to download and run Chromium.
> - `PUPPETEER_SKIP_CHROMIUM_DOWNLOAD` - do not download bundled Chromium during installation step.
> - `PUPPETEER_DOWNLOAD_HOST` - overwrite URL prefix that is used to download Chromium. Note: this includes protocol and might even include path prefix. Defaults to `https://storage.googleapis.com`.
> - `PUPPETEER_CHROMIUM_REVISION` - specify a certain version of Chromium you'd like Puppeteer to use. See [puppeteer.launch([options\])](https://github.com/GoogleChrome/puppeteer/blob/v1.18.1/docs/api.md#puppeteerlaunchoptions) on how executable path is inferred. **BEWARE**: Puppeteer is only [guaranteed to work](https://github.com/GoogleChrome/puppeteer/#q-why-doesnt-puppeteer-vxxx-work-with-chromium-vyyy) with the bundled Chromium, use at your own risk.
> - `PUPPETEER_EXECUTABLE_PATH` - specify an executable path to be used in `puppeteer.launch`. See [puppeteer.launch([options\])](https://github.com/GoogleChrome/puppeteer/blob/v1.18.1/docs/api.md#puppeteerlaunchoptions) on how the executable path is inferred. **BEWARE**: Puppeteer is only [guaranteed to work](https://github.com/GoogleChrome/puppeteer/#q-why-doesnt-puppeteer-vxxx-work-with-chromium-vyyy)with the bundled Chromium, use at your own risk.
>
> > **NOTE** PUPPETEER_* env variables are not accounted for in the [`puppeteer-core`](https://www.npmjs.com/package/puppeteer-core) package.

그런 옵션은 없었다.



## Serverless Chrome이 Node 10.x을 지원할 수 없는 이유

AWS Lambda에서 Node 10 버전을 쓸 수 있게 된 건 [2019년 5월](https://aws.amazon.com/ko/about-aws/whats-new/2019/05/aws_lambda_adds_support_for_node_js_v10/)부터다. 그런데 10버전의 노드는 새로운 운영체제인 **Amazon Linux 2**에서 실행된다고 한다. Serverless Chrome이 8버전까지만 지원하는 이유는 여기에 있다. Amazon Linux 2 도커 환경에서 아직 빌드를 돌리지 못한 것이다. 아마 올해 안으로는 10버전을 지원하기 시작하지 않을까 한다.

그럼 람다에서 Node.js 10버전을 쓰면 어떤 이득이 있느냐, 그건 [공식문서](https://nodejs.org/fr/blog/release/v10.0.0/)나 [이를 알아듣기 쉽게 풀어서 해석한 다른 블로그](https://nodesource.com/blog/what-s-new-to-lts-with-the-introduction-of-node-js-10-lts/)를 보면 된다. 아마 10 버전에서 지원하기 시작한 최신 문법을 내 프로젝트에 썼을 것 같지는 않으니, 그냥 8 버전으로 낮춰서 코드를 실행해보고 잘 되면 고민하지 않기로 했다. 언젠가 Serverless Chrome에서 Node 10.x을 지원하기 시작하면 업데이트해주면 된다.



## Fifth Trial : Using Node 8.x with Serverless Chrome

Node.js 버전을 8로 낮추기 위해 NVM을 써도 되고 도커를 써도 된다. NVM은 공식적으로 윈도우를 지원하지 않으므로 윈도우 환경에서 NVM을 설치하려면 [nvm-windows](https://github.com/coreybutler/nvm-windows)나 [Nodist](https://github.com/nullivex/nodist)를 사용해야 한다. 

Nodist를 설치하기로 결정했는데 기존 node를 꼭 삭제해야하는지는 잘 모르겠다. 난 삭제했다. Node.js는 그냥 설치프로그램을 실행시켜서 삭제시킬 수 없다. 다음의 단계를 거쳐 바퀴벌레를 없애려는 세스코마냥 완전히 박멸시켜야 한다.

1. 제어판의 '프로그램 및 기능(舊 프로그램 추가/제거)'에서 Node.js를 선택해서 삭제한다.
2. `C:\Program Files\nodejs`를 삭제한다.
3. `C:\Users\<username>\AppData\Roaming\npm`을 삭제한다.

`chocolatey`를 이용하면 쉽게 설치가 가능하다. 단, 윈도우 환경변수가 자동으로 잡히지 않으므로 Path 환경변수에 `C:\Program Files (x86)\Nodist\bin`를 추가해줘야 모든 경로에서 Nodist 명령을 사용할 수 있다.

이제 다음 명령으로 8버전 Node.js를 설치하고 선택해주도록 하자.

```
$ nodist + 8
$ nodist global 8
$ nodist
```



## Use junction instead of symbolic link

그리고 배포를 시도해보면 높은 확률로 다음과 같은 문제에 봉착할 것이다.

```
$ sls deploy
Serverless: Injecting Headless Chrome...
Serverless: Packaging service...
Serverless: Excluding development dependencies...

  Error --------------------------------------------------

  EPERM: operation not permitted, scandir 'C:\Users\jinai\git_projects\job-seek\.build\node_modules'

     For debugging logs, run again after setting the "SLS_DEBUG=*" environment variable.

  Get Support --------------------------------------------
     Docs:          docs.serverless.com
     Bugs:          github.com/serverless/serverless/issues
     Issues:        forum.serverless.com

  Your Environment Information ---------------------------
     OS:                     win32
     Node Version:           8.16.0
     Serverless Version:     1.47.0
```

처음에는 권한 설정이 어디서 꼬였나 하고 한참 삽질을 했지만, 구글링을 열심히 하다 보면 [`.build` 폴더 안의 `node_modules` 파일을 삭제하고 프로젝트 루트경로의 `node_modules`를 복사해 넣으라는 해결책](https://stackoverflow.com/questions/50590798/nodejs-serverless-windows-10/50613829#50613829)을 발견할 수 있다. 이 버그는 [Serverless Plugin Chrome](https://github.com/adieuadieu/serverless-chrome/issues/96)과 Windows 10의 합작품이다. 윈도우 파일시스템이 FAT32를 쓰다가 NTFS로 넘어왔다는 사실은 유명하다. 그 과정에서 남아있는 부산물 중에 junction이라는 게 있는데, 이는 쉽게 말해서 윈도우 폴더에 대한 심볼릭링크라고 볼 수 있다. 여기서 중요한 건, 윈도우는 파일에 대해서만 symbolic link가 동작하고 **폴더에 대해서는 junction만 동작한다**는 점이다. Serverless Plugin Chrome은 `.build` 안에 `node_modules`에 대한 symbolic link를 만들어 쓰고 있기 때문에 오동작이 일어나는 것이다.

`node_modules`를 직접 복사하지 않고 바로가기를 만들어서 넣어주면 될 것 같지만, 직접 실험해보면 안된다. **하지만 [직접 커맨드로 junction을 만들어주면 된다](https://github.com/adieuadieu/serverless-chrome/issues/96#issuecomment-511102141)!** 일일이 명령어 치긴 귀찮으니까 스크립트로 만들어버리자. `package.json`을 열고 `scripts`에 다음 스크립트를 추가해주자.

```package.json
"scripts": {
	...
	"deploy-win": "MKDIR .build && MKLINK /j .build\\node_modules node_modules && sls deploy",
	...
}
```

그리고 다음처럼 스크립트를 실행해주면...

```
$ npm run deploy-win
```

| <img src="https://raw.githubusercontent.com/3jins/Images/master/serverless-deploy-success.png" width="100%" alt="Deploy has successed"/> |
| :----------------------------------------------------------: |
|                          감동 ㅠㅠ                           |

실행결과를 열어보면 어디서 에러가 났다고 메시지가 떠있긴 한데, 어쨌든 배포까지는 정상적으로 됐나보다. 이제 [다음 삽질](https://enhanced.kr/postviewer/143)을 하러 가도록 하자. 


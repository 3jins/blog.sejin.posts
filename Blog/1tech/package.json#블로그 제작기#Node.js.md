[블로그 제작기](http://enhanced.kr/postviewer/7) 시리즈의 글입니다.

package.json이란 npm에서 사용하는 [json](https://www.json.org/json-ko.html) 형식의 패키지 관리 파일이다. 얘가 없으면 그 프로젝트는 npm의 관리 하에 있지 않다고 봐도 된다.

npm 명령어와 package.json의 관계에 대한 내용은 [Node.js & npm](http://enhanced.kr/postviewer/11)을 참고하자.

#### 구조

일단 내 설명보다는 [여기](https://docs.npmjs.com/files/package.json)가 더 정확하다. ~~그러니까 개발자들은 블로그를 멀리 하고 표준문서를 보는 게 낫다.~~ 만약 영어가 극혐이라면 [여기](https://outofbedlam.gitbooks.io/npm-handbook/content/config/package-json.html)나 [여기](http://programmingsummaries.tistory.com/385)가 설명이 자세하고 좋으니 참고하자. 본 포스트에서는 각 항목에 대해 핵심만 짚어서 간단하게만 설명하니 자세한 내용은 다른 문서나 블로그에서 확인해야 한다.

개인적으로 중요하다고 생각하는(어떤 프로젝트를 만들든 간에 무조건 알아야 되는) 항목들에는 볼드처리를 해두었다.

*   **name**: 프로젝트 이름이다. 꼭 써야 된다.

*   **version**: [시맨틱 버저닝](https://semver.org/lang/ko/)을 따르면 되는데 잘 모르겠으면 그냥 npm에서 시키는 대로 1.0.0으로 만들자. name이랑 같이 얘까지는 꼭 써야 된다.

*   description: 프로젝트 설명인데 뭐.. 굳이 채워야하나 싶다.

*   keywords: npm 허브에 공개할 라이브러리 만들 때만 필요하다.

*   homepage: 만든 프로젝트에 대한 공식 블로그가 있다면 적으면 좋다.

*   bugs: 버그리포트를 받을 이슈저장소와 메일주소 등등이다. 

    ```json
    { "url" : "https://github.com/owner/project/issues",
      "email" : "project@hostname.com"
    }
    ```

    이런 식으로 적으면 된다. 물론 안 적는다고 문제가 생기지는 않는다.

*   license: 따르는 [라이센스](https://choosealicense.com/licenses/)가 있다면 적을 수 있다.

*   author: 프로젝트 제작자, 즉, 당신 이름 적으면 된다. 객체 형태로 해서 연락처도 적어도 된다.

*   contributors: 제작자가 여러명이거나 제작자 외에 컨트리뷰터들이 있으면 객체의 배열 형태로 작성할 수 있다.

*   files: `.npmignore` 파일이나 `.gitignore` 파일 내용과 관계없이 절대 무시되지 않는 파일의 목록. 하지만 `README`, `LICENSE`, `package.json` 등 일부 파일은 여기 쓰여있지 않아도 무조건 포함되고 반대로 `.git`, `.*.swp`, `node_modules` 등의 파일이나 폴더는 여기 쓰여있어도 무조건 무시된다. 역시 굳이 따로 설정할 거 없으면 안 써도 된다.

*   **main**: 프로젝트의 시작 지점이 되는 파일을 명시한다. 

*   bin: 

    tl;dr: PATH에 등록되는 실행파일을 추가로 만든다. (표준문서 설명이 복잡해서 자세히 썼다.)

    npm으로 패키지를 설치하게 되면 `-g` 옵션을 주었을 때는 prefix/bin 안에, 로컬설치일 때는 ./node_modules/bin 안에 name값으로 main에 적어둔 파일의 심볼릭링크를 하나 생성하고 PATH에 등록하여 어디서나 패키지명으로 패키지를 실행할 수 있게끔 한다. 그런데 프로젝트 하나에 실행파일이 여러개가 필요한 경우가 있을 수 있다. 이 경우 bin에 

    ````json
    { "bin" : { "myapp" : "./cli.js" } }
    ````

    와 같이 입력하면, `cli.js` 파일의 심볼릭링크를 myapp이란 이름으로 /usr/local/bin 안에 생성한다.

    이해를 돕기 위해 예시를 더 들자면

    ```json
    {
        "name": "sejin",
        "version": "1.0.0",
        "main": "server.js",
    }
    ```

    위와 같은 경우 `npm install sejin`으로 패키지를 설치 후 `sejin`으로 main.js를 실행시킬 수 있고

    ```json
    {
        "name": "sejin",
        "version": "1.0.0",
        "main": "server.js",
        "bin": {"enhanced": "./scripts/enhanced.js"}
    }
    ```

    위와 같은 경우 `npm install sejin`으로 패키지를 설치 후 `sejin`으로 main.js를 실행시킬 수 있고 또한 `bin`으로 ./scripts/enhanced.js를 실행시킬 수 있는 것이다.

*   man: `man` 프로그램이 찾아서 보여줄 매뉴얼 문서. 

*   directories: 패키지의 디렉토리 구조를 뭐 어떻게 적는다는데 아직 사용되는 것 같지도 않고 무슨 소린지 잘 이해하지도 못했다. 

*   repository: 컨트리뷰트할 수 있는 레포타입이랑 주소 쓰면 된다.

*   **scripts**: 사용할 스크립트나 명령들을 등록해둔다.

*   config: 설정값이다. 포트번호 같은 걸 여기에 쓰나보다.

*   **dependencies**: 의존성 모듈의 목록을 시맨틱 버저닝 값과 함께 명시한다. 

    프로젝트를 집에 비유한다면 의존성 모듈은 창문섀시나 기둥처럼 배포 후에도 프로젝트가 굴러가기 위해 필요한 모듈을 말한다.

    시맨틱 버저닝 값 앞에 캐럿(^) 같은 게 붙는데 이에 관해서는 [여기](https://blog.outsider.ne.kr/1041)를 참고하면 좋다.

*   **devDependencies**: 개발용 모듈의 목록을 시맨틱 버저닝 값과 함께 명시한다.

    개발용 모듈이란, 유닛 테스트 등을 위해 개발할 때에는 필요한 모듈이지만 배포하여 사용될 때는 필요 없는 모듈로, 프로젝트를 집에 비유한다면 망치나 톱과 같은 모듈을 일컫는다. 대표적으로 `babel`이 개발모듈에 해당하는데 bundle.js를 생성한 다음에 토사구팽되어도 무방하기 때문이다.

*   peerDependencies: 다른 모듈과 지금의 프로젝트를 함께 사용하는 경우 버전의 호환성을 나타낸다.

*   optionalDependencies: 예비용 의존성 모듈들.

*   bundledDependencies: 무슨 소린지 모르겠다. @_@

*   engines: 호환되는 node의 버전을 지정한다.

*   ~~engineStrict: npm 3.0.0에서 deprecated 되었다. 무시하자.~~

*   os: 사용 가능한 운영체제를 지정한다. `!`를 이용하면 블랙리스트 방식으로 바꿀 수도 있다.

*   cpu: 동작 가능한 cpu를 지정한다. `!`를 이용하면 블랙리스트 방식으로 바꿀 수도 있다.

*   preferGlobal: `true`로 설정되면 global로 설치할 경우 경고를 뿜뿜한다.

*   private: `true`로 설정되면 npm에 패키지를 publish할 수 없다. 회사 내부의 private 프로젝트 등에 사용해서 실수를 방지하자.

*   publishConfig: publish할 때의 설정값들.

*   디폴트값들

    특정 조건이 만족될 때 npm은 package.json에 명시되지 않은 값도 있는 것으로 간주한다.

    *   server.js가 프로젝트 최상위 경로에 있을 경우

        ```json
        "scripts": {"start": "node server.js"}
        ```

    *   binding.gyp가 프로젝트 최상위 경로에 있을 경우

        ```json
        "scripts":{"preinstall": "node-gyp rebuild"}
        ```

    *   AUTHORS가 프로젝트 최상위 경로에 있을 경우

        ```json
        "contributors": [...]
        ```

        ​












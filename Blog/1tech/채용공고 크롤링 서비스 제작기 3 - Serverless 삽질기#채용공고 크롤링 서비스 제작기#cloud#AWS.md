[serverless 환경을 선택한 이유](https://enhanced.kr/postviewer/139)와 [MongoDB Atlas 삽질기](https://enhanced.kr/postviewer/140)에 이어 이번에는 serverless 삽질기다. 



## Serverless?

[Serverless](https://serverless.com/)는 AWS Lambda, Google Cloud Functions, Azure Functions 등 FaaS에의 번거로운 배포과정을 줄여주는 도구다. AWS Lambda 기준으로, Serverless를 통해 프로젝트를 배포하게 되면 Lambda 서비스가 생기고 우리의 코드가 들어감은 물론이고, 알아서 Amazon Cloudwatch Logs와 API Gateway에 Lambda를 연결해주기까지 하여 디버깅이 수월해진다.



## Serverless tutorial

다음의 단계를 거쳐서 배포하게 된다.

1. AWS credential 부여
2. `serverless.yml` 파일 생성
3. 배포

### Prerequisites

serverless를 AWS와 연동하여 쓰기 위해서는 다음의 프로그램들이 깔려 있어야 한다.

- **Node.js & npm**

    ~~아직도 이거 설치 안 한 흑우 없제?~~

- **AWS-CLI**

    [여기](https://docs.aws.amazon.com/ko_kr/cli/latest/userguide/cli-chap-install.html)서 시키는대로 설치하도록 하자. 윈도우 사용자라면 괜히 pip 써서 설치하겠다고 깝치지 말고 msi 파일 받아서 설치하는 게 정신건강에 이롭다. 파이썬과 윈도우는 궁합이 극악이다.

- **serverless**

    ```
    npm install -g serverless
    ```

### AWS credential 부여

AWS credential을 부여하려면 우선 AWS credential을 만드는 게 순서다. 만들러 가보도록 하자.

<img src="https://raw.githubusercontent.com/3jins/Images/master/aws-credential-iam.png" width="80%" alt="IAM page of AWS"/>

서비스 메뉴의 [IAM](https://console.aws.amazon.com/iam/home?region=ap-northeast-2#/home)을 눌러서 들어가보자.

<img src="https://raw.githubusercontent.com/3jins/Images/master/aws-credential-add-user.png" width="80%" alt="A page for adding a user"/>

사용자 메뉴에서 '사용자 추가' 버튼을 눌러준다.

<img src="https://raw.githubusercontent.com/3jins/Images/master/aws-credential-user-info.png" width="80%" alt="User info input page"/>

'AWS Management Console 액세스'는 안 써봐서 모르겠지만 설명으로 봐서는 아마 비밀번호를 쓰는 방식이 아닐까 싶다. 비밀번호를 일일히 치려면 불편하고 안전하지도 않으므로 '**프로그래밍 방식 액세스**'를 선택해서 크레덴셜 키를 이용할 수 있도록 하자. 

<img src="https://raw.githubusercontent.com/3jins/Images/master/aws-credential-permission.png" width="80%" alt="Granting permission page"/>

세 개의 메뉴가 있다.

'그룹에 사용자 추가'는 그룹별로 권한을 주고 관리하겠다는 뜻이다. 관리자가 여러명일 때 쓰는 방식이므로 나 같은 백수에게는 해당사항이 없다.

'기존 사용자에서 권한 복사'는 이미 만들어놓은 사용자의 권한을 그대로 가져와서 쓰겠다는 뜻이다. 나는 클라우드 뉴비니까 이미 만들어놓은 사용자가 있을 리 만무하다.

내가 선택해야 하는 것은 '기존 정책 직접 연결'으로 AWS 측에서 미리 준비해둔 정책을 가져와서 퍼미션을 주겠다는 뜻이다. 봐봤자 뭔 소린지 알아들을 수도 없고, 누가 내 서비스 공격할 것도 아니니까 AdministrationAccess를 주도록 하자.

넘어가면 '태그 추가(선택 사항)' 같은 게 나오는데 크레덴셜이 많을 때 의미가 있는 내용이다. 그냥 넘기면 된다. 마지막 화면에서 정보가 맞게 들어있는지 검토해주고 '사용자 만들기'를 눌러 크레덴셜을 만들어주도록 하자.

감춰져있는 비밀 액세스 키를 복사해서 다음처럼 serverless 명령어를 콘솔에 쳐주면 AWS credential이 부여된다. 이 비밀 액세스키는 해당 페이지를 벗어나면 다시는 확인할 수 없다. 대신 언제든지 다시 생성할 수는 있다. 

```bash
serverless config credentials --provider aws --key 액세스키 --secret 비밀액세스키
```

### Serverless 배포 환경 설정

딱 2개만 필요하다고 보면 된다. 

1. 배포할 소스코드
2. Serverless 설정파일

당장 소스코드가 준비돼있는 것도 아니고 설정파일이 어떤 식으로 생겼는지 모르는 나와 같은 뉴비들을 위해 Serverless에는 템플릿이 준비되어 있다. 다음과 같은 명령으로 `./serverless-practice` 경로에 `hello-world` 템플릿으로 serverless 관련 파일들을 만들어보자.

```bash
serverless create --template hello-world --path serverless-practice
```

`serverless`의 alias는 `sls`다. 따라서 다음과 같이 쳐도 같은 결과를 얻는다.

```bash
sls create -t hello-world -p serverless-practice
```

다음과 같이 3개 파일이 생기는 것을 볼 수 있다.

```
$ ls -la
total 5
drwxr-xr-x 1 SEJIN 197121   0 Jul  2 17:29 ./
drwxr-xr-x 1 SEJIN 197121   0 Jul  2 17:23 ../
-rw-r--r-- 1 SEJIN 197121  86 Jul  2 17:29 .gitignore
-rw-r--r-- 1 SEJIN 197121 385 Jul  2 17:29 handler.js
-rw-r--r-- 1 SEJIN 197121 658 Jul  2 17:29 serverless.yml
```

`.gitignore`는 익숙한 파일이니 넘어가도록 하자.

`handler.js`를 열어보면 다음과 같은 아주 간단한 Node.js 서버 예제가 들어있다. ~~hello world 따위에 CORS 설정을 해놓는 serverless 당신은 도대체...~~ 

```javascript
'use strict';

module.exports.helloWorld = (event, context, callback) => {
  const response = {
    statusCode: 200,
    headers: {
      'Access-Control-Allow-Origin': '*', // Required for CORS support to work
    },
    body: JSON.stringify({
      message: 'Go Serverless v1.0! Your function executed successfully!',
      input: event,
    }),
  };
                                                                                 
  callback(null, response);
};
```

중요한 건 `serverless.yml`으로, `hanlder.js`를 명령어 한 번으로 AWS 인스턴스에 업로드하기 위한 설정파일이다.

```yml
# Welcome to serverless. Read the docs
# https://serverless.com/framework/docs/

# Serverless.yml is the configuration the CLI
# uses to deploy your code to your provider of choice

# The `service` block is the name of the service
service: serverless-hello-world

# The `provider` block defines where your service will be deployed
provider:
  name: aws
  runtime: nodejs10.x

# The `functions` block defines what code to deploy
functions:
  helloWorld:
    handler: handler.helloWorld
    # The `events` block defines how to trigger the handler.helloWorld code
    events:
      - http:
          path: hello-world
          method: get
          cors: true
```

### 배포

다음 명령어를 통해 배포할 수 있다.

```bash
sls deploy
```

이제 도키도키한 가슴을 부여잡고 [역할탭](https://console.aws.amazon.com/iam/home?region=ap-northeast-2#/roles)에 들어가보자. 아래 스크린샷처럼 우리의 예제가 AWS Lambda에 잘 배포된 것을 확인할 수 있을 것이다.

<img src="https://raw.githubusercontent.com/3jins/Images/master/serverless-deploy-result.png" width="80%" alt="serverless deploy result"/>

람다 서비스 대시보드에 들어가서 직접 함수를 실행해보면 다음 스크린샷처럼 응답이 잘 나오는 것을 확인할 수 있다.

<img src="https://raw.githubusercontent.com/3jins/Images/master/aws-lambda-hello-world-success.png" width="80%" alt="successful response screen of AWS Lambda"/>


[블로그 제작기](http://enhanced.kr/postviewer/7) 시리즈의 글입니다.

HTTPS 프로토콜이 지원하는 SSL(Secure Socket Layer) 통신은 이제 웹에서 선택이 아닌 필수로 변해가는 추세다. 기본적으로 SSL 통신의 역할은 패킷내용의 암호화다. 따라서 로그인 같은 기능이 있는 사이트라면 무조건적으로 HTTPS를 지원해야 하지만, 이 블로그처럼 사용자 입력을 전혀 받지 않는 사이트라면 사실 굳이 필요 없을 것 같아보인다. 과연 그럴까..?

SSL을 쓰지 않으면 보안 문제를 제외하고도 다음과 같은 문제가 있다.

1. [HTTP/2를 이용할 수 없게 된다.](https://tech.ssut.me/2017/05/07/https-is-faster-than-http/#spdyandhttp2)
2. [구글의 검색엔진은 HTTPS를 쓰는 사이트를 우선적으로 검색결과에 노출시킨다.](https://webmasters.googleblog.com/2014/08/https-as-ranking-signal.html)
3. ~~대한민국을 포함한~~ 일부 국가정부의 검열대상이 된다. 
4. 이제 SSL 연결설정이 안 된 사이트의 경우 크롬 주소창에 '안전하지 않음'이 아니라 '주의 요함'이 표시되어 오지게 어그로를 끈다. [심지어 70버전 크롬부터는 그 '주의 요함'이 빨간색으로 표시될 예정이다.](https://www.boannews.com/media/view.asp?idx=69625)

그러므로 SSL 연결을 꼭 해야만 했다. 그리고 이 블로그가 기술블로그이며 만든 이유의 반이 '제 웹 개발실력이 이 정도 되니까 저 좀 개발자로 뽑아주세요'인데 2018년에 SSL 연결조차 안돼있으면 얼마나 하찮아 보이겠는가.



## Supporting HTTPS with Express

우선 앱 자체에서 SSL을 지원하게 해야 한다. [Express 표준문서](http://expressjs.com/en/4x/api.html#app.listen)를 보면 다음과 같이 짜라고 한다.

```javascript
var express = require('express');
var https = require('https');
var http = require('http');
var app = express();

http.createServer(app).listen(80);
https.createServer(options, app).listen(443);
```

근데 문서 만드신 분이 술이라도 잡수시고 쓰셨는지 `options`가 뭔지 안 나와있다. 구글링을 해보면 `options`에는 다음처럼 RSA key가 저장돼있어야 한다.

```javascript
var options = {  
    key: fs.readFileSync('./keys/key.pem'),
    cert: fs.readFileSync('./keys/cert.pem')
};
```

그럼 저 key들은 어디서 만들어지느냐. 두 가지 방법이 있는데 하나는 비싼 돈을 내고 공인기관에게서 인증서를 구입해서 얻는 방법이고, 다른 하나는 `openssl`을 사용해서 자체적인 인증서를 만드는 방법이다. 전자는 개인 블로그 운영하는 입장에서는 너무 과하다. 그럼 자체적으로 인증서를 만들면? 브라우저가 다음과 같은 에러를 [뿜뿜](https://www.youtube.com/watch?v=JQGRg8XBnB4)한다.

> NET::ERR_CERT_AUTHORITY_INVALID 

그렇다. 난 공인기관이 아니었던 거시다! 방법이 없을까?



## Let's Encrypt

**[Let's Encrypt](https://letsencrypt.org/)**는 Mozilla, Chrome, Cisco, Akamai, Facebook, GitHub 등 웹과 네트워크의 중요성을 잘 아는 수많은 회사와 단체들이 모여 만든 비영리단체다. 이들은 90일 유효기간의 SSL 인증서를 무료로 뿌린다. 개꿀따리긴 한데 90일마다 신경써서 갱신해주기 은근히 귀찮을 것 같다. 그러나 이게 바로 오픈소스의 힘인 것인가... [greenlock-express](https://www.npmjs.com/package/greenlock-express)는 Let's Encrypt를 Express 기반의 웹사이트에 알아서 연결해 줌은 물론, 갱신까지 알아서 해준다고 한다.

이제 greenlock-express의 README.md가 시키는 대로 서버파일을 좀 수정해주자.

```javascript
/* server/server.js */

...

greenlock.create({
  version: 'draft-11', 
  server: 'https://acme-v02.api.letsencrypt.org/directory',
  email: '당신들의이메일을@여기다가쓰시면됩니다',
  agreeTos: true,
  approveDomains: ['enhanced.kr'],
  configDir: path.join(require('os').homedir(), 'acme', 'etc'),
  app: app,
  communityMember: false,
  telemetry: true,
  renewWithin: 14 * 24 * 60 * 60 * 1000,
  renewBy: 10 * 24 * 60 * 60 * 1000,
}).listen(httpPort, httpsPort);
```

그 다음 빌드하고 서버를 재실행시키면... 짜잔!

| <img src="https://raw.githubusercontent.com/3jins/Images/master/ssl_enhanced.png" width="80%"/> |
| :----------------------------------------------------------: |
| 주소창 옆에 저 녹색 자물쇠를 달기 위해 얼마나 삽질을 했던가 ㅠㅠ |



## 로컬에는 키가 없는데..?

Let's Encrypt는 192.168.99.100과 같은 ip주소에다가는 인증서를 발급해주지 않는다. 발급해준다 한들 내부망의 ip에다가 발급해놓으면 다른 사람의 인증서랑 중복될 수 있기 때문에 또 문제가 생긴다.

그런데 테스트를 위한 로컬환경에는 보통 도메인이 없다. 그래서 서버코드를 greenlock을 쓰도록 수정해놓으면 로컬에서 앱을 실행할 수가 없다.

해결방법은 간단하다. 실행모드에 따라 서버코드를 다르게 짜서 로컬에서는  HTTP로, 실서버에서는 SSL으로 접속하도록 만들면 된다.

```javascript
/* server/server.js */

...

if(process.env.SERVER_ENV === 'development') {
    app.listen(testPort, () => {
        console.log('Express listening on port', testPort);
    });
} else {
    greenlock.create({
        ...
    }).listen(httpPort, httpsPort);
}

...

```


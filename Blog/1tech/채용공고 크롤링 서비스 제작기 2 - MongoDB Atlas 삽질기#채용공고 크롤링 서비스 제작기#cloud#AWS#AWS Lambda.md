[지난 글](https://enhanced.kr/postviewer/139)에 이어 MongoDB Atlas 삽질기를 써보려고 한다. 이 글은 프리티어 사용을 기준으로 하여 작성되었고, 1. 클러스터를 하나 만들어서 2. 기본적인 권한을 설정하고 3. 접속하여 확인해보는 것까지를 다룬다.



## 시작하기

일단 [MongoDB Atlas 사이트](https://www.mongodb.com/cloud/atlas)에 접속하도록 하자.

<img src="https://raw.githubusercontent.com/3jins/Images/master/mongodb-atlas-start-free.png" width="80%"/>

그럼 위와 같은 화면을 볼 수 있다. Start free 버튼을 누르면 스크롤이 아래로 쭉 내려갈 것이다.

<img src="https://raw.githubusercontent.com/3jins/Images/master/mongodb-atlas-get-started-free.png" width="80%"/>

위와 같은 화면이 나타날 텐데 기본적인 정보를 입력해서 가입하고 Get started free 버튼을 눌러서 새 클러스터를 만들어보도록 하자.



## 클러스터 만들기

여러 항목이 있는데 신경 써야 할 부분은 3개 뿐이다. 나머지는 프리티어에서는 어차피 쓸 수 없으니 무시하자.

### provider & region

<img src="https://raw.githubusercontent.com/3jins/Images/master/mongodb-atlas-provider-region.png" width="80%"/>

provider는 아무거나 골라도 크게 상관없다. AWS가 제일 네임드니까 그냥 AWS를 고르도록 하자.

Region은 여러 가지가 있고 Seoul 리전도 있다. 반가운 마음에 저거 눌렀다가는 프리티어가 없어서 당황하는 자신을 발견하게 된다. 내 경우는 다음의 선택 기준을 따라 골랐다.

- FREE-TIER-AVAILABLE 딱지가 붙어 있는 곳 중에 골라야 한다.
- 지역이 가까울 수록 응답 속도가 빨라진다고 하니 가급적이면 아시아 지역에서 고르는 게 좋다.
- AWS Lambda와 같은 지역에 있어야 이득이다. AWS Lambda가 제공되는 지역 중에서 고른다(EC2는 Atlas와 가까워져도 어차피 클라이언트에서 멀어지면 쌤쌤이라 굳이 고려하지 않았다).

### cluster tier

<img src="https://raw.githubusercontent.com/3jins/Images/master/mongodb-atlas-cluster-tier.png" width="80%"/>

무조건 Free forever 붙어있는 M0 Sandbox를 고르면 된다. 회사 서비스라면, 또는 집에 돈이 썩어넘치면 다른 걸 써보도록 하자. 비싸면 더 좋다.

### cluster name

<img src="https://raw.githubusercontent.com/3jins/Images/master/mongodb-atlas-cluster-name.png" width="80%"/>

헷갈리지 않게 이름을 지어주자. 기본값으로 있는 이름이 Cluster0길래 같은 형식으로 지어봤다.

<img src="https://raw.githubusercontent.com/3jins/Images/master/mongodb-atlas-create-cluster.png" width="80%"/>

다 됐으면 맨 아래에 있는 Create Cluster 버튼을 눌러 클러스터를 만들어주자. 참고로 한 프로젝트당 프리티어 클러스터는 하나밖에 못 만든다. 다른 프로젝트를 만들면 프리티어 클러스터를 새로 더 만들 수는 있다.



## 데이터베이스 user 추가

<img src="https://raw.githubusercontent.com/3jins/Images/master/mongodb-atlas-dashboard.png" width="80%"/>

위 스크린샷처럼 상태를 확인할 수 있는 대시보드가 나타날 것이다. 하단의 Get Started 버튼을 눌러보면 시작하기 위한 5단계 중 4개 단계가 남아있다고 나오는데 저기서 시키는대로 그냥 따라하면 된다.

시키는 대로 좌측의 Database Access 메뉴에 들어가보도록 하자.

<img src="https://raw.githubusercontent.com/3jins/Images/master/mongodb-atlas-add-new-user.png" width="80%"/>

권한에 따라 여러 계정을 만들어주면 된다. Job Seek의 경우에는 웹서버에서 추가적인 정보를 입력할 필요가 없기 때문에 EC2용 계정은 read-only로, lambda용 계정은 read/write로 주기로 했다. admin 계정이 굳이 필요한지는 잘 모르겠다.



## 테스트해보기

### IP whitelist 추가

<img src="https://raw.githubusercontent.com/3jins/Images/master/mongodb-atlas-whitelist-ip.png" width="80%"/>

일단 테스트를 위해서 ADD CURRENT IP ADDRESS 버튼을 눌러 현 위치의 IP에서 접속이 가능하도록 해두자. 물론 보안상 취약점이 될 수 있으니 나중에 지워줘야 한다.

나중에 AWS Lambda와 EC2의 고정 아이피를 받으면 추가해주도록 하자.

### sample data 집어넣기

<img src="https://raw.githubusercontent.com/3jins/Images/master/mongodb-atlas-add-sample-data.png" width="80%"/>

다시 대시보드로 돌아와서 저 'Load Sample Dataset'을 눌러주고 좀 기다리면 샘플 데이터가 입력된다.

### 확인

이제 접속해서 확인해보면 된다. 방법은 여러가지가 있으니 개인적으로 편한 방법을 쓰면 된다. 내 경우에는 [MongoDB Compass](https://www.mongodb.com/products/compass)라는 GUI 툴을 평소에 쓰고 있어서 이를 이용해 확인했다.

hostname 등의 정보는 다음 스크린샷처럼 CONNECT 버튼을 눌러서 시키는대로 따라하다보면 알게 된다.

<img src="https://raw.githubusercontent.com/3jins/Images/master/mongodb-atlas-connect.png" width="80%"/>

접속해서 보면 다음처럼 샘플데이터가 들어있는 것을 확인할 수 있다.

<img src="https://raw.githubusercontent.com/3jins/Images/master/mongodb-atlas-mongodb-compass.png" width="80%"/>

확인했으면 프리티어는 용량제한이 있으니 `sample_`로 시작하는 collection을 모두 삭제해주자.
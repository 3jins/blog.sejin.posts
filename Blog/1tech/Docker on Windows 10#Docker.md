맥북러들은 맥북에는 터미널이 내장되어 있다면서 윈도우를 쓰는 개발자들을 종종 기만한다. 

마이크로소프트가 블루스크린과 쓰레기 같은 익스플로러 끼워팔기의 대명사로 널리 각인되어 있던 과거와 달리, 2014년에 [사티아 나델라](https://ko.wikipedia.org/wiki/%EC%82%AC%ED%8B%B0%EC%95%84_%EB%82%98%EB%8D%B8%EB%9D%BC)가 마소의 새로운 최고경영자로 임명되면서 "윈도우가 이걸..?"이라는 말이 나올만큼 윈도우10은 이전과 많이 달라졌다. 그 중 하나가 Bash의 지원이다. 하지만, `apt-get` 하나 내장되어 있지 않은 윈도우10의 bash로는 할 수 있는 게 별로 없다(윈도우 bash로 패키지 설치를 할 수 있는 방법이 있는지 잘 모르겠다. 검색해보니까 WSL을 써서 뭘 어떻게 한다는데 어쨌든 확실한 건 직관적으로 바로 쓸 수는 없다는 거다). 몇 년 지나면 윈도우의 터미널도 많이 진화할 것 같은 느낌이지만, 그 때까지 손 놓고 가만 있을 수도 없고, 맥북을 살 돈도 없다.

나와 같은 고민을 겪고 있는 개발자 또는 개발자 지망생들에게 한 줄기 빛 같은 도구가 있으니 바로 Docker 되시겠다. 도커를 이용하면 윈도우에서 간단히, 별다른 성능 저하 없이도 리눅스 가상환경을 만들어낼 수 있다. 사실 도커가 그런 이유로 만들어진 프로그램은 아니고, 갓갓하신 데브옵스들께서 독립적인 빌드 환경을 만들기 편하라고 있는 프로그램이지만, 어쨌든 난 윈도우에서 리눅스 환경으로 개발이나 테스트를 진행해야 할 때 유용하게 사용하고 있다.

그럼 본격적으로 도커에 대해 알아보도록 하자.



## Docker vs VM

위에서 내가 지껄여놓은 글을 읽어보면 도커가 가상머신의 일종인가 싶을 것이다. 비슷은 한데 좀 다르다.

### Container

가상머신의 경우는 HostOS가 가상머신에게 할당해준 프로세스 안에서 GuestOS 하나를 커널째로 만들어 구동시킨다. 반면, 도커는 [Container](https://www.docker.com/what-container)라는 이름의 격리된 프로세스를 구동시킨다. 이 컨테이너들은 HostOS의 커널을 공유한다고 하며, 그로 인해 빠르게 실행된다고 한다. 

그러니까, 자세한 내용은 나도 잘 모르겠지만, 컨테이너는 필요한 최소한의 설정 몇십메가바이트만 가지고 HostOS에 기생하여 실행되기 때문에 적은 용량으로 빠르게 실행된다는 소리인 것 같다.

### Image

VM에서 쓰는 이미지는 iso파일으로 운영체제 그 자체였다. 이미지를 보관하기만 해도 기본이 10GB라 노트북 용량이 거덜나기 일쑤였다. 반면, 도커의 이미지는 굉장히 가볍다. 도커의 이미지는, 컨테이너가 갖고 있어야 하는 파일 및 설정정보를 갖고 있는 파일이라고 정의된다. 웹툰 덴마에 보면 [평면구속 퀑](https://namu.wiki/w/%ED%80%91#s-5.14)이 등장하는데, 딱 이 느낌이다. (약간의 차이가 있다면, 컨테이너를 이미지로 만든다고 해서 컨테이너가 사라지는 건 아니라는 점 정도?) 만화에서도 활용도가 매우 높은 능력으로 나오는데 도커에서도 이미지 개념은 다음과 같이 활용도가 매우 높다.

* 특정한 환경을 구성하여 이를 박제해뒀다가 그 환경이 필요할 때마다 꺼내 쓸 수 있다.
* [도커허브](https://hub.docker.com/)를 통해 배포할 수도 있다. 
* 이미지가 용량도 겁나게 작아서 컨테이너 채로 백업이 가능하다. 

### Dockerfile

~~VM 너 도커파일 쓰면 편하단다. 느 집엔 이거 없지?~~

도커파일은 이미지를 자동으로 생성할 수 있는 스크립트 파일이다. 예를 들어, 2017년에 특정한 여러 패키지가 설치되어 있는 우분투 이미지가 필요하다고 하자. Ubuntu 16.04 LTS 컨테이너를 만들고 필요한 패키지들을 설치했다. 그리고 18년이 되어 이 이미지를 18.04로 업그레이드하고 싶다면? VM 같으면 이미지파일을 아예 새로 만들어야 한다. 하지만, 도커의 경우에는 Dockerfile을 이용해서 이미지를 만들어서 쓰다가 이미지의 특정 부분만 수정하고 싶으면 Dockerfile에서 그 부분만 수정해서 다시 이미지를 재생산하면 된다. 개꿀?



## Usage

꼭 알아야 하는 몇 가지 기능만 언급하겠다.

### Get an Image

이걸 쳐보자.

```bash
docker images
```

아무것도 안 나올 것이다. 왜냐하면 로컬에 설치돼있는 이미지의 목록을 보여주는 명령이니까.

```bash
docker search ubuntu
```

이건 도커허브에서 이름에 ubuntu가 포함된 이미지를 검색하는 명령이다. 스타 수가 같이 나오는데 스타 많은 이미지가 가장 갓갓하신 분께서 만든 이미지라고 보면 된다.

```bash
docker pull ubuntu
```

이 명령을 이용해서 우분투 이미지를 로컬로 가져올 수 있다. 다시 `docker images` 명령을 치면 ubuntu 이미지가 잘 받아졌음을 확인할 수 있다.

### Make a Container

`run` 명령을 이용한다. 여기에 쓸 수 있는 옵션이 오지게 많다. 일단 기본적으로는 

```bash
docker run -it --name ubuntu-test ubuntu /bin/bash
```

정도 실행해보면 컨테이너가 만들어져서 돌아가는 걸 확인할 수 있다.

* -it: 그냥 닥치고 쳐야되는 옵션이다. 자세히 설명하자면,
  * -i: interactive. 커맨드의 결과가 콘솔에 출력되게 해주는 명령이다.
  * -t: tty. 터미널 환경을 만들어준다.
* --name: 컨테이너의 이름을 설정한다.
* ubuntu: 사용할 이미지의 이름이다.
* /bin/bash: 컨테이너가 시작될 때 자동으로 실행될 프로그램이다. 여기에 /bin/bash이 아니라 데몬을 적어둘 수도 있다.

좀 더 응용해보자. 이번에는 node 이미지를 받아 컨테이너를 만들고 그 위에서 node 프로젝트를 개발할 수 있는 환경을 만들어보겠다.

```bash
docker run\
	-it\
  -p 5913:5913 -p 443:443\
  --name=blog\
  -v /c/Users/jinai/git_projects/blog/blog.sejin:/blog\
  -v /blog/node_modules node\
  /bin/bash
```

* -p: port다. 첫 번째 값은 도커 호스트의 포트, 두 번째 값은 도커 컨테이너의 포트다. 둘을 다른 값으로 줘야 되는 경우는 거의 없다.
* -v(첫번째꺼): volume. HostOS의 특정 디렉토리와 컨테이너 안의 특정 디렉토리를 연결한다. 
* -v(두번째꺼): 이거 왜 했는지 까먹었다. 무슨 역할을 하는건지도 까먹었다. 어쨌든 이거 없으면 패키지 의존성 문제가 생기더라.

### Manage Container

```bash
docker ps
```

이 명령은 실행 중인 도커 컨테이너들을 모두 보여준다.

```bash
docker ps -a
```

이 명령은 생성된 모든 도커 컨테이너들을 보여준다.

```bash
docker start ubuntu-test
```

```bash
docker attach ubuntu-test
```

```bash
docker stop ubuntu-test
```

```
docker rm ubuntu-test
```

설명하기 귀찮으니 직접 쳐보면서 뭔지 알아보도록 하자.



## Docker Toolbox

개인용 윈도우는 Home Edition과 Pro Edition으로 나뉜다. 이 둘 사이에는 여러 차이가 있겠지만, 일단 내가 알기로 Home Edition은 Hypervisor가 지원되지 않는다. 이 때문에 두 운영체제는 쓸 수 있는 도커 환경이 완전히 달라진다.

Pro를 쓰시는 분들은 그냥 도커 공식 홈페이지에 가서 [Docker for Windows](https://docs.docker.com/docker-for-windows/) 깔고 cmd 창에다가 바로 도커 명령어를 쳐서 쓰면 된다. 겁나 편하다. 역시 돈이 최고다.

반면, Home Edition을 쓰시는 분들은 [Docker Toolbox](https://docs.docker.com/toolbox/overview/)이란 걸 써야 한다. Virtual Box에 의존해서 도커 컨테이너를 만들고, Docker Quickstart Terminal이라는 별도의 터미널을 이용해서 Docker Host를 실행시키는 구조다. 그런데 홈페이지 들어가보면 이런 말이 쓰여있다.

> **Legacy desktop solution.** Docker Toolbox is for older Mac and Windows systems that do not meet the requirements of [Docker for Mac](https://docs.docker.com/docker-for-mac/) and [Docker for Windows](https://docs.docker.com/docker-for-windows/). We recommend updating to the newer applications, if possible. 

레거시..... 마음이 아프지만 어쩔 수 없이 이거 써야된다. 처음에 도커 설치할 때 이 사실을 몰라서 많이 헤맸었다. home edition을 쓰는 여러분들은 참고하시길 바란다.
[크로미움 컨트리뷰터되기](https://enhanced.kr/postviewer/1554) 시리즈의 글입니다.

컨트리뷰션을 하기 위한 첫 단계는 당연히 해당 프로젝트의 소스코드를 받아 빌드 및 실행해보는 것이다. 본 포스트에서는 크로미움 소스를 받아 빌드 및 실행해보는 내용을 다루고자 한다.

사실 이 내용은 내가 굳이 블로그에 글을 쓸 필요도 없다. 왜냐하면 [크로미움 문서](https://chromium.googlesource.com/chromium/src/+/master/docs/linux_build_instructions.md)에 엄청 자세하게 모든 게 나와있기 때문이다. 솔직히 저 문서의 링크를 공개한 것으로 본 포스트의 역할은 다 한 것이라고 생각하지만 그냥 내 공부 차원에서 마저 정리하도록 하겠다.



## Meeting the System Requirements

코드를 받으면 된다. 근데 이게 사실 만만한 게 아니다.

> **System requirements**
>
> - A 64-bit Intel machine with at least 8GB of RAM. More than 16GB is highly recommended.
> - At least 100GB of free disk space.
> - You must have Git and Python v2 installed already.

16GB 이상~~(more than이면 초과 아니냐)~~의 램으로 빌드할 것을 **HIGHLY** 권장하고, 디스크 공간은 최소 100GB가 있어야 하며, Git과 레거시인 2점대 파이썬이 필요하다. 흠...

| <img src="https://raw.githubusercontent.com/3jins/Images/master/disk-remain.png" width="25%"/> <img src="https://raw.githubusercontent.com/3jins/Images/master/cpu-performance.png" width="80%"/> |
| :----------------------------------------------------------: |
|                          실화냐...                           |

난 노트북에 결코 적은 돈을 쓰지 않았다. 하지만 시스템 요구사항을 하나도 맞출 수가 없다. ~~심지어 깔려있는 파이썬 및 환경변수들도 Python 3.6이라 그조차도 안 맞는다. 가상환경 만들어서 뭐 어찌어찌하면 될 것 같긴 하지만 어쨌든 얘가 문제가 아니다.~~ 멘토님 말씀이 8기가 램으로 크로미움 소스 빌드하면 대충 24시간 정도 걸려서 빌드시켜놓고 1박 2일 여행 갔다오면 된단다...

방법이 정녕 1박 2일 여행밖에는 없는 것일까. 다행히 자비로우신 구글갓께서는 만인에게 300달러어치의 [GCP(Google Cloud Platform)](https://console.cloud.google.com) 서버자원을 하사하셨다. 여기에 인스턴스를 만들어 빌드를 진행하면 된다.

### GCP (Google Cloud Platform)

가입 같은 건 알아서 하시길 바란다. 해외 결제 가능한 카드 문제나 이런 건 나도 잘 모른다.

메뉴에서 Compute Engine > VM 인스턴스 항목으로 들어간다. 그럼 Compute Engine을 준비 중이라는 문구를 한참동안 볼 수 있다. 깊은 인내심을 가지고 다 될 때까지 기다려보자.

로딩이 끝나고 나면 '만들기' 버튼을 눌러 VM 인스턴스를 만든다. 이 때 주의해서 설정해야 하는 부분이 몇가지 있다.

1. vCPU 많을수록 좋다. 그렇다고 너무 많으면 크레딧 빠지는 속도가 감당이 안되므로 24개 정도로 맞춘다.

   기본적으로는 8개까지밖에 지원이 안된다. 계정을 업그레이드하면 무려 96개까지 사용 가능하다. 업그레이드 후라도 free credit이 남아있으면 돈이 안 빠지므로 그냥 업그레이드하면 된다. 단, 크레딧을 모두 다 쓴 게 아닌지 계속 확인해야 한다. 다음은 [Google Cloud 측의 FAQ 문서](https://cloud.google.com/free/docs/frequently-asked-questions?hl=ko&_ga=2.130436554.-450825139.1534416329#how-to-upgrade)에서 발췌한 내용이다.

   > **Caution:** With an upgraded account, you will be automatically charged after your free credits are fully used or after your credits expire, whichever comes first.

2. RAM은 16GB 이상이면 다 비슷하고 클 수록 돈만 많이 나온다. 최소로 맞춰주도록 한다.

3. 운영체제는 아무거나 해도 된다. Ubuntu 쓰는 게 편하다.

4. Disk는 150GB 이상 할당한다. 200GB 정도 잡아주는 게 좋다.

5. 집에 돈이 쌓여서 밑에 돈이 아야아야 하는거 아니라면 선점가능성을 사용으로 한다.

   선점가능성을 사용으로 맞추면, 전체 서버자원이 모자랄 때 내 인스턴스의 프로세스를 죽이고 다른 인스턴스에서 내 자원을 가져갈 수 있는 대신 가격이 낮아진다. ~~미국시간으로 새벽은 우리나라의 오후다. 오후가 자원을 뺏길 확률이 낮다.~~ 그리고 얘를 사용해야 하는 더 중요한 이유가 있는데, 우리는 잠깐 빌드만 하고 나면 서버를 쓰지 않는다. 계속 켜두게 되면 며칠만에 300달러의 크레딧을 모두 소진하게 되므로 안 쓸 때는 꺼둬야 한다. 그런데 선점가능성이 사용으로 되어있으면 무조건 24시간 이내에 자원이 모자라는 일이 발생해 내 서버가 꺼진다. 그래서 이 옵션을 켜야 한다.

크레딧이 얼마나 남았는지는 [결제 메뉴](https://console.cloud.google.com/billing)에서 확인할 수 있다. 계정 업그레이드를 해놨으니 자주 확인해서 돈 나가는 일 없도록 하자.

### Python 2.7

GCP Instance를 만들면 Python3는 설치가 돼있다. 무시하고 2점대 파이썬을 받아주면 된다.

```
$ sudo apt-get install python
```



## Get the Code

### depot_tools

> The Chromium [depot_tools(7)](http://commondatastorage.googleapis.com/chrome-infra-docs/flat/depot_tools/docs/html/depot_tools.html) suite contains many git workflow-enhancing tools which are designed to work together to enable anyone to wrangle the Chromium codebase expertly.

[depot_tools](http://commondatastorage.googleapis.com/chrome-infra-docs/flat/depot_tools/docs/html/depot_tools.html)는 크로미움 프로젝트에 최적으로 커스터마이즈된 깃 도구 정도라고 생각하면 된다. 진짜 각 잡고 크로미움 코드 수정할 사람이라면 이 도구에 친숙해져야 하므로 [튜토리얼](http://commondatastorage.googleapis.com/chrome-infra-docs/flat/depot_tools/docs/html/depot_tools_tutorial.html)을 자세히 읽어보자.

* **Download**

  ```
  $ git clone https://chromium.googlesource.com/chromium/tools/depot_tools.git
  ```

* **Environment Variable Setup**

  ```
  $ export PATH="$PATH:${HOME}/depot_tools"
  ```

  어디서든 depot_tools를 쓸 수 있도록 하기 위해 PATH 환경변수에 등록해준다. 여기서 ${HOME} 대신 '~'를 쓰면 경로가 제대로 등록되지 않는다는 것에 주의하자.

  근데 그냥 저렇게 명령어 한 번 치고 끝내면 서버가 꺼질 때마다 환경변수가 사라진다. 그러므로 `~/.bashrc` 파일의 맨 끝에다가 저 문장을 넣어준 뒤

  ```
  $ source ~/.bashrc
  ```

  명령을 이용해 영구적으로 환경변수 설정을 해주도록 하자.



### Source Code Cloning

```
$ fetch --nohooks chromium
```

한 20분 정도는 걸린다. 실행시켜놓고 알고리즘 문제라도 하나 풀고 있자.

`--nohooks`이라는 옵션이 있는데 [DEPS](https://dev.chromium.org/developers/how-tos/depottools#TOC-DEPS-file) 파일에 명시된 `hooks`를 무시하라는 옵션이다. 이 옵션을 붙인 이유는 [이 이슈](https://codereview.chromium.org/106403003)에 나와있듯이 아직 일부 빌드 의존성이 안 맞는 상태기 때문에 hooks를 실행시키면 에러가 나기 때문이다.

만약 이미 fetch 받은 chromium 소스코드가 있는데 최신 버전으로 업그레이드를 시켜주고 싶다면 `fetch` 대신 다음 명령어를 이용하면 된다.

```
$ gclient sync
```

이제부턴 모든 작업을 `src` 경로에서 진행하므로 경로를 옮겨주자.

```
$ cd ./src
```



## Build Configuration

### Build Dependencies / Hooks

이제 아까 `--nohooks` 옵션으로 건너뛴 부분을 마저 처리해줄 시간이다. 

* **Install Additional Build Dependencies**

  빌드에 필요한 패키지들을 설치해준다. 컴파일러, 라이브러리 등이 설치된다. 뭐가 깔리는지 궁금한 사람들은 `/build/install-build-deps.sh` 파일을 열어서 보면 된다.

  ```
  $ ./build/install-build-deps.sh
  ```

* **Run the Hooks**

  이제 아까 `--nohooks` 옵션으로 제외시켰던 `hooks`에 등록돼있는 명령들을 수행시켜주도록 하자.

  ```
  $ gclient runhooks
  ```



### Setting Up the Build

```
$ gn args out/Debug
```

빌드 옵션값을 설정해주는 명령이다. 크로미움 문서에는 `Debug` 대신 `Default`로 되어 있는데 뭘로 해도 상관 없다고 한다. 빌드 옵션은 다음과 같이 써주면 된다.

```
enable_nacl = false
is_component_build = true
is_debug = true
symbol_level = 1
use_jumbo_build = true
```

문서에서 뒤에 나오는 'Faster Builds' 부분은 건너뛰어도 된다고 한다.



## Build Chromium

```
$ ninja -C out/Debug chrome
```

20000개 이상의 패키지에 대한 빌드 작업을 수행한다. 아까 GCP에서 코어를 24개를 잡아줬기 때문에 24개씩 동시에 빌드하고 있을 것이다. 한시간 조금 안 되게 걸리는데 한 패키지 빌드하는데 평균 3초 정도 걸린다고 보면 진짜 그 정도 걸린다. ($3 \times {{20000} \over {24}} = 2500$)



## Run Chromium

```
$ out/Debug/chrome
```

리모트 환경이라면 GUI를 지원하지 않으므로 실행되지는 않을 것이다. 아래와 같이 나온다면 잘 된 것이다. 

```
$ out/Debug/chrome
(chrome:5029): Gtk-WARNING **: 10:07:40.294: cannot open display: 
```

자신의 컴퓨터가 슈퍼 짱짱해서 크로미움 정도는 거뜬히 받아 빌드할 수 있다 하시는 분들이라면 [이 동영상](https://www.youtube.com/watch?v=rMbJj-48VwY&feature=youtu.be)과 같이 크롬이랑 비슷하게 생긴 브라우저가 뜰 것이다. 
[B to Y about Git](https://enhanced.kr/postviewer/629) 시리즈의 글입니다.



## Repository

Git은 프로젝트를 Repository(저장소, 이하 레포) 단위로 관리한다. 깃헙과 같은 깃 서버에 올라가있는 레포는 원격(remote) 레포라고 부르고, 사용자가 가지고 있는 레포는 로컬(local) 레포라 부른다. 

모든 레포는 그 레포에 관한 설정이나 브랜치 정보 등을 보관하는 `.git`이라는 디렉토리를 가지고 있다. `.git`을 가지고 있는 디렉토리를 Git은 레포로 인식한다.



## Creating a Git Repostory

레포를 만드는 데에는 두 가지 방법이 있다. 하나는 원격 레포를 먼저 만들고 로컬 레포에서 이를 클론하는 방법, 다른 하나는 로컬 레포를 먼저 만들고 원격 레포를 동기화하는 방법이다. 



### 1. Create a Remote Repository and Clone It to The Local

다음 그림처럼 우측상단의 '+' 버튼을 눌러 'New Repository' 버튼을 선택한다.

<img src="https://raw.githubusercontent.com/3jins/Images/master/new_repository.png" width="50%"/>

적으라는 것들이 좀 있다. 테스트용이니까 대충 적어서 만들어보면 되는데 잘 모를만한 것들 몇가지만 설명하겠다.

- Private

  자신과 자신이 선택한 사람들만 해당 레포를 볼 수 있게 된다. 돈 내고 프리미엄 계정으로 업그레이드하면 쓸 수 있다. 학생이라면 학교메일을 이용해 학생인증을 하면 무료로 쓸 수 있다.

- Initialize this repository with a README 

  README 파일은 레포에 대한 설명을 마크다운 문법으로 작성한 파일으로 해당 레포의 얼굴이라 보면 된다. 체크하면 레포이름과 레포설명을 이용해서 자동으로 최소한의 파일만 하나 만들어주고 **Initial Commit**을 하나 만들어준다. 여기서는 체크를 하든 말든 크게 상관이 없지만(체크하는 게 좋다), 로컬 레포를 원격 레포로 옮겨야 하는 경우에는 체크하면 안된다.

- .gitignore

  DB파일이나 인증토큰 등 오픈되어서는 안되는 파일들, IDE 등 로컬환경에 따라 필요할 수도 필요없을 수도, 또는 그 내용이 다를 수도 있는 파일들, 또는 깃헙에 올리기에는 용량이 너무 큰 파일들이 레포지터리 안에 존재할 수 있다. 이런 파일들은 .gitignore 파일을 만들어서 그 안에 써두면 자동으로 Git이 인식대상에서 제외한다. 일일이 만들기 번거롭고 놓치기 쉬운 부분이 있을텐데 https://www.gitignore.io/라는 곳에 가서 사용하는 기술스택을 입력하면 .gitignore 파일을 자동으로 만들어준다.

- license

  오픈소스 프로그램에 대해 어느 정도까지의 사용을 허락할 것인지를 명시하는 라이센스다. MIT, GNU, BSD 등 다양한 라이센스가 있고, 각각 조금씩 다르다. 가장 느슨한 라이센스가 MIT인 것으로 알고 있다.

원격 레포를 다 만들었으면 다음처럼 원격레포의 클론을 위한 주소를 복사한다.

<img src="https://raw.githubusercontent.com/3jins/Images/master/github_clone.png" width="50%"/>

이제 로컬의 Git 터미널을 열어서 git clone [붙여넣기]를 해보자. Git이 자동으로 원격 레포로부터 파일들을 받아오고 로컬 레포를 원격 레포에 연결할 것이다.

### 2. Create a Local Repository and Set An Upstream to Remote

이번엔 로컬에서 레포를 먼저 만드는 방법이다.

```bash
$ mkdir 레포이름
$ cd ./레포이름
$ ls -a
./	../	
$ git init
$ ls -a
./	../	.git/
```

로컬에 레포가 만들어졌다. 원격 레포에 동기화를 시켜주려면 뭔가 커밋이 있어야 한다.

```bash
$ touch README.md
$ git add .
$ git commit -m "Initial commit"
```

이제 원격레포주소를 연결해주고 업스트림을 만들어주자. `-u` 옵션은 `--set-upstream` 옵션의 a.k.a.다.

```bash
$ git remote add origin 원격레포주소.git
$ git push -u origin master
```


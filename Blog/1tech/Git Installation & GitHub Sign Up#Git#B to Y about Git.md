[B to Y about Git](https://enhanced.kr/postviewer/629) 시리즈의 글입니다.

Git과 GitHub을 사용하기 위한 준비를 해보자. PC에 Git을 설치해야 하고, GitHub에 가입하면 된다.



## Git Installation

자신의 운영체제에 따라 조금씩 방법이 다르다. [여기](https://git-scm.com/book/ko/v1/%EC%8B%9C%EC%9E%91%ED%95%98%EA%B8%B0-Git-%EC%84%A4%EC%B9%98)서 시키는대로 따라하면 되겠다.

### Configuration

설치 후에는 기본적인 설정이 필요한데, 이 두 줄만 쳐주면 된다.

```bash
$ git config --global user.name "본인 이름"
$ git config --global user.email "본인@메일주소"
```

설정이 잘 됐는지 확인하기 위해 다음 명령어를 이용한다.

```bash
$ git config --list
```

이 설정값은 이후 행하는 모든 커밋에 서명처럼 기록된다. 



## GitHub Sign Up

이건 뭐.. 그냥 가입하면 된다. 알아서 가입하자.

Username은 다른 사람과 중복되지만 않으면 아무거나 해도 상관없지만, 가급적 사람들이 외우기 쉽고 자신임을 알아볼 수 있는 이름으로 선택하는 게 좋다. 제일 좋은 건 본명을 쓰는 것이지만, 이미 쓰고 있는 사람이 있거나 개성을 부여하고 싶다면 기억하기 쉽거나 본인의 이름을 유추할 수 있는 이름으로 고르자. [얘](https://github.com/oh3vci)처럼 이상한거 쓰면 협업시에 본인 아이디 알려주기도 어렵고, 포트폴리오로 쓰기도 어려워진다.


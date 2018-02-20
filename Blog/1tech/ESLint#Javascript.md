일전에 아는 형이 나에게 이런 말을 했다.

> 누가 잘못했는지 들어봐봐. 해커톤을 나갔는데 A가 ESLint 설정을 걸어버렸어. 해커톤은 그냥 코딩만 하기도 바쁜데 ESLint를 거기다 걸어버리는 게 말이 돼?

난 이렇게 대답했다.

> 엥, ESLint가 왜요?

지금부터 ESLint가 무엇이길래 저 형이 저런 말을 했으며, 내가 왜 A의 편을 들어줬는지 알아보도록 하자.



## Lint

ESLint는 Lint의 일종이다. 파이썬에서는 Pylint를 쓰고, 찾아보니 ktlint, SwiftLint, Lint4j, Golint, ruby-lint 등 언어마다 각자의 Lint가 있는 것 같다. 

프로그래밍에서 Lint라 하면, 정해진 코딩 스타일을 벗어난 코드를 자동으로 검사해 에러나 경고를 띄워주는 도구를 의미한다. Lint를 사용하게 되면 코드가 깔끔해진다. 특히, 협업을 진행할 때 서로의 코딩 스타일이 안 맞는 경우를 원천봉쇄할 수 있으며, 컨벤션이 다른 여러 프로젝트를 동시에 진행한다 해도 헷갈릴 일이 없어진다.

쓸데없는 궁금증일 수 있지만 왜 이런 도구를 Lint라고 부를까? [위키백과](https://en.wikipedia.org/wiki/Lint_(software))를 찾아보면 다음과 같은 문장을 찾아볼 수 있다.

> The term "lint" was derived from the name of the undesirable bits of fiber and fluff found in sheep's wool.

그리고 이런 의미로 만들어진 최초의 Lint는 [C언어의 스타일 검사를 위한 Unix 유틸리티](http://citeseerx.ist.psu.edu/viewdoc/summary?doi=10.1.1.56.1841)였다고 한다. 이 이후로 모든 언어 스타일 검사기는 나오는 족족 *Lint*라는 이름을 달고 나오게 되었다.



## ESLint in Node.js

위에서 말했다시피 자바스크립트를 위한 린트는 ESLint다. Node.js 환경에서 ESLint를 어떻게 적용하는지 알아보도록 하자. 물론 내 블로그 따위보다는 [공식문서](https://eslint.org/docs/user-guide/getting-started)가 더 정확하고 자세하다. 여기서는 잡다구리한 내용은 다 빼고 어떤 식으로 쓰는지 감을 잡기에 좋도록 아주 최소한의 내용만 다루도록 하겠다.

### eslintrc

ESLint에서 사용할 규칙을 적어두는 설정파일이다. 보통 `.eslintrc` 내지는 `.eslintrc.json`이라는 이름의 json 파일 형태로 만들지만, [5가지의 파일포맷](https://eslint.org/docs/user-guide/configuring#configuration-file-formats)을 사용할 수 있으므로 편한대로 쓰도록 하자.

```json
{
  "extends": "airbnb",
  "rules": {
    "linebreak-style": 0
  }
}
```

위는 json 형식의 `.eslintrc` 파일의 예시다. 설정을 어떻게 맞춰야 할지 잘 모르겠으면 그냥 위처럼 쓰면 중간은 간다.

`extends`는 이미 존재하는 스타일가이드의 설정을 그대로 가져오겠다는 뜻이다. 이슬람교에는 코란이 있고 기독교와 천주교에는 성경이 있듯이 자바스크립트에는 [Airbnb Javascript Style Guide](https://github.com/airbnb/javascript)가 있으므로 이를 따르면 무난하다.

`rules`에서는 상속해온 설정 이외에 새로운 설정을 추가하거나, 맘에 안 드는 설정을 빼버릴 수 있다. 모든 rule의 목록은 역시 [공식문서](https://eslint.org/docs/rules/)에서 확인 가능하다. 내가 빼버린 `linebreak-style`은 줄바꿈을 LF(`\n`) 또는 CRLF(`\r\n`) 중에 하나로 강제하겠다는 설정인데, 어차피 Git에 커밋할 때 알아서 한 쪽으로 맞춰지기 때문에 신경쓸 필요가 없는 설정이며, 윈도우 사용자와 유닉스계열 사용자가 협업할 수 없도록 만드는 악랄한 설정이므로 운영체제 박애주의를 실현하기 위해 빼버렸다.

### package.json

`eslintrc` 파일만 만들어줬다고 해서 알아서 이를 바로 적용할 만큼 Node.js가 똑똑하지는 못하다. 필요한 모듈을 별도로 설치해줘야 한다.

Airbnb 스타일 가이드를 따를 경우, 다음의 모듈이 필요하다. (`eslint`는 당연히 설치해야 한다.)

```
"eslint-config-airbnb": "^17.1.0",
"eslint-plugin-import": "^2.14.0",
"eslint-plugin-jsx-a11y": "^6.1.2",
"eslint-plugin-react": "^7.11.1",
```

물론 버전은 최신꺼 쓰면 된다. 귀찮아서 내 `package.json` 파일에서 긁어왔을 뿐... 배포할 때는 굳이 없어도 되므로 웹사이트의 성능을 위해 `--save-dev` 옵션을 줘서 `dev-dependencies`에 추가해주도록 하자.

### IDE Plugin

지금까지 해놓은 작업만으로도 Node.js를 실행시킬 때 eslint는 작동할 것이다. 하지만 이는 빌드를 진행하기 전까지는 어디가 잘못됐는지 모른다는 뜻이므로 불편하다. 각자 사용하는 IDE에 플러그인을 설치해주면 알아서 잘못된 부분에 바로바로 밑줄을 그어주니 생산성 향상을 위해 플러그인까지 설치해주자.

내 경우는 웹스톰을 사용한다. JetBrains에서는 [설치 가이드](https://www.jetbrains.com/help/webstorm/eslint.html)를 친절하게 캡쳐화면과 함께 제공해주니 보고 따라하자. Atom, VSCode, 그 외 기타 다른 IDE를 쓰시는 분들께는 구글신의 가호가 함께하길 바란다.



## 蛇足

아까 그 형은 왜 이 좋은 eslint를 사용한 게 잘못되었다고 했을까? 무릇 아무리 좋은 도구라 해도 사용하기에 따라서 그 효과는 크게 달라지기 마련이다. 칼로 요리나 수술을 할 수도 있지만 살인을 할 수도 있고, 원자력으로 전기를 만들 수도, 폭탄을 만들 수도 있다. 

A군은 공동으로 작업하는 프로젝트에 이미 개발이 진행된 상태에서 해커톤 당일에 협의 없이 ESLint 설정을 넣어버렸다. 이로 인해 그 형은 이미 만들어둔 코드를 수정하는 데에 많은 시간을 허비하게 된 것이니 충분히 화날 수 있는 상황이었다고 생각한다.

하지만 그래도 eslint는 쓰는 게 좋다.
추후에 변경될지도 모르지만, 일단 초기 설계를 글로 남겨놓고자 한다. 이 글을 읽기 전에 불쌍한 관종 애송이 개발자를 위해 [여기](https://github.com/3jins/emojicode-transpiler)에 들어가서 스타를 눌러주도록 하자.



## 전체 목표

두 가지에 초점을 맞춘다.

1. emojicode를 이모지 입력기 없이도 쉽게 짤 수 있도록 transpiler를 만든다. 거창하게 형태를 바꾸는 것까지는 아니고, 어셈블리처럼 이모지와 내가 정의한 키워드를 1:1로 매핑시키려 한다. 구현 목표 섹션에서 자세히 후술한다.
2. npm 패키지로 만들어 배포해본다.



## 구현 목표

다음과 같은 세가지 형태의 코드를 transpile해주는 것을 목표로 삼았다.

### traditional

Emojicode의 예시를 먼저 보자.

```text
🏁 🍇
   💭 Get things up and running here...
   😀 🔤Hello World!🔤❗️
🍉
```

이건 언어를 처음 배우면 한 번씩 출력해본다는 헬로월드 예제다. 🏁는 메인로직이 시작됨을 의미하고, 🍇와 🍉은 각각 scope의 시작과 끝을 의미한다. 💭은 한 줄 주석, 은😀은 콘솔출력, 🔤​는 그 사이에 문자열이 들어감을, ❗️은 statement의 끝을 알린다. 

이걸 보고 나니, 우리가 익숙한 다른 언어에서 흔히 나오는 키워드로 이들을 대체할 수 있겠다는 생각이 들었다. 일반적인 언어의 문법으로 다시 한 번 보도록 하자. 

```text
main {
  // Get things up and running here...
  print "Hello World!";
}
```

이건 언어를 처음 배우면 한 번씩 출력해본다는 헬로월드 예제다. `main`은 메인로직이 시작됨을 의미하고, `{`와 `}`은 각각 scope의 시작과 끝을 의미한다. `//`은 한 줄 주석, `print`는 콘솔출력, `"`는 그 사이에 문자열이 들어감을, `;`은 statement의 끝을 알린다. 

사실상 똑같다. emojicode-transpiler는 두번째 코드를 첫번째 코드로 변환해주는 것을 하나의 목표로 한다.

### GFM

GFM이란, Github-Flavored-Markdown의 줄임말이다. GFM은 이모지를 나타내는 표현도 정의하고 있다. 🏁​는 `:checkered_flag:`와 같이 쓸 수 있고, ​🍇​는 `:grapes:`와 같이 쓸 수 있다. 위에 나온 헬로월드 예제는 GFM을 이용해서 쓰면 다음과 같이 쓸 수 있다.

```text
:checkered_flag: :grapes:
  :thought_balloon: Get things up and running here...
  :grinning: "Hello World!":exclamation:
:watermelon:
```

emojicode-transpiler는 위와 같은 코드 역시 Emojicode로 바꿔줄 것이다.

### Mixed

이모지코드의 본래 문법과 실용성을 모두 잡기 위해 두 문법을 다음처럼 섞어쓰고자 하는 사람들도 있을 것이다.

```text
:checkered_flag: {
  // Get things up and running here...
  :grinning: "Hello World!";
}
```

이 역시 emojicode 문법으로 번역해주고자 한다.



## 개발환경

사용 언어를 무엇으로 할까 고민해봤다. 선정기준은 다음과 같았다.

- 런타임이 아니라, 컴파일타임에 전처리하는 프로그램이므로 성능은 크게 고려하지 않아도 된다.
- 내가 쓸 줄 아는 언어여야 한다(이 프로젝트에 너무 많은 시간을 쏟을 생각이 없다).
- 닭 잡는데 소 잡는 칼을 쓰지 말자.
- Emojicode와의 궁합이 좋아야 한다.

### 성능

전처리용이므로 성능이 막 엄청 중요하지는 않을 것이라 생각한다. 물론 웹팩 같은 애들 빌드시간 오래 걸리는 건 답답하긴 하지만, 일단 프로젝트가 잘 되고 답답한 사람들이 생기면 알아서 새로운 거 만들겠지 뭐...

### 쓸 줄 아는 언어

[본 시리즈의 서론](https://enhanced.kr/postviewer/133)에서 말했다시피 붕 뜬 시간에 잠깐 하는 프로젝트다. 임원면접 날짜 잡히면 하던거 그만 두고 면접 준비에 올인해야 한다.

### 우도할계 ㄴㄴ

성능 최적화할 거라고 당장 프로토타입도 안 나왔고 사람들 관심도 없는 프로젝트에 C++을 써서 짠다는 건 좀 과하다는 느낌이 든다. 애초에 난 소 잡는 칼을 잘 못 다룬다.

### Emojicode와의 궁합

사실 이게 제일 중요한 기준이다. 다른 걸 다 충족해도 요게 안 맞으면 눈물을 머금고 버려야 한다. 정말 운이 좋게도 [공식 문서](https://www.emojicode.org/docs/guides/yarn.html)에 보면, Emojicode의 패키지 관리는 Node.js 진영에서 쓰는 [Yarn](https://github.com/yarnpkg/yarn/)을 이용한다고 한다. Node.js를 써도 된다는 뜻이다. 개꿀따리 개꿀따.

### 결론

~~답정너 같지만~~ Node.js를 쓰기로 했다.



## 초기설계

크게 다음의 네 모듈로 나누어 개발하고자 한다.

### match.js

내가 정의한 키워드와 이모지 문자를 매핑해주는 모듈이다. 그냥 하나의 Javascript Object이다. 

```javascript
{
  gfm: {
    grapes: '🍇',
    ...,
  },
  traditional: {
    separator: {
      '{': '🍇',
      ...,
    },
    keyword: {
      main: '🏁',
      ...,
    },
    ...,
  }
  boundary: {
    pair: [
      ['abc', 'abc'],
      ['"', '"'],
      ...,
    ],
    prefix: [
      'crayon',
      '$',
      ...,
    ],
    ...,
  },
}
```

~~아니 하이라이팅 왜 저따위야...~~ 대충 위와 같은 형태로 만들 생각이다.

- gfm
    - 구현 목표 섹션에서 설명한 바와 같다. 각 GFM 키워드는 두 개의 콜론으로 감싸져 있으므로 다른 생각할 필요 없이 왼쪽부터 순서대로 읽으면서 콜론 사이에 있는 걸 막 바꿔주면 된다.
- traditional
    - gfm과는 다르게 좀 더 복잡한 형태를 띈다. gfm은 `:crayon::new:`처럼 그냥 두 키워드가 붙어있어도 단순무식하게 변환해주면 됐지만, traditional에서는 `mainvar`와 `main var` 같이, 두 키워드 사이에 whitespace나 구분자가 들어가는지의 여부가 해석을 다르게 만들 여지가 있다.
- boundary
    - code 섹션과 non-code 섹션을 나누는 구분자를 모아두었다. non-code 섹션의 예로는 주석, 문자열, 변수명 등이 있을 것이다.

### separate.js

code 섹션과 non-code 섹션을 나눈다. 예를 들어, 아래와 같은 코드가 있다고 하자.

```
:checkered_flag: {
  :thought_balloon: Get things up and running here...
  :grinning: "Hello World!:abc:;
:watermelon:
```

이 코드를 separate()에 넣고 돌리면 다음과 같은 결과가 반환되어야 한다.

```javascript
[
  {
    type: 'code',
    content: ':checkered_flag: :grapes:\n  :thought_balloon:',
  },
  {
    type: 'comment',
    content: ' Get things up and running here...',
  },
  {
    type: 'code',
    content: '\n  :grinning: "',
  },
  {
    type: 'string',
    content: 'Hello World!',
  },
  {
    type: 'code',
    content: ':abc:;\n:watermelon:\n',
  },
],
```

### transpile.js

separate에서 나눈 섹션 중 code 부분을 match.js의 객체를 기반으로 분석하여 Emojicode로 해석하는 모듈이다. 카카오가 좋아하는 문자열 파싱 문제를 푸는 느낌이 들 것 같다.

### merge.js

separate.js가 나눈 코드를 transpile한 뒤, 다시 하나의 코드로 합치는 작업을 수행한다.
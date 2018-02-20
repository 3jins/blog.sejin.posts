백수 취준생이지만 마음을 급하게 먹지 않았다. 상반기에는 정말 가고 싶은 회사만 몇 군데 최소한으로 지원하고, 대신 준비를 철저히 하자는 전략을 세웠다. 그 덕택에 지금처럼 면접결과를 기다리는 동안 붕 뜬 시간이 생겼다. 기술면접 준비하는 동안 이론공부를 오지게 해서 이걸 지금 또 하기는 싫고, 코딩을 했으면 좋겠는데 맨날 수정하던 블로그 말고 뭔가 새로운 걸 하고 싶었다.

그러던 중 단톡방에서 친구가 재밌는 걸 들고 왔다. [Emojicode](https://github.com/emojicode/emojicode)라는 언어인데, [아희](https://aheui.github.io/aheuicon), [난해한 혀엉... 언어](https://gist.github.com/xnuk/d9f883ede568d97caa158255e4b4d069), [Whitespace](https://en.wikipedia.org/wiki/Whitespace_(programming_language)), [Brainf*ck](https://en.wikipedia.org/wiki/Brainfuck), [Malbolge](https://en.wikipedia.org/wiki/Malbolge) 등의 언어들과 마찬가지로, 실용성은 개나 줘버리고 재미삼아 만든 언어의 일종으로 보였다.

| <img src="https://raw.githubusercontent.com/3jins/Images/master/emojicode-init.png" width="80%"/> |
| :----------------------------------------------------------: |
|                 emojicode-transpiler의 시작                  |

이 언어를 잘 살펴보면 다음과 같은 특징이 눈에 들어온다.

- 가독성이 나쁘지 않다.

    의미에 맞는 이모지를 키워드나 구분자로 사용하고 있어서 문법을 몰라도 뭘 하는 프로그램인지 추론할 수 있을 정도다. 게다가 키워드 대신 이모지를 쓰기 때문에 코드의 길이까지 짧다.

- 새삥이다. 

    2016년 1월에 [첫 커밋](https://github.com/emojicode/emojicode/commit/69cb7a5f0dad40b07f5d9956fe0abbc109baed2b)이 있고, 2016년 6월에 [첫 정식 릴리즈인 0.2 버전](https://github.com/emojicode/emojicode/releases?after=v0.2.2)이 있다. 자세한 건 모르겠지만, 0.4 버전을 건너뛰고 [0.5 버전](https://github.com/emojicode/emojicode/releases?after=v0.5.1)에서 'ground-breaking release'를 내놓았는데 이게 2017년 7월이다. 아마 이 때부터 본격적으로 세상에 알려지지 않았나 추측해본다. 이는 다시 말해, 내가 이 언어를 가지고 뭔가를 하면 최초의 타이틀을 따기 쉬운 노다지라는 의미다.

- 힙하다.

    이유는 잘 모르겠지만 힙스터들은 이모지를 좋아하는 경향이 있다.

실용성 면에서 이모지코드의 단점이라면 작성하기가 매우 불편하다는 점일 것이다. 위의 짤에서 나온 말 중 하나처럼, 사실상 상형문자나 다름이 없다. 하지만 이 단점만 보완한다면 꽤 쓸만한 언어가 되지 않을까 하는 생각이 들었다. 이런 이유로 나는 정상적인 프로그래밍 언어 문법과 이모지코드 언어 문법을 매핑해주는 [emojicode-transpiler](https://github.com/3jins/emojicode-transpiler)를 만들기로 결정한다.



## Index

### 제작기

1. [emojicode-tanspiler 초기 설계](https://enhanced.kr/postviewer/134)

### Transpiler

1. [Babel의 내부 1 - 전체 동작 흐름](https://enhanced.kr/postviewer/135)

### NPM
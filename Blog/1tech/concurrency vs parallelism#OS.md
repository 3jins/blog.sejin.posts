2017년에 운영체제 수업을 들은 적이 있다. 은퇴 직전의 교수님이었는데 강의 전달력이 아주 심각하셨다. 한 학기 내내 교수님은 매 수업시간 같은 말씀을 반복하셨는데 당시 수업을 듣던 우리는 그 뜻을 도저히 헤아리지 못했다.

> concurrent와 parallel은 다른 거야. concurrent는 한글로 병렬이고 parallel은 병행인데.. 아니 반댄가? 어쨌든 거기 너, 그래서 그 차이가 뭐라고? concurrent는 스레드가 동시에 수행되지만 같이 수행되는 건 아니고, parallel은 그렇지 않다 이거야. 그래서 이게 바로 운영체제야!

... 과장 안 보태고 실화다. 그 땐 구글링도 오지게 못해서 인터넷에서도 답을 못 찾았고 교재로 쓰던 공룡책에도 답이 없었던 걸로 기억한다. 이젠 그 답을 알게 되었으니 그 때 교수님이 우리에게 그렇게 강조하고 싶어하셨던 그 내용이 무엇인지 이번 글에서 이해해보도록 하자.



## parallelism

Parallelism은 완벽한 동시실행이다. 두 스레드가 parallel하게 실행된다고 하면, 둘 사이에는 context switching이 일어나지 않는다. 두 스레드는 공유자원에 정확히 같은 시간에 접근하는 것 또한 가능하다. Java 등 많은 언어에서 지원하는 thread가 parallelism의 대표적인 예가 된다.

<img src="https://raw.githubusercontent.com/3jins/Images/master/parallel.png" width="38%"/>

여러 스레드를 parallel하게 실행하기 위해서는 CPU가 core를 여러개 가지고 있어야 한다. Core는 CPU 안에서 기초 연산을 담당하는 부분으로, parallel한 실행은 각 코어에게 스레드를 하나씩 할당하여 이루어진다. 



## concurrency

Concurrency는 parallelism을 흉내낸 것이다. 두 스레드가 concurrent하게 실행된다는 것은 결국 한 번에 하나의 작업밖에 실행되지 않는 것이며, 스레드 간 context switching이 일어난다. Node.js의 싱글스레드 비동기방식이 concurrency의 대표적인 예시다.

<img src="https://raw.githubusercontent.com/3jins/Images/master/concurrent.png" width="35%"/>



## 사족

우리 교수님은 저 두 단어를 한국어로 번역하면 뭐라 부르는지 매번 헷갈려하셨다. 덕택에 나도 헷갈린다. 명확하게 짚고 넘어가자. parallelism은 '병렬'이고, concurrency는 '동시'다. 그냥 영어 어원 그대로(para: 옆, allo: 다른 / con: 같은, curreer: 흐르다) 번역하면 된다. ㅎㅎ



## 참고

[https://bytearcher.com/articles/parallel-vs-concurrent/](https://bytearcher.com/articles/parallel-vs-concurrent/)
원래 나는 작년(2017년) 여름에 디자인패턴에 대해 공부를 할 예정이었다. 그러나 당연히 불합격할 줄 알았던 소프트웨어 마에스트로에 합격하고 휴학을 결정하게 되면서 1년 반 동안 일정이 너무 빡빡해졌고 디자인패턴의 공부는 한없이 미뤄져 지금에 이르게 되었다.

디자인패턴은 소프트웨어 설계에서 빈번하게 발생하는 문제들을 정형화된 해결책으로 미리 만들어둔 것들을 의미한다. 디자인패턴이 소프트웨어의 설계에 있어서 [은탄환](https://en.wikipedia.org/wiki/Silver_bullet)은 아니지만, 많은 문제를 깔끔하게 해결할 수 있고, 코드의 구현방식을 남에게 설명하거나 설명받을 때에도 디자인패턴을 알고 있는 편이 좋다~~고 한다~~.

대체로 디자인패턴에 대해 설명한 책이나 글들은 Java와 같은 객체지향 언어를 예시로 들어 서술한다. 이는 디자인패턴 개념이 유명해진 시초가 GoF(Gang of Fours)의 ***Design Patterns: Elements of Reusable Object-Oriented Software***라는 책이었기 때문이다. 자바스크립트 역시 객체지향 패러다임을 따를 수 있는 언어지만, 대부분의 객체지향 언어가 클래스 기반인 것과 달리 자바스크립트는 프로토타입 지향(prototype-oriented, class-less) 언어이므로 구현형태가 다소 다른 경우가 많다. 본 시리즈에서는 자바스크립트 코드로 각 디자인패턴을 어떻게 구현하는지 알아볼 것이다.



## Patterns

* [싱글톤 (singleton)](https://enhanced.kr/postviewer/114)

- [데코레이터 (decorator)](https://enhanced.kr/postviewer/132)

- 옵저버 (observer)
- 팩토리 (factory)
- 프록시 (proxy)
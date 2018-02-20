Oracle JDK에 적용되던 BCL 라이센스가 2018년 7월 부로 연 단위 유료 구독형 라이센스로 개편되면서 자바를 새로 설치할 때마다 어떤 버전을 써야 할지 헷갈리기 그지 없다. 



## 무엇을 써야 하는가

일단 Java10 버전부터 적용되는 유료 라이센스는 기업 입장에서는 유료지만, 혼자 자바를 공부하는 목적으로 쓸 때는 무료다. 하지만 어차피 취업하면 회사에서 쓰는 자바를 써야 하기 때문에 얼씨구 무료구나하고 알아보지도 않고 막 쓰면 안 될 것 같은 느낌이 든다. 그러니까 쓰기 전에 어떤 차이가 있는지 잘 알아보도록 하자.

### OpenJDK vs Oracle JDK

OpenJDK는 JCP라는 단체에서 관리하는 Java 표준 명세의 구현체다. Oracle JDK는 이 OpenJDK에 애플릿 등의 일부 프론트엔드스러운 기능을 더 얹은 것이라고 보면 된다. 백엔드 서버를 짜는데에는 OpenJDK를 쓰나 Oracle JDK를 쓰나 별 차이가 없다는 게 [학계의 정설](https://namu.wiki/w/%ED%95%99%EA%B3%84%EC%9D%98%20%EC%A0%95%EC%84%A4)이다.

### TCK Authorization

의외로 Oracle JDK 뿐만 아니라 OpenJDK 역시 Oracle이 운영한다. 자바의 구현명세를 완벽히 충족한 구현체에 대해서 Oracle은 TCK 인증을 해주고 있다. TCK 인증을 통과한 JDK 구현체는 모두 OpenJDK의 기능을 포함하고 있기 때문에 어떤 것을 사용해도 무방하다.

### 결론

아무거나 써도 된다. 회사 가서만 Oracle JDK 설치하지 않도록 하자.



## Open JDK 설치

### Linux

[https://openjdk.java.net/install/](https://openjdk.java.net/install/)

### OSX

[https://github.com/AdoptOpenJDK/homebrew-openjdk](https://openjdk.java.net/install/)

### Windows

[https://github.com/ojdkbuild/ojdkbuild](https://openjdk.java.net/install/)


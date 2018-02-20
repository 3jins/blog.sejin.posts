스프링 프로젝트를 만드는 방법은 진짜 여러 조합이 가능하다. 여러 블로그, 책, 강의 등을 뒤져보면 초기 프로젝트의 모양이 가지각색임을 알 수 있다. 초심자 입장에서는 아무것도 모르기 때문에 괜히 아무거나 따라했다가 시대에 뒤쳐진 기술이나 배우고 있지는 않을까 고민할 수도 있다. 하지만 초심자가 배우는 게 뭔 깊이가 있다고 그런 고민을 하겠는가. 일단 닥치는대로 아무거나 따라해보자. 조금 따라하다 보면 어떤 기술의 조합으로 프로젝트를 만들고 있으며, 그 중에 어떤 게 고인물 기술인지가 서서히 눈에 들어올 것이다. 그 때 여러 기술을 비교해보고 자신의 상황에 적당한 기술로 갈아타도 늦지 않다.



## Spring Boot

본 시리즈에서는 일단 스프링 부트를 사용할 계획이다. 스프링부트는 스프링에서 자주 쓰는 설정대로 자동으로 만들어주는 도구다. [공식 홈페이지의 가이드](https://spring.io/guides/gs/spring-boot/)에 따르면 **Spring Initializr**라는 도구를 사용하면 스프링부트 프로젝트를 쉽게 만들 수 있다고 한다.



## Initial Running

일단 무턱대고 기본구조만 만들어서 실행해보도록 하자.

### Spring Initializr

본 글에서는 인텔리제이 얼티메이트 버전에서 제공하는 기능을 이용해서 프로젝트를 생성하고자 한다. 대학생이라면 학교메일 인증하고 얼티메이트 버전을 받아 쓸 수 있다. 대학생이 아니고 얼티메이트 버전이 없다면.. 음... 유감이다. 주변에 라이센스 갖고 있는 사람 찾아서 프로젝트 만들고 메일로 압축해서 보내달라고 하든지 아니면 [여기](https://start.spring.io/) 들어가서 빈 칸 채우면 알아서 만들어진다고 한다.

<img src="https://raw.githubusercontent.com/3jins/Images/master/spring-init-service-url.png" width="80%"/>

위 그림처럼 new project 열어서 왼쪽에 있는 Spring Initializr를 눌러주자. ~~e가 빠진 건 처음 작명할 때 낸 오타였을까 의도했던 것일까가 문득 궁금해진다.~~

<img src="https://raw.githubusercontent.com/3jins/Images/master/spring-init-metadata.png" width="80%"/>

프로젝트의 메타데이터를 대충 맞춰준다. 프로젝트명, 아티팩트, 패키지는 연동돼있으니 프로젝트명만 수정하면 되고, 어떤 빌드도구(Gradle or Maven)와 어떤 버전의 자바를 쓸지 정도를 결정하면 되겠다. 다른 건 크게 안 중요하니까 취향껏 바꾸든가 말든가 알아서 하자.

<img src="https://raw.githubusercontent.com/3jins/Images/master/spring-init-dependencies.png" width="80%"/>

무슨 라이브러리에 의존할 것인지 고르면 되는데, 우리 같은 애송이가 뭘 알겠는가. 괜히 애먼거 건드리지 말고 그냥 넘기자.

<img src="https://raw.githubusercontent.com/3jins/Images/master/spring-init-name-location.png" width="80%"/>

이건 아까 메타데이터 설정할 때 물어봐놓고 또 물어본다. 마음이 바꼈으면 바꾸든가 아니면 그냥 finish 해주면 된다.

### 알아서 다 해준대매 ㅠㅠ

이대로 실행시키면 잘 돌아갈 것만 같다. 실제로 돌려보면 다음 그림처럼 희망적인 메세지를 볼 수 있다.

<img src="https://raw.githubusercontent.com/3jins/Images/master/springboot-started.png" width="80%"/>

하지만 현실은 그렇게 녹록치 않다.

| <img src="https://raw.githubusercontent.com/3jins/Images/master/localhost-8080-denied.png" width="50%"/> |
| :----------------------------------------------------------: |
|                           ???????                            |

사실은 아까 dependency를 아무것도 설정하지 않은 게 문제였다.

<img src="https://raw.githubusercontent.com/3jins/Images/master/springboot-build-gradle-path.png" width="80%"/>

요 파일을 열어보면 맨 아래에 dependency를 명시해놓은 항목이 있을 것이다.

```gradle
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
}
```

implementation의 저 `spring-boot-starter`를 다음처럼 `spring-boot-starter-web`으로 바꿔주자.

```gradle
dependencies {
    implementation 'org.springframework.boot:spring-boot-starter-web'
    testImplementation 'org.springframework.boot:spring-boot-starter-test'
}
```

그럼 아래처럼 에러페이지를 만날 수 있다. 여기까지 하면 스프링 프로젝트를 일단 만든 것이다. ~~살다살다 에러페이지 보고 좋아할 줄은...~~

<img src="https://raw.githubusercontent.com/3jins/Images/master/whitelabel-error-page.png" width="80%"/>
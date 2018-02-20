지금까지 나에게 Spring이라 하면 악명 높은 대한민국 전자정부 프레임워크 때문에 레거시라는 이미지가 강했다. 하지만 한 때 전세계를 장악했던 자바 진영에서 가장 유명하고 잘 만들어진 프레임워크였던 스프링은 과거에 웬만한 서비스에 다 사용되었고, 이런 스프링을 실제 서비스 중인 코드에서 완전히 걷어낸다는 것은 불가능에 가깝다는 것을 알게 되었다. 일례로 네이버의 경우 Node.js로 서버 코드를 전환하려고 노력 중이지만 여전히 대부분의 서버는 스프링으로 구현되어 있다고 한다.

난 스프링에 대해 정말 1도 모른다. 학교 커리큘럼이 자바를 워낙 좋아하다 보니 기본적인 수준의 자바는 다룰 줄 알지만 자바로 서버를 짜본 적은 없다. 스프링 기본서라는 '토비의 스프링'을 펼쳐봤지만, 당장 환경설정도 어떻게 해야 할지 감이 안 오는데 이 책은 그런 거 다 제껴놓고 리팩토링부터 시작한다. 차근차근 보면 자연스럽게 구조가 머리에 들어올지는 몰라도 2000페이지에 걸친 리팩토링을 따라 가고 있을만큼 내가 여유롭지는 않았다.

초심자가 좀 더 빠르게 전체적인 윤곽을 잡을 수 있는 친절한 튜토리얼이 있으면 좋겠다는 생각에 내가 공부한 내용들을 하나씩 블로그에 올리기로 했다. 본 시리즈는 자바와 웹에 대한 기본 지식이 있지만 스프링에 대해서는 전혀 모르는 사람을 위해 굵직한 뼈대를 잡는 내용을 위주로 작성될 예정이다.



##  Index

1. [OpenJDK vs Oracle JDK](https://enhanced.kr/postviewer/127)
2. Spring 시작하기
    - [Spring Boot initialization](https://enhanced.kr/postviewer/143)
    - Spring Boot Starter
    - Spring project structure
3. Managing dependency
    - [Dependency configuration](https://enhanced.kr/postviewer/148)
    - Gradle 더 잘 다루기
4. DI
    - [Spring의 실행 흐름](https://enhanced.kr/postviewer/145)
    - Bean Wiring 
5. Testing
    - JUnit
    - Mockito를 이용한 외부시스템 테스트
6. Database connection
    - MyBatis
    - JPA & Hibernate
7. Project configruration
    - application.property & application.yml
    - application.yml 분리하기
8. AOP (Aspect Oriented Programming)
    - Aspects, Filters, Interceptors
9. Session management
    - session management in Spring
    - session management in Spring Boot
10. OAuth
11. Using frontend framework with Spring


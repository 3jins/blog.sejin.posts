이 블로그를 처음 만들기 시작하고 디자인을 어떻게 만들지 없는 미적감각으로 고민하던 때가 있었다. 당시 내가 아는 한 디자이너 친구는 나에게 이런 말을 해줬다.

> 너희 개발자들은 코드 만들 때는 **남의 코드 잘 가져다 쓰면서** 디자인 할 때는 왜 남의 디자인 참고 안 하냐?

디자인 얘기를 할 것처럼 서론을 풀었지만 사실 오늘 할 이야기는 남의 코드를 잘 가져다 쓰는 방법, 그 중에서도 스프링 프로젝트에 한정된 얘기다.



## 모듈과 의존성

지금으로부터 불과 5년 전만 해도 개발자들에게 자바스크립트는 HTML을 깔짝깔짝 움직이기 위한 장난감 정도로 치부됐던 적이 있었다. 하지만 지금 자바스크립트 진영은 엄청나게 성장하여 프론트엔드는 물론 백엔드, 모바일, 심지어 데스크탑 어플리케이션까지 장악해 나가고 있다. V8 엔진, 모질라의 지원 등 여러 요인이 있긴 했겠지만 자바스크립트가 이만큼 성장할 수 있었던 큰 동력 중 하나는 의존성 관리 도구 [NPM](https://enhanced.kr/postviewer/6)이 아니었을까 한다.

자바스크립트 뿐만 아니라 웬만큼 성장한 언어는 유명한 의존성 관리 도구를 갖고 있다. 파이썬의 PIP, 루비의 RubyGems가 대표적인 예라 할 수 있다. 현대의 소프트웨어 개발은 [뉴턴이 쳤던 거인의 어깨 드립](https://en.wikipedia.org/wiki/Standing_on_the_shoulders_of_giants)처럼 다른 사람들이 개발해놓은 수십, 수백 가지의 모듈 위에서 이루어진다. 이 각 모듈은 항상 같은 형태가 보장되지 않는다. 기능 개선이나 버그 수정 등의 이유로 버전이 계속해서 올라가고 호환되지 않는 버전의 모듈을 가져오면 [2년 전 소프트웨어 마에스트로 과정에서 개발했던 우리 팀의 안드로이드 프로젝트](https://github.com/SoftMilkTea/Camcha.Android)처럼 아예 고장나서 실행조차 되지 않기도 한다. 따라서 이를 간단한 스크립트 기반으로 자동으로 관리해주는 도구의 사용은 필수적일 수밖에 없다.



## Dependency management tools for Java/Kotlin

5년 전의 PHP마냥 서서히 죽어가고 있지만 우리나라에서는 좋다고 쓰고 있는 자바와 죽어가는 자바를 대체해가는 코틀린 역시 의존성 도구를 가지고 있다. 유명한 게 Ant, Maven, Gradle의 3가지인데 Ant는 이미 관뚜껑 닫고 누웠으니 무시하고 Maven과 Gradle만 간단히 비교해보자.

|                       | Gradle                       | Maven                            |
| --------------------- | ---------------------------- | -------------------------------- |
| should be written in  | Groovy                       | XML                              |
| flexibility           | **더 유연하다고 함**         | 안 되는 게 있다고 함             |
| build performance     | **빠름**                     | 느림                             |
| dependency management | 여러 프로젝트 동시 관리 가능 | 동시 관리가 되긴 하는데 어색해짐 |

위 표는 [Gradle 홈페이지에 나와 있는 내용](https://gradle.org/maven-vs-gradle/)을 간단히 정리한 표다. 난 아직 애송이라 flexibility나 dependency management는 봐도 잘 이해가 안 되지만, 어쨌든 Gradle이 더 빠르고, XML을 약혐하기 때문에 Gradle을 더 선호한다.



## Spring + Gradle

### Gradle 기반 프로젝트 구조

```
root/
  ├ build.gradle
  ├ gradle/
  │  └ wrapper/
  │     ├ gradle-wrapper.jar
  │     └ gradle-wrapper.properties
  ├ gradlew
  ├ gradlew.bat
  └ src/
```

- build.gradle

    기본 빌드 설정 파일. 어떤 모듈을 어떤 순서로 컴파일할지 Groovy 문법으로 기록한다.

- gradle/wrapper

    Gradle wrapper의 배포파일(gralde-wrapper.jar)과 설정파일(gradle-wrapper.properties)이 들어있다.

- gradlew, gradle

    각각 Unix 계열 운영체제와 윈도우에서 동작하는 Gradle Wrapper 명령어다.

### Gradle Wrapper

Gradle Wrapper는 적절한 버전의 Gradle을 알아서 찾아 불러오는 모듈이다. Gradle에 명령을 직접 전달하지 않고 Wrapper를 거쳐서 전달한다고 보면 되겠다. 

자세한 내용은 지식이 짧아 생략한다. 이후 보강하도록 하겠다.

### build.gradle를 이용해서 의존성 관리하기

Spring Boot Initializr는 기본적인 build.gradle 파일을 만들어준다. 다른 부분은 일단 건드리지 말고 `dependencies` 부분만 보도록 하자.

- `implementation`

- ~~`compile`~~

    Gradle 3.0부터 deprecated되었다. 현재는 호환성을 위해 `api`와 동일하게 동작한다.

- `api`

- `compileOnly`



## Spring + Maven

당장 쓸 게 아니니 나중에 다뤄보도록 하자.


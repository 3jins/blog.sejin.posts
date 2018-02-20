Spring 프로젝트의 소스코드를 열어보면 복잡한 폴더구조와 설정파일 때문에 다소 당황스러운 느낌이 든다. 이는 스프링의 실행흐름이 다른 자바 프로그램과 다르기 때문이다. 



## 사전 지식

### IoC (Inversion of Control)

보통 프로그램을 짜면 개발자가 직접 애플리케이션 코드에서 필요한 라이브러리를 불러와 사용한다. 즉, 의존성 객체의 생성 및 관리를 개발자가 직접 하는 것이다. 반대로 Spring에서는 **Container**, 또는 **IoC Container**라는 개념이 있어서, 개발자가 아니라 컨테이너가 제어권을 가져간다. 이를 가리켜 **제어 역전 (IoC, Inversion of Control)**이라 부른다.

### DI (Dependency Injection)

자바 클래스 `Wizard`에서 의존객체 `thorns`를 사용하는 방법은 다음처럼 2가지가 있다.

```java
public class Wizard {
	private Staff thorns;
	
	Wizard () {
		thorns = new Staff(/*level: */55, /*magicAttack: */70);
	}
}
```

```java
public class Wizard {
  private Staff thorns;
  
  Wizard(Staff thorns) {
    this.thorns = thorns;
  }
}
```

후자처럼 클래스 외부에서 의존객체를 생성해서 넣어주는 방법을 **의존성 주입(DI, Dependency Injection)**이라 한다. 위자드의 스태프를 `thorns`에서 `evelWings`로 바꿔야 한다고 생각해보자. 전자의 경우에는 Wizard의 소스코드를 직접 수정해야 한다. 하지만 후자의 경우에는 Wizard의 코드는 건드리지 않아도 된다. 즉, **의존성 주입으로 인해 의존객체와의 결합성이 느슨해지는 효과를 얻을 수 있는 것**이고, 이는 [개방-폐쇄 원칙](https://en.wikipedia.org/wiki/Open%E2%80%93closed_principle)에 더 부합하는 코드라고 볼 수 있겠다.

### POJO (Plain Old Java Object) & Spring Beans

[EJB]([https://ko.wikipedia.org/wiki/%EC%97%94%ED%84%B0%ED%94%84%EB%9D%BC%EC%9D%B4%EC%A6%88_%EC%9E%90%EB%B0%94%EB%B9%88%EC%A6%88](https://ko.wikipedia.org/wiki/엔터프라이즈_자바빈즈))에 반대되는 개념으로 자바 이외의 다른 환경과 기술에 종속되지 않는 자바객체를 의미한다. Spring은 EJB 대신 POJO를 사용하는 프레임워크다. 'Spring이 가볍다'는 말은 여기서 기인한다.

스프링에서는 의존 객체를 Bean이라 부르며, Bean은 POJO이다. [JavaBeans](https://stackoverflow.com/a/3295517/7258660)와는 다른 개념이니 헷갈리지 말자. 



## Spring 애플리케이션의 기본 흐름

Spring을 사용하지 않는 Java 프로그램은 다음과 같이 흘러간다.

1. 객체 생성
2. 객체가 직접 의존 객체 생성
3. 의존 객체 사용

Spring을 사용하는 Java 프로그램은 그 흐름이 다소 달라진다. 

1. 스프링 컨테이너가 의존 객체들(Beans) 생성
2. 객체 생성
3. 스프링 컨테이너에 의해 객체에 의존 객체 주입
4. 의존 객체 사용


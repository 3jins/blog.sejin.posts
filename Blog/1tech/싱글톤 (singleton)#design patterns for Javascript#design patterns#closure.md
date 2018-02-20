[design patterns for Javascript](https://enhanced.kr/postviewer/113) 시리즈의 글입니다.

프로그램의 여러 객체에서 사용되는 공통적인 값이 있을 수 있다. 그 값은 설정값일 수도 있고 특정 리소스의 url일 수도 있다. 사용범위가 매우 넓고 그 호출이 빈번하다면, 또는 객체들 간에 공유되어야 하는 같은 값이라면, 전역변수 또는 클래스변수로 선언하는 게 메모리 사용량이나 코드의 가독성 면에서 더 유리할 수 있다.

이 때, 만약 공통적인 값이 값이 아니라 객체(예를 들어 데이터베이스 커넥션 풀 객체)라면 어떨까. 객체는 클래스로부터 만들어진다. 아무리 객체를 전역변수로 선언한다 하더라도 클래스가 있는 이상 불필요한 중복객체가 만들어질 가능성은 남아있는 셈이다.

싱글톤 패턴을 사용하면 여러 번 객체선언을 하더라도 최초 한 번만 객체가 생성되게끔 할 수 있다. 싱글톤 패턴의 동작방식은 다음과 같다.

1. 자기자신을 대신할 전역객체 `instance`를 준비한다.
2. 싱글톤 클래스의 생성자에서 `instance`가 비어있는지 확인하고, 비어있을 때만 자신을 초기화하여 `instance`에 집어넣는다.
3. `instance`를 자신인 양 반환한다.



## Implementation in Java

### Eager Initialization

싱글톤의 가장 기본적인 형태다. 생성자를 `private`으로 선언하여 클래스 외부에서의 선언을 원천봉쇄하고, 프로그램이 처음 실행될 때 초기화된 static 객체를 프로그램 전역에서 쓰게 하는 것이 핵심이다. 

```java
public class Configuration {
  private static Configuration instance = new Configuration();
  private int numCores = 4;
  
  private Configuration() {}
  
  public static Configuration getInstance() {
    return instance;
  }
  
  public int getNumCores() { return numCores; }
  public void setNumCores(int numCores) { this.numCores = numCores; }
}
```

### Lazy Initialization

객체를 프로그램 시작시에 로드하지 않고, 필요해졌을 때 로딩하는 방법이다. 필요할 때 호출하는 메소드인 `getInstance`에서 `instance`가 `null`인 경우에만 선언해준다.

```java
public class Configuration {
  private static Configuration instance = null;
  private int numCores = 4;
  
  private Configuration() {}
  
  public static Configuration getInstance() {
    if (instance == null) instance = new Configuration();
    return instance;
  }
  
  public int getNumCores() { return numCores; }
  public void setNumCores(int numCores) { this.numCores = numCores; }
}
```



## Implementation in Javascript

자바에서 싱글톤을 구현하는 핵심은 **`private`에 의한 외부 초기화 방지**였다. 자바스크립트에서 `private`를 쓰기 위해서는 **closure**가 필요하다. 사실 얼마 전에 본 라인플러스 인턴 면접에서 나온 질문이었다. 단순히 클로저에 대한 질문인 줄 알았는데 왠지 면접관의 표정이 뭔가 아쉬워보이더니 아무래도 이 부분까지 대답하기를 기대한 것 같다.

Eager implementation은 생략하고 lazy implementation 방식으로만 구현해보도록 하겠다.

### without class syntax (ES5 Implementation)

```javascript
var Configuration = (function() {
  // private variables made by the closure
  var instance;	
  var numCores;
  
  function init() {
    return {
      // public methods
      getNumCores: function() { return numCores; },
      setNumCores: function(_numCores) { numCores = _numCores },
    }
  }
  
  return {
    getInstance: function () {
      if (!instance) instance = init();
      return instance;
    },
  };
})();
```

Configuration 변수에 즉시실행함수를 할당한 이유는 외부함수의 존재 이유가 클로저 뿐이기 때문에 빨리 죽어서 클로저를 내놓으라는 의도다. 

### with class syntax (ES6+ Implementation)

ES6부터는 자바스크립트에서도 `class`를 쓸 수 있다.

```javascript
let instance;
class Configuration {
  constructor() {
    if (instance !== undefined) return instance;

    this.numCores = 0;
    this.getNumCores = () => this.numCores;
    this.setNumCores = (numCores) => this.numCores = numCores;
    this.instance = this;
  }
}

const configuration = (new Configuration());
export default configuraiton;
```

ES6의 `class`는 기본적으로 `private` 문법을 지원하지 않는다. 대신 `export` 문법을 이용해서 선언된 객체만을 모듈의 외부에 공개하면 싱글톤 형태가 된다.

### with static syntax (ES7+ Implementation)

더 이상 찝찝하게 `instance`를 `class` 밖에 둘 필요가 없다. `private` 없는 것만 빼면 Java와 똑같이 구현 가능하다.

```javascript
class Configuration {
  static instance;
  constructor() {
    if (instance !== undefined) return instance;
    
    this.numCores = 0;
    this.getNumCores = () => this.numCores;
    this.setNumCores = (numCores) => this.numCores = numCores;
    this.instance = this;
  }
}
```



## Issues about singleton pattern

싱글톤을 쓸 때에 주의할 점 두 가지가 있다.

### 남용

처음 C언어나 Java 등의 언어를 배울 때, 전역변수나 정적변수를 최대한 쓰지 말라는 말을 들어봤을 것이다. 싱글톤은 전역변수들이 그렇듯이, 객체를 메모리에 적재하여 내리지 않는다(C++은 소멸자를 이용하거나 포인터를 직접 건드릴 수 있기 때문에 싱글톤 객체를 destroy할 수도 있는 모양이다). 따라서 무분별하게 싱글톤 객체를 생성하는 것은 메모리 누수로 이어질 수 있다.

전역 객체를 쓰는 대신 싱글톤을 써서 얻는 가장 큰 이점은 '프로그램 시작시가 아니라 필요할 때 객체를 초기화할 수 있다'는 점이 아닐까 한다. 이게 이점이 되는 경우가 과연 얼마나 있을지 잘 생각해보면 나와 같은 애송이 수준에서는 사용을 지양해야 하는 패턴이라는 결론에 도달할 것이다.

### 멀티스레드

여러 스레드가 동기화 처리가 제대로 안 된 상태에서 동시에 싱글톤 객체에 접근하려고 하면 하나만 존재해야 할 싱글톤 객체가 여러개 만들어지는 일이 생길 수 있다. 이를 막기 위한 방법은 다음과 같이 몇 가지가 있다.

1. 스레드가 나뉘기 전에 싱글톤 객체를 미리 만들어둔다. Eager initializatiwon 형태를 따르는 것도 방법이 된다.
2. `getInstance` 메소드를 동기화시킨다. 대신 성능저하를 감수해야 한다.
3. 언어에 따라 알려진 테크닉을 사용한다. Java의 경우 LazyHolder라는 방식이 제일 널리 쓰이는데, 클래스로더의 객체 초기화 시점을 이용하는 이디엄이다. 

Node.js의 경우, 멀티 스레드란 개념이 없기 때문에, 이 부분에 대해 크게 신경 쓸 부분이 없다. 비동기 코드가 실행된다면, `instance`에는 Promise 객체가 들어있을 것이다.


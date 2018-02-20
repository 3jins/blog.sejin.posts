믿기지 않을 수도 있지만, [자바스크립트는 객체지향 언어](https://en.wikipedia.org/wiki/Object-oriented_programming#OOP_in_dynamic_languages)이기도 하다. 다만 널리 알려진 자바 같은 정통 OOP 언어와는 조금 다른, OOP의 한 갈래인 [프로토타입 기반 프로그래밍](https://ko.wikipedia.org/wiki/%ED%94%84%EB%A1%9C%ED%86%A0%ED%83%80%EC%9E%85_%EA%B8%B0%EB%B0%98_%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D) 언어라고 볼 수 있다.

자바스크립트는 `class`라는 개념이 없다(ES6에서 class 문법이 도입되었지만, 이는 prototype을 기반으로 기존에 우리가 알던 class의 모습을 흉내낸 것일 뿐이다). 대신, 자바스크립트는 객체지향의 기본 개념인 상속, 다형성을 **프로토타입**이란 개념을 통해 구현하고 있고, **모듈 패턴**을 통해 캡슐화 개념을 구현하고 있다. 본 포스트에서는 자바스크립트의 프로토타입에 대해서 다뤄보도록 한다.

상속과 다형성을 어떻게 구현하는지는 여기서 다루지 않는다. 실행문맥, `this`, binding 등에 대한 지식이 필요하기 때문이다. 



## new

Java의 경우, `new` 키워드를 통해  `class`를 객체화하고 해당 객체의 주소값을 반환한다. Javascript도 이와 유사하다. 다만 `new`가 `class` 대신 `function`을 통해 객체를 생성하고 그 주소를 반환한다는 점이 다르다. (사실 Javascript의 `new`가 하는 일이 [한 가지](https://developer.mozilla.org/ko/docs/Web/JavaScript/Guide/Inheritance_and_the_prototype_chain#prototype_%EA%B7%B8%EB%A6%AC%EA%B3%A0_Object.getPrototypeOf) 더 있는데 지금 보면 헷갈리니까 뒤에서 보도록 하자.)

[웹툰 좀비딸](https://comic.naver.com/webtoon/list.nhn?titleId=715772)에 나오는 두 고양이를 객체로 만들면서 `new`의 사용법을 익혀보도록 하자.

| <img src="https://raw.githubusercontent.com/3jins/Images/master/kimaeyong-and-takealook.png" width="80%"/> |
| :----------------------------------------------------------: |
|                       김애용과 때껄룩                        |

```javascript
function Cat(pattern, cryingSound) {
  this.pattern = pattern;
  this.cryingSound = cryingSound;
  this.say = function() {
    console.log(this.cryingSound);
  };
  this.attack = function() {
    console.log('nyang-nyang punch!');
  };
}

var kimaeyong = new Cat('cheese', 'Aeyong!');
var takealook = new Cat('black', 'Take a look~');

kimaeyong.say(); // Aeyong!
kimaeyong.attack(); // nyang-nyang punch!
console.log(kimaeyong.pattern); // cheese
takealook.say(); // Take a look~
takealook.attack(); // nyang-nyang punch!
console.log(takealook.pattern); // black
```

대충 위와 같이 쓸 수 있다.



## prototype

위의 예시는 kimaeyong과 takealook이 가진 `attack` 함수가 동일함에도 각기 메모리를 잡아먹는다는 문제가 있다. 고양이가 지금 2마리니까 별로 심각해보이지 않을 뿐, 수천마리의 고양이를 만들어야 한다면 낭비가 아닐 수 없다. 

다음처럼 공통적으로 쓰는 값이나 멤버는 프로토타입 체인에 넣어줌으로써 해당 문제를 해결할 수 있다.

```javascript
function Cat(pattern, cryingSound) {
  this.pattern = pattern;
  this.cryingSound = cryingSound;
  this.say = function() {
    console.log(this.cryingSound);
  };
}
Cat.prototype.attack = function() {
  console.log('nyang-nyang punch!');
};

var kimaeyong = new Cat('cheese', 'Aeyong!');
var takealook = new Cat('black', 'Take a look~');

kimaeyong.say(); // Aeyong!
kimaeyong.attack(); // nyang-nyang punch!
console.log(kimaeyong.pattern); // cheese
takealook.say(); // Take a look~
takealook.attack(); // nyang-nyang punch!
console.log(takealook.pattern); // black
```

### prototype chain

위의 결과를 이해하기 위해서는 프로토타입 체인에 대해 알아야 한다. 자바스크립트의 객체는 함수 또는 [객체 리터럴](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Operators/Object_initializer)을 통해 생성된다.

```javascript
var obj = new Object();
```

위의 예시는 `Object` 함수와 `new` 키워드를 통해 빈 객체를 생성한 것이다. `new`가 인자로 받은 함수는 **생성자**라고도 부른다. 생성자와 `new`를 통해 만들어진 객체는 생성시점에 해당 객체에 prototype 체인을 생성하게 된다.

`객체.멤버` 또는 `객체.함수`를 호출하면, 현재 객체에서부터 프로토타입을 거슬러 올라가면서 해당 값이 있는지를 찾는다. 맨 꼭대기인 `Object.prototype`까지 다 뒤져도 해당 값이 없다면, 그 때야 비로소 다음과 같은 에러를 뱉는다.

> Uncaught TypeError: 객체.함수 is not a function

```javascript
var obj = Object();
console.log(Object.getPrototypeOf(obj)); // {}

var kimaeyong = new Cat('cheese', 'Aeyong!');
console.log(Object.getPrototypeOf(kimaeyong)); // Cat { attack: [Function] }

var takealook = new Cat('black', 'Take a look~');
console.log(Object.getPrototypeOf(takealook)); // Cat { attack: [Function] }

console.log(Object.getPrototypeOf(kimaeyong) === Object.getPrototypeOf(takealook)); // true
```

여기서 `kimaeyong`과 `takealook`의 프로토타입이 같은 객체임에 주목하자. (객체는 원시자료형이 아니기 때문에 똑같이 생겼어도 주소가 다르면 다른 객체로 취급된다. 예를 들어 `{} === {}`는 `false`를 반환한다.) 위의 예시로부터 **같은 생성자로 만든 객체는 프로토타입을 공유한다**는 사실을 알 수 있다. 그래서 `Cat.attack()`은 `Cat`으로부터 생성된 모든 객체가 공유하게 된다.



## 주의점

다음은 [공식문서](https://developer.mozilla.org/ko/docs/Web/JavaScript/Guide/Inheritance_and_the_prototype_chain#%EA%B2%B0%EB%A1%A0)에서 직접 언급하고 있는 주의점들이다.

### prototype of builtin object

빌트인 객체의 프로토타입은 수정하지 않는 것이 좋다. 다른 라이브러리에서 해당 객체를 사용할 때 오작동을 일으킬 위험이 있기 때문이다. 물론, 직접 만든 객체의 프로토타입은 맘대로 수정해도 된다. 그러라고 만들어놓은 프로토타입이기 때문이다.

### length of prototype chain

체인의 끝까지 올라가면서 탐색을 수행하기 때문에 성능을 위해서 체인의 길이가 너무 길어지지 않도록 하는 게 좋다.
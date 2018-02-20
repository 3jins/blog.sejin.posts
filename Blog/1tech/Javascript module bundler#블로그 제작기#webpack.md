[블로그 제작기](https://enhanced.kr/postviewer/7) 시리즈의 글입니다.

인간은 하나의 커다란 정보보다는 적당히 나뉘어진 정보를 더 직관적으로 받아들인다. 학교에서 배우는 지식은 과목으로 분류되고 각 과목은 다시 단원으로 분류된다. 모든 동물을 종에 따라 분류하고, 희망이나 사랑과 같은 추상적인 개념조차도 단어로 나눠 분류한다.

코드 역시 적당히 분리돼있을 때 우리는 더 쉽게 이해할 수 있다. 코드의 양이 늘어나면, 가독성을 높이기 위해 쓰임이 비슷한 코드끼리 별도의 파일로 묶어 관리하게 되며, 이 묶인 코드조각을 **모듈**이라 한다. Node.js 생태계를 처음 접하면 패키지, 모듈, 라이브러리, 이 세 가지 용어가 혼용되면서 혼란을 느낀다(적어도 난 그랬다). 셋은 사실상 비슷한 의미를 갖고, 대략 모듈 < 패키지 < 라이브러리 순으로 덩치가 큰 느낌 정도라고 보면 되겠다.

본 포스트에서는 전통적인 자바스크립트에서 모듈을 관리해왔던 방법과, 모던 자바스크립트에서 사용하는 모듈 번들러가 무엇인지에 대해 알아보고자 한다.



## Module Management in Javascript

자바스크립트는 애초에 큰 규모의 프로그래밍을 염두에 두고 만든 언어가 아니다. 그러다보니 모듈을 관리하는 표준적인 문법이 따로 없었다. 수많은 모듈 관리 방식들 중 살아남아 유명해진 몇 개의 관리 방식만 간단히 설명하도록 하겠다.

### CommonJS

node.js 진영에서 사용하던 모듈화 방식이다.

```javascript
// math.js
module.exports = {
    sum: function() {
        var total = 0;
        for (var idx in arguments) {
            total += arguments[idx];
        }
        return total;
    }
};
```

```javascript
// server.js
var math = require('./math');
console.log(math.sum(1, 2));
```

위의 예시에서와 같이 `module.exports` 또는 `exports` 구문을 통해 모듈을 내보내고 `require` 구문을 통해 모듈을 가져온다.

### AMD(Asynchronous Module Definition)

AMD는 CommonJS와는 달리 모듈화를 위해 RequireJS와 같은 **모듈 로더**를 사용해야 한다. 그래서 node.js 진영이 아니어도 지원 가능한 모듈화 방식이라는 장점이 있다.

```javascript
// math.js
define([], function() {
    return {
      sum: function() {
          var total = 0;
          for (var idx in arguments) {
              total += arguments[idx];
          }
          return total;
      }
    };
});
```

```javascript
// server.js
requirejs.config({
    baseUrl: './'
});

require(['math'], function(Math) {
  console.log(Math.sum(1, 2));
});
```

```html
<script src="require.js"></script>
<script src="server.js"></script>
```

코드출처: [https://gist.github.com/KennethanCeyer/3c78f7c18c3519355509e2174103d633](https://gist.github.com/KennethanCeyer/3c78f7c18c3519355509e2174103d633)

### 그 외

두 개가 끝인 줄 알았겠지만 저 외에도 UMD, System.register 등 별 듣도 보도 못한 온갖 표준화 방식이 존재한다. ~~CommonJS랑 AMD도 처음 들어봐놓고 옛날부터 알던 것 같이 얘기하고 있다.~~

### ES6 Module Formats

ES6로 넘어오면서 `import`와 `export`라는 직관적이고 깔끔한 모듈사용구문이 등장하게 되었다. 

```javascript
// Math.js
class Math {
    sum: function() {
        let total = 0;
        for (let idx in arguments) {
            total += arguments[idx];
        }
        return total;
    }
}
export default Math;
```

```javascript
// server.js
import sum from './Math'			// 문법 맞는지 확인. 대소문자 컨벤션 확인.
console.log(sum(1, 2));
```



## Module Bundlers

모듈이 많아진다는 것은 곧 브라우저에서 여러 개의 파일로 분리된 모듈을 불러와야 함을 의미한다. 이는 클라이언트와 서버 사이의 요청과 응답의 수가 많아진다는 것을 의미하고 엄청난 성능의 저하를 초래할 수 있다. 모듈 번들러는 별도로 분리해서 관리하던 각 모듈을, 빌드할 때는 하나의 배포용 파일에 모아주는 역할을 수행한다. 그 덕택에 우리는 코딩할 때는 분리된 파일을 다루고, 배포할 때는 하나의 파일을 전달하여 가독성과 성능의 두 마리 토끼를 모두 잡을 수 있게 된 것이다. 

### Webpack

모듈 번들러가 웹팩만 있는 건 아니다. 웹팩 이전부터 Browserify와 같은 모듈번들러는 존재해왔다. 본래 Node.js 생태계에서는 Browserify와 같은 모듈번들러로 사용할 모듈을 묶고, Grunt나 Gulp와 같은 태스크 러너로 빌드 및 실행에 필요한 번거로운 과정을 자동화했다. 이 2단계로 이뤄진 과정을 하나로 묶어놓은 게 웹팩이다. 즉, 웹팩은 태스크 러너의 역할을 함께 수행하는 모듈 번들러라고 볼 수 있겠다.

웹팩의 사용법에 관해서는 다음 글에서 자세히 다루기로 한다.
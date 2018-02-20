태초의 웹에는 Javascript가 없었다. HTTP와 HTML의 HT(HyperText)가 의미하듯, 원래 웹은 링크로 문서를 연결하는 것을 목적으로 설계되었다. 1995년에 넷스케이프사에서 네비게이터 브라우저에 인터랙티브한 경험을 추가하기 위해 최초로 자바스크립트를 만들었고, 이 때부터 브라우저에서 렌더링된 DOM은 사용자 이벤트에 대한 처리를 할 수 있게 되었다.



## Event Handler, Event Model, DOM Level

이벤트에 대한 처리를 할 수 있는 자바스크립트 함수를 **이벤트 핸들러**라 한다. 또 DOM element에 이벤트 핸들러를 달아주는 방법을 **이벤트 모델**이라 한다. 이벤트 모델은 여러 종류가 있다. DOM의 버전을 가리킬 때 'level'이라는 표현을 쓴다. 다음처럼 DOM level 0에서 2가지 모델이 정의되었고, DOM level 2에서 2가지 모델이 추가로 정의되었다.

- 표준 모델
    - DOM level 0
        - inline model (인라인 모델)
        - traditional model (고전/기본 모델)
    - DOM level 2
        - rewrite of the traditional model
- 비표준 모델
    - IE-specific model (이걸 DOM level 2로 분류하기도 하는데 말도 안되는 소리다. IE에서만 동작하는 게 무슨 표준이냐.)



## Event Models

이제 각 이벤트 모델에 대해 자세히 알아보도록 하자.

### inline model

HTML 안에서 직접 등록할 수 있다.

```html
<body onload="alert('하위^^')">
  <div>
    <button onclick="spitBluntAnswer()">hi</button>
  </div>
  <script>
    function spitBluntAnswer() {
      alert('뭐');
    }
  </script>
</body>
```

### traditional model

자바스크립트로 직접 등록할 수 있다.

```html
<body>
  <div>
    <button id="bluntBtn">hi</button>
  </div>
  <script>
    window.onload = function () {
      alert('하위^^');
    };

    var bluntBtn = document.getElementById('bluntBtn');
    bluntBtn.onclick = function() {
      spitBluntAnswer(); // 호이스팅
    };

    function spitBluntAnswer() {
      alert('뭐');
    }
  </script>
</body>
```

### rewrite of the traditional model (DOM level 2)

[W3C에서 표준으로 지정](https://www.w3.org/TR/2000/REC-DOM-Level-2-Events-20001113/events.html#Events-registration)한 모델으로 현재(2019년 5월) 최신 표준이다(level 3에서 event에 대한 업데이트가 있었지만 이벤트 모델에 대한 수정은 없었고, level 4에서는 이벤트 부분을 전혀 건드리지 않았다).

얘는 IE 8 이하에서는 동작하지 않는다. 하루 빨리 지구상에서 IE 8 이하의 브라우저가 사라지길 간절히 바란다.

표준문서를 보면 다음과 같이 정의되어 있다.

```javascript
void addEventListener(DOMString type, EventListener listener, boolean useCapture);
```

- DOMString type

    등록할 이벤트의 종류를 명시한다. 'load', 'click', 'keypressed' 등 DOM level 0에서 쓰던 이름에서 'on'만 제외하고 넣어주면 된다.

- EventListener listener

    [Event](https://www.w3.org/TR/2000/REC-DOM-Level-2-Events-20001113/events.html#Events-Event)를 인자로 받는 함수다. 여기서 이벤트핸들러를 호출하며, Event에 들어있는 정보에 따라 핸들러의 호출을 제어할 수 있다.

- boolean useCapture

    이벤트캡처링을 쓸 것인지 버블링을 쓸 것인지를 명시한다. 아무것도 넣지 않으면 기본적으로 `false`를 넣은 것처럼 동작한다. 이벤트 캡처링과 버블링에 대해서는 [Javascript 이벤트 버블링, 캡처링, 위임](https://enhanced.kr/postviewer/130)을 참고하자.

```html
<body>
  <div id="button-div">
    <button class="bluntBtn">hi</button>
    <button class="kindBtn">hello</button>
  </div>
  <script>
    window.addEventListener('load', function(event) {
      alert('하위^^');
    });

    var btns = document.getElementById('button-div');
    btns.addEventListener('click', function(event) { // event delegation
      if (event.target.className === 'bluntBtn') spitBluntAnswer();
      else givePoliteAnswer();
    });

    function spitBluntAnswer() {
      alert('뭐');
    }
    function givePoliteAnswer() {
      alert('무슨 일 있으신가요?');
    }
  </script>
</body>
```

### IE-specific model

8 버전 이하의 IE는 W3C 표준을 따르지 않는다. IE-specific model 역시 마이크로소프트의 거대한 똥 중 하나라고 보면 된다. 10 버전까지는 하위호환성을 위해 이 모델을 지원하지만 11 버전부터는 아예 지원하지 않는다고 한다.

한 elment에 여러 이벤트를 동시에 등록할 수 있다는 특징이 있다. 

```html
<body>
  <button class="bluntBtn">hi</button>
  <script>
    window.addEventListener('load', function(event) {
      alert('하위^^');
    });

    var bluntBtn = document.getElementById('bluntBtn');
    bluntBtn.attachEvent('click', spitBluntAnswer);
    bluntBtn.attachEvent('click', givePoliteAnswer);

    function spitBluntAnswer() {
      alert('뭐');
    }
    function givePoliteAnswer() {
      alert('무슨 일 있으신가요?');
    }
  </script>
</body>
```


군대에서 이병이 사고(event)를 쳤다고 하자. 그걸 맞선임한테 걸리면 그냥 맞선임한테 혼나는 정도로(handle event) 끝난다. 하지만 분대장한테 걸리면 얘기가 좀 달라진다.

> 니 위로, 내 밑으로 다 불러와라.

이번 포스트는 위계가 뚜렷한 DOM 구조에서 이벤트가 발생했을 때 그 이벤트가 어떻게 전파되는가에 대한 이야기다.



## 이벤트 전파

브라우저에서 발생한 이벤트는 해당 element에서만 처리되고 끝나지 않는다. 잔잔한 호수에 돌을 던졌을 때처럼, ~~평화로운 소대에서 신병이 사고를 쳤을 때처럼~~ 이벤트는 주변 DOM으로 퍼져나간다.

### event bubbling

이벤트 버블링이란, 한 element에서 이벤트가 발생하면, DOM의 상위요소로 그 이벤트가 전파되는 것을 의미한다. ~~니 위로 다 불러와라~~

다음 코드를 보자.

```html
<html>
  <head>
    <style>
      div {
        max-width: 100%;
        min-height: 50px;
        border: black solid 2px;
        margin: 10px;
      }
    </style>
  </head>
  <body>
    <div class="one">
      one
      <div class="two">
        two
        <div class="three">
          three
        </div>
      </div>
    </div>

    <script>
      var divs = document.querySelectorAll('div');
      divs.forEach(function (div) {
        div.addEventListener('click', function (e) {
          console.log(e.currentTarget.className);
        });
      });
    </script>
  </body>
</html>
```

위 코드를 크롬 브라우저에서 실행시키면 다음과 같은 화면을 볼 수 있다.

<img src="https://raw.githubusercontent.com/3jins/Images/master/event-bubbling-example.png" width="80%"/>

위의 세 div에는 모두 클릭이벤트에 대한 핸들러가 추가되어 있다. 여기서 three의 영역을 클릭해보면 콘솔에는 다음과 같은 메세지가 출력된다.

```
three
two
one
```

누른 것은 three 하나지만, 클릭이벤트가 상위 DOM 요소에까지 전파되는 것이다. 하위로는 전파되지 않는다. three 바깥의 two 영역을 눌러보면 메세지가 다음처럼 나오는 것을 볼 수 있다.

```
two
one
```

물론 one보다 상위에 있는 body, html, document, window에도 이벤트가 전파된다. 본 예제에서는 div element에만 이벤트 핸들러를 달아줬기 때문에 그 위까지는 출력되지 않는 것 뿐이다.

### event capturing

(솔직히 이벤트 캡쳐링은 쓸 일이 거의 없을 것 같다. 그냥 이런 게 있구나 하고 넘어가면 된다.)

이벤트 캡쳐링는 이벤트 버블링과 비슷한 개념이다. 다만 상위 element로 전파되는 것이 아니라 최상위 element에서부터 이벤트가 발생한 element까지 탐색해 내려오면서 이벤트를 발생시킨다.

Event bubbling을 위해 썼던 코드에서 `addEventListener`함수를 호출할 때, 끝에 다음처럼 옵션을 하나 추가해주자.

```javascript
divs.forEach(function (div) {
  div.addEventListener('click', function (e) {
    console.log(e.currentTarget.className);
  }, {
    capture: true,
  });
});
```

이번에 three를 누르면 반대로 one에서부터 three까지 이벤트가 전파되는 것을 볼 수 있다.

### 그냥 중첩된 영역 눌러서 그런거 아니에요?

라고 생각할 수 있다. three의 영역이 two와 one의 영역 안에 있으니까 한 번에 세 element가 다 클릭되어 이벤트가 발동된 것이 아닐까하는 생각. 다음처럼 코드를 짜보자.

```html
<html>
  <head>
    <style>
      div {
        position: absolute;
        width: 100px;
        min-height: 50px;
        border: black solid 2px;
        margin: 10px;
      }
      .one {
        background-color: #f004;
      }
      .two {
        background-color: #00f4;
        top: 35px;
      }
    </style>
  </head>
  <body>
    <div class="one">one</div>
    <div class="two">two</div>
    <script>
      var divs = document.querySelectorAll('div');
      divs.forEach(function (div) {
        div.addEventListener('click', function (e) {
          console.log(e.currentTarget.className);
        })
      });
    </script>
  </body>
</html>
```

다음과 같은 화면을 볼 수 있을 것이다.

<img src="https://raw.githubusercontent.com/3jins/Images/master/event-bubbling-example-2.png" width="30%"/>

이제 one과 two가 중첩된 보라색 영역을 클릭해보자. one보다 two가 나중에 렌더링됐기 때문에 two가 one보다 위에 위치하고, 중첩된 영역을 눌러봤자 two의 이벤트만 발동되는 것을 볼 수 있다.

### event.stopPropagation()

Bubbling이 일어날 때 특정 element 위로는(capturing이라면 아래로는) 이벤트가 전파되지 않았으면 할 수도 있다. element의 eventHandler에서 `event.stopPropagation()` 함수를 호출해주면 해당 element를 넘어서까지 이벤트가 전파되지 않는다. ~~내 밑으로 다 불러와라~~

```javascript
var divs = document.querySelectorAll('div');
divs.forEach(function (div) {
  div.addEventListener('click', function (e) {
    if (e.currentTarget.className === 'two') e.stopPropagation(); // two 위로는 전파 ㄴㄴ
    console.log(e.currentTarget.className);
  });
});
```

Event bubbling의 예제코드에 위 코드처럼 `stopPropagtaion` 함수를 추가하면 three를 눌렀을 때, one까지는 버블링이 진행되지 않는 것을 볼 수 있다.

```
three
two
```



## 이벤트 위임 (event delegation)

이벤트 위임은 이름을 봐서는 뭔가 있어보이는데 사실 별 게 아니다. 그냥 '이벤트 버블링의 활용' 정도로 보면 된다. 수학책과 수학익힘책 같은 느낌이랄까.

`li` 같은 요소를 여럿 만들 수 있다. 이 각각에 대해 다 이벤트를 달아주기는 좀 그렇다. `li`의 갯수만큼 똑같은 이벤트 함수를 만드는 것이고, 그만큼 쓸데없이 메모리가 냠냠되는 결과를 초래하기 때문이다. 우리는 배운 사람이므로 `li`의 상위 element에 이벤트 함수 하나만 딱 달아주면 이벤트 버블링이 일어난다는 사실을 안다. 이렇게 자식 요소에서 발생한 이벤트를 부모 요소로 위임하는 것을 이벤트 위임이라 한다.

다음은 todo list에서 체크박스를 눌렀을 때 취소선을 그어주는 이벤트를 등록하는 예제다.

```html
<html>
  <body>
    <ul id="list"></ul>

    <script>
      var list = document.getElementById('list');
      var todoList = [
        '장 비우기',
        '세수하기',
        '아침 먹기',
        '빈둥대기',
        '점심 먹기',
        '낮잠 자기',
        '저녁 먹기',
        'TV 보기',
        '야식 먹기',
        '잠자기',
      ];
      todoList.forEach((todo) => {
        var todoLiNode = document.createElement('li');
        var checkboxNode = document.createElement('input');
        checkboxNode.setAttribute('type', 'checkbox');
        var todoTextNode = document.createTextNode(todo);
        todoLiNode.appendChild(checkboxNode);
        todoLiNode.appendChild(todoTextNode);
        list.appendChild(todoLiNode);
      });

      /* event deligation: 하위 엘리먼트가 몇 개든 간에 부모 요소에 딱 한 번만 등록하면 된다. */
      list.addEventListener('click', (e) => {
        var eventTarget = e.target;
        if (eventTarget.tagName !== 'INPUT' || eventTarget.type !== 'checkbox') return null;
        eventTarget.parentElement.style = eventTarget.checked ? 'text-decoration: line-through' : '';
      });
    </script>
  </body>
</html>
```




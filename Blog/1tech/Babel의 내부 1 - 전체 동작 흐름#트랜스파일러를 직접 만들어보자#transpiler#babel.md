난 학교 다니면서 컴파일러 수업을 수강하지 못했다. 여기에는 두 가지 이유가 있다.

1. 컴파일러 과목이 2학기(하반기)에 배정돼있었는데 3학년부터 하반기마다 바빴다. 17년에는 소마하느라 휴학을 때렸고, 18년에는 졸업작품 + 취준으로 정신없었다.
2. 우리학교 컴파일러 담당 교수님이 강의전달력 안 좋은 것으로 원탑이셨다. 워낙 드랍하는 학생이 많다 보니 교수님 본인이 수업 첫 시간에 컴파일러 왜 듣는지 여쭤보시고 대답이 시원찮으면 수강취소를 권하신다고 할 정도다.

한 번쯤 듣고 졸업했으면 싶어서 컴파일러를 들었다는 사람을 찾아다녀봤지만 애초에 다들 기피하는 과목이라 수강한 사람을 찾기가 힘들었고, 들었다는 사람은 불과 1년만에 자신이 뭘 배웠는지 기억하지 못했다. 이런 과목을 졸업작품과 병행하기엔 너무 리스크가 컸기 때문에 결국 컴파일러 수업은 듣지 못하게 되었다.

컴파일러에 대한 공부를 얕게나마 해야 할 순간이 언젠가 올 것이라 생각은 했지만, 그 시기가 생각보다 빨라진 것 같다. 치환작업이나 정규표현식 정도로 간단히 만들 수 있을 것이라 생각한 [emojicode-transpiler](https://github.com/3jins/emojicode-transpiler/)는 막상 짜보니 별 것 아닌 기능을 구현하는 데에도 상당히 많은 경우의 수를 고려해야 한다는 사실을 깨닫게 되었다. 그래도 그냥 키워드 치환 정도만 하면 되는 프로젝트를 위해 컴파일러를 다 공부하기에는 무리가 있으니 좀 더 가벼운 Babel에 대해서 알아보는 시간을 갖기로 한다.

주로 참고한 원문은 [여기](https://medium.com/@makk.bit/babel-under-the-hood-63e3fb961243)로, 예제 같은 건 여기서 따왔다. 사실상 저 원글의 요약본이라고 봐도 되겠다.

누누이 말하지만 나 같은 애송이가 뭣도 모르고 막 지껄여놓은 블로그보다는 [공식문서](https://babeljs.io/docs/en/)를 보는 게 낫다. 내 글의 곳곳에서 공식문서나 위키피디아로 링크를 걸어두는 건 이런 이유다. ~~꺼무위키로 향하는 링크는 그냥 드립이다.~~ 정말 나는 영어가 딸려서 공식문서 봐도 뭔 소린지 모르겠다, 또는 설명이 너무 자세해서 전체 그림이 안 잡힌다 할 때만 이 부족한 글을 참고하길 바란다.

서론이 길었다. **TL;DR: 바벨의 동작원리에 대해 알아보자.**



## Babel의 전체 동작 흐름

일단 자세한 방법은 ~~마법이니까~~ 제끼고 전체적으로 어떻게 흘러가는지부터 보도록 하자.

### Stage 1 : Parsing

소스코드를 **추상구문트리**(**AST**, **Abstract Syntax Tree**)로 변환하는 과정이다. **@babel/parser**(a.k.a. **babylon**)라는 모듈이 이 과정을 맡아 수행한다.

```javascript
(foo, bar) => foo + bar;
```

```json
{
  "program": {
    "body": [
      {
        "type": "ExpressionStatement",
        "expression": {
          "type": "ArrowFunctionExpression",
          "params": [
            {
              "type": "Identifier",
              "name": "foo"
            },
            {
              "type": "Identifier",
              "name": "bar"
            }
          ],
          "body": {
            "type": "BinaryExpression",
            "left": {
              "type": "Identifier",
              "name": "foo"
            },
            "operator": "+",
            "right": {
              "type": "Identifier",
              "name": "bar"
            }
          }
        }
      }
    ]
  }
}
```

이런 식으로 씸플하게 구조화한다.

### Stage 2 : Transformation

주어진 AST를 같은 의미, 다른 문법의 AST로 바꾸는 과정이다. ES6 등 신규 문법의 AST를 낮은 버전의 호환가능한 문법으로 바꿔준다. **[@babel/traverse](https://github.com/jamiebuilds/babel-handbook/blob/master/translations/en/plugin-handbook.md#babel-traverse)**라는 모듈에 의해 수행된다.

바벨을 한 번이라도 써 본 사람이라면 누구나 본 적이 있을 **preset**이 바로 이 과정에서 활약한다. preset은 사실 별  건 아니고 여러 [Babel plugin](https://babeljs.io/docs/en/plugins)들을 모아놓은 것이다<span style="opacity:0.5">(실제로 [6버전의 es2015 preset 코드](https://github.com/babel/babel/blob/48573f1fb4/packages/babel-preset-es2015/src/index.js)를 보면 예외처리랑 플러그인들 불러와서 export 해주는 것밖에 없다)</span>. Babel plugin은 ECMAScript의 각 버전별 문법을 호환가능한 형태로 바꿔주는 모듈이다. 코드를 까보면 [plugin에서 @babel/traverse를 불러와 사용한다](https://github.com/babel/babel/blob/master/packages/babel-plugin-transform-arrow-functions/src/index.js#L2)는 것을 알 수 있다. <span style="opacity:0.5">타입스크립트에 대한 이해가 부족해서 더 자세한 건 알 수 없었다. ㅠㅠ</span>

위에서 만든 AST가 transformation 과정을 거치면 다음과 같은 형태로 바뀐다.

```json
{
  "program": {
    "type": "Program",
    "body": [
      {
        "type": "ExpressionStatement",
        "expression": {
          "type": "Literal",
          "value": "use strict"
        }
      },
      {
        "type": "ExpressionStatement",
        "expression": {
          "type": "FunctionExpression",
          "async": false,
          "params": [
            {
              "type": "Identifier",
              "name": "foo"
            },
            {
              "type": "Identifier",
              "name": "bar"
            }
          ],
          "body": {
            "type": "BlockStatement",
            "body": [
              {
                "type": "ReturnStatement",
                "argument": {
                  "type": "BinaryExpression",
                  "left": {
                    "type": "Identifier",
                    "name": "foo"
                  },
                  "operator": "+",
                  "right": {
                    "type": "Identifier",
                    "name": "bar"
                  }
                }
              }
            ]
          },
          "parenthesizedExpression": true
        }
      }
    ]
  }
}
```

### Stage 3 : Generation

변환된 AST를 다시 코드로 바꿔주는 과정이다. 1단계 Parsing의 역연산이라고 볼 수 있겠다. **[@babel/generator](https://github.com/jamiebuilds/babel-handbook/blob/master/translations/en/plugin-handbook.md#toc-babel-generator)**에 의해 행해진다.

Transformation 단계를 거쳐 변형된 AST를 generator에게 주면 다음과 같은 ES5 문법의 JS코드를 뚝딱 만들어낸다.

```javascript
"use strict";
(function (foo, bar) {
  return foo + bar;
});
```



## 내부 동작 원리

이에 대해서는 ~~있을지 없을지도 모르는~~ 다음 글에서 다루도록 한다. 
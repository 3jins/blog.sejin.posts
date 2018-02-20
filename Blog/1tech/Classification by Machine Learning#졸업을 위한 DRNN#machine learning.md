[졸업을 위한 DRNN](https://enhanced.kr/postviewer/1551) 시리즈의 글입니다.

데이터를 분류하는 것은 아주 중요한 문제다. 기계학습에서 분류 문제는 크게 2가지로 분류(~~여기서도 분류가 쓰일만큼 분류는 중요하다~~)하여 접근한다.



## Binary Classification

기다/아니다로 분류하는 문제다. 예를 들면 Andrew Ng의 강의에서 등장하는 종양 악성여부 판단 문제가 있겠다.

데이터를 직선을 이용해서 추정(linear regression)하면 제대로 추정이 되지 않는다.

| <img src="https://raw.githubusercontent.com/3jins/Images/master/linear-regression-shortcoming.png" width="80%"/> |
| :----------------------------------------------------------: |
| 출처: [코세라 강의](https://www.coursera.org/learn/machine-learning/) |

위 그림처럼 양성종양 중에 대따만한 종양이 하나 인풋 데이터로 들어왔다면 쟤 하나 때문에 양성종양의 평균 크기가 확 커지게 된다. Linear regression을 이용해서 $h_\theta(x) > 0.5$일 때 양성종양인 것으로 분류한다고 하면, 상대적으로 작은 종양들은 음성종양이라고 예측된다. 저런 특이 케이스를 예외처리해주는 방법도 있지만 자동화하려고 인공지능 쓰는건데 이런 수작업이 들어가게 되면 에바참치 극혐띠한 상황이 된다. 

직선 대신, 특정 지점에서 갑자기 가파르게 증가하는 그래프를 그리는 함수를 가설함수로 쓰면 저 멀리에 떨어진 특이한 데이터도 커버할 수 있다. 그런 함수가 있느냐, 있다, 아니, 많다. 가장 대표적인 친구가 **sigmoid** 되시겠다. 이 친구의 그래프는 다음과 같이 생겼다.

| <img src="https://raw.githubusercontent.com/3jins/Images/master/sigmoid-graph.png" width="50%"/> |
| :----------------------------------------------------------: |
| 출처: [위키백과](https://upload.wikimedia.org/wikipedia/commons/thumb/8/88/Logistic-curve.svg/320px-Logistic-curve.svg.png) |

아주 개꿀이다. 얘를 쓰면 좋겠다. 함수식은 다음과 같다.
$$
g(z) = \frac{1}{1+e^{-z}}
$$

Sigmoid함수를 이용한 binary classification은 흔히 logistic regression이라고도 불리운다. 둘로만 나뉘기 때문에 threshold에 대한 regression이라는 의미로 이렇게 부르는 것 같다.

 [앞선 글](https://enhanced.kr/postviewer/1662)에서 cross entropy error에 대해 알아본 바 있다. $h(g(x))$를 미분했을 때 계산하기 어려운 시그모이드를 없애버리기 위한 목적으로, 바로 여기서 cross entopy error가 사용된다.



## Multinomial Classification

Multinomial classification은 분류 대상이 둘보다 많을 때의 문제다. 해결책은 binary classification을 여러 번 하는 것이다. 예를 들어, A, B, C 세가지를 분류한다면 (A, ￢A), (B, ￢B), (C, ￢C)로 각각 분류하여 A, B, C를 따로 분류하면 된다.

이 말은 곧 가설함수도 분류대상의 수만큼 존재한다는 뜻이다. Binary classification에서는 가설함수의 값이 하나로 정해져 있었기 때문에 0.5를 기준으로 분류하면 됐는데 multinomial classification은 그런 방법이 통하지 않는다.

### Softmax

Multinomial classification을 수행하면 세 개의 가설함수값이 출력될 것이다. 이 중 가장 값이 큰 놈이 우리가 원하는 분류결과일 가능성이 높다. 근데 이 값이 좀 들쭉날쭉해서 활용하기에 애매하다. 이 값들을 확률의 모양으로 평준화시켜주는 함수가 있으니 그 이름하여 **softmax** 되겠다. 
$$
\mathrm{softmax}(i) = { { e^{y_i} } \over { \sum_j {e^{y_j}} } }
$$
여러 값에 대하여 softmax를 씌우면 합이 1이 되게끔, 그리고 큰 값은 더 큰 비율을 가지게끔 과장돼서 변형된다. 예를 들어, $y_A = 2.0, y_B = 1.0, y_C = 0.1$이라면 softmax 적용 후에는 $p_A = 0.7, p_B = 0.2, p_C = 0.1$이 된다.

이 값은 분류 결과가 각각 A, B, C일 확률이라고 해석해도 무방하다. A일 확률이 0.7로 가장 높으므로 output으로 A를 선택하게 된다.
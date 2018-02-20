[졸업을 위한 DRNN](https://enhanced.kr/postviewer/1551) 시리즈의 글입니다.

문제를 해결하는 데 있어서 가장 기본이 되는 일은 목표가 무엇인지 정확히 아는 것이다. 모든 기계학습의 목표는 결국 **loss function의 값이 최소가 되는 hypothesis function을 찾는 것**이다. 그럼 이 둘이 무엇인지 알아보는 시간을 갖도록 하자.



## 가설 함수 (Hypothesis Function)

세상의 모든 일은 함수로 표현할 수 있다. 여기서 [함수](https://en.wikipedia.org/wiki/Function_(mathematics)#Definition)란, 주어진 입력에 대해서 출력값이 1:1로 대응되는 관계를 일컫는다. 예를 들어, 내가 오늘 먹을 저녁메뉴는 내가 오늘 먹은 점심메뉴($x_1$), 내 위치($x_2$), 같이 먹는 사람들($x_3$), 내가 갖고 있는 돈($x_4$) 등 여러 입력을 고려하여 결정하게 된다. 

기계학습은 입력들과 그에 대응되는 출력이 무엇인지가 주어지고, 궁극적으로 그 사이의 함수를 찾아내는 것을 목표로 한다. 이러한 함수는 여러 형태가 될 수 있지만, 만약 그 형태가 선형함수라고 한다면, $h(x_1, x_2, x_3, x_4) = \theta _1x_1 + \theta _2x_2 + \theta _3x_3 + \theta _4x_4 + b$와 같은 형태로 나타낼 수 있다. 학습이 진행되기 전에는 계수에 대해 아는 바가 없기 때문에 계수로 랜덤 값을 쓴다. 이 때의 함수 $h$를 **가설 함수(hypothesis function)**라 한다. 

GPU의 이점을 최대한 활용하기 위해서는 식들을 최대한 행렬로 표현하는 게 좋다. 가설함수는 계수와 변수를 별도의 행렬로 분리해서 곱하는 형태로 표현할 수 있는데 이 때 계수의 벡터 $W = \{\theta _1, \theta _2, \theta _3, \theta _4, b\}$를 **Weight**라 하고, 가설 함수가 선형이라면 $h = WX$ 형태로 표현 가능하다.



## 손실 함수 (Loss Function)

가설 함수는 가설일 뿐이므로, 가설 함수에 주어진 입력을 넣었을 때 나오는 값은 실제 나와야 하는 값과 차이가 있기 마련이다. 그래서 오차의 총량을 구해서 이 값이 최소가 되는 가설 함수를 구한다면, 그 가설 함수는 우리가 구하고자 하는 함수와 가장 가까울 것이다. 이 때 오차의 총량을 나타내는 함수가 바로 **손실 함수(loss function)**, 또는 **비용 함수(cost function)**이다.

#### 손실 함수의 종류

인공지능의 문제는 크게 회귀(regression)와 [분류(classification)](https://enhanced.kr/postviewer/1749)로 나뉜다. 회귀 문제는 output이 연속적인 경우, 각 input에 대응하는 output의 값 자체를 예측하는 문제다. 예를 들어 집값을 예측하는 문제가 회귀에 해당한다. 분류 문제는 output의 형태가 이산적인 경우에 각 output 값이 분리되는 경계선(threshold)을 찾는 문제다. 

문제의 종류에 따라 사용해야 하는 손실 함수 역시 달라진다. 실제 손실함수의 종류는 무수히 많고, 문제에 따라 적절한 손실함수를 새로 디자인해야 할 수도 있다. 여기서는 가장 유명하고 대표적인 두 손실함수만 알아보고 넘어가겠다.

* MSE (Mean Squared Error)
    $$
    J(x) = {1 \over 2m} \sum^m_1(y - h(x))^2
    $$
    회귀문제에서 사용하는 손실함수다. 약간 TMI지만 [저 $J$는 Jacobian Matrix의 J를 따온 것](https://stackoverflow.com/questions/42420235/what-does-the-capital-letter-j-mean-in-cost-function-j%CE%B8)이라고 한다. 자세한 건 나도 모르겠다. 

    제곱이 있는 이유는 양수와 음수의 상쇄를 막기 위함이다. 쉽게 생각해서 통계에서 배우는 분산과 똑같은 공식이라고 보면 된다. 

    분모에 2가 곱해져 있는 이유가 의아할 수 있는데, 나중에 [gradient descent](https://enhanced.kr/postviewer/1697) 다룰 때 나오겠지만 사실 손실 함수는 편미분을 당하기 위해 태어난 녀석이다. 따라서 그 때의 계산을 깔끔하게 만들어주기 위해 분모에 2를 그냥 붙여놓은 것이다. '아니, 저렇게 계산 쉽게 만들겠다고 분모에 2를 막 붙여도 되나' 싶을텐데, 결론적으로 된다. 손실 함수는 사실 꼭 저 꼴이 아니어도 된다. 제곱 대신 절댓값을 써도 되고 네제곱을 써도 상관없다. 가설함수와 실제 데이터 간의 오차를 제대로 표현만 하면 되기 때문에 꼭 저 꼴이 아니더라도 코드 짜는 사람이 정의하기 나름이다. 

* CEE (Cross Entropy Error)

    $$
    \begin{eqnarray} J(x) & = & \left\{ \begin{array}{ll} {-\ln(h_\theta(x))} & {\mathrm{if} \;\, y = 1} \\ {-\ln(1 - h_\theta(x))} & {\mathrm{if} \;\, y = 0}  \end{array} \right. \end{eqnarray}
    $$
    분류 문제에서는 output 벡터의 형태를 잡아주기 위해 활성함수로 흔히 sigmoid 함수를 사용한다. Sigmoid 함수의 형태는 다음과 같다.
    $$
    g(z) = \frac{1}{1+e^{-z}}
    $$
    Sigmoid를 사용했을 때 가설함수는 다음과 같다.
    $$
    h_\theta(X) = g(\theta^T \cdot X)
    $$
    얘를 MSE 공식에다가 그대로 쳐박았다가는 매우 괴랄한 식이 튀어나온다. 식만 괴랄한게 아니라 그래프도 wavy하게 나와서 local minima 잔치가 열리고 gradient descent로 도저히 최솟값을 구할 수 없게 된다. Cost function의 공식은 사실 정해져 있는 게 아니라 적당히 바꿔줘도 상관없다. 어쨌든 $y = 1​$일 때 $h_\theta(x) = 1​$ 나오고 $y = 0​$일 때 $h_\theta(x) = 0​$ 나오면 되는 것 아닌가. 그래서 똑똑하신 연구자분들께서는 cross entropy error를 고안했고, 얘를 미분하면 $e​$와 $log​$가 만나 귀신같이 상쇄된다. 
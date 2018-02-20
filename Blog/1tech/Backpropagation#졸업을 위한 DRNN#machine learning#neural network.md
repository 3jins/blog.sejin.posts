[졸업을 위한 DRNN](https://enhanced.kr/postviewer/1551) 시리즈의 글입니다.

지난 번에 쓴 [Neural Network 포스트](https://enhanced.kr/postviewer/1732)에 보면, 이런 말이 있다.

> weight와 bias를 조금씩 조작해서 다시 입력을 넣다 보면 출력값들이 어느 순간 기대한 값들에 가까워질 것이다.

근데 저 weight와 bias를 뭐 어떻게 조작한다는 말일까. 그 해답은 **역전파(back propagation)**에 있다.



## Feedback

**역전파**는 뉴럴네트워크에 전달해주는 일종의 **피드백**이다. 피드백의 단어 뜻은 다음과 같다.

> feedback
> 명사
>
> 2. `심리학•교육학`
>
>     행동이나 반응을 그 결과를 참고로 하여 수정하고 더욱 적절한 것으로 해 가는 방법.

즉, 역전파를 수행하기 위해서는 '적절한 것'의 기준이 있어야 한다는 의미다. 그 기준은 보통 input data에서 label으로 주어진다. 뉴럴네트워크가 수행한 '행동이나 반응의 결과'가 얼마나 부적절한가의 척도는 [손실 함수](https://enhanced.kr/postviewer/1662)를 통해 알 수 있다. 따라서 역전파는 결국 **주어진 input에 대한 신경망의 output을 보고 loss function의 값을 구한 뒤, 그 값을 최소화하기 위해 신경망(=weights)에 되돌려주는 피드백**이다.



## Gradient Descent

그럼 그 피드백을 어떻게 구할 것이냐. Gradient라는 친구를 이용하면 된다. Gradient라고 무슨 거창하게 생긴 이름을 붙여놨는데 쫄 필요 없다. 그냥 대학교 1학년 때 누구나 미적분학 시간에 배우는 편미분값이다.
$$
\triangledown f = [{\partial f \over \partial x}, {\partial f \over \partial y}]
$$
예를 들어, $f(x, y) = x^2 + y^2$이라면 $\triangledown f = [2x, 2y]$가 된다.

이 부분에 대한 자세한 설명은 [Gradient Descent](https://enhanced.kr/postviewer/1697) 포스트를 참고하도록 하자.



## Chain Rule

Backpropagation의 핵심개념으로, 미분 또는 편미분 간의 곱셈은 그냥 일반 분수처럼 서로 약분이 된다는 법칙이다. ~~사실 뉴럴네트워크를 공부하는 데 있어서는 몰라도 된다.~~ 고등학교 교과과정에서 고2 쯤에 미분을 처음 배울 때 등장한다.

다음과 같은 식을 생각해보자.
$$
f(x, y, z) = (x+y)z
$$
여기서 $q = x+y$라 하면 $f(x, y, z) = qz$이므로 다음이 성립한다.
$$
{\partial f \over \partial x} = {\partial f \over \partial q} \cdot {\partial q \over \partial x} = z \cdot 1 = z
$$

| <img src="https://raw.githubusercontent.com/3jins/Images/master/backpropagation-diagram.png" width="80%"/> |
| :----------------------------------------------------------: |
| 출처: [http://aikorea.org/cs231n/optimization-2/](http://aikorea.org/cs231n/optimization-2/) |

실제 값 x, y, z를 넣어주면 위 그림처럼 backpropagation이 수행되어 편미분값이 구해진다.



## Backpropagation

우리 졸업작품을 지도해주신 교수님의 말씀에 따르면 backpropagation은 그냥 'chain rule을 DP를 이용해 구현한 것'에 불과하다. 이 말이 시사하는 바는 역전파의 구현에 대해 이해하는 데에 많은 힘을 뺄 필요가 없다는 뜻이다. 우리는 잘 만들어진 라이브러리에서 역전파 함수를 가져와서 쓰기만 하면 된다.

학습모델은 결국 수학적인 함수이므로 연산게이트를 모아놓은 회로 형태로 표현될 수 있다. 이 회로를 의인화했을 때, 각 게이트에서 학습하는 gradient 값은 '회로가 출력하는 output의 loss를 줄이기 위해 각 게이트에게 이렇게 변했으면 좋겠다'하고 바라는 값으로 볼 수 있다. 

학습 모델 회로의 전체적인 형태는 activation function에 의해 결정되고, 각 게이트의 세세한 수치는 각 layer의 weight에 의해 결정된다. Backpropagation이 변경하는 부분은 weight에 해당한다. 

여기까지만 알고 있으면 된다. Chain rule에 의해 이전 layer의 weight이 어떻게 변하는지 따위는 역전파 자체에 대한 연구를 진행하거나 딥러닝 라이브러리를 만들 게 아니라면 몰라도 된다. Backpropagation의 원리에 대해서는 이전에 공부해본 적이 있지만 다 까먹었으므로 이 글에서 적지 않겠다.
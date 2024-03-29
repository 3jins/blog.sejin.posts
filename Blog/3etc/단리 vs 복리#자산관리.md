문이과가 공통으로 배우고, 수능에 무조건 출제되는 수학1의 수열에서 우리는 다음과 같이 세뇌당했다.

> 복리는 기하급수적으로 증가하기 때문에 복리가 단리보다 좋은거다.

과연 그럴까? 개발자들의 자산관리를 책임지는 [파이썬](https://www.python.org/)과 함께 확인해보도록 하자.



## 용어

먼저 용어를 정리하고 들어가자.

- 월이율 N%
    - 말 그대로 매월 이율이 N%가 붙는다는 것이다. 예를 들어 월이율 1% 복리상품이라면 한 달이 지날 때마다 예치한 총 금액이 1.01배가 된다는 뜻이다.
- 연이율 N%
    - 월이율 N/12%와 같은 말이다.



## 복리 vs 단리

우리가 주목해야 할 부분은 적금의 기간과 낮은 이자율이다. 2의 거듭제곱만 봐오던 30번의 지수증가로 2가 10억이 되는 마법에 익숙하다. 하지만 그건 2씩이나 되니까 가능한 얘기고 1.004라면 얘기가 좀 다르다.

```
>>> (1 + (0.05 / 12)) ** 36
1.1614722313334678
```

연이율 5%의 복리상품에 3년을 넣어뒀을 때의 총 이자다. 그나마 이건 첫달에 넣은 금액에 붙는 양이고, 늦게 넣은 금액일 수록 그 이자율은 줄어든다.

매달 200만원씩 복리로 넣는다고 가정하고 다음과 같은 파이썬 스크립트를 작성해서 돌려보자.

```python
total = 0
monthly = 200
rate = 0.05 / 12
duration = 36

for month in range(duration):
  total += 200 * ((1 + rate) ** (duration - month))

print(total)
```

```
7782.961550273169
```

총 7200만원을 투입해서 583만원을 벌었다.

이번엔 같은 금리의 단리상품을 계산해보자.

```python
total = 0
monthly = 200
rate = 0.05 / 12
duration = 36

for month in range(duration):
  total += 200 * (1 + rate * (duration - month))

print(total)
```

```
7755.0
```

총 7200만원을 투입해서 555만원을 벌었다.

한 달에 200만원을 저축할 여유가 있는 사람이 3년동안 28만원을 더 벌었으면 이 돈으로 뭘 할 수 있을까. 3년 내내 적금에 쏟아부은 돈으로 한달 점심값 정도 번 셈인데 별로 의미가 없다.



## 무조건 금리 센 상품이 이득

**월이율 5% 복리**와 **월이율 6%의 단리**, 두 상품을 비교해보자. 두 상품의 이율은 단 1% 밖에 차이나지 않으며, 복리의 효과를 최대화하기 위해 **5년의 장기상품**이라고 하자. 우리가 기존에 알던 상식대로라면 이 정도 조건이라면 그래도 복리상품이 더 많은 이득을 줘야 할 것이다.

```python
# 월이율 5% 5년 복리

total = 0
monthly = 200
rate = 0.05 / 12
duration = 60

for month in range(duration):
  total += 200 * ((1 + rate) ** (duration - month))

print(total)
```

```
13657.888303869317
```

```python
# 월이율 6% 5년 단리

total = 0
monthly = 200
rate = 0.06 / 12
duration = 60

for month in range(duration):
  total += 200 * (1 + rate * (duration - month))

print(total)
```

```
13830.0
```

결과는 **금리 높은 단리가 이자가 더 세다**는 것이다. 10년 넘게 저금할 수 있는 상품이 아니면 복리/단리는 의미가 없다. 그냥 이율 높은 상품을 고르면 된다. 수능 빈출유형은 우리에게 거짓말을 했다. 자라나는 청소년에게 수능은 담배보다 해로운 것 같다.


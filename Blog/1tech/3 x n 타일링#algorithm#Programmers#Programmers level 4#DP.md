오늘 [카카오 코드페스티벌 예선전](https://enhanced.kr/postviewer/1258)에 참가해보았는데 자랑은 아니지만 쳐발렸다. 그래서 심기일전하고, 깝치지 말고 내 수준에 맞는 문제부터 풀기로 하여, 다시 프로그래머스 문제들을 풀기 시작했다. 대회 준비를 하면서 17년 기출문제도 좀 풀어봤지만 6문제 중에 3문제 밖에 못 풀었고, 나머지 3문제 중 2문제는 해설을 보고도 이해하지 못했거나 제대로 돌아가는 코드를 작성하지 못했으므로 나중에 실력이 될 때 다시 풀어보도록 하겠다.

Level 4 첫번째 문제다. [2xn 타일링](https://enhanced.kr/postviewer/1019)과 생긴 것부터 상당히 비슷한 요 놈은 실제로 푸는 방법도 비슷하다. 다만, 2xn 타일링과는 달리 처리해줘야 할 예외가 좀 있다. 어려운 문제들은 처리해줘야 할 예외상황들이 복잡한 경우가 많은데, 이 문제는 예외처리가 그렇게 심하게 까다롭지도 않으면서, 잘못 생각하면 엄청난 삽질에 빠질 수 있는 문제라 예외 케이스 거르는 법을 연습하기에 아주 좋은 문제라고 생각한다.



## Problem

[https://programmers.co.kr/learn/courses/30/lessons/12902](https://programmers.co.kr/learn/courses/30/lessons/12902)

> ###### 문제 설명
>
> 가로 길이가 2이고 세로의 길이가 1인 직사각형 모양의 타일이 있습니다. 이 직사각형 타일을 이용하여 세로의 길이가 3이고 가로의 길이가 n인 바닥을 가득 채우려고 합니다. 타일을 채울 때는 다음과 같이 2가지 방법이 있습니다
>
> - 타일을 가로로 배치 하는 경우
> - 타일을 세로로 배치 하는 경우
>
> 예를들어서 n이 8인 직사각형은 다음과 같이 채울 수 있습니다.
>
> ![Imgur](https://i.imgur.com/zBW7peI.png)
>
> 직사각형의 가로의 길이 n이 매개변수로 주어질 때, 이 직사각형을 채우는 방법의 수를 return 하는 solution 함수를 완성해주세요.
>
> ##### 제한사항
>
> - 가로의 길이 n은 5,000이하의 자연수 입니다.
> - 경우의 수가 많아 질 수 있으므로, 경우의 수를 1,000,000,007으로 나눈 나머지를 return해주세요.
>
> ##### 입출력 예
>
> | n    | result |
> | ---- | ------ |
> | 4    | 11     |
>
> ##### 입출력 예 설명
>
> 입출력 예 #1
> 다음과 같이 11가지 방법이 있다.
> ![Imgur](https://i.imgur.com/nnoT9kL.png)
> ![Imgur](https://i.imgur.com/QTZFrTH.png)
> ![Imgur](https://i.imgur.com/YE1JfJn.png)
> ![Imgur](https://i.imgur.com/QhYvRTr.png)
> ![Imgur](https://i.imgur.com/NKgKTIR.png)
> ![Imgur](https://i.imgur.com/3uobFxe.png)
> ![Imgur](https://i.imgur.com/sEK9oor.png)
> ![Imgur](https://i.imgur.com/u6dpiep.png)
> ![Imgur](https://i.imgur.com/re3C19N.png)
> ![Imgur](https://i.imgur.com/GerdAJB.png)
> ![Imgur](https://i.imgur.com/ITcbWj0.png)

문제 겁나 길다...



## Trial

핵심은 당연히 DP고, 최상급 표현이 없으므로 최적해를 구하는 문제가 아닌, 점화식을 세우는 문제다. 일단, 짝수 넓이의 타일로 전체를 채워야 되기 때문에 n이 홀수인 경우는 0을 출력해야 한다.

그 외에 예외가 되는 상황을 만드는 타일 조합은 다음의 두 가지다.

![Imgur](https://i.imgur.com/GerdAJB.png)![Imgur](https://i.imgur.com/ITcbWj0.png)

괜히 문제에서 11개나 되는 케이스를 나열해준 게 아니다. 이 두 그림의 공통점은 3x2 단위로 쪼개어지지 않고 무조건 저 3x4짜리가 붙어 다녀야만 한다는 것이다. 저기서 끝나지 않는다.

<img src="https://raw.githubusercontent.com/3jins/Images/master/3x6_tiling.png" width="30%"/>

<img src="https://raw.githubusercontent.com/3jins/Images/master/3x8_tiling.png" width="40%"/>

이렇게 계속해서 길이가 늘어난다.

처음에는 3x2n 타일은 n개의 3x2 타일을 붙여 만들 수 있다는 데에서 착안했다. 그래서 3x2가 n개 붙은 경우, 3x2 n-1개와 분리되지 않는 3x4 1개가 붙은 경우, 3x2 n-2개와 분리되지 않는 3x4 2개가 붙은 경우, ... 등을 모두 더하려고 시도했다. 하지만 1. 경우의 수가 너무 복잡하게 늘어났고, 2. 결정적으로 팩토리얼 연산을 수반하는 컴비네이션이 필요했기 때문에 다른 방법을 찾아야 했다.



## Solution

2xn 타일링과 똑같은 방법으로 푸는 게 제일 간단하다. 3xn 타일링을 할 수 있는 경우의 수를 $f(n)$이라 하자. 이 때, $f(8)$을 구한다고 생각해보자.

<img src="https://raw.githubusercontent.com/3jins/Images/master/3x8_all_tiling.png" width="40%"/>

위 그림을 수식으로 나타내 보면 $f(8) = f(2) * f(6) + 2f(4) + 2f(2) + 2f(0)$이 된다. 좀 느낌이 오지 않는가? 이를 일반화하면 

$$
f(2n) = 3f(2n-2) + 2\sum_{k=0}^{k=n-2}f(2k) = f(2n-2) + 2\sum_{k=0}^{k=n-1}f(2k)
$$

가 된다. 

이제, 코드로 옮겨보자.

```c++
#include <string>
#include <vector>

#define MAX 2500
#define DIVISOR 1000000007

using namespace std;

int solution(int n) {
    if (n % 2 == 1) return 0;
    n /= 2;
    long long numTiles[MAX] = {1, 3};
    long long sumNumTiles = 4;

    for (int i = 2; i <= n; i++) {
        numTiles[i] = numTiles[i - 1] + 2 * sumNumTiles;
        sumNumTiles += numTiles[i];
        numTiles[i] %= DIVISOR;
        sumNumTiles %= DIVISOR;
    }

    return numTiles[n];
}
```

주의할 점이 두 가지가 있겠다. 하나는 합을 구하는 부분으로, 새로운 항을 하나 구할 때마다 일일이 루프 돌면서 합을 구하면 $O(n^2)$ 시간이 걸리고 만다. 따라서 합을 저장하는 변수를 따로 선언해서 매번 새로운 항을 구할 때 이 변수 값을 갱신시켜 주도록 하자.

다른 하나는, 자료형에 관한 부분으로, `int` 자료형으로는 제대로 된 답이 안 나온다. 그 이유는 문제에서 주어진 나누는 수가 10억 7이기 때문이다. 오버플로우 나는 순간 그 이후로 모든 답이 다 깨져버리므로 자료형을 `long long`으로 선언해줘야 한다.



## Retrospect

#### 수열에서 조합 소리 안 나게 해라

복잡한 조합이 포함되는 일반항은 결코 좋은 해법이 될 수 없다. n이 커짐에 따라 구하기도 어렵고, 구한다 한들 결국 팩토리얼 때문에 시간복잡도에 막히기 때문이다. 

#### 점화식 세우기: 일반항($f(n)$)을 단순하게

**1. $f(n)$을 최대한 단순하게 추상화시켜서 생각**해야 한다. 그리고 **2. $f(n)$이 $f(n + 1)$로 변하는 과정을 무엇이 추가되어야 하는지에 초점을 맞춰 상상**해보자. 그 과정에서 예외가 생길 수 있다. **3. $f(n)$과 $f(n+1)$이 만나는 접합부에 특히 주목**하자. **4. 예외가 일반화될 수 있는지** 마지막으로 점검하자.
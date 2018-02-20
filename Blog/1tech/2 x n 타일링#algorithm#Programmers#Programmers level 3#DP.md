이 문제가 굉장히 어렵다고 생각했다. 하지만 내 친구는 이 [문제를 풀기 위한 아이디어는 깊게 고민하지 않고 도출해](https://poqw.github.io/JM_ASYMTILING/)냈다. 코딩테스트는 그런 사람들과 경쟁해야 하는 어렵고도 험난한 거시어따.



## Problem

[https://programmers.co.kr/learn/courses/30/lessons/12900](https://programmers.co.kr/learn/courses/30/lessons/12900)

> 가로 길이가 2이고 세로의 길이가 1인 직사각형모양의 타일이 있습니다. 이 직사각형 타일을 이용하여 세로의 길이가 2이고 가로의 길이가 n인 바닥을 가득 채우려고 합니다. 타일을 채울 때는 다음과 같이 2가지 방법이 있습니다.
>
> - 타일을 가로로 배치 하는 경우
> - 타일을 세로로 배치 하는 경우
>
> 예를들어서 n이 7인 직사각형은 다음과 같이 채울 수 있습니다.
>
> ![Imgur](https://i.imgur.com/29ANX0f.png)
>
> 직사각형의 가로의 길이 n이 매개변수로 주어질 때, 이 직사각형을 채우는 방법의 수를 return 하는 solution 함수를 완성해주세요.
>
> ##### 제한사항
>
> - 가로의 길이 n은 60,000이하의 자연수 입니다.
> - 경우의 수가 많아 질 수 있으므로, 경우의 수를 1,000,000,007으로 나눈 나머지를 return해주세요.
>
> ------
>
> ##### 입출력 예
>
> | n    | result |
> | ---- | ------ |
> | 4    | 5      |
>
> ##### 입출력 예 설명
>
> 입출력 예 #1
> 다음과 같이 5가지 방법이 있다.
>
> ![Imgur](https://i.imgur.com/keiKrD3.png)
>
> ![Imgur](https://i.imgur.com/O9GdTE0.png)
>
> ![Imgur](https://i.imgur.com/IZBmc6M.png)
>
> ![Imgur](https://i.imgur.com/29LWVzK.png)
>
> ![Imgur](https://i.imgur.com/z64JbNf.png)



## Trial

일단 이 문제는 단순화시킬 수 있다. 사실상 n을 1과 2의 합으로 나타낼 수 있는 경우의 수를 구하는 문제다.

처음 보면 DP구나 싶은데, 잘 안 풀렸다. 조금 머리를 굴려보다 보면 [이 문제](https://enhanced.kr/postviewer/150)와 매우 닮았다는 생각이 든다. 그래서 일단 똑같은 방법으로 풀어보았다.

```c++
#include <string>
#include <vector>

#define DIVIDER 1000000007
using namespace std;

int cnt = 0;

void traversal(int n, int sum) {
    if(sum > n) return;
    if(sum == n) cnt++;
    if(sum + 1 <= n) traversal(n, sum + 1);
    if(sum + 2 <= n) traversal(n, sum + 2);
}

int solution(int n) {
    traversal(n, 0);
    return cnt % DIVIDER;
}
```

시간초과 난다.

입력이 최대 6만이라 트리의 높이가 최대 6만이 된다(1만 다 더하는 경우). 높이가 6만인 트리의 원소 수는 $2^{600001}$이다. 그걸 다 순회하고 있으니 뭐.. 시간초과 안 나는 게 이상하겠다.



## Solution

결론적으로 이 문제를 푸는 가장 효율적인 방법은 DP가 맞다. 이 문제에서는 i항에서 그 이후 항으로 넘어가는 방법이 딱 두가지가 있다.

1. 세로로 1개를 추가한다. (i+1)
2. 가로로 2개를 추가한다. (i+2)

여기서 초항을 살펴보면 n = 1인 경우 1, n = 2인 경우 2이므로  결국 피보나치 수열이 나오게 된다.

```c++
#include <vector>

#define DIVIDER 1000000007
using namespace std;

int solution(int n) {
    vector<int> dp = {1, 2};
    for(int i=2; i<n; i++) {
        dp.emplace_back((dp[i - 1] + dp[i - 2]) % DIVIDER);
    }
    return dp[n - 1];
}
```



## Retrospect

#### Counting

조합문제면 일단 카운팅부터 생각해봐야 한다. 물론, 이 문제는 그렇지 않았다.

#### Observation

안 풀리면 하나하나 관찰해보는 게 도움이 된다. 일반적으로 그렇다고 할 수는 없지만 이 문제는 눈치가 빠르면 n=4인 경우까지만 나열해봐도 피보나치일 수 있다는 생각이 들 법하다.

가설을 세우고 증명하는 것과 그냥 생으로 풀이법을 찾아내는 건 체감난이도에서 엄청난 차이가 난다. 

#### Ignition (점화식)

DP 중에서 LIS와 같은 문제는 최선을 갱신한다. [문제에서](https://enhanced.kr/postviewer/100) [요구하는](https://enhanced.kr/postviewer/121) [답에](https://enhanced.kr/postviewer/504) [최상급](https://enhanced.kr/postviewer/795) [표현](https://enhanced.kr/postviewer/882)이 있으면 최선을 갱신해야 한다. 

하지만 이 문제처럼 정확한 답을 찾아내야 하는 DP 문제도 있다. 이런 류의 문제의 본질은 결국 점화식이다. 그리고 점화식을 풀기 위한 첫 발자국은 **초항**의 갯수를 찾고, 그 초항을 모두 구해내는 것이다.
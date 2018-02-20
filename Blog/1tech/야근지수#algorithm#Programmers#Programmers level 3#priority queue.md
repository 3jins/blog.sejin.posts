이 문제는 2018년 중순부로 개편되었다. 야근을 해야 하는 비운의 주인공이 수민이면 개편 전이고, Demi면 개편 후다. 개편 전에는 겁나 쉬웠는데, 개편 후에는 시간복잡도 통과 기준이 조금 까다로워졌나보다.

개인적으로 이런 문제가 진짜 어려운 문제라고 생각한다. DP 같은 별도의 지식은 전혀 필요하지 않지만, 시간복잡도에 약간의 제약이 있으면서, 상당히 많은 예외처리를 신경 써야 한다.



## Problem

[https://programmers.co.kr/learn/courses/30/lessons/12927](https://programmers.co.kr/learn/courses/30/lessons/12927)

> 회사원 Demi는 가끔은 야근을 하는데요, 야근을 하면 야근 피로도가 쌓입니다. 야근 피로도는 야근을 시작한 시점에서 남은 일의 작업량을 제곱하여 더한 값입니다. Demi는 N시간 동안 야근 피로도를 최소화하도록 일할 겁니다.Demi가 1시간 동안 작업량 1만큼을 처리할 수 있다고 할 때, 퇴근까지 남은 N 시간과 각 일에 대한 작업량 works에 대해 야근 피로도를 최소화한 값을 리턴하는 함수 solution을 완성해주세요.
>
> ##### 제한 사항
>
> - `works`는 길이 1 이상, 20,000 이하인 배열입니다.
> - `works`의 원소는 50000 이하인 자연수입니다.
> - `n`은 1,000,000 이하인 자연수입니다.
>
> ##### 입출력 예
>
> |   works   |  n   | result |
> | :-------: | :--: | :----: |
> | [4, 3, 3] |  4   |   12   |
> | [2, 1, 2] |  1   |   6    |
> |   [1,1]   |  3   |   0    |

~~아니, 야근 시간이 왜 100만까지 가~~



## Trial

기본적인 아이디어는 이거다. $\sum{a_i^2}$은 $a_i$의 편차가 적을 수록 크다. `works` 변수의 길이가 2일 때 이 아이디어는 다음 그림처럼 직관적이다.

<img src="https://raw.githubusercontent.com/3jins/Images/master/optimize_overtime_quotient.png" width="60%"/>

위 그림에서 $p_1 \neq q_1$인 반면, $p_2 = q_2$이다. 그리고 야근지수1이 야근지수2보다 작다는 것은 명확하게 드러난다.

개편 전의 수민이가 야근하는 문제라면 회사가 양심적이라서 그냥 n번 돌면서 works 배열에서 최댓값 찾아서 1씩 빼주면 된다. 하지만 Demi가 다니는 회사는 제정신이 아니라서 야근을 최대 100만시간까지 해야 하고, 업무 수는 최대 20000개까지 주어진다. 이대로 생각 없이 이중 루프를 돌리면 200억번의 루프가 발생한다.

워라밸의 상징인 스웨덴에서 건너온 [한 아저씨](https://namu.wiki/w/%ED%86%A0%EB%A5%B4%EB%B9%84%EC%9A%98)는 이렇게 말했다.

> **일단 만들어! 그리고 부숴!** 
>
> Build 'em up, break 'em down. 

그래서 난 일감들을 일단 정렬하고, 그 다음에 부수기로 했다. 그럼 $O(w\log{w} + n) = O(n)$ 시간 내에 문제를 해결할 수 있다. 

```c++
#include <string>
#include <vector>
#include <algorithm>

using namespace std;

bool cmp(int a, int b) {
    return a > b;
}

long long solution(int n, vector<int> works) {
    long long answer = 0;
    int numWorks = works.size();
    sort(works.begin(), works.end(), cmp);

    int sum = 0;
    for (int i = 0; i < numWorks; i++) {
        sum += works[i];
    }
    if (sum <= n) return 0;

    int idx = 0;
    while (n > 0) {
        while (works[0] == 0) {
            works.erase(works.begin());
            idx--;
        } 
        while (works[idx] == works[idx + 1]) idx++;
        for (int i = 0; i <= idx; i++) {
            works[i]--;
            n--;
            if (n == 0) break;
        }
    }

    for (int i = 0; i < numWorks; i++) {
        answer += works[i] * works[i];
    }

    return answer;
}
```



## Solution

위 코드도 정답이다. 하지만 예외처리할 게 은근히 많다. 남들의 코드를 뒤적여보니 우선순위 큐를 쓰면 아주 간단히 풀린다는 사실을 알 수 있었다.

```c++
#include <string>
#include <vector>
#include <queue>

using namespace std;

long long solution(int n, vector<int> works) {
    priority_queue<int> pq;
    for (int &i : works) pq.push(i);

    while (n--) {
        int t = pq.top();
        if (t == 0) break;
        pq.pop();
        pq.push(--t);
    }

    long long answer = 0;
    while (!pq.empty()) {
        long long t = pq.top();
        answer += t * t;
        pq.pop();
    }
    return answer;
}
```



## Retrospect

#### Priority Queue

[우선순위 큐](https://enhanced.kr/postviewer/1297)는 큐에 집어넣을 때 알아서 힙정렬을 해서 들어간다. 따라서 **일단 정렬하고 그 다음에 부수는 전략을 자동으로 해주는 셈**이다.

#### Exception Handling

내 코드에서 필요했던 예외처리는 크게 3개였다.

1. 가장 큰 값이 두번째로 큰 값과 같아진 경우
2. 0이 나온 경우
3. 모두 0이 된 경우

첫번째 경우는 window를 만들어서 해결했다.

두번째 경우는 배열에서 제거해버려서 해결했다.

세번째 경우는 반드시 발생하지 않기 때문에 아예 반복문이 시작되기 전에 처리했다.

이거 제대로 예외처리하느라 시간이 꽤 걸렸는데 실전에서 이런 류의 문제를 만나면 어떻게 해야 그 시간을 줄일 수 있을지 감이 안 온다. 일단 많은 문제를 풀어보도록 하자.
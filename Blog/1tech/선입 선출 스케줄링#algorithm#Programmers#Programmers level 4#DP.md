이 문제는 사실 작년에 소마할 때 필수강의로 지정 돼 있었던 자료구조 코세라 강의에서 과제로 나왔던 문제라 파이썬으로 ~~답 보고 베껴~~풀어봤었다. 이번에는 그래서 핵심 아이디어는 금방 잡아낼 수 있었지만 구현력이 쓰레기라 푸는 데에 많은 시간을 소모해야 했다.



## Problem

[https://programmers.co.kr/learn/courses/30/lessons/12920](https://programmers.co.kr/learn/courses/30/lessons/12920)

> ###### 문제 설명
>
> 처리해야 할 동일한 작업이 n 개가 있고, 이를 처리하기 위한 CPU가 있습니다.
>
> 이 CPU는 다음과 같은 특징이 있습니다.
>
> - CPU에는 여러 개의 코어가 있고, 코어별로 한 작업을 처리하는 시간이 다릅니다.
> - 한 코어에서 작업이 끝나면 작업이 없는 코어가 바로 다음 작업을 수행합니다.
> - 2개 이상의 코어가 남을 경우 앞의 코어부터 작업을 처리 합니다.
>
> 처리해야 될 작업의 개수 n과, 각 코어의 처리시간이 담긴 배열 cores 가 매개변수로 주어질 때, 마지막 작업을 처리하는 코어의 번호를 return 하는 solution 함수를 완성해주세요.
>
> ##### 제한 사항
>
> - 코어의 수는 10,000 이하 2이상 입니다.
> - 코어당 작업을 처리하는 시간은 10,000이하 입니다.
> - 처리해야 하는 일의 개수는 50,000개를 넘기지 않습니다.
>
> ------
>
> ##### 입출력 예
>
> |  n   |  cores  | result |
> | :--: | :-----: | :----: |
> |  6   | [1,2,3] |   2    |



## Trial

들어오는 데이터의 수가 만 단위다. 그 말인즉슨, $O(n^2)$으로 풀면 시간제한에서 걸러진다는 뜻이다. 최소한 $O(n\log n)$까지는 시간복잡도를 낮춰야 한다.

$O(n^2)$으로 먼저 풀어보자. n개의 작업을 처리한다. 매 작업을 받을 때마다 모든 코어를 돌면서 할당할 수 있는 코어가 있는지를 살핀다.

n개의 작업은 반드시 처리해야 하므로 마법 같은 수학공식을 찾아내지 않는 이상 저 밑으로 시간을 줄이는 것은 불가능하다. 그럼 줄일 수 있는 것은 코어 전체를 살펴보는 시간이 되겠다. 

작업이 들어있는 큐가 정렬이 되어 있다면 참 좋을 것 같다. 새 작업 하나 할당할 때마다 그냥 큐에서 `dequeue`해서 초콜릿처럼 꺼내먹어요 하면 되기 때문이다. 큐를 정렬하는 방법이라면? 겁나 빠른 정렬을 하더라도 $O(n\log n)$의 시간은 걸린다. 근데 그건 완성된 큐를 정렬할 때 얘기고, 추가할 때 앞뒤 비교해서 제 위치에 끼우는 식이라면? 이진탐색 등의 방법을 이용하면 $O(\log n)$ 시간에 정렬할 수 있다.

자료구조에 대한 공부를 한 적이 있는 사람이라면 위 말을 보고 생각나는 자료구조가 하나 있을 것이다. 바로 [우선순위 큐](https://enhanced.kr/postviewer/1297)다. 힙의 삽입 및 삭제 알고리즘이 $O(\log n)$ 시간만에 끝난다는 걸 이용해서 우선순위 큐는 `push` 연산과 `pop` 연산을 $O(\log n)$ 시간에 처리할 수 있다.

```c++
#include <string>
#include <vector>
#include <queue>

using namespace std;

int solution(int n, vector<int> cores) {
    int numCores = cores.size();
    int spentTime = 0;
    int workIdx = 0;

    priority_queue<pair<int, int>, vector<pair<int, int>>, greater<pair<int, int>>> pq;
    for (int i = 0; i < numCores; i++) {
        pq.push({cores[i] + spentTime, i});
        if (++workIdx == n) return i + 1;
    }
    while (true) {
        pair<int, int> finishedWork = pq.top();
        pq.pop();
        finishedWork.first += cores[finishedWork.second];
        pq.push(finishedWork);
        if (++workIdx == n) return finishedWork.second + 1;
    }
}
```

그런데, 이 코드로는 시간제한 내에 코드가 돌지 않는다.



## Solution

사실 답은 모른다. 벡터 쓴 게 문제인가 싶어서 직접 배열 쓰는 우선순위 큐 만들어보려다가 그냥 포기했다. 

<img src="https://raw.githubusercontent.com/3jins/Images/master/fifo_scheduling_qna.png" width="100%"/>

다 같이 멘탈 붕괴.



## Retrospect

#### Priority Queue

[야근지수](https://enhanced.kr/postviewer/1192) 문제에서도 등장했던 우선순위 큐다. 쓰는 방법을 제대로 익히고 넘어가자.

#### 역발상

사실 이 문제에서 어려웠던 건 우선순위 큐를 써야 한다는 걸 알아차리는 게 아니었다. 

만약 3초가 걸리는 작업이 한 코어에서 끝났다고 하자. 그럼 다른 모든 코어에게 그 작업이 끝났다는 걸 알려야 한다. 코어는 최대 10000갠데 그걸 다 돌면서 3초씩을 빼줘야 할까? 그럼 $O(n^2\log n)$이 걸리는데..?

**모든 원소에게 같은 변화를 가해야 한다면, 원소 각각에게 연산을 하지 말고 환경에 연산**을 해서 $O(n)$을 $O(1)$로 줄일 수가 있다. 3초짜리 작업이 한 코어에서 끝났으면 그 다음 작업부터는 소요시간을 3초씩 더 더해주면 된다. 3초 뒤의 상황으로 가서 m초 짜리 작업을 추가하는 대신, 3초 뒤에 m초 짜리 작업을 추가할 것이므로 현재 시점에서 m+3초짜리 작업을 추가해주면 똑같은 상황이 되는 것이다.
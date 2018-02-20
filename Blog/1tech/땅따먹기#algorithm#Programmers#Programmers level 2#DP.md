문제 출제자가 [땅따먹기](https://namu.wiki/w/%EC%82%AC%EB%B0%A9%EC%B9%98%EA%B8%B0)에 대해 오해를 하고 있는 듯하다. 이름에서부터 알 수 있듯이 땅따먹기는 내 것이 아닌 땅을 따고 각자가 딴 땅의 양을 정량화하여 승패를 결정해야 한다. 하지만, 이 문제에서 언급하는 땅따먹기는 땅을 따는 개념부터가 없다. 그냥 밟고 지나가면 점수를 얻게끔 되어있는데 짓밟고 지나가면 내 땅이라고 생각하는 건가 싶다. 만약 그렇다면 이는 군국주의적인 발상이라 해야 할 것 같다. 헛소리 그만하고 문제로 넘어가자.



## Problem

[https://programmers.co.kr/learn/courses/30/lessons/12913](https://programmers.co.kr/learn/courses/30/lessons/12913)

> 땅따먹기 게임을 하려고 합니다. 땅따먹기 게임의 땅(land)은 총 N행 4열로 이루어져 있고, 모든 칸에는 점수가 쓰여 있습니다. 1행부터 땅을 밟으며 한 행씩 내려올 때, 각 행의 4칸 중 한 칸만 밟으면서 내려와야 합니다. **단, 땅따먹기 게임에는 한 행씩 내려올 때, 같은 열을 연속해서 밟을 수 없는 특수 규칙이 있습니다.**
>
> 예를 들면,
>
> | 1 | 2 | 3 | 5 |
>
> | 5 | 6 | 7 | 8 |
>
> | 4 | 3 | 2 | 1 |
>
> 로 땅이 주어졌다면, 1행에서 네번째 칸 (5)를 밟았으면, 2행의 네번째 칸 (8)은 밟을 수 없습니다.
>
> 마지막 행까지 모두 내려왔을 때, 얻을 수 있는 점수의 최대값을 return하는 solution 함수를 완성해 주세요. 위 예의 경우, 1행의 네번째 칸 (5), 2행의 세번째 칸 (7), 3행의 첫번째 칸 (4) 땅을 밟아 16점이 최고점이 되므로 16을 return 하면 됩니다.
>
> ##### 제한사항
>
> - 행의 개수 N : 100,000 이하의 자연수
> - 열의 개수는 4개이고, 땅(land)은 2차원 배열로 주어집니다.
> - 점수 : 100 이하의 자연수
>
> ------
>
> ##### 입출력 예
>
> |              land               | answer |
> | :-----------------------------: | :----: |
> | [[1,2,3,5],[5,6,7,8],[4,3,2,1]] |   16   |



## Trial

전형적인 DP 문제다. 그리고 2차 DP다. 최선값이 여러개기 때문이다.

```c++
#include <iostream>
#include <vector>
#include <algorithm>

#define ROWS 4

using namespace std;

int solution(vector<vector<int>> land) {
    int cols = land.size();
    land.insert(land.begin(), vector<int>(ROWS, 0));
    for (int i = 0; i < cols; i++) {
        for (int j = 0; j < ROWS; j++) {
            int localMax = 0;
            for (int k = 0; k < ROWS; k++) {
                if (k == j) continue;
                if (land[i][k] + land[i + 1][j] > localMax)
                    localMax = land[i][k] + land[i + 1][j];
            }
            land[i + 1][j] = localMax;
        }
    }

    return *max_element(land[cols].begin(), land[cols].end());
}
```

다음 그림을 그대로 코드로 옮겼다고 보면 된다.

<img src="https://raw.githubusercontent.com/42deSix/Images/master/land_obtaining.png" width="40%"/>

<img src="https://raw.githubusercontent.com/42deSix/Images/master/land_obtaining_dp_1.png" width="40%"/>

<img src="https://raw.githubusercontent.com/42deSix/Images/master/land_obtaining_dp_2.png" width="40%"/>



## Solution 

Trial이 곧 Solution이다. (뿌듯)



## Retrospect

일단 DP문제임은 알겠다. 근데 뭔가 어렵다. 왜 그런지 생각해보자.

#### 추가된 조건을 제거하기

우리에게 총과 수류탄이 있다고 하자. 높은 벽의 반대편에 있는 적을 제거하려면 직사화기가 아니라 수류탄을 써야 한다는 사실은 자명하다. 여기서 만약 적진에 족구 챔피언이 있어서 수류탄을 던지면 우리 쪽으로 걷어차낼 확률이 높다는 조건이 있다고 하자. 그렇다고 해서 벽에다가 직사화기를 때려갈겨야 할 리는 없다. 방법은 간단하다. 추가된 조건을 제거하고 수류탄을 써서 문제를 해결하면 된다(뭐 조건을 제거하는 방법이라면 안전핀 뽑고 손잡이 뗀 후에 2~3초 있다가 수류탄을 던지는 방법 등이 있겠다).

이 문제도 마찬가지다. DP를 적용할 때 우리가 신경써야 하는 것은 loop의 매 iteration마다 구해지는 최선값이다. 사실 이 문제는 최선값이 열의 수만큼 있지만, 마지막에 그 최선값들 중 최댓값을 출력해야 하는 조건이 붙어있다. 만약 이 문제에서 구해야 하는 값이 최고점수가 아니라, 마지막 행에서 밟는 땅마다의 최고점수 리스트였다면, DP를 행 단위로 적용해야 한다는 것이 눈에 쉽게 들어왔을 것이다.

#### 1차 vector DP

2차원 벡터가 나온다고 다 2차 DP가 아니다. 이 문제처럼 2차원 int형 벡터를 1차원 vector\<int\>형 벡터로 봐야 하는 경우도 있다.


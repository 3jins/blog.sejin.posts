쉬운 문제 찾아 풀려고 Level 2를 선택했는데 두 문제 연속으로 겁나 어렵다. Level 1부터 풀어야 되나 싶다. 



## Problem

[https://programmers.co.kr/learn/courses/30/lessons/12905](https://programmers.co.kr/learn/courses/30/lessons/12905)

> 1와 0로 채워진 표(board)가 있습니다. 표 1칸은 1 x 1 의 정사각형으로 이루어져 있습니다. 표에서 1로 이루어진 가장 큰 정사각형을 찾아 넓이를 return 하는 solution 함수를 완성해 주세요. (단, 정사각형이란 축에 평행한 정사각형을 말합니다.)
>
> 예를 들어
>
> |  1   |  2   |  3   |  4   |
> | :--: | :--: | :--: | :--: |
> |  0   |  1   |  1   |  1   |
> |  1   |  1   |  1   |  1   |
> |  1   |  1   |  1   |  1   |
> |  0   |  0   |  1   |  0   |
>
> 가 있다면 가장 큰 정사각형은
>
> |  1   |  2   |  3   |  4   |
> | :--: | :--: | :--: | :--: |
> |  0   | `1`  | `1`  | `1`  |
> |  1   | `1`  | `1`  | `1`  |
> |  1   | `1`  | `1`  | `1`  |
> |  0   |  0   |  1   |  0   |
>
> 가 되며 넓이는 9가 되므로 9를 반환해 주면 됩니다.
>
> ##### 제한사항
>
> - 표(board)는 2차원 배열로 주어집니다.
> - 표(board)의 행(row)의 크기 : 1000 이하의 자연수
> - 표(board)의 열(column)의 크기 : 1000 이하의 자연수
> - 표(board)의 값은 1또는 0으로만 이루어져 있습니다.
>
> ------
>
> ##### 입출력 예
>
> |                   board                   | answer |
> | :---------------------------------------: | :----: |
> | [[0,1,1,1],[1,1,1,1],[1,1,1,1],[0,0,1,0]] |   9    |
> |           [[0,0,1,1],[1,1,1,1]]           |   4    |
>
> ##### 입출력 예 설명
>
> 입출력 예 #1
> 위의 예시와 같습니다.
>
> 입출력 예 #2
> | 0 | 0 | `1` | `1` |
> | 1 | 1 | `1` | `1` | 
> 로 가장 큰 정사각형의 넓이는 4가 되므로 4를 return합니다.



## Trial

일단 뭐 감이 안 와서 먼저 단순 4중 반복문 돌려서 짜보기로 했다. 모든 좌표를 돌면서 <u>각 좌표마다 대각선 아래로 어디까지 뻗어나갈 수 있는지를 구해서(`getMaxSquareLen`)</u> 그 중의 최소값을 리턴하게 만들었다.

```c++
#include <iostream>
#include<vector>
using namespace std;

int getMaxSquareLen(vector<vector<int>> board, int y, int x, int max) {
    if(board[y][x] == 0) return 0;
    if(max == 1) return 1;
    for(int i=2; i<=max; i++) {
        for(int j=0; j<i; j++) {
            if(board[y + i - 1][x + j] == 0) return i - 1;
            if(board[y + j][x + i - 1] == 0) return i - 1;
        }
    }
    return max;
}

int solution(vector<vector<int>> board)
{
    int answer = 0;
    int h = board.size();
    int w = board[0].size();
    
    for(int i=0; i<h; i++) {
        for(int j=0; j<w; j++) {
            int newMaxLen = getMaxSquareLen(board, i, j, min(h - i, w - j));
            answer = max(answer, newMaxLen * newMaxLen);
        }
    }

    return answer;
}
```



## Solution 

사실 시간초과가 날 것을 알고 있었다. 입력의 크기가 가로 1000, 세로 1000이기 때문에, 4중 포문을 돌게 되면 100만 * 100만번의 반복이 생긴다. C++ 기준으로 100만번을 반복할 때 약 1초가 걸린다고 하므로... 음....

이 문제는 사실 DP를 쓰면 빠르게 풀 수 있다. 근데 그 풀이가 워낙 창의적이라서 나 같은 범인(凡人)들은 그냥 외워야 된다. ㅎㅎㅎ...

이 문제의 풀이는 다음과 같다. 다음과 같은 배열이 주어졌다고 하자.

<img src="https://raw.githubusercontent.com/42deSix/Images/master/original_dp_table_example.png" width="40%"/>

그럼 일단 다음처럼 이 배열의 위와 왼쪽에 0으로 벽을 두른다.

<img src="https://raw.githubusercontent.com/42deSix/Images/master/incomplete_dp_table_example.png" width="50%"/>

그 다음, $1 \le i \le board\_height$,  $1 \le j \le board\_width$에 대하여, `board[i][j]`의 값을 `min(board[i-1][j-1], board[i][j-1], board[i-1][j]) + 1`으로 갱신한다. 단,  `board[i][j]`의 값이 0이라면 그냥 0으로 놔둔다.

이렇게 함으로써, 모든 좌표에 대해 그를 둘러싼 ┏ 모양이 모두 1인지 검사하여 그 주변의 정사각형이 1로 채워져있는지 확인하게 되며, 그 정사각형의 크기를 대각선 아래로 모을 수 있다. 직관적으로 이해하기 위해, 완성된 dp table을 보도록 하자.

<img src="https://raw.githubusercontent.com/42deSix/Images/master/complete_dp_table_example.png" width="50%"/>



```c++
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

int solution(vector<vector<int>> board) {
    int answer = 0;
    int h = board.size();
    int w = board[0].size();

    board.insert(board.begin(), vector<int>(w, 0));
    for(int i=0; i < h + 1; i++) {
        board[i].insert(board[i].begin(), 0);
    }

    for(int i = 1; i < h + 1; i++) {
        for(int j = 1; j < w + 1; j++) {
            if(board[i][j] == 0) continue;
            vector<int> compares = {
                    board[i - 1][j - 1],
                    board[i - 1][j],
                    board[i][j - 1],
            };
            board[i][j] = *min_element(compares.begin(), compares.end()) + 1;
            if(answer < board[i][j]) {
                answer = board[i][j];
            }
        }
    }

    return answer * answer;
}
```



## Retrospect

#### 2차 DP

지금까지 대부분의 DP 문제는 1차원 배열만 가지고 해결이 가능했다. 하지만 이 문제는 2차원 배열을 필요로 한다. 이런 문제를 **2차 DP**라고 부르기로 한다. 사실 2차 DP라는 말은 없는 것으로 알고 있다. [아는 형](https://github.com/znxkznxk1030/)이 그렇게 부르길래 나도 그냥 그렇게 부르기로 하겠다(입에 잘 달라붙지 않는가). 

DP는 **과거의 최선과 현재를 비교해서 최선을 갱신**한다. 1차 DP에서는 **과거의 최선이 바로 이전 항에** 저장된다. 2차 DP에서는 확장되어 **과거의 최선이 `(i-1, j)`, `(i, j-1)`, 그리고 `(i-1, j-1)`의 세 군데에** 저장되는 것이다. 이는 차원이 2개기 때문에 $2^2-1$개가 되는 것이다. 마찬가지로, 3차 DP로 확장된다면, 과거의 최선은 $2^3-1=7$개가 될 것이고, 얘네들과 현재값을 비교해야 할 것이다. 
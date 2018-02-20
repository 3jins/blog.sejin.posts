Easy는 개뿔, 겁나 어렵다. 이렇게 어려운 문제를 내놓는데 [삼성전자가 매년 몇천명씩이나 신입사원을 채용할 수 있다](http://www.hankookilbo.com/v/fea8333f5ee04692b21226db553f5fab)는 게 신기할 정도다(물론 마케팅 등의 부서에서도 사람을 뽑겠지만 4500명이나 뽑는다는데 이 시험 치고 들어가는 사람이 1000단위는 되지 않을까). 

## Problem

[https://www.acmicpc.net/problem/12100](https://www.acmicpc.net/problem/12100)

> | 시간 제한 | 메모리 제한 | 제출  | 정답 | 맞은 사람 | 정답 비율 |
> | --------- | ----------- | ----- | ---- | --------- | --------- |
> | 1 초      | 512 MB      | 11985 | 2789 | 1628      | 21.917%   |
>
> ### 문제
>
> 2048 게임은 4×4 크기의 보드에서 혼자 즐기는 재미있는 게임이다. 이 [링크](https://gabrielecirulli.github.io/2048/)를 누르면 게임을 해볼 수 있다.
>
> 이 게임에서 한 번의 이동은 보드 위에 있는 전체 블럭을 상하좌우 네 방향 중 하나로 이동시키는 것이다. 이 때, 같은 값을 갖는 두 블럭이 충돌하면 두 블럭은 하나로 합쳐지게 된다. 한 번의 이동에서 이미 합쳐진 블럭은 또 다른 블럭과 다시 합쳐질 수 없다. (실제 게임에서는 이동을 한 번 할 때마다 블럭이 추가되지만, 이 문제에서 블럭이 추가되는 경우는 없다)
>
> | <img src="https://onlinejudgeimages.s3-ap-northeast-1.amazonaws.com/problem/12094/1.png" width=100%/> | <img src="https://onlinejudgeimages.s3-ap-northeast-1.amazonaws.com/problem/12094/2.png" width=100%/> | <img src="https://onlinejudgeimages.s3-ap-northeast-1.amazonaws.com/problem/12094/3.png" width=100%/> |
> | :----------------------------------------------------------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
> |                           <그림 1>                           |                           <그림 2>                           |                           <그림 3>                           |
>
> <그림 1>의 경우에서 위로 블럭을 이동시키면 <그림 2>의 상태가 된다. 여기서, 왼쪽으로 블럭을 이동시키면 <그림 3>의 상태가 된다.
>
> | <img src="https://onlinejudgeimages.s3-ap-northeast-1.amazonaws.com/problem/12094/4.png" width=100%/> | <img src="https://onlinejudgeimages.s3-ap-northeast-1.amazonaws.com/problem/12094/5.png" width=100%/> | <img src="https://onlinejudgeimages.s3-ap-northeast-1.amazonaws.com/problem/12094/6.png" width=100%/> | <img src="https://onlinejudgeimages.s3-ap-northeast-1.amazonaws.com/problem/12094/7.png" width=100%/> |
> | :----------------------------------------------------------: | :----------------------------------------------------------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
> |                           <그림 4>                           |                           <그림 5>                           |                           <그림 6>                           |                           <그림 7>                           |
>
> <그림 4>의 상태에서 블럭을 오른쪽으로 이동시키면 <그림 5>가 되고, 여기서 다시 위로 블럭을 이동시키면 <그림 6>이 된다. 여기서 오른쪽으로 블럭을 이동시켜 <그림 7>을 만들 수 있다.
>
> | <img src="https://onlinejudgeimages.s3-ap-northeast-1.amazonaws.com/problem/12094/8.png" width=100%/> | <img src="https://onlinejudgeimages.s3-ap-northeast-1.amazonaws.com/problem/12094/10.png" width=100%/> |
> | :----------------------------------------------------------: | :----------------------------------------------------------: |
> |                           <그림 8>                           |                           <그림 9>                           |
>
> <그림 8>의 상태에서 왼쪽으로 블럭을 옮기면 어떻게 될까? 2가 충돌하기 때문에, 4로 합쳐지게 되고 <그림 9>의 상태가 된다.
>
> | <img src="https://onlinejudgeimages.s3-ap-northeast-1.amazonaws.com/problem/12094/17.png" width=100%/> | <img src="https://onlinejudgeimages.s3-ap-northeast-1.amazonaws.com/problem/12094/18.png" width=100%/> | <img src="https://onlinejudgeimages.s3-ap-northeast-1.amazonaws.com/problem/12094/19.png" width=100%/> | <img src="https://onlinejudgeimages.s3-ap-northeast-1.amazonaws.com/problem/12094/20.png" width=100%/> |
> | :----------------------------------------------------------: | :----------------------------------------------------------: | :----------------------------------------------------------: | :----------------------------------------------------------: |
> |                          <그림 10>                           |                          <그림 11>                           |                          <그림 12>                           |                          <그림 13>                           |
>
> <그림 10>에서 위로 블럭을 이동시키면 <그림 11>의 상태가 된다. 
>
> <그림 12>의 경우에 위로 블럭을 이동시키면 <그림 13>의 상태가 되는데, 그 이유는 한 번의 이동에서 이미 합쳐진 블럭은 또 합쳐질 수 없기 때문이다.
>
> | <img src="https://onlinejudgeimages.s3-ap-northeast-1.amazonaws.com/problem/12094/21.png" width=100%/> | <img src="https://onlinejudgeimages.s3-ap-northeast-1.amazonaws.com/problem/12094/22.png" width=100%/> |
> | :----------------------------------------------------------: | :----------------------------------------------------------: |
> |                          <그림 14>                           |                          <그림 15>                           |
>
> 마지막으로, 똑같은 수가 세 개가 있는 경우에는 이동하려고 하는 쪽의 칸이 먼저 합쳐진다. 예를 들어, 위로 이동시키는 경우에는 위쪽에 있는 블럭이 먼저 합쳐지게 된다. <그림 14>의 경우에 위로 이동하면 <그림 15>를 만든다.
>
> 이 문제에서 다루는 2048 게임은 보드의 크기가 N×N 이다. 보드의 크기와 보드판의 블럭 상태가 주어졌을 때, 최대 5번 이동해서 만들 수 있는 가장 큰 블럭의 값을 구하는 프로그램을 작성하시오.
>
> ### 입력
>
> 첫째 줄에 보드의 크기 N (1 ≤ N ≤ 20)이 주어진다. 둘째 줄부터 N개의 줄에는 게임판의 초기 상태가 주어진다. 0은 빈 칸을 나타내며, 이외의 값은 모두 블럭을 나타낸다. 블럭에 써있는 수는 2보다 크거나 같고, 1024보다 작거나 같은 2의 제곱꼴이다. 블럭은 적어도 하나 주어진다.
>
> ### 출력
>
> 최대 5번 이동시켜서 얻을 수 있는 가장 큰 블럭을 출력한다.
>
> ### 예제 입력 1
>
> ```
> 3
> 2 2 2
> 4 4 4
> 8 8 8
> ```
>
> ### 예제 출력 1
>
> ```
> 16
> ```



## Solution

DFS를 쓰면 된다. 재귀적으로 돌면서 4방향 모두 다 한 번씩 밀어주면 된다.

```c++
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

int boardSize = 0;

int getLargest(vector<vector<int>> board) {
    int max = 0;
    for (int i = 1; i <= boardSize; i++) {
        for (int j = 1; j <= boardSize; j++) {
            if (max < board[i][j]) max = board[i][j];
        }
    }
    return max;
}

vector<vector<int>> fillSpace(vector<vector<int>> board, int direction) {
    pair<int, int> zeroPoint;
    bool findingZero = true;
    switch (direction) {
        case 0: // right
            for (int i = 1; i <= boardSize; i++) {
                for (int j = boardSize; j >= 1; j--) {
                    if (!findingZero && board[i][j] != 0) {
                        board[zeroPoint.first][zeroPoint.second] = board[i][j];
                        board[i][j] = 0;
                        findingZero = true;
                        j = zeroPoint.second;
                    }
                    if (findingZero && board[i][j] == 0) {
                        zeroPoint = {i, j};
                        findingZero = false;
                    }
                }
                findingZero = true;
            }
            break;
        case 1: // down
            for (int j = 1; j <= boardSize; j++) {
                for (int i = boardSize; i >= 1; i--) {
                    if (!findingZero && board[i][j] != 0) {
                        board[zeroPoint.first][zeroPoint.second] = board[i][j];
                        board[i][j] = 0;
                        findingZero = true;
                        i = zeroPoint.first;
                    }
                    if (findingZero && board[i][j] == 0) {
                        zeroPoint = {i, j};
                        findingZero = false;
                    }
                }
                findingZero = true;
            }
            break;
        case 2: // left
            for (int i = 1; i <= boardSize; i++) {
                for (int j = 1; j <= boardSize; j++) {
                    if (!findingZero && board[i][j] != 0) {
                        board[zeroPoint.first][zeroPoint.second] = board[i][j];
                        board[i][j] = 0;
                        findingZero = true;
                        j = zeroPoint.second;
                    }
                    if (findingZero && board[i][j] == 0) {
                        zeroPoint = {i, j};
                        findingZero = false;
                    }
                }
                findingZero = true;
            }
            break;
        case 3: // up
            for (int j = 1; j <= boardSize; j++) {
                for (int i = 1; i <= boardSize; i++) {
                    if (!findingZero && board[i][j] != 0) {
                        board[zeroPoint.first][zeroPoint.second] = board[i][j];
                        board[i][j] = 0;
                        findingZero = true;
                        i = zeroPoint.first;
                    }
                    if (findingZero && board[i][j] == 0) {
                        zeroPoint = {i, j};
                        findingZero = false;
                    }
                }
                findingZero = true;
            }
            break;
        default:
            return board;
    }
    return board;
}

vector<vector<int>> move(vector<vector<int>> board, int direction) {
    int yStart[4] = {1, boardSize, 1, 1};
    int xStart[4] = {boardSize, 1, 1, 1};
    int yEnd[4] = {boardSize + 1, 0, boardSize + 1, boardSize + 1};
    int xEnd[4] = {0, boardSize + 1, boardSize + 1, boardSize + 1};
    int yIter[4] = {1, -1, 1, 1};
    int xIter[4] = {-1, 1, 1, 1};
    int yMove[4] = {0, -1, 0, 1};
    int xMove[4] = {-1, 0, 1, 0};

    board = fillSpace(board, direction);
    for (int i = yStart[direction]; i != yEnd[direction]; i += yIter[direction]) {
        for (int j = xStart[direction]; j != xEnd[direction]; j += xIter[direction]) {
            if (board[i][j] == board[i + yMove[direction]][j + xMove[direction]]) {
                board[i][j] *= 2;
                board[i + yMove[direction]][j + xMove[direction]] = 0;
            }
        }
    }

    return fillSpace(board, direction);
}

int recursiveMove(int count, vector<vector<int>> board) {
    if (count == 5) return getLargest(board);

    vector<int> maxCandidates;
    maxCandidates.push_back(getLargest(board));
    for (int i = 0; i < 4; i++) {
        vector<vector<int>> movedBoard = move(board, i);
        if (board == movedBoard) continue;
        maxCandidates.push_back(recursiveMove(count + 1, movedBoard));
    }
    return *max_element(maxCandidates.begin(), maxCandidates.end());
}

int main() {
    vector<vector<int>> board;
    cin >> boardSize;
    for (int i = 0; i < boardSize + 2; i++) {
        vector<int> tmpVec;
        int tmp;
        for (int j = 0; j < boardSize + 2; j++) {
            if(i == 0 || j == 0 || i == boardSize + 1 || j == boardSize + 1)
                tmpVec.push_back(0);
            else {
                cin >> tmp;
                tmpVec.push_back(tmp);
            }
        }
        board.push_back(tmpVec);
    }
    cout << recursiveMove(0, board) << endl;
    return 0;
}
```





## Retrospect

얘는 [구슬 탈출 2](https://enhanced.kr/postviewer/1845)와는 달리 문제 자체가 대단히 어려운 건 아니다. 하지만 구현과정에서 꼼꼼하게 생각하고 시작하지 않으면 발견하기 어려운 에러가 나타나 괴롭힐 것이다.

#### Recursive DFS

> 어려워 보이면 일단 재귀로 풀 수 있는지를 확인하는 게 우선이다. 

DFS를 구현하는 방법은 2가지가 있다. 하나는 스택을 이용한 구현이고, 다른 하나는 재귀를 이용한 구현이다. 그 중 재귀 방식 DFS는 BFS에 비해 구현이 압도적으로 쉽다. 대신 깊이가 조금만 깊어지면 메모리가 기하급수적으로 소모되기 때문에 거의 쓸모가 없다. 하지만, 이 문제처럼 입력이 매우 작은 경우라면 재귀로 짜는 게 코드도 매우 간결해지고 여러모로 유리하다.

#### 밀어채우기

퍼즐게임류 문제에서 빈칸을 밀어 채우는 알고리즘은 매우 자주 등장한다. 테트리스를 짜도 나오고 [카카오 공채 프렌즈 4블록](https://enhanced.kr/postviewer/1668)에서도 나온다.  보통, 득점 조건을 만족한 뒤 사라진 블록에 대해 후처리를 해주기 위해 많이 쓰는데, 이 문제에서는 득점 전에도 한 번 밀어채워놔줘야 했다.

#### 최적화 시도의 위험성

밀어채우기 전처리가 맨 처음 한 번만 필요하다고 생각했다. 두번째부터는 이전 턴에서 후처리로 이미 밀어채우기를 해놨기 때문이었다. 하지만 밀어채우기가 한 방향으로만 이루어지기 때문에 방향이 달라지면 다시 전처리를 해줘야 했다.

코딩테스트를 볼 때는 자존심 같은 건 싹 갖다버리고 일단 푸는데에 집중해야 할 것 같다. 삼성이나 카카오나 최적화에 대한 부분은 거의 묻지 않으므로 일단 풀고 시간초과가 나면 그 때 최적화에 대해 고민하도록 하자.
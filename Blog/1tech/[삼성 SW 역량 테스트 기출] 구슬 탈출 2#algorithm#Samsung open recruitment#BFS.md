카카오 작년 공채 기출은 다 풀었고, 재작년 카카오 공채는 블라인드 공채도 아닌데다가 채점도 해볼 수 없고 정답코드도 따로 공개되지 않는다. 그래서 일단 삼성 공채 문제부터 쭉 풀어보기로 했다. 

그런데 놀랍게도, 삼성 공채 문제가 카카오 공채보다 어렵다. 이 문제는 대략 6시간을 붙잡고 있으면서(삼성 소프트웨어 역량 테스트의 제한시간은 3시간이고 2문제가 나온다), 나 혼자 힘으로 못 풀어서 질문게시판에 있는 추가적인 테스트케이스들을 넣어가며 코드수정을 거듭하여 겨우겨우 푸는데 성공했다. 



## Problem

[https://www.acmicpc.net/problem/13460](https://www.acmicpc.net/problem/13460)

>| 시간 제한 | 메모리 제한 | 제출  | 정답 | 맞은 사람 | 정답 비율 |
>| --------- | ----------- | ----- | ---- | --------- | --------- |
>| 2 초      | 512 MB      | 11335 | 2863 | 1546      | 23.165%   |
>
>### 문제
>
>스타트링크에서 판매하는 어린이용 장남감 중에서 가장 인기가 많은 제품은 구슬 탈출이다. 구슬 탈출은 직사각형 보드에 빨간 구슬과 파란 구슬을 하나씩 넣은 다음, 빨간 구슬을 구멍을 통해 빼내는 게임이다.
>
>보드의 세로 크기는 N, 가로 크기는 M이고, 편의상 1×1크기의 칸으로 나누어져 있다. 가장 바깥 행과 열은 모두 막혀져 있고, 보드에는 구멍이 하나 있다. 빨간 구슬과 파란 구슬의 크기는 보드에서 1×1크기의 칸을 가득 채우는 사이즈이고, 각각 하나씩 들어가 있다. 게임의 목표는 빨간 구슬을 구멍을 통해서 빼내는 것이다. 이 때, 파란 구슬이 구멍에 들어가면 안된다.
>
>이 때, 구슬을 손으로 건드릴 수는 없고, 중력을 이용해서 이리 저리 굴려야 한다. 왼쪽으로 기울이기, 오른쪽으로 기울이기, 위쪽으로 기울이기, 아래쪽으로 기울이기와 같은 네 가지 동작이 가능하다.
>
>각각의 동작에서 공은 동시에 움직인다. 빨간 구슬이 구멍에 빠지면 성공이지만, 파란 구슬이 구멍에 빠지면 실패이다. 빨간 구슬과 파란 구슬이 동시에 구멍에 빠져도 실패이다. 빨간 구슬과 파란 구슬은 동시에 같은 칸에 있을 수 없다. 또, 빨간 구슬과 파란 구슬의 크기는 한 칸을 모두 차지한다. 기울이는 동작을 그만하는 것은 더 이상 구슬이 움직이지 않을 때 까지이다.
>
>보드의 상태가 주어졌을 때, 최소 몇 번 만에 빨간 구슬을 구멍을 통해 빼낼 수 있는지 구하는 프로그램을 작성하시오.
>
>### 입력
>
>첫 번째 줄에는 보드의 세로, 가로 크기를 의미하는 두 정수 N, M (3 ≤ N, M ≤ 10)이 주어진다. 다음 N개의 줄에 보드의 모양을 나타내는 길이 M의 문자열이 주어진다. 이 문자열은 '`.`', '`#`', '`O`', '`R`', '`B`' 로 이루어져 있다. '`.`'은 빈 칸을 의미하고, '`#`'은 공이 이동할 수 없는 장애물 또는 벽을 의미하며, '`O`'는 구멍의 위치를 의미한다. '`R`'은 빨간 구슬의 위치, '`B`'는 파란 구슬의 위치이다.
>
>입력되는 모든 보드의 가장자리에는 모두 '`#`'이 있다. 구멍의 개수는 한 개 이며, 빨간 구슬과 파란 구슬은 항상 1개가 주어진다.
>
>### 출력
>
>최소 몇 번 만에 빨간 구슬을 구멍을 통해 빼낼 수 있는지 출력한다. 만약, 10번 이하로 움직여서 빨간 구슬을 구멍을 통해 빼낼 수 없으면 -1을 출력한다.
>
>### 예제 입력 2
>
>```
>7 7
>#######
>#...RB#
>#.#####
>#.....#
>#####.#
>#O....#
>#######
>```
>
>### 예제 출력 2
>
>```
>5
>```



## Solution

문제 자체는 딱 봐도 BFS다. 그런데 일반적인 BFS와는 다음의 두 가지가 다르다.

1. 노브레이크
2. 움직이는 오브젝트가 둘

원래 나는 BFS를 외워서 푸는데, 이 두 가지 때문에 엄청나게 많은 차이가 발생하고 예외처리를 해줘야하는 부분도 어마무시하게 많았다. 라인 수부터 무려 160줄에 육박한다...

```c++
#include <iostream>
#include <vector>
#include <queue>

#define MAX_SIZE 10
using namespace std;

class Coord {
public:
    int y;
    int x;
    int By;
    int Bx;
    int count;
    bool isSuccess;

    Coord() {}

    Coord(int y, int x, int By, int Bx, int count, bool isSuccess) {
        this->y = y;
        this->x = x;
        this->By = By;
        this->Bx = Bx;
        this->count = count;
        this->isSuccess = isSuccess;
    }
};

char map[MAX_SIZE][MAX_SIZE];
int rowSize, colSize;
int isVisited[MAX_SIZE][MAX_SIZE][MAX_SIZE][MAX_SIZE];
queue<Coord *> bfsQ;

vector<Coord *> findNextSteps(Coord *curPos) {
    vector<Coord *> nextSteps;

    int count = curPos->count;
    if (count == 10) return nextSteps;
    int ySteps[4] = {0, 1, 0, -1};
    int xSteps[4] = {1, 0, -1, 0};
    for (int i = 0; i < 4; i++) {
        bool isBlueEscaped = false;
        int nextY = curPos->y + ySteps[i];
        int nextX = curPos->x + xSteps[i];
        int nextBy = curPos->By + ySteps[i];
        int nextBx = curPos->Bx + xSteps[i];
        if (map[nextY][nextX] == '#' && map[nextBy][nextBx] == '#') continue;
        if (isVisited[nextY][nextX][nextBy][nextBx]) continue;

        bool blueShouldBack = false;
        while (map[nextBy][nextBx] != '#') {
            if (map[nextBy][nextBx] == 'O') {
                isBlueEscaped = true;
                break;
            }
            if (nextBy == curPos->y && nextBx == curPos->x) blueShouldBack = true;
            nextBy += ySteps[i];
            nextBx += xSteps[i];
        }
        if (isBlueEscaped) continue;

        bool isSuccess = false;
        bool redShouldBack = false;
        while (map[nextY][nextX] != '#') {
            if (map[nextY][nextX] == 'O') {
                isSuccess = true;
                break;
            }
            if (nextY == curPos->By && nextX == curPos->Bx) redShouldBack = true;
            nextY += ySteps[i];
            nextX += xSteps[i];
        }

        int stepBacks[4] = {-ySteps[i], -xSteps[i], -ySteps[i], -xSteps[i]};
        if (redShouldBack) {
            stepBacks[0] *= 2;
            stepBacks[1] *= 2;
        }
        if (blueShouldBack) {
            stepBacks[2] *= 2;
            stepBacks[3] *= 2;
        }

        if(isSuccess) {
            nextSteps.push_back(new Coord(0, 0, 0, 0, count+1, true));
            continue;
        }
        Coord *nextStepCandidate = new Coord(
                nextY + stepBacks[0],
                nextX + stepBacks[1],
                nextBy + stepBacks[2],
                nextBx + stepBacks[3],
                count + 1,
                isSuccess
        );
        if (nextStepCandidate->y == curPos->y &&
            nextStepCandidate->x == curPos->x &&
            nextStepCandidate->By == curPos->By &&
            nextStepCandidate->Bx == curPos->Bx)
            continue;
        nextSteps.push_back(nextStepCandidate);
        isVisited[nextStepCandidate->y][nextStepCandidate->x][nextStepCandidate->By][nextStepCandidate->Bx] = 1;
    }

    return nextSteps;
}

int solution(vector<string> mapVector) {
    int minCount = 0;
    rowSize = mapVector.size();
    colSize = mapVector[0].size();

    Coord *goal;
    Coord *curPos = new Coord();
    curPos->count = 0;
    for (int i = 0; i < rowSize; i++) {
        for (int j = 0; j < colSize; j++) {
            if (mapVector[i][j] == 'R') {
                curPos->y = i;
                curPos->x = j;
                map[i][j] = '.';
                continue;
            }
            if (mapVector[i][j] == 'B') {
                curPos->By = i;
                curPos->Bx = j;
                map[i][j] = '.';
                continue;
            }
            map[i][j] = mapVector[i][j];
        }
    }
    bfsQ.push(curPos);

    while (!bfsQ.empty()) {
        curPos = bfsQ.front();
        bfsQ.pop();
        vector<Coord *> nextSteps = findNextSteps(curPos);
        for (auto nextStep : nextSteps) {
            if (nextStep->isSuccess) return nextStep->count;
            bfsQ.push(nextStep);
        }
    }

    return -1;
}

int main() {
    int N, M;
    cin >> N >> M;
    vector<string> map;
    for(int i=0; i<N; i++) {
        string mapRow;
        cin >> mapRow;
        map.push_back(mapRow);
    }
    cout << solution(map) << endl;
    return 0;
}
```



## Retrospect

삼성 알고리즘 시험 넘모 어렵다... 

#### Modified BFS (Defining a Problem)

지금 이 글을 인공지능 수업시간에 쓰고 있는데 교수님이 **문제공간을 정의하는 것**이 아주 중요하다는 말씀을 하셨다. 여기서도 마찬가지인 것 같다. 변형된 BFS는 큐에 무엇을 넣어야 하는가를 정의하는 게 매우 중요하다고 생각된다.

* No Brake

  기존의 BFS는 한 칸씩 움직이기 때문에 큐에다가 별로 깊게 생각 안하고 좌표쌍을 넣어두면 됐었다. 하지만 얘는 벽에 부딪힐 때까지 멈추지 않고 쭉 나아간다. 결국 마지막으로 종착한 지점을 기준으로 생각하면 된다.

* Multiple Moving Objects

  여기서는 움직이는 물체가 둘이다. 따라서 방문여부를 정의하는 방식이 약간 달라져야 한다. 두 물체의 좌표가 동시에 고려되어야 하기 때문에 방문 테이블을 2차원이 아닌 4차원으로 만들어서 방문여부 검사를 해주어야 한다.

#### 이상/이하/초과/미만

> 만약, 10번 이하로 움직여서 빨간 구슬을 구멍을 통해 빼낼 수 없으면 -1을 출력한다.

저게 10번부터 -1인지 11번부터 -1인지가 대충 읽으면 은근히 헷갈린다. 저런 조건이 나오면 일단 긴장해야 하고, 10번 움직여야 하는 케이스와 11번 움직여야 하는 케이스를 각각 만들어 제대로 출력되는지까지 확인해야 한다.

#### 충돌판정과 종료조건의 공존

목표지점('O')에 도달했는지 여부를 충돌판정과 같은 로직(벽에 일단 들어갔다가 자신이 서있는 곳이 '#'이면 나중에 다시 한 칸 물러난다)으로 짜두었다. 그런데 목표지점에 도달했을 때는 한 칸 물러나기를 바란게 아니었는데 얘 때문에 문제가 생겼다.

바로 이전 상황과 파란 구슬과 빨간 구슬의 위치가 같으면 움직인 것으로 간주하지 않게 해놨는데 목표지점까지 빨간 구슬이 한 칸 딱 움직이는 경우 다시 물러나면서 골인이 안되는 문제가 있었다(사실 함수분리를 안하고 메인에서 다 짜면 바로 리턴하면서 끝내면 그만이라 생기지 않는 문제긴 하다). 

조건이 여럿(예를 들어 A, B, C라 하자) 들어갈 때는 A와 B가 조건이 겹쳐 A를 타게팅한 C 조건이 B까지 싸잡아 처리해버리는 경우가 있지 않은지 주의하도록 하자.
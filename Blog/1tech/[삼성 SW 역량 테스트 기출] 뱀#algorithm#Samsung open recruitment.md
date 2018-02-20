이 문제는 사실 [크로아티아 올림피아드 전국 1차 예선 주니어](http://hsin.hr/2005/national/first_day/juniors/problems.pdf) 문제다. 삼성이 문제 만들기 오지게 귀찮았나보다.



## Problem

[https://www.acmicpc.net/problem/3190](https://www.acmicpc.net/problem/3190)

> | 시간 제한 | 메모리 제한 | 제출  | 정답 | 맞은 사람 | 정답 비율 |
> | --------- | ----------- | ----- | ---- | --------- | --------- |
> | 1 초      | 128 MB      | 10440 | 2465 | 1686      | 22.880%   |
>
> ## 문제
>
>  'Dummy' 라는 도스게임이 있다. 이 게임에는 뱀이 나와서 기어다니는데, 사과를 먹으면 뱀 길이가 늘어난다. 뱀이 이리저리 기어다니다가 벽 또는 자기자신의 몸과 부딪히면 게임이 끝난다.
>
> 게임은 NxN 정사각 보드위에서 진행되고, 몇몇 칸에는 사과가 놓여져 있다. 보드의 상하좌우 끝에 벽이 있다. 게임이 시작할때 뱀은 맨위 맨좌측에 위치하고 뱀의 길이는 1 이다. 뱀은 처음에 오른쪽을 향한다.
>
> 뱀은 매 초마다 이동을 하는데 다음과 같은 규칙을 따른다.
>
> - 먼저 뱀은 몸길이를 늘려 머리를 다음칸에 위치시킨다.
> - 만약 이동한 칸에 사과가 있다면, 그 칸에 있던 사과가 없어지고 꼬리는 움직이지 않는다.
> - 만약 이동한 칸에 사과가 없다면, 몸길이를 줄여서 꼬리가 위치한 칸을 비워준다. 즉, 몸길이는 변하지 않는다.
>
> 사과의 위치와 뱀의 이동경로가 주어질 때 이 게임이 몇 초에 끝나는지 계산하라.
>
> ## 입력
>
> 첫째줄에 보드의 크기 N이 주어진다. (2 ≤ N ≤ 100) 다음 줄에 사과의 개수 K가 주어진다. (0 ≤ K ≤ 100)
>
> 다음 K개의 줄에는 사과의 위치가 주어지는데, 첫번째 정수는 행, 두번째 정수는 열 위치를 의미한다. 사과의 위치는 모두 다르며, 맨 위 맨 좌측 (1행 1열) 에는 사과가 없다.
>
> 다음 줄에는 뱀의 방향 변환 횟수 L 이 주어진다. (1 ≤ L ≤ 100)
>
> 다음 L개의 줄에는 뱀의 방향 변환 정보가 주어지는데,  정수 X와 문자 C로 이루어져 있으며. X초가 끝난 뒤에 왼쪽(C가 'L') 또는 오른쪽(C가 'D')로 90도 방향을 회전시킨다는 뜻이다. X는 10,000 이하의 양의 정수이며, 방향 전환 정보는 X가 증가하는 순으로 주어진다.
>
> ## 출력
>
> 첫째 줄에 게임이 몇 초에 끝나는지 출력한다.
>
> ## 예제 입력 1
>
> ```
> 6
> 3
> 3 4
> 2 5
> 5 3
> 3
> 3 D
> 15 L
> 17 D
> ```
>
> ## 예제 출력 1
>
> ```
> 9
> ```
>
> ## 예제 입력 2
>
> ```
> 10
> 4
> 1 2
> 1 3
> 1 4
> 1 5
> 4
> 8 D
> 10 D
> 11 D
> 13 L
> ```
>
> ## 예제 출력 2
>
> ```
> 21
> ```
>
> ## 예제 입력 3
>
> ```
> 10
> 5
> 1 5
> 1 3
> 1 2
> 1 6
> 1 7
> 4
> 8 D
> 10 D
> 11 D
> 13 L
> ```
>
> ## 예제 출력 3
>
> ```
> 13
> ```



## Solution

[여기](http://hsin.hr/2005/index.html)에 가면 실제 대회에서 주어진 입출력셋들을 구할 수 있다. 디버깅은 이걸로 하자.

```c++
#include <iostream>
#include <vector>

#define MAX_MAP_SIZE 100

using namespace std;

class SnakeNode {
public:
    int y, x, direction;

    SnakeNode(int y, int x, int direction) {
        this->y = y;
        this->x = x;
        this->direction = direction;
    }
};

int mapSize = 0;
int map[MAX_MAP_SIZE + 2][MAX_MAP_SIZE + 2];
vector<SnakeNode *> snake;

void initMap(int _mapSize, vector<pair<int, int>> appleCoords) {
    mapSize = _mapSize;

    // TODO: optimize this
    for (int i = 0; i <= mapSize + 1; i++) {
        for (int j = 0; j <= mapSize + 1; j++) {
            if (i == 0 || j == 0 || i == mapSize + 1 || j == mapSize + 1)
                map[i][j] = -1;
        }
    }

    for (auto appleCoord : appleCoords) {
        map[appleCoord.first][appleCoord.second] = 1;
    }
    snake.push_back(new SnakeNode(1, 1, 0));
}

bool isCollision(int y, int x) {
    if(map[y][x] < 0) return true;
    for(auto snakeNode : snake) {
        if(snakeNode->y == y && snakeNode->x == x) return true;
    }
    return false;
}

int moveSnake(int turn) {
    int ySteps[4] = {0, 1, 0, -1};
    int xSteps[4] = {1, 0, -1, 0};

    SnakeNode *head = snake.back();
    int direction = (head->direction + turn + 4) % 4;
    int y = head->y + ySteps[direction];
    int x = head->x + xSteps[direction];

    if(isCollision(y, x)) return -1;
    SnakeNode *newSnakeHead = new SnakeNode(y, x, direction);
    snake.push_back(newSnakeHead);
    if (map[y][x] != 1) snake.erase(snake.begin());
    else map[y][x] = 0;

    return 0;
}

int solution(int _mapSize, vector<pair<int, int>> appleCoords, vector<pair<int, char>> snakeLogs) {
    int totalSec = 0;
    initMap(_mapSize, appleCoords);

    int turn = 0;
    int sec = 0;
    for (auto snakeLog : snakeLogs) {
        sec = snakeLog.first - totalSec;
        for (int i = 0; i < sec; i++) {
            totalSec++;
            if(moveSnake((i == 0 ? turn : 0)) < 0) return totalSec;
        }
        turn = snakeLog.second == 'L' ? -1 : 1;
    }
    totalSec++;
    if(moveSnake(turn) < 0) return totalSec;
    while(true) {
        totalSec++;
        if(moveSnake(0) < 0) return totalSec;
    }
}

int main() {
    int N, K;
    cin >> N;
    cin >> K;
    vector<pair<int, int>> appleCoords;
    for(int i=0; i<K; i++) {
        int y, x;
        cin >> y >> x;
        appleCoords.push_back({y, x});
    }
    int L;
    cin >> L;
    vector<pair<int, char>> snakeLogs;
    for(int i=0; i<L; i++) {
        int sec;
        char turn;
        cin >> sec >> turn;
        snakeLogs.push_back({sec, turn});
    }
    cout << solution(N, appleCoords, snakeLogs) << endl;
    
    return 0;
}
```

핵심은 뱀의 각 노드를 별도로 분리해서 생각하는 것이다. 명작 인디게임인 '바인딩 오브 아이작'을 해본 적이 있는 사람이라면 그 게임에 나오는 [한 보스몹](https://bindingofisaacrebirth.gamepedia.com/Larry_Jr.) 덕분에 이 생각을 쉽게 할 수 있다. 

| <img src="https://raw.githubusercontent.com/3jins/Images/master/larry-jr.png" width="100%"/> |
| :----------------------------------------------------------: |
|               각 몸뚱이가 별도로 히트판정된다.               |

그 외에는 그냥 문제에서 준 조건대로 꼼꼼히 풀면 된다.



## Retrospect

삼성문제는 실수하지 않고 짜는 게 핵심이다. 우리 어머니께서는 삼성이 노트7 배터리 폭탄 사건 때문에 실수 없이 코드 짜는 사람을 선호하고 있는 게 아니냐고 추측하셨다.

#### 음수를 양수로 나눈 나머지

`%` 연산은 절대값을 기준으로 한다. 즉, `-1 % 4`는 -1이지, 3이 나오지 않는다는 것이다.

#### 누적값

이 문제에서 뱀의 방향 변환 정보들을 쭉 주는데, 여기에서 주는 시간값이 누적값이다. 다음 명령으로부터 X초 후가 아니라, 처음 게임 시작으로부터 X초 후에 방향을 전환한다는 의미다. 처음에 이걸 잘못 이해해서 코드가 제대로 안 돌아갔었다.

#### 인덱스의 시작은 0일 수도 있고 1일 수도 있다

당연히 인덱스의 시작은 0이라고만 생각했다. 이 문제에서는 가장 좌측상단의 좌표를 (0, 0)이 아니라 (1, 1)로 줬다.

#### 쓰고 났으면 치워라

내가 어릴 때 우리 어머니께서 나에게 하시던 잔소리다. ~~이번 문제 retrospect에만 우리 어머니가 두 번이나 나오신다.~~ 직관적인 인터페이스라 하더라도 그게 사람에게 직관적인거지, 컴퓨터에게 직관적인 게 아니다. 뱀이 사과 위치를 지나갔으면 우리가 사과를 직접 없애줘야 한다. 
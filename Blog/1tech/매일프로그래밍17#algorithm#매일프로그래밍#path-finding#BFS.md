16주차 문제는 머지소트의 응용 같은 느낌이었다. 별로 어렵지 않으므로 생략하고 17주차로 넘어가겠다. 17주차 문제는 최단거리를 구하는 문제로 BFS를 쓰면 풀린다. 아마 DFS 써도 풀릴 것 같지만 난 그냥 BFS로만 풀겠다. 



## Problem

> 0과 1로 만들어진 2D 정수 배열이 있습니다. 0은 장애물이고 1은 도로일때, 두 좌표가 주어지면, 첫번째 좌표에서 두번째 좌표까지 가장 가까운 거리를 구하시오. 두 좌표는 모두 도로에서 시작되고 좌, 우, 아래, 위로 움직이며 대각선으로는 움직일 수 없습니다. 만약 갈 수 없다면 -1을 리턴하시오.
>
> 예제)
>
> Input:
>
> {{**1**, 0, 0, **1**, **1**, 0},
>
> {**1**, 0, 0, **1**, 0, 0},
>
> {**1**, **1**, **1**, **1**, 0, 0},
>
> {1, 0, 0, 0, 0, 1},
>
> {1, 1, 1, 1, 1, 1}}
>
> Start: (0, 0)
>
> Finish: (0, 4)
>
> Output: 8



## Trial

```cpp
#include <iostream>
#include <vector>
#include <queue>

using namespace std;

queue<pair<pair<int, int>, int>> bfsQ;
vector<vector<int>> isVisited;

bool isVisitable(vector<vector<int>> map, pair<int, int> pos) {
    if (pos.first < 0) return false;
    if (pos.second < 0) return false;
    if (pos.first >= map.size()) return false;
    if (pos.second >= map[0].size()) return false;
    if (map[pos.first][pos.second] != 1) return false;
    return true;
}

vector<pair<pair<int, int>, int>> findNeighbors(vector<vector<int>> map, pair<pair<int, int>, int> curPos) {
    vector<pair<pair<int, int>, int>> neighbors;
    int numDirections = 4;
    int coordsY[numDirections] = {-1, 0, 0, 1};
    int coordsX[numDirections] = {0, -1, 1, 0};
    for (int i = 0; i < numDirections; i++) {
        pair<pair<int, int>, int> neighborCandidate = {
                {
                        curPos.first.first + coordsY[i],
                        curPos.first.second + coordsX[i]
                },
                curPos.second + 1
        };
        if (isVisitable(map, neighborCandidate.first)) {
            neighbors.emplace_back(neighborCandidate);
        }
    }
    return neighbors;
}

int isEnd(pair<int, int> curPos, pair<int, int> finish) {
    if (curPos == finish) return 1;
    return 0;
}

int getShortestDistance(vector<vector<int>> map, pair<int, int> start, pair<int, int> finish) {
    isVisited = map;
    pair<pair<int, int>, int> curPos = {{start.first, start.second}, 0};
    bfsQ.push(curPos);

    while (!bfsQ.empty()) {
        curPos = bfsQ.front();
        bfsQ.pop();
//        cout << curPos.first.first << curPos.first.second << endl;
        if(isEnd(curPos.first, finish)) {
            return curPos.second;
        }
        isVisited[curPos.first.first][curPos.first.second] = 0;
        vector<pair<pair<int, int>, int>> neighbors = findNeighbors(map, curPos);
        vector<pair<pair<int, int>, int>>::iterator neighbor;
        for (neighbor = neighbors.begin(); neighbor != neighbors.end(); neighbor++) {
            pair<int, int> neighborPos = neighbor->first;
            if (isVisited[neighborPos.first][neighborPos.second] == 0) continue;
            bfsQ.push(*neighbor);
        }
    }

    return -1;
}
```



## Solution

실제 해답과 내 답에 큰 차이는 없었다. 다만, 내 답은 노드의 자료형을 `pair<pair<int, int>, int>`라는 거대한 형태로 통째로 들고 다니는데 반해, 해답에서는 이 자료형을 클래스로 묶어서 들고 다녔다. 앞으로는 그렇게 짜야겠다.



## Retrospect

#### BFS

[BFS](https://enhanced.kr/postviewer/593) 문제는 신경 쓸 게 많아서 풀 때 복잡하지만, 어느 정도 정형화돼 있어서 뭘 신경 써야 하는지 외워놓으면 수월하게 풀 수 있다.

지금은 시험기간이다. 하지만 시험공부를 격렬하게 하기 싫으므로 인생에 도움이 되는 게 확실한 알고리즘 공부나 하기로 했다. 시험을 망해서 삶이 조금 어려워지더라도 그 시간에 놀거나 멍하니 보내지 않고 알고리즘 공부를 하면 그걸 상쇄할 수 있으리라 본다. 



## Problem

> 간격(interval)으로 이루어진 배열이 주어지면, 겹치는 간격 원소들을 합친 새로운 배열을 만드시오. 간격은 시작과 끝으로 이루어져 있으며 시작은 끝보다 작거나 같습니다.
>
> 예제)
> Input: {{2,4}, {1,5}, {7,9}}
> Output: {{1,5}, {7,9}}
> Input: {{3,6}, {1,3}, {2,4}}
> Output: {{1,6}}



## Trial

딱 보자마자 소프트웨어 마에스트로 스터디에서 얼핏 들었던 [union-find](http://enhanced.kr/postviewer/282)가 생각나 찾아보기로 했다.
실제로 disjoint set 만들어서 다 merge 시키면 합쳐져야 하는 구간에 있는 애들끼리 똑같은 집합 안에 들어가면서 대충 정리된다.

```c++
#include <iostream>
#include <vector>

using namespace std;

class DisjointSet {
public:
    vector<int> parent;
    vector<int> rank;

    void makeSet(int n) {
        for (int i = 0; i <= n; i++) {
            parent.push_back(i);
            rank.push_back(1);
        }
    }

    int find(int u) {
        if (u == parent[u]) return u;
        return parent[u] = find(parent[u]);
    }

    void merge(int u, int v) {
        if (rank[u] > rank[v]) merge(v, u);
        parent[u] = parent[v];
        if (rank[u] == rank[v]) rank[v]++;
    }
};

int getMax(vector<vector<int>> v) {
    int size = v.size();
    int max = 0;
    for (int i = 0; i < size; i++) {
        if (v[i][1] > max) max = v[i][1];
    }
    return max;
}

void printResult(vector<int> parent) {
    int size = parent.size();
    int start = 1, end = 0;

    for (int i = 2; i <= size; i++) {
        if (parent[i] != parent[i - 1]) {
            end = i - 1;
            if (start != end) {
                cout << "{" << start << ", " << end << "}" << ", ";
            }
            start = i;
        }
    }
}

void printMergedArrays() {
    vector<vector<int>> input = {{3, 6},
                                 {1, 3},
                                 {2, 4}};
    int size = input.size();
    int max = getMax(input);
    DisjointSet ds;
    ds.makeSet(max);

    for (int i = 0; i < size; i++) {
        int start = input[i][0];
        int end = input[i][1];
        for (int j = start + 1; j <= end; j++) {
            ds.merge(j, start);
        }
    }
    for(int i = 1; i <= max; i++) {
        ds.find(i); // path compress
    }

    printResult(ds.parent);
}
```



## Solution

해답은 Disjoint Set 없이 풀더라. 그냥 {start, end} 주어졌을 때 start 순으로 정렬해놓고 loop 돌면서 다음 start가 지금 end보다 작으면 합치는 방식을 썼다. 코드는 생략하겠다.



## Retrospect

그렇게 어려운 문제는 아니었지만 수확이 많아서 뿌듯하다. 역시 시험기간에는 시험공부 말고 딴 짓을 해야 생산성이 높다.

#### Disjoint Set

합집합에 관한 문제라 적용해봤다. 잘 되더라.

#### 2차원 정렬

Solution에서 쓴 방법은 2차원 벡터 중 한 원소를 기준으로 잡아 정렬하는 방식이었다. 뭐 사실 그냥 버블소트로 짜면 금방 짜지만 $O(n^2)$을 감안해야 하고 그렇다고 그 짧은 시간동안 퀵소트 같은 걸로 짜고 있기도 좀 뭐하다. 1차원 정렬은 라이브러리로 이것저것 많이 제공되는걸 아는데 2차원 정렬에 대해서는 잘 모르므로 이에 대해 [정리]()해보기로 했다.
시험공부를 하는 방법은 두 가지가 있다. 하나는 일단 시험기간까지 모든 강의자료를 모아뒀다가 시험기간에 한 번에 공부하는 것이고, 다른 하나는 매 시간 강의자료를 받을 때마다 공부하는 것이다. 큐에 데이터를 정렬해 담는 방법 또한 마찬가지다. 하나는 일단 다 때려부어넣고 나중에 정렬하는 것이고, 다른 하나는 집어넣을 때 제자리를 찾아 집어넣는 것이다. 후자의 방법을 자동으로 수행해주는 STL 큐가 있으니 바로 이번 포스트의 주제인 **priority queue** 되시겠다. 따라서 벼락치기의 위험성과 철저한 예복습의 중요성을 아는 사람이라면 priority queue가 얼마나 중요한 자료구조인지 느낌이 올 것이다.



## 사용법

#### 기본 사용법

별거 없다. 그냥 push하면 알아서 내림차순으로 정렬돼서 들어간다.

```c++
priority_queue<int> pq;
pq.push(0);
pq.push(7);
pq.push(1);
pq.push(3);

while(!pq.empty()) {
  cout << pq.top() << " ";
  pq.pop();
}
```

```
7 3 1 0
```

#### 단일 숫자 자료형이 아닌 경우

`int`만 쓴다는 보장은 없다. 만약 `pair`를 쓴다면? 첫 번째 값을 기준으로 정렬된다.

```c++
priority_queue<pair<int, int>> pq;
pq.push({0, 1});
pq.push({7, 9});
pq.push({1, 9});
pq.push({3, 3});

while(!pq.empty()) {
  cout << "{" << pq.top().first << ", " << pq.top().second << "} ";
  pq.pop();
}
```

```
{7, 9} {3, 3} {1, 9} {0, 1}
```

이는 `vector`의 경우에도 마찬가지다.

```c++
priority_queue<vector<int>> pq;
pq.push({0, 1, 2});
pq.push({7, 9, 0});
pq.push({1, 9, 1});
pq.push({3, 3, 8});

while(!pq.empty()) {
  cout << "{" << pq.top()[0] << ", " << pq.top()[1] << ", " << pq.top()[2] << "} ";
  pq.pop();
}
```

```
{7, 9, 0} {3, 3, 8} {1, 9, 1} {0, 1, 2}
```

#### 오름차순 정렬

오름차순으로 바꾸고 싶다면? 다음처럼 하면 된다. 두번째 인자는 내부 컨테이너 타입을 지정해주는 것이고, 세 번째 인자는 오름차순으로 바꾸겠다는 의미다.

```c++
priority_queue<int, vector<int>, greater<int>> pq;
pq.push(0);
pq.push(7);
pq.push(1);
pq.push(3);

while(!pq.empty()) {
  cout << pq.top() << " ";
  pq.pop();
}
```

```
0 1 3 7
```

`pair`를 쓰는데 오름차순으로 정렬하고 싶다면 다음처럼 해주면 된다.

```c++
priority_queue<pair<int, int>, vector<pair<int, int>>, greater<pair<int, int>>> pq;
pq.push({0, 1});
pq.push({7, 9});
pq.push({1, 9});
pq.push({3, 3});

while(!pq.empty()) {
  cout << "{" << pq.top().first << ", " << pq.top().second << "} ";
  pq.pop();
}
```

```
{0, 1} {1, 9} {3, 3} {7, 9}
```

#### 임의 정렬

이런 경우를 생각해볼 수 있다. 

> `pair`의 첫번째 항 순서대로 오름차순 정렬하되, 같은 값일 경우 두번째 항 순서대로 내림차순 정렬하고 싶다.

이런 특이하고 복잡한 정렬을 위해 사용자가 직접 정렬함수를 만들 수 있게 되어 있다.

```c++
struct cmp {
    bool operator()(pair<int, int> a, pair<int, int> b){
        if(a.first == b.first) return a.second < b.second;
        return a.first > b.first;	// 이게 오름차순이다. 주의하자.
    }
};

priority_queue<pair<int, int>, vector<pair<int, int>>, cmp> pq;
pq.push({1, 0});
pq.push({9, 7});
pq.push({9, 1});
pq.push({3, 3});

while(!pq.empty()) {
  cout << "{" << pq.top().first << ", " << pq.top().second << "} ";
  pq.pop();
}
```

```
{1, 0} {3, 3} {9, 7} {9, 1} 
```


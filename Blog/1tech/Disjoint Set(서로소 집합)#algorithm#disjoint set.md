우리말로는 서로소 집합이라고도 부른다. 서로소 집합이란, 교집합이 없는 두 집합을 의미한다. 배열 구조로 표현하기도 한다는데 시간복잡도가 쓰레기라고 하니 패스하자(정확히는 Union 연산의 시간복잡도가 쓰레기고 Find 연산은 오히려 더 빠르다고 한다. 하지만 Union 연산이 Find 연산보다 훨씬 많이 쓰인다고 한다). 

보통 Disjoint Set은 **트리들의 집합** 형태로 표현되는 자료구조다. 각 트리의 **루트노드**의 원소가 **집합번호**가 된다. Disjoint Set에는 `MakeSet`, `Find`, `Union`의 세 함수가 있다.



## 구현

### MakeSet

n개의 노드를 만들고 각 노드가 각각의 집합이 되게 한다. 경우에 따라 따로 함수로 분리하지 않고 main함수에서 그냥 초기화 하기도 한다.

```c++
void makeSet(int n) {
  for(int i = 0; i <= n; i++) {
    parent.push_back(i);	// parent는 int형 배열(또는 vector)으로 전역변수로 초기화돼있다.
  }
}
```



### Find

노드의 루트를 찾는다.

```c++
int find(int x) {
  if(x == parent[x]) return x;
  return find(parent[x]);
}
```



### Union (Merge)

두 노드가 서로 다르다면 한 노드를 다른 한 노드의 자식으로 넣는다. C/C++에서는 Union이 예약어기 때문에 보통 함수 이름을 `merge`라고 짓는다.

```c++
void merge(int u, int v) {
  parent[find(u)] = find(v);
}
```



### 추가 설명

만약 이 글을 읽고 있는 당신이 나와 비슷한 수준으로 멍청하다면 위의 코드를 읽으며 두 가지 의문이 들었을 것이다(의문이 들지 않고 그냥 이해됐다면 이 섹션은 읽을 필요 없다).

1. 왜 disjoint set 자료구조의 이름이 parent인가
2. tree 구조라더니 union에서 왜 자식요소를 추가하는 게 아니라 배열 원소를 그냥 덮어써버리는가

두 의문은 한 번에 해결 가능하다. 실제로 union-find가 적용된 코드를 보면 이해가 좀 된다.

```c++
vector<int> parent;

void makeSet(int n) {
  for(int i = 0; i <= n; i++) {
    parent.push_back(i);	// parent는 int형 배열(또는 vector)으로 전역변수로 초기화돼있다.
  }
}

int find(int u) {
  if(u == parent[u]) return u;
  return find(parent[u]);
}

void merge(int u, int v) {
  parent[find(u)] = find(v);
}

int main() {
  makeSet(5);		// parent: {1, 2, 3, 4, 5}
  merge(1, 2);	// parent: {2, 2, 3, 4, 5}
  merge(2, 3);	// parent: {2, 3, 3, 4, 5}
  return 0;
}
```

disjoint set의 각 노드는 (자식을 찾는 연산이 없기 때문에) 자신의 자식은 저장할 필요 없이 부모만 누군지 알면 된다. 또한 원래 자기자신의 값을 저장하고 있어야 하지만 배열이나 벡터에 순서대로 노드를 저장함으로써 인덱스가 곧 자기자신의 값이 되게 할 수 있는 것이다.

즉, `parent`는 각 노드의 부모노드를 저장하는 배열이며 `merge`에 의해 부모노드를 갱신하는 방식인 것이다.



## 최적화 

위의 코드로는 skewed 트리가 될 가능성이 있다. 본래 시간복잡도를 줄이는 것은 워스트케이스를 대비하기 위함이기 때문에 최적화되지 않은 트리기반 union-find는 의미가 없다.



### Union by Rank

최적화된 disjoint-set을 `union-by-rank`라 한다. 이 자료구조에서는 `parent` 외에 추가적으로 `rank`가 있어서 트리의 높이를 저장한다. union 연산을 수행할 때는 항상 rank가 작은 쪽을 큰 쪽의 root 아래에 집어넣어서 트리의 높이가 늘어나는 걸 최대한 막으며 두 트리의 높이가 같은 경우에만 rank를 1증가시킨다.

```c++
void merge(int u, int v) {
  // u의 높이가 더 크면 swap
  if(rank[find(u)] > rank[find(v)])	merge(v, u);
  
  // u를 v 밑으로
  parent[u] = parent[v];	
  
  // u와 v의 높이가 같으면 v의 높이를 ++
  if(rank[find(u)] == rank[find(v)])	rank[v]++;
}
```



### 경로 압축 (Path Compression)

`find` 함수 역시 어느 정도 최적화가 가능하다. 사실 각 node가 parent node를 저장하고 있을 필요 없이 root node만 저장하고 있어도 된다. 왜냐하면 disjoint set에서 중요한건 각 node가 어떤 집합(root node)에 속해있느냐 뿐이기 때문이다.

```c++
int find(int u) {
  if (u == parent[u]) return u;
  return parent[u] = find(parent[u]);	// parent[u]를 갱신하고 그 값을 반환
}
```










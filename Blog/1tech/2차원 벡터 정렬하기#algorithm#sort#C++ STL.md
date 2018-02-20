정렬은 알고리즘 문제를 풀 때 오지게 많이 쓰인다. 버블소트 같은거야 30초면 짜지만 시간복잡도가 쓰레기라 실제 문제를 풀 때 쓸 수 없는 경우가 많고 $O(n \log n)$ 시간이 걸리는 퀵소트, 머지소트, 힙소트는 필요할 때마다 짜서 쓰기가 쉽지 않다. 

그나마 1차원 벡터를 정렬하는 경우라면 깃헙 같은데다가 코드 박아놓고 필요할 때 복붙하면 되지만 2차원 벡터를 정렬해야 한다면 그걸 또 변형해서 써야되는 등 이만저만 귀찮은 일이 아니다. 그러므로 이에 대해 각 잡고 정리해보는 시간을 갖도록 하자.




## 벡터의 정렬 (sort)

`algorithm` 표준라이브러리 헤더에 있는 `sort` 함수를 이용해서 다음처럼 벡터를 오름차순으로 정렬할 수 있다. [인트로소트](https://en.wikipedia.org/wiki/Introsort)라고 하는 퀵소트 기반의 변형 알고리즘을 기반으로 한다 [카더라](https://www.acmicpc.net/board/view/16770). 그래서 안정적으로 $O(n\log n)$ 시간이 걸린다고 한다. 즉, 소트 문제가 나오면 퀵소트 같은거 짠답시고 뻘짓하지 말고 그냥 이거 쓰면 되겠다.

```c++
#include <vector>
#include <algorithm>
using namespace std;

int main() {
  vector<int> v = {9, 3, 0, 7, 1, 3};
  sort(v.begin(), v.end());
  for(int i = 0; i < v.size(); i++)
  	cout << v[i] << " ";
  return 0;
}
```

내림차순이라면? 다음처럼 sort의 세번째 인자를 사용하면 된다.

```c++
#include <vector>
#include <algorithm>
using namespace std;

bool cmp(int a, int b) {
  return a > b;		// 나중 인자가 더 작아지는 방향으로
}

int main() {
  vector<int> v = {9, 3, 0, 7, 1, 3};
  sort(v.begin(), v.end(), cmp);
  for(int i = 0; i < v.size(); i++)
    cout << v[i] << " ";
  return 0;
}
```

cmp 함수 매번 만들기 헷갈리고 귀찮으면 `functional` 헤더를 이용하는 방법도 있다.

```c++
#include <vector>
#include <algorithm>
#include <functional>
using namespace std;

int main() {
  vector<int> v = {9, 3, 0, 7, 1, 3};
  sort(v.begin(), v.end(), greater<int>());
  for(int i = 0; i < v.size(); i++)
    cout << v[i] << " ";
  return 0;
}
```



## 2차원 벡터의 정렬

이제 2차원 벡터를 정렬하는 문제로 넘어가보도록 하자. {{9, 3, 0}, {7, 1, 3}, {2, 1, 8}}과 같은 2차원 벡터에서 n번째 원소를 기준으로 정렬하고 싶다면 어떻게 해야할까? C++의 STL sort에 대해 제대로 이해하고 있다면 의외로 간단하다. 바로 sort의 세번째 인수로 들어가는 콜백함수를 사용하면 되기 때문이다.

```c++
#include <vector>
#include <algorithm>
using namespace std;

bool cmp(vector<int> a, vector<int> b) {
  return a[2] > b[2];
}

int main() {
  vector<vector<int>> v = {{9, 3, 0}, {7, 1, 3}, {2, 1, 8}};
  sort(v.begin(), v.end(), cmp);
  for(int i = 0; i < v.size(); i++) {
    for(int j = 0; j < 3; j++)
    	cout << v[i][j] << " ";
  	cout << endl;
  }
  return 0;
}
```


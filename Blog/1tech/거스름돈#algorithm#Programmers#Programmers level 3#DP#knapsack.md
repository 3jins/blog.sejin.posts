이 문제 풀다가 난 발전이 없다고 자괴감에 빠져버릴 뻔 했다. 하지만 이 문제는 처음 봤으면 못 푸는 게 당연하다(물론 아닌 사람들도 있다). 왜냐하면 이 문제는 네임드 알고리즘인 **[냅색](https://enhanced.kr/postviewer/1094)**을 이용해야 하기 때문이다.



## Problem

[https://programmers.co.kr/learn/courses/30/lessons/12907](https://programmers.co.kr/learn/courses/30/lessons/12907)

> Finn은 편의점에서 야간 아르바이트를 하고 있습니다. 야간에 손님이 너무 없어 심심한 Finn은 손님들께 거스름돈을 n 원을 줄 때 방법의 경우의 수를 구하기로 하였습니다.
>
> 예를 들어서 손님께 5원을 거슬러 줘야 하고 1원, 2원, 5원이 있다면 다음과 같이 4가지 방법으로 5원을 거슬러 줄 수 있습니다.
>
> - 1원을 5개 사용해서 거슬러 준다.
> - 1원을 3개 사용하고, 2원을 1개 사용해서 거슬러 준다.
> - 1원을 1개 사용하고, 2원을 2개 사용해서 거슬러 준다.
> - 5원을 1개 사용해서 거슬러 준다.
>
> 거슬러 줘야 하는 금액 n과 Finn이 현재 보유하고 있는 돈의 종류 money가 매개변수로 주어질 때, Finn이 n 원을 거슬러 줄 방법의 수를 return 하도록 solution 함수를 완성해 주세요.
>
> ##### 제한 사항
>
> - n은 100,000 이하의 자연수입니다.
> - 화폐 단위는 100종류 이하입니다.
> - 모든 화폐는 무한하게 있다고 가정합니다.
> - 정답이 커질 수 있으니, 1,000,000,007로 나눈 나머지를 return 해주세요.
>
> ------
>
> ##### 입출력 예
>
> |  n   |  money  | result |
> | :--: | :-----: | :----: |
> |  5   | [1,2,5] |   4    |



## Trial

일단 냅색을 공부하고 나서 다시 풀기 시작했기 때문에, 바로 냅색 문제로 변환시키려고 시도해봤다. 1원, 2원, 5원짜리 동전으로 5원을 만드는 문제를 (value, weight) = (1, 1), (2, 2), (5, 5)인 물건 셋을 5kg 가방에 넣는 문제로 생각해봤지만, 여기서는 냅색문제와 달리 동전의 수가 무한하고, 냅색 문제의 풀이는 최적값을 구하는 것이지 가짓수를 구하는 게 아니라 잘 적용되지 않았다.

생각을 바꿔서 냅색 문제의 아이디어만 차용해보기로 했다. 아이템 종류를 행으로, 무게를 열로 하는 2차원 배열을 만들고, 그 배열의 각 원소가 각 행과 열의 조건에서 가능한 가짓수를 나타내게끔 해봤다.

```c++
#include <string>
#include <vector>
#include <algorithm>

#define DIVISOR 1000000007

using namespace std;

int solution(int n, vector<int> money) {
    vector<vector<int>> cases;
    int numMoneyUnit = money.size();
    for (int i = 0; i < numMoneyUnit; i++) {
        vector<int> row;
        if (i == 0) {
            for (int j = 0; j <= n; j++) {
                row.emplace_back(j % money[i] == 0);
            }
        } else {
            for (int j = 0; j <= n; j++) {
                int newCase = 0;
                for (int k = j; k >= 0; k -= money[i]) {
                    newCase += cases[i - 1][k];
                }
                row.emplace_back(newCase);
            }
        }
        cases.emplace_back(row);
    }

    return cases[numMoneyUnit - 1][n] % DIVISOR;
}
```

위 코드로 화폐단위가 {2, 3, 4, 5}, 거스름돈의 크기가 10인 경우를 표로 나타내보면 다음과 같다.

|      |   0   |  1   |  2   |  3   |   4   |  5   |  6   |  7   |  8   |  9   |  10   |
| :--: | :---: | :--: | :--: | :--: | :---: | :--: | :--: | :--: | :--: | :--: | :---: |
|  2   |   1   |  0   |  1   |  0   |   1   |  0   |  1   |  0   |  1   |  0   |   1   |
|  3   | **1** |  0   |  1   |  1   | **1** |  1   |  2   |  1   |  2   |  2   |   2   |
|  4   |   1   |  0   |  1   |  1   |   2   |  1   |  3   |  2   |  4*  |  3   |   5   |
|  5   |   1   |  0   |  1   |  1   |   2   |  2   |  3   |  3   |  5   |  5   | **7** |

코드 찬찬히 읽어보면 이해되겠지만 부연설명을 좀 하겠다. 예를 하나 들어서 설명을 하자면, 3행 9열은 2원, 3원, 4원을 가지고 8원을 만드는 경우의 수를 나타낸다. 2원, 3원, 4원으로 8원을 만드는 경우의 수는 2원, 3원만으로 8원을 만드는 경우의 수 + 2원, 3원으로 4원을 만드는 경우의 수 + 2원, 3원으로 0원을 만드는 경우의 수와 같다. 따라서 2행 9열 + 2행 5열 + 2행 1열 = 4가 된다.

일단 잘 풀린다. 하지만 시간초과가 난다. 아무래도 else 절 안에서 돌아가는 저 3중 for문이 문제인 것 같다.

문제에서 n은 10만까지, 화폐단위 종류는 100까지 주어졌으므로 이중 for문이 돌 때 최대 100 * 10만 = 1000만번의 연산을 수행하게 된다. 이는 약 10초로 문제에서 설정해놓은 시간제한이 정확히 얼마인지는 모르겠지만 $O(n^2)$으로 돌려도 솔직히 간당간당한데, $O(n^3)$에 가까운 코드를 돌리고 있으니 시간초과가 나는 건 당연하다.



## Solution

그럼 그 3중 for문을 어떻게 풀어버릴 수 있을까. 난 이미 DP라는 카드를 한 번 썼다. 무슨 세계대회 문제도 아니고 DP를 두 번이나 써야 할 리 만무하고, 그럼 어떤 마술 같은 식이 for문 하나를 해체한다고 보는 게 자연스럽다.

마법을 부리기 위해서는 관찰이 선행되어야 한다. ~~괜히 25살까지 모솔이면 마법사가 되는게 아니다. 항상 짝사랑만 하면서 관찰만 잘하게 되다보니 마법을 부리게 되는 것이다.~~ 다시 3행 9열의 경우를 보자. 3중 for문의 원인이 되는 부분은 2행 5열 + 2행 1열 부분이다. 얘가 의미하는 바가 뭔지 생각을 해보자. 2원과 3원으로 4원을 만드는 경우의 수 + 2원과 3원으로 0원을 만드는 경우의 수..? 그렇다. 2원 3원 4원으로 4원을 만드는 경우의 수였던 것이다. 따라서 그 3중첩 위치의 for문은 $O(1)$의 알고리즘으로 줄어들 수 있게 된다.

```c++
#include <string>
#include <vector>
#include <algorithm>

#define DIVISOR 1000000007

using namespace std;

int solution(int n, vector<int> money) {
    vector<vector<int>> cases;
    int numMoneyUnit = money.size();
    for (int i = 0; i < numMoneyUnit; i++) {
        vector<int> row;
        if (i == 0) {
            for (int j = 0; j <= n; j++) {
                row.emplace_back(j % money[i] == 0);
            }
        } else {
            for (int j = 0; j <= n; j++) {
                int newCase = cases[i - 1][j];
                if (j >= money[i]) {
                    newCase += row[j - money[i]];
                }
                row.emplace_back(newCase % DIVISOR);
            }
        }
        cases.emplace_back(row);
    }

    return cases[numMoneyUnit - 1][n];
}
```

근데 얘 조차도 시간초과가 난다. 이제 내 힘으로 어쩔 수 없으니 구글링을 해보자. [다른 블로그](http://gurumee92.tistory.com/64)에 올라와 있는 코드를 보니 내 코드와 같다. 다른 점이라면 벡터가 아니라 배열을 썼다는 점 정도..? 내 친구가 전에 벡터는 값을 추가하는 데에 시간이 많이 걸려서 별로 좋지 않다고 한 기억이 난다. 벡터를 배열로 쭉 바꿔보자.

```c++
#include <string>
#include <vector>
#include <algorithm>

#define DIVISOR 1000000007

using namespace std;

int solution(int n, vector<int> money) {
    int numMoneyUnit = money.size();
    int cases[numMoneyUnit][n + 1];

    for (int i = 0; i <= n; i++) {
        cases[0][i] = i % money[0] == 0;
    }
    for (int i = 1; i < numMoneyUnit; i++) {
        for (int j = 0; j <= n; j++) {
            cases[i][j] = cases[i - 1][j];
            if(money[i] <= j) {
                cases[i][j] += cases[i][j - money[i]];
            }
        }
    }

    return cases[numMoneyUnit - 1][n] % DIVISOR;
}
```

미친... 통과된다.



## Retrospect

#### Knapsack

[냅색](https://enhanced.kr/postviewer/1094)문제는 풀 줄 알아도 풀기 어렵다. 일단 만나면 제끼고 다른 문제부터 풀자.

#### 벡터는 편하다. 하지만 느리다.

솔직히 벡터는 편하다. 선언할 때 크기 신경 안 써도 되고 얼마나 좋은가. 하지만 배열보다 분명히 느림이 이 문제에서 증명됐다. 가급적이면 배열을 쓰도록 하자. 벡터와 배열의 상호변경에 관한 코드는 [여기](https://enhanced.kr/postviewer/515)에서 확인할 수 있다.
knapsack 또한 LIS처럼 알고리즘 빈출유형 중의 하나로 [DP](http://enhanced.kr/postviewer/91)를 활용하는 대표적인 문제 유형이다.

| <img src="https://raw.githubusercontent.com/3jins/Images/master/must_buy_without_words.png" width="35%"/> |
| :----------------------------------------------------------: |
|            LIS와 마찬가지로 얘도 꼭 외워야 한다.             |

음.. 일단 이 유형의 문제는 x나 어렵다. 내가 웬만하면 블로그에는 비속어 안 쓰려고 하는데 고운 말 중에 이 난이도를 표현할 말이 없었다. 왜냐하면 정답코드와 해설을 보고도 처음에 전혀 이해가 안 갔기 때문이다. 코드 보다가 짜증이 나서 [종만북](http://www.kyobobook.co.kr/product/detailViewKor.laf?mallGb=KOR&ejkGb=KOR&orderClick=LEB&barcode=9788966260546)이라는, 사놓고 집에서 먼지만 쌓여가는 책의 목차를 펴봤다. 이 책은 문제의 난이도를 상중하로 구분하는데 솔직히 하 난이도도 절대 쉽지 않다. 근데, 이 책에 아주 전형적인 냅색문제가 있었고 표시된 난이도는 상이었다.

실전에서 이 문제를 만났을 때 가장 좋은 전략은 제끼고 나중에 푸는 거다. 풀 줄 알아도 헷갈리기 때문이다. 이 문제를 처음 푼 사람은 천재가 분명하다. 

그럼 잡설은 집어치우고 예제를 보자.



## Problem

>[https://www.acmicpc.net/problem/12865](https://www.acmicpc.net/problem/12865)
>
>| 시간 제한 | 메모리 제한 | 제출 | 정답 | 맞은 사람 | 정답 비율 |
>| :-------: | :---------: | :--: | :--: | :-------: | :-------: |
>|   2 초    |   512 MB    | 486  | 153  |    99     |  30.650%  |
>
>#### 문제
>
>이 문제는 아주 평범한 배낭에 관한 문제이다.
>
>한 달 후면 국가의 부름을 받게 되는 준서는 여행을 가려고 한다. 세상과의 단절을 슬퍼하며 최대한 즐기기 위한 여행이기 때문에, 가지고 다닐 배낭 또한 최대한 가치 있게 싸려고 한다.
>
>준서가 여행에 필요하다고 생각하는 N개의 물건이 있다. 각 물건은 무게 W와 가치 V를 가지는데, 해당 물건을 배낭에 넣어서 가면 준서가 V만큼 즐길 수 있다. 아직 행군을 해본 적이 없는 준서는 최대 K무게까지의 배낭만 들고 다닐 수 있다. 준서가 최대한 즐거운 여행을 하기 위해 배낭에 넣을 수 있는 물건들의 가치의 최대값을 알려주자.
>
>#### 입력
>
>첫 줄에 물품의 수 N(1 ≤ N ≤ 100)과 준서가 버틸 수 있는 무게 K(1 ≤ K ≤ 100,000)가 주어진다. 두 번째줄부터 N개의 줄에 거쳐 각 물건의 무게 W(1 ≤ W ≤ 100,000)와 해당 물건의 가치 V(0 ≤ V ≤ 1,000)가 주어진다.
>
>#### 출력
>
>한 줄에 배낭에 넣을 수 있는 물건들의 가치합의 최대값을 출력한다.
>
>#### 예제 입력 1
>
>```
>4 7
>6 13
>4 8
>3 6
>5 12
>```
>
>#### 예제 출력 1
>
>```
>14
>```



## Strategy

가장 쉽게 짜는 방법은 모든 물건마다 넣은 경우와 넣지 않은 경우의 수를 싸그리 조사해서 $O(2^n)$ 시간으로 푸는 것이다. 하지만, 알고리즘 문제에서 지수 시간복잡도는 쓰레기다. 

이 문제에 DP를 적용할 때 사용하는 기본적인 수식은 다음과 같다.

배낭에 일단 물건을 적당히 집어넣었을 때,<br/>가치총합의 최댓값 = 집어넣은 물건들의 가치총합 + 집어넣을 물건들의 가치총합

그리고 이건 배낭에 일단 물건을 집어넣었을 때의 최댓값이고, 물건을 집어넣지 않은 경우도 고려해야 한다. 그걸 iterate 시키면 된다. 자, 아이디어는 알았지만 식이 전혀 안 떠오른다. 당연하다. 이제부터 나오는 내용은 그냥 외우면 된다.

문제에서 준 예제를 가지고 생각해보자. 배낭은 최대 7kg의 무게를 견딜 수 있고, 배낭에 집어넣고 싶은 물건들은 다음과 같다.

| 물건1 | 물건2 | 물건3 | 물건4 |
| :---: | :---: | :---: | :---: |
|  13   |   8   |   0   |   6   |
|  6kg  |  4kg  |  1kg  |  3kg  |

이로부터 dp를 적용하기 위한 2차원 벡터 또는 행렬을 하나 만든다. 이름은 `valueSums`라 하자. `valueSums`의 열의 크기는 가방이 최대로 담을 수 있는 무게 + 1과 같다.

<img src="https://raw.githubusercontent.com/3jins/Images/master/knapsack_phase_1.png" width="90%"/>

이제 물건의 종류 수 만큼 iteration을 돌면서 이 벡터의 다음 행을 업데이트한다. 현재 iteration이 `i`행, `w`열이라 하고, 현재 물건의 무게를 `weight`, 현재 물건의 가치를 `value`라 할 때, 업데이트 규칙은 다음과 같다.

1. 'weight > `w`'인 경우, `valueSums[i - 1][w]`로 업데이트한다(윗 행 같은 열의 값을 그대로 가져온다).
2. 아닌 경우, `valueSums[i - 1][w]`와 `valueSums[i - 1][w - weight] + value` 중 더 가치가 큰 쪽으로 업데이트한다.

이 과정을 반복하는 것을 그림으로 나타내면 다음과 같다.

<img src="https://raw.githubusercontent.com/3jins/Images/master/knapsack_phase_2.png" width="90%"/>

<img src="https://raw.githubusercontent.com/3jins/Images/master/knapsack_phase_3.png" width="90%"/>

<img src="https://raw.githubusercontent.com/3jins/Images/master/knapsack_phase_4.png" width="90%"/>

<img src="https://raw.githubusercontent.com/3jins/Images/master/knapsack_phase_5.png" width="90%"/>

이렇게 되는 정확한 이유는 이해가 갈듯 말듯하다. 언젠가 [깨달음이 올 것](https://ko.wiktionary.org/wiki/%EB%8F%88%EC%98%A4)이라 생각하며, 일단은 외워서 풀도록 하자.



## Solution

```c++
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

int optimizeKnapsack(int maxWeight, vector<vector<int>> items) {
    int numItems = items.size() - 1;	// Tips 1
    vector<vector<int>> valueSums;
    valueSums.emplace_back(vector<int>(maxWeight + 1, 0)); // initialize

    for (int i = 1; i <= numItems; i++) {	// Tips 1
        vector<int> row;
        for (int w = 0; w <= maxWeight; w++) {	// Tips 2
            if (items[i][0] > w)
                row.emplace_back(valueSums[i - 1][w]);
            else
                row.emplace_back(max(valueSums[i - 1][w], valueSums[i - 1][w - items[i][0]] + items[i][1]));
        }
        valueSums.emplace_back(row);
    }

    int maxValue = 0;
    for (int i = 0; i <= numItems; i++) {
        int rowMax = *max_element(valueSums[i].begin(), valueSums[i].end());
        if (rowMax > maxValue) maxValue = rowMax;
    }

    return maxValue;
}

int main() {
    int numItems, maxWeight;
    vector<vector<int>> items = {{0, 0}};	// Tips 1

    cin >> numItems >> maxWeight;
    for (int i = 0; i < numItems; i++) {
        int weight, value;
        cin >> weight >> value;
        items.emplace_back(vector<int>({weight, value}));
    }
    cout << optimizeKnapsack(maxWeight, items) << endl;
    return 0;
}
```



## Tips

문제풀이의 핵심과 살짝 벗어난 부분들이 있는데, 이 부분을 정해두지 않으면 버그를 잡는데 시간이 많이 걸린다.  꼭 다음의 팁을 따를 필요는 없지만, 자신만의 방식을 정해놓고 그대로 숙달할 필요가 있다. 이 팁은 예외처리 코드를 최대한 피해가기 위한 팁이다.

#### 1. 물건의 갯수에 관한 배열/벡터는 인덱스를 1부터 시작해라

첫 행에다가 0으로 다 채워놓자. 안 그러면 예외처리 해야 할 부분이 엄청나게 늘어난다.

#### 2. weight의 이터레이션은 0부터 `maxWeight`까지를 돌아야 한다 

배낭에 아무것도 담지 않는 경우를 같이 고려해주지 않으면 코드가 복잡해진다.
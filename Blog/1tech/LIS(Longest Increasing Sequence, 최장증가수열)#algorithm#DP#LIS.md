LIS는 알고리즘 빈출유형 중의 하나로 [DP](http://enhanced.kr/postviewer/91)를 활용하는 대표적인 문제 유형이다. 

| <img src="https://raw.githubusercontent.com/3jins/Images/master/must_buy_without_words.png" width="35%"/> |
| :----------------------------------------------------------: |
|        이런 건 꼭 외워야 한다. 안 그러면 못 푸니까...        |

예제를 보자.

>주어진 정수 배열에서 순서를 깨뜨리지 않고 가장 긴 증가수열을 만들어 출력하라.
>
>예시)
>
>입력: 10, 20, 40, 25, 20, 50, 30, 70, 85
>
>출력: 10, 20, 40, 50, 70, 85

그냥 아무 생각 없이 $O(n^2)$으로 짜면 굉장히 간단하다. 하지만 그럴 생각이라면 우린 DP를 공부하지 않았을 것이다. LIS의 경우는 $O(nlogn)$의 시간으로 해결이 가능하다.





## 전략

DP 문제 해결의 핵심은 과거의 최선과 현재를 비교하여 최선을 갱신하는 것이다.  

1. LIS를 저장할 벡터 `LIS`를 하나 만들고 첫번째 값으로 주어진 수열의 0번 값을 넣는다.
2. 주어진 수열의 i번 값(이하 `seq[i]`)과 `LIS`의 끝 값(이하 `LIS.back()`)을 비교하여 `seq[i]`가 더 크다면 `LIS`의 끝에 추가한다.
3. `seq[i]`가 더 작다면 `LIS`의 중간에 `seq[i]`가 들어갈 자리를 찾아 갈아치워 `LIS` 배열이 최선의 상태를 유지하게 한다.

여기서 '최선'이라 함은 `LIS` 배열의 각 값을 최대한 작은 값으로 유지하여 `seq` 배열에서 나중에 탐색되는 값들을 최대한 놓치지 않고 다 `LIS` 배열에 꽉꽉 채워담는 것을 의미한다.

얼핏 생각하면 이 전략은 여전히 $O(n^2)$의 시간이 걸릴 것만 같다. 하지만 3번 항목에서 C++의 비밀병기~~는 아니고 사실 나 빼고 다 아는거 같은~~ `lower_bound`가 등장한다면 얘기가 달라진다. 말은 거창하게 했지만 사실 뭐 별건 아니고 주어진 값이 주어진 벡터에서 [**이진탐색**](https://www.youtube.com/watch?v=PvxRdiZJX0g)을 이용해 자신이 들어가야 하는 위치를 찾아주는 함수다. 예를 들어, [1, 3, 6, 8, 9]와 같이 오름차순으로 정렬된 배열에서 7에 대하여 `lower_bound`를 쓰게 되면 현재 8이 차지하고 있는 메모리주소를 반환한다. 얘 덕택에 시간복잡도가 $O(n \log n)$으로 줄어드는 것이다.

#### Window

다른 문제에도 이 아이디어를 응용할 수 있게 하기 위해 'window'라는 개념을 가지고 다시 설명하겠다.

<img src="https://raw.githubusercontent.com/3jins/Images/master/LIS_window.png" width="100%"/>

**주어진 규칙**(여기서는 증가수열)이 적용되는 구간을 윈도우로 묶고 한 칸씩 윈도우를 늘려가며 확인한다. 더 늘릴 수 없는 경우 '최선'이었던 파란색 윈도우와 크기를 비교해 어느 쪽이 더 최선인지 확인한다. 

여기서 **주어진 규칙**이 달라진다 해도 이 아이디어를 똑같이 적용할 수 있다. 예를 들면 [매일프로그래밍 10주차 문제](https://enhanced.kr/postviewer/504)처럼.



## 코드

```c++
#include <iostream>
#include <vector>

using namespace std;

/* 그냥 출력함수니 무시하도록 하자. */
void printVector(vector<int> seq) {
    int lenSeq = seq.size();
    for (int i = 0; i < lenSeq; i++) {
        cout << seq[i] << " ";
    }
    cout << endl;
}

void printLIS(vector<int> seq) {
    vector<int> LIS = {seq[0]};
    int lenSeq = seq.size();
    int lenLIS = LIS.size();	// 과거의 최선

    for (int i = 1; i < lenSeq; i++) {
        if (seq[i] > LIS.back()) {	// 현재 vs 과거의 최선
            LIS.push_back(seq[i]);
            lenLIS++;
        }
        else {
            vector<int>::iterator iterator = lower_bound(LIS.begin(), LIS.end(), seq[i]);
            *iterator = seq[i];
        }
    }
    printVector(LIS);
}
```


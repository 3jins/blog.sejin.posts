문제 자체는 아주 쉽다. 하지만 강력한 함정이 있으므로 간단히 정리하겠다.



## Problem

[https://www.acmicpc.net/problem/3190](https://www.acmicpc.net/problem/3190)

> | 시간 제한 | 메모리 제한 | 제출  | 정답 | 맞은 사람 | 정답 비율 |
> | :-------: | :---------: | :---: | :--: | :-------: | :-------: |
> |   2 초    |   512 MB    | 17722 | 4482 |   3274    |  23.914%  |
>
> ## 문제
>
> 총 N개의 시험장이 있고, 각각의 시험장마다 응시자들이 있다. i번 시험장에 있는 응시자의 수는 Ai명이다.
>
> 감독관은 총감독관과 부감독관으로 두 종류가 있다. 총감독관은 한 방에서 감시할 수 있는 응시자의 수가 B명이고, 부감독관은 한 방에서 감시할 수 있는 응시자의 수가 C명이다.
>
> 각각의 시험장에 총감독관은 오직 1명만 있어야 하고, 부감독관은 여러 명 있어도 된다.
>
> 각 시험장마다 응시생들을 모두 감시해야 한다. 이 때, 필요한 감독관 수의 최소값을 구하는 프로그램을 작성하시오.
>
> ## 입력
>
> 첫째 줄에 시험장의 개수 N(1 ≤ N ≤ 1,000,000)이 주어진다.
>
> 둘째 줄에는 각 시험장에 있는 응시자의 수 Ai (1 ≤ Ai ≤ 1,000,000)가 주어진다.
>
> 셋째 줄에는 B와 C가 주어진다. (1 ≤ B, C ≤ 1,000,000)
>
> ## 출력
>
> 각 시험장마다 응시생을 모두 감독하기 위해 필요한 감독관의 최소 수를 출력한다.
>
> ## 예제 입력 1
>
> ```
> 1
> 1
> 1 1
> ```
>
> ## 예제 출력 1
>
> ```
> 1
> ```
>
> ## 예제 입력 2
>
> ```
> 3
> 3 4 5
> 2 2
> ```
>
> ## 예제 출력 2
>
> ```
> 7
> ```
>
> ## 예제 입력 3
>
> ```
> 5
> 1000000 1000000 1000000 1000000 1000000
> 5 7
> ```
>
> ## 예제 출력 3
>
> ```
> 714290
> ```
>
> ## 예제 입력 4
>
> ```
> 5
> 10 9 10 9 10
> 7 20
> ```
>
> ## 예제 출력 4
>
> ```
> 10
> ```
>
> ## 예제 입력 5
>
> ```
> 5
> 10 9 10 9 10
> 7 2
> ```
>
> ## 예제 출력 5
>
> ```
> 13
> ```



## Solution

```c++
#include <iostream>
#include <vector>

using namespace std;

long long solution(vector<int> listNumTestTakers, int chiefCapability, int viceCapability) {
    long long count = 0;
    for(int numTestTaker : listNumTestTakers) {
        numTestTaker -= chiefCapability;
        count++;
        if(numTestTaker <= 0) continue;
        count += ((numTestTaker - 1) / viceCapability) + 1;
    }
    return count;
}

int main() {
    int numRooms;
    vector<int> listNumTestTakers;
    int chiefCapability;
    int viceCapability;

    cin >> numRooms;
    for(int i=0; i<numRooms; i++) {
        int numTestTaker;
        cin >> numTestTaker;
        listNumTestTakers.push_back(numTestTaker);
    }
    cin >> chiefCapability >> viceCapability;

    cout << solution(listNumTestTakers, chiefCapability, viceCapability) << endl;
    
    return 0;
}
```

얼마나 쉬운지 코드조차 짧다. 심지어 입력 받는 코드가 풀이 코드보다 길다.



## Retrospect

#### hidden long long

큰 수를 다룰 때는 `int` 대신 `long long`을 써야 한다는 것은 유명하다. 하지만 큰 수가 숨겨져 있다면 이를 발견하기 어렵다. 여기서 숨겨져 있다는 말은, 입력값에는 큰 수가 없지만, 극단적인 경우를 입력으로 넣었을 때, 중간 계산 과정에서 큰 수가 뿅하고 나타나는 상황을 말한다. 이 경우 자료형을 `long long`으로 바꿔줘야 할 수 있다.

보통 이런 경우 채점 결과가 '틀렸습니다'가 나온다.

#### nk + 1 ~ nk + n

어떤 숫자를 n으로 나눴을 때의 몫을 기준으로 숫자를 분리하면 nk ~ nk + n - 1을 하나의 범위로 잡게 된다. 하지만 현실세계에서 숫자는 보통 0이 아니라 1에서 시작하기 때문에 nk + 1 ~ nk + n을 하나의 범위로 잡는 게 더 편할 때가 많다. 이를 코드로 짜려면 은근히 헷갈리는데 그냥 외워버리면 편할 것 같다.

`int seq = ((num - 1) / divisor) + 1;`

`divisor`가 7이라면 `num`이 1~7일 때 `seq`은 1, `num`이 8~14일 때 `seq`은 2가 되는 식이다.

#### threshold로 예외처리: == vs <=

딱 그 경우만 예외처리해야 되는 경우(==)가 있고, 그 경우부터 전부 예외처리 해야 하는 경우(<=, >=)가 있다. 이를 잘 구별하도록 하자.


드디어 Level 2의 마지막 문제다. 사실 별거 아닌 수학문제지만 최소공배수에 대한 개념이 잘못 잡혀있어서 한참을 헤맸다. 중1 때 배우는 이런 기본적인 수학문제 가지고 한참을 고민하다니 나도 늙어서 머리가 굳고 있나보다.



## Problem

[https://programmers.co.kr/learn/courses/30/lessons/12913](https://programmers.co.kr/learn/courses/30/lessons/12953)

> 두 수의 최소공배수(Least Common Multiple)란 입력된 두 수의 배수 중 공통이 되는 가장 작은 숫자를 의미합니다. 예를 들어 2와 7의 최소공배수는 14가 됩니다. 정의를 확장해서, n개의 수의 최소공배수는 n 개의 수들의 배수 중 공통이 되는 가장 작은 숫자가 됩니다. n개의 숫자를 담은 배열 arr이 입력되었을 때 이 수들의 최소공배수를 반환하는 함수, solution을 완성해 주세요.
>
> ##### 제한 사항
>
> - arr은 길이 1이상, 15이하인 배열입니다.
> - arr의 원소는 100 이하인 자연수입니다.
>
> ##### 입출력 예
>
> |    arr     | result |
> | :--------: | :----: |
> | [2,6,8,14] |  168   |
> |  [1,2,3]   |   6    |



## Trial

두 수 $A$, $B$에 대해서 $G = \gcd(A, B)$, $A = Ga,$ $B = Gb$라 할 때, $lcm(A, B) = Gab$ 이다. 

그래서 난 한치의 의심도 없이 $lcm(A, B, C) = Gabc$일 것이라 생각했다.

```c++
#include <string>
#include <vector>
#include <algorithm>

using namespace std;

vector<int> factorize(int target) {
    vector<int> primes;
    int limit = ceil(sqrt(target));
    for (int i = 2; i <= limit; i++) {
        while (target % i == 0) {
            target /= i;
            primes.emplace_back(i);
        }
    }
    return primes;
}

int getGCD(vector<int> arr) {
    int gcd = 1;
    int arrSize = arr.size();
    int smallest = *min_element(arr.begin(), arr.end());
    vector<int> primes = factorize(smallest);
    vector<int>::iterator iter;
    for (iter = primes.begin(); iter != primes.end(); iter++) {
        int prime = *iter;
        int count = 0;
        for (int i = 0; i < arrSize; i++) {
            if (arr[i] % prime != 0) break;
            arr[i] /= prime;
            count++;
        }
        if(count < arrSize) continue;
        gcd *= prime;
    }
    return gcd;
}

int solution(vector<int> arr) {
    int gcd = getGCD(arr);
    int lcm = gcd;
    int arrSize = arr.size();
    for (int i = 0; i < arrSize; i++) {
        lcm *= arr[i] / gcd;
    }
    return lcm;
}
```

테스트케이스는 모두 잘 통과했다. 하지만 채점결과는...

<img src="https://raw.githubusercontent.com/42deSix/Images/master/lcm_all_fail.png" width="80%"/>



## Solution 

사람들이 어떻게 풀었는지 찾아봤다. 한결같이 $lcm(A, B, C) = lcm(lcm(A, B), C)$임을 이용하고 있었다.

```c++
#include <string>
#include <vector>
#include <algorithm>

using namespace std;

vector<int> factorize(int target) {
    vector<int> primes;
    int limit = ceil(sqrt(target));
    for (int i = 2; i <= limit; i++) {
        while (target % i == 0) {
            target /= i;
            primes.emplace_back(i);
        }
    }
    return primes;
}

int getGCD(int a, int b, bool useEuclidean = true) {
    int gcd = 1;
    if(useEuclidean) {	// 유클리드 호제법 이용
        while(a != b) {
            if(a > b) a = a - b;
            else b = b - a;
        }
        gcd = a;
    } else {	// 소인수분해 이용
        vector<int> primes = factorize(a);
        vector<int>::iterator iter;
        for (iter = primes.begin(); iter < primes.end(); iter++) {
            int prime = *iter;
            if (b % prime == 0) {
                b /= prime;
                gcd *= prime;
            }
        }
    }
    return gcd;
}

int getLCM(int a, int b) {
    int gcd = getGCD(a, b);
    return a * b / gcd;
}

int getLCM(vector<int> arr) {
    int lcm = arr[0];
    int arrSize = arr.size();
    for (int i = 1; i < arrSize; i++) {
        lcm = getLCM(lcm, arr[i]);
    }
    return lcm;
}

int solution(vector<int> arr) {
    int lcm = getLCM(arr);
    return lcm;
}

```





## Retrospect

$lcm(A, B, C)$는 $Gabc$와 같지 않았다. 또한, 사람들은 최대공약수를 구할 때 소인수분해 따위는 하지 않고, 계속 두 수를 빼서 최대공약수를 구했다. 아니, 알고리즘을 공부하려고 했더니 정수론이 튀어나온다. 도대체 이딴 문제를 왜 섞어놨는지 모르겠다. [오일러 프로젝트](http://euler.synap.co.kr/) 같은 데에나 박아두고 취준생들 좀 안 괴롭혔으면 좋겠다. ㅠㅠ

#### 쌍마다 서로소와 서로소는 다르다

자, 천천히 생각해보도록 하자.

세 수 $A$, $B$, $C$에 대해서 <br/>
$G = \gcd(A, B, C)$, <br/>
$A = Ga,$ $B = Gb,$ $C = Gc$ 일 때,<br/>
$lcm(A, B, C) = lcm(lcm(A, B), C) = lcm(Gab, Gc)$이다.<br/>
이 때 과연, $gcd(Gab, Gc) = G$가 맞을까?

$a = 2\times3, b = 3\times5, c = 5\times2$라 한다면, $\gcd(a, b, c) = 1$이지만, $\gcd(ab, c) = 10$이 된다.<br/>
즉, 세 수가 서로소 관계라고 해서 그 중 두 수를 뽑았을 때 그 둘도 서로소라는 보장은 없다. <br/>
따라서 $lcm(A, B, C) = lcm(Gab, Gc) \neq Gabc$일 수 있다.

#### 유클리드 호제법

사용방법은 코드에 있고, 증명은 [여기](https://enhanced.kr/postviewer/1014)에 있다. 
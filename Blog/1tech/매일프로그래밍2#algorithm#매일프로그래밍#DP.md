개강 준비 때문에 이래저래 바빴기 때문에 매일프로그래밍 문제가 지금 4주치나 쌓여있다. 빨리 몰아서 풀어버리도록 하자.



## Problem

> 피보나치 배열은 0과 1로 시작하며, 다음 피보나치 수는 바로 앞의 두 피보나치 수의 합이 된다. 정수 N이 주어지면, N보다 작은 모든 짝수 피보나치 수의 합을 구하여라.
>
> 예제)
>
> Input: N = 12
>
> Output: 10 // 0, 1, 2, 3, 5, 8 중 짝수인 2 + 8 = 10.



## Trial

2주차 문제는 1주차에 비해서 훨씬 쉬운 느낌이다. 그냥 DP를 아느냐 정도만 묻는 느낌.

피보나치 수열은 앞 두 항을 더해서 다음 항을 만든다. 초등학교 교육만 잘 받았으면 홀수와 짝수를 어떻게 조합해서 더하느냐에 따라 홀짝성이 달라진다는 사실은 잘 알 것이고 이로부터 피보나치 수열은 3의 배수번째 항에서만 짝수가 나타난다는 사실을 쉽게 유추할 수 있다.

그럼 이제 주어진 숫자보다 작은 범위의 피보나치 배열만 구해서 3으로 나눈 나머지가 2인 항(인덱스가 0에서 시작하기 때문)들만 골라 더해주면 문제가 끝난다.

```c++
#include <iostream>
#include <vector>

using namespace std;

/* Get integer vector that only contains fibonacci values smaller than N */
vector<int> getNeededFibonacciVector(int N) {
    vector<int> fibonacci = {1, 1}; // It doesn't matter if first element is 0 or 1.
    int i = 1;
    while (fibonacci[i] < N) {
        fibonacci.push_back(fibonacci[i] + fibonacci[i - 1]);
        i++;
    }
    fibonacci.pop_back();
    return fibonacci;
}

/* Add 3k+2 elements */
int getSumOfEvenFibonacci(int N) {
    vector<int> fibonacci = getNeededFibonacciVector(N);
    int idx = fibonacci.size();
    int sum = 0;
    for (int i = 2; i < idx; i += 3) {
        sum += fibonacci[i];
    }

    return sum;
}
```

테스트케이스 여러개 넣어봤을 때 잘 풀리는 것 같으니 이제 솔루션을 보자.



## Solution

```c++
int evenFibSum(int N) {
  int sum = 0;
  int x = 1;
  int y = 2;
  while (x <= N) {
    if (x % 2 == 0) {
      sum += x;
    }
    int z = x + y;
    x = y;
    y = z;
  }
  return sum;
}
```

x라 간단하다...



## Retrospect

모든 일은 **하는 김에 한 번에 관련된 일들을 끝내버리는 게 효율적**이다. 아침에 일어나서 똥싸러 간 김에 샤워까지 하면 시간이 절약되는 것과 마찬가지 이치다. 이 문제 역시 마찬가지로 굳이 피보나치 구하는 로직과 짝수인 항을 추출하는 로직을 분리할 필요 없이 피보나치 n항 구하는 김에 홀수인 항 거르고 짝수인 항만 다 더하면 아주 간단히 풀린다.


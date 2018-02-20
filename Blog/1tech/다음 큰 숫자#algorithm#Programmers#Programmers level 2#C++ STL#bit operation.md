너무 더워서 그런지 낮에 코딩이 안되길래 밤낮을 바꿔보았다. 밤에 코딩하니까 집중력이 아주 높다. 게다가 제 때 자야 한다는 압박감이 없어서 카페인 걱정 없이 맘대로 커피도 마실 수 있다. 하지만 이번 주말에 고등학교 친구들을 만나야 되고, 또 늦게 군대 간 친구 면회도 가야돼서 밤낮을 다시 돌려놔야 한다. 서론을 좀 써야 글의 프리뷰가 이쁘게 나오기 때문에 아무 소리나 막 써봤다. 그럼 본격적으로 문제를 풀어보자.



## Problem

[https://programmers.co.kr/learn/courses/30/lessons/12911](https://programmers.co.kr/learn/courses/30/lessons/12911)

> 자연수 n이 주어졌을 때, n의 다음 큰 숫자는 다음과 같이 정의 합니다.
>
> - 조건 1. n의 다음 큰 숫자는 n보다 큰 자연수 입니다.
> - 조건 2. n의 다음 큰 숫자와 n은 2진수로 변환했을 때 1의 갯수가 같습니다.
> - 조건 3. n의 다음 큰 숫자는 조건 1, 2를 만족하는 수 중 가장 작은 수 입니다.
>
> 예를 들어서 78(1001110)의 다음 큰 숫자는 83(1010011)입니다.
>
> 자연수 n이 매개변수로 주어질 때, n의 다음 큰 숫자를 return 하는 solution 함수를 완성해주세요.
>
> ##### 제한 사항
>
> - n은 1,000,000 이하의 자연수 입니다.
>
> ------
>
> ##### 입출력 예
>
> | n    | result |
> | ---- | ------ |
> | 78   | 83     |
> | 15   | 23     |
>
> ##### 입출력 예 설명
>
> 입출력 예#1
> 문제 예시와 같습니다.
> 입출력 예#2
> 15(1111)의 다음 큰 숫자는 23(10111)입니다.



## Trial

2진수로 바꿔놓고 생각해 봤다. 예를 들어, 1001110의 다음 큰 숫자를 구한다고 생각해보자. 1001110과 다른 숫자면서 1과 0의 갯수가 같아야 하므로 최소한 하나의 0은 1로 바뀌어야 하고, 하나의 1은 0으로 바뀌어야 한다. 1001110보다 큰 숫자로 바뀌어야 하기 때문에 1로 바뀌어야 하는 0 중 적어도 하나는 맨 끝에 있는 1(10011<u>**1**</u>0)보다 왼쪽에 있는 0(10<u>**0**</u>1110)이어야 한다. 

이제 저 0을 기준으로 숫자를 쪼갤 수 있다(100, 1110). 100이 101로 바뀔 것이므로, 1110에서 1의 갯수가 하나 줄어들고 0이 하나 늘어나야 한다. 그러면서 최대한 작은 값을 유지하면 되므로 1110은 0011로 바뀌면 된다.

이 로직에 의해 1001110의 다음 큰 숫자는 1010011이 된다. 이를 코드로 옮기면 다음과 같다.

```c++
#include <string>
#include <vector>

using namespace std;

string decaToBi(int deca) {
  string bi = "";
  while (deca > 0) {
    bi = (deca % 2 ? '1' : '0') + bi;
    deca /= 2;
  }
  return bi;
}

int biToDeca(string bi) {
  int biLen = bi.length();
  int multiplier = 1;
  int deca = 0;
  while (biLen > 0) {
//  deca += (bi[biLen - 1] - '0') * multiplier;
//  biLen--;
    deca += (bi[--biLen] - '0') * multiplier;
    multiplier *= 2;
  }
  return deca;
}

pair<int, int> binaryDigitCounter(string bi) {
  string::iterator iter;
  int numZeros = 0;
  int numOnes = 0;
  for (iter = bi.begin(); iter != bi.end(); iter++) {
    if (*iter == '1') numOnes++;
    else numZeros++;
  }
  return pair<int, int>(numZeros, numOnes);
}

string makeSmallestBinaryByNumDigits(pair<int, int> numDigits) {
  int numZeros = numDigits.first;
  int numOnes = numDigits.second;
  string smallestBinary = "";
  for (int i = 0; i < numZeros; i++) {
    smallestBinary += '0';
  }
  for (int i = 0; i < numOnes; i++) {
    smallestBinary += '1';
  }
  return smallestBinary;
}

int solution(int n) {
  string bi = decaToBi(n);	// log n
  int lastOneIdx = bi.find_last_of("1");
  int thresholdIdx = bi.rfind("0", lastOneIdx);	// log n
  string biggerPart;
  string smallerPart;
  if (thresholdIdx < 0) {
    biggerPart = "1";
    smallerPart = bi;
  } else {
    bi.replace(thresholdIdx, 1, "1");
    biggerPart = bi.substr(0, thresholdIdx + 1);
    smallerPart = bi.substr(thresholdIdx + 1);
  }
  pair<int, int> numDigits = binaryDigitCounter(smallerPart);	// log n
  smallerPart = makeSmallestBinaryByNumDigits(pair<int, int>(numDigits.first + 1, numDigits.second - 1));	// log n
//bi = biggerPart + smallerPart;
//return biToDeca(bi);
  return biToDeca(biggerPart + smallerPart);	// log n
}
```

주석처리 된 부분은 처음에 짠 코드다. 그 코드로 효율성 테스트 6개 중 5개를 통과하고 1개에 실패했다. 

입력이 최대 100만이므로 C++ 기준으로 루프 한 바퀴 돌리는데 최대 1초가 걸린다. 그런데 위 코드는 $O(\log n)$ 시간이 소요된다. 대강 계산해 봤을 때 $5 \times \log n$ 정도의 시간이 걸린다. 워스트케이스(100만 근처의 숫자 입력시)가 루프 100번 정도 도는 수준이다. 도대체 시간제한을 얼마로 걸어놨길래 이게 통과가 안되는걸까.

실제로 코드를 돌려보면 100만이나 999999 같은 값을 넣어도 순식간에 결과가 나온다. 조금만 수정하면 통과가 될 것 같아 몇 군데에서 불필요한 연산 수를 조금 줄였더니 통과가 되었다. 주석 바로 아래의 코드는 수정된 코드다.



## Solution 

$5 \times \log n$의 시간을 $2 \times \log n$으로 줄일 수 있더라. 다만, 이 방법은 이진수 한정으로 적용 가능하다.

```c++
#include <string>
#include <vector>

using namespace std;

int countOnes(int n) {
    int cnt = 0;
    while(n > 0) {
        if(1 & n) cnt++;
        n = n >> 1;
    }
    return cnt;
}

int solution(int n) {
    int numOnes = countOnes(n);
    while(true) {
        if(numOnes == countOnes(++n)) return n;
    }
}
```

미친거 아니냐... 알고리즘 문제 푸는데 비트연산이라니 아름답잖아..!



## Retrospect

두 가지 방법으로 해결 가능한 문제였다. 

#### 문자열 함수

내가 사용한 방법은 문자열 파싱이 상당히 많이 필요하다. 근데 겁나 헷갈리더라. 그냥 코딩하는 것 같으면 문서 열어놓고 찾아가면서 쓰면 되는데, 취준생 입장에서 알고리즘을 풀 때는 결국 시간 내에 코드를 완성해야 하므로 좀 외우고 다니든가 아니면 최소한 잘 정리된 모음집이라도 있어야 할 것 같다. [그래서 만들어 보았다](https://enhanced.kr/postviewer/806).

#### 비트연산

2진수 문제는 컴퓨터가 2진수를 쓰기 때문에 굳이 2진수로 바꿀 필요가 없다. 비트연산자를 써서 간단히 해결할 수 있으니 이를 염두에 두고 풀도록 하자.
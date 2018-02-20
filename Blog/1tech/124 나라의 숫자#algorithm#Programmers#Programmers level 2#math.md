이 문제는 나랑 상성이 너무 안 맞는다. 이런 문제를 실전이 아니라 연습에서 만나서 참 다행이다.



## Problem

[https://programmers.co.kr/learn/courses/30/lessons/12899](https://programmers.co.kr/learn/courses/30/lessons/12899)

> 124 나라가 있습니다. 124 나라에서는 10진법이 아닌 다음과 같은 자신들만의 규칙으로 수를 표현합니다.
>
> 1. 124 나라에는 자연수만 존재합니다.
> 2. 124 나라에는 모든 수를 표현할 때 1, 2, 4만 사용합니다.
>
> 예를 들어서 124 나라에서 사용하는 숫자는 다음과 같이 변환됩니다.
>
> | 10진법 | 124 나라 | 10진법 | 124 나라 |
> | :------: | :--------: | :------: | :--------: |
> | 1      | 1        | 6      | 14       |
> | 2      | 2        | 7      | 21       |
> | 3      | 4        | 8      | 22       |
> | 4      | 11       | 9      | 24       |
> | 5      | 12       | 10     | 41       |
>
> 자연수 n이 매개변수로 주어질 때, n을 124 나라에서 사용하는 숫자로 바꾼 값을 return 하도록 solution 함수를 완성해 주세요.
>
> ##### 제한사항
>
> - n은 500,000,000이하의 자연수 입니다.
>
> ##### 입출력 예
>
> | n    | result |
> | :----: | :------: |
> | 1    | 1      |
> | 2    | 2      |
> | 3    | 4      |
> | 4    | 11     |



## Trial

이런 식으로 풀면 일단 모든 채점용 케이스가 통과는 된다.

1. 124숫자 대신 123숫자를 먼저 만든 뒤 마지막에 124 숫자로 바꾸기로 한다.
2. 주어진 10진수를 3진수로 변환한다.
3. $3^0$의 자릿수부터 차례로 하나씩 확인을 해본다. 
   * 0이 아니라면 그대로 놔두면 된다.
   * 123숫자에는 0이 없기 때문에 윗자릿수에서 3을 빌려오고 윗자릿수를 1 빼줘야 한다. 이는 별도의 함수를 만들어 재귀적으로 처리한다.

```c++
#include <iostream>
#include <string>
#include <vector>
#include <algorithm>

using namespace std;

string tri = "";

string decaToTri(int deca) {
    string tri = "";
    while(deca > 0) {
        tri = to_string(deca % 3) + tri;
        deca /= 3;
    }
    return tri;
}

void lend(int curIdx) {
    if(tri[curIdx - 1] == '0') lend(curIdx - 1);
    tri[curIdx - 1] = tri[curIdx - 1] - 1;
    tri[curIdx] = '3';
}

string solution(int n) {
    tri = decaToTri(n);
    int triLen = tri.length();

    for(int i=triLen - 1; i>0; i--) {
        if(tri[i] != '0') continue;
        lend(i);
    }

    replace(tri.begin(), tri.end(), '3', '4');
    if(tri[0] == '0') tri = tri.substr(1, tri.length() - 1);
    cout << tri << endl;
    return tri;
}
```

input data가 최대 5억이지만 실제로 쓰는건 최대 9자리의 문자열이다. 3진수로 바꾼다 해봤자 $3^{18}$이면 3억이 넘어가기 때문에 20자리를 넘지 않는 문자열이다. 게다가 위의 알고리즘은 $O(n)$의 시간복잡도를 갖는다. 

그럼에도 불구하고 이 알고리즘은 시간제한을 통과하지 못한다. 얼탱이가 없다.



## Solution

```c++
#include <iostream>
#include <string>
#include <vector>

using namespace std;

string solution(int no) {
    string answer = "";
    char digits[3] = {'4', '1', '2'};
    int remainder;
    while (no > 0) {
        remainder = no % 3;
        no /= 3;
        if (remainder == 0) 
          no--;
        answer = digits[remainder] + answer;
    }
    return answer;
}
```

 

## Retrospect

아는 형이 알고리즘에서 수학문제는 그냥 닥치고 외우는 거라고 했다. 이제 그 말이 무슨 뜻인지 좀 알 것 같다.

#### 0이 없는 진법

문제 보고 그냥 딱 떠오르면 다행이지만, 이번처럼 안 풀리는 문제라면 뭐 답이 없다. 그냥 외우도록 하자.

#### ㅌㅌㅌ

문제 다 읽고 나서 5분 이상 고민했지만 코드 로직이 안 떠오르면 못 푸는 문제다. 실전에서 이런 문제를 만나면 일단 넘기도록 하자. 

이 때 최악의 경우는 뭔가를 잘못 생각해서 잘못된 코드를 한참 짜다가 실수했음을 발견하는 경우다. 이 때는 어차피 그 시험은 망한거니까 그냥 즐겜 마인드로 다른 문제 구경하면서 천천히 풀자.


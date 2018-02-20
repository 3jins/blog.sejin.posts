이런 문제가 [전](https://enhanced.kr/postviewer/756)에 말했던 믹스보이스에 해당하는 문제 같다. 별 어려운 개념이 쓰이지 않는다. 그냥 반복으로 풀면 된다. 근데 이상하게 쉽지 않다.



## Problem

[https://programmers.co.kr/learn/courses/30/lessons/12904](https://programmers.co.kr/learn/courses/30/lessons/12904)

> 앞뒤를 뒤집어도 똑같은 문자열을 팰린드롬(palindrome)이라고 합니다.
> 문자열 s가 주어질 때, s의 부분문자열(Substring)중 가장 긴 팰린드롬의 길이를 return 하는 solution 함수를 완성해 주세요.
>
> 예를들면, 문자열 s가 abcdcba이면 7을 return하고 abacde이면 3을 return합니다.
>
> ##### 제한사항
>
> - 문자열 s의 길이 : 2500 이하의 자연수
> - 문자열 s는 알파벳 소문자로만 구성
>
> ------
>
> ##### 입출력 예
>
> |    s    | answer |
> | :-----: | :----: |
> | abcdcba |   7    |
> | abacde  |   3    |



## Trial

이 문제가 LIS의 변형 같은거라고 생각하는 순간 안 풀린다. 그 딴 거 없다. 이 문제는 $O(n^2)$의 시간복잡도면 충분하다. 왜냐하면 입력의 크기가 2500 이하의 자연수기 때문이다.

$n = 2500$이라면 $n^2 = 6,250,000$이다. 즉, 백만 단위다. 시간제한이 1초라면 대충 $O(n^2) / 6$의 시간 내로 풀면 된다. DP 쓰는 문제라면 대놓고 $n^2$이 1억 넘어가게 줬을 것이다.

처음 코드는 다음과 같았다.

```c++
#include <iostream>
#include <string>

using namespace std;

string str = "";

bool isPalindrome(int head, int tail) {
    int lastIdx = (tail - head) / 2;
    for (int i = 0; i <= lastIdx; i++) {
        if (str[head + i] != str[tail - i])
            return false;
    }
    return true;
}

int solution(string s) {
    str = s;
    int strLen = str.size();
    int answer = 1;

    for (int i = 0; i < strLen - 1; i++) {
        for (int j = i + 1; j < strLen; j++) {
            if (isPalindrome(i, j) && j - i + 1 > answer) {
                answer = j - i + 1;
            }
        }
    }

    return answer;
}
```

하지만, 저 코드를 잘 곱씹어보면

$\sum_{i=1}^{n-1}{i} = {{(n-1)(n-2)} \over {2}}$ 임을 알 수 있다. $n$에다가 2500을 넣는다면 약 300만 번의 루프가 발생한다는 뜻이고, 시간은 3초 정도가 걸리게 된다.



## Solution

쓸데없이 루프가 도는 것을 방지하면 된다.

```c++
#include <iostream>
#include <string>

using namespace std;

string str = "";

bool isPalindrome(int head, int tail) {
    int lastIdx = (tail - head) / 2;
    for (int i = 0; i <= lastIdx; i++) {
        if (str[head + i] != str[tail - i])
            return false;
    }
    return true;
}

int solution(string s) {
    str = s;
    int strLen = str.size();
    int answer = 1;

    for (int i = 0; i < strLen - 1; i++) {
        for (int j = i + 1; j < strLen; j++) {
            if(j - i + 1 <= answer)		// 예외처리!
                continue;
            if (isPalindrome(i, j)) {
                answer = j - i + 1;
            }
        }
    }

    return answer;
}
```

뭐.. 최악의 경우에는 여전히 3초 정도가 소요되겠지만, 대부분의 케이스를 걸러낸다. 어쨌든 이 코드로 통과가 되더라.



## Retrospect

음.. 뭐 별다른 건 없다. 쫄지 말고 이중루프라도 시도해봐야 한다는 것 정도..?

여담으로 이 문제는 C++일 때는 재귀코드가 시간초과 나는데, 자바스크립트로 풀었을 경우에는 재귀로 짜도 풀린다. 코드는 다음과 같다.

```c++
function solution(s){
  if (s === s.split("").reverse().join("")) {
    return s.length; 
  } else { 
    var A = longest_palindrom(s.substring(0, s.length-1)); 
    var B = longest_palindrom(s.substring(1, s.length)); 
    return Math.max(A, B); 
  }
}
```


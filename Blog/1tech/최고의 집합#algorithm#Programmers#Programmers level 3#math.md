레벨3에서 제일 마지막으로 푼 문제다. 솔직히 출제자 뚝배기를 깨야 한다. 집합이라며... 집합이라며...!! ㅠㅠ

중고등학교 때 나처럼 용어의 정의 충실하게 봐 가면서 수학 공부 열심히 하신 분들 중에 저 집합이란 단어 때문에 시간낭비하시는 분이 없기를 바라는 마음에서 글을 쓴다.



## Problem

[https://programmers.co.kr/learn/courses/30/lessons/12938](https://programmers.co.kr/learn/courses/30/lessons/12938)

> 자연수 n 개로 이루어진 집합 중에 다음 두 조건을 만족하는 집합을 최고의 집합이라고 합니다.
>
> 1. 각 원소의 합이 S가 되는 수의 집합
> 2. 위 조건을 만족하면서 각 원소의 곱 이 최대가 되는 집합
>
> 예를 들어서 자연수 2개로 이루어진 집합 중 합이 9가 되는 집합은 다음과 같이 4개가 있습니다.
> { 1, 8 }, { 2, 7 }, { 3, 6 }, { 4, 5 }
> 그중 각 원소의 곱이 최대인 { 4, 5 }가 최고의 집합입니다.
>
> 집합의 원소의 개수 n과 모든 원소들의 합 s가 매개변수로 주어질 때, 최고의 집합을 return 하는 solution 함수를 완성해주세요.
>
> ##### 제한사항
>
> - 최고의 집합은 **오름차순으로 정렬된 1차원 배열(list, vector)** 로 return 해주세요.
> - 만약 최고의 집합이 존재하지 않는 경우에 **크기가 1인 1차원 배열(list, vector)** 에 `-1` 을 채워서 return 해주세요.
> - 자연수의 개수 n은 1 이상 10,000 이하의 자연수입니다.
> - 모든 원소들의 합 s는 1 이상, 100,000,000 이하의 자연수입니다.
>
> ------
>
> ##### 입출력 예
>
> |  n   |  s   | result |
> | :--: | :--: | :----: |
> |  2   |  9   | [4, 5] |
> |  2   |  1   |  [-1]  |



## Trial

처음에 엄청 쉬운 문제구나 싶었다. 레벨 3인데 이렇게 레벨 1 급으로 쉬워도 되나 싶어서 문제를 다시 읽어봤다. 아, 집합이구나! 집합의 정의는 다음과 같다.

> 집합(集合, [영어](https://ko.wikipedia.org/wiki/%EC%98%81%EC%96%B4): set)은 명확한 기준에 의하여 주어진 <u>**서로 다른 대상들**</u>이 모여 이루는 새로운 대상이다. 
>
> 출처: [위키백과](https://ko.wikipedia.org/wiki/%EC%A7%91%ED%95%A9)

그러니까 입력이 6, 100으로 주어지면 [16, 16, 16, 16, 17, 17]이면 집합이 아니구나, 저기서 값을 좀 조정해야 되겠구나라고 결론을 내렸다. 솔직히 쉽지가 않다. 목적은 원소들을 최대한 평균값에 가깝게 모아놓는 건데, 어떻게 흐뜨러트려야 최대가 나올지 애매했다.

생각을 계속 하다가 떠오른 아이디어가 있었다. [1, 2, 3, 4, 5, 6]에서 시작해서 모든 원소에 1씩을 계속 더한다. 그러다 보면 언젠가 그 합이 100을 초과하는 순간이 온다([15, 16, 17, 18, 19, 20]). 그 때 가장 작은 원소부터 순서대로 1씩 빼다 보면 합이 딱 100이 되는 순간이 있는데 그 순간의 집합을 답으로 반환하면 되겠더라.

```c++
#include <string>
#include <vector>

using namespace std;

int vectorSum(vector<int>::iterator begin, vector<int>::iterator end) {
    int sum = 0;
    vector<int>::iterator iter;
    for(iter=begin; iter != end; iter++) {
        sum += *iter;
    }
    return sum;
}

vector<int> solution(int n, int s) {
    vector<int> answer;
    for(int i=1; i<=n; i++) {
        answer.emplace_back(i);
    }
    if(vectorSum(answer.begin(), answer.end()) > s) return {-1};
    for(int i=1; i<s-n; i++) {
        for(int j=0; j<n; j++) {
            answer[j]++;
        }
        int sum = vectorSum(answer.begin(), answer.end());
        if(sum == s) return answer;
        if(sum > s) {
            for(int k=0; k<n; k++) {
                answer[k]--;
                if(sum - k - 1 == s) return answer;
            }
        }
    }
    return answer;
}
```

근데 효율성 테스트는 둘 째 치고, 정확도 테스트에서 개판이 난다. 왜 그럴까 곰곰이 생각해보다가 질문 게시판을 들어가 봤다.

<img src="https://raw.githubusercontent.com/3jins/Images/master/the_best_set_qna_q.png" width="100%"/>

설마 싶었다.



## Solution

그리고 그 설마가 진짜였다. 집합이 아니었던 것이다.

```c++
#include <string>
#include <vector>

using namespace std;

vector<int> solution(int n, int s) {
    vector<int> answer;
    int quotient = s / n;
    int remainder = s % n;
    if (!quotient) return {-1};
    for (int i = 0; i < n - remainder; i++) {
        answer.emplace_back(quotient);
    }
    for (int i = 0; i < remainder; i++) {
        answer.emplace_back(quotient + 1);
    }
    return answer;
}
```

하...



## Retrospect

#### 후기

<img src="https://raw.githubusercontent.com/3jins/Images/master/the_best_set_qna_a.png" width="100%"/>

#### 합이 일정할 때 곱이 최대가 되는 조건

고등학교 때부터 최댓값 구하라는 문제 나오면 시간 없을 때 야매로 풀려고 많이 쓰던 조건이라 별 설명없이 문제풀이에 적용했는데 궁금하신 분들 있을까봐 간단히만 쓰겠다.

두 수일 때의 증명은 간단하다. $(x-y)^2 >= 0$으로부터 $xy <= {{x^2+y^2} \over 2}$가 성립한다. 즉, $xy$가 최댓값을 갖기 위해서는 $(x-y)^2 = 0$이어야 하기 때문에 $x=y$일 때 최대가 된다.

세 수 이상일 경우에는 두 수씩 묶어서 생각하면 된다. 예를 들어, $x$, $y$, $z$의 세 수가 있다고 하면 $x = yz$, $y = zx$, $z=xy$를 동시에 성립시켜야 하고 따라서 $x = y = z$여야 한다. 엄밀한 증명이 필요하면 뭐 귀납법을 써보시든지 알아서 하시면 되겠다. 난 안 궁금하므로 생략한다.

설명이 잘 이해가 안 가면 주변에 수학 잘하는 친구한테 물어보자.


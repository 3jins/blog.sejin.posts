[지난주](http://enhanced.kr/postviewer/121)에 난 분명히 이렇게 말했었다.

>  개강 준비 때문에 이래저래 바빴기 때문에 매일프로그래밍 문제가 지금 4주치나 쌓여있다. 빨리 몰아서 풀어버리도록 하자.

하지만 과제가 좀 많았다. 역시 4학년 수업만으로 6전공을 듣는건 너무 빡셌나 싶다.



## Problem

> 정수 n이 주어지면, n개의 여는 괄호 "("와 n개의 닫는 괄호 ")"로 만들 수 있는 괄호 조합을 모두 구하시오. (시간 복잡도 제한 없습니다).
>
> 예제)
>
> Input: 1
>
> Output: ["()"]
>
> Input: 2
>
> Output: ["(())", "()()"]
>
> Input: 3
>
> Output: ["((()))", "(()())", "()(())", "(())()", "()()()"]



## Trial

이게 알고리즘인지 이산수학인지 확률과통계인지 모르겠다. 

일단 문제에서 예제로 준 input 1~3을 관찰해보자. input이 3인 경우를 보면 뭔가 input2에서 봤던 output들이 input3의 output들 각각에 부분적으로 녹아있는 모습이 보인다(Overlapping Subproblem). 이쯤 되면 어려운 문제인데 길찾기가 아니면 전부 DP가 아닌가 싶다. 

#### trial1: 덩어리 수로 분류하여 세기

처음에 떠올린 방법은 덩어리 수로 분류하여 해결하는 방법이었다. input 3의 경우를 예로 들면

* 덩어리 1개: ((())), (()())
* 덩어리 2개: ()(()), (())()
* 덩어리 3개: ()()()

로 분류가 가능하다. 하지만 이 경우는 두 가지 계산을 함께 수행해야 한다. (잘 이해가 안 된다면 이 방법대로 input 4인 경우의 Output 크기를 계산해보자. 바로 이해할 수 있을 것이다.)

1. 두 덩어리 이상인 경우 각 덩어리의 가능한 조합
2. 두 덩어리 이상인 경우 덩어리를 줄 세우는 순열

경험상 논리가 복잡하면 예외가 많아지고 코드가 더러워지는 경향이 있다. 따라서 좀 더 간결한 방법이 없는지 생각해보기로 했다.

#### trial2: 첫 덩어리의 크기로 분류하여 세기

첫 덩어리의 크기를 기준으로 분류하는 게 가능할 것 같다. input 3인 경우를 다시 예로 들어보겠다.

* 크기1: **()**(()), **()**()()
* 크기2: **(())**()
* 크기3: **(()())**, **((()))**

이 경우는 순열을 고려할 필요가 없어진다. 훨씬 코드 짜기 쉬울 것 같은 느낌이 들지 않는가...! 이제 코드로 옮겨보도록 하자.

```c++
#include <iostream>
#include <vector>

using namespace std;

// 문자열벡터를 쭉 돌면서 다 출력
void printStringVector(vector<string> v) {
    int lenVector = v.size();

    for (int i = 0; i < lenVector; i++) {
        cout << v[i] << " ";
    }
    cout << endl;
}

// 문자열벡터의 각 원소에 괄호를 한겹씩 더 씌워서 리턴
vector<string> wrapEachElement(vector<string> v) {
    int lenVector = v.size();
    for (int i = 0; i < lenVector; i++) {
        v[i] = "(" + v[i] + ")";
    }
    return v;
}

// 두 문자열벡터를 붙일 수 있는 모든 조합을 리턴.
// 붙이는 순서는 고정.
vector<string> getConcatCombination(vector<string> v1, vector<string> v2) {
    int len1 = v1.size(), len2 = v2.size();
    vector<string> combinations = {};
    for (int i = 0; i < len1; i++) {
        for (int j = 0; j < len2; j++) {
            combinations.push_back(v1[i] + v2[j]);
        }
    }
    return combinations;
}

void printParenthesisCom(int n) {
    vector<vector<string>> dp = {{""}, {"()"}};
    for (int i = 2; i <= n; i++) {
        vector<string> combinations = {};
        for (int j = 0; j < i; j++) {
            // dp[j]: 첫 덩어리
            // dp[i - j - 1]: 뒤에 따라오는 애들
            vector<string> leadingMass = wrapEachElement(dp[j]);
            vector<string> newCombinations = getConcatCombination(leadingMass, dp[i - j - 1]);
            combinations.insert(combinations.end(), newCombinations.begin(), newCombinations.end());
        }
        dp.push_back(combinations);
    }

    printStringVector(dp[n]);
}
```

일단 n=4인 경우까지 테스트했는데 잘 돌더라.

함수로 나눠놔서 눈에 잘 안 띄겠지만 사실 이 코드는 무려 4중 for문이 돈다. 문제에서 시간복잡도 제한이 없다고 했기 때문에 맘 놓고 썼다. 하지만 분명 더 좋은 코드가 있으리라고 기대한다. 그럼 이제 답을 보자! 



## Solution

DP를 쓸 필요조차 없었다. 나는 앞서 이 글에서 이렇게 말했었다.

> 이쯤 되면 어려운 문제인데 길찾기가 아니면 전부 DP가 아닌가 싶다. 

그리고 그건 개소리가 되었다.

```C++
List<String> parenthesisPairs(int n) {
  List<String> ans = new ArrayList();
  recurse(ans, "", 0, 0, n);
  return ans;
}

void recurse(List<String> ans, String cur, int open, int close, int n){
  if (str.length() == n * 2) {
    ans.add(cur);
    return;
  }
  if (open < n) {
    recurse(ans, cur + "(", open + 1, close, n);
  }
  if (close < open) {
    recurse(ans, cur + ")", open, close + 1, n);
  }
}
```





## Retrospect

괄호 문제들은 좀 독특하게 접근해야 되는 것 같다. 

#### 괄호는 두 종류의 기호 밖에 없다

이 문제에 접근하기 위한 키포인트가 무엇인가에 대해 곰곰히 생각해보았다. 주입식 교육의 폐해인지 괄호라는 말을 딱 들으면 스택 내지는 쌍이 맞아야 한다는 것 정도밖에는 떠오르질 않는다. 하지만 소괄호에는 아주 기본적인 특징이 하나 있는데 그건 **여는 괄호와 닫는 괄호 두 종류의 기호 밖에 없다는 것**이다. (물론 중괄호나 대괄호가 끼어들어가면 기호 수가 4개, 8개로 늘어난다. 그건 그 때 가서 생각하자.)

따라서 **괄호문제 + 조합문제**의 콜라보라면  다음과 같은 트리를 생각해볼 수 있게 된다.

| <img src="https://raw.githubusercontent.com/42deSix/Images/master/parenthesis_tree.png" width="35%"/> |
| :----------------------------------------------------------: |
|                n = 2인 경우의 모든 경우의 수                 |

#### 잘 모르겠으면 일단 세어보자

조합문제의 기본 중의 기본은 **경우의 수를 다 세어보는 것**이다. **트리는 재귀적으로 모든 노드를 탐색(traversal)할 수 있다**. 이를 이용해서 모든 조합을 다 탐색해보다가 괄호의 조건인 '쌍이 맞아야 한다'가 어긋나는 경우를 거르면 된다.

위 그림에서 빨간색 X는 닫는 괄호가 올 수 없는 상황을 거른 것이고 파란색 X는 여는 괄호가 최대 갯수인 n을 초과하여 올 수 없는 상황을 거른 것이다.
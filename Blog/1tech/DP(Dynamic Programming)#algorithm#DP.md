다이내믹 프로그래밍.. 이름만 들어서는 XP(Extreme Programming)와 비슷한 느낌도 들고 옵치나 배그하듯 긴장하고 있다가 일어나는 상황변화에 재빠르게 대처해야 할 것만 같은 뉘앙스를 풍긴다. 하지만 사실 이 이름은 큰 의미를 갖지 못한다. 

DP의 탄생배경에 관한 얘기를 잠깐 하자면, DP는 1950년경 미국 수학자 [Richard E. Bellman](https://en.wikipedia.org/wiki/Richard_E._Bellman)(벨만포드 알고리즘의 그 벨만 아저씨 맞다)에 의해 고안되었다. 그는 당시 스탠포드 대학의 수학과 부교수로 재임하고 있다가 RAND라는 공군사관학교에 잠깐 발령인지 뭔지로 가 있게 되었는데 국방부의 윌슨이라는 분이 '연구'라는 단어를 병적으로 극혐했다고 한다. 그래서 처음에 리차드는 'planning'이란 단어를 쓰고 싶었지만 윌슨님께서 ㅈㄹㅂㄱ(His face would suffuse, he would turn red, and he would get violent)하실거라 예상하여 'programming'이란 단어를 쓰기로 했다고 한다. 그리고 dynamic은 여러 스테이지로 나뉘는 DP의 특성을 설명하기에도 좋으면서 동시에 경멸조(pejorative sense)로 쓸 수 없는 단어라 썼다고 한다. 

뭐 그래서 결론적으로 DP라는 단어는 벨만 형님이 윌슨 아저씨 때문에 방어적으로(as an umbrella) 지은 이름일 뿐이니 거기에서 의미를 찾지는 말자. [여기](http://www.cas.mcmaster.ca/~se3c03/journal_papers/dy_birth.pdf)가 원문이니 궁금한 사람들은 읽어보자.

참고로 메모이제이션(memoization)이라는 이름으로도 불린다. 개인적으로 이 이름이 더 직관적이라고 생각한다.





## DP 사용 조건

서론이 길었는데, 이제 본격적으로 DP에 대해 자세히 알아보도록 하자. DP가 적용 가능한 문제는 다음의 조건을 만족해야 한다.

1. Overlapping Subproblem
2. Optimal Substructure

뭔 개소린가 싶을 것이다. 당연하다. 나 역시 DP가 뭔지 대충 알고 봤음에도 저게 뭔 개소린가 싶었다. 찬찬히 뜯어보도록 하자.



### Overlapping Subproblem

직역하면 중복되는 하위문제다. 즉, **작은 문제(subproblem)로 분리했을 때 그 문제들이 서로 겹친다**는 뜻이다. 이 특징 때문에 DP의 시간복잡도가 낮아진다고 볼 수 있겠다.



### Optimal Substructure

이건 개인적으로 이름을 왜 이 따위로 지어놨을까 싶으므로 직역하지 않겠다. **큰 문제의 정답은 작은 문제에서 구할 수 있다**는 의미라고 한다. 그래서 모든 문제를 다 풀지 않고 작은 문제 몇개만 풀고 그 작은 문제들에 간단한 연산만 해서 큰 문제의 답을 구할 수 있다는 뜻이다.





## Example

百聞而 不如一見, 百見而 不如一覺, 百覺而 不如一打라 했으니 아무리 보고 깨닫더라도 코딩해보지 않으면 온전히 이해할 수 없다. 직접 문제에 적용해보도록 하자.

이 포스트에서는 DP의 교과서 격이라 볼 수 있는 피보나치 함수에 적용해보도록 하겠다.

```c++
#include <iostream>
#include <vector>

int getFibonacci(int n) {
    /* exception handling */
    if (n < 1) {
        cout << "삐빅! 관종입니다!" << endl;
        return -1;
    }

    /* DP */
    vector<int> dp = {1, 1};
    for (int i = 2; i < n; i++) {
        dp.push_back(dp[i - 1] + dp[i - 2]);
    }
    return dp[n - 1];   // 예외처리는 생략한다!
}
```

간단하지 않은가. 40번째 항을 계산한다고 해도 덧셈연산 38번만 하면 되니 시간복잡도는 $O(n)$이다. 단순 재귀로 구현했다면 [$O(n^2)$ 꼴의 시간복잡도](https://ko.wikipedia.org/wiki/%ED%94%BC%EB%B3%B4%EB%82%98%EC%B9%98_%EC%88%98_%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%A8)가 나왔을 것이다.


















친구가 자기 회사 입사코딩테스트 문제 난이도가 [LeetCode](https://leetcode.com/)의 medium과 hard 사이의 난이도로 나온다고 해서 hard 난이도를 풀어보았다. 겁나 어렵다...



## Problem

Given an input string (`s`) and a pattern (`p`), implement regular expression matching with support for `'.'`and `'*'`.

```
'.' Matches any single character.
'*' Matches zero or more of the preceding element.
```

The matching should cover the **entire** input string (not partial).

**Note:**

- `s` could be empty and contains only lowercase letters `a-z`.
- `p` could be empty and contains only lowercase letters `a-z`, and characters like `.` or `*`.

**Example 1:**

```
Input:
s = "aa"
p = "a"
Output: false
Explanation: "a" does not match the entire string "aa".
```

**Example 2:**

```
Input:
s = "aa"
p = "a*"
Output: true
Explanation: '*' means zero or more of the precedeng element, 'a'. Therefore, by repeating 'a' once, it becomes "aa".
```

**Example 3:**

```
Input:
s = "ab"
p = ".*"
Output: true
Explanation: ".*" means "zero or more (*) of any character (.)".
```

**Example 4:**

```
Input:
s = "aab"
p = "c*a*b"
Output: true
Explanation: c can be repeated 0 times, a can be repeated 1 time. Therefore it matches "aab".
```

**Example 5:**

```
Input:
s = "mississippi"
p = "mis*is*p*."
Output: false
```



## Trial

```javascript
const isMatch = (str, regex) => {
  const parseRegex = (regex) => {
    const parsed = [];
    const regexLen = regex.length;
    let charIdx = 0;
    while (charIdx < regexLen) {
      if (charIdx === regexLen - 1 || regex[charIdx + 1] !== '*') {
        parsed.push(regex[charIdx]);
        charIdx += 1;
      } else {
        parsed.push([regex[charIdx], regex[charIdx + 1]].join(''));
        charIdx += 2;
      }
    }
    return parsed;
  };

  const isSame = (ch, pattern) => {
    if (pattern === '.') return true;
    else return ch === pattern;
  };

  const isPatternsRemained = (patternIdx) => {
    let i = 0;
    while (patternIdx + i < patterns.length) {
      if (!patterns[patternIdx + i].endsWith('*')) return true;
      i += 1;
    }
    return false;
  };

  const isPositiveEnd = (ch, pattern, patternIdx) => (
    ch === undefined && pattern === undefined
    || ch === undefined && !isPatternsRemained(patternIdx)
  );
  const isNegativeEnd = (ch, pattern, patternIdx) => (
    (pattern === undefined && ch !== undefined)
    || (ch === undefined && !isPositiveEnd(ch, pattern, patternIdx))
  );

  const compare = (chIdx, patternIdx, test) => {
    let isMatched = false;
    let isUnmatched = false;
    while (true) {
      const ch = chars[chIdx];
      const pattern = patterns[patternIdx];
      // console.log(test, chIdx, patternIdx, ch, pattern);
      if (isPositiveEnd(ch, pattern, patternIdx)) isMatched = true;
      if (isNegativeEnd(ch, pattern, patternIdx)) isUnmatched = true;
      if (isMatched) return true;
      if (isUnmatched) return false;

      if (pattern.endsWith('*')) {
        // if (isNegativeEnd(ch, pattern, patternIdx)) return false;
        if (!isSame(ch, pattern[0])) { // cannot continue
          patternIdx += 1;
        } else {
          if (compare(chIdx + 1, patternIdx, test + 1)) { // continue
            isMatched = true;
          } else {
            isUnmatched = true;
          }
          if (compare(chIdx, patternIdx + 1, test + 1)) { // escape
            isMatched = true;
          } else {
            isUnmatched = true;
          }
        }
      } else if (isSame(ch, pattern)) {
        if (isNegativeEnd(ch, pattern, patternIdx)) isUnmatched = true;
        chIdx += 1;
        patternIdx += 1;
      } else {
        isUnmatched = true;
      }
    }
  };

  const chars = str.split('');
  const patterns = parseRegex(regex);

  return compare(0, 0, 0);
};

module.exports = isMatch;
```

믿기지 않을 수도 있지만 accept된 코드다. 심하게 복잡한데, 골자는 다음과 같다.

1. `parseRegex`

    정규식을 여러 토큰으로 자른다. `*`이 붙은 경우, 2글자가 하나의 토큰이 된다. 예를 들어, `aa*.b`가 주어지면 `['a', 'a*', '.', 'b']`가 되는 식이다.

2. `compare`

    각 토큰과 문자열의 각 문자를 하나하나 비교한다. 이 때, `*` 토큰을 만날 경우, 모든 경우의 수를 탐색해봐야 하므로 재귀로 넘긴다.

이게 다다. 여기서 `compare`의 루프 종료조건 처리가 복잡해졌을 뿐.



## Solution

풀이를 보고 정말 내가 애송이라는 것을 처절하게 느낄 수 있었다. 풀이에서는 크게 2가지 방법을 알려줬는데 하나는 재귀, 다른 하나는 무려 DP를 사용하는 방법이다.

### Approach 1: Recursion

```java
class Solution {
    public boolean isMatch(String text, String pattern) {
        if (pattern.isEmpty()) return text.isEmpty();
        boolean first_match = (!text.isEmpty() &&
                               (pattern.charAt(0) == text.charAt(0) || pattern.charAt(0) == '.'));

        if (pattern.length() >= 2 && pattern.charAt(1) == '*'){
            return (isMatch(text, pattern.substring(2)) ||
                    (first_match && isMatch(text.substring(1), pattern)));
        } else {
            return first_match && isMatch(text.substring(1), pattern.substring(1));
        }
    }
}
```

나와 핵심 아이디어는 같지만 코드의 길이가 어마어마하게 짧다. 핵심은 다음의 2가지로 요약할 수 있겠다.

1. 문자열의 substring을 재귀함수에 넘긴다. 그래서 시작인덱스의 문자만 비교한다.

    하지만 사실 이건 공간복잡도 면에서 약간의 손해를 감수하는 것이기도 하다. 인덱스만 넘기는 게 당연히 메모리를 덜 먹는다.

2. 리턴할 때 굳이 분기하지 않아도 된다. `||`로 여러 결과를 연결해 리턴한다. 어차피 `true` 하나만 있으면 그냥 결국 다 `true`니까.

    이로 인해서 모든 재귀 호출이 `return`문 안에 들어갈 수 있고, 상태플래그 같은 걸 쓸 필요 없이 코드가 깔끔해진다.

### Approach 2: Dynamic Programming

```java
enum Result {
    TRUE, FALSE
}

class Solution {
    Result[][] memo;

    public boolean isMatch(String text, String pattern) {
        memo = new Result[text.length() + 1][pattern.length() + 1];
        return dp(0, 0, text, pattern);
    }

    public boolean dp(int i, int j, String text, String pattern) {
        if (memo[i][j] != null) {
            return memo[i][j] == Result.TRUE;
        }
        boolean ans;
        if (j == pattern.length()){
            ans = i == text.length();
        } else{
            boolean first_match = (i < text.length() &&
                                   (pattern.charAt(j) == text.charAt(i) ||
                                    pattern.charAt(j) == '.'));

            if (j + 1 < pattern.length() && pattern.charAt(j+1) == '*'){
                ans = (dp(i, j+2, text, pattern) ||
                       first_match && dp(i+1, j, text, pattern));
            } else {
                ans = first_match && dp(i+1, j+1, text, pattern);
            }
        }
        memo[i][j] = ans ? Result.TRUE : Result.FALSE;
        return ans;
    }
}
```

음... 내 짧은 식견으로는 이게 왜  DP인지 잘 모르겠다. 애초에 뭘 최적화하는 게 아니라 하나라도 되면 그냥 되는건데 왜 기록을 하고 있는걸까.. 그냥 위의 접근방법에서 `text`를 통째로 잘라넣는 대신 인덱스 2개 넣어주면 성능이 똑같이 나올 것 같은 느낌이 든다. 나중에 실력이 좀 더 쌓이면 이 부분은 다시 보도록 하자.



## Retrospect

이 문제에서 배운 점을 요약하면 다음과 같다.

#### 논리연산자

**복잡한 분기**를 처리할 때는 **논리연산자**를 잘 활용하면 코드가 짧아질 뿐만 아니라 성능까지 좋아진다. 다음은 기존 풀이와 개선된 풀이의 성능차이다.

<img src="https://raw.githubusercontent.com/3jins/Images/master/leetcode-regular-expression-matching-performance-comparison.png" width="60%"/>

#### 문자열처리와 재귀

짧은 문자열을 이용한 재귀일 때는 인덱스를 넘기는 것보다 문자열을 넘기는 게 나을 수 있다.

#### DP

재귀로 풀 수 있으면 DP로도 풀 수 있는 듯하다. 하지만 최적화 문제가 아니라면 DP로 변경했을 때의 이점이 뭘까는 의문이다.


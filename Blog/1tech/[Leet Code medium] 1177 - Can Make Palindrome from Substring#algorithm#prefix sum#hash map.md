친구 몇 명과 함께 매주 정기적으로 적당한 난이도의 문제를 하나 골라서 함께 풀어보는 [모임](https://github.com/Faangorithm)을 갖기 시작했다. 그 중 어제 푼 문제는 특히 배운 내용이 많아 따로 글로 정리하기로 했다. 이 문제는 처음 봤을 때는 medium 난이도라는 게 안 믿길 만큼 쉬워 보였는데, 풀면서 여러 가지를 깨달아가면서 점점 체감 난이도가 상승했다.



## Problem

[https://leetcode.com/problems/can-make-palindrome-from-substring/](https://leetcode.com/problems/can-make-palindrome-from-substring/)

>Given a string `s`, we make queries on substrings of `s`.
>
>For each query `queries[i] = [left, right, k]`, we may **rearrange** the substring `s[left], ..., s[right]`, and then choose **up to** `k` of them to replace with any lowercase English letter. 
>
>If the substring is possible to be a palindrome string after the operations above, the result of the query is `true`. Otherwise, the result is `false`.
>
>Return an array `answer[]`, where `answer[i]` is the result of the `i`-th query `queries[i]`.
>
>Note that: Each letter is counted **individually** for replacement so if for example `s[left..right] = "aaa"`, and `k = 2`, we can only replace two of the letters.  (Also, note that the initial string `s` is never modified by any query.)
>
> 
>
>**Example :**
>
>```
>Input: s = "abcda", queries = [[3,3,0],[1,2,0],[0,3,1],[0,3,2],[0,4,1]]
>Output: [true,false,false,true,true]
>Explanation:
>queries[0] : substring = "d", is palidrome.
>queries[1] : substring = "bc", is not palidrome.
>queries[2] : substring = "abcd", is not palidrome after replacing only 1 character.
>queries[3] : substring = "abcd", could be changed to "abba" which is palidrome. Also this can be changed to "baab" first rearrange it "bacd" then replace "cd" with "ab".
>queries[4] : substring = "abcda", could be changed to "abcba" which is palidrome.
>```
>
> 
>
>**Constraints:**
>
>- `1 <= s.length, queries.length <= 10^5`
>- `0 <= queries[i][0] <= queries[i][1] < s.length`
>- `0 <= queries[i][2] <= s.length`
>- `s` only contains lowercase English letters.

이 문제의 첫번째 어려운 점은 독해가 어렵다는 점이다. 쓸데없이 입력형식이 복잡해서 설명이 어렵다. 예시를 먼저 보고 다시 설명을 읽으면 조금이나마 쉽게 읽을 수 있다.

요약하면 주어진 문자열의 일부를 잘라서 재배열한 뒤 최대 k개의 글자를 교체했을 때 거꾸로 읽어도 똑같은 문자열이 나오는지 확인하라는 문제다.



## Trial

일단 조건이 상세하게 나와있으니까 복잡도부터 계산해보자.

문자열 s의 길이는 최대 10만이다. 또한 쿼리의 갯수도 10만개까지 주어질 수 있다. 이게 시간제한이 각 쿼리에 대해 걸리는건지 전체 쿼리를 다 계산하는데 걸리는건지가 애매했다. 각 쿼리에 대해 시간제한이 걸린다는 가정 하에 먼저 문제를 풀어봤다.

```java
class Solution {
    public List<Boolean> canMakePaliQueries(String s, int[][] queries) {
        List<Boolean> resultList = new ArrayList<>();
        for (int[] query : queries) {
            int[] alphabetMap = new int[26];
            String substr = s.substring(query[0], query[1] + 1);
            int substrLen = substr.length();
            for (int i = 0; i < substrLen; i++) {
                alphabetMap[substr.charAt(i) - 'a']++;
            }
            int cnt = 0;
            for (int i = 0; i < 26; i++) {
                cnt += (alphabetMap[i] % 2);
            }
            if (substrLen % 2 == 1) cnt--;
            if (cnt / 2 <= query[2]) {
                resultList.add(true);
            } else {
                resultList.add(false);
            }
        }
        return resultList;
    }
}
```

어차피 재배열이 가능하기 때문에 문자열에서 등장하는 각 문자의 빈도수만 세면 된다. 'abbaacaddc'와 같이 문자열의 모든 문자가 짝수번 등장하는 경우, 아무 글자도 대체하지 않고 'aabcddcbaa'와 같은 팰린드롬을 만들 수 있다. 따라서 홀수 번 등장하는 문자가 몇 개인가가 중요하다. 이를 길이가 26인 해시맵 또는 배열에 기록해두면 된다.

결국 좌우대칭을 시킬 것이므로 등장횟수(`cnt`)의 반만 있으면 되고, 추가로 문자열의 길이가 홀수라면 가운데에 들어갈 문자는 홀수여도 상관없기에 등장횟수에서 1을 빼준다. (참고로 문자열의 길이가 홀수일 때는 모든 문자가 짝수번 등장할 수는 없으므로 이 부분에 대한 예외처리는 필요없다.)

이렇게 풀면 $O(N)$의 시간복잡도로 쉽게 해결할 수 있다. ~~사실 이 풀이가 나오기 전에 문제 오독으로 2번을 틀렸다.~~ 하지만 이 코드를 제출해보면 31개 테스트케이스 중 29번째 테스트케이스에서 시간초과가 나는 것을 볼 수 있다.



## Solution

시간복잡도의 장벽을 넘기 위해서는 두 가지 방법이 있다. 각 쿼리마다 $O(\log N)$의 복잡도로 문제를 해결하거나, 또는 쿼리 돌리기 전에 한 번의 전처리로 모든 걸 해결하거나.

내 부족한 상식으로는 도저히 답을 찾을 수 없었지만, 해답은 간단한 데에 있었다.
$$
a_n = S_n - S_{n-1}
$$
이 수능 냄새나는 수식을 이용하면 $26 \times N$ 시간의 전처리만으로 모든 것을 해결할 수 있다.

```java
class Solution {
    public List<Boolean> canMakePaliQueries(String s, int[][] queries) {
        List<Boolean> resultList = new ArrayList<>();
        int strLen = s.length();
        int[][] prefixSums = new int[strLen + 1][26];
        for (int i = 1; i <= strLen; i++) {
            for (int j = 0; j < 26; j++) {
                prefixSums[i][j] = prefixSums[i - 1][j];
            }
            prefixSums[i][s.charAt(i - 1) - 'a']++;
        }

        for (int[] query : queries) {
            int numOdds = 0;
            for (int i = 0; i < 26; i++) {
                int freq = prefixSums[query[1] + 1][i] - prefixSums[query[0]][i];
                numOdds += (freq % 2);
            }
            if ((query[1] - query[0] + 1) % 2 == 1) numOdds--;
            if (numOdds / 2 <= query[2]) {
                resultList.add(true);
            } else {
                resultList.add(false);
            }
        }
        return resultList;
    }
}
```

`prefixSums`는 Trial의 `alphabetMap`을 문자열 길이만큼 만들어놓은 배열이다. 예를 들어, 'abcbd'라는 문자열이 주어진다면, `prefixSums`는 `[[0, 0, 0, 0], [1, 0, 0, 0], [1, 1, 0, 0], [1, 1, 1, 0], [1, 2, 1, 0], [1, 2, 1, 1]]`이 될 것이다.

2번 인덱스부터 4번 인덱스까지의 부분문자열에 대한 문자 빈도 수의 맵을 만들어야 한다면 $S_{4 + 1} - S_2$, 즉, `prefixSums[4 + 1]`의 모든 원소에 `prefixSums[2]`의 모든 원소를 빼면 된다.

이후의 로직은 Trial과 같다.



## Retrospect

#### 문제를 똑바로 읽자

문제를 빨리 파악하기 위해 테스트케이스부터 보는 건 좋지만, 테스트케이스를 이해했으면 빼먹은 조건이 없는지 문제를 다시 읽어봐야 한다.

#### 재배열 쌉가능

처음에는 정렬해놓고 시작할 생각을 했었다. 

재배열이 가능하다는 것은 순서가 아무 의미가 없다는 뜻으로, 이 때 쓸 수 있는 가장 강력한 무기는 해시맵이다.

#### 부분합

슬라이딩 윈도우와 매우 비슷한 모습으로 등장한다. 차이가 있다면, 슬라이딩 윈도우 문제와 달리 **부분합 문제는 `left`와 `right`가 움직이는 데에 순서가 없다는 점**이다.
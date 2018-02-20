7주차 ~ 9주차는 생략하고 10주차로 바로 넘어간다. 라인플러스 인턴 코딩테스트 준비할 겸해서 호다닥 풀었었는데 별로 어려운 부분이 없었기 때문이다. 인턴 코딩테스트도 떨어질 정도의 허접한 실력으로 풀어서 쉬웠으면 그 문제는 객관적으로 쉬운거라고 본다.

10주차 문제는 1주차에 나왔던 LIS와 좀 닮았다. 근데 자세히 보면 미묘하게 다르고 그로 인해 풀이 방법이 상당히 달라진다. ~~그리고 사실 이번 문제는 푸는것보다 문제 읽고 이해하는 게 더 어렵다.~~



## Problem

>  String이 주어지면, 중복된 char가 없는 가장 긴 서브스트링 (substring)의 길이를 찾으시오.  
>
> 예제)
>
> Input: “aabcbcbc”
>
> Output: 3 // “abc”
>
> Input: “aaaaaaaa”
>
> Output: 1 // “a”
>
> Input: “abbbcedd”
>
> ﻿Output: 4 // “bced”

문제가 말이 좀 어려운 것 같아서 부연설명하겠다. '중복된 char가 없는'이 무슨 뜻이냐면, 해당 문자열을 구성하는 모든 문자가 다 달라야 한다는 뜻이다. 즉, substring이 'deca'는 되지만 'deda'는 안된다는 뜻이다.  



## Trial

처음에 문제 이해 잘못해서 이상하게 풀었다. 내가 의도한 대로 답이 잘 나오긴 했는데 별 의미 없으니까 넘어가겠다. 



## Solution

[longest increasing sequence](http://enhanced.kr/postviewer/96)의 변형이다. window의 규칙을 검사하는 부분만 달라지면 된다. 

window 안의 모든 원소를 돌면서 중복검사를 하려면 시간이 많이 든다($O(n^2)$). 윈도우 안에 각 원소가 있는지만 확인하면 되므로 해시맵을 사용하면 시간복잡도를 $O(n)$까지 낮출 수 있다.

```c++
int getLongestUnduplicatedSubstringSize(string s) {
    vector<char> v(s.begin(), s.end());
    int stringSize = v.size();
    unordered_map<char, int> map;
    int substrStart = 0;
    int ret = substrStart;

    for (int i = 0; i < stringSize; i++) {
        if(map.find(v[i]) != map.end()) {
            substrStart = map[v[i]] + 1;
        }
        map[v[i]] = i;
        ret = max(ret, i - substrStart + 1);
    }

    return ret;
}
```





## Retrospect

#### iterator of vector

이 문제에서는 유독 iterator가 많이 등장한다. 

* string -> vector 변환
* vector -> string 변환
* unordered_map.find

이걸 써야 된다는 걸 몰라서, 또는 어떻게 쓰는지를 잘 몰라서 은근히 시간을 많이 잡아먹었다. 이에 [이 부분에 대해 정리](https://enhanced.kr/postviewer/515)하지 않고 넘어갈 수가 없었다.

#### LIS 변형

LIS의 window 개념만 잘 잡아놓으면 어디서 머리를 잘 굴려야 하는지를 쉽게 파악할 수 있었다.

#### Hash Map

이 문제가 어렵다면 결국 얘 때문이다. 나중에 해쉬맵만 모아서 다시 풀어보는 시간을 갖도록 하자.
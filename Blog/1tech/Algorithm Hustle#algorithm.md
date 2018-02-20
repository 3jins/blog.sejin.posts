라인, 카카오, NHN, 삼성, 우아한형제들, SK Hynix, LG CNS, 넷마블 등 많은 기업에서 코딩테스트를 통해 개발자를 채용한다. 몇 년 전만 해도 코딩테스트는 웬만하면 별찍기 수준의 문제로, 허수지원자를 거르는 용도였으나 요즘은 그 난이도가 꽤 높아서 제대로 준비하지 않으면 시간 내에 문제를 풀 수가 없다. 

지인, 지인의 지인, 커뮤니티 등을 통해 들은 시니어 개발자들의 의견을 종합해보면 다음의 결론이 나온다.
$$
\begin{array}{c}
\text{구현능력} \cap \text{개발능력} = \phi \\
\text{but} \\ 
\text{개발자의 실력} = \text{구현능력} \cup \text{개발능력}
\end{array}
$$
말인즉슨, 알고리즘 문제를 푸는 구현능력 같은 건 당장 눈에 보이는 프로그램을 개발하는 데에 필요 없어 보이지만, 실력 있는 개발자가 되기 위해서는 결국은 갖춰야 한다는 능력이라는 의미다. 따라서 알고리즘으로 개발자를 채용하는 현실에 크게 불만 갖지 말고 그냥 겸사겸사해서 준비하면 되겠다.





## Probelm Set

다음은 내가 풀어본 문제들의 인덱스 및 해당하는 유형이다. 너무 쉬운 문제는 별도로 정리하지 않았다. 모든 문제의 리스트는 [깃헙](https://github.com/3jins/algorithm-hustle/)에서 확인할 수 있다.



### 매일프로그래밍

알고리즘 문제를 매주 한 문제씩 메일로 받아볼 수 있는 유료구독 서비스다. 이걸 이렇게 다 공개해놔도 되는지 잘 모르겠다... 본인들 말로는 실리콘밸리 코딩인터뷰 문제라고 하는데 뭐 특별한 근거는 없다. DP 유형 문제의 퀄리티가 좋고 난이도가 적당하여 꾸준히 풀고 있다.

* [1\. 가장 큰 이어지는 원소들의 합](https://enhanced.kr/postviewer/100) - DP(pseudo LIS)
* [2\. 짝수 피보나치 수의 합](https://enhanced.kr/postviewer/121) - DP
* [3\. 괄호 조합 구하기](https://enhanced.kr/postviewer/150) - counting
* [5\. 합이 타겟값이 되는 두 원소 찾기](https://enhanced.kr/postviewer/200) - hash map
* [6\. 겹치는 간격 합치기](https://enhanced.kr/postviewer/280) - disjoint set, sort
* [10\. 중복 문자가 없는 LIS](https://enhanced.kr/postviewer/504) - DP(LIS), hash map
* [12\. 자신을 뺀 나머지 원소들의 곱](https://enhanced.kr/postviewer/529) - hash map, puzzle
* [15\. 링크드리스트의 끝에서 N번째 노드 제거하기](https://enhanced.kr/postviewer/577) - two pointers
* [17\. 장애물이 있는 최단거리 문제](https://enhanced.kr/postviewer/624) - BFS
* [18\. 이진트리 좌우반전](https://enhanced.kr/postviewer/700) - data structure



### 프로그래머스

요즘 코딩테스트를 봤다 하면 거의 다 이 플랫폼을 써서 보기 때문에 프로그래머스에 익숙해지는 게 좋다. 레벨별로 알고리즘 문제를 분류해놔서 수준에 맞게 연습하기 매우 좋다. 어떻게든 문제를 꾸역꾸역 풀고 나면 다른 사람들의 코드도 볼 수 있다. 

단점이라면, 첫 째로 못 푼 문제는 풀이를 제공하지 않기 때문에 블로그를 전전하면서 풀이를 찾아내야 한다는 점, 둘 째로 취준생에게는 필요 없는 수학문제가 은근히 많이 섞여있다는 점 정도겠다.

#### Level 2

Level 2에도 가끔 어려운 문제가 있다. 다 풀어보는 게 좋다.

* [124 나라의 숫자](https://enhanced.kr/postviewer/760) - math(진법)
* [가장 큰 정사각형 찾기](https://enhanced.kr/postviewer/795) - 2차 DP
* [다음 큰 숫자](https://enhanced.kr/postviewer/843) - bit operation
* [땅따먹기](https://enhanced.kr/postviewer/882) - 2차(?) DP, 추가조건 제거하기
* [N개의 최소공배수](https://enhanced.kr/postviewer/961) - math(유클리드 호제법, 최소공배수)

#### Level 3

아주 어려운 문제들은 아니다. 못 풀면 그냥 유형이 익숙치 않은 것이므로 연습하면 누구나 풀 수 있는 수준이다. 거의 대부분의 회사는 이 레벨까지 '빠르고 정확하게' 풀 수 있으면 된다.

* [2 x n 타일링](https://enhanced.kr/postviewer/1019) - DP
* [가장 긴 팰린드롬](https://enhanced.kr/postviewer/1057) - recursive call, loop
* [거스름돈](https://enhanced.kr/postviewer/1135) - DP(knap sack)
* [야근지수](https://enhanced.kr/postviewer/1192) - priority queue
* [최고의 집합](https://enhanced.kr/postviewer/1252) - math(합이 정해졌을 때 곱의 최댓값)

#### Level 4

대회 문제와 공채 문제의 경계쯤 된다. 카카오 공채의 경우 이 정도 수준까지 섞어서 출제하는 것 같다.

* [3 x n 타일링](https://enhanced.kr/postviewer/1266) - DP
* [선입 선출 스케줄링](https://enhanced.kr/postviewer/1357) - 미해결
* [숫자 블록](https://enhanced.kr/postviewer/1418)



### 카카오 블라인드 테스트

그 유명한 카카오 블라인드 공채 시험 기출이다. 신입 공채들 중에는 상당히 난이도가 높은 축에 속한다. 문제가 그 자체로 어렵기보다는 보통 예외케이스가 많아서 시간이 촉박한 게 특징이다.

* [2018 1차 (2017년 시행)](https://enhanced.kr/postviewer/1668)

* [2018 3차 (2017년 시행)](https://enhanced.kr/postviewer/1794)



### 삼성 SW 역량 테스트

카카오보다 오히려 더 어려운 느낌이다. 대신 유형이 90% 이상 DFS에 한정돼 있다.

* [구슬 탈출 2](https://enhanced.kr/postviewer/1845) - BFS
* [2048 (Easy)](https://enhanced.kr/postviewer/1896) - DFS
* [뱀](https://enhanced.kr/postviewer/2082) - simulation
* [시험감독](https://enhanced.kr/postviewer/2137) - 유형 모름



### 삼성 Expert Academy

졸업작품 끝내고 나서 풀 예정이다. 참고로 그 괴랄하게 어렵다는 삼성 SW 검정시험의 expert 단계랑은 다른거다. 





## Knowledge

코딩테스트를 푸는 데 있어서 필요한 여러 지식을 정리한 글의 모음이다.

* 일반
  * [유형별 알고리즘 적용 전략](https://enhanced.kr/postviewer/934)
* 알고리즘 유형
  * 길찾기
    * DFS
    * [BFS](https://enhanced.kr/postviewer/593)
  * DP
    * [DP](https://enhanced.kr/postviewer/91)
    * LCS
    * [LIS](https://enhanced.kr/postviewer/96)
    * [knapsack](https://enhanced.kr/postviewer/1094)
    * two pointers
  * 자료구조
    * [Disjoint Set](https://enhanced.kr/postviewer/282)
* C++ 문법
  * [C++ 문자열 파싱 함수 실용 백과](https://enhanced.kr/postviewer/806)
  * [Regular Expression](https://enhanced.kr/postviewer/2028)
  * [Number, Character, String Convertion](https://enhanced.kr/postviewer/1787)
  * [C++ 2차원 벡터 정렬하기](https://enhanced.kr/postviewer/508)
  * [Priority Queue](https://enhanced.kr/postviewer/1297)
  * [C++ Iterator](https://enhanced.kr/postviewer/515)
  * [Implementing Data Structure with C++](https://enhanced.kr/postviewer/607)
* 기타
  * [유클리드 호제법(Euclidean Algorithm)](https://enhanced.kr/postviewer/1014)
  * 
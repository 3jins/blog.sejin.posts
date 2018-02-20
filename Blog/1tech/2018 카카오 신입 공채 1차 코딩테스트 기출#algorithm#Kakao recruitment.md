내게 있어서 알고리즘 공부의 제 1 목적은 취직이다. 문제해결능력과 컴퓨팅적 사고를 기르고 하는 건 덤일 뿐이다. 사실 요즘 풀고 있는 알고리즘 문제들은 실제 코딩할 때 필요한 수준의 사고력을 훨씬 뛰어넘어서 자꾸 [DP로](https://enhanced.kr/postviewer/1135) [마법을](https://enhanced.kr/postviewer/1094) [부릴](https://enhanced.kr/postviewer/96) 것을 강요한다.

이에 회의감을 느껴 내 현재 수준을 알아보고자 공채로 신입 되는 게 낙타 바늘구멍 통과하는 것마냥 어렵다는 카카오 신입 공채 코딩테스트 문제를 풀어보기로 했다(2017년에 시행한 공채시험이 2018 공채다). 원래 글 하나당 한 문제씩 정리하려고 했는데 1차 테스트는 생각보다 난이도가 낮아서 포스트 하나에 싹 정리하기로 했다.

문제 본문은 [카카오 공식 블로그](http://tech.kakao.com/2017/09/27/kakao-blind-recruitment-round-1/)를 참고하자. 



## 1. 비밀 지도 (난이도: 하)

이건 좀 너무하다 싶게 쉬워서 생략한다. 그냥 비트연산 잘 쓰면 된다.



## 2. 다트 게임 (난이도: 하)

카카오에서 코딩 문제를 냈다 하면 반드시 나오는 게 있다. ~~아이유와~~ 길찾기(6번에서 나온다), 그리고 문자열 파싱이다. 카카오 코드 페스티벌에 나오는 문자열 파싱 문제는 하나 같이 다들 괴랄하고 더러워서 보면 넘기는 게 상책인데 반해, 얘는 그렇게 심하게 어렵지는 않다.

### Solution

까다로울 수 있는 부분이 ~~정확한 독해와~~ 스타상 정도로 보인다. 스타상은 바로 전에 얻은 점수까지 2배로 올려줘야 하기 때문이다. 하지만 그냥 뒤에서부터 거꾸로 점수 계산해주면 무난하게 해결할 수 있다.

또, 10점이 존재하기 때문에 이 부분을 별도로 예외처리해줘야 한다. 이 과정에서 코드 한 줄 실수로 없애면서 시간을 많이 소모해버렸다.

```c++
#include <iostream>
#include <vector>

using namespace std;

class Record {
public:
    int score;
    char bonus;
    char option;
};

vector<Record *> parseDartResult(string dartResult) {
    int strLen = dartResult.length();
    int flag = 0;
    vector<Record *> parsed;
    Record *record;
    for (int i = 0; i < strLen; i++) {
        switch (flag) {
            case 0:
                record = new Record();
                if(dartResult[i] == '1' && dartResult[i + 1] == '0') {
                    record->score = 10;
                    i++;
                } else {
                    record->score = dartResult[i] - '0';
                }
                flag++;
                break;
            case 1:
                record->bonus = dartResult[i];
                if (i == strLen - 1) {
                    parsed.emplace_back(record);
                } else {
                    flag++;
                }
                break;
            case 2:
                if (dartResult[i] == '*' || dartResult[i] == '#') {
                    record->option = dartResult[i];
                    flag = 0;
                    parsed.emplace_back(record);
                } else {
                    parsed.emplace_back(record);
                    record = new Record();
                    if (dartResult[i] == '1' && dartResult[i + 1] == '0') {
                        record->score = 10;
                        i++;
                    } else {
                        record->score = dartResult[i] - '0';
                    }
                    flag = 1;
                }
                break;
        }
    }
    return parsed;
}

int solution(string dartResult) {
    int total = 0;
    bool starPrize = false;
    vector<Record *> parsed = parseDartResult(dartResult);

    for (int i = 2; i >= 0; i--) {
        // score
        int curScore = parsed[i]->score;

        // bonus
        switch (parsed[i]->bonus) {
            case 'D':
                curScore *= curScore;
                break;
            case 'T':
                curScore *= curScore * curScore;
                break;
        }

        // option
        if (starPrize) {
            curScore *= 2;
            starPrize = false;
        }
        if (parsed[i]->option == '#') {
            curScore *= -1;
        } else if (parsed[i]->option == '*') {
            curScore *= 2;
            starPrize = true;
        }

        total += curScore;
    }

    return total;
}
```

좀 길긴 한데.. 문자열 파싱해서 `Record` 벡터에 잘 담아둔 다음에 거꾸로 벡터 포장 풀어서 점수 계산한 코드다. 

### Retrospect

#### 정규식

사실 이 문제는 저런 식으로 일일이 다 노가다해서 검사할 게 아니라 정규식 쓰면 코드가 훨씬 짧아진다. PHP에서는 `ereg`라는 함수가 있어서 정규식에 매칭되는 문자열을 변수에 담아 쓸 수 있다. C++에도 그런 놈이 없을까 찾아봤는데 [있다](http://www.cplusplus.com/reference/regex/smatch/). 다른 글에서 별도로 정리하도록 하겠다.



## 3. 캐시 (난이도: 하)

~~해설이 '구글링해서 복붙하시면 됩니다'다.~~ 솔직히 처음 봤을 때 어려운 문제인 줄 알았다. 문제 생긴 게 딱 [선입 선출 스케줄링](https://enhanced.kr/postviewer/1357) 느낌이 나서 쫄았는데, 막상 풀어보니 별 거 아니었다. 해설에서는 무슨 LRU 알고리즘 검색해서 풀라고 돼 있는 것으로 보아 출제의도는 진짜 LRU인 것 같다. 하지만 깊이 생각하지 않고 풀어도 그냥 벡터 쓸 줄 알면 누구나 풀 수 있다. 아마 LRU에 꽂혀서 문제 내면서 난이도 맞추려고 하향하다 보니 출제의도가 흐려진 게 아닌가하고 감히 추측해본다.

### Solution

```c++
#include <iostream>
#include <vector>

using namespace std;

string toLowerCase(string str) {
    int strLen = str.length();
    for (int i = 0; i < strLen; i++) {
        if (str[i] >= 'A' && str[i] <= 'Z') {
            str[i] += 'a' - 'A';
        }
    }
    return str;
}

int solution(int cacheSize, vector<string> cities) {
    int runTime = 0;
    bool isHit = false;
    vector<string> cache;
    int numCities = cities.size();

    for (int i = 0; i < numCities; i++) {
        string cityName = toLowerCase(cities[i]);
        vector<string>::iterator iter;
        for (iter = cache.begin(); iter != cache.end(); iter++) {
            if (*iter == cityName) {
                cache.erase(iter);
                runTime++;
                isHit = true;
                break;
            }
        }
        cache.emplace_back(cityName);
        if (isHit) {
            isHit = false;
            continue;
        }
        runTime += 5;
        if (i >= cacheSize) cache.erase(cache.begin());
    }
    return runTime;
}
```

### Retrospect

워스트케이스가 도시 수(10만) * 캐시 크기(30) = 300만이 되겠다. 이는 3초를 의미한다. 알고리즘 문제에서 3초란 참 애매한 시간이라 실제 채점시스템에 돌려보지 않으면 모르는 일인데 난이도도 하로 박혀있고 캐시를 어쨌든 뒤져야 되기 때문에 저기서 더 줄이기는 어려울 것으로 보인다.



## 4. 셔틀버스 (난이도: 중)

다음은 카카오 측이 이 시험에 대해 공식적으로 밝힌 입장이다.

> 아울러 이번 코딩 테스트는 대회가 아니라 채용을 위한 시험인 만큼 ACM-ICPC 같은 어려운 알고리즘 설계 능력을 겨루는 문제가 아닌 **업무에서 있을만한 상황을 가정하여** 독창적이고 다양한 분야의 문제를 출제했고...

아... 출근 최대한 늦게 하는 게 업무에서 있을만한 상황이구나... 좋은 회사다.

이 문제 푸는데는 시간은 제법 걸렸다. 조건이 많기 때문이다. 아직도 테스트케이스 외에 다른 케이스를 집어넣으면 에러가 날 것 같기도 하지만 문제 자체가 어렵다기보다는 꼼꼼하게 푸는 게 더 중요하다는 생각이 든다. 연습을 많이 하도록 하자.

### Solution

```c++
#include <iostream>
#include <vector>
#include <string>
#include <queue>

using namespace std;

vector<string> split(string s, string delim) {
    vector<string> ret;
    while (true) {
        int cutIdx = s.find(delim);
        if (cutIdx < 0) {
            ret.emplace_back(s);
            break;
        }
        ret.emplace_back(s.substr(0, cutIdx));
        s = s.substr(cutIdx + delim.length());
    }
    return ret;
}

vector<int> convertTimeTable(vector<string> timeTable) {
    vector<int> times;
    int timeTableSize = timeTable.size();
    for (int i = 0; i < timeTableSize; i++) {
        vector<string> splitTime = split(timeTable[i], ":");
        times.emplace_back(stoi(splitTime[0]) * 60 + stoi(splitTime[1]));
    }
    return times;
}

string solution(int numBuses, int interval, int capacity, vector<string> timeTable) {
    int time = 540;
    int latestTime = time;
    vector<int> intTimeTable = convertTimeTable(timeTable);
    int timeTableSize = intTimeTable.size();
    queue<int> waitQueue;
    int latest = 1;

    for (int i = 0; i < numBuses; i++) {
        for (int j = 0; j < timeTableSize; j++) {
            if (intTimeTable[j] <= time) {
                if (latest < intTimeTable[j]) {
                    latest = intTimeTable[j];
                }
                waitQueue.push(intTimeTable[j]);
                intTimeTable.erase(intTimeTable.begin() + j--);
                timeTableSize--;
            }
        }
        if (waitQueue.size() < capacity) {
            if (i + 1 == numBuses) latestTime = time;
            else latestTime = time + interval - 1;
        } else {
            latestTime = latest - 1;
        }
        time += interval;
        for (int i = 0; i < capacity; i++) {
            if (waitQueue.empty()) break;
            waitQueue.pop();
        }
    }

    int hour = latestTime / 60;
    int minute = latestTime % 60;
    string hourStr = (hour < 10 ? "0" + to_string(hour) : "" + to_string(hour));
    string minuteStr = (minute < 10 ? "0" + to_string(minute) : "" + to_string(minute));
    return hourStr + ":" + minuteStr;
}
```

### Retrospect

#### Data Scope

이 문제에서 버스가 오기 시작하는 시각은 09:00이다. 그래서 09:00을 0으로 잡고 문제를 풀기 시작했었다. 그런데 버스는 9시에 처음으로 오지만 크루들은 그 이전에도 온다. 이 때문에 음수시간이 발생하게 되고, 음수값에다가 나눗셈이 들어가는 순간 내가 의도한 값이 안 나오는 결과를 초래한다.

주어진 값을 변형해서 쓸 때는 입출력 데이터가 어디부터 어디까지 올 수 있는지를 잘 고려해서 변형하도록 하자. 뻔히 00:01 같은 입력값도 있는데 굳이 음수 만들어서 고생하지 말자.



## 5. 뉴스 클러스터링 (난이도: 중)

교집합 구하는 부분만 조금 어려울 수 있고 나머지 부분은 무난하다.

### Solution

```c++
#include <iostream>
#include <vector>
#include <string>

#define COMP 65536
using namespace std;

string toLowerCase(string str) {
    int strLen = str.length();
    for (int i = 0; i < strLen; i++) {
        if (str[i] >= 'A' && str[i] <= 'Z') {
            str[i] += 'a' - 'A';
        }
    }
    return str;
}

bool isAlphabet(char c) {
    if (c < 'a' || c > 'z') return false;
    return true;
}

vector<string> makePairs(string str) {
    vector<string> pairs;
    int strLen = str.length();
    for (int i = 0; i < strLen - 1; i++) {
        if (!isAlphabet(str[i]) || !isAlphabet(str[i + 1])) continue;
        pairs.emplace_back(str.substr(i, 2));
    }
    return pairs;
}

int getIntersectionSize(vector<string> v1, vector<string> v2) {
    int interSize = 0;
    int size1 = v1.size();
    int size2 = v2.size();
    for (int i = 0; i < size1; i++) {
        for (int j = 0; j < size2; j++) {
            if (v1[i] == v2[j]) {
                interSize++;
                v1.erase(v1.begin() + i--);
                v2.erase(v2.begin() + j--);
                size1--;
                size2--;
            }
        }
    }
    return interSize;
}

int solution(string s1, string s2) {
    s1 = toLowerCase(s1);
    s2 = toLowerCase(s2);
    vector<string> pairs1 = makePairs(s1);
    vector<string> pairs2 = makePairs(s2);
    int size1 = pairs1.size();
    int size2 = pairs2.size();
    if(size1 == 0 && size2 == 0) return COMP;
    int interSize = getIntersectionSize(pairs1, pairs2);
    return interSize * COMP / (size1 + size2 - interSize);
}
```

합집합 구하는 함수는 따로 안 만들었다. 왜냐하면 $n(A \cup B) = n(A) + n(B) - n(A \cap B)$이기 때문이다.

### Retrospect

#### 다중집합의 교집합

내 풀이에서는 교집합을 구할 때 이중 for문이 돈다. 사실 그래도 된다. 문자열 길이를 1000으로 제한해뒀기 때문이다. 

다중집합의 교집합은 일반적인 교집합과 조금 다르다. 집합 내에 원소가 중복될 수 있기 때문에 한 번 교집합을 구할 때 쓰인 원소는 재활용되지 않아야 한다. 이 때문에 내 풀이에는 다음의 4줄이 들어간다.

```c++
v1.erase(v1.begin() + i--);
v2.erase(v2.begin() + j--);
size1--;
size2--;
```

별로 아름답지 못하다. 더 깔끔하게 푸는 방법을 찾아봤는데 있다. 예를 들어 $A = \{1, 1, 1, 2, 3\}$, $B = \{1, 1, 3, 3, 3\}$이라 하자. 그럼 $A \cap B = \{1, 1, 3\}$이다. $A$와 $B$는 1, 2, 3을 원소로 갖는다. 1과 2는 $B$가 더 적게 가지고 있고, 3은 $A$가 더 적게 가지고 있다. 더 적게 가진 쪽을 따라가면 된다. 

다만, 이 문제에서는 그 풀이를 쓰기는 어렵다. A, B 각각에 대해서 원소의 숫자를 세야 하는데 원소의 종류가 숫자로 한정돼있거나 하면 해쉬맵 같은 걸 쓰면 되지만, 여기서는 원소로 문자열이 들어가기 때문에 그냥 이중포문 돌리는 게 정신건강에 이롭다.



## 6. 프렌즈 4블록 (난이도: 상)

코드페스티벌이랑 비교해서 난이도 차이가 너무 심한 거 아닌가 싶다. 난이도 상이라 이제 좀 어려운거 나오겠구나 하고 긴장했는데 너무 간단하다. 그냥 [BFS](https://enhanced.kr/postviewer/593)의 하위호환 정도 된다. 푸는 방법 외우고 있으면 별 생각 없이 코드 짜면 풀린다는 뜻이다. '하위'호환인 이유는 경로의 길이가 3칸으로 정해져있어서 따로 큐를 둘 필요가 없기 때문이다.

### Solution

```c++
#include <iostream>
#include <vector>
#include <string>

#define MAX_BOARD 30

using namespace std;

int map[MAX_BOARD + 1][MAX_BOARD];
int height = 0;
int width = 0;

void convertBoardToMap(vector<string> board) {
    int height = board.size();
    int width = board[0].length();
    for (int j = 0; j < width; j++) {
        map[0][j] = -1;
    }
    for (int i = 1; i <= height; i++) {
        for (int j = 0; j < width; j++) {
            map[i][j] = board[i - 1][j];
        }
    }
}

void removeMatchedBlocks(int y, int x) {
    if (y >= height) return;
    if (x >= width - 1) return;
    int steps[3][3] = {
            {0, 1, 1},
            {1, 0, 1}
    };
    int curBlock = map[y][x];

    for (int i = 0; i < 3; i++) {
        if (map[y + steps[0][i]][x + steps[1][i]] != curBlock) return;
    }
    for (int i = 0; i < 3; i++) {
        removeMatchedBlocks(y + steps[0][i], x + steps[1][i]);
    }
    for (int i = 0; i < 3; i++) {
        map[y + steps[0][i]][x + steps[1][i]] = 0;
    }
    map[y][x] = 0;
}

int calculateScore() {
    int score = 0;
    for (int i = 1; i <= height; i++) {
        for (int j = 0; j < width; j++) {
            if (map[i][j] == 0) score++;
        }
    }
    return score;
}

void blockDown() {
    for (int j = 0; j < width; j++) {
        for (int i = height; i > 0; i--) {
            if (map[i][j] > 0) continue;
            int k = i;
            while (--k >= 1 && map[k][j] <= 0);
            map[i][j] = map[k][j];
            map[k][j] = -1;
        }
    }
}

int solution(int h, int w, vector<string> board) {
    int score = 0;
    convertBoardToMap(board);
    height = h;
    width = w;

    int cnt = 0;
    while (true) {
        for (int i = 1; i <= height - 1; i++) {
            for (int j = 0; j < width - 1; j++) {
                if (map[i][j] <= 0) continue;
                removeMatchedBlocks(i, j);
            }
        }
        int curScore = calculateScore();
        if (!curScore) break;
        score += curScore;
        blockDown();
    }

    return score;
}
```

코드는 길지만 8할이 그냥 외운거 손가락으로 출력한 것에 불과하다. 조심해야 할 부분은 blockDown 정도 되겠다. 게임판의 칸 수가 최대 900개밖에 안돼서 블록 떨어지는 부분 그냥 깊게 생각 안하고 다 떨어질 때까지 이터레이션 계속 돌리는 방식으로 구현했다.

맵에서 충원되는 블록이 없기 때문에 빈 자리를 두 종류로 구분해야 했다. 방금 막 생긴 빈자리가 있느냐 없느냐에 따라 게임종료 여부가 결정되기 때문이다.

### Retrospect

흠.. 솔직히 회고할 만한 게 없다. 다음 문제로 넘어가자.



## 7. 추석 트래픽 (난이도: 상)

문자열 파싱 문제가 하나 더 나왔다. 2번 풀고 나서 정규식 쓰는 법에 대해 공부를 좀 해놨기 때문에 그렇게 어렵지 않게 풀 수 있었다.

### Solution

```c++
#include <iostream>
#include <vector>
#include <string>
#include <regex>

using namespace std;

typedef pair<int, int> pii;

vector<pii> convertLinesToLogs(vector<string> lines) {
    vector<pii> logs = {{1, 1}};
    for (string line : lines) {
        const string logLine = line;
        regex tester("(2016-09-15 ([0-9]{2}):([0-9]{2}):([0-9]{2}).([0-9]{3}) ([0-9](.[0-9]*)?)s)");
        smatch match;
        int endTime = 0;
        int startTime = 0;
        if (regex_search(logLine.begin(), logLine.end(), match, tester)) {
            endTime =
                    stoi(match[2]) * 3600 * 1000 + stoi(match[3]) * 60 * 1000 + stoi(match[4]) * 1000 + stoi(match[5]);
            startTime = endTime - stof(match[6]) * 1000 + 1;
        }
        logs.emplace_back(pii(startTime, endTime));
    }
    return logs;
}

int solution(vector<string> lines) {
    int maxThroughput = 1;
    vector<pii> logs = convertLinesToLogs(lines);
    int numLogs = logs.size();
    for (int i = numLogs - 1; i > 0; i--) {
        int throughput = 1;
        for (int j = i - 1; j >= 0; j--) {
            if(logs[j].second >= logs[i].first - 999) {
                throughput++;
            }
        }
        if(throughput > maxThroughput) maxThroughput = throughput;
    }
    return maxThroughput;
}
```

hh:mm:ss.fff꼴은 계산하기에 영 좋지 않다. 그래서 파싱해서 그냥 숫자로 바꿔줬다. 하루는 86400초이므로 소수점 아래 셋째 자리까지 계산해야한다면 8640만까지의 숫자를 나타내야 한다. `int`는 21억 정도의 숫자까지 커버 가능하므로 얘를 쓰면 되겠다.

코드 끝자락 쯤에 나오는 저 999는 솔직히 맞는지 확실하지 않다. 어쩌면 1001이거나 1000일 수도 있다. 생각하기 귀찮아서 그냥 놔뒀다. 실전이면 세 번 제출해서 맞는걸로 낼 생각이다.

### Retrospect

이 문제는 $O(n\log n)$의 시간복잡도로도 풀 수 있다고 되어있다. 일일이 비교하지 않고 이진탐색을 이용해서 처리종료시각 - 999보다 처리시작시각이 큰 애들 갯수를 인덱스 차를 통해 구하면 될 것 같다.



## Overall Retrospect

카카오는 같은 문제라도 풀어보고 싶어지게끔 아기자기하게 만든다.

#### 시험 난이도

카카오는 뽑아만 주시면 제주도 방향으로 세 번쯤 절하고 갈 수 있을만큼 내 기준에서 좋은 회사다. 그런 것치곤 난이도가 많이 낮아서 놀랐다. 애초에 시간복잡도를 고민해야 하는 문제는 단 하나도 없고, 특수한 스킬 같은 걸 요구하지 않고 그냥 코딩할 줄 알면 풀 수 있는 문제들이었다. 물론, 채점을 해본 게 아니므로 예외케이스를 제대로 커버하지 못했을 가능성이 있지만, 1번 문제를 빼고는 난이도를 뭘 보고 가른건지 가늠하기 힘들만큼 문제들이 고만고만하고 어렵지 않았다고 느꼈다.

실전에서는 더 좋은 방법이 있을거라고 고민할 필요가 없다. 보면 죄다 입력의 크기도 작고, 7번 문제처럼 더 좋은 알고리즘이 있다 하더라도 그걸 쓸 필요가 없게 나온다. 그냥 이중 for문 써서 풀어버리면 된다. 일단 제출하고 안되면 그 때 가서 고민하도록 하자.

#### 난이도 순 배치...?

사람마다 다를 순 있겠지만, 카카오 측에서 상중하로 갈라놓은 난이도는 별로 의미가 없었다. 1번 문제는 그냥 많이 쉬웠고, 3번 ~ 7번은 난이도가 비슷했으며, 오히려 난이도 '하'가 박혀있는 2번이 신경써야 할 게 많아서 어려웠다. 나와있는 난이도와 체감난이도가 다르다고 괜히 멘탈 나갈 필요가 전혀 없는 시험이란 뜻이다.

#### 시간 부족

문제가 쉬우면 다 풀고 시간이 남아야 하는데 난 X신이라 시간이 부족했다. 5번까지 푸니까 대충 5시간이 거의 다 찼고 7문제를 모두 푸는데는 약 7시간 정도가 걸렸다. 실전에서는 뒤로 갈 수록 체력이 부족해진다는 것까지 감안하면 커트라인인 4문제도 간당간당하다.

수능 준비할 때도 맨날 시간부족이 발목을 잡았는데 코딩테스트로 넘어와도 마찬가지다. 일단 연속 5시간을 지치지 않을 체력을 기르고, 왜 시간이 그렇게 오래 걸렸는지를 잘 곱씹어 보면서 같은 삽질을 다시 하는 경우가 없도록 잘 준비해야겠다.


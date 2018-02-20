2차 테스트는 알고리즘 문제가 아니라 프로그램 하나를 구현하는 시험이었다. 애초에 문제가 자세히 공개돼있지도 않아서 따로 풀어볼 수도 없었으므로 생략한다.

3차 테스트는 1차 테스트와 비슷한 유형이다. 난이도가 확 뛸 것이라고 생각했는데 1차에 비해 크게 어려워진 느낌은 아니다. 차이가 있다면 구글링이 불가능하다는 것 정도..? 거기서 느껴지는 체감 난이도가 크리라고 생각된다.

문제 본문은 [카카오 공식 블로그](http://tech.kakao.com/2017/11/14/kakao-blind-recruitment-round-3/)를 참고하자. 



## 1. N진수 게임

흠.. 아주 이상한 코딩동아리다. 동아리를 들어갔는데 저런 게임을 하면 못 버티고 뛰쳐나와 버릴 것 같다.

### Solution

그냥 진법 변환 잘 하고, 수열 일반항 계산 잘 하면 된다. 재수를 거치면서 수열의 일반항 구하는 데는 어느 정도 일가견이 있기 때문에 어렵지 않았다.

```c++
#include <iostream>
#include <vector>

#define MAX_TURN 1000

using namespace std;

string DecToOther(int num, int radix) {
    string numStr = "";
    while(num > 0) {
        char digit;
        if(num % radix < 10) digit = num % radix + '0';
        else digit = num % radix + 'A' - 10;
        string tmp = "";
        tmp += digit;
        numStr = tmp + numStr;
        num /= radix;
    }

    return numStr;
}

string solution(int radix, int numTurns, int numMembers, int myTurn) {
    char wholeWords[MAX_TURN] = {'0'};
    string myWords = "";
    int maxTurn = (numTurns - 1) * (numMembers) + myTurn;

    int numNow = 1;
    string numNowStr = "1";
    int numLen = 1;
    int digitPointer = 0;
    int turnCount = 0;
    while (turnCount++ < maxTurn - 1) {
        wholeWords[turnCount] = numNowStr[digitPointer++];
        if (digitPointer == numLen) {
            numNow++;
            numNowStr = DecToOther(numNow, radix);
            numLen = numNowStr.length();
            digitPointer = 0;
        }
    }

    for (int i = myTurn - 1; i <= maxTurn; i += numMembers) {
        myWords += wholeWords[i];
    }

    return myWords;
}
```

### Retrospect

#### char/int/string conversion

문제 자체가 어려울 건 없다. 하지만 C++을 쓰다보니 자동형변환이 대부분 안돼서 형변환이 은근히 까다로웠다. 이에 대해서 [따로 정리](https://enhanced.kr/postviewer/1787)해보았다.

#### 코드 한 줄 빼먹는 것의 크리티컬함

코드를 한 번 완성했다가 버그 하나를 잡기 위해 일부를 수정하는 경우가 있다. 그 때 남겨야 할 라인이 누락된 경우에는 영문도 모른 채 멘탈만 날아간다. 

버그 중 최강은 오타이며, 오타 중 최강은 컴파일러가 못 잡는 오타다. 코드 한 줄이 실수로 사라지면 이건 잡을 방법이 없다. 에러가 생길 일이 없는 코드에 에러가 있는데 도저히 못 잡겠다면 실수로 일부 라인을 날린 게 아닌지 의심해봐야 한다.



## 2. 압축

### Solution

깊이 생각하지 않고 쭉 짜면 일단 다음처럼 3중 루프 코드가 나오게 된다.

```c++
#include <iostream>
#include <vector>
#include <string>

using namespace std;

vector<string> dictionary;
int dictionarySize = 0;

int searchDictionary(string word) {
    vector<string>::iterator iter;
    for(iter = dictionary.begin(); iter != dictionary.end(); iter++) {
        if(*iter == word) return distance(dictionary.begin(), iter);
    }
    return -1;
}

void initDictionary() {
    dictionary.clear();
    for(char c = 'A'; c <= 'Z'; c++) {
        string tmp = "";
        tmp += c;
        dictionary.emplace_back(tmp);
    }
    dictionarySize = 'Z' - 'A' + 1;
}

vector<int> solution (string msg) {
    vector<int> indexList;
    int msgLen = msg.length();
    initDictionary();

    int i = 0;
    while(i < msgLen) {
        int len = 0;
        int idx = 0;
        int longestWordIdx = 0;
        string word = "";
        while(idx >= 0) {
            if(i + len > msgLen) break;
            word = msg.substr(i, ++len);
            idx = searchDictionary(word);
            if(idx >= 0) longestWordIdx = idx;
        }
        dictionary.emplace_back(word);
        dictionarySize++;
        i += (len - 1);
        indexList.emplace_back(longestWordIdx + 1);
    }

    return indexList;
}
```

3중 루프가 시간복잡도를 통과하는 일은 잘 없는데 여기서도 과연 그럴지 생각해보자. 

* 루프1: 전체 문자열을 돈다.
* 루프2: 글자마다 사전을 뒤진다.
* 루프3: 루프2에서 찾는 단어가 사전에 없을 때까지 현재 글자에 다음 글자를 하나씩 붙여가며 다시 뒤진다.

루프1은 당연히 최대 1000번의 루프를 돈다. 

여기서 루프2와 루프3의 루프 수가 완전히 독립적이지 않다는 데에 주목하자. 루프2의 워스트케이스는 2글자짜리 단어부터 최대한 중복없이 사전에 들어가는 것이다. 그래야 문자열에서 건너뛰는 글자 없이 최대한 여러번 돌기 때문이다. 반면, 이렇게 됐을 때 루프 3은 매번 2번씩의 루프만 돌면 된다. 결국 루프2와 루프3의 이터레이션 수의 곱은 대략 1000 정도이다. 따라서 3중루프라 해도 시간복잡도를 통과할 가능성이 높다.

게다가 이 문제의 정답률은 95.80%다. 말인즉슨, 생각 없이 짜면 정답이라는 뜻이다.

### Retrospect

#### char/int/string conversion

여기서도 이게 쓰인다. 확실히 알아두고 넘어가자.



## 3. 파일명 정렬

~~무지가 무지 부럽다.~~ 역시 문자열 파싱 문제다. 카카오는 해설을 정렬문제인 것처럼 써놨지만 사실 정렬은 C++ STL이 알아서 다 해주고 파싱만 잘하면 된다.

### Solution

```c++
#include <iostream>
#include <vector>
#include <string>
#include <algorithm>

using namespace std;

class FileName {
public:
    string head;
    int number;
    string tail;

    FileName(string head, int number, string tail) {
        this->head = head;
        this->number = number;
        this->tail = tail;
    }
};

typedef pair<FileName *, int> pfi;

bool cmp(pfi a, pfi b) {
    if(a.first->head == b.first->head) return a.first->number < b.first->number;
    return a.first->head < b.first->head;
}

string toLowerCase(string s) {
    string lowerCaseString = "";
    int strLen = s.length();
    for (int i = 0; i < strLen; i++) {
        if (s[i] >= 'A' && s[i] <= 'Z') {
            lowerCaseString += s[i] - ('A' - 'a');
            continue;
        }
        lowerCaseString += s[i];
    }
    return lowerCaseString;
}

FileName *tokenizeFileName(string s) {
    string modifiedStr = toLowerCase(s);
    int strLen = modifiedStr.length();
    string head = "";
    string numberStr = "";
    string tail = "";
    int phase = 0;
    for (int i = 0; i < strLen; i++) {
        switch (phase) {
            case 0:
                if (modifiedStr[i] >= '0' && modifiedStr[i] <= '9') {
                    phase++;
                    numberStr += modifiedStr[i];
                    break;
                }
                head += modifiedStr[i];
                break;
            case 1:
                if (modifiedStr[i] < '0' || modifiedStr[i] > '9') {
                    phase++;
                    tail += modifiedStr[i];
                    break;
                }
                numberStr += modifiedStr[i];
                break;
            case 2:
                tail += modifiedStr[i];
                break;
        }
    }
    int number = stoi(numberStr);
    return new FileName(head, number, tail);
}

vector<string> solution(vector<string> fileNames) {
    vector<string> sortedFileNames;
    int numFiles = fileNames.size();
    vector<pfi> refinedFileNames;
    for (int i = 0; i < numFiles; i++) {
        FileName *tokenizedFileName = tokenizeFileName(fileNames[i]);
        refinedFileNames.push_back(pfi(tokenizedFileName, i));
    }

    sort(refinedFileNames.begin(), refinedFileNames.end(), cmp);

    for (int i = 0; i < numFiles; i++) {
        sortedFileNames.push_back(fileNames[refinedFileNames[i].second]);
    }
    return sortedFileNames;
}
```

문자열 파싱을 앞에서부터 해야되는데 정규식은 뒤에서부터 하기 때문에 별로 좋지 않다. 그렇게 복잡한 파싱도 아니므로 직접 하는 게 낫다.

조건 그대로 차분하게 파싱하는 게 중요하다. 문자열을 HEAD, NUMBER, TAIL로 나눈다고 했는데, TAIL은 정렬순서에 아무 영향을 미치지 않으므로 확장자 제거와 같은 전처리가 전혀 필요없다. 숫자가 여러번 나오는 경우, 첫번째 숫자만 신경쓰면 된다. 이런 조건들을 다 파악하고 나면 생각보다 문제가 어렵지 않다는 것을 알 수 있다.

잘 파싱해서 객체 벡터에 담아주고 [STL함수인 sort](https://enhanced.kr/postviewer/508)에 넘겨주면 알아서 소팅이 된다. 개꿀?

### Retrospect

#### STL<STL<STL<STL...>>>

내가 사용한 자료구조는 `vector<pair<FileName *, int>>`다. STL이 워낙 많아 뭔가 빼먹기 딱 좋다. 실제로 `cmp` 함수 짤 때 매개변수로 그냥 `FileName *` 자료형 넣어줬다가 에러 나서 한참동안 못 찾았다. STL 자료형을 복잡하게 엮어 쓸 때는 빼먹지 않도록 주의해야겠다.

#### Stable Sort

카카오 해설은 주로 이상한 포인트만 짚는 것 같다. 다음 해에 문제의 변별력이 떨어지는 것을 우려하는 것일까.. 사실 안정정렬 따위를 신경 쓸 필요가 없다. 퀵소트 같이 랜덤 들어가는 걸 쓰면서 나대지만 않으면 자동으로 안정정렬이 되기 때문이다. 참고로 `stable_sort`라는 STL 함수가 `algorithm` 헤더에 들어있으니 알아만 두자.



## 4. 방금그곡

~~1분에 음계 하나씩 누른다고..? 행위예술이냐?~~ 

처음 읽으면 처리해야 될 게 많아 보여서 엄청 복잡해보인다. 하지만 카카오문제들은 복잡한 조건에 비해 의외로 쉽게 풀리는데 그 이유는 **입력데이터의 크기가 작기 때문**이다. 이 문제도 잘 생각해보면 엄청 간단하게 만들 수 있다.

### Solution

```c++
#include <iostream>
#include <vector>
#include <regex>

using namespace std;

class MusicInfo {
public:
    string title;
    string notes;

    MusicInfo(string title, string notes) {
        this->title = title;
        this->notes = notes;
    }
};

// Replace /.#/ to single byte characters
string convertSharps(string str) {
    string converted;
    int strLen = str.length();
    char buffer = str[0];
    for (int i = 1; i < strLen; i++) {
        if(str[i] == '#') {
            buffer += 'G' - 'A' + 1;
            converted += buffer;
            continue;
        }
        converted += buffer;
        buffer = str[i];
    }
    converted += buffer;
    return converted;
}

string solution(string neoNotes, vector<string> musicInfos) {
    neoNotes = convertSharps(neoNotes);

    // Refine the raw information
    vector<MusicInfo *> refinedMusicInfos;
    vector<string>::iterator iter;
    for (iter = musicInfos.begin(); iter != musicInfos.end(); iter++) {
        const string musicInfo = *iter;
        regex tester("([0-9]{2}:[0-9]{2}),([0-9]{2}:[0-9]{2}),([A-Z]+),([A-Z#]+)");
        smatch match;
        if (regex_search(musicInfo.begin(), musicInfo.end(), match, tester)) {
            // time info
            string startStr = match[1];
            int start = stoi(startStr.substr(0, 2)) * 60 + stoi(startStr.substr(3, 2));
            string endStr = match[2];
            int end = stoi(endStr.substr(0, 2)) * 60 + stoi(endStr.substr(3, 2));
            int broadcastTime = end - start;

            // title info
            string title = match[3];

            // note info
            string wholeNotes = "";
            string notes = convertSharps(match[4]);
            int musicLength = notes.length();
            for (int i = 0; i < broadcastTime; i++) {
                wholeNotes += notes[i % musicLength];
            }

            refinedMusicInfos.push_back(new MusicInfo(title, wholeNotes));
        }
    }

    // Find the music
    vector<MusicInfo *>::iterator iter2;
    for (iter2 = refinedMusicInfos.begin(); iter2 != refinedMusicInfos.end(); iter2++) {
        MusicInfo *refinedMusicInfo = *iter2;
        if ((refinedMusicInfo->notes).find(neoNotes) != string::npos) return refinedMusicInfo->title;
    }

    return "(None)";
}
```

`MusicInfo` 클래스에는 곡명(`title`)과 음계(`notes`) 정보만 담도록 해놨다. 이유는 간단하다. 7분짜리 곡을 1시간(60분)동안 방송했다면, 전체음계는 7분짜리 곡을 8번 반복하고 음계 4번을 더 반복한 게 되기 때문이다($7 \times 8 + 4$). 이렇게 각 곡을 죽 펴놓고 `substr`을 통해 네오가 흥얼거린 음계가 포함되는지 찾으면 아주 쉽게 풀 수 있다.

주의해야 하는 부분이 하나 있는데 바로 반올림이다. 얘네는 2음절이라 문제가 생길 수 있다. 그냥 간단히 1음절짜리 다른 문자로 교체해주면 된다. 반올림 없는 원음들을 C0, D0과 같은 식으로 2음절로 표현해줘도 되겠다만 아무래도 헷갈릴 것이다.

### Retrospect

#### std::string::npos

`find`는 찾는 문자열이 없으면 -1을 뱉지 않고 `std::string::npos`를 뱉는다. 이거 int형으로 출력해보면 `unsigned int` 범위의 최대 숫자가 나온다. 아마 형변환과정에서 오버플로우가 나는 것 같다.

구글링이 허용되지 않는 시험에서 npos란 단어가 기억이 안 나면 그냥 계산기 켜서 2^32-1 구하면 된다. ㅎㅎ



## 5. 자동완성

드디어.. 시간복잡도를 고려해야 하는 문제가 나왔다. N은 10만까지, L은 100만까지 가는데 어차피 L이 길이 총합이라서 각 단어의 길이가 길어지면 N의 수가 줄어들고, N의 수가 커지면 각 단어의 길이가 줄어드는 구조다. 이중 루프로 해결하면 100만번 루프로 끝낼 수 있다.

### Trial

다른 문제들 풀던 방식으로 풀면 당연히 시간초과가 날 것이다.

```c++
#include <iostream>
#include <vector>

using namespace std;

int solution(vector<string> s) {
    int count = 0;
    int numWords = s.size();
    int goNext = false;

    for (int i = 0; i < numWords; i++) {
        vector<string> copied;
        for (int j = 0; j < numWords; j++) {
            copied.push_back(s[j]);
        }
        int sizeCopied = numWords;
        string targetWord = s[i];
        int targetWordLen = targetWord.length();
        for (int j = 0; j < targetWordLen; j++) {
            count++;
            for (int k = 0; k < sizeCopied; k++) {
                if (targetWord[j] != copied[k][j]) {
                    copied.erase(copied.begin() + k--);
                    sizeCopied--;
                }
                if (sizeCopied == 1) {
                    goNext = true;
                    break;
                }
            }
            if (goNext) {
                goNext = false;
                break;
            }
        }
    }
    return count;
}
```

### Solution

애초에 풀라고 낸 문제가 아닌 것 같다. 해설에서 트라이(Trie)라는 자료구조를 쓰라고 파워당당하게 써놨는데, 처음 보는 자료구조인데다가 어디서 자주 볼 수 있는 자료구조도 아니고, 트리구조이기까지 해서, 뭔지 알아도 이걸 시험장에서 연습해본 적 없는 채로 구현하려면 자료구조 짜는데만 2시간은 족히 걸릴 것으로 보인다. 

구글링이 가능한 시험이라면 학습능력을 보기 위해 출제했다는 식으로 핑계를 댈 수 있겠다. 하지만, 이 시험은 구글링이 불가능했다고 카카오 측에서 밝혔다. 추측컨데, 알고리즘 대회 준비하는 수준의 지원자가 있는지 확인해보고 싶어서 낸 문제가 아닌가 싶다.

하지만, 이 문제를 다른 식으로 파훼한 사람들이 상당히 많았나보다. 단어를 정렬해놓고 푸는 방법이다. 이를 통해 $O(n^2)$의 시간복잡도를 $O(n\log n) + O(n)$으로 줄일 수 있다.

```c++
#include <iostream>
#include <vector>
#include <algorithm>

using namespace std;

bool cmp(string a, string b) {
    int len = min(a.length(), b.length());
    for(int i=0; i<len; i++) {
        if(a[i] != b[i]) {
            return a[i] < b[i];
        }
    }
    return a.length() < b.length();
}

int solution(vector<string> s) {
    int totalCount = 0;
    int count = 0;
    int numWords = s.size();

    sort(s.begin(), s.end(), cmp);

    int indexCache = 0;
    string targetWord = s[0];
    int targetWordLen = targetWord.length();
    for (int j = 0; j < targetWordLen; j++) {
        count++;
        indexCache++;
        if(targetWord[j] != s[1][j]) break;
        if(j == targetWordLen - 1) indexCache++;
    }
    totalCount += count;

    for (int i = 1; i < s.size() - 1; i++) {
        int countCandidate = indexCache;
        indexCache = 0;
        count = 0;
        targetWord = s[i];
        targetWordLen = targetWord.length();
        for (int j = 0; j < targetWordLen; j++) {
            count++;
            indexCache++;
            if(targetWord[j] != s[i + 1][j]) break;
            if(j == targetWordLen - 1) indexCache++;
        }
        totalCount += max(count, countCandidate);
    }

    return totalCount + indexCache;
}
```

### Retrospect

#### 사전순 정렬

이 문제에서 보다시피 이런 류의 문제를 푸는 방식은 2가지다. Trie를 이용하거나, 풀기 전에 정렬 전처리를 해주거나.



## Overall Retrospect

3차 시험에는 아이유도 길찾기도 없었다. 그저 문자열파싱만 가득했을 뿐...

#### Closed Book Coding Test

3차 시험은 인터넷 없이 치뤄야 한다. 그러므로 잘 정리된 문서나 코드를 준비해봤자 아무 소용이 없고, 시험 직전에 다음과 같은 몇몇 문법을 외워가야 한다.

* 문자열처리
* 정규표현식
* 정렬 STL

#### Difficulty Prediction

작년 시험이 비벼볼 만한 수준이라고 해서 올해 시험도 그럴거라 생각하면 당연히 오산일 것이다. 여기엔 몇 가지 이유가 있다.

1. 카카오 공채는 작년이 첫 시행이었고 많은 이목을 끌어 모았으므로 올해 난이도는 크게 변할 수 있다.
2. 개발자 채용에 알고리즘이 도입된지가 얼마 안됐다. 즉, 사람들의 알고리즘 실력이 아주 빠른 속도로 상승하는 추세라는 뜻이다. 매년 코딩테스트의 난이도가 빠르게 상승하는 것은 당연하다.
3. 카카오 코드페스티벌도 작년보다 올해가 더 어려웠다.
   1. 작년 시험에서 시간복잡도를 고려해야 하는 문제는 3차 마지막 문제 하나였다. 근데 그걸 푼 사람들이 꽤 있었으니 그런 문제를 내도 되겠다고 생각하지 않을까?
4. 3차의 1번, 2번은 정답률이 90%를 넘었다.

그러니까 시간이 얼마 안 남았지만 시간을 최대한 아껴서 알고리즘 공부에 매진하도록 해야겠다.
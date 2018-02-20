대학교 2학년 때 KISA에서 정보보호 관련 무료강의를 신청해 들은 적이 있다. 그 때 강사님이 정규표현식 언급을 했는데 사람들이 잘 모른다는 반응을 보이자 강사님은 우리를 x신 취급하기 시작했다. 당시 난 정규표현식이라는 단어 자체를 처음 들었으므로 받았던 충격은 컸다.

지금은 정규표현식을 어떻게 쓰는지 군대에서 PHP 코딩을 하는 도중 공부한 적이 있어 대강 알고 있다. 하지만 다른 언어에서는 정규표현식을 위해 어떤 함수를 제공하는지 제대로 모른다. 그래서 이에 대해서 정리해보고자 한다.



## C++

알고리즘은 C++로 풀어야 제맛이다. 알고리즘에서 문자열 문제가 나오면 우리의 정신건강과 소중한 풀이시간을 지키기 위해 정규표현식을 쓰는 것이 좋겠다.

### 찾기

`regex_match`를 이용해서 찾는다.

```c++
#include <iostream>
#include <regex>
using namespace std;

int main() {
  string test = "2018/09/11 21:10:46.512";
  regex tester("[0-9]{4}/[0-9]{2}/[0-9]{2} [0-9]{2}:[0-9]{2}:[0-9]{2}.[0-9]{3}");
  
  if(!regex_match(test, tester)) {
    cout << "The given string is not in a date format." << endl;
  } else {
    cout << "OK" << endl;
  }
}
```

### 찾아바꾸기

찾아바꾸기는 조금 복잡하다. `smatch`와 `regex_search`를 이용한다.

```c++
#include <iostream>
#include <regex>
using namespace std;

int main() {
  const string test = "2018/09/11 21:10:46.512";
  regex tester("([0-9]{4})/([0-9]{2})/([0-9]{2}) ([0-9]{2}):([0-9]{2}):([0-9]{2}).([0-9]{3})");
  
  smatch match;
  if(regex_search(test.begin(), test.end(), match, tester)) {
    string year = match[1];
    string month = match[2];
    string date = match[3];
    string hour = match[4];
    string min = match[5];
    string sec = match[6];
    string ms = match[7];
    
    cout << "year: " << year << endl;
    cout << "sec: " << sec << "." << ms << endl;
  }
}
```

몇 가지 주의사항이 있다.

1. 타겟 문자열이 `const string`이어야 한다.
2. match의 각 원소로 담기는 값은 정규표현식에서 소괄호로 묶여 있어야 한다.



## Javascript

추후 정리 예정



## 관련 사이트

1. [https://regexr.com](https://regexr.com)

   정규표현식을 실시간으로 테스트해볼 수 있다.

2. https://regexone.com/

   정규표현식이 익숙치 않은 사람이 처음 공부하기에 좋다.
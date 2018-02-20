언어마다 문자열 파싱 라이브러리는 그 생김새가 소름끼치도록 죄다 다르다. 그래서 그냥 안 외우고 필요할 때 찾아서 쓰지만 나의 밥줄이 걸린 코딩테스트를 통과하기 위해서라면 기꺼이 외워주도록 하겠다. 

평생 안 쓸 것 같은 괴랄한 친구들은 제외하고 자주 쓸 것 같은 함수들만 정리할 것이다. 또한, C++ 스타일 외에 C 스타일 등 다양한 함수 정의를 갖고 있는 경우가 많은데 C++ 스타일만 다루려고 한다. 이 문서는 내 코딩 경험이 늘어남에 따라 계속 업데이트 될 수 있다.



## 검색

### [string::find](http://www.cplusplus.com/reference/string/string/find/)

`algorithm` 헤더에도 `find`가 있다. 조심하자.

다음과 같이 사용 가능하다.

1. `size_t find (const string& str)`

   주어진 문자열이 처음으로 등장하는 인덱스를 반환한다.

   ```c++
   string hayStack = "Cats are lovely, so are you.";
   string needle = "are";
   cout << hayStack.find(needle) << endl;	// 5
   ```

2. `size_t find (const string& str, size_t pos = 0)`

   특정 인덱스 이후로 주어진 문자열이 처음으로 등장하는 인덱스를 반환한다.

   ```c++
   string hayStack = "Cats are lovely, so are you.";
   string needle = "are";
   cout << hayStack.find(needle, 5) << endl;	// 5
   cout << hayStack.find(needle, 6) << endl;	// 20
   ```

### [string::rfind](http://www.cplusplus.com/reference/string/string/rfind/)

이번엔 뒤에서부터 찾는다. `find`와 사용법이 거의 같으므로 예제 하나 던져놓고 넘어가겠다.

```c++
string hayStack = "Cats are lovely, so are you.";
string needle = "are";
cout << hayStack.rfind("are") << endl;	// 20
cout << hayStack.rfind("are", 19) << endl;	// 5
```

### [string::find_last_of](http://www.cplusplus.com/reference/string/string/find_last_of/)

`rfind`와 비슷하다. 미묘하게 다르게 작동하긴 하는데, 아무리 생각해봐도 굳이 얘를 쓸 필요를 못 느끼겠다. 그냥 `rfind`를 쓰도록 하자.



## 분리

### [string::substr](http://www.cplusplus.com/reference/string/string/substr/)

부분 문자열을 잘라낸다.

1. `string substr (size_t pos, size_t len)`

   특정 인덱스에서부터 일정 글자 수만큼을 잘라낸다.

   ```c++
   string hayStack = "Cats are lovely, so are you.";
   string needle = "lovely";
   cout << hayStack.substr(hayStack.find(needle), needle.length()) << endl;    // "lovely"
   ```

2. `string substr (size_t pos)`

   두 번째 인자를 생략하면 끝까지 잘라낸다.

   ```c++
   string hayStack = "Cats are lovely, so are you.";
   string needle = "lovely";
   cout << hayStack.substr(hayStack.find(needle)) << endl;    // "lovely, so are you."
   ```


### split

주어진 토큰을 기준으로 문자열을 잘라 배열이나 벡터에 넣어주는 함수. [사실 그런거 없다.](http://ysonggit.github.io/coding/2014/12/16/split-a-string-using-c.html) 그래서 만들어보았다.

```c++
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
```



## 편집

### [string::replace](http://www.cplusplus.com/reference/string/string/replace/)

교체될 타겟을 잡는 방법이 `substr`과 비슷하다. 

1. `string& replace (size_t pos, size_t len, const string& str)`

   특정 인덱스에서부터 일정 글자 수만큼의 문자열을 주어진 문자열로 대체한다. 특이하게도 기존 문자열도 대체된 문자열로 바꾸고, 리턴도 한다.

   ```c++
   string hayStack = "Cats are lovely, so are you.";
   string needle = "lovely";
   string newString = "the best";
   cout << hayStack.replace(hayStack.find(needle), needle.length(), newString) << endl;
   // Cats are the best, so are you.
   cout << hayStack << endl;
   // Cats are the best, so are you.
   ```

2. `string& replace (size_t pos, size_t len, const string& str)`

   두 번째 인자를 생략할 수는 없다. 끝까지를 모두 대체하려면 다음과 같이 한다.

   ```c++
   string hayStack = "Cats are lovely, so are you.";
   string needle = "lovely";
   string newString = "lovely, so are dogs";
   cout << hayStack.replace(hayStack.find(needle), hayStack.length(), newString) << endl;
   // Cats are lovely, so are dogs.
   ```

3. `string& replace (iterator i1, iterator i2, const string& str)`

   이터레이터도 먹힌다.

   ```c++
   string hayStack = "Cats are lovely, so are you.";
   string newString = "Dogs";
   cout << hayStack.replace(hayStack.begin(), hayStack.begin() + 4, newString) << endl;
   // Dogs are lovely, so are you.
   ```

   

## 숫자 자료형과의 호환

### [std::stoi](http://www.cplusplus.com/reference/string/stoi/)

문자열 -> 정수

사용법이랄 것도 없다. `stoi("3")`하면 정수 3을 리턴한다.

### [std::to_string](http://www.cplusplus.com/reference/string/to_string/)

정수 -> 문자열

자바와는 달리 C++은 `문자열 + 정수`를 하면 concatenation이 되지 않고 에러를 뱉는다. 이 때 정수에다가 `to_string` 함수를 씌워주면 된다.

사용법이랄 것도 없다. `to_string(3)`하면 문자열 "3"을 리턴한다.


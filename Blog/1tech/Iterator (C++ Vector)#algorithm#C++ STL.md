Iterator는 OOP 언어에서 흔히 볼 수 있는 문법이다. 리스트 형태의 자료구조를 순차적으로 순회하기 위해 사용하며 종종 Iterator 객체를 따로 두지 않고 `for ... in ...`이나 `for each()` 형태의 문법을 제공하는 경우도 많다.

C++에서는 Vector에 대해 Iterator를 제공하며 다른 자료구조에 대해서도 제공을 하는지는 잘 모르겠다. vector를 사용하는 이상 Iterator를 사용하는 것은 불가피하므로 Iterator에 대해 자세히 알아보는 시간을 가지도록 하자.



## 기본 사용법

백문이 불여일견이고 talk은 cheap하다 하였으니 코드부터 보고 시작하자.

```c++
int main() {
  vector<int> v = {9, 8, 7, 6, 5, 4, 3, 2, 1};
  vector<int>::iterator iter;

  for(iter = v.begin(); iter != v.end(); iter++) {
    cout << iter.base() << ": " << *iter.base() << endl;
  }
  return 0;
}

/* 
<실행결과>
0x1191708: 9
0x119170c: 8
0x1191710: 7
0x1191714: 6
0x1191718: 5
0x119171c: 4
0x1191720: 3
0x1191724: 2
0x1191728: 1
*/
```

자세하게는 쓰지 않겠다. 궁금하면 표준문서를 보자. 이터레이터는 `vector<int>::iterator iter;`와 같이 특정 자료형에 종속되어서 선언되며, `++`와 같은 연산자가 내부에 정의되어 있어 이 연산자를 통해 순회가 가능하다. `base()` 함수를 통해 값이 저장된 주소를 얻을 수 있다.



## 사용 예

### 문자열 -> 벡터

```c++
int main() {
  string str = "abcd";
  vector<char> vec(str.begin(), str.end());
  cout << vec[0] << vec.size() << endl;
  return 0;
}
```



### 벡터 -> 문자열

문자열을 벡터로 만드는 것과 똑같다. ~~조립은 분해의 역순이다.~~

```c++
int main() {
  vector<char> vec = {'a', 'b', 'c', 'd'};
  string str(vec.begin(), vec.end());
  cout << str << endl;
  return 0;
}
```

참고로 `vector<int>`에다가 돌리면 원하는대로 안 나온다. 



### unordered_map.find(const key_type& k)

해쉬맵에서 키가 존재하는지 확인하는 함수다. 직관적으로 boolean을 반환할 것 같이 생겼지만 사실 iterator를 반환한다.

```c++
int main() {
	unordered_map<char, int> map;
	for (int i = 0; i < stringSize; i++) {
    if(map.find(v[i]) != map.end())	// There is no key 'v[i]'
      cout << "fail" << endl;
    else
      cout << map[v[i]] << endl;
	}

  return 0;
}
```

`find()`를 호출하면 iterator가 자동으로 도는데, 만약 리턴된 값이 `end()`에 도달해있다면 key를 찾지 못한거다. 왜 굳이 저런 식으로 짜야 했는가에 대한 의문이 드는 부분이다.


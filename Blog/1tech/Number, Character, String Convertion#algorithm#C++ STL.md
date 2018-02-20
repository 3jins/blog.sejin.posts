자바스크립트, 파이썬 등의 인터프리터 언어는 명시적으로 자료형을 정의하지 않기 때문에 묵시적 형변환이 아주 잘 되어있다. 자바 역시 묵시적 형변환이 아주 자연스럽게 이루어진다. 반면, C++은 그렇지 않다. 쭉 정리해보도록 하자.



## Conversions

### Character -> Number

일단 얘가 기본이다. char형의 숫자를 숫자로 바꾸는 방법은 매우 쉽다.

```c++
char numChar = '9';
int convertedNumChar = numChar - '0';
cout << typeid(convertedNumChar).name() << endl;
```

char형은 아스키값으로 매핑되어있기 때문에 가장 작은 한자리 숫자를 빼주면 그대로 int가 된다.

### Number/Character -> String 

`string to_string(숫자)` 하나면 다 처리된다.

```c++
int num = 123;
char numChar = '9';
string convertedNum = to_string(num);
string convertedNumChar = to_string(numChar - '0');
cout << convertedNum << ": " << typeid(convertedNum).name() << endl;
cout << convertedNumChar << ": " << typeid(convertedNumChar).name() << endl;
```

### Number -> Character

약간 꼼수 같긴 하지만, 문자열로 변경한 뒤에 문자를 뽑아내면 된다.

```c++
int num = 9;
char convertedNum = to_string(num)[0];
cout << convertedNum << ": " << typeid(convertedNum).name() << endl;
```

### String -> Number/Character

character로 바꾸는건 number로 바꾼 다음에 다시 바꿔주면 그만이므로 number로 바꾸는 것만 가능하면 된다.

깊이 생각할 필요가 없다. `stoi`가 알아서 해준다.

```c++
string str = "123";
int num = 321;
cout << stoi(str) + num << endl;
```



## Conclusion

> 두려움은 환상이다.
>
> \- Michael Jeffrey Jordan

모를 때는 형변환이 나올 때마다 멘탈에 타격을 입어왔다. 하지만 알고보니 외워야 할 건 딱 2개다.

* `to_string`
* `stoi`

이제 두려워하지 말자.


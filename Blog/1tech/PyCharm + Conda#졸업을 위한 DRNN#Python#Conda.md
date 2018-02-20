[졸업을 위한 DRNN](https://enhanced.kr/postviewer/1551) 시리즈의 글입니다.

파이참에서 콘다 가상환경을 그대로 가져와 쓰려고 한다. 하지만 파이참은 `import numpy` 하나 밖에 안했는데 다음과 같은 에러를 뿜뿜한다.

> Traceback (most recent call last):
>
>  File "C:\Users\jinai\Miniconda3\envs\TEST\lib\site-packages\numpy\core\__init__.py", line 16, in \<module\>
>
>   from . import multiarray
>
> ImportError: DLL load failed: 지정된 모듈을 찾을 수 없습니다.

이것 때문에 얼마나 삽질을 했는지 모른다. 구글링을 해봐도 이런 증상을 겪는 사람은 좀 있는데 [답변은 인터프리터 안 바꿔줬냐는 것 밖에 없었다](https://stackoverflow.com/questions/50440391/importing-pytorch-in-pycharm-using-anaconda)(심지어 8할은 진짜로 인터프리터 안 바꿔서 생긴 문제였다). 

원래 코딩은 뉴비에게는 환경설정이 제일 재미없고 어려운 법. 결국 해결책을 찾아냈으니 나와 같은 문제로 삽질을 하는 사람들이 있다면 고생하지 않을 수 있도록 방법을 공유하고자 한다.



## 결론

두괄식으로 결론부터 뱉어놓겠다. Conda에서 가상환경을 만든 뒤 파이참에서 그 환경을 불러오면 제대로 로딩되지 않는다. **파이참에서 Conda 가상환경을 만들어야 한다.**

1. `Ctrl + Alt + s`를 눌러 설정 창을 열고 왼쪽 메뉴에서 'Project > Project Interpreter'를 선택하자. 상단 인터프리터 오른쪽 톱니바퀴 버튼을 누르고 'Add...' 버튼을 눌러주도록 하자.

   <img src="https://raw.githubusercontent.com/3jins/Images/master/pycharm-conda-settings-1.png" width="100%"/>

2. 왼쪽에서 'Conda Environment'를 누르고, 'Existing environment' 말고, 'New environment'를 누르자. 

   **Location** 값은 Conda가 가상환경들을 관리하는 위치다. 어딘지 잘 모르겠으면 Conda Prompt에서 `conda info` 쳐보고 active env location 부분 보고 따라치면 된다.

   **Python version**은 특별한 이유 없으면 최신버전으로 한다. 특별한 이유 있으면 다른 버전 고르자.

   **Conda executable**은 보통 기본값이 이상한 데 가리키고 있어서 'Conda executable not found' 같은거 뜰텐데 알아서 conda.exe 어딨는지 찾아서 넣어주자. 보통 `(홈경로)\Miniconda3\Scripts\conda.exe`나 `(홈경로)\Anaconda3\Scripts\conda.exe`로 잡아주면 될거다.

   이제 OK 버튼을 눌러주자.

   <img src="https://raw.githubusercontent.com/3jins/Images/master/pycharm-conda-settings-2.png" width="100%"/>

3. 필요한 패키지는 Conda Prompt를 켜서 [이전 포스트에 나와있는 대](https://enhanced.kr/postviewer/1598)로 `npm install numpy` 등의 명령으로 차근차근 설치해주면 된다.

   | <img src="https://raw.githubusercontent.com/3jins/Images/master/pycharm-conda-settings-3.png" width="100%"/> |
   | :----------------------------------------------------------: |
   |                             짠!                              |



## 왜 불러오는 것은 안 되는가

에러메세지를 다시 한 번 보도록 하자.

> Traceback (most recent call last):
>
>  File "C:\Users\jinai\Miniconda3\envs\TEST\lib\site-packages\numpy\core\__init__.py", line 16, in \<module\>
>
>   from . import multiarray
>
> ImportError: DLL load failed: 지정된 모듈을 찾을 수 없습니다.

지정된 DLL 파일을 못 찾아온다고 한다. ~~그렇다, Windows에서 Python을 쓸 때만 생기는 문제다. 돈을 모아 맥북을 사면 모든 것이 해결된다.~~ 윈도우에서 Python 인터프리터를 사용하기 위해서는 Visual C++이 필요하다. [파이썬 위키](https://wiki.python.org/moin/WindowsCompilers#Which_Microsoft_Visual_C.2B-.2B-_compiler_to_use_with_a_specific_Python_version_.3F)에 따르면, 다음과 같이 각 파이썬의 버전별로 필요한 Visual C++ 컴파일러의 버전이 다르다.

| **Visual C++** |       **CPython**       |
| :------------: | :---------------------: |
|      14.0      |        3.5, 3.6         |
|      10.0      |        3.3, 3.4         |
|      9.0       | 2.6, 2.7, 3.0, 3.1, 3.2 |

Conda에서 불러왔던 파이썬 인터프리터는 3.7이다. 느낌이 쎄하지 않는가? Visual C++의 최신 버전이 필요한 것이다. 파이참에서 직접 가상환경을 만들 때는 시스템에 설치돼있던 파이썬의 버전이 3.6이었고, 가상환경의 파이썬 역시 3.6으로 만들어져서 괜찮았던 것이다.

즉, 위에서 내가 언급한 해결방법은 일종의 꼼수지만 어쨌든 무조건 문제를 해결할 수 있는 방법이다.



## 교훈

윈도우에서는 시스템에 설치돼있는 파이썬 인터프리터보다 버전이 높은 인터프리터를 쓰면 안 된다.


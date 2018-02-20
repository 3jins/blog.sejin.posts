[졸업을 위한 DRNN](https://enhanced.kr/postviewer/1551) 시리즈의 글입니다.

교수님께서는 졸업작품을 진행하는 데 있어서는 라이브러리를 직접 뜯어고쳐야 할 일이 많기 때문에 텐서플로우보다 PyTorch가 더 나을거라고 하셨다. 그럼 PyTorch를 설치해봐야 하겠다. 



## 개발환경

내 PC의 파이썬 개발환경은 다음과 같다.

* Intel Core i5-7200U
* Windows 10
* Python 3.6 (3.7이 나와있지만 업그레이드하려면 지우고 다시 깔아야 한대서 그냥 놔두기로 했다)



## 가상환경 구축

**Anaconda나 Miniconda 환경에서 PyTorch를 쓸 거라면 이 부분은 필요하지 않습니다.**

파이썬은 패키지들이 서로 꼬이는 것을 방지하기 위해 `virtualenv`를 이용해서 프로젝트마다 가상환경을 만들고 필요한 패키지를 설치한다. 

보통 프로젝트 폴더 내에서 `virtualenv env` 명령어로 가상환경을 위한 폴더인 `env`를 만들어준다. `env` 안에는 윈도우의 경우 `Scripts`, 맥이나 리눅스의 경우 `bin` 디렉토리가 있을텐데, 그 안에 패키지들이 설치된다. 앞으로, 이 가상환경에 패키지를 설치할 때는 `프로젝트폴더/env/Scripts/pip3.exe`를 실행시켜야 한다.

activate

저 깊은 곳에 쳐박혀있는 pip3를 실행시켜야 한다니 벌써 손가락이 저려온다. 다행히도 파이썬에서는 `activate`라는 기능을 통해 환경변수를 일시적으로 조작할 수 있다. 윈도우의 경우 `call env\Scripts\activate`를, 맥이나 리눅스의 경우 `source env/bin/activate`를 실행해주면 된다. 사실, 윈도우의 경우라도 Windows10에서는 bash shell을 제공하므로 bash shell을 사용한다면 source가 그대로 먹힌다.

#### PyTorch 설치

이제 가상환경도 구축되었겠다, 본격적으로 PyTorch를 설치해보도록 하자. [PyTorch 공식 홈페이지](https://pytorch.org/)에 들어가보면 다음 스크린샷처럼 친절하게 환경별로 사용해야 하는 명령어를 알려준다.

<img src="https://raw.githubusercontent.com/3jins/Images/master/pytorch-get-started.png" width="100%"/>

'오, 개꿀'하고 홈페이지에서 알려주는 첫 번째 명령어를 입력하는 순간, 나는 새빨간 에러와 마주하게 되었다.

```
pip3 install http://download.pytorch.org/whl/cpu/torch-0.4.1-cp36-cp36m-win_amd64.whl
```

> torch-0.4.1-cp36-cp36m-win_amd64.whl is not a supported wheel on this platform.

처음엔 뭔 개소린가 싶었지만, 나는 곧 깨닫게 되었다. [PyTorch는 x86 아키텍처 위에서 동작하는 윈도우를 지원할 생각이 없다는 것을](https://pytorch.org/previous-versions/).

#### 

## Conda

사실 콘다 사용법 제대로 알아보기 귀찮아서 그냥 생으로 설치하려고 한건데 아무래도 내가 콘다 사용법 알아보는 것보다는 파이토치가 x86 윈도우 지원하는 게 더 귀찮았나보다. 

[Conca 문서](https://conda.io/docs/)에 보면 Conda를 다음과 같이 정의하고 있다.

> Conda is an open source package management system and environment management system that runs on Windows, macOS and Linux.

즉, Conda는 가상환경 및 패키지 관리도구다. pip + virtualenv랑 다를 게 없다는 뜻이다. 그럼 이걸 왜 쓸까? 스택 오버플로우 질문 중에 [이에 대해 묻는 질문](https://stackoverflow.com/questions/20994716/what-is-the-difference-between-pip-and-conda)이 있다. 해당 글에 따르면 pip는 파이썬 패키지만 설치할 수 있는 반면, Conda는 HDF5, MKL, LLVM 등의 과학 관련 non-Python 라이브러리를 설치할 수 있어서 둘의 용도가 좀 다르다고 한다. 어쨌든 확실한 건 **파이썬으로 수학을 할 거면 Conda를 쓰는 게 속 편하다**는 거다. 실제로 pip로 scipy 같은 거 설치하려고 하면 뭔가 꼬이면서 잘 설치가 안된다.

Anaconda vs Miniconda

아나콘다와 미니콘다 두 가지가 있다. 'Anaconda = Miniconda + 150여가지의 미리 설치된 패키지'라고 생각하면 된다. 둘 중 뭘 쓰든 상관없지만 아나콘다를 설치하면 설치시간이 어마어마하게 길고 용량을 3GB 이상 잡아먹는다는 것은 알아둬야 할 것이다.

Conda의 설치가 끝났으면 먼저 딴 짓 하기 전에 이것부터 해주자. [패키지간 의존성 오류를 방지할 수 있다](https://gzupark.github.io/articles/Why-Anaconda-How-to-control-Anaconda/#3-anaconda-%EC%84%A4%EC%B9%98)고 한다.

```
> conda upgrade conda
> conda upgrade --all
```

### 가상환경 

* 만들기 (`conda create`)

  다음과 같은 명령어를 쳐주면 `drnn-test`란 이름의 가상환경 디렉토리를 만들고 그 안에 `numpy`와 `scipy` 패키지를 설치해준다. 여기서 주의할 점은 `virtualenv`와는 달리 프로젝트 폴더 내에 가상환경 폴더가 위치하는 게 아니라는 점이다. 그러므로 그냥 가상환경 이름으로 프로젝트 이름을 써주면  되겠다. 

  ```
  conda create -n drnn-test numpy scipy
  ```

* 실행 (`activate`) 

  윈도우의 경우 `activate drnn-test`, 맥/리눅스는 `source activate drnn-test` 해주면 된다. 

* export(`conda env export`) / import(`conda env create`)

  현재 실행하고 있는 가상환경을 `.yaml` 파일으로 내보내고, `.yaml` 파일을 가지고 가상환경을 만들 수 있다. Docker의 image와 비슷한 개념이다.

### 패키지 관리

`conda install`, `conda update`, `conda remove` 등의 명령어를 쓸 수 있다.



## Conda 환경에 PyTorch 설치

이제 환경설정이 다 됐으니 다시 PyTorch 공식 홈페이지에서 시키는대로 PyTorch 설치를 해보자.

```
(base) > conda activate drnn-test
(drnn-test) > conda install pytorch-cpu -c pytorch 
(drnn-test) > pip3 install torchvision
```

`torchvision` 설치할 때 다음과 같은 에러를 만날 수도 있다.

> Command "python setup.py egg_info" failed with error code 1 in 경로

그럼 pip를 업데이트한 뒤 다시 torchvision을 설치하면 된다.

```
python -m pip install --upgrade pip
```

설치가 잘 됐는지 확인하기 위해 다음처럼 명령을 입력해보자.

```
(drnn-test) > python
Python 3.7.0 (default, Jun 28 2018, 08:04:48) [MSC v.1912 64 bit (AMD64)] :: Anaconda, Inc. on win32
Type "help", "copyright", "credits" or "license" for more information.
>>> import torch
>>>
```

`import torch`를 했는데 아무 에러 없이 파이썬 쉘이 떨어진다면 설치가 잘 된 것이다.
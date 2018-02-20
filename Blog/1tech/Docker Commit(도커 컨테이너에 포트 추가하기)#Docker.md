도커를 쓰다 보면 컨테이너에 새로운 포트를 추가로 오픈해야 하는 경우가 생긴다. 하지만, 도커의 컨테이너는 가동 중에 새로운 포트를 열 수 없게 되어있다. 그럼 매번 포트를 추가할 때마다 컨테이너를 새로 만들어야 하느냐면 맞다. 음... 그럼 포트를 추가할 때마다 기존 컨테이너에 설치한 패키지, 만들어둔 설정 등을 일일이 다시 깔고 설정해야 하느냐면, 다행히도 그렇지는 않다. 지금부터 그 방법에 대해 알아보도록 하자.



## commit

도커의 `commit` 명령은 **현재 컨테이너를 이미지로 박제시키는 명령**이다. 이것만 들어도 감이 오지 않는가? 우리는 기존 컨테이너에 포트를 추가하기 위해서

  1. `commit` 명령으로 현재 컨테이너의 상태를 이미지로 박제하고
  2. 현재 컨테이너를 삭제한 후
  3. 박제된 이미지를 다시 `run`하면서 포트를 추가할 것이다.



## Adding a new port to the existing container

필자가 가정한 상황은 다음과 같다.

* 기존의 컨테이너는 80번 포트가 열려있고, Host OS의 c:\Users\sejin\yahoyaho와 container의 /sejin/yahoyaho가 연결되어 있다. 데몬을 돌리는 컨테이너는 아니고, bash를 이용해 사용자입력을 받는 컨테이너다.
* 여기에 443번 포트를 추가하고자 한다.
* 기존의 컨테이너 이름은 existing-container이다.

그럼 다음처럼 명령들을 실행하면 깔끔하게 포트가 추가된다.

```bash
> docker stop existing-container
> docker commit existing-container stuffed-image
> docker rm existing-container
> docker run -it -p 80:80 -p 443:443 --name existing-container stuffed-image /bin/bash
```


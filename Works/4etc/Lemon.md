프로젝트명에서부터 느껴지듯이 멜론을 모티브로 제작한 스트리밍 음악 재생 서비스입니다.

| <img src="https://raw.githubusercontent.com/42deSix/Images/master/melon_lemon.png" width="35%"/> |
| :----------------------------------------------------------: |
|                        Melon? Lemon!                         |

시연 영상은 다음 링크에서 확인해보실 수 있습니다.

[https://drive.google.com/open?id=1GOJzhsB4Q0dFeJILUgxldb4TrjrsTDNk](https://drive.google.com/open?id=1GOJzhsB4Q0dFeJILUgxldb4TrjrsTDNk)



### 기술명세

*   개발기간: 2014년 8월 ~ 2014년 11월
*   기술스택:
    *   서버: Ubuntu 14.04, Apache 2.4.7, mysql 5.5.38, C, fmod
    *   클라이언트: Java





### 개발배경

1학년 때 만들었던 [JLPlayer](http://enhanced.kr/postviewer/46)에 대해 약간 아쉬움이 남아서 네트워크 프로그래밍 수업에서 진행한 개인 프로젝트입니다. TCP 소켓을 통해 클라이언트와 서버가 통신하는 구조를 갖고 있습니다.



### 의의 및 아쉬웠던 점

2학년 때 3학년 2학기 수업을 당겨 들었다 보니 분산/비동기 처리는 물론 뮤텍스/세마포어에 대한 이해조차 부족했습니다. 그래서 다중 클라이언트 핸들링이 제대로 이루어지지 않았고 둘 이상의 클라이언트가 동시에 재생요청을 해올 경우 서버가 죽어버리는 문제가 있었습니다. 이 점을 제대로 처리하지 못한 점이 아쉽습니다.

하지만 제대로 서버-클라이언트 구조를 만들어봤다는 점, 라이브러리를 이해하고 사용해봤다는 점, DBMS와 연동해봤다는 점 등에서 이후의 프로젝트를 진행하는 데에 큰 도움이 되는 경험이었습니다.





RTP
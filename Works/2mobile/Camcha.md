LED가 부착된 애드온 하드웨어를 활용해서 빛을 발산하고 스마트폰 카메라와 OpenCV를 이용하여 렌즈에 반사된 빛을 탐지한 뒤 이 결과를 경찰 신고, 탐지위치 공유 등 여러 서비스에 연결해주는 스마트폰 어플리케이션입니다. 자세한 정보를 알고 싶으신 분들을 위해 [최종평가 발표자료](https://drive.google.com/open?id=1Lli8qAUYVjcNjn2j3rSU3c2yb_1Fen3a)를 첨부하겠습니다. 소스코드는 [깃헙 레포지터리](https://github.com/SoftMilkTea)에 올라와 있습니다.



### 기술명세

- 개발기간: 2017년 9월 ~ 2017년 11월
- 기술스택
  - Android(Java), OpenCV(C++), Django(Python3), Arduino 



### 개발배경

소프트웨어 마에스트로 8기 과정에서 제작한 몰래카메라 탐지 어플리케이션입니다. 기획 단계에서 팀원들과 사회에 기여할 수 있는 공익적인 작품을 만들기로 합의하여 많은 조사와 멘토링 끝에 선정한 주제였습니다. 기존 시장에 [몰카탐지기](https://drive.google.com/open?id=1eqj86hSHmuAwcDD82RvgwPc0c2xFMVwo)가 시판되고 있으며 실제로 대학교 학생회 등에서 이를 구매하여 탐지활동을 벌이고 있지만 개인이 소지하기에는 가격, 휴대성, 정확도 면에서 매우 부적합하다는 점에 주목했습니다. 



### 의의 및 아쉬웠던 점

~~어째 안드로이드 프로젝트는 할 때마다 미완성으로 끝나는 것 같기는 한데~~ 프로젝트를 끝마치지 못했습니다. 프로젝트가 잘 안됐던 게 아니라 다른 외적 요인으로 인해서 그만두기로 팀원들끼리 합의했으며 이에 대한 내용은 [여기](http://enhanced.kr/postviewer/6)에서 보실 수 있습니다.

이외에도 프로젝트에 DI 등의 패턴을 적용해보거나 여러 다양한 프레임워크/라이브러리를 써볼 수도 있는데 그러지 못한 점, 안드로이드 스튜디오에서 기본으로 Gradle이라는 갓갓한 툴을 제공해줌에도 불구하고 Gradle에 대해 제대로 공부해보지 않고 프로젝트를 진행했던 점, 팀원들끼리 코드 리뷰가 거의 전무했던 점, 배권한 멘토님이 강조하신 TDD를 개발 프로세스에 끝내 적용하지 못했던 점 등 많은 아쉬움이 남습니다.

아쉬움도 많았지만 사실 얻은 것이 훨씬 많습니다. 

우선 캠차 프로젝트를 진행하면서 협업 및 의사소통에 대해 많은 것을 배웠습니다. [Roostory](http://enhanced.kr/postviewer/213)에서 제대로 다루지 못했던 깃에 대해 잘 다루지는 못해도 제대로 된 이해를 하게 되었습니다. 코드의 품질이 기존에 제가 생각했던 것에 비해 훨씬 중요하다는 것도 깨달았고 Trello를 이용해서 팀원간 진행상황을 공유하고 일정관리를 하는 법도 배웠습니다. 그리고 팀원끼리 의사소통을 충분히 자주 하는 것이 프로젝트 진행에 있어서 얼마나 중요한가에 대해서도 직접 사소한 것까지 서로 계속해서 공유해보며 알게 되었습니다.

 NDK와 JNI를 이용해서 다른 언어로 쓰여진 코드를 안드로이드 프로젝트에 삽입하는 방법, 끝내 OpenCV 포팅 문제 때문에 적용하지는 못했지만 [얕게나마 공부한 Kotlin](https://dunnotodono.blogspot.kr/2017/10/kotlin-opencv.html), 팀원 형의 어깨너머로 구경한 아두이노를 이용해서 회로를 구성하고 코딩하는 방법 등 기술적인 성장 역시 어느 정도 있었습니다.

그리고 문서나 발표자료를 어떤 식으로 작성해야 고생한 것에 비해 그 노력이 평가절하되지 않을 수 있는가에 대해서도 많은 고민을 했던 프로젝트입니다.

완성했더라면 더 좋았겠지만 충분히 많은 것을 얻었고 앞으로 성장하기 위해 무엇에 주목해야 하는가에 대한 안목을 갖게 해준 프로젝트이기에 결과에 상관 없이 후회가 남지 않는 작품입니다.
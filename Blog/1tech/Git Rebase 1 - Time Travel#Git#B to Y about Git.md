[B to Y about Git](https://enhanced.kr/postviewer/629) 시리즈의 글입니다.

[소프트웨어 마에스트로](http://enhanced.kr/postviewer/6) 과정 초기에 송태웅 개발자님의 Git 사용법 강의가 있었다. 그 때 송태웅 개발자님은 우리에게 이런 말씀을 해주셨다.

> rebase를 제대로 쓸 수 있으면 나름 Git 좀 쓴다고 해도 됩니다.

당시 나는 애드커밋푸시도 제대로 못해서 에베베하던 뉴비였기 때문에 rebase에 대해 제대로 이해하지 못했지만 지금은 애드커밋푸시 정도는 할 줄 아는 뉴비로 진화했기 때문에 rebase 사용법을 각 잡고 제대로 공부해보고자 한다.



## Rebase의 개념

### Re(construct the )base

rebase는 본질적인 개념은 이름 그대로 지금까지 쌓아둔 커밋(base)을 재구성하는 것이다. 깃에서 제공하는 기능 그대로 그 재구성의 과정을 따라가보면 다음 그림과 같이 3개의 단계로 이루어진다.

<img src="https://raw.githubusercontent.com/3jins/Images/master/rebase_essense.png" width="90%"/>

1. 기존 커밋베이스에서 수정하고 싶은 커밋 이후의 모든 커밋을 들어내어 pick 상태로 만든다.
2. pick된 커밋 중 일부를 수정하거나, 합치거나, 새로운 커밋을 추가하는 등 변경 작업을 가한다.
3. 들어낸 커밋들을 다시 쌓는다.

### Time Travel

흔히 위의 방식으로 Rebase를 많이 설명한다. 하지만 내가 보기엔 시간여행이라는 관점에서 리베이스를 보는 게 더 직관적이라 이 방법으로도 다시 설명해보겠다. 

좀 옛날 영화긴 하지만 '나비효과'라고 일기를 통해 과거로 돌아가는 초능력을 가진 주인공이 나오는 영화가 있었다. Rebase는 그런 능력을 우리에게 제공한다. 커밋에 기록이 남아있는 과거의 특정시점으로 되돌아가서 그 시점부터 지금까지의 커밋들 중 필요한 것들을 차례로 수정하면서 현재로 돌아올 수 있다. 그리고 영화 내용처럼 과거의 일을 수정하는 과정에서 우리는 예기치 못한 일과 마주치게 될 수도 있다. 

| <img src="https://raw.githubusercontent.com/3jins/Images/master/butterfly_effect_diary.gif" width="80%"/> |
| :----------------------------------------------------------: |
| 영화 '나비효과(2004)'의 주인공은 일기에 기록된 시점으로 시간을 되돌릴 수 있다. |





## How to Use 

아무 레포나 들어가서 `git rebase -i head`라고 쳐보자. 그럼 다음과 같은 설명을 볼 수 있다.

```shell
# Rebase 45f0785..45f0785 onto 45f0785 (1 command(s))
#
# Commands:
# p, pick = use commit
# r, reword = use commit, but edit the commit message
# e, edit = use commit, but stop for amending
# s, squash = use commit, but meld into previous commit
# f, fixup = like "squash", but discard this commit's log message
# x, exec = run command (the rest of the line) using shell
# d, drop = remove commit
#
# These lines can be re-ordered; they are executed from top to bottom.
#
# If you remove a line here THAT COMMIT WILL BE LOST.
#
# However, if you remove everything, the rebase will be aborted.
#
# Note that empty commits are commented out
```

pick이 뭐고 edit이 뭐고 squash가 뭔지 설명이 다 나와있지만, 저 정도 읽어서는 이해하기 힘들다. 자세한 상황과 함께 살펴보도록 하자.



### 0. Basic Process

Rebase 명령은 다음과 같은 순서로 이루어진다. 

1. **과거로 이동**

   수정할 커밋 이전의 커밋해시값을 적당한 길이만큼 잘라넣어 rebase 시킨다. 

   ```bash
   git rebase -i 9c96e
   ```

   또는, 마지막 n개의 commit을 수정하려고 한다면 다음처럼 `HEAD~n`을 인자로 넘겨도 된다.

   ```bash
   git rebase -i HEAD~4
   ```

   `-i` 옵션은 interactive의 약자로, 2번 단계와 같은 방식으로 명령을 전달하겠다는 뜻이다.

2. **현재로 돌아오면서 들릴 커밋들 선택**

   다음처럼 선택된 모든 커밋들이 디폴트로 pick된 상태로 vim 에디터에 나타난다.

   ```shell
   pick 5cdce9d Nice commit
   pick badf00d Defective commit
   pick c0ffee9 Coffee commit
   pick 60dc0de Last commit
   
   # Rebase 9c96ef9..5cdce9d onto 9c96ef9 (4 command(s))
   ```

   이 중 변경하고 싶은 커밋들을 다음처럼 수정해준 뒤 저장해주면 된다.

   ```shell
   pick 5cdce9d Nice commit
   edit badf00d Defective commit
   pick c0ffee9 Coffee commit
   pick 60dc0de Last commit
   
   # Rebase 9c96ef9..5cdce9d onto 9c96ef9 (4 command(s))
   ```

3. **현재로 되돌아오면서 선택한 커밋들 수정**

   쭉 돌아오면서 선택된 커밋들을 한 군데씩 들리게 된다. 들릴 때마다 무언가 작업을 해주고

   ```bash
   무언가 작업을 한다.
   ```

   작업이 다 끝났으면 continue 옵션을 통해 다음 커밋으로 넘어가준다.

   ```bash
   git rebase --continue
   ```

   더 이상 선택된 커밋이 없다면 리베이스 여행을 무사히 마치고 현시점으로 돌아오게 된다.



### 1. Pick

해당 커밋을 그대로 주워서 쓰겠다는 뜻이다. 되돌아간 시점의 커밋부터 last commit까지의 모든 커밋들이 나야나 댄스를 추고 있고 당신에게 선택받은 커밋들만이 무사히 살아남아 커밋이력에 들어간다. pick되지 않은 커밋들은 edit으로 성형되어 들어가거나 squash되어 합쳐지거나 심지어 커밋이력에서 빠질수도 있다.



### 2. Reword

continue시 쭉 지나가다가 Reword 명령이 붙은 커밋에 도달하면 빠르게 커밋메세지만 수정하고 넘어간다.



### 3. Edit

Edit 명령이 붙은 커밋에 도달하면 온갖 종류의 수정작업을 다 할 수 있다. 코드를 고쳐도 된다. 하지만 잘못 고쳤다가는 continue 도중 충돌이 일어날 수 있다.



### 4. Squash

둘 이상의 커밋을 하나로 합친다. 사용방법은 예를 들어 설명하는 게 좋겠다.

```shell
pick 5cdce9d Nice commit
squash badf00d Defective commit
squash c0ffee9 Coffee commit
pick 60dc0de Last commit
pick babed06 I love puppies
squash babeca7 I also love kittens
pick 60dc0de Last commit

# Rebase 9c96ef9..5cdce9d onto 9c96ef9 (4 command(s))
```

위와 같이 세개의 커밋을 squash로 고쳤다고 치자. 그럼 `c0ffee9 Coffee commit`과 `badfood Defective commit`은 `5cdce9d Nice commit`으로 합쳐지고 `babeca7 I also love kittens`는 `babed06 I love puppies`로 합쳐진다. 물론, rebase 과정에서 커밋메세지 또한 수정할 수 있다. 



### 5. Fixup

Squash의 하위호환이다. 단, 커밋메세지 수정 없이 그냥 바로 합쳐진다. 



### 6. Exec

이전까지의 명령어들은 모두 commit 앞에 붙어있던 pick을 수정해서 전달했지만, `exec`은 commit 사이에 라인을 추가해서 사용한다.

```bash
exec echo Rebase start
pick 5cdce9d Nice commit
exec git rev-parse HEAD >> rebase-log.txt
pick badf00d Defective commit
exec git rev-parse HEAD >> rebase-log.txt
pick c0ffee9 Coffee commit
exec git rev-parse HEAD >> rebase-log.txt
pick 60dc0de Last commit
exec echo Rebase end

# Rebase 9c96ef9..5cdce9d onto 9c96ef9 (4 command(s))
```

위와 같이 커밋 사이사이에 exec 명령을 이용해 쉘명령을 실행할 수 있다. 참고로 `git rev-parse` 명령은 인자로 전달받은 HEAD가 가리키는 커밋의 해시값을 출력한다.



### 7. Drop

해당 커밋을 삭제한다. drop 명령으로 삭제해도 되고 그냥 그 라인 자체를 날려버려도 된다.





## 시간여행의 패러독스

Rebase는 과거로 돌아가는 시간여행이다. 과거로의 시간여행이 불가능함을 지적하는 '시간여행의 패러독스'라는 유명한 개념이 존재한다. 만약, 내가 2000년으로 돌아가서 어린 날의 내 손목 하나를 잘랐다고 해보자. 현재로 돌아왔을 때 내 손목은, 아니, 내 삶은 과연 어떻게 변해 있는 것이 자연스러울까? 분명 난 그 손목을 가지고 한 일들이 많았을 것이고 그 결과가 쌓여서 현재에 영향을 미쳤을 것이다. 그 모든 일들은 다 현재에 어떤 식으로 반영돼있을지 예측이나 할 수 있겠는가?

다시 Git으로 돌아와서, 비슷하게 다음과 같은 상황을 가정해보자.

<img src="https://raw.githubusercontent.com/3jins/Images/master/rebase_time_travel_1.png" width="90%"/>

그림처럼 test.txt 파일을 만들고 여기에 한 문장을 써서 커밋을 했고, 다시 한 문장을 추가한 후 새로운 커밋을 남겼다.

<img src="https://raw.githubusercontent.com/3jins/Images/master/rebase_time_travel_2.png" width="90%"/>

여기서 rebase를 통해 과거로 돌아가 test.txt를 위 그림처럼 수정한다면 현재의 커밋내용에서 충돌이 발생하게 된다. 실제로 이런 식으로 수정한 뒤 `git rebase --continue` 명령을 통해 현재로 돌아오려고 하면 깃은 다음과 같은 에러를 내뿜는다.

> error: could not apply 62759c2... Modify test.txt
>
> When you have resolved this problem, run "git rebase --continue".
> If you prefer to skip this patch, run "git rebase --skip" instead.
> To check out the original branch and stop rebasing, run "git rebase --abort".
> Could not apply 62759c2e275429c2b59101ff3f4e78bc1ea617e5... Modify test.txt

시키는 대로 하면 된다. 우리에겐 두 가지의 선택지가 있다. 원래의 미래로 되돌릴 것이냐(`abort`) 아니면 바꾼 미래로 덮어쓸 것이냐(`skip`). 결과는 다음과 같아진다.

- abort

  rebase한 내용을 버리고 원래대로 되돌린다.

  ```bash
  $ (master) git rebase --abort
  $ (master) cat test.txt
  YAHO
  YAHO
  ```

- skip

  아예 "Modify test.txt" 커밋을 없애버리고 진행한다.

  ```bash
  $ (master) git rebase --skip
  Successfully rebased and updated refs/heads/master.
  $ (master) cat test.txt
  YAHO
  What will happen if I add this line?
  ```
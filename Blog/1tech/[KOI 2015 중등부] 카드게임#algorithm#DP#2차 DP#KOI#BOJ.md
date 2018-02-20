네이버의 품을 떠난지 어언 2개월.. 다시 취준생이 된 나는 코딩테스트 준비를 하고 있다. 과거에는 가볍게 ~~외워서~~ 풀었던 문제유형지만 오랜만에 풀려니까 잘 안된다. 제대로 정리해놓고 넘어가도록 하자.



## Problem

[https://www.acmicpc.net/problem/10835](https://www.acmicpc.net/problem/10835)

> | 시간 제한 | 메모리 제한 | 제출 | 정답 | 맞은 사람 | 정답 비율 |
> | :-------- | :---------- | :--- | :--- | :-------- | :-------- |
> | 1 초      | 256 MB      | 5830 | 1471 | 1051      | 27.614%   |
>
> ## 문제
>
> 지훈이는 최근에 혼자 하는 카드게임을 즐겨하고 있다. 게임에 사용하는 각 카드에는 양의 정수 하나가 적혀있고 같은 숫자가 적힌 카드는 여러 장 있을 수 있다. 게임방법은 우선 짝수개의 카드를 무작위로 섞은 뒤 같은 개수의 두 더미로 나누어 하나는 왼쪽에 다른 하나는 오른쪽에 둔다. 그리고 빈 통을 하나 준비한다. 
>
> 이제 각 더미의 제일 위에 있는 카드끼리 서로 비교하며 게임을 한다. 게임 규칙은 다음과 같다. 지금부터 왼쪽 더미의 제일 위 카드를 왼쪽 카드로, 오른쪽 더미의 제일 위 카드를 오른쪽 카드로 부르겠다.
>
> 1. 언제든지 왼쪽 카드만 통에 버릴 수도 있고 왼쪽 카드와 오른쪽 카드를 둘 다 통에 버릴 수도 있다. 이때 얻는 점수는 없다.
> 2. 오른쪽 카드에 적힌 수가 왼쪽 카드에 적힌 수보다 작은 경우에는 오른쪽 카드만 통에 버릴 수도 있다. 오른쪽 카드만 버리는 경우에는 오른쪽 카드에 적힌 수만큼 점수를 얻는다.
> 3. (1)과 (2)의 규칙에 따라 게임을 진행하다가 어느 쪽 더미든 남은 카드가 없다면 게임이 끝나며 그때까지 얻은 점수의 합이 최종 점수가 된다. 
>
> 다음 예는 세 장 씩 두 더미의 카드를 가지고 게임을 시작하는 경우이다
>
> | 카드 순서 | 왼쪽 더미 | 오른쪽 더미 |
> | :-------- | :-------- | :---------- |
> | 1         | 3         | 2           |
> | 2         | 2         | 4           |
> | 3         | 5         | 1           |
>
> ![img](https://onlinejudgeimages.s3-ap-northeast-1.amazonaws.com/problem/10835/1.png)이 경우, 우선 오른쪽 카드 2가 왼쪽 카드 3보다 작으므로 규칙 (1)에 따라 왼쪽 카드만 버리거나 왼쪽 카드와 오른쪽 카드를 모두 버리거나, 규칙 (2)에 따라 오른쪽 카드만 버릴 수 있다. 만약 오른쪽 카드만 버리는 것으로 선택하면, 2만큼 점수를 얻고 오른쪽 카드 2는 버린다. 이제 오른쪽 더미의 제일 위 카드는 4이고 이는 왼쪽 카드 3보다 크므로 규칙 (1)에 따라 왼쪽 카드만 버리거나 왼쪽 카드와 오른쪽 카드를 둘 다 버릴 수 있다. 만약 둘 다 버리는 것으로 선택하면, 이제 왼쪽 카드는 2가 되고 오른쪽 카드는 1이 된다. 이 경우 다시 규칙 (1)과 (2)에 따라 세 가지 중 한가지를 선택할 수 있고, 그 중 왼쪽 카드만 버리는 것으로 선택하면 이제 왼쪽 카드는 5가 되고 오른쪽 카드는 1이 된다. 이 경우에도 역시 규칙 (1)과 (2)에 따라 세 가지 중 한가지를 선택할 수 있고, 그 중 오른쪽 카드만 버리는 것으로 선택하면 1만큼 점수를 얻고 오른쪽 카드 1은 버린다. 이제 오른쪽 더미에는 남은 카드가 없으므로 규칙 (3)에 따라 게임이 끝나며 최종 점수는 2+1=3이 된다.
>
> 두 더미의 카드가 주어졌을 때, 게임을 통해 얻을 수 있는 최종 점수의 최댓값을 출력하는 프로그램을 작성하시오. 위 예에서 최종 점수의 최댓값은 7이다.
>
> ## 입력
>
> 첫 줄에는 한 더미의 카드의 개수를 나타내는 자연수 N(1 ≤ N ≤ 2,000)이 주어진다. 다음 줄에는 왼쪽 더미의 카드에 적힌 정수 A(1 ≤ A ≤ 2,000)가 카드 순서대로 N개 주어진다. 그 다음 줄에는 오른쪽 더미의 카드에 적힌 정수 B(1 ≤ B ≤ 2,000)가 카드 순서대로 N개 주어진다. 각 더미에는 같은 숫자를 가진 카드가 두 개 이상 있을 수 있다.
>
> ## 출력
>
> 얻을 수 있는 최종 점수의 최댓값을 출력한다.



## Solution

단순히 DP를 쓸 줄 아느냐를 묻는 문제다. 그냥 풀 줄 안다면 풀면 되고, 풀 줄 모르면 털리면 된다.

```java
import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.util.*;

public class CardGame10835 {
    private int mNumCardsPerDeck;
    private int[] mLeftDeck;
    private int[] mRightDeck;
    private int[][] mOptimized;

    public CardGame10835() {
        init();
        System.out.println(getMaxScore(0, 0));
    }

    public void init() {
        BufferedReader reader = new BufferedReader(new InputStreamReader(System.in));
        StringTokenizer tokenizer;
        try {
            mNumCardsPerDeck = Integer.parseInt(reader.readLine());
            mLeftDeck = new int[mNumCardsPerDeck];
            mRightDeck = new int[mNumCardsPerDeck];
            tokenizer = new StringTokenizer(reader.readLine());
            for (int i = 0; i < mNumCardsPerDeck; i++) {
                mLeftDeck[i] = Integer.parseInt(tokenizer.nextToken());
            }
            tokenizer = new StringTokenizer(reader.readLine());
            for (int i = 0; i < mNumCardsPerDeck; i++) {
                mRightDeck[i] = Integer.parseInt(tokenizer.nextToken());
            }
        } catch (IOException e) {
            e.printStackTrace();
        }

        mOptimized = new int[mNumCardsPerDeck][mNumCardsPerDeck];
        for (int i = 0; i < mNumCardsPerDeck; i++) {
            for (int j = 0; j < mNumCardsPerDeck; j++) {
                mOptimized[i][j] = -1;
            }
        }
    }

    public boolean isEnd(int leftIdx, int rightIdx) {
        return (leftIdx == mNumCardsPerDeck) || (rightIdx == mNumCardsPerDeck);
    }

    public int getMaxScore(int leftIdx, int rightIdx) {
        if (isEnd(leftIdx, rightIdx)) return 0;
        if (mOptimized[leftIdx][rightIdx] >= 0) return mOptimized[leftIdx][rightIdx];
        int left = mLeftDeck[leftIdx];
        int right = mRightDeck[rightIdx];
        if (left > right) {
            mOptimized[leftIdx][rightIdx] = right + getMaxScore(leftIdx, rightIdx + 1);
        } else {
            mOptimized[leftIdx][rightIdx] = Math.max(getMaxScore(leftIdx + 1, rightIdx), getMaxScore(leftIdx + 1, rightIdx + 1));
        }
        return mOptimized[leftIdx][rightIdx];
    }
}
```



## Retrospect

DP 문제를 풀 때는 다음의 사항을 명심하도록 하자.

#### 반복과 분기

DP 문제를 판별하는 주 기준이다. 반복이 일어나며 매 반복마다 분기가 존재한다. 이 여러 분기 중 최적값을 찾아내는 문제라면 DP일 가능성이 농후하다.

#### DP 배열

DP 문제 풀이에 보면 보통 `int dp[]`같은 배열을 하나씩 선언해두고 시작한다(내 코드에서는 `mOptimized`라는 이름으로 선언되어 있다). DP일 경우 얘는 반드시 존재한다. 예외는 없다.

#### 변하는 인덱스값

고등학교 수열 문제에서 나오는 $\sum$의 $i$처럼 이터레이션을 돌면서 변하는 값이 존재한다. 한 개일 수도, 2개 이상일 수도 있다. 한 개라면 DP 배열을 1차원으로 선언하고, 두 개라면 2차원으로 선언한다.

#### 점화식

DP 문제는 점화식을 통해 DP 배열을 업데이트한다. 이 때, 업데이트 과정에서 DP 배열에 이미 저장된 값을 쓰지 않으면 DP를 사용한 게 아니다. **매 iteration마다 1. DP 배열의 값을 쓸 수 있는지 확인하고, 2. DP 배열의 값을 가져오거나, 연산을 거친 뒤, 3. 현재 iteration에서 가능한 모든 경우의 수 중 최적의 값으로 DP 배열을 업데이트한다.**

#### DP 배열이 비어있는 동안

DP 배열의 첫번째 업데이트가 이루어지기 전까지, DP를 사용한 풀이와 그렇지 않은 풀이는 시간복잡도가 같다. 즉, 핵심은 재귀냐 반복이냐 따위가 아니라 DP 배열을 업데이트하고 이를 재활용했느냐에 있음을 명심해야 한다.


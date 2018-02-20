11주차 문제는 그냥 해쉬맵을 쓸 줄 아느냐 정도를 묻는 문제로, 쉬우므로 생략하고 12주차로 넘어간다.

12주차 문제는 약간 아이큐 테스트에 가깝다. 비슷한 문제를 한 번이라도 만나봤으면 그냥 푸는 거고 아니면 못 푼다고 보면 되겠다. 아, 머리가 좋으면 처음 봐도 풀 수 있다.

## Problem

>  정수로된 배열이 주어지면, 각 원소가 자신을 뺀 나머지 원소들의 곱셈이 되게하라.
>
>  단, 나누기 사용 금지, O(n) 시간복잡도
>
>  예제)
>
>  input: [1, 2, 3, 4, 5]
>
>  output: [120, 60, 40, 30, 24]



## Trial

재밌겠다하고 덤볐다가 손도 못 댔다. 난 쓰레기다. ㅠㅠ



## Solution

급식 때의 머리로 풀었으면 풀 수 있었을지도 모르겠다. 우리나라 7~8차 교육과정을 이수한 사람이라면 수열의 합을 배우면서 갓갓수학자 가우스찡의 일화를 한번쯤 들어봤을텐데 정확하게 그 아이디어를 사용한다.

```c++
vector<int> productAllExceptSelf(int arr[], int arrSize) {
    vector<int> preOrder = {1};
    vector<int> postOrder = {1};
    vector<int> result;

    for (int i = 1; i < arrSize; i++) {
        preOrder.emplace_back(preOrder[i - 1] * arr[i - 1]);
        postOrder.emplace(postOrder.begin(), postOrder[0] * arr[arrSize - i]);
    }
    for (int i = 0; i < arrSize; i++) {
        result.emplace_back(preOrder[i] * postOrder[i]);
    }
    return result;
}
```



##Retrospect

#### 갓갓 가우스 형님

가우스는 10살 때 1부터 100까지의 합을 [구하라](https://www.google.co.kr/search?q=%EA%B5%AC%ED%95%98%EB%9D%BC&newwindow=1&rlz=1C1CHZL_koKR751KR751&source=lnms&tbm=isch&sa=X&ved=0ahUKEwjA7KbR3ITcAhVGErwKHdgxC7MQ_AUICigB&biw=1217&bih=655)는 문제를 풀기 위해 1~100의 증가수열과 그걸 반대로 뒤집은 100~1의 감소수열을 더했다. 이 문제에서도 비슷하게 **등비수열을 만들고 뒤집어서 더하면 된다**.

#### 누적합, 누적곱

처음에 해답 보고 풀려고 하다가 난 이런 생각을 했다.

> 'arr[4] * arr[3] * arr[2] * arr[1] 같은거 저장하려면 어차피 O(n)번 더 돌아야 되는거 아닌가?'

하지만 0개 곱에서부터 4개 곱까지 곱을 하나씩 누적해서 올라가기 때문에 그럴 필요가 없었다. **무언가가 누적되는 구조라면 굳이 O(n)번을 더 돌 필요가 없다**.
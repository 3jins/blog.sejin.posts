13주, 14주차 문제는 인간적으로 너무 쉬워서 생략하고 15주차로 넘어간다. 사실 15주차 문제도 쉽지만 새로운 STL을 정리할 수 있기 때문에 간단히 정리하겠다.



## Problem

> 링크드 리스트(linked list)의 머리 노드(head node)와 정수 N이 주어지면, 끝에서 N번째 노드(node)를 제거하고 머리 노드(head node)를 리턴하시오.
>
> 단, 리스트를 한번만 돌면서 풀어야합니다. N은 리스트 길이보다 크지 않습니다.
>
> 예제)
>
> Input: 1->2->3->4->5, N=2
>
> Output: 1->2->3->5
>
> Input: 1->2->3, N=3
>
> Output: 2->3
>
> Input: 1, N=1
>
> ﻿Output: null



## Trial

한 바퀴 돌면서 스택에 쑤셔박아놨다가 N번 pop해서 해결했다.

```c++
list<int> eliminateNth(list<int> inputList, int n) {
    stack<list<int>::iterator> nodeStack;
    list<int>::iterator iter;
    list<int>::iterator target;

    for(iter = inputList.begin(); iter != inputList.end(); iter++) {
        nodeStack.push(iter);
    }
    for(int i = 0; i < n; i++) {
        target = nodeStack.top();
        nodeStack.pop();
    }
    inputList.erase(target);

    return inputList;
}
```

근데 사실 이건 리스트를 1번 다 돌고 나서 다시 좀 더 돌기 때문에 문제에서 시간제한을 빡시게 주면 통과할 수 없다.



## Solution

해설에서는 포인터를 두 개 쓴다. 이는 '투 포인터'라는 아이디어로 대표적인 DP 유형 중 하나다. 

n + (M - n ) = M임을 이용해서 n과 M만으로 M - n을 뽑아내는 게 핵심이다. 약간 [비커 두 개로 특정 양의 물 담아내는 창의사고력 문제](http://0pionium.tistory.com/372) 삘이 난다. 

```java
Node solution(Node head, int n) {
    if (head == null) {
        return null;
    }
    Node first = head;
    Node second = head;
    for (int i = 0; i < n; i++) {
        first = first.next;
    }
    if (first == null) {
        head = head.next;
        return head;
    }
    while (first.next != null) {
        first = first.next;
        second = second.next;
    }
    second.next = second.next.next;
    return head;
}
```



## Retrospect

#### STL List

iterator가 굉장히 많이 쓰인다. 함수의 인자로 iterator를 넣어서 타겟노드를 지정하는 경우가 많다.

#### STL Stack

얘는 주의점이 하나 있다. pop()이 아무것도 리턴하지 않는다. 그래서 top()을 먼저 해서 값을 얻고 pop()을 해서 값을 제거해줘야 한다.

#### Two Pointers

$$
n + (M - n) = M
$$

이 수식은 얼핏 봐서는 잘 와닿지 않는다. 유형을 외우는 게 중요하므로, 이거 하나만 기억하자. **끝에서 n번째를 구하는 것은 포인터 두개를 이용해서 해결할 수 있다.** 그리고 그 방법이 기억 안 날 때 이 글을 다시 보면 되겠다.


갑자기 C++로 자료구조를 짜야하는 문제가 나왔다. 그 덕택에 C++ 문법 공부를 많이 하게 되었다.



## Problem

> 이진 트리를 루트 노드를 기준으로 좌우반전 하시오.
>
> 이 문제는 구글이 Homebrew 창시자에게 낸 문제로 유명합니다.



## Trial

뭔가 매일프로그래밍 측이 문제 내기 귀찮아하는 게 보이는 거 같다. 네임드 문제라고 파는 건 양심에 찔려서 그런 게 아닐까.

어떻게 풀면 되는지는 금방 나왔다. 그냥 모든 노드를 순회하면서 왼쪽 자식이랑 오른쪽 자식을 바꿔주면 끝난다. traversal급으로 쉽다. 다만 문제는 BinaryTree를 짜야 시작할 수 있다는 거다.



## Solution

InvertableBinaryTree가 상속받는 BinaryTree의 코드는 [여기](https://enhanced.kr/postviewer/607)서 보면 된다. 

```c++
#ifndef ALGORITHM_HUSTLE_INVERTABLE_BINARY_TREE_H
#define ALGORITHM_HUSTLE_INVERTABLE_BINARY_TREE_H

#include "binary_tree.h"

template<typename T>
class InvertableBinaryTree : public BinaryTree<T> {
    friend class BinaryTree<T>;
    TreeNode<T> *root;
public:
    InvertableBinaryTree(T rootData = 0);
    void swap(TreeNode<T> *parent);
    void invert(TreeNode<T> *root);
};

template <typename T>
InvertableBinaryTree<T>::InvertableBinaryTree(T rootData) {
    root = new TreeNode<T>(rootData);
}

template<typename T>
void InvertableBinaryTree<T>::swap(TreeNode<T> *parent) {
    TreeNode<T> *tmp = parent->left;
    parent->left = parent->right;
    parent->right = tmp;
}

template<typename T>
void InvertableBinaryTree<T>::invert(TreeNode<T> *root) {
    if(isLeaf(root)) return;
    if(root->left != null)
        invert(root->left);
    if(root->right != null)
        invert(root->right);
    swap(root);
}

#endif
```



## Retrospect

알고리즘 시험을 보는 용도로만 쓴다고 해서 사용하려는 언어를 대충 봐서는 안 된다. 물론 주력언어만큼 깊이 팔 필요는 없겠지만 알고리즘 시험에서도 이처럼 얼마든지 구현 문제를 낼 수가 있다. 

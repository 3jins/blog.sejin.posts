난 프론트엔드 웹쟁이라 자바스크립트가 주력언어인데다가 학교에서는 Java랑 C만 오지게 가르쳐서 C++로 제대로 코딩해본 적이 없다. 하지만 알고리즘 문제 풀이를 할 때는 주로 C++을 사용한다. 그 이유는 그냥 남들이, 특히 알고리즘 고수분들이 많이 써서 그냥 따라하는 것일 뿐 뭐 특별히 C++이 좋은 이유를 알아서는 아니다. 이렇다 보니 C++으로 클래스를 만들어 쓰거나 하려면 문법을 몰라서 아무것도 할 수가 없다. 

사실 웬만한 단편적인 문제를 해결할 때 객체지향적으로 짤 필요도 없고 `vector`와 `iterator` 개념 정도만 알면 충분했다. 그런데 C++으로 직접 트리를 짜야되는 상황이 되자 문제가 생겼다. 그래서 C++을 이용해서 간단히 링크드리스트와 이진트리 헤더파일을 만들어보는 시간을 가져보도록 하겠다. 

(C++의 STL을 다루는 건 아니지만 C++ 문법에 대한 내용인데 태그 따로 분리해서 나누기도 애매해서 그냥 C++ STL 태그에 포함시켰다.)



## Java와는 다르다 Java와는!

C++은 Java와 비슷하게 생긴 주제에 Java와 상당히 다르다. 아예 처음 보는 문법이야 찾아보면 되지만 Java에 똑같이 생긴 문법이 존재하지만 C++에서는 다르게 사용하는 문법도 몇 가지 있는데 이 부분은 짚어놓지 않으면 C++을 쓸 때마다 헷갈릴 수밖에 없다.

### new

Java의 `new`는 다음처럼 클래스로부터 객체를 선언할 때 사용한다. 반면, C++의 `new`는 그 뿌리를 C의 `malloc`에 두고 있다. 이 사소한 차이로 인해 **Java의 선언문은 객체를 반환**하지만, **C++의 선언문은 객체의 주소를 반환한다**는 점이 달라진다. 그래서 C++에서 객체가 멤버변수로 들어갈 경우, 해당 멤버변수는 죄다 포인터로 선언한다. 마치 동적할당처럼.

### this

Java의 `this`는 클래스에 의해 생성된 객체 자신을 의미했다. 즉, **`this`는 객체처럼 쓰였다**. 반면, C++의 `this`는 자기자신을 가리키는 포인터다. 이게 어떤 의미를 갖느냐면... Java에서는 `this.name`과 같은 식으로 쓰고, C++에서는 `this->name`과 같은 식으로 쓰게 되는 효과를 갖는다.



## Linked List

이제 앞서 언급한 부분들에 유념해보면서 몸풀기로 먼저 링크드리스트 클래스를 구현해보자.

```C++
#ifndef ALGORITHM_HUSTLE_LINKED_LIST_H
#define ALGORITHM_HUSTLE_LINKED_LIST_H

#define null 0

template <typename T>
class LinkedList {
private:
    class Node {
    public:
        T data;
        Node* nextNode;
        Node(T data, Node *nextNode = null) {
            this->data = data;
            this->nextNode = nextNode;
        }
    };
    Node *head;
    Node *tail;
    int nodeCount;
public:
    LinkedList();
    T get(int index);
    void add(T data);
    void add(int index, T data);
    void set(int index, T data);
    void remove(int index);
    bool isEmpty();
    int size();
};

template <typename T>
LinkedList<T>::LinkedList() {
    tail = new Node(0);
    head = new Node(0, tail);
    nodeCount = 0;
}

template <typename T>
T LinkedList<T>::get(int index) {
    Node *curNode = head;
    for(int i=0; i<index; i++) {
        curNode = curNode->nextNode;
    }
    return curNode->data;
}

template <typename T>
void LinkedList<T>::add(T data) {
    Node *newNode = new Node(data, head->nextNode);
    newNode->nextNode = head->nextNode;
    head->nextNode = newNode;
    nodeCount++;
}

template <typename T>
void LinkedList<T>::add(int index, T data) {
    Node *newNode = new Node(data, head->nextNode);
    Node *curNode = head;
    for(int i=0; i<index; i++) {
        curNode = curNode->nextNode;
    }
    newNode->nextNode = curNode->nextNode;
    curNode->nextNode = newNode;
    nodeCount++;
}

template <typename T>
void LinkedList<T>::remove(int index) {
    Node *curNode = head;
    for(int i=0; i<index; i++) {
        curNode = curNode->nextNode;
    }
    Node *tmp = curNode->nextNode;
    curNode->nextNode = curNode->nextNode->nextNode;
    tmp->nextNode = null;
    delete tmp;
    nodeCount--;
}

template <typename T>
bool LinkedList<T>::isEmpty(){
    return nodeCount==0;
}

template <typename T>
int LinkedList<T>::size() {
    return nodeCount;
}

#endif
```

```C++
#include <iostream>
#include "linked_list.h"

using namespace std;

int main() {
    LinkedList<int> list = LinkedList<int>();
    cout << list.isEmpty() << endl;
    list.add(30);
    list.add(10);
    list.add(1, 20);
    cout << list.get(0) << endl;
    cout << list.get(1) << endl;
    cout << list.get(2) << endl;
    cout << list.get(3) << endl;
    list.remove(1);
    cout << list.get(0) << endl;
    cout << list.get(1) << endl;
    cout << list.get(2) << endl;
    cout << list.size() << endl;
    return 0;
}
```

사족: `Node` 클래스의 멤버변수가 `public`인 이유는 `Node` 클래스 자체가 이미 `LinkedList` 클래스에서 `private`으로 선언된 내부클래스기 때문에 밖으로 나갈 일이 없기 때문이다.



## Binary Tree

이제 바이너리 트리를 짜는 것도 별 문제가 없을 것 같다.

```c++
#ifndef ALGORITHM_HUSTLE_TREE_NODE_H
#define ALGORITHM_HUSTLE_TREE_NODE_H
#define null 0

template<typename T>
class TreeNode {
public:
    T data;
    TreeNode *left;
    TreeNode *right;

    TreeNode(T data, TreeNode *left = null, TreeNode *right = null) {
        this->data = data;
        this->left = left;
        this->right = right;
    }
};

#endif //ALGORITHM_HUSTLE_TREE_NODE_H
```

```c++
#ifndef ALGORITHM_HUSTLE_LINKED_LIST_H
#define ALGORITHM_HUSTLE_LINKED_LIST_H

#define null 0

template <typename T>
class LinkedList {
private:
    class Node {
    public:
        T data;
        Node* nextNode;
        Node(T data, Node *nextNode = null) {
            this->data = data;
            this->nextNode = nextNode;
        }
    };
    Node *head;
    Node *tail;
    int nodeCount;
public:
    LinkedList();
    T get(int index);
    void add(T data);
    void add(int index, T data);
    void set(int index, T data);
    void remove(int index);
    bool isEmpty();
    int size();
};

template <typename T>
LinkedList<T>::LinkedList() {
    tail = new Node(0);
    head = new Node(0, tail);
    nodeCount = 0;
}

template <typename T>
T LinkedList<T>::get(int index) {
    Node *curNode = head;
    for(int i=0; i<index; i++) {
        curNode = curNode->nextNode;
    }
    return curNode->data;
}

template <typename T>
void LinkedList<T>::add(T data) {
    Node *newNode = new Node(data, head->nextNode);
    newNode->nextNode = head->nextNode;
    head->nextNode = newNode;
    nodeCount++;
}

template <typename T>
void LinkedList<T>::add(int index, T data) {
    Node *newNode = new Node(data, head->nextNode);
    Node *curNode = head;
    for(int i=0; i<index; i++) {
        curNode = curNode->nextNode;
    }
    newNode->nextNode = curNode->nextNode;
    curNode->nextNode = newNode;
    nodeCount++;
}

template <typename T>
void LinkedList<T>::remove(int index) {
    Node *curNode = head;
    for(int i=0; i<index; i++) {
        curNode = curNode->nextNode;
    }
    Node *tmp = curNode->nextNode;
    curNode->nextNode = curNode->nextNode->nextNode;
    tmp->nextNode = null;
    delete tmp;
    nodeCount--;
}

template <typename T>
bool LinkedList<T>::isEmpty(){
    return nodeCount==0;
}

template <typename T>
int LinkedList<T>::size() {
    return nodeCount;
}

#endif
```

```c++
#include <iostream>
#include <vector>
#include "binary_tree.h"

using namespace std;

template <typename T>
void printVector(vector<T> v) {
    typename vector<T>::iterator iter;
    for(iter = v.begin(); iter != v.end(); iter++) {
        cout << *iter << " ";
    }
    cout << endl;
}

int main() {
    BinaryTree<char> tree = BinaryTree<char>();
    tree.buildTree(tree.getRoot(), {'A', 'B', 'C', 'D', 'E', 'F', 'G', 'H', 'I'});
    printVector(tree.preorderTraversal(tree.getRoot()));
    return 0;
}
```



## A.E.

따로 언급하진 않았지만 코드에는 등장한 C++ 문법들이 좀 있다. 추후 나의 구글링을 위해 간단히만 적어두겠다.

* `#ifndef` ~~~ `#endif`

  'include guards'라는 이름으로 검색하면 많이 나온다. 이거 대신 '#pragma once'라는 표준문법이 아닌 문법을 쓰기도 한다.

* `template <typename T>`

  적어놓으면 그 바로 아래의 클래스나 함수가 템플릿클래스 또는 템플릿함수가 되어 템플릿으로 받는 자료형 T를 쓸 수 있게 된다.

  선언한 자료형 T를 템플릿클래스나 템플릿함수 안에서 쓸 때 가끔 앞에 typename을 붙이라고 컴파일러가 요구하기도 한다. 왜 그런지는 모르겠다. 그냥 붙이라면 붙이자.
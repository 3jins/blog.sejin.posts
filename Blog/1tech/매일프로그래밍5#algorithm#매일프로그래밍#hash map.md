3월 12일자 문제는 워낙 쉬운데다가 단순 수학문제라 별 중요한 개념도 없었으므로 패스하고 바로 3월 19일자 문제를 정리하겠다. 



## Problem

정수 배열과 타겟 숫자가 주어지면, 합이 타겟값이 되는 두 원소의 인덱스를 찾으시오.

단, 시간복잡도 O(n) 여야 합니다.

예제)

Input: [2, 5, 6, 1, 10], 타겟 8

Output: [0, 2] // 배열[0] + 배열[2] = 8



## Trial

처음엔 감도 못잡았다. 한참 넋놓고 보다가 **문제를 단순화**시켜보기로 했다.

[2, 5, 6, 1, 10], 타겟 8

여기서 모든 배열원소에 1씩을 빼고, 두 개를 더해서 타겟이 나와야 하므로 타겟에는 2를 뺀다.

[1, 4, 5, 0, 9], 타겟 6

처음 문제와 완벽히 같은 문제다. 배열 원소들 중에 0이 있으므로 타겟인 6이랑 같은 원소가 있는지만 체크하면 답을 구할 수 있다. 하지만 배열원소 중에 6은 없다. 그럼 이 작업을 한 번 더 해본다.

[**0**, 3, **4**, -1, 9], 타겟 4

따라서 답은 0, 2가 된다. 코드로 옮겨보자.

```c++
#include <iostream>
#include <vector>

using namespace std;

void printTargetComb(vector<int> inputs, int target) {
    int sizeInputs = inputs.size();
    vector<int> results = {};
    while (target > 0) {
        target -= 2;
        results = {};
        for (int i = 0; i < sizeInputs; i++) {
            if (--inputs[i] == 0) {
                results.push_back(i);
            } else if (inputs[i] == target) {
                results.push_back(i);
            }
            if (results.size() == 2) {
                cout << results[0] << ", " << results[1] << endl;
                return;
            }
        }
    }
    cout << "Impossible" << endl;
    return;
}
```

한 가지 문제가 있다. 과연 이 알고리즘의 시간복잡도를 $O(n)$이라 할 수 있을까. $O(n)$이라 하면, input의 크기에 상관없이 반복문을 상수번만 돌아야 한다. 근데 얘는 input의 크기에는 영향을 받지 않지만 타겟값에 영향을 받는다. 시간복잡도가 $O(n) * {타겟}/2$가 된다. 찝찝함을 안고 답을 보기로 했다.



## Solution

```c++
int[] solution(int[] input, int target) {
	Map<int, int> map = new HashMap<>();
	for (int i = 0; i < input.length; i++) {
		int complement = target - input[i];
		if (map.containsKey(complement)) {
			return [map.get(complement), i];
		}
		map.put(input[i], i);
	}
	return [-1, -1]; // No solution
}
```

ㄷㄷ... 



## Retrospect

#### hash map

hash map은 Python의 dictionary와 유사한 자료구조다. [깊게 들어가면](http://asfirstalways.tistory.com/332) 해쉬 함수랑 엮이면서 무슨 collision이니 뭐니 하는 복잡한 얘기들이 막 나오는데 그냥 알고리즘 푸는 입장에서는 다 필요없고 **key와 value가 매칭되는 자료구조**라는 것과 **key로 값을 가져오는데 걸리는 시간복잡도가 배열과 동일한 $O(1)$**이라는 것만 알면 된다.

그럼 이 문제에서 hash map이 어떻게 시간복잡도를 비약적으로 감소시켰는지 생각해보자. 일단 시간복잡도가 $O(n)$이란 말은 정렬 같은 건 하지 말라는 뜻이다. 정렬이 필요한데 정렬을 안 하려면? 그렇다. **이미 정렬된 데에다가 갖다 얹어버리면** 된다. 이미 정렬된 것의 대표적인 예로는 **수(數)직선**이 있다. 이제 우리에게 필요한 건 값을 가져오는 데에 필요한 시간복잡도가 $O(1)$인 자료구조 뿐이다. 따라서 사실 배열을 써도 무방하다만, Python 등의 언어와는 달리 에서C++는 명시적인 `null`이 없고 `null`을 0으로 간주하기 때문에 여기서 쓰기 애매한 것이다.

매일프로그래밍에서 제공해 준 풀이가 Java코드이므로 C++로 해석해보기로 했다. 여기서 나 같은 C++ 뉴비에게 [헷갈릴 만한 사실](https://stackoverflow.com/questions/1646266/difference-between-hash-map-and-unordered-map)이 하나 있다. Java에서는 해쉬맵 라이브러리를 그냥 가져다 쓰면 되는데 C++은 옛날에 해쉬맵을 표준 라이브러리로 제공하지 않았다고 한다. 당시에는 `hash_map`이라는 비표준 라이브러리가 임시방편으로 쓰였고 C++11에 들어서면서 [해쉬맵 표준 라이브러리](https://docs.microsoft.com/en-us/cpp/standard-library/hash-map)를 만들게 되었는데 비표준 라이브러리인 `hash_map`과의 충돌을 피하기 위해 `unorderd_map`이라고 명명하고 `hash_map`은 deprecated 시켜버렸다. 그럼 C++ 코드를 올리고 글을 마치겠다.

```c++
#include <iostream>
#include <vector>
#include <unordered_map>

using namespace std;

void printTargetComb(vector<int> inputs, int target) {
    unordered_map<int, int> map;
    int sizeInput = inputs.size();

    for(int i=0; i<sizeInput; i++) {
        if(map.find(inputs[i]) != map.end()) {  // map에 inputs[i]가 있는지 확인. 배열에는 이런 함수가 없기 때문에 쓰기 애매하다.
            cout << i << ", " << map[inputs[i]] << endl;
        }
        map[target - inputs[i]] = i;
    }
}
```


[졸업을 위한 DRNN](https://enhanced.kr/postviewer/1551) 시리즈의 글입니다.

이전 글인 [<워드임베딩(word embedding이란)>](https://enhanced.kr/postviewer/2216)에서 워드임베딩을 위한 벡터표현 방식이 sparse와 dense로 나뉜다고 했었다. Sparse한 방식의 구현은 그냥 이중 for문만 돌리면 되므로 막 어렵지는 않다. 근데 dense 방식이라면 얘기가 달라진다. Input data를 학습하여 동시에 등장하는 단어일수록 그 유사도가 가깝게 표현되도록 임베딩시켜야 하기 때문이다.

저번 글에서도 언급했다시피 dense representation을 구현한 워드임베딩 모델에는 Word2Vec, GloVe, FastText 등 여러가지가 있다. 그 중 이번 글에서 다루게 될 [Word2vec](https://en.wikipedia.org/wiki/Word2vec)은 **CBOW**와 **Skip-Gram**이라는 두 가지 방식을 사용한다. 이에 대해서 좀 더 자세히 알아보도록 하자. ~~사실 원리 읽어봤는데 1도 이해 안돼서 간단히 다룰거다.~~



## Models in Word2Vec

Word2vec에서는 CBOW와 skip-gram이라는 두 가지 방식 중 하나를 선택해서 워드임베딩을 진행할 수 있다.

### CBOW(Continuous Bag of Words)

문맥으로부터 적절한 단어를 추론하는 모델이다. 예를 들어, 고양이에 대한 문장을 잔뜩 집어넣어 학습시키고 나면 '털', '관리', '혀', '깨끗', '고양이' 등의 단어를 보고 '그루밍'을 유추하는 식이다. Sliding window를 이용해서 주변 단어와의 관계를 살핀다. 

### skip-gram

CBOW와는 반대로 단어로부터 문맥을 구성할 수 있는 단어들을 추론하는 모델이다. 그 외의 학습과정들은 CBOW와 매우 유사하다고 한다.



## Gensim

본래 C++ 라이브러리지만, 파이썬에서는 `gensim`이라는 패키지 안에 word2vec이 구현되어있다. 임베딩은 거의 얘가 알아서 해준다고 봐도 무방하다. 내가 이 라이브러리에 대해 마스터한 건 아니지만, 졸업작품에 사용한 기초적인 수준까지만이라도 정리해두겠다.

### [LineSentence](https://radimrehurek.com/gensim/models/word2vec.html#gensim.models.word2vec.LineSentence), [PathLineSentences](https://radimrehurek.com/gensim/models/word2vec.html#gensim.models.word2vec.PathLineSentences)

Input data 파일을 불러와서 공백 단위로 끊은 이중리스트 형태로 만들어준다. `LineSentence`는 파일 하나를 불러오고, PathLineSentences는 여러 파일이 들어있는 경로 하나를 불러온다.

```python
import os
from gensim.models import word2vec

data_path = os.path.join('.', 'data')	 # Path to data
sentences = word2vec.PathLineSentences(data_path)

target_data = os.path.join(data_path, 'train_0000.txt')
sentence = word2vec.LineSentences(data_path)

print('-' * 5, 'PathLineSentences', '-' * 5)
print(list(sentences))
print('-' * 5, 'LineSentence', '-' * 5)
print(list(sentence))
```

만약 `./data`에 `train_0000.txt`와 `train_0001.txt` 두 개의 파일이 있었고, 두 파일의 내용은 각각 '밤새 모니터에 튀긴 침이 마르기도 전에'와 '밤새 난 생각해봐 세상에 산재한 세말의 탐색과 생산에 관해'였다면, 위 파일의 실행결과는 다음과 같을 것이다.

```
----- PathLineSentences -----
[['밤새', '모니터에', '튀긴', '침이', '마르기도', '전에'],
 ['밤새', '난', '생각해봐', '세상에', '산재한', '세말의', '탐색과', '생산에', '관해']]
----- LineSentence -----
[['밤새', '모니터에', '튀긴', '침이', '마르기도', '전에']]
```

### [Word2Vec](https://radimrehurek.com/gensim/models/word2vec.html#gensim.models.word2vec.Word2Vec)

노파심에 미리 말해두자면 `word2vec`과 `Word2Vec`은 다른거다. `word2vec` 안에 `Word2Vec`이 들어있다. ~~네이밍 센스 참...~~ 학습된 모델을 저장하는 객체라 보면 된다.

```python
import os
from gensim.models import word2vec

data_path = os.path.join('.', 'data')	 # Path to data
sentences = word2vec.PathLineSentences(data_path)

embedding_model = word2vec.Word2Vec(
  sentences=sentences,
  size=100,
  window=2,
  min_count=5,
  workers=4,
  iter=100,
  sg=1,
)
```

위와 같이 생성자에 값들을 쑤셔박아주면 된다. 

`size`는 임베딩벡터의 크기다. 단어가 매칭될 벡터의 길이에 해당한다.

`window`는 슬라이딩 윈도우의 길이다. 이게 길어지면 어떤 효과가 생기는지까지는 잘 모르겠다.

학습과정에서 `min_count`보다 적게 등장하는 단어는 무시된다. 괴랄한 단어나 오타 등을 걸러낼 때 쓰면 좋다.

`workers`는 학습하는데 사용할 스레드의 수다. 지원하는 코어 수만큼 잡아주면 되겠다.

`iter`는 몇 번을 반복시킬 것이냐에 대한 값으로 100 ~ 200 정도 값을 잡아두면 적당하다.

`sg`는 학습에 skip-gram을 쓸지 CBOW를 쓸지를 결정하는 값이다. 1이면 skip-gram, 0이면 CBOW이다.

```python
embedding_model.save('model.wv')
```

위와 같이 학습된 모델을 파일 형태로 저장하는 것도 가능하다.

### [KeyedVectors](https://radimrehurek.com/gensim/models/keyedvectors.html)

Word vector들의 구현과 유사도에 관련된 함수를 제공하는 모듈이다.

gensim에서 제공하는 임베딩 모델은 Word2Vec만 있는 게 아니라고 한다. 하지만 어떤 방식으로 임베딩을 진행하던 간에 densed representation을 따르는 이상 결국 워드벡터들의 형태는 같은 모양을 띄게 된다. 따라서 워드벡터들은 임베딩 방식에는 독립적이어야 한다. 이것이 `KeyedVectors`가 `word2vec` 모듈에 독립적으로 존재하는 이유다.

```python
from gensim.models import KeyedVectors

embedding_model = KeyedVectors.load('model.wv', mmap='r')
```

위와 같이 앞서 파일을 저장했던 모델을 불러올 수 있다.

#### [Word2VecKeyedVectors](https://radimrehurek.com/gensim/models/keyedvectors.html#gensim.models.keyedvectors.Word2VecKeyedVectors)

단어들과 벡터들을 매핑시켜주는 객체다. 구현은 Word2Vec 안에서 이루어지지 않지만 Word2Vec 객체를 선언하면 Word2Vec 객체가 들고 있는 객체다.

```python
wv = embedding_model.wv  # 이 wv의 type이 Word2VecKeyedVectors이다.

print(wv.get_vector('밤새'))	# 길이 100의 벡터를 뱉을 것이다.

vector_list = wv.index2entity	 # 학습된 단어들의 리스트다.
print(len(vector_list))		# 학습한 단어의 총 갯수를 뱉을 것이다.

print(wv.similarity('생산에', '탐색과'))	# '생산에'와 '탐색과'의 의미적인 유사도를 출력한다. 
print(wv.most_similar(positive=['밤새'], topn=10))	# '밤새'와 가장 의미적인 유사도가 높은 상위 10개의 단어를 출력한다.
```



## 사족 (Input Data)

참고로 이 글에서는 그냥 생각나는 대로 input data의 예시를 한국어 가사를 적어버렸는데 한국어는 제대로 학습이 안 될 가능성이 높다. 한국어는 조사에 의해 문장 내에서 단어의 쓰임이 결정되며, 그 순서는 거의 의미가 없다고 봐도 무방하기 때문이다.

> X발 영어는 이런거 못하잖아
>
> X발 이런거 영어는 못하잖아
>
> 영어는 X발 이런거 못하잖아
>
> 영어는 못하잖아 이런거 X발
>
> 못하잖아 X발 이런거 영어는

그래서 한국어를 처리하려면 형태소분석 등의 전처리 과정이 추가로 필요한 것으로 알고 있다. 그냥 gensim을 테스트해보는 게 목적이라면 가급적 영어로 테스트해보도록 하자.

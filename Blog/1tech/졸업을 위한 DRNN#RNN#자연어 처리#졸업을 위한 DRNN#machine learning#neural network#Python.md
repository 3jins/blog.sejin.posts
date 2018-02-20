수능을 두 번이나 말아먹고 편입할 영어실력도 없었던 나는 그 죄로 [건잡대](https://kuwiki.kr/w/%EA%B1%B4%EC%9E%A1%EB%8C%80)의 졸업장을 따야만 하는 운명에 놓였다. 우리 학교의 졸업요건에는 여러 가지가 있지만, 그 중 가장 빡센 요건은 아무래도 **졸업작품**이 아닌가 한다.

이번 학기에 [나를](https://github.com/3jins/D-RNN) [포함한](https://github.com/poqw/D-RNN) [4명의](https://github.com/skrudtn/D-RNN) [동기들](https://github.com/wooyongha/D-RNN)은 졸업작품을 시작하기로 했고, 교수님들에 의해 제시된 여러 가지의 주제들 중 CPU/GPU 프로그래밍, 리눅스 커널 프로그래밍과 같은 극도로 하드한 최악의 주제를 피하기 위해 썩 좋은 건 아니지만, 경쟁 없이 무혈입성할 수 있는 차악의 주제였던 [Developmental Recurrent Neural Network](https://github.com/NineDegis/D-RNN)를 선택하게 되었다. 

RNN-LSTM 모델에 developmental training을 접목하여 성능향상을 꾀해보는 연구주제다. 저 developmental training이란 게 그냥 들으면 무슨 잘 알려진 네임드 학습방식처럼 들리지만, 사실 RNN에 학습시킬 input 데이터를 한 번에 다 넣지 말고 적당한 크기로 조각내어 학습시킨 뒤 다시 각 조각을 몇개씩 합쳐서 다시 학습시키는 방식이다. 쉽게 말해 Input Data를 조금씩 development시켜 학습시킴으로써 모델이 맥락을 좀 더 잘 이해하도록 만들어보겠다는 의미다. 

학교 측에서 진행하는 괴랄한 소프트웨어 중심 대학 사업 덕택에 졸업작품 전시회가 예정보다 1달이나 빨라지면서, 2개월밖에 안되는 시간 동안 졸업작품과 취업준비를 병행하던 우리는 결국 구현만 겨우 마무리하고 정작 주제의 핵심인 연구 부분은 심도 있게 진행하지 못했다. 하지만 뉴럴 네트워크에 대한 심도 있는 이해와 함께 여러 가지로 배운 바가 많은 주제다. 이 시리즈에서는 주로 뉴럴네트워크에 대해 다룰 것이며, 나머지 배운 바와 느낀 점에 대해서는 후기(works)에서 다루도록 하겠다.



## Index

### Environment Settings

1. [Intel CPU Wiindows에 PyTorch 설치하기](https://enhanced.kr/postviewer/92)
2. [PyCharm + Conda](https://enhanced.kr/postviewer/91)
3. Tensorboard

### Deep Learning Basis

1. [Hypothesis Function, Loss Function](https://enhanced.kr/postviewer/101)
2. [Gradient Descent](https://enhanced.kr/postviewer/103)
3. [Classification by Machine Learning](https://enhanced.kr/postviewer/104)
4. [Neural Network](https://enhanced.kr/postviewer/105)
5. [Back Propagation](https://enhanced.kr/postviewer/110)
6. Terms in Neural Network Training

### Word Embedding

1. [워드임베딩(word embedding)이란](https://enhanced.kr/postviewer/108)
2. [Word2Vec & gensim](https://enhanced.kr/postviewer/109)
3. padding & masking

### RNN & LSTM

1. RNN
2. LSTM
3. LSTM with PyTorch

### Experiments

1. Developmental Training

### 후기

[https://enhanced.kr/postviewer/119](https://enhanced.kr/postviewer/119)

개강 직전에 블록체인 뽕 맞고 분산시스템컴퓨팅이라는 과목명에 혹해서 괜히 수강신청했다가 낮은 수업 퀄리티로 고생하고 있다. 그나마 교수님 수업은 잘 가르치는 건 아니라도 내용이라도 정확하지 수업 준비도 제대로 안 해오는 조교의 실습 수업은 가서 앉아있으면 토가 나올 지경이다. 2달이나 수업을 해놓고 한 거라곤 사실상 환경구축 밖에 없다. 과제도 3개가 나왔다는데 실습수업은 안 들어서 잘 모르겠다. 근데 실습 내용이 시험에 나온다기에 실습 자료를 들여다봤지만 워낙 두서가 없어서 알아먹을 수가 없다. 그래서 시험 전 날 밤에 난 그냥 인터넷에서 자료를 긁어모아서 하둡에 대한 대략적인 개념을 잡기로 했고 이 글은 그 산물이다. (**tl;dr: 학교조교수업 안 들어서 자습해서 이 포스트로 정리함**)

과거 구글은 검색엔진의 대용량 데이터 처리를 목적으로 Google File System과 MapReduce를 만들었다. Hadoop은 이 두 기술에 기반하여 만들어진, 대용량 데이터의 분산처리를 위한 자바기반 오픈소스 프로젝트다. 

이번 포스트에서는 하둡 2.0의 기본 구조와 동작원리에 대해 파헤쳐 볼 것이다.



## Clusters (群)

하둡은 기본적으로 **마스터 노드 군(群, cluster)**과 **슬레이브 노드 군**으로 나뉜다.

같은 일을 처리하는 컴퓨터들의 그룹. 회사의 부서 같은 개념으로 이해하면 된다. 



## Components (Layers)

한 클러스터는 다음의 세 컴포넌트(또는 layer)로 구성된다. 

* Storage Component (HDFS)
* Resource Management Component (YARN)
* Processing Components (MapReduce)

YARN은 하둡 2.0에서 추가되었다. 클러스터가 HDFS와 MapReduce만으로 구성된다는 글을 본다면 그건 1.X 버전에 대한 설명이다.

<br/>

### Storage Component (HDFS)

HDFS는 Java로 쓰여진 파일시스템이다. 저사양의 많은 서버로 스토리지를 구성할 수 있기 때문에 기존의 대용량 서버에 비해 비용적으로 이점을 갖는다.

HDFS에서의 마스터 노드는 **Name Node**, 슬레이브 노드는 **Data Node**라 부른다.

#### 작동방식

* 각 파일들은 로드될 때 128MB 크기(2.0 기준)의 **블록**들로 쪼개진다.
* 각 블록은 여러(default: 3개) data node에 복제되어 저장된다. 
* name node는 metadata를 저장하여 어떤 data node가 어떤 데이터를 갖고 있는지 관리한다.

#### fsshell

hdfs를 쓰기 위한 gui 도구들도 여러가지 있지만 일단 원형인 콘솔 환경에서의 구동방식을 정확히 이해해야 한다. gui 도구에 대해서는 필요한 분들이 있다면 알아서 찾아보시길 바라며 여기서는 콘솔환경에서의 동작을 위한 fsshell에 대해서만 다루고자 한다. 
fsshell은 `hdfs dfs -명령어`의 포맷으로 사용한다. 참고로 설정파일이 제대로 작성되지 않은 상태에서는 동작하지 않을 수 있다***<u>(실습 진행 후 내용 추가할 것)</u>***.

* `hdfs dfs -ls`: HDFS의 홈 디렉토리의 파일들을 출력한다.
* `hdfs dfs -put test.txt test.txt`: 로컬 파일시스템의 test.txt를 HDFS에 복사한다.
* `hdfs dfs -get test.txt /user/pp/test.txt test.txt`: HDFS의 test.txt를 로컬 파일시스템으로 복사해온다.
* `get`과 `put`은 각각 `copyToLocal`과 `copyFromLocal`으로도 쓸 수 있다.

<br/>

### Resource Management Component (YARN) 

YARN(Yet Another Resource Negotiator)은 여러 data processing engine들(이를테면 MapReduce)이 하나의 cluster로 작동할 수 있게끔 묶어주는 역할을 한다.

#### 구성요소들

* Daemons

  노드에서 실행되는 프로그램을 데몬이라 한다.

  * **Resource Manager (RM)**

    마스터 노드에서 돌아가며 시스템자원(CPU, 메모리)을 슬레이브 노드에게 할당하여 관리한다.

  * **Node Manager (NM)**

    슬레이브 노드에서 돌아가며 RM과 계속 통신한다.

* **Containers**

  RM에 의해 생성되어 슬레이브 노드에게 정량의 자원(CPU, 메모리)을 제공한다. Application은 하나 이상의 컨테이너 위에서 작동한다.

* **Application Master (AM)**

  Application당 하나씩만 존재한다. 

  Task를 이행하기 위해 RM에게 Container를 추가요청한다.

#### 동작

* 클라이언트에서 파일(mydata)의 처리 요청이 들어온다.
* Application Master가 Name Node를 통해 mydata가 어디에 있는지 찾는다.
* Application Master가 RM에게 어떤 슬레이브 노드에게 얼마만큼의 자원을 할당해달라고 요청한다.
* RM이 요청받은 대로 컨테이너들을 만들어준다.
* Application Master가 Slave들에게 Job을 나눠 Task를 할당해주면 슬레이브노드들이 받은 컨테이너 안에서 Task를 처리한다. (Job Scheduler는 등장 안하나..?)
* 작업이 끝나면 Application Master가 RM에게 끝났음을 알려 자원을 회수하게 한다.

<br/>

### Processing Component (MapReduce)

여러 노드에게 일들을 나눠서 시키는 컴포넌트. 

#### Jobs & Tasks

* **Jobs**

  Job은 하나의 프로그램 자체다("A Job is a 'full program'").

  MapReduce2에서 job은 application이라고도 불린다.

* **Tasks**

  Job은 여러개의 Task로 잘려서 Mapper와 Reducer에게 할당된다. 

#### Phases

* **Mapping**

  각 Task는 하나의 HDFS 블록 위에서 실행된다(반드시 그런 것은 아니라고 한다).

  데이터를 key-value 형태로 매핑하는 일을 한다.

* **Shuffling and Sorting**

  Mapper들이 매핑해놓은 데이터를 한 데 섞은(shuffling) 뒤 key 값에 따라 정렬(sorting)한다.

* **Reducing**

  정리된 데이터를 받아 마지막으로 가공하여 최종 결과물을 내놓는다.

#### Example: Word Count

문장을 입력받아 글자의 등장 횟수를 세는 프로그램이다. 

* Input data

  "The cat sat on the mat <br/>
  the aardvark sat on the sofa"

* Mapping

  Mapper on Node1: {the: 1, cat: 1, sat: 1, on: 1, the: 1, mat: 1}

  Mapper on Node2: {the: 1, aardvark: 1, sat: 1, on: 1, the: 1, sofa: 1}

* Shuffling and Sorting

  {aardvark: [1], cat: [1], mat: [1], on: [1, 1], sat: [1, 1], sofa: [1], the: [1, 1, 1, 1]}

* Reducing (Reducer가 3개인 경우)

  {aardvark: 1, cat: 1}, {mat: 1, on: 2, sat: 2}, {sofa: 1, the: 4}

  {aardvark: 1, cat: 1, mat: 1, on: 2, sat: 2, sofa: 1, the: 4} -> HDFS에 저장


RDBMS는 데이터를 저장하는 방식이 체계적이긴 하지만 대신 유연하지 못하다. 그래서 `JOIN`은 RDBMS에서만 쓸 수 있는 문법인 동시에 `JOIN`이 없으면 RDBMS로 규모 있는 시스템을 설계할 수 없다. 그래서 혹자는 join을 관계형 데이터베이스의 꽃이라고도 부른다. 

내가 처음 `JOIN` 문법을 접했던 것은 1군사령부에서 소프트웨어 개발병으로 근무하던 때였다. 처음에 아주 간단한 웹서비스인 줄 알고 맡았던 [독서마라톤](https://enhanced.kr/postviewer/176)의 제작은 전산체계실로부터 계속 들어오는 요구사항에 의해 점점 규모가 커졌고, 기존에 만들어놨더 데이터베이스에서 그들의 요구사항을 수용하려면 `JOIN`을 사용할 수밖에 없었다.

이처럼 RDBMS를 쓰는 이상 `JOIN`은 간과할 수 없는 중요한 개념이다. 하지만 오랫동안 안 쓰면 개념 자체를 잊어먹는다는 치명적인 문제가 있다. 그러므로 본 글에서는 `JOIN`에 대해 제대로 정리해보는 시간을 가져보도록 하겠다.

Join 문법의 예시를 들기 위해서 다음과 같은 테이블 구조를 만들어놓고 시작하겠다.

```
mysql> select * from class;                  
+----------+------------+                    
| class_id | class_name |                    
+----------+------------+                    
|        1 | warrior    |                    
|        2 | magician   |                    
|        3 | rogue      |                    
|        4 | archer     |                    
+----------+------------+                    
4 rows in set (0.00 sec)                     
                                             
mysql> select * from game_character;         
+--------------+----------------+----------+ 
| character_id | character_name | class_id | 
+--------------+----------------+----------+ 
|            1 | 다잌스트라     |        2 |      
|            2 | 벨만           |        4 |   
|            3 | 반고흐         |        3 |    
|            4 | 살바도르달리   |        3 |       
|            5 | 크루스칼       |        2 |     
+--------------+----------------+----------+ 
5 rows in set (0.00 sec)                     
```



## Join

소개팅을 한다고 생각해보자. 그 어색한 자리에서 두 남녀(relations)는 서로의 공통점을 찾기 위해 최근에 본 영화, 카페에서 나오는 음악, 지금 먹고 있는 음식 등 수많은 화제(tuple)를 늘어놓을 것이다. 그러다 화제에서 공통된 관심사(attribute)를 찾으면 이를 주제로 골라(select) 대화를 이어나간다. 이처럼 Join은 두 릴레이션이 가진 모든 튜플의 조합을 늘어놓고 공통 attribute를 찾는 과정이다.

Join은 크게 **내부 조인(Inner Join)**, **외부 조인(Outer Join)**, 그리고 조인이라고 보기 애매한 **크로스 조인(Cross Join)**으로 나뉜다. 

### Join의 기본 문법

Join의 문법은 **명시적 표현법(explicit notation)**과 **묵시적 표현법(implicit notation)**으로 나뉜다. 두 표현 중 어느 쪽을 쓰더라도 [성능적으로는 별 차이가 없다](https://stackoverflow.com/questions/44917/explicit-vs-implicit-sql-joins)고 한다.

#### Explicit Notation

```sql
SELECT ~~~
FROM table1 [CROSS|INNER|NATURAL|OUTER|LEFT OUTER|RIGHT OUTER] JOIN table2
ON ~~~
```

#### Implicit Notation

```mysql
SELECT ~~~
FROM table1, table2
WHERE ~~~
```



## Cross Join (Cartesian Product)

모든 조합을 다 선택한다. 모든 `JOIN`은 `CROSS JOIN`을 베이스로 하여 이 중에서 조건(`ON`, `WHERE` / `SELECT`)에 맞는 행과 열을 추출한다고 보면 된다. 참고로 `CROSS JOIN`에 조건절을 추가하면 바로 `INNER JOIN`이 된다.

```mysql
SELECT *
FROM class CROSS JOIN game_character;
```

```mysql
SELECT *
FROM class, game_character;
```

```
+----------+------------+--------------+----------------+----------+
| class_id | class_name | character_id | character_name | class_id |
+----------+------------+--------------+----------------+----------+
|        1 | warrior    |            1 | 다잌스트라     |        2 |
|        2 | magician   |            1 | 다잌스트라     |        2 |
|        3 | rogue      |            1 | 다잌스트라     |        2 |
|        4 | archer     |            1 | 다잌스트라     |        2 |
|        1 | warrior    |            2 | 벨만           |        4 |
|        2 | magician   |            2 | 벨만           |        4 |
|        3 | rogue      |            2 | 벨만           |        4 |
|        4 | archer     |            2 | 벨만           |        4 |
|        1 | warrior    |            3 | 반고흐         |        3 |
|        2 | magician   |            3 | 반고흐         |        3 |
|        3 | rogue      |            3 | 반고흐         |        3 |
|        4 | archer     |            3 | 반고흐         |        3 |
|        1 | warrior    |            4 | 살바도르달리   |        3 |
|        2 | magician   |            4 | 살바도르달리   |        3 |
|        3 | rogue      |            4 | 살바도르달리   |        3 |
|        4 | archer     |            4 | 살바도르달리   |        3 |
|        1 | warrior    |            5 | 크루스칼       |        2 |
|        2 | magician   |            5 | 크루스칼       |        2 |
|        3 | rogue      |            5 | 크루스칼       |        2 |
|        4 | archer     |            5 | 크루스칼       |        2 |
+----------+------------+--------------+----------------+----------+
20 rows in set (0.00 sec)
```

위처럼 두 테이블을 cross join을 하게 되면 두 테이블의 튜플을 조합할 수 있는 대로 다 조합한 테이블이 출력된다. 위의 예에서는 $5 \times 4 = 20$개가 출력되었다.



## 내부 조인 (Inner Join)

조인은 기본적으로 내부 조인이다. 실제로 명시적 표현에서 그냥 `JOIN`을 쓰면 자동적으로 `INNER JOIN`이 실행된다. 

### 동등 조인 (Equi Join)

`JOIN`한 둘 이상의 테이블에서 공통된 튜플을 갖는 행을 모두 추출한다. 쿼리로 보는 게 이해하기 더 좋다.

```mysql
SELECT *
FROM class AS cl INNER JOIN game_character AS gc
ON cl.class_id = gc.class_id;
```

```mysql
SELECT *
FROM class AS cl, game_character AS gc
WHERE cl.class_id = gc.class_id;
```

두 쿼리 모두 결과는 같다.

```
+----------+------------+--------------+----------------+----------+
| class_id | class_name | character_id | character_name | class_id |
+----------+------------+--------------+----------------+----------+
|        2 | magician   |            1 | 다잌스트라     |        2 |
|        4 | archer     |            2 | 벨만           |        4 |
|        3 | rogue      |            3 | 반고흐         |        2 |
|        3 | rogue      |            4 | 살바도르달리   |        3 |
|        2 | magician   |            5 | 크루스칼       |        2 |
+----------+------------+--------------+----------------+----------+
5 rows in set (0.00 sec)
```

참고로 조건절에 부등호연산을 쓰면 동등조인이 아니다.

#### 자연 조인 (Natural Join)

동등 조인의 일종이다. 쓰지 않는 것을 추천한다.

```mysql
SELECT *
FROM class NATURAL JOIN game_character;
```

```
+----------+------------+--------------+----------------+
| class_id | class_name | character_id | character_name |
+----------+------------+--------------+----------------+
|        2 | magician   |            1 | 다잌스트라     |
|        4 | archer     |            2 | 벨만           |
|        3 | rogue      |            3 | 반고흐         |
|        3 | rogue      |            4 | 살바도르달리   |
|        2 | magician   |            5 | 크루스칼       |
+----------+------------+--------------+----------------+
5 rows in set (0.00 sec)
```

동등 조인과의 차이는 딱 2개가 있다.

1. 조건절이 필요 없이 조인하는 테이블들의 컬럼들 중 이름이 같은 애들을 자동으로 찾아 비교한다는 점
2. 중복된 열을 굳이 2번 출력하지 않는다는 점

2번 때문에 얼핏 자연조인이 동등조인보다 나아보이지만 1번 때문에 쓰지 않는 게 좋다. 우리의 예시에서 `class` 테이블에는 `class_name`이 있고, `game_character` 테이블에는 `character_name`이 있다. 하지만 이 둘은 둘 다 그냥 `name`이라고 이름지어놔도 전혀 이상하지 않다. 이 경우 `NATURAL JOIN`은 우리의 의도와는 달리 `EMPTY SET`을 리턴하게 된다.

### 자가 조인 (Self Join)

같은 테이블을 조인하는 경우다. 논리적으로 말이 되지 않는 경우지만, 테이블 따로 만들기 귀찮으니 우리의 `game_character` 테이블을 셀프조인해보도록 하겠다.

```mysql
SELECT gc1.*
FROM game_character gc1 INNER JOIN game_character gc2
ON gc1.class_id = gc2.character_id AND gc2.class_id = gc1.character_id;
```

```mysql
SELECT gc1.*
FROM game_character gc1, game_character gc2
WHERE gc1.class_id = gc2.character_id AND gc2.class_id = gc1.character_id;
```

```
+--------------+----------------+----------+
| character_id | character_name | class_id |
+--------------+----------------+----------+
|            3 | 반고흐         |        3 |
+--------------+----------------+----------+
1 row in set (0.00 sec)
```



## 외부 조인 (Outer Join)

눈치 챘는지는 모르겠지만 우리의 `game_character` 테이블에는 전사(warrior)를 선택한 사람이 아무도 없었다. 이 경우를 포함해서 보여줄 수 있는 확장된 `JOIN` 문법이 외부 조인이다.

참고로, 외부 조인은 묵시적 표현법이 없다.

### Left Outer Join

```mysql
SELECT * 
FROM class AS cl LEFT OUTER JOIN game_character AS gc
ON cl.class_id = gc.class_id;
```

```
+----------+------------+--------------+----------------+----------+
| class_id | class_name | character_id | character_name | class_id |
+----------+------------+--------------+----------------+----------+
|        2 | magician   |            1 | 다잌스트라     |        2 |
|        4 | archer     |            2 | 벨만           |        4 |
|        3 | rogue      |            3 | 반고흐         |        3 |
|        3 | rogue      |            4 | 살바도르달리   |        3 |
|        2 | magician   |            5 | 크루스칼       |        2 |
|        1 | warrior    |         NULL | NULL           |     NULL |
+----------+------------+--------------+----------------+----------+
6 rows in set (0.00 sec)
```

`FROM`절에서 `class`가 왼쪽, `game_character`가 오른쪽에 있음에 주목하자. `LEFT OUTER JOIN`은 왼쪽 테이블에 매칭되는 짝이 없더라도 일단 다 출력하고 본다.

### Right Outer Join

```mysql
SELECT *
FROM class AS cl RIGHT OUTER JOIN game_character AS gc
ON cl.class_id = gc.class_id;
```

```
+----------+------------+--------------+----------------+----------+
| class_id | class_name | character_id | character_name | class_id |
+----------+------------+--------------+----------------+----------+
|        2 | magician   |            1 | 다잌스트라     |        2 |
|        2 | magician   |            5 | 크루스칼       |        2 |
|        3 | rogue      |            3 | 반고흐         |        3 |
|        3 | rogue      |            4 | 살바도르달리   |        3 |
|        4 | archer     |            2 | 벨만           |        4 |
+----------+------------+--------------+----------------+----------+
5 rows in set (0.00 sec)
```

`game_character`는 매칭되는 짝이 모두 있으므로 `INNER JOIN`과 마찬가지로 5건만 출력되었다. 

### Full Outer Join

`LEFT OUTER JOIN`과 `RIGHT OUTER JOIN`의 합집합이라고 생각하면 된다. ANSI SQL 문법 표준에는 `FULL OUTER JOIN`이 [정의되어 있지만](https://en.wikipedia.org/wiki/Join_(SQL)), MySQL에는 없어서 다음처럼 `LEFT OUTER JOIN`과 `RIGHT OUTER JOIN`을 `UNION`해서 써야 한다.

```mysql
SELECT *
FROM class AS cl LEFT OUTER JOIN game_character AS gc
ON cl.class_id = gc.class_id
UNION
SELECT *
FROM class AS cl RIGHT OUTER JOIN game_character AS gc
ON cl.class_id = gc.class_id;
```


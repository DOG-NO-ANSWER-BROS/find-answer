# INNER JOIN vs OUTER JOIN
이 글은 MySql을 기준으로 작성되었습니다.
## 배경 지식
주제에 들어가기 전, 알면 좋을 것:
### 곱집합과 교차결합
집합 X = [1, 2, 3]
집합 Y = [a, b, c] 일 때,
집합 X와 Y의 곱집합:
| | a | b | c|
|--|--|--|--|
|1| a,1 | b,1 | c,1 |
|2| a,2 | b,2 | c,2 |
|3| a,3 | b,3 | c,3 |
```sql
SELECT * FROM X, Y;
```
위와 같은 쿼리를 실행하면 두 테이블을 이용하여 다음과 같은 결과를 얻을 수 있다:
| Y | X |
| a | 1 |
| b | 1 |
| c | 1 |
| a | 2 |
| b | 2 |
| c | 2 |
| a | 3 |
| b | 3 |
| c | 3 |
그리고 이를 **교차결합**이라고 한다.
### PRIMARY KEY, FOREIGN KEY
#### PRIMARY KEY (기본키)
관계형 데이터베이스에서는 테이블의 데이터가 유일한 값을 가지도록 권장함 (마치 수학의 집합).
그래서 데이터가 가지고 있는 것이 바로 기본키.
#### FOREIGN KEY (외부키)
다른 테이블의 기본키를 참조하는 열.
### UNION
JOIN이 여러 테이블을 하나로 합쳐 열을 추가하는 행위라면,
UNION은 행을 늘린다. 즉, A 테이블에서 얻은 정보(예를 들어 라면 이름)를
B 테이블에서 얻은 정보(예를 들어 초콜릿 이름)와 UNION을 해서 '음식 이름'으로 행을 합칠 수 있다.
(이 때, 합칠 데이터의 자료형이 같아야 한다.)
위의 설명 예시 쿼리:
```sql
SELECT name FROM RAMEN
UNION
SELECT name FROM CHOCOLATE
```
## 내부결합과 외부결합
### 내부결합
교차결합으로 결합 조건을 지정하여 검색한다.
결합하는 테이블 모두 값이 존재할 때 포함된다. 즉, 어느 한 쪽에만 존재하는 데이터행은 다루지 않는다.
### 외부결합
교차결합으로 결합 조건을 지정하여 검색한다.
키 포인트는 **외부결합은 '어느 한 쪽에만 존재하는 데이터행을 어떻게 다룰지'를 변경할 수 있다는 것.**
## INNER JOIN 그리고 OUTER JOIN
### INNER JOIN
내부결합을 위해 사용하는 구문. **그냥 JOIN을 쓰면 INNER JOIN을 사용하는 것과 동일하다.**
```sql
SELECT 
  employee.name, 
  detail.age 
FROM 
  employee 
  INNER JOIN detail ON employee.id = detail.id;
```
### OUTER JOIN
외부결합을 위해 사용하는 구문. LEFT OUTER JOIN, RIGHT OUTER JOIN 그리고 FULL OUTER JOIN이 있다.
#### LEFT / RIGHT
앞서 외부 결합은 어느 한 쪽에만 존재하는 데이터행을 어떻게 다룰지를 변경할 수 있다고 했다.
LEFT와 RIGHT은 그 '어떻게'를 의미한다.
##### 👈 LEFT
왼쪽 테이블을 기준으로 데이터를 정렬한다. 즉, 왼쪽 테이블에 값이 존재하면, 오른쪽 테이블에 값이 없어도 결과에 포함된다.
```sql
SELECT
  employee.name,
  detail.age
 FROM
  employee
  LEFT OUTER JOIN detail ON employee.id = detail.id;
```
위와 같은 예시로 봤을 때, 해당 경우를 생각해보자:
```
employee 검프 - id : 1, 나이 : 27
employee NULL - id : 2, 나이 : 26
employee 02 - id : 3, 나이 : NULL
(이름은 employee 테이블에, 그리고 나이는 detail 테이블에 있다)
```
이 경우 LEFT OUTER JOIN을 했으므로 다음과 같은 결과가 나온다:
|name|age|
|--|--|
|검프|27|
|02|NULL|

왼쪽의 테이블을 기준으로 값이 존재하는 행을 가지고 왔기 때문이다.

##### 👉 RIGHT
오른쪽 테이블을 기준으로 데이터를 정렬한다. 즉, 오른쪽 테이블에 값이 존재하면, 오른쪽 테이블에 값이 없어도 결과에 포함된다.
```sql
SELECT
  employee.name,
  detail.age
 FROM
  employee
  RIGHT OUTER JOIN detail ON employee.id = detail.id;
```
이번에는 위와 같이 RIGHT OUTER JOIN을 아까와 동일한 상황에서 해본다:
```
employee 검프 - id : 1, 나이 : 27
employee NULL - id : 2, 나이 : 26
employee 02 - id : 3, 나이 : NULL
(이름은 employee 테이블에, 그리고 나이는 detail 테이블에 있다)
```
이 경우 RIGHT OUTER JOIN을 했으므로 다음과 같은 결과가 나온다:
|name|age|
|--|--|
|검프|27|
|NULL|26|

오른쪽 테이블을 기준으로 값이 존재하는 행을 가지고 왔기 때문이다.
#### FULL OUTER JOIN
[FULL OUTER JOIN에 대한 좋은 포스트](https://stackoverflow.com/questions/4796872/how-to-do-a-full-outer-join-in-mysql)
**MySql에서는 FULL OUTER JOIN을 직접적으로 실행해주는 구문이 없다.** 따라서 이 기능을 원한다면 UNION을 활용하여 직접 나타내야한다.

FULL은 결국 LEFT와 RIGHT을 둘 다 원한다는 의미다. 즉, 그 어떤 정보도 누락하지 않는 대신, 서로 다른 열에 NULL 값이 있더라도 다른 정보로 인식하여 두 테이블로부터 데이터를 가지고 오는 것이다.

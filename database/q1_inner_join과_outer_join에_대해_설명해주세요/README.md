# Inner Join, Outer Join

## 배경지식
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

### UNION
JOIN이 여러 테이블을 하나로 합쳐 열을 추가하는 행위라면,
UNION은 행을 늘린다.
```sql
SELECT name FROM RAMEN
UNION
SELECT name FROM CHOCOLATE
```

## Join
조인이란 2개 이상의 테이블을 서로 엮어 조회하는 것.

## Join의 종류

- Inner Join : 서로 연관된 내용만 검색하는 조인 방법
- Outer Join : 한 쪽에는 데이터가 있고 한 쪽에는 데이터가 없는 경우, 데이터가 있는 쪽 테이블의 내용을 전부 출력하는 방법.
- Cross Join : 카디션 곱이라고도 하며 조인되는 두 테이블에서 곱집합을 만든다.
- Self Join : 테이블에서 자기자신을 조인하는 것

## Inner Join
`Inner Join`은 서로 매칭 되는 것만 엮어서 조회하는 것. 이때 둘다 null이 아닌 것 만 엮어서 조회한다.

## Outer Join
`Outer Join`은 매칭 뿐만 아니라 매칭되지 않는 데이터도 조회한다. Outer Join에는 `Left Outer Join`, `Right Outer Join`, `Full Outer Join` 이 있다. Left, Right은 매칭이 되지 않는 데이터도 조회할 테이블 방향이다.

(*MySQL에는 Full Outer Join 이 없다. Left Outer Join 과 Right Outer Join 을 UNION 하여 사용한다.*)

## 실습 예제
### member table
![image](https://user-images.githubusercontent.com/63634505/116237088-c2353080-a79a-11eb-80e5-f0d980a07a0e.png)

### user table
![image](https://user-images.githubusercontent.com/63634505/116237469-38399780-a79b-11eb-8ca4-d02e108b5f68.png)
---
## Inner Join
###  Team테이블과 Member테이블애 매칭되는 데이터 조회 (Inner생략 가능)

![https://user-images.githubusercontent.com/48986787/116079907-5fc52d00-a6d3-11eb-9b30-f8fc24ae46c6.png](https://user-images.githubusercontent.com/48986787/116079907-5fc52d00-a6d3-11eb-9b30-f8fc24ae46c6.png)

```sql
SELECT * FROM team 
INNER JOIN member ON team.team_id = member.team_id;
```

![https://user-images.githubusercontent.com/48986787/116078991-440d5700-a6d2-11eb-98e9-cb002b55029a.png](https://user-images.githubusercontent.com/48986787/116078991-440d5700-a6d2-11eb-98e9-cb002b55029a.png)

팀 2(bemin)에는 현재 아무도 들어가 있지 않기 때문에, 결과값이 나오지 않는다.

## Outer Join
### 1. Team의 모든 데이터를 매칭여부에 관계없이 조회
![https://user-images.githubusercontent.com/48986787/116080815-76b84f00-a6d4-11eb-9141-edbe387c5c92.png](https://user-images.githubusercontent.com/48986787/116080815-76b84f00-a6d4-11eb-9141-edbe387c5c92.png)

```sql
SELECT * FROM team 
LEFT OUTER JOIN member ON team.team_id = member.team_id;
```

![https://user-images.githubusercontent.com/48986787/116080616-4670b080-a6d4-11eb-8565-c79de5e79e9b.png](https://user-images.githubusercontent.com/48986787/116080616-4670b080-a6d4-11eb-8565-c79de5e79e9b.png)

팀 2(bemin)에 아무도 없지만, **매칭되는 데이터의 유무에 관계없이 데이터를 가져온다.**

### 2. Team에만 존재하는 데이터를 조회
![https://user-images.githubusercontent.com/48986787/116083719-ebd95380-a6d7-11eb-8a1f-6568183c9d61.png](https://user-images.githubusercontent.com/48986787/116083719-ebd95380-a6d7-11eb-8a1f-6568183c9d61.png)

```sql
SELECT * FROM team 
LEFT OUTER JOIN member ON team.team_id = member.team_id
WHERE member.team_id is null;
```

![https://user-images.githubusercontent.com/48986787/116082469-65704200-a6d6-11eb-86bc-33862c0caeee.png](https://user-images.githubusercontent.com/48986787/116082469-65704200-a6d6-11eb-86bc-33862c0caeee.png)

### 3. Member의 모든 데이터를 매칭여부에 관계없이 조회

![https://user-images.githubusercontent.com/48986787/116082031-e844cd00-a6d5-11eb-98cd-dedf1f7b9d2b.png](https://user-images.githubusercontent.com/48986787/116082031-e844cd00-a6d5-11eb-98cd-dedf1f7b9d2b.png)

```sql
SELECT * FROM member 
LEFT OUTER JOIN team ON member.team_id = team.team_id;
```

![https://user-images.githubusercontent.com/48986787/116081953-d2cfa300-a6d5-11eb-8f56-ff987b10c417.png](https://user-images.githubusercontent.com/48986787/116081953-d2cfa300-a6d5-11eb-8f56-ff987b10c417.png)

현재 member는 다 team에 들어가있어서 null이 나오지 않았다.

### CROSS JOIN

모든 데이터를 조회할 수 있다.(실무에선 잘 사용하지 않는다)

![https://user-images.githubusercontent.com/48986787/116083770-fd226000-a6d7-11eb-8927-41db15559a89.png](https://user-images.githubusercontent.com/48986787/116083770-fd226000-a6d7-11eb-8927-41db15559a89.png)

```sql
SELECT * FROM team 
CROSS JOIN member;
```

![https://user-images.githubusercontent.com/48986787/116083943-32c74900-a6d8-11eb-8aeb-68c75882761a.png](https://user-images.githubusercontent.com/48986787/116083943-32c74900-a6d8-11eb-8aeb-68c75882761a.png)
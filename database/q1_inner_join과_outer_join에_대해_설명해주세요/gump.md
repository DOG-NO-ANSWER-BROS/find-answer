# q1_**inner join과 outer join에 대해 설명해주세요**

조인(Join)이란 2개 이상의 테이블을 서로 엮어 조회하는 것이에요.

**Inner Join**은 **서로 매칭되는 (둘다 null이 아닌)것**만 엮어서 조회해요.

**Outer Join**은 **매칭 뿐만 아니라 매칭되지 않는 데이터도 조회**해요 (베이스 테이블의 컬럼에 해당하는 값이 없어도 null 로 불러옴)

> 이해가 쉽게 되지 않으니 예제와 그림을 통해 설명할게요

<br>

## **실습 데이터**

```sql
CREATE TABLE IF NOT EXISTS team (
    team_id VARCHAR (36) NOT NULL,
    name VARCHAR (64) NOT NULL,
    PRIMARY KEY (team_id)
    );

CREATE TABLE IF NOT EXISTS member (
    member_id VARCHAR(36) NOT NULL,
    name VARCHAR(64) NOT NULL,
    team_id VARCHAR(36) NOT NULL,
    PRIMARY KEY (member_id),
    FOREIGN KEY (team_id)
        REFERENCES team (team_id)
        ON DELETE CASCADE ON UPDATE CASCADE
);
    
INSERT INTO team value(1, "woowa");
INSERT INTO team value(2, "baemin");

INSERT INTO member value(1, "gump", 1);
INSERT INTO member value(2, "kim-gump", 1);
```

Team과 Member가 1:N 으로 연관되어있고, Member가 Team의 Id를 FK로 가지고 있어요.

<br>

## **InnerJoin**

### **1. Team테이블과 Member테이블에 매칭되는 데이터 조회 (Inner생략 가능)**

![https://user-images.githubusercontent.com/48986787/116079907-5fc52d00-a6d3-11eb-9b30-f8fc24ae46c6.png](https://user-images.githubusercontent.com/48986787/116079907-5fc52d00-a6d3-11eb-9b30-f8fc24ae46c6.png)

```sql
SELECT * 
FROM team 
INNER JOIN member ON team.team_id = member.team_id;
```

![https://user-images.githubusercontent.com/48986787/116078991-440d5700-a6d2-11eb-98e9-cb002b55029a.png](https://user-images.githubusercontent.com/48986787/116078991-440d5700-a6d2-11eb-98e9-cb002b55029a.png)

팀 2(baemin)에는 현재 아무도 들어가 있지 않기 때문에, 결과값이 나오지 않아요.(~~제가 이팀에 들어가겠습니다!!~~)

<br>

## **OuterJoin**

### **1. Team의 모든 데이터를 매칭여부에 관계없이 조회**

![https://user-images.githubusercontent.com/48986787/116080815-76b84f00-a6d4-11eb-9141-edbe387c5c92.png](https://user-images.githubusercontent.com/48986787/116080815-76b84f00-a6d4-11eb-9141-edbe387c5c92.png)

```sql
SELECT * 
FROM team 
LEFT OUTER JOIN member ON team.team_id = member.team_id;
```

![https://user-images.githubusercontent.com/48986787/116108216-ab390480-a6ee-11eb-8a60-0fa990a48442.png](https://user-images.githubusercontent.com/48986787/116108216-ab390480-a6ee-11eb-8a60-0fa990a48442.png)

팀 2(baemin)에 아무도 없지만, **매칭되는 데이터의 유무에 관계없이 데이터를 가져와요.** 

### **2. Team에만 존재하는 데이터를 조회**

![https://user-images.githubusercontent.com/48986787/116083719-ebd95380-a6d7-11eb-8a1f-6568183c9d61.png](https://user-images.githubusercontent.com/48986787/116083719-ebd95380-a6d7-11eb-8a1f-6568183c9d61.png)

```sql
SELECT * 
FROM team 
LEFT OUTER JOIN member ON team.team_id = member.team_id
WHERE member.team_id is null;
```

![https://user-images.githubusercontent.com/48986787/116108290-bee46b00-a6ee-11eb-9f80-3fa71880a5df.png](https://user-images.githubusercontent.com/48986787/116108290-bee46b00-a6ee-11eb-9f80-3fa71880a5df.png)

### **3. Member의 모든 데이터를 매칭여부에 관계없이 조회**

![https://user-images.githubusercontent.com/48986787/116082031-e844cd00-a6d5-11eb-98cd-dedf1f7b9d2b.png](https://user-images.githubusercontent.com/48986787/116082031-e844cd00-a6d5-11eb-98cd-dedf1f7b9d2b.png)

```sql
SELECT * 
FROM member 
RIGHT OUTER JOIN team ON member.team_id = team.team_id;
```

![https://user-images.githubusercontent.com/48986787/116081953-d2cfa300-a6d5-11eb-8f56-ff987b10c417.png](https://user-images.githubusercontent.com/48986787/116081953-d2cfa300-a6d5-11eb-8f56-ff987b10c417.png)

현재 member는 다 team에 들어가있어서 null이 나오지 않았어요.

<br>

## **CROSS JOIN**

모든 데이터를 조회할 경우가 있어요(실무에선 잘 안쓴데요).

![https://user-images.githubusercontent.com/48986787/116083770-fd226000-a6d7-11eb-8927-41db15559a89.png](https://user-images.githubusercontent.com/48986787/116083770-fd226000-a6d7-11eb-8927-41db15559a89.png)

```sql
SELECT * 
FROM team 
CROSS JOIN member;
```

![https://user-images.githubusercontent.com/48986787/116108421-d91e4900-a6ee-11eb-8473-dd925f154eca.png](https://user-images.githubusercontent.com/48986787/116108421-d91e4900-a6ee-11eb-8473-dd925f154eca.png)
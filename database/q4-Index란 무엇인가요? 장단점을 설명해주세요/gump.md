# 정리

## Index란 ?

(검색을 위해) `특정한 규칙`대로 부여된 `특정한 대상`을 가르키는 정보예요. 쉽게 말해 데이터가 있는 위치를 특정할 수 있는 정보라 말할 수 있어요. 
책의 목차, 찾아보기, 주민등록번호, 학번 등등이 인덱스의 예라 볼 수 있어요. 책에 원하는 정보에 대한 인덱스가 없다면, 페이지를 하나하나 찾아봐야해요. 

### 인덱스의 관리

DBMS는 index를 항상 최신의 정렬된 상태로 유지해야 원하는 값을 빠르게 탐색할 수 있어요. 그렇기 때문에 인덱스가 적용된 컬럼에 INSERT, UPDATE, DELETE가 수행된다면 각각 다음과 같은 연산을 추가적으로 해주어야 하며 그에 따른 오버헤드가 발생해요.

- INSERT: 새로운 데이터에 대한 인덱스를 추가함
- DELETE: 삭제하는 데이터의 인덱스를 사용하지 않는다는 작업을 진행함
- UPDATE: 기존의 인덱스를 사용하지 않음 처리하고, 갱신된 데이터에 대해 인덱스를 추가함

## 인덱스(Index)의 장점과 단점

### 장점

테이블을 조회하는 속도와 성능을 향상 시킬 수 있어요.

### 단점

인덱스를 관리하기 위해 DB의 약 10%에 해당하는 저장공간이 필요해요. 
CREATE, DELETE, UPDATE가 빈번한 속성에 인덱스를 걸게 되면 인덱스의 크기가 비대해져서 성능이 오히려 저하될 수 있어요.

## Refer

[https://www.youtube.com/watch?v=NkZ6r6z2pBg&t=1s](https://www.youtube.com/watch?v=NkZ6r6z2pBg&t=1s)
[https://www.youtube.com/watch?v=P5SZaTQnVCA&t=1s](https://www.youtube.com/watch?v=P5SZaTQnVCA&t=1s)
[https://www.baeldung.com/jpa-indexes](https://www.baeldung.com/jpa-indexes)
[https://www.codecademy.com/articles/sql-indexes](https://www.codecademy.com/articles/sql-indexes)

<br> 

# 질문

## 나만 알만한 것

### 질문

스프링 데이터 jpa 인덱스를 어떻게 관리할까?
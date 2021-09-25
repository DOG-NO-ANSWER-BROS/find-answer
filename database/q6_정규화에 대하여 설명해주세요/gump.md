# 정리

> 데이터베이스 정규화(Normalization)는 데이터 중복을 줄이고 데이터 무결성을 개선하기 위해 정규화 단계에 따라 관계형 데이터 베이스를 구조화하는 프로세스예요.

## 이상 현상(Anomly)

정규화되지 않은 관계에서 관계를 수정(업데이트, 삽입/삭제) 하려고 하면, 아래와 같은 부작용이 발생할 수 있어요.

### **업데이트 이**상

![https://user-images.githubusercontent.com/48986787/134591966-1d5fa0ba-d6ee-463f-ba4c-67980d3928f0.png](https://user-images.githubusercontent.com/48986787/134591966-1d5fa0ba-d6ee-463f-ba4c-67980d3928f0.png)

업데이트 후 동일한 정보가 여러 행에 표시될 수 있어요.

### **삽입 이상**

![https://user-images.githubusercontent.com/48986787/134592391-01ff46a1-5701-4f56-a825-ada7aaba7b74.png](https://user-images.githubusercontent.com/48986787/134592391-01ff46a1-5701-4f56-a825-ada7aaba7b74.png)

테이블에 값을 못넣게 될 수 있어요.
위 그림에서 아직 어떤 과정도 지정되지 않은 교수의 경우, 과정 코드를 null로 설정하는 경우를 제외하고는 기록 할 수 없어요.

### 삭제 이상

![https://user-images.githubusercontent.com/48986787/134593139-77f5d08d-a617-463e-a29a-b6af327988b4.png](https://user-images.githubusercontent.com/48986787/134593139-77f5d08d-a617-463e-a29a-b6af327988b4.png)

위 그림에서 일시적으로 어떤 과정에도 배정되지 않으면, row자체가 사라질 수 있어요 

위와 같은 문제들을 해결하기 위해 정규화 단계가 도입되었어요.

<br>

## 정규화(Normalization) 단계별 적용

데이터베이스 정규화(Normalization)는 데이터 중복을 줄이고 데이터 무결성을 개선하기 위해 정규화 단계에 따라 관계형 데이터 베이스를 구조화하는 프로세스예요.

즉, 정규화는 관계형 데이터베이스 테이블을 상위 레벨 정규화로 설계하는 데 사용되는 데이터베이스 설계 기술이에요. 레벨 조건에 충족하지 못하면, 다음 레벨의 정규화를 사용할 수 없어요.
이게 무슨 말이냐면, 비정규화 형식의 데이터를 가지고, 최고 레벨의 정규화를 달성하는 것을 목표로 첫번째 레벨부터 정규화가 충족되도록 하는 것이에요.

### 초기 데이터

![https://user-images.githubusercontent.com/48986787/134595786-e0d0f3ad-8960-4cc2-abf5-b83724b4ca7d.png](https://user-images.githubusercontent.com/48986787/134595786-e0d0f3ad-8960-4cc2-abf5-b83724b4ca7d.png)

관계형 모델을 준수하기 위해 행을 고유하기 식별하는 기본키가 있어야 해요. 책의 제목이 같을 수는 있지만, ISBN 번호는 책을 고유하게 식별하므로 기본키로 사용할 수 있어요.

![https://user-images.githubusercontent.com/48986787/134596038-449b41cb-8431-48e8-8b59-b455bf4c3585.png](https://user-images.githubusercontent.com/48986787/134596038-449b41cb-8431-48e8-8b59-b455bf4c3585.png)

### 제 1 정규화(1NF)

테이블의 `각 column에 하나의 값`이 있도록 분해해요(원자 값, Atomic Value). 
값 집합이나, 중복을 허용하지 않아요.
이를 위해 이전 테이블에서 값 집합인 Subject를 테이블로 분리해요

![https://user-images.githubusercontent.com/48986787/134596629-8c96b5b6-cd06-4408-9e17-19df565abed1.png](https://user-images.githubusercontent.com/48986787/134596629-8c96b5b6-cd06-4408-9e17-19df565abed1.png)

Subject테이블에서 foreign키가 추가 되었어요. 이는 Book 테이블의 기본키를 참조해요.
결론적으로 정규화 되지 않은 1개의 테이블 대신, 1NF를 준수하는 테이블이 두개가 됐어요.

### 제 2 정규화(2NF)

제1 정규화를 진행한 테이블에 대해 완전 함수 종속을 만족하도록 테이블을 분해해요.
완전 함수 종속이란 기본키의 부분집합이 결정자가 되어선 안된다는 것을 뜻해요.

Book 테이블에서 복합키{Title, Format}가 기본키가 될 수 있기에, 아래와 같이 변형시킬 수 잇어요.

![https://user-images.githubusercontent.com/48986787/134598536-a18b16eb-5893-4db8-8466-a2d8e2de95d6.png](https://user-images.githubusercontent.com/48986787/134598536-a18b16eb-5893-4db8-8466-a2d8e2de95d6.png)

후보 키의 일부가 아닌 모든 속성은 {Title}에 종속되지만, Price는 Format에 종속돼요. 2NF를 준수하고 중복을 제거하려면 후보 키가 아닌 모든 속성이 후보 키의 일부가 아니라 전체 후보 키에 종속 되어야 해요.
이 테이블을 정규화 하려면, {Title}을 (단수) 후보 키(기본 키)로 만들어 후보 키가 아닌 모든 속성이 전체 후보 키에 종속되도록 하고, Price를 별도의 테이블로 제거하여 Format에 대한 종속성을 가지게 해요.

![https://user-images.githubusercontent.com/48986787/134598765-87612cc5-e0f5-4a74-b748-3ccef8b55e32.png](https://user-images.githubusercontent.com/48986787/134598765-87612cc5-e0f5-4a74-b748-3ccef8b55e32.png)

### 제 3 정규화(3NF)

제2 정규화를 진행한 테이블에 대해 이행적 종속을 없애도록 테이블을 분해해요.
이행적 종속이란 A -> B, B -> C가 성립할 때 A -> C가 성립되는 것을 의미해요.

Book 테이블에는 여전히 이행적 종속이 있어요. Author Nationality는 Author에 종속되고, 이는 Title에 종속되기 때문에요. Genre 또한 마찬가지에요. Genre Name은 Title에 종속된 Genre ID에 종속 돼요. 

Author Nationality, Gere Name을 각각의 테이블에 배치하여 이행적 종속을 없애요.

![https://user-images.githubusercontent.com/48986787/134599051-12df7ef7-9f63-4e33-8543-f90bb7201f7e.png](https://user-images.githubusercontent.com/48986787/134599051-12df7ef7-9f63-4e33-8543-f90bb7201f7e.png)

### BCNF 정규화(Boyce-Codd Normal FORM)

제3 정규화를 진행한 테이블에 대해 모든 결정자가 후보키가 되도록 테이블을 분해해요. 
위에서 3정규화를 진행하며 BCNF를 당설하게 했어요. 

<br> 

# 질문

## 진짜 궁금한 것

JPA에서 정규화는 언제 적용될까?

### 질문

초기 엔티티를 설계할 때, 정규화가 이미 진행 된 상태의 엔티티를 고려하곤 했어요.(PK, UK 등)
정규화를 생각하지 않았던 이유는 이미 엔티티들을 분리를 어느정도 했고, 개발한 서비스가 그렇게 크지 않았기 때문이에요. 
이번에 정규화를 공부하며 이런 것들을 항상 고려하고, 설계를 진행해야 함을 알게됐어요.
질문에 대한 답은, 데이터베이스 구조  설계시 라고 말할 것 같아요.(JPA는 관계형 데이터베이스를 사용하는 방식을 정의해논 인터페이스이기 때문)

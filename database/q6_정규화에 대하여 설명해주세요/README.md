## 정규화란?

관계형 데이터베이스의 설계에서 중복을 최소화하게 데이터를 구조화 하는 프로세스. 데이터의 중복성을 제거, 삽입/삭제/갱신 이상을 제거하는 것이 핵심

## 데이터베이스 정규화의 목적

- 중복 데이터를 최소화하여 데이터 불일치 위험 최소화
- 수정, 삭제 시 이상현상을 최소화하여 데이터 구조의 안정성을 최대화
- 어떠한 릴레이션이라도 데이터베이스 내에서 표현 가능하게 만든다
- 데이터 삽입시 릴레이션 재구성에 대한 필요성을 줄인다
- 효과적인 검색 알고리즘을 생성할 수 있다

## 이상현상(Anomaly)
1. 삭제 이상: 튜플 삭제 시 같이 저장된 다른 정보까지 연쇄적으로 삭제되는 현상
2. 삽입 이상: 튜플 삽입 시 특정 속성에 해당하는 값이 없어 NULL을 입력해야 하는 현상
3. 수정 이상: 튜플 수정 시 중복된 데이터의 일부만 수정되어 일어나는 데이터 불일치 현상

## 정규화 과정
### 제 1 정규형 (First Normal Form, 1NF)
* 관계형 데이터베이스의 테이블에는 하나의 셀에 하나의 값만 저장할 수 있다는 제약이 있다.
* 그래서 반복되는 데이터를 가로(열 방향)가 아닌 세로(행 방향)로 늘린다. 이것이 제 1 정규형의 1단계.
* 테이블을 분리한다. 이 때 중복된 값이 존재하지 않는 열을 사용하여 기본키로 지정한다.

#### 조건
1. 어떤 Relation에 속한 모든 Domain이 원자값만으로 되어 있다.
2. 모든 attribute에 반복되는 그룹이 나타나지 않는다.
3. 기본 키를 사용하여 관련 데이터의 각 집합을 고유하게 식별할 수 있어야 한다.

#### 사례
<img width="747" alt="Screen Shot 2021-09-24 at 8 42 43 AM" src="https://user-images.githubusercontent.com/48251668/134597687-e09bd711-aae2-447a-a280-0e167d7d1d23.png">
* Telephone Number 가 원자값으로 이루어져 있지 않다.
<img width="752" alt="Screen Shot 2021-09-24 at 8 43 20 AM" src="https://user-images.githubusercontent.com/48251668/134597719-a357986d-2544-4014-b795-c9fd2fd6514f.png">
* 먼저 열 방향으로 늘려서 원자값을 만들어본다. 하지만 반복되는 그룹이 만들어졌다.
<img width="739" alt="Screen Shot 2021-09-24 at 8 44 04 AM" src="https://user-images.githubusercontent.com/48251668/134597768-724517c6-e854-4298-8e00-3af361117547.png">
그래서 행 방향으로 반복되는 데이터를 늘린다.
<img width="741" alt="Screen Shot 2021-09-24 at 8 44 36 AM" src="https://user-images.githubusercontent.com/48251668/134597800-dfa72b14-8cd2-4932-948e-5f708507abf1.png">
마지막으로 테이블을 나눈다.

### 제 2 정규형 (Second Normal Form, 2NF)
* 데이터가 중복하는 부분을 찾고 테이블을 분할해 나간다. (제 1 정규화에서 테이블에 기본키를 작성한 것과 같은 방법)
* 기본키에 의해 특정되는 열과 그렇지 않은 열로 나누는 것으로 정규화가 이루어진다.

#### 조건
* 모든 컬럼이 완전 함수적 종속을 만족한다.
##### 함수적 종속이란?
어떤 X 의 값에 따라 정해지는 Y 값이 있을 때 Y를 X에 대해 함수적 종속이라고 한다. 예를 들어 주민등록 번호 X가 있다면 그것에 의해 정해지는 그 주민등록 번호 주인의 이름인 Y가 있다.

#### 사례
<img width="740" alt="Screen Shot 2021-09-24 at 8 47 53 AM" src="https://user-images.githubusercontent.com/48251668/134598014-aaf25de8-da34-418a-a3af-a5d564ac56e7.png">
`Manufacturer`와 `Model`을 알면 `Model Full Name`을 알 수 있다. 하지만 `Manufacturer Country`는 아니다.
<img width="739" alt="Screen Shot 2021-09-24 at 8 49 22 AM" src="https://user-images.githubusercontent.com/48251668/134598122-2e689965-448e-44fb-b3f2-ea87208e7636.png">
따라서 이와 같은 상황에서 부분 함수 종속을 제거하여 아래의 그림처럼 분리해본다.
<img width="746" alt="Screen Shot 2021-09-24 at 8 49 47 AM" src="https://user-images.githubusercontent.com/48251668/134598158-b124ee9b-a2f6-43c9-86ae-b91df9420428.png">
<img width="704" alt="Screen Shot 2021-09-24 at 8 50 15 AM" src="https://user-images.githubusercontent.com/48251668/134598205-7aab22fe-6473-446d-ba11-7e064a1984f3.png">

### 제 3 정규형 (Third Normal Form, 3NF)

#### 조건
1. Relation이 제 2 정규화 되어 있다.
2. 기본키(primary key)가 아닌 속성(attribute)들은 기본키에만 의존해야 한다.

#### 사례
<img width="738" alt="Screen Shot 2021-09-24 at 8 52 17 AM" src="https://user-images.githubusercontent.com/48251668/134598347-91b830e7-7782-41b6-87c2-c344e2344ad8.png">
* `Winner Date of Birth`는 기본키가 아닌 속성인 `Winner`를 거쳐서 `Tournament`와 `Year`에 의존하고 있다. 이는 3NF를 위반한 것.
* {`Tournament`, `Year`} 가 후보키다.
<img width="757" alt="Screen Shot 2021-09-24 at 8 54 17 AM" src="https://user-images.githubusercontent.com/48251668/134598505-cec30db2-da2c-4632-9b9e-89d2ace712c6.png">
* 테이블을 나누어 3NF를 만족하도록 한다.

## 질문

- JPA에서 정규화는 언제 적용될까?


초기 엔티티를 설계할 때, 정규화가 이미 진행 된 상태의 엔티티를 고려하곤 했어요.(PK, UK 등)
정규화를 생각하지 않았던 이유는 이미 엔티티들을 분리를 어느정도 했고, 개발한 서비스가 그렇게 크지 않았기 때문이에요.
이번에 정규화를 공부하며 이런 것들을 항상 고려하고, 설계를 진행해야 함을 알게됐어요.
질문에 대한 답은, 데이터베이스 구조  설계시 라고 말할 것 같아요.(JPA는 관계형 데이터베이스를 사용하는 방식을 정의해논 인터페이스이기 때문)


- 실무에선 어느 정도까지의 정규화를 진행하는 것이 좋을까?

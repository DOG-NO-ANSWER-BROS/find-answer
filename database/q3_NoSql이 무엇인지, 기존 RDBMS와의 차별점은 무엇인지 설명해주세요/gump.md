# [DB] NoSql이 무엇인지, 기존 RDBMS와의 차별점은 무엇인지 설명해주세요

# 정리

 이번 포스트는 [SQL vs. NoSQL Databases: What’s the Difference?](https://www.upwork.com/resources/sql-vs-nosql-databases-whats-the-difference?utm_source=google&utm_campaign=SEM_GGL_INTL_NonBrand_Marketplace_DSA&utm_medium=cpc&utm_content=113089129402&utm_term=&campaignid=11384804789&matchtype=b&device=c&gclid=CjwKCAjwjJmIBhA4EiwAQdCbxuKKmxY9Iph0otnDTgAia4_LH2cYNOE6_mY4veUbF7y4OHD4EzgUTBoCLyQQAvD_BwE) 을 번역하며 정리한 것이에요.

> NoSql 데이터베이스(비관계형 데이터베이스)는 관계형 데이터베이스와 방식은 다르지만 관계 데이터를 저장할 수 있어요. Not Only SQL, 기존의 관계형 DBMS가 갖고있는 특성 뿐만 아니라 다른 특성들을 부가적으로 지원한다는 것을 의미해요.

데이터베이스는 크게 `SQL`과 `NoSQL`, `관계형 데이터베이스`와 `비관계형 데이터 베이스`로 나뉘어요. 
둘의 차이점은 구축 방법, 저장 정보 유형 및 저장 정보에 따라 달라져요.
`관계형 데이터베이스`는 전화번호와 주소를 저장하는 전화번호부와 같이 구조화 되어있어요.
`비관계형 데이터베이스`는 사람의 주소와 전화번호부터 Facebook 좋아요 및 모든 것을 포함하는 파일 폴더와 같이 문서 지향적이며 분산되어있어요.

SQL로만 작성되었는지 여부를 통해 SQL및 NoSQL이라 불러요. 
SQL이 무엇인지, 어떻게 이러한 데이터베이스를 만드는지, 어떤 형식의 데이터베이스를 선택해야하는지 등을 알아 보죠.

![https://user-images.githubusercontent.com/48986787/127773393-8ef062fc-dc31-4111-be99-685cd97f8284.png](https://user-images.githubusercontent.com/48986787/127773393-8ef062fc-dc31-4111-be99-685cd97f8284.png)

## SQL: Relational databases

데이터구조는 SQL과 NoSQL을 구분하는 주요 기능 중 하나에요. 이를 살펴보도록해요.
`관계형 데이터베이스`는 전화번호부 처럼 데이터를 저장하는 구조화된 방법이에요. column과 row가 있는 두 개 이상의 테이블로 구성돼요. 각각의 row는 정보를 나타내고, column은 이름, 주소, 및 전화번호와 같은 매우 특정한 유형의 정보를 정렬해요. 

![https://user-images.githubusercontent.com/48986787/127777103-a562c9ef-7edd-45c4-82bb-8714bcfbe22c.png](https://user-images.githubusercontent.com/48986787/127777103-a562c9ef-7edd-45c4-82bb-8714bcfbe22c.png)

테이블과 필드 타입간의 관계를 스키마라고 해요. `관계형 데이터베이스`에서 스키마는 정보를 추가하기 전에 명확하게 정의되어야 해요. 즉, 각 테이블과 필드의 설계를 확실히 하고 데이터를 저장 해야함을 의미해요.
잘 설계된 스키마는 데이터 중복을 최소화하고, 테이블이 동기화되지 않는 것을 방지해요. 
잘 설계되지 않은 스카미의 예를 들어보죠. 국내휴대전화번호를 저장하도록 설계된 Column은 11자리의 수(xxx-xxxx-xxxx)가 필요할거에요. 이렇게 Column을 지정하면, 잘못된 값을 저장하지 않을 수 있다는 이점이 있어요.
하지만, 스키마를 변경해야하는 경우(11자리 이상의 국제 전화번호 항목을 포함해야 하는 경우)에 전체 데이터베이스를 편집해야해요. 즉, 데이터의 유연성이 떨어지게돼요.

`SQL(Structured Query Language)`은 데이터베이스 설계자가 관계형 데이터베이스를 설계하는 데 사용하는 프로그래밍 언어에요. SQL은 쿼리를 통해 데이터를  생성, 검색, 수정, 삭제해요.
SQL의 가장 큰 장점 중 하나는 간단하면서도 강력한 JOIN절이에요. 이를 통해 개발자는 하나의 명령어로 여러 테이블에 저장된 관련 데이터를 검색 할 수 있어요. join과 여러 소프트웨어 호환성이 뛰어나다는 장점으로 SQL 데이터베이스는 인기가 많아요.

<br>

## SQL종류

### MySQL

가장 인기 있는 오픈 소스 데이터베이스

### MariaDB

MySQL을 포함하고 향상된 데이터베이스

### PostgreSQL

### Oracle

C++ 언어로 작성된 객체 관계형 데이터베이스

### IMB DB2

### Sybase

### MS SQL Server

### Microsoft Azure

모든 운영 체제를 지원하고 데이터를 한 곳에서 저장, 계산 및 확장할 수 있는 클라우드 컴퓨팅 플랫폼 제공

<br>

## NoSQL Databases: Not Only SQL & distributed data

데이터 요구사항이 처음부터 명확하지 않거나 대량의 구조화되지 않은 데이터를 처리하는 경우, 명확하게 정의된 스키마로 관계형 데이터베이스를 개발하기엔 여유가 없을 수 있어요. 
`NoSQL`은 모든 유형의 관련 정보를 조합하는 파일 폴더와 비슷해요. 그렇게 때문에 유연해요.

모든 구조화 되지 않은 정보를 저장, 처리 및 분석하려는 시도는 SQL에 대한 스키마 없는 개발로 이어졌어요. 이러한 대안을 합쳐서 `NoSQL`이라고 하며 "Not only SQL"을 의미해요. NoSQL이라는 용어는 관계형 데이터베이스에 대한 광범위한 대안을 포함하지만 공통점은 데이터를 보다 유연하게 처리할 수 있다는 것이에요.

`NoSQL` 데이터베이스는 테이블 대신 문서 지향적이에요. 이렇게 하면 구조화되지 않은 데이터(예: 기사, 사진, 소셜 미디어 데이터, 비디오 또는 블로그 게시물 내 콘텐츠)를 쉽게 찾을 수 있는 단일 문서에 저장할 수 있어요.  `관계형 데이터베이스`처럼 필드로 분류되지는 않아요.
`NoSQL 데이터베이스`는 특히 앱 개발자에게 또 다른 주요 이점인 접근 용이성을 제공해요. `관계형 데이터베이스`는 Java, PHP 및 Python과 같은 객체 지향 프로그래밍 언어로 작성된 응용 프로그램과 복잡한 관계를 가지고 있어요. NoSQL 데이터베이스는 개발자가 SQL을 배우거나 데이터베이스 시스템의 기본 아키텍처를 이해하지 않고도 API를 통해 쿼리를 실행할 수 있어요.

<br>

## NoSQL 유형

### Key-value model

### Column store

### Document database

### Graph database

<br>

## NoSQL종류

### MongoDB

동적 스키마의 JSON과 같은 문서가 있는 문서 지향 데이터베이스예요. 오픈 소스이므로 무료이며 우수한 고객 서비스를 제공해요.

### HBase

### Oracle NoSQL

### Cassandra

<br>

## 어떤 데이터베이스를 선택해야할까?

데이터베이스 기술과 관련하여 만능 해결책은 없어요. 그렇기 때문에 많은 기업이 다양한 작업을 위해 관계형 데이터베이스와 비관계형 데이터베이스를 모두 사용해요. NoSQL 데이터베이스가 속도와 확장성으로 인기를 얻고 있지만 여전히 고도로 구조화된 SQL 데이터베이스가 선호되는 상황이 있어요. 다음은 SQL 데이터베이스를 선택하는 몇 가지 이유예요.

### SQL 데이터베이스를 사용하는 이유

**ACID를 보장해야할 떄**
ACID 보장은 트랜잭션이 데이터베이스와 상호 작용하는 방식을 정확히 규정하여 에러를 줄이고 데이터베이스의 무결성을 보호해요. 일반적으로 NoSQL 데이터베이스는 유연성과 처리 속도를 위해 ACID 준수를 희생하지만 많은 전자 상거래 및 금융 애플리케이션의 경우 ACID 보장 데이터베이스가 여전히 선호되는 옵션이에요.

**한번 구조화된 데이터가 변경되지 않을 때**
비즈니스에서 더 많은 서버가 필요한 대규모 성장이 발생하지 않고 일관된 데이터로만 작업하는 경우, 다양한 데이터 유형과 높은 트래픽 볼륨을 지원하도록 설계된 관계형 데이터베이스를 사용하지 않을 이유가 없어요.

### NoSql 데이터 베이스를 사용하는 이유

서버측 애플리케이션의 다른 모든 구성요소가 빠르고 원활하도록 설계된 경우 NoSQL 데이터베이스는 데이터의 병목 현상을 방지해요. 빅 데이터는 전통적인 관계형 데이터베이스가 할 수 없는 일을 하는 NoSQL이에요. MongoDB, CouchDB, Cassandra 및 HBase가 있어요.

**구조화되지 않은 대용량 대이터를 저장해야할 떄**
NoSQL 데이터베이스는 함께 저장할 수 있는 데이터 유형에 제한이 없으며 필요에 따라 다른 새로운 유형을 추가할 수 있어요. 문서 기반 데이터베이스(Document)를 사용하면 데이터의 "유형"을 미리 정의하지 않고도 데이터를 한 곳에 저장할 수 있어요.

**빠른 개발이 필요할 때**
NoSQL은 구조화를 먼저 하지 않기에, 짧은 스프린트 내에서 개발중이거나, 데이터 구조를 자주 변경해야할 때 사용해요.

**클라우드 컴퓨팅 및 스토리지를 최대한 활용할 때**
클라우드 기반 스토리지는 탁월한 비용 절감 솔루션이지만 확장하려면 데이터를 여러 서버에 쉽게 분산시켜야 해요. 온사이트 또는 클라우드에서 상용(저렴하고 더 작은) 하드웨어를 사용하면 추가 소프트웨어의 번거로움을 줄일 수 있으며 Cassandra와 같은 NoSQL 데이터베이스는 많은 골칫거리 없이 즉시 여러 데이터 센터에 걸쳐 확장할 수 있도록 설계되있어요.

<br>

## Refer

[https://www.mongodb.com/ko-kr/nosql-explained](https://www.mongodb.com/ko-kr/nosql-explained)

 [SQL vs. NoSQL Databases: What’s the Difference?](https://www.upwork.com/resources/sql-vs-nosql-databases-whats-the-difference?utm_source=google&utm_campaign=SEM_GGL_INTL_NonBrand_Marketplace_DSA&utm_medium=cpc&utm_content=113089129402&utm_term=&campaignid=11384804789&matchtype=b&device=c&gclid=CjwKCAjwjJmIBhA4EiwAQdCbxuKKmxY9Iph0otnDTgAia4_LH2cYNOE6_mY4veUbF7y4OHD4EzgUTBoCLyQQAvD_BwE) 

<br> 

## 진짜 궁금한 것

### 질문

일반적인 게시판 프로젝트에서 어떤 데이터베이스를 선택해야할까?
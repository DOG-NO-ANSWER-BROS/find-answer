# 정리
## 한줄 요약
스프링에서 재정의한 SqlException

## 왜 unchecked로?

DataAccessExcetpion은 Checked인 SqlException을 RuntimeException으로 재정의하여 되던지는 예외이다. 사실 이 질문은 왜 SQL Exception이 Checked인가?가 더 적합한 질문이라고 생각한다. 체크드 예외는 하위계층의 예외 시그니처를 상위계층으로 전파함으로써 코드들의 결합도를 높인다. 그게 싫다면 내부적으로 try catch를 해주어야 한다.  현재 스프링을 다루는 입장에선 하나의 sql에러가 반드시 처리해야할 예외라고 여겨지지 않지만, SQL예외가 처음 나온 시점에서 생각해보면, 지금보다 컴퓨팅파워나 데이터베이스의 복구처리나 커넥션 관리의 여건이 좋지 않았을거라 생각한다. Checked예외로 정의함으로써, 반드시 개발자가 신경써야할 부분으로 여겼겠지, 싶다.

스프링에선 Unchecked로 예외 되던지기를 통해 데이터계층에서 어플리케이션 계층으로 예외 시그니처가 전파되는 것을 막고(OCP - 변화에 닫혀있을 수 있도록) 예외의 상세내용을 알려주기 위해 SqlException을 재정의 한 것임이 API 문서에 드러난다.

https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/dao/DataAccessException.html

## 계층 추상화

토비의 스프링에선 또다른 중요한 이야기를 해주는데, 그것은 DataAccess 기술의 추상화이다.

스프링은 DI 컨테이너로써, 다양한 DataAccess 기술을 지원한다. 하지만 비즈니스 로직은 어떤 기술을 도입하던지 영향을 받아선 안된다. (layer architecture)
하지만 여기에서 문제점이, 각 데이터 접근기술 (JdbcTemplate, JTA, JPA 등)이 던지는 예외가 다 다르다는 점이 있다. 또한 RDBMS 벤더사 마다도 데이터베이스에서 어플리케이션으로 보내주는 예외코드도 다 다르다. 이런 상황에서 하나의 예외처리 로직으로 통일하기 위해서, 스프링에서 DataAcessExcetpion을 만들어 벤더별 / 기술별 예외를 통일한 것이다. 

<img width="674" alt="스크린샷 2021-08-27 오전 9 19 26" src="https://user-images.githubusercontent.com/51393021/131052379-0dd6a5eb-e4b9-4ead-9d42-ec31009a96b3.png">

# 질문
- SqlException을 Checked 예외로 만든 배경이 무엇일까?

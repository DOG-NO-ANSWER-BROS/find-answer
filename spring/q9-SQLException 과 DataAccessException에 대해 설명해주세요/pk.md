# SQLException

* JDBC 애플리케이션을 실행하는 동안 발생하는 예외의 기본 클래스이다.
* JDBC는 대부분 인터페이스에 의해 정의되지만 예외 지원은 클래스에서 제공된다.
* SQLException은 다음과 같은 내용을 포함한다:
  * 텍스트 설명
  * SQLState
  * 오류 코드
  * 발생한 다른 예외에 대한 참조
* 대부분의 SQLException은 복구가 불가능하고, DAO 밖에서 이 예외를 다룰 가능성이 거의 없다.
* throws를 이용하여 계속해서 다른 객체에 해당 예외의 처리를 위임하지 말고 최대한 빨리 unchecked exception으로 전환해주는 것이 좋다.

스프링은 예외 전환이라는 방식을 사용하여 이러한 SQLException을 DataAccessException으로 바꾼다.

### 예외 전환
* 예외가 발생했을 때 적절한 예외로 전환해서 다시 던지는 방식.
* 예외를 그대로 던졌을 때 예외상황에 대한 적절한 의미를 부여해주지 못할 경우 그 의미를 분명하기 위해 바꾼다.
* checked 예외는 비즈니스 로직으로 볼 때 의미가 있는 예외가 아니므로 runtime 에러로 포장해서 던지는게 좋다.

# DataAccessException

* Runtime Exception 이다.
* 데이터 접근 관련 예외가 해당 예외를 상속받아 만들어진다. (JdbcTemplate에서 던지는 예외들은 다 DataAccessException의 서브클래스)
* JdbcTemplate은 SQLException을 단지 DataAccessException으로 포장하는 것이 아니라 DB의 에러 코드를 DatabaseAccessException 계층 구조의 클래스 중 하나로 매핑한다.
* 드라이버나 DB 메타정보를 참고해서 DB 벤더를 확인한 다음, DB 별로 미리 만들어진 매핑정보를 이용해 어떤 예외 클래스를 사용할 것인지 선택하기 때문에 사용하는 DB의 벤더가 달라도 동일한 예외를 받을 수 있다.

## 질문
### DataAccessException을 사용하면서 주의해야할 점이 있을까?
* 특정 기술에 따라 예상치 못한 결과가 반환될 수 있다. 아직 모든 예외가 명확하게 추상화되어 있지 않기 때문이다.

##### 출처
[Java SQLException 클래스](https://www.ibm.com/docs/ko/i/7.3?topic=exceptions-java-sqlexception-class)
[DataAccessException이란?](https://hyeonyeee.tistory.com/96)
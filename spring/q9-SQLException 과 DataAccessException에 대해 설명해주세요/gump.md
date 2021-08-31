# 정리

> SQLException은 Java의 `CheckedException`, DataAccessException은 Spring의 `RuntimeException/UnCheckedException`이에요.

## SQLException

`CheckedException`은 복구가 불가능한 예외인 시스템 예외예요. 쉽게 말해 프로그램의 오류 또는 개발자의 부주의 때문에 발생하는 경우이거나, 통제할 수 없는 외부상황 때문에 발생해 코드 레벨에서는 복구할 방법이 없는 것을 뜻해요.

- SQL 문법이 틀린 경우
- 제약조건을 위반한 경우
- DB 서버가 다운된 경우
- 네트워크 불안정
- DB커넥션 풀 부족

<br>

## DataAccessException

사라진 `SQLException`. 
JdbcTemplate을 적용하고, throws `SQLException` 선언을 지울 수 있었어요. 
예외는 적절하게 복구되던지, 작업을 중단시키고 운영자 또는 개발자에게 분명하게 통보되어야 해요.
`SQLException`이 발생하는 이유는 SQL에 문법 에러가 있거나 DB에서 처리할 수 없을 정도로 데이터 엑세스 로직에 심각한 버그가 있거나, 서버가 죽거나, 네트워크가 끊기는 등의 심각한 상황이 벌어졌기 때문이에요. 
그렇기 때문에 단지 로그에 예외 메세지를 출력하는 것은 아무런 도음이 되지 않아요. 
예외 처리 방법은 `예외 복구`(재시도 카운트를 줌), `예외처리 회피`(예외처리를 담당하지 않고 호출한 쪽으로 던지는 것), `예외 전환`(예외 회피와 비슷하게 예외를 복구해서 정상적인 상태로는 만들 수 없기 때문에 예외를 메소드 밖으로 던지는 것, 하지만 이때 적절한 예외로 전환해서 던짐)

```java
// 예외처리 회피
public void add() throws SQLException{
	// JDBC API
}

public void add() throws SQLException {
	try{
    //JDBC API
  } catch (SQLException e){
     //로그 출력
     throw e;
  }
}
```

스프링 JDBCTemplate에서는 가능한 언체크/런타임 예외로 전환해주는 전략을 따르고 있어요. 
결국 DataAccessException은 앞서말한 예외전략을 따르는 DataAccess 예외 추상화의 결과로 나왔어요. 
(SQLException 뿐만 아닌 JDBC 외의 자바 데이터 엑세스 기술에서 발생하는 예외에서도 적용)
스프링은 DI 컨테이너로써, 공통된 인터페이스를 통해 다양한 DataAcess기술을 지원해요. 하지만 비즈니스 로직은 어떤 기술을 도입하던지 영향을 받아선 안돼요 (구현이 변경된다고 비지니스 로직이 영향을 받으면 안됨)). (OCP, DIP, LSP) 
RDMS 벤더사마다 데이터베이스에서 애플리케이션으로 던지는 코드가 달라요. 이런 상황에서 하나의 예외 처리 로직으로 통일하기 위해, 스프링에서 DataAccessException을 만들어 벤더별, 기술별 예외를 통일했어요. 

### 주의점

데이터 액세스 기술에 상관없이 키 값이 중복이 되는 상황에 아래와 같이 동일한 예외가 발생하지 않아요.

JDBC는 **DuplicateKeyException**, 하이버네이트는 **ContraintViolationException**을 발생시키기 때문에, 이에 유의해야해요. 

<br>

## Refer

토비의 스프링 

<br> 

## 진짜 궁금한 것

### 질문

DataAccess외에 스프링에서 제공하는 예외 추상화는 무엇이 있을까?
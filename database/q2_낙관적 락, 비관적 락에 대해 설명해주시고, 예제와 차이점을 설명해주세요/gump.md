# q2_낙관적 락, 비관적 락에 대해 설명해주시고, 예제와 차이점을 설명해주세요

트랙잭션이 보장해야하는 것은 **ACID**예요. 

트랜잭션은 기본적으로 **원자성(Atomicity), 일관성(Consistency), 지속성(Durability)**은 보장해요. 문제는 **격리성(Isolation)**인데 트랜잭션간에 격리성을 완벽히 보장하라면 트랜잭션을 거의 차례대로 실행해야 해요.

하지만 이렇게 하면 동시성 처리가 매우 나빠져요. 이러한 이유로 ANSI 표준은 격리수준을 4단계로 나누어 정의해요. 

- **READ UNCOMMITED(커밋되지 않은 읽기)**
- **READ COMMITTED(커밋된 읽기)**
- **REPEATABLE READ(반복 가능한 읽기)**
- **SERIALIZABLE(직렬화 가능)**

자세한 부분은 이후에 다루기로 할게요.

**트랜잭션 격리(Transactional isolation)**는 일반적으로 트랜잭션에서 엑세스되는 모든 것을 락(잠그는)하는 방식으로 구현돼요. 

트랜잭션 락에는 낙관적 락과 비관적 락 두가지 접근방식이 있어요. 

## **낙관적 락 (Optimistic Concurrency Control)**

트랜잭션 대부분은 충돌이 발생하지 않는다고 낙관적으로 가정하는 방법이에요.

즉, 트랜잭션이 영향을 미치는 데이터 자원을 잠그지 않고 진행될 수 잇다고 가정해요. 

데이터베이스가 제공하는 락 기능을 사용하는 것이 아닌 **애플리케이션이 제공하는 락**을 사용해요.

트랜잭션을 커밋하기 전까지는 트랜잭션의 충돌을 알 수 없어요. 

즉, 커밋하기 전에 각 트랜잭션은 다른 트랜잭션이 데이터를 수정하지 않았는지 확인해요. 검사 결과 충돌하는 수정 사항이 발견되면 커밋 트랜잭션이 롤백돼요.

## **비관적 락 (Pessimistic Concurrency Control)**

동시에 진행되는 트랜잭션이 충돌할 것이라고 가정하는 방법이에요.

즉, 트랜잭션의 충돌이 발생한다고 가정하고 우선 락을 걸고 봐요.

리소스를 읽은 후에 락(잠금)을 하고, 애플리케이션이 데이터 사용을 마친 후에 락(잠금)을 해제해요.

데이터베이스가 제공하는 락 기능을 사용해요

대표적으로 select for update 구문이 있어요.

## **Refer**

[https://enterprisecraftsmanship.com/posts/optimistic-locking-automatic-retry/](https://enterprisecraftsmanship.com/posts/optimistic-locking-automatic-retry/)

[https://docs.jboss.org/jbossas/docs/Server_Configuration_Guide/4/html/TransactionJTA_Overview-Pessimistic_and_optimistic_locking.html](https://docs.jboss.org/jbossas/docs/Server_Configuration_Guide/4/html/TransactionJTA_Overview-Pessimistic_and_optimistic_locking.html)

[https://www.youtube.com/watch?v=VxGKvqHhU5c](https://www.youtube.com/watch?v=VxGKvqHhU5c)

[https://docs.jboss.org/hibernate/orm/4.0/devguide/en-US/html/ch05.html](https://docs.jboss.org/hibernate/orm/4.0/devguide/en-US/html/ch05.html)
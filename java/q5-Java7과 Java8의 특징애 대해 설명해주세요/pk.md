## Java 7
* Type Interface 다이아몬드 지시자(<>) 사용
* String 타입을 이용하여 switch 문 분기
* try-with-resources : AutoClosable과 Closable 인터페이스를 이용하여 resource를 자동으로 닫을 수 있음 
* 숫자에 '_' 사용. 예시 : ```int NUMBER = 1_000_000;```
* 숫자형에 `0B`, `0`, `0X`를 prefix로 binary로 표현
* 하나의 catch 문에서 여러 예외 잡기
* NIO 패키지 추가
* G1 가비지 컬렉터

## Java 8

### stream API
Stream 형식의 내부 for문을 사옹할 수 있게 되었다.
* Stream은 함수형 프로그래밍에서 나온 방식으로, Stream과 함께 람다 형식과 함수형 인터페이스가 추가되었다.

### lambda expression & functional interface
SAM(Single Abstract Interface) 형태를 따르고 있는 모든 인터페이스는 함수형 인터페이스다. 그리고 그 구현체는 람다 형식처럼 취급될 수 있다. 그런데 함수형 인터페이스의 특징은 default 메서드를 가질 수 있다는 점이다.

* 하지만 default 메서드는 남용되어서는 안된다. 목적이 "하위호환을 지키면서 기능을 확장"할 때 사용해야한다.

### Interface 디폴트와 정적 메서드
* Java 8 전에 인터페이스는 추상 메서드만 포함할 수 있었다. 
* 따라서 인터페이스는 구현체가 모두 overriding 하는 추상메서드를 만들지 않는 한 새로운 기능을 추가할 수 없었다.
* Java 8부터는 인터페이스가 디폴트 메서드와 정적 메서드를 가질 수 있다. 그리고 이 두 메서드 형식은 오버라이딩 해서 사용할 수 없다.

### 메서드 레퍼런스
람다 형식이 Java 8에 추가되면서 메서드를 호출할 수 있는 새로운 방식도 추가되었다.
* 스태틱 메서드
* 인스턴스 메서드
* 생성자 (new)

### Optional<T>
Java 8 이전에는 NullPointerException이 발생하지 않도록 참조하고 있는 값이 null인지 의식적으로 검증하는 과정이 필요했다. 그 과정이 지속되다 보면 비즈니스 로직과는 거리가 먼, 중복적인 검증 과정이 등장하기 마련이었다.
* 그래서 Optional이 등장했는데, optional은 그 안에 값이 없다면 발생시킬 수 있는 로직을 정하거나 새로운 객체를 반환할 수 있게 해준다.

## 질문
프로젝트를 진행할 때 Java의 어떤 버전을 사용하고, 그 이유는 무엇일까?

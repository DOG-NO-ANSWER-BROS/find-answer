# 정리
자바 7과 8의 차이점
http://www.differencebetween.net/technology/difference-between-java-7-and-java-8/#ixzz76U7dNeWz

8의 메이저 변경
1. Language-level support for Lambda Expressions
2. Interface default and Static Methods
3. Unsigned Integer Arithmetic
4. Concurrent API enhancements
5. New Date and Time API
6. Parallel Sorting
7. Null Reference Template
8. New JavaScript Engine, Nashorn
9. New and improved Stream API
10. removal of permanent generation

하나하나 알아보자.

### 1. 람다 지원
자바의 인스턴스는 일급 객체다. 하지만 메소드는? '메소드' 라는 개념을 다른 메소드에 넘길 수 없으므로, 일급 객체가 아니다. 자바에서 특정한 상태에 놓여있는 인스턴스는 인자로 넘길 수 있지만, "행동" 그 자체를 넘길 수 없었다.
람다 도입으로 일부나마 행동 그 자체를 객체처럼 다루는 게 가능해졌다. 그 전에는 익명 클래스를 넘기는 트릭으로 해결하고 있던 문제를, 람다 문법 지원으로 깔끔하게 해결할 수 있게 해주었다. 사용자 정의 함수형 인터페이스 말고도, 
기본적으로 다양한 상황에 쓰일만한 FunctionalInterface 패키지와 디폴트 메소드들도 많이 지원해준다.

### 2. 인터페이스에서 디폴트 메소드와 정적메소드 지원
이전까지 인터페이스에는 명세만 구현할 수 있을 뿐, 구체적인 행동을 기술할 수 없었다. 이는 abstract class로 가능하다. (명세만 작성하고 싶으면 abstract method, 구체적인 메소드를 작성하고 싶으면 구현)
자바 8부터 인터페이스에 스태틱 메소드와 디폴트 메소드를 지원하기 시작했는데, 이는 이미 구현되어있는 인터페이스들과 구현체들에 영항을 주지 않고 새로운 변경점을 추가하기 위함이라고 한다. 개인적으론 람다와 스트림이 추가되면서
기존 JCF에 커다란 변화가 생겼기 때문에, 고육지책으로 나왔다는 생각이 든다. 예컨데 List API에 stream()메소드를 추가해버리는 식으로 구현하면, 기존 자바 사용자들의 List를 직접 구현한 소스코드들이 모조리 컴파일 에러가 날 것이다.
그렇다고 util 클래스에 있는 모든 list구현체에 따로 stream을 구현한다? 이것도 List 인터페이스를 활용하는 수많은 프로젝트에게 구현체를 활용할 것을 강요하게 된다. 고심이 많았을 것 같은데, 그래서 기존 구현에 영향을 주지 않으면서 
변경점만 활용할 수 있도록 디폴트 메소드를 만들게 된 것 같다. static과 헷갈릴 수 있는데, 디폴트 메소드는 인스턴스에서 활용할 수 있는 메소드이다. 오버라이드도 물론 가능하다. 
처음 디폴트 메소드를 봤을 때 이러면 abstract class와 인터페이스에 무슨 차이가 있지? 생각했는데, abstract class는 상태를 가질 수 있고, interface는 상태를 가질 수 없다는 점이 다르다.
기존 구현에 영향을 주지 않고 코드를 추가하려는 기존 목적과 달리 디폴트 메소드를 재밌게 활용한 예는 스프링의 설정 (WebMvcConfigure 라든지) 클래스들이 있다. 
내부의 모든 메소드를 반드시 구현해야하는 일반 메소드가 아니라 디폴트메소드로 만들어놓고, 유저가 선택적으로 구현할 수 있도록 (IDE의 도움을 받으면 오버라이드 할 수 있는 구현체 목록이 나오므로) 지원한다.
참 똘똘하다.

### 3. 부호없는 산수 지원
이건 이번에 정리하며 처음 알았다.. 그리고 앞으로 쓸 일도 잘 없을것 같다 ^^.. Integer와 Long의 스태틱 클래스로 지원한다고 한다.
부호없는 연산을 하게되면 기존의 부호 비트인 첫 비트부터 값 비트로 계산하게 된다고 한다. 그 결과로서
Integer.MAX_VALUE
Integer.MIN_VALUE
이 두개의 대소 비교는 부호있는 계산일 때와 그렇지 않을 떄가 다르다.

```java
int signedComparison = Integer.compare(positive, negative);
assertEquals(1, signedComparison);

int unsignedComparison = Integer.compareUnsigned(positive, negative);
assertEquals(-1, unsignedComparison);
```
위의 두가지 테스트는 통과한다. 왜?
비트로 생각해보자.

Integer.MAX_VALUE는 다음과 같을것이다 ; 0111_1111_...1111
Integer.MIN_VALUE는 다음과 같을것이다 ; 1000_0000_...0000
첫 부호를 부호라고 생각할 땐 MAX_VALUE가 크지만, 첫 비트부터 값으로 계산하면 MIN_VALUE가 1 큰 것이다.

밸덩 블로그에서는 요구가 많아서 추가되었고, 이로인한 이점에 대해선 모르겠다고 설명한다. 부호연산일 때와 다른 결과가 나오기 때문에 사용에 주의하라고 한다.
나도 이점에 대해서 이해하지 못 하겠고, 앞으로도 안쓸 거 같다.
https://www.baeldung.com/java-unsigned-arithmetic 

### 4. 동시성 API 강화
동시 쓰레드 관리 API인 fork join pool 이나, ConcurrentHashMap 등 동시성 이슈와 연관된 로직들이 개선되었다. 이 부분에 대해선 분량이 방대하기에 따로 정리할 기회가 있으면 정리하겠다. 
주로 동시성 때 안정성을 확보하기 위해 성능을 희생했던 부분을 다양한 방식으로 개선했다.

### 5. 새로운 시간 API
자바의 약점이라 꼽히는 Date API.. 진짜 구리다. 왜 month는 0부터 시작하는가? 시간 비교는 왜 이리 힘든가? calnedar API와의 변환하며.. 포맷을 표현하는 일 하며..
욕을 많이 먹고 8에서 새로운 시간 API를 만들고, Date는 deprecate 시켜버렸다. java.time 패키지에 추가되었다.
시간을 표현할 때는 LocalTime,
날짜를 표현할 때는 LocalDate,
모두 표현할 때는 LocalDateTime,
시간대(time-zone)까지 표현하려면 ZonedDateTime을 사용한다.
시간 비교를 위한 API, 각 월에 대한 상수클래스 등 다양한 편리성 기능도 제공한다.

### 6. 병렬 솔팅
대용량 솔팅시에 병렬 스레드로 솔팅을 진행할 수 있는 api이다. 스레드를 생성하는 비용을 고려해야 하기 때문에, 적은 사이즈의 솔팅에선 오히려 성능이 저하될 수 있음에 주의하자.

### 7. null 대체할 수 있는 Optional API
null이란? 개발자 공공의 적 null... npe를 유발해 디버깅 시간을 10배로 늘려준다. 괜히 null 같은 걸 만들어서 개발할 때 편의적으로 null을 넣고 싶게 만들고, 나중에 유지보수할 때 사람 미치게 만든다. 
여기저기 쓸모없는 null 방어로직을 만들게 한다.
`nullable하다` 라는 개념을 표현하기 위해 Optional API가 추가되었다! 이제 null 방어 로직을 짜지 않고, nullable인데 Optional을 쓰지 않은 개발자를 욕할 근거를 만들어주었다.
Optional은 size 1인 콜렉션과 비슷하기 때문에, 랩핑비용을 고려해야 한다. 그래도 null을 미워하는 방향으로 자바가 흘러가는 것 같아 Optional API 극호감이다.

### 8. Javascript 엔진, Nashorn
JVM 상에서 javascript를 구동할 수 있게 만드는 엔진이라고 한다! $JAVA_HOME/bin 에 jjs 라는 CLI 로 이용할 수 있다고 한다.
api로도 제공하는데, 다음처럼 활용 할 수 있다고한다.

```java
ScriptEngine engine = new ScriptEngineManager().getEngineByName("nashorn");
engine.eval("print('Hello World');");
```
공식 사이트
http://openjdk.java.net/projects/nashorn/

### 9. stream api
컬렉션의 집합, 조회, 변환, 병렬수행 등을 선언형으로 아주 쉽게 활용할 수 있는 Stream API이다. 활용법과 특징은 워낙 방대하기에 스킵하겠다.
특징을 몇 가지 소개하자면, 
1. 내부 연산자를 활용한다
2. 연산을 합쳐셔(루프 서킷), 그리고 lazy 하게 실행된다(최종연산 에 이르러야 모든 연산을 모아서 실행한다)
3. 선언형으로 표현한다.

### 10. permenant 영역 삭제, metaspace 추가
Permanent Generation 메모리 영역이 없어지고 Metaspace 영역이 생겼다.
Permanent Generation영역은 Class 혹은 Method Code가 저장되는 영역이었는데, Heap space에 자리했다. 이 영역이 native로 이동하며 이름이 metaspace로 변경되었다.
heap 스페이스를 넉넉~하게 쓰려는 의도라고 생각한다.

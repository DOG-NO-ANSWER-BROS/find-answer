# 정리

JVM, JRE, JDK에 관한 부분은 이전에 정리했어요. 

[[Live-Study] JVM은 무엇이며 자바 코드는 어떻게 실행하는 것인가](https://livenow14.tistory.com/19)

간략히 알아보기로 해요.

<br>

## JVM

![https://user-images.githubusercontent.com/48986787/101020130-5e268b80-35b1-11eb-9530-7400f33d64c5.png](https://user-images.githubusercontent.com/48986787/101020130-5e268b80-35b1-11eb-9530-7400f33d64c5.png)

Write Once Run Anywhere의 특성을 가진, 즉 플랫폼에 독립적인 Java의 특징을 구현하기 위한 가상머신이에요. 

H/W, OS위에 실행되기 때문에 JVM자체는 플랫폼에 종속적이에요. (즉, 플랫폼에 따라 호환되는 JVM을 실행해야함)

JVM은 사용자 언어인 `자바(.java)`와 `기계어(바이너리)` 사이의 중간 언어인 자바 바이트코드를 사용해요.

쉽게 말해 JVM은 **`자바 바이트코드(.class)를 해석하고 실행하기위한 가상머신`**이에요. 

![https://user-images.githubusercontent.com/48986787/101020230-844c2b80-35b1-11eb-8626-c573e30167ae.png](https://user-images.githubusercontent.com/48986787/101020230-844c2b80-35b1-11eb-8626-c573e30167ae.png)

- **클래스 로더 시스템 (Class Loader): .class 에서 바이트코드를 읽고 메모리에 저장**
- **메모리(Runtime Data Area): JVM이 OS위에서 실행되면서 할당받는 메모리 영역**
- **실행엔진(Execution Engine):** 클래스 로더를 통해 런타임 데이터 영역에 배치된 바이트 코드를 명령어 단위로 읽어서 실행
- **네이티브 메소드 인터페이스 (JNI):** 자바 애플리케이션에서 C, C++, 어셈블리로 작성된 함수를 사용할 수 있는 방법 제공

<br>

## JRE (Java Runtime Environment): JVM + 라이브러리

- 자바 실행 환경
- 자바 애플리케이션을 실행할 수 있도록 구성된 배포판.
- JVM과 핵심 라이브러리 및 자바 런타임 환경에서 사용하는 프로퍼티 세팅이나 리소스파일을 가지고 있다.
- 개발 관련 도구는 포함하지 않는다. (그건 JDK에서 제공)
- 자바 11부터는 제공하지않고, 이것만 써서하는건 흔치않다.

<br>

## JDK (Java Development Kit): JRE + 개발 툴

- 자바 개발 도구
- JRE + 개발에 필요할 툴
- 소스 코드를 작성할 때 사용하는 자바 언어는 플랫폼에 독립적(JVM은 플랫폼에 종속적).
- 오라클은 자바 11부터는 JDK만 제공하며 JRE를 따로 제공하지 않는다.
- Write Once Run Anywhere

<br> 

## Java 8과 Java 11

java는 6개월마다 Major 업데이트를 진행해요.(현재 Java의 최신버전은 JDK 17이에요 )

매번 업데이트가 진행된다고 무작정 JDK를 업데이트 하면안되는 이유는 `LTS(Long Term Support)`지원 버전이냐 아니냐에 따라 버전에 대한 지속적인 버그 수정 및 업데이트 지원에 차이가 나기 때문이에요. 

### Java 8

- 오라클 인수 후 첫번째 버전
- 2개 버전으로 나뉨(Oracle JDK, OpenJDK)
- Lambda, new Date and Time API(LocalDateTime, …)
- interface default method
- Optional
- PermGen Area 제거

### Java 11

- Oracle JDK와 OpenJDK 통합
- Oracle JDK가 구독형 유료 모델로 전환
- 서드파티 JDK 로의 이전 필요
- lambda 지역변수 사용법 변경

# 질문

## 나만 알만한 것

### 질문

JVM의 JIT 컴파일러의 동작원리 

### 답변

바이트 코드 전체를 네이티브 코드로 변경하고 인터프리팅하지 않고 네이티브 코드로 직접 실행함 

JVM은 내부적으로 해당 메서드가 얼마나 자주 호출되고 실행되는지 체크하고, 일정 기준을 넘었을 때에만 JIT 컴파일러를 통해 컴파일하여 네이티브 코드를 생성

<br>
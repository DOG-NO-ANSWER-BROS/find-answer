## JVM, Java Virtual Machine

JVM은 Java Virtual Machine의 약자이다.
- JAVA의 대표적인 특징 중 하나는 OS 플랫폼 독립적이라는 것이다.
- 어떻게 하나의 코드가 unix, window, 임베디드 os 등에서 동일하게 동작하는가? 각자 다른 기계어를 필요로 하는데.
- 이는 중간자 역할인 JVM이 있기에 가능하다. 자바 바이트코드를 각 플랫폼에 알맞는 기계어로 번역해준다.
> 자바 바이트 코드(Java bytecode)란 자바 가상 머신이 이해할 수 있는 언어로 변환된 자바 소스 코드를 의미합니다.
자바 컴파일러에 의해 변환되는 코드의 명령어 크기가 1바이트라서 자바 바이트 코드라고 불리고 있습니다.
이러한 자바 바이트 코드의 확장자는 .class입니다.
자바 바이트 코드는 자바 가상 머신만 설치되어 있으면, 어떤 운영체제에서라도 실행될 수 있습니다.

![image](https://user-images.githubusercontent.com/51393021/123712614-a7bc2800-d8ad-11eb-85c6-3807987960a9.png)

- 구현상 다음과 같은 특징이 있다.
    1. 스택 기반의 가상 머신
    2. 단일 상속 형태의 객체 지향 프로그래밍을 가상 머신 수준에서 구현
    3. 포인터를 지원하되 C와 같이 주소 값을 임의로 조작이 가능한 포인터 연산이 불가능
    4. 가비지 컬렉션 사용
    5. 모든 기본 타입의 정의를 명확히 함으로써 플랫폼 독립성 보장
    6. 데이터 흐름 분석(영어: data flow analysis)에 기반한 자바 바이트코드 검증기(영어: verifier)를 통해 스택 넘침, 명령어 피연산자의 타입 규칙 위반, 필드 접근 규칙 위반, 지역 변수의 초기화 전 사용 등 많은 문제를 실행 전에 검증하여 실행 시 안전을 보장하고 별도의 부담을 줄여줌
    7. 명령어에서 스택에서 가져올 피연산자의 타입을 명령어에 지정(예: 정수 덧셈은 iadd, 단정밀도 실수 덧셈은 fadd)

### JVM 구조
<img width="590" alt="Screen Shot 2021-06-29 at 9 23 39 AM" src="https://user-images.githubusercontent.com/48251668/123719117-b1e52300-d8bb-11eb-82d9-711857e592dc.png">

* 메서드(클래스) 영역
    - static 변수, 전역변수, 코드에서 사용되는 Class 정보 등이 올라간다.
    - 코드에서 사용되는 class들을 로더로 읽어 클래스별로 런타임 필드데이터, 메서드 데이터 등을 분류해 저장한다.
* 스택
    - 지역변수, 함수 등이 할당되는 LIFO 방식의 메모리다. 메서드를 실행하고 리턴하는 과정에서도 쓰인다.
* 힙
    - new 연산자를 통한 `동적 할당`된 객체들 저장. Garbage Collector로 메모리를 관리한다.
* PC 레지스터 (Program Count Register)
    - 현재 실행되고 있는 JVM의 설명서의 주소를 가지고 있다.
* Native Method Stack
    - 애플리케이션에서 사용되고 있는 모든 native method들을 담고 있다.

* Execution Engine
    * `가상 프로세서`
    * `인터프리터` : 바이트코드 스트림을 읽고 실행한다.
    * `JIT 컴파일러 (Just-In-Time Compiler)` : 성능을 향상시키기 위해 실행된다. 비슷한 기능을 가지고 있는 바이트코드를 함께 컴파일하여 컴파일에 필요한 시간을 단축시킨다.

* Java Native Interface
    - C, C++, Assembly 등 다른 언어로 쓰인 애플리케이션과 소통할 수 있는 인터페이스를 제공하는 프레임워크. 자바는 이 프레임워크를 이용하여 콘솔에 출력하거나 OS 라이브러리와 상호 작용한다.

## JRE, Java Runtime Environment

OS 위에서 실행되는 소프트웨어 레이어로, 특정 자바 프로그램이 실행되기 위한 클래스 라이브러리와 기타 리소스를 제공한다.
- JRE는 다음을 포함한다: 자바 클래스 라이브러리, 자바 클래스 로더, JVM

- JRE는 각 운영체제에 알맞은 JVM을 구현해 놓은 프로그램이다. JRE가 가지고 있는 JVM에 의거하여 바이트코드가 해석되고 실행된다.
      ![image](https://user-images.githubusercontent.com/51393021/123712533-75aac600-d8ad-11eb-8792-cce08fd69fb7.png)
  
- SE (standard edtion), EE (enterprise edition), ME (micro edtion)이 존재한다.

- SE는 [자바 가상 머신 규격](https://docs.oracle.com/javase/specs/)에 맞춘 표준 스펙이고, EE는 거의 같지만 서버용 규격으로 튜닝된 VM을 사용한다고 한다.

- ME는 임베디드용 경량화 VM을 채용한다. SE에서 지원하는 API 중 일부를 지원하지 않는다.

## JDK, Java Development Kit
![image](https://user-images.githubusercontent.com/63634505/123609312-840bca00-d83a-11eb-9678-7e1fc79c5d68.png)
자바 개발 도구(Java Development Kit) 의 약자이다. JDK 는 JRE + 개발을 위해 필요한 도구(javac, java 등) 들을 포함한다. 일반 적으로 Java를 설치할 때 설치하는 것이 JDK이다. JDK 를 설치하면 자동으로 JRE 가 설치 된다.

JDK에는 JRE에는 없는 자바 컴파일러(javac, java compiler) 를 포함하고 있다. 컴파일러란 우리가 작성한 자바 문법을 컴퓨터가 이해할 수 있도록 바꿔주는 해석기 역할을 한다.

*.java → *.class 로 생성

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

## 질문

### GC & WORA?
- GC(Garbage Collection)
    - JVM이 메모리를 관리하는 프로세스를 지칭. 자바 프로그램 상에서 사용하지 않는 메모리를 지속적으로 찾아 제거한다. 개발자는 메모리에 대한 신경을 안쓰도록 도와주는 역할을 한다.
- WORA(Write Once Run Anywhere)?
    - 한 번 쓰고 모든 곳에서 실행한다.

### `Native Method`가 뭔가요?

C와 C++ 같이 다른 언어로도 구현하고 자바로도 구현한 메서드. 즉, 다른 언어로 구현된 부분을 사용하기 위해 존재한다. `native`로 명시된 메서드는 바디를 가질 수 없고, 세미콜론으로 끝나야한다.
```java
// 예시
public class DateTimeUtils {
  public native String getSystemTime();
  // ...
}
```

### 클래스로더, 자바 인터프리터, jit 컴파일러는 뭔가요?
1. 클래스 로더는 .class파일을 열어 알맞은 메모리 영역(메소드, 힙)에 배치하는 역할을 한다. 로딩, 링크, 초기화 3단계로 이루어져 있다.
        - 로딩 : 바이트 코드를 바이너리 데이터로 만들어 메소드 영역에 저장, Class<?>타입을 힙에 저장
        - 링크 : .class를 검증하고, static 변수와 기본 값에 필요한 메모리 확보, 메모리 레퍼런스를 실제 레퍼런스로 교체
        - 초기화 : static 변수에 값을 할당

![123](https://user-images.githubusercontent.com/51393021/123713860-477ab580-d8b0-11eb-93a3-f8784c188c80.png)

2. 인터프리터는 뭔가요?
    - 자바 실행 시 활용하는 실행엔진이다. 바이트 코드를 한줄 한줄 읽어서 실행하는 역할(네이티브 언어로 바꾸어 OS에 전달)을 한다.
3. jit 컴파일러는요?
    - 인터프리터 효율을 높이기 위해, 인터프리터가 반복되는 코드를 발견하면 jit컴파일러로 반복되는 코드를 모두 네이티브 코드로 바꾸어둔다. 그 다음부터 인터프리터는 네이티브 코드로 컴파일된 코드를 바로 사용한다.

### JVM의 JIT 컴파일러의 동작원리가 뭔가요?

JVM은 내부적으로 해당 메서드가 얼마나 자주 호출되고 실행되는지 체크하고, 일정 기준을 넘었을 때에만 JIT 컴파일러를 통해 컴파일하여 네이티브 코드를 생성
# 정리
## JVM,JDK,JRE
1. JVM은 Java Virtual Machine의 약자이다.
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

2. JRE는 Java Runtime Environment의 약자이다.
  - JRE는 각 운영체제에 알맞은 JVM을 구현해 놓은 프로그램이다. JRE가 가지고 있는 JVM에 의거하여 바이트코드가 해석되고 실행된다. 
  ![image](https://user-images.githubusercontent.com/51393021/123712533-75aac600-d8ad-11eb-8792-cce08fd69fb7.png)
  - SE (standard edtion), EE (enterprise edition), ME (micro edtion)이 존재한다. 
  - SE는 [자바 가상 머신 규격](https://docs.oracle.com/javase/specs/)에 맞춘 표준 스펙이고, EE는 거의 같지만 서버용 규격으로 튜닝된 VM을 사용한다고 한다. 
  - ME는 임베디드용 경량화 VM을 채용한다. SE에서 지원하는 API 중 일부를 지원하지 않는다. 

3. JDK는 Java Development kit의 약자이다.
  - JRE와 기본 구성은 동일하다. 다만 개발자가 작성한 .java코드를 .class로 변환해주는 javac 패키지를 포함하고 있다.

## JAVA 8 vs JAVA 11
 https://www.javatpoint.com/java-8-vs-java-11
 - 여러 변화가 있다고 하지만 개인적으로 눈에 띄는 점은 다음과 같다.
    - String API에 디폴트 메소드 추가 `isBlank(), lines(),repeat(n), stripLeading(), stripTrailing(), strip()`
    - java9에 추가된 것이지만, Collection API에 default method 추가 `Collection.of() 등`
    - garbage collector 성능 개선
    - javaFX 라이브러리 제거 (스윙을 대체하기 위한 GUI 도구)

# 질문
- 클래스로더, 자바 인터프리터, jit 컴파일러는 뭔가요? 
  1. 클래스 로더는 .class파일을 열어 알맞은 메모리 영역(메소드, 힙)에 배치하는 역할을 한다. 로딩, 링크, 초기화 3단계로 이루어져 있다.
     - 로딩 : 바이트 코드를 바이너리 데이터로 만들어 메소드 영역에 저장, Class<?>타입을 힙에 저장
     - 링크 : .class를 검증하고, static 변수와 기본 값에 필요한 메모리 확보, 메모리 레퍼런스를 실제 레퍼런스로 교체
     - 초기화 : static 변수에 값을 할당

 ![123](https://user-images.githubusercontent.com/51393021/123713860-477ab580-d8b0-11eb-93a3-f8784c188c80.png)

  2. 인터프리터는 뭔가요?
     - 자바 실행 시 활용하는 실행엔진이다. 바이트 코드를 한줄 한줄 읽어서 실행하는 역할(네이티브 언어로 바꾸어 OS에 전달)을 한다.
  3. jit 컴파일러는요?
     - 인터프리터 효율을 높이기 위해, 인터프리터가 반복되는 코드를 발견하면 jit컴파일러로 반복되는 코드를 모두 네이티브 코드로 바꾸어둔다. 그 다음부터 인터프리터는 네이티브 코드로 컴파일된 코드를 바로 사용한다.
   

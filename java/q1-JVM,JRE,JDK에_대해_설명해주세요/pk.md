## JVM, Java Virtual Machine

<img width="590" alt="Screen Shot 2021-06-29 at 9 23 39 AM" src="https://user-images.githubusercontent.com/48251668/123719117-b1e52300-d8bb-11eb-82d9-711857e592dc.png">

### 무엇일까?
* `JVM`은 추상적인 개념으로, 자바 바이트코드가 실행되는 런타임 환경을 제공하는 일종의 명세서이다.
* `JRE`가 바로 이 명세서의 구현체다(?)
* 자바 코드를 커맨드 창에 입력하고 자바 클래스를 실행하려고 하면 `JVM`의 인스턴스가 만들어진다.

### 어떤 역할을 할까?
1. 코드를 로드한다
2. 코드를 검증한다(?)
3. 코드를 실행한다
4. 런타임 환경을 제공한다

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
JRE는 다음을 포함한다:
  * 자바 클래스 라이브러리
  * 자바 클래스 로더
    - 클래스를 제대로 로드하고 코어 자바 클래스 라이브러리에 연결한다
  * JVM


## JDK, Java Development Kit
자바 애플리케이션 개발을 위해 필요한 툴셋이다.

# Process & Thread

* 프로세스 : 동작하는 하나의 프로그램
    * 독립적인 메모리 영역을 갖는다
* 쓰레드 : 프로세스의 활동 단위
    * 다른 쓰레드와 메모리 영역을 공유한다

## 질문
`Native Method`가 뭔가요?

C와 C++ 같이 다른 언어로도 구현하고 자바로도 구현한 메서드. 즉, 다른 언어로 구현된 부분을 사용하기 위해 존재한다. `native`로 명시된 메서드는 바디를 가질 수 없고, 세미콜론으로 끝나야한다.
```java
// 예시
public class DateTimeUtils {
  public native String getSystemTime();
  // ...
}
```

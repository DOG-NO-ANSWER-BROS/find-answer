# 정리
Java8은 자바의 대표적인 안정화버전으로 사랑받고 있죠. LTS를 지원하는 Java11은 8을 대체할 메이저 버전으로 각광받고 있는데요.
Java 11은 8에 비해 어떤 점이 개선되었을까요?

microsoft docs의 reasons to move to java 11를 읽고, 저만의 방식으로 정리하였음을 밝힙니다! [링크](https://docs.microsoft.com/en-us/java/openjdk/reasons-to-move-to-java-11)

## 성능적인 측면에서의 개선
### 모듈
자바 9에서 도입된 module 시스템으로 인해 기존 classpath 방식으로 해결하기 힘들었던 구성 및 캡슐화를 해결합니다.
하나의 어플리케이션을 모듈이라는 독립단위로 분리할 수 있게 됩니다.

이 방식은 패키지 단위의 캡슐화를 가능하게 하기 때문에 유지보수성에 도움이 됩니다. 
그리고 각 모듈이 필요로하는 런타임 환경을 별개로 구성할 수 있습니다.

- 스프링부트 프로젝트 '웨지 프로젝트' 를 생각해봅시다. 웨지 프로젝트에선 기본적으로 스프링 WEB MVC 의존성을 가지고 있다가, A 기능을 추가하며 Spring Batch 의존성을 추가합니다.
해당 경우 자바8 프로젝트에선 dependencies로 모든 의존성을 포함하는 방법으로 구성을 했을 텐데요, 모듈을 이용하면 A기능을 A라는 이름의 별도 프로젝트(모듈) 분리할 수 있게 됩니다.
이러면 '웨지 프로잭트'에서 Spring Batch 의존성을 제거하고 A 프로젝트를 모듈로 포함하는 방식으로 구성이 가능합니다.
해당 방식은 A 프로젝트를 여타 프로젝트에서도 모듈로 활용할 수 있게 하고 기능 별 역할 분리를 해 장애 단일점 극복, 필요한 모듈만 오토스케일링, 유지보수성 향상, 런타임 환경 최적화 등의 이점을 얻을 수 있습니다.

### 프로파일링과 진단
1. JFR 과 JMC의 오픈소스화
JFR(Java Flight Recorder)은 실행 중인 Java 애플리케이션에서 진단 및 프로파일링 데이터를 수집합니다. 
JFR은 실행 중인 Java 애플리케이션에 거의 영향을 미치지 않습니다. 
수집된 데이터는 JMC(Java Mission Control) 및 기타 도구로 분석할 수 있습니다. 
JFR(Java Flight Recorder)은 실행 중인 Java 애플리케이션에서 진단 및 프로파일링 데이터를 수집합니다. JFR은 실행 중인 Java 애플리케이션에 거의 영향을 미치지 않습니다. 수집된 데이터는 JMC(Java Mission Control) 및 기타 도구로 분석할 수 있습니다. 
JFR과 JMC는 Java 8의 상용 기능이었지만 둘 다 Java 11의 오픈 소스입니다.

2. 통합 로깅
Java 11에는 JVM의 모든 구성 요소에 대한 공통 로깅 시스템이 있습니다. 
이 통합 로깅 시스템을 통해 사용자는 로깅할 구성 요소와 수준을 정의할 수 있습니다. 
이 세분화된 로깅은 JVM 충돌에 대한 근본 원인 분석을 수행하고 프로덕션 환경에서 성능 문제를 진단하는 데 유용합니다.

3. 낮은 오버헤드를 지닌 heap 프로파일링
JVMTI(Java Virtual Machine Tool Interface)에 heap 영역을 프로파일링 하기 위한 새로운 API가 추가되었습니다. 
기존 JFR의 단점을 보완하여 heap을 좀 더 쉽게 프로파일링 할 수 있게 해줍니다.

4. StackWalker를 통한 스택 로깅
java 9에 추가된 StackWalker 클래스를 통해 예외상황시 스택트레이스 로깅을 세부 조정할 수 있습니다.

5. Garbage Collector 개선
자바 11에서 지원하는 GC 목록은 다음과 같습니다. Serial, Parallel, Garbage-First, Epsilon. 
default GC는 G1GC (Garbage-First garbage collector)입니다. 
G1GC는 heap 영역을 분할하여 최대한 짧은 시간 안에 GC를 수행하기 위한 목적을 가지고 있고, 힙 영역을 분할하여 각각의 영역에 대해 GC를 처리하는 알고리즘을 활용하여 그 목표를 달성합니다.

또한 ZGC가 실험적 기능으로 도입되었습니다.

자바 8의 default GC는 Parallel이었으며, 여러 스레드를 활용하여 GC시간을 줄이려는 GC입니다.

Epsilon GC는 heap영역이 가득차면 어플리케이션을 종료하는 GC로, 수명이 짧은 서비스나 garbage가 거의 생기지 않는 프로젝트에 적합합니다.

### 도커 컨테이너 개선사항

자바 8에선 컨테이너 환경의 메모리 및 CPU 제약사항이 잘 인식되지 않는 문제가 있었습니다. JVM 의 최대 힙크기를 호스트 메모리의 1/4로 설정하는 등의 문제였는데요,   
Java 10부터 JVM은 컨테이너 제어 그룹(cgroup)에서 설정한 제약 조건을 사용하여 메모리 및 CPU 제한을 설정합니다
예를 들어 기본 최대 힙 크기는 컨테이너 메모리 제한의 1/4입니다(예: -m2G의 경우 500MB).

JVM 옵션도 추가되어 Docker 컨테이너 사용자가 Java 힙에 사용할 시스템 메모리 양을 세밀하게 제어할 수 있습니다.

이 지원은 기본적으로 활성화되어 있으며 Linux 기반 플랫폼에서만 사용할 수 하네요!
* 대부분의 cgroup 활성화 작업은 jdk8u191을 기준으로 Java 8에도 업데이트 되었다고 합니다!

### 다중 릴리즈 jar 파일
원래 java 버전이 다르다면 jar파일을 여러개 만들어야 하는게 기존의 java였는데요, 하나의 jar파일로 여러 java 릴리즈 버전을 포함할 수 있는 multi release jar파일 방식이 도입되었습니다.

### 기타 기능
SHA-3을 활용한 새로운 해쉬 암호화 알고리즘을 도입하였습니다.
다양한 동시성 API와 Atomic 관련한 API가 추가되었습니다.
편리하게 Http 통신을 할 수 있는 Http Client API가 추가되었습니다! Http2 와 WebSocket도 지원합니다.
Collections API와 관련해 좀 더 쉽게 Collection을 만들 수 있는 팩토리 메소드가 추가되었습니다.

# 레퍼런스
https://docs.microsoft.com/en-us/java/openjdk/reasons-to-move-to-java-11 
https://daddyprogrammer.org/post/10411/jdk-roadmap-change-jdk9-11/
https://lts0606.tistory.com/457
https://www.baeldung.com/java-multi-release-jar

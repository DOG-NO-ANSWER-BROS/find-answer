# Java 8 & Java 11

## 변경사항

* 사업적인 방식으로 Java를 사용할 경우, Java 10이 라이센스 없이 무료로 사용할 수 있는 마지막 Oracle JDK다. Java 11부터는 Oracle로부터 무료로 LTS 지원을 받지 못한다.
* 물론 [Open JDK](https://jdk.java.net/)는 Oracle이 계속해서 지원한다. 그런데 Open JDK는 Oracle 말고도 많은 지원사가 있다.

### 기능
#### 1. Modularity

Java 11에서는 Java 9에서 추가된 [Modularity](https://www.baeldung.com/java-9-modularity) 기능을 사용할 수 있다. Modularity 기능은 프로그램이나 애플리케이션을 여러 모듈로 나눌 수 있게 해준다.

#### 2. --release 옵션

--release 는 -target 과 -source 가 합쳐진 옵션으로, -bootclasspath 옵션과 함께 사용하여 원하는 Java release 버전으로 컴파일 할 수 있도록 해주는 옵션이다.

#### 3. Multi-Release JAR (MRJAR)

jar 파일 안에 같은 class 파일이 있더라도 현재 사용하고 있는 [JVM의 버전이 무엇이냐에 따라서 사용하는 class 파일이 달라진다.](https://www.baeldung.com/java-multi-release-jar)

#### 4. var 

Java 10에서 등장한 기능으로, 로컬 변수에 사용하여 변수의 타입을 Java가 저절로 지정해주도록 할 수 있다.

#### 5. String 관련 메서드

Java 11부터는 다양한 문자열 관련 메서드가 추가된다.

* isBlank
* lines
* strip
* stripLeading
* stripTrailing
* repeat

#### 6. File 관련 메서드

파일로부터 문자열을 읽고 쓰기 쉬워졌다. File 클래스의 `readString`, `writeString` 메서드를 활용할 수 있다.
```java
Path filePath = Files.writeString(Files.createTempFile(tempDir, "demo", ".txt"), "Sample text");
String fileContent = Files.readString(filePath);
assertThat(fileContent).isEqualTo("Sample text");
```

#### 7. deprecated features
* Applet API가 deprecated 되면서 Java 브라우저 플러그인이 제거되었다.
* Concurrent Mark Sweep Garbage Collector (CMS)가 deprecated되고 더 이상 지원되지 않는다. Deprecated 된 이유는 GCC 관련 유지보수 일을 줄이기 위해서라고 한다.
* ECMAScript API가 빠르게 변하고 있어서 제거되었다. [Nashorn JavaScript 엔진](https://en.wikipedia.org/wiki/Nashorn_(JavaScript_engine))을 유지보수하는데 일이 많아서라고 한다.


## 질문
Java 17도 LTS 지원을 받는다는데, Java 17에는 어떤 기능들이 추가되었을까?

### 참고 자료
https://www.javatpoint.com/java-8-vs-java-11
https://www.baeldung.com/java-11-new-features

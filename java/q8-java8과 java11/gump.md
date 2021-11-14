# 정리

java8의 특징은 [이전 포스트](https://livenow14.tistory.com/73)에 다뤘어요. 이번엔 java 11을 다뤄보려 해요.

> Java 10은 라이선스 없이 상업적으로 사용할 수 있는 마지막 무료 `Oracle JDK` 였어요.
JAva 11부터는 `Open JDK`를 무료로 사용할 수 있어요.
> 

Java 11에서 추가된 내용들을 기능, 성능 관점에서 보도록 해요.  

## 기능 추가

### 1. String 클래스, 메서드 추가

isBlank, lines, strip, stripLeading, stripTrailing, and repeat가 추가됐어요. 

```java
String multilineString = "My name \n \n is \n Gump.";
List<String> lines = multilineString.lines()
  .filter(line -> !line.isBlank())
  .map(String::strip)
  .collect(Collectors.toList());

assertThat(lines).containsExactly("My name", "is", "Gump");
```

### 2. File 클래스, 메서드 추가

readString, writeString이 추가됐어요. 

파일에서 문자열을 더 게 읽고 쓸 수 있게 됐어요. 

```java
Path filePath = Files.writeString(Files.createTempFile(tempDir, "demo", ".txt"), "Sample text");
String fileContent = Files.readString(filePath);
assertThat(fileContent).isEqualTo("Sample text");
```

### 3. 컬랙션 인터페이스, 메서드 추가

컬랙션의 toArray() 메서드를 오버로딩하는 메서드가 추가됐어요.

원하는 타입의 배열을 선택할 수 있게 됐어요.

```java
List sampleList = Arrays.asList("Java", "Kotlin");
String[] sampleArray = sampleList.toArray(String[]::new);
assertThat(sampleArray).containsExactly("Java", "Kotlin");
```

### 4. Predicate 인터페이스, static not 메서드 추가

Predicate 인터페이스에 부정을 나타내는 not 메서드가 추가됐어요.

```java
#Predicate.java
static <T> Predicate<T> not(Predicate<? super T> target) {
    Objects.requireNonNull(target);
    return (Predicate<T>)target.negate();
}
```

예시

```java
List<String> sampleList = Arrays.asList("Java", "\n \n", "Kotlin", " ");
List withoutBlanks = sampleList.stream()
  .filter(Predicate.not(String::isBlank))
  .collect(Collectors.toList());
assertThat(withoutBlanks).containsExactly("Java", "Kotlin");
```

### 5. 람다에서 로컬 변수 Var 사용

람다식에서 var을 사용할 수 있게 됐어요. 

```java
List<String> sampleList = Arrays.asList("Java", "Kotlin");
String resultString = sampleList.stream()
  .map((@Nonnull var x) -> x.toUpperCase())
  .collect(Collectors.joining(", "));
assertThat(resultString).isEqualTo("JAVA, KOTLIN");
```

### 6. HTTP Client

Java 9에 도입된 HTTP 클라이언트가 표준 기능이 됐어요. (HTTP/1.1 과 HTTP/2을 지원헤요)전반적인 HTTP API의 성능이 향상댔어요. 

```java
HttpClient httpClient = HttpClient.newBuilder()
  .version(HttpClient.Version.HTTP_2)
  .connectTimeout(Duration.ofSeconds(20))
  .build();
HttpRequest httpRequest = HttpRequest.newBuilder()
  .GET()
  .uri(URI.create("http://localhost:" + port))
  .build();
HttpResponse httpResponse = httpClient.send(httpRequest, HttpResponse.BodyHandlers.ofString());
assertThat(httpResponse.body()).isEqualTo("Hello from the server!");
```

### 7. **Nest-Based Access Control**

중첩클래스들이 접근지정자에 상관없이 서로의 필드에 접근이 가능해졌어요. 

```java
class Test {  
    static class One {  
        private int one; 
     } 
     static class Two {  
        private int two;  
    }
 }
```

### 8. 자바 파일 실행

javac를 통해 컴파일 하지 않고도, 바로 실행할 수 있게 됐어요. 

```java
# before

$ javac HelloWorld.java
$ java HelloWorld 
Hello Java 8!
```

```java
# after

$ java HelloWorld.java
Hello Java 11!
```

## 성능향상

### 1. 동적 클래스 파일 상수

- CONSTANT_Dynamic이라는 새로운 상수 풀 형식을 지원해요.

### 2. 개선된 Aarch64

- 기존 문자열 및 배열 내장 함수가 최적화 됐어요.
- java.lang.Math 의 sin, cos, log 메서드에 대한 새로운 내장 함수를 구현해요.

### 3. 새로운 GC

- Epsilon이라는 새로운 GC가 실험적으로 나왔어요.
- 기본은 Z GC

### 4. JFR

- 애플리케이션에서 진단 및 프로파일링 데이터를 수집하는 데 사용할 수 있는 프로파일링 도구가 나왔어요.

## Removed

### 1. **Java EE and CORBA**

앞으로 EE 나 CORBA 모듈이 필요한 경우 명시적으로 의존을 추가해야 해요.

### 2. JMC and JavaFX

FX 라이브러리가 OpenJFX 프로젝트로 옮겨가면서 코어에서 삭제됐어요.

## Deprecated

### 1. Nashorn JavaScript engine

### 2. Pack200 compression scheme for JAR files

## Java8의 사용이유

저는 최근까지도 Java 8을 사용했는데요. `"Java 11이 있는데 Java 8을 왜 사용했나?"`라는 질문이 자주 들어와서 이유를 정리해볼해요. 

### 1. 외부 개발 툴과의 연동성

웹서비스를 구동할 때, 자바 애플리케이션 외에 툴들이 필요한 경우가 있는데요. 이런 툴과의 연동성 문제에서 가장 안정적이라 생각해요. Java 11 부터 요금 정책이 바뀌었기 때문에, Oracle JDK가 아닌 Open JDK를 사용해야 하면서 생기는 이슈들이 꽤 있었어요. 

### 2. Java 11에서의 변경사항이 크게 없음

메서드, 기능들이 어느정도 추가됐지만, Java8에서와 크게 차이가 나지 않기 때문에, Java 11을 사용해야하는 큰 이유를 찾지 못햇어요. 
또한, Java 8 → Java 11에서의 문제가 크게 없기 때문에. 이후 릴리즈가 지원을 하지 않을 때, 버전 업그레이드를 한다 해도 괜찮다 생각해요. 

<br> 

# 질문

## 나만 알만한 것

### 질문

Java11도 있는데, Java8을 왜 쓸까요?

### 답변

본문에 있음.
# 정리

## Java7의 특징

### Try-with-resources 지원

try문 안에서 사용되는 Connection, Files, Input/OutStream 등과 같은 자원들을 자동적으로 해제를 할 수 있게 되었어요. 물론 이전 형식도 지원을 해요. 
try문 안에 자원을 선언하면 돼요. 

`Before`

```java
BufferedReader br = new BufferedReader(new FileReader(path));
try {
   return br.readLine();
} finally {
   br.close();
}
```

`After`

```java
try (BufferedReader br = new BufferedReader(new FileReader(path)) {
   return br.readLine();
}
```

중복 자원도 허용해요. 

```java
try (final InputStream inputStream = connection.getInputStream();
             final OutputStream outputStream = connection.getOutputStream()){
//code
}
```

### 다이아몬드 연산자를 활용한 Type Reference(타입 추론)지원

타입 추론(Type Inference)란 코드 작성 당시 타입이 정해지지 않았지만, 컴파일러가 그 타입을 유추하고 결정하는 것이에요.

생성자 호출시에 필요한 **파라미터(매개변수)**를 다이아몬드 연산자(`<>`)을 사용함으로 생략할 수 있습니다.

`Before`

```java
Map<String, List<String>> myMap = new HashMap<String, List<String>>();
```

이전에는 위와 같이 **파라미터(매개변수)**를 선언했어야 했지만,

`After`

```java
Map<String, List<String>> myMap = new HashMap<>();
```

위와 같이, 파라미터가 있는 생성자의 형식을 **빈 파라미터(매개변수) 집합(<>)**으로 대체할 수 있어요.

### 다중 Exception Catching

하나의 catche 블록에서 여러개의 예외처리가 가능하게 됐어요

`Before`

```java
try{
//code
} catch (FirstException ex) {
     log.info(ex);
     throw ex;
} catch (SecondException ex) {
     log.info(ex);
     throw ex;
}
```

`After`

```java
try{
//code
} catch (FirstException | SecondException ex) {
     log.info(ex);
    throw ex;
}
```

### 숫자 리터럴에서 언더스코어(underscore) 지원

리터럴이란 **프로그램에서 직접 표현한 값을 뜻해요.**
쉽게 얘기해서 소스 코드(변수에 넣는 데이터)의 고정된 값을 의미하는 용어예요.
리터럴 값에 언더스코어를 지정할 수 있게 됐어요. 

`Before`

```java
int oneMillion = 1000000;
```

`After`

```java
int oneMillion = 1_000_000;
```

### Switch문에서 String 객체 사용가능

Switch문에서 String객체가 사용가능하게 됐어요.
컴파일러는 연결된 `if-then-else문` 보다 `String 객체를 사용하는 switch문`에서 효율적인 바이트 코드를 생성해요. 

`Before`

```java
String text = "baz";
if("foo".equals(text)){
    someMemthod();
} else if ("bar".equals(text)){
    someMemthod2();
} else if ("baz".equals(text)) {
    someMemthod3();
}
```

`After`

```java
String text = "baz";
switch (text) {
    case "foo":
        someMemthod();
        break;
    case "bar":
        someMemthod2();
        break;
    case "baz":
        someMemthod3();
        break;
}
```

### @SafeVarargs 지원

자바1.5에 추가된 varargs(가변인자)를 사용하며 나타나는 예외를 없애기 위해 사용되는 애노테이션이에요.
가변인자란, 파라미터 변수에 `...`을 변수명 앞에 붙이면, 컴파일러가 파라미터를 배열 형식으로 바꾸어버리고, 파라미터로 주어지는 변수들을 모아서 배열 형식의 객체로 만드는 것이에요.

`Before`

```java
public static <T> T[] unsafe(T... elements) {
    return elements; // unsafe! don't ever return a parameterized varargs array
}
```

```java
warning: [unchecked] Possible heap pollution from parameterized vararg type T
  public static <T> T[] unsafe(T... elements) {
```

`After`

```java
public class Machine<T> {
    private List<T> versions = new ArrayList<>();

    @SafeVarargs
    public final void safe(T... toAdd) {
        for (T version : toAdd) {
            versions.add(version);
        }
    }
}
```

애노테이션을 통해 컴파일 예외가 발생하지 않음

## Java8의 특징

### Heap Permanent Generation 제거

![https://user-images.githubusercontent.com/48986787/133349158-3eb69488-00bf-4532-8541-8ab192a39404.png](https://user-images.githubusercontent.com/48986787/133349158-3eb69488-00bf-4532-8541-8ab192a39404.png)

이전에는 초기 설정시, 메모리를 설정 했어야 하는데(PermSize, MaxPermSize) Permanent Generation이 Metaspace로 대체되었어요. Metaspace는 런타임 시 메모리 요구 사항에 따라 자체 크기를 조정해요.
필요시 "MaxMetaspaceSize" 매개변수를 설정하여 Metaspace의 양을 조정할 수 있어요.

### Interface Default and Static Methods

인터페이스에 디폴트 메서드와, 정적 메서드를 포함시킬 수 있게 됐어요 
디폴트 메서드는 구현클래스의 인스턴스를 통해 액세스 할 수 있으며, 재정이 할 수 있어요.

```java
default String getOverview() {
    return "ATV made by " + producer();
}
```

```java
Vehicle vehicle = new VehicleImpl();
String overview = vehicle.getOverview();
```

정적 메서드는 인터페이스를 통해서만, 인터페이스 내부에서 사용할 수 있어요. 즉. 구현 클래스에서 재정의 할 수 없어요.

```java
static String producer() {
    return "N&F Vehicles";
}
```

```java
String producer = Vehicle.producer();
```

### Functional interfaces

함수형 인터페이스 

### Lambda expressions

람다 표현식

### Method References

메서드 참조

### Date and time API 지원

날짜와 시간 API 제공

### Optional<T> 지원

Optional을 제공하여 null에 대한 참조를 안전하게 해줌

### Collectors

### forEach

### Stream API

### Stream Filter

### StringJoiner

### Parallel Array Sorting (배열 정렬의 병렬처리)

배열을 정렬할 때, Arrays.sort를 사용하는데, 이때 Merge Sort 나 Tim Sort 알고리즘을 사용했어요. 이는 순차적으로 실행된다는 단점이 있었는데, `Arrays.parallelSort`가 추가되며 병렬처리가 가능해졌어요. 
성능 향상을 보려면 배열의 크기 일정해야해요. 일부 비교에서는 개선 사항을 보기 시작하려면 배열에 약 2백만 개의 요소가 필요하다고 말해요.

### Base64 encoding and decoding 지원

encoding과 decoding을 위해 Base64클래스를 지원해요. 이를 사용하려면 java.util.Base64클래스를 가져오면돼요. 이 클래스는 각 레벨에서 정보를 암호화 하기 위해 3가지 다른 인코더와 디코더를 제공한다고 해요. [Java Base64 Encode and Decode](https://www.javatpoint.com/java-base64-encode-decode)

### Encoding

```java
String base64 = Base64.getEncoder().encodeToString("string to encode".getBytes("utf-8"));
```

### Decoding

```java
byte[] asBytes = Base64.getDecoder().decode("your base 64 string");
```

<br> 

### 질문

varargs란? 

### 답변

본문에 있음
# 정리

언젠가는 한번 정리해봐야지 했는데, 정리 하다보니 역시 Java8이 오랬동안 사랑받는 이유를 알거 같아요(추가, 변경사항이 엄청나군요..!). 가장 큰 특징들을 그나마 추려봤어요. 

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

<br>

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

하나의 기능을 제공하는 단 하나의 추상메서드를 정의하는 인터페이스가 추가됐어요.
기능을 위해 default메소드는 원하는 만큼 추가할 수 있어요.
@FunctionalInterface와 함께 쓰이며, 부적절하게 사용될 때 컴파일 하지 않게 할 수 있어요.

`Comsumer`

```java
@FunctionalInterface
public interface Consumer<T> {

    /**
     * Performs this operation on the given argument.
     *
     * @param t the input argument
     */
    void accept(T t);

    /**
     * Returns a composed {@code Consumer} that performs, in sequence, this
     * operation followed by the {@code after} operation. If performing either
     * operation throws an exception, it is relayed to the caller of the
     * composed operation.  If performing this operation throws an exception,
     * the {@code after} operation will not be performed.
     *
     * @param after the operation to perform after this operation
     * @return a composed {@code Consumer} that performs in sequence this
     * operation followed by the {@code after} operation
     * @throws NullPointerException if {@code after} is null
     */
    default Consumer<T> andThen(Consumer<? super T> after) {
        Objects.requireNonNull(after);
        return (T t) -> { accept(t); after.accept(t); };
    }
}
```

### Lambda expressions

람다식은 메서드로 전달할 수 있는 익명 함수를 단순화 한 것이에요.
쉽게 객체 대신 코드를 메서드의 파라미터로 전달하거나, 데이터 그룹을 처리하여 알고리즘을 실행하는데 사용돼요.

**내부/외부 Iteration**

외부반복

```java
for (String value: myCollection) {
		System.out.println(value);
}
```

내부반복

```java
myCollection.forEach(value -> System.out.println(value));
```

**passing code with behavior parameterization** (동적 파라미터화 코드 전달)

`Before`

```java
public class Apple {
    private final int weight;

    public Apple(int weight) {
        this.weight = weight;
    }

    public int getWeight() {
        return weight;
    }
}
```

```java
void before() {
    List<Apple> apples = Arrays.asList(new Apple(10),
            new Apple(30),
            new Apple(50),
            new Apple(70));
    List<Apple> inventory = new ArrayList<>();

    for (Apple apple : apples) {
        if (apple.getWeight() > 50) {    //동작
            inventory.add(apple);
        }
    }
    for (Apple apple : inventory) {
        System.out.println("apple.getWeight() = " + apple.getWeight());
    }
}
```

위에서의 동작은 조건문 부분이라 볼 수 있어요. 이부분을 동작 파라미터를 사용해서 리팩토링해보면 

`After`

```java
void after() {
    List<Apple> apples = Arrays.asList(new Apple(10),
            new Apple(30),
            new Apple(50),
            new Apple(70));
    List<Apple> inventory = filterApples(apples, it -> it.getWeight() > 50 );

    for (Apple apple : inventory) {
        System.out.println("apple.getWeight() = " + apple.getWeight());
    }
}

public static List<Apple> filterApples(List<Apple> apples, Predicate<Apple> p) {
    List<Apple> inventory = new ArrayList<>();
    for (Apple apple : inventory) {
        if (p.test(apple)) {
            inventory.add(apple);
        }
    }
    return inventory;
}
```

같은 결과를 출력하는 것을 알 수 있다.

### Method References

메서드 참조는 람다식을 좀더 짧고 읽기 쉽게 사용하는 것이에요. 메서드 참조에는 4가지 방법이 있어요. 

**정적 메서드 참조**

정적 메서드에 대한 참조는 `클래스::메소드명`으로 사용돼요. 먼저 람다식을 사용한 경우를 살펴보면 아래와 같아요.

```java
boolean isReal = list.stream().anyMatch(u -> User.isRealUser(u));
```

이를 메서드 참조로 변경하면

```java
boolean isReal = list.stream().anyMatch(User::isRealUser);
```

위와 같이 사용돼요. 

**인스턴스 메서드 참조**

인스턴스 메서드에 대한 참조는 `인스턴스::메소드명`으로 사용돼요.
아래 코드는 User의 isLegalName(String string)을 호출해요.

```java
User user = new User();
boolean isLegalName = list.stream().anyMatch(user::isLegalName);
```

**특정 유형의 객체의 인스턴스 메서드 참조**

특정유형(String, BigDecimal 등)

```java
long count = list.stream().filter(String::isEmpty).count();
```

**생성자 참조**

생성자 참조는 `클래스명::new`로 사용해요. 생성자는 특수한 메소드이기 대문에, 메소드 이름으로 new를 사용해요. 

```java
Stream<User> stream = list.stream().map(User::new);
```

### Date and time API 지원

이전에는 날짜와 시간을 처리하는 것이 굉장히 귀찮았어요. 월요일이 0 또는 1으로 시작하는지, moth는 0으로 시작하는 지 등을 파악 했어야해요.
이를 편리하게 해주는 API가 추가됐어요.

`javax.time.Clock`

```
Clock.systemUTC(); //current time of your system in UTC.
Clock.millis();//time in milliseconds from 1/1/1970.
```

`javax.tme.ZoneId`

```
ZoneId zone = ZoneId.of(“Europe/London”);//zoneId from a timezone.
Clock clock = Clock.system(zone);//set the zone of a Clock.
```

`javax.time.LocalDate`

```
LocalDate date = LocalDate.now();//current date
String day = date.getDayOfMonth();//day of the month
String month = date.getMonthValue();//month
String year = date.getYear();//year
```

### Optional<T> 지원

Optional을 제공하여 null에 대한 참조를 안전하게 할 수 있게 됐어요.
이전에는 어떠한 객체를 참조할 때 null인지 아닌지 검사를 무조건 했어요(코드량이 많아지면 엄청 귀찮았겟죠?).
이러한 불편함을 해소하기 위해, NPE를 얻을 상황을 처리하는 Optinal이 추가가 됐어요.
Optinal <T>에서, T의 값이 null이면, 빈 Optinal를 반환하기에, 비어있을 경우 정의된 작업을 처리할 수 있게 됐어요.
`befer`

```java
User user = getUser();
if (user != null) {
    Address address = user.getAddress();
    if (address != null) {
        String street = address.getStreet();
        if (street != null) {
            return street;
        }
    }
}
return "not specified";
```

`after`

```java
Optional<User> user = Optional.ofNullable(getUser());
String result = user
  .map(User::getAddress)
  .map(Address::getStreet)
  .orElse("not specified");
```

아래와 같이 Optional에서 제공하는 메서드 들을 통해, null참조 가능성이 있는 상황에 유용하게 쓰일 수 있게 됐어요. (더 많은 메소드는 java.util.Optional확인)

**Optional.empty()**

```java
Optional<String> optional = Optional.empty();
```

**Optional.ofNullable(T t) && Optional.of(T t)**

```java
Optional<String> optional = Optional.ofNullable(getString());
```

### Stream API

Stream이란 순차/병렬 작업을 지원하는 어떠한 순차적인 요소예요.  (java.util.stream.Stream)
쉽게 스트림을 데이터 컬렉션 반복을 멋지게 처리하는 기능이라 생각하면 편해요.

간단히 특징에 대해 정리하면

- 스트림 API의 특징에는 선언형, 조립할 수 있음, 병렬화가 있다.
- 스트림은 연속된 요소, 중간연산, 최종연산으로 이루어진다.
- 자바 컬렉션은 외부반복, 스트림은 내부반복을 사용한다.
- 중간 연산은 파이프라인으로 구성되어 최종 연  산에서 한 번에 처리된다. 이를 지연 계산된다고 한다.
- 중간 연산은 stream을 반환하고, 최종 연산은 스트림이 아닌 결과을 반환한다.
- 중간 연산의 예시로는 map, filter, flatMap 가 있고, 최종 연산의 예시로는 count, foreach, collect가 있다.

**데이터 컬렉션(컬렉션)과 스트림의 차이는?**

문서에도 잘 나와있지만, 둘의 차이는. 크게 목표하는 바가 다르다는 거에요.
컬렉션은 데이터를 어떻게 저장/관리하고 접근하는지를 목표하고 있어요.
이와 반대로 스트림은 데이터를 직접 접근하거나 조작하는 기능을 제공하지 않으며, 데이터를 어떻게 계산할지에 대한 목표를 가지고있어요.
즉, 데이터의 저장/관리가 목적이라면 컬렉션을, 데이터의 계산이 목적이라면 스트림을 사용하는게 좋다고 볼 수 있어요.

### Collections

Stream API와 Collections API가 통합되어 Iterator를 상속하는 컬랙션과 Map 컬렉션에 대한 대량  작업이 가능하게 됐어요.

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

## Refer

- [http://openjdk.java.net/projects/jdk8/features#150](http://openjdk.java.net/projects/jdk8/features#150)
- [https://manifesto.co.uk/java-jdk-8-features/](https://manifesto.co.uk/java-jdk-8-features/)
- [https://www.baeldung.com/java-8-new-features](https://www.baeldung.com/java-8-new-features)

### 질문

varargs란? 

### 답변

본문에 있음
# 리플렉션이란
애플리케이션 런타임 때 클래스, 인터페이스, 메서드, 그리고 필드에 대한 정보를 확인하고 수정할 수 있게 해주는 API입니다. 뿐만 아니라 새로운 객체를 초기화할 수도 있고, 메서드를 호출할 수도 있어요. 필드 값을 주입해줄 수도 있죠.

여기 `name` 필드와 `age` 필드 두 개만 가지고 있는 `Person` 필드가 있습니다. 생성자와 메서드는 만들지 않고 저 두 필드만 존재하는 클래스입니다. (물론 저렇게 하면 디폴트 생성자가 생기겠죠)
```java
public class Person {
    
    private String name;
    private int age;

}
```
해당 `Person` 클래스의 필드를 하나 가지고 오는 테스트를 작성해보겠습니다. Reflection을 제대로 사용해보기 위해 레퍼런스 타입을 Object로 하고, `getFields()` 메서드는 public 필드만 가지고 오니까 `getDeclaredFields()` 메서드를 사용하겠습니다. 
```java
class PersonTest {

    @Test
    void getFields() {
        Object person = new Person();
        Field[] fields = person.getClass().getDeclaredFields();
        System.out.println(fields[0]);
        // private java.lang.String reflectiontags.Person.name 출력
    }
}
```
* 앞서 말한 것처럼 `getFields()` 메서드는 public 필드만 가지고 옵니다.
* `getDeclaredFields()` 메서드는 public, protected, 그리고 private 필드까지 모두 가지고 옵니다.
* 결과를 보면 `Field` 클래스를 출력할 경우 "[접근 제한자] [클래스 타입] [패키지.클래스.필드_이름]" 형태로 출력되는 것을 볼 수 있습니다. 우리가 원하는 String 형태로 다음과 같이 간단하게 바꿀 수 있습니다.
```java
    private List<String> getFieldNames(Field[] fields) {
        return Arrays.stream(fields)
                .map(Field::getName)
                .collect(Collectors.toList());
    }
```

## 클래스 이름
이제 reflection api의 더 강력한 기능을 사용해보기 위해 추가 코드를 작성합니다. Eating 인터페이스를 구현하는 Animal 클래스를 만든 뒤, Animal 클래스를 상속받은 Goat 클래스를 만들겁니다. 그리고 Goat 클래스는 추가로 Locomotion 이라는 인터페이스를 구현하도록 해보겠습니다. [이 곳](https://www.baeldung.com/java-reflection) 에서 더 자세한 설명을 보실 수 있습니다.
```java
public interface Eating {

    String eats();
}
```
```java
public interface Locomotion {

    String getLocomotion();
}
```
```java
public abstract class Animal implements Eating {

    public static String CATEGORY = "domestic";

    private String name;

    protected Animal(String name) {
        this.name = name;
    }

    protected abstract String getSound();

    public static String getCATEGORY() {
        return CATEGORY;
    }

    public static void setCATEGORY(String CATEGORY) {
        Animal.CATEGORY = CATEGORY;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```
```java
public class Goat extends Animal implements Locomotion {

    protected Goat(String name) {
        super(name);
    }

    @Override
    protected String getSound() {
        return null;
    }

    @Override
    public String eats() {
        return null;
    }

    @Override
    public String getLocomotion() {
        return null;
    }
}

```
여기서 각 인터페이스와 클래스에 대한 설명을 딱히 안 적었는데요. 지금은 해당 코드가 어떤 역할을 하는지 아는 것이 그리 중요하지 않기 때문입니다. Reflection API를 이용해서 런타임 때 해당 코드의 정보를 가지고 와보겠습니다. 먼저 클래스의 이름입니다.
```java
    @Test
    void givenObject_whenGetsClassName_thenCorrect() {
        // given
        Object goat = new Goat("goat");

        // when
        Class<?> clazz = goat.getClass();

        // then
        assertEquals("Goat", clazz.getSimpleName());
        assertEquals("reflectiontags.Goat", clazz.getName());
        assertEquals("reflectiontags.Goat", clazz.getCanonicalName());
    }
```
* `getSimpleName()` 메서드를 사용하면 해당 클래스의 이름을 가지고 옵니다.
* `getName()` 메서드를 사용하면 패키지명을 포함한 이름을 가지고 옵니다.
* `getCanonicalName()` 또한 'get 정식 이름'이라는 의미로, 패키지명을 포함한 클래스 이름을 가지고 옵니다.

다음과 같이 패키지명과 클래스명을 이용해서 해당 클래스를 객체로 가지고 올 수도 있습니다. 만약 이름이 잘못되었다면 `ClassNotFoundException`이 발생하죠. 실제 객체를 초기화하기엔 메모리가 낭비가 크다고 생각할 때 특히 이 방식을 사용해서 클래스 정보를 가지고 올 수 있습니다.
```java
    @Test
    void givenClassName_whenCreatesObject_thenCorrect() throws ClassNotFoundException {
        // when
        final Class<?> clazz = Class.forName("reflectiontags.Goat");

        // then
        assertEquals("Goat", clazz.getSimpleName());
        assertEquals("reflectiontags.Goat", clazz.getName());
        assertEquals("reflectiontags.Goat", clazz.getCanonicalName());
    }
```

## 클래스 제어자
Reflection API에서 `getModifier()` 메서드를 이용해서 클래스의 제어자를 가지고 올 수 있습니다. Integer 타입으로 반환하는데, 클래스의 각 제어자가 비트 플래그 형식으로 되어있어서 해당 타입으로 표현할 수 있습니다. `Modifier` 클래스에 있는 제어자의 유무 / 종류를 확인할 수 있는 유틸성 메서드를 활용하여 추출해온 제어자를 다룰 수 있습니다.
```java
    @Test
    void givenClass_whenRecognisesModifiers_thenCorrect() throws ClassNotFoundException {
        // given
        Class<?> goatClass = Class.forName("reflectiontags.Goat");
        Class<?> animalClass = Class.forName("reflectiontags.Animal");

        // when
        int goatMods = goatClass.getModifiers();
        int animalMods = animalClass.getModifiers();

        // then
        assertTrue(Modifier.isPublic(goatMods));
        assertTrue(Modifier.isAbstract(animalMods));
        assertTrue(Modifier.isPublic(animalMods));
    }
```

## 클래스 생성자, 메서드, 필드

`getClass()`를 이용하여 객체 의 클래스 정보를 가지고 온 다음, 내가 원하는 그 클래스의 정보를 추출해오는 방식은 이제 익숙해졌습니다. 해당 방식을 이용하면 클래스의 생성자, 메서드, 필드 뿐만 아니라 그 클래스가 구현하고 있는 인터페이스도 가지고 올 수 있습니다. (직접 구현안 인터페이스만 가지고 옵니다. 즉, 부모 클래스가 인터페이스를 구현한다고 해서 그 인터페이스까지 함께 불러오지 않습니다)
```java
// 생성자
Constructor<?>[] constructors = goat.getClass().getConstructors();
// 인터페이스
Class<?>[] interfaces = goat.getClass().getInterfaces();
// 메서드 (앞서 필드의 예시와 동일하게 getName()을 이용해서 String으로 사용가능
Method[] methods = goat.getClass().getDeclaredMethods();
// 필드 (없으면 NoSuchFieldException을 던진다)
Field field = animal.getClass().getDeclaredField("name");
// 필드 타입
Class<?> fieldType = field.getType();
```

## 질문
### `object.getClass()` 와 `MyClass.class` 의 차이는 무엇일까요?
다음과 같은 테스트를 작성해보면 별 문제 없이 테스트가 통과합니다.
```java
@Test
public void givenObjectAndType_whenGettingClassObject_thenTwoMethodsHaveTheSameResult() {
    String str = "안녕하세요 검형 웨형 제로투형";

    Class stringClass = str.getClass();
    Class clazz = String.class;

    assertSame(stringClass, clazz);
}
```
즉, 두 방식을 사용하면 같은 Class 객체를 가지고 온다는 것을 알 수 있습니다. 
하지만 함정이 있습니다. 언제나 같은 객체를 가지고 오는 것이 아니거든요. 다음과 같이 `Animal` 클래스를 상속받는 `Bear` 클래스가 있다고 해보겠습니다:
```java
public class Animal {
    protected int legCount;
}

public class Bear extends Animal {
    // ...
}
```
그러고 아래의 테스트를 작성하면 테스트가 실패합니다:
```java
@Test
public void givenClassInheritance_whenGettingRuntimeTypeAndStaticType_thenFail() {
    Animal animal = new Bear();

    Class runtimeType = animal.getClass();
    Class staticType = Animal.class;

    assertSame(staticType, runtimeType);
}
```
**결과**
```java
org.opentest4j.AssertionFailedError: 
Expected :class reflectiontags.PersonTest$Animal
Actual   :class reflectiontags.PersonTest$Bear
```
클래스 타입을 정적으로 가지고 오는지, 또는 동적으로 가지고 오는지에 따라 그 정보가 달라지기 때문에 테스트가 실패하는 것으로, `Animal animal = new Bear()` 에서 `animal`은 런타임 때 `Bear` 타입이 되는 것을 알 수 있습니다.

다음 테이블을 참고하여 추가적인 차이점을 확인해 볼 수도 있습니다.

| | object.getClass() | MyClass.class |
|---|---|---|
| Class objects | 객체의 런타임 타입 | MyClass의 정적 타입 |
| 원시값들 (int, boolean 등) | 사용할 수 없음 | 사용 가능 |
| 인터페이스, 추상 클래스, 또는 초기화 할 수 없는 클래스들 | 당연히 사용 불가능 | 사용 가능 |

##### 참고
[리플렉션 가이드](https://www.baeldung.com/java-reflection)
[getClass와 class의 차이](https://www.baeldung.com/java-getclass-vs-class)

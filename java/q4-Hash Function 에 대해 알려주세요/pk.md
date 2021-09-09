# Hash Function

Hash Function은 가변적인 정보를 고정된 크기의 값으로 바꿔준다. 알고리즘, 자료구조, 그리고 암호화 같은 곳에 많이 쓰인다.

* 해싱은 단방향이다. 해싱된 값으로 오리지널 데이터를 복원하지 못한다.
* 특정 데이터는 해싱 할 때마다 동일한 결과가 나온다.
* 결과가 가능한 범위 내에 골고루 분배되어야 한다.
* 앞서 말했듯, 고정된 크기의 결과가 만들어진다.
* 충돌을 방지하기 위해 충분히 복잡한 방식을 거쳐야 한다.

## Java String 클래스에서의 해싱 함수
```java
    public int hashCode() {
        int h = hash;
        if (h == 0 && value.length > 0) {
            char val[] = value;
            for (int i = 0; i < value.length; i++) {
                h = 31 * h + val[i];
            }
            hash = h;
        }
        return h;
    }
```
이펙티브 자바에서는 위의 함수에서 31이 홀수이면서 소수이기 때문에 선택되었다고 한다. 약수가 있는 수를 사용하면 충돌이 발생할 가능성이 더 높기 때문이라고.

자바에서 위의 String 클래스 hashCode() 메서드를 실행하면 다음과 같은 결과가 나온다.
```java
public class Test {
    public static void main(String[] args) {
        String letter = "h";
        String myString = "gump 02 and wedge";
        String yourString = "noAnswer bros";
        String another = "hello I'm PK";
        System.out.println(letter.hashCode()); // 104
        System.out.println(myString.hashCode()); // 922089276
        System.out.println(myString.hashCode()); // 922089276
        System.out.println(yourString.hashCode()); // 531383509
        System.out.println(yourString.hashCode()); // 531383509
        System.out.println(another.hashCode()); // -1600944230
        System.out.println(another.hashCode()); // -1600944230
    }
}
```
위에서 살펴본 해시 함수의 특징을 여기서 볼 수 있다. 결과를 다시 기존의 String 값으로 바꿀 수 없고, 같은 값은 항상 동일한 결과가 나오며, 고정된 크기를 가진 값이 나온다. (int 자료형은 4 바이트)

## 해시 테이블
해싱을 할 때 가장 많이 사용하는 자료구조 중 하나가 바로 해시 테이블이다.
* 해시 테이블은 `key-value` 형태로 자료를 보관한다.
* 배열 안에 정보를 담는다.
* key는 유니크한 값이어야 한다.
* 이미 존재하는 key를 이용하여 데이터를 넣는 경우 자료가 덮어 씌워진다. (구현체에 따라 다를 수도 있다)

## 충돌
의도하지 않게 해시 함수가 서로 다른 데이터를 같은 해시값으로 만들 수도 있다. 해시 테이블은 다음과 같은 방식으로 해당 상황을 해결한다:
### 1. 다른 체이닝을 만든다.
충돌하는 해시값이 있다면 새로운 자료구조를 만들어서 충돌하는 값들을 보관한다. 대부분 리스트 형식을 사용한다.
#### 특징
* 구현하기 쉽다
* 해시 테이블이 꽉 찰 일이 없다. 언제나 체이닝을 이용하여 값을 추가할 수 있다.
* 얼마나 많은 양의 값이, 얼마나 자주 추가 또는 삭제될지 예상하기 힘들 때 사용한다.
* 하지만 캐시 성능이 그리 좋지 않다. 대부분 리스트 형식을 사용하여 보관한다고 했는데, 이게 linked list이기 때문. linked list는 조회할 때 시간 복잡도가 O(n)이다.
* 메모리를 낭비할 수 있다. 일부 테이블 공간이 쓰이지 않을 가능성이 있기 때문이다.

### 2. open addressing
충돌이 발생하면 빈자리를 찾아 저장한다. Linear Probing과 Quadratic Probing이 있다.
#### Linear Probing
충돌이 발생했을 때 그 다음 공간이 비어있는지 살핀 후, 비어있다면 그 곳에 넣는 방식이다. 그런데 이 방식은 충돌이 잦을 수록 데이터가 한 곳에 몰리고, 더 많은 충돌을 발생시킬 수 있다.
#### Quadratic Probing
그래서 "좀 더 멀리 있는 빈 공간을 찾는" Quadratic Probing이 나왔다. 예를 들어 Linear Probing이 n 칸 뒤의 공간을 검사한다면 Quadratic Probing은 n 제곱 칸 뒤의 공간을 검사하는 방식.
* 하지만 이 방식도 hashCode 값이 같다면 동일한 공간을 검사하게 되는 건 같다. 그래서 Double Hash(이중 해시)이 나왔는데, Hash 함수 두 가지를 적용하는 방식이다.

## hashCode()
IDE마다 구현하는 hashCode() 메서드의 방식은 다르다 (심지어 Lombok도 hashCode()를 만들어 주는데, 나름의 방식이 있다). IntelliJ가 만드는 hashCode()는 다음과 같다:
```java
@Override
public int hashCode() {
    int result = (int) (id ^ (id >>> 32));
    result = 31 * result + name.hashCode();
    result = 31 * result + email.hashCode();
    return result;
}
```
따라서 우리는 hashCode()를 만드는 절대적인 정답은 없다는 것을 알 수 있다.

## 질문
* 해시 테이블에 있는 key - value 정보에서 key 값을 외부에서 변경하면 어떤 일이 발생할까?
```java
    public static void main(String[] args) {
        String key = "hihi";
        String value = "byebye";
        Map<String, String> myMap = new HashMap<>();
        myMap.put(key, value);
        key = "hellohello";
        System.out.println(myMap.get(key)); // null
    }
``` 
위의 코드를 실행하면 null이 나온다.
```java
    public static void main(String[] args) {
        String key = "hihi";
        String value = "byebye";

        String anotherKey = "hellohello";
        String anotherValue = "go away";

        Map<String, String> myMap = new HashMap<>();

        myMap.put(key, value);
        myMap.put(anotherKey, anotherValue);
        key = "hellohello";
        System.out.println(myMap.get(key)); // "go away"
    }
```
그리고 위의 코드를 실행하면 `key`를 이용해서 조회했음에도 불구하고 `anotherKey`와 연결된 `anotherValue` 값이 나오는 것을 볼 수 있다. 즉, hashCode를 이용하여 자료를 관리할 때는 key 값을 변경하면 기존의 그 key와 연결된 value를 찾을 수 없다.

### 질문 2
* 앞서 말한 31 이라는 숫자가 홀수이면서 소수여서 해시값을 만들기 위해 적합하다고 했는데, 홀수이면서 소수인 수는 그 외에도 많지 않나?
31이라는 숫자는 홀수 & 소수라는 특성 말고 곱셈을 비트 단위 연산으로 할 수 있다는 특징도 갖고 있다. 비트 단위 연산은 일반 곱셈보다 속도가 빠르다고 한다!
```java
31 * i == (i >> 5) - i
```
ASCII 형태의 문자를 사용하던 시절에 사람들이 실제로 50,000 개의 영단어를 사용하여 어떤 수가 충돌도 덜 발생하고 효율적일지를 고려하여 31이라는 수를 결정했다고 한다. 하지만 요즘과 같은 UTF-8 시대에는 31이라는 수도 충돌이 잦다고... 524287 과 같은 수를 사용하면 `524287 * i == i << 19 - i`와 같은 비트 단위 연산도 가능하고 UTF-8 에서 사용하는 문자 관련해서는 충돌이 (당연히) 덜 발생한다고 한다. 요즘 컴퓨터 성능을 생각해보면 이 정도는 크게 성능 이슈를 발생시키지 않기도 하고! 그래서 어떤 사람들은 이 31이라는 수가 요즘 세상엔 적절하지 않다고 말하기도 한다. 흥미로워서 야크 셰이빙 좀 해봤습니다.

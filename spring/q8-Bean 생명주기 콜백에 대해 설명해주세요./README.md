## 컨테이너 생명주기

스프링 컨테이너는 빈 객체들을 관리. 객체들을 싱글턴으로 제공하거나 의존성 주입하는 것 뿐만 아니라 생성과 소멸, 생명주기를 관리한다. 컨테이너는 초기화될 때 빈 객체들을 생성, 주입하고 종료할 때 빈 객체들은 소멸된다.

![https://user-images.githubusercontent.com/63634505/123973337-a4808380-d9f6-11eb-8ddd-57625f195fde.png](https://user-images.githubusercontent.com/63634505/123973337-a4808380-d9f6-11eb-8ddd-57625f195fde.png)

## 빈 생명주기

### 객체 생성 → 초기화 → 사용 → 소멸

자바 설정, 어노테이션, XML 등으로 설정 파일을 읽어 빈 객체를 생성(인스턴스화) →

프로퍼티 설정 파일을 읽어 해당되는 빈 객체에 의존성 주입 →

Aware시리즈 인터페이스(BeanNameAware, BeanFactoryAware 등)을 구현한 경우 해당되는 메서드 호출

### 초기화 메소드

초기화 메소드는 빈 오브젝트가 생성되고 DI 작업까지 마친 다음에 실행되는 메소드를 말한다. 오브젝트의 기본적인 초기화 작업은 생성자에서 진행하면 된다. 하지만 DI를 통해 모든 프로퍼티가 주입된 후에야 가능한 초기화 작업도 있다. 이 때 초기화 메서도를 사용한다

- 초기화 콜백 인터페이스

    → InitializingBean 인터페이스를 구현, afterPropertiesSet 메서드를 오바리이딩 하여 사용

- init-method 지정 → XML 을 이용해 빈을 등록할 시
- @PostConstruct
- @Bean(initMethod)

### 제거 메소드

제거 메소드는 컨테이너가 종료될 때 호출돼 빈이 사용한 리소스를 반환하거나 종료 전 처리를 수행한다.

- 제거 콜백 인터페이스
- destroy-method
- @PreDestroy
- @Bean(destroyMethod)

InitializingBean 사용 예

```java
public class Client implements InitializingBean, DisposableBean {
    private String host;

    public void setHost(String host) {
        this.host = host;
        System.out.println("Client.setHost() 실행");
    }

    // 빈 객체 생성 후 초기화 과정이 필요할 때 구현
    public void afterPropertiesSet() throws Exception {
        System.out.println("Client.afterPropertiesSet() 실행");
    }

    public void send() {
        System.out.println("Client.send() to" + host);
    }

    // 빈 객체의 소멸 과정이 필요한 경우 구현
    public void destroy() throws Exception {
        System.out.println("Client.destroy() 실행");
    }
}

```

초기화, 소멸 과정이 필요한 예로는 데이터베이스 커넥션 풀이 있다. 객체의 초기화 과정에서 데이터베이스와 연결을 생성하고 컨테이너를 사용하는 동안 연결을 유지한다. 빈 객체를 소멸할 때 데이터베이스 연결을 끊어주는 방식이다.

## Bean 생명주기 콜백

> 콜백이란 어떤 이벤트가 발생했거나 특정 시점에 도달했을 때 시스템에서 호출하는 함수를 말해요.

`Bean 생명주기 콜백(Bean LifeCycle Callback)`이란 단어를 들으면, 머릿속에 딱 와닿는 느낌이 없는듯해요. 각 단어에 많은 의미가 있기 때문이라 생각해요.
생명주기 콜백을 알아보기 전 각 용어에 대해 짚고 가볼 필요가 있어요.

<br>

## 스프링 빈 (Spring bean)

`빈` 또는 `빈 객체`는 `스프링이 IoC 방식으로 관리하는 오브젝트`라는 뜻이에요. 주의할 점은 스프링을 사용하는 애플리케이션에서 만들어지는 모든 객체가 다 빈은 아니라는 사실이에요. 그중에서 스프링이 직접 생성과 제어를 담당하는 객체만을 빈이라고 불러요.

<br>

## Bean 생명주기

Bean 객체가 생성되어 소멸하기 전까지의 모든 과정을 이르는 말이에요.
스프링이 관리하는 객체인 빈은 그 `생성과 다른 빈에 대한 의존관계 주입`, `초기화(메소드 호출)`, `DI와 DL을 통한 사용`, `제거(메소드 호출)`에 이르기까지 모든 객체의 생명주기를 `스프링 컨테이너가 관리`해요.

<br>

## 빈 생명주기 콜백 메소드(Bean LifeCycle Callback Method)

스프링 컨테이너는 의존관계 주입이 완료되면 스프링 빈에게 콜백 메서드를 통해서 `초기화, 종료` 시점을 알려주는 다양한 기능을 제공해요.
`콜백`이란 어떤 이벤트가 발생했거나 특정 시점에 도달했을 때 시스템에서 호출하는 함수를 말해요.
스프링 빈의 이벤트 라이프사이클은 이래와 같아요
**[스프링 컨테이너 생성 → 스프링 빈 생성 → 의존관계 주입 → 초기화 콜백 → 사용 → 소멸 전 콜백 → 스프링 종료]**

### 초기화 콜백 메소드 & 제거 콜백 메서드

`초기화 콜백 메소드`는 빈이 생성되고, 빈의 의존관계 주입이 완료된 후 호출돼요.
`제거 콜백 메소드`는 빈이 소멸하기 직전에 호출돼요

스프링에서는 이를 어떻게 지원하는지 알아보죠.

`InitializingBean`, `DisposableBean`

- **afterPropertiesSet()** 메서드로 **초기화**를, DisposableBean은 **destroy()** 메서드로 **소멸**을 지원
- 스프링 전용 인터페이스. 해당 코드가 스프링 전용 인터페이스에 의존
- 초기화, 소멸 메서드의 이름을 변경할 수 없음
- 인터페이스를 사용하는 초기화, 종료 방법은 스프링 초창기에 나온 방법들이고, 지금은 다음의 더 나은 방법들이 있어서 거의 사용하지 않음

`@Bean(init-method)`, `@Bean(destroyMethod)`

- **@Bean(initMethod = "init", destroyMethod = "close")**처럼 지정
- 메서드 이름을 자유롭게 줄 수 있음
- 스프링 빈이 스프링 코드에 의존하지 않음
- 코드가 아니라 설정 정보를 사용하기 때문에 코드를 고칠 수 없는 외부 라이브러리에도 초기화, 종료 메서드를 적용할 수 있음
- @Bean의 destroyMethod는 기본값이 (inferred)(추론) 으로 등록되어 있기에 close, shutdown이라는 이름의 메서드를 자동으로 호출해줌. 이름 그대로 종료 메서드를 추론해서 호출

`@PostConstruct, @PreDestory` 애노테이션 지원

- 최신 스프링에서 가장 권장하는 방법
- 애노테이션 하나만 붙이면 되므로 매우 편리함
- 패키지를 잘 보면 javax.annotation.PostConstruct 임. 스프링에 종속적인 기술이 아니라JSR-250이라는 자바 표준이다. 따라서 스프링이 아닌 다른 컨테이너에서도 동작함
- 컴포넌트 스캔과 잘 어울림
- 유일한 단점은 외부 라이브러리에는 적용하지 못한다는 것. 외부 라이브러리를 초기화, 종료해야 하면 @Bean의 기능을 사용

# Bean 생명주기 콜백에 대해 설명해주세요

스프링 컨테이너는 Bean 객체의 생명주기를 관리한다. 생명주기는 해당 Bean 객체의 스코프에 따라 다르지만, 가장 많이 사용되는 Singleton 스코프의 Bean 객체가 따르는 생명주기는 다음과 같다.

```
컨테이너 생성 -> 객체 생성 -> 초기화 -> 사용 -> 객체 소멸 -> 컨테이너 소멸

```

- 컨테이너는 생성되고 난 후 설정 파일(xml, 어노테이션 등)을 읽어서 Bean 객체를 인스턴스화한다.
- 해당 Bean 객체에 의존성을 주입한다.
- 객체의 초기화 이후, 그리고 소멸 직전 생명 주기 콜백 메서드가 호출된다.

## 콜백 메서드란?

스프링 컨테이너는 Bean 객체의 생명 주기를 컨테이너의 생명 주기 내에서 관리하고 객체 생성이나 소멸 시 호출 될 수 있는 콜백 메서드를 제공하고 있다.

### 콜백 시점

스프링은 이 과정 중 두 시점에서 콜백을 준다:

1. 초기화 콜백 : 빈이 생성되고, 빈의 의존관계 주입이 완료된 후
2. 소멸 콜백 : 빈이 소멸되기 전

### 콜백 방식

스프링은 이러한 콜백을 여러 방식으로 지원한다.

1. @PostConstruct, @PreDestroy 어노테이션
2. 인터페이스 : `InitializingBean`, `DisposableBean`
3. 설정 정보에 초기화 메서드, 종료 메서드 지정

### 여러 콜백 방식의 순서

- 객체가 초기화 된 후에는 @PostConstruct, InitializingBean 인터페이스, init-method 속성 순으로 호출된다.
- 객체가 소멸할 때는 @PreDestroy, DisposableBean, destroy-method 속성 순으로 호출된다.

## @PostConstruct, @PreDestroy

빈으로 등록할 클래스의 메서드에 `@PostConstruct`, `@PreDestroy` 어노테이션을 붙여서 콜백 메서드로 지정할 수 있다.

- 최신 스프링에서 가장 권장하는 방법이다.
- JSR-250 이라는 자바 표준 기술이기 때문에 스프링에 종속적인 기술이 아니다. 따라서 스프링이 아닌 다른 컨테이너에서도 잘 작동한다.

단점:

- 소스코드가 없는 외부 라이브러리에 적용할 수 없다.

## `InitializingBean` 과 `DisposableBean` 인터페이스

- `InitializingBean` 인터페이스를 사용하여 빈을 초기화하고 `DisposableBean` 인터페이스를 이용하여 빈을 소멸하는 방식이다.
- `InitialzingBean`은 `afterPropertiesSet()` 메서드, `DisposableBean`은 `destroy()` 메서드를 이용하는데, 각각 의존관계 주입이 끝난 후와 빈이 소멸되기 직전에 호출된다.
- `afterPropertiesSet()` 메서드는 객체에 특별한 프로퍼티를 추가적으로 설정하거나 필수적으로 요구되는 사항들이 모두 충족되었는지 검사하기 위해, `destroy()` 메서드는 자원을 해제할 때 사용할 수 있다.
- 스프링 초창기에 나온 방식이라 단점들이 발견되었고, 현재는 많이 쓰이지 않는 방식이다.

단점:

1. 빈이 스프링 전용 인터페이스에 의존한다.
2. 소스코드가 없는 외부 라이브러리에는 적용할 수 없다.
3. 초기화, 소멸 메서드의 이름을 변경할 수 없다.

## 설정 정보에 초기화 메서드, 종료 메서드 지정

```
@Bean(initMethod="init", destroyMethod="close")

```

- 위의 예시처럼, 빈의 설정 정보에 초기화 메서드와 소멸 메서드의 이름을 명시할 수 있다.
- 외부 라이브러리에도 적용할 수 있다.
- 대표적인 예로 JDBC DataSource 구현체나 Hibernate SessionFactory 객체의 `close()` 메서드를 실행할 때 쓰인다.

`destroyMethod` 에는 한 가지 신기한 특징이 있다.

```
String destroyMethod() default AbstractBeanDefinition.INFER_METHOD;

```

디폴트 값이 `AbstractBeanDefinition.INFER_METHOD` 로 되어있는데, 빈에 public 이고 파라미터가 없는 `close()` 또는 `shutdown()` 메서드를 자동으로 destroy 메서드로 설정해주는 기능이다. 만약 이 기능을 원치 않는다면 `destroyMethod=""`로 명시하면 된다.

## 빈 생명주기 콜백

처음엔 빈 생명주기 콜백 이란 말을 듣고 띠용 했기 때문에 용어의 정리부터 하겠다.

1. 빈(bean)은 스프링이 구동 시 applicationContext에 저장해놓고 재사용 하거나, 필요할 때 생성할 수 있는 객체를 가리킨다.
2. 생명주기(life cycle)란 객체의 탄생(생성)과 죽음(파괴)까지의 과정을 나타낸다. 자바에서 탄생은 객체 생성(new), 죽음은 GC에 의해 메모리에서 사라지는 것이다.
3. 콜백 함수(callback function)란 어떤 함수가 실행된 후 이행되어야 하는 함수를 가리킨다. 누군가 내게 전화를 하면 전화가 끝나고 다시 전화를 거는 것에 빗대어 `callback` 함수 이다.

종합하면 다음과 같이 설명할 수 있겠다.

> 스프링에서 생성한 빈이 생성 혹은 파괴 될 때 이행되어야 하는 함수

그런데.. 이상하지 않은가? 빈 생명주기 콜백을 잠시 머릿속에서 지워보자.

생성시에 이행되어야 하는 함수 라는 말을 들으면 무엇이 생각나는가? 자바에선 이를 공식적으로 지원하지 않던가?

바로바로 `생성자Constructor` 이다. 자바에선 객체가 생성될 때 초기화 작업을 할 수 있는 생성자 문법을 지원한다. 그런데 빈 생명주기 콜백함수는 왜 별도로 존재하는가?
(생성자에서 초기화 작업을 하는 것은 좋은 패턴은 아니라고 생각하는데, 이유는 가장 아래에 서술해 놓았다.)

## 빈의 생명주기

그 것은 스프링이 DI 컨테이너 이기 때문이다. 빈은 스스로 완성되지 않는다. DI Container에 의해 의존성을 주입받아 필요로 하는 객체를 갖춘 순간부터 해당 빈은 본인의 역할을 수행할 수 있다.

field inject, constructor inject, setter inject 세가지 주입 방식 중 생성자 주입을 이용하지 않으면 빈이 생성된 시점에선 필드에 할당할 객체를 갖추고 있지 않다.(null)

예시 코드는 [https://velog.io/@syleemk/Spring-Core-빈-생명주기-콜백](https://velog.io/@syleemk/Spring-Core-%EB%B9%88-%EC%83%9D%EB%AA%85%EC%A3%BC%EA%B8%B0-%EC%BD%9C%EB%B0%B1) 에서 따왔다.

```java
public class NetworkClient {

    // 주입받아야 할 대상
    private String url;

    public NetworkClient() {
        System.out.println("생성자 호출, url" + url);
        connect();
        call("초기화 연결 메시지");
    }

    // setter 주입
    public void setUrl(String url) {
        this.url = url;
    }

    //서비스 시작시 호출
    public void connect() {
        System.out.println("connect = " + url);
    }

    public void call(String message) {
        System.out.println("call: " + url + " message = " + message);
    }

    //서비스 종료시 호출
    public void disconnect() {
        System.out.println("close: " + url);
    }
}

```

NetworkClient 객체가 connect를 하고 싶으면, url을 가지고 있어야 한다. 기존 방식대로 하면 생성자 시점엔 필요로 하는 bean 객체가 없을 수 있다. 그래서 connect는 사실 주입 받은 후에 실행해야 한다.
그런데 `주입받은 직후` 를 어떻게 알 수 있을까?

- 인터페이스 Initializing Bean, Disposable Bean
Initializing Bean은 afterProperiesSet()메서드로 초기화를 지원한다. (의존관계 주입이 끝난 후 호출되는 콜백 메서드이다.)
DisposableBean은 destroy()메서드로 소멸을 지원한다. (빈이 소멸되기 직전에 호출되는 콜백 메서드이다.)

```java
@Component
public class Bean2 implements DisposableBean, InitializingBean {
    private Bean1 bean1;

    public Bean2(Bean1 bean1) {
        this.bean1 = bean1;
        System.out.println("응애 나 아가빈");
    }

    @Override
    public void afterPropertiesSet() throws Exception {
        System.out.println("이제 활동 가능");
    }

    @Override
    public void destroy() throws Exception {
        System.out.println("으악 나 주거");
    }

}

```

하지만 스프링 라이브러리 메소드이므로 스프링 의존적이고, 내가 고칠 수 없는 외부라이브러리엔 적용할 수 없다. 테스트가 난해해지는 단점도 있다.
위의 이유로 현재는 거의 사용하지 않는다. 그렇다면?

빈에 대해서는 `@Bean(initMethod="init", destroyMethod="close")` 식으로 설정을 줄 수 있다.
컴포넌트에 대해선 @PostConstruct, @PreDestroy애노테이션을 메소드 위에 붙여 사용하면 편리하게 초기화와 종료를 실행할 수 있다.
@PostConstruct, @PreDestroy는 자바 표준 이기도 하다.(javax.annotation.)
참 좋은 세상이다.

참고 레퍼런스 : [https://velog.io/@syleemk/Spring-Core-빈-생명주기-콜백](https://velog.io/@syleemk/Spring-Core-%EB%B9%88-%EC%83%9D%EB%AA%85%EC%A3%BC%EA%B8%B0-%EC%BD%9C%EB%B0%B1)

## 질문

### Q1. 빈의 스코프?

빈 객체의 default 는 singleton. prototype 으로 스코프를 설정하면 객체를 생성 할 때마다 새로운 객체를 생성한다.

### Q2. 객체의 생성과 초기화를 분리하는 이유

생성자는 필수 정보(파라미터)를 받고, 메모리를 할당해서 객체를 생성하는 책임을 가지는 반면에 초기화는 생성된 값들을 바탕으로 외부 커넥션을 연결하는 등의 무거운 동작을 수행한다. 따라서 명확하게 나누는 것이 유지보수 관점에서 좋다.

### Q3. 콜백이라는 단어를 어떻게 정의할 수 있을까 ?

본문 참조 

### Q4. 생성자에서 무거운 초기화 작업을 하는 게 별로인 이유?

1. 객체의 생성과 초기화를 분리하자!
- 생성자는 필수정보(파라미터)를 받고, 메모리를 할당해서 객체를 생성하는 책임을 가진다.
- 반면에 초기화는 이렇게 생성된 값들을 활용하여 외부 커넥션을 연결하는 등의 무거운 동작을 수행한다.
- 따라서 생성자 안에서 무거운 초기화작업을 같이 하는 것보다는 객체를 생성하는 부분과 초기화 하는 부분을 명확하게 나누는 것이 유지보수 관점에서 좋다.
- 물론 초기화 작업이 내부 값들만 약간 변경하는 정도로 단순한 경우에는 생성자에서 한번에 다 처리하는게 나을 수 있다.

### Q5. 스프링 Bean 객체의 스코프 종류로는 어떤 것들이 있나요?

아래의 표는 Spring 에서 제공하는 디폴트 스코프 종류다.

| scope | 설명 |
|---|---|
| singleton | 빈 설정을 이용하여 Spring IoC 컨테이너당 하나의 인스턴스를 만든다 |
| prototype | 빈 설정을 이용하여 여러 인스턴스를 만든다 |
| request | 빈 설정을 이용하여 HTTP 요청당 하나의 인스턴스를 만든다. 즉, 각각의 HTTP 요청은 하나의 빈 객체를 가지고 있다 |
| session | HTTP 세션 당 하나의 인스턴스를 만든다. |
| application | ServletConext 당 하나의 인스턴스를 만든다. |
| websocket | WebSocket 당 하나의 인스턴스를 만든다. |

디폴트 스코프 말고 내가 직접 스코프를 만들어서 사용할 수도 있다. 이를 Custom Scope라고 한다. Spring 3.0부터 [스레드 범위 스코프도 제공](https://docs.spring.io/spring-framework/docs/current-SNAPSHOT/reference/html/core.html#beans-factory-scopes-custom-using)해주는데, 디폴트 스코프가 아니므로 커스텀 스코프로 등록해서 사용해야 한다.
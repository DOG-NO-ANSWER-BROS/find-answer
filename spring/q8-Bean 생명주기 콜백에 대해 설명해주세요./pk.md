# Bean 생명주기 콜백에 대해 설명해주세요

스프링 컨테이너는 Bean 객체의 생명주기를 관리한다. 생명주기는 해당 Bean 객체의 스코프에 따라 다르지만, 가장 많이 사용되는 Singleton 스코프의 Bean 객체가 따르는 생명주기는 다음과 같다.
```text
컨테이너 생성 -> 객체 생성 -> 초기화 -> 사용 -> 객체 소멸 -> 컨테이너 소멸
```
* 컨테이너는 생성되고 난 후 설정 파일(xml, 어노테이션 등)을 읽어서 Bean 객체를 인스턴스화한다.
* 해당 Bean 객체에 의존성을 주입한다.
* 객체의 초기화 이후, 그리고 소멸 직전 생명 주기 콜백 메서드가 호출된다.

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
* 객체가 초기화 된 후에는 @PostConstruct, InitializingBean 인터페이스, init-method 속성 순으로 호출된다.
* 객체가 소멸할 때는 @PreDestroy, DisposableBean, destroy-method 속성 순으로 호출된다.

## @PostConstruct, @PreDestroy
빈으로 등록할 클래스의 메서드에 `@PostConstruct`, `@PreDestroy` 어노테이션을 붙여서 콜백 메서드로 지정할 수 있다.
* 최신 스프링에서 가장 권장하는 방법이다.
* JSR-250 이라는 자바 표준 기술이기 때문에 스프링에 종속적인 기술이 아니다. 따라서 스프링이 아닌 다른 컨테이너에서도 잘 작동한다.
  
단점:
* 소스코드가 없는 외부 라이브러리에 적용할 수 없다.

## `InitializingBean` 과 `DisposableBean` 인터페이스
* `InitializingBean` 인터페이스를 사용하여 빈을 초기화하고 `DisposableBean` 인터페이스를 이용하여 빈을 소멸하는 방식이다.
* `InitialzingBean`은 `afterPropertiesSet()` 메서드, `DisposableBean`은 `destroy()` 메서드를 이용하는데, 각각 의존관계 주입이 끝난 후와 빈이 소멸되기 직전에 호출된다.
* `afterPropertiesSet()` 메서드는 객체에 특별한 프로퍼티를 추가적으로 설정하거나 필수적으로 요구되는 사항들이 모두 충족되었는지 검사하기 위해, `destroy()` 메서드는 자원을 해제할 때 사용할 수 있다.
* 스프링 초창기에 나온 방식이라 단점들이 발견되었고, 현재는 많이 쓰이지 않는 방식이다.

단점:
1. 빈이 스프링 전용 인터페이스에 의존한다.
2. 소스코드가 없는 외부 라이브러리에는 적용할 수 없다.
3. 초기화, 소멸 메서드의 이름을 변경할 수 없다.

## 설정 정보에 초기화 메서드, 종료 메서드 지정
```java
@Bean(initMethod="init", destroyMethod="close")
```
* 위의 예시처럼, 빈의 설정 정보에 초기화 메서드와 소멸 메서드의 이름을 명시할 수 있다.
* 외부 라이브러리에도 적용할 수 있다.
* 대표적인 예로 JDBC DataSource 구현체나 Hibernate SessionFactory 객체의 `close()` 메서드를 실행할 때 쓰인다.

`destroyMethod` 에는 한 가지 신기한 특징이 있다.
```java
String destroyMethod() default AbstractBeanDefinition.INFER_METHOD;
```
디폴트 값이 `AbstractBeanDefinition.INFER_METHOD` 로 되어있는데, 빈에 public 이고 파라미터가 없는 `close()` 또는 `shutdown()` 메서드를 자동으로 destroy 메서드로 설정해주는 기능이다. 만약 이 기능을 원치 않는다면 `destroyMethod=""`로 명시하면 된다.

## 질문

### 스프링 Bean 객체의 스코프 종류로는 어떤 것들이 있나요?
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

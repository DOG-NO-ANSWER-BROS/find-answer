# @SpringBootApplication 애노테이션의 @SpringBootConfiguration, @EnableAutoConfiguration는 무엇이고, 어떤 역할을 하나요?

스프링 부트를 의존성에 추가하면  (implementation 'org.springframework.boot:spring-boot-starter-web') 

아래와 같이 @SpringBootApplication 애노테이션이 붙어있는 메인 클래스를 확인할 수 있어요.

```java
@SpringBootApplication
public class SpringBootApplication {

    public static void main(String[] args) {
        SpringApplication.run(SpringBootApplication.class, args);
    }
}
```

메인 클래스 부분에서 코드를 수정하는 부분은 거의없지만, 서비스의 시작이라는 관점에서 굉장히 중요한 부분이라 생각해요. 

<br>

## **@SpringBootApplication**

우선 @SpringBootApplication을 들어가서 확인해보기로 해요

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(excludeFilters = { @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
		@Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
public @interface SpringBootApplication {
```

중점적으로 봐야할 부분만 남겨보죠.

```java
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(excludeFilters = { @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
		@Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
public @interface SpringBootApplication {
```

클래스 내의 설명으론, 하나 이상의 @Bean 메서드를 선언하고, auto-configuration, component scanning을 일으키는 구성 클래스라고 해요. 

@SpringBootApplication 를 쓰지 않아도 @Configuration, @EnableAutoConfiguration 및 @ComponentScan만 선언하면 동일한 역할을 기대할 수 있다고 해요. 

> **즉. @Configuration, @EnableAutoConfiguration 및 @ComponentScan 3가지의 역할을 수행하는 애노테이션이에요.**

## **@SpringBootConfiguration**

스프링에서는 @Configuration으로 환경설정 빈을 표현해요.

앞서 분명 @Configuration으로 선언해도 동일한 역할을 수행한다고 했는데, 왜 @SpringBootConfiguration이라 특별한 이름을 지어줬을까요?

### **@Configuration vs @SpringBootConfiguration**

```java
@SpringBootApplication
-------> @SpringBootConfiguration
       -------> @Configuration
```

Spring Boot Document에 따르면, 계층구조가 위와 같이 되어있어요. 

SpringBoot만의 Configuration이라는 의미를 주기 위함이라 생각해요.

### **유일한 차이점**

@SpringBootConfiguration을 사용하면 구성을 자동으로 찾을 수 있다고 해요. 

그렇기 때문에  **@SpringBootConfiguration는 한 서비스 내에 2개 이상을 사용하지 못해요.** 

**(즉,** @SpringBootApplication를 사용하면, 다른곳에선 @SpringBootConfiguration를 사용하지 못함)

구성을 자동으로 찾는 부분은 @SpringBootTest에서 확인할 수 있어요. 그렇기 때문에 테스트시 유용하다고 해요 

<br>

## **@EnableAutoConfiguration**

@ComponentScan으로 빈이 등록된 이후, 추가적인 Bean들을 읽어 등록하는 애노테이션이에요

spring.factories 내부에 여러 Configuration들을 조건에 따라 Bean을 등록해요.

```java
#/spring-boot-autoconfigure-2.4.3.jar!/META-INF/spring.factories

# Initializers
org.springframework.context.ApplicationContextInitializer=\
...

# Application Listeners
org.springframework.context.ApplicationListener=\
...

# Auto Configuration Import Listeners
org.springframework.boot.autoconfigure.AutoConfigurationImportListener=\
...

# Auto Configuration Import Filters
org.springframework.boot.autoconfigure.AutoConfigurationImportFilter=\
...

# Auto Configure
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
org.springframework.boot.autoconfigure.admin.SpringApplicationAdminJmxAutoConfiguration,\
...

# Failure analyzers
org.springframework.boot.diagnostics.FailureAnalyzer=\
org.springframework.boot.autoconfigure.data.redis.RedisUrlSyntaxFailureAnalyzer,\
...

# Template availability providers
org.springframework.boot.autoconfigure.template.TemplateAvailabilityProvider=\
org.springframework.boot.autoconfigure.freemarker.FreeMarkerTemplateAvailabilityProvider,\
...
```

(이 파일 이외에도 많아요)

@EnableAutoConfiguration도 결국 Configuration이에요. 즉, Bean을 등록하는 자바 설정 파일에요.

### **자동만 되면 안좋지 않나..?**

아니요, 개발자가 직접 코드를 작성해 @EnableAutoConfiguration에 관리되게 해줄 수 있어요.

즉, spring.factories를 직접 작성하여 자동으로 설정파일을 넣어줄 수 있어요.

```java
# resources/META-INF/spring.factories

org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
com.livenow.config.GumpCustomConfiguration
```

<br>

## **Refer**

[https://docs.spring.io/spring-boot/docs/2.0.x/reference/html/using-boot-using-springbootapplication-annotation.html](https://docs.spring.io/spring-boot/docs/2.0.x/reference/html/using-boot-using-springbootapplication-annotation.html)

[https://www.baeldung.com/springbootconfiguration-annotation](https://www.baeldung.com/springbootconfiguration-annotation)

[https://stackoverflow.com/questions/56910260/what-is-the-difference-between-springbootconfiguration-vs-configuration](https://stackoverflow.com/questions/56910260/what-is-the-difference-between-springbootconfiguration-vs-configuration)
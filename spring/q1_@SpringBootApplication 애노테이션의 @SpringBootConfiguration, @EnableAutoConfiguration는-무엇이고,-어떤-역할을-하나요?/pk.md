# @SpringBootApplication의 INSIDE

스프링부트를 사용하면 기존에 Spring에서 진행해야 했던 여러 설정 처리를 자동으로 해결해준다.
메인 애플리케이션 클래스에 @SpringBootApplication 어노테이션을 붙여주면 된다.

그렇다면 @SpringBootApplication에는 무엇이 들어있을까?

# @SpringBootConfiguration
먼저, 스프링에서는 @Configuration 어노테이션으로 환경설정 빈 클래스를 표현한다.

`@SpringBootConfiguration`은 `@Configuration` 대신 환경설정 빈 클래스를 표현하고, 해당 설정이 SpringBoot와 관련이 
있음을 명시하기 위해 사용한다.

## Why?
단위 테스트, 또는 통합 테스트를 할 때 `@SpringBootApplication` 설정을 찾아야하는데, `@SpringBootConfiguration` 
어노테이션을 이용하여 자동으로 찾게끔 하는 것.

## Maybe...
그러면 다음과 같은 코드가 정상적으로 실행이 될까?
```java
@EnableAutoConfiguration
@Configuration
@ComponentScan
public class SpringSomethingApplication {
    
    public static void main(String[] args) {
        SpringApplication.run(SpringSomethingApplication.class, args);
    }
}
```
**정상적으로 실행된다.** 다만, 테스트를 작성할 때 문제가 되는 것.

예를 들어 테스트를 작성할 때 `@SpringBootTest` 어노테이션을 활용할텐데, 해당 어노테이션도 `@SpringBootConfiguration`을 
찾아서 필요한 설정을 가지고 온다. 그래서 `@Configuration` 어노테이션을 쓰면 에러가 발생할 것.

## Maybe... 2
그렇다면 `@Configuration` 어노테이션을 앞으로 `@SpringBootConfiguration`으로 대체해도 괜찮지 않을까?

**NO** 안된다. `@SpringBootConfiguration`은 애플리케이션에서 하나만 쓰이길 권장한다. (라고 말하고 두 개 쓰면 에러 뜬다.)

자동으로 연결해주는 설정이 두 개가 되면 안됨. 즉, `@SpringBootApplication` 어노테이션이 자신의 프로젝트에 있다면, 
`@SpringBootConfiguration` 어노테이션은 고려하지 않아도 된다.

# @EnableAutoConfiguration
External Libraries 안에 있는 `spring-boot-autoconfigure` jar을 확인해보자.

직접 수정할 수도 있는데, 일단은 기본적으로 주어지는 설정을 주입시켜주는 어노테이션이다.
즉, 프로젝트에 쓰일 외부 라이브러리나 설정이 필요한 부분에 대한 객체 주입을 해결해준다. (더 구체적으로 볼 필요 있음)

포인트는 내가 필요한 것을 알아서 판단해서 스프링 컨테이너에 빈 등록을 해준다는 것이다.
그리고 앞서 말했듯, 개발자가 직접 원하는 설정으로 변경할 수 있다.

## spring.factories
스프링 부트의 메타데이터가 저장되어 있는 파일. 이 파일을 참조하여 여러가지 빈을 생성한다.

# @ComponentScan
해당 어노테이션이 붙은 클래스가 있는 패키지부터 하위 패키지까지 들어가며 스프링 컨테이너에 객체를 등록한다.
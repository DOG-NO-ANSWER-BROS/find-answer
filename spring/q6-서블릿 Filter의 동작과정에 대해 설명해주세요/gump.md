# 정리

스프링 mvc의 전반적인 요청과정은 이전에 정리했어요 

[[Spring] 쓰레드 관점에서 본 Spring MVC HTTP 요청과정](https://livenow14.tistory.com/59)

<br>

## **Servlet Filter**

요청을 받은 `WAS`가 `Servlet Container`에게 `Request, Response`를 던져주면, 이를 먼저 `Filter`에서 받아. `Request`에 대한 필터링를 진행하고, 서블릿에게 `ServletRequest, ServletResponse`를 건네요.

요청에 대한 작업이 끝난 후 `Response`에 대한 필터링를 진행해요.

쉽게말해, 요청과 응답에 대한 필터링을 진행해요.

<br>

## **동작과정**

```java
import javax.servlet.*;
import java.io.IOException;

public class GumpFilter implements Filter {
    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
        // FilterConfig를 통한 filter 설정
        // 서블릿 컨테이너가 필터 인스턴스 생성한 후 초기화 하기 위해서 사용 전 호출하는 메소드
    }

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
        // 필터에서 구현해야 하는 로직을 작성하는 메소드

				// 처리 전
				... 
				chain.doFilter(request, response);
				...
				// 처리 후
    }

    @Override
    public void destroy() {
        // 필터 인스턴스를 종료시키기 전에 호출하는 메소드
    }
}
```

filter는 총 3가지의 동작과정을 거쳐요. **`1.**초기화, 2. 필터링, 3. 소멸`

1. **init(서블릿 컨터네이너가 기본적으로 구현)**

    서블릿 컨테이너는 필터를 인스턴스화 한 후 정확히 한 번 init 메소드를 호출해요.

    성공적으로 초기화가 완료돼야 필터링을 수행할 수 있어요.

    FilterConfig를 통한 커스텀한 filter 설정이 가능해요.

2. **doFilter(개발자가 직접 구현)**

    `request, response`에 대한 필터링 로직을 작성하는 곳이에요.

    `resquest, response`가 체인을 통과할 때 마다 서블릿 컨테이너에서 호출돼요. 

    여기서 체인은, 연결되어 있음을 의미해요.

    즉, 파라미터로 들어올 시 이전 필터의 정보를 받아오며, 메소드 안에서 사용시 다음의 필터 또는 리소스를 호출하는 역할을 해요

    사용하지 않을 시, 요청을 servlet으로 보내지 않고 바로 응답하게해요. 이를 응용한다면 조건문을 통해 특정 조건에 부합하지 않을 때, 예외를 뱉게하거나 즉시 응답을 반환하게 할 수 있어요.

3. **destroy(서블릿 컨테이너가 기본적으로 구현)**

    `doFilter` 메서드 내의 모든 스레드가 종료되거나 설정한 제한 시간이 지난 후에 서블릿 컨테이너에 의해 한번 호출돼요.

    웹 컨테이너가 destroy를 호출 한 후에는 인스턴스에서 doFilter를 다시 호출하지 않아요.

    인스턴스를 소멸하기 전 호출하는 메소드에요.

<br>

## 사용방법

### **`@Component 등록`**

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Component;

import javax.servlet.*;
import javax.servlet.http.HttpServletRequest;
import java.io.IOException;

@Component
public class MethodFilter implements Filter {
    private static final Logger logger = LoggerFactory.getLogger(MethodFilter.class);

    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
        logger.info("Start Method checking");
        Filter.super.init(filterConfig);
    }

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {

        HttpServletRequest req = (HttpServletRequest) request;
        logger.info("Request Method: {}", req.getMethod());

        chain.doFilter(request, response);

        logger.info("Return Method: {}", req.getMethod());
    }

    @Override
    public void destroy() {
        logger.info("End Method checking");
        Filter.super.destroy();
    }
}
```

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Component;

import javax.servlet.*;
import javax.servlet.http.HttpServletRequest;
import java.io.IOException;

@Component
public class UriFilter implements Filter {
    private static final Logger logger = LoggerFactory.getLogger(UriFilter.class);

    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
        logger.info("Start URI checking");
        Filter.super.init(filterConfig);
    }

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {

        HttpServletRequest req = (HttpServletRequest) request;
        logger.info("Request URI: {}", req.getRequestURL());

        chain.doFilter(request, response);

        logger.info("Return URI: {}", req.getRequestURL());
    }

    @Override
    public void destroy() {
        logger.info("End URI checking");
        Filter.super.destroy();
    }
}
```

`@Component`로 두개의 다른 역할을 하는 Filter를 빈으로 등록했어요. 

**출력로그 ( 애플리케이션 컨택스트 초기화 시 )**

```java
2021-06-18 00:28:48.850  INFO 19598 --- [           main] wooteco.subway.auth.filter.MethodFilter  : Start Method checking
2021-06-18 00:28:48.851  INFO 19598 --- [           main] wooteco.subway.auth.filter.UriFilter     : Start URI checking
```

**출력로그 ( HTTP 요청 시 )**

```java
2021-06-18 00:28:52.637  INFO 19598 --- [nio-8080-exec-1] wooteco.subway.auth.filter.MethodFilter  : Request Method: POST
2021-06-18 00:28:52.637  INFO 19598 --- [nio-8080-exec-1] wooteco.subway.auth.filter.UriFilter     : Request URI: http://localhost:8080/members
2021-06-18 00:28:52.637  INFO 19598 --- [nio-8080-exec-1] wooteco.subway.auth.filter.UriFilter     : Return URI: http://localhost:8080/members
2021-06-18 00:28:52.637  INFO 19598 --- [nio-8080-exec-1] wooteco.subway.auth.filter.MethodFilter  : Return Method: POST
```

**출력로그 ( 서비스 종료 시 )**

```java
2021-06-18 00:32:34.114  INFO 19598 --- [extShutdownHook] wooteco.subway.auth.filter.MethodFilter  : End Method checking
2021-06-18 00:32:34.114  INFO 19598 --- [extShutdownHook] wooteco.subway.auth.filter.UriFilter     : End URI checking
```

> 어떤 필터가 먼저 초기화 될까?

기본 설정 시, 스프링에서 정의하는 우선순위에 따라 동작을 해요(`클래스 이름의 알파벳 순서`). 

즉, `MethodFilter`에서 `doFilter`를 제거하면, 다음 과정를 거치지 않고 바로 반환을 하게되고, `UriFilter`는 동작하지 않게돼요. 

예를 들기 위해 MethodFilter클래스를 조금 수정했어요.

```java

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Component;

import javax.servlet.*;
import javax.servlet.http.HttpServletRequest;
import java.io.IOException;

@Component
public class MethodFilter implements Filter {
    private static final Logger logger = LoggerFactory.getLogger(MethodFilter.class);

    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
        logger.info("Start Method checking");
        Filter.super.init(filterConfig);
    }

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {

        HttpServletRequest req = (HttpServletRequest) request;
        logger.info("Request Method: {}", req.getMethod());
    }

    @Override
    public void destroy() {
        logger.info("End Method checking");
        Filter.super.destroy();
    }
}
```

**출력로그**

```java
2021-06-18 00:28:48.850  INFO 19598 --- [           main] wooteco.subway.auth.filter.MethodFilter  : Start Method checking
2021-06-18 00:28:48.851  INFO 19598 --- [           main] wooteco.subway.auth.filter.UriFilter     : Start URI checking
...
2021-06-18 00:28:52.637  INFO 19598 --- [nio-8080-exec-1] wooteco.subway.auth.filter.MethodFilter  : Request Method: POST

```

요구사항에 HTTP Uri 정보가 꼭 필요하다면, 우선순위가 필요하겠죠? 그때 `@Order` 애노테이션을 사용해요

### @Order 적용

```java
@Component
@Order(1)
public class UriFilter implements Filter {
첫 코드와 동일
...
}
```

```java
@Component
@Order(2)
public class MethodFilter implements Filter {
첫 코드와 동일
...
}
```

**출력로그( '...' 아래 출력이 달라짐 )**

```java
2021-06-18 00:46:17.393  INFO 19761 --- [           main] wooteco.subway.auth.filter.MethodFilter  : Start Method checking
2021-06-18 00:46:17.393  INFO 19761 --- [           main] wooteco.subway.auth.filter.UriFilter     : Start URI checking
...
2021-06-18 00:46:20.645  INFO 19761 --- [nio-8080-exec-2] wooteco.subway.auth.filter.UriFilter     : Request URI: http://localhost:8080/members
2021-06-18 00:46:20.645  INFO 19761 --- [nio-8080-exec-2] wooteco.subway.auth.filter.MethodFilter  : Request Method: POST
2021-06-18 00:46:20.645  INFO 19761 --- [nio-8080-exec-2] wooteco.subway.auth.filter.MethodFilter  : Return Method: POST
2021-06-18 00:46:20.645  INFO 19761 --- [nio-8080-exec-2] wooteco.subway.auth.filter.UriFilter     : Return URI: http://localhost:8080/members
```

<br>

### **`@Bean 등록`**

`@Component`로 등록시, 적용 범위는 전체 URL에요. 특정 URL에만 적용하기 위해 `@Bean`으로 등록해야해요. 

```java
애노테이션 제거
public class UriFilter implements Filter {
위와 동일
...
}
```

```java
애노테이션 제거
public class MethodFilter implements Filter {
위와 동일
...
}
```

```java
import org.springframework.boot.web.servlet.FilterRegistrationBean;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class FilterConfiguration implements WebMvcConfigurer {

    @Bean
    public FilterRegistrationBean<UriFilter> uriFilterRegistrationBean() {
        FilterRegistrationBean<UriFilter> registrationBean = new FilterRegistrationBean<>();
        registrationBean.setFilter(new UriFilter());
        registrationBean.addUrlPatterns("/members");

        return registrationBean;
    }

    @Bean
    public FilterRegistrationBean<MethodFilter> methodFilterRegistrationBean() {
        FilterRegistrationBean<MethodFilter> registrationBean = new FilterRegistrationBean<>();
        registrationBean.setFilter(new MethodFilter());
        registrationBean.addUrlPatterns("/stations");

        return registrationBean;
    }
}
```

순서는 코드상 `선언된 순서`예요(현재 코드에선 uriFilter먼저).

<br>

## 언제 사용하는게 좋을까?

Servlet Filter는 

1. 인증
2. 로깅
3. 이미지 변환
4. 데이터 압축 
5. 인코딩 / 디코딩
6. 암호화
7. 캐싱 

등 많은 곳에 사용될 수 있어요.
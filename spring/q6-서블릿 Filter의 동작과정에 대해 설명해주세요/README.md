## Servlet Filter 란?

공통적인 기능들을 서블릿이 호출되기 전에 처리하거나 호출된 후에 처리하고 싶으면 서블릿 필터를 통해 구현할 수 있다. 필터를 사용하면 클라이언트의 요청을 가로채서 서버 컴포넌트(Servlet, Jsp)의 공통적인 기능을 수행할 수 있다. 스프링 컨텍스트 외부에 존재하여 스프링과 무관한 자원에 대해 동작한다.

```java
public interface Filter {

    public default void init(FilterConfig filterConfig) throws ServletException {}

    public void doFilter(ServletRequest request, ServletResponse response,
            FilterChain chain) throws IOException, ServletException;

    public default void destroy() {}
}

```

## Filter 로 할 수 있는 것

- 인증
- 로깅 및 감사
- 이미지 변환
- 데이터 압축
- 암호화 필터
- 토크나이징 필터
- XML 컨텐츠를 변형하는 XSLT 필터
- Mime-Type 체인 필터
- URL 및 기타정보들을 캐시하는 필터

## Filter 적용 순서

1. 필터 인터페이스 구현 자바 클래스 생성
2. `/WEB-INF/web.xml` 에 filter 엘리먼트를 사용하여 필터 클래스를 등록

## Filter 의 라이프 사이클

생성, 초기화, 필터, 종료 4단계로 이뤄진다.

- init()

필터 객체가 생성되고 준비 작업을 위해 딱 한번 호출. 매개변수는 FilterConfig의 인스턴스다. init() 메소드를 호출하면 필터 인스터는스는 바로 요청을 처리 할 수 있는 상태가 된다.

- doFilter()

필터에 매핑된 URL에 요청이 들어올때마다 doFilter() 가 호출된다. 필터가 실질적으로 할 로직을 작성하는 메소드이다.

- destroy()

모든 요청에 대한 처리가 끝나면 destroy() 메서드가 호출되며 필터는 비활성 상태가 된다.

FirstFilter - SecondFilter - Controller - SecondFilter - FirstFilter 순으로 동작한다.

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

Filter를 적용하기 위해 스프링 빈으로 등록해야해요. 이 때  `@Component 등록` `@Bean 등록` 두가지 방법이 있어요.

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

<br>

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

<br>

**@Order 적용**

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

# 서블릿 필터의 동작 과정 설명

## 서블릿 필터란?

요청 또는 응답에 관해 필터링 처리를 하는 오브젝트.
doFilter 메서드를 실행하여 필터링을 하며, `FilterConfig` 오브젝트에 접근 권한이 있어서,
이로부터 필터가 어떠한 `ServletContext`를 사용할 수 있는지에 참고할 수 있고,
필터를 할 때 필요한 파라미터와 리소스를 얻을 수 있다.

## 왜 사용할까?

요청을 DispatcherServlet에 건내주기 전에, 그리고 응답을 클라이언트에게 반환하기 전에 필요한 일을 담당한다.
예를 들어:

1. 인증
2. 로깅
3. 이미지 컴버팅
4. 데이터 압축, 인코딩 / 디코딩
5. XML 컨텐트를 XSL/T로 변환
6. 캐싱 등등...

즉, 서블릿이 호출되기 전에 전처리를 하거나, 호출 된 후 후처리를 담당한다.

## 어떻게 사용할까?

```java
package javax.servlet;

public interface Filter {

  public default void init(FilterConfig filterConfig) throws ServletException {}

  public void doFilter(ServletRequest request, ServletResponse response,
          FilterChain chain) throws IOException, ServletException;

  public default void destroy() {}
}

```

`doFilter` 메서드가 주목해야 할 부분이다.

1. 파라미터로 `ServletRequest`, `ServletResponse`, 그리고 `FilterChain`을 받는다.
- `ServletRequest`와 `ServletResponse`는 `HttpServletRequest`와 `HttpServletResponse`의 부모 인터페이스다.
- `FilterChain`에는 컨테이너로부터 받은 다른 Filter들의 정보를 담고 있다.
1. 요청의 헤더를 확인한 뒤 요청의 헤더, 정보 또는 요청 자체를 완전히 바꿀 수 있다.
2. 응답의 헤더 또는 데이터를 수정할 수도 있다.
3. `FilterChain`에서 다음 필터의 `doFilter()` 메서드를 호출한다. 요청을 차단할 수도 있는데, 이 경우에는 해당 필터가 응답을 채워야한다.
4. 다음 필터의 `doFilter()`를 호출하고 나면 그 뒤부터는 후처리 작업을 진행할 수 있다.
5. 문제가 발생하면 예외를 던진다.

즉, 다음과 같은 구조:

```java
public class MyFilter implements Filter {
    // ... init, destory 메서드 오버라이딩

    @Override
    public void doFilter(ServletRequest request,
                         ServletResponse response,
                         FilterChain chain) throws IOException, ServletException {
        // 전처리
        chain.doFilter(request, response);
        // 후처리
    }
}

```

### 필터는 추가하거나 빼기 간편하다.

web.xml 파일에서 추가하고 제거하면 된다.

```java
<web-app>

<filter>
<filter-name>filter1</filter-name>
<filter-class>MyFilter</filter-class>
</filter>

<filter-mapping>
<filter-name>filter1</filter-name>
<url-pattern>/something</url-pattern>
</filter-mapping>

</web-app>

```

### Spring Boot에서는?

### 1. `@Component` 사용

```java
@Component // (1)
@Order(1) // (2)
public class MyFilter implements Filter {

    @Override
    public void doFilter(ServletRequest request,
                         ServletResponse response,
                         FilterChain chain) throws IOException, ServletException {
        // 전처리
        chain.doFilter(request, response);
        // 후처리
    }
}

```

1. `@Component` 어노테이션으로 빈으로 등록시킨다. 이 방식은 모든 url에 filter를 적용하는 방식.
2. 가장 먼저 전처리가 실행되는 필터. 즉, 1~9 순으로 있다면 `@Order(9)`인 필터의 전처리가 가장 나중에 실행된다. 역으로 후처리는 9, 8, 7 ... 1 순.

### 2. `FilterRegistrationBean` 사용

```java
import org.springframework.boot.web.servlet.FilterRegistrationBean;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class MyConfiguration {

    @Bean
    public FilterRegistrationBean<MyFilter> myFilterRegistration() {
      FilterRegistrationBean<MyFilter> registrationBean = new FilterRegistrationBean<>();
      registrationBean.setFilter(myFilter());
      registrationBean.addUrlPatterns("/something");

      return registrationBean;
    }

    @Bean
    public MyFilter myFilter() {
        return new MyFilter();
    }
}

```

- 해당 방식을 사용하면 url을 지정해서 필터를 적용할 수 있다.

## 인터셉터와 다른 점

필터는 서블릿 api에 속한 기능이고 인터셉터는 Spring MVC에서 사용하는 기능이다.
즉, 필터는 서블릿에 요청이 전달되기 전에 사용되고, 인터셉터(`Handler Interceptor`)는
`Handler Mapping`이 Handler에게 요청 처리를 위임하기 전에 실행된다.

따라서 클라이언트가 보내는 모든 요청/응답에 대한 처리를 해야한다면 Filter를 사용하는 것이 적합하고,
HandlerMapping 별로 다른 처리를 해야한다면 Interceptor를 사용하는게 맞다.

라고 말은 했지만 아직 확실하게 와닿지는 않아서 다음 편에 HandlerInterceptor 공식 문서를 참조하여 Interceptor 설명을 추가할 예정.

<br> 

## 필터와 인터셉터의 차이

- 스프링 Interceptor은 다들 알고 있을 것이다. Spring MVC에서 내부적으로 요청 처리 앞과 뒤에 AOP를 적용하고 싶은 일이 있으면 인터셉터를 활용한다. 즉, 디스패쳐 서블릿 동작 내부의 일이다.
- 필터는 그럼 무엇인가? 흔히들 다음과 같은 짤을 많이 인용한다.

스프링 필터라고 구글에 검색하면 가장 먼저 나오는 블로그이다. 구글에서 자주 뵙는 갓대희님...

    ![https://user-images.githubusercontent.com/51393021/121841974-6c9cef00-cd1a-11eb-805c-2fd3bc3e2ef2.png](https://user-images.githubusercontent.com/51393021/121841974-6c9cef00-cd1a-11eb-805c-2fd3bc3e2ef2.png)

- 중요한 부분은 필터가 디스패쳐 서블릿을 들어가기 전에 동작한다는 것이다. 여기서 생기는 의문!
- 요청에 대해서 공통적으로 적용할 만한 작업이 있을 때 인터셉터, 필터를 통해 처리한다는 내용은 알았다. 그러나 그 두개의 차이는 무엇인가? 서블릿에 들어오기 전과 들어온 후를 구분하는 의미는 무엇인가?
- 우리는 웹 개발자고, 스프링을 통해 웹 어플리케이션을 만들 떄 서블릿에 들어온 이후에 동작에 대해 코딩한다.(Cotroller 코드는 디스패쳐 서블릿 이후의 동작이다) 그래서 인터셉터와 필터의 차이가 애매하게 느껴질 수 있다(내가 그랬다)
- 이 두개의 차이를 알기 위해선 웹 컨테이너와 웹 어플리케이션의 관계에 대해 이해해야 한다.
- 웹 어플리케이션의 정체는 WAS에 실을 WAR(혹은 JAR) 프로그램이다. 구현은 WAS의 스펙에 맞추어 웹 요청을 추상화 하여 처리할 수 있게 만든 Servlet을 구현해야 한다. 스프링의 MVC는 이 Serlvet의 내부 구현이다.
- 한편 WAS의 구현 중 에는 웹 요청을 받은 직후, 처리하고 클라이언트에게 요청을 반환하기 전 처리할 코드를 구현 할 수 있는 filter라는 기능이 존재한다. 스프링에서 필터 구현은, 이 필터에 들어갈 기능을 구현하는 것이다.
- 부트에서의 필터구현은 워낙 많은 참고자료가 있기에 블로그 중 하나를 첨부한다. [https://luvstudy.tistory.com/79](https://luvstudy.tistory.com/79)
- 인터셉터에서 구현할 만한 것은 request와 resposne객체에 영향을 줄 만한 것, Argument Resolver라던가, CorsFilter라던가, viewResolver라던가.. 등등이 있을 것 이다.
- 내 스프링-MVC 에서 제공하고자 하는 비즈니스 로직과 관련된 것은 인터셉터에서 해줄 것 같고, 인증/인가 처럼 서블릿을 탈 필요가 없거나 요청에 대한 로깅처럼 WAS단에서 처리할만한 작업은 필터에서 해줄 것 같다.
- 당장 떠오르는 건 없지만, 서블릿에 들어가기 전 공통 처리가 필요한 부분, 혹은 요청이 나가기 직전 처리해야할 작업이 생길 경우 필터를 고려해보자. 필터는 서블릿 별로, 그리고 Url패턴 별로 지정할 수 있는데, 스프링을 활용할 경우엔 디스패처 서블릿 한가지만 존재하므로 서블릿 맵핑 보다는 url 맵핑을 활용 하게 될 것 같다.

## 필터의 동작

- 필터를 직접 구현하려면 tomcat의 WEB-INF/web.xml 에 필터 이름과 필터 구현에 사용할 클래스 파일 위치(이 클래스는 javax.servlet.Filter 를 구현한 클래스여야 한다), 그리고 이 필터가 적용될 서블릿 혹은 URL 패턴을 명기해주면 된다.
- 참고로 서블릿 3.0부터 `@WebFilter` 라는 어노테이션을 통해 필터를 등록할 수 있다 ㅎ_ㅎ

    ```java
      <?xml version="1.0" encoding="utf-8"?>

      <web-app ...>

        <filter>
          <filter-name>FilterName</filter-name>
          <filter-class>javacan.filter.FileClass</filter-class>
          <init-param>
            <param-name>paramName</param-name>
            <param-value>value</param-value>
          </init-pram>
        </filter>

        <filter-mapping>
          <filter-name>FilterName</filter-name>
          <url-pattern>*.jsp</url.patter>
        </filter-mapping>

        ...

      </web-app>

    ```

    ```java
      public class WedgeFilter implements Filter {
        @Override
        public void init(FilterConfig filterConfig) throws ServletException {
            // 필터를 초기화할 때 호출된다. default 메소드이므로 굳이 호출하지 말자.
        }

        @Override
        public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
            //필터에서 해야할 액션을 정의한다. request는 요청 객체, response는 응답객체, chain은 다음에 수행할 필터 정보가 담겨있다.
            //할거 다 하고 나면 다음 필터를 실행시켜주자.
            //chain.doFilter(request, response);
        }

        @Override
        public void destroy() {
            //필터가 박살날 때 호출된다. 마찬가지로 default 메소드 이므로 굳이 호출하지 말자22
            Filter.super.destroy();
        }
       }

    ```

- 만약 필터가 여러개 적용되는 상황인 경우, web.xml에 정의되어있는 순서대로 동작한다.(필터체인의 정체)
- 필터 체인의 동작은 다음과 같이 스택구조로 일어난다.
- 

    ![https://user-images.githubusercontent.com/51393021/121978032-4763bb80-cdc2-11eb-9216-5d61feb00191.jpg](https://user-images.githubusercontent.com/51393021/121978032-4763bb80-cdc2-11eb-9216-5d61feb00191.jpg)

- 3과 7이라는 숫자는 신경쓰지 말자. 두 개의 필터체인이다.
- 부트의 필터 구현은 필터 등록을 **알아서** 해주는 것이다. 부트 최고~ 예컨데 필터를 빈으로 등록하면 @WebFilter를 통해 등록해 줄 것이고, 필터 순서를 등록하면 filter의 순서를 조정할 것이다.
- 그리고 WAS a.k.a servlet container(우리는 주로 tomcat을 쓴다)실행하면, was는 웹 요청을 받았을 때 서블릿을 실행하기 전 맵핑되어있는 필터체인을 신나게 돌릴 것이다.

## 질문

### Q1 filter의 체인이 끊어지면 어떻게 될까?

### Q2. 스프링에서의 Filter 등록 방법과 두 개 이상의 Filter를 등록하면 순서는 어떻게 동작할까?

```java
// filter 하나 등록
@Configuration
public class Config{

    @Bean
    public FilterRegistrationBean firstFilterRegister() {
        FilterRegistrationBean registrationBean = new FilterRegistrationBean(new FirstFilter());
        return registrationBean;
    }
}

```

```java
// filter 두개 등록
@Configuration
public class Config{

    @Bean
    public FilterRegistrationBean firstFilterRegister() {
        FilterRegistrationBean registrationBean = new FilterRegistrationBean(new FirstFilter());
        registrationBean.setOrder(1);
        return registrationBean;
    }

    @Bean
    public FilterRegistrationBean secondFilterRegister() {
        FilterRegistrationBean registrationBean = new FilterRegistrationBean(new SecondFilter());
        registrationBean.setOrder(2);
        return registrationBean;
    }
}

```

FirstFilter - SecondFilter - Controller - SecondFilter - FirstFilter 순으로 동작한다.

### Q3. 필터랑 인터셉터는 무슨 차이지!! 언제 뭘 써야하는가!!

본문에 있음
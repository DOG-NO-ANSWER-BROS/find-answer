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
  * `ServletRequest`와 `ServletResponse`는 `HttpServletRequest`와 `HttpServletResponse`의 부모 인터페이스다.
  * `FilterChain`에는 컨테이너로부터 받은 다른 Filter들의 정보를 담고 있다.
2. 요청의 헤더를 확인한 뒤 요청의 헤더, 정보 또는 요청 자체를 완전히 바꿀 수 있다.
3. 응답의 헤더 또는 데이터를 수정할 수도 있다.
4. `FilterChain`에서 다음 필터의 `doFilter()` 메서드를 호출한다. 요청을 차단할 수도 있는데, 이 경우에는 해당 필터가 응답을 채워야한다.
5. 다음 필터의 `doFilter()`를 호출하고 나면 그 뒤부터는 후처리 작업을 진행할 수 있다.
6. 문제가 발생하면 예외를 던진다.

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
```xml
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
#### 1. `@Component` 사용
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

#### 2. `FilterRegistrationBean` 사용

```java
import org.springframework.boot.web.servlet.FilterRegistrationBean;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class MyConfiguration {
  
    @Bean
    public FilterConfigurationBean<MyFilter> myFilterRegistration() {
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
* 해당 방식을 사용하면 url을 지정해서 필터를 적용할 수 있다.

## 인터셉터와 다른 점
필터는 서블릿 api에 속한 기능이고 인터셉터는 Spring MVC에서 사용하는 기능이다.
즉, 필터는 서블릿에 요청이 전달되기 전에 사용되고, 인터셉터(`Handler Interceptor`)는
`Handler Mapping`이 Handler에게 요청 처리를 위임하기 전에 실행된다.

따라서 클라이언트가 보내는 모든 요청/응답에 대한 처리를 해야한다면 Filter를 사용하는 것이 적합하고,
HandlerMapping 별로 다른 처리를 해야한다면 Interceptor를 사용하는게 맞다.

라고 말은 했지만 아직 확실하게 와닿지는 않아서 다음 편에 HandlerInterceptor 공식 문서를 참조하여 Interceptor 설명을 추가할 예정.


## HandlerInterceptor

### what?
`HandlerAdapter`가 핸들러를 실행시키기 전에 실행되는 것.
핸들러 실행 전후로 전처리와 후처리를 담당한다.

* `HandlerMapping`별 `HandlerInterceptor`를 지정할 수 있다.
* 인터셉터 역시 `ApplicationContext`에 빈으로 등록되어 있다.
* 연속적으로 여러 인터셉터를 `HandlerMapping`에 등록할 수 있다.
    * A - B - C - D 순으로 인터셉터가 등록되었을 경우, preHandle은 A - B - C - D 순, 그리고 컨트롤러 실행 후 postHandle은 D - C - B - A 순으로 실행된다. (preHandle과 postHandle이 무엇인지는 밑에 설명)

### Filter와 HandlerInterceptor의 차이

#### Filter
* 모든 요청 처리 과정에 참여한다. 즉, filter의 기능이 모든 요청에 적용된다.
* Servlet api에 속해있다.

#### HandlerInterceptor
* HandlerMapping 단위로 등록할 수 있다. 이러면 A HandlerMapping에 등록된 인터셉터는 B HandlerMapping에서 실행되지 않는다.
* URL 패턴을 이용해서 모든 핸들러 매핑에 일괄적으로 적용할 수도 있다. (Filter가 servlet별, URL별로 나뉘는 것과 똑같다.)
* Handler와 View 렌더링 과정에서 조금 더 세부적인 처리를 추가할 수 있다:
    * preHandle : 핸들러가 실행되기 전 실행
    * postHandle : 핸들러가 실행된 후 실행
    * afterCompletion : 뷰가 렌더링 된 후 실행
    
`HttpServletRequest`, `HttpServletResponse` 말고도 실행될 컨트롤러 빈 오브젝트, 컨트롤러가 돌려주는
`ModelAndView`, 그리고 발생한 예외 등 Spring-MVC에서 생기는 요소를 제공받아 요청에 대한 더 정교한 처리를 할 수 있다.
무엇보다 인터셉터 자체가 빈이기 때문에 다른 빈을 주입받아 사용할 수 있다!

물론 Filter에서도 빈을 사용할 수 있긴 하다. 하지만 Filter 자체는 `ApplicationContext`가 관리하는 빈이 아니다.

### how?

1. 인터셉터를 구현한다.
```java
@Slf4j
public class LoggingInterceptor implements HandlerInterceptor {

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        log.info("initialized my interceptor");
        return true; // false를 반환하면 DispatcherServlet이 필터가 응답을 처리했다고 판단한다.
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        // returns true
        HandlerInterceptor.super.postHandle(request, response, handler, modelAndView);
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response,
                                Object handler, Exception ex) throws Exception {
        // returns true;
        HandlerInterceptor.super.afterCompletion(request, response, handler, ex);
    }
}
```

2. `WebMvcConfigurer`를 구현한 Configuration 클래스의 `addInterceptors` 메서드를 오버라이딩하여 registry에 추가한다.
```java
@Configuration
public class WebMvcConfig implements WebMvcConfigurer {

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(loggingInterceptor())
                .addPathPatterns("/lines/**");
    }

    @Bean
    public LoggingInterceptor loggingInterceptor() {
        return new LoggingInterceptor();
    }
}
```

해당 URL로 요청이 갈 때 controller가 요청을 처리하기 전에 인터셉터가 실행된다.
위의 코드에서는 간단하게 "/lines" 관련 url로 요청이 갈 때 로깅을 한다:

`2021-06-16 21:49:40.781  INFO 5151 --- [nio-8080-exec-7] w.s.line.application.LoggingInterceptor  : initialized my interceptor`

인터셉터의 `preHandle`메서드가 false를 반환하면 DispatcherServlet이 필터가 응답을 처리했다고 판단한다.

## 질문
Interceptor의 `preHandle()`이 false를 반환하면 client에 무엇이 전달될까?

답 : 200 ok 응답. 그리고 body에는 아무것도 들어있지 않다. 물론 파라미터로 받은 `response`를 수정하면 원하는 응답을 만들 수 있다:
```java
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response,
                             Object handler) throws Exception {
        response.setStatus(HttpStatus.BAD_REQUEST.value());
        return false;
    }
```
다음과 같이 `preHandle()`을 작성하면 400을 반환한다.


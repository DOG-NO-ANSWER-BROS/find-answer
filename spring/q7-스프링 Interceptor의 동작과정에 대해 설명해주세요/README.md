## 인터셉터란?

컨트롤러에 들어오는 요청 HttpRequest와 컨트롤러가 응답하는 HttpResponse를 말그대로 가로채는(Intercept) 역할을 한다. 인터셉터는 관라지 페이지에 접근하기 전데 관리자 인증을 하는 용도로 활용될 수 있다.

Filter와 역할이 유사한데, Filter와 Interceptor 는 다르다 유의하자.

![https://user-images.githubusercontent.com/63634505/122759765-47d5e800-d2d5-11eb-81f7-a62bb0747a44.png](https://user-images.githubusercontent.com/63634505/122759765-47d5e800-d2d5-11eb-81f7-a62bb0747a44.png)

## Filter와 Interceptor 차이

- 호출 시점

Filter 는 DispatcherServlet 실행 전, Interceptor는 DispatcherServlet이 실행된 후

- 설정 위치

Filter 는 web.xml, Interceptor 는 spring-servlet.xml

- 구현 방식

Filter 는 설정을 하면 구현이 가능하다 하지만 Interceptor 는 설정하고 메서드 구현도 필요하다.

## Filter 와 Interceptor 인터페이스

Filter

```java
public interface Filter {
    void doFilter(ServletRequest request, ServletResponse response, FilterChain chain);
}

```

Interceptor

Interceptor는 Handler를 실행하기전(preHandle), Handler를 실행한 후(postHandle), view를 렌더링 한후(afterCompletion) 등, 메서드에 따라 실행 시점을 다르게 가져간다.

```java
public interface HandleInterceptor {
    boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler);
    void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView mav);
    void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex);
}

```

## 스프링 부트에서의 Configuration Class

```
@Configuration
public class SampleWebMvcConfig extends WebMvcConfigurerAdapter {

    @Override
    public void addInterceptors(InterceptorRegistry registry) {

        List<String> URL_PATTERNS = Arrays.asList("/doA", "doB");
        registry.addInterceptor(new SampleInterceptor())
        	.addPathPatterns(URL_PATTERNS)
            	.excludePathPatterns("/login")
            	.excludePathPatterns("/admin/**/");
    }
}

```

addInterceptor 는 원하는 인터셉터 클래스를 지정할 수 있다.

addPathPatterns 는 적용할 URL 패턴을 설정한 `*` `**` 등을 사용하여 URI pattern String 을 전송하거나 URI pattern 배열을 전송할 수 있다.

excludePathPatterns 는 인터셉터를 제외할 URL 패턴을 등록하는 메서드이다. 해당 URL로 접근시에는 인터셉터를 적용하지 않도록 한다.

## HandlerInterceptor 메소드

- preHandle(request, response, handler)

    지정된 컨트롤러의 동작 이전에 수행할 동작(사전제어)

- postHandle(request, response, handler, modelAndView)

    지정된 컨트롤러의 동작 이후에 처리할 동작(사후 제어). Spring MVC 의 DispatcherServlet이 화면을 처리하기 전에 동작

- afterCompletion(request, response, handler, exception)

    DispatcherServlet 의 화면 처리가 완료된 이후에 처리할 동작

## Handler Interceptor

![https://user-images.githubusercontent.com/48986787/122791845-8fb83780-d2f4-11eb-9207-9576609c24a5.png](https://user-images.githubusercontent.com/48986787/122791845-8fb83780-d2f4-11eb-9207-9576609c24a5.png)

전반적인 클라이언트 요청흐름 그림에서 확인해보면, `Spring Container`내에서 `Handler Mapping(수행할 handler 결정)` 이후 `HandlerAdaptor(결정된 핸들러 수행)` 전/후 과정에서 수행됨을 확인할 수 있어요.

주 사용 목적은 `핸들러(컨트롤러)`의 수정없이 핸들러 수행 전/후처리 동작을 추가하여 `핸들러(컨트롤러)`의 반복적인 코드를 제거하기 위함이에요.

쉽게 말해, `HandlerMapping`이 결정한 handler을 `HandlerAdapter` 수행 전, 후로 가로체어 추가적인 작업이 가능해요.  또한 View 렌더링 이후 클라이언트에게 Response를 전달하기 전에 추가적인 작업이 가능해요.

### 어디에 사용될까?

가만보니, Filter와 매우 유사해요.

인터페이스가 선언 되어있는 곳의 글을 번역하자면, Filter의 기능중 `1. 핸들러 자체의 실행을 금지하는 옵션`, `2. 후처리 기능`만 허용하기에. 실제로는 Filter가 더 강력하다고 볼 수 있어요.

기본 가이드라인으로, `HandlerInterceptor`는 조금더 세분화된 즉, 핸들러의 반복 코드 제거나, 권한 확인시에 사용하는 것을 권장된다고 해요.

또한, `Filter`는 모든 요청과 응답에 관한 처리(멀티파트, GIZP 압축)등과 같은 처리에 사용되고, 특정 컨텐츠(이미지와 같은)에 필터를 매핑하는 경우 사용한다고 해요.

### 코드

```java
package org.springframework.web.servlet;

public interface HandlerInterceptor {

	default boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)
			throws Exception {

		return true;
	}

	default void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler,
			@Nullable ModelAndView modelAndView) throws Exception {
	}

	default void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler,
			@Nullable Exception ex) throws Exception {
	}
}

```

- **preHandle**

지정된 컨트롤러의 동작 이전에 수행할 동작 (사전 제어).

- **postHandle**

지정된 컨트롤러의 동작 이후에 처리할 동작 (사후 제어).

Spring MVC의 Dispatcher Servlet이 화면을 처리하기 전에 동작.

- **afterCompletion**

Dispatcher Servlet의 화면 처리가 완료된 이후 처리할 동작.

<br>

## 동작 순서 (DispatcherServlet의 관점)

```java
1. HandlerMapping 할당
2. HandlerAdapter 할당
3. preHandler
4. HandlerAdapter 수행
5. postHandler
6. View 렌더링
7. afterCompletion

```

<br>

1. `HandlerMapping` 사용할 핸들러 결정
    1. `HandlerExecutionChain`에 사용할 핸들러를 지정함
2. `HandlerAdapter` 사용할 핸들러 결정
3. `preHandler()` 메소드 호출
    1. `HandlerExecutionChain`에 여러 인터셉터를 두어 지정된 핸들러를 처리. 이를 통해 `interceptor`는 HTTP 오류를 보내거나 False를 반환하여 실행 체인을 중단할 수 있음 .

    ```java
    if (!mappedHandler.applyPreHandle(processedRequest, response)) {
       return;
    }

    ```

    1. return값이 false 면, 바디가 없는 200 ok반환. (response에 응답바디 설정 가능)
    2. true면 다음 작업이 실행
4. `HandlerAdapter` 수행
5. `postHandler()` 메소드 호출
6. View 렌더링 수행
7. `afterCompletion` 호출

### Multi Interceptor

2개 이상의 Interceptor가 등록되었을 때 동작순서를 알아보기로 해요.

```java
1. HandlerMapping 할당
2. HandlerAdapter 할당
3-1. preHandler 1
3-2. preHandler 2
4. HandlerAdapter 수행
5-1. postHandler 2
5-2. postHandler 1
6. View 렌더링
7-1. afterCompletion 2
7-2. afterCompletion 1

```

### 코드 확인

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {

    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new GumpInterceptor()).order(2);
        registry.addInterceptor(new GumpGumpInterceptor()).order(1)
    }
}

```

기본적으로 선언 순선에 따라 실행되고, `order메소드`를 통해  순서를 지정할 수 있어요.

## HandlerInterceptor

### what?

`HandlerAdapter`가 핸들러를 실행시키기 전에 실행되는 것.
핸들러 실행 전후로 전처리와 후처리를 담당한다.

- `HandlerMapping`별 `HandlerInterceptor`를 지정할 수 있다.
- 인터셉터 역시 `ApplicationContext`에 빈으로 등록되어 있다.
- 연속적으로 여러 인터셉터를 `HandlerMapping`에 등록할 수 있다.
    - A - B - C - D 순으로 인터셉터가 등록되었을 경우, preHandle은 A - B - C - D 순, 그리고 컨트롤러 실행 후 postHandle은 D - C - B - A 순으로 실행된다. (preHandle과 postHandle이 무엇인지는 밑에 설명)

### Filter와 HandlerInterceptor의 차이

### Filter

- 모든 요청 처리 과정에 참여한다. 즉, filter의 기능이 모든 요청에 적용된다.
- Servlet api에 속해있다.

### HandlerInterceptor

- HandlerMapping 단위로 등록할 수 있다. 이러면 A HandlerMapping에 등록된 인터셉터는 B HandlerMapping에서 실행되지 않는다.
- URL 패턴을 이용해서 모든 핸들러 매핑에 일괄적으로 적용할 수도 있다. (Filter가 servlet별, URL별로 나뉘는 것과 똑같다.)
- Handler와 View 렌더링 과정에서 조금 더 세부적인 처리를 추가할 수 있다:
    - preHandle : 핸들러가 실행되기 전 실행
    - postHandle : 핸들러가 실행된 후 실행
    - afterCompletion : 뷰가 렌더링 된 후 실행

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

1. `WebMvcConfigurer`를 구현한 Configuration 클래스의 `addInterceptors` 메서드를 오버라이딩하여 registry에 추가한다.

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

`2021-06-16 21:49:40.781 INFO 5151 --- [nio-8080-exec-7] w.s.line.application.LoggingInterceptor : initialized my interceptor`

인터셉터의 `preHandle`메서드가 false를 반환하면 DispatcherServlet이 필터가 응답을 처리했다고 판단한다.

<br>

### ## 인터셉터 동작과정

개발자가 코드상에서 HandlerInterceptor인터페이스를 구현한 인터셉터를 만들어 xml 혹은 java configuration 을 통해 인터셉터를 레지스트리에 등록한다. path pattern으로 등록할 수 있다.

웹어플리케이션에서 해당 정보를 읽는 시점은 DispactcherSevlet의 상위 추상 객체 FrameworkServlet에서 initWebApplicationContext 하는 시점이다. 디스패처 서블릿은 내부적으로 콘텍스트를 기반으로 전략들을 등록시키는데,
메소드들을 보면 한번 씩 들어본 내용일 것이다.

```java
class DispatcherServlet extends FrameworkServlet {
...
	protected void initStrategies(ApplicationContext context) {
		initMultipartResolver(context);
		initLocaleResolver(context);
		initThemeResolver(context);
		initHandlerMappings(context); // 인터셉터도 여기서 맵핑된다.
		initHandlerAdapters(context);
		initHandlerExceptionResolvers(context);
		initRequestToViewNameTranslator(context);
		initViewResolvers(context);
		initFlashMapManager(context);
	}
...
}

```

나중에 요청이 들어오면 서블릿의 기본 구현인 doService()내에서 doDispatch() 라는 메소드를 실행시킨다.

doDispatch() 코드 안에서 Spring MVC의 코드들이 실행되는데,
그러면 먼저 initHandler에서 등록시켜놓은 handlerMappings 리스트를 기반으로(인터셉터, 컨트롤러 모두 등록되어 있다.) 실행시켜야 할 HandlerExecutionChain을 만든다.

HandlerExecutionChain은 path pattern과 일치하는 인터셉터의 리스트와 마지막으로 수행해야할 컨트롤러 핸들러 어댑러로 이루어진다.

HandlerExecutionChain을 실행시키면 먼저 인터셉터 리스트의 prehandle() 메소드를 수행하고, 컨트롤러 로직을 수행한 후, 인터셉터 리스트를 역순으로 순회하며 posthandle()을 수행한다.

Response객체에 stream을 소모 한 후(요청에 내보낼 내용을 작성한 이후), 다시 인터셉터 리스트를 역순으로 순회하며 afterCompletion을 수행한다.

### 깔끔하게 정리

1. 디스패처 서블릿이 실행 될 때, 빈으로 등록해놓은(혹은 빈 레지스트리에 등록해놓은) 인터셉터 목록이 등록된다.
2. 알맞은 url 패턴에 맞는 요청이 들어왔을 때, 인터셉터 목록을 리스트 자료구조로 만들어 놓는다.
3. 컨트롤러를 수행하기 전 인터셉터 리스트를 정 순서로 preHandle() 메소드에 구현해 놓은 로직을 수행한다.
4. 컨트롤러를 수행하고 나면 인터셉터 리스트를 역 순서로 postHandle() 메소드에 구현해 놓은 로직을 수행한다.
5. Response에 대한 처리를 완료하고 나면 인터셉터 리스트를 역 순서로 afterCompletion() 메소드에 구현해 놓은 로직을 수행한다.

## 질문

### Q1. 인터셉터가 여러개일 경우(firstInterceptor, secondInterceptor ...) 인터셉터의 순서는?

firstPre -> secondPre -> controller -> secondPost -> firstPost -> secondAfter -> firstAfter

### Q2. 커스텀 인터셉터가 여러개 일 때, 특정 url을 어떻게 지정할까?

```java
@Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new GumpInterceptor()).addPathPatterns("/test/**");
        registry.addInterceptor(new GumpGumpInterceptor()).addPathPatterns("/test/**");
    }

```

### Q3. Interceptor의 `preHandle()`이 false를 반환하면 client에 무엇이 전달될까?

답 : 200 ok 응답. 그리고 body에는 아무것도 들어있지 않다. 물론 파라미터로 받은 `response`를 수정하면 원하는 응답을 만들 수 있다:

```java
@Override
public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
    response.setStatus(HttpStatus.BAD_REQUEST.value())
    return false;
}

```

다음과 같이 `preHandle()`을 작성하면 400을 반환한다.

### Q4. WeqRequestInterceptor 란 무엇인가..?

웹 요청을 추상화 해놓은 WebRequest를 인자로 받아 처리한다. 좀더 간략화한 Interceptor라고 생각한다.
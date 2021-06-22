# 정리

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

## Refer

[https://goodgid.github.io/Spring-HandlerInterceptor/](https://goodgid.github.io/Spring-HandlerInterceptor/)

# 질문

## 나만 알만한 것

### 질문

### 커스텀 인터셉터가 여러개 일 때, 특정 url을 어떻게 지정할까?

```java
@Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new GumpInterceptor()).addPathPatterns("/test/**");
        registry.addInterceptor(new GumpGumpInterceptor()).addPathPatterns("/test/**");
    }
```
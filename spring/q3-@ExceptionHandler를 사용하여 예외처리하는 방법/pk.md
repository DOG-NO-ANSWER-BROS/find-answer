# ExceptionHandler

## 정리

### 먼저, 예외를 처리하는 방법
* MVC의 구조에서 만약 예외를 잡는 곳이 없다면 서블릿 컨테이너(우리의 경우 Tomcat)가 예외를 처리하게 된다.
* 하지만 DispatcherServlet은 서블릿 컨테이너로 예외를 던지기 전에 그 예외를 처리할 수 있는지 확인한다.
* Dispatcher의 그 예외 처리 전략이 바로 `HandlerExceptionResolver`. 즉, 컨트롤러에서 생긴 예외를 잡을 리졸버를 찾는 것이다.

### HandlerExceptionResolver 전략의 네 가지 구현체
```java
public interface HandlerExceptionResolver {

	@Nullable
	ModelAndView resolveException(
                                HttpServletRequest request, 
                                HttpServletResponse response, 
                                @Nullable Object handler, Exception ex);

}
```
* `DispatcherServlet`의 전략이 다 그렇듯, `HandlerExceptionResolver`도 네 가지 구현체가 제공되고 그 중 세 가지는 default로 등록되어 있다.
* 그리고 위의 코드에서 알 수 있듯, 에러가 발생했을 시 따로 그 에러만을 위한 페이지를 `ModelAndView`를 통해 전달할 수 있다.
* default 전략이라고 해도, 내가 직접 만든 구현체를 등록시키면 default가 자동으로 적용되지 않기 때문에 명시적으로 등록해야한다.

#### 1. ResponseStatusExceptionResolver (default)
```java
@ResponseStatus(HttpStatus.BAD_REQUEST)
public class RequestException {
    // 예외 구현
}
```
* 커스텀 예외를 만들고, 그 위에 `@ResponseStatus` 어노테이션을 붙여서 예외를 특정 Http 상태코드로 전달해주는 전략:
* 커스텀 예외가 아닌 예외에는 적용할 수 없다. 기존 예외를 처리하려면 `@ExceptionHandler`를 사용해야 한다.

#### 2. DefaultHandlerExceptionResolver (default)
<img width="690" alt="Screen Shot 2021-05-25 at 1 52 49 AM" src="https://user-images.githubusercontent.com/48251668/119380610-ec2b3580-bcfb-11eb-9e7b-0abb691ac836.png">
* Spring MVC 내부에서 발생하는 에러를 다루는 구현체. 아마 우리가 프로그래밍하면서 생긴 에러 중 일부분을 이 리졸버가 잡아줬을 것.
* 우리가 직접 던지는 에러를 담당하지는 않는다. 

* 대표적으로 [TypeMisMatchException](https://stackoverflow.com/questions/57944251/spring-boot-handle-type-mismatch-errors) 이 있다. 요청으로 들어온 값을 파싱하다가 오류가 생기면 400이 뜬다.

#### 3. SimpleMappingExceptionResolver (not default, 쓰기 위해서는 따로 bean으로 등록해야 함)
<img width="662" alt="Screen Shot 2021-05-25 at 2 04 27 AM" src="https://user-images.githubusercontent.com/48251668/119382824-8b9cf800-bcfd-11eb-8762-0dd34f68ea76.png">
* 예외가 생겼을 때 보낼 페이지를 지정할 수 있는 리졸버.

### 그리고 4. AnnotationMethodHandlerExceptionResolver (default)

* `@ExceptionHandler`를 사용하여 에러를 처리하는 방식이다.
* `@ResponseStatus` 방식보다 손이 많이 가지만 그만큼 더 자율적이다.

기본적인 예시:
```java
    @ExceptionHandler(RuntimeException.class)
    public ResponseEntity<String> handleUnknownException() {
        final String message = "unhandled exception occured";
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body(message);
    }
```
* 위의 코드는 애플리케이션에서 **개발자가 예상하지 못한 에러**를 잡을 때 주로 사용하는 방식이다.
* 그런데 해당 메서드의 return 값과 parameter에 들어갈 수 있는 요소는 [훨씬 많다](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/bind/annotation/ExceptionHandler.html).
* Servlet 관련 객체인 `HttpServletRequest`, `HttpServletResponse`, 심지어 `HttpSession`까지도 parameter에 명시만 해주면 가지고 올 수 있다.

예를 들어:
```java
    @ExceptionHandler(RuntimeException.class)
    public String handleError(HttpServletRequest request, Exception e) {
      logger.error("Request: " + request.getRequestURL() + " raised " + e);
      return "exception";
    }
```
* 위의 코드처럼 String을 return하고, `HttpServletRequest`를 parameter로 받아와서 로깅에 활용할 수도 있다.
* 참고로 String을 리턴한다고 해서 진짜 문자열이 보내지는게 아니다. 해당 이름의 뷰를 찾아서 띄워줌.
* String을 리턴하고 싶다면 `ResponseEntity<String>`을 활용하면 된다.

`@ResponseStatus`와 함께 사용할 수도 있다:
```java
    @ResponseStatus(value=HttpStatus.UNAUTHORIZED, reason="just doing it")
    @ExceptionHandler(IllegalArgumentException.class)
    public void handleMyIllegalArgumentException() {
      // IllegalArugmentException 발생 시 401 반환. 좋은 예시는 아니다.
    }
```
* `@ResponseStatus`만 단독으로는 기본 예외에 적용하지 못한다고 했었지만, 이렇게 `@ExceptionHandler`와 사용하면 가능하다!
* 리턴 타입이 void인 것도 확인할 수 있다.



## 질문
"언제, 어떤 방식을 사용하시나요?"

## 팁
출처: [스프링 공식 블로그](https://spring.io/blog/2013/11/01/exception-handling-in-spring-mvc)
* 내가 만든 에러는 먼저 `@ResponseStatus` 를 고려한다.
* 기본 에러를 처리하고 싶다면 `@ControllerAdvice`에서 `@ExceptionHandler`로 처리할 것을 고려한다.
* Controller 특정 에러를 처리하고 싶다면 해당 컨트롤러 안에서 `@ExceptionHandler`를 사용한다.
* 일관성을 지키기 위해 노력한다. 주의할 점은 **여러 `@ControllerAdvice`의 처리 순서는 Spring에 정해지지 않았다는 것**.

## 팁 2
<img width="344" alt="Screen Shot 2021-05-25 at 2 52 13 AM" src="https://user-images.githubusercontent.com/48251668/119387675-39130a00-bd04-11eb-8df8-33fa0a20a424.png">

* 우리 서버 처음 만들면 볼 수 있는 'whitelabel error page'는 스프링 부트에서 만든 페이지다.

* `org.springframework.boot.autoconfigure.web.servlet.error` 패키지에 있는 `ErrorMvcAutoConfiguration` 클래스에서 그 구현 방식을 볼 수 있다.

# 인터셉터 동작과정
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

## 질문 - WeqRequestInterceptor 란 무엇인가..?
1. 웹 요청을 추상화 해놓은 WebRequest를 인자로 받아 처리한다. 좀더 간략화한 Interceptor라고 생각한다.

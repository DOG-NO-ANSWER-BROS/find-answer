# 정리

## 디스패쳐 서블릿

이름에서 느껴지듯이, 탐색하는 서블릿이다. 스프링 MVC에서 요청이 들어왔을 때, 요청을 전달할 컨트롤러 메서드를 찾아 위임하는 클래스이다.

디스패쳐 서블릿이 은 달리 말하면 컨트롤러들의 프록시 객체라고도 볼 수 있다. AOP를 구현하는 방법은 여러가지가 있는데, 프록시도 그 중 하나이다. 컨트롤러는 비즈니스 로직만 처리하고, 요청 맵핑, 뷰 객체로 변환, 파라미터 전달 등 다양한 기능을 디스패쳐 서블릿이 대신 수행해준다. 이처럼 관심사의 분리를 통해 컨트롤러를 구현하는 개발자는 비즈니스 로직에 집중할 수 있다. MVC입장에선 이를 프론트 컨트롤러 패턴이라고 한다.

## FLOW

1. 디스패쳐 서블릿에 요청이 들어온다. 요청은 이미 서블릿 컨테이너에 의해  HttpServletRequest로 추상화된 상태이다. 

2. 이후 MuitifileResolver 등에서 다른 방식으로 처리해야할 요청인지 분석한다.

3. 아니라면 HandlingMapping에 요청을 전달하여 요청을 처리할 컨트롤러를 탐색한다.

4. 핸들러를 실행할 수 있는 어댑터를 탐색한다. 어댑터에는 supports 가 구현되어 있는데, 지원가능한 어댑터 목록을 순회하며 instanceOf 비교를 통해 적합한 어댑터를 찾는다. 아래는 컨트롤러 핸들러 어댑터의 예시이다.

   ```java
   public class SimpleControllerHandlerAdapter implements HandlerAdapter {
   
   	@Override
   	public boolean supports(Object handler) {
   		return (handler instanceof Controller);
   	}
     ...
   ```

5. 찾은 핸들러 어댑터를 통해 컨트롤러로 타입캐스팅하여 컨트롤러의 handleRequest(req, res)메소드를 실행한다. 결과적으로 모델 앤 뷰를 리턴한다.

   ```java
   	@Override
   	@Nullable
   	public ModelAndView handle(HttpServletRequest request, HttpServletResponse response, Object handler)
   			throws Exception {
   
   		return ((Controller) handler).handleRequest(request, response);
   	}
   
   ```

6. 반환받은 모델앤 뷰 객체를 통해 뷰 리졸버를 찾아서 실행한다.
	```java
	@Nullable
	protected View resolveViewName(String viewName, @Nullable Map<String, Object> model,
			Locale locale, HttpServletRequest request) throws Exception {

		if (this.viewResolvers != null) {
			for (ViewResolver viewResolver : this.viewResolvers) {
				View view = viewResolver.resolveViewName(viewName, locale);
				if (view != null) {
					return view;
				}
			}
		}
		return null;
	}
	```
	```java
	public interface ViewResolver {
	@Nullable
	View resolveViewName(String viewName, Locale locale) throws Exception;
	}

	```
7. 모델에 값이 있다면 뷰와 함께 렌더링하고, 없다면 뷰를 그대로 반환한다. 이 뷰는 HttpServletResponse객체에 담겨 서블릿 컨테이너에 전달된다.

# 질문
1. 요청이 들어왔을 때 LocaleResolver, ThemeResolver, MultipartResolver 를 거친다는데, 각각의 리졸버가 하는 역할이 무엇일까?
    

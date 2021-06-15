# 정리
# 필터와 인터셉터의 차이
- 스프링 Interceptor은 다들 알고 있을 것이다. Spring MVC에서 내부적으로 요청 처리 앞과 뒤에 AOP를 적용하고 싶은 일이 있으면 인터셉터를 활용한다. 즉, 디스패쳐 서블릿 동작 내부의 일이다.
- 필터는 그럼 무엇인가? 흔히들 다음과 같은 짤을 많이 인용한다.
![image](https://user-images.githubusercontent.com/51393021/121841974-6c9cef00-cd1a-11eb-805c-2fd3bc3e2ef2.png)
스프링 필터라고 구글에 검색하면 가장 먼저 나오는 블로그이다. 구글에서 자주 뵙는 갓대희님...
- 중요한 부분은 필터가 디스패쳐 서블릿을 들어가기 전에 동작한다는 것이다. 여기서 생기는 의문!
- 요청에 대해서 공통적으로 적용할 만한 작업이 있을 때 인터셉터, 필터를 통해 처리한다는 내용은 알았다. 그러나 그 두개의 차이는 무엇인가? 서블릿에 들어오기 전과 들어온 후를 구분하는 의미는 무엇인가?
- 우리는 웹 개발자고, 스프링을 통해 웹 어플리케이션을 만들 떄 서블릿에 들어온 이후에 동작에 대해 코딩한다.(Cotroller 코드는 디스패쳐 서블릿 이후의 동작이다) 그래서 인터셉터와 필터의 차이가 애매하게 느껴질 수 있다(내가 그랬다)
- 이 두개의 차이를 알기 위해선 웹 컨테이너와 웹 어플리케이션의 관계에 대해 이해해야 한다.
- 웹 어플리케이션의 정체는 WAS에 실을 WAR(혹은 JAR) 프로그램이다. 구현은 WAS의 스펙에 맞추어 웹 요청을 추상화 하여 처리할 수 있게 만든 Servlet을 구현해야 한다. 스프링의 MVC는 이 Serlvet의 내부 구현이다.
- 한편 WAS의 구현 중 에는 웹 요청을 받은 직후, 처리하고 클라이언트에게 요청을 반환하기 전 처리할 코드를 구현 할 수 있는 filter라는 기능이 존재한다. 스프링에서 필터 구현은, 이 필터에 들어갈 기능을 구현하는 것이다.
- 부트에서의 필터구현은 워낙 많은 참고자료가 있기에 블로그 중 하나를 첨부한다. https://luvstudy.tistory.com/79
- 인터셉터에서 구현할 만한 것은 request와 resposne객체에 영향을 줄 만한 것, Argument Resolver라던가, CorsFilter라던가, viewResolver라던가.. 등등이 있을 것 이다.  
- 내 스프링-MVC 에서 제공하고자 하는 비즈니스 로직과 관련된 것은 인터셉터에서 해줄 것 같고, 인증/인가 처럼 서블릿을 탈 필요가 없거나 요청에 대한 로깅처럼 WAS단에서 처리할만한 작업은 필터에서 해줄 것 같다.

- 당장 떠오르는 건 없지만, 서블릿에 들어가기 전 공통 처리가 필요한 부분, 혹은 요청이 나가기 직전 처리해야할 작업이 생길 경우 필터를 고려해보자. 필터는 서블릿 별로, 그리고 Url패턴 별로 지정할 수 있는데, 스프링을 활용할 경우엔 디스패처 서블릿 한가지만 존재하므로 서블릿 맵핑 보다는 url 맵핑을 활용 하게 될 것 같다.
# 필터의 동작
- 필터를 직접 구현하려면 tomcat의 WEB-INF/web.xml 에 필터 이름과 필터 구현에 사용할 클래스 파일 위치(이 클래스는 javax.servlet.Filter 를 구현한 클래스여야 한다), 그리고 이 필터가 적용될 서블릿 혹은 URL 패턴을 명기해주면 된다.
- 참고로 서블릿 3.0부터 `@WebFilter` 라는 어노테이션을 통해 필터를 등록할 수 있다 ㅎ_ㅎ
  ```xml
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
- ![iOS 이미지](https://user-images.githubusercontent.com/51393021/121978032-4763bb80-cdc2-11eb-9216-5d61feb00191.jpg)
- 3과 7이라는 숫자는 신경쓰지 말자. 두 개의 필터체인이다. 
- 부트의 필터 구현은 필터 등록을 **알아서** 해주는 것이다. 부트 최고~ 예컨데 필터를 빈으로 등록하면 @WebFilter를 통해 등록해 줄 것이고, 필터 순서를 등록하면 filter의 순서를 조정할 것이다. 
- 그리고 WAS a.k.a servlet container(우리는 주로 tomcat을 쓴다)실행하면, was는 웹 요청을 받았을 때 서블릿을 실행하기 전 맵핑되어있는 필터체인을 신나게 돌릴 것이다.
  
## 질문
- 필터랑 인터셉터는 무슨 차이지!! 언제 뭘 써야하는가!!
## 답변
- 본문에 있음

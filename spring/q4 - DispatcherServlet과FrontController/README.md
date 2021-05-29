## MVC 컨트롤러의 단점

일반적인 MVC 컨트롤러로 구현 하면 공통적인 부분을 처리하기 어렵다.

![image](https://user-images.githubusercontent.com/63634505/119676938-3c7bd200-be79-11eb-9e66-5b0305e23004.png)

- **포워드 중복**

  View 로 이동하는 코드가 항상 중복된다.

```java
RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
dispatcher.forward(request, response);
```

- **ViewPath 중복**

  prefix : `/WEB_INF/views/` , suffix : `.html` , `.jsp`

```java
String viewPath = "/WEB-INF/views/hello.html";
```

- **사용하지 않는 코드**

```java
HttpServletRequest request, HttpServletResponse response
```

FrontController 를 도입하면 중복 처리 문제를 해결 할 수 있다.

## FrontController 란?

서버로 들어오는 클라이언트의 모든 요청을 받아 처리해주는 컨트롤러이다. 즉 웹의 모든 요청을 처리하는 컨트롤러이다. MVC 패턴에서 코드 중복없이 재사용 할 수 있도록 한다.

**FrontController 도입 후**

![image](https://user-images.githubusercontent.com/63634505/119677091-61704500-be79-11eb-864f-0214396fed16.png)

## FrontController 특징

- FrontController 하나로 클라이언트 요청을 처리
- 요청에 맞는 컨트롤러를 찾아 호출해준다.
- 공통처리 부분을 FrontController에서 해준다.
- 서블릿 처리 부분을 FrontControlle에서만 해주면 된다.

**스프링 MVC에서 DispatcherServlet 이 FrontController 로 구현되어 있다.**

![image](https://user-images.githubusercontent.com/63634505/119677257-88c71200-be79-11eb-9837-5cc20e0f7b13.png)

![image](https://user-images.githubusercontent.com/63634505/119677365-a005ff80-be79-11eb-8e82-be853c3a031e.png)

## DispatcherServlet이란?

dispatch는 보내다라는 뜻을 가지고 있다. `스프링 MVC`에서 `FrontController`패턴을 구현한 Servlet 이다.

즉, 서블릿 컨테이너에서 HTTP 프로토콜을 통해 들어오는 모든 요청을 Presentation 계층의 제일 앞에 둬서 중앙집중식으로 처리해주는 FrontController라 말할 수 있다.

어플리케이션으로 들어오는 모든 요청을 핸들링 해준다.

![https://user-images.githubusercontent.com/48986787/119694236-0265fc80-be88-11eb-85c5-58f2bf6b9737.png](https://user-images.githubusercontent.com/48986787/119694236-0265fc80-be88-11eb-85c5-58f2bf6b9737.png)

### 장점

### 컨트롤러가 서블릿 의존성에서 벗어나도록 해준다

`DispatcherServlet`이 없으면 모든 컨트롤러는 서블릿으로 구현되어야 했다. 프론트 컨트롤러가 서블릿으로서 모든 요청을 받고 이에 알맞은 컨트롤러를 찾아 호출해주는 역할을 함으로써 자연스럽게 컨트롤러는 서블릿을 구현하지 않아도 된다.

`DispatcherServlet`은 호출하는 컨트롤러에 HttpServletRequest와 HttpServletResponse를 직접 넘기지 않고 Map과 Model 또는 ModelAndView를 직접 생성하여 컨트롤러에 넣어줌으로 인해서 컨트롤러가 서블릿에 의존하지 않도록 해준다.

## Refer

[https://yoonbing9.tistory.com/79](https://yoonbing9.tistory.com/79)

[https://rutgo.tistory.com/231](https://rutgo.tistory.com/231)

[https://rutgo.tistory.com/231](https://rutgo.tistory.com/231)

[https://jess-m.tistory.com/15](https://jess-m.tistory.com/15)

<br>

# 질문

## 나만 알만한 것

### 질문

DispatcherServelt이 가지는 전략들

### 답변

**MultipartResolver**

multipart 파일 업로드 요청에 대해 해석하여 변환

**LocaleResolve**

웹 기반의 locale 결정. 스프링에서는 Request, Session, Cookie 등을 기반으로 사용한 구현체가 있음

**ThemeResolver**

웹 기반의 Theme 결정. 태그를 사용한 경우 구현체 ThemeResolver로 Theme를 정의할 수 있음.

**HandlerMapping**

Request와 핸들러(메소드) 객체를 매핑을 정의. 일반적으로 사용하는 @RequestMapping 을 이용한 핸들러 매핑 전략임.

**HandlerAdapter**

HandlerMapping이 핸들러(메소드)를 찾아주면, HandlerAdpter가 해당 컨트롤러의 핸들러에 전달하기 위한 Adapter라고 보면 된다  ModelAndView를 리턴하게끔 해준다고 생각.

**HandlerExceptionResolver**

Handler 매핑이나, 컨트롤러 실행 도중 발생한 예외를 다룸

**RequestToViewNameTranslator**

컨트롤러에서 뷰를 지정하지 않았을 때, Request를 참고하여 내부적으로 뷰 이름을 생성.

**ViewResolver**

컨트롤러에서 Request를 처리하고 생성하는 결과물에 대한 view 처리. 컨트롤러가 return "index.jsp"; 와 같이 뷰 이름을 넘겨주더라도 viewResolver가 ModelAndView 객체로 변경해서 돌려줌. 디폴트는 InternalResourceViewResolver (컨트롤러가 리턴한 뷰 이름으로 실제 뷰 이름을 지정. prefix와 suffix를 조합하는 로직도 여기포함).

**FlashMapManager**

FlashMap 객체를 retrieve & save.


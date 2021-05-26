# q4 - DispatcherServlet과 FrontController

# 정리

## DispatcherServlet이란?

dispatch는 보내다라는 뜻을 가지고 있어요. `스프링 MVC`에서 `FrontController`패턴을 구현한 Servlet이에요. 

즉, 서블릿 컨테이너에서 HTTP 프로토콜을 통해 들어오는 모든 요청을 Presentation 계층의 제일 앞에 둬서 중앙집중식으로 처리해주는 FrontController라 말할 수 있어요.

어플리케이션으로 들어오는 모든 요청을 핸들링 해줘요.

![https://user-images.githubusercontent.com/48986787/119694236-0265fc80-be88-11eb-85c5-58f2bf6b9737.png](https://user-images.githubusercontent.com/48986787/119694236-0265fc80-be88-11eb-85c5-58f2bf6b9737.png)

그림에서 흐름은 위에서 아래로 진행돼요.

### 장점

### 컨트롤러가 서블릿 의존성에서 벗어나도록 해줘요

`DispatcherServlet`이 없으면 모든 컨트롤러는 서블릿으로 구현되어야 했어요. 프론트 컨트롤러가 서블릿으로서 모든 요청을 받고 이에 알맞은 컨트롤러를 찾아 호출해주는 역할을 함으로써 자연스럽게 컨트롤러는 서블릿을 구현하지 않아도 돼요.

`DispatcherServlet`는 호출하는 컨트롤러에 HttpServletRequest와 HttpServletResponse를 직접 넘기지 않고 Map과 Model 또는 ModelAndView를 직접 생성하여 컨트롤러에 넣어줌으로 인해서 컨트롤러가 서블릿에 의존하지 않도록 해줘요.

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

<br>

## 진짜 궁금한 것

### 질문

DispatcherServlet의 과부화가 생기지 않을까?
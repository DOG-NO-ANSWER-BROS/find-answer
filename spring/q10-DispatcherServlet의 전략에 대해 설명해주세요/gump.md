# 정리

> 스프링 MVC에서 FrontController패턴을 통해 구현한 Servlet인 DispatcherServlet에서 사용하는 전략들을 대해 알아보도록해요.

스프링은 `유연성`과 `확장성`, `다양성`에 무게를 두고 있는 프레임워크에요. 추상화를 통해 모든 기능을 다양한 방법으로 확장할 수 있게 설계되어 있어요. 스프링이 제공하는 주요 기능은 세부 전략을 변경해서 사용할 수 있도록 확장 포인트가 제공돼요. 
스프링을 잘 사용한다는 것은, 스프링이 제공하는 유연하고 확장서이 뛰어난 구조를 이용해서, 각 프로젝트에 맞는 최적화된 구조를 만들어내고, 관례를 따라 빠르게 개발 가능한 스프링 기반의 프레임워크를 만들어서 사용할 수 있다는 뜻이에요. 

## DispatcherServlet

DispatcherServlet은 스프링의 웹 기술을 구성하는 `다양한 전략들을 DI로 구성`해서 확장하도록 만들어진 스프링 서블릿/MVC의 엔진과 같은 역할을 해요.
스프링의 웹 기술은 `MVC 아키텍쳐`를 근간으로 하고있어요. MVC는 프레젠테이션 계층의 구성요소 정보를 담은 모델(M), 화면 출력 로직을 담은 뷰(V), 그리고 제어 로직을 담은 컨트롤러(C)로 분리하고, 이 세 가지 요소가 서로 협력해서 하나의 웹 요청을 처리하고 응답을 만들어내는 구조예요.
MVC 아키텍처는 보통 프론트 컨트롤러 패턴과 함께 사용돼요.
DispatcherServlet은 FrontController 패턴으로 구현되어 있는데, 프레젠테이션 계층의 제일 앞 단에 있으며 서버로 들어오는 모든 요청을 먼저 받아서, 공통적인 작업(Mapping, Adpater 등)을 먼저 수행한 후에 세부 컨트롤러에 작업을 위임하고, 클라이언트에게 보낼 뷰를 선택해서 최종 결과를 생성하는 작업을 수행해요. 
`DispatcherServlet이 MVC 컴포넌트들과 동작하는 기본적인 구조`를 살펴봐요.

![https://user-images.githubusercontent.com/48986787/131529284-4d6c72fd-f87e-4de2-9bd9-81de6c9f359a.png](https://user-images.githubusercontent.com/48986787/131529284-4d6c72fd-f87e-4de2-9bd9-81de6c9f359a.png)

DispatcherServlet의 조금더 자세한 동작과정은 아래 그림에서 확인해요

![https://user-images.githubusercontent.com/48986787/131533760-d7aeefcf-44ca-46a8-a929-71293c4d6aaa.png](https://user-images.githubusercontent.com/48986787/131533760-d7aeefcf-44ca-46a8-a929-71293c4d6aaa.png)

### 1.DispatcherServlet의 Http Request 접수

자바의 서블릿 컨테이너는 HTTP 프로토콜을 통해 들어오는 요청이 스프링의 `DispatcherServlet`에 할당된 것이라면, HTTP 요청정보를 `DispatcherServlet`에 전달해줘요. 
`DispatcherServlet`은 모든 요청에 대해 공통적으로 진행해야 하는 전처리 작업이 등록된 것이 있다면 이를 먼저 수행해요. 공통적으로 이용 가능한 보안이나 파라미터 조작, 한글 디코딩과 같은 작업이 처리돼요.

### 2. DispatcherServlet에서 컨트롤러로 HTTP 요청 위임

URL이나 파라미터 정보, HTTP 메서드 등을 참고로 해 서 어떤 컨트롤러에게 작업을 위임할지 결정해요. 컨트롤러를 선정하는 것은 `HandlerMapping`(그림확인)전략을 이용해요. 스프링에서는 컨트롤러를 핸들러라고 부르는데, `웹의 요청을 다루는(handle) 오브젝트`라는 의미에요. 사용자 요청을 기준으로 어떤 핸들러에게 작업을 위임할지를 결정하는 것을 `핸들러 매핑 전략`이라 말해요.
이를 전략이라 부르는 이유는 DI의 가장 대표적인 용도라고 할 수 잇는 전략 패턴이 적용되어 있기 때문이에요. 
DispatcherServlet의 `핸들러 매핑 전략`은 DispatcherServlet의 수정 없이도 DI를 통해 얼마든지 확장 가능해요. DispatcherServlet은 그 자체로 스프링 컨텍스트에 등록되는 빈이 아니므로 DI가 일어나는 것은 아니에요. 하지만 마치 DI가 적용되는 것처럼 서블릿 애플리케이션 컨텍스트의 빈을 가져와 사용해요. 이때 Autowiring을 사용해요. 
자바 객체 사이에 무엇인가 요청이 전달되려면 메소드가 호출돼야하고, 그러려면 DispatcherServelt이 컨트롤러 오브젝트의 메소드를 호출 할 수 있는 방법이 있어야해요. 이때 어댑터를 이용해요.  전형적인 어댑터 패턴을 사용해서, 특정 컨트롤러를 호출해야 할 때는 해당 컨트롤러 타입을 지원하는 어댑터를 중간에 껴서 호출하는 것이에요. 그러면 DispatcherServlet은 항상 일정한 방식으로 컨트롤러를 호출하고 결과를 받을 수 있어요. 이는 `핸들러 어댑터 전략`이라 불러요. 이때, 모든 웹 요청 정보가 담긴 `HttpServletRequest` 타입의 오젝트를 전달하고, 이를 어댑터가 적절히 변환해서 컨트롤러의 메소드가 받을 수 있는 파라미터로 변환해서 전달해요. `HttpServletResponse`도 함께 넣어줘요. HttpServletResponse 오브젝트 안에 직접 값을 넣어 줄 수 도 있기 때문이에요.

### 3. 컨트롤러의 모델 생성과 정보 등록

컨트롤러의 작업은 먼저 `1.사용자 요청을 해석하는 것`, `2.그에 따라 실제 비즈니스 로 직을 수행하도록 서비스 계층 오브젝트에게 작업을 위임하는 것`, 그리고 `3.결과를 받아서 모델을 생성하는 것`, 마지막으로 `4.어떤 뷰를 사용할지 결정하는 것`의 네 가지로 분류할 수 있어요. 모델을 생성하고 모델에 정보를 넣어주는 게 컨트롤러가 해야 할 마지막 중요한 두 가지 작업 중 하나에요. 컨트롤러가 어떤 식으로든 다시 DispatcherServlet에 돌려줘야 할 두 가지정보가 있는데,그 중 하나가 모델이고 다른 하나가 뷰에요.
모델은 보통 맵에 담긴 정보라고 생각하면 돼요. 이름과 그에 대응되는 값의 쌍으로 정보를 만드는 것이에요.
모델은 이름과 객체 값의 쌍으로 만들어짐을 기억하면돼요. 

### 4. 컨트롤러의 결과 리턴: 모델과 뷰

모델이 준비됐으면 다음은 뷰를 결정할 차례에요. MVC의 모든 요소가 그렇듯이 뷰도 하나의 오브젝트에요. 컨트롤러가 뷰 오브젝트를 직접 리턴할 수 있지만, 보통은 뷰의 논리적인 이름은 리턴해주면 `뷰 리졸버`가 이를 이용해 뷰 오브젝트를 생성해줘요. 뷰를 사용하는 전략과 방봅도 매우 다양하기 때문에, 일단은 컨트롤러가 뷰에 정보를 돌려준다는 사실만 기억해두고 넘어가요.
ModelAndView라는 이름의 오브젝트가 있는데, 이 ModelandView가 DispatcherServlet이 최종적으로 어댑터를 통해 컨트롤러부터 돌려받는 오브젝트에요. 이름 그대로 모델과 뷰, 두 가지 정보를 담고 있어요.
모델과 뷰를 넘기는 것으로 컨트롤러의 책임은 끝이에요. 다시 작업은 DispatcherServlet으로 넘어가요

### 5. DispatcherServlet의 뷰 호출과 6. 모델 참조

DispatcherServlet이 컨트롤러로부터 모델과 뷰를 받은 뒤에 진행하는 작업은, 뷰 객체에 모델을 전달해주고, 클라이언트에게 돌려줄 최종 결과물을 생성해달라고 요청하는 것이에요. 보통은 브라우저를 통해 사용자가 결과를 볼 테니 브라우저에서 나타날 HTML을 생성하는 일이 가장 흔한 뷰의 작업이에요. JSON으로 생성해 주는 뷰도 있어요. 모델은 같을지라도 어떤 뷰가 선택되느냐에 따라 다른 형태의 결과물이 만들어져요. 
뷰 작업을 통한 최종 결과물은 HttpServletResponse 오브젝트 안에 답겨요 

### 7. HTTP 응답 돌려주기

뷰 생성까지의 모든 작업을 마쳤으면 DispatcherServlet은 등록된 후처리기가 있는지 확인하고(Interceptor afterCompletion), 있다면 후처리기에서 후속 작업을 진행한 뒤에 뷰가 만들어준 HttpServletResponse에 담긴 최종 결과를 서블릿 컨테이너에게 돌려줘요. 서블릿 컨 테이너는 HttpServletResponse에 담긴 정보를 HTTP 응답으로 만들어 사용자의 브라우저나 클라이언트에게 전송하고 작업을 종료해요. 

 

<br> 

## DispatcherServlet의 DI 확장 가능한 전략

스프링 MVC는 자주 사용되는 전략을 디폴트로 설정해주고 있어요. 필요한 전략만 확장해서 사용하고 나머지는 디폴트 전략을 이용해도 돼요.

### HandlerMapping

URL과 요청 정보를 기준으로 어떤 핸들러 객체, 즉 컨트롤러를 사용할 것인지 결정하는 로직을 담당해요.
HandlerMapping 인터페이스를 구현해서 만들 수 있어요. (하나 이상의 핸들러 매핑을 가질 수 있음)
우선순위

`0` = RequestMappingHandlerMapping : 애노테이션 기반의 컨트롤러인 @RequestMapping에서
사용
`1` = BeanNameUrlHandlerMapping : 스프링 빈의 이름으로 핸들러를 찾는다.

### HandlerAdapter

핸들러 매핑으로 선택한 컨트롤러/핸들러는 DispatcherServlet이 호출할 때 사용하는 어댑터. 컨트롤러 타입에는 제한이 없으며, 컨트롤러 호출 방법은 타입에 따라 다르기 때문에 컨트롤러를 결정했다고 해도 호출 방법을 DispatcherServlet이 알길이 없어요. 

@RequestMapping과 @Controller 애 노테이션을 통해 정의되는 컨트롤러의 경우는 DefaultAnnotationHandlerMapping에 의해 핸들러가 결정되고, 그에 대응되는 AnnotationMethodHandlerAdapter에 의해 호출이 일어나요

우선순위

`0` = RequestMappingHandlerAdapter : 애노테이션 기반의 컨트롤러인 @RequestMapping에서 사용
`1` = HttpRequestHandlerAdapter : HttpRequestHandler 처리
`2` = SimpleControllerHandlerAdapter : Controller 인터페이스(애노테이션X, 과거에 사용)
처리

### HandlerExceptionResolver

### ViewResolver

### LocaleResolver

### ThemeResolver

### RequestToViewNameTranslator

### MultipartResolver

### FlashMapManager

# 질문

## 나만 알만한 것

### 질문

DispatcherServlet은 어떻게 빈을 주입할까? 

### 답변
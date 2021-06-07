# 정리

## 우선 스프링 MVC의 요청과정을 큰 그림으로 보기로해요.

![https://user-images.githubusercontent.com/48986787/120974971-b2eacf00-c7ab-11eb-9f28-fe5f92644697.png](https://user-images.githubusercontent.com/48986787/120974971-b2eacf00-c7ab-11eb-9f28-fe5f92644697.png)

큰 흐름으로, 클라이언트가 HTTP 요청을 보내면, WAS는 TCP/IP 연결 대기 과정(`welcome 소캣`)에서 클라이언트를 위한 소캣(`연결 소캣`)을 생성해서 연결해요.

연결이후 많은과정이 일어나요. (다음 과정으로 간다는 것은, 반환되지 않았다는 얘기에요)

1. WAS는 HTTP 메세지를 파싱해서, Web Server에서만 필요한 정보면, 정적 페이지를 반환해요.
2. `Request`, `Response` 객체를 만들어 Filter 객체에게 던져줘요.
3. Filter에서 요청된 내용을 변경(`인코딩 변환 처리`)하거나,여러가지 체크(`XSS 방어`)를해요. 여기서 체크에 걸린다면, 예외를 반환해요.
4. Filter에서 `HttpServletRequest`, `HttpServletResponse`객체로 변환하고, 이를 DispatcherServlet에게 넘겨줘요. (ServletContainer가 servlet의 구현에 대한 부분을 담당하고, 이후 servlet에 요청에대한 역할을 위임하는 방식하는 방식이기 때문)
5. DispatcherServlet내의 `HandlerMapping(컨트롤러 메소드 지정)`, `HandlerAdapter(컨트롤러 메소드 실행)`인터페이스 구현체들이 실행돼요. 이때 각 인터페이스 구현 과정 중 interceptor, resolver에 만족하지 못한다면, 예외를 반환해요. 
6. request의 내용(`헤더, 바디`)을 바탕으로 서비스가 실행되고, response에 서비스 결과를 넘겨줘요. 

각각의 자세한 부분은 이후 포스팅에서 다루기로 할게요.

### 그렇다면, 각 과정의 자원은 공유되는 것일까요?

TCP 연결 요청에 관한 부분은 RMI를 통해 해결하기에, TCP 연결 설정에 관한 부분은 다루지 않기로해요(너무 어려워요 ㅠㅠ). 간략히, TCP 연결 쓰례드가 Servlet 쓰레드의 부모 쓰레드로 설정돼요.

모든 접근이 동일한 자원을 공유한다면, 하나의 요청이 끝날 때 까지 자원을 사용하지 못할거에요.

그렇기 때문에 스프링에서는 ThreadPool을 이용한 `Multi Thread`를 이용해요. 

쓰레드는 `애플리케이션 코드를 하나하나 순차적으로 실행하는 것`이에요.

![https://user-images.githubusercontent.com/48986787/120984637-d0bd3180-c7b5-11eb-9ea9-84ccaf5b5a8f.png](https://user-images.githubusercontent.com/48986787/120984637-d0bd3180-c7b5-11eb-9ea9-84ccaf5b5a8f.png)

요청이 들어오면, `ThreadPool`에서 여유 쓰레드가 있는지 확인하고, 있다면 요쳥에 대해 쓰레드를 할당해줘요. 이 쓰레드는 servlet을 실행해요. → `여유 쓰레드가 없다면 대기하거나 거절할 수 있어요(WAS 정책에 따라 다름).`

모든 요청이 반환되면 사용된 쓰레드는 쓰레드 풀에 반환돼요.

이러한 방식의 장점으로는 쓰레드가 미리 생성되어 있으므로, 쓰레드를 생성하고 종료하는 비용(CPU)이 절약되고, 응답시간이 빨라져요. 또한 생성 가능한 쓰레드의 최대치가 있으므로 너무 많은 요청이 들어와도 기존 요청은 안전하게 처리할 수 있어요.

## 결론

각 과정은 코드상으로 확인할 수 있는 부분들을 보고 작성했어요. 그래서 틀린부분이 분명히 있을것이라 생각해요. 혹시 틀린부분이 있다면.. 주저말고 피드백을 주시면 감사하겠습니다(피드백을 통해 성장할게요) 🙇🏻‍♂️ 

<br>

## Refer

[https://goddaehee.tistory.com/154](https://goddaehee.tistory.com/154)

<br>

# 질문

## 나만 알만한 것

### 질문

스프링에서는 TCP연결에 관한 부분을 어떻게 해결할까?

### 답변

RMI(Remote Method Invocation)를 통해

<br>

## 진짜 궁금한 것

### 질문
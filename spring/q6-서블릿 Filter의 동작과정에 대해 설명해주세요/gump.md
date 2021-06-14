
# 정리

스프링 mvc의 전반적인 요청과정은 이전에 정리했어요 

[[Spring] 쓰레드 관점에서 본 Spring MVC HTTP 요청과정](https://livenow14.tistory.com/59)

<br>

## **Servlet Filter**

요청을 받은 `WAS`가 `Servlet Container`에게 `Request, Response`를 던져주면, 이를 먼저 `Filter`에서 받아. `Request`에 대한 필터링를 진행하고, 서블릿에게 `ServletRequest, ServletResponse`를 건네요.

요청에 대한 작업이 끝난 후 Response에 대한 필터링를 진행해요.

쉽게말해, 요청과 응답에 대한 필터링을 진행해요.

필터는 서블릿과 달리 각 필터에 대해 종속성이 없어요. 

<br>

## **동작과정**

```java
import javax.servlet.*;
import java.io.IOException;

public class GumpFilter implements Filter {
    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
        // FilterConfig를 통한 filter 설정
        // 서블릿 컨테이너가 필터 인스턴스 생성한 후 초기화 하기 위해서 사용 전 호출하는 메소드
    }

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
        // 필터에서 구현해야 하는 로직을 작성하는 메소드

				// 처리 전
				... 
				chain.doFilter(request, response);
				...
				// 처리 후
    }

    @Override
    public void destroy() {
        // 필터 인스턴스를 종료시키기 전에 호출하는 메소드
    }
}
```

filter는 총 3가지의 동작과정을 거쳐요. **`1.**초기화, 2. 필터링, 3. 소멸`

1. **init(웹 컨터네이너가 기본적으로 구현)**

    서블릿 컨테이너는 필터를 인스턴스화 한 후 정확히 한 번 init 메소드를 호출해요.

    성공적으로 초기화가 완료돼야 필터링을 수행할 수 있어요.

    FilterConfig를 통한 커스텀한 filter 설정이 가능해요.

2. **doFilter(개발자가 직접 구현)**

    `request, response`에 대한 필터링 로직을 작성하는 곳이에요.

    `resquest, response`가 체인을 통과할 때 마다 서블릿 컨테이너에서 호출돼요. 

    여기서 체인은, 연결되어 있음을 의미해요.

    즉, 파라미터로 들어올 시 이전 필터의 정보를 받아오며, 메소드 안에서 사용시 다음의 필터 또는 리소스를 호출하는 역할을 해요(사용하지 않을 시 다음 필터로 결과를 전달하지 않아요). 

    필터에서 구현해야하는 로직을 작성해요. 

3. **destroy(웹 컨테이너가 기본적으로 구현)**

    `doFilter` 메서드 내의 모든 스레드가 종료되거나 설정한 제한 시간이 지난 후에 서블릿 컨테이너에 의해 한번 호출돼요.

    웹 컨테이너가 destroy를 호출 한 후에는 인스턴스에서 doFilter를 다시 호출하지 않아요.

    인스턴스를 소멸하기 전 호출하는 메소드에요.

<br>

### 언제 사용하는게 좋을까?

1. 인증
2. 로깅
3. 이미지 변환
4. 데이터 압축 
5. 인코딩 / 디코딩
6. 암호화
7. 캐싱 

등 많은 곳에 사용될 수 있어요. 

<br> 

# 질문

## 나만 알만한 것

### 질문

### 답변

<br>

## 진짜 궁금한 것

### 질문

filter의 체인이 끊어지면 어떻게 될까?
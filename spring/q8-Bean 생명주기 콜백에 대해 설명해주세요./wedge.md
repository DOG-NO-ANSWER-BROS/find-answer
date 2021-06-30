# 정리

## 빈 생명주기 콜백

처음엔 빈 생명주기 콜백 이란 말을 듣고 띠용 했기 때문에 용어의 정리부터 하겠다.
1. 빈(bean)은 스프링이 구동 시 applicationContext에 저장해놓고 재사용 하거나, 필요할 때 생성할 수 있는 객체를 가리킨다.  
2. 생명주기(life cycle)란 객체의 탄생(생성)과 죽음(파괴)까지의 과정을 나타낸다. 자바에서 탄생은 객체 생성(new), 죽음은 GC에 의해 메모리에서 사라지는 것이다. 
3. 콜백 함수(callback function)란 어떤 함수가 실행된 후 이행되어야 하는 함수를 가리킨다. 누군가 내게 전화를 하면 전화가 끝나고 다시 전화를 거는 것에 빗대어 `callback` 함수 이다. 

종합하면 다음과 같이 설명할 수 있겠다. 

> 스프링에서 생성한 빈이 생성 혹은 파괴 될 때 이행되어야 하는 함수 

그런데.. 이상하지 않은가? 빈 생명주기 콜백을 잠시 머릿속에서 지워보자.

생성시에 이행되어야 하는 함수 라는 말을 들으면 무엇이 생각나는가? 자바에선 이를 공식적으로 지원하지 않던가?

바로바로 `생성자Constructor` 이다. 자바에선 객체가 생성될 때 초기화 작업을 할 수 있는 생성자 문법을 지원한다. 그런데 빈 생명주기 콜백함수는 왜 별도로 존재하는가? 
(생성자에서 초기화 작업을 하는 것은 좋은 패턴은 아니라고 생각하는데, 이유는 가장 아래에 서술해 놓았다.)

## 빈의 생명주기 

그 것은 스프링이 DI 컨테이너 이기 때문이다. 빈은 스스로 완성되지 않는다. DI Container에 의해 의존성을 주입받아 필요로 하는 객체를 갖춘 순간부터 해당 빈은 본인의 역할을 수행할 수 있다.

field inject, constructor inject, setter inject 세가지 주입 방식 중 생성자 주입을 이용하지 않으면 빈이 생성된 시점에선 필드에 할당할 객체를 갖추고 있지 않다.(null)

예시 코드는 https://velog.io/@syleemk/Spring-Core-%EB%B9%88-%EC%83%9D%EB%AA%85%EC%A3%BC%EA%B8%B0-%EC%BD%9C%EB%B0%B1 에서 따왔다.

```java
public class NetworkClient {
     
    // 주입받아야 할 대상
    private String url;
    
    public NetworkClient() {
        System.out.println("생성자 호출, url" + url);
        connect();
        call("초기화 연결 메시지");
    }
    
    // setter 주입
    public void setUrl(String url) {
        this.url = url;
    }
    
    //서비스 시작시 호출
    public void connect() {
        System.out.println("connect = " + url);
    }
    
    public void call(String message) {
        System.out.println("call: " + url + " message = " + message);
    }
    
    //서비스 종료시 호출
    public void disconnect() {
        System.out.println("close: " + url);
    }
}
```

NetworkClient 객체가 connect를 하고 싶으면, url을 가지고 있어야 한다. 기존 방식대로 하면 생성자 시점엔 필요로 하는 bean 객체가 없을 수 있다. 그래서 connect는 사실 주입 받은 후에 실행해야 한다.
그런데 `주입받은 직후` 를 어떻게 알 수 있을까?

- 인터페이스 Initializing Bean, Disposable Bean
Initializing Bean은 afterProperiesSet()메서드로 초기화를 지원한다. (의존관계 주입이 끝난 후 호출되는 콜백 메서드이다.)
DisposableBean은 destroy()메서드로 소멸을 지원한다. (빈이 소멸되기 직전에 호출되는 콜백 메서드이다.)

```java
@Component
public class Bean2 implements DisposableBean, InitializingBean {
    private Bean1 bean1;

    public Bean2(Bean1 bean1) {
        this.bean1 = bean1;
        System.out.println("응애 나 아가빈");
    }

    @Override
    public void afterPropertiesSet() throws Exception {
        System.out.println("이제 활동 가능");
    }
  
    @Override
    public void destroy() throws Exception {
        System.out.println("으악 나 주거");
    }

}
```

하지만 스프링 라이브러리 메소드이므로 스프링 의존적이고, 내가 고칠 수 없는 외부라이브러리엔 적용할 수 없다. 테스트가 난해해지는 단점도 있다.
위의 이유로 현재는 거의 사용하지 않는다. 그렇다면?

빈에 대해서는 `@Bean(initMethod="init", destroyMethod="close")` 식으로 설정을 줄 수 있다.
컴포넌트에 대해선 @PostConstruct, @PreDestroy애노테이션을 메소드 위에 붙여 사용하면 편리하게 초기화와 종료를 실행할 수 있다. 
@PostConstruct, @PreDestroy는 자바 표준 이기도 하다.(javax.annotation.)
참 좋은 세상이다.

# 질문
- 생성자에서 무거운 초기화 작업을 하는 게 별로인 이유? 
1. 객체의 생성과 초기화를 분리하자! 
- 생성자는 필수정보(파라미터)를 받고, 메모리를 할당해서 객체를 생성하는 책임을 가진다.
- 반면에 초기화는 이렇게 생성된 값들을 활용하여 외부 커넥션을 연결하는 등의 무거운 동작을 수행한다.
- 따라서 생성자 안에서 무거운 초기화작업을 같이 하는 것보다는 객체를 생성하는 부분과 초기화 하는 부분을 명확하게 나누는 것이 유지보수 관점에서 좋다. 
- 물론 초기화 작업이 내부 값들만 약간 변경하는 정도로 단순한 경우에는 생성자에서 한번에 다 처리하는게 나을 수 있다.

참고 레퍼런스 : https://velog.io/@syleemk/Spring-Core-%EB%B9%88-%EC%83%9D%EB%AA%85%EC%A3%BC%EA%B8%B0-%EC%BD%9C%EB%B0%B1

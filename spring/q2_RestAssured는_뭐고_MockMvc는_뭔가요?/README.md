RestAssured와 MockMvc는 테스트를 편리하게 할 수 있게 해주는 테스트 도구.

## RestAssured 란?

REST 웹 서비스를 검증하기 위한 Java 라이브러리. given-when-then 패턴으로 코드를 작성할 수 있어 세팅, 액션, 검증을 명확하게 파악 할 수 있다. End-to-End 테스트테 사용한다. `@SpringBootTest` 로 실제 용청을 보내서 전체적인 로직을 테스트 한다.

```java
@Test
public void firstTest() throws Exception
{
	RestAssured
		.given()
			.param("param1", "param2")
		.when()
			.get("/test")
		.then()
			.statusCode(200)
		    .log().all();
}
```

### 사용 예시:
```java
    public static ExtractableResponse<Response> postLine(final LineRequest lineRequest) {
        return RestAssured
                .given().log().all()
                    .body(lineRequest)
                    .contentType(MediaType.APPLICATION_JSON_VALUE)
                .when()
                    .post("/lines")
                .then().log().all()
                    .extract();
    }
```
처음 봤던 `firstTest()`는 테스트에서 바로 쓰였을 때, 그리고 위의 코드는 중복해서 쓰이는 RestAssured 부분을
따로 메서드로 추출해서 사용했을 때의 모습이다. 이 둘을 자세히 보면 `RequestParam`으로 값을 받을 때는 `param()`, 
그리고 `RequestBody`를 넣어줄 때는 `body()`메서드를 쓰는 것을 볼 수 있다. 그리고 `extract()`를 사용하여 응답을 
따로 추출하여 사용할 수도 있다. 응답 안에 포함된 다양한 정보(ex: 상태 코드, 컨텐트 타입, 바디 안의 값 등)를 확인하기 위해 이렇게 따로 추출한다.

## RestAssured 의존성

RestAssured는 직접 의존성을 추가해줘야 한다. 의존성 하나를 추가함으로 프로젝트가 점점 무거워 질 수 있다.

Gradle

```java
testImplementation 'io.rest-assured:rest-assured:3.3.0'
```

- JUnit 의존성 을 주입하기 전에 선언해야한다.

## RestAssured 특징

- 속도

`@SpringBootTest` 로 수행하기 때문에 시간이 오래 걸린다.

- 가독성

BDD스타일로 작성할 수 있어 가독성이 좋다.

## MockMVC

서버에 배포하지 않고 웹 에플리케이션의 스프링 MVC 동작을 재현할 수 있는 클래스이다. Mock 이 가짜라는 의미. 대부분 Controller Layer 단위 테스트에 사용한다. 테스트에 사용할 가상의 WAS가 필요한데 이를 AOP를 통해 생성한다. 가상의 WAS에 컨트롤러 인스턴스를 주입하여 로딩. andExpect가 assertThat을 대신한다. andDo로 필요한 데이터가 있으면 추출

```java
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.*;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.*;
import static org.springframework.test.web.servlet.setup.MockMvcBuilders.*;

 // ...

WebApplicationContext wac = ...;

MockMvc mockMvc = webAppContextSetup(wac).build();

mockMvc.perform(get("/form"))
     .andExpect(status().isOk())
     .andExpect(content().mimeType("text/html"))
     .andExpect(forwardedUrl("/WEB-INF/layouts/main.jsp"));
```

## MockMvc 의존성

Spring Framework Test 클래스 중 하나로, Spring test 의존성이 추가되어있는 경우 의존성을 추가하지 않아도 사용할 수 있다.

### MockMvc 생성

- `@SpringBootTest` + `@AutoConfigureMockMvc` → 통합테스트 할때
- `@WebMvcTest` → MVC 쪽만 테스트 할때 사용
- MockMvc를 직접 생성하는 방법

```java
@Before
public void before() {
  	mockMvc = MockMvcBuilders.standaloneSetup(MyController.class)
          	    .alwaysExpect(MockMvcResultMatchers.status().isOk())
           	    .build();
}
```

### MockMvc 특징

- 속도

  `@WebMvcTest` 로 테스트를 수행할 수 있다. Presentation Layer Bean만 로드하여 시간이 빠름.

- 가독성

  RestAssured에 비하여 가독성이 떨어진다.

### MockMvc 테스트 사용방법

```java
@Test
public void test() throws Exception {
     mockMvc.perform(get("/test")        // controller의 /test URI를 get방식으로 호출
          .param("number1", "1")  // 파라미터 number1에 1 입력
          .param("number2", "1")) // 파라미터 number2에 1입력
          .andDo(print())                        // 결과를 print. MockMvcBuilders의 alwaysDo(print())로 대체 가능
          .andExpect(status().isOk());           // 호출 결과값이 OK가 나오면 정상처리
 }
```

## **결론**

둘을 한 클래스에서 같이 쓰려면, RessAssured에서 [별도의 구성](https://github.com/rest-assured/rest-assured/wiki/GettingStarted#spring-mock-mvc)을 해줘야한다.

전체 End - to - End ( 전 구간 테스트)를 위해서는 RessAssured를, 컨트롤러 마다의 단위테스트틑 위해서는 MockMVC를 쓴다. 즉, 상황에 따라 결정한다.

이외에도 많은 테스트 도구들이 존재한다.

(어딘가에서는 RestAssured와 스프링 내장의 TestRestTemplate의 작동방식이 비슷하다고 한다.)
# q2_RestAssured는 뭐고 MockMvc는 뭔가요?

## **일단 둘다 애플리케이션의 테스트를 편리하게 할 수 있게 해주는 테스트 토구예요**

둘은 비슷하면서 사용 목적에 따라 사용 시기가 달라져요. 

## **RestAssured**

RestAssured는 `REST 웹 서비스를 검증하기 위한 라이브러리`이며 대부분 `End-to-End Test(전 구간 테스트)`에 사용돼요.

### 문법

```java
Given(). 
        param("x", "y"). 
        header("z", "w").
when().
Method().
Then(). 
        statusCode(XXX).
        body("x, ”y", equalTo("z"));
```

### 작동 방식

```java
    @DisplayName("지하철역을 생성한다.")
    @Test
    void createStation() {
        // given
        Map<String, String> params = new HashMap<>();
        params.put("name", "강남역");

        // when
        ExtractableResponse<Response> response = RestAssured.given().log().all()
                .body(params)
                .contentType(MediaType.APPLICATION_JSON_VALUE)
                .when()
                .post("/stations")
                .then().log().all()
                .extract();

        // then
        assertThat(response.statusCode()).isEqualTo(HttpStatus.CREATED.value());
        assertThat(response.header("Location")).isNotBlank();
    }
```

테스트에서 볼 수 있듯이 `전체로그(log, all)`, `데이터 (params)`, `데이터 타입을 지정(contentTyep)`해서 `메소드(post)`를 사용했고, 그 이후에 `응답값에 대한 부분들 (log,all`)을 `추출(extract)` 한 것을 볼 수 있어요. 

(json data 변환을 쉽게 하게 해줬어요)

## **MockMVC**

MockMvc는 웹 애플리케이션을 `애플리케이션 서버에 배포하지 않고도 스프링 MVC의 동작을 재현`할 수 있는 라이브러리이며 대부분 Controller Layer Unit Test(단위 테스트)에 사용돼요.

@WebMvcTest는 Presentation Layer의 Bean들만 로드해요(빨라요)

### 작동 방식

```java
@WebMvcTest
@AutoConfigureMockMvc
class HelloBootTest {
 
    @Autowired
    MockMvc mockMvc;
 
    @Test
    void whenNoRequestParam_returnDefault() throws Exception {
        this.mockMvc.perform(get("/hello"))
            .andExpect(status().isOk())
            .andExpect(content().string(containsString("Hello world!")))
            .andDo(print());
    }
 
   @Test
	 public void getMember() throws Exception {
   mockMvc.perform(get("/members/1")
           .accept(MediaType.APPLICATION_JSON))
           .andExpect(status().isOk())
           .andExpect(jsonPath("$.id", Matchers.is(1)))
	 @Test
   public void someMethodTest() throws Exception {
   //works as expected
   mockMvc.perform(put("/some/uri/{foo}/{bar}", "foo", "bar"))
           .andExpect(status().isOk()); //works
}
```

## **결론**

둘을 한 클래스에서 같이 쓰려면, RessAssured에서 [별도의 구성](https://github.com/rest-assured/rest-assured/wiki/GettingStarted#spring-mock-mvc)을 해줘야해요.

전체 End - to - End ( 전 구간 테스트)를 위해서는 RessAssured를, 컨트롤러 마다의 단위테스트틑 위해서는 MockMVC를 쓰드록 해요. (상황에 따라 결정해야해요)

이외에도 많은 테스트 도구들이 존재해요. 

(어딘가에서는 RestAssured와 스프링 내장의 TestRestTemplate의 작동방식이 비슷하다고 해요)

## Refer

[https://examples.javacodegeeks.com/spring-boot-mockmvc-tutorial/](https://examples.javacodegeeks.com/spring-boot-mockmvc-tutorial/)
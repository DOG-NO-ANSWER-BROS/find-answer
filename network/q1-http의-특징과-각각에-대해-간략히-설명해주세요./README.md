# q1_http의 특징과 각각에 대해 간략히 설명해 주세요

### **HTTP(HyperText Transfer Protocol)**

인터넷 프로토콜 스택 4계층 중, 애플리케이션 계층의 프로토콜이에요. 

### **기반 프로토콜**

기반 프로토콜로는 

**TCP**: HTTP/1.1, HTTP/2

**UDP**: HTTP3

이에요. 현재는 HTTP/1.1을 주로 사용해요.

### **특징**

클라이언트 서버 구조를 가져요.

무상태 프로토콜이에요.

비 연결성을 가져요

HTTP 메시지로 통신해요 

### **클라이언트 서버 구조**

Request Response 구조라는 뜻이에요.

클라이언트는 서버에 요청을 보내고, 응답을 대기하고

서버는 요청에 대한 결과를 만들어서 응답해요. 

![https://user-images.githubusercontent.com/48986787/115743886-0906dd00-a3cd-11eb-8559-be9f0ebd571b.png](https://user-images.githubusercontent.com/48986787/115743886-0906dd00-a3cd-11eb-8559-be9f0ebd571b.png)

이렇게 클라이언트와 서버가 분리가 되어있는 것이 양쪽이 독립적으로 실행될 수 있기에 좋다고 해요.

### 1. HTTP 세션

HTTP 클라이언트가 TCP(Transmission Control Protocol) 커넥션을 서버의 포트에 연결하며 HTTP 세션은 시작됩니다.
보통 그 포트는 80번이지만, 8080번이 쓰일 때도 있죠. 서버는 그 포트에서 기다리고 있다가 클라이언트로부터 리퀘스트(request)가 들어오면 자신만의 메시지를 보냅니다. 이것이 바로 리스폰스(response)죠.

이 request-response 트랜잭션을 하나의 HTTP 세션이라고 합니다.

### **무상태 프로토콜(Stateless)**

서버가 클라이언트의 상태를 보존하지 않아요

장점으로는 서버의 확장성이 높아져요.(스케일 아웃)

단점으로는 클라이언트가 추가 데이터를 전송해야 해서 데이터가 많아져요.

### **무상태 프로그램의 한계**

모든 것을 무상태로 설계할 수 있는 경우도 있고 없는 경우도 있어요.

그렇기에 로그인이 필요한 곳에서는 왠만하면 상태유지를 사용해요. 이때 쿠키, 세션을 사용해요

상태유지는 최소한만 사용해요

### **비 연결성(Connectionless)**

HTTP는 기본으로 연결을 유지하지 않아요. 

그렇기에 TCP/IP연결 시 모든 자원에서 이 과정이 포함되요 → 속도가 증가됨

이때 HTTP 지속 연결(Persistent Connections)로 문제를 해결할 수 있어요.

![https://user-images.githubusercontent.com/48986787/115745392-5899d880-a3ce-11eb-9862-04330aac1402.png](https://user-images.githubusercontent.com/48986787/115745392-5899d880-a3ce-11eb-9862-04330aac1402.png)

![https://user-images.githubusercontent.com/48986787/115745461-6baca880-a3ce-11eb-8c8b-008ad20297cb.png](https://user-images.githubusercontent.com/48986787/115745461-6baca880-a3ce-11eb-8c8b-008ad20297cb.png)

### **HTTP 메시지**

HTTP 메시지는 클라이언트에서 서버로의 요청(`Request`)과 서버에서 클라이언트로의 응답(`Response`)으로 구성돼요.

일반적으로 아래의 그림과 같이 이루어져 있어요.

쉽게 말해 목적지를 가지는 머리와 몸을 가진 메시지예요. 

![https://user-images.githubusercontent.com/48986787/115747448-3bfea000-a3d0-11eb-8f58-ba38e5508550.png](https://user-images.githubusercontent.com/48986787/115747448-3bfea000-a3d0-11eb-8f58-ba38e5508550.png)

![https://user-images.githubusercontent.com/48986787/115749258-e3300700-a3d1-11eb-9814-c21a7b07fa7e.png](https://user-images.githubusercontent.com/48986787/115749258-e3300700-a3d1-11eb-9814-c21a7b07fa7e.png)

### **Request**

![https://user-images.githubusercontent.com/48986787/115748304-01e1ce00-a3d1-11eb-80f3-dec21a1a22af.png](https://user-images.githubusercontent.com/48986787/115748304-01e1ce00-a3d1-11eb-80f3-dec21a1a22af.png)

Request HTTP 메시지는 위와 같이 이루어져 있어요. 

![https://user-images.githubusercontent.com/48986787/115750494-3060a880-a3d3-11eb-9dd2-4a5d4db941bc.png](https://user-images.githubusercontent.com/48986787/115750494-3060a880-a3d3-11eb-9dd2-4a5d4db941bc.png)

시작라인인 Request-Line에는 위와 같이, Method (공백) Request-URI (공백) HTTP-Version CRLF(줄바꿈)으로 이루어져 있어요.

메소드의 종류는 아래와 같아요.

![https://user-images.githubusercontent.com/48986787/115748774-71f05400-a3d1-11eb-932a-4ae4c0a52d70.png](https://user-images.githubusercontent.com/48986787/115748774-71f05400-a3d1-11eb-932a-4ae4c0a52d70.png)

### **예제**

![https://user-images.githubusercontent.com/48986787/115749214-d90e0880-a3d1-11eb-9bcc-51ee36d3daea.png](https://user-images.githubusercontent.com/48986787/115749214-d90e0880-a3d1-11eb-9bcc-51ee36d3daea.png)

### **Response**

![https://user-images.githubusercontent.com/48986787/115749401-05298980-a3d2-11eb-812f-56be9d1b3328.png](https://user-images.githubusercontent.com/48986787/115749401-05298980-a3d2-11eb-812f-56be9d1b3328.png)

Response HTTP 메시지는 위와 같이 이루어져 있어요. 

![https://user-images.githubusercontent.com/48986787/115749525-22f6ee80-a3d2-11eb-860d-2430789814b3.png](https://user-images.githubusercontent.com/48986787/115749525-22f6ee80-a3d2-11eb-860d-2430789814b3.png)

시작라인인 Status-Line에는 위와 같이, HTTP-Version (공백) Status-Code (공백) Reason-Phrase CRLF(줄바꿈)으로 이루어져 있어요.

상태코드의 종류는 아래와 같아요

![https://user-images.githubusercontent.com/48986787/115749757-63566c80-a3d2-11eb-93ea-492ede9bd610.png](https://user-images.githubusercontent.com/48986787/115749757-63566c80-a3d2-11eb-93ea-492ede9bd610.png)

|코드|메시지|설명|
|:------:|:---:|:---:|
|1XX|정보|정보 교환|
|2XX|성공|데이터 전송이 성공적이거나, 이해 됬거나, 수락됨|
|3XX|방향 바꿈|자료의 위치가 바뀜|
|4XX|클라이언트 오류|클라이언트 측 오류, 주소 잘못 입력하거나 요청이 잘못됨|
|5XX|서버 오류|서버 측의 오류로 올바른 요청을 처리할 수 없음|

### **예제**

![https://user-images.githubusercontent.com/48986787/115749822-7701d300-a3d2-11eb-8d2f-0cb430349df3.png](https://user-images.githubusercontent.com/48986787/115749822-7701d300-a3d2-11eb-8d2f-0cb430349df3.png)

<br>

## **Refer**

- [모든 개발자를 위한 HTTP 웹 기본 지식](https://www.inflearn.com/course/http-%EC%9B%B9-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC/dashboard)
- [https://www.w3.org/Protocols/rfc2616/rfc2616-sec4.html](https://www.w3.org/Protocols/rfc2616/rfc2616-sec4.html)
- [https://www.w3.org/Protocols/rfc2616/rfc2616.html](https://www.w3.org/Protocols/rfc2616/rfc2616.html)
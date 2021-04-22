# HTTP 야호~~ 🙌

HTTP 는 Hypertext Transfer Protocol의 약자로, 서버-클라이언트 간의 통신을 request-response 형식으로 받겠다는 규칙을 정한 프로토콜입니다.

HTTPS는 암호화된 HTTP 연결을 말해요. 예전에는 SSL(Secure Sockets Layer)을 통해서 암호화했는데,
요즘에는 TLS(Transport Layer Security)를 통해 하죠. 이 경우 http:// 가 아닌 https:// 를 사용해야한답니다.
HTTPS는 흔히들 'HTTP over TLS' 라고 표현해요. (즉, TLS는 HTTPS 외에 다른 곳에서도 쓰입니다.)

## HTTP 세션
HTTP 클라이언트가 TCP(Transmission Control Protocol) 커넥션을 서버의 포트에 연결하며 HTTP 세션은 시작됩니다.
보통 그 포트는 80번이지만, 8080번이 쓰일 때도 있죠. 서버는 그 포트에서 기다리고 있다가 클라이언트로부터 리퀘스트(request)가 들어오면
자신만의 메시지를 보냅니다. 이것이 바로 리스폰스(response)죠.

이 request-response 트랜잭션을 하나의 HTTP 세션이라고 합니다.

## 메시지 형태

리퀘스트와 리스폰스 각각의 메시지 형태를 살펴보면 좋겠네요.

### 리퀘스트
* 리퀘스트 줄 (ex: GET /images/logo.png HTTP 1.1)
* [리퀘스트 헤더 필드](https://en.wikipedia.org/wiki/List_of_HTTP_header_fields#Request_fields)
* 비어있는 줄
* [선택적 메시지 body](https://en.wikipedia.org/wiki/HTTP_message_body)

### 리스폰스
* status 줄, status가 된 이유와 함께 (ex: HTTP/1.1 200 OK)
* 리스폰스 헤더 필드 (ex: Content-Type: text/html)
* 비어있는 줄
* [선택적 메시지 body](https://en.wikipedia.org/wiki/HTTP_message_body)

### 물론
리퀘스트 메서드(GET, POST, DELETE... 등)나 리스폰스 status에 관련해서 공부해보면 좋겠지만,
충분히 그것만의 주제로 다뤄도 이상하지 않을 놈들이라 지금은 다루지 않습니다.
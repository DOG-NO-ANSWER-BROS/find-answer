# 정리
> RESTful이란 REST 아키텍처 스타일을 기반으로 작성된 웹 서비스를 의미해요.

# REST
* `REST`는 `Re`presentational `S`tate `T`ransfer의 약자로, 하이퍼미디어 기반 분산 시스템을 구축하기 위한 아키텍처 스타일.
* HTTP 는 Protocol, REST 는 아키텍처이다. 즉, REST 는 설계 방식이다. 표준이 아니라는 것.
* 클라이언트가 RESTful API 를 통해 요청을 하면 해당 API 의 엔드포인트까지 리소스의 상태의 표현(representation of the state of the resource)이 전달된다.
* 해당 정보는 HTTP 프로토콜을 통해 JSON, HTML, 또는 plain text와 같은 다양한 형식으로 전달될 수 있는데, 그 중 가장 많이 쓰이는 것이 JSON.
* JSON으로 전달되는 상태 표현 외에도 헤더와 파라미터 역시 중요하다. URI, authorization, cookie, 그리고 응답 코드와 같은 정보가 담기기 때문이다.

## REST 구성요소

1. **자원(resource): URI**
- 모든 자원에 고유한 ID가 존재하고, 이 자원은 Server에 존재.
- 자원을 구별하는 ID는 ‘/users/:users_id’와 같은 HTTP URI.
- Client는 URI를 이용해서 자원을 지정하고 해당 자원의 상태(정보)에 대한 조작을 Server에 요청.
2. **행위(Verb): HTTP Method
- HTTP 프로토콜의 Method를 사용
- HTTP 프로토콜은 GET, POST, PUT, DELETE와 같은 메서드를 제공**
3. **표현(Representation of Resource)**
- Client가 자원의 상태(정보)에 대한 조작을 요청하면 Server는 이에 적절한 응답(Representation)을 보냄.
- REST에서 하나의 자원은 JSON, XML, TEXT, RSS 등 여러 형태의 Representation으로 나타내어 질 수 있음.
- JSON 혹은 XML를 통해 데이터를 주고 받는 것이 일반적

## REST 처리 방식

REST는 `자원지향구조(ROA: Resource Oriented Architecture)`로 웹 사이트의 컨텐츠(Text, 이미지, 동영상), DB의 내용 등을 전부 `하나의 자원으로 파악`하여 `각 자원의 고유한 URI(Uniform Resource Identifier)를 부여`하고, 해당 자원에 대한 `CRUD(Create, Read, Update, Delete) 작업을 HTTP의 기본 명령어인 POST, GET, PUT, DELETE를 통해서 처리`한다.

**HTTP 메소드, CRDU, SQL**

|HTTP Method|CRUD|SQL|
|:--:|:--:|:--:|
| POST | Create  | INSERT |
| GET | Read  | SELECT |
| PUT | Update  | UPDATE |
| DELETE | Delete  | DELETE |

## REST의 특징
다음 여섯가지 특징을 만족해야 api 가 비로소 RESTful 하다고 말할 수 있다.

### client - server
* 클라이언트, 서버, 그리고 리소스로 이루어진 아키텍처로 이루어져 있으며, HTTP를 통해 소통한다.
- 자원이 있는 쪽이 Server, 자원을 요청하는 쪽이 Client가 됨.
- REST Server: API를 제공, 비즈니스 로직 처리 및 저장을 책임
- Client: 사용자 인증이나 context(세션, 로그인 정보)등을 직접 관리하고 책임짐.
- 서로 간 의존성이 줄어듬

### stateless
- HTTP 프로토콜이 무상태 프로토콜이므로, REST 역시 무상태.
  — Server는 Client의 context(상태)를 저장하지 않음. (세션과 쿠키 같은 context 정보를 신경쓰지 않아도 되므로 구현이 단순해짐)
- Server는 각각의 요청을 완전히 별개의 것으로 인식하고 처리
  — 이전 요청이 다음 요청의 처리에 연관되어서는 안됨
  — 장점으로는 Server의 확정성이 높아짐
  — 단점으로는 상태가 없기에 Client가 추가 데이터를 전송해야함.

### cache
* 서버는 클라이언트에게 응답을 보낼 때 해당 응답이 캐싱 가능한지 가능하지 않은지를 알려줘야 한다. 따라서 클라이언트가 캐싱 가능한 응답을 받았을 경우, 그 응답을 캐싱해두고 다시 관련 정보가 필요할 때 그 정보를 확인할 수 있다. 이를 통해 추가로 요청을 보내는 작업을 피할 수 있다.

### uniform interface
* 클라이언트와 서버가 소통할 때 사용하는 일관성 있는 인터페이스가 있고, 그 인터페이스를 지키면 일부분을 바꿔가며 활용할 수 있다. 다음 네 가지 요소를 통해 해당 특징을 만족할 수 있다.

#### 1. identification of resources
REST에서 '리소스'는 HTML 문서, 이미지, 또는 유저의 정보와 같이 아무거나 될 수 있다. 따라서 어떤 리소스를 나타내는 식별자(위치)는 상호작용을 통해 바뀌지 않아야 한다. 만약 바뀔 일이 있었다면 클라이언트에게 바꼈다는 사실을 알리고, 바뀐 위치를 알려줘야 한다.

#### 2. manipulation of resources through representations
클라이언트는 생성하거나 수정하고 싶은 리소스가 있다면 수정되거나 변경된 상태를 표현하여 서버에게 전송해야한다.

#### 3. self-descriptive messages
서버와 클라이언트는 요청과 응답을 충분히 이해할 수 있는 형식으로 보내야 한다.

#### 4. hypermedia
서버가 클라이언트에게 응답을 보낼 때, 클라이언트가 다음으로 어떤 요청을 추가로 할 수 있는지 포함하여 보내야 한다. 예를 들어 클라이언트가 user 리소스를 POST 하겠다는 요청을 하고 그 요청이 성공적으로 처리되었을 경우, 서버는 클라이언트에게 유저 정보 조회, 유저 정보 수정 등 해당 리소스로 할 수 있는 다음 액션을 알려주는 것이다.

### layered system
- REST Server는 다중 계층으로 구성될 수 있음.
  — API Server는 순수 비즈니스 로직을 수행하고 그 앞단에 보안, 로드밸런싱, 암호화, 사용자 인증 등을 추가하여 구조상의 유연성을 줄 수 있음. 또한 로드밸런싱, 공유 캐시 등을 통해 확장성과 보안성을 향상시킬 수 있음.
- PROXY, 게이트웨이 같은 네트워크 기반의 중간 매체를 사용할 수 있음.

### code-on-demand (optional)
* 서버에서 코드를 클라이언트에게 보내서 실행할 수 있게 한다. (ex: javascript)
- 반드시 충족할 필요는 없음

### RESTful의 목적

REST API 는 이전에 SOAP과의 경쟁에서 이겼는데 그 이유중 하나가 이해하기 쉽고 사용하기 쉬워서이다. RESTful의 근본적인 목적은 성능향상이 아니라 일관적인 컨벤션으로 API의 이해도와 호환성을 높이기 위함이다. 성능이 중요한 상황에서 굳이 RESTful을 고집할 필요는 없다.

## 질문

### 우리는 RESTful api를 어느정도까지 실현해야 할까요?
그리고 우리가 개발한것들은 REST api라 볼 수 있는가? RESTful 한가?

### 마이페이지 조회라 할때

[GET] /api/v1/users/[id}로 해야할까

[GET] /api/v1/users/me로 해야할까
# 정리

> RESTful이란 REST 아키텍처 스타일을 기반으로 작성된 웹 서비스를 의미해요.

REST API는 web 개발에서 자주 다뤄지는 용어에요. 
RESTful이 뭔지 설명하기 앞서, REST에 대해 알아보기로 해요.

## REST?

`REST`는 `Re`presentational `S`tate `T`ransfer의 약자로, 웹 서비스를 생성하기 위한 아키텍처 패턴이에요. 
쉽게 말해, `REST`는 클라이언트와 서버 간에 데이터를 공유하기 위한 방법 중 좋은 방법을 모아놓은 하나의 패턴이에요. 표준이 아니라는 것을 명심해야해요. 
자원을 `이름(자원의 표현)`으로 구분하여 해당 자원의 `상태(정보)`를 주고받는 모든 것을 의미해요. 

**자원(resource)의 표현(representation)에 의한 상태 전달**
자원(resource)이란 해당 소프트웨어가 관리하는 모든 것을 얘기해요. 문서, 그림, 데이터, DB내용 등을 의미해요.
표현(representation)이란 자원을 표현하기 위한 `이름`을 의미해요. DB의 유저 정보가 자원일 때, "Users"를 자원의 표현으로 정해요 

**상태(정보) 전달
데이터가 요청되어지는 시점에서 자원의 상태(정보)를 전달해요. JSON혹은 XML을 통해 데이터를 주고 받는 것이 일반적이에요.**

### REST 처리 방식

REST는 `자원지향구조(ROA: Resource Oriented Architecture)`로 웹 사이트의 컨텐츠(Text, 이미지, 동영상), DB의 내용 등을 전부 `하나의 자원으로 파악`하여 `각 자원의 고유한 URI(Uniform Resource Identifier)를 부여`하고, 해당 자원에 대한 `CRUD(Create, Read, Update, Delete) 작업을 HTTP의 기본 명령어인 POST, GET, PUT, DELETE를 통해서 처리`해요. 

**HTTP 메소드, CRDU, SQL**

|HTTP Method|CRUD|SQL|
|:--:|:--:|:--:|
| POST | Create  | INSERT |
| GET | Read  | SELECT |
| PUT | Update  | UPDATE |
| DELETE | Delete  | DELETE |

### REST 구성요소

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

### REST의 기본 원칙

아래의 원칙을 따라야, REST한 서비스다 라고 말할 수 있어요. 

1. **Server-Client(서버-클라이언트 구조)**
- 자원이 있는 쪽이 Server, 자원을 요청하는 쪽이 Client가 됨.
- REST Server: API를 제공, 비즈니스 로직 처리 및 저장을 책임
- Client: 사용자 인증이나 context(세션, 로그인 정보)등을 직접 관리하고 책임짐. 
- 서로 간 의존성이 줄어듬
2. **Stateless(무상태)**
- HTTP 프로토콜이 무상태 프로토콜이므로, REST 역시 무상태.
  — Server는 Client의 context(상태)를 저장하지 않음. (세션과 쿠키 같은 context 정보를 신경쓰지 않아도 되므로 구현이 단순해짐)
- Server는 각각의 요청을 완전히 별개의 것으로 인식하고 처리 
  — 이전 요청이 다음 요청의 처리에 연관되어서는 안됨
  — 장점으로는 Server의 확정성이 높아짐 
  — 단점으로는 상태가 없기에 Client가 추가 데이터를 전송해야함. 
3. **Cacheable(캐시 처리 가능)**
- HTTP의 특징 중 하나인 Cacheable을 적용할 수 있음 
  — 응답 결과 리소스를 캐시하는 것 
- 캐시 사용을 통해 응답시간이 빨라지고 REST Server 트랜잭션이 발생하지 않기 때문에 전체 응답시간, 성능, 서버의 자원 이용률을 향상시킬 수 있음
4. **Layered System(계층화)**
- Client는 REST API Server만 호출함.( API 만 알고있음 )
- REST Server는 다중 계층으로 구성될 수 있음.
  — API Server는 순수 비즈니스 로직을 수행하고 그 앞단에 보안, 로드밸런싱, 암호화, 사용자 인증 등을 추가하여 구조상의 유연성을 줄 수 있음. 또한 로드밸런싱, 공유 캐시 등을 통해 확장성과 보안성을 향상시킬 수 있음.
- PROXY, 게이트웨이 같은 네트워크 기반의 중간 매체를 사용할 수 있음.
5. **Uniform Interface(인터페이스 일관성)**
- URI로 지정한 Resource에 대한 조작을 통일되고 한정적인 인터페이스로 수행함.
- HTTP 표준 프로토콜에 따르는 모든 플랫폼에서 사용이 가능.
  — 특정 언어나 기술에 종속되지 않음을 의미
6. Code-On-Demand(optional)
- Server로 스크립트를 받아서 Client에서 실행
- 반드시 충족할 필요는 없음 

## REST API?

### API(Application Programming Interfae)란

데이터와 기능의 집합을 제공하여 협력관계 간의 정보를 교환가능 하도록 하는 것이에요.
쉽게 말해, 한 시스템이 협력 시스템에 제공하는 것을 얘기해요. 

### REST API란?

REST 기반으로 서비스 API를 구현한 것이에요.
최근 OpenAPI는 대부분 REST API를 제공해요.  

### REST API의 특징

REST는 HTTP 표준을 기반으로 구현하므로, HTTP를 지원하는 프로그램 언어로 클라이언트, 서버를 구현할 수 있어요. 
즉, 확장성과 재사용성을 높아요.

### REST API 설계 기본 규칙

### REST API 설계 예시

## RESTful?

RESTful이란 REST 아키텍처 스타일을 기반으로 작성된 웹 서비스를 의미해요.
RESTful은 REST를 REST답게 쓰기 위한 방법으로, 표준이 아니에요.
즉, REST 원리를 따르는 시스템은 RESTful이란 용어로 지칭해요.

### RESTful의 목적

### RESTful 하지 못한 경우

<br>

## 진짜 궁금한 것

### 질문

마이페이지 조회라 할때 

[GET] /api/v1/users/[id}로 해야할까 

[GET] /api/v1/users/me로 해야할까
# q1_http의 특징과 각각에 대해 간략히 설명해 주세요

우선 개념에 대해 알아볼 필요가 있어요.

<br>

## **HTTP란?**

HTTP Hyper Text Transfer Protocol의 약자로, 인터넷 프로토콜 스택의 4걔층 중 애플리케이션 계층의 프로토콜이에요. 

인터넷 프로토콜 스택이라는 말과, 프로토콜이라는 말을 더 정확히 들어가보죠.

## **Protocol**

컴퓨터 사이에 원활한 데이터 통신을 하기 위해 필요한 통신 규약이에요.

이때, 하나의 프로토콜의 설계로 데이터 송수신의 문제를 해결 될 수 없기에 프로토콜 스택은 아래와 같이 4가지의 영역별로 세분화 돼요. 

![https://user-images.githubusercontent.com/48986787/115721682-406b8e80-a3b9-11eb-9e9f-5f98194d96cc.png](https://user-images.githubusercontent.com/48986787/115721682-406b8e80-a3b9-11eb-9e9f-5f98194d96cc.png)

- 인터넷 프로토콜 스택 4계층

그러고 이러한 계층들의 과정을 도식화 해보면 아래와 같아요.

![https://user-images.githubusercontent.com/48986787/115734029-5c286200-a3c4-11eb-9dd5-04ea8fa24a11.png](https://user-images.githubusercontent.com/48986787/115734029-5c286200-a3c4-11eb-9dd5-04ea8fa24a11.png)

> **하나씩 생겨난 이유에 대해 다뤄볼 거에요.**

<br>

## **네트워크 인터페이스 계층**

![https://user-images.githubusercontent.com/48986787/115722757-431ab380-a3ba-11eb-850e-8948db824678.png](https://user-images.githubusercontent.com/48986787/115722757-431ab380-a3ba-11eb-850e-8948db824678.png)

집에 컴퓨터가 두대가 있어요. 저는 두대의 컴퓨터를 연결해서 통신을 해보려해요. 

집에 케이블을 찾아, 둘울 연결했어요. 

![https://user-images.githubusercontent.com/48986787/115723357-de138d80-a3ba-11eb-86b4-c049859b5af4.png](https://user-images.githubusercontent.com/48986787/115723357-de138d80-a3ba-11eb-86b4-c049859b5af4.png)

이제 이 컴퓨터들은 통신이 가능해요

![https://user-images.githubusercontent.com/48986787/115723540-09967800-a3bb-11eb-9570-81ab8fa28875.png](https://user-images.githubusercontent.com/48986787/115723540-09967800-a3bb-11eb-9570-81ab8fa28875.png)

![https://user-images.githubusercontent.com/48986787/115723711-35b1f900-a3bb-11eb-9cc0-9cfa67eb45c5.png](https://user-images.githubusercontent.com/48986787/115723711-35b1f900-a3bb-11eb-9cc0-9cfa67eb45c5.png)

이때 사용되는 계층이 `네트워크 인터페이스 계층`이에요 (Lan 드라이버, 장비)

저는 여기서 만족하지 않고, 해외에 사는 친구에게 메세지를 보내고 싶어졌어요. 

하지만 저는 몇백,천km의 케이블을 가지고 있지 않아요.

### **이때 인터넷이 필요해요.**

![https://user-images.githubusercontent.com/48986787/115724494-f506af80-a3bb-11eb-8479-ec41fb01579b.png](https://user-images.githubusercontent.com/48986787/115724494-f506af80-a3bb-11eb-8479-ec41fb01579b.png)

인터넷은 케이블의 역할을 대신해요. 

<br>

## **인터넷 계층**

그림상으로는 간단하게 보이지만, 실제 내부는 굉장히 복잡해요.

![https://user-images.githubusercontent.com/48986787/115726261-8a567380-a3bd-11eb-8a5e-c9a23577f712.png](https://user-images.githubusercontent.com/48986787/115726261-8a567380-a3bd-11eb-8a5e-c9a23577f712.png)

인터넷은 이렇게 수많은 노드들이 이어져있기 때문에. 친구에게 메시지를 보내려면 최소한의 규칙이 있어야해요.

(노드들에게 정보를 주면서, 받은 노드가 다음 노드에게 전달)

이때 사용하는 것이 IP(Internet Protocol)예요.

![https://user-images.githubusercontent.com/48986787/115726769-fc2ebd00-a3bd-11eb-9b5e-4c3530f0a834.png](https://user-images.githubusercontent.com/48986787/115726769-fc2ebd00-a3bd-11eb-9b5e-4c3530f0a834.png)

### IP(Internet Protocol)

지정한 IP 주소(목적지 주소)에 데이터를 전달해요.

패킷이라는 통신 단위로 데이터를 전달해요.

### **IP 패킷**

그림으로 살펴보면 `출발지 IP, 목적지 IP, 기타 등이 포함되며, 전송할 데이터가` 포함돼요. 

![https://user-images.githubusercontent.com/48986787/115728372-6d22a480-a3bf-11eb-8ef3-27256954af1b.png](https://user-images.githubusercontent.com/48986787/115728372-6d22a480-a3bf-11eb-8ef3-27256954af1b.png)

친구 컴퓨터의 IP를 알고, 전송할 데이터 준비를 했고, 어떻게 보낼지 결정(통신 단위)했어요,

즉, 해외의 친구에게 메세지를 보낼 준비가 완료됐어요

### **클라이언트 패킷 전달**

![https://user-images.githubusercontent.com/48986787/115730522-57ae7a00-a3c1-11eb-9cbd-bac76cdc0da0.png](https://user-images.githubusercontent.com/48986787/115730522-57ae7a00-a3c1-11eb-9cbd-bac76cdc0da0.png)

### **서버 패킷 전달**

![https://user-images.githubusercontent.com/48986787/115731690-4b76ec80-a3c2-11eb-95c7-fa23366d5867.png](https://user-images.githubusercontent.com/48986787/115731690-4b76ec80-a3c2-11eb-95c7-fa23366d5867.png)

Hello를 보냈고, Yes라는 답변을 받았어요.

간단히 보면 정말 편리해보이는데, 아쉽게도 IP프로토콜엔 많은 한계가 있어요.

### **IP 프로토콜의 한계**

**비연결성:**

패킷을 받을 대상이 없거나 서비스 불능 상태여도 패킷을 전송해요

**비신뢰성**:

중간에 패킷이 사라지거나, 순서대로 오지 않아도 체크할 수 없어요.

**프로그램 구분:**

같은 IP를 사용하는 서버에서 통신하는 애플리케이션이 둘 이상이면 어떠한 서비스가 목적지인지 알 수 없어요.

> 이러한 IP 프로토콜의 한계를 해결하는 방법이 TCP에요

<br>

## **전송 계층**

### **TCP ( Transmission Control Protocol )**

TCP는 전송 제어 프로토콜이라 불러요.

특징으로는 `연결지향( TCP 3way handshake(가상연결))형 프로토콜이며, 데이터 전달을 보증하고, 순서를 보장`해요.

신뢰할 수 있는 프로토콜이라 불러요. 

### **TCP/IP 패킷**

![https://user-images.githubusercontent.com/48986787/115735546-86c6ea80-a3c5-11eb-9a6c-04e725dbb9f9.png](https://user-images.githubusercontent.com/48986787/115735546-86c6ea80-a3c5-11eb-9a6c-04e725dbb9f9.png)

### 데이터 전달 보증, 순서를 보장 할 수 있는 이유는 3Way handshake과정이 있기 때문이에요.

TCP 3 way handshake는 실제로 연결된게 아니에요. 연결이 됐나보다~처럼 논리적으로 연결이 된 것이에요.(물리적으로 연결된 랜선이랑은 느낌이 다름)

<br>

## **애플리케이션 계층**

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

### **예제**

![https://user-images.githubusercontent.com/48986787/115749822-7701d300-a3d2-11eb-8d2f-0cb430349df3.png](https://user-images.githubusercontent.com/48986787/115749822-7701d300-a3d2-11eb-8d2f-0cb430349df3.png)

<br>

## **Refer**

- [모든 개발자를 위한 HTTP 웹 기본 지식](https://www.inflearn.com/course/http-%EC%9B%B9-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC/dashboard)
- [https://www.w3.org/Protocols/rfc2616/rfc2616-sec4.html](https://www.w3.org/Protocols/rfc2616/rfc2616-sec4.html)
- [https://www.w3.org/Protocols/rfc2616/rfc2616.html](https://www.w3.org/Protocols/rfc2616/rfc2616.html)


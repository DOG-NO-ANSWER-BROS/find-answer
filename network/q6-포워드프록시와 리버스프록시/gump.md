# 정리

> 프록시 서버는 리소스를 요청하는 클라이언트와 해당 리소스를 제공하는 서버 사이에서 중계자 역할을 하는 서버 애플리케이션이에요.
> 

클라이언트는 파일이나 웹 페이지와 같은 리소스를 응답할 수 있는 서버에 직접 요청(request)하는 대신 요청을 처리하고 네트워크 트렌잭션을 수행하는 프록시서버로 요청해요. 
이는 요청의 복잡성을 단순화 또는 제어하고, 로드 밸런싱, 보안 과 같은 추가 이점을 제공해요.

![https://user-images.githubusercontent.com/48986787/136121474-dde12d06-7359-44d3-b51e-a09d61e182bc.png](https://user-images.githubusercontent.com/48986787/136121474-dde12d06-7359-44d3-b51e-a09d61e182bc.png)

## 프록시 종류

 프록시 서버는 사용자의 로컬 컴퓨터나 목적지 서버 사이에 있을 수 있어요. 수정되지 않은 요청과 응답을 전달하는 프록시 서버를 일반적으로 게이트웨이, 또는 터널링 프록시라고 해요.
포워드 프록시는 데이터를 검색하는 데 사용되는 인터넷 연결 프록시에요. 
리버스 프록시는 일반적으로 개인 네트워크의 서버에 대한 연결을 제어하고 보호하기 위해 사용되는 내부 프록시에요. 리버스 프록시는 일반적으로 로드 밸럭싱, 인증, 암호 해독 및 캐싱과 같은 작업도 수행해요. 

### 포워드 프록시(Forward proxy)

![https://user-images.githubusercontent.com/48986787/137041289-15dc4a67-7611-4040-a250-8658c598ec21.png](https://user-images.githubusercontent.com/48986787/137041289-15dc4a67-7611-4040-a250-8658c598ec21.png)

포워드 프록시는 모든 인터넷 사용자가 엑세스할 수 있는 전달 프록시 서버에요. 

![https://user-images.githubusercontent.com/48986787/137041476-677f5f7c-8c24-4672-8952-a5b59b89922a.png](https://user-images.githubusercontent.com/48986787/137041476-677f5f7c-8c24-4672-8952-a5b59b89922a.png)

포워드 프록시는 클라이언트 또는 클라이언트 그룹에 프록시 서비스를 제공해요. (그림에서는 클라이언트 그룹에서 프록시 서비스를 제공하는 것이에요)

동작 **과정**

그림에서의 클라이언트 중 하나가 인터넷에서 파일 전송 서버(File Transfer Server)에 연결을 시도하면 해당 요청이 먼저 정방향 프록시를 통과해야해요.
포워드 프록시의 설정에 따라 요청을 허용하거나 거부할 수 있어요. 요청이 허용되면 방화벽으로 전달된 다음 파일 전송 서버로 전달돼요. 
파일 전송 서버에서는 요청을 보낸 클라이언트가 아닌 포워드 프록시에 대한 응답을 처리해요. 
포워드 프록시는 서버의 응답을 요청한 클라이언트에게 전달해요. 

**포워드 프록시 사용이유**

포워드 프록시 서버는 Cache 를 사용하여 자주 사용하는 데이터라면 요청을 보내지 않고 캐시에서 가져올 수 있어요.
IP우회를 사용하여 클라이언트의 IP를 추적하기 어렵게 만들 수 있어요.

클라이언트 컴퓨터가 내부 네트워크에서 찾을 수 있는 유일한 컴퓨터는 아니에요. 즉, 외부 클라이언트에 서비스를 제공해야 하는 경우에는 포워드 프록시를 사용하는 것이 더 적절해요. 

### 리버스 프록시(Reverse proxy)

![https://user-images.githubusercontent.com/48986787/137041326-c2f2ca2b-4902-415e-8159-3158c6dc4d40.png](https://user-images.githubusercontent.com/48986787/137041326-c2f2ca2b-4902-415e-8159-3158c6dc4d40.png)

`포워드 프록시`는 클라이언트(또는 요청 호스트)를 대신하여 프록시하는 반면 `리버스 프록시`는 서버를 대신하여 프록시해요. 리버스 프록시는 아래와 같이 뒤에 있는 서버를 대신하여 외부 클라이언트의 요청을 수락해요.
즉. 프록시 서버의 응답은 호스트에서 직접 온 것 처럼 반환되어 클라이언트는 호스트에 대해 알지 못해요. 
주로 하나 이상의 웹 서버 환경에 사용돼요. 

![https://user-images.githubusercontent.com/48986787/137043384-8d6830e8-3b74-4264-9495-4b4259fca33d.png](https://user-images.githubusercontent.com/48986787/137043384-8d6830e8-3b74-4264-9495-4b4259fca33d.png)

리버스 프록시의 사용이유는 아래와 같아요 

1. **로드밸런싱(Load Balancing):**
    
     ****하나의 프록시로 다수의 서버 요청을 처리하며, 1개의 IP로 다수의 웹서비스를 수행할 수 있어요. 사용자들의 요청을 필요한 Application Server에 분산시킬 수 있으며. 서버가 다운되면 로드 밸러서는 트래픽을 나머지 온라인 서버로 Redirection 해요.
    
2. **압축(Compression):**
    
    인바운드와 아웃바운드 데이터를 압축할 수 있으며, 일반적으로 요청되는 콘텐츠를 캐시하여 클라이언트와 서버 간의 트래픽 흐름을 가속화 할 수 있어요. 또한 SSL 암호화와 같은 추가 태스크를 수행하여 웹 서버의 로드를 제거할 수 있으므로 이에 따라 성능이 개선돼요.
    
3. **암호화/SSL 가속:** 
    
    보안이 적용된 웹사이트가 생성될 때, SSL 암호화는 웹 서버 자체가 아닌 SSL 가속 하드웨어가 장착된 리버스 프록시에 의해 수행되는 경우가 많아요. 
    또한 호스트는 단일 "SSL 프록시"를 제공하여 임의의 수의 호스트에 대해 SSL 암호화를 제공할 수 있으므로 각 호스트에 대해 별도의 SSL 서버 인증서를 구성하지 않아도 돼요.하지만 이는 SSL 프록시 뒤에 있는 모든 호스트가 공유해야 하는 단점이 있어요.
    
4. **Security and anonymity:**
    
    내부망으로 향하는 요청을 차단함으로써, 내부망의 신원이나 보안 공격에 대한 추가적인 방어역할을 할 수 있어요.
    
5. **정적 콘텐츠 캐싱:**
    
    그림 및 기타 정적 그래픽 콘텐츠와 같은 정적 콘텐츠를 캐싱하여 웹 서버의 부담을 줄일 수 있어요.
    

# 차이점

## 1. End Point

Forward Proxy 는 클라이언트가 요청하는 End Point 가 **실제 서버 도메인**이고 프록시는 둘 사이의 통신을 담당해요.
Reverse Proxy 는 클라이언트가 요청하는 End Point 가 **프록시 서버의 도메인**이고 실제 서버의 정보는 알 수 없어요.

## 2. 감춰지는 대상

Forward Proxy 는 **클라이언트가 감춰져요.** 요청 받는 서버는 포워드 프록시 서버를 통해서 요청을 받기 때문에 클라이언트의 정보를 알 수 없어요. 
Reverse Proxy 는 반대로 **서버가 감춰져요.**  클라이언트는 리버스 프록시 서버에게 요청하기 때문에 실제 서버의 정보를 알 수가 없어요.

## Refer

- [https://en.wikipedia.org/wiki/Proxy_server](https://en.wikipedia.org/wiki/Proxy_server)

<br> 

# 질문

## 나만 알만한 것

### 질문

포워드 프록시와 리버스 프록시, 언제써야할까?! 

### 답변

감춰지는 대상이 클라이언트라면 포워드 프록시를 구축하고, 서버를 감춰야 한다면 리버스 프록시를 구축해요.
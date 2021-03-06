# 클라이언트와 소통할 때 CA의 역할

## CA
* 인증 기관, 웹 사이트, 이메일 주소, 회사 또는 개인과 같은 엔티티의 신원을 확인하고 전자 문서를 발행하여 암호화 키에 바인딩하는 회사 또는 조직이다.
* 기업이나 단체별로 운영하는 사설 인증기관도 있고, 상업 목적 또는 무료로 인증을 해주는 기관도 있다.
* 이런 인증 기관은 공개키 인증서를 발급한다. 그 인증서를 통해 공개 키가 특정 서버나 단체에 속해 있다는 것을 알 수 있다.

## 인증서를 사용하는 이유
* 네트워크를 통해 보내는 패킷이 라우터와 스위치를 거쳐 갈 때, 스니핑 방식을 통해 누군가가 가로챌 수 있다.
![Screen Shot 2021-10-15 at 4 19 29 AM](https://user-images.githubusercontent.com/48251668/137381862-73e9beb4-533f-40ac-949f-804a59280566.png)
* 따라서 민감한 정보를 보호하기 위해 암호화를 하는데, 오늘 날 가장 널리 쓰이는 SSL/TLS 방식이 인증서를 암호화 과정에 사용한다
* 인증서는 '과연 특정 대상이 신뢰할 수 있는 존재인가'를 나타낸다.

#### 클라이언트가 서버와 암호화된 통신을 하기 위해서는 서버측이 보낸 인증서를 클라이언트가 확인하는 과정이 있어야한다.
물론 클라이언트가 그 방법을 알 필요는 없다. 그렇다면 어떻게 그 확인 과정이 이루어질까? 🤔

## 인증서를 확인하는 과정
CA는 **'믿을만한 3자** 역할이다. 즉, 클라이언트가 서버와 암호화된 통신을 하려고 할 때, 믿을만한 3자인 CA를 통해 그 서버가 믿을만한지 알아내는 것이다. 방법은 다음과 같다:<br>
![Screen Shot 2021-10-15 at 4 39 10 AM](https://user-images.githubusercontent.com/48251668/137384384-637b990a-220c-460c-9e49-06b2aceb908f.png)
* CA는 본인만의 고유한 비밀키를 가지고 있고, 이에 대응하는 공개 키를 준비해 놓는다.
* 해당 공개키를 클라이언트가 사용하는 브라우저에 내장한다.
* 서버(회사)는 본인만의 인증서를 만든다.
* **해당 인증서를 서버(회사)는 CA 측에 보내서 CA의 비밀키로 암호화 해달라고 요청한다**
* 서버(회사)는 클라이언트에게 그렇게 암호화된 인증서를 보내고, 클라이언트는 브라우저에 내장된 공개키를 이용하여 복호화한다.
* 그렇게 클라이언트는 서버를 믿을 수 있는 존재라고 판단한다.

#### 이렇게 만들어지는 인증서를 '계층 구조'로 되어 있다고 표현한다.
CA가 만드는 인증서를 Root 인증서라고 하고, 이러한 인증서는 일반적으로 웹 브라우저에 미리 내장되어 있고, 해당 인증서에 대응하는 공개키도 함께 포함되어 있다.

#### CA를 통해 서버(회사)가 자신의 인증서를 만드는 것을 Chain of Trust 라고 한다.
CA의 인증서가 믿을만한 것이라면 해당 인증서의 비밀 키로 암호화된 하위 인증서 역시 믿을만하다고 보는 것이다.

## 질문
### Intermediate CA란 무엇일까?
CA가 아닌, 앞서 말한 CA의 root 인증서를 이용하여 자신만의 인증서를 만든 회사가 믿을만한 존재로 CA 역할을 할 때, 그 회사를 Intermediate CA (ICA)라고 한다.<br>
즉, Google 과 같은 회사는 믿을만한 회사라서 브라우저의 CA 인증서 목록에 Google의 것도 함께 등록되어 있다.

개인 또는 서버(회사)가 인증서를 발급 받기 위해서는 Root 인증서가 아닌 이런 ICA에 인증서의 사인을 요청하게 된다.
그렇게 ICA의 비밀 키를 이용해 개인의 인증서를 암호화 하고, 브라우저에 내장된 ICA의 공개키를 이용하여 해당 인증서를 복호화 할 수 있다.

<img width="494" alt="Screen Shot 2021-10-15 at 4 56 09 AM" src="https://user-images.githubusercontent.com/48251668/137386692-827f1bcd-d534-49ad-a7d1-1c69d23b4162.png">
<img width="500" alt="Screen Shot 2021-10-15 at 4 56 18 AM" src="https://user-images.githubusercontent.com/48251668/137386707-1b90d091-1474-4d0b-ac99-df4473b8ed6a.png">
<img width="493" alt="Screen Shot 2021-10-15 at 4 56 34 AM" src="https://user-images.githubusercontent.com/48251668/137386717-84b4e5b4-64b2-4732-9761-dc6bc873bf00.png">



#### 굳이 왜 ICA가 존재하는걸까?
개인 또는 서버(회사)가 그냥 CA에게 인증서 사인을 요청하면 안되나?

해당 레이어가 추가로 존재하는 이유는, 인증을 수행하는 기관의 인증서가 손상되거나 비밀키가 유출될 경우, 이를 철회할 수 있도록 하기 위해서이다.<br>
즉, 비밀키가 유출되거나 다른 보안적인 문제가 생겼을 때 Root 인증서를 사용하는 모든 인증서를 철회할 필요 없이, 관련된 intermediate 인증서로 사인한 인증서만 철회하면 된다.<br>
보안 레이어를 한 단계 추가하는 것이다.

# 정리
## SSL부터
SSL 통신은 Secure Sokect Layer로, TCP/UDP 계층의 패킷 교환을 암호화하여 네트워크 상의 보안을 강화하는 프로토콜이다.

그 방식은 암호화와 복호화를 할 수 있는 대칭키를 생성하고, 이 대칭키를 RSA방식으로 전달하여 통신상의 보안을 확보하는 식으로 이루어진다.
RSA는 공개키/비공개키로 이루어져 있어 공개키 방식이라고도 불린다.
공개키 방식은 대칭키에 비해 안전하지만, 대칭키에 비해 계산과정이 복잡하다. 그래서 대칭키를 전달할 때 공개키 방식을 사용하고, 실제로 암호화와 복호화는 대칭키로 수행하는 방식을 사용한다.

https는 http프로토콜에 ssl을 적용한 방식이다.

## HTTPS의 통신과정

### 1. 서버 - 인증 서명 요청서(CSR) 생성

1. Server에서 Public Key와 Private Key를 쌍으로 생성한다 
2. Server에서 '인증 서명 요청서(CSR)'를 만든다. ⇒ SHA256과 같은 해쉬 알고리즘으로 해쉬 수행
  - 국가 코드, 도시, 회사명, 부서명, 이메일, 도메인 주소 등이 들어간다.
3. Server의 Public Key도 넣는다.
4. `Hash(Server에_대한_정보)`를 CA에 전달

### 2. CA - SSL 인증서 발급

1. CA에 '인증 서명 요청서(CSR)(=`Hash(Server에_대한_정보)`)'을 보내면, CA가 인증 서명 요청서(CSR)에 **CA의 Private Key**를 통해 **전자서명**(이 때는 '암호화'라고 표현하지 않고, '전자 서명'이라고 표현한다.)를 한 형태인 **SSL 인증서**를 발급한다. 
2. `CA의_Private_key로_암호화(Hash(Server에_대한_정보))`(= SSL 인증서)를 Server에 다시 전달


### 3. 서버 - SSL 인증서를 받음

> Server는 CA로부터 암호화된 SSL 인증서를 받는다.

### 4. 이후 과정은 아래와 같다.
![image](https://user-images.githubusercontent.com/51393021/137821517-fce1b6ec-dcfb-40f6-95e2-b2bc72048fac.png)

# 질문
## CA가 필요한 이유가 무엇일까?
- CA는 서버에 접속하려하는 클라이언트가 자신이 연결하려고 하는 서버가 맞는지 인증해주는 역할을 한다. http 통신 패킷의 암호화 라는 측면은 https를 통해 이룰 수 있지만, 내가 암호화한 패킷을 보내려는 서버가 '진짜 서버'인지는 알 수 없다. 공인 인증기관에서 확인하고 인증서를 발급함으로써 신뢰할 수 있는 서버라는 것을 확인하는 것이다.

## CA의 비밀키가 탈취되면 어떻게 될까?
- 모든 https 패킷은 감청가능하게 된다. 그래서 특정 인증기관의 비밀키가 탈취되어도 문제없도록, 2계층 혹은 3계층 구조를 띄고있다. 가장 상위에 위치한 인증기관인 Root CA, 중간기관역할을 수행하는 Intermediate CA. 서버는 중간기관에서 인증서를 발급받는다. 이러면 중간기관의 비밀키가 탈취 당해도 나머지 기관들은 안전하다.
- 서버가 중간기관에 정보를 제출하여 중간기관이 서명(암호화)하여 인증서를 생성하듯이, 중간기관도 자신의 공개키를 상위 기관에 제출하면 상위기관이 서명하여 인증서를 생성한다. 이를 인증서 체인이라 부른다.
- ![image](https://user-images.githubusercontent.com/51393021/137822889-d5c21e83-2702-4d7f-98c1-0120a823f1a3.png)


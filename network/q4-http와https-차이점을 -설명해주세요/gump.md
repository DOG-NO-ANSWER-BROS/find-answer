## Http

http란 Hyper Text transfer Protocol의 약자로, 인터넷 프로토콜 스택의 4계층 중 애플리케이션 계층의 프로토콜이에요.

여기서 Protocol은 컴퓨터 사이에 원활한 데이터 통신을 하기 위해 필요한 통신규약이에요.

80포트를 사용해요.

더 자세한 내용은  [[Network] http의 특징과 각각에 대해 간략히 설명해 주세요](https://livenow14.tistory.com/45) 여기 정리했어요.

## Https

HyperText Transfer Protocol Secure의 약자로 말그대로  HTTP에 데이터 암호화가 추가된 프로토콜이에요.

HTTPS는 HTTP와 다르게 433번 포트를 사용하며, 네트워크 상에서 중간에 제3자가 정보를 볼 수 없도록 공개키 암호화를 지원하고 있어요.

어플리케이션 계층과 트랜스포트 계층사이에 TLS(Transprot Layer Security) SSL가 존재하며 이 계층에서 보안을 담당해요

## Https가 생겨난 이유

HTTP는 평문 데이터를 기반으로 하기 때문에, 유저정보와 같은 민감한 정보가 인터넷 상에 그대로 노출돼요.

또한 http의 큰 특징은 connectless이기 때문에, 클라이언트를 정확히 판단할 수 없어요. 그래서 중간자 공격(Man-in-the-middle attack)을 할 수 있게 돼요.

이를 막기 위한 방법 중 하나가 바로 https예요.

## Refer

 [https://mangkyu.tistory.com/98](https://mangkyu.tistory.com/98)

## 질문

### 웹사이트 상에서 http와 https의 검색 필터차이
# 정리

> `인증 기관(Certificate Authority, CA)`은 디지털 인증서를 발행하는 주체예요.
> 

사용자가 접속한 사이트가 유효한 사이트인지 확인하는 역할을 해요. 

## 동작과정

![https://user-images.githubusercontent.com/48986787/138006127-b82fb463-cdb2-45df-b22c-0a119c5d72c0.png](https://user-images.githubusercontent.com/48986787/138006127-b82fb463-cdb2-45df-b22c-0a119c5d72c0.png)

먼저 사이트는, 사이트 인증서(인증기관이 사이트에게 발급해주는 문서)가 필요해요.
이를 발급받기 위해 인증기관에게 사이트 정보와, 사이트 공개키를 전달해요.

![https://user-images.githubusercontent.com/48986787/138006211-c5c1cb77-5d7b-4fc6-b51e-4ed088acce2f.png](https://user-images.githubusercontent.com/48986787/138006211-c5c1cb77-5d7b-4fc6-b51e-4ed088acce2f.png)

인증기관은 전달받은 데이터를 검증해요.

![https://user-images.githubusercontent.com/48986787/138006741-34baf408-2c9c-471d-bb3f-26f5a37d2037.png](https://user-images.githubusercontent.com/48986787/138006741-34baf408-2c9c-471d-bb3f-26f5a37d2037.png)

성공적으로 검증을 완료하면. 사이트 인증서 발급을 위해 인증기관의 비공개키로 서명(암호화)해요. 

![https://user-images.githubusercontent.com/48986787/138006903-5cd33de9-0a49-4843-8b56-503d678958c8.png](https://user-images.githubusercontent.com/48986787/138006903-5cd33de9-0a49-4843-8b56-503d678958c8.png)

서명이 완료되면, 사이트 인증서가 생성이 되고, 생성한 인증서를 사이트에게 전달해요 

![https://user-images.githubusercontent.com/48986787/138006984-6df8db33-750a-4ead-a68f-b0de3eae1daf.png](https://user-images.githubusercontent.com/48986787/138006984-6df8db33-750a-4ead-a68f-b0de3eae1daf.png)

인증된 사이트가 되었네요! 

![https://user-images.githubusercontent.com/48986787/138007102-f98238a0-0c5d-4876-9493-328d58d95ee4.png](https://user-images.githubusercontent.com/48986787/138007102-f98238a0-0c5d-4876-9493-328d58d95ee4.png)

클라이언트의 웹 브라우저에는 인증기관에서 발급한 인증서가 신뢰할 수 있는지를 확인하기 위해 '신뢰할 수 있는 인증서'(공개키)가 미리 등록되어 있어요. 
여기까지는, 아직 클라이언트가 사이트에 접속 요청을 보내기 전이에요. 

![https://user-images.githubusercontent.com/48986787/138007312-12a36daa-b20b-4f52-b46c-84925a061df5.png](https://user-images.githubusercontent.com/48986787/138007312-12a36daa-b20b-4f52-b46c-84925a061df5.png)

사용자가 사이트에게 접속 요청을 보내면, 사이트는 자신이 신뢰있는 사이트임을 증명하기 위해 사용자에게 사이트 인증서를 전달해요. 

![https://user-images.githubusercontent.com/48986787/138008398-71fd6373-4b77-432c-917c-fb50729e7a4b.png](https://user-images.githubusercontent.com/48986787/138008398-71fd6373-4b77-432c-917c-fb50729e7a4b.png)

사용자는 브라우저에 내장되어 있는 인증기관 공개키로, 사이트 인증서를 복호화 해서 검증해요. 
사이트 인증서를 해독하면, 사이트 정보와 사이트 공개키를 얻을 수 있어요.

<br> 

# 질문

## 나만 알만한 것

### 질문

중간에 인증서가 바뀌는 공격을 받았을 때 어떻게 확인할 수 있을까?

### 답변
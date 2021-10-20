# 정리

> `인증 기관(Certificate Authority, CA)`은 디지털 인증서를 발행하는 주체예요.
> 

사용자가 접속한 사이트가 유효한 사이트인지 확인하는 역할을 해요. 

## 동작과정 (그림 위주)

### 인증기관(CA)에 사이트 정보, 공개키 전달

![https://user-images.githubusercontent.com/48986787/138006127-b82fb463-cdb2-45df-b22c-0a119c5d72c0.png](https://user-images.githubusercontent.com/48986787/138006127-b82fb463-cdb2-45df-b22c-0a119c5d72c0.png)

먼저 사이트는, 사이트 인증서(인증기관이 사이트에게 발급해주는 문서)가 필요해요.
이를 발급받기 위해 인증기관에게 사이트 정보와, 사이트 공개키를 전달해요.

---

### 데이터 검증

![https://user-images.githubusercontent.com/48986787/138006211-c5c1cb77-5d7b-4fc6-b51e-4ed088acce2f.png](https://user-images.githubusercontent.com/48986787/138006211-c5c1cb77-5d7b-4fc6-b51e-4ed088acce2f.png)

인증기관은 전달받은 데이터를 검증해요.

---

### 서명(암호화)

![https://user-images.githubusercontent.com/48986787/138006741-34baf408-2c9c-471d-bb3f-26f5a37d2037.png](https://user-images.githubusercontent.com/48986787/138006741-34baf408-2c9c-471d-bb3f-26f5a37d2037.png)

성공적으로 검증을 완료하면. 사이트 인증서 발급을 위해 인증기관의 비공개키로 서명(암호화)해요. 

---

### 인증서 전달

![https://user-images.githubusercontent.com/48986787/138006903-5cd33de9-0a49-4843-8b56-503d678958c8.png](https://user-images.githubusercontent.com/48986787/138006903-5cd33de9-0a49-4843-8b56-503d678958c8.png)

서명이 완료되면, 사이트 인증서가 생성이 되고, 생성한 인증서를 사이트에게 전달해요 

---

### 인증된 사이트

![https://user-images.githubusercontent.com/48986787/138006984-6df8db33-750a-4ead-a68f-b0de3eae1daf.png](https://user-images.githubusercontent.com/48986787/138006984-6df8db33-750a-4ead-a68f-b0de3eae1daf.png)

인증된 사이트가 되었네요! 

---

### 인증기관 공개키 요청

![https://user-images.githubusercontent.com/48986787/138007102-f98238a0-0c5d-4876-9493-328d58d95ee4.png](https://user-images.githubusercontent.com/48986787/138007102-f98238a0-0c5d-4876-9493-328d58d95ee4.png)

클라이언트의 웹 브라우저에는 인증기관에서 발급한 인증서가 신뢰할 수 있는지를 확인하기 위해 '신뢰할 수 있는 인증서'(공개키)가 미리 등록되어 있어요. 
여기까지는, 아직 클라이언트가 사이트에 접속 요청을 보내기 전이에요. 

---

### 사이트 접속요청

![https://user-images.githubusercontent.com/48986787/138007312-12a36daa-b20b-4f52-b46c-84925a061df5.png](https://user-images.githubusercontent.com/48986787/138007312-12a36daa-b20b-4f52-b46c-84925a061df5.png)

사용자가 사이트에게 접속 요청을 보내면, 사이트는 자신이 신뢰있는 사이트임을 증명하기 위해 사용자에게 사이트 인증서를 전달해요. 

---

### 인증서 복호화

![https://user-images.githubusercontent.com/48986787/138029800-6e6c9c0a-8aa7-4e36-97cb-39a24aeeda82.png](https://user-images.githubusercontent.com/48986787/138029800-6e6c9c0a-8aa7-4e36-97cb-39a24aeeda82.png)

사용자는 브라우저에 내장되어 있는 인증기관 공개키로, 사이트 인증서를 복호화 해서 검증해요. 
사이트 인증서를 해독하면, 사이트 정보와 사이트 공개키를 얻을 수 있어요.

---

### 대칭키 생성 (g^A mod p)

![https://user-images.githubusercontent.com/48986787/138030840-aeda6d75-a1a2-434a-bfac-c2c8b3b0cb5b.png](https://user-images.githubusercontent.com/48986787/138030840-aeda6d75-a1a2-434a-bfac-c2c8b3b0cb5b.png)

사용자는 자신의 `비공개키 A`로 `대칭키(g^A mod p)`를 생성해요. 이때 사용하는 알고리즘은 `디피-헬만 알고리즘`이에요.
생성한 `대칭키`와 `자신의 공개키`를 `사이트 공개키`로 감싸요. 

---

### 대칭키, 공개키 전달

![https://user-images.githubusercontent.com/48986787/138030998-af29f4e5-0b76-4fdb-b0cc-1859e242c1ba.png](https://user-images.githubusercontent.com/48986787/138030998-af29f4e5-0b76-4fdb-b0cc-1859e242c1ba.png)

이렇게 사용자가  `사이트 공개키`로 감싸진 정보를 사이트로 보내면, 사이트는 `자신의 비공개키 B`로 사용자에게서 받은 정보를 복호화해요. 
사이트는 `사용자의 대칭키`, `사용자 공개키`를 얻어요. 

---

### 대칭키 생성 (g^B mod p)

![https://user-images.githubusercontent.com/48986787/138032156-ca8efe36-ddb3-47bf-865d-695b264e8511.png](https://user-images.githubusercontent.com/48986787/138032156-ca8efe36-ddb3-47bf-865d-695b264e8511.png)

사이트도 사용자와 마찬가지로, 자신의 `비공개키 B`를 이용해서 `대칭키(g^B mod p)`를 만들어요.
생성한 `대칭키`와 `자신의 공개키`를 `사용자 공개키`로 감싸요. 

---

### 대칭키, 공개키 전달

![https://user-images.githubusercontent.com/48986787/138033714-51031206-9799-4f41-ba73-ec2c53b74c2c.png](https://user-images.githubusercontent.com/48986787/138033714-51031206-9799-4f41-ba73-ec2c53b74c2c.png)

사이트는 `사용자 공개키`로 감싸진 `대칭키`와 `자신의 공개키`를 사용자에게 전달해요. 

---

### 대칭키 할당

![https://user-images.githubusercontent.com/48986787/138034412-347debf3-7622-44ed-9717-3326989f5914.png](https://user-images.githubusercontent.com/48986787/138034412-347debf3-7622-44ed-9717-3326989f5914.png)

이제 사용자, 사이트는 각각 `사이트의 대칭키(g^B mod p)`, `사용자의 대칭키(g^A mod p)`를 가지게 되었네요!

---

### 새로운 대칭키 생성(g^AB mod p)

![https://user-images.githubusercontent.com/48986787/138038751-133b6673-bc2a-4b88-8ed6-2b6036c846f8.png](https://user-images.githubusercontent.com/48986787/138038751-133b6673-bc2a-4b88-8ed6-2b6036c846f8.png)

사용자는 개인키 A를 사용해서 대칭키 `g^AB mod p`를 생성하고, 사이트는 개인키 B를 사용해서 대칭키 `g^AB mod p`를 생성합니다.
A와 B는 새로 생성한 이 대칭키로`(g^AB mod p`)를 정보를 교환해요.

---

### 새로운 대칭키에 의한 정보 전달

![https://user-images.githubusercontent.com/48986787/138042292-9b79f2fa-a031-44f5-a90e-4354fd2125ad.png](https://user-images.githubusercontent.com/48986787/138042292-9b79f2fa-a031-44f5-a90e-4354fd2125ad.png)

각각의 비공개키로 만들어진 대칭키라, 안전한 정보 교환이 가능하겠네요! 

---

## 질문 - 대칭키가 해커에 의해 해킹 당할 확률은?

이는 우테코 3기 크루, `다니`가 설명해준 것으로 마무리 할게요. 현재 제 안목에서 가장 명확하다 생각이 드네요 :) 

![https://user-images.githubusercontent.com/48986787/138043424-97622ce5-96f0-447a-a3f1-da9b903c020a.png](https://user-images.githubusercontent.com/48986787/138043424-97622ce5-96f0-447a-a3f1-da9b903c020a.png)

## Refer

- [https://m.blog.naver.com/alice_k106/221468341565](https://m.blog.naver.com/alice_k106/221468341565)
- [다니의 테코톡](https://www.youtube.com/watch?v=wPdH7lJ8jf0)
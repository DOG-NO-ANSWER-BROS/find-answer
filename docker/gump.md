# 정리

## 도커(Docker)?

> 컨테이너 기반 가상화 도구예요.

일반적으로 도커라 하면 `도커 엔진`, 또는 도커에 관련된 모든 프로젝트를 의미해요. 보통 도커 엔진이라는 의미로 많이 사용되는데, 도커 엔진은 `컨테이너를 생성하고 관리하는 주체`로서 이 자체로도 컨테이너를 제어하고, 다양한 기능을 제공하는 도커의 주 프로젝트이기 때문이에요. 

여기서 컨테이너란 말이 어려워요.

### 컨테이너(Container)

사전적 의미로 컨테이너는 어떤 물체를 격리하는 공간을 뜻해요.

하지만 개발적 의미로는 `이미지의 목적에 맞는 파일이 들어있는 파일시스템과 격리된 시스템 자원 및 네트워크를 사용할 수있 는 독립된 가상공간`이라고 해요.  즉, 이미지의 목적에 따라 생성되는 프로세스 단위의 격리 환경이에요. 호스트 입장에서는 격리된 환경을 가진 프로세스라 볼 수 있어요.

여기서 `이미지`는 컨테이너를 생성할 때 필요한 요소이며, 가상 머신을 생성할 때 사용하는 iso 파일과 비슷한 개념이예요.

<br>

## 가상머신과 도커 컨테이너

![https://user-images.githubusercontent.com/48986787/123285892-698ad580-d548-11eb-8968-e87462a4d6b1.png](https://user-images.githubusercontent.com/48986787/123285892-698ad580-d548-11eb-8968-e87462a4d6b1.png)

기본적으로 로컬 컴퓨터(제 컴퓨터는 mac에요)에서 어떠한 App을 사용한다면, 컴퓨터의 OS를 공유하는 App이 올라가게돼요.  

하지만 갑자기 윈도우에서 App을 사용하고 싶다면 어떻게 할까요? 멀티 부트 등의 기술을 사용할 수 있지만, 하나의 `Host Os`를 공유하며 즉, mac os가 부팅된 상태에서 window를 사용하고싶거나 다른 os를 사용하고 할수 있게 `하이퍼바이저`라는 기술이 등장해요.

### 하이퍼바이저

가상화 기술을 통해 여러 개의 운영체제를 하나의 `호스트os`에 생성해서 사용할 수 있게하는 소프트웨어예요. 이러한 여러 개의 운영체제는 `가상 머신`이라는 단위로 구별되고, 각 가상머신에는 여러 운영체제가 설치되어 사용돼요. `하이퍼바이저`에 의해 생성되고 관리되는 운영체제는 `게스트 운영체제(Guest Os)`라 하며, 각 게스트 운영체제는 다른 게스트 운영체제와는 완전히 독립된 공간과 시스템 자원을 할당받아 사용해요. 대표적인 가상화 툴로 VirtualBox, VMware등이 있어요 

### 가상머신의 단점

각종 시스템 자원을 가상화하고 독립된 공간을 생성하는 작업은 하이퍼바이저를 반드시 거치기 때문에 일반 호스트에 비해 성능의 손실이 발생해요. 또한 가상 머신은 `게스트 운영체제`를 사용하기 위한 라이브러리, 커널 등을 전부 포함하기 때문에 가상 머신을 배포하기 위한 이미지로 만들었을 때 크기 또한 커져요. 

즉, 가상 머신은 완벽한 운영체제를 생성할 수 있는 장점은 있지만 성능이 느리고, 용량상으로 부담이 있어요. 

### 도커 컨테이너의 등장

도커 컨테이너는 가상화된 공간을 생성하기 위해 리눅스 자체 기능인 chroot, 네임스페이스, cgroup을 사용함으로써 프로세스 단위의 격리 환경을 만들기 때문에 성능 손실이 거의 없어요. 

컨테이너에 필요한 커널은 `host os의 커널`을 공유하여 사용하고, 컨테이너 안에는 애플리케이션을 구동하는 데 필요한 라이브러리 및 실행 파일만 존재하기 때문에 이미지로 만들었을 때 이미지의 용량 또한 가상 머신에 비해 대폭 줄어들어요. 따라서 이미지를 만들어 배포하는 시간이 가상 머신에 비해 빠르며, 가상화된 공간을 사용할 때의 성능 손실도 거의 없다는 장점이 있어요. 

도커의 컨테이너는 host os의 커널 공유하기에 컨테이너 자체에 특별한 권한을 주면,  host os의 자원을 사용할 수 있어요. 이 때문에 일반적으로 컨테이너 내부에서 수많은 소프트웨어를 설치하고 설정 파일을 수정해도 host os에게 영향을 끼치지 않아요.

> 잠깐, 여기서 커널이란?

커널(Kernel)은 컴퓨터 하드웨어와 프로세스를 잇는 핵심 인터페이스예요. 그리고 두 가지 관리 리소스 사이에서 최대한 효과적으로 통신해요.

커널이라는 이름은 단단한 껍질 안의 씨앗처럼 OS 내에 위치하고 전화기, 노트북, 서버 또는 컴퓨터 유형에 관계없이 하드웨어의 모든 주요 기능을 제어하기 때문에 붙은 이름이에요.

커널의 주요 기능은 `컴퓨터에 속한 자원들에 대한 접근을 중재`하는 것이예요. 커널은 운영 체제의 핵심 부분이므로, `커널의 역할 역시 운영 체제의 핵심 역할`이라 할 수 있어요. 

<br>

## Refer

시작하세요, 도버/쿠버네티스 

<br> 

# 질문

## 나만 알만한 것

### 질문

chroot란?

### 답변

chroot는 Change Root Directory의 줄임말로 명령어 자체가 루트 디렉터리를 바꾼다는 뜻을 가지고 있음.
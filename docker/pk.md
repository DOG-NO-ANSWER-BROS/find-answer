## 도커

### 가상환경
* 여러 개의 운영체제를 하나의 호스트에서 생성해 사용하는 방식. -> 하나의 운영체제 : 가상 머신
* 독립된 공간이 만들어지지만, 시스템 자원 역시 독립된다.

### 왜 docker를 사용할까?
- 개발과 배포의 편리함: 독립되어 있으면서도 쉽게 복제 가능
    * '도커 이미지'라는 일종의 패키지를 운영서버에 전달, 의존성을 걱정할 필요 없음.
    * 컨테이너는 호스트 OS와 격리된 독립된 개발환경이다.

- 독립성과 확장성 상승:
    * 모놀리스가 아닌 마이크로서비스 환경에서 도커를 많이 사용한다.
    * 마이크로서비스를 구성하는 독립된 모듈에 적합한 기술이기 때문.
        * 모듈 별 독립된 환경을 제공하고, 모듈을 빠르게 추가하거나 삭제할 수 있다.

### 특징

* 리눅스 자체 기능인 **chroot, namespace, 그리고 cgroup**을 사용하여 프로세스 단위로 독립적인 환경을 만든다. 즉, 성능 저하가 거의 없다.
* 호스트 OS와 커널을 공유한다.
* 컨테이너 안에는 애플리케이션을 구동할 때 필요한 라이브러리와 실행파일만 존재한다. 따라서 이미지 용량이 매우 가볍고, 배포 시간이 빠르다.

## 도커 이미지
* 모든 컨테이너는 이미지를 기반으로 생성된다.
* 가상 머신을 생성할 때 사용하는 iso 파일과 비슷한 개념이다.
* 여러 개의 계층으로 된 바이너리 파일로 존재하고, 컨테이너를 생성하고 실행할 때 읽기 전용으로 사용된다.

### 기본 구조
```shell
pkeugine/ubuntu:14.04
-> [repository name] / [image name] : [tag]
```
* 저장소 - 이미지가 저장된 장소. 이름이 명시되지 않으면 도커에서 기본적으로 제공하는 이미지 저장소인 도커 허브 공식 이미지인 것. 필수는 아니라서 그냥 생략하는 경우도 있다.
* 이미지 이름 - 해당 이미지가 어떤 역할 하는지 나타낸다. 생략 불가능해서 반드시 설정해야 한다.
* 태그 - 이미지의 버전 관리 혹은 리비전(revision) 관리에 사용한다. 생략하면 `latest`가 된다.

### 컨테이너를 이용하여 이미지 만들어보기
* 도커는 도커 허브(Docker Hub)라는 중앙 이미지 저장소에서 이미지를 내려받는다.
* `docker search`를 이용하여 image를 검색할 수 있다.
* `docker create`, `docker run`, `docker pull` 명령어로 이미지를 내려받을 수 있다.
* `docker rmi <docker image id>` 하면 생성된 이미지를 지울 수 있다.
```shell
$ docker search ubuntu
NAME                                                      DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
ubuntu                                                    Ubuntu is a Debian-based Linux operating sys…   12423     [OK]
dorowu/ubuntu-desktop-lxde-vnc                            Docker image to provide HTML5 VNC interface …   543                  [OK]
websphere-liberty                                         WebSphere Liberty multi-architecture images …   274       [OK]
rastasheep/ubuntu-sshd                                    Dockerized SSH service, built on top of offi…   253                  [OK]
consol/ubuntu-xfce-vnc                                    Ubuntu container with "headless" VNC session…   241                  [OK]
ubuntu-upstart                                            Upstart is an event-based replacement for th…   110       [OK]
ansible/ubuntu14.04-ansible                               Ubuntu 14.04 LTS with ansible                   98                   [OK]
1and1internet/ubuntu-16-nginx-php-phpmyadmin-mysql-5      ubuntu-16-nginx-php-phpmyadmin-mysql-5          50                   [OK]
open-liberty                                              Open Liberty multi-architecture images based…   46        [OK]
ubuntu-debootstrap                                        debootstrap --variant=minbase --components=m…   44        [OK]
i386/ubuntu                                               Ubuntu is a Debian-based Linux operating sys…   25
nuagebec/ubuntu                                           Simple always updated Ubuntu docker images w…   24                   [OK]
solita/ubuntu-systemd                                     Ubuntu + systemd                                24                   [OK]
1and1internet/ubuntu-16-apache-php-5.6                    ubuntu-16-apache-php-5.6                        14                   [OK]
1and1internet/ubuntu-16-apache-php-7.0                    ubuntu-16-apache-php-7.0                        13                   [OK]
eclipse/ubuntu_jdk8                                       Ubuntu, JDK8, Maven 3, git, curl, nmap, mc, …   13                   [OK]
1and1internet/ubuntu-16-nginx-php-phpmyadmin-mariadb-10   ubuntu-16-nginx-php-phpmyadmin-mariadb-10       11                   [OK]
1and1internet/ubuntu-16-nginx-php-5.6-wordpress-4         ubuntu-16-nginx-php-5.6-wordpress-4             9                    [OK]
1and1internet/ubuntu-16-nginx-php-5.6                     ubuntu-16-nginx-php-5.6                         8                    [OK]
1and1internet/ubuntu-16-apache-php-7.1                    ubuntu-16-apache-php-7.1                        7                    [OK]
1and1internet/ubuntu-16-nginx-php-7.0                     ubuntu-16-nginx-php-7.0                         4                    [OK]
pivotaldata/ubuntu                                        A quick freshening-up of the base Ubuntu doc…   4
smartentry/ubuntu                                         ubuntu with smartentry                          1                    [OK]
pivotaldata/ubuntu-gpdb-dev                               Ubuntu images for GPDB development              1
1and1internet/ubuntu-16-rspec                             ubuntu-16-rspec                                 0                    [OK]

# 우분투 이미지를 이용하여 새로운 컨테이너를 생성한다.
# 로컬에서 먼저 이미지를 찾고, 없으면 docker hub에서 이미지를 받아오는 것 같다.
$ docker run -i -t --name commit_test ubuntu:14.04
Unable to find image 'ubuntu:14.04' locally
14.04: Pulling from library/ubuntu
2e6e20c8e2e6: Pull complete
0551a797c01d: Pull complete
512123a864da: Pull complete
Digest: sha256:5c01e896fa6eeaa41f3509c64af668d71d06e318cfe373dabab9d61b9eaf6441
Status: Downloaded newer image for ubuntu:14.04

# 컨테이너 환경에 들어온 상황.
root@3f3b11fcbaa4:/# ls
bin  boot  dev  etc  first  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
root@3f3b11fcbaa4:/# exit

# 컨테이너를 이미지로 만드는 작업은 `commit` 명령어로 수행 가능하다.
# docker commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]] 구조로 커맨드를 작성한다.
docker commit \
-a "pkeugine" -m "my first commit" \
commit_test \
commit_test:1.0.0
```

### 이미지 구조
```shell
# inspect를 이용하여 컨테이너, 네트워크, 볼륨, 이미지 등 모든 도커 단위의 정보를 얻을 수 있다.
# 단, 이름이 중복되는 경우 컨테이너에 대해 먼저 수행되므로 --type을 명시하도록 한다.
$ docker inspect ubuntu:14.04
[
    {
        "Id": "sha256:13b66b487594a1f2b75396013bc05d29d9f527852d96c5577cc4f187559875d0",
        "RepoTags": [
            "ubuntu:14.04"
        ],
        "RepoDigests": [
            "ubuntu@sha256:5c01e896fa6eeaa41f3509c64af668d71d06e318cfe373dabab9d61b9eaf6441"
        ],
        "Parent": "",
        "Comment": "",
        "Created": "2021-03-25T22:33:44.754436368Z",
        "Container": "a55677c29cfc1b4d4574b70df6fb6aa1db116fecd36b66d33ed480999a14037d",
        "ContainerConfig": {
            "Hostname": "a55677c29cfc",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": [
                "/bin/sh",
                "-c",
                "#(nop) ",
                "CMD [\"/bin/bash\"]"
            ],
            "Image": "sha256:8de4ecfd37404a523d0253873a2b15a274d3657e9f726b472eb82c32ffbc76f4",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {}
        },
        "DockerVersion": "19.03.12",
        "Author": "",
        "Config": {
            "Hostname": "",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": [
                "/bin/bash"
            ],
            "Image": "sha256:8de4ecfd37404a523d0253873a2b15a274d3657e9f726b472eb82c32ffbc76f4",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": null
        },
        "Architecture": "amd64",
        "Os": "linux",
        "Size": 196513448,
        "VirtualSize": 196513448,
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/b70d7a9a49d0c89d8c1974d8f9b499d78ae86e07494dedb591f1a6a9d3a319ba/diff:/var/lib/docker/overlay2/ab85fa4732599bfa851b9b04bed7682e307ad6c26aeb4afda9cd05cc9b79e3b8/diff",
                "MergedDir": "/var/lib/docker/overlay2/033a263f90746e3e24e3fa79d976e5d7129ae5369850102c9d9fca21998c63d2/merged",
                "UpperDir": "/var/lib/docker/overlay2/033a263f90746e3e24e3fa79d976e5d7129ae5369850102c9d9fca21998c63d2/diff",
                "WorkDir": "/var/lib/docker/overlay2/033a263f90746e3e24e3fa79d976e5d7129ae5369850102c9d9fca21998c63d2/work"
            },
            "Name": "overlay2"
        },
        "RootFS": {
            "Type": "layers",
            "Layers": [
                "sha256:f2fa9f4cf8fd0a521d40e34492b522cee3f35004047e617c75fadeb8bfd1e6b7",
                "sha256:30d3c4334a2379748937816c01f5c972a8291a5ccc958d6b33d735457a16196e",
                "sha256:83109fa660b2ed9307948505abd3c1f24c27c64009691067edb765bd3714b98d"
            ]
        },
        "Metadata": {
            "LastTagTime": "0001-01-01T00:00:00Z"
        }
    }
]
```
가장 아래에 있는 `Layers` 항목을 보자. 이미지를 커밋할 때 컨테이너에서 변경된 사항만 새로운 레이어로 저장하고, 그 레이어를 포함해 새로운 이미지를 생성한다.
```shell
# history 커맨드를 이용하여 이미지의 레이어 구조를 확인할 수도 있다.
$ history ubuntu:14.04
IMAGE          CREATED        CREATED BY                                      SIZE      COMMENT
13b66b487594   3 months ago   /bin/sh -c #(nop)  CMD ["/bin/bash"]            0B
<missing>      3 months ago   /bin/sh -c mkdir -p /run/systemd && echo 'do…   7B
<missing>      3 months ago   /bin/sh -c [ -z "$(apt-get indextargets)" ]     0B
<missing>      3 months ago   /bin/sh -c set -xe   && echo '#!/bin/sh' > /…   195kB
<missing>      3 months ago   /bin/sh -c #(nop) ADD file:276b5d943a4d284f8…   196MB
```

앞서 `docker rmi <docker image id>`를 이용하면 이미지를 삭제할 수 있다고 했는데, `docker rmi [repository[:tag]]` 역시 가능하다.
그런데 이미 어떠한 컨테이너가 사용중인 이미지는 지울 수 없다. `-f` 옵션을 추가하면 강제로 삭제할 수 있지만, 이미지 레이어 파일을 실제로 삭제하지 않고 이미지 이름만 삭제하기 때문에 의미가 없다.
```shell
# 이런 식으로 지워지지 않고 남아있다...
$ docker images
REPOSITORY               TAG       IMAGE ID       CREATED         SIZE
<none>                   <none>    4100541a8b3c   3 minutes ago   197MB
mysql                    5.7       2c9028880e58   6 weeks ago     447MB
ubuntu                   14.04     13b66b487594   3 months ago    197MB
flowdas/python-docs-ko   1.2.6     d30c29655b5b   8 months ago    355MB
```
* 이를 `댕글링 이미지`라 하는데, `docker images -f dangling=true` 명령어를 사용하여 별도로 확인할 수 있다.

## 도커 컨테이너
* 이미지에는 Ubuntu, CentOS와 같은 운영체제부터 MySql 데이터베이스 등의 각종 애플리케이션, 그리고 빅데이터 분석 도구 등 다양한 종류가 있다.
* 해당 이미지를 이용하여 컨테이너를 생성하면 격리된 환경에서 이미지의 목적에 맞는 파일 시스템, 자원 및 네트워크를 사용할 수 있다.
* 컨테이너에서 무엇을 하든 원래 이미지는 영향을 받지 않는다.

## 도커와 컨테이너 생태계
### OCI (Open Container Initiative)
* 도커에서는 컨테이너 기술이 특정 회사나 벤더에 의존적으로 개발되지 않도록 중립적인 입장에서 표준을 정의했다. 그것이 바로 OCI.
* 컨테이너를 구성하는 다음 요소의 표준을 정의한다:
    * 런타임
    * 이미지 스펙

OCI 이후 도커는 다음 세 가지로 분리되어 관리된다:
* runC
* containerd
* 도커 엔진

유저가 docker 관련 커맨드를 입력하면...
* 도커 엔진과 소통을 하고
* 그 도커 엔진이 containerd와 소통하며
* containerd가 runc(또는 OCI 표준에 맞는 다른 런타임)을 실행하여 컨테이너를 실행한다

## Docker Compose

여러 개의 컨테이너를 하나의 프로젝트로서 다룰 수 있는 작업 환경을 제공한다.

**여러 개의 컨테이너로 구성된 애플리케이션 구축**: 모든 컨테이너 하나씩 실행하고 제대로 동작하는지 확인하기 번거롭다

--- 
### 도커 사용하여 쓸 수 있는 유용한 커맨드

#### docker 설치
```shell
sudo apt-get update && \
sudo apt-get install -y apt-transport-https ca-certificates curl software-properties-common && \
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add - && \
sudo apt-key fingerprint 0EBFCD88 && \
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" && \
sudo apt-get update && \
sudo apt-get install -y docker-ce && \
sudo usermod -aG docker ubuntu && \
sudo curl -L "https://github.com/docker/compose/releases/download/1.23.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose && \
sudo chmod +x /usr/local/bin/docker-compose && \
sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
```

#### docker 실행하기
```shell
docker-compose up -d
```
#### docker 정지하기
```shell
docker stop $(docker container ls -q)
```
#### docker 삭제하기
```shell
docker-compose rm -v -f
sudo rm -rf db
```

#### 기타 등등
```shell
docker ps
docker-compose ps
docker-compose scale # 여러 개의 컨테이너를 생성
```

---

## 질문

도커를 조금씩 알아보고, 도커 컴포스를 살짝 써봤다. 그런데 쿠버네티스는 뭐지?

* 도커 컨테이너, 도커 스웜(여러 대의 도커 엔진을 효율적으로 관리하기 위한 툴), 그리고 도커 컴포스를 한 곳으로 모아 사용할 수 있는 프로젝트.
* 이를 이용하여 상용 단계의 컨테이너 플랫폼을 구축할 수 있다.

### 쿠버네티스 장점

(다른 오픈소스 컨테이너 오케스트레이션 툴과 비교했을 때)

* 서버 자원 클러스터링, 마이크로서비스 구조의 컨테이너 배포, 서비스 장애 복구 등 컨테이너 기반의 서비스 운영에 필요한 대부분의 오케스트레이션 기능을 폭넓게 제공
* 성능과 안정성 신뢰 가능. 구글, 레드햇과 같은 든든한 오픈소스 진영이 참여하고 있다.
* 영속적 볼륨 (persistent volume), 스케줄링, 장애 복구, 오토 스케일링, 서비스 디스커버리 및 인그레스 등 컨테이너 기반 클라우드를 운영할 때 필요한 대부분의 기능과 컴포넌트를 사용자가 직접 커스터마이징 가능
* CNCF(Cloud Native Computing Foundation) 및 다른 클라우드 운영 도구들과 쉽게 연동되어 확장성 높음

### 쿠버네티스 단점
* 다른 오케스트레이션 툴보다 더 많은 지식을 필요로 한다. 도커 스웜보다 구조 훨씬 복잡, 사용 방법 다양.
* 이를 도입했다가 쿠버네티스 관리 자체가 어려워질 수 있다.
* 관리하기 위한 인력과 비용 고려 필요하다.
* 즉, 오버 엔지니어링이 되기 쉽다... **한 마디로 어렵다**
* **이미 이거 쓰면서 80%는 이해 못 했다**
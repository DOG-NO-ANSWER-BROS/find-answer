# 정리
## 한줄 요약
가비지 컬렉터는 주기적으로 JVM의 heap 메모리를 점검하여 스택에서 참조되지 않는 객체를 메모리에서 해제하는 장치이다.

## 동작 순서
가비지 컬렉터가 동작하면, 다음과 같은 동작을 수행한다.
Mark And Sweep (Optionally Compact)
1. Garbage collector가 Stack의 모든 변수를 스켄하면서 각각 어떤 객체를 참조하고 있는지 찾아서 마킹
2. Reachable Objec(리스트 안에 객체, 스택 → 힙(리스트) →힙(객체))가 참조하고 있는 객체도 찾아서 마킹 (1, 2 번을 Mark라 부름)
![](https://user-images.githubusercontent.com/51393021/132425744-523d4cf4-3b2f-4b52-8b48-9899477fe56b.png)
3. 마킹되지 않은 객체를 Heap에서 제거 (Sweep이라 부름)
4. compact를 하는 GC라면, 단편화 된 메모리를 정리
![](https://user-images.githubusercontent.com/48986787/132426253-cf4270f7-bfcd-47d2-a7e4-c78a3a0cd3c8.png)

## 동작 시점
먼저 힙의 구조는 아래와 같이 되어있다.
![](https://user-images.githubusercontent.com/51393021/132426039-0a07773f-1002-45ad-b009-2d04cdd816d9.png)

1. 영 제너레이션
  - 영 제너레이션은 새롭게 생성된 객체가 할당되는 공간이다.
  - 영 제너레이션은 에덴과 서바이버 공간으로 나뉜다.
  ![image](https://user-images.githubusercontent.com/51393021/132426143-666f2f17-c018-4be0-a741-d68ad1400733.png)
  1. 객체가 힙메모리에 할당 되면 우선 에덴에 할당된다. 에덴 영역이 가득 찬다면, 마이너 GC(에덴 영역에서의 mark and sweep)가 일어난다. 여기서 reachable해서 살아남은 객체는, survivor영역으로 이동한다.
  2. 살아남아서 서바이버 영역으로 이동한 객체는 age가 증가한다. 
  3. 또 다음 마이너 gc가 일어나면, 새로운 reachable 객체들은 서바이버 영역으로 이동하고, 기존 서바이버는 age가 1 증가한다.
  4. 이 과정이 반복되어 age가 일정 임계점을 돌파하면, old generation 영역으로 이동(promotion)한다.
  
3. 올드 제너레이션
  - 영 제너레이션에서 특정 age가 넘은 참조 메모리들이 이동하는 공간이다.
  - 이 공간이 가득차면, major GC(올드제너레이션에서의 mark and sweep)가 일어난다.

## 고려해야할 요소
### 고려할 요소
#### 1. heap 사이즈
* OS로부터 할당된 JVM의 일 처리 가능한 메모리
* 메모리가 클 수록 더 많은 객체를 담을 수 있고, 그만큼 GC 해야하는 시간이 길어진다

#### 2. 애플리케이션 데이터 사이즈
* 애플리케이션 규모가 클수록 해당 애플리케이션을 위한 메모리가 많이 필요하다
* 모든 새로운 객체는 young generation에 담길 것이라서 최대 heap 사이즈에 영향을 끼친다

#### 3. CPU 수
* 특정 알고리즘은 여러 CPU 코어가 있을 때 효과적이고, 또 어떤 알고리즘은 아니다

#### 4. 일시 정지 시간
* 다시 메모리를 확보하기 위해 멈추는 시간도 알고리즘 선택에 영향을 끼친다

#### 5. 처리량 (Throughput)
* 애플리케이션을 처리하는 시간이 많고 GC 하는 시간이 짧아야한다

#### 6. Memory Footprint
* 실제 애플리케이션을 위한 메모리인데 GC가 사용하는 경우. 한정된 환경에서는 memory footprint로 성능 확장을 한다

#### 7. 신속성 (Promptness)
* 객체가 죽고나서 해당 객체가 차지했던 메모리를 확보하기까지의 시간이 짧아야 한다
* heap 크기가 클수록 신속하지 못하다

## GC 구현체
GC는 다음 다섯 가지 구현체가 있다:
1. Serial Garbage Collector
2. Parallel Garbage Collector
3. CMS Garbage Collector
4. G1 Garbage Collector
5. Z Garbage Collector

### 1. Serial Garbage Collector
* 가장 간단한 GC 구현체
* 단일 쓰레드 위에서 돌아간다. 실행될 때 애플리케이션이 멈춘다...!
* 따라서 서버와 같은 멀티 쓰레드 환경에서는 적합하지 않다
* 그리 크지 않은 서비스, 일시 정지에 크게 민감하지 않은 애플리케이션에서 주로 쓰인다

### 2. Parallel Garbage Collector
* JVM의 GC의 디폴트 구현체로, Throughput Collectors라고 불리기도 한다 (Throughput -> 처리량)
* Serial Garbage Collector와는 달리 여러 쓰레드를 이용하여 heap을 관리한다
* 하지만 다른 애플리케이션의 쓰레드를 멈추는 것은 똑같다
* GC를 위한 쓰레드 수, 일시 정지 시간, 처리량, heap size 등을 정할 수 있다
* java 8의 디폴트 GC

### 3. CMS Garbage Collector
* Concurrent Mark Sweep 의 약자
* 여러 쓰레드를 이용하고, 애플리케이션의 프로세서 자원을 활용하여 일시 정지 시간을 줄인다
* 그만큼 애플리케이션이 느려진다
* Java 9에서부터 해당 GC는 depricated 되었다

### 4. G1 Garbage Collector
* 멀티 프로세서 환경에서 큰 메모리 공간을 사용할 수 있는 애플리케이션을 위해 만들어졌다
* CMS 가비지 컬렉터를 대체함
* 다른 GC와는 달리 G1 GC는 heap을 같은 크기의 서로 다른 가상의 메모리로 나눈다
* 가비지 컬렉션을 할 때 heap을 돌며 Marking 한다
* 마킹이 끝나면 스위핑을 진행해서 사용할 수 있는 공간을 마련한다
* java 9 ~ 12의 디폴트 GC

### 5. Z Garbage Collector
* 리눅스에 실험적인 요소를 위해 Java 11부터 새로 나온 GC
* 자원을 많이 필요로 하는 일은 병행성으로 해결한다. 애플리케이션 쓰레드를 10ms 보다 더 오래 멈추게 하지 않는다
* 레퍼런스 컬러링 (컬러 포인터) 이라는 개념을 사용한다. ZGC의 핵심 컨셉인데, 객체의 상태를 참조하기 위해 metadata 비트를 아용한다
* 8MB 부터 16TB 크기의 heap을 다룰 수 있으면서 그 크기에 일시 정지 시간이 영향을 받지 않는다
* 디폴트 GC가 아니다

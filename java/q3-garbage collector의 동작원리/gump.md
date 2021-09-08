# 정리

> 간단히 힙에 특정 메모리가 가득찼을때 스택에 참조되지 않는 객체를 제거하는 과정이에요.

## 과정

Garbage 컬렉터의 과정은 아래와 같아요 

1. Garbage collector가 Stack의 모든 변수를 스켄하면서 각각 어떤 객체를 참조하고 있는지 찾아서 마킹
2. Reachable Objec(리스트 안에 객체, 스택 → 힙(리스트) →힙(객체))가 참조하고 있는 객체도 찾아서 마킹 (1, 2 번을 Mark라 부름)
3. 마킹되지 않은 객체를 Heap에서 제거  (Sweep이라 부름)

<br>

## Garbage Collection는 언제 일어날까?

먼저 힙의 구조는 아래와 같이 되어있어요 

![https://user-images.githubusercontent.com/48986787/132424930-539a9369-dbea-447e-be09-108583228833.png](https://user-images.githubusercontent.com/48986787/132424930-539a9369-dbea-447e-be09-108583228833.png)

![https://user-images.githubusercontent.com/48986787/132425021-4a918e09-57e1-493b-8f5f-fe34cd2334a9.png](https://user-images.githubusercontent.com/48986787/132425021-4a918e09-57e1-493b-8f5f-fe34cd2334a9.png)

eden의 메모리가 다 사용되면

![https://user-images.githubusercontent.com/48986787/132425052-7beb283c-fad4-47eb-b3a9-3904c105cf9f.png](https://user-images.githubusercontent.com/48986787/132425052-7beb283c-fad4-47eb-b3a9-3904c105cf9f.png)

Mark and Sweep과정이 일어나게 되고, 

![https://user-images.githubusercontent.com/48986787/132425174-0dccf971-17e3-4d6d-93b6-5de014699a7c.png](https://user-images.githubusercontent.com/48986787/132425174-0dccf971-17e3-4d6d-93b6-5de014699a7c.png)

![https://user-images.githubusercontent.com/48986787/132425242-365665ca-7c6b-4425-a1a8-eb85b1f0bd16.png](https://user-images.githubusercontent.com/48986787/132425242-365665ca-7c6b-4425-a1a8-eb85b1f0bd16.png)

![https://user-images.githubusercontent.com/48986787/132425270-28f60a2a-5135-4804-8ddb-025e64219989.png](https://user-images.githubusercontent.com/48986787/132425270-28f60a2a-5135-4804-8ddb-025e64219989.png)

이러다 Surival0의 메모리가 다차게되면 

![https://user-images.githubusercontent.com/48986787/132425322-6b40bfe8-f0ab-4747-a86b-bd021be90d05.png](https://user-images.githubusercontent.com/48986787/132425322-6b40bfe8-f0ab-4747-a86b-bd021be90d05.png)

Survival 0 영역에 있던 객체는 Surival 1 영역으로 이동

![https://user-images.githubusercontent.com/48986787/132425385-cc0dbb78-9163-43ba-8d35-bf0ab0e31197.png](https://user-images.githubusercontent.com/48986787/132425385-cc0dbb78-9163-43ba-8d35-bf0ab0e31197.png)

위와 같이 Eden이 다 차게 되면

![https://user-images.githubusercontent.com/48986787/132425427-5252dcbb-6a74-410e-a60f-ac40827a35da.png](https://user-images.githubusercontent.com/48986787/132425427-5252dcbb-6a74-410e-a60f-ac40827a35da.png)

Survial 영역은 한쪽이 항상 비어있어야 하기에 Survial1로 옮겨짐

그렇다면, Survival1이 다차면 어떻게 될까?

![https://user-images.githubusercontent.com/48986787/132425603-297d2136-c2e8-446e-b4b2-cf05ef47bf44.png](https://user-images.githubusercontent.com/48986787/132425603-297d2136-c2e8-446e-b4b2-cf05ef47bf44.png)

Age값이 증가한 채로 survival0으로 옮겨짐 (색깔차이가 있다)

![https://user-images.githubusercontent.com/48986787/132425651-21a10102-c03b-4095-bb0f-dac22d98a580.png](https://user-images.githubusercontent.com/48986787/132425651-21a10102-c03b-4095-bb0f-dac22d98a580.png)

메모리가 다 차게되면 

![https://user-images.githubusercontent.com/48986787/132425691-41e7af34-62fc-4827-9779-40768727b198.png](https://user-images.githubusercontent.com/48986787/132425691-41e7af34-62fc-4827-9779-40768727b198.png)

![https://user-images.githubusercontent.com/48986787/132425747-1cb17acf-3792-4ddb-a0bd-8f0b2fb2ed79.png](https://user-images.githubusercontent.com/48986787/132425747-1cb17acf-3792-4ddb-a0bd-8f0b2fb2ed79.png)

Old Generation이 다 차게되면  Major GC가 발생한다.

![https://user-images.githubusercontent.com/48986787/132425800-cdc2c983-9a98-40aa-84a6-4cbf2f0c15f9.png](https://user-images.githubusercontent.com/48986787/132425800-cdc2c983-9a98-40aa-84a6-4cbf2f0c15f9.png)

위와 같은 과정이 반복되면서 Garbage Collector가 메모리를 관리해요 

## Garbage Collector 종류

**Serial GC**

GC를 처리하는 스레드가 1개, CPU코어가 1개만 있을 때 사용하는 방식 
Mark-Compact collection 알고리즘을 사용함 (위에서 봤던 과정에서 Compact과정이 추가된 것)

![https://user-images.githubusercontent.com/48986787/132426253-cf4270f7-bfcd-47d2-a7e4-c78a3a0cd3c8.png](https://user-images.githubusercontent.com/48986787/132426253-cf4270f7-bfcd-47d2-a7e4-c78a3a0cd3c8.png)

**Paralle GC**
GC를 처리하는 스레드가 여러 개, Serial GC보다 빠르게 객체를 처리할 수 있음 
Parallel GC는 메모리가 충분하고 코어의 개수가 많을 때 사용하면 좋음

**Concurrent MArk Sweep GC(CMS)**

`Stop The Wolrd`(GC를 실행하기 위해 JVM이 애플리케이션 실행을 멈추는 것)
GC를 실행하는 스래드를 제외한 나머지 스레드는 모두 작업을 멈춤
GC 작업을 완료한 이후 중단한 작업을 다시 시작 

![https://user-images.githubusercontent.com/48986787/132426481-a107b850-fce9-484e-8425-1134f3499da9.png](https://user-images.githubusercontent.com/48986787/132426481-a107b850-fce9-484e-8425-1134f3499da9.png)

이러한 Stop the world시간을 줄이기 위해 고려된 것
위에서 봤던 과정 1,2,3, 과 같아요.
애플리케이션의 응답 시간이 빨라야할 때 CMS GC를 사용해요 (CPU를 더 많이 사용)

Compaction을 사용하지 않아요.
****

**G1 GC**

![https://user-images.githubusercontent.com/48986787/132426677-37b03549-8b64-4878-b829-480183de2f76.png](https://user-images.githubusercontent.com/48986787/132426677-37b03549-8b64-4878-b829-480183de2f76.png)

앞서본 구조를 쪼개논 것. 
바둑판의 각 영역에 객첼르 할당하고 GC를 실행 

Compaction을 사용해요.

<br> 

# 질문

### 질문

자바는 디폴트 GC가 어떤것일까?
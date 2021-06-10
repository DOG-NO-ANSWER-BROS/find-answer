# 정리

> 둘다 서로 다른 브렌치의 작업결과를 합친다는 점에서 의미는 비슷해요. 하지만 실제 동작은 많이 달라요.

![https://user-images.githubusercontent.com/48986787/121395362-4e876580-c98d-11eb-9469-5015a9683f28.png](https://user-images.githubusercontent.com/48986787/121395362-4e876580-c98d-11eb-9469-5015a9683f28.png)

현재 그림과 같이 작업을 합치고 싶은 브렌치가 Feature 브렌치라는 예로 간략히 설명하자면, 

`merge`는 Feature에  Master의 변경사항들을 합치며, Master의 영향이 없게 헤요. 즉 merge가 일어난 이후에도 브렌치는 2개로 유지돼요.

`rebase`는 Feature에  Master의 변경사항들을 합치며, Master의 베이스를 현재 Feature로 변경해요.  즉 두개로 나뉘어져 있던 브렌치가 Feature로 통합돼요. 

<br>

## Refer

[https://www.atlassian.com/git/tutorials/merging-vs-rebasing](https://www.atlassian.com/git/tutorials/merging-vs-rebasing)

  

<br> 

# 질문

## 나만 알만한 것

### 질문

### 답변

<br>

## 진짜 궁금한 것

### 질문

깃 reflog는 어떻게 관리될까?
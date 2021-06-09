# merge와 rebase의 차이점

## merge와 rebase의 정의
* merge -  두 개 이상의 기록을 합친다.
* rebase - 다른 끝 점에 커밋을 다시 적용시킨다.

## 즉, 다음과 같다:
* merge - 하나의 새로운 커밋에 변경점을 모두 적용한다.
  * --no-comit 옵션으로 커밋을 안 할 수도 있다! 커밋 직전에 과연 내가 원하는대로 잘 변경되었는지 확인 가능.
* rebase - 현재 내가 있는 위치의 분기점을 최신화한다.
* 명심할 점: 결국 둘 다 다른 브랜치의 변경점을 내 브랜치로 가지고 오기 위해 쓴다.

## 그렇다면 왜 둘이 공존할까?
### 상황
```
     -- E - F - G (pk)
    /
A - B - C - D (main)
```
1. 내가 pk 브랜치에서 작업하고 있는데, 팀원이 main 브랜치에 C, D 커밋을 푸쉬 했다.
2. 내 브랜치에 해당 변경사항을 적용시켜야 한다.
3. merge와 rebase 중 하나를 택한다.

### merge
```shell
git checkout pk
git merge main
```
또는
```shell
git merge pk main
```
* pk 브랜치에 merge 커밋이 하나 생성되고, main 브랜치의 변경사항을 pk 브랜치로 가지고 온다.
* 장점 - 쉽다.
* 단점 - main 브랜치가 활발하면 pk 브랜치에 merge 커밋이 자주 생긴다. 깃 히스토리를 볼 때 가독성이 나빠진다.

### rebase
```shell
git checkout pk
git rebase main
```
```
** rebase 했을 때 git 상황 **

             -- E' - F' - G' (pk)
            /
A - B - C - D (main)
```
* rebase를 하면 main의 끝 점에 커밋이 새로 생긴다.
* 즉, main 브랜치의 변경사항을 모두 가지고 오면서 새로운 커밋을 pk 브랜치에 만들지 않는다.
* 장점 - 깃 히스토리가 훨씬 자연스럽게 만들어진다. 그리고 어쩌면 불필요하다고 볼 수 있는 커밋을 만들지 않는다.
* 또 다른 장점 - rebase를 하며 커밋들을 합칠 수 있다. 마치 amend하는 것처럼.
```shell
git checkout pk
git rebase -i main
```
* -i는 interactive 옵션으로, 이동할 커밋이 무엇인지 보여준다 (위의 그림으로는 E, F, G 커밋)
```shell
pick 33d5b7a E 커밋 메시지
pick 9480b3d F 커밋 메시지
pick 5c67e61 G 커밋 메시지
```
* `git rebase -i main`을 하면 위와 같이 뜰텐데,
```shell
pick 33d5b7a E 커밋 메시지
fixup 9480b3d F 커밋 메시지
pick 5c67e61 G 커밋 메시지
```
* pick을 fixup으로 바꿔주면 E 커밋에 F가 amend 된다.
```
** fixup과 함께 rebase 했을 때 git 상황 **

             -- E'(F를 포함) - G' (pk)
            /
A - B - C - D (main)
```
* ... rebase가 장점만 있을까? 그랬으면 merge는 depricated 되었겠지.

### rebase의 단점
* 겁나 어렵다. 즉, 함부로 못하고 조심스럽게 해야한다.
```
** 기존 git 상황 **

     -- E - F - G (pk)
    /
A - B - C - D (main)
```
```
** main 브랜치에서 git rebase pk를 했을 때 **

     -- E - F - G (pk)
    /            \
A - B             C' - D' (main)
```
* rebase 잘못하면 pk에서 main을 rebase 하는게 아니라, main에서 pk 브랜치를 rebase 해버리는 상황이 생길 수 있다.
* 이러면 협업하는 사람들의 main 브랜치와 내 main 브랜치가 완전 다른 상황이 되어버린다.
```
** 머리 터지는 git 상황 **
                   C' - D' (내 main)
                 /
     -- E - F - G (pk)
    /
A - B - C - D (다른 사람들의 main)
```
* 방지하기 위해 해야할 행동: git rebase를 시도하는 branch가 공용 브랜치인가? 스스로에게 묻기.
* 답이 "YES"라면 rebase 했을 때의 상황을 그림으로 그려보자...

## 질문
* git cherry pick은 뭔가요?

### 정의
* cherry-pick : 이미 존재하는 여러 커밋 중 원하는 커밋을 골라서 현재 내 브랜치에 새로운 커밋을 만든다.

### 상황
```
     -- E - F - G (pk)
    /
A - B - C - D (main)
```
* main 브랜치에서 F 커밋을 가지고 오고 싶을 때,
```shell
git checkout main
git log # 이 커맨드로 커밋 내용을 확인할 수 있다.
git cherry-pick F
```
```
** 커맨드 입력 후 만들어지는 상황
     -- E - F - G (pk)
    /
A - B - C - D - F (main)
```
* `--no-commit` 옵션이 여기도 존재한다. 커밋을 실제로 하지 않고 변경사항만 적용한다.
* `-edit` 옵션을 이용해서 커밋 메시지를 수정할 수 있다.

# merge와 rebase의 차이점

> 두 가지 모두 같은 문제상황을 해결하기 위해 사용한다. 브랜치가 나뉘었을 때 어떻게 하나의 분기로 합치는가?

아래와 같은 상황의 커밋트리가 있다.
![image](https://user-images.githubusercontent.com/51393021/121343465-ac9c5480-c95d-11eb-90c9-b30bb7b36624.png)

### merge
- 머지는 서로 갈라진 분기를 하나의 머지커밋을 생성해서 해결한다.
  ![image](https://user-images.githubusercontent.com/51393021/121344246-81663500-c95e-11eb-8f14-0943cb8cad2d.png)
- 보통 베이스와 헤드, 기능 브랜치가 달라진 경우 3way-merge를 시도하게 되는데 만약 master와 bts-image이 서로 같은 파일을 수정했다면 conflict가 발생하게 된다. conflict를 해결하여 머지커밋을 생성하면 된다.

### rebase
- 리베이스는 내 커밋이 연결 되어있던 베이스를 바꾸는 것이다. 머지가 마스터 브랜치에서 기능 브랜치를 머지했던 것과는 달리 리베이스는 기능 브랜치에서 마스터 브랜치를 리베이스 해야한다.
- 리베이스의 결과물은 커밋 기롯이 똑같더라도 새로운 해쉬값이 생성되기 떄문이다. 이후 다른 기능 브랜치에서 머지하려고 할 떄 수많은 컨플릭트를 마주할 수 있다.  
  ![image](https://user-images.githubusercontent.com/51393021/121344300-9216ab00-c95e-11eb-8401-3dad23e17723.png)
  ![image](https://user-images.githubusercontent.com/51393021/121344345-9fcc3080-c95e-11eb-8d2a-a4e42ec6baa3.png)

## 즉, 다음과 같다:
* `merge` - bts-image 브랜치에 master의 변경사항들을 합치며, master에 영향이 없게 한다. 즉 merge가 일어난 이후에도 브렌치는 2개로 유지된다.
    * --no-commit 옵션으로 커밋을 안 할 수도 있다! 커밋 직전에 과연 내가 원하는대로 잘 변경되었는지 확인 가능.
* `rebase` - bts-image 브랜치에 master의 변경사항들을 합치며, bts-image의 베이스를 현재 master로 변경한다. 즉 두개로 나뉘어져 있던 브렌치가 bts-image로 통합된다.
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

### rebase 를 할때는 public 브랜치에서는 신중하게

![image](https://user-images.githubusercontent.com/63634505/121307651-2bcc6100-c93b-11eb-8f14-75aed3a2486a.png)

master 브랜치에서 rebase를 할경우 master 의 모든 커밋이 feature 브랜치의 끝점으로 가게 된다. 이때 다른 팀원들은 master 브랜치를 사용하고 있다. 새로운 커밋으로 바꾸기 때문에 팀원들마다 master 브랜치가 다른 상황이 생길 수 있다.

또 다른 예시:
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

### 명령어
1. git merge %합칠 브랜치%
   —ff 디폴트 옵션으로, fast-forward 라면 참조만 변경, 아니라면 머지 커밋 생성함

로그가 하나로 되어 토픽 브랜치가 있었다는 정보가 없어진다.

토픽 브랜치의 존재를 로그에 남길 필요가 없어 그대로 커밋해도 커밋 밀도에 문제가 없는 경우에 사용.

—no-ff fast-forward라도 머지커밋 생성

—ff와 달리 토픽 브랜치가 있었다는 정보가 남는다.

기본적으로는 「[A successful Git branching model](http://keijinsonyaban.blogspot.com/2010/10/successful-git-branching-model.html)」를 참고로--no-ff를 사용하는것이 좋다.

—squash 워크 트리와 인덱스만 가져오고, 커밋 기록은 가져오지 않음.

토픽 브랜치가 있었다는 정보나 코밋로그도 모두 남지 않는다.

토픽 브랜치에서 행한 작업이 작아 １커밋으로 정리해버려도 문제가 없는 경우 사용.

2. git rebase -i 인터렉티드 모드 진입, edit로 만들면 수정할 대상이 됨.

- 커밋 해시를 이용한 방법, 내가 수정하고 싶은 커밋의 바로 직전 커밋 해쉬 입력
  ```
    git rebase -i 9d9cde8
  ```
- HEAD를 이용한 방법
  ```
    git rebase -i HEAD~3
  ```
- 인터렉티드 모드에서의 명령어
  - pick : 그대로 사용하겠다, 이를 이용해 커밋 순서를 변경하거나 아예 커밋을 삭제할 수 있음
  - reword : 커밋 메시지 수정
  - edit : 커밋 메시지 및 내용 수정, edit 입력 후 git commit --amend는 메시지 수정, 하고 싶은 커밋을 한 후 (몇개의 커밋을 더 쌓을 수 도 있음) git rebase —continue 를 통해 edit을 끝낼 수 있다.
  - squash : 여러 커밋을 합침. 커밋메시지도 합침
  - fixup : 여러 커밋을 합침. 커밋 메시지는 이전의 커밋 메시지만 남김
  - exec : rebase 도중 실행할 쉘 커맨드를 입력할 수 있음. 예시
    ![image](https://user-images.githubusercontent.com/51393021/121346774-7e207880-c961-11eb-9728-aaef6e4ab9d6.png)
    
## 질문

### git cherry pick은 뭔가요?

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

### 리베이스하면 커밋 해쉬값이 바뀌는데, 이전 커밋은 복구할 수 없나요?
가능하다. git reflog 를 활용하면 기존에 리베이스했던 코드 해쉬도 확인할 수 있다.
- 아래 이미지는 리베이스를 진행한 후 reflog 명령어를 활용한 예시이다. bbee0d62를 다른 브랜치에 리베이스를 수행하여 해쉬가 bbee0d62 -> e97ea043으로 변경 되었는데, reflog를 활용하면 이전 커밋 해쉬를 찾아올 수 있고 이를 이용해 원하는 복구 작업을 수행할 수 있다.   
  ![image](https://user-images.githubusercontent.com/51393021/121442931-88755d80-c9c7-11eb-8105-c73e41f5dd3d.png)

### fast-forward 병합과  non fast-forward ?

![image](https://user-images.githubusercontent.com/63634505/121307779-54545b00-c93b-11eb-9c47-220dc601ac46.png)
- `--ff`

  기본설정  fast-forward 인 경우 머지 커밋은 만들어지지 않고 브랜치의 참조만 변경

- `--no-ff`

  fast-forward 의 관계인 경우에도 머지 커밋을 만든다.

- `--squash`

  머지 커밋이 만들어지지 않는다. 스스로 커밋할 필요가 있다.
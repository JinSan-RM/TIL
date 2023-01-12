# Git & GitHub

### **.gitignore**

---------------

>.gitignore파일은 .git 폴더와 동일한 위치에 생성/
제외하고 싶은 파일은 .gitignore파일에 올려야한다.

`.gitignore`에는 어떤 파일들이 들어갈까?

     - 민감한 개인정보
     - OS(운영체제)에서 활용되는 파일
     - IDE 혹은 Txteditor등에서 활용되는 파일
     - 개발언어 혹은 프레임워크에서 사용되는 파일

`.gitignore` 사용방법
    
     mkdir gitignore
     cd gitignore
     pwd
     git init (local repository 로만들기) .git 폴더 생성
     touch .gitignore (.git과 .gitignore 은 같은 폴더에 있어야함)(코드로 열어야함)

     gitignore.io 사이트에서 사용하고자하는 os, 언어, db, 프레임워크 등 입력후 텍스트 코드  copy
     gitignore에 제외하고 싶은 파일 이나 확장자 등등을 입력

### **Clone, Pull**
--------


`Clone` 

git clone{remote_Repository 원격 저장소}

>GitHub repository에서 Local Repository로 복제한다. .git 폴더 또한 복제

진행방향--->>>

- clone 받은 후에 git init 하면 안된다.
- 모든 변경사항의 내용들이 복제 된다.
- .init 한곳에 다시 init 하면 안된다.

`Pull`

>git pull{remote_Repository 원격 저장소} remote_Repository의 변경사항을 (commit)을
local로 당겨서 가져온다.

- 이미 repository가 연결되어있으면 Pull / commit을 update 

- 원격에 백업되어있는 repository를 그대로 가져와야하나 Clone Repository를 전체 download

- pull은 upodate clone은 download



`branch` 

>branch 란 : 작업 공간을 나누어 **독립적으로 작업** 할수 있도록 도와주는 Git의 

      Branch           Merge
    (분기,가지)      (병합, 합병)

Merge HEAD가 가르키는 브랜치에 합쳐진다.

git merge (합칠 브랜치 이름)

Merge는 세 종류
기존 브랜치와 분기한 브랜치의 관계
- Fast-Forward
    
- 3 - Way merge

- Merge Conflict
    해결해서 수정해줘야함

### **Git workflow**
-------
>**Branch**와 **pullrequest**를 이용한 협업

Feature Branch Workflow (shared repository model)

원격 저장소의 소유권이 있는 경우 push할때 내가 만든 브랜치 이름을 push git push origin (브랜치)



### **Forking Workflow** (Fork & Pull model)
-------
    원격 저장소의 소유권이 없는 경우
    원본 레파지토리에 복제한 내 레파지토리에서 작업한다.
    작업후 내 레파지토리에 branch를 commit 하고
    해당 권한자에게 pullrequest한다.

## Git 명령어 정리

### 1. **basic**
------------------
    작성자 이름, 메일 등록 (최초 1번만 실행)
    git config --global user.name "github username"
    git config --global user.email "github email"

    config 정보 출력
    git config --global --list

    일반 폴더 -> 로컬 저장소
    git init

    버전 상태 출력
    git status

    Working Directory -> Staging Area
    git add [File]
    git add .  # 모든 파일 add

    Staging Area -> Commits
    git commit -m "commit message"

    commits 목록 출력
    git log
    git log --oneline  # 한줄로 보기 옵션
    git log -p  # 커밋마다 차이 보기 옵션

### 2. **remote**
------------------
    로컬 저장소와 원격 저장소를 연결
    git remote add origin [Github repository URL]

    연결된 원격 저장소 목록 조회
    git remote -v

    원격 저장소 연결 삭제
    git remote rm origin
    git remote remove origin

    로컬 저장소의 commits을 원격 저장소에 반영
    git push origin master
    git push -u origin master  # -u 옵션을 했다면 이후 push할 때는 git push만으로도 가능

    원격 저장소를 로컬에 복제
    git clone [Github repository URL]

    원격 저장소의 변경 사항 로컬에 받아오기 (동기화)
    git pull origin master

### 3. **reset, revert**
------
    특정 커밋 상태로 되돌리기 (soft, mixed, hard 세 가지 옵션)
    git reset --soft [commit ID]
    git reset --mixed [commit ID]
    git reset --hard [commit ID]

    커밋을 취소하는 커밋을 새로 생성하여 특정 커밋을 되돌리기
    git revert [commit ID]

    이전 커밋 목록 모두 출력
    git reflog

### 4. **branch, merge**
------
    브랜치 목록 확인
    git branch

    새 브랜치 생성
    git branch [branch name]

    특정 브랜치 삭제
    git branch -d [branch name]
    git branch -D [branch name]  # 강제 삭제(병합되지 않은 브랜치도 삭제)

    git switch [branch name]  # 다른 브랜치로 이동
    git switch -c [branch name]  # 브랜치를 생성함과 동시에 이동

    한 줄로, 모든 브랜치의, 그래프를 포함하여 커밋 목록 출력
    git log --oneline --all --graph

    브랜치 병합
    git merge [branch name]

### 5. **setting**
-----
    $ git config --global user.name "이름"
    $ git config --global user.email "메일 주소"
    
    제대로 들어갔는지 확인
    $ git config --global -l
    $ git config --global --list


### 6. **commit** 순서
----
    .git  폴더 생성 (master라고 터미널 창에 떠야함), *init 2번 금지*
    $ git init

    git의 상태 확인
    $ git status
    
        1. `Untracked` : Git이 관리하지 않는 파일 (한번도 Staging Area에 올라간 적 없는 파일)
        2. `Tracked` : Git이 관리하는 파일
        3. `Unmodified` : 최신 상태
        4. `Modified` : 수정되었지만 아직 Staging Area에는 반영하지 않은 상태
        5. `Staged` : Staging Area에 올라간 상태

    github에 올릴 주소
    $ git remote add origin https://깃허브주소 
    
        git remote -v 로 목록 조회
        git remote rm, remove 이름으로 저장소 연결 삭제
        git remote add upstream https://깃허브주소 upstreamd으로 다른 저장소의 이름을 붙여줌

    git에 파일을 올림
    # 특정 파일
    $ git add a.txt
    
    # 특정 폴더
    $ git add my_folder/
    
    # 현재 디렉토리에 속한 파일/폴더 전부
    $ git add . 

        Untracked, Modified → Staged 로 상태를 변경합니다.

    git에 commit 하기
    $ git commit -m "first commit"

    git에 branch 하기
    $ git switch 브랜치 이름

        $ git switch -c 브랜치 이름으로 한번에 생성과 이동이 가능

    git log
    $ git log

        커밋의 내역(ID, 작성자, 시간, 메세지 등)을 조회할 수 있는 명령어

        - `--oneline` : 한 줄로 축약해서 보여줍니다.
        - `--graph` : 브랜치와 머지 내역을 그래프로 보여줍니다.
        - `--all` : 현재 브랜치를 포함한 모든 브랜치의 내역을 보여줍니다.
        - `--reverse` : 커밋 내역의 순서를 반대로 보여줍니다. (최신이 가장 아래)
        - `-p` : 파일의 변경 내용도 같이 보여줍니다.
        - `-2` : 원하는 갯수 만큼의 내역을 보여줍니다. (2 말고 임의의 숫자 사용 가능)


    github에 업로드
    $ git push -u origin master/브랜치이름

    Pullrequest까지 한 다음

    $ git switch master 으로 원래 브랜치로 돌아옴

    $ git pull origin 주소 혹은 master로 병합된 파일을 받아온다.

    $ git branch -d 브랜치 이름으로 기존 사용했던거 삭제.

    이후 반복 작업.
# Git & Github 1. 기본 사용법
### Repository
* git이 관리하는 폴더 저장소
#### git init 
새로운 저장소를 생성할 때 사용
* 상위폴더에 git init하면 O / 하위폴더에는 git init 하지 않음 X
* git init 폴더에 있는지 확인
* git init 한 후 두번다시 git init 하지 않음

#### git status
- 현재 git으로 관리되는 파일들의 상태를 보여줍니다.

#### git add 
 - 작업디렉토리 상의 변경 내용을 스테이징 영역에 추가
 - filename staging area에 모든 파일을 올림 -> git add . 
 - 정해진 파일만 올림 -> git add filename

#### git commit
 - 코드 변화를 기록하는 것이 commit(커밋)
>git commit -m "내용"

#### git config
사용자 정보 설정 입력
>git config --global user.name "user_name"
git config --global user.email "user_email"

#### git log
git commit history 조회
git log --oneline
>[master] (root-commit) 6e2c3cf] first commit 에서 6e2c3cf은 고유 이름

staging area 특정 버전을 따로 관리하기 위한 기능

#### mac의 경우
>git config --global init.defaultBranch master
로 master의 권한을 준다.

- Github Repository 생성 (원격)
- local Repository (git init)


### Github repository <-> local repository

- remote : 원격저장소 관리 
- origin : 원격과 로컬의 약속된 이름
git remote add origin {원격 저장소 주소, Repository 주소} <- 원격 저장소 주소
>git push -u origin master, git push origin master

>git remote -v (remote한 내역 확인)

https://github.com/JinSan-RM/TIL.git
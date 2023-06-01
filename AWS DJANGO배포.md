## AWS EC2 서버에서 Git clone하기

git clone이란 repository에 올려 놓은 프로젝트를 복제하는 명령어입니다. 이 섹션에서 git clone은 서버에 프로젝트를 다운로드 하는데 사용이 되고, 이후에는 서버와 로컬에서의 버전 관리를 위해 쓰는 첫번째 명령어로서 쓰였다고도 할 수 있습니다.

 

github을 통해 업로드한 파일들을 서버에 원격 접속하여 clone하도록 하겠습니다. 먼저 서버에 접속해줍니다.

> $ ssh -i ~/.ssh/deploy_test.pem ubuntu@ec2-15-164-212-231.ap-northeast-2.compute.amazonaws.com

프로젝트 파일들은 모두 /srv/ 폴더에 다운로드 받을 것입니다. 따라서 이 폴더를 현재 유저인 ubuntu의 폴더로 소유권을 바꾸어 줍니다. sudo는 관리자 권한으로 명령함을 의미하고, chown은 change owner의 줄임말로 폴더의 소유권을 변경할 때 사용합니다.

> $ sudo chown -R ubuntu:ubuntu /srv/

소유권이 잘 바뀌었는지 확인하려면 루트 폴더(/) 로 이동한 뒤, 파일 리스트를 자세히 표시 (ls -al)하면 srv 폴더의 소유자가 ubuntu로 바뀌었음을 알 수 있습니다.

>$ cd /
$ ls -al
total 96
drwxr-xr-x  23 root   root    4096 Mar 14 19:32 .
drwxr-xr-x  23 root   root    4096 Mar 14 19:32 ..
drwxr-xr-x   2 root   root    4096 Mar 14 19:31 bin
drwxr-xr-x   3 root   root    4096 Mar 14 19:32 boot
drwxr-xr-x  15 root   root    2980 Mar 14 18:59 dev
drwxr-xr-x  89 root   root    4096 Mar 14 19:36 etc
drwxr-xr-x   3 root   root    4096 Mar 14 18:59 home
lrwxrwxrwx   1 root   root      31 Mar 14 19:32 initrd.img -> boot/initrd.img-4.15.0-1060-aws
lrwxrwxrwx   1 root   root      31 Jan 12 17:42 initrd.img.old -> boot/initrd.img-4.15.0-1057-aws
drwxr-xr-x  20 root   root    4096 Mar 14 19:36 lib
drwxr-xr-x   2 root   root    4096 Jan 12 17:33 lib64
drwx------   2 root   root   16384 Jan 12 17:38 lost+found
drwxr-xr-x   2 root   root    4096 Jan 12 17:33 media
drwxr-xr-x   2 root   root    4096 Jan 12 17:33 mnt
drwxr-xr-x   2 root   root    4096 Jan 12 17:33 opt
dr-xr-xr-x 103 root   root       0 Mar 14 18:59 proc
drwx------   4 root   root    4096 Mar 14 18:59 root
drwxr-xr-x  25 root   root     920 Mar 14 20:27 run
drwxr-xr-x   2 root   root   12288 Mar 14 19:31 sbin
drwxr-xr-x   5 root   root    4096 Mar 14 18:59 snap
drwxr-xr-x   2 ubuntu ubuntu  4096 Jan 12 17:33 srv
dr-xr-xr-x  13 root   root       0 Mar 14 18:59 sys
drwxrwxrwt   9 root   root    4096 Mar 14 20:27 tmp
drwxr-xr-x  10 root   root    4096 Jan 12 17:33 usr
drwxr-xr-x  13 root   root    4096 Jan 12 17:37 var
lrwxrwxrwx   1 root   root      28 Mar 14 19:32 vmlinuz -> boot/vmlinuz-4.15.0-1060-aws
lrwxrwxrwx   1 root   root      28 Jan 12 17:42 vmlinuz.old -> boot/vmlinuz-4.15.0-1057-aws

이제 /srv/ 폴더로 이동하여 github 의 파일들을 다운 받습니다.

> $ cd /srv
>$ git clone [레포지토리 주소]

##  uWSGI python 패키지를 이용해 WSGI 서버를 Django와 연결해 보도록 하겠습니다.
----
## runserver 해보기

사실 우리가 옮긴 프로젝트를 바로 runserver 하여 화면을 띄워볼 수 있습니다. 지금 서버 컴퓨터에는 프로젝트를 실행하기 위한 패키지들이 깔려 있지 않으니, 로컬 컴퓨터에서와 동일하게 가상환경을 만들어 주고 패키지 버전들을 저장한 requirements.txt를 통해 패키지를 깔아보도록 하겠습니다.

먼저, 가상환경을 만들기 전에 python3-venv를 설치합니다.
> $ sudo apt-get install python3-venv

가상환경의 위치는 현재 사용하고 있는 유저인 ubuntu의 홈 폴더에 만들도록 하겠습니다.

>$ cd ~
>$ python3 -m venv myvenv

가상환경의 위치는 현재 사용하고 있는 유저인 ubuntu의 홈 폴더에 만들도록 하겠습니다.

>$ cd ~

>$ python3 -m venv myvenv

가상환경을 활성화 하고,



>$ source myvenv/bin/activate

그러면 자동으로 Django를 포함한 requirements.txt에 써져있는 패키지들이 깔리기 시작합니다.  그 다음 프로젝트 폴더에 있는 manage.py를 이용해 runserver를 아래와 같이 해주세요.

>$ python3 manage.py runserver 0:8080

그리고 자신의 퍼블릭 DNS 주소 뒤에 포트 번호를 의미하는 :8080을 붙여 브라우저에 접속해보세요.

> http://ec2-15-164-212-231.ap-northeast-2.compute.amazonaws.com:8080/

그러면 아마 로딩만 하고 페이지가 띄워지지 않는 것을 볼 수 있습니다. 우리가 만든 서버의 8080 포트가 열려있지 않기 때문입니다. AWS 로 접속하여 EC2 화면으로 가봅시다. 여기서 우리가 생성한 인스턴스를 클릭하고 하단에 보면 보안 그룹이라고 있습니다. 각자 보안 그룹이 하나씩 걸려있을 것인데, 그 보안 그룹을 클릭해 주세요.

![Alt text](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcVEd25%2FbtqCLd2uLZM%2FQYYGtyB8BvtpWtwkAHtYl1%2Fimg.png)
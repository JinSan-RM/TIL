local-환경설정
1. python3.9 가상환경 생성
2. requirements.txt 작성및 필요 모듈 설치
- 필수 모듈 설치시 종속성에 따라 같이 설치되는 모듈은 문서에 지움.??
3. 태스트를 위해 db를 sqlight 로 임시 설정
4. settins.py allowed host에 

AWS-환경설정
1. 인스턴스 생성
2. 키페어 생성

3. 도커 설치
- <https://docs.docker.com/engine/install/ubuntu/>
- Install using the apt repository 참조
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg

sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL <https://download.docker.com/linux/ubuntu/gpg> | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

echo \\
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] <https://download.docker.com/linux/ubuntu> \\
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \\
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
docker 
# 커맨드로 잘 설치 되었는지 확인

4. portainer 설치
- <https://docs.portainer.io/start/install-ce/server/docker/linux>
sudo docker volume create portainer_data
sudo docker run -d -p 9000:9000 --name portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:latest

sudo docker container ls 
# 컨테이너 잘 설치되었는지 확인

5. aws 보안그룹 기본설정 되어있기 떄문에 사용할 포트 열어야함.
- 보안그룹에서 9000번 포트 오픈

6. docker swarm 활성화
sudo docker swarm init

7. 포트열기
- 보안그룹 80, 443 포트 열기
- 보안그룹 8000 포트 열기

8. docker와 연결을 위한 nginx.conf 설정
- gunicorn 과 연결이기 떄문에 gunicorn 공식 페이지 에서 참조한 내용을 베이스로 입력후 수정
<https://gunicorn.org/#deployment>

worker_processes auto;

events {
}

http {
  server {
    listen 80;
    client_max_body_size 100m;
    #비디오 업로드를 위한 용량 제한을 늘려준다.  추가

    include mime.types;
    # mime.types 안에 있는 문구를 가져와서 사용한다.  추가

    location /static/ {
        alias /data/static/;
    }
    # docker volume 과 연동하기위한 구문 추가

    location /media/ {
        alias /data/media/;
    }
    # docker volume 과 연동하기위한 구문 추가

    location / {
        proxy_pass http://django_container_gunicorn:8000;
        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
  }
}

-해당 파일을 home/project 폴더로 업로드후 nginx container 생성


소스코드
1. gunicorn 설치
pip install gunicorn
-requirement.txt 에 추가

2. Collectstatic - static 파일 취합. 준비
-settins.py 설정 변경
STATIC_URL = '/static/'
STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
STATICFILES_DIRS = [BASE_DIR/"static"]

python manage.py collectstatic
-Dockerfile에 구문 추가
#staticfiles 폴더 안에 static관련 소스들이 모인다.
#django-gunicorn 조합은 static 파일은 랜더링 못한다. static파일은 nginx에서 랜더링 할수 있도록 따로 뽑아서 nginx에 공유해주기위해
#따로 모으는것이다. 뒤에 Docker volume 통해 연결할것

- 현재 까지 한 세팅은 지금 dockerfile 로 이미지 생성 해야 애러 없음 dockerfile
FROM python:3.9.0
WORKDIR /home/
RUN echo "testing33"
RUN git clone <https://github.com/moongi-lee/safezone.git>
WORKDIR /home/safezone
RUN pip install -r requirements.txt
RUN echo "SECRET_KEY=django-insecure-j!1$5ccgna@8!g5p*v^0dg%ws0naauc-g13%5jpiskx&qxyz70" > .env
RUN apt-get update && apt-get install -y libgl1-mesa-glx
RUN python manage.py migrate
RUN python manage.py collectstatic
EXPOSE 8000
CMD ["gunicorn", "safezone.wsgi", "--bind", "0.0.0.0:8000"]

3. local, deploy settins.py  분리 하기
+ -allowedhost "*" 추가
-mainapp 에서 settins 폴더 생성
-폴더 안에서 base, local, deploy 파일생성
base : 공통
local : 개발환경에서 settins내용
deploy : 배포환경에서 settings내용
-소스코드 참조

-BaseDIR 바꿔주기
-DBUG 바꿔주기
-DATABASE 바꿔주기
-secrete 관련 내용추가

4. manage.py 수정
- 소스코드 참고

5. safezoneapp views.py 경로수정

- 현재 까지 한 세팅은 지금 dockerfile 로 이미지 생성 해야 애러 없음 dockerfile
FROM python:3.9.0
WORKDIR /home/
RUN echo "testing66"
RUN git clone <https://github.com/moongi-lee/safezone.git>
WORKDIR /home/safezone
RUN pip install -r requirements.txt
RUN pip install mysqlclient
RUN echo "SECRET_KEY=django-insecure-j!1$5ccgna@8!g5p*v^0dg%ws0naauc-g13%5jpiskx&qxyz70" > .env
RUN apt-get update && apt-get install -y libgl1-mesa-glx
RUN python manage.py collectstatic
EXPOSE 8000
CMD ["bash", "-c", "python manage.py migrate --settings=safezone.settings.deploy && gunicorn safezone.wsgi --env DJANGO_SETTINGS_MODULE=safezone.settings.deploy --bind 0.0.0.0:8000"]

포테이너
1. docker network 생성 
- add network button 
- name = nginx-django  그대로 생성

2. docker volume 생성
- add volume button
- static
- media
- database
# 추가 설정없이 그냥 이름만 해놓고 만듬

3. docker secrete 생성 # 이거에 맞춰서 settins.py 추가해줘야함
DJANGO_SECRET_KEY = django-insecure-j!1$5ccgna@8!g5p*v^0dg%ws0naauc-g13%5jpiskx&qxyz70
DJANGO_SECRET_KEY = j!1$5ccgna@8!g5p*v^0dg%ws0naauc-g13%5jpiskx&qxyz70
MYSQL_PASSWORD = ubuntu
MYSQL_ROOT_PASSWORD = ubuntu

2. nginx container 생성 - (nginx.conf파일 주석 확인)
- network = nginx-django
- bind volume 
host = /home/project/nginx.conf
container = /etc/nginx/nginx.conf
- named volume
container = /data/static/
volume = static - local
container = /data/media/
volume = media - local

3. django_container (dockerfile로 이미지 생성후 그걸로 container생성)
- network = nginx-django
volume
container = /home/safezone/staticfiles/
volume = static - local
container = /home/safezone/media/
volume = media - local
container = /home/project/best.pt
host = /home/project/best.pt

4. mariaDB container 추가 및 소스코드에서 database 분리
- docker hub 에서 mariadb 검색시 공식 사용가능한 버전 목록 확인가능
- 10.5 사용
-mariadb docker volume 사용법은
- <https://hub.docker.com/_/mariadb> 참조  # Where to Store Data 참조 (사이트에서 찾기)

-settins.py deploy 에서 입력한 DB 정보를 토대로 컨테이너 생성시 사용해야함.
-mariadb 또한 volume 을 통해 django랑 이어줘야함

-network = nginx-django
-volume 
container = /var/lib/mysql
volume = database

-env # Environment Variables (사이트에서 찾기)
MYSQL_ROOT_PASSWORD = ubuntu
MYSQL_DATABASE = safezone
MYSQL_USER = safezone
MYSQL_PASSWORD = ubuntu

5. docker stack setting - docker-compose 문서 작성
# 1,2,3,4 번을 최초 1회 시도시, 다음부터 docker compose 문서로 1234번을 자동으로 해줌.
# scale 조절이 편해짐
# 기타 여러 오류로 인한 서버 문제 발생,종료시 자동으로 서버를 재시작 해줌
- 내용은 대략 위 1234번에서 한 세팅들을 문서 하나에 적는것.
- 소스코드 확인

6. Stack 생성
- docker-compose 파일 업로드하여 생성

파일경로 시각화

애러발생요인 및 주의사항
1. host ubuntu 에서 nginx 구동시, nginx container 생성하여 host의 80포트 연결할시 콘테이너가 생성되지 않는 애러가 있음
- host ubuntu 의 nginx 실행 종료해야함.

2. 컨테이너 끼리 연결시 하나라도 network 설정안해주면 애러난다. 

3. staticfiles gitignore 등록안하면 이미지 생성시 애러 생김

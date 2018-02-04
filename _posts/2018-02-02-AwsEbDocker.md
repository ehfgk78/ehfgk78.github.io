---
layout: post
title:  "AWS ElasticBeanstalk( Docker플랫폼 )로 Django 배포하기"
date:  2018-02-02 02:20 +0900
categories: [Django, AWS, Docker]
tags: [ Django, AWS, Docker]
---

* Kramdown table of contents
{:toc .toc}

<br/>
<br/>

# 

Docker 공식 문서:  https://docs.docker.com/get-started/

[초보를 위한 도커 안내서 (subicura)](https://subicura.com/2017/01/19/docker-guide-for-beginners-1.html)

[이한영 강사의 블로그](https://lhy.kr/eb-docker) 

[나채원 블로그](https://nachwon.github.io/django/2017/11/01/django-deploy-8-docker.html)

[Get Docker CE for Ubuntu](https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/#uninstall-old-versions)

[Install Docker for Mac](https://docs.docker.com/docker-for-mac/install/#download-docker-for-mac)

[AWS 전문가 되기](https://brunch.co.kr/magazine/devops)

## 전체 모습

```sh
(Front)'service.com'
(Backend)'api.service.com'
        │
   <HTTP/HTTPS>요청      
        ├─── Route53 #도메인주소 설정 
       ELB #SSL설정:ACM, #RDS, S3설정 
        │
       EC2── Nginx #HTTPS리다이렉션 설정 
              │
            Docker⟝Nginx⟝uwSGI⟝Django #Health Check
```



# 도커 

​	도커(Docker)는 리눅스 응용프로그램들을 소프트웨어 컨테이너 안에 배치시키는 일을 자동화하는 오픈 소스 프로젝트이다.  도커 컨테이너(`Container`)는 **<u>어떤 소프트웨어와 이를 실행하는데 필요한 모든 것을 완전한 파일 시스템 안에 감싼다.</u>**  여기에는 코드, 런타임, 시스템 도구 , 시스템 라이브러리 등 서버에 설치되는 무엇이든 아우른다.  도커의 이러한 기능은 실행 중인 환경과 관계없이 언제나 동일하게 실행될 것을 보증한다. 

여기에서는 `Docker Container`  안에서  Django 실행에 필요한 모든 것들 ( Ubuntu ➜ zsh( + oh-my-zsh ➜ pyenv ➜ python 3.6.3 ➜ Django ➜ uWSGI + Nginx)을 설치하여 실행해보고, 이를 `Docker image`로 만들어  `Docker Hub`에 업로드한다.  여기에 `AWS ElasticBeanstalk`을 이용하여 Django를 자동으로 배포할 것이다.  Docker image를 만드는 과정은 `Dockerfile`을 이용하여 자동으로 생성(Build)되도록 할 것이다. 



## ⚿ 도커 설치

```sh
# docker 설치
curl -s https://get.docker.com/ | sudo sh

# docker에게 sudo권한을 주기 (sudo docker 명령어 ➝ docker 명령어)
## 현재 접속 중인 사용자에게 sudo 권한 주기
sudo usermod -aG docker $USER 
## 지정한 유저에게 권한 주기
sudo usermod -aG docker 유저명 
## 다음 로그인 시점부터 적용된다. 

# 설치 확인
docker run hello-world
```

✛ [curl](https://curl.haxx.se/)  + [curl 설치 및 사용법 - HTTP GET/POST, REST API 연계등](https://www.lesstif.com/pages/viewpage.action?pageId=14745703)

✛ [커맨드라인 환경에서 REST API (HTTP) 요청 보내기 (cURL, resty, httpie, Vim REST Control)](https://bakyeono.net/post/2016-05-02-rest-api-client-for-cli.html) 



## Ubuntu 16.04 이미지 

```sh
# 문법
docker run [옵션s] 이미지[:TAG|@DIGEST] [COMMAND] [ARG ...]
# 옵션s :: 설명
## --rm :: 프로세스 종료시 해당 이미지의 컨테이너 자동 제거
## -it :: 도커 컨테이너 내의 실행을 현재 터미널에서 입력(interactive terminal)
## -p <host 포트>:<container 포트>  :: 포트 연결(포워딩)
## -d:: 백그라운드 모드, -e:: 컨테이너 내 환경변수 설정, --link::컨테이너연결
## --name:: 컨테이너 이름 설정 
## -v:: 호스트와 컨테이너의 디렉토리 연결(마운트)
```

```sh
# ubuntu 이미지 검색
docker search ubuntu:16.04
# 이미지 다운로드:: docker pull [옵션] 이미지[:태그명]
# 다운로드와 동시에 container실행은 아래처럼 
docker run ubuntu:16.04

# 컨테이너 내 우분투 운영체제 내장 bash 실행
docker run --rm -it ubuntu:16.04 bin/bash
```



## ⛺ Dockerfile.base

**Pycharm Plug-in** 

`Docker Integration`

```dockerfile
FROM        ubuntu:16.04
MAINTAINER  홍길동@gmail.com

# 우분투 패키지툴 업데이트 및 pip, git, vim설치
RUN  apt-get -y update
RUN  apt-get -y dist-upgrade
RUN  apt-get install -y python-pip git vim

# pyenv와 python 3.6.3
## pyenv common build problems
RUN  apt-get install -y make build-essential libssl-dev \
zlib1g-dev libbz2-dev libreadline-dev libsqlite3-dev wget curl \
llvm libncurses5-dev libncursesw5-dev xz-utils tk-dev
## pyenv 설치
RUN  curl -L https://raw.githubusercontent.com/yyuu/pyenv-installer/master/bin/pyenv-installer | bash
## pyenv path설정 
ENV  PATH /root/.pyenv/bin:$PATH
# python 3.6.3
RUN  pyenv install 3.6.3

# zsh
RUN  apt-get install -y zsh
RUN  wget https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | zsh || true
RUN  chsh -s /usr/bin/zsh

# pyenv settings
RUN  echo 'export PATH="/root/.pyenv/bin:$PATH"' >> ~/.zshrc
RUN  echo 'eval "$(pyenv init -)"' >> ~/.zshrc
RUN  echo 'eval "$(pyenv virtualenv-init -)"' >> ~/.zshrc

# pyenv virtualenv
RUN  pyenv virtualenv 3.6.3 app
# uWGSI install
RUN  /root/.pyenv/versions/app/bin/pip install uwsgi
# Nginx install
RUN  apt-get -y install nginx
# supervisor install
RUN  apt-get -y install supervisor

# pip
ENV  LANG C.UTF-8
COPY  requirements.txt /srv/requirements.txt
RUN  /root/.pyenv/versions/app/bin/pip install  \
     -r /srv/requirements.txt
```

**base 이미지 빌드** 

```sh
docker build -t ehfgk78/base -f Dockerfile.base .
## -t 이미지명 태그
## -f 이미지를 빌드할 Dockerfile명 
## 마지막 .은 이미지를 생성할 경로가 현재 디렉토리라는 뜻 
```

**이미지 레이어** 

위 `Dockerfile.base`를 실행하면 뜨는 로그를 살펴보자

```tex
Sending build context to Docker daemon  18.11MB  #1
Step 1/19 : FROM ubuntu:16.04                    #2    
 ---> 747cb2d60bbe                               #3
Step 2/19 : MAINTAINER nachwon@naver.com         #4
 ---> Running in 4fd03f3be194                    #5
 ---> feabda9f3e02                               #6
Removing intermediate container 4fd03f3be194     #7
.
.
Successfully built 975e4869f7c3                  #8
Successfully tagged base:latest                  #9
```

- **`#1`**: 처음 빌드를 시작하면 명령을 실행한 디렉토리의 파일들 (Build contest) 을 Docker 서버로 전송한다.
- **`#2`**: FROM ubuntu:16.04 명령을 실행한다.
- **`#3`**: 위 명령의 실행 결과를 이미지 747cb2d60bbe 로 만든다. 이 경우에는 ubuntu:16.04 이미지를 가져오는 것이므로 우분투 이미지의 ID인 747cb2d60bbe이 그대로 표시된다.
- **`#4`**: MAINTAINER 명령을 실행한다.
- **`#5`**: 위 명령을 4fd03f3be194 라는 임시 컨테이너를 만들어 그 안에서 실행한다.
- **`#6`**: 명령의 실행 결과를 이미지 feabda9f3e02 로 저장한다.
- **`#7`**: 명령 실행을 위해 만들었던 임시 컨테이너 4fd03f3be194를 삭제한다.
- **`#8`**: 가장 마지막 명령을 실행한 결과로 생성된 이미지는 975e4869f7c3 이다. 이 이미지가 최종 결과인 base 이미지인 것이다.
- **`#9`**: base 이미지에 latest 라는 태그를 붙여준다.

 `임시 컨테이너 생성` > `다음 명령 실행` > `실행 결과를 이미지로 저장` > `임시 컨테이너 삭제`

이렇게  한 번의 반복으로 생성되는 이미지를 `이미지 레이어`라고 한다.  각 명령의 실행결과는 `레이어 단위`로 저장되므로 설치과정을 처음부터 다시 할 필요없고, 특정 시점의 레이어로 돌가갈 수 있다. 



## Docker Hub 

### Docker-Registry

```dockerfile
# AWS EB는 Dockerfile과 git commit을 기준으로 배포한다. EB가 Dockerfile의 base 이미지를 인식할 수 있도록 공개저장소인 Docker Hub에 업로드해줘야 한다. DockerHub가 아니더라도 가능하다. 
FROM  <Docker Hub 사용자 계정>/<이미지 이름>:<태그>
... 
```

```sh
$ docker login
Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username (****): ******
Password: 
Login Succeeded
```

```sh
# base이미지에 새 태그명 azelf/base를 붙인다
$ docker tag base azelf/base

# azelf/base저장소에 해당 이미지를 push
$ docker push <Docker Hub 사용자 계정>/<이미지 이름>:<태그>
	$ docker push azelf/base
```



## Django settings

Settings 분리 참조 

```sh
instagram_project
  .config_secret
      settings_common.json
      settings_local.json
      settings_dev.json
  instagram
      config
          settings
              __init__.py  
              base.py 
              local.py
              dev.py 
              deploy.py 
      wsgi 
          __init__.py
          local.py
          dev.py
      urls # media_root
```

## local Nginx, uWSGI 설정

settings 분리 참조 

**전체 모습** 

```ini
localhost:8000──Docker[-80─Nginx─<소켓>─uWSGI─Django]
                          ──┿──       ──┿──
                         supervisor   supervisor
```



## ⛺ Dockerfile.local 

```dockerfile
FROM  base
MAINTAINER  ehfgk78@gmail.com

ENV  DJANGO_SETTINGS_MODULE  config.settings.local
ENV  LANG C.UTF-8

COPY . /srv/app
RUN  /root/.pyenv/versions/app/bin/pip install \
-r /srv/app/requirements.txt

# pyenv local 설정
WORKDIR  /srv/app
RUN  pyenv local app

# Nginx
RUN  cp /srv/app/.config/local/nginx/nginx.conf /etc/nginx/nginx.conf
RUN  cp  /srv/app/.config/local/nginx/app.conf /etc/nginx/sites-available/
RUN  rm -rf /etc/nginx/sites-enabled/*
RUN  ln  -sf /etc/nginx/sites-available/app.conf \
/etc/nginx/sites-enabled/app.conf

# uWSGI
RUN  mkdir -p  /var/log/uwsgi/app

# supervisor
RUN  cp /srv/app/.config/local/supervisor/*  /etc/supervisor/conf.d
CMD  supervisord -n

# manage.py
WORKDIR  /srv/app/instagram
#RUN  /root/.pyenv/versions/app/bin/python manage.py collectstatic --noinput
#RUN  /root/.pyenv/versions/app/bin/python manage.py migrate --noinput

# 개방 port
EXPOSE  80 8012
```



## ⛺ Dockerfile

```dockerfile
FROM  Greg/base
MAINTAINER  Greg@gmail.com

ENV  LANG C.UTF-8
#⇒ Dockerfile.local
  # ENV  DJANGO_SETTINGS_MODULE=config.settings.local 불필요함 
#⇒ Dockerfile (dev/deploy)
  ENV  DJANGO_SETTINGS_MODULE=config.settings.dev

# 파일 복사 및 requirements 설치 - pip update용
## 주의! .config_secret등 비밀설정파일이 복사되므로 공개에 주의해야 한다. 
COPY  . /srv/app
RUN  /root/.pyenv/versions/app/bin/pip install \
     -r  /srv/app/requirements.txt

# pyenv local 설정
WORKDIR  /srv/app
RUN  pyenv local app

# NginX
#⇒ Dockerfile.local
RUN cp /srv/app/.config/local/nginx/nginx.conf /etc/nginx/nginx.conf
RUN cp  /srv/app/.config/local/nginx/app.conf /etc/nginx/sites-available/
#⇒ Dockerfile.dev
RUN cp /srv/app/.config/dev/nginx/nginx.conf /etc/nginx/nginx.conf
RUN cp  /srv/app/.config/dev/nginx/app.conf /etc/nginx/sites-available/
#⇒ Dockerfile.deploy 위와 다를 바 없다. 

RUN  rm -rf /etc/nginx/sites-enabled/*
RUN  ln  -sf /etc/nginx/sites-available/app.conf \
         /etc/nginx/sites-enabled/app.conf
# uWSGI
RUN  mkdir -p  /var/log/uwsgi/app
# manage.py
WORKDIR  /srv/app/mysite
RUN  /root/.pyenv/versions/app/bin/python manage.py collectstatic --noinput
RUN  /root/.pyenv/versions/app/bin/python manage.py migrate --noinput
# supervisor
#⇒ Dockerfile.local
RUN cp /srv/app/.config/local/supervisor/* /etc/supervisor/conf.d
#⇒ Dockerfile.dev
RUN cp /srv/app/.config/dev/supervisor/* /etc/supervisor/conf.d
#⇒ Dockerfile.deploy 위와 다를 바 없음 
CMD  supervisord -n

# docker의 port개방::  Nginx 설정파일 app.conf 주의!
## server {
##     listen 80;
## }
EXPOSE  80 8012
```



## ⏬ docker-run.sh

Dockerfile 실행을  shell script로 자동화 한다. 

```sh
#!/usr/bin/env bash

docker build -t base -f Dockerfile.base .
docker build -t instagram .
docker run --rm -it -p 8013:80 instagram
```

```shell
# 실행권한 부여
$ chomod 755 docker-run.sh
# 실행
$ ./docker-run 
```



## ⛗ 도커 명령어s

```shell
# docker build 
## [현재 위치]를 기준으로 [Dockerfile]을 이용해 [새 이미지]생성
$ docker build . -t [이미지이름(태그명)]
## [현재 위치]를 기준으로 [특정 파일]을 이용해 [새 이미지]생성
$ docker build . -f [파일명] -t [이미지태그명]

# docker run: 이미지 ➔ 컨테이너 실행
## 컨테이너 내 zsh실행
$ docker --rm -it [이미지명] /bin/zsh
## 포트 연결   ✔주소창 localhost:8012 
$ docker run --rm -it -p <외부포트>:<컨테이너포트> <이미지명> <실행할 명령>
	$ docker run --rm -it -p 8012:8000 -p 8013:80 eb_docker /bin/zsh
      nginx
      /root/.pyenv/versions/app/bin/uwsgi -i /srv/app/.config/uwsgi/app.ini
# docker exec: 실행 중 컨테이너 
$ docker exec [container id] [실행할 명령]
	$ docker exec [container id] cat /etc/nginx/nginx.conf > ./nginx.conf
	$ docker exec -it [container id] /bin/zsh	


# 컨테이너 모두 삭제
$ docker rm $(docker ps -a -q)
# 이미지 모두 삭제
$ docker rmi $(docker images -q)
# <none> 이미지 삭제
$ docker rmi -f $(docker images --filter "dangling=true" -q)

# 이미지 목록 
$ docker images
# 컨테이너 목록 (실행 중이지 않은 것 포함)
$ docker ps -a
```



# ElasticBeanstalk 

[공식문서](http://docs.aws.amazon.com/elasticbeanstalk/latest/dg/Welcome.html) http://docs.aws.amazon.com/elasticbeanstalk/latest/dg/Welcome.html

Docker를 이용한 배포에 대해 최근  **ECS**를 쓰이고 있다.  여기서는 EB만을 다루기로 한다. 

[ECS( Amazon EC2 Container Service )](https://aws.amazon.com/ko/ecs/)

## IAM

`서비스` ➫ `Add user` : EB-User: ☑Programmatic access ➫ `permission`  ➫ `Attach existing policies directly` ➫ ☑ AWSElasticBeanstalkFullAccess ➫ Review ➫ `Create user` ➫  **`Access key ID`, `Secret access key`** 

```shell
# ~/.aws/credentials
[ec2]
aws_access_key_id = ************
aws_secret_access_key = ********************

[eb-user]
aws_access_key_id = *********
aws_secret_access_key = **************
```



## awsebcli

```shell
# Elastic Beanstalk 명령줄 인터페이스(EB CLI)
$ pip install awsebcli

# 애플리케이션 생성 
$ eb init --profile eb-user
#--------------------------
Select a default region 10) ap-northeast-2: Asia Pacific(Seoul)
Enter Application Name
It appears you are using Docker. Is this correct? (Y/n): Y
Select a platform versions 1) Docker 17.03.2-ce #최신버전
CodeCommit? (y/N)(default is n): n
Select a keypair 4)[ Create new KeyPair ]
Type a keypair name.(Default is aws-eb): EB-Docker-Deploy-Keypair
## ~/.ssh/EB-Docker-Deploy-Keypair.pem 

# (애플리케이션 서버 전 개발/배포)환경 생성
$ eb create --profile eb-user
# ----------------------------
Enter Environment Name: EB_Docker-Deploy-Master
Enter DNS CNAME prefix: greg_eb_docker # 고유값이어야 함 
Select a load balancer type  2)application
## .gitignore에 자동 생성 
#### Elastic Beanstalk Files
.elasticbeanstalk/*
!.elasticbeanstalk/*.cfg.yml
!.elasticbeanstalk/*.global.yml

# EB 배포
eb deploy
# 배포 확인 
eb open

# 빌드 관련 오류 보기 (EB에서 도커컨테이너 실행 중)
eb ssh
# -----
sudo docker ps
sudo docker exec -it [컨테이너 ID] /bin/zsh
(app)➡ cat /var/log/eb-activity.log
```

###  eb init /	eb create / eb deploy / eb open /eb ssh



##  `.ebignore`

​    EB는 이미지 안에 Dockerfile을 찾아 읽고,  `eb deploy`는  `git commit`한 파일들을 기준으로 배포한다.  **중요한 secret 정보**(예를들어, `.config_secret`)들은 GitHub에 공개할 수 없으므로 `.gitignore`에 담는다.   `git commit` 할 수 없으므로, EB는 위 정보들을 인식할 수 없어서 다음과 같은 Error가 발생한다. 

```sh
# EB 내 docker 환경 (➡ 위 빌드관련 오류보기 참조)
...
config_secret_common = json.loads(open(CONFIG_SECRET_FILE).read())
fileNotFoundError: [Errno 2] No Such file or directory: '/srv/app/.config_secret/settings_common.json'
```

  .ebignore를 작성하면  EB는 .gitignore를 무시한다.  문제는 .gitignore로 무시해야할 파일들도 모두 인식하므로, 

 ` .gitignore`의 내용을 모두 복사하여 붙이고,

 다음과 같이 `.ebignore`를 작성한다. 

```sh
# custom
.idea/
.static_root/
.media/

.config_secret/
# .ebignore
!.config_secret/
...
```

## RDS 인바운드 규칙 

```sh
# EB docker의 error상황
> psql --host=<RDS엔드포인트> --port=5432 --user=<사용자명> DB이름
psql: could not connect to server: Operation timed out
Is the Server running on host "<RDS엔드포인트>" and accepting 
   TCP/IP connection on port 5432?
```

AWS `서비스` > `EC2` > `보안그룹`:  자동생성된 EB 보안 그룹 `awseb-e***` 중 `설명`에서

**SecurityGroup for ElasticBeanstalk environment**이 있는 그룹ID 선택

인바운드규칙 편집 >  `PostgreSQL` `5432`  `사용자지정`  <그룹ID>



## S3 

[S3 버킷 사용하기](http://docs.aws.amazon.com/ko_kr/AmazonS3/latest/dev/UsingBucket.html) 

### IAM 권한부여

`IAM` ➔ `Users` EB-User ➔ `Add permissions` :  Attach existing policies directly :  **AmazonS3FullAccess**

###   버킷 생성  

```python
# 기존 버킷 이용하기 : 새 객체 업로드 하기 
# AWS Console에서 만들기
# CLI boto3로 만들기 
ipython
[1]: import boto3
[2]: session = boto3.Session(profile_name='eb-user')
[3]: client = session.client('s3')
[4]: client.create_bucket(Bucket='<고유명칭>', 
            CreateBucketConfiguration={
             'LocationConstraint': 'ap-northeast-2'})  
```



## ACM 

AWS 서비스 ➔ `Certificate Manager ` ➔ `인증서요청` ➔ `도메인 이름 추가`: Root 도메인을 포함하려면 2개를 입력해야함  `pikachu.kr` + `*.pikachu.kr`  ➔ `계속` : AWS로부터 이메일 2개 확인 `I Approve` ➔ 발급 완료

❐ 도메인 1개 당 월 500원 부과 

**ACM**( AWS Certificate Manager )



## Route53

❶ AWS 서비스 ➫ Route53 ➫ Hosted zones ➫ `Create Hosted Zone` ➫ `Create Record Set` ➫  **`Type NS`** 

➁ 기존 도메인이 등록된 Hosting.KR > 도메인 체크 > 도메인관리: **네임서버 주소변경**   > `신청하기`에서  위   **`Type NS`** 에 적힌 네임서버 주소들 4개를 순서대로 변경 > 변경하기 > 확인

❸ 도메인에 해당하는 IP check

[공식문서]( https://docs.aws.amazon/com/ko_kr/Route53/latest/DeveloperGuide/MigrateDNS.html)  :  https://docs.aws.amazon/com/ko_kr/Route53/latest/DeveloperGuide/MigrateDNS.html

유틸리티 dig 

```sh
$ dig <도메인 네임>
```

Hosting.KR에서 네임서버 주소변경 이메일 확인 

➃ AWS Elastic Beanstalk 환경으로 트래픽 라우팅 

위 ❶ 창에서  `Go to Record Sets`  ➫ `Create Record Set`  ➫  오른쪽 

```tex
Type:  A-IPv4 address
Alias ◉YES
Alis Target: ***.ap-northeast-2.elasticbeanstalk***
```

```sh
# IP없는 ELB가 도메인 주소를 받을 수 있는 이유: 위 Alias ◉YES의 의미 
ELB(로드밸런서) ⟜ Route53 ⟜ EC2(by EB)
```



## HTTPS 리다이렉션 

```sh
# SSL 동작원리
브라우저 ➛ 서버 : Http Connection on Port 80 요청
서버 ➛ 브라우저 : Redirection to HTTPS 요구
브라우저 ➛ 서버 :  Http Connection on Port 443 요청
서버 ➛ 브라우저 : Server Certificate 보내줌
### Connection enabled
### 'SSL/TLS인증서', 'HTTPS추가'를 하는 이유
```

###  SSL/TLS 인증서

  	위 **ACM** 참조  

### ELB에 HTTPS 추가

❺ AWS 서비스  ➫  EC2  ➫  로드밸런서  ➫  `리스너 추가`: 프로토콜 선택 **HTTPS**  아래와 같이 작성 ➫  생성 

```sh
프로토콜 HTTPS(보안 HTTP)
포트 443
기본대상그룹
기본 인증서 선택 ◉ACM에서 인증서 선택(권장)
인증서이름 ******
```

➅ EB 보안그룹 인바운드 규칙 

AWS 서비스  ➫ EC2  ➫ 보안그룹  ➫ ▣ 설명 Elastic Beanstalk created security ~~ ➫ 인바운드 규칙 편집 : HTTPS 추가 ➫ 저장 



### EB nginx 설정 

```sh
# EB의 nginx설정 
eb ssh
...
sudo vi /etc/nginx/sites-available/elasticbeanstalk-nginx-docker-proxy.conf

## .ebextensions/01_nginx_https_redirect.config
sudo service nginx restart
```

#### `.ebextensions`

검색 :  **구성 파일 (.ebextensions)을 사용하여 고급 환경 사용자 지정** 

`.ebextension/01_nginx_https_redirect.config`

위 파일은 EB에서 `.ebextension/`의 숫자 순서대로 실행시켜준다. 

아래 파일을 읽는 법은  [공식문서]( https://docs.aws.amazon.com/ko_kr/elasticbeanstalk/latest/dg/customize-containers-ec2.html)를 참조할 것 

```yaml
# .ebextension/01_nginx_https_redirect.config
# EB 안에 현재 파일을 다음과 같은 파일로 생성하겠다. 
files:
  "/etc/nginx/sites-available/elasticbeanstalk-nginx-docker-proxy.conf":
# 권한 설정   
  mode: "00644"
# 소유자, 그룹 설정   
  owner: root
  group: root
  content: |
map $http_upgrade $connection_upgrade {
    default        "upgrade";
    ""            ""; }
# 서버 설정 
server {
   listen 80;

   gzip on;
   gzip_comp_level 4;
   gzip_types text/html text/plain text/css application/json application/x-javascript text/xml application/xml application/xml+rss text/javascript;

  if ($time_iso8601 ~ "^(\d{4})-(\d{2})-(\d{2})T(\d{2})") {
      set $year $1;
      set $month $2;
      set $day $3;
      set $hour $4;
  }
 access_log /var/log/nginx/healthd/application.log.$year-$month-$day-$hour healthd;
 access_log    /var/log/nginx/access.log;

 location / {
   proxy_pass   http://docker;
   proxy_http_version  1.1;
   proxy_set_header  Connection  $connection_upgrade;
   proxy_set_header  Upgrade     $http_upgrade;
   proxy_set_header  Host        $host;
   proxy_set_header  X-Real-IP   $remote_addr;
   proxy_set_header  X-Forwarded-For $proxy_add_x_forwarded_for;
 }
 # 수정한 부분: http요청이 들어오면 https로 요청할 것을 리다이렉션하는 부분 
 if ($http_x_forwarded_proto = 'http') {
    return 301 https://$host$request_uri;
 }
}
```








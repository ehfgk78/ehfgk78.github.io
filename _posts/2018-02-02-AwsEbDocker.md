---
layout: post
title:  "AWS ElasticBeanstalk( Docker플랫폼 )으로 Django 배포하기"
date:  2018-02-02 02:20 +0900
categories: [Django, AWS, Docker]
tags: [ Django, AWS, Docker]
---

* Kramdown table of contents
{:toc .toc}
<br/>
<br/>

---

# 참조 

Docker 공식 문서 [ https://docs.docker.com/get-started/ ](https://docs.docker.com/get-started/) 

[초보를 위한 도커 안내서 (subicura)](https://subicura.com/2017/01/19/docker-guide-for-beginners-1.html)

[이한영 강사의 블로그](https://lhy.kr/eb-docker) 

[나채원 블로그](https://nachwon.github.io/django/2017/11/01/django-deploy-8-docker.html)

[Get Docker CE for Ubuntu](https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/#uninstall-old-versions)

[Install Docker for Mac](https://docs.docker.com/docker-for-mac/install/#download-docker-for-mac)

[AWS 전문가 되기](https://brunch.co.kr/magazine/devops)

<br/>

<br/>

---

# 0. 서버의 전체 구성

<br/>

![AWS_Elastic_Beanstalk_IP_flow]({{ site.url }}/data/AwsEB/0-AWS_EB_IP_flow.png) 

**그림**_도커 플랫폼 AWS  Elastic Beanstalk의 서비스 구성 

<br/>

![AWS_Elastic_Beanstalk_IP_flow]({{ site.url }}/data/AwsEB/0-AWS_structure2.png) 

**그림**_AWS_서비스 구성 

<br/> 

<br/>

---

# 1. 도커 
<br/>
​	도커(Docker)는 리눅스 응용프로그램들을 **소프트웨어 컨테이너** 안에 배치시키는 일을 자동화하는 오픈 소스 프로젝트이다.  

* 도커 컨테이너( **Container** )는 **<u>어떤 소프트웨어와 이를 실행하는데 필요한 모든 것을 완전한 파일 시스템 안에 감싼다.</u>**   
* 감싸는 것들에는 코드, 런타임, 시스템 도구 , 시스템 라이브러리 등 서버에 설치되는 무엇이든 아우른다.  
* 도커의 이러한 기능은 **<u>실행 중인 환경과 관계없이 언제나 동일하게 실행될 것을 보증</u>**한다. 


<br/>
이 포스트에서는, 

*  **Docker Container**  안에서  Django 실행에 필요한 모든 것들 ( Ubuntu ➜ zsh( + oh-my-zsh ➜ pyenv ➜ python 3.6.3 ➜ Django ➜ uWSGI + Nginx 등)을 설치하여 실행해보고, 
*  이를  **Docker image**로 만들어  **Docker Hub**에 업로드할 것이다. 
*  이후  **AWS ElasticBeanstalk**을 이용하여 **Django를 자동으로 배포**할 것이다.  
*  Docker image는  **Dockerfile**을 이용하여 자동으로 생성(Build)되도록 할 것이다. 

크게 <u>Dockerfile을 만드는 작업</u>과  <u>AWS ElasticBeanstalk(도커플랫폼) 환경을 만드는 작업</u> 두 부분에 중점을 두어 이 포스트를 작성하였다. 

<br/>
<br/>

---

## 1) 도커 설치 ⚿

> ✛ [curl](https://curl.haxx.se/)  + [curl 설치 및 사용법 - HTTP GET/POST, REST API 연계등](https://www.lesstif.com/pages/viewpage.action?pageId=14745703)

> ✛ [커맨드라인 환경에서 REST API (HTTP) 요청 보내기 (cURL, resty, httpie, Vim REST Control)](https://bakyeono.net/post/2016-05-02-rest-api-client-for-cli.html)  



<br/>

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

<br/>

<br/>

---

## 2) Ubuntu16.04 이미지    

<br/> 


1) 문법 - docker image 다운로드 및 docker container 실행
*  {% raw %} **docker  run [옵션s]  이미지[:TAG][ COMMAND]  [ARG ...]** {% endraw %}
*  옵션 설명
   * `--rm`  프로세스 종료시 해당 이미지의 컨테이너 자동 제거
   * `-it`  도커 컨테이너 내의 실행을 현재 터미널에서 입력(interactive terminal)
   * `-p <host 포트>:<container 포트>`  포트 연결(포워딩)
   * ` -d` 백그라운드 모드, `-e`컨테이너 내 환경변수 설정, `--link`컨테이너연결
   * `--name` 컨테이너 이름 설정 
   * ` -v`  호스트와 컨테이너의 디렉토리 연결(마운트)


2) 실제 **실행** 을 해보면 다음과 같다. 

```sh
# ubuntu 이미지 검색
docker search ubuntu:16.04
# 이미지 다운로드:: docker pull [옵션] 이미지[:태그명]
# 다운로드와 동시에 container실행은 아래처럼 
docker run ubuntu:16.04

# 컨테이너 내 우분투 운영체제 내장 bash 실행
docker run --rm -it ubuntu:16.04 bin/bash
```

<br/>

<br/>

---

## 3) `Dockerfile.base`  ⛺

<br/> 

* **Pycharm Plugin -  Docker Integration** :  pycharm을 통합개발IDE로 사용한다면 위 플러그인은  Dockerfile을 인식하고 Dockerfile의 문법을 자동으로 완성해주는 기능을 제공한다. 
* **Dockerfile.base,  Dockerfile.local, Dockerfile로 나누어 작성하는 이유**가 중요하다. 
  *  첫째는 웹 개발 단계에서 로컬에서 개발하다가 AWS 등에 배포하는 단계로 넘어가는데  각 단계마다 프로그램 환경이 다르기 때문에  그에 맞추어 Dockerfile을 작성해야할 필요성이 있다.  
  *  둘째는 AWS ElasticBeanstalk(줄여서 'EB'라고 표현하겠다.)에  Django를 배포하는 과정(`eb deploy`)에서 EB는 Dockerfile을 참조하여 Docker 환경을 구축(빌드, build)한다.  이 경우 OS 설치(여기서는 우분투16.04LTS)부터 빌드하면  배포시간이 너무 길어진다.  그 시간을 줄이기 위해서 Dockerfile.base로 구축한 Docker image를  Docker Hub를 통하여 이용하면, EB는 Dockerfile을 통하여 Django 배포에만 집중할 수 있다.   
  *  Dockerfile.base에서 구축하는 환경은 공개할 수 있지만  Dockerfile에서 구축하는 Django 환경은 비공개로 해야한다는 점에서 위 방법은 타당하며, 앞으로의 전개는 이러한 공개/비공개 이슈를 적정하게 반영할 것이다. 
* Dockerfile.base의 작성은 아래와 같다.  명령을 한 줄씩 실행하며 Test를 하고 Dockerfile.base를 완성시킨다.  구축 환경의 내용을 살펴보면 공개해도 되는 내용들임을 알  수 있다. 

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

<br/>

* **base 이미지 빌드** 
  * 문법  **docker build  [빌드위치]  -f [도커파일이름]  -t [이미지 태그이름]** 
  * 현재 디렉토리(.)에  Dockerfile.base에 따라 gildong/base 이미지를 빌드하는 도커 명령어는 다음과 같다. 

  ```sh
  ➜  docker build . -f Dockerfile.base -t gildong/base
  ```


<br/>

* **이미지 레이어** -  다른 가상머신(Virtual Machine)과 다르게, Docker가 유연하고 가벼운 이유가 이미지 레이어이다.  

  * 위 명령어를 실행하면 아래와 같은 로그가 나타난다. 

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
  * 위 로그의 각 단계(Step 1/19,  Step 2/19, ... 등)별로 이미지 레이어가 만들어진다.  위 로그에 대한 설명는 아래 테이블에 정리한다. 

  | 번호      | 설명                                       |
  | ------- | ---------------------------------------- |
  | **\#1** | 처음 빌드를 시작하면 명령을 실행한 디렉토리의 파일들 (Build contest) 을 Docker 서버로 전송한다. |
  | **\#2** | FROM ubuntu:16.04 명령을 실행한다.              |
  | **\#3** | 위 명령의 실행 결과를 이미지 747cb2d60bbe 로 만든다. 이 경우에는 ubuntu:16.04 이미지를 가져오는 것이므로 우분투 이미지의 ID인 747cb2d60bbe이 그대로 표시된다. |
  | **\#4** | MAINTAINER 명령을 실행한다.                     |
  | **\#5** | 위 명령을 4fd03f3be194 라는 임시 컨테이너를 만들어 그 안에서 실행한다. |
  | **\#6** | 명령의 실행 결과를 이미지 feabda9f3e02 로 저장한다.      |
  | **\#7** | 명령 실행을 위해 만들었던 임시 컨테이너 4fd03f3be194를 삭제한다. |
  | **\#8** | 가장 마지막 명령을 실행한 결과로 생성된 이미지는 975e4869f7c3 이다. 이 이미지가 최종 결과인 base 이미지인 것이다. |
  | **\#9** | base 이미지에 latest 라는 태그를 붙여준다.            |

  ​

  *  임시 컨테이너 생성  ➔  다음 명령 실행  ➔  **실행 결과를 이미지로 저장**  ➔ 임시 컨테이너 삭제 
  *  이렇게  한 번의 반복으로 생성되는 이미지를  **이미지 레이어**라고 한다.  각 명령의 실행결과는 **레이어 단위**로 저장되므로 <u>설치과정을 처음부터 다시 할 필요없고, 특정 시점의 레이어로 돌가갈 수 있다.</u>


<br/>

<br/>

---

## 4) Docker Hub 

<br/>

 Docker Hub는 Docker image를 공개적으로 저장하는 원격 저장소이다. 

* Docker Hub를 알아야 하는 이유 

  * **AWS Elastic Beanstalk**은 Dockerfile과 git commit을 기준으로 배포한다. EB가 Dockerfile의 base 이미지를 인식할 수 있도록 공개저장소인 Docker Hub에 업로드해야한다. 
  * 공개 원격저장소인 DockerHub 대신 **Docker-Registry**를 사용할 수 있으나  이 포스트에서는 설명하지 않는다. 

  ```dockerfile
  FROM  <Docker Hub 사용자 계정>/<이미지 이름>:<태그>
  ... 
  ```

  ​


* Docker Hub에 회원 가입 후  로그인 

```sh
➔ docker login
Login with your Docker ID to push and pull images from Docker Hub. 
If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username (****): ******
Password: 
Login Succeeded
```

<br/>

* Docker Hub에  Docker image 올리기 

```sh
# base이미지에 새 태그명 azelf/base를 붙인다
➔ docker tag base azelf/base

# azelf/base저장소에 해당 이미지를 push
➔ docker push <Docker Hub 사용자 계정>/<이미지 이름>:<태그>
➔ docker push azelf/base
```

<br/>

<br/>

---

###  ✔  Django settings

<br/>



[Django Settings 환경설정 분리]({% post_url 2018-02-03-Django-Settings_Requirements %}) 참조  

```sh
<project container>
 ├─ .config_secret
 │    ├─ settings_common.json
 │    ├─ settings_local.json
 │    └─ settings_dev.json
 └─ <Django project>
      └─ config/
          ├─ settings/
          │   ├─ __init__.py  
          │   ├─ base.py 
          │   ├─ local.py
          │   ├─ dev.py 
          │   └─ deploy.py
          ├─ wsgi/
          │   ├─ __init__.py
          │   ├─ local.py
          │   └─ dev.py
          └─ urls # media_root 설정 
```

<br/>

### ✔  local Nginx, uWSGI 설정

* [Django Settings 환경설정 분리]({% post_url 2018-02-03-Django-Settings_Requirements %}) 참조  
* **전체 모습** 

![local Nginx, uWSGI 설정]({{ site.url }}/data/AwsEB/0-local_nginx_uwsgi.png)

<br/>

<br/>

---

## 6) `Dockerfile.local` ⛺  

<br/>

* base 이미지로부터 시작한다.  로컬 환경에서 빌드하므로 Docker Hub의 이미지를 쓸 필요 없다. 
* local DataBase는 sqlite3로 한다.  postgresql은 마이스레이션에서  문제가 발생하는 경향이 있다. 

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
RUN  /root/.pyenv/versions/app/bin/python manage.py collectstatic --noinput
RUN  /root/.pyenv/versions/app/bin/python manage.py migrate --noinput

# 개방 port
EXPOSE  80 8012
```

<br/>

<br/>

---

## 7) `Dockerfile.dev`  ⛺ 

<br/>

* 도커파일은 개발 단계마다 다르게 설정된다. 아래 예시 파일에서 기본적인 사용법과 의미를 파악하는데에 주력한다.  단순히 복사/붙이기를 해서는 안된다. 

```dockerfile
FROM  Greg/base
MAINTAINER  Greg@email.com

ENV  LANG C.UTF-8
ENV  DJANGO_SETTINGS_MODULE=config.settings.dev

# 파일 복사 및 requirements 설치 - pip update용
## 주의! .config_secret등 비밀설정파일이 복사되므로 비밀공개에 주의해야 한다. 
COPY  . /srv/app
RUN  /root/.pyenv/versions/app/bin/pip install \
     -r  /srv/app/requirements.txt

# pyenv local 설정
WORKDIR  /srv/app
RUN  pyenv local app

# NginX
RUN cp /srv/app/.config/dev/nginx/nginx.conf /etc/nginx/nginx.conf
RUN cp  /srv/app/.config/dev/nginx/app.conf /etc/nginx/sites-available/

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
RUN cp /srv/app/.config/dev/supervisor/* /etc/supervisor/conf.d
CMD  supervisord -n

# docker의 port개방::  Nginx 설정파일 app.conf 주의!
## server {
##     listen 80;
## }
EXPOSE  80 8012
```

<br/>

<br/>

---

## 8)  `Dockerfile.deploy`  ⛺

<br/> 

```dockerfile
FROM  Greg/base
MAINTAINER  Greg@email.com

ENV  LANG C.UTF-8
ENV  DJANGO_SETTINGS_MODULE=config.settings.deploy

# 파일 복사 및 requirements 설치 - pip update용
## 주의! .config_secret등 비밀설정파일이 복사되므로 비밀공개에 주의해야 한다. 
COPY  . /srv/app
RUN  /root/.pyenv/versions/app/bin/pip install \
     -r  /srv/app/requirements.txt

# pyenv local 설정
WORKDIR  /srv/app
RUN  pyenv local app

# NginX- deploy
RUN cp /srv/app/.config/deploy/nginx/nginx.conf /etc/nginx/nginx.conf
RUN cp  /srv/app/.config/deploy/nginx/app.conf /etc/nginx/sites-available/

RUN  rm -rf /etc/nginx/sites-enabled/*
RUN  ln  -sf /etc/nginx/sites-available/app.conf \
         /etc/nginx/sites-enabled/app.conf
# uWSGI
RUN  mkdir -p  /var/log/uwsgi/app
# manage.py
WORKDIR  /srv/app/mysite
RUN  /root/.pyenv/versions/app/bin/python manage.py collectstatic --noinput
RUN  /root/.pyenv/versions/app/bin/python manage.py migrate --noinput
# supervisor-deploy
RUN cp /srv/app/.config/deploy/supervisor/* /etc/supervisor/conf.d
CMD  supervisord -n

# docker의 port개방::  Nginx 설정파일 app.conf 주의!
## server {
##     listen 80;
## }
EXPOSE  80 8012
```



<br/>

<br/>

---

## 9)  `docker-run.sh`  ⏬ 

<br/>

* docker-run.sh 파일 - Dockerfile 실행을  shell script로 자동화 한다. 

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

<br/>

<br/>

---

## 10) 도커 명령어 모음  

<br/>

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

<br/>

<br/>

# 2. 도커 플랫폼(ElasticBeanstalk)  

<br/>

> [AWS 공식문서- Docker 컨테이너에서 Elastic Beanstalk 애플리케이션 배포](https://docs.aws.amazon.com/ko_kr/elasticbeanstalk/latest/dg/create_deploy_docker.html) 

최근 공식문서의 한글화가 많이 진행되어  영문으로 읽던 고통이  해소되었다.   한글화가 이루어지더라도  기술문서는 어느 정도 알아야 읽힌다는 점은 어쩔 수 없다. 

 *  Elastic Beanstalk 초보자를 위해서 AWS는 [시작 자습서](https://docs.aws.amazon.com/ko_kr/elasticbeanstalk/latest/dg/GettingStarted.html)를 마련하였으나 마찬가지로 어느 정도 알아야 자습이 가능하다.   알면 읽을 필요 없다는 점이 위 자습서의 아이러니한 운명이다. 
 *  우리가 구체적으로 다뤄볼 주제는 [ (Dockerfile을 사용하여) 미리 구성된 Docker 컨테이너](https://docs.aws.amazon.com/ko_kr/elasticbeanstalk/latest/dg/create_deploy_dockerpreconfig.html)이다.  위 링크의 설명만으로 충분하지 않아서  이 포스트를 작성해 보았다. 
 *  이 포스트를 작성하는 데에 [이한영 강사](https://lhy.kr/)로부터 배운 내용임을 미리 밝혀둔다. (거의 모든 포스팅은 이한영 강사로부터 배운 것이다) 




Elastic Beanstalk을 사용하는 이유 -  백엔드 개발자는 서버환경을 구축하고 웹 애플리케이션을 개발하는 역할을 하였다.  문제는 이 모두를 잘 다루는데  많은 시행착오와 시간을 들여야 한다는 점이다.   AWS의  Elastic Beanstalk(이하 EB로 약칭한다)을 사용하면 서버 구축/유지 관리에 드는 노력을 줄이고  웹 애플리케이션 개발에 집중할 수 있다. 

 * EB는  Docker를 실행할 EC2,  데이터베이스 RDS,  파일 스토리지 S3 등을 연결해주고,  ELB로 EC2의 트래픽 등을 점검하다가 트래픽이 넘치면 자동으로 EC2를 추가해주는 Auto Scaling 기능을 알아서 설정해준다. 
 * 개인적인 의견은  EB 구축과정이  서버 구축만큼 어려운 것 같다. (공부할 것이 많다) 

<br/>

>  [ECS( Amazon EC2 Container Service )](https://aws.amazon.com/ko/ecs/)
>
>  참고로 Docker를 이용한 배포에 대해 최근  **ECS**를 쓰이고 있다.  

<br/>

## 1) IAM

* 과정 요약 -  EB 유저 생성,  Access Key ID 및 Secret access key

  `서비스` ➫ `Add user` : EB-User: ☑Programmatic access ➫ `permission`  ➫ `Attach existing policies directly` ➫ ☑ AWSElasticBeanstalkFullAccess ➫ Review ➫ `Create user` ➫  **`Access key ID`, `Secret access key`** 

  <br/>

* 권한 설정 부분 -  AWSElasticBeanstalkFullAccess 

![AWSElasticBeanstalkFullAccess ]({{ site.url }}/data/AwsEB/0-IAM_AWSElasticBeanstalkFullAccess.png)

<br/>

* Access_Key _ID 및 Access_Secret_Key 저장하기 
  * [유저명] 기입 후 해당 유저에 맞는 값들을 저장한다. 

```shell
➜  vim ~/.aws/credentials

[ec2]
aws_access_key_id = ************
aws_secret_access_key = ********************

[eb-user]
aws_access_key_id = *********
aws_secret_access_key = **************
```

<br/>

<br/>

## 2) EB CLI

> [AWS 공식문서 - Elastic Beanstalk 명령줄 인터페이스(EB CLI)](https://docs.aws.amazon.com/ko_kr/elasticbeanstalk/latest/dg/eb-cli3.html)
>
> **EB CLI** (Elastic Beanstalk Command Line Interface)는  GUI 방식의  AWS Management Console 대신 <u>텍스트 터미널을 통하여 AWS EB 환경을 만들고, 관리하는 등 상호 작용을 하는 명령줄 클라이언트</u>를 말합니다.   참고로 Python으로 개발되었다.  
>
> <br/>
>
> * [명령어 리스트 - AWS공식문서](https://docs.aws.amazon.com/ko_kr/elasticbeanstalk/latest/dg/eb3-cmd-commands.html) 
>
> | 기본 명령어         | 설명                           |
> | -------------- | ---------------------------- |
> | `eb create`    | 환경 생성                        |
> | `eb deploy`    | `Dockerfile`을 사용하여 환경 업데이트   |
> | `eb open`      | 브라우저에서 EB환경이 열림              |
> | `eb status`    | 현재 상태 보기 , `git status`와 비슷함 |
> | `eb health`    | Linux의 ps, top와 비슷함          |
> | `eb events`    | EB의 이벤트 출력                   |
> | `eb logs`      | 인스턴스의 로그기록을 가져옴              |
> | `eb config`    | 환경 구성 옵션 보기                  |
> | `eb terminate` | 환경 종료                        |





<br/>

❶  [설치 / 제거 - AWS 공식문서](https://docs.aws.amazon.com/ko_kr/elasticbeanstalk/latest/dg/eb-cli3-install.html) 

```sh
# 1.설치 명령
➜  pip install awsebcli --upgrade --user

# 2. PATH 환경변수에 실행파일 경로 추가 
# 2-1. 사용하는 SHELL 알기 
➜  echo $SHELL
/usr/bin/zsh
# 2-2. .zshrc(zsh의 프로파일 스크립트 파일)에서 export
➜  vim ~/.zshrc
export PATH=~/.local/bin:$PATH
# 2-3. 프로파일 스크립트를 현재 세션에 로드하기
➜  source ~/.zshrc

# 3. EB CLI 설치 확인
➜  eb --version
EB CLI 3.12.0 (Python 3.6.2)

# 4. 제거
➜  pip uninstall awsebcli
```

<br/>

❷  [EB CLI 및 프로젝트 구성](https://docs.aws.amazon.com/ko_kr/elasticbeanstalk/latest/dg/eb-cli3-configuration.html)  `➜ eb init --profile [EB유저명]`

* **--profile** 방식 [명명된 프로파일 사용] 
* `git init`과 비슷하다. 
* [EB CLI이 Git과 통합된다.](https://docs.aws.amazon.com/ko_kr/elasticbeanstalk/latest/dg/eb3-cli-git.html) 

```sh
➜ eb init --profile eb-user

#--------------------------
Select a default region 10) ap-northeast-2: Asia Pacific(Seoul)
Enter Application Name
It appears you are using Docker. Is this correct? (Y/n): Y
# 1) 최신버전을 고른다. 
Select a platform versions 1) Docker 17.03.2-ce 
# CodeCommit은 사용하지 않는다. 
CodeCommit? (y/N)(default is n): n
# 키페어의 위치는 ~/.ssh/EB-Docker-Deploy-Keypair.pem 전제로 한다. 
Select a keypair 4)[ Create new KeyPair ]
Type a keypair name.(Default is aws-eb): EB-Docker-Deploy-Keypair
```

<br/>

❸  EB 환경 생성  `➜ eb create`

```sh
➜ eb create --profile eb-user
# ----------------------------
Enter Environment Name: EB_Docker-Deploy-Master
# 아래 CNAME은 고유해야 함
Enter DNS CNAME prefix: greg_eb_docker  
Select a load balancer type  2)application
```

* 위 명령 실행 결과 `.gitignore` 에 다음과 같이 추가편집된다. 

```
## .gitignore에 자동 생성 
#### Elastic Beanstalk Files
.elasticbeanstalk/*
!.elasticbeanstalk/*.cfg.yml
!.elasticbeanstalk/*.global.yml
```

<br/>

❹  배포 -  기본적으로 git commit을 기준으로  배포한다.  

* 그러나 settings에서 분리된 .config_secret을  git의 추적에서 배제(.gitignore에 `.config_secret/`)하였으므로 배포에 실패하게 된다. 
* 따라서 아래 3)의  .ebignore 작성 과정에서  위 .config_secret 디렉토리를 EB가 인식할 수 있도록 처리해야 한다. 
* **주의하자!!! -**  .ebignore가 작성된 이상  EB는 .gitignore를 무시하므로 더 이상 git commit을 기준으로 배포하지 않는다. 

```shell
➜ eb deploy
```

<br/>

❺  배포 확인하기 - 아래 명령을 하면 기본 웹 브라우저에 뜬다. 

```sh
➜ eb open
```

<br/>

❻  빌드 관련 오류 보기 - EB 안에서 작동하는 도커에 ssh 통신으로 접속한다.  도커 컨테이너 내부에서  `eb-activity.log`기록을 살펴볼 수 있다. 

```sh
# 빌드 관련 오류 보기 (EB에서 도커컨테이너 실행 중)
➜ eb ssh
# -----
➜ sudo docker ps
➜ sudo docker exec -it [컨테이너 ID] /bin/zsh
(app)➡ cat /var/log/eb-activity.log
```

<br/>

<br/>

##  3) `.ebignore`

> [AWS 공식문서 - EB CLI 구성 - .ebignore를 사용하여 파일 무시](https://docs.aws.amazon.com/ko_kr/elasticbeanstalk/latest/dg/eb-cli3-configuration.html) 
>
> * 여기에 설명이 무척 잘 되어 있습니다. 
>
> `.ebignore` 파일을 프로젝트 디렉터리에 추가하여 EB CLI가 디렉터리의 특정 파일을 무시하도록 명령할 수 있습니다. 이 파일은 `.gitignore` 파일처럼 작동합니다. 프로젝트 디렉터리를 Elastic Beanstalk에 배포하고 새 애플리케이션 버전을 생성하는 경우, EB CLI에는 이렇게 생성되는 소스 번들의 `.ebignore`에서 지정한 파일이 포함되지 않습니다.
>
> `.ebignore`가 없고 `.gitignore`가 있는 경우, EB CLI는 `.gitignore`에 지정된 파일을 무시합니다. `.ebignore`가 있으면 EB CLI는 `.gitignore`를 읽지 않습니다.
>
> `.ebignore`가 있으면 EB CLI는 git 명령을 사용하여 소스 번들을 생성하지 않습니다. 즉 EB CLI는 `.ebignore`에 지정된 파일을 무시하고 다른 모든 파일을 포함시킵니다. 특히 커밋되지 않은 소스 파일을 포함시킵니다.

<br/>

* .ebignore를 작성해야 하는 이유
  * ​ `eb deploy`(EB CLI 중 하나)는  `git commit`한 파일들을 기준으로 배포한다.  **중요한 secret 설정 정보**(예를들어, `.config_secret`)들은 GitHub에 공개할 수 없는 비공개 정보이므로 `.gitignore`를 통하여 git의 추적에서 벗어나야 한다.  
  * git의 추적으로 벗어난 결과   git commit을 기준으로 배포하는 `eb deploy`가 위 설정 정보를 읽지 못하게 되므로 아래와 같이 배포에 실패한다. 
  * .ebignore의 역할은  .config_secret 디렉토리의 파일들만 제외하고 .gitignore에서 무시한 파일들을 역시 무시해야한다. 
* .ebignore 작성 전 Error의 모습  

```sh
# EB 내 docker 환경 (➡ 위 빌드관련 오류보기 참조)
➜ eb ssh
# -----
➜ sudo docker ps
➜ sudo docker exec -it [컨테이너 ID] /bin/zsh
(app)➡ cat /var/log/eb-activity.log

...
config_secret_common = json.loads(open(CONFIG_SECRET_FILE).read())
fileNotFoundError: [Errno 2] No Such file or directory: '/srv/app/.config_secret/settings_common.json'
```

<br/>

*  .ebignore에서 작성할 내용 

  * .ebignore를 작성하면 EB는 .gitignore를 무시한다.  즉, EB는  .gitignore로 무시해야할 파일 모두를 인식하게 된다. 
  * 그러나 이 단계의 목표는  EB가 .config_secret/ 디렉토리의 설정 파일들에 담긴 설정 정보만  인식할 수 있게 처리하는 것이다. 
  * 따라서  ❶  ` .gitignore`의 내용을 모두 복사하여 붙이고,  ❷ .config_secret/에 대해서만 아래와 같이 작성한다. 

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

<br/>

<br/>

## 4) RDS 인바운드 규칙 

> 

앱/웹 개발자는 애플리케이션의 소스코드를 가장 소중하게 여기지만 서비스 운영자나 경영자의 입장에서 가장 중요한 자산은 고객들의 정보를 담은 데이터베이스이다.  

데이터베이스만 지킬 수 있으면  어플리케이션 소스코드는 개발자에게 맡겨서 복구할 수 있지만  한번 잃어버린 데이터베이스는 복구할 수 없다.  따라서 업무 현실을 살펴보면 DB를 날린 은행과 보험사 사건은 단연 톱 뉴스 감이 되고, DBA가 개발자보다 더 높은 연봉을 받는 경우가 많다. 

따라서  보안상 데이터베이스에 대한 접근을 가장 엄격하게 다루어야 한다.    어떤 DB관리자는 DB작업을 할 때 목욕을 하고 경건한 마음으로 작업한다고 말한다. 

AWS는 이러한 DB의 보안을 위해 **RDS 보안그룹 인바운드규칙**에서  `내 IP` 주소로부터 오는 요청만 허용하고 있다. 이하  ➀ 인바운드 규칙 설정 전의 오류 상황과 ➁ 인바운드 규칙에 EC2( Django 앱이 도커 컨테이너에서 작동하고 있고 RDS로의 연결이 필요하다.)의  보안그룹을 추가하는 방법을 설명한다. 

<br/>

* 오류 상황 - 인바운드 규칙 설정 전 

```sh
# EB docker의 error상황
> psql --host=<RDS엔드포인트> --port=5432 --user=<사용자명> DB이름

psql: could not connect to server: Operation timed out
Is the Server running on host "<RDS엔드포인트>" and accepting 
   TCP/IP connection on port 5432?
```

<br/>

* 인바운드 규칙에  EC2 보안그룹을 추가하기 

  ![EC2 security Group]({{ site.url }}/data/AwsEB/0-inbound_securityGroup2.png)

  <br/>

  * AWS `서비스` > `EC2` > `보안그룹`  중 **SecurityGroup for ElasticBeanstalk environment**을 선택 

  ![SecurityGroup for ElasticBeanstalk environment]({{ site.url }}/data/AwsEB/0-inbound_securityGroup4.png)

  <br/>

  * 인바운드규칙 편집 >  `PostgreSQL` `5432`  `사용자지정`  <그룹ID>

  ![RDS inbound]({{ site.url }}/data/AwsEB/0-inbound_RDS.png)

<br/>

<br/>

## 3) S3 

>  [S3 버킷 사용하기](http://docs.aws.amazon.com/ko_kr/AmazonS3/latest/dev/UsingBucket.html) 

<br/>

1)  **IAM 권한부여 ** 

`IAM` ➔ `Users` EB-User ➔ `Add permissions` :  Attach existing policies directly :  **AmazonS3FullAccess**

<br/> 

2)  **버킷 생성** 

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

<br/>

<br/>



## 4) ACM 

<br/>

AWS 서비스 ➔ `Certificate Manager ` ➔ `인증서요청` ➔ `도메인 이름 추가`: Root 도메인을 포함하려면 2개를 입력해야함  `pikachu.kr` + `*.pikachu.kr`  ➔ `계속` : AWS로부터 이메일 2개 확인 `I Approve` ➔ 발급 완료

❐ 도메인 1개 당 월 500원 부과 

**ACM**( AWS Certificate Manager )

<br/>

<br/>

## 5) Route53

<br/>

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
Route53 ⟜ ELB(로드밸런서) ⟜ EC2(by EB)
```

<br/>

<br/>

## 6) HTTPS 리다이렉션 

<br/>

1)  **SSL 동작원리 및 SSL/TLS인증서,  HTTPS 추가를 하는 이유** 

```sh
# SSL 동작원리
브라우저 ➛ 서버 : Http Connection on Port 80 요청
서버 ➛ 브라우저 : Redirection to HTTPS 요구
브라우저 ➛ 서버 :  Http Connection on Port 443 요청
서버 ➛ 브라우저 : Server Certificate 보내줌
### Connection enabled
### 'SSL/TLS인증서', 'HTTPS추가'를 하는 이유
```

<br/>

2)  **SSL/TLS 인증서** 

  	위 **ACM** 참조  

<br/>

3)  **ELB에 HTTPS 추가** 

* ❺ AWS 서비스  ➫  EC2  ➫  로드밸런서  ➫  `리스너 추가`: 프로토콜 선택 **HTTPS**  아래와 같이 작성 ➫  생성 

```sh
프로토콜 HTTPS(보안 HTTP)
포트 443
기본대상그룹
기본 인증서 선택 ◉ACM에서 인증서 선택(권장)
인증서이름 ******
```

* ➅ EB 보안그룹 인바운드 규칙 
  * AWS 서비스  ➫ EC2  ➫ 보안그룹  ➫ ▣ 설명 Elastic Beanstalk created security ~~ ➫ 인바운드 규칙 편집 : HTTPS 추가 ➫ 저장 

<br/>

4)  **EB nginx** 

* 설정 

```sh
# EB의 nginx설정 
eb ssh
...
sudo vi /etc/nginx/sites-available/elasticbeanstalk-nginx-docker-proxy.conf

## .ebextensions/01_nginx_https_redirect.config
sudo service nginx restart
```



* **`.ebextensions`** 

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

<br/>

<br/>

---

# 3. AWS ELB - Django App  Healthcheck 문제

> [Gotcha 블로그 - 5 Gotchas with Elastic Beanstalk and Django](https://hashedin.com/5-gotchas-with-elastic-beanstalk-and-django/)

> [이한영 블로그 - Elastic Beanstalk의 Django 애플리케이션에서 발생하는 ELB Health check 4xx에러 해결](https://lhy.kr/elb-healthcheck-for-django)  

>  [Host Header Attack](https://www.acunetix.com/blog/articles/automated-detection-of-host-header-attacks/) 



이 포스트 중 Host Header Attack에 대한 설명은  [Host Header Attack](https://www.acunetix.com/blog/articles/automated-detection-of-host-header-attacks/) 의 일부를 발췌한 내용입니다.  ELB  Health check 4xx에러는 위 [이한영 블로그](https://lhy.kr/elb-healthcheck-for-django)를 참고하였습니다.  위 에러의 해결을 담은 포스트는  [Gotcha 블로그](https://hashedin.com/2017/01/06/5-gotchas-with-elastic-beanstalk-and-django/)를 참조하였습니다. 

<br/>

## 1) 문제점

**하나**의 IP주소를 가지는 웹서버가 **여러** 웹사이트와 웹어플리케이션을 제공하는 것이 일반적이므로, 웹서버가 특정 웹사이트에 대한 사용자의 HTTP 요청에 적정하게 응답하려면 **Host Header의 정보** ( *웹서버의 여러 웹사이트 중 사용자가 찾는 웹사이트의 위치가 담긴 정보* )가 필요하다.  그런데 이 Host Header의 정보를 <u>사용자가 제어할 수 있습니다.</u>  따라서 웹개발자는 다음 문장을 항상 명심해야 합니다. 

> in application security user input should **always** be considered **unsafe** and therefore, **never**trusted without properly validating it first.  (애플리케이션 영역에서 사용자의 입력은 항상 안전하지 않고, 유효할 것이라고 신뢰해서는 안된다.) 



![Web Cache poisoning]({{ site.url }}/data/AwsEB/0-http_host_header_attack.png) 

[ 그림.  Web-cache poisoning ]

인터넷 보안 문제 중 **HTTP Host Header attack**은  공격자가 사용자와 웹서버 사이에서 사용자의 HTTP요청에 들어있는 **Host Header의 정보**를 이용하거나 망가뜨리는 방법으로 사용자의 비밀번호 재설정 정보를 망가뜨리거나(Password Reset Poisoning), 웹 캐시 데이터를 망가뜨리는 기술(Web-cache poisoning)입니다.

구체적으로  Password Reset Poisoning을 살펴보자.  웹사이트를 만들 때  웹개발자가  Host Header의 값을 사용하여 비밀번호 재설정 링크를 만들면,  공격자는 웹서버가 피해자에게 전송한 위 링크를  공격 할 수 있습니다. 피해자가 웹서버가 보내온 e메일에서 오염된 링크를 클릭하는 순간 공격자는 피해자의 암호 재설정 토큰을 탈취하여 피해자의 암호를 재설정 할 수 있습니다.



이에 대한 대응방안으로  Django를 비롯한 여러 웹 개발 프레임워크는 **HTTP Host Header attack 보안 공격**을 방지하기 위해서  `settings.ALLOWED_HOST`의 white list를 이용할 것을 권고한다. 

>  [Django-Host header validation](https://docs.djangoproject.com/en/1.11/topics/security/#host-header-validation) 
>
>  [Django-ALLOWED HOSTS](https://docs.djangoproject.com/en/1.11/ref/settings/#std:setting-ALLOWED_HOSTS)  

예를 들어,  ***learndocker.kr*** 호스트만 HTTP 요청을 허용한다면 아래와 같이 설정한다. 

```python
ALLOWED_HOSTS = ['learndocker.kr',]
```

<br/> 

**여기서 Health check 문제**가 발생한다.  AWS ELB의 Health check는 ELB가 EC2 인스턴스에서 구동하는 웹애플케이션의 URL로 GET요청을 보냄으로써 해당 웹애플리케이션이 정상적으로 구동하는지 여부를 확인하는 기능이다. 

![AWS_ELB_IP_flow]({{ site.url }}/data/AwsEB/0-AWS_EB_IP_flow.png)

 그런데 AWS의 ELB는 외부에서 오는 요청과 관계없이  AWS VPC 내부의 EC2 인스턴스 ip(Private IP)로 요청을 보내기 때문에  Django의 settings.ALLOWED_HOSTS에 추가되지 않은 위  Private IP의 요청은 거절당하여  아래 그림과 같이  AWS의 Elastic Beanstalk에서 **Health check가 실패했다**는 결과가 나타난다. 

![AWS EB Health check failure]({{ site.url }}/data/AwsEB/0-health_check_fail.png) 

<br/>

## 2) 해결

> 참조-  [5 Gotchas with Elastic Beanstalk and Django](https://hashedin.com/5-gotchas-with-elastic-beanstalk-and-django/) 
>
> [이한영 블로그 - Elastic Beanstalk의 Django 애플리케이션에서 발생하는 ELB Health check 4xx에러 해결](https://lhy.kr/elb-healthcheck-for-django)  



* 해결은 간단하다.  Django의  `settings.ALLOWED_HOSTS`에   Django가 동작하는 EC2 인스턴스 ip( Private IP )를 추가하면 된다. 
* 아래 python 코드는 위 이한영 블로그의 것이다. 

```python
# settings.py
ALLOWED_HOSTS = []

def is_ec2_linux():
    """Detect if we are running on an EC2 Linux Instance
       See http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/identify_ec2_instances.html
    """
    if os.path.isfile("/sys/hypervisor/uuid"):
        with open("/sys/hypervisor/uuid") as f:
            uuid = f.read()
            return uuid.startswith("ec2")
    return False


def get_linux_ec2_private_ip():
    """Get the private IP Address of the machine if running on an EC2 linux server"""
    from urllib.request import urlopen
    if not is_ec2_linux():
        return None
    try:
        response = urlopen('http://169.254.169.254/latest/meta-data/local-ipv4')
        ec2_ip = response.read().decode('utf-8')
        if response:
            response.close()
        return ec2_ip
    except Exception as e:
        print(e)
        return None
        
private_ip = get_linux_ec2_private_ip()
if private_ip:
    ALLOWED_HOSTS.append(private_ip)
```

<br/>

* 수정된 코드로 설정을 변경한 후 awsebcli 환경에서 `eb deploy`를 하면  **Health check**가 정상임을 확인할 수 있다. 

![health check success]({{ site.url }}/data/AwsEB/0-health_check_success.png)




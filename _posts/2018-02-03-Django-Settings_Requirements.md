---
layout: post
title:  "Django 환경분리- config_secret, settings, requirements"
date:  2018-02-03 00:20 +0900
categories: [Django, 웹서버]
tags: [ Django, secret, settings, requirement, shell, Nginx, uWSGI, 웹서버 ]
---



* Kramdown table of contents
{:toc .toc}
<br/>
<br/>

---

# 1. 소프트웨어 개발 수명 주기

> [위키백과 - 소프트웨어 개발 수명 주기(Software Development Life Cycle, **SDLC**](https://ko.wikipedia.org/wiki/%EC%86%8C%ED%94%84%ED%8A%B8%EC%9B%A8%EC%96%B4_%EA%B0%9C%EB%B0%9C_%EC%88%98%EB%AA%85_%EC%A3%BC%EA%B8%B0) 
>
> * **소프트웨어 개발 수명 주기**(Software Development Life Cycle, **SDLC**)란 [시스템 엔지니어링](https://ko.wikipedia.org/wiki/%EC%8B%9C%EC%8A%A4%ED%85%9C_%EC%97%94%EC%A7%80%EB%8B%88%EC%96%B4%EB%A7%81), [정보 시스템](https://ko.wikipedia.org/wiki/%EC%A0%95%EB%B3%B4_%EC%8B%9C%EC%8A%A4%ED%85%9C), 또는 [소프트웨어 공학](https://ko.wikipedia.org/wiki/%EC%86%8C%ED%94%84%ED%8A%B8%EC%9B%A8%EC%96%B4_%EA%B3%B5%ED%95%99)에서 정보 시스템을 계획, 개발, 시험, 채용하는 과정을 뜻하는 용어이다. 
> * 소프트웨어 개발 생명 주기는 하드웨어부터 소프트웨어까지 넓은 범위에 적용할 수 있다. 
> * 대개 요구사항 분석→설계→개발→테스트→운영 단계로 구성되어 있다.

  제품 개발의 조립라인(포드 생산시스템의 컨베이어 시스템)처럼, 작업을 명확하게 나뉜 여러 단계로 나누어 소프트웨어를 개발하는 것을  작업의 흐름도로 나타낸 방법론이다.  프로젝트 수명주기 + 운영 + 수정/유지보수 + 페기까지 다룬다. 

![Wiki- Systems Development Life Cycle](https://upload.wikimedia.org/wikipedia/commons/thumb/b/bb/Systems_Development_Life_Cycle.jpg/720px-Systems_Development_Life_Cycle.jpg)

<br/>

<br/>

---

# 2. Django 환경을 분리하는 이유

<br/>

소프트웨어 개발 수명 주기(SDLC)는 작업을 단계별로 나누어 효율성을 높이기 위한 방법론 중 하나인데  여기에는 **협업과 분업의 원리**가 구체화 되어있다.  

Django의 앱개발 과정 또한 **요구사항 분석⟶   설계⟶ 개발/테스트 ⟶ 운영⟶ 유지/보수⟶ 폐기**의 단계를 밟으면서  개발자들 사이에 협업과 분업을 효과적으로 조직화하고, 소스코드의 유지/보수를 고려해야 한다. 

협업과 분업, 코드의 유지/보수, 공개/비공개 처리 등이 잘 이루어지려면,

* 어떤 개발자의 개인적인 개발 환경이 local환경에서 뿐만 아니라 협업 개발자에게도 공유되어야 하고,
  * ➞ `settings.local`을 별도로 설정하는 것
* 개발 단계별 [버전 관리(VCS)](https://ko.wikipedia.org/wiki/%EB%B2%84%EC%A0%84_%EA%B4%80%EB%A6%AC)가 필요하며
* 환경 설정의 중복 등 코드의 중복을 줄여야 하고,
  * ➞ 공통 설정은 `settings.base`에 모두 담을 것 
* <u>개발 환경을 각 단계에 맞게</u> 서로 다르게 설정해야 할 필요가 있다. 
  * 개발단계의  `settings.DEBUG=True`  ⟶ 배포단계의`settings.DEBUG=False`로 바꾸기 
  * DB 엔진을 **개인개발(local) 환경**에서  sqlite3 또는 postgresql,  **공통개발(dev)환경**에서 postgrestsql,  **배포(deployment) 환경**에서 AWS RDS postgresql 등으로 선택하기 
  * 각 단계마다 필요한 라이브러리가 다르고, 
  * 각 단계에서 쓰이는 장비의 설정이 다르다. 
* 비밀값( `config_secret`)을 각 단계에 맞게 설정해야 한다. 
  * local 환경에서는 각 개발자에 맞게 공개/비공개 처리에 크게 신경쓰지 않아도 되지만
  * 공통 개발 환경에서는  개발자 간의 충돌을 막아야 하고
  * 배포 환경에서는 public에 공개하는 환경이므로  공개/비공개를 엄격하게 처리해야 한다. 
  * 어느 단계에 있듯 버전관리시스템(VCS), 특히 github 원격 저장소에 SECRET_KEY가 노출되어서는 안된다. 




Django 프로젝터를 시작( `➞ django-admin startproject <프로젝트명>`)하면  settings.py 파일에 모든 설정이 담겨 있고  설정 환경 분리가 되어 있지 않다. 

[SECRET_KEY](https://docs.djangoproject.com/en/1.11/ref/settings/#std:setting-SECRET_KEY)는 Session과 Cookie를 관리하거나 비밀번호 재설정, 암호화 서명 등에 쓰인다. 

<br/>

<br/>

---


#  3. Settings 환경 분리

> **Two Scoops of Django**  - 대니얼/오드리 로이 그랜펠드 지음, 김승진 옮김

> [Django 공식문서](https://docs.djangoproject.com/en/1.10/topics/settings/)

> [Django settings.py 환경을 여러 개의 설정 파일로 분리하여 사용하기](https://cjh5414.github.io/django-settings-separate/) 

> [Django settings 환경 분리하기 - 경영학도의 좌충우돌 프로그래밍](http://whatisthenext.tistory.com/125)

<br/>



## 1) 목표

* **local** -   디버그가 쉽게, 개발이 쉽게, 
  * PostgreSQL,  AWS에 연결없이 Django 개발


* **dev**  -  RDS, S3연결, 디버깅이 쉽게, 개발 쉽게 
* **deploy**  - 실제 배포 환경 

<br/>

## 2)  .config_secret 분리

   코드를 유지/보수하기 위해서  버전관리(VCS)를 한다.  주로 Git을 쓰게 되는데 GitHub의 **공개적인** 원격 저장소에 소스코드를 올리는 이상  ❶ Django의 SECRET_KEY,  ❷ 데이터베이스의 password,  ❸ AWS의 IAM 정보,  ❹ facebooke 로그인 api 키,  ❺ google 로그인 api 키 등이 노출될 위험이 매우 크다.   

  주위의 동료들이  무심코   `➜  git push`를 하는 바람에  AWS의 IAM정보가 노출되어 요금폭탄을 맞은 경우가 많았다.  해커들이 위 정보를 이용하여 각 리젼마다 AWS EC2 서비스 등을 무차별적으로 운영하여  비트코인을 채굴하는 것으로 보인다. 

   SECRET 값의 보안을 유지하는 방법으로는,

* **.gitignore에 settings.py를 추가하기**   - **단점**은 AWS에 Docker를 이용한 배포과정에서  awsebcli가  커밋된 파일만 다루기 때문에  git이 추적하지 않는 settings.py 없이  Django 앱플리케이션이 구동되지 않는다는 점이다.  이 방법은 local환경, 즉 개발 초기 단계에만 유용하다.  


​     <br/>

* **환경 변수로 처리하는 방법**   -  `.bashrc` ,  `.bash_profile`,  `.zshrc` 파일 등에 환경변수를 설정하는 방법이다.  **단점**은  배포 환경에서 환경 변수를 설정하는 방식이  배포 도구에 따라서 달라지고,  Apache 웹 서버인 경우 사용할 수 없으며,  비밀값들이 많으면 설정이 번거롭다는 점이다.  

  * **.zshrc**에  환경 변수 설정 코드 추가 

  ```sh
  ➔  vim ~/.zshrc
  # 코드 추가 
  export DJANGO_SECRET_KEY='asqfe/bd************'
  # 확인
  ➔  echo $DJANGO_SECRET_KEY
  ```

  * **settings.py**에서 환경 변수를 인식하는 코드로 대체 

  ```python
  import os

  SECRET_KEY = os.environ["DJANGO_SECRET_KEY"]
  ```

  <br/>


* **JSON 파일로 처리하기**  - 이 방법은 비밀 값들이 파일 단위로 관리할 수 있어서 단계별 설정에 용이하다.  다만 이 파일은  ➀ JSON 형식을 엄격하게 지켜야하고,  ➁   **.gitignore**에 추가하여 git의 추적에서 벗어나되,  ➂  **.ebignore**에서는 배제하여 AWS EB가 인식할 수 있도록 처리해야 한다.  

  * `.config_secret/`   디렉토리 구성

  ```sh
  .config_secret
    ├── settings_common.json 
    ├── settings_deploy.json 
    └── settings_dev.json 
  ```

  <br/>

  * JSON 형식 예시 

  ```json
  {
    "django": {
        "secret_key": "asdfae***************",
        "databases": {
        "default": {
          "ENGINE": "django.db.backends.postgresql",
          "HOST": "wooltari-rds.cwycqpvtqpqx.ap-northeast-2.rds.amazonaws.com",
          "PORT": "****",
          "NAME": "pet",
          "USER": "wooltari",
          "PASSWORD": "******"
        }
      }
    },
    "aws": {
      "access_key_id": "********HKW3A",
      "secret_access_key": "**********LIWP8aTb",
      "s3_bucket_name": "wooltari-bucket",
      "s3-region-name": "ap-northeast-2",
      "s3-signature-version": "s3v4"
    }
  }    
  ```


<br/>

<br/>

---

## 3)  settings 환경 설정 분리

* settings.py를 아래와 같이 **settings 파이썬 패키지**로 만든다.  

```sh
settings/
  ├── __init__.py  # 환경변수를 설정한다. 이유는 각 항목에서 설명한다. 
  ├── base.py      # 공통부분 설정
  ├── local.py     # 로컬 환경 
  ├── dev.py       # 협업 개발 환경 
  └── deploy.py    # 배포 환경 
```

<br/>

### (1) `settings.__init__`과  환경변수 설정

> [위키백과 - 환경변수 :](https://ko.wikipedia.org/wiki/%ED%99%98%EA%B2%BD_%EB%B3%80%EC%88%98)   프로세스가  컴퓨터에서 동작하는 방식에 영향을 주는 동적인 값들의 모임이라고 한다.  위키백과의 설명을 잘 이해할 수 없어서 경험적으로 알게 된 점을 요약하면  환경 변수는, 
>
> * 쉘에서 정의되고,  
> * 쉘에서 실행하는 동안 프로세스의 작동에 필요한 변수임 
> * 특히 `$PATH` 환경변수는 자신에게 할당한 path값들을 참조하여 실행파일의 경로를  찾아준다. 

예를 들어,  터미널에서 명령어(`ls`,  `cd`, `mv`, `cp`, `rm`, `python` ...)를 입력하여 엔터를 치면 실행되는 이유는 `$PATH`라는 환경변수가 자신에게 할당된 값들을 기준으로 위 명령어의 위치를 찾아주기 때문이다.  명령어의 위치를 찾는 순서는 현재 디렉토리에서 시작하여  `$PATH`에 할당된 path들을 참조하게 된다. 



`manage.py`의 코드를 살펴보면, 

**현재까지**  python manage.py가 실행하는 명령어 (`runserver`, `shell`,  `shell_plus` 등)는 **DJANGO_SETTINGS_MODULE** 환경변수의 참조값인 **`config.settings`파일** (  파이썬의 실행경로 표현 방식임.  실제로는  현재 디렉토리에 있는 서브디렉토리 config/의  settings.py를 의미함 )<u>에서 설정된 값을  참조한다</u>.  

*   `manage.py` 

```python
#!/usr/bin/env python
import os
import sys

if __name__ == "__main__":
    os.environ.setdefault("DJANGO_SETTINGS_MODULE", "config.settings")
    ...   
```

<br/>

* `settings.__init__.py`의 역할 -  settings 환경설정 모듈의 분리로 인하여 불필요하게 된 settings.py파일을 삭제하면,   python manage.py가 참조하는 Django 환경설정 파일을 바꿔야 한다.  바꾸는 방식은,  <br/> <br/>

  * ❶  shell 환경에서 환경변수를 타이핑 하는 방법 - 이 방법의 단점은 manage.py를 실행할 때마다 일일이 타이핑하므로 피곤하다는 점이다.  쉘의 export 구문을 활용하더라도 타이핑의 수고로움을 덜 수 있으나  환경변수가 드러나지 않아  매 확인이 필요하다. 

  ```sh
  # 로컬 환경에서 runserver
  ➜  DJANGO_SETTINGS_MODULE=config.settings.local python manage.py runserver

  # export 
  ➜  export DJANGO_SETTINGS_MODULE=config.settings.dev
  # 환경변수 확인
  ➜  echo $DJANGO_SETTINGS_MODULE
  config.settings.dev
  # runserver 실행 
  ➜  python manage.py runserver
  ```

  ​

  * manage.py 코드를 수정하는 방법 -  이 방법은 코드 유지/보수 관리가 어려워지므로 권장하지 않는다.  <br/><br/>
  * ❷  **\__ini__.py**에서  환경변수를 정하는 방법  - 타이핑의 수고로움이 없는 대신 개발자가 환경변수를 선택하는 로직을 잘 구현해야 하고,  협업 개발자나 유지/보수 관리자가 이 파일의 내용을 알아야 한다.   

  ```python
  # settings/__init__.py
  import os

  # DJANGO_SETTINGS_MODULE 환경변수가 지정되지 않거나 config.settings인 경우 
  SETTINGS_MODULE = os.environ.get('DJANGO_SETTINGS_MODULE')
  if not SETTINGS_MODULE or SETTINGS_MODULE == 'config.settings':
      from .local import *
  ```

  <br/><br/> 

### (2) `settings.base.py`

<br/>

* 파일 구조 

```sh
<컨테이너 폴더>                # ROOT_DIR의 위치 
├── .git
├── .gitignore
├── .media
├── .static_root
├── .config_secret/
│     ├── settings_common.json 
│     ├── settings_deploy.json 
│     └── settings_dev.json 
├── <프로젝트 폴더>              # BASE_DIR의 위치 
│     ├── <Django App폴더>
│     ├── config
│     │   ├── __init__.py
│     │   ├── settings
│     │   │    ├── __init__.py  
│     │   │    ├── base.py     # ✔ 현재 이 폴더이다. 
│     │   │    ├── local.py  
│     │   │    ├── dev.py    
│     │   │    └── deploy.py  
│     │   ├── urls.py
│     │   └── wsgi
│     ├── db.sqlite3
│     ├── manage.py
│     ├── static
│     └── templates
└── requirements.txt
```

 <br/>

* settings.py 옮기기 

```sh
➜  mv config/settings.py config/settings/base.py
```

<br/>

* settings / base.py -  위 파일 구조를 전제로 한 코드이다. 

```python
DEBUG = True

# Paths - base.py의 파일위치가 한 단계 낮아졌으므로, BASE_DIR이 Django project 폴더를 가리키도록 조정한다. 

_BASE = os.path.dirname(os.path.dirname(os.path.abspath(__file__)))
BASE_DIR = os.path.dirname( _BASE )
ROOT_DIR = os.path.dirname(BASE_DIR)
TEMPLATE_DIR = os.path.join(BASE_DIR, 'templates')

# Config paths
CONFIG_SECRET_DIR = os.path.join(ROOT_DIR, '.config_secret')
CONFIG_SECRET_COMMON_FILE = os.path.join(CONFIG_SECRET_DIR, 'settings_common.json')
CONFIG_SECRET_DEV_FILE = os.path.join(CONFIG_SECRET_DIR, 'settings_dev.json')
CONFIG_SECRET_DEPLOY_FILE = os.path.join(CONFIG_SECRET_DIR, 'settings_deploy.json')

config_secret_common = json.loads(open(CONFIG_SECRET_COMMON_FILE).read())
SECRET_KEY = config_secret_common['django']['secret_key']

# Static paths
STATIC_DIR = os.path.join(BASE_DIR, 'static')
STATIC_URL = '/static/'
STATICFILES_DIRS = [ STATIC_DIR, ]
STATIC_ROOT = os.path.join(ROOT_DIR, '.static_root')

# Media paths
MEDIA_ROOT = os.path.join(ROOT_DIR, '.media')
MEDIA_URL = '/media/'
```



### ✔ MEDIA 처리 

* media_root는  static_root와 달리 알아서  Django가 알아서 경로를 찾아주지 않는다. 
* 나중에 media_root,  static_root 모두  AWS S3 (원격 스토리지)를 사용할 것이다. 

```python
# settings.MEDIA_URL 대신 (settings.py삭제) 아래처럼 설정한다.
urlpatterns += static(
    settings.base.MEDIA_URL,
    document_root=settings.base.MEDIA_ROOT
)
```

<br/> 

### (3)  `settings.local`

```python
# base를 기준으로 하되, DB는 sqlite3, localhost를 사용함
from .base import *

DEBUG = True
SECRET_KEY = SETTINGS_COMMON["django"]["key"]
SETTINGS_LOCAL = json.loads(open(os.path.join(CONFIG_SECRET, "settings_local.json")).read())
WSGI_APPLICATION = 'config.wsgi.local.application'
ALLOWED_HOSTS = [ 'localhost', '127.0.0.1',]
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': os.path.join(BASE_DIR, 'db.sqlite3'), }}
SECRET_KEY = ✳✳✳✳✳✳✳✳✳✳✳✳✳✳
```

<br/>

### (4)  `settings.dev`

```python
# base를 기준으로 함, DB는 PostgreSQL
# storage는 .static_root 및 .media에서 AWS S3로 바꿈 
from .base import *

SETTINGS_DEV = json.loads(open(os.path.join(CONFIG_SECRET, "settings_dev.json")).read())

DEBUG = True 

WSGI_APPLICATION = 'config.wsgi.dev.application'
ALLOWED_HOSTS = ['localhost', '.try.net', '.elasticbeanstalk.com',]

# AWS Access
AWS_ACCESS_KEY_ID = SETTINGS_DEV['aws']['id']
AWS_SECRET_ACCESS_KEY = SETTINGS_DEV['aws']['key']

# AWS RDS Access
DATABASES = SETTINGS_DEV['psql']
pprint(DATABASES)

# AWS S3 bucket Access
AWS_SECRET_ACCESS_KEY = SETTINGS_DEV['aws']['key']
AWS_STORAGE_BUCKET_NAME = SETTINGS_DEV['aws']['bucket']
## AWS S3의 version4 사용: WebBrowser에서 staticfile인식 문제
AWS_S3_SIGNATURE_VERSION = 's3v4'
AWS_S3_REGION_NAME = 'ap-northeast-2'
# AWS Storage
STATICFILES_LOCATION = 'static'
MEDIAFILES_LOCATION = 'media'
# S3 FileStorage
DEFAULT_FILE_STORAGE = 'config.storages.MediaStorage'
STATICFILES_STORAGE = 'config.storages.StaticStorage'

# django-storages 앱 등록
INSTALLED_APPS += ['storages', ]
```

* DATABASE가 settings.dev와   settings.deploy에서 서로 다를 경우 `.config_settings/settings_deploy.json`을  만든다. 

```sh
.config_secret
  ├── settings_common.json 
  ├── settings_deploy.json 
  └── settings_dev.json 

# pqsql로 접속 
➜  psql --host=<AWS RDS 엔드포인트> --port=5432 --user=learn postgres

# DATABASE 생성
postgres=> CREATE DATABASE eb_docker_dev OWNER learn;
postgres=> CREATE DATABASE eb_docker OWNER learn;
```

<br/> 

### (5)  `settings.deploy`

```python
from pprint import pprint
from .base import  *

config_secret = json.loads(open(CONFIG_SECRET_DEPLOY_FILE).read())
WSGI_APPLICATION = 'config.wsgi.deploy.application'

DEBUG = False

# AWS Storage
STATICFILES_LOCATION = 'static'
MEDIAFILES_LOCATION = 'media'
# AWS
AWS_ACCESS_KEY_ID = config_secret['aws']['aws_access_key_id']
AWS_SECRET_ACCESS_KEY = config_secret['aws']['aws_secret_access_key']
AWS_STORAGE_BUCKET_NAME = config_secret['aws']['aws_storage_bucket']
## S3의 version4 사용 > url domain 이후 시그니쳐를 인식시킴
AWS_S3_SIGNATURE_VERSION = 's3v4'
AWS_S3_REGION_NAME = 'ap-northeast-2'

# S3 FileStorage
DEFAULT_FILE_STORAGE = 'config.storages.MediaStorage'
STATICFILES_STORAGE = 'config.storages.StaticStorage'

DATABASES = config_secret['django']['databases']

ALLOWED_HOSTS = [ # 'localhost',
    '.wooltari.net', '.elasticbeanstalk.com',]
```

<br/>
<br/>

---

# 4. uwsgi 분리

<br/>



### 1)  local.py

```python
import os
from django.core.wsgi import get_wsgi_application

os.environ.setdefault("DJANGO_SETTINGS_MODULE", "config.settings.local")

application = get_wsgi_application()
```

<br/>



### 2)  dev.py 

  위와 비슷하고, `"config.settings.dev"`로 바꾼다. 

```python
import os
from django.core.wsgi import get_wsgi_application

os.environ.setdefault("DJANGO_SETTINGS_MODULE", "config.settings.local")

application = get_wsgi_application()
```



<br/>

<br/>

#  5. requirements.txt 분리

개발 단계에서만 필요한 패키지들을 굳이 운영 단계에까지 설치하여 사용할 필요가 없다.  특히 도커 플랫폼인 AWS의 Elastic Beanstalk에서 `➜ eb deploy`를 하면  dockerfile을 통해 `➜ pip install -r requirement`를 실행하므로  패키지 설치에 시간과 메모리를 소모한다.  

따라서 **배포의 경량화**를 통해  효율적인 배포를 하는 방안의 하나로 requirements.txt의 분리가 필요하다. 

* 디렉토리 구조

```sh
requirements/
  ├── base.txt
  ├── dev.txt
  └── deploy.txt
```

* base.txt - 공통 패키지들을 놓는다. 

```
Django==2.0.1
...
```

* dev.txt - 개발 환경에만 필요한 것들을 넣는다. 

```sh
Django-extension ...
pytext...
```

* deploy.txt - 배포 환경에만 필요한 것들을 넣는다. 

<br/>

<br/>

---

# 6. Nginx, uwsgi, supervisor 

여기부터는 AWS ElasticBeanstalk ( Docker플랫폼 )을 사용하여 Django를 배포하기 위한 **사전 설정** 이다.  개발 단계와 배포 단계가 다르므로  settings 환경 분리와 같이 가야 한다. 

<br/>

## 1) 웹 서버의 구성

![local Nginx/uwsgi/supervisor]({{ site.url }}/data/settings/0-local_nginx_uwsgi.png) 

* **dockerfile**의 명령어 `expose 80`에 맞추어 설정
  * **`port:80 Nginx Server port:666`  ⇐ tmp/app.sock ⇒ `uWSGI` ⟺ `Django`**
* docker system 내에서는  http통신보다 socket 통신이 훨씬 효율이 높으므로,  Nginx와 uWSGI 사이에는 socket 통신으로 설정한다. 
* docker의 연결 포트는  dockerfile의 내용을 참작해야 한다. 

<br/>

## 2) Pycharm `File Types`

pycharm을  통합개발IDE로 사용한다면,  개발자는  아래와 같은  pycharm settings에서 File Type을 설정하면  pycharm이  파일명으로부터 파일 타입을 인식하여  그에 맞는 문법을 알려준다.   편리한 기능이다. 

| File Type         | Patterns                           | 사용례                                      |
| ----------------- | ---------------------------------- | ---------------------------------------- |
| Dockerfile        | Dockerfile, Dockerfile.\*          | Dockerfile,   Dockerfile.base,   Dockerfile.local |
| Nginx Config      | \*.conf,    \*.nginx               | app.conf,    nginx.conf                  |
| supervisor, uwsgi | \*.ini,   \*.service, supervisor\* | supervisor-nginx.conf,    supervisor-uwsgi.conf,   app.ini |

<br/>

<br/>

## 3) Nginx 설정

> [위키백과-웹서버](https://ko.wikipedia.org/wiki/%EC%9B%B9_%EC%84%9C%EB%B2%84) 
>
> [Apache vs. Nginx 성능비교 \| 명확하게](https://brightly888.blogspot.kr/2017/07/apache-vs-nginx.html) 
>
> <https://httpd.apache.org/docs/2.4/en/mod/event.html>
>
> <http://nginx.org/en/docs/>
>
> <https://www.nginx.com/blog/thread-pools-boost-performance-9x/> 



![웹서버 사용분포](https://upload.wikimedia.org/wikipedia/commons/thumb/1/12/Usage_share_of_web_servers_%28Source_Netcraft%29.svg/800px-Usage_share_of_web_servers_%28Source_Netcraft%29.svg.png)





* 웹 서버의 종류는 많다.  Django의 runserver도 웹 서버의 기능을 담당한다.   대표적으로 Apache,  IIS(마이크로소프트), Nginx,  GWS등이 있다.   
* 웹 서버를 선택하는 기준은 동시 접속 수를 얼마나 잘 수용할 수 있는가?  즉, 웹 서버 트래픽 폭주에 어느 만큼 대응할 수 있는가로 판단한다. 
* 여기서는 모듈로 구성되고  Event-driven이면서 비동기 방식으로 동작하며  worker 프로세스가 Single-threaded인  **Nginx가  부하가 커지더라도 CPU/메모리 사용량이 크게 증가하는 등 성능저하가 일어나지 않으므로** Apache보다 **사용하기에 적합하다**고 판단하였다. ( 물론 절대적인 기준은 없다. ) 
* 한편 Django runserver가  개발/테스트 환경에 적합하게 설계된 반면  Nginx는 배포 환경에 보다 적합(웹 트래픽에서  runserver가 쫓아갈 수 없다.)하다.   따라서 각 웹서버의 장점을 최대한 살리려면  local 환경에서는 runserver를 사용하여 손쉽게 테스트를 할 수 있도록 하고,  배포환경에서는 웹 트래픽에 강한 Nginx를 사용한다. 
* 이하  nginx 설정과  이에 대응하는 앱 설정을 살펴본다. 

<br/>

### (1) `nginx.conf` 

* 설치 

```sh
➜ sudo apt-get install nginx

# /etc/nginx/nginx.conf가 자동 생성됨 ➜ 이것을 copy해야 함 
➜ docker exec <실행 중 컨테이너 id> cat /etc/nginx/nginx.conf > nginx.conf
```

* 데몬 관리 툴 -  systemd?  supervisor?
  * nginx의 경우  `daemon on`이 **default** 이다.  즉, nginx는 기본적으로 데몬으로 작동한다. 
  * systemd는  데몬을 process 단위로 관리할 수 있으나 local과 격리된 프로세스로 작동하는 **docker container에서 쓸 수 없다.**  
  * 그러나  **supervisor는  docker container에서  process를 관리할 수 있다.**  다만  daemon을 관리할 수 없으므로 nginx설정파일에서 `daemon off`로 설정해야 한다. 

```nginx
user root;    # ✓ 수정 www-data가 기본값임 
worker_processes auto;
pid /run/nginx.pid;
daemon off;   # ✓ 수정 

events {
        worker_connections 768;
        # multi_accept on;
}
http {  # Basic Settings
        sendfile on;
        tcp_nopush on;
        tcp_nodelay on;
        keepalive_timeout 65;
        types_hash_max_size 2048;
        # server_tokens off;
        # server_names_hash_bucket_size 64;
        # server_name_in_redirect off;

        include /etc/nginx/mime.types;
        default_type application/octet-stream;
  
        # SSL Settings
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
          # Dropping SSLv3, ref: POODLE
        ssl_prefer_server_ciphers on;
        # Logging Settings
        access_log /var/log/nginx/access.log;
        error_log /var/log/nginx/error.log;
        # Gzip Settings
        gzip on;
        gzip_disable "msie6";
        # gzip_vary on;
        # gzip_proxied any;
        # gzip_comp_level 6;
        # gzip_buffers 16 8k;
        # gzip_http_version 1.1;
        # gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

        # Virtual Host Configs
        include /etc/nginx/conf.d/*.conf;
        include /etc/nginx/sites-enabled/*;
}


#mail {
#       # See sample authentication script at:
#       # http://wiki.nginx.org/ImapAuthenticateWithApachePhpScript
#       # auth_http localhost/auth.php;
#       # pop3_capabilities "TOP" "USER";
#       # imap_capabilities "IMAP4rev1" "UIDPLUS";
#
#       server {
#               listen     localhost:110;
#               protocol   pop3;
#               proxy      on;
#       }
#       server {
#               listen     localhost:143;
#               protocol   imap;
#               proxy      on;
#       }
#}
```

<br/>

### (2) `app.conf `

* 여기서  Nginx와 uWSI를 연결하는 설정을 한다. 
* 파일위치와  URL을 매칭하는 설정도 이루어진다. 
* 고객 요청의 크기(`client_max_body_size`)는 넉넉하게 잡아준다.
* **settings.local**의 경우 -  localhost가 작동한다. 

```nginx
server {
    listen 80;    # ✓ Dockerfile의 expose 80 
# .config/local/nginx/app.conf
    server_name  localhost *.elasticbeanstalk.com *.wooltari.net;
  
    charset utf-8;
    client_max_body_size 128M;

    location / {
        uwsgi_pass    unix:///tmp/app.sock;
        include       uwsgi_params;
    }
    location /static/ {
        alias /srv/app/.static_root/;
    }
    location /media/ {
        alias /srv/app/.media/;
    }
}
```

<br/>

* **settings.dev** 또는 **settings.deploy**의 경우 -  localhost를 삭제한다. 

```nginx
server {
    listen 80;    # ✓ Dockerfile의 expose 80 
  
# .config/deploy/nginx/app.conf
# .config/dev/nginx/app.conf  
    server_name  *.elasticbeanstalk.com *.wooltari.net;
  
    charset utf-8;
    client_max_body_size 128M;

    location / {
        uwsgi_pass    unix:///tmp/app.sock;
        include       uwsgi_params;
    }
    location /static/ {
        alias /srv/app/.static_root/;
    }
    location /media/ {
        alias /srv/app/.media/;
    }
}
```

<br/>

## 4) WSGI 설정

> [위키백과- WSGI](https://ko.wikipedia.org/wiki/%EC%9B%B9_%EC%84%9C%EB%B2%84_%EA%B2%8C%EC%9D%B4%ED%8A%B8%EC%9B%A8%EC%9D%B4_%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4)는  Web Server Gateway Interface의 약자이다.  웹서버와  웹 애플리케이션의 인터페이스를 위한  python 프레임워크이다. 
>
> * WSGI 미들웨어라고 불리운다. 
> * 웹서버가 볼 때에는 애플리케이션으로,  애플리케이션의 관점에서는 서버로 행동한다. 따라서  Nginx가 없더라도 WSGI만으로 Server기능을 수행할 수 있으나  Nginx만큼 효율적이고 안정적으로  웹 트래픽(동시접속 등)을 성능저하 없이 감당할 수 없다. 
> * WSGI의 기능은,
>   * 환경변수가 바뀌면  그에 따른 대상 URL에 맞추어  요청(request)의 경로를 지정해주고,
>   * 같은 프로세스에서 여러 애플리케이션과 프레임워크가 실행되도록 하며,
>   * XSLT 스타일시트처럼 전처리한다. 
>
> <br/>
>
> [우분투에서 Django + gunicorn + supervisor + nginx 연동하기](http://yujuwon.tistory.com/entry/%EC%9A%B0%EB%B6%84%ED%88%AC%EC%97%90%EC%84%9C-Django%EC%99%80-gunicorn-supervisor-nginx-%EC%97%B0%EB%8F%99-%ED%95%98%EA%B8%B0) 
>
> * WSGI서버에는 uWSGI, gunicorn, Apache/mod-wsgi를 많이 사용하는데,  uWSGI는 고성능 서버 성능을,  gunicorn은 보통 수준의 성능이지만 설치와 관리가 간단하다는 장점을 가지고 있다.

<br/>



###  (1) `app.ini` 

* 개발 단계에 맞게 모듈을 선택하고,  pyenv의 환경과  소켓 등을 설정한다. 
* 로그 기록 경로도 설정한다.  
* **.config/local/uwsgi/app.ini** 

```ini
[uwsgi]
chdir = /srv/app/<장고프로젝트폴더명>

# .config/local/uwsgi/app.ini
module = config.wsgi.local:application
# .config/dev/uwsgi/app.ini
module = config.wsgi.dev:application
# .config/deploy/uwsgi/app.ini
module = config.wsgi.deploy:application

home = /root/.pyenv/versions/app

socket = /tmp/app.sock
chmod-socket = 666

enable-threads = true
master = true
vacuum = true
pidfile = /tmp/app.pid
logto = /var/log/uwsgi/app/@(exec://date +%%Y-%%m-%%d).log
log-reopen = true
```

*  **.config/dev/uwsgi/app.ini**

```ini
[uwsgi]
chdir = /srv/app/<장고프로젝트폴더명>

# .config/dev/uwsgi/app.ini
module = config.wsgi.dev:application
...  이하 위와 같음 
```

* **.config/deploy/uwsgi/app.ini** 

```ini
[uwsgi]
chdir = /srv/app/<장고프로젝트폴더명>

# .config/deploy/uwsgi/app.ini
module = config.wsgi.deploy:application
... 이하 위와 같음 
```



<br/>

<br/>

## 5) supervisor 설정 

> [Supervisor: A Process Control System 공식문서](http://supervisord.org/)  -  supervisor는 사용자가 UNIX와 유사한 운영 체제에서 여러 프로세스를 모니터하고 제어할 수있는 클라이언트 / 서버 시스템입니다.
>
> [10분만에 익히는 supervisor 설치와 사용법](https://jwkcp.github.io/2016/11/07/how-to-use-supervisor-in-one-minute/) - supervisor는  24시간 돌아가는 서비스 운영에서  손쉽게 프로세스의 상태를 보여주고 죽은 프로세스도 자동으로 살려주는 어려움을 해결하기위해 만들어졌다. 
>
> * 용어
>   * supervisor | 이 제품의 이름
>   * supervisor**d**  | 백그라운드 데몬 프로세스 
>   * supervisor**ctl**  |  supervisor가 구동하는 프로세스들을 관리하는 명령어 
>
> [Supervisor. 프로세스 관리, 로그 확인](http://blog.kichul.co.kr/2017/04/10/2017-04-10-supervisor-python/) 
>
> * 기본 사용법 
>
> ```sh
> pip install supervisor
>  
> # 설정 파일 생성
> echo_supervisord_conf > /etc/supervisord.conf
>  
> # 설정파일 적절히 수정
> vi /etc/supervisord.conf
>  
> # 데몬 실행
> /usr/bin/python /usr/bin/supervisord -c /etc/supervisord.conf
> ```
>
> * 설정 파일 작성법
>
> ```ini
> [program:python_web_application]
> command=python -m python_web_application.cli api
> directory=/python_web_application_working_dir
> autostart=true
> ```
>
> | 키         | 설명                                      |
> | --------- | --------------------------------------- |
> | command   | 어떤 커맨드를 실행할지 정의                         |
> | directory | 어떤 디렉토리에서 실행할지 정의                       |
> | autostart | supervisor 데몬이 시작할 때 이 프로세스도 같이 실행할지 정의 |
>
> * 프로세스 관리 방법  supervisorctl
>
> ```sh
> # supervisor 실행
> ➜  supervisorctl
>
> # 결과
> project1_minio_sync             RUNNING   pid 25099, uptime 19:35:34
> project1_react_app_es6          RUNNING   pid 30815, uptime 6 days, 0:49:03
> ...
>
> ➜  supervisor> help
> ```
>
> <br>
>
> ```sh
> # 모든 프로세스 보기
> status
>  
> # 설정된 모든 프로세스 보기
> avail
>  
> # 나가기
> exit or quit
>  
> # foreground로 프로세스를 확인
> fg process_name
>  
> # 프로세스 시작, 중지, 재시작
> start process_name
> stop process_name
> restart process_name
>  
> # supervisord를 재시작
> reload
>  
> # 설정 파일을 다시 불러온다
> reread
>  
> # 프로세스를 그룹에 추가
> add process_name
> ```
>
> 

<br/>

* 설치 

  * apt-get으로 설치 

  ```sh
  ➜  apt-get install suervisor
  ```

  * pip로 설치

  ```sh
  ➜  pip install supervisor
  ```


<br/>

* 설정 - /etc/supervisor 디렉토리 아래 supervisord.conf 설정 파일을 찾을 수 있다. 

  * [program:프로그램명] 기재 
  * **실행 스크립트 경로**를 command 속성에 넣는다. 

<br/>

* **supervisor-nginx.conf**  -  nginx를 관리하는 supervisor 설정 파일.  nginx 명령어는 path 환경변수가 알아서 찾아주는 것으로 보인다. 

```ini
[program:nginx]
command = nginx`supervisor-uwsgi.conf`
```



* **supervisor-uwsgi.conf**  - uwsgi 명령어는 path를 설정해주어야 한다.  .bashrc 등에 path 환경변수 설정을 할 수 있으나  번거롭다.  자주 쓰이는 명령어가 아니기 때문이다. 

  * local

  ```ini
  [program:uwsgi]

  command = /root/.pyenv/versions/app/bin/uwsgi -i /srv/app/.config/local/uwsgi/app.ini
  ```

  * dev

  ```ini
  [program:uwsgi]

  command = /root/.pyenv/versions/app/bin/uwsgi -i /srv/app/.config/dev/uwsgi/app.ini
  ```

  * deploy

  ```ini
  [program:uwsgi]

  command = /root/.pyenv/versions/app/bin/uwsgi -i /srv/app/.config/deploy/uwsgi/app.ini
  ```






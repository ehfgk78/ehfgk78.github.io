---
layout: post
title: "Django에서 비밀값(secret)관리"
date: 2018-02-04 00:20 +0900
categories: [Django]
tags: [Django, secret]
---

* Kramdown table of contents
{:toc .toc}
<br/>
<br/>

---

#  1.  참조 자료와 이 포스트의 목적

* 이 포스트는  [이한영 - Django에서 비밀값(secrets) 관리하기](https://lhy.kr/django-secrets)를 공부할 목적으로 끄적인 것입니다.   공부하실 분은 위 링크를 참조하세요. 
* 관련 포스트는 [Django 환경분리- config_secret, settings, requirements]({% post_url 2018-02-03-Django-Settings_Requirements %}) 중  config_secret 부분입니다. 
* 이 포스트는  python 문법 중 `setattr()`이  비밀값 관리에 적용되는 예를  익히기 위함입니다. 

<br/>

<br/>

---

# 2. 비밀 값 관리 

<br/>

## 1) 관리하는 이유 - Git Hub  

소스 코드는 기업과 개발자의 중요한 자산이다.  소스 코드의 유지/보수/협업과 분업을 위해서는  버전관리를 해야하는데,  최근에 버전관리가 대부분 git을 통해서 이루어지다보니  원격 공개저장소 사이트인 Git Hub를 많이 사용한다. 

소스 코드는 소프트웨어 제품의 일부이다.  소프트웨어의 어떤 부분들은 보안이 매우 중요한데,  Django 애플리케이션의 경우  SECRET_KEY,  데이터베이스의 password,  배포 환경의 접근 제어 값 등은 아예 노출되어서는 안되는 정보들이다. 

 이 포스트는 악의적인 해커들의 공격이나 개발자 스스로가 야기하는 `git push`,  배포 환경에서  예측할 수 없는 사용자의 입력으로부터 위와 같은 비밀 정보를 보호하는 방법 중  비밀 정보들을 Git Hub에 노출되지 않게 처리하는 작업을 담았다.  

동시에  파이썬의 `setattr()`을 적용하는 구체적인 사례를 배우고자 한다. 



<br/>

## 2) 폴더 구조 

```sh
<프로젝트 컨테이너 폴더>
  ├─ .git
  ├─ .gitignore
  ├─ secrets.json  ✔
  └─ <django 프로젝트 폴더>
       ├─ <설정패키지>
       │    ├─ __init__.py
       │    ├─ settings.py  ✔
       │    ├─ urls.py
       │    └─ wsgi.py
       └─ manage.py
```

* python 가상환경 명칭은 `secret-env`라고 가정한다. 

<br/>

## 3) secrets.json 파일 

```sh
(secret-env)➜  cd <path-to/프로젝트 컨테이너 폴더>
(secret-env)➜  touch secrets.json

(secret-env)➜  vim secrets.json

{
  "SECRET_KEY" : "<Django secret key>"
}
```

* json 형식에 유의해야 한다.  특히 마지막 딕셔너리 형태의 원소 끝에 콤마를 찍는 습관을 피해야 한다. 

<br/>

## 4) settings.py와  동적인 속성 할당 setattr()

```python
import sys
import json

# Django 프로젝트 폴더 위치
BASE_DIR = ...
# 컨테이너 폴더 위치
ROOT_DIR = os.path.dirname(BASE_DIR)
# secrets.json 경로
SECRETS_PATH = os.path.join(ROOT_DIR, 'secrets.join')
# json형식을 파이썬 객체로 변환
secrets = json.loads( open(SECRETS_PATH).read())

#  동적 할당 !!!
## __name__은 현재 파일이름인 settings.py
for key, value in secrets.items():
    setattr(sys.modules[__name__], key, value)
```

<br/>

## 5)  .gitignore에  secrets.json 추가

```sh
(secret-env)➜  echo "secrets.json" >> .gitignore
```

<br/>

<br/>



---



# 3. 기존 커밋에 비밀 값이 포함된 경우

```sh
# 비밀값이 포함된 기존 커밋 찾기 
(secret-env)➜ git log --oneline 
# 기존 커밋을 찾았다고 가정하고, 해당 커밋을 삭제 
(secret-env)➜ git reset <해당 커밋 id>
```

<br/>

<br/>



---



# 4. git push한 경우

* 공개 저장소에 올라간 비밀 값은  더 이상 보호할 수 없다. 
* 이 경우 비밀 값을 재설정해야 한다.

<br/>

<br/>



---



# 5. SECRET_KEY  랜덤 생성

```python
import string, random

# Get ascii Characters numbers and punctuation (minus quote characters as they could terminate string).
chars = ''.join([string.ascii_letters, string.digits, string.punctuation]).replace('\'', '').replace('"', '').replace('\\', '')

SECRET_KEY = ''.join([random.SystemRandom().choice(chars) for i in range(50)])

print(SECRET_KEY)
```

* 위의 내용이 너무 복잡하면 아래 처럼 간단히 코딩해볼 수 있다. 

```python
SECRET_KEY = ''.join([random.choice(string.ascii_lowercase) for i in range(40)])
```

<br/>

<br/>

---



# 6. 비밀값을 환경변수에 담기

> [[Two Scoops of Django](https://www.twoscoopspress.com/products/two-scoops-of-django-1-11) 5장]

<br/>

* 쉘 환경 

```sh
# 사용하는 쉘이 무엇인지 파악하기
(secret-env)➜  echo $SHELL
/usr/bin/zsh

# 그 쉘의 프로파일 스크립트 파일에서 환경변수 export
(secret-env)➜  vim ~/.zshrc

export DJANGO_SECRET_KEY='anfavn-0n{******'

#  환경변수 확인
(secret-env)➜  echo $DJANGO_SECRET_KEY
```

<br/>

* settings.py 

```python
import os

SECRET_KEY = os.environ["DJANGO_SECRET_KEY"]
```

<br/>

* 예외 처리하는 방식

```python
import os
from django.core.exceptions import ImproperlyConfigured


def get_env_variable(var_name):
  """환경 변수를 가져오거나 예외를 반환한다."""
  try:
    return os.environ[var_name]
  except KeyError:
    error_msg = "Set the {} environment variable".format(var_name)
    raise ImproperlyConfigured(error_msg)


SECRET_KEY = get_env_variable("INSTA_SECRET_KEY")
```


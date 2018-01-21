---
layout: post
title: "PyCharm과 pyenv를 사용한 Django 개발환경설정"
date: 2018-01-21 15:00 +0900
categories: [Django, Pycharm]
tags: [Django, Pycharm, pyenv]
---

* Kramdown table of contents
{:toc .toc}





# 1. 참고 

> [pycharm 단축키 맵](http://resources.jetbrains.com/storage/products/pycharm/docs/PyCharm_ReferenceCard.pdf) 
>
> [공식문서](https://www.jetbrains.com/help/pycharm/) 
>
> [공식문서 - 빠른 시작](http://www.jetbrains.com/pycharm/quickstart/) 



> [이한영 강사 블로그](https://lhy.kr/pyenv-python-development-environment-setting)
>
> [나채원 블로그](https://nachwon.github.io/django-1-setting/) 





# 2. Installing PyCharm on Linux

* 무료버전은 **pycharm-community** 
* ❶ 압축 파일을 다운로드받아 압축을 해제한 후 /opt/에 설치
* ➁ apt를 이용하여 설치하는 방법 

~~~bash
# ➀ 홈페이지에서 압축 파일을 다운로드 받아서 설치하는 방법
## unpack
tar xfz <pycharm-community>-*.tar.gz -C <new_archive_folder>
## filesystem hierarchy standard (FHS) is /opt
sudo tar xf <pycharm-community>-*.tar.gz -C /opt/

cd <new archive folder>/<pycharm-community>-*/bin
~~~
~~~bash
# ➁ apt 이용
sudo add-apt-repository ppa:mystic-mirage/pycharm 
sudo apt update
sudo apt install pycharm-community
## remove
sudo apt remove pycharm pycharm-community
~~~




# 3. Pycharm setting
* 인터프리터 선택 : 가상환경 선택 
    [file] - [settings] -[Project] - [Project Interpreter]
* Alias in `.zshrc` 
~~~bash
alias pycharm="/usr/lib/pycharm-community/bin/pycharm.sh"
~~~


## setting-keymap

* [file] - [settings] -[keymap] 

| 동작                        | 설명        | key                            |
| ------------------------- | --------- | ------------------------------ |
| Increase Font Size        |           | `Alt,  f` + 	&uarr;            |
| Decrease Font Size        |           | `Alt,  f` + 	&darr;            |
| **!Reformat** (PEP)       | PEP규칙적용   | `Alt, r`                       |
| **!import** automatically | 자동 import | `Alt, Enter`                   |
| **!refactor**             | 리펙토링      | `F2`                           |
| **!occurrences Next**     | 다중선택      | `Alt, j`                       |
| **multi-cursur**          |           | `Ctrl`+ `Ctrl` + &uarr;/&darr; |



## settings

| 설명        | 위치                                       |
| --------- | ---------------------------------------- |
| 프로젝트 설정언어 | [file] - [settings] - [Project Interpreter] |
| 주석편집      | [file] - [settings] - [Editor] - [Color & Font] - Comments |
| 스펠링 검사    | [file] - [settings] - [Inspections] - Spelling - Typo |





## CLI 환경 설정

* shell 환경 파일 :  `~/.bashrc` , `~/.zshrc` 
* cli 환경에서 `charm .`을 하면 pycharm이 실행되도록 해보자. 

```sh
➜  vi ~/.zshrc

alias charm="/usr/lib/pycharm-community/bin/pycharm.sh"
```

* zsh 재실행 또는 `source  ~/.zshrc` 




---

# 4. Django 개발 환경 설정



## 1) 기초 환경 

* pyenv,  pyenv-virtualenv가 설치되어야 합니다. 
* 이하 , **`~projects / instagram-project /`** 에서  instagram 프로젝트를 django 개발환경으로 설정하는 방법을 기술한다. 
* Ubuntu 16.04 LTS, Pycharm Community Edition 2017. 2.3을 기준으로 작성되었다. 



## 2) 프로젝트 컨테이너 폴더 

```sh
➜ cd ~/projects

# 프로젝트를 감싸는 컨테이너 폴더 생성 및 이동 :  take instagram-projects
➜ mkdir instagram-projects
➜ cd instagram-projects
```



## 3) pyenv 가상환경 

```sh
# instagram-projects에서 pyenv 가상환경 생성 및 적용
➜ pyenv virtualenv 3.6.2 instagram-env
➜ pyenv local instagram-env
```



## 4) Django 설치

```sh
# Django 및 서드파티 패키지 설치
(instagram-env) ➜ pip install django ipython django_extensions

# requirements.txt 
## 패키지 세팅 사항 저장하기 - 저장된 패키지들은 나중에 그대로 다시 설치할 수 있다. 
## pip install -r requirements.txt
(instagram-env) ➜ pip freeze > requirements.txt

# README.md - 파이썬 버전을 명시한다. 
(instagram-env) ➜ vim README.md
python-version: 3.6.2
```



## 5) git 관련 설정  

* 리모트 저장소 설정 전에 GitHub에서 new repository를 설정한 후 아래와 같이 진행하자. 

```sh
# 로컬 저장소 초기화
(instagram-env) ➜ git init

# 리모트 저장소 설정
(instagram-env) ➜ git:(master) git remote add <리모트저장소이름> <저장소URL>
```



* **`.gitignore`**  파일 만들기 

  * https://www.gitignore.io/ 에서  ❶ 운영체제 ,  ❷ 사용하는 IDE,  ❸ 프로그래밍 언어 등 입력하면  .gitignore에 넣을 내용이 출력된다. 


  * 여기에서는  Linux, Python, Django, Pycharm에 관한 .gitignore파일을 만들자. 

```sh
(instagram-env) ➜ git:(master) vim .gitignore

​~~~ 위 출력 내용을 여기에 붙인다.
​~~~ 파일의 처음에 pycharm 설정 관련 디렉토리 .idea를 기입한다. 
# Custom
.idea/ 

(instagram-env) ➜ git:(master) git add -A
# 아래 명령어에서 추가되면 안 되는 파일이 있는지 확인 (db.sqlite3등)
(instagram-env) ➜ git:(master) git diff --staged
(instagram-env) ➜ git:(master) git commit -m "1st commit"

# 리모트 저장소 추가 후
(instagram-env) ➜ git:(master) git push origin master
```



## 6) pycharm 설정

* CLI 환경에서 pycharm 가동

```sh
(instagram-env) ➜ git:(master) pycharm .
```



* **python interpreter 설정 ** 


1. [File] > [Settings] > [Project] > `Project Interpreter`
2. 우측 창의 Project Interpreter: 우측의 `드롭다운` 메뉴를 클릭
3. 가장 아래로 스크롤하여 `Show All`선택
4. 좌측 아래의 `+`클릭 -> `Add local`선택
5. 인터프리터로 사용될 파이썬 선택
   -  `Ubuntu`는 `/Users/<자신의 홈폴더명>/.pyenv/versions/instagram-env/bin/python`선택
6. 추가된 인터프리터 선택 후 `OK`클릭
7. 나온 창에서 `OK`클릭






# 5. Django 프로젝트 시작



## 1) django-admin startproject

```sh
(instagram-env) ➜ git:(master) django-admin startproject instagram
```



## 2) 프로젝트 폴더 구조

```sh
# 프로젝트 컨테이너 폴더 
instagram-project/
  ┃  # Django 프로젝트 폴더 (Sources Root)
  ┣━ instagram/
  ┃    ┣━ manage.py 
  ┃    ┃ # 프로젝트 instagram 설정파일 모음 패키지
  ┃    ┗━ instagram # 나중에 config로 리펙토링한다. 
  ┃         ┣━ __init__.py
  ┃         ┣━ settings.py
  ┃         ┣━ urls.py
  ┃         ┗━ wsgi.py
  ┣━ .git/
  ┣━ .gitignore
  ┣━ .python-version
  ┣━ .idea/
  ┣━ requirements.txt
  ┗━ README.md
```



## 3) pycharm- source root 

* 현재 django 프로젝트 폴더인 **`instagram` 폴더** 내의 모듈들이 참조하고 있는 이 프로젝트의 `Root directory` 는 가장 최상위 폴더인  **`instagram_project`** 이다.  이 경우 나중에  django 프로젝트 내부 모듈이 프로젝트 폴더인 **instagram 폴더**를 참조해야 함에도 불구하고 **컨테이너 폴더인 instagram_project 폴더**를 참조하는 문제가 발생한다. 이를 예방하기 위해 `Root directory` 를 `instagram` 폴더와 일치시켜주어야 한다.


* Django프로젝트 폴더 (`instagram-project/instagram/`)에 우클릭 ➔

   `Mark Directory as` ➔  `Sources Root`선택

  ​

## 4) 리팩토링 : config

* 위 프로젝트 폴더 구조를 보면 `instgram` 폴더 안에 또 `instagram` 라는 폴더가 있는 것을 볼 수 있다.  헷갈리지만 django 개발 역사에서 위와 같이 구성한 이유가 있다.  그럼에도 불구하고 일단 헷갈리니까 일단 하위 `instagram` 폴더를 `config` 로 바꾼다.  이 때, `Pycharm` 의 `refactor` 기능을 활용하여 내부 파일들이 바뀐 폴더이름을 제대로 참조할 수 있도록 하는 것이 중요하다. 


* 설정파일 모음 패키지(`instagram-project/instagram/instagram/`)에 우클릭 ➔  `Refactor`  ➔  `Rename`  선택
* `Search for references`와 `Search in comments and strings`두 항목을 모두 체크 한 후, 값을 `config`로 변경 후 `Refactor`버튼 클릭
* 아래쪽의 `Do Refactor`버튼 클릭 



## 5) runserver: 확인

* 이상없이 작동하는지 확인하자. 

```sh
(instagram-env) ➜ git:(master) cd instagram
(instagram-env) ➜ git:(master) ./manage.py runserver

# 성공 메시지 

Performing system checks...

System check identified no issues (0 silenced).

You have 13 unapplied migration(s). Your project may not work properly until you apply the migrations for app(s): admin, auth, contenttypes, sessions.
Run 'python manage.py migrate' to apply them.

September 30, 2017 - 08:00:17
Django version 1.11.5, using settings 'config.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

* 브라우저 창에  localhost:8000으로 접속하여 이상없음을 확인한다. 


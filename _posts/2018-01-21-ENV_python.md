---
layout: post
title:  "Ubuntu 파이썬 개발환경 구축- pyenv, virtualenv "
date:   2018-01-20 20:20 +0900
categories:  python
tags: [python, pyenv, virtualenv]
---

* Kramdown table of contents
{:toc .toc}
# 참조

> [Github:  yyuu의 pyenv](https://github.com/pyenv/pyenv)
> [pyenv-installer](https://github.com/pyenv/pyenv-installer)
> [이한영 블로그 - pyenv와 virtualenv를 사용한 파이썬 개발환경 구성](https://lhy.kr/configuring-the-python-development-environment-with-pyenv-and-virtualenv) 
> [안수찬 블로그 - pyenv + virtualenv + autoenv 를 통한 Python 개발 환경 구축하기](https://ansuchan.com/how-to-set-python-dev-env/)

> [Common build problems](https://github.com/yyuu/pyenv/wiki/Common-build-problems)  파이썬 설치 전 필수 패키지 





---



# 파이썬 버전관리 이유 

파이썬은 여러 버전을 가지고 있고  특히 2.x버전과 3.x버전에 큰 차이가 있는데, 프로젝트 마다 언어와 프레임워크를 다르게 사용하려면 버전관리 및 가상환경 분리가 필요하다.  이 포스팅은 Github yyuu님의 pyenv, pyenv-virtualenv 라이브러리,  이한영 강사의 블로그 및 안수찬 블로그를 참고하였다.  우분투 리눅스 배포판을 기준으로 설치하였다. 

* `pyenv` - "Simple Python Version Management", 로컬에 다양한 파이썬 버전을 설치하고 사용할 수 있도록 한다. pyenv를 사용함으로써 파이썬 버전에 대한 의존성을 해결할 수 있다.
* `virtualenv` - “Virtual Python Environment builder”, 로컬에 다양한 파이썬 환경을 구축하고 사용할 수 있도록 한다. 일반적으로 Python Packages라고 부르는 ( pip install을 통해서 설치하는 ) 패키지들에 대한 의존성을 해결할 수 있다.  이로써 프로젝트별로 설치된 패키지들 간의 충돌을 막을 수 있다. 






## 1. pyenv,  pyenv-virtualenv 설치 

```sh
curl -L https://raw.githubusercontent.com/yyuu/pyenv-installer/master/bin/pyenv-installer | bash
```

* bash를 기준으로 하였다. 







## 2. pyenv 설정 

* bash에서는 `.bashrc` 또는 `.bash_profile` 
* zsh에서는  `~/.zshrc`에서 설정한다. 

```sh
➜ vi ~/.bashrc

# 가장 아래쪽에 아래 문장 추가
export PATH="~/.pyenv/bin:$PATH"
eval "$(pyenv init -)"
eval "$(pyenv virtualenv-init -)"

➜ source ~/.zshrc
```



* shell 설정을 바꾸었으면,  ❶ 터미널을 재시작하거나  ❷ `source  ~/.zshrc` 실행한다. 




---

# python 설치



## 1. 필수 패키지 설치

* [Common build problems](https://github.com/yyuu/pyenv/wiki/Common-build-problems)
* Ubuntu/Debian


```sh
sudo apt-get install -y make \
build-essential libssl-dev zlib1g-dev libbz2-dev \
libreadline-dev libsqlite3-dev wget curl llvm libncurses5-dev \
libncursesw5-dev xz-utils tk-dev
```





## 2. pyenv install --list

* 설치 가능한 파이썬 버전 보기

```sh
➜ pyenv install --list
  3.6.0
  3.6-dev
  3.6.1
  3.6.2
  3.6.3
  3.7.0a2
  3.7-dev

# 이후 원하는 버전을 설치한다.
➜ pyenv install 3.6.1
```



---



# pyenv-virtualenv 



## 1.  pyenv global

* 컴퓨터 전체에서 사용할 파이썬 환경 설정

```sh
➜  pyenv global 3.6.1
➜  pyenv versions
  system
* 3.6.1 (set by /usr/local/var/pyenv/version)
```





## 2. pyenv virtualenv

* 가상환경 만들기

```sh
#문법  pyenv virtualenv <version> <env_name>
➜  pyenv virtualenv 3.6.1 env361
```





## 3. pyenv local

* 프로젝트 폴더 (git 저장소 단위 폴더)에 위 가상환경 설정하기

```sh
#문법  pyenv local <env_name>
➜  cd ~/projects/env361/
➜  pyenv local env361
#프롬프트 좌측에 가상환경 이름 표시 

(env361) ➜ ls -al

drwxrwxr-x  5 learn learn 4.0K  1월 17 02:01 .
drwxrwxr-x 16 learn learn 4.0K  1월  8 15:15 ..
-rw-rw-r--  1 learn learn    8 12월  1 23:03 .python-version
```


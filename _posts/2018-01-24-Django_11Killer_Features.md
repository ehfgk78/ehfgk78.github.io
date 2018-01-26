---
layout: post
title:  "Django 프로젝트 전에 꼭 알아야할 11가지"
date:   2018-01-24 02:20 +0900
categories: [ Django]
tags: [Django ]
---

* Kramdown table of contents
{:toc .toc}


# 1. 참고

<br/>

> [Joseph Misiti - 11 Killer Features I use in Every Django Project ](http://medium.com/cs-math/2014-django-development-mistakes-in-2014-f48623f58b21)  
> * [raccoony의 번역 - 2014년에 Django로 개발하면서 실수한 부분](http://raccoonyy.github.io/django-development-mistakes-in-2014-translatate/) 
> * 이 글은 위 사이트의 내용을 요약 정리한 것입니다. 

<br/>

> [Joseph Misiti -11 Things I Wish I Knew About Django Development Before I Started My Company](https://medium.com/cs-math/11-things-i-wish-i-knew-about-django-development-before-i-started-my-company-f29f6080c131)


<br/>
<br/>
<br/>

# 2. 요약

<br/>

## 1) Postgres DB엔진을 쓸 것 

* **Django ORM**  지원하지 않는 MongoDB등은  Django admin이나 **3rd party  라이브러리**를 사용할 수 없다. 
* Postgres가 2014. 12. 18.부터  JSONB 데이터 타입을 지원하기 시작하였으므로  Postgres에 **문서**를 저장할 수 있고, 성능 저하 없이 MongoDB처럼 쿼리를 실행할 수 있다. 
* **pycharm Professional edition(유로버전)**이 제공하는 Database tool window 기능을 활용하자.  DB접근이 쉬워진다. 
  * [Working with the Database tool window](https://www.jetbrains.com/help/pycharm/working-with-the-database-tool-window.html)
  * [Databases and SQL](https://www.jetbrains.com/help/pycharm/databases-and-sql.html) 




<br/>



## 2) Unit Test를 위한 infra-structure를 구성할 것

* [ [ 여기 블로그 -TDD ] - Obey the Testing Goat!]( {{ site.baseurl }}{% post_url 2018-01-24-TDD01 %})  



<br/>




## 3) DRF로 Restful하게 만들 것

* `Vue.js`, `Angular.js` 등 클라이언트 MVC 프레임워크의 수가 급증하므로 REST API를 구현할 **DRF**( django-rest-framework )를 사용하는 것이 좋아. 




<br/>


## 4) Django model field의 `help_text`를 사용할 것

* `help_text`속성은 Django의 폼 화면이나 admin 화면에 나타나고  문서화에도 도움이 된다. 


<br/>




## 5) Django의 URL 적용할 것 - 쿼리 문자열 쓰지 않기 

* `https://www.example.com/user?id=20` 대신  **`https://www.example.com/user/20/`를 쓰자.   컨트롤러(`view.py`)에서 타입 검사 같은 코드를 덜 작성하기 때문이다. 



<br/>



## 6) Django ORM 최대한 사용하기

DB의 스키마 설계에 공을 많이 들이는 것이 좋다. 이때 Django ORM을 최대한 이용하면 잘못된 데이터를 저장하는 일 없이 수 많은 문제가 해결된다.  특히  DB 제약조건을  정확히 지정하는 것이 좋다. 

* null 값을 가질 수 없는 필드에는 null=True를 설정하지 마세요.
* 고유한 값을 저장하는 필드들은 unique_together로 지정해주세요.
* unique 파라미터도 적절히 사용해 주세요.
* max_length도 알맞게 지정하세요.


<br/>


## 7) `django-extensions` 와 `Django-model-utils` 사용하기

* `django-extensions`  -  매우 뛰어난 3rd party library -  ❶ printing settings,

     ❷  shell_plus,   ❸ dumping scripts,   ❹ encryption, etc

* `django-model-utils` - Django ORM에서 지원하지 않거나  구현 방식이 조금 다른 유용한 기능들을 제공함 -   ❶ TimeStampField,   ❷ MonitorField,   ❸ Choices 등


<br/>


## 8) Sentry 사용할 것 

* 가입형은 한 달에 $24부터 시작하며, 이 돈을 낼 만한 가치가 충분하다. 


<br/>


## 9) `django-debug-toolbar`- 최적화, 디버깅

* SQL 쿼리와 요청, 템플릿, 캐시 등을 모두 추적할 수 있다. 

<br/>
<br/>


## 10) Custom User Model 처음부터 쓰기 

User 모델에 필드를 추가할 때도 기존의 방법(기본 User 모델과 OneToOne으로 엮인 새 모델을 만드는 방법)보다 직관적입니다. Django를 이미 사용하고 있다면 기본 User 모델의 email과 username 필드가 30 글자로 제한되어서 답답했을 겁니다. 커스텀 User 모델을 도입하면 이런 문제들이 모두 해결됩니다. 


<br/>

## 11)  `django-suit` 등  django admin의 대체품 사용하기

* django-suit,  django-grapelli
* [Django Admin interface](https://github.com/rosarior/awesome-django#admin-interface) 



<br/>
<br/>
<br/>

# 3. 그 외 권고 사항들 
<br/>

## 1) 올바른 디렉토리 구조로 시작할 것 
* 아래는 하나의 예시에 불과하다. 

```sh
┌─  apps/  # customized django apps
├─ vendor/  # pip 등으로 설치할 수 없는 apps
├─ bin/   # all bash scripts that automate development
├─ config/
├─ media/
├─ static/
├─ templates/
├─ manage.py
└─ README.md 
```

<br/>

## 2) Celery- 비동기처리와 정기적 반복 처리 
* celery backend로 RabbitMQ보다 **redis**를 추천한다. 
* Facebook API로 가져온 데이터를 이메일로 보낸는 작업에 쓰인다.
* 정기적인 반복 처리에도 UNIX crontab 대신 **celery를 사용하자. 

<br/>

## 3) named URLs/reverse 
* 당연한 얘기지만  URLs에서  name을 반드시 정하고,  url template tag에서 사용할 것 -  이는 나중에 url이 변동될 때 하드 코딩을 막아준다. 


<br/>

## 4) settings.py 분리
* multiple layered settings files
* development, staging 그리고 production 단계별로 settings.py를 분리해야 한다. 

```sh
settings/
   ├─ __init__.py
   ├─ local.py
   ├─ dev.py
   └─ production.py   
```

<br/>

## 5) supervisor for monitoring 

* 배치(deploying) 시 모든 프로세스를 감시/관리할 수 있도록 supervisor를 사용하자. 

<br/>

## 6) AJAX/JSON 사용할 것

* 모든 HTTP 요청마다  페이지 전체로 reload할 것이 아니라면 AJAX를 써야 한다. 문제는 Django에 내장 JSON HTTP response가 없다는 점이다. 

* 관련 코드 
	* [decorator](https://github.com/samuelclay/NewsBlur/blob/master/utils/json_functions.py) 
	* [response](https://github.com/clutchio/clutch/blob/master/django_ext/http.py)


<br/>

## 7) Redis를 사용할 것

* celery jobs이나  session을 저장하는 것,  cache로 사용하는 것, 자동 완성 등을 구현할 수 있다. 

<br/>

## 8) munin이나 statds를 process mornitoring에 사용할 것
* 관련 코드와 사이트는 아래와 같다. 

>  [Munin](http://munin-monitoring.org/)
	* [munin plugins](https://github.com/samuel/python-munin)

> [statds](http://codeascraft.etsy.com/2011/02/15/measure-anything-measure-everything/)  

<br/>

## 9) jammit for static asset compression 사용 
* jammit은  static asset(css, javaScript 등)을 압축하는 모듈인데 ruby on rails에서 사용도니다. 그러나 python에서도 사용할 수 있는데 관련 사이트는 아래와 같다.
 
> [Jammit](http://documentcloud.github.io/jammit/)

>	 [jammit.py](https://github.com/samuelclay/NewsBlur/blob/master/utils/jammit.py)















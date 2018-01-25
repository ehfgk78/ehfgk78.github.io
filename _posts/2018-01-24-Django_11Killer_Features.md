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

> [Joseph Misiti - 11 Killer Features I use in Every Django Project ](http://medium.com/cs-math/2014-django-development-mistakes-in-2014-f48623f58b21)  
> * [raccoony의 번역 - 2014년에 Django로 개발하면서 실수한 부분](http://raccoonyy.github.io/django-development-mistakes-in-2014-translatate/) 
> * 이 글은 위 사이트의 내용을 요약 정리한 것입니다. 





# 2. 요약



## 1) Postgres DB엔진을 쓸 것 

* **Django ORM**  지원하지 않는 MongoDB등은  Django admin이나 **3rd party  라이브러리**를 사용할 수 없다. 
* Postgres가 2014. 12. 18.부터  JSONB 데이터 타입을 지원하기 시작하였으므로  Postgres에 **문서**를 저장할 수 있고, 성능 저하 없이 MongoDB처럼 쿼리를 실행할 수 있다. 
* **pycharm Professional edition(유로버전)**이 제공하는 Database tool window 기능을 활용하자.  DB접근이 쉬워진다. 
  * [Working with the Database tool window](https://www.jetbrains.com/help/pycharm/working-with-the-database-tool-window.html)
  * [Databases and SQL](https://www.jetbrains.com/help/pycharm/databases-and-sql.html) 








## 2) Unit Test를 위한 infra-structure를 구성할 것

* [ [ 여기 블로그 -TDD ] - Obey the Testing Goat!]( {{ site.baseurl }}{% post_url 2018-01-24-TDD01 %})  








## 3) DRF로 Restful하게 만들 것

* `Vue.js`, `Angular.js` 등 클라이언트 MVC 프레임워크의 수가 급증하므로 REST API를 구현할 **DRF**( django-rest-framework )를 사용하는 것이 좋아. 







## 4) Django model field의 `help_text`를 사용할 것

* `help_text`속성은 Django의 폼 화면이나 admin 화면에 나타나고  문서화에도 도움이 된다. 







## 5) Django의 URL 적용할 것 - 쿼리 문자열 쓰지 않기 

* `https://www.example.com/user?id=20` 대신  **`https://www.example.com/user/20/`를 쓰자.   컨트롤러(`view.py`)에서 타입 검사 같은 코드를 덜 작성하기 때문이다. 







## 6) Django ORM 최대한 사용하기

DB의 스키마 설계에 공을 많이 들이는 것이 좋다. 이때 Django ORM을 최대한 이용하면 잘못된 데이터를 저장하는 일 없이 수 많은 문제가 해결된다.  특히  DB 제약조건을  정확히 지정하는 것이 좋다. 

* null 값을 가질 수 없는 필드에는 null=True를 설정하지 마세요.
* 고유한 값을 저장하는 필드들은 unique_together로 지정해주세요.
* unique 파라미터도 적절히 사용해 주세요.
* max_length도 알맞게 지정하세요.





## 7) `django-extensions` 와 `Django-model-utils` 사용하기

* `django-extensions`  -  매우 뛰어난 3rd party library -  ❶ printing settings,

     ❷  shell_plus,   ❸ dumping scripts,   ❹ encryption, etc

* `django-model-utils` - Django ORM에서 지원하지 않거나  구현 방식이 조금 다른 유용한 기능들을 제공함 -   ❶ TimeStampField,   ❷ MonitorField,   ❸ Choices 등





## 8) Sentry 사용할 것 

* 가입형은 한 달에 $24부터 시작하며, 이 돈을 낼 만한 가치가 충분하다. 





## 9) `django-debug-toolbar`- 최적화, 디버깅

* SQL 쿼리와 요청, 템플릿, 캐시 등을 모두 추적할 수 있다. 





## 10) Custom User Model 처음부터 쓰기 

User 모델에 필드를 추가할 때도 기존의 방법(기본 User 모델과 OneToOne으로 엮인 새 모델을 만드는 방법)보다 직관적입니다. Django를 이미 사용하고 있다면 기본 User 모델의 email과 username 필드가 30 글자로 제한되어서 답답했을 겁니다. 커스텀 User 모델을 도입하면 이런 문제들이 모두 해결됩니다. 







## 11)  `django-suit` 등  django admin의 대체품 사용하기

* django-suit,  django-grapelli
* [Django Admin interface](https://github.com/rosarior/awesome-django#admin-interface) 
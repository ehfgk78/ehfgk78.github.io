---
layout: post
title: "jekyll 더 알아보기"
description: "jekyll에 대한 자세한 사용법."
categories: jekyll
tags: jekyll
redirect_from:
  - /2017/12/27/
---

* Kramdown table of contents
{:toc .toc}


# Know Where (참조)

> [Jekyll 공식 사이트](https://jekyllrb.com/)  
>
> _config.yml,  _post/,  _include/,  _layout/의 쓰임부터 시작하여 data/, asset/, plug-ins 등으로 확장하여 공부하는 게 좋습니다. 

> [Setting up your GitHub Pages site locally with Jekyll](https://help.github.com/articles/setting-up-your-github-pages-site-locally-with-jekyll/)

> [지킬 2015-11-23  한국어 공식 사이트](https://jekyllrb-ko.github.io/) 

> [이한영 강사의 블로그](https://lhy.kr/create-jekyll-blog-using-rbenv-and-github-pages)

> [Jekyll을 이용하여 github에 블로그 만들기](https://brunch.co.kr/@hee072794/39)


# Jekyll을 사용하는 이유
Jekyll은 **아주 간단하게**  블로그를 위한 정적 사이트를 만듭니다.   

① 데이터베이스 대신 **파일시스템**을 사용하여  ② **git**으로 블로그 포스트(소스)들을 관리하고  ③복잡한 HTML대신 **Markdown** 등 사용하기 쉬운 마크업으로 문서의 소스를 만들 수 있으며, ④ 무료로 **GitHub에 호스팅**하여 정적 사이트를 만들어 주기 때문입니다. 
다만  데이터베이스를 사용하지 않으므로  ❺ 댓글기능은 disqus 등 다른 서비스를 plug-in해야 합니다.  


# Ubuntu16.04에서 jekyll 설치하기 
## 환경 설정

내 컴퓨터(local)에서  Jekyll을 실행하려면  ***Ruby*** 가 필요한데, 바로 설치하기보다는 먼저 rbenv를 설치한 후 Ruby를 설치하자. 
 Ruby의 버전관리 및 가상환경이 주는 장점(버전 충돌방지 + 개개의 프로젝트 중심 환경설정)을 누리기 위해  `rbenv`를 사용하여 Ruby를 설치한다.  여기서는 Ubuntu16.04를 기준으로 설명하였다.  macOS는 아래 글을 참조하자. 

[Ubuntu16.04에서 rbenv로 Ruby를 설치하기](https://www.digitalocean.com/community/tutorials/how-to-install-ruby-on-rails-with-rbenv-on-ubuntu-16-04)

[macOS에서 Homebrew,  rbenv로 Ruby설치하기](https://lhy.kr/create-jekyll-blog-using-rbenv-and-github-pages) 


**1) rbenv 설치**  

```sh
# Update and install dependencies
➜ sudo apt-get update
➜ sudo apt-get install autoconf bison build-essential libssl-dev libyaml-dev libreadline6-dev zlib1g-dev libncurses5-dev libffi-dev libgdbm3 libgdbm-dev
# Install rbenv
➜ git clone https://github.com/rbenv/rbenv.git ~/.rbenv
# ~/.zshrc에 .rbenv의 실행파일 PATH에 대한 환경설정 
➜ echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.zshrc
➜ echo 'eval "$(rbenv init -)"' >> ~/.zshrc
# ~/.zshrc 환경실행
➜ source ~/.zshrc
# rbenv 설치 확인
➜ type rbenv

# ruby-build: git을 통해 ruby를 설치하기 위한 빌드 파일 
➜ git clone https://github.com/rbenv/ruby-build.git ~/.rbenv/plugins/ruby-build
```

**2) Ruby 설치** 

이 설치가 끝나면 **Gem** ( Ruby 패키지 관리자, RubyGems )이 설치된다. 

```sh
# 설치할 수 있는 ruby버전 목록 확인 
➜ rbenv install -i
# ruby 2.4.1 설치
➜ rbenv install 2.4.1
# 전역에 설치할 ruby버전 지정
➜ rbenv global 2.4.1
# 설치확인 
➜ rbenv versions
* 2.4.1 (set by /home/learn/.rbenv/version)
```

**3) rehash 작업** 

rehash는 rbenv가 관리하는 루비 명령어들을  `~/.rbenv/shims`에 셀스크립트파일로 복사해주는 과정이다. 

➀ 터미널을 새로 실행(`rbenv init -`)하거나  ➁ 기존 터미널에서 아래 명령어를 실행해준다. 

```sh
➜ rbenv rehash
```

**4) bundle,  github-pages** 설치 

* Gem이 시스템 전역으로 패키지를 관리한다면 bundle은 **현재 프로젝트 폴더**에서 사용할 패키지를  `Gemfile`과  `Gemfile.lock`을 통하여 관리한다. 
* github-pages는 GitHub에서 사용할 Jekyll관련 의존성 패키지들을 관리하는 Gem이다. 

```sh
➜ gem install jekyll bundler github-pages
```



## (local) jekyll 블로그 생성

```sh
➜ jekyll new <블로그이름>

Running bundle install in /home/learn/projects/MyBlog... 
  Bundler: To update, run `gem install bundler`
  Bundler: To update, run `gem install bundler`

<블로그이름> 폴더의 구조 
   ├── 404.html
   ├── Gemfile
   ├── Gemfile.lock
   ├── _config.yml
   ├── _posts
   │      └── 2017-12-08-welcome-to-jekyll.markdown
   ├── about.md
   └── index.md

➜ cd <블로그이름>
# bundle이 관리하는 현재 폴더 내의 패키지 설치 및 업데이트 
➜ bundle install
➜ bundle update

# jekyll 정적 사이트를  내 컴퓨터(local)에서 실행한다.
➜ bundle exec jekyll serve
# localhost:4000 접속 
# _site 폴더가 생성됨:: 정적 사이트 생성 결과물 

# Site 2
$ bundle exec jekyll serve --host myhost --port 1234
# => "http://myhost:1234"
```

## GitHub에 배포

GitHub는 Jekyll 실행 결과(`_site`) 대신 **Jekyll 소스(`_posts`내 마크다운 문서)** 를 직접 이용한다. 

* **GitHub 저장소 생성**:  Create a new repository  ➨ `<자신의 유저명>.github.io`형식 준수
* **로컬 git 저장소 생성** - `git init`
* **`.gitignore` 작성** -  Ruby, Jekyll, Ubuntu
* **GitHub 저장소( remote 저장소 )**에 올리기 

```sh
# 로컬 저장소 생성 
➜ git init
# gitignore대상은 gitignore.io를 사용한다.  
## 버전관리제외 대상은 Ruby, Linux, Jekyll이다. 
➜ vi .gitignore 
➜ git add -A
➜ git commit -m 'First commit'
➜ git remote add git@github.com:LeeHanYeong/LeeHanYeong.github.io.git
➜ git push origin master
```

```sh
<블로그이름>폴더 구조 
   ├── .git
   ├── .gitignore
   ├── .sass-cache
   ├── _site
   ├── 404.html
   ├── Gemfile
   ├── Gemfile.lock
   ├── _config.yml
   ├── _posts  
   ├── about.md
   └── index.md
```
➫ 확인  [https://ehfgk78.github.io](https://ehfgk78.github.io)


# Jekyll 디렉토리 구조와 기능 

Jekyll의 디렉토리 구조와 그에 따른 기능은 다음과 같습니다.

```sh
폴더 구조
├─  _config.yml  #환경설정 정보
├─  _data
├─  _drafts      #초안
├           └─ begin-with-the-crazy-ideas.md 
├─  _includes    #삽입용 템플릿
├           ├─ footer.html 
├           └─ header.html
├─  _layouts     #템플릿 
├           ├─ default.html  
├           └─ post.html
├─  _posts
├─  _site        #Jekyll 실행 결과 생성된 사이트가 저장되는 경로
├─  .jekyll-metadata    #Jekyll 실행시 참조하는 파일 
└─  index.html 
```


## _config.yml

Jekyll 전역 환경 설정 

```ini
# Site Settings
encoding: UTF-8              #파일의 인코딩 
source: DIR                  #읽을 파일들의 경로 
destination: DIR             #생성 파일들의 경로 
safe: BOOL                   #사용자 플러그인 비활성화, 심볼릭링크 무시
exclude: [DIR, FILE, ...]    #파일변환 제외
include: [DIR, FILE, ...]    #변환대상에 강제로 포함시킴, .htaccess
keep_files: [DIR, FILE, ...] #사이트 생성 전 destination 초기화 때 
                             #보관할 파일을 지정한다. 

timezone: TIMEZONE
encoding: utf-8
defaults                     :#YAML Front Matter 변수 default값 설정

## 고유 주소(URL)                             
permalink: /blog/:year/:month/:day/:title/  
## 플러그인
plugins:
  - jekyll-feed
  - jekyll-redirect-from
```
Build 명령어 옵션

Serve 명령 옵션

마크다운 옵션 


## YAML Front Matter (머리말)  

Jekyll은[YAML](http://yaml.org/) 머리말 블록을 가진 모든 파일을 특별한 파일로 인식하여 처리합니다. 머리말은 반드시 올바른 YAML 형식으로 작성되어야 하며, **대시문자 3 개(- - -)로 감싸서** 파일의 맨 첫 부분에 위치해야 합니다.  머리말에 변수를 쓸 수 있으나 반드시 필요한 것은 아닙니다.  

```sh
- - -
layout: post
title: Blogging Like a Hacker
- - -
```
**머리말에 쓰이는 사전 정의 변수 ( Predefined Variables )**

 [Predefined Global Variables](https://jekyllrb.com/docs/frontmatter/#predefined-global-variables)

| 변수         | 설명                                       |
| ---------- | ---------------------------------------- |
| layout     | `_layouts`에 있는 레이아웃 파일을 적용한다.            |
| permalink  | URL설정   `/year/month/day/title.html`형태를 바꿀 수 있다. |
| published  | 사이트 생성시  발행여부 (true / false)             |
| categories | 특정 폴더 대신 카테고리에 넣을 때, 쉼표로 여러 카테고리에 넣을 수 있다. |
| tags       | 카테고리와 같음                                 |
| date       | 포스트의 날짜를 덮어쓴다.           |


**Custom Variables** (사용자 정의 변수)

머리말에 있는 변수들이 사전에 정의되지 않았을 경우  **변환시 Liquid 템플릿 엔진에 전달**된다. 

```sh
구문 
<!DOCTYPE HTML>
<html>
  <head>
    <title> { { page.title } } </title>
  </head>
  <body>
      …
``` 
위 구문을 이용한 결과는 다음과 같다. 
```html

<!DOCTYPE HTML>
<html>
  <head>
    <title> {{ page.title }} </title>
  </head>
  <body>
      …
``` 

# _posts/ 

**파일명** 은 이 형식을 지켜야 한다. 

```sh
YEAR-MONTH-DAY-title.MARKUP
2011-12-31-new-years-eve-is-awesome.md
2012-09-12-how-to-write-a-blog.textile 
```

다른 포스트로 **링크**할 때에는 `post_url`태그를 사용하고, 
YAML 머리말을 반드시 작성해야 한다. 

```md
- - -
layout: post
title:  "Welcome to Jekyll!"
date:   2015-11-17 16:16:01 -0600
categories: jekyll update
- - -

You’ll find this post in your **_posts/** directory. 
Go ahead and edit it and re-build the site to see your changes. 
You can rebuild the site in many different ways, 
but the most common way is to run **bundle exec jekyll serve**, 
which launches a web server and auto-regenerates your site when a file is updated.
```


## assets/  `이미지와 자원 삽입`

```md
![친절한 스크린샷]( { { "/assets/screenshot.jpg" | absolute_url } } )
[다운로드]( { { "/assets/mydoc.pdf" | absolute_url } } )
```


## index of posts : 포스트 리스트

Liquid 템플릿 언어의 기능을 이용한다. 

**템플릿이 작동하는 방법** 은 나중에 소개한다. 

 
```html
구문
<ul>
  { % for post in site.posts % }
    <li> <a href="{ { post.url } }">{ { post.title } }</a> </li>
  { % endfor % }
</ul>
```
위 구문의 결과는 다음과 같다. 
```html
<ul>  
    <li> <a href="/blog/2017/12/27/jekyll-detail/"> jekyll 더 알아보기 </a></li>  
    <li> <a href="/blog/2017/12/26/guake-and-mc/"> Ubuntu 터미널 Guake와 파일관리자 MC를 사용하기 </a></li>  
    <li> <a href="/blog/2017/12/20/welcome-to-jekyll/"> Jekyll 사용법 </a></li>
</ul>    
```

## post categories or tags

`_layouts`폴더에서  category.html 파일을 만들었다고 가정한다. 

```html
- - -
layout: page
- - -

{ % for post in site.categories[page.category] % }
 <a href="{ { post.url | absolute_url } }">
    { { post.title } }
 </a>
{ % endfor % }
```


**category/** 폴더에서  blog 카테고리에 속하는 `blog.html`이 있다고 가정한다 . 

```markdown
- - -
layout: category
title: Blog
category: blog
- - -
```
이 경우, 목록 페이지는 다음과 같이 접근할 수 있다. 
```markdown
{baseurl}/category/blog.html
```

## Post excerpts: 포스트 발췌하기 

Jekyll은 **모든 포스트의 첫 블록(Default)** 또는 **컨텐츠 시작부터 `excerpt_separator`가 나오는 곳 까지의 부분**을 
`post.excerpt`로 설정합니다.   

```sh
 <ul>
   { % for post in site.posts % }
     <li>
       <a href="{ { post.url } }">  { { post.title } } </a>
       { { post.excerpt } }
     </li>
   { % endfor % }
 </ul>
```

**Customize: 발췌 부분 정하기**

포스트의 ***YAML 머리말***에 `excerpt_separator`를 정하면 
본문에서 발췌범위를 정할 수 있습니다. 
전역 설정은 위 YAML 머리말 대신 ***_config.yml***에서 `excerpt_separator`를 정하면 됩니다. 
  
```markdown
- - -
excerpt_separator: <!--more-->
- - -

Excerpt
<!--more-->

Out-of-excerpt
```

**발췌 비활성화**

전역 설정 **_config.yml**에서 다음과 같이 설정한다. 
```sh
excerpt_separator: ""   # 발췌 비활성화 
```

## 구문 강조 

**Highlighting code snippets**  

```html
{ % highlight ruby % }
def show
  @widget = Widget(params[:id])
  respond_to do |format|
    format.html # show.html.erb
    format.json { render json: @widget }
  end
end
{ % endhighlight % }
```

```ruby
# 결과 
def show
  @widget = Widget(params[:id])
  respond_to do |format|
    format.html # show.html.erb
    format.json { render json: @widget }
  end
end
```

**코드 줄 번호** 를 넣으려면, 다음과 같다. 

```html
{ % highlight ruby linenos % }
```


# post외 게시물 작성

**Homepage (주로 `index.html`)** 등은 날짜 기반의 게시물이 아닌 **정적 페이지**입니다. 
 
 정적 페이지를 작성하는 방법은,
 * 블로그의 root 폴더에 `.html` 또는 `.md/.markdown`파일을 작성하거나 
 * 임의의 폴더에 정적 페이지를 작성하면 됩니다. 
   
**root 폴더에서 정적 페이지 만들기**

```sh
.
├  _config.yml
├  _includes/
├  _layouts/
├  _posts/
├  _site/
├  about.html    # => http://example.com/about.html
├  index.html    # => http://example.com/
├  other.md      # => http://example.com/other.html
└  contact.html  # => http://example.com/contact.html
```

**임의의 폴더에서 정적 페이지를 작성하기**   

임의의 폴더에서 작성한 정적 페이지의 YAML머리말에  **permalink**를 덧붙입니다.

```sh
- - -
title: My page
permalink: mypageurl.html
- - -
```

# Static Files

static files는 <u>YAML 머리말이 없는 파일</u>을 말한다. 

머리말이 없으므로 Jekyll에 의해서 랜더링되거나 변환되지 않습니다. 
여기에는 이미지, pdf 등을 포함합니다.  

이 파일에 대한 접근은 **site.static_files**로 통하고, 다음과 같은 metadata가 있습니다. 

| 변수                | 설명                              |
| ------------------ | -------------------------------- |
| file.path          | 상대 경로  `/assets/img/image.jpg` |
| file.modified_time | `2016-04-01 16:35:26 +0200`      |
| file.name          |                                  |


**_config.yml** 

_config.yml에서 다음과 같이 설정하면, img파일이 assets/img에 있으며, 
YAML머리말에 `image: true`이 있는 것으로 간주됩니다. 

```yaml
defaults:
  - scope:
      path: "assets/img"
    values:
      image: true
```

HTML파일에는 다음과 같이 처리합니다. 

```html
{ % assign image_files = site.static_files | where: "image", true % }
{ % for myimage in image_files % }
  { { myimage.path } }
{ % endfor % }
```

# Variables

지킬 (Jekyll)은 **사이트를 탐색하여 처리 할 파일을 찾습니다.**  
_config.yml ,YAML머리말에서 변수를 설정하고, Liquid templating system에서 변수를 처리합니다.
 
| Global 변수: site         | page            | layout   | content    | paginator                    |
| ------------------------ | --------------- | -------- | ---------  | ---------------------------- |
| _config.yml              | **머리말**       |**머리말** | **layout** |                              |
| site.time                | page.content    |          |            | paginator.per_page           |
| site.pages               | page.title      |          |            | paginator.posts              |
| site.posts               | page.excerpt    |          |            | paginator.total_posts        |
| site.related_posts       | page.url        |          |            | paginator.total_pages        |
| site.static_files        | page.date       |          |            | paginator.page               |
| site.html_pages          | page.id         |          |            | paginator.previous_page      |
| site.collections         | page.categories |          |            | paginator.previous_page_path |
| site.data                | page.tag        |          |            | paginator.next_page          |
| site.documents           | page.path       |          |            | paginator.next_page_path     |
| site.categories.CATEGORY | page.next       |          |            |                              |
| site.tags.TAG            | page.previous   |          |            |                              |
| site.url                 |                 |          |            |                              |


# _data/

Variables 외에도 Data를 **Liquid템플릿 시스템**을 통하여 처리할 수 있습니다.  
일단 YAML, JSON, and CSV 파일 등을 **_data/**폴더에 저장해야 합니다. 
(CSV파일에는 반드시 header row가 있어야 합니다.)
그러나 **_config.yml**파일을 수정할 필요가 없습니다.  

**<u>플러그인 / 테마</u>** 또한 데이터 파일을 사용하여 구성 변수를 설정할 수도 있습니다.

1) 용례: List of members

**_data/members.yml** 

```sh
- name: Eric Mill
  github: konklone

- name: Parker Moore
  github: parkr

- name: Liu Fengyun
  github: liufengyun
```    

  **_data/members.csv** 

```sh
name,github   # Header row !!! 
Eric Mill,konklone
Parker Moore,parkr
Liu Fengyun,liufengyun
```

위 데이터는 **site.data.members**로 접근할 수 있습니다. 
이를 **Liquid템플릿 시스템**을 통하여 아래와 같이 처리할 수 있습니다. 

```html
<ul>
{ % for member in site.data.members % }
  <li>
    <a href="https://github.com/{ { member.github } }">
      { { member.name } }
    </a>
  </li>
{ % endfor % }
</ul>
```



2) 용례: Organizations 

**_data/orgs/jekyll.yml**
```yml
username: jekyll
name: Jekyll
members:
  - name: Tom Preston-Werner
    github: mojombo

  - name: Parker Moore
    github: parkr
```

위 데이터에 접근하려면 **site.data.orgs**를 사용합니다. 
이를 **Liquid템플릿 시스템**을 통하여 아래와 같이 처리할 수 있습니다. 

```html
<ul>
{ % for org_hash in site.data.orgs % }
{ % assign org = org_hash[1] % }
  <li>
    <a href="https://github.com/{ { org.username } }">
      { { org.name } }
    </a>
    ({ { org.members | size } } members)
  </li>
{ % endfor % }
</ul>
```

2) 용례: Accessing a specific author 

**_data/people.yml**

```sh
dave:
    name: David Smith
    twitter: DavidSilvaSmith
```

위 **dave 데이터**에 대한 접근은  특정 페이지의 YAML머리말에서 다음과 같이 이루어집니다. 

```html
- - -
title: sample post
author: dave
- - -

{ % assign author = site.data.people[page.author] % }
<a rel="author"
  href="https://twitter.com/{ { author.twitter } }"
  title="{ { author.name } }">
    { { author.name } }
</a>
```

# Template
Jekyll은 Liquid 템플릿 언어를 사용하여 템플릿을 처리합니다. 
모든 표준 Liquid 태그 및 필터가 지원됩니다.

**Filters**

```html
Relative URL : 도메인의 root가 아닌 subpath를 쓸 때 유용하다 
쓰임
{ { "/assets/style.css" | relative_url } }
결과 
/my-baseurl/assets/style.css

Absolute URL
쓰임 
{ { "/assets/style.css" | absolute_url } }
결과 
http://example.com/my-baseurl/assets/style.css

Date to String
쓰임
{ { site.time | date_to_string } }
결과 
07 Nov 2008

Where : 키에 지정된 값이있는 배열의 모든 객체를 선택합니다.
{ { site.members | where:"graduation_year","2014" } }


Where Expression: True인 경우 배열의 모든 객체를 선택합니다. 
예 01 
{ { site.members | where_exp:"item",
    "item.graduation_year == 2014" } } 
예 02
{ { site.members | where_exp:"item",
    "item.graduation_year < 2014" } } 
예 03
{ { site.members | where_exp:"item",
    "item.projects contains 'foo'" } }

Number of Words
{ { page.content | number_of_words } }
1337

Sort
{ { page.tags | sort } }
{ { site.posts | sort: 'author' } }
{ { site.pages | sort: 'title', 'last' } }
```

**Include**
```html
{ % include footer.html % }
```
Jekyll은 모든 include 파일이 소스 디렉토리의 루트에 있는 **_includes 디렉토리**에 위치 할 것으로 기대합니다. 
위의 예에서는 _includes / footer.html의 내용이 포함됩니다. 

**Links**
```html
{ { site.baseurl } }{ % link _collection/name-of-document.md % }
{ { site.baseurl } }{ % link _posts/2016-07-26-name-of-post.md % }
{ { site.baseurl } }{ % link news/index.html % }
{ { site.baseurl } }{ % link /assets/files/doc.pdf % }
```

Markdown에서도 링크할 수 있습니다. 

```markdown
[ Link to a document ]({ { site.baseurl } }{ % link _collection/name-of-document.md % })
[ Link to a post ]({ { site.baseurl } }{ % link _posts/2016-07-26-name-of-post.md % })
[ Link to a page ]({ { site.baseurl } }{ % link news/index.html % })
[ Link to a file ]({ { site.baseurl } }{ % link /assets/files/doc.pdf % })
```

**포스트에 연결하기**
```html
{ { site.baseurl } }{ % post_url 2010-07-21-name-of-post % }
또는
{ { site.baseurl } }{ % post_url /subdir/2010-07-21-name-of-post % }
```

# Include

**1) 기초 사용법: _include/**

```html
{ % include footer.html % }
```
위 구문은  _include / footer.html을 포함하겠다는 의미입니다. 

**2) _include/ 가 아닌 폴더**

포함된 컨텐츠를 _includes 디렉토리에 둘 필요가 없습니다.
이 경우 `include_relative`를 사용합니다. 

```html
{ % include_relative somedir/footer.html % }
```

**3) 변수 사용하기**

```html
- - -
title: My page
my_variable: footer_company_a.html
- - -
...
{ % include { { page.my_variable } } % }
...
```
위 구문은 `{ % include footer_company_a.html % }`와 같다. 


**4) 매개변수를 includes 파일로 전달하기 (Passing parameters to includes)**

`_includes / note.html`

```sh
<div markdown="span" class="alert alert-info" role="alert">
<i class="fa fa-info-circle"></i> <b>Note:</b>
{{ include.content }}
</div>
```

```html
{ % include note.html content="This is my sample note." % }
```
매개변수(여기, 'content')를 include 파일에 전달하는 것은 Markdown 콘텐츠에서 복잡한 형식을 숨기고 싶을 때 유용합니다.

```sh 
<figure>
   <a href="http://jekyllrb.com">
   <img src="logo.png" style="max-width: 200px;"
      alt="Jekyll logo" />
   <figcaption>This is the Jekyll logo</figcaption>
</figure>
```
`_include/image.html`
```sh
<figure>
   <a href="{ { include.url } }">
   <img src="{ { include.file } }" style="max-width: { { include.max-width } };"
      alt="{ { include.alt } }"/>
   <figcaption>{ { include.caption } }</figcaption>
</figure>
```

```sh
{ % include image.html 
    url="http://jekyllrb.com"
    max-width="200px" 
    file="logo.png" 
    alt="Jekyll logo"
    caption="This is the Jekyll logo." % }
```

**5) 매개변수(parameter)에 변수(variable)를 includes 파일로 전달하기**

include에 전달하는 매개변수에 변수를 포함하려면,
include로 전달하기 전에 **전체 매개 변수를 변수로 저장**해야합니다. 
`capture` 태그를 사용하여 변수를 만들 수 있습니다.(아래, download_note)

```sh
{ % capture download_note % }
The latest version of { { site.product_name } } is now available.
{ % endcapture % }
```
위와 같이 만든 변수를 include의 매개변수에 포함하면 됩니다. 

```sh
{ % include note.html content=download_note % }
```

한편, 위 site.product_name은 Variables를 살펴보자
(**_config.yml**에 site.product_name이 정의되어 있다.)


**6) 매개변수에 데이터 파일의 내용을 includes 파일로 전달하기**

_data / profiles.yml

```yml
- name: John Doe
  login_age: old
  image: johndoe.jpg

- name: Jane Doe
  login_age: new
  image: janedoe.jpg
```

_includes / sportlight.html

```sh
{ % for person in include.participants % }
{ % if person.login_age == "new" % }
{ { person.name } }
{ % endif % }
{ % endfor % }
```

이제 spotlight.html 포함 파일을 삽입하면 매개 변수로 YAML 파일을 제출할 수 있습니다.

```sh
{ % include spotlight.html participants=site.data.profiles % }
```


# Permalinks (고유주소)

**1) Permalinks**

Permalinks는 블로그의 게시물(posts), 정적페이지(pages), collections의 URL을 지칭합니다.
예를들어, post 템플릿 permalink의 기본 형태는 `/:categories/:year/:month/:day/:title.html`입니다.


**2) 환경 설정**

* _config.yml  

```sh
permalink: /:categories/:year/:month/:day/:title.html
```
위 구문은 아래와 같이 내장 permalink style을 사용하여 간략하게 사용할 수 있다. 
```sh
permalink: date
```

* YAML 머리말

```markdown
- - -
title: My page title
permalink: /mypageurl/
- - -
```

**3) Template variables for permalinks**

변수 | 설명 
--|--
year, month, day | YAML머리말의 date 겹쳐쓰기;  post파일이름
hour, minute, second | YAML머리말에서 date 겹쳐쓰기
i_month, i_day | YAML머리말에서 date 겹쳐쓰기; 0으로 시작되지 않음
title, slug | YAML머리말에서 slug 겹쳐쓰기; post파일이름 
categories | /category01/category02등으로 쓸 수 있다. 

**4) Built-in permalink styles**

permalink style | URL Template
--|--
date | `/:categories/:year/:month/:day/:title.html`
pretty | `/:categories/:year/:month/:day/:title/`
ordinal | `/:categories/:year/:y_day/:title.html`
none | `/:categories/:title.html`

사용례 : `/2009-04-29-slap-chop.md`

URL Template | 결과 
--|--
none 또는 `permalink: date` |  `/2009/04/29/slap-chop.html`
`pretty` |  `/2009/04/29/slap-chop/`
`/:month-:day-:year/:title.html` | `/04-29-2009/slap-chop.html`
`/blog/:year/:month/:day/:title/` | `/blog/2009/04/29/slap-chop/`
`/:year/:month/:title`  |  `/2009/04/slap-chop`


**5) 정적 페이지와 collections를 위한 Permalink**

이 경우 categories, date는 무시된다. 예를들어,

`/:categories/:year/:month/:day/:title.:output_ext`는 `/:title.html`로 된다. 

**6) default paths**

* _posts/ categoryname/2016/12/01/mypostname/index.html

* mypage/ index.html

* _site/ collection_name/mypage/index.html


# 페이지 나누기 (Pagination)

많은 웹 사이트 - 특히 블로그 -에서 게시물의 주요 목록을 작은 목록으로 나누어 여러 페이지에 걸쳐 표시 할 수 있습니다. 
Jekyll은 **pagination 플러그인(jekyll-paginate)**을 사용하여 
페이지 매김된 파일과 폴더를 자동으로 만들 수 있습니다. 

**☂ 페이지 나누기는 HTML 파일에서만 작동합니다.**

**☂ permalink(고유주소)를 YAML 머리말에 설정하면 페이지 나누기가 되지 않습니다.**

**☂ jekyll-paginate-v2 플러그인은 categories, tags, collection에도 적용할 수 있습니다.** 

**☂ 페이지 나누기는 tag, categories를 지원하지 않습니다.** 


**1) 페이지 나누기 환경 설정 : `_config.yml`** 


```yaml 
 paginate: 5 
```
페이지 당 게시할 item의 최대 개수를 정한다. 아래와 같이 페이지 나누기할 위치를 정할 수 있다.

```markdown
 paginate_path: "/blog/page:num/"
```
위 구문은 blog/index.html의 Liquid `paginator`를 거칩니다. 

예를 들어 12개의 포스트가 있고 _config.yml에서 `paginate: 5`로 설정되었다면 Jekyll은,  
```markdown
`blog/index.html`에 5개의 포스트를,
`blog/page2/index.html`에 다음 5개의 포스트를,
`blog/page3/index.html`에 마지막 2개의 포스트를 작성합니다. 
```

**2) paginator liquid object의 attribute**

Attribute |  설명 
--|--
`paginator.page` | 현재 페이지
`paginator.per_page` | 페이지당 포스트 개수 
`paginator.posts` | 현재 페이지의 포스트 개수 
`paginator.total_posts` | 사이트 포스트의 총 개수
`paginator.total_page` | 총 페이지 개수
`paginator.previous_page` | 이전 페이지 숫자,  없으면 `nil`
`paginator.previous_page_path` | 이전 페이지의 path, 없으면 `nil`
`paginator.next_page` | 다음 페이지의 숫자, 없으면 'nil'
`paginator.next_page_path` | 다음 페이지의 path


**3) 페이지 매김된 Post를 랜더링하기**
```sh
- - -
layout: default
title: My Blog
- - -

<!-- This loops through the paginated posts -->
{ % for post in paginator.posts % }
  <h1><a href="{ { post.url } }">{ { post.title } }</a></h1>
  <p class="author">
    <span class="date">{ { post.date } }</span>
  </p>
  <div class="content">
    { { post.content } }
  </div>
{ % endfor % }

<!-- Pagination links -->
<div class="pagination">
  { % if paginator.previous_page % }
    <a href="{ { paginator.previous_page_path } }" class="previous">Previous</a>
  { % else % }
    <span class="previous">Previous</span>
  { % endif % }
  <span class="page_number ">Page: { { paginator.page } } of { { paginator.total_pages } }</span>
  { % if paginator.next_page % }
    <a href="{ { paginator.next_page_path } }" class="next">Next</a>
  { % else % }
    <span class="next">Next</span>
  { % endif % }
</div>
```

다음 HTML은 페이지 1을 처리하며 현재 페이지를 제외한 모든 페이지에 대한 링크가 있는 각 페이지의 목록을 렌더링합니다.
```sh 
{ % if paginator.total_pages > 1 % }
<div class="pagination">
  { % if paginator.previous_page % }
    <a href="{ { paginator.previous_page_path | prepend: site.baseurl | replace: '//', '/' } }">&laquo; Prev</a>
  { % else % }
    <span>&laquo; Prev</span>
  { % endif % }

  { % for page in (1..paginator.total_pages) % }
    { % if page == paginator.page % }
      <em>{ { page } }</em>
    { % elsif page == 1 % }
      <a href="{ { paginator.previous_page_path | prepend: site.baseurl | replace: '//', '/' } }">{ { page } }</a>
    { % else % }
      <a href="{ { site.paginate_path | prepend: site.baseurl | replace: '//', '/' | replace: ':num', page } }">{ { page } }</a>
    { % endif % }
  { % endfor % }

  { % if paginator.next_page % }
    <a href="{ { paginator.next_page_path | prepend: site.baseurl | replace: '//', '/' } }">Next &raquo;</a>
  { % else % }
    <span>Next &raquo;</span>
  { % endif % }
</div>
{ % endif % }
```

# Plugins
Jekyll은 플러그인을 통하여 <u>소스코드를 직접 수정하지 않고도 자신의 코드가 실행</u>할 수 있습니다.

**☂ GitHub Pages**는 보안상의 이유로 --safe옵션을 통하여 사용자 플러그인이 비활성화된 상태에서  생성됩니다.
따라서 GitHub Pages는 사용자 플러그인을 직접 사용할 수 없습니다. 
그래도 로컬에서 사용자 플러그인을 설치하여 <u>변환을 먼저 한 후</u> 생성된 파일을 올리면 됩니다.  


## 1) Installing a plugin : 3가지 옵션 
➀ `_plugins` 폴더를 site source root에 만들고서 플러그인을 넣는다. `.rb`로 마무리 짓기.
 
❷ `_config.yml`에 플러그인 등록하기 
```sh
# This will require each of these plugins automatically.
plugins:
  - jekyll-gist
  - jekyll-coffeescript
  - jekyll-assets
  - another-jekyll-plugin
```
이 후 플러그인을 설치한다. 
```markdown
gem install jekyll-gist jekyll-coffeescript jekyll-assets another-jekyll-plugin
```
 
➂ **Gemfile**에 플러그인을 설치하기 
```markdown
 group :jekyll_plugins do
   gem "jekyll-gist"
   gem "jekyll-coffeescript"
   gem "jekyll-assets"
   gem "another-jekyll-plugin"
 end
```
이후  `bundle install`을 한다. 


## 2) jekyll 플러그인 그룹 
이 부분은 Ruby를 알아야 진행할 수 있으므로 생략한다. 

**➀ Generators   ❷ Converters  ➂ Commands  ❹ Tags  ➄ Hooks**

## 3) 유용한 플러그인들 
1) Generator 

[ArchiveGenerator](https://gist.github.com/707909)

[LESS.js Generator](https://gist.github.com/642739)

[Version Reporter by Blake Smith](https://gist.github.com/449491)

[Sitemap.xml Generator by Michael Levin](https://github.com/kinnetica/jekyll-plugins) 

[Full-text search](https://github.com/PascalW/jekyll_indextank)

[AliasGenerator](https://github.com/tsmango/jekyll_alias_generator)

[Pageless Redirect Generator](https://github.com/nquinlan/jekyll-pageless-redirects) Generates redirects based on files in the Jekyll root, with support for htaccess style redirects.

[RssGenerator](https://github.com/agelber/jekyll-rss)  Automatically creates an RSS 2.0 feed from your posts.

[Monthly archive generator](https://github.com/shigeya/jekyll-monthly-archive-plugin)  Generator and template which renders monthly archive like MovableType style, based on the work by Ilkka Laukkanen and others above.

[Category archive generator](https://github.com/shigeya/jekyll-category-archive-plugin)  Generator and template which renders category archive like MovableType style, based on Monthly archive generator.

[Emoji for Jekyll](https://github.com/yihangho/emoji-for-jekyll)  Seamlessly enable emoji for all posts and pages.

[Compass integration for Jekyll](https://github.com/mscharley/jekyll-compass)   Easily integrate Compass and Sass with your Jekyll website.

- [Pages Directory](https://github.com/bbakersmith/jekyll-pages-directory): Defines a `_pages` directory for page files which routes its output relative to the project root.
- [Page Collections](https://github.com/jeffkole/jekyll-page-collections): Generates collections of pages with functionality that resembles posts.
- [Windows 8.1 Live Tile Generation](https://github.com/sheehamj13/jekyll-live-tiles): Generates Internet Explorer 11 config.xml file and Tile Templates for pinning your site to Windows 8.1.
- [Typescript Generator](https://github.com/sheehamj13/jekyll_ts): Generate Javascript on build from your Typescript.
- [Jekyll::AutolinkEmail](https://github.com/ivantsepp/jekyll-autolink_email): Autolink your emails.
- [Jekyll::GitMetadata](https://github.com/ivantsepp/jekyll-git_metadata): Expose Git metadata for your templates.
- [Jekyll Http Basic Auth Plugin](https://gist.github.com/snrbrnjna/422a4b7e017192c284b3): Plugin to manage http basic auth for jekyll generated pages and directories.
- [Jekyll Auto Image](https://github.com/merlos/jekyll-auto-image): Gets the first image of a post. Useful to list your posts with images or to add [twitter cards](https://dev.twitter.com/cards/overview) to your site.
- [Jekyll Portfolio Generator](https://github.com/codeinpink/jekyll-portfolio-generator): Generates project pages and computes related projects out of project data files.
- [Jekyll Flickr Plugin](https://github.com/lawmurray/indii-jekyll-flickr)  Generates posts for photos uploaded to a Flickr photostream.
- [Jekyll::Paginate::Category](https://github.com/midnightSuyama/jekyll-paginate-category): Pagination Generator for Jekyll Category.
- [AMP-Jekyll by Juuso Mikkonen](https://github.com/juusaw/amp-jekyll): Generate [Accelerated Mobile Pages](https://www.ampproject.org/)of Jekyll posts.
- [Jekyll Art Gallery plugin](https://github.com/alexivkin/Jekyll-Art-Gallery-Plugin): An advanced art/photo gallery generation plugin for creating galleries from a set of image folders. Supports image tagging, thumbnails, sorting, image rotation, post-processing (remove EXIF, add watermark), multiple collections and much more.
- [jekyll-ga](https://github.com/developmentseed/jekyll-ga): A Jekyll plugin that downloads Google Analytics data and adds it to posts. Useful for making a site that lists “most popular” content. [Read the introduction](https://developmentseed.org/blog/google-analytics-jekyll-plugin/) post on the developmentSEED blog.
- [jekyll-multi-paginate](https://github.com/fadhilnapis/jekyll-multi-paginate): Simple Jekyll paginator for multiple page. Ease you to make pagination on multiple page especially like multiple language.
- [jekyll-category-pages](https://github.com/field-theory/jekyll-category-pages): Easy-to-use category index pages with and without pagination. Supports non-URL-safe category keywords and has extensive documentation and test coverage.
- [Stickyposts](https://github.com/ibrado/jekyll-stickyposts): Moves or copies (pins) posts marked `sticky: true` to the top of the list. Perfect for keeping important announcements on the home page, or giving collections a descriptive entry. Paginator friendly.
- [Jekyll::Paginate::Content](https://github.com/ibrado/jekyll-paginate-content): Content paginator in the style of jekyll-paginator-v2 that splits pages, posts, and collection entries into several pages. Specify a separator or use HTML <h1> etc. headers. Automatic splitting, single-page view, pager/trail, self-adjusting links, multipage TOC, SEO support.



2) Converters

- [Textile converter](https://github.com/jekyll/jekyll-textile-converter): Convert `.textile` files into HTML. Also includes the `textilize` Liquid filter.
- [Sass SCSS Converter by Mark Wolfe](https://gist.github.com/960150): Sass converter which uses the new CSS compatible syntax, based Sam X’s fork above.
- [LESS Converter by Jason Graham](https://gist.github.com/639920): Convert LESS files to CSS.
- [LESS Converter by Josh Brown](https://gist.github.com/760265): Simple LESS converter.[Customized Kramdown Converter](https://github.com/mvdbos/kramdown-with-pygments): Enable Pygments syntax highlighting for Kramdown-parsed fenced code blocks.
- [Bigfootnotes Plugin](https://github.com/TheFox/jekyll-bigfootnotes): Enables big footnotes for Kramdown.



3) Filters

- [Domain Name Filter by Lawrence Woodman](https://github.com/LawrenceWoodman/domain_name-liquid_filter): Filters the input text so that just the domain name is left.
- [Summarize Filter by Mathieu Arnold](https://gist.github.com/731597): Remove markup after a `<div id="extended">` tag.
- [Read in X Minutes](https://gist.github.com/zachleat/5792681) by [zachleat](https://github.com/zachleat): Estimates the reading time of a string (for blog post content).
- [Jekyll-timeago](https://github.com/markets/jekyll-timeago): Converts a time value to the time ago in words.
- [pluralize](https://github.com/bdesham/pluralize): Easily combine a number and a word into a grammatically-correct amount like “1 minute” or “2 minute**s**”.
- [reading_time](https://github.com/bdesham/reading_time): Count words and estimate reading time for a piece of text, ignoring HTML elements that are unlikely to contain running text.
- [Table of Content Generator](https://github.com/dafi/jekyll-toc-generator): Generate the HTML code containing a table of content (TOC), the TOC can be customized in many way, for example you can decide which pages can be without TOC.
- ✔ [jekyll-toc](https://github.com/toshimaru/jekyll-toc): A liquid filter plugin for Jekyll which generates a table of contents.
- [jekyll-humanize](https://github.com/23maverick23/jekyll-humanize): This is a port of the Django app humanize which adds a “human touch” to data. Each method represents a Fluid type filter that can be used in your Jekyll site templates. Given that Jekyll produces static sites, some of the original methods do not make logical sense to port (e.g. naturaltime).
- [Jekyll-Ordinal](https://github.com/PatrickC8t/Jekyll-Ordinal): Jekyll liquid filter to output a date ordinal such as “st”, “nd”, “rd”, or “th”.
- [Deprecated articles keeper](https://github.com/kzykbys/JekyllPlugins) by [Kazuya Kobayashi](http://blog.kazuya.co/): A simple Jekyll filter which monitor how old an article is.
- [Jekyll-jalali](https://github.com/mehdisadeghi/jekyll-jalali) by [Mehdi Sadeghi](http://mehdix.ir/): A simple Gregorian to Jalali date converter filter.
- [Jekyll Thumbnail Filter](https://github.com/matallo/jekyll-thumbnail-filter): Related posts thumbnail filter.
- [liquid-md5](https://github.com/pathawks/liquid-md5): Returns an MD5 hash. Helpful for generating Gravatars in templates.
- [jekyll-roman](https://github.com/paulrobertlloyd/jekyll-roman): A liquid filter for Jekyll that converts numbers into Roman numerals.
- [jekyll-typogrify](https://github.com/myles/jekyll-typogrify): A Jekyll plugin that brings the functions of [typogruby](http://avdgaag.github.io/typogruby/).
- [Jekyll Email Protect](https://github.com/vwochnik/jekyll-email-protect): Email protection liquid filter for Jekyll
- [Jekyll Uglify Filter](https://github.com/mattg/jekyll-uglify-filter): A Liquid filter that runs your JavaScript through UglifyJS.
- [match_regex](https://github.com/sparanoid/match_regex): A Liquid filter to perform regex match.
- [replace_regex](https://github.com/sparanoid/replace_regex): A Liquid filter to perform regex replace.
- [Jekyll Money](https://rubygems.org/gems/jekyll-money): A Jekyll plugin for dealing with money. Because we all have to at some point.



4) Tag

- [Jekyll-gist](https://github.com/jekyll/jekyll-gist): Use the `gist` tag to easily embed a GitHub Gist onto your site. This works with public or secret gists.
- [Asset Path Tag](https://github.com/samrayner/jekyll-asset-path-plugin) by [Sam Rayner](http://www.samrayner.com/): Allows organisation of assets into subdirectories by outputting a path for a given file relative to the current post or page.
- [Tag Cloud Plugin by Ilkka Laukkanen](https://gist.github.com/710577): Generate a tag cloud that links to tag pages.
- [GIT Tag by Alexandre Girard](https://gist.github.com/730347): Add Git activity inside a list.
- [Render Time Tag by Blake Smith](https://gist.github.com/449509): Displays the time a Jekyll page was generated.
- [Status.net/OStatus Tag by phaer](https://gist.github.com/912466): Displays the notices in a given status.net/ostatus feed.
- [Embed.ly client by Robert Böhnke](https://github.com/robb/jekyll-embedly-client): Autogenerate embeds from URLs using oEmbed.
- [Logarithmic Tag Cloud](https://gist.github.com/2290195): Flexible. Logarithmic distribution. Documentation inline.
- [oEmbed Tag by Tammo van Lessen](https://gist.github.com/1455726): Enables easy content embedding (e.g. from YouTube, Flickr, Slideshare) via oEmbed.
- [Jekyll Twitter Plugin](https://github.com/rob-murray/jekyll-twitter-plugin): A Liquid tag plugin that renders Tweets from Twitter API. Currently supports the [oEmbed](https://dev.twitter.com/rest/reference/get/statuses/oembed) API.
- [Jekyll-contentblocks](https://github.com/rustygeldmacher/jekyll-contentblocks): Lets you use Rails-like content_for tags in your templates, for passing content from your posts up to your layouts.
- [Generate YouTube Embed](https://gist.github.com/1805814) by [joelverhagen](https://github.com/joelverhagen): Jekyll plugin which allows you to embed a YouTube video in your page with the YouTube ID. Optionally specify width and height dimensions. Like “oEmbed Tag” but just for YouTube.
- [Jekyll-beastiepress](https://github.com/okeeblow/jekyll-beastiepress): FreeBSD utility tags for Jekyll sites.
- [Jsonball](https://gist.github.com/1895282): Reads json files and produces maps for use in Jekyll files.
- [Bibjekyll](https://github.com/pablooliveira/bibjekyll): Render BibTeX-formatted bibliographies/citations included in posts and pages using bibtex2html.
- [Jekyll-citation](https://github.com/archome/jekyll-citation): Render BibTeX-formatted bibliographies/citations included in posts and pages (pure Ruby).
- [JekyllGalleryTag](https://github.com/redwallhp/JekyllGalleryTag) by [redwallhp](https://github.com/redwallhp): Generates thumbnails from a directory of images and displays them in a grid.
- [Jekyll-swfobject](https://github.com/sectore/jekyll-swfobject): Liquid plugin for embedding Adobe Flash files (.swf) using [SWFObject](https://github.com/swfobject/swfobject).
- [Jekyll Picture Tag](https://github.com/robwierzbowski/jekyll-picture-tag): Easy responsive images for Jekyll. Based on the proposed [``](https://html.spec.whatwg.org/multipage/embedded-content.html#the-picture-element) element, polyfilled with Scott Jehl’s [Picturefill](https://github.com/scottjehl/picturefill).
- [Jekyll Image Tag](https://github.com/robwierzbowski/jekyll-image-tag): Better images for Jekyll. Save image presets, generate resized images, and add classes, alt text, and other attributes.
- [Jekyll Responsive Image](https://github.com/wildlyinaccurate/jekyll-responsive-image): Responsive images for Jekyll. Automatically resizes images, supports all responsive methods (`<picture>`, `srcset`, Imager.js, etc), super-flexible configuration.
- [Ditaa Tag](https://github.com/matze/jekyll-ditaa) by [matze](https://github.com/matze): Renders ASCII diagram art into PNG images and inserts a figure tag.
- [Jekyll Suggested Tweet](https://github.com/davidensinger/jekyll-suggested-tweet) by [David Ensinger](https://github.com/davidensinger/): A Liquid tag for Jekyll that allows for the embedding of suggested tweets via Twitter’s Web Intents API.
- [Jekyll Date Chart](https://github.com/GSI/jekyll_date_chart) by [GSI](https://github.com/GSI): Block that renders date line charts based on textile-formatted tables.
- [Jekyll Image Encode](https://github.com/GSI/jekyll_image_encode) by [GSI](https://github.com/GSI): Tag that renders base64 codes of images fetched from the web.
- ✔ [jekyll-font-awesome](https://gist.github.com/23maverick23/8532525): Quickly and easily add Font Awesome icons to your posts.
- [Lychee Gallery Tag](https://gist.github.com/tobru/9171700) by [tobru](https://github.com/tobru): Include [Lychee](https://lychee.electerious.com/) albums into a post. For an introduction, see [Jekyll meets Lychee - A Liquid Tag plugin](https://tobrunet.ch/articles/jekyll-meets-lychee-a-liquid-tag-plugin/)
- [Image Set/Gallery Tag](https://github.com/callmeed/jekyll-image-set) by [callmeed](https://github.com/callmeed): Renders HTML for an image gallery from a folder in your Jekyll site. Just pass it a folder name and class/tag options.
- [jekyll_figure](https://github.com/lmullen/jekyll_figure): Generate figures and captions with links to the figure in a variety of formats
- [Jekyll GitHub Sample Tag](https://github.com/bwillis/jekyll-github-sample): A liquid tag to include a sample of a github repo file in your Jekyll site.
- [Jekyll Project Version Tag](https://github.com/rob-murray/jekyll-version-plugin): A Liquid tag plugin that renders a version identifier for your Jekyll site sourced from the git repository containing your code.
- [mathml.rb](https://github.com/tmthrgd/jekyll-plugins) by Tom Thorogood: A plugin to convert TeX mathematics into MathML for display.
- [webmention_io.rb](https://github.com/aarongustafson/jekyll-webmention_io) by [Aaron Gustafson](http://aaron-gustafson.com/): A plugin to enable [webmention](https://indieweb.org/webmention) integration using [Webmention.io](https://webmention.io/). Includes an optional JavaScript for updating webmentions automatically between publishes and, if available, in realtime using WebSockets.
- [Jekyll 500px Embed](https://github.com/lkorth/jekyll-500px-embed) by Luke Korth. A Liquid tag plugin that embeds [500px](https://500px.com/) photos.
- [inline_highlight](https://github.com/bdesham/inline_highlight): A tag for inline syntax highlighting.
- [jekyll-mermaid](https://github.com/jasonbellamy/jekyll-mermaid): Simplify the creation of mermaid diagrams and flowcharts in your posts and pages.
- [twa](https://github.com/Ezmyrelda/twa): Twemoji Awesome plugin for Jekyll. Liquid tag allowing you to use twitter emoji in your jekyll pages.
- [jekyll-asciinema](https://github.com/mnuessler/jekyll-asciinema): A tag for embedding asciicasts recorded with [asciinema](https://asciinema.org/) in your Jekyll pages.
- ✔ [Jekyll-Youtube](https://github.com/dommmel/jekyll-youtube) A Liquid tag that embeds Youtube videos. The default emded markup is responsive but you can also specify your own by using an include/partial.
- [Jekyll Flickr Plugin](https://github.com/lawmurray/indii-jekyll-flickr) by [Lawrence Murray](http://www.indii.org/): Embeds Flickr photosets (albums) as a gallery of thumbnails, with lightbox links to larger images.
- [jekyll-figure](https://github.com/paulrobertlloyd/jekyll-figure): A liquid tag for Jekyll that generates `<figure>`elements.
- ✔ [Jekyll Video Embed](https://github.com/eug/jekyll-video-embed): It provides several tags to easily embed videos (e.g. Youtube, Vimeo, UStream and Ted Talks)
- ✔ [jekyll-i18n_tags](https://github.com/KrzysiekJ/jekyll-i18n_tags): Translate your templates.
- [Jekyll Ideal Image Slider](https://github.com/jekylltools/jekyll-ideal-image-slider): Liquid tag plugin to create image sliders using [Ideal Image Slider](https://github.com/gilbitron/Ideal-Image-Slider).
- [Jekyll Tags List Plugin](https://github.com/crispgm/jekyll-tags-list-plugin): A Liquid tag plugin that creates tags list in specific order.
- ✔ [Jekyll Maps](https://github.com/ayastreb/jekyll-maps) by [Anatoliy Yastreb](https://github.com/ayastreb): A Jekyll plugin to easily embed maps with filterable locations.
- [Jekyll Cloudinary](https://nhoizey.github.io/jekyll-cloudinary/) by [Nicolas Hoizey](https://nicolas-hoizey.com/): a Jekyll plugin adding a Liquid tag to ease the use of Cloudinary for responsive images in your Markdown/Kramdown posts.
- [jekyll-include-absolute-plugin](https://github.com/tnhu/jekyll-include-absolute-plugin) by [Tan Nhu](https://github.com/tnhu): A Jekyll plugin to include a file from its path relative to Jekyll’s source folder.
- ✔ [Jekyll Download Tag](https://github.com/mattg/jekyll-download-tag): A Liquid tag that acts like `include`, but for external resources.
- [Jekyll Brand Social Wall](https://github.com/MediaComem/jekyll-brand-social-wall): A jekyll plugin to generate a social wall with your favorite social networks
- [Jekyll If File Exists](https://github.com/k-funk/jekyll-if-file-exists): A Jekyll Plugin that checks if a file exists with an if/else block.
- [github-cards](https://github.com/edward-shen/github-cards): Creates styleable Github cards for your Github projects.
- ✔ [disqus-for-jekyll](https://github.com/kacperduras/disqus-for-jekyll): A Jekyll plugin to view the comments powered by Disqus.



5) Other

- [Analytics for Jekyll](https://github.com/hendrikschneider/jekyll-analytics) by Hendrik Schneider: An effortless way to add various trackers like Google Analytics, Piwik, etc. to your site
- [ditaa-ditaa](https://github.com/tmthrgd/ditaa-ditaa) by Tom Thorogood: a drastic revision of jekyll-ditaa that renders diagrams drawn using ASCII art into PNG images.
- [Pygments Cache Path by Raimonds Simanovskis](https://github.com/rsim/blog.rayapps.com/blob/master/_plugins/pygments_cache_patch.rb): Plugin to cache syntax-highlighted code from Pygments.
- [Draft/Publish Plugin by Michael Ivey](https://gist.github.com/49630): Save posts as drafts.
- [Growl Notification Generator by Tate Johnson](https://gist.github.com/490101): Send Jekyll notifications to Growl.
- [Growl Notification Hook by Tate Johnson](https://gist.github.com/525267): Better alternative to the above, but requires his “hook” fork.
- [Related Posts by Lawrence Woodman](https://github.com/LawrenceWoodman/related_posts-jekyll_plugin): Overrides `site.related_posts` to use categories to assess relationship.
- [jekyll-tagging-related_posts](https://github.com/toshimaru/jekyll-tagging-related_posts): Jekyll related_posts function based on tags (works on Jekyll3).
- [Tiered Archives by Eli Naeher](https://gist.github.com/88cda643aa7e3b0ca1e5): Create tiered template variable that allows you to group archives by year and month.
- [Jekyll-localization](https://github.com/blackwinter/jekyll-localization): Jekyll plugin that adds localization features to the rendering engine.
- [Jekyll-rendering](https://github.com/blackwinter/jekyll-rendering): Jekyll plugin to provide alternative rendering engines.
- [Jekyll-pagination](https://github.com/blackwinter/jekyll-pagination): Jekyll plugin to extend the pagination generator.
- [Jekyll-tagging](https://github.com/pattex/jekyll-tagging): Jekyll plugin to automatically generate a tag cloud and tag pages.
- [Jekyll-scholar](https://github.com/inukshuk/jekyll-scholar): Jekyll extensions for the blogging scholar.
- [Jekyll-assets](http://jekyll.github.io/jekyll-assets/) by [ixti](https://github.com/ixti): Rails-alike assets pipeline (write assets in CoffeeScript, Sass, LESS etc; specify dependencies for automatic bundling using simple declarative comments in assets; minify and compress; use JST templates; cache bust; and many-many more).
- [JAPR](https://github.com/kitsched/japr): Jekyll Asset Pipeline Reborn - Powerful asset pipeline for Jekyll that collects, converts and compresses JavaScript and CSS assets.
- [File compressor](https://gist.github.com/2758691) by [mytharcher](https://github.com/mytharcher): Compress HTML and JavaScript files on site build.
- [Singlepage-jekyll](https://github.com/JCB-K/singlepage-jekyll) by [JCB-K](https://github.com/JCB-K): Turns Jekyll into a dynamic one-page website.
- [generator-jekyllrb](https://github.com/robwierzbowski/generator-jekyllrb): A generator that wraps Jekyll in [Yeoman](http://yeoman.io/), a tool collection and workflow for building modern web apps.
- [grunt-jekyll](https://github.com/dannygarcia/grunt-jekyll): A straightforward [Grunt](http://gruntjs.com/) plugin for Jekyll.
- [jekyll-postfiles](https://github.com/indirect/jekyll-postfiles): Add `_postfiles` directory and `{{ postfile }}` tag so the files a post refers to will always be right there inside your repo.
- [A layout that compresses HTML](http://jch.penibelst.de/): GitHub Pages compatible, configurable way to compress HTML files on site build.
- [remote-include](http://www.northfieldx.co.uk/remote-include/): Includes files using remote URLs
- [jekyll-minifier](https://github.com/digitalsparky/jekyll-minifier): Minifies HTML, XML, CSS, and Javascript both inline and as separate files utilising yui-compressor and htmlcompressor.
- [Jekyll views router](https://bitbucket.org/nyufac/jekyll-views-router): Simple router between generator plugins and templates.
- [Jekyll Language Plugin](https://github.com/vwochnik/jekyll-language-plugin): Jekyll 3.0-compatible multi-language plugin for posts, pages and includes.
- [Jekyll Deploy](https://github.com/vwochnik/jekyll-deploy): Adds a `deploy` sub-command to Jekyll.
- [Official Contentful Jekyll Plugin](https://github.com/contentful/jekyll-contentful-data-import): Adds a `contentful` sub-command to Jekyll to import data from Contentful.
- [Hawkins](https://github.com/awood/hawkins): Adds a `liveserve` sub-command to Jekyll that incorporates [LiveReload](http://livereload.com/) into your pages while you preview them. No more hitting the refresh button in your browser!
- [Jekyll Autoprefixer](https://github.com/vwochnik/jekyll-autoprefixer): Autoprefixer integration for Jekyll
- [Jekyll-breadcrumbs](https://github.com/git-no/jekyll-breadcrumbs): Creates breadcrumbs for Jekyll 3.x, includes features like SEO optimization, optional breadcrumb item translation and more.
- [generator-jekyllized](https://github.com/sondr3/generator-jekyllized): A Yeoman generator for rapidly developing sites with Gulp. Live reload your site, automatically minify and optimize your assets and much more.
- [jekyll-menus](https://github.com/forestryio/jekyll-menus): Hugo style menus for your Jekyll site… recursive menus included.
- [jekyll-data](https://github.com/ashmaroli/jekyll-data): Read data files within Jekyll Theme Gems.
- [jekyll-migrate-permalink](https://github.com/mpchadwick/jekyll-migrate-permalink): Adds a `migrate-permalink` sub-command to help deal with side effects of changing your permalink.
- [Jekyll-Post](https://github.com/robcrocombe/jekyll-post): A CLI tool to easily draft, edit, and publish Jekyll posts.
- [jekyll-numbered-headings](https://github.com/muratayusuke/jekyll-numbered-headings): Adds ordered number to headings.
- [jekyll-pre-commit](https://github.com/mpchadwick/jekyll-pre-commit): A framework for running checks against your posts using a git pre-commit hook before you publish them.


# Themes
지킬 테마는 
_layouts/, _includes/ 및 stylesheet를 사이트의 콘텐츠로 덮어 쓸 수있는 방법으로 패키지합니다.

## 1) gem 기반의 테마에 대한 이해
gem 기반 테마를 사용하면 
사이트의 디렉토리 중 일부 (예 : assets, _layouts, _includes 및 _sass 디렉토리)가 테마의 gem에 저장되어 즉석에서 숨길 수 있습니다. 
그러나 필요한 모든 디렉토리는 지킬의 빌드 과정에서 읽고 처리됩니다. 

Minima의 경우 지킬 사이트 디렉토리에 다음 파일만 표시됩니다.

```bash
├── Gemfile
├── Gemfile.lock
├── _config.yml
├── _posts
│          └── 2016-12-04-welcome-to-jekyll.markdown
├── about.md
└── index.md
```
Gemfile 및 Gemfile.lock 파일은 Bundler에서 지킬 사이트를 구축하는 데 필요한 gem 및 gem 버전을 추적하는 데 사용됩니다.

Gem 기반 테마를 사용하면 테마 개발자가 테마 Gem을 가진 모든 사용자가 업데이트를 쉽게 사용할 수 있습니다. 
업데이트가있을 때, 테마 개발자는 업데이트를 RubyGems로 푸시합니다.

테마 gem을 가지고 있다면 (원한다면) `bundle update`를 실행하여 프로젝트의 모든 gem을 업데이트 할 수 있습니다. 
또는 `bundle update <THEME>`를 실행하여 `<THEME>`를 테마 이름 (예 : `minima`)으로 바꾸면 테마 젬을 업데이트 할 수 있습니다. 
테마 개발자가 만든 새로운 파일이나 업데이트 (예 : 스타일 시트 또는 포함)는 프로젝트에 자동으로 가져옵니다.

## 2) 테마 재정의  

지킬 테마는 기본 `_layout`, `_include` 및 stylesheets를 설정합니다. 
그러나 테마 기본값을 사용자 자신의 사이트 컨텐츠로 대체 할 수 있습니다.

레이아웃을 바꾸거나 테마에 포함 시키려면 
수정하려는 특정 파일의 `_layouts/` 또는 `_includes/` 디렉토리에 
복사본을 만들거나 덮어 쓸 파일과 동일한 이름을 가진 파일을 처음부터 만듭니다.

예를 들어 선택한 테마에 페이지 레이아웃이있는 경우 
`_layouts/` 디렉토리 (즉, `_layouts / page.html`)에 자체 페이지 레이아웃을 만들어 테마 레이아웃을 재정의 할 수 있습니다.

➀ 테마의 path알아내기 
```bash
➜ bundle show jekyll-theme-simple-texture
/home/learn/.rbenv/versions/2.4.1/lib/ruby/gems/2.4.0/gems/jekyll-theme-simple-texture-0.2.2
```

❷ 테마의 path로 들어가서 내장 파일 검색하기 
```bash
cd /home/learn/.rbenv/versions/2.4.1/lib/ruby/gems/2.4.0/gems/jekyll-theme-simple-texture-0.2.2
tree -L 2
.
├── LICENSE
├── README.md
├── _includes
│   ├── blog
│   ├── common
│   ├── helpers
│   └── home
├── _layouts
│   ├── blog.html
│   ├── home.html
│   ├── page.html
│   ├── post.html
│   └── redirect.html
├── _sass
│   ├── simple-texture
│   ├── simple-texture-blog.scss
│   └── simple-texture-home.scss
└── assets
    ├── fonts
    ├── images
    ├── javascripts
    └── stylesheets
```
재정의는 자신의 지킬 사이트에서 할 수 있다. 

## 3) gem 기반의 테마를 일반 테마로 바꾸기 
**gem 기반 테마를 없애고** 
테마 파일에 아무것도 저장하지 않고 
모든 파일이 지킬 사이트 디렉토리에있는 일반 테마로 변환하려고한다고 가정합니다.

➀ gem 테마에 있는 파일들을 jekyll site 폴더로 옮깁니다. 

❷ jekyll site의 `Gemfile`를 열고,  `gem 'jekyll-theme-simple-texture'`를 삭제하기

➂ `_config.yml`을 열고, `theme: jekyll-theme-simple-texture` 를 삭제하기 

이제 더 이상 `bundle update`를 할 필요 없다. 


## 4) gem 기반 테마를 설치하기 

➀ `jekyll new`실행하지 않았다면,  Gemfile에 테마를 추가한다. 


```bash
# ./Gemfile

gem "jekyll-theme-simple-texture"
```

❷ `jekyll new`를 실행하였다면, `gem "minima"`를 삭제하고 자신의 테마를 추가한다.

```bash
# ./Gemfile

- gem "minima", "~> 2.0"
+ gem "jekyll-theme-simple-texture"
```

➂ 테마를 설치한다. 
```bash
bundle install 
```

➃ `_config.yml`에 자신의 테마를 활성화 시킵니다. 
```bash
theme: jekyll-theme-simple-texture
```

➄ 로컬에서 jekyll site를 실행시키기 
```bash
bundle exec jekyll serve
```

## 5) 테마 만들기 
공식 사이트 참조 







# _drafts/ (초안)

`_drafts` 폴더 만들어서  초안 파일을 넣기

```sh
|-- _drafts/
|      |-- a-draft-post.md
```

초안 미리보기 

```sh
jekyll build --drafts
```


# Assets
공식 사이트 참조 
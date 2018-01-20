---
layout: post
title: "jekyll-simple-texture 설치하여 사용하기"
description: "jekyll-simple-texture 설치하여 사용하기"
categories: [jekyll]
tags: [jekyll]
redirect_from:
  - /2018/01/01/
---

* Kramdown table of contents
{:toc .toc}

# 참조 
> [simple texture](http://themes.jekyllrc.org/simple-texture/)

> [jekyll 테마들](http://jekyllthemes.org/)

> [GitHub Pages 테마들](https://pages.github.com/themes/)

---

# 테마 소개 : simple texture

제가 운영하는 이 블로그는 jekyll 정적 블로그 생성기에서 [simple texture](http://themes.jekyllrc.org/simple-texture/)를 사용하였습니다. 

---

# 설치 과정의 문제점 
simple texture는 [GitHub Pages 테마들](https://pages.github.com/themes/)에 등록된 것이 아니므로
`$ bundle install`을 통하여 github에 그대로 올릴 수 없습니다. 

내 컴퓨터(local)는 `$ bundle exec jekyll serve`를 실행하여 web page를 생성할 때 **_site/**를 참조하지만 
**github pages**는 **_post/, _layouts/, _sass/**를 직접 이용하기 때문입니다. 

`$ jekyll new <블로그 사이트 폴더 이름>`로 블로그를 생성하면 jekyll은 기본적으로 **minima theme**을 설치하는데 
위 테마는 [GitHub Pages 테마들](https://pages.github.com/themes/)이 지원하는 것들 중 하나입니다.  

이 사이트는 **github pages**가 simple-texture를 직접 이용할 수 있도록 
* **minima**를 기본으로 simple-texture를 덧붙였습니다. 
* **simple-texture/starter-kit/**와 
* **/gems/jekyll-theme-simple-texture-0.2.2/**의 모든 파일과 DIR을
 
`<블로그 사이트 폴더>`에 직접  넣어서 만들었습니다.  

```ini
start-kit/
    ├── Gemfile
    ├── _config.yml
    ├── _data
    │     └── i18n.yml
    ├── _posts
    │     ├── 2013-04-22-hello-world.md
    │     ├── 2017-05-25-quick-kramdown-demo.md
    │     └── 2017-05-27-code-highlighting.md
    ├── blog
    │     ├── categories/
    │     ├── tags/
    │     └── index.html
    ├── index.html
    └── search.json
``` 

```ini 
/gems/jekyll-theme-simple-texture-0.2.2/
    ├── LICENSE
    ├── README.md
    ├── _includes
    │       ├── blog
    │       ├── common
    │       ├── helpers
    │       └── home
    ├── _layouts
    │       ├── blog.html
    │       ├── home.html
    │       ├── page.html
    │       ├── post.html
    │       └── redirect.html
    ├── _sass
    │       ├── simple-texture
    │       ├── simple-texture-blog.scss
    │       └── simple-texture-home.scss
    └── assets
            ├── fonts
            ├── images
            ├── javascripts
            └── stylesheets
```




---

# 이 사이트의 빌드 과정

위 테마의 설치과정은 아래와 같습니다. 
참고로 설치환경은 Ubuntu16.04LTS입니다. Mac사용자는 조금 다르게 설치하셔야 합니다. 


## 1) 기본 설치 - rbenv, ruby, rehash, bundler 및 github-pages

(1) rbenv

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

(2) Ruby 설치 

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

(3) rehash 작업 

rehash는 rbenv가 관리하는 루비 명령어들을  `~/.rbenv/shims`에 셀스크립트파일로 복사해주는 과정이다. 

➀ 터미널을 새로 실행(`rbenv init -`)하거나  ➁ 기존 터미널에서 아래 명령어를 실행해준다. 

```sh
➜ rbenv rehash
```

(4) bundler,  github-pages 설치 

* Gem이 시스템 전역으로 패키지를 관리한다면 bundle은 **현재 프로젝트 폴더**에서 사용할 패키지를  `Gemfile`과  `Gemfile.lock`을 통하여 관리한다. 
* github-pages는 GitHub에서 사용할 Jekyll관련 의존성 패키지들을 관리하는 Gem이다. 

```sh
➜ gem install jekyll bundler github-pages
```
* 확인 
```bash
➜ gem list
*** LOCAL GEMS ***

activesupport (4.2.9, 4.2.8)
addressable (2.5.2)
bigdecimal (default: 1.3.0)
bundler (1.16.0, 1.15.4)
coffee-script (2.4.1)
coffee-script-source (1.11.1)
colorator (1.1.0)
commonmarker (0.17.7.1, 0.17.7)
concurrent-ruby (1.0.5)
did_you_mean (1.1.0)
ethon (0.11.0, 0.10.1)
execjs (2.7.0)
faraday (0.13.1)
ffi (1.9.18)
forwardable-extended (2.6.0)
gemoji (3.0.0)
github-pages (172, 160)
github-pages-health-check (1.3.5)
html-pipeline (2.7.1, 2.7.0)
i18n (0.9.1, 0.8.6)
```


## 2) (local 환경) jekyll 블로그 생성

```sh
➜ jekyll new .

Running bundle install in /home/learn/projects/MyBlog... 
  Bundler: To update, run `gem install bundler`
  Bundler: To update, run `gem install bundler`

MyBlog 폴더의 구조 
   ├─ 404.html
   ├─ Gemfile
   ├─ Gemfile.lock
   ├─ _config.yml
   ├─ _posts
   │    └─ 2017-12-08-welcome-to-jekyll.markdown
   ├─ about.md
   └─ index.md

➜ cd MyBlog
# bundle이 관리하는 현재 폴더 내의 패키지 설치 및 업데이트 
➜ MyBlog  bundle install
➜ MyBlog  bundle update
```

그리고 위 폴더의 모든 파일들을 삭제한다.  
```bash
MyBlog 폴더/  
  (빈 폴더 )
```


## 3) `starter-kit/` 다운로드 + `bundle install`

(1) starter-kit 옮기기
 
https://github.com/yizeng/jekyll-theme-simple-texture에서 starter-kit를 download하여 
`starter-kit/`의 모든 파일들을 root directory(여기 `MyBlog/`)에 옮긴다.  

```
MyBlog
 ├── Gemfile
 ├── _config.yml
 ├── _data
 │    └── i18n.yml
 ├── _posts
 │    └── 2013-04-22-hello-world.md
 ├── blog
 │    ├── categories/
 │    ├── tags/
 │    └── index.html
 ├── index.html
 └── search.json
```

(2) bundle install 실행 

```bash
➜ MyBlog  bundle install
```

## 4) (local) Run Jekyll
```bash
# jekyll 정적 사이트를  내 컴퓨터(local)에서 실행한다.
➜ MyBlog  bundle exec jekyll serve
# localhost:4000 접속 
# _site 폴더가 생성됨:: 정적 사이트 생성 결과물 

# Site 2
➜ MyBlog  bundle exec jekyll serve --host myhost --port 1234
# => "http://myhost:1234"
```

---

# github에 올리기

GitHub는 Jekyll 실행 결과(`_site`) 대신  **Jekyll 소스(`_posts/` 문서파일)** 를 직접 이용합니다. 
GitHub에서는 `.../gems/jekyll-theme-simple-texture-0.2.2`를 인식하지 못하므로
 local 환경과 다르게 설정해야 한다.  
 

## 1) `starter-kit/` + `gem/` ➟ `MyBlog/` 
`starter-kit/`과 `/gems/jekyll-theme-simple-texture-0.2.2/`의 모든 파일들을 root directory(여기 `MyBlog/`)에 옮긴다. 

```bash
➜ bundle show jekyll-theme-simple-texture
# 또는 
➜ bundle info jekyll-theme-simple-texture

/path-to/gems/jekyll-theme-simple-texture-0.2.2/
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

## 2) GitHub 저장소 만들기 
 ➨ `<자신의 유저명>.github.io`형식 준수

## 3) `$ git init`

## 4) `.gitignore` 
-  Ruby, Jekyll, Ubuntu, pycharm, 

## 5) GitHub 저장소에 올리기 

```sh
# 로컬 저장소 생성 
➜ git init
# gitignore대상은 gitignore.io를 사용한다.  
## 버전관리제외 대상은 Ruby, Linux, Jekyll이다. 
➜ vi .gitignore 
➜ git add -A
➜ git commit -m 'First commit'
➜ git remote add origin git@github.com:ehfgk78.github.io.git
➜ git push origin master
```

---

# 나에게 맞는 블로그 만들기 

## 1) _layouts/blog.html
이 파일을 기준으로 `page.html`, `post.html`등이 만들어진다. 

{% raw %}
```html
<!DOCTYPE html>

<html class="no-js" lang="{{ site.lang }}">
<head>
  {% include common/meta.html %}
  <link href='{{ site.baseurl }}/assets/stylesheets/blog.css' rel="stylesheet" type="text/css">
  {% include common/libraries.html %}
</head>

<body>
  {% include common/notices.html %}
  {% include blog/overlay.html %}
  <div id="page" class="hentry">
   {% include mypages/header.html %}
    <div class="body animated fadeInDown" role="main">
        <div class="unit-body">
            <div class="unit-inner unit-body-inner">
                <div class="entry-content">
                    {{ content }}
                </div>
            </div>
        </div>
    </div>
    {% include mypages/footer.html %}
    </div>

    {% include blog/scripts.html %}
</body>
</html>
```
{% endraw %}

* CSS 파일은 `/assets/stylesheets/blog.css`을 참조하는데, 이에 대응하는 `/assets/stylesheets/blog.scss`를 살펴보자. 
{% raw %}
```bash
---
---
@import 'simple-texture-blog';
```
{% endraw %}


## 2) CSS 문서 
이에 따라 `_sass/simple-texture-blog.scss`를 보자.
`_includes/`가 뒷받침해준다. 

{% raw %}
```scss
//@import 'simple-texture/blog/variables';
@import 'myStyle/variables';

@import 'simple-texture/common/normalize';
@import 'simple-texture/common/animate';
@import 'simple-texture/common/fonts';
@import 'simple-texture/common/helper';
@import 'simple-texture/common/highlight'; // ✓ 여기 코드 하이라이팅 
@import 'simple-texture/common/pace';

html {
    background: url('../images/theme/noisy_texture_section0-200x200.png') repeat 0 0 #F9F9F9;
}

body {
    font-family: $fonts-body;
    font-size: 18px;
    line-height: 32px;

    color: $color-text;
}

.clearfix::after {
    content: "";
    clear: both;
    display: table;
}

@import 'simple-texture/blog/overlay';
//@import 'simple-texture/blog/header';
@import 'myStyle/header';
//@import 'simple-texture/blog/footer';
@import 'myStyle/footer';

//@import 'simple-texture/blog/content';  // ✓ 마크다운 기본 설정 
@import 'myStyle/content';
@import 'simple-texture/blog/responsive';
```
{% endraw %} 

## 3) 변수들 : `_data/`, `_config.yml`

* [https://jekyllrb.com/docs/plugins/](https://jekyllrb.com/docs/plugins/)

`_config.yml` 파일에 gems 라는 키로 배열을 추가하고 사용하려는 플러그인들의 gem 이름을 나열하세요. 
예를 들면 다음과 같습니다:

```bash
# _config.yml
gems: [jekyll-test-plugin, jekyll-jsonify, jekyll-assets]
# This will require each of these gems automatically.
```

다음, 
```bash
$ gem install jekyll-test-plugin jekyll-jsonify jekyll-assets 
```
라고 실행하여 해당 플러그인들을 설치합니다.


## 4) Gemfile 
* [https://jekyllrb.com/docs/plugins/](https://jekyllrb.com/docs/plugins/)

Gemfile은 Bundler 그룹에 관련 플러그인을 추가합니다. 즉 `bundle install` 명령만 한 번 실행해주면 Bundler 그룹에 있는 모든 플러그인이 설치됩니다.

```bash
source "https://rubygems.org"

#############################################################################
# Hello! This is where you manage which Jekyll version is used to run.
# 안녕하세요,  여기는 당신이 실행할 Jekyll의 버전을 관리하는 곳입니다.
#
# When you want to use a different version, change it below, save the
# 당신이 다른 버전을 원한다면,
#  ❶ 아래의 명령들을 바꾸고
#  ❷ 파일을 저장하고
# file and run `bundle install`.
#  ❸ `bundle install`을 실행해야 합니다.
#
# Run Jekyll with `bundle exec`, like so:
# 그리고 ❹ 다음과 같이 Jekyll을 실행해야 합니다.
#
#     bundle exec jekyll serve#
#############################################################################

gem "jekyll", "~> 3.6.2"

#######################################################################
# If you want to use GitHub Pages, remove the "gem "jekyll"" above and
#  만약 당신이 GitHub Pages를 운영하고 싶다면,
#  ➀ 위 "gem "jekyll""를 지우세요.
#
# uncomment the line below.
#  ➁ 아래 줄의 주석 처리를 제거하여 실행할 수 있게 해야 합니다.
#
# gem "github-pages", group: :jekyll_plugins
#
# To upgrade, run `bundle update github-pages`.
# 업그레이드를 하려면 `bundle update github-pages`를 해야 합니다.
###########################################################


#default theme for new Jekyll sites.
#You may change this to anything you like.
#gem "minima", "~> 2.0"
#gem "jekyll-theme-simple-texture",

# plugins, put them here!
group :jekyll_plugins do
  gem 'jekyll-feed'
  gem 'jekyll-redirect-from'
  gem 'jekyll-seo-tag'
  gem 'jekyll-sitemap'
end
```

`$ gem "jekyll-theme-simple-texture"` 등을 삭제하거나 주석처리한다. 
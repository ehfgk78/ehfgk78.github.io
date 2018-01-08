---
layout: post
title:  "Jekyll 간단 사용법"
date:   2017-12-20 22:09:13 +0900
categories: jekyll
tags: [jekyll, Kramdown]
---

* Kramdown table of contents
{:toc .toc}


# 전체 보기 
You’ll find this post in **your `_posts` directory**. Go ahead and edit it and re-build the site to see your changes. You can rebuild the site in many different ways, but the most common way is to run `jekyll serve`, which launches a web server and auto-regenerates your site when a file is updated.

이 게시물은 `_posts` 디렉토리에서 찾을 수 있습니다. 사이트를 수정하고 사이트를 다시 빌드하면 변경 사항을 확인할 수 있습니다. 여러 가지 방법으로 사이트를 재구성 할 수 있지만 가장 일반적인 방법은 웹 서버를 시작하고 파일이 업데이트 될 때 사이트를 자동으로 재생성하는 `jekyll serve` 를 실행하는 것입니다.

**To add new posts**, simply add a file in the `_posts` directory that follows **➀ the  convention `YYYY-MM-DD-name-of-post.ext`**  and includes **➁ the necessary front matter**. Take a look at the source for this post to get an idea about how it works.

**새 게시물을 추가** 하려면, `_posts` 디렉토리에 **➀ YYYY-MM-DD-name-of-post.ext 규칙**을 따르는 파일을 추가하고 **➁필요한 앞 부분(YAML 머리말)**을 포함하십시오. 이 게시물의 출처를보고 어떻게 작동하는지에 대한 아이디어를 얻으십시오.  **➂ 글 목록( Kramdown에서 제공하는 기능 )**을 자동으로 제공해주는 기능이 눈여겨볼 만 하다. 



# YAML 머리말

```sh
---
layout: post
title:  "Jekyll 사용법"
date:   2017-12-20 22:09:13 +0900
categories: jekyll
tags: jekyll
---
```



# 글 목록 보기  

```sh
* Kramdown table of contents
{:toc .toc}
```


# 구문 강조 
Jekyll also offers powerful support for **code snippets(구문 강조)**:

{% highlight ruby %}
# ruby
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
{% endhighlight %}

{% highlight python %}
# python
def say_hello():
    print("Hello, world")
{% endhighlight %}

Check out the [Jekyll docs][jekyll-docs] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll’s GitHub repo][jekyll-gh]. If you have questions, you can ask them on [Jekyll Talk][jekyll-talk].

[jekyll-docs]: https://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/

---
layout: post
title: "jekyll에서 jekyll 문법을 표현하기 - escape liquid template"
description: "jekyll에서 jekyll 문법을 표현하기 - escape liquid template"
categories: [jekyll]
tags: [jekyll]
redirect_from:
  - /2018/01/02/
---

# 참고
[How to escape liquid template tags? - Stack Overflow](https://stackoverflow.com/questions/3426182/how-to-escape-liquid-template-tags)

[Raw - Liquid template language](https://shopify.github.io/liquid/tags/raw/)


# raw 태그 사용 
Jekyll로 글을 쓰다 보면 Liquid 문법을 표현하고 싶은 경우가 있다.

{% raw %} 
{% .. %} 라든지 {{ .. }} 같은 형태의 구문이다. 그대로 쓰게 되면 샘플 코드를 실행하려고 해서 문제가 된다. 이렇 때는 raw tag를 사용하면 된다.
{% endraw %}

이렇게 쓴다. 
{% raw %}
```sh
{ % raw % } {% some exp %} { % endraw % }
```
{% endraw %}

## 예제 1
{% raw %}
```html
  { % raw % }
    {% assign image_files = site.static_files | where: "image", true %}
    {% for myimage in image_files %}
       {{ myimage.path }}
    {% endfor %}
  { % endraw % }
```
{% endraw %}
---
layout: post
title: "Simple Texture 코드 구문 강조(code highlighting features)"
description: "A quick demo of Simple Texture theme's code highlighting features"
categories: jekyll
tags: [simple texture, jekyll]
redirect_from:
  - /2017/12/26/
---

# 목차 

* Kramdown table of contents
{:toc .toc}


# Code Spans

This is a test for inline codeblocks like `C:/Ruby23-x64` or `SELECT  "offices".* FROM "offices" `

Here is a literal `` ` ``  ***backtick***.

And here is a Ruby code fragment `x = Class.new`



# Fenced Code Blocks

~~~~~~~~~~~~
​~~~~~~~
code with tildes
​~~~~~~~~
~~~~~~~~~~~~


# Simple codeblock with long lines

    function myFunction() {
        alert("Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.");
    }

# Language of Code Blocks

```ruby
구문
def what?
  42
end
```
---

# Highlighted



## External Gist
```sh
구문 
<script src="https://gist.github.com/yizeng/9b871ad619e6dcdcc0545cac3101f361.js"></script>
```
<script src="https://gist.github.com/yizeng/9b871ad619e6dcdcc0545cac3101f361.js"></script>




## Simple Highlight

```sh
구문
{ % highlight ruby % }
{ % endhighlight % }
```

{% highlight ruby %}
def foo
  puts 'foo'
end
{% endhighlight %}



## Highlight with long lines

```sh
구문 
{ %  highlight c#  % } 
public class Hello {
    public static void Main() {
{ % endhighlight % }
```

{% highlight c# %}
public class Hello {
    public static void Main() {
        Console.WriteLine("Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.");
    }
}
{% endhighlight %}



## Highlight with line numbers and long lines

```shell
구문 
 { %  highlight javascript linenos=table  % }
 function myFunction() {
    alert("
 { %  endhighlight  % } 
```

{% highlight javascript linenos=table %}
function myFunction() {
    alert("Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.");
}
{% endhighlight %}



[Simple Texture]: https://github.com/yizeng/jekyll-theme-simple-texture
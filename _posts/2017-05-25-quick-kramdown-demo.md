---
layout: post
title: "kramdown 마크다운 편집기"
description: "A quick demo post to some kramdown features."
categories: jekyll
tags: [jekyll, kramdown]
redirect_from:
  - /2017/12/26/
---

* Kramdown table of contents
{:toc .toc}

# General Usage

This is a normal paragraph.
`[a link](http://yizeng.me)`
This is [a link](http://yizeng.me) to my homepage.
`[link](http://yizeng.me/blog "Yi Zeng's Blog")`
A [link](http://yizeng.me/blog "Yi Zeng's Blog") can also have a title.

`***text with light and strong emphasis***`
This is a ***text with light and strong emphasis***.

` **is _emphasized_ as well**`
This **is _emphasized_ as well**.

`*does _not_ work*`
This *does _not_ work*.

`**does __not__ work either**`
This **does __not__ work either**.

`footnote[^1]`
This is a footnote[^1].

`<kbd>keyboard text</kbd>`
``<code>``
This scarcely known tag emulates <kbd>keyboard text</kbd>, which is usually styled like the `<code>` tag.

`<ins>inserted</ins>`
This tag should denote <ins>inserted</ins> text.

`_italicize_`
The emphasize tag should _italicize_ text.

`<strike>strikeout text</strike>`
This tag will let you <strike>strikeout text</strike>.


## Blockquotes

`> ruby -v`
> ruby -v
>
> tsc -v

### Nested

> This is a paragraph in blockquote.
>
> > A nested blockquote.
>

### Lists inside

> Unordered List
> * lists one
> * lists two
> * lists three
>
> Ordered List
> 1. lists one
> 2. lists two
> 3. lists three

### Long lines

> Jekyll is a simple, blog-aware, static site generator perfect for personal, project, or organization sites. Think of it like a file-based CMS, without all the complexity. Jekyll takes your content, renders Markdown and Liquid templates, and spits out a complete, static website ready to be served by Apache, Nginx or another web server. Jekyll is the engine behind GitHub Pages, which you can use to host sites right from your GitHub repositories.

## Lists

* list 1 item 1
  * nested list item 1
  * nested list item 2
  * nested list item 3 with blockquote
> ruby -v
>
> tsc -v
* list 1 item 2
* list 1 item 3

## Tables


* Table 1

    |-----------------+------------+-----------------+----------------|
    | Default aligned                          | Left aligned | Center aligned | Right aligned |
    | ---------------------------------------- | :----------- | :------------: | ------------: |
    | First body part                          | Second cell  |   Third cell   |   fourth cell |
    | Second line                              | foo          |   **strong**   |           baz |
    | Third line                               | quux         |      baz       |           bar |
    | Footer row                               |              |                |               |
    | -----------------+------------+-----------------+---------------- |              |                |               |

* Table 2

    |---
    | Default aligned | Left aligned | Center aligned | Right aligned
    |-|:-|:-:|-:
    | First body part | Second cell | Third cell | fourth cell
    | Second line |foo | **strong** | baz
    | Third line |quux | baz | bar
    | Footer row

## Horizontal Rules

`* * *`

***

`---`

---

`_  _  _  _`

_  _  _  _

`---------------`

---------------


## Images

Here comes an image!

`!` `[` `smiley` `]` `(` `https://kramdown.gettalong.org/overview.png` `)`

![smiley](https://kramdown.gettalong.org/overview.png)

[^1]: This is a footnote.

[kramdown]: https://kramdown.gettalong.org/
[Simple Texture]: https://github.com/yizeng/jekyll-theme-simple-texture
---
layout: post
title:  "Ubuntu 터미널 Guake와 파일관리자 MC를 사용하기"
date:   2017-12-26 20:20 +0900
categories: Ubuntu
tags: [Ubuntu, guake, mc]
---

* Kramdown table of contents
{:toc .toc}

# 참조

[GUAKE Terminal](http://guake-project.org/) 

[Install Guake Top Down Terminal In Gnome Ubuntu 16.04](https://www.youtube.com/watch?v=4kPJcUClZL8) 

[Github Guake](https://github.com/Guake/guake)


![Guake 터미널의 모습](http://guake-project.org/img/screenshot.png)


# Guake란?  

Guake는  ***`F12`***만 누르면  위에서 아래로(Top-down) 펼쳐졌다가 다시 누르면 사라지는  Gnome의 터미널창입니다.  Quake란 게임의 터미널로부터 영감을 얻어 **매우 편리하게 사용할 수 있도록** 설계되어 있습니다.  



# MC란?

Midnight Commander - a powerful file manager

`guake`를 실행해서 바로 **미드나잇 커맨더** 를 실행합니다. `mc` 하고 엔터하면 됩니다. 그런 다음 **`F11`**  눌러서 최대화 시킨다음,  **`F12`** 를 눌러서 필요할 때마다 펼쳤다가 올릴 수 있습니다. 



# 설치

```sh
sudo apt-get update
sudo apt-get install guake mc
```





# 지우기 

To remove **➀  just mc package itself** from Ubuntu 16.04 (Xenial Xerus) execute on terminal:

```
sudo apt-get remove mc guake
```



## Uninstall mc and it's dependent packages  

To remove **➀ packages and ➁ any other dependant package**  which are no longer needed from Ubuntu Xenial.

```sh
$ sudo apt-get remove --auto-remove guake mc 
```



## Purging mc 

If you also want to delete **➀ configuration and/or  ➁ data files**  of mc, guake from Ubuntu Xenial then this will work:

```sh
$ sudo apt-get purge mc guake
```

To delete **➀ configuration and/or ➁ data files of mc, guake and  ➂ their dependencies** from Ubuntu Xenial then execute:

```sh
$ sudo apt-get purge --auto-remove mc guake
```
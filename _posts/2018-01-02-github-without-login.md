---
layout: post
title:  "로그인 없이 GitHub 사용하기"
description: "Connecting to GitHub with SSH - GitHub Help"
categories: git
tags: [git, github, ssh, RSA key]
redirect_from:
  - /2018/01/02/
---

* Kramdown table of contents
{:toc .toc}

# 참고
> [Connecting to GitHub with SSH - GitHub Help](https://help.github.com/articles/connecting-to-github-with-ssh/)  
> 여기 설명이 매우 좋습니다. 

> [Connecting to GitHub with SSH - GitHub Enterprise Help](https://help.github.com/enterprise/2.11/user/articles/connecting-to-github-with-ssh/)

> [git-credential-cache - git Documentation](https://git-scm.com/docs/git-credential-cache)

> [GitLab](https://gitlab.com/)

> [[GitHub] (공용 서버 등에서) 로그인 없이 GitHub 사용하기](http://blog.leocat.kr/notes/2017/11/27/github-deploy-key)  
> 한글 블로그, 읽기 편합니다.  이 블로그는 이곳을 참고합니다.  

# 문제 상황
공용 서버는 말 그대로 공용이기 때문에 특정 사용자로 로그인을 하기 난감한 경우가 있다.
이런 경우 **repository에 서버 자체를 인증**해 두고, ssh를 통해 로그인을 하지 않고 접근할 수 있는 방법이 있다.
위와 같은 문제 상황을 염두하지 않더라도  github에 인증없이 ssh로 통신하는 것은 매우 편하다.

# 방법 
## 1) RSA key 생성
```sh
$ ssh-keygen -t rsa
$ cat /myhome/.ssh/id_rsa.pub
```
## 2) GitHub에 Deploy Key 등록
접속할 Github repository ➜ Settings ➜ Deploy Keys ➜ Add deploy key

## 3) 접속 확인
```sh
$ ssh -T git@github.com
$ ssh -T git@'{ENTERPRISE_HOST}'
```
## 4) 프로젝트 clone받기 

```sh
git@github.com:me/my-project.git 
```
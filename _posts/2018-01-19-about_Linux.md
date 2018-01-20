---
layout: post
title:  "리눅스, 그 주요 명령어들"
date:   2018-01-19
categories: Ubuntu
tags: [Ubuntu, rsync, screen, tmux,  cron]
---

* Kramdown table of contents
{:toc .toc}


# 1. 리눅스? 우분투? 가상머신! 

* [리눅스 기본 명령어](http://www.incodom.kr/Linux/%EA%B8%B0%EB%B3%B8%EB%AA%85%EB%A0%B9%EC%96%B4)  -  정리가 잘 되어 있다. 
* [YouTube- Linux Sysadmin Basics -- Course Introduction](https://www.youtube.com/watch?v=bju_FdCo42w&list=PLtK75qxsQaMLZSo7KL-PmiRarU7hrpnwK) 


* [생활코딩 리눅스](https://opentutorials.org/course/141/1002)  
* [리눅스의 역사](http://en.wikipedia.org/wiki/File:Gldt.svg) 
* [우분투-위키백과]
* [궁극의 랜섬웨어 대응법 “리눅스에서 윈도우를 가상머신으로”]([ http://www.itworld.co.kr/news/104881#csidx4f60429395e9a1eb752436371941867 ](http://www.itworld.co.kr/news/104881#csidx4f60429395e9a1eb752436371941867)![img](http://linkback.itworld.co.kr/images/onebyone.gif?action_id=4f60429395e9a1eb752436371941867))


 **우분투**는 데비안(Debian) 계열의 리눅스 배포본 중 하나인데,  빠른 업데이트와 보안성이 좋아 많은 웹 서버에서 사용하고 있는 운영체제이다.  UNIX라는 같은 뿌리의 커널에서  MacOS, 리눅스(Linux) 등이 나왔으므로  우분투에 익숙해지면  UNIX-Like 운영체제 모두에 친숙해질 수 있다. 

 보통 우분투 등 리눅스 계열의 OS를 쓰는 사람들은  **윈도우 계열의 OS를 가상 PC에 올려 많이 사용** 한다.  원활한 인터넷 뱅킹, 쇼핑몰 결제,  국가 운영 사이트 등에서 요구하는 수 많은 activeX들, MS오피스나 한컴오피스를 사용해야만 하는 오피스 실무 등 가상머신을 활용해야만 하는 필요가 있다. 



**윈도우를 격리하는 것이 좋은 이유**

보안 측면에서 윈도우를 가상머신으로 구동하는 것이 훨씬 안전하다. 운영체제를 가상화함으로써 운영체제를 하드웨어 자체와 분리하고 일종의 장벽을 만들어 **호스트 운영체제인 리눅스가 외부에서 관리** 할 수 있다. **가상 스토리지**는 보통 하드디스크처럼 보이지만, 명시적으로 가상머신 바깥의 폴더에 대한 액세스를 허용하지 않는 한, 나머지 시스템에는 액세스할 수 없다. 이런 가상 스토리지의 장점은 모든 윈도우 
애플리케이션, 즉 파일이나 애플리케이션, 작업 등이 하나의 파일로 저장된다는 것이다. 이 파일은 쉽게 백업할 수 있고, 저장할 수  있으며, 암호화해 클라우드에 저장할 수 있고, 수백 번 복사해도 되고 삭제해도 된다. 버추얼박스(VirtualBox)는 심지어 가상 드라이브의 스냅샷을 지원해 가상 스토리지 백업과 관련된 성가신 부분을 모두 제거했다.







---

# 2. 우분투 설치

* [VMware와 VirtualBox 비교](https://blog.xianchoi.kr/331)

클라우드 컴퓨팅 :  **Amazon**, Azure

가상머신 :  [VMware](https://my.vmware.com/web/vmware/free#desktop_end_user_computing/vmware_player/7_0),  [VirtualBox for Linux](https://www.virtualbox.org/wiki/Linux_Downloads)

머신 설치 



---

# 3. 터미널

## 1) GUI  ⬌  CLI

* `Ctrl` + `Alt` + `F1 ~ F5`   : GUI 환경에서 CLI 환경으로 
* `Ctrl` + `Alt` + `F7` :  CLI  ➜ GUI 



## 2) X윈도우에서 터미널 창 열기 

* `Ctrl` + `Alt` + `T`   또는 좌측 바에서 터미널 창 클릭 



## 3) X윈도우 창 바꾸기 

* `Ctrl` + `Alt`  + `⇐, ⇑, ⇓, ⇒`(방향키)


---

# 4. 슈퍼유저 

* 슈퍼유저 - 리눅스 시스템을 관리하는 사용자

## 1) sudo - 슈퍼유저 권한 실행

```sh
$ sudo 명령어 
$ sudo useradd 테스트유저01
[sudo] password for ubuntu-learn: ********
# 
```



## 2) sudo su - 슈퍼유저로 전환

```sh
$ sudo su
root@ubuntu-learn:/home/learn# 
```

```sh
$ su
암호: 슈퍼유저password
root@ubuntu-learn:/home/learn#
```



## 3) GUI 환경에서 슈퍼유저 - `사용자 계정` 

* `윈도우key` + `사용자 계정`  ⇒ 잠금해제 ⇒ 인증


---

# 5. 사용자 관리

* 리눅스는 다중 접속, 멀티테스킹을 지원하는 운영체제이므로 다중 사용자를 지원한다.  로그인 과정을 통하여 사용자를 구분하며, 사용자별로 그룹을 지정하여 관리할 수 있다. 

## 1) 사용자 생성 - `adduser`, `useradd` 

```sh
➜ ~ adduser
adduser: 루트만이 사용자나 그룹을 시스템에 추가할 수 있습니다.
```

* sudo 권한으로 실행해야 함 : 대화형 입력이 이루어짐 
* 혼자 또는 작은 규모의 사용자 집단에서 쓰고,  규모가 있는 조직에서는 뒤에 나올 group을 사용한다.

```sh
➜ ~ sudo adduser
[sudo] password for learn: ******
adduser: 하나 혹은 두 개의 이름만 지정할 수 있습니다.

➜ ~ sudo adduser test_user
'test_user' 사용자를 추가 중...
새 그룹 'test_user' (1001) 추가 ...
새 사용자 'test_user' (1001) 을(를) 그룹 'test_user' (으)로 추가 ...
'/home/test_user' 홈 디렉터리를 생성하는 중...
'/etc/skel'에서 파일들을 복사하는 중...
새 UNIX 암호 입력: 
새 UNIX 암호 재입력: 
passwd: 암호를 성공적으로 업데이트했습니다
test_user의 사용자의 정보를 바꿉니다
새로운 값을 넣거나, 기본값을 원하시면 엔터를 치세요
	이름 []: 
	방 번호 []: 
	직장 전화번호 []: 
	집 전화번호 []: 
	기타 []: 
정보가 올바릅니까? [Y/n] Y
```

* useradd는 adduser와 달리 대화형이 아닌 방식으로 사용자 환경을 옵션으로 지정한다. 

```sh
➜  ~ sudo useradd
Usage: useradd [options] LOGIN
       useradd -D
       useradd -D [options]

Options:
  -b, --base-dir BASE_DIR       base directory for the home directory of the
                                new account
  -c, --comment COMMENT         GECOS field of the new account
  -d, --home-dir HOME_DIR       home directory of the new account
  -D, --defaults                print or change default useradd configuration
  -e, --expiredate EXPIRE_DATE  expiration date of the new account
  -f, --inactive INACTIVE       password inactivity period of the new account
  -g, --gid GROUP               name or ID of the primary group of the new
                                account
  -G, --groups GROUPS           list of supplementary groups of the new
                                account
  -h, --help                    display this help message and exit
  -k, --skel SKEL_DIR           use this alternative skeleton directory
  -K, --key KEY=VALUE           override /etc/login.defs defaults
  -l, --no-log-init             do not add the user to the lastlog and
                                faillog databases
  -m, --create-home             create the user's home directory
  -M, --no-create-home          do not create the user's home directory
  -N, --no-user-group           do not create a group with the same name as
                                the user
  -o, --non-unique              allow to create users with duplicate
                                (non-unique) UID
  -p, --password PASSWORD       encrypted password of the new account
  -r, --system                  create a system account
  -R, --root CHROOT_DIR         directory to chroot into
  -s, --shell SHELL             login shell of the new account
  -u, --uid UID                 user ID of the new account
  -U, --user-group              create a group with the same name as the user
  -Z, --selinux-user SEUSER     use a specific SEUSER for the SELinux user mapping
      --extrausers              Use the extra users database
```

* 디렉토리 test_user2를 만들고  /home/test_user2를 홈디렉토리로 지정하며, shell 환경을 bash로 만드는 작업

```sh
➜  ~ sudo useradd -m test_user2  -d /home/test_user2 -s /bin/bash 
```

* 확인 - ① 우분투 전원버튼을 누르거나  ② 로그아웃하면  새로 만든 유저들을 확인할 수 있다.

```sh
➜  /home ls -al
합계 36K
drwxr-xr-x  6 root       root       4.0K  1월 17 13:05 .
drwxr-xr-x 24 root       root       4.0K  1월 11 06:04 ..
drwxr-xr-x 48 learn      learn      4.0K  1월 17 13:19 learn
drwxr-xr-x  2 test_user  test_user  4.0K  1월 17 12:53 test_user
drwxr-xr-x  2 test_user2 test_user2 4.0K  1월 17 13:05 test_user2
```



## 2) 사용자 비밀번호 - `passwd`

* **슈퍼유저**는 모든 사용자의 비밀번호를 변경할 수 있고, 
* **일반 사용자**는 자신의 비밀번호만 사용할 수 있음 

```sh
➜  /home sudo su test_user
[sudo] password for learn: ******
test_user@ubuntu-learn:/home$ cd test_user

test_user@ubuntu-learn:~$ sudo passwd test_user
test_user에 대한 암호 변경 중
(현재) UNIX 암호: xxxxxxxxx
새 UNIX 암호 입력: yyyyyyy
새 UNIX 암호 재입력: yyyyyyyy
passwd: 암호를 성공적으로 업데이트했습니다. 
```



## 3) 사용자 삭제 - `deluser`, `userdel`

```sh
➜  ~ sudo deluser
[sudo] password for learn:  ******

제거할 사용자 이름 입력: test_user
'test_user' 사용자 제거 중...
경고: 'test_user'그룹이 회원목록에 더이상 없음.
완료.
# 그러나 홈디렉토리는 남아있음
➜  /home ls -al
합계 36K
drwxr-xr-x  6 root      root      4.0K  1월 17 13:05 .
drwxr-xr-x 24 root      root      4.0K  1월 11 06:04 ..
drwxr-xr-x 48 learn     learn     4.0K  1월 17 18:07 learn
drwxr-xr-x  2 test_user test_user 4.0K  1월 17 18:04 test_user
drwxr-xr-x  2      1002      1002 4.0K  1월 17 13:05 test_user2
```

* 홈디렉토리까지 모두 지우기 

```sh
➜  ~ sudo userdel -rf test_user
➜  /home ls -al
drwxr-xr-x  4 root  root  4.0K  1월 17 18:33 .
drwxr-xr-x 24 root  root  4.0K  1월 11 06:04 ..
drwxr-xr-x 48 learn learn 4.0K  1월 17 18:33 learn
```



## 4) 사용자 정보 변경 - `chfn`, `chsh`, `usermod`

```sh
➜  ~ sudo chfn learn
learn의 사용자의 정보를 바꿉니다
새로운 값을 넣거나, 기본값을 원하시면 엔터를 치세요
	이름 [learn]: 홍길동
	방 번호 []: 
	직장 전화번호 []: 
	집 전화번호 []: 
	기타 []: 
chfn: 이름에 ASCII가 아닌 문자가 들어 있습니다: '홍길동'
```



## 5) 그룹 관리

### (1) gnome-system-tools : GUI 방식

* Unity환경에서는 **GUI 방식으로**  그룹관리를 할 수 없음
* 따라서 우분트 소프트웨어 센터 등을 통하여 위 gnome-system-tools를 설치해야 함 



### (2) 명령어 :  CLI 방식 

* 그룹 생성 -  `addgroup`,  `groupadd`
* 그룹 변경 -  `groupmod`
* 그룹 삭제 - `delgroup` , `groupdel`

```sh
➜  ~ sudo groupadd -h
Usage: groupadd [options] GROUP

Options:
  -f, --force                   exit successfully if the group already exists,
                                and cancel -g if the GID is already used
  -g, --gid GID                 use GID for the new group
  -h, --help                    display this help message and exit
  -K, --key KEY=VALUE           override /etc/login.defs defaults
  -o, --non-unique              allow to create groups with duplicate
                                (non-unique) GID #GID중복사용가능 
  -p, --password PASSWORD       use this encrypted password for the new group
  -r, --system                  create a system account
  -R, --root CHROOT_DIR         directory to chroot into
      --extrausers              Use the extra users database

# 그룹 test 생성 
➜  ~ sudo groupadd test
[sudo] password for learn: 

# test의 GID 확인 : 1001
➜  ~ cat /etc/group
test:x:1001:      

# 그룹 test의 GID 변경  1001 >> 3000
➜  ~ sudo groupmod -g 3000 test
# 변경 확인
➜  ~ cat /etc/group
test:x:3000:  
```



### (3) Group에 사용자 추가 / 변경

* **test_user 사용자를 test, test2 그룹에 복수로 추가**하면서  홈디렉토리의 위치를 /home/test_user,  bash 쉘을 사용하는 경우 

```sh
➜  ~ sudo useradd -d /home/test_user -s /bin/bash -G test, test2 -m dakrink

➜  ~ sudo id dakrink
uid=1001(darkrink)  gid=3001(darkrink) 그룹들=3001(dakrink), 3002(dakrink), 3000(test)

# 수정
➜  ~ sudo usermod -Gdev, test dakrink
➜  ~ id dakrink
uid=1001(darkrink)  gid=3001(darkrink) 그룹들=3001(dakrink), 3002(dev), 3000(test)

# 그룹 변경 :  .test라는 파일의 그룹 변경 
sudo chgrp dev .test
# 변경 확인
drwxrwxr-x  12 learn learn 4.0K  9월  8 14:21 .rbenv
-rw-rw-r--   1 learn test     0  1월 17 19:17 .test
drwxrwxr-x   3 learn learn 4.0K  8월 26 21:42 .vim
```



---

# 6. 파일  및 디렉토리

## 1)  `ls` , `cd` , `pwd` , `mkdir`,  `rm`,  `cp` , `touch`

```sh
# 모든 파일, 리스트 형식 출력, 가독성 
ls -alh 
# 기술된 경로의 디렉토리들을 자동으로 만들어줌 
mkdir -p test1/test2/test2
```



## 2) ACL (접근제어목록)

* [Access Control List - 위키 백과](https://ko.wikipedia.org/wiki/%EC%A0%91%EA%B7%BC_%EC%A0%9C%EC%96%B4_%EB%AA%A9%EB%A1%9D) 
* 파일과 디렉토리를 사용할 수 있는 권한을 관리

```sh
drwx   r-x r-x  9 learn learn 4.0K  1월  9 18:23 문서
 소유자|그룹|기타     소유자 그룹    
```

### (1) `chmod` 

* 파일과 디렉토리의 권한 설정

```sh
  4      2      1
# R(읽기) W(쓰기) X(실행)

# 소유자
chmod U∓RWX File/디렉토리
# 그룹
chmod G∓RWX File/디렉토리
# 기타
chmod O∓RWX File/디렉토리
```

```sh
# 소유자(읽기+쓰기+실행) + 그룹(읽기+쓰기) + 기타(읽기)
➜  ~ chmod 764 test_file
# 소유자 읽기 권한 부여
➜  ~ chmod u+R test_file
# 그룹의 읽기 권한 제거
➜  ~ chmod g-R test_file
```



### (2) `chown`

* 파일과 디렉토리의 소유자를 변경함 

```sh
# chown -R: 하위 디렉토리까지 포함하여 변경함
## test_file의 소유자를 HongGildong으로 변경하기 
➜  ~ chown HongGildong test_file
## test_file의 소유자와 그룹을 HongGildong으로 변경하기 
➜  ~ chown HongGildong:HongGildong test_file
```

* `chgrp`  - 파일과 디렉토리의 그룹을 변경함 

```sh
# test_file의 그룹을 HongGildong으로 변경하기 
➜  ~ chgrp HongGildong test_file
```







## 3) 마운트

* 가상환경 (VMwhere, VirtualBox 등)에서 자원에 대한 접근 확장에 많이 쓰임


* [마운트-위키백과](https://ko.wikipedia.org/wiki/%EB%A7%88%EC%9A%B4%ED%8A%B8) 
* [컴퓨터 과학](https://ko.wikipedia.org/wiki/%EC%BB%B4%ED%93%A8%ED%84%B0_%EA%B3%BC%ED%95%99)에서 [저장 장치](https://ko.wikipedia.org/wiki/%EC%A0%80%EC%9E%A5_%EC%9E%A5%EC%B9%98)에 접근할 수 있는 경로를 [디렉터리](https://ko.wikipedia.org/wiki/%EB%94%94%EB%A0%89%ED%84%B0%EB%A6%AC) 구조에 편입시키는 작업을 말한다. 좁은 의미로는 [유닉스](https://ko.wikipedia.org/wiki/%EC%9C%A0%EB%8B%89%EC%8A%A4) 계열의 [운영 체제](https://ko.wikipedia.org/wiki/%EC%9A%B4%EC%98%81_%EC%B2%B4%EC%A0%9C)에서의 [mount](https://ko.wikipedia.org/wiki/Mount_(%EC%9C%A0%EB%8B%89%EC%8A%A4)) 명령어 또는 그 명령어를 사용하는 것을 말한다. mount 명령어를 사용하면 저장 장치의 접근 경로를 원하는 위치에 생성할 수 있다. 마운트를 이용하면 [분산 파일 시스템](https://ko.wikipedia.org/wiki/%EB%B6%84%EC%82%B0_%ED%8C%8C%EC%9D%BC_%EC%8B%9C%EC%8A%A4%ED%85%9C)으로 확장하기가 용이하다. 사용자는 마운트된 미디어의 파일들에만 접근이 가능하다.
* GUI 환경 - 가상환경에서 많이 사용됨 
* CLI 환경은 아래를 참고

```sh
# cdrom의 media를 마운트하기 
➜  ~ sudo mount /dev/cdrom/media
mount: block device dev/sr0 is write-protected, mounting read-only

# cdrom의 위치 : Linux에서 관용적인 위치
➜  ~ cd /
drwxr-xr-x   3 root root 4.0K  8월 26 01:54 media #미디어들
drwxr-xr-x   2 root root 4.0K  8월  1 20:17 mnt  #마운트한 것들

# cdrom의 media를 마운트 해제하기 
➜  ~ sudo unmount /dev/cdrom/media

# 파일시스템의 종류(adfs,autofs, ext, ntfs, ...)를 지정하여 마운트하기
➜  ~ mount -t ext3 /dev/cdrom/media
```



```sh
➜  ~ df
Filesystem     1K-blocks      Used Available Use% Mounted on
udev             4000704         0   4000704   0% /dev
tmpfs             804596     50596    754000   7% /run
/dev/sda5      172889920  11804112 152280384   8% /
tmpfs            4022960     70196   3952764   2% /dev/shm
tmpfs               5120         4      5116   1% /run/lock
tmpfs            4022960         0   4022960   0% /sys/fs/cgroup
/dev/sda1          98304     21864     76440  23% /boot/efi
/dev/sda6       37327540   9658024  25750336  28% /home
tmpfs             804596       212    804384   1% /run/user/1000
/dev/sda3      278035736 174724068 103311668  63% /media/learn/OS

```



---

# 7. 프로세스 관리

## 0) PID와 PPID

* PID : 프로세스 고유 아이디
* PPID:  자신의 부모 프로세스의 고유 아이디 (**Parent Process ID** )
* 부모프로세스가 종료되면 자식프로세스도 종료된다. 
* 프로세스 사이의 부모-자식 관계는 `pstree`를 통하여 자세하게 알 수 있다. 

```sh
➜  / pstree
systemd─┬─ModemManager─┬─{gdbus}
        │              └─{gmain}
        ├─NetworkManager─┬─dhclient
        │                ├─dnsmasq
        │                ├─{gdbus}
        │                └─{gmain}
        ├─accounts-daemon─┬─{gdbus}
        │                 └─{gmain}
        ├─acpid
        ├─apache2───2*[apache2───26*[{apache2}]]
        ├─avahi-daemon───avahi-daemon
        ├─bluetoothd
        ├─colord─┬─{gdbus}
        │        └─{gmain}
        ├─cron
        ├─cups-browsed─┬─{gdbus}
        │              └─{gmain}
        ├─dbus-daemon
        ├─dockerd─┬─docker-containe───13*[{docker-containe}]
        │         └─15*[{dockerd}]      
        ├─lightdm─┬─Xorg───{InputThread}
        │         ├─lightdm─┬─upstart─┬─Typora─┬─Typora───Typora─┬─{Chrome_ChildIOT}
        │         │         │         │        │                 ├─3*[{CompositorTileW}]
        │         │         │         │        │                 ├─{Compositor}
        │         │         │         │        │                 ├─{HTMLParserThrea}
        │         │         │         │        │                 ├─{Renderer::FILE}
   
```





## 1) ps

* 리눅스 서버에서 현재 동작하고 있는 프로세스 정보를 확인
* 프로세스 고유 ID(PID), 구동 터미널(TTY), 구동 시간(TIME), 명령어(CMD)를 기본으로 알 수 있음

```sh
# X윈도우 터미널 창에서 
➜  / ps
  PID TTY          TIME CMD
24993 pts/21   00:00:00 sudo
10221 pts/21   00:00:00 zsh
24874 pts/21   00:00:00 ps
```



```sh
# console 환경(: Ctrl + Alt + F1~F5)에서
➜  / ps
  PID TTY        TIME CMD
24993 tty1   00:00:00 sudo
10221 tty1   00:00:00 zsh
24874 tty1   00:00:00 ps
# x윈도우로 복귀: Ctrl + Alt + F7
```

* 모든 명령어 보기  `ps a` 

* STAT은 중요하다.

  * `S` 는 현재 동작 중임을 나타남 


  * `d`(데드락 deadlock)  - 동작 정지 상황. 어떤 조건이 만족하면 다시 작동함 
  * `z`(좀비 zombie) - 프로그램이 종료시켰음에도 종료되지 않음.  강제종료시켜야함. 

* VSZ (가상메모리 사이즈, Virtual memory SiZe)

* RSS (실제 메모리 사이즈)

```sh
➜  / ps a

  PID TTY      STAT   TIME COMMAND

 5100 pts/20   Ss     0:00 /usr/bin/zsh
18749 tty2     Ss+    0:00 /sbin/agetty --noclear tty2 linux
25760 tty1     Ss+    0:00 /sbin/agetty --noclear tty1 linux
25833 pts/21   R+     0:00 ps a
31367 pts/1    Ss     0:00 zsh
31728 pts/19   Ss     0:00 /usr/bin/zsh
```

* 사용자별로 프로세스 보기 `ps u` 

```sh
➜  / ps u

USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
learn     5100  0.0  0.0  58040  6724 pts/20   Ss    1월16   0:00 /usr/bin/zsh
learn    25965  0.0  0.0  45712  3336 pts/21   R+   00:06   0:00 ps u
learn    31367  0.0  0.0  55688  6156 pts/1    Ss    1월16   0:00 zsh
learn    31723  0.0  0.8 690864 70620 pts/1    Rl+   1월16   0:25 python2 -m guake.main
learn    31728  0.0  0.0  57796  6592 pts/19   Ss    1월16   0:00 /usr/bin/zsh
```



* 백그라운드를 포함한 모든 프로세스를 출력  `ps x`

```sh
➜  / ps x
```

* 프로세스 상태 정보를 좀 더 자세히 출력  `ps elf`

```sh
➜  / ps elf
F   UID   PID  PPID PRI  NI    VSZ   RSS WCHAN  STAT TTY        TIME COMMAND
0  1000 31367  2852  20   0  55688  6156 sigsus Ss   pts/1      0:00 zsh XDG_SEAT_PATH=/org/freedesktop/DisplayManager/Seat0 XDG_CONFIG_DIRS=/etc/
0  1000 31723 31367  20   0 690864 70688 flush_ Sl+  pts/1      0:26  \_ 

# zsh의 PID는 31367이며 부모프로세스는 2852이다. 
```

* 종종 쓰이는 형태  `ps aux`

```sh
➜  / ps aux
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.0  0.0 185696  4968 ?        Ss    1월06   0:14 /sbin/init splash
root         2  0.0  0.0      0     0 ?        S     1월06   0:00 [kthreadd]
...
root     32336  0.0  0.0      0     0 ?        S     1월11   0:00 [jfsSync]
root     32375  0.0  0.0      0     0 ?        S<    1월11   0:00 [bioset]
```



## 2) top

* [[Linux\] Top 명령어 사용법](http://bluelimn.tistory.com/entry/Linux-Top-%EB%AA%85%EB%A0%B9%EC%96%B4-%EC%82%AC%EC%9A%A9%EB%B2%95)


* 동적으로 ps결과를 볼 수 있음

```sh
➜  / top 

top - 00:36:21 up 11 days,    22:59,   8 users, 
      #동작시간 11일36:21      #현재 시간  # 8명의 사용자들 
load average: 1.48, 1.16, 1.15  
#동작부하:       1분/  5분/   15분 간격
Tasks: 298 total, 3 running,  290 sleeping, 0 stopped,  5 zombie    
#전체 프로세스 개수  #현재 동작   # 대기 중       # 정지      # 좀비프로세스 개수 
%Cpu(s):  23.1 us,  8.1 sy,  0.0 ni, 68.6 id,  0.2 wa,  0.0 hi,  0.1 si,  0.0 st
# cpu사용율   사용자    시스템                    대기(데드락)
KiB Mem :  8045924 total,  1810540 free,  4350364 used,  1885020 buff/cache
# 사용자별로 쓸 수 있는 메모리의 크기가 한정되어 있다. 넘어가면 스왑메모리를 사용함
KiB Swap:  7999484 total,  7311408 free,   688076 used.  2685080 avail Mem 
# 스왑메모리가 크면 좋지 않다. >>> 디스크 I/O가 발생함 

  PID USER      PR  NI    VIRT    RES    SHR S  %CPU %MEM     TIME+ COMMAND   
 2308 learn     20   0 1653712 224660  40380 R  33.4  2.8   1334:16 compiz  
 1120 root      20   0  599304 131444  85252 R  30.1  1.6 943:56.36 Xorg  
 4486 learn     20   0 2514840 414520 163860 S  19.2  5.2  50:43.68 Web Content   
```



* 부하 테스트 - 1,000명이 10,000번 특정 페이지에 접속할 경우 

```sh
root@ubuntu:~# ab -n10000 -c1000 http://dev.test.org/testcourse/1
root@ubuntu:~# top

top - 00:58:58 up 11 days, 23:21,  8 users,  load average: 14.57, 3.49, 1.32
Tasks: 294 total,   1 running, 288 sleeping,   0 stopped,   5 zombie
%Cpu(s): 27.9 us,  5.1 sy,  0.0 ni, 67.0 id,  0.0 wa,  0.0 hi,  0.0 si,  0.0 st
KiB Mem :  8045924 total,  1578008 free,  4437840 used,  2030076 buff/cache
KiB Swap:  7999484 total,  7311564 free,   687920 used.  2602680 avail Mem 

```



## 3) kill

* 좀비 프로세스 종료시키기 
* 윈도우 작업 관리자에서 프로세스 종료와 같음

```sh
# PID 찾기 
ps aux | grep [프로세스 이름]
top 
# 일반 kill
kill [PID]
# root계정으로 kill
su -
passwd: *****
root@ubuntu:~# kill [PID]
# 강제 종료
root@ubuntu:~# kill -9 [PID]
```



## 4)  GUI 환경

* X윈도우즈 :  **시스템 감시(system monitor)**  : 상황을 직관적으로 알 수 있다. 


---

# 8. 스케쥴 관리(Cron)

* [위키백과-Cron](https://ko.wikipedia.org/wiki/Cron) 
* UNIX / Linux에서 특정 시간, 특정 날짜에 정해진 명령어를 실행 - 일정 시간 간격으로 웹서비스를 확인하는 등에 쓰임 
* `etc/crontab`

```sh
➜  / ps aux | grep cron
root       828  0.0  0.0  37356  2468 ?        Ss    1월06   0:02 /usr/sbin/cron -f
learn    20745  0.0  0.0  15504   928 pts/21   S+   23:20   0:00 grep --color=auto --exclude-dir=.bzr --exclude-dir=CVS --exclude-dir=.git --exclude-dir=.hg --exclude-dir=.svn cron

➜  sudo vi /etc/crontab 

SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

# m h dom mon dow user  command
17 *    * * *  root  cd / && run-parts --report /etc/cron.hourly
25 6    * * *  root  test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily )
47 6    * * 7  root    test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly )
52 6    1 * *  root  test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )

# 동작 확인
➜ /var/log  cat syslog | grep cron

Jan 18 09:44:57 ubuntu-learn anacron[2098]: Job `cron.daily' terminated
Jan 18 09:44:57 ubuntu-learn anacron[2098]: Normal exit (1 job run)
Jan 18 18:15:08 ubuntu-learn systemd[1]: Started Run anacron jobs at resume.
Jan 18 18:15:08 ubuntu-learn systemd[1]: Started Run anacron jobs.
Jan 18 18:15:08 ubuntu-learn anacron[7439]: Anacron 2.3 started on 2018-01-18
Jan 18 18:15:08 ubuntu-learn anacron[7439]: Normal exit (0 jobs run)
Jan 18 18:17:01 ubuntu-learn CRON[7850]: (root) CMD (   cd / && run-parts --report /etc/cron.hourly)
Jan 18 19:17:01 ubuntu-learn CRON[10003]: (root) CMD (   cd / && run-parts --report /etc/cron.hourly)
```

## 1) 문법

* 분(m) | 시간(h) | 날짜(dom) | 달(mon) | 요일(dow) | 사용자(user)  | 명령(command)
  * min(0~59) 
  * hour(0~23)
  * day of month(1 ~31)
  * month(1~12)
  * day of week(0~7) - 0:Sun, 1:Mon ~ 6:Sat, 7:Sun 



| 표현   | 설명                            |           |
| ---- | ----------------------------- | --------- |
| *    | everytime                     |           |
| -    | 해당 시간 사이 , 예) 1-3             | 1시부터 3시까지 |
| ,    | 구분,  예) 1, 3                  | 1시, 3시    |
| /    | 뒤에 나오는 단위별로 매번,  예)  m자리에 */2 | 2분 단위로    |





---

# 9. 패키지 관리

## 1) apt 

* apt (Advanced-Package-Tool)

> [리눅스에서 패키지 관리하는 방법 - 초보개발자](http://freehoon.tistory.com/43) 
>
> 리눅스에서 필요한 프로그램을 설치하는 방법에는 여러 가지가 있습니다.  예전부터 많이 사용해 오던 방식으로는 압축 파일로 묶여 있는 파일을 다운받아 서버에 업로드 한 후 압축을 풀고, 실행(경우에 따라 컴파일) 하는 방법이 있습니다. 하지만 이 방법은 프로그램 의존성 검사 등과 같은 것을 관리자가 하나하나 해주어야 한다는 불편함이 있었는데요, 
>
> 최근에는 이런 **패키지 형식**으로 프로그램을 다운받아 설치하는 방법을 주로 사용하고 있습니다. 의존성 감사까지 자동으로 이루어지고 있어서 **관리가 매우 편합니다.** 



| 명령어 또는 개념                    | 설명                                |
| ---------------------------- | --------------------------------- |
| Repository                   | 패키지 저장소 - 리눅스에서 **설치 가능**한 패키지 목록 |
| ❶ 패키지 **검색 **                |                                   |
| `➔ apt-cache search 패키지명`    | Repository에서 설치 가능한 패키지를 검색       |
| ❷ 갱신                         |                                   |
| `➔ apt-get update`           | Repository를 최신으로 갱신함              |
| `➔ apt-get upgrade`          | 설치한 패키지를 최신 버전으로 업그레이드            |
| `➔ apt-get dist-upgrade`     | 의존성을 검사하며 설치한 패키지를 업그레이드          |
| ❸ 설치                         |                                   |
| `➔ apt-get install 패키지명`     | 패키지명 설치                           |
| `➔   apt-cache dist-upgrade` | Repository 에서 설치 의존성을 검사하며 설치     |
| ❹ 삭제                         |                                   |
| `➔  apt-get remove 패키지명`     |                                   |
| `➔  apt-get purge 패키지명`      | 설정파일까지 삭제                         |



---

# 10. Rsync

## 1) 참고

> [wikipedia - Rsync](http://en.wikipedia.org/wiki/Rsync)  
>
> [Rsync : 10 Practical Examples of Rsync Command in Linux](http://www.tecmint.com/rsync-local-remote-file-synchronization-commands/?utm_source=feedburner&utm_medium=feed&utm_campaign=Feed%3A+tecmint+%28Tecmint%3A+Linux+Howto%27s+Guide%29) 
>
> [우분투 리눅스 문서 - rsync](https://help.ubuntu.com/community/rsync)
>
> [Rsync 10가지 사용 예제들](https://www.joinc.co.kr/w/Site/Tip/Rsync) 
>
> [Rsync 정리](http://gyus.me/?p=214)  - 정리가 잘 됨 

* 원격 동기화 툴/ 네트워크 프로토콜 (파일/디렉토리 복사  및 동기화) 
* 백업용으로 많이 쓰임
* cron을 통하여 mirror 사이트(시스템)을 쉽게 구축할 수 있음 
* rcp, scp를 대체할 수 있음


---

# 11.  vi

## 1) 참조

> [vi - 위키백과, 우리 모두의 백과사전](https://www.google.co.kr/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&uact=8&ved=0ahUKEwizhJij8OHYAhXHmJQKHcIDCQUQFgglMAA&url=https%3A%2F%2Fko.wikipedia.org%2Fwiki%2FVi&usg=AOvVaw29abMU28Y5GFdQ-lPvSKxi)
>
> [VI 에디터 사용법(vim) - KLDP](https://wiki.kldp.org/KoreanDoc/html/Vim_Guide-KLDP/Vim_Guide-KLDP.html)  - 여기 설명이 좋다. 
>
> [완전 초보를 위한 Vim - 놀부](https://nolboo.kim/blog/2016/11/15/vim-for-beginner/)  
>
> [Practical Vim 2판 정리 - 놀부](https://nolboo.kim/practical-vim/) - 여기 설명이 매우 좋다. 
>
> [vim을 왜 쓰냐고? - 삶의 흔적](http://mjae.kr/2016/10/01/vimlinux-1-vim%EC%9D%84-%EC%99%9C-%EC%93%B0%EB%83%90%EA%B3%A0/) - Vim을 매우 현란하게 쓸 수 있다.  걍력 추천 !!!
>
> #### ✓ 파이썬을 위한 Vim 
>
> [Vim- Full Stack Python](https://www.fullstackpython.com/vim.html) - 매우 풍부한 내용이 있다. 
>
> [VIM and Python - A Match Made in Heaven](https://realpython.com/blog/python/vim-and-python-a-match-made-in-heaven/) - 예제가 풍부하다. 
>
> [VIM and Python - a Match Made in Heaven이 안된다는 분들을 위한 작은 가이드](https://www.sangkon.com/2015/11/06/vim-and-python-small-problem-and-solution/) 
>
> * 위 사이트를 잘 보완해준다. 
>
> [vim을 Python 에디터로 잘 활용하기](http://pythoninreal.blogspot.kr/2013/12/vim-python.html)  - 한글문서, 괜찮은 싸이트이다. 



---

# 12. 리눅스 기본명령어 

> [리눅스 기본 명령어](http://www.incodom.kr/Linux/%EA%B8%B0%EB%B3%B8%EB%AA%85%EB%A0%B9%EC%96%B4)  -  정리가 잘 되어 있다. 





---

# 13. tmux vs Screen

* 하나의 터미널을 여러 개의 가상 터미널로 실행할 수 있는 멀티플렉서이다.  여러 개의 터미널을 띄우는 것과는 다르다는 점에 유의!!!
* ​

> ✓ screen
>
> [screen - 이한영](https://lhy.kr/linux/linux-screen)
>
> [리눅스 기본명령어 - screen](http://www.incodom.kr/Linux/%EA%B8%B0%EB%B3%B8%EB%AA%85%EB%A0%B9%EC%96%B4/screen) 
>
> [YouTube- How to use GNU screen - the terminal multiplexer](https://www.youtube.com/watch?v=I4xVn6Io5Nw)
>
> ✓ tmux
>
> [TTY 멀티플랙서 tmux](https://blog.outsider.ne.kr/699) - 한글 설명 
>
> [TMUX – The Terminal Multiplexer (Part 1)](http://blog.hawkhost.com/2010/06/28/tmux-the-terminal-multiplexer/)
>
> [TMUX – The Terminal Multiplexer (Part 2)](http://blog.hawkhost.com/2010/07/02/tmux-%E2%80%93-the-terminal-multiplexer-part-2/)
>
> [YouTube-Basic tmux Tutorial - Windows, Panes, and Sessions over SSH](https://www.youtube.com/watch?v=BHhA_ZKjyxo)



---

# 14. df & du

> [**Unix/Linux 디스크 용량 확인 (df/du)**](http://ra2kstar.tistory.com/135)
>
> [Linux 기본명령어 - df ](http://www.incodom.kr/Linux/%EA%B8%B0%EB%B3%B8%EB%AA%85%EB%A0%B9%EC%96%B4/df) 



---

# 15. 메일서버 

> [메일 서버 세팅하기 - postfix, dovecot, roundcube](http://zzaps.tistory.com/312) 
>
> [내 메일서버 설치하기(Ubuntu 16.04)](http://blog.khphub.com/23) 
>
> [[Linux/우분투] Postfix를 이용한 메일서버 개설](http://www.atblog.co.kr/?p=4877) 
>
> [Ubuntu에서 Postfix와 Gmail SMTP를 사용하여 메일 보내기](http://blog.saltfactory.net/relay-mail-to-gmail-via-postfix-on-ubuntu/) 



---



# 16. DNS

> [우분투 네임서버 구축하기](https://blog.lael.be/post/6308)
>
> [국내 도메인 등록업체 가격비교 (cheap domain)](https://blog.lael.be/post/6357) 

* 우분트 네트워크(내부적인 네트워크)를 구성할 때 쓰인다.


---

# 17. 보안 - iptables, tcpwrapper

> [Ubuntu iptables로 방화벽 port 설정하기](http://freestrokes.tistory.com/entry/Ubuntu-iptables%EB%A1%9C-%EB%B0%A9%ED%99%94%EB%B2%BD-port-%EC%84%A4%EC%A0%95%ED%95%98%EA%B8%B0) 
>
> [[LINUX] 리눅스에서 TCP Wrapper 사용하기(접근제어-ACL설정)](http://yangnoon.tistory.com/37) 
>
> [Linux 접속 차단하기. (TCP Wrapper) - TechNote.kr](https://www.google.co.kr/url?sa=t&rct=j&q=&esrc=s&source=web&cd=2&cad=rja&uact=8&ved=0ahUKEwj-8KPfn-LYAhXBy7wKHYmaA-QQFggsMAE&url=http%3A%2F%2Ftechnote.kr%2F175&usg=AOvVaw3ma0szfh14G-X3rxSVMHR6)  


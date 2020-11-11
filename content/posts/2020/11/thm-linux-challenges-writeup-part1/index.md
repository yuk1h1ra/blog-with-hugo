---
title: "【THM: Try Hack Me】Linux Challengesを解いてみた - Part1【WriteUP】"
date: 2020-11-10T08:43:35+09:00
description:
draft: false
hideToc: false
enableToc: true
enableTocContent: false
tocPosition: inner
tocLevels: ["h2", "h3", "h4"]
tags:
- writeup
- ctf
- tryhackme
categories:
- CTF
series:
- Linux Challenges - THM
image:
---

# はじめに

Try Hack Meというオンラインでサイバーセキュリティを学べるサイトで学習を勧めています。
そこでは、Walkthrough形式とCTFs形式で学ぶことができ、InputとOutputを両方学習することができます。

今回はそのTryHackMeの[Linux Challenges](https://tryhackme.com/room/linuxctf)を解いてみたので、そちらのWriteUPを記事にしたいと思います。
量が多いため、何回かに分けてWriteUPを記していきたいと思います。

まだ解いていない人や、これから解いてみたいという人は、ブラウザバックしてください。

また、この記事には直接Flagの値は記載していません。自分の手で実際に実行してFlagの奪取をしてください。
それでは、やっていきましょう。

# Write UP

## Task 1 Linux Challenges Introduction

TryHackMeでは、今回学ぶ大章をRoomと呼んでいます。
今回のRoomでは、Linuxの基本的なスキルを学びながらFlagを奪取していくことになります。

自分は仮想環境にあるParrot Security OSを立ち上げ、VPN接続をした後、SSHにてRoomのマシンに接続しています。
Parrot Securityの環境構築方法については、以前ブログ記事にしているので、そちらを参考にしてみてください。

<iframe class="hatenablogcard" style="width:100%;height:155px;margin:15px 0;" title="セキュリティに特化したParrot Security OSをVirtualBoxにインストールするまで – Yuk1h1ra's Knowledge Kingdom" src="https://hatenablog-parts.com/embed?url=https://blog.yuk1h1ra.me/posts/2020/09/install-parrot-security-os/" frameborder="0" scrolling="no"></iframe>

> https://blog.yuk1h1ra.me/posts/2020/09/install-parrot-security-os/

### How many visible files can you see in garrys home directory?

こちらは簡単ですね。ディレクトリ内のファイルの数を答えればOKです。

```bash
garry@ip-10-10-14-129:~$ ls
flag1.txt  flag24  flag29
```

## Task 2 The Basics

### What is flag 1?

flag1の内容を読み取ってあげればFlag1がわかりました。

```bash
garry@ip-10-10-14-129:~$ cat flag1.txt 
There are flags hidden around the file system, its your job to find them.

Flag 1: xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx

Log into bobs account to get flag 2.

Username: bob
Password: xxxxxxxxxx
```

### Log into bob's account using the credentials shown in flag 1.

flag1.txtに書かれているbobにログインし、次項からはbobで実行していきます。

### What is flag 2?

とりあえず、bobのホームディレクトリを確認したら、flag2.txtがあったので、そちらを読み取ってFlagゲットです

```bash
bob@ip-10-10-14-129:~$ ls
Desktop  Documents  Downloads  flag13  flag21.php  flag2.txt  flag8.tar.gz  Music  Pictures  Public  Templates  Videos

bob@ip-10-10-14-129:~$ cat flag2.txt 
Flag 2: xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

### Flag 3 is located where bob's bash history gets stored.

bobのbash histroyを見ればいいとのことで、自分は`history`コマンドで確認したら１行目にありました。

```bash
bob@ip-10-10-14-129:~$ history
    1  xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
    2  cat ~/.bash_history 
    3  rm ~/.bash_history
    4  vim ~/.bash_history
    5  exit
    6  ls
    7  crontab -e
    8  ls
    9  cd /home/alice/
   10  ls
   11  cd .ssh
   12  ssh -i .ssh/id_rsa alice@localhost
   13  exit
   14  ls
   15  cd ../alice/
   16  cat .ssh/id_rsa
   17  cat /home/alice/.ssh/id_rsa
   18  exit
   19  cat ~/.bash_history 
   20  exit
   21  cd
   22  ls
   23  cat flag2.txt 
   24  history
```

### Flag 4 is located where cron jobs are created.

cronについてですが、自分はcronは触ったことはあるけどよくわからない状態でしたのでmanページなどで確認していました。
`crontab -e`で現在のcrontabを編集できるため、そちらを確認したところFlagが書いてありました。

```bash
bob@ip-10-10-14-129:~$ crontab -e
No modification made
```

### Find and retrieve flag 5.

flag5を探せとのことで、とりあえず`find`で検索してみたら該当するのがありました。

```bash
bob@ip-10-10-14-129:~$ find / 2>/dev/null | grep flag5
/lib/terminfo/E/flag5.txt
bob@ip-10-10-14-129:~$ cat /lib/terminfo/E/flag5.txt 
xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

### "Grep" through flag 6 and find the flag. The first 2 characters of the flag is c9.

Flag5の応用です。flag6.txtは膨大な量のテキストデータですので、うまくgrepしてあげることで奪取することができました。

```bash
bob@ip-10-10-14-129:~$ find / 2>/dev/null | grep flag6
/home/flag6.txt
```

### Look at the systems processes. What is flag 7.

プロセス中にFlagが含まれているとのことで、それの確認をしました。
top/htopで探す方法もあると思いますが、自分はpsコマンドで探しちゃいました。

```bash
bob@ip-10-10-14-129:~$ ps aux | grep flag
root      1394  0.0  0.0   6008   264 ?        S    Nov09   0:00 flag7:xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx 1000000
bob       3274  0.0  0.0  12944   940 pts/1    S+   00:55   0:00 grep --color=auto flag
```

### De-compress and get flag 8.

.tar.gzの解凍方法がわかるか？って問題です。
bobのホームディレクトリにflag8.tar.gzがあるのを確認します。

```bash
bob@ip-10-10-14-129:~$ ls
Desktop  Documents  Downloads  flag13  flag21.php  flag2.txt  flag8.tar.gz  Music  Pictures  Public  Templates  Videos
```

最悪、「tar.gz 解凍」とかで検索すると出てくるので、それで対応でもいいですね。

```bash
bob@ip-10-10-14-129:~$ tar -zxvf flag8.tar.gz 
flag8.txt
```

### By look in your hosts file, locate and retrieve flag 9.

Linuxのhostsファイルは/etc/hostsにあります。
確認してみると、127.0.0.1にflagと思わしき文字列が追加されていたのでそれでflagゲットです。

```bash
bob@ip-10-10-14-129:~$ cat /etc/hosts
127.0.0.1 localhost

# The following lines are desirable for IPv6 capable hosts
::1 ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
ff02::3 ip6-allhosts

127.0.0.1	xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx.com
```

### Find all other users on the system. What is flag 10.

Linuxシステムでのすべてのユーザは/etc/passwdに記載されています。
確認してみたところ変な名前のユーザがいたため、そちらがフラグになります。

```bash
bob@ip-10-10-14-129:~$ cat /etc/passwd
(中略)
xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx:x:1002:1002:,,,:/home/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx:/bin/bash
(中略)
```

## Task 3 Linux Functionality

### Run the command flag11. Locate where your command alias are stored and get flag 11.

flag11コマンドを実行してみろとあったので、とりあえず実行。

```bash
bob@ip-10-10-14-129:~$ flag11
You need to look where the alias are created...
```

エイリアスの話になってきたので、.bashrcを確認してみてflagゲット。

```bash
bob@ip-10-10-14-129:~$ cat .bashrc 
(中略)

#custom alias
alias flag11='echo "You need to look where the alias are created..."' #xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx

(中略)
```

### Flag12 is located were MOTD's are usually found on an Ubuntu OS. What is flag12?

MOTD'sってなんだ？と思い、とりあえず検索。
すると、ログインした際に表示されるメッセージだということがわかりました。

manページやgoogle先生の力を借りて、表示するメッセージがどこに保存されているのかを確認し、grepしちゃいました。

```bash
bob@ip-10-10-14-129:~$ cat /etc/update-motd.d/* | grep Flag
# Flag12: xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

### Find the difference between two script files to find flag 13.

２つのファイルの相違点を確認しろってことでしたので、diffコマンドで一発でした。

```bash
bob@ip-10-10-14-129:~$ cd flag13/
bob@ip-10-10-14-129:~/flag13$ ls
script1  script2
bob@ip-10-10-14-129:~/flag13$ diff script1 script2 
```

```diff
2437c2437
< Lightoller sees Smith walking stiffly toward him and quickly goes to him. He yells into the Captain's ear, through cupped hands, over the roar of the steam... 
---
> Lightoller sees xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx Smith walking stiffly toward him and quickly goes to him. He yells into the Captain's ear, through cupped hands, over the roar of the steam... 
```

### Where on the file system are logs typically stored? Find flag 14.

Linuxでは、ログファイルは基本的に/var/logにあります。
確認してみたところ、それっぽいファイル`flagtourteen.txt`(Typo or grep避け)がありました。

```bash
bob@ip-10-10-14-129:~$ ls /var/log/
alternatives.log    apt            btmp                   cups          flagtourteen.txt  gpu-manager.log  kern.log.2.gz  speech-dispatcher  syslog.3.gz          Xorg.0.log
alternatives.log.1  auth.log       btmp.1                 dist-upgrade  fontconfig.log    hp               lastlog        syslog             unattended-upgrades  Xorg.0.log.old
amazon              auth.log.1     cloud-init.log         dpkg.log      fsck              kern.log         lxd            syslog.1           wtmp                 xrdp-sesman.log
apache2             auth.log.2.gz  cloud-init-output.log  dpkg.log.1    gdm3              kern.log.1       mysql          syslog.2.gz        wtmp.1
```

### Find flag 15.

> Can you find information about the system, such as the kernel version etc.

flag15の設問の前にヒントが記されていました。
自分は最初os-releaseを確認したのですが見つからず、侵入しているマシンがubuntuであったことからlsb-releaseを確認したところflagをゲットすることができました。

```bash
bob@ip-10-10-14-129:~$ cat /etc/lsb-release 
FLAG_15=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
DISTRIB_ID=Ubuntu
DISTRIB_RELEASE=16.04
DISTRIB_CODENAME=xenial
DISTRIB_DESCRIPTION="Ubuntu 16.04.5 LTS"
```

### Flag 16 lies within another system mount.

自分が普段マウントする時は/mntにマウントしていたため、そちらを確認していたのですが、/media/にありましたね。
考え方は、マウントするときと同じです。

```bash
bob@ip-10-10-14-129:~$ cd /media/
bob@ip-10-10-14-129:/media$ ls
f
bob@ip-10-10-14-129:/media$ cd f/l/a/g/1/6/is/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx/
```

### Login to alice's account and get flag 17. Her password is TryHackMe123

今度はaliceにログインし、そこからflagをゲットするみたいです。
aliceのホームディレクトリに移動し、flagをゲットしました。

```bash
bob@ip-10-10-14-129:~$ su alice -
Password: 
bash: cannot set terminal process group (-1): Inappropriate ioctl for device
bash: no job control in this shell
alice@ip-10-10-14-129:/home/bob$ cd
alice@ip-10-10-14-129:~$ ls
flag17  flag19  flag20  flag22  flag23  flag32.mp3
alice@ip-10-10-14-129:~$ cat flag17 
xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

### Find the hidden flag 18.

hidden fileときたので、適当にホームディレクトリ配下をすべて表示させたらありました。

```bash
alice@ip-10-10-14-129:~$ ls -al
total 172
drwxr-xr-x 4 alice alice  4096 Feb 20  2019 .
drwxr-xr-x 6 root  root   4096 Feb 20  2019 ..
-rw------- 1 alice alice   518 Mar  7  2019 .bash_history
-rw-r--r-- 1 alice alice   220 Feb 18  2019 .bash_logout
-rw-r--r-- 1 alice alice  3771 Feb 18  2019 .bashrc
drwx------ 2 alice alice  4096 Feb 18  2019 .cache
-rw-rw-r-- 1 alice alice    33 Feb 18  2019 flag17
-rw-rw-r-- 1 alice alice    33 Feb 18  2019 .flag18
-rw-rw-r-- 1 alice alice 99001 Feb 19  2019 flag19
-rw-rw-r-- 1 alice alice    45 Feb 19  2019 flag20
-rw-rw-r-- 1 alice alice    96 Feb 19  2019 flag22
-rw-rw-r-- 1 alice alice    33 Feb 19  2019 flag23
-rw-rw-r-- 1 alice alice 10560 Feb 19  2019 flag32.mp3
-rw------- 1 alice alice    32 Feb 19  2019 .lesshst
-rw-r--r-- 1 alice alice   655 Feb 18  2019 .profile
drw-r--r-- 2 alice alice  4096 Mar  7  2019 .ssh
-rw------- 1 alice alice  3075 Feb 19  2019 .viminfo
alice@ip-10-10-14-129:~$ cat .flag18 
xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

### Read the 2345th line of the file that contains flag 19.

flag19の2345行目を読めとのことでしたので、-nオプションをつけて行数を表示し、そこからgrepしちゃいました。
2141行目が表示されているのは、内容に2345という文字列が含まれているからです。

また、Hintにはsedコマンドを使えと書いてあったのですが、自分はこの方法で溶けちゃいました。(sedコマンドイマイチよくわからない)

```bash
alice@ip-10-10-14-129:~$ cat -n flag19 | grep 2345
  2141	ac01e02345063d870cbf2aa7ee1077e6
  2345	xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

# 終わりに

今回は、Try Hack MeのLinux ChallengesをTask3までとき終わることができました。
ブログにWriteUPを書くため、作業ログを取りながら解いていく形になり、結果としてTask3を終えるまで合計2時間くらいかかってしまいました。

普段からLinuxを使っている方や、Linuxの操作に慣れている方なら簡単に解くことができると思いますし、これからLinuxを学んでいく/学びたいと思っている人も前RoomにてLinuxについて学ぶことができるため、復習を兼ねてサクサクっと解いてみることをオススメします。

Task4以降のWriteUPも随時掲載予定です。

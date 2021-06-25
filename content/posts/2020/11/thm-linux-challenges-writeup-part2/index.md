---
title: "【THM: Try Hack Me】Linux Challengesを解いてみた - Part2【WriteUP】"
date: 2020-11-11T08:50:57+09:00
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

## はじめに

[前回](https://blog.yuk1h1ra.me/posts/2020/11/thm-linux-challenges-writeup-part1/)に引き続き、TryHackMeのLinux ChallengesのWriteUPです。
今回は、Task4までの解法になります。

## WriteUP

## Task 4 Data Representation, Strings and Permissions

### Find and retrieve flag 20

まずは、flag20を確認します。

```bash
alice@ip-10-10-239-217:~$ ls
flag17  flag19  flag20  flag22  flag23  flag32.mp3

alice@ip-10-10-239-217:~$ file flag20
flag20: ASCII text

alice@ip-10-10-239-217:~$ cat flag20 
MDJiOWFhYjhhMjk5NzBkYjA4ZWM3N2FlNDI1ZjZlNjg=
```

中身を見るとflagが暗号化されていました。最後の「=」をみて、base64だろうなということで、pythonで適当にデコードして終わりです。

```bash
alice@ip-10-10-239-217:~$ python3
Python 3.5.2 (default, Nov 12 2018, 13:43:14) 
[GCC 5.4.0 20160609] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import base64
>>> print(base64.b64decode(b'MDJiOWFhYjhhMjk5NzBkYjA4ZWM3N2FlNDI1ZjZlNjg=').decode())
xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
>>> 
```

### Inspect the flag21.php file. Find the flag

flag21では、普通にcatをするだけでは見つけられないようになっていました。
自分はvimで開いてしまいましたが、`cat -A`を使う方法もあるそうです。

```bash
alice@ip-10-10-239-217:~$ find / 2>/dev/null | grep flag21.php
/home/bob/flag21.php
alice@ip-10-10-239-217:~$ vim ../bob/flag21.php 
```

### Locate and read flag 22. Its represented as hex

ファイルを確認すると16進で書かれたflagが出てきました。これらをASCIIに変換する必要があります。
(なんとなく、41を見たらA, 61をみたらaだなという感覚を持っておくといいかもしれないです)

```bash
alice@ip-10-10-239-217:~$ xxd -r -p flag22
xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxalice@ip-10-10-239-217:~$ 
```

### Locate, read and reverse flag 23

とても便利な`rev`コマンドというのを調べていて見つけたので、それでflagをゲットしました。

```bash
alice@ip-10-10-239-217:~$ cat flag23 | rev
xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

### Analyse the flag 24 compiled C program. Find a command that might reveal human readable strings when looking in the machine code code

flag24はC言語で書かれコンパイルされた物だそうです。
とりあえず`file`でファイルの確認をしていきます。

```bash
alice@ip-10-10-239-217:/home/garry$ file flag24 
flag24: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 2.6.32, BuildID[sha1]=d88e59a01b68aa0969e59bb68726cd7bf8ded9bf, not stripped
```

実行ファイルだったので、これもまた実行してみます。

```bash
alice@ip-10-10-239-217:/home/garry$ ./flag24 
Nothing to see here!!alice@ip-10-10-239-217:/home/garry$ 
```

「ここにはないよ！」と怒られてしまいました。
CTFで表層解析をする際にとりあえず打っとけ！ということで、`strings`で一発でした。

```bash
alice@ip-10-10-239-217:/home/garry$ strings flag24
```

### Find flag 26 by searching the all files for a string that begins with 4bceb and is 32 characters long

自分は最初以下のコマンドで実行していたのですが、時間がかかりすぎていたり、うまくフラグが取れなかったのでカンニングしちゃいました。

```bash
find / -type f 2>/dev/null | xargs grep -E '^4bceb' 2>/dev/null
```

考え方はあっていたのですが、細かいオプションとかですかね。。。？
そのコマンドを使ったことはあるけど、その場しのぎで使っていたのがダメでしたね。少しずつ覚えていけたらなと思います。

```bash
alice@ip-10-10-179-249:~$ find / -xdev -print0 -type f 2>/dev/null | xargs -0 grep -E '^[a-z0-9]{32}$' 2>/dev/null 
Binary file /snap/bin/amazon-ssm-agent.ssm-cli matches
Binary file /var/cache/apt/pkgcache.bin matches
Binary file /var/cache/apt/srcpkgcache.bin matches
/var/cache/apache2/mod_cache_disk/config.json:4bceb76f490b24ed577d704c24d6955d
/var/lib/dbus/machine-id:41da6f3264844132ae79a13a0e1457c3
/var/log/flagtourteen.txt:71c3a8ad9752666275dadf62a93ef393
/lib/terminfo/E/flag5.txt:bd8f33216075e5ba07c9ed41261d1703
/home/bob/.bash_history:9daf3281745c2d75fc6e992ccfdedfcd
```

### Locate and retrieve flag 27, which is owned by the root user

flag27はパーミッション関係でした。管理者権限で一発で終わりですね。

```bash
alice@ip-10-10-179-249:~$ sudo cat /home/flag27 
xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

### Whats the linux kernel version?

カーネルのバージョンの確認方法ですが、自分はオプションで`--kernel-release`を使っちゃいました。
`uname -a`とかでも行けると思います。

```bash
alice@ip-10-10-179-249:~$ uname --kernel-release 
4.4.xxxxxxxxxx
```

### Find the file called flag 29 and do the following operations on it

> 1. Remove all spaces in file.
> 2. Remove all new line spaces.
> 3. Split by comma and get the last element in the split.

これは最初カンマの直前の文字を列挙していくものだと思っていたので、だいぶ変なことをやっていました。
調べていくなかで、`tr`コマンドというのを見つけたので、それも使ってflagをゲットしています。
やっていることは、1,2,3の順番通りにしているので理解はしやすいかと思います。

```bash
alice@ip-10-10-97-2:/home/garry$ cat flag29 | tr -d ' ' | tr -d '\n' | sed 's/,/,\n/g' 
(中略)
meinecaseferrivulputate,
atmelpericulisocurreret.Dicoverearaccusamusuex,
xxxxxxxxxxxxxxxxxxxxx.alice@ip-10-10-97-2:/home/garry$ 
```

## 終わりに

今回はTryHackMeのLinux ChallengesのTask4のWriteUPを書きました。
使ったことはあるけど、詳しくは知らない、なんとなく名前だけ知っているコマンドなどもあり、こうやって使うのか〜と勉強になりながら解いていくことができました。

次回のTask5にて、Linux ChallengesのWriteUPは終わりになります。

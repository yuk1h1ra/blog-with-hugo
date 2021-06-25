---
title: "【THM: Try Hack Me】Linux Challengesを解いてみた - Part3【WriteUP】"
date: 2020-12-04T09:06:29+09:00
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

[Part1](https://blog.yuk1h1ra.me/posts/2020/11/thm-linux-challenges-writeup-part1/)や、[Part2](https://blog.yuk1h1ra.me/posts/2020/11/thm-linux-challenges-writeup-part2/)に続いて第３回になりました。

今回でTryHackMeのLinux ChallengesのWriteUPは終了となります。

それでは解いていきましょう。

## WriteUP

## Task 5 SQL, FTP, Groups and RDP

### Use curl to find flag 30

`curl`を使ってflag30を奪取しましょうとあったので、sshで接続していない端末にて、curlコマンドを実行して終了です。

```bash
$ curl 10.10.230.39
flag30:xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

### Flag 31 is a MySQL database name

MySQLの問題ですね。

とりあえずログインし、データベース一覧を確認したらありました。

```bash
$ mysql -uroot -pxxxxxx

mysql> show databases;
+-------------------------------------------+
| Database                                  |
+-------------------------------------------+
| information_schema                        |
| database_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx |
| mysql                                     |
| performance_schema                        |
| sys                                       |
+-------------------------------------------+
5 rows in set (0.01 sec)
```

### Bonus flag question, get data out of the table from the database you found above

こちらもフラグのあったデータベースから、データを取得して終了です。

```bash
mysql> show tables;
+-----------------------------------------------------+
| Tables_in_database_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx |
+-----------------------------------------------------+
| flags                                               |
+-----------------------------------------------------+
1 row in set (0.00 sec)

mysql> select * from flags;
+----+----------------------------------+
| id | flag                             |
+----+----------------------------------+
|  1 | xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx |
+----+----------------------------------+
1 row in set (0.00 sec)
```

### Using SCP, FileZilla or another FTP client download flag32.mp3 to reveal flag 32

SCPまたは、FileZillaなどを用いてflag32.mp3をダウンロードしろとのこと。

flag32.mp3は`/home/alice/`配下にあったため、そちらを`scp`を用いて取得しました。

```bash
$scp -r alice@10.10.230.39:/home/alice/flag32.mp3 ./flag32.mp3
```

mp3ファイルでしたので、VLCで開いたところフラグの音声が聞こえてきます。

が、自分はVMのオーディオ設定をしていなかったため、そこは注意が必要です。

### Flag 33 is located where your personal $PATH's are stored

Linuxの基本的な内容を説いている問題です。
個人のPATHをどこに記述するかの話ですね。

zshなんかだと、`.zprofile`とかにもなりますが、今回は`.profile`を確認いたしました。

```bash
$ find /home -type f -name .profile 2>/dev/null
/home/bob/.profile
/home/ubuntu/.profile
/home/garry/.profile
/home/alice/.profile

cat /home/bob/.profile
#Flag 33: xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

### Switch your account back to bob. Using system variables, what is flag34?

環境変数を問う問題ですね。アカウントをbobに変更後、`echo`コマンドでflag34を確認しました。
flag34が記述されている場所は`/etc/environment`に記述されていました。

```bash
$ su bob
Password: 

$ echo $flag34
xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

### Look at all groups created on the system. What is flag 35?

ユーザの場合は`/etc/passwd`なんかで確認できるのですが、今回はgroupsということで、`/etc/group`を確認しました。

```bash
$ cat /etc/group
(中略)

flag35_xxxxxxx: x :1005:

(中略)
```

### Find the user which is apart of the "hacker" group and read flag 36

先程、`/etc/group`で確認したところ、hackerグループに属しているのはbobでした。

なので、aliceのまま確認しようとしても見れないかと思います。

flag自体は、findのオプションにて、hackerグループに属しているものを探しました。

```bash
bob@ip-10-10-230-39:~$ find / -group hacker 2>/dev/null
/etc/flag36
bob@ip-10-10-230-39:~$ cat /etc/flag36
xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

## おわりに

これで、TryHackMeのLinux Challengesのすべての問題のWriteUPが終了しました。

基本的なLinuxコマンドを知っている人や、学んだ人ならGoogleを駆使しながら解ける問題ばかりですが、これでCTFとはどんなものかを慣れていけると思います。

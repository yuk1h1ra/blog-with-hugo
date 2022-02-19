---
title: "【THM: TryHackMe】Advent of Cyber 2を解いてみた【Day13】"
date: 2022-02-19T13:43:23+09:00
description:
draft: false
hideToc: false
enableToc: true
enableTocContent: false
tocPosition: inner
tocLevels: ["h2", "h3", "h4"]
tags:
- TryHackMe
- WriteUP
categories:
- WriteUP
series:
- Advent of Cyber 2
image: /posts/2021/08/img/advent-of-cyber-2-thumbnail.png
---

## はじめに

今回はJohn Hammondさんが作成した問題を問いていきます。

{{< notice warning "警告" >}}
本記事はすべてのセキュリティに携わる人に向けて執筆しており、クラッキングを推奨するものではありません。本記事の手法を用いて、許可された環境以外で実行することは絶対にやめてください。
{{< /notice >}}

## Coal for Christmas

### What old, deprecated protocol and service is running?

> どのような古い、非推奨のプロトコルやサービスが動作していますか？

まずは、ポートスキャンをしてなんのポートが空いているか確認します。

```bash
$ nmap 10.10.xxx.xxx
Starting Nmap 7.92 ( https://nmap.org ) at 2022-02-19 13:48 JST
Nmap scan report for 10.10.XX.XXX
Host is up (0.33s latency).
Not shown: 997 closed tcp ports (conn-refused)
PORT    STATE SERVICE
22/tcp  open  ssh
23/tcp  open  telnet
111/tcp open  rpcbind
```

この中で、非推奨なプロトコルは **telnet** です。

telnetはすべての通信を暗号化せず平分のまま送信するため、現在では非推奨のプロトコルになります。

### What credential was left for you?

> どのようなクレデンシャルが残されていたのでしょうか？

telnetで通信を確立します。

```bash
telnet 10.10.xxx.xxx 23
```

アクセスすると、username/passwordが表示されています。

そのまま、パスワードをflagにいれて終了です。

### What distribution of Linux and version number is this server running?

> このサーバーで動作しているLinuxのディストリビューションとバージョン番号を教えてください。

サーバーのバージョンを確認します。

```bash
$ uname -a
```

Ubuntuが使われていることが分かります。

Linuxディストリビューションを確認する場合、 `/etc/*release` を確認する方法もあります。

```bash
$ cat /etc/*release
```

これで、バージョン情報も確認できます。

### Who got here first?

> 誰が最初にここに来ましたか？

文中に `cat cookies_and_milk.txt` を確認しろとあるので、確認します。

```bash
cat cookies_and_milk.txt
```

丁寧に名前が書いてあるので、そちらを入力します。

### What is the verbatim syntax you can use to compile, taken from the real C source code comments?

> 実際のC言語ソースコードのコメントから引用した、コンパイルに使える逐語的な構文とは？

`cookies_and_milk.txt` はDirtyCowコードを修正されたCソースコードのようです。

`dirty.c` をマシン上に作成し、コンパイルすることが目的です。

gccを使うときのオプションが今回のflagになっていますが、dirty.cに詳しく書いてあるので、そちらを入力します。

### What "new" username was created, with the default operations of the real C source code?

> 本物のC言語ソースコードのデフォルトの操作で、どのような「新しい」ユーザー名が作成されたのでしょうか。

デフォルトのユーザーはソースコード上に書いてあるので、そちらを入力します。

### What is the MD5 hash output?

> MD5ハッシュの出力は？

さきほどコンパイルしたdirtyを実行し、新しいユーザーを作成します。

そして、その作成されたユーザーでログインをします。

ログインができてしまうことまでが本題ですので、残りはflagの解答です。

本文中にcoalを置き去りにした後、 `tree | md5sum`を実行してくださいとあるので、そのようにします。

```bash
$ su <username>
$ cd /root
$ touch coal
$ tree | md5sum
```

これで出てきたハッシュ値を入力して終了です。

## おわりに

問題自体はStep by Stepになっているので、途中でつまるようなことはあまりないかと思います。

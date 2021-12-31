---
title: "【THM: TryHackMe】Advent of Cyber 2を解いてみた【Day11】"
date: 2021-12-31T11:48:55+09:00
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

今回は特権昇格についての章になります。


{{< notice warning "警告" >}}
本記事は全てのセキュリティに携わる人に向けて執筆しており、クラッキングを推奨するものではありません。本記事の手法を用いて、許可された環境以外で実行することは絶対にやめてください。
{{< /notice >}}

## The Rogue Gnome

### What type of privilege escalation involves using a user account to execute commands as an administrator?

> 管理者としてコマンドを実行するためにユーザーアカウントを使用する場合、どのような種類の特権昇格が必要ですか？

特権昇格には主に2つの種類があります。
1. Horizontal Privilege Escalation
1. Vertical Privilege Escalation

Holizontal Privilage Escalationでは、自分と同等の権限を持つ別のユーザのリソースにアクセスするために脆弱性を悪用します。
この2つのユーザの違いはアクセスできるデータにあるため、権限を上位に移動させているわけではありません。

Vertical Privilage Escalationでは、管理者のような高い権限を持つアカウントとして実行するために脆弱性を悪用します。

今回の問題では、管理者としてコマンドを実行するための特権昇格の種類を聞いているため、Vertical Privilege Escalationが正しいです。

### What is the name of the file that contains a list of users who are a part of the sudo group?

> sudoグループに属しているユーザーのリストを含むファイルの名前は何ですか？

sudoはユーザが別のユーザの権限レベルでプログラムを実行するためのコマンドです。
sudoを利用できるユーザは「sudoers」と呼ばれ、`/etc/sudoers`にリストアップされています。

### What are the contents of the file located at /root/flag.txt?

> /root/flag.txtにあるファイルの内容は何ですか？

まずは`ssh`でマシンに接続します。
パスワードは本文中に載っているものを使用します。

```bash
ssh cmnatic@10.10.xxx.xxx
```

SUID権限が設定されている実行可能ファイルを列挙します。

```bash
find / -perm -u=s -type f 2>/dev/null
```

これでSUID権限が設定されているファイルを洗い出すことができました。

ここで、[GTFOBins](https://gtfobins.github.io/#+suid)を使ってSUIDを用いて特権昇格できるものがないか確認していきます。

先程のSUIDがセットされているものと見比べると、`/bin/bash`を悪用することができそうだとあたりをつけます。

sshで接続したマシン上で以下のコマンドを実行します。

```bash
/bin/bash -p
```

無事rootユーザになることができたので、flag.txtを読み取って終了です。

## おわりに

今回は特権昇格について学びました。
マシンを攻略していく中で特権昇格は必須事項となってくるので、今回の回答をもとに他のマシン攻略もして、GTFObinsの使い方や列挙の方法を少しずつ学んでいけるといいと思います。

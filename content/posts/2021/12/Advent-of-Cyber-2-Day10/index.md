---
title: "【THM: TryHackMe】Advent of Cyber 2を解いてみた【Day10】"
date: 2021-12-31T10:43:51+09:00
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
- smb
categories:
- WriteUP
series:
- Advent of Cyber 2
image: /posts/2021/08/img/advent-of-cyber-2-thumbnail.png
---

## はじめに

今回はsmbとはなにか、どのように使われているのかの章になります。
ネットワークファイル共有プロトコルについて学び、脆弱なsmbサーバの列挙について学んでいきます。

{{< notice warning "警告" >}}
本記事は全てのセキュリティに携わる人に向けて執筆しており、クラッキングを推奨するものではありません。本記事の手法を用いて、許可された環境以外で実行することは絶対にやめてください。
{{< /notice >}}

## Don't be sElfish!

### Using enum4linux, how many users are there on the Samba server?

> enum4linuxを使って、Sambaサーバーに何人のユーザーがいますか？

enum4linuxとは、WindowsおよびSambaサーバから情報を列挙するためのツールです。
ヘルプを見てみると、ユーザの列挙についてのオプションがあるため、そちらを使用します。

```bash
enum4linux -U 10.10.xxx.xxx
```

### Now how many "shares" are there on the Samba server?

> さて、Sambaサーバーにはいくつの「shares」があるのでしょうか？

こちらもヘルプから、sharelistを取得できるオプションがあるため、そちらを使用します。

```bash
enum4linux -S 10.10.xxx.xxx
```

### Use smbclient to try to login to the shares on the Samba server (10.10.125.106). What share doesn't require a password?

> smbclientを使って、Sambaサーバーのshareにログインしてみてください。どのshareがパスワードを必要としませんか?

先程列挙したsharesにアクセスしていきます。

```bash
sudo su -
smbclient //10.10.xxx.xxx/tbfc-xxx
```

パスワードを必要とせずにアクセスできたら成功です。

### Log in to this share, what directory did ElfMcSkidy leave for Santa?

> このshareにログインします。ElfMcSkidyはサンタのためにどんなディレクトリを残しましたか？

ログインできていたら、以下のコマンドでディレクトリの中身を確認して終了です。

```bash
smb: \> ls
```

## おわりに

今回はsmbとはなにか、smbclientやenum4linuxの使い方を軽く触りました。
最後の問題ではログインした後のファイルのダウンロード等もできるので、コマンドの使い方やsmbに慣れるという意味でも試してみることをおすすめします。

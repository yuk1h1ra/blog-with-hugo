---
title: "【THM: TryHackMe】Advent of Cyber 2を解いてみた【Day8】"
date: 2021-12-30T10:25:50+09:00
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
- nmap
categories:
- WriteUP
series:
- Advent of Cyber 2
image: /posts/2021/08/img/advent-of-cyber-2-thumbnail.png
---

## はじめに

今回はnmapを使って情報収集する方法を学ぶ章になっています。

{{< notice warning "警告" >}}
本記事は全てのセキュリティに携わる人に向けて執筆しており、クラッキングを推奨するものではありません。本記事の手法を用いて、許可された環境以外で実行することは絶対にやめてください。
{{< /notice >}}

## What's Under the Christmas Tree?

### When was Snort created?

> Snortが作られたのはいつですか？

SnortはOSSの侵入検知・防止サービス(IPS/IDS)です。
ルールを定義し、それらに一致するパケットを検索し、ユーザーにアラートを生成します。

{{<blogcard title="Snort" url="https://www.snort.org/">}}

回答はググればすぐに出てくるので、省略します。

### Using Nmap on 10.10.xxx.xxx , what are the port numbers of the three services running?

> 10.10.xxx.xxxでNmapを使用すると、実行中の3つのサービスのポート番号は何ですか？

それでは`nmap`を使っていきましょう。

```bash
nmap 10.10.xxx.xxx
```

立ち上げたインスタンスのIPアドレスを指定してあげると、そのインスタンスで開いているポートが何であるかわかります。

### Use Nmap to determine the name of the Linux distribution that is running, what is reported as the most likely distribution to be running?

> Nmapを使用して、実行されているLinuxディストリビューションの名前を判別します。実行されている可能性が最も高いディストリビューションとして報告されているものは何ですか？

nmapのオプションには様々なものがあります。色々試してみるのがいいと思います。
一応flagが得られるコマンドを以下に記します。

```bash
sudo nmap -A 10.10.xxx.xxx
```

### Use Nmap's Network Scripting Engine (NSE) to retrieve the "HTTP-TITLE" of the webserver. Based on the value returned, what do we think this website might be used for?

> Nmapのネットワークスクリプトエンジン（NSE）を使用して、Webサーバーの「HTTP-TITLE」を取得します。返された値に基づいて、このWebサイトは何に使用されると思いますか？

HTTP-TITLEというスクリプトがあるので、そちらを使用します。

{{<blogcard title="HTTP-TITLE" url="https://nmap.org/nsedoc/scripts/http-title.html">}}

ドキュメントにあった一例を参考にコマンドを実行するとflagを得られます。

```bash
nmap -sV -sC 10.10.xxx.xxx
```

## おわりに

今回はポートスキャナであるnmapを使って、ネットワーク上のコンピュータの情報を収集することができました。
ペンテストやネットワーク診断では必ずといっていいほど使わているツールなので、helpをよく読みながら解き進めていくのがいいかと思います。

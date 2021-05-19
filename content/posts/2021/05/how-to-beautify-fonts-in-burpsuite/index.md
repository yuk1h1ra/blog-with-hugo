---
title: "BurpSuiteのガビガビなフォントを綺麗にする方法"
date: 2021-05-19T14:21:38+09:00
description: LinuxでBurpSuiteを使っているとフォントがガビガビで表示されてしまっているため、綺麗なフォントに変更する方法を紹介します。
draft: true
hideToc: false
enableToc: true
enableTocContent: false
tocPosition: inner
tocLevels: ["h2", "h3", "h4"]
tags:
- burpsuite
categories:
- Tools
series:
-
image:
---

普段プロキシツールであるBurpSuiteを使用していますが、そのフォントがあまりにも汚くとてもじゃないが読めたものじゃない状況でした。
今回は、そんなBurpやZAPなど、Java applicationに関するフォントのアンチエイリアスについての解決方法を紹介します。

## 環境

私の現在の環境は以下の通りです。

> OS: Manjaro Linux
> BurpSuite: v2021.3.3
> Java: OpenJDK Runtime Environment (build 15.0.2+7)

## 問題点

BurpSuiteを立ち上げた際に、フォントが以下のように綺麗に表示されておりませんでした。

![Burpsuite-Dashboard](img/2021-05-19-16-24-53.png)

特に、タブの部分や、

![Brupsuite-tab](img/2021-05-19-16-35-11.png)

文字の部分が汚くなっていることが解ると思います。

![Burpsuite-text](img/2021-05-19-16-36-24.png)

## 解決方法

天下のArchWikiに解決方法が乗っていました。

{{< blogcard title="Java 実行環境のフォント - ArchWiki" url="https://wiki.archlinux.jp/index.php/Java_%E5%AE%9F%E8%A1%8C%E7%92%B0%E5%A2%83%E3%81%AE%E3%83%95%E3%82%A9%E3%83%B3%E3%83%88#.E3.82.A2.E3.83.B3.E3.83.81.E3.82.A8.E3.82.A4.E3.83.AA.E3.82.A2.E3.82.B9" >}}

フォントのアンチエイリアスについては、`/etc/environment`に追記するだけで済みそうです。

```$:.bash
sudo vim /etc/environment
```

```
#
# This file is parsed by pam_env module
#
# Syntax: simple "KEY=VAL" pairs on separate lines
#
_JAVA_OPTIONS='-Dawt.useSystemAAFontSettings=on'
```

この記述をした後、再起動すれば設定が反映されています。

YouTubeにも同様にして解決している動画があったので添付しておきます。

{{< youtube o_fPgmaCNQg >}}

## 設定反映後

設定反映後の状態を見てみましょう

![](img/2021-05-19-16-52-26.png)

先程よりも綺麗に表示されていることが分かりますでしょうか？

気になっていたタブやフォントの拡大画像はこちらです。

![](img/2021-05-19-16-53-40.png)

![](img/2021-05-19-16-54-42.png)

無事に綺麗なフォントで表示することができました。

## おわりに

普段使っているツールなだけに、アンチエイリアスのかかっていないフォントではやる気が削がれたり、見にくく目が疲れてしまったりします。
こういった少しの手間だけでこんなにも綺麗になり、変なところでストレスを抱えず生産性の向上につながっていくと思います。

最後まで読んでいただきありがとうございます。この記事が誰かの役になったのなら幸いです。
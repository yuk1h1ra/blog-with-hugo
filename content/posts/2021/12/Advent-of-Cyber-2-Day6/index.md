---
title: "【THM: TryHackMe】Advent of Cyber 2を解いてみた【Day6】"
date: 2021-12-29T13:01:00+09:00
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
- XSS
- OWASP ZAP
categories:
- WriteUP
series:
- Advent of Cyber 2
image: /posts/2021/08/img/advent-of-cyber-2-thumbnail.png
---

## はじめに

今回はOWASP ZAPを使ってXSSを発見する章になります。
基本的には本文中に解説が乗っており、OWASP ZAPを使ったことがない人を対象としている感じがしました。

{{< notice warning "警告" >}}
本記事は全てのセキュリティに携わる人に向けて執筆しており、クラッキングを推奨するものではありません。本記事の手法を用いて、許可された環境以外で実行することは絶対にやめてください。
{{< /notice >}}

## Be careful with what you wish on a Christmas night

### What vulnerability type was used to exploit the application?

> アプリケーションを悪用するために使用された脆弱性の種類は何ですか？

こちらは前の文章を読んでいれば解ける問題です。

### What query string can be abused to craft a reflected XSS?

> どのようなクエリー文字列を悪用して、反射型XSSを作成することができますか？

反射型XSSの基本的な部分です

### Run a ZAP (zaproxy) automated scan on the target. How many XSS alerts are in the scan?

> ターゲットに対してZAP（zaproxy）自動スキャンを実行します。スキャンに含まれるXSSアラートは何件ですか？

こちらも説明文を読めば解ける問題でした。

## おわりに

今回は本文を読めばすぐに終わってしまうラッキー問題でした。
Webアプリケーションの脆弱性診断やBugBountyではプロキシツールを多用するため、使ったことのない人はツールの使い方を学べるいい機会だと感じました。

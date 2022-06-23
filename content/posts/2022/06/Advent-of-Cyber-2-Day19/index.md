---
title: "【THM: TryHackMe】Advent of Cyber 2を解いてみた【Day19】"
date: 2022-06-23T19:44:36+09:00
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

TryHackMeのAdvent of Cyber 2 Day19のWriteUPです。

{{< blogcard title="Advent of Cyber 2" url="https://tryhackme.com/room/adventofcyber2" >}}

今回はWebアプリケーションの「Server-Side Request Forgery(SSRF)」の章になります。

{{< notice warning "警告" >}}
本記事はすべてのセキュリティに携わる人に向けて執筆しており、クラッキングを推奨するものではありません。本記事の手法を用いて、許可された環境以外で実行することは絶対にやめてください。
{{< /notice >}}

## The Naughty or Nice List

最初にアクセスすると、以下のようなページが表示されます。

![TOPページ](img/2022-06-23-20-30-00.png)

テキストボックスに文字列を入力後、「Search」をクリックすると、「`http://10.10.xx.xx/?proxy=http%3A%2F%2Flist.hohoho%3A8080%2Fsearch.php%3Fname%3Dtest`」のようなURLにGETリクエストが飛びます。

![検索](img/2022-06-23-20-32-16.png)

クエリーパラメーターにURLの形式があるため、SSRFができるのではないかとあたりをつけます。SSRFの章ですしね笑

「proxy」パラメーターの値はURL decodeしてあげると「`http://list.hohoho:8080/search.php?name=test`」であることがわかります。

では、最初にクエリーパラメーターを「`http://list.hohoho:8080/`」に変更してアクセスしてみます。

![8080](img/2022-06-23-20-48-40.png)

メッセージには「Not Found. The requested URL was not found on this server.」と表示され、404エラーと推測される文字列が出てきました。

次に、「`http://list.hohoho:80`」にアクセスしてみます。

![80](img/2022-06-23-20-51-59.png)

「Failed to connect to list.hohoho port 80: Connection refused」と表示され、ポート80が空いていないことがわかります。

また、「`http://list.hohoho:22`」にアクセスしてみます。

![22](img/2022-06-23-20-55-29.png)

エラーメッセージが変わり、「Recv failure: Connection reset by peer」と表示されるようになりました。これにより、22番のポートが開いていることが分かります。

つまり、Webアプリケーションから内部の「list.hohoho」の空いているポートが分かってしまっている状態です。

このように、本来アクセス出来ないサーバーに対して、Webアプリケーションを通してアクセス出来る攻撃をSSRF攻撃です。

### What is Santa's password?

次は「http://localhost」にアクセスを試みます。

![](img/2022-06-23-21-14-25.png)

メッセージには、「Your search has been blocked by our security team.」と表示され、アクセスが出来ないようになっています。list.hohohoが含まれているかどうかをチェックしているみたいです。

本文中に、「`localtest.me`」は全てのサブドメインを「127.0.0.1」に名前解決しますとあるため、クエリーパラメーターに「http://list.hohoho.localtest.me」を設定してアクセスしてあげます。

![](img/2022-06-23-21-10-02.png)

無事、パスワードと思しき文字列が表示されていることが分かります。

### What is the challenge flag?

Santaのパスワードが分かったので、Adminからログインして終了です。

## おわりに

今回はWebアプリケーションの脆弱性SSRFを用いて内部の情報を得るという問題でした。

時間としては、1時間半から2時間程度で終了しました。

Webアプリケーションの脆弱性診断をやっている方や、少しセキュリティについて詳しい方であれば、「あぁ、あれね」と比較的わかりやすい問題でした。

また、全くセキュリティがわからない人でも、Walktrhough形式になっているので、詰まることなく進められると思います。

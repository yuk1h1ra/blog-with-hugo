---
title: "【THM: TryHackMe】Advent of Cyber 2を解いてみた【Day18】"
date: 2022-03-13T10:33:09+09:00
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

TryHackMeのAdvent of Cyber 2 Day18のWriteUPです。

{{< blogcard title="Advent of Cyber 2" url="https://tryhackme.com/room/adventofcyber2" >}}

今回もリバース・エンジニアリングの章になります。

{{< notice warning "警告" >}}
本記事はすべてのセキュリティに携わる人に向けて執筆しており、クラッキングを推奨するものではありません。本記事の手法を用いて、許可された環境以外で実行することは絶対にやめてください。
{{< /notice >}}

## The Bits of Christmas

まず、今回のターゲットとなるTBFC_APPがどんなアプリケーションかを軽く確認してみます。

![TBFC_APP](img/2022-03-13-10-54-37.png)

アプリを起動すると、パスワード入力を求められています。

### What is Santa's password?

> サンタのパスワードは何ですか？

今回は.NETで作成されたアプリケーションですので、[ILSpy](https://github.com/icsharpcode/ILSpy)を用いて、デコンパイルしていきます。

ILSpyで開いた結果が以下のようになります。

![ILSpy](img/2022-03-13-11-01-42.png)

アプリケーションを確認した際にPasswordという文字列が確認できたので、検索してみます。

![find Password](img/2022-03-13-11-03-55.png)

それっぽいところが発見できました。

コードを軽く読んでみると、入力された文字列と既に設定されている文字列を比較しているようなコードが確認できます。

サンタのパスワードは、比較元の文字列を入力してあげれば終了です。

### Now that you've retrieved this password, try to login...What is the flag?

> このパスワードを取得したので、ログインしてみてください...フラグは何ですか？

先程得たパスワードをアプリケーションで入力してあげると、flagを取得できます。

![flag](img/2022-03-13-11-11-00.png)

アプリケーション上で確認してもいいですし、コード上からも確認できます。

先程のコードで、文字列を比較したあと正しいパスワードが入力されたあとに`Messagebox.Show`でメッセージボックスに表示される文字列が記述されています。

![find Password](img/2022-03-13-11-03-55.png)

ここを読み解くことで、flagを取得できます。

## おわりに

今回はMicrosoft .NETアプリケーションのリバース・エンジニアリングについてでした。

自分も初めて.NETアプリケーションのデコンパイルをしたので、分からないことも多かったのですが、問題自体は簡単だったので、今までの知識で解くことが出来ました。

CTFでリバース・エンジニアリングの問題が出たときに、常にELFファイルではないということ、その取っ掛かりとしてこの問題はちょうどいいのではないかと思います。

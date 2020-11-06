---
title: "【Hacker101】Micro-CMS V1を解いてみた【WriteUP】"
date: 2020-06-02T12:57:41+09:00
description:
draft: false
hideToc: false
enableToc: true
enableTocContent: false
tocPosition: inner
tocLevels: ["h2", "h3", "h4"]
tags:
- writeup
- hacker101
- ctf
categories:
- CTF
series:
-
image:
---

**注意** CTFの解法が以下に載っています。自力で解きたい方は解法を見る前にブラウザバックしてください。

## 概要

Micro-CMS v1ではMarkdown形式で記述されたものを作成、編集、閲覧などができるサービスでした。
最初にTesting, Markdown Testというページがありそれらの編集などもできるようなサービスでした。

## Flag0

Create a new pageから試しにページを作成してみると、作成されたページのindexが11であることに気が付きます。

![create-new-post](./img/01-create-post-index.png)

Testing, Markdown Testは1,2と連番だったのにいきなり11になるのはおかしいと感じ、3~10までindex番号を変更して見てみると、indexが"6"のときだけ403 Forbidden Errorが出ました。

![page-6](./img/02-page-6.png)

直接参照する際には権限周りで怒られていましたが、editページに飛んで見ると見事Flagを回収することができました。

![edit-page-6](./img/03-edit-page-6.png)

## Flag1

Flag1では、編集ページの入力値の改ざんを行うことでFlagを回収することができました。

入力値はFormの値だけではなく、URLのパスからも意図しない入力値が送られることがあります。

editページでは数字が入力されるのを期待していましたが、数値以外のもの、シングルクオート(')を与えてあげることでFlagを取得することができました。

![edit-page-singlequote](./img/04-edit-page-singlequote.png)

## Flag2

みんな大好きXSSです。

入力を表示させるようなページがある場合はみんな試すと思いますので脳死で`<scirpt>alert('XSS');</scirpt>`を入力しましょう。

![xss-text](./img/05-xss-text.png)

しかしXSSが表示されるところは詳細ページでは起こりません。

![xss-not-work](./img/06-xss-not-work.png)

入力されたものが影響を与えるのは詳細ページだけではありません。

今回はTitleにXSSのスクリプトタグを入力してみましょう。

![xss-title](./img/07-xss-title.png)

そして、Homeに戻ってみると、無事Flagが回収することができました。

![xss-work](./img/08-xss-work.png)

## Flag3

XSSはscriptタグで囲まれた場所以外にも発生します。

今回はbuttonからクリックした際にXSSが発生するようにしました。

![onclick](./img/09-onclick.png)

Webページによっては"script"という文字列を置換してスクリプトを実行させないようにするようなサイトもありますが、onclickを用いることでscirptという文字列を使用せずにXSSを発生させることができます。

![button-source](./img/10-button-source.png)

こちらも無事Flagを回収することができました。


## まとめ

比較的基本に忠実な問題ばかりでした。
CTF初学者や始めてみたい人にとってWriteUPを見ながらでもいいので解いてみて解き方の感覚を掴むのにもってこいの問題だと思います。

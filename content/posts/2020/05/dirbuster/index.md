---
title: "Webアプリ上のディレクトリ/ファイル名を総当りで検索する「DirBuster」の紹介"
date: 2020-05-27T12:23:41+09:00
description:
draft: false
hideToc: false
enableToc: true
enableTocContent: false
tocPosition: inner
tocLevels: ["h2", "h3", "h4"]
tags:
- bugbounty
- tools
- how-to
categories:
- BugBounty
series:
-
image: 
---

![dirbuster](./img/01-dirbuster.png)

## DirBusterとは

DirBusterは、Web/アプリケーションサーバー上のディレクトリとファイル名を総当りで検索するアプリケーションです。Webアプリケーションにはリンクから辿れない数多くのページやアプリケーションが隠されています。DirBusterはこれらを総当りで見つけるためのツールです。

DirBusterの簡単な使い方を次に紹介します。

## Step1 DirBusterの起動

Parrot Security OSではデフォルトでインストールされているため、そちらを起動します。

![start](./img/02-start.png)

## Step2 URLを指定

対象のWebアプリケーションのURLを指定してあげます。
今回は、Hacker101のPetshopProを対象にしていきます。

![petshop](./img/03-petshop.png)

今回対象のWebアプリケーションは`http://35.227.24.107/02dcccde7d/`となるため、それを入力します。

![url](./img/04-url.png)

## Step3 リストの選択

Scanする際に単純なBruteForceも可能ですが、あらかじめ用意されたリストを使用してそのリストをぶん回していく方法があります。今回はそのリストを使用します。

DirBusterには `/usr/share/dirbuster/wordlists/` の中にいくつかのリストがあるためそちらを使用します

![directory-list](./img/05-directory-list.png)

## Step4 実行

準備ができたらStartを押して実行します。

![running](./img/06-running.png)

設定の際にFile extensionやUse Blank ExtensionにチェックをいれることでScanする対象を選択することができます。

今回は時間の短縮のためScanの必要のないディレクトリは停止させたりしています。

## Step５ 結果

タブのResultに今までのスキャン結果が出ています。
今回のWebアプリケーションでは管理画面であるloginページは通常ではたどり着くことができませんでしたが、DirBusterを用いてスキャンすることで見事見つけることができました。

![login](./img/07-login.png)

## 総括

DirBusterを用いれば秘匿されたWebアプリケーションのディレクトリやファイルの検索を行うことができます。
しかし、BruteForceでの検索となるため、目的のファイルやディレクトリを発見するのに時間がかかることがわかります。
またサービスに多量のリクエストが送られるため、許可された環境下でのみ使用してください。

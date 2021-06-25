---
title: "セキュリティに特化したParrot Security OSをVirtualBoxにインストールするまで"
date: 2020-09-24T13:04:27+09:00
description:
draft: false
hideToc: false
enableToc: true
enableTocContent: false
tocPosition: inner
tocLevels: ["h2", "h3", "h4"]
tags:
- bugbounty
- linux
- setup
categories:
- BugBounty
series:
-
image:
---

![thumbnail](img/01_parrot-security-f063de7e.png)

## Parrot Security OS とは

Parrot Security OSはイタリアで開発が行われている、セキュリティとプライバシーを念頭に置いて設計されたDebianベースのGNU/Linuxです。

ペネトレーションテストから、デジタルフォレンジック、リバースエンジニアリングまで、あらゆる種類のサイバーセキュリティ運用のためのツールが含まれています。

![desktop](img/02_parrot-security-d913f862.png)

## Kali Linuxとの違い

Kali Linuxとどっちがより良いかなどよく比較されます。
基本的に含まれているツール群はKaliにあったらParrotにもあります。

Kaliはペネトレーションテストに特化したOSなのに対し、ParrotはHOME版も出しており、普段使いにもできるような印象があります。

また、「AnonSurf」というすべての通信をTorを用いて通信を行うようにするアプリケーションは、Kaliにはデフォルトでインストールされていないため、その点ではKaliに勝っています。

また、デスクトップ環境にも違いがあります。
Kali LinuxではGnomeを、ParrotではMATEを採用しています。
VirtualBox上で動かす上で、限られたリソースで動作させなくてはいけないため、軽いデスクトップ環境のMATEのほうが無難です。

海外のコミュニティなどでも検索をかけてみましたが、Parrotを好んで使っている、Parrotを勧めているようなコメントもありました。

今までごたごたと語ってきましたが、結局は見た目と好みで選んでいいと思います。

## インストール

### ISOダウンロード

まずはParrotのホームページから、isoをダウンロードしてきてください。

<iframe class="hatenablogcard" style="width:100%;height:155px;margin:15px 0;" title="Parrot OS Dowload Home Edition" src="https://hatenablog-parts.com/embed?url=https://parrotlinux.org/download/" frameborder="0" scrolling="no"></iframe>

> [https://parrotlinux.org/download/](https://parrotlinux.org/download/)

MATE版、KDE版、OVA版がありますが、自分で一から設定したいためOVA版はパス。

![download-page](img/03_parrot-security-c4a5cb01.png)

MATEとKDEですが、ノートPCなどに直接インストールするのであればKDEもありです。
自分も普段使いのManjaroLinuxは、KDEを採用しています。

しかし、VM上に展開するには重い印象があります。

あと、MATE版はとても綺麗に作られていたのですが、KDE版のデスクトップほとんどデフォルトって感じであまり好みではなかったです。

KDE版にはそこまで力をいれていないのかなって印象です。

### VirualBoxのマシン作成

#### 1. 新規作成

新しい仮想マシンを作成します。

![new](img/04_parrot-security-a5a13c6e.png)

#### 2. 名前とオペレーティングシステム

適当に好きな名前をつけます

![name](img/05_parrot-security-63f965e2.png)

#### 3. メモリーサイズ

メモリサイズを設定します。
メモリが2GBとかの化石PCを使ってる人なんてもういないと思うので、4GBとか8GBとか当ててください。

![memory](img/06_parrot-security-590d37b9.png)

#### 4.1 ハードディスク

新しく仮想ハードディスクを選択してください。

![harddisk](img/07_parrot-security-efc6e1ef.png)

#### 4.2 ハードディスクのファイルタイプ

提案されているものそのままで大丈夫です。

![filetype](img/08_parrot-security-649734e0.png)

#### 4.3 物理ハードディスクにあるストレージ

書いてあるとおりです。
今回は可変で作成しますが、固定でもいいです。
ぶっちゃけどちらでもいいです。

![storage](img/09_parrot-security-7b3f5f3f.png)

#### 4.4 ファイルの場所とサイズ

自分がいま使っている状況で20GB程度使用しているため、それ以上は絶対必要です。
自身の空き容量と相談してください。

![size](img/10_parrot-security-691dada3.png)

無事、新しくマシンが作成されました。

![create](img/11_parrot-security-dd98f3fc.png)

次は設定に移ります。

### VirtualBoxのマシン設定

#### 一般

高度タブからクリップボード、ドラッグアンドドロップを双方向にしてください。
後にGuestAdditionsを導入した際に便利になります。

![clipboard](img/12_parrot-security-ecdfa72e.png)

#### システム

チップセットをICH9、ポインティングデバイスをUSBマルチタブレットにしてください。
これが一番最新の設定らしいです。
これが原因で動かないとかはないですが、しておいて損はないです。

![system](img/13_parrot-security-b1d88256.png)

#### プロセッサ

自分はホストの半分のプロセッサ数を割り振ってます。
また、PAE/NXを有効化しておいてください。

![proc](img/14_parrot-security-a274dc4c.png)

#### ディスプレイ

ビデオメモリですが、ディスプレイ数が1の時は128MBが最大です。

![128MB](img/15_parrot-security-028f2850.png)

ビデオメモリを増やしてあげると、256MBまで増えます。
ワザップですね。

![256MB](img/16_parrot-security-e181511d.png)

ちなみに、3Dアクセラレーションにもチェックを入れておいてください。

#### ストレージ

ここからディスクファイルを選択し、Parrotのisoイメージを選択してあげてください。

![storage](img/17_parrot-security-eb2f0eaf.png)

先程空だったところにisoイメージが挿入されていると思います。
Live CD/DVDにチェックをいれます。

![livecd](img/18_parrot-security-70476357.png)

一度OKを押し、設定を終了します。

![ok](img/19_parrot-security-de3deb83.png)

#### Re:スクリーン

このままではスクリーン数が8のままなので、それを1つにしていきます。
スクリーン数を変更してもビデオメモリは256MBのままですね！

![onescreen](img/20_parrot-security-cd0a696b.png)

#### グラフィックコントローラー

起動する前に、グラフィックコントローラを変更します。
この画面のVMSVGAというところをクリックし、VBoxSVGAを選択してあげます。

![vmsvga](img/21_parrot-security-a4e45274.png)

これで起動する準備が整いました。
起動しましょう。

![start](img/22_parrot-security-0ed9a11e.png)

### 仮想マシンの起動

LiveCDが起動します。十字キーを使ってinstallを選択します。

![start](img/23_parrot-security-c9871cb8.png)

GUIでインストールを行いたいため、そちらを選択します。

![gui](img/24_parrot-security-2901b9fd.png)

ここからはマウスが使えるので簡単だと思います。
日本語を選択しましょう。

![language](img/25_parrot-security-f38a9482.png)

警告が出ますが、日本語の翻訳が追いついてなくて英語になる可能性あるよってだけです。
はいを選択していいでしょう。

![yes](img/26_parrot-security-1dd8c9e8.png)

住んでいる地域を選択します。

![location](img/27_parrot-security-4a12beb8.png)

キーボードの設定です。
自分はUSキーなので米国を選択しますが、日本語も選択できます。

![keyboard](img/28_parrot-security-67c012a5.png)

rootのパスワードの設定画面が開きます。

![rootpasswd](img/29_parrot-security-46a0b995.png)

ユーザの設定です。
こちらはスペースや大文字が入っても大丈夫です。

![account](img/30_parrot-security-eced70a2.png)

アカウントのユーザ名です。
これをもとにディレクトリが作成されるため、半角英数字で設定してあげます。

![username](img/31_parrot-security-9eaab4da.png)

パスワードの設定です。
短すぎると警告を受けた気がするので、長い方がいいです。

![password](img/32_parrot-security-2ab9e2b5.png)

ディスクのパーティショニングです。
ディスク全体を使うを選択します。

![disk](img/33_parrot-security-73ab8dfe.png)

こちらで、ディスクがVBoxのものであるか、自分が設定したディスクサイズに近いかどうかを確認してください。
確認が済んだら続けるでOKです。

![vboxdisk](img/34_parrot-security-02dcc53a.png)

すべてのファイルを１つのパーティションにを選択します。

![](img/35_parrot-security-7e4fa54e.png)

![](img/36_parrot-security-10d7ba72.png)

![](img/37_parrot-security-dfcdd2ce.png)

スワップスペースについての警告ですが、8Gもメモリ積んでればスワップはいらないので無視します。
いいえで大丈夫です。

![](img/38_parrot-security-9d3f4041.png)

ディスクをこれで作っていいか確認が来るので、はいを選択します。

![](img/39_parrot-security-cec64da9.png)

ディスクにインストールしています。
コーヒーでも飲んで待ちましょう。

![](img/40_parrot-security-3c74bbba.png)

ブートローダーの設定が出てきました。
OSをブートする際に必要なのではいを選択します。

![](img/41_parrot-security-dc76ac63.png)

VBOXのハードディスクを選択し、続けるで大丈夫です。

![](img/42_parrot-security-2e9d84dd.png)

インストールが完了しました。
続けるを押してください。

![](img/43_parrot-security-55f2659c.png)

しばらく待っていると、再起動が始まりログイン画面が出てきました。
お疲れ様でした。VirtualBoxにインストールが完了しています。

![](img/44_parrot-security-a27a13fc.png)

## パッケージアップグレード

Parrot Security OSではパッケージのアップデートにラッパーコマンドがあります。

端末を開いて以下のコマンドを実行するだけで自動的にパッケージの更新をしてくれます。

```bash
sudo parrot-upgrade
```

![](img/45_parrot-security-f2bbad48.png)

ログインした際に毎回確認をして、最新の状態を保つようにしてください。

## まとめ

セキュリティ機能に特化したParrot Security OSをVirtualBoxにインストールする手順をまとめました。

今回vmwareを選択しなかった理由としては、VirtualBoxではスナップショットを取ることができるため、もしマルウェアなどに感染してしまった場合などに過去の状態にすぐに戻ることができるからです。

CTFや脆弱性診断をする際に、Parrot Security OS 使ってみてください。

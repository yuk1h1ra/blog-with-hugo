---
title: "Manjaro LinuxでRDPサーバーをセットアップする方法"
date: 2024-11-24T23:25:33+09:00
description:
draft: false
hideToc: false
enableToc: true
enableTocContent: false
tocPosition: inner
tocLevels: ["h2", "h3", "h4"]
tags:
- Linux
categories:
- 
series:
-
image:
---

## はじめに

最近、PCを新しく新調したため、使用していたノートPCが余ってしまいました。

そのため、その余ったノートPCを活用して、Manjaro LinuxをRDPサーバーとして構築しました。

## 環境

セットアップに使用したPCのスペックは以下の通りです。

- PC: ThinkPad T480s
- CPU: Intel i7-8550U
- GPU: NVIDIA GeForce MX150
- メモリ: 24GB
- OS: Manjaro Linux
- Display Server: Xorg
- デスクトップ環境: KDE Plasma 6.1.5

## VNCとRDPのどちらにするか

リモートデスクトップをするにあたり、RDPとVNCのどちらにするのか検討しました。

参考にした記事はこちらです。

{{< blogcard title="RDP対VNCの詳細ガイド | TSplus" url="https://tsplus.net/ja/in-depth-guide-on-rdp-vs-vnc/" >}}

今回は、Windowsからの接続をメインに考え、描画品質や操作感に優れるRDPを採用しました。

## RDPサーバーのセットアップ

### 1. パッケージのインストール

以下のコマンドでxrdpとxorgxrdpをインストールします。

```bash
$ pamac build xrdp xorgxrdp
```

### 2. 非rootユーザーでのログイン設定

`/etc/X11/Xwrapper.config` ファイルに以下の行を追加します。この設定により、非rootユーザーでもログイン可能になります。

```
allowed_users=anybody
needs_root_rights=no
```

### 3. サービスの起動

以下のコマンドでxrdpサービスを有効化します。

```bash
$ sudo systemctl enable --now xrdp.service
$ sudo systemctl enable --now xrdp-sesman.service
```

### 4. 接続テスト

Windowsのリモートデスクトップ接続を開き、ManjaroサーバーのIPアドレスを入力します。xrdpのログイン画面が表示されたことが確認できました。

{{< img src="/posts/2024/11/img/image.png" >}}

### 5. KDE Plasmaの表示問題を修正

ログイン後、画面が真っ暗になりました。

KDE Plasmaには既知の問題が存在しており、以下の記事を参考に修正しました。

{{< blogcard title="How to make Manjaro (KDE 5) work as a xrdp server?" url="https://github.com/neutrinolabs/xrdp/issues/1456" >}}

`/etc/xrdp/startwm.sh` を以下のように編集。

```diff
# arch user
if [ -r ~/.xinitrc ]; then
    pre_start
-   . ~/.xinitrc
+   eval $(dbus-launch --sh-syntax) 
+   startplasma-x11
    post_start
    exit 0
fi
```

再度、サービスを再起動し、無事接続されることを確認しました。

{{< img src="/posts/2024/11/img/image-1.png" >}}

## ラップトップのフタを閉じてもスリープさせない設定

ラップトップをサーバーとして使用する場合、フタを閉じてもスリープしないように設定します。

以下の記事を参考にしました。

{{< blogcard title="Arch Linux: ラップトップのフタを閉じてもスリープしてほしくない" url="https://blog.micheam.com/2020/10/01/arch-linux-how-to-prevent-suspend-when-lid-close/" >}}

`/etc/systemd/logind.conf` を以下のように編集。

```diff
  [Login]
  (中略)
- #HandleLidSwitch=suspend
- #HandleLidSwitchExternalPower=suspend
- #HandleLidSwitchDocked=ignore
+ HandleLidSwitch=ignore
+ HandleLidSwitchExternalPower=ignore
+ HandleLidSwitchDocked=ignore
```

これで、ラップトップのフタを閉じてもスリープしないように設定できました。

## サウンド設定

RDP接続時にサウンドがノートPCから聞こえてきたため、以下のパッケージをインストールしました。

```bash
$ pamac build pipewire-module-xrdp-git
```

## ルーターで固定IPの割り振り

ルーターの管理画面から固定IPを割り振り、IPアドレスが変わることがないように設定しました。

## まとめ

この記事では、ThinkPad T480sにManjaro Linuxをインストールし、RDPサーバーを構築する手順を解説しました。

ただ、余ったノートPCはサーバーとして稼働させておきたく、Manjaroじゃなくてもいい部分も多いなと感じているので、そのうち総入れ替えをする予定です。

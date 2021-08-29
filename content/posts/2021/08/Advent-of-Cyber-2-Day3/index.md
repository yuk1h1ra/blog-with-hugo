---
title: "【THM: TryHackMe】Advent of Cyber 2を解いてみた【Day3】"
date: 2021-08-29T15:10:37+09:00
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
- Web Exploitation
- Burp Suite
categories:
- WriteUP
series:
- Advent of Cyber 2
image: /posts/2021/08/img/advent-of-cyber-2-thumbnail.png
---

## はじめに

Day3では、認証について、デフォルト設定の危険性についてが主な題材となっています。

認証とは、一般的にユーザーIDやパスワード等を用いて、実際に本人であるかを確認する手法です。
攻撃者はこの認証をどうにかして突破したいと考えており、今回は認証情報によく使われるリストを用いて突破する方法を紹介しています。

**それでは、Day3を解いていきましょう。**

{{< notice warning "警告" >}}
本記事は全てのセキュリティに携わる人に向けて執筆しており、クラッキングを推奨するものではありません。本記事の手法を用いて、許可された環境以外で実行することは絶対にやめてください。
{{< /notice >}}

## Christmas Chaos

最初にTopページにアクセスすると、次のような画面が表示されます。

![Login Page](img/2021-08-29-15-42-23.png)

今回は、プロキシツールである**Burp Suite**を使って、辞書攻撃を仕掛ける問題となっています。

Kali LinuxやParrot Security OSを使用している場合はデフォルトでインストールされているため、そのまま立ち上げます。

![Burp Suite](img/2021-08-29-15-46-46.png)

Burp Suiteはデフォルトで`127.0.0.1:8080`になっています。

![Default Listener](img/2021-08-29-15-49-43.png)

それでは、FoxyProxyの設定をしていきます。

FirefoxのアドオンのFoxyProxyをインストールした後に、以下の画像のOptionから設定していきます。

![Foxy Proxy](img/2021-08-29-15-54-18.png)

Optionを押した後は以下の画像のようになるので、左上の`Add`から新しい設定を追加します。

![FoxyProxy Options](img/2021-08-29-15-56-51.png)

Nameを好きな名前、IPアドレス/PortをBurpSuiteの設定に記載されているものに設定し、Saveで保存します。

![FoxyProxy Add](img/2021-08-29-15-59-42.png)

無事、新しい設定が表示されていることがわかります。

![FoxyProxy Saved New Settings](img/2021-08-29-16-01-34.png)

では、FoxyProxyで設定したものを選択し、[http://burpsuite](http://burpsuite)にアクセスします。

![HTTP burpsuite](img/2021-08-29-16-04-07.png)

右上のCA Certificateをクリックし、証明書をダウンロードします。

ダウンロードした証明書をブラウザにインポートしていきます。
Firefoxの設定から、`プライバシーと設定->証明書->証明書を表示`と進んでいき、先程ダウンロードした証明書をインポートしていきます。

![Import Certificate](img/2021-08-29-16-15-26.png)

これで、設定は完了です。
正しく設定できていれば、BurpSuiteで確認することができます。

![BurpSuite - Proxy Tab](img/2021-08-29-16-20-35.png)

しかし、このままでは意図していないリクエストも表示されてしまうため、フィルタリングをすることで見やすくしていきます。

BurpSuiteのTarget->Site mapから、今回のホストを右クリックし、Add to scopeを選択してあげます。

![BurpSuite - Add to scope](img/2021-08-29-16-24-38.png)

その後、Proxy->Http historyから、`Filter: Hiding CSS, image and general binary content`をクリックし、`Show only in-scope items`にチェックをいれることで、ターゲットのリクエストのみを抽出することができます。

![BurpSuite - Filter](img/2021-08-29-16-26-49.png)

![BurpSuite - Show only in-scope items](img/2021-08-29-16-28-37.png)

![BurpSuite - Filtering Histories](img/2021-08-29-16-30-57.png)

あとは、TryHackMeに記載されている通りにやればフラグはゲットできます。

## おわりに

今回はほとんどCTFの解説をすることがなかったので、BurpSuiteの設定方法を詳しく説明しました。
自分も最初はBurpSuiteの設定方法がうまく出来ず、苦労した経験があったので詰まりそうな部分を詳しく記載いたしました。

CTFを始めてみたけど、設定から躓いてしまう人がいましたら、是非この記事を参考にしてみてください。

---
title: "【THM: TryHackMe】Advent of Cyber 2を解いてみた【Day14】"
date: 2022-02-19T15:12:17+09:00
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

今回はTheCyberMentorが作成した問題です。

OSINTといい、一般に公開された情報をもとに機密情報を取得する方法を学びます。

{{< notice warning "警告" >}}
本記事はすべてのセキュリティに携わる人に向けて執筆しており、クラッキングを推奨するものではありません。本記事の手法を用いて、許可された環境以外で実行することは絶対にやめてください。
{{< /notice >}}

## Where's Rudolph?

### What URL will take me directly to Rudolph's Reddit comment history?

> ルドルフのRedditのコメント履歴に直接アクセスできるURLは？

Task #1には「Rudolph loved to use Reddit and browsed aplendty. His username was 'IGuidetheClaus2020'」

とあるので、Redditでそのユーザーを探します。

Reddit内にCommentタブがあるので、そのURLがflagです。

### According to Rudolph, where was he born?

> ルドルフによると、彼はどこで生まれましたか？

これはルドルフがRedditに残しているコメントの中に答えが含まれています。

### Rudolph mentions Robert.  Can you use Google to tell me Robert's last name?

> ルドルフはロバートについて言及しています。 Googleでロバートの苗字を教えてくれませんか？

これは少し知識がいります。

赤鼻のトナカイの原題は「Rudolph the Red-Nosed Reindeer」です。

その作者がRobertです。

作者のLast Nameがflagになっています。

### On what other social media platform might Rudolph have an account?

> ルドルフは他にどのようなソーシャルメディアにアカウントを持っているのでしょうか？

こちらもReddit内の投稿でヒントが隠されています。

### What is Rudolph's username on that platform?

> そのプラットフォームでのルドルフのユーザー名は？

Redditに使われているユーザー名からTwitterでも同様のIDを使用しているのではないかと考え、検索したらヒットしました。

### What appears to be Rudolph's favorite TV show right now?

> ルドルフが今、一番好きなテレビ番組は何だと思われますか？

ルドルフのTwitterを見ているとリツイートの中にテレビ番組のものが含まれているので、注意深く見ていくとみつかります。

### Based on Rudolph's post history, he took part in a parade.  Where did the parade take place?

> ルドルフの投稿履歴を見ると、パレードに参加したようです。 そのパレードはどこで行われたのでしょうか？

投稿された画像をGoogle検索にかけてみるとどこで開催されたパレードなのか分かります。

### Okay, you found the city, but where specifically was one of the photos taken?

> 街は見つけました、しかし具体的にどこで撮られた写真の1つでしたか？

写真から具体的な場所を指定されたらExif確認するのが最初に考えることでしょう。

Twitterの後の投稿に、より高い解像度の写真があるためそちらをダウンロードし、Exifを確認します。

ただ、答えの形式へのFormatの仕方がわからなかったため、Hintに記載されているURLよりflagを得ました。

{{< blogcard title="Jeffrey's Image Metadata Viewer" url="http://exif.regex.info/exif.cgi" >}}

### Did you find a flag too?

> flagも見つけましたか？

exiftoolコマンドを使ってExifを確認すると、Copyrightの部分にflagがありました。

```bash
exiftool filename.jpg
```

### Has Rudolph been pwned? What password of his appeared in a breach?

> ルドルフは打ち負かされましたか？彼のどのパスワードが漏洩したのでしょうか？

ルドルフのTwitterには、Emailが掲載されています。

そこから、漏洩したパスワードがないかを確認します。

2022/2/19日現在、[scylla.so](https://scylla.so/)はサイト改築中でアクセスできませんでした。

### Based on all the information gathered.  It's likely that Rudolph is in the Windy City and is staying in a hotel on Magnificent Mile.  What are the street numbers of the hotel address?

> 集まった情報をもとに  ルドルフはウィンディ・シティにおり、マグニフィセント・マイルにあるホテルに滞在していると思われます。 ホテルの住所の番地は何番ですか？

Exif情報からGoogleマップで確認します。

近くにあるホテルの住所にある番地が答えになります。

## おわりに

今回はユーザー名から出身地や今現在滞在している場所などを特定するOSINTの問題をやりました。

画像からExifを見るのはCTFでもよくやりますが、Exifがない画像から場所を特定する問題も多くあります。

自分も少しやってみたのですが、手も足もでずに諦めてしまいました。

興味がある人は是非Tryしてみてください。

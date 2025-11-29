# Yuk1h1ra's Knowledge Kingdom

## 概要

セキュリティ関係に特化したブログ。
ハッキング、CTF、WriteUP、ツールの紹介などが主な記事内容になります。

## 特徴

### 静的サイトジェネレーター

このブログは静的サイトジェネレーター(SSG)を使用して作成されています。
使用したのは[Hugo](https://github.com/gohugoio/hugo)です。

様々なSSGがある中でhugoを選択した理由は、Starの数とテーマの豊富さです。
SSGでサイト作成が初めてだったため、Star数がある程度あり選べるテーマが豊富にあるHugoにしました。

実際使って見ると、コマンド一つで完結し公式Documentを見ながら始めれば大抵の問題は解決できたので良かったです。

他のSSGを選択しなかった理由を幾つか列挙しておきます。

- Hexo: モジュールを入れるのにnpmコマンドを適宜打つ必要がある可能性があり、学習コストが高いと感じたため
- Jekyll: Github Pagesで公式にサポートされている点はいいが、他のSSGを調べている際にJekyllから乗り換えする記事が目立ったため
- Gatsby: カスタマイズ性は一番あり、Reactをやっている人なら難なく使用できるが、ブログにそこまでのカスタマイズを求めていないため。

### Hugo Theme

Hugoでは、様々なテーマを選択することができます。
このブログでは、[Zzo](https://github.com/zzossig/hugo-theme-zzo)を使用しています。

選定理由は、モバイル対応しているとか、SEO対策がテーマに盛り込まれているとか、Syntax Highlightに対応しているとかではありません。
単純に、テーマが**カッコイイ**からです。

## コマンド類

### 新しい記事の作成

ブログのディレクトリを/[year]/[month]/[title]に設定しているため、以下のコマンドで一発で作成できる

```bash
hugo new content posts/[year]/[month]/[title]/index.md
```

### テーマリポジトリの更新

submoduleに追加したテーマがアップデートされた際に、それをYKKでも反映させるには以下のコマンドを実行する

```bash
git submodule update --remote
```

## ShortCodes

hugoのShortCodesを用いると様々な面で便利になる。
詳しくは[Hugo ShortCodes](https://gohugo.io/content-management/shortcodes/)もしくは、[Zzo ShortCodes](https://themes.gohugo.io//theme/hugo-theme-zzo/en/posts/shortcodes/)を参照する。

参考になりそうなShortCodesについては以下に記述する。

### tweet

tweetのIDをURLから取得し貼り付ける

```txt
{{< tweet 12345678901234567890 >}}
```

### blogcard

自作のShortCodeで、ブログカードを作成する際に使用する

```txt
{{< blogcard title="This is Title" url="https://example.com" >}}
```

### md形式の画像からzzoの画像に変換

#### 検索（Find）

```
!\[(.*?)\]\((.*?)\)
```

#### 置換（Replace）

```
{{< img src="/posts/2025/11/$2" >}}
```

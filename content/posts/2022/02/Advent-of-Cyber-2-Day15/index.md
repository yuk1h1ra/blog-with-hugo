---
title: "【THM: TryHackMe】Advent of Cyber 2を解いてみた【Day15】"
date: 2022-02-20T10:20:54+09:00
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

今回はpythonスクリプトの章になります。

プログラミング言語は多数ありますが、CTFではpythonがよく用いられています。

理由としては、pythonにはCTFに有用なモジュールが多数あり、またインタプリタ言語なため、軽く記述する分にはとても楽に書けるからです。

{{< notice warning "警告" >}}
本記事はすべてのセキュリティに携わる人に向けて執筆しており、クラッキングを推奨するものではありません。本記事の手法を用いて、許可された環境以外で実行することは絶対にやめてください。
{{< /notice >}}

## There's a Python in my stocking!

### What's the output of True + True?

> True + Trueの出力は何ですか？

基本的な問題です。

hello.pyに以下の記述をして、実行してあげればflagは分かります。

```bash
print(True + True)
```

普段からプログラミングに慣れている人なら実行しなくても解答できると思います。

### What's the database for installing other peoples libraries called?

> 他の人のライブラリをインストールするためのデータベースは何と呼ばれているのですか？

日本語にうまく訳せませんが、これは他の人のライブラリをインストールする際に用いられているものはなんですか？という問題です。

pythonでは、「Python Package Index」に自分の作成したパッケージを配布したり、他の人のパッケージをダウンロードしたりしています。

この省略系を解答します。

### What is the output of bool("False")?

> bool（"False"）の出力は何ですか？

こちらもそのまま出力させれば解答は得られます。

注目するべき点はFalseが文字列として扱われているところです。

### What library lets us download the HTML of a webpage?

> WebページのHTMLをダウンロードできるライブラリはどれですか。

pythonでよく用いられているライブラリです。

こちらはライブラリの名前を解答すればいいです。

分からない場合も本文中に記述されているため、解答に困ることはないでしょう。

### What is the output of the program provided in "Code to analyse for Question 5" in today's material?

> 本日の資料の「問5の解析コード」で提供されているプログラムの出力はどのようなものか。

こちらはこのような問題になっています。

```python
x = [1, 2, 3]

y = x

y.append(6)

print(x)
```

これは、一般的に参照渡しの話です。

pythonのリスト型は代入するときに参照渡しとなります。

### What causes the previous task to output that?

> 前のタスクがそれを出力する原因は何でしょうか？

参照渡しの話です。

解答自体は本文中にヒントが隠されているので、それを解答します。

## おわりに

今回はpythonの基本的なスクリプトを記述する章でした。

一度でもプログラミングをしたことがある人は言語の違いがありますが、30分程度で解けてしまう問題です。

---
title: "【THM: TryHackMe】Advent of Cyber 2を解いてみた【Day16】"
date: 2022-02-20T11:00:27+09:00
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

今回は

{{< notice warning "警告" >}}
本記事はすべてのセキュリティに携わる人に向けて執筆しており、クラッキングを推奨するものではありません。本記事の手法を用いて、許可された環境以外で実行することは絶対にやめてください。
{{< /notice >}}

## Help! Where is Santa?

### What is the port number for the web server?

> Webサーバーのポート番号は？

ポート番号を調べるので、nmapで見ていきます。

```bash
nmap 10.10.xxx.xxx
```

### Without using enumerations tools such as Dirbuster, what is the directory for the API?  (without the API key)

> Dirbusterなどの列挙ツールを使用せずに、APIのディレクトリは何ですか？ (APIキーなし)

使用せずにとあるので、html上のソースコードにないか確認していきます。

自分はスクリプトを書くのもいいですが、開発者ツールで探して終わらせてしまいました。

### Where is Santa right now? Find out the correct API key. Remember, this is an odd number between 0-100.

> サンタは今どこにいますか？正しいAPIキーを調べてください。0～100の奇数であることをご確認ください。

これらの問題を解くpythonのスクリプトを記述します。

```python
import requests

for num in range(1, 100, 2):
    res = requests.get(f"http://10.10.xxx.xxx/api/{num}")
    print(res.text)
```

やっていることは単純で、1~100までの奇数を順にリクエストを飛ばし、そのレスポンスを確認しています。

帰ってきたidとqを答えて終了です。

## おわりに

今回は前回学んだpythonスクリプトを使って、リクエストを飛ばす問題でした。

処理の自動化ができる部分はスクリプトを記述することでより簡単に解決できる場合があります。

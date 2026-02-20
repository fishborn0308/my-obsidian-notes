---
created: 2026-01-09 16:40
modified: 2026-02-20 17:36
environment: [Server/Mail]
vulnearability: []
type: pentest-walkthrough
pentest_category: Web
platform: [PurpleFlair]
tools: []
cve: []
tags: 
---

# [Pentest-Walkthrough] Web - Project: SMTPサービスへのスキャン

攻撃対象マシンのSMTPサーバの情報を集めてSMTP通信をしましょう。

## SMTPサービスへのスキャン

```
┌──(kali㉿kali)-[~]
└─$ nmap -Pn 10.234.132.49
Starting Nmap 7.95 ( https://nmap.org ) at 2026-01-09 16:43 JST
Nmap scan report for ip-10-234-132-49.ap-northeast-1.compute.internal (10.234.132.49)
Host is up (0.00029s latency).
Not shown: 980 closed tcp ports (reset)
PORT     STATE SERVICE
21/tcp   open  ftp
22/tcp   open  ssh
23/tcp   open  telnet
25/tcp   open  smtp
53/tcp   open  domain
80/tcp   open  http
111/tcp  open  rpcbind
139/tcp  open  netbios-ssn
445/tcp  open  microsoft-ds
512/tcp  open  exec
513/tcp  open  login
514/tcp  open  shell
1099/tcp open  rmiregistry
1524/tcp open  ingreslock
2121/tcp open  ccproxy-ftp
3306/tcp open  mysql
5432/tcp open  postgresql
6667/tcp open  irc
8009/tcp open  ajp13
8180/tcp open  unknown
MAC Address: 0A:0E:8D:7C:2C:45 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 0.39 seconds
                                                                        
┌──(kali㉿kali)-[~]
└─$ 
```

## NSEスクリプトを用いたSMTPサーバでサポートされているコマンドの確認

```
┌──(kali㉿kali)-[~]
└─$ nmap -p 25 --script smtp-commands 10.234.132.49
Starting Nmap 7.95 ( https://nmap.org ) at 2026-01-09 16:45 JST
Nmap scan report for ip-10-234-132-49.ap-northeast-1.compute.internal (10.234.132.49)
Host is up (0.00012s latency).

PORT   STATE SERVICE
25/tcp open  smtp
|_smtp-commands: metasploitable.localdomain, PIPELINING, SIZE 10240000, VRFY, ETRN, STARTTLS, ENHANCEDSTATUSCODES, 8BITMIME, DSN
MAC Address: 0A:0E:8D:7C:2C:45 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 9.27 seconds
                                                                        
┌──(kali㉿kali)-[~]
└─$ 
```

## Netcatを用いたメールの送信

Netcatコマンドを用いてSMTPサーバに接続します。

```
nc <target1_ip> 25
```

接続に成功すると以下の出力を得ます。

```
┌──(kali㉿kali)-[~]
└─$ nc <target1_ip> 25                                 
220 metasploitable.localdomain ESMTP Postfix (Ubuntu)
```

Netcatを用いて接続すると、バナー情報の確認や通信ができます。

次に、SMTPサーバに送信セッションの開始を要求します。 送信セッションの開始を要求するには、以下のコマンドを実行します。

```
EHLO <domain_name>
```

実行すると以下の出力を得ます。

```
EHLO kali
250-metasploitable.localdomain
250-PIPELINING
250-SIZE 10240000
250-VRFY
250-ETRN
250-STARTTLS
250-ENHANCEDSTATUSCODES
250-8BITMIME
250 DSN
```

送信元のメールアドレスを指定するには、以下のコマンドを実行します。

```
MAIL FROM: <email_address>
```

また、送信先のメールアドレスを指定するには、以下のコマンドを実行します。

RCPT TO: <email_address>

ここでは、送信元に<from@example.com>、送信先に<msfadmin@metasploitable.localdomain>を指定して実行します。

```
MAIL FROM: <from@example.com>
250 2.1.0 Ok
RCPT TO: <msfadmin@metasploitable.localdomain>
250 2.1.5 Ok
```

最後に、メールを送信します。メールデータを決定して送信したあとに、SMTP接続を終了するには以下のコマンドを実行します。

```
data
.
quit
```

実行すると以下の出力を得ます。

```
data
354 End data with <CR><LF>.<CR><LF>
.
250 2.0.0 Ok: queued as 1E9701A3FBA
quit
221 2.0.0 Bye
```

ただし、一般的なメール送信では、以下のように空行を挟んでヘッダーとボディが入力されることに注意してください。

ヘッダーには差出人や宛先のメールアドレス、件名などを記載します。ボディにはメール本文を記載します。

```
data
to:<email_address>
from:<email_address>
subject:<subject>

<mail_body>
.
quit
```

参考

攻撃者はアカウント/メーリングリストの存在を確認するためにVRFY/EXPNを用いることがあります。

例えば、以下のようなコマンドを実行することで、アカウントmsfadminは存在し、fsmadminは存在しないことがわかります。

```
VRFY msfadmin
252 2.0.0 msfadmin
VRFY fsmadmin
550 5.1.1 <fsmadmin>: Recipient address rejected: User unknown in local recipient table
```
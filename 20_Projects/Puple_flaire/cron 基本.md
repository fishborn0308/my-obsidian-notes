---
created: 2026-01-08 11:23
modified: 2026-02-20 16:15
environment: [OS/Linux]
vulnearability: [Cron]
type: pentest-walkthrough
pentest_category: OS
platform: [PurpleFlair]
tools: []
cve: []
tags: 
---

# [Pentest-Walkthrough] OS - Project: Cron 基本

この問題では、cronを自身で設定することでcronの仕組みを理解することを目的とします。cronの設定は、/etc/crontabファイルを編集する方法と、crontab -eコマンドを利用して設定する2通りの方法があり、この問題ではそれぞれを学習します。

なお、この問題ではKali Linuxのみを利用します。

## 前準備

ユーザごとのcron設定の見え方を確認するため、kaliユーザとは別のユーザを作成します。

useraddコマンドを利用し、testuserという名前のユーザを作成しましょう。

```
sudo useradd -m testuser
```

passwdコマンドを利用し、作成したユーザのパスワードを設定しましょう。なお、このユーザでログインするため、入力したパスワードは覚えておいてください。

```
sudo passwd testuser
```

chshコマンドでこのユーザのログインシェルを/bin/zshへ変更しましょう。

```
sudo chsh testuser -s /bin/zsh
```

ユーザの作成とログインシェルの設定が完了したら、以下の手順で操作して、cronについて学習しましょう。

## /etc/crontabの編集

/etc/crontabを編集することで、定期実行をしてみましょう。

### 1. /etc/crontabファイルの内容と権限の確認

cronの設定ファイル/etc/crontabの内容と権限を確認しましょう。

catコマンドで/etc/crontabファイルを標準出力し、内容を確認します。

```
$ cat /etc/crontab
# /etc/crontab: system-wide crontab
# Unlike any other crontab you don't have to run the `crontab'
# command to install the new version when you edit this file
# and files in /etc/cron.d. These files also have username fields,
# that none of the other crontabs do.

SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

# Example of job definition:
# .---------------- minute (0 - 59)
# |  .------------- hour (0 - 23)
# |  |  .---------- day of month (1 - 31)
# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
# |  |  |  |  |
# *  *  *  *  * user-name command to be executed
17 *    * * *   root    cd / && run-parts --report /etc/cron.hourly
25 6    * * *   root    test -x /usr/sbin/anacron || { cd / && run-parts --report /etc/cron.daily; }
47 6    * * 7   root    test -x /usr/sbin/anacron || { cd / && run-parts --report /etc/cron.weekly; }
52 6    1 * *   root    test -x /usr/sbin/anacron || { cd / && run-parts --report /etc/cron.monthly; }
```

また、ls -laコマンドにて/etc/crontabファイルの権限を確認します。

```
┌──(kali㉿kali)-[~]
└─$ ls -la /etc/crontab
-rw-r--r-- 1 root root 1040 Nov 21 11:36 /etc/crontab
```

-rw-r--r--であることと所有者・所有グループがrootであることから、rootユーザーが読み書きの権限を持ち、kaliなどの一般ユーザが読み込み権限のみを持っていることがわかります。

### 2. /etc/crontabファイルによるcron設定

root権限で1分おきにwhoamiコマンドを実行し、その結果を/home/kali/crontest.txtファイルへ追記する​cronを設定しましょう。

テキストエディタで/etc/crontabファイルを開き、以下のcron設定を追記します。 なお、kaliユーザはファイル編集権限を持たないため、sudoコマンドと合わせてテキストエディタを実行する必要があります。

```
*/1 * * * * root whoami >> /home/kali/crontest.txt
```

### 3. /etc/crontabファイルによるcron実行の確認

cronによりwhoamiの実行結果が/home/kali/crontest.txtファイルへ追記されていることを確認しましょう。

cronを設定してから数分経過後、/home/kali/crontest.txtファイルが作成されていることを確認でき、このファイルにrootという文字列が追加されていることがわかります。

```
┌──(kali㉿kali)-[~]
└─$ cat /home/kali/crontest.txt
root
root
...(snip)...
```

### 4. testuserユーザでの/etc/crontabファイルの確認

testuserユーザで/etc/crontabファイルを閲覧できることを確認しましょう。

一般ユーザは/etc/crontabファイルの読み込み権限を持っているため、一般ユーザでもその内容を確認することができます。ここでは、前準備で作成したtestuserユーザへ切り替えた後、/etc/crontabファイルの内容を確認します。

まず、suコマンドでtestuserへ切り替えます。パスワードには自身で設定したパスワードを入力してください。

```
┌──(kali㉿kali)-[~]
└─$ su -l testuser
Password:
```

testuserユーザで/etc/crontabファイルを閲覧でき、実行されるコマンドを確認します。

```
┌──(testuser㉿kali)-[~]
└─$ cat /etc/crontab
...(snip)...
*/1 * * * * root whoami >> /home/kali/crontest.txt
```

kaliユーザで追加したcron設定を、kaliユーザとは別のtestuserユーザで確認できます。

### 5. /etc/crontabファイルの設定初期化

次の項目から別の内容となります。原状復帰のため、kaliユーザへ戻り、/etc/crontabファイルへ追加した設定を削除しましょう。

ログアウトし、testuserユーザからkaliユーザへ戻ります。

テキストエディタで/etc/crontabファイルを開き、追加したcron設定を削除します。

## crontabコマンドでの設定

/etc/crontabの編集ではなく、crontabコマンドを使って設定する方法があります。この方法を学びましょう。

### 1. /var/spool/cron配下のファイル・ディレクトリ権限と初期状態の確認

/var/spool/cron配下のファイル・ディレクトリの権限を確認しましょう。

/var/spool/cronディレクトリを確認すると、/var/spool/cron/crontabsディレクトリがあり、一般ユーザには読み込み権限と書き込み権限がないことがわかります。

```
┌──(kali㉿kali)-[~]
└─$ ls -la /var/spool/cron
total 12
drwxr-xr-x 3 root root    4096 Dec  9  2022 .
drwxr-xr-x 6 root root    4096 Aug  8  2023 ..
drwx-wx--T 2 root crontab 4096 Nov  8 11:15 crontabs
```

### 2. crontabコマンドによるcron設定

1分おきにwhoamiコマンドを実行し、その結果を/home/kali/crontab.txtファイルへ追記する​cronを設定しましょう。

crontab -eコマンドにてユーザのcronの設定を行うことができます。これを実行するとviでcronの設定を書き込めるようになるため、*/1 * * * * whoami >> /home/kali/crontab.txtを追記して保存しましょう。

### 3. crontabコマンドによるcron設定の確認

crontab -lコマンドを実行し、cronの設定を確認しましょう。

```
┌──(kali㉿kali)-[~]
└─$ crontab -l
...(snip)...
*/1 * * * * whoami >> /home/kali/crontab.txt
```

### 4. /var/spool/cron/crontabs配下のファイルによるcron設定の確認

/var/spool/cron配下に作成されるcron設定ファイルを閲覧することができるか確認しましょう。

1.で確認した通り、/var/spool/cron/crontabsディレクトリは一般ユーザで読み込み権限がないため、一般ユーザではそのディレクトリ内のファイルを閲覧することができません。

crontabコマンドを利用してcronの設定を行った場合は、/var/spool/cron/crontabs配下に、ユーザ名を名前に持つファイルが作成され、そのファイルにcronの設定が書き込まれます。このファイルはrootユーザのみ確認することができます。

```
┌──(kali㉿kali)-[~]
└─$ sudo ls -la /var/spool/cron/crontabs
total 12
drwx-wx--T 2 root crontab 4096 Nov 18 14:49 .
drwxr-xr-x 3 root root    4096 Dec  9  2022 ..
-rw------- 1 kali crontab  220 Nov 18 14:49 kali

┌──(kali㉿kali)-[~]
└─$ sudo cat /var/spool/cron/crontabs/kali
...(snip)...
*/1 * * * * whoami >> /home/kali/crontab.txt
```

### 5. crontabコマンドによるcron実行の確認

cronによりwhoamiコマンドの実行結果が/home/kali/crontab.txtファイルへ追記されていることを確認しましょう。

cronを設定してから数分経過後、/home/kali/ディレクトリにcrontab.txtファイルが作成されていることを確認でき、このファイルにkaliという文字列が追加されていることがわかります。

```
┌──(kali㉿kali)-[~]
└─$ cat /home/kali/crontab.txt
kali
kali
...(snip)...
```

Authorization required, but no authorization protocol specified

cron: Authorization required, but no authorization protocol specified

というエラーメッセージは、通常、X Window System（X11）の表示に関連するプログラム（グラフィカルなアプリケーションやユーティリティ）をcronジョブとして実行しようとしたときに発生します。

XAUTHORITY=/home/your_username/.Xauthority

DISPLAY=:0.0

# 以下にジョブを記述

* * * * * /path/to/your/script.sh

xhost +local:

# または特定のユーザー/ホストからのアクセスを許可

DISPLAY=:0.0 /path/to/your/script.sh

cronジョブのコマンドラインまたはcrontabの冒頭で、使用したいディスプレイを指定します。通常は:0や:0.0です。
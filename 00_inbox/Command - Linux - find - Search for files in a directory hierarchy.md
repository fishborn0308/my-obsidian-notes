--- 
created: '2026-02-26' 
modified: '2026-02-26' 
environment: [OS/Linux, Shell/Bash] 
vulnerability: [Information_Disclosure, Privilege_Escalation, Arbitrary_Command_Execution] 
knowledge_category: Command 
tags: [find, coreutils, enumeration, search, knowledge_base] 
---

# Command: find (search for files in a directory hierarchy)

## 概要
`find` は、指定したディレクトリツリー内を再帰的に検索し、ファイル名、サイズ、権限、タイムスタンプ、所有者などの属性に基づいてファイルを抽出する強力なツールです。単なる検索にとどまらず、`-exec` オプションによって検索結果に対して直接コマンドを実行できるため、ペンテストにおける権限昇格ポイントの自動探索や、インフラ運用における一括処理の要となります。

## 類似・モダンコマンド比較

| コマンド | 特徴・メリット | デメリット |
| :--- | :--- | :--- |
| `find` | **標準搭載。** 非常に多機能で、詳細な条件指定（権限、時間等）が可能。 | 構文が独特で少し複雑。大規模FSでは低速な場合がある。 |
| `locate` | `updatedb` で作成されたDBを参照するため、**極めて高速。** | DBが更新されていないと最新のファイルを見つけられない。 |
| `fd` (fd-find) | Rust製。デフォルトで高速、Git無視、カラー表示。構文が直感的。 | 標準環境にはない。複雑な属性検索は `find` に劣る。 |
| `grep -r` | ファイル**内容**を検索する。 | ファイル属性（パーミッション等）の検索はできない。 |

## 連携例
`find` は後続処理への「フィルター」として機能します。

1. **特定拡張子のファイルから文字列を検索**
   `find . -name "*.conf" -exec grep "password" {} +`
2. **空のディレクトリを全て削除**
   `find /path/to/dir -type d -empty -delete`
3. **xargs と組み合わせて高速処理**
   `find . -type f -name "*.log" -print0 | xargs -0 rm` (スペース対応の `-print0` は必須)

## オプション詳解（主要なものは⭐️）

| オプション | 役割 | 備考 |
| :--- | :--- | :--- |
| `-name` / `-iname` ⭐️ | ファイル名指定。`i`がつくと大文字小文字を区別しない。 | ワイルドカード `*` は引用符で囲むこと。 |
| `-type` ⭐️ | ファイルタイプ指定。`f` (file), `d` (dir), `l` (symlink)。 | 特定の種類のオブジェクトのみ絞り込む。 |
| `-perm` ⭐️ | 権限(Permission)で検索。 | `-perm -4000` (SUID) 等、ペンテストで多用。 |
| `-user` / `-group` | 所有者やグループで検索。 | 不適切な所有権のファイル発見に使用。 |
| `-size` ⭐️ | ファイルサイズで検索。`+10M` (10MB以上) 等。 | `c` (bytes), `k`, `M`, `G` が使用可能。 |
| `-mtime` / `-atime` | 修正/アクセス日時。`+7` (7日前より古い) 等。 | ログの整理や侵入調査に使用。 |
| `-exec` ⭐️ | 検索結果に対してコマンドを実行。 | `{}` はファイル名に置換される。最後は `\;` か `+`。 |
| `-maxdepth` | 検索する階層の深さを指定。 | `1` を指定すればカレントディレクトリのみ。 |
| `-not` / `!` | 条件を反転させる。 | 「～以外」を探す際に非常に便利。 |
| `-ls` | 見つかったファイルを `ls -dils` 形式で表示。 | 詳細を一度に見たい時に便利。 |

## 業務シナリオ別詳細

### 1. インフラ運用・トラブルシューティング
**シナリオ: 古いログファイルの一括削除とディスククリーンアップ**
数ヶ月以上更新がなく、特定の容量を超えるログファイルを特定し、安全に削除または圧縮します。

- **実行コマンド**: `find /var/log -type f -name "*.log" -mtime +30 -size +100M -ls`
- **実務ログ例**:

```bash
user@infra-srv:~$ find /var/log/app -type f -mtime +90 -exec ls -lh {} \;
-rw-r--r-- 1 app_user app_group 250M Nov 15 10:00 /var/log/app/old_trace.log
-rw-r--r-- 1 app_user app_group 1.2G Oct 01 09:00 /var/log/app/debug_dump.log
# 確認後、削除実行
user@infra-srv:~$ find /var/log/app -type f -mtime +90 -delete
```

> **知見**: `-mtime +90`（90日より前）と `-size` を組み合わせることで、ディスクを圧迫している「過去の遺物」のみをピンポイントで排除できます。

---

### 2. ブルーチーム視点（ログ解析・フォレンジック）

**シナリオ: 侵害発生時刻前後に作成・変更されたファイルの抽出**

Webサーバが改ざんされた疑いがある際、特定の短期間（タイムウィンドウ）に変更された不審なスクリプトをあぶり出します。

- **実行コマンド**: `find /var/www/html -newermt "2026-02-25 00:00:00" ! -newermt "2026-02-26 12:00:00" -ls`
    
- **実務ログ例**:

```bash
user@soc-analyst:~$ find /var/www/html -type f -newermt "2026-02-26 14:00"
/var/www/html/assets/images/logo.png
/var/www/html/includes/db_conn.php.bak
/var/www/html/upload/shell.php  <-- [!] 不審なファイルを発見
```

> **知見**: `-newermt` は、日時を直接指定できるため、IDS/IPSの検知アラート時刻に合わせた調査に極めて有効です。`!`（否定）との組み合わせで期間を絞り込みます。

---

### 3. レッドチーム視点（列挙・権限昇格）

**シナリオ: SUID/SGIDバイナリの探索と書き込み可能ディレクトリの特定**

OSCPのラボや実戦において、権限昇格(Privilege Escalation)のベクトルを調査します。

- **実行コマンド**:
    
    1. `find / -perm -4000 -type f 2>/dev/null` (SUID探索)
        
    2. `find / -writable -type d 2>/dev/null` (書き込み可能ディレクトリ探索)
        
- **実務ログ例**:

```bash
user@target-box:~$ find /usr/bin /usr/local/bin -perm -4000 -ls 2>/dev/null
 135422  44 -rwsr-xr-x   1 root     root        43088 Feb 26 2024 /usr/bin/newgrp
 135601  60 -rwsr-xr-x   1 root     root        59976 Feb 26 2024 /usr/bin/passwd
 204552 120 -rwsr-sr-x   1 root     root       118423 Jan 10 15:30 /usr/local/bin/custom_backup
```

> **知見**: `/usr/local/bin/custom_backup` に SUID が立っていることを発見。標準バイナリではないため、この実行ファイルの挙動を解析（stringsやstrace）して root 奪取の脆弱性を探るフェーズへ移行します。

## エラー対処・環境変数・セキュリティ

### 1. よくあるエラーと対処法
| エラーメッセージ | 原因 | 対策 |
| :--- | :--- | :--- |
| `find: ‘/root’: Permission denied` | 実行ユーザーにそのディレクトリの検索権限がない。 | 末尾に `2>/dev/null` を付けてエラーを捨てる。 |
| `find: paths must precede expression` | パス指定の後にオプションが来ていない、または引数の順序ミス。 | `find [パス] [オプション]` の順守を確認。 |
| `find: missing argument to '-exec'` | `-exec` の終端記号（`\;` や `+`）がない、またはエスケープ忘れ。 | `\;`（バックスラッシュ＋セミコロン）を必ず付ける。 |

### 2. 環境変数と動作設定
`find` は環境変数よりも、シェルの「Globbing（展開）」の影響を強く受けます。

- **ワイルドカードのクォート**:
  `find . -name *.txt` と書くと、シェルが先にカレントディレクトリの `.txt` を展開してしまい、意図しない挙動になります。必ず `find . -name "*.txt"` のように引用符で囲みます。
- **`-print0` と `xargs -0`**:
  ファイル名にスペースや改行が含まれる場合、通常のパイプでは壊れます。これらを組み合わせることで、ヌル文字（\0）区切りで安全に受け渡せます。

### 3. セキュリティ考慮事項（GTFOBins / 権限昇格）
`find` はペンテスターにとって「スイスアーミーナイフ」です。

- **GTFOBins (Shell Execution)**:
  もし `find` に SUID ビットが設定されている、または `sudo` 権限がパスワードなしで与えられている場合、**root権限で任意コマンドが実行可能**です。
  ```bash
  # SUIDが立っている、または sudo 可能な場合
  find . -exec /bin/sh -p \; -quit
  ```

- **不適切なパーミッション探索**:
    
    `find / -type f -perm -o+w` (全ユーザー書き込み可能なファイル) を探すことで、設定ファイルや起動スクリプトの改ざんによる権限昇格（PrivEsc）を狙えます。
    
### 4. 実務上の注意点

- **リソース負荷**:
    
    ルートディレクトリ `/` からの検索 (`find / -name ...`) は、ディスクI/Oに高負荷をかけます。本番環境では `-maxdepth` を指定するか、可能な限りパスを絞り込んで実行してください。
    
- **`-exec` のパフォーマンス**:
    
    `-exec ... \;` は見つかったファイルごとにプロセスを起動するため低速です。大量のファイルを処理する場合は `-exec ... +` または `xargs` を使用して、まとめて引数に渡す方が圧倒的に高速です。
    
- **`-delete` の配置**:
    
    `-delete` オプションは**最後に配置**してください。先に置くと、条件に合致するか判定する前に削除が試行されるリスクがあります。
    
    - ⭕️ `find . -name "*.tmp" -delete`
        
    - ❌ `find . -delete -name "*.tmp"`
---
created: '2026-02-26'
modified: '2026-02-26'
environment: [OS/Linux, Shell/Bash, Shell/Zsh]
vulnerability: [Information_Disclosure, Privilege_Escalation_Recon]
knowledge_category: Command
tags: [ls, coreutils, enumeration, file_system, knowledge_base]
---

# Command: ls (list directory contents)

## 概要
`ls` は、GNU Coreutils パッケージに含まれる、ディレクトリ内の内容をリスト表示するための最も基本的なコマンドの一つです。Unixの誕生初期から存在し、ファイル名、パーミッション、所有者、タイムスタンプなどのメタデータを取得する第一歩となります。ペンテストにおける「Enumeration（列挙）」のフェーズでは、不適切に設定されたファイル権限や隠しファイルを発見するための生命線となります。

## 類似・モダンコマンド比較

| コマンド | ツールセット | 特徴・メリット | デメリット |
| :--- | :--- | :--- | :--- |
| `ls` | GNU Coreutils | 標準的。ほぼ全てのLinux環境にプリインストールされている。 | デフォルトで色分けがない場合があり、視認性が環境に依存する。 |
| `dir` | GNU Coreutils | `ls -C -b` とほぼ同等。Windowsユーザー向けに近い挙動。 | Linux環境では `ls` が主流のため、あえて使う理由は少ない。 |
| `exa` / `eza` | Rust-based | モダンな代替。デフォルトで色鮮やか、ツリー表示、Gitステータス表示が可能。 | 標準環境にはないため、別途インストールが必要。 |
| `vdir` | GNU Coreutils | `ls -l -b` と同等。詳細表示がデフォルト。 | 同上。 |

## 連携例
`ls` は単体よりも、パイプラインで他コマンドと組み合わせることで真価を発揮します。

1. **特定の拡張子をカウントする**
   `ls -1 *.log | wc -l`
2. **容量の大きい順にソートして上位を表示**
   `ls -lS | head -n 10`
3. **更新日時でソートし、特定の文字列を含むファイルを抽出**
   `ls -lt | grep "config"`

## オプション詳解（主要なものは⭐️）

| オプション                   | 役割                                              | 備考                                                       |
|:---------------------------- |:------------------------------------------------- |:---------------------------------------------------------- |
| `-a`, `--all` ⭐️            | 隠しファイル（`.`で始まるもの）を含めて全て表示。 | 設定ファイル（.bashrc等）の確認に必須。                    |
| `-l` ⭐️                     | ロングフォーマット（詳細表示）。                  | 権限、所有者、サイズ、更新日時を表示。                     |
| `-h`, `--human-readable` ⭐️ | ファイルサイズを KB, MB, GB で読みやすく表示。    | `-l` と併用。                                              |
| `-t` ⭐️                     | 更新日時の新しい順にソート。                      | 直近の変更ファイルを探す際に使用。                         |
| `-S`                         | ファイルサイズが大きい順にソート。                | ディスク容量圧迫の調査に使用。                             |
| `-R`, `--recursive` ⭐️      | ディレクトリ内を再帰的に表示。                    | 構造全体の把握に使用するが、階層が深いと出力が膨大になる。 |
| `-F`, `--classify`           | ファイル種別を記号で付与。                        | `/` (dir), `*` (exec), `@` (link) 等。                     |
| `-i`, `--inode`              | iノード番号を表示。                               | ハードリンクの特定や、ファイルシステム破損の調査に使用。   |
| `-d`, `--directory`          | ディレクトリ自体を表示。                          | ディレクトリ自体の権限を確認したい時に使用。               |
| `--color[=WHEN]`             | 出力に色をつける。                                | `auto`, `always`, `never` を指定可能。                     |
| `-u`                         | アクセス日時（atime）でソート。                   | フォレンジック調査時に重要。                               |
| `-c`                         | 属性変更日時（ctime）でソート。                   | 権限変更などの追跡に使用。                                 || 

## 業務シナリオ別詳細

### 1. インフラ運用・トラブルシューティング
**シナリオ: 特定ディレクトリの容量圧迫調査と権限不備の特定**
サーバー構築時やディスクフル警告時、どのファイルが容量を占有しているか、またはアプリケーションが書き込めない原因（権限設定）を調査します。

- **実行コマンド**: `ls -lhS /var/log` (サイズ順・読みやすい単位)
- **実務ログ例**:
```bash
user@infra-srv:~$ ls -lhS /var/log/nginx
total 4.2G
-rw-r--r-- 1 root root 3.8G Feb 25 23:59 access.log.1
-rw-r--r-- 1 root root 350M Feb 26 20:00 access.log
-rw-r----- 1 root adm   50M Feb 26 19:30 error.log
drwxr-xr-x 2 root root 4.0K Feb 01 12:00 old_logs
````

> **知見**: `access.log.1` が巨大化しており、ログローテーションの設定見直しが必要であることが一目で分かります。

---

### 2. ブルーチーム視点（ログ解析・フォレンジック）

**シナリオ: 不審なファイルの作成時刻と属性の確認**

侵害調査において、攻撃者が設置したバックドアやツールを特定します。特に「いつ変更されたか」はタイムライン分析の鍵です。

- **実行コマンド**: `ls -la --time-style=long-iso --full-time /dev/shm`
    
- **実務ログ例**:

```bash
user@victim-srv:~$ ls -la --time-style=long-iso /dev/shm
total 12
drwxrwxrwt  2 root root   80 2026-02-26 14:20:15.000000000 +0900 .
drwxr-xr-x 19 root root 3840 2026-02-26 09:00:02.000000000 +0900 ..
-rwxr-xr-x  1 www-data www-data 8421 2026-02-26 14:18:32.000000000 +0900 .reversetcp
-rw-r--r--  1 www-data www-data   15 2026-02-26 14:19:05.000000000 +0900 k.sh
```

> **知見**: `/dev/shm`（メモリ上のディレクトリ）に `www-data` 権限で不審な実行ファイル `.reversetcp` が存在。作成時刻からWeb脆弱性を突かれた可能性を特定します。

---

### 3. レッドチーム視点（列挙・権限昇格）

**シナリオ: SUIDバイナリの探索と隠し設定ファイルの列挙**

OSCP等のラボ環境で、システム内の「弱い設定」を探し出します。

- **実行コマンド**:
    
    1. `ls -la /home/user` (隠しファイルの列挙)
        
    2. `ls -l $(which python3)` (実行ファイルの権限確認)
        
- **実務ログ例**:

```bash
user@target-box:~$ ls -laR /home/user/conf 2>/dev/null
/home/user/conf:
total 16
drwxr-xr-x 2 user user 4096 Feb 26 20:45 .
drwxr-xr-x 3 user user 4096 Feb 26 20:45 ..
-rw------- 1 root root   45 Feb 26 20:40 .backup_pass
-rw-r--r-- 1 user user  120 Feb 26 20:45 config.yml
```

> **知見**: 本来一般ユーザーは見れないはずの `.backup_pass` が、ディレクトリのパーミッション設定ミスにより存在が露見（読み取りは不可でも存在を知ることは偵察の第一歩）。また、SUIDビットが立っているバイナリ（例: `-rwsr-xr-x`）を `ls -l` で確認し、GTFOBinsへ繋げます。

## エラー対処・環境変数・セキュリティ

### 1. よくあるエラーと対処法
| エラーメッセージ | 原因 | 対策 |
| :--- | :--- | :--- |
| `ls: cannot open directory '.': Permission denied` | 実行ユーザーにディレクトリの「読み取り(r)」権限がない。 | `sudo` を使用するか、上位ディレクトリの権限を確認。 |
| `ls: cannot access 'file': No such file or directory` | 指定したパスが誤っている、またはタイポ。 | `pwd` で現在地を確認し、相対/絶対パスを再考。 |
| `ls: memory exhausted` | 非常に大量（数百万単位）のファイルがあるディレクトリで実行。 | `find . -maxdepth 1` や `printf '%s\n' *` 等で代用を検討。 |

### 2. 環境変数とエイリアス
`ls` の挙動はシェルの設定に強く依存します。

- **`LS_COLORS`**: ファイル種別ごとの色を指定。`dircolors` コマンドで生成。
- **標準的なエイリアス**:
  多くのディストリビューションでは `~/.bashrc` に以下が設定されています。

  ```bash
  alias ls='ls --color=auto'
  alias ll='ls -alF'
  alias la='ls -A'
  alias l='ls -CF'
  ```
### 3. セキュリティ考慮事項（GTFOBins / 権限昇格）

`ls` 自体でシェルを取ることは稀ですが、情報収集において致命的な役割を果たします。

- **GTFOBins (Read Files)**:
    
    もし `ls` に SUID ビットが設定されている、または `sudo` 権限がパスワードなしで与えられている場合、**本来アクセスできないディレクトリの内容を閲覧可能**です。
    
    ```bash
    # 特権昇格への足がかり（シャドウファイルの存在確認や他ユーザーのSSH鍵探し）
    sudo ls -la /root/
    sudo ls -la /etc/shadow
    ```
    
- **Information Disclosure**:
    
    `.history` や `.bash_history`、`.ssh/config`、`.env` ファイルが `ls -a` で発見されることで、二次的な攻撃（横展開）の材料となります。
    
### 4. 実務上の注意点

- **スクリプト内での使用禁止**:
    
    シェルスクリプト内でファイル一覧を取得する場合、`ls` の出力をパース（`for i in $(ls)`）するのは**非推奨**です。ファイル名にスペースや特殊文字が含まれると正しく処理できないため、**Globbing**（`for i in *`）を使用してください。
    
- **タイムスタンプの罠**:
    
    `ls -l` で表示されるのは「最後に修正された時間（mtime）」です。攻撃者が `touch` コマンドで偽装（Timestomping）している可能性があるため、フォレンジックでは `ctime`（属性変更時間）も併せて確認する必要があります。
    
    ```bash
    ls -lc  # ctimeを表示
    ```
    
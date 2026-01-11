---
tags:
  - 'wmic'
  - 'cmd'
  - 'cheatsheet'
title: 'wmic - Windows Management Instrumentation コマンドライン'
summary: 'WMI (Windows Management Instrumentation) を通じて、システムの詳細な情報取得や管理操作を行うための強力なコマンドラインインターフェースです。（非推奨）'
related:
  - 'systeminfo'
  - 'tasklist'
  - 'sc'
  - 'powershell'
---

# `wmic` - Windows Management Instrumentation コマンドライン

## 概要

`wmic` (Windows Management Instrumentation Command-line) は、WMIを利用して、ローカルまたはリモートのWindowsシステムのハードウェア、ソフトウェア、OSコンポーネントに関する膨大な情報を取得したり、一部を操作したりするための強力なコマンドラインツールです。SQLに似た構文で、`wmic process list full` のように「エイリアス」と「動詞」を組み合わせて使用します。

**⚠️ 重要**: `wmic` は Windows 10, version 21H1 から**非推奨 (deprecated)** となっています。将来のWindowsリリースでは削除される可能性があります。現在、同様の機能は **PowerShell** を使用することが強く推奨されています。

(出自: `Windows標準搭載`)

## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 (あれば) |
| :--- | :--- | :--- |
| **`systeminfo`, `tasklist`** | これらのコマンドは特定の情報を要約して表示します。`wmic` は、プロセスのコマンドライン引数や、マザーボードのシリアル番号など、はるかに詳細で広範な情報を取得できます。 | `wmic` (詳細情報) |
| ⭐ **PowerShell** | `Get-CimInstance`, `Get-WmiObject` などのコマンドレットが `wmic` の後継です。取得した情報をオブジェクトとして扱えるため、フィルタリング、加工、後続処理が圧倒的に強力です。 | PowerShell |

## よく連携されるコマンド

`wmic` は、取得した情報を `for` ループで処理したり、`findstr` でフィルタリングしたりと、バッチスクリプト内で多用されます。

### シナリオ例: プロセスのコマンドライン引数を取得する (ブルーチーム視点)

* **目的**: `svchost.exe` のような汎用的なプロセスが、どのようなパラメータで起動されているかを特定する。
* **連携コマンド**: `findstr`
* **解説**: `tasklist` では表示されないプロセスの「コマンドライン引数」は、マルウェアの活動を特定する上で極めて重要な情報です。`wmic process get name,commandline` を使うことでこれを取得できます。
* **コマンド例**:

    ```cmd
    REM 全てのプロセスの名前とコマンドライン引数を表示
    wmic process get name,commandline

    REM 'svchost.exe' のコマンドラインのみをフィルタリング
    wmic process get name,commandline | findstr /I "svchost.exe"
    ```

## スイッチ/オプション説明 (エイリアスと動詞)

`wmic` は `wmic [global switches] <alias> [where clause] <verb> [verb parameters]` の形式で使用します。

| グローバルスイッチ | 説明 |
| :--- | :--- |
| `/node:<"server">` | リモートコンピュータを指定します。 |
| `/user:<"user">` | ユーザー名を指定します。 |
| `/password:<"pass">` | パスワードを指定します。 |
| `/output:<file>` | 出力をファイルにリダイレクトします。 |
| `/format:<style>` | 出力形式を指定します (`csv`, `htable` など)。 |

| よく使われるエイリアス | 説明 |
| :--- | :--- |
| ⭐ **`process`** | システム上のプロセスを管理します。 |
| ⭐ **`service`** | Windowsサービスを管理します。 |
| ⭐ **`qfe`** | 適用済みの修正プログラム (Quick Fix Engineering) を表示します。 |
| **`os`** | オペレーティングシステムの情報を表示します。 |
| **`cpu`** | CPUの詳細情報を表示します。 |
| **`diskdrive`** | 物理ディスクドライブの情報を表示します。 |
| **`startup`** | 自動実行されるアプリケーションの一覧を表示します。 |

| よく使われる動詞 | 説明 |
| :--- | :--- |
| ⭐ **`list`** | データのインスタンスを一覧表示します。 |
| ⭐ **`get`** | 特定のプロパティの値を取得します。 |
| **`call`** | オブジェクトのメソッドを実行します (`create`, `terminate`など)。 |
| **`set`** | 書き込み可能なプロパティの値を設定します。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: PCのシリアル番号をリモートで取得する。
* **組み合わせ**: `wmic /node:<server> bios get serialnumber`
* **解説**: 資産管理のために、物理的にPCに触れることなく、リモートからシリアル番号などのハードウェア情報を収集します。
* **例**:

    ```cmd
    REM リモートPC 'PC-001' のBIOSシリアル番号を取得
    wmic /node:"PC-001" bios get serialnumber
    ```

### 2. ブルーチーム視点

* **タスク**: **ライブレスポンス**。不審なプロセスの親プロセスID (PPID) とコマンドライン引数を特定する。
* **組み合わせ**: `wmic process get name,processid,parentprocessid,commandline`
* **解説**: **インシデント調査の基本**。プロセスがどのように起動されたか（親プロセスは何か）、どのような引数で実行されているか（コマンドラインは何か）を特定することで、攻撃の連鎖を追跡します。
* **例**:

    ```cmd
    REM プロセスツリーを調査するために詳細情報をCSVに出力
    wmic process get name,processid,parentprocessid,commandline /format:csv > process_details.csv
    ```

### 3. レッドチーム視点

* **タスク**: **ラテラルムーブメント (Lateral Movement)**。`wmic` を使ってリモートのホストでプロセスを実行する。
* **組み合わせ**: `/node:<server> process call create`
* **解説**: 攻撃者は管理者権限を奪取した後、`psexec` の代替として `wmic` を使って、ネットワーク内の他のコンピュータ上で任意のコマンドやペイロードを実行します。`wmic` はWindows標準搭載であり、ファイアウォールを通過しやすい (RPC/DCOMを利用) ため、ラテラルムーブメントの手法として多用されます。
* **例**:

    ```cmd
    REM リモートサーバー 'SRV-02' 上で calc.exe を起動
    wmic /node:"SRV-02" process call create "calc.exe"
    ```

## エラーメッセージとトラブルシューティング

### よくあるエラーメッセージ

1. **エラーメッセージ**: `ERROR: Description = Access is denied.`
    * **考えられる原因**: WMI名前空間へのアクセス権限がないか、リモート実行時にファイアウォールでブロックされています。
    * **解決策**: コマンドプロンプトを「管理者として実行」します。リモートの場合は、ターゲットのリモート管理 (WMI) がファイアウォールで許可されていることを確認してください。

## セキュリティに関する考慮事項

### 脆弱性と悪用事例

* **脆弱性**: `wmic` の**リモート実行機能**と**詳細な情報収集能力**が悪用されます。
* **悪用シナリオ**: レッドチームのシナリオで述べた通り、`wmic` はラテラルムーブメントのための強力なツールです。また、アンチウイルス製品のアンインストール、シャドウコピーの削除、イベントログの操作など、様々な悪意のある活動に利用されます。

### LOLBAS (Living Off The Land Binaries and Scripts) における利用例

* **機能**: **偵察 (Reconnaissance)**, **コード実行 (Execution)**, **ラテラルムーブメント (Lateral Movement)**
* **参照**: [https://lolbas-project.github.io/lolbas/Binaries/Wmic/](https://lolbas-project.github.io/lolbas/Binaries/Wmic/)
* **解説**: `wmic.exe` は、システム管理と攻撃の両面で極めて多機能であるため、LOLBASの中でも最も強力で頻繁に悪用されるバイナリの1つです。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: 最小権限の原則を徹底し、不要なアカウントにリモートWMI実行権限を与えない。ホストベースのファイアウォールで、信頼されていないホストからのWMI (DCOM/RPC) 通信をブロックする。
* **Detection (検知)**: `wmic.exe` の実行、特に `/node` スイッチを伴うリモート実行や、`process call create` のようなプロセス作成コマンドを、プロセス監査ログ (イベントID 4688) やEDRで厳重に監視する。

## 注意点・補足

* **非推奨**: `wmic` は非推奨であり、PowerShellへの移行が推奨されています。PowerShellはより安全で、強力なログ機能を備えています。

---
[CMDインデックスに戻る](../../cmd_index.md)

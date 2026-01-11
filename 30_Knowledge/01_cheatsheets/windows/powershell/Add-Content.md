---
tags:
  - 'Add-Content'
  - 'powershell'
  - 'cheatsheet'
title: 'Add-Content - ファイルに内容を追記する'
summary: '指定したアイテム（主にファイル）に、文字列やオブジェクトなどの内容を追記します。'
related:
  - 'Get-Content'
  - 'Set-Content'
  - 'Out-File'
---

# `Add-Content` - ファイルに内容を追記する

## 概要

`Add-Content` コマンドレットは、指定したファイルに内容を追記（末尾に追加）します。`Value` パラメータで指定した文字列や、パイプラインから渡されたオブジェクトをファイルの末尾に追加するため、ログファイルへの記録や、既存のファイルへの情報追加など、様々なスクリプトで利用されます。ファイルが存在しない場合は、新しく作成されます。

## 類似コマンドレット/コマンドとの差異

| 類似コマンドレット/コマンド | 差異 / 使い分けのポイント |
| :--- | :--- |
| **`Set-Content`** | `Set-Content` はファイルの内容を**上書き**します。`Add-Content` は**追記**します。 |
| **`Out-File -Append`** | `Out-File -Append` も追記を行いますが、よりフォーマットされた出力を扱うのに適しています。`Add-Content` はより高速で、単純な文字列の追加に向いています。 |
| **`>>` (CMD)** | CMDのリダイレクション演算子 `>>` と同じく追記を行いますが、`Add-Content` はエンコーディングの指定など、より多くのオプションを持っています。 |

## よく連携されるコマンドレット

### シナリオ例: 実行中のプロセス一覧をログファイルに記録する (インフラ構築・運用視点)

* **目的**: 現在実行中のプロセスの一覧を取得し、その情報を既存のログファイルに追記する。
* **連携コマンドレット**: `Get-Process`, `Out-String`
* **解説**: `Get-Process` で取得したプロセスオブジェクトを、そのまま `Add-Content` に渡すと、プロセス名だけが追記されます。より詳細な情報を整形して追記するために、一度 `Out-String` を通して整形された文字列に変換してから `Add-Content` に渡します。
* **コマンド例**:

    ```powershell
    # 現在の日時ヘッダーをログに追加
    Add-Content -Path C:\Logs\proc_log.txt -Value "--- Process list at $(Get-Date) ---"

    # プロセス一覧を整形してログに追記
    Get-Process | Out-String | Add-Content -Path C:\Logs\proc_log.txt
    ```

## パラメータ説明

| パラメータ | 型 | 説明 |
| :--- | :--- | :--- |
| ⭐ **`-Path`** | `String[]` | 内容を追記するファイルのパスを指定します。必須パラメータです。 |
| ⭐ **`-Value`** | `Object[]` | 追記する内容を指定します。パイプラインからの入力がない場合、必須です。 |
| **`-Encoding`** | `String` | ファイルのエンコーディングを指定します。(`UTF8`, `Unicode`, `ASCII` など) |
| **`-Force`** | `SwitchParameter` | 読み取り専用のファイルにも書き込みを試みます。 |
| **`-Credential`** | `PSCredential` | ファイルへの書き込みに別のユーザーの資格情報を使用します。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: スクリプトの実行ログを記録する。
* **組み合わせ**: `Add-Content -Path <LogFile> -Value <Message>`
* **解説**: スクリプトの各ステップで、タイムスタンプと共に処理内容や結果をログファイルに追記していく、最も基本的な使い方です。
* **例**:

    ```powershell
    # ログファイルパスを変数に格納
    $logFile = "C:\Scripts\Logs\daily_maintenance.log"

    # スクリプトの開始を記録
    Add-Content -Path $logFile -Value "$(Get-Date): Script started."

    # ... (何らかの処理) ...

    # スクリプトの終了を記録
    Add-Content -Path $logFile -Value "$(Get-Date): Script finished."
    ```

### 2. ブルーチーム視点

* **タスク**: 調査中に発見したIOC (Indicator of Compromise) を記録ファイルに追記する。
* **組み合わせ**: `Add-Content`
* **解説**: フォレンジック調査中に、不審なIPアドレス、ファイルハッシュ、ユーザー名などを発見した場合、それらを1つのファイルに追記していくことで、調査記録を作成します。
* **例**:

    ```powershell
    # 発見した不審なIPアドレスをIOCリストに追記
    Add-Content -Path D:\Case001\ioc_list.txt -Value "Suspicious IP: 198.51.100.50"
    ```

### 3. レッドチーム視点

* **タスク**: **防御回避 (Defense Evasion)** のため、`hosts` ファイルを改ざんしてDNS解決を乗っ取る。
* **組み合わせ**: `Add-Content`
* **解説**: 攻撃者は管理者権限を奪取した後、`Add-Content` を使って `hosts` ファイルに偽のエントリを追記します。これにより、`windowsupdate.microsoft.com` のような正規のドメインへの通信を、自身が制御する悪意のあるサーバーにリダイレクトさせることができます。
* **例**:

    ```powershell
    # windowsupdate.microsoft.com を攻撃者のサーバー (10.10.10.10) に向けるエントリを追記
    Add-Content -Path C:\Windows\System32\drivers\etc\hosts -Value "10.10.10.10 windowsupdate.microsoft.com"
    ```

## エラーメッセージとトラブルシューティング

### よくあるエラーメッセージ

1. **エラーメッセージ**: `Add-Content: Access to the path 'C:\Program Files\...' is denied.`
    * **考えられる原因**: 書き込もうとしているファイルやディレクトリに対する権限がありません。
    * **解決策**: PowerShellを「管理者として実行」するか、書き込み権限のあるディレクトリを使用してください。

## セキュリティに関する考慮事項

### 悪用事例

* **悪用シナリオ**: レッドチームのシナリオで述べた通り、`Add-Content` は `hosts` ファイル、設定ファイル (`web.config`など)、スクリプト (`.ps1`, `.bat`) などを改ざんし、システムの動作を変更したり、バックドアを仕掛けたりするために悪用されます。

### LOLBASにおける利用例

* **機能**: **防御回避 (Defense Evasion)**
* **解説**: `Add-Content` は PowerShell の組み込みコマンドレットであり、その実行自体は警告を発しません。攻撃者はこれを利用して、ディスク上のファイルを変更し、検知を回避しながら攻撃活動を行います。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: ファイルシステムに適切なACL（アクセス制御リスト）を設定し、重要なファイル（特に `hosts` ファイルやシステムディレクトリ内のファイル）への書き込み権限を最小限に抑える。
* **Detection (検知)**: ファイル整合性監視 (FIM) ツールや `Sysmon` (イベントID 11: FileCreate) を使って、重要なファイルへの変更を監視する。PowerShellのスクリプトブロックロギングを有効化し、`Add-Content` を含む不審なコマンドの実行を記録する。

## 注意点・補足

* **エンコーディング**: `Add-Content` は、既存のファイルに追記する際にそのファイルのエンコーディングを維持しようとします。新規ファイルの場合、デフォルトのエンコーディング (PowerShellのバージョンによって異なる) が使用されます。意図しない文字化けを防ぐため、必要に応じて `-Encoding` パラメータを明示的に指定することが推奨されます。

---
[PowerShellインデックスに戻る](../../powershell_index.md)

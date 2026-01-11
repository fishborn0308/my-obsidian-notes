---
tags:
  - 'Out-File'
  - 'powershell'
  - 'cheatsheet'
title: 'Out-File - コマンドの出力をファイルに送信する'
summary: 'PowerShellコマンドの出力を受け取り、その内容をテキストファイルに保存します。'
related:
  - 'Set-Content'
  - 'Add-Content'
  - 'Export-Csv'
  - '>'
---

# `Out-File` - コマンドの出力をファイルに送信する

## 概要

`Out-File` コマンドレットは、パイプラインから渡されたオブジェクトを受け取り、そのオブジェクトを文字列に変換して、指定したテキストファイルに保存します。これは、CMDのリダイレクション演算子 (`>`) と似ていますが、エンコーディング、幅、追記/上書きなどをより細かく制御できるのが特徴です。

スクリプトの実行結果や、`Get-Process`, `Get-Service` などのコマンドレットの出力を、人間が読める形式でファイルに記録するために使用されます。

## 類似コマンドレット/コマンドとの差異

| 類似コマンドレット/コマンド | 差異 / 使い分けのポイント |
| :--- | :--- |
| **`>` (リダイレクション)** | `>` は `Out-File` の簡易版です。`Out-File` は `-Encoding`, `-Width`, `-Append` などのパラメータを使って、より詳細な出力制御が可能です。 |
| **`Set-Content` / `Add-Content`** | `Set-Content` や `Add-Content` は、主に文字列や文字列の配列をファイルに書き込むのに適しています。`Out-File` は、様々な種類のオブジェクトを受け取り、PowerShellのフォーマットシステムを使って整形された文字列として出力するのが得意です。 |
| **`Export-Csv`** | `Export-Csv` は、オブジェクトを**構造化されたCSV形式**で保存することに特化しています。`Out-File` は、コンソールに表示されるような**フォーマットされたテキスト**として保存します。 |

## よく連携されるコマンドレット

### シナリオ例: 実行中のプロセス一覧をテキストファイルに保存する (インフラ構築・運用視点)

* **目的**: システムで実行中のプロセスの一覧を取得し、その出力をそのままテキストファイルに保存して記録する。
* **連携コマンドレット**: `Get-Process`
* **解説**: `Get-Process` で取得したプロセスオブジェクトのコレクションを、パイプライン経由で `Out-File` に渡します。`Out-File` は、コンソールに表示されるのと同じ表形式のテキストをファイルに書き込みます。
* **コマンド例**:

    ```powershell
    # Get-Process の出力を process_list.txt に保存
    Get-Process | Out-File -FilePath "C:\Logs\process_list.txt" -Encoding utf8
    ```

## パラメータ説明

| パラメータ | 型 | 説明 |
| :--- | :--- | :--- |
| ⭐ **`-FilePath`** | `String` | 出力先のファイルパスを指定します。 |
| **`-InputObject`** | `PSObject` | ファイルに書き込むオブジェクトを指定します。通常はパイプラインから渡されます。 |
| ⭐ **`-Encoding`** | `String` | ファイルの文字エンコーディングを指定します。日本語環境では `utf8` や `Default` を指定することが多いです。 |
| ⭐ **`-Append`** | `SwitchParameter` | 既存のファイルの内容を上書きせず、末尾に**追記**します。 |
| **`-Width`** | `Int32` | 出力ファイルの1行の幅（文字数）を指定します。これを超えると折り返されたり、切り捨てられたりすることがあります。 |
| ⭐ **`-Force`** | `SwitchParameter` | 読み取り専用のファイルへの上書きや、存在しないディレクトリパスへのファイル作成を強制的に行います。 |
| **`-NoClobber`** | `SwitchParameter` | ファイルが既に存在する場合に、上書きを防ぎエラーを発生させます。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: スクリプトの実行ログを、既存のログファイルに追記していく。
* **組み合わせ**: `... | Out-File -FilePath <LogFile> -Append`
* **解説**: `-Append` スイッチを使うことで、コマンドの実行結果を時系列で1つのログファイルに記録し続けることができます。
* **例**:

    ```powershell
    # サーバーの空きディスク容量を daily_report.log に追記
    Get-CimInstance -ClassName Win32_LogicalDisk |
    Select-Object DeviceID, @{Name="Size(GB)";Expression={$_.Size / 1GB}}, @{Name="FreeSpace(GB)";Expression={$_.FreeSpace / 1GB}} |
    Out-File -FilePath "C:\Logs\daily_report.log" -Append
    ```

### 2. ブルーチーム視点

* **タスク**: **ライブレスポンス**。侵害されたホストの現在のネットワーク接続状態を、証拠としてファイルに保存する。
* **組み合わせ**: `Get-NetTCPConnection | Out-File <OutputFile>`
* **解説**: `Get-NetTCPConnection` のようなコマンドレットの実行結果は揮発性であるため、`Out-File` を使ってその時点での状態をファイルとして保存し、後の詳細なフォレンジック分析のために保全します。
* **例**:

    ```powershell
    # 現在のTCP接続一覧を net_connections.txt として保存
    Get-NetTCPConnection | Out-File -FilePath "C:\Forensics\net_connections.txt"
    ```

### 3. レッドチーム視点

* **タスク**: **偵察 (Reconnaissance)** 結果の保存、または**ペイロードの作成**。
* **組み合わせ**: `... | Out-File <File>`
* **解説**:
  * **偵察**: 攻撃者は、`Get-Process`, `Get-LocalUser` などで収集した情報を `Out-File` で一時ファイルに保存し、後から分析したり、データ窃取のために準備したりします。
  * **ペイロード作成**: `echo` のように、`Out-File` を使って悪意のあるスクリプト (`.ps1`, `.bat`) をターゲットシステム上に書き出すことも可能です。
* **例**:

    ```powershell
    # Active Directoryのユーザーリストを %TEMP% に保存
    Get-ADUser -Filter * | Select-Object Name | Out-File -FilePath "$env:TEMP\ad_users.txt"
    ```

## エラーメッセージとトラブルシューティング

### よくあるエラーメッセージ

1. **エラーメッセージ**: `Out-File: Access to the path '...' is denied.`
    * **考えられる原因**: 出力先ディレクトリへの書き込み権限がありません。
    * **解決策**: PowerShellを「管理者として実行」するか、書き込み権限のあるパスを指定してください。

## セキュリティに関する考慮事項

### 悪用事例

* **偵察データの保存**: レッドチームのシナリオで述べた通り、`Out-File` は攻撃者が収集した偵察データをファイルに保存し、分析や窃取の準備をするために利用されます。
* **悪意のあるファイルの作成**: 攻撃者は、`Invoke-WebRequest` でダウンロードしたペイロードの内容を `Out-File` でディスクに書き出したり、文字列を `Out-File` で `.ps1` ファイルとして保存したりすることで、マルウェアをシステム上に展開します。

### LOLBASにおける利用例

* **機能**: **データ収集 (Data Collection)**, **防御回避 (Defense Evasion)**
* **解説**: `Out-File` は PowerShell の標準機能であり、攻撃者が検知を回避しながらファイルを作成・変更するために利用されます（Living off the Land）。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: AppLockerやWDAC (Windows Defender Application Control) を使用して、信頼できないディレクトリ（`%TEMP%`, `C:\Users\Public`など）からのスクリプトや実行ファイルの実行を禁止する。
* **Detection (検知)**: `Sysmon` (イベントID 11: FileCreate) を使って、予期せぬ場所へのファイル作成（特にスクリプト拡張子を持つファイル）を監視する。PowerShellのスクリプトブロックロギング (イベントID 4104) を有効化し、`Out-File` の実行、特にその入力となるコマンドや書き込み先を記録・監視する。

## 注意点・補足

* **デフォルトエンコーディング**: `Out-File` のデフォルトの文字エンコーディングは、PowerShellのバージョンによって異なります（例: PowerShell 5.1では `Unicode (UTF-16LE)`）。異なるシステムやアプリケーションでファイルを扱う場合は、`-Encoding` パラメータで `utf8` などを明示的に指定することが推奨されます。

---
[PowerShellインデックスに戻る](../../powershell_index.md)

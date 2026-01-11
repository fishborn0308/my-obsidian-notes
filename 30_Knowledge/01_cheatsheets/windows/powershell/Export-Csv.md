---
tags:
  - 'Export-Csv'
  - 'powershell'
  - 'cheatsheet'
title: 'Export-Csv - オブジェクトをCSV形式でファイルにエクスポートする'
summary: 'PowerShellオブジェクトのコレクションを、CSV (Comma-Separated Value) 形式の文字列に変換し、ファイルに保存します。'
related:
  - 'Import-Csv'
  - 'ConvertTo-Csv'
  - 'Out-File'
---

# `Export-Csv` - オブジェクトをCSV形式でファイルにエクスポートする

## 概要

`Export-Csv` コマンドレットは、PowerShellオブジェクト（またはオブジェクトの配列）を受け取り、それらをCSV形式のファイルとして保存します。このコマンドレットは、スクリプトで収集したインベントリ情報、ログデータ、監査結果などを、Excelなどの表計算ソフトで簡単に開いて分析できる形式で出力するために広く使用されます。

各オブジェクトのプロパティがCSVの列ヘッダーとなり、各オブジェクトがCSVの行として表現されます。

## 類似コマンドレット/コマンドとの差異

| 類似コマンドレット/コマンド | 差異 / 使い分けのポイント |
| :--- | :--- |
| **`ConvertTo-Csv`** | `ConvertTo-Csv` はオブジェクトをCSV形式の**文字列**に変換して、コンソールやパイプラインに出力します。`Export-Csv` はオブジェクトをCSVとして直接**ファイルに保存**します。`... \| ConvertTo-Csv \| Out-File <file>` と `... \| Export-Csv <file>`は似ていますが、`Export-Csv` の方が専用でオプションも豊富です。 |
| **`Out-File`** | `Out-File` は、オブジェクトのコンソール表示に近い形式でファイルに書き込みます。`Export-Csv` は、表形式のデータ構造を維持したCSV形式での保存に特化しています。 |

## よく連携されるコマンドレット

### シナリオ例: 実行中のサービス一覧をCSVファイルとして保存する (インフラ構築・運用視点)

* **目的**: システムで実行中の全てのサービスのリストと、その状態や表示名などの情報を取得し、CSVファイルとして保存する。
* **連携コマンドレット**: `Get-Service`, `Select-Object`
* **解説**: `Get-Service` でサービスオブジェクトのコレクションを取得し、パイプラインで `Select-Object` に渡して必要なプロパティ（列）だけを選択・整形します。最後に、その結果を `Export-Csv` に渡してファイルに保存します。
* **コマンド例**:

    ```powershell
    # サービスの一覧から必要なプロパティを選択し、CSVファイルにエクスポート
    Get-Service | Select-Object -Property Name, DisplayName, Status, StartType | Export-Csv -Path C:\Inventory\services.csv -NoTypeInformation -Encoding UTF8
    ```

## パラメータ説明

| パラメータ | 型 | 説明 |
| :--- | :--- | :--- |
| ⭐ **`-Path`** | `String` | 出力先のCSVファイルパスを指定します。必須パラメータです。 |
| ⭐ **`-InputObject`** | `PSObject` | CSVにエクスポートするオブジェクトを指定します。通常はパイプラインから渡されます。 |
| ⭐ **`-NoTypeInformation`** | `SwitchParameter` | CSVファイルの先頭に `#TYPE` から始まる型情報ヘッダーを**含めません**。Excelなどで直接開く場合は、このスイッチを付けるのが一般的です。 |
| ⭐ **`-Encoding`** | `String` | ファイルの文字エンコーディングを指定します。日本語環境でExcelで開く場合は `UTF8` や `UTF8BOM` を指定することが多いです。 |
| **`-Append`** | `SwitchParameter` | 既存のCSVファイルの末尾に出力を追記します。 |
| **`-Delimiter`** | `Char` | 区切り文字を指定します。デフォルトはカンマ (`,`) です。 |
| **`-Force`** | `SwitchParameter` | 読み取り専用ファイルへの上書きなどを強制的に行います。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: 複数のサーバーの基本情報（ホスト名、OS、IPアドレス）を取得し、インベントリリストとして保存する。
* **組み合わせ**: `Get-ComputerInfo`, `Select-Object`, `Export-Csv`
* **解説**: 複数のマシンに対して `Invoke-Command` を使い、各マシンから収集した情報を `Export-Csv` で1つのCSVファイルに集約します。
* **例**:

    ```powershell
    # computer_list.txt に記載された全サーバーの情報を収集
    $computers = Get-Content -Path .\computer_list.txt
    Invoke-Command -ComputerName $computers -ScriptBlock {
        Get-ComputerInfo | Select-Object CsName, OsName, OsOperatingSystemSKU
    } | Export-Csv -Path C:\Inventory\server_inventory.csv -NoTypeInformation
    ```

### 2. ブルーチーム視点

* **タスク**: **ログ分析と証拠保全**。特定の時間範囲のセキュリティイベントログを取得し、分析のためにCSVとしてエクスポートする。
* **組み合わせ**: `Get-WinEvent`, `Export-Csv`
* **解説**: `Get-WinEvent` で取得したイベントオブジェクトはプロパティが複雑なため、`Select-Object` で必要なプロパティ（`TimeCreated`, `Id`, `Message`など）を整形してから `Export-Csv` に渡すのが一般的です。
* **例**:

    ```powershell
    # 直近24時間のログオン失敗イベント (ID 4625) をCSVにエクスポート
    $yesterday = (Get-Date).AddDays(-1)
    Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4625; StartTime=$yesterday} |
    Select-Object TimeCreated, Id, Message |
    Export-Csv -Path D:\Logs\LogonFailures.csv -NoTypeInformation
    ```

### 3. レッドチーム視点

* **タスク**: **データ収集 (Data Collection) / データ窃取 (Data Exfiltration)**。
* **組み合わせ**: `Get-ChildItem`, `Get-LocalUser` など + `Export-Csv`
* **解説**: 攻撃者は侵入後、システム内の機密情報（ファイルリスト、ユーザーリスト、設定情報など）を収集します。`Export-Csv` を使うことで、これらの構造化されたデータを単一のファイルにまとめ、外部に持ち出す（窃取する）準備をします。
* **例**:

    ```powershell
    # ローカルユーザーの一覧をCSV形式で %TEMP% に保存
    Get-LocalUser | Export-Csv -Path "$env:TEMP\users.csv" -NoTypeInformation
    ```

## エラーメッセージとトラブルシューティング

### よくあるエラーメッセージ

1. **エラーメッセージ**: `Export-Csv: Access to the path '...' is denied.`
    * **考えられる原因**: 出力先ディレクトリへの書き込み権限がありません。
    * **解決策**: PowerShellを「管理者として実行」するか、書き込み権限のあるパスを指定してください。

## セキュリティに関する考慮事項

### 悪用事例

* **データ窃取の準備**: レッドチームのシナリオで述べた通り、`Export-Csv` は、攻撃者がシステムから窃取したい情報を整理・集約するための便利なツールとして悪用されます。正規のコマンドレットであるため、実行自体が直接的なアラートに繋がりにくいです。

### LOLBASにおける利用例

* **機能**: **データ収集 (Data Collection)**
* **解説**: `Export-Csv` は PowerShell の標準機能であり、`powershell.exe` を通じて実行されます。攻撃者はこの正規の機能を利用して、検知されにくい形でシステム情報を収集し、窃取しやすい形式にまとめます。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: -
* **Detection (検知)**: PowerShellのスクリプトブロックロギング (イベントID 4104) を有効化し、`Export-Csv` の実行、特に出力先が `%TEMP%` や `C:\Users\Public` などの一時的な場所である場合や、機密情報にアクセスするコマンドレット (`Get-ADUser`, `Get-WinEvent`など) と連携している場合に監視を強化する。

## 注意点・補足

* **`-NoTypeInformation`**: このスイッチを付けないと、CSVファイルの1行目に `"#TYPE Selected.System.ServiceProcess.Service"` のような型情報が出力されます。これはPowerShellで `Import-Csv` する際には有用ですが、Excelなどで直接開く場合は不要な行となるため、付けることが推奨されます。

---
[PowerShellインデックスに戻る](../../powershell_index.md)

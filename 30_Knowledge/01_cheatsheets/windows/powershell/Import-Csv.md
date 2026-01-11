---
tags:
  - 'Import-Csv'
  - 'powershell'
  - 'cheatsheet'
title: 'Import-Csv - CSVファイルからオブジェクトを作成する'
summary: 'CSV (Comma-Separated Value) ファイルを読み込み、各行をヘッダーに基づいたプロパティを持つカスタムオブジェクトに変換します。'
related:
  - 'Export-Csv'
  - 'Get-Content'
  - 'ForEach-Object'
---

# `Import-Csv` - CSVファイルからオブジェクトを作成する

## 概要

`Import-Csv` コマンドレットは、CSV形式のファイルからデータをインポートし、CSVファイルの各行を1つのオブジェクトとして、各列をそのオブジェクトのプロパティとして扱います。デフォルトでは、CSVファイルの1行目にある列見出しを、オブジェクトのプロパティ名として使用します。

このコマンドレットは、ユーザーリスト、サーバーインベントリ、設定データなど、表形式のデータをPowerShellスクリプトに読み込み、プログラム的に処理するための中心的な役割を果たします。

このコマンドレットには、`ipcsv` という便利なエイリアスがデフォルトで設定されています。

## 類似コマンドレット/コマンドとの差異

| 類似コマンドレット/コマンド | 差異 / 使い分けのポイント |
| :--- | :--- |
| **`Get-Content`** | `Get-Content` はファイルの内容を1行ずつの**文字列配列**として読み込みます。`Import-Csv` はCSVの構造を解釈し、各行をプロパティを持つ**カスタムオブジェクト**に変換します。 |
| **`Export-Csv`** | `Export-Csv` は `Import-Csv` の逆の操作を行います。PowerShellオブジェクトをCSV形式で**ファイルに保存**します。 |
| **`ConvertFrom-Csv`** | `ConvertFrom-Csv` はCSV形式の**文字列**をオブジェクトに変換します。`Import-Csv` は**ファイル**から直接オブジェクトを作成します。`Get-Content <file> \| ConvertFrom-Csv` は `Import-Csv <file>` とほぼ同等です。 |

## よく連携されるコマンドレット

### シナリオ例: CSVのユーザーリストに基づいてActive Directoryユーザーを作成する (インフラ構築・運用視点)

* **目的**: 新入社員のリストが書かれたCSVファイルを読み込み、各行の情報に基づいてActive Directoryに新しいユーザーアカウントをバッチ作成する。
* **連携コマンドレット**: `ForEach-Object`, `New-ADUser`
* **解説**: `Import-Csv` でユーザーリストをオブジェクトのコレクションとして読み込みます。次に、`ForEach-Object` ループで各ユーザーオブジェクトを1つずつ処理し、そのプロパティ（`.UserName`, `.FirstName`など）を使って `New-ADUser` コマンドレットのパラメータに渡し、アカウントを作成します。
* **コマンド例**:

    ```powershell
    # new_users.csv をインポートし、各ユーザーをループ処理
    Import-Csv -Path "C:\HR\new_users.csv" | ForEach-Object {
        # CSVの列ヘッダーがオブジェクトのプロパティ名になる
        $samAccountName = $_.SamAccountName
        $givenName = $_.FirstName
        $surname = $_.LastName

        # 新しいADユーザーを作成
        New-ADUser -SamAccountName $samAccountName -GivenName $givenName -Surname $surname -Enabled $true
    }
    ```

## パラメータ説明

| パラメータ | 型 | 説明 |
| :--- | :--- | :--- |
| ⭐ **`-Path`** | `String[]` | インポートするCSVファイルのパスを指定します。 |
| ⭐ **`-Delimiter`** | `Char` | 列を区切る文字を指定します。デフォルトはカンマ (`,`) です。タブ区切り (TSV) の場合は `"`t"` を指定します。 |
| ⭐ **`-Header`** | `String[]` | CSVファイルにヘッダー行がない場合、このパラメータで列の名前を文字列の配列として指定します。 |
| ⭐ **`-Encoding`** | `String` | ファイルの文字エンコーディングを指定します。日本語を含むCSVを扱う際は `UTF8` や `Default` の指定が必要になることがあります。 |
| **`-UseCulture`** | `SwitchParameter` | OSの現在のカルチャで設定されているリスト区切り文字をデリミタとして使用します。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: サーバーのインベントリCSVを読み込み、特定のOSのサーバーだけをリストアップする。
* **組み合わせ**: `Import-Csv | Where-Object`
* **解説**: `Import-Csv` で取得したオブジェクトのコレクションをパイプラインで `Where-Object` に渡し、特定のプロパティ（列）の値に基づいてフィルタリングします。
* **例**:

    ```powershell
    # server_inventory.csv から 'OperatingSystem' が 'Windows Server 2022' のサーバーを検索
    Import-Csv -Path "C:\Inventory\server_inventory.csv" | Where-Object { $_.OperatingSystem -like "*Windows Server 2022*" }
    ```

### 2. ブルーチーム視点

* **タスク**: 脅威インテリジェンスから提供された、悪意のあるIPアドレスのリスト（CSV形式）を読み込み、ファイアウォールルールを作成する。
* **組み合わせ**: `Import-Csv | ForEach-Object { New-NetFirewallRule ... }`
* **解説**: IOC（侵害指標）のリストを `Import-Csv` で読み込み、各IPアドレスに対して `New-NetFirewallRule` を実行して、それらのIPからの受信通信をブロックするルールを自動的に作成します。
* **例**:

    ```powershell
    # blocklist.csv に 'IPAddress' というヘッダーでIPがリストされていると仮定
    Import-Csv -Path "C:\ThreatIntel\blocklist.csv" | ForEach-Object {
        New-NetFirewallRule -DisplayName "Block Malicious IP - $($_.IPAddress)" -Direction Inbound -Action Block -RemoteAddress $_.IPAddress
    }
    ```

### 3. レッドチーム視点

* **タスク**: **偵察 (Reconnaissance)**。収集した情報を処理する。
* **組み合わせ**: `Export-Csv | Import-Csv`
* **解説**: 攻撃者は、他のコマンド (`Get-Process`, `Get-LocalUser`など) で収集した情報を `Export-Csv` でファイルに保存しておくことがあります。後から `Import-Csv` を使ってそのデータをPowerShellに再読み込みし、フィルタリングや分析を行って次の攻撃ターゲットを絞り込みます。
* **例**:

    ```powershell
    # (以前に収集した) ユーザーリストを読み込み、無効化されていないアカウントのみを表示
    Import-Csv -Path "$env:TEMP\users.csv" | Where-Object { $_.Enabled -eq 'True' }
    ```

## エラーメッセージとトラブルシューティング

### よくあるエラーメッセージ

1. **現象**: ファイルが正しく列に分割されず、1行が1つの長いプロパティとして読み込まれる。
    * **考えられる原因**: ファイルの区切り文字がカンマ (`,`) ではない（例: セミコロン `;` やタブ）。
    * **解決策**: `-Delimiter` パラメータで正しい区切り文字を指定してください。（例: `Import-Csv -Path .\data.csv -Delimiter ";"`）

2. **現象**: 日本語の文字が文字化けする。
    * **考えられる原因**: ファイルのエンコーディングとPowerShellが想定しているエンコーディングが一致していません。
    * **解決策**: `-Encoding` パラメータで、ファイルの実際のエンコーディング（`UTF8`, `Default`など）を明示的に指定してください。

## セキュリティに関する考慮事項

### 悪用事例

* **偵察データの処理**: レッドチームのシナリオで述べた通り、`Import-Csv` は攻撃者が収集した偵察データを効率的に処理し、攻撃計画を立てるために利用されます。

### LOLBASにおける利用例

* **機能**: **データ収集 (Data Collection) / 偵察 (Reconnaissance)**
* **解説**: `Import-Csv` は PowerShell の標準機能であり、攻撃者が環境に溶け込みながら、収集したCSV形式のデータ（例: パスワードダンプの結果）を解析するために利用されます。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: -
* **Detection (検知)**: PowerShellのスクリプトブロックロギング (イベントID 4104) を有効化し、`Import-Csv` の実行、特に不審な場所にあるCSVファイルや、機密情報を含みうるファイル名のCSVを読み込んでいるケースを監視する。

## 注意点・補足

* **ヘッダー行**: `Import-Csv` は、CSVファイルの1行目が列名（ヘッダー）であることを前提としています。ヘッダーがないファイルの場合は、`-Header` パラメータで列名を指定する必要があります。

---
[PowerShellインデックスに戻る](../../powershell_index.md)

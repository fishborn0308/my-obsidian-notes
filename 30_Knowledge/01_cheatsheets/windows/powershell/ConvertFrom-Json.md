---
tags:
  - 'ConvertFrom-Json'
  - 'powershell'
  - 'cheatsheet'
title: 'ConvertFrom-Json - JSON文字列をオブジェクトに変換する'
summary: 'JSON (JavaScript Object Notation) 形式の文字列を、PowerShellで操作できるカスタムオブジェクト (.PSCustomObject) に変換します。'
related:
  - 'ConvertTo-Json'
  - 'Invoke-RestMethod'
  - 'Get-Content'
---

# `ConvertFrom-Json` - JSON文字列をオブジェクトに変換する

## 概要

`ConvertFrom-Json` コマンドレットは、JSON形式の文字列を解析し、PowerShellでネイティブに扱うことができるオブジェクト (通常は `[PSCustomObject]` や `[Hashtable]`) に変換します。Web APIからの応答データや、設定ファイルとして一般的に使用されるJSON形式のデータを、PowerShellスクリプト内で簡単に操作できるようにするために不可欠なコマンドレットです。

## 類似コマンドレット/コマンドとの差異

| 類似コマンドレット/コマンド | 差異 / 使い分けのポイント |
| :--- | :--- |
| **`ConvertTo-Json`** | `ConvertTo-Json` は `ConvertFrom-Json` の逆の操作を行います。PowerShellオブジェクトをJSON形式の**文字列**に変換します。 |
| **`Invoke-RestMethod`** | このコマンドレットはWeb APIにリクエストを送信し、返ってきたJSON応答を**自動的にPowerShellオブジェクトに変換**します。`Invoke-RestMethod ... \| ConvertFrom-Json` のように明示的にパイプする必要は通常ありません。 |

## よく連携されるコマンドレット

### シナリオ例: Web APIから取得したJSONデータを処理する (インフラ構築・運用視点)

* **目的**: Web APIからJSON形式で返されたユーザー情報（文字列）をPowerShellオブジェクトに変換し、特定のプロパティ（メールアドレスなど）にアクセスする。
* **連携コマンドレット**: `Invoke-WebRequest`, `Select-Object`
* **解説**: `Invoke-WebRequest` でAPIから生のコンテンツ（JSON文字列）を取得し、その結果をパイプで `ConvertFrom-Json` に渡してオブジェクトに変換します。その後、オブジェクトのプロパティにアクセスしたり、`Select-Object` で必要なプロパティだけを抽出したりします。
* **コマンド例**:

    ```powershell
    # Web APIからユーザーデータをJSON文字列として取得
    $jsonString = (Invoke-WebRequest -Uri "[https://api.example.com/users/1](https://api.example.com/users/1)").Content

    # JSON文字列をPowerShellオブジェクトに変換
    $userObject = $jsonString | ConvertFrom-Json

    # オブジェクトのプロパティにアクセス
    Write-Host "User Name: $($userObject.name)"
    Write-Host "Email: $($userObject.email)"
    ```

## パラメータ説明

| パラメータ | 型 | 説明 |
| :--- | :--- | :--- |
| ⭐ **`-InputObject`** | `String` | 変換するJSON文字列を指定します。パイプラインからの入力を受け取ります。 |
| **`-AsHashtable`** | `SwitchParameter` | JSONオブジェクトを `[PSCustomObject]` ではなく `[Hashtable]` として作成します。 |
| **`-Depth`** | `Int32` | 処理するオブジェクトのネストの最大深度を指定します。デフォルトは1024です。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: JSON形式の設定ファイルを読み込み、特定の設定値を取得する。
* **組み合わせ**: `Get-Content`, `ConvertFrom-Json`
* **解説**: `Get-Content -Raw` で設定ファイル全体を単一の文字列として読み込み、それを `ConvertFrom-Json` に渡してオブジェクトに変換します。これにより、`$config.Database.ConnectionString` のようにドット記法で簡単に設定値にアクセスできます。
* **例**:

    ```powershell
    # appsettings.json を読み込んでオブジェクトに変換
    $config = Get-Content -Path .\appsettings.json -Raw | ConvertFrom-Json

    # 接続文字列を取得
    $connectionString = $config.ConnectionStrings.DefaultConnection
    Write-Host "Connection String is: $connectionString"
    ```

### 2. ブルーチーム視点

* **タスク**: 脅威インテリジェンスプラットフォームのAPIから返されたJSON形式のIOC（侵害指標）情報を解析する。
* **組み合わせ**: `Invoke-RestMethod` (暗黙的) または `ConvertFrom-Json` (明示的)
* **解説**: APIからのJSON出力をオブジェクトに変換し、IPアドレスやドメイン名のリストを抽出して、ファイアウォールルールやSIEMの監視リストに登録するスクリプトを作成します。
* **例**:

    ```powershell
    # 脅威インテリジェンスAPIからIOCリストを取得
    $iocJson = (Invoke-WebRequest -Uri "[https://threat-intel.local/api/iocs](https://threat-intel.local/api/iocs)").Content
    $iocs = $iocJson | ConvertFrom-Json

    # 悪意のあるIPアドレスのリストを抽出
    $maliciousIps = $iocs | Where-Object { $_.type -eq 'ipv4-addr' } | Select-Object -ExpandProperty value
    ```

### 3. レッドチーム視点

* **タスク**: **偵察 (Reconnaissance)**。ターゲットのWebアプリケーションの設定ファイル (`package.json`, `project.json`など) を解析し、使用されているライブラリやフレームワークのバージョンを特定する。
* **組み合わせ**: `Get-Content`, `ConvertFrom-Json`
* **解説**: 攻撃者はシステム侵入後、アプリケーションの設定ファイルを探索します。`ConvertFrom-Json` を使うことで、これらのファイルを構造化されたオブジェクトとして扱い、脆弱なバージョンのライブラリ (`dependencies`) が使用されていないかを効率的に調査します。
* **例**:

    ```powershell
    # package.jsonを解析して依存ライブラリをリストアップ
    $package = Get-Content -Path C:\inetpub\wwwroot\app\package.json -Raw | ConvertFrom-Json
    $dependencies = $package.dependencies
    $dependencies
    ```

## エラーメッセージとトラブルシューティング

### よくあるエラーメッセージ

1. **エラーメッセージ**: `ConvertFrom-Json: Invalid JSON primitive: ...`
    * **考えられる原因**: 入力文字列が有効なJSON形式ではありません。よくある原因は、末尾のカンマ、不適切な引用符、エスケープされていない特殊文字などです。
    * **解決策**: 入力となるJSON文字列の構文が正しいかを確認してください。オンラインのJSONバリデーターなどを使って検証するのが有効です。

## セキュリティに関する考慮事項

### 悪用事例

* **情報収集**: `ConvertFrom-Json` 自体は直接的な攻撃ツールではありませんが、レッドチームのシナリオで述べた通り、攻撃者がシステム内の設定ファイルやAPI応答を解析し、機密情報（APIキー、接続文字列など）や脆弱なコンポーネントを特定するための重要な補助ツールとして利用されます。

### LOLBASにおける利用例

* `ConvertFrom-Json` はPowerShellの標準機能であり、LOLBASの文脈では、他のコマンドレット (`Invoke-WebRequest` など) と組み合わせて偵察活動やデータ解析の一部として使用されます。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: 設定ファイルに平文でパスワードやAPIキーを保存しない。可能であれば暗号化するか、専用のシークレット管理ツールを使用する。
* **Detection (検知)**: PowerShellのスクリプトブロックロギング (イベントID 4104) を有効化し、`ConvertFrom-Json` が不審なファイルやAPIエンドポイントからのデータを処理していないかを監視する。

## 注意点・補足

* **`Get-Content` と `-Raw`**: JSONファイルを読み込む際は、`Get-Content` に `-Raw` スイッチを付けることが重要です。これにより、ファイル全体が単一の複数行文字列として読み込まれ、`ConvertFrom-Json` が正しく解析できるようになります。`-Raw` を付けないと、ファイルは1行ずつの文字列配列として読み込まれ、解析に失敗します。

---
[PowerShellインデックスに戻る](../../powershell_index.md)

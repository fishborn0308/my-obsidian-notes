---
tags:
  - 'Get-Content'
  - 'powershell'
  - 'cheatsheet'
title: 'Get-Content - ファイルの内容を読み取る'
summary: 'テキストファイルなどのアイテムから内容を1行ずつ読み取り、文字列の配列として返します。'
related:
  - 'Set-Content'
  - 'Add-Content'
  - 'Get-ChildItem'
  - 'type (CMD)'
---

# `Get-Content` - ファイルの内容を読み取る

## 概要

`Get-Content` コマンドレットは、指定したパスにあるアイテム（主にテキストファイル）の内容を読み取ります。デフォルトでは、ファイルの内容を1行ずつ読み取り、それぞれを文字列として配列に格納して返します。このため、パイプラインを通じて1行ずつフィルタリングや処理を行うのに非常に適しています。CMDの `type` やLinuxの `cat` に相当する基本的なコマンドレットです。

このコマンドレットには、`gc`, `cat`, `type` という便利なエイリアスがデフォルトで設定されています。

## 類似コマンドレット/コマンドとの差異

| 類似コマンドレット/コマンド | 差異 / 使い分けのポイント |
| :--- | :--- |
| **`type` (CMD)** | CMDの `type` はファイル内容を単一のテキストブロックとして表示します。`Get-Content` はデフォルトで1行ずつの**文字列配列**を返すため、`ForEach-Object` や `Where-Object` での行単位の処理が容易です。 |
| **`Import-Csv`** | `Import-Csv` は、CSV形式のファイル専用です。各行をヘッダーに基づいたプロパティを持つカスタムオブジェクトに変換します。`Get-Content` は、あらゆるテキストファイルの生の内容を扱います。 |

## よく連携されるコマンドレット

### シナリオ例: ログファイルから特定のエラーを含む行を検索する (インフラ構築・運用視点)

* **目的**: 大規模なログファイルから "Error" という単語が含まれる行のみを抽出する。
* **連携コマンドレット**: `Where-Object` または `Select-String`
* **解説**: `Get-Content` でファイルを1行ずつ読み取り、その結果をパイプラインで `Where-Object`（またはより高速な `Select-String`）に渡して、条件に一致する行のみをフィルタリングします。
* **コマンド例**:

    ```powershell
    # Where-Object を使用する例
    Get-Content -Path "C:\Logs\app.log" | Where-Object { $_ -match "Error" }

    # より推奨される Select-String を使用する例
    Get-Content -Path "C:\Logs\app.log" | Select-String -Pattern "Error"
    ```

## パラメータ説明

| パラメータ | 型 | 説明 |
| :--- | :--- | :--- |
| ⭐ **`-Path`** | `String[]` | 内容を読み取るアイテムのパスを指定します。 |
| ⭐ **`-Raw`** | `SwitchParameter` | ファイル全体を、改行を含む**単一の文字列**として読み取ります。JSONやXML、複数行にわたるテキストブロックを扱う際に非常に重要です。 |
| ⭐ **`-Wait`** | `SwitchParameter` | ファイルの末尾に到達した後もファイルをロックし続け、新しい行が追加されるのを待ちます。Linuxの `tail -f` と同等で、ログのリアルタイム監視に便利です。 |
| ⭐ **`-TotalCount`** | `Int64` | ファイルの先頭から指定した行数だけを読み取ります。`-Head` はこのパラメータのエイリアスです。 |
| ⭐ **`-Tail`** | `Int32` | ファイルの末尾から指定した行数だけを読み取ります。 |
| **`-Encoding`** | `String` | ファイルのエンコーディングを指定します (`UTF8`, `Unicode`, `ASCII` など)。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: サーバーリストが書かれたテキストファイルを読み込み、各サーバーに対してリモートでコマンドを実行する。
* **組み合わせ**: `Get-Content | ForEach-Object { Invoke-Command ... }`
* **解説**: `Get-Content` でサーバー名のリストを1行ずつ読み込み、`ForEach-Object` ループで各サーバーに対して `Invoke-Command` を使って処理を実行します。
* **例**:

    ```powershell
    # servers.txt に記載された各サーバーのOSバージョンを取得
    Get-Content -Path "C:\Scripts\servers.txt" | ForEach-Object {
        Invoke-Command -ComputerName $_ -ScriptBlock { Get-ComputerInfo | Select-Object -ExpandProperty OsName }
    }
    ```

### 2. ブルーチーム視点

* **タスク**: **ライブレスポンス**。不審なスクリプトファイル（`.ps1`, `.vbs`）の中身を、実行せずに安全に確認する。
* **組み合わせ**: `Get-Content <suspicious_file>`
* **解説**: スクリプトファイルを直接実行するとマルウェアが活動を開始する危険があるため、`Get-Content` を使ってそのソースコードをコンソールに表示し、どのような処理が記述されているかを安全に調査します。
* **例**:

    ```powershell
    # 不審なPowerShellスクリプトの中身を表示して調査
    Get-Content -Path "C:\Users\Public\update.ps1"
    ```

### 3. レッドチーム視点

* **タスク**: **偵察 (Reconnaissance)**。設定ファイル (`web.config`など)、スクリプト、履歴ファイルから、パスワードや接続文字列、APIキーなどの機密情報を探す。
* **組み合わせ**: `Get-Content <file> | Select-String "password"`
* **解説**: 攻撃者は侵入後、`Get-ChildItem` で機密情報を含んでいそうなファイルを探し出し、`Get-Content` と `Select-String` を組み合わせてファイル内から認証情報を窃取しようとします。
* **例**:

    ```powershell
    # PowerShellのコマンド履歴ファイルから 'password' という文字列を探す
    Get-Content -Path (Get-PSReadlineOption).HistorySavePath | Select-String -Pattern "password"
    ```

## エラーメッセージとトラブルシューティング

### よくあるエラーメッセージ

1. **エラーメッセージ**: `Get-Content: Cannot find path '...' because it does not exist.`
    * **考えられる原因**: 指定した `-Path` が存在しません。
    * **解決策**: `Test-Path` コマンドレットでパスの存在を確認し、正しいパスを指定してください。

## セキュリティに関する考慮事項

### 悪用事例

* **情報収集 (Reconnaissance)**: レッドチームのシナリオで述べた通り、`Get-Content` は攻撃者がシステム内に平文で保存されたパスワード、APIキー、設定情報などの機密情報を発見するための基本的なツールです。

### LOLBASにおける利用例

* **機能**: **偵察 (Reconnaissance)**, **データ窃取 (Data Exfiltration)**
* **解説**: `Get-Content` は PowerShell の標準機能であり、攻撃者が侵入後の情報収集フェーズでファイルの内容を調査するために利用する最も基本的な環境寄生型の手法です。`-Encoding Byte` と組み合わせることで、ファイルをバイト配列として読み取り、データをエンコードして外部に送信するような、より高度なデータ窃取にも利用されます。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: パスワードやAPIキーなどの機密情報を設定ファイルに平文で保存しない。可能であれば暗号化するか、専用のシークレット管理ツールを使用する。
* **Detection (検知)**: PowerShellのスクリプトブロックロギング (イベントID 4104) を有効化し、`Get-Content` の実行、特に対象となるファイルが設定ファイルやユーザープロファイル内の機密ファイルである場合に監視を強化する。ファイルアクセス監査の有効化も有効です。

## 注意点・補足

* **`-Raw` の重要性**: JSON や XML のような複数行にわたる構造化されたテキストを `ConvertFrom-Json` や `[xml]` に渡す場合は、`-Raw` スイッチを使ってファイル全体を単一の文字列として読み込む必要があります。

---
[PowerShellインデックスに戻る](../../powershell_index.md)

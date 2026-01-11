---
tags:
  - 'Get-Service'
  - 'powershell'
  - 'cheatsheet'
title: 'Get-Service - システムのサービスを取得する'
summary: 'ローカルまたはリモートコンピュータにインストールされているサービス（Windowsサービス）のリストを取得します。'
related:
  - 'Start-Service'
  - 'Stop-Service'
  - 'Restart-Service'
  - 'Get-Process'
  - 'sc query'
---

# `Get-Service` - システムのサービスを取得する

## 概要

`Get-Service` コマンドレットは、ローカルまたはリモートコンピュータのWindowsサービスをオブジェクトとして取得します。このコマンドレットは、システムにインストールされている全てのサービス、あるいは特定のサービスの状態（実行中、停止など）、スタートアップの種類、依存関係などを表示します。

取得したサービスオブジェクトはパイプラインを通じて、`Start-Service`、`Stop-Service`、`Restart-Service` などの他のコマンドレットに渡してサービスを直接制御できます。

このコマンドレットには、`gsv` という便利なエイリアスがデフォルトで設定されています。

## 類似コマンドレット/コマンドとの差異

| 類似コマンドレット/コマンド | 差異 / 使い分けのポイント |
| :--- | :--- |
| **`sc query` (CMD)** | `sc query` はサービスの情報を**テキスト**として表示します。`Get-Service` は各サービスを**オブジェクト**として返すため、`Status` や `StartType` などのプロパティでプログラム的にフィルタリングしたり、結果を他のコマンドレットに渡したりするのが非常に容易です。 |
| **`Get-Process`** | `Get-Process` は実行中の**プロセス**を取得します。サービスはバックグラウンドで実行されるプログラムですが、Windows上ではプロセスとは別の管理エンティティです。多くのサービスは `svchost.exe` などの共有プロセス内で実行されます。 |

## よく連携されるコマンドレット

### シナリオ例: 停止している全ての自動開始サービスを開始する (インフラ構築・運用視点)

* **目的**: システム起動時に自動で開始されるべき (`StartType -eq 'Automatic'`) にもかかわらず、現在停止 (`Status -eq 'Stopped'`) しているサービスを全て検索し、開始する。
* **連携コマンドレット**: `Where-Object`, `Start-Service`
* **解説**: `Get-Service` で全てのサービスオブジェクトを取得し、パイプラインで `Where-Object` に渡して2つの条件（自動開始かつ停止中）でフィルタリングします。最後に、条件に一致したサービスオブジェクトを `Start-Service` に渡して開始させます。
* **コマンド例**:

    ```powershell
    # 停止中の自動開始サービスを検索し、開始する（-WhatIfで事前確認）
    Get-Service | Where-Object { $_.StartType -eq 'Automatic' -and $_.Status -eq 'Stopped' } | Start-Service -WhatIf
    ```

## パラメータ説明

| パラメータ | 型 | 説明 |
| :--- | :--- | :--- |
| ⭐ **`-Name`** | `String[]` | 取得するサービスのサービス名 (`ServiceName`) を指定します。ワイルドカード (`*`) が使用できます。 |
| **`-DisplayName`** | `String[]` | 取得するサービスの表示名を指定します。ワイルドカードが使用できます。 |
| **`-ComputerName`** | `String[]` | サービスを取得するリモートコンピュータを指定します。 |
| **`-DependentServices`** | `SwitchParameter` | 指定したサービスに依存しているサービスを取得します。 |
| **`-RequiredServices`** | `SwitchParameter` | 指定したサービスが依存しているサービスを取得します。（`-ServicesDependedOn` はこのパラメータのエイリアスです） |
| **`-Include`** | `String[]` | 取得対象に含めるサービスを指定します。 |
| **`-Exclude`** | `String[]` | 取得対象から除外するサービスを指定します。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: "SQL" という単語が名前に含まれる全てのサービスの状態を確認する。
* **組み合わせ**: `Get-Service -Name "*SQL*"`
* **解説**: `-Name` パラメータでワイルドカード (`*`) を使うことで、関連するサービス群の状態を一度に確認できます。
* **例**:

    ```powershell
    # 名前に 'SQL' を含む全てのサービスの状態を表示
    Get-Service -Name "*SQL*"
    ```

### 2. ブルーチーム視点

* **タスク**: **永続化の監査**。システムに登録されている全てのサービスを監査し、不審なものがないかを確認する。
* **組み合わせ**: `Get-Service | Get-CimInstance -ClassName Win32_Service`
* **解説**: `Get-Service` だけでは実行ファイルのパスが簡単にはわかりません。`Get-CimInstance -ClassName Win32_Service` と組み合わせることで、各サービスの実行パス (`PathName`) を含む詳細情報を取得できます。不審な名前のサービスや、予期せぬ場所（例: `C:\Users\Public`）から実行されているサービスはマルウェアの可能性があります。
* **例**:

    ```powershell
    # 全てのサービスの表示名、状態、および実行パスをリストアップ
    Get-CimInstance -ClassName Win32_Service | Select-Object DisplayName, State, PathName
    ```

### 3. レッドチーム視点

* **タスク**: **偵察 (Reconnaissance)**。侵入したホストの役割を推測し、セキュリティ製品を特定する。
* **組み合わせ**: `Get-Service`
* **解説**: 攻撃者は侵入後、`Get-Service` を実行して実行中のサービスのリストを偵察します。`MSSQLSERVER` や `w3svc` (IIS) のようなサービスが実行されていれば、そのサーバーの役割がわかります。また、`WinDefend` (Windows Defender), `CbDefense` (Carbon Black) のようなアンチウイルスやEDR製品のサービス名を探し、防御機構を特定します。
* **例**:

    ```powershell
    # 実行中のサービスを全て取得して偵察
    Get-Service | Where-Object { $_.Status -eq 'Running' }

    # 特定のセキュリティ製品のサービスを探す
    Get-Service -Name "*defender*", "*sysmon*" -ErrorAction SilentlyContinue
    ```

## エラーメッセージとトラブルシューティング

### よくあるエラーメッセージ

1. **エラーメッセージ**: `Get-Service: Cannot find any service with service name '...'`
    * **考えられる原因**: 指定した `-Name` のサービスが存在しません。
    * **解決策**: サービス名のスペルが正しいか確認してください。`DisplayName` ではなく `ServiceName` を指定する必要があります。

## セキュリティに関する考慮事項

### 悪用事例

* **偵察 (Reconnaissance)**: レッドチームのシナリオで述べた通り、`Get-Service` は攻撃者が侵入先システムの役割や導入されているセキュリティ製品を把握するための、最も基本的な偵察ツールの1つです。
* **サービス操作による防御回避**: `Get-Service` で特定したセキュリティ製品のサービスを、`Stop-Service` や `Set-Service` を使って停止・無効化しようと試みることがあります。

### LOLBASにおける利用例

* **機能**: **偵察 (Reconnaissance)**
* **解説**: `Get-Service` は PowerShell の標準機能であり、`sc.exe` と同様に、攻撃者が環境に溶け込みながらシステム情報を収集するために利用されます（Living off the Land）。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: セキュリティ製品には、自身のサービスを停止や変更から保護する自己防衛機能が備わっていることが重要です。
* **Detection (検知)**: PowerShellのスクリプトブロックロギング (イベントID 4104) を有効化し、`Get-Service` の実行を記録・監視する。特に、他の偵察コマンドと連続して実行される場合や、`Stop-Service` でセキュリティ製品を停止させようとする挙動は攻撃の兆候です。

## 注意点・補足

* **`Name` vs `DisplayName`**: サービスをパイプラインで他のコマンドレット (`Stop-Service` など) に渡す場合、`DisplayName` ではなく `Name` (サービス名) プロパティが使われることを意識する必要があります。

---
[PowerShellインデックスに戻る](../../powershell_index.md)

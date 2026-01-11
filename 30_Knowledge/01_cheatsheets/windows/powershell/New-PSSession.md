---
tags:
  - 'New-PSSession'
  - 'powershell'
  - 'cheatsheet'
title: 'New-PSSession - リモートコンピュータへの持続的なセッションを作成する'
summary: '1つまたは複数のリモートコンピュータへの、再利用可能な持続的PowerShellセッション (PSSession) を作成します。'
related:
  - 'Enter-PSSession'
  - 'Invoke-Command'
  - 'Get-PSSession'
  - 'Remove-PSSession'
---

# `New-PSSession` - リモートコンピュータへの持続的なセッションを作成する

## 概要

`New-PSSession` コマンドレットは、1つまたは複数のリモートコンピュータへの持続的な接続（セッション）を作成します。このコマンドレットは、`Enter-PSSession` のように対話的なセッションを開始するのではなく、バックグラウンドで接続を確立し、その接続を表す**セッションオブジェクト**を返します。

作成されたセッションオブジェクトは変数に保存しておき、後から `Invoke-Command` や `Enter-PSSession` の `-Session` パラメータに渡すことで、何度も再利用できます。これにより、複数のコマンドを同じリモートセッションで効率的に実行したり、認証のオーバーヘッドを削減したりできます。

この機能は、バックグラウンドで **Windows Remote Management (WinRM)** を利用しています。

## 類似コマンドレット/コマンドとの差異

| 類似コマンドレット/コマンド | 差異 / 使い分けのポイント |
| :--- | :--- |
| **`Enter-PSSession`** | `Enter-PSSession` は**対話的**なセッションを開始します。`New-PSSession` は、後から利用するための**非対話的**な持続的セッションを作成します。 |
| **`Invoke-Command`** | `Invoke-Command -ComputerName` は、コマンド実行のたびに一時的なセッションを作成・破棄します。`New-PSSession` で作成したセッションを `Invoke-Command -Session` に渡すことで、同じセッションを再利用して複数のコマンドを実行でき、パフォーマンスが向上します。 |

## よく連携されるコマンドレット

### シナリオ例: 複数のサーバーに対して一連のコマンドを実行する (インフラ構築・運用視点)

* **目的**: 複数のWebサーバーに対して、まずサービスの状態を確認し、次にサービスを再起動するという一連の操作を、同じセッションを使って効率的に行う。
* **連携コマンドレット**: `Invoke-Command`, `Remove-PSSession`
* **解説**: まず `New-PSSession` で対象の全サーバーへのセッションを作成します。次に、作成したセッションオブジェクトを `Invoke-Command` に渡して、複数のコマンドを順次実行します。作業が完了したら、`Remove-PSSession` ですべての接続をクリーンに閉じます。
* **コマンド例**:

    ```powershell
    # サーバーリスト
    $servers = "WEB-SRV-01", "WEB-SRV-02"

    # 1. 全サーバーへの持続的セッションを作成
    $sessions = New-PSSession -ComputerName $servers

    # 2. 同じセッションを使って、複数のコマンドを実行
    Invoke-Command -Session $sessions -ScriptBlock { Get-Service -Name "w3svc" }
    Invoke-Command -Session $sessions -ScriptBlock { Restart-Service -Name "w3svc" -Force }

    # 3. 作業完了後、全てのセッションを閉じる
    Remove-PSSession -Session $sessions
    ```

## パラメータ説明

| パラメータ | 型 | 説明 |
| :--- | :--- | :--- |
| ⭐ **`-ComputerName`** | `String[]` | セッションを作成するリモートコンピュータの名前またはIPアドレスを、カンマ区切りで指定します。 |
| ⭐ **`-Credential`** | `PSCredential` | 接続に使用するユーザー資格情報を指定します。 |
| **`-Port`** | `Int32` | WinRMリスナーのポートを指定します。（デフォルト: HTTP 5985, HTTPS 5986） |
| **`-UseSSL`** | `SwitchParameter` | 接続にSSL (HTTPS) を使用することを指定します。 |
| **`-SessionOption`** | `PSSessionOption` | タイムアウト値など、セッションの高度なオプションを設定します。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: 多数のサーバーに対して、状態チェック -> 設定変更 -> 状態再チェック、という一連の作業をスクリプトで自動化する。
* **組み合わせ**: `New-PSSession` -> `Invoke-Command` -> `Remove-PSSession`
* **解説**: 上記「よく連携されるコマンドレット」のシナリオ例を参照。複数のコマンドを実行する場合、このパターンは `Invoke-Command -ComputerName` を何度も実行するよりも効率的です。
* **例**: -

### 2. ブルーチーム視点

* **タスク**: **ライブレスポンス**。複数の侵害されたホストから、同時にフォレンジックアーティファクトを収集する。
* **組み合わせ**: `New-PSSession` -> `Invoke-Command`
* **解説**: 調査対象となる複数のホストへのセッションを一度に確立し、`Invoke-Command` を使って並行して証拠収集スクリプトを実行します。これにより、インシデント対応の時間を大幅に短縮できます。
* **例**:

    ```powershell
    # 調査対象ホストのリスト
    $targets = "PC-01", "PC-02", "PC-03"
    $sessions = New-PSSession -ComputerName $targets

    # 全ターゲットから同時に不審なプロセス情報を収集
    Invoke-Command -Session $sessions -ScriptBlock {
        Get-CimInstance -ClassName Win32_Process | Select-Object ProcessId, ParentProcessId, CommandLine
    } | Export-Csv -Path C:\Forensics\process_list.csv -NoTypeInformation
    ```

### 3. レッドチーム視点

* **タスク**: **ラテラルムーブメント (Lateral Movement)** と**C2チャネルの確立**。
* **組み合わせ**: `New-PSSession`
* **解説**: 攻撃者は、窃取した認証情報を使って `New-PSSession` でネットワーク内の複数のマシンへの持続的なアクセスを確立します。これらのセッションはバックグラウンドで維持され、攻撃者はいつでも `Invoke-Command -Session` や `Enter-PSSession -Session` を使って、これらのマシンを遠隔操作できます。これは、攻撃者がネットワーク内での足場を固めるための強力な手法です。
* **例**:

    ```powershell
    # 窃取した認証情報で複数のサーバーへのセッションを確立し、変数に保持
    $cred = Get-Credential
    $compromisedSessions = New-PSSession -ComputerName "SRV-01", "SRV-02", "DC-01" -Credential $cred
    ```

## エラーメッセージとトラブルシューティング

### よくあるエラーメッセージ

1. **エラーメッセージ**: `New-PSSession: [ServerName] Connecting to remote server ServerName failed with the following error message : WinRM cannot process the request.`
    * **考えられる原因**:
      * ターゲットコンピュータでWinRMが有効になっていない (`Enable-PSRemoting`)。
      * ファイアウォールがWinRMのポート (TCP 5985/5986) をブロックしている。
    * **解決策**: ターゲットコンピュータで `Enable-PSRemoting -Force` を実行します。ファイアウォールの設定を確認します。

## セキュリティに関する考慮事項

### 悪用事例

* **ラテラルムーブメントと永続化**: レッドチームのシナリオで述べた通り、`New-PSSession` は攻撃者がネットワーク内で足場を築き、複数のマシンを同時に制御下に置くための中心的なツールです。

### LOLBASにおける利用例

* **機能**: **ラテラルムーブメント (Lateral Movement)**, **コード実行 (Execution)**
* **解説**: `New-PSSession` は PowerShell の標準機能であり、攻撃者がマルウェアをインストールすることなく、ネットワーク内で自由に活動するための正規のリモート管理機能として悪用されます。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: 最小権限の原則を徹底し、管理者アカウントの認証情報を厳格に管理する。**JEA (Just Enough Administration)** を構成し、リモート接続を許可するユーザーや、実行可能なコマンドを制限する。
* **Detection (検知)**: PowerShellの**モジュールロギング**や**スクリプトブロックロギング** (イベントID 4103, 4104) を有効化する。Windowsイベントログで、リモートからのログオン成功 (イベントID 4624, ログオンタイプ 3) と、そのセッションで実行されたコマンドを監視する。

## 注意点・補足

* **リソース消費**: `New-PSSession` で作成されたセッションは、明示的に `Remove-PSSession` で閉じるまで、リモートコンピュータ上でリソースを消費し続けます。スクリプトの最後には、必ずセッションをクリーンアップするようにしてください。

---
[PowerShellインデックスに戻る](../../powershell_index.md)

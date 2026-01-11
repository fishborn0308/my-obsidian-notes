---
tags:
  - 'Enter-PSSession'
  - 'powershell'
  - 'cheatsheet'
title: 'Enter-PSSession - 対話的なリモートセッションを開始する'
summary: 'リモートコンピュータへの対話的なPowerShellセッション（PSSession）を開始します。これにより、リモートコンピュータのコンソールを直接操作しているかのようにコマンドを実行できます。'
related:
  - 'Invoke-Command'
  - 'New-PSSession'
  - 'Get-PSSession'
  - 'Exit-PSSession'
  - 'ssh'
---

# `Enter-PSSession` - 対話的なリモートセッションを開始する

## 概要

`Enter-PSSession` コマンドレットは、単一のリモートコンピュータとの対話的なセッションを開始します。セッションが確立されると、コマンドプロンプトが `[<ComputerName>]: PS C:\>` のように変化し、入力したコマンドはすべてリモートコンピュータ上で実行されます。これにより、GUIのリモートデスクトップ接続 (RDP) を使用せずに、サーバーの管理やトラブルシューティングを効率的に行うことができます。

この機能は、バックグラウンドで **Windows Remote Management (WinRM)** を利用しています。

## 類似コマンドレット/コマンドとの差異

| 類似コマンドレット/コマンド | 差異 / 使い分けのポイント |
| :--- | :--- |
| **`Invoke-Command`** | `Invoke-Command` は、1つまたは複数のリモートコンピュータに対して**非対話的**にコマンドブロックを実行し、結果をローカルに返します。スクリプトによる自動化に適しています。`Enter-PSSession` は**対話的**な単一セッション用です。 |
| **`ssh`** | `ssh` はクロスプラットフォームで広く使われている安全なリモートシェルプロトコルです。PowerShell Remoting (WinRM) はWindows環境に特化しており、PowerShellオブジェクトをシリアライズして転送できる利点があります。 |
| **`telnet` / `rlogin`** | これらは暗号化されていない古いプロトコルであり、セキュリティ上の理由から絶対に使用すべきではありません。`Enter-PSSession` はWinRM上で暗号化された通信を行います。 |

## よく連携されるコマンドレット

### シナリオ例: 事前に作成したセッションに接続する (インフラ構築・運用視点)

* **目的**: 複数のサーバーへの接続を事前に確立しておき、必要に応じて特定のセッションに素早く対話的に接続する。
* **連携コマンドレット**: `New-PSSession`, `Get-PSSession`
* **解説**: `New-PSSession` を使うと、複数のリモートコンピュータへの接続をバックグラウンドで確立し、セッションオブジェクトとして保持できます。その後、`Enter-PSSession -Session` パラメータでそのオブジェクトを指定することで、再認証なしで即座に対話セッションを開始できます。
* **コマンド例**:

    ```powershell
    # 2台のWebサーバーへのセッションを事前に作成
    $sessions = New-PSSession -ComputerName "WEB-SRV-01", "WEB-SRV-02"

    # 1台目のサーバー (WEB-SRV-01) に対話セッションを開始
    Enter-PSSession -Session $sessions[0]

    # [...リモートでの作業...]
    # exit と入力してセッションを抜ける

    # 2台目のサーバーに対話セッションを開始
    Enter-PSSession -Session $sessions[1]
    ```

## パラメータ説明

| パラメータ | 型 | 説明 |
| :--- | :--- | :--- |
| ⭐ **`-ComputerName`** | `String` | 接続するリモートコンピュータの名前またはIPアドレスを指定します。 |
| **`-Session`** | `PSSession` | `New-PSSession` で作成された既存のセッションオブジェクトを指定して接続します。 |
| ⭐ **`-Credential`** | `PSCredential` | 接続に使用するユーザー資格情報を指定します。指定しない場合は現在のユーザーの資格情報が使われます。 |
| **`-Port`** | `Int32` | WinRMリスナーのポートを指定します。（デフォルト: HTTP 5985, HTTPS 5986） |
| **`-UseSSL`** | `SwitchParameter` | 接続にSSL (HTTPS) を使用することを指定します。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: リモートのWebサーバー (IIS) の状態を確認し、アプリケーションプールをリサイクルする。
* **組み合わせ**: `Enter-PSSession -ComputerName <ServerName>`
* **解説**: サーバーにRDPでログインすることなく、PowerShell Remotingを使って対話的に管理コマンドを実行します。
* **例**:

    ```powershell
    # Webサーバーに対話セッションを開始
    Enter-PSSession -ComputerName "WEB-SRV-01"

    # (リモートセッション内で) IISのコマンドレットを実行
    Import-Module WebAdministration
    Get-WebAppPoolState -Name "DefaultAppPool"
    Restart-WebAppPool -Name "DefaultAppPool"

    # 'exit' を入力してセッションを終了
    exit
    ```

### 2. ブルーチーム視点

* **タスク**: **ライブレスポンス**。侵害された疑いのあるホストに、RDPを介さずに接続して調査を行う。
* **組み合わせ**: `Enter-PSSession -ComputerName <Host> -Credential <AdminCreds>`
* **解説**: RDPセッションは攻撃者に検知されたり、GUI操作がログに残りにくかったりする場合があります。`Enter-PSSession` を使うことで、より隠密かつCUIベースで調査コマンド (`netstat`, `Get-Process`, `Get-WinEvent`など) を実行し、証拠を収集できます。
* **例**: -

### 3. レッドチーム視点

* **タスク**: **ラテラルムーブメント (Lateral Movement)**。窃取した認証情報を使って、ネットワーク内の別のマシンに侵入を拡大する。
* **組み合わせ**: `Enter-PSSession -ComputerName <Target> -Credential <StolenCreds>`
* **解説**: 攻撃者は、`mimikatz` などで窃取したドメイン管理者などの認証情報を使い、`Enter-PSSession` でまだ侵入していない他のサーバーやPCに対話シェルを確立します。これは、PowerShellベースの攻撃において最も一般的で強力な横展開の手法です。
* **例**:

    ```powershell
    # 窃取したドメイン管理者の資格情報でファイルサーバーに侵入
    $cred = Get-Credential # 対話的にユーザー名とパスワードを入力
    Enter-PSSession -ComputerName "FILE-SRV-01" -Credential $cred
    ```

## エラーメッセージとトラブルシューティング

### よくあるエラーメッセージ

1. **エラーメッセージ**: `Enter-PSSession: Connecting to remote server <ServerName> failed with the following error message : WinRM cannot process the request.`
    * **考えられる原因**:
      * ターゲットコンピュータでWinRMが有効になっていない (`Enable-PSRemoting`)。
      * ファイアウォールがWinRMのポート (TCP 5985/5986) をブロックしている。
      * ネットワークの問題でターゲットに到達できない。
    * **解決策**: ターゲットコンピュータで `Enable-PSRemoting -Force` を実行します。ファイアウォールの設定を確認します。`Test-NetConnection -ComputerName <ServerName> -Port 5985` でポート接続をテストします。

## セキュリティに関する考慮事項

### 悪用事例

* **ラテラルムーブメント**: レッドチームのシナリオで述べた通り、`Enter-PSSession` は攻撃者がネットワーク内を移動するための主要なツールです。一度ドメイン管理者などの強力な認証情報を窃取されると、ネットワーク内のほぼ全てのマシンがこのコマンドレット経由で侵害される危険性があります。

### LOLBASにおける利用例

* **機能**: **ラテラルムーブメント (Lateral Movement)**, **コード実行 (Execution)**
* **解説**: `Enter-PSSession` は PowerShell の標準機能であり、`powershell.exe` を通じて実行されます。攻撃者はこの正規のリモート管理機能を利用して、マルウェアをインストールすることなく、ネットワーク内で自由に活動します。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: 最小権限の原則を徹底し、管理者アカウントの認証情報を厳格に管理する。**JEA (Just Enough Administration)** を構成し、管理者に必要最小限のコマンドレットのみをリモートで実行できるように制限する。ホストベースのファイアウォールで、信頼できる管理端末からのみWinRM接続を許可する。
* **Detection (検知)**: PowerShellの**モジュールロギング**や**スクリプトブロックロギング** (イベントID 4103, 4104) を有効化し、リモートセッションでどのようなコマンドが実行されたかを記録・監視する。Windowsイベントログで、リモートからのログオン成功 (イベントID 4624, ログオンタイプ 3) を監視する。

## 注意点・補足

* **セッションの終了**: 対話セッションを終了するには、`exit` と入力するか、`Exit-PSSession` コマンドレットを実行します。

---
[PowerShellインデックスに戻る](../../powershell_index.md)

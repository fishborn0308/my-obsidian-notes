---
tags:
  - 'Restart-Computer'
  - 'powershell'
  - 'cheatsheet'
title: 'Restart-Computer - コンピュータを再起動する'
summary: 'ローカルまたはリモートのコンピュータを再起動します。'
related:
  - 'Stop-Computer'
  - 'Restart-Service'
  - 'shutdown'
---

# `Restart-Computer` - コンピュータを再起動する

## 概要

`Restart-Computer` コマンドレットは、ローカルコンピュータまたはリモートのコンピュータを再起動します。このコマンドレットは、ソフトウェアのインストール後や構成変更の適用後など、システムの再起動が必要なタスクをスクリプトから自動化するために使用されます。

CMDの `shutdown /r` コマンドに相当するモダンなPowerShellの代替であり、複数のコンピュータに対する一括操作や、再起動完了を待機するなどの高度な機能を提供します。

## 類似コマンドレット/コマンドとの差異

| 類似コマンドレット/コマンド | 差異 / 使い分けのポイント |
| :--- | :--- |
| **`shutdown /r` (CMD)** | `shutdown` はCMDベースのコマンドで、タイマー設定や通知メッセージの機能があります。`Restart-Computer` はPowerShellのオブジェクトパイプラインに統合されており、複数のコンピュータへの一括実行や、`-Wait` パラメータによる同期的なスクリプト実行が容易です。 |
| **`Stop-Computer`** | `Stop-Computer` はコンピュータを**シャットダウン**します。`Restart-Computer` はコンピュータを**再起動**します。 |

## よく連携されるコマンドレット

### シナリオ例: 複数のサーバーにパッチを適用し、順次再起動する (インフラ構築・運用視点)

* **目的**: サーバーリストにある全てのサーバーに対してパッチ適用スクリプトを実行し、1台ずつ再起動が完了するのを待ってから次のサーバーの再起動に進む。
* **連携コマンドレット**: `ForEach-Object`
* **解説**: `-Wait` パラメータは、再起動が開始され、PowerShellが再び通信できるようになるまでスクリプトの実行を待機させます。これにより、複数のサーバーを安全に、かつ順番に再起動させることができます。
* **コマンド例**:

    ```powershell
    # 'servers.txt' に記載されたサーバーリストを1台ずつ処理
    Get-Content -Path .\servers.txt | ForEach-Object {
        Write-Host "Restarting $_ ..."
        # -WhatIf で事前確認。本番では -WhatIf を外す
        Restart-Computer -ComputerName $_ -Force -Wait -WhatIf
        Write-Host "$_ has been restarted."
    }
    ```

## パラメータ説明

| パラメータ | 型 | 説明 |
| :--- | :--- | :--- |
| ⭐ **`-ComputerName`** | `String[]` | 再起動するリモートコンピュータの名前またはIPアドレスを指定します。デフォルトはローカルコンピュータです。 |
| ⭐ **`-Force`** | `SwitchParameter` | ログオンしているユーザーに警告せず、実行中のアプリケーションを強制的に閉じます。 |
| ⭐ **`-Wait`** | `SwitchParameter` | コマンドレットが、再起動が完了するまで待機するようにします。 |
| **`-For`** | `enum` | `-Wait` と共に使用し、何を待機するかを指定します (`WinRM`, `PowerShell`, `Default`)。 |
| **`-Credential`** | `PSCredential` | リモートコンピュータへの接続に使用するユーザー資格情報を指定します。 |
| **`-Delay`** | `Int` | 再起動までの遅延時間を秒単位で指定します。 |
| ⭐ **`-WhatIf`** | `SwitchParameter` | コマンドを実行せずに、何が起こるかを表示します。安全確認のために使用します。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: メンテナンス作業後、ローカルコンピュータを即時に強制再起動する。
* **組み合わせ**: `Restart-Computer -Force`
* **解説**: 最も基本的な使い方。設定変更などを適用するために、スクリプトの最後に配置します。
* **例**:

    ```powershell
    # 現在のコンピュータを強制的に再起動
    Restart-Computer -Force
    ```

### 2. ブルーチーム視点

* **タスク**: **インシデント対応における封じ込め**の一環。特定の構成変更を適用するために、侵害されたホストをリモートから再起動する。
* **組み合わせ**: `Restart-Computer -ComputerName <Host> -Force`
* **解説**: 例えば、ファイアウォールルールを厳格化するグループポリシーを `gpupdate /force` で適用した後、`Restart-Computer` で再起動を強制し、マルウェアの通信を確実に遮断する、といったシナリオで使われます。
* **例**:

    ```powershell
    # 侵害されたホスト 'VICTIM-PC' をリモートから強制再起動
    Restart-Computer -ComputerName "VICTIM-PC" -Force
    ```

### 3. レッドチーム視点

* **タスク**: **サービス妨害 (DoS, Denial of Service)** または**永続化設定の適用**。
* **組み合わせ**: `Restart-Computer`
* **解説**:
  * **DoS**: 攻撃者は管理者権限を奪取した後、`Restart-Computer` を使って重要なサーバーを予期せず再起動させ、業務を妨害します。
  * **永続化**: レジストリの `Run` キーへの書き込みや、サービスの実行ファイル置き換えなど、再起動や再ログオンをトリガーとする永続化手法を適用するために、このコマンドレットで再起動を誘発します。
* **例**:

    ```powershell
    # ターゲットサーバーを強制再起動してサービス妨害を行う
    Restart-Computer -ComputerName "TARGET-SRV" -Force
    ```

## エラーメッセージとトラブルシューティング

### よくあるエラーメッセージ

1. **エラーメッセージ**: `Restart-Computer: Failed to restart the computer '...' with the following error message: Access is denied.`
    * **考えられる原因**: 再起動を実行するための権限がありません。リモートコンピュータの場合は、相手先のマシンでローカル管理者権限が必要です。
    * **解決策**: PowerShellを「管理者として実行」します。リモートの場合は、`-Credential` パラメータで適切な管理者資格情報を指定してください。

## セキュリティに関する考慮事項

### 悪用事例

* **サービス妨害 (DoS)**: レッドチームのシナリオで述べた通り、システムの可用性を損なうために悪用されます。
* **永続化のトリガー**: 攻撃者が仕掛けたバックドアを有効化するために、再起動を強制する目的で利用されます。

### LOLBASにおける利用例

* **機能**: **影響 (Impact)**, **防御回避 (Defense Evasion)**
* **解説**: `Restart-Computer` は PowerShell の標準機能であり、`shutdown.exe` と同様に、攻撃者がシステムの可用性を妨害したり、自身の永続化設定を有効化したりするために利用されます（Living off the Land）。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: 最小権限の原則を徹底し、一般ユーザーに再起動権限を与えない。ユーザー権利の割り当て (`secpol.msc`) で、「リモートシステムからの強制シャットダウン」権限を Administrators のみに制限する。
* **Detection (検知)**: Windows イベントログで、システムのシャットダウンまたは再起動のイベント (イベントID 1074) を監視し、その理由や実行者を確認する。PowerShellのスクリプトブロックロギングを有効化し、`Restart-Computer` の実行を記録・監視する。

## 注意点・補足

* **`-WhatIf` の重要性**: `Restart-Computer` はシステムを停止させる破壊的なコマンドです。特に複数のコンピュータ (`-ComputerName "srv1", "srv2"`) に対して実行する前には、必ず `-WhatIf` スイッチを付けて、意図した通りの操作が行われるかを確認することが強く推奨されます。

---
[PowerShellインデックスに戻る](../../powershell_index.md)

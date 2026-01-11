---
tags:
  - 'Remove-LocalUser'
  - 'powershell'
  - 'cheatsheet'
title: 'Remove-LocalUser - ローカルユーザーアカウントを削除する'
summary: 'ローカルコンピュータから1つまたは複数のユーザーアカウントを恒久的に削除します。'
related:
  - 'New-LocalUser'
  - 'Get-LocalUser'
  - 'Disable-LocalUser'
  - 'net_user'
---

# `Remove-LocalUser` - ローカルユーザーアカウントを削除する

## 概要

`Remove-LocalUser` コマンドレットは、ローカルコンピュータのセキュリティアカウントマネージャー (SAM) データベースから、指定したユーザーアカウントを恒久的に削除します。この操作は元に戻すことができず、ユーザープロファイルや関連データも削除される可能性があるため、実行には細心の注意が必要です。

このコマンドレットは、`Microsoft.PowerErsatz.LocalAccounts` モジュールに含まれています。

## 類似コマンドレット/コマンドとの差異

| 類似コマンドレット/コマンド | 差異 / 使い分けのポイント |
| :--- | :--- |
| **`net user <user> /delete` (CMD)** | `net user /delete` と機能的に同等ですが、`Remove-LocalUser` はPowerShellのオブジェクトパイプラインを活用できます。例えば `Get-LocalUser` の結果を直接パイプで渡してユーザーを削除できます。 |
| **`Disable-LocalUser`** | `Disable-LocalUser` はアカウントを**無効化**するだけで、アカウント自体は残ります。`Remove-LocalUser` はアカウントを**完全に削除**します。一時的にアクセスを停止したい場合は `Disable-LocalUser` が安全です。 |

## よく連携されるコマンドレット

### シナリオ例: 特定の条件に一致する古いアカウントを削除する (インフラ構築・運用視点)

* **目的**: アカウント棚卸しの一環として、名前に "temp" を含み、かつ無効化されているアカウントを全て検索して削除する。
* **連携コマンドレット**: `Get-LocalUser`, `Where-Object`
* **解説**: まず `Get-LocalUser` で条件に一致するユーザーオブジェクトを取得します。次に、その結果をパイプラインで `Remove-LocalUser` に渡し、対象のアカウントを削除します。
* **コマンド例**:

    ```powershell
    # 名前に 'temp' を含み、かつ無効化されているユーザーを削除（-WhatIfで事前確認）
    Get-LocalUser -Name "*temp*" | Where-Object { -not $_.Enabled } | Remove-LocalUser -WhatIf
    ```

## パラメータ説明

| パラメータ | 型 | 説明 |
| :--- | :--- | :--- |
| ⭐ **`-Name`** | `String[]` | 削除するユーザーアカウントの名前を指定します。 |
| **`-SID`** | `SecurityIdentifier` | 削除するユーザーアカウントのセキュリティ識別子 (SID) を指定します。 |
| **`-InputObject`** | `LocalUser[]` | パイプラインから渡されたユーザーオブジェクトを受け取ります。 |
| ⭐ **`-WhatIf`** | `SwitchParameter` | コマンドを実行せずに、何が起こるかを表示します。破壊的な操作の前に安全確認のために必須です。 |
| ⭐ **`-Confirm`** | `SwitchParameter` | 実行前にユーザーに確認を求めます。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: 不要になった一時的なユーザーアカウント `tempuser` を削除する。
* **組み合わせ**: `Remove-LocalUser -Name <UserName>`
* **解説**: 最も基本的な使い方。アカウントが不要になった際に、システムからクリーンに削除します。
* **例**:

    ```powershell
    # 'tempuser' アカウントを削除
    Remove-LocalUser -Name "tempuser"
    ```

### 2. ブルーチーム視点

* **タスク**: **インシデント対応における修復 (Remediation)**。攻撃者によって作成されたバックドアアカウントを完全に削除する。
* **組み合わせ**: `Remove-LocalUser -Name <AttackerAccount>`
* **解説**: 調査によって特定された、攻撃者が永続化のために作成した不正なローカルアカウントをシステムから完全に除去します。
* **例**:

    ```powershell
    # 不正アカウント 'backdoor_user' を削除
    Remove-LocalUser -Name "backdoor_user" -Confirm:$false
    ```

### 3. レッドチーム視点

* **タスク**: **痕跡消去 (Anti-Forensics)**。自身の活動のために作成したアカウントを削除する。
* **組み合わせ**: `Remove-LocalUser`
* **解説**: 攻撃者は、活動終了時に、永続化や権限昇格のために一時的に作成したローカルアカウントを `Remove-LocalUser` で削除し、監査ログやフォレンジック調査から自身の存在を消し去ろうとします。
* **例**:

    ```powershell
    # 攻撃者が使用した 'temp_admin' アカウントを痕跡消去のために削除
    Remove-LocalUser -Name "temp_admin"
    ```

## エラーメッセージとトラブルシューティング

### よくあるエラーメッセージ

1. **エラーメッセージ**: `Remove-LocalUser: User <username> was not found.`
    * **考えられる原因**: 指定した `-Name` のユーザーアカウントが存在しません。
    * **解決策**: `Get-LocalUser` を実行して、ユーザー名のスペルが正しいか、アカウントが既に削除されていないかを確認してください。

2. **エラーメッセージ**: `Remove-LocalUser: Access is denied.`
    * **考えられる原因**: ユーザーアカウントの削除には管理者権限が必要です。
    * **解決策**: PowerShellを「管理者として実行」して、再度コマンドを実行してください。

## セキュリティに関する考慮事項

### 悪用事例

* **痕跡消去**: レッドチームのシナリオで述べた通り、攻撃者は活動の痕跡を消すためにこのコマンドレットを悪用します。
* **妨害 (Sabotage)**: 攻撃者が管理者権限を奪取した後、正規のユーザーアカウントや管理者アカウントを削除し、システムの運用を妨害する可能性があります。

### LOLBASにおける利用例

* **機能**: **防御回避 (Defense Evasion)**, **影響 (Impact)**
* **解説**: `Remove-LocalUser` は PowerShell の標準機能であり、攻撃者が環境に溶け込みながら、システムのユーザー構成を変更し痕跡を消去したりシステムに損害を与えたりするために利用されます。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: 重要なアカウント（特に管理者アカウント）の削除権限を厳格に管理する。定期的なバックアップを取得する。
* **Detection (検知)**: Windows セキュリティイベントログで、**イベントID 4726 (ユーザー アカウントが削除されました)** を厳重に監視する。PowerShellのスクリプトブロックロギング (イベントID 4104) を有効化し、`Remove-LocalUser` の実行を記録・監視する。

## 注意点・補足

* **`-WhatIf` の重要性**: `Remove-LocalUser` はアカウントを**恒久的に削除する**破壊的なコマンドです。意図しないアカウントを削除する大惨事を防ぐため、**実行前には必ず `-WhatIf` スイッチを付けて、どのユーザーが削除対象になるかを正確に確認してください。**

---
[PowerShellインデックスに戻る](../../powershell_index.md)

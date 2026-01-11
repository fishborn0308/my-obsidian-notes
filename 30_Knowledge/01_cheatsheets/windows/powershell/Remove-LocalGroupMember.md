---
tags:
  - 'Remove-LocalGroupMember'
  - 'powershell'
  - 'cheatsheet'
title: 'Remove-LocalGroupMember - ローカルグループからメンバーを削除する'
summary: 'ローカルのセキュリティグループから、1人または複数のユーザーやグループを削除します。'
related:
  - 'Add-LocalGroupMember'
  - 'Get-LocalGroupMember'
  - 'Remove-LocalUser'
  - 'net_localgroup'
---

# `Remove-LocalGroupMember` - ローカルグループからメンバーを削除する

## 概要

`Remove-LocalGroupMember` コマンドレットは、ローカルコンピュータ上のセキュリティグループから、指定したユーザーやグループをメンバーとして削除します。このコマンドレットは、ユーザーの権限を変更したり、不要になったアクセス権を剥奪したりといった、アカウント管理の自動化に使用されます。

このコマンドレットは、`Microsoft.PowerShell.LocalAccounts` モジュールに含まれています。

## 類似コマンドレット/コマンドとの差異

| 類似コマンドレット/コマンド | 差異 / 使い分けのポイント |
| :--- | :--- |
| **`net localgroup <group> <user> /delete`** | `net localgroup` はCMDの伝統的なコマンドです。`Remove-LocalGroupMember` はPowerShellのオブジェクトパイプラインを活用でき、`Get-LocalGroupMember` の結果を直接パイプで渡してメンバーを削除するなど、スクリプト内での柔軟性が高いです。 |

## よく連携されるコマンドレット

### シナリオ例: 特定の条件に一致するメンバーをグループから削除する (インフラ構築・運用視点)

* **目的**: "Backup Operators" グループのメンバーの中から、名前に "temp" を含むアカウントを全て検索し、グループから削除する。
* **連携コマンドレット**: `Get-LocalGroupMember`, `Where-Object`
* **解説**: まず `Get-LocalGroupMember` でグループの全メンバーオブジェクトを取得します。次に、その結果をパイプラインで `Where-Object` に渡し、名前の条件でフィルタリングします。最後に、条件に一致したメンバーオブジェクトを `Remove-LocalGroupMember` に渡して削除します。
* **コマンド例**:

    ```powershell
    # 'Backup Operators' グループから、名前に 'temp' を含むメンバーを削除（-WhatIfで事前確認）
    Get-LocalGroupMember -Group "Backup Operators" | Where-Object { $_.Name -like "*temp*" } | Remove-LocalGroupMember -Group "Backup Operators" -WhatIf
    ```

## パラメータ説明

| パラメータ | 型 | 説明 |
| :--- | :--- | :--- |
| ⭐ **`-Group`** | `LocalGroup` | メンバーを削除する対象のローカルグループの名前またはオブジェクトを指定します。 |
| ⭐ **`-Member`** | `LocalPrincipal[]` | グループから削除する1人または複数のユーザー/グループの名前またはオブジェクトを指定します。 |
| **`-Name`** | `String` | `-Group` パラメータのエイリアス。グループ名を指定します。 |
| ⭐ **`-WhatIf`** | `SwitchParameter` | コマンドを実行せずに、何が起こるかを表示します。安全確認のために使用します。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: 退職したユーザー `userX` を、全てのローカルグループから削除する。
* **組み合わせ**: `Get-LocalGroup`, `ForEach-Object`
* **解説**: 全てのローカルグループを取得し、ループ処理で各グループに対して `Remove-LocalGroupMember` を実行します。`-ErrorAction SilentlyContinue` を付けることで、ユーザーが元々所属していなかったグループに対するエラーを無視できます。
* **例**:

    ```powershell
    # 'userX' を全てのローカルグループから削除
    Get-LocalGroup | ForEach-Object {
        Remove-LocalGroupMember -Group $_ -Member "userX" -ErrorAction SilentlyContinue
    }
    ```

### 2. ブルーチーム視点

* **タスク**: **インシデント対応における修復 (Remediation)**。攻撃者によって特権グループに追加された不正アカウントを削除する。
* **組み合わせ**: `Remove-LocalGroupMember -Group "Administrators" -Member <AttackerAccount>`
* **解説**: 調査によって特定されたバックドアアカウントを、"Administrators" や "Remote Desktop Users" などの特権グループから削除し、攻撃者のアクセス権を剥奪します。
* **例**:

    ```powershell
    # 不正アカウント 'backdoor_user' を管理者グループから削除
    Remove-LocalGroupMember -Group "Administrators" -Member "backdoor_user"
    ```

### 3. レッドチーム視点

* **タスク**: **痕跡消去 (Anti-Forensics)** または **妨害 (Sabotage)**。
* **解説**:
  * **痕跡消去**: 攻撃者は、自身の活動のために一時的に特権グループに追加したアカウントを、活動後にグループから削除して、監査の目を欺こうとすることがあります。
  * **妨害**: 管理者権限を奪取した後、正規の管理者アカウントを "Administrators" グループから削除し、インシデント対応担当者のアクセスを妨害しようとすることがあります。
* **例**:

    ```powershell
    # 正規の管理者 'Admin01' を Administrators グループから削除する妨害行為の例
    Remove-LocalGroupMember -Group "Administrators" -Member "Admin01"
    ```

## エラーメッセージとトラブルシューティング

### よくあるエラーメッセージ

1. **エラーメッセージ**: `Remove-LocalGroupMember : Member <username> was not found in group <groupname>.`
    * **考えられる原因**: 削除しようとしたメンバーは、そのグループに所属していません。
    * **解決策**: `Get-LocalGroupMember` で現在のメンバーを確認してください。スクリプト内でこのエラーを無視したい場合は `-ErrorAction SilentlyContinue` を使用します。

2. **エラーメッセージ**: `Remove-LocalGroupMember : Access is denied.`
    * **考えられる原因**: グループメンバーの変更には管理者権限が必要です。
    * **解決策**: PowerShellを「管理者として実行」して、再度コマンドを実行してください。

## セキュリティに関する考慮事項

### 悪用事例

* **妨害とロックアウト**: レッドチームのシナリオで述べた通り、攻撃者は正規の管理者を特権グループから削除することで、システムの管理を妨害し、インシデント対応を遅らせようとします。

### LOLBASにおける利用例

* **機能**: **防御回避 (Defense Evasion)**, **影響 (Impact)**
* **解説**: `Remove-LocalGroupMember` は PowerShell の標準機能であり、攻撃者がシステムの権限設定を操作して、防御者を妨害したり、自身の活動の痕跡を隠蔽したりするために利用されます。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: 特権グループのメンバーシップを厳格に管理し、変更管理プロセスを徹底する。
* **Detection (検知)**: Windows セキュリティイベントログで、**イベントID 4733 (メンバーがセキュリティが有効なローカル グループから削除されました)** を厳重に監視する。特に、管理者アカウントが `Administrators` グループから削除された場合は、高優先度のアラートを発報すべきです。PowerShellのスクリプトブロックロギングも有効です。

## 注意点・補足

* **`-WhatIf`**: グループメンバーの変更はシステムのセキュリティに直接影響するため、コマンド実行前には `-WhatIf` パラメータを付けて、意図した通りの操作が行われるかを確認することが強く推奨されます。

---
[PowerShellインデックスに戻る](../../powershell_index.md)

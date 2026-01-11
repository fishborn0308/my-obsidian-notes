---
tags:
  - 'Add-LocalGroupMember'
  - 'powershell'
  - 'cheatsheet'
title: 'Add-LocalGroupMember - ローカルグループにメンバーを追加する'
summary: 'ローカルのセキュリティグループに、1人または複数のユーザーやグループを追加します。'
related:
  - 'Get-LocalGroupMember'
  - 'Remove-LocalGroupMember'
  - 'New-LocalUser'
  - 'net_localgroup'
---

# `Add-LocalGroupMember` - ローカルグループにメンバーを追加する

## 概要

`Add-LocalGroupMember` コマンドレットは、ローカルコンピュータ上のセキュリティグループに、ユーザーや他のグループを追加するために使用されます。このコマンドレットは、`Microsoft.PowerShell.LocalAccounts` モジュールに含まれており、スタンドアロンサーバーやワークステーションのアクセス権管理を自動化する上で基本的な役割を果たします。

## 類似コマンドレット/コマンドとの差異

| 類似コマンドレット/コマンド | 差異 / 使い分けのポイント |
| :--- | :--- |
| **`net localgroup <group> <user> /add`** | `net localgroup` はCMDの伝統的なコマンドです。`Add-LocalGroupMember` はPowerShellのオブジェクトパイプラインを活用でき、複数のメンバーを一度に追加したり、`New-LocalUser` の結果を直接渡したりと、スクリプト内での柔軟性が格段に高いです。 |

## よく連携されるコマンドレット

### シナリオ例: 新規ユーザーを作成し、Remote Desktop Usersグループに追加する (インフラ構築・運用視点)

* **目的**: 新しいユーザーアカウントを作成し、そのアカウントがリモートデスクトップでログインできるように権限を付与する。
* **連携コマンドレット**: `New-LocalUser`
* **解説**: `New-LocalUser` で作成したユーザーオブジェクトをパイプライン経由で `Add-LocalGroupMember` に渡すことで、ユーザー作成からグループ追加までを一行で完結させることができます。
* **コマンド例**:

    ```powershell
    # 'devuser01' を作成し、パスワードを設定して、即座に 'Remote Desktop Users' グループに追加
    New-LocalUser -Name "devuser01" -PasswordNeverExpires -Password (Read-Host -AsSecureString "Enter password for devuser01") | Add-LocalGroupMember -Group "Remote Desktop Users"
    ```

## パラメータ説明

| パラメータ | 型 | 説明 |
| :--- | :--- | :--- |
| ⭐ **`-Group`** | `LocalGroup` | メンバーを追加するローカルグループの名前またはオブジェクトを指定します。 |
| ⭐ **`-Member`** | `LocalPrincipal[]` | グループに追加する1人または複数のユーザー/グループの名前またはオブジェクトを指定します。 |
| **`-Name`** | `String` | `-Group` パラメータのエイリアス。グループ名を指定します。 |
| **`-InputObject`** | `LocalPrincipal[]` | `-Member` パラメータのエイリアス。パイプラインからの入力を受け取ります。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: 複数のユーザーを一度に特定のローカルグループに追加する。
* **組み合わせ**: `Add-LocalGroupMember -Group <group> -Member <user1>, <user2>`
* **解説**: `-Member` パラメータには、カンマ区切りで複数のユーザー名を指定できます。これにより、複数のアカウントに対する権限付与を効率的に行えます。
* **例**:

    ```powershell
    # ユーザー 'user1' と 'user2' を 'Hyper-V Administrators' グループに追加
    Add-LocalGroupMember -Group "Hyper-V Administrators" -Member "user1", "corp\user2"
    ```

### 2. ブルーチーム視点

* **タスク**: **権限昇格の監査**。特権グループのメンバーシップを確認し、逸脱があれば修正する。
* **連携コマンドレット**: `Get-LocalGroupMember`
* **解説**: まず `Get-LocalGroupMember` で "Administrators" グループのメンバーをリストアップし、意図しないアカウントが含まれていないかを確認します。もし不審なアカウントがあれば、`Remove-LocalGroupMember` で削除します。（このコマンドレットは追加操作なので、監査後の修正アクションとして使われます）
* **例**:

    ```powershell
    # (監査後) 誤って追加されていた 'tempadmin' を管理者グループから削除するために Remove を使用
    # Get-LocalGroupMember -Group "Administrators" | Where-Object { $_.Name -ne "Administrator" -and $_.Name -ne "Domain Admins" }
    # Remove-LocalGroupMember -Group "Administrators" -Member "tempadmin"
    ```

### 3. レッドチーム視点

* **タスク**: **永続化 (Persistence)**。自身が制御するアカウントを特権グループに追加する。
* **組み合わせ**: `Add-LocalGroupMember -Group "Administrators" -Member <attacker_user>`
* **解説**: 攻撃者は権限昇格に成功した後、バックドアとして作成したアカウントや、既に存在するアカウントを `Administrators` や `Remote Desktop Users` グループに追加します。これにより、システムの再起動後も管理者権限でのアクセスやリモートログインを維持しようとします。CMDの `net localgroup` よりもPowerShellを使うことで、ロギングを回避する試みが行われることがあります。
* **例**:

    ```powershell
    # 攻撃者が作成した 'backdoor' アカウントをローカル管理者グループに追加
    Add-LocalGroupMember -Group "Administrators" -Member "backdoor"
    ```

## エラーメッセージとトラブルシューティング

### よくあるエラーメッセージ

1. **エラーメッセージ**: `Add-LocalGroupMember : User <username> was not found.`
    * **考えられる原因**: グループに追加しようとしたユーザーアカウントが存在しません。
    * **解決策**: ユーザー名が正しいか確認してください。ドメインユーザーの場合は `Domain\User` の形式で指定する必要があります。

2. **エラーメッセージ**: `Add-LocalGroupMember : Access is denied.`
    * **考えられる原因**: グループメンバーの変更には管理者権限が必要です。
    * **解決策**: PowerShellを「管理者として実行」して、再度コマンドを実行してください。

## セキュリティに関する考慮事項

### 悪用事例

* **永続化**: レッドチームのシナリオで述べた通り、このコマンドレットは攻撃者がシステムへの永続的なアクセスを確保するための常套手段です。バックドアアカウントを特権グループに追加することで、いつでも管理者権限でシステムに再侵入できるようになります。

### LOLBASにおける利用例

* **機能**: **永続化 (Persistence)**, **権限昇格 (Privilege Escalation)**
* **解説**: `Add-LocalGroupMember` は PowerShell の標準機能であり、`powershell.exe` を通じて実行されます。攻撃者はこの正規の機能を利用して、検知されにくい形でシステムのセキュリティ設定を変更します。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: 最小権限の原則を徹底し、特権グループへの所属は必要最小限のユーザーに限定する。
* **Detection (検知)**: Windows セキュリティイベントログで、**イベントID 4732 (メンバーがセキュリティが有効なローカル グループに追加されました)** の発生を厳重に監視する。PowerShellのスクリプトブロックロギング (イベントID 4104) を有効化し、`Add-LocalGroupMember` の実行を記録・監視する。

## 注意点・補足

* **メンバーの型**: `-Member` パラメータには、ユーザーアカウント、グループアカウント、またはコンピューターアカウントを指定できます。

---
[PowerShellインデックスに戻る](../../powershell_index.md)

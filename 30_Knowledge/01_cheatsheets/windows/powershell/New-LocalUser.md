---
tags:
  - 'New-LocalUser'
  - 'powershell'
  - 'cheatsheet'
title: 'New-LocalUser - ローカルユーザーアカウントを作成する'
summary: 'ローカルコンピュータに新しいユーザーアカウントを作成します。'
related:
  - 'Set-LocalUser'
  - 'Get-LocalUser'
  - 'Remove-LocalUser'
  - 'Add-LocalGroupMember'
  - 'net_user'
---

# `New-LocalUser` - ローカルユーザーアカウントを作成する

## 概要

`New-LocalUser` コマンドレットは、ローカルコンピュータのセキュリティアカウントマネージャー (SAM) データベースに、新しいユーザーアカウントを作成します。このコマンドレットを使って、アカウント名、パスワード、説明などのプロパティを指定して、新しいユーザーをスクリプトから作成できます。

このコマンドレットは、`Microsoft.PowerShell.LocalAccounts` モジュールに含まれています。

## 類似コマンドレット/コマンドとの差異

| 類似コマンドレット/コマンド | 差異 / 使い分けのポイント |
| :--- | :--- |
| **`net user <user> <pass> /add` (CMD)** | `net user` はパスワードを平文でコマンドラインに記述する必要があります。`New-LocalUser` は、`-Password` パラメータで `SecureString` を受け取れるため、より安全にパスワードを扱うことができます。また、出力がオブジェクトであるため、作成したユーザーをすぐにパイプラインで他のコマンドレットに渡せます。 |
| **`New-ADUser`** | `New-ADUser` は **Active Directory ドメイン**に新しいユーザーアカウントを作成します。`New-LocalUser` は**ローカルコンピュータ**上にアカウントを作成します。 |

## よく連携されるコマンドレット

### シナリオ例: 新しい管理者アカウントを作成する (インフラ構築・運用視点)

* **目的**: 新しいローカルユーザー `tempadmin` を作成し、即座にローカルの "Administrators" グループに追加する。
* **連携コマンドレット**: `Add-LocalGroupMember`
* **解説**: `New-LocalUser` で作成したユーザーオブジェクトをパイプライン経由で `Add-LocalGroupMember` に渡すことで、ユーザー作成から管理者権限の付与までを一行で完結させることができます。
* **コマンド例**:

    ```powershell
    # 'tempadmin' ユーザーを作成し、'Administrators' グループに追加
    New-LocalUser -Name "tempadmin" -Password (Read-Host -AsSecureString "Enter password") -FullName "Temporary Admin" | Add-LocalGroupMember -Group "Administrators"
    ```

## パラメータ説明

| パラメータ | 型 | 説明 |
| :--- | :--- | :--- |
| ⭐ **`-Name`** | `String` | 新しいユーザーのアカウント名を指定します。 |
| ⭐ **`-Password`** | `SecureString` | 新しいユーザーのパスワードを `SecureString` オブジェクトとして指定します。 |
| **`-FullName`** | `String` | ユーザーのフルネームを指定します。 |
| **`-Description`** | `String` | アカウントの説明を指定します。 |
| ⭐ **`-PasswordNeverExpires`** | `SwitchParameter` | パスワードを無期限に設定します。 |
| **`-UserMayNotChangePassword`** | `SwitchParameter` | ユーザーが自身のパスワードを変更できないように設定します。 |
| **`-AccountNeverExpires`** | `SwitchParameter` | アカウントを無期限に設定します。 |
| **`-Disabled`** | `SwitchParameter` | アカウントを作成と同時に無効化します。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: アプリケーション用の非対話的なサービスアカウントを作成する。
* **組み合わせ**: `New-LocalUser -PasswordNeverExpires -UserMayNotChangePassword`
* **解説**: サービスアカウントが意図せずパスワード変更を求められたり、変更されたりするのを防ぎます。
* **例**:

    ```powershell
    # 'svc_myapp' サービスアカウントを作成
    $password = ConvertTo-SecureString "aVeryComplexP@ssw0rd!" -AsPlainText -Force
    New-LocalUser -Name "svc_myapp" -Password $password -Description "Service account for MyApp" -PasswordNeverExpires -UserMayNotChangePassword
    ```

### 2. ブルーチーム視点

* **タスク**: **不正アカウント作成の検知**。`New-LocalUser` の実行履歴を監査する。
* **組み合わせ**: (PowerShellログ分析)
* **解説**: **インシデント対応の基本**。攻撃者が永続化のためにローカルアカウントを作成することは一般的な手口です。PowerShellのスクリプトブロックロギング (イベントID 4104) や、Windows セキュリティイベントログ (イベントID 4720: ユーザーアカウントが作成されました) を監視し、意図しない `New-LocalUser` の実行やアカウント作成がないかを確認します。
* **例**: -

### 3. レッドチーム視点

* **タスク**: **永続化 (Persistence)**。侵入したホストに、バックドアとなるローカル管理者アカウントを作成する。
* **組み合わせ**: `New-LocalUser | Add-LocalGroupMember -Group "Administrators"`
* **解説**: 権限昇格に成功した後、攻撃者はこのコマンドレットを使って隠しアカウント（例: `admin$` のように `$` を付ける）や、正規のアカウントに見せかけたアカウントを作成し、管理者グループに追加します。これにより、いつでもシステムに再侵入できる足がかりを確保します。
* **例**:

    ```powershell
    # バックドアアカウント 'support_3889' を作成し、管理者にする
    $password = ConvertTo-SecureString "P@ssw0rd123!" -AsPlainText -Force
    New-LocalUser -Name "support_3889" -Password $password | Add-LocalGroupMember -Group "Administrators"
    ```

## エラーメッセージとトラブルシューティング

### よくあるエラーメッセージ

1. **エラーメッセージ**: `New-LocalUser: The password does not meet the password policy requirements.`
    * **考えられる原因**: 指定したパスワードが、システムのパスワードポリシー（複雑さ、長さ、履歴など）を満たしていません。
    * **解決策**: より複雑なパスワードを指定してください。

2. **エラーメッセージ**: `New-LocalUser: Access is denied.`
    * **考えられる原因**: ユーザーアカウントの作成には管理者権限が必要です。
    * **解決策**: PowerShellを「管理者として実行」して、再度コマンドを実行してください。

## セキュリティに関する考慮事項

### 悪用事例

* **永続化のためのバックドアアカウント作成**: レッドチームのシナリオで述べた通り、`New-LocalUser` は攻撃者がシステムへの永続的なアクセスを確保するための最も一般的で強力な手法の1つです。

### LOLBASにおける利用例

* **機能**: **永続化 (Persistence)**
* **解説**: `New-LocalUser` は PowerShell の標準機能であり、`net user` と同様に、攻撃者が環境に溶け込みながらバックドアアカウントを作成するために利用されます（Living off the Land）。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: 最小権限の原則を徹底し、不要なローカルアカウントの作成を許可しない。LAPS (Local Administrator Password Solution) を導入し、ローカル管理者アカウントのパスワードを自動的かつ一意に管理する。
* **Detection (検知)**: Windows セキュリティイベントログで、**イベントID 4720 (ユーザー アカウントが作成されました)** を厳重に監視する。PowerShellのスクリプトブロックロギング (イベントID 4104) を有効化し、`New-LocalUser` の実行を記録・監視する。

## 注意点・補足

* **`SecureString`**: スクリプト内でパスワードを扱う際は、平文で直接記述するのではなく、`Read-Host -AsSecureString` で対話的に入力するか、`ConvertTo-SecureString` を使って暗号化された文字列として扱うことがセキュリティ上推奨されます。

---
[PowerShellインデックスに戻る](../../powershell_index.md)

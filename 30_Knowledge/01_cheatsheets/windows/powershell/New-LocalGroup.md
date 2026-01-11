---
tags:
  - 'New-LocalGroup'
  - 'powershell'
  - 'cheatsheet'
title: 'New-LocalGroup - ローカルグループを作成する'
summary: 'ローカルコンピュータに新しいセキュリティグループを作成します。'
related:
  - 'Get-LocalGroup'
  - 'Remove-LocalGroup'
  - 'Add-LocalGroupMember'
  - 'net_localgroup'
---

# `New-LocalGroup` - ローカルグループを作成する

## 概要

`New-LocalGroup` コマンドレットは、ローカルのセキュリティアカウントマネージャー (SAM) に、新しいセキュリティグループを作成します。このコマンドレットは、特定の権限セットを管理するための新しいグループを作成し、サーバーやワークステーションのアクセス権管理を構造化するために使用されます。

このコマンドレットは、`Microsoft.PowerShell.LocalAccounts` モジュールに含まれています。

## 類似コマンドレット/コマンドとの差異

| 類似コマンドレット/コマンド | 差異 / 使い分けのポイント |
| :--- | :--- |
| **`net localgroup <group> /add` (CMD)** | `net localgroup` はCMDの伝統的なコマンドです。`New-LocalGroup` はPowerShellのオブジェクトパイプラインを活用でき、作成したグループオブジェクトをすぐにパイプラインで `Add-LocalGroupMember` に渡すなど、スクリプト内での柔軟性が高いです。 |
| **`New-ADGroup`** | `New-ADGroup` は **Active Directory ドメイン**に新しいグループを作成します。`New-LocalGroup` は**ローカルコンピュータ**上にグループを作成します。 |

## よく連携されるコマンドレット

### シナリオ例: 新しいグループを作成し、すぐにメンバーを追加する (インフラ構築・運用視点)

* **目的**: アプリケーション管理者向けの新しいローカルグループ `AppAdmins` を作成し、そのグループに `user1` を追加する。
* **連携コマンドレット**: `Add-LocalGroupMember`
* **解説**: `New-LocalGroup` で作成したグループオブジェクトをパイプライン経由で `Add-LocalGroupMember` に渡すことで、グループ作成からメンバー追加までを一行で完結させることができます。
* **コマンド例**:

    ```powershell
    # 'AppAdmins' グループを作成し、'user1' をメンバーとして追加
    New-LocalGroup -Name "AppAdmins" -Description "Administrators for My Application" | Add-LocalGroupMember -Member "user1"
    ```

## パラメータ説明

| パラメータ | 型 | 説明 |
| :--- | :--- | :--- |
| ⭐ **`-Name`** | `String` | 新しいグループの名前を指定します。 |
| **`-Description`** | `String` | グループの説明を指定します。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: 特定のアプリケーションのログファイルを読み取る権限だけを持つ、監査用のグループを作成する。
* **組み合わせ**: `New-LocalGroup`
* **解説**: 最小権限の原則に従い、特定のタスク専用のグループを作成することで、アクセス権の管理を容易にします。作成後、`icacls` や `Set-Acl` で、このグループに対してログディレクトリへの読み取り権限を付与します。
* **例**:

    ```powershell
    # 'LogReaders' という名前で新しいグループを作成
    New-LocalGroup -Name "LogReaders" -Description "Members can read application logs."
    ```

### 2. ブルーチーム視点

* **タスク**: **不正なグループ作成の検知**。`New-LocalGroup` の実行履歴を監査する。
* **組み合わせ**: (PowerShellログ分析)
* **解説**: 攻撃者が権限を維持するために、偽装した名前の特権グループ（例: "Admins" や "Administrators " のように紛らわしい名前）を作成する可能性があります。PowerShellのスクリプトブロックロギング (イベントID 4104) や、Windows セキュリティイベントログ (イベントID 4731: セキュリティが有効なローカル グループが作成されました) を監視し、意図しない `New-LocalGroup` の実行やグループ作成がないかを確認します。
* **例**: -

### 3. レッドチーム視点

* **タスク**: **偵察 (Reconnaissance)** と**永続化 (Persistence)** の準備。
* **組み合わせ**: `New-LocalGroup`
* **解説**: 攻撃者は、自身の活動を隠蔽するために、正規のグループに見せかけた独自のグループを作成し、そこにバックドアアカウントを所属させることがあります。これにより、直接 "Administrators" グループにメンバーを追加するよりも監査の目を欺きやすくなる可能性があります。
* **例**:

    ```powershell
    # 正規のグループに見せかけたグループを作成
    New-LocalGroup -Name "Backup Operators " -Description "Backs up files and directories"
    ```

## エラーメッセージとトラブルシューティング

### よくあるエラーメッセージ

1. **エラーメッセージ**: `New-LocalGroup: Group '...' already exists.`
    * **考えられる原因**: 指定した `-Name` のグループが既に存在します。
    * **解決策**: 別のグループ名を使用するか、既存のグループが不要であれば `Remove-LocalGroup` で削除してください。

2. **エラーメッセージ**: `New-LocalGroup: Access is denied.`
    * **考えられる原因**: ローカルグループの作成には管理者権限が必要です。
    * **解決策**: PowerShellを「管理者として実行」して、再度コマンドを実行してください。

## セキュリティに関する考慮事項

### 悪用事例

* **偽装グループの作成**: レッドチームのシナリオで述べた通り、攻撃者は正規のグループ（特に管理者グループ）と酷似した名前のグループを作成し、管理者の誤認を誘うことで、バックドアアカウントに権限を付与しようとします。

### LOLBASにおける利用例

* **機能**: **永続化 (Persistence)**, **防御回避 (Defense Evasion)**
* **解説**: `New-LocalGroup` は PowerShell の標準機能であり、攻撃者が環境に溶け込みながら、システムの権限設定を変更し、永続化の足がかりを築くために利用されます。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: 最小権限の原則を徹底し、不要なローカルグループの作成を許可しない。
* **Detection (検知)**: Windows セキュリティイベントログで、**イベントID 4731 (セキュリティが有効なローカル グループが作成されました)** を厳重に監視する。PowerShellのスクリプトブロックロギング (イベントID 4104) を有効化し、`New-LocalGroup` の実行を記録・監視する。

## 注意点・補足

* このコマンドレットは、`Microsoft.PowerShell.LocalAccounts` モジュールの一部です。このモジュールは、Windows 10、Windows Server 2016以降のバージョンにデフォルトで含まれています。

---
[PowerShellインデックスに戻る](../../powershell_index.md)

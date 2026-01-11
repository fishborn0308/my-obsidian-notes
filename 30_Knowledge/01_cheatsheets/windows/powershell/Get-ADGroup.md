---
tags:
  - 'Get-ADGroup'
  - 'powershell'
  - 'cheatsheet'
  - 'Active_Directory'
title: 'Get-ADGroup - Active Directory グループを取得する'
summary: '1つまたは複数のActive Directoryグループオブジェクトを取得します。'
related:
  - 'Get-ADGroupMember'
  - 'Get-ADUser'
  - 'net group'
---

# `Get-ADGroup` - Active Directory グループを取得する

## 概要

`Get-ADGroup` コマンドレットは、Active Directoryドメイン内の1つまたは複数のグループオブジェクトを取得します。このコマンドレットは、特定のグループをその名前やSAMアカウント名で検索したり、`-Filter` パラメータを使って特定の条件に一致する全てのグループを検索したりするために使用されます。

**注**: このコマンドレットを使用するには、Active Directoryモジュール for Windows PowerShell がインストールされている必要があります。これは通常、ドメインコントローラーやRSAT (リモートサーバー管理ツール) がインストールされた管理用PCで利用可能です。

## 類似コマンドレット/コマンドとの差異

| 類似コマンドレット/コマンド | 差異 / 使い分けのポイント |
| :--- | :--- |
| **`net group` (CMD)** | `net group` はグループ名とそのメンバーを文字列として返します。`Get-ADGroup` は、詳細なプロパティ（説明、グループスコープなど）を含む豊富な情報を持つ**オブジェクト**を返し、パイプラインでの後続処理が可能です。 |
| **`Get-ADUser`** | `Get-ADUser` は**ユーザー**オブジェクトを取得します。`Get-ADGroup` は**グループ**オブジェクトを取得します。 |

## よく連携されるコマンドレット

### シナリオ例: 特定のグループのメンバーを全て表示する (インフラ構築・運用視点)

* **目的**: "Domain Admins" グループのオブジェクトを取得し、そのグループに所属する全てのメンバーをリストアップする。
* **連携コマンドレット**: `Get-ADGroupMember`
* **解説**: まず `Get-ADGroup` で目的のグループオブジェクトを取得し、その結果をパイプラインで `Get-ADGroupMember` に渡します。これにより、指定したグループのメンバー一覧を簡単に取得できます。
* **コマンド例**:

    ```powershell
    # "Domain Admins" グループのメンバーを一覧表示
    Get-ADGroup -Identity "Domain Admins" | Get-ADGroupMember
    ```

## パラメータ説明

| パラメータ | 型 | 説明 |
| :--- | :--- | :--- |
| ⭐ **`-Identity`** | `ADGroup` | 取得するグループを一意に指定します。`DistinguishedName`, `GUID`, `SID`, `SAMAccountName` のいずれかで指定できます。 |
| ⭐ **`-Filter`** | `String` | PowerShell Expression Language に似た構文を使い、条件に一致するグループオブジェクトを検索します。ワイルドカード (`*`) が使用できます。 |
| **`-Properties`** | `String[]` | デフォルトでは返されない追加のプロパティを取得します。メンバーシップ情報 (`Members`, `MemberOf`) などを取得する際に `*` を指定することが多いです。 |
| **`-SearchBase`** | `String` | 検索を開始するOU (組織単位) の `DistinguishedName` を指定します。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: 名前に "Sales" を含む全てのドメイングループを検索する。
* **組み合わせ**: `Get-ADGroup -Filter 'Name -like "*Sales*"'`
* **解説**: `-Filter` パラメータとワイルドカード (`*`) を使うことで、命名規則に基づいたグループの検索が容易になります。
* **例**:

    ```powershell
    # 名前に "Sales" を含む全てのグループの Name と Description を表示
    Get-ADGroup -Filter 'Name -like "*Sales*"' -Properties Description | Select-Object Name, Description
    ```

### 2. ブルーチーム視点

* **タスク**: **特権グループの監査**。ドメインの管理者権限を持つグループ (`Domain Admins`) の詳細情報を確認する。
* **組み合わせ**: `Get-ADGroup -Identity <Group> -Properties *`
* **解説**: `-Properties *` を付けて、グループの作成日時 (`whenCreated`)、変更日時 (`whenChanged`) などのメタデータを確認し、意図しない変更が行われていないかを監査します。
* **例**:

    ```powershell
    # Domain Admins グループの詳細プロパティを全て表示
    Get-ADGroup -Identity "Domain Admins" -Properties *
    ```

### 3. レッドチーム視点

* **タスク**: **偵察 (Reconnaissance)**。ドメインの権限構造をマッピングする。
* **組み合わせ**: `Get-ADGroup -Filter *`
* **解説**: 攻撃者は侵入後、まず `Get-ADGroup -Filter *` ですべてのグループをリストアップします。その後、`Domain Admins`, `Enterprise Admins`, `Administrators`, `SQL Admins`, `Exchange Admins` のような特権的な名前を持つグループを特定し、`-Identity` でそれらのグループを詳しく調査して、次の攻撃ターゲットとなる特権ユーザーを探します。
* **例**:

    ```powershell
    # ドメイン内の全てのグループ名を取得
    Get-ADGroup -Filter * | Select-Object Name

    # 'Domain Admins' グループのメンバーを調べる (連携シナリオ参照)
    Get-ADGroup -Identity "Domain Admins" | Get-ADGroupMember
    ```

## エラーメッセージとトラブルシューティング

### よくあるエラーメッセージ

1. **エラーメッセージ**: `Get-ADGroup : Cannot find an object with identity: '...'`
    * **考えられる原因**: 指定した `-Identity` のグループが存在しないか、スペルが間違っています。
    * **解決策**: グループ名が正しいか確認してください。別のドメインのグループを探している場合は、`-Server` パラメータでドメインコントローラーを明示的に指定する必要があります。

2. **エラーメッセージ**: `Unable to contact the server. This may be because this server does not exist, it is currently down, or it does not have the Active Directory Web Services running.`
    * **考えられる原因**: ドメインコントローラーに接続できません。または、ドメインコントローラーでActive Directory Web Services (ADWS) が実行されていません。
    * **解決策**: ドメインコントローラーへのネットワーク接続を確認します。ドメインコントローラー上で `Get-Service ADWS` を実行し、サービスが実行中であることを確認してください。

## セキュリティに関する考慮事項

### 悪用事例

* **偵察 (Reconnaissance)**: レッドチームのシナリオで述べた通り、`Get-ADGroup` は攻撃者がActive Directoryの権限構造を把握するための最も基本的なツールの1つです。どのようなグループが存在し、誰が特権グループに所属しているかを知ることは、権限昇格やラテラルムーブメントの計画に不可欠です。

### LOLBASにおける利用例

* **機能**: **偵察 (Reconnaissance)**
* **解説**: `Get-ADGroup` は `ActiveDirectory` モジュールの一部であり、`powershell.exe` を通じて実行されます。攻撃者はこの正規の管理機能を利用して、環境に溶け込みながらドメイン内の情報を収集します。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: 最小権限の原則を徹底し、一般ユーザーアカウントにドメインの情報を広範囲に読み取らせる必要がない場合は、ACLを適切に設定して読み取り権限を制限する（高度な設定）。
* **Detection (検知)**: PowerShellのスクリプトブロックロギング (イベントID 4104) を有効化し、`Get-ADGroup` の実行、特に `-Filter *` のような広範囲なクエリや、短時間に多数のグループを照会するような不審な挙動を監視する。

## 注意点・補足

* **デフォルトプロパティ**: `Get-ADGroup` はデフォルトでは一部のプロパティしか返しません。`Description`, `Members`, `MemberOf` など、より多くの情報を取得したい場合は、`-Properties` パラメータで明示的に指定する必要があります。

---
[PowerShellインデックスに戻る](../../powershell_index.md)

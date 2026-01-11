---
tags:
  - 'Get-ADUser'
  - 'powershell'
  - 'cheatsheet'
  - 'Active_Directory'
title: 'Get-ADUser - Active Directory ユーザーを取得する'
summary: '1人または複数のActive Directoryユーザーオブジェクトを取得します。'
related:
  - 'Set-ADUser'
  - 'New-ADUser'
  - 'Get-ADComputer'
  - 'Get-ADGroup'
  - 'net_user'
---

# `Get-ADUser` - Active Directory ユーザーを取得する

## 概要

`Get-ADUser` コマンドレットは、Active Directoryドメイン内の1人または複数のユーザーオブジェクトを取得します。このコマンドレットは、特定のユーザーをその名前やSAMアカウント名で検索したり、`-Filter` パラメータを使って特定の条件（例: 全ての無効なアカウント）に一致するユーザーを検索したりするために使用されます。Active Directoryのユーザー管理と監査における最も基本的なコマンドレットの1つです。

**注**: このコマンドレットを使用するには、Active Directoryモジュール for Windows PowerShell がインストールされている必要があります。これは通常、ドメインコントローラーやRSAT (リモートサーバー管理ツール) がインストールされた管理用PCで利用可能です。

## 類似コマンドレット/コマンドとの差異

| 類似コマンドレット/コマンド | 差異 / 使い分けのポイント |
| :--- | :--- |
| **`net user` (CMD)** | `net user` はユーザーの基本情報を文字列として返します。`Get-ADUser` は、豊富なプロパティを持つ**オブジェクト**を返し、パイプラインでの後続処理や、特定の属性 (`mail`, `lastLogonTimestamp`など) の取得が容易です。 |
| **`Get-LocalUser`** | `Get-LocalUser` は**ローカルコンピュータ**上のユーザーを取得します。`Get-ADUser` は**ドメイン**上のユーザーを取得します。 |
| **`Get-ADPrincipal`** | ユーザー、グループ、コンピューターなど、セキュリティプリンシパル全般を検索できます。ユーザーのみに限定する場合は `Get-ADUser` の方が意図が明確です。 |

## よく連携されるコマンドレット

### シナリオ例: 全てのロックアウトされたユーザーアカウントを見つけてロックを解除する (インフラ構築・運用視点)

* **目的**: Active Directory内で現在ロックアウトされている全てのアカウントを検索し、それらのロックを解除する。
* **連携コマンドレット**: `Unlock-ADAccount`
* **解説**: まず `Search-ADAccount -LockedOut` を使うのが最も効率的です。取得したユーザーオブジェクトをパイプラインで `Unlock-ADAccount` に渡すことで、ロックアウトされた全ユーザーのロックを一度に解除できます。
* **コマンド例**:

    ```powershell
    # ロックアウトされている全アカウントを検索し、ロックを解除
    Search-ADAccount -LockedOut | Unlock-ADAccount
    ```

## パラメータ説明

| パラメータ | 型 | 説明 |
| :--- | :--- | :--- |
| ⭐ **`-Identity`** | `ADUser` | 取得するユーザーを一意に指定します。`DistinguishedName`, `GUID`, `SID`, `SAMAccountName` のいずれかで指定できます。 |
| ⭐ **`-Filter`** | `String` | PowerShell Expression Language に似た構文を使い、条件に一致するユーザーオブジェクトを検索します。ワイルドカード (`*`) が使用できます。 |
| ⭐ **`-Properties`** | `String[]` | デフォルトでは返されない追加のプロパティを取得します。`mail`, `lastLogonDate`, `memberOf` など、特定の情報を取得する際に必須です。全て取得する場合は `*` を指定します。 |
| **`-SearchBase`** | `String` | 検索を開始するOU (組織単位) の `DistinguishedName` を指定します。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: 90日以上ログインしていない非アクティブなユーザーアカウントを検索する。
* **組み合わせ**: `Get-ADUser -Filter <filter> -Properties <properties>`
* **解説**: `-Properties` で `LastLogonDate` を取得し、`-Filter` でその値を評価します。これにより、アカウント棚卸しのためのリストを作成できます。
* **例**:

    ```powershell
    # 90日以上ログインしていない有効なアカウントを検索
    $inactiveDate = (Get-Date).AddDays(-90)
    Get-ADUser -Filter {Enabled -eq $true -and LastLogonDate -lt $inactiveDate} -Properties LastLogonDate | Select-Object Name, SamAccountName, LastLogonDate
    ```

### 2. ブルーチーム視点

* **タスク**: **不審なアカウントの調査**。特定のユーザーアカウントがどのグループに所属しているか、いつ作成されたかなどの詳細情報を確認する。
* **組み合わせ**: `Get-ADUser -Identity <User> -Properties *`
* **解説**: `-Identity` で対象ユーザーを指定し、`-Properties *` ですべての属性を取得します。特に `MemberOf` (所属グループ)、`whenCreated` (作成日時)、`BadPwdCount` (パスワード失敗回数) などはインシデント調査において重要な情報です。
* **例**:

    ```powershell
    # ユーザー 'suspicious_user' の全プロパティを取得
    Get-ADUser -Identity "suspicious_user" -Properties *
    ```

### 3. レッドチーム視点

* **タスク**: **偵察 (Reconnaissance)**。ドメイン内の全ユーザーをリストアップし、特権アカウントを探す。
* **組み合わせ**: `Get-ADUser -Filter *`
* **解説**: 攻撃者は侵入後、まず `Get-ADUser -Filter *` でドメイン内の全ユーザーを列挙します。その後、`Description` や `Name` に "admin", "svc", "sql" といったキーワードが含まれるアカウントを探したり、`Domain Admins` グループのメンバーを特定したりして、次の攻撃ターゲットを絞り込みます。
* **例**:

    ```powershell
    # 名前に "admin" を含む全ユーザーを検索
    Get-ADUser -Filter 'Name -like "*admin*"'

    # パスワードが無期限に設定されているアカウント (サービスアカウントの可能性が高い) を検索
    Get-ADUser -Filter 'PasswordNeverExpires -eq $true'
    ```

## エラーメッセージとトラブルシューティング

### よくあるエラーメッセージ

1. **エラーメッセージ**: `Get-ADUser : Cannot find an object with identity: '...'`
    * **考えられる原因**: 指定した `-Identity` のユーザーが存在しないか、スペルが間違っています。
    * **解決策**: ユーザー名が正しいか確認してください。

## セキュリティに関する考慮事項

### 悪用事例

* **Active Directory 偵察**: レッドチームのシナリオで述べた通り、`Get-ADUser` は攻撃者がドメインの構造、ユーザー、権限関係を把握するための最も基本的で強力な偵察ツールです。収集された情報は、権限昇格やラテラルムーブメントの足がかりとして悪用されます。

### LOLBASにおける利用例

* **機能**: **偵察 (Reconnaissance)**
* **解説**: `Get-ADUser` は `ActiveDirectory` モジュールの一部であり、`powershell.exe` を通じて実行されます。攻撃者はこの正規の管理機能を利用して、環境に溶け込みながらドメイン内の情報を収集します (Living off the Land)。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: 最小権限の原則を徹底し、一般ユーザーアカウントにドメインの情報を広範囲に読み取らせる必要がない場合は、ACLを適切に設定して読み取り権限を制限する（高度な設定）。
* **Detection (検知)**: PowerShellのスクリプトブロックロギング (イベントID 4104) を有効化し、`Get-ADUser` の実行、特に `-Filter *` のような広範囲なクエリや、短時間に多数のユーザーを照会するような不審な挙動（例: BloodHoundなどの偵察ツールによる実行）を監視する。

## 注意点・補足

* **`LastLogonDate`**: この属性はドメイン内で自動的に複製されますが、リアルタイムではありません（最大14日程度の遅延があり得ます）。より正確な最終ログオン時刻が必要な場合は、各ドメインコントローラーに直接問い合わせて `lastLogon` 属性を確認する必要があります。

---
[PowerShellインデックスに戻る](../../powershell_index.md)

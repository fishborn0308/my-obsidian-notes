---
tags:
  - 'Get-LocalGroup'
  - 'powershell'
  - 'cheatsheet'
title: 'Get-LocalGroup - ローカルグループを取得する'
summary: 'ローカルコンピュータ上の1つまたは複数のセキュリティグループを取得します。'
related:
  - 'Get-LocalGroupMember'
  - 'New-LocalGroup'
  - 'net_localgroup'
---

# `Get-LocalGroup` - ローカルグループを取得する

## 概要

`Get-LocalGroup` コマンドレットは、ローカルのセキュリティアカウントマネージャー (SAM) に存在するローカルグループを取得します。このコマンドレットは、特定のグループを名前やSIDで検索したり、システムに存在する全てのグループを一覧表示したりするために使用されます。

このコマンドレットは、`Microsoft.PowerShell.LocalAccounts` モジュールに含まれており、スタンドアロンサーバーやワークステーションの権限設定を監査する上で基本的な役割を果たします。

## 類似コマンドレット/コマンドとの差異

| 類似コマンドレット/コマンド | 差異 / 使い分けのポイント |
| :--- | :--- |
| **`net localgroup` (CMD)** | `net localgroup` はグループ名の一覧を**テキスト**として表示します。`Get-LocalGroup` は、`Description` や `SID` などのプロパティを持つ**オブジェクト**を返すため、パイプラインで `Get-LocalGroupMember` に渡すなど、スクリプト内でのプログラム的な処理が非常に容易です。 |

## よく連携されるコマンドレット

### シナリオ例: 全てのローカルグループとそのメンバーを一覧表示する (インフラ構築・運用視点)

* **目的**: サーバーに存在する全てのローカルグループを検索し、各グループに所属するメンバーをリストアップして設定を監査する。
* **連携コマンドレット**: `Get-LocalGroupMember`
* **解説**: `Get-LocalGroup` で全てのグループオブジェクトを取得し、その結果をパイプラインで `ForEach-Object` ループに渡します。ループ内で、各グループに対して `Get-LocalGroupMember` を実行し、グループ名とそのメンバーを表示します。
* **コマンド例**:

    ```powershell
    # 全てのローカルグループを取得し、それぞれのメンバーを一覧表示
    Get-LocalGroup | ForEach-Object {
        Write-Host "Group: $($_.Name)"
        Get-LocalGroupMember -Group $_
        Write-Host "" # 空行を挿入
    }
    ```

## パラメータ説明

| パラメータ | 型 | 説明 |
| :--- | :--- | :--- |
| ⭐ **`-Name`** | `String[]` | 取得するグループの名前を指定します。ワイルドカード (`*`) が使用できます。 |
| **`-SID`** | `SecurityIdentifier` | 取得するグループのセキュリティ識別子 (SID) を指定します。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: "Remote" という単語が名前に含まれる全てのローカルグループを検索する。
* **組み合わせ**: `Get-LocalGroup -Name "*Remote*"`
* **解説**: `-Name` パラメータでワイルドカード (`*`) を使うことで、命名規則に基づいたグループの検索が容易になります。
* **例**:

    ```powershell
    # 名前に "Remote" を含む全てのローカルグループを検索
    Get-LocalGroup -Name "*Remote*"
    ```

### 2. ブルーチーム視点

* **タスク**: **権限設定の監査**。システム上の特権グループ（Administrators, Remote Desktop Usersなど）が存在し、その説明が改ざんされていないかなどを確認する。
* **組み合わせ**: `Get-LocalGroup -Name "Administrators"`
* **解説**: `Get-LocalGroup` で特権グループのオブジェクトを取得し、`Description` などのプロパティが期待通りであるかを確認します。意図しないグループ（例: "Administrators " のように末尾にスペースがある偽装グループ）が作成されていないかの監査にも利用します。
* **例**:

    ```powershell
    # Administrators グループの詳細情報を取得
    Get-LocalGroup -Name "Administrators" | Format-List *
    ```

### 3. レッドチーム視点

* **タスク**: **偵察 (Reconnaissance)**。侵入したホストの権限構造を把握する。
* **組み合わせ**: `Get-LocalGroup`
* **解説**: 攻撃者は侵入後、まず `Get-LocalGroup` を実行して、そのマシンにどのようなローカルグループが存在するかをリストアップします。特に、`Administrators`, `Remote Desktop Users`, `Hyper-V Administrators` のような特権的なグループや、アプリケーション固有の管理者グループの存在は、権限昇格やラテラルムーブメントの標的を探す上で重要な手がかりとなります。
* **例**:

    ```powershell
    # 存在する全てのローカルグループ名をリストアップして偵察
    Get-LocalGroup | Select-Object Name
    ```

## エラーメッセージとトラブルシューティング

### よくあるエラーメッセージ

1. **エラーメッセージ**: `Get-LocalGroup : Group <groupname> was not found.`
    * **考えられる原因**: 指定した `-Name` のグループが存在しません。
    * **解決策**: グループ名のスペルが正しいか確認してください。引数を指定せずに `Get-LocalGroup` を実行して、存在するグループの一覧を確認するのも有効です。

## セキュリティに関する考慮事項

### 悪用事例

* **偵察 (Reconnaissance)**: レッドチームのシナリオで述べた通り、`Get-LocalGroup` は攻撃者が侵入したシステムの権限設定を理解し、次の攻撃ターゲット（特権グループ）を特定するための基本的な偵察ツールです。

### LOLBASにおける利用例

* **機能**: **偵察 (Reconnaissance)**
* **解説**: `Get-LocalGroup` は PowerShell の標準機能であり、`powershell.exe` を通じて実行されます。攻撃者はこの正規の管理機能を利用して、環境に溶け込みながらシステムの権限情報を収集します（Living off the Land）。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: 最小権限の原則を徹底し、不要な特権グループを作成したり、ユーザーを所属させたりしない。
* **Detection (検知)**: PowerShellのスクリプトブロックロギング (イベントID 4104) を有効化し、`Get-LocalGroup` の実行を記録・監視する。特に、他の偵察コマンド (`Get-LocalUser`, `Get-NetTCPConnection`など) と連続して実行される場合は、攻撃の兆候である可能性があります。

## 注意点・補足

* このコマンドレットは、`Microsoft.PowerShell.LocalAccounts` モジュールの一部です。このモジュールは、Windows 10、Windows Server 2016以降のバージョンにデフォルトで含まれています。

---
[PowerShellインデックスに戻る](../../powershell_index.md)

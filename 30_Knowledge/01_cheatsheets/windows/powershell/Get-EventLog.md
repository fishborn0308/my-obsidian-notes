---
tags:
  - 'Get-EventLog'
  - 'powershell'
  - 'cheatsheet'
title: 'Get-EventLog - クラシックイベントログからイベントを取得する (レガシー)'
summary: 'Windowsのクラシックイベントログ（Application, Security, Systemなど）からイベントを取得します。'
related:
  - 'Get-WinEvent'
  - 'wevtutil'
  - 'eventvwr'
---

# `Get-EventLog` - クラシックイベントログからイベントを取得する (レガシー)

## 概要

`Get-EventLog` コマンドレットは、ローカルまたはリモートコンピュータのクラシックイベントログ（Application, Security, Systemなど）からイベントを取得します。

**⚠️ 重要**: このコマンドレットは、Windows Vistaで導入された新しいWindowsイベントログ技術（`.evtx` ファイルや詳細な構造化ログ）に完全には対応していません。PowerShell 3.0以降では、より高機能でモダンな **`Get-WinEvent`** コマンドレットの使用が強く推奨されます。`Get-EventLog` は後方互換性のために残されています。

## 類似コマンドレット/コマンドとの差異

| 類似コマンドレット/コマンド | 差異 / 使い分けのポイント |
| :--- | :--- |
| ⭐ **`Get-WinEvent`** | `Get-WinEvent` は、クラシックログと新しい構造化ログの両方に対応し、XPathクエリを使った高度なフィルタリングが可能です。パフォーマンスも優れており、現在の標準です。新規のスクリプトでは**常に `Get-WinEvent` を使用すべきです**。 |
| **`wevtutil` (CMD)** | CUIベースのイベントログ管理ツール。`wevtutil` はログのクリアやエクスポートなど管理機能も持ちますが、PowerShellのオブジェクト指向の利点はありません。 |

## よく連携されるコマンドレット

### シナリオ例: システムログから最新のエラーイベントを取得する (インフラ構築・運用視点)

* **目的**: システムログに記録された、直近10件のエラーイベントを表示する。
* **連携コマンドレット**: `Where-Object` (フィルタリング)
* **解説**: `-LogName` で対象のログを指定し、`-Newest` で取得する件数を限定します。`-EntryType` でイベントの種類（Error, Warningなど）をフィルタリングするのが基本的な使い方です。
* **コマンド例**:

    ```powershell
    # システムログから最新10件のエラーイベントを取得
    Get-EventLog -LogName System -EntryType Error -Newest 10
    ```

## パラメータ説明

| パラメータ | 型 | 説明 |
| :--- | :--- | :--- |
| ⭐ **`-LogName`** | `String` | 取得するイベントログの名前を指定します (`Application`, `Security`, `System` など)。 |
| ⭐ **`-Newest`** | `Int32` | 取得する最新のイベント数を指定します。 |
| **`-After`** | `DateTime` | 指定した日時より**後**に生成されたイベントを取得します。 |
| **`-Before`** | `DateTime` | 指定した日時より**前**に生成されたイベントを取得します。 |
| **`-EntryType`** | `String[]` | 取得するイベントの種類を指定します (`Error`, `Warning`, `Information`, `SuccessAudit`, `FailureAudit`)。 |
| **`-InstanceId`** | `Int64[]` | イベントIDを指定します。 |
| **`-ComputerName`** | `String[]` | ログを取得するリモートコンピュータを指定します。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: 直近1時間以内に発生したアプリケーションのクラッシュイベント (イベントID 1000) を調査する。
* **組み合わせ**: `Get-EventLog -LogName Application -After (Get-Date).AddHours(-1) | Where-Object { $_.InstanceId -eq 1000 }`
* **解説**: `-After` パラメータで時間範囲を指定し、パイプラインで `Where-Object` を使って特定のイベントIDでフィルタリングします。
* **例**:

    ```powershell
    # Applicationログから直近1時間のイベントID 1000を検索
    $startTime = (Get-Date).AddHours(-1)
    Get-EventLog -LogName Application -After $startTime | Where-Object { $_.InstanceId -eq 1000 }
    ```

### 2. ブルーチーム視点

* **タスク**: **インシデント調査**。セキュリティログからログオン失敗 (FailureAudit) のイベントを抽出する。
* **組み合わせ**: `Get-EventLog -LogName Security -EntryType FailureAudit`
* **解説**: `-EntryType FailureAudit` を使って、ブルートフォース攻撃の兆候である可能性のあるログオン失敗イベントを素早くリストアップします。（`Get-WinEvent` を使った方がより詳細なフィルタリングが可能です）
* **例**:

    ```powershell
    # セキュリティログから最新50件のログオン失敗イベントを取得
    Get-EventLog -LogName Security -EntryType FailureAudit -Newest 50
    ```

### 3. レッドチーム視点

* **タスク**: **偵察 (Reconnaissance)** と**痕跡消去**。
* **解説**:
  * **偵察**: 攻撃者は侵入後、`Get-EventLog` を使ってシステムやセキュリティのログを調査し、管理者の活動パターン、ログオン/ログオフの時間、インストールされているソフトウェアなどの情報を収集します。
  * **痕跡消去**: `Clear-EventLog` コマンドレットと組み合わせて、自身の攻撃活動の痕跡（不正ログイン、コマンド実行など）を消去しようとします。
* **例**:

    ```powershell
    # 偵察のため、直近のセキュリティ監査成功イベントを確認
    Get-EventLog -LogName Security -EntryType SuccessAudit -Newest 20
    ```

## エラーメッセージとトラブルシューティング

### よくあるエラーメッセージ

1. **エラーメッセージ**: `Get-EventLog: The log <LogName> is not found on computer '.'`
    * **考えられる原因**: 指定した `-LogName` が存在しません。
    * **解決策**: `Get-EventLog -List` を実行して、利用可能なクラシックログの一覧を確認してください。

## セキュリティに関する考慮事項

### 悪用事例

* **偵察**: レッドチームのシナリオで述べた通り、イベントログは攻撃者にとって、システムの構成や運用状況、他のユーザーの活動を把握するための貴重な情報源です。
* **痕跡消去**: `Clear-EventLog` コマンドレットは、攻撃者が自身の活動を隠蔽するために使用する代表的な手法です。

### LOLBASにおける利用例

* **機能**: **偵察 (Reconnaissance)**
* **解説**: `Get-EventLog` は PowerShell の標準機能であり、攻撃者が環境に溶け込みながらシステムのログを調査するために利用されます。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: **ログの集中管理（ログフォワーディング）**。イベントログをリアルタイムでSIEMなどの外部ログサーバーに転送することで、ローカルホスト上でログがクリアされても証拠を保全できます。
* **Detection (検知)**: **イベントログがクリアされたことを示すイベントID 1102 (The audit log was cleared) を最優先で監視し、即座にアラートを発報するように設定する**。PowerShellのスクリプトブロックロギングを有効化し、`Get-EventLog` や `Clear-EventLog` の実行を監視する。

## 注意点・補足

* **`Get-WinEvent`への移行**: `Get-EventLog` は古いシステムや特定のシナリオでは依然として有用ですが、新しいWindowsイベントログの全機能（構造化データ、XPathクエリなど）を活用するには `Get-WinEvent` への移行が不可欠です。パフォーマンスも `Get-WinEvent` の方が大幅に優れています。

---
[PowerShellインデックスに戻る](../../powershell_index.md)

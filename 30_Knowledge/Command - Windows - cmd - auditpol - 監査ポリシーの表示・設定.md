---
created: '2026-02-21'
modified: '2026-02-21'
environment: [OS/Windows, OS/Windows_Server]
vulnearability: [Defense_Evasion]
knowledge_category: Command
tags:
  - 'cmd'
  - 'auditpol'
  - 'logging'
  - 'forensics'
  - 'defense_evasion'
  - 'knowledge_base'
---

# Command - Windows - cmd - auditpol - 監査ポリシーの表示・設定

## 概要

`auditpol` は、Windowsの監査ポリシーを表示、作成、変更するためのコマンドラインツールです。
システムが「どの操作をイベントログ（セキュリティログ）に記録するか」を細かく制御します。GUIの「ローカルセキュリティポリシー」よりも詳細なサブカテゴリ単位での設定が可能です。

(出自: `Windows 標準搭載`)

## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 (あれば) |
| :--- | :--- | :--- |
| `secpol.msc` | GUIツール。直感的な操作が可能だが、詳細なサブカテゴリ設定には `auditpol` が必要。 | - |
| `secedit` | セキュリティ構成のインポート/エクスポートに使用。監査ポリシー以外も含む包括的な設定。 | - |

## よく連携されるコマンド

### シナリオ例: 現在の監査設定のバックアップと確認 (ブルーチーム視点)

* **目的**: 事故対応（IR）や監査において、現在のログ取得設定が適切かを確認し、設定を保存する。
* **連携コマンド**: `auditpol`, `findstr`
* **解説**: 全設定をテキストに出力し、特定の重要なカテゴリ（「成功」のみ、「失敗」のみなど）を抽出します。
* **コマンド例**:
    ```cmd
    REM 現在のすべての監査設定をバックアップファイルに保存
    auditpol /backup /file:C:\temp\audit_policy.txt

    REM 「プロセスの作成」に関する現在の設定のみを確認
    auditpol /get /category:"詳細な追跡" | findstr "プロセスの作成"
    ```



## スイッチ/オプション説明

| スイッチ/オプション | 説明 |
| :--- | :--- |
| `/get` | 現在の監査ポリシーを表示する。 |
| `/set` | 監査ポリシーを設定・変更する。 |
| `/list` | 利用可能なカテゴリやサブカテゴリを一覧表示する。 |
| `/backup` | 監査ポリシーをファイルに保存する。 |
| `/restore` | ファイルから監査ポリシーを復元する。 |
| `/remove` | ユーザー単位の監査ポリシーを削除する。 |
| `/clear` | すべての監査ポリシーを消去する（非常に危険）。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点 (ブルーチーム/防御設計)

* **タスク**: セキュリティ要件に基づき、ログオン/ログオフの成功と失敗の両方を記録するように設定する。
* **組み合わせ**: `/set /subcategory:"<サブカテゴリ名>" /success:enable /failure:enable`
* **解説**: サーバー構築時、不正ログイン試行を検知するために必須の設定です。
* **例**:
    ```cmd
    REM ログオン状態の成功と失敗を両方記録
    auditpol /set /subcategory:"ログオン" /success:enable /failure:enable
    ```

### 2. ブルーチーム視点 (フォレンジック・検知)

* **タスク**: 攻撃者の挙動を追跡するため、コマンド実行履歴（プロセス作成）の監査を有効化する。
* **組み合わせ**: `/get /subcategory:"プロセスの作成"`
* **解説**: EDRやSIEMにログを送る前段階として、OS側でログが生成されているかを確認します。
* **例**:
    ```cmd
    REM 「詳細な追跡」カテゴリ内の「プロセスの作成」の現在の設定を確認
    auditpol /get /subcategory:"プロセスの作成"
    ```

### 3. レッドチーム視点 (防御回避)

* **タスク**: 自身の活動（アカウント作成、ファイルの操作など）がログに残らないように、一時的に監査を無効化する。
* **組み合わせ**: `/set /subcategory:"<サブカテゴリ名>" /success:disable /failure:disable`
* **解説**: 管理者権限を奪取した後、痕跡を残さないための Defense Evasion テクニックとして使用されます。
* **例**:
    ```cmd
    REM ユーザーアカウント管理のログ記録を一時的に停止
    auditpol /set /subcategory:"ユーザー アカウント管理" /success:disable /failure:disable
    ```

## エラーメッセージとトラブルシューティング

### よくあるエラーメッセージ
1.  **エラーメッセージ例 1**: `Error 0x00000005: Access is denied.`
    * **考えられる原因**: 管理者権限がない。
    * **解決策**: コマンドプロンプトを「管理者として実行」してください。

2.  **エラーメッセージ例 2**: `The parameter is incorrect.`
    * **考えられる原因**: カテゴリ名やサブカテゴリ名が言語設定（日本語/英語）に依存しているため、一致していない。
    * **解決策**: `auditpol /list /subcategory:*` を実行して、そのOSで有効な正確な名称を確認してください。

## 環境変数と設定ファイル

### 関連するレジストリ/設定ファイル

* **`HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\Lsa`**: 監査ポリシーに関連する重要なレジストリ設定（`SCENoApplyLegacyAuditPolicy` など）が格納されています。
* **`C:\Windows\system32\GroupPolicy\Machine\Microsoft\Windows NT\Audit\audit.csv`**: グループポリシーによって適用される監査設定のバックアップ。

## セキュリティに関する考慮事項

### 脆弱性と悪用事例

* **悪用シナリオ**: **Defense Evasion (T1562.002)**。攻撃者が `auditpol /clear` や `/set /success:disable` を実行してセキュリティ監視を盲目にします。

### LOLBAS (Living Off The Land Binaries and Scripts) における利用例

* **機能**: `Defense Evasion` (ログの無効化)
* **参照**: [LOLBAS - Auditpol.exe](https://lolbas-project.github.io/lolbas/Binaries/Auditpol/)

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: 特権ユーザーによる `auditpol` の使用を監視し、予期せぬ無効化が行われないよう制限する。
* **Detection (検知)**: 監査ポリシー自体が変更されたことを示す **イベントID 4719**（システム監査ポリシーが変更されました）を監視し、即座にアラートを上げるように設計する。

## 注意点・補足

* **言語の壁**: `auditpol` の引数（カテゴリ名）はOSのインストール言語に依存します。スクリプトを組む際は、言語に依存しない GUID を使用して指定する方法も検討してください。

---
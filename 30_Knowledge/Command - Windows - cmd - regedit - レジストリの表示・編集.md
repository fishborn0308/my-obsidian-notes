---
# --- YAML Frontmatter ---
created: 2026-02-22 09:54
modified: 2026-02-22 15:13
environment: [OS/Windows]
vulnearability: [Persistence, Privilege_Escalation, Credential_Access]
knowledge_category: Command
tags:
  - regedit
  - cmd
  - registry
  - configuration
  - knowledge_base
---

# Command - Windows - cmd - regedit - レジストリの表示・編集

## 概要

`regedit` は、Windows オペレーティング システムの構成設定が格納されているデータベース「レジストリ」を表示、検索、および変更するための GUI ツールです。

システム、ドライバー、サービス、アプリケーションの動作を決定する無数の設定値が含まれています。

(出自: Windows標準搭載)

## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 (あれば) |
| :--- | :--- | :--- |
| `reg` | **CLI版レジストリエディタ。** コマンドラインのみで値の追加、削除、検索が可能。スクリプトに最適。 | `reg` (自動化) |
| `Get-ItemProperty` | PowerShell版。レジストリを「ドライブ (HKLM:)」として扱い、高度な操作が可能。 | `PowerShell` (複雑な処理) |

## よく連携されるコマンド

### シナリオ例: 設定ファイル (.reg) のサイレント適用 (インフラ・レッドチーム)

* **目的**: ユーザーに画面を出さずに、あらかじめ作成したレジストリ設定をシステムに反映させる。
* **連携コマンド**: `echo` ( .regファイル作成用)
* **解説**: `/s` スイッチを使用することで、確認ダイアログを表示せずにインポートを完了させます。
* **コマンド例**:
    ```cmd
    REM 準備した settings.reg をサイレントにインポート
    regedit /s C:\Temp\settings.reg
    ```

## スイッチ/オプション説明

`regedit` は主に GUI として使われますが、CLI 引数で以下の動作が可能です。

| スイッチ | 説明 |
| :--- | :--- |
| ⭐ `/s` | **サイレントモード。** インポート時にメッセージを表示しない。 |
| `/e <File>` | 指定したファイルにレジストリキーをエクスポートする（古い形式）。 |
| `/m` | レジストリエディターのインスタンスを複数起動する（通常は1つのみ）。 |
| `[Path]` | 指定したレジストリパス（例: `HKEY_LOCAL_MACHINE\Software`）を開いた状態で起動する。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: アプリケーション配布時の設定一括適用
* **解説**: グループポリシー（GPO）が使えない環境や、キッティングスクリプト内で `/s` を用いて設定を流し込みます。
* **例**:
    ```cmd
    REM プロキシ設定などを .reg 経由で適用
    regedit /s \\Server\Share\ProxySettings.reg
    ```

### 2. ブルーチーム視点

* **タスク**: 永続化（Persistence）ポイントの調査
* **解説**: マルウェアが自動起動のために悪用する「Runキー」などを目視で調査します。
* **調査対象例**:
    - `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Run`
    - `HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Run`

### 3. レッドチーム視点

* **タスク**: RDP（リモートデスクトップ）の有効化
* **解説**: シェルを取得した際、レジストリを書き換えて RDP サービスを許可し、GUI での操作を可能にします。
* **例**:
    ```cmd
    REM RDPを有効化する設定例（実際は reg コマンドや regedit /s で実行）
    reg add "HKLM\System\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f
    ```

## エラーメッセージとトラブルシューティング

### よくあるエラーメッセージ

1.  **エラーメッセージ例 1**: `レジストリを編集することは、管理者によって禁止されています。`
    * **考えられる原因**: グループポリシーによってレジストリ編集ツール（regedit.exe）の使用が制限されている。
    * **解決策**: ポリシーを解除するか、`reg` コマンドなど別の手段を試す。

2.  **エラーメッセージ例 2**: `キー '...' を開くときにエラーが発生しました。`
    * **考えられる原因**: 参照しようとしているキーに対するアクセス権限（ACL）がない。
    * **解決策**: 「管理者として実行」する。それでもダメな場合は `SYSTEM` 権限への昇格が必要な場合がある。

## 環境変数と設定ファイル

レジストリ自体が Windows の「究極の設定ファイル」です。物理的には以下のファイルなどに分割されて保存されています（これを「ハイブ」と呼びます）。

* `%SystemRoot%\System32\Config\SYSTEM`
* `%SystemRoot%\System32\Config\SOFTWARE`
* `%UserProfile%\NTUSER.DAT`

## セキュリティに関する考慮事項

### 脆弱性と悪用事例

* **悪用シナリオ**:
    - **LSA Secrets**: 過去にログインしたユーザーのパスワードハッシュなどがレジストリにキャッシュされている場合があり、これを窃取される（Credential Access）。
    - **UAC Bypass**: 特定のレジストリ値を書き換えることで、ユーザーに通知を出さずに管理者権限でコマンドを実行させる。

### LOLBAS (Living Off The Land Binaries and Scripts) における利用例

* **機能**: `Execute`, `UAC Bypass`
* **解説**: `/s` オプションを利用して悪意ある設定を静かに流し込む、あるいはレジストリを介したコード実行に利用される。
* **参照**: [LOLBAS - regedit.exe](https://lolbas-project.github.io/lolbas/Binaries/Regedit/)

### 対応策・緩和策 (ブルーチーム視点)

* **Detection (検知)**:
    - **イベントID 4657**: レジストリ値が変更されたことを監視（監査ポリシーの有効化が必要）。
    - 重要なキー（Runキー、Image File Execution Optionsなど）の変更を EDR で監視する。

## 注意点・補足

* **即時反映**: 多くの設定は変更した瞬間に反映されますが、一部の設定（システム全体に関わるものなど）は再起動やログオフが必要です。
* **バックアップ**: 編集ミスはシステムが起動しなくなる原因となるため、変更前に必ず該当キーの「エクスポート」を行うのが鉄則です。

---
---
# --- YAML Frontmatter ---
created: 2026-02-22 15:10
modified: 2026-02-23 09:52
environment: [OS/Windows_Server, Active_Directory]
vulnearability: [Credential_Access, Information_Disclosure]
knowledge_category: Command
tags:
  - ntdsutil
  - cmd
  - active_directory
  - ntds_dit
  - fsmo
  - knowledge_base
---

# Command - Windows - cmd - ntdsutil - Active Directory データベースの管理

## 概要

`ntdsutil` は、Active Directory Domain Services (AD DS) および Active Directory Lightweight Directory Services (AD LDS) のデータベースを管理・保守するためのコマンドライン ツールです。

FSMO ロールの転送、メタデータのクリーンアップ、スナップショットの管理、IFM (Install From Media) 用のデータ作成など、GUI では実行できない高度なディレクトリ操作を可能にします。

(出自: Windows 2000 Server 以降標準搭載)

## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 (あれば) |
| :--- | :--- | :--- |
| `dsa.msc` | 「AD ユーザーとコンピューター」GUI。日常的なユーザー管理用。 | `dsa.msc` (一般管理) |
| `vssadmin` | ボリューム シャドウ コピーの管理。AD 以外のスナップショットも扱う。 | `vssadmin` (汎用スナップショット) |
| `Move-ADDirectoryServerOperationMasterRole` | PowerShell 版。FSMO ロールの転送などをスクリプト化する場合。 | `PowerShell` (自動化) |

## よく連携されるコマンド

### シナリオ例: 障害が発生したドメインコントローラーの残骸削除 (メタデータクリーンアップ)

* **目的**: 正常に降格できなかった古いドメインコントローラーの情報を AD 内から完全に削除する。
* **解説**: `ntdsutil` の `metadata cleanup` サブコマンドを使用して、AD データベース内の整合性を保ちます。
* **コマンド例**:
    ```cmd
    ntdsutil "metadata cleanup" "remove selected server <ServerName>" quit
    ```

## スイッチ/サブコマンド説明

`ntdsutil` は対話型シェル形式で動作します。

| サブコマンド | 説明 |
| :--- | :--- |
| ⭐ `roles` | FSMO (操作マスター) ロールの管理、転送、および強制取得 (Seize) を行う。 |
| ⭐ `ifm` | **Install From Media.** 新しい DC 構築用に AD データベースのコピーを作成する。 |
| ⭐ `snapshot` | AD データベースの VSS スナップショットを作成・管理する。 |
| ⭐ `metadata cleanup` | ネットワークから削除されたサーバーの古い情報を AD から削除する。 |
| `files` | AD データベース (`ntds.dit`) の整合性チェック、圧縮、移動を行う。 |
| `set password` | 特定のアカウントのパスワードをリセットする（一部の特殊なケース）。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: 遠隔地への DC 設置用メディア (IFM) の作成
* **解説**: 回線速度が遅い拠点に新しい DC を立てる際、AD データを丸ごとエクスポートして持ち運べるようにします。
* **例**:
    ```cmd
    ntdsutil "ac i ntds" "ifm" "create full C:\AD_Backup" quit
    ```

### 2. ブルーチーム視点

* **タスク**: AD データベースの整合性監査
* **解説**: 不正な書き換えや破損がないか、`files` サブコマンドでデータベースの物理的整合性をチェックします。
* **例**: `ntdsutil "files" "integrity" quit`

### 3. レッドチーム視点 (OSCP / Credential Access)

* **タスク**: ドメイン全体のパスワードハッシュの窃取 (NTDS.dit Dump)
* **解説**: 侵入したドメインコントローラー上で `ifm` サブコマンドを悪用し、ロックされている `ntds.dit` を「メディア作成」の名目でコピー（ダンプ）します。
* **例**:
    ```cmd
    REM ifm 機能を使って ntds.dit と SYSTEM ハイブを C:\temp に書き出す
    ntdsutil "ac i ntds" "ifm" "create full C:\temp" quit
    ```

    → 取得した `ntds.dit` と `SYSTEM` ハイブをオフラインで解析することで、ドメイン全体の全ユーザーのハッシュを入手できます。

## エラーメッセージとトラブルシューティング

### よくあるトラブル

1.  **現象**: `NTDS 定義を読み込めません` と表示される。
    * **原因**: 対象のサーバーがドメインコントローラーではない、または AD サービスが停止している。
    * **解決策**: `Active Directory Domain Services` サービスが実行中であることを確認する。

2.  **現象**: `ifm` 実行時に「ディスク領域が不足しています」と出る。
    * **原因**: `ntds.dit` は非常に巨大なファイル（数GB以上）になることがあります。
    * **解決策**: 十分な空き容量のあるドライブを出力先に指定する。

## セキュリティに関する考慮事項

### 脆弱性と悪用事例

* **悪用シナリオ**:
    - **FSMO Role Seizure**: 攻撃者が権限を奪取した後、強制的にロールを奪い取り、AD 環境を意図的に崩壊させる、または支配を強める。
    - **Offline Cracking**: 前述の `ifm` を使用した `ntds.dit` の窃取は、検出が難しく（正規の管理機能であるため）、かつ非常に致命的です。

### LOLBAS (Living Off The Land Binaries and Scripts) における利用例

* **機能**: `Credential Access`, `Dump`
* **参照**: [LOLBAS - ntdsutil.exe](https://lolbas-project.github.io/lolbas/Binaries/Ntdsutil/)

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**:
    - ドメインコントローラーへの物理・論理アクセスを「Domain Admins」などの最小限の特権ユーザーに制限する。
* **Detection (検知)**:
    - **イベントID 4688**: `ntdsutil.exe` の実行、特に引数に `ifm` や `create` を含むものを監視。
    - **EASM/EDR**: DC 上での `ntdsutil` の不審な起動を即座にアラート対象とする。

## 注意点・補足

* **対話モードの短縮**: `ntdsutil "ac i ntds" ifm ...` のように引用符で囲んでコマンドを繋げることで、対話プロンプトに入らずに一行で実行可能です。
* **管理者権限**: ドメインコントローラー上でのローカル管理者、またはドメイン管理者権限が必須です。

---
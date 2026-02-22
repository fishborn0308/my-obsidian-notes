---
# --- YAML Frontmatter ---
created: 2026-02-22 15:10
modified: 2026-02-22 15:13
environment: [OS/Windows, Backup/Recovery]
vulnearability: [Data_Exfiltration, Ransomware_Behavior]
knowledge_category: Command
tags:
  - vssadmin
  - cmd
  - vss
  - shadow_copy
  - persistence
  - forensics
  - knowledge_base
---

# Command - Windows - cmd - vssadmin - ボリューム シャドウ コピー サービスの管理

## 概要

`vssadmin` は、Windows のボリューム シャドウ コピー サービス (VSS) を操作・管理するツールです。

シャドウ コピーとは、ある時点でのボリューム（ドライブ）の複製（スナップショット）を保存する機能で、ファイルが使用中（ロック中）であってもバックアップを取ることを可能にします。

(出自: Windows Server 2003 以降標準搭載)

## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 (あれば) |
| :--- | :--- | :--- |
| `wbadmin` | Windows Server バックアップの操作。バックアップ全体の管理に向く。 | `wbadmin` (全体バックアップ) |
| `ntdsutil` | Active Directory の管理ツール。AD データベースの「スナップショット」取得に特化。 | `ntdsutil` (AD復旧) |
| `New-VSSSnapshot` | PowerShell (VSS モジュール)。スクリプトによる高度な自動化が可能。 | `PowerShell` |

## よく連携されるコマンド

### シナリオ例: ロックされたシステムファイルのバックアップ (インフラ運用)

* **目的**: システム稼働中に、通常はロックされてコピーできないファイルを、スナップショット経由でバックアップする。
* **連携コマンド**: `copy`, `mklink`
* **解説**: シャドウコピーを作成し、それをシンボリックリンクとしてマウントして中身をコピーします。
* **コマンド例**:
    ```cmd
    REM Cドライブのシャドウコピーを作成
    vssadmin create shadow /for=C:
    ```

## スイッチ/オプション説明

`vssadmin` は [サブコマンド] [オプション] の形式で実行します。

| サブコマンド | 説明 |
| :--- | :--- |
| ⭐ `list shadows` | 現在存在するシャドウ コピーの一覧を表示する。 |
| ⭐ `list writers` | 登録されている VSS ライター（SQL Server や AD など）の状態を確認する。 |
| ⭐ `delete shadows` | シャドウ コピーを削除する。**（ランサムウェアが多用する）** |
| `create shadow` | 新しいシャドウ コピーを作成する。 |
| `list providers` | インストールされている VSS プロバイダーを表示する。 |
| `resize shadowstorage` | シャドウ コピーを保存する領域（記憶域）のサイズ制限を変更する。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: バックアップ失敗時の原因調査
* **解説**: バックアップソフトがエラーを吐く際、`list writers` で特定のサービス（ライター）が「失敗」状態で固まっていないか確認します。
* **例**:
    ```cmd
    vssadmin list writers
    ```

    → 状態が `[1] 安定` 以外であれば、関連サービスの再起動が必要です。

### 2. ブルーチーム視点 (デジタルフォレンジック)

* **タスク**: 削除されたファイルの復元調査
* **解説**: 攻撃者がファイルを消去しても、古いシャドウコピーが残っていれば、そこから過去のファイルを抽出して解析できます。
* **例**: `vssadmin list shadows` で過去のスナップショットを確認。

### 3. レッドチーム視点 (Credential Access / Ransomware)

* **タスク**: Active Directory データベース (`NTDS.dit`) の窃取
* **解説**: 稼働中のドメインコントローラーでは `NTDS.dit` はロックされていますが、シャドウコピーを作成することで、ロックされていない「複製」をコピーし、オフラインでパスワード解析を行えます。
* **例**:
    ```cmd
    vssadmin create shadow /for=C:
    copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1\Windows\NTDS\NTDS.dit C:\temp\
    ```

* **タスク**: 復旧手段の破壊 (Ransomware 挙動)
* **解説**: ランサムウェアはファイルを暗号化する直前に、ユーザーが「以前のバージョン」から復元できないようにシャドウコピーをすべて消去します。
* **例**: `vssadmin delete shadows /all /quiet`

## エラーメッセージとトラブルシューティング

### よくあるトラブル

1.  **現象**: `list shadows` で何も表示されない。
    * **原因**: 「システムの保護」が無効であるか、バックアップが一度も実行されていない。
2.  **現象**: `create shadow` が失敗する。
    * **原因**: 記憶域（ShadowStorage）の空き容量が不足している。
    * **解決策**: `vssadmin resize shadowstorage` で最大サイズを増やすか、古いシャドウコピーを消去する。

## セキュリティに関する考慮事項

### 脆弱性と悪用事例

* **悪用シナリオ**:
    - **Shadow Copy Deletion**: 侵入した攻撃者が、フォレンジックやデータ復旧を妨害するために実行。
    - **Credential Theft**: 前述の通り、AD データベースやレジストリハイブのコピーに使用。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**:
    - 一般ユーザーから `vssadmin` の実行権限を剥奪する（通常は管理者権限が必要）。
* **Detection (検知)**:
    - **イベントID 4688**: `vssadmin.exe delete shadows` の実行を即座にアラート対象とする。これは**ランサムウェアの極めて強力なインジケーター (IoC)** です。
    - **イベントID 13 (VSS)**: シャドウコピーの作成・削除ログを監視。

## 注意点・補足

* **管理者権限**: すべての操作に管理者権限（昇格したプロンプト）が必要です。
* **コマンドの沈黙**: `/quiet` スイッチを付けるとユーザーへの確認なしに削除が実行されるため、スクリプト（や悪意あるプログラム）で多用されます。

---
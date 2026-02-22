---
created: '2026-02-22'
modified: '2026-02-22'
environment: [OS/Windows, Hardware/Storage]
vulnearability: [Information_Disclosure, Data_Destruction, Persistence]
knowledge_category: Command
tags:
  - 'cmd'
  - 'diskpart'
  - 'partition'
  - 'disk_management'
  - 'knowledge_base'
---

# Command - Windows - cmd - diskpart - ディスク パーティションの管理

## 概要

`diskpart` は、ディスク、パーティション、ボリューム、および仮想ハード ディスク (VHD) を管理するための対話型コマンドライン インタープリターです。GUI の「ディスクの管理」ツールで可能なすべての操作に加え、パーティションの ID 変更や詳細情報の表示など、より高度な操作が可能です。

(出自: `Windows 標準搭載 - 外部コマンド (diskpart.exe)`)

## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 (あれば) |
| :--- | :--- | :--- |
| `diskmgmt.msc` | GUI 版。視覚的にディスクの状態を把握・操作できる。 | 一般的な操作 |
| `Storage` モジュール | PowerShell 版（例: `Get-Disk`, `Initialize-Disk`）。スクリプト化が容易。 | PS 環境 |
| `fsutil` | ファイルシステムレベルの操作（クォータ設定、ハードリンク等）に特化。 | ファイルシステム管理 |

## よく連携されるコマンド

### シナリオ例: 新規追加ディスクの初期化とフォーマット (インフラ構築)

* **目的**: サーバーに追加した未割り当てのディスクを使用可能な状態にする。
* **連携コマンド**: `diskpart`, `format`
* **解説**: `diskpart` でパーティションを作成し、ドライブ文字を割り当てます。
* **コマンド例**:
    ```cmd
    REM diskpart を起動して一連の操作を行う
    diskpart
    DISKPART> list disk
    DISKPART> select disk 1
    DISKPART> create partition primary
    DISKPART> format fs=ntfs quick
    DISKPART> assign letter=E
    DISKPART> exit
    ```



## 主要なサブコマンド (DISKPART プロンプト内)

**※実行には「管理者権限」が必須です。**

| サブコマンド | 説明 |
| :--- | :--- |
| **列挙 (List)** | |
| `list disk` | 物理ディスクの一覧を表示する。 |
| `list volume` | すべてのドライブ文字（C:, D: 等）を含むボリューム一覧を表示する。 |
| ⭐ `list partition` | 選択中のディスク内にあるパーティションを一覧表示する。 |
| **選択 (Select)** | |
| `select disk [n]` | 操作対象のディスクを指定する。 |
| `select volume [n]` | 操作対象のボリュームを指定する。 |
| **操作 (Action)** | |
| ⭐ `detail disk` | ディスクの詳細情報（パス、ターゲット ID、読み取り専用属性等）を表示。 |
| `assign letter=[L]` | ドライブ文字を割り当てる。 |
| `remove letter=[L]` | ドライブ文字を削除する（非表示化）。 |
| ⭐ `extend` | パーティションを未割り当て領域へ拡張する。 |
| `clean` | ディスク上のすべての構成情報を削除する（データ抹消）。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: 仮想マシンのシステムドライブ (C:) が容量不足になった際、ディスクを拡張した後にパーティションを広げる。
* **例**:
    ```cmd
    DISKPART> select volume c
    DISKPART> extend
    ```

### 2. ブルーチーム視点 (デジタルフォレンジック)

* **タスク**: 隠しパーティションや、通常の OS からは見えない領域（Recovery パティション等）に不審なデータが格納されていないか調査する。
* **例**:
    ```cmd
    REM 通常は見えないパーティションにドライブ文字を割り当てて中身を確認
    DISKPART> list partition
    DISKPART> select partition 3
    DISKPART> assign letter=Z
    ```

### 3. レッドチーム視点 (偵察とデータ窃取)

* **タスク 1: Storage Enumeration (詳細なストレージ調査)**
    * **目的**: 接続されている USB メモリ、外部 HDD、またはマウントされている ISO/VHD ファイルを特定する。
    * **解説**: `list volume` では見えない「マウントされていないパーティション」を `list disk` と `list partition` で洗い出します。

* **タスク 2: Mounting VHDs (仮想ディスクの悪用)**
    * **目的**: 盗み出したバックアップファイル（.vhdx）をその場でマウントし、中のデータを閲覧する。
    * **コマンド例**:
        ```cmd
        DISKPART> select vdisk file="C:\temp\backup.vhdx"
        DISKPART> attach vdisk
        ```

* **タスク 3: Data Destructive (データの破壊/妨害 - T1485)**
    * **目的**: 攻撃の最終段階で、`clean` コマンドを使用してディスクのパーティション情報を破壊し、システムを起動不能にする。

## エラーメッセージとトラブルシューティング

1.  **No disk selected.**
    * **原因**: 操作対象（Disk, Volume 等）を `select` せずにコマンドを実行した。
    * **解決策**: まず `list disk` を行い、`select disk 0` のように対象を確定させてください。

2.  **Access is denied.**
    * **原因**: 管理者権限がない、または他のプロセスがディスクを排他的にロックしている。

3.  **The volume is too small to be extended.**
    * **原因**: 拡張しようとしているパーティションの直後に未割り当て領域がない。

## セキュリティに関する考慮事項

### 脆弱性と悪用事例

* **隠しドライブの露出**: 
    * 攻撃者は `diskpart` を使って、管理者が隠したドライブ（バックアップ用やツール保管用）にドライブ文字を再割り当てし、アクセスを試みます。
* **Anti-Forensics**: 
    * `clean` コマンドは、クイックフォーマットよりも深くパーティションテーブルを破壊するため、簡易的なデータ復元を困難にします。

### LOLBAS (Living Off The Land Binaries and Scripts) における利用例

* **機能**: `Execute`, `Information Discovery`
* **特性**: スクリプトファイル（`/s` スイッチ）を読み込んで自動実行できるため、一連の破壊工作や設定変更を自動化するのに悪用されます。
* **参照**: [LOLBAS - diskpart.exe](https://lolbas-project.github.io/lolbas/Binaries/Diskpart/)

### 対応策・緩和策 (ブルーチーム視点)

* **Detection (検知)**:
    * **イベントID 4688**: `diskpart.exe` の実行、特に `/s` スイッチを使用したスクリプト実行を監視。
    * **スクリプト内容の監視**: 実行された `diskpart` スクリプト内に `clean`, `delete`, `assign` などのキーワードが含まれていないかチェック。
* **Prevention (予防)**:
    * サーバーへのリモートアクセス権限を最小限に抑え、一般ユーザーが `diskpart` を実行できないよう制限する（標準で管理者権限が必要だが、特権昇格後の活動を注視）。

## 注意点・補足

* **非対話実行 (自動化)**: `diskpart /s script.txt` のように、コマンドを記述したテキストファイルを読み込ませることで、バッチファイルから自動実行が可能です。
* **即時反映**: `diskpart` での操作（特に `clean` や `delete`）は**「元に戻す」ことができない**ため、実行前に必ずバックアップや確認を行う必要があります。

---
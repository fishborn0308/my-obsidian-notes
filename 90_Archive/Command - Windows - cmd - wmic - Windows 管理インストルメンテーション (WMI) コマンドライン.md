---
# --- YAML Frontmatter ---
created: 2026-02-22 15:12
modified: 2026-02-23 09:52
environment: [OS/Windows]
vulnearability: [Information_Disclosure, Lateral_Movement]
knowledge_category: Command
tags:
  - wmic
  - cmd
  - wmi
  - enumeration
  - reconnaissance
  - knowledge_base
---

# Command - Windows - cmd - wmic - Windows 管理インストルメンテーション (WMI) コマンドライン

## 概要

`wmic` は、Windows 管理インストルメンテーション (WMI) をコマンドラインから操作するためのツールです。

ローカルまたはリモートのコンピューターに対して、ハードウェア構成、OS の設定、実行中のプロセス、インストール済みのソフトウェアなどの情報をクエリしたり、特定のアクション（プロセスの起動や停止など）を実行したりできます。

(出自: Windows 2000 以降標準搭載。現在は非推奨)

## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 (あれば) |
| :--- | :--- | :--- |
| `Get-CimInstance` | PowerShell 版。`wmic` の後継。出力がオブジェクト形式で扱いやすい。 | **PowerShell** (現代の標準) |
| `systeminfo` | OS 全体の概要をまとめて表示。`wmic` は個別の項目を深掘りするのに向く。 | `systeminfo` (概要確認) |
| `tasklist` | プロセス一覧のみを表示。`wmic process` は実行パスや引数も詳しく見れる。 | `wmic` (詳細調査) |

## 基本構文

`wmic [エイリアス] [動詞] [オプション]` の形式で実行します。

* **エイリアス (Alias)**: `process`, `service`, `os`, `bios`, `product`, `qfe` など。
* **動詞 (Verb)**: `get` (取得), `list` (一覧), `call` (メソッド実行), `set` (設定変更), `delete` (削除)。

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: インストール済みの修正プログラム（パッチ）の確認
* **解説**: Windows Update の適用状況をリスト化します。
* **コマンド例**:
    ```cmd
    REM インストール済みのパッチ ID と適用日を表示
    wmic qfe get HotFixID,InstalledOn
    ```

* **タスク**: PC のシリアル番号の取得
* **解説**: 物理的な PC を開けたり裏返したりせずに、資産管理用のシリアルを確認します。
* **コマンド例**:
    ```cmd
    wmic bios get serialnumber
    ```

### 2. ブルーチーム視点

* **タスク**: 不審なプロセスのコマンドライン引数の調査
* **解説**: `taskmgr` では見えにくい「そのプロセスがどのような引数で起動されたか」を暴きます。
* **コマンド例**:
    ```cmd
    REM 特定のプロセス名（例: powershell.exe）の実行パスとコマンドライン引数を表示
    wmic process where name="powershell.exe" get CommandLine,ExecutablePath
    ```

### 3. レッドチーム視点 (OSCP / Enumeration)

* **タスク**: インストール済みソフトウェアの列挙
* **解説**: ターゲットにどのような脆弱なソフトが入っているか、あるいはセキュリティソフト（AV/EDR）は何かを調査します。
* **コマンド例**:
    ```cmd
    REM インストール済みの製品名とバージョンをリスト（非常に重いコマンドなので注意）
    wmic product get name,version
    ```

* **タスク**: 横展開 (Lateral Movement)
* **解説**: 奪取した管理者資格情報を使用して、リモートマシン上でプロセスを起動させます。
* **コマンド例**:
    ```cmd
    REM リモートマシン (TargetPC) で計算機を起動させる（テスト用）
    wmic /node:"TargetPC" process call create "calc.exe"
    ```

## 重要：非推奨化と将来について

Microsoft は `wmic` の使用を段階的に制限しています。

- **Windows 11 (24H2 以降)**: 一部の環境でデフォルトで機能が削除または無効化されています。
- **代替案**: 今後は PowerShell の `Get-CimInstance` を使用してください。
    - 例: `wmic process get name` → `Get-CimInstance Win32_Process | Select-Object Name`

## セキュリティに関する考慮事項

### 脆弱性と悪用事例

* **悪用シナリオ**:
    - **Reconnaissance**: 侵入直後の偵察。`wmic` ひとつでマシンの全貌がわかってしまいます。
    - **Anti-Forensics**: `wmic shadowcopy delete` による復旧ポイントの削除（ランサムウェアの定石）。

### LOLBAS (Living Off The Land Binaries and Scripts) における利用例

* **機能**: `Execute`, `Information Requirements`
* **参照**: [LOLBAS - wmic.exe](https://lolbas-project.github.io/lolbas/Binaries/Wmic/)

### 対応策・緩和策 (ブルーチーム視点)

* **Detection (検知)**:
    - **イベントID 4688**: `wmic.exe` の起動を監視。特に `/node` (リモート) や `process call create` (実行) を含む引数は要警戒。
    - **WMI ログ**: `Microsoft-Windows-WMI-Activity/Operational` ログで不審な WMI クエリを監視。

## 注意点・補足

* **実行速度**: `wmic product` は非常に時間がかかり、システムの負荷も高いため、本番環境での実行は慎重に行ってください。
* **リモート操作**: リモートマシンに対して実行する場合、相手側で RPC (TCP 135) および動的ポートの開放が必要です。

---
---
created: 2026-02-22 07:56
modified: 2026-02-22 09:30
environment: [OS/Windows, Hardware/Driver]
vulnearability: [Privilege_Escalation, Rootkit, Information_Disclosure]
knowledge_category: Command
tags:
  - cmd
  - driverquery
  - enumeration
  - kernel_driver
  - knowledge_base
---

# Command - Windows - cmd - driverquery - デバイス ドライバーの表示と管理

## 概要

`driverquery` は、インストールされているすべてのデバイス ドライバーの一覧とそのプロパティ（モジュール名、表示名、ドライバーの種類、リンク日付など）を表示します。システムでどのようなカーネルモード ソフトウェアが動作しているかを確認するための強力な診断ツールです。

(出自: `Windows 標準搭載 - 外部コマンド (driverquery.exe)`)

## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 (あれば) |
| :--- | :--- | :--- |
| `sc query type= driver` | サービス管理ツール経由での調査。ドライバーの状態（実行中/停止）に強い。 | サービス状態確認 |
| `Get-WindowsDriver` | PowerShell 版。オンライン/オフラインイメージのドライバー詳細を取得可能。 | PS 環境 |
| `pnputil` | ドライバー パッケージの追加、削除、エクスポートなど「操作」に長けている。 | ドライバー管理 |
| **デバイス マネージャー** | GUI 版。視覚的にハードウェアとの紐付けを確認できる。 | 一般的な確認 |

## よく連携されるコマンド

### シナリオ例: 署名のない不審なドライバーの特定 (ブルーチーム視点)

* **目的**: デジタル署名がない、あるいは信頼できない発行元によるドライバーを特定し、ルートキットの可能性を調査する。
* **連携コマンド**: `driverquery`, `findstr`
* **解説**: `/si` スイッチで署名情報を取得し、不一致を抽出します。
* **コマンド例**:
    ```cmd
    REM 署名されていない（または情報がない）ドライバーをリストアップ
    driverquery /si | findstr /v "Microsoft"
    ```

## スイッチ/オプション説明

| スイッチ/オプション | 説明 |
| :--- | :--- |
| **基本表示** | |
| (引数なし) | インストールされているドライバーの基本一覧を表示する。 |
| ⭐ `/v` | **詳細表示**。ドライバーの状態、開始の種類、メモリ使用量、パスなどを表示。 |
| ⭐ `/si` | **署名情報**を表示。ドライバーがデジタル署名されているかどうかを確認。 |
| **出力形式** | |
| `/fo {TABLE|LIST|CSV}` | 出力形式を指定。デフォルトは TABLE。CSV は解析に便利。 |
| `/nh` | 出力にカラムヘッダーを表示しない（CSV 出力時などに有効）。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: 特定のハードウェア（例：NIC や RAID カード）のドライバー バージョンが、メーカー推奨と一致しているか確認する。
* **例**:
    ```cmd
    REM ネットワーク関連のドライバー詳細を表示
    driverquery /v | findstr /i "network eth nic"
    ```

### 2. ブルーチーム視点 (ルートキット調査)

* **タスク**: 起動時に自動実行される不審なカーネル ドライバーが、正規のディレクトリ (`C:\Windows\System32\drivers`) 以外から読み込まれていないか確認する。
* **例**:
    ```cmd
    REM ドライバーのファイルパスを含めて詳細表示
    driverquery /v /fo list
    ```

### 3. レッドチーム視点 (権限昇格の偵察)

* **タスク 1: Vulnerable Driver Hunting (脆弱なドライバーの探索)**
    * **目的**: 既知の脆弱性がある古いドライバー（例: CAPCOM.sys や古いアンチウイルス、オーバークロックツールのドライバー）を探し、カーネル権限への昇格（BYOVD: Bring Your Own Vulnerable Driver）の足がかりにする。
    * **解説**: ドライバー名と日付（Link Date）から、既知の脆弱性 DB と照合します。
    * **コマンド例**:
        ```cmd
        REM ドライバー名とリンク日付を一覧表示
        driverquery /fo table /nh
        ```

* **タスク 2: EDR/AV Enumeration (セキュリティ製品の特定)**
    * **目的**: 導入されているセキュリティ製品（EDR、HIPS 等）のドライバー名を特定し、回避策を練る。
    * **解説**: `CrowdStrike`, `SentinelOne`, `CarbonBlack` 等のベンダー固有のドライバーを探します。

## エラーメッセージとトラブルシューティング

1.  **情報の不一致（/si スイッチ）**
    * **現象**: `driverquery /si` で「情報なし」や「不明」と表示される。
    * **原因**: ネットワーク経由でリモート操作している場合や、サードパーティ製の特殊なドライバーで署名検証が通らない場合。
    * **解決策**: `sigcheck` (Sysinternals) などのより高度な検証ツールの併用を検討してください。

2.  **Access is denied. (アクセス拒否)**
    * **原因**: 一部の詳細情報（`/v` スイッチ等）の取得には管理者権限が必要です。

## セキュリティに関する考慮事項

### 脆弱性と悪用事例

* **BYOVD (Bring Your Own Vulnerable Driver):** * 攻撃者は管理者権限を得た後、意図的に脆弱な古い（しかし署名は有効な）ドライバーをインストールし、その脆弱性を突くことで、さらに深いカーネル特権を奪取しようとします。`driverquery` はそのための「隙」を探すために悪用されます。
* **Rootkit Persistence:** * 攻撃者はルートキットをドライバーとして登録し、`driverquery` などの標準ツールからも検知しにくい（あるいは正規の名前に偽装する）形で潜伏を維持します。

### LOLBAS (Living Off The Land Binaries and Scripts) における利用例

* **機能**: `Reconnaissance` (偵察)
* **参照**: [LOLBAS - driverquery.exe](https://lolbas-project.github.io/lolbas/Binaries/Driverquery/)

### 対応策・緩和策 (ブルーチーム視点)

* **Detection (検知)**:
    * **イベントID 4688**: `driverquery.exe` の実行、特に `/v` や `/si` スイッチを伴う不審な頻度の実行を監視。
    * **不審なドライバーの読み込み**: イベントログ（Source: `Service Control Manager`, ID: `7045`）で新しいサービス（ドライバー）の登録を監視。
* **Prevention (予防)**:
    * **HVCI (Hypervisor-Protected Code Integrity)** の有効化。これにより、署名のない、または脆弱なドライバーの読み込みをハードウェアレベルで制限できます。

## 注意点・補足

* **実行環境**: `driverquery` はリモートのコンピュータに対しても `/s` スイッチで実行可能ですが、認証とポート（RPC/WMI）の開放が必要です。
* **名前の偽装**: 攻撃者はドライバーの「表示名（Display Name）」を `Windows Kernel Driver` のように偽装することがありますが、「モジュール名（Module Name）」や「ファイルパス」を精査することで正体を見破れることがあります。

---
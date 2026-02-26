---
created: 2026-02-22 08:17
modified: 2026-02-22 09:30
environment: [OS/Windows, Service/Management]
vulnearability: [Privilege_Escalation, Defense_Evasion]
knowledge_category: Command
tags:
  - cmd
  - dism
  - system_repair
  - feature_management
  - image_servicing
  - knowledge_base
---

# Command - Windows - cmd - dism - システム イメージの管理と修復

## 概要

`dism` (展開イメージのサービスと管理) は、Windows イメージ（.wim）や仮想ハードディスク（.vhd/.vhdx）を修復、準備、および変更するためのコマンドライン ツールです。

実行中の OS (Online) に対して、壊れたシステムファイルの修復、Windows 機能（IIS, SNMP, SMB1.0 など）の有効化・無効化、ドライバの追加など、OS の「構成」そのものを操作するために使用されます。

(出自: `Windows 標準搭載 - 外部コマンド (dism.exe)`)

## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 (あれば) |
| :--- | :--- | :--- |
| `sfc /scannow` | メモリ上のシステムファイルをスキャンして修復。`dism` よりも手軽。 | まずは `sfc` |
| `sc` | サービスの起動・停止。`dism` は機能（コンポーネント）の有効化を行う。 | サービス管理 |
| `Add-WindowsFeature` | Windows Server 用の PowerShell コマンドレット。 | Server 運用 |
| `Repair-Volume` | ファイルシステム（ディスク）の修復を行う。 | ディスク修復 |

## よく連携されるコマンド

### シナリオ例: システムファイルの徹底修復 (運用・保守)

* **目的**: `sfc` で修復できない深刻なエラーを、Windows Update やイメージソースから正常なファイルをダウンロードして修復する。
* **連携コマンド**: `dism`, `sfc`
* **解説**: `dism` でコンポーネントストアを正常化してから、`sfc` でファイルを修復します。
* **コマンド例**:
    ```cmd
    REM コンポーネントストアの破損をチェックし、修復を実行
    dism /Online /Cleanup-Image /RestoreHealth
    REM その後、システムファイルをスキャン
    sfc /scannow
    ```

## スイッチ/オプション説明

**※実行には「管理者権限」が必須です。**

| スイッチ / オプション | 説明 |
| :--- | :--- |
| **イメージ指定** | |
| ⭐ `/Online` | 現在実行中の OS を対象にする。 |
| `/Image:path` | オフラインの Windows イメージのパスを指定する。 |
| **修復・保守** | |
| ⭐ `/Cleanup-Image` | イメージのクリーンアップや修復操作を行う（`/CheckHealth`, `/ScanHealth`, `/RestoreHealth` と併用）。 |
| `/AnalyzeComponentStore` | コンポーネントストアのサイズを分析する。 |
| **機能管理** | |
| ⭐ `/Get-Features` | イメージ内で利用可能なすべての Windows 機能の一覧を表示。 |
| ⭐ `/Enable-Feature` | 指定した機能を有効にする。 |
| `/Disable-Feature` | 指定した機能を無効にする。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: 新しいサーバーで Web サーバー (IIS) 機能をコマンドラインから一括で有効化する。
* **例**:
    ```cmd
    REM IIS 機能を有効化
    dism /Online /Enable-Feature /FeatureName:IIS-WebServerRole
    ```

### 2. ブルーチーム視点 (整合性調査)

* **タスク**: 攻撃者によって OS の機能が改ざん（例: 不審な機能が勝手に有効化）されていないか確認する。
* **例**:
    ```cmd
    REM 有効化されている機能のみを一覧表示
    dism /Online /Get-Features /Format:Table | findstr "Enabled"
    ```

### 3. レッドチーム視点 (足場固めと防御回避)

* **タスク 1: Feature Activation (攻撃用機能の有効化)**
    * **目的**: 標準では無効化されているが、攻撃に役立つ機能（例: Telnet クライアント、WSL、SMB1.0）を強制的に有効化する。
    * **解説**: 管理者権限を奪取後、さらに活動範囲を広げるために OS の能力を拡張します。
    * **コマンド例**:
        ```cmd
        REM 偵察に便利な Telnet クライアントを有効化
        dism /Online /Enable-Feature /FeatureName:TelnetClient
        ```

* **タスク 2: Impair Defenses (防御機能の無効化 - T1562.001)**
    * **目的**: Windows 内蔵のセキュリティ機能（例: Windows Sandbox 等、攻撃に不都合なもの）をコンポーネントレベルで削除・無効化する。
* **タスク 3: Drivers Injection (不審なドライバの追加)**
    * **目的**: `/Add-Driver` を使用して、署名チェックをバイパスするような脆弱なドライバや、永続化のための不正ドライバを流し込む。

## エラーメッセージとトラブルシューティング

1.  **Error: 0x800f081f (The source files could not be found)**
    * **原因**: `/RestoreHealth` 実行中に修復用のソースファイルが見つからない（インターネット接続不可、または Windows Update サーバーとの通信不良）。
    * **解決策**: インストールメディアをマウントし、`/Source:WIM:D:\sources\install.wim:1 /LimitAccess` のようにソースを明示的に指定します。

2.  **Error: 740 (Elevated permissions are required)**
    * **原因**: 管理者権限のあるプロンプトで実行していない。

## セキュリティに関する考慮事項

### 脆弱性と悪用事例

* **機密情報の露出**:
    * 攻撃者はオフラインイメージに対して `dism` を実行し、中に含まれる `unattend.xml` やレジストリハイブをマウント・解析することで、パスワード情報を窃取しようとします。
* **永続化 (Persistence)**:
    * 機能を有効化するだけでなく、独自のドライバをパッケージとして統合することで、システム再起動後も高い権限で動作し続ける仕組みを構築されるリスクがあります。

### LOLBAS (Living Off The Land Binaries and Scripts) における利用例

* **機能**: `AWL Bypass`, `Execute`
* **特性**: Windows の正規管理ツールであるため、ドライバの追加や機能変更のログが「日常的な管理業務」の中に紛れてしまい、検知が遅れることがあります。
* **参照**: [LOLBAS - dism.exe](https://lolbas-project.github.io/lolbas/Binaries/Dism/)

### 対応策・緩和策 (ブルーチーム視点)

* **Detection (検知)**:
    * **イベントID 4688**: `dism.exe` の不審な実行。特に `/Enable-Feature` や `/Add-Driver` の引数を伴うものを監視。
    * **DISM ログの監視**: `C:\Windows\Logs\DISM\dism.log` には詳細な操作履歴が残るため、これを SIEM 等で分析します。
* **Prevention (予防)**:
    * 管理者権限の厳格な管理。`dism` を操作できるユーザーを最小限に絞る。

## 注意点・補足

* **再起動**: 多くの機能変更（`/Enable-Feature` 等）は、反映にシステムの再起動を必要とします。
* **ログ**: 画面に出ない詳細なエラー内容は、常に `C:\Windows\Logs\DISM\dism.log` に出力されます。

---
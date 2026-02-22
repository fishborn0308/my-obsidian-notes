---
# --- YAML Frontmatter ---
created: '2026-02-22'
modified: '2026-02-22'
environment: [OS/Windows]
vulnearability: [Persistence, Privilege_Escalation]
knowledge_category: Command
tags:
  - 'schtasks'
  - cmd
  - 'task_scheduler'
  - 'persistence'
  - 'knowledge_base'
---

# Command - Windows - cmd - schtasks - タスク スケジューラの管理

## 概要

`schtasks` コマンドは、ローカルまたはリモート コンピューター上のスケジュールされたタスクを作成、削除、照会、変更、実行、および終了するために使用されます。
GUI の「タスク スケジューラ」で行うすべての操作をコマンドラインから実行でき、バッチスクリプトによる自動化に最適です。
(出自: Windows標準搭載)

## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 (あれば) |
| :--- | :--- | :--- |
| `at` | 旧式のスケジュールコマンド。Windows 8 以降では非推奨となり、現在は `schtasks` の使用が強く推奨される。 | `schtasks` |
| `taskschd.msc` | タスク スケジューラの GUI 版。視覚的な確認や複雑なトリガー設定に適している。 | GUI環境ならこちら |
| `New-ScheduledTask` | PowerShell 版。オブジェクトとしてタスクを扱えるため、PSスクリプト内での高度な制御が可能。 | `PowerShell` |

## よく連携されるコマンド

### シナリオ例: メンテナンス用バッチの定期実行 (インフラ構築)

* **目的**: 毎日深夜にディスククリーンアップとログ圧縮を行うバッチファイルをスケジュール登録する。
* **連携コマンド**: `cmd.exe`, `powershell.exe`
* **解説**: 作成したバッチファイルを `/tr` (Task Run) で指定し、`/sc` (Schedule) で頻度を指定します。
* **コマンド例**:
    ```cmd
    REM 毎日 03:00 にクリーンアップバッチを実行するタスクを作成
    schtasks /create /tn "DailyCleanup" /tr "C:\Scripts\cleanup.bat" /sc daily /st 03:00
    ```

## スイッチ/オプション説明

`schtasks` は [サブコマンド] [オプション] の形式で実行します。

| サブコマンド | 説明 |
| :--- | :--- |
| ⭐ `/create` | 新しいスケジュール済みタスクを作成する。 |
| ⭐ `/delete` | タスクを削除する。 |
| ⭐ `/query` | すべてのタスクを表示する、または特定のタスクの詳細を確認する。 |
| ⭐ `/run` | スケジュールを待たずに、今すぐタスクを実行する。 |
| `/end` | 実行中のタスクを強制終了する。 |
| `/change` | 既存のタスクの実行プログラムや設定を変更する。 |

| 主要オプション (create時) | 説明 |
| :--- | :--- |
| ⭐ `/tn <TaskName>` | タスクに名前を付ける。 |
| ⭐ `/tr <TaskRun>` | 実行するプログラムのパスを指定する。 |
| ⭐ `/sc <Schedule>` | 頻度を指定（`minute`, `hourly`, `daily`, `weekly`, `onlogon`, `onidle` など）。 |
| ⭐ `/st <StartTime>` | 開始時刻を指定（HH:mm）。 |
| ⭐ `/ru <RunAsUser>` | タスクを実行するユーザー名を指定（`SYSTEM` などを指定可能）。 |
| `/rl <Level>` | 実行レベルを指定。`HIGHEST` を指定すると「最上位の特権」で実行される。 |
| `/f` | 同名のタスクがある場合、警告なしで上書き作成する。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: サーバー起動時の自動サービスチェック
* **解説**: OS 起動時（システム起動時）に特定の診断スクリプトを `SYSTEM` 権限で走らせます。
* **例**:
    ```cmd
    REM 起動時に管理者権限で診断スクリプトを実行
    schtasks /create /tn "BootCheck" /tr "powershell.exe -File C:\Diag.ps1" /sc onstart /ru SYSTEM /rl HIGHEST
    ```

### 2. ブルーチーム視点

* **タスク**: 永続化を目的とした不審なタスクの列挙
* **解説**: ユーザー名が `SYSTEM` なのに実行パスが `Temp` フォルダを指しているような、異常なタスクがないか詳細に調査します。
* **例**:
    ```cmd
    REM 詳細なタスク情報を CSV 形式で出力して分析
    schtasks /query /fo CSV /v > tasks_audit.csv
    ```

### 3. レッドチーム視点 (Persistence / PrivEsc)

* **タスク**: 権限昇格を伴うバックドアの永続化
* **解説**: ログインするたびにリバースシェルが `SYSTEM` 権限で実行されるように設定します。
* **例**:
    ```cmd
    REM ユーザーがログオンするたびに SYSTEM 権限でシェルを実行
    schtasks /create /tn "WindowsUpdateAssist" /tr "C:\Users\Public\rev.exe" /sc onlogon /ru SYSTEM /rl HIGHEST
    ```

## エラーメッセージとトラブルシューティング

### よくあるエラーメッセージ

1.  **エラーメッセージ例 1**: `エラー: アクセスが拒否されました。`
    * **考えられる原因**: 管理者権限のないプロンプトから実行している、または `/ru SYSTEM` を指定する権限がない。
    * **解決策**: 「管理者として実行」したプロンプトを使用する。

2.  **エラーメッセージ例 2**: `エラー: タスク名 "..." は既に存在します。`
    * **考えられる原因**: 同じ名前のタスクが既に登録されている。
    * **解決策**: `/f` オプションを付けて強制上書きするか、先に `/delete` で削除する。

## 環境変数と設定ファイル

タスクの実体は XML ファイルとして保存されていますが、レジストリでも管理されています。
* **ファイル保存場所**: `C:\Windows\System32\Tasks`
* **レジストリパス**: `HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Schedule\TaskCache\Tree`

## セキュリティに関する考慮事項

### 脆弱性と悪用事例

* **脆弱性**: **Insecure Task Permissions**
    - タスクが実行する実行ファイル（バイナリ）のアクセス権が緩く、一般ユーザーが書き換え可能な場合、そのファイルを置き換えることでタスク実行時に権限昇格が可能になる。
* **悪用シナリオ**: 
    - 攻撃者が `schtasks` を使い、一見正規のサービス名に見えるタスクを登録し、深夜に C2 サーバへ通信させる。

### LOLBAS (Living Off The Land Binaries and Scripts) における利用例

* **機能**: `Persistence`, `Execute`
* **解説**: 攻撃の初期潜入後、再起動しても権限を維持するための標準的な手法として利用される。
* **参照**: [LOLBAS - schtasks.exe](https://lolbas-project.github.io/lolbas/Binaries/Schtasks/)

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: 
    - スケジュールされたタスクが参照するバイナリおよびスクリプトの ACL を厳格化し、一般ユーザーの書き込みを禁止する。
* **Detection (検知)**: 
    - **イベントID 4698**: 新しいタスクが作成された。
    - **イベントID 4699**: タスクが削除された。
    - **イベントID 4702**: タスクが更新された。
    - 特に `/ru SYSTEM` や `/rl HIGHEST` を含むタスク作成をリアルタイムで監視する。

## 注意点・補足

* **パスの引用符**: `/tr` で指定するパスにスペースが含まれる場合は、`\"C:\Program Files\App.exe\"` のようにエスケープされた引用符を使用する必要があります。
* **対話型実行**: デフォルトではバックグラウンドで実行されるため、GUI アプリを起動させる場合はセッションの考慮が必要です。

---
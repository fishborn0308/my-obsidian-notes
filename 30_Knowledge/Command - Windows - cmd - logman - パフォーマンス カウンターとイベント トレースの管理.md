---
# --- YAML Frontmatter ---
created: 2026-02-22 14:43
modified: 2026-02-22 15:13
environment: [OS/Windows]
vulnearability: []
knowledge_category: Command
tags:
  - logman
  - cmd
  - performance
  - etw
  - enumeration
  - knowledge_base
---

# Command - Windows - cmd - logman - パフォーマンス カウンターとイベント トレースの管理

## 概要

`logman` は、パフォーマンス カウンターおよびイベント トレース ログ (ETW) の作成、開始、停止、および照会を行うためのコマンドライン ツールです。

GUI の「パフォーマンス モニター」で行う操作をスクリプト化でき、サーバーのパフォーマンス診断やセキュリティ監査のためのトレース収集に利用されます。

(出自: Windows標準搭載)

## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 (あれば) |
| :--- | :--- | :--- |
| `perfmon` | `logman` の GUI 版。視覚的な設定やリアルタイム表示に適している。 | `perfmon` (GUI) |
| `typeperf` | パフォーマンス カウンターの値をコマンドプロンプトに直接出力する。 | `typeperf` (簡易表示) |
| `Get-Counter` | PowerShell 版。オブジェクト形式でカウンターデータを取得・処理できる。 | `PowerShell` |

## よく連携されるコマンド

### シナリオ例: 特定のプロバイダーからのイベントトレース収集 (ブルーチーム)

* **目的**: 攻撃者が利用する可能性のある特定の機能（例: Microsoft-Windows-Kernel-Process）の動作を詳細に記録する。
* **連携コマンド**: `wevtutil`, `findstr`
* **解説**: `logman query providers` で利用可能なプロバイダーを探し、`logman create trace` で収集を開始します。
* **コマンド例**:
    ```cmd
    REM プロセスの作成・終了を追跡するトレースセッションを作成
    logman create trace ProcessTrace -p "Microsoft-Windows-Kernel-Process" 0x10 -o C:\Logs\process.etl
    logman start ProcessTrace
    ```

## スイッチ/オプション説明

`logman` は [動詞] [セッション名] [オプション] の形式で実行します。

| 動詞 (Verbs) | 説明 |
| :--- | :--- |
| `start` / `stop` | 指定したデータ コレクター セッションを開始または停止する。 |
| `create` | 新しいデータ コレクター（カウンターまたはトレース）を作成する。 |
| `query` | データ コレクターのプロパティまたは実行中のセッションを照会する。 |
| `update` | 既存のデータ コレクターの設定を更新する。 |
| `delete` | 指定したデータ コレクターを削除する。 |

| 主要オプション | 説明 |
| :--- | :--- |
| ⭐ `-p <provider>` | トレース セッションに使用する ETW プロバイダーを指定する。 |
| `-c <path>` | 収集するパフォーマンス カウンターのパスを指定する。 |
| `-o <path>` | 出力されるログファイル (.etl または .blg) のパスを指定する。 |
| `-f <format>` | ログの形式を指定（`bin`, `csv`, `tsv`）。 |
| `-u <user> <pwd>` | リモート接続時などに使用する実行ユーザーを指定。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: サーバー負荷時のパフォーマンスカウンター自動収集
* **解説**: 閾値を超えた際などにバッチから起動し、CPU やメモリの使用状況を 1 秒間隔で記録します。
* **例**:
    ```cmd
    REM 1秒間隔でプロセッサ使用率を記録するセッションを作成
    logman create counter CPU_Load -c "\Processor(_Total)\% Processor Time" -si 1 -o C:\PerfLogs\cpu.blg
    logman start CPU_Load
    ```

### 2. ブルーチーム視点

* **タスク**: セキュリティ製品をバイパスする挙動の動的解析
* **解説**: `Event Tracing for Windows (ETW)` は強力な可視化手段です。攻撃者が `Sysmon` などを停止させても、`logman` でカーネルレベルのトレースを直接貼ることで、プロセスの隠蔽などを検知できる場合があります。
* **例**:
    ```cmd
    REM ネットワーク関連のカーネルイベントをトレース
    logman create trace NetTrace -p "Microsoft-Windows-Kernel-Network" -o C:\Logs\net.etl
    logman start NetTrace
    ```

### 3. レッドチーム視点

* **タスク**: 実行環境における監視（ETW）の列挙
* **解説**: 現在どのようなトレースが実行されているかを確認し、自分の攻撃がどのプロバイダーによって検知される可能性があるかを推測します（Anti-Analysis）。
* **例**:
    ```cmd
    REM 現在実行中のすべてのトレースセッションを表示
    logman query -ets
    ```

## エラーメッセージとトラブルシューティング

### よくあるエラーメッセージ

1.  **エラーメッセージ例 1**: `アクセスが拒否されました。`
    * **考えられる原因**: セッションの作成や開始には管理者権限が必要です。
    * **解決策**: 「管理者として実行」したプロンプトで再試行。

2.  **エラーメッセージ例 2**: `データ コレクター セッション "..." は既に実行されています。`
    * **考えられる原因**: 同じ名前のセッションが既に開始されている。
    * **解決策**: `logman stop <name>` で一度停止させるか、別のセッション名を使用する。

## 環境変数と設定ファイル

`logman` 自体は環境変数を持ちませんが、出力される `.etl` ファイルは非常に大容量になる可能性があるため、ディスク容量（`%SystemDrive%`）の監視が重要です。

## セキュリティに関する考慮事項

### 脆弱性と悪用事例

* **悪用シナリオ**:
    - **Evasion (ETW Blinding)**: 攻撃者が管理者権限を得た後、`logman stop` を使ってセキュリティ製品（EDR等）が依存しているトレースセッションを停止させ、活動を不可視にする。
    - **Information Disclosure**: ETW を通じて、他のプロセスがやり取りしている機密情報（暗号化前のデータや API 引数）を傍受される可能性。

### LOLBAS (Living Off The Land Binaries and Scripts) における利用例

* **機能**: `Information Requirements`
* **参照**: [LOLBAS - logman.exe](https://lolbas-project.github.io/lolbas/Binaries/Logman/)

### 対応策・緩和策 (ブルーチーム視点)

* **Detection (検知)**:
    - **イベントID 4688**: `logman` の実行ログ、特に `stop` や `delete` サブコマンドの使用を監視。
    - 重要なセキュリティトレース（例: `DefenderApiLogger` や `EtwRT...`）が予期せず停止した際のアラート設定。

## 注意点・補足

* **`-ets` スイッチ**: `query` や `stop` 時に `-ets` を付けると、標準のデータコレクターセットではなく、OS が直接管理している「イベント トレース セッション」を対象にします。
* **ログの解析**: 出力された `.etl` ファイルは、`eventvwr` (イベントビューアー) で開くか、`netsh trace convert` 等でテキスト化して解析します。

---
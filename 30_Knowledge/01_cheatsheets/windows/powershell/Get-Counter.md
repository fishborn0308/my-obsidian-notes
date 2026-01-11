---
tags:
  - 'Get-Counter'
  - 'powershell'
  - 'cheatsheet'
title: 'Get-Counter - パフォーマンスカウンターのデータを取得する'
summary: 'Windowsのパフォーマンスカウンターから、CPU使用率、メモリ、ディスクI/Oなど、OSやアプリケーションのパフォーマンスデータをリアルタイムまたはログから取得します。'
related:
  - 'perfmon'
  - 'typeperf (CMD)'
  - 'Get-Process'
---

# `Get-Counter` - パフォーマンスカウンターのデータを取得する

## 概要

`Get-Counter` コマンドレットは、Windowsのパフォーマンスカウンターからパフォーマンスデータを取得します。このコマンドレットを使うことで、プロセッサの負荷、メモリ使用量、ディスクやネットワークのアクティビティなど、OSや特定のアプリケーションに関する詳細なパフォーマンスメトリクスを収集できます。

取得したデータは、リアルタイムでのパフォーマンス監視、トラブルシューティング、あるいは長期間のデータ分析のために利用されます。

## 類似コマンドレット/コマンドとの差異

| 類似コマンドレット/コマンド | 差異 / 使い分けのポイント |
| :--- | :--- |
| **`perfmon`** | `perfmon` は、パフォーマンスデータを収集・表示するための**GUI**ツール（パフォーマンスモニター）です。`Get-Counter` はその機能を**CUI**で提供し、スクリプトによるデータの取得と自動化を可能にします。 |
| **`typeperf` (CMD)** | CMDの `typeperf` もパフォーマンスカウンターのデータをコマンドラインに表示したり、ファイルに書き出したりするコマンドです。`Get-Counter` は結果を**オブジェクト**として返すため、PowerShellのパイプラインで後続処理（フィルタリング、エクスポートなど）を行うのが遥かに容易です。 |

## よく連携されるコマンドレット

### シナリオ例: 特定のプロセスのCPU使用率を継続的に監視する (インフラ構築・運用視点)

* **目的**: CPUを異常に消費している疑いのあるプロセス (`BadApp.exe`) のCPU使用率を5秒ごとに10回サンプリングし、結果をCSVにエクスポートする。
* **連携コマンドレット**: `Export-Csv`
* **解説**: `-Counter` パラメータで、特定のプロセスインスタンスの `% Processor Time` カウンターを指定します。`-SampleInterval` と `-MaxSamples` でサンプリングの間隔と回数を制御し、収集したデータを `Export-Csv` でファイルに保存して後から分析できるようにします。
* **コマンド例**:

    ```powershell
    # 'BadApp' プロセスのCPU使用率を5秒ごとに10回取得
    Get-Counter -Counter "\Process(BadApp)\% Processor Time" -SampleInterval 5 -MaxSamples 10 |
    # 読みやすい形式に整形してCSVにエクスポート
    Select-Object -ExpandProperty CounterSamples |
    Select-Object -Property Path, CookedValue, Timestamp |
    Export-Csv -Path C:\Logs\BadApp_CPU_Usage.csv -NoTypeInformation
    ```

## パラメータ説明

| パラメータ | 型 | 説明 |
| :--- | :--- | :--- |
| ⭐ **`-Counter`** | `String[]` | 取得するパフォーマンスカウンターのパスを指定します。`\Category(Instance)\Counter` の形式です。（例: `\Processor(_Total)\% Processor Time`） |
| ⭐ **`-SampleInterval`** | `Int32` | データをサンプリングする間隔を秒単位で指定します。 |
| ⭐ **`-MaxSamples`** | `Int64` | 取得するサンプルの最大数を指定します。このパラメータを指定すると、指定回数サンプリングした後にコマンドが終了します。 |
| **`-ComputerName`** | `String[]` | データを取得するリモートコンピュータを指定します。 |
| **`-Continuous`** | `SwitchParameter` | `Ctrl+C` で中断されるまで、`-SampleInterval` ごとに継続的にデータを取得します。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: サーバーの全体的なパフォーマンス（CPU、メモリ、ディスク）を素早く確認する。
* **組み合わせ**: `Get-Counter -Counter <CounterList>`
* **解説**: 複数の主要なカウンターを配列として一度に指定することで、システムの健全性を概観できます。
* **例**:

    ```powershell
    # CPU使用率、空きメモリ、ディスクアイドル時間の3つのカウンターを一度に取得
    $counters = @(
        "\Processor(_Total)\% Processor Time",
        "\Memory\Available MBytes",
        "\PhysicalDisk(_Total)\% Idle Time"
    )
    Get-Counter -Counter $counters
    ```

### 2. ブルーチーム視点

* **タスク**: **脅威ハンティング**。不審なプロセスのリソース消費パターンを分析する。
* **組み合わせ**: `Get-Counter -Counter "\Process(SuspiciousApp)\..." -Continuous`
* **解説**: マルウェアの疑いがあるプロセスが、異常なネットワーク活動（`IO Data Bytes/sec`）やディスク活動（`IO Read Bytes/sec`）を行っていないかを、`-Continuous` スイッチを使ってリアルタイムで監視します。
* **例**:

    ```powershell
    # 'evil.exe' プロセスのネットワーク送受信バイト数をリアルタイムで監視
    Get-Counter -Counter "\Process(evil)\IO Data Bytes/sec" -Continuous
    ```

### 3. レッドチーム視点

* **タスク**: **偵察 (Reconnaissance)**。侵入したサーバーの役割と活動レベルを推測する。
* **組み合わせ**: `Get-Counter`
* **解説**: 攻撃者は侵入後、`Get-Counter` を使って特定のパフォーマンスカウンターを確認します。例えば、`\SQLServer:*` 関連のカウンターの値が高ければ、それが重要なデータベースサーバーであると判断できます。`\Web Service(*)\*` のカウンターはWebサーバーの活動レベルを示します。これにより、情報価値の高いターゲットを特定します。
* **例**:

    ```powershell
    # SQL Server関連の利用可能なカウンターセットをリストアップして偵察
    Get-Counter -ListSet "SQLServer:*"
    ```

## エラーメッセージとトラブルシューティング

### よくあるエラーメッセージ

1. **エラーメッセージ**: `Get-Counter: The specified counter path could not be interpreted or the counter does not exist.`
    * **考えられる原因**: 指定したカウンターのパスの構文が間違っているか、そのカウンターが存在しません（例: 該当するプロセスが実行されていない）。
    * **解決策**: `Get-Counter -ListSet *` ですべてのカウンターセットをリストアップし、正しいカウンターパスを確認してください。インスタンス名（`(process_name)`など）が正しいかも確認します。

## セキュリティに関する考慮事項

### 悪用事例

* **偵察 (Reconnaissance)**: レッドチームのシナリオで述べた通り、`Get-Counter` は攻撃者がシステムの役割、実行中の主要なアプリケーション、そしてその活動レベルを把握するための強力な偵察ツールです。

### LOLBASにおける利用例

* **機能**: **偵察 (Reconnaissance)**
* **解説**: `Get-Counter` は PowerShell の標準機能であり、攻撃者がシステムの内部構成を理解するために利用する環境寄生型の手法です。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: -
* **Detection (検知)**: PowerShellのスクリプトブロックロギング (イベントID 4104) を有効化し、`Get-Counter` の実行、特にリモートコンピュータ (`-ComputerName`) に対する実行や、偵察目的で使われやすいカウンターの照会を監視する。

## 注意点・補足

* **カウンターパスの確認**: 利用可能なカウンターのパスは非常に多岐にわたります。`perfmon.exe` を起動し、GUIでカウンターを追加する画面を使うと、利用可能なカウンターをツリー形式で簡単に探すことができます。

---
[PowerShellインデックスに戻る](../../powershell_index.md)

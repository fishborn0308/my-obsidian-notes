---
tags:
  - 'Get-Process'
  - 'powershell'
  - 'cheatsheet'
title: 'Get-Process - 実行中のプロセスを取得する'
summary: 'ローカルまたはリモートのコンピュータで実行されているプロセスのリストを取得します。'
related:
  - 'Stop-Process'
  - 'Get-Service'
  - 'Get-CimInstance'
  - 'tasklist'
  - 'ps'
---

# `Get-Process` - 実行中のプロセスを取得する

## 概要

`Get-Process` コマンドレットは、ローカルまたはリモートのコンピュータで実行されているプロセスを表すオブジェクトを取得します。このコマンドレットは、CMDの `tasklist` やLinuxの `ps` に相当する基本的なコマンドで、各プロセスの名前、ID (PID)、パフォーマンス情報（CPU、メモリ使用量など）を返します。

取得したプロセスオブジェクトはパイプラインを通じて `Stop-Process` に渡してプロセスを終了させたり、`Sort-Object` や `Where-Object` でフィルタリングしたりと、PowerShellの強力なオブジェクト操作の起点となります。

このコマンドレットには、`gps`, `ps` という便利なエイリアスがデフォルトで設定されています。

## 類似コマンドレット/コマンドとの差異

| 類似コマンドレット/コマンド | 差異 / 使い分けのポイント |
| :--- | :--- |
| **`tasklist` (CMD)** | `tasklist` はプロセスの情報を**テキスト**として表示します。`Get-Process` は各プロセスを**オブジェクト**として返すため、プロパティ（`.CPU`, `.WS`など）に基づいたソートやフィルタリングが非常に強力です。 |
| **`Get-CimInstance Win32_Process`** | `Get-CimInstance Win32_Process` は、プロセスの**コマンドライン引数 (`CommandLine`)** や**親プロセスID (`ParentProcessId`)** など、より詳細なWMI情報を取得できます。`Get-Process` は、CPUやメモリなどのパフォーマンスデータを素早く取得するのに適しています。 |

## よく連携されるコマンドレット

### シナリオ例: CPU使用率が高い上位5つのプロセスを特定し、終了する (インフラ構築・運用視点)

* **目的**: システムのパフォーマンスを低下させている原因となっているプロセスを特定し、強制的に終了させる。
* **連携コマンドレット**: `Sort-Object`, `Select-Object`, `Stop-Process`
* **解説**: `Get-Process` で全てのプロセスを取得し、`Sort-Object` でCPU使用率 (`CPU` プロパティ) の降順に並べ替えます。`Select-Object -First 5` で上位5つを抽出し、最後に `Stop-Process` に渡して終了させます。`-WhatIf` を付けることで、実際に終了する前にどのプロセスが対象になるかを確認できます。
* **コマンド例**:

    ```powershell
    # CPU使用率でソートし、上位5つのプロセスを終了（-WhatIfで事前確認）
    Get-Process | Sort-Object -Property CPU -Descending | Select-Object -First 5 | Stop-Process -Force -WhatIf
    ```

## パラメータ説明

| パラメータ | 型 | 説明 |
| :--- | :--- | :--- |
| ⭐ **`-Name`** | `String[]` | 取得するプロセスの名前を指定します。ワイルドカード (`*`) が使用できます。 |
| ⭐ **`-Id`** | `Int32[]` | 取得するプロセスのID (PID) を指定します。 |
| **`-ComputerName`** | `String[]` | プロセスを取得するリモートコンピュータを指定します。 |
| **`-Module`** | `SwitchParameter` | プロセスが読み込んでいるDLLモジュールをリストアップします。 |
| ⭐ **`-IncludeUserName`** | `SwitchParameter` | (`管理者権限要`) 各プロセスを実行しているユーザー名 (`UserName`プロパティ) を表示します。 |
| **`-FileVersionInfo`** | `SwitchParameter` | プロセスの実行可能ファイルのバージョン情報を取得します。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: メモリ使用量（ワーキングセット）が多いプロセスをリストアップする。
* **組み合わせ**: `Get-Process | Sort-Object -Property WS -Descending`
* **解説**: `WS` (WorkingSet) プロパティでソートすることで、どのプロセスが物理メモリを最も多く消費しているかを簡単に特定できます。
* **例**:

    ```powershell
    # メモリ使用量が多い上位10プロセスを表示
    Get-Process | Sort-Object -Property WS -Descending | Select-Object -First 10 -Property Name, Id, @{Name="Memory(MB)"; Expression={$_.WS / 1MB -as [int]}}
    ```

### 2. ブルーチーム視点

* **タスク**: **ライブレスポンス**。不審なプロセスを、実行ユーザーと実行ファイルのパスと共に特定する。
* **組み合わせ**: `Get-Process -IncludeUserName | Select-Object Name, Id, UserName, Path`
* **解説**: **インシデント対応の基本**。`-IncludeUserName` でどのユーザーアカウント（`SYSTEM`, `Network Service`, 特定のユーザー名など）でプロセスが実行されているかを確認します。また、`Path` プロパティは、`svchost.exe` のような正規のプロセス名に偽装したマルウェアが、不審な場所（例: `C:\Users\Public`）から実行されていないかを特定するのに役立ちます。
* **例**:

    ```powershell
    # 全プロセスの名前, ID, ユーザー名, パスをリストアップ
    Get-Process -IncludeUserName | Select-Object -Property Name, Id, UserName, Path
    ```

### 3. レッドチーム視点

* **タスク**: **偵察 (Reconnaissance)**。侵入したホストで実行されているセキュリティ製品を特定する。
* **組み合わせ**: `Get-Process`
* **解説**: 攻撃者は侵入後、`Get-Process` (またはエイリアスの `ps`) を実行し、アンチウイルスやEDR製品のプロセス名 (`MsMpEng.exe`, `cb.exe`, `CylanceSvc.exe` など）を探します。これにより、ターゲットの防御機構を把握し、検知を回避するための手法を選択します。
* **例**:

    ```powershell
    # 実行中のプロセスを偵察
    Get-Process

    # 特定のセキュリティ製品のプロセスを探す
    Get-Process -Name "*defender*", "*sysmon*" -ErrorAction SilentlyContinue
    ```

## エラーメッセージとトラブルシューティング

### よくあるエラーメッセージ

1. **エラーメッセージ**: `Get-Process: Cannot find a process with the name "..."`
    * **考えられる原因**: 指定した `-Name` のプロセスが実行されていません。
    * **解決策**: プロセス名のスペルが正しいか確認してください。ワイルドカード (`*`) を使って部分一致で検索するのも有効です (例: `Get-Process -Name "*note*"`）。

## セキュリティに関する考慮事項

### 悪用事例

* **偵察 (Reconnaissance)**: レッドチームのシナリオで述べた通り、`Get-Process` は攻撃者がシステムの稼働状況、実行中のアプリケーション、そして最も重要な**セキュリティ対策ソフト**を把握するための基本的な偵察ツールです。

### LOLBASにおける利用例

* **機能**: **偵察 (Reconnaissance)**
* **解説**: `Get-Process` は PowerShell の標準機能であり、`tasklist.exe` と同様に、攻撃者が環境に溶け込みながらシステム情報を収集するために利用されます（Living off the Land）。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: -
* **Detection (検知)**: PowerShellのスクリプトブロックロギング (イベントID 4104) を有効化し、`Get-Process` の実行を記録・監視する。特に、他の偵察コマンド (`Get-NetTCPConnection`, `Get-CimInstance`など) と連続して実行される場合は、攻撃の兆候である可能性があります。

## 注意点・補足

* **管理者権限**: `-IncludeUserName` や、他のユーザーが実行しているプロセスの `Path` プロパティなどを完全に取得するには、PowerShellを「管理者として実行」する必要があります。

---
[PowerShellインデックスに戻る](../../powershell_index.md)

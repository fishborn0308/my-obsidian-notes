---
tags:
  - 'Get-CimInstance'
  - 'Win32_Process'
  - 'powershell'
  - 'cheatsheet'
  - 'wmi'
title: 'Get-CimInstance Win32_Process - プロセスの詳細情報を取得する'
summary: 'WMI/CIMを通じて、実行中のプロセスに関する詳細情報（コマンドライン、親プロセスID、所有者など）を取得します。'
related:
  - 'Get-Process'
  - 'Get-CimInstance'
  - 'tasklist'
  - 'wmic'
---

# `Get-CimInstance Win32_Process` - プロセスの詳細情報を取得する

## 概要

`Get-CimInstance -ClassName Win32_Process` は、システム上で実行されている全てのプロセスに関する詳細な管理情報を取得するためのコマンドです。標準の `Get-Process` コマンドレットでは簡単に取得できない、**`CommandLine`** (プロセスの起動に使われたコマンドライン引数)、**`ParentProcessId`** (このプロセスを起動した親プロセスのID)、実行ファイルのパス (`ExecutablePath`) などを取得できるため、システムのトラブルシューティング、インシデント対応、フォレンジック調査において極めて重要な役割を果たします。

## 類似コマンドレット/コマンドとの差異

| 類似コマンドレット/コマンド | 差異 / 使い分けのポイント |
| :--- | :--- |
| **`Get-Process`** | `Get-Process` はプロセスのパフォーマンス情報（CPU、メモリ使用量など）を素早く取得するのに適しています。`Get-CimInstance Win32_Process` は、プロセスの**起動経緯や詳細な構成情報**（コマンドライン、所有者など）を取得するのに優れています。 |
| **`tasklist /v`** | `tasklist` も詳細情報を表示できますが、出力はテキストです。`Get-CimInstance` は**オブジェクト**を返すため、結果のフィルタリング、ソート、さらなる加工が容易です。 |

## `Win32_Process` の主要なプロパティ

`Get-CimInstance -ClassName Win32_Process` で取得できるオブジェクトには多数のプロパティがありますが、特に以下のものはセキュリティ分析やトラブルシューティングで頻繁に使用されます。

| プロパティ名 | 説明 |
| :--- | :--- |
| ⭐ **`ProcessId`** | プロセスID (PID)。システム内でプロセスを一意に識別します。 |
| ⭐ **`ParentProcessId`** | このプロセスを起動した**親プロセス**のID (PPID)。プロセスの親子関係を追跡し、攻撃の連鎖を解明する上で不可欠です。 |
| ⭐ **`Name`** | プロセスのイメージ名 (例: `powershell.exe`)。 |
| ⭐ **`CommandLine`** | プロセスが起動された際の**完全なコマンドライン引数**。マルウェアの挙動を特定するための最も重要な情報の一つです。 |
| **`ExecutablePath`** | プロセスの実行可能ファイルのフルパス。偽装されたプロセスを発見するのに役立ちます。 |
| **`CreationDate`** | プロセスが作成された日時。 |
| **`SessionId`** | プロセスが実行されているセッションのID。 |

## よく連携されるコマンドレット

### シナリオ例: 特定の親プロセスから起動された全ての子プロセスを特定する (ブルーチーム視点)

* **目的**: 不審な `powershell.exe` (例: PID 1234) が、他にどのような子プロセスを起動したかを調査する。
* **連携コマンドレット**: `Where-Object`
* **解説**: `Get-CimInstance` ですべてのプロセスを取得し、その結果を `Where-Object` に渡します。`ParentProcessId` が調査対象のPIDと一致するプロセスのみをフィルタリングします。
* **コマンド例**:

    ```powershell
    # 親プロセスIDが 1234 である全ての子プロセスを検索
    Get-CimInstance -ClassName Win32_Process | Where-Object { $_.ParentProcessId -eq 1234 }
    ```

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: 特定のアプリケーション (`myapp.exe`) が、期待通りの設定ファイル (`--config "C:\App\prod.conf"`) を読み込んで起動しているかを確認する。
* **組み合わせ**: `Get-CimInstance -ClassName Win32_Process -Filter "..."`
* **解説**: `-Filter` パラメータで `Name` を指定して効率的に対象プロセスを絞り込み、その `CommandLine` プロパティを確認します。
* **例**:

    ```powershell
    # 'myapp.exe' プロセスのコマンドライン引数を表示
    Get-CimInstance -ClassName Win32_Process -Filter "Name = 'myapp.exe'" | Select-Object -ExpandProperty CommandLine
    ```

### 2. ブルーチーム視点

* **タスク**: **脅威ハンティング**。`svchost.exe` や `rundll32.exe` のような正規のプロセス名を使っているが、その実体（`ExecutablePath`）が `C:\Windows\System32` 以外の場所にある不審なプロセスを発見する。
* **組み合わせ**: `Where-Object`
* **解説**: 全てのプロセスを取得し、プロセス名が正規のものでありながら、実行パスが予期せぬ場所であるものをフィルタリングします。これはマルウェアがよく使う偽装の手口です。
* **例**:

    ```powershell
    # 'svchost.exe' という名前だが、System32以外から実行されているものを検索
    Get-CimInstance -ClassName Win32_Process -Filter "Name = 'svchost.exe'" | Where-Object { $_.ExecutablePath -notlike "$env:SystemRoot\System32\*" }
    ```

### 3. レッドチーム視点

* **タスク**: **偵察 (Reconnaissance)**。`wmic` が非推奨または監視されている環境で、プロセスの詳細情報を収集して、セキュリティソフトや他の管理者の活動を把握する。
* **組み合わせ**: `Get-CimInstance`
* **解説**: 攻撃者は侵入後、`Get-CimInstance -ClassName Win32_Process` を実行して、アンチウイルス製品のプロセス (`MsMpEng.exe` など) や、他の管理者が実行しているコマンド (`powershell.exe -File ...` など) の `CommandLine` を調査し、システムの防御機構や運用状況を把握します。
* **例**:

    ```powershell
    # 全プロセスの詳細情報を取得して偵察
    Get-CimInstance -ClassName Win32_Process | Select-Object ProcessId, ParentProcessId, Name, CommandLine
    ```

## セキュリティに関する考慮事項

### 悪用事例

* **詳細な偵察**: `Get-CimInstance Win32_Process` は、攻撃者がシステムの内部で何が起こっているかを詳細に把握するための最も強力な偵察ツールの1つです。特に `CommandLine` プロパティは、実行中のスクリプト、接続先、設定ファイルパスなど、機密情報への多くの手がかりを含んでいます。

### LOLBASにおける利用例

* **機能**: **偵察 (Reconnaissance)**
* **解説**: `Get-CimInstance` は PowerShell の標準機能であり、`wmic.exe` と同様に、攻撃者が環境に溶け込みながらシステム情報を収集するために多用されます（Living off the Land）。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: -
* **Detection (検知)**: **PowerShellスクリプトブロックロギング (イベントID 4104) を有効化することが極めて重要です**。これにより、`Get-CimInstance` の実行と、それによって取得された情報がどのように利用されたかを追跡できます。また、**コマンドライン引数のロギング (イベントID 4688)** を有効にすることで、どのプロセスがどの引数で起動されたかを記録し、`ParentProcessId` と組み合わせて不審なプロセスの連鎖を追跡できます。

## 注意点・補足

* **パフォーマンス**: `Get-CimInstance -ClassName Win32_Process` は詳細な情報を取得するため、`Get-Process` よりも若干実行に時間がかかることがあります。

---
[PowerShellインデックスに戻る](../../powershell_index.md)

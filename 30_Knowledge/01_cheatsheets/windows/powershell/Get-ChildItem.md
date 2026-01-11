---
tags:
  - 'Get-ChildItem'
  - 'powershell'
  - 'cheatsheet'
title: 'Get-ChildItem - アイテムの子アイテムを取得する'
summary: 'ファイルシステム上のファイルやディレクトリ、レジストリキーなど、指定した場所に含まれるアイテムを取得します。'
related:
  - 'Get-Item'
  - 'Remove-Item'
  - 'Copy-Item'
  - 'dir'
---

# `Get-ChildItem` - アイテムの子アイテムを取得する

## 概要

`Get-ChildItem` コマンドレットは、1つまたは複数の指定した場所にあるアイテム（子アイテム）を取得します。アイテムがコンテナーである場合（ファイルシステムのディレクトリやレジストリキーなど）、その中のアイテムを取得します。CMDの `dir` コマンドやLinuxの `ls` コマンドに相当する、PowerShellで最も基本的なコマンドレットの1つです。

このコマンドレットは、ファイルやディレクトリをオブジェクトとして返すため、その結果をパイプラインで他のコマンドレットに渡し、フィルタリング、ソート、コピー、削除などの後続処理を行うことができます。

このコマンドレットには、`gci`, `ls`, `dir` という便利なエイリアスがデフォルトで設定されています。

## 類似コマンドレット/コマンドとの差異

| 類似コマンドレット/コマンド | 差異 / 使い分けのポイント |
| :--- | :--- |
| **`dir` / `ls` (CMD/Linux)** | `dir` や `ls` はファイルやディレクトリの情報を**テキスト**として表示します。`Get-ChildItem` はそれらを**オブジェクト**として返すため、`.Name` や `.Length` などのプロパティにアクセスしたり、`Where-Object` で絞り込んだりといった、プログラム的な操作が可能です。 |

## よく連携されるコマンドレット

### シナリオ例: 古いログファイルを検索して削除する (インフラ構築・運用視点)

* **目的**: ログディレクトリ内にある、最終更新日時が30日以上前の `.log` ファイルを全て検索し、削除する。
* **連携コマンドレット**: `Where-Object`, `Remove-Item`
* **解説**: `Get-ChildItem` で全てのログファイルオブジェクトを取得し、パイプラインで `Where-Object` に渡して日付の条件でフィルタリングします。最後に、条件に一致したファイルオブジェクトを `Remove-Item` に渡して削除します。
* **コマンド例**:

    ```powershell
    # C:\Logs 以下の .log ファイルを検索し、30日以上前のものを削除（-WhatIfで事前確認）
    Get-ChildItem -Path "C:\Logs" -Filter "*.log" -Recurse | Where-Object { $_.LastWriteTime -lt (Get-Date).AddDays(-30) } | Remove-Item -WhatIf
    ```

## パラメータ説明

| パラメータ | 型 | 説明 |
| :--- | :--- | :--- |
| ⭐ **`-Path`** | `String[]` | アイテムを取得するパスを指定します。ワイルドカードが使用できます。 |
| ⭐ **`-Recurse`** | `SwitchParameter` | 指定したパスのサブディレクトリを再帰的に検索します。 |
| **`-Filter`** | `String` | プロバイダーがサポートする形式でアイテムをフィルタリングします。一般的に `-Include` よりも高速です。（例: `"*.txt"`) |
| **`-Include`** | `String[]` | 取得対象に含めるアイテムを指定します。`-Recurse` と共に使われることが多いです。（例: `*.log`, `*.tmp`） |
| **`-Exclude`** | `String[]` | 取得対象から除外するアイテムを指定します。 |
| ⭐ **`-Force`** | `SwitchParameter` | 隠しファイルやシステムファイルなど、通常は表示されないアイテムも取得します。 |
| ⭐ **`-File`** | `SwitchParameter` | ファイルのみを取得します。 |
| ⭐ **`-Directory`** | `SwitchParameter` | ディレクトリのみを取得します。 |
| **`-Depth`** | `UInt32` | `-Recurse` と共に使用し、再帰の深さを指定します。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: ディスク容量を圧迫している、1GB以上の巨大なファイルを探し出す。
* **組み合わせ**: `Get-ChildItem -Recurse | Where-Object { $_.Length -gt 1GB }`
* **解説**: `-Recurse` でディスク全体をスキャンし、`Where-Object` で `Length` プロパティ（ファイルサイズ）が1GBより大きいファイルオブジェクトのみをフィルタリングします。
* **例**:

    ```powershell
    # Cドライブ全体を検索し、1GBより大きいファイルをサイズ順に表示
    Get-ChildItem -Path "C:\" -Recurse -File -ErrorAction SilentlyContinue | Where-Object { $_.Length -gt 1GB } | Sort-Object -Property Length -Descending | Select-Object -First 20
    ```

### 2. ブルーチーム視点

* **タスク**: **ライブレスポンス**。直近24時間以内に作成または変更された実行可能ファイル (`.exe`, `.dll`) を調査する。
* **組み合わせ**: `Get-ChildItem -Recurse -Include <patterns> | Where-Object { $_.LastWriteTime -gt (Get-Date).AddDays(-1) }`
* **解説**: マルウェアがシステムに配置された可能性がある場合、このコマンドで最近変更された実行可能ファイルをリストアップし、不審なファイルがないかを確認します。
* **例**:

    ```powershell
    # C:\Users 配下で、直近1日以内に変更された .exe, .dll, .ps1 ファイルを検索
    $yesterday = (Get-Date).AddDays(-1)
    Get-ChildItem -Path "C:\Users" -Recurse -Include "*.exe", "*.dll", "*.ps1" -ErrorAction SilentlyContinue | Where-Object { $_.LastWriteTime -gt $yesterday }
    ```

### 3. レッドチーム視点

* **タスク**: **偵察 (Reconnaissance)**。機密情報が含まれていそうな設定ファイルやユーザーデータを探す。
* **組み合わせ**: `Get-ChildItem -Recurse -Filter <pattern>`
* **解説**: 攻撃者は侵入後、`Get-ChildItem` を使ってファイルシステム全体から `web.config`, `*.bak`, `*password*.txt` のような名前のファイルを探し出し、情報窃取の足がかりとします。
* **例**:

    ```powershell
    # Cドライブ全体から、名前に 'config' を含む .xml または .ini ファイルを検索
    Get-ChildItem -Path "C:\" -Recurse -Include "*config*.xml", "*config*.ini" -ErrorAction SilentlyContinue
    ```

## エラーメッセージとトラブルシューティング

### よくあるエラーメッセージ

1. **エラーメッセージ**: `Get-ChildItem: Access to the path '...' is denied.`
    * **考えられる原因**: アクセス権のないディレクトリ（他のユーザーのプロファイル、一部のシステムフォルダなど）にアクセスしようとしました。
    * **解決策**: PowerShellを「管理者として実行」します。それでもアクセスできない場所がある場合は、`-ErrorAction SilentlyContinue` を付けてエラーを無視し、スキャンを続行させることができます。

## セキュリティに関する考慮事項

### 悪用事例

* **偵察 (Reconnaissance)**: レッドチームのシナリオで述べた通り、`Get-ChildItem` は攻撃者が侵入したシステム内のファイルシステムを探索し、情報価値の高いファイル（設定ファイル、パスワードリスト、ユーザーデータなど）を発見するための最も基本的なツールです。

### LOLBASにおける利用例

* **機能**: **偵察 (Reconnaissance)**
* **解説**: `Get-ChildItem` は PowerShell の標準機能であり、`powershell.exe` を通じて実行されます。攻撃者はこの正規の管理機能を利用して、検知されにくい形でファイルシステムを探索します（Living off the Land）。

### 対応策・緩和策 (ブルーチーム視-

**Prevention (予防)**: 最小権限の原則に基づき、ファイルやディレクトリに適切なACL（アクセス制御リスト）を設定し、ユーザーに不要なディレクトリへのアクセス権を与えない。

* **Detection (検知)**: PowerShellのスクリプトブロックロギング (イベントID 4104) を有効化し、`Get-ChildItem` を使った広範囲なファイルスキャン（特に `-Recurse` を伴うもの）や、機密性の高いディレクトリに対する実行を監視する。`Sysmon` でのファイルアクセスイベントの監視も有効です。

## 注意点・補足

* **パフォーマンス**: `-Filter` パラメータは、プロバイダー（ファイルシステムなど）のレベルでフィルタリングを行うため、`Where-Object` を使って後からフィルタリングするよりも一般的に高速です。可能な限り `-Filter` を使用することが推奨されます。

---
[PowerShellインデックスに戻る](../../powershell_index.md)

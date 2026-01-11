---
tags:
  - 'New-Item'
  - 'powershell'
  - 'cheatsheet'
title: 'New-Item - 新しいアイテムを作成する'
summary: 'ファイルシステムに新しいファイルやディレクトリを作成したり、レジストリに新しいキーを作成したりします。'
related:
  - 'Get-Item'
  - 'Copy-Item'
  - 'Remove-Item'
  - 'mkdir'
---

# `New-Item` - 新しいアイテムを作成する

## 概要

`New-Item` コマンドレットは、PowerShellのプロバイダー（ファイルシステム、レジストリなど）で新しいアイテムを作成します。最も一般的には、新しいファイルやディレクトリ（フォルダ）を作成するために使用されます。`-ItemType` パラメータで作成するアイテムの種類を指定し、`-Value` でファイルに書き込む初期コンテンツを指定することもできます。

このコマンドレットには、`ni` という便利なエイリアスがデフォルトで設定されています。

## 類似コマンドレット/コマンドとの差異

| 類似コマンドレット/コマンド | 差異 / 使い分けのポイント |
| :--- | :--- |
| **`mkdir` / `md` (CMD/PowerShellエイリアス)** | PowerShellでは、`mkdir` と `md` は `New-Item -ItemType Directory` のエイリアスとして機能します。ディレクトリ作成に特化しており、より直感的です。 |
| **`echo "content" > file.txt` (CMD)** | CMDのリダイレクトは新しいファイルを作成してコンテンツを書き込みます。`New-Item -Value "..."` は同様の機能ですが、エンコーディングの指定など、より多くのオプションをPowerShell内で制御できます。 |
| **`Set-Content`** | `Set-Content` も、存在しないファイルに対して実行すると新しいファイルを作成して内容を書き込みます。`New-Item` はアイテムの「作成」という意図がより明確です。 |

## よく連携されるコマンドレット

### シナリオ例: 日付名のディレクトリを作成し、その中にログファイルを作成する (インフラ構築・運用視点)

* **目的**: 今日の日付 (`YYYY-MM-DD`) で名付けられたディレクトリを作成し、その中に空のログファイル `script.log` を作成する。
* **連携コマンドレット**: `Get-Date`, `Join-Path`
* **解説**: `Get-Date` で現在の日付を取得し、フォーマットしてディレクトリ名とします。`Join-Path` を使って安全にパスを結合し、`New-Item` でディレクトリとファイルを順に作成します。
* **コマンド例**:

    ```powershell
    # 今日の日付でディレクトリパスを生成
    $dirName = Get-Date -Format "yyyy-MM-dd"
    $logPath = "C:\Logs\$dirName"

    # ディレクトリを作成 (-Forceで既に存在してもエラーにならない)
    New-Item -Path $logPath -ItemType Directory -Force

    # 空のログファイルを作成
    New-Item -Path (Join-Path -Path $logPath -ChildPath "script.log") -ItemType File
    ```

## パラメータ説明

| パラメータ | 型 | 説明 |
| :--- | :--- | :--- |
| ⭐ **`-Path`** | `String[]` | 新しいアイテムを作成するパスを指定します。 |
| ⭐ **`-Name`** | `String` | 新しいアイテムの名前を指定します。`-Path` がディレクトリを指す場合、このパラメータでファイル名/ディレクトリ名を指定します。 |
| ⭐ **`-ItemType`** | `String` | 作成するアイテムの種類を指定します。ファイルシステムでは `File` または `Directory` が一般的です。 |
| **`-Value`** | `Object` | 作成するアイテム（特にファイル）の初期コンテンツを指定します。 |
| ⭐ **`-Force`** | `SwitchParameter` | 既に存在するディレクトリ内にアイテムを作成したり、読み取り専用の場所に作成したりするなど、制限を上書きします。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: スクリプトの実行に必要な一時ディレクトリを作成する。
* **組み合わせ**: `New-Item -Path <path> -ItemType Directory`
* **解説**: スクリプトの冒頭で作業用のディレクトリを作成し、最後に `Remove-Item` でクリーンアップする、という一連の流れで使われます。
* **例**:

    ```powershell
    # 作業用の一時ディレクトリを作成
    $tempDir = New-Item -Path "$env:TEMP\MyWorkDir" -ItemType Directory -Force
    Write-Host "Working in $($tempDir.FullName)"
    ```

### 2. ブルーチーム視点

* **タスク**: **インシデント調査**。マルウェアが作成した不審なファイルやディレクトリの作成タイムスタンプを確認する。
* **組み合わせ**: (`Get-Item` での調査)
* **解説**: `New-Item` は攻撃者がファイルやディレクトリを作成する際にも使用されます。ブルーチームは、PowerShellのログを分析し、`New-Item` がいつ、どのパスに対して実行されたかを調査することで攻撃のタイムラインを特定する手がかりとします。
* **例**: -

### 3. レッドチーム視点

* **タスク**: **偵察 (Reconnaissance)** と**永続化 (Persistence)** のための隠しディレクトリやファイルを作成する。
* **組み合わせ**: `New-Item -ItemType Directory`
* **解説**: 攻撃者は侵入後、自身のツールや窃取したデータを保管するために、正規のディレクトリに見せかけた名前のディレクトリ (`C:\ProgramData\Microsoft Help` のようにスペースを入れるなど) や、あまり監視されていない場所 (`C:\Users\Public`など) に `New-Item` でディレクトリを作成します。
* **例**:

    ```powershell
    # ツールを隠すためのディレクトリを 'ProgramData' に作成
    New-Item -Path "$env:ProgramData\SystemUpdates" -ItemType Directory -Force
    ```

## エラーメッセージとトラブルシューティング

### よくあるエラーメッセージ

1. **エラーメッセージ**: `New-Item: Access to the path '...' is denied.`
    * **考えられる原因**: アイテムを作成しようとしているディレクトリに対する書き込み権限がありません。
    * **解決策**: PowerShellを「管理者として実行」するか、書き込み権限のあるディレクトリをパスとして指定してください。

2. **エラーメッセージ**: `New-Item: An item with the specified name ... already exists.`
    * **考えられる原因**: 作成しようとしたアイテムが既に存在します。
    * **解決策**: 別の名前を使用するか、上書きしてもよい場合は `-Force` スイッチを追加してください。

## セキュリティに関する考慮事項

### 悪用事例

* **ペイロードの配置**: 攻撃者は `New-Item` と `Add-Content` (または `-Value`) を組み合わせて、悪意のあるスクリプトやバイナリをシステム上に作成します。
* **ステージングディレクトリの作成**: データ窃取の前に、収集したファイルを一時的に保管するためのディレクトリを作成するために使用されます。

### LOLBASにおける利用例

* **機能**: **防御回避 (Defense Evasion)**
* **解説**: `New-Item` は PowerShell の標準機能であり、攻撃者が検知を回避しながらファイルシステム上にアイテムを作成するために利用されます（Living off the Land）。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: AppLockerやWDAC (Windows Defender Application Control) を使用して、信頼できないディレクトリ（`%TEMP%`, `C:\Users\Public`など）からの実行ファイルの実行を禁止する。
* **Detection (検知)**: `Sysmon` (イベントID 11: FileCreate) を使って、予期せぬ場所へのファイルやディレクトリの作成を監視する。PowerShellのスクリプトブロックロギング (イベントID 4104) を有効化し、`New-Item` の実行、特に不審なパスやファイル名での実行を記録・監視する。

## 注意点・補足

* **中間ディレクトリ**: `New-Item -Path "C:\a\b\c" -ItemType File` のようにパスを指定した場合、中間にある `a` や `b` ディレクトリが存在しないとエラーになります。中間ディレクトリも同時に作成するには、`-Force` スイッチを使用します。

---
[PowerShellインデックスに戻る](../../powershell_index.md)

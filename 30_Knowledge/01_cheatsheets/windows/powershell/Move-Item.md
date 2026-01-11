---
tags:
  - 'Move-Item'
  - 'powershell'
  - 'cheatsheet'
title: 'Move-Item - アイテムを移動する'
summary: 'ファイル、ディレクトリ、レジストリキーなどのアイテムを、ある場所から別の場所へ移動します。アイテムの名前変更にも使用できます。'
related:
  - 'Copy-Item'
  - 'Remove-Item'
  - 'Rename-Item'
  - 'move'
---

# `Move-Item` - アイテムを移動する

## 概要

`Move-Item` コマンドレットは、アイテムとその内容を、ある場所から別の場所へ移動します。移動元と移動先が同じプロバイダー（例: どちらもファイルシステム）である必要があります。ファイルやディレクトリの移動だけでなく、同じ場所で新しい名前を指定することで、アイテムの**名前変更**にも使用できます。

このコマンドレットには、`mv`, `mi`, `move` という便利なエイリアスがデフォルトで設定されています。

## 類似コマンドレット/コマンドとの差異

| 類似コマンドレット/コマンド | 差異 / 使い分けのポイント |
| :--- | :--- |
| **`move` (CMD)** | CMDの `move` は文字列ベースで動作します。`Move-Item` はオブジェクトを扱い、PowerShellのパイプラインで `Get-ChildItem` の結果を直接受け取るなど、連携がスムーズです。 |
| **`Copy-Item`** | `Copy-Item` はアイテムを**複製**し、元のアイテムはそのまま残ります。`Move-Item` はアイテムを**移動**し、元のアイテムは削除されます。 |
| **`Rename-Item`** | `Rename-Item` はアイテムの名前変更に特化しています。`Move-Item` は、名前変更と、別の場所への移動の両方が可能です。 |

## よく連携されるコマンドレット

### シナリオ例: 古いログファイルをアーカイブフォルダに移動する (インフラ構築・運用視点)

* **目的**: ログディレクトリ内にある、最終更新日時が30日以上前の `.log` ファイルを全て検索し、アーカイブ用の別フォルダに移動する。
* **連携コマンドレット**: `Get-ChildItem`, `Where-Object`
* **解説**: `Get-ChildItem` で全てのログファイルオブジェクトを取得し、パイプラインで `Where-Object` に渡して日付の条件でフィルタリングします。最後に、条件に一致したファイルオブジェクトを `Move-Item` に渡して指定したディレクトリに移動させます。
* **コマンド例**:

    ```powershell
    # 30日以上前の .log ファイルを 'C:\Logs\Archive' に移動（-WhatIfで事前確認）
    Get-ChildItem -Path "C:\Logs\*.log" | Where-Object { $_.LastWriteTime -lt (Get-Date).AddDays(-30) } | Move-Item -Destination "C:\Logs\Archive\" -WhatIf
    ```

## パラメータ説明

| パラメータ | 型 | 説明 |
| :--- | :--- | :--- |
| ⭐ **`-Path`** | `String[]` | 移動元のアイテムのパスを指定します。パイプラインからの入力を受け取ります。 |
| ⭐ **`-Destination`** | `String` | 移動先のパスを指定します。 |
| ⭐ **`-Force`** | `SwitchParameter` | 読み取り専用のアイテムを上書きするなど、制限を強制的に上書きします。 |
| **`-LiteralPath`** | `String[]` | ワイルドカードを解釈せず、パスを文字通りに受け取ります。 |
| ⭐ **`-WhatIf`** | `SwitchParameter` | コマンドを実行せずに、何が起こるかを表示します。破壊的な操作の前に安全確認のために使用します。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: ファイルの名前を変更する。
* **組み合わせ**: `Move-Item -Path <old_name> -Destination <new_name>`
* **解説**: `-Destination` に新しいファイル名を指定することで、ファイルのリネームができます。`Rename-Item` と同じ結果になりますが、`Move-Item` でも可能であることは覚えておくと便利です。
* **例**:

    ```powershell
    # 'temp_report.txt' を 'final_report.txt' に名前変更
    Move-Item -Path ".\temp_report.txt" -Destination ".\final_report.txt"
    ```

### 2. ブルーチーム視点

* **タスク**: **インシデント対応における封じ込め**。マルウェアを隔離（Quarantine）する。
* **組み合わせ**: `Move-Item -Path <malware> -Destination <quarantine_folder>`
* **解説**: 疑わしいファイルを `Remove-Item` で削除すると証拠が失われます。代わりに `Move-Item` を使って、そのファイルを隔離用の安全なディレクトリに移動させます。これにより、マルウェアを元の場所から無力化しつつ、後の詳細なフォレンジック分析のためにファイルを保全できます。
* **例**:

    ```powershell
    # 不審なファイルを隔離ディレクトリに移動
    Move-Item -Path "C:\Users\Public\svchost.exe" -Destination "C:\Quarantine\" -Force
    ```

### 3. レッドチーム視点

* **タスク**: **実行パスハイジャック**。正規のプログラムを悪意のあるものに置き換える。
* **組み合わせ**: `Move-Item`
* **解説**: **永続化 (Persistence)** や**防御回避 (Defense Evasion)** の一環。攻撃者は、まず正規のプログラム（例: `Util.exe`）を `Move-Item` で別の名前にリネームして退避させます。次に、バックドアを仕込んだ悪意のあるプログラムを `Move-Item` で正規のプログラム名に変更して配置します。
* **例**:

    ```powershell
    # 正規のヘルパーツールを退避
    Move-Item -Path "C:\App\helper.exe" -Destination "C:\App\helper.exe.bak"

    # 悪意のあるペイロードを正規のツール名にリネームして配置
    Move-Item -Path "C:\Users\Public\payload.exe" -Destination "C:\App\helper.exe"
    ```

## エラーメッセージとトラブルシューティング

### よくあるエラーメッセージ

1. **エラーメッセージ**: `Move-Item: Access to the path '...' is denied.`
    * **考えられる原因**: 移動元または移動先に対する権限がありません。
    * **解決策**: PowerShellを「管理者として実行」します。または `Get-Acl`/`Set-Acl` でアクセス権を確認・変更してください。

2. **エラーメッセージ**: `Move-Item: The process cannot access the file '...' because it is being used by another process.`
    * **考えられる原因**: 移動しようとしているファイルが、別のプログラムによって開かれている（ロックされている）ため、移動できません。
    * **解決策**: Process Explorer (Sysinternals) などのツールで、どのプロセスがファイルをロックしているかを確認し、そのプロセスを終了させてから再試行します。

## セキュリティに関する考慮事項

### 悪用事例

* **ファイルハイジャック**: レッドチームのシナリオで述べた通り、正規の実行ファイルやDLLを悪意のあるものに置き換える「実行パスハイジャック」や、設定ファイルを改ざんする目的で利用されます。
* **データ移動**: 収集した機密情報を、外部に持ち出す前の一時的な場所（ステージングエリア）に移動させるために使用されます。

### LOLBASにおける利用例

* **機能**: **防御回避 (Defense Evasion)**
* **解説**: `Move-Item` は PowerShell の標準機能であり、攻撃者が検知を回避しながら悪意のあるファイルを正規の場所に配置したり、既存のファイルを置き換えたりするために利用されます。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: ファイルシステムに適切なACL（アクセス制御リスト）を設定し、重要なシステムファイルやアプリケーションディレクトリへの書き込み・変更権限を最小限に抑える。
* **Detection (検知)**: ファイル整合性監視 (FIM) ツールや `Sysmon` (イベントID 11: FileCreate は移動操作でも発生) で、重要なファイルの移動、名前変更、上書きを監視する。PowerShellのスクリプトブロックロギングも有効です。

## 注意点・補足

* `-WhatIf` パラメータは、破壊的な操作や広範囲に影響する操作を行う前に、どのような変更が行われるかを安全に確認するための非常に重要な機能です。積極的に利用することを推奨します。

---
[PowerShellインデックスに戻る](../../powershell_index.md)

---
tags:
  - 'Rename-Item'
  - 'powershell'
  - 'cheatsheet'
title: 'Rename-Item - アイテムの名前を変更する'
summary: 'ファイル、ディレクトリ、レジストリキーなど、指定したアイテムの名前を変更します。'
related:
  - 'Move-Item'
  - 'Copy-Item'
  - 'Get-Item'
  - 'ren'
---

# `Rename-Item` - アイテムの名前を変更する

## 概要

`Rename-Item` コマンドレットは、ファイルシステム上のファイルやディレクトリ、レジストリキーなど、指定したアイテムの名前を変更します。`Move-Item` とは異なり、アイテムを別の場所に移動させることはなく、現在の場所で名前の変更のみを行います。

このコマンドレットには、`ren`, `rni` という便利なエイリアスがデフォルトで設定されています。

## 類似コマンドレット/コマンドとの差異

| 類似コマンドレット/コマンド | 差異 / 使い分けのポイント |
| :--- | :--- |
| **`ren` (CMD)** | CMDの `ren` は文字列ベースで動作し、ワイルドカードの挙動も異なります。`Rename-Item` はオブジェクトを扱い、パイプラインを通じて受け取ったオブジェクトに対して名前変更を行えるため、より柔軟で予測可能な動作をします。 |
| **`Move-Item`** | `Move-Item` はアイテムの移動と名前変更の両方が可能です。`Rename-Item` は**名前の変更に特化**しており、操作の意図が明確になります。 |

## よく連携されるコマンドレット

### シナリオ例: 複数のファイル名の一部を置換する (インフラ構築・運用視点)

* **目的**: ディレクトリ内にある全てのファイル名に含まれる "temp_" という接頭辞を "final_" に一括で置換する。
* **連携コマンドレット**: `Get-ChildItem`, `ForEach-Object`
* **解説**: `Get-ChildItem` で対象のファイルオブジェクトを全て取得します。次に、`ForEach-Object` ループで各ファイルを1つずつ処理し、`-replace` 演算子で新しい名前を生成して `Rename-Item` を実行します。
* **コマンド例**:

    ```powershell
    # 'temp_' で始まる全ての .txt ファイルを 'final_' で始まる名前に変更（-WhatIfで事前確認）
    Get-ChildItem -Path ".\temp_*.txt" | ForEach-Object {
        $newName = $_.Name -replace '^temp_', 'final_'
        Rename-Item -Path $_.FullName -NewName $newName -WhatIf
    }
    ```

## パラメータ説明

| パラメータ | 型 | 説明 |
| :--- | :--- | :--- |
| ⭐ **`-Path`** | `String` | 名前を変更するアイテムの現在のパスを指定します。 |
| ⭐ **`-NewName`** | `String` | アイテムの新しい名前を指定します。パスを含めることはできません。 |
| **`-LiteralPath`** | `String` | ワイルドカードを解釈せず、パスを文字通りに受け取ります。 |
| ⭐ **`-Force`** | `SwitchParameter` | 読み取り専用や隠しファイルなど、通常は変更できないアイテムを強制的に名前変更します。 |
| ⭐ **`-WhatIf`** | `SwitchParameter` | コマンドを実行せずに、何が起こるかを表示します。破壊的な操作の前に安全確認のために使用します。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: 設定ファイルのテンプレート (`config.xml.template`) から `.template` 拡張子を取り除き、有効な設定ファイルとしてリネームする。
* **組み合わせ**: `Rename-Item -Path <old_name> -NewName <new_name>`
* **解説**: 最も基本的な使い方。デプロイスクリプトなどで、テンプレートファイルを実際の設定ファイルとして有効化する際によく使われます。
* **例**:

    ```powershell
    # テンプレートファイルの名前を変更
    Rename-Item -Path ".\config.xml.template" -NewName "config.xml"
    ```

### 2. ブルーチーム視点

* **タスク**: **インシデント対応における封じ込め**。マルウェアの疑いがあるファイルを実行できないように、拡張子を変更して無力化する。
* **組み合わせ**: `Rename-Item -Path <malware_path> -NewName <new_name>`
* **解説**: ファイルを削除 (`Remove-Item`) すると証拠が失われます。`Rename-Item` を使って拡張子を `.exe` から `.vir` などに変更することで、ファイルを実行不可能な状態にしつつ、後のフォレンジック分析のために保全します。
* **例**:

    ```powershell
    # 不審な実行ファイルの拡張子を変更して無力化
    Rename-Item -Path "C:\Users\Public\svchost.exe" -NewName "svchost.exe.vir" -Force
    ```

### 3. レッドチーム視点

* **タスク**: **防御回避 (Defense Evasion)**。攻撃ツールを正規のシステムファイル名に偽装する（マスカーディング）。
* **組み合わせ**: `Rename-Item`
* **解説**: 攻撃者は、`netcat` (`nc.exe`) や `mimikatz.exe` のような既知の攻撃ツールを、`svchost.exe` や `lsass.exe` のようなありふれたシステムプロセス名にリネームしてディスクに配置します。これにより、単純なファイル名ベースの検知ルールを回避しようとします。
* **例**:

    ```powershell
    # 攻撃ツール 'psexec.exe' を 'services.exe' に偽装
    Rename-Item -Path "C:\Users\Public\psexec.exe" -NewName "services.exe"
    ```

## エラーメッセージとトラブルシューティング

### よくあるエラーメッセージ

1. **エラーメッセージ**: `Rename-Item: Cannot rename because item at '...' is in use.`
    * **考えられる原因**: 名前を変更しようとしているファイルが、別のプロセスによって開かれている（ロックされている）ためです。
    * **解決策**: Process Explorer (Sysinternals) などのツールで、どのプロセスがファイルをロックしているかを確認し、そのプロセスを終了させてから再試行します。

## セキュリティに関する考慮事項

### 悪用事例

* **マスカーディングによる防御回避**: レッドチームのシナリオで述べた通り、`Rename-Item` は攻撃者が自身のツールやペイロードを、正規のファイルや一般的に見られるファイル名に偽装するための基本的な手段です。これにより、EDRやアンチウイルスによる検知を回避しやすくなります。

### LOLBASにおける利用例

* **機能**: **防御回避 (Defense Evasion)**
* **解説**: `Rename-Item` は PowerShell の標準機能であり、攻撃者が検知を回避しながらファイルシステム上で自身のツールを隠蔽するために利用されます（Living off the Land）。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: AppLockerやWDAC (Windows Defender Application Control) を使用して、信頼できない場所（`C:\Users\Public`など）からの実行を制限する。これにより、たとえファイル名が偽装されていても、実行を防ぐことができます。
* **Detection (検知)**: `Sysmon` (イベントID 11: FileCreate は名前変更でも発生) やファイル整合性監視 (FIM) ツールで、ファイルの名前変更イベントを監視する。特に、正規のプロセス名を持つファイルが予期せぬ場所で作成されたり、ハッシュ値が既知のものと異なる場合は、マスカーディングの兆候である可能性があります。PowerShellのスクリプトブロックロギングも有効です。

## 注意点・補足

* **`-WhatIf` の重要性**: 複数のファイルに対して一括で名前変更を行うような破壊的な操作の前には、必ず `-WhatIf` スイッチを付けてコマンドを実行し、意図した通りの名前変更が行われるかを確認することが強く推奨されます。

---
[PowerShellインデックスに戻る](../../powershell_index.md)

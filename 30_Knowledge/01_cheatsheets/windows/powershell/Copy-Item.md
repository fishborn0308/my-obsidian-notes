---
tags:
  - 'Copy-Item'
  - 'powershell'
  - 'cheatsheet'
title: 'Copy-Item - アイテムをコピーする'
summary: 'ファイル、ディレクトリ、レジストリキーなどのアイテムを、ある場所から別の場所へコピーします。'
related:
  - 'Move-Item'
  - 'Remove-Item'
  - 'Get-ChildItem'
  - 'robocopy'
---

# `Copy-Item` - アイテムをコピーする

## 概要

`Copy-Item` コマンドレットは、ファイルシステム上のファイルやディレクトリ、レジストリ、証明書ストアなど、さまざまなプロバイダーのアイテムをコピーします。CMDの `copy` や `xcopy` に相当する基本的なファイル操作コマンドですが、PowerShellのオブジェクトパイプラインと連携できるため、より柔軟で強力な操作が可能です。

このコマンドレットには、`cp`, `cpi`, `copy` という便利なエイリアスがデフォルトで設定されています。

## 類似コマンドレット/コマンドとの差異

| 類似コマンドレット/コマンド | 差異 / 使い分けのポイント |
| :--- | :--- |
| **`copy` / `xcopy` (CMD)** | CMDのコマンドは文字列ベースで動作します。`Copy-Item` はオブジェクトを扱い、PowerShellのパイプラインで `Get-ChildItem` の結果を直接受け取るなど、連携がスムーズです。 |
| **`robocopy` (CMD)** | `robocopy` は、リトライ機能やミラーリングなど、より高度で堅牢なファイル同期機能を提供します。大量のファイル移行や信頼性が求められるバックアップには `robocopy` が適しています。`Copy-Item` は日常的なコピー操作やスクリプト内の基本的なファイル操作に向いています。 |
| **`Move-Item`** | `Move-Item` はアイテムを**移動**します（元のアイテムは削除されます）。`Copy-Item` はアイテムを**複製**し、元のアイテムはそのまま残ります。 |

## よく連携されるコマンドレット

### シナリオ例: 特定の種類のファイルだけをバックアップする (インフラ構築・運用視点)

* **目的**: ドキュメントフォルダから、全ての `.docx` ファイルを再帰的に検索し、バックアップフォルダにコピーする。
* **連携コマンドレット**: `Get-ChildItem`
* **解説**: `Get-ChildItem` に `-Recurse` と `-Filter` を付けて対象のファイルをオブジェクトとして取得し、その結果をパイプラインで `Copy-Item` に渡します。これにより、複雑な条件に一致するファイル群を簡単にコピーできます。
* **コマンド例**:

    ```powershell
    # Documents フォルダ以下の全 .docx ファイルを D:\DocBackup にコピー
    Get-ChildItem -Path $env:USERPROFILE\Documents -Filter "*.docx" -Recurse | Copy-Item -Destination "D:\DocBackup\"
    ```

## パラメータ説明

| パラメータ | 型 | 説明 |
| :--- | :--- | :--- |
| ⭐ **`-Path`** | `String[]` | コピー元のアイテムのパスを指定します。パイプラインからの入力を受け取ります。 |
| ⭐ **`-Destination`** | `String` | コピー先のパスを指定します。 |
| ⭐ **`-Recurse`** | `SwitchParameter` | ディレクトリをコピーする際に、サブディレクトリとその内容を再帰的にコピーします。 |
| **`-Filter`** | `String` | `-Path` パラメータの対象をフィルタリングするための文字列を指定します。（例: `"*.log"`) |
| **`-Include`** | `String[]` | コピー対象に含めるアイテムを指定します。`-Recurse` と共に使われることが多いです。 |
| **`-Exclude`** | `String[]` | コピー対象から除外するアイテムを指定します。 |
| ⭐ **`-Force`** | `SwitchParameter` | 読み取り専用ファイルの上書きや、隠しファイルのコピーなど、制限を強制的に上書きします。 |
| **`-Container`** | `SwitchParameter` | ディレクトリをコピーする際に、親ディレクトリの構造を維持します。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: アプリケーションのディレクトリ全体をバックアップする。
* **組み合わせ**: `Copy-Item -Path <source> -Destination <dest> -Recurse`
* **解説**: `-Recurse` スイッチを付けることで、ディレクトリとその中身（サブディレクトリ、ファイル）を丸ごとコピーできます。
* **例**:

    ```powershell
    # C:\App\WebApp を D:\Backups\WebApp_backup に再帰的にコピー
    Copy-Item -Path "C:\App\WebApp" -Destination "D:\Backups\WebApp_backup" -Recurse
    ```

### 2. ブルーチーム視点

* **タスク**: **証拠保全 (Forensics)**。侵害されたホストから、不審なファイルを調査用ストレージに保全する。
* **組み合わせ**: `Copy-Item`
* **解説**: `move` や `del` と異なり、`Copy-Item` は元のファイルを変更せずに複製を作成するため、証拠の完全性を維持する上で基本となります。
* **例**:

    ```powershell
    # 不審なファイルを D:\Forensics\Case001 に保全
    Copy-Item -Path "C:\Users\Public\suspicious.exe" -Destination "D:\Forensics\Case001\"
    ```

### 3. レッドチーム視点

* **タスク**: **データ収集 (Data Collection)** と**ラテラルムーブメントの準備**。
* **組み合わせ**: `Copy-Item <source> <destination>`
* **解説**: 攻撃者は侵入後、`Copy-Item` を使って機密情報が含まれるファイルを `%TEMP%` や `C:\Users\Public` のような一時的な場所に集約したり、他のサーバーに展開するためのツールキットをネットワーク共有にコピーしたりします。PowerShell を使うことで、CMDの `copy` よりも検知されにくい場合があります。
* **例**:

    ```powershell
    # ユーザーのドキュメントをデータ窃取の準備として Public フォルダにコピー
    Copy-Item -Path "$env:USERPROFILE\Documents\*.docx" -Destination "$env:PUBLIC\Documents\" -Recurse
    ```

## エラーメッセージとトラブルシューティング

### よくあるエラーメッセージ

1. **エラーメッセージ**: `Copy-Item: Access to the path '...' is denied.`
    * **考えられる原因**: コピー元への読み取り権限、またはコピー先への書き込み権限がありません。
    * **解決策**: PowerShellを「管理者として実行」するか、`Get-Acl`/`Set-Acl` でアクセス権を確認・変更してください。

2. **エラーメッセージ**: `Copy-Item: Cannot find path '...' because it does not exist.`
    * **考えられる原因**: コピー元として指定したパスが存在しません。
    * **解決策**: `Test-Path` コマンドレットでパスの存在を確認し、正しいパスを指定してください。

## セキュリティに関する考慮事項

### 悪用事例

* **データ窃取とステージング**: レッドチームのシナリオで述べた通り、`Copy-Item` は攻撃者がデータを収集し、外部に持ち出す前の一時的な場所（ステージングエリア）に集めるために悪用されます。
* **ツール展開**: ネットワーク内の他のマシンに攻撃ツールを拡散させるために、`Copy-Item` を使ってファイル共有経由でファイルをコピーします。

### LOLBASにおける利用例

* **機能**: **データ収集 (Data Collection)**, **データ窃取 (Data Exfiltration)**
* **解説**: `Copy-Item` は PowerShell の標準機能であり、`powershell.exe` を通じて実行されます。攻撃者はこの正規の機能を利用して、検知されにくい形でファイル操作を行います。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: ファイル共有やディレクトリに適切なACLを設定し、最小権限の原則を徹底する。
* **Detection (検知)**: PowerShellのスクリプトブロックロギング (イベントID 4104) を有効化し、`Copy-Item` の実行、特に機密性の高いファイルや、ネットワークパスを対象とした操作を監視する。`Sysmon` (イベントID 11: FileCreate) で予期せぬ場所へのファイル作成を監視する。

## 注意点・補足

* **ディレクトリのコピー**: ディレクトリをコピーする際に `-Recurse` を付けないと、ディレクトリの箱だけがコピーされ、中身はコピーされません。

---
[PowerShellインデックスに戻る](../../powershell_index.md)

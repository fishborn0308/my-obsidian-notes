---
tags:
  - 'Get-Help'
  - 'powershell'
  - 'cheatsheet'
title: 'Get-Help - コマンドのヘルプを表示する'
summary: 'コマンドレット、関数、スクリプトなど、PowerShellのコマンドに関するヘルプ情報を表示します。'
related:
  - 'Get-Command'
  - 'Update-Help'
  - 'Get-Member'

---

# `Get-Help` - コマンドのヘルプを表示する

## 概要

`Get-Help` コマンドレットは、PowerShellのコマンド（コマンドレット、関数、エイリアスなど）の目的、構文、パラメータ、使用例といった詳細なヘルプ情報を表示します。PowerShellでコマンドの使い方を学習し、理解するための最も基本的なツールです。

`help` 関数や `man` エイリアスは、内部で `Get-Help` を呼び出しており、同じように使用できます。

## 類似コマンドレット/コマンドとの差異

| 類似コマンドレット/コマンド | 差異 / 使い分けのポイント |
| :--- | :--- |
| **`Get-Command`** | `Get-Command` はコマンドの**存在**とその基本情報（種類、モジュールなど）を**検索**します。`Get-Help` は、見つけたコマンドの**使い方**を**説明**します。 |
| **`<command> /?` (CMD)** | CMDのヘルプ表示方法。`Get-Help` は、パラメータごとの詳細、複数の使用例、入力と出力の型など、はるかに構造化され、詳細な情報を提供します。 |

## よく連携されるコマンドレット

### シナリオ例: コマンドを検索し、その使い方を調べる (インフラ構築・運用視点)

- **目的**: "IP" に関連するコマンドを検索し、その中の1つ (`Get-NetIPAddress`) の使い方を詳しく知る。

- **連携コマンドレット**: `Get-Command`
- **解説**: まず `Get-Command` で目的の機能に関連するコマンドを発見し、その名前を `Get-Help` に渡して詳細なドキュメントを表示します。
- **コマンド例**:

    ```powershell
    # 'IP' という名詞を持つコマンドを検索
    Get-Command -Noun "*IP*"

    # 見つけた 'Get-NetIPAddress' の詳細なヘルプを表示
    Get-Help -Name "Get-NetIPAddress" -Full
    ```

## パラメータ説明

| パラメータ | 型 | 説明 |
| :--- | :--- | :--- |
| ⭐ **`-Name`** | `String` | ヘルプを表示するコマンドの名前を指定します。 |
| ⭐ **`-Full`** | `SwitchParameter` | パラメータの詳細、入力、出力、メモ、使用例を含む、**完全な**ヘルプ情報を表示します。 |
| ⭐ **`-Detailed`** | `SwitchParameter` | 標準の表示より詳細な情報（パラメータの説明や例など）を表示します。 |
| ⭐ **`-Examples`** | `SwitchParameter` | コマンドの**使用例のみ**を表示します。実践的な使い方を素早く知りたい場合に非常に便利です。 |
| ⭐ **`-Online`** | `SwitchParameter` | 最新のヘルプドキュメントを、デフォルトのWebブラウザでオンラインで開きます。 |
| **`-Parameter <String>`** | `String` | 指定したパラメータに関する詳細な情報のみを表示します。 |
| **`-ShowWindow`** | `SwitchParameter` | ヘルプを、検索機能付きの独立したGUIウィンドウに表示します。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

- **タスク**: 新しいコマンドレット `Invoke-WebRequest` の実践的な使い方を学ぶ。
- **組み合わせ**: `Get-Help <Cmdlet> -Examples`
- **解説**: `-Examples` スイッチを使うことで、ドキュメント全体を読まなくても、コマンドの具体的な使用例を素早く確認できます。
- **例**:

    ```powershell
    # Invoke-WebRequest の使用例を表示
    Get-Help -Name "Invoke-WebRequest" -Examples
    ```

### 2. ブルーチーム視点

- **タスク**: 監査中に発見した不審なPowerShellスクリプト内で使われている、見慣れないコマンドレットの機能を調査する。
- **組み合わせ**: `Get-Help <Cmdlet> -Full`
- **解説**: 攻撃者はしばしば、正規のPowerShellコマンドレットを予期せぬ方法で悪用します。スクリプト内で発見した未知のコマンドレット（例: `Invoke-Expression`）に対して `Get-Help -Full` を実行し、そのコマンドがどのような入力（`-InputObject`）を受け取り、何を実行するのかを正確に理解することで、スクリプトの真の目的を解明します。
- **例**:

    ```powershell
    # Invoke-Expression (エイリアス: iex) の危険性をヘルプで確認
    Get-Help -Name "Invoke-Expression" -Full
    ```

### 3. レッドチーム視点

- **タスク**: **偵察 (Reconnaissance)**。環境寄生（Living off the Land）に利用できるコマンドレットの悪用方法を発見する。
- **組み合わせ**: `Get-Help <Cmdlet> -Parameter *`
- **解説**: 攻撃者は、`Invoke-Command` や `Enter-PSSession` のようなラテラルムーブメントに利用できるコマンドレットのヘルプを調べ、`-Credential` や `-Port`、`-Session` といった、攻撃シナリオに適合するパラメータを探します。
- **例**:

    ```powershell
    # Invoke-Command の全パラメータを確認し、リモート実行の方法を調査
    Get-Help Invoke-Command -Parameter *
    ```

## エラーメッセージとトラブルシューティング

### よくあるエラーメッセージ

1. **エラーメッセージ**: `Get-Help could not find <command> in any help file.`
    - **考えられる原因**:
        - コマンド名のスペルが間違っている。
        - そのコマンドが含まれるモジュールがインポートされていない。
        - ヘルプファイルがシステムにインストールされていない。
    - **解決策**: コマンド名を確認します。`Get-Command <command>` でコマンドが存在するか確認します。ヘルプファイルがない場合は、管理者として `Update-Help` を実行します。

## セキュリティに関する考慮事項

### 悪用事例

- **偵察 (Reconnaissance)**: レッドチームのシナリオで述べた通り、`Get-Help` は攻撃者が侵入したシステムで利用可能なコマンドの機能や、その悪用方法を学習するための最も基本的な偵察ツールです。

### LOLBASにおける利用例

- **機能**: **偵察 (Reconnaissance)**

- **解説**: `Get-Help` は PowerShell の標準機能であり、攻撃者が他の LOLBAS 手法（`Invoke-Expression` の利用法など）を発見・学習するために使用されます。

### 対応策・緩和策 (ブルーチーム視点)

- **Prevention (予防)**: -

- **Detection (検知)**: PowerShellのスクリプトブロックロギング (イベントID 4104) を有効化し、`Get-Help` の実行を記録・監視する。`Get-Help` の実行自体は非常に一般的であるため、他の偵察コマンド（`Get-Command`, `Get-CimInstance`など）との連続実行や、不審なプロセスからの実行といった文脈で評価することが重要です。

## 注意点・補足

- **`Update-Help`**: PowerShellのヘルプファイルは、OSのインストール時点では最小限しか含まれていません。最新かつ完全なヘルプ情報を得るためには、**管理者としてPowerShellを実行し、`Update-Help` コマンドレットを定期的に実行する**必要があります。これにより、インターネット経由で最新のヘルプファイルがダウンロード・インストールされます。

---
[PowerShellインデックスに戻る](../../powershell_index.md)

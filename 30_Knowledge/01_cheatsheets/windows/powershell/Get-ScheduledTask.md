---
tags:
  - 'Get-ScheduledTask'
  - 'powershell'
  - 'cheatsheet'
title: 'Get-ScheduledTask - スケジュールされたタスクを取得する'
summary: 'ローカルまたはリモートコンピュータに登録されているスケジュールされたタスクを取得します。'
related:
  - 'New-ScheduledTask'
  - 'Set-ScheduledTask'
  - 'Enable-ScheduledTask'
  - 'schtasks'
---

# `Get-ScheduledTask` - スケジュールされたタスクを取得する

## 概要

`Get-ScheduledTask` コマンドレットは、タスクスケジューラに登録されているスケジュールされたタスクをオブジェクトとして取得します。このコマンドレットを使うことで、システムに存在する全てのタスクを一覧表示したり、特定の名前やパスに基づいてタスクを検索したりできます。

取得したタスクオブジェクトは、そのタスクのアクション、トリガー、設定などの詳細なプロパティを持っており、パイプラインを通じて他のコマンドレット（`Set-ScheduledTask`, `Stop-ScheduledTask`など）に渡して、タスクを管理できます。

## 類似コマンドレット/コマンドとの差異

| 類似コマンドレット/コマンド | 差異 / 使い分けのポイント |
| :--- | :--- |
| **`schtasks /query` (CMD)** | `schtasks` はタスクの情報を**テキスト**として表示します。`Get-ScheduledTask` は各タスクを**オブジェクト**として返すため、`State` や `Actions` などのプロパティでプログラム的にフィルタリングしたり、結果を他のコマンドレットに渡したりするのが非常に容易です。 |

## よく連携されるコマンドレット

### シナリオ例: 特定の状態のタスクを検索し、その詳細を表示する (インフラ構築・運用視点)

* **目的**: 現在「準備完了 (Ready)」状態ではない、あるいは「無効 (Disabled)」になっている全てのタスクを検索し、そのタスクが実行するコマンドを確認する。
* **連携コマンドレット**: `Where-Object`, `Select-Object`
* **解説**: `Get-ScheduledTask` で全てのタスクオブジェクトを取得し、パイプラインで `Where-Object` に渡して `State` プロパティが `'Ready'` や `'Disabled'` ではないものをフィルタリングします。最後に、`Select-Object -ExpandProperty` を使って、各タスクの `Actions` プロパティ（実行コマンド）を展開して表示します。
* **コマンド例**:

    ```powershell
    # 'Ready' または 'Disabled' 状態ではないタスクを検索
    Get-ScheduledTask | Where-Object { $_.State -ne 'Ready' -and $_.State -ne 'Disabled' }

    # 上記で見つかったタスクの実行コマンドの詳細を表示
    Get-ScheduledTask | Where-Object { $_.State -ne 'Ready' } | Select-Object -ExpandProperty Actions
    ```

## パラメータ説明

| パラメータ | 型 | 説明 |
| :--- | :--- | :--- |
| ⭐ **`-TaskName`** | `String[]` | 取得するタスクの名前を指定します。ワイルドカード (`*`) が使用できます。 |
| **`-TaskPath`** | `String` | タスクが配置されているパスを指定します。（例: `\Microsoft\Windows\Defrag\`） |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: "MyTasks" というフォルダに格納されている全てのスケジュールタスクをリストアップする。
* **組み合わせ**: `Get-ScheduledTask -TaskPath "\MyTasks\"`
* **解説**: `-TaskPath` を使うことで、特定のアプリケーションやプロジェクトに関連するタスクをまとめて管理・監査できます。
* **例**:

    ```powershell
    # '\MyTasks\' パスにある全てのタスクの状態と次の実行時刻を表示
    Get-ScheduledTask -TaskPath "\MyTasks\" | Select-Object TaskName, State, NextRunTime
    ```

### 2. ブルーチーム視点

* **タスク**: **永続化の監査**。システムに登録されている全てのタスクを調査し、不審なものがないかを確認する。
* **組み合わせ**: `Get-ScheduledTask | Select-Object *`
* **解説**: **インシデント対応の基本**。全てのタスクを取得し、その `Actions`（実行コマンド）、`Author`（作成者）、`Triggers`（実行トリガー）などを詳細に調査します。特に、`Actions` が不審なパス（例: `C:\Users\Public`）の実行ファイルを指しているタスクや、異常な頻度で実行されるタスクはマルウェアの可能性があります。
* **例**:

    ```powershell
    # 全てのタスクを取得し、実行されるコマンド（Actions）でグループ化して集計
    Get-ScheduledTask | Select-Object -ExpandProperty Actions | Group-Object -Property Execute
    ```

### 3. レッドチーム視点

* **タスク**: **偵察 (Reconnaissance)** と**権限昇格の機会探索**。
* **組み合わせ**: `Get-ScheduledTask`
* **解説**: 攻撃者は侵入後、`Get-ScheduledTask` を実行して、システムでどのようなタスクが自動実行されているかを把握します。特に、`SYSTEM` などの高い権限で実行されるタスクを探し出し、そのタスクが実行するスクリプトやバイナリに書き込み権限があれば、それを悪意のあるものに置き換えることで**権限昇格**や**永続化**を図ります (Task Hijacking)。
* **例**:

    ```powershell
    # SYSTEM権限で実行されるタスクを探す
    Get-ScheduledTask | Where-Object { $_.Principal.UserId -eq "S-1-5-18" } | Select-Object TaskName, @{Name='Actions';Expression={$_.Actions.Execute}}
    ```

## エラーメッセージとトラブルシューティング

### よくあるエラーメッセージ

1. **エラーメッセージ**: `Get-ScheduledTask : No MSFT_ScheduledTask objects found with property 'TaskName' equal to '...'`
    * **考えられる原因**: 指定した `-TaskName` のタスクが存在しません。
    * **解決策**: タスク名のスペルが正しいか確認してください。`-TaskPath` が正しいかも確認する必要があります。

## セキュリティに関する考慮事項

### 悪用事例

* **偵察と永続化**: レッドチームのシナリオで述べた通り、`Get-ScheduledTask` は攻撃者がシステムの自動化タスクを把握し、それを乗っ取るための偵察に利用されます。`New-ScheduledTask` や `Set-ScheduledTask` と組み合わせることで、攻撃者は自身のペイロードをシステムに永続化させます。

### LOLBASにおける利用例

* **機能**: **偵察 (Reconnaissance)**
* **解説**: `Get-ScheduledTask` は PowerShell の標準機能であり、`schtasks.exe` と同様に、攻撃者が環境に溶け込みながら永続化の機会を探るために利用されます（Living off the Land）。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: 最小権限の原則を徹底し、タスクが実行するスクリプトやバイナリへの書き込み権限を厳格に管理する。
* **Detection (検知)**: Windows イベントログで、**新しいタスクの作成 (イベントID 4698)**、タスクの削除 (4699)、タスクの更新 (4702) を厳重に監視する。PowerShellのスクリプトブロックロギング (イベントID 4104) を有効化し、`Get-ScheduledTask` の実行や、タスクを作成・変更するコマンドレットの実行を記録・監視する。

## 注意点・補足

* **モジュール**: このコマンドレットは `ScheduledTasks` モジュールに含まれています。これは近年のWindowsバージョンにはデフォルトでインストールされています。

---
[PowerShellインデックスに戻る](../../powershell_index.md)

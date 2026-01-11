---
tags:
  - 'Get-WmiObject'
  - 'powershell'
  - 'cheatsheet'
  - 'wmi'
  - 'legacy'
title: 'Get-WmiObject - WMIクラスのインスタンスを取得する (レガシー)'
summary: 'WMI (Windows Management Instrumentation) を通じて、システムのハードウェアやソフトウェアの管理情報を表すクラスのインスタンスを取得します。（非推奨）'
related:
  - 'Get-CimInstance'
  - 'Invoke-WmiMethod'
  - 'wmic'
---

# `Get-WmiObject` - WMIクラスのインスタンスを取得する (レガシー)

## 概要

`Get-WmiObject` コマンドレットは、WMIリポジトリから指定したWMIクラスのインスタンスを取得します。このコマンドレットは、OSの情報、プロセスの詳細、ハードウェア情報など、システムの広範な管理情報をオブジェクトとして取得するために、長年にわたりPowerShellの標準的なツールとして使用されてきました。

**⚠️ 重要**: このコマンドレットは**レガシー (旧式)** と見なされています。PowerShell 3.0以降では、よりモダンで効率的な **`Get-CimInstance`** コマンドレットの使用が強く推奨されます。`Get-WmiObject` は古いDCOMプロトコルを使用するのに対し、`Get-CimInstance` はWinRMプロトコルを使用し、リモート管理においてより優れています。

このコマンドレットには、`gwmi` という便利なエイリアスがデフォルトで設定されています。

## 類似コマンドレット/コマンドとの差異

| 類似コマンドレット/コマンド | 差異 / 使い分けのポイント |
| :--- | :--- |
| ⭐ **`Get-CimInstance`** | `Get-CimInstance` は `Get-WmiObject` の後継です。WinRMプロトコルを使用するため、リモート接続がより高速で、ファイアウォール設定も容易です。構文は非常に似ていますが、**新規のスクリプトでは常に `Get-CimInstance` を使用すべきです**。 |
| **`wmic` (CMD)** | `wmic` はCMD用のレガシーなツールで、出力をテキストとして返します。`Get-WmiObject` は結果を豊かなプロパティを持つPowerShell**オブジェクト**として返すため、パイプラインでの後続処理が圧倒的に強力です。 |

## よく連携されるコマンドレット

### シナリオ例: インストールされているソフトウェアの一覧を取得する (インフラ構築・運用視点)

* **目的**: システムにインストールされているアプリケーションとそのバージョンを一覧表示する。
* **連携コマンドレット**: `Select-Object`
* **解説**: `Win32_Product` クラスはインストールされているソフトウェアの情報を保持しています。これを取得し、`Select-Object` で必要なプロパティだけを抜き出して表示します。（注: `Win32_Product` のクエリは非常に低速で、システムの整合性チェックをトリガーすることがあるため、注意が必要です）
* **コマンド例**:

    ```powershell
    # インストール済みソフトウェアの名前とバージョンを表示
    Get-WmiObject -Class Win32_Product | Select-Object -Property Name, Version
    ```

## パラメータ説明

| パラメータ | 型 | 説明 |
| :--- | :--- | :--- |
| ⭐ **`-Class`** | `String` | 取得するWMIクラスの名前を指定します。（例: `Win32_Process`, `Win32_Service`） |
| ⭐ **`-Filter`** | `String` | WQL (WMI Query Language) の `WHERE` 句に相当する構文を使い、取得するインスタンスをフィルタリングします。 |
| **`-Query`** | `String` | 完全なWQLクエリ (`SELECT * FROM ...`) を文字列として指定します。 |
| **`-ComputerName`** | `String[]` | 情報を取得するリモートコンピュータを指定します。 |
| **`-Namespace`** | `String` | WMI名前空間を指定します。デフォルトは `root\cimv2` です。 |
| **`-Credential`** | `PSCredential` | リモート接続に使用するユーザー資格情報を指定します。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: OSのバージョンやビルド番号、最終起動時刻などを取得する。
* **組み合わせ**: `Get-WmiObject -Class Win32_OperatingSystem`
* **解説**: `Win32_OperatingSystem` クラスには、OSに関する包括的な情報が含まれています。
* **例**:

    ```powershell
    # OSの情報を取得し、主要なプロパティをリスト形式で表示
    Get-WmiObject -Class Win32_OperatingSystem | Select-Object -Property Caption, BuildNumber, LastBootUpTime, NumberOfUsers
    ```

### 2. ブルーチーム視点

* **タスク**: **ライブレスポンス**。不審なプロセスのコマンドライン引数と親プロセスIDを特定する。
* **組み合わせ**: `Get-WmiObject -Class Win32_Process -Filter "..."`
* **解説**: **インシデント調査の基本**。`Get-CimInstance` と同様に、`Win32_Process` クラスの `CommandLine` と `ParentProcessId` プロパティは、マルウェアの活動を追跡する上で極めて重要です。
* **例**:

    ```powershell
    # 'powershell.exe' プロセスのコマンドライン引数を全て表示
    Get-WmiObject -Class Win32_Process -Filter "Name = 'powershell.exe'" | Select-Object -ExpandProperty CommandLine
    ```

### 3. レッドチーム視点

* **タスク**: **偵察 (Reconnaissance)**。ターゲットシステムにインストールされているアンチウイルス製品を特定する。
* **組み合わせ**: `Get-WmiObject -Namespace "root\SecurityCenter2" -Class "AntiVirusProduct"`
* **解説**: 攻撃者は侵入後、WMIを使ってシステム情報を収集します。特に `SecurityCenter2` 名前空間には、登録されているアンチウイルス製品の情報が含まれており、これを照会することで防御機構を特定し、回避策を計画します。
* **例**:

    ```powershell
    # インストールされているアンチウイルス製品の情報を取得
    Get-WmiObject -Namespace "root\SecurityCenter2" -Class "AntiVirusProduct"
    ```

## エラーメッセージとトラブルシューティング

### よくあるエラーメッセージ

1. **エラーメッセージ**: `Get-WmiObject : Invalid class`
    * **考えられる原因**: 指定した `-Class` の名前が存在しません。
    * **解決策**: WMIクラス名が正しいか確認してください。

2. **エラーメッセージ**: `Get-WmiObject : The RPC server is unavailable.`
    * **考えられる原因**: リモートコンピュータを指定したが、DCOM (RPC) 通信がファイアウォールでブロックされているか、WMIサービスが実行されていません。
    * **解決策**: リモートコンピュータのファイアウォールで「Windows Management Instrumentation (WMI)」の受信が許可されていることを確認してください。

## セキュリティに関する考慮事項

### 悪用事例

* **広範囲な偵察**: レッドチームのシナリオで述べた通り、`Get-WmiObject` は攻撃者がシステムのハードウェア、ソフトウェア、セキュリティ設定、実行中のプロセスなど、ほぼ全ての情報を収集するための非常に強力な偵察ツールです。
* **リモート実行**: `Invoke-WmiMethod` と組み合わせることで、リモートのマシン上でプロセスを作成 (`Win32_Process`クラスの`Create`メソッド) するなど、ラテラルムーブメントにも利用されます。

### LOLBASにおける利用例

* **機能**: **偵察 (Reconnaissance)**, **コード実行 (Execution)**
* **解説**: `Get-WmiObject` は PowerShell の標準機能であり、`wmic.exe` と同様に、攻撃者が環境に溶け込みながらシステム情報を収集・操作するために多用されます（Living off the Land）。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: ホストベースのファイアウォールで、信頼できる管理端末からのみWMI (RPC/DCOM) 通信を許可する。
* **Detection (検知)**: PowerShellのスクリプトブロックロギング (イベントID 4104) を有効化し、`Get-WmiObject` の実行、特に不審なクラスの照会やリモート実行を監視する。`Sysmon` でWMIイベントを監視 (イベントID 19, 20, 21) することも非常に有効です。

## 注意点・補足

* **`Get-CimInstance`への移行**: `Get-WmiObject` は機能しますが、古い技術に基づいています。可能な限り、新しい `Get-CimInstance` コマンドレットを使用するようにスクリプトを更新することが推奨されます。

---
[PowerShellインデックスに戻る](../../powershell_index.md)

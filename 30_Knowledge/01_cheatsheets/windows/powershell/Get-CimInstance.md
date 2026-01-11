---
tags:
  - 'Get-CimInstance'
  - 'powershell'
  - 'cheatsheet'
  - 'cim'
  - 'wmi'
title: 'Get-CimInstance - CIMクラスのインスタンスを取得する'
summary: 'CIM (Common Information Model) を通じて、システムのハードウェアやソフトウェアの管理情報を表すクラスのインスタンス（特定のオブジェクト）を取得します。'
related:
  - 'Get-WmiObject'
  - 'Invoke-CimMethod'
  - 'wmic'
---

# `Get-CimInstance` - CIMクラスのインスタンスを取得する

## 概要

`Get-CimInstance` コマンドレットは、CIMサーバーから指定したCIMクラスのインスタンスを取得します。CIMは、WMI (Windows Management Instrumentation) の後継となる、より標準的でクロスプラットフォームな管理情報モデルです。このコマンドレットを使うことで、OSの情報、プロセスの詳細、ハードウェアのシリアル番号など、`wmic` や古い `Get-WmiObject` と同様に、システムのほぼ全ての管理情報をオブジェクトとして取得できます。

PowerShell 3.0以降では、`Get-WmiObject` よりも `Get-CimInstance` の使用が推奨されています。

## 類似コマンドレット/コマンドとの差異

| 類似コマンドレット/コマンド | 差異 / 使い分けのポイント |
| :--- | :--- |
| **`Get-WmiObject`** | `Get-WmiObject` は古いDCOMプロトコルを使用します。`Get-CimInstance` はよりモダンでファイアウォールフレンドリーなWinRMプロトコルを使用するため、リモート管理において優れています。構文や機能は非常に似ていますが、新規のスクリプトでは `Get-CimInstance` を使うべきです。 |
| **`wmic` (CMD)** | `wmic` はCMD用のレガシーなツールです。`Get-CimInstance` は結果を豊かなプロパティを持つPowerShell**オブジェクト**として返すため、パイプラインでのフィルタリング、ソート、加工が圧倒的に強力です。 |

## よく連携されるコマンドレット

### シナリオ例: 実行中のプロセスの所有者を取得する (インフラ構築・運用視点)

* **目的**: 現在実行中の全てのプロセスの名前、プロセスID、およびそのプロセスの所有者を取得する。
* **連携コマンドレット**: `Select-Object`
* **解説**: `Win32_Process` クラスはプロセスの詳細情報を持っていますが、所有者情報を取得するには `GetOwner()` メソッドを呼び出す必要があります。取得したインスタンスに対して、計算プロパティ (`@{...}`) を使ってメソッドを実行し、結果を整形して表示します。
* **コマンド例**:

    ```powershell
    # 全てのプロセスの名前、ID、所有者を取得
    Get-CimInstance -ClassName Win32_Process | Select-Object -Property Name, ProcessId, @{Name="Owner"; Expression={$_.GetOwner().User}}
    ```

## パラメータ説明

| パラメータ | 型 | 説明 |
| :--- | :--- | :--- |
| ⭐ **`-ClassName`** | `String` | 取得するCIMクラスの名前を指定します。（例: `Win32_Process`, `Win32_Service`） |
| ⭐ **`-Filter`** | `String` | WQL (WMI Query Language) を使って、取得するインスタンスをフィルタリングします。サーバーサイドでフィルタリングされるため効率的です。 |
| **`-Query`** | `String` | 完全なWQLクエリを文字列として指定します。 |
| **`-ComputerName`** | `String[]` | 情報を取得するリモートコンピュータを指定します。 |
| **`-Namespace`** | `String` | WMI名前空間を指定します。デフォルトは `root/cimv2` です。 |
| **`-Property`** | `String[]` | 取得するプロパティを限定します。ネットワーク帯域を節約し、パフォーマンスを向上させます。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: システムにインストールされているHotfix（パッチ）の一覧を取得する。
* **組み合わせ**: `Get-CimInstance -ClassName Win32_QuickFixEngineering`
* **解説**: `Win32_QuickFixEngineering` クラスは、`systeminfo` と同様にインストール済みのパッチ情報を保持しています。オブジェクトとして取得できるため、特定のパッチのインストール日 (`InstalledOn`) でソートするなどの加工が容易です。
* **例**:

    ```powershell
    # インストールされているパッチを、インストール日が新しい順に表示
    Get-CimInstance -ClassName Win32_QuickFixEngineering | Sort-Object -Property InstalledOn -Descending
    ```

### 2. ブルーチーム視点

* **タスク**: **ライブレスポンス**。不審なプロセスのコマンドライン引数と親プロセスIDを特定する。
* **組み合わせ**: `Get-CimInstance -ClassName Win32_Process -Filter "..."`
* **解説**: **インシデント調査の基本**。`Win32_Process` クラスは `CommandLine` と `ParentProcessId` という非常に重要なプロパティを持っており、プロセスがどのように起動され、何を実行しているかを特定できます。
* **例**:

    ```powershell
    # Process ID 1234 のプロセスの詳細情報を取得
    Get-CimInstance -ClassName Win32_Process -Filter "ProcessId = 1234" | Select-Object *

    # powershell.exe プロセスのコマンドライン引数を全て表示
    Get-CimInstance -ClassName Win32_Process -Filter "Name = 'powershell.exe'" | Select-Object -ExpandProperty CommandLine
    ```

### 3. レッドチーム視点

* **タスク**: **偵察 (Reconnaissance)**。ターゲットシステムにインストールされているアンチウイルス製品を特定する。
* **組み合わせ**: `Get-CimInstance -Namespace root\SecurityCenter2 -ClassName AntiVirusProduct`
* **解説**: 攻撃者は侵入後、WMI/CIMを使ってシステム情報を収集します。特に `SecurityCenter2` 名前空間には、登録されているアンチウイルス製品の情報が含まれており、これを照会することで防御機構を特定し、回避策を計画します。
* **例**:

    ```powershell
    # インストールされているアンチウイルス製品の情報を取得
    Get-CimInstance -Namespace root\SecurityCenter2 -ClassName AntiVirusProduct | Select-Object DisplayName, PathToSignedProductExe
    ```

## エラーメッセージとトラブルシューティング

### よくあるエラーメッセージ

1. **エラーメッセージ**: `Get-CimInstance: The WS-Management service cannot process the request. The CIM namespace ... was not found.`
    * **考えられる原因**: 存在しない `-ClassName` や `-Namespace` を指定しました。
    * **解決策**: クラス名や名前空間のスペルが正しいか確認してください。

## セキュリティに関する考慮事項

### 悪用事例

* **広範囲な偵察**: レッドチームのシナリオで述べた通り、`Get-CimInstance` は攻撃者がシステムのハードウェア、ソフトウェア、セキュリティ設定、実行中のプロセス、ログインユーザーなど、ほぼ全ての情報を収集するための非常に強力な偵察ツールです。
* **リモート実行**: `Invoke-CimMethod` と組み合わせることで、リモートのマシン上でプロセスを作成 (`Win32_Process`クラスの`Create`メソッド) するなど、ラテラルムーブメントにも利用されます。

### LOLBASにおける利用例

* **機能**: **偵察 (Reconnaissance)**, **コード実行 (Execution)**
* **解説**: `Get-CimInstance` は PowerShell の標準機能であり、`wmic.exe` と同様に、攻撃者が環境に溶け込みながらシステム情報を収集・操作するために多用されます（Living off the Land）。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: ホストベースのファイアウォールで、信頼できる管理端末からのみWinRM (TCP 5985/5986) 接続を許可する。
* **Detection (検知)**: PowerShellのスクリプトブロックロギング (イベントID 4104) を有効化し、`Get-CimInstance` の実行、特に不審なクラス (例: `AntiVirusProduct`) の照会や、`Invoke-CimMethod` によるプロセス作成などを監視する。`Sysmon` でWMIイベントを監視 (イベントID 19, 20, 21) することも非常に有効です。

## 注意点・補足

* **`wmic`からの移行**: `wmic process list full` は `Get-CimInstance -ClassName Win32_Process | Select-Object *` に、`wmic qfe get hotfixid` は `Get-CimInstance -ClassName Win32_QuickFixEngineering | Select-Object -Property HotFixID` に、といった形で対応させることができます。

---
[PowerShellインデックスに戻る](../../powershell_index.md)

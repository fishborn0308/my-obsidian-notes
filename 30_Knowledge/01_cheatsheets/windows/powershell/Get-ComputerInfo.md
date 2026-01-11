---
tags:
  - 'Get-ComputerInfo'
  - 'powershell'
  - 'cheatsheet'
title: 'Get-ComputerInfo - システムの総合的な情報を取得する'
summary: 'OS、ハードウェア、BIOSなど、システムの包括的なプロパティをまとめたオブジェクトを取得します。'
related:
  - 'systeminfo'
  - 'Get-CimInstance'
---

# `Get-ComputerInfo` - システムの総合的な情報を取得する

## 概要

`Get-ComputerInfo` コマンドレットは、ローカルコンピュータのOS、コンピュータの役割、プロセッサ、BIOS、メモリなど、多岐にわたるシステム情報を集約したオブジェクトを取得します。このコマンドレットは、CMDの `systeminfo` コマンドが返す情報を、PowerShellで扱いやすいオブジェクト形式で提供するものです。スクリプトによるインベントリ収集や、システムの基本構成の確認に非常に便利です。

## 類似コマンドレット/コマンドとの差異

| 類似コマンドレット/コマンド | 差異 / 使い分けのポイント |
| :--- | :--- |
| **`systeminfo` (CMD)** | `systeminfo` は情報を**テキスト**として表示します。`Get-ComputerInfo` は**オブジェクト**として返すため、`$info.OsName` のように特定のプロパティに簡単にアクセスしたり、パイプラインで後続処理を行ったりすることが可能です。 |
| **`Get-CimInstance`** | `Get-CimInstance` は、`Win32_OperatingSystem` や `Win32_ComputerSystem` といった特定のWMI/CIMクラスを指定して情報を取得します。`Get-ComputerInfo` は、これらの複数のクラスから情報を自動的に収集し、一つのまとまったオブジェクトとして返してくれるラッパー的なコマンドレットです。 |

## よく連携されるコマンドレット

### シナリオ例: 複数のサーバーから特定の情報を収集する (インフラ構築・運用視点)

* **目的**: サーバーリストにある全てのサーバーから、OS名、インストールされている物理メモリの容量、およびWindowsがインストールされた日時を取得する。
* **連携コマンドレット**: `Invoke-Command`, `Select-Object`
* **解説**: `Invoke-Command` を使って複数のリモートコンピュータ上で `Get-ComputerInfo` を実行し、返ってきたオブジェクトから `Select-Object` を使って必要なプロパティだけを抽出します。
* **コマンド例**:

    ```powershell
    # 'servers.txt' に記載されたサーバーリストに対して実行
    $servers = Get-Content -Path .\servers.txt
    Invoke-Command -ComputerName $servers -ScriptBlock {
        Get-ComputerInfo | Select-Object -Property CsName, OsName, RamSizeGB, OsInstallDate
    }
    ```

## パラメータ説明

| パラメータ | 型 | 説明 |
| :--- | :--- | :--- |
| ⭐ **`-Property`** | `String[]` | 取得する情報のカテゴリを指定します。ワイルドカード (`*`) が使用できます。（例: `"Cs*"` はコンピュータシステム情報のみを取得） |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: 新しく構築したサーバーの基本構成情報を確認し、レポートを作成する。
* **組み合わせ**: `Get-ComputerInfo | Select-Object *`
* **解説**: `Select-Object *` を使うと、`Get-ComputerInfo` が収集できる全てのプロパティをリスト形式で表示できます。これにより、システムの全体像を詳細に把握できます。
* **例**:

    ```powershell
    # 現在のコンピュータの全ての情報をリスト形式で表示
    Get-ComputerInfo | Select-Object *
    ```

### 2. ブルーチーム視点

* **タスク**: **インシデントレスポンスの初期対応**。侵害されたホストのシステム構成を正確に記録する。
* **組み合わせ**: `Get-ComputerInfo`
* **解説**: `systeminfo` と同様に、調査対象ホストのOSバージョン (`OsVersion`)、インストール日時 (`OsInstallDate`)、最終起動日時 (`OsLastBootUpTime`) などをオブジェクトとして取得し、証拠として保存します。
* **例**:

    ```powershell
    # システム情報を取得し、XML形式でファイルに保存（オブジェクトの構造を維持）
    Get-ComputerInfo | Export-Clixml -Path C:\Forensics\computer_info.xml
    ```

### 3. レッドチーム視点

* **タスク**: **偵察 (Reconnaissance)**。侵入したホストのOSバージョンやアーキテクチャを特定し、利用可能な脆弱性を探す。
* **組み合わせ**: `(Get-ComputerInfo).OsVersion`
* **解説**: 攻撃者は侵入後、`Get-ComputerInfo` を実行してOSの詳細なビルド番号 (`OsBuildNumber`) やアーキテクチャ (`OsArchitecture`) を特定します。この情報を基に、権限昇格に利用できるカーネルエクスプロイトなどの既知の脆弱性を検索します。
* **例**:

    ```powershell
    # OSのバージョン情報のみを取得
    Get-ComputerInfo -Property OsVersion

    # または特定のプロパティに直接アクセス
    (Get-ComputerInfo).OsVersion
    ```

## エラーメッセージとトラブルシューティング

### よくあるエラーメッセージ

* `Get-ComputerInfo` は主にローカルマシンに対して実行されるため、エラーは稀です。リモートで `Invoke-Command` と共に使用する場合は、`Enter-PSSession` と同様のWinRM関連のエラーが発生する可能性があります。

## セキュリティに関する考慮事項

### 悪用事例

* **偵察 (Reconnaissance)**: レッドチームのシナリオで述べた通り、`Get-ComputerInfo` は攻撃者が侵入先システムの詳細な構成（特にOSのパッチレベル）を把握するための重要な情報源です。これにより、効果的な権限昇格手法を選択することが可能になります。

### LOLBASにおける利用例

* **機能**: **偵察 (Reconnaissance)**
* **解説**: `Get-ComputerInfo` は PowerShell の標準機能であり、`systeminfo.exe` と同様に、攻撃者が環境に溶け込みながらシステム情報を収集するために利用されます（Living off the Land）。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: 定期的にセキュリティパッチを適用し、システムを最新の状態に保つことが、このコマンドレットによって得られる情報が悪用されるリスクを低減する最も効果的な対策です。
* **Detection (検知)**: PowerShellのスクリプトブロックロギング (イベントID 4104) を有効化し、`Get-ComputerInfo` の実行、特に他の偵察コマンド (`Get-Process`, `Get-NetIPAddress`など) と連続して実行されるような挙動を監視する。

## 注意点・補足

* **情報の選択**: `Get-ComputerInfo` は非常に多くの情報を返すため、パイプラインで `Select-Object` や `Format-List` を使って、必要なプロパティだけを選択・表示するのが一般的です。

---
[PowerShellインデックスに戻る](../../powershell_index.md)

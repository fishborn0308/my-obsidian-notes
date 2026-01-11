---
tags:
  - 'Get-NetAdapter'
  - 'powershell'
  - 'cheatsheet'
title: 'Get-NetAdapter - ネットワークアダプターを取得する'
summary: 'システムに存在する物理および仮想のネットワークアダプター（NIC）のプロパティを取得します。'
related:
  - 'Get-NetIPAddress'
  - 'Get-NetIPConfiguration'
  - 'ipconfig'
---

# `Get-NetAdapter` - ネットワークアダプターを取得する

## 概要

`Get-NetAdapter` コマンドレットは、コンピュータにインストールされている全てのネットワークアダプターのプロパティ（名前、MACアドレス、ステータス、リンク速度など）を取得します。物理的なイーサネットカード、Wi-Fiアダプターだけでなく、仮想スイッチやVPNアダプターなどの仮想インターフェイスも含まれます。

このコマンドレットは、ネットワークのインベントリ収集や、アダプターの状態を確認する基本的なトラブルシューティングに使用されます。

## 類似コマンドレット/コマンドとの差異

| 類似コマンドレット/コマンド | 差異 / 使い分けのポイント |
| :--- | :--- |
| **`ipconfig /all` (CMD)** | `ipconfig` はIPアドレス設定を含む包括的な情報を**テキスト**で表示します。`Get-NetAdapter` はアダプターの**物理的なプロパティ**に焦点を当てた**オブジェクト**を返すため、`Status` や `MacAddress` でのフィルタリングが容易です。 |
| **`Get-NetIPConfiguration`** | `Get-NetIPConfiguration` は、アダプターにバインドされた**IPアドレス関連の設定**（IPアドレス、DNSサーバー、ゲートウェイ）を取得します。`Get-NetAdapter` は、アダプター**自体**のハードウェア寄りの情報を取得します。 |

## よく連携されるコマンドレット

### シナリオ例: 有効な物理アダプターのIP設定を取得する (インフラ構築・運用視点)

* **目的**: システム上で現在有効 (`Up`) になっている物理的なネットワークアダプターを特定し、それらに割り当てられているIPv4アドレスを取得する。
* **連携コマンドレット**: `Get-NetIPAddress`
* **解説**: `Get-NetAdapter` で物理アダプターをフィルタリングして取得し、その結果をパイプラインで `Get-NetIPAddress` に渡します。`Get-NetIPAddress` は渡されたアダプターオブジェクトに関連付けられたIPアドレス情報のみを返します。
* **コマンド例**:

    ```powershell
    # 'Up' 状態の物理NICを取得し、そのIPv4アドレスを表示
    Get-NetAdapter -Physical -Status 'Up' | Get-NetIPAddress -AddressFamily IPv4
    ```

## パラメータ説明

| パラメータ | 型 | 説明 |
| :--- | :--- | :--- |
| **`-Name`** | `String[]` | 取得するアダプターの名前を指定します。ワイルドカード (`*`) が使用できます。 |
| **`-InterfaceIndex`** | `UInt32[]` | アダプターのインターフェイスインデックス番号を指定します。 |
| ⭐ **`-Physical`** | `SwitchParameter` | 物理ネットワークアダプターのみを取得します。 |
| ⭐ **`-IncludeHidden`** | `SwitchParameter` | 通常は表示されない非表示のアダプターも取得します。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: サーバー上の全てのアダプターの名前、MACアドレス、現在のリンク状態を一覧表示する。
* **組み合わせ**: `Get-NetAdapter | Format-Table Name, MacAddress, Status`
* **解説**: `Format-Table` を使って、取得したアダプターオブジェクトから必要なプロパティだけを見やすい表形式で表示します。
* **例**:

    ```powershell
    # 全てのアダプターの主要なプロパティを表形式で表示
    Get-NetAdapter | Format-Table -Property Name, MacAddress, Status, LinkSpeed
    ```

### 2. ブルーチーム視点

* **タスク**: **ライブレスポンス**。侵害されたホストのネットワークインターフェイス情報を全て記録する。
* **組み合わせ**: `Get-NetAdapter -IncludeHidden | Select-Object *`
* **解説**: `-IncludeHidden` を付けて、攻撃者が隠蔽のために作成した可能性のある仮想アダプターなども含めて、全てのアダプター情報を取得します。`Select-Object *` で全プロパティを記録し、後のフォレンジック分析に備えます。
* **例**:

    ```powershell
    # 全てのアダプター（隠し含む）の詳細情報をXML形式で保存
    Get-NetAdapter -IncludeHidden | Select-Object * | Export-Clixml -Path C:\Forensics\net_adapters.xml
    ```

### 3. レッドチーム視点

* **タスク**: **偵察 (Reconnaissance)**。侵入したホストのネットワーク構成を把握する。
* **組み合わせ**: `Get-NetAdapter`
* **解説**: 攻撃者は侵入後、`Get-NetAdapter` を実行して、そのホストがいくつのネットワークインターフェイスを持っているか、仮想ネットワーク（Hyper-Vなど）に接続されているか、どのような名前が付けられているか（例: "DMZ_NIC", "CORP_LAN"）を調査します。これにより、ホストの役割やネットワークトポロジーを推測し、ラテラルムーブメントの計画を立てます。
* **例**:

    ```powershell
    # ネットワークアダプターを偵察
    Get-NetAdapter
    ```

## エラーメッセージとトラブルシューティング

### よくあるエラーメッセージ

1. **エラーメッセージ**: `Get-NetAdapter : No MSFT_NetAdapter objects found with property 'Name' equal to '...'`
    * **考えられる原因**: 指定した `-Name` のネットワークアダプターが存在しません。
    * **解決策**: アダプター名のスペルが正しいか確認してください。引数を指定せずに `Get-NetAdapter` を実行して、存在するアダプターの一覧を確認するのが有効です。

## セキュリティに関する考慮事項

### 悪用事例

* **偵察 (Reconnaissance)**: レッドチームのシナリオで述べた通り、`Get-NetAdapter` は攻撃者が侵入先システムのネットワーク構成を詳細に把握するための重要な情報源です。マルチホーム（複数のNICを持つ）サーバーは、異なるセキュリティゾーンへの足がかりとなる可能性があるため、特に重要な偵察対象となります。

### LOLBASにおける利用例

* **機能**: **偵察 (Reconnaissance)**
* **解説**: `Get-NetAdapter` は PowerShell の標準機能であり、攻撃者が環境に溶け込みながらネットワーク構成を調査するために利用されます（Living off the Land）。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: -
* **Detection (検知)**: PowerShellのスクリプトブロックロギング (イベントID 4104) を有効化し、`Get-NetAdapter` の実行を記録・監視する。特に、他の偵察コマンド (`Get-NetIPAddress`, `Get-Process`など) と連続して実行される場合は、攻撃の兆候である可能性があります。

## 注意点・補足

* **モジュール**: このコマンドレットは `NetAdapter` モジュールに含まれています。これは近年のWindowsバージョンにはデフォルトでインストールされています。

---
[PowerShellインデックスに戻る](../../powershell_index.md)

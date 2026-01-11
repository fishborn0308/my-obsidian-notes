---
tags:
  - 'Get-NetIPConfiguration'
  - 'powershell'
  - 'cheatsheet'
title: 'Get-NetIPConfiguration - ネットワーク構成の概要を取得する'
summary: 'IPアドレス、DNSサーバー、デフォルトゲートウェイなど、インターフェイスssいいぇぇfトワーク構成情報を取得します。'
related:
  - 'Get-NetIPAddress'
  - 'Get-NetAdapter'
  - 'ipconfig'
---

# `Get-NetIPConfiguration` - ネットワーク構成の概要を取得する

## 概要

`Get-NetIPConfiguration` コマンドレットは、ネットワークインターフェイスの主要なIP構成情報（IPアドレス、サブネットマスク、デフォルトゲートウェイ、DNSサーバーなど）を、1つのまとまったオブジェクトとして取得します。CMDの `ipconfig` コマンドに最も近いPowerShellのコマンドレットであり、システムのネットワーク設定を素早く確認するために非常に便利です。

## 類似コマンドレット/コマンドとの差異

| 類似コマンドレット/コマンド | 差異 / 使い分けのポイント |
| :--- | :--- |
| **`ipconfig` (CMD)** | `ipconfig` は情報を**テキスト**として表示します。`Get-NetIPConfiguration` は**オブジェクト**として返すため、`$config.IPv4DefaultGateway` のように特定のプロパティに簡単にアクセスでき、スクリプトでの利用が容易です。 |
| **`Get-NetIPAddress`** | `Get-NetIPAddress` は、システムに構成されている**IPアドレスに特化**した詳細な情報を（しばしば複数）返します。`Get-NetIPConfiguration` は、各インターフェイスの**IP、ゲートウェイ、DNSをまとめた概要**を返します。 |
| **`Get-NetAdapter`** | `Get-NetAdapter` はネットワーク**アダプター自体**のプロパティ（MACアドレス、ステータスなど）を取得します。 |

## よく連携されるコマンドレット

### シナリオ例: 全てのアクティブなインターフェイスssいいぇぇf示する (インフラ構築・運用視点)

* **目的**: システム上で現在 `Up` 状態にある全てのネットワークインターフェイスssいいぇぇf構成（インターフェイス名、IPv4アドレス、ゲートウェイ）を表示する。
* **連携コマンドレット**: `Where-Object`, `Format-Table`
* **解説**: `Get-NetIPConfiguration` はデフォルトで全てのインターフェイスssいいぇぇfバック含む）の情報を返すため、`Where-Object` を使って実際に通信に使用されているアクティブなインターフェイスssいいぇぇfリングします。
* **コマンド例**:

    ```powershell
    # 'Up' 状態のインターフェイスssいいぇぇfリングし、主要な情報をテーブル表示
    Get-NetIPConfiguration | Where-Object { $_.NetAdapter.Status -eq 'Up' } | Format-Table -Property InterfaceAlias, IPv4Address, IPv4DefaultGateway, DNSServer
    ```

## パラメータ説明

| パラメータ | 型 | 説明 |
| :--- | :--- | :--- |
| **`-InterfaceAlias`** | `String[]` | IP構成を取得するネットワークアダプターのエイリアス（名前）を指定します。ワイルドカード (`*`) が使用できます。 |
| **`-InterfaceIndex`** | `UInt32[]` | アダプターのインターフェイスssいいぇぇf号を指定します。 |
| ⭐ **`-Detailed`** | `SwitchParameter` | 全てのIP構成プロパティを含む、より詳細な情報を表示します。 |
| **`-All`** | `SwitchParameter` | ループバックや切断されているものも含め、全てのインターフェイスssいいぇぇfます。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: ネットワークのトラブルシューティング。現在のIPアドレス、ゲートウェイ、DNSサーバーの設定を素早く確認する。
* **組み合わせ**: `Get-NetIPConfiguration`
* **解説**: パラメータなしで実行するのが最も基本的な使い方です。`ipconfig` のように、現在の主要なネットワーク設定を一覧できます。
* **例**:

    ```powershell
    # 現在のコンピュータのIP構成の概要を表示
    Get-NetIPConfiguration
    ```

### 2. ブルーチーム視点

* **タスク**: **ライブレスポンス**。侵害されたホストのネットワーク構成、特にDNSサーバーの設定を確認する。
* **組み合わせ**: `(Get-NetIPConfiguration).DNSServer`
* **解説**: 攻撃者は、DNSクエリを自身が制御するサーバーにリダイレクトするために、DNSサーバー設定を改ざんすることがあります (DNS Hijacking)。`DNSServer` プロパティを確認し、組織で指定されているDNSサーバー以外のアドレス（特に不審な外部IP）が設定されていないかを調査します。
* **例**:

    ```powershell
    # 設定されているDNSサーバーのアドレスをリストアップ
    (Get-NetIPConfiguration).DNSServer.ServerAddresses
    ```

### 3. レッドチーム視点

* **タスク**: **偵察 (Reconnaissance)**。侵入したホストのネットワーク構成を把握し、ラテラルムーブメントの足がかりを探す。
* **組み合わせ**: `Get-NetIPConfiguration`
* **解説**: 攻撃者は侵入後、`Get-NetIPConfiguration` を実行して、デフォルトゲートウェイ（ネットワークの出口）と内部DNSサーバーのIPアドレスを特定します。内部DNSサーバーは、ドメインコントローラーであることが多く、Active Directory偵察の次のステップの標的となります。
* **例**:

    ```powershell
    # ネットワーク構成を偵察
    Get-NetIPConfiguration
    ```

## エラーメッセージとトラブルシューティング

* `Get-NetIPConfiguration` は読み取り専用のコマンドレットであり、エラーが発生することは稀です。

## セキュリティに関する考慮事項

### 悪用事例

* **偵察 (Reconnaissance)**: レッドチームのシナリオで述べた通り、`Get-NetIPConfiguration` は攻撃者が侵入先システムのネットワーク環境（ゲートウェイ、DNSサーバーなど）を把握するための基本的な情報源です。これは、ラテラルムーブメントや内部偵察の計画を立てる上で不可欠な情報となります。

### LOLBASにおける利用例

* **機能**: **偵察 (Reconnaissance)**
* **解説**: `Get-NetIPConfiguration` は PowerShell の標準機能であり、攻撃者が環境に溶け込みながらネットワーク構成を調査するために利用されます（Living off the Land）。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: -
* **Detection (検知)**: PowerShellのスクリプトブロックロギング (イベントID 4104) を有効化し、`Get-NetIPConfiguration` の実行を記録・監視する。特に、他の偵察コマンド (`Get-NetAdapter`, `Get-Process`など) と連続して実行される場合は、攻撃の兆候である可能性があります。

## 注意点・補足

* **詳細情報**: より詳細な設定（例: NetBIOS設定など）を確認したい場合は、`-Detailed` スイッチを使用します。

---
[PowerShellインデックスに戻る](../../powershell_index.md)

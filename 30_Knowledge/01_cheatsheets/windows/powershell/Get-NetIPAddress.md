---
tags:
  - 'Get-NetIPAddress'
  - 'powershell'
  - 'cheatsheet'
title: 'Get-NetIPAddress - IPアドレスの構成情報を取得する'
summary: 'システムに構成されているIPアドレスとそのプロパティ（インターフェース、プレフィックス長など）を取得します。'
related:
  - 'Get-NetAdapter'
  - 'Get-NetRoute'
  - 'Get-NetIPConfiguration'
  - 'ipconfig'
---

# `Get-NetIPAddress` - IPアドレスの構成情報を取得する

## 概要

`Get-NetIPAddress` コマンドレットは、コンピュータのネットワークインターフェイスに構成されているIPアドレスに関する詳細な情報を取得します。IPv4アドレスとIPv6アドレスの両方を取得でき、各アドレスのプレフィックス長（サブネットマスク）、インターフェイスインデックス、アドレスの状態（`Preferred`, `Deprecated`など）といったプロパティをオブジェクトとして返します。

このコマンドレットは、システムのIP構成を監査したり、特定のアドレスがどのインターフェイスに割り当てられているかをスクリプトで判別したりする際に使用されます。

## 類似コマンドレット/コマンドとの差異

| 類似コマンドレット/コマンド | 差異 / 使い分けのポイント |
| :--- | :--- |
| **`ipconfig` (CMD)** | `ipconfig` はネットワーク構成の概要を**テキスト**で表示します。`Get-NetIPAddress` は各IPアドレスを個別の**オブジェクト**として返すため、`AddressFamily` や `PrefixLength` でのフィルタリング、ソートが非常に容易です。 |
| **`Get-NetAdapter`** | `Get-NetAdapter` はネットワーク**アダプター自体**のプロパティ（MACアドレス、ステータスなど）を取得します。`Get-NetIPAddress` は、そのアダプターに**割り当てられているIPアドレス**の情報を取得します。 |
| **`Get-NetIPConfiguration`** | `Get-NetIPConfiguration` は、IPアドレス、DNSサーバー、デフォルトゲートウェイを**一つのオブジェクトにまとめて**表示します。`Get-NetIPAddress` は**IPアドレスのみに特化**した詳細な情報を複数返します。 |

## よく連携されるコマンドレット

### シナリオ例: 特定のアダプターのIPv4アドレスを取得する (インフラ構築・運用視点)

* **目的**: "Ethernet0" という名前のネットワークアダプターに割り当てられているIPv4アドレスを特定する。
* **連携コマンドレット**: `Get-NetAdapter`
* **解説**: まず `Get-NetAdapter` で目的のアダプターオブジェクトを取得し、その結果をパイプラインで `Get-NetIPAddress` に渡します。`-AddressFamily` パラメータでIPv4に限定することで、必要な情報だけを正確に取得できます。
* **コマンド例**:

    ```powershell
    # 'Ethernet0' アダプターに設定されているIPv4アドレスを取得
    Get-NetAdapter -Name "Ethernet0" | Get-NetIPAddress -AddressFamily IPv4
    ```

## パラメータ説明

| パラメータ | 型 | 説明 |
| :--- | :--- | :--- |
| **`-InterfaceAlias`** | `String[]` | IPアドレスを取得するネットワークアダプターのエイリアス（名前）を指定します。ワイルドカード (`*`) が使用できます。 |
| **`-InterfaceIndex`** | `UInt32[]` | アダプターのインターフェイスインデックス番号を指定します。 |
| ⭐ **`-AddressFamily`** | `AddressFamily` | IPアドレスのファミリーを指定します (`IPv4` または `IPv6`)。 |
| **`-PrefixLength`** | `Byte[]` | プレフィックス長（サブネットマスクのビット数）でフィルタリングします。 |
| **`-IPAddress`** | `String[]` | 特定のIPアドレスを指定して取得します。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: サーバーに設定されている非ループバックのIPv4アドレスをすべてリストアップする。
* **組み合わせ**: `Get-NetIPAddress -AddressFamily IPv4 | Where-Object { $_.AddressFamily -eq 'IPv4' -and $_.IPAddress -ne '127.0.0.1' }`
* **解説**: `-AddressFamily` でIPv4に絞り込み、`Where-Object` でループバックアドレス (`127.0.0.1`) を除外します。
* **例**:

    ```powershell
    # 実稼働しているIPv4アドレスとそのプレフィックス長を表示
    Get-NetIPAddress | Where-Object { $_.AddressFamily -eq 'IPv4' -and $_.InterfaceAlias -notlike "Loopback*" } | Select-Object IPAddress, PrefixLength, InterfaceAlias
    ```

### 2. ブルーチーム視点

* **タスク**: **ライブレスポンス**。侵害されたホストのIPアドレス構成を全て記録する。
* **組み合わせ**: `Get-NetIPAddress`
* **解説**: `ipconfig` と同様に、調査対象ホストのIPアドレス構成を正確に記録します。オブジェクトとして取得することで、後の分析スクリプトで扱いやすくなります。APIPAアドレス (`169.254.x.x`) が割り当てられていないかなどの確認も重要です。
* **例**:

    ```powershell
    # 全てのIPアドレス情報を取得し、XML形式でファイルに保存
    Get-NetIPAddress | Export-Clixml -Path C:\Forensics\net_ip_addresses.xml
    ```

### 3. レッドチーム視点

* **タスク**: **偵察 (Reconnaissance)**。侵入したホストのIPアドレスを特定し、ネットワークセグメントを把握する。
* **組み合わせ**: `Get-NetIPAddress`
* **解説**: 攻撃者は侵入後、`Get-NetIPAddress` や `ipconfig` を実行して、現在のホストのIPアドレスとサブネットマスクを特定します。これにより、自分がどのネットワークセグメントにいるのかを把握し、同じセグメント内の他のホストをスキャンするための起点とします。
* **例**:

    ```powershell
    # 偵察のため、主要なIPアドレス情報を取得
    (Get-NetIPAddress -AddressFamily IPv4).IPAddress
    ```

## エラーメッセージとトラブルシューティング

* `Get-NetIPAddress` は読み取り専用のコマンドレットであり、エラーが発生することは稀です。

## セキュリティに関する考慮事項

### 悪用事例

* **偵察 (Reconnaissance)**: レッドチームのシナリオで述べた通り、`Get-NetIPAddress` は攻撃者が侵入先システムのネットワーク構成を把握するための基本的な情報源です。IPアドレスとサブネットマスクの情報は、ラテラルムーブメント（横展開）の計画を立てる上で不可欠です。

### LOLBASにおける利用例

* **機能**: **偵察 (Reconnaissance)**
* **解説**: `Get-NetIPAddress` は PowerShell の標準機能であり、攻撃者が環境に溶け込みながらネットワーク構成を調査するために利用されます（Living off the Land）。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: -
* **Detection (検知)**: PowerShellのスクリプトブロックロギング (イベントID 4104) を有効化し、`Get-NetIPAddress` の実行を記録・監視する。特に、他の偵察コマンド (`Get-NetAdapter`, `Get-Process`など) と連続して実行される場合は、攻撃の兆候である可能性があります。

## 注意点・補足

* **モジュール**: このコマンドレットは `NetTCPIP` モジュールに含まれています。これは近年のWindowsバージョンにはデフォルトでインストールされています。

---
[PowerShellインデックスに戻る](../../powershell_index.md)

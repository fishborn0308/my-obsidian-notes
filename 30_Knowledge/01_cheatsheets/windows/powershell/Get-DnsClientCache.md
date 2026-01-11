---
tags:
  - 'Get-DnsClientCache'
  - 'powershell'
  - 'cheatsheet'
title: 'Get-DnsClientCache - DNSクライアントキャッシュの内容を取得する'
summary: 'ローカルコンピュータのDNSクライアントにキャッシュされている名前解決エントリを取得します。'
related:
  - 'Clear-DnsClientCache'
  - 'Resolve-DnsName'
  - 'ipconfig /displaydns'
---

# `Get-DnsClientCache` - DNSクライアントキャッシュの内容を取得する

## 概要

`Get-DnsClientCache` コマンドレットは、ローカルのDNSクライアントキャッシュに保存されているすべてのDNSレコードを表示します。DNSキャッシュは、一度解決したドメイン名とIPアドレスのペアを一時的に保存しておくことで、次回同じドメイン名にアクセスする際の応答速度を向上させる仕組みです。

このコマンドレットは、名前解決のトラブルシューティングや、コンピュータが最近どのようなホストと通信したかを調査する際に使用されます。

## 類似コマンドレット/コマンドとの差異

| 類似コマンドレット/コマンド | 差異 / 使い分けのポイント |
| :--- | :--- |
| **`ipconfig /displaydns` (CMD)** | `ipconfig /displaydns` はキャッシュの内容を**テキスト**として表示します。`Get-DnsClientCache` は各キャッシュエントリを**オブジェクト**として返すため、`Where-Object` でステータスをフィルタリングしたり、`Sort-Object` でソートしたりといった、プログラム的な操作が可能です。 |
| **`Clear-DnsClientCache`** | `Clear-DnsClientCache` はDNSキャッシュの内容を**消去**します。`Get-DnsClientCache` は内容を**表示**します。 |

## よく連携されるコマンドレット

### シナリオ例: 特定のドメイン名のキャッシュエントリを検索する (インフラ構築・運用視点)

* **目的**: DNSキャッシュの中から、"example.com" に関連するエントリだけをフィルタリングして表示する。
* **連携コマンドレット**: `Where-Object`
* **解説**: `Get-DnsClientCache` で全てのキャッシュエントリを取得し、その結果をパイプラインで `Where-Object` に渡します。`Entry` プロパティ（ドメイン名）に "example.com" が含まれるものだけを絞り込みます。
* **コマンド例**:

    ```powershell
    # DNSキャッシュから "example.com" を含むエントリを検索
    Get-DnsClientCache | Where-Object { $_.Entry -like "*example.com*" }
    ```

## パラメータ説明

| パラメータ | 型 | 説明 |
| :--- | :--- | :--- |
| **`-Name`** | `String[]` | 取得するキャッシュエントリの名前（ドメイン名）を指定します。ワイルドカード (`*`) が使用できます。 |
| **`-Type`** | `DnsRecordType` | レコードのタイプを指定します (`A`, `AAAA`, `CNAME`, `MX` など)。 |
| **`-Status`** | `DnsCacheEntryStatus` | エントリのステータスでフィルタリングします (`Success`, `Pending`, `Error` など)。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: 名前解決のトラブルシューティング。特定のホスト名がどのIPアドレスに解決されているか、キャッシュ上の情報を確認する。
* **組み合わせ**: `Get-DnsClientCache -Name <hostname>`
* **解説**: DNSサーバーの設定変更が反映されない場合などに、古い情報がキャッシュに残っていないかを確認します。
* **例**:

    ```powershell
    # 'server01.corp.local' のキャッシュエントリを確認
    Get-DnsClientCache -Name "server01.corp.local"
    ```

### 2. ブルーチーム視点

* **タスク**: **ライブレスポンス**。侵害されたホストのDNSキャッシュを調査し、悪意のあるドメインへの接続痕跡がないかを確認する。
* **組み合わせ**: `Get-DnsClientCache`
* **解説**: マルウェアはC2サーバーとの通信にドメイン名を使用することが多いです。DNSキャッシュを調べることで、そのホストが最近どのようなドメインに名前解決を試みたかを知ることができ、C2サーバーのドメインや、フィッシングサイトのドメインを発見する手がかりとなります。
* **例**:

    ```powershell
    # DNSキャッシュの全エントリを取得し、分析のためにCSVファイルに出力
    Get-DnsClientCache | Export-Csv -Path C:\Forensics\dns_cache.csv -NoTypeInformation
    ```

### 3. レッドチーム視点

* **タスク**: **偵察 (Reconnaissance)**。侵入したホストのDNSキャッシュを調査し、内部ネットワークのサーバー（ドメインコントローラー、ファイルサーバーなど）を特定する。
* **組み合わせ**: `Get-DnsClientCache`
* **解説**: 攻撃者は侵入後、DNSキャッシュを調べることで、そのユーザーやシステムが普段どのようなサーバーと通信しているかを把握します。`_ldap._tcp.dc._msdcs.<domain>` のようなSRVレコードのキャッシュが見つかれば、ドメインコントローラーの存在を特定できます。これはラテラルムーブメントの標的を探すための重要な情報収集活動です。
* **例**:

    ```powershell
    # DNSキャッシュを全て表示して内部のサーバー名を偵察
    Get-DnsClientCache
    ```

## エラーメッセージとトラブルシューティング

* `Get-DnsClientCache` は読み取り専用のコマンドレットであり、管理者権限がなくても実行できるため、エラーが発生することは稀です。

## セキュリティに関する考慮事項

### 悪用事例

* **内部ネットワークの偵察**: レッドチームのシナリオで述べた通り、`Get-DnsClientCache` は攻撃者が侵入先ネットワークの構造や、重要なサーバーの場所を特定するための非常に価値の高い偵察ツールです。

### LOLBASにおける利用例

* **機能**: **偵察 (Reconnaissance)**
* **解説**: `Get-DnsClientCache` は PowerShell の標準機能であり、攻撃者が環境に溶け込みながら、最近のネットワーク通信の履歴を把握するために利用されます（Living off the Land）。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: -
* **Detection (検知)**: PowerShellのスクリプトブロックロギング (イベントID 4104) を有効化し、`Get-DnsClientCache` の実行を記録・監視する。特に、他の偵察コマンド (`Get-Process`, `Get-NetTCPConnection`など) と連続して実行される場合は、攻撃の兆候である可能性があります。

## 注意点・補足

* **キャッシュの寿命**: DNSキャッシュのエントリにはTTL (Time To Live) があり、一定時間が経過すると自動的に削除されます。そのため、調査を行うタイミングによっては、既に痕跡が消えている可能性もあります。

---
[PowerShellインデックスに戻る](../../powershell_index.md)

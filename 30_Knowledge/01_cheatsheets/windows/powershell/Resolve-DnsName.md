---
tags:
  - 'Resolve-DnsName'
  - 'powershell'
  - 'cheatsheet'
title: 'Resolve-DnsName - DNS名前解決を実行する'
summary: '指定したドメイン名に対するDNSクエリを実行し、A, AAAA, MX, NSなどのDNSレコードを取得します。'
related:
  - 'Clear-DnsClientCache'
  - 'Get-DnsClientCache'
  - 'nslookup'
---

# `Resolve-DnsName` - DNS名前解決を実行する

## 概要

`Resolve-DnsName` コマンドレットは、DNS (Domain Name System) サーバーに対して名前解決のクエリを実行します。このコマンドレットは、CMDの `nslookup` に相当するモダンなPowerShellの代替であり、ドメイン名からIPアドレス（正引き）、IPアドレスからドメイン名（逆引き）、あるいはMXやNSなどの特定のレコードタイプを照会するために使用されます。

`nslookup` がテキストベースの出力を返すのに対し、`Resolve-DnsName` は各DNSレコードを詳細なプロパティを持つ**オブジェクト**として返すため、結果のフィルタリングやスクリプトでの利用が非常に容易です。

## 類似コマンドレット/コマンドとの差異

| 類似コマンドレット/コマンド | 差異 / 使い分けのポイント |
| :--- | :--- |
| **`nslookup` (CMD)** | `nslookup` は結果を**テキスト**として表示します。`Resolve-DnsName` は各レコードを**オブジェクト**として返すため、`IPAddress` や `NameHost` などのプロパティに直接アクセスでき、プログラム的な処理がはるかに簡単です。 |
| **`ping`** | `ping` は名前解決を試みた後、ICMPでホストの**疎通確認**まで行います。`Resolve-DnsName` は**名前解決のクエリのみ**に特化しています。 |
| **`Test-NetConnection`** | `Test-NetConnection` は名前解決、ping、TCPポート接続テストなど、より包括的な接続テストを提供します。DNSクエリのみが目的の場合は `Resolve-DnsName` の方がシンプルです。 |

## よく連携されるコマンドレット

### シナリオ例: ドメインの全てのMXレコードを取得し、接続テストを行う (インフラ構築・運用視点)

* **目的**: "example.com" ドメインのメールサーバー (MXレコード) を全て検索し、各サーバーのSMTPポート (TCP 25) への接続が可能かテストする。
* **連携コマンドレット**: `Test-NetConnection`
* **解説**: まず `Resolve-DnsName -Type MX` でドメインの全てのMXレコードを取得します。返ってきたオブジェクトの `NameExchange` プロパティにメールサーバーのホスト名が含まれているため、それを `ForEach-Object` ループで `Test-NetConnection` に渡し、ポート接続をテストします。
* **コマンド例**:

    ```powershell
    # 'example.com' のMXレコードを取得
    $mxRecords = Resolve-DnsName -Name "example.com" -Type MX

    # 各メールサーバーのTCPポート25への接続をテスト
    $mxRecords | ForEach-Object {
        Test-NetConnection -ComputerName $_.NameExchange -Port 25
    }
    ```

## パラメータ説明

| パラメータ | 型 | 説明 |
| :--- | :--- | :--- |
| ⭐ **`-Name`** | `String` | 解決する名前またはIPアドレスを指定します。 |
| ⭐ **`-Type`** | `RecordType` | 問い合わせるDNSレコードのタイプを指定します (`A`, `AAAA`, `PTR`, `MX`, `NS`, `CNAME`, `SOA`, `TXT` など)。デフォルトは `A` と `AAAA` の両方です。 |
| ⭐ **`-Server`** | `String[]` | 問い合わせに使用するDNSサーバーのIPアドレスを指定します。指定しない場合は、システムのDNS設定が使用されます。 |
| **`-DnsOnly`** | `SwitchParameter` | LLMNRやNetBIOSなどのホスト名解決プロトコルを使用せず、DNSクエリのみを実行します。 |
| **`-NoHostsFile`** | `SwitchParameter` | `hosts` ファイルを無視して、DNSサーバーに直接問い合わせます。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: DNSレコードの設定変更が、特定のDNSサーバー（例: Google Public DNS）に反映されているかを確認する。
* **組み合わせ**: `Resolve-DnsName -Name <domain> -Server <dns_server>`
* **解説**: `-Server` パラメータで外部のDNSサーバーを指定することで、内部DNSと外部DNSで見え方が異なる問題をトラブルシューティングできます。
* **例**:

    ```powershell
    # [www.example.com](https://www.example.com) のAレコードを 8.8.8.8 に問い合わせる
    Resolve-DnsName -Name "[www.example.com](https://www.example.com)" -Type A -Server "8.8.8.8"
    ```

### 2. ブルーチーム視点

* **タスク**: **インシデント調査**。マルウェアの通信先と思われる不審なドメインの情報を調査する。
* **組み合わせ**: `Resolve-DnsName <suspicious_domain>`
* **解説**: `nslookup` と同様に、不審なドメインのAレコード（IPアドレス）や、そのIPアドレスのPTRレコード（逆引き）を調査します。これにより、攻撃インフラの手がかりを得ることができます。
* **例**:

    ```powershell
    # 不審なドメインのIPアドレスを特定
    Resolve-DnsName -Name "malicious-c2.evil.com"

    # 既知の不審なIPアドレスの逆引きを試みる
    Resolve-DnsName -Name "198.51.100.100" -Type PTR
    ```

### 3. レッドチーム視点

* **タスク**: **偵察 (Reconnaissance)**。ターゲット組織のDNSインフラをマッピングする。
* **組み合わせ**: `-Type NS`, `-Type MX`, `-Type SRV`
* **解説**: 攻撃者は侵入前または侵入後の初期段階で、`Resolve-DnsName` を使ってターゲットドメインのDNSレコードを広範囲に調査します。

* `-Type NS`: ネームサーバーを特定

* `-Type MX`: メールサーバーを特定（フィッシングやパスワードスプレー攻撃の標的）
* `-Type SRV`: `_ldap._tcp.<domain>` などのSRVレコードを検索し、ドメインコントローラーを特定

* **例**:

    ```powershell
    # ターゲットドメインのメールサーバーを偵察
    Resolve-DnsName -Name "target-company.com" -Type MX

    # ターゲットドメインのドメインコントローラーを特定するためのSRVレコードを検索
    Resolve-DnsName -Name "_ldap._tcp.dc._msdcs.target-company.com" -Type SRV
    ```

## エラーメッセージとトラブルシューティング

### よくあるエラーメッセージ

1. **エラーメッセージ**: `Resolve-DnsName: <name> : DNS name does not exist`
    * **考えられる原因**: `NXDOMAIN`。問い合わせたドメイン名はDNSサーバーに存在しません。
    * **解決策**: ドメイン名のスペルが正しいか確認してください。

2. **エラーメッセージ**: `Resolve-DnsName: <name> : Unspecified error`
    * **考えられる原因**: DNSサーバーから応答がないか、`SERVFAIL` などのエラーが返されました。
    * **解決策**: `-Server` パラメータで別のDNSサーバーを指定して問題が再現するか確認します。`Clear-DnsClientCache` でローカルのキャッシュをクリアしてみるのも有効です。

## セキュリティに関する考慮事項

### 悪用事例

* **偵察 (Reconnaissance)**: レッドチームのシナリオで述べた通り、`Resolve-DnsName` は攻撃者がターゲットのネットワークインフラ（ドメインコントローラー、メールサーバーなど）を特定するための、極めて重要な偵察ツールです。

### LOLBASにおける利用例

* **機能**: **偵察 (Reconnaissance)**
* **解説**: `Resolve-DnsName` は PowerShell の標準機能であり、`nslookup.exe` と同様に、攻撃者が環境に溶け込みながらDNS情報を収集するために利用されます（Living off the Land）。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: 権威DNSサーバーで、ゾーン転送 (`AXFR`) を信頼できるサーバーのみに制限する。
* **Detection (検知)**: 内部から外部への異常なDNSクエリ（`ANY` や `SRV` タイプのクエリが多発するなど）を、ファイアウォールやDNSサーバーのログ、ネットワーク監視ツールで監視する。PowerShellのスクリプトブロックロギングも有効です。

## 注意点・補足

* **モジュール**: このコマンドレットは `DnsClient` モジュールに含まれています。これは近年のWindowsクライアントOSおよびサーバーOSにはデフォルトでインストールされています。

---
[PowerShellインデックスに戻る](../../powershell_index.md)

---
created: 2026-02-21 08:10
modified: 2026-02-21 08:27
environment: [Network, OS/Windows]
vulnearability: [Information_Disclosure]
knowledge_category: Command
tags:
  - cmd
  - ipconfig
  - network_recon
  - dns
  - dhcp
  - knowledge_base
---

# Command - Windows - cmd - ipconfig - ネットワーク設定の表示・構成

## 概要

`ipconfig` (IP Configuration) は、Windows のすべての現在の TCP/IP ネットワーク構成値を表示し、動的ホスト構成プロトコル (DHCP) およびドメイン ネーム システム (DNS) の設定を更新するためのコマンドです。

ネットワーク接続のトラブルシューティングにおける「最初の一手」であり、インターフェースの状態、IPアドレス、サブネットマスク、デフォルトゲートウェイなどの基本情報を即座に提供します。

(出自: `Windows 標準搭載 - TCP/IP ユーティリティ`)

## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 (あれば) |
| :--- | :--- | :--- |
| `ifconfig` | Linux/Unix 系の旧式コマンド。 | `ip addr` (Linux) |
| `ip addr` / `ip link` | 現代の Linux における標準。より詳細な L2/L3 操作が可能。 | - |
| `Get-NetIPAddress` (PS) | PowerShell 版。オブジェクトとして情報を返すため、特定のIPのみを抽出するなどの自動化に強い。 | PS 環境 |
| `netsh interface ip show` | ネットワーク設定の表示だけでなく、詳細な変更（固定IP設定など）を行う際に使用。 | 設定変更時 |

## よく連携されるコマンド

### シナリオ例: 特定のネットワーク情報の抽出 (調査視点)

* **目的**: 膨大な出力から IPv4 アドレスや DNS サーバ情報だけを素早く抜き出す。
* **連携コマンド**: `ipconfig`, `findstr`
* **解説**: `/all` スイッチは情報量が多いため、パイプと正規表現（あるいはキーワード検索）を組み合わせて必要な行だけを表示させます。
* **コマンド例**:
	```cmd
    REM 自分の IPv4 アドレスのみを表示
    ipconfig | findstr "IPv4"

    REM DNS サーバーの設定を確認

    ipconfig /all | findstr "DNS"

    ```

## スイッチ/オプション説明

| スイッチ | 説明 |
| :--- | :--- |
| **表示系** | |
| (引数なし) | 各アダプターの IP アドレス、サブネットマスク、デフォルトゲートウェイを表示。 |
| ⭐ `/all` | **【最重要】** 詳細情報を表示。ホスト名、MACアドレス、DHCP/DNSサーバー、リースの有効期限など。 |
| **DHCP操作系** | |
| `/release [adapter]` | 指定したアダプター（省略時は全て）の IPv4 アドレスを解放（DHCPサーバーに返却）。 |
| `/renew [adapter]` | 指定したアダプターの IPv4 アドレスを更新（DHCPサーバーに再要求）。 |
| **DNS操作系** | |
| ⭐ `/flushdns` | **【重要】** DNS リゾルバー キャッシュを消去。ドメイン名解決の不具合時に多用。 |
| `/displaydns` | 現在キャッシュされている DNS レコードの内容を表示。 |
| `/registerdns` | DHCP リースを更新し、DNS 名を再登録する。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点 (Docker/VMware利用者)

* **タスク**: 仮想環境構築時、どのアダプターがどの仮想ネットワーク（NAT/Host-Only）に紐付いているか特定する。
* **解説**: `ipconfig /all` で「説明 (Description)」欄を確認し、`VMware Virtual Ethernet Adapter` や `vEthernet (Default Switch)` などの名称から判別します。
* **例**:
	```cmd
    REM 仮想ネットワークアダプターの情報を含め全表示
    ipconfig /all
    ```

### 2. ブルーチーム視点

* **タスク**: インシデント調査時に、端末が現在どの DNS サーバーを参照しているか、不審なドメインを解決した形跡（キャッシュ）がないか確認する。
* **解説**: `/displaydns` でキャッシュを確認することで、ブラウザ履歴を消されても「どのドメインにアクセスしようとしたか」の痕跡を拾える可能性があります。
* **例**:
	```cmd
    REM DNS キャッシュを表示し、特定の悪性ドメインが含まれていないか確認
    ipconfig /displaydns | findstr "malicious-domain"
    ```

### 3. レッドチーム視点

* **タスク**: 内部偵察 (Internal Reconnaissance)。ターゲット端末が所属するサブネットの広さを確認し、他セグメント（マルチホーム等）へのルートがないか調べる。
* **解説**: ゲートウェイが複数ある場合や、複数の NIC が刺さっている場合、それはネットワーク間の「踏み台（ピボット）」として利用できる絶好のポイントになります。
* **例**:
	```cmd
    REM ネットワーク構成全体を把握し、ピボッティングの可能性を探る
    ipconfig /all
    ```

## エラーメッセージとトラブルシューティング

1.  **エラーメッセージ例 1**: `The operation failed as no adapter is in the state permissible for this operation.`
		* **考えられる原因**: `/renew` を実行したが、アダプターが接続されていない（メディアが切断されている）か、固定IPが設定されている。
		* **解決策**: 物理的な接続を確認し、DHCP 設定が有効になっているか確認してください。

2.  **エラーメッセージ例 2**: `Access is denied.`
		* **考えられる原因**: `/flushdns` などの一部の操作には管理者権限が必要な場合があります。
		* **解決策**: 管理者として実行してください。

## 環境変数と設定ファイル

### 関連するレジストリ/設定ファイル

* **`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\Interfaces`**: 各アダプターの個別設定が格納されているレジストリ。
* **`C:\Windows\System32\drivers\etc\hosts`**: DNS 解決において `ipconfig` より優先される静的マッピングファイル。

## セキュリティに関する考慮事項

### 脆弱性と悪用事例

* **悪用シナリオ**: 攻撃者が `ipconfig /displaydns` を定期的に実行し、管理者のアクセス先を特定してフィッシングやパスワードスプレーの標的を絞り込みます。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: 一般ユーザーに不要な情報を与えないよう、ホスト名の命名規則を抽象化する等の対策。
* **Detection (検知)**: `/displaydns` や `/all` の頻繁な実行は偵察の兆候である可能性があるため、SIEM 等で不審なプロセス実行（Event ID 4688）として監視する。

## 注意点・補足

* **IPv6 への対応**: `ipconfig` は IPv4/IPv6 両方の情報を表示しますが、IPv6 の一時アドレス（一時的な匿名アドレス）の概念を理解していないと、表示される大量の IP アドレスに混乱することがあります。
* **名前のスペース**: アダプター名にスペースが含まれる場合（例: `Wi-Fi 2`）、`/release "Wi-Fi 2"` のように引用符で囲む必要があります。

---
---
tags:
  - ip_link
  - ip
  - iproute2
  - networking
  - ifconfig
  - ip_addr
  - ethtool
created: 2025-09-22 08:41
modified: 2026-02-20 15:40
environment: [OS/Linux]
vulnearability: []
knowledge_category: Command
---

# Command  - Linux - ip_link - ネットワークデバイス (インターフェイス) を管理する

## 概要

`ip link` は、`ip` コマンドスイートの一部であり、ネットワークインターフェイス（物理的なNIC、仮想ブリッジ、VLANデバイスなど）そのものを表示・設定・管理するためのコマンドです。これはOSI参照モデルの**データリンク層 (L2)** に相当する操作を行います。インターフェイスの有効化/無効化 (`up`/`down`)、MACアドレスの変更、MTU (Maximum Transmission Unit) の設定など、IPアドレスが割り当てられる前の「土台」となるデバイスの状態を管理します。

(出自: `iproute2` パッケージに含まれる)

## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 |
| :--- | :--- |:---|
| **`ifconfig`** | **旧世代のコマンド**。`ip link` と `ip addr` の両方の機能を持ち合わせていました。`ip link` はL2層のデバイス管理に特化しており、より明確な役割分担がされています。 | `ip link` |
| **`ip addr`** | `ip link` で管理されるデバイスの上に、**IPアドレス (L3層)** を設定・管理します。「デバイスを操作するのが `ip link`」「アドレスを操作するのが `ip addr`」と覚えると分かりやすいです。 | - |
| **`ethtool`** | ネットワークインターフェイスの**物理的な特性**（リンク速度、デュプレックスモード、ドライバ情報など）を、より低レベルで表示・設定するための専門ツールです。 | - |

## よく連携されるコマンド

`ip link` は、コンテナや仮想化のための仮想ネットワークを構築する際、`ip netns` や `brctl` (または `ip link add type bridge`) と密接に連携します。

### シナリオ例: 仮想Ethernetペアを作成し、名前空間に接続する (インフラ構築・運用視点)

* **目的**: 2つのネットワーク名前空間を接続するための仮想的なケーブル (`veth`ペア) を作成する。
* **連携コマンド**: `ip netns`, `ip link`
* **解説**: `ip link add type veth` で仮想インターフェイスのペアを作成し、`ip link set` でそれぞれの端を異なるネットワーク名前空間に移動させます。これはコンテナネットワークの基礎となる技術です。
* **コマンド例**:

```bash
# 2つの名前空間 ns1, ns2 を作成 (事前に)
sudo ip netns add ns1
sudo ip netns add ns2

# vethペア (veth1 と veth2) を作成
sudo ip link add veth1 type veth peer name veth2

# 各vethをそれぞれの名前空間に移動
sudo ip link set veth1 netns ns1
sudo ip link set veth2 netns ns2
```

## オプション説明 (`ip link` のアクション)

| オプション | 説明 |
| :--- | :--- |
| **表示** | |
| ⭐ `show` (or `list`, `ls`) | (デフォルト) ネットワークデバイスの一覧を表示します。 |
| **状態変更** | |
| ⭐ `set <dev> up` | デバイスを**有効化 (activate)** します。 |
| ⭐ `set <dev> down` | デバイスを**無効化 (deactivate)** します。 |
| **属性変更** | |
| `set <dev> address <MAC>` | デバイスのMACアドレスを変更します。 |
| `set <dev> mtu <bytes>` | デバイスのMTU値を変更します。 |
| **デバイス作成/削除** | |
| `add <link> type <type>` | 新しい仮想デバイスを作成します。（例: `type bridge`, `type vlan`, `type veth`） |
| `del <link>` | 仮想デバイスを削除します。 |

## よく使われるオプション組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: ネットワークインターフェイスを「再起動」して接続をリフレッシュする。
* **組み合わせ**: `sudo ip link set <dev> down && sudo ip link set <dev> up`
* **解説**: ネットワーク設定を変更した後や、ドライバの挙動がおかしい場合に、インターフェイスを一度無効化し、再度有効化することで問題を解決しようとする際の基本的な手順です。
* **例**:

```bash
# eth0 インターフェースを一度無効化し、再度有効化する
sudo ip link set dev eth0 down && sudo ip link set dev eth0 up
```

### 2. ブルーチーム視点

* **タスク**: 侵害されたホストをネットワークから即座に切り離す（封じ込め）。
* **組み合わせ**: `sudo ip link set <interface> down`
* **解説**: インシデントレスポンス中に、侵害されたホストがこれ以上被害を拡大（ラテラルムーブメント）したり、外部のC2サーバーと通信したりするのを防ぐための、最も確実な封じ込め措置の1つです。
* **例**:

```bash
# eth0を無効化して、サーバーのネットワーク接続を遮断
sudo ip link set dev eth0 down
```

### 3. レッドチーム視点

* **タスク**: MACスプーフィングでネットワークアクセス制御をバイパスする。
* **組み合わせ**: `sudo ip link set dev <dev> address <new_mac>`
* **解説**: ネットワークアクセス制御 (NAC) がMACアドレスに基づいて行われている環境をバイパスするために、許可されたデバイスのMACアドレスになりすまします。
* **例**:

```bash
# eth0 の MACアドレスを偽装する
sudo ip link set dev eth0 down
sudo ip link set dev eth0 address 00:11:22:33:44:55
sudo ip link set dev eth0 up
```

## エラーメッセージとトラブルシューティング

* 一般的なエラーは [Linux共通のトラブルシューティング](./troubleshooting_common_errors.md) を参照。

1. **エラーメッセージ例 1**: `RTNETLINK answers: Operation not permitted`
		* **考えられる原因**: `set`, `add`, `del` など、設定を変更する操作を一般ユーザー権限で実行しようとしました。
		* **解決策**: コマンドの前に `sudo` を付けて実行してください。

## 環境変数と設定ファイル

* `ip link` の動作に直接影響する環境変数は通常ありません。
* 一般的な環境変数・設定ファイルは [Linux共通の環境変数・設定ファイル](./environment_and_config.md) を参照。

## セキュリティに関する考慮事項

### 脆弱性と悪用事例

* **脆弱性**: `ip link` 自体ではなく、**不適切なネットワーク設定**や**権限管理**がリスクとなります。
* **悪用シナリオ**:
	* **MACスプーフィング**: 攻撃者がMACアドレスを偽装し、ネットワークアクセス制御をバイパスする。
	* **プロミスキャスモード**: ブルーチームのシナリオで述べたように、`ip link show` の出力に `PROMISC` フラグがあれば、ネットワークスニッファが動作している兆候です。

### GTFOBins / LOLBAS における利用例

* **機能**: **Privilege Escalation**
* **解説**: もしユーザーが `sudo ip` を実行できる権限を持っている場合、`-f` オプションでスクリプトファイルを読み込ませる機能などを悪用して、任意のコマンドを実行することが可能です。
* **参照**: `https://gtfobins.github.io/gtfobins/ip/`

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: 最小権限の原則に従い、一般ユーザーに `ip` コマンドの `sudo` 権限を安易に与えない。スイッチ側でポートセキュリティを設定し、許可されていないMACアドレスからの通信をブロックする。
* **Detection (検知)**: `auditd` を使って、`netlink` 経由でのネットワーク構成の変更を監視する。`ip link show` の出力を定期的に取得し、ベースラインと比較して意図しない変更（特に `PROMISC` フラグ）がないかを確認する。

## 注意点・補足

* **設定の永続化**: `ip link` コマンドで行った設定変更（MACアドレスの変更など）は、デフォルトでは**OSを再起動すると失われます**。設定を永続化させるには、各ディストリビューションのネットワーク管理設定ファイル（`/etc/netplan/`など）を編集する必要があります。


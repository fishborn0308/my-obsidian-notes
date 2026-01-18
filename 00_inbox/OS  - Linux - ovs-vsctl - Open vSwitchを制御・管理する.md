---
tags:
  - ovs-vsctl
  - open_vswitch
  - ovs
  - networking
  - sdn
  - brctl
  - ip_link
  - ovs-ofctl
created: 2025-06-29 15:02
modified: 2026-01-18 20:34
environment:
  - OS/Linux
vulnearability: []
knowledge_category: OS
---

# OS  - Linux - ovs-vsctl - Open vSwitchを制御・管理する

## 概要

`ovs-vsctl` (Open vSwitch vs. Control) は、Open vSwitchのメインデーモンである `ovs-vswitchd` を設定するための主要なコマンドラインユーティリティです。Open vSwitch (OVS) は、標準のLinuxブリッジを遥かに超える機能を持つ、プロダクショングレードのプログラマブルな仮想スイッチです。

`ovs-vsctl` を使うことで、仮想スイッチ（ブリッジ）の作成、ポートの追加・削除、VLANの設定、トンネリングなど、仮想ネットワークの構成を柔軟に操作できます。

(出自: `openvswitch-switch` パッケージに含まれる)

## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 |
| :--- | :--- |:---|
| ⭐ **`brctl` / `ip link`**| 標準の**Linuxブリッジ**を管理します。OVSは、VLAN、トンネリング (VXLANなど)、**OpenFlow**による高度なフロー制御など、Linuxブリッジにはない多数の高度な機能を持ちます。 | OVS (高機能) |
| **`ovs-ofctl`** | Open vSwitchの**フローテーブル**を直接操作します。`ovs-vsctl` はスイッチの**構成**（ポート、VLANなど）を行い、`ovs-ofctl` はそのスイッチの**動作**（パケット転送ルール）を定義します。 | - |

## よく連携されるコマンド

`ovs-vsctl` は、仮想マシンやコンテナを接続するために `ip` コマンドと連携して仮想インターフェイスを作成・設定する際によく使われます。

### シナリオ例: VM用のVLANアクセスポートを作成する (インフラ構築・運用視点)

* **目的**: KVM仮想マシン (`tap0`) を、VLAN 100に所属するアクセスポートとしてブリッジ `br-int` に接続する。
* **連携コマンド**: `ip`, `ovs-vsctl`
* **解説**: まず `ip` コマンドでVM用のTAPインターフェイスを作成・有効化します。その後、`ovs-vsctl add-port` に `tag=` オプションを付けてブリッジに追加することで、そのポートを特定のVLANに所属させます。
* **コマンド例**:

```bash
# 1. VM用のTAPデバイスを作成 (例)
sudo ip tuntap add dev tap0 mode tap
sudo ip link set tap0 up

# 2. tap0をVLAN 100のアクセスポートとしてbr-intブリッジに追加
sudo ovs-vsctl add-port br-int tap0 tag=100
```

## オプション説明 (`ovs-vsctl` のサブコマンド)

| オプション | 説明 |
| :--- | :--- |
| **情報表示** | |
| ⭐ `show` | OVSの現在の設定状況をツリー形式で表示します。 |
| `list-br`, `list-ports`, `list-ifaces` | それぞれブリッジ、ポート、インターフェイスを一覧表示します。 |
| **ブリッジ操作** | |
| ⭐ `add-br <bridge>` | `<bridge>` という名前の新しいブリッジを作成します。 |
| `del-br <bridge>` | ブリッジを削除します。 |
| **ポート操作** | |
| ⭐ `add-port <bridge> <port>`| ブリッジにポートを追加します。 |
| ⭐ `del-port [<bridge>] <port>`| ブリッジからポートを削除します。 |
| **高度な設定** | |
| `set <Table> <Record> <col=val>`| OVSデータベースの値を直接設定する汎用コマンド。VLAN設定などで多用します。 |
| `--may-exist`, `--if-exists`| スクリプトで使う際に、対象が存在/不存在でもエラーにしない冪等性オプション。 |

## よく使われるオプション組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: 仮想マシン (`tap0`) と物理NIC (`eth0`) を接続する基本的なブリッジ `br-ex` を作成する。
* **組み合わせ**: `add-br`, `add-port`
* **解説**: 仮想スイッチを作成し、そこに物理インターフェイスと仮想インターフェイスを接続する、OVSの最も基本的な利用パターンです。
* **例**:

```bash
# 1. 外部接続用のブリッジを作成
sudo ovs-vsctl add-br br-ex

# 2. 物理NIC eth0 と VM用のtap0 をブリッジに追加
sudo ovs-vsctl add-port br-ex eth0
sudo ovs-vsctl add-port br-ex tap0

# 3. 状態を確認
sudo ovs-vsctl show
```

### 2. ブルーチーム視点

* **タスク**: 侵害されたVMをネットワークから即座に切り離して封じ込める。
* **組み合わせ**: `ovs-vsctl del-port <bridge> <compromised_vm_port>`
* **解説**: インシデントレスポンス中に、侵害されたVMが接続されているポート (`vnet0`など) を特定し、`del-port` でブリッジから切り離します。これにより、VMをシャットダウンすることなく、ネットワーク的な活動を即座に停止させることができます。
* **例**:

```bash
# 侵害されたVMのポート 'vnet5' をブリッジ 'br-int' から切り離す
sudo ovs-vsctl del-port br-int vnet5
```

### 3. レッドチーム視点

* **タスク**: 侵入後の偵察として、仮想ネットワークの全体像をマッピングする。
* **組み合わせ**: `ovs-vsctl show`
* **解説**: 攻撃者は侵入した仮想化ホストでこのコマンドを実行し、ブリッジの構成、VLANのID、各VMの接続状況などを把握します。`br-int`, `br-storage`, `br-mgmt` のような名前からネットワークの役割を推測し、次の攻撃対象を特定します。
* **例**:

```bash
# 現在のOVS構成を全て表示
ovs-vsctl show
```

## エラーメッセージとトラブルシューティング

* 一般的なエラーは [Linux共通のトラブルシューティング](./troubleshooting_common_errors.md) を参照。

1. **エラーメッセージ例 1**: `ovs-vsctl: unix:/var/run/openvswitch/db.sock: connect: No such file or directory`
		* **考えられる原因**: Open vSwitchのデーモン (`ovs-vswitchd`) が起動していません。
		* **解決策**: `sudo systemctl start openvswitch-switch` (または同等のコマンド) でサービスを起動してください。

## 環境変数と設定ファイル

* `ovs-vsctl` の動作に直接影響する環境変数は通常ありません。
* 一般的な環境変数・設定ファイルは [Linux共通の環境変数・設定ファイル](./environment_and_config.md) を参照。

### 関連する設定ファイル

* **`/etc/openvswitch/conf.db`**: Open vSwitchの構成情報が保存されるデータベースファイル。`ovs-vsctl` はこのファイルを操作します。

## セキュリティに関する考慮事項

### 脆弱性と悪用事例

* **脆弱性**: OVS自体よりも、**制御プレーンのセキュリティ**が重要です。
* **悪用シナリオ**: 攻撃者がroot権限を奪取した場合、`ovs-vsctl` を使って**ポートミラーリング**を設定し、同じブリッジに接続された他のVMの通信を全て自身のVMにコピーさせ、ネットワークトラフィックを盗聴（スニッフィング）することが可能です。

### GTFOBins / LOLBAS における利用例

`ovs-vsctl` はOSの標準コンポーネントではないため、GTFOBinsにはリストされていません。しかし、`sudo` 権限で実行できる場合、ネットワーク構成を自由に変更できるため、実質的にシステムを制御する強力な権限となります。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: ホストOSへのrootアクセスを厳格に管理する。OVSの管理インターフェイスへのアクセスを制限する。
* **Detection (検知)**: OVSの構成変更ログや、`ovs-vsctl` コマンドの実行を `auditd` で監視する。

## 注意点・補足

* **設定の永続性**: `ovs-vsctl` で行った設定は、OVSのデータベースファイルに書き込まれ、**デフォルトで永続化されます**。


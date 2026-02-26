---
# --- YAML Frontmatter ---
created: 2026-02-22 16:49
modified: 2026-02-23 09:52
environment: [OS/Linux, Network/L2_Bridge, Virtualization/KVM]
vulnearability: [Man-in-the-Middle, L2_Sniffing, STP_Manipulation]
knowledge_category: Command
tags:
  - brctl
  - bridge-utils
  - networking
  - knowledge_base
---

# Command - Linux - brctl - イーサネットブリッジ（L2スイッチ）の管理

## 概要

`brctl` は、Linux カーネル内で複数のネットワークインターフェースを論理的に結合し、仮想的な L2 スイッチ（ブリッジ）を作成・管理するコマンドです。仮想マシンを物理ネットワークに直接参加させる際のハブとして機能します。

(出自: `bridge-utils` パッケージに含まれる。現在は `ip link add type bridge` への移行が推奨されている)

## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 |
| :--- | :--- | :--- |
| `ip link` | `iproute2` パッケージに含まれる現代の標準。ブリッジ作成も可能。 | **現代の標準** |
| `nmcli` | NetworkManager を通じて設定。永続的な設定（再起動後も有効）に強い。 | RHEL系/デスクトップ |
| `ovs-vsctl` | Open vSwitch の管理用。SDN や高度な仮想化ネットワークで使用。 | 大規模仮想化環境 |
| `brctl` | 伝統的で直感的な構文。古い教材や特定の仮想化スクリプトで多用される。 | レガシー/学習用 |

## よく連携されるコマンド

### シナリオ例: 仮想マシン(VM)用ブリッジの作成 (運用視点)

* **目的**: 物理 NIC (`eth0`) と仮想 NIC (`vnet0`) を繋ぎ、VM が外部と通信できるようにする。
* **連携コマンド**: `ip addr`, `tunctl`
* **解説**: ブリッジを作成し、そこにインターフェースを参加させます。
* **コマンド例**:
    ```bash
    # ブリッジ 'br0' を作成
    sudo brctl addbr br0
    # 物理インターフェースを追加
    sudo brctl addif br0 eth0
    # ブリッジを起動
    sudo ip link set br0 up
    ```

## オプション説明

| オプション | 説明 |
| :--- | :--- |
| **ブリッジ管理** | |
| ⭐️ `addbr <bridge>` | 新しいブリッジインターフェースを作成する。 |
| `delbr <bridge>` | 指定したブリッジを削除する（先に down にする必要あり）。 |
| ⭐️ `show` | 現在のブリッジ構成と参加しているインターフェースを表示する。 |
| **インターフェース管理** | |
| ⭐️ `addif <br> <dev>` | ブリッジに物理/仮想インターフェースを追加する。 |
| `delif <br> <dev>` | ブリッジからインターフェースを切り離す。 |
| **STP設定** | |
| `stp <br> <on/off>` | スパニングツリープロトコル (STP) の有効/無効を切り替える。 |
| `showmacs <br>` | ブリッジが学習している MAC アドレスとポートの対応表を表示する。 |

## よく使われるオプション組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: ループ防止のための STP 有効化。
* **組み合わせ**: `stp on`
* **解説**: ブリッジを複数接続してネットワークに冗長性を持たせる際、ループによるブロードキャストストームを防ぐために STP を有効にします。
* **例**:
    ```bash
    # br0 で STP を有効にする
    sudo brctl stp br0 on
    ```

### 2. ブルーチーム視点

* **タスク**: ネットワーク内の不審な MAC アドレスの特定。
* **組み合わせ**: `showmacs`
* **解説**: ブリッジ（仮想スイッチ）を通過しているデバイスの MAC アドレス一覧を確認し、未知のデバイスや MAC スプーフィングの兆候がないか調査します。
* **例**:
    ```bash
    # ブリッジが認識している MAC アドレス一覧を表示
    brctl showmacs br0
    ```

### 3. レッドチーム視点

* **タスク**: ステルスな中間者攻撃（Transparent Bridge MitM）。
* **組み合わせ**: `addbr` + `addif`
* **解説**: 2つの物理 NIC を持つマシンをターゲットの経路上に「透明なブリッジ」として挿入します。IP アドレスを持たせないことで、ターゲットからは存在が見えないまま通信を盗聴（スニッフィング）できます。
* **例**:
    ```bash
    # 2つのNICをブリッジして通信を透過させる
    sudo brctl addbr br0
    sudo brctl addif br0 eth0
    sudo brctl addif br0 eth1
    sudo ip link set br0 up
    # この状態で tcpdump を実行
    sudo tcpdump -i br0
    ```

## エラーメッセージとトラブルシューティング

1.  **エラーメッセージ例 1**: `device eth0 is already a member of a bridge; can't enslave it to bridge br0.`
    * **考えられる原因**: `eth0` が既に他のブリッジに参加している。
    * **解決策**: `brctl show` で現在の参加状況を確認し、必要であれば `delif` で切り離してから追加する。

2.  **エラーメッセージ例 2**: `bridge br0 does not exist!`
    * **考えられる原因**: 指定したブリッジ名が間違っているか、まだ作成されていない。
    * **解決策**: `brctl show` で存在するブリッジ名を確認する。

## 環境変数と設定ファイル

* `brctl` は一時的な設定を行うコマンドです。
* **永続化**: Debian/Ubuntu では `/etc/network/interfaces` に、RHEL系では `/etc/sysconfig/network-scripts/ifcfg-br0` 等に記述する必要があります。

## セキュリティに関する考慮事項

### 脆弱性と悪用事例

* **脆弱性**: STP が無効な場合、ネットワークループによる DoS が発生しやすい。
* **悪用シナリオ**: 攻撃者が偽の STP BPDU パケットを送信してブリッジの優先順位を操作し、自身を「ルートブリッジ」に設定させることで、ネットワーク全体のトラフィックを引き寄せる（STP Attack）。

### GTFOBins における利用例

`brctl` 自体は GTFOBins には登録されていませんが、`sudo` 権限がある場合、ネットワーク構成を自由に変更できるため、隔離されたネットワーク（セグメント）を橋渡しして情報の持ち出しルートを作成するなどの悪用が可能です。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: STP を適切に設定し、必要に応じて BPDU Guard などの機能をスイッチ側で併用する。
* **Detection (検知)**: `brctl` コマンドの実行履歴を監視し、予期せぬブリッジの作成やインターフェースの追加を検知する。

## 注意点・補足

* **パケットフィルタリング**: ブリッジを通過するパケットに対して `iptables` や `nftables` で制限をかけたい場合、`br_netfilter` カーネルモジュールをロードする必要があります。
* **移行推奨**: 前述の通り、現在は `ip link` を使うのがモダンです。
    * 例: `ip link add br0 type bridge`
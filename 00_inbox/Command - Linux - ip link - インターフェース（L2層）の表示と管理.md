---
# --- YAML Frontmatter ---
created: '2026-02-23'
modified: '2026-02-23'
environment: [OS/Linux (iproute2)]
vulnearability: [Network_Denial_of_Service, MAC_Spoofing]
knowledge_category: Command
tags:
  - 'ip'
  - 'ip-link'
  - 'networking'
  - 'layer2'
  - 'administration'
  - 'knowledge_base'
---

# Command - Linux - ip link - インターフェース（L2層）の表示と管理

## 概要
`ip link` オブジェクトは、ネットワークデバイス（NIC、ブリッジ、VLAN、トンネル等）の物理的な側面を管理します。IP アドレスを持つ前の「器」としての設定を担当し、デバイスの有効化・無効化、MAC アドレスの変更、MTU（最大転送単位）の調整などを行います。



## 主要サブコマンド
| サブコマンド | 略称 | 説明 |
| :--- | :--- | :--- |
| **`show`** | `sh`, `s` | デバイスの状態と属性を表示する（デフォルト）。 |
| **`set`** | | デバイスの属性（状態、名前、アドレス等）を変更する。 |
| **`add`** | `a` | 新しい仮想デバイス（VLAN, Bridge 等）を作成する。 |
| **`delete`** | `del`, `d` | 仮想デバイスを削除する。 |

## コマンド例と構文

### 1. デバイス状態の確認 (show)
ケーブルの接続状態（carrier）やエラー統計などを確認する際に使用します。
```bash
# 全デバイスの L2 情報を表示
ip link show

# 特定のデバイス（eth0）のみ表示
ip link show dev eth0

# 統計情報（パケット数、エラー数等）を表示
ip -s link show eth0
````

### 2. デバイスの状態変更 (set)

最もよく使われる「スイッチ」としての機能です。

Bash

```
# インターフェースを有効（UP）にする
sudo ip link set eth0 up

# インターフェースを無効（DOWN）にする
sudo ip link set eth0 down

# MTU サイズを 9000（ジャンボフレーム）に変更する
sudo ip link set eth0 mtu 9000

# MAC アドレスを変更する（偽装/変更）
sudo ip link set eth0 address 00:aa:bb:cc:dd:ee
```

### 3. 仮想デバイスの作成 (add / delete)

仮想化環境や複雑なネットワーク構築で多用します。

Bash

```
# 'br0' という名前のブリッジデバイスを作成
sudo ip link add name br0 type bridge
sudo ip link set br0 up

# VLAN デバイス (ID: 10) を eth0 上に作成
sudo ip link add link eth0 name eth0.10 type vlan id 10
```

## 出力項目の読み解き

- **`mtu 1500`**: 最大転送パケットサイズ。標準は 1500 バイト。
    
- **`qdisc`**: パケットの送信待ち行列のアルゴリズム（Queueing Discipline）。
    
- **`state UP / DOWN / UNKNOWN`**: デバイスの動作状態。
    
- **`mode DEFAULT`**: 動作モード。
    
- **`link/ether`**: 物理層のアドレス（MAC アドレス）。
    
- **`brd`**: レイヤ2のブロードキャストアドレス（通常は `ff:ff:ff:ff:ff:ff`）。
    

## 業務シナリオ別活用法

### 1. インフラ構築・運用視点

- **タスク**: NIC チーミングやブリッジの構築。
    
- **解説**: 物理的な NIC を束ねて仮想的なスイッチ（Bridge）を作る際、`ip link add` でデバイスを定義し、`ip link set` でそれらをリンクさせます。
    

### 2. ブルーチーム視点

- **タスク**: 物理層での異常検知。
    
- **解説**: インターフェースの統計 (`-s`) を見て、`errors` や `dropped` が急増していないか確認します。これはケーブル不良や、ネットワーク層への攻撃（DDoS）の予兆である可能性があります。
    

### 3. レッドチーム視点

- **タスク**: MAC アドレス偽装によるフィルタリング回避。
    
- **解説**: 許可された端末の MAC アドレスをコピーして `ip link set eth0 address` で書き換えることで、ポートセキュリティを突破しようと試みます。
    

## セキュリティに関する考慮事項

### 脆弱性とリスク

- **情報の露出**: MAC アドレスのベンダーコード（前方3バイト）から、そのサーバーが物理マシンか仮想マシン（VMware, AWS等）か、どのメーカーの機器かといった「足がかり」を攻撃者に与えます。
    

### 対応策・緩和策 (ブルーチーム視点)

- **Monitoring**: `ip link set ... down` が勝手に実行されていないか監視します。これはサービス停止（DoS）を狙った攻撃の可能性があります。
    
- **L2 Security**: スイッチ側で「ポートセキュリティ（MACアドレス制限）」を設定し、`ip link` によるアドレス変更を無効化します。
    


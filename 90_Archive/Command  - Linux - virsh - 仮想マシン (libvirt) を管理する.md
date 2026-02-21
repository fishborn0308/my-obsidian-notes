---
tags:
  - virsh
  - kvm
  - qemu
  - virtualization
  - libvirt
  - virt-manager
  - docker
  - systemctl
created: 2025-06-29 15:02
modified: 2026-02-20 17:36
environment: [OS/Linux]
vulnearability: []
knowledge_category: Command
---

# Command  - Linux - virsh - 仮想マシン (libvirt) を管理する

## 概要

`virsh` (virtualization shell) は、**libvirt** APIを操作するための主要なコマンドラインインターフェイスです。libvirtは、KVM/QEMU, Xen, LXCといった様々なハイパーバイザやコンテナエンジンを、統一的な方法で管理するためのライブラリとデーモンです。

`virsh` を使うことで、仮想マシン（libvirtの世界では **ドメイン** と呼ばれる）の起動・停止・再起動・状態確認、仮想ネットワークやストレージの管理など、仮想化ホストに関するほぼ全ての操作をコマンドラインから実行できます。

(出自: `libvirt-client` パッケージに含まれる)

## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 |
| :--- | :--- |:---|
| **`virt-manager`** | `virsh` の**グラフィカル (GUI)**版。デスクトップ環境で直感的に仮想マシンを管理したい場合に使います。 | `virsh` (CUI/スクリプト) |
| **`qemu-system-*`**| `libvirt` が内部的に利用する低レベルなエミュレータ/ハイパーバイザ本体。`virsh` はその複雑さを隠蔽し、安定した管理インターフェイスを提供します。 | `virsh` |
| **`docker` / `podman`**| **コンテナ**を管理します。コンテナはホストカーネルを共有するOSレベルの仮想化です。`virsh` は独立したカーネルを持つ**完全な仮想マシン (VM)**を管理します。 | - |

## よく連携されるコマンド

`virsh` の出力はXML形式が多いため、`grep` やXMLパーサー (`xmlstarlet`) と連携して特定の情報を抽出することがよくあります。

### シナリオ例: VMのディスクイメージパスを特定する (インフラ構築・運用視点)

* **目的**: 仮想マシン `web-server` が使用しているディスクイメージの物理パスを調べる。
* **連携コマンド**: `grep`
* **解説**: `virsh dumpxml` でVMの全設定をXML形式で出力し、その中から `source file` という行を `grep` でフィルタリングすることで、ディスクイメージのパスを特定します。
* **コマンド例**:

		```bash
		# web-serverのXML定義からディスクファイルのパスを抽出

		sudo virsh dumpxml web-server | grep "source file"

		```

## オプション説明 (`virsh` のサブコマンド)

| オプション | 説明 |
| :--- | :--- |
| **ドメイン (VM) 管理** | |
| ⭐ `list [--all]` | ドメイン（VM）を一覧表示します。`--all` を付けると停止中のものも表示。 |
| ⭐ `start <domain>`| ドメインを起動します。 |
| ⭐ `shutdown <domain>`| ドメインにACPIシャットダウン信号を送り、穏やかにシャットダウンさせます。 |
| ⭐ `destroy <domain>`| ドメインを**強制的に停止**します。「電源ブチ切り」に相当します。 |
| `reboot <domain>` | ドメインを再起動します。 |
| `suspend`/`resume`| ドメインを一時停止/再開します。 |
| `autostart [--disable]`| ホスト起動時にドメインを自動起動するように設定/解除します。 |
| **コンソール/定義** | |
| ⭐ `console <domain>`| ドメインの仮想シリアルコンソールに接続します。 |
| ⭐ `dumpxml <domain>`| ドメインの設定をXML形式で表示します。 |
| `define <file.xml>`| XMLファイルから新しいドメインを定義（登録）します。 |
| `undefine <domain>`| ドメインの定義を削除します。 |

## よく使われるオプション組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: SSHが応答しないVMのトラブルシューティングを行う。
* **組み合わせ**: `virsh console <domain>`
* **解説**: 仮想マシンのネットワーク設定を間違えてSSHでログインできなくなった場合、`virsh console` でシリアルコンソールに接続し、rootでログインして設定ファイルを修正します。
* **例**:

		```bash
		# my-vm のコンソールに接続 (終了は Ctrl + ])

		sudo virsh console my-vm

		```

## 2. ブルーチーム視点

* **タスク**: 侵害されたVMを、メモリ情報を保全したまま活動停止させる（封じ込め）。
* **組み合わせ**: `sudo virsh suspend <compromised_vm>`
* **解説**: `virsh suspend` でVMをメモリの状態を保持したまま一時停止させ、活動を即座に止めます。その後、メモリダンプを取得して詳細なフォレンジック分析を行ったり、`virsh destroy` で完全に停止させたりします。
* **例**:

		```bash
		# 侵害されたVM 'suspicious-vm' を一時停止

		sudo virsh suspend suspicious-vm

		```

## 3. レッドチーム視点

* **タスク**: 侵入した仮想化ホストから、オフラインでVM内のデータを窃取する。
* **組み合わせ**: `virsh dumpxml <domain>` → `mount <disk_image>`
* **解説**: `dumpxml` でVMのディスクイメージのパスを特定し、そのディスクイメージをホスト上で直接マウントします。これにより、VMを起動させることなく、内部のファイル（`/etc/shadow`など）をオフラインで読み書きします。
* **例**:

		```bash
		# 1. ディスクパスを特定

		sudo virsh dumpxml target-vm | grep "source file"

		# 2. (特定したディスクイメージをマウントしてデータを窃取)
		```

## エラーメッセージとトラブルシューティング

* 一般的なエラーは [Linux共通のトラブルシューティング](OS%20%20-%20Linux%20-%20troubleshooting_common_errors%20-%20Linux共通エラー対応ガイド.md) を参照。

1. **エラーメッセージ例 1**: `error: failed to connect to the hypervisor`
		* **考えられる原因**: `libvirtd` デーモンが起動していないか、コマンドを実行しているユーザーに `libvirt` グループへの所属など、デーモンと通信するための権限がありません。
		* **解決策**: `sudo systemctl start libvirtd` でデーモンを起動します。`sudo` を付けて `virsh` を実行するか、`sudo usermod -aG libvirt $(whoami)` で現在のユーザーを `libvirt` グループに追加します（再ログインが必要です）。

## 環境変数と設定ファイル

* `virsh` の動作に直接影響する環境変数は通常ありません。
* 一般的な環境変数・設定ファイルは [Linux共通の環境変数・設定ファイル](./environment_and_config.md) を参照。

### 関連する設定ファイル

* **`/etc/libvirt/qemu/`**: KVM/QEMUドメインのXML定義ファイルが格納されるディレクトリ。
* **`/var/lib/libvirt/images/`**: デフォルトのディスクイメージ格納場所。

## セキュリティに関する考慮事項

### 脆弱性と悪用事例

* **脆弱性**: **ハイパーバイザの侵害**。ホストOSのセキュリティが、その上で動作する全てのVMのセキュリティの根幹です。
* **悪用シナリオ**: 攻撃者がホストのroot権限を奪取した場合、レッドチームのシナリオで述べたように、全VMのディスクイメージにオフラインでアクセスし、データを窃取したり、バックドアを仕込んだりすることが可能になります。

### GTFOBins / LOLBAS における利用例

* **機能**: **Privilege Escalation**
* **解説**: もしユーザーが `sudo virsh` を実行できる権限を持っている場合、VMのディスクイメージをマウントして `/etc/sudoers` を書き換えるなど、間接的にroot権限を奪取できます。
* **参照**: `https://gtfobins.github.io/gtfobins/virsh/`

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: ハイパーバイザ（ホストOS）へのアクセスを厳格に管理する。`libvirt` グループへのユーザー追加は、`sudo` 権限を付与するのと同等に慎重に行う。
* **Detection (検知)**: `auditd` で `virsh` コマンドの実行や、`/etc/libvirt/` 配下のファイル変更を監視する。

## 注意点・補足

* **ドメイン (Domain)**: `virsh` や `libvirt` の文脈では、「ドメイン」は「仮想マシン」とほぼ同義です。

---


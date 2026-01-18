---
tags:
  - hostnamectl
  - systemd
  - hostname
  - networking
  - hostname
  - systemd
  - /etc/hostname
created:
modified:
environment: []
vulnearability: []
knowledge_category: Command
---

# Command  - Linux - hostnamectl - システムのホスト名を永続的に管理する




## 概要

`hostnamectl` は、`systemd` を採用したモダンなLinuxシステムにおいて、**システムのホスト名と関連設定を照会・変更するための標準的なコマンド**です。旧来の `hostname` コマンドとは異なり、`hostnamectl` で行った変更は `/etc/hostname` などの設定ファイルに自動的に書き込まれ、**OSを再起動しても維持されます（永続化）**。

また、単なるホスト名だけでなく、「Pretty Hostname」やシャーシタイプ、デプロイ環境といったメタデータも管理できる、より高機能なツールです。

(出自: `systemd` パッケージに含まれる)

## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 |
| :--- | :--- |:---|
| **`hostname`** | 旧来のコマンド。`hostname` による変更は一時的なものであり、再起動すると元に戻ります。`hostnamectl` は永続的な変更を行います。 | `hostnamectl` |
| **`/etc/hostname` の手動編集**| このファイルを手動で編集してもホスト名は永続的に変更できますが、`hostnamectl` は関連サービスに変更を即座に通知するなど、よりクリーンで安全な方法です。 | `hostnamectl` |

## よく連携されるコマンド

`hostnamectl` でホスト名を変更した後は、新しいホスト名が正しく名前解決できるかを確認するために、`ping` や `dig` といったコマンドと連携してテストすることがよくあります。

### シナリオ例: ホスト名変更後の疎通確認 (インフラ構築・運用視点)

* **目的**: ホスト名を変更した後、その新しい名前で自身に `ping` が通るかを確認する。
* **連携コマンド**: `ping`
* **解説**: `/etc/hosts` ファイルが正しく設定されていれば、新しいホスト名で `ping` を打つとループバックアドレス (`127.0.0.1` など) から応答が返ってきます。
* **コマンド例**:

```bash
# 1. ホスト名を 'web-server-01' に永続的に変更
sudo hostnamectl set-hostname web-server-01

# 2. 新しいホスト名でpingを実行して確認
ping -c 4 web-server-01
```

## オプション説明 (`hostnamectl` のサブコマンド)

| オプション | 説明 |
| :--- | :--- |
| **情報表示** | |
| ⭐ `status` | (デフォルト) 現在のホスト名設定（Static, Prettyなど）、マシンID、OSバージョンなどをまとめて表示します。 |
| **設定** | |
| ⭐ `set-hostname <name>`| **静的なホスト名**を永続的に設定します。 |
| `set-pretty <name>` | より自由な形式（UTF-8、スペースなどを含む）で設定できる、表示用の名前を設定します。 |
| `set-chassis <type>`| シャーシタイプ (`vm`, `container`, `server`, `laptop`など) を設定します。 |
| `set-deployment <env>`| デプロイ環境 (`production`, `staging`, `development`など) を設定します。 |

## よく使われるオプション組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: 新しく構築したサーバーに、永続的なホスト名を設定する。
* **組み合わせ**: `sudo hostnamectl set-hostname <new-hostname>`
* **解説**: Ansibleなどの構成管理ツールや、サーバーの初期セットアップスクリプト内で、サーバーの役割に応じたホスト名を確実に設定します。これにより、サーバーの識別と管理が容易になります。
* **例**:

```bash
# 新しいWebサーバーのホスト名を 'prod-web-01.example.com' に設定
sudo hostnamectl set-hostname prod-web-01.example.com

# 設定された内容を確認
hostnamectl status
```

### 2. ブルーチーム視点

* **タスク**: ホストのアイデンティティを包括的に確認する。
* **組み合わせ**: `hostnamectl status`
* **解説**: インシデント調査の初期段階で、ホストの `Static` なホスト名と `Transient` なホスト名が異なっていないかなどを確認します。もし `Transient` ホスト名が見慣れないものに設定されている場合、DHCPスプーフィング攻撃などの可能性を疑うきっかけになります。
* **例**:

```bash
# 調査対象サーバーの全ホスト名情報を確認
hostnamectl status
```

### 3. レッドチーム視点

* **タスク**: **防御回避 (Defense Evasion)** の一環として、侵害したホストの重要性を隠蔽する。
* **組み合わせ**: `sudo hostnamectl set-hostname <innocuous-name>`
* **解説**: 攻撃者が `prod-ad-controller` のような名前の重要なサーバーのroot権限を奪取した場合、そのホスト名を `dev-test-03` のような無害な名前に変更することがあります。これにより、監視システムなどがそのサーバーの本来の重要性に気づきにくくなり、攻撃者が発見されるまでの時間を稼ぐことができます。
* **例**:

```bash
# データベースサーバーのホスト名を一般的な名前に偽装
sudo hostnamectl set-hostname generic-worker-05
```

## エラーメッセージとトラブルシューティング

* 一般的なエラーは [Linux共通のトラブルシューティング](./troubleshooting_common_errors.md) を参照。

1. **エラーメッセージ例 1**: `Must be root to change settings.`
    * **考えられる原因**: `set-hostname` などの設定変更コマンドを、一般ユーザー権限で実行しようとしました。
    * **解決策**: コマンドの前に `sudo` を付けて実行してください。

## 環境変数と設定ファイル

* `hostnamectl` の動作に直接影響する環境変数は通常ありません。
* 一般的な環境変数・設定ファイルは [Linux共通の環境変数・設定ファイル](./environment_and_config.md) を参照。

### 関連する設定ファイル

* **`/etc/hostname`**: `set-hostname` で設定された静的ホスト名が保存されます。
* **`/etc/machine-info`**: `set-pretty`, `set-chassis` などで設定されたメタデータが保存されます。

## セキュリティに関する考慮事項

### 脆弱性と悪用事例

* **脆弱性**: `hostnamectl` 自体ではなく、その出力が**情報漏洩**に繋がります。
* **悪用シナリオ**: `hostnamectl` で設定されるホスト名や各種メタデータ（デプロイ環境、設置場所など）は、攻撃者にとってそのサーバーの役割や価値を判断するための重要な情報源です。

### GTFOBins / LOLBAS における利用例

`hostnamectl` はOSの構成を変更するコマンドであり、直接的なコード実行や権限昇格の機能を持たないため、GTFOBinsにはリストされていません。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: サーバーの役割が直接的に推測できないような、無機質な命名規則を採用することを検討する。`sudoers` の設定で、一般ユーザーに `hostnamectl` の実行権限を安易に与えない。
* **Detection (検知)**: `auditd` やファイル整合性監視(FIM)ツールで、`/etc/hostname` や `/etc/machine-info` ファイルへの変更を監視する。

## 注意点・補足

* **再起動不要**: `hostnamectl` で行った変更は、関連する設定ファイルに書き込まれ、システムサービスに即座に通知されるため、**OSを再起動しなくても反映されます**。



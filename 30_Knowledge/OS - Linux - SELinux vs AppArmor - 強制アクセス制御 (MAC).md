---
tags:
  - 'selinux'
  - 'apparmor'
  - 'mac'
  - 'security'
  - 'linux'
  - 'sudo'
  - 'capabilities'
  - 'chcon'
  - 'aa-status'
created: 2025-06-28 14:24
modified: 2026-01-18 15:49
environment:
  - OS/Linux
vulnearability: []
knowledge_category: OS
  - 'sudo'
  - 'capabilities'
  - 'chcon'
  - 'aa-status'
---

# OS - Linux - SELinux vs AppArmor - 強制アクセス制御 (MAC)

## 概要

`SELinux` と `AppArmor` は、Linuxカーネルのセキュリティモジュール (LSM) を利用した**強制アクセス制御 (Mandatory Access Control, MAC)** システムです。これらは、従来のユーザー/グループベースの任意アクセス制御 (DAC) を補強し、たとえ `root` 権限で実行されているプロセスであっても、事前に定義された**セキュリティポリシー**によって許可された操作しか行えないように制限します。

Webサーバーが侵害されても、データベースファイルへのアクセスや、外部への不審な通信を禁止するなど、**侵害の影響を封じ込める**ための強力な防衛機構です。

(出自: `SELinux` は RHEL/CentOS系に、`AppArmor` は Debian/Ubuntu系に標準で組み込まれる)

## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 |
| :--- | :--- |:---|
| ⭐ **SUID / Capabilities** | SUIDやケーパビリティが特定のプロセスに**権限を与える**昇格モデルであるのに対し、MACは全てのプロセスに対して**制限を課す**強制モデルです。 | MAC (より包括的) |

## よく連携されるコマンド

これらのMACシステムは、`auditd` や `journalctl` と密接に連携し、ポリシー違反のイベントをログに記録します。

### シナリオ例: SELinuxの拒否ログを分析してポリシーを修正する (ブルーチーム視点)

* **目的**: アプリケーションがSELinuxによってブロックされている原因を特定し、それを許可するためのポリシーを作成する。
* **連携コマンド**: `ausearch`, `audit2allow`
* **解説**: まず `ausearch` でAVC (Access Vector Cache) 拒否ログを検索します。次に、そのログを `audit2allow` に渡すことで、アクセスを許可するためのカスタムポリシーモジュールを対話的に生成できます。
* **コマンド例**:

    ```bash
    # 1. SELinuxの拒否ログを検索
    sudo ausearch -m avc -ts recent

    # 2. ログから許可ルールを作成
    sudo ausearch -m avc -ts recent | sudo audit2allow -M my-app-policy

    # 3. 作成したポリシーモジュールをインストール
    sudo semodule -i my-app-policy.pp
    ```

## オプション説明 (主要な管理コマンド)

### SELinux

| コマンド | 説明 |
| :--- | :--- |
| ⭐ `sestatus` | SELinuxの現在の状態（モード、ポリシー名など）を表示します。 |
| ⭐ `getenforce`/`setenforce`| 現在のモードを表示 (`getenforce`)、または一時的に変更 (`setenforce [0\|1]`) します。 |
| ⭐ `restorecon` | ファイルのコンテキストを、ポリシーで定められた**デフォルト値に復元**します。 |
| `chcon` | ファイルのコンテキストを**一時的に**変更します。 |
| `semanage` | ファイルコンテキストのデフォルト設定など、**永続的な**ポリシーを変更します。 |

### AppArmor

| コマンド | 説明 |
| :--- | :--- |
| ⭐ `aa-status` | AppArmorの現在の状態と、ロードされているプロファイルの一覧を表示します。 |
| ⭐ `aa-enforce <profile>`| プロファイルを強制モード (enforce) に設定します。 |
| `aa-complain <profile>`| プロファイルを警告モード (complain) に設定します。違反をブロックせず、ログに記録します。 |
| `aa-genprof <executable>`| 対話的にアプリケーションのプロファイルを**生成**します。 |
| `aa-logprof` | ログをスキャンし、既存のプロファイルを対話的に**更新**します。 |

## よく使われるオプション組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク (SELinux)**: Webサーバー用に新しいディレクトリを作成し、コンテンツを配置したが、403 Forbiddenエラーになる。
* **組み合わせ**: `restorecon -Rv <directory>`
* **解説**: ファイルのSELinuxコンテキストが間違っているのが原因。`restorecon` で正しいコンテキスト (`httpd_sys_content_t`) を再帰的に設定します。
* **例**:

    ```bash
    # /srv/www ディレクトリのコンテキストを再帰的に復元
    sudo restorecon -Rv /srv/www
    ```

### 2. ブルーチーム視点

* **タスク**: 侵害の封じ込めと検知。
* **組み合わせ**: (監査ログの監視) `ausearch -m avc` / `journalctl | grep "apparmor="`
* **解説**: MACは、攻撃者が1つのプロセスを乗っ取っても、そこから被害を拡大させるのを防ぐ最後の防衛線です。Webサーバープロセスから `/etc/passwd` へのアクセス試行などはブロックされ、監査ログに詳細に記録されます。
* **例**:

    ```bash
    # SELinuxの拒否ログをリアルタイムで監視
    sudo ausearch -m avc -ts recent
    ```

### 3. レッドチーム視点

* **タスク**: 侵入後の偵察として、MACの状態を確認する。
* **組み合わせ**: `sestatus`, `aa-status`
* **解説**: 攻撃者は侵入後、まずこれらのコマンドでMACが有効か (`Enforcing`)、それとも無効か (`Disabled`/`Permissive`) を確認します。Enforcingモードであれば、攻撃者は乗っ取ったプロセスの権限の範囲内でしか活動できず、攻撃の難易度は劇的に上がります。
* **例**:

    ```bash
    # SELinuxの状態を確認
    sestatus
    ```

## エラーメッセージとトラブルシューティング

* 一般的なエラーは [Linux共通のトラブルシューティング](./troubleshooting_common_errors.md) を参照。

1. **現象**: **パーミッションは正しいはずなのに、ファイルにアクセスできない。**
    * **考えられる原因**: SELinuxまたはAppArmorのポリシーによってアクセスが拒否されています。
    * **解決策**: `ausearch -m avc -ts recent` (SELinux) または `journalctl -f | grep "apparmor="` (AppArmor) を実行して、拒否ログを確認し、ポリシーを修正（`audit2allow`など）するか、コンテキストを修正（`restorecon`）します。

## 環境変数と設定ファイル

* 一般的な環境変数・設定ファイルは [Linux共通の環境変数・設定ファイル](./environment_and_config.md) を参照。

### 関連する設定ファイル

* **SELinux**: `/etc/selinux/config` (メイン設定), `/etc/selinux/targeted/contexts/files/` (ファイルコンテキスト定義)
* **AppArmor**: `/etc/apparmor.d/` (プロファイルが格納されるディレクトリ)

## セキュリティに関する考慮事項

### 脆弱性と悪用事例

* **脆弱性**: MACシステム自体よりも、**ポリシーの設定ミス**がリスクとなります。
* **悪用シナリオ**: ポリシーが緩すぎると、本来ブロックされるべき攻撃（Webサーバーからのリバースシェルなど）が許可されてしまいます。逆に複雑すぎて管理者が `setenforce 0` で無効化してしまうと、防御機構が全く機能しなくなります。

### GTFOBins / LOLBAS における利用例

これらのツールは防御機構であり、GTFOBinsにはリストされていません。しかし、多くの攻撃手法は、SELinuxやAppArmorが `Disabled` または `Permissive` モードであることを前提としています。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: 可能な限り `Enforcing` モードで運用する。ポリシーの穴を最小限にするため、定期的に監査する。
* **Detection (検知)**: MACの拒否ログをSIEMに転送し、監視する。予期せぬ大量の拒否ログは、攻撃の兆候である可能性があります。

## 注意点・補足

* **コンテナセキュリティ**: DockerやPodmanなどのコンテナ技術は、セキュリティの基盤としてSELinuxやAppArmorを積極的に利用します。コンテナプロセスに対して、ホストOSとは別の、より厳しいMACプロファイルを適用することで、コンテナからのエスケープ攻撃を防ぎます。

---


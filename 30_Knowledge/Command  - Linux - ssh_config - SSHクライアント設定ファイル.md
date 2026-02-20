---
tags:
  - ssh
  - ssh_config
  - security
  - ssh-keygen
  - ssh-copy-id
created: 2025-06-29 15:02
modified: 2026-02-20 15:43
environment: [OS/Linux]
vulnearability: []
knowledge_category: Command
---

# Command  - Linux - ssh_config - SSHクライアント設定ファイル

## 概要

`ssh_config` は、SSHクライアント (`ssh`, `scp`, `sftp`など) の動作をホストごとにカスタマイズするための設定ファイルです。このファイルに接続先のホスト名、ユーザー名、ポート番号、使用する秘密鍵などをあらかじめ定義しておくことで、毎回コマンドラインで長いオプションを指定する必要がなくなり、SSH接続を大幅に簡略化、効率化できます。

(出自: `openssh-client` パッケージに含まれる)

## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 |
| :--- | :--- |:---|
| ⭐ **`sshd_config`** | **サーバー側**のSSHデーモン (`sshd`) の設定ファイルです。`ssh_config` は**クライアント側**の設定ファイルであり、役割が全く異なります。 | - |
| **コマンドラインオプション**| `ssh -p 2222 ...` のように、コマンドで直接指定したオプションが**最優先**されます。`ssh_config` は、それらのデフォルト値を定義します。 | - |

## よく連携されるコマンド

`ssh_config` は `ssh` コマンドと密接に連携し、その動作を定義します。

### シナリオ例: 踏み台サーバー経由の接続を簡略化する (インフラ構築・運用視点)

* **目的**: ファイアウォールの内側にあるDBサーバーに、踏み台サーバー (`bastion`) を経由して簡単に接続できるようにする。
* **連携コマンド**: `ssh`
* **解説**: `~/.ssh/config` に `ProxyJump` ディレクティブを設定することで、`ssh prod-db-01` と打つだけで、自動的に `bastion` サーバーを経由して `db.internal` に接続してくれます。
* **コマンド例 (`~/.ssh/config` の設定)**:

		```bash
    # 踏み台サーバー

    Host bastion

        HostName bastion.example.com

        User admin

        IdentityFile ~/.ssh/keys/bastion_key.pem

    # 踏み台経由で接続するDBサーバー

    Host prod-db-01

        HostName db.internal

        User db_user

        IdentityFile ~/.ssh/keys/internal_db_key.pem

        ProxyJump bastion

    ```


## オプション説明 (主要なディレクティブ)

| オプション | 説明 |
| :--- | :--- |
| **接続定義** | |
| ⭐ `Host` | 設定を適用するホストを識別するためのパターン（エイリアス）。`*` (任意文字列) や `?` (任意1文字) のワイルドカードが使えます。 |
| ⭐ `HostName` | 実際の接続先ホスト名またはIPアドレス。 |
| ⭐ `User` | 接続に使用するユーザー名。 |
| ⭐ `Port` | 接続先のポート番号。デフォルトは22です。 |
| **認証** | |
| ⭐ `IdentityFile`| 認証に使用する秘密鍵のパス。 |
| `IdentitiesOnly`| `yes` に設定すると、`IdentityFile` で明示的に指定した鍵のみを認証に使用します。 |
| **高度な接続** | |
| ⭐ `ProxyJump` | 踏み台サーバー（Bastion Host）の `Host` エイリアスを指定します。 |
| `ForwardAgent`| エージェントフォワーディングを有効にします。踏み台サーバーからさらに別のサーバーに接続する際に便利ですが、セキュリティリスクも伴います。 |
| `ServerAliveInterval`| サーバーとの接続を維持するために、指定した秒数ごとにnullパケットを送信します。 |

## よく使われるオプション組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: 複数のサーバーへの接続情報をエイリアスとして登録し、日々の管理業務を効率化する。
* **組み合わせ**: `Host`, `HostName`, `User`, `Port`, `IdentityFile`
* **解説**: 上記「よく連携されるコマンド」のシナリオ例を参照。この設定により、`ssh prod-web-01` のような短いコマンドで、複雑なオプションを付けずに接続できます。
* **例**:

		```bash
    # config設定後

    ssh prod-db-01

    ```


### 2. ブルーチーム視点

* **タスク**: 調査対象ホストへの接続方法を標準化し、安全性を確保する。
* **組み合わせ**: `Host`, `HostName`, `User`, `IdentityFile`, `PasswordAuthentication no`
* **解説**: 調査用ワークステーションの `ssh_config` に専用の設定を記述しておきます。`PasswordAuthentication no` をクライアント側で指定することで、誤ってパスワード認証を試みないように強制できます。
* **例 (`~/.ssh/config`)**:

		```bash

    Host compromised-host

        HostName 203.0.113.80

        User readonly_analyst

        IdentityFile ~/.ssh/keys/forensics_key.pem

        PasswordAuthentication no

    ```


### 3. レッドチーム視点

* **タスク**: **偵察 (Reconnaissance)**。侵入したホストの `~/.ssh/config` ファイルを調査する。
* **組み合わせ**: `cat ~/.ssh/config`
* **解説**: このファイルには、そのユーザーが普段接続している他のサーバーの**ホスト名、IPアドレス、ユーザー名、使用している鍵のパス**といった、内部ネットワークの構造や重要なサーバーの情報を把握するための貴重な情報が含まれています。
* **例**:

		```bash
    # 侵入したユーザーのssh configを調査

    cat /home/user1/.ssh/config

    ```


## エラーメッセージとトラブルシューティング

* 一般的なエラーは [Linux共通のトラブルシューティング](OS%20%20-%20Linux%20-%20troubleshooting_common_errors%20-%20Linux共通エラー対応ガイド.md) を参照。

1. **エラーメッセージ例 1**: `Bad configuration option: ...`
		* **考えられる原因**: `ssh_config` ファイル内のディレクティブ名にタイプミスがあります。
		* **解決策**: ディレクティブのスペルが正しいか (`man ssh_config` で確認) を確認してください。

2. **エラーメッセージ例 2**: `Bad owner or permissions on .../.ssh/config`
		* **考えられる原因**: `~/.ssh/config` ファイルのパーミッションが緩すぎます（例: グループや他人が書き込み可能になっている）。
		* **解決策**: `chmod 600 ~/.ssh/config` を実行して、パーミッションを所有者のみ読み書き可能に修正してください。

## 環境変数と設定ファイル

* 一般的な環境変数・設定ファイルは [Linux共通の環境変数・設定ファイル](./environment_and_config.md) を参照。

### 関連する設定ファイル

* ⭐ **`~/.ssh/config`**: ユーザーごとのクライアント設定ファイル。
* **`/etc/ssh/ssh_config`**: システム全体のクライアント設定ファイル。

## セキュリティに関する考慮事項

### 脆弱性と悪用事例

* **脆弱性**: `ssh_config` 自体ではなく、その**内容**が**情報漏洩**に繋がります。
* **悪用シナリオ**: 攻撃者がユーザーアカウントを侵害した場合、`~/.ssh/config` を読み取ることで、内部ネットワークの構成や、他のサーバーへのアクセス方法（ホスト名、ユーザー名など）を偵察し、ラテラルムーブメント（横展開）の足がかりとします。

### GTFOBins / LOLBAS における利用例

`ssh_config` は設定ファイルのため、GTFOBinsにはリストされていません。しかし、GTFOBinsにリストされている `ssh` コマンドの悪用は、この設定ファイルから得られた情報を元に行われることが多いです。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: `~/.ssh/config` ファイルのパーミッションを `600` に設定する。
* **Detection (検知)**: FIMツールで `~/.ssh/config` ファイルへの読み取りアクセスや変更を監視する。

## 注意点・補足

* **ワイルドカードの活用**: `Host *.example.com` のようにワイルドカードを使うと、特定のドメインやIPアドレス範囲に共通の設定を適用でき、記述を簡潔に保てます。

---


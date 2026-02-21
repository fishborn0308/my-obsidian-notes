---
tags:
  - docker_network_connect
  - docker
  - networking
  - container
created: 2025-06-29 15:02
modified: 2026-02-20 17:36
environment:
vulnearability: []
knowledge_category: Command
---

# Command - docker - docker network connect - 実行中のコンテナをネットワークに接続する

## 概要

`docker network connect` は、**実行中のコンテナ**を、指定したDockerネットワークに追加で接続するためのコマンドです。コンテナは複数のネットワークに所属できるため、このコマンドを使うことで、コンテナを停止・再作成することなく、動的に新しい通信経路を追加できます。

(出自: `docker` パッケージに含まれる)

## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 |
| :--- | :--- |:---|
| ⭐ **`docker run --network`** | **コンテナの作成・起動時**に、所属するネットワークを指定します。`network connect` は**すでに実行中のコンテナ**に対して、後からネットワークを追加する点が異なります。 | `docker run` (作成時) |
| **`docker network disconnect`** | `connect` の**逆の操作**。実行中のコンテナを指定したネットワークから切り離します。 | - |

## よく連携されるコマンド

`docker network connect` は、`docker network inspect` と連携して、接続後の状態を確認するワークフローでよく使われます。

### シナリオ例: コンテナに動的にネットワークを追加し、状態を確認する (インフラ構築・運用視点)

* **目的**: 実行中のコンテナ `my-webapp` を、`backend-net` ネットワークに追加で接続し、新しいIPアドレスが割り当てられたことを確認する。
* **連携コマンド**: `docker inspect`
* **解説**: `connect` コマンドでネットワークに追加した後、`docker inspect` を使ってコンテナの詳細情報を表示し、`NetworkSettings.Networks` セクションに新しいネットワークが追加されていることを確認します。
* **コマンド例**:

		```bash
    # 1. 実行中の 'my-webapp' を 'backend-net' に接続

    docker network connect backend-net my-webapp

    # 2. 接続後のネットワーク状態を確認

    docker inspect my-webapp | jq '.[0].NetworkSettings.Networks'

    ```


## オプション説明

| オプション | 説明 |
| :--- | :--- |
| **接続設定** | |
| ⭐ `<network>` `<container>`| 接続対象の**ネットワーク名**と**コンテナ名**（またはID）を指定します。 |
| `--alias <name>` | 接続するネットワーク内で、コンテナが持つ別名を指定します。他のコンテナはこのエイリアス名を使って名前解決できます。 |
| `--ip <ipv4_address>` | 接続するネットワーク内で、コンテナに静的なIPv4アドレスを割り当てます。 |

## よく使われるオプション組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: 稼働中のWebサーバーコンテナを、メンテナンスのために一時的にDBネットワークに接続する。
* **組み合わせ**: `docker network connect <network_name> <container_name>`
* **解説**: `frontend` と `backend` ネットワークが分離されている環境で、Webコンテナを停止せずに `backend` ネットワークに追加接続し、疎通確認やデータ移行作業を行います。
* **例**:

		```bash
    # `my-webapp` コンテナを `backend_network` に接続

    docker network connect backend_network my-webapp

    ```


### 2. ブルーチーム視点

* **タスク**: 侵害されたコンテナの通信を、隔離された監視ネットワークに接続して分析する。
* **組み合わせ**: `docker network create honeypot-net` → `docker network connect honeypot-net <suspicious_container>`
* **解説**: 隔離された調査用のネットワークを作成し、不審なコンテナをそのネットワークに接続します。このネットワークには、`tcpdump` を実行する監視コンテナのみを配置し、マルウェアがどのような通信を試みるかを安全に監視します。
* **例**: -

### 3. レッドチーム視点

* **タスク**: **ラテラルムーブメント (横展開)**。攻撃用コンテナをターゲットの内部ネットワークに接続する。
* **組み合わせ**: `docker network connect <backend_network> <attacker_container>`
* **解説**: Dockerソケットを奪取した後、攻撃用ツールを入れたコンテナを起動し、それを `prod-db-net` のような内部ネットワークに接続します。これにより、ホストのファイアウォールをバイパスし、外部からアクセスできない内部のデータベースコンテナなどへ直接攻撃を仕掛けます。
* **例**:

		```bash
    # 攻撃用コンテナを起動

    docker run -itd --name attacker-box alpine

    # ターゲットのDBネットワークに接続

    docker network connect prod-db-net attacker-box

    ```


## エラーメッセージとトラブルシューティング

* 一般的なエラーは [Linux共通のトラブルシューティング](../linux/troubleshooting_common_errors.md) を参照。

1. **エラーメッセージ例 1**: `Error response from daemon: container <container_name> is not running`
		* **考えられる原因**: 接続しようとしたコンテナが停止しています。
		* **解決策**: `docker start <container_name>` でコンテナを起動してから、再度 `connect` コマンドを実行してください。

## 環境変数と設定ファイル

* `docker network connect` の動作に直接影響する環境変数や設定ファイルは通常ありません。
* 一般的な環境変数・設定ファイルは [Linux共通の環境変数・設定ファイル](../linux/environment_and_config.md) を参照。

## セキュリティに関する考慮事項

### 脆弱性と悪用事例

* **脆弱性**: **ネットワークセグメンテーションの破壊**。
* **悪用シナリオ**: 攻撃者が `docker` コマンドの実行権限を得た場合、`docker network connect` を使って、本来分離されているべきフロントエンドとバックエンドのネットワークを接続し、セキュリティモデルを破壊して内部の機密情報にアクセスします。

### GTFOBins / LOLBAS における利用例

`docker network connect` は `docker` コマンドの一部として扱われます。`sudo docker` の権限があれば、任意のネットワーク操作が可能です。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: Dockerソケットへのアクセス権限を厳格に管理する。
* **Detection (検知)**: `auditd` で `docker` コマンドの実行、特に `network connect` のような構成変更を監視する。

## 注意点・補足

* **名前解決**: コンテナを同じDockerネットワークに接続すると、Dockerに組み込まれたDNSサーバーにより、コンテナ名や `--alias` で指定した名前を使って、コンテナ間で名前解決ができるようになります。

---

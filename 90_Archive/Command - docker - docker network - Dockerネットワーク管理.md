---
tags:
  - docker_network
  - docker
  - networking
  - container
created: 2025-06-29 15:02
modified: 2026-02-22 15:13
environment:
vulnearability: []
knowledge_category: Command
---

# Command - docker - docker network - Dockerネットワーク管理

## 概要

Dockerネットワークは、コンテナが相互に、またはDockerホストや外部ネットワークと通信するための基盤を提供する機能です。デフォルトでも `bridge` ネットワークが用意されていますが、アプリケーションごとに**カスタムネットワークを作成してコンテナを接続する**ことが、コンテナ間の通信を安全に分離・管理するためのベストプラクティスです。

`docker network` コマンド群は、これらの仮想ネットワークの作成、一覧表示、詳細調査、削除などを行うためのインターフェイスを提供します。

(出自: `docker` パッケージに含まれる)

## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 |
| :--- | :--- |:---|
| ⭐ **`ip link`** | ホストOSの**ネットワークデバイス**を管理します。`docker network` はDockerが管理する**論理的な仮想ネットワーク**を管理します。 | - |

## 🌐 主要なサブコマンド

Dockerネットワークの管理は、以下のサブコマンドを組み合わせて行います。

| コマンド | 概要 | 詳細 |
| :--- | :--- | :--- |
| ⭐ **`docker network ls`** | ネットワークの一覧を表示する | [./docker_network_ls.md](Command%20-%20docker%20-%20docker%20network%20ls%20-%20Dockerネットワークを一覧表示する.md) |
| ⭐ **`docker network create`**| 新しいネットワークを作成する | [./docker_network_create.md](Command%20-%20docker%20-%20docker%20network%20create%20-%20新しいDockerネットワークを作成する.md) |
| ⭐ **`docker network inspect`**| ネットワークの詳細情報を表示する | [./docker_network_inspect.md](Command%20-%20docker%20-%20docker%20network%20inspect%20-%20Dockerネットワークの詳細情報を表示する.md) |
| ⭐ **`docker network connect`**| 実行中のコンテナをネットワークに接続する | [./docker_network_connect.md](Command%20-%20docker%20-%20docker%20network%20connect%20-%20実行中のコンテナをネットワークに接続する.md) |
| ⭐ **`docker network disconnect`**| コンテナをネットワークから切り離す | [./docker_network_disconnect.md](Command%20-%20docker%20-%20docker%20network%20disconnect%20-%20コンテナをネットワークから切り離す.md) |
| **`docker network rm`** | １つ以上のネットワークを削除する | [./docker_network_rm.md](Command%20-%20docker%20-%20docker%20network%20rm%20-%201つ以上のDockerネットワークを削除する.md) |
| **`docker network prune`** | 未使用のネットワークを一括削除する | [./docker_network_prune.md](Command%20-%20docker%20-%20docker%20network%20prune%20-%20未使用のDockerネットワークを一括削除する.md) |

---

## よく使われるオプション組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: 2層アプリケーション（Webサーバーとデータベース）を、安全に分離されたネットワークで構築する。
* **組み合わせ**: `docker network create` → `docker run --network`
* **解説**: 専用ネットワークを作成し、各コンテナをそのネットワークに接続します。これにより、DBコンテナはWebコンテナからのみアクセス可能となり、セキュリティが向上します。
* **例**:

		```bash
    # 1. アプリケーション専用ネットワークを作成

    docker network create my-app-net

    # 2. データベースコンテナを起動し、ネットワークに接続 (ポートは公開しない)

    docker run -d --name db --network my-app-net -e POSTGRES_PASSWORD=secret postgres:15

    # 3. Webサーバーコンテナを同じネットワークに接続し、ポートを公開

    docker run -d --name webapp --network my-app-net -p 8080:80 -e DATABASE_HOST=db my-webapp-image

    ```




## 2. ブルーチーム視点

* **タスク**: 侵害されたコンテナの影響範囲を特定する。
* **組み合わせ**: `docker inspect <container>` → `docker network inspect <network>`
* **解説**: まず `docker inspect` で侵害されたコンテナがどのネットワークに接続しているかを特定します。次に `docker network inspect` でそのネットワークを調査し、他にどのコンテナが同じネットワークセグメントに存在するかをリストアップします。これにより、攻撃が横展開する可能性のある範囲を特定できます。
* **例**: -

## 3. レッドチーム視点

* **タスク**: 侵入したホストの内部ネットワーク構成を偵察する。
* **組み合わせ**: `docker network ls` → `docker network inspect <network>`
* **解説**: `docker network ls` で `prod-db-net` のような興味深い名前のネットワークを探し、`inspect` で詳細を調査します。これにより、外部に公開されていないデータベースなどのコンテナ名と内部IPアドレスを特定し、次の攻撃対象リストを作成します。
* **例**: -

# エラーメッセージとトラブルシューティング

* 一般的なエラーは [Linux共通のトラブルシューティング](../linux/troubleshooting_common_errors.md) を参照。

1. **エラーメッセージ例 1**: `Error response from daemon: network <network_name> is in use by container <container_id>`
		* **考えられる原因**: 削除しようとしているネットワークが、まだ存在するコンテナによって使用されています。
		* **解決策**: エラーメッセージに表示されたコンテナIDを `docker rm` で先に削除してください。

# 環境変数と設定ファイル

* `docker network` の動作に直接影響する環境変数は通常ありません。
* 一般的な環境変数・設定ファイルは [Linux共通の環境変数・設定ファイル](../linux/environment_and_config.md) を参照。

# セキュリティに関する考慮事項

## 脆弱性と悪用事例

* **脆弱性**: **不適切なネットワークセグメンテーション**。
* **悪用シナリオ**: 全てのコンテナをデフォルトの `bridge` ネットワークに接続すると、あるコンテナが侵害された場合に、他の無関係なコンテナにも被害が及ぶ（ラテラルムーブメント）可能性があります。

## GTFOBins / LOLBAS における利用例

`docker network` は `docker` コマンドの一部として扱われます。`sudo docker` の権限があれば、任意のネットワーク操作が可能です。

## 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: **アプリケーションごとに専用のネットワークを作成すること**は、コンテナセキュリティの基本です。
* **Detection (検知)**: `docker network ls` や `docker network inspect` を定期的に実行し、意図しないネットワークが作成されていないか、コンテナが不適切なネットワークに接続されていないかを監査する。

# 注意点・補足

* **`host` ネットワークのリスク**: `--network host` を指定すると、コンテナのネットワーク隔離が無効になります。コンテナ内のプロセスがホストのネットワークサービスを乗っ取る可能性があるため、利用は慎重に行うべきです。

---


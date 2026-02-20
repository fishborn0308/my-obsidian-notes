---
tags:
  - docker_network_disconnect
  - docker
  - networking
  - container
created: 2025-06-29 15:02
modified: 2026-02-20 17:36
environment:
vulnearability: []
knowledge_category: Command
---

# Command - docker - docker network disconnect - コンテナをネットワークから切り離す

## 概要

`docker network disconnect` は、**実行中のコンテナ**を、指定したDockerネットワークから切り離すためのコマンドです。コンテナは複数のネットワークに所属できるため、このコマンドを使うことで、コンテナを停止させることなく、特定のネットワークとの接続だけを動的に解除できます。主に、一時的なメンテナンスや、セキュリティ上の理由でコンテナを特定のネットワークセグメントから隔離する際に利用されます。

(出自: `docker` パッケージに含まれる)

## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 |
| :--- | :--- |:---|
| ⭐ **`docker network connect`** | `disconnect` の**逆の操作**。実行中のコンテナをネットワークに追加で接続します。 | - |
| **`docker stop`** | コンテナを停止すると、接続されていた**全てのネットワーク**から自動的に切り離されます。`disconnect` は、コンテナを実行させ続けたまま**特定のネットワーク**との接続だけを解除します。 | - |

## よく連携されるコマンド

`docker network disconnect` は、`docker network connect` とセットで、コンテナのネットワーク所属を動的に変更するワークフローで使われます。

### シナリオ例: コンテナの所属ネットワークを変更する (インフラ構築・運用視点)

* **目的**: メンテナンスのため、Webコンテナを一時的に `production-net` から切り離し、`maintenance-net` にのみ接続する。
* **連携コマンド**: `docker network connect`
* **解説**: コンテナを停止することなく、所属ネットワークを切り替えます。
* **コマンド例**:

		```bash
		# 1. webappコンテナをメンテナンスネットワークに接続

		docker network connect maintenance-net webapp

		# 2. webappコンテナを本番ネットワークから切り離す

		docker network disconnect production-net webapp

		```

## オプション説明

| オプション | 説明 |
| :--- | :--- |
| **接続設定** | |
| ⭐ `<network>` `<container>`| 切り離す対象の**ネットワーク名**と**コンテナ名**（またはID）を指定します。 |
| **動作制御** | |
| `-f`, `--force` | コンテナがネットワークから正常に切り離されるのを待たず、強制的に実行します。 |

## よく使われるオプション組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: 多機能コンテナの一部ネットワーク接続を一時的に無効化する。
* **組み合わせ**: `docker network disconnect <network_name> <container_name>`
* **解説**: `frontend` と `backend` の2つのネットワークに接続されているコンテナを、一時的に `frontend` から切り離して外部からのアクセスを遮断し、`backend` ネットワークとの通信だけを維持します。
* **例**:

		```bash
		# `multi-role-container` を `frontend_net` から切り離す

		docker network disconnect frontend_net multi-role-container

		```

## 2. ブルーチーム視点

* **タスク**: **侵害されたコンテナをネットワークから隔離する（封じ込め）**。
* **組み合わせ**: `docker network disconnect <network_name> <compromised_container>`
* **解説**: 侵害されたコンテナが同じネットワーク上の他のコンテナへ攻撃（ラテラルムーブメント）を広げるのを防ぐため、即座に内部ネットワークから切り離します。コンテナ自体は稼働させ続けたままなので、ライブフォレンジック調査を継続しつつ、被害の拡大を防げます。
* **例**:

		```bash
		# 侵害されたWebサーバーを、DBが接続されている `backend_db_net` から緊急で切り離す

		docker network disconnect backend_db_net compromised_webapp

		```

## 3. レッドチーム視点

* **タスク**: **破壊活動 (Sabotage)**。アプリケーションの正常な通信を妨害する。
* **組み合わせ**: `docker network disconnect <network_name> <critical_container>`
* **解説**: 攻撃者が `docker` コマンドの実行権限を得た場合、アプリケーションを構成するコンテナ間の接続 (`webapp` と `db` など) を `disconnect` で切断し、サービスを機能不全に陥らせます。
* **例**: -

# エラーメッセージとトラブルシューティング

* 一般的なエラーは [Linux共通のトラブルシューティング](../linux/troubleshooting_common_errors.md) を参照。

1. **エラーメッセージ例 1**: `Error response from daemon: container <container_id> is not connected to network <network_name>`
		* **考えられる原因**: 指定したコンテナは、指定したネットワークに元々接続されていません。
		* **解決策**: `docker inspect <container_id>` を実行し、`NetworkSettings.Networks` セクションで、コンテナが現在どのネットワークに接続しているかを確認してください。

# 環境変数と設定ファイル

* `docker network disconnect` の動作に直接影響する環境変数や設定ファイルは通常ありません。
* 一般的な環境変数・設定ファイルは [Linux共通の環境変数・設定ファイル](../linux/environment_and_config.md) を参照。

# セキュリティに関する考慮事項

## 脆弱性と悪用事例

* **脆弱性**: **不適切な権限管理**。
* **悪用シナリオ**: `docker` コマンドの実行権限を持つユーザーは、`docker network disconnect` を使って、アプリケーションを構成するコンテナ間の重要な通信経路を切断し、サービス妨害 (DoS) を引き起こすことができます。

## GTFOBins / LOLBAS における利用例

`docker network disconnect` は `docker` コマンドの一部として扱われます。`sudo docker` の権限があれば、任意のネットワーク操作が可能です。

## 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: Dockerソケットへのアクセス権限を厳格に管理する。
* **Detection (検知)**: `auditd` で `docker` コマンドの実行、特に `network disconnect` のような構成変更を監視する。

# 注意点・補足

* **最後のネットワーク**: コンテナが接続している最後のネットワークから `disconnect` すると、そのコンテナはどのネットワークにも接続されていない状態になります。

---


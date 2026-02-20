---
tags:
  - docker_compose_down
  - docker_compose
  - docker
created: 2025-06-29 15:02
modified: 2026-02-20 17:36
environment:
vulnearability: []
knowledge_category: Command
---

# Command - docker - docker compose down - Composeアプリケーションを停止・削除する

## 概要

`docker compose down` は、`docker compose up` で起動したアプリケーションを構成する**コンテナ、ネットワーク、デフォルトボリュームを停止し、削除する**コマンドです。開発やテストのサイクルで、使用済みの環境をクリーンアップし、リソースを解放するために使用されます。単にコンテナを停止するだけでなく、関連するリソースを一括で片付けるための、クリーンで冪等性のある操作です。

(出自: `docker compose` の基本機能)

## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 |
| :--- | :--- |:---|
| ⭐ **`docker compose stop`** | コンテナを**停止**するだけで、**削除はしません**。`start` で再開できます。`down` は完全な撤去です。 | `stop` (一時停止) |
| **`docker system prune`**| **Composeプロジェクトに関係なく**、システム上の未使用リソースを一括で削除します。`down` は特定のComposeプロジェクトにスコープが限定されます。 | - |

## よく連携されるコマンド

`docker compose down` は、CI/CDパイプラインやテストスクリプトにおいて、テスト実行後のクリーンアップ処理の最終ステップとして組み込まれます。

### シナリオ例: テスト環境の完全なクリーンアップ (インフラ構築・運用視点)

* **目的**: CI/CDパイプラインでE2Eテストを実行した後、テスト中に作成された全てのDockerリソースを完全に削除し、次の実行に備える。
* **連携コマンド**: `docker compose up`, `docker compose down`
* **解説**: `up` で環境を構築し、テストを実行した後、`down -v` でボリュームも含めて完全に環境を破棄します。
* **コマンド例**:

		```bash
    # テスト環境をバックグラウンドで起動

    docker compose -f docker-compose.test.yml up -d

    # (テスト実行...)

    # ボリュームも含めてテスト環境を完全に削除

    docker compose -f docker-compose.test.yml down -v

    ```


## オプション説明

| オプション | 説明 |
| :--- | :--- |
| **削除対象の拡張** | |
| ⭐ `-v`, `--volumes` | `docker-compose.yml` の `volumes` セクションで定義された**名前付きボリューム**も一緒に削除します。**データベースのデータなどが消える可能性があるため、使用には注意が必要**です。 |
| `--rmi <type>` | イメージを削除します。`<type>` には `all`(全てのイメージ)または `local`(カスタムビルドされたイメージのみ)を指定します。 |
| `--remove-orphans`| Composeファイルで定義されなくなった「孤児 (orphan)」コンテナを削除します。 |
| **その他** | |
| `-t, --timeout <seconds>`| コンテナの停止を待つタイムアウト秒数を指定します。デフォルトは10秒です。 |

## よく使われるオプション組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: 開発環境のテストが完了したアプリケーション環境をクリーンアップする。
* **組み合わせ**: `docker compose down`
* **解説**: コンテナとネットワークを削除しますが、名前付きボリュームは安全のために残す、最も基本的な使い方です。
* **例**:

		```bash
    # feature-branch-test ディレクトリで作業後、環境を片付ける

    cd ~/projects/feature-branch-test/

    docker compose down

    ```


### 2. ブルーチーム視点

* **タスク**: 侵害されたアプリケーションを迅速かつ完全に無力化する（封じ込め）。
* **組み合わせ**: `docker compose -f <path/to/docker-compose.yml> down`
* **解説**: `-f` でComposeファイルを指定し、対象のアプリケーションを停止・削除します。関連するコンテナとネットワークを一度にシャットダウンすることで、攻撃者の活動を封じ込めます。
* **例**:

		```bash
    # 侵害されたWebアプリケーションのコンテナ群を緊急停止・削除

    docker compose -f /opt/compromised-app/docker-compose.yml down

    ```


### 3. レッドチーム視点

* **タスク**: **痕跡消去 (Anti-Forensics)**。攻撃の踏み台として利用したコンテナ環境を削除する。
* **組み合わせ**: `docker compose down --rmi all -v`
* **解説**: 攻撃者が展開したカスタムツール環境を、コンテナ、ネットワーク、ボリューム、さらには使用したイメージまで含めて全て削除し、フォレンジック調査を困難にしようと試みます。
* **例**: -

## エラーメッセージとトラブルシューティング

* 一般的なエラーは [Linux共通のトラブルシューティング](../linux/troubleshooting_common_errors.md) を参照。

1. **エラーメッセージ例 1**: `ERROR: error while removing network: network <network_name> has active endpoints`
		* **考えられる原因**: Composeが管理するネットワークに、Composeプロジェクト外のコンテナが接続されています。
		* **解決策**: `docker network inspect <network_name>` で接続されているコンテナを確認し、切り離すか停止させてから再度 `down` を実行します。

## 環境変数と設定ファイル

* `docker compose` は `.env` ファイルを読み込みます。
* 一般的な環境変数・設定ファイルは [Linux共通の環境変数・設定ファイル](../linux/environment_and_config.md) を参照。

## セキュリティに関する考慮事項

### 脆弱性と悪用事例

* **脆弱性**: `docker compose down` は**破壊的なコマンド**です。
* **悪用シナリオ**: 攻撃者が `docker` コマンドの実行権限を得た場合、`docker compose down -v` を実行して、本番環境のデータベースボリュームを意図的に削除し、深刻な**サービス妨害 (DoS)** と**データ損失**を引き起こす可能性があります。

### GTFOBins / LOLBAS における利用例

`docker compose` は `docker` コマンドの一部として扱われます。`sudo docker` の権限があれば、任意の操作が可能です。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: 本番環境のデータボリュームは、不用意に削除されないようにバックアップとリストアのプロセスを確立する。Dockerソケットへのアクセス権を厳格に管理する。
* **Detection (検知)**: `docker compose down` の実行を `auditd` で監視し、計画外の環境破棄がないかを確認する。

## 注意点・補足

* **`stop` と `down` の違い**: `stop` は一時停止、`down` は完全な撤去です。この違いを理解することが、意図しないデータ損失を防ぐ上で非常に重要です。
* **冪等性**: `docker compose down` は冪等な操作です。つまり、すでに `down` 済みの環境に対して再度実行してもエラーにはなりません。

---


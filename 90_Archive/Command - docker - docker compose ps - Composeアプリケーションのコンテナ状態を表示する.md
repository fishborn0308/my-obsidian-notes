---
tags:
  - docker_compose_ps
  - docker_compose
  - docker
created: 2025-06-29 15:02
modified: 2026-02-22 15:13
environment:
vulnearability: []
knowledge_category: Command
---

# Command - docker - docker compose ps - Composeアプリケーションのコンテナ状態を表示する

## 概要

`docker compose ps` は、カレントディレクトリの `docker-compose.yml` ファイルに基づいて実行されているコンテナ（サービス）の一覧と、それらの状態(State)、ポートマッピング(Ports)などを表示するコマンドです。`docker compose up` でアプリケーションを起動した後に、各コンテナが意図通りに起動しているか、正常に稼働中か、あるいは問題が発生して停止していないかを素早く確認するために使われます。

(出自: `docker compose` の基本機能)

## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 |
| :--- | :--- |:---|
| ⭐ **`docker ps`** | Dockerホスト上で実行されている**すべてのコンテナ**を一覧表示します。`docker compose ps` は**特定のComposeプロジェクト**の状態だけを確認します。 | `docker compose ps` (Compose環境) |
| **`docker compose top`** | プロジェクトの各サービスの**内部で実行されているプロセス**を表示します。`docker compose ps` はコンテナ**自体**の状態を表示します。 | - |

## よく連携されるコマンド

`docker compose ps` は、アプリケーションの起動とログ確認を行うコマンドと密接に連携します。

### シナリオ例: アプリケーション起動後の状態確認 (インフラ構築・運用視点)

* **目的**: `docker compose up -d` で起動したアプリケーションが、全サービス正常に立ち上がっているかを確認する。
* **連携コマンド**: `docker compose up`, `docker compose logs`
* **解説**: アプリケーションを起動した後、まず `docker compose ps` で全サービスの `State` が `Up` になっているかを確認します。もし `Exited` や `restarting` のサービスがあれば、`docker compose logs` でそのサービスのエラーログを調査します。
* **コマンド例**:

		```bash
    # 1. アプリケーションをバックグラウンドで起動

    docker compose up -d

    # 2. 全サービスの起動状態を確認

    docker compose ps

    # 3. (もし問題があれば) ログを確認

    docker compose logs webapp

    ```




## オプション説明

| オプション | 説明 |
| :--- | :--- |
| **表示制御** | |
| ⭐ `-a`, `--all` | 停止しているコンテナも含め、プロジェクトに関連するすべてのコンテナを表示します。 |
| `-q`, `--quiet` | コンテナIDのみを表示します。他のコマンドと組み合わせる際に便利です。 |
| `--services` | 実行中のサービス名のみを一覧表示します。 |
| `--filter <key=value>`| 特定の条件で表示するコンテナをフィルタリングします。（例: `--filter "status=running"`） |

## よく使われるオプション組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: アプリケーションの全サービスが正常に稼働しているかを確認する。
* **組み合わせ**: `docker compose ps`
* **解説**: `docker compose up -d` でアプリケーションをバックグラウンドで起動した後、このコマンドで全てのサービスが `Up` または `Up (healthy)` 状態になっていることを確認します。
* **例**:

		```bash
    # アプリケーションの起動状態を確認

    docker compose ps

    ```




## 2. ブルーチーム視点

* **タスク**: 不審なアプリケーションの構成要素と、過去の実行履歴を調査する。
* **組み合わせ**: `docker compose -f /path/to/docker-compose.yml ps -a`
* **解説**: `-f` で調査対象のComposeファイルを指定し、`-a` で過去に実行されて今は停止しているコンテナも含めて一覧表示します。短時間だけ実行されてすぐに停止したような不審なコンテナ（ワンショットの攻撃スクリプトなど）の痕跡を発見できる可能性があります。
* **例**:

		```bash
    # 不審なディレクトリにあるComposeプロジェクトの全コンテナ履歴を確認

    docker compose -f /opt/suspicious-app/docker-compose.yml ps -a

    ```




## 3. レッドチーム視点

* **タスク**: 侵入したホスト上で稼働しているアプリケーションの構成を偵察する。
* **組み合わせ**: `docker compose ps`
* **解説**: `docker-compose.yml` ファイルを発見した後、`ps` コマンドでそのシステムで稼働しているアプリケーションの構成を偵察します。サービス名 (`db`, `api`など) やポートマッピングから、アプリケーションの構造を理解し、次の攻撃対象を特定します。
* **例**:

		```bash
    # (find / -name "docker-compose.yml" 2>/dev/null でファイルを見つけた後...)

    cd /path/to/project

    docker compose ps

    ```




## エラーメッセージとトラブルシューティング

* 一般的なエラーは [Linux共通のトラブルシューティング](../linux/troubleshooting_common_errors.md) を参照。

1. **現象**: **`docker compose ps` を実行してもコンテナが一覧に表示されない。**
		* **考えられる原因**:
				* `docker compose up` を実行していない。
				* コンテナが起動に失敗して即座に終了している。
				* `docker-compose.yml` が存在しないディレクトリでコマンドを実行している。
		* **解決策**: まず `docker compose up` を実行します。`docker compose ps -a` で `Exited` 状態のコンテナがないか確認し、あれば `docker compose logs` で終了理由を調査します。

2. **現象**: **`State` カラムが `restarting` のまま変わらない。**
		* **考えられる原因**: コンテナの起動プロセスでエラーが発生し、Composeが `restart: always` などのポリシーに従って再起動を繰り返しています。
		* **解決策**: `docker compose logs <service_name>` で対象サービスのログを確認し、エラーの原因（設定ミス、DB接続失敗など）を特定して修正します。

## 環境変数と設定ファイル

* `docker compose` は `.env` ファイルを読み込みます。
* 一般的な環境変数・設定ファイルは [Linux共通の環境変数・設定ファイル](../linux/environment_and_config.md) を参照。

## セキュリティに関する考慮事項

### 脆弱性と悪用事例

* **脆弱性**: `docker compose ps` は読み取り専用ですが、その出力は**情報漏洩**に繋がります。
* **悪用シナリオ**: コンテナ名やポートマッピング情報から、アプリケーションのアーキテクチャや使用技術 (`postgres`, `redis`など) が推測されます。攻撃者はこの情報を元に、特定のバージョンの脆弱性を探すなど、偵察活動に利用します。

### GTFOBins / LOLBAS における利用例

`docker compose` は `docker` コマンドの一部として扱われます。`sudo docker` の権限があれば、任意の操作が可能です。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: Dockerホストへのアクセス権を厳格に管理する。
* **Detection (検知)**: `docker` コマンドの実行を `auditd` で監視し、不審なユーザーによる偵察活動がないかを確認する。

## 注意点・補足

* **作業ディレクトリ**: `docker compose ps` は、カレントディレクトリにある `docker-compose.yml` を参照します。別の場所にあるファイルを対象にしたい場合は、`-f` オプションでパスを指定します。

---


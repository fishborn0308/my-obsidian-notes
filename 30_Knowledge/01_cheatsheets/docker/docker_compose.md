---
tags:
  - 'docker_compose'
  - 'docker'
  - 'container_orchestration'
  - 'cheatsheet'
title: 'docker compose - マルチコンテナアプリケーションを定義・実行する'
summary: 'YAMLファイルを使って、複数のコンテナで構成されるアプリケーションのサービス、ネットワーク、ボリュームを定義し、一括で管理するためのツールです。'
related:
  - 'docker-compose_yaml'
  - 'docker_run'
  - 'docker_stack'
  - 'Kubernetes'
---

# `docker compose` - マルチコンテナアプリケーションを定義・実行する

## 概要

`docker compose` は、複数のコンテナで構成されるアプリケーションを、`docker-compose.yaml` という単一のYAMLファイルで定義し、ビルド、起動、停止、削除といったライフサイクル全体を管理するためのツールです。Webサーバー、データベース、キャッシュサーバーといった複数のサービスを協調させて動作させるアプリケーションのセットアップをコード化し、開発から本番まで一貫した環境を簡単に再現できます。

(出自: `docker` パッケージに含まれる)

## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 |
| :--- | :--- | :--- |
| ⭐ **`docker run`** | **単一**のコンテナを起動します。`docker compose` は**複数**のコンテナを協調させて起動します。 | `docker compose` (マルチコンテナ) |
| **シェルスクリプト** | 複数の `docker run` コマンドをシェルスクリプトで実行することも可能ですが、`docker compose` は宣言的に構成を記述できるため、可読性、管理性に優れています。 | `docker compose` |
| **Kubernetes** | Kubernetesはより大規模で複雑な**クラスタ環境**を対象とします。`docker compose` は主に**単一ホスト上**での開発やシンプルなデプロイに適しています。 | Kubernetes (大規模) |

## 🌐 主要なサブコマンド

`docker compose` の操作は、操作対象となるサブコマンドを指定することから始まります。

| コマンド | 概要 | 詳細 |
| :--- | :--- | :--- |
| ⭐ **`docker compose up`** | サービスをビルド、作成、**起動**する | [./docker_compose_up.md](./docker_compose_up.md) |
| ⭐ **`docker compose down`** | サービスに関連するコンテナ、ネットワーク等を**停止・削除**する | [./docker_compose_down.md](./docker_compose_down.md) |
| ⭐ **`docker compose ps`** | プロジェクトのコンテナ**一覧を表示**する | [./docker_compose_ps.md](./docker_compose_ps.md) |
| ⭐ **`docker compose logs`** | サービスの**ログを表示**する | [./docker_compose_logs.md](./docker_compose_logs.md) |
| **`docker compose start`** | 停止しているサービスを**開始**する | (未作成) |
| **`docker compose stop`** | 実行中のサービスを**停止**する | (未作成) |
| **`docker compose restart`** | サービスを**再起動**する | (未作成) |
| **`docker compose build`** | サービスのイメージを**ビルド**する | (未作成) |
| **`docker compose exec`** | 実行中のコンテナで**コマンドを実行**する | (未作成) |

---

## よく使われるオプション組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: アプリケーションスタック全体をバックグラウンドで起動し、状態を確認する。
* **組み合わせ**: `docker compose up -d` → `docker compose ps`
* **解説**: `-d` でバックグラウンド起動した後、`ps` で全サービスが `Up` 状態かを確認し、問題があれば `logs` で原因を調査するのが一連の流れです。
* **例**:

    ```bash
    # アプリケーションをバックグラウンドで起動
    docker compose up -d

    # 起動状態を確認
    docker compose ps
    ```

### 2. ブルーチーム視点

* **タスク**: 侵害された可能性のあるアプリケーションの構成と状態を把握する。
* **組み合わせ**: `docker compose ps`, `docker compose logs`
* **解説**: まず `ps` で不審なサービス（予期せず `Exited` になっている、不審なポートを公開しているなど）がないかを確認します。次に `logs` で各サービスのログを調査し、エラーメッセージや不審なアクセス記録がないかを確認します。
* **例**: -

### 3. レッドチーム視点

* **タスク**: 侵入したホスト上で `docker-compose.yml` ファイルを発見し、アプリケーションのアーキテクチャと弱点を分析する。
* **組み合わせ**: (`find` でファイル発見後) `cat docker-compose.yml` → `docker compose ps`
* **解説**: `docker-compose.yml` は、アプリケーションの構造を理解するための設計図です。攻撃者はこの情報を元に、どのサービスが重要か、どこに脆弱性が存在しそうかを判断し、ラテラルムーブメント（横展開）の計画を立てます。
* **例**: -

## エラーメッセージとトラブルシューティング

* 一般的なエラーは [Linux共通のトラブルシューティング](../linux/troubleshooting_common_errors.md) を参照。

1. **エラーメッセージ例 1**: `services.webapp.ports contains an invalid type, it should be a number, or a string`
    * **考えられる原因**: `docker-compose.yml` のYAMLの構文エラー。インデントがずれている、コロン `:` の後のスペースがない、などが一般的です。
    * **解決策**: YAMLリンターを使って構文をチェックしてください。インデントはスペース2つが一般的です。

## 環境変数と設定ファイル

* 一般的な環境変数・設定ファイルは [Linux共通の環境変数・設定ファイル](../linux/environment_and_config.md) を参照。

### 関連する設定ファイル

* ⭐ **`docker-compose.yaml`**: アプリケーションスタック全体を定義するメインのファイル。
* ⭐ **`.env` ファイル**: `docker-compose.yaml` と同じディレクトリに置くと、そこに記述された `KEY=VALUE` 形式の変数が、Composeファイル内で `${KEY}` として自動的に利用できます。

## セキュリティに関する考慮事項

### 脆弱性と悪用事例

* **脆弱性**: **信頼できないComposeファイルの実行**。
* **悪用シナリオ**: インターネット上で見つけた信頼性の低い `docker-compose.yml` を安易に実行すると、悪意のあるイメージを `pull` したり、ホストの機密ディレクトリをコンテナにマウントしたりする記述が含まれている可能性があります。

### GTFOBins / LOLBAS における利用例

`docker compose` は `docker` コマンドの一部として扱われます。`sudo docker` の権限があれば、任意の操作が可能です。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: `docker-compose.yml` の `ports` ディレクティブで、`127.0.0.1:8080:80` のようにローカルホストにのみバインドし、意図しない外部公開を防ぐ。機密情報は `.env` ファイルに記述し、`.gitignore` に追加する。
* **Detection (検知)**: `docker compose` の実行ログやDockerデーモンのAPIコールを監視し、不審なイメージの `pull` やコンテナの作成がないかを確認する。

## 注意点・補足

* **ファイル名**: コマンドはデフォルトでカレントディレクトリの `compose.yaml` または `docker-compose.yaml` を探します。

---
[インデックスに戻る](../docker_index.md)

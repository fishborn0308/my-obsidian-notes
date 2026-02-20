---
tags:
  - 'docker-compose.yaml'
  - 'compose.yaml'
  - 'docker'
  - 'container orchestration'
  - 'cheetsheet'
title: 'docker-compose.yaml - マルチコンテナアプリケーションの定義ファイル'
summary: '複数のコンテナで構成されるアプリケーションのサービス、ネットワーク、ボリュームをYAML形式で定義し、一括で管理するための設計図です。'
related:
  - 'docker compose up'
  - 'docker compose down'
  - 'Dockerfile'
  - 'Kubernetes Manifest'
---

# `docker-compose.yaml` - マルチコンテナアプリケーションの定義ファイル

## 概要

`docker-compose.yaml` (または `compose.yaml`) は、複数のコンテナで構成されるアプリケーションの**サービス (services)**、**ネットワーク (networks)**、**ボリューム (volumes)** を定義するためのYAML形式のファイルです。このファイル1つで、Webサーバー、データベース、キャッシュサーバーといった複数のコンテナの構成、連携、起動順序などをまとめて管理できます。

`docker compose up` コマンドは、このファイルを読み込んで、定義されたアプリケーションスタック全体を一度に起動・管理します。これにより、複雑なアプリケーションのセットアップをコード化し、開発から本番まで一貫した環境を簡単に再現できます。

(出自: `docker compose` の基本機能)

## 類似技術との差異

| 技術 | 差異 / 使い分けのポイント | 推奨 |
| :--- | :--- |:---|
| ⭐ **`Dockerfile`** | `Dockerfile` は**単一の**コンテナイメージをビルドするための設計図です。`docker-compose.yaml` は、そうしてビルドされた複数のイメージを**どのように組み合わせて実行するか**を定義します。 | - |
| **シェルスクリプト** | 複数の `docker run` コマンドをシェルスクリプトで実行することも可能ですが、`docker-compose.yaml` は宣言的に構成を記述できるため、可読性、管理性に優れています。 | `docker-compose.yaml` |
| **Kubernetes** | KubernetesもコンテナオーケストレーションのためのYAMLファイルを扱いますが、より大規模で複雑な**クラスタ環境**を対象とします。`docker-compose.yaml` は主に**単一ホスト上**での開発やシンプルなデプロイに適しています。 | Kubernetes (大規模) |

## よく連携されるコマンド

`docker-compose.yaml` ファイルは、`docker compose` コマンドと連携してその真価を発揮します。

### シナリオ例: アプリケーションスタックの一括起動 (インフラ構築・運用視点)

* **目的**: `docker-compose.yaml` に定義されたWebサーバーとデータベースを、バックグラウンドで一括起動する。
* **連携コマンド**: `docker compose up`
* **解説**: `docker-compose.yaml` があるディレクトリで `docker compose up -d` を実行するだけで、Composeはファイルを読み込み、定義された全てのサービスをビルド・作成し、バックグラウンドで起動します。
* **コマンド例**:

    ```bash
    # アプリケーションをバックグラウンドで起動
    docker compose up -d
    ```

## 📜 主要なキーと構文

| キー | 説明 |
| :--- | :--- |
| **トップレベルキー** | |
| ⭐ `services` | **必須**。アプリケーションを構成する各コンテナ（サービス）を定義します。このセクションがComposeファイルの中核です。 |
| `networks` | コンテナが接続するカスタムネットワークを定義します。 |
| `volumes` | データを永続化させるための名前付きボリュームを定義します。 |
| **`services` 内のキー** | |
| ⭐ `image` | サービスが使用するDockerイメージを指定します。 |
| ⭐ `build` | イメージ名の代わりに、`Dockerfile` があるディレクトリのパスを指定します。 |
| ⭐ `ports` | `"ホスト側ポート:コンテナ側ポート"` の形式でポートをマッピングします。 |
| ⭐ `volumes` | `"名前付きボリューム or ホストパス:コンテナ内パス"` の形式でボリュームをマウントします。 |
| ⭐ `environment`| コンテナ内に環境変数を設定します。 |
| `depends_on` | サービスの起動順序を制御します。 |

## よく使われるオプション組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: 典型的なWebアプリケーションスタック（WordPress + MySQL）を定義する。
* **組み合わせ**: `services`, `volumes`, `environment`
* **解説**: 各サービスを定義し、`volumes` でDBのデータを永続化、`environment` で接続情報を設定します。
* **例 (`docker-compose.yaml`)**:

    ```yaml
    services:
      db:
        image: mysql:8.0
        volumes:
          - db_data:/var/lib/mysql
        environment:
          - MYSQL_ROOT_PASSWORD=...
          - MYSQL_DATABASE=wordpress
      wordpress:
        image: wordpress:latest
        ports:
          - "8080:80"
        environment:
          - WORDPRESS_DB_HOST=db:3306
          - WORDPRESS_DB_PASSWORD=...
    volumes:
      db_data:
    ```

### 2. ブルーチーム視点

* **タスク**: アプリケーションの `docker-compose.yaml` を監査し、セキュリティ上の設定ミスがないかを確認する。
* **組み合わせ**: (ファイルレビュー)
* **解説**: `ports` (DBポートが外部公開されていないか？)、`volumes` (ホストの機密ディレクトリがマウントされていないか？)、`environment` (機密情報がハードコードされていないか？) などを重点的にチェックします。
* **例**: -

### 3. レッドチーム視点

* **タスク**: 侵入したホスト上で `docker-compose.yaml` ファイルを発見し、アプリケーションのアーキテクチャと弱点を分析する。
* **組み合わせ**: (ファイルレビュー)
* **解説**: `environment` セクションに平文で書かれたパスワードやAPIキーは、ラテラルムーブメント（横展開）のための**情報の宝庫**です。また、サービス構成から内部ネットワークのトポロジーを把握し、次の攻撃対象を特定します。
* **例**: -

## エラーメッセージとトラブルシューティング

* 一般的なエラーは [Linux共通のトラブルシューティング](../linux//troubleshooting_common_errors.md) を参照。

1. **エラーメッセージ例 1**: `services.webapp.ports contains an invalid type, it should be a number, or a string`
    * **考えられる原因**: YAMLの構文エラー。インデントがずれている、コロン `:` の後のスペースがない、などが一般的です。
    * **解決策**: YAMLリンターを使って構文をチェックしてください。インデントはスペース2つが一般的です。

## 環境変数と設定ファイル

* 一般的な環境変数・設定ファイルは [Linux共通の環境変数・設定ファイル](../linux/environment_and_config.md) を参照。

### 関連する設定ファイル

* ⭐ **`.env` ファイル**: `docker-compose.yaml` と同じディレクトリに `.env` ファイルを置くと、そこに記述された `KEY=VALUE` 形式の変数が、Composeファイル内で `${KEY}` として自動的に利用できます。

## セキュリティに関する考慮事項

### 脆弱性と悪用事例

* **脆弱性**: **機密情報のハードコーディング**。
* **悪用シナリオ**: `docker-compose.yaml` に直接パスワードやAPIキーを書き込むと、このファイルがGitなどでバージョン管理された際に機密情報が漏洩します。

### GTFOBins / LOLBAS における利用例

`docker-compose.yaml` は設定ファイルのため、GTFOBinsにはリストされていません。しかし、このファイルを解釈する `docker compose` コマンドは、`docker` コマンドの一部として権限昇格に悪用される可能性があります。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: **機密情報は `.env` ファイルに記述**し、`.env` は `.gitignore` に追加する。本番環境ではDocker Secretsや外部のシークレット管理ツールを利用する。
* **Detection (検知)**: Gitリポジトリをスキャンし、`docker-compose.yaml` 内にハードコードされたシークレットがないか定期的に監査する。

## 注意点・補足

* **バージョニング**: かつてファイルの先頭に `version: "3.8"` のようなバージョン指定が必須でしたが、最新のCompose Specでは不要になりました。

---


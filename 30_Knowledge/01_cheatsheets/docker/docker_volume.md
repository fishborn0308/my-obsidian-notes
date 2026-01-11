---
tags:
  - 'docker_volume'
  - 'docker'
  - 'storage'
  - 'persistence'
  - 'cheatsheet'
title: 'docker volume - コンテナのデータを永続化する'
summary: 'コンテナのライフサイクルから独立してデータを永続化させるためのDockerボリュームを管理するコマンドスイートです。'
related:
  - 'docker_run_-v'
  - 'docker-compose_yaml'
  - 'docker_inspect'
---

# `docker volume` - コンテナのデータを永続化する

## 概要

`docker volume` は、コンテナのデータを永続化させるための**ボリューム**を作成・管理・削除するためのコマンドスイートです。コンテナは削除されるとその内部のデータも失われますが、ボリュームを使うことで、データベースのデータやユーザーがアップロードしたファイルなどを、コンテナのライフサイクルから独立させて永続的に保持できます。

Dockerにおけるデータの永続化には主に**名前付きボリューム**と**バインドマウント**があり、`docker volume` コマンドは主に「名前付きボリューム」を管理します。

(出自: `docker` パッケージに含まれる)

## 類似コマンドと差異

| 概念 | 差異 / 使い分けのポイント | 推奨 |
| :--- | :--- | :--- |
| ⭐ **名前付きボリューム** | Dockerが完全に管理するストレージ。`docker volume create` で作成。バックアップや移行が容易で、**本番環境で推奨されます**。 | ⭐ (本番環境) |
| **バインドマウント** | ホストOS上の**既存の**ディレクトリやファイルをコンテナ内に直接マウントします。ホストのファイルシステムに強く依存し、セキュリティリスクも考慮が必要です。 | (開発環境) |
| **匿名ボリューム** | 名前が指定されずに自動で作られるボリューム。管理が難しいため、通常は名前付きボリュームが推奨されます。 | - |

## 🌐 主要なサブコマンド

`docker volume` の操作は、操作対象となるサブコマンドを指定することから始まります。

| コマンド | 概要 | 詳細 |
| :--- | :--- | :--- |
| ⭐ **`docker volume create`** | 新しい**名前付きボリューム**を作成する | [./docker_volume_create.md](./docker_volume_create.md) |
| ⭐ **`docker volume ls`** | Dockerホスト上のボリュームを**一覧表示**する | [./docker_volume_ls.md](./docker_volume_ls.md) |
| ⭐ **`docker volume inspect`** | ボリュームの**詳細情報**（ホスト上のパスなど）を表示する | [./docker_volume_inspect.md](./docker_volume_inspect.md) |
| ⭐ **`docker volume rm`** | 1つ以上のボリュームを**削除**する | [./docker_volume_rm.md](./docker_volume_rm.md) |
| **`docker volume prune`** | 未使用のボリュームを**一括で削除**する | (未作成) |

---

## よく使われるオプション組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: データベースコンテナのデータを永続化する。
* **組み合わせ**: `docker volume create`, `docker run -v`
* **解説**: まず `docker volume create` でデータ用のボリュームを明示的に作成し、`docker run` の `-v` オプションでそのボリュームをコンテナ内のデータディレクトリにマウントします。
* **例**:

    ```bash
    # 1. DBデータ用のボリュームを作成
    docker volume create my-app-db-data

    # 2. 作成したボリュームをマウントしてPostgreSQLコンテナを起動
    docker run -d --name database -v my-app-db-data:/var/lib/postgresql/data postgres:15
    ```

### 2. ブルーチーム視点

* **タスク**: 侵害されたコンテナが使用していたボリュームを特定し、証拠保全する。
* **組み合わせ**: `docker inspect <container>`, `docker volume inspect <volume>`
* **解説**: まず `docker inspect` でコンテナがどのボリュームをマウントしているかを確認します。次に `docker volume inspect` でそのボリュームのホスト上の物理パス (`Mountpoint`) を特定し、ディレクトリごとコピーして証拠保全します。
* **例**: -

### 3. レッドチーム視点

* **タスク**: 侵入したホストの偵察を行い、重要なデータがどこに保存されているかを探す。
* **組み合わせ**: `docker volume ls`, `docker volume inspect <volume>`
* **解説**: 攻撃者は `docker volume ls` で `prod-db-data` のような名前のボリュームを探し、`inspect` でその物理パスを特定します。その後、ホスト上でそのパスに直接アクセスし、コンテナを介さずにデータベースファイルなどを直接窃取しようと試みます。
* **例**: -

## エラーメッセージとトラブルシューティング

* 一般的なエラーは [Linux共通のトラブルシューティング](../linux/troubleshooting_common_errors.md) を参照。

1. **エラーメッセージ例 1**: `Error response from daemon: remove <volume_name>: volume is in use by [<container_id>]`
    * **考えられる原因**: 削除しようとしているボリュームが、まだ存在するコンテナ（実行中または停止中）によって使用されています。
    * **解決策**: エラーメッセージに表示されたコンテナIDを `docker rm` で先に削除してから、再度ボリュームを削除してください。

## 環境変数と設定ファイル

* `docker volume` の動作に直接影響する環境変数は通常ありません。
* 一般的な環境変数・設定ファイルは [Linux共通の環境変数・設定ファイル](../linux/environment_and_config.md) を参照。

## セキュリティに関する考慮事項

### 脆弱性と悪用事例

* **脆弱性**: **データの永続化と漏洩リスク**。
* **悪用シナリオ**: ボリュームはコンテナを削除しても残るため、ボリューム内に機密情報が保存されている場合、そのボリュームへのアクセス権を持つ攻撃者は情報を読み取ることができます。

### GTFOBins / LOLBAS における利用例

`docker volume` は `docker` コマンドの一部として扱われます。`sudo docker` の権限があれば、任意の操作が可能です。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: 機密データを含むボリュームは、Dockerの暗号化機能や、基盤となるファイルシステムの暗号化機能を使って保護する。不要になったボリュームは `docker volume rm` で適切に削除する。
* **Detection (検知)**: `docker volume ls` を定期的に実行し、意図しないボリュームが作成されていないか監査する。

## 注意点・補足

* **Composeとの連携**: `docker-compose.yml` ファイルのトップレベルに `volumes` セクションを定義することで、`docker compose up` を実行した際に名前付きボリュームが自動的に作成されます。

---
[インデックスに戻る](../docker_index.md)

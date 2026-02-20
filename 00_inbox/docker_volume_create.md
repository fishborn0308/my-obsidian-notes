---
tags:
  - 'docker volume create'
  - 'docker'
  - 'volume management'
  - 'storage'
  - 'cheetsheet'
title: 'docker volume create - 新しいDockerボリュームを作成する'
summary: 'コンテナのデータを永続化させるための名前付きボリュームを明示的に作成します。データの管理とバックアップを容易にします。'
related:
  - 'docker volume ls'
  - 'docker volume rm'
  - 'docker run -v'
  - 'docker compose up'
---

# `docker volume create` - 新しいDockerボリュームを作成する

## 概要

`docker volume create` は、コンテナのデータを永続化させるための**名前付きボリューム (Named Volume)** を新しく作成するコマンドです。ボリュームはDockerによって管理される特別なディレクトリで、ホストOSのファイルシステム内 (`/var/lib/docker/volumes/` 配下) に作成されます。コンテナのライフサイクル（作成、削除）から完全に独立しているため、コンテナを削除・再作成してもデータが失われません。

`docker run -v my-vol:/data ...` のようにコンテナ実行時に自動作成することもできますが、`docker volume create` で事前に明示的に作成することで、管理がより明確になります。

(出自: `docker` パッケージに含まれる)

## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 |
| :--- | :--- |:---|
| ⭐ **`docker run -v`** | `docker run` 時にボリュームが存在しない場合、自動的に作成します。`docker volume create` はボリュームを**事前に独立して作成**します。 | `docker volume create` (明示的管理) |
| **`docker volume rm`** | `create` の逆の操作。ボリュームを**削除**します。 | - |

## よく連携されるコマンド

`docker volume create` は、作成したボリュームをコンテナにマウントする `docker run` コマンドと一連のワークフローとして連携します。

### シナリオ例: データベース用のボリュームを作成してコンテナを起動する (インフラ構築・運用視点)

* **目的**: PostgreSQLデータベースコンテナを起動する前に、データ格納用のボリューム `pg-data` を明示的に作成する。
* **連携コマンド**: `docker run`
* **解説**: まず `docker volume create` でボリュームを作成し、その名前を `docker run` の `-v` オプションで指定してマウントします。
* **コマンド例**:

    ```bash
    # 1. データ用のボリュームを先に作成
    docker volume create postgres-data

    # 2. 作成したボリュームをマウントしてコンテナを起動
    docker run -d \
      --name my-database \
      -e POSTGRES_PASSWORD=mysecretpassword \
      -v postgres-data:/var/lib/postgresql/data \
      postgres:15
    ```

## オプション説明

| オプション | 説明 |
| :--- | :--- |
| **ドライバとオプション** | |
| ⭐ `<volume_name>` | 作成するボリュームの名前を指定します。（省略するとランダムな名前が付きます） |
| ⭐ `-d`, `--driver <driver>` | 使用するボリュームドライバを指定します。デフォルトは `local` です。 |
| `--name <name>` | 作成するボリュームの名前を明示的に指定します。 |
| `-o`, `--opt <key=value>`| ボリュームドライバに渡すオプションを指定します。（例: NFSドライバにサーバーアドレスを指定） |
| `--label <key=value>` | ボリュームに管理用のラベルを付与します。 |

## よく使われるオプション組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: データベースやステートフルなアプリケーションのデータを保存するための名前付きボリュームを事前に作成する。
* **組み合わせ**: `docker volume create <volume_name>`
* **解説**: 上記「よく連携されるコマンド」シナリオの通り、データがどこに保存されているかが明確になり、バックアップや管理が容易になります。
* **例**: -

### 2. ブルーチーム視点

* **タスク**: メモリベースの `tmpfs` ボリュームを作成し、マルウェアの動的解析に利用する。
* **組み合わせ**: `docker volume create --driver local -o type=tmpfs -o device=tmpfs -o o=size=100m,uid=1000 evidence-volume`
* **解説**: ドライバオプション (`-o`) を使い、ホストのディスクに書き込まないメモリベースの `tmpfs` ボリュームを作成します。コンテナがディスクに書き込みを行うのを防ぎ、ホストへの影響を最小限に抑えながら動的解析を行うことができます。
* **例**: -

### 3. レッドチーム視点

* **タスク**: 侵入したホスト上で、窃取したデータやツールを隠す場所としてボリュームを作成する。
* **組み合わせ**: `docker volume create important-data-backup`
* **解説**: `docker volume ls` を実行した管理者が、正規のバックアップ用ボリュームだと誤認し、見過ごすことを狙って紛らわしい名前のボリュームを作成します。
* **例**: -

## エラーメッセージとトラブルシューティング

* 一般的なエラーは [Linux共通のトラブルシューティング](../linux/troubleshooting_common_errors.md) を参照。

1. **エラーメッセージ例 1**: `Error response from daemon: a volume with the same name already exists`
    * **考えられる原因**: 指定した名前のボリュームはすでに存在します。
    * **解決策**: `docker volume ls` で既存のボリュームを確認してください。別の名前を使用するか、既存のボリュームが不要であれば `docker volume rm` で削除します。

## 環境変数と設定ファイル

* `docker volume create` の動作に直接影響する環境変数は通常ありません。
* 一般的な環境変数・設定ファイルは [Linux共通の環境変数・設定ファイル](../linux/environment_and_config.md) を参照。

## セキュリティに関する考慮事項

### 脆弱性と悪用事例

* **脆弱性**: **データの永続化と漏洩リスク**。
* **悪用シナリオ**: ボリュームはコンテナを削除しても残るため、ボリューム内に機密情報が保存されている場合、そのボリュームへのアクセス権を持つ攻撃者は情報を読み取ることができます。

### GTFOBins / LOLBAS における利用例

`docker volume create` は `docker` コマンドの一部として扱われます。`sudo docker` の権限があれば、任意の操作が可能です。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: 機密データを含むボリュームは、Dockerの暗号化機能や、基盤となるファイルシステムの暗号化機能を使って保護する。不要になったボリュームは `docker volume rm` で適切に削除する。
* **Detection (検知)**: `docker volume ls` を定期的に実行し、意図しないボリュームが作成されていないか監査する。

## 注意点・補足

* **Composeとの連携**: `docker-compose.yml` ファイルのトップレベルに `volumes` セクションを定義することで、`docker compose up` を実行した際に名前付きボリュームが自動的に作成されます。

---


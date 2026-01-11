---
tags:
  - 'docker_volume_inspect'
  - 'docker'
  - 'volume_management'
  - 'storage'
  - 'cheatsheet'
title: 'docker volume inspect - Dockerボリュームの詳細情報を表示する'
summary: '指定したDockerボリュームに関する低レベルで詳細な情報をJSON形式で出力し、ホスト上の物理パス（Mountpoint）などを確認します。'
related:
  - 'docker_volume_ls'
  - 'docker_volume_create'
  - 'docker_inspect'
  - 'jq'
---

# `docker volume inspect` - Dockerボリュームの詳細情報を表示する

## 概要

`docker volume inspect` は、指定した1つ以上の名前付きボリュームに関する**低レベルで詳細な情報**をJSON形式で出力するコマンドです。これには、ボリュームの名前、使用しているドライバ、ホストOS上の実際の格納パス (`Mountpoint`)、ラベル、ドライバ固有のオプションなどが含まれます。主に、ボリュームの物理的な場所を確認してバックアップを行ったり、設定内容をデバッグしたりする際に使用されます。

(出自: `docker` パッケージに含まれる)

## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 |
| :--- | :--- | :--- |
| ⭐ **`docker volume ls`** | Dockerホスト上に存在するボリュームの**概要**を一覧表示します。`inspect` は、一つのボリュームに関する**全ての詳細情報**を表示します。 | - |
| **`docker inspect <container>`** | **コンテナ**の詳細情報を表示します。出力の `Mounts` セクションで、どのボリュームが接続されているかを確認できますが、ボリューム自体の詳細は `volume inspect` が直接的です。 | - |

## よく連携されるコマンド

`docker volume inspect` の出力はJSON形式のため、`jq` コマンドや `--format` オプションと連携して、スクリプトで特定の情報を抽出する際によく使われます。

### シナリオ例: ボリュームの物理パスを取得してバックアップする (インフラ構築・運用視点)

* **目的**: コンテナ化されたデータベースの物理バックアップを取得するため、データが格納されているホスト上のディレクトリパスを特定する。
* **連携コマンド**: `--format` (Go template), `rsync`
* **解説**: `--format` オプションで `Mountpoint`（ホスト上のパス）だけを正確に抽出し、そのパスを `rsync` などのバックアップコマンドに渡します。
* **コマンド例**:

    ```bash
    # 1. postgres-data ボリュームのホストパスを取得
    PG_DATA_PATH=$(docker volume inspect --format '{{.Mountpoint}}' postgres-data)

    # 2. 取得したパスを使ってバックアップを実行
    sudo rsync -a "${PG_DATA_PATH}/" "/mnt/backups/postgres-$(date +%F)/"
    ```

## オプション説明

| オプション | 説明 |
| :--- | :--- |
| **出力形式** | |
| ⭐ `-f`, `--format <Go template>` | Go言語のテンプレートを使い、出力から特定の情報だけを抽出します。 |

## よく使われるオプション組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: ボリュームの物理的な場所を確認してバックアップを行う。
* **組み合わせ**: `docker volume inspect --format '{{.Mountpoint}}' <volume_name>`
* **解説**: 上記「よく連携されるコマンド」シナリオの通り、ボリュームの `Mountpoint` を特定し、バックアップスクリプトなどで利用します。
* **例**: -

### 2. ブルーチーム視点

* **タスク**: 不審なコンテナに関連付けられたボリュームの物理的な証拠を保全する。
* **組み合わせ**: `docker volume inspect <suspicious_volume_name>`
* **解説**: 侵害されたコンテナが使用していたボリュームを特定した場合、`inspect` コマンドでその `Mountpoint` を確認します。これにより、攻撃者が永続化させたデータ（マルウェア、窃取した情報など）がホスト上のどこに保存されているかを正確に突き止め、フォレンジック調査の対象とできます。
* **例**: -

### 3. レッドチーム視点

* **タスク**: 侵入したホスト上で、重要なデータが格納されていそうなボリュームの物理パスを特定する。
* **組み合わせ**: `docker volume inspect <volume_name>`
* **解説**: 攻撃者は `docker volume ls` で `prod-db-data` のような興味深い名前のボリュームを探し、`inspect` でその `Mountpoint` を特定します。その後、ホスト上でそのパスに直接アクセスし、コンテナのアプリケーションロジックを介さずに、データベースファイルなどを直接窃取しようと試みます。
* **例**: -

## エラーメッセージとトラブルシューティング

* 一般的なエラーは [Linux共通のトラブルシューティング](../linux/troubleshooting_common_errors.md) を参照。

1. **エラーメッセージ例 1**: `Error: No such volume: <volume_name>`
    * **考えられる原因**: 指定した名前のボリュームが存在しません。
    * **解決策**: `docker volume ls` を実行して、存在するボリュームの名前を正確に確認してください。

## 環境変数と設定ファイル

* `docker volume inspect` の動作に直接影響する環境変数は通常ありません。
* 一般的な環境変数・設定ファイルは [Linux共通の環境変数・設定ファイル](../linux/environment_and_config.md) を参照。

## セキュリティに関する考慮事項

### 脆弱性と悪用事例

* **脆弱性**: **情報漏洩**。
* **悪用シナリオ**: `docker volume inspect` で `Mountpoint` を知ることができるDocker管理者は、そのパスを通じてボリュームのデータにホストから直接アクセスできます。これは、コンテナによるファイルシステムの隔離をバイパスすることを意味します。

### GTFOBins / LOLBAS における利用例

`docker volume inspect` は `docker` コマンドの一部として扱われます。`sudo docker` の権限があれば、任意の操作が可能です。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: Dockerソケットへのアクセス権限を厳格に管理する。
* **Detection (検知)**: `auditd` で `docker volume inspect` の実行を監視し、不審なユーザーによる偵察活動がないかを確認する。

## 注意点・補足

* **`jq`との連携**: `docker volume inspect` の出力はJSONなので、`jq` コマンドラインJSONプロセッサと組み合わせることで、より柔軟なデータ抽出が可能です。

    ```bash
    # ボリュームのラベル情報をきれいに表示
    docker volume inspect my-volume | jq '.[0].Labels'
    ```

---
[インデックスに戻る](../docker_index.md)

---
tags:
  - docker_image
  - docker
  - image_management
created: 2025-06-29 15:02
modified: 2026-02-23 09:52
environment:
vulnearability: []
knowledge_category: Command
---

# Command - docker - docker image - Dockerイメージを管理する

## 概要

Dockerイメージは、アプリケーションとその依存関係（ライブラリ、設定ファイル、ランタイムなど）をパッケージ化した、読み取り専用のテンプレートです。コンテナは、このイメージから作成される実行インスタンスです。`docker image` コマンド群は、このイメージのライフサイクル全体（作成、配布、管理、削除）を操作するためのインターフェイスを提供します。

(出自: `docker` パッケージに含まれる)

## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 |
| :--- | :--- |:---|
| ⭐ **`docker container`** | `docker image` はコンテナの**設計図**を管理します。`docker container` は、その設計図から作成された**実行インスタンス**を管理します。 | - |
| **`Dockerfile`** | `Dockerfile` はイメージを作成するための**テキストベースの設計図**です。`docker image build` (または `docker build`) は、このファイルを読み込んで実際のイメージを作成します。 | - |

## 🌐 主要なサブコマンド

Dockerイメージの管理は、以下のサブコマンドを組み合わせて行います。

| コマンド | 概要 | 詳細 |
| :--- | :--- | :--- |
| ⭐ **`docker image ls`** | ローカルにあるイメージを**一覧表示**する (`docker images` と同義) | [./docker_images.md](Command%20-%20docker%20-%20docker%20images%20-%20ローカルのDockerイメージを一覧表示する.md) |
| ⭐ **`docker image build`**| `Dockerfile`からイメージを**ビルド**する (`docker build` と同義) | [./docker_build.md](Command%20-%20docker%20-%20docker%20build%20-%20Dockerfileからイメージをビルドする.md) |
| ⭐ **`docker image pull`** | レジストリからイメージを**ダウンロード**する (`docker pull` と同義) | [./docker_pull.md](Command%20-%20docker%20-%20docker%20pull%20-%20レジストリからDockerイメージを取得する.md) |
| ⭐ **`docker image push`** | イメージをレジストリに**アップロード**する (`docker push` と同義) | [./docker_push.md](Command%20-%20docker%20-%20docker%20push%20-%20Dockerイメージをレジストリにアップロードする.md) |
| ⭐ **`docker image rm`** | 1つ以上のイメージを**削除**する (`docker rmi` と同義) | [./docker_rmi.md](Command%20-%20docker%20-%20docker%20rmi%20-%201つ以上のDockerイメージを削除する.md) |
| **`docker image tag`** | イメージに**タグ**を付ける (`docker tag` と同義) | [./docker_tag.md](Command%20-%20docker%20-%20docker%20tag%20-%20Dockerイメージにタグを付ける.md) |
| **`docker image inspect`**| イメージの**詳細情報**を表示する (`docker inspect` の一種) | [./docker_inspect.md](Command%20-%20docker%20-%20docker%20inspect%20-%20Dockerオブジェクトの詳細情報を表示する.md) |
| **`docker image prune`** | 未使用のイメージを**一括で削除**する | (未作成) |

---

## よく使われるオプション組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: アプリケーションのCI/CDパイプラインを構築する。
* **組み合わせ**: `docker image build` → `docker image tag` → `docker image push`
* **解説**: ソースコードからイメージをビルドし、バージョンタグとレジストリ用のタグを付け、プライベートレジストリにプッシュするという一連の流れは、DevOpsにおける最も基本的なワークフローです。
* **例**:

		```bash
    # 1. アプリケーションをビルド

    docker image build -t my-app:1.2 .

    # 2. レジストリ用にタグ付け

    docker image tag my-app:1.2 [my-registry.com/my-app:1.2](https://my-registry.com/my-app:1.2)

    # 3. レジストリにプッシュ

    docker image push [my-registry.com/my-app:1.2](https://my-registry.com/my-app:1.2)

    ```





## 2. ブルーチーム視点

* **タスク**: **サプライチェーンセキュリティ**を確保するため、イメージの出所と内容を検証する。
* **組み合わせ**: `docker image pull` → `trivy image` → `docker image inspect`
* **解説**: `pull` したイメージに対して、`Trivy` などのツールで脆弱性スキャンを行い、`inspect` や `history` でイメージの構成レイヤーやメタデータに不審な点がないかを確認します。
* **例**: -

## 3. レッドチーム視点

* **タスク**: **サプライチェーン攻撃**のため、悪意のあるイメージを作成し、正規のイメージに偽装する。
* **組み合わせ**: `docker image build` → `docker image tag`
* **解説**: 攻撃者はバックドアを仕込んだ `Dockerfile` からイメージをビルドした後、`docker image tag` を使って `ubuntu:latest` のような公式イメージに見せかけたタグを付け、管理者の目を欺こうとします。
* **例**: -

# エラーメッセージとトラブルシューティング

* 一般的なエラーは [Linux共通のトラブルシューティング](../linux/troubleshooting_common_errors.md) を参照。

1. **エラーメッセージ例 1**: `Error response from daemon: conflict: unable to remove repository reference "<image>" (must force) - container ... is using its referenced image`
		* **考えられる原因**: 削除しようとしているイメージが、まだ存在するコンテナ（実行中または停止中）によって使用されています。
		* **解決策**: `docker rm` で対象のコンテナを先に削除してから、再度イメージを削除してください。

# 環境変数と設定ファイル

* 一般的な環境変数・設定ファイルは [Linux共通の環境変数・設定ファイル](../linux/environment_and_config.md) を参照。

# セキュリティに関する考慮事項

## 脆弱性と悪用事例

* **脆弱性**: **信頼できないイメージの利用**と**イメージ内の脆弱性**。
* **悪用シナリオ**: Docker Hubなどの公開レジストリには、マルウェアが仕込まれたイメージや、深刻な脆弱性が放置された古いイメージが存在します。これらを安易に利用すると、システムが侵害される直接的な原因となります。

## GTFOBins / LOLBAS における利用例

`docker image` は `docker` コマンドの一部として扱われます。`sudo docker` の権限があれば、任意の操作が可能です。

## 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: **公式イメージ**または**認証済み発行元**のイメージのみを使用する。組織内で検証済みのイメージのみを格納する**プライベートレジストリ**を運用する。
* **Detection (検知)**: CI/CDパイプラインに `Trivy` などの**脆弱性スキャン**を組み込み、ビルド・デプロイされる全てのイメージをスキャンする。

# 注意点・補足

* **`latest` タグ**: `latest` タグは必ずしも最新版を意味するわけではありません。再現性を確保するため、**常に具体的なバージョンタグを指定する**ことが強く推奨されます。

---


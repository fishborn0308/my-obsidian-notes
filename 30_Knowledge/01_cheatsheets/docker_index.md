# Dockerコマンド チートシート インデックス

このページは、Docker関連コマンドのチートシートへのインデックスです。各コマンドの詳細な使い方やシナリオについては、それぞれのリンク先ページを参照してください。

---

## 1. コンテナのライフサイクル管理 (Container Lifecycle)

コンテナの作成、実行、停止、削除など、日々の運用で最もよく使われるコマンド群です。

- [`docker run`](docker/docker_run.md) - 新しいコンテナを作成し、実行する
- [`docker ps`](docker/docker_ps.md) - コンテナの一覧を表示する
- [`docker stop`](docker/docker_stop.md) - 実行中のコンテナを停止する
- [`docker start`](docker/docker_start.md) - 停止しているコンテナを開始する
- [`docker rm`](docker/docker_rm.md) - 1つ以上のコンテナを削除する
- [`docker exec`](docker/docker_exec.md) - *実行中のコンテナ内でコマンドを実行する
- [`docker logs`](docker/docker_logs.md) - *コンテナのログを取得する

---

## 2. イメージ管理 (Image Management)

Dockerイメージのビルド、ダウンロード、一覧表示、削除などを行うコマンド群です。

- [`docker images`](docker/docker_images.md) - *ローカルにあるイメージの一覧を表示する
- [`docker build`](docker/docker_build.md) - Dockerfileからイメージをビルドする
- [`docker pull`](docker/docker_pull.md) - レジストリからイメージを取得する
- [`docker push`](docker/docker_push.md) - イメージをレジストリにプッシュする
- [`docker rmi`](docker/docker_rmi.md) - 1つ以上のイメージを削除する
- [`docker tag`](docker/docker_tag.md) - イメージにタグを付ける
- [`Dockerfile`](docker/Dockerfile.md) - Dockerイメージの設計図

---

## 3. システムと情報 (System & Info)

Dockerシステム全体の管理、情報表示、不要なリソースのクリーンアップなどを行うコマンド群です。

- [`docker system df`](docker/docker_system_df.md) - Dockerが使用するディスク容量を表示する
- [`docker system prune`](docker/docker_system_prune.md) - 未使用のリソースを一括削除する
- [`docker info`](docker/docker_info.md) - Dockerシステムの詳細情報を表示する
- [`docker version`](docker/docker_version.md) - Dockerのバージョン情報を表示する
- [`docker inspect`](docker/docker_inspect.md) - Dockerオブジェクトの詳細情報を低レベルで表示する

---

## 4. ネットワーク管理 (Network Management)

コンテナ間の通信を制御する仮想ネットワークを作成・管理するコマンド群です。

- [`docker network ls`](docker/docker_network_ls.md) - ネットワークの一覧を表示する
- [`docker network create`](docker/docker_network_create.md) - 新しいネットワークを作成する
- [`docker network connect`](docker/docker_network_connect.md) - コンテナをネットワークに接続する
- [`docker network inspect`](docker/docker_network_inspect.md) - ネットワークの詳細情報を表示する
- [`docker network rm`](./docker/docker_network_rm.md)  - 1つ以上のネットワークを削除する
- [`docker network disconnect`](./docker/docker_network_disconnect.md) - コンテナをネットワークから切り離す
- [`docker network prune`](./docker/docker_network_prune.md) - 未使用のネットワークを一括削除する

---

## 5. ボリューム管理 (Volume Management)

コンテナのデータを永続化させるためのボリュームを作成・管理するコマンド群です。

- [`docker volume ls`](docker/docker_volume_ls.md) - ボリュームの一覧を表示する
- [`docker volume create`](docker/docker_volume_create.md) - 新しいボリュームを作成する
- [`docker volume inspect`](docker/docker_volume_inspect.md) - ボリュームの詳細情報を表示する
- [`docker volume rm`](docker/docker_volume_rm.md) - 1つ以上のボリュームを削除する

---

## 6. Docker Compose

複数のコンテナで構成されるアプリケーションを定義し、管理するためのツールです。

- [`docker compose up`](docker/docker_compose_up.md) - サービスをビルド、作成、開始する
- [`docker compose down`](docker/docker_compose_down.md) - サービスに関連するコンテナ、ネットワーク等を停止・削除する
- [`docker compose ps`](docker/docker_compose_ps.md) - プロジェクトのコンテナ一覧を表示する
- [`docker compose logs`](docker/docker_compose_logs.md) - サービスのログを表示する
- [`docker-compose.yaml`](docker/docker-compose.yaml.md) - マルチコンテナアプリケーションの定義ファイル

---
tags:
  - 'docker_network_ls'
  - 'docker'
  - 'networking'
  - 'cheatsheet'
title: 'docker network ls - Dockerネットワークを一覧表示する'
summary: 'Dockerホスト上に存在する仮想ネットワークを一覧表示し、名前、ID、ドライバ、スコープなどの概要を確認します。'
related:
  - 'docker_network_inspect'
  - 'docker_network_create'
  - 'docker_network_rm'
  - 'ip_addr'
---

# `docker network ls` - Dockerネットワークを一覧表示する

## 概要

`docker network ls` (または `docker network list`) は、Dockerホスト上に存在するすべての仮想ネットワークを一覧表示するコマンドです。各ネットワークのID、名前、使用しているドライバ、スコープ(影響範囲)といったサマリー情報を確認できます。新しいネットワークを作成する前に名前の重複を避けたり、コンテナを接続する対象ネットワークを確認したり、システム全体のネットワーク構成を素早く把握するために使用されます。

(出自: `docker` パッケージに含まれる)

## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 |
| :--- | :--- | :--- |
| ⭐ **`docker network inspect`** | 特定のネットワークに関する**すべての詳細情報**(サブネット、接続コンテナなど)をJSON形式で表示します。`ls` は**概要の一覧表示**、`inspect` は**詳細な調査**に使います。 | - |
| **`ip addr` / `brctl show`** | Dockerホストの**物理/仮想ネットワークインターフェイス**を一覧表示します。`docker network ls` はDockerが管理する**論理的なネットワーク**を一覧表示します。 | - |

## よく連携されるコマンド

`docker network ls` は、その出力に含まれるネットワークIDや名前を、`xargs` を通じて他のコマンド（特に `docker network rm`）に渡す際によく連携されます。

### シナリオ例: 特定のパターンに一致するネットワークを一括削除する (インフラ構築・運用視点)

* **目的**: テスト用に作成した `"test-"` で始まる名前のネットワークを全て削除する。
* **連携コマンド**: `grep`, `awk`, `xargs`, `docker network rm`
* **解説**: `docker network ls` でネットワーク一覧を取得し、`grep` でパターンに一致する行をフィルタリング、`awk` でネットワーク名（またはID）を抽出し、`xargs` で `docker network rm` に渡して一括削除します。
* **コマンド例**:

    ```bash
    # "test-" で始まる名前のネットワークIDを抽出し、一括で削除する
    docker network ls --filter "name=test-" -q | xargs docker network rm
    ```

## オプション説明

| オプション | 説明 |
| :--- | :--- |
| **出力制御** | |
| ⭐ `-q`, `--quiet` | ネットワークIDのみを表示します。他のコマンドと組み合わせる際に便利です。 |
| `--no-trunc` | ネットワークIDを省略せずにすべて表示します。 |
| **フィルタリング** | |
| ⭐ `--filter <key>=<value>` | 特定の条件で表示するネットワークをフィルタリングします。（例: `--filter "driver=bridge"`, `--filter "name=my-app"`） |

## よく使われるオプション組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: ホスト上に存在するネットワークを棚卸しし、不要なものを特定する。
* **組み合わせ**: `docker network ls`
* **解説**: `docker compose down` を実行した後に残った孤立ネットワークなどがないかを確認し、`docker network prune` や `docker network rm` でクリーンアップする際の起点となります。
* **例**:

    ```bash
    # 現在の全ネットワークを確認
    docker network ls
    ```

### 2. ブルーチーム視点

* **タスク**: システム監査の一環として、意図しないネットワークが存在しないか確認する。
* **組み合わせ**: `docker network ls --filter "driver=host"`
* **解説**: `--filter` オプションを使い、`host` ドライバを使用しているネットワーク（実質的には `host` ネットワーク）に接続されたコンテナがないかを調査します。`host` ネットワークはコンテナのネットワーク的な隔離を無効にするため、セキュリティリスクが高まります。
* **例**:

    ```bash
    # hostネットワークを利用しているコンテナがないか確認するための第一歩
    docker network inspect host
    ```

### 3. レッドチーム視点

* **タスク**: **侵入したホストのネットワーク構成を偵察する**ための最初のステップ。
* **組み合わせ**: `docker network ls`
* **解説**: 攻撃者は、まず `docker network ls` を実行して、どのようなネットワークが存在するかを把握します。`prod-db-net` や `backend-api-network` のような名前から、アプリケーションのアーキテクチャや重要なネットワークを推測し、次に `docker network inspect` で詳細を調査してラテラルムーブメント（横展開）の標的を探します。
* **例**: -

## エラーメッセージとトラブルシューティング

* 一般的なエラーは [Linux共通のトラブルシューティング](../linux/troubleshooting_common_errors.md) を参照。
* `docker network ls` は情報を表示するだけのシンプルなコマンドなので、エラーが発生することは稀です。

## 環境変数と設定ファイル

* `docker network ls` の動作に直接影響する環境変数や設定ファイルは通常ありません。
* 一般的な環境変数・設定ファイルは [Linux共通の環境変数・設定ファイル](../linux/environment_and_config.md) を参照。

## セキュリティに関する考慮事項

### 脆弱性と悪用事例

* **脆弱性**: `docker network ls` の出力は**情報漏洩**に繋がります。
* **悪用シナリオ**: `docker network ls` の出力は、攻撃者にとってシステムのネットワークトポロジーを理解するための重要な情報源です。ネットワークの命名規則が `project_database` のように分かりやすい場合、攻撃者は重要なコンテナがどこに接続されているかを容易に推測できます。

### GTFOBins / LOLBAS における利用例

`docker network ls` は `docker` コマンドの一部として扱われます。`sudo docker` の権限があれば、任意のネットワーク操作が可能です。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: Dockerホストへのアクセス権を厳格に管理する。
* **Detection (検知)**: `docker network ls` の実行を `auditd` で監視し、不審なユーザーによる偵察活動がないかを確認する。

## 注意点・補足

* **デフォルトネットワーク**: Dockerをインストールすると、`bridge`, `host`, `none` の3つのネットワークがデフォルトで作成されます。これらは通常、削除できません。

---
[インデックスに戻る](../docker_index.md)

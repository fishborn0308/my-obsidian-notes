---
tags:
  - 'docker_network_inspect'
  - 'docker'
  - 'networking'
  - 'troubleshooting'
created: 2025-06-29 15:02
modified: 2026-02-20 16:24
environment:
vulnearability: []
knowledge_category: Command
---

# `docker network inspect` - Dockerネットワークの詳細情報を表示する

## 概要

`docker network inspect` は、指定した1つ以上のDockerネットワークに関する**低レベルで詳細な情報**をJSON形式で出力するコマンドです。これには、ネットワークのドライバ、サブネットやゲートウェイのIPアドレス情報、接続されているコンテナの一覧、および各コンテナのネットワーク設定（IPアドレス、MACアドレスなど）といった情報が含まれます。主に、コンテナ間のネットワーク接続に関する問題のトラブルシューティングや、ネットワーク構成の正確な把握に使用されます。

(出自: `docker` パッケージに含まれる)

## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 |
| :--- | :--- |:---|
| ⭐ **`docker network ls`** | Dockerホスト上に存在するネットワークの**概要**を一覧表示します。`inspect` は、一つのネットワークに関する**全ての詳細情報**を表示します。 | - |
| **`docker inspect <container>`**| **コンテナ**の詳細情報を表示します。出力の一部にネットワーク情報も含まれますが、`network inspect` は**ネットワーク**を主体とします。 | - |
| **`ip addr`, `brctl show`**| Dockerホストの**物理・仮想ネットワークインターフェイス**を表示します。`docker network inspect` はDockerが管理する**論理的なネットワーク**の情報を表示します。 | - |

## よく連携されるコマンド

`docker network inspect` の出力はJSON形式のため、`jq` コマンドと連携して必要な情報を抽出するのが非常に強力です。

### シナリオ例: 接続コンテナの一覧とIPアドレスを取得する (インフラ構築・運用視点)

* **目的**: `my-app-net` ネットワークに接続されている全コンテナの名前とIPv4アドレスの一覧を取得する。
* **連携コマンド**: `jq`
* **解説**: `inspect` のJSON出力をパイプで `jq` に渡し、`Containers` オブジェクトを解析して、必要なフィールドだけを整形して表示します。
* **コマンド例**:

    ```bash
    # 接続されているコンテナの名前とIPv4アドレスを一覧表示
    docker network inspect my-app-net | jq '.[0].Containers | to_entries | .[] | {name: .value.Name, ip: .value.IPv4Address}'
    ```

## オプション説明

| オプション | 説明 |
| :--- | :--- |
| **出力形式** | |
| ⭐ `-f`, `--format <Go template>`| Go言語のテンプレートを使い、出力から特定の情報だけを抽出します。 |

## よく使われるオプション組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: コンテナ間のネットワーク接続問題をトラブルシューティングする。
* **組み合わせ**: `docker network inspect <network_name>`
* **解説**: `Containers` セクションに目的のコンテナが含まれているか、`IPAM.Config` セクションでサブネットやゲートウェイが意図通りか、各コンテナに正しいIPアドレスが割り当てられているかを確認します。
* **例**:

    ```bash
    # アプリケーションネットワーク "my-app-net" の構成を詳細に調査
    docker network inspect my-app-net
    ```

### 2. ブルーチーム視点

* **タスク**: インシデント発生時に、特定のネットワークの全体像と接続状況を把握する。
* **組み合わせ**: `docker network inspect <network_name>`
* **解説**: 侵害されたコンテナが接続されているネットワークを調査し、他にどのコンテナが同じネットワークセグメントに存在するかをリストアップします。これにより、攻撃が横展開（ラテラルムーブメント）する可能性のある範囲を特定します。
* **例**:

    ```bash
    # 侵害されたコンテナが所属する prod-backend-net を調査し、影響範囲を特定
    docker network inspect prod-backend-net
    ```

### 3. レッドチーム視点

* **タスク**: 侵入したホストの内部ネットワーク構成を偵察する。
* **組み合わせ**: `docker network ls` → `docker network inspect <network_name>`
* **解説**: `docker network ls` で `prod-db-net` のような興味深い名前のネットワークを探し、次に `inspect` を実行します。`Containers` セクションを精査し、外部に公開されていないデータベースやAPIサーバーなどのコンテナ名と内部IPアドレスを特定し、次の攻撃対象リストを作成します。
* **例**: -

## エラーメッセージとトラブルシューティング

* 一般的なエラーは [Linux共通のトラブルシューティング](../linux/troubleshooting_common_errors.md) を参照。

1. **エラーメッセージ例 1**: `Error: No such network: <network_name>`
    * **考えられる原因**: 指定した名前のネットワークが存在しません。
    * **解決策**: `docker network ls` を実行して、存在するネットワークの名前を正確に確認してください。

## 環境変数と設定ファイル

* `docker network inspect` の動作に直接影響する環境変数や設定ファイルは通常ありません。
* 一般的な環境変数・設定ファイルは [Linux共通の環境変数・設定ファイル](../linux/environment_and_config.md) を参照。

## セキュリティに関する考慮事項

### 脆弱性と悪用事例

* **脆弱性**: **情報漏洩**。
* **悪用シナリオ**: `docker network inspect` の出力は、アプリケーションの内部ネットワークトポロジーそのものです。コンテナ名、IPアドレス、MACアドレスなどの詳細な情報が含まれるため、攻撃者にとってシステム内部を偵察するための強力なツールです。

### GTFOBins / LOLBAS における利用例

`docker network inspect` は `docker` コマンドの一部として扱われます。`sudo docker` の権限があれば、任意のネットワーク操作が可能です。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: Dockerソケットへのアクセス権限を厳格に管理する。
* **Detection (検知)**: `auditd` で `docker` コマンドの実行、特に `inspect` 系コマンドによる偵察活動を監視する。

## 注意点・補足

* **ドライバごとの違い**: ネットワークドライバ (`bridge`, `overlay`など)によって、出力に含まれる `Options` や `IPAM` の内容は異なります。

---


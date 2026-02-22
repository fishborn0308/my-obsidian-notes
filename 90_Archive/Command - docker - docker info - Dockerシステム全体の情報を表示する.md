---
tags:
  - docker_info
  - docker
  - system_management
created: 2025-06-29 15:02
modified: 2026-02-22 09:30
environment:
vulnearability: []
knowledge_category: Command
---

# Command - docker - docker info - Dockerシステム全体の情報を表示する

## 概要

`docker info` は、実行しているDockerデーモンに関する広範な情報を一覧表示するコマンドです。これには、コンテナやイメージの数、使用しているストレージドライバやロギングドライバ、ホストOSやカーネルのバージョン、CPUやメモリの搭載量、Dockerルートディレクトリの場所、セキュリティオプションの設定状況などが含まれます。システムの構成確認や、トラブルシューティングの際に全体像を把握するための最初のステップとして非常に有効です。

(出自: `docker` パッケージに含まれる)

## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 |
| :--- | :--- |:---|
| ⭐ **`docker version`** | Dockerクライアントとデーモンの**バージョン番号**に特化しています。`docker info` はバージョン情報も一部含みますが、**設定やリソース状況**など、より広範な実行時情報を表示します。 | `docker info` (全体像) |
| **`docker system df`** | Dockerが使用している**ディスク容量**の内訳を表示することに特化しています。 | - |
| **`uname -a`**| ホストOS自体のカーネルバージョンを表示します。`docker info` はDocker固有の情報を表示します。 | - |

## よく連携されるコマンド

`docker info` の出力から特定の情報を抜き出すために、`grep` や `--format` オプションがよく使われます。

### シナリオ例: スクリプトでCgroupドライバを確認する (インフラ構築・運用視点)

* **目的**: サーバーのプロビジョニングスクリプト内で、Dockerが使用しているCgroupドライバが `systemd` であることを確認する。
* **連携コマンド**: `grep`
* **解説**: `docker info` の出力の中から "Cgroup Driver" という行を `grep` でフィルタリングし、設定が正しいかを自動的にチェックします。
* **コマンド例**:

		```bash
    # Cgroup Driverがsystemdであることを確認

    docker info | grep "Cgroup Driver: systemd"

    ```



## オプション説明

| オプション | 説明 |
| :--- | :--- |
| **出力形式** | |
| ⭐ `-f`, `--format <Go template>`| Go言語のテンプレートを使い、出力から特定の情報だけを抽出します。スクリプトでの利用に便利です。 |

## よく使われるオプション組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: 新しく構築したDockerホストが意図した通りに設定されているかを確認する。
* **組み合わせ**: `docker info`
* **解説**: ストレージドライバは `overlay2` か、ロギングドライバは `journald` か、セキュリティオプションは有効かなどを一目で確認します。
* **例**:

		```bash
    # Dockerホストの全体像を把握

    docker info

    ```



## 2. ブルーチーム視点

* **タスク**: 侵害された可能性のあるDockerホストの構成を把握する。
* **組み合わせ**: `docker info`
* **解説**: **インシデントレスポンスの初期調査**。`Logging Driver` (ログの保存場所)、`Docker Root Dir` (データディレクトリ)、`Security Options` (セキュリティ機能の有効状況) などを特定し、調査の足がかりとします。
* **例**:

		```bash
    # 調査対象ホストのDocker構成を記録

    sudo docker info > host-docker-info.txt

    ```



## 3. レッドチーム視点

* **タスク**: 侵入したホストの偵察を行い、権限昇格の足がかりを探す。
* **組み合わせ**: `docker info`
* **解説**: 攻撃者は `Kernel Version` からカーネルエクスプロイトの可能性を探り、`Security Options` が無効になっていればコンテナエスケープが容易になると判断します。`Swarm: active` であれば、他のクラスタノードへの横展開を狙います。
* **例**:

		```bash

    # 侵入後にDockerホストのセキュリティ設定を偵察

    docker info | grep -i "Security Options" -A 5

    ```



## エラーメッセージとトラブルシューティング

* 一般的なエラーは [Linux共通のトラブルシューティング](../linux/troubleshooting_common_errors.md) を参照。

1. **エラーメッセージ例 1**: `Cannot connect to the Docker daemon at unix:///var/run/docker.sock. Is the docker daemon running?`
		* **考えられる原因**: Dockerデーモンが起動していないか、実行ユーザーにDockerソケットへのアクセス権限がありません。
		* **解決策**: `sudo systemctl status docker` でデーモンの状態を確認・起動します。`sudo` を付けてコマンドを実行するか、現在のユーザーを `docker` グループに追加します。

## 環境変数と設定ファイル

* `docker info` の動作に直接影響する環境変数は通常ありません。
* 一般的な環境変数・設定ファイルは [Linux共通の環境変数・設定ファイル](../linux/environment_and_config.md) を参照。

## セキュリティに関する考慮事項

### 脆弱性と悪用事例

* **脆弱性**: `docker info` 自体ではなく、その出力が**情報漏洩**に繋がります。
* **悪用シナリオ**: `docker info` の出力は、システムのセキュリティ体制や構成を把握するための「設計図」となり得ます。攻撃者はこの情報を元に、システムの脆弱性を探します。

### GTFOBins / LOLBAS における利用例

`docker info` は情報表示のみを行うコマンドであり、GTFOBinsにはリストされていません。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: Dockerコマンドの実行権限を厳格に管理する。
* **Detection (検知)**: `docker info` の実行を `auditd` で監視し、不審なユーザーによる偵察活動がないかを確認する。

## 注意点・補足

* **警告 (WARNINGS) の確認**: コマンド出力の末尾に `WARNINGS` が表示されている場合は、内容を必ず確認してください。システムのパフォーマンスやセキュリティに影響する重要な設定不備を示している可能性があります。

---


---
tags:
  - docker_system
  - docker
  - system_management
  - cleanup
created: 2025-06-29 15:02
modified: 2026-02-20 17:36
environment:
vulnearability: []
knowledge_category: Command
---

# Command - docker - docker system - Dockerシステム全体を管理する

## 概要

`docker system` は、Dockerデーモンの管理、ディスク使用量の確認、未使用リソースの一括削除（クリーンアップ）など、Dockerシステム全体に関わる操作を行うためのコマンドスイートです。個別のコンテナやイメージではなく、Docker環境全体のメンテナンスや情報収集に使用されます。

(出自: `docker` パッケージに含まれる)

## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 |
| :--- | :--- |:---|
| ⭐ **`docker info` / `version`** | これらは `docker system` のサブコマンドとしても存在しますが、トップレベルコマンド (`docker info`) としても利用できます。機能は同じです。 | - |

## 🌐 主要なサブコマンド

`docker system` の操作は、操作対象となるサブコマンドを指定することから始まります。

| コマンド | 概要 | 詳細 |
| :--- | :--- | :--- |
| ⭐ **`docker system df`** | Dockerが使用する**ディスク容量**の内訳を表示する | [./docker_system_df.md](Command%20-%20docker%20-%20docker%20system%20df%20-%20Dockerのディスク使用量を表示する.md) |
| ⭐ **`docker system prune`**| 未使用のコンテナ、ネットワーク、イメージなどの**リソースを一括削除**する | [./docker_system_prune.md](Command%20-%20docker%20-%20docker%20system%20prune%20-%20未使用のDockerリソースを一括削除する.md) |
| **`docker system info`** | Dockerシステムの**詳細情報**を表示する (`docker info` と同義) | [./docker_info.md](Command%20-%20docker%20-%20docker%20info%20-%20Dockerシステム全体の情報を表示する.md) |
| **`docker system events`** | Dockerデーモンからの**リアルタイムイベント**を表示する | (未作成) |

---

## よく使われるオプション組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: Dockerホストのディスク使用状況を確認し、不要なリソースをクリーンアップする。
* **組み合わせ**: `docker system df` → `docker system prune`
* **解説**: まず `df` で解放可能な (`RECLAIMABLE`) 容量を確認し、その後 `prune` で安全にクリーンアップを実行する、という一連の流れは、Dockerホストの定期的なメンテナンスにおける基本です。
* **例**:

		```bash
		# 1. ディスク使用状況のサマリーを確認

		docker system df

		# 2. 確認後、未使用リソースを一括削除

		docker system prune -f

		```

## 2. ブルーチーム視点

* **タスク**: 侵害されたホストのDocker環境の構成を把握する。
* **組み合わせ**: `docker system info`
* **解説**: `info` サブコマンド（`docker info`と同じ）を使って、`Logging Driver` や `Security Options` (SELinux, AppArmorなど) を確認し、システムのセキュリティ構成やログの保存場所を特定します。
* **例**:

		```bash
		# 調査対象ホストのDocker構成を記録

		sudo docker system info > host-docker-info.txt

		```

## 3. レッドチーム視点

* **タスク**: 侵入したホストのDocker環境を偵察し、攻撃の足がかりを探す。
* **組み合わせ**: `docker system info`
* **解説**: `info` の出力から `Kernel Version` を特定してカーネルエクスプロイトを探したり、`Security Options` が無効になっていることを確認してコンテナエスケープの可能性を探ったりします。
* **例**:

		```bash
		# 侵入後にDockerホストのセキュリティ設定を偵察

		docker system info | grep -i "Security Options" -A 5

		```

## エラーメッセージとトラブルシューティング

* 一般的なエラーは [Linux共通のトラブルシューティング](../linux/troubleshooting_common_errors.md) を参照。

1. **エラーメッセージ例 1**: `Cannot connect to the Docker daemon...`
		* **考えられる原因**: Dockerデーモンが起動していないか、実行ユーザーにDockerソケットへのアクセス権限がありません。
		* **解決策**: `sudo systemctl status docker` でデーモンの状態を確認・起動します。`sudo` を付けてコマンドを実行するか、現在のユーザーを `docker` グループに追加します。

## 環境変数と設定ファイル

* 一般的な環境変数・設定ファイルは [Linux共通の環境変数・設定ファイル](../linux/environment_and_config.md) を参照。

## セキュリティに関する考慮事項

### 脆弱性と悪用事例

* **脆弱性**: `prune` コマンドの**破壊的な性質**。
* **悪用シナリオ**: 攻撃者が `docker system prune -a --volumes` を実行すると、本番環境のデータボリュームが削除され、深刻な**サービス妨害 (DoS)** と**データ損失**を引き起こす可能性があります。

### GTFOBins / LOLBAS における利用例

`docker system` は `docker` コマンドの一部として扱われます。`sudo docker` の権限があれば、任意の操作が可能です。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: Dockerソケットへのアクセス権限を厳格に管理する。本番環境での `prune` コマンドの実行は、変更管理プロセスを通じて計画的に行う。
* **Detection (検知)**: `auditd` で `docker system prune` の実行を監視し、計画外のクリーンアップ操作がないかを確認する。

## 注意点・補足

* **`system prune` の危険性**: `--volumes` オプションは、データベースのデータなどが格納された名前付きボリュームを削除する可能性があるため、**本番環境での安易な実行は絶対に避けるべきです**。

---


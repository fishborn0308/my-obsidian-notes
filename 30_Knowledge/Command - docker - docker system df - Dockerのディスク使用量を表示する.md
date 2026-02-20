---
tags:
  - docker_system_df
  - docker
  - disk_usage
  - cleanup
created: 2025-06-29 15:02
modified: 2026-02-20 17:36
environment:
vulnearability: []
knowledge_category: Command
---

# Command - docker - docker system df - Dockerのディスク使用量を表示する

## 概要

`docker system df` は、Dockerが使用しているディスク容量の内訳をオブジェクト（イメージ、コンテナ、ボリューム、ビルドキャッシュ）ごとに要約して表示するコマンドです。Linuxの `df` (disk free) コマンドに似ていますが、Dockerリソースに特化しています。このコマンドを使うことで、どの種類のオブジェクトがディスク容量を最も消費しているか、また、どれくらいの容量が解放可能（reclaimable）であるかを素早く把握できます。

(出自: `docker` パッケージに含まれる)

## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 |
| :--- | :--- |:---|
| ⭐ **`df` (Linux)** | **ファイルシステム全体**のディスク使用量を表示します。`docker system df` は、その中でも **Dockerが管理するリソース**に限定して使用量を表示します。 | - |
| **`docker images`** | **個々**のイメージのサイズを表示します。`docker system df` は、イメージ**全体**の合計サイズと解放可能な容量を表示します。 | - |
| **`du -sh /var/lib/docker/`**| Dockerのルートディレクトリ全体の合計サイズを計算します。`docker system df` は、より詳細な内訳（解放可能な容量など）を提供します。 | `docker system df` |

## よく連携されるコマンド

`docker system df` は、ディスク容量を解放するための `docker system prune` と一連のワークフローで連携します。

### シナリオ例: ディスク使用状況を確認し、クリーンアップする (インフラ構築・運用視点)

* **目的**: Dockerホストのディスク容量が逼迫してきた際に、解放可能な容量を確認し、安全にクリーンアップする。
* **連携コマンド**: `docker system prune`
* **解説**: まず `docker system df` を実行して、`RECLAIMABLE` な容量がどれくらいあるかを確認します。この値が大きければ、`docker system prune` を実行することで安全に容量を確保できると判断できます。
* **コマンド例**:

		```bash
		# 1. Dockerのディスク使用状況を確認

		docker system df

		# 2. (RECLAIMABLEな容量が多いことを確認後) 未使用リソースを一括削除

		docker system prune

		```

## オプション説明

| オプション | 説明 |
| :--- | :--- |
| **表示形式** | |
| ⭐ `-v`, `--verbose` | 各オブジェクトの詳細なリスト（どのイメージが未使用かなど）も併せて表示します。 |

## よく使われるオプション組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: Dockerホストのディスク使用状況を素早く確認する。
* **組み合わせ**: `docker system df`
* **解説**: サーバーのディスク使用量アラートが発報された場合、`df -h` と共にこのコマンドを実行し、原因がDockerリソースにあるかを特定します。
* **例**:

		```bash
		# Dockerのディスク使用状況を素早く確認

		docker system df

		```

## 2. ブルーチーム視点

* **タスク**: 侵害されたホストで、不審なディスク消費がないかを確認する。
* **組み合わせ**: `docker system df -v`
* **解説**: `-v` オプションで詳細を表示し、意図しないイメージやコンテナ、ボリュームが大量に作成されていないかを確認します。攻撃者がデータを窃取してコンテナ内にため込んでいる場合、`Containers` や `Local Volumes` の `SIZE` が急激に増加する可能性があります。
* **例**: -

## 3. レッドチーム視点

* **タスク**: 侵入したホストのDocker環境の規模と活動状況を偵察する。
* **組み合わせ**: `docker system df`
* **解説**: `TOTAL` と `ACTIVE` の数から、そのホストが本番環境である可能性を推測します。`Build Cache` のサイズが大きければ、CI/CDのビルドサーバーである可能性があり、ソースコードや認証情報へのアクセスが期待できるかもしれません。
* **例**: -

# エラーメッセージとトラブルシューティング

* 一般的なエラーは [Linux共通のトラブルシューティング](../linux/troubleshooting_common_errors.md) を参照。

1. **現象**: **`RECLAIMABLE` な容量が多いのに、`docker system prune` を実行しても容量があまり解放されない。**
		* **考えられる原因**: `RECLAIMABLE` には、`dangling`イメージだけでなく、**未使用の**（コンテナから参照されていない）イメージやボリュームも含まれる場合があります。デフォルトの `docker system prune` は`dangling`なリソースしか削除しません。
		* **解決策**: `docker image prune -a` や `docker volume prune`、あるいは `docker system prune -a --volumes` を実行して、より広範な未使用リソースを削除する必要があります。

# 環境変数と設定ファイル

* `docker system df` の動作に直接影響する環境変数や設定ファイルは通常ありません。
* 一般的な環境変数・設定ファイルは [Linux共通の環境変数・設定ファイル](../linux/environment_and_config.md) を参照。

# セキュリティに関する考慮事項

## 脆弱性と悪用事例

* **脆弱性**: `docker system df` は読み取り専用ですが、その出力が**情報漏洩**に繋がります。
* **悪用シナリオ**: コマンドの出力から、Docker環境の規模、活動状況、さらにはビルドサーバーである可能性などを推測され、攻撃計画に利用される可能性があります。

## GTFOBins / LOLBAS における利用例

`docker system df` は `docker` コマンドの一部として扱われます。`sudo docker` の権限があれば、任意の操作が可能です。

## 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: Dockerソケットへのアクセス権限を厳格に管理する。
* **Detection (検知)**: `auditd` で `docker system df` の実行を監視し、不審なユーザーによる偵察活動がないかを確認する。

# 注意点・補足

* **`Build Cache` とは**: `docker build` を実行した際に、ビルドを高速化するために保存される中間レイヤーなどのキャッシュです。`docker builder prune` でクリーンアップできます。

---


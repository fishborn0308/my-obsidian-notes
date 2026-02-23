---
tags:
  - docker_inspect
  - docker
  - troubleshooting
created: 2025-06-29 15:02
modified: 2026-02-23 09:52
environment:
vulnearability: []
knowledge_category: Command
---

# Command - docker - docker inspect - Dockerオブジェクトの詳細情報を表示する

## 概要

`docker inspect` は、コンテナ、イメージ、ボリューム、ネットワークなど、Dockerが管理するオブジェクトに関する**低レベルで詳細な情報**をJSON形式で出力するコマンドです。`docker ps` や `docker images` が表示するサマリー情報とは異なり、`inspect` はオブジェクトの設定、実行時の状態、ネットワーク設定、マウントされたボリュームなど、あらゆるメタデータを網羅的に提供します。主に、詳細なトラブルシューティングや、スクリプトによる自動化で特定の設定値を取得する際に利用されます。

(出自: `docker` パッケージに含まれる)

## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 |
| :--- | :--- |:---|
| ⭐ **`docker ps`, `docker images`**| オブジェクトの**概要**を人間が読みやすい表形式で表示します。`inspect` は**一つのオブジェクト**に関する**全ての詳細情報**を機械が処理しやすいJSON形式で表示します。 | - |
| **`docker info`** | Docker**システム全体**に関する広範な情報を表示します。`inspect` は**個別のオブジェクト**に焦点を当てます。 | - |

## よく連携されるコマンド

`docker inspect` の出力は巨大なJSONなので、`--format` オプションで整形するか、`jq` コマンドと連携して必要な情報だけを抽出するのが一般的です。

### シナリオ例: コンテナのIPアドレスを取得する (インフラ構築・運用視点)

* **目的**: 実行中のコンテナに割り当てられたIPアドレスを正確に取得する。
* **連携コマンド**: `--format` (Go template)
* **解説**: `--format` オプションとGo言語のテンプレート構文を使い、巨大なJSON出力からネットワーク設定の中のIPアドレスだけをピンポイントで抜き出します。
* **コマンド例**:

		```bash
    # webappコンテナのIPアドレスを取得して変数に格納

    WEBAPP_IP=$(docker inspect -f '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' webapp)

    echo "Webapp IP is: $WEBAPP_IP"

    ```





## オプション説明

| オプション | 説明 |
| :--- | :--- |
| **出力形式** | |
| ⭐ `-f`, `--format <Go template>`| Go言語のテンプレートを使い、出力から特定の情報だけを抽出します。 |
| `-s`, `--size` | (コンテナの場合) コンテナの書き込み可能レイヤーの合計サイズを表示します。 |

## よく使われるオプション組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: コンテナが起動しない、または期待通りに動作しない原因を詳細に調査する。
* **組み合わせ**: `docker inspect <container_id>`
* **解説**: オプションなしでコンテナの全情報をダンプします。`State.ExitCode`, `State.Error` を確認して終了コードやエラーメッセージを把握したり、`Mounts` セクションを見てボリュームが正しくマウントされているかなどを詳細に調査します。
* **例**:

		```bash
    # 起動に失敗したコンテナ 'my-app' の詳細情報を調査

    docker inspect my-app

    ```





## 2. ブルーチーム視点

* **タスク**: 侵害された疑いのあるコンテナの構成情報と動的な状態を完全に把握する。
* **組み合わせ**: `docker inspect <suspicious_container_id>`
* **解説**: **フォレンジック調査の基本**。不審なコンテナの `inspect` 結果をファイルに保存し、証拠保全します。`Mounts` (危険なマウントはないか)、`Config.Env` (機密情報はないか)、`NetworkSettings` (不審な接続はないか) などを精査します。
* **例**:

		```bash
    # 調査対象コンテナの詳細情報をファイルに保存

    docker inspect container-of-interest > container-of-interest_inspect.json

    ```





## 3. レッドチーム視点

* **タスク**: 侵入したホスト上で、稼働中のコンテナから攻撃の足がかりとなる情報を収集する。
* **組み合わせ**: `docker inspect <target_container_id>`
* **解説**: **偵察の最重要コマンドの一つ**。攻撃者は `inspect` の出力を精査し、`Config.Env` にハードコードされたパスワードやAPIキーなどの認証情報を窃取したり、`/var/run/docker.sock` のような危険なマウントポイントを探してコンテナエスケープを試みたりします。
* **例**:

		```bash
    # ターゲットコンテナの環境変数を探る

    docker inspect target-container | grep -i "PASS\|KEY\|TOKEN"

    ```





## エラーメッセージとトラブルシューティング

* 一般的なエラーは [Linux共通のトラブルシューティング](../linux/troubleshooting_common_errors.md) を参照。

1. **エラーメッセージ例 1**: `Error: No such object: <object_name>`
		* **考えられる原因**: 指定した名前またはIDのオブジェクト(コンテナ、イメージなど)が存在しません。
		* **解決策**: `docker ps -a` や `docker images` で、正しい名前またはIDを確認してください。

## 環境変数と設定ファイル

* `docker inspect` の動作に直接影響する環境変数は通常ありません。
* 一般的な環境変数・設定ファイルは [Linux共通の環境変数・設定ファイル](../linux/environment_and_config.md) を参照。

## セキュリティに関する考慮事項

### 脆弱性と悪用事例

* **脆弱性**: **深刻な情報漏洩**。
* **悪用シナリオ**: **環境変数 (`Env`) にパスワードやAPIキーを直接設定するのは非常に危険なプラクティスです**。`docker inspect` を実行できる権限を持つユーザーは、これらの情報を平文で閲覧できてしまいます。

### GTFOBins / LOLBAS における利用例

`docker inspect` は情報表示が主目的のためGTFOBinsにはリストされていません。しかし、リストされている `docker` コマンドによる権限昇格攻撃の**偵察段階**で不可欠な役割を果たします。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: **機密情報は環境変数に保存せず**、Docker Secretsや外部のシークレット管理ツールを使用する。Dockerソケットへのアクセス権を厳格に管理する。
* **Detection (検知)**: `docker inspect` コマンドの実行、特に不審なユーザーからの実行を `auditd` で監視する。

## 注意点・補足

* **`jq`との連携**: `docker inspect` の出力はJSONなので、`jq` というコマンドラインJSONプロセッサと組み合わせることで、`--format` よりもさらに複雑で柔軟なデータ抽出が可能です。

		```bash
    # コンテナの全マウント情報をきれいに表示

    docker inspect my-container | jq '.[0].Mounts'

    ```

---


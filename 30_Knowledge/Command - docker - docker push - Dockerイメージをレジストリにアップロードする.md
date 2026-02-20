---
tags:
  - docker_push
  - docker
  - image_management
  - registry
created: 2025-06-29 15:02
modified: 2026-02-20 17:36
environment:
vulnearability: []
knowledge_category: Command
---

# Command - docker - docker push - Dockerイメージをレジストリにアップロードする

## 概要

`docker push` は、ローカルに存在するDockerイメージを、Docker HubやGoogle Artifact Registry (GAR), Amazon Elastic Container Registry (ECR) などのリモートのコンテナイメージレジストリにアップロード（プッシュ）するためのコマンドです。`push` することで、ビルドしたイメージをチームメンバーと共有したり、本番サーバーにデプロイしたり、CI/CDパイプラインを通じて配布したりすることが可能になります。

(出自: `docker` パッケージに含まれる)

## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 |
| :--- | :--- |:---|
| ⭐ **`docker pull`** | `push` の**逆の操作**。リモートレジストリからローカルにイメージをダウンロードします。 | - |
| **`docker build`** | `Dockerfile` から**イメージをローカルに作成**します。`push` は、こうして作成されたイメージをリモートにアップロードするための次のステップです。 | - |
| **`git push`** | `git` のコマンドであり、ソースコードの変更をリモートリポジトリにアップロードします。`docker push` が扱うのは**コンテナイメージ**です。 | - |

## よく連携されるコマンド

`docker push` は、イメージにレジストリ用の名前を付ける `docker tag` と、レジストリに認証する `docker login` と一連のワークフローで連携します。

### シナリオ例: ビルドしたイメージをレジストリにプッシュする (インフラ構築・運用視点)

* **目的**: CI/CDパイプラインでビルドしたアプリケーションイメージを、プライベートレジストリにアップロードする。
* **連携コマンド**: `docker build`, `docker tag`, `docker login`, `docker push`
* **解説**: ビルドしたイメージにレジストリの命名規則に沿ったタグを付け、レジストリにログインしてからプッシュします。
* **コマンド例**:

		```bash
		# 1. ローカル名でイメージをビルド

		docker build -t my-app:1.2 .

		# 2. ECRリポジトリの命名規則に合わせてタグ付け

		docker tag my-app:1.2 [123456789012.dkr.ecr.ap-northeast-1.amazonaws.com/my-app:1.2](https://123456789012.dkr.ecr.ap-northeast-1.amazonaws.com/my-app:1.2)

		# 3. レジストリにログイン (事前に認証設定が必要)

		docker login -u AWS -p $(aws ecr get-login-password) 123456789012.dkr.ecr.ap-northeast-1.amazonaws.com

		# 4. イメージをプッシュ

		docker push [123456789012.dkr.ecr.ap-northeast-1.amazonaws.com/my-app:1.2](https://123456789012.dkr.ecr.ap-northeast-1.amazonaws.com/my-app:1.2)

		```

## オプション説明

| オプション | 説明 |
| :--- | :--- |
| **基本操作** | |
| ⭐ `<name>[:<tag>]` | プッシュするイメージの名前とタグを指定します。 |
| **その他** | |
| `-a`, `--all-tags` | 指定したリポジトリに付けられているすべてのタグをプッシュします。 |
| `-q`, `--quiet` | 詳細な進捗状況を表示せず、静かに出力します。 |

## よく使われるオプション組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: CI/CDパイプラインでビルドとテストが完了したイメージを、プライベートレジストリにプッシュし、デプロイ可能な状態にする。
* **組み合わせ**: `docker push <registry>/<project>/<image>:<tag>`
* **解説**: 上記「よく連携されるコマンド」のシナリオ例を参照。これが最も代表的な使い方です。
* **例**: -

### 2. ブルーチーム視点

* **タスク**: 調査対象のマルウェアなどをコンテナ化し、分析用のプライベートレジストリにアップロードして保管・共有する。
* **組み合わせ**: `docker push <private_malware_zoo_registry>/<case_id>:<sample_name>`
* **解説**: マルウェア検体をコンテナイメージ内に封じ込め、隔離されたプライベートレジストリに `push` することで、他のアナリストが安全に検体を `pull` して分析できるようになります。
* **例**: -

### 3. レッドチーム視点

* **タスク**: 悪意のあるペイロードを仕込んだイメージを、公式イメージに似せた名前で公開レジストリにアップロードする（サプライチェーン攻撃）。
* **組み合わせ**: `docker push <public_registry>/<typo_squatted_image_name>:latest`
* **解説**: `postgres` を `postgress` としたり、人気ライブラリに似せた名前のイメージを公開したりします。開発者がタイプミスでこの悪意のあるイメージを `pull` して実行することを狙います。
* **例**: -

## エラーメッセージとトラブルシューティング

* 一般的なエラーは [Linux共通のトラブルシューティング](../linux/troubleshooting_common_errors.md) を参照。

1. **エラーメッセージ例 1**: `denied: requested access to the resource is denied`
		* **考えられる原因**: レジストリに `docker login` していないか、ログインしているユーザーに書き込み(プッシュ)権限がありません。
		* **解決策**: `docker login <registry_host>` を実行して認証情報を確認してください。クラウドのレジストリの場合は、IAMロールやサービスアカウントの権限を確認します。

2. **エラーメッセージ例 2**: `name unknown: repository not found`
		* **考えられる原因**: プッシュ先のレジストリに、リポジトリ(イメージを格納する場所)がまだ作成されていません。
		* **解決策**: レジストリのWebコンソールやCLIで、対応するリポジトリを作成してください。

## 環境変数と設定ファイル

* `docker push` の動作に直接影響する環境変数は通常ありません。
* 一般的な環境変数・設定ファイルは [Linux共通の環境変数・設定ファイル](../linux/environment_and_config.md) を参照。

## セキュリティに関する考慮事項

### 脆弱性と悪用事例

* **脆弱性**: **サプライチェーン攻撃**。
* **悪用シナリオ**: 攻撃者がCI/CDパイプラインを侵害した場合、正規のビルドプロセスに割り込み、悪意のあるコードを含んだイメージをプライベートレジストリに `push` する可能性があります。これにより、そのイメージを利用する全ての環境にマルウェアが拡散してしまいます。

### GTFOBins / LOLBAS における利用例

`docker push` は `docker` コマンドの一部として扱われます。`sudo docker` の権限があれば、任意の操作が可能です。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: CI/CDサーバーなど共有環境では、短命なアクセストークンを使用し、ビルドジョブ終了後には `docker logout` で認証情報をクリアする。
* **Detection (検知)**: **Docker Content Trust (イメージ署名)** を利用し、`push` 時にイメージにデジタル署名を強制する。

## 注意点・補足

* **タグ付けの重要性**: `docker push` は、イメージ名に含まれる**レジストリホスト名**を見て、どこにアップロードすべきかを判断します。`push` する前には、`docker tag` を使って必ず正しい完全な名前をイメージに付けてください。

---


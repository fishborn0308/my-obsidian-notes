---
tags:
  - 'docker_tag'
  - 'docker'
  - 'image_management'
created: 2025-06-29 15:02
modified: 2026-02-20 16:24
environment:
vulnearability: []
knowledge_category: Command
---

# `docker tag` - Dockerイメージにタグを付ける

## 概要

`docker tag` は、既存のローカルイメージに対して、新しい別名（タグ）を付けるためのコマンドです。この操作はイメージのコピーを作成するわけではなく、**同じイメージの実体（IMAGE ID）に対して、新しいポインターやラベルを追加する**ようなものです。主に、ビルドしたイメージをリモートのレジストリに `push` する前に、レジストリが要求する命名規則（例: `registry.example.com/my-app:1.0`）に合わせて名前を付け直したり、特定のバージョンを `latest` としてマークしたりするために使用されます。

(出自: `docker` パッケージに含まれる)

## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 |
| :--- | :--- |:---|
| ⭐ **`docker build -t`** | `docker build` の `-t` オプションは、**イメージのビルド時**に名前とタグを付けます。`docker tag` は、**すでに存在するイメージ**に対して後からタグを追加します。 | - |
| **`git tag`** | `git` のコマンドであり、ソースコードの特定のコミットに対してバージョンタグなどを付けます。`docker tag` が扱うのは**コンテナイメージ**です。 | - |

## よく連携されるコマンド

`docker tag` は、`docker build` で作成したイメージを `docker push` でアップロードするまでの中間ステップとして、これらのコマンドと密接に連携します。

### シナリオ例: ビルドしたイメージをレジストリにプッシュする (インフラ構築・運用視点)

* **目的**: CI/CDパイプラインでビルドしたアプリケーションイメージを、プライベートレジストリにアップロードする。
* **連携コマンド**: `docker build`, `docker push`
* **解説**: `docker build` でローカルイメージを作成した後、`docker tag` でレジストリの命名規則に沿った名前を付け、`docker push` でアップロードします。
* **コマンド例**:

    ```bash
    # 1. ローカル名でイメージをビルド
    docker build -t my-app:1.2 .

    # 2. ECRリポジトリの命名規則に合わせてタグ付け
    docker tag my-app:1.2 [123456789012.dkr.ecr.ap-northeast-1.amazonaws.com/my-app:1.2](https://123456789012.dkr.ecr.ap-northeast-1.amazonaws.com/my-app:1.2)

    # 3. レジストリにプッシュ
    docker push [123456789012.dkr.ecr.ap-northeast-1.amazonaws.com/my-app:1.2](https://123456789012.dkr.ecr.ap-northeast-1.amazonaws.com/my-app:1.2)
    ```

## オプション説明

`docker tag` コマンド自体に頻繁に使われるオプションはありません。引数である名前とタグの指定方法が重要になります。

| 引数 | 説明 |
| :--- | :--- |
| ⭐ `<source_image>[:<tag>]` | タグ付けしたい既存のイメージの名前またはID。 |
| ⭐ `<target_image>[:<tag>]` | 新しく付ける名前とタグ。 |

## よく使われるオプション組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: ビルドしたイメージをプライベートレジストリに `push` するために、レジストリの命名規則に従ったタグを付ける。
* **組み合わせ**: `docker tag <local_name>:<version> <registry>/<repo>/<name>:<version>`
* **解説**: 上記「よく連携されるコマンド」シナリオの通り、CI/CDパイプラインにおける最も代表的な使い方です。
* **例**: -

### 2. ブルーチーム視点

* **タスク**: 調査対象のイメージに、管理しやすく分かりやすい名前を付ける。
* **組み合わせ**: `docker tag <image_id> <descriptive_name>:<tag>`
* **解説**: インシデント調査中に、`a1b2c3d4e5f6` のようなIDを持つ不審なイメージを発見した場合、`docker tag a1b2c3d4e5f6 malware-case-007:suspicious` のようにタグ付けします。これにより、調査チーム内でイメージを正確に参照できます。
* **例**: -

### 3. レッドチーム視点

* **タスク**: **防御回避 (Defense Evasion)**。悪意のあるイメージに、正規のイメージであるかのように見せかける偽のタグを付ける。
* **組み合わせ**: `docker tag <malicious_image_id> <common_image_name>:<tag>`
* **解説**: 攻撃者がホスト上に配置した `kali-linux` ベースのツールキットイメージに、`docker tag attacker/kali-tools:latest ubuntu:22.04` のようにタグ付けして偽装します。`docker images` を実行した管理者が、それが正規のUbuntuイメージであると誤認し、見過ごすことを狙います。
* **例**: -

## エラーメッセージとトラブルシューティング

* 一般的なエラーは [Linux共通のトラブルシューティング](../linux/troubleshooting_common_errors.md) を参照。

1. **エラーメッセージ例 1**: `Error response from daemon: No such image: <image_name>:<tag>`
    * **考えられる原因**: タグ付けの元として指定したソースイメージが存在しません。
    * **解決策**: `docker images` を実行し、ローカルに存在する正しいイメージ名、タグ、またはイメージIDをコピーして、再度コマンドを実行してください。

## 環境変数と設定ファイル

* `docker tag` の動作に直接影響する環境変数や設定ファイルは通常ありません。
* 一般的な環境変数・設定ファイルは [Linux共通の環境変数・設定ファイル](../linux/environment_and_config.md) を参照。

## セキュリティに関する考慮事項

### 脆弱性と悪用事例

* **脆弱性**: **タグの可変性 (Mutability)**。
* **悪用シナリオ**: 本番環境で `:latest` タグを指定してデプロイすると、意図しない、あるいは未検証のバージョンのイメージが実行されてしまう可能性があります。

### GTFOBins / LOLBAS における利用例

`docker tag` は `docker` コマンドの一部として扱われます。`sudo docker` の権限があれば、任意の操作が可能です。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: 本番環境では、`my-app:1.2.3` のような**不変なバージョンタグ**を使用するか、さらに厳密にはイメージの**ダイジェスト値** (`my-app@sha256:abcdef...`) を指定してデプロイする。
* **Detection (検知)**: レジストリの監査ログを監視し、重要なイメージタグが意図せず上書きされていないかを確認する。

## 注意点・補足

* **タグはただのラベル**: `docker tag` は軽量な操作です。イメージレイヤーのコピーなどは一切発生しません。同じ `IMAGE ID` を持つ2つの名前（タグ）が `docker images` のリストに表示されるだけです。

---


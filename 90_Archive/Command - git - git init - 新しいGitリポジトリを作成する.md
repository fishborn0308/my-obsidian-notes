---
tags:
  - git_init
  - git
created: 2025-06-29 15:02
modified: 2026-02-22 15:13
environment:
vulnearability: []
knowledge_category: Command
---

# Command - git - git init - 新しいGitリポジトリを作成する

## 概要

`git init` コマンドは、現在のディレクトリ、または指定したディレクトリを新しいGitリポジトリとして初期化します。このコマンドを実行すると、リポジトリの履歴や設定を格納するための `.git` という隠しディレクトリが作成され、Gitがそのディレクトリ内のファイルの変更履歴を追跡できるようになります。

(出自: `git` パッケージに含まれる)

## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 |
| :--- | :--- |:---|
| ⭐ **`git clone`** | **既存のリモート**リポジトリをローカルに複製します。`git init` は、**ローカルで新しい空の**リポジトリを作成します。 | - |

## よく連携されるコマンド

`git init` はGitを使い始める際に最初に実行されるコマンドであり、その後に他のGitコマンドが続きます。

### シナリオ例: 新しいプロジェクトを開始する (インフラ構築・運用視点)

* **目的**: 新規のインフラ構成管理ファイルやスクリプトをGitでバージョン管理する。
* **連携コマンド**: `git add`, `git commit`
* **解説**: まず `git init` でリポジトリを作成し、管理したいファイルを `git add` でステージングエリアに追加、最後に `git commit` で最初のコミットを作成します。
* **コマンド例**:

		```bash
    # このコマンドシーケンスで新しいリポジトリを開始する

    mkdir my_new_infra

    cd my_new_infra

    git init

    touch playbook.yml

    git add .

    git commit -m "Initial commit"

    ```




## オプション説明

| オプション | 説明 |
| :--- | :--- |
| **リポジトリタイプ** | |
| ⭐ `[<directory>]` | 指定したディレクトリに新しいリポジトリを作成します。省略するとカレントディレクトリに作成します。 |
| `--bare` | 作業ディレクトリを持たない「ベアリポジトリ」を作成します。通常、リモートリポジトリとして使用されます。 |
| **その他** | |
| `--initial-branch=<name>` | 最初のコミット時に作成されるブランチのデフォルト名を指定します。(Git 2.28以降) |
| `-b <name>` | `--initial-branch`の省略形です。 |

## よく使われるオプション組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: 新規にAnsibleのプレイブックやTerraformの設定ファイルを作成し、それをバージョン管理する。
* **組み合わせ**: `git init`
* **解説**: 新しいIaC (Infrastructure as Code) プロジェクトや、サーバーのデプロイメントスクリプトなどの管理を開始する際に使用します。
* **例**:

		```bash

    mkdir terraform_aws_vpc

    cd terraform_aws_vpc

    git init

    # terraformファイルを配置後

    git add .

    git commit -m "Initial commit for AWS VPC Terraform configuration"

    ```




## 2. ブルーチーム視点

* **タスク**: 独自のセキュリティ監視スクリプトや、`auditd` などの設定ファイル変更を追跡する。
* **組み合わせ**: `git init`
* **解説**: セキュリティツールやスクリプト、監査設定の変更履歴を管理する際に使用します。
* **例**:

		```bash

    mkdir security_audits

    cd security_audits

    git init

    # auditd設定ファイルなどをコピー・編集後

    cp /etc/audit/rules.d/audit.rules .

    git add audit.rules

    git commit -m "Initial commit of custom auditd rules"

    ```




## 3. レッドチーム視点

* **タスク**: チーム内で共有するカスタムエクスプロイトやスクリプトを格納するためのプライベートな「ベアリポジトリ」をセットアップする。
* **組み合わせ**: `git init --bare`
* **解説**: チーム内で共有するツールキットをプライベートなGitリポジトリで管理する場合など、ごく限定的なシナリオで使用されます。
* **例**:

		```bash
    # チーム内の共有サーバー上で実行

    mkdir /srv/git/redteam_tools.git

    cd /srv/git/redteam_tools.git

    git init --bare

    ```




## エラーメッセージとトラブルシューティング

* 一般的なエラーは [Linux共通のトラブルシューティング](../linux/troubleshooting_common_errors.md) を参照。

1. **エラーメッセージ例 1**: `fatal: destination path '<directory_name>' already exists and is not an empty directory.`
		* **考えられる原因**: 指定したディレクトリがすでに存在し、空でないため、そこに新しいリポジトリを作成できません。
		* **解決策**: 既存のディレクトリをGitリポジトリとして初期化する場合は、そのディレクトリ**内**で `git init` を引数なしで実行します。

## 環境変数と設定ファイル

* `git init` に直接影響する独自の環境変数はありません。
* 一般的な環境変数・設定ファイルは [Linux共通の環境変数・設定ファイル](../linux/environment_and_config.md) を参照。

### 関連する設定ファイル

* **`.git/config`**: 新しく初期化されたリポジトリ内に作成されるローカルリポジトリの設定ファイル。
* **`~/.gitconfig`**: グローバル設定ファイル。`git init` を実行する前に設定されているユーザー情報などが新しいリポジトリに影響します。

## セキュリティに関する考慮事項

### 脆弱性と悪用事例

* **脆弱性**: `git init` 自体に既知の深刻な脆弱性は報告されていません。
* **悪用シナリオ**: **機密情報の誤コミット**。`git init` でリポジトリを初期化した後、適切に `.gitignore` を設定せず、認証情報やAPIキーを含むファイルを誤ってコミットしてしまうケースが頻繁に発生します。

### GTFOBins / LOLBAS における利用例

`git init` は、直接的なコード実行機能を持たないため、GTFOBinsにはリストされていません。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: **`.gitignore` の徹底**。リポジトリを初期化したら、まず機密情報や一時ファイルなどを追跡対象から除外するために、厳格な `.gitignore` ファイルを配置する。
* **Detection (検知)**: プリコミットフック (`git-secrets`など) を利用して、コミットされる内容に機密情報が含まれていないかチェックする自動化を導入する。

## 注意点・補足

* `git init` を実行すると、現在のディレクトリに隠しディレクトリ `.git` が作成されます。この `.git` ディレクトリには、リポジトリの全ての履歴と設定が格納されており、削除すると履歴が失われます。

---


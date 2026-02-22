---
tags:
  - git_config
  - git
created: 2025-06-29 15:02
modified: 2026-02-22 09:30
environment:
vulnearability: []
knowledge_category: Command
---

# Command - git - git config - Gitの設定を管理する

## 概要

`git config`コマンドは、Gitの動作をカスタマイズするための設定値を読み書きします。これらの設定は、ユーザー名やメールアドレスといった基本的な情報から、エイリアス、デフォルトの動作、外部ツールとの連携まで、多岐にわたります。設定には3つのレベルがあり、それぞれ適用範囲が異なります。

(出自: `git` パッケージに含まれる)

## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 |
| :--- | :--- |:---|
| **手動編集** | `.gitconfig` や `.git/config` ファイルを直接テキストエディタで編集することもできますが、`git config` コマンドを使う方がタイプミスを防ぎ、安全です。 | `git config` |

## よく連携されるコマンド

`git config`はGitの動作自体を設定するため、他のコマンドの前提となる設定を行います。

### シナリオ例: はじめてのGit環境構築時にユーザー情報を設定する (インフラ構築・運用視点)

* **目的**: Gitのコミットに表示されるユーザー名とメールアドレスを設定し、誰がコミットを行ったかを明確にする。
* **連携コマンド**: `git commit`
* **解説**: Gitを使いはじめる際、または新しい環境をセットアップする際に、コミット作成者情報をグローバルに設定します。この設定は、そのシステム上のすべてのGitリポジトリに適用されます。
* **コマンド例**:

		```bash
    # Gitのユーザー名を設定

    git config --global user.name "Your Name"

    # Gitのメールアドレスを設定

    git config --global user.email "your.email@example.com"

    # 設定が正しく行われたか確認

    git config --global --list

    ```



## オプション説明

| オプション | 説明 |
| :--- | :--- |
| **設定レベル** | |
| ⭐ `--global` | **グローバル設定**。ユーザーのホームディレクトリの `~/.gitconfig` に書き込み、全リポジトリに適用。 |
| `--system` | **システム設定**。システム上の全ユーザーに適用。管理者権限が必要。 |
| `--local` | **ローカル設定** (デフォルト)。現在のリポジトリの `.git/config` に書き込み、そのリポジトリのみに適用。 |
| **操作** | |
| ⭐ `--list` | 設定を一覧表示します。 |
| `--get <key>` | 指定したキーの設定値を取得します。 |
| `--unset <key>` | 指定したキーの設定値を削除します。 |
| `--edit` | 設定ファイルをエディタで開きます。 |

## よく使われるオプション組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: WindowsとLinuxが混在する環境で、改行コードの問題によるデプロイ失敗を避ける。
* **組み合わせ**: `git config --global core.autocrlf <value>`
* **解説**: `core.autocrlf` を設定することで、Gitが自動的に改行コードを変換し、クロスプラットフォームでのコラボレーションをスムーズにします。
* **例**:

		```bash
    # Windowsユーザーの場合

    git config --global core.autocrlf true

    # Linux/macOSユーザーの場合 (推奨)

    git config --global core.autocrlf input

    ```



## 2. ブルーチーム視点

* **タスク**: 監査証跡の信頼性を高めるため、全てのGitコミットにGPG署名を義務付ける。
* **組み合わせ**: `git config --global commit.gpgsign true`
* **解説**: コミット署名は、コミットの作成者が本人であることを暗号学的に証明し、悪意のあるコミットインジェクションや履歴の改ざんを防ぐ重要なセキュリティ対策です。
* **例**:

		```bash
    # すべてのコミットに自動的にGPG署名するように設定

    git config --global commit.gpgsign true

    # 使用するGPG署名キーのIDを設定

    git config --global user.signingkey <GPG_KEY_ID>

    ```



## 3. レッドチーム視点

* **タスク**: 侵入したシステムで、自身の痕跡を偽装または隠蔽する。
* **組み合わせ**: `git config --local user.email "fake@example.com"`
* **解説**: ターゲットシステムへの侵入後、Gitリポジトリを操作する際に、ローカルリポジトリの設定を一時的に変更し、コミットの著者情報を偽装します。
* **例**:

		```bash
    # 現在のリポジトリでのコミットのユーザー名を偽装

    git config --local user.name "Anonymous User"

    # 現在のリポジトリでのコミットのメールアドレスを偽装

    git config --local user.email "fake_email@example.com"

    ```



## エラーメッセージとトラブルシューティング

* 一般的なエラーは [Linux共通のトラブルシューティング](../linux/troubleshooting_common_errors.md) を参照。

1. **エラーメッセージ例 1**: `error: could not lock config file .git/config: File exists`
		* **考えられる原因**: 別のGitプロセスが設定ファイルを変更しているか、以前のGit操作が異常終了し、ロックファイルが残っています。
		* **解決策**: 他のGit操作が実行されていないことを確認し、`.git/config.lock` ファイルが存在する場合は手動で削除します。

## 環境変数と設定ファイル

* 一般的な環境変数・設定ファイルは [Linux共通の環境変数・設定ファイル](../linux/environment_and_config.md) を参照。

### 関連する設定ファイル

* ⭐ **`~/.gitconfig`**: **グローバル設定ファイル**。
* ⭐ **`.git/config`**: **ローカル設定ファイル**。
* **`$(prefix)/etc/gitconfig`**: **システム設定ファイル**。

## セキュリティに関する考慮事項

### 脆弱性と悪用事例

* **脆弱性**: **設定値のインジェクション**。`core.editor` など、外部コマンドを呼び出す設定に不正なスクリプトが注入される可能性があります。
* **悪用シナリオ**: 攻撃者が被害者の `.gitconfig` ファイルを改ざんし、`[core] editor = evil_script.sh` と設定。`git commit` 実行時に `evil_script.sh` が実行され、機密情報が流出する。

### GTFOBins / LOLBAS における利用例

`git config` は設定の読み書きに特化しており、直接的な悪用機能は提供しないため、GTFOBinsにはリストされていません。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: **コミット署名を強制する**。リモートリポジトリの設定で、署名付きコミットのみを許可するブランチ保護ルールを有効にする。
* **Detection (検知)**: FIMツールで、`.gitconfig` や `.git/config` ファイルの変更を監視する。

## 注意点・補足

* **設定の優先順位**: Gitの設定は、**ローカル > グローバル > システム**の順に優先されます。
* **コマンドのエイリアス**: `git config --global alias.<alias_name> "<command>"` で、よく使うGitコマンドに短いエイリアスを設定できます。

---


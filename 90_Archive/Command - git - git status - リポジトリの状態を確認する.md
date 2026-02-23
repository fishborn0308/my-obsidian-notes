---
tags:
  - git_status
  - git
created: 2025-06-29 15:02
modified: 2026-02-23 09:52
environment:
vulnearability: []
knowledge_category: Command
---

# Command - git - git status - リポジトリの状態を確認する

## 概要

`git status` コマンドは、作業ディレクトリとステージングエリアの状態を表示します。このコマンドは、どのファイルが変更されたか、どのファイルがステージングされているか、どのファイルがまだGitによって追跡されていないかといった情報を提供します。`git add` や `git commit` を実行する前に、現在のリポジトリの状態を把握するために最も頻繁に使用されるコマンドの1つです。

(出自: `git` パッケージに含まれる)

## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 |
| :--- | :--- |:---|
| ⭐ **`git diff`** | `git status`は「何が変更されたか」をファイルリストで示します。`git diff`は**具体的な変更内容**を行レベルで表示します。 | - |
| **`git log`** | `git log`は過去の「コミット履歴」を表示します。`git status`は現在の「作業状態」を示します。 | - |

## よく連携されるコマンド

`git status`は、Gitの基本的なワークフローの中核をなすコマンドです。

### シナリオ例: コミット前の状態確認 (共通)

* **目的**: コミットする前に、どの変更がコミットに含まれるかを正確に把握する。
* **連携コマンド**: `git add`, `git commit`
* **解説**: ファイルを変更した後、`git add` でコミットしたい変更をステージングします。その後 `git status` を実行すると、「Changes to be committed」（コミットされる変更）と「Changes not staged for commit」（コミットされない変更）が明確に区別して表示されます。
* **コマンド例**:

		```bash
    # 1. ファイルを編集
    # ...

    # 2. 変更をステージング

    git add file1.txt

    # 3. 現在の状態を確認

    git status

    # On branch main
    # Changes to be committed:
    #   (use "git restore --staged <file>..." to unstage)
    #         modified:   file1.txt
    #
    # Changes not staged for commit:
    #   (use "git add <file>..." to update what will be committed)
    #   (use "git restore <file>..." to discard changes in working directory)
    #         modified:   file2.txt
    ```





## オプション説明

| オプション | 説明 |
| :--- | :--- |
| **表示形式** | |
| ⭐ `-s`, `--short` | 出力を短い形式で表示します。`M` (Modified), `A` (Added), `D` (Deleted), `??` (Untracked) などのステータスコードで表示されます。 |
| `-b`, `--branch` | ブランチと追跡情報を短い形式で表示します。 |
| `--long` | (デフォルト) 詳細な形式で表示します。 |
| **その他** | |
| `-u`, `--untracked-files[=<mode>]` | 未追跡ファイルの表示方法を制御します (`no`, `normal`, `all`)。 |

## よく使われるオプション組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: 現在の作業ブランチとリモートブランチの状態を素早く確認する。
* **組み合わせ**: `git status -sb`
* **解説**: `-s` (short) と `-b` (branch) を組み合わせることで、ブランチ名、リモート追跡ブランチとの差（進んでいるか、遅れているか）、変更されたファイルの状態を一行でコンパクトに表示できます。
* **例**:

		```bash

    git status -sb

    # ## main...origin/main [ahead 1]
    #  M README.md
    # ?? new_file.txt
    ```





## 2. ブルーチーム視点

* **タスク**: インシデント調査で、Gitリポジトリ内に不審なファイルが作成されていないかを確認する。
* **組み合わせ**: `git status`
* **解説**: 侵害されたシステム上のGitリポジトリで `git status` を実行し、「Untracked files」（未追跡ファイル）のセクションに、Webシェル (`shell.php`) や攻撃ツールなどの予期せぬファイルが表示されていないかを確認します。
* **例**: -

## 3. レッドチーム視点

* **タスク**: 偵察段階で、ターゲットのリポジトリの現在の作業状況を把握する。
* **組み合わせ**: `git status`
* **解説**: 攻撃者は侵入後、Gitリポジトリ内で `git status` を実行し、開発者がどのようなファイルにどのような変更を加えているかを把握します。これにより、開発中の機能や、修正中の脆弱性などの情報を得て、次の攻撃のヒントとします。
* **例**: -

# エラーメッセージとトラブルシューティング

* 一般的なエラーは [Linux共通のトラブルシューティング](../linux/troubleshooting_common_errors.md) を参照。

1. **エラーメッセージ例 1**: `fatal: Not a git repository`
		* **考えられる原因**: 現在のディレクトリまたはその親ディレクトリに `.git` ディレクトリが存在しません。
		* **解決策**: 正しいGitリポジトリのディレクトリに移動するか、`git init` で新しいリポジトリを作成してください。

# 環境変数と設定ファイル

* `git status` に直接影響する独自の環境変数はありません。
* 一般的な環境変数・設定ファイルは [Linux共通の環境変数・設定ファイル](../linux/environment_and_config.md) を参照。

# セキュリティに関する考慮事項

## 脆弱性と悪用事例

* **脆弱性**: **情報漏洩**。
* **悪用シナリオ**: `git status` の出力には、**未追跡ファイル**が表示されます。もし `.gitignore` の設定が不十分で、認証情報やAPIキーを含むファイル (`.env`, `credentials.json`など) が未追跡ファイルとして表示された場合、その存在が意図せず漏洩する可能性があります。

## GTFOBins / LOLBAS における利用例

`git status` は情報表示に特化したコマンドのため、GTFOBinsにはリストされていません。

## 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: **`.gitignore` を徹底**し、機密情報を含む可能性のあるファイルが決して追跡対象にならないようにする。
* **Detection (検知)**: -

# 注意点・補足

* `git status`は、Gitの操作で迷ったときに**最初に実行すべきコマンド**です。現在の状態と、次に行うべき操作のヒントを常に提供してくれます。

---


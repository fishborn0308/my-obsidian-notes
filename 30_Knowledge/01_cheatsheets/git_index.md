# Git コマンド チートシート (目次)

このチートシートは、Gitの基本的なコマンドと概念、そしてVS Codeとの連携方法をまとめたものです。バージョン管理の理解を深め、効率的な開発ワークフローを構築するために役立ちます。

---

## 1. 基本操作 (ローカルリポジトリ)

* [`git init`](git/git_init.md) - 新しいGitリポジトリを作成
* [`git add`](git/git_add.md) - 変更をステージングエリアに追加
* [`git commit`](git/git_commit.md) - ステージングされた変更をコミット
* [`git status`](git/git_status.md) - 現在のリポジトリの状態を表示
* [`git diff`](git/git_diff.md) - 変更内容を比較
* [`git log`](git/git_log.md) - コミット履歴を表示
* [`git restore`](git/git_restore.md) - ファイルの変更を取り消す(Git 2.23以降推奨)
* [`git reset`](git/git_reset.md) - コミット履歴やステージングエリアをリセット
* [`git rm`](git/git_rm.md) - ファイルを削除し、ステージングエリアからも削除

## 2. ブランチ操作

* [`git branch`](git/git_branch.md) - ブランチの管理
* [`git checkout`](git/git_checkout.md) - ブランチの切り替え、ファイルの復元
* [`git switch`](git/git_switch.md) - ブランチの切り替え(Git 2.23以降推奨)
* [`git merge`](git/git_merge.md) - ブランチのマージ
* [`git rebase`](git/git_rebase.md) - コミット履歴の再構築

## 3. リモートリポジトリ操作

* [`git clone`](git/git_clone.md) - リモートリポジトリをローカルにクローン
* [`git remote`](git/git_remote.md) - リモートリポジトリの管理
* [`git push`](git/git_push.md) - ローカルの変更をリモートに送信
* [`git pull`](git/git_pull.md) - リモートの変更をローカルに取り込む
* [`git fetch`](git/git_fetch.md) - リモートの変更をローカルに取得(マージはしない)

## 4. その他の便利なコマンド

* [`git config`](git/git_config.md) - Gitの設定管理
* [`git stash`](git/git_stash.md) - 作業中の変更を一時的に退避
* [`git reflog`](git/git_reflog.md) - Gitの操作履歴を表示

---

## Git と VS Code の連携

VS CodeにはGit機能が統合されており、ほとんどのGit操作をGUIで行うことができます。

* **ソース管理ビュー**: サイドバーの「ソース管理」アイコン(三本線の分岐マーク)から、変更のステージング、コミット、ブランチ切り替え、プッシュ/プルなどを視覚的に操作できます。
* **差分の表示**: ファイルの変更がリアルタイムで表示され、過去のコミットとの差分も簡単に確認できます。
* **GitGraph / GitLens 拡張機能**: `GitGraph`や`GitLens`といった強力な拡張機能を導入することで、コミット履歴の可視化、行ごとのコミット情報表示などが可能になり、Git操作の理解と効率が飛躍的に向上します。

詳細は[`VS Code での Git 運用`](git/vscode_git_workflow.md) ドキュメントを参照してください。

---

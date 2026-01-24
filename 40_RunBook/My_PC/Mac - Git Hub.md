---
created: 2026-01-10 20:07
modified: 2026-01-24 11:43
tags:
  - Git-Hub
  - config
---
# Mac - Git Hub

## GitHubでトークンを発行する

1. ブラウザで [GitHubのSettings > Tokens](https://github.com/settings/tokens) (Classic) を開きます。

	- 場所：右上のアイコン ＞ **Settings** ＞ 左メニュー一番下の **Developer settings** ＞ **Personal access tokens** ＞ **Tokens (classic)**
				
2. **「Generate new token」** ＞ **「Generate new token (classic)」** をクリック。
3. 設定を入力します：

	- **Note**: `Obsidian-Mac` など（自分がわかる名前）。
	- **Expiration**: `No expiration`（無期限）または長めの期間を推奨。
	- **Select scopes**: **「repo」** にチェックを入れます（これでリポジトリの操作権限が付与されます）。
				
4. 一番下の **[Generate token]** をクリック。
5. **重要：表示された `ghp_` から始まる文字列をコピーして、メモ帳などに控えてください。**（一度画面を閉じると二度と表示されません）

## Macでのインストール

**Gitのインストール確認**:

```
git --version


git config --global user.name "あなたの名前"
git config --global user.email "あなたのメールアドレス"
```

ローカルリポジトリの作成

```
cd /Users/username/Documents/MyNotes  # 例：ローカルリポジトリの場所へ移動する
git init
```

リモートの紐付け

```
git remote add origin https://github.com/あなたのユーザー名/リポジトリ名.git
```

最初のコミットとプッシュ

```
# 全ファイルをステージング（追加）
git add .

# コミット（保存）
git commit -m "First commit"

# メインブランチ名を指定してアップロード
git branch -M main
git push -u origin main
```

補足：今後のパスワード入力を省略したい場合

もし毎回パスワード（トークン）を聞かれる場合は、ターミナルで以下のコマンドを一度だけ実行してください。Macのキーチェーンと連携して自動入力されるようになります。

```
git config --global credential.helper osxkeychain
```

## .gitignore

### 1. `.gitignore` の作成手順

1. Obsidianの保管庫（Vault）フォルダを**右クリック ＞ クイックアクション ＞ フォルダに新規ターミナル** を選択します。
2. ターミナルで以下のコマンドを入力して、空のファイルを作成します。

		Bash
		
		```

		touch .gitignore

		```
		
3. そのまま、ターミナルからエディタで中身を書き込みます（VS Codeが入っているので `code` コマンドが便利です）。

		Bash
		
		```

		code .gitignore

		```
		
		※VS Codeが立ち上がらない場合は `nano .gitignore` で編集するか、Finderで隠しファイルを表示（`Command + Shift + .`）してテキストエディタで開いてください。
		

---

### 2. コピペ用：Obsidian推奨の設定内容

MacとWindowsの2台運用において、**競合（コンフリクト）を避けるために必須の設定**です。以下をコピーして貼り付けて保存してください。

Plaintext

```bash
# --- Obsidian 内部設定 (デバイスごとに異なるため同期しない) ---
.obsidian/workspace.json
.obsidian/workspace-mobile.json
.obsidian/graph.json
.obsidian/cache/
.obsidian/plugins/obsidian-git/data.json

# --- OS固有のゴミファイル ---
.DS_Store
*.swp
Thumbs.db
desktop.ini

# --- セキュリティ・バックアップ ---
# もし秘密情報を含む環境変数などがあれば
.env
```

#### なぜこれが必要なのか（エンジニア的視点）

- **`workspace.json`**: 「どのノートを開いているか」「サイドバーの幅」などの情報です。Macで開いている状態をWindowsに同期してしまうと、Windowsで開いた時にレイアウトが崩れたり、同期の衝突が頻繁に発生します。
- **`.DS_Store`**: Macがフォルダの表示設定を保存する隠しファイルです。Windows側では不要な「ゴミ」として扱われ、Gitの履歴を汚す原因になります。

### 3. コピペ用：VSCode推奨の設定内容

```bash
# --- VS Code 関連 ---
.vscode/*
!.vscode/settings.json
!.vscode/tasks.json
!.vscode/launch.json
!.vscode/extensions.json
*.code-workspace

# --- OS 固有のファイル ---
# Windows
Thumbs.db
desktop.ini
$RECYCLE.BIN/

# macOS
.DS_Store
.AppleDouble
.LSOverride
.Spotlight-V100
.Trashes

# --- 開発・スクリプト関連 (10_Script / 20_Program用) ---
# Python
__pycache__/
*.py[cod]
*$py.class
.venv/
venv/
ENV/

# Node.js
node_modules/
npm-debug.log*

# ログファイル
*.log
*.bak
*.tmp

# --- セキュリティ・インフラ関連 (OSCP/ペンテスト意識) ---
# 秘密情報 (絶対にコミットしない)
.env
.env.local
*.pem
*.key
auth.json

# ツール固有のテンポラリ
.terraform/
.vagrant/
```

---

#### 設定のポイント解説

##### 1. `.vscode/` フォルダの扱い

.vscode/* で一度すべて無視したあと、!（否定）を使って、「共有したい設定（settings.jsonなど）」だけをあえて管理対象に戻しています。

これにより、MacとWindowsでVS Codeのフォントサイズや拡張機能の設定を同期しつつ、作業状態（どのファイルを開いているか等）は同期しないようにできます。

##### 2. 言語・環境別の設定

- **Python / Node.js**: `10_Script` や `20_Program` で使用するライブラリ（`node_modules` や `venv`）は、Gitで管理するには重すぎ、かつ各OSで再インストールすべきものなので除外しています。
- **秘密情報の保護**: ペンテスト学習中、APIキーやパスワードを `.env` に書くことがあるかもしれません。これを誤ってGitHub（Publicリポジトリなら特に）へPushしないよう、厳重に除外しています。

##### 3. Brewfile (40_Brewfile) の同期

Mac miniで使用している `Brewfile` は、そのままコミットしてOKです。これにより、万が一Macを再セットアップする際も、`brew bundle` コマンド一発で環境が復元できます。

### 4. すでにファイルをコミット（保存）してしまっている場合

もし、すでに `workspace.json` などがGitHubに上がってしまっている場合は、`.gitignore` を作っただけでは消えません。その場合は以下のコマンドをターミナルで実行してください。

```bash
# キャッシュされているファイルを一旦すべて削除対象にする（実ファイルは消えません）
git rm -r --cached .

# 改めて .gitignore を反映して追加
git add .

# コミット
git commit -m "chore: apply updated .gitignore"
git push origin main
```
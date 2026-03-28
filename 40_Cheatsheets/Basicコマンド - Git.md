# 基本コマンド

## Git 基本コマンド・チートシート

### 1. 変更を記録する（基本ワークフロー）
作業の区切りで必ず行うセットです。

| コマンド | 内容 | 備考 |
| :--- | :--- | :--- |
| `git status` | 現在の状態を確認 | どのファイルが変更されたか一目でわかる |
| `git add <file>` | 特定のファイルをステージング | `git add .` で全変更を一括ステージング |
| `git commit -m "msg"` | メッセージを付けて記録 | 後で見返して「何をしたか」わかるように |
| `git push` | リモート(GitHub等)へ送信 | Mac側でこれを忘れると他で同期できない |

### 2. 他の環境の変更を取り込む
Syncthingでファイルは同期されますが、Gitの履歴を最新にするために必要です。

| コマンド | 内容 | 備考 |
| :--- | :--- | :--- |
| `git fetch` | リモートの最新情報を取得 | ローカルのファイルは書き換わらない |
| `git pull` | 最新をダウンロードして結合 | `fetch` + `merge` を一度に行う |
| `git diff` | 変更箇所の差分を表示 | どこを書き換えたか詳細に確認 |

### 3. 困った時のレスキュー
「間違えて消した」「変な風に書き換わった」時に使います。

| コマンド | 内容 | 備考 |
| :--- | :--- | :--- |
| `git checkout -- <file>` | 特定のファイルを元に戻す | 編集を破棄して最後のコミット状態へ |
| `git reset --soft HEAD^` | 直前のコミットを取り消す | 編集内容は残したまま、コミットだけ消す |
| `git log --oneline` | 履歴を一行ずつ表示 | 過去の変更履歴をサクッと確認 |

---

## あなたの構成（Mac管理 + Syncthing）での注意点

あなたの環境では、**「Gitのインデックス（履歴）」と「実ファイル（Syncthing同期）」のズレ**に注意が必要です。

* **Macでコミットしたら、必ずPushする:**
    WindowsやKali側で `git status` を打った際、ファイルの中身はSyncthingで新しくなっていても、Git側が「古い状態から変更された」と誤認するのを防ぐためです。
* **`.gitignore` を活用する:**
    OS固有のゴミファイル（Macの `.DS_Store` や Windowsの `Thumbs.db`）がGitに混じると、Kali側でノイズになります。
    ```bash
    # 推奨される .gitignore の中身
    .DS_Store
    desktop.ini
    .sync/
    *.sync-conflict-*
    ```
* **コンフリクト（競合）への対処:**
    Syncthingが生成する `*.sync-conflict-*` ファイルを見つけたら、VS Codeの「比較機能」を使ってどちらを残すか決め、不要な方を削除してから `git add` してください。

---

## Git：ローカル環境での構成要領

### 1. ユーザー名とメールアドレスの設定
コミット履歴に記録される名前と連絡先を登録

```bash
git config --global user.name "Your Name"
git config --global user.email "your_email@example.com"
```

### 2. デフォルトブランチ名を `main` にする
昔は `master` が主流でしたが、現在は `main` が標準です。これを設定しておくと、新しくリポジトリを作った時に自動的に `main` になります。

```bash
git config --global init.defaultBranch main
```

### 3. エディタの設定 (VS Code派なら)
Gitのメッセージ入力などで立ち上がるエディタをVS Codeに固定しておくと便利です。

```bash
git config --global core.editor "code --wait"
```

---

### 4. 設定内容の確認
正しく反映されたか確認します。

```bash
git config --list
```

---

### 5. 実際にリポジトリを初期化する手順
設定が終わったら、プロジェクトを始めたいディレクトリに移動して以下のコマンドを叩きます。

1. **ディレクトリ作成と移動**
   ```bash
   mkdir my-project && cd my-project
   ```
2. **Gitの初期化**
   ```bash
   git init
   ```
   > これで `.git` という隠しフォルダが作られ、管理が始まります。
3. **最初のコミットを試す**
   ```bash
   touch README.md
   git add README.md
   git commit -m "first commit"
   ```

---

### 💡 KaliユーザーへのTips: `.gitignore` の準備
Kaliでツールを動かしたりスクリプトを書いたりすると、ログファイルや一時ファイルが大量に生成されます。それらをGit管理から外すために、`.gitignore` ファイルをプロジェクト直下に作っておくのが「デキる開発者」の第一歩です。

* **GitHubからテンプレを拾うのが一番楽です:** [gitignore.io](https://www.toptal.com/developers/gitignore) などのサイトで「Linux」「Python」「Visual Studio Code」などを選ぶと、最適な中身を生成してくれます。

## Git：リモートリポジトリとの連携

### 1. 安全な接続のための「SSH鍵」を作る
パスワードを毎回打つのは面倒ですし、セキュリティ的にも推奨されません。公開鍵認証（SSH）を使いましょう。

1.  **鍵ペアを生成する**
    ```bash
    ssh-keygen -t ed25519 -C "your_email@example.com"
    ```
    ※保存場所はそのままEnter、パスフレーズはお好みで（空でもOK）。
2.  **公開鍵の中身をコピーする**
    ```bash
    cat ~/.ssh/id_ed25519.pub
    ```
    表示された `ssh-ed25519 AAA...` で始まる長い文字列をすべてコピーします。
3.  **GitHubに登録する**
    * GitHubの [Settings] > [SSH and GPG keys] > [New SSH key] を開く。
    * Titleに「Kali-Laptop」など名前をつけ、Keyに先ほどの文字列を貼り付けて保存。

---

### 2. ローカルリポジトリをリモートに繋ぐ
GitHub側で新しいリポジトリ（例: `my-project`）を「空の状態で」作成したら、手元のターミナルで以下を叩きます。

```bash
# 1. リモートの住所（URL）を登録する
git remote add origin git@github.com:ユーザー名/my-project.git

# 2. ローカルの変更をリモートに送り出す（初回のみ -u をつける）
git push -u origin main
```

---

### 3. VS Codeでの「Git操作」をもっと快適に
せっかくVS Codeを使っているので、ターミナルだけでなくGUIも活用しましょう。

* **ソース管理アイコン (Ctrl + Shift + G):** 変更したファイルが一覧表示されます。ファイル名の横の `+` を押すと `git add`、上のチェックマーク `✓` を押すと `git commit` が実行されます。
* **拡張機能「GitGraph」:**
  ブランチの枝分かれが視覚的に見えるようになり、Kaliでの開発が楽しくなります。
* **拡張機能「GitHub Pull Requests and Issues」:**
  これを入れると、ブラウザを開かなくてもVS Code上でプルリクエストの確認などが完結します。

---

### 💡 連携がうまくいかない時のチェックリスト
もし `Permission denied (publickey)` と出たら、以下を試してください。

1.  **SSH接続のテスト:** `ssh -T git@github.com` を実行。「Hi (ユーザー名)!」と返ってくれば成功です。
2.  **リモートURLの確認:** `git remote -v` を打ち、URLが `https://` ではなく `git@github.com:` で始まっているか確認してください。


## Git：エクスプロイトの管理

GitHubからツールを落とし、自分の環境に合わせて調整するための基本コマンドです。

| 操作 | コマンド | 備考 |
| --- | --- | --- |
| **ツールの取得** | `git clone <URL>` | ツールをローカルにコピー。 |
| **最新状態に更新** | `git pull` | ツールがアップデートされたら実行。 |
| **変更の破棄** | `git checkout .` | コードをいじりすぎて壊した時に元に戻す。 |
| **特定行の検索** | `grep -r "pattern" .` | 落としてきたコード内から特定の関数などを探す。 |

* **ailias**
	* **GitClone:** `gclone` `cd ~/tools/git && git clone`
	* **GitToolsのアップデート:** `gup` `find ~/Tools/Git -maxdepth 2 -name .git -type d -execdir git pull --rebase \;`

> [!IMPORTANT]
> **OSCPの掟:** 公開されているエクスプロイト（特に行数の多いPythonスクリプト）は、実行前に必ず中身を読み、自分のKaliのIPやポートに書き換える箇所がないか確認してください。

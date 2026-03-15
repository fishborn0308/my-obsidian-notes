# Windows版 Git環境構築手順書

WindowsでGitを使用するには、**Git for Windows (Git Bash)** をインストールするのが最も一般的で確実な方法です。

### 1. インストーラーのダウンロード

まずは公式サイトからインストーラーを取得します。

1. [Git for Windows 公式サイト](https://gitforwindows.org/) にアクセスします。
2. **「Download」** ボタンをクリックして、`.exe` ファイルをダウンロードします。

### 2. インストール手順

ダウンロードしたファイルを実行し、セットアップを開始します。基本的には「Next」の連打で問題ありませんが、以下のポイントだけ確認してください。

* **Select Components**: デフォルトのままでOKです（「Git Bash Here」にチェックが入っていると便利です）。
* **Choosing the default editor**: Gitで使用するエディタを選びます。こだわりがなければ「Vim」か、使い慣れた「VS Code」などを選択してください。
* **Adjusting the name of the initial branch**: 新規リポジトリのメインブランチ名を `main` にしたい場合は、下の「Override the default branch name for new repositories」を選択し、`main` と入力します。
* **Adjusting your PATH environment**: 「Git from the command line and also from 3rd-party software」を選択（推奨）。これでコマンドプロンプトやPowerShellからもGitが使えます。

その他の設定はすべてデフォルト（一番上の選択肢）で「Next」を押し続け、最後に **「Install」** をクリックします。

---

### 3. インストールの確認

インストールが完了したら、正しく認識されているか確認します。

1. キーボードの `Win + R` を押し、「cmd」と入力してコマンドプロンプト（またはPowerShell）を開きます。
2. 以下のコマンドを入力して実行します。
```bash
git --version

```


3. `git version 2.x.x.windows.1` のようにバージョンが表示されれば成功です。

---

### 4. 初期設定（ユーザー情報の登録）

Gitを利用する際、誰が変更を行ったかを記録するために「ユーザー名」と「メールアドレス」の設定が必須です。

コマンドプロンプトまたは Git Bash で以下のコマンドを実行してください。

```bash
# ユーザー名の設定（半角英数字、姓名の間はスペース）
git config --global user.name "Your Name"

# メールアドレスの設定
git config --global user.email "your-email@example.com"

```

> [!TIP]
> **設定の確認方法**
> `git config --list` を実行すると、登録された情報を確認できます。

---

### 5. 便利な追加設定（任意）

日本語のファイル名が文字化けするのを防ぐ設定です。

```bash
git config --global core.quotepath false

```

---
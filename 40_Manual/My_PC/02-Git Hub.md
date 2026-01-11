
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
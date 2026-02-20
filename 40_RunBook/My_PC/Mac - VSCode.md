---
created: 2026-01-04 08:35
modified: 2026-02-20 16:15
tags:
  - VCCode
  - config
---

# Mac - VSCode

## VSCode

### ディレクトリ構成

- 00_inbox
- 10_Script
- 20_Program
- 30_Jsons
- 40_Brewfile
- 50_Configs

### VS Codeの設定(`settings.json`)

`settings.json`はVS Codeの動作を細かく制御するためのファイルです。VS Codeを開き、`Cmd + Shift + P`でコマンドパレットを開き、「Preferences: Open User Settings (JSON)」と入力して開くことができます。

```json
// --- 基本・外観 ---

"editor.fontSize": 14,

"editor.lineHeight": 2,

"editor.fontFamily": "'Source Code Pro', Consolas, monospace",

"workbench.iconTheme": "vs-seti",

"editor.minimap.enabled": true,

"editor.guides.bracketPairs": true,

// --- 整形・インデント (Global) ---

"editor.tabSize": 2,

"editor.insertSpaces": true,

"editor.formatOnSave": true,

"editor.defaultFormatter": "esbenp.prettier-vscode",

"files.trimTrailingWhitespace": true,

"files.insertFinalNewline": true,

  

// --- 言語別設定 (Global設定を上書き) ---

"[python]": {

"editor.tabSize": 4,

"editor.defaultFormatter": "ms-python.python"

},

"[markdown]": {

"editor.defaultFormatter": "DavidAnson.vscode-markdownlint", // MDはlint優先

"editor.wordWrap": "on"

},

  

// --- ファイル除外設定 (バックアップ対象外の定義) ---

"files.exclude": {

"**/.git": true,

"**/.DS_Store": true,

"**/node_modules": true

},

  

// --- 拡張機能設定 ---

"pasteImage.path": "${currentFileDir}/images/${currentFileBasenameNoExtension}/",

"markdownlint.config": {

"MD013": false, // 行長制限オフ

"MD033": { "allowed_elements": ["br"] }

}
}
```

```json
{

"language": "en,ja",

"ignoreRegExpList": [

"/^0x[a-fA-F0-9]+$/", // 16進数を無視

"/[a-fA-F0-9]{32,}/", // MD5やSHAなどのハッシュ値を無視

"/^192\\.168\\..*/" // 特定のIPアドレス体系を無視

],

"dictionaries": [

"softwareTerms", // 必須

"node",

"python",

"bash",

"markdown"

]

}
```

### VS Codeの拡張機能

### 【必須：執筆・ドキュメント】

1. **Markdown All in One:** (目次生成、補完、プレビューすべてをこれ一本に)
2. **markdownlint:** (文法チェック)
3. **Paste Image:** (画像の貼り付け)
4. **Draw.io Integration:** (図解作成)
5. **Code Spell Checker:** (専門用語は「辞書追加」で育てていく)

### 【必須：開発・Git】

6. **GitHub Copilot:** (AI支援)
7. **GitLens:** (行ごとの履歴確認)
8. **Git Graph:** (全体のコミットグラフ視覚化)
9. **Prettier:** (コード・MDの自動整形)
10. **Error Lens:** (エラーのインライン表示)

### 【便利・外観】

11. **Material Icon Theme:** (アイコン視認性)
12. **indent-rainbow:** (インデント可視化)
13. **Path Intellisense:** (パス補完)
14. **YAML:** (Red Hat公式)
15. **TODO Highlight:** (TODOの埋もれ防止)

---

### Mac miniでの環境構築のヒント

* **Homebrewの導入:**
	* Mac用のパッケージマネージャであるHomebrewを導入しておくと、今後必要なツール(Git自体もそうですが、各種セキュリティツールなど)のインストールが非常に楽になります。
	* ターミナルで以下のコマンドを実行してインストールできます。

			```bash

	    /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

	    ```

* **Gitのインストール:**
	* MacにはデフォルトでGitがインストールされていることが多いですが、最新版をHomebrewでインストールしておくことをオススメします。

			```bash

	    brew install git

	    ```


## Obsidian

### ディレクトリ構成

- 00_inbox: メモ等、軽易なもの
- 10_Daily: デイリーノート
- 20_Projects: 検証、CTF等のwrite upなど
- 30_Knowledge: 学習などの成果
- 40_RunBook: プロジェクトから永続的に使用するドキュメントを作成
	- My PC: デスクで使用しているPCの設定や構成
	- Tamplate: テンプレート
- 90_Archive: 過去のプロジェクトやまとめ終わったRunBookなど
- 99_Assets: スクリーンショットなど

## Obsidianの拡張機能

- Excalidraw
- Dataview
- Templater
- Advanced Tables
- Git
- Tag Wrangler
- Linter
- Advanced URI

### 活用法

#### 1. 知識の「自動インデックス」と「タグ管理」

（**Dataview** × **Tag Wrangler**）

「どこに書いたか」を覚える必要をなくすための設定です。

- DataviewでのMOC（目次）作成:

		30_Knowledge/01_cheatsheets の各フォルダに、そのカテゴリのファイルを一覧表示するクエリを埋め込みます。
		
- Tag Wranglerでのリファクタリング:

		プロジェクト識別子（[SEC]など）を後から整理したくなった際、タグパネルから一括でタグ名の変更（Rename）ができるようになります。これはOSCP学習が進み、タグが細分化したときに非常に重宝します。
		

---

#### 2. 検証の「標準化」と「クリーンアップ」

（**Templater** × **Linter**）

OSCPの試験や実務のレポート作成において、フォーマットの美しさと一貫性は信頼性に直結します。

- Templaterで動的なメタデータ挿入:

		新規ノート作成時に、自動的にOS環境（Mac/Win）やプロジェクトカテゴリをプロンプトで選択させ、YAMLフロントマター（ノート冒頭の設定項目）を生成します。
		
- Linterでの自動整形:

		保存時に「末尾の空白を削除」「ハッシュタグをYAMLへ移動」「見出しのレベル修正」などを自動で行います。これにより、VS Codeで開いた際やGitHub上で閲覧した際も、常に美しいMarkdown状態が維持されます。
		

---

#### 3. 視覚的な「攻撃経路」の構築

（**Excalidraw** × **Advanced Tables**）

- Excalidrawでのインフラ図解:

		提示された「詳細配線マップ」を、Excalidrawを使って図解化しましょう。Obsidian内のノート（例：Minisforum）をキャンバスにドラッグ＆ドロップすると、図の中にノートへのリンクを埋め込めるため、「図をクリックすると詳細マニュアルが開く」というインタラクティブな地図が作れます。
		
- Advanced Tablesでのインベントリ管理:

		ハードウェア一覧のような大きな表も、Tabキーでサクサク入力でき、列の並び替えも瞬時に行えます。
		

---

#### 4. 外部ツールとの「連携」と「同期」

（**Git** × **Advanced URI**）

- Gitの競合回避:

		Linterと併用することで、MacとWindowsで発生しがちな「改行コードの違い」などによる不要な差分を抑え、クリーンな同期を維持できます。
		
- Advanced URIによる司令塔（Raycast）連携:

		Raycastから特定のノートを「特定の行（例：最新のログ行）」で開いたり、外部スクリプトからObsidianにデータを流し込んだりするための「API窓口」として機能します。

## 設定

### 1. 執筆と構造化の効率化 (Visualization & Editing)

| **プラグイン**           | **概要 (エンジニア的メリット)**                  | **推奨設定のポイント**                                                              |
| ------------------- | ------------------------------------ | -------------------------------------------------------------------------- |
| **Excalidraw**      | 手書き風の図解作成。ネットワーク構成図や攻撃経路の視覚化に。       | **Folder**: `99_Assets/Excalidraw` に設定。<br><br>  <br><br>**Autosave**: ON。 |
| **Advanced Tables** | Markdownテーブルの編集。アセット管理表をExcelのように操作。 | **Auto-format**: ON。`Tab`キーでのセル移動が快適になります。                                 |
| **Linter**          | 表記揺れの自動修正。GitHub上での可視性を高める。          | **Lint on save**: ON。改行ルールやYAMLの整理を自動化。                                    |

---

### 2. 知識の自動収集と管理 (Data & Automation)

#### **Dataview**

- **概要**: ノートを「データベース」化します。`[SEC]` や `[NET]` といったタグが付いたノートを自動で一覧化します。
- **設定**:

		- `Enable JavaScript Queries`: **ON**（高度な集計に必要）。
				
		- `Enable Inline Queries`: **ON**（文章の中に「現在のプロジェクト数：10」のように埋め込めます）。
				
- **活用例**: `30_Knowledge` のインデックスを自動生成。

#### **Templater**

- **概要**: テンプレートに「変数（今日の日報、ファイル名など）」を組み込めます。
- **設定**:

		- `Template folder location`: `40_Manual/Templates` を指定。
				
		- `Trigger Templater on new file creation`: **ON**。新規作成時に自動適用。
				
- **活用例**: 「ペンテスト検証ログ」をワンボタンで作成。

#### **Tag Wrangler**

- **概要**: タグの整理（一括リネーム）。
- **設定**: 設定画面はなく、タグパネルを右クリックして操作します。
- **活用例**: 増えすぎた `#nmap` タグを `#tool/nmap` に一括整理。

---

### 3. インフラ同期と外部連携 (Infrastructure & Integration)

#### **Git**

- **概要**: バージョン管理と複数デバイス同期の心臓部。
- **設定**:

		- **Vault backup interval**: `30` (分)。
				
		- **Auto pull on startup**: **ON**（Mac/Winの同期ズレを防止）。
				
		- **Author name / Email**: Gitの初期設定と合わせる。
				

#### **Advanced URI**

- **概要**: Obsidianの外部（Raycastやスクリプト）から、Obsidian内の特定のノートを操作するための窓口。
- **設定**:

		- `Allow execute commands`: **ON**。
				
- **活用例**: Raycastのショートカットで、特定のプロジェクトノートを即座に開く。

---

### 具体的な設定フロー：まずはここから

エンジニアのワークフローとして、以下の順番でセットアップを進めるのが最も効率的です。

#### Step 1: 資産管理のクリーンアップ (`99_Assets`)

Obsidianの設定 `Files & Links` から、**「Default location for new attachments」** を `In the folder specified below` にし、`99_Assets` を指定してください。これでスクリーンショットが散らかりません。

#### Step 2: Linter で「自分ルール」を定義

Linterの設定で、**「Heading blank lines（見出しの上下に空行）」** と **「Trailing spaces（行末の空白削除）」** をONにします。これにより、GitHubで見たときのMarkdown崩れがなくなります。

#### Step 3: Git の `.gitignore` を再確認

Mac miniとMinisforum（Windows）間で同期する際、以下のファイルをリポジトリのルートにある `.gitignore` に追加してください。

Plaintext

```
# デバイスごとのウィンドウ配置の衝突を防ぐ
.obsidian/workspace.json
.obsidian/workspace-mobile.json
.obsidian/cache/
```

---

### 運用のコツ：Tag Wrangler の活用

fishborn0308さんのディレクトリ構成には `00_inbox` がありますね。

1. とりあえず `00_inbox` に `#temp` タグをつけてメモを投げる。
2. 週末に **Tag Wrangler** を使い、タグパネルで `#temp` を `#SEC/HTB` などにリネーム、または適切なフォルダへ移動。

このフローにより、**「書くときは摩擦ゼロ、整理は一括」** という理想的な運用が可能になります。

次は、Templaterを使って「ペンテスト検証ログ」や「日報」の具体的なコードを作成してみましょうか？

ご希望の項目（例：IPアドレスやスキャン結果の枠が含まれるノート）を教えていただければ、すぐにテンプレートコードを提示します。

---

### 運用のアドバイス：設定の優先順位

まずは、以下の順番で設定を固めるのがスムーズです。

1. **Linterの設定**: GitHubでの見栄えを考慮し、改行やスペースのルールを固定する。
2. **Templaterの雛形作成**: `00_inbox` 用と `20_Projects` 用の2つのテンプレートを作る。
3. **Gitの自動バックアップ設定**: 15分〜30分間隔で自動コミットされるようにする。
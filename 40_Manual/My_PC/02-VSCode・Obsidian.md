
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
- 10_RunBook: CTF等のwrite upなど
- 20_Projects: 検証など
- 30_Knowledge: 学習などの成果
	- 00_app: アプリ関連のマニュアル
	- 01_cheatsheats: コマンドや構文を参照する辞書
		- common_practices: OSに関わらず共通的なこと
		- docker: Docker関連コマンド
		- git: Git関連コマンド
		- Pentest: ペンテスト関連チートシート
			- app: Pentestツールに関するチートシート
		- Linux: Linux関連コマンド
		- Windows: Windows関連コマンド
			- cmd: コマンドプロンプト関連コマンド
			- powershell：パワーシェル関連コマンド
- 40_Manual: マニュアル関連
- 90_Archive: 過去のプロジェクトやまとめ終わったRunBookなど
- 99_Assets: スクリーンショットなど

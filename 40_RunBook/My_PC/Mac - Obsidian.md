---
created: 2026-01-24 11:44
modified: 2026-02-20 16:15
tags:
  - Obsidian
  - config
---

# Mac - Obsidian

# Mac - Obsidian 管理構成案（最新版）

## 1. ディレクトリ構成

- **00_inbox**: メモ、一時保存、**Thino**の投稿先
- **10_Daily**: デイリーノート
- **20_Projects**: 検証、CTF等のwrite upなど（アクティブな作業）
- **30_Knowledge**: 学習成果、体系化された知識
- **40_RunBook**: プロジェクトから永続的に使用するドキュメント

		- **My PC**: デスクで使用しているPCの設定や構成
				
		- **Template**: テンプレート（**Templater**用）
				
- **90_Archive**: 過去のプロジェクト、完了したRunBook
- **99_Assets**: スクリーンショット、**Excalidraw**データ等

### Obsidianの拡張機能

- Excalidraw
- Dataview
- Templater
- Advanced Tables
- Git
- Tag Wrangler
- Linter
- Advanced URI
- Copilot
- Excalidraw
- File Color
- Iconize
- Hover Editor
- Outliner
- Thino

---

## 2. 拡張機能と活用法

### A. 知識の「自動インデックス」と「AI連携」

（**Dataview** × **Tag Wrangler** × **Copilot**）

- **Dataview**: `30_Knowledge` の各カテゴリに、ファイルを自動一覧表示するMOC（目次）を生成。
- **Tag Wrangler**: 増えすぎた `#nmap` などのタグを `#tool/nmap` へ一括リネームして整理。
- **Copilot**: 自分の `30_Knowledge` をインデックスさせ、「過去の自分の検証結果」に基づいた技術回答を生成。

### B. 検証の「標準化」と「構造化」

（**Templater** × **Linter** × **Outliner**）

- **Templater**: 新規作成時にYAMLフロントマターを自動生成（OS環境やカテゴリを選択）。
- **Linter**: 保存時に「末尾の空白削除」「見出しの整形」を自動化し、GitHub上での可視性を維持。
- **Outliner**: 階層構造の箇条書きをスムーズに操作。攻撃シナリオやインフラ構成のステップ管理を強化。

### C. 視覚的・直感的な「情報管理」

（**Excalidraw** × **Hover Editor** × **Iconize** × **File Color**）

- **Excalidraw**: インフラ構成図や攻撃経路を視覚化。図の中にノートリンクを埋め込み可能。
- **Hover Editor**: リンクをホバーするだけで別ノートを「浮いたウィンドウ」で開き、今の作業を中断せずに追記。
- **Iconize / File Color**: フォルダごとにアイコンと色を設定。`20_Projects`（進行中）を青にするなど、視認性を最大化。

### D. クイックキャプチャと外部連携

（**Thino** × **Git** × **Advanced URI**）

- **Thino**: Twitter風UIで、検証中のコマンド実行結果や思いつきを `00_inbox` へ時系列で即座に記録。
- **Git**: 15〜30分間隔で自動コミット。Mac/Windows間の同期競合を防止。
- **Advanced URI**: Raycastから特定のノートを直接開くためのAPI窓口。

---

## 3. 具体的な設定値（エンジニア向け）

|**プラグイン**|**推奨設定のポイント**|
|---|---|
|**Thino**|`Folder to store memo`: `00_inbox` に設定。|
|**Hover Editor**|`Trigger Key`: `Cmd/Ctrl` に設定。誤爆を防ぎつつ必要な時だけ開く。|
|**Copilot**|`QA Mode`: ON。`Index directory`: `30_Knowledge` を指定。|
|**Linter**|`Lint on save`: ON。`Heading blank lines`: ON。|
|**Git**|`Auto pull on startup`: ON。`Vault backup interval`: 30。|

---

## 4. 運用のコツ：エンジニア・ワークフロー

1. **キャプチャ**: 検証中の生ログやメモは `Thino` で `00_inbox` に流し込む。
2. **構造化**: `Outliner` で手順を整理し、`Excalidraw` で構成図を描く。
3. **参照**: 図解やリンクを `Hover Editor` でチラ見しながら、メインのレポートを執筆。
4. **リファクタリング**: 週末に `Tag Wrangler` でタグを整理し、`Linter` で整形された綺麗なMarkdownを `Git` で管理する。

---

### 追加のアドバイス：`.gitignore` の更新

複数の拡張機能を追加したため、設定の衝突を防ぐために `.gitignore` （リポジトリ直下）に以下が含まれているか確認してください。

Plaintext

```
.obsidian/workspace.json
.obsidian/workspace-mobile.json
.obsidian/cache/
.obsidian/plugins/obsidian-iconize/data.json  # デバイス間でアイコン表示が崩れる場合
```

## 5. 拡張機能 詳細設定ガイド

## 1. 思考の流速を上げる (Input & Capture)

### **Thino**

検証中の「コマンド結果」や「一言メモ」を時系列で残すための設定です。

- **Advance - Use which plugin's default configuration**: `Daily`
- **ショートカット割り当て**: hotkeyから`Cmd + Shift + M` で即座にThinoの入力欄が開くように設定
- **Default Editor Mode:** `Live-Preview`

### **Outliner**

箇条書きを「タスク」や「手順書」として扱いやすくします。

- **Improve the style of your lists**: ON（見た目が整います）。
- **Stick the cursor to the content**: `Stick cursor out of bullets and checkboxes`（インデント操作時にカーソルがズレなくなります）。

---

## 2. 視覚的・直感的に管理する (UI & Navigation)

### **Iconize**

- **Add  predefined icon pack**: `Lucide` を推奨（シンプルでエンジニア向け）。
- **Add icon rule**

| **フォルダ名**      | **推奨アイコン (Lucide)** | **理由**        |
| -------------- | ------------------- | ------------- |
| `00_inbox`     | `Inbox`             | 未整理の受信箱として    |
| `10_Daily`     | `Calendar`          | 日報・記録用        |
| `20_Projects`  | `TerminalSquare`    | 攻略対象やアクティブな検証 |
| `30_Knowledge` | `Brain`             | 体系化された知識庫     |
| `40_RunBook`   | `BookText`          | インフラ操作の手順書    |
| `90_Archive`   | `Archive`           | アーカイブ         |
| `99_Assets`    | `Images`            | 画像など          |

### **File Color**

- **Palette**: 落ち着いた色を選択し、左サイドバーのフォルダを右クリックして `Set Color`。
- **運用**:

		- `20_Projects` → **Blue**（アクティブな作業）
		- `00_inbox` → **Red**（未処理のタスクがある場所）
		- `Templates` → **Purple**
		- `90_Archive` → **Black**
	- **Color Background:** `ON`

### **Hover Editor**

このプラグインは設定次第で「化け」ます。

- **Obsidian標準設定 > コアプラグイン > ページプレビュー:** `プレビューをON`
- **Default Mode**: `Reading View`。
- **Auto Focus**: ON（ホバーした瞬間に編集可能にする）。
- **Initial popover width × height:** 800×720
- **活用シーン**: ネットワーク図（Excalidraw）上の各ノードをクリックせず、`Cmd + ホバー` でその機器の設定メモを浮かせたまま編集。

---

## 3. インテリジェンスと自動化 (AI & Data)

### **Copilot**

自分の過去のメモをAIに読み込ませます。

- **Default Model**: (使用可能な最新モデルを選択)。
- **Index Vault**: `30_Knowledge` と `20_Projects` を指定。
- **QA Mode Settings**: `Enable Local Indexing` をON。
- **運用**: 「以前のCTFでSQLインジェクションに使ったペイロードは何？」と聞けば、自分の過去のWriteupから回答してくれます。

### **Excalidraw**

- **Default folder**: `99_Assets/Excalidraw`。
- **Auto-export SVG**: ON（Obsidian以外の場所でも画像としてプレビューしやすくなります）。
- **Default font typeface to use for embedded markdown file**: `Cascadia Code`

---

## 4. 既存プラグインの再調整 (Core Maintenance)

### **Git**

- **Auto commit-and-sync interval (minutes):**: `30`
- **Commit message on auto commit-and-sync**: `vault backup: {{date}} {{time}}`
- **Commit message on manual commit**: `vault backup: {{date}} {{time}}`
- **Pull on startup**: `ON`

### **Linter**

- **General**: `Lint on save` をON。
- **YAML**: `Force YAML key lowercase` をON（検索性を高めるため）。
- **Markdown**: `Heading blank lines`（見出しの上下に空行）をON。

---

## 次のステップへの提案

まずは **Iconize** でサイドバーを整えるところから始めると、一気に「自分専用の司令塔」感が出てモチベーションが上がります。

設定を進める中で、**「Thinoに投稿したメモを自動で特定のプロジェクトフォルダに振り分ける仕組み」**や、**「Copilotに特定の技術分野（Red Teamなど）を深く学習させる方法」**について詳しく知りたいものはありますか？
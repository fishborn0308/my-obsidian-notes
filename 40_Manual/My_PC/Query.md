
下記のマニュアルについて矛盾点や否定的意見をお願いします。

## マニュアル

### infrastructure

```Markdown


## 1. ハードウェア・アセット・インベントリ

| カテゴリ            | デバイス名 / モデル番号                                 | スペック・詳細備考                              |
| :-------------- | :-------------------------------------------- | :------------------------------------- |
| **PC (Win)**    | Minisforum M1 Pro-285H                        | Core Ultra 9 285H / 64GB RAM / 2TB SSD |
| **PC (Mac)**    | Mac mini 2020 M1                              | 16GB RAM / 512GB SSD                   |
| **Monitor 1**   | innocn WR40-PRO                               | 40インチ ウルトラワイド (中央メイン)                  |
| **Monitor 2/3** | TERRA 2448WPV V3.1                            | 24インチ ×2 (右上横置き / 右下縦置き)               |
| **Network**     | TP-LINK BE85 / TL-SG505I/A                    | Wi-Fi 7 ルーター / 5ポートスイッチハブ              |
| **Key/Mouse1**  | MX Keys S / MX Master 4                       | **Logi Bolt規格**へ統合 Mac・Winでドングル使用      |
| **Key/Mouse2**  | Magic Keyboard / Magic Trackpad / Magic Mouse | Mac側で使用 (BlueToouth)                   |
| **Power**       | サンワ 10個口 / 個別SW付タップ                           | 一括スイッチ型と個別スイッチ型の2系統                    |
| **Light**       | BenQ ScreenBar Halo                           | 24インチ上段に設置 / ワイヤレスリモコン                 |

---

## 2. 電源系統図 (Power Distribution)

### 【系統A：大元】

**用途：長期不在時・災害時の完全遮断用。** 通常時は常にON。

* **物理配置：** デスク奥など、日常的に手が届かない場所に配置。
* **直結デバイス：** 昇降デスク、ルーター（BE85）
* **下位接続：** 系統B、系統C
	* **FlexiSpot E7 Pro:** 昇降デスク本体（常に動かせるようにするため）
	* **LENTIONハブ:** セルフパワー給電用（外付けHDDの安定稼働）
	* **TP-LINK BE85:** Wi-Fi 7 ルーター
	* **系統B**
	* **系統C**

### 【系統B：常時通電 / インフラ用】

**用途：Macのスリープ維持とネットワークの安定。** * **スイッチ特性：** 個別スイッチなし、または常にONを物理的にガード。

* **構成：** Mac mini, スイッチハブ, 40インチモニター, ScreenBar
	* **TP-LINK TL-SG505I/A:** スイッチハブ
	* **Mac mini:** 本体電源（スリープ運用）
	* **innocon WR40-PRO:** モニター電源
	* **BenQ ScreenBar Halo:** ライト電源

### 【系統C：作業時一括起動用】

**用途：作業時のみON。待機電力のカット。**

* **物理配置：** デスク上の手の届きやすい位置にスイッチを配置。
* **構成：** Minisforum, 24インチモニター×2, 各種充電器
	* **Minisforum:** 本体電源
	* **TERRA 2448WPV ×2:** モニター電源
	* **携帯スタンド:** 携帯とiPodsの充電用
	* **充電器:** USB-Cとライトニング  

> [!IMPORTANT]

> **注意点:** 系統Cをオフにしても、系統Aがオンであればルーター（Wi-Fi）やMac mini（スリープ）は生きているため、外出先からのTailscale経由のアクセスなども維持されます。この「二段構え」が明確であれば、以前指摘した矛盾は解消されます。



---

## 3. 詳細配線マップ (Port-by-Port Mapping)

### Mac mini 2020 M1 背面

1. **HDMIポート:** 24インチ (右下・縦置き用) ※OS側で90度回転設定
2. **USB-C (1):** 40インチモニター (DisplayPort出力)
3. **USB-C (2):** LENTIONハブ ➔ 外付けHDD×2 / Logi Bolt ドングル
4. **USB-A (1):** j5create JAC-365EJ (USB-A変換プラグ使用) ➔ 24インチ (右上・横置き用)
5. **USB-A (2):** EMEET S600 (Webカメラ)

### Windows (Minisforum)

1. **映像出力:** 40インチモニターへ直接接続
2. **USB-A (前面):** Logi Bolt ドングル
3. **USB-A (前面/延長):** YubiKey 5 NFC (天板右前端に固定)
4. **ネットワーク:** 有線LAN (スイッチハブ経由)

---
```

### APP

```markdown
## Mac

### 公式アプリ

* **1Password**: 強固なセキュリティを持つパスワード管理ツール。多要素認証（MFA）の管理も可能。
* **VScode**: 拡張性が高い高機能コードエディタ。開発からドキュメント作成まで幅広く対応。
* **Dropbox**: 信頼性の高いクラウドストレージ。ファイル同期や共有に利用。
* **GitHub**: Gitリポジトリのデスクトップ管理ツール。ソースコードのバージョン管理を視覚的に操作。
* **Slack**: チームコミュニケーションツール。エンジニア間での情報共有のデファクト。
* **Brave Browser**: プライバシー保護と広告ブロックに特化した、Chromiumベースの高速ブラウザ。
* **A Better Finder Renamer 12**: 高度なルール設定が可能な、ファイル名の一括置換・リネームツール。
* **BetterTouchTool**: トラックパッドやマウスのジェスチャー、ショートカットを高度にカスタマイズ。
* **ImageOptim**: 画像ファイルのメタデータを削除し、画質を維持したままファイルサイズを圧縮。
* **Notion**: ドキュメント、タスク管理、Wikiなどを集約できるオールインワン・ワークスペース。
* **Popclip**: テキストを選択した際に、コピーや検索、整形などのメニューを即座に表示。
* **zoom**: ビデオ会議および画面共有プラットフォーム。
* **Logi Options+**: ロジクール製デバイス（マウス・キーボード）のボタン割り当てや設定管理。
* **Fantastical**: 自然言語入力に対応した、視認性の高いカレンダー・リマインダーアプリ。
* **Yoink**: ファイルの一時置き場（シェルフ）を提供し、ドラッグ＆ドロップ操作を効率化。
* **Spark**: スマートな受信トレイ管理とクイック返信が特徴の高機能メールクライアント。
* **Keka**: 多様なフォーマットに対応した、シンプルかつ強力な圧縮・解凍ユーティリティ。
* **Th-Marker x**: 大量の画像に対し、サイズ変更やウォーターマーク追加を一括で行うツール。
* **Raycast**: Spotlightを強化したようなランチャー。スクリプトや拡張機能で多様な操作を自動化。
* **Eagle**: 画像やデザイン素材、参考資料を管理・検索しやすくするファイリングツール。
* **Obsidian**: ローカルのMarkdownファイルを活用する知識管理ツール。情報の網状リンクが特徴。
* **Microsoft Remote Desktop (Beta)**: Windows環境へリモート接続するための公式RDPクライアント。
* **Sound Source**: アプリごとの音量調節や出力先の変更、EQ設定が可能なオーディオコントローラー。
* **Tailscale**: WireGuardベースのメッシュVPN。複雑な設定なしでデバイス間を安全に接続。
* **TablePlus**: 多くのDB（MySQL, PostgreSQL等）に対応した、軽量でモダンなGUIエディタ。
* **CyberChef**: 「データのスイスアーミーナイフ」。エンコード・デコードや解析をブラウザ上で行う。
* **iTerm2**: macOS標準ターミナルの機能を大幅に拡張したターミナルエミュレータ。
* **Little Snitch 6**: アウトバウンド通信を監視・制御する、ホスト型ファイアウォール。
* **iStat Menus 7**: メニューバーからCPU、メモリ、ネットワーク、温度などの情報をリアルタイム監視。
* **Karabiner-Elements**: キーボードのキーマッピングを詳細にカスタマイズするツール。

### Apple Store

* **Be Focused Pro**: ポモドーロ・テクニックを用いたタスク・時間管理アプリ。
* **Kindle**: Amazonの電子書籍リーダー。技術書の閲覧にも利用。
* **Magnet**: ウィンドウを画面の端にドラッグして、瞬時に整列・分割管理するツール。

### Webアプリ

* **Draw.io**: ブラウザ上で動作する高機能な作図ツール。ネットワーク図やフローチャートの作成に最適。
* **Squoosh**: Googleが開発した画像圧縮・変換ツール。比較しながら最適な書き出しが可能。

---

## Windows

* **VMware**: 業界標準の仮想化プラットフォーム。複数のOSを安定して同時稼働。
* **GNS3**: 複雑なネットワーク構成をシミュレートするツール。実機のIOSイメージも動作可能。
* **Notepad++**: 軽量で多機能なテキストエディタ。多言語のシンタックスハイライトに対応。
* **Wireshark**: 世界で最も普及しているネットワークプロトコルアナライザ。パケット解析の必須ツール。
* **MobaXterm**: SSH, X11, RDP等を統合した、エンジニア向け究極のターミナルツール。
* **WSL2 (Kali Linux)**: Windows上でペネトレーションテスト用OSであるKaliを直接動作させる環境。
* **Cursor**: AI（LLM）と密接に統合された、次世代のコードエディタ（VS Codeフォーク）。
* **Logi Options+**: Windows環境におけるロジクール製デバイスのカスタマイズ管理。
* **Microsoft PowerToys**: Windowsをパワーユーザー向けに強化する公式ユーティリティ集。
* **QuickLook**: スペースキーを押すだけでファイルの内容をプレビュー（Macの機能を再現）。
* **EarTrumpet**: アプリケーションごとに音量を個別に操作できるサウンドコントロール。
* **NanaZip**: 7-Zipをベースに、Windows 11のコンテキストメニュー等に最適化された解凍ソフト。
* **Brave**: プライバシー重視のブラウザ。Windows環境でも高い広告ブロック性能を発揮。
* **Firefox**: オープンソースのブラウザ。セキュリティ診断用のアドオンが豊富。
* **1Password**: クロスプラットフォームで同期可能なパスワード管理ツール。
* **Burp Suite Community**: ウェブアプリケーションのセキュリティ脆弱性を診断するためのプロキシツール。
* **Windows Terminal**: 複数のシェル（PowerShell, CMD, WSL）をタブで管理できるモダンなターミナル。
* **Everything**: ローカルHDD内のファイルを一瞬で検索できる、超高速検索インデックスツール。
* **HeidiSQL / DBeaver**: データベースの管理・開発用GUIツール（Heidiは軽量、DBeaverは多機能）。
* **RDCMan**: 大量のリモートデスクトップ接続を一括管理するためのマイクロソフト公式ツール。
* **Portmaster**: 全アプリの通信を可視化し、トラッカーやマルウェアドメインを遮断するファイアウォール。
* **SnipDo**: テキスト選択時にコピー、検索、メール作成などのポップアップを表示（MacのPopclip相当）。
* **HWinfo64**: PC内部のハードウェア情報を詳細にモニタリングする診断ツール。
* **Macrium Reflect**: ディスクイメージのバックアップ、クローニング、修復を行う高機能ソフト。

---

```

#### Browser拡張

```markdown
## ブラウザの使い分け

* **Mac (日常): Brave**
* 強力な広告ブロックとプライバシー保護機能を標準装備。日常的なブラウジングや動画視聴を高速かつ安全に行うためのメインブラウザ。

* **Windows (Pentest): Firefox**
* カスタマイズ性が高く、プロキシ設定や開発者ツールが充実。ウェブアプリケーションの脆弱性診断やセキュリティ調査に特化して運用。

---

## 拡張機能

### Brave（日常・生産性）

* **1password**: ブラウザ上でパスワードやパスキーを安全に自動入力。
* **Dark Reader**: あらゆるウェブサイトをダークモードに変換し、長時間の作業による目の疲れを軽減。
* **DeArrow**: YouTubeのサムネイルやタイトルを、ユーザー投稿の適切なものに置き換え、クリックベイトを防止。
* **DeepL**: 世界最高精度のAI翻訳ツール。選択したテキストを即座に高精度な日本語へ翻訳。
* **Eagle**: ブラウザ上の画像をワンクリックでデザイン管理ソフト「Eagle」へ保存。
* **Extensity**: 増えすぎた拡張機能をメニューから一括でオン/オフ管理し、ブラウザを軽量に保つ。
* **Google翻訳**: ウェブページ全体の翻訳や、単語のクイックな意味確認に使用。
* **Keepa**: Amazonの商品価格推移をグラフ表示。買い時の判断やセール詐欺の防止に役立つ。
* **Return YouTube Dislike**: 現在の仕様では非表示となっているYouTubeの「低評価数」を再表示し、動画の信頼性を判断。
* **Save To Notion**: ウェブ上の記事や情報を、整形した状態でNotionのデータベースへ直接保存。
* **SponsorBlock for YouTube**: 動画内のスポンサー枠やイントロなどの不要なセグメントを自動的にスキップ。
* **ZED: Zoom Easy Downloader**: Zoomのクラウドレコーディング動画を簡単にダウンロードするための支援ツール。

### Firefox（ペンテスト・OSINT）

* **Wappalyzer**: 閲覧中のサイトが使用しているCMS、フレームワーク、DB等の技術スタックを瞬時に特定。
* **Shodan**: 表示しているサイトのIPアドレスに基づき、公開されているポートやサービス情報をShodanから取得。
* **TruffleHog**: ページのソースコードやJS内をスキャンし、誤って公開されているAPIキーや認証情報を検出。
* **FoxyProxy**: プロキシ設定を素早く切り替え。Burp Suite等の診断ツールへ通信を転送する際の必須アイテム。
* **Hack-Tools**: ペンテストに役立つチートシートや、リバースシェルのコマンド生成、エンコードツールを集約。
* **Cookie Editor**: クッキーの値を確認・編集・削除。セッションハイジャックのテストや認証状態の操作に使用。
* **SingleFile**: ページ全体（画像やCSS含む）を1つのHTMLファイルとして保存。証拠保存やオフライン閲覧に最適。
* **Copy as Markdown**: 閲覧中のページリンクや選択範囲をMarkdown形式でコピー。学習ノート作成を効率化。
* **Extensity**: 診断時のみ必要な重い拡張機能（TruffleHog等）を素早く切り替えるために使用。
* **Dark Reader**: 診断業務は深夜に及ぶことも多いため、Firefox側でも視認性と目の保護を両立。

---

```

#### VSCode・Obsidian


```markdown


## VSCode

### VS Codeの設定(`settings.json`)

`settings.json`はVS Codeの動作を細かく制御するためのファイルです。VS Codeを開き、`Cmd + Shift + P`でコマンドパレットを開き、「Preferences: Open User Settings (JSON)」と入力して開くことができます。

	
	{
	  // --- 1. エディタ基本設定 (Global) ---
	  "editor.fontSize": 14,
	  "editor.lineHeight": 2,
	  "editor.fontFamily": "'Source Code Pro', Consolas, monospace",
	  "editor.tabSize": 2,
	  "editor.insertSpaces": true,
	  "editor.wordWrap": "on",
	  "editor.formatOnSave": true, // 基本はON
	  "editor.defaultFormatter": "esbenp.prettier-vscode", // Prettierを基本に
	
	  // --- 2. ファイル・検索除外 (整理) ---
	  "files.exclude": {
		"**/.git": true,
		"**/.DS_Store": true,
		"**/node_modules": true
	  },
	  "search.exclude": {
		"**/node_modules": true,
		"**/dist": true
	  },
	
	  // --- 3. 言語別の上書き (言語特有のルールのみ記述) ---
	  "[python]": {
		"editor.tabSize": 4,
		"editor.defaultFormatter": "ms-python.python"
	  },
	  "[markdown]": {
		"editor.formatOnSave": true, // markdownlintで整形
		"editor.defaultFormatter": "DavidAnson.vscode-markdownlint"
	  },
	
	  // --- 4. 拡張機能固有の設定 ---
	  // Paste Image: 保存先の一貫性を保持
	  "pasteImage.path": "${currentFileDir}/images/${currentFileBasenameNoExtension}/",
	  
	  // markdownlint: ルールの微調整
	  "markdownlint.config": {
		"MD013": false, // 行の長さ制限を無視
		"MD033": { "allowed_elements": ["br"] }
	  }
	}
	

### VS Codeの拡張機能

1. **Draw.io Integration:**
   * VS Code内でDraw.io(diagrams.net)の図を作成・編集できます。
   * ネットワーク構成図、システムアーキテクチャ図、攻撃ツリーなど、セキュリティ学習において図解は非常に重要です。
   * `*.drawio`または`*.dio`ファイルをVS Codeで開くと、Draw.ioのエディタが立ち上がります。

2. **PlantUML:**
   * Mermaidと同様に、テキストベースでUML図(シーケンス図、クラス図など)を作成・プレビューできます。
   * Mermaidと合わせて、図の表現力を高めるのに役立ちます。特にシステムの設計やプロセスの理解を深める際に強力
   * `*.drawio`または`*.dio`ファイルをVS Codeで開くと、Draw.ioのエディタが立ち上がります。

3. **Code Spell Checker:**
   * スペルミスをチェックし、学習ノートの品質を向上させます。
   * 特に技術用語を記述する際に役立ちます。

4. **Output Colorizer:**
   * VS Codeの出力に色をつける拡張機能

5. **indent-rainbow:**
   * インデントに色をつける拡張機能

6. **Material Icon Theme:**
   * ファイルアイコンをデフォルトよりもわかりやすいアイコンに変更する拡張機能

7. **Path Intellisense:**
   * ファイルパスの自動補完機能を提供します。
   * Markdown内で画像や関連ファイルへのリンクを記述する際に便利です。

8. **Auto Rename Tag:**
   * tagをリネームしてくれる拡張機能

9. **Error Lens:**
    * エディタ内に警告やエラーが表示される

10. **Bookmarks::**
    * コードやMarkdownの特定の行にブックマークを設定できます。後から参照したい重要な情報や、途中までしか書けていないセクションなどにマークを付けておくと、素早くアクセスできて便利

11. **TODO Highlight:**
    * TODOコメントをハイライト表示する拡張機能

12. **Todo Tree:**
    * TODOコメントの一覧を表示することができる拡張機能

13. **YAML:**
    * YAML拡張機能(Red Hat)

14. **Prettier - Code formatter:**
    * コードだけでなくMarkdownもフォーマットできます。
    * 記述スタイルを統一し、読みやすいノートを維持するのに役立ちます。
    * `settings.json`で`editor.defaultFormatter`を`esbenp.prettier-vscode`に設定し、`editor.formatOnSave`を`true`にすると、保存時に自動で整形されます。

15. **Markdown All in One:**
    * Markdownのプレビュー、自動補完、目次生成など、Markdownの執筆に必要な機能が全て詰まっています。
    * 特に「Table of Contents(目次)」機能は、長いノートで非常に役立ちます。
    * `Cmd + Shift + P`でコマンドパレットを開き、「Markdown All in One: Create Table of Contents」で自動生成できます。

16. **markdownlint:**
    * markdownlintは「Markdown」の構文とスタイルをチェックしてくれる拡張機能

17. **Mermaid Markdown Syntax Highlighting:**
    * Markdown内でMermaid記法(テキストベースでフローチャートやシーケンス図などを記述できる)を使用する際に、シンタックスハイライトとプレビューを有効にします。
    * 複雑なプロセスや情報の流れを図示するのに便利です。

18. **Markdown Link Checker:**
    * Markdown内のリンク切れをチェックする拡張機能

19. **Markdown Link Updater:**
    * ワークスペース内のファイルが移動またはリネームされると、Markdownリンクが自動的に更新

20. **Paste Image:**
    * クリップボードの画像をMarkdownに直接貼り付けることができます。
    * スクリーンショットなどをノートに含める際非常に便利です。
    * 貼り付けた画像は、設定したパス(例: `./images/`)に自動で保存されます。

21. **GitLens — Git supercharged:**
    * Gitの情報をVS Code上で視覚的に強化します。
    * ファイルの各行の最終コミット情報(誰がいつ変更したか)を表示したり、コミット履歴を詳細に確認したりできます。
    * ローカルGitでのバージョン管理をより強力にサポートします。

22. **Git Graph:**
    * Gitの履歴をグラフで表示する拡張機能

23. **GitHub Actions:**
    * GitHub Actions関連の機能を提供する拡張機能

24. **GitHub Pull Requests and Issues:**
    * GitHubのプルリクエストをVS Codeで直接管理できる拡張機能

25. **GitHub Copilot:**
    * AIを活用したコード補完ツール

26. **Container Tools
    * Docker コンテナのツール

### Mac miniでの環境構築のヒント

* **Homebrewの導入:**
  * Mac用のパッケージマネージャであるHomebrewを導入しておくと、今後必要なツール(Git自体もそうですが、各種セキュリティツールなど)のインストールが非常に楽になります。
  * ターミナルで以下のコマンドを実行してインストールできます。

	    
	    /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
	    

* **Gitのインストール:**
  * MacにはデフォルトでGitがインストールされていることが多いですが、最新版をHomebrewでインストールしておくことをオススメします。

	    
	    brew install git
	    

## Obsidian

研究中

```

### RunBook

```Markdown

## 目的

脳のメモリを使わず操作を完結

### 日常

**Mac(Brave)をメインとして効率的に情報をNotion/Eagleへ集約

- **Raycast Quicklinks
	- n: Notion
	- tr + スペース + {クエリ}: Google翻訳
	- k + スペース + {クエリ}: Kindle Cloud Reader
- **Brave
	- Eagle for Chrome
	- Save to Notion
- Logi Option+
	- ActionRingsとジェスチャーボタンでショートカットを登録
- BTT
	- Magic Trackpadでの画面操作をジェスチャーで管理

#### 主な操作

- **捕獲:** 
	- Braveでレシピ表示
	- Eagle for chromeで画像保存
	- またはSave to Notionで画像保存
- **加工:**
	- ImageOptimで画像圧縮
	- Th-Maker Xで画像リサイズ
- **登録:**
	- NotionのDBに画像をドラッグ

### 専門

**ネットワーク・ペンテスト・セキュリティ学習

- Logi Option+
	- ActionRingsとジェスチャーボタンでショートカットを登録
	- Mac側でLogi Option+のプロファイルを切り替え（Windows Remote Desktop使用中で切り分け）

#### 主な操作

- MacからRDP（フル画面）
- WindowsでVM起動
- VSCodeからRemote SSHでCUI操作
- Karabinerで Cmd → Ctrl
- ブラウザはFireFox
- 学習成果・実験ログ・証跡の保存はObsidian

## ウインドウ管理・スナップエリア構成

- メインモニタ
	- 左エリア: 幅33% ウインドウを貯めておくエリア
	- 中央エリア: 幅40~50% メイン作業場
	- 右エリア: 幅20~30% サブ作業場
- サブモニタ１
	- ピボットで縦使用
	- VSCodeやPDFの全画面表示
- サブモニタ２
	- 監視・チャット
	- ウィジェット
```
###  Maintenance

```markdown
## Backup戦略

### バックアップ用ストレージ

- DropBox
- 500GB
  - マウントしない。
  - CCCを使用したデータバックアップ(Mac)
  - OS再インストール＋手動設定・アプリインストール次のクリーンな状態のバックアップ
    - Mac
    - Windows
- 2TB
  - Time Machine

### OSイメージ

- Mac
  - Time Machine
  - 致命的な場合は500GBのクリーンなイメージから
- Windows
  -　500GBに保存したOSイメージから

### データ

- Macのデータのみ保管
  - /Users/ユーザ名/Documents/work
    - VS Code
    - Obsidian
    - Eagle

DropBoxと500GBのHDDでバックアップ

### メンテナンスチェックリスト

- Monthly
  - OSのアップデート
  - Brewfileの更新: `brew bundle dump`
  - Eagleのデータ整理: 重複削除

- Weekly
  - CCC実行
  - VS Codeのコミット

- Daily
  - ダウンロードファイルの削除
  - ブラウザタブの整理
    - ブックマーク
    - RayCastのQuicklinks
  - Workフォルダの整理: 一時的に作成したデータ、SlackやSparkに溜まった一次情報を格納

### 緊急時の復旧

- 災害時の復旧
  - 認証情報の確保
    - 1Passwordのログイン
  - 情報の復元
    - Notion/GitHubのログイン
  - OS
    - TimeMachine
    - Macrium Reflect
  -入力系
    - Logi Options+のログイン
    - Karabiner-ElementsのConfig

```

#### App_Buckup

```markdown
## 1. バックアップ対象の分類

CCCでバックアップする際、以下の3つの属性でフォルダを分けると管理がしやすくなります。

| 分類 | 対象アプリ（例） | バックアップの考え方 |
| --- | --- | --- |
| **A. ローカルDB型** | Obsidian, Eagle, TablePlus | **最優先。** アプリ内にのみデータがあるため、フォルダごとコピー必須。 |
| **B. 設定（Config）型** | iTerm2, Karabiner, BTT, Raycast | ツール自体の設定。エクスポート機能か `~/Library/Application Support/` を対象に。 |
| **C. 作業データ型** | VS Code, Draw.io, Wireshark | `/work` フォルダ等に集約し、プロジェクト単位でバックアップ。 |

---

## 2. Mac側：CCCに登録すべき主要パス一覧

Macのアプリは設定ファイルが分散しがちです。以下のパスを参考に、CCCのタスクを作成することをおすすめします。

### データの「実体」がある場所

* **Obsidian / Eagle**:
* 作成した「Vault（保管庫）」や「Library」の親フォルダ。

* **Raycast / BetterTouchTool / Karabiner**:
* 各アプリのエクスポート機能で書き出した設定ファイル（を `/work/configs` 等に集約）。

* **1Password**:
* 基本はクラウド同期ですが、万が一に備え「Emergency Kit」のPDFを暗号化領域へ。

* **iTerm2**:
* `Settings` > `General` > `Preferences` で「Load preferences from a custom folder」を有効にし、そのフォルダを対象にする。

### セキュリティ・ネットワーク関連

* **Little Snitch**:
* ルール設定のエクスポート（`.lsrules`）。

* **Tailscale / TablePlus**:
* 接続先情報などは `~/Library/Application Support/TablePlus` などに格納されています。

---

## 3. Windows側：バックアップすべき資産

Windowsは「イメージバックアップ」が基本ですが、CCCのようにファイル単位で救出する場合の重要ターゲットです。

* **WSL2 (Kali Linux)**:
* `wsl --export` した `.tar` ファイル。

* **GNS3 / VMware**:
* `/Documents/GNS3/projects` フォルダ。
* 仮想マシンの `.vmx` や `.vmdk` ファイル（容量が大きいため、変更があった時のみ）。

* **Burp Suite**:
* ユーザー設定ファイルや、特定のプロジェクトファイル。

* **MobaXterm**:
* セッション情報（エクスポートした `.mxtpro`）。

---

## 4. バックアップ・フロー図

---

## 5. 実践へのアドバイス： CCCタスクの構成

CCCで「バックアップフォルダを選定」する際、1つの巨大なタスクにするのではなく、**「性質別」にタスクを分ける**と効率的です。

1. **Task 1: Daily_Work**: `~/Documents/work` (Obsidian, VS Code等)

* 毎日実行。差分のみなので高速。

1. **Task 2: Weekly_App_Configs**: 各アプリの設定エクスポートファイル

* 週に一度実行。

1. **Task 3: Monthly_Heavy_Assets**: EagleのライブラリやVMイメージ

* 月に一度。容量が大きいため、時間に余裕がある時に実行。

```

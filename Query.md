
下記の構成のデスクを作成しました

Geminiでの前提条件とコンテキスト管理をしたい

## 01-infrastructure

```

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

## 02-Runbook

```

## 1. デバイスの三権分立と中央検索

「記録・操作の摩擦」をゼロにし、脳のメモリを使わず操作を完結させる。

| デバイス / ツール         | 役割（担当）                      | 操作の直感            |
| :----------------- | :-------------------------- | :--------------- |
| **Trackpad (BTT)** | **空間制御:** 場所、サイズ、モニター間移動    | 「どこで作業するか」を決める   |
| **MX Master 4**    | **実行制御:** タブ操作、更新、決定、保存     | 「その場で何を動かすか」を決める |
| **Raycast (司令塔)**  | **召喚制御:** アプリ起動、ファイル検索、履歴呼出 | 「ゼロから何かを呼び出す」    |

---

## 2. ウィンドウ管理：ステージマネージャ × 3画面

40インチをメインに、ステージマネージャをONにして運用。

### モニター別役割

1. **40インチ (Main/Sub):** ステージマネージャON。中央を「舞台」、左を「袖」として使う。
2. **24インチ縦 (Pivot):** 資料・ログ・長いコードのフル画面表示用。
3. **24インチ横 (Monitor 2):** 監視・ウィジェット・チャットの固定配置。

### Trackpad (BTT) ジェスチャー

- **3本指タップ:** 中央メイン領域（40インチ右寄り65%）へ移動
	- Action - Move/Resize:Top left Corner,top Teft corner of the screen
		- X:Relative to screen wid :35
		- Y:Relative to screen wid :0
		- wid:Relative to screen :65
		- height:Relative to screen :100
- **3本指スワイプ左:** サブ領域（40インチ左端30%）へ移動
	- Action - Move/Resize:Top left Corner,top Teft corner of the screen
		- X:Relative to screen wid :7
		- Y:Relative to screen wid :0
		- wid:Relative to screen :28
		- height:Relative to screen :100
- **3本指スワイプ右:** ピボット画面（24インチ縦）へ移動 ＋ 最大化
	- Action - Move Window to Next Monitor
	- Action - Resize Active Windows to Specific Position & Size
		- x:3441
		- y:0
		- Width:1078
		- Height:1918
- **3本指スワイプ上:** ウィンドウを最大化
	- Action - Maximize Windows
- **3本指スワイプ下:** アプリを隠す（Hide）
	- Action - cmd + H

---

## 3. アプリ内操作：MX Master 4 (流れ・実行：Logi Options+)

マウスを動かさず、親指一本で「実行」を完結させる。

- **ジェスチャーボタン + 左右:** 仮想デスクトップ/フル画面アプリの切り替え（Mac ↔ Win）
- **ジェスチャーボタン + 上:** コピー (Cmd + C)
- **ジェスチャーボタン + 下:** 貼り付け (Cmd + V)
- **ジェスチャーボタン クリック:Raycast起動    
- **ActionRings (単押し):** スクリーンショット(範囲)、クリップボード履歴、主要アプリ召喚

---
## 4. 情報の「分散配置」と「中央検索」

「記録の摩擦（どこに書くか迷うこと）」をゼロにし、「検索の速度」を最大化する。情報はツールごとに最適化して分散させ、**Raycast** で串刺し検索を行う。

### 情報の「住み分け」定義

|**保存先**|**役割（情報の種類）**|**検索方法 / キー**|
|---|---|---|
|**Notion**|**構造化・長期資産:** レシピ、カリキュラム、マニュアル、DB|Raycast (`n`)|
|**Obsidian**|**思考・実験ログ:** 日報、検証ログ、コマンド履歴、下書き|Raycast (`o`)|
|**Eagle**|**視覚資産:** スクリーンショット、証跡、デザイン参考、画像|Raycast (`e`)|
|**GitHub**|**コード資産:** スクリプト、Config、IaC定義|Raycast (`gh`)|

### 命名規則（Namespace）の統一

ツールを跨いでも検索にかかるよう、タイトルにプロジェクト識別子を付与する。
- `[SEC]`: セキュリティ学習・ペンテスト関連
- `[NET]`: ネットワークインフラ構築関連
- `[Daily]`: 生活・事務・ルーチン関連

---

## Phase 1. macOS システム設定（基盤）

まず、OS側で物理的な配置とステージマネージャの挙動を正しく定義します。

### 1. ディスプレイの配置 (Arrangement)

1. **システム設定 > ディスプレイ > 配置...** を開きます。
2. 左から **[40インチ（主ディスプレイ）] - [24インチ縦]** の順に並べます
3. [40インチ（主ディスプレイ）]の右上に[24インチ横]を並べます
4. 24インチ縦（ピボット）については、ディスプレイ設定の「回転」を **90度または270度** に設定してください。
    

### 2. ステージマネージャの設定

1. **システム設定 > デスクトップとDock** を開きます。

2. **ステージマネージャ** を ON にします。

3. 「カスタマイズ」をクリックし、以下の設定を推奨します：
    - **最近使ったアプリケーションを表示:** **ON**（40インチの左端にストリップが表示されます）
    - **デスクトップ上のアイテムを表示:** お好み（クリーンにするならOFF）
    - **アプリケーションからウィンドウを表示:** 「一度にすべて」

### 3. RDPフルスクリーン運用の最適化
* **OS設定:** 「ディスプレイごとに個別の操作スペース」を必ずON。
* **RDP設定:** `Use all monitors` はOFF。`Optimize for Retina` はON。
* **起動ルール:** RDP接続センターのウィンドウを40インチに置いてから接続を開始する。
---

## Phase 2. BTTによる詳細な領域座標の設定

40インチ画面の左端にはステージマネージャの「ストリップ（最近のアプリ）」が並ぶため、**「サブ領域」はその右隣から開始**するように設定するのがポイントです。

### BTTアクション：Move Window to Custom Position

BTTの `Custom Move & Resize` 設定画面で、以下の数値を入力してください（パーセンテージ指定が最も安定します）。

|**配置先**|**X軸（左からの位置）**|**Y軸（上からの位置）**|**幅 (Width)**|**高さ (Height)**|**備考**|
|---|---|---|---|---|---|
|**メイン領域** (3本指タップ)|**35%**|**0%**|**65%**|**100%**|ステージの主役。右寄せ。|
|**サブ領域** (3本指左スワイプ)|**7%**|**0%**|**28%**|**100%**|ストリップのすぐ右に配置。|

> **設定のコツ:** X軸を **7%** に設定することで、ステージマネージャの「最近使ったアプリ」のアイコンと重ならずに、資料を並べることができます。

---

## Phase 3. モニター間移動の設定（ピボット画面）

3枚モニターがある場合、「次のモニターへ移動」は順番が狂うことがあるため、**「特定のモニター」を指定**するのが確実です。

### BTTアクション：3本指右スワイプ

1. **Move Window to Monitor...** を選択。

2. **Monitor 3**（24インチ縦置き）を指定。

3. 「その後に行うアクション」として **Maximize Window** を追加。

---

## Phase 4. ステージマネージャを使いこなす運用フロー

この構成を実現すると、以下のようなワークフローになります。

1. **アプリを召喚:** ジェスチャーボタンでRaycastを起動し、アプリを開く。

2. **舞台に上げる:** `3本指タップ` で、アプリが画面中央（メイン領域）にパッと整列。

3. **資料を脇に置く:** 別のブラウザを開き、`3本指左スワイプ`。これで「メイン作業」と「参照資料」がペアになります。

    - **ここが重要:** ステージマネージャはこの「メインとサブのペア」を一つの **グループ** として記憶します。

4. **グループの切り替え:** 左端に見えている「最近使ったアプリ」をクリックするか、マウスのサイドホイールで、別の作業ペア（例：NotionとSlackのセット）へ一瞬で切り替わります。

5. **集中モード:** ログや長いコードをじっくり見たい時は、そのウィンドウを `3本指右スワイプ`。右側のピボット画面へワープし、フル画面化されます。

## 5. 操作フロー

### 日常：情報の「捕獲（Capture）」と「加工」

**Brave(Mac)をメインとして効率的に情報を集約する。**

1. **捕獲:**

    - Web記事は `Save to Notion` で構造化して保存。
    - 画像や図解は `Eagle for Chrome` で即座に保存。

2. **加工:**

    - `ImageOptim` / `Th-Maker X` で画像サイズを最適化。

3. **登録:**

    - NotionのDBやObsidianのノートへ、加工済み画像をドラッグ。

### 専門：環境構築・ペンテスト（Pentest）

**Windows(VM)環境での作業と、Macでの記録を並行させる。**

1. **接続:** Macから `Microsoft Remote Desktop` でWindowsへフル画面接続。

2. **操作:** `Logi Options+` のプロファイルを切り替え、ジェスチャーボタンをWindows操作に最適化。

3. **記録:** * コマンド履歴や実験結果は、Mac側の `Obsidian` にプロジェクト識別子（`[SEC]`等）をつけてリアルタイムに殴り書き。

    - VMの構成変更は「VM環境再構築・構成定義書テンプレート」に従い、Obsidianに記録。

---

## 6. 検索・復旧（Search & Retrieve）

**脳のメモリを使わず、Raycastからすべてを呼び出す。**

1. **クイックリンク:** Raycastの `Quicklinks` に以下を登録。

    - `n` + スペース: Notion検索
    - `o` + スペース: Obsidian検索
    - `tr`: Google翻訳
    - `k`: Kindle Cloud Reader

2. **串刺し検索:** `Cmd + Space` 後、識別子（例: `SEC`）とキーワードを入力。候補に並んだ各ツールのページから、必要な情報へ即座にジャンプする

---
```

### 02-01-APP

```
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
* **Google Chrome**: ワカモレ環境を開く用のブラウザ。
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
* **Slack**: チームコミュニケーションツール。エンジニア間での情報共有のデファクト。

---

```

### 02-02-Browser

```
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
* **Dark Reader**: 診断業務は深夜に及ぶことも多いため、Firefox側でも視認性と目の保護を両立。

---

```

### 02-03VSCode・Obsidian

```

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
```

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
```

### 03-App_Buckup

```
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

2. **Task 2: Weekly_App_Configs**: 各アプリの設定エクスポートファイル

	* 週に一度実行。

3. **Task 3: Monthly_Heavy_Assets**: EagleのライブラリやVMイメージ

	* 月に一度。容量が大きいため、時間に余裕がある時に実行。


```

## 03-Maintenance

```
## 1. Backup戦略：再現性重視の「軽量」バックアップ

500GBの物理的制約を考慮し、巨大なバイナリ（OS丸ごと）よりも、設定ファイル（Code）とドキュメント（Note）の保存を優先する。
### バックアップ用ストレージ構成

| ストレージ | 役割 | バックアップ内容 |
| --- | --- | --- |
| **DropBox** | リアルタイム同期 | `work` フォルダ (Markdown, Scripts), 1Password Kit |
| **500GB HDD** | コールド・バックアップ | **クリーンOSイメージ** (Win/Mac各1), 各種Config, VMテンプレート |
| **2TB HDD** | タイムマシン | Mac miniのシステム全体 (Time Machine用) |

### 500GB HDD内のディレクトリ構造

1. `/OS_Images/`: クリーンインストール直後の最小イメージのみ。

2. `/VM_Templates/`: 各VMの構成定義書 (Markdown) とゴールデンイメージ。

3. `/App_Configs/`: VSCode (`settings.json`), Logi Options+, Karabiner, iTerm2設定。

4. `/Archives/`: 年に一度、`work` フォルダから切り出す過去資産。
---
## 2. メンテナンス・チェックリスト

「脳のメモリを使わない」ための定例作業。

### Monthly (毎月第1週末)

* **OS/App更新:** Windows Update, macOS Update, `brew upgrade` の実行。

* **インフラ定義更新:** `brew bundle dump` を実行し、インストール済みリストを更新。

* **VM見直し:** VM構成テンプレートと実機に乖離がないか確認。不要なスナップショットを削除。

* **Eagle整理:** 不要なキャプチャの削除と重複確認。

### Weekly (毎週日曜夜)

* **CCC実行:** Macの `work` フォルダおよび `App_Configs` を500GB HDDへミラーリング。

* **ストレージ残量確認:** 500GB HDDの空き容量をチェック。20%を切る場合はOSイメージの旧世代を削除。

### Daily (作業終了時)

* **Git Sync:** `work` フォルダ内の変更をすべて Commit & Push。

* **クリーンアップ:** `Downloads` フォルダを空にする。ブラウザのタブを整理（Raycast Quicklinksへ移行）。

* **電源管理:** 系統C（作業用スイッチ）をOFF。長期不在時は系統A（大元）をOFF。

---
## 3. 緊急時の復旧プロトコル (Disaster Recovery)

「何から手をつけるか」の優先順位を定義。
### Phase 1: 認証の確保 (RTO: 10min)

1. **1Passwordの復旧:** 500GB HDD内の「Emergency Kit」を参照。

2. **MFAの疎通:** スマホまたは YubiKey で各クラウドサービスへのアクセスを確保。

### Phase 2: 情報の同期 (RTO: 30min)

1. **Notion / GitHub:** 最新の作業ログとタスクリストを同期。

2. **Obsidian:** DropBoxから最新のナレッジベースをプル。

### Phase 3: 環境の再構築 (RTO: 2h〜)

1. **OS復旧:** Time Machine または 500GB HDD内のクリーンイメージから書き戻し。

2. **Appインストール:** `brew bundle` で一括インストール。

3. **Config反映:** `/App_Configs/` 内の設定ファイルを各アプリへインポート。

4. **VM復旧:** テンプレートに基づき、必要なものだけゴールデンイメージからクローン。

---
## 4. 500GB 運用ルール（厳守事項）

* **「とりあえずバックアップ」の禁止:** VMのフルバックアップは原則禁止。構成メモ（テンプレート）で代用する。

* **Eagleのライブラリ制限:** Eagleのデータが100GBを超えたら、古い素材を外部アーカイブ（またはクラウドのみ）へ逃がし、ローカルの500GB HDDを圧迫させない。

* **重複排除:** クラウド（Dropbox）にあるものは、CCCのバックアップ対象から外す設定にする。
```

## Obsidianのテンプレート

```
### ## 1. ペンテスト・検証ログ用テンプレート

（HTB/OSCPや技術検証など、`20_Projects` フォルダで主に使用します）

新規ファイル作成時に「ターゲットIP」などを聞き、自動的に構成を整えます。

---

### 改善のポイント

#### 1. JavaScript (Templater) による変数管理

冒頭の `<%* ... -%>` ブロックで入力を変数（`targetIp` など）に格納しています。これにより、フロントマターだけでなく、本文中の `nmap` コマンド内にも**自動的に IP アドレスが流し込まれる**ようになり、タイポを防げます。

#### 2. Dataview への対応

フロントマターに `status`, `user_flag`, `root_flag`, `difficulty` を追加しました。これにより、後ほど以下のようなクエリを書くだけで、**「現在の攻略進捗一覧」**を自動生成できるようになります。

> **活用例（別ファイルに作成）:**
> ```sql
> ```dataview
> TABLE target_ip, os, difficulty, status
> FROM "20_Projects"
> WHERE type = "verification"
> SORT date DESC
> ```

#### 3. 証跡（Evidence）セクションの標準化

ユーザー様の「40インチメイン画面で作業、ピボット画面で記録」というスタイルに合わせ、画像リンクをあらかじめテーブル形式で用意しました。ファイル名を `プロジェクト名_nmap.png` のようにリネームして保存するだけで、即座にドキュメントが完成します。

## 2. デイリーノート用テンプレート

（日々の学習記録やタスク管理用。識別子 `[Daily]` を自動付与します）

### 4つの改善ポイント

#### 1. 前後のノートへのナビゲーション

冒頭に `[[昨日の日付]] | [[明日の日付]]` を入れています。ピボット画面（縦長）でObsidianを表示している際、カレンダーを開かなくても**マウス操作だけで数日間の学習記録をサクサクと遡れる**ようになります。

#### 2. Dataviewによる「活動の自動収集」

これが最も強力です。`今日作成・更新したノート` セクションに Dataview のクエリを埋め込みました。

- **メリット:** 「今日、どのマシンの攻略を進めたか」「どの設定ファイルを書き換えたか」を自分でリストアップする必要がありません。
- **資産化:** 1ヶ月後にデイリーノートを見返したとき、その日に具体的にどのプロジェクトノート（`[SEC]` 等）を触っていたかが一目でわかります。

#### 3. タイムログの意識

インフラエンジニアの運用ログ（作業票）の感覚で、箇条書きの冒頭に `14:35 -` のように時刻を入れる癖をつけると、OSCP試験本番のタイムマネジメント（何に時間を溶かしたか）の訓練になります。

#### 4. 命名規則（Namespace）の徹底

テンプレート内にあらかじめ `[SEC]` `[NET]` の見出しや接頭辞を置いておくことで、**「情報の住み分け」を脳を使わずに強制**できます。

---

### 運用のコツ：ピボット画面での表示

ユーザー様の構成（右側に24インチ縦置き）なら、**「左側にデイリーノート」「右側に現在進行中のプロジェクトノート（[SEC]）」**を上下または左右に並べておくスタイルが非常に効率的です。

- **入力:** 何か作業をしたら、とりあえずデイリーノートに時刻とやったことを殴り書き。
- **整理:** 詳細な手順やコマンドの結果は、プロジェクトノート側のテンプレート（先ほどの検証ログ）に集約。

このテンプレートを適用して、実際に **「昨日攻略したノート」が自動でリストアップされる快感** をぜひ体験してみてください。

次に、このデイリーノートから **「1週間の学習時間を自動集計する週報」** の作り方も気になりますか？

## 3. チートシート・技術メモ用テンプレート

（`30_Knowledge` で使用。Linterが整形しやすい構造にします）

### 改良のポイント

#### 1. プロパティ参照の安定化

前回の検証ログ同様、`tp.frontmatter["category"]` をタイトルやリンクで直接使おうとすると、ファイル作成のタイミングによっては「空白」になってしまうことがあります。

最初に `const category = ...` と変数で受けておくことで、タイトルや下部の `[[_Index_...]]` リンクにも**100%確実に値が反映**されます。

#### 2. 「逆引き・解決策」のセクション名修正

ユーザー様がコメントされていた通り、`(➔ 逆引き・解決策 に修正)` を反映しました。このセクションは **「トラブルシューティングの記録」** としても機能するため、エンジニアにとって最も価値ある情報になります。

#### 3. Dataviewによる「実戦投入記録」の自動化

ここが最大のポイントです。

チートシートの末尾に「この知識をどのプロジェクト（マシン攻略）で使ったか」を自動表示するクエリを組み込みました。

* **メリット:** 「nmapの使い方はわかったけど、実際の攻略でどう使ったかな？」と思った時、チートシートの下を見るだけで、**過去の自分の成功例（検証ログ）へ一瞬でジャンプ**できます。

---

### 導入後のフォルダ運用アドバイス

このテンプレートは、`30_Knowledge/01_cheatsheets` フォルダ以下の**Folder Templates**として設定することをお勧めします。

1.  `Settings` > `Templater` > `Folder Templates`
2.  `30_Knowledge/01_cheatsheets` フォルダに対して、このテンプレートを割り当てる。
3.  新しいコマンド集を作る際は、そのフォルダ内でファイルを作るだけで、自動的に「カテゴリ選択」が始まります。

## 運用のアドバイス

### 1. テンプレートの呼び出し方

ノートを作成した後、**`Alt + E`**（デフォルトのTemplaterショートカット）を押すとテンプレート一覧が出るので、そこから選んで適用します。

### 2. Linterとの連携

これらのテンプレートにはYAMLフロントマターが組み込まれています。保存（`Cmd + S`）すると、先ほど設定した **Linter** が自動的に動いて：

* タグを整理し
* 見出しの上下に空行を入れ
* 末尾の空白を消してくれます。

### 3. JavaScript (Templater) による変数管理

冒頭の `<%* ... -%>` ブロックで入力を変数（`targetIp` など）に格納しています。これにより、フロントマターだけでなく、本文中の `nmap` コマンド内にも**自動的に IP アドレスが流し込まれる**ようになり、タイポを防げます。

### 4. Dataview への対応

フロントマターに `status`, `user_flag`, `root_flag`, `difficulty` を追加しました。これにより、後ほど以下のようなクエリを書くだけで、**「現在の攻略進捗一覧」**を自動生成できるようになります。

> **活用例（別ファイルに作成）:**
> ```sql
> ```dataview
> TABLE target_ip, os, difficulty, status
> FROM "20_Projects"
> WHERE type = "verification"
> SORT date DESC
> ```

#### 知識側から検証を逆引きする（逆参照）

例えば、`30_Knowledge/01_cheatsheets/app/nmap.md` というチートシートの末尾に、以下のコードを記述します。

#### 「このツールを使用した検証ログ」一覧の表示


TABLE target_ip as "ターゲット", status as "ステータス", date as "実施日"
FROM "20_Projects"
WHERE contains(tools, this.file.name) OR contains(tools, [[#]])
SORT date DESC


#### このクエリで起こること：

- `20_Projects` フォルダ内をスキャン。
- `tools` プロパティに「nmap（このファイル名）」が含まれているノートを自動抽出。
- **結果:** nmapのチートシートを開くだけで、**「過去にどのマシンで、どんなオプションを使ってスキャンしたか」**の実例が即座に一覧表示されます。

---

#### 検証側から知識へ飛ぶ（順参照）

逆に、検証ログ（`20_Projects`）から、ツールの使い方をすぐに確認したい場合です。

### テンプレートの「ターゲット情報」付近に追加


### 関連リソース
- **使用ツールの解説:** <% tp.frontmatter.tools.map(t => `[[${t}]]`).join(", ") %>


_※Templater実行時に `tools` が入力されていれば、自動的にチートシートへのリンクが生成されます。_

### 5.証跡（Evidence）セクションの標準化

ユーザー様の「40インチメイン画面で作業、ピボット画面で記録」というスタイルに合わせ、画像リンクをあらかじめテーブル形式で用意しました。ファイル名を `プロジェクト名_nmap.png` のようにリネームして保存するだけで、即座にドキュメントが完成します。

### 6. プロジェクト識別子（SEC/NET）によるダッシュボード

さらに一歩進めて、`20_Projects` のトップページ等に、**プロジェクト全体の進捗を可視化するダッシュボード**を作成します。

### [SEC] ペンテスト進捗管理ボード


TABLE 
    target_ip as "IP", 
    os as "OS", 
    difficulty as "難易度",
    choice(user_flag, "✅", "❌") as "User",
    choice(root_flag, "✅", "❌") as "Root"
FROM "20_Projects"
WHERE contains(tags, "SEC")
SORT date DESC


---

### 6. ステージマネージャ環境を活かす運用のコツ

ユーザー様の「40インチ＋ピボット24インチ」という環境なら、以下のような使い方が最強です。

1. **40インチ中央（メイン）:** RDPで Kali Linux を操作。
2. **24インチ（ピボット）:** Obsidianを表示。
3. **連携:** - 攻撃中に「あれ、Hydraのオプションなんだっけ？」と思ったら、Raycastで `o hydra` と叩き、Obsidianでチートシートを召喚。
		- そのチートシートの末尾を見れば、**「以前、似たようなターゲットで成功した時の設定」**が Dataview で表示されている。
		- そのままコピーして RDP へペースト。

---

### 次のステップ：さらなる自動化に向けて

この「逆引き」の仕組みが動き出すと、ノートを書くのが「作業」ではなく「資産構築」に変わります。

次は、**「特定のツールのスキャン結果（例：Nmapの .nmap ファイル）を Obsidian にドラッグ＆ドロップした際、自動的に重要なポート情報だけを抽出して整形する」**ような、インフラ屋らしい「自動パース」の仕組みに興味はありますか？

あるいは、**「1週間の学習記録を自動集計して振り返る、週報テンプレート」**の作成に興味がありますか？
```

### Daily-Note-Template

```
<%*
// 昨日の日付と明日の日付を計算（ナビゲーション用）
const yesterday = tp.date.now("YYYY-MM-DD", -1);
const tomorrow = tp.date.now("YYYY-MM-DD", 1);
-%>
---
type: daily
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
	- Daily
---

[[<% yesterday %>|◀ 昨日]] | [[<% tomorrow %>|明日 ▶]]

## [Daily] <% tp.date.now("YYYY-MM-DD (ddd)") %>

### 📅 今日の目標

- [ ] 

### 📝 作業・学習ログ (Time Log)

> [!TIP] `Cmd + Shift + I`（自作ショートカット等）で現在時刻を入れてから書き始めると、後でタイムラインとして振り返りやすくなります。

-  **[Pentest]**
-  **[SEC]**
-  **[NET]**

### 🔗 今日作成・更新したノート

> [!INFO] その日に触ったプロジェクトノートが自動でリストアップされます（Dataview）。


LIST FROM "" WHERE (file.cday = this.file.day OR file.mday = this.file.day) AND type != "daily"


### 🛠 明日への申し送り

- [ ]
```

### Knowledge-Cheatsheet-Template

```
<%*

// 1. カテゴリを選択させる（変数に格納して実行を安定させる）

const categories = ['Git', 'Docker', 'Linux', 'Windows', 'Pentest'];

const category = await tp.system.suggester(categories, categories);

const title = tp.file.title;

-%>

---
type: cheatsheet
category: <% category %>
tags:
	- Knowledge
date: <% tp.date.now("YYYY-MM-DD") %>
---

## [<% tp.frontmatter["category"] %>] <% tp.file.title %>

### 概要

- 

### コマンド / 設定例




### 逆引き・逆引き（➔ 逆引き・解決策 に修正）

- **やりたいこと:** （例：特定のプロセスを強制終了したい）
- **解決策:** `kill -9 <PID>`
- **補足:** ゾンビプロセスには効かない場合がある

### このナレッジを使用した検証ログ

[!INFO] このツール/知識を実際に使用した 20_Projects のログが自動で表示されます。

TABLE target_ip as "ターゲット", status as "状況", date as "実施日"
FROM "20_Projects"
WHERE contains(tools, this.file.name) OR contains(tools, [[<% title %>]])
SORT date DESC

### 関連リンク

- [[_Index_<% tp.frontmatter["category"] %>]]
```
### Pentest-Project-Template

```
<%*
// 1. 起動時に入力を求める (変数に格納することで本文での再利用を確実にする)
const targetIp = await tp.system.prompt("Target IP Address", "10.10.10.");
const targetOs = await tp.system.suggester(["Linux", "Windows", "Other"], ["Linux", "Windows", "Other"]);
const difficulty = await tp.system.suggester(["Easy", "Medium", "Hard", "Insane"], ["Easy", "Medium", "Hard", "Insane"]);
const projectID = "[Pentest]";
-%>
---
type: verification
project: "<% tp.file.title %>"
target_ip: <% targetIp %>
os: <% targetOs %>
difficulty: <% difficulty %>
status: "In Progress"
user_flag: false
root_flag: false
tools: [] # 例: [nmap, gobuster, hydra]
tags:
  - Pentest
  - Draft
date: <% tp.date.now("YYYY-MM-DD") %>
---

## <% projectID %> Project: <% tp.file.title %>

### 1. ターゲット情報

- **IP Address:** <% targetIp %>
- **OS:** <% targetOs %>
- **Difficulty:** <% difficulty %>
- **目的:** ---

### 2. 偵察 (Reconnaissance)

#### チェックリスト
- [ ] **Nmap全ポートスキャン**
- [ ] **Webサービス列挙 (Port 80/443)**
- [ ] **ディレクトリ・ファイル探索 (Gobuster/Feroxbuster)**
- [ ] **脆弱性DB検索 (Searchsploit)**

#### Nmap Scan

基本スキャン
nmap -sV -sC -p- -oN nmap_result.txt <% targetIp %>


#### Web Enumeration (Port 80/443)

---

### 3. 脆弱性分析・潜入 (Exploitation)

- **発見した脆弱性:** - **使用コード/ペイロード:** 

ペイロードや実行コマンドをここに記入

---

### 4. 権限昇格 (Privilege Escalation)

- **Local Recon (linpeas/winpeas):** - **Path to Root:** 


昇格に使用したコマンド

---

### 5. 証跡 (Evidence)

> [!TIP]
> 
> MX Master 4 のアクションで撮影したスクショを 99_Assets に保存し、ファイル名を以下の形式に合わせてリンクさせます。

|**内容**|**スクリーンショット**|
|---|---|
|**Nmap結果**|![[<% tp.file.title %>_nmap.png]]|
|**User Flag**|![[<% tp.file.title %>_user.png]]|
|**Root Flag**|![[<% tp.file.title %>_root.png]]|

---

### 6. 考察・メモ

```


下記の構成のデスクを作成しました

Obsidianをもっと充実させようと思っています

01-infrastructure

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

02-Runbook

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

02-01-APP

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

02-02-Browser

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

02-03VSCode・Obsidian

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
```




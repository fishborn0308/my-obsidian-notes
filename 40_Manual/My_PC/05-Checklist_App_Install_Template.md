## 🍎 Mac アプリ インストールリスト

* [ ] **Homebrew**: iTerm上でインストール

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
echo >> /Users/fishborn0308/.zprofile
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> /Users/fishborn0308/.zprofile
eval "$(/opt/homebrew/bin/brew shellenv)"
```

* [ ] **git**
* [ ] **mas**

```bash
brew install git
brew install mas
```

### 1. Homebrew / 公式サイト（基本ツール）

* [ ] **1Password**: パスワード管理（最優先）
* [ ] **Brave Browser**: メインブラウザ
* [ ] **Dropbox**: クラウドストレージ同期
* [ ] **VScode**: エディタ
* [ ] **Obsidian**: ナレッジベース
* [ ] **iTerm2**: ターミナル
* [ ] **Raycast**: ランチャー
* [ ] **Slack**: コミュニケーション
* [ ] **Notion**: ドキュメント管理
* [ ] **GitHub**: Git管理GUI
* [ ] **Zoom**: ビデオ会議
* [ ] **DeepL:** 翻訳

### 2. インフラ・セキュリティ・ネットワーク

* [ ] **Tailscale**: メッシュVPN
* [ ] **TablePlus**: データベース管理
* [ ] **Little Snitch 6**: ファイアウォール
* [ ] **iStat Menus 7**: システムモニター
* [ ] **CyberChef**: データ解析（Web版でも可）
* [ ] **Microsoft Remote Desktop (Beta)**: RDPクライアント

### 3. ユーティリティ・システム拡張

* [ ] **Karabiner-Elements**: キーボードカスタマイズ
* [ ] **BetterTouchTool**: トラックパッド拡張
* [ ] **Logi Options+**: ロジクールデバイス管理
* [ ] **Sound Source**: オーディオ制御
* [ ] **Popclip**: テキスト選択ポップアップ
* [ ] **Yoink**: ファイルシェルフ
* [ ] **Keka**: 圧縮・解凍
* [ ] **A Better Finder Renamer 12**: リネームツール
* [ ] **ImageOptim**: 画像圧縮
* [ ] **Th-Marker x**: 画像リサイズ・透かし
* [ ] **Eagle**: デザイン・素材管理

### 4. メール・カレンダー

* [ ] **Spark**: メールクライアント
* [ ] **Fantastical**: カレンダー

### 5. Mac App Store (mas)

* [ ] **Be Focused Pro**: ポモドーロタイマー
* [ ] **Kindle**: 電子書籍
* [ ] **Magnet**: ウィンドウ管理

---

## 💻 Windows アプリ インストールリスト

### 1. 仮想化・ネットワーク（最優先）

* [ ] **VMware**: 仮想化基盤
* [ ] **GNS3**: ネットワークシミュレータ
* [ ] **WSL2 (Kali Linux)**: ペネトレーション環境
* [ ] **Wireshark**: パケット解析
* [ ] **MobaXterm**: 統合ターミナル・SSH

### 2. 開発・セキュリティ

* [ ] **Cursor**: AI統合エディタ
* [ ] **Notepad++**: 軽量エディタ
* [ ] **Windows Terminal**: 標準ターミナル
* [ ] **Burp Suite Community**: Web脆弱性診断
* [ ] **HeidiSQL / DBeaver**: DB管理GUI

### 3. ブラウザ・セキュリティ管理

* [ ] **Brave**: メインブラウザ
* [ ] **Firefox**: 診断用ブラウザ（拡張機能セットアップ込み）
* [ ] **1Password**: パスワード管理
* [ ] **Portmaster**: 通信監視・ファイアウォール

### 4. システムユーティリティ

* [ ] **Microsoft PowerToys**: 公式ユーティリティ集
* [ ] **Everything**: 高速ファイル検索
* [ ] **QuickLook**: ファイルプレビュー
* [ ] **EarTrumpet**: 音量ミキサー
* [ ] **NanaZip**: アーカイバ（7-Zipベース）
* [ ] **Logi Options+**: デバイス管理
* [ ] **SnipDo**: テキストポップアップ
* [ ] **Everything**: 高速検索

### 5. ハードウェア・バックアップ

* [ ] **HWinfo64**: ハードウェア診断
* [ ] **Macrium Reflect**: イメージバックアップ作成

---

## 🌐 Webアプリ（ブックマーク登録）

* [ ] **Draw.io**: ネットワーク図作成
* [ ] **Squoosh**: 画像圧縮

---

### 💡 構築時のアドバイス

このチェックリストを **Obsidian** の新規ノートに貼り付け、インストールが完了するごとに `[x]` をつけて管理することをお勧めします。

特に **Mac** においては、まず `mas` を含む **Homebrew** をセットアップし、作成した `check_brew.sh` を回しながら環境を整えていくと、今後「野良アプリ」を増やさずに済み、バックアップ戦略の精度が向上します。

---
created: 2026-01-04 12:38
modified: 2026-02-20 16:14
---
# 🍎 Mac アプリ インストールリスト

* [x] **Homebrew**: iTerm上でインストール

```bash
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
echo >> /Users/fishborn0308/.zprofile
echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> /Users/fishborn0308/.zprofile
eval "$(/opt/homebrew/bin/brew shellenv)"
```

* [x] **git**
* [x] **mas**

```bash
brew install git
brew install mas
```

## 1. Homebrew / 公式サイト（基本ツール）

* [x] **1Password**: パスワード管理（最優先）
* [x] **Brave Browser**: メインブラウザ
* [x] **Dropbox**: クラウドストレージ同期
* [x] **VScode**: エディタ
* [x] **Obsidian**: ナレッジベース
* [x] **iTerm2**: ターミナル
* [x] **Raycast**: ランチャー
* [x] **Slack**: コミュニケーション
* [x] **Notion**: ドキュメント管理
* [x] **GitHub**: Git管理GUI
* [x] **Zoom**: ビデオ会議
* [x] **DeepL:** 翻訳

## 2. インフラ・セキュリティ・ネットワーク

* [x] **Tailscale**: メッシュVPN
* [x] **TablePlus**: データベース管理
* [ ] **Little Snitch 6**: ファイアウォール
* [x] **iStat Menus 7**: システムモニター
* [x] **CyberChef**: データ解析（Web版でも可）
* [x] **Microsoft Remote Desktop (Beta)**: RDPクライアント

## 3. ユーティリティ・システム拡張

* [x] **Karabiner-Elements**: キーボードカスタマイズ
* [x] **BetterTouchTool**: トラックパッド拡張
* [x] **Logi Options+**: ロジクールデバイス管理
* [x] **Sound Source**: オーディオ制御
* [x] **Popclip**: テキスト選択ポップアップ
* [x] **Yoink**: ファイルシェルフ
* [x] **Keka**: 圧縮・解凍
* [x] **A Better Finder Renamer 12**: リネームツール
* [x] **ImageOptim**: 画像圧縮
* [x] **Th-Marker x**: 画像リサイズ・透かし
* [x] **Eagle**: デザイン・素材管理

## 4. メール・カレンダー

* [x] **Spark**: メールクライアント
* [x] **Fantastical**: カレンダー

## 5. Mac App Store (mas)

* [x] **Be Focused Pro**: ポモドーロタイマー
* [x] **Kindle**: 電子書籍
* [x] **Magnet**: ウィンドウ管理

---

# 💻 Windows アプリ インストールリスト

## 1. 仮想化・ネットワーク（最優先）

* [x] **VMware**: 仮想化基盤
* [ ] **GNS3**: ネットワークシミュレータ
* [ ] **WSL2 (Kali Linux)**: ペネトレーション環境
* [x] **Wireshark**: パケット解析
* [x] **MobaXterm**: 統合ターミナル・SSH

## 2. 開発・セキュリティ

* [x] **Cursor**: AI統合エディタ
* [x] **Notepad++**: 軽量エディタ
* [x] **Windows Terminal**: 標準ターミナル
* [ ] **Burp Suite Community**: Web脆弱性診断
* [ ] **HeidiSQL / DBeaver**: DB管理GUI

## 3. ブラウザ・セキュリティ管理

* [x] **Firefox**: 診断用ブラウザ（拡張機能セットアップ込み）
* [x] **Google Chrome: ワカモレ環境へのアクセス用ブラウザ
* [x] **1Password**: パスワード管理
* [x] **Portmaster**: 通信監視・ファイアウォール

## 4. システムユーティリティ

* [x] **Microsoft PowerToys**: 公式ユーティリティ集
* [x] **Everything**: 高速ファイル検索
* [x] **QuickLook**: ファイルプレビュー
* [x] **EarTrumpet**: 音量ミキサー
* [x] **NanaZip**: アーカイバ（7-Zipベース）
* [x] **Logi Options+**: デバイス管理
* [x] **SnipDo**: テキストポップアップ

## 5. ハードウェア・バックアップ

* [x] **HWinfo64**: ハードウェア診断
* [ ] **Macrium Reflect**: イメージバックアップ作成

## 6. コミュニケーション

* [x] **Slack**: コミュニケーション---

# 🌐 Webアプリ（ブックマーク登録）

* [ ] **Draw.io**: ネットワーク図作成
* [ ] **Squoosh**: 画像圧縮

---

## 💡 構築時のアドバイス

このチェックリストを **Obsidian** の新規ノートに貼り付け、インストールが完了するごとに `[x]` をつけて管理することをお勧めします。

特に **Mac** においては、まず `mas` を含む **Homebrew** をセットアップし、作成した `check_brew.sh` を回しながら環境を整えていくと、今後「野良アプリ」を増やさずに済み、バックアップ戦略の精度が向上します。

---
created: 2026-02-20 09:37
modified: 2026-02-20 16:14
environment: [OS/Windows11]
vulnearability: []
runbook_category: Setup_Guide
tools: []
tags: 
---

# Setting & Tips

## install

- notepad++
- Brave
- 1password
- Obsidian

## Obsidian クロスプラットフォーム・インストールガイド

（Windows 11 & Apple Silicon Mac 対応）

### 1. Windows への本体インストール

まずは、Windows環境の土台を作ります。

1. **Obsidian本体:** [公式サイト](https://obsidian.md/)からインストーラーをダウンロードし、実行します。
2. **Git for Windows:** [gitforwindows.org](https://gitforwindows.org/)からインストール。

		- インストール中の選択肢は、基本的にデフォルト（Recommended）でOKです。
				

---

### 2. Git 認証とリポジトリの同期

GitHubにある既存のドキュメントをWindowsへ連れてきます。

1. **Git Config の設定:**

		PowerShellを開き、Macと同じユーザー情報を登録します。
		
		Bash
		
		```

		git config --global user.name "あなたの名前"

		git config --global user.email "your_email@example.com"

		```
		
2. **SSHキーの作成と登録:**

		Bash
		
		```

		ssh-keygen -t ed25519 -C "your_email@example.com"

		# 全てエンターキーで進む

		cat ~/.ssh/id_ed25519.pub

		```
		
		表示された文字列をコピーし、[GitHubのSSH Keys](https://github.com/settings/keys)に登録してください。
		
3. **クローン（ダウンロード）:**

		Bash
		
		```

		cd Documents

		git clone git@github.com:ユーザー名/リポジトリ名.git

		```
		

---

## 3. Obsidian の設定（同期の自動化）

1. Obsidianを起動し、**「Open folder as vault」**でクローンしたフォルダを選択。
2. **Obsidian Git プラグイン**をインストール（Settings ＞ Community plugins）。
3. **推奨設定:**

		- `Vault backup interval (minutes)`: **10**（10分ごとに自動保存）
				
		- `Pull updates on startup`: **ON**（起動時にMacの変更を反映）
				
		- `Push on backup`: **ON**
				

---

## 4. 【重要】秘匿情報の扱い（SANS等の教育資料）

クラウド（Notta等）へのアップロードを避けるため、文字起こしは以下の**ローカル完結型**で行います。

- **Macユーザー:** **MacWhisper** を使用。

		- Appleシリコンのパワーで、データを外に出さず爆速でテキスト化します。
				
- **Windowsユーザー:** **Buzz** または **Whisper Desktop** を使用。

		- ローカルのGPUを使用して処理を完結させます。
				
- **Obsidianへの取り込み:**

		文字起こししたテキストファイルをObsidianの保管庫に入れれば、Gitを通じて安全にMacとWindowsで共有されます。
		

---

## 5. デバイス間での設定の競合回避（オプション）

MacとWindowsでフォントサイズなどを変えたい場合は、リポジトリ直下の `.gitignore` ファイルに以下を追記してください。

Plaintext

```
.obsidian/appearance.json
.obsidian/workspace.json
```

これで、メモの中身だけが同期され、外観設定はデバイスごとに独立します。

---

## 💡 運用のコツ

- **作業開始時:** WindowsでObsidianを開いたら、左下のステータスバーで `git pull` が成功したか一瞬確認する癖をつけると安心です。
- **作業終了時:** Obsidianを閉じる前に少し待つか、コマンドパレット（`Ctrl + P`）から `Git: Commit all changes` を手動実行すると同期漏れが防げます。

**次に、SSHキーの登録で詰まっている箇所や、特定のプラグインの設定方法について詳しく知りたい部分はありますか？**

# File共有

## 1. Windowsの共有フォルダにMacからアクセスする（推奨）

Windows側で特定のフォルダを「共有」設定にし、Macからそれを見に行く方法です。

1. **Windows側の設定:**

		- 共有したいフォルダを右クリック ＞ **[プロパティ]** ＞ **[共有]** タブ。
				
		- **[共有(S)...]** をクリックし、ユーザー（自分自身など）を追加して「読み取り/書き込み」権限を与えます。
				
		- **[設定] ＞ [ネットワークとインターネット] ＞ [状態]** で、自分のIPアドレス（例: `192.168.x.x`）を確認しておきます。
				
2. **Macからの接続:**

		- Finderを開き、メニューバーの **[移動] ＞ [サーバへ接続...]** を選択。
				
		- アドレス欄に `smb://(WindowsのIPアドレス)` を入力して接続。
				
		- Windowsのユーザー名とパスワードを求められるので入力します。
				

---

## 2. Snapdrop を使う（設定不要・ブラウザのみ）

「設定が面倒」「1回だけ数枚の送りたい」という場合に非常に便利です。AppleのAirDropのブラウザ版のようなサービスです。

- **手順:**

		1. WindowsとMacの両方でブラウザを開き、[Snapdrop](https://snapdrop.net/) にアクセスします。
				
		2. 同じネットワークにいれば、お互いのアイコンが表示されます。
				
		3. Windows側でMacのアイコンをクリックしてファイルを選択すれば、即座に送信されます。
				
- **メリット:** インストール不要、OSを問わず使える。

---

## 3. iCloud Drive や Google ドライブを使う

ネットワーク設定に詳しくなくても、クラウドストレージを経由すれば確実に送れます。

- **手順:**

		1. Windowsでブラウザから iCloud.com や Google ドライブにログインし、ファイルをアップロード。
				
		2. Macの Finder（iCloud連携済みの場合）やブラウザからダウンロード。
				
- **注意点:** ファイルサイズが大きい場合、インターネット回線の速度に依存するため時間がかかることがあります。
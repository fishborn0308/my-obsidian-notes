---
created: 2026-02-20 09:37
modified: 2026-02-20 09:52
environment: [OS/Windows11] # 例: [OS/Windows11, Web/Apache2, DB/MySQL]
vulnearability: [] # 例: [SQLi, SSXi, OSCommandi]
runbook_category: Setup_Guide
tools: [] # 例: [nmap, gobuster, hydra]
tags: []
---

# Setting & Tips

## install

- notepad++
- Brave
- 1password
- Obsidian

## File共有

### 1. Windowsの共有フォルダにMacからアクセスする（推奨）

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

### 2. Snapdrop を使う（設定不要・ブラウザのみ）

「設定が面倒」「1回だけ数枚の送りたい」という場合に非常に便利です。AppleのAirDropのブラウザ版のようなサービスです。

- **手順:**

		1. WindowsとMacの両方でブラウザを開き、[Snapdrop](https://snapdrop.net/) にアクセスします。
				
		2. 同じネットワークにいれば、お互いのアイコンが表示されます。
				
		3. Windows側でMacのアイコンをクリックしてファイルを選択すれば、即座に送信されます。
				
- **メリット:** インストール不要、OSを問わず使える。

---

### 3. iCloud Drive や Google ドライブを使う

ネットワーク設定に詳しくなくても、クラウドストレージを経由すれば確実に送れます。

- **手順:**

		1. Windowsでブラウザから iCloud.com や Google ドライブにログインし、ファイルをアップロード。
				
		2. Macの Finder（iCloud連携済みの場合）やブラウザからダウンロード。
				
- **注意点:** ファイルサイズが大きい場合、インターネット回線の速度に依存するため時間がかかることがあります。
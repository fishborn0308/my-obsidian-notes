---
created: 2025-12-28 10:33
modified: 2026-01-24 12:11
tags:
  - Manual
---
# バックアップ

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


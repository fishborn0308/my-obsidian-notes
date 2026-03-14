# OSCP学習環境 構築手順書（プロトタイプ）

## 1. 仮想基盤とOSの基本

| 項目 | 設定内容 |
| --- | --- |
| **プラットフォーム** | **VMware Workstation / Player** (公式推奨の安定性) |
| **イメージ** | [Kali VMware 64-bit (Pre-built)](https://www.google.com/search?q=https://www.kali.org/get-kali/%23kali-virtual-machines) |
| **基本設定** | RAM: 8GB推奨, CPU: 2コア以上, **日本語フォント** (`fonts-noto-cjk`) |
| **ディレクトリ構造** | `~/oscp/` (labs, exam, scripts, tools) を作成して整理 |



## 2. システムの基本更新と日本語化

1. **システム更新:**
```bash
sudo apt update && sudo apt full-upgrade -y

```


2. **日本語フォント（文字化け防止）:**
```bash
sudo apt install -y fonts-noto-cjk

```



## 3. ディレクトリ構造の作成（整理のルール化）

ツールや成果物が散らばらないよう、ホーム直下に専用ディレクトリを作成します。

```bash
mkdir -p ~/oscp/{labs,exam,scripts,tools}

```

* `labs/`: 各攻略マシンの作業用（IPアドレスごとにサブフォルダ作成）
* `exam/`: 試験本番用
* `scripts/`: 自作や編集するスクリプト（LinPeas等）
* `tools/`: `/opt` に入れるまでもない軽量ツール

## 4. ツール管理とPython環境 (`pipx`)

システムを汚さず、どこからでもツールを呼び出せるようにします。

1. **pipxのセットアップ:**
```bash
sudo apt install -y pipx && pipx ensurepath

```

2. **便利なユーティリティ:**
```bash
sudo apt install -y tmux rlwrap seclists curl enum4linux-ng

```


3. **AutoRecon (推奨自動化ツール):**
```bash
# 依存関係のインストール後、pipx等でインストール
sudo apt install -y seclists curl dnsrecon nbtscan nikto nmap onesixtyone oscanner smbclient smbmap sslscan ccze
pipx install git+https://github.com/Tib3rius/AutoRecon.git

```

4. **主要ツールのインストール:**
* `pipx install impacket` (ネットワーク攻撃)
* `pipx install netexec` (AD列挙・攻撃)
* `pipx install updog` (アップロード対応HTTPサーバ)

5. **PrivEsc（権限昇格）スクリプトの配置:**
* `~/oscp/scripts/` に `LinPeas.sh` や `WinPEAS.exe` をダウンロードして配置。



## 5. 効率化カスタマイズ

1. **Tmuxの設定:** `~/.tmux.conf` を作成（マウス操作有効化など）。
2. **エイリアスの設定:** `~/.zshrc` に追記。
```bash
alias pserv="python3 -m http.server 80"
alias mterm="sudo thunar /opt"

```


3. **Burp Suite:** 初期設定ウィザードを済ませ、ブラウザのFoxyProxyを設定。

## 6. ノート・レポート環境

* **Obsidian**（またはCherryTree）をインストール。
* OSCP用のテンプレート（列挙、試行ログ、権限昇格メモ）を読み込む。

---

# OSCP環境構築完了 チェックリスト

新しいPCで環境を作ったら、最後に以下の項目をチェックしてください。

### □ インフラ・基本設定

* [ ] VMwareでKaliが正常に起動し、インターネットに接続できるか？
* [ ] ブラウザやターミナルで日本語が正しく表示されるか？
* [ ] ホストOSとゲストOS間で「コピペ」と「ファイルのドラッグ＆ドロップ」ができるか？

### □ ツール・ディレクトリ

* [ ] `~/oscp/` 配下の4つのディレクトリが存在するか？
* [ ] `AutoRecon` がコマンドラインから起動できるか？
* [ ] `impacket-smbserver` 等の重要スクリプトの場所を把握しているか？
* [ ] `/usr/share/wordlists/rockyou.txt` が解凍されているか？
* `sudo gunzip /usr/share/wordlists/rockyou.txt.gz`



### □ ネットワーク・転送

* [ ] `python3 -m http.server` でファイルを公開し、ターゲット（または別VM）から取得できるか？
* [ ] `rlwrap nc -lvnp` でリバースシェルの待機ができるか？

### □ 記録・バックアップ

* [ ] ノートツール（Obsidian等）で新しいノートが作成・保存できるか？
* [ ] **【最重要】** 全ての設定が終わった状態で、VMの「スナップショット」を作成したか？

---

### アドバイス

この手順書自体をGitHubのプライベートリポジトリやGoogleドライブに置いておくと、どのPCからでも参照できて便利です。また、余裕があれば設定を自動化するシェルスクリプト（`setup.sh`）を自作しておくと、さらに構築時間を短縮できます。

まずはこのリストに、あなたが普段使っている「こだわりの設定」を一つ追加することから始めてみてはいかがでしょうか？
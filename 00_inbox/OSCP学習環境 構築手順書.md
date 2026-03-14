# OSCP学習環境 構築手順書（プロトタイプ）

## 1. 仮想基盤とOSの基本

| 項目 | 設定内容 |
| --- | --- |
| **プラットフォーム** | **VMware Workstation / Player** (公式推奨の安定性) |
| **イメージ** | [Kali VMware 64-bit (Pre-built)](https://www.google.com/search?q=https://www.kali.org/get-kali/%23kali-virtual-machines) |
| **基本設定** | RAM: 8GB推奨, CPU: 2コア以上, **日本語フォント** (`fonts-noto-cjk`) |
| **ディレクトリ構造** | `~/oscp/` (labs, exam, scripts, tools) を作成して整理 |

---

## 2. システムの基本更新と日本語化

1. **システム更新:**
```bash
sudo apt update && sudo apt full-upgrade -y

```


2. **日本語フォント（文字化け防止）**
```bash
sudo apt install -y fonts-noto-cjk

```

3.  **電源管理（スリープ・ロック禁止）**

長時間のスキャンを中断させないための必須設定です。

```bash
gsettings set org.gnome.desktop.screensaver lock-enabled false
gsettings set org.gnome.desktop.session idle-delay 0
xset s off
xset -dpms

```

---

## 3. ディレクトリ構造の作成（整理のルール化）

| ディレクトリ          | 役割                               | 権限 / 性格                            |
| --------------------- | ---------------------------------- | -------------------------------------- |
| **`/opt`**            | システム全体で使う大型ツール       | `root`管理（pipx等でのインストール先） |
| **`~/tools/git`**     | GitHubからクローンしたスクリプト群 | ユーザー権限。`gclone`でここに集約     |
| **`~/tools/scripts`** | ターゲットへ転送する厳選スクリプト | `LinPeas`, `WinPEAS` など実戦用        |
| **`~/labs`**          | 各マシンの攻略ログ・成果物         | マシン名/IPごとのサブフォルダ管理      |
| **`~/logs`**          |     Tmuxからのログを保存        |  Tmuxの自動ログ保存先              |

```bash
mkdir -p ~/{labs,tools/scripts,tools/git}

```

* `logs/`: Tmuxの自動ログ保存先
* `labs/`: 各攻略マシンの作業用（IP or FQDNごとにサブフォルダ作成）
* `tools/`: `/opt` に入れるまでもない軽量ツール
	* `scripts/`: 自作や編集するスクリプト（LinPeas等）
	* `git/`: GitHubからクローンしたスクリプト群

---

## 4. ツール管理とPython環境 (`pipx`)

システムを汚さず、どこからでもツールを呼び出せるようにします。

1. **pipxのセットアップ:**
```bash
sudo apt install -y pipx && pipx ensurepath

```

2. **便利なユーティリティ:**
```bash
sudo apt install -y tmux rlwrap seclists curl enum4linux-ng kitty fonts-jetbrains-mono

sudo update-alternatives --config x-terminal-emulator # Kittyを選択

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


---

## 5. ターミナル & マルチプレクサ設定

### `~/.tmux.conf` (直感操作重視)

```bash
# プレフィックスを Ctrl+a に
set -g prefix C-a
unbind C-b
bind C-a send-prefix

# 【直感分割】 | で縦分割、 - で横分割
bind | split-window -h -c "#{pane_current_path}"
bind - split-window -v -c "#{pane_current_path}"

# マウス有効化・履歴拡張
set -g mouse on
set -g history-limit 10000

# --- ~/.tmux.conf の末尾に追加 ---

# プラグインのリスト
set -g @plugin 'tmux-plugins/tpm'
set -g @plugin 'tmux-plugins/tmux-logging'

# ログの保存先を ~/oscp/logs に変更（ディレクトリは先に作っておく）
set -g @logging-path "~/oscp/logs"

# TPMの初期化（この行は必ず一番下に！）
run '~/.tmux/plugins/tpm/tpm'

```

※初回起動時に `Prefix + I` でプラグインをインストール。

### `~/.zshrc` エッセンス (自動化・効率化)

```bash
# --- ツール連携設定 (Simple & Modern) ---

# fzf (曖昧検索) の初期化：これだけで Ctrl+R などが有効化されます
eval "$(fzf --zsh)"
# インストールスクリプトが生成する標準的な読み込み
# [ -f ~/.fzf.zsh ] && source ~/.fzf.zsh

# zoxide (高速移動) の初期化： z コマンドを有効化
eval "$(zoxide init zsh --cmd z)"

# zsh拡張 (パッケージで入れたもの)
source /usr/share/zsh-autosuggestions/zsh-autosuggestions.zsh
source /usr/share/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh

# ターゲット設定関数 (Hosts自動追記・タグ付き)
target() {
    export ip="$1" target="$1"
    local tag="# OSCP_TARGET"
    if [ -n "$2" ]; then
        export fqdn="$2"
        sudo sed -i "/ $fqdn/d; /^$ip /d" /etc/hosts
        echo "$ip $fqdn $tag" | sudo tee -a /etc/hosts > /dev/null
    fi
    echo "[+] Target: $ip ($fqdn)"
    getent hosts "${fqdn:-$ip}"
}

# 一括クリーンアップ
target_clear() {
    sudo sed -i "/# OSCP_TARGET/d" /etc/hosts
    unset ip target fqdn
    echo "[!] Environment & Hosts cleared."
}

# メンテナンス・便利エイリアス
alias gclone='cd ~/tools/git && git clone'
alias update-tools='find ~/tools/git -maxdepth 2 -name .git -type d -execdir git pull \;'
alias pserv="python3 -m http.server 80"
alias udot="updog -p 80"
alias icat="kitty +kitten icat"

```

### `~/.config/kitty/kitty.conf` (視認性重視)

```conf
# --- フォント設定 ---
# 視認性抜群の JetBrains Mono か Fira Code の Nerd Font 版がおすすめ
font_family      JetBrainsMono Nerd Font
bold_font        auto
italic_font      auto
bold_italic_font auto
font_size        12.0

# --- スクロールバック (OSCPでは命) ---
# ログが消えないよう多めに設定。記憶力が足りなくなったらファイルへ
scrollback_lines 20000

# --- ベル設定 (集中力を削がない) ---
enable_audio_bell no
visual_bell_duration 0.0

# --- ウィンドウ・デザイン ---
# わずかな透過は、裏の資料をチラ見するのに便利
background_opacity 0.92
# ウィンドウの余白（少しあると見やすい）
window_padding_width 4

# --- コピペの挙動 ---
# 選択するだけでコピー（Puttyスタイル。慣れると爆速です）
copy_on_select yes
# クリップボードとの同期
strip_trailing_spaces smart

# --- パフォーマンス ---
# 遅延を最小限に
repaint_delay 10
input_delay 3
sync_to_monitor yes

# --- 色設定 (Theme) ---
# 結局「Gruvbox Dark」か「Nord」が一番目が疲れません
# ここでは目に優しい Gruvbox 系の色味を推奨
foreground            #ebdbb2
background            #282828
selection_foreground  #282828
selection_background  #ebdbb2

```

---



---

3. **Burp Suite:** 初期設定ウィザードを済ませ、ブラウザのFoxyProxyを設定。

## 6. ノート・レポート環境

* **Obsidian**（またはCherryTree）をインストール。
* OSCP用のテンプレート（列挙、試行ログ、権限昇格メモ）を読み込む。




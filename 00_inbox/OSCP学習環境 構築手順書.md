# OSCP学習環境 構築手順書（プロトタイプ）

## 1. 仮想基盤とOSの基本

| 項目 | 設定内容 |
| --- | --- |
| **プラットフォーム** | **VMware Workstation / Player** (公式推奨の安定性) |
| **イメージ** | [Kali VMware 64-bit (Pre-built)](https://www.google.com/search?q=https://www.kali.org/get-kali/%23kali-virtual-machines) |
| **基本設定** | RAM: 8GB推奨, CPU: 2コア以上, グラフィックメモリ: 2GB, 3Dアクセラレータあり **日本語フォント** (`fonts-noto-cjk`) |
| **ディレクトリ構造** | `~/oscp/` (labs, exam, scripts, tools) を作成して整理 |

---

## 2. システムの基本更新と日本語化

1. **システム更新:**
```bash
# パッケージの更新と不要な依存関係の削除、キャッシュの整理を一括で行う
sudo apt update && sudo apt full-upgrade -y && sudo apt autoremove -y && sudo apt autoclean -y
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
mkdir -p ~/{labs,logs,tools/scripts,tools/git}
# configファイルの作成
mkdir -p ~/.config/kitty
touch ~/.config/kitty/kitty.conf
touch ~/.tmux.conf
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
sudo apt install -y tmux rlwrap seclists curl enum4linux-ng flameshot

```

```bash
sudo apt update
sudo apt install -y kitty tmux zoxide fzf \
    zsh-autosuggestions zsh-syntax-highlighting \
    fonts-jetbrains-mono
    
git clone https://github.com/tmux-plugins/tpm ~/.tmux/plugins/tpm

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
* `pipx install updog` (アップロード対応HTTPサーバ)

5. **PrivEsc（権限昇格）スクリプトの配置:**
* `~/oscp/scripts/` に `LinPeas.sh` や `WinPEAS.exe` をダウンロードして配置。


---

## 5. ターミナル & マルチプレクサ & スクリーンショット設定

### スクリーンショット（Flameshot）の設定

OSCPのレポートには「実行したコマンド」と「結果」の証拠が不可欠です。

1. **起動確認:**
ターミナルで `flameshot gui` を実行し、範囲選択ができるか確認します。
2. **ショートカット登録 (重要):**
Kaliの [Settings] > [Keyboard] > [Application Shortcuts] から、以下のショートカットを登録すると効率が劇的に上がります。
* **Command:** `flameshot gui`
* **Shortcut:** `Print` キー（または好みのキー）


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

sed -i -e "s/^alias ll='ls -l'/alias ll='ls -alF'/" \
       -e "s/^HISTSIZE=1000$/HISTSIZE=10000/" \
       -e "s/^SAVEHIST=2000$/SAVEHIST=10000/" ~/.zshrc

# 書き換え内容の確認
grep -E "alias ll|HISTSIZE|SAVEHIST" ~/.zshrc

# 設定の反映
source ~/.zshrc
```

```bash
vim ~/.zshrc

# 設定の反映
source ~/.zshrc

```


```bash

# 履歴設定 (過去のコマンドを数万件保持)
setopt HIST_IGNORE_ALL_DUPS  # 重複を記録しない
setopt SHARE_HISTORY         # 別ウィンドウの履歴も即座に共有

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
    if [ -z "$1" ]; then
        echo "Usage: target <IP> [FQDN]"
        return 1
    fi

    export ip="$1"
    export target="$1"
    local tag="# OSCP_TARGET"
    
    # --- 【追加】ディレクトリ自動作成 & 移動 ---
    # ~/oscp/labs/10.10.10.101 のようなディレクトリを作る
    export workdir="$HOME/oscp/labs/$ip"
    mkdir -p "$workdir"
    cd "$workdir"
    # -----------------------------------------

    if [ -n "$2" ]; then
        export fqdn="$2"
        sudo sed -i "/ $fqdn/d; /^$ip /d" /etc/hosts
        echo "$ip $fqdn $tag" | sudo tee -a /etc/hosts > /dev/null
    fi

    echo "[+] Target: $ip (${fqdn:-no fqdn})"
    echo "[+] Switched to: $workdir"
    getent hosts "${fqdn:-$ip}"
}

# 一括クリーンアップ
target_clear() {
    sudo sed -i "/# OSCP_TARGET/d" /etc/hosts
    unset ip target fqdn
    echo "[!] Environment & Hosts cleared."
}

# プロンプトの右側にターゲット情報を表示する設定
# IPが設定されている時だけ、赤文字で [T: IP (FQDN)] と表示します
set_rprompt() {
    if [ -n "$ip" ]; then
        if [ -n "$fqdn" ]; then
            echo "%F{red}[T: $ip ($fqdn)]%f"
        else
            echo "%F{red}[T: $ip]%f"
        fi
    else
        echo ""
    fi
}

# プロンプトが表示されるたびに上記関数を呼び出す
setopt prompt_subst
RPROMPT='$(set_rprompt)'

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



## トラブルシューティング・ガイド

### 1. リポジトリ・アップデートの失敗

**症状:** `apt update` を実行すると "Repository changed its 'Suite' value" や GPGエラーが出て更新できない。
**原因:** 古いイメージのリポジトリ情報や署名キーが期限切れ、または構造が変わっているため。

**解決コマンド:**

```bash
# リリース情報の変更を明示的に許可して更新
sudo apt update --allow-releaseinfo-change

# 署名キーのエラーが出る場合（最新のキーリングを導入）
wget -q -O - https://archive.kali.org/archive-key.asc | sudo apt-key add

# 標準の sources.list を強制再生成（中身が壊れている場合）
echo "deb http://http.kali.org/kali kali-rolling main contrib non-free non-free-firmware" | sudo tee /etc/hosts /etc/apt/sources.list

```


### 2. マウスカーソルが消失する

**症状:** VMware環境で、ゲストOS内にマウスカーソルが表示されなくなる。
**原因:** VMwareの**「仮想マシンの互換性（Hardware Compatibility）」**が古い

#### 仮想ハードウェアのアップグレード手順

1. **Kali Linuxを完全にシャットダウン**します（サスペンド状態では設定変更できません）。
2. VMwareのライブラリ（左側のリスト）でKali Linuxを**右クリック**します。
3. **[管理]** ＞ **[ハードウェア互換性のアップグレード...]** を選択します。
* ※VMware Player（無償版）の場合は、[仮想マシン設定の編集] ＞ [オプション] タブ ＞ [全般] ＞ [構成ファイルのアップグレード] または右下の [互換性] から変更できる場合があります。

1. ウィザードが表示されるので、**最新のバージョン**（例：Workstation 17.x など）を選択して進めます。
2. 「この仮想マシンのバックアップ（クローン）を作成しますか？」と聞かれたら、念のため作成しておくことをおすすめします。
3. 完了後、仮想マシンを起動してマウスが動くか確認してください。

### 3. マウスカーソルが消失する

**症状:** VMware環境で、ゲストOS内にマウスカーソルが表示されなくなる。
**原因:** VMware Toolsの不整合、またはビデオアクセラレーションの競合。

**解決策:**

* **方法A（即時）:** `Ctrl + Alt` で一度ホストにマウスを戻し、再度クリックしてゲストに入る。
* **方法B（設定変更）:** VMをシャットダウンし、[設定] > [ディスプレイ] > **「3Dグラフィックスのアクセラレーション」をオフ**にする。
* **方法C（サービス再起動）:** ターミナルで以下を叩く（画面操作が効かない場合は `Ctrl+Alt+T` でKittyを開き入力）。
```bash
sudo systemctl restart lightdm

```
* **方法D (VMware Tools (open-vm-tools) の再インストール)
```bash
sudo apt update
sudo apt install --reinstall upgrade open-vm-tools-desktop
sudo reboot

```
* **方法E マウスのゲーム設定を変更する**
	1. VMware Workstation / Player の **[編集]** ＞ **[環境設定]** を開きます。
	2. **[入力]** タブを選択します。
	3. **「ゲーム用にマウスを最適化」** という項目を **[常に使用しない]** に変更します。
* **方法F USBコントローラーの互換性変更**
	1. 仮想マシンの設定から **[USB コントローラ]** を選択します。
	2. USB互換性を **[USB 3.1]**（または利用可能な最新のもの）に変更して保存します。


### 4. ディスク容量不足（ログによる圧迫）

**症状:** ツールが突然クラッシュする、あるいは保存ができなくなる。
**原因:** `tmux-logging` やスキャン結果でディスクが一杯になった。

**解決コマンド:**

```bash
# どこが容量を食っているか確認
df -h
du -sh ~/oscp/logs/* | sort -rh | head -n 5

# 不要なキャッシュの削除
sudo apt clean

```

### 5. ネットワークアダプタの不調

**症状:** `target` に ping が飛ばない、VPNは繋がっているのに通信できない。
**原因:** VMwareの仮想アダプタ（vmnet）のハングアップ。

**解決策:**

* **ホスト側:** VMwareの「Virtual Network Editor」で [Restore Defaults] を実行。
* **ゲスト側:** ネットワークマネージャーの再起動。
```bash
sudo systemctl restart NetworkManager

```


### 6. apt upgradeが止まってしまう

**症状:** `apt update` が途中で止まってしまう
**原因:** 古い壁紙ファイルと新しいテーマファイルの構成がぶつかってしまうなど依存関係の競合による

#### 1. 壊れた依存関係を強制的に修復する

まずは、エラーで中途半端になっているインストール状態を解消します。

```bash
sudo apt --fix-broken install

```

これで解決しない場合は、以下の手順へ進んでください。

#### 2. 問題のパッケージを一度削除する

壁紙やテーマのパッケージは、システム自体の動作には影響しません。一度削除して、アップデート後に再インストールするのが一番確実です。

```bash
# 問題になりやすいパッケージを強制排除
sudo dpkg --purge --force-depends kali-wallpapers-legacy
sudo dpkg --purge --force-depends kali-themes-common

```

#### 3. 未完了の構成を強制適用

削除したあと、残っている設定を正常化させます。

```bash
sudo dpkg --configure -a

```

#### 4. アップデートを再開する

これで邪魔なパッケージがいなくなったので、通常のアップデートが通るようになります。

```bash
sudo apt update
sudo apt full-upgrade -y

```

#### 5. （必要であれば）壁紙を入れ直す

最新版の壁紙やテーマをインストールし直します。これで最新の構成で上書きされます。

```bash
sudo apt install kali-linux-large

```

※ `kali-linux-large` を入れると、標準的な壁紙やツール一式が最新の状態で揃います。


---
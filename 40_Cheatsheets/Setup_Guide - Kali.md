# Kali Setup

## 仮想基盤とOSの基本

| 項目 | 設定内容 |
| --- | --- |
| **プラットフォーム** | **VMware Workstation / Player** (公式推奨の安定性) |
| **イメージ** | Kali VMware 64-bit|
| **基本設定** | RAM: 8GB推奨, CPU: 2コア以上, グラフィックメモリ: 2GB, 3Dアクセラレータあり **日本語フォント** (`fonts-noto-cjk`) |

---

## システムの基本更新と日本語化

1. **システム更新:**
```zsh
# パッケージの更新と不要な依存関係の削除、キャッシュの整理を一括で行う
sudo apt update && sudo apt full-upgrade -y
sudo apt autoremove -y && sudo apt autoclean -y
```

2. **日本語フォント（文字化け防止）**
```zsh
sudo apt install -y fonts-noto-cjk

```

3.  **電源管理（スリープ・ロック禁止）**

長時間のスキャンを中断させないための必須設定です。

```zsh
gsettings set org.gnome.desktop.screensaver lock-enabled false
gsettings set org.gnome.desktop.session idle-delay 0
xset s off -dpms s noblank

```

4. **自動サスペンドOFF**

```zsh
sudo systemctl mask sleep.target suspend.target hibernate.target hybrid-sleep.target
```

---

## ディレクトリ構造の作成（整理のルール化）

| カテゴリ   | ディレクトリパス                                                    | 用途                                  |
| :----- | :---------------------------------------------------------- | :---------------------------------- |
| **記録** | `~/Vault/Target/$ip/{assets,result,log}`                    | Obsidian管理。スクショ、Nmap結果、tmuxログ       |
| **倉庫** | `~/Tools/{Git,Python,C#,Powershell,Bin}`                    | オリジナルツールの保管庫。ここからコピーして使う            |
| **作業** | `~/Workbench/{Recon,AD,Web,Exploit,Wordlists,Verification}` | Kaliローカルで実行するツール群                   |
| **配送** | `~/Transfer/{RevShell,PrivEsc,PostEx,Pivoting}`             | ターゲットへ送る用。内部に **Linux/Windows** を作成 |


```zsh
mkdir -p ~/Vault/Target \
         ~/Tools/{Git,Python,Powershell,Shell,Bin} \
         ~/Workbench/{Recon,AD/enumeration,AD/attacks,Web/sqli,Exploit,Wordlists,Verification} \
         ~/Transfer/{RevShell,PrivEsc,PostEx,Pivoting}/{Linux,Windows}

# 確認用
ls -R ~/Vault ~/Tools ~/Workbench ~/Transfer

```

---

## pipxのインストール

```bash
sudo apt install -y pipx && pipx ensurepath

```

## Docker

### インストール

```zsh
#  システム更新
sudo apt update && sudo apt upgrade -y

sudo apt install -y docker.io
sudo systemctl enable docker --now
sudo usermod -aG docker $USER
newgrp docker

docker --version
docker run hello-world

# Compose確認
docker compose version
```

- `docker-compose` は旧式

---
### よくあるトラブル

#### 権限エラー

```bash
permission denied ...
```

→ dockerグループ追加 or 再ログイン

---

#### Dockerが起動しない

```bash
sudo systemctl restart docker
sudo systemctl status docker
```

---

## ターミナルのカスタマイズ

### shellの確認

shellの確認
```zsh
echo $SHELL
```

#### bashだった場合 (/etc/passwdは/bin/zshに切り替わるがターミナルはbashのままだった)

zshに切り替え(一時的)
```zsh
chsh -s /bin/zsh
zsh
```
zshに切り替え(永続的)
```zsh
sudo chsh -s /bin/zsh kali
sudo reboot
```

### ターミナルの変更

```zsh
sudo apt update
sudo apt install kitty -y
# Kitty を選択肢としてシステムに登録
sudo update-alternatives --install /usr/bin/x-terminal-emulator x-terminal-emulator /usr/bin/kitty 50
# 番号のリストが表示されるので、kitty の番号を入力して Enter
sudo update-alternatives --config x-terminal-emulator
# fontのインストール
gclone https://github.com/ryanoasis/nerd-fonts.git ~/Tools/Git/nerd-fonts
cd ~/Tools/Git/nerd-fonts

# フォントのインストール
mkdir -p ~/.local/share/fonts
cd /tmp
wget https://github.com/ryanoasis/nerd-fonts/releases/latest/download/JetBrainsMono.zip
unzip JetBrainsMono.zip -d JetBrainsMono
cp JetBrainsMono/*.ttf ~/.local/share/fonts/
fc-cache -fv
fc-list | grep "JetBrainsMono"

```

- Windows:[.config/kitty]()
- Mac:[.config/kitty](vscode://file/Users/fishborn0308/Documents/work/VSCode/40_Configs/kitty.conf)

```zsh
mkdir -p ~/.config/kitty
cat << 'EOF' > ~/.config/kitty/kitty.conf
# ペースト
shell /bin/zsh

font_family JetBrainsMono Nerd Font
font_size 11.0
window_padding_width 5

tab_bar_edge top
tab_bar_style powerline

foreground #ebdbb2
background #282828
selection_foreground #282828
selection_background #ebdbb2

scrollback_lines 20000

enable_audio_bell no
visual_bell_duration 0.0

input_delay 3
repaint_delay 10
sync_to_monitor yes

copy_on_select yes
strip_trailing_spaces smart

terminal_select_modifiers alt

EOF
```

### shellの拡張機能のインストール

#### **zoxide**

```zsh
sudo apt install zoxide
# ~/.zshrcに反映（個別で実施する場合）
# echo 'eval "$(zoxide init zsh)"' >> ~/.zshrc
# source ~/.zshrc
# 使い方: z oscp  (これで ~/Documents/OSCP/work/ に飛んだりできる)
# ※一度移動する必要あり
# 使い方: zi   fzf を使って、記憶されたディレクトリの一覧からインタラクティブに選択して移動
```

#### **fzf**

```zsh
sudo apt install fzf
# ~/.zshrcに反映（個別で実施する場合）
# echo 'source <(fzf --zsh)' >> ~/.zshrc
# source ~/.zshrc
# fzf のプレビュー機能を有効化（ファイルの中身を見ながら検索）
export FZF_DEFAULT_OPTS="--height 40% --layout=reverse --border --preview 'cat {} | head -50'"
# 履歴検索 (Ctrl+R) のときもプレビューを出す（打ったコマンドの全体像が見える）
export FZF_CTRL_R_OPTS="--preview 'echo {}' --preview-window down:3:hidden:wrap --bind '?:toggle-preview'"
# 使い方: Ctrl + R: コマンド履歴を検索
# 使い方: Alt + C: フォルダ（ディレクトリ）を曖昧検索して移動
```

#### **Zshプラグイン:** `zsh-autosuggestions` と `zsh-syntax-highlighting` を有効化。

```zsh
sudo apt install zsh-autosuggestions
sudo apt install zsh-syntax-highlighting
# ~/.zshrcに反映（個別で実施する場合）
# echo "source /usr/share/zsh-autosuggestions/zsh-autosuggestions.zsh" >> ~/.zshrc
# echo "source /usr/share/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh" >> ~/.zshrc
# source ~/.zshrc
```

#### ~/.zshrcの編集

##### alias llとHISTSIZEとSAVEHISTの編集

```zsh

sed -i -e "s/^alias ll='ls -l'/alias ll='ls -alF'/" \
       -e "s/^HISTSIZE=1000$/HISTSIZE=10000/" \
       -e "s/^SAVEHIST=2000$/SAVEHIST=10000/" ~/.zshrc

# 書き換え内容の確認
grep -E "alias ll|HISTSIZE|SAVEHIST" ~/.zshrc

# 設定の反映
source ~/.zshrc
```

~/.zshrcに追記

- Windows:[.zshrc]()
- Mac:[.zshrc](vscode://file/Users/fishborn0308/Documents/work/VSCode/40_Configs/.zshrc)


```zsh
cat << 'EOF' >> ~/.zshrc
# ペースト
# ----------------------------------------
# History
# ----------------------------------------
setopt HIST_IGNORE_ALL_DUPS
setopt SHARE_HISTORY

# ----------------------------------------
# Plugins / Tools
# ----------------------------------------
source /usr/share/zsh-autosuggestions/zsh-autosuggestions.zsh
source /usr/share/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
source <(fzf --zsh)
eval "$(zoxide init zsh)"

export FZF_DEFAULT_OPTS="--height 40% --layout=reverse --border --preview 'head -50 {} 2>/dev/null'"
export FZF_CTRL_R_OPTS="--preview 'echo {}' --preview-window down:3:hidden:wrap --bind '?:toggle-preview'"

# ----------------------------------------
# Aliases
# ----------------------------------------
alias ll='ls -alF'
alias gclone='cd ~/tools/git && git clone'
alias gup='find ~/Tools/Git -maxdepth 2 -name .git -type d -execdir git pull --rebase \;'
alias maintenance='sudo apt update && sudo apt full-upgrade -y && gup && pipx upgrade-all'
alias pserv='python3 -m http.server 8000'
alias pserv80='sudo python3 -m http.server 80'
alias udot='updog -p 80'
alias icat='kitty +kitten icat'
alias vpnip="ip -br -4 a show tun0 | awk '{print \$3}' | cut -d/ -f1"

# ----------------------------------------
# Paths
# ----------------------------------------
export WORDLISTS="$HOME/Workbench/Wordlists"
export USERS="$WORDLISTS/Usernames"
export PASSES="$WORDLISTS/Passwords"
export CREDS="$WORDLISTS/Credentials"
export DISCOVERY="$WORDLISTS/Discovery"
export ROCKYOU="$WORDLISTS/Passwords/rockyou.txt"
export SECLISTS="/usr/share/seclists"

alias cdword='cd $WORDLISTS'
alias cdusers='cd $USERS'
alias cdpass='cd $PASSES'
alias cdcreds='cd $CREDS'
alias cddisc='cd $DISCOVERY'
alias cdseclists='cd $SECLISTS'

# ----------------------------------------
# Prompt helpers
# ----------------------------------------
refresh_oscp_prompt() {
  local my_ip
  my_ip=$(ip -br -4 a show tun0 2>/dev/null | awk '{print $3}' | cut -d/ -f1)
  [ -z "$my_ip" ] && my_ip=$(ip -br -4 a show eth0 2>/dev/null | awk '{print $3}' | cut -d/ -f1)
  CURRENT_MY_IP="${my_ip:-N/A}"

  if [ -n "$TARGET_IP" ]; then
    if [ -n "$TARGET_NAME" ]; then
      TARGET_STATUS="%F{red}[T: $TARGET_IP ($TARGET_NAME)]%f"
    else
      TARGET_STATUS="%F{red}[T: $TARGET_IP]%f"
    fi
  else
    TARGET_STATUS=""
  fi
}

autoload -Uz add-zsh-hook
add-zsh-hook precmd refresh_oscp_prompt

setopt prompt_subst
PROMPT='%F{cyan}[L: ${CURRENT_MY_IP}]%f %F{blue}%~%f %# '
RPROMPT='${TARGET_STATUS}'

# ----------------------------------------
# Target management
# ----------------------------------------
target() {
  local ip="$1"
  local name="$2"
  local vault_base="$HOME/Vault/Target"
  local current_link="$vault_base/current_assets"

  if [ -z "$ip" ]; then
    export TARGET_IP=""
    export TARGET_NAME=""
    export SCREENSHOT_DIR=""
    export TARGET_DIR=""
    export OUT=""
    export LOG=""
    export ASSETS=""
    echo "ターゲット指定を解除しました。"
    return
  fi

  local target_dir="$vault_base/$ip"
  mkdir -p "$target_dir/assets" "$target_dir/result/autorecon" "$target_dir/log"
  touch $target_dir/$ip.md
  
  export TARGET_IP="$ip"
  export TARGET_NAME="$name"
  export workdir="$target_dir"
  export TARGET_DIR="$target_dir"
  export OUT="$target_dir/result"
  export LOG="$target_dir/log"
  export ASSETS="$target_dir/assets"

  local tag="# OSCP_TARGET"
  if [ -n "$name" ]; then
    sudo sed -i "\| $name$tag|d; \|^$ip |d" /etc/hosts
    echo "$ip $name $tag" | sudo tee -a /etc/hosts > /dev/null
  fi

  ln -sfn "$target_dir/assets" "$current_link"
  export SCREENSHOT_DIR="$current_link"

  if [ -n "$TMUX" ]; then
    tmux set-environment -g TARGET_IP "$ip"
    tmux set-environment -g TARGET_NAME "$name"
    tmux set-environment -g LOG_PATH "$target_dir/log"
  fi

  cd "$target_dir" || return

  echo "----------------------------------------"
  echo "Target Set & Moved to $target_dir"
  echo "IP   : $TARGET_IP"
  echo "FQDN : ${TARGET_NAME:-N/A}"
  echo "----------------------------------------"
}

targetcl() {
  sudo sed -i '/# OSCP_TARGET/d' /etc/hosts
  export TARGET_IP=""
  export TARGET_NAME=""
  export SCREENSHOT_DIR=""
  export TARGET_DIR=""
  export OUT=""
  export LOG=""
  export ASSETS=""

  if [ -n "$TMUX" ]; then
    tmux set-environment -gu TARGET_IP
    tmux set-environment -gu TARGET_NAME
    tmux set-environment -gu LOG_PATH
  fi

  echo "[!] Target cleared."
}

# ----------------------------------------
# Search helpers
# ----------------------------------------
vault-grep() {
  grep -r --color=always -E "$1" ~/Vault/Target/
}

getlist() {
  local link_name="$WORDLISTS/wordlist"

  local selected_list
  selected_list=$(
    find "$WORDLISTS" "$SECLISTS" -maxdepth 4 \( -type f -o -type l \) \
      ! -path "$link_name" 2>/dev/null | fzf \
      --prompt="Select Wordlist > " \
      --preview='
        target=$(readlink -f "{}" 2>/dev/null || echo "{}")
        echo "[ PATH: {} ]"
        echo "[ REAL: $target ]"
        echo "[ SIZE: $(wc -l < "$target" 2>/dev/null) lines ]"
        echo "------------------"
        head -n 15 "$target" 2>/dev/null
      '
  )

  if [ -n "$selected_list" ]; then
    local resolved
    resolved=$(readlink -f "$selected_list" 2>/dev/null || echo "$selected_list")

    ln -sfn "$resolved" "$link_name"
    export ACTIVE_DICT
    ACTIVE_DICT=$(basename "$selected_list")

    echo "[+] Linked: $resolved -> $link_name"
    echo "[*] Exported: ACTIVE_DICT=$ACTIVE_DICT"
  else
    echo "No list selected."
  fi
}

# ----------------------------------------
# Ligolo / Chisel
# ----------------------------------------
setup_ligolo() {
  sudo ip tuntap add user $USER mode tun ligolo
  sudo ip link set ligolo up
  echo "[+] TUN interface 'ligolo' is UP."
}

alias chisel_srv='~/Tools/Bin/chisel server -p 8080 --reverse'

# ----------------------------------------
# Logging helper
# ----------------------------------------
alias l2o='~/Tools/Bin/log2obsidian.sh'
EOF
```

### **tmux**のインストール

```zsh
sudo apt install tmux
```

#### **Tmux プラグインマネージャー (TPM) の導入**

* **インストール:**

```zsh
git clone https://github.com/tmux-plugins/tpm ~/.tmux/plugins/tpm
```

#### `~/.tmux.conf` の作成

- Windows:[.tmux.conf]()
- Mac:[.tmux.conf](vscode://file/Users/fishborn0308/Documents/work/VSCode/40_Configs/.tmux.conf)

```zsh
cat << 'EOF' > ~/.tmux.conf
# ペースト
# ----------------------------------------
# Shell
# ----------------------------------------
set-option -g default-shell /bin/zsh

# ----------------------------------------
# Basic
# ----------------------------------------
set -g mouse on
set -g history-limit 50000
set -g display-time 4000
set -g status-interval 5

# ----------------------------------------
# Prefix
# ----------------------------------------
set -g prefix C-a
unbind C-b
bind C-a send-prefix

# ----------------------------------------
# Splits
# ----------------------------------------
bind | split-window -h -c "#{pane_current_path}"
bind - split-window -v -c "#{pane_current_path}"

# ----------------------------------------
# Reload
# ----------------------------------------
bind r source-file ~/.tmux.conf \; display "Reloaded!"

# ----------------------------------------
# Logging path refresh
# ----------------------------------------
bind L run-shell 'tmux show-environment -g LOG_PATH >/dev/null 2>&1 && tmux display "LOG_PATH is set" || tmux display "LOG_PATH is not set"'

# ----------------------------------------
# Status bar
# ----------------------------------------
set -g status-bg black
set -g status-fg white
set -g status-left "#[fg=green]#S "
set -g status-right "#[fg=yellow]%Y-%m-%d %H:%M:%S"

# ----------------------------------------
# Plugins
# ----------------------------------------
set -g @plugin 'tmux-plugins/tpm'
set -g @plugin 'tmux-plugins/tmux-logging'

# ログ保存先の初期値
set -g @logging-path "$HOME/Vault/Target/_tmp/log"

# ----------------------------------------
# TPM
# ----------------------------------------
run '~/.tmux/plugins/tpm/tpm'
EOF

```

#### **拡張機能の反映**

**Tmux内で `Prefix (Ctrl+a)` -> `I` (大文字) を押すとインストール**

## スクリーンショットのカスタマイズ

### **flameshot**

* **General**
* Save image after copy

* 保存ファイル名の自動化（超重要）

  デフォルトでは `screenshot.png` のような名前になりますが、これを**「日時」**に変更することで、作業ログ（Obsidian等）との時系列が一致しやすくなります。

  * **設定方法:** `Flameshot Config` > `Filename Editor`
  * **おすすめ設定:** `%Y-%m-%d_%H-%M-%S`
  * これで `2026-03-13_16-00-00.png` のように保存され、証拠の整理が劇的に楽になります。

* インターフェースとツールの最適化

  試験中は「矢印」と「ぼかし（隠蔽）」、「テキスト」を多用します。

  * **UI設定:** `General` > `Show help message` をオフにする（邪魔なため）。
  * **ボタンの選択:** `Interface` タブで、以下のツールだけを表示するように絞ると、ツールバーがスッキリします。
    * **矢印 (Arrow):** 注目ポイントを指す。
    * **矩形 (Rectanglar Selection):** 重要な文字列を囲む。
    * **ぼかし (Pixelate):** VPNのIPやパスワードなど、報告書に載せたくない情報を隠す。
    * **テキスト (Text):** 補足説明を入れる。
    * **保存 (Save) & コピー (Copy to Clipboard):** クリップボードコピーをメインにするとObsidianへの貼り付けが速いです。

* ショートカットキーの割り当て

  OSCPでは「キャプチャを撮る」動作を何百回も繰り返します。マウス操作ではなくキーボード一発で起動させましょう。

1. **起動確認:**
ターミナルで `flameshot gui` を実行し、範囲選択ができるか確認します。
2. **ショートカット登録 (重要):**
Kaliの [Settings] > [Keyboard] > [Application Shortcuts] から、以下のショートカットを登録すると効率が劇的に上がります。
* **Command:** `flameshot gui`
* **Shortcut:** `Print` キー（または好みのキー）

## Workbench

### ~/Workbench/Wordlists

```zsh
# ディレクトリ作成
mkdir -p ~/Workbench/Wordlists/{Discovery,Usernames,Passwords,Credentials}

# rockyou をコピーして解凍（OS標準の場所を汚さない）
cp /usr/share/wordlists/rockyou.txt.gz ~/Workbench/Wordlists/Passwords/
gunzip ~/Workbench/Wordlists/Passwords/rockyou.txt.gz
```

Kaliにプリインストールされている膨大なリストの中から、主要なものを選択

```zsh
export WORDLISTS="$HOME/Workbench/Wordlists"
export USERS="$WORDLISTS/Usernames"
export PASSES="$WORDLISTS/Passwords"
export CREDS="$WORDLISTS/Credentials"
export DISCOVERY="$WORDLISTS/Discovery"
export ROCKYOU="$WORDLISTS/Passwords/rockyou.txt"
export SECLISTS="/usr/share/seclists"

ln -sfn $SECLISTS/Discovery/Web-Content/common.txt $DISCOVERY/web-common.txt
ln -sfn $SECLISTS/Discovery/Web-Content/DirBuster-2007_directory-list-2.3-medium.txt $DISCOVERY/dir-medium.txt
ln -sfn $SECLISTS/Discovery/Web-Content/raft-medium-files.txt $DISCOVERY/raft-files.txt
ln -sfn $SECLISTS/Discovery/Web-Content/raft-medium-directories.txt $DISCOVERY/raft-dir.txt
ln -sfn $SECLISTS/Discovery/DNS/subdomains-top1million-110000.txt $DISCOVERY/dns-110k.txt
ln -sfn $SECLISTS/Discovery/DNS/subdomains-top1million-5000.txt $DISCOVERY/dns-5k.txt

ln -sfn $SECLISTS/Usernames/top-usernames-shortlist.txt $USERS/users-short.txt
ln -sfn $SECLISTS/Usernames/xato-net-10-million-usernames.txt $USERS/users-10m.txt

ln -sfn $SECLISTS/Passwords/Default-Credentials/default-passwords.txt $PASSES/default-creds.txt
ln -sfn $SECLISTS/Passwords/Default-Credentials/default-passwords.csv $PASSES/default-creds.csv
ln -sfn $SECLISTS/Fuzzing/login_bypass.txt $PASSES/login_bypass.txt

ln -sfn $SECLISTS/Fuzzing/LFI/LFI-Jhaddix.txt $WORDLISTS/Fuzzing/lfi.txt
ln -sfn $SECLISTS/Fuzzing/Databases/SQLi/Generic-SQLi.txt $WORDLISTS/Fuzzing/sqli.txt
ln -sfn $SECLISTS/Miscellaneous/Web/http-request-headers/http-request-headers-common-non-standard-examples.txt $WORDLISTS/Fuzzing/bypass-waf.txt
ln -sfn $SECLISTS/Fuzzing/XSS/robot-friendly/XSS-Cheat-Sheet-PortSwigger.txt $WORDLISTS/Fuzzing/XSS-Cheat-Sheet-PortSwigger.txt

```

### 「コピー元」と「編集済み」の区別

`Tools` から `Workbench/Exploit` にコピーした際、ファイル名にターゲットの識別子（IPの末尾など）を付ける癖をつけると、後で「どの設定で投げたか」が明白になります。

* **元ファイル:** `~/Tools/Python/exploit_db_12345.py`
* **コピー後:** `~/Workbench/Exploit/exploit_101.py` （10.10.10.101用）

### 書き換え箇所の検索（grep）
コピーしたコード内のどこを書き換えるべきか探すとき、以下のキーワードで `grep` すると、編集が必要な箇所（IP/Port/URL）を素早く見つけられます。

```bash
grep -Ei 'ip|port|addr|url|http' exploit_101.py
```

## Verification

### パケットキャプチャ

```zsh
# Post
# -nnはホスト名やポート名の解決を無効化し、動作を速くする（Localhostのため）
# 後半のフィルタ（0x504f5354）は、"POST" という文字列から始まるパケットのみを抽出するマジックナンバー
sudo tcpdump -i lo -A -nn -vv 'tcp port 8000 and (tcp[((tcp[12:1] & 0xf0) >> 2):4] = 0x504f5354)'
# Get
# -nnはホスト名やポート名の解決を無効化し、動作を速くする（Localhostのため）
# 後半のフィルタ（0x47455420）は、"GET" という文字列から始まるパケットのみを抽出するマジックナンバー
sudo tcpdump -i lo -nn -vv -A 'tcp port 8000 and (tcp[((tcp[12:1] & 0xf0) >> 2):4] = 0x47455420)'
```

### Pythonサーバ

- Basic認証用サーバ 

- Windows:[Server_BasicAuth.py]()
- Mac:[Server_BasicAuth.py](vscode://file/Users/fishborn0308/Documents/work/VSCode/10_Scripts/python/Verification/Server_BasicAuth.py)



- ログインフォーム 

- Windows:[Server_LoginForm.py]()
- Mac:[Server_LoginForm.py](vscode://file/Users/fishborn0308/Documents/work/VSCode/10_Scripts/python/Verification/Server_LoginForm.py)



## 一括インストール

```zsh
sudo apt install zoxide fzf zsh-autosuggestions zsh-syntax-highlighting tmux flameshot kitty
git clone https://github.com/tmux-plugins/tpm ~/.tmux/plugins/tpm
```





## トラブルシューティング

### 依存関係エラー

**症状:** `apt upgrade` や `apt install` で止まる、依存関係の unmet が出る
**原因:** パッケージのバージョン衝突、古い/破損パッケージ
**解決策:**

```zsh
sudo apt --fix-broken install       # 自動修復
sudo dpkg --configure -a            # 未設定パッケージの設定
sudo apt autoremove                 # 不要なパッケージ削除
sudo apt clean                      # キャッシュ削除

# 競合が残る場合は古いパッケージを削除
dpkg --remove --force-remove-reinstreq <package>
# 削除できない場合は強制削除
sudo dpkg --purge --force-depends <package>
```

⚠️ 競合が残る場合は古いパッケージを `dpkg --remove --force-remove-reinstreq <package>` で削除してから再インストール。

---

### リポジトリ・アップデートの失敗

**症状:**

```text
The following signatures couldn't be verified because the public key is not available: NO_PUBKEY <KEYID>
```

**原因:** Kali リポジトリの署名鍵が古い、または更新されていない
**解決策:**

```zsh
# 署名鍵の更新
sudo wget https://archive.kali.org/archive-keyring.gpg -O /usr/share/keyrings/kali-archive-keyring.gpg
sudo apt update

# リリース情報の変更を明示的に許可して更新
sudo apt update --allow-releaseinfo-change

# 標準の sources.list を強制再生成（中身が壊れている場合）
echo "deb http://http.kali.org/kali kali-rolling main contrib non-free non-free-firmware" \
| sudo tee /etc/apt/sources.list
sudo apt update
```

---

### パッケージの途中で止まる / 中途半端にインストールされる

**症状:**

```text
dpkg: error processing package ... (--configure):
```

**原因:** インストールやアップグレードが途中で失敗
**解決策:**

```zsh
sudo dpkg --configure -a           # 未設定のパッケージを設定
sudo apt install -f                # 依存関係修復
```

---

### リポジトリの古い URL / 無効なリポジトリ

**症状:** `apt update` で 404 エラーや「Failed to fetch」
**原因:** sources.list の URL が古い、または Kali Rolling に対応していない
**解決策:** `/etc/apt/sources.list` を公式リポジトリに更新

```text
deb http://http.kali.org/kali kali-rolling main non-free contrib
```

その後：

```zsh
sudo apt update
```

---

### パッケージキャッシュの破損

**症状:** パッケージのダウンロードやインストールが途中で止まる
**解決策:**

```zsh
sudo apt clean
sudo apt autoclean
sudo apt update
```

---

### ディスク容量不足

**症状:** 「No space left on device」やインストールが止まる
**解決策:**

```zsh
sudo apt autoremove
sudo apt clean
df -h   # 容量確認
```

必要なら古いカーネルやログを削除。

---

### ネットワークアダプタの不調

**症状:** `target` に ping が飛ばない、VPNは繋がっているのに通信できない。

**原因:** VMwareの仮想アダプタ（vmnet）のハングアップ。

**解決策:**

* **ホスト側:** VMwareの「Virtual Network Editor」で [Restore Defaults] を実行。
* **ゲスト側:** ネットワークマネージャーの再起動。
```zsh
sudo systemctl restart NetworkManager

```

### マウスカーソルが消失する - 仮想マシンの互換性に問題

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

---

### マウスカーソルが消失する - VMware Toolsの不整合、またはビデオアクセラレーションの競合

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
```zsh
sudo apt update
sudo apt install --reinstall -y open-vm-tools-desktop
sudo reboot

```
* **方法E マウスのゲーム設定を変更する**
	1. VMware Workstation / Player の **[編集]** ＞ **[環境設定]** を開きます。
	2. **[入力]** タブを選択します。
	3. **「ゲーム用にマウスを最適化」** という項目を **[常に使用しない]** に変更します。
* **方法F USBコントローラーの互換性変更**
	1. 仮想マシンの設定から **[USB コントローラ]** を選択します。
	2. USB互換性を **[USB 3.1]**（または利用可能な最新のもの）に変更して保存します。

---

💡 **ポイント**

* この順番で実行すれば、依存関係エラー、署名鍵エラー、古いパッケージ、キャッシュ破損などの典型的トラブルはまとめて解決可能です。
* もし途中で止まったら、そのパッケージ名を確認して `dpkg --remove --force-remove-reinstreq` で個別に処理できます。

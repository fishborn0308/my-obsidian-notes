

## 4. ツール管理とPython環境 (`pipx`)

システムを汚さず、どこからでもツールを呼び出せるようにします。

1. **pipxのセットアップ:**
```bash
sudo apt install -y pipx && pipx ensurepath

```

2. **便利なユーティリティ:**
```bash
# ペネトレーションテスト・実戦用ツールのインストール
sudo apt install -y rlwrap netexec seclists curl enum4linux-ng \
	flameshot rustscan

```

```bash
# ターミナル・ユーティリティ・フォントのインストール
sudo apt update
sudo apt install -y kitty tmux zoxide fzf \
    zsh-autosuggestions zsh-syntax-highlighting \
    fonts-jetbrains-mono

# Tmux プラグインマネージャー (TPM) の導入    
git clone https://github.com/tmux-plugins/tpm ~/.tmux/plugins/tpm

# デフォルトターミナルの切り替え
sudo update-alternatives --config x-terminal-emulator # Kittyを選択
```

3. **Git経由のインストール**

* ***AutoRecon (推奨自動化ツール):**

```bash
# 依存関係のインストール後、pipx等でインストール
sudo apt install -y seclists curl dnsrecon nbtscan nikto nmap onesixtyone oscanner smbclient smbmap sslscan ccze
pipx install git+https://github.com/Tib3rius/AutoRecon.git

```

4. **Pythonツールのインストール:**
* `pipx install impacket` (ネットワーク攻撃)
* `pipx install updog` (アップロード対応HTTPサーバ)

4. **Enumeration（列挙）の配置:**

```bash
# 保存用ディレクトリの作成
mkdir -p ~/tools/scripts/windows/enumeration
cd ~/tools/scripts/windows/enumeration

# 1. Seatbelt (GhostPackの主力ツール)
# ※ビルド済みのものを探すか、自身でビルドしたものを配置
# 公式: https://github.com/GhostPack/Seatbelt
# (OSCPでは最新のビルド済みバイナリを整理しておくのが定石です)

# 2. SharpUp (特権昇格のチェックに特化したC#ツール)
# ※ビルド済みのものを探すか、自身でビルドしたものを配置
# https://github.com/GhostPack/SharpUp

# 3. PowerUp.ps1 (PowerShellベースの定番)
wget https://raw.githubusercontent.com/PowerShellMafia/PowerSploit/master/Privesc/PowerUp.ps1

# 4. WinPEAS (列挙ツールの定番)
wget https://github.com/peass-ng/PEASS-ng/releases/latest/download/winPEASany.exe
```

```bash
# 保存用ディレクトリの作成
mkdir -p ~/tools/scripts/linux/enumeration
cd ~/tools/scripts/linux/enumeration

# 1. LinPEAS (最強の自動列挙スクリプト)
wget https://github.com/peass-ng/PEASS-ng/releases/latest/download/linpeas.sh

# 2. Linux Exploit Suggester (カーネル脆弱性診断)
wget https://raw.githubusercontent.com/mzet-/linux-exploit-suggester/master/linux-exploit-suggester.sh -O les.sh

# 3. pspy (プロセスのリアルタイム監視：cronジョブの特定に便利)
# ※最近はバイナリ版を落としておくのが楽です
wget https://github.com/DominicBreuker/pspy/releases/download/v1.2.1/pspy64
```

5. **PrivEsc（権限昇格）の配置:**

```bash
# 保存用ディレクトリの作成
mkdir -p ~/tools/scripts/windows/privesc
cd ~/oscp/scripts/windows/privesc

# 1. Juicy Potato (Windows 2012/2016用)
wget https://github.com/ohpe/juicy-potato/releases/download/v0.1/JuicyPotato.exe

# 2. Rogue Potato (Windows 2019 / Windows 10用)
# ※リポジトリから最新のバイナリを取得
wget https://github.com/antonioCoco/RoguePotato/releases/download/1.0/RoguePotato.zip

# 3. PrintSpoofer (Juicyが効かない最新環境の定番)
wget https://github.com/itm4n/PrintSpoofer/releases/download/v1.0/PrintSpoofer64.exe

# 4. GodPotato (最新の汎用Potato)
wget https://github.com/BeichenDream/GodPotato/releases/download/V1.20/GodPotato-Net4.exe
```

1. **Pivoting/tunneling(トンネル・横展開)の配置:**

```bash
# 保存用ディレクトリ
mkdir -p ~/tools/scripts/tunneling/chisel
cd ~/oscp/scripts/tunneling/chisel

# 最新リリースの取得 (Linux 64bit用とWindows 64bit用)
# ※バージョン番号は適宜最新を確認してください
wget https://github.com/jpillora/chisel/releases/download/v1.9.1/chisel_1.9.1_linux_amd64.gz
wget https://github.com/jpillora/chisel/releases/download/v1.9.1/chisel_1.9.1_windows_amd64.gz

# 解凍
gunzip *.gz
mv chisel_1.9.1_linux_amd64 chisel
mv chisel_1.9.1_windows_amd64 chisel.exe
chmod +x chisel

# achiveを保管
wget https://github.com/jpillora/chisel/releases/download/v1.9.1/chisel_1.9.1_linux_amd64.gz
wget https://github.com/jpillora/chisel/releases/download/v1.9.1/chisel_1.9.1_windows_amd64.gz
```

```bash
# 保存用ディレクトリ
mkdir -p ~/tools/scripts/tunneling/ligolo
cd ~/oscp/scripts/tunneling/ligolo

# Proxy (Kali側) と Agent (ターゲット側) を取得
wget https://github.com/nicocha30/ligolo-ng/releases/download/v0.5.2/ligolo-ng_proxy_0.5.2_linux_amd64.tar.gz
wget https://github.com/nicocha30/ligolo-ng/releases/download/v0.5.2/ligolo-ng_agent_0.5.2_linux_amd64.tar.gz
wget https://github.com/nicocha30/ligolo-ng/releases/download/v0.5.2/ligolo-ng_agent_0.5.2_windows_amd64.zip

# 解凍して整理
tar -xvf ligolo-ng_proxy_0.5.2_linux_amd64.tar.gz proxy
tar -xvf ligolo-ng_agent_0.5.2_linux_amd64.tar.gz agent
unzip ligolo-ng_agent_0.5.2_windows_amd64.zip -d agent_win
```
---

## 5. ターミナル & マルチプレクサ & スクリーンショット設定





### `~/.zshrc` エッセンス (自動化・効率化)


```bash



# --- 1. 情報更新ロジック (precmd) ---
# プロンプトが表示される直前に、IP情報などを一括更新します
refresh_oscp_prompt() {
    # 自局IPの取得 (tun0優先)
    local my_ip=$(ip -br -4 a show tun0 2>/dev/null | awk '{print $3}' | cut -d/ -f1)
    [ -z "$my_ip" ] && my_ip=$(ip -br -4 a show eth0 2>/dev/null | awk '{print $3}' | cut -d/ -f1)
    CURRENT_MY_IP="${my_ip:-N/A}"

    # ターゲット情報の組み立て
    if [ -n "$ip" ]; then
        if [ -n "$fqdn" ]; then
            TARGET_STATUS="%F{red}[T: $ip ($fqdn)]%f"
        else
            TARGET_STATUS="%F{red}[T: $ip]%f"
        fi
    else
        TARGET_STATUS=""
    fi
}

# zshのフックに登録
autoload -Uz add-zsh-hook
add-zsh-hook precmd refresh_oscp_prompt

# --- 2. ターゲット設定関数 (改良版) ---
target() {
    if [ -z "$1" ]; then
        echo "Usage: target <IP> [FQDN]"
        return 1
    fi

    export ip="$1"
    export target="$1"
    unset fqdn # 前のターゲット情報をクリア
    local tag="# TARGET"
    
    export workdir="$HOME/labs/$ip"
    mkdir -p "$workdir"
    cd "$workdir"

    if [ -n "$2" ]; then
        export fqdn="$2"
        sudo sed -i "/ $fqdn/d; /^$ip /d" /etc/hosts
        echo "$ip $fqdn $tag" | sudo tee -a /etc/hosts > /dev/null
    fi

    echo "[+] Target: $ip (${fqdn:-no fqdn})"
    echo "[+] Switched to: $workdir"
}

target_clear() {
    sudo sed -i "/# TARGET/d" /etc/hosts
    unset ip target fqdn
    echo "[!] Environment & Hosts cleared."
}

# --- 3. プロンプトの定義 ---
setopt prompt_subst
# 左側：[L: 自局IP] パス #
PROMPT='%F{cyan}[L: ${CURRENT_MY_IP}]%f %F{blue}%~%f %# '
# 右側：[T: 相手IP]
RPROMPT='${TARGET_STATUS}'

# Ligolo-ng の設定用 (TUNデバイス作成)
setup_ligolo() {
    sudo ip tuntap add user $USER mode tun ligolo
    sudo ip link set ligolo up
    echo "[+] TUN interface 'ligolo' is UP."
}

# Chisel サーバー起動 (Kali側で待機)
alias chisel_srv='~/tools/scripts/tunneling/chisel/chisel server -p 8080 --reverse'

# メンテナンス・便利エイリアス
alias gclone='cd ~/tools/git && git clone'
alias update-tools='find ~/tools/git -maxdepth 2 -name .git -type d -execdir git pull \;'
alias pserv="python3 -m http.server 80"
alias udot="updog -p 80"
alias icat="kitty +kitten icat"
# 自分のIPを簡潔に表示 (IPv4のみ)
alias myip="ip -br -4 a"
# VPNのIPだけを「値だけ」取り出す (よりシンプルに書けます)
alias vpnip="ip -br -4 a show tun0 | awk '{print \$3}' | cut -d/ -f1"
```


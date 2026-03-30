# Basicコマンド（Kali / OSCP運用）

---

## ■ メンテナンス

### 日常
```zsh
sudo apt update && sudo apt full-upgrade -y
sudo apt autoremove -y && sudo apt autoclean -y
```

---

## ■ 環境設定

### スリープ無効化

```zsh
xset s off -dpms s noblank
```

### サスペンド無効化

```zsh
sudo systemctl mask sleep.target suspend.target hibernate.target hybrid-sleep.target
```

### サスペンド再有効化（必要時）

```zsh
sudo systemctl unmask sleep.target suspend.target hibernate.target hybrid-sleep.target
```

---

## ■ ディレクトリ構造

```text
~/Vault/Target/<IP>/
├── assets/
├── result/
│   ├── nmap_*
│   └── autorecon/
├── log/
└── <IP>.md
```

```zsh
mkdir -p ~/Vault/Target \
         ~/Tools/{Git,Python,Powershell,Shell,Bin} \
         ~/Workbench/{Recon,AD/enumeration,AD/attacks,Web/sqli,Exploit,Wordlists,Verification} \
         ~/Transfer/{RevShell,PrivEsc,PostEx,Pivoting}/{Linux,Windows}
```

---

## ■ 変数（target実行で自動セット）

|変数|内容|
|---|---|
|$TARGET_IP|ターゲットIP|
|$TARGET_NAME|ホスト名|
|$TARGET_DIR|~/Vault/Target/|
|$workdir|作業ディレクトリ|
|$OUT|result|
|$LOG|log|
|$ASSETS|assets|

---

## ■ ターゲット管理

### 設定

```zsh
target 10.10.10.10 targetname
```

### tmux付き起動

```zsh
targettmux 10.10.10.10 targetname
```

### 全リセット

```zsh
targetreset
```

---

## ■ スキャンフロー（最重要）

### 初動

```zsh
scaninit
```

### ポート確認

```zsh
ports-init
ports-serv
```

### 次アクション

```zsh
next
nextsvc
```

### フルスキャン確認

```zsh
fullcheck
```

---

## ■ UDPスキャン

```zsh
udp-fast
udp-top
udp-deep
```

---

## ■ AutoRecon

```zsh
ar
ar_f_nmap
ar_f_nmap_full
```

ログ確認

```zsh
arlog
```

---

## ■ ワードリスト操作

```zsh
getlist
```

ワードリストの確認

```zsh
# Discoveryの中身だけを2階層まで表示
tree -L 2 $SECLISTS/Discovery/

# 行数をカウント
wc -l $SECLISTS/Discovery/Web-Content/common.txt

# 中身の確認
head -n 10 $SECLISTS/Discovery/Web-Content/directory-list-2.3-medium.txt

# 名前で検索する (`find`)
find $SECLISTS -name "*wordpress*"
```

---
## ■ 脆弱性検索

```zsh
# データベースの更新
searchsploit -u
# 特定のIDのパスを表示
searchsploit -p <ID>
# そのまま中身を読む (lessで開く)
searchsploit -x <ID>
# カレントディレクトリにコピー
searchsploit -m <ID>
# タイトル（Title）のみ検索
searchsploit -t <keyword>
# オンラインのURLを表示
searchsploit -w <keyword>
```

---
## ■ ファイル転送

### 送信（HTTP）

```zsh
pserv      # 8000
pserv80    # 80
```

### 受信

```zsh
udot
```

---

## ■ IP確認

```zsh
ip -br -4 a
vpnip
```

---

## ■ ポート確認

```zsh
ss -tulnp
```

---

## ■ tmux操作

| 操作       | キー                            |
| -------- | ----------------------------- |
| prefix   | Ctrl + a                      |
| 分割（横）    | \|                            |
| 分割（縦）    | -                             |
| ログ開始     | Prefix + Shift + p            |
| ログ保存先の変更 | Prefix + Shift + l            |
| 画面キャプチャ  | Prefix + Alt + p              |
| reload   | Prefix + r                    |
| ﾃﾞﾀｯﾁ    | Prefix + d                    |
| ｱﾀｯﾁ     | tmux attach -t <session_name> |
| プラグイン導入  | Prefix + Shift + i            |

---

## ■ ターミナル操作（Kitty & Zsh）

| 操作         | ショートカット / コマンド          |
| ---------- | ----------------------- |
| コマンド履歴検索   | Ctrl + R                |
| ディレクトリ移動   | z <フォルダ名の一部>            |
| 補完の確定      | → (右矢印) または Ctrl + F    |
| 画面クリア      | Ctrl + l                |
| コピー / 貼り付け | マウス選択/ Ctrl + Shift + V |
| 画像表示       | icat <画像名>              |

---

## ■ ログ → Obsidian

```zsh
ports-note
nestsvc-note
full-note
scan-note

l2o
l2m
```

---

## ■ スクリーンショット

```zsh
fshot
```

---

## ■ ネットワーク検証

### リスナー確認

```zsh
ss -tulnp | grep <port>
```

### 接続確認

```zsh
nc -zv $(vpnip) <port>
```

### パケット確認

```zsh
sudo tcpdump -i tun0 port <port>
```

### フィルタリング例

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

---

## ■ ファイアウォール関連 (ufw / iptables)

| 操作            | コマンド                      | 備考                            |
| ------------- | ------------------------- | ----------------------------- |
| **状態確認**      | `sudo ufw status`         | `inactive` なら全開放状態。           |
| **FWを無効化**    | `sudo ufw disable`        | **[推奨]** 試験中はトラブル防止のためOFFが基本。 |
| **特定のポートを許可** | `sudo ufw allow 4444/tcp` | シェルを待機するポートを個別に開ける。           |
| **ルールを全リセット** | `sudo ufw reset`          | 設定がぐちゃぐちゃになった時に。              |

### 接続が来ない時のデバッグ

```zsh
# DROPされている通信をリアルタイムで監視
sudo watch -n 1 "iptables -L -n -v | grep DROP"
```

---
## ■ テスト系コマンド（Verification Commands）

### 名前解決とネットワークの疎通確認

`/etc/hosts` の設定や DNS が正しく機能しているかを確認

| コマンド                                  | 用途                           |
| ------------------------------------- | ---------------------------- |
| **`getent hosts <host_name>`**        | `/etc/hosts` や DNS から IP を引く |
| **`ping -c 3 $ip`**                   | 生存確認（ICMP）                   |
| **`traceroute -n $ip`**               | 経路の確認                        |
| **`dig @$ip version.bind txt chaos`** | DNS サーバーの調査                  |

---

### 自分の待ち受け（リスナー）確認

```bash
# 指定したポート（例: 4444）で待ち受けているプロセスを表示
# 自分のシェルが nc や msfconsole で開いているか確認
ss -tulnp | grep 4444

# 外部（ターゲット）から自分のポートが見えるかテスト
# 別のペインから実行
nc -zv $(vpnip) 4444

```

---

### リバースシェルの「導通」テスト (tcpdump)

```bash
# VPNインターフェース(tun0)を流れる特定のポートの通信を監視
# これを実行したままペイロードを叩き、パケットが来れば「相手は送っている」と判断できる
sudo tcpdump -i tun0 port 4444

```

---

### 転送したファイルの整合性テスト

```bash
# Kali 側
md5sum binary.exe

# Windows (PowerShell) 側
Get-FileHash -Algorithm MD5 .\binary.exe

```

---
## ■ grep（高速確認）

```zsh
grep -Ei 'ip|port|url' file
```

---

## ■ Git（最低限）

```zsh
git clone <url>
git pull
```

---

## ■ systemctl

```zsh
sudo systemctl start <service>
sudo systemctl restart <service>
sudo systemctl status <service>
sudo systemctl stop <service>
# 自動起動
sudo systemctl disable <service>
sudo systemctl enable <service>

```

---

## ■ Python

### 仮想環境の基本フロー

```bash
# 1. ツール用ディレクトリへ移動
cd ~/tools/some-exploit

# 2. 仮想環境の作成 (名前は 'venv' が一般的)
python3 -m venv venv

# 3. 仮想環境に入る (有効化)
# プロンプトの左端に (venv) と表示されます
source venv/bin/activate

# 4. 必要なライブラリをインストール
# システムを汚さず、このフォルダ内だけにインストールされます
pip install -r requirements.txt

# 5. ツールを実行
python3 exploit.py

# 6. 仮想環境から抜ける
deactivate
```

---

### pipx：ツールインストール

* **基本:** `pipx install <ツール名>`
* **GitHubから直接:** `pipx install git+https://github.com/user/repo.git`
* **一覧表示:** `pipx list`（どこに何が入っているか一目瞭然）
* **全一括更新:** `pipx upgrade-all`（試験前に一度やっておくと安心）

## ■ トラブル対処

### ポート占有

```zsh
sudo fuser -k 80/tcp
```

### 依存関係

```zsh
sudo apt --fix-broken install
sudo dpkg --configure -a
```

### Python

#### `pip install` でエラーが出る

最近の Kali (Debian) では、システム全体に `pip` でインストールしようとすると **"externally-managed-environment"** というエラーが出ます。

* **解決策:** `pipx` を使うか、上記の `venv` を作成してください。

#### Python 2.7 が必要な古いエクスプロイト

```bash
# Kaliにはまだ python2 が入っています
python2 exploit.py

```

#### `ModuleNotFoundError` が出た時

```bash
# 仮想環境内でこれを試す
pip install --upgrade pip
pip install <足りないモジュール名>

```

---

## ■ 最低限コマンド

```zsh
pwd
ls -alF
cd
sudo -i
ps aux | grep <name>
```

---

## ■ OSCP当日ルーチン

1. targettmux
    
2. scaninit
    
3. tmux logging ON
    
4. ports-init → next
    
5. 手動列挙開始
    
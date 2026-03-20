# Basicコマンド

## メンテナンス
### 日常的なメンテナンス

```zsh
sudo apt update && sudo apt upgrade -y
sudo apt autoremove -y
```

### メジャーアップデート

```zsh
sudo apt update && sudo apt full-upgrade -y
sudo apt autoclean
sudo apt autoremove -y
```
## 環境設定

### スリープ無効化

```zsh
xset s off -dpms s noblank
```
Linuxのコンソール（Xなし）の場合
```zsh
setterm -blank 0 -powerdown 0 -powersave off
```

### 自動サスペンドを無効化

無効化
```zsh
sudo systemctl mask sleep.target suspend.target hibernate.target hybrid-sleep.target
```
有効化
```zsh
sudo systemctl mask sleep.target suspend.target hibernate.target hybrid-sleep.target
```

## ディレクトリの作成

```zsh
mkdir -p ~/Vault/Target \
         ~/Tools/{Git,Python,C#,Powershell,Shell,Bin} \
         ~/Workbench/{Recon,AD/enumeration,AD/attacks,Web/sqli,Exploit,Wordlists,Verification} \
         ~/Transfer/{RevShell,PrivEsc,PostEx,Pivoting}/{Linux,Windows}

# 確認用
ls -R ~/Vault ~/Tools ~/Workbench ~/Transfer

```

| カテゴリ   | ディレクトリパス                                                    | 用途                                  |
| :----- | :---------------------------------------------------------- | :---------------------------------- |
| **記録** | `~/Vault/Target/$ip/{assets,result,log}`                    | Obsidian管理。スクショ、Nmap結果、tmuxログ       |
| **倉庫** | `~/Tools/{Git,Python,C#,Powershell,Bin}`                    | オリジナルツールの保管庫。ここからコピーして使う            |
| **作業** | `~/Workbench/{Recon,AD,Web,Exploit,Wordlists,Verification}` | Kaliローカルで実行するツール群                   |
| **配送** | `~/Transfer/{RevShell,PrivEsc,PostEx,Pivoting}`             | ターゲットへ送る用。内部に **Linux/Windows** を作成 |

---

## 自作変数

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

---

### 2. ネットワーク・ステータス（動的更新）

`precmd` によって、プロンプトが表示されるたびに最新状態に更新されます。

|**変数名**|**内容**|**備考**|
|---|---|---|
|**`$CURRENT_MY_IP`**|自分のIP (tun0優先、次いでeth0)|プロンプト左側 `[L: ...]` に表示|
|**`$TARGET_STATUS`**|ターゲットの表示用文字列|プロンプト右側 `[T: ...]` に表示|

---

### 3. Wordlists & クレデンシャル（固定・共通）

攻撃リソースへ即座にアクセスするための定数です。

| **変数名**       | **パス**                 | **用途**                           |
| ---------------- | ------------------------ | ---------------------------------- |
| **`$WORDLISTS`** | `~/Workbench/Wordlists`  | 自作・加工済み辞書の親ディレクトリ |
| **`$USERS`**     | `$WORDLISTS/Usernames`   | 収集したユーザー名リスト           |
| **`$PASSES`**    | `$WORDLISTS/Passwords`   | 収集したパス、解凍済み Rockyou     |
| **`$CREDS`**     | `$WORDLISTS/Credentials` | `user:pass` 形式のコンボリスト     |
| **`$ROCKYOU`**   | `$PASSES/rockyou.txt`    | 最強のパスワードリスト             |
| **`$SECLISTS`**  | `/usr/share/seclists`    | Kali標準の Web 攻略用辞書群        |
| **`$DISCOVERY`**    | `$WORDLISTS/Discovery`   | Webディレクトリ・ファイル列挙用辞書の格納先     |

* **ailias**
	* **cdword:** `cd $WORDLISTS`
	* **cdusers:** `cd $WORDLISTS`
	* **cdpass:** `cd $WORDLISTS`
	* **cdcreds:** `cd $WORDLISTS`
	* **cddisc:** `cd $DISCOVER`
	*  **cdseclists'** `cd $SECLISTS`


---


##  ターミナル操作（Kitty & Zsh）

まずは、爆速でコマンドを打ち、履歴を遡るための基本操作です。

| 操作 | ショートカット / コマンド | 内容 |
| --- | --- | --- |
| **コマンド履歴検索** | `Ctrl + R` | **fzf**が起動。過去に打った複雑なワンライナーを一瞬で呼び出す。 |
| **ディレクトリ移動** | `z <フォルダ名の一部>` | **zoxide**が学習したパスへ超高速ジャンプ。 |
| **補完の確定** | `→` (右矢印) または `Ctrl + F` | **zsh-autosuggestions**の薄いグレーの提案を確定。 |
| **画面クリア** | `Ctrl + l` | 画面を綺麗にする（実際にはスクロールアップされるだけ）。 |
| **コピー / 貼り付け** | **マウス選択** / `Ctrl + Shift + V` | Kitty設定で「選択のみでコピー」が有効。 |
| **画像表示** | `icat <画像名>` | ターミナル内でスクリーンショット等を確認。 |

---

## 画面分割・ログ管理（Tmux）

証拠を残しつつ、複数の作業を並行するための必須操作です。

| 操作                    | ショートカット          | 内容                                            |
| ----------------------- | ----------------------- | ----------------------------------------------- |
| **プレフィックス**      | `Ctrl + a`              | **すべてのTmux操作の起点。**                    |
| **縦に分割**            | `Prefix` + \|           | 画面を左右に割る                                |
| **横に分割**            | `Prefix` + `-`          | 画面を上下に割る                                |
| **ペイン移動**          | `Ctrl + a` + `矢印キー` | 分割した画面間を移動。                          |
| **ロギング開始/停止**   | `Prefix` + `Shift + p`  | **[重要]** 全出力を `~/oscp/logs/` に保存開始。 |
| **画面キャプチャ**      | `Prefix` + `Alt + p`    | 今見えている画面をテキストで即座に保存。        |
| **プラグイン導入**      | `Prefix` + `I` (大文字) | TPMで設定したプラグインをインストール。         |
| **ログ保存先の変更**    | `Prefix` + `Shift + l`          | ログ保存先を現在の `$TARGET_IP` に更新          |
| **ロギングの開始/終了** | `Prefix` + `Shift + p`  | ロギング開始/停止                               |
| **ソースの更新**        | `Prefix` + `r`  | source ~/.tmux.conf                           |

---

## スクリーンショット（Flameshot）

レポート作成において、証拠画像は「説明」よりも重要です。

| 操作 | 手順 / ショートカット | 内容 |
| --- | --- | --- |
| **起動** | `PrintScreen` キー | 画面が暗転し、選択モードへ。 |
| **注釈を入れる** | マウスでドラッグ | 矢印、枠、ぼかし、テキストがその場で入れられる。 |
| **保存 / コピー** | `Ctrl + S` / `Ctrl + C` | ファイル保存か、クリップボードへ。 |
| **全画面保存** | `flameshot full -p ~/logs` | 迷ったらこれ。全画面を即保存。 |

---

## ターゲット管理（自作関数）

今回 `.zshrc` に組み込んだ、OSCP特化型の運用コマンドです。

* **ターゲット設定:** `target 10.10.10.123 victim.htb`
* `/etc/hosts` に追記される。
* `~/Vault/Result/10.10.10.123/` が自動作成され、そこに移動する。
* 右側プロンプトにターゲットが表示される。


* **ターゲット解除:** `targetcl`
* 変数をクリアし、`/etc/hosts` を掃除する。

* **tmuxログを掃除して Obsidian へ追記'** `l2o`

---

## Kali 基本コマンド（サバイバル）

試験中に「あれ、何だっけ？」となりやすい基本です。

* **パス確認:** `pwd`（今どこにいるか）
* **権限昇格:** `sudo -i`（rootシェルを取る）
* **プロセス確認:** `ps aux | grep <名前>`
* **ailias**
	* **ファイル転送 (送る):** `pserv`（Kaliでサーバー起動）→ ターゲットで `wget`/`curl`
	* **ファイル転送 (受ける):** `udot`（Kaliで **updog** 起動。ブラウザからアップロード可能）

## Git：エクスプロイトの管理

GitHubからツールを落とし、自分の環境に合わせて調整するための基本コマンドです。

| 操作 | コマンド | 備考 |
| --- | --- | --- |
| **ツールの取得** | `git clone <URL>` | ツールをローカルにコピー。 |
| **最新状態に更新** | `git pull` | ツールがアップデートされたら実行。 |
| **変更の破棄** | `git checkout .` | コードをいじりすぎて壊した時に元に戻す。 |
| **特定行の検索** | `grep -r "pattern" .` | 落としてきたコード内から特定の関数などを探す。 |

* **ailias**
	* **GitClone:** `gclone` `cd ~/tools/git && git clone`
	* **GitToolsのアップデート:** `gup` `find ~/Tools/Git -maxdepth 2 -name .git -type d -execdir git pull --rebase \;`

> [!IMPORTANT]
> **OSCPの掟:** 公開されているエクスプロイト（特に行数の多いPythonスクリプト）は、実行前に必ず中身を読み、自分のKaliのIPやポートに書き換える箇所がないか確認してください。

## Python : 仮装環境の管理

### 1. Python 仮想環境 (venv)

「あるツールでは Python 3.9 が必要だが、別のツールでは 3.12 が必要」といった依存関係の衝突を避けるために使います。

#### 🛠️ 仮想環境の基本フロー

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

### 2. pipx：ツールインストールの決定版

今回のセットアップで多用した `pipx` は、**「仮想環境の作成」と「パスへの追加」を全自動で行ってくれる**ツールです。

* **基本:** `pipx install <ツール名>`
* **GitHubから直接:** `pipx install git+https://github.com/user/repo.git`
* **一覧表示:** `pipx list`（どこに何が入っているか一目瞭然）
* **全一括更新:** `pipx upgrade-all`（試験前に一度やっておくと安心）

---

### 3. Python トラブル解決

#### ① `pip install` でエラーが出る

最近の Kali (Debian) では、システム全体に `pip` でインストールしようとすると **"externally-managed-environment"** というエラーが出ます。

* **解決策:** `pipx` を使うか、上記の `venv` を作成してください。

#### ② Python 2.7 が必要な古いエクスプロイト

古いマシンでは Python 2 系で書かれたスクリプトが飛んでくることがあります。

```bash
# Kaliにはまだ python2 が入っています
python2 exploit.py

```

#### ③ `ModuleNotFoundError` が出た時

```bash
# 仮想環境内でこれを試す
pip install --upgrade pip
pip install <足りないモジュール名>

```

---

## 時刻の修正

### 1. ターゲット（Windows）の時刻を確認する

まずは相手の時間を知る必要があります。

| ツール | コマンド | 備考 |
| --- | --- | --- |
| **NetExec (nxc)** | `nxc smb $ip` | 実行結果の右側にサーバー時刻が表示されます。 |
| **Impacket** | `impacket-netview -target $ip` | 列挙の過程でシステム時刻が表示されます。 |
| **SMB (smbclient)** | `smbclient -L //$ip/` | 接続時にサーバーの現在時刻が表示されることがあります。 |

---

### 2. Kali Linux の時刻を調整する

ターゲットの時刻が分かったら、自分のマシンをそれに合わせます。

#### ① 手動で設定する（最も確実）

```bash
# 書式: sudo date -s "YYYY-MM-DD HH:MM:SS"
sudo date -s "2026-03-14 16:00:00"

```

#### ② ターゲットのDCから同期する（ntpdate）

ターゲットがNTP（123/udp）を公開している場合、直接同期できます。

```bash
sudo ntpdate -u $ip

```

#### ③ タイムゾーンの変更

もしタイムゾーン自体がズレている場合は、対話的に変更します。

```bash
sudo dpkg-reconfigure tzdata

```

---

### 3. 自動化：ターゲットに一瞬で合わせるワンライナー

`NetExec` の出力を利用して、ターゲットマシンの時刻を自動で自分のKaliにセットする便利なワンライナーです。

```bash
# nxcの出力から日付と時刻を抜き出し、dateコマンドに渡す
sudo date -s "$(nxc smb $ip | awk '{print $11, $12}')"

```

---

### 4. OSCP実戦での注意点

* **スナップショット後のズレ:** VMをレジューム（復帰）させた直後は、時刻が大幅にズレていることが多いです。試験中は定期的に `date` コマンドで確認してください。
* **ntpの停止:** Kaliがインターネット上のNTPサーバーと同期しようとして、手動設定した時刻を勝手に戻してしまうことがあります。
```bash
# 同期サービスを一時停止
sudo timedatectl set-ntp false

```


* **ログへの記録:** 時刻を合わせた際は、必ずその時のコマンドと「ズレていた秒数」をメモしておきましょう。レポートで「Kerberos認証に失敗したため時刻同期を行った」と書くのは、プロの視点として高く評価されます。

---

## IPアドレスの確認

OSCPの作業中、特に複数のインターフェース（VPN, ローカル, Docker等）がある場合に威力を発揮します。

| コマンド | 表示結果のイメージ | メモ |
| --- | --- | --- |
| **`ip -br a`** | `lo UNKNOWN 127.0.0.1/8` <br><br> `eth0 UP 10.0.2.15/24` <br><br> `tun0 UP 10.10.14.5/23` | **一撃で全IPを把握。** ステータス（UP/DOWN）も一目瞭然。 |
| **`ip -br -4 a`** | 上記から IPv6 を除外 | IPv4 だけが必要な OSCP ではこれが最強。 |

* **ailias**
	* **自分のIPを簡潔に表示:** `myip` `ip -br -4 a`
	* **VPNのIPだけを値だけ取り出す:** `vpnip` `ip -br -4 a show tun0 | awk '{print \$3}' | cut -d/ -f1`

## ファイアウォール関連 (ufw / iptables)

Kali Linuxではデフォルトで `ufw` (Uncomplicated Firewall) が入っていますが、無効になっていることが多いです。試験中は「意図せずブロックしていないか」を確認するのが鉄則です。

| 操作 | コマンド | 備考 |
| --- | --- | --- |
| **状態確認** | `sudo ufw status` | `inactive` なら全開放状態。 |
| **FWを無効化** | `sudo ufw disable` | **[推奨]** 試験中はトラブル防止のためOFFが基本。 |
| **特定のポートを許可** | `sudo ufw allow 4444/tcp` | シェルを待機するポートを個別に開ける。 |
| **ルールを全リセット** | `sudo ufw reset` | 設定がぐちゃぐちゃになった時に。 |

### 💡 接続が来ない時のデバッグ

`iptables` のルールが複雑に絡んでいる場合、以下のコマンドでパケットが「DROP（破棄）」されていないか確認します。

```bash
# DROPされている通信をリアルタイムで監視
sudo watch -n 1 "iptables -L -n -v | grep DROP"

```

---

## システムサービス管理 (systemctl)

ツールが動かないときや、設定を変更した後は `systemctl` でサービスの状態を制御します。

### 基本操作

```bash
# サービスの起動
sudo systemctl start <サービス名>

# サービスの停止
sudo systemctl stop <サービス名>

# サービスの再起動（設定変更後など）
sudo systemctl restart <サービス名>

# 状態確認（動いているか、エラーが出ていないか）
sudo systemctl status <サービス名>

```

### よく使うサービス名

* **`postgresql`**: `msfconsole` (Metasploit) を使う前に起動が必要。
* **`ssh`**: 自分のKaliに他から入りたい時。
* **`docker`**: Dockerコンテナベースのツールを使う時。
* **`neo4j`**: `BloodHound` を使うためのデータベース。

### 実戦で役立つ「トラブル回避」設定

#### ① 起動時に自動実行させない（永続化の解除）

試験環境をクリーンに保つため、不要なサービスは自動起動をオフにしておくのが賢明です。

```bash
# 自動起動を無効にする
sudo systemctl disable <サービス名>

# 逆に、常に動かしておきたい場合
sudo systemctl enable <サービス名>

```

#### ② 「ポートが既に使われている」エラーの対処

`nc` や `python3 -m http.server` を起動しようとして「Address already in use」と出た場合、古いプロセスが残っています。

```bash
# ポート80を使っているプロセスのPIDを特定して殺す
sudo fuser -k 80/tcp

```

---

## テスト系コマンド（Verification Commands）

### 1. 名前解決とネットワークの疎通確認

`/etc/hosts` の設定や DNS が正しく機能しているかを確認します。

| コマンド | 用途 | OSCPでの活用シーン |
| --- | --- | --- |
| **`getent hosts <名前>`** | `/etc/hosts` や DNS から IP を引く | `target` 関数実行後、Kali が正しく名前を認識したか確認。 |
| **`ping -c 3 $ip`** | 生存確認（ICMP） | そもそもネットワーク的に繋がっているか。 |
| **`traceroute -n $ip`** | 経路の確認 | VPN 経由で正しいゲートウェイを通っているか。 |
| **`dig @$ip version.bind txt chaos`** | DNS サーバーの調査 | ターゲットが DNS の場合に、バージョン情報を抜く。 |

---

### 2. 自分の待ち受け（リスナー）確認

リバースシェルが届かないとき、自分の Kali が正しくポートを開けて待っているかを確認します。

```bash
# 指定したポート（例: 4444）で待ち受けているプロセスを表示
# 自分のシェルが nc や msfconsole で開いているか確認
ss -tulnp | grep 4444

# 外部（ターゲット）から自分のポートが見えるかテスト
# 別のペインから実行
nc -zv $(vpnip) 4444

```

---

### 3. リバースシェルの「導通」テスト (tcpdump)

「ペイロードは実行したはずなのに、シェルが返ってこない」という時の最強のデバッグ手段です。

```bash
# VPNインターフェース(tun0)を流れる特定のポートの通信を監視
# これを実行したままペイロードを叩き、パケットが来れば「相手は送っている」と判断できる
sudo tcpdump -i tun0 port 4444

```

---

### 4. 認証・権限のテスト (NetExec / Impacket)

取得した認証情報（ユーザー名・パスワード）が有効かどうかをテストします。

```bash
# SMBの認証テスト（Pwn3d! が出れば管理者権限あり）
nxc smb $ip -u 'user' -p 'pass'

# WinRM が有効でログイン可能かテスト
nxc winrm $ip -u 'user' -p 'pass'

# Kerberos プレ認証が通るか確認
impacket-GetNPUsers -dc-ip $ip -no-pass -usersfile users.txt domain.local/

```

---

### 5. 転送したファイルの整合性テスト

ファイルをターゲットに送ったあと、壊れていないか確認するためにハッシュ値を取ります。

```bash
# Kali 側
md5sum binary.exe

# Windows (PowerShell) 側
Get-FileHash -Algorithm MD5 .\binary.exe

```

---

### 6. ネットワークとサービスの統合確認フロー

1. **サービスの確認:** `sudo systemctl status apache2` (サービス自体は動いているか？)
2. **ポートの開放確認:** `ss -antp | grep 80` (自分のマシン内でポートがListenしているか？)
3. **FWの確認:** `sudo ufw status` (外部からのパケットを遮断していないか？)
4. **外部からの疎通確認:** 別のターミナルから `nc -zv $(vpnip) 80` (自分自身に外から繋がるか？)

### 最後に：OSCP試験当日のルーティン

1. **Kitty**を起動し、**Tmux**を開始。
2. 各ペインで `target <IP>` を実行。
3. `Prefix + Shift + p` で**ロギングを開始**したことを確認。
4. そこから `rscan` (RustScan) で偵察開始！
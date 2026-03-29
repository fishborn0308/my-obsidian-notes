# SSHトンネリング ﾁｰﾄｼｰﾄ

## 0. 立ち上げ

まず対象セッションを作り、必要なら infra 側も起動します。`targettmux` は `TARGET_IP / TARGET_DIR / OUT / LOG / ASSETS` などを tmux セッション単位で保持するので、以後のコマンドがそのまま使えます。`pserv`, `pserv80`, `udot`, `vpnip`, `setup_ligolo`, `chisel_srv` も既に alias / 関数として入っています。 

```zsh
targettmux 10.10.10.10 box
scaninit
ports-serv
nextsvc

# 別でinfraを開く
infratmux
```

---

## 1. まず結論：L / R / D / Jump / sshuttle の使い分け

### Local Forward `-L`

**Kali から見えない内部サービスを、Kali の localhost に引っ張ってくる** 時に使います。
Web 管理画面、DB、Redis、Jenkins、内部 8000/8080/3306/5432 で一番使います。

```zsh
ssh -N -L 8080:127.0.0.1:80 user@"$TARGET_IP"
```

Kali 側でアクセス:

```zsh
curl http://127.0.0.1:8080
```

典型例:

```zsh
# ターゲット自身の127.0.0.1:80をKaliの8080へ
ssh -N -L 8080:127.0.0.1:80 user@"$TARGET_IP"

# ターゲットから見える内部ホスト10.10.20.15:8080をKaliの8081へ
ssh -N -L 8081:10.10.20.15:8080 user@"$TARGET_IP"

# 内部DB
ssh -N -L 3306:127.0.0.1:3306 user@"$TARGET_IP"
ssh -N -L 5432:127.0.0.1:5432 user@"$TARGET_IP"
```

バックグラウンド化:

```zsh
ssh -fN -L 8080:127.0.0.1:80 user@"$TARGET_IP"
```

---

### Remote Forward `-R`

**ターゲット側から見て、Kali の待受ポートを引っ張る** 時に使います。
リバースシェル受け、Kali の HTTP サーバ配布、Burp 的な待受確認で有効です。

```zsh
ssh -N -R 9001:127.0.0.1:9001 user@"$TARGET_IP"
```

上は「ターゲット側の `127.0.0.1:9001` に来た通信を、Kali 側の `127.0.0.1:9001` に返す」形です。

Kali 側で先に待受:

```zsh
ss -tulnp | grep 9001
nc -lvnp 9001
```

Kali の HTTP server をターゲット側へ見せたい時:

```zsh
# Kali側
pserv

# SSH reverse
ssh -N -R 8000:127.0.0.1:8000 user@"$TARGET_IP"
```

ターゲット側では:

```zsh
curl http://127.0.0.1:8000
```

あなたの環境では HTTP 配布役として `pserv` / `pserv80` / `udot` をすでに持っているので、`-R` と組み合わせると転送がかなり楽です。 

---

### Dynamic Forward `-D`

**SOCKS プロキシを生やして、複数の内部宛通信をまとめて流す** 時に使います。
単発ポート転送ではなく、内部探索・ブラウザ・proxychains 向けです。

```zsh
ssh -N -D 1080 user@"$TARGET_IP"
```

使いどころ:

* 内部 Web を複数見る
* `nxc`, `crackmapexec` 相当の横断系で socks を使う
* `proxychains` で内部へ投げる
* ブラウザを SOCKS に向ける

例:

```zsh
proxychains nmap -sT -Pn 10.10.20.15 -p 80,443
proxychains curl http://10.10.20.15:8080
```

---

### Jump Host `-J`

**踏み台経由で別ホストへ SSH 接続したい** 時に使います。
単純な 1 段踏み台ならこれが最もきれいです。

```zsh
ssh -J user1@"$TARGET_IP" user2@10.10.20.15
```

ポート転送と併用:

```zsh
ssh -J user1@"$TARGET_IP" -N -L 8080:127.0.0.1:80 user2@10.10.20.15
```

複数段:

```zsh
ssh -J userA@jump1,userB@jump2 userC@finalhost
```

---

### sshuttle

**“雑に VPN っぽく” 内部セグメントへ入れたい** 時に使います。
SOCKS より透過的で、内部探索が長引く時に便利です。

```zsh
sshuttle -r user@"$TARGET_IP" 10.10.20.0/24
```

複数セグメント:

```zsh
sshuttle -r user@"$TARGET_IP" 10.10.20.0/24 10.10.30.0/24
```

これは `infra` セッション側で張っておくと、`target` 側の scan / enum / notes を汚しにくいです。infra はもともと VPN / HTTP server / pivot / Transfer 用に分ける設計です。

---

## 2. 実戦での選び方

### ケース1: 内部の localhost 管理画面を見たい

`-L`

```zsh
ssh -fN -L 8080:127.0.0.1:80 user@"$TARGET_IP"
firefox http://127.0.0.1:8080
```

### ケース2: 内部の別ホスト 10.10.20.15:8443 を見たい

`-L`

```zsh
ssh -fN -L 8443:10.10.20.15:8443 user@"$TARGET_IP"
firefox https://127.0.0.1:8443
```

### ケース3: ターゲットから Kali の nc リスナーへ返したい

`-R`

```zsh
# Kali
nc -lvnp 4444

# SSH
ssh -fN -R 4444:127.0.0.1:4444 user@"$TARGET_IP"
```

### ケース4: ターゲットから Kali のファイル配布 HTTP を使いたい

`-R`

```zsh
# Kali
pserv

# SSH
ssh -fN -R 8000:127.0.0.1:8000 user@"$TARGET_IP"
```

### ケース5: 内部 Web を何台も見る

`-D` または `sshuttle`

```zsh
ssh -N -D 1080 user@"$TARGET_IP"
```

または

```zsh
sshuttle -r user@"$TARGET_IP" 10.10.20.0/24
```

### ケース6: 踏み台 1 台の先にさらに SSH したい

`-J`

```zsh
ssh -J user1@"$TARGET_IP" user2@10.10.20.15
```

---

## 3. すぐ使える定型コマンド集

### Web

```zsh
ssh -fN -L 8080:127.0.0.1:80 user@"$TARGET_IP"
ssh -fN -L 8443:127.0.0.1:443 user@"$TARGET_IP"
ssh -fN -L 8081:10.10.20.15:8080 user@"$TARGET_IP"
```

### DB

```zsh
ssh -fN -L 3306:127.0.0.1:3306 user@"$TARGET_IP"
ssh -fN -L 5432:127.0.0.1:5432 user@"$TARGET_IP"
ssh -fN -L 6379:127.0.0.1:6379 user@"$TARGET_IP"
```

### Reverse shell / listener

```zsh
ssh -fN -R 4444:127.0.0.1:4444 user@"$TARGET_IP"
ssh -fN -R 9001:127.0.0.1:9001 user@"$TARGET_IP"
```

### ファイル配布

```zsh
pserv
ssh -fN -R 8000:127.0.0.1:8000 user@"$TARGET_IP"

pserv80
ssh -fN -R 80:127.0.0.1:80 user@"$TARGET_IP"
```

### SOCKS

```zsh
ssh -fN -D 1080 user@"$TARGET_IP"
```

### 踏み台

```zsh
ssh -J user1@"$TARGET_IP" user2@10.10.20.15
ssh -J user1@"$TARGET_IP" -L 8080:127.0.0.1:80 user2@10.10.20.15
```

### VPN風

```zsh
sshuttle -r user@"$TARGET_IP" 10.10.20.0/24
```

---

## 4. あなたの tmux 運用での置き場

### target セッション側

* `scaninit`
* `ports-serv`
* `nextsvc`
* 認証確認
* 単発 `ssh -L`

### infra セッション側

* 長時間生かす `ssh -L/-R/-D`
* `sshuttle`
* `pserv`, `pserv80`, `udot`
* `setup_ligolo`
* `chisel_srv`

この分離は、あなたの tmux 設計と完全に噛み合っています。`infra` はもともと VPN / HTTP server / pivot / Transfer 用で、`target` は対象ごとの scan / enum / notes 用です。 

---

## 5. トラブルシュート

### Kali 側で本当に待受しているか

```zsh
ss -tulnp | grep 8080
ss -tulnp | grep 4444
```

### VPN 側 IP の確認

```zsh
vpnip
ip -br -4 a
```

### 外から見えるか

```zsh
nc -zv "$(vpnip)" 4444
nc -zv 127.0.0.1 8080
```

### パケットが来ているか

```zsh
sudo tcpdump -i tun0 port 4444
sudo tcpdump -i tun0 port 8000
```

### FW 確認

```zsh
sudo ufw status
sudo ufw allow 4444/tcp
```

あなたの基本コマンド集にも `ss`, `nc -zv $(vpnip)`, `tcpdump -i tun0 port <port>`, `ufw` 系の確認手順が整理されています。 

---

## 6. 証跡の残し方

トンネルを張ったら、少なくとも次は残しておくと後で迷いません。

```text
- どの踏み台に張ったか
- L / R / D / J / sshuttle のどれか
- ローカル側ポート
- 宛先 host:port
- 認証方式（password / key）
- 成功確認コマンド
```

あなたの環境では `fshot` で証跡を `assets/` に保存でき、`l2o` でログを Obsidian に流せます。`ASSETS` や `TARGET_DIR` も tmux 環境変数から同期される前提です。 

---

## 7. あなた向け最短ルーチン

```zsh
targettmux 10.10.10.10 box
scaninit
ports-serv
nextsvc

# 例: 内部Web確認
ssh -fN -L 8080:127.0.0.1:80 user@"$TARGET_IP"
curl http://127.0.0.1:8080

# 例: KaliのHTTPをターゲットへ
infratmux
pserv
ssh -fN -R 8000:127.0.0.1:8000 user@"$TARGET_IP"

# 例: 内部網を広く触る
sshuttle -r user@"$TARGET_IP" 10.10.20.0/24
```

---

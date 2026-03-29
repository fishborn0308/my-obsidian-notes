# RDP チートシート（あなたの環境向け）

> 対象: 許可された演習環境 / HTB / THM / OSCP系ラボ
> 主用途:
>
> * 3389 の発見
> * RDP の列挙
> * 認証付き接続
> * WinRM / SMB と比較した優先度判断
> * 証跡保存とメモ反映

---

## 1. 初動フロー

あなたの普段の流れでは、まず target コンテキストを作ってからスキャンに入るのが自然です。`targettmux` で target 専用 tmux session を作り、`scaninit` で初動スキャン、`ports-init` / `ports-serv` / `nextsvc` で次アクションを確認する運用になっています。`next` 系では **3389 が見えたら RDP → xfreerdp** を候補として出す設計です。 

```zsh
targettmux 10.10.10.10 dc01
scaninit
ports-init
ports-serv
nextsvc
fullcheck
```

RDP が見えた後の基本判断:

```text
3389 only      → RDP資格情報の有無確認
3389 + 445     → SMB列挙も並行
3389 + 5985/6  → WinRMも候補
3389 + 88/389  → AD文脈を強く疑う
```

---

## 2. Discovery / Enumeration

### 2-1. まずは service scan の結果を見る

```zsh
ports-serv
nextsvc
```

あなたの `nextsvc` / `log2obsidian.sh` は、`ms-wbt-server` を見つけると `[RDP] → xfreerdp / creds / NLA` を次アクションに出すようになっています。 

---

### 2-2. Nmap による基本確認

```zsh
nmap -Pn -n -p 3389 -sV "$TARGET_IP" -oA "$OUT/nmap_rdp_basic"
```

### 2-3. 暗号化方式の列挙

```zsh
nmap -Pn -n -p 3389 --script rdp-enum-encryption "$TARGET_IP" -oA "$OUT/nmap_rdp_crypto"
```

### 2-4. NTLM / ホスト情報の確認

```zsh
nmap -Pn -n -p 3389 --script rdp-ntlm-info "$TARGET_IP" -oA "$OUT/nmap_rdp_ntlm"
```

### 2-5. 専用スキャナがある場合

```zsh
rdpscan "$TARGET_IP"
```

---

## 3. 認証と接続

RDP は、あなたの構成では **Kali 側から xfreerdp** が第一候補です。
一方で、Windows ハブも使っているので、必要なら Windows 側から `mstsc` を使うのも自然です。Mac → Windows → Kali という全体アーキテクチャを持っているため、接続経路の切り替えは実務上ありです。

### 3-1. xfreerdp 基本

```zsh
xfreerdp /u:Administrator /p:'Passw0rd!' /v:$TARGET_IP
```

### 3-2. ドメイン指定

```zsh
xfreerdp /d:corp.local /u:alice /p:'Passw0rd!' /v:$TARGET_IP
```

### 3-3. 実戦向けの定番オプション

```zsh
xfreerdp /u:alice /p:'Passw0rd!' /v:$TARGET_IP /dynamic-resolution +clipboard /cert:ignore
```

### 3-4. NLA / TLS で詰まるとき

```zsh
xfreerdp /u:alice /p:'Passw0rd!' /v:$TARGET_IP /sec:nla /cert:ignore
xfreerdp /u:alice /p:'Passw0rd!' /v:$TARGET_IP /sec:tls /cert:ignore
xfreerdp /u:alice /p:'Passw0rd!' /v:$TARGET_IP /sec:rdp /cert:ignore
```

### 3-5. 画面サイズ固定

```zsh
xfreerdp /u:alice /p:'Passw0rd!' /v:$TARGET_IP /size:1440x900 /cert:ignore
```

### 3-6. レガシー fallback

```zsh
rdesktop -u alice -p 'Passw0rd!' -g 1280x720 "$TARGET_IP"
```

### 3-7. Windows 側から接続する場合

```powershell
mstsc /v:10.10.10.10
mstsc /v:10.10.10.10:3389
```

---

## 4. クリップボードより安定しやすい運用

あなたの構成は Mac / Windows / Kali の多段運用なので、RDP 接続中のデータ受け渡しを **クリップボードだけに依存しない** 方が実務向きです。Kali には `~/Transfer` 系の置き場があり、`infratmux` でも transfer 用ペインを使う設計になっています。 

### 4-1. xfreerdp でディレクトリ共有

```zsh
xfreerdp /u:alice /p:'Passw0rd!' /v:$TARGET_IP /drive:transfer,$HOME/Transfer /cert:ignore
```

### 4-2. 受け渡し後の整合性確認

```zsh
md5sum payload.exe
```

Windows 側:

```powershell
Get-FileHash -Algorithm MD5 .\payload.exe
```

整合性確認コマンドは、あなたの基本メモにもある運用です。

---

## 5. パスワード確認・認証試行

資格情報があるなら、まず **単発接続** を優先。
総当たり系はラボで明示的に許可されている場合だけ使う方がよいです。

### Hydra 例

```zsh
hydra -t 4 -V -f -l administrator -P "$PASSES/default-creds.txt" rdp://$TARGET_IP
```

あなたの環境では `PASSES` と `SECLISTS` が既に変数化されているので、辞書の置き場はそこに揃えるのが自然です。

別の辞書へ差し替えるなら:

```zsh
getlist
hydra -t 4 -V -f -l administrator -P ./wordlist rdp://$TARGET_IP
```

---

### 6. Post-Exploitation（事後操作・権限維持）
アクセス権を得た後のセッション操作やバックドア化の手法

####  セッションハイジャック (Shadowing)

Windowsの内蔵コマンド `tscon` を使用して、既存のユーザーセッションを奪取します（管理者権限が必要な場合があります）。

* **セッションの切り替え:**
```zsh
tscon <SessionID> /dest:console
```
* **セッションの切断:**
```zsh
tsdiscon <SessionID>
```

#### 権限維持 (Persistence)

* **RDPWrap:**
    Windows Home版など本来RDPサーバー機能がないOSでRDPを有効化したり、複数ユーザーの同時接続を可能にするツールです。
    
```zsh
rdpwrap # （ツールの実行・設定）
```
## 7. RDP が見えたときの優先順位

### 優先高

```text
3389 + 445/139   → SMB列挙、共有、認証再利用
3389 + 5985/5986 → WinRM接続可否
3389 + 88/389    → ADの可能性、ドメイン名・ユーザー文脈の確認
```

### 優先中

```text
3389 単独         → まず資格情報の有無
3389 + Web        → Webから得た creds の横展開
```

### 優先低

```text
3389 があるだけで即RDP突破に固執
```

あなたの `next` / `nextsvc` も、RDP を単独で絶対優先にせず、WinRM や AD、SMB と並べて評価する流れになっています。

---

## 8. 証跡の残し方

GUI操作が絡むので、RDP は **コマンドログよりスクリーンショット中心** が扱いやすいです。
あなたの環境では `assets/` が target 配下にあり、`fshot` で Obsidian へ貼りやすい形式のリンクをクリップボードに入れる運用です。 

### 推奨タイミング

```text
- ログオン成功直後
- hostname / whoami / ipconfig 確認時
- 資格情報再利用の根拠が見えた時
- 管理者権限確認時
```

### スクショ

```zsh
fshot
```

### ノートに追記

RDP 自体は GUI 中心なので、`l2o` よりも手動メモ + スクショが主です。
ただしスキャン結果からの auto block 更新には `scan-note` / `nextsvc-note` が使えます。

```zsh
scan-note
nextsvc-note
full-note
```

---

## 9. トラブルシューティング

### 9-1. xfreerdp が失敗する

まず試す順:

```zsh
xfreerdp /u:alice /p:'Passw0rd!' /v:$TARGET_IP /cert:ignore
xfreerdp /u:alice /p:'Passw0rd!' /v:$TARGET_IP /sec:nla /cert:ignore
xfreerdp /u:alice /p:'Passw0rd!' /v:$TARGET_IP /sec:tls /cert:ignore
xfreerdp /u:alice /p:'Passw0rd!' /v:$TARGET_IP /sec:rdp /cert:ignore
```

### 9-2. 3389 が本当に開いているか再確認

```zsh
ss -tulnp | grep 3389
nmap -Pn -n -p 3389 -sV "$TARGET_IP"
```

### 9-3. ホスト名解決や疎通の確認

```zsh
getent hosts dc01.corp.local
ping -c 3 "$TARGET_IP"
traceroute -n "$TARGET_IP"
```

これらの疎通確認系は、あなたの基本コマンド集に沿った確認方法です。

### 9-4. VMware 側の不調

Kali 側ネットワークが怪しいときは:

```zsh
sudo systemctl restart NetworkManager
```

VMware 環境でのネットワーク不調に対して、あなたのセットアップガイドでもこの対処を置いています。

### 9-5. 長時間作業で止めない

```zsh
xset s off -dpms s noblank
sudo systemctl mask sleep.target suspend.target hibernate.target hybrid-sleep.target
```

長時間の列挙や接続検証を中断させない前提は、あなたの Kali セットアップでも明示されています。 

---

## 10. あなた向けの最小運用セット

```zsh
targettmux 10.10.10.10 dc01
scaninit
ports-serv
nextsvc

nmap -Pn -n -p 3389 --script rdp-enum-encryption,rdp-ntlm-info "$TARGET_IP" -oA "$OUT/nmap_rdp_enum"

xfreerdp /u:alice /p:'Passw0rd!' /v:$TARGET_IP /dynamic-resolution +clipboard /cert:ignore

fshot
```

---

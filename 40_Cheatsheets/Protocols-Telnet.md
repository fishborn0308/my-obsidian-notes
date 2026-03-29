
# Telnet Cheat Sheet

> 対象: 許可された演習環境 / 検証環境
>
> 主用途:
> - Telnet サービスの存在確認
> - banner / prompt / 認証画面の確認
> - default / weak credential の検証
> - ログイン後の権限・設定確認
> - Telnet クライアントを使った他TCPサービスの疎通確認

---

## 0. 前提

この環境では、まず target 系コマンドで対象をセットする。

```zsh
targettmux 10.10.10.10 targetname
```

または

```zsh
target 10.10.10.10 targetname
```

これにより以下が自動セットされる。

- `$TARGET_IP`
- `$TARGET_NAME`
- `$TARGET_DIR`
- `$OUT`
- `$LOG`
- `$ASSETS`

作業ディレクトリは概ね `~/Vault/Target/$TARGET_IP/` 前提。

---

## 1. 推奨フロー

### 1-1. 初動

```zsh
scaninit
ports-init
ports-serv
nextsvc
```

### 1-2. Telnet が見えたら手動列挙へ

`nextsvc` の思想としても Telnet は:

- manual login check first
- default / weak creds
- inspect banner / prompt

という扱い。

### 1-3. 並行タスク

```zsh
fullcheck
```

必要時のみ:

```zsh
ar_f_nmap
udp-top
```

> Telnet 単体で深追いしすぎず、裏では full scan を確認する。

---

## 2. Discovery

### 2-1. Nmap結果からTelnet確認

```zsh
grep -Ei 'telnet|23/tcp' "$OUT"/nmap_service.nmap
```

または

```zsh
ports-serv
```

### 2-2. 直接接続

```zsh
telnet "$TARGET_IP" 23
```

### 確認ポイント

- 接続できるか
- banner が出るか
- `login:` / `username:` / `password:` が出るか
- 接続直後に閉じるか
- 文字化けや独自CLIがあるか
- 機器系CLIか、Unix/Linuxログインか

### 観察メモ

- Banner:
  - `<記入>`
- Login Prompt:
  - `<あり / なし>`
- Password Prompt:
  - `<あり / なし>`
- Device / OS Hint:
  - `<記入>`

---

## 3. Enumeration

### 3-1. banner / prompt / 挙動確認

```zsh
telnet "$TARGET_IP" 23
```

### 見るもの

- 製品名
- ベンダー名
- ホスト名
- firmware / OS のヒント
- 認証前に見える情報
- 失敗時メッセージ差分
- ログイン成功後の prompt 変化

### 3-2. Nmap補助確認

```zsh
nmap -Pn -n -sV -p 23 "$TARGET_IP" -oA "$OUT/nmap_telnet"
```

必要なら NSE を確認:

```zsh
locate .nse | grep telnet
```

### 3-3. 他サービス疎通確認に Telnet クライアントを使う

```zsh
telnet "$TARGET_IP" 25
```

用途:

- SMTP など TCP サービスへの手動接続
- banner 確認
- 対話疎通確認

> これは Telnet サービス列挙ではなく、Telnet クライアントを使った TCP 疎通確認。

---

## 4. Authentication Check

### 4-1. 認証画面の観察

確認事項:

- username を要求するか
- password を要求するか
- welcome string があるか
- auth failure の差分があるか
- 成功後 prompt がどう変わるか

残す項目:

- welcome string:
  - `<記入>`
- error string:
  - `<記入>`
- prompt change:
  - `<記入>`

---

### 4-2. Default / Empty Auth

#### 空パス試行候補

```text
root:
admin:
user:
guest:
test:
```

#### 定番の弱い組み合わせ

```text
root:root
root:toor
admin:admin
admin:password
user:user
user:password
guest:guest
test:test
```

#### 先に疑うもの

- ネットワーク機器の初期資格情報
- 組み込み機器の既定パスワード
- 他サービスで見つかったクレデンシャルの再利用

---

### 4-3. Credential Reuse

#### 他サービスから拾った資格情報

- [ ] FTP
- [ ] SMB
- [ ] HTTP
- [ ] SSH
- [ ] SNMP
- [ ] DB系

#### 記録表

| user | pass | source | tested |
| ---- | ---- | ------ | ------ |
| | | | [ ] |

#### Telnet再試行

```zsh
telnet "$TARGET_IP" 23
```

---

### 4-4. Brute Force / Spray

#### 重要: この環境の `getlist` の実動

```zsh
getlist
```

実際の `getlist` は **カレントディレクトリに `wordlist` というシンボリックリンクを作る**。  
`$WORDLISTS/wordlist` を直接使う実装ではないので注意。

確認:

```zsh
ls -l ./wordlist
echo "$ACTIVE_DICT"
```

#### Username list を選ぶ例

```zsh
getlist
hydra -L ./wordlist -p Password123 -t 4 -W 3 -f telnet://"$TARGET_IP"
```

#### Empty Password Spray

```zsh
hydra -L ./wordlist -p "" -t 4 -W 3 -f telnet://"$TARGET_IP"
```

#### Password list を選ぶ例

```zsh
getlist
hydra -l admin -P ./wordlist -t 4 -W 3 -f telnet://"$TARGET_IP"
```

#### 既知の user:pass コンボを使う場合

```zsh
hydra -C "$CREDS/some-creds.txt" -t 4 -W 3 -f telnet://"$TARGET_IP"
```

#### 既定系ファイルの確認

```zsh
ls "$PASSES"
ls "$CREDS"
```

#### オプションの意味

- `-t 4` : 並列数を抑えて安定重視
- `-W 3` : 待機を入れてロックアウト回避寄り
- `-f`   : 1件ヒットで停止

> OSCP系では「速さ」より「落とさない」「証跡を残す」方を優先。

---

## 5. Post-Login Observation

> ここではログイン後の確認観点だけを扱う

### 5-1. Telnet クライアント対話起動

```zsh
telnet
```

用途:

- ローカル telnet クライアント起動
- 接続先を対話的に指定したいとき

### 5-2. ログイン後に最初に見るもの

#### Linux / Unix 系なら

```text
whoami
id
hostname
uname -a
pwd
```

#### ネットワーク機器系なら

```text
show running-config
show version
show ip interface brief
show users
```

### 確認ポイント

- privilege level
- hostname
- OS / firmware
- config の閲覧可否
- ローカルユーザー
- Telnet / SSH の有効状況
- ACL / 接続元制限
- 平文資格情報の有無
- SNMP / NTP / Syslog などの運用設定

### 5-3. Linux系で追加確認したいもの

```text
cat /etc/issue
cat /etc/passwd
sudo -l
ip a
ss -tulnp
```

> Telnet で入れたとしても、即 exploit ではなく「どの権限で何が見えるか」を切り分ける。

---

## 6. この環境向けの記録方法

### 6-1. ノートの置き場

対象ノートは通常:

```text
~/Vault/Target/$TARGET_IP/$TARGET_IP.md
```

### 6-2. tmux logging を先にON

Telnet の banner / prompt / login failure を残したいなら、先に tmux logging を有効化してから操作する。

### 6-3. 最新ログを攻略メモへ追記

```zsh
l2m
```

### 6-4. Targetノート側へ反映

`scan-note` / `ports-note` / `nextsvc-note` / `full-note` は Nmap由来の情報更新向き。  
Telnet の細かい banner や認証メッセージ差分は、`Notes` や `Manual` セクションへ手動記録する。

#### 手動で残すべき項目

```text
- Telnet banner:
- login prompt:
- password prompt:
- auth failure text:
- success prompt:
- guessed device type:
- tried creds:
- valid creds:
- post-login privilege:
- config access:
```

---

## 7. 典型フロー

### Phase 1: 初動

```zsh
scaninit
ports-serv
nextsvc
```

確認:

- 23/tcp open か
- service detection で telnet と出ているか
- ほかに creds source になりそうなサービスがないか

---

### Phase 2: 認証前列挙

```zsh
telnet "$TARGET_IP" 23
```

確認:

- banner
- hostname
- vendor
- device type
- login prompt
- password prompt
- 情報漏えい

---

### Phase 3: 認証試行

1. 空パス
2. default creds
3. 他サービス creds reuse
4. 必要なら spray / brute force

---

### Phase 4: ログイン後確認

#### ネットワーク機器系

```text
show running-config
show version
```

#### Linux / Unix 系

```text
whoami
id
hostname
uname -a
sudo -l
```

確認:

- 権限
- config access
- Telnet/SSH設定
- ローカルアカウント
- 平文資格情報
- 横展開の足掛かり

---

### Phase 5: 並行確認

```zsh
fullcheck
```

必要なら:

```zsh
ar_f_nmap
```

> Telnetに集中しすぎず、後から出た追加ポートも確認する。

---

## 8. One-Liners

### Telnet 接続確認

```zsh
telnet "$TARGET_IP" 23
```

### Telnet が見えているか確認

```zsh
grep -Ei 'telnet|23/tcp' "$OUT"/nmap_service.nmap
```

### Telnet専用の補助Nmap

```zsh
nmap -Pn -n -sV -p 23 "$TARGET_IP" -oA "$OUT/nmap_telnet"
```

### Username spray

```zsh
hydra -L ./wordlist -p Password123 -t 4 -W 3 -f telnet://"$TARGET_IP"
```

### Password brute force

```zsh
hydra -l admin -P ./wordlist -t 4 -W 3 -f telnet://"$TARGET_IP"
```

### コンボファイル使用

```zsh
hydra -C "$CREDS/some-creds.txt" -t 4 -W 3 -f telnet://"$TARGET_IP"
```

### 最新ログを攻略メモへ

```zsh
l2m
```

---

## 9. 記録テンプレ

### Basic Findings

- Target:
  - `$TARGET_IP`
- Port:
  - `23/tcp`
- Banner:
  - `<記入>`
- Prompt:
  - `<記入>`
- Device Type:
  - `<Router / Switch / Appliance / Linux / Unknown>`
- Auth Required:
  - `<Yes / No>`
- Cleartext Risk:
  - `<High / Medium / Low>`

### Auth Findings

- username prompt:
  - `<Yes / No>`
- password prompt:
  - `<Yes / No>`
- error string:
  - `<記入>`
- welcome string:
  - `<記入>`
- valid creds:
  - `<user:pass / none>`

### Post-Login Findings

- Privilege:
  - `<user / enable / admin / unknown>`
- Config Access:
  - `<Yes / No>`
- Telnet Enabled:
  - `<Yes / No / Unknown>`
- SSH Enabled:
  - `<Yes / No / Unknown>`
- Reuse Value:
  - `<Low / Medium / High>`
- Notes:
  - `<記入>`

---

## 10. 優先順位

### 認証前

- [ ] banner
- [ ] hostname
- [ ] vendor
- [ ] device type
- [ ] login prompt
- [ ] password prompt
- [ ] 情報漏えい

### 認証試行

- [ ] empty password
- [ ] default credentials
- [ ] credential reuse
- [ ] spray / brute force

### ログイン後

- [ ] privilege level
- [ ] config access
- [ ] Telnet enabled
- [ ] SSH enabled
- [ ] local users
- [ ] plaintext secrets
- [ ] ACL / 接続元制限
- [ ] lateral movement material

---

## 11. Remediation View

- Telnet を無効化できるか
- SSH に移行できるか
- 管理セグメント制限があるか
- banner 最小化ができるか
- 監査ログがあるか
- 平文認証を廃止できるか

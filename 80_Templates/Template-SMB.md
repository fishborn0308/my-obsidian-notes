---
tags:
  - HTB
  - service/smb
  - protocol/smb
  - vuln/anonymous
target: <target_ip_or_name>
host: <hostname>
ip: <target_ip>
status: <todo,pending,inprogress,done>
domain: ""
workgroup: ""
os: ""
URL: ""
---
# SMB (139/445)

---

## 🔎 Basic Info

### Nmap / SMB Discovery

```bash
nmap -v -p 139,445 --script smb-os-discovery <target_ip>
```

**結果**
```text
<貼り付け>
```

### NetBIOS Scan

```bash
sudo nbtscan -r <subnet>
```

**結果**
```text
<貼り付け>
```

### OS / Host / Domain Summary

- OS:
  - `<記入>`
- Hostname:
  - `<記入>`
- Domain:
  - `<記入>`
- Workgroup:
  - `<記入>`
- SMB Signing:
  - `<Enabled / Disabled / Unknown>`
- SMBv1:
  - `<Enabled / Disabled / Unknown>`

---

# Phase 1: Initial Check（1〜3分）

## ポートとサービス確認

- [ ] `139/445` が開いている
- [ ] ホスト名取得
- [ ] ドメイン / Workgroup 取得
- [ ] OS 情報取得

### 実行コマンド

```bash
nmap -v -p 139,445 --script smb-os-discovery <target_ip>
ls -1 /usr/share/nmap/scripts/smb*
```

### メモ

- 気になった点:
  - `<記入>`
- 次に見るべきもの:
  - `<記入>`

---

# Phase 2: Anonymous / Guest Enumeration

## 匿名列挙

- [ ] `anonymous` で共有一覧取得
- [ ] `guest` で共有一覧取得
- [ ] 共有の権限確認
- [ ] ユーザー列挙の可否確認

### 実行コマンド

```bash
smbclient -L //<target_ip> -N
smbclient -L //<target_host> -N
smbclient -L <target_ip> -U guest

nmap -p 139,445 --script=smb-enum-users <target_ip>
nmap -p 139,445 --script=smb-enum-shares <target_ip>

smbmap -H <target_ip>
enum4linux -a <target_ip>
```

### 共有一覧

| Share | Anonymous | Guest | Read | Write | Notes |
|------|------|------|------|------|------|
| `<share>` | `<Y/N>` | `<Y/N>` | `<Y/N>` | `<Y/N>` | `<記入>` |
| `<share>` | `<Y/N>` | `<Y/N>` | `<Y/N>` | `<Y/N>` | `<記入>` |

### 匿名で見えたもの

- ユーザー:
  - `<記入>`
- 共有:
  - `<記入>`
- コメント / 説明:
  - `<記入>`
- 興味深いファイル / ディレクトリ:
  - `<記入>`

### 結果貼り付け

```text
<貼り付け>
```

---

# Phase 3: Authenticated Enumeration

## 認証済み列挙

- [ ] 有効資格情報で接続確認
- [ ] 共有一覧再確認
- [ ] users / sessions / pass-pol 確認
- [ ] SYSVOL / NETLOGON / C$ / ADMIN$ 確認

### 使用資格情報

- Domain: `<domain>`
- Username: `<username>`
- Password/Hash:
  - `<記入しない or ヒントだけ>`
- Auth Type:
  - `<domain / local / hash>`

### 実行コマンド

```bash
crackmapexec smb <target_ip> -u <username> -p '<password>'
crackmapexec smb <target_ip> -u <username> -p '<password>' --shares
crackmapexec smb <target_ip> -u <username> -p '<password>' --users
crackmapexec smb <target_ip> -u <username> -p '<password>' --sessions
crackmapexec smb <target_ip> -u <username> -p '<password>' --pass-pol

smbmap -H <target_ip> -u <username> -p '<password>'
smbmap -H <target_ip> -u <username> -p '<password>' -r <share_name>

smbclient //<target_host>/<share_name> -U <username>
smbclient.py [domain]/[user]:[password/password hash]@[Target IP Address]
```

### 認証後に見えた共有

| Share | Read | Write | Browse | High Value | Notes |
|------|------|------|------|------|------|
| `<share>` | `<Y/N>` | `<Y/N>` | `<Y/N>` | `<Y/N>` | `<記入>` |
| `<share>` | `<Y/N>` | `<Y/N>` | `<Y/N>` | `<Y/N>` | `<記入>` |

### 注目共有

- [ ] `SYSVOL`
- [ ] `NETLOGON`
- [ ] `C$`
- [ ] `ADMIN$`
- [ ] `Users`
- [ ] `Backups`
- [ ] `Department / Finance / IT / Scripts` 系

### 発見事項

- sessions:
  - `<記入>`
- users:
  - `<記入>`
- password policy:
  - `<記入>`
- interesting groups:
  - `<記入>`

---

# Phase 4: Share Access / File Review

## 共有内調査

- [ ] 設定ファイル確認
- [ ] スクリプト確認
- [ ] バックアップ確認
- [ ] 資格情報っぽい文字列確認
- [ ] ドキュメント / xlsx / txt / ini / xml / config / ps1 / bat を確認

### 実行コマンド

```bash
smbclient //<target_host>/<share_name> -U <username>%<password>
```

```bash
put <file>
get <file>
mask ""
recurse ON
prompt OFF
mget *
```

```bash
smbclient //<DC>/SYSVOL -U "<Domain>\\<User>"
smbclient //<Server>/<Share> -U <Username>%<Password> -c "get <RemoteFile> <LocalFile>"
smbclient //<remote_host>/<share_name> -U <username>%<password> -c "mget *"
```

### 取得ファイル

| File | Path | Why Interesting | Follow-up |
|------|------|------|------|
| `<file>` | `<path>` | `<理由>` | `<次の行動>` |
| `<file>` | `<path>` | `<理由>` | `<次の行動>` |

### 文字列チェックメモ

探すもの:
- password
- pass
- creds
- credential
- username
- user
- login
- net use
- runas
- xml
- ini
- ps1
- bat
- vbs
- kdbx
- rdp

### メモ

- 重要ファイル:
  - `<記入>`
- 資格情報候補:
  - `<記入>`
- 再利用できそうなもの:
  - `<記入>`

---

# Phase 5: Domain / AD-Oriented SMB Checks

## AD 観点での確認

- [ ] `SYSVOL` を確認
- [ ] GPP password 候補を確認
- [ ] logon script を確認
- [ ] deployment / script 配布を確認
- [ ] RID / SID 列挙を確認

### 実行コマンド

```bash
lookupsid.py [domain]/[user]:[password/password hash]@[Target IP Address]

crackmapexec smb <TARGET[s]> -u <user> -p <password> -d <domain> -M gpp_password
crackmapexec smb <TARGET[s]> -u <user> -H LMHash:NTLMHash -d <domain> -M gpp_password
```

### SYSVOL / GPP チェック結果

- Groups.xml:
  - `<あり / なし>`
- ScheduledTasks.xml:
  - `<あり / なし>`
- Services.xml:
  - `<あり / なし>`
- Drives.xml:
  - `<あり / なし>`
- Scripts:
  - `<記入>`

### 発見事項

```text
<貼り付け>
```

---

# Phase 6: Credential Reuse / Validation

## 資格情報の再利用確認

- [ ] local auth か domain auth か確認
- [ ] hash で通るか確認
- [ ] 他ホストに横展開できるか確認

### 実行コマンド

```bash
crackmapexec smb <target_ip> -u <username> -p '<password>'
crackmapexec smb <Rhost/range> -u user.txt -p '<password>' --continue-on-success
crackmapexec smb <Rhost/range> -u user.txt -p password.txt --continue-on-success
crackmapexec smb <target_ip> -u <username> -H <hash> --local-auth
crackmapexec smb <target_ip> -u <users_txt> -p '<password>' -d <domain> --continue-on-success
```

### 再利用結果

| Host | User | Auth Type | Result | Notes |
|------|------|------|------|------|
| `<host>` | `<user>` | `<password/hash/local/domain>` | `<OK/NG>` | `<記入>` |
| `<host>` | `<user>` | `<password/hash/local/domain>` | `<OK/NG>` | `<記入>` |

---

# Phase 7: Vulnerability / Legacy Checks

## 脆弱性確認

- [ ] MS17-010 確認
- [ ] SMBv1 の有無
- [ ] relay の余地確認（Signing）

### 実行コマンド

```bash
nmap -p445 --script smb-vuln-ms17-010 <target_ip>
```

### 結果

- MS17-010:
  - `<Vulnerable / Not Vulnerable / Unknown>`
- SMB Signing:
  - `<Enabled / Disabled / Unknown>`
- SMBv1:
  - `<Enabled / Disabled / Unknown>`

### メモ

- relay 可能性:
  - `<記入>`
- legacy attack 可能性:
  - `<記入>`

---

# Phase 8: Mount / Transfer

## Linux でマウント

```bash
mount.cifs //<RemoteHost>/<Share> /mnt/share -o user="<User>",pass="<Password>"
mount -t cifs //<Server>/<Share> <MountPoint> -o username=<Username>,password=<Password>
mount -t cifs -o username=<User>,password=<Password>,vers=1.0,nodfs //<target_ip>/<Share> /mnt/windows/
```

## Windows でマウント

```cmd
net use Z: \\<RemoteHost>\<Share> /user:[<domain>\]<User> <Password>
net use \\<target>\C$ /user:<admin> <password>
```

## コピー

```powershell
Copy-Item -Path <file> -Destination \\<host>\share\
Copy-Item -Path \\<Server>\<Share>\<File> -Destination <LocalPath>
robocopy <DriveLetter>:\<Path> <LocalPath> <FilePattern>
```

```cmd
copy <file_path> \\<target_host>\C$\Temp\<file_name>
```

### 搬入 / 搬出メモ

- Upload:
  - `<記入>`
- Download:
  - `<記入>`
- Mount 成功:
  - `<記入>`

---

# Phase 9: Execution Access Check

> ここでは **実行可否の確認だけ** を記録。  
> 詳細な Post-Login / 横展開は別テンプレへ。

## 実行可否

- [ ] admin 権限あり
- [ ] `psexec` 可
- [ ] `wmiexec` 可
- [ ] `smbexec` 可
- [ ] `atexec` 可
- [ ] `secretsdump` 可

### 実行コマンド

```bash
crackmapexec smb <target_ip> -u <user> -p '<password>' -x 'whoami'
psexec.py <domain>/<user>:<password>@<target_ip>
smbexec.py <domain>/<user>:<password>@<target_ip>
wmiexec.py <domain>/<user>:<password>@<target_ip>
atexec.py <domain>/<user>:<password>@<target_ip>
secretsdump.py [domain]/[user]:[password/password hash]@[Target IP Address]
```

### 実行結果

| Method | Result | Privilege | Notes |
|------|------|------|------|
| `cme -x` | `<OK/NG>` | `<user/admin/system>` | `<記入>` |
| `psexec` | `<OK/NG>` | `<user/admin/system>` | `<記入>` |
| `wmiexec` | `<OK/NG>` | `<user/admin/system>` | `<記入>` |
| `smbexec` | `<OK/NG>` | `<user/admin/system>` | `<記入>` |
| `atexec` | `<OK/NG>` | `<user/admin/system>` | `<記入>` |
| `secretsdump` | `<OK/NG>` | `<user/admin/system>` | `<記入>` |

---

# Loot / Evidence

## Loot

- Users:
  - `<記入>`
- Shares:
  - `<記入>`
- Creds:
  - `<記入>`
- Hashes:
  - `<記入>`
- Interesting files:
  - `<記入>`
- Scripts:
  - `<記入>`
- Backups:
  - `<記入>`

## Evidence

### Command Output

```text
<貼り付け>
```

### Screenshots

- `![[<screenshot1>.png]]`
- `![[<screenshot2>.png]]`

---

# Findings Summary

## 重要所見

- `<例: guest で share 一覧取得可能>`
- `<例: Finance share が read 可>`
- `<例: SYSVOL 読み取り可能>`
- `<例: GPP password 痕跡あり>`
- `<例: 管理共有 C$ にアクセス可能>`
- `<例: local admin で psexec 成功>`

## 想定攻撃パス

1. `<記入>`
2. `<記入>`
3. `<記入>`

## 次のアクション

- [ ] `<記入>`
- [ ] `<記入>`
- [ ] `<記入>`

---

# One-Liners

## Anonymous 初動

```bash
nmap -p 139,445 --script smb-os-discovery,smb-enum-shares,smb-enum-users <target_ip>
smbclient -L //<target_ip> -N
smbmap -H <target_ip>
enum4linux -a <target_ip>
```

## Creds あり

```bash
crackmapexec smb <target_ip> -u <user> -p '<password>' --shares --users --sessions --pass-pol
smbmap -H <target_ip> -u <user> -p '<password>'
smbclient //<target_host>/<share> -U <user>%<password>
```

## Admin あり

```bash
crackmapexec smb <target_ip> -u <user> -p '<password>' -x 'whoami'
secretsdump.py <domain>/<user>:<password>@<target_ip>
psexec.py <domain>/<user>:<password>@<target_ip>
```
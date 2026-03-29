# RDP + SMB + WinRM 横断判断チートシート

> 対象: 許可された演習環境 / HTB / THM / OSCP 系
> 主用途:
>
> * 445 / 3389 / 5985 / 5986 を見たときの優先順位判断
> * どの資格情報をどこへ再利用するかの判断
> * GUI接続、シェル接続、共有列挙の切り分け
> * Obsidian の Notes / Next Actions へ落とし込み

---

## 1. まず見る順番

あなたの今の設計では、Windows系サービスが見えても、いきなりRDPへ突っ込むより、**SMBで情報を増やし、WinRMは資格情報が取れたら即候補、RDPはGUIが必要な時や証拠取得時に使う**、という順が噛み合います。`next()` でも SMB は P2、WinRM と RDP は P3 です。

### 優先順位

```text
1. SMB   → 情報収集・共有・匿名可否・ドメイン文脈
2. WinRM → 資格情報があるなら即シェル候補
3. RDP   → GUI確認、操作証跡、設定確認、最終確認
```

### 基本原則

```text
- 445 が見えたら、まず SMB を触る
- 5985/5986 は「creds があるかどうか」がすべて
- 3389 は「見えているだけ」では価値が低い
- 常に creds reuse を意識する
```

---

## 2. ポート組み合わせ別の判断

### A. 445 のみ

```text
最優先: SMB
狙い: anonymous、共有、ユーザー名、ホスト名、ドメイン名、署名、Samba/Windows判定
次: 他ポート追加待ち / fullcheck
```

### B. 445 + 3389

```text
最優先: SMB
次点: RDP
理由:
- SMB でユーザー名、共有、ドメイン文脈を増やせる
- RDP は資格情報なしだと進みにくい
- SMB で得た creds を RDP へ流す方が効率的
```

### C. 445 + 5985/5986

```text
最優先: SMB
次点: WinRM
理由:
- SMB で user/domain/share を取る
- creds が出た瞬間、evil-winrm が強い
- GUI不要なら RDP より WinRM が軽い
```

### D. 445 + 3389 + 5985/5986

```text
最優先: SMB
次: WinRM
最後: RDP
理由:
- SMB が情報源
- WinRM は即シェル
- RDP は確認・GUI操作・証跡用途
```

### E. 3389 のみ

```text
最優先: RDP列挙
ただし:
- creds がなければ深追いしすぎない
- fullcheck で 445 / 5985 / 88 / 389 の追加有無を待つ
```

### F. 5985/5986 のみ

```text
最優先: WinRM接続可否
ただし:
- creds がなければ大きく進みにくい
- username / password / hash 入手元を別経路で探す
```

---

## 3. 初動コマンド

あなたの基本ルーチンはこのままでよいです。`scaninit` が初動スキャン、service scan、full scan(bg) まで流し、`ports-init / ports-serv / next / nextsvc` で次の分岐を見る構成です。Obsidian テンプレも `<auto_actions>` と Services ブロックを前提にしています。 

```zsh
targettmux 10.10.10.10 dc01
scaninit
ports-init
ports-serv
next
nextsvc
fullcheck
```

Obsidian 反映:

```zsh
scan-note
nextsvc-note
full-note
```

`log2obsidian.sh` は `nmap_service.nmap` から **[SMB] / [WINRM] / [RDP]** を抽出して `<auto_actions>` に流す構成です。

---

## 4. SMB フェーズ

SMB はこの横断判断の起点です。
目的は「攻撃」ではなく、まず **情報を増やすこと** です。

### 4-1. 基本列挙

```zsh
nmap -Pn -n -p 139,445 -sV "$TARGET_IP" -oA "$OUT/nmap_smb_basic"
enum4linux-ng -A "$TARGET_IP" | tee "$OUT/enum4linux-ng.txt"
smbclient -L //$TARGET_IP -N
```

### 4-2. 資格情報あり

```zsh
smbclient -L //$TARGET_IP -U 'user%pass'
smbclient //$TARGET_IP/C$ -U 'user%pass'
```

### 4-3. 注目点

```text
- anonymous で一覧が見えるか
- 共有名に NETLOGON / SYSVOL / Users / Backups があるか
- ドメイン名・ホスト名が取れるか
- 署名必須か
- 読み取りだけか、書き込みまでできるか
```

### 4-4. SMB から次へ進む条件

```text
- username が取れた → WinRM/RDP の候補が増える
- password / config / script が取れた → WinRM 優先
- GUIが必要な痕跡が出た → RDP 候補
```

---

## 5. WinRM フェーズ

`nextsvc()` でも **WinRM は「creds available なら evil-winrm」** という扱いです。
つまり、WinRM は「列挙対象」というより **資格情報の出口** です。 

### 5-1. 接続

```zsh
evil-winrm -i "$TARGET_IP" -u user -p 'Passw0rd!'
```

### 5-2. SSL (5986)

```zsh
evil-winrm -S -i "$TARGET_IP" -u user -p 'Passw0rd!'
```

### 5-3. ドメインユーザー

```zsh
evil-winrm -i "$TARGET_IP" -u user -p 'Passw0rd!' -r domain.local
```

### 5-4. WinRM を先に使うべき場面

```text
- creds がある
- すぐシェルが欲しい
- GUI不要
- コマンド証跡を tmux log に残したい
```

### 5-5. WinRM 接続後の最小確認

```powershell
whoami
hostname
ipconfig
whoami /groups
```

---

## 6. RDP フェーズ

RDP は **GUI確認・設定確認・証跡取得** に向いています。
あなたの環境では `assets/` と `fshot` があるので、RDP 成功後はスクリーンショット運用と相性が良いです。`~/Vault/Target/<IP>/assets` を持つ構成で、`fshot` は Obsidian 用リンクをクリップボードに入れます。 

### 6-1. 基本接続

```zsh
xfreerdp /u:user /p:'Passw0rd!' /v:$TARGET_IP /cert:ignore
```

### 6-2. 実戦向け

```zsh
xfreerdp /u:user /p:'Passw0rd!' /v:$TARGET_IP /dynamic-resolution +clipboard /cert:ignore
```

### 6-3. ドメイン指定

```zsh
xfreerdp /d:domain.local /u:user /p:'Passw0rd!' /v:$TARGET_IP /cert:ignore
```

### 6-4. NLA/TLS で詰まるとき

```zsh
xfreerdp /u:user /p:'Passw0rd!' /v:$TARGET_IP /sec:nla /cert:ignore
xfreerdp /u:user /p:'Passw0rd!' /v:$TARGET_IP /sec:tls /cert:ignore
xfreerdp /u:user /p:'Passw0rd!' /v:$TARGET_IP /sec:rdp /cert:ignore
```

### 6-5. RDP を先に使うべき場面

```text
- GUIでしか見えない設定を確認したい
- 操作結果をスクショで残したい
- WinRM は通らないがRDPは通る
- ローカルアプリやデスクトップ証跡が必要
```

### 6-6. 共有ディレクトリを渡す

```zsh
xfreerdp /u:user /p:'Passw0rd!' /v:$TARGET_IP /drive:transfer,$HOME/Transfer /cert:ignore
```

---

## 7. creds reuse の横断ルール

あなたの `next()` にも **Always: creds reuse** が入っています。ここがこの3サービス横断の中心です。

### ルール

```text
SMBで得たもの → WinRM / RDPへ
WinRMで得たもの → SMB / RDPへ
RDPで見つけたもの → SMB / WinRMへ戻す
```

### 具体例

```text
- smbclient / enum4linux-ng で user 名が取れた
  → その user で WinRM / RDP を試す

- 共有フォルダから scripts/config/notes を取得
  → password / domain / host 情報を WinRM / RDP に使う

- WinRM で whoami / hostname / groups が確認できた
  → その user で SMB の権限範囲を再確認

- RDP で GUI 上の保存済み資格情報や設定を見つけた
  → WinRM / SMB に戻して検証
```

---

## 8. どれを使うか迷ったときの早見表

```text
情報を増やしたい         → SMB
即シェルが欲しい         → WinRM
GUI確認・証跡が欲しい    → RDP
資格情報がない           → SMB優先
資格情報がある           → WinRM優先、必要ならRDP
ドメイン文脈が見えた     → SMB/LDAP/Kerberos も並行
```

---

## 9. 最小実戦セット

### 445 + 3389 + 5985 が見えたとき

```zsh
targettmux 10.10.10.10 dc01
scaninit
ports-serv
nextsvc

enum4linux-ng -A "$TARGET_IP" | tee "$OUT/enum4linux-ng.txt"
smbclient -L //$TARGET_IP -N

# creds が得られたら
evil-winrm -i "$TARGET_IP" -u user -p 'Passw0rd!'

# GUIが必要なら
xfreerdp /u:user /p:'Passw0rd!' /v:$TARGET_IP /dynamic-resolution +clipboard /cert:ignore

# 記録
fshot
nextsvc-note
full-note
```

---

## 10. Obsidian に残す観点

テンプレは `Notes` と `Next Actions` を持ち、`<auto_actions>` が自動更新される前提です。なので手動で追記すべきなのは次です。

### Notes に書く

```text
- どのサービスを起点にするか
- creds の出所
- domain / hostname / share 名
- GUI確認が必要か
- WinRM成功/失敗理由
```

### Evidence に残す

```text
- RDP ログオン成功画面
- whoami / hostname / ipconfig
- SMB 共有一覧
- WinRM 接続成功時の初期確認
```

### Next Actions に残す

```text
- SMB 再列挙
- WinRM での権限確認
- RDP での GUI設定確認
- fullcheck 後の追加ポート再評価
```

---

## 11. この環境向けの結論

この3つを横断するとき、あなたの環境では次で固定してよいです。

```text
1. SMB は入口
2. WinRM は資格情報の出口
3. RDP はGUI確認と証跡
```

つまり、

```text
445 がある → 先にSMB
creds がある → WinRM
GUI必要 / 証跡必要 → RDP
```

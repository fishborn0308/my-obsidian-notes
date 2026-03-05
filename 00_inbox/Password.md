侵入テストにおけるパスワードおよび資格情報の調査・攻撃手法について、主要なロジックとツールを整理してまとめました。

### 1. 資格情報の収集と抽出 (Credential Looting)

初期侵入後にシステム内部からパスワードやハッシュを窃取する手法です。

- **Windows (WinPEAS / Mimikatz):**
    
    - **設定ファイル:** `unattend.xml`、`sysprep.inf`、`web.config` などの自動インストールや構成ファイルに平文パスワードが含まれていないか検索します 。
        
    - **レジストリとVault:** LAPS（Local Administrator Password Solution）のパスワード、ブラウザの保存パスワード、Windows Vault、Credential Managerに保存されたトークンを調査します。
        
    - **メモリ抽出:** `Mimikatz` を使用して、LSASSメモリから平文パスワード、NTハッシュ、Kerberosチケットをダンプします。コマンド例: `sekurlsa::logonpasswords`。
        
- **Linux (LinPEAS):**
    
    - **機密ファイル:** `/etc/shadow` の読み取り試行や、`/etc/passwd` 内にパスワードハッシュが直接書き込まれていないか確認します 。
        
    - **履歴ファイル:** `.bash_history`、`.nano_history`、`.mysql_history` などのコマンド履歴から、過去に入力された平文パスワードやAPIキーを探します 。
        
    - **SSH鍵:** ホームディレクトリ内の `.ssh/id_rsa` などの秘密鍵を収集します 。
        

### 2. デフォルトおよび一般的な資格情報 (Default Credentials)

多くのサービスやデバイスには既定の資格情報が設定されており、列挙の初期段階で必ず試行すべき項目です。

|**サービス / プロトコル**|**一般的なデフォルトユーザー名 : パスワード**|
|---|---|
|**SMB / Windows**|`guest` : (空), `Administrator` : `password`, `admin`|
|**MSSQL**|`sa` : (空), `sa` : `password`, `sa` : `Password123`|
|**Oracle DB**|`sys` : `change_on_install`, `system` : `manager`, `scott` : `tiger`|
|**RDP**|`Administrator` : `admin`, `admin` : `admin`, `user` : `user`|
|**FTP**|`anonymous` : `anonymous`, `ftp` : `ftp`|
|**SNMP**|コミュニティ名: `public`, `private`|

### 3. パスワード攻撃手法 (Password Attacks)

- **ブルートフォースとスプレー:** `Hydra` や `NetExec` (nxc) を使用して、特定のアカウントへの総当たり攻撃や、複数のアカウントに対して共通のパスワードを試す「パスワードスプレー」を実施します。
    
- **Kerberos攻撃 (Active Directory):**
    
    - **Kerberoasting:** サービスアカウント（SPN）のハッシュを要求し、オフラインでクラックを試みます。
        
    - **AS-REP Roasting:** 事前認証が不要なユーザーからハッシュを窃取します 。
        
- **Pass-the-Hash (PtH):** パスワード自体が不明でも、NTハッシュがあれば SMB や RDP 経由でターゲットシステムに認証できます。
    

### 4. パスワード解析ツール (Cracking Tools)

窃取したハッシュをオフラインで解析して平文に戻すためのツールです。

- **解析ソフトウェア:**
    
    - **John the Ripper:** 多様なハッシュ形式に対応した汎用クラッカー 。
        
    - **Hashcat:** GPUを利用した高速解析ツール 。
        
- **補助ツール:**
    
    - **unshadow:** Linuxの `/etc/passwd` と `/etc/shadow` を統合し、John the Ripperで読み込める形式に変換します 。
        
    - **hash-identifier:** ハッシュ値を入力し、その暗号化アルゴリズム（MD5, SHA-1等）を特定します 。
        

### 5. 調査チェックリスト

- [ ] **空パスワード:** `nmap` スクリプト等を使用して、`sa` や `root` などの特権アカウントが空パスワードでログイン可能か確認したか？
    
- [ ] **ロックアウトポリシー:** パスワードスプレーを行う前に、アカウントのロックアウト閾値を `net accounts` 等で確認したか？
    
- [ ] **再利用:** 取得した資格情報を、他のサーバーやAD内の他ホストに対して使い回していないか確認したか？
    
- [ ] **イベントログ:** Windowsイベントログ等に、パスワードがプレーンテキストで記録される「プロセス作成監査」などの設定が有効になっていないか？
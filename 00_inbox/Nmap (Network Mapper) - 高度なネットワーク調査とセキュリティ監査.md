---
# --- YAML Frontmatter ---
created: '2024-05-22'
modified: '2024-05-22'

environment: [Network/Scanner, OS/Linux, OS/Windows, OS/macOS]

vulnearability: [Reconnaissance, Info_Gathering, Port_Scanning, Version_Detection, OS_Fingerprinting]

knowledge_category: Command

tags:
  - 'nmap'
  - 'bash'
  - 'network_security'
  - 'knowledge_base'
---

# Nmap (Network Mapper) - 高度なネットワーク調査とセキュリティ監査

## 1. 概念説明
**Nmap**は、ネットワーク探索およびセキュリティ監査を行うためのオープンソースの強力なツールです。生のIPパケットを使用して、ネットワーク上のホストの稼働状況、提供されているサービス（アプリケーション名とバージョン）、実行されているオペレーティングシステム（OS）、使用されているパケットフィルタ/ファイアウォールの種類などを特定します。

ペンテストの初期段階である「偵察（Reconnaissance）」において、攻撃表面（Attack Surface）を特定するために最も頻繁に使用されるツールの一つです。



## 2. コマンド概要とオプション解説

### 基本構文
`nmap [スキャンタイプ] [オプション] {ターゲット指定}`

### ターゲット指定
- `192.168.1.1`: 単一IP
- `192.168.1.0/24`: サブネット全体
- `192.168.1.1-100`: IP範囲
- `-iL <file>`: リストファイルから読み込み

### スキャンタイプ (重要)
| オプション | 名称 | 解説 |
| :--- | :--- | :--- |
| `-sS` | TCP SYNスキャン | **ステルススキャン**。接続を確立しないためログに残りづらく、高速で標準的。 |
| `-sT` | TCP Connectスキャン | 3ウェイハンドシェイクを完了させる。特権がない場合やIPv6で常用。 |
| `-sU` | UDPスキャン | DNS, SNMP, DHCPなどのUDPポートを調査。低速。 |
| `-sV` | サービスバージョン検出 | 開いているポートで稼働しているソフトのバージョンを特定。 |
| `-O` | OS検出 | TCP/IPスタックの挙動から相手のOSを推測。 |
| `-sA` | ACKスキャン | ファイアウォールのルールセット（ステートフルか等）を調査。 |

### ポート指定とスキャン速度
| オプション | 解説 |
| :--- | :--- |
| `-p <port>` | 特定ポート（例: `-p 80,443`）または範囲（例: `-p 1-65535`）を指定。 |
| `-p-` | 全ポート（1-65535）をスキャン。 |
| `-F` | 高速モード。トップ100ポートのみスキャン。 |
| `-T[0-5]` | タイミングテンプレート。0(最遅)〜5(最速)。実務では `-T4` が一般的。 |

### 出力オプション
| オプション | 解説 |
| :--- | :--- |
| `-oN <file>` | 通常のテキスト形式で出力。 |
| `-oX <file>` | XML形式で出力（他のツールとの連携用）。 |
| `-oG <file>` | Grep可能な形式で出力。 |
| `-oA <basename>` | 上記3つ全ての形式で同時出力。 |

### スキャンクエリの確認

`https://nmap.org/book/vscan-fileformat.html`

Nmapはnmap-service-probesというファイルを使用します。このファイルは/usr/share/nmapディレクトリに配置されている。
このファイル内で、"Probe"で始まる行はターゲットサービスに送信するメッセージを示し、"match"で始まる行はプローブへの応答に基づいてサービスを識別するためのシグネチャです。

- **カテゴリ毎のスキャンクエリの確認**

```
grep -v ^# /usr/share/nmap/nmap-service-probes | cut -d' ' -f1 | sort | uniq -c
```

- **Apacheに関連するスキャンクエリの確認**

```
grep -m1 Apache /usr/share/nmap/nmap-service-probes
```

### NSE (Nmap Scripting Engine)
`-sC` または `--script=<category>` を使用して、脆弱性診断や高度な探索を自動化します。
- `default`: 標準的なスクリプト群を実行。
- `vuln`: 既知の脆弱性をチェック。
- `auth`: 認証バイパスなどをチェック。

#### NSEスクリプトの確認

NSE用に定義された全スクリプトのインベントリを含むファイル

`/usr/share/nmap/scripts/script.db`

- **カテゴリ毎のNSEスクリプトの確認**

```
grep -v ^# /usr/share/nmap/nmap-service-probes | cut -d' ' -f1 | sort | uniq -c
```


- **カテゴリ毎のNSEスクリプトの確認**

```
grep -v ^# /usr/share/nmap/nmap-service-probes | cut -d' ' -f1 | sort | uniq -c
```

- **Apacheに関連するNSEスクリプトの確認**

```
grep -m1 Apache /usr/share/nmap/nmap-service-probes
```
---

## 3. 実務ログ例

### ケース1：ステルス全ポートサービス・OS検出
最も標準的で詳細な情報を取得するペンテストの初手です。

```bash
# コマンド実行
sudo nmap -sS -sV -O -T4 -p- 192.168.1.50 -oN scan_results.txt

# --- 実行ログ ---
Starting Nmap 7.94 ( [https://nmap.org](https://nmap.org) ) at 2024-05-22 10:00 JST
Nmap scan report for 192.168.1.50
Host is up (0.00052s latency).
Not shown: 65531 closed tcp ports (reset)
PORT     STATE SERVICE     VERSION
22/tcp   open  ssh         OpenSSH 8.9p1 Ubuntu 3ubuntu0.1 (Ubuntu Linux; protocol 2.0)
80/tcp   open  http        Apache httpd 2.4.52 ((Ubuntu))
443/tcp  open  ssl/http    Apache httpd 2.4.52 ((Ubuntu))
3306/tcp open  mysql       MySQL 8.0.33-0ubuntu0.22.04.2
Device type: general purpose
Running: Linux 5.X
OS CPE: cpe:/o:linux:linux_kernel:5
OS details: Linux 5.4 - 5.15
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at [https://nmap.org/submit/](https://nmap.org/submit/) .
Nmap done: 1 IP address (1 host up) scanned in 15.42 seconds
```

### ケース2：NSEによる脆弱性スキャン (HTTP関連)

ターゲットがWebサーバーの場合、既知の脆弱性（CVE）を自動スキャンします。

Bash

```
# コマンド実行
nmap -p 80,443 --script http-vuln-* 192.168.1.50

# --- 実行ログ ---
PORT   STATE SERVICE
80/tcp open  http
| http-vuln-cve2017-5638: 
|   VULNERABLE:
|   Apache Struts Remote Code Execution Vulnerability
|     State: VULNERABLE
|     IDs:  CVE:CVE-2017-5638
|       This vulnerability allows remote attackers to execute arbitrary commands.
```

## 4. セキュリティ上の留意点

- **許可なきスキャンは禁止**: 外部ネットワークに対するスキャンは、攻撃の準備行為とみなされ、法的な問題やIDS/IPSによる遮断を招きます。
    
- **IDSの検知**: `-T5` などの高速スキャンや `-sX` (Xmas scan) は、現代のIDSでは容易に検知されます。隠密性が求められる場合は `-T2` 以下を検討してください。
    
---


## 1. Nmap 入出力管理マニュアル

スキャン対象の効率的な読み込みと、結果の確実な保存方法です。

### ターゲットのインポート

- **`-iL [ファイル名]`**: テキストファイル形式のホストリストを読み込みます。手動入力の手間を省き、ミスを防ぎます。
    

### 出力形式の選択

|**オプション**|**形式**|**特徴・用途**|
|---|---|---|
|**`-oN`**|通常|画面表示と同じ、人間が読みやすい形式。|
|**`-oG`**|Greppable|**推奨。** 1ホスト1行で出力。`grep`や`awk`での解析に最適。|
|**`-oX`**|XML|**重要。** Metasploitなどの他ツールへのインポート用。|
|**`-oA`**|**全部入り**|指定したベース名で**.nmap, .gnmap, .xmlの3種を同時生成。**|

> [!TIP]
> 
> **「常に `-oA` を使用せよ」**
> 
> ディスク容量は解析時間よりも安価です。後でどの形式が必要になっても困らないよう、全形式保存を習慣にしましょう。

---

## 2. ホスト検出（プロービング）の仕組み

Nmapはポートスキャンの前に、対象が「生きているか」を確認します（フェーズ1）。この挙動は実行権限によって変わります。

### 実行権限によるデフォルト挙動の違い

- **UID 0 (Root/管理者):**
    
    - 同一サブネット：**ARPリクエスト**のみ。
        
    - 別サブネット：ICMPエコー、TCP SYN(443)、TCP ACK(80)、ICMPタイムスタンプ。
        
- **非特権ユーザー:**
    
    - TCP SYN(80) および TCP SYN(443) のみ（OSの接続機能を流用）。
        

### 主なプローブオプション

- **`-Pn`**: プローブをスキップし、全ホストが稼働中と仮定する（FWでPingが遮断されている場合に有効）。
    
- **`-sn`**: ポートスキャンを行わず、ホストの稼働確認（ネットワークスイープ）のみ実行。
    
- **`-PS[ポート]`**: 指定したポートへTCP SYNを送信して確認。
    
- **`-PE` / `-PP` / `-PM`**: 各種ICMP（エコー / タイムスタンプ / アドレスマスク）を使用。
    

---

## 3. DNSを活用した高速ネットワーク探索

広大なIPレンジ（10.0.0.0/8など）から、アクティブな区画を特定する高度な手法です。

### ステップ：

1. **リストスキャン（`-sL`）の実行:**
    
    ターゲットIPに直接パケットを送らず、DNSサーバーへ「逆引き（PTR）」を大量に問い合わせます。
    
    
    ```bash
    nmap -iL rfc1918.txt -sL --dns-servers [DNS_IP] -oG ptrs.gnmap
    ```
    
2. **結果の解析と抽出:**
    
    - ホスト名が解決できた（＝使用されている可能性が高い）IPを抽出し、/24形式のリストに変換
	    ```bash
	    grep -v '()' ptrs.gnmap | awk '/Host/ {split($2, ip, "."); print ip[1]"."ip[2]"."ip[3]".0/24"}' | sort -uV > nets.txt
	    ```
    
	- ポート445でリスニングしているホストの数
		```
		grep -w 445/open /tmp/scan.gnmap | wc -l
		```

	- ポート445でリスニングしているホストのIP
		```
		grep -w 445/open /tmp/scan.gnmap | cut -d ' ' -f 2
		```
---

## 4. ポートスキャンの詳細設定

ホスト検出後、どのポートをどのようにスキャンするかを制御します。

### ポート指定オプション

- **デフォルト**: 上位1,000ポート（`nmap-services`に基づく人気順）。
    
- **`-p-`**: 全ポート（1-65535）をスキャン。
    
- **`-F`**: 上位100ポートのみ（高速）。
    
- **`--top-ports N`**: 人気の上位N個のポートをスキャン。
    
- **`--open`**: 開いているポートのみ表示（結果の視認性が向上）。
    

### スキャンタイプ

- **TCPコネクトスキャン (`-sT`)**:
    
    3ウェイハンドシェイクを完遂。非特権でも動作し安全だが、接続がログに残りやすい。
    
- **SYNスキャン (`-sS`)**:
    
    **デフォルト（要Root）。** ハーフオープンスキャン。接続を完了させないため高速でログに残りづらい。
    
- **UDPスキャン (`-sU`)**:
    
    UDPは応答が遅いため時間がかかる。LinuxのICMPレート制限を回避するには `--defeat-icmp-ratelimit` が有効（ただし精度と引き換え）。
    



---

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

### NSE (Nmap Scripting Engine)
`-sC` または `--script=<category>` を使用して、脆弱性診断や高度な探索を自動化します。
- `default`: 標準的なスクリプト群を実行。
- `vuln`: 既知の脆弱性をチェック。
- `auth`: 認証バイパスなどをチェック。

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
    

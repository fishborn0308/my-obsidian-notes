---
# --- YAML Frontmatter ---
created: '2026-03-04'
modified: '2026-03-04'

environment: [Network/Scanner, OS/Linux, OS/macOS]

vulnearability: [Reconnaissance, Info_Gathering, Port_Scanning, DoS_Potential]

knowledge_category: Command

tags:
  - 'masscan'
  - 'bash'
  - 'network_security'
  - 'knowledge_base'
---

# Masscan - 世界最速級のインターネットポートスキャナー

## 1. 概念説明
**Masscan**は、極めて高速に動作するTCPポートスキャナーです。Nmapと似た操作性を持ちつつ、内部的には独自のTCP/IPスタックを実装しており、非同期送信を行うことで、理論上は**毎秒2,500万パケット**という驚異的な速度でスキャンが可能です。

Nmapが特定のホストの詳細（OSやサービス）を深く調査するのに適しているのに対し、Masscanは「広大なネットワーク（インターネット全体など）から特定のポートが開いているIPを瞬時に炙り出す」という用途に特化しています。



## 2. コマンド概要とオプション解説

### 基本構文
`masscan [ターゲット指定] -p[ポート] [オプション]`

### 必須・重要オプション
| オプション | 解説 |
| :--- | :--- |
| `-p <port>` | スキャン対象ポート（例: `-p80,443`, `-p1-65535`, `-pU:53` (UDP)）。 |
| `--rate <number>` | 送信レート（パケット/秒）。デフォルトは100。1M（100万）などの指定が可能。 |
| `--banners` | バナーグラビング（HTTP ServerヘッダやSSHバージョン等）を有効化。 |
| `--exclude <file>` | スキャン対象から除外するIPリスト（政府機関、自社資産など）を指定。 |
| `--wait <seconds>` | 最後のパケット送信後の待機時間。デフォルトは10秒。 |

### ネットワーク構成オプション
| オプション | 解説 |
| :--- | :--- |
| `--adapter-ip <IP>` | スキャンに使用する自身のIPアドレスを指定。 |
| `--adapter-port <Port>` | スキャンに使用するソースポートの範囲を指定。 |
| `--router-mac <MAC>` | ローカルルーターのMACアドレスを指定（自動検出に失敗する場合）。 |

### 出力オプション
| オプション | 解説 |
| :--- | :--- |
| `-oX <file>` | XML形式で保存。 |
| `-oJ <file>` | JSON形式で保存（解析が容易なため推奨）。 |
| `-oL <file>` | リスト形式（単純なテキスト）で保存。 |
| `-oG <file>` | Grep可能な形式で保存。 |

---

## 3. 実務ログ例

### ケース1：特定セグメントに対する全ポート高速スキャン
10.0.0.0/8 という巨大なネットワークに対し、10,000パケット/秒の速度で全TCPポートを確認します。

```bash
# コマンド実行 (管理者権限が必要)
sudo masscan 10.0.0.0/8 -p1-65535 --rate 10000 -oJ internal_scan.json

# --- 実行ログ ---
Starting masscan 1.3.2 ([http://bit.ly/14hpRfs](http://bit.ly/14hpRfs)) at 2026-03-04 01:00:00 GMT
 -- build = Mar  4 2026 10:00:00
 -- http-user-agent = masscan/1.3
Scanning 16777216 hosts [65535 ports/host]
Discovered open port 80/tcp on 10.1.20.5
Discovered open port 443/tcp on 10.1.20.5
Discovered open port 22/tcp on 10.5.100.2
Discovered open port 3389/tcp on 10.10.1.50
...
````

### ケース2：インターネット全体への特定ポート調査（バナー取得あり）

インターネット上の全てのIPに対し、ポート80の稼働状況を確認し、サーバー情報を取得します。

Bash

```
# コマンド実行
sudo masscan 0.0.0.0/0 -p80 --banners --rate 100000 --exclude exclude.txt

# --- 実行ログ ---
Discovered open port 80/tcp on 93.184.216.34
Banner on port 80/tcp on 93.184.216.34: [http.server] ECS (oxr/8323)
```

## 4. セキュリティ上の留意点

- **帯域飽和のリスク**: `--rate` を高く設定しすぎると、自組織のネットワーク帯域を使い果たしたり、上流のルーターをクラッシュさせる（DoS状態）可能性があります。
    
- **偽装IPの使用**: Masscanは独自のスタックを使うため、OSのTCPスタックを通しません。そのため、OSが想定外の応答パケットに対しRSTを送ってしまうことがあります。これを防ぐには `iptables` で該当ポートのRSTをDROPする設定が推奨されます。
    
- **法的・倫理的配慮**: インターネット全体へのスキャンは、多くの組織で攻撃予兆として検知され、プロバイダーへの通報対象となります。
    

---


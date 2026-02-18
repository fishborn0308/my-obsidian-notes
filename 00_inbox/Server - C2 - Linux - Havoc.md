---
created:
modified:
environment:
  - OS/Linux
  - Server/C2
vulnearability: []
knowledge_category: Server
tags:
  - Malware
  - C2
  - CVE-2024-41570
---

# Server - Havoc (Technical Reference)

## 概要

Havocは最新のエンドポイントセキュリティ環境下での運用を想定して設計されたオープンソースのポストエクスプロイテーション・フレームワークである  
商用C2フレームワークの代替としてレッドチームやペネトレーションテスト用途で利用される

> ⚠ 本ドキュメントは防御・検知研究を目的とした技術解説である  
> 許可のない環境での使用は禁止する

---

### 1. 主要コンポーネント

- **Teamserver（Golang）**  
  C2サーバー本体  
  マルチユーザー対応  
  エージェントとの通信管理、タスク配信、ログ保持を担当する  

- **Client（C++ / Qt）**  
  オペレーター用GUIクライアント  
  Teamserverへ接続し、セッション管理・ペイロード生成・リスナー設定を行う  

- **Demon（C++ / ASM）**  
  ターゲット端末上で動作するエージェント  
  インジェクション、プロセス操作、メモリ実行などを実行する  

---

### 2. 主な回避技術（Evasion）と技術的補足

Havocには複数の検知回避技術が実装されています。ただし、いずれも**万能ではなく、検知可能性は十分に存在する**

#### 2.1 Sleep Obfuscation

- 待機中のペイロードコードやヒープ領域を暗号化
- スリープ復帰時に復号
- メモリスキャン型EDRへの耐性向上を目的

**注意点**
- メモリ保護属性の変更（RW → RX など）
- 不自然なタイマーキュー利用
- NtDelayExecution の高頻度利用

などの挙動は依然として検知対象となり得る

---

#### 2.2 Indirect Syscalls

##### 背景

多くのEDRは `ntdll.dll` 内のネイティブAPI（例：`NtCreateThreadEx`）にユーザーモードフックを挿入し、引数や呼び出し元を監視

##### Direct Syscalls の課題

Direct Syscallsでは：

- プログラム自身のメモリ領域に `syscall` 命令を配置
- `ntdll.dll` を経由せず直接カーネルへ遷移

一部のEDRでは：

- 「ntdll外からのsyscall実行」
- 不自然なスタックトレース

を検知対象としています。

※ ただし、すべてのEDRが同一ロジックではない

##### Indirect Syscalls の仕組み

- システムコール番号をレジスタ（例：EAX）に設定
- 自身のコード内で `syscall` を実行せず
- `ntdll.dll` 内の既存 syscall stub へジャンプ（jmp）

##### 回避の原理

- syscall命令自体は `ntdll.dll` 内の正規領域を経由
- 単純な「ntdll外syscall検知」を回避しやすい

##### 限界

- Return Address は攻撃コード側
- スタックウォークやCall Stack整合性検査で検知可能
- カーネルモード監視では無効

→ 「完全に正規呼び出しに見える」わけではない

---

#### 2.3 AMSI / ETW Patching

- AMSI（Antimalware Scan Interface）の無効化
- ETW（Event Tracing for Windows）のイベント送出阻害

**防御観点**

- AMSI関連メモリ改変
- `EtwEventWrite` のパッチ検出
- イメージ整合性検査

により検知可能な場合がある

---

## 🛠️ 構築（概要）

※ ビルド手順は公式リポジトリに従うこと

- Go環境
- Qt開発環境
- 各種C++ビルド依存関係

Teamserver起動時は：

- リスナーポートの制限
- VPN内限定公開
- IP制限

を強く推奨

---

### 1\. 環境構築 (Qt6対応)

最新のHavocはQt6に依存しています。以下の手順でビルド環境を整える

```bash
# 依存パッケージのインストール (Qt6および最新ライブラリ)
sudo apt update && sudo apt install -y git build-essential cmake libfontconfig1 libglu1-mesa-dev libspdlog-dev libboost-all-dev libncurses5-dev libssl-dev libffi-dev libsqlite3-dev python3-dev qt6-base-dev libqt6svg6-dev qt6-declarative-dev

# リポジトリのクローン
git clone https://github.com/HavocFramework/Havoc.git
cd Havoc

# Teamserverのビルド
cd Teamserver
go mod download
make server

# Clientのビルド
cd ../Client
make client
```

### 2\. Demon（エージェント）の生成

1.  **Clientの起動:** `./Havoc` でGUIを起動し、Teamserverに接続
2.  **Listenerの作成:** `Payload` \> `Listeners` からHTTP/HTTPSリスナーを設定
3.  **Payload設定:** `Payload` \> `Generator` を選択
      * **Injection:** `Syscall` オプションで `Indirect` を選択（推奨）
      * **Sleep:** `Jitter`（揺らぎ）を設定し、ビーコン（定期通信）のパターン検知を回避
4.  **出力:** `Generate` を押し、バイナリ（.exe / .dll）を生成

---
## 防御側（Blue Team）の視点

### ネットワーク・エンドポイント痕跡

| カテゴリ | 指標 | 技術的補足 |
|----------|------|------------|
| ネットワーク | TCP 40056 | Teamserverのデフォルトポート（変更可能） |
| ネットワーク | 固定マジック値 | 通信プロトコル識別子として利用される場合あり |
| プロセス | CreateRemoteThread | 他プロセスへのコード注入 |
| Sysmon | Event ID 8 | Remote Thread 作成 |
| Sysmon | Event ID 10 | 不審な Process Access |
| Sysmon | Event ID 7 | 署名なしモジュールロード |

※ デフォルト設定前提であり、実運用ではカスタマイズ可能

### ネットワーク層における検知コンポーネント

Havocの検知はエンドポイントログだけでなく、ネットワーク指紋分析との相関が重要である  
暗号化通信であっても挙動と統計的特徴から識別可能である  

---

#### 1. JA3 / JA3S フィンガープリント分析

##### 概要

JA3はTLS ClientHelloの以下要素をハッシュ化した指紋技術である  

- TLS Version  
- Cipher Suites  
- Extensions  
- Elliptic Curves  
- EC Point Formats  

JA3Sはサーバ側の応答指紋である  

* **JA3 (クライアント側):** Demon（Windowsバイナリ）が使用するTLSライブラリや暗号スイートの組み合わせ
    * **特徴:** Windowsの正規の更新プロセスやブラウザとは異なる、特有のハッシュ値が生成されることがある

* **JA3S (サーバー側):** Teamserverが応答する際の指紋
    * **検知のポイント:** 特定のJA3とJA3Sの組み合わせ（ペア）をブラックリスト化することで、IPアドレスが変わってもHavocのインフラを特定できる

##### 検知観点

- 社内標準ブラウザのJA3ホワイトリスト構築  
- 未知JA3の外向き通信検知  
- 特定JA3 × JA3Sペアのブラックリスト化  
- 通常ブラウザと異なる暗号スイート優先順位  

##### 運用ポイント

- 単独指標としては弱いため他ログとの相関が必要  
- CDNやリバースプロキシ経由で変化する可能性あり  
- 長期ベースライン構築が有効  

---

#### 2. TLS自己署名証明書パターン

デフォルト設定のTeamserverは自己署名証明書を使用するケースがある  

##### 検知ポイント

- Issuer = Subject
  - デフォルトの生成スクリプトによる値
	- `C=US, ST=California, L=San Francisco, O=Havoc, CN=Havoc` 
- 組織と無関係なCN  
- SAN未設定または不自然な値  
- 極端に短期または長期の有効期限  
  - 攻撃者が使い捨てにするため
- JARM
  - サーバーのTLSスタックに対するアクティブスキャン
    - Teamserver固有の応答パターンを特定可能

##### 相関分析

- 新規ドメイン × 自己署名証明書  
- JA3異常 × 自己署名TLS  
- 内部端末から直接外部自己署名TLS通信  

##### 防御施策

- SSLインスペクション  
- 証明書フィンガープリント監視  
- CTログ照合  

---

#### 3. HTTPヘッダ特徴分析

HavocはHTTPプロファイルをカスタマイズ可能だが、挙動の癖が残る  

##### 3.1 User-Agent異常

- OSと整合しないUA  
- 古いバージョン固定  
- 組織標準と一致しないUA  
- 頻度とブラウザ挙動の不一致  

##### 3.2 ヘッダ順序異常

実ブラウザはヘッダ順序がほぼ固定である  

例：

```
Host
Connection
Upgrade-Insecure-Requests
User-Agent
Accept
Accept-Encoding
Accept-Language
```

異常例  

- 順序ランダム  
- 必須ヘッダ欠落  
- Accept系ヘッダ不自然  
- デフォルトContent-Type固定  

##### 3.3 コンテンツ長と周期性

- 極小サイズPOSTの周期送信  
- 一定バイト長の暗号化データ  
- Jitter付きでも統計的周期性が残存  
- 平常時とタスク実行時のサイズ差  

NetFlowやZeekでフロー統計分析可能  

---

### IDSによる検知アプローチ

#### 1. シグネチャ設計方針

- 固定値  
- HTTPヘッダ特徴  
- TLS証明書属性  
- JA3ハッシュ  
- パケットサイズ  
- 周期通信  
- 内部 → 外部フロー  

単一条件ではなく複数条件を組み合わせる  

---

#### 2. 検知例

##### デフォルトポート検知（参考レベル）

※ ポートは変更可能なため単独では弱い指標

```snort
alert tcp $HOME_NET any -> $EXTERNAL_NET 40056 \
(msg:"Possible Havoc Teamserver default port access"; \
flow:to_server,established; \
sid:100001; rev:1;)
```

##### マジック値検知（バイナリプロトコル）

通信中に特定の識別子（例：0xDEADBEEF）が含まれるケースを想定

```snort
alert tcp $HOME_NET any -> $EXTERNAL_NET any \
(msg:"Possible Havoc C2 magic value"; \
flow:to_server,established; \
content:"|DE AD BE EF|"; \
depth:4; \
sid:100002; rev:1;)
```

- 改善ポイント

  - `offset` 指定
  - `flowbits` によるセッション追跡
  - `byte_test` による追加検証

##### 小容量周期POST

小容量POSTを周期的に送信するビーコン検知例
- 300バイト未満のPOST
- 60秒以内に5回以上
- 内部端末単位で追跡

```snort
alert http $HOME_NET any -> $EXTERNAL_NET any \
(msg:"Suspicious small periodic HTTP POST"; \
flow:to_server,established; \
content:"POST"; http_method; \
dsize:<300; \
threshold:type both, track by_src, count 5, seconds 60; \
sid:100003; rev:1;)
```

##### 不審User-Agent

組織標準と異なるUAの例 ※ 実際にはホワイトリスト型検知の方が有効

```snort
alert http $HOME_NET any -> $EXTERNAL_NET any \
(msg:"Unknown or Suspicious User-Agent"; \
flow:to_server,established; \
http_header; \
pcre:"/User-Agent:\s*(curl|python|go-http-client)/i"; \
sid:100004; rev:1;)
```

##### TLS自己署名証明書検知（Suricata例）

SuricataではTLSフィールドにアクセス可能

```suricata
alert tls $HOME_NET any -> $EXTERNAL_NET any \
(msg:"Suspicious Self-Signed Certificate"; \
tls.cert_issuer; content:"CN="; \
tls.cert_subject; content:"CN="; \
flow:established; \
sid:100005; rev:1;)
```

より高度な例：

```suricata
alert tls $HOME_NET any -> $EXTERNAL_NET any \
(msg:"Self-signed TLS certificate detected"; \
tls.cert_subject; tls.cert_issuer; \
pcre:"/CN=.*$/"; \
sid:100006; rev:1;)
```

※ 実際は `issuer == subject` 比較をログ解析側で実施する方が現実的

##### JA3検知

SuricataではJA3ログ出力が可能

`suricata.yaml` で有効化：

```yaml
app-layer:
  protocols:
    tls:
      ja3-fingerprints: yes
```

検知例：

```suricata
alert tls $HOME_NET any -> $EXTERNAL_NET any \
(msg:"Unknown JA3 fingerprint"; \
ja3.hash; content:"<既知悪性JA3ハッシュ>"; \
sid:100007; rev:1;)
```

運用上は：

- 組織内JA3ホワイトリスト構築
- 未知JA3の外向き通信アラート

が効果的

---

### 🔬 パケットキャプチャと分析

#### 1. 取得

```bash
sudo tcpdump -i eth0 -w capture.pcap host <疑わしいIP>
```

特定ポート指定：

```bash
sudo tcpdump -i eth0 tcp port 40056 -w havoc_traffic.pcap
```

---

#### 2. 分析観点

##### フロー統計

* Conversations
* IO Graph
* 周期スパイク確認

##### TLS解析

* Client Hello抽出
* JA3確認
* Cipher Suite優先順位
* Issuer / Subject一致確認

##### HTTP解析

* Content-Length
* User-Agent
* ヘッダ順序
* エンコード形式

---

#### Wiresharkによる分析(例)

##### ① フロー確認

- Statistics → Conversations
- Endpoints
- IO Graph

周期通信の可視化が可能

---

##### ② TLS解析

フィルタ例：

```
tls.handshake.type == 1
```

確認項目：

- JA3値
- Cipher Suite
- SNI
- 証明書Issuer/Subject一致

---

##### ③ HTTP解析

フィルタ例：

```
http.request.method == "POST"
```

観察ポイント：

- Content-Length
- User-Agent
- ヘッダ順序
- エンコード形式

---

##### ④ 周期性分析

IO Graphで：

- 10秒～60秒間隔のスパイク
- Jitter付き周期通信

を確認

---

####  分析のポイント

##### A. ビーコンの周期性（Jitter）の観察

Wiresharkの「Statistics \> I/O Graphs」を開き、パケットの発生間隔を確認

  * **固定間隔:** `Sleep 5`（Jitter 0%）の場合、きれいな5秒おきのスパイクが見える
  * **Havocの特徴:** `Jitter` が設定されている場合、スパイクの間隔が不規則になる、これは「人によるブラウジング」を装うための手法だが、深夜帯に発生し続ける不規則な通信は異常として特定可能

##### B. TLSハンドシェイクとJA3の抽出

TLS通信の場合、中身は見えませんが「指紋」は抽出できる

1.  Wiresharkで `Client Hello` パケットを選択
2.  `Transport Layer Security > TLS > Handshake Protocol` を展開。
3.  **JA3ハッシュの確認:** `JA3 Fullstring`（使用可能な暗号スイート等の羅列）を確認し、これが正規の `msedge.exe` や `chrome.exe` のものと一致するか検証する

##### C. パケットサイズによるプロファイリング

Havocのタスク（コマンド実行結果など）が送受信される際、パケットサイズに顕著な変化が現れる

  * **チェックイン時:** 非常に小さいパケット。
  * **データ転送時:** 命令（Shellcodeなど）が送られる際、急激にサイズが増大する、この「平常時と実行時の落差」をフロー統計で監視するのが効果的

-----

### エンドポイントにおける検知コンポーネント

#### エンドポイント相関分析

ネットワーク単体では限界がある
EDRログとの相関が決定的である

##### 例

* CreateRemoteThread
* 不審ProcessAccess
* RXメモリ領域変化
* Named Pipe UUID形式生成
* 異常DNS頻度

---

#### Sysmonを用いた検知クエリ (KQL)

##### 1\. 不審なスレッド作成 (Sysmon Event ID 8)

Demonが正規プロセスにコードを注入する動きを検知

```kusto
DeviceEvents
| where ActionType == "CreateRemoteThread"
| where InitiatingProcessFileName !in~ ("csrss.exe", "wininit.exe")
| where TargetProcessFileName in~ ("w32tm.exe", "SearchProtocolHost.exe", "notepad.exe")
| project TimeGenerated, DeviceName, InitiatingProcessFileName, TargetProcessFileName
```

##### 2\. 未署名DLLのロード (Sysmon Event ID 7)

メモリ上でのみ展開されるリフレクティブロードの兆候を監視

```kusto
DeviceImageLoadEvents
| where isempty(SignerCertificateType) or SignerCertificateType == "None"
| where FolderPath startswith @"c:\windows\system32\"
| where InitiatingProcessFileName != "System"
| project TimeGenerated, DeviceName, InitiatingProcessFileName, FileName
```

---

#### 検知クエリ例（Microsoft Sentinel / Defender）

##### 1. Remote Thread 検知

```kusto
DeviceEvents
| where ActionType == "CreateRemoteThread"
| where InitiatingProcessFileName !in~ ("csrss.exe", "wininit.exe")
| project TimeGenerated, DeviceName,
          InitiatingProcessFileName,
          TargetProcessFileName,
          TargetProcessId
```

---

##### 2. Process Access（Event ID 10相当）

```kusto
DeviceEvents
| where ActionType == "ProcessAccessed"
| where InitiatingProcessFileName !in~ ("csrss.exe", "wininit.exe")
| project TimeGenerated, DeviceName,
          InitiatingProcessFileName,
          TargetProcessFileName,
          GrantedAccess
```

---

##### 3. 署名なしモジュールのロード

```kusto
DeviceImageLoadEvents
| where isempty(SignerCertificateType)
| where InitiatingProcessFileName !in~ ("explorer.exe", "svchost.exe")
| project TimeGenerated, DeviceName,
          InitiatingProcessFileName,
          FolderPath
```

※ ペイロード名は任意であり、固定名ではない

---

#### 高度な検知クエリ（KQL）の作成

基本的なインジェクション検知に加え、C2特有の「振る舞い」を多角的に分析するクエリを運用する

##### 1. 名前付きパイプ（Named Pipe）の監視 (Sysmon ID 17, 18)

Demonがプロセス間通信や横展開（Lateral Movement）に使用する名前付きパイプの作成を検知する

```kusto
DeviceEvents
| where ActionType == "NamedPipeEvent"
| where PipeName matches regex @"^[a-f0-9]{8}-[a-f0-9]{4}-[a-f0-9]{4}-[a-f0-9]{4}-[a-f0-9]{12}$" // UUID形式の不審なパイプ名
| where InitiatingProcessFileName !in~ ("svchost.exe", "lsass.exe")
| project TimeGenerated, DeviceName, InitiatingProcessFileName, PipeName
```

---

##### 2. DNSクエリの異常（ビーコン通信の兆候）

不自然な頻度で発生する特定のドメインへの名前解決を特定する

```kusto
DeviceNetworkEvents
| where RemotePort == 53
| summarize QueryCount = count() by RemoteUrl, DeviceName, bin(TimeGenerated, 1h)
| where QueryCount > 100 // 1時間あたりの閾値を設定
| order by QueryCount desc
```

---

##### 3. メモリ整合性の欠如（スタックウォークの偽装検知）

`Indirect Syscalls` を使用しても、スタックトレースが不自然な（有効なリターンアドレスを持たない）ケースを抽出する

```kusto
DeviceEvents
| where ActionType == "GeneralAudit" // EDR固有のメモリ監査イベント
| where AdditionalFields contains "InconsistentStack" or AdditionalFields contains "UnknownModule"
| project TimeGenerated, DeviceName, InitiatingProcessFileName, AdditionalFields
```

---

### 📊 防御マトリックス

| レイヤー     | 検知手法             | 実用性 |
| -------- | ---------------- | --- |
| ネットワーク   | JA3 / 証明書 / 周期通信 | 中〜高 |
| ホスト（ログ）  | Sysmon / プロセス相関  | 高   |
| ホスト（メモリ） | スタック整合性          | 高   |
| フロー統計    | 長期周期分析           | 高   |
| 単一ポート    | ポート監視のみ          | 低   |

---

### SOC実践ポイント

1. JA3ベースライン構築
2. 自己署名TLS可視化
3. HTTPヘッダ順序ログ取得
4. フロー周期性ダッシュボード化
5. エンドポイント × ネットワーク相関クエリ作成
6. 誤検知率評価と継続的改善

---

### 結論

HavocのようなC2通信検知では

* シグネチャ単体では不十分
* 統計的分析が重要
* TLS指紋は比較的偽装コストが高い
* HTTP挙動には統計的特徴が残る
* エンドポイント挙動の完全隠蔽は困難

最も有効なのは

> IDSシグネチャ × JA3分析 × フロー周期性 × EDRログ相関

による多層検知アプローチである

---

## 攻撃側（Red Team）の視点

### Havoc通信プロファイル（.yaotl）のカスタマイズ

デフォルトの設定はセキュリティ製品のシグネチャに登録されていることが多いため、独自のプロファイルを作成して正規のトラフィックに紛れ込ませる必要がある

#### 1. 通信ヘッダーとUser-Agentの偽装

セキュリティ製品の「不審なUser-Agent」や「C2特有のヘッダー」による検知を回避する

* **User-Agent:** ターゲット環境で一般的に使われているブラウザ（ChromeやEdge）の最新文字列に設定する
* **カスタムヘッダー:** `X-Forwarded-For` や `Cookie` ヘッダーを模倣し、プロキシ経由の正規アクセスに見せかける

```yaml
# havoc.yaotl の設定例
Config {
    Name = "Custom_Office365_Impersonation"
}

Http {
    UserAgent = "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/121.0.0.0 Safari/537.36"
    
    # ビーコン（通信間隔）の設定
    Beacons = [
        "https://outlook.office365.com/owa/"
    ]

    Response {
        Headers = [
            "Content-Type: application/json",
            "Server: Microsoft-IIS/10.0",
            "X-Powered-By: ASP.NET"
        ]
    }
}

```

#### 2. 通信パターンの隠蔽（JitterとSleep）

一定間隔の通信は「ビーコン」として容易に検知されるため、揺らぎ（Jitter）を大きく設定する

* **Jitter:** 通信間隔にランダムなパーセンテージ（例: 20-30%）を加え、機械学習による周期性分析を妨害する
* **Data Encoding:** 通信内容を `Base64` だけでなく、独自の難読化を組み合わせてペイロードのシグネチャを消す

---

### 高度な回避

#### 1. GitHub Actions による自動難読化ビルドパイプライン

ペイロードのバイナリを生成する際、手動ビルドでは難読化の漏れが発生しやすい
GitHub Actionsを利用して、常に難読化（Obfuscation）された最新のDemonを自動生成する仕組みを構築する

##### ワークフローの構成要素

* **Garble (Go難読化):** Teamserver（Go製）のシンボル名や文字列を難読化するために使用する
* **LLVM-Obfuscator / Custom Scripts:** Demon（C++/ASM製）の制御フローを平滑化し、静的解析を困難にする
* **Artifacts:** ビルドされたバイナリをパスワード付きZIPで保存し、GitHubのセキュリティスキャンによる検知を防ぐ

##### 実装例 (.github/workflows/build-demon.yml)

```yaml
name: Havoc Demon Automated Build
on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Havoc
        uses: actions/checkout@v4

      - name: Setup Go & Python
        uses: actions/setup-go@v5
        with:
          go-version: '1.21'

      # 難読化ツールのインストール
      - name: Install Garble
        run: go install mvdan.cc/garble@latest

      # Teamserverのビルド（Garbleを使用）
      - name: Build Teamserver with Garble
        run: |
          cd Teamserver
          garble -literals -tiny build -o teamserver-obf main.go

      # Demonのビルドと難読化
      # ここでは例としてコンパイラフラグによるストリップ処理を付与する
      - name: Build Demon
        run: |
          cd Client
          cmake -DCMAKE_BUILD_TYPE=Release .
          make
          # シンボル情報の削除
          strip --strip-all havoc-client

```

---

#### 2. EDRの検知特性に合わせたプロファイル（.yaotl）の最適化

主要なEDR（Microsoft Defender for Endpoint, CrowdStrike等）はそれぞれ異なる検知ロジックを持つ
ターゲットの環境に合わせてプロファイルを調整することで、生存率を大幅に向上させる

##### EDR別・対策ポイント一覧

| 対象EDR | 主要な検知ロジック | プロファイルでの対策 |
| --- | --- | --- |
| **Microsoft Defender (MDE)** | AMSI/ETWによる挙動監視 | `Amsi = true` および `Etw = true` でパッチを適用する |
| **CrowdStrike Falcon** | 不審なスタック呼び出し | `Indirect Syscalls` を強制し、`Stack Spoofing` を有効化する |
| **SentinelOne** | プロセスインジェクションの相関解析 | デフォルトの `notepad.exe` ではなく `SearchIndexer.exe` 等の正規プロセスを指定する |

##### 実践的な難読化設定例 (.yaotl)

```yaml
Demon {
    # メモリ上でのスリープ技術の選択
    # Ekkoは有名なスリープ難読化テクニックである
    Sleep {
        Technique = "Ekko" 
        Jitter = 25
    }

    # インジェクション先の偽装
    # ターゲット端末で常に動いているプロセスを選択することが重要である
    Injection {
        Spawn64 = "C:\\Windows\\System32\\svchost.exe"
        Spawn32 = "C:\\Windows\\SysWOW64\\svchost.exe"
    }
}

```

---

### 高度な回避の鍵：Environmental Keying

最新のEDRはサンドボックス内でペイロードを実行し、その挙動を確認する
これを回避するために、**「特定の組織環境でしか動作しない」**ロジックをプロファイルに組み込む手法が有効である

* **ドメインチェック:** 端末が特定のActive Directoryドメインに参加していない場合は即座に終了する
* **ファイル存在確認:** 特定の業務アプリがインストールされているかを確認し、デバッグ環境（サンドボックス）を識別する

> [!TIP]
> **継続的な検証の重要性**
> 回避技術は日々更新されるシグネチャとのいたちごっこである
> `CheckPlease` などのツールを併用し、ビルドしたバイナリが静的・動的にどう判定されるかを常にテストすることを推奨する

---

## ⚠ 脆弱性：CVE-2024-41570

Havoc Teamserverにおいて報告された脆弱性の一例。

- 種類：ヒープベースのバッファオーバーフロー
- 条件：細工されたパケット処理時の境界チェック不備
- 影響：リモートコード実行（RCE）の可能性

### 対策

- 公式修正版へのアップデート（修正バージョンは公式リリース参照）
- Teamserverをインターネットへ直接公開しない
- IP制限またはVPN配下での運用
- WAF/リバースプロキシ導入

---

## コマンド / 設定例

```bash

```

## 逆引き・解決策

- **やりたいこと:** （例：特定のプロセスを強制終了したい）
- **解決策:** `kill -9 <PID>`
- **補足:** ゾンビプロセスには効かない場合がある

## このナレッジを使用した検証ログ

[!INFO] このツール/知識を実際に使用した 20_Projects のログが自動で表示されます。

```dataview
TABLE target_ip as "ターゲット", status as "状況", date as "実施日"
FROM "20_Projects"
WHERE contains(tools, this.file.name) OR contains(tools, [[Untitled]])
SORT date DESC
```

## 関連リンク

- [[_Index_undefined]]
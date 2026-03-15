---
tags:
  - ServerSecurity
  - PenetrationTesting
  - SOC
  - IncidentResponse
  - Network
  - Protocol
  - HTTP
  - HTTPS
  - TLS
  - LogAnalysis
  - Authentication
  - Authorization
  - PrivilegeEscalation
  - ProcessMonitoring
  - WindowsSecurity
  - LinuxSecurity
  - Forensics
  - Defense
  - Detection
  - TCPConnectScan
  - SYNStealthScan
  - BannerGrabbing
  - Netcat
  - Nmap
  - TAP
  - SPAN
  - WAF
  - SNI
  - JA3
  - JA3S
  - JWT
  - Auditd
  - Sysmon
  - EventID
  - GPO
  - SIEM
  - NTP
  - CyberChef
  - Velociraptor
  - Volatility
  - JQ
  - THCHydra
  - Hashcat
  - DirectoryTraversal
  - Shellshock
  - RCE
  - IDOR
  - BOLA
  - JWTAbuse
  - JuicyPotato
  - SUIDAbuse
  - LogWiping
  - LivingOffTheLand
  - PasswordSpraying
  - BruteForce
  - SessionHijacking
  - SQLInjection
  - OSCommandInjection
  - PassTheTicket
  - GoldenTicket
  - Windows
  - Linux
  - WebServer
  - DBServer
  - ActiveDirectory
  - Rootkit
  - OfflineBackup
  - DNS
  - Triage
  - Remediation
  - LiveResponse
  - FindEvil
  - BaselineComparison
  - ObjectOrientedPowerShell
  - WMI
  - CIM
  - Kerberos
  - NTLMHash
  - LMHash
  - PasswordHashing
  - PasswordStretching
  - RuleBasedAttack
  - OSINT
  - DynamicAnalysis
  - StaticAnalysis
  - JSONParsing
  - BootCamp
  - CloudSecurity
  - Microsoft365
  - S3Bucket
  - UsernameEnumeration
  - MFA
  - ProcessTree
  - NetworkForensics
  - MemoryForensics
  - MalwareAnalysis
  - PromptEngineering
  - Automation
  - AI
  - MTTR
  - Sysinternals
  - ProcessExplorer
  - Autoruns
  - TCPDump
  - TShark
  - Squid
  - AnyRun
  - HybridAnalysis
  - Strings
  - GoogleNotebookLM
  - zip2john
created: 2025-06-29 15:02
modified: 2026-02-22 09:30
environment: [Windows, Linux, Web_Seerver, DB_Server, ActiveDirectory]
vulnearability: [Directory Traversal, Shellshock, RCE, IDOR, BOLA, JWT Abuse, JuicyPotato, SUID Exploitation, Log Wiping, Living off the Land, Password Spraying, Brute Force, Session Hijacking, SQL Injection, OS Command Injection, Pass-the-Ticket, Golden Ticket]
knowledge_category: Server/Common
---

# サーバセキュリティのための基礎技術整理

## 1. 本章の目的と位置づけ

本章は、ペネトレーションテスター（以下、ペンテスター）およびSOCアナリストが共通して持つべき「技術的足場」を構築することを目的とする

サーバを単一の点として捉えるのではなく、パケットが到達し、プロセスが起動し、権限が遷移し、最終的にログに収束するまでの一連の「攻撃経路（アタックサーフェス）」として理解しなければならない

後続の「Webサーバ編」「DBサーバ編」では、本章で解説するプロトコルやログの挙動を前提知識として扱うため、基礎概念の重複説明は排除する

ペンテスターの「検知されないための技術」とSOCの「隠された攻撃を可視化する技術」の視点を統合し、実務に直結する深さまで網羅的に解説する

> **実務での注意点**
>
> 攻撃手法と検知手法は常にイタチごっこであるため、特定のツール名や製品名ではなく「背後にあるプロトコルの仕様」を理解することが、陳腐化しないスキルの獲得に繋がる

---

## 2. ネットワーク基礎（TCP/IP、スキャン、可視化ポイント）

サーバへの攻撃は、ネットワークの出入口を探る偵察行為から始まる

プロトコルの挙動と物理的な可視化構成を理解することは、スキャン手法の選択や異常通信の検知に直結する

### スキャン手法とプロトコルの挙動

- **TCP Connect Scan**

		- 3-way handshake（SYN -> SYN/ACK -> ACK）を完全に完了させる
				
		- **攻撃者の視点**: 確実な応答を得られるが、対象のアプリケーションログに接続記録が残るリスクが高い
				
		- **SOCの視点**: OS層だけでなく、Webサーバ等のミドルウェアログにも痕跡が残るため相関分析が容易となる
				
- **ハーフオープンスキャン（SYN Stealth Scan）**

		- クライアントからSYNを送り、サーバからSYN/ACKが返った直後にRST（切断）を送信する
				
		- **攻撃者の視点**: セッションがフルで確立されないため、多くのアプリケーションログに記録を残さず高速な調査が可能となる
				
		- **SOCの視点**: アプリログには残らないため、FWやIDSにおける「大量のSYN送信と未完了セッションの多発」を閾値監視して検知する
				
- **バナーグラビング**

		- `nc`（netcat）や `telnet` を用いて特定ポートに接続し、サービスが応答する際の自己紹介テキスト（バナー）を取得する
				
		- **攻撃者の視点**: 取得したバナー（例: `Server: Apache/2.4.41`）からバージョンを特定し、既知の脆弱性（CVE）を突き止める
				
		- **SOCの視点**: 外部への不要なバナー露出は情報漏洩であるため、設定ファイルで不要なヘッダ情報を隠蔽または偽装する
				

### ネットワーク可視化の物理構成（TAPとSPAN）

[図解：ネットワーク可視化構成]

ルータとスイッチの間に物理的な分岐装置（TAP）を設置する構成と、スイッチの内部設定で通信を複製する（SPAN）構成が存在する

- **TAP (Test Access Point)**

		- ネットワーク回線の間に物理的に割り込ませ、パケットを電気的・光学的にコピーしてIDSやパケットキャプチャ装置に送る
				
		- 全トラフィック（物理層のエラーパケット含む）を確実に取り込めるが、導入時にネットワークの瞬断を伴う
				
- **SPAN (Switched Port Analyzer) / ミラーポート**

		- スイッチの設定により、指定したVLANやポートのトラフィックを別の監視用ポートに複製する
				
		- 物理的な構成変更なしで導入できるが、スイッチのCPU高負荷時や帯域超過時にパケットドロップ（複製漏れ）が発生する
				

> **実務での注意点**
>
> 攻撃者はSPANの仕様を悪用し、意図的に大容量のダミートラフィックを流してスイッチのパケットドロップを誘発させ、本命の攻撃パケットをIDSの監視から逸らす手法（DoS併用型）をとることがある

---

## 3. HTTP / HTTPSの仕組み（リクエスト・レスポンス、ヘッダ、可視性）

Webアプリケーション攻撃を理解するための最重要項目であり、正規の通信に「毒」を混ぜる手法が主流である

### 悪用される主要HTTPヘッダ

- **X-Forwarded-For**: プロキシやロードバランサを経由した際の送信元IPを示すが、攻撃者が任意に偽装し、ソースIP制限をバイパスするために悪用する
- **User-Agent**: 攻撃ツールのデフォルト値（例: `sqlmap/1.4`）が残る箇所だが、正規のブラウザ文字列に偽装して検知を逃れる
- **Content-Type**: WAFのシグネチャ検知を回避するため、意図的に `multipart/form-data` などを複雑にエンコードしてペイロードを送信する
- **Host**: バーチャルホストのルーティング不備を狙い、内部向けの管理ドメイン（例: `admin.localhost`）を指定してアクセスを試みる

### ログ例：HTTPリクエストによる攻撃（Apache アクセスログ）

Plaintext

```
10.0.5.12 - - [19/Feb/2026:14:20:00 +0900] "GET /download?file=../../../../etc/passwd HTTP/1.1" 200 1254 "https://malicious.com/" "Mozilla/5.0"
10.0.5.12 - - [19/Feb/2026:14:21:15 +0900] "POST /cgi-bin/test.sh HTTP/1.1" 500 231 "-" "() { :; }; echo; /usr/bin/id"
```

- **ペンテスターの視点**: 1行目はディレクトリトラバーサルによるOSファイル読み取り、2行目はShellshock脆弱性を狙ったOSコマンド実行である
- **SOCの視点**: URI内の `../` や、User-Agent内の不自然な記号群 `() { :; };` を正規表現で抽出し、インシデントとして起票する

> **実務での注意点**
>
> HTTPS化された通信は、サーバの終端装置（WAFやロードバランサ）でSSL/TLSを復号しない限り、ネットワーク経路上にある従来のIDSではペイロードの攻撃文字列を一切検知できない

---

## 4. TLSの基礎（ハンドシェイク、証明書、メタデータ）

TLSは通信の機密性を守るが、攻撃者にとっても「検知を回避する強固な盾」となる

### TLSハンドシェイクと漏洩するメタデータ

[図解：TLSハンドシェイクにおける情報可視化ポイント]

Client Hello送信時から暗号化通信確立までの間で、暗号化されずにやり取りされる情報が検知の鍵となる

- **SNI (Server Name Indication)**: クライアントが接続先ホスト名を平文で提示するため、悪意のあるドメインへの接続を境界FWで検知・遮断可能である
- **証明書情報**: 攻撃者が用いるC2（Command & Control）サーバの証明書は、無料の認証局や自己署名証明書（オレオレ証明書）が使われることが多く、Issuer（発行元）やValidity（有効期間）の異常が検知指標となる
- **JA3 / JA3S フィンガープリント**: Client Helloに含まれる暗号スイートや拡張領域のパラメータ順序は、通信元のソフトウェア（ブラウザやマルウェア）固有の癖を持つ。これをハッシュ値（JA3）化することで、ペイロードが暗号化されていても「既知のマルウェアによる通信」であるかを特定する

> **実務での注意点**
>
> マルウェアは環境寄生（LotL: Living off the Land）を狙い、OS標準のPowerShellやcurlのJA3ハッシュを偽装して通信を行うため、JA3単体での過信は禁物であり他ログとの相関が必要となる

---

## 5. 認証と認可（方式、セッション、侵害時の影響）

境界防御を突破した後に攻撃者が標的とするのが「権限」の奪取と維持である

### 認証と認可の概念

- **認証 (Authentication)**: そのユーザが「誰であるか」を確認するプロセス（ID/パスワード、多要素認証）
- **認可 (Authorization)**: 認証されたユーザに対して「何ができるか」の権限を制御するプロセス

### セッション管理とトークン操作の脆弱性

一度認証が成功すると、サーバは状態を維持するために `Session ID` や `JWT (JSON Web Token)` を発行する

- **IDOR (Insecure Direct Object Reference)**: `GET /user/profile?id=1001` のようなリクエストのID部分を推測可能な値に変更し、認可制御の不備を突いて他人のデータを不正に閲覧する（BOLAとも呼ばれる）
- **JWTの悪用**: トークン内のヘッダを改ざんし、署名アルゴリズムを `none`（署名なし）に変更してサーバ側の検証をバイパスさせ、管理者権限を奪取する

> **実務での注意点**
>
> アプリケーションのデバッグログやプロキシログのURLパラメータに Session ID や JWT が平文で出力されている場合、ログ自体がセッションハイジャックの攻撃材料となるため、必ずマスキング処理を施すこと

---

## 6. プロセスと権限モデル（LinuxとWindowsの違い、特権昇格）

サーバ内部に侵入した後の「横展開（ラテラルムーブメント）」と「権限昇格」を理解するための基礎である

### Windowsの権限モデルとプロセスツリー

- **プロセスツリーの異常**: 正規のプロセス親子関係からの逸脱を監視する

		- 例：`w3wp.exe`（IISのWebサーバプロセス）の子プロセスとして `cmd.exe` や `powershell.exe` が起動するのは、RCE（遠隔コード実行）が成功した典型的な証跡である
				
- **特権の悪用**: `SYSTEM`, `Administrator`, `Standard User` の区分に加え、特定の強力な権限が狙われる。例えば `SeImpersonatePrivilege`（クライアント認証後に偽装する特権）を悪用した **Juicy Potato** などのツールにより、サービスアカウントから最上位の `SYSTEM` 権限へと昇格する

### Linuxの権限モデルとSUID

- **SUID (Set owner User ID) の悪用**: 実行時にファイル所有者の権限（通常はroot）で動作する仕組み

		- 攻撃者は `find / -perm -4000 -type f 2>/dev/null` コマンドでSUIDが設定されたバイナリを探索する
				
		- `find` コマンド自体にSUIDが付与されている場合、`find . -exec /bin/sh -p \;` を実行することで即座にrootシェルを奪取できる
				

> **実務での注意点**
>
> ペンテストにおいて、一般ユーザ権限を取得した直後に「次に行うべきコマンド」は派手な攻撃ではなく、`whoami /priv` や `id` による現行ユーザの所属グループおよび特権の静かな確認である

---

## 7. ログの概念と発生箇所（統合分析の基礎）

「証拠」がどこに、どのような形式で残るかを整理し、点と点を線で結ぶ相関分析の基礎を構築する

### ログの3階層とSIEM連携

1. **アプリケーションログ**: Webサーバ、DBサーバ等の操作履歴（SQLインジェクション等の痕跡）
2. **OSログ / Endpoint (EDR)**: ログイン履歴、プロセス起動、ファイル変更（権限昇格やマルウェア実行の痕跡）
3. **ネットワークログ**: DNSクエリログ（C2サーバへの名前解決）、Proxyログ（外部へのデータ持ち出し）、NetFlow（異常な通信量やビーコニングの検知）

### ログの保護と隠蔽対策

攻撃者は自身の足跡を消すために「Log Wiping（ログ消去）」を行う

SOCとしては、攻撃者が侵入したサーバローカルのログは改ざんされる前提に立ち、**外部のログ管理サーバ（SyslogサーバやSIEM）**へリアルタイムに転送し、Write-Once（追記のみ可能）なストレージで保護することが必須となる

> **実務での注意点**
>
> 複数のログソースをSIEMで統合する際、各機器のNTP（Network Time Protocol）による時刻同期がずれていると、攻撃のタイムラインを正確に再構成することが不可能になるため、導入時の時刻設定は致命的に重要である

---

## 8. Linuxログ体系の詳細監視

Linuxサーバの調査において、標準ログと拡張監査ログを組み合わせて分析する

### 主要なログファイル群

- **/var/log/auth.log (または secure)**: SSHログインの成功・失敗、sudo実行記録
- **/var/log/syslog (または messages)**: システム全般の挙動、サービスの起動・停止
- **~/.bash_history**: ユーザが実行したコマンド履歴

### Auditd (Linux Audit System) の活用

標準ログだけでは「実行されたコマンドの引数」や「特定の機密ファイルへのアクセス」を追跡しきれないため、カーネルレベルでイベントを記録する `auditd` を利用する

**ログ例：Auditdによる不正アクセス検知**

Plaintext

```
type=EXECVE msg=audit(1708345200.123:456): argc=3 items=1 ppid=1024 pid=2048 user=www-data comm="sh" args="/bin/sh -c cat /etc/shadow"
```

- **分析ポイント**: `user=www-data`（権限の低いWebサービスアカウント）がシェルを起動し、パスワードハッシュが格納された `/etc/shadow` を閲覧しようとしている。これは明確なRCEおよび情報探索のサインである

> **実務での注意点**
>
> 攻撃者が `rm ~/.bash_history` や `history -c` でコマンド履歴を消去しても、`auditd` はカーネル層でシステムコールを記録しているため実行内容は確実に残る

---

## 9. Windowsイベントログと重要EID

Windows環境の分析には、特定のイベントID（EID）の理解と、拡張監視ツールである `Sysmon` の導入が不可欠である

### Sysmon (System Monitor) の役割

デフォルトのイベントログを補完し、より高度なプロセス監視やネットワーク監視を提供する

- **EID 1**: Process Creation（プロセスの作成、親プロセス情報、実行ファイルのハッシュ値）
- **EID 3**: Network Connection（どのプロセスが、どのIP/Portへ通信したか）
- **EID 7**: Image Loaded（プロセスに読み込まれたDLL情報の記録）

### SOCアナリスト必携：重要イベントID（Securityログ）一覧表

|**EID**|**内容**|**攻撃者の活動例**|**分析・検知のポイント**|
|---|---|---|---|
|**4624**|ログオン成功|不正なクレデンシャル使用|ログオンタイプに注目する（3: ネットワーク経由、10: RDP経由）|
|**4625**|ログオン失敗|パスワードスプレー、ブルートフォース|短時間に同一アカウント、または同一ソースIPから多発していないか追跡する|
|**4688**|プロセスの作成|攻撃コマンドやスクリプトの実行|GPOで「コマンドライン引数」を含める設定にし `powershell -enc` 等の難読化を監視する|
|**4698**|タスクの作成|永続化（Persistence）|攻撃者がバックドアをシステム再起動後も起動させるためのスケジュールタスク登録を検知する|
|**4720**|ユーザ作成|バックドアアカウントの作成|正規の管理者作業以外の予期せぬタイミングでの新規ユーザ作成を警告する|
|**4768**|Kerberos TGT要求|パス・ザ・チケット、Golden Ticket|Active Directory環境下での特権アカウントに対する異常なチケット発行要求を監視する|

> **実務での注意点**
>
> Windowsのデフォルト設定では「プロセスの作成（4688）」に引数が含まれず、どのスクリプトが実行されたか判別できないため、本番環境構築時には必ずグループポリシー（GPO）で「コマンドラインのプロセス作成イベントを含める」を有効化すること

---

## 10. まとめと次章への接続

本章では、パケットのキャプチャポイント（TAP/SPAN）から始まり、ポートスキャンの隠蔽技術、プロトコルの暗号化による死角、プロセスと権限の構造、そして最終的な証跡となる重要イベントIDまでを俯瞰した

- **ペンテスター**は、これらの仕組みを逆手に取る。TAPで監視されている環境下ではハーフオープンスキャンすら検知されることを認識し、より低速・分散型のスキャンや、環境寄生型（LotL）コマンドを用いたログ隠蔽を計画する
- **SOCアナリスト**は、点在する各種ログを統合し、例えば「EID 4625の多発直後にEID 4624が発生し、さらに w3wp.exe から不審な子プロセスが起動した」という一連の流れから、インシデントのストーリーを正確に組み立てるスキルが求められる

次章「第1章：Webサーバの要塞化と攻撃分析」では、本章で学んだHTTPの構造やOSの権限モデルを基に、ApacheやIISに対する具体的な攻撃手法（SQLインジェクション、OSコマンドインジェクション）とその防御・検知策（Hardening）を詳細に検証していく

> **実務での注意点**
>
> ここまで学習した基礎概念は座学で終わらせず、仮想環境にて意図的にスキャンや攻撃コマンドを実行し、自身の目で生ログやパケットキャプチャを確認する「ハンズオン」を必ず実施すること
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
  - TCP_Connect_Scan
  - SYN_Stealth_Scan
  - Banner_Grabbing
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
  - THC_Hydra
  - Hashcat
  - Directory_Traversal
  - Shellshock
  - RCE
  - IDOR
  - BOLA
  - JWT_Abuse
  - Juicy_Potato
  - SUID_Abuse
  - Log_Wiping
  - Living_Off_The_Land
  - Password_Spraying
  - Brute_Force
  - Session_Hijacking
  - SQL_Injection
  - OS_Command_Injection
  - Pass_The_Ticket
  - Golden_Ticket
  - Rootkit
  - Offline_Backup
  - DNS
  - Triage
  - Remediation
  - Live_Response
  - Find_Evil
  - Baseline_Comparison
  - Object_Oriented_PowerShell
  - WMI
  - CIM
  - Kerberos
  - NTLM_Hash
  - LM_Hash
  - Password_Hashing
  - Password_Stretching
  - Rule_Based_Attack
  - OSINT
  - Dynamic_Analysis
  - Static_Analysis
  - JSON_Parsing
  - BootCamp
  - Cloud_Security
  - Microsoft365
  - S3_Bucket
  - Username_Enumeration
  - MFA
  - Process_Tree
  - Network_Forensics
  - Memory_Forensics
  - Malware_Analysis
  - Prompt_Engineering
  - Automation
  - AI
  - MTTR
  - Sysinternals
  - Process_Explorer
  - Autoruns
  - TCPDump
  - TShark
  - Squid
  - AnyRun
  - Hybrid_Analysis
  - Strings
  - Google_NotebookLM
  - zip2john
created: 2025-06-29 15:02
modified: 2026-02-20 15:59
environment:
  - Windows
  - Linux
  - WebServer
  - DBServer
  - ActiveDirectory
vulnearability:
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
knowledge_category: Training
---

# Sec504 day2

# 1. パスワード攻撃のタイプとツール

主に2つのオンラインパスワード攻撃について議論されました。

-   **パスワード推測攻撃 (Password Guessing Attack):** 攻撃者が単一のユーザーアカウントに対して多数のパスワードを試行します。通常、アカウントロックアウトポリシーによって阻止されます。
-   **パスワードスプレー攻撃 (Password Spraying Attack):** ロックアウトを回避するため、多数のユーザーアカウントに対して1〜2個の一般的なパスワードを試行します。試行の合間に時間を置くことで、失敗カウンターのリセットを待ちます。
-   **THC Hydra:** これらの攻撃に使用されるオープンソースツールで、SSH、Web、VNC、SMBなど様々なネットワークプロトコルをサポートしています。
-   **パスワードリストとフィルタリング:** 攻撃者は、公開されている一般的なパスワード、ターゲットに関連する単語（会社名、都市名）、季節パターン（例：「Summer2024」）を組み合わせてリストを作成します。`PW Inspector` ツールを使用すると、パスワードポリシー（最小長、必須文字種など）に基づいてリストを精査できます。
-   **クレデンシャルスタッフィング (Credential Stuffing):** 過去のデータ漏洩から流出したユーザー名とパスワードの組み合わせ（「Have I Been Pwned」などのプラットフォームで確認可能）を利用して、他のサービスへのログインを試みる攻撃です。パスワードの使い回しを悪用します。

## 1.1. 実習タスク: THC Hydra ラボ

> -   SSHサーバーと `GoHydraTGT` ターゲットサーバーのセットアップ。
> -   `Nmap` を使用してターゲットサーバーのIPアドレスとオープンポートを特定。
> -   ターゲットのWebサーバーからメールアドレスを検索し、ユーザー名を抽出（社員名簿などから）。
> -   `awk` や `sed` コマンドを使用して、抽出したデータからユーザー名を分離・加工。
> -   ユーザー名を `e-users.txt` というファイルに保存。
> -   生成したリストとパスワードリストを使用し、HydraでターゲットへのSSH攻撃を実行。

# 2. M365 クラウドパスワード攻撃

続いて、クラウドサービス（特にMicrosoft 365）を標的とした攻撃に焦点が当てられました。

-   **ユーザー名の列挙 (Username Enumeration):** 攻撃者はMicrosoftのAPIエンドポイントを悪用し、「存在しないユーザー」と「パスワードが間違っているだけの有効なユーザー」を区別することで、有効なユーザー名のリストを作成します。
-   **MSOL Spray:** M365へのパスワードスプレー攻撃に使用されるPowerShellスクリプト。
-   **スマートロックアウトの回避:** Azureの「スマートロックアウト」機能は、複数回のログイン失敗後に不審なIPをブロックします。攻撃者はAWS API Gatewayやボットネットを使用してソースIPをローテーションさせることでこれを回避します。`Pyoprox` は、この目的のためにAWS API Gatewayを活用するペネトレーションテストツールです。
-   **MFA（多要素認証）のバイパス:** 設定ミスのある「条件付きアクセスポリシー」を突くことでMFAを回避できる場合があります。例えば、特定のIP範囲やレガシーデバイス（古い端末）に対してMFAを無効にしている場合などです。「User-Agent」を偽装することで、攻撃者がレガシーデバイスを使用しているとシステムに信じ込ませます。`Invoke-MFA-Suite` がこの試行を支援します。
-   **インシデントレスポンス:** クラウド侵害後の主要なアクションには、監査ログの確認が含まれます。攻撃者はログの出力を減らすためにライセンスをダウングレード（E5から安価なものへ）したり、MFAなしでアクセスできるように設定を変更したりすることがあります。

## 2.1. 実習タスク: M365 パスワード攻撃ラボ

> -   `dig` を使用してターゲットのメールサーバー（Outlook M365など）を特定。
> -   `cool` などのツールを使用してターゲットのメールアドレスリストを作成。
> -   `MSOL Spray` を実行し、攻撃IPがブロックされる様子を観察。
> -   `Pyoprox` を実装してスマートロックアウトを回避。動的に生成されるURLの置換を確実に行う。
> -   リクエストごとに送信元IPアドレスが変化することを確認。
> -   指定されたターゲットアカウント（Lorenz HaaseとJillian）のパスワード推測を試行。

# 3. オフラインパスワードクラッキング

オンライン攻撃と比較して、ハッシュを取得した後の「オフラインクラッキング」は、速度と検知されないという利点があることが強調されました。

-   **Windows ハッシュ:**
    -   **LAN Manager (LM) ハッシュ:** 非常に古いOSで使われていた極めて脆弱なスキーム。パスワードを大文字に変換し、2つの7文字パーツに分割するため、容易に解読されます。
    -   **NT ハッシュ (NTLM ハッシュ):** Windows Vistaから導入。Unicode変換とMD4を使用。LMよりは優れていますが、「ソルト」がないため理想的ではありません。
    -   **ソルトの欠如:** Windowsハッシュには通常ソルトがありません。つまり、同じパスワードからは常に同じハッシュが生成されるため、パスワードの使い回しが露呈し、レインボーテーブル攻撃に脆弱になります。
-   **UNIX/Linux パスワードハッシュ:** `/etc/shadow`（rootのみアクセス可）に保存されます。8バイトのソルトを使用するため、レインボーテーブルの作成には膨大なストレージが必要となり、事実上攻撃を防ぐことができます。ハッシュの種類は `$X$` のマーカー（MD5, Blowfish, SHA-512など）で識別されます。
-   **パスワードストレッチング:** ハッシュ計算を数千回繰り返すことで、攻撃者の計算コストを高め、オフラインクラッキングを大幅に遅延させます。
-   **クラッキング用ハードウェア:** 攻撃者は専用のハードウェアを使用します。
    -   **GPU (NVIDIA):** CPUよりも大幅に高速な解読が可能。
    -   **FPGA:** さらに強力で、高度な標的型攻撃（APT）グループによって使用されます。

# 4. Hashcat ツール

`Hashcat` は非常に効率的でカスタマイズ可能なパスワードクラッカーであり、GPUアクセラレーションを活用できるため広く好まれています。

-   **攻撃モード:**
    -   **Straight Mode (-a 0):** 辞書攻撃。
    -   **Combinator Attack (-a 1):** 2つの辞書の単語を組み合わせる。
    -   **Mask Attack (-a 3):** カスタムパターン（`?u` 大文字, `?l` 小文字, `?d` 数字, `?s` 記号）を使用して生成。
    -   **Hybrid Modes (-a 6, -a 7):** ワードリストとマスクを組み合わせる。
-   **ハッシュタイプの指定:** 多くの場合自動検出が可能ですが、`-m` オプション（例: `-m 1000` はNTハッシュ）で指定します。
-   **ルールファイル:** 辞書の単語を「oを0に変換」「eを3に変換」「大文字化」などのバリエーションに変換するルールを含み、解読成功率を高めます。
-   **出力:** 解読されたパスワードは `.pot` ファイルに保存されます。`--show` で表示、`--username` でユーザー名を付加して表示できます。

## 4.1. 実習タスク: Hashcat ラボ

> -   `zip2john` を使用してパスワード保護されたZIPファイルからハッシュを抽出。
> -   Hashcatで通常の辞書攻撃を試行（強力なパスワードの場合は失敗することを確認）。
> -   ルールファイル（`best64.rule` など）を活用し、解読を再試行。
> -   ルールベース攻撃により解読に成功。
> -   `.pot` ファイルからパスワードを抽出し、ファイルを解凍。

# 5. パスワード攻撃への対策

-   **LMハッシュの無効化:** Active Directory環境では、グループポリシーを通じてLMハッシュの生成を無効化することが極めて重要です。
-   **強力なパスワードポリシー:**
    -   **長さが最優先:** 最低15文字（理想は20文字以上）を目指します。15文字以上であればLMハッシュは生成されず、NTハッシュの解読時間も劇的に増大します。特権アカウントには25文字以上が推奨されます。
    -   **パスフレーズ:** 短く複雑なパスワードよりも、長く覚えやすいパスフレーズ（例：「Iwanttoquitsmoking」）の使用を推奨します。
-   **頻繁な変更を避ける:** NISTは、3ヶ月ごとの強制変更などは、パスワードの弱体化や使い回しを招くため推奨していません。通常は年1回程度で十分です。
-   **MFA (多要素認証):** 全てのアカウントにMFAを実装します。
    -   SMSベースのMFAは偽装に弱いため避けます。
    -   FIDO2準拠のソリューション（`YubiKey` など）が最も安全です。
    -   ただし、MFAも万能ではなく、セッションハイジャックなどの攻撃ベクトルがあることに注意が必要です。

# 6. 不安全なクラウドストレージ (S3 バケット)

Amazon S3、Google Cloud Storage、Azure Blob Storageなどの設定ミスに伴うリスクについてです。

-   **脆弱性:** 歴史的に、デフォルト設定でファイルが公開設定になっていたことが多く、多くのデータ漏洩を招きました。現在はデフォルトで非公開ですが、管理者の設定ミスにより機密データが露出するケースが後を絶ちません。
-   **発見:** 攻撃者は検索エンジンや専用ツールを使用して公開バケットを探索します。
-   **バケット名の列挙:** 組織名に「dev」「files」「web」などの一般的なトークンを組み合わせて名前を推測します。
-   **ツール:** `Bucket Finder` (AWS)、`gcpbucket.py` (GCP)、`Basic Blob Finder` (Azure) などが列挙プロセスを自動化します。
-   **ケーススタディ:** 公開の読み取りだけでなく書き込みも許可されていた事例。バケット内にWebシェルのバックドアが設置され、それが本番サーバーに同期されたことで、サーバーが完全に侵害されました。
-   **対策:**
    -   **HTTPプロキシログの監視:** クラウドストレージへのアクセスを監視する最も効果的な方法です。
    -   **定期的な監査:** 管理画面を定期的に確認し、意図しない公開設定がないかチェックします。
    -   **アクセスログの有効化:** デフォルトでは無効なことが多いため、明示的に設定する必要があります。

## 6.1. 実習タスク: 不安全なクラウドストレージラボ

> -   Docker (`5s3`) を使用してAWSクラウド環境をシミュレート。
> -   S3バケットの作成などの基本操作。
> -   ワードリストを使用してバケットを探索。
> -   Webサイトの認証を回避し、S3上のPDFに直接アクセス。
> -   このバイパスを利用して機密JSONファイル (`salesstatus.json`) を取得。

# 7. Netcat ユーティリティ

「ネットワークのアーミーナイフ」と呼ばれる `Netcat` は、古いツールながら今もなお多用途で強力です。

-   **基本モード:**
    -   **リスニングモード (-l):** 指定ポートで接続を待ち受けます。
    -   **クライアントモード:** 待ち受けているポートに接続します。
-   **標準入出力 (I/O):** 標準入力(0)、標準出力(1)、標準エラー(2)の理解は、パイプやリダイレクトを伴う高度な使用に不可欠です。
-   **データ転送:** ファイル内容を入出力にリダイレクトすることで、簡単にアップロード/ダウンロードが可能です。
-   **ポートスキャンとバナーグラビング:** `-z` でスキャンを行い、オープンポートからバナー（SSHやHTTPのバージョン情報）を取得できます。
-   **バックドア (リモートシェル):**
    -   **バインドシェル (Bind Shell):** ターゲット側でNetcatがシェル（`-e /bin/sh`）を実行して待ち受けます。
    -   **リバースシェル (Reverse Shell):** 侵害されたホストが攻撃側のNetcatに接続しにいきます。ファイアウォールの回避によく使われます。
-   **永続化:** `nohup ... &` や無限ループのシェルスクリプトを組み合わせることで、バックグラウンドでの継続実行を可能にします。
-   **リレー (ピボッティング):** 直接アクセスできないターゲットに対し、中間ホストをリレーとして通信をトンネルさせます。双方向通信には「名前付きパイプ (FIFO)」を用いたストリーム管理が必要です。

## 7.1. 実習タスク: Netcat ラボ

> -   チャット機能とファイル転送の基本操作。
> -   ポートスキャンとバナーグラビング。
> -   バインドシェルとリバースシェルの構築。
> -   **チャレンジ:** 名前付きパイプを使用して、3つの端末（攻撃者、リレー、ターゲット）間での双方向リレーを構築。
# VS Code + Markdown + Gitによるナレッジ管理

## workディレクトリのファイル構造

```bash
work
├── 01_cheatsheets/        # 目的：コマンドや構文を素早く参照する「辞書」
│   ├── linux/            # Linuxコマンドの詳細ファイル群
│   │   ├── ls.md
│   │   ├── cd.md
│   │   ├── ...
│   │   └── environment_and_config.md      # Linux共通の環境変数・設定ファイル
│   │   └── troubleshooting_common_errors.md # Linux共通のトラブルシューティング
│   ├── cmd/              # CMDコマンドの詳細ファイル群
│   │   ├── dir.md
│   │   ├── ...
│   │   └── environment_and_config.md      # CMD共通の環境変数・設定ファイル
│   │   └── troubleshooting_common_errors.md # CMD共通のトラブルシューティング
│   ├── powershell/       # PowerShellコマンドの詳細ファイル群
│   │   ├── Get-ChildItem.md
│   │   ├── ...
│   │   └── environment_and_config.md      # PowerShell共通の環境変数・設定ファイル
│   │   └── troubleshooting_common_errors.md # PowerShell共通のトラブルシューティング
│   ├── git/              # Gitコマンドの詳細ファイル群
│   │   ├── git_add.md
│   │   └── ...
│   ├── common_practices/ # 一般的な実践・概念に関するファイル群
│   │   ├── backup_strategies.md           # バックアップ戦略
│   │   └── security_best_practices.md     # ユーザー教育など、全体に共通するセキュリティベストプラクティス
│   ├── network_troubleshooting/ # ネットワークトラブルシューティングの詳細ファイル群
│   │   ├── common_network_issues.md
│   │   └── ...
│   ├── logging_and_monitoring/ # ログ関連の詳細ファイル群
│   │   ├── linux_log_files.md
│   │   └── ...
│   ├── linux_index.md    # Linuxコマンドの目次
│   ├── cmd_index.md      # CMDコマンドの目次
│   ├── powershell_index.md # PowerShellコマンドの目次
│   ├── git_index.md      # Gitコマンドの目次
│   ├── images/               # ノートに貼り付ける画像を格納するディレクトリ
│   ├── README.md             # チートシート全体の概要や使い方(目次にもなりうる)
│   └── ... (必要に応じて他の目次ファイル)

├── 02_Learning_Notes/         # 目的：概念や手順を体系的に学ぶ「教科書」
│   ├── 00_OS/
│   │   ├── Linux/
│   │   │   ├── 01_Commands.md
│   │   │   ├── 02_Kernel_Tuning.md
│   │   │   ├── 03_Systemd.md
│   │   │   ├── 04_Storage_FileSystem.md
│   │   │   └── 99_Security_Hardening.md  # (→ 04_Security/BlueTeam/Hardening へリンク)
│   │   └── Windows/
│   │       ├── 01_PowerShell_Scripting.md
│   │       ├── 02_Active_Directory.md
│   │       └── 99_Security_Hardening.md  # (→ 04_Security/BlueTeam/Hardening へリンク)
│   │
│   ├── 01_Networking/
│   │   ├── 01_Network_Fundamentals/          # ネットワークの基礎理論
│   │   │   ├── 01_TCP_IP_Stack.md.          # TCP/IPモデルの各層、プロトコル、データカプセル化
│   │   │   ├── 02_Routing_Protocols.md      # RIP、OSPF、BGP、EIGRPなどのルーティングプロトコル
│   │   │   ├── 03_Switching_Concept.md      # スタティックルート、ダイナミックルート、ルート集約
│   │   │   ├── 04_VLAN_trunking.md          # L2/L3スイッチング、MACアドレス、STP
│   │   │   ├── 05_VPN_Technologies.md       # VPNの概念、トランキング(802.1Q)、VTP、IPsec VPNやssl VPNの種類と特徴・構築の基礎
│   │   │   └── 06_Network_Security_Basics.md # ネットワークセキュリティの基本原則、Dos攻撃対策、侵入検知/防御システム(IDS/IPS)の概念
│   │   ├── 02_Network_Devices/               # 各種ネットワーク機器
│   │   │   ├── 01_Router_config.md          # ルータ基本設定、インターフェイス設定、ルーティング設定例
│   │   │   ├── 02_Switch_config.md          # スイッチのVLAN設定、ポートセキュリティ、EtherChannel/Link Aggregation設定例
│   │   │   ├── 03_Firewall_config.md.       # ファイヤーウォールルール設定、NAT設定、VPNゲートウェイ設定例
│   │   │   ├── 04_LoadBalancer_config.md    # ロードバランサの種類(L4/L7)、アルゴリズム、ヘルスチェック設定例
│   │   │   └── 05_WAF_config.md             # Webアプリケーションファイヤーウォールの基本、設定例、一般的な保護ルール
│   │   ├── 03_Network_Design/# ネットワークの設計の原則とパターン
│   │   │   ├── 01_Campus_Network.md.        # キャンパスネットワークの階層モデル(コア、ディストリビューション、アクセス層)と詳細設計
│   │   │   ├── 02_DataCenter_Network.md     # データセンターネットワークの設計(スパインリーフ、オーバーレイネットワーク、XLANなど)
│   │   │   ├── 03_Cloud_Network.md          # AWS_VPC、Azure_Vnet、GCP_VPCなどのクラウドネットワーク設計、サブネット、ルーティング、ピアリングなど
│   │   │   ├── 04_Network_Segmentation.md
│   │   │   └── 05_IP_Addressing.md
│   │   └── 04_Packet_Analysis/
│   │   │  ├── Wireshark_Usage.md
│   │   │  └── tcpdump_Usage.md
│   │   ├── 05_Network_Monitoring_Tools.md   # Zabbix、Prometheus、Nagiosなどのネットワーク監視ツール、SNMP、NetFlowなどのプロトコル、トラフィック分析
│   │   ├── 06_Network_Troubleshooting.md    # ネットワーク問題の切り分け手順、ping,traceroute,tcpdump,wiresharkなどの活用方法、OSI参照モデルを活用したトラブルシューティング
│   │   └── 07_Network_Automation.md         # ネットワーク自動化のメリット、Python,Ansibleなどのツール、APIを活用した設定変更、DevNetOpsの概念
│   │
│   ├── 02_Virtualization_Containers/
│   │   ├── VMware/
│   │   │   ├── ESXi_Setup.md
│   │   │   └── vCenter_Management.md
│   │   ├── Docker/
│   │   │   ├── Dockerfile_Best_Practices.md
│   │   │   ├── Docker-Compose.md
│   │   │   └── Docker_Networking.md
│   │   └── Kubernetes/# (今後の学習用)
│   │       ├── Concepts.md
│   │       └── kubectl_Commands.md
│   │
│   ├── 03_Servers_Middleware/          # サーバとミドルウェアのアーキテクチャ
│   │   ├── 10_WebServer/               # Webサービス
│   │   │    ├── 01_Service_Overview.md  # サービス全体の概要、冗長化、3層アーキテクチャ
│   │   │    └── Nginx/
│   │   │          ├──01_Overview.md # 諸元、概要、特徴などを記載　また、この後に続く詳細について記述したドキュメントのハブとして各ドキュメンへのリンクを集約
│   │   │          ├──02_Vulnarabilities.md # 脆弱性情報、パッチ適用履歴、攻撃事例などを記載
│   │   │          ├──03_Security.md # セキュリティー設定、脆弱性対策などを記載
│   │   │          ├──04_Troubleshooting.md # ログ、エラーコード、障害切り分けなどを記載
│   │   │          ├──05_Automation.md # 自動化、スクリプト例などを記載
│   │   │          ├──06_Onprem_Setup.md # オンプレでのインストール、構築要領などを記載
│   │   │          ├──07_Cloud_Setup.md # クラウド環境での構築要領などを記載
│   │   │          └──08_Architecture.md # アーキテクチャー、主要コンポーネント、データフローなどについて記載
│   │   └── 20_Database/# データベースサービス
│   │   │    ├── 01_Service_Overview.md  # サービス全体の概要、レプリケーション、シャーディング
│   │   │    └── MySQL_MariaDB/
│   │   │          ├──01_Overview.md # 諸元、概要、特徴などを記載　また、この後に続く詳細について記述したドキュメントのハブとして各ドキュメンへのリンクを集約
│   │   │          ├──02_Vulnarabilities.md # 脆弱性情報、パッチ適用履歴、攻撃事例などを記載
│   │   │          ├──03_Security.md # セキュリティー設定、脆弱性対策などを記載
│   │   │          ├──04_Troubleshooting.md # ログ、エラーコード、障害切り分けなどを記載
│   │   │          ├──05_Automation.md # 自動化、スクリプト例などを記載
│   │   │          ├──06_Onprem_Setup.md # オンプレでのインストール、構築要領などを記載
│   │   │          ├──07_Cloud_Setup.md # クラウド環境での構築要領などを記載
│   │   │          └──08_Architecture.md # アーキテクチャー、主要コンポーネント、データフローなどについて記載
│   │   └── ...
│   ├── 04_Security/
│   │   ├── BlueTeam/
│   │   │   ├── 01_Incident_Response.md
│   │   │   ├── 02_Log_Analysis/
│   │   │   │   ├── SIEM_Basics.md
│   │   │   │   └── Log_Patterns/         # (OS, Apache, FW等のログの見方)
│   │   │   ├── 03_Hardening/
│   │   │   │   ├── OS_Hardening.md
│   │   │   │   └── Middleware_Hardening.md
│   │   │   ├── 04_Network_Defense/
│   │   │   │   ├── Firewall_Design.md
│   │   │   │   └── WAF_IDS_IPS_Rules.md
│   │   │   └── 05_Forensics_Basics.md
│   │   └── RedTeam/
│   │       ├── 01_Methodology/           # (Cyber Kill Chain, MITRE ATT&CK)
│   │       ├── 02_Reconnaissance.md      # (OSINT, Nmap)
│   │       ├── 03_Exploitation/
│   │       │   ├── WebApp_Attacks/     # (SQLi, XSS, etc.)
│   │       │   └── System_Attacks/       # (Metasploit, etc.)
│   │       ├── 04_Post_Exploitation/
│   │       │   ├── Privilege_Escalation.md
│   │       │   └── Lateral_Movement.md
│   │       ├── 05_CTF_Writeups/
│   │       │   ├── HackTheBox/
│   │       │   └── TryHackMe/
│   │       └── 06_SANS_Notes/
│   │           ├── SEC504_GCIH.md
│   │           └── SEC542_GWAPT.md
│   │
│   └── 05_LabEnvironments/ # VMware/Dockerによる検証環境構築メモ、より実践的な内容を記載
│   │   ├── 01_Network_Lab_GNS3.md
│   │   ├── 02_AD_Attack_Lab_Setup.md
│   │   └── 03_Pentest_Lab_Setup.md     # (DockerでDVWAやJuice Shopを立てる手順など)
│   ├── images/               # ノートに貼り付ける画像を格納するディレクトリ
│   └── README.md             # 学習ノートの概要や使い方(目次にもなりうる)
├── images/               # ノートに貼り付ける画像を格納するディレクトリ
└── README.md             # 全体の概要や使い方(トップレベルの目次にもなりうる)
```

VS Code + Markdown + Gitによるナレッジ管理、素晴らしいですね。ご提示いただいたチートシートの構成やVS Codeの設定は非常に詳細かつ実践的で、このまま学習の強力な基盤となるはずです。特に、コマンド単位の詳細ファイル構成案は、インフラ構築・ブルーチーム・レッドチームという3つの視点が明確に分かれており、学習した知識を多角的に整理できる優れたテンプレートです。

その上で、ご質問の「チートシートの**他にも**学習ノートを作成していきたい」という点、そしてOS、検証環境、ネットワーク、サーバ/ミドルウェアに関するファイル構成について、ご提案の構成をさらに強化するためのアイデアを提案します。

### 役割の異なる2つの知識ベースを連携させる

現在の「チートシート」構成は、コマンドやツールといった\*\*「点」の知識**を辞書的に引くのに最適です。これに加えて、複数の技術を組み合わせた手順や、概念的な理解、試行錯誤の記録といった**「線」や「面」の知識\*\*を蓄積する「学習ノート」の領域を明確に分けることで、より強力なナレッジベースを構築できます。

具体的には、既存の`cheatsheets`ディレクトリと並列で`learning_notes`というディレクトリを作成し、役割を分担させます。

```bash
MyKnowledgeBase/
├── cheatsheets/      # ← ご提案いただいた、辞書的に引くための「点」の知識
│   ├── linux/
│   ├── cmd/
│   └── ...
├── learning_notes/   # ← これから作成する、体系的な「線」と「面」の知識
│   ├── 01_Infrastructure_Fundamentals/
│   ├── 02_Security_Blue_Team/
│   ├── 03_Security_Red_Team/
│   └── 04_Labs_Environments/
├── images/
└── README.md
```

### この構成のポイント

1. **役割分担の明確化**:

      * `cheatsheets`:「このコマンドのこのオプション何だっけ？」という時に引く**辞書**。
      * `learning_notes`:「あの脆弱性診断環境、どうやって作ったっけ？」「インシデント対応って何からやるんだっけ？」という時に読む**教科書・手順書**。

2. **学習テーマに沿った構成**:

      * ご自身の学習内容(インフラ、ブルー、レッド、検証環境)がそのままディレクトリ構造になっているため、知識の追加や見直しが直感的に行えます。

3. **試行錯誤の記録**:

      * CTFのWrite-upや、検証環境構築時のトラブルシューティングなどを記録することで、単なる知識のインプットだけでなく、実践的な経験を資産として残せます。

4. **図や手順書の置き場**:

      * `04_Labs_Environments`のように、Draw.ioで作成した構成図や、詳細な構築手順書をまとめる場所を用意することで、再現性のある学習環境を維持できます。

## VS Codeの設定(`settings.json`)

`settings.json`はVS Codeの動作を細かく制御するためのファイルです。VS Codeを開き、`Cmd + Shift + P`でコマンドパレットを開き、「Preferences: Open User Settings (JSON)」と入力して開くことができます。

```json
{

  // カラーテーマとアイコンテーマ (お好みで)
  "workbench.colorTheme": "Default Dark+", // または "Monokai", "One Dark Pro" など
  "workbench.iconTheme": "vs-seti", // または "Material Icon Theme" など
  "workbench.editor.closeOnFileDelete": true, // 外部要因でファイルが削除されたときにエディタを閉じる
  "workbench.editor.pinnedTabsOnSeparateRow": true, // ピン止めされたタブがピン止めされていないタブの上に表示
  "workbench.editor.wrapTabs": true, // タブが画面幅を超えたときに折り返す

  // エディタの設定
  // エディタの表示設定
  "editor.fontSize": 14, // 視認性の良いフォントサイズ
  "editor.lineHeight": 2, // 行間を広げて可読性を向上
  "editor.wordSegmenterLocales": ["ja"], // 単語の分割を行うロケールを設定 / 日本語の単語を区切りとして扱ってくれます
  "editor.renderWhitespace": "boundary", // スペースやタブを視覚化して意図しない空白を防止
  "editor.renderControlCharacters": true, // 制御文字を表示する
  "editor.guides.bracketPairs": true, // 対応する括弧を線で結んで表示
  "editor.bracketPairColorization.enabled": true, // 括弧の対応を色付ける
  "editor.fontFamily": "'Source Code Pro', Consolas, monospace", // プログラミング用フォントを設定
  "editor.minimap.enabled": true, // ミニマップを表示 (コード全体の概要把握に便利)
  "explorer.autoReveal": false, // エディタ切り替えと、エクスプローラのファイル選択が同期されなくなる
  "window.zoomLevel": 0, // 全体のズームレベル (必要に応じて調整)

  // インデントとフォーマット設定
  "editor.detectIndentation": true, // プロジェクトのインデントスタイルを自動検出
  "editor.insertSpaces": true, // タブの代わりにスペースを使用
  "editor.tabSize": 2, // インデントのスペース数
  "editor.rulers": [80, 120], // コード行の長さガイドライン
  "editor.wordWrap": "on", // 長い行を折り返し表示
  "editor.wrappingIndent": "indent", // 折り返し行のインデントが元の行のインデント+1

  // インテリセンスの強化(コード補完と候補表示の挙動を最適化)
  "editor.suggestSelection": "first", // 関連性の高い候補を優先的に表示
  "editor.acceptSuggestionOnEnter": "smart", // テキストを変更する場合にのみEnterでサジェストを受け付ける
  "editor.snippetSuggestions": "top", // スニペットを候補の最上位に表示
  "editor.wordBasedSuggestions": "allDocuments", // 単語ベースの候補表示を有効化

  // フォーマット設定 explicit:auto save時以外のsave時に実行
  "editor.formatOnSave": true, //  保存時に自動的にコードを整形
  "editor.defaultFormatter": null, // 言語ごとに適切なフォーマッターを選択可能 / Prettierなどの拡張機能と連携
  "editor.codeActionsOnSave": {
    "source.organizeImports": "explicit", // 未使用importの削除 / importの並び替え
    "source.addMissingImports": "explicit", // importエラーを解決
    "source.fixAll.eslint": "explicit", // eslint限定でfixを実行
    "source.fixAll": "explicit", //eslintやstylelintなどのfixを実行
    "source.fixAll.markdownlint": "explicit" //markdownlint
  },
  // exploreの設定
  "explorer.compactFolders": false, // サイドバーのフォルダ表示をコンパクトにしない (視認性のため)

  // ファイル管理関連の設定
  // ファイル除外設定
  "files.exclude": { // 不要なファイルやフォルダを非表示
    "**/.git": true, // Gitの管理ファイル(表示する必要性が低い)
    "**/.DS_Store": true, // macOSのシステムファイル(不要)
    "**/node_modules": true, // 依存パッケージ(容量が大きく、直接編集しない)
    "**/.idea": true // IDEの設定ファイル(プロジェクトに直接関係なし)
  },

  // ファイル検索設定
  "search.exclude": { // 検索時に不要なファイルを除外し、検索パフォーマンスを向上
    "**/node_modules": true,
    "**/bower_components": true,
    "**/dist": true,
    "**/coverage": true
  },

  // オートセーブの設定
  "files.autoSave": "afterDelay", // 遅延後に自動保存
  "files.autoSaveDelay": 1000, // 1秒後に自動保存
  "files.trimTrailingWhitespace": true, // 行末の空白文字を自動削除 (Trim Trailing Whitespace)
  "files.insertFinalNewline": true, // ファイルの保存時に末尾に改行を挿入
  "files.trimFinalNewlines": true, // ファイルの保存時に末尾の余分な改行を削除

  // パフォーマンス最適化設定
  "files.watcherExclude": { // ファイルウォッチャーの除外設定
      "**/.git/objects/**": true, // 大きなフォルダを監視対象から除外 / メモリ使用量の削減
      "**/node_modules/**": true,
      "**/tmp/**": true,
  },

  // Markdown関連の設定
  "markdown.preview.fontSize": 14, // Markdownプレビューのフォントサイズ
  "markdown.preview.lineHeight": 24, // Markdownプレビューの行の高さ
  "markdown.preview.markEditorSelection": true, // プレビューとエディタの選択範囲を同期
  "markdown.preview.breaks": true, // Markdownプレビューで改行を反映
  "markdown.styles": [ // MarkdownのCSSをカスタマイズ (オプション)
    // 例えば、GitHubのMarkdownスタイルを適用する場合
    // "https://cdnjs.cloudflare.com/ajax/libs/github-markdown-css/5.1.0/github-markdown.min.css"
  ],
  "markdown-mermaid.showPreview": true, // Mermaid図のプレビューを有効化

  // Git関連の設定 (ローカルGit管理用)
  "git.autofetch": true, // 自動でリモートの変更をフェッチ (今後GitHubなどにプッシュする際にも便利)
  "git.enableCommitSigning": false, // コミット署名を有効にする場合はtrue (GPG設定が必要)
  "git.confirmSync": false, // 同期時の確認をスキップ
  "git.openRepositoryInParentFolders": "always", // プロジェクトのサブディレクトリでVS Codeを開いても、親のGitリポジトリを自動で認識し、Git機能を有効にする

  // 各プログラミング言語に適した環境をカスタマイズ
  "[python]": {
    "editor.tabSize": 4, // Pythonでは一般的にインデントにスペース4つを使用
    "editor.formatOnSave": true, // ファイル保存時に自動でコードを整形
    "editor.defaultFormatter": "ms-python.python" // Pythonのデフォルトフォーマッタとして、VS CodeのPython拡張機能(ms-python.python)に含まれるものを使用
  },
  "[javascript]": {
    "editor.tabSize": 2, //  JavaScriptではスペース2つでのインデントが一般的
    "editor.defaultFormatter": "esbenp.prettier-vscode" // JavaScript(やその他のウェブ関連言語)のフォーマッタとして、非常に人気の高いPrettierを使用するように設定
  },
  "[markdown]": {
    "editor.formatOnSave": true, // markdownlint
    "editor.formatOnPaste": true, // markdownlint
    "editor.wordWrap": "on", // Markdownファイルの内容が画面の右端で自動的に折り返されるように設定
    "editor.quickSuggestions": { // Markdown編集時に、コメント、文字列、その他の要素に対してクイックサジェスト(入力補完)を有効
      "comments": true,
      "strings": true,
      "other": true
      }
  },
  "[yaml]": {
  "editor.tabSize": 2, // YAMLではスペース2つが一般的
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "redhat.vscode-yaml" // YAML拡張機能(Red Hat)を導入する場合
  },
  "[json]": {
    "editor.tabSize": 2, // JSONもスペース2つが一般的
    "editor.formatOnSave": true,
    "editor.defaultFormatter": "esbenp.prettier-vscode" // PrettierでJSONも整形可能
  },
  "[jsonc]": { // JSON with Comments (settings.json など)
    "editor.tabSize": 2,
    "editor.formatOnSave": true,
    "editor.defaultFormatter": "esbenp.prettier-vscode"
  },

  // ターミナルの設定 (VS Code内でコマンド実行用)
  "terminal.integrated.fontSize": 14, // 視認性の良いフォントサイズ
  "terminal.integrated.inheritEnv": true, // 環境変数を継承
  "terminal.integrated.enableVisualBell": true, // ターミナルでのベルがなったときに、画像の赤線箇所のようにターミナル名の横にベルマークを表示
  "terminal.integrated.profiles.osx": {
    "zsh": {
      "path": "/bin/zsh",
      "args": []
    }
  },
  "terminal.integrated.defaultProfile.osx": "zsh",

  // 拡張機能の設定
  // Prettier設定
  "prettier.singleQuote": true, // 文字列リテラルにシングルクォーテーション(')を使用するように指示
  "prettier.trailingComma": "es5", // オブジェクトや配列の最後の要素の後にカンマ(trailing comma)を付加するかどうかを制御 / "es5" に設定することで、ES5で有効な場合にのみ trailing comma を追加

  // ESLint設定 / VS CodeのESLint拡張機能がリンティング(コードの構文やスタイルチェック)を実行するファイルの種類を指定
  "eslint.validate": [  // .js や .jsx のようなJavaScriptファイルと、.ts や .tsx のようなTypeScriptファイルに対してESLintによる検証を有効
    "javascript",
    "typescript"
  ],

  // GitLens設定
  "gitlens.currentLine.enabled": true, // カーソルがある現在の行に、その行のGit履歴情報(最終コミットの作者、日付、メッセージの一部など)がインラインで表示
  "gitlens.hovers.currentLine.over": "line", // GitLensのホバー(マウスオーバー)時の表示挙動を制御 / "line" に設定することで、現在の行にマウスカーソルを合わせたときに、その行の詳細なGit情報(完全なコミットメッセージ、変更内容、前のコミットなど)がポップアップ表示

  // Paste Image設定
  "pasteImage.path": "${currentFileDir}/images/${currentFileBasenameNoExtension}/",

  // markdownlint設定
  "markdownlint.config": {
    "MD013": false, // 行の長さをチェックするルール (MD013: Line length) を無効化
    "MD033": { "allowed_elements": ["br"] }, // HTMLタグの使用に関するルール (MD033: Inline HTML)
    "MD041": false, // 最初のヘッダーがレベル1であること (MD041: First line in file should be a top-level heading) を無効化
    "MD007": { "indent": 2 }, // 順序なしリストのインデントを制御 / 通常2スペースのインデントが推奨
    "MD024": true // 同じ内容のヘッダーが複数ある場合に警告を出すルール / true に設定すると、異なるレベル(ネスト)であれば同じ内容のヘッダーを許可
  },
  // cSpell設定
  "cSpell.dictionaries": [
    "companies", // 会社名
    "filetypes", // ファイルタイプ(.js, .tsなど)
    "softwareTerms", // ソフトウェア関連の用語
    "html", // HTMLのタグや属性
    "css", // CSSのプロパティなど
    "fonts", // フォント名
    "fullstack", // フルスタック開発関連
    "lorem-ipsum", // ダミーテキスト
    "nodejs", // Node.js関連
    "npm", // npm関連
    "typescript", // TypeScript関連
    "vue", // Vue.js関連
    "react", // React関連
    "angular", // Angular関連
    "aws", // AWS関連
    "azure", // Azure関連
    "docker", // Docker関連
    "git", // Git関連
    "java", // Java関連
    "json", // JSON関連
    "markdown", // Markdown関連
    "php", // PHP関連
    "python", // Python関連
    "sql", // SQL関連
    "go", // Go言語関連
    "rust" // Rust言語関連
    "bash", //Bash関連
    "powershell", //Powershell関連
    "cpp", // C++言語関連
    "charp" // C#言語関連
    // 他にも多数あります。C Spellのドキュメントで確認できます。
  ]
  
}
```

## VS Codeの拡張機能

1. **Draw.io Integration:**
   * VS Code内でDraw.io(diagrams.net)の図を作成・編集できます。
   * ネットワーク構成図、システムアーキテクチャ図、攻撃ツリーなど、セキュリティ学習において図解は非常に重要です。
   * `*.drawio`または`*.dio`ファイルをVS Codeで開くと、Draw.ioのエディタが立ち上がります。

2. **PlantUML:**
   * Mermaidと同様に、テキストベースでUML図(シーケンス図、クラス図など)を作成・プレビューできます。
   * Mermaidと合わせて、図の表現力を高めるのに役立ちます。特にシステムの設計やプロセスの理解を深める際に強力
   * `*.drawio`または`*.dio`ファイルをVS Codeで開くと、Draw.ioのエディタが立ち上がります。

3. **テキスト校正くん:**
   * VS CodeでテキストファイルやMarkdownファイルの日本語の文章をチェックする拡張機能
   * 自動修正可能な校正ルール違反については、違反箇所にテキストカーソルを置いた状態でキーボードの`[Alt] + [Enter]` (`MacOS`の場合は`[command] + [. ]`) を同時押しすると、校正ルール違反は自動的に修正されます。

4. **Code Spell Checker:**
   * スペルミスをチェックし、学習ノートの品質を向上させます。
   * 特に技術用語を記述する際に役立ちます。

5. **Output Colorizer:**
   * VS Codeの出力に色をつける拡張機能

6. **indent-rainbow:**
   * インデントに色をつける拡張機能

7. **Material Icon Theme:**
   * ファイルアイコンをデフォルトよりもわかりやすいアイコンに変更する拡張機能

8. **Path Intellisense:**
   * ファイルパスの自動補完機能を提供します。
   * Markdown内で画像や関連ファイルへのリンクを記述する際に便利です。

9. **Auto Close Tag / Auto Rename Tag:**
   * tagを自動で閉じたり、リネームしてくれる拡張機能

10. **Error Lens:**
    * エディタ内に警告やエラーが表示される

11. **Bookmarks::**
    * コードやMarkdownの特定の行にブックマークを設定できます。後から参照したい重要な情報や、途中までしか書けていないセクションなどにマークを付けておくと、素早くアクセスできて便利

12. **TODO Highlight:**
    * TODOコメントをハイライト表示する拡張機能

13. **Todo Tree:**
    * TODOコメントの一覧を表示することができる拡張機能

14. **YAML:**
    * YAML拡張機能(Red Hat)

15. **Prettier - Code formatter:**
    * コードだけでなくMarkdownもフォーマットできます。
    * 記述スタイルを統一し、読みやすいノートを維持するのに役立ちます。
    * `settings.json`で`editor.defaultFormatter`を`esbenp.prettier-vscode`に設定し、`editor.formatOnSave`を`true`にすると、保存時に自動で整形されます。

16. **Markdown All in One:**
    * Markdownのプレビュー、自動補完、目次生成など、Markdownの執筆に必要な機能が全て詰まっています。
    * 特に「Table of Contents(目次)」機能は、長いノートで非常に役立ちます。
    * `Cmd + Shift + P`でコマンドパレットを開き、「Markdown All in One: Create Table of Contents」で自動生成できます。

17. **markdownlint:**
    * markdownlintは「Markdown」の構文とスタイルをチェックしてくれる拡張機能

18. **Mermaid Markdown Syntax Highlighting:**
    * Markdown内でMermaid記法(テキストベースでフローチャートやシーケンス図などを記述できる)を使用する際に、シンタックスハイライトとプレビューを有効にします。
    * 複雑なプロセスや情報の流れを図示するのに便利です。

19. **Markdown Preview Enhanced:**
    * Markdownをよりプレビューを見やすくしてくれる拡張機能

20. **Markdown Link Checker:**
    * Markdown内のリンク切れをチェックする拡張機能

21. **Markdown Link Updater:**
    * ワークスペース内のファイルが移動またはリネームされると、Markdownリンクが自動的に更新

22. **Table of Contents:**
    * Markdown All in Oneにも目次機能がありますが、この拡張機能はより高度な目次生成機能を提供

23. **Paste Image:**
    * クリップボードの画像をMarkdownに直接貼り付けることができます。
    * スクリーンショットなどをノートに含める際非常に便利です。
    * 貼り付けた画像は、設定したパス(例: `./images/`)に自動で保存されます。

24. **GitLens — Git supercharged:**
    * Gitの情報をVS Code上で視覚的に強化します。
    * ファイルの各行の最終コミット情報(誰がいつ変更したか)を表示したり、コミット履歴を詳細に確認したりできます。
    * ローカルGitでのバージョン管理をより強力にサポートします。

25. **Git Graph:**
    * Gitの履歴をグラフで表示する拡張機能

26. **Git History:**
    * GitHub Actions関連の機能を提供する拡張機能

27. **GitHub Actions:**
    * GitHub Actions関連の機能を提供する拡張機能

28. **GitHub Pull Requests and Issues:**
    * GitHubのプルリクエストをVS Codeで直接管理できる拡張機能

29. **GitHub Copilot:**
    * AIを活用したコード補完ツール

30. **Container Tools
    * Docker コンテナのツール

## その他、Mac miniでの環境構築のヒント

* **Homebrewの導入:**
  * Mac用のパッケージマネージャであるHomebrewを導入しておくと、今後必要なツール(Git自体もそうですが、各種セキュリティツールなど)のインストールが非常に楽になります。
  * ターミナルで以下のコマンドを実行してインストールできます。

    ```bash
    /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
    ```

* **Gitのインストール:**
  * MacにはデフォルトでGitがインストールされていることが多いですが、最新版をHomebrewでインストールしておくことをオススメします。

    ```bash
    brew install git
    ```

* **ターミナルアプリの検討:**
  * VS Codeの統合ターミナルでも十分ですが、iTerm2のような高機能ターミナルアプリも検討すると良いでしょう。分割表示やタブ管理など、作業効率を向上させる機能が豊富です。

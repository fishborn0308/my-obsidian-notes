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




























## Lab 1.3: Masscan

### Background

初期認証情報を入手したら、Hiboxyのネットワークインフラをマッピングする必要があります。各システムを個別にスキャンする代わりに、Masscanを使用してネットワーク全体の全サービスを迅速に特定します。この高速偵察により、Webサーバー、SSHサービス、SMB共有、その他の潜在的な侵入経路が明らかになります。

スキャン結果は複数形式で保存されるため、特定のサービスを効率的に検索し、ペネトレーションテストの次のフェーズでより焦点を絞った攻撃を行うためのターゲットリストを作成できます。

### 1: Scan Preparation

この実習では、まず対象ネットワークのスキャンから始めます。このスキャンにはMasscanを使用します。書籍で説明した通り、Masscanはパケット送信時にカーネルをバイパスします。つまり、LinuxシステムがSYN-ACK応答を受信すると、処理方法が分からずRSTで応答します。このスキャンはノイズを発生させますが、リセットを防止することで影響を軽減したいと考えています。これを実現する方法は二つあります。

一つ目は、ファイアウォールがRSTパケットを送信しないように設定すること。
二つ目は、既知の送信元ポートを使用し、SYN-ACK応答が特定ポートに届くようにした後、そのポートへのトラフィックをブロックすることです。

ここでは後者の手法を採用します。
送信元ポートとして55555を選択し、このポートへのパケットをブロックするファイアウォールルールを作成します。Masscanは依然としてパケットを検出するため応答を見逃すことはありませんが、OSが余分なRSTパケットを送信するのを防ぎます。
ファイアウォールルールを作成するには、以下のコマンドを使用します：

```
sudo iptables -A INPUT -p tcp --dport 55555 -j DROP
```

### 2: Initial Scan

スキャンを開始しましょう。このスキャンでは、送信元ポート55555と毎秒15,000パケットのレートを使用します。
複数人が同時にスキャンを実行することでターゲットが飽和状態になるのを防ぐため、ローカルホストをスキャンします。

以下の設定でMasscanを起動します：
- 全65536 TCPポートをスキャン
- レート 15000
- 送信元ポート 55555
- 出力をバイナリ形式でlocal.masscanというファイルに保存
- ターゲット 127.0.0.1
- 
VPN帯域幅を節約するため、当面はローカルホストをスキャンします。

```
sudo masscan --ports 0-65535 --rate 15000 --src-port=55555 -oB /tmp/local.masscan 127.0.0.1
```

```
sec560@560vm:~/Desktop$ sudo iptables -A INPUT -p tcp --dport 55555 -j DROP
sec560@560vm:~/Desktop$ sudo masscan --ports 0-65535 --rate 15000 --src-port=55555 -oB /tmp/local.masscan 127.0.0.1
Starting masscan 1.3.9-integration (http://bit.ly/14GZzcT) at 2026-03-03 02:08:16 GMT
Initiating SYN Stealth Scan
Scanning 1 hosts [65536 ports/host]
sec560@560vm:~/Desktop$
```

macOSでVMware Fusionの警告が表示されましたか？

macOSでVMware Fusionを使用している場合、「仮想マシンがすべてのネットワーク通信を監視しようとしており、管理者権限が必要です」というVMware Fusionの警告が表示されることがあります。このプロンプトでは安全に「キャンセル」をクリックできます。

You should see the local.masscan file in the /tmp directory.

```
ls -l /tmp/local.masscan
```

```
sec560@560vm:~/Desktop$ ls -l /tmp/local.masscan 
-rw-r--r-- 1 root root 273 Mar  3 02:08 /tmp/local.masscan
sec560@560vm:~/Desktop$ 
```

このスキャンはローカルシステムに対して実行したため、結果は特に興味深いものではありません。重要なのはMasscanの実行方法を理解することです。

ターゲットネットワーク全体に対する完全なポートスキャンは、Masscanを使用しても時間がかかります。時間を節約するため、~/labs/masscan_10.130.10.0_24_full.bin に完全なMasscanバイナリファイルが用意されています。分析にはこれを使用します。

提供されたファイル内のMasscan結果を確認しましょう。

### 3: Converting the Scan to XML

Masscanによるスキャンがいかに簡単かは既にご覧いただきました。時間を節約するため、以下のオプションを使用したMasscanのバイナリ出力を提供します。注意：実行に時間がかかりすぎるため、これらのオプションは使用しないでください！

    --ports 0-65535: 全てのTCPポートをチェック
    --rate 15000: 1秒あたり最大15,000パケットを送信（他のスキャン実行者がいないラボ環境では安全）
    --banners: SYN-ACK後のRSTパケット送信を中止し、ACK（完全接続確立）後にサーバー送信データ（SSHやSMTPバナーなど）を解析
    --src-port=55555: 指定した送信元ポート。ファイアウォールルールでRSTパケットを制御可能
    -oB ~/labs/masscan_10.130.10.0_24_full.bin: バイナリ出力を保存
    -e tun0: VPNインターフェースを指定
    10.130.10.0/24: スキャン対象ネットワーク範囲

このファイルを使ってMasscanの各種出力を確認しましょう。--readscanオプションで既存のバイナリ形式を読み込み、任意の出力形式でファイルを書き出せます。出力形式は以下の通り：

    -oX: XML
    -oG: Grep対応形式
    -oJ: JSON形式
    -oL: リスト形式

まず--readscanオプションでXML形式に変換し、ファイルをfull.xmlとして保存します。次にlessコマンドでファイルの内容を確認します。

```
masscan --readscan ~/labs/masscan_10.130.10.0_24_full.bin -oX /tmp/masscan_10.130.10.0_24_full.xml
less /tmp/masscan_10.130.10.0_24_full.xml
```

XML形式は人間が直接閲覧するものではありませんが、他のツール（GoWitnessや脆弱性スキャナーなど）との連携に優れています。lessコマンドを終了するにはqと入力してください。
そのファイル名について気になりますか？

多くの人が使うよりも冗長ではありますが、明確で説明的なファイル名を付ける時間を取ることで、実際のペネトレーションテスト中に避けられない混乱を軽減できると筆者は考えています。ファイル名はツール名（masscan_）、対象ネットワーク（10.130.10.0/24）、スキャンタイプ（_full）、出力形式（.xmlと.bin）を示しています。これは特にディレクトリ内に複数のファイルがある場合に有用です。

より読みやすい形式であるGrepable形式を見てみましょう。

### 4: Grepable Format

バイナリ形式からgrep可能な形式（-oGオプションを使用）を抽出し、ファイルをfull.grepとして保存する。



```
masscan --readscan ~/labs/masscan_10.130.10.0_24_full.bin -oG /tmp/masscan_10.130.10.0_24_full.gnmap
```



```
sec560@560vm:~$ masscan --readscan ~/labs/masscan_10.130.10.0_24_full.bin -oG /tmp/masscan_10.130.10.0_24_full.gnmap
[+] --readscan ~/labs/masscan_10.130.10.0_24_full.bin
```

Let's view the sorted grepable format by using the cat command with the full.grep file.



```
cat /tmp/masscan_10.130.10.0_24_full.gnmap | sort
```



```
sec560@560vm:~$ cat /tmp/masscan_10.130.10.0_24_full.gnmap | sort
Host: 10.130.10.10 ()   Port: 22    Service: ssh    Banner: SSH-2.0-OpenSSH_9.6p1 Ubuntu-3ubuntu13.5\x0d\x0a\x00\x00\x04\x5c\x07\x14\xd9\x8f\xde\xc2\xd5\x05\xcc\x13\xfb\xa9\xe3\xf09cb\xa9\x00\x00\x011sntrup761x25519-sha512@openssh.com,[...trimmed...]
Host: 10.130.10.10 ()   Port: 23    Service: ssh    Banner: SSH-2.0-OpenSSH_9.6p1 Ubuntu-3ubuntu13.5\x0d\x0a\x00\x00\x04\x5c\x07\x14%Z\x9b\x8d\xe5\xd8\x912.\xcc\xd9\xd6\x01\x93\xb5@\x00\x00\x011sntrup761x25519-sha512@openssh.com,curve25519-sha256,curve25519-sha256@libssh.org,[...trimmed...]
Host: 10.130.10.10 ()   Port: 80    Service: http   Banner: HTTP/1.1 200 OK\x0d\x0aServer: nginx/1.24.0 (Ubuntu)\x0d\x0aDate: Tue, 24 Sep 2024 04:22:22 GMT\x0d\x0aContent-Type: text/html\x0d\x0aContent-Length: 615\x0d\x0aLast-Modified: Wed, 11 Sep 2024 23:16:10 GMT\x0d\x0aConnection: close\x0d\x0aETag: \x2266e224ba-267\x22\x0d\x0aAccept-Ranges: bytes\x0d\x0a\x0d
Host: 10.130.10.10 ()   Port: 80    Service: http.server    Banner: nginx/1.24.0 (Ubuntu)
Host: 10.130.10.10 ()   Port: 80    Service: title  Banner: Welcome to nginx!
Host: 10.130.10.11 ()   Port: 22    Service: ssh    Banner: SSH-2.0-OpenSSH_9.6p1 Ubuntu-3ubuntu13.5\x0d\x0a\x00\x00\x04\x5c\x07\x14\xe6s=A;\xa1\xfb\xe9\x22\xae\xbcz\x86\x87\x18O\x00\x00\x011sntrup761x25519-sha512@openssh.com,
...truncated for brevity...
```

Nmap形式では、特定のホストの全ポートが1行にまとめられます。効果はほぼ同じですが、Masscanのgrep対応形式は各システム/ポートの組み合わせが別々の行に表示される点で若干異なります。

また、ポート22と80のバナー情報にも注目してください。Masscanは--bannersオプション使用時にこの情報を保存します。Masscanにはバージョン情報を抽出する機能が含まれていないため、これは手動で行う必要があります。

次に、環境内でポート53をリッスンしているすべてのDNSサービスをgrepで検出します。



```
grep -w 53/open /tmp/masscan_10.130.10.0_24_full.gnmap
```

`-w (--word-regexp): パターンを単語として検索する。完全一致

```
sec560@560vm:~$ grep -w 53/open /tmp/masscan_10.130.10.0_24_full.gnmap
Timestamp: 1727151766   Host: 10.130.10.4 ()    Ports: 53/open/tcp//domain//
```

ポート53でリスニングしているDNSサーバーが1台あることがすぐにわかります。このサービスにはバナーが表示されていないことに注意してください。これは、DNSがクライアントからリクエストが送信されるまで応答を送信しないためです。

次に、ポート445でリスニングしているホストを探して、Windowsシステムと思われるものを素早く検索します。


```
grep -w 445/open /tmp/masscan_10.130.10.0_24_full.gnmap
```


```
sec560@560vm:~$ grep -w 445/open /tmp/masscan_10.130.10.0_24_full.gnmap
Timestamp: 1727151580   Host: 10.130.10.21 ()   Ports: 445/open/tcp//microsoft-ds//
Timestamp: 1727151634   Host: 10.130.10.7 ()    Ports: 445/open/tcp//microsoft-ds//
Timestamp: 1727151644   Host: 10.130.10.25 ()   Ports: 445/open/tcp//microsoft-ds//
Timestamp: 1727151649   Host: 10.130.10.23 ()   Ports: 445/open/tcp//microsoft-ds//
Timestamp: 1727151680   Host: 10.130.10.5 ()    Ports: 445/open/tcp//microsoft-ds//
Timestamp: 1727151688   Host: 10.130.10.4 ()    Ports: 445/open/tcp//microsoft-ds//
Timestamp: 1727151777   Host: 10.130.10.44 ()   Ports: 445/open/tcp//microsoft-ds//
```

ポート445が開いているホストの数を数えましょう：


```
grep -w 445/open /tmp/masscan_10.130.10.0_24_full.gnmap | wc -l
```



```
sec560@560vm:~$ grep -w 445/open /tmp/masscan_10.130.10.0_24_full.gnmap | wc -l
7
```

確認できる限り、ポート445でリスニング状態かつアクセス可能なシステムは7つ存在し、おそらくWindowsシステムである。

### 5: JSON Format

バイナリ形式からJSON形式を抽出（-oJオプションを使用）し、ファイルをfull.jsonとして保存する。



```
masscan --readscan ~/labs/masscan_10.130.10.0_24_full.bin -oJ /tmp/masscan_10.130.10.0_24_full.json
```

それでは、JSONファイルの最初の数行を見てみましょう。



```
head /tmp/masscan_10.130.10.0_24_full.json
```



```
sec560@560vm:~$ head /tmp/masscan_10.130.10.0_24_full.json
[
{   "ip": "10.130.10.11",   "timestamp": "1727151519", "ports": [ {"port": 80, "proto": "tcp", "status": "open", "reason": "syn-ack", "ttl": 63} ] }
,
{   "ip": "10.130.10.6",   "timestamp": "1727151523", "ports": [ {"port": 80, "proto": "tcp", "status": "open", "reason": "syn-ack", "ttl": 127} ] }
,
{   "ip": "10.130.10.11",   "timestamp": "1727151523", "ports": [ {"port": 80, "proto": "tcp", "service": {"name": "http.server", "banner": "nginx/1.24.0 (Ubuntu)"} } ] }
,
{   "ip": "10.130.10.11",   "timestamp": "1727151523", "ports": [ {"port": 80, "proto": "tcp", "service": {"name": "title", "banner": "Welcome to nginx!"} } ] }
,
{   "ip": "10.130.10.11",   "timestamp": "1727151523", "ports": [ {"port": 80, "proto": "tcp", "service": {"name": "http", "banner": "HTTP/1.1 200 OK\u000d\u000aServer: nginx/1.24.0 (Ubuntu)\u000d\u000aDate: Tue, 24 Sep 2024 04:18:42 GMT\u000d\u000aContent-Type: text/html\u000d\u000aContent-Length: 615\u000d\u000aLast-Modified: Wed, 11 Sep 2024 23:16:45 GMT\u000d\u000aConnection: close\u000d\u000aETag: \u002266e224dd-267\u0022\u000d\u000aAccept-Ranges: bytes\u000d\u000a\u000d"} } ] }
```

XML形式と同様に、これは人間が直接読むことを意図したものではありません。他のツールによって解析され利用されることを想定して設計されています。なお、JSON形式にもバナー情報が含まれています。
別のツールでJSONを解析したいですか？

### 6: List Format

バイナリ形式からリスト形式（-oLオプション使用）を抽出し、出力をfull.txtとして保存する。



```
masscan --readscan ~/labs/masscan_10.130.10.0_24_full.bin -oL /tmp/masscan_10.130.10.0_24_full.txt
```



```
sec560@560vm:~$ masscan --readscan ~/labs/masscan_10.130.10.0_24_full.bin -oL /tmp/masscan_10.130.10.0_24_full.txt
[+] --readscan /home/sec560/labs/masscan_10.130.10.0_24_full.bin
```

では、新しく作成したファイルの内容を見てみましょう。



```
head -n 15 /tmp/masscan_10.130.10.0_24_full.txt
```


```
sec560@560vm:~$ head -n 15 /tmp/masscan_10.130.10.0_24_full.txt
#masscan
open tcp 80 10.130.10.11 1727151519
open tcp 80 10.130.10.6 1727151523
banner tcp 80 10.130.10.11 1727151523 http.server nginx/1.24.0 (Ubuntu)
banner tcp 80 10.130.10.11 1727151523 title Welcome to nginx!
banner tcp 80 10.130.10.11 1727151523 http HTTP/1.1 200 OK\x0d\x0aServer: nginx/1.24.0 (Ubuntu)\x0d\x0aDate: Tue, 24 Sep 2024 04:18:42 GMT\x0d\x0aContent-Type: text/html\x0d\x0aContent-Length: 615\x0d\x0aLast-Modified: Wed, 11 Sep 2024 23:16:45 GMT\x0d\x0aConnection: close\x0d\x0aETag: \x2266e224dd-267\x22\x0d\x0aAccept-Ranges: bytes\x0d\x0a\x0d
open tcp 3389 10.130.10.21 1727151524
banner tcp 80 10.130.10.6 1727151526 http.server Microsoft-IIS/10.0
banner tcp 80 10.130.10.6 1727151526 title IIS Windows Server
banner tcp 80 10.130.10.6 1727151526 http HTTP/1.1 200 OK\x0d\x0aContent-Type: text/html\x0d\x0aLast-Modified: Wed, 11 Sep 2024 23:28:42 GMT\x0d\x0aAccept-Ranges: bytes\x0d\x0aETag: \x2282b3d57a24db1:0\x22\x0d\x0aServer: Microsoft-IIS/10.0\x0d\x0aDate: Tue, 24 Sep 2024 04:18:45 GMT\x0d\x0aConnection: close\x0d\x0aContent-Length: 703\x0d\x0a\x0d
open tcp 3389 10.130.10.5 1727151531
open tcp 135 10.130.10.7 1727151534
open tcp 8172 10.130.10.25 1727151536
open tcp 5985 10.130.10.21 1727151539
open tcp 5985 10.130.10.23 1727151539
```

この形式は読み取りがはるかに簡単です。列は次の通りです：

    タイプ: open または banner
    プロトコル: 常に tcp
    ポート: この項目のポート番号
    IPアドレス: 項目のアドレス
    タイムスタンプ: エントリのエポック時間
    追加情報: banner 行の場合、プロトコル（例では http）とバナーを表示

grepを使用してtcp 22を検索すると、ポート22の結果を確認できます。



```
grep -w 'tcp 22' /tmp/masscan_10.130.10.0_24_full.txt
```



```
sec560@560vm:~$ grep -w 'tcp 22' /tmp/masscan_10.130.10.0_24_full.txt
open tcp 22 10.130.10.22 1727151568
banner tcp 22 10.130.10.22 1727151599 ssh SSH-2.0-OpenSSH_9.6p1 Ubuntu-3ubuntu13.5\x0d\x0a\x00\x00\x04\x5c\x07\x14(\x82\x18\xf5\xbc\xe4\x9am\xea\xe2s\x00;\xa9\x03 \x00\x00\x011sntrup761x25519-sha512@openssh.com,curve25519-sha256,curve25519-sha256@libssh.org[...trimmed...]
```

ここでは、SSHサーバーが開いている10.130.10.22とその関連バナーを確認できます。

再度grepを使用し、ポート25（SMTP）をリッスンしているシステムを探します。



```
grep -w 'tcp 25' /tmp/masscan_10.130.10.0_24_full.txt
```



```
sec560@560vm:~$ grep -w 'tcp 25' /tmp/masscan_10.130.10.0_24_full.txt
open tcp 25 10.130.10.25 1727151674
banner tcp 25 10.130.10.25 1727151706 ssl TLS/1.2 cipher:0xc030, mail01, mail01, mail01.hiboxy.com
banner tcp 25 10.130.10.25 1727151706 X509 MIIDBzCCAe+gAwIBAgIQODDHsNbjBZNBr4Q8jkb1izANBgkqhkiG9w0BAQUFADARMQ8wDQYDVQQDEwZtYWlsMDEwHhcNMjQwOTExMjM1NTUyWhcNMjkwOTExMjM1NTUyWjARMQ8wDQYDVQQDEwZtYWlsMDEwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQDF/T7gFVULRoivsyQQQjJIdbLydrPKEKdlaoA8akwan4LDX8hT+OoISu19i0/Khdn8Xy6DUZSY3dGeMZPBO149CNeyiSXQVOr38BDPBL2mxJhdBjf3BKFe2H0CspBdo7vCDkLnVoKCYahUdpgkO5kL8m51g7nTH5L117L48nX2NJg7W5kTcFp6fBfCdAKBZk79f+8udN4gKCVJf+YcUIeyKpezziGdbQXowAGI5KhXM21iwbKMrDVW/Bw8cQlgNgtq7/gBG4xVQmIc57msgbMozZiIEUmNjbEzLmOlEz1La/AUWb4QeBVW3m/Py4UPgJ3OuRFyC6nwKEKNxb4cjqARAgMBAAGjWzBZMA4GA1UdDwEB/wQEAwIFoDAkBgNVHREEHTAbggZtYWlsMDGCEW1haWwwMS5oaWJveHkuY29tMBMGA1UdJQQMMAoGCCsGAQUFBwMBMAwGA1UdEwEB/wQCMAAwDQYJKoZIhvcNAQEFBQADggEBAAY9K0ea6ExbNZdkc6VavH0U1VnudcdGpTSc6v+K+rWiUC3W/yE96/zWMbPoBoZcdFUA269H9DZK4z0yTQL9queIY7I2XuldS3vPRuiTLlo5V93wM5YgWWmfmpFdX4gcbqi9catyzmuMIGJIYjguPUfSSmBeRU/7hb9J5bplJ3eoKYc+4msRrh6gvQNYvm0CdaQ9wnpHRbYRKc5KsLjYGJgaMf700lqOyRjMLARw1m8pJnK/z42h2zjLue2Sn1KCFNscl8m0NTu7BUfwYWInZuE6bZxlKubSNCKNk7fXjhUkXdOHH7/TIycg02bWKXGK53froqvkJiWOPULI6h1ur0g=
banner tcp 25 10.130.10.25 1727151706 smtp 220 mail01.hiboxy.com Microsoft ESMTP MAIL Service ready at Tue, 24 Sep 2024 04:21:14 +0000\x0a250-mail01.hiboxy.com Hello [10.254.252.3]\x0a250-SIZE 37748736\x0a250-PIPELINING\x0a250-DSN\x0a250-ENHANCEDSTATUSCODES\x0a250-STARTTLS\x0a250-X-ANONYMOUSTLS\x0a250-AUTH NTLM\x0a250-X-EXPS GSSAPI NTLM\x0a250-8BITMIME\x0a250-BINARYMIME\x0a250-CHUNKING\x0a250-SMTPUTF8\x0a250 XRDST\x0a220 2.0.0 SMTP server ready
```

10.130.10.25 システムの SMTP バナーも、ホスト名が mail01.hiboxy.com であることを示しています。

### 7: Extracting Live Hosts and Ports

Greppable (.gnmap) 出力の一部を見てみましょう。

```
Timestamp: 1727151519   Host: 10.130.10.11 ()   Ports: 80/open/tcp//http//
Timestamp: 1727151523   Host: 10.130.10.6 ()    Ports: 80/open/tcp//http//
Host: 10.130.10.11 ()   Port: 80    Service: http.server    Banner: nginx/1.24.0 (Ubuntu)
```

各ポートの状態は出力で1行ずつ表示され、バナー情報は別の行に表示されることに注意してください。

grepを使用して/open/tcp/を含むすべての行を検索しましょう。これを行うには、grepコマンドを使用できます：



```
grep '/open/tcp' /tmp/masscan_10.130.10.0_24_full.gnmap
```


```
sec560@560vm:~$ grep '/open/tcp' /tmp/masscan_10.130.10.0_24_full.gnmap
Timestamp: 1727151519   Host: 10.130.10.11 ()   Ports: 80/open/tcp//http//
Timestamp: 1727151523   Host: 10.130.10.6 ()    Ports: 80/open/tcp//http//
Timestamp: 1727151524   Host: 10.130.10.21 ()   Ports: 3389/open/tcp//ms-wbt-server//
Timestamp: 1727151531   Host: 10.130.10.5 ()    Ports: 3389/open/tcp//ms-wbt-server//
...output truncated for brevity...
```

ポートを取得したい場合、awkコマンドを使用できます。awkはcutコマンドよりも優れており、複数の連続した区切り文字を処理できます（masscanは列を揃えるために必要に応じてスペースを追加するため）。awkを使用して4番目のフィールド（ポート番号）を出力できます。

まず、出力の4番目のフィールドである稼働中のホストを取得します：



```
grep '/open/tcp' /tmp/masscan_10.130.10.0_24_full.gnmap | awk '{print $4}' | sort -uV
```



```
sec560@560vm:~$ grep '/open/tcp' /tmp/masscan_10.130.10.0_24_full.gnmap | awk '{print $4}' | sort -uV
10.130.10.4
10.130.10.5
10.130.10.6
10.130.10.7
10.130.10.10
10.130.10.11
10.130.10.21
10.130.10.22
10.130.10.23
10.130.10.25
10.130.10.33
10.130.10.44
10.130.10.45
```

ソートオプション:

    u: 一意の値のみを返す
    V: バージョン番号の自然順ソート（IPアドレスでも有効！）

稼働中のホストを、nmapなどの他のツールが利用できる形式で/tmp/10.130.10.0_24_alivehosts.txtのようなファイルに保存できます。この作業は演習としてお任せします。

次に、組織内のすべてのリスニングポートのリストを取得します。ポートは7番目のフィールドに含まれているため、そこから始めましょう:



```
grep '/open/tcp' /tmp/masscan_10.130.10.0_24_full.gnmap | awk '{print $7}' | sort -uV | head
```



```
sec560@560vm:~$ grep '/open/tcp' /tmp/masscan_10.130.10.0_24_full.gnmap | awk '{print $7}' | sort -uV | head
22/open/tcp//ssh//
23/open/tcp//telnet//
25/open/tcp//smtp//
53/open/tcp//domain//
80/open/tcp//http//
135/open/tcp//epmap//
139/open/tcp//netbios-ssn//
389/open/tcp//ldap//
443/open/tcp//https//
444/open/tcp//snpp//
```

次に、`cut` を使ってそれらのユニークなポート番号のみを取得しましょう：



```
grep '/open/tcp' /tmp/masscan_10.130.10.0_24_full.gnmap | awk '{print $7}' | cut -d '/' -f 1 | sort -un
```



```
sec560@560vm:~$ grep '/open/tcp' /tmp/masscan_10.130.10.0_24_full.gnmap | awk '{print $7}' | cut -d '/' -f 1 | sort -un
22
23
25
53
80
135
139
389
443
444
445
464
465
475
476
477
587
[...truncated for brevity...]
```

次に、開いているポートを open_ports.txt というファイルに保存します。このファイルは nmap などの他のツールが利用できる形式で保存します。



```
grep '/open/tcp' /tmp/masscan_10.130.10.0_24_full.gnmap | awk '{print $7}' | cut -d '/' -f 1 | sort -un | tr '\n' ',' | sed 's/,$//' > /tmp/10.130.10.0_24_openports.txt
```

このコマンドからは出力はありません。

稼働中のIPアドレスとポートを特定することで、Nmapを含む他のツールに適切なターゲットリストを提供し、スキャンを高速化できます。

### 結論

本ラボでは、Masscanがポートスキャンを高速に実行する手段であることを確認しました。ただし、スキャン速度が速すぎてターゲットインフラをダウンさせないよう注意が必要です。Masscanはポートスキャンに特化したツールです。

今後のラボでは、Nmapの高度な機能、特にOSフィンガープリンティングとバージョンスキャンについて検討します。これらはターゲットマシン上で動作するOSの種類やソフトウェアのバージョン情報を収集する手法です。この情報は、攻撃対象を絞り込み、特定のツールやエクスプロイトを用いてターゲット環境へのアクセスを得る上で、ペネトレーションテスターにとって極めて有用です。

さらに、GoWitnessを使用して組織内でアクセス可能なウェブサイトを素早く確認します。これにより、ペネトレーションテスターは興味深いウェブサイトを迅速に特定できます。

## Lab 1.4: Nmap

### Background

Masscanで概要を把握した後は、特定のシステムに関する詳細な情報が必要です。Nmapを使用して標的を絞ったスキャンを実施し、正確なサービスバージョン、オペレーティングシステム、潜在的な脆弱性を特定します。

偵察により、Windowsドメインコントローラー、Linuxサーバー、および初期アクセス獲得後の足掛かりとなり得る各種サービスが明らかになります。詳細なサービス列挙は、今後の攻撃におけるエクスプロイト戦略の指針となります。

### 1: Initial Scan

ターゲットサブネットのスキャンを実行しましょう。

```
nmap -n 10.130.10.1-10
```

```
sec560@560vm:~$ nmap -n 10.130.10.1-10
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-08-14 18:08 UTC
Nmap scan report for 10.130.10.5
Host is up (0.083s latency).
Not shown: 997 filtered tcp ports (no-response)
PORT     STATE SERVICE
80/tcp   open  http
445/tcp  open  microsoft-ds
3389/tcp open  ms-wbt-server

Nmap scan report for 10.130.10.6
Host is up (0.082s latency).
Not shown: 998 filtered tcp ports (no-response)
PORT     STATE SERVICE
80/tcp   open  http
3389/tcp open  ms-wbt-server

Nmap scan report for 10.130.10.10
Host is up (0.086s latency).
Not shown: 996 closed tcp ports (conn-refused)
PORT     STATE SERVICE
22/tcp   open  ssh
23/tcp   open  telnet
80/tcp   open  http
9100/tcp open  jetdirect

Nmap done: 10 IP addresses (3 hosts up) scanned in 11.85 seconds
```

-n オプションは、Nmap がドメイン名を解決しないことを意味します。Nmap スキャン実行中に p キーを押すとパケット単位のログ記録が有効になり、Shift-p で無効になります。

講義で説明したように、Nmapはroot権限で実行されていない限りICMPパケットを送信できません。先ほどのコマンドをsudoまたはrootユーザーで実行すれば、ICMPパケットも確認できるでしょう。

また、vキーとdキーをそれぞれ複数回押して、詳細出力とデバッグ情報を表示してみてください。入力が追いつかない場合は、スキャンを再実行してからキーを押すことを試みてください。

### 2: Scanning 10.130.10.33

次に、ターゲットマシン10.130.10.33に対してTCPポートスキャンを実行します。

新しいターミナルを開く

tcpdumpを起動し、ネットワーク10.130.10.0/24に関連するトラフィックを表示するように設定します（名前解決は行いません）。

パケットを監視するためのスニッファを実行できるよう、新しいターミナルウィンドウを起動します：


```
sudo tcpdump -i tun0 -nn net 10.130.10.0/24
```



以下のコマンドを実行するまで、パケットは表示されません。

次に、元のNmapターミナルウィンドウに戻り、10.130.10.33ホストをスキャンするためにNmapを呼び出します。TCP接続スキャン（完全なスリーウェイハンドシェイク）を実行します：



```
sudo nmap -n -sT 10.130.10.33 -F
```


```
sec560@560vm:~$ sudo nmap -n -sT 10.130.10.33 -F
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-08-14 18:11 UTC
Nmap scan report for 10.130.10.33
Host is up (0.075s latency).
Not shown: 98 filtered tcp ports (no-response)
PORT     STATE SERVICE
1433/tcp open  ms-sql-s
3389/tcp open  ms-wbt-server


Nmap done: 1 IP address (1 host up) scanned in 3.12 seconds
```

Nmapは、上記のようにスキャン完了までの合計時間を表示します。

また、スニッファの出力も確認してください。あなたのマシンからターゲットに向けて大量のSYNパケット（S）が送信されているはずです。3ウェイハンドシェイクを完了させるため、比較的少数のSYN-ACKが返ってくるほか、あなたのマシンからACKが送信されます。

ただし、この実行ではNmapは全てのTCPポートをスキャンしていません。/usr/share/nmap/nmap-servicesファイルに示されているように、最も頻繁に使用される上位100ポート（-Fオプションのため。デフォルトでは上位1,000ポート）をスキャンしています。-p 0-オプションで全ポートをスキャンする場合、このコマンドは5分以上かかる可能性があります。全ポートをスキャンしたい場合は、ラボを初回完了後に実施してください。--top-ports 3000 でより多くのポートをスキャンした場合の結果を見てみましょう。



```
sudo nmap -n -sT 10.130.10.33 --top-ports 3000
```



```
sec560@560vm:~$ sudo nmap -n -sT 10.130.10.33 --top-ports 3000
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-08-14 18:12 UTC
Nmap scan report for 10.130.10.33
Host is up (0.078s latency).
Not shown: 2997 filtered tcp ports (no-response)
PORT     STATE SERVICE
1433/tcp open  ms-sql-s
3389/tcp open  ms-wbt-server
5985/tcp open  wsman

Nmap done: 1 IP address (1 host up) scanned in 14.40 seconds
```

このスキャンは時間がかかりましたが、Nmapがより多くの開いているポートを発見したことに注目してください。もちろん、スキャンするポート数が多いほど時間はかかります。これは時間と精度のトレードオフです。65,536ポートすべてをチェックすることは可能ですが、非常に長い時間がかかります（1,000ポートのスキャンの約65倍）。ホスト数が少ない場合は問題ないかもしれませんが、大規模なネットワークやターゲット範囲ではスキャンが著しく遅くなる可能性があります。
3: 出力形式

次に、-oAオプションでNmapが生成できる出力形式ファイルを確認します。デフォルトポートで-sTスキャンを再実行し、主要な全形式（-oAでNormal、Greppable、XML出力を指定）で結果を保存します。結果を/tmpディレクトリに保存し、ファイル名にはスキャン種別とターゲットIPアドレスを示す「scan」を接頭辞として付けます。さらに-F（上位100ポートのみスキャン）と-T4（タイムアウト短縮と並列スキャン）でスキャンを高速化します。



```
sudo nmap -n -sT 10.130.10.0/24 -oA /tmp/scan -F -T4
```



```
sec560@560vm:~$ sudo nmap -n -sT 10.130.10.0/24 -oA /tmp/scan -F -T4
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-08-14 18:13 UTC
Nmap scan report for 10.130.10.4
Host is up (0.081s latency).
Not shown: 93 filtered tcp ports (no-response)
PORT     STATE SERVICE
53/tcp   open  domain
88/tcp   open  kerberos-sec
135/tcp  open  msrpc
139/tcp  open  netbios-ssn
389/tcp  open  ldap
445/tcp  open  microsoft-ds
3389/tcp open  ms-wbt-server

[...trimmed for brevity...]

Nmap scan report for 10.130.10.45
Host is up (0.079s latency).
Not shown: 99 filtered tcp ports (no-response)
PORT     STATE SERVICE
3389/tcp open  ms-wbt-server

Nmap done: 256 IP addresses (13 hosts up) scanned in 10.07 seconds

Then get a list of the files associated with the scan inside of /tmp.
```



```
ls -l /tmp/scan*
```



```
sec560@560vm:~$ ls -l /tmp/scan*
-rw-r--r-- 1 root root  2627 Aug 14 18:13 /tmp/scan.gnmap
-rw-r--r-- 1 root root  3065 Aug 14 18:13 /tmp/scan.nmap
-rw-r--r-- 1 root root 19786 Aug 14 18:13 /tmp/scan.xml
```

同じ基本名で拡張子が異なる3つのファイルを確認してください：

    .gnmap 拡張子の検索可能な形式
    .nmap 拡張子の通常形式
    .xml 拡張子のXML形式

less コマンドで .nmap ファイルを確認してください（q を押すと終了します）：
Command

```
less /tmp/scan.nmap
```

スキャン中に画面に表示される情報と同じ内容がファイルに含まれていることがわかります。

次に、.xml出力ファイルを確認しましょう（終了するにはqを押してください）：



```
less /tmp/scan.xml
```

このファイルはXML形式であり、人間が直接読むには非常に読みにくいです。ただし、この形式はGoWitness（このコースの後半で取り上げます）を含む他のツールで読み取ることができます。

最後に、catコマンドを使って.gnmapの内容を表示してみましょう。


```
cat /tmp/scan.gnmap
```


```
sec560@560vm:~$ cat /tmp/scan.gnmap
# Nmap 7.94SVN scan initiated Wed Aug 14 18:13:03 2024 as: nmap -n -sT -oA /tmp/scan -F -T4 10.130.10.0/24
Host: 10.130.10.4 ()    Status: Up
Host: 10.130.10.4 ()    Ports: 53/open/tcp//domain///, 88/open/tcp//kerberos-sec///, 135/open/tcp//msrpc///, 139/open/tcp//netbios-ssn///, 389/open/tcp//ldap///, 445/open/tcp//microsoft-ds///, 3389/open/tcp//ms-wbt-server///    Ignored State: filtered (93)
Host: 10.130.10.5 ()    Status: Up
Host: 10.130.10.5 ()    Ports: 80/open/tcp//http///, 445/open/tcp//microsoft-ds///, 3389/open/tcp//ms-wbt-server/// Ignored State: filtered (97)
Host: 10.130.10.6 ()    Status: Up
Host: 10.130.10.6 ()    Ports: 80/open/tcp//http///, 3389/open/tcp//ms-wbt-server///    Ignored State: filtered (98)
...trimmed for brevity...
```

特定のホストに関する全結果は1行にまとめられ、開いているポートと関連サービスが識別されます。この形式はgrepによる検索が容易です。さっそく実行してみましょう！

### 4: Finding Hosts by Open Port

LDAPで使用されるポートであるポート389でリスニングしているすべてのシステムを見つけましょう。



```
grep 389/open /tmp/scan.gnmap
```



```
sec560@560vm:~$ grep 389/open /tmp/scan.gnmap
Host: 10.130.10.4 ()    Ports: 53/open/tcp//domain///, 88/open/tcp//kerberos-sec///, 135/open/tcp//msrpc///, 139/open/tcp//netbios-ssn///, 389/open/tcp//ldap///, 445/open/tcp//microsoft-ds///, 3389/open/tcp//ms-wbt-server///    Ignored State: filtered (93)
Host: 10.130.10.5 ()    Ports: 80/open/tcp//http///, 445/open/tcp//microsoft-ds///, 3389/open/tcp//ms-wbt-server/// Ignored State: filtered (97)
Host: 10.130.10.6 ()    Ports: 80/open/tcp//http///, 3389/open/tcp//ms-wbt-server///    Ignored State: filtered (98)
Host: 10.130.10.7 ()    Ports: 135/open/tcp//msrpc///, 445/open/tcp//microsoft-ds///, 3389/open/tcp//ms-wbt-server///   Ignored State: filtered (97)
Host: 10.130.10.21 ()   Ports: 135/open/tcp//msrpc///, 139/open/tcp//netbios-ssn///, 445/open/tcp//microsoft-ds///, 3389/open/tcp//ms-wbt-server/// Ignored State: filtered (96)
Host: 10.130.10.23 ()   Ports: 135/open/tcp//msrpc///, 445/open/tcp//microsoft-ds///, 3389/open/tcp//ms-wbt-server///   Ignored State: filtered (97)
...truncated for brevity...
```

よく見ると、.4システムはポート389でリスニングしているだけなのに、このリストには他のホストも表示されていることがわかります。これは389が3389の一部だからです。正確に389を検索したい場合は、別のコマンドを使用する必要があります。ポート番号の前にスペースがある点に注意してください。grepの-wオプションを使用すると、「一致する語が完全な単語を形成する行のみを選択する」（man grepの出力に記載）ことができます。



```
grep -w 389/open /tmp/scan.gnmap
```



```
sec560@560vm:~$ grep -w 389/open /tmp/scan.gnmap
Host: 10.130.10.4 ()    Ports: 53/open/tcp//domain///, 88/open/tcp//kerberos-sec///, 135/open/tcp//msrpc///, 139/open/tcp//netbios-ssn///, 389/open/tcp//ldap///, 445/open/tcp//microsoft-ds///, 3389/open/tcp//ms-wbt-server///    Ignored State: filtered (93)
```

では、wc -l（小文字のL）を使用して、ポート445でリスニングしているホストの数を確認しましょう。

wc -l コマンドを使用すると、ポート445が開いているホストの数を取得できます。


```
grep -w 445/open /tmp/scan.gnmap | wc -l
```



```
sec560@560vm:~$ grep -w 445/open /tmp/scan.gnmap | wc -l
7
```

wcコマンドは「単語数」を取得するために使用され、-l（小文字のL）オプションは行数を返すよう指示します。ポート445でリスニングしているシステムが7つあることに注意してください。

ポート445でリスニングしているシステムのリストが必要な場合、cutコマンドでIPアドレスを抽出できます。区切り文字は-dオプションで指定し、スペース ' ' を区切り文字として使用します。IPアドレスは2番目のフィールドであり、-f 2 で選択できます。



```
grep -w 445/open /tmp/scan.gnmap | cut -d ' ' -f 2
```



```
sec560@560vm:~$ grep -w 445/open /tmp/scan.gnmap | cut -d ' ' -f 2
10.130.10.4
10.130.10.5
10.130.10.7
10.130.10.21
10.130.10.23
10.130.10.25
10.130.10.44
```

### 5: The nmap-services File


次に、nmap-servicesファイル（Nmapがスキャンする頻度の高いポートのリストを取得するファイル）内のポートを確認します。以下のコマンドを実行します：




```
less /usr/share/nmap/nmap-services
```

このファイルの形式には、サービス名（例：ftp）、関連するポートとプロトコル（例：21/tcp）、Fyodorによる広範なインターネットスキャン調査中に特定ポートが検出された相対頻度、およびオプションのコメントが含まれます。ポート自体は通常TCPまたはUDPですが、一部はRFC 4960で定義された代替レイヤ4プロトコルであるストリーム制御伝送プロトコル（SCTP）に関連付けられていることに注意してください。

### 6: UDP Scanning

NmapによるTCPポートスキャンを確認したところで、次はUDPポートスキャンを試してみましょう。先述したように、LinuxカーネルはICMPポート到達不能応答を1秒に1回のみ送信するよう制御しています。10.130.10.10がLinuxマシンであるため、この1秒に1パケットという挙動が確認できるはずです。tcpdumpスニッファを実行し続け、10.130.10.10ホストとの間で送受信されるパケットを表示させておいてください。

次に、-sUオプションを使用して10.130.10.10のUDPポートスキャンを実行するため、Nmapを起動します。


```
sudo nmap -n -sU 10.130.10.10
```

スニッファの出力には、複数のUDPパケットと、定期的に送信されるICMPポート到達不能応答が表示されるでしょう。

Nmapウィンドウでスペースキーを押すとステータスレポートが表示されます。システム速度やネットワーク速度にもよりますが、スキャンがわずかな割合しか完了していないことが確認できるはずです。

出力は以下のような形式になります：



Press the spacebar to see the status.

```
sec560@560vm:~$ sudo nmap -n -sU 10.130.10.10
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-08-14 18:18 UTC
Stats: 0:00:12 elapsed; 0 hosts completed (1 up), 1 undergoing UDP Scan
UDP Scan Timing: About 2.57% done; ETC: 18:27 (0:08:13 remaining)
```

遅い！

このスキャンは非常に遅いです。スキャンが完了する前に、Nmapを停止するにはCtrl+Cを押してください。

### 7: Targeted UDP Scan

対象に対してNmap UDPスキャンを再実行し、今回はより狭いポート範囲（53、111、414、500-501）に焦点を当てます。対象は.4および.10のホストです。



```
sudo nmap -n -sU 10.130.10.4,10 -p 53,111,414,500-501
```


```
sec560@560vm:~$ sudo nmap -n -sU 10.130.10.4,10 -p 53,111,414,500-501
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-08-14 18:19 UTC
Nmap scan report for 10.130.10.4
Host is up (0.085s latency).

PORT    STATE         SERVICE
53/udp  open          domain
111/udp open|filtered rpcbind
414/udp open|filtered infoseek
500/udp open|filtered isakmp
501/udp open|filtered stmf

Nmap scan report for 10.130.10.10
Host is up (0.077s latency).

PORT    STATE  SERVICE
53/udp  closed domain
111/udp closed rpcbind
414/udp closed infoseek
500/udp closed isakmp
501/udp closed stmf

Nmap done: 2 IP addresses (2 hosts up) scanned in 3.94 seconds
```

.4（Windows ドメイン コントローラー）ホストでは、ポートが open|filtered と表示されることに注意してください。

.10（Linux）ホストでは、すべてのポートが closed です。一見すると混乱する可能性があります。このため、開いているポートのみを表示するために --open オプションを使用することがよくあります。

--openオプションを使用してコマンドを再実行すると、Nmapが10.130.10.10:10でリスニング状態のUDPポートを一切検出できなかったことがより明確になります：



```
sudo nmap -n -sU 10.130.10.4,10 -p 53,111,414,500-501 --open
```



```
sec560@560vm:~$ sudo nmap -n -sU 10.130.10.4,10 -p 53,111,414,500-501 --open
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-08-14 18:19 UTC
Nmap scan report for 10.130.10.4
Host is up (0.078s latency).

PORT    STATE         SERVICE
53/udp  open|filtered domain
111/udp open|filtered rpcbind
414/udp open|filtered infoseek
500/udp open|filtered isakmp
501/udp open|filtered stmf

Nmap done: 2 IP addresses (2 hosts up) scanned in 1.98 seconds
```

### 結論

本実習では、Nmapスキャンが対象環境を走査して潜在的なターゲットシステムを特定する方法を学びました。また、TCPおよびUDPスキャンの各種オプションに加え、非常に有用な--open Nmapコマンドラインオプションについても探求しました。
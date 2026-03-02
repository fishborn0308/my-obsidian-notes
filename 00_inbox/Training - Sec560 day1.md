# Miniature Engagement, Recon, and Scanning

## Miniature Engagement

### The Mindset of Penetration Testing

![[スクリーンショット 2026-03-02 9.24.13.png]]

- ハッカーのように考える
	- 攻撃者の視点
	- 現実的であること
	- 有望な手がかりを追う
- 防御側の改善を支援する
	- 再現可能なプロセス
	- 進行中の記録
	- 本番環境へのリスク最小化

この二つのバランスが成功の鍵となる

本講義の冒頭で、ペネトレーションテスターと倫理的ハッカーの思考様式を簡潔に考察しよう。

我々の仕事とは、攻撃者が標的マシンで悪事を働くことを可能にする欠陥を発見し、組織がビジネスリスクをより深く理解し、混乱が生じる前に脆弱性を解消できるようにすることである。しかし、この目標を達成するためには、ペネトレーションテスターと倫理的ハッカーは、しばしば矛盾するように聞こえる二つの概念を含む思考様式を維持しなければならない。

第一に、ペネトレーションテスターや倫理的ハッカーは柔軟かつ現実的で、枠にとらわれない思考が求められます。成功するためには、従来のシステム管理者やネットワーク設計者とは異なる視点で、しばしば非伝統的な方法で問題解決を試みる必要があります。

しかし同時に、現実的な手法を用いる際には、徹底的かつ系統的で慎重であることが不可欠です。価値ある仕事とは、対象組織が自らの脆弱性とリスクを理解し、欠陥を軽減するための措置を講じられるよう、理解可能かつ再現性のあるものでなければなりません。優れた記録を取り、ペネトレーションテストや倫理的ハッキングを専門的に行わない人々——つまり、あなたの現実的で「異なる考え方」の姿勢を共有しないかもしれない人々——にも理解しやすい形で調査結果を提示する、高品質な報告書を作成する必要があります。

この思考様式に苦戦し、一方に偏る過ちを犯す者もいる。しかし多くの人は両思考の葛藤を解決し、バランスを取れる。成功するペネトレーションテスターとなるには、この均衡を追求すべきである。

### Vulnerability, Exploit, Threat, Risk

![[スクリーンショット 2026-03-02 9.23.58.png]]

#### Vulnerability 

ペネトレーションテスターの目的はセキュリティ脆弱性を悪用することですが、脆弱性とは何でしょうか？RFC 4949では次のように定義されています：「システムの設計、実装、運用、管理における欠陥または弱点であり、システムのセキュリティポリシーを侵害するために悪用される可能性があるもの」。簡単に言えば、脅威アクターによって悪用可能な欠陥や弱点と定義できます。多くの人は脆弱性をメモリ破損の欠陥（例：バッファオーバーフロー）と関連付けますが、それは全セキュリティ脆弱性の一部に過ぎません。脆弱性には以下が含まれますが、これらに限定されません：
- 設定ミス
- 設計およびアーキテクチャ上の欠陥
- 監査機能の欠如
- 脆弱な/推測可能なパスワード
- デフォルト認証情報 

上記以外にも多くの脆弱性カテゴリが存在します。MITREはハードウェア・ソフトウェアの弱点タイプを列挙した「Common Weaknesses Enumeration」を作成しました。これは特定のソフトウェアの具体的な欠陥を列挙する「CVE（Common Vulnerabilities and Exposures）」とは混同しないでください。

#### Exploit 

エクスプロイトとは、脆弱性を悪用するコードや手法のことです。以下にいくつかの例を示します：

- メモリ管理の脆弱性を悪用し、攻撃者がシステムにリモートアクセスできるようにする
- 既存の機能を利用してコードをアップロード・実行する（例：ユーザーが履歴書やアバターをアップロードできる機能を利用して、WebサーバーにWebシェルをアップロードする） 
- 攻撃者が自身の名前をJavaScriptコードに変更し、ウェブサイトの脆弱な入力フィルタリングを悪用する
- システム上で特権操作を実行する実行可能ファイルを実行し、アクセス権を昇格させる SEC560 | エンタープライズ侵入テスト 脆弱性、エクスプロイト、脅威、リスク 脅威アクターに悪用される可能性のある欠陥や弱点 例： バッファオーバーフロー、設定ミス、設計上の欠陥脆弱性脆弱性を悪用するコードまたは手法例：公開エクスプロイトコード、サーバーへのWebシェルアップロードエクスプロイト危害をもたらす可能性のある主体または行為者例：人間の攻撃者、ワーム、リンクをクリックするユーザー脅威損失または損害の可能性通常は以下のように算出：リスク = 発生確率 × 影響度リスク 9 10エクスプロイトが脆弱性を悪用する方法は多岐にわたる。実際、同じ脆弱性でも脅威主体によって最終目的が異なるため、異なる方法で悪用される可能性があります。本コースの後半でエクスプロイトとエクスプロイト分類について詳しく説明します。

#### Threat

脅威とは、危害を加える可能性のある主体または行為者を指す。より広くは、危害を引き起こし得るあらゆるものを意味する。脅威は、クレジットカード番号を盗もうとする組織犯罪者である場合もあれば、誤って間違ったボタンをクリックしたユーザーである場合もある。脅威は人間である必要すらない。ワームは脅威である。なぜなら、コード自体が拡散し損害をもたらすからだ。同様に、自社のセキュリティツールも特定の状況下では脅威と見なされる可能性がある。Webアプリケーション脆弱性スキャナはウェブサイトを巡回しデータを送信する。本番環境のウェブサイトと本番データを対象にスキャナが「削除ボタンをクリック」した場合、何が起こるだろうか？スキャナは機微な操作を回避しようとするが、こうした事態は実際に発生する。これがバックアップとテスト環境の重要性を示す一例である。

#### Risk

リスクとは、損失や損害が発生する可能性と定義される。リスクは通常、（発生確率）×（潜在的影響）として算出される。潜在的影響は合理的な確度で算出可能な数値であるが、発生確率ははるかに議論の余地が大きい数値である。多くの場合、発生確率（ひいてはリスク値そのもの）はセキュリティチームの専門家の見解に帰着する。チームは集合的な知識と経験を用いてリスクを判断し、最終的に事業部門へ指針を提供する。リスクと組織的ニーズのバランスを取ることは、セキュリティチームの基本的な目標の一つである。残念ながら、多くのセキュリティチームは障害物と見なされ、組織が安全に発展・進歩することを妨げている。

ペネトレーションテスターとしての役割の一部は、発見した脆弱性に関連するリスクを見積もることです。これは強力かつ重要な役割です。経験の浅いテスターは脆弱性のリスクを過大評価しがちです。彼らの報告書はリスク評価が偏りがちで、大半のリスクが「高」または「重大」に分類されます。

ペネトレーションテスターが経験を積むにつれ、リスクの本質と組織プロセスへの影響を理解するようになります。リスクを理解し適切に特定することは、組織が修正作業の優先順位付けを行う上で役立ちます。

リスクを脅威・脆弱性・リスクの交点として捉えることで単純化できます。いずれか一つを減らすか排除できれば、リスクも同様に低減または解消されます。システムへのアクセスを制限すれば、脅威がサービスに到達するのを防げます。同様に、システムから機密データ（個人識別情報/PII、パスワード等）を排除すれば、攻撃者に価値を提供せず、データの紛失・窃取・破壊も発生しないため、リスクは存在しなくなる。

フィルタリングツールを導入してエクスプロイトの実行を阻止できる。例えばWAF（Webアプリケーションファイアウォール）で脆弱性を引き起こす文字列をフィルタリングする手法がある。同様に、侵入防止システム（IPS）は、標的システムに対する特定の攻撃行動（エクスプロイトを誘発する行為）を阻止できます。いずれの場合も脆弱性は存在しますが、悪用されることはありません。注：攻撃者はこれらのツールを回避するため、シグネチャやエンコーディングを変更することが多いため、この手法には注意が必要です。

最終的に、脆弱性を修正することでリスクを排除できます。

### Pen Test, Red Team, Purple Team, Vuln Assessment, Audit

![[スクリーンショット 2026-03-02 9.29.16.png]]

#### Penetration Testing

侵入テストは、攻撃者がコンピュータやネットワークに侵入し、データを窃取・損傷・改ざんする可能性のある標的の脆弱性を特定することに焦点を当てます。侵入テスター（ペネトレーションテスター）は、悪意のある攻撃者と類似したツールを使用します。時には、悪意のある攻撃者が侵入テスターのツールや技術を流用することさえあります。名称が示す通り、侵入テストには（可能な限り）侵入が求められます。侵入を禁じるテストはペネトレーションテストとは言えません。対象組織がペネトレーションテストを依頼しながら、脆弱性の検証や二次的脆弱性の発見を可能にするエクスプロイト、パスワード推測などの活動を禁止する場合があります。こうした制約付きのテストは、むしろ脆弱性評価に近い性質を持ちます。

#### Red Team

レッドチームは、セキュリティプログラム全体の有効性をテストすることに焦点を当てています。彼らは現実世界の攻撃者の戦術、技術、手順（TTPs）を模倣し、防御、検知、対応における弱点を測定することを目的としています。レッドチームはペネトレーションテスターと多くのツールを共有しますが、焦点は異なります。レッドチームはブルーチーム（防御側）の脅威検知・対応・排除能力における欠陥の特定に注力します。レッドチームは目標達成に役立つ脆弱性のみを利用するのに対し、ペネトレーションテスターは多くの脆弱性発見を主な任務とします。

ペネトレーションテストは欠陥の発見、ビジネスリスクの理解、組織のセキュリティ態勢改善支援に焦点を当てます。レッドチームの主な役割は、ブルーチームが攻撃を検知・対応する能力を向上させることです。端的に言えば、ペネトレーションテストは防御手段（技術）に、レッドチームは防御者（人材）に焦点を当てるのです。

#### Purple Team 

パープルチームは、レッドチームとブルーチームが協力するクロスファンクショナルチームです。連携が密であるほど効果的です。この評価手法では、攻撃者がビジネスに損害を与える可能性のある欠陥を特定するブルーチームの能力（予防的統制のテスト）を測定します。ペネトレーションテスト（レッドチーム）検知・対応能力のテストを目的とするレッドチームとブルーチームのクロスファンクショナルチーム、および／または攻撃側と防御側のフィードバックループ強化パープルチーム脆弱性の特定・定量化・ランク付け（悪用なし）脆弱性評価セキュリティ監査監査は特定の基準セットに対するテストを意味する攻撃への対応能力の向上を示すため、テストは文書化されるべきである。これらの指標はツールやトレーニングへの投資を正当化するのに使用できる。

#### Vulnerability Assessment

多くの人々は、ペネトレーションテスターや倫理的ハッカーが行う作業を説明するために「脆弱性評価」や「セキュリティ評価」という表現を使用しますが、ペネトレーションテストとセキュリティ評価の間には微妙な違いがあります。

ペネトレーションテストは、侵入またはデータ窃取に焦点を当てています。発見された脆弱性を悪用して対象環境へ侵入することに重点が置かれます。

脆弱性評価やセキュリティ評価は、実際に悪用して侵入することには関心を向けず、脆弱性の発見に焦点を当てます。

したがって、システム掌握やデータ窃取を目的とするペネトレーションテストはより深く掘り下げられる一方、セキュリティ評価や脆弱性評価はセキュリティ上の欠陥を探すプロセスを含む広範なものです。これらの評価には、通常ペネトレーションテストに含まれないポリシーや手順のレビューも含まれることがよくあります。

前のスライドの表は、各種評価の違いを示しています。もちろん、それぞれにある程度の差異があり、ペネトレーションテストが最も変動性が高いです。ペネトレーションテスターは、悪用不可能な脆弱性も含め、全ての脆弱性を見つけることを求められることがよくあります。

#### Security Audit 

最後に、セキュリティ監査という表現があります。監査とは、あらかじめ定められた厳格な基準に対して測定を行うことを意味します。こうした監査は、ほぼ例外なく詳細なチェックリストを用いて実施されます。

一部のペネトレーションテストや倫理的ハッキング組織では、テストでカバーすべき項目をまとめた独自の内部チェックリストを作成していますが、これらのチェックリストは包括的な監査用ほど詳細ではありません。

本講座の焦点は監査ではありません。SANSにはセキュリティ監査を詳細に扱う他の講座が多数あります。本講座の焦点は倫理的ハッキングとペネトレーションテストにあります。

#### Penetration Testing Goals

ペネトレーションテストは、現実的な攻撃者によるビジネスリスクを実証するものです：
- 現実世界の攻撃者を模倣する
- 脆弱性を発見する（理想的には攻撃者に先んじて）
- 制御された環境下でそれらの欠陥を悪用する
- ビジネスリスクを判定する
- 組織のセキュリティを向上させる

ペネトレーションテストの正式な定義は以下の通りです：

>ペネトレーションテストとは、現実世界のコンピュータ攻撃者が使用する手法を模倣して脆弱性を発見し、管理された環境下で、慎重に設計された範囲と行動規範に従い、専門的かつ安全な方法でそれらの欠陥を悪用し、ビジネスリスクと潜在的な影響を判断するプロセスである。その目的は、組織のセキュリティ慣行の改善を支援することにある。

重要なのは、脆弱性の特定と悪用が常にビジネスに焦点を当てるべきだということである。ペネトレーションテストの結果は、技術的リスクだけでなく、組織的リスクに常に焦点を当てるべきである。

多くの組織は、敵対者に先んじてセキュリティ上の欠陥を見つけるために、倫理的ハッキングとペネトレーションテストを利用している。セキュリティポリシー、手順、技術を適用した後、組織は徹底的なペネトレーションテストを実施することで、実際の攻撃（友好的な攻撃者によるものとはいえ）に照らして自社のセキュリティがどれほど効果的かを確認できる。

倫理的ハッキングとペネトレーションテストの追加的な利点は、実際の脆弱性を可視化し、悪意ある攻撃者が達成し得る可能性を示すことで、経営陣の注意を喚起できる点にある。意思決定者は、ビジネス用語で慎重に構成されたテスト結果を提示されると、組織のセキュリティ態勢強化に向けたリソースと関与を提供する可能性が高まる。

### Types of Penetration Tests

- Network services test
	- One of the most common 
- Assumed breach test 
- Web application test 
- Social engineering test 
- Email-based or phone-based 
- Wireless security test 
	- Not just Wi-Fi 
- Physical security test 
- Product security test
	- Could be software package or hardware (e.g., IoT) 
	- Breaking or bypassing encryption on local data or intercepted traffic
- Enterprise penetration test
	- Pen testing the whole company 
	- If it can be in scope, it's in scope

There are numerous kinds of penetration tests:

- **Network services test:** One of the most common types of tests, it involves finding target systems on the network, looking for openings in their underlying operating systems and available network services, and then exploiting them remotely. Some of these network service tests happen remotely across the internet, targeting the organization's perimeter networks. Others are launched locally from the target's own facilities to evaluate the security of the internal network, seeing what kinds of vulnerabilities an internal user could discover.
- **Assumed breach test:** Designed to find vulnerabilities in the network once an attacker has gained access to a system in the network. This is a fantastic way to find issues in Active Directory permissions, file permissions (excessive sharing on file shares), and client-side software. 
- **Web application test:** These tests look for security vulnerabilities in web-based applications and APIs. 
- **Social engineering test:** Involves attempting to trick a user into revealing sensitive information, such as a password, or possibly convincing a user to click a link in an email. These tests are often conducted via email or over the phone, targeting selected users and evaluating processes, procedures, and user awareness. 
- **Wireless security test:** These tests involve exploring a target's physical environment to find unauthorized wireless access points or authorized wireless access points with security weaknesses. 
- **Physical security test:** This test looks for flaws in the physical security practices of a target organization. Testers might attempt to gain access to buildings and rooms or to take laptops, desktops, or recycling bins out of target facilities. A dumpster diving test is a variation of a physical security analysis. Physical testing must be conducted carefully to ensure that the testers do not get hurt or arrested during their work. 
- **Product security test:** This test focuses on one specific product. It could be hardware or software. In this kind of test, you look for security flaws in products that can be used in the tester's laboratory systems. Such tests look for flaws in the software, such as exploitable buffer overflow conditions, SEC560 | Enterprise Penetration Testing Types of Penetration Tests • Network services test − One of the most common Assumed breach test • Web application test • Social engineering test − Email-based or phone-based • Wireless security test − Not just Wi-Fi • Physical security test • Product security test − Could be software package or hardware (e.g., IoT) − Breaking or bypassing encryption on local data or intercepted traffic • Enterprise penetration test − Pen testing the whole company − If it can be in scope, it's in scope 14 15 privilege escalation flaws, and the exposure of unencrypted sensitive data. These tests could focus on bypassing or breaking the encryption of data stored on a local system or across the network. 
- **Enterprise penetration test:** This test has as its scope, to a first approximation, everything that the company has the authority to grant the penetration tester to test. Some exclusions may still apply, but the tester should emphasize that removing systems from the scope of the enterprise penetration test reduces the fidelity of the results.

### Attack Phases

![[スクリーンショット 2026-03-02 9.51.35.png]]

Both malicious attackers and professional penetration testers/ethical hackers apply various phases in their attacks. Attacks are often separated into these phases:

- Reconnaissance is the process of investigating the target organization to gather information about it from publicly available sources, such as domain registration services, websites, and so on. Some people include techniques such as social engineering and dumpster diving in the recon phase. 
- Scanning is the process of finding openings in the target organization, such as internet gateways, available systems, listening ports, and vulnerability lists. 
- In the Exploitation phase, attackers exploit target systems to compromise them, possibly getting control of them or causing a denial-of-service attack. 
- Post-exploitation is what happens after the initial compromise. Both penetration testers and malicious attackers use their access to pivot and move throughout the target environment.

Although legitimate tests often include the previously listed phases, malicious attackers often go further than the Rules of Engagement allow for a professional penetration test. The next phase, often used by a malicious attacker to maintain access to and control of a target machine, involves setting up the compromised machine so that the attacker can keep control over it, with techniques such as installing backdoors and planting rootkits. Malicious attackers also often use a final phase, Covering the Tracks, in which they employ log editing, file hiding, and covert channels to hide their activities on a system. 

Please note that the best of the attackers (both the good guys and the evil ones) are pragmatists. They don't always proceed from reconnaissance to scanning to gaining access and so on. Sure, they use these steps, but they are likely to jump around among them as events and discoveries warrant. For example, during the recon phase, attackers may discover an exploitable flaw that they will use to gain access directly, temporarily bypassing scanning. Then, after they gain access to one machine, they may go back and start scanning. 

From a professional testing perspective, though, be careful when jumping out of order among these steps, making sure that you return to the earlier phases to conduct a comprehensive test.

### Recent Enterprise Breaches: Learning from Reality

- Change Healthcare 
	- Root cause: Missing MFA on public-facing Citrix server 
	- Impact: $2+ billion in damages with nation-wide disruption 
	- Lesson: Basic controls prevent catastrophic breaches 
- Snowflake 
	- Method: Infostealer → stolen credentials → 160+ victims 
	- Victims: Ticketmaster (560M records), AT&T, Santander Bank 
	- Lesson: Risks in your supply chain and third parties = risks to your enterprise
- Ransomware Evolution 
	- RansomHub: 434 victims in first year 
	- Healthcare: 92% of organizations targeted in last 12 months 
	- Average payment: $2.73 million (double/triple extortion now standard) 
	- Lesson: Ransomware remains #1 threat 
- Living Off The Land (LOL) 
	- Many incidents use only legitimate tools (PowerShell, WMI, CertUtil, rundll32) 
	- Even when using C2, attackers often "shell out" to native binaries such as whoami, nltest

### Lockheed Martin Cyber Kill Chain

- Pros:
	- Industry recognition 
	- Focus on attacker's actions and viewpoint 
	- Breaking the chain breaks the attack 
- Cons: 
	- For most orgs, they see nothing until steps 3 and 4

**Pros of the Cyber Kill Chain**
Industry Recognition: The Cyber Kill Chain model is well-regarded across the cybersecurity industry, known for its effectiveness in organizing the stages of cyber threats. 

Focus on Attacker's Actions: The model is structured around the actions of an attacker, allowing defenders to anticipate and counteract each stage of an attack effectively. 

Preventative Strategy: By identifying and disrupting any stage of the kill chain, it is possible to stop the attack process altogether. This "break the chain, break the attack" approach underlines the potential to mitigate threats before they reach their objectives.

**Cons of the Cyber Kill Chain** 
Visibility and Detection Challenges: One significant drawback of the model is that many organizations do 

the model). This late detection makes proactive defense challenging and often leaves organizations reactive to threats rather than preemptive.

### MITRE ATT&CK

- MITRE ATT&CK framework is a knowledge base of attack tactics based on realworld attackers
- Techniques are grouped together into tactics
	- Phishing and Valid Accounts techniques are part of the Initial Access tactic
	- Techniques have multiple procedures (PsExec.exe vs Metasploit's psexec)
- The framework includes Groups, which track sets of related activity to a common adversary group (such as APT1, FIN7, etc.)
- Great resource for learning specific tools and techniques as well as understanding the playbook of real-world attackers

MITRE ATT&CK (pronounced "attack") is a knowledge base of attack tactics based on real-world attackers. The framework includes many techniques (and sub-techniques) used by good and bad attackers. It is a valuable resource for learning new tools and techniques. The techniques are grouped together into "tactics." For example, the "Initial Access" tactic includes the techniques Exploit Public Facing Application, Phishing, and Valid Accounts, among others. 

The tactics and techniques are pieces used by an attacker. ATT&CK also includes "Groups," which is a set of related activities tracked to a common adversary group (e.g., APT1, FIN7). These provide the story of various breaches, which we can use to learn when and how to use specific attacks. 

The combination of the Groups and the Tactics/Techniques make ATT&CK a valuable resource to develop our own offensive skills. 

MITRE ATT&CK Matrix for Enterprise can be found at https://attack.mitre.org/.

### Attackers Want Your Data

- Breach: Unauthorized access to protected information
	- Some attacks begin and end in one step:
		- Open Amazon S3 buckets
		- Public-facing exploitable systems such as 2025's Microsoft SharePoint, Citrix NetScaler, Palo Alto GlobalProtect, Ivanti VPN, and SonicWall
		- Unprotected databases such as MongoDB
	- If the entire breach is a wget command, you have no time for detection and response!

Open Amazon S3 Buckets: Misconfigured S3 buckets are a common error that can lead to massive data leaks. These buckets, if not properly secured, can be accessed without authorization using simple web requests. 

Vulnerabilities in File Transfer Software: Tools like MOVEit and GoAnywhere, designed for secure file transfer, have had vulnerabilities discovered in recent years that could allow attackers to exploit them and gain unauthorized access. 

Unprotected Databases: Databases such as MongoDB that are left unprotected on the internet can be accessed and downloaded without needing sophisticated hacking techniques. 

Consequences of Quick Breaches: An entire data breach could be conducted with a single wget command, leaving virtually no time for detection and response. This goes to show the importance of proactive security measures and defense in depth. 

There are no walls so high, nor so strong, that they cannot be breached. We need time for our detection and response capabilities.

### Let's Keep Things Simple

![[スクリーンショット 2026-03-02 10.11.42.png]]

Here, we aim to simplify your understanding of the typical progression of an attack, breaking it down into three critical phases that reflect the attacker's pathway from initial entry to achieving their ultimate objectives. 

Attackers want your data. Since that data is often inside your environment, attackers need inside. Since that data requires privileges that not everyone has access to, attackers often need privileges as well.

**Initial Access** 
The first step for an attacker is gaining access to the internal network or systems. This can be accomplished through various methods, such as phishing, exploiting vulnerabilities, or using stolen credentials.

**Privileges** 
Once inside, attackers often seek to escalate their privileges to gain higher levels of access. This can involve exploiting system weaknesses, configuration errors, or using social engineering tactics to gain the necessary permissions to access more sensitive areas of the network.

**Actions on Objective**
The final phase involves the attackers executing their intended actions, which could be data theft, deploying malware, or conducting other malicious activities designed to fulfill their initial objectives. This phase represents the culmination of the attack process and is ultimately what the attackers aim to achieve through their efforts.

### Initial Access Eventually Becomes a Breach

![[スクリーンショット 2026-03-02 10.15.55.png]]

- Attackers inside your perimeter will eventually cause harm 
	- Much like a trespasser inside your building or an unchecked illness
- Once attackers have gained Initial Access, it starts a race: 
	- Attackers race to gain necessary Privileges for their Actions on Objective 
	- Defenders race to detect and respond (eradicating attackers' presence)
- Whoever wins the race wins the incident

This slide highlights the inevitability of harm once attackers breach your perimeter, comparing it to a trespasser in a building or an unchecked illness. Once inside, the attacker has started a race: 

Attackers race to escalate privileges and achieve their objectives, such as data theft or system disruption. 

Defenders, starting only when they detect the attacker's presence, must quickly respond to eradicate the attackers and prevent damage. 

The race begins only when defenders detect the breach, emphasizing the importance of detective controls for early detection. Whoever wins this race—attackers or defenders—determines the incident's outcome. Rapid detection and response are crucial to maintaining security.

### Preventing Breaches (1)

To prevent successful breaches, defenders need to both detect and respond to attackers who gain initial access, before attackers accomplish their goal.

![[スクリーンショット 2026-03-02 10.17.06.png]]

This slide emphasizes the need for defenders to quickly detect and respond to attackers to prevent successful breaches. It highlights the following points:

- Shorter Detection and Response Timeline: Defenders must rapidly detect and respond to threats to minimize potential damage. 
- Longer Privilege Escalation and Actions on Objective Timeline: Slowing down attackers' progress provides defenders more time to act.

The combination of quick detection and response with delaying attackers' actions leads to preventing breaches, illustrating the importance of efficient monitoring and swift defensive actions.

### Preventing Breaches (2)

To prevent successful breaches, defenders need to both detect and respond to attackers who gain initial access, before attackers accomplish their goal.

![[スクリーンショット 2026-03-02 10.18.24.png]]

This slide highlights the consequences of delayed detection and response in preventing breaches. It illustrates the following points:

- Longer Detection and Response Timeline: If defenders take too long to detect and respond to threats, it increases the likelihood of a successful breach. This is all too common due to failed detective controls, erroneous marking of alerts as false positives, shift changes or outsourced SOC offering differing capabilities, etc. 
- Shorter Privilege Escalation and Actions on Objective Timeline: Attackers quickly escalate privileges and achieve their objectives if not promptly countered.

Imagine an attacker gaining access via credential stuffing and VPN (or RDP), followed by Kerberoasting for privileges, followed by domain-wide ransomware. This sort of attack can happen in minutes, not hours or days.

### Defenders' Three Objectives

1. This slide outlines three key objectives for defenders to enhance their security posture: Reduce the number of ways attackers gain initial access: 
	- Consolidate remote support tools and monitor for unauthorized usage. 
	- Minimize the external attack surface by securing 1FA services and Exchange servers. 
	- Remove endpoint code execution methods like macros, AutoDDE, and Quick Assist 
2. Lower the time to detect and respond to an attacker: 
	- Focus on detecting post-exploitation activities early. 
	- Regularly test and improve response capabilities to quickly eradicate threats. 
	- Use deception technologies like Canarytokens to identify breaches early. 
3. Increase the time for an attacker to accomplish their goal: 
	- Remove easy targets for privilege escalation. 
	- Conduct penetration testing to identify and mitigate vulnerabilities. 

These objectives aim to prevent breaches by reducing entry points (so defenders have to respond less frequently), improving detection and response times (so defenders get early notifications of attacker's initial access), and making it harder for attackers to achieve their goals (to buy time for the detection and response capabilities of the defenders).

### Defenders' Objectives (1)

1. Reduce the number of ways attackers gain initial access 
2. Lower the time to detect and respond to attackers 
3. Increase the time for attackers to accomplish their goal

![[スクリーンショット 2026-03-02 10.24.01.png]]

This slide focuses on the first objective: Reduce the number of ways attackers gain initial access. 

No matter how many advantages you have, if you get in enough fights, you will lose. Seek to fight less often.

### Defenders' Objectives (2)

1. Reduce the number of ways attackers gain initial access
2. Lower the time to detect and respond to attackers
3. Increase the time for attackers to accomplish their goal

![[スクリーンショット 2026-03-02 10.26.39.png]]

This slide focuses on the second objective: Lower the time to detect and respond to attackers. 

Reducing the time it takes to detect and respond to threats is crucial for minimizing damage. The analogy of a shorter racetrack signifies the importance of speeding up detection and response capabilities. Faster detection and response can help prevent attackers from completing their objectives. 

Implement strategies like early detection of post-exploitation activities, regularly testing and improving response capabilities, and utilizing deception technologies like Canarytokens to quickly identify breaches. By doing so, defenders can stay ahead of attackers and reduce the impact of potential breaches.

### Defenders' Objectives (3)

1. Reduce the number of ways attackers gain initial access 
2. Lower the time to detect and respond to attackers 
3. Increase the time for attackers to accomplish their goal

![[スクリーンショット 2026-03-02 10.27.51.png]]

This slide focuses on the third objective: Increase the time for attackers to accomplish their goal. 

Extending the time it takes for attackers to complete their objectives gives defenders more time to detect and respond to threats. The longer racetrack symbolizes the need to make the attacker's path more complicated and time-consuming. 

Strategies include removing easy targets for privilege escalation and conducting regular penetration testing to identify and mitigate vulnerabilities. By increasing the difficulty and time required for attackers to achieve their goals, defenders can better manage and contain potential breaches, ultimately improving overall security.

### Ideal vs. Acceptable Timelines

- Defenders can't win unless they know about each intrusion! 
- Defenders' three jobs 
	- Ideal: Defenders can detect and respond (eradicating attackers' presence) before attackers have spread the infection 
	- Minimum: Defenders detect and respond before the breach 
- Incidents don't stop when attackers are detected! 
- "Shell is only the beginning": Carlos Perez's mantra for attackers 
- "Detection is only the beginning": Mantra for defenders

Ideally, defenders would detect and respond to threats quickly enough to eradicate attackers before they can spread. This requires highly effective monitoring and response capabilities but minimizes the damage done by the attacker, minimizes the amount of information attackers are able to gain before they're kicked out, and asymmetrically costs attackers more than defenders (as attackers often invest significantly into their initial access). 

Ultimately, though, defenders need to at least detect and respond before the breach occurs. Otherwise, what's the point of having human defenders? Again, no wall is so tall or so strong that it can't be breached. 

Detection is only the beginning of the response process. Continuous monitoring and action are required to fully address and eliminate threats. 

Defenders must be vigilant and proactive, knowing that successful defense depends on early detection and swift, effective response to every intrusion.

### Only Five Ways In (Initial Access)

Organizations have far more than just internet-facing services to attack:
1. Employees who can run code 
2. Remote methods of connectivity (VPN, VDI, RDP) 
3. Internal networks with DHCP
4. Third-party software 
5. Public-facing software (such as Exchange, MOVEit, JetBrains)

![[スクリーンショット 2026-03-02 10.31.26.png]]

Here, we show attackers five methods of initial access, illustrating how the attack surface of an organization is more than merely their public-facing assets.

1. Employees Who Can Run Code: Targeted primarily through phishing campaigns. Phishing aims to deceive employees into executing malicious code or revealing sensitive credentials, leveraging the access privileges of unsuspecting employees to infiltrate systems. Remember, phishing can be more than just via email! 
2. Remote Connectivity Methods (VPN, VDI, RDP): These are exploited by attackers logging on remotely. Vulnerabilities in remote access technologies or poor security practices (such as weak passwords and lack of multi-factor authentication) can allow unauthorized access to an organization's internal network. 
3. Internal Networks with DHCP: Attackers can insert rogue devices into the network. These devices can be configured to exploit DHCP functionalities to maliciously alter network configurations or intercept network traffic, compromising internal communication. 
4. Third-Party Software: Often a vector for supply chain attacks. Attackers exploit vulnerabilities in third-party software products that are integral to business operations to compromise downstream systems and data. 
5. Public-Facing Software (such as Exchange, MOVEit, JetBrains): Typically involved in "real" exploitation scenarios, where attackers directly exploit known vulnerabilities in software that is accessible from the internet, often resulting in data breaches or further network compromise.

Reference: https://malcomvetter.medium.com/how-we-breached-your-network-755e40f52d85

### Class Theme (Hiboxy Introduction)

- Hiboxy is our class's target company 
	- They make 3D-printed zippers; very hightech! 
- The CEO (Mark Cruz) wants to know what business risks he's facing
- What's the initial scope? Just Mark's card.

This class focuses on Hiboxy, a company that specializes in producing 3D-printed zippers, representing a significant advancement in textile manufacturing technology. The CEO, Mark Cruz, is concerned about the potential business risks that could impact his company. Understanding these risks is crucial for strategic planning and maintaining the competitive edge of his high-tech business.

1. Company Overview: Hiboxy stands out in the manufacturing sector with its innovative approach to producing zippers using 3D printing technology. This method likely presents unique challenges and opportunities, from production efficiency to intellectual property concerns. 
2. CEO's Concern: Mark Cruz, as the CEO, has expressed a desire to thoroughly understand the risks facing Hiboxy. His proactive approach suggests a focus on both immediate operational risks and longterm strategic vulnerabilities that could influence the company's success. 
3. . Initial Scope: The focus is initially narrowed to understanding the risks as they pertain directly to the CEO, Mark Cruz, possibly indicating a personal approach to leadership and decision-making within the company.

We also see Mark's business card, possibly obtained via a first meeting at a conference.

### Reconnaissance (in Brief)

- Reconnaissance: Exploration to find useful information for a given purpose 
	- Invest in some recon before the first scoping call! 
	- How does email flow to the client? 
		- On-premises Exchange? Microsoft 365? ProofPoint? 
	- What's the username format? Password requirements? 
	- What's the email address format? Breached credentials? 
	- Any single-factor authentication services exposed to the Internet? 
		- Especially those backed by on-prem Active Directory!

The slide emphasizes the criticality of conducting reconnaissance before the initial scoping call with a client. This initial investigation lays the groundwork for a more informed and effective engagement strategy.

1. Purpose of Reconnaissance: The aim is to gather actionable intelligence that can be used to tailor the approach to the specific environment and vulnerabilities of the client. 
2. Key Areas of Focus:
	- Email Systems: Understanding the email infrastructure, such as whether the client uses Onpremises Exchange, Microsoft 365, or ProofPoint, helps in assessing potential vectors for phishing or direct compromise. 
	- Authentication Protocols: Information on username and password formats and any recent breaches of credentials can guide the development of penetration testing tactics focused on credential stuffing or brute force attacks 
	- Authentication Security: Identifying if there are any single-factor authentication systems, particularly those tied to on-prem Active Directory, exposed to the internet. These systems can often be targeted for initial access due to their lower security compared to multi-factor systems.

This reconnaissance phase is vital for identifying the weakest links and potential attack vectors within a client's infrastructure, thereby enabling a more targeted and effective security assessment.

### Features Are Forever

![[スクリーンショット 2026-03-02 10.38.33.png]]

"Hackers don't break in. We log in." 

Cybersecurity expert Keren Elazari succinctly captures a critical aspect of contemporary cybersecurity challenges on the podcast "How Real Is It?". 

This quote shows the evolution of hacking tactics from forceful entry (e.g., breaking through security barriers, such as exploitation of public-facing systems) to subtler methods, such as phishing, social engineering, or using stolen credentials. These tactics exploit the normal functionalities of systems ("features") rather than their weaknesses ("bugs"), which can make them harder to detect and prevent. 

Organizations must therefore adopt a more comprehensive approach to security, one that goes beyond patching vulnerabilities to include managing and securing user identities and access privileges. 

The emphasis on logging in rather than breaking in shows that cybersecurity is increasingly about understanding and mitigating risks associated with user behavior and access management. 

Reference: https://www.youtube.com/watch?v=6BqpU4V0Ypk

### Finding Single-Factor Auth

![[スクリーンショット 2026-03-02 10.40.08.png]]

**Microsoft 365:** Often used by organizations for collaboration and communication, Microsoft 365 might be secured with only a password, making it a prime target for credential stuffing or phishing attacks.

**Remote Desktop Protocol (RDP):** Critical for remote administration but also a common entry point for attackers if only protected by 1FA, due to the direct access it provides to a system's desktop environment.

**Virtual Private Networks (VPNs):** While VPNs secure the transmission of data across networks, using 1FA can make them vulnerable to interception and unauthorized access, particularly if compromised credentials are used.

**Microsoft Exchange:** As a widely used email server, Exchange systems secured with 1FA are at risk of being compromised, leading to potential data breaches and access to sensitive communications.

**Active Directory:** Federation Services (ADFS): This service extends the capabilities of Active Directory to systems outside the corporate firewall and is particularly sensitive because it handles authentication across these boundaries. Entra ID is sometimes configured to use on-prem Active Directory for authentication via ADFS, which means Active Directory is essentially exposed externally.

**Enterprise Websites:** Often public-facing and therefore accessible to attackers, websites that use 1FA for administrative access can be easily compromised, leading to defacement, data theft, or worse.

Identifying and upgrading these systems to use multi-factor authentication (MFA) can significantly reduce the risk of unauthorized access and is a recommended step in strengthening an organization's security posture.

### NTLMRecon

- NTLMRecon scans URLs, hosts, and subnets for HTTP endpoints that perform NTLMSSP authentication
	- − Similar to Nmap's http-ntlm-info.nse script 
- Scans 50+ paths and submits fake authentication requests
- Gathers the following information about identified hosts
	- AD Domain Name
	- Server name 
	- DNS Domain Name 
	- FQDN 
	- Parent DNS Domain 
- Output is saved to a CSV file
NTLMRecon: https://github.com/pwnfoo/NTLMRecon/

NTLMRecon scans URLs, hosts, and subnets for HTTP endpoints that perform NTLMSSP authentication. While Nmap's http-ntlm-info.nse scrip provides similar information, it requires additional configuration to check specific paths, pages, or subdirectories for NTLMSSP authentication. NTMLRecon includes a list of 50 common paths without additional configuration, but it also allows users to specify a custom wordlist with **--wordlist**. 

To install and configure NTLMRecon, clone it from GitHub and run setup.py: 

`$ git clone https://github.com/pwnfoo/NTLMRecon.git` 
`$ cd NTLMRecon` 
`$ python3 setup.py install`

### Attacking NTLMSSP

- Internal Attacks 
	- HTTP NTLM endpoints are vulnerable to relay attacks 
- External Attacks 
	- Hosts that allow NTLM authentication are limited to single-factor auth 
	- Target these systems with credential stuffing and password spray attacks 
	- NTLM also leaks domain and server information, which is often for the internal domain 
- NTLMRecon will identify potential targets

```
$ ntlmrecon --input 10.130.10.0/24 --outfile ntlmrecon_10.130.10.0.csv [+] Brute-forcing 50 endpoints on https://10.130.10.1 [+] Brute-forcing 50 endpoints on https://10.130.10.2 [+] Brute-forcing 50 endpoints on https://10.130.10.3 [+] Brute-forcing 50 endpoints on https://10.130.10.4 [+] https://10.130.10.4/CertServ has NTLM authentication enabled!
```

Identifying NTLMSSP endpoints is helpful both internally and externally. On internal networks, NTLSMSSP endpoints can be targeted with relay attacks because, unlike SMB, there is no way to prevent an NTLM relay to an HTTP endpoint. Externally, NTLMSSP endpoints provide an opportunity for multifactor authentication (MFA) bypass. NTLM does not support MFA, so targeting exposed NTLMSSP endpoints with password sprays and credential stuffing attacks can be very effective.

### Credential Stuffing, in Brief (1)

1. People reuse passwords (often 1–5 pw patterns/person) 
2. Sites are compromised 
3. Organizations use 1FA externally

The result? Third-party breaches affect companies via employees' reused passwords.

![[スクリーンショット 2026-03-02 10.48.42.png]]

A widespread behavior among internet users is the reuse of passwords across multiple platforms. It's common for individuals to use the same one to five password patterns across various services. This habit significantly increases vulnerability because once one password is compromised, it can potentially provide access to several accounts owned by the same user.

Websites and services often suffer data breaches, leading to large volumes of user credentials being exposed. The attackers for these breaches don't have to release the breached credentials publicly, but they commonly do. These credentials can include usernames, passwords, and other personally identifiable information (PII).

Many organizations still rely on single-factor authentication for external access to systems, which only requires a username and password. This form of authentication does not provide sufficient security against the types of attacks that exploit stolen or reused credentials. 

The combination of these three elements leads to a situation where third-party breaches have a domino effect, impacting not just the directly breached service but also other organizations where the same credentials are reused. The comic panels in the slide humorously contrast the common misconception of hacking—as a dramatic physical infiltration—with the reality that many hacks are simply about exploiting known credentials across different systems. 

Don't worry, there will be future xkcd references in class! 

Obligatory xkcd reference: https://xkcd.com/2176/

### Credential Stuffing, in Brief (2)

![[スクリーンショット 2026-03-02 10.49.38.png]]

Here, we show a step-by-step example of a credential stuffing attack, showing how easy it can be using publicly available breach data and toolsets.

1. Credential Harvesting: The attacker begins by extracting potential credentials from a compiled list of breached data (COMB.txt) that specifically targets email addresses at the fake domain 'archer.zzz'. The command uses filters and saves these credentials into a file named 'creds.txt'. 
2. Credential Verification: Before attempting the actual attack, the first three credentials are verified to check their format and potential usability. This step ensures that the credentials are correctly formatted and plausible before they are used in an attack. 
3. Executing the Attack: Using the hydra tool, a well-known software for performing automated login attempts, the attacker tries to log into the 'krg.archer.zzz' mail server using the credentials saved in 'creds.txt'. The output shows that one of the login attempts was successful, indicating that the password for 'sarcher@archer.zzz' was 'Sploosh123!'.

The successful login highlights the critical vulnerability associated with reused passwords and singlefactor authentication systems. This demonstration underscores the importance of using multi-factor authentication and maintaining rigorous password policies to defend against such attacks. Additionally, it shows the practical application of tools available for such purposes, reminding organizations of the need to secure their systems against credential stuffing by regularly updating their cybersecurity measures.

### Credential Stuffing (The Commands)

![[スクリーンショット 2026-03-02 10.51.37.png]]

```
$ rg -iN '^[^@]+@archer.zzz:' COMB.txt > creds.txt 
$ head –n 3 creds.txt 
$ hydra –C creds.txt krg.archer.zzz imap
```

Here, we look at the commands used in a credential stuffing attack, specifically tailored to exploit email credentials from a breach compilation targeting the domain 'archer.zzz'.

1. Command for Extracting Credentials: The 'rg' (Ripgrep) command is utilized to search through a large dataset (COMB.txt), which contains breached email and password combinations. The parameter --iN specifies a case-insensitive search, focusing on lines that start with one or more characters not including the '@' symbol, followed by '@archer.zzz:', identifying email addresses from a specific domain. 
2. Verifying Credentials: The head -n 3 creds.txt command is used to quickly verify the format and plausibility of the top three entries in the 'creds.txt' file, ensuring that the data extracted is correctly formatted and usable for the next step in the attack. 
3. Using Hydra for Credential Stuffing: The last command, hydra -C, commands Hydra to attempt logins on the 'krq.archer.zzz' mail server using the IMAP protocol with the credentials listed in 'creds.txt'. This illustrates the practical use of Hydra to automate the login process using discovered credentials, highlighting the tool's efficiency in testing multiple combinations quickly.

### Software for Testing: Prepackaged Suites

- SANS Slingshot includes many of the tools to get you started in pen testing
- Other Linux distributions can also be helpful 
	- Kali Linux by Offensive Security: https://www.kali.org/ 
	- Parrot Linux from Parrot Security: https://parrotsec.org/ 
	- Black Arch Linux: https://blackarch.org/ 
	- Ubuntu or Debian with PTF: https://github.com/trustedsec/ptf

Remember, we've tested the provided Linux and Windows VMs, which have the lab directions and other files needed for the labs!

First, you need software for your testing regimen. With this course, you received a copy of the SEC560 Linux virtual machine. Furthermore, this VMware image includes tools pre-installed and, in many cases, preconfigured so that you can apply them directly in your own testing. 

Another useful source of tools is the bootable Linux distributions various people have made freely available, loaded with useful assessment and attack tools. A solid set of tools is included in Kali Linux, created and maintained by Offensive Security. Numerous similar Linux images for pen testing are also available, but Kali is one of the best because of its comprehensive set of tools, compatibility with a wide range of hardware, and carefully designed organization and layout.

### An Important Note: Command Prompts

![[スクリーンショット 2026-03-02 10.58.27.png]]

- We work with numerous different shells and switch often 
- Be aware of shells within shells: Linux → Metasploit → Meterpreter → … 
- The labs and notes indicate the prompts 
	- Windows cmd.exe: C:\> 
	- Windows PowerShell: PS C:\> 
	- Linux (non-root): $ or % 
	- Linux (root): # 
	- msfconsole: msf > 
	- Meterpreter: meterpreter >

Please make sure you enter commands at the right prompt!

Throughout this course, we use numerous different shells, both in our operating system and within Metasploit. We frequently change between these different shells as we switch back and forth between Linux and Windows, as well as within different aspects of Metasploit. Sometimes, even on a single page in the book, you use two or even three different types of shells to do something and then observe the results. 

All the labs and notes were carefully written to indicate the proper shell you are supposed to use at any given time by including the shell prompt right before each command you are supposed to type. That is, each lab command is preceded by the prompt indicating which shell to use. The shell types you encounter throughout this class include:

Double-check at each lab step that you are entering the proper command into the proper shell. Otherwise, a given lab step will not work for you properly.

### Networking

- VPN address will be 10.254.X.X 
	- tun0 on Linux, Ethernet2 on Windows 
- NAT addresses: will vary depending on your system! 
	- It will commonly be 172.16.X.X or 192.168.X.X, but it could be different 
- Network mask and DNS are set automatically 
- In your VMs, we have helpers that show your IP address in the UI 
	- Windows IP addresses can lag by up to a minute

![[スクリーンショット 2026-03-02 11.02.14.png]]

All the VMs will use DHCP to dynamically acquire IP addresses and network information.

Your VMs will be configured to use NAT, where the network connectivity will be shared with the computer, and the VMs are not directly accessible on the local network. The VMs will be able to communicate with each other.

Many of the labs will require that you connect to the SANS-hosted infrastructure using a VPN. To configure the VPN, follow the directions in your workbook (electronic or paper).

You will not use your host system in any of the lab networks; however, your host will get an IP address on the lab network (for in-person classes), so make sure you have the latest software patches and host protections in place.

### Remote Connectivity via VPN

![[スクリーンショット 2026-03-02 11.09.02.png]]

Depending on how you are using your local VM, you may need to use the IP address for the local interface or the VPN interface. If you are setting up a connection directly between your VMs, then use the address assigned to the eth0 interface on Linux or the Ethernet0 interface on Windows. 

If you need to have a remote target connect to your VM, such as an exploit callback, use your Windows or Linux tun0 interface and associated IP address or the Ethernet 2 interface on Windows.

## Building an Infrastructure

- For Pen Testing
	- Attack software 
	- Hardware 
	- Network infrastructure
- For Testing Tools and Techniques
	- Target software 
		- Active Directory domain controller
		- Target hosts and services 
	- Hardware 
	- Network infrastructure

Your first time running a tool shouldn't be in someone else's production network!

![[スクリーンショット 2026-03-02 11.22.03.png]]

Before we begin pen testing, we need systems from which we can test and systems that we can use as a test lab, such as for testing new tools and techniques. These two pieces will likely be separate, but we need the systems, nonetheless. Your needs may differ from what is presented here. This is designed to be a baseline infrastructure, which you can expand or modify based on your needs.

### Building a Lab

Test new tools and techniques in your lab! A broken lab is fine; breaking production systems is a big problem!

- Use VMs: Easily revert, store, clone, and build systems 
- Servers: Active Directory, File Servers, IIS; Multiple Linux distros 
- End user: Windows 11, Ubuntu, Fedora 
- Licensing:
	- Linux: Typically free. RHEL requires a paid subscription, but Fedora is very similar 
	- Windows: Use free trials or pay for a license

Virtual machines are a great way for offensive personnel to build, test, revert quickly, and clone test systems. The VMs can be run in the cloud, on the tester's laptop, or even on old hardware. 

You should test new tools in a laboratory environment to make sure you understand how they work and their potential impacts on a target machine. Such laboratory testing and analysis is especially vital for free tools downloaded from the internet because of concerns about quality, the potential to crash a target, and hidden functionality that could compromise the test systems. 

It is important to have a good lab that consists of many of the same systems and software you will see in the target environment. Obviously, the lab can't contain all possible combinations or all software, but a representative sampling is good for our purposes. 

We must have systems that resemble a wide range of target networks. Most networks use a Windows Domain and, therefore, a Domain Controller (we'll discuss attacking the Windows Domain later in this course). Similarly, most of these networks include Windows file servers and web servers. You could combine these two roles into one system to save on physical resources and licensing. Of course, the most widely used end-user system in the enterprise is Windows, so we'll need one of those systems in our lab. 

Unfortunately, using Windows in a lab is more difficult due to licensing. You can pay for licenses and can have as many different targets as you like, including older server and end-user systems. Many people have much tighter budgetary constraints that limit them to free systems only. We do not recommend or condone stealing or otherwise bypassing the legal licensing requirements set by Microsoft (or other vendors). Microsoft offers free VMs for testing; however, it is limited to the latest operating system, and the licensing window is limited. 

On the Linux side, we can use a range of free Linux systems. For example, Ubuntu is a common free Linux distribution found in enterprises. While most of the installs and updates for Linux are free, some are not. For example, Red Hat updates are not free, but it is very similar to Fedora.

### Systems Used for Internal Testing

Use a "leave behind" or "drop box" system for long-running tasks and remote access.
- Physical: Laptop or Minicomputer 
	- Use full disk encryption or wipe the device before return shipping 
	- Note: Internal policies often allow corporate-owned devices on the network but require extra paperwork for third-party-owned devices 
- Virtual: Custom VM
	- Target is responsible for destruction 
	- Requires internal team to provision, set up, and configure

If you're physically on the same network as the targets, use Bridged networking, not NAT!

Next, we need the actual systems that will be used for the testing. The needs for internal testing and external testing are different. 

For internal testing, we can use a "leave behind" or "drop box" for long-running tasks. We can also use this for remote-internal or pseudo-internal testing, which allows the penetration testers to work remotely but have internal access to their desired testing systems. The drop box could be physical, such as a laptop or a minicomputer. If you use full disk encryption (FDE) on the system, the target will then have to unlock the disk when initially booted or on reboot. Alternatively, the host OS could be booted without a key, and then the sensitive client data could be stored in an encrypted container using a key known to the pen testers. If there is any unencrypted data on the system, ensure it is properly scrubbed before shipping. You never want to ship unencrypted sensitive target data through the mail or a public shipping company. If that system is lost, it could be considered a reportable breach. 

To get around the shipping issue, some third-party testers will sell or expense the drop box to the client as part of the contract. This also solves another common issue: internal access. Some organizations have strict policies as to what systems are allowed on the network. Corporate assets are allowed on the network, but third-party-owned assets require extra verification, documentation, and paperwork. If the organization owns the physical drop box device, then it is often easier to get the device on the network. 

You could create a custom VM configured to connect back to the pen testers, and no shipping is ever required. However, this often requires extra action from the internal team to provision, set up, and configure the VM.

### Dedicated Test Systems

Use unique, dedicated systems for each client
- Ideally, even unique systems per engagement
- Reduces likelihood of cross-contaminated reports or going out of scope 
- Many testers use VMs since it is easier to start clean 
- Do not use your day-to-day, surfing, or email system for testing 
- Testing systems will not have security tools, such as firewalls or AV/EDR
- External Testing: Build a new system in the cloud for each test

Scrub client data and testing VMs at the conclusion of a test.

Next, you need the actual systems that will be doing the testing. We recommend that you use systems that are dedicated to testing and are unique for each engagement.

- Disabled defenses: The attack systems will not have defensive software such as AV/EDR, and it will often have its firewall disabled. These modifications decrease the defensive strength of the system should it come under attack from an adversary. You don't want these features disabled on your day-today system. 
- Uptime: The testing systems will often need to be available and online for weeks or months. If you were to run the attack from your laptop (or even a VM on your laptop), the test could be impacted if you carry your laptop to another location (network change) or perform your day-to-day tasks (installing software, rebooting, patching). 
- Prevent data leakage: If you use unique systems for each test, it is less likely that the data from one test will end up in the data from another test. This is especially important for third-party testers where the test data is likely to be from different organizations where such an action could be considered a breach of confidentiality. Such cross-contamination could lead to embarrassment, loss of trust, and even legal action.

At the conclusion of a test, be sure to clean the target data from the test systems. You can't lose data you don't have.

### Sources for Free Tools and Exploits

- Exploit-DB: exploit-db.com 
	- Sorted by remote, local, web app, denial of service, shellcode, and papers 
- MITRE CVE Repository: cve.org 
	- Latest information about vulnerabilities 
- GitHub: github.com 
	- Search by CVE or vulnerability name 
	- Warning: Some people lie on the internet

We are not endorsing these sites or the tools they distribute. Remember to be careful! Always test exploits and tools in your lab!

Although there are numerous exploit and attack tool repositories on the internet, some of the most comprehensive archives that are updated on a regular basis include the Exploit Database and Packet Storm Security. Other sites come and go on a regular basis, but these sites are long-standing and tend to have relatively higher-quality tools. 

The Exploit Database (https://www.exploit-db.com/, or exploit-db for short) is maintained by the same group that maintains Kali Linux, Offensive Security. Its site hosts more than 10,000 exploits and sorts them into useful categories, such as Remote Exploits, Local Exploits, Web Applications, Denial of Service/Proof of Concept, Shellcode, and Papers. For each exploit in these categories, it lists the platform (Windows, Linux, PHP, and so on) and the author. 

Note that we are not endorsing these sites or the tools that they distribute. These sites have been quite controversial, and you need to be careful with any code you download. Be careful downloading tools from sources you have not vetted, as malicious attackers routinely release fake exploits that will actually compromise your attacking system (https://www.bleepingcomputer.com/news/security/fake-microsoftexchange-proxynotshell-exploits-for-sale-on-github/). 

Beyond the tool and exploit sites, numerous vulnerability research sites are also available. Although these sites do not distribute exploit code freely, they do publish information about vulnerabilities. These detailed vulnerability descriptions are invaluable in letting a tester know that there is an issue with a system type or service version discovered in a test. Even though an exploit might not be available (in fact, an exploit may have never been publicly released or even created), the tester still needs to understand the vulnerabilities so that they can be included in the test report. 

Some of the best sites with vulnerability research and detailed descriptions are the following sites:
- The United States Computer Emergency Readiness Team (US-CERT) (https://www.cisa.gov/newsevents/cybersecurity-advisories), maintained by the US Department of Homeland Security (DHS) 
- The Common Vulnerabilities and Exposures (CVE) repository operated by MITRE (https://cve.mitre.org/)

### Tools for Penetration Testing Teams

- Knowledge base 
	- Must be easy to search and update or it won't be used 
	- Share tools and techniques for specific attacks 
	- Internal teams can share remediations and solutions 
	- Tools: OneNote, Confluence, Wiki 
- Storage − In-house tools, code, artifacts found in target environment 
	- Tools: File share, SharePoint, cloud storage, Git 
- Central code repository (Git, CVS, SVN, Mercurial, TFS/VSTS)
- Chat (secured) 
- Secure credential sharing 
	- You need a secure way to share credentials with your team 
	- Tools: 1Password (includes 2FA), LastPass, KeePass 
- Common finding repository 
	- Don't rewrite the same finding every time; tweak it for the specific case 
	- Tools: Excel, Word, DB, Git 
- Collaborative report writing 
	- Tools: M365, Google Docs, Git 
- Pen Test specific (less common) 
	- Tools: Dradis, PlexTrac

Most penetration testers work as part of a team. Even if you are working by yourself, it is important to take good notes so you can save yourself time on future engagements. We recommend that you have a knowledge base containing tools, techniques, and command examples to make your attacks more efficient. Also, this is a great way to share with your team, especially new or junior members. The specific tool isn't as important as the process, but some of the common tools used are Confluence, a wiki, or OneNote. This must be easy to use, search, and update, or your team won't use it.

You will need storage for data specific to the engagement. You will often find keys, certificates, and other artifacts that are useful in the penetration test that you need to (securely) share with other team members. This could be an internal file share, SharePoint, cloud storage, or a Git repository. This needs to be easily accessible and updatable by the team members. Remember to clean up this data when the test is complete!

Your team will often have in-house code that needs to be shared with team members. Most teams these days use Git. Other common version control software includes CVS, SVN, Mercurial, and the Microsoft Team Foundation Server (TFS) or Visual Studio Team Services (VSTS).

Your team needs to communicate, so a secure chat mechanism is important. Signal and Telegram offer great security, but the scalability for an enterprise-ready tool is lacking. Microsoft Teams, while somewhat behind the curve in the world of chat, offers retention policies to make cleanup automatic. Similarly, Slack offers a retention policy at their paid tier. Many teams using internal services, such as IRC, allow the pen testers to have more control over the data.

Clients will give you credentials, you will likely acquire new credentials (cracking, guessing, or even cleartext), and you possibly will even create accounts in the target network. It is important to securely share that information with your teammates. There are a number of password vaulting tools that can be useful here, including 1Password, LastPass, and KeePass. One nice benefit of 1Password is that it also allows you to store some 2FA tokens.

Finally, we need ways to write reports in a consistent manner. It is a timesaver to have a repository for common vulnerabilities and findings. These findings are pre-vetted prior to going into the report. This allows you to have a good quality base for the finding and you can tweak the finding for the particular scenario or target. This is a tremendous time-saver. The findings in the repository should not contain any target information, making it much safer to copy into a report. Be very careful never to copy data between reports, as this can lead to data leakage. The authors of this course have heard of multiple instances where one target's name or details ended up in a report for a different target. This can be embarrassing and undermine your credibility.

To make report writing easier for multiple people, having a collaborative reporting environment is nice because it saves time. Ideally, this allows multiple people to work on the report (and even the same portion of the report) at the same time. Services like Google Docs and SharePoint with Microsoft Office allow for this kind of shared editing. Some penetration testers use version control software (e.g., Git, SVN) to manage reporting, but in the author's experience, this can lead to a number of code/edit conflicts that take extra time to resolve.

Some penetration testers use collaborative tools specifically designed for penetration testing. The two most common ones are Dradis and Lair.

The Dradis tool is a Ruby-on-Rails project designed for recording information among multiple penetration testers working on one or more projects together. The Dradis server runs on Windows, Linux, or macOS and features multiple client options: a command-line client, several different thick client applications, or a web-based interface.

All results are organized as a hierarchical tree, typically organized starting by overall project, then split according to functional areas of the target infrastructure (for example, DMZ/intranet/extranet, servers/network devices/clients, or other applicable divisions of the test's scope), then separated by individual devices, down to individual ports on those devices, and then through findings and notes associated with each port.

With Dradis, a tester can import results from the Nmap port scanning tool, the Nessus or Qualys network vulnerability scanning tools, the Nikto web server scanning tool, or the Burp web application attack tool. In addition, the tester can manually enter findings and notes or add analytical notes to results already imported.

## Linux for Penetration Testers

### Linux vs. Windows

Windows and Linux are simply tools (or toolboxes) 
- Many attack tools work better/only on Linux
- Domain operations are often easier on Windows 
- Switch between them as needed (VMs)
- macOS is acceptable, but use VMs 
	- macOS is required for some iOS mobile testing

That said, Linux is an OS penetration testers need to understand. We need to briefly go through Linux basics to make sure everyone is on the same page.

A common question among penetration testers and ethical hackers is, "Should I focus my skills and toolbox on Linux or Windows?" When confronted with this question, we recommend that your pen test toolset include both operating systems side by side, working together to maximize your efficiency and capabilities. The truth is that some tools work better on Linux, whereas others work better on Windows. Some tools work just fine on both, whereas other tools have been released for only one of those platforms. Thus, if you choose to work in only one OS or at least just focus on that OS, you'll be missing out on a lot of useful tools and techniques. To improve productivity and streamline workflow, we recommend virtualizing one of these two OSs, perhaps using VMware, and running the two simultaneously on the same hardware so that you can quickly switch between them.

The entire question posed at the start of the preceding paragraph illustrates a mindset that should be transcended. Don't think of them as two different operating systems. Think of them as one set of tools that you use in your penetration testing and ethical hacking job. As a carpenter or plumber would use the best tool that is available and convenient for a given job, so should you. To continue with that analogy, don't think of Windows and Linux as two different toolboxes; instead, they are two different compartments in your single toolbox.

Some of you are no doubt wondering whether macOS is an acceptable platform for penetration testing and ethical hacking. It is—with remarkable stability and ease of use. However, there are some tools for Linux and Windows that simply will not run on macOS, no matter how hard you try to get them installed. Thus, if you plan to use macOS, make sure you get a virtualization solution for it (such as VMware Fusion or Parallels) so that you can also run both Windows and Linux on top of macOS.

In this course, we will be using our Linux VM to perform our attacks. Linux is the Operating System (OS) used by the vast majority of offensive personnel. Many tools work better or work only in Linux. Also, the package management system in many of the Linux distributions makes the configuration and setup of the tools quite easy. While we will be using Linux a lot, remember that both Windows and Linux are simply tools, and we should switch between them as needed to be efficient.

560vm is based on Ubuntu. Inside our VM, we will be using the Bourne Again Shell (Bash) as our shell on the command line. There are other Linux distributions and shells, each of which does things a little differently, but the core functionally is the same.

### Users: Root and Non-root

- Principle of least privilege: Only use elevated privileges when necessary
	- As attackers, we can often exploit deficiencies in least privilege 
- Non-root users − Have a prompt with a $ (sometimes % in other shells) 
	- Home directory similar to: /home/username 
- Root users 
	- Have a prompt with a # 
	- Home directory: /root
- As root, create a (backdoor) account: useradd username 
- Change your password with passwd 
	- Change another user's password (must be root): passwd username

Pen Test Tip: See if you can access other user's home directories, history files, and keys

The principle of least privilege "states that only the minimum access necessary to perform an operation should be granted, and that access should be granted only for the minimum amount of time necessary" (US-CERT).

Attackers will often abuse shortcomings in the execution of the principle to escalate permissions or move from a non-privileged user to a privileged user. On a Linux system, the prompt will change depending on the user's access level. Also, the home directories for "root" and regular users are, by default, in different locations.

- Non-root users: 
	- Have a prompt with a $ or % 
	- Home directory similar to: /home/username 
- Root users:
	- Have a prompt with a # 
	- Home directory: /root

If you want to create an account, such as a backdoor for persistence, you can use the useradd command. The command takes the following options:

useradd [options] LOGIN 
	-d HOME_DIR 
	-e EXPIRE_DATE (format YYYY-MM-DD) 
	-u UID (numeric user ID, must be unique unless -o is used. Users with a UID of zero are granted root-level access)

See more options by running: man useradd

### Who Am I?

 - Attackers will often gain access to a system and have no idea who they are or what access they have
	- The shell prompt is not always visible to an attacker
- Get your username with whoami 
- Get your user ID and group IDs with id

```
whoami 
clark 
id 
uid=1000(clark) gid=1000(clark) groups=1000(clark),24(cdrom),27(sudo)… ./exploit 
whoami 
root 
id 
uid=0(root) gid=0(root) groups=0(root)
```

When you successfully exploit a system, often, you will not know the level of access you have or the user you are running as. One of the first things to do is run the whoami command to determine your user. To get more information about your access, you can use the id command to get your numeric user ID and your group memberships. In the example below, we have access as a regular user (clark), and then, using a privilege escalation exploit, we gain root permissions.

### Linux Filesystem Structure

- The top of the file system is called / (root) 
- The filesystem can vary for different distributions of Linux and BSD

TIP The /etc directory contains a number of interesting files, including configuration files, account information (in passwd), and hashes (in shadow). This is often a useful location to search.

![[スクリーンショット 2026-03-02 12.08.04.png]]

- /bin and /sbin contain executable programs. 
- /root is the root login account's home directory. This is hugely important because if you log in directly as root, this will be your initial location in the directory structure. If you log in as an individual user other than root, you'll be put in that user's directory, typically somewhere inside of /home. 
- /dev stores devices (drives, terminals, etc.). 
- /etc holds configuration items, like the account information (stored in /etc/passwd) and hashed passwords (stored in /etc/shadow). 
- /home contains users' home directories. 
- /lib contains common libraries.
- /mnt is where various remote and temporary file systems (CD-ROMs, floppies, etc.) are attached. 
- /proc is a virtual file system used to store kernel info. 
- /tmp is for temporary data and is usually cleared at reboot. 
- /usr holds user programs and other data. 
- /var holds many different items, including logs (/var/log/). 
- /opt stores optional items and is often a location for specialized tools that have been added to a distribution.

### Navigating the Filesystem

![[スクリーンショット 2026-03-02 12.12.25.png]]

We can use the cd command to change directories. This moves our current location to the new location. The shell will often show us our current directory, but if we want to print the working directory, we can use the pwd command.

When referencing files or directories, we can use their absolute path or the location relative to our current location. The absolute path is the full path to the object. The command below uses absolute pathing:

```
sec560@560vm:~$ cd /var/log 
sec560@560vm:/var/log$ pwd 
/var/log
```

Relative pathing references the location relative to our current location and does not use the leading slash (/). We can use .. ("dot dot") to access the parent directory (go up one level). This command uses relative pathing and changes our current working directory to /var, but only because we are currently in a directory under var:

```
sec560@560vm:/var/log$ cd .. 
sec560@560vm:/var$ pwd
/var
```

This command also uses relative pathing to move to the log directory in our current directory (var):

```
sec560@560vm:/var$ cd log
sec560@560vm:/var/log$ pwd
/var/log
```

Your home directory is aliased as ~ (tilde). To move to your home directory, you can use

```
sec560@560vm:/var/log$ cd ~ 
sec560@560vm:~$
```

If we use the cd command without a directory, it will take us to our home directory.

```
sec560@560vm:/var/log$ cd 
sec560@560vm:~$
```

### Where Am I?

![[スクリーンショット 2026-03-02 13.31.51.png]]

When you gain access to a system, you often do not know your current working directory. To get this location on Linux, we can use the pwd (print working directory) command. In a continuous shell, such as with the terminal, you can change directories and the shell remembers the change. The example below shows a continuous shell where we can't see the shell prompt.

```
pwd 
/var/www 
cd /etc 
pwd 
/etc
```

### Listing Files

![[スクリーンショット 2026-03-02 15.22.35.png]]

The ls command is used to "list" files. By default, the command will only show the file names. The default option will also skip hidden files. On Linux, an object is hidden when the first character is a dot (.). To see these hidden files, we can use the -a option to show "all" objects. Use the -l (lowercase L) to show the long format, which includes the type and permissions, link count, owner, group, file size, timestamp, and name.

### Permissions—File Mode Bits

![[スクリーンショット 2026-03-02 15.23.24.png]]

Permissions are broken into four parts: 
1. Type: The first letter in the permission is the type of the object. The most common letters you will see are: 
	- Directory: d 
	- Regular file: - 
	- Symbolic link: l 
2. User (owner): These are the permissions given to the user who owns the file. We use the term "user" since "owner" starts with an "o," and we need to differentiate between the user (u) and other (o) when using commands like chmod (discussed later). 
3. Group: These are the permissions given to the members of the group assigned to the file. 
4. Other: These are the permissions given to everyone who is not an owner or the relevant group. In certain cases, this is referred to as the "world" permissions. 

The User, Group, and Other permissions are broken down into three pieces. If there is a letter in the relevant position, then the permission is granted. If it is a minus/dash (-), then the permission is not granted. The permissions in order are: 
1. r: Read – Grants the permission to read the file or directory 
2. w: Write – Grants the permission to write to the file or directory 
3. x: Execute – For files, it grants the permission to run or execute the file. For directories, it grants permissions to enter the directory and access files and directories inside.

There is another special permission that can be applied to files instead of the "x". An "s" in the first (user) or second (group) is the SETUID or SETGID bit, respectively. SETUID means the executable runs under the context of the user (owner). Likewise, the SETGID bit means the executable will run under the context of the specified group.

### Changing File Permissions

![[スクリーンショット 2026-03-02 15.27.01.png]]

The chmod command changes file permissions and can use either symbolic mode or octal mode. 

Symbolic mode uses intuitive characters to represent the permissions changes. The parameters specify the target of the change, a modifier, and the permission to modify.

Target: 
- u : user (owner) 
- g : group 
- o : other
- a : all (user, group, others)

Modifiers: 
`+`: add permission
`-`: remove permission 
`=` : set specific permission  
Permissions: 
- r : read
- w : write
- x : execute

Octal mode uses numeric values to set specific permissions. Each target has three permissions, which are assigned a number. The permission's numeric representations are then added together to summarize the target's permission with a single number. If a permission isn't set, it is treated as 0. The values are:

- 4 – read (r) 
- 2 – write (w) 
- 1 – execute (x) 
- 0 – not set (-)

Permissions of rwx are represented by the numeric value of 7, which is the result of adding 4+2+1.

File example: The default permissions for a file displayed by ls -l are -rw-rw-r--, which in octal would be 664:

- Type: - this is a file 
- User: rw- = 4+2+0 = 6 
- Group: rw- = 4+2+0 = 6 
- Others: r-- = 4+0+0 = 4

### Escalating with SETUID


















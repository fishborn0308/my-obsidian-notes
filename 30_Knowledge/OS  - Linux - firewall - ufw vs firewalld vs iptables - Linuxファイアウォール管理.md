---
tags: [ufw firewalld iptables firewall security netfilter systemd fail2ban]
created: 2025-06-29 15:02
modified: 2026-02-20 15:23
environment: [OS/Linux]
vulnearability: []
knowledge_category: OS
---

# OS  - Linux - firewall - `ufw` vs `firewalld` vs `iptables` - Linuxファイアウォール管理

## 概要

Linuxカーネルには **Netfilter** というパケットフィルタリング機能が組み込まれており、これがファイアウォールの実体です。`ufw`, `firewalld`, `iptables` は、このNetfilterをユーザーが管理するためのフロントエンドツールです。

* **`iptables`**: 古くから使われている伝統的なツール。ルールを**チェイン**（INPUT, OUTPUTなど）に直接追加していく、低レベルで非常に柔軟な設定が可能です。
* **`firewalld`**: RHEL/CentOS 7以降で標準となった、より高レベルでダイナミックな管理ツール。**ゾーン**という概念を導入し、インターフェイスごとに異なるポリシーを簡単に適用できます。
* **`ufw`** (Uncomplicated Firewall): Ubuntuで標準採用されている、**シンプルさ**を最優先した管理ツール。複雑さを隠蔽し、「ポートを開ける」といった単純なルールを非常に簡単なコマンドで実現することに特化しています。

(出自: `iptables`, `firewalld`, `ufw` パッケージ)

## 類似コマンドと差異

| 特徴 | `ufw` (Uncomplicated Firewall) | `firewalld` | `iptables` |
| :--- | :--- | :--- | :--- |
| **管理パラダイム** | **シンプル**なルールと**アプリケーション**プロファイル。最も高レベルで直感的。 | **ゾーン**と**サービス**ベース。高レベルで抽象的。 | **チェイン**と**ルール**ベース。低レベルで詳細。 |
| **使いやすさ** | **非常に簡単**。学習コストが最も低い。 | 一般的なタスクは**簡単**。 | 非常に柔軟だが、**専門知識が必要**。 |
| **設定変更** | **動的**。設定変更は既存の通信に影響しない。ルールはデフォルトで永続化。 | **動的**。リロードしても既存の通信は維持される。 | **静的**。ルールセット全体の再読み込みで、一瞬通信が途切れる可能性あり。 |
| **デフォルト** | **Ubuntu**, Debian | **RHEL**, CentOS, Fedora | 旧世代の各ディストリビューション |
| **主な用途** | デスクトップ、シンプルなサーバー | 複数のネットワークを持つサーバー、複雑な環境 | 詳細なカスタムルールが必要な環境 |

---

## 一般的なファイアウォールタスクの比較

| タスク | `ufw` | `firewalld` (`firewall-cmd`) | `iptables` |
| :--- | :--- | :--- | :--- |
| ⭐ **ルール一覧** | `sudo ufw status numbered` | `sudo firewall-cmd --list-all` | `sudo iptables -L -n -v` |
| **有効化** | `sudo ufw enable` | `sudo systemctl enable --now firewalld` | `sudo systemctl enable --now iptables` |
| ⭐ **TCP/80番を許可** | `sudo ufw allow 80/tcp` | `sudo firewall-cmd --add-port=80/tcp --permanent` | `sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT` |
| **サービス名で許可**| `sudo ufw allow http` | `sudo firewall-cmd --add-service=http --permanent` | (直接は不可) `iptables ... --dport 80 ...` |
| ⭐ **IPアドレスを拒否**| `sudo ufw deny from 1.2.3.4` | `sudo firewall-cmd --add-rich-rule='rule family="ipv4" source address="1.2.3.4" reject' --permanent` | `sudo iptables -A INPUT -s 1.2.3.4 -j REJECT` |
| **設定のリロード** | (不要、即時反映) | `sudo firewall-cmd --reload` | `sudo systemctl restart iptables` |
| **設定の永続化** | **デフォルトで永続化** | `--permanent` オプションが必要 | `service iptables save` や `iptables-persistent` が必要 |

---

## よく使われるオプション組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: Webサーバーを構築し、SSH, HTTP, HTTPSアクセスのみを許可する。
* **`ufw` での実装 (最もシンプル)**:

```bash
sudo ufw default deny incoming
sudo ufw allow ssh
sudo ufw allow http
sudo ufw allow https
sudo ufw enable
```

* **`firewalld` での実装**:

```bash
sudo firewall-cmd --add-service=ssh --permanent
sudo firewall-cmd --add-service=http --permanent
sudo firewall-cmd --add-service=https --permanent
sudo firewall-cmd --reload
```

* **`iptables` での実装**:

```bash
sudo iptables -P INPUT DROP
sudo iptables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT
sudo iptables -A INPUT -i lo -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 443 -j ACCEPT
sudo service iptables save
```

### 2. ブルーチーム視点

* **タスク**: 攻撃元IP `198.51.100.10` からのアクセスを即座にブロックする。
* **`ufw` での実装**:

```bash
# ルールの先頭に deny ルールを挿入して最優先でブロック
sudo ufw insert 1 deny from 198.51.100.10
```

* **`firewalld` での実装**:

```bash
# rich-rule を使って即時ブロック (永続化しない)
sudo firewall-cmd --add-rich-rule='rule family="ipv4" source address="198.51.100.10" drop'
```

* **`iptables` での実装**:

```bash
# INPUTチェインの先頭にDROPルールを挿入
sudo iptables -I INPUT 1 -s 198.51.100.10 -j DROP
```

### 3. レッドチーム視点

* **タスク**: 侵入したサーバーのファイアウォール設定を調査し、「穴」を探す。
* **調査コマンド**:
	* **ufw**: `sudo ufw status`
	* **firewalld**: `sudo firewall-cmd --list-all`
	* **iptables**: `sudo iptables -L -n -v`
* **解説**: 攻撃者はまずファイアウォール設定を偵察し、許可されているポート（例: DNS/53, HTTP/80）を使ってC2通信を偽装したり、もしroot権限を奪取できれば、自分たちの通信を許可するルールを追加したりファイアウォール自体を無効化します。

---

## 環境変数と設定ファイル

* 一般的な環境変数・設定ファイルは [Linux共通の環境変数・設定ファイル](./environment_and_config.md) を参照。

### 関連する設定ファイル

* **`ufw`**:
	* `/etc/default/ufw`: メイン設定ファイル。
	* `/etc/ufw/before.rules`, `after.rules`: カスタムルール用。
* **`firewalld`**:
	* `/etc/firewalld/firewalld.conf`: デーモンのメイン設定ファイル。
	* `/etc/firewalld/zones/`: ゾーン設定ファイル（XML形式）が格納される。
* **`iptables`**:
	* ルールはメモリ上で管理され、特定のファイルに自動保存されません。
	* **RHEL/CentOS**: `/etc/sysconfig/iptables` に `service iptables save` で保存。
	* **Debian/Ubuntu**: `iptables-persistent` パッケージを使い `/etc/iptables/rules.v4` に保存。

## セキュリティに関する考慮事項

### 脆弱性と悪用事例

* **脆弱性**: ツール自体よりも**設定ミス**が最大の脆弱性です。意図せず不要なポートを開けたり、テスト用に無効化したまま放置したりすることが侵入の直接的な原因となります。
* **悪用シナリオ**: 攻撃者がroot権限を奪取した場合、ファイアウォールルールを変更して自身のアクセスを許可したり、ファイアウォール自体を無効化して検知を逃れようとします。

### GTFOBins / LOLBAS における利用例

* **機能**: **Privilege Escalation**, **Defense Evasion**
* **解説**: もし一般ユーザーに `sudo` で `iptables` や `firewall-cmd` を実行できる権限が与えられている場合、そのユーザーはファイアウォールに任意のルールを追加できます。例えば、外部からリバースシェルを受け付けるためのポートを開放したり、ファイアウォールを完全に無効化したりすることで、実質的にシステムのセキュリティを無力化できます。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: **Default Dropポリシー**（明示的に許可したもの以外はすべて拒否する）を基本方針とします。`sudoers` の設定を厳格にし、一般ユーザーにファイアウォール管理コマンドの実行権限を与えない。
* **Detection (検知)**: `auditd` を使ってファイアウォールルールの変更を監視する。ファイアウォールのログをSIEMに転送し、予期せぬ許可/拒否イベントを監視する。

## 注意点・補足

* **SSH接続の許可を忘れずに**: リモートサーバーでファイアウォールを有効化する際は、**必ずSSHの接続を許可するルールを追加してから有効化してください**。これを忘れると、サーバーから締め出されてしまいます。
* **ツールの併用は避ける**: `firewalld`, `ufw`, `iptables`サービスなどを**同時に有効にしないでください**。互いに干渉し、予期せぬ動作を引き起こす原因となります。


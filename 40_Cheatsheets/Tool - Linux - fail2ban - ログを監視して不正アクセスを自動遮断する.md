---
# --- YAML Frontmatter ---
created: 2026-02-22 21:24
modified: 2026-02-23 09:52
environment: [OS/Linux, Network/Security]
vulnearability: [Brute_Force_Attack, Denial_of_Service]
knowledge_category: Tool/Service
tags:
  - fail2ban
  - security
  - firewall
  - intrusion-prevention
  - knowledge_base
---

# Tool - Linux - fail2ban - ログを監視して不正アクセスを自動遮断する

## 概要

`fail2ban` は、各種サービスのログファイル（SSH, Apache, Nginx 等）を監視し、認証失敗などの不審なパターンを繰り返す IP アドレスを特定。一定期間、ファイアウォール（iptables や nftables）のルールを更新してその IP からの接続を拒否する侵入防止フレームワークです。

## 主要な構成概念

| コンポーネント | 役割 |
| :--- | :--- |
| **Filter (フィルター)** | ログの中から「攻撃」と見なすパターン（正規表現）を定義したもの。 |
| **Action (アクション)** | バンした際に行う処理（iptables への追加、メール通知等）を定義したもの。 |
| **Jail (刑務所/ジェイル)** | フィルターとアクションを組み合わせた実行単位。SSH 用、HTTP 用などがある。 |

## 操作用コマンド: fail2ban-client

`fail2ban` 自体はデーモンとして背後で動いていますが、設定の確認や操作は `fail2ban-client` を使用します。

### よく連携されるコマンド

* **目的**: 実際にバンされている IP アドレスを確認し、必要に応じて手動で解除する。
* **連携コマンド**: `iptables -L`, `nft list ruleset`
* **コマンド例**:
    ```bash
    # 現在アクティブな Jail のリストを確認
    sudo fail2ban-client status
    # sshd ジェイルで現在バンされている IP を確認
    sudo fail2ban-client status sshd
    ```

## オプション・サブコマンド説明

※実行には **root 権限** が必須です。

| サブコマンド | 説明 |
| :--- | :--- |
| ⭐️ `status` | 全体、または特定の Jail の稼働状況とバンされた IP を表示する。 |
| ⭐️ `set <JAIL> unbanip <IP>` | 間違えてバンしてしまった自分の IP などを手動で解除する。 |
| ⭐️ `set <JAIL> banip <IP>` | 特定の悪質な IP を手動で即座にバンする。 |
| `reload` | 設定ファイルを再読み込みする。 |

## よく使われる業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: SSH へのブルートフォース攻撃対策。
* **解説**: デフォルトで有効にされることが多い設定です。3回ログインに失敗したら 10分間アクセス禁止にする、といった設定でサーバーを保護します。
* **設定例 (`/etc/fail2ban/jail.local` 等)**:
    ```ini
    [sshd]
    enabled = true
    port    = ssh
    filter  = sshd
    logpath = /var/log/auth.log
    maxretry = 3
    bantime  = 600
    ```

### 2. ブルーチーム視点

* **タスク**: 攻撃者の「粘り強さ」の分析と長期的な防御。
* **解説**: `fail2ban` のログ (`/var/log/fail2ban.log`) を分析し、繰り返しバンされている IP レンジを特定。それを上位のファイアウォールで恒久的に遮断（Blacklist）する判断材料にします。
* **例**:
    ```bash
    # バンされた履歴が多い IP のランキングを表示
    grep "Ban" /var/log/fail2ban.log | awk '{print $NF}' | sort | uniq -c | sort -nr
    ```

### 3. レッドチーム視点

* **タスク**: バンを回避するための低速攻撃（Low and Slow）。
* **解説**: `fail2ban` の設定（`maxretry` と `findtime`）を推測し、検知に引っかからない絶妙な間隔（例: 10分に1回だけ試行）で攻撃を行います。また、複数のプロキシや IP をローテーションさせて検知を分散させます。

## エラーメッセージとトラブルシューティング

1.  **エラーメッセージ例 1**: `error: [Errno 111] Connection refused`
    * **原因**: `fail2ban-server` が起動していない。
    * **解決策**: `sudo systemctl start fail2ban` を実行。

2.  **IP を解除したのにアクセスできない**:
    * **原因**: `fail2ban-client unbanip` で解除しても、ファイアウォール（iptables）側にゴミが残っている、または別の Jail でもバンされている。
    * **解決策**: `iptables -S` でルールを確認し、手動で削除するか、全ての Jail の状態をチェックする。

## セキュリティに関する考慮事項

### 脆弱性とリスク

* **DoS 攻撃への悪用**: 攻撃者が「送信元 IP を偽装（Spoofing）」できる場合、ターゲットの管理者の IP を送信元に仕立てて攻撃を模倣することで、管理者自身をサーバーから締め出させる（Denial of Service）ことが理論上可能です。

### 対応策・緩和策 (ブルーチーム視点)

* **Ignore IP**: `/etc/fail2ban/jail.conf` の `ignoreip` に、管理用端末や社内拠点の固定 IP を必ず登録しておき、自爆（セルフバン）を防ぎます。
* **Recidive Jail**: 何度もバンを繰り返す「常習犯」に対して、より長いバンタイム（例: 1週間）を適用する Jail を構成します。

## 注意点・補足

* **ログの消失**: ログローテーション（logrotate）の設定と `fail2ban` の監視タイミングがずれると、一瞬だけ検知が漏れる可能性があります。
* **リソース消費**: 非常に巨大なログファイルを正規表現で常にスキャンし続けるため、アクセスが極端に多いサイトでは CPU 負荷の原因になることがあります。
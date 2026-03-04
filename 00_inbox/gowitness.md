---
# --- YAML Frontmatter ---
created: '2024-05-23'
modified: '2024-05-23'

environment: [Linux, macOS, Windows, Web]
vulnearability: [Information_Gathering, Reconnaissance]

knowledge_category: Command
tags:
  - 'gowitness'
  - 'bash'
  - 'recon'
  - 'knowledge_base'
---

## 1. 概要
`gowitness` は、Go言語で書かれた、Google Chrome (headless) を利用してウェブサイトのスクリーンショットを自動的に取得するユーティリティです。
ペンテストやバグバウンティの偵察（Reconnaissance）フェーズにおいて、大量のホストやIPアドレスから稼働しているWebサービスを視覚的に素早く確認するために使用されます。

単なるキャプチャ機能だけでなく、取得した結果を SQLite データベースに保存し、レポート用サーバーを立ち上げてブラウザから結果を一覧表示できる機能が強力です。

## 2. コマンドオプション解説

### 基本構造
```bash
gowitness [command] [flags]
```

### 主要なサブコマンド

- **`single`**: 単一のURLに対してスクリーンショットを取得。
    
- **`file`**: ファイルからURLのリストを読み込んで一括取得。
    
- **`scan`**: NmapやNessusの出力ファイル（XML）からターゲットを抽出。
    
- **`report`**: 取得した結果を表示するためのWebインターフェースを起動。
    
- **`server`**: API駆動のスクリーンショットサービスとして実行。
    

### 全般的な主要オプション

|**オプション**|**短縮**|**説明**|
|---|---|---|
|`--url`|`-u`|ターゲットとなるURLを指定 (single用)|
|`--file`|`-f`|ターゲットリストが記述されたファイルパス (file用)|
|`--resolution`|`-r`|スクリーンショットの解像度 (例: `1920,1080`)|
|`--timeout`|`-t`|タイムアウト秒数 (デフォルト: 10)|
|`--delay`|`-d`|ページロード後の待機秒数 (JavaScript実行待ちに有効)|
|`--threads`|`-T`|並行実行スレッド数 (デフォルト: 4)|
|`--db`||結果を保存するSQLiteデータベース名 (デフォルト: `gowitness.db`)|
|`--proxy`||HTTP/SOCKSプロキシ経由で実行|
|`--user-agent`||カスタムUser-Agentの使用|
|`--header`|`-H`|カスタムHTTPヘッダーの追加|
|`--no-http`||デフォルトの 80番ポートへの試行を無効化|
|`--fullpage`||ページ全体のスクロールスクリーンショットを取得|

---

## 3. 実務ログ例

### シナリオ1: Nmapの結果からWebサービスを視覚化する

Nmapでスキャンした結果（`-oX`）を元に、オープンしているポートに対して一括でスクリーンショットを撮影します。


```bash
# 1. Nmapでスキャン実行
nmap -sV -p 80,443,8080,8443 192.168.1.0/24 -oX scan_results.xml

# 2. gowitnessでスキャン結果を読み込み
gowitness nmap -f scan_results.xml --threads 10

# 3. レポートサーバーを起動して確認
gowitness report serve --address 127.0.0.1:8081
```

> **実行結果ログ:**
> 
> `[*] Scanned 24 hosts from nmap file`
> 
> `[*] [http://192.168.1.10:80] Title: Home | Login`
> 
> `[*] [https://192.168.1.15:8443] Title: pfSense - Login`
> 
> `[*] Starting report server at http://127.0.0.1:8081`

### シナリオ2: 認証が必要なサイトのキャプチャ (Cookie使用)

ログイン後の画面を確認したい場合や、特定のセッションを維持したままキャプチャを撮る際に使用します。


```bash
gowitness single -u [https://internal-admin.local/dashboard](https://internal-admin.local/dashboard) \
  --header "Cookie: session=xxxxxx" \
  --delay 3 \
  --resolution "1440,900"
```

---

## 4. ペンテストにおける活用Tips

- **デフォルト認証の特定**: 大量の管理画面（Jenkins, Tomcat, Router等）のスクリーンショットを並べることで、初期設定のままのデバイスを瞬時に特定できます。
    
- **サブドメイン列挙との組み合わせ**: `subfinder` や `assetfinder` の出力をパイプで渡し、一気に可視化します。
    
    
    
    ```bash
    cat subdomains.txt | gowitness file -f - --threads 20
    ```
    
- **WAFの回避**: 大量のリクエストを送るため、`--delay` を調整するか、プロキシ（`--proxy`）を挟んでソースIPを分散させることを検討してください。
    
- **Chromeの依存**: 実行環境に Google Chrome または Chromium がインストールされている必要があります。ヘッドレス環境（Docker等）では `CHROME_PATH` 環境変数の設定が必要になる場合があります。
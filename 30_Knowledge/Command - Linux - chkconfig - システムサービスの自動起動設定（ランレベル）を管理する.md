---
# --- YAML Frontmatter ---
created: '2026-02-22'
modified: '2026-02-22'
environment: [OS/RHEL_Legacy, OS/CentOS6, OS/Amazon_Linux_1]
vulnearability: [Persistence, Defense_Evasion]
knowledge_category: Command
tags:
  - 'chkconfig'
  - 'sysv-init'
  - 'service-management'
  - 'persistence'
  - 'knowledge_base'
---

# Command - Linux - chkconfig - システムサービスの自動起動設定（ランレベル）を管理する

## 概要
`chkconfig` は、SysV init 形式のシステムにおいて、システムサービス（デーモン）がどのランレベル（Runlevel）で自動起動するかを照会、更新するためのツールです。`/etc/rc.d/init.d` にあるスクリプトのシンボリックリンクを管理することで、OS起動時の挙動を制御します。
(出自: `chkconfig` パッケージに含まれる。主に Red Hat 系の古いディストリビューションで使用)



## 類似コマンドと差異
| 類似コマンド | 差異 / 使い分けのポイント | 推奨 |
| :--- | :--- | :--- |
| `systemctl` | systemd 環境（現代の主流）における標準。サービスの起動・停止・自動起動設定をすべて統合。 | **現代の標準** |
| `service` | サービスの「現在の状態（起動・停止・再起動）」を操作する。自動起動の設定は行わない。 | 状態の即時操作 |
| `update-rc.d` | Debian/Ubuntu 系の SysV init 環境で使用される、chkconfig と同等のツール。 | Debian系レガシー |
| `ntsysv` | chkconfig の設定を対話的な GUI（TUI）で行うツール。 | 対話的な一括設定 |

## よく連携されるコマンド
### シナリオ例: 新規サービスの登録と有効化 (運用視点)
* **目的**: 自作スクリプトをシステムサービスとして登録し、OS起動時に自動実行されるようにする。
* **連携コマンド**: `vi /etc/init.d/<service>`, `service`
* **解説**: スクリプト内に `chkconfig: <levels> <start_priority> <stop_priority>` というコメント行を記述し、登録します。
* **コマンド例**:
    ```bash
    # 1. スクリプトを登録
    sudo chkconfig --add my_service
    # 2. 自動起動をONにする
    sudo chkconfig my_service on
    # 3. 確認
    chkconfig --list my_service
    ```

## オプション説明
| オプション | 説明 |
| :--- | :--- |
| **表示・確認** | |
| ⭐️ `--list [<name>]` | すべて、または特定のサービスのランレベルごとの設定を表示する。 |
| **管理** | |
| ⭐️ `--add <name>` | 新しいサービスを管理対象に追加する。 |
| ⭐️ `--del <name>` | サービスを管理対象から削除する。 |
| ⭐️ `<name> on/off` | サービスをデフォルトのランレベル（通常2,3,4,5）で自動起動「有効/無効」にする。 |
| `--level <levels>` | 対象とするランレベルを指定する（例: `--level 35`）。 |

## よく使われるオプション組み合わせと業務シナリオ

### 1. インフラ構築・運用視点
* **タスク**: サーバ起動時に必ず Apache (httpd) が立ち上がるように設定。
* **組み合わせ**: `httpd on`
* **解説**: サーバの再起動後に手動でサービスを起動する手間を省き、可用性を維持します。
* **例**:
    ```bash
    # httpd の自動起動を有効化
    sudo chkconfig httpd on
    ```

### 2. ブルーチーム視点
* **タスク**: 侵害調査における「不審な自動起動サービス」の検知。
* **組み合わせ**: `--list`
* **解説**: 攻撃者が自身のバックドア（リバースシェル等）を OS 起動時に実行させるよう設定していないか、ランレベル 3（マルチユーザーモード）や 5（グラフィカルモード）に未知のサービスがないか調査します。
* **例**:
    ```bash
    # すべてのサービスの起動設定を確認し、"on" になっているものを精査
    chkconfig --list | grep ":on"
    ```

### 3. レッドチーム視点
* **タスク**: 永続化（Persistence）のためのサービス登録。
* **組み合わせ**: `--add` + `on`
* **解説**: 侵入後、`/etc/init.d/` に悪意あるスクリプトを設置し、`chkconfig` で有効化します。これにより、管理者が手動でプロセスを kill したりサーバを再起動したりしても、再びバックドアが起動します。
* **例**:
    ```bash
    # 自身のバックドアをサービスとして登録
    sudo cp /tmp/bd /etc/init.d/backdoor
    sudo chkconfig --add backdoor
    sudo chkconfig backdoor on
    ```

## エラーメッセージとトラブルシューティング

1.  **エラーメッセージ例 1**: `service <name> does not support chkconfig`
    * **考えられる原因**: `/etc/init.d/` 内のスクリプトに `chkconfig:` で始まるコメント行（ヘッダー情報）が含まれていない。
    * **解決策**: スクリプトの先頭付近に `# chkconfig: 2345 80 20` のような形式のコメントを追加する。

2.  **エラーメッセージ例 2**: `error reading information on service <name>: No such file or directory`
    * **考えられる原因**: 指定したサービススクリプトが `/etc/init.d/` に存在しない。
    * **解決策**: スクリプトの配置場所を確認する。

## 環境変数と設定ファイル

### 関連する設定ファイル
* **`/etc/init.d/`**: 各サービスの起動スクリプト本体が格納されるディレクトリ。
* **`/etc/rc.d/rc[0-6].d/`**: 各ランレベルで起動（S...）または停止（K...）するサービスのシンボリックリンクが配置される場所。`chkconfig` はここを操作します。

## セキュリティに関する考慮事項

### 脆弱性と悪用事例
* **悪用シナリオ**: 攻撃者が root 権限を得た後、既存の正規サービス（例: `sendmail` や `cups`）の起動スクリプトを改ざんし、正規の動作と並行して自身のペイロードを実行させる。

### GTFOBins における利用例
`chkconfig` 自体はバイナリとしての GTFOBins 登録はありませんが、**Persistence（永続化）** の手法として攻撃者にとって最重要コマンドの一つです。

### 対応策・緩和策 (ブルーチーム視点)
* **Prevention (予防)**: `/etc/init.d/` 配下のファイルおよび `/etc/rc.d/` 内のシンボリックリンクの書き込み権限を厳格に制限する。
* **Detection (検知)**: `chkconfig` コマンドの実行、および `/etc/rc.d/` 配下のファイル変更を `auditd` で監視する。

## 注意点・補足
* **ランレベルの役割**:
    * 0: 停止 (Halt)
    * 1: シングルユーザーモード
    * 3: マルチユーザーモード (CLI)
    * 5: マルチユーザーモード (GUI)
    * 6: 再起動 (Reboot)
* **systemd との互換性**: systemd が導入された OS で `chkconfig` を実行すると、内部的に `systemctl` へリダイレクトされたり、互換性のための警告が表示されたりします。
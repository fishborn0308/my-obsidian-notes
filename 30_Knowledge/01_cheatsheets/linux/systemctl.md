---
tags:
  - 'systemctl'
  - 'systemd'
  - 'service_management'
  - 'linux'
  - 'cheatsheet'
title: 'systemctl - systemdのシステムとサービスを管理する'
summary: 'systemd initシステムを制御・管理するための中心的なコマンド。サービスの起動・停止、自動起動設定、システムの状態確認などを統一的に行います。'
related:
  - 'service'
  - 'chkconfig'
  - 'shutdown'
  - 'journalctl'
---

# `systemctl` - systemdのシステムとサービスを管理する

## 概要

`systemctl` は、モダンなLinuxディストリビューションで標準となった**initシステム**である **`systemd`** を制御・管理するための中心的なコマンドです。サービスの起動・停止・自動起動設定、システムの再起動・シャットダウン、ユニットの状態確認など、システムの動作を管理するあらゆる操作を統一的なインターフェイスで提供します。

旧来の `service`, `chkconfig`, `update-rc.d`, `shutdown` といった複数のコマンドの役割を、`systemctl` 1つで担います。

(出自: `systemd` パッケージに含まれる)

## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 |
| :--- | :--- | :--- |
| ⭐ **`service`** | SysVinit時代のサービス管理コマンド。`systemctl` はサービスの依存関係解決や、より詳細な状態管理など、遥かに高機能です。 | `systemctl` |
| **`chkconfig` / `update-rc.d`** | SysVinit時代に、サービスの**自動起動**を設定するためのコマンドでした。この機能は `systemctl enable/disable` に完全に置き換えられました。 | `systemctl` |
| **`reboot` / `shutdown`** | システムの再起動やシャットダウンを行うコマンド。`systemctl` でも `systemctl reboot` のように同等の操作が可能です。 | `systemctl` |

## よく連携されるコマンド

`systemctl` は、サービスの状態を確認した後に、そのログを詳細に調査するために `journalctl` と密接に連携します。

### シナリオ例: サービス起動失敗の原因を調査する (インフラ構築・運用視点)

* **目的**: `systemctl start` で起動に失敗したサービスの、詳細なエラーログを確認する。
* **連携コマンド**: `journalctl`
* **解説**: `systemctl status <service>` を実行すると、サービスの現在の状態と共に、関連する直近のログが数行表示されます。さらに詳細なログが必要な場合は、`journalctl -u <service>` を実行して、そのサービスの全てのログを時系列で確認します。
* **コマンド例**:

    ```bash
    # 1. Nginxサービスのステータスを確認 (ここで 'failed' と表示されたと仮定)
    sudo systemctl status nginx.service

    # 2. nginx.service ユニットに絞って、詳細なログを確認する
    sudo journalctl -u nginx.service -e
    ```

## オプション説明 (`systemctl` のサブコマンド)

| オプション | 説明 |
| :--- | :--- |
| **サービス操作** | |
| ⭐ `start <unit>` | ユニットを**起動**します。 |
| ⭐ `stop <unit>` | ユニットを**停止**します。 |
| ⭐ `restart <unit>` | ユニットを**再起動**します。 |
| `reload <unit>` | サービスを停止せずに**設定を再読み込み**させます。 |
| **自動起動設定** | |
| ⭐ `enable <unit>` | ユニットの**自動起動を有効化**します。 |
| ⭐ `disable <unit>` | ユニットの**自動起動を無効化**します。 |
| **状態表示** | |
| ⭐ `status <unit>` | ユニットの**詳細な状態**（実行中か、ログの抜粋など）を表示します。 |
| `is-enabled <unit>` | ユニットが自動起動有効化されているかを確認します。 |
| `list-units` | 実行中のユニットを一覧表示します。 |
| **システム操作** | |
| `daemon-reload` | ユニットファイルを変更した後に、その変更を `systemd` に認識させるために実行します。 |

## よく使われるオプション組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: 新しいサービスをインストール後、即座に起動し、かつOS起動時からも自動で立ち上がるように設定する。
* **組み合わせ**: `sudo systemctl enable --now <service>`
* **解説**: サービスの自動起動を有効化 (`enable`) すると同時に、即座にサービスを起動 (`start`) する、非常に便利な使い方です。
* **例**:

    ```bash
    # Nginxをインストール後、起動と自動起動設定を一度に行う
    sudo systemctl enable --now nginx
    ```

### 2. ブルーチーム視点

* **タスク**: システムに不審なサービス（バックドア）が永続化のために設置されていないか監査する。
* **組み合わせ**: `systemctl list-unit-files --type=service`
* **解説**: インストールされている全てのサービスユニットの状態を一覧表示します。`enabled` 状態になっているサービスの中に、標準的でない、あるいは明らかに不審な名前のサービスがないかを確認します。
* **例**:

    ```bash
    # 全てのサービスユニットをリストし、grepで不審な名前がないか確認
    systemctl list-unit-files --type=service | grep -i "backdoor\|revshell"
    ```

### 3. レッドチーム視点

* **タスク**: **モダンなLinuxにおける最も強力な永続化**を確立する。
* **組み合わせ**: (ユニットファイル作成) → `sudo systemctl enable --now <backdoor.service>`
* **解説**: 攻撃者がroot権限を奪取した後、`/etc/systemd/system/` ディレクトリに、リバースシェルなどを起動する悪意のあるサービスユニットファイルを作成します。そして `systemctl enable --now` を実行することで、そのバックドアを即座に起動させ、さらにOS起動時に自動実行されるように仕掛けます。
* **例**:

    ```bash
    # /etc/systemd/system/revshell.service というファイルを作成後...
    # バックドアサービスを有効化・起動する
    sudo systemctl enable --now revshell.service
    ```

## エラーメッセージとトラブルシューティング

* 一般的なエラーは [Linux共通のトラブルシューティング](./troubleshooting_common_errors.md) を参照。

1. **エラーメッセージ例 1**: `Failed to start <service_name>.service: Unit <service_name>.service not found.`
    * **考えられる原因**: 指定したサービス名のユニットファイルが存在しません。
    * **解決策**: サービス名が正しいか、パッケージが正しくインストールされているかを確認してください。

2. **現象**: **ユニットファイルを編集したが、変更が反映されない。**
    * **考えられる原因**: ユニットファイル (`.service`など) を手動で作成・編集した後に、`systemd` に変更を認識させるためのコマンドを実行していません。
    * **解決策**: 必ず `sudo systemctl daemon-reload` を実行してください。

## 環境変数と設定ファイル

* 一般的な環境変数・設定ファイルは [Linux共通の環境変数・設定ファイル](./environment_and_config.md) を参照。

### 関連する設定ファイル

* ⭐ **`/etc/systemd/system/`**: 管理者が作成・カスタマイズしたユニットファイルが置かれる、最優先のディレクトリ。
* **`/usr/lib/systemd/system/`**: パッケージインストール時に、デフォルトのユニットファイルが置かれるディレクトリ。

## セキュリティに関する考慮事項

### 脆弱性と悪用事例

* **脆弱性**: `systemd` 自体の脆弱性よりも、**ユニットファイルの設定不備**や、**書き込み可能なユニットファイルディレクトリ**がリスクとなります。
* **悪用シナリオ**: レッドチームのシナリオで述べた通り、`systemd` サービスは攻撃者が永続化を確立するための最も一般的で強力な手法です。

### GTFOBins / LOLBAS における利用例

* **機能**: **Privilege Escalation**
* **解説**: もし一般ユーザーに、自身が書き込み可能なパスにあるユニットファイルを `sudo systemctl start` で実行できる権限が与えられている場合、そのユニットファイルの `ExecStart=` に悪意のあるコマンドを記述することで、任意のコマンドをroot権限で実行できます。
* **参照**: `https://gtfobins.github.io/gtfobins/systemctl/`

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: ユニットファイルに `User=`, `Group=`, `ProtectSystem=`, `PrivateTmp=` といったサンドボックス化オプションを設定し、サービスの権限を最小化する。
* **Detection (検知)**: FIMツールで `/etc/systemd/system/` ディレクトリ配下のファイルの作成・変更を監視し、意図しないサービスの追加を検知する。

## 注意点・補足

* **`stop` vs `disable`**: `stop` はサービスを**今すぐ停止**させるだけです。`disable` は**次回の起動時から自動で立ち上がらないようにする**設定です。両者は目的が異なります。

---
[インデックスに戻る](../linux_index.md)

---
# --- YAML Frontmatter ---
created: 2026-02-22 16:34
modified: 2026-02-23 09:52
environment: [OS/Linux, Kernel/Audit]
vulnearability: [Privilege_Escalation, Data_Exfiltration, Post-Exploitation]
knowledge_category: Command
tags:
  - ausearch
  - auditd
  - forensics
  - blue_team
  - knowledge_base
---

# Command - Linux - ausearch - 監査ログ（audit log）の検索・フィルタリング

## 概要

`ausearch` は、`auditd` によって生成された `/var/log/audit/audit.log` を効率的に検索するための専用ツールです。生のログは非常に読みづらいため、このコマンドを使って「特定のユーザー」「特定のファイル」「特定の時間帯」などの条件でフィルタリングし、人間が読める形式に変換します。

(出自: `audit` パッケージに含まれる)

## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 |
| :--- | :--- | :--- |
| `grep` | 文字列ベースの単純検索。auditログの構造（タイムスタンプのデコード等）を理解しない。 | 簡易検索 |
| `ausearch` | auditログの構造を理解しており、メッセージタイプやUIDでの検索、IDから名前への変換が可能。 | 詳細なフォレンジック |
| `aureport` | ログの集計とサマリーレポートを作成する。傾向把握に向く。 | 統計・レポート作成 |

## よく連携されるコマンド

### シナリオ例: ログの可読化と解析 (ブルーチーム視点)

* **目的**: UID（数値）や16進数でエンコードされたコマンド引数を、人間が読めるテキストに変換して表示する。
* **連携コマンド**: `less`, `grep`
* **解説**: `-i` オプションを使うことで、UIDをユーザー名に、GIDをグループ名に、プロトコル番号をプロトコル名に自動変換します。
* **コマンド例**:
    ```bash
    # 本日のすべての失敗したイベントを人間が読める形式で表示
    sudo ausearch -m ALL -sv no -ts today -i | less
    ```

## オプション説明

| オプション | 説明 |
| :--- | :--- |
| **検索フィルタ** | |
| ⭐️ `-i`, `--interpret` | 数値をテキスト（ユーザー名等）に変換して表示する。 |
| ⭐️ `-k <key>` | `auditctl` で設定したキー名（タグ）で検索する。 |
| ⭐️ `-m <type>` | メッセージタイプ（USER_AUTH, SYSCALL, EXECVE等）で検索する。 |
| `-p <pid>` | プロセスIDで検索する。 |
| `-ui <uid>` | ユーザーIDで検索する。 |
| `-ts <date> [<time>]` | 開始時間を指定。`today`, `yesterday` や `02/22/2026` 形式が使用可能。 |
| `-te <date> [<time>]` | 終了時間を指定。 |
| **実行結果フィルタ** | |
| `-sv <yes|no>` | 成功(yes)または失敗(no)でフィルタリングする。 |

## よく使われるオプション組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: 特定の重要ファイル（例: `/etc/hosts`）への変更履歴の確認。
* **組み合わせ**: `-f <path> -i`
* **解説**: ファイルパスを直接指定して、誰がそのファイルにアクセスまたは変更したかを追跡します。
* **例**:
    ```bash
    # /etc/hosts へのアクセス履歴を時系列で表示
    sudo ausearch -f /etc/hosts -i
    ```

### 2. ブルーチーム視点

* **タスク**: 特定の「キー」に関連する不審な挙動の調査。
* **組み合わせ**: `-k <key_name> -ts <time>`
* **解説**: `auditctl` で仕込んでおいた監視ルール（例: `ssh_config_change`）に合致するログを抽出します。
* **例**:
    ```bash
    # 'priv_esc' というキーで記録された、過去1時間以内のログを表示
    sudo ausearch -k priv_esc -ts recent -i
    ```

### 3. レッドチーム視点

* **タスク**: 自身の活動がどのような証拠として残ったかのセルフチェック。
* **組み合わせ**: `-m EXECVE -ui <my_uid>`
* **解説**: 自分が実行したコマンドが、引数を含めてどのように記録されているかを確認します。検知回避（Evasion）の戦略を練るために使用します。
* **例**:
    ```bash
    # 自分のUIDが実行した全コマンドのログを確認
    sudo ausearch -m EXECVE -ui $(id -u) -i
    ```

## 実務ログ例 (ausearch -i の出力)

```text
type=SYSCALL msg=audit(02/22/2026 15:30:01.123:456): arch=x86_64 syscall=openat success=yes exit=3 a0=ffffff9c a1=7ffd55... items=1 ppid=1234 pid=5678 auid=admin uid=root gid=root euid=root suid=root fsuid=root tty=pts0 ses=5 comm="cat" exe="/usr/bin/cat" key="shadow_access"
type=PATH msg=audit(02/22/2026 15:30:01.123:456): item=0 name="/etc/shadow" inode=135 dev=fd:00 mode=0100600 ouid=root ogid=shadow
````

> **解説**: `auid=admin`（ログイン時はadmin）が、`uid=root` 権限で `/usr/bin/cat` を使い `/etc/shadow` を開いたことがわかります。`key="shadow_access"` により検索が容易になります。

## エラーメッセージとトラブルシューティング

1. **エラーメッセージ例 1**: `<no matches>`
    
    - **考えられる原因**: 指定した条件に合うログが存在しない、または検索対象のログファイルが古い。
    - **解決策**: 時間指定 (`-ts`) を広げるか、`-m ALL` で全メッセージタイプを対象にしてみる。
        
2. **エラーメッセージ例 2**: `Error opening /var/log/audit/audit.log (Permission denied)`
    
    - **考えられる原因**: 一般ユーザー権限で実行している。
    - **解決策**: `sudo` を付与して実行する。

## 環境変数と設定ファイル

### 関連する設定ファイル

- **`/etc/audit/auditd.conf`**: ログファイルの場所や最大サイズ。`ausearch` はここを参照してログを探します。

## セキュリティに関する考慮事項

### 脆弱性と悪用事例

- **悪用シナリオ**: 攻撃者が root 権限を得た場合、`ausearch` で自身の活動ログを特定し、該当箇所を物理的に削除したり、タイムスタンプを偽装したりする可能性があります。

### GTFOBins / LOLBAS における利用例

`ausearch` 自体は登録されていませんが、root 権限で実行できる場合、システム上のあらゆるユーザーの活動、機密ファイルへのアクセス履歴、場合によっては環境変数に含まれる認証情報などを盗み見るために悪用される可能性があります。

### 対応策・緩和策 (ブルーチーム視点)

- **Detection (検知)**: 攻撃者が `ausearch` を使って「何がバレているか」を確認する行為自体を監視対象にする（`auditctl -w /usr/sbin/ausearch -p x -k forensic_tool_usage`）。

## 注意点・補足

- **ログのローテーション**: `auditd` の設定で古いログが消去される設定になっている場合、過去のインシデントを `ausearch` で追えなくなるため、ログの外部転送が重要です。
- **パフォーマンス**: 数GBに及ぶ巨大なログファイルを検索する場合、非常に時間がかかることがあります。時間指定 (`-ts`) を活用して範囲を絞ることが鉄則です。
    

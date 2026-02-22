---
# --- YAML Frontmatter ---
created: '2026-02-22'
modified: '2026-02-22'
environment: [OS/Linux, FS/ext4, FS/xfs]
vulnearability: [Persistence, Defense_Evasion]
knowledge_category: Command
tags:
  - 'chattr'
  - 'lsattr'
  - 'e2fsprogs'
  - 'hardening'
  - 'knowledge_base'
---

# Command - Linux - chattr - ファイルシステムの属性を変更する (不変属性など)

## 概要
`chattr` は、Linuxのファイルシステム（主にext2/3/4）において、通常のファイル権限（所有者・グループ・パーミッション）とは別に用意されている「属性」を変更するためのコマンドです。最大の特徴は、**rootユーザーであっても属性を解除しない限り削除や変更ができない**設定（不変属性）が可能な点にあります。
(出自: `e2fsprogs` パッケージに含まれる)



## 類似コマンドと差異
| 類似コマンド | 差異 / 使い分けのポイント | 推奨 |
| :--- | :--- | :--- |
| `chmod` | 読み書き実行（rwx）の「権限」を制御。rootなら無視できる。 | 通常のアクセス制御 |
| `chown` | ファイルの所有者を変更する。 | 所有権の管理 |
| `chattr` | ファイルシステムレベルの「性質」を制御。rootですら属性に従う必要がある。 | システム保護・改ざん防止 |

## よく連携されるコマンド
### シナリオ例: 属性の確認と設定 (運用視点)
* **目的**: 設定した属性が正しく反映されているかを確認する。
* **連携コマンド**: `lsattr`
* **解説**: `ls` コマンドでは `chattr` で設定した属性は見えません。必ず `lsattr` を使用します。
* **コマンド例**:
    ```bash
    # 不変属性を付与し、確認する
    sudo chattr +i /etc/passwd
    lsattr /etc/passwd
    ```

## オプション説明
| 演算子/フラグ | 説明 |
| :--- | :--- |
| **演算子** | |
| `+` | 属性を付与する。 |
| `-` | 属性を解除する。 |
| `=` | 指定した属性のみにする。 |
| **主要属性（フラグ）** | |
| ⭐️ `i` (immutable) | **不変属性**。変更、削除、名前変更、リンク作成が一切不可になる（root含む）。 |
| ⭐️ `a` (append-only) | **追記専用属性**。既存部分の変更・削除はできないが、追記のみ可能。ログファイル等に有効。 |
| `A` (no atime) | ファイルアクセス時にアクセス時刻（atime）を更新しない。パフォーマンス向上に。 |
| `e` (extent) | ファイルがエクステントを使用してディスク上のブロックを管理していることを示す（通常自動付与）。 |

## よく使われるオプション組み合わせと業務シナリオ

### 1. インフラ構築・運用視点
* **タスク**: `/etc/resolv.conf` が勝手に書き換えられるのを防ぐ。
* **組み合わせ**: `+i`
* **解説**: DHCPクライアントやNetworkManagerによって意図せずネームサーバ設定が上書きされるのを防ぎます。
* **例**:
    ```bash
    # resolv.conf を固定する
    sudo chattr +i /etc/resolv.conf
    ```

### 2. ブルーチーム視点
* **タスク**: 重要システムファイル（`/etc/shadow`, `/etc/sudoers`等）の要塞化。
* **組み合わせ**: `+i`
* **解説**: 万が一侵入され root 権限を奪取されても、攻撃者が即座にユーザーを追加したり権限設定を変更したりするのを物理的に阻害し、時間を稼ぎます。
* **例**:
    ```bash
    # sudoers ファイルを保護
    sudo chattr +i /etc/sudoers
    ```

### 3. レッドチーム視点
* **タスク**: 削除困難なバックドア（Webシェル等）の設置による永続化（Persistence）。
* **組み合わせ**: `+i`
* **解説**: 侵入後、Webディレクトリに置いた Web シェルに `+i` を付与します。管理者が Web シェルを発見して `rm -rf` を実行しても「Operation not permitted」となり、削除に失敗します。属性の存在を知らない管理者を混乱させることができます。
* **例**:
    ```bash
    # Webシェルを削除不能にする
    sudo chattr +i /var/www/html/shell.php
    ```

## 実務ログ例 (不変属性の挙動)
```bash
# 1. ファイルを作成
touch secure_file
# 2. 不変属性を付与
sudo chattr +i secure_file
# 3. root権限で削除を試行
sudo rm secure_file
# 出力: rm: cannot remove 'secure_file': Operation not permitted
# 4. 書き込みを試行
sudo echo "hack" > secure_file
# 出力: bash: secure_file: Permission denied
# 5. 属性を確認
lsattr secure_file
# 出力: ----i---------e------- secure_file
````

## エラーメッセージとトラブルシューティング

1. **エラーメッセージ例 1**: `chattr: Operation not supported while reading flags...`
    
    - **考えられる原因**: 指定したファイルシステム（例: NFSや一部の仮想ファイルシステム）が `chattr` 属性をサポートしていない。
        
    - **解決策**: ext4 や xfs などのローカルファイルシステム上で実行しているか確認する。
        
2. **エラーメッセージ例 2**: `chattr: Permission denied while setting flags...`
    
    - **考えられる原因**: rootユーザーであっても、`CAP_LINUX_IMMUTABLE` ケーパビリティが欠落している環境（一部のコンテナ環境等）では設定できない。
        
    - **解決策**: 特権コンテナとして実行するか、ホストOS側で操作する。
        

## 環境変数と設定ファイル

- `chattr` は環境変数や設定ファイルを持ちませんが、ファイルシステムのマウントオプション（`ro` マウント等）が優先される場合があります。
    

## セキュリティに関する考慮事項

### 脆弱性と悪用事例

- **悪用シナリオ**: 攻撃者がカーネルモジュールをロードし、`chattr` や `lsattr` が属性を正しく表示・操作できないようにシステムコールをフックする（Rootkitの手法）。
    

### GTFOBins における利用例

`chattr` 自体はバイナリとしての GTFOBins 登録はありませんが、**Defense Evasion（防御回避）** において最重要コマンドの一つです。

### 対応策・緩和策 (ブルーチーム視点)

- **Prevention (予防)**: 重要な属性の変更を `auditd` で監視する。
    
- **Detection (検知)**: `ls -l` では正常に見えるのに削除できないファイルがある場合、即座に `lsattr` で属性をチェックする。
    

## 注意点・補足

- **バックアップ時の注意**: `tar` や `rsync` でバックアップをとる際、デフォルトでは `chattr` の属性は保持されません。復元後に再度属性を付与する必要があります。
    
- **LVM/RAID**: 物理的なディスク構成に関わらず、ファイルシステム層での設定となります。
    
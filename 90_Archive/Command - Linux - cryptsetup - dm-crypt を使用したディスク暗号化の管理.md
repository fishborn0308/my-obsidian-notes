---
# --- YAML Frontmatter ---
created: 2026-02-22 21:12
modified: 2026-02-23 09:52
environment: [OS/Linux, Security/Encryption]
vulnearability: [Data_Theft, Unauthorized_Access]
knowledge_category: Command
tags:
  - cryptsetup
  - luks
  - encryption
  - dm-crypt
  - knowledge_base
---

# Command - Linux - cryptsetup - dm-crypt を使用したディスク暗号化の管理

## 概要

`cryptsetup` は、カーネルの `dm-crypt` モジュールを利用して、ディスクパーティションやファイル全体を暗号化するためのツールです。特に **LUKS** 形式は、複数のパスフレーズ（スロット）管理や暗号化標準の定義をサポートしており、Linux のフルディスク暗号化 (FDE) の基盤となっています。

(出自: `cryptsetup` パッケージに含まれる)

## 類似・関連コマンド

| コマンド | 役割 / 使い分け |
| :--- | :--- |
| `fdisk / parted` | パーティションを作成する（暗号化の「器」を作る）。 |
| `mkfs` | 暗号化を解除（Open）した後の仮想デバイスにファイルシステムを作成する。 |
| `mount` | 復号されたデバイスをディレクトリに紐付ける。 |
| `veracrypt` | クロスプラットフォーム対応の暗号化ツール。GUIが強力。 |

## よく連携されるコマンド

### シナリオ例: 暗号化ボリュームの新規作成とマウント (運用視点)

* **目的**: 新しいパーティションを暗号化し、安全にデータ保存できるようにする。
* **解説**: 物理デバイスを LUKS 形式で初期化し、名前を付けて仮想デバイスとして開き、フォーマットします。
* **コマンド例**:
    ```bash
    # 1. パーティションをLUKSで初期化 (警告が出るので注意)
    sudo cryptsetup luksFormat /dev/sdb1
    # 2. 暗号化ボリュームを開き、'my_secure_data' という名前でマップ
    sudo cryptsetup open /dev/sdb1 my_secure_data
    # 3. マップされたデバイスをフォーマット
    sudo mkfs.ext4 /dev/mapper/my_secure_data
    # 4. マウント
    sudo mount /dev/mapper/my_secure_data /mnt/secure
    ```

## 主要なサブコマンドとオプション

| サブコマンド | 説明 |
| :--- | :--- |
| ⭐️ `luksFormat <dev>` | デバイスを LUKS 形式で初期化する（中身は消去される）。 |
| ⭐️ `open <dev> <name>` | 暗号化を解除し、`/dev/mapper/<name>` として利用可能にする。 |
| ⭐️ `close <name>` | ボリュームを閉じ、仮想デバイスを削除する。 |
| `status <name>` | 暗号化のアルゴリズムやキーサイズ、セクタ数などの情報を表示。 |
| `luksAddKey <dev>` | 新しいパスフレーズを追加する（最大8スロット）。 |
| `luksHeaderBackup` | LUKS ヘッダーのバックアップを作成する（ヘッダー破損対策に必須）。 |

## よく使われるシナリオと業務

### 1. インフラ構築・運用視点

* **タスク**: サーバ再起動時の自動復号設定（キースロット管理）。
* **組み合わせ**: `/etc/crypttab`, `/etc/fstab`
* **解説**: パスフレーズの代わりにキーファイルを読み込ませることで、手動入力なしで起動時に自動マウントさせます。
* **例**:
    ```bash
    # キーファイルを生成してスロットに追加
    dd if=/dev/urandom out=/root/keyfile bs=1024 count=4
    sudo chmod 400 /root/keyfile
    sudo cryptsetup luksAddKey /dev/sdb1 /root/keyfile
    ```

### 2. ブルーチーム視点

* **タスク**: 押収したディスクイメージの解析。
* **組み合わせ**: `mount -o ro` (読み取り専用マウント)
* **解説**: 容疑者のディスクから取得したイメージファイルを `cryptsetup` で開き、証拠を汚染させないよう読み取り専用で解析します。
* **例**:
    ```bash
    # イメージファイルをループバックデバイスとして開き、復号
    sudo cryptsetup open --type luks forensic_image.img evidence_vol
    ```

### 3. レッドチーム視点

* **タスク**: パスフレーズやマスターキーの窃取。
* **組み合わせ**: `memory dump` または `keylogger`
* **解説**: LUKS 自体は極めて堅牢ですが、メモリ上には「復号済みのマスターキー」が存在します。管理者権限を得た後、メモリダンプからキーを抽出する手法（例: `find_aes` 等）を検討します。

## エラーメッセージとトラブルシューティング

1.  **エラーメッセージ例 1**: `Device /dev/sdb1 is not a valid LUKS device.`
    * **考えられる原因**: デバイスが LUKS でフォーマットされていない、またはヘッダーが破損している。
    * **解決策**: `isLuks` サブコマンドで確認し、破損ならバックアップから復元する。

2.  **エラーメッセージ例 2**: `No key available with this passphrase.`
    * **考えられる原因**: パスフレーズが間違っている、またはキーボード配列の設定が異なっている。
    * **解決策**: 別のキースロットを試すか、入力時の言語設定を確認する。

## セキュリティに関する考慮事項

### 脆弱性とリスク

* **LUKSヘッダーの消失**: ヘッダー（先頭数MB）が破損または上書きされると、**パスフレーズが正しくてもデータを二度と復元できなくなります**。必ず `luksHeaderBackup` をとることが鉄則です。
* **Evil Maid Attack**: 攻撃者が物理的に端末にアクセスできる場合、ブートローダーを改ざんしてパスフレーズを盗み取る可能性があります。

### 緩和策 (ブルーチーム視点)

* **Prevention (予防)**: TPM (Trusted Platform Module) と連携させ、ハードウェア構成が変わった場合に復号を拒否するように構成する。
* **Detection (検知)**: `/etc/crypttab` の予期せぬ変更や、不審なタイミングでの `luksDump` 実行を監視する。

## 注意点・補足

* **ワイプの推奨**: `luksFormat` を行う前に、デバイス全体を `shred` や `dd` でランダムデータで埋めておくと、どこにデータがあるかの判別（暗号化領域の境界特定）が難しくなり、より安全です。
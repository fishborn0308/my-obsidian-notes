---
tags:
  - sshd_config
  - ssh
  - security
  - server_hardening
  - ssh_config
  - fail2ban
  - systemctl
created: 2025-06-29 15:02
modified: 2026-01-18 15:02
environment:
  - OS/Linux
vulnearability: []
knowledge_category: Command
---

# Command  - Linux - sshd_config - SSHサーバーの設定ファイル

## 概要

`/etc/ssh/sshd_config` は、SSHデーモン (`sshd`) の動作を制御するためのメイン設定ファイルです。このファイル内のディレクティブ（設定項目）を変更することで、SSHサーバーがどのポートで待ち受けるか、どの認証方式を許可するか、どのユーザーのログインを許可/拒否するかといったセキュリティに関わる極めて重要な挙動を定義します。

**サーバーのセキュリティ強化（ハーデニング）において、このファイルを適切に設定することは最初の、そして最も重要なステップの一つです。**

(出自: `openssh-server` パッケージに含まれる)

## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 |
| :--- | :--- |:---|
| ⭐ **`ssh_config`** | **クライアント側** (`ssh`) の設定ファイルです。`sshd_config` は**サーバー側** (`sshd`) の設定ファイルであり、役割が全く異なります。 | - |

## よく連携されるコマンド

`sshd_config` ファイルを編集した後は、構文をテストする `sshd -t` と、設定を反映させる `systemctl restart sshd` を必ず連携して使用します。

### シナリオ例: SSHサーバーの設定を変更し、安全に適用する (インフラ構築・運用視点)

* **目的**: SSHの待ち受けポートを22番から2222番に変更し、設定を反映させる。
* **連携コマンド**: `vi` (またはエディタ), `sshd`, `systemctl`
* **解説**: まず設定ファイルを編集します。次に `sshd -t` で構文エラーがないかを必ず確認します。エラーがなければ、サービスを再起動して変更を適用します。構文チェックを怠ると、sshdが起動しなくなりサーバーにログインできなくなる可能性があります。
* **コマンド例**:

    ```bash
    # 1. 設定ファイルを編集
    sudo vi /etc/ssh/sshd_config
    # -> Port 22 を Port 2222 に変更

    # 2. 構文チェック (何も出力されなければ成功)
    sudo sshd -t

    # 3. SSHサービスを再起動して変更を適用
    sudo systemctl restart sshd
    ```

## オプション説明 (主要なディレクティブ)

| オプション | 説明 |
| :--- | :--- |
| **ネットワーク設定** | |
| ⭐ `Port` | SSHが待ち受けるポート番号。デフォルトは `22`。 |
| `ListenAddress` | SSHが待ち受けるIPアドレスを指定します。 |
| **認証 (Authentication)** | |
| ⭐ `PermitRootLogin` | `root`ユーザーの直接SSHログインを許可するか (`yes`), 公開鍵のみか (`prohibit-password`), 禁止するか (`no`)。**`no` が強く推奨されます**。 |
| ⭐ `PasswordAuthentication`| パスワードによる認証を許可するか (`yes`/`no`)。**`no` が強く推奨されます**。 |
| ⭐ `PubkeyAuthentication`| 公開鍵認証を許可するか (`yes`/`no`)。`yes` が推奨されます。 |
| `PermitEmptyPasswords`| 空のパスワードでのログインを許可するか (`yes`/`no`)。`no` であるべきです。 |
| `MaxAuthTries` | 1接続あたりの最大認証試行回数を制限します。 |
| **アクセス制御** | |
| `AllowUsers` | SSHログインを許可するユーザーをスペース区切りで指定します。 |
| `AllowGroups` | SSHログインを許可するグループを指定します。 |
| **ロギングとその他** | |
| `LogLevel` | ログに記録する情報の詳細度。`VERBOSE` にすると監査に役立ちます。 |
| `X11Forwarding`| X11フォワーディングを許可するか (`yes`/`no`)。GUIを使わないサーバーでは `no` にすべきです。 |

## よく使われるオプション組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: **SSHサーバーのセキュリティ強化（ハーデニング）**。
* **組み合わせ**: `PermitRootLogin no`, `PasswordAuthentication no`
* **解説**: `root`での直接ログインを禁止し、パスワード認証を無効化して公開鍵認証のみを許可することは、ブルートフォース攻撃に対する最も効果的な防御策であり、現代のサーバー構築における標準的なプラクティスです。
* **例 (`/etc/ssh/sshd_config` の設定)**:

    ```sshd_config
    Port 2222
    PermitRootLogin no
    PasswordAuthentication no
    PubkeyAuthentication yes
    AllowUsers adminuser devuser
    ```

### 2. ブルーチーム視点

* **タスク**: `sshd_config` の設定を監査し、セキュリティリスクがないか確認する。
* **組み合わせ**: `cat /etc/ssh/sshd_config`
* **解説**: インシデント調査や定期監査でこのファイルを確認し、`PermitRootLogin yes` や `PasswordAuthentication yes` のような危険な設定が有効になっていないか、`AllowUsers` に不審なユーザーが追加されていないかなどをチェックします。
* **例**:

    ```bash
    # 現在の設定ファイルから、主要なセキュリティ項目を抜き出して確認
    sudo grep -E "^Port|^PermitRootLogin|^PasswordAuthentication|^AllowUsers" /etc/ssh/sshd_config
    ```

### 3. レッドチーム視点

* **タスク**: **侵入後の偵察**と**永続化**。
* **組み合わせ**: `cat /etc/ssh/sshd_config`
* **解説**: 攻撃者はシステム侵入後、まずこのファイルを読み取り、サーバーのセキュリティ設定レベルを把握します。`PasswordAuthentication yes` であればブルートフォース攻撃の対象となり得ます。root権限を奪取した後は、`AllowUsers` に自身のバックドア用アカウントを追加するなどの改ざんを行い、永続化を図ります。
* **例**:

    ```bash
    # 設定ファイルから AllowUsers や AllowGroups を探し、攻撃対象となりうるユーザー/グループを特定
    cat /etc/ssh/sshd_config | grep -E "AllowUsers|AllowGroups"
    ```

## エラーメッセージとトラブルシューティング

* 一般的なエラーは [Linux共通のトラブルシューティング](OS%20%20-%20Linux%20-%20troubleshooting_common_errors%20-%20Linux共通エラー対応ガイド.md) を参照。

1. **現象**: **`sshd_config` を変更してサービスを再起動したら、SSH接続できなくなった。**
    * **考えられる原因**: ファイルに構文エラーがあるか、`Port` や `ListenAddress`, `AllowUsers` などの設定で自分自身を締め出す設定をしてしまった。
    * **解決策**: 変更を加える前に、必ず `sudo sshd -t` を実行して構文チェックを行ってください。もし締め出された場合は、物理コンソールやクラウドのシリアルコンソールからログインして修正する必要があります。

## 環境変数と設定ファイル

* `sshd_config` の動作に直接影響する環境変数は通常ありません。
* 一般的な環境変数・設定ファイルは [Linux共通の環境変数・設定ファイル](./environment_and_config.md) を参照。

## セキュリティに関する考慮事項

### 脆弱性と悪用事例

* **脆弱性**: `sshd` 自体の脆弱性（例: [Terrapin Attack - CVE-2023-48795](https://terrapin-attack.com/)）と、**設定ファイルの不備**がリスクとなります。
* **悪用シナリオ**: `PasswordAuthentication yes` と `PermitRootLogin yes` の両方が有効になっているサーバーは、`root` アカウントに対するブルートフォース攻撃の格好の標的となります。

### GTFOBins / LOLBAS における利用例

`sshd` はGTFOBinsにリストされています。

* **機能**: **Privilege Escalation**
* **解説**: もし `sshd_config` が一般ユーザーによって書き込み可能であるといった重大な設定ミスがある場合、攻撃者は `UsePrivilegedSeparation no` などの危険なオプションを設定したり、`AuthorizedKeysFile` のパスを変更したりして、権限昇格を試みることができます。
* **参照**: `https://gtfobins.github.io/gtfobins/sshd/`

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: このチートシートで推奨されているハーデニング設定を適用する。SSHサーバーのソフトウェアを常に最新の状態に保つ。`Fail2ban` を導入し、ブルートフォース攻撃を自動的にブロックする。
* **Detection (検知)**: FIMツールで `/etc/ssh/sshd_config` の変更を監視する。`sshd` のログをSIEMに集約し、大量のログイン失敗や、予期せぬIPからのログイン成功を監視する。

## 注意点・補足

* **`Match` ブロック**: `sshd_config` の末尾では、`Match User <username>` や `Match Address <ip_address>` のように、特定の条件に一致する場合にのみ設定を上書きする `Match` ブロックを記述できます。

---


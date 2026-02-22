---
# --- YAML Frontmatter ---
created: '2026-02-22'
modified: '2026-02-22'
environment: [OS/RHEL8+, OS/CentOS_Stream, OS/Rocky_Linux, OS/AlmaLinux]
vulnearability: [Broken_Authentication, Privilege_Escalation, Account_Lockout_Evasion]
knowledge_category: Command
tags:
  - 'authselect'
  - 'pam'
  - 'nsswitch'
  - 'identity-management'
  - 'knowledge_base'
---

# Command - Linux - authselect - システム認証（PAM/NSS）のプロファイル管理

## 概要
`authselect` は、`/etc/pam.d`（認証モジュール）および `/etc/nsswitch.conf`（名前解決順序）の設定を、あらかじめ定義された「プロファイル」に基づいて安全に一括管理するツールです。手動で複雑な設定ファイルを編集する際のリスクを低減します。
(出自: RHEL 8 以降の標準認証設定ツール)

## 類似コマンドと差異
| 類似コマンド | 差異 / 使い分けのポイント | 推奨 |
| :--- | :--- | :--- |
| `authconfig` | 旧世代のツール。RHEL 8 以降では非推奨となり `authselect` に置き換わった。 | 旧OS環境のみ |
| `pam-auth-update` | Debian/Ubuntu 系の環境で PAM 設定を管理するためのツール。 | Debian系環境 |
| `sssd` | 実際の認証処理（LDAP/AD連携等）を行うデーモン。`authselect` はこれを使う「設定」を行う。 | 認証バックエンド |

## よく連携されるコマンド
### シナリオ例: SSSD を利用したディレクトリ連携の有効化 (運用視点)
* **目的**: システムを LDAP や Active Directory 連携させるため、SSSD プロファイルを選択する。
* **連携コマンド**: `systemctl`, `sssd`
* **解説**: `authselect` でプロファイルを切り替えた後、実際の認証デーモンを起動・有効化します。
* **コマンド例**:
    ```bash
    # sssd プロファイルを選択し、ホームディレクトリ自動作成機能を有効化
    sudo authselect select sssd with-mkhomedir --force
    # 関連サービスの起動
    sudo systemctl enable --now sssd
    ```

## オプション説明
| オプション | 説明 |
| :--- | :--- |
| **表示・確認** | |
| ⭐️ `list` | 利用可能なプロファイルの一覧を表示する。 |
| ⭐️ `current` | 現在適用されているプロファイルとオプションを表示する。 |
| `check` | 現在の設定が `authselect` の管理下にあるか（手動変更されていないか）を確認する。 |
| **設定変更** | |
| `select <profile>` | 指定したプロファイルを適用する（例: sssd, winbind）。 |
| `--with-<feature>` | プロファイルに機能を追加する（例: `with-faillock`, `with-mkhomedir`）。 |
| `--force` | 手動で変更された既存の設定ファイルを上書きして適用する。 |
| `apply-changes` | カスタムプロファイルなどの変更を再適用する。 |

## よく使われるオプション組み合わせと業務シナリオ

### 1. インフラ構築・運用視点
* **タスク**: ユーザーの初ログイン時にホームディレクトリを自動作成する設定。
* **組み合わせ**: `select sssd with-mkhomedir`
* **解説**: AD連携環境などで、ローカルにユーザーディレクトリが存在しない場合に、ログインと同時に作成させます。
* **例**:
    ```bash
    sudo authselect select sssd with-mkhomedir
    ```

### 2. ブルーチーム視点
* **タスク**: パスワード試行失敗によるアカウントロックアウト（pam_faillock）の強制。
* **組み合わせ**: `select sssd with-faillock`
* **解説**: ブルートフォース攻撃対策として、`faillock` 機能を有効にします。これにより `/etc/pam.d/system-auth` 等に適切なモジュールが自動挿入されます。
* **例**:
    ```bash
    # 認証失敗時のロックアウト機能を有効化
    sudo authselect select sssd with-faillock
    # ロックアウト状況の確認（実務ログ例）
    sudo faillock --user <username>
    ```

### 3. レッドチーム視点
* **タスク**: 認証プロセスの調査とバイパスの検討。
* **組み合わせ**: `current`, `check`
* **解説**: 侵入後、そのシステムが LDAP 連携しているか (`sssd`)、パスワードロックアウトが有効か (`with-faillock`) を即座に把握し、パスワードスプレー攻撃のリスクを評価します。
* **例**:
    ```bash
    # 現在の認証構成を隠密に確認
    authselect current
    ```

## エラーメッセージとトラブルシューティング

1.  **エラーメッセージ例 1**: `Unexpected changes of /etc/pam.d/system-auth! Use --force...`
    * **考えられる原因**: `authselect` を通さず、手動で PAM 設定ファイルを編集したため、整合性が崩れている。
    * **解決策**: `authselect select ... --force` を実行してプロファイルを再適用する。※手動変更内容は消失するため注意。

2.  **エラーメッセージ例 2**: `Profile "sssd" does not exist`
    * **考えられる原因**: 必要なパッケージがインストールされていないか、タイポ。
    * **解決策**: `authselect list` で存在を確認し、必要に応じて `sssd` 等のパッケージをインストールする。

## 環境変数と設定ファイル

### 関連する設定ファイル
* **`/etc/authselect/authselect.conf`**: 現在適用されているプロファイル名とオプションが記録されている。
* **`/etc/authselect/custom/`**: 独自のカスタムプロファイルを定義する場合の配置場所。
* **`/usr/share/authselect/default/`**: システム標準のプロファイルテンプレート（読み取り専用）。

## セキュリティに関する考慮事項

### 脆弱性と悪用事例
* **脆弱性**: 誤ったプロファイル選択や、`with-faillock` の未設定により、ブルートフォース攻撃に対する脆弱性が残る。
* **悪用シナリオ**: 攻撃者が root 権限を得た場合、`authselect` を利用して認証を `sssd` から `local` (minimal) に切り替え、中央管理のログ監視を回避しつつ、ローカルにバックドアユーザーを作成する可能性があります。

### GTFOBins / LOLBAS における利用例
`authselect` 自体は権限昇格の直接的なバイナリとして登録されることは稀ですが、**認証設定の改ざん（Persistence/Evasion）**において極めて重要です。

### 対応策・緩和策 (ブルーチーム視点)
* **Prevention (予防)**: 認証設定の変更は必ず `authselect` を通じて行い、手動編集を禁止する。
* **Detection (検知)**: `/etc/authselect/authselect.conf` および `/etc/pam.d/` 下のファイルの変更を `auditd` で監視する。

## 注意点・補足
* **手動編集の禁止**: `authselect` を導入したシステムでは、`/etc/pam.d/system-auth` や `/etc/nsswitch.conf` を直接編集してはいけません。`authselect` の再適用時に変更がすべて上書きされるためです。
* **バックアップ**: プロファイルを変更する前には、念のため `/etc/pam.d/` 全体のバックアップを推奨します。
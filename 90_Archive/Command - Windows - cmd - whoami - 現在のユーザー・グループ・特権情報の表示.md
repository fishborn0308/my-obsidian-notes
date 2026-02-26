---
# --- YAML Frontmatter ---
created: 2026-02-22 14:23
modified: 2026-02-22 15:13
environment: [OS/Windows]
vulnearability: [Privilege_Escalation]
knowledge_category: Command
tags:
  - whoami
  - cmd
  - enumeration
  - privilege_escalation
  - knowledge_base
---

# Command - Windows - cmd - whoami - 現在のユーザー・グループ・特権情報の表示

## 概要

`whoami` コマンドは、現在ローカル システムにログオンしているユーザーのユーザー名、グループ メンバーシップ、およびセキュリティ権限（特権）を表示します。

特にペネトレーションテストにおいては、取得したシェルの権限を確認し、権限昇格が可能かどうかを判断するための「状況把握（Situational Awareness）」に不可欠なコマンドです。

(出自: Windows標準搭載 / 旧Windowsリソースキット由来)

## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 (あれば) |
| :--- | :--- | :--- |
| `id` | Linux/Unixにおける同等コマンド。UID, GIDを表示する。 | `id` (Linux) |
| `net user` | 特定のユーザーの詳細設定（パスワード期限等）を表示。`whoami` は現在の「トークン」情報を表示。 | 用途による |
| `$env:USERNAME` | PowerShellの環境変数。ユーザー名のみを素早く取得できる。 | `$env:USERNAME` |

## よく連携されるコマンド

### シナリオ例: 権限昇格の可能性を調査 (レッドチーム)

* **目的**: 現在のユーザーが「悪用可能な特権」を持っているかを確認する。
* **連携コマンド**: `findstr`
* **解説**: `/priv` スイッチで特権を表示し、攻撃に利用できる特定の特権（例: SeImpersonatePrivilege）があるか抽出します。
* **コマンド例**:
    ```cmd
    REM 悪用可能な特権（Impersonateなど）を持っているか確認
    whoami /priv | findstr "Enabled"
    ```

## スイッチ/オプション説明

| スイッチ/オプション | 説明 |
| :--- | :--- |
| **基本表示** | |
| (なし) | `ドメイン\ユーザー名` の形式で表示。 |
| ⭐ `/user` | 現在のユーザー名とセキュリティ識別子 (SID) を表示。 |
| ⭐ `/groups` | 現在のユーザーが所属しているグループの一覧を表示。 |
| ⭐ `/priv` | **最重要。** 現在のユーザーに割り当てられているセキュリティ特権と、その有効/無効状態を表示。 |
| ⭐ `/all` | 上記すべての情報（ユーザー、グループ、特権、SID等）をまとめて表示。 |
| **出力形式** | |
| `/fo <Format>` | `TABLE` (表), `LIST` (リスト), `CSV` (カンマ区切り) から選択。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: バッチ処理の実行コンテキスト確認
* **解説**: スケジュールされたタスクやバックグラウンドサービスが、意図した通りのアカウント（例: `NT AUTHORITY\NETWORK SERVICE`）で動作しているかを確認します。
* **例**:
    ```cmd
    REM スクリプトのログに実行ユーザーを記録
    echo 実行ユーザー: >> monitor.log
    whoami >> monitor.log
    ```

### 2. ブルーチーム視点

* **タスク**: 侵害された端末での不審な特権の調査
* **解説**: 管理者が通常持たないような強力な特権（`SeDebugPrivilege` など）が有効化されている不審なプロセスやセッションがないかを確認します。
* **例**:
    ```cmd
    REM 現在のセッションの詳細なトークン情報を調査
    whoami /all /fo LIST
    ```

### 3. レッドチーム視点 (OSCP等で必須)

* **タスク**: 権限昇格ターゲットの選定
* **解説**: `whoami /priv` を実行し、以下の特権が `Enabled` であれば即座に権限昇格のチャンスと判断します。
    - **`SeImpersonatePrivilege`**: JuicyPotato, PrintSpoofer などのツールで `SYSTEM` 権限を狙える。
    - **`SeDebugPrivilege`**: メモリ内のパスワード（LSASS）のダンプが可能。
    - **`SeBackupPrivilege / SeRestorePrivilege`**: SAMハイブなどの強制コピーが可能。
* **例**:
    ```cmd
    REM 自分が「誰」で「何ができるか」の全情報を取得
    whoami /all
    ```

## エラーメッセージとトラブルシューティング

### よくあるエラーメッセージ

1.  **エラーメッセージ例 1**: `'whoami' は、内部コマンドまたは外部コマンド...として認識されていません。`
    * **考えられる原因**: 非常に古い Windows (XPより前) を使用しているか、`C:\Windows\System32` へのパスが通っていない。
    * **解決策**: `%SystemRoot%\System32\whoami.exe` を直接指定して実行する。

## 環境変数と設定ファイル

`whoami` コマンドは、現在のプロセスが持つ「アクセス トークン」の情報を動的に読み取ります。

## セキュリティに関する考慮事項

### 脆弱性と悪用事例

* **悪用シナリオ**:
    - **Reconnaissance**: 攻撃者が最初に実行し、自分が `www-data` (IIS) なのか、`Local Service` なのか、あるいは `Administrator` なのかを判断します。それにより、次に投げるエクスプロイト（Kernel Exploit か Token Manipulation か）を決定します。

### LOLBAS (Living Off The Land Binaries and Scripts) における利用例

* **機能**: `Information Requirements`
* **解説**: 攻撃の初期段階における環境認識（Enumeration）の標準。
* **参照**: [LOLBAS - whoami.exe](https://lolbas-project.github.io/lolbas/Binaries/Whoami/)

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**:
    - サービスアカウントには必要最小限の特権のみを付与する。
* **Detection (検知)**:
    - **イベントID 4688**: `whoami` の実行を監視。特に Web サーバーのプロセス（`w3wp.exe` 等）やデータベースプロセスから `whoami` が実行された場合は、Webシェルが設置された可能性が高いため、即座にアラートを出す設定を推奨します。

## 注意点・補足

* **SIDの重要性**: `/user` で表示される SID の末尾が `-500` であれば、それはビルトインの Administrator アカウントであることを意味します。
* **整合性レベル (Integrity Level)**: `/all` を実行すると出力の最後に「レベル」が表示されます。「高整合性 (High)」であれば管理者権限で実行中、「中整合性 (Medium)」であれば一般ユーザー権限です。

---
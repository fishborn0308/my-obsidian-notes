---
created: '2026-02-21'
modified: '2026-02-21'
environment: [OS/Windows]
vulnearability: [Reconnaissance, Privilege_Escalation]
knowledge_category: Command
tags:
  - 'cmd'
  - 'net_localgroup'
  - 'group_management'
  - 'privilege_escalation'
  - 'reconnaissance'
  - 'knowledge_base'
---

# Command - Windows - cmd - net localgroup - ローカルグループの管理

## 概要

`net localgroup` コマンドは、ローカルコンピュータ上のローカルグループの追加、表示、および変更を行うために使用されます。
特定のユーザーを `Administrators`（管理者）グループに追加して特権を与えたり、`Remote Desktop Users` グループに追加して遠隔操作を許可したりする際に多用されます。

(出自: `Windows 標準搭載 - net.exe`)

## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 (あれば) |
| :--- | :--- | :--- |
| `net group` | **ドメイン**グループを管理する（Active Directory 環境専用）。 | ドメイン環境 |
| `Get-LocalGroup` | PowerShell版。グループ一覧をオブジェクトとして取得可能。 | PS環境 |
| `Add-LocalGroupMember` | PowerShellでユーザーをグループに追加する。 | PS環境 |

## よく連携されるコマンド

### シナリオ例: ローカル管理者の特定と新規追加 (レッドチーム視点)

* **目的**: 現在の端末で誰が管理者権限を持っているかを確認し、必要に応じて自身のアカウントを管理者へ昇格させる。
* **連携コマンド**: `net localgroup`, `net user`
* **解説**: まずグループメンバーを列挙し、その後 `/add` スイッチで特定のユーザーを追加します。
* **コマンド例**:
    ```cmd
    REM 管理者グループのメンバーを確認
    net localgroup administrators

    REM ユーザー "attacker" を管理者グループに追加
    net localgroup administrators attacker /add
    ```

## スイッチ/オプション説明

| スイッチ/オプション | 説明 |
| :--- | :--- |
| **基本表示** | |
| (引数なし) | サーバー名と、そのコンピュータの全てのローカルグループを表示する。 |
| `groupname` | 指定したローカルグループのメンバーを一覧表示する。 |
| ⭐ `/domain` | **【重要】** ローカルではなく Active Directory ドメインに対して操作を行う。 |
| **グループ・メンバー操作** | |
| `/add` | 新しいローカルグループを作成する、または既存グループにユーザーを追加する。 |
| `/delete` | ローカルグループを削除する、または既存グループからユーザーを削除する。 |
| `[username [ ...]]` | グループに追加または削除する 1 つ以上のユーザー名を指定。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: 新しく配属された保守担当ユーザーに対し、リモートデスクトップによる接続権限を付与する。
* **例**:
    ```cmd
    REM "Remote Desktop Users" グループに "operator01" を追加
    net localgroup "Remote Desktop Users" operator01 /add
    ```

### 2. ブルーチーム視点

* **タスク**: 定期監査において、意図しない一般ユーザーが管理者グループ (`Administrators`) に紛れ込んでいないかチェックする。
* **例**:
    ```cmd
    REM 管理者グループのリストをテキストへ出力してベースラインと比較
    net localgroup administrators > current_admins.txt
    ```

### 3. レッドチーム視点 (偵察と権限昇格)

* **タスク 1: Local Admin Enumeration (ローカル管理者の列挙)**
    * **目的**: どのローカルアカウントが特権を持っているか、またはどのドメイングループがローカル管理者に含まれているかを特定する。
    * **解説**: `Administrators` グループの中に `Domain Admins` が含まれているか（通常は含まれる）、あるいは特定のドメインユーザーが個別に追加されているかを確認します。
    * **コマンド例**:
        ```cmd
        net localgroup administrators
        ```
    * **実務ログ例**:
        ```text
        別名                     administrators
        コメント                 コンピュータ/ドメインへの完全なアクセス権があります。
        メンバー
        -------------------------------------------------------------------------------
        Administrator
        EXAMPLE\Domain Admins    <-- ドメイン管理者グループが紐付いていることを確認
        j.doe                    <-- 特定のドメインユーザーが個別に追加されている
        コマンドは正常に終了しました。
        ```

* **タスク 2: 権限昇格の実行 (Privilege Escalation)**
    * **目的**: 低権限ユーザーのシェルを取得した後、管理者権限を取得する。
    * **解説**: 管理者権限を持つプロセスを奪取（エクスプロイト等）した後、自身の作業用アカウントに永続的な特権を付与します。
    * **コマンド例**:
        ```cmd
        REM 作業用アカウントを特権グループに追加
        net localgroup administrators svc_temp /add
        ```
    * **実務ログ例**:
        ```text
        コマンドは正常に終了しました。
        REM 以降、svc_temp はこの端末で全ての操作が可能になる。
        ```

* **タスク 3: 他の重要グループの調査**
    * **目的**: 直接的な管理者権限以外に、攻撃に役立つ権限を持つユーザーを探す。
    * **対象グループ例**:
        * `Remote Management Users`: WinRM（PowerShell Remoting）による遠隔操作が可能。
        * `Backup Operators`: ファイルのアクセス権を無視してバックアップ（＝重要データの読み取り）が可能。
    * **コマンド例**:
        ```cmd
        net localgroup "Remote Management Users"
        ```

## エラーメッセージとトラブルシューティング

1.  **System error 5 has occurred. Access is denied.**
    * **原因**: 管理者権限で実行されていない。メンバーの追加・削除には昇格したプロンプトが必要です。
    * **解決策**: 「管理者として実行」で再度実行してください。

2.  **The specified local group does not exist.**
    * **原因**: グループ名のスペルミス、または日本語OSで英語のグループ名（例: `Administrators` ではなく `Administrators` と打ったが実際は「Administrators」で認識されている場合など）を指定した。
    * **解決策**: 引数なしの `net localgroup` を実行し、正確な名称を確認してください。

## セキュリティに関する考慮事項

### 脆弱性と悪用事例

* **不適切な権限付与 (Weak Permissions - T1098):**
    * サービスアカウントや一般ユーザーが誤って `Administrators` グループに追加されている場合、それらのアカウントが侵害された瞬間に端末全体の支配権を奪われます。
* **偵察 (Local Group Enumeration - T1087.001):**
    * 低権限ユーザーでも `net localgroup` を実行してグループ構成を確認できることが多いため、ネットワーク内での「横展開（Lateral Movement）」のターゲット選定に利用されます。

### LOLBAS (Living Off The Land Binaries and Scripts) における利用例

* **機能**: `Reconnaissance` (偵察), `Persistence` (永続化)
* **特性**: 攻撃者がマルウェアを持ち込まずに、OS標準機能だけで「管理者への昇格」という致命的な操作を完結させるために使用されます。
* **参照**: [LOLBAS - net.exe](https://lolbas-project.github.io/lolbas/Binaries/Net/)

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**:
    * **Restricted Groups**: グループポリシーを使用して、ローカルグループのメンバーを強制的に固定し、手動での追加を自動的に削除するように設定します。
    * **最小権限の原則**: ユーザーには必要最低限のグループ権限（例: RDPのみなら `Remote Desktop Users`）のみを付与します。
* **Detection (検知)**:
    * **イベントID 4732**: ローカル グループにメンバーが追加されました。
    * **イベントID 4733**: ローカル グループからメンバーが削除されました。
    * これらのイベントをリアルタイムで監視し、特に `Administrators` グループへの変更は即座に調査対象とします。

## 注意点・補足

* **グループ名の大文字小文字**: Windows では通常区別されませんが、スクリプトの可読性のために正確な記述を推奨します。
* **スペースを含むグループ名**: `Remote Desktop Users` のようにスペースを含む場合は、必ず引用符 `"` で囲んでください。

---
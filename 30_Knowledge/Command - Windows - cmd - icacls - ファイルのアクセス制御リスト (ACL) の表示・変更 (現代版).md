---
created: 2026-02-21 07:56
modified: 2026-02-22 09:30
environment: [OS/Windows]
vulnearability: [Privilege_Escalation, Defense_Evasion]
knowledge_category: Command
tags:
  - cmd
  - icacls
  - permissions
  - acl
  - knowledge_base
---

# Command - Windows - cmd - icacls - ファイルのアクセス制御リスト (ACL) の表示・変更 (現代版)

## 概要

`icacls` (Integrity Control Access Control Lists) は、NTFS ファイルおよびディレクトリの権限を表示、変更、バックアップ、復元するための強力なツールです。

前身の `cacls` では不完全だった「権限の継承」や「整合性レベル (Integrity Level)」を正しく扱うことができ、現在の Windows 管理における標準コマンドです。

(出自: `Windows 標準搭載`)

## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 (あれば) |
| :--- | :--- | :--- |
| `cacls` | 旧式のコマンド。継承の扱いに難があり、現在は `icacls` の使用が鉄則。 | - |
| `takeown` | ファイルの所有権を強制的に取得する。権限がないファイルに対し `icacls` を使う前段として使用。 | セットで使用 |
| `Get-Acl` / `Set-Acl` | PowerShell でオブジェクト指向に操作。複雑な条件分岐を含む自動化に最適。 | PowerShell 環境 |

## よく連携されるコマンド

### シナリオ例: サービスバイナリの権限脆弱性調査 (レッドチーム視点 / OSCP)

* **目的**: 権限の弱いサービスバイナリを見つけ、自身のペイロードと差し替えて権限昇格 (Privilege Escalation) を狙う。
* **連携コマンド**: `tasklist`, `sc`, `icacls`
* **解説**: 実行中のサービスパスを特定し、そのファイルに対して現在のユーザーが「書き込み (W)」や「変更 (M)」権限を持っているかを確認します。
* **コマンド例**:
    ```cmd
    REM 特定のサービスバイナリに対する権限を調査
    icacls "C:\Program Files\Vulnerable App\service.exe"
    ```

## スイッチ/オプション説明

| スイッチ/オプション | 説明 |
| :--- | :--- |
| **基本操作** | |
| (引数なし) | 指定したファイルやディレクトリの ACL を表示する。 |
| `/grant[:r] user:perm` | 権限を付与する。`:r` を付けると、以前の明示的な権限を置換する。 |
| `/remove user` | 指定したユーザーの権限を ACL から削除する。 |
| `/inheritance:e|d|r` | 継承の設定。`e`:有効、`d`:無効にしてACEをコピー、`r`:無効にしてACEを削除。 |
| **高度な操作** | |
| `/save aclfile /t` | ACL をファイルに保存する。`/t` はサブディレクトリも含める。 |
| `/restore aclfile` | 保存された ACL ファイルから権限を復元する。 |
| `/verify` | ACL が健全（正規形式）であるかを確認する。 |
| `/reset` | ACL をデフォルトの継承されたものに置き換える。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: 新規構築したファイルサーバーの特定フォルダで、上位からの権限継承を切り、独自のアクセス権を設定する。
* **組み合わせ**: `/inheritance:d`, `/grant`
* **解説**: 継承を解除（d: disable）しつつ現在の権限を保持し、必要なユーザーにのみフルコントロールを与えます。
* **例**:
    ```cmd
    REM 継承を切り、現在のACEをコピーした上で、管理者のみフルコントロールにする
    icacls D:\PrivateData /inheritance:d
    icacls D:\PrivateData /grant:r Administrators:(F)
    ```

### 2. ブルーチーム視点

* **タスク**: システムディレクトリ内で、一般ユーザーに書き込み権限が与えられている異常な箇所を特定する。
* **組み合わせ**: `icacls <dir> /t /c /q`
* **解説**: サブディレクトリを含め (`/t`)、エラーを無視し (`/c`)、成功メッセージを非表示 (`/q`) にして高速にスキャンします。
* **例**:
    ```cmd
    REM Usersグループに書き込み(W)や変更(M)があるファイルを一括チェック（簡易版）
    icacls C:\Windows\* /t /c | findstr /i "(M) (F) (W)" | findstr /i "Users"
    ```

### 3. レッドチーム視点

* **タスク**: 権限昇格後、将来的なアクセスのために特定のディレクトリへのフルアクセス権を自分に付与する (Persistence)。
* **組み合わせ**: `/grant user:(F)`
* **解説**: 標的のディレクトリに対し、自分（または低権限ユーザー）にフルコントロール (`F`) を与えることで、いつでもファイルを設置できるようにします。
* **例**:
    ```cmd
    REM カレントユーザーに秘密のフォルダのフルコントロールを付与
    icacls "C:\Windows\System32\drivers\etc" /grant %username%:(F)
    ```

## エラーメッセージとトラブルシューティング

### よくあるエラーメッセージ

1.  **エラーメッセージ例 1**: `Successfully processed 0 files; Failed processing 1 files`
    * **考えられる原因**: 指定したパスが存在しないか、所有権がないため ACL を読み取れない。
    * **解決策**: `takeown /f <file>` で一度所有権を自分に移してから `icacls` を実行してください。

2.  **エラーメッセージ例 2**: `Access is denied.`
    * **考えられる原因**: UAC（ユーザーアカウント制御）により制限されている。
    * **解決策**: 管理者として実行してください。

## 環境変数と設定ファイル

`icacls` 自体に設定ファイルはありませんが、設定した権限は NTFS の **$MFT (Master File Table)** 内に保存されます。

## セキュリティに関する考慮事項

### 脆弱性と悪用事例

* **悪用シナリオ**: **Weak File Permissions (T1574.010)**。アプリケーションのインストールディレクトリが `Authenticated Users:(M)` になっている場合、攻撃者はその中の DLL を自身の悪意ある DLL に差し替えることで、アプリ起動時にコードを実行（DLL Hijacking）できます。

### LOLBAS (Living Off The Land Binaries and Scripts) における利用例

* **機能**: `Defense Evasion` (アクセス権変更による監視ツールの無効化など)
* **参照**: [LOLBAS - icacls.exe](https://lolbas-project.github.io/lolbas/Binaries/Icacls/)

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: `Program Files` などの重要ディレクトリに対し、一般ユーザーへの `Write/Modify` 権限が絶対に付与されないよう、グループポリシーで徹底する。
* **Detection (検知)**: **イベントID 4670** を監視し、特にシステムディレクトリやサービスディレクトリに対する ACL の変更を即座にアラート化する。

## 注意点・補足

* **権限記号の読み方**:
    * `(F)` : Full Control
    * `(M)` : Modify (変更)
    * `(RX)`: Read & Execute (読み取りと実行)
    * `(W)` : Write (書き込み)
    * `(AD)`: Append Data (データの追加)
* **継承フラグ**:
    * `(I)` : 継承された権限 (Inherited)
    * `(OI)`: オブジェクト継承 (Object Inherit - ファイルに継承)
    * `(CI)`: コンテナー継承 (Container Inherit - フォルダに継承)

---
---
# --- YAML Frontmatter ---
created: '2026-02-22'
modified: '2026-02-22'
environment: [OS/Windows]
vulnearability: [Privilege_Escalation, Insecure_Service_Permissions, Unquoted_Service_Path]
knowledge_category: Command
tags:
  - 'sc'
  - 'services'
  - 'privilege_escalation'
  - 'enumeration'
  - 'knowledge_base'
---

# Command - Windows - cmd - sc - サービス コントロール マネージャーとの通信

## 概要

`sc` コマンドは、Windows サービス コントロール マネージャー (SCM) との通信を行い、サービスの作成、開始、停止、一時停止、および構成の変更を行うためのツールです。
`net start` よりも詳細な設定が可能であり、リモートマシンのサービス操作もサポートしています。
(出自: Windows標準搭載)



## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 |
| :--- | :--- | :--- |
| `net start / stop` | サービスの開始・停止のみを行う簡易コマンド。設定変更は不可。 | 開始・停止のみなら `net` |
| `Get-Service` | PowerShell版。サービスの状態をオブジェクトとして取得・操作できる。 | `PowerShell` |
| `services.msc` | GUIベースの管理ツール。視覚的に全体像を把握するのに適している。 | `services.msc` (GUI) |

## よく連携されるコマンド

### シナリオ例: サービスの実行パスを確認して脆弱性を探す (レッドチーム)

* **目的**: サービスが実行しているバイナリのパスを確認し、書き換え可能かどうかを調査する。
* **連携コマンド**: `icacls`
* **解説**: `sc qc` で構成情報を取得し、表示された `BINARY_PATH_NAME` に対して `icacls` で権限を確認します。
* **コマンド例**:
    ```cmd
    REM サービスの構成（実行パスなど）を確認
    sc qc "TargetService"
    ```

## スイッチ/オプション説明

`sc` は第一引数に「サブコマンド」を取ります。

| サブコマンド | 説明 |
| :--- | :--- |
| ⭐ `query` | サービスの状態（実行中・停止中など）を表示する。 |
| ⭐ `qc` | **(Query Config)** サービスの構成情報（実行ファイルパス、開始型、実行ユーザー）を表示する。 |
| ⭐ `config` | サービスの構成内容（実行ファイルパスなど）を変更する。 |
| `start` / `stop` | サービスを開始 / 停止する。 |
| `create` / `delete` | サービスを新規作成 / 削除する。 |
| `description` | サービスの説明文を設定・変更する。 |

| 主要な設定引数 (config時) | 説明 |
| :--- | :--- |
| ⭐ `binpath=` | 実行ファイルのパスを指定。**※「=」の後に必ずスペースが必要。** |
| `start=` | 開始型を指定（`auto`, `demand`, `disabled`）。**※「=」の後に必ずスペースが必要。** |
| `obj=` | サービスを実行するアカウントを指定（例: `LocalSystem`）。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: リモートサーバーのサービス状態確認と再起動
* **解説**: 管理端末からリモートサーバーのサービスが生きているか確認し、必要に応じて再起動します。
* **例**:
    ```cmd
    REM リモートサーバー(192.168.1.10)のSpoolerサービスの状態を確認
    sc \\192.168.1.10 query "Spooler"
    ```

### 2. ブルーチーム視点

* **タスク**: 不審なサービスのバイナリパスの監査
* **解説**: 攻撃者によって偽装されたサービス（例: `svchost.exe` に見せかけた別名の実行ファイル）がないか、すべてのサービスのパスを一覧してチェックします。
* **例**:
    ```cmd
    REM すべてのサービスの構成情報をリストアップ（調査用）
    for /f "tokens=2" %i in ('sc query state^= all ^| findstr "SERVICE_NAME"') do sc qc %i
    ```

### 3. レッドチーム視点 (OSCP等で最重要)

* **タスク**: サービスバイナリの差し替えによる権限昇格 (Service Binary Hijacking)
* **解説**: サービスの設定（binpath）を書き換える権限がある場合、実行パスを自分のリバースシェル（例: `rev.exe`）に書き換え、サービスを再起動することで `SYSTEM` 権限を奪取します。
* **例**:
    ```cmd
    REM サービスの実行パスを自分のペイロードに書き換え
    sc config "WeakService" binpath= "C:\Temp\shell.exe"
    
    REM サービスを再起動して実行（権限があれば）
    sc stop "WeakService"
    sc start "WeakService"
    ```

## エラーメッセージとトラブルシューティング

### よくあるエラーメッセージ

1.  **エラーメッセージ例 1**: `[SC] OpenService FAILED 5: アクセスが拒否されました。`
    * **考えられる原因**: サービスの構成を変更、または停止させるための十分な権限がない。
    * **解決策**: 「管理者として実行」する。それでも不可な場合は、そのサービス自体の ACL (アクセス制御リスト) で制限されている。

2.  **エラーメッセージ例 2**: `[SC] OpenSCManager FAILED 1722: RPC サーバーを利用できません。`
    * **考えられる原因**: リモートマシンの操作を試みたが、ネットワーク接続が切れている、または対象の RPC サービスが停止している。
    * **解決策**: ターゲットとの疎通と、ファイアウォール設定を確認する。

## 環境変数と設定ファイル

`sc` の操作対象となるサービス情報は、以下のレジストリキーに格納されています。
* **パス**: `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services`

## セキュリティに関する考慮事項

### 脆弱性と悪用事例

* **悪用シナリオ**: 
    - **Insecure Service Permissions**: 一般ユーザーが特定のサービスの `SERVICE_CHANGE_CONFIG` 権限を持っている場合、`binpath` を書き換えて権限昇格に悪用される。
    - **Unquoted Service Path**: パスにスペースが含まれ、かつ `"` で囲まれていない場合、途中のパスに悪意あるバイナリを置いて実行させる。

### LOLBAS (Living Off The Land Binaries and Scripts) における利用例

* **機能**: `Execute`, `Privilege Escalation`
* **参照**: [LOLBAS - sc.exe](https://lolbas-project.github.io/lolbas/Binaries/Sc/)

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: 
    - サービスの ACL を厳格に管理し、一般ユーザーに `config` 権限を与えない。
    - すべてのサービスのパスが引用符 `"` で囲まれていることを確認する。
* **Detection (検知)**: 
    - **イベントID 7045**: 新しいサービスがインストールされた。
    - **イベントID 4697**: サービスがシステムに登録された（特に実行ユーザーが `SYSTEM` のものに注意）。

## 注意点・補足

* **スペースの罠**: `binpath= C:\...` のように、**イコールの後にスペースが必須**である点は、このコマンド最大の「初見殺し」仕様です。
* **戻り値**: `sc` は成功しても失敗しても標準出力にメッセージを出すため、バッチファイルで成否を判定する場合は `%ERRORLEVEL%` を正しくチェックする必要があります。

---
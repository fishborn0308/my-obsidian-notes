---
# --- YAML Frontmatter ---
created: '2026-02-22'
modified: '2026-02-22'
environment: [OS/Windows]
vulnearability: [Persistence, Privilege_Escalation, Credential_Access]
knowledge_category: Command
tags:
  - 'reg'
  - cmd
  - 'registry'
  - 'post_exploitation'
  - 'knowledge_base'
---

# Command - Windows - cmd - reg - レジストリの操作 (CLI版)

## 概要

`reg` コマンドは、コマンドラインから Windows レジストリを操作するための強力なツールです。レジストリキーの追加、削除、検索、保存、復元、およびインポート/エクスポートが可能です。
GUI (regedit) を使用できない環境や、スクリプトによる自動設定において不可欠なコマンドです。
(出自: Windows標準搭載)



## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 (あれば) |
| :--- | :--- | :--- |
| `regedit` | GUI ツール。視覚的な操作に適している。 | `regedit` (GUI環境) |
| `Get-ItemProperty` | PowerShell コマンドレット。オブジェクトとして情報を扱える。 | `PowerShell` |
| `regini` | テキストファイルからレジストリを書き換える古いツール。現在は `reg` が主流。 | `reg` |

## よく連携されるコマンド

### シナリオ例: レジストリキーの存在を確認して処理を分岐 (スクリプト)

* **目的**: 特定のソフトウェアがインストールされているか、または特定の設定が有効かを判定する。
* **連携コマンド**: `if`, `findstr`
* **解説**: `reg query` の終了コード（%ERRORLEVEL%）を利用して分岐します。
* **コマンド例**:
    ```cmd
    REM 特定のキーが存在するか確認
    reg query "HKLM\Software\Microsoft\Windows\CurrentVersion\Uninstall" /s | findstr /i "TargetApp"
    if %ERRORLEVEL% equ 0 echo ソフトウェアが見つかりました。
    ```

## スイッチ/オプション説明

`reg` は第一引数に「サブコマンド」を取ります。

| サブコマンド | 説明 |
| :--- | :--- |
| ⭐ `query` | レジストリキーの値やサブキーを一覧表示・検索する。 |
| ⭐ `add` | 新しいキーや値を追加する、または既存の値を上書きする。 |
| ⭐ `delete` | キーや値を削除する。 |
| ⭐ `save` | 指定したキーのコピーをバイナリファイル（ハイブ形式）として保存する。 |
| `restore` | `save` で保存したファイルをレジストリに書き戻す。 |
| `import` / `export` | .reg ファイル形式で取り込み/書き出しを行う。 |

| 主要オプション | 説明 |
| :--- | :--- |
| `/v <Value>` | 特定の値の名前を指定する。 |
| `/t <Type>` | データの種類を指定（`REG_SZ`, `REG_DWORD`, `REG_BINARY` など）。 |
| `/d <Data>` | 代入するデータを指定する。 |
| `/f` | 確認メッセージを表示せずに強制的に実行（上書き/削除時）。 |
| `/s` | サブキーを再帰的に（深く）検索・表示する。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: リモートデスクトップ (RDP) のコマンドライン有効化
* **解説**: サーバー構築時、リモートから設定を流し込んで RDP を即座に有効にします。
* **例**:
    ```cmd
    REM RDP接続を許可し、ファイアウォールのルールも適用する（実際は1行ずつ実行）
    reg add "HKLM\System\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0 /f
    ```

### 2. ブルーチーム視点

* **タスク**: 自動起動設定（Runキー）の定期監査
* **解説**: 攻撃者がよく利用する永続化ポイントをスキャンし、身に覚えのないプログラムが登録されていないか確認します。
* **例**:
    ```cmd
    REM ユーザーごとの自動起動設定を一括チェック
    reg query "HKCU\Software\Microsoft\Windows\CurrentVersion\Run" /s
    ```

### 3. レッドチーム視点 (OSCP等で多用)

* **タスク**: 認証情報の窃取 (Credential Dumping)
* **解説**: `SAM` および `SYSTEM` ハイブをバイナリとして保存し、攻撃端末に持ち帰ってオフラインでパスワードハッシュを解析（impacket-secretsdump等）します。
* **例**:
    ```cmd
    REM SAMとSYSTEMハイブをローカルに保存
    reg save HKLM\SAM sam.save
    reg save HKLM\SYSTEM system.save
    ```

* **タスク**: 権限昇格の脆弱性調査 (AlwaysInstallElevated)
* **解説**: MSIファイルを管理者権限で実行できる設定が有効になっていないか確認します。
* **例**:
    ```cmd
    reg query HKLM\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
    reg query HKCU\SOFTWARE\Policies\Microsoft\Windows\Installer /v AlwaysInstallElevated
    ```

## エラーメッセージとトラブルシューティング

### よくあるエラーメッセージ

1.  **エラーメッセージ例 1**: `エラー: アクセスが拒否されました。`
    * **考えられる原因**: 
        - HKLM などの重要な場所を書き換えようとしたが、管理者権限がない。
        - セキュリティソフトがレジストリ操作をブロックしている。
    * **解決策**: コマンドプロンプトを「管理者として実行」する。

2.  **エラーメッセージ例 2**: `エラー: 指定されたレジストリ キーまたは値が見つかりません。`
    * **考えられる原因**: パスの指定ミス（特に `HKEY_LOCAL_MACHINE` を `HKLM` と略さずに書く際のスペルミスなど）。
    * **解決策**: パスを `"` で囲み、略称（HKLM, HKCU, HKU）を使用する。

## 環境変数と設定ファイル

レジストリにはパスワードなどの機密情報が含まれることが多いため、`reg save` で作成したファイル自体の取り扱いには細心の注意が必要です。

## セキュリティに関する考慮事項

### 脆弱性と悪用事例

* **悪用シナリオ**: 
    - **UAC Bypass**: `Fodhelper.exe` などの正規プログラムが参照するレジストリキーを書き換え、管理者権限で任意のコマンドを実行させる。
    - **Persistence**: `Image File Execution Options` (IFEO) を利用し、特定のアプリ（例: `notepad.exe`）が起動した際にバックドアを同時起動させる。

### LOLBAS (Living Off The Land Binaries and Scripts) における利用例

* **機能**: `Credential Access`, `Execute`
* **参照**: [LOLBAS - reg.exe](https://lolbas-project.github.io/lolbas/Binaries/Reg/)

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: 
    - 最小権限の原則（PoLP）を適用し、一般ユーザーに HKLM の書き込み権限を与えない。
* **Detection (検知)**: 
    - **イベントID 4657**: レジストリ値の変更を監視。
    - 特に `SAM` ハイブの `reg save` 実行は、資格情報窃取の明確な予兆としてアラートを出すべきです。

## 注意点・補足

* **ハイブの略称**:
    - `HKEY_LOCAL_MACHINE` -> `HKLM`
    - `HKEY_CURRENT_USER` -> `HKCU`
    - `HKEY_CLASSES_ROOT` -> `HKCR`
    - `HKEY_USERS` -> `HKU`
* **再起動**: `reg add` で値を変更しても、アプリや OS がその値を読み込むタイミング（起動時やログオン時）にならないと反映されない設定も多いです。

---
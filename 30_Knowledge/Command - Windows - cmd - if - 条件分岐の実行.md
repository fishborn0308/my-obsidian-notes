---
created: 2026-02-22 08:41
modified: 2026-02-22 09:30
environment: [OS/Windows]
vulnearability: [Reconnaissance, Execution]
knowledge_category: Command
tags:
  - if
  - cmd
  - scripting
  - conditional
  - batch_logic
  - automation
  - knowledge_base
---

# Command - Windows - cmd - if - 条件分岐の実行

## 概要

`if` コマンドは、バッチ プログラム内で条件処理を実行します。指定した条件が「真」である場合にのみ、続くコマンドを実行します。文字列の比較、ファイルの存在確認、および直前のコマンドの終了コード（ERRORLEVEL）の判定など、スクリプトの動的な制御に不可欠な要素です。

(出自: `Windows 標準搭載 - cmd.exe 内蔵コマンド`)

## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 (あれば) |
| :--- | :--- | :--- |
| `if` (PowerShell) | オブジェクトの比較が可能で、構文がより現代的（C#に近い）。 | PS 環境 |
| `test` / `[` | Linux (bash) における条件評価コマンド。 | (Linux環境) |
| `choice` | ユーザーの選択を待機し、その結果を `if errorlevel` で受ける。 | 対話型分岐 |

## よく連携されるコマンド

### シナリオ例: 管理者権限の有無による処理分岐 (偵察・権限昇格)

* **目的**: スクリプトが管理者として実行されているかを確認し、権限が不足していれば警告して終了する。
* **連携コマンド**: `net session`, `if`, `goto`
* **解説**: 管理者権限が必要な `net session` を実行し、その成否 (`errorlevel`) を判定します。
* **コマンド例**:
    ```cmd
    @echo off
    net session >nul 2>&1
    if %errorlevel% neq 0 (
        echo [!] Please run this script as Administrator.
        goto :eof
    )
    echo [+] Success: Administrator privileges detected.
    ```

## 条件式と演算子

### 基本的な条件

| 条件 | 説明 |
| :--- | :--- |
| `exist [path]` | 指定したファイルやフォルダが存在する場合に真。 |
| `defined [var]` | 指定した環境変数が定義されている場合に真。 |
| `errorlevel [n]` | 直前のコマンドの終了コードが `n` **以上**の場合に真。 |
| `not [condition]` | 条件の結果を反転させる。 |

### 比較演算子 (文字列/数値)

`/i` スイッチを付けると大文字小文字を区別しません。

| 演算子 | 意味 |
| :--- | :--- |
| `EQU` | 等しい (Equal to) |
| `NEQ` | 等しくない (Not equal to) |
| `LSS` | より小さい (Less than) |
| `LEQ` | 以下 (Less than or equal to) |
| `GTR` | より大きい (Greater than) |
| `GEQ` | 以上 (Greater than or equal to) |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: 設定ファイルが存在しない場合のみ、デフォルト設定を作成する。
* **例**:
    ```cmd
    if not exist "C:\Config\app.ini" (
        echo [Default Settings] > "C:\Config\app.ini"
        echo Created default config.
    )
    ```

### 2. ブルーチーム視点 (自動修復)

* **タスク**: 特定の重要なサービス（例: アンチウイルス）が停止している場合に、自動的に再起動を試みる。
* **例**:
    ```cmd
    sc query "WinDefend" | find "STOPPED"
    if %errorlevel% equ 0 (
        net start "WinDefend"
        echo Defender was stopped and has been restarted.
    )
    ```

### 3. レッドチーム視点 (環境把握と回避 - T1497)

* **タスク 1: Anti-VM Check (仮想環境の検知)**
    * **目的**: ターゲットのファイルシステム内に、VMware や VirtualBox 特有のファイルがあるか確認し、あれば解析を避けるために終了する。
    * **コマンド例**:
        ```cmd
        if exist "C:\windows\System32\Drivers\Vmmouse.sys" exit
        ```

* **タスク 2: Environmental Keying (実行環境の限定)**
    * **目的**: ペイロードが特定のドメイン名やホスト名と一致する場合のみ動作するようにし、サンドボックス内での暴発を防ぐ。
    * **コマンド例**:
        ```cmd
        if /i "%USERDOMAIN%" neq "TARGET_DOMAIN" exit
        ```

## エラーメッセージとトラブルシューティング

1.  **括弧 `()` の記述ミス**
    * **現象**: `if` 文の途中でスクリプトがクラッシュする、または意図しない挙動をする。
    * **解決策**: `if condition (` の後の開き括弧は、`if` と同じ行に記述する必要があります。また、括弧内の各コマンドは適切な改行が必要です。

2.  **空の変数による構文エラー**
    * **現象**: `%var%` が空のとき、`if %var%==1` が `if ==1` となり構文エラーになる。
    * **解決策**: 変数を引用符で囲みます。 `if "%var%"=="1"`

## セキュリティに関する考慮事項

### 脆弱性と悪用事例

* **ロジックバイパス**:
    * 攻撃者が環境変数を事前に書き換えられる場合、スクリプト内のセキュリティチェック（例: `if "%ADMIN_STATUS%"=="OK"`）を容易にバイパスされる可能性があります。

## 注意点・補足

* **else 句**: `if condition ( cmd1 ) else ( cmd2 )` のように記述します。`else` は `)` と同じ行に置く必要があります。
* **数値比較の注意**: `EQU` などの演算子を使う際、両辺が数字のみで構成されていれば数値として、それ以外は文字列として比較されます。

---
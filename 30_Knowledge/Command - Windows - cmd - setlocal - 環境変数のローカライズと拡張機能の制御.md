---
# --- YAML Frontmatter ---
created: 2026-02-22 09:34
modified: 2026-02-22 15:13
environment: [OS/Windows]
vulnearability: []
knowledge_category: Command
tags:
  - setlocal
  - cmd
  - batch_scripting
  - delayed_expansion
  - knowledge_base
---

# Command - Windows - cmd - setlocal - 環境変数のローカライズと拡張機能の制御

## 概要

`setlocal` コマンドは、バッチファイル内において環境変数の変更の有効範囲（スコープ）を限定（ローカライズ）するために使用されます。

`setlocal` を実行した後に変更された環境変数は、そのバッチファイルが終了するか、`endlocal` コマンドが実行された時点で、実行前の状態に自動的に復元されます。

(出自: Windows標準搭載)

## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 (あれば) |
| :--- | :--- | :--- |
| `endlocal` | `setlocal` で開始したローカライズを明示的に終了し、変数を元の状態に戻す。 | セットで使用 |
| `set` | 変数を設定するが、`setlocal` なしだと実行環境全体の変数を汚染する可能性がある。 | 併用 |

## よく連携されるコマンド

### シナリオ例: ループ内での動的な変数更新 (スクリプト作成)

* **目的**: `for` ループの中で、一回ごとに更新される変数の値を参照する。
* **連携コマンド**: `set`, `for`
* **解説**: 通常の `%VAR%` 形式ではループ開始時に値が固定（即時展開）されてしまいます。`enabledelayedexpansion` を有効にし、`!VAR!` 形式を使うことで、ループ実行中の最新値を参照できます。
* **コマンド例**:
    ```cmd
    @echo off
    setlocal enabledelayedexpansion
    set COUNT=0
    for /L %%i in (1,1,5) do (
        set /a COUNT+=1
        echo 現在のカウントは !COUNT! です
    )
    endlocal
    ```

## スイッチ/オプション説明

| スイッチ | 説明 |
| :--- | :--- |
| **拡張機能制御** | |
| `enableextensions` | コマンド拡張機能（`for /f` や `if` の拡張など）を有効にする（デフォルトで有効なことが多い）。 |
| `disableextensions` | コマンド拡張機能を無効にする。古いMS-DOS互換を重視する場合に使用。 |
| **遅延展開制御** | |
| ⭐ `enabledelayedexpansion` | **遅延環境変数の展開**を有効にする。`!変数名!` 形式での参照が可能になる。 |
| `disabledelayedexpansion` | 遅延環境変数の展開を無効にする（デフォルト）。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: 他のスクリプトに影響を与えない一時的なパス設定
* **組み合わせ**: `set PATH`
* **解説**: 特定のツールを実行するために一時的に `PATH` を通すが、そのバッチ終了後は元の `PATH` に戻したい場合に使用します。
* **例**:
    ```cmd
    @echo off
    setlocal
    REM このバッチ内だけで有効なツールパスを追加
    set PATH=%PATH%;C:\SpecialTool\bin
    call Tool.exe
    endlocal
    REM ここでは PATH は元の状態に戻っている
    ```

### 2. ブルーチーム視点

* **タスク**: セーフティな解析スクリプトの作成
* **組み合わせ**: `endlocal`
* **解説**: インシデントレスポンスにおいて、調査対象サーバーの既存の環境変数を一切汚さずに、解析に必要な変数（一時保存先など）を扱うために必須です。
* **例**:
    ```cmd
    @echo off
    setlocal
    set ANALYSIS_TMP=C:\Users\Public\Tmp
    REM 調査処理...
    endlocal
    ```

### 3. レッドチーム視点

* **タスク**: 複雑な列挙（Enumeration）スクリプトの作成
* **組み合わせ**: `for /f`, `enabledelayedexpansion`
* **解説**: ネットワーク内の他端末（Live Hosts）をスキャンし、見つかったIPをリスト化して動的に処理するような場合、遅延展開がないとロジックが破綻します。
* **例**:
    ```cmd
    @echo off
    setlocal enabledelayedexpansion
    set HOSTS=
    for /f "tokens=2" %%a in ('arp -a ^| findstr "Dynamic"') do (
        set HOSTS=!HOSTS! %%a
    )
    echo 発見されたホスト一覧: !HOSTS!
    endlocal
    ```

## エラーメッセージとトラブルシューティング

### よくあるエラーメッセージ

1.  **エラーメッセージ例 1**: (エラーは出ないが、変数が期待通りに更新されない)
    * **考えられる原因**: `for` や `if` のブロック内で `%VAR%` を使用しており、遅延展開が有効になっていない、または `!VAR!` を使っていない。
    * **解決策**: `setlocal enabledelayedexpansion` を宣言し、変数を `! !` で囲む。

2.  **エラーメッセージ例 2**: `SETLOCAL の最大再帰レベルに達しました。`
    * **考えられる原因**: ループや再帰呼び出しの中で `setlocal` が何度も実行され、`endlocal` が呼ばれていない（上限は32レベル）。
    * **解決策**: `setlocal` と `endlocal` のペアが正しく動作しているか確認する。

## 環境変数と設定ファイル

`setlocal` は環境変数そのものというより、**環境変数の管理の仕組み**を制御します。

## セキュリティに関する考慮事項

### 脆弱性と悪用事例

* **悪用シナリオ**:
    * **難読化**: 攻撃者が作成する複雑なバッチファイル（ダウンローダーなど）では、`enabledelayedexpansion` を用いて文字列を動的に組み立て、静的解析によるシグネチャ検知（例: `powershell` という文字列を分割して結合）を回避します。

### 対応策・緩和策 (ブルーチーム視点)

* **Detection (検知)**:
    * バッチファイル内で `setlocal enabledelayedexpansion` が多用されている場合、そのスクリプトは高度なロジック（または難読化）を含んでいる可能性が高いため、詳細な動的解析や引数の監視が推奨されます。

## 注意点・補足

* **バッチファイル内限定**: `setlocal` はバッチファイル（.bat, .cmd）内でのみ機能します。コマンドプロンプトに直接打ち込んでも効果はありません。
* **暗黙の終了**: バッチファイルの末尾に達すると、明示的に `endlocal` を書いていなくても自動的に `endlocal` が実行されたものとみなされます。

---
---
created: '2026-02-21'
modified: '2026-02-21'
environment: [OS/Windows]
vulnearability: [Information_Disclosure]
knowledge_category: Command
tags:
  - 'cmd'
  - 'type'
  - 'file_reading'
  - 'looting'
  - 'knowledge_base'
---

# Command - Windows - cmd - type - テキストファイルの内容表示

## 概要

`type` は、1 つ以上のテキストファイルの内容を表示するためのコマンドです。Unix/Linux システムにおける `cat` コマンドに相当します。バイナリファイルを表示しようとすると文字化けが発生します。

(出自: `Windows 標準搭載 - cmd.exe 内蔵コマンド`)

## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 (あれば) |
| :--- | :--- | :--- |
| `more` | 1画面ずつ停止して表示する。長いファイルの閲覧に適している。 | 長文ファイル |
| `cat` (Linux) | 機能はほぼ同じ。Windows の `type` はワイルドカードによる複数結合表示が可能。 | - |
| `Get-Content` (PS) | PowerShell版。内容をオブジェクトの配列として返すため、後続のフィルタリングに強い。 | PS環境 |

## よく連携されるコマンド

### シナリオ例: 設定ファイルからのパスワード抽出 (レッドチーム視点 / Looting)

* **目的**: システム内の設定ファイル（web.config, Unattend.xml など）を読み、認証情報を探す。
* **連携コマンド**: `type`, `findstr`
* **解説**: ファイルの中身を標準出力に出し、`findstr` で特定のキーワードを検索します。
* **コマンド例**:
    ```cmd
    REM 設定ファイルから "password" という文字列を含む行を探す
    type C:\inetpub\wwwroot\web.config | findstr /i "password"
    ```



## スイッチ/オプション説明

`type` には主要なスイッチはありません。主な使い方は引数にファイル名を指定することです。

| 引数例 | 説明 |
| :--- | :--- |
| `file.txt` | `file.txt` の内容を表示する。 |
| `*.log` | カレントディレクトリ内のすべての `.log` ファイルの内容を連続して表示する。 |

## よく使われる組み合わせと業務シナリオ

### 1. ブルーチーム視点

* **タスク**: インシデント発生時、不審なバッチファイルやスクリプトの中身を、エディタで開かずに安全に確認する。
* **例**:
    ```cmd
    REM 悪意が疑われるバッチファイルの中身をコマンドラインで確認
    type C:\Users\Public\Downloads\update.bat
    ```

### 2. レッドチーム視点

* **タスク**: 収集したデータを 1 つのファイルにまとめて転送準備をする。
* **例**:
    ```cmd
    REM 複数のログファイルを 1 つの temp.txt に結合する
    type *.log > all_logs.txt
    ```

## エラーメッセージとトラブルシューティング

1.  **エラーメッセージ例 1**: `Access is denied.`
    * **考えられる原因**: ファイルの読み取り権限がない。
    * **解決策**: `icacls` で権限を確認するか、管理者権限での実行を試みる。

## セキュリティに関する考慮事項

* **悪用シナリオ**: 攻撃者が `C:\Windows\Panther\Unattend.xml` などのセットアップ応答ファイルを `type` で読み取り、ローカル管理者のパスワードを取得する手法は非常に有名です。
* **LOLBAS**: `type` 自体はファイルを読み取るだけですが、代替データストリーム (ADS) の内容を読み取る際にも使用されます。
    * 参照: `type c:\ads\file.txt:secret.txt`

---
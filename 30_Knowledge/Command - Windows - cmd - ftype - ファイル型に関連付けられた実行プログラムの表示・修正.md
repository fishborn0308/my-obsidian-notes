---
created: 2026-02-20 23:24
modified: 2026-02-21 08:39
environment: [OS/Windows]
vulnearability: [Privilege_Escalation, Persistence, Code_Execution]
knowledge_category: Command
tags:
  - cmd
  - file_association
  - lolbas
  - knowledge_base
  - ftype
---

# Command - Windows - cmd - ftype - ファイル型に関連付けられた実行プログラムの表示・修正

## 概要

`ftype` (File Type) コマンドは、ファイル型（例: `txtfile`, `exefile`）と、そのファイルを開く際に実行される実行ファイル（パスや引数）の紐付けを表示、または変更するためのコマンドです。

`assoc` コマンドで拡張子に紐付けられた「型」に対し、具体的な「動作」を定義する役割を担います。

(出自: `Windows 標準搭載 - cmd.exe 内蔵コマンド`)

## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 (あれば) |
| :--- | :--- | :--- |
| `assoc` | `assoc` は「拡張子 → 型」を定義。`ftype` は「型 → プログラム」を定義する。 | セットで使用 |
| `Default Programs` (GUI) | Windowsの設定画面。ユーザー個別の関連付けを優先的に管理する。 | 一般ユーザー向け |
| `Get-ItemProperty` (PS) | レジストリの `shell\open\command` キーを直接操作する。 | スクリプト制御ならPS |

## よく連携されるコマンド

### シナリオ例: 拡張子から実行プログラムまでのパスを一気に特定する (フォレンジック視点)

* **目的**: 不審な拡張子のファイルが、ダブルクリック時にどのプログラムで実行されるかを確認する。
* **連携コマンド**: `assoc`, `ftype`, `findstr`
* **解説**: Windowsのファイル起動プロセスを逆引きします。
* **コマンド例**:
	```cmd
    REM .ps1 拡張子の型を確認し、その型に紐づく実行コマンドを表示する
    for /f "tokens=2 delims==" %i in ('assoc .ps1') do ftype %i
    ```

## スイッチ/オプション説明

| スイッチ/オプション | 説明 |
| :--- | :--- |
| **引数なし** | 現在定義されているすべてのファイル型と実行コマンドを表示する。 |
| `fileType` | 指定したファイル型（例: `txtfile`）の現在の実行コマンドを表示する。 |
| `fileType=commandLine` | 指定した型に新しい実行コマンドを関連付ける。コマンド内の `%1` は引数（開くファイル名）に置き換わる。 |
| `fileType=` | 指定したファイル型の実行コマンド関連付けを削除する。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: 独自開発したツールの拡張子に対して、特定の引数付きでバイナリを起動させる。
* **組み合わせ**: `ftype MyTool.File="C:\Path\to\tool.exe" "%1" --config default`
* **解説**: `%1` を含めることで、ファイルをツールにドラッグ＆ドロップした際やダブルクリックした際に、そのパスを引数として渡せます。
* **例**:
	```cmd
    REM ログ解析ツールを専用のファイル型に紐付け
    ftype LogViewer.File="C:\Tools\Viewer.exe" "%1"
    ```

### 2. ブルーチーム視点

* **タスク**: `txtfile` や `htmlfile` などの標準的な型が、不審なバイナリ（PowerShellやスクリプト）に書き換えられていないか確認する。
* **組み合わせ**: `ftype | findstr /i "powershell.exe cmd.exe temp appdata"`
* **解説**: 本来ドキュメントビューアで開くべきファイル型が、シェルや一時ディレクトリの実行ファイルを指している場合、永続化攻撃の兆候です。
* **例**:
　　```cmd
    REM 危険な実行ファイルが関連付けに含まれていないか抽出
    ftype | findstr "powershell cscript wscript"
　　```

### 3. レッドチーム視点

* **タスク**: ユーザーがよく使うファイル（例: テキストファイル）を開く動作をハイジャックし、ペイロードを実行しつつ本来のアプリも開く。
* **組み合わせ**: `ftype txtfile=C:\Windows\System32\cmd.exe /c "start /b malicious.exe & notepad.exe %1"`
* **解説**: `ftype` を書き換えることで、ユーザーに気づかれずにバックグラウンドで処理を実行させる「File Association Hijacking」の手法です。
* **例**:
	```cmd
    REM txtfileの動作を書き換えて永続化を狙う（管理者権限が必要）
    ftype txtfile=C:\Windows\System32\cmd.exe /c "powershell.exe -WindowStyle Hidden -EncodedCommand <payload> & notepad.exe %1"
    ```

## エラーメッセージとトラブルシューティング

### よくあるエラーメッセージ

1.  **エラーメッセージ例 1**: `Access is denied.`
		* **考えられる原因**: システム全体のファイル型定義（`HKCR`）を書き換えるため、管理者権限が必要です。
		* **解決策**: 管理者として実行したコマンドプロンプトを使用してください。

## 環境変数と設定ファイル

### 関連するレジストリ/設定ファイル

* **`HKEY_CLASSES_ROOT\<fileType>\shell\open\command`**: `ftype` コマンドが直接操作するレジストリの場所です。ここにある `(Default)` 値が実行コマンドそのものです。

## セキュリティに関する考慮事項

### 脆弱性と悪用事例

* **悪用シナリオ**: 永続化（Persistence）。OS起動後、ユーザーが特定のファイルを開くたびに攻撃者のコードが実行されます。
* **バイパス**: ユーザー個別の関連付け（`HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Explorer\FileExts`）が設定されている場合、`ftype` によるシステム全体の変更よりも優先されることがあります。

### LOLBAS (Living Off The Land Binaries and Scripts) における利用例

* **機能**: `Persistence` (永続化), `AWL Bypass` (コマンドラインに難読化を混ぜる等)
* **参照**: [LOLBAS - ftype.exe](https://lolbas-project.github.io/lolbas/Binaries/Ftype/)

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: EDR等で `ftype.exe` の不審な実行（特に標準的な型に対する変更）を監視する。
* **Detection (検知)**: レジストリ `HKCR\*\shell\open\command` の変更イベントを監視。

## 注意点・補足

* **%1 の重要性**: `ftype` を定義する際、`"%1"` を忘れると、ファイルをダブルクリックしても「そのファイル自体」を引数として渡せなくなるため、アプリケーションが正しく開きません。
* **優先順位の複雑さ**: Windows 10以降、ユーザーの「既定のアプリ」の選択は非常に強力に保護されており、`ftype` だけで強制的に変更できないケース（ユーザーへの確認ダイアログが出る等）が増えています。

---
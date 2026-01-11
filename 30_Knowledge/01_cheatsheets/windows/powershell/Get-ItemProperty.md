---
tags:
  - 'Get-ItemProperty'
  - 'powershell'
  - 'cheatsheet'
title: 'Get-ItemProperty - アイテムのプロパティを取得する'
summary: '指定したアイテムのプロパティ（ファイルの情報やレジストリの値など）を取得します。'
related:
  - 'Set-ItemProperty'
  - 'Get-Item'
  - 'Copy-ItemProperty'
  - 'reg query'
---

# `Get-ItemProperty` - アイテムのプロパティを取得する

## 概要

`Get-ItemProperty` コマンドレットは、指定したアイテムが持つプロパティを取得します。`Get-Item` がアイテムそのもの（ファイルオブジェクトやレジストリオブジェクトなど）を返すのに対し、`Get-ItemProperty` はそのアイテムの**プロパティ（属性や値）**を直接取得するのが特徴です。

このコマンドレットは、ファイルやディレクトリのバージョン情報、タイムスタンプなどのメタデータを取得するためにも使われますが、最も強力な用途は**レジストリキーに格納されている値を取得する**ことです。

このコマンドレットには、`gp` という便利なエイリアスがデフォルトで設定されています。

## 類似コマンドレット/コマンドとの差異

| 類似コマンドレット/コマンド | 差異 / 使い分けのポイント |
| :--- | :--- |
| **`Get-Item`** | `Get-Item` はアイテム**全体**のオブジェクトを返します。プロパティにアクセスするには `(Get-Item ...).Property` のように一手間かかります。`Get-ItemProperty` はプロパティとその値を直接含むカスタムオブジェクトを返します。 |
| **`reg query` (CMD)** | `reg query` はレジストリの値を**テキスト**として返します。`Get-ItemProperty` は値を適切なデータ型（文字列、DWORDなど）を持つ**オブジェクト**として返すため、スクリプト内での計算や比較が容易です。 |

## よく連携されるコマンドレット

### シナリオ例: インストールされているソフトウェアのバージョンを確認する (インフラ構築・運用視点)

* **目的**: レジストリから、インストールされている特定のソフトウェア（例: 7-Zip）のバージョン番号を取得する。
* **連携コマンドレット**: `Select-Object`
* **解説**: ソフトウェアのアンインストール情報は、一般的にレジストリの `Uninstall` キー配下に格納されています。`Get-ItemProperty` でそのキーが持つプロパティ（値）を取得し、`Select-Object -ExpandProperty` で目的のプロパティ (`DisplayVersion`) だけを抽出します。
* **コマンド例**:

    ```powershell
    # 7-Zipのアンインストール情報が格納されているレジストリキーからプロパティを取得
    Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\7-Zip" | Select-Object -ExpandProperty DisplayVersion
    ```

## パラメータ説明

| パラメータ | 型 | 説明 |
| :--- | :--- | :--- |
| ⭐ **`-Path`** | `String[]` | プロパティを取得するアイテムのパスを指定します。 |
| ⭐ **`-Name`** | `String[]` | 取得するプロパティの名前を指定します。指定しない場合、全てのプロパティを取得します。ワイルドカードが使用できます。 |
| **`-LiteralPath`** | `String[]` | ワイルドカードを解釈せず、パスを文字通りに受け取ります。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: リモートデスクトップ接続が有効になっているか、レジストリを読んで確認する。
* **組み合わせ**: `Get-ItemProperty -Path <RegistryPath> -Name <ValueName>`
* **解説**: システム設定の多くはレジストリに保存されています。`Get-ItemProperty` を使うことで、GUIを開かずにこれらの設定値をスクリプトで監査できます。
* **例**:

    ```powershell
    # RDPを許可する設定値 (fDenyTSConnections) をレジストリから取得 (0なら有効)
    (Get-ItemProperty -Path "HKLM:\System\CurrentControlSet\Control\Terminal Server" -Name "fDenyTSConnections").fDenyTSConnections
    ```

### 2. ブルーチーム視点

* **タスク**: **永続化の監査**。自動実行のために `Run` キーに登録されているプログラムのパスを確認する。
* **組み合わせ**: `Get-ItemProperty -Path <RunKeyPath>`
* **解説**: `Run` キーはマルウェアが永続化のために最もよく利用する場所の1つです。`Get-ItemProperty` でこのキーの値を取得し、登録されているプログラムのパスが正当なものか、不審な場所（例: `%TEMP%`）を指していないかを監査します。
* **例**:

    ```powershell
    # システム全体のスタートアップ項目 (Runキー) を取得
    Get-ItemProperty -Path "HKLM:\Software\Microsoft\Windows\CurrentVersion\Run"

    # 現在のユーザーのスタートアップ項目 (Runキー) を取得
    Get-ItemProperty -Path "HKCU:\Software\Microsoft\Windows\CurrentVersion\Run"
    ```

### 3. レッドチーム視点

* **タスク**: **偵察 (Reconnaissance)**。システムやアプリケーションの設定情報をレジストリから収集する。
* **組み合わせ**: `Get-ItemProperty`
* **解説**: 攻撃者は侵入後、`Get-ItemProperty` を使って広範囲のレジストリキーを調査します。インストールされているソフトウェアの一覧 (`Uninstall`キー)、保存されている認証情報、ネットワーク設定などを探し出し、さらなる攻撃や権限昇格の手がかりとします。
* **例**:

    ```powershell
    # インストールされている全ソフトウェアの表示名とバージョンをリストアップ
    Get-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall\*" | Select-Object DisplayName, DisplayVersion
    ```

## エラーメッセージとトラブルシューティング

### よくあるエラーメッセージ

1. **エラーメッセージ**: `Get-ItemProperty: Cannot find path '...' because it does not exist.`
    * **考えられる原因**: 指定した `-Path` のアイテム（レジストリキーなど）が存在しません。
    * **解決策**: `Test-Path` でパスの存在を確認し、正しいパスを指定してください。

2. **エラーメッセージ**: `Get-ItemProperty: Property <name> does not exist at path <path>.`
    * **考えられる原因**: 指定した `-Name` のプロパティ（レジストリ値など）が、そのアイテムに存在しません。
    * **解決策**: `-Name` パラメータを省略して全てのプロパティを表示させ、正しいプロパティ名を確認してください。

## セキュリティに関する考慮事項

### 悪用事例

* **偵察による情報収集**: レッドチームのシナリオで述べた通り、`Get-ItemProperty` は攻撃者がシステムの構成、インストールされているソフトウェア、保存されている設定値などを詳細に把握するための、非常に強力な偵察ツールです。

### LOLBASにおける利用例

* **機能**: **偵察 (Reconnaissance)**
* **解説**: `Get-ItemProperty` は PowerShell の標準機能であり、攻撃者が環境に溶け込みながらシステムの詳細な構成情報を収集するために利用されます（Living off the Land）。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: レジストリにパスワードやAPIキーなどの機密情報を平文で保存しない。
* **Detection (検知)**: PowerShellのスクリプトブロックロギング (イベントID 4104) を有効化し、`Get-ItemProperty` の実行、特に機密情報が保存されがちなレジストリキーへのアクセスや、偵察ツール（BloodHoundなど）による広範囲なスキャンを監視する。`Sysmon` でのレジストリアクセスイベント (イベントID 12, 13, 14) の監視も有効です。

## 注意点・補足

* **戻り値の型**: `Get-ItemProperty` が返すオブジェクトは、`Get-Item` が返すオブジェクトとは異なります。これはプロパティの集合体であり、アイテム自体のメソッド（例: `Delete()`）は持っていません。

---
[PowerShellインデックスに戻る](../../powershell_index.md)

---
tags:
  - 'Get-Acl'
  - 'powershell'
  - 'cheatsheet'
title: 'Get-Acl - アイテムのアクセス制御リスト (ACL) を取得する'
summary: 'ファイルやレジストリキーなどのアイテムのセキュリティ記述子（所有者、アクセス権など）を取得します。'
related:
  - 'Set-Acl'
  - 'Get-Item'
  - 'icacls'
---

# `Get-Acl` - アイテムのアクセス制御リスト (ACL) を取得する

## 概要

`Get-Acl` コマンドレットは、ファイル、ディレクトリ、レジストリキーなどの指定したアイテムのセキュリティ記述子を表すオブジェクトを取得します。このセキュリティ記述子には、アイテムの所有者と、アクセス制御リスト (ACL) が含まれています。ACLはどのユーザーやグループが、そのアイテムに対してどのような操作（読み取り、書き込み、実行など）を許可または拒否されているかを定義するアクセス制御エントリ (ACE) のコレクションです。

このコマンドレットには、`gacl` という便利なエイリアスがデフォルトで設定されています。

## 類似コマンドレット/コマンドとの差異

| 類似コマンドレット/コマンド | 差異 / 使い分けのポイント |
| :--- | :--- |
| **`icacls` (CMD)** | `icacls` はACL情報を文字列として表示します。`Get-Acl` はACLを**オブジェクト**として取得するため、`Access` プロパティを展開して各ACEを個別に調べたり、フィルタリングしたりと、スクリプト内でのプログラム的な処理が非常に容易です。 |
| **`Get-Item`** | `Get-Item` はファイルやディレクトリの基本的なプロパティ（名前、サイズ、最終更新日時など）を取得します。アクセス権というセキュリティ情報に特化しているのが `Get-Acl` です。 |

## よく連携されるコマンドレット

### シナリオ例: ファイルのアクセス権を変更する (インフラ構築・運用視点)

* **目的**: 既存のファイル (`C:\data\report.docx`) のACLを取得し、新しいアクセスルールを追加して、変更を適用する。
* **連携コマンドレット**: `Set-Acl`
* **解説**: PowerShellでのACL操作は、(1)`Get-Acl`で現在のACLオブジェクトを取得、(2)そのオブジェクトに対して新しいルールを作成・追加、(3)`Set-Acl`で変更したACLオブジェクトをファイルに適用する、という3ステップが基本となります。
* **コマンド例**:

    ```powershell
    # 1. 対象ファイルの現在のACLを取得
    $acl = Get-Acl -Path "C:\data\report.docx"

    # 2. 新しいアクセスルールを定義 (例: 'CORP\Sales' グループに読み取りを許可)
    $accessRule = New-Object System.Security.AccessControl.FileSystemAccessRule("CORP\Sales", "Read", "Allow")
    $acl.SetAccessRule($accessRule)

    # 3. 変更したACLをファイルに適用
    Set-Acl -Path "C:\data\report.docx" -AclObject $acl
    ```

## パラメータ説明

| パラメータ | 型 | 説明 |
| :--- | :--- | :--- |
| ⭐ **`-Path`** | `String[]` | ACLを取得するアイテムのパスを指定します。 |
| **`-InputObject`** | `PSObject` | `Get-Item` などで取得したアイテムオブジェクトをパイプラインから受け取ります。 |
| **`-Audit`** | `SwitchParameter` | アイテムのシステムアクセス制御リスト (SACL) を取得します。監査設定を表示する場合に使用します。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: 共有フォルダのアクセス権が正しく設定されているかを確認する。
* **組み合わせ**: `Get-Acl | Format-List`
* **解説**: `Get-Acl` の結果を `Format-List` に渡すことで、所有者 (`Owner`) とアクセスルール (`Access`) の一覧を縦に並べて見やすく表示できます。
* **例**:

    ```powershell
    # D:\Share フォルダのACLを詳細に表示
    Get-Acl -Path "D:\Share" | Format-List
    ```

### 2. ブルーチーム視点

* **タスク**: **設定ミス（過剰な権限）の監査**。重要なシステムファイルに対して、予期せぬユーザーやグループに書き込み権限が付与されていないかを確認する。
* **組み合わせ**: `(Get-Acl <path>).Access`
* **解説**: ACLオブジェクトの `Access` プロパティを展開することで、アクセス制御エントリ (ACE) のリストを取得できます。これを `Where-Object` でフィルタリングし、例えば `Everyone` や `Authenticated Users` に `FullControl` や `Write` が許可されている危険な設定を発見します。
* **例**:

    ```powershell
    # hosts ファイルのアクセス権を調査し、Administrators と SYSTEM 以外に書き込み権限を持つエントリがないか確認
    (Get-Acl C:\Windows\System32\drivers\etc\hosts).Access | Where-Object { $_.FileSystemRights -match "Write" -and $_.IdentityReference -notin "BUILTIN\Administrators", "NT AUTHORITY\SYSTEM" }
    ```

### 3. レッドチーム視点

* **タスク**: **偵察 (Reconnaissance)**。侵入したシステム内で、書き込み可能なファイルやディレクトリを探す。
* **組み合わせ**: `Get-ChildItem`, `Get-Acl`, `Where-Object`
* **解説**: 攻撃者は権限昇格の足がかりやマルウェアの配置場所として、パーミッションが緩い（例: `Everyone` に書き込みが許可されている）ディレクトリを探します。`Get-ChildItem` でファイル/ディレクトリを列挙し、それぞれに対して `Get-Acl` で権限をチェックし、悪用可能な場所を特定します。
* **例**:

    ```powershell
    # C:\Program Files 配下を再帰的に検索し、'Authenticated Users' が書き込み権限を持つディレクトリを探す
    Get-ChildItem -Path "C:\Program Files" -Recurse -Directory | ForEach-Object {
        $dir = $_
        Get-Acl -Path $dir.FullName | ForEach-Object {
            $_.Access | Where-Object { $_.IdentityReference -eq "NT AUTHORITY\Authenticated Users" -and $_.FileSystemRights -match "Write" } | ForEach-Object {
                $dir # 条件に一致したディレクトリを出力
            }
        }
    }
    ```

## エラーメッセージとトラブルシューティング

### よくあるエラーメッセージ

1. **エラーメッセージ**: `Get-Acl: Cannot find path '...' because it does not exist.`
    * **考えられる原因**: 指定した `-Path` が存在しません。
    * **解決策**: `Test-Path` コマンドレットでパスの存在を確認し、正しいパスを指定してください。

## セキュリティに関する考慮事項

### 悪用事例

* **偵察による脆弱性の発見**: レッドチームのシナリオで述べた通り、`Get-Acl` は攻撃者がシステムの設定ミス（過剰な権限が付与されたファイルやディレクトリ）を発見するための強力な偵察ツールです。発見された場所は、DLLハイジャック、実行ファイルの置き換え、永続化のためのペイロード設置などに悪用されます。

### LOLBASにおける利用例

* **機能**: **偵察 (Reconnaissance)**
* **解説**: `Get-Acl` は PowerShell の標準機能であり、`powershell.exe` を通じて実行されます。攻撃者はこの正規の管理機能を利用して、検知されにくい形でシステムのセキュリティ設定を調査します。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: **最小権限の原則**を徹底し、ファイルやディレクトリには必要最小限のアクセス権のみを付与する。
* **Detection (検知)**: PowerShellのスクリプトブロックロギング (イベントID 4104) を有効化し、`Get-Acl` を使った広範囲なスキャンや、重要なシステムファイルに対する調査を監視する。ファイル整合性監視 (FIM) を導入し、重要なファイルのACLが変更された際にアラートを発報する。

## 注意点・補足

* **プロパティの展開**: `Get-Acl` の出力はオブジェクトです。アクセス権の詳細を見るには、`(Get-Acl C:\file.txt).Access` のように `Access` プロパティを展開する必要があります。

---
[PowerShellインデックスに戻る](../../powershell_index.md)

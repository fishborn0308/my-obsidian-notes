---
tags:
  - 'Get-WinEvent'
  - 'powershell'
  - 'cheatsheet'
title: 'Get-WinEvent - Windowsイベントログからイベントを取得する'
summary: 'Windows Vista以降の新しいWindowsイベントログからイベントを取得します。構造化されたクエリを使った強力なフィルタリングが可能です。'
related:
  - 'Get-EventLog'
  - 'wevtutil'
  - 'eventvwr'
  - 'Export-Csv'
---

# `Get-WinEvent` - Windowsイベントログからイベントを取得する

## 概要

`Get-WinEvent` コマンドレットは、ローカルまたはリモートコンピュータのWindowsイベントログからイベントを取得するための、モダンで強力なコマンドレットです。クラシックログ（System, Application, Security）と、Windows Vista以降で導入されたアプリケーションおよびサービスログの両方に対応しています。

最大の特長は、`-FilterHashtable` や `-FilterXPath` パラメータを使った**サーバーサイドでの効率的なフィルタリング**が可能であることです。これにより、大量のログの中から目的のイベントを高速に検索できます。パフォーマンスと機能性の両面で、レガシーな `Get-EventLog` よりも優れており、ログ分析とインシデント対応における標準ツールです。

## 類似コマンドレット/コマンドとの差異

| 類似コマンドレット/コマンド | 差異 / 使い分けのポイント |
| :--- | :--- |
| ⭐ **`Get-EventLog`** | `Get-EventLog` は古いクラシックログ専用のレガシーなコマンドレットです。`Get-WinEvent` は全てのWindowsイベントログに対応し、フィルタリング性能が圧倒的に高いため、**常に `Get-WinEvent` を使用すべきです**。 |
| **`wevtutil qe` (CMD)** | `wevtutil qe` もXPathクエリをサポートしていますが、出力はテキストです。`Get-WinEvent` は各イベントを**オブジェクト**として返すため、`Message` プロパティのXMLを展開したり、結果を `Export-Csv` に渡したりと、プログラム的な後続処理が非常に強力です。 |

## よく連携されるコマンドレット

### シナリオ例: 特定のプロセス作成イベントを詳細に分析する (ブルーチーム視点)

* **目的**: セキュリティログから、`powershell.exe` のプロセスが作成されたイベント (イベントID 4688) を全て検索し、そのコマンドライン引数を表示する。
* **連携コマンドレット**: `ForEach-Object`
* **解説**: `-FilterHashtable` を使って、ログ名、イベントID、およびイベントデータ内のプロセス名 (`NewProcessName`) で効率的にフィルタリングします。取得したイベントオブジェクトの `Message` プロパティには詳細がXML形式で含まれているため、`[xml]` 型に変換して特定のノード（`CommandLine`）にアクセスします。
* **コマンド例**:

    ```powershell
    # セキュリティログから powershell.exe のプロセス作成イベントを検索
    Get-WinEvent -FilterHashtable @{LogName='Security'; Id=4688} | ForEach-Object {
        # イベントメッセージをXMLとして解釈
        $eventXml = [xml]$_.ToXml()
        # プロセス名が 'powershell.exe' の場合のみ、コマンドラインを表示
        if ($eventXml.Event.EventData.Data.'#text' -match 'powershell.exe') {
            $eventXml.Event.EventData.Data | Where-Object { $_.Name -eq 'CommandLine' } | Select-Object '#text'
        }
    }
    ```

## パラメータ説明

| パラメータ | 型 | 説明 |
| :--- | :--- | :--- |
| ⭐ **`-FilterHashtable`** | `Hashtable` | キーと値のペアでクエリを定義します。最も使いやすく推奨されるフィルタリング方法です。(`LogName`, `Id`, `StartTime`, `EndTime`, `ProviderName`, `Keywords`など) |
| **`-FilterXPath`** | `String` | XPath 1.0 構文を使って、より複雑で詳細なクエリを定義します。 |
| ⭐ **`-LogName`** | `String[]` | 取得するイベントログの名前を指定します。 |
| **`-ProviderName`** | `String[]` | イベントを発行したプロバイダーの名前を指定します。 |
| ⭐ **`-MaxEvents`** | `Int64` | 取得するイベントの最大数を指定します。 |
| **`-ComputerName`** | `String` | ログを取得するリモートコンピュータを指定します。 |
| **`-Oldest`** | `SwitchParameter` | イベントを古いものから順に取得します。デフォルトは新しい順です。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: 直近24時間以内にシステムログに記録された全てのエラーと重大なイベントを調査する。
* **組み合わせ**: `Get-WinEvent -FilterHashtable @{...}`
* **解説**: `-FilterHashtable` を使って、ログ名 (`System`)、時間範囲 (`StartTime`)、およびイベントレベル (`Level`) を組み合わせて、トラブルシューティングに必要な情報を効率的に絞り込みます。
* **例**:

    ```powershell
    # システムログから直近24時間のエラー(Level=2)と重大(Level=1)なイベントを取得
    $startTime = (Get-Date).AddDays(-1)
    Get-WinEvent -FilterHashtable @{LogName='System'; StartTime=$startTime; Level=1,2}
    ```

### 2. ブルーチーム視点

* **タスク**: **ブルートフォース攻撃の調査**。特定の時間帯に、特定のユーザーアカウントに対するログオン失敗 (イベントID 4625) が多発していないかを確認する。
* **組み合わせ**: `-FilterHashtable`
* **解説**: **インシデント対応の基本**。`FilterHashtable` を使うことで、大量のセキュリティログの中から、特定のイベントID、時間、さらにはイベントデータ内の特定の情報（例: ターゲットユーザー名）をサーバーサイドで高速にフィルタリングできます。
* **例**:

    ```powershell
    # セキュリティログから 'admin' ユーザーに対するログオン失敗イベントを検索
    $filter = @{
        LogName = 'Security'
        Id = 4625
        Data = 'admin' # イベントデータ内に 'admin' という文字列が含まれるものを検索
    }
    Get-WinEvent -FilterHashtable $filter
    ```

### 3. レッドチーム視点

* **タスク**: **痕跡消去 (Anti-Forensics)**。自身の活動ログを消去する前に、どのようなログが記録されているかを偵察する。
* **組み合わせ**: `Get-WinEvent -LogName "Microsoft-Windows-PowerShell/Operational"`
* **解説**: 攻撃者は、`Clear-EventLog` を実行する前に、PowerShellの操作ログ (`Operational`ログ) やセキュリティログに自身の活動（コマンド実行、不正ログインなど）が記録されているかを確認します。これにより、消去すべきログを特定します。
* **例**:

    ```powershell
    # PowerShellの操作ログで直近のコマンド実行履歴を偵察
    Get-WinEvent -LogName "Microsoft-Windows-PowerShell/Operational" -MaxEvents 20
    ```

## エラーメッセージとトラブルシューティング

### よくあるエラーメッセージ

1. **エラーメッセージ**: `Get-WinEvent: No events were found that match the specified selection criteria.`
    * **考えられる原因**: 指定したフィルタ条件に一致するイベントが存在しません。
    * **解決策**: フィルタ条件（ログ名、イベントID、時間範囲など）が正しいか確認してください。条件を緩めてみて、イベントが取得できるか試します。

## セキュリティに関する考慮事項

### 悪用事例

* **偵察と痕跡消去**: レッドチームのシナリオで述べた通り、`Get-WinEvent` は攻撃者が自身の活動がどのように記録されているかを調査し、`Clear-EventLog` で消去すべきログを特定するために利用されます。

### LOLBASにおける利用例

* **機能**: **偵察 (Reconnaissance)**
* **解説**: `Get-WinEvent` は PowerShell の標準機能であり、攻撃者が環境に溶け込みながらシステムの監査ログを調査するために利用されます（Living off the Land）。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: **ログの集中管理（ログフォワーディング）**。イベントログをリアルタイムでSIEMなどの外部ログサーバーに転送することで、ローカルホスト上でログがクリアされても証拠を保全できます。
* **Detection (検知)**: **イベントログがクリアされたことを示すイベントID 1102 (The audit log was cleared) を最優先で監視し、即座にアラートを発報するように設定する**。PowerShellのスクリプトブロックロギングを有効化し、`Get-WinEvent` や `Clear-EventLog` の実行を監視する。

## 注意点・補足

* **フィルタリングのパフォーマンス**: `-FilterHashtable` や `-FilterXPath` は、ログサービス側でイベントをフィルタリングしてから結果を返すため、`Get-WinEvent | Where-Object { ... }` のように取得した**後**にパイプラインでフィルタリングするよりも、パフォーマンスが劇的に向上します。大量のログを扱う際は、これらのパラメータを積極的に使用してください。

---
[PowerShellインデックスに戻る](../../powershell_index.md)

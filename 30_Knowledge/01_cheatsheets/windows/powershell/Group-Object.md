---
tags:
  - 'Group-Object'
  - 'powershell'
  - 'cheatsheet'
title: 'Group-Object - オブジェクトを指定したプロパティでグループ化する'
summary: 'オブジェクトのコレクションを、指定したプロパティの値に基づいてグループ化します。データの集計や分類に非常に強力です。'
related:
  - 'Sort-Object'
  - 'Measure-Object'
  - 'Where-Object'
---

# `Group-Object` - オブジェクトを指定したプロパティでグループ化する

## 概要

`Group-Object` コマンドレットは、パイプラインから渡されたオブジェクトのコレクションを、指定したプロパティの値が同じもの同士でグループ化します。これにより、大量のデータの中から特定の種類のアイテムがいくつあるかをカウントしたり、特定のカテゴリに分類して表示したりといった、データの集計・分析作業を効率的に行うことができます。

SQLの `GROUP BY` 句に似た機能を提供し、PowerShellのオブジェクト指向の利点を最大限に活用するための重要なコマンドレットです。

このコマンドレットには、`group` という便利なエイリアスがデフォルトで設定されています。

## 類似コマンドレット/コマンドとの差異

| 類似コマンドレット/コマンド | 差異 / 使い分けのポイント |
| :--- | :--- |
| **`Sort-Object`** | `Sort-Object` はオブジェクトを特定のプロパティで**並べ替え**ます。`Group-Object` はプロパティの値で**グループにまとめ、集計**します。 |
| **`Measure-Object`** | `Measure-Object` は、オブジェクトのコレクション全体に対する数値的な計算（合計、平均、最大、最小など）を行います。`Group-Object` はカテゴリごとの集計を行います。 |

## よく連携されるコマンドレット

### シナリオ例: 実行中のプロセスを名前ごとにカウントする (インフラ構築・運用視点)

* **目的**: システムで実行中のプロセスを名前ごとにグループ化し、それぞれのインスタンス数を表示する。
* **連携コマンドレット**: `Get-Process`, `Sort-Object`
* **解説**: `Get-Process` ですべてのプロセスオブジェクトを取得し、パイプラインで `Group-Object` に渡して `ProcessName` プロパティでグループ化します。最後に、`Sort-Object` で `Count` （各グループの要素数）の降順に並べ替えることで、最も多く実行されているプロセスを簡単に見つけられます。
* **コマンド例**:

    ```powershell
    # 全てのプロセスを取得し、プロセス名でグループ化して、インスタンス数が多い順に表示
    Get-Process | Group-Object -Property ProcessName | Sort-Object -Property Count -Descending
    ```

## パラメータ説明

| パラメータ | 型 | 説明 |
| :--- | :--- | :--- |
| ⭐ **`-Property`** | `Object[]` | オブジェクトをグループ化するために使用するプロパティを指定します。 |
| **`-InputObject`** | `PSObject` | グループ化するオブジェクトのコレクションを指定します。通常はパイプラインから渡されます。 |
| **`-NoElement`** | `SwitchParameter` | グループ化された元のオブジェクト (`Group` プロパティ) を出力に含めません。`Count` と `Name` の集計結果だけが必要な場合に便利です。 |
| **`-CaseSensitive`** | `SwitchParameter` | グループ化の際に、プロパティの値の大文字と小文字を区別します。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: ディレクトリ内のファイルを拡張子ごとに分類し、それぞれのファイル数をカウントする。
* **組み合わせ**: `Get-ChildItem | Group-Object -Property Extension`
* **解説**: `Get-ChildItem` でファイルオブジェクトを取得し、その `Extension` プロパティでグループ化することで、どのような種類のファイルが、いくつ存在するかを簡単に把握できます。
* **例**:

    ```powershell
    # C:\Windows\System32 内のファイルを拡張子でグループ化し、カウント
    Get-ChildItem -Path "C:\Windows\System32" -File | Group-Object -Property Extension | Sort-Object -Property Count -Descending
    ```

### 2. ブルーチーム視点

* **タスク**: **ログ分析**。セキュリティイベントログから、イベントIDごとの発生回数を集計する。
* **組み合わせ**: `Get-WinEvent | Group-Object -Property Id`
* **解説**: **インシデント調査の基本**。`Get-WinEvent` で大量のログを取得し、`Id` (イベントID) プロパティでグループ化することで、異常に多く発生しているイベントを特定できます。これは、ブルートフォース攻撃（多数のログオン失敗イベント）や、特定のマルウェアの活動などを発見する上で非常に有効です。
* **例**:

    ```powershell
    # セキュリティログの直近1000件を取得し、イベントIDでグループ化して発生回数順に表示
    Get-WinEvent -LogName Security -MaxEvents 1000 | Group-Object -Property Id | Sort-Object -Property Count -Descending
    ```

### 3. レッドチーム視点

* **タスク**: **偵察 (Reconnaissance)**。システム上のプロセスを実行ユーザーごとに分類する。
* **組み合わせ**: `Get-Process -IncludeUserName | Group-Object -Property UserName`
* **解説**: 攻撃者は侵入後、`Group-Object` を使って、どのユーザー（`SYSTEM`, `Administrator`, 一般ユーザーなど）がどのようなプロセスを実行しているかを分析します。これにより、権限昇格のターゲットとなるプロセスや、他のユーザーの活動を把握します。
* **例**:

    ```powershell
    # 実行中のプロセスをユーザー名でグループ化してカウント
    Get-Process -IncludeUserName -ErrorAction SilentlyContinue | Group-Object -Property UserName | Select-Object Name, Count
    ```

## エラーメッセージとトラブルシューティング

* `Group-Object` は入力オブジェクトを処理するだけなので、エラーが発生することは稀です。入力オブジェクトが期待したプロパティを持っていない場合、グループ化が意図通りに行われないことがあります。その際は、パイプラインの前段のコマンドレットの出力を `Get-Member` で確認してください。

## セキュリティに関する考慮事項

### 悪用事例

* **偵察 (Reconnaissance)**: レッドチームのシナリオで述べた通り、`Group-Object` は攻撃者がシステムの状況を要約・分析するための強力な偵察ツールです。実行中のプロセス、ネットワーク接続、サービスなどを様々な角度から分類・集計することで、システムの構成や活動状況を効率的に把握します。

### LOLBASにおける利用例

* **機能**: **偵察 (Reconnaissance)**
* **解説**: `Group-Object` は PowerShell の標準機能であり、攻撃者が環境に溶け込みながら、収集した情報を分析・集約するために利用されます（Living off the Land）。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: -
* **Detection (検知)**: PowerShellのスクリプトブロックロギング (イベントID 4104) を有効化し、`Group-Object` の実行を記録・監視する。特に、他の偵察コマンド (`Get-Process`, `Get-NetTCPConnection`など) と連携して、短時間に何度も実行される場合は、攻撃の兆候である可能性があります。

## 注意点・補足

* **出力オブジェクト**: `Group-Object` の出力は、`Group`（元のオブジェクトのコレクション）、`Count`（グループ内の要素数）、`Name`（グループ化に使われたプロパティの値）などのプロパティを持つ `GroupInfo` オブジェクトです。

---
[PowerShellインデックスに戻る](../../powershell_index.md)

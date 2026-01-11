---
tags:
  - 'Get-MpThreatDetection'
  - 'powershell'
  - 'cheatsheet'
  - 'Windows Defender'
title: 'Get-MpThreatDetection - 脅威の検出履歴を取得する'
summary: 'Microsoft Defenderウイルス対策によって検出されたアクティブな脅威および過去の脅威の履歴を取得します。'
related:
  - 'Get-MpThreat'
  - 'Remove-MpThreat'
  - 'Start-MpScan'
---

# `Get-MpThreatDetection` - 脅威の検出履歴を取得する

## 概要

`Get-MpThreatDetection` コマンドレットは、Microsoft Defenderウイルス対策スキャンによって検出された脅威の履歴を表示します。このコマンドレットは、過去にどのようなマルウェアが、いつ、どのファイルで検出されどのようなアクション（隔離・削除など）が取られたかを調査するために使用されます。インシデントの初期調査や、システムの健全性確認において重要な情報源となります。

## 類似コマンドレット/コマンドとの差異

| 類似コマンドレット/コマンド | 差異 / 使い分けのポイント |
| :--- | :--- |
| **`Get-MpThreat`** | `Get-MpThreat` は、現在システム上で**アクティブ**な（まだ完全には修復されていない）脅威を表示します。`Get-MpThreatDetection` は、修復済みも含めた**過去の検出履歴**を表示します。 |
| **イベントビューアー (eventvwr)** | Defenderの検出ログは、イベントビューアーの `Microsoft-Windows-Windows Defender/Operational` ログにも記録されます。`Get-MpThreatDetection` は、その情報をPowerShellオブジェクトとして、より簡単に照会・フィルタリングできるインターフェイスを提供します。 |

## よく連携されるコマンドレット

### シナリオ例: 特定の脅威に関する検出履歴を全て表示する (ブルーチーム視点)

* **目的**: 特定のマルウェア（例: `Trojan:Win32/Wacatac`）が、過去にシステムで何回検出されたか、その詳細な履歴を確認する。
* **連携コマンドレット**: `Where-Object`
* **解説**: `Get-MpThreatDetection` ですべての検出履歴を取得し、その結果をパイプラインで `Where-Object` に渡します。`ThreatName` プロパティが目的の脅威名と一致するものをフィルタリングします。
* **コマンド例**:

    ```powershell
    # 'Trojan:Win32/Wacatac' という名前の脅威に関する検出履歴を全て取得
    Get-MpThreatDetection | Where-Object { $_.ThreatName -like "*Wacatac*" }
    ```

## パラメータ説明

| パラメータ | 型 | 説明 |
| :--- | :--- | :--- |
| **`-ThreatID`** | `Int64[]` | 取得する脅威のIDを指定します。 |

*注: `Get-MpThreatDetection` には詳細なフィルタリングパラメータがありません。通常、全ての履歴を取得した後にパイプラインで `Where-Object` や `Sort-Object` を使ってフィルタリングします。*

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: 最近のウイルススキャンで何か検出されていなかったかを定期的に確認する。
* **組み合わせ**: `Get-MpThreatDetection | Sort-Object -Property InitialDetectionTime -Descending`
* **解説**: 全ての検出履歴を取得し、`InitialDetectionTime`（初期検出日時）で降順にソートすることで、最新の検出イベントから順に確認できます。
* **例**:

    ```powershell
    # 最新の脅威検出履歴を10件表示
    Get-MpThreatDetection | Sort-Object -Property InitialDetectionTime -Descending | Select-Object -First 10
    ```

### 2. ブルーチーム視点

* **タスク**: **インシデント調査**。特定のファイル（例: `C:\Users\Public\svchost.exe`）が過去にマルウェアとして検出された履歴があるかを調査する。
* **組み合わせ**: `Get-MpThreatDetection | Where-Object { $_.Resources -match "<FilePath>" }`
* **解説**: `Resources` プロパティには、脅威が検出されたファイルパスなどの情報が含まれています。正規表現 (`-match`) を使って特定のファイルパスを含む検出イベントを検索することで、そのファイルが過去にフラグ付けされたかどうかを特定できます。
* **例**:

    ```powershell
    # 'C:\Users\Public\svchost.exe' に関連する検出履歴を検索
    # パスのバックスラッシュをエスケープする必要がある
    Get-MpThreatDetection | Where-Object { $_.Resources -match "C:\\Users\\Public\\svchost.exe" }
    ```

### 3. レッドチーム視点

* **タスク**: **偵察 (Reconnaissance)** と**防御回避策の評価**。
* **組み合わせ**: `Get-MpThreatDetection`
* **解説**: 攻撃者は侵入後、`Get-MpThreatDetection` を実行して、どのような種類のマルウェアが過去に検出・ブロックされたかを確認します。これにより、ターゲットの環境で有効な脅威インテリジェンスのレベルや、どのような攻撃手法が検知されやすいかを推測し、自身のペイロードを調整します。
* **例**:

    ```powershell
    # 過去の検出履歴を偵察
    Get-MpThreatDetection
    ```

## エラーメッセージとトラブルシューティング

### よくあるエラーメッセージ

1. **エラーメッセージ**: `Get-MpThreatDetection : The term 'Get-MpThreatDetection' is not recognized as the name of a cmdlet...`
    * **考えられる原因**: Microsoft Defenderが実行されていない、または関連するPowerShellモジュール (`Defender`) が正しく読み込まれていません。
    * **解決策**: `Get-Service -Name "WinDefend"` でサービスが実行中であることを確認します。`Import-Module Defender` を実行してから再試行してみてください。

## セキュリティに関する考慮事項

### 悪用事例

* **偵察による防御機構の把握**: レッドチームのシナリオで述べた通り、攻撃者はこのコマンドレットの出力を分析して、ターゲットの防御能力を評価します。検出されたマルウェアの種類やシグネチャ名から、EDRの存在や特定の検知ルールを推測することがあります。

### LOLBASにおける利用例

* **機能**: **偵察 (Reconnaissance)**
* **解説**: `Get-MpThreatDetection` は PowerShell の標準機能（Defenderモジュール）であり、攻撃者が環境に溶け込みながら、システムのセキュリティ履歴を調査するために利用されます（Living off the Land）。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: -
* **Detection (検知)**: PowerShellのスクリプトブロックロギング (イベントID 4104) を有効化し、`Get-MpThreatDetection` の実行を記録・監視する。特に、他の偵察コマンドと連続して実行される場合は、攻撃の兆候である可能性があります。

## 注意点・補足

* **管理者権限**: 全ての検出履歴を正しく取得するには、PowerShellを「管理者として実行」することが推奨されます。

---
[PowerShellインデックスに戻る](../../powershell_index.md)

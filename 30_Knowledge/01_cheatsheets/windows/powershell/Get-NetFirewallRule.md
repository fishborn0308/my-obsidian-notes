---
tags:
  - 'Get-NetFirewallRule'
  - 'powershell'
  - 'cheatsheet'
title: 'Get-NetFirewallRule - ファイアウォール規則を取得する'
summary: 'Windows Defender ファイアウォールの規則を取得します。'
related:
  - 'New-NetFirewallRule'
  - 'Set-NetFirewallRule'
  - 'Enable-NetFirewallRule'
  - 'netsh advfirewall firewall show rule'
---

# `Get-NetFirewallRule` - ファイアウォール規則を取得する

## 概要

`Get-NetFirewallRule` コマンドレットは、ローカルまたはリモートコンピュータ上のWindows Defender ファイアウォールに存在する規則を取得します。このコマンドレットを使うことで、特定の名前、プロファイル、ポート番号など、様々な条件に基づいてファイアウォール規則を検索し、その詳細なプロパティ（有効/無効、アクション、方向など）をオブジェクトとして取得できます。システムのセキュリティ設定の監査や、トラブルシューティングに不可欠なコマンドレットです。

## 類似コマンドレット/コマンドとの差異

| 類似コマンドレット/コマンド | 差異 / 使い分けのポイント |
| :--- | :--- |
| **`netsh advfirewall firewall show rule` (CMD)** | `netsh` は規則の情報を**テキスト**として表示します。`Get-NetFirewallRule` は、各規則を豊富なプロパティを持つ**オブジェクト**として返すため、パイプラインで `Where-Object` でフィルタリングしたり、`Set-NetFirewallRule` に渡して変更したりといった、プログラム的な操作が圧倒的に強力です。 |

## よく連携されるコマンドレット

### シナリオ例: 無効化されている全ての受信規則を検索して有効化する (インフラ構築・運用視点)

* **目的**: 現在無効 (`Enabled -eq 'False'`) になっている全ての受信 (`Direction -eq 'Inbound'`) 規則を検索し、それらを有効にする。
* **連携コマンドレット**: `Where-Object`, `Enable-NetFirewallRule`
* **解説**: `Get-NetFirewallRule` で全ての規則を取得し、パイプラインで `Where-Object` に渡して条件（無効かつ受信）でフィルタリングします。最後に、条件に一致した規則オブジェクトを `Enable-NetFirewallRule` に渡して有効化します。
* **コマンド例**:

    ```powershell
    # 無効になっている受信規則を全て検索し、有効化する（-WhatIfで事前確認）
    Get-NetFirewallRule -Direction Inbound | Where-Object { $_.Enabled -eq 'False' } | Enable-NetFirewallRule -WhatIf
    ```

## パラメータ説明

| パラメータ | 型 | 説明 |
| :--- | :--- | :--- |
| **`-Name`** | `String` | 取得する規則の名前を指定します。 |
| **`-DisplayName`** | `String` | 取得する規則の表示名を指定します。ワイルドカード (`*`) が使用できます。 |
| ⭐ **`-Enabled`** | `Enabled` | 規則が有効か無効かでフィルタリングします (`True` または `False`)。 |
| ⭐ **`-Direction`** | `Direction` | 通信の方向でフィルタリングします (`Inbound` または `Outbound`)。 |
| ⭐ **`-Action`** | `Action` | 規則のアクションでフィルタリングします (`Allow` または `Block`)。 |
| **`-Profile`** | `Profile` | 規則が適用されるプロファイルでフィルタリングします (`Domain`, `Private`, `Public`)。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: リモートデスクトップ (RDP) 接続を許可している規則が有効になっているかを確認する。
* **組み合わせ**: `Get-NetFirewallRule -DisplayGroup "Remote Desktop"`
* **解説**: `DisplayGroup` を使うと、特定の機能に関連する規則（この場合はリモートデスクトップ関連）をまとめて取得できます。
* **例**:

    ```powershell
    # "Remote Desktop" グループに属するファイアウォール規則の状態を確認
    Get-NetFirewallRule -DisplayGroup "Remote Desktop" | Select-Object Name, Enabled, Direction, Profile
    ```

### 2. ブルーチーム視点

* **タスク**: **ファイアウォール設定の監査**。意図せず許可 (`Allow`) されている受信 (`Inbound`) 規則がないかを確認する。
* **組み合わせ**: `Get-NetFirewallRule -Action Allow -Direction Inbound`
* **解説**: このコマンドで、外部からの通信を許可している全ての規則をリストアップし、不要なポートが開いていないか、あるいは攻撃者によって作成された不審な許可ルールがないかを監査します。
* **例**:

    ```powershell
    # 全ての受信許可ルールを取得し、ローカルポートでソートして表示
    Get-NetFirewallRule -Action Allow -Direction Inbound | Sort-Object -Property { [int]($_.LocalPort | Select-Object -First 1) }
    ```

### 3. レッドチーム視点

* **タスク**: **偵察 (Reconnaissance)** と**防御回避**。
* **組み合わせ**: `Get-NetFirewallRule`
* **解説**:
  * **偵察**: 攻撃者は侵入後、`Get-NetFirewallRule` を実行して、どのような通信が許可・ブロックされているかを把握します。これにより、C2サーバーとの通信に使用できるポートを探したり、データ窃取に利用できる経路を特定したりします。
  * **防御回避**: `New-NetFirewallRule` や `Set-NetFirewallRule` を使って、自身の活動に必要な通信を許可するルールを追加したり、既存のルールを変更したりします。
* **例**:

    ```powershell
    # アウトバウンド通信を許可しているルールを全てリストアップして偵察
    Get-NetFirewallRule -Action Allow -Direction Outbound
    ```

## エラーメッセージとトラブルシューティング

### よくあるエラーメッセージ

* `Get-NetFirewallRule` は読み取り専用のコマンドレットであり、管理者権限がなくても実行できるため、エラーが発生することは稀です。

## セキュリティに関する考慮事項

### 悪用事例

* **偵察による防御機構の把握**: レッドチームのシナリオで述べた通り、`Get-NetFirewallRule` は攻撃者がシステムのネットワーク防御策を理解するための重要な偵察ツールです。許可されているポートやプロトコルを把握することで、検知されにくい通信経路を選択することが可能になります。
* **設定変更による防御回避**: `New-NetFirewallRule` や `Set-NetFirewallRule` と組み合わせて、攻撃者はファイアウォールに穴を開け、C2通信やラテラルムーブメントを容易にします。

### LOLBASにおける利用例

* **機能**: **偵察 (Reconnaissance)**
* **解説**: `Get-NetFirewallRule` は PowerShell の標準機能であり、攻撃者が環境に溶け込みながら、システムのファイアウォール設定を調査するために利用されます（Living off the Land）。

### 対応策・緩和策 (ブルーチーム視-

**Prevention (予防)**: グループポリシー (GPO) を使って、重要なファイアウォールポリシーを組織全体で一元的に強制適用し、ローカルでの安易な変更を防ぐ。

* **Detection (検知)**: PowerShellのスクリプトブロックロギング (イベントID 4104) を有効化し、`Get-NetFirewallRule` の実行を記録・監視する。Windows セキュリティイベントログで、ファイアウォール規則の変更 (イベントID 4907, 4910) や、ファイアウォールサービスの停止などを厳重に監視する。

## 注意点・補足

* このコマンドレットは `NetSecurity` モジュールに含まれています。これは近年のWindowsバージョンにはデフォルトでインストールされています。

---
[PowerShellインデックスに戻る](../../powershell_index.md)

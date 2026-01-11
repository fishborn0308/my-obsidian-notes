---
tags:
  - 'New-NetFirewallRule'
  - 'powershell'
  - 'cheatsheet'
title: 'New-NetFirewallRule - 新しいファイアウォール規則を作成する'
summary: 'Windows Defender ファイアウォールに新しい受信または送信規則を作成します。'
related:
  - 'Get-NetFirewallRule'
  - 'Set-NetFirewallRule'
  - 'Remove-NetFirewallRule'
  - 'netsh advfirewall firewall add rule'
---

# `New-NetFirewallRule` - 新しいファイアウォール規則を作成する

## 概要

`New-NetFirewallRule` コマンドレットは、Windows Defender ファイアウォールに新しい受信（Inbound）または送信（Outbound）の規則を作成します。このコマンドレットを使うことで、特定のプロトコル、ポート、プログラム、IPアドレスに基づいて通信を許可 (`Allow`) またはブロック (`Block`) するルールを、スクリプトから自動的に作成できます。サーバーのセットアップ自動化や、セキュリティポリシーの適用に不可欠なコマンドレットです。

## 類似コマンドレット/コマンドとの差異

| 類似コマンドレット/コマンド | 差異 / 使い分けのポイント |
| :--- | :--- |
| **`netsh advfirewall firewall add rule` (CMD)** | `netsh` はCMDベースのコマンドです。`New-NetFirewallRule` はPowerShellのオブジェクト指向の利点を活かせます。例えば、複数のポートやIPアドレスを配列としてパラメータに渡すなど、より柔軟でスクリプトに適した構文を持っています。 |
| **`Set-NetFirewallRule`** | `Set-NetFirewallRule` は**既存の**規則を変更します。`New-NetFirewallRule` は**新しい**規則を作成します。 |

## よく連携されるコマンドレット

### シナリオ例: 規則を作成し、すぐにその内容を確認する (インフラ構築・運用視点)

* **目的**: 新しいファイアウォール規則を作成し、それが正しく登録されたかを確認する。
* **連携コマンドレット**: `Get-NetFirewallRule`
* **解説**: `New-NetFirewallRule` を実行して新しい規則を作成します。その後、`-DisplayName` パラメータで同じ名前を指定して `Get-NetFirewallRule` を実行し、作成した規則のプロパティが意図通りに設定されているかを確認します。
* **コマンド例**:

    ```powershell
    # カスタムWebアプリ用にTCPポート8080を開くルールを作成
    New-NetFirewallRule -DisplayName "WebApp Port" -Direction Inbound -Action Allow -Protocol TCP -LocalPort 8080

    # 作成したルールが正しく設定されているか確認
    Get-NetFirewallRule -DisplayName "WebApp Port" | Format-List -Property DisplayName, Enabled, Direction, Action, Protocol, LocalPort
    ```

## パラメータ説明

| パラメータ | 型 | 説明 |
| :--- | :--- | :--- |
| ⭐ **`-DisplayName`** | `String` | 規則の分かりやすい表示名を指定します。 |
| ⭐ **`-Direction`** | `Direction` | 通信の方向を指定します (`Inbound` または `Outbound`)。 |
| ⭐ **`-Action`** | `Action` | 規則のアクションを指定します (`Allow` または `Block`)。 |
| **`-Protocol`** | `String` | プロトコルを指定します (`TCP`, `UDP`, `ICMPv4`, `ICMPv6` など)。 |
| ⭐ **`-LocalPort`** | `String[]` | ローカルポート番号を指定します。カンマ区切りで複数指定、ハイフンで範囲指定が可能です。（例: `80, 443`, `8000-8080`） |
| **`-RemotePort`** | `String[]` | リモートポート番号を指定します。 |
| **`-LocalAddress`** | `String[]` | ローカルIPアドレスを指定します。 |
| ⭐ **`-RemoteAddress`** | `String[]` | リモートIPアドレスを指定します。特定のIPからのアクセスを許可/ブロックする場合に使用します。 |
| **`-Program`** | `String` | この規則を適用するプログラムの実行可能ファイルのパスを指定します。 |
| **`-Profile`** | `Profile` | 規則を適用するネットワークプロファイルを指定します (`Domain`, `Private`, `Public`, `Any`)。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: 新しいWebサーバーを構築し、HTTP (80) と HTTPS (443) の受信を許可する。
* **組み合わせ**: `-Direction Inbound -Action Allow -Protocol TCP -LocalPort <Ports>`
* **解説**: Webサーバーとして機能するために必要な基本的なポートを開放します。
* **例**:

    ```powershell
    # Webトラフィックを許可するルールを作成
    New-NetFirewallRule -DisplayName "Allow Web Inbound" -Direction Inbound -Action Allow -Protocol TCP -LocalPort 80, 443
    ```

### 2. ブルーチーム視点

* **タスク**: **インシデント対応における封じ込め**。脅威インテリジェンスで特定された、既知の悪意のあるIPアドレスからの全ての受信通信をブロックする。
* **組み合わせ**: `-Direction Inbound -Action Block -RemoteAddress <IP>`
* **解説**: C2サーバーや攻撃元として特定されたIPアドレスを `-RemoteAddress` に指定し、`-Action Block` で通信を遮断することで、被害の拡大を防ぎます。
* **例**:

    ```powershell
    # 悪意のあるIP '198.51.100.10' からの全ての受信をブロック
    New-NetFirewallRule -DisplayName "Block Malicious IP - 20251019" -Direction Inbound -Action Block -RemoteAddress "198.51.100.10"
    ```

### 3. レッドチーム視点

* **タスク**: **ラテラルムーブメントとC2通信の確立**。
* **組み合わせ**: `-Direction Inbound/Outbound -Action Allow`
* **解説**: 攻撃者は管理者権限を奪取した後、`New-NetFirewallRule` を使ってファイアウォールに穴を開けます。例えば、RDP (ポート 3389) を外部に開放したり、C2サーバーへのアウトバウンド通信を特定のポートで許可したりして、自身の活動を容易にします。正規のコマンドレットを使うため、アンチウイルスによる検知を回避しやすいです。
* **例**:

    ```powershell
    # ペイロードがC2サーバーと通信するために、アウトバウンドのTCP 4444を許可
    New-NetFirewallRule -DisplayName "Allow C2 Outbound" -Direction Outbound -Action Allow -Protocol TCP -RemotePort 4444
    ```

## エラーメッセージとトラブルシューティング

### よくあるエラーメッセージ

1. **エラーメッセージ**: `New-NetFirewallRule : Access is denied.`
    * **考えられる原因**: ファイアウォール規則の作成には管理者権限が必要です。
    * **解決策**: PowerShellを「管理者として実行」して、再度コマンドを実行してください。

## セキュリティに関する考慮事項

### 悪用事例

* **防御回避と永続化**: レッドチームのシナリオで述べた通り、`New-NetFirewallRule` は攻撃者がシステムのネットワーク防御を無効化し、外部との通信経路を確保するための常套手段です。

### LOLBASにおける利用例

* **機能**: **防御回避 (Defense Evasion)**, **永続化 (Persistence)**
* **解説**: `New-NetFirewallRule` は PowerShell の標準機能であり、`netsh` と同様に、攻撃者が環境に溶け込みながらファイアウォール設定を変更するために利用されます（Living off the Land）。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: グループポリシー (GPO) を使って、重要なファイアウォールポリシーを組織全体で一元的に強制適用し、ローカルでの安易な変更を防ぐ。
* **Detection (検知)**: Windows イベントログで、**新しいファイアウォール規則が追加されたことを示すイベントID 4907** を厳重に監視する。PowerShellのスクリプトブロックロギング (イベントID 4104) を有効化し、`New-NetFirewallRule` の実行を記録・監視する。

## 注意点・補足

* **最小権限の原則**: ファイアウォール規則を作成する際は、常に最小権限の原則に従ってください。ポートを開放する場合は、`-RemoteAddress` で接続元IPを可能な限り限定するなど、必要最小限の範囲で許可することがセキュリティ上重要です。

---
[PowerShellインデックスに戻る](../../powershell_index.md)

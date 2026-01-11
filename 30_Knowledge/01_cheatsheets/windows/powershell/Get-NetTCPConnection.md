---
tags:
  - 'Get-NetTCPConnection'
  - 'powershell'
  - 'cheatsheet'
title: 'Get-NetTCPConnection - TCP接続の情報を取得する'
summary: 'システムのアクティブなTCP接続と待ち受けポートに関する詳細な情報を取得します。'
related:
  - 'Get-NetUDPEndpoint'
  - 'Get-Process'
  - 'netstat'
---

# `Get-NetTCPConnection` - TCP接続の情報を取得する

## 概要

`Get-NetTCPConnection` コマンドレットは、ローカルコンピュータのTCP接続に関する情報を取得します。これには、ローカルおよびリモートのIPアドレスとポート、接続の状態 (`Listen`, `Established`, `TimeWait`など)、および各接続に関連付けられたプロセスID (PID) が含まれます。

このコマンドレットは、CMDの `netstat` コマンドに相当するモダンなPowerShellの代替であり、結果をオブジェクトとして返すため、フィルタリング、ソート、他のコマンドレットとの連携が非常に強力です。

## 類似コマンドレット/コマンドとの差異

| 類似コマンドレット/コマンド | 差異 / 使い分けのポイント |
| :--- | :--- |
| **`netstat` (CMD)** | `netstat` は接続情報を**テキスト**として表示します。`Get-NetTCPConnection` は各接続を**オブジェクト**として返すため、`State` や `RemotePort` などのプロパティでプログラム的にフィルタリングしたり、`Get-Process` と連携したりするのが容易です。 |
| **`Get-Process`** | `Get-Process` はプロセス自体の情報（CPU、メモリ使用量など）を取得します。`Get-NetTCPConnection` は、そのプロセスが行っている**ネットワーク接続**の情報を取得します。これらは `-OwningProcess` プロパティを通じて関連付けられています。 |

## よく連携されるコマンドレット

### シナリオ例: 特定のプロセスが行っているネットワーク接続を特定する (ブルーチーム視点)

* **目的**: 不審なプロセス (`suspicious.exe`) が、どのリモートIPアドレスと通信しているかを特定する。
* **連携コマンドレット**: `Get-Process`
* **解説**: まず `Get-Process` で対象のプロセスのPIDを取得します。次に、`Get-NetTCPConnection` の出力から、`OwningProcess` プロパティがそのPIDと一致する接続のみをフィルタリングします。
* **コマンド例**:

    ```powershell
    # 'suspicious' という名前のプロセスのPIDを取得
    $proc = Get-Process -Name "suspicious"

    # そのPIDに関連付けられたTCP接続を全て表示
    Get-NetTCPConnection | Where-Object { $_.OwningProcess -eq $proc.Id }
    ```

## パラメータ説明

| パラメータ | 型 | 説明 |
| :--- | :--- | :--- |
| **`-LocalAddress`** | `String[]` | フィルタリング対象のローカルIPアドレスを指定します。 |
| **`-LocalPort`** | `UInt16[]` | フィルタリング対象のローカルポート番号を指定します。 |
| **`-RemoteAddress`** | `String[]` | フィルタリング対象のリモートIPアドレスを指定します。 |
| **`-RemotePort`** | `UInt16[]` | フィルタリング対象のリモートポート番号を指定します。 |
| ⭐ **`-State`** | `State` | TCP接続の状態でフィルタリングします (`Listen`, `Established`, `CloseWait` など)。 |
| ⭐ **`-OwningProcess`** | `UInt32` | 接続を所有しているプロセスのID (PID) を指定します。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: Webサーバーがポート 80 と 443 で正しく待ち受け (`Listen`) 状態になっているかを確認する。
* **組み合わせ**: `Get-NetTCPConnection -State Listen`
* **解説**: `-State Listen` で待ち受け中のポートのみに絞り込み、`LocalPort` を確認することで、サービスが正しく起動し、ポートをバインドしているかを監査します。
* **例**:

    ```powershell
    # 待ち受け状態のポートを全て表示し、ポート番号でソート
    Get-NetTCPConnection -State Listen | Sort-Object -Property LocalPort
    ```

### 2. ブルーチーム視点

* **タスク**: **ライブレスポンス**。マルウェアのC2通信など、不審な外部へのTCP接続を発見する。
* **組み合わせ**: `Get-NetTCPConnection -State Established`
* **解説**: **インシデント対応の基本**。`-State Established` で現在確立されている全ての接続をリストアップし、`RemoteAddress` と `RemotePort` を精査します。見慣れないIPアドレスや、一般的でないポートへの接続は、マルウェアの活動を示唆している可能性があります。`OwningProcess` からプロセスを特定し、さらなる調査につなげます。
* **例**:

    ```powershell
    # 確立済みの接続を、プロセス情報と合わせて表示
    Get-NetTCPConnection -State Established | Select-Object -Property LocalPort, RemoteAddress, RemotePort, State, OwningProcess, @{Name="ProcessName";Expression={(Get-Process -Id $_.OwningProcess -ErrorAction SilentlyContinue).ProcessName}}
    ```

### 3. レッドチーム視点

* **タスク**: **偵察 (Reconnaissance)**。侵入したホストのネットワーク活動を把握する。
* **組み合わせ**: `Get-NetTCPConnection`
* **解説**: 攻撃者は侵入後、`Get-NetTCPConnection` を実行して、そのサーバーがどの他のサーバー（データベース、ドメインコントローラー、ファイルサーバーなど）と通信しているかを特定します。これにより、内部ネットワークの構成をマッピングし、次のラテラルムーブメント（横展開）の標的を探します。
* **例**:

    ```powershell
    # 内部のプライベートIPアドレス範囲への接続をリストアップして偵察
    Get-NetTCPConnection -State Established | Where-Object { $_.RemoteAddress -like "10.*" -or $_.RemoteAddress -like "192.168.*" }
    ```

## エラーメッセージとトラブルシューティング

* `Get-NetTCPConnection` は読み取り専用のコマンドレットであり、エラーが発生することは稀です。

## セキュリティに関する考慮事項

### 悪用事例

* **偵察 (Reconnaissance)**: レッドチームのシナリオで述べた通り、`Get-NetTCPConnection` は攻撃者がシステムのネットワーク接続状況、通信相手、稼働中のサービスなどを把握するための非常に価値の高い偵察ツールです。

### LOLBASにおける利用例

* **機能**: **偵察 (Reconnaissance)**
* **解説**: `Get-NetTCPConnection` は PowerShell の標準機能であり、`netstat.exe` と同様に、攻撃者が環境に溶け込みながらネットワーク活動を調査するために利用されます（Living off the Land）。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: -
* **Detection (検知)**: PowerShellのスクリプトブロックロギング (イベントID 4104) を有効化し、`Get-NetTCPConnection` の実行を記録・監視する。EDRやネットワーク監視ツールで、予期せぬポートでの待ち受けや、不審な外部IPへの接続を検知する。

## 注意点・補足

* **モジュール**: このコマンドレットは `NetTCPIP` モジュールに含まれています。これは近年のWindowsバージョンにはデフォルトでインストールされています。

---
[PowerShellインデックスに戻る](../../powershell_index.md)

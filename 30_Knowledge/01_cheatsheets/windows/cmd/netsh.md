---
tags:
  - 'netsh'
  - 'cmd'
  - 'cheatsheet'
title: 'netsh - ネットワーク構成を管理する'
summary: 'Windowsのネットワークコンポーネント（IPアドレス、ファイアウォール、インターフェースなど）を表示・設定するための、非常に強力で包括的なコマンドラインスクリプティングユーティリティです。'
related:
  - 'ipconfig'
  - 'route'
  - 'powershell'
---

# `netsh` - ネットワーク構成を管理する

## 概要

`netsh` (Network Shell) は、ローカルまたはリモートのコンピュータ上で、実行中のほぼ全てのネットワークコンポーネントの構成を表示または変更することができる、コマンドラインスクリプティングユーティリティです。`netsh` は「コンテキスト」と呼ばれる階層構造を持ち、`netsh interface ip ...` や `netsh advfirewall firewall ...` のように、操作したい対象を選択しながらコマンドを実行します。対話モードとバッチモードの両方をサポートしており、ネットワーク設定の自動化に不可欠なツールです。

(出自: `Windows標準搭載`)

## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 (あれば) |
| :--- | :--- | :--- |
| **`ipconfig` / `route`** | これらのコマンドは主にネットワーク情報の**表示**や一時的な更新に利用されます。`netsh` は、永続的なIPアドレス設定やファイアウォールルールの作成など、より高度で**永続的な設定変更**が可能です。 | `netsh` |
| **PowerShell** | `Get-NetAdapter`, `Set-NetIPAddress`, `New-NetFirewallRule` などのコマンドレットは、ネットワーク設定をオブジェクトとして扱え、より最新で強力なスクリプティング機能を提供します。 | PowerShell |

## よく連携されるコマンド

`netsh` は、設定をスクリプトファイルとしてエクスポート・インポートする機能を持っており、複数のマシンに同じ構成を適用する際に便利です。

### シナリオ例: ファイアウォールルールのバックアップとリストア (インフラ構築・運用視点)

* **目的**: あるサーバーのWindows Defender ファイアウォールのルールを全てエクスポートし、別のサーバーに同じルールをインポート（複製）する。
* **連携コマンド**: (なし)
* **解説**: `netsh advfirewall export` で現在のルールを `.wfw` ファイルにバックアップし、それを別のサーバーにコピーして `import` することで、ファイアウォール構成を簡単に複製できます。
* **コマンド例**:

    ```cmd
    REM 現在のファイアウォールルールを C:\temp\fw_rules.wfw にエクスポート
    netsh advfirewall export "C:\temp\fw_rules.wfw"

    REM (別のサーバーで) ファイルからルールをインポート
    netsh advfirewall import "C:\temp\fw_rules.wfw"
    ```

## スイッチ/オプション説明

`netsh` は `netsh <コンテキスト> <サブコンテキスト> <コマンド>` の形式で実行します。

| コンテキスト | 説明 |
| :--- | :--- |
| **`interface`** | ネットワークインターフェイス（イーサネット、Wi-Fiなど）に関連する設定。 |
| ⭐ `interface ip` | IPv4またはIPv6のアドレス、DNSサーバー、WINSサーバーなどを設定します。 |
| **`advfirewall`** | Windows Defender Advanced Firewall の設定。 |
| ⭐ `advfirewall firewall` | ファイアウォールルールの追加、削除、変更を行います。 |
| **`wlan`** | ワイヤレスネットワークプロファイルの表示や接続設定。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: サーバーのネットワークインターフェイスに静的IPアドレスを設定する。
* **組み合わせ**: `netsh interface ip set address`
* **解説**: GUIを使わずに、コマンドラインからサーバーのIPアドレス、サブネットマスク、ゲートウェイを一度に設定します。サーバー構築の自動化スクリプトで多用されます。
* **例**:

    ```cmd
    REM "Ethernet" インターフェースに静的IPを設定
    netsh interface ip set address name="Ethernet" static 192.168.1.100 255.255.255.0 192.168.1.1
    REM DNSサーバーも設定
    netsh interface ip set dns name="Ethernet" static 8.8.8.8
    ```

### 2. ブルーチーム視点

* **タスク**: 現在有効なファイアウォールルールを全てリストアップし、不審な穴がないか監査する。
* **組み合わせ**: `netsh advfirewall firewall show rule name=all`
* **解説**: システムのファイアウォール設定を網羅的に確認し、意図せず許可されているポートやプログラムがないかを調査します。
* **例**:

    ```cmd
    REM 全てのファイアウォールルールを表示し、出力をファイルに保存して分析
    netsh advfirewall firewall show rule name=all dir=in verbose > firewall_rules.txt
    ```

### 3. レッドチーム視点

* **タスク**: **ラテラルムーブメント (Lateral Movement)** や **C2通信** のために、ファイアウォールに穴を開ける。
* **組み合わせ**: `netsh advfirewall firewall add rule`
* **解説**: 攻撃者は管理者権限を奪取した後、`netsh` を使ってファイアウォールルールを変更します。RDP (ポート 3389) を許可してリモートアクセスを容易にしたり、C2サーバーへのアウトバウンド通信を許可したり、あるいはファイアウォール自体を無効化したりします。
* **例**:

    ```cmd
    REM 外部からの RDP 接続を許可するルールを追加
    netsh advfirewall firewall add rule name="Allow RDP" dir=in action=allow protocol=TCP localport=3389

    REM すべてのプロファイルでファイアウォールを無効化
    netsh advfirewall set allprofiles state off
    ```

## エラーメッセージとトラブルシューティング

### よくあるエラーメッセージ

1. **エラーメッセージ**: `The requested operation requires elevation.`
    * **考えられる原因**: ネットワーク設定の変更には管理者権限が必要です。
    * **解決策**: コマンドプロンプトを「管理者として実行」して、再度コマンドを実行してください。

## 環境変数と設定ファイル

* `netsh` は、その動作に影響を与える特定の環境変数や設定ファイルを使用しません。
* 一般的な環境変数は、[CMD共通の環境変数・設定ファイル](./environment_and_config.md)で解説。

## セキュリティに関する考慮事項

### 脆弱性と悪用事例

* **脆弱性**: `netsh` 自体ではなく、**ファイアウォール設定の不備**や**不正な変更**がセキュリティリスクとなります。
* **悪用シナリオ**: レッドチームのシナリオで述べた通り、攻撃者は `netsh` を使ってファイアウォールに穴を開け、攻撃の足がかりとしたり、活動を容易にしたりします。

### LOLBAS (Living Off The Land Binaries and Scripts) における利用例

* **機能**: **偵察 (Reconnaissance)**, **防御回避 (Defense Evasion)**, **永続化 (Persistence)**
* **参照**: [https://lolbas-project.github.io/lolbas/Binaries/Netsh/](https://lolbas-project.github.io/lolbas/Binaries/Netsh/)
* **解説**: `netsh` は、ファイアウォールルールの変更による防御回避だけでなく、**ポートプロキシ機能** (`portproxy`) を使って通信を転送させたり、ヘルパーDLLをロードさせて永続化を図ったりと非常に多様な攻撃手法に悪用される環境寄生型バイナリです。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: 最小権限の原則を徹底し、不要なユーザーにネットワーク設定の変更権限を与えない。
* **Detection (検知)**: Windows セキュリティイベントログで、ファイアウォールルールの変更 (イベントID 4907, 4910) や、ファイアウォールサービスの停止 (イベントID 4950) などを厳重に監視する。`netsh.exe` の実行をプロセス監査ログで監視する。

## 注意点・補足

* **対話モード**: `netsh` を引数なしで実行すると、対話モードに入ります。ここで `advfirewall`, `firewall` のようにコンテキストを移動しながらコマンドを実行することも可能です。

---
[CMDインデックスに戻る](../../cmd_index.md)

---
tags:

- log
- windows
- cheatsheet
---

# windows\_log\_files - Windowsにおける主要ログファイルと活用術

## 概要

Windowsオペレーティングシステムが生成するログは、システムの動作状況、アプリケーションのエラー、セキュリティイベントなどを記録しています。これらのログは、問題の診断、パフォーマンスの最適化、そしてサイバー攻撃の検知と分析に不可欠です。

## 1\. Windowsログ管理の基本

Windowsにおけるログ管理の仕組みと、基本的な概念について説明します。

### 1.1. イベントログの仕組み

* **イベントログ**: Windowsのログ管理の中心的な仕組み。ログはバイナリ形式で記録され、イベントビューアーやPowerShellで参照します。
* **イベントログの種類**:
  * **Application(アプリケーション)**: アプリケーションからのイベント。
  * **Security(セキュリティ)**: ログオン/ログオフ、ファイルアクセス、特権利用など、セキュリティ監査ポリシーに基づいて記録されるイベント。
  * **System(システム)**: Windowsシステムコンポーネントからのイベント。起動/シャットダウン、サービスエラー、ドライバーの問題など。
  * **Setup(セットアップ)**: Windowsのインストールや更新に関するイベント。
  * **Forwarded Events(転送されたイベント)**: 他のコンピュータから転送されたイベント。
  * **Applications and Services Logs(アプリケーションとサービスログ)**: 特定のアプリケーションやサービス(例: Microsoft-Windows-PowerShell, Microsoft-Windows-SMBServer)の詳細なログ。
* **イベントID**: 各イベントを識別する一意の番号。
* **ソース(Source)**: イベントを記録したアプリケーションやコンポーネント。
* **レベル(Level)**: イベントの重要度(情報、警告、エラー、成功の監査、失敗の監査)。

### 1.2. ログファイルの保存場所と形式

* イベントログは通常、以下のディレクトリに`.evtx`形式のバイナリファイルとして保存されます。
  * `C:\Windows\System32\winevt\Logs\`
* テキストベースのログファイル(IISログ、SQL Serverログなど)についても言及します。

### 1.3. ログの管理と設定

* **イベントビューアー(Event Viewer)**: GUIによるログの閲覧、フィルタリング、エクスポート。
* **ログのサイズと保持期間**: 各イベントログの最大サイズと、古いイベントを上書きするかどうかの設定(グループポリシーまたはレジストリ)。
* **監査ポリシー(Audit Policy)**: どのようなセキュリティイベントをログに記録するかを設定する機能。特にセキュリティログの充実のために重要。
  * ローカルセキュリティポリシー(`secpol.msc`)またはグループポリシーで設定。
  * 主な監査カテゴリ(アカウントログオン、アカウント管理、ディレクトリサービスアクセスなど)。

## 2\. 主要なイベントログの詳細

各イベントログの目的、内容、よく見るイベントID、関連するトラブルシューティングやセキュリティ分析のポイントについて説明します。

### 2.1. System(システム) ログ

* **目的**: OSコンポーネント、デバイスドライバー、サービスに関するイベント。
* **内容**:
  * システム起動・シャットダウン(イベントID 6005/6006)
  * サービス起動・停止、エラー
  * ハードウェアエラー
  * ネットワークアダプターの状態変化
* **重要イベントID例**:
  * **7000-7045**: サービスエラー
  * **1000-1002**: アプリケーションクラッシュ(Error Reporting)
* **活用シナリオ**: システムの不安定性、サービス障害、ドライバー問題の診断。

### 2.2. Security(セキュリティ) ログ

* **目的**: セキュリティ監査ポリシーに基づいて記録されるイベント。最も重要なセキュリティログ。
* **内容**:
  * **ログオン/ログオフ**:(イベントID 4624/4625/4634/4647)- 成功/失敗ログオン、ログオフ、セッション切断。
  * **アカウント管理**:(イベントID 4720/4722/4726など)- ユーザーアカウントの作成、変更、削除。
  * **プロセス追跡**:(イベントID 4688)- プロセス起動。
  * **ファイル/フォルダアクセス**:(イベントID 4656/4663)- オブジェクトアクセスの成功/失敗。
  * **特権の使用**:(イベントID 4672)- 特権割り当て。
  * **グループメンバーシップ変更**:(イベントID 4728/4732/4756など)
* **重要イベントID例**:
  * **4624**: ログオン成功
  * **4625**: ログオン失敗
  * **4688**: 新規プロセス作成(コマンドライン含む)
  * **4720**: ユーザーアカウント作成
  * **4732**: セキュリティ対応グローバルグループのメンバー追加
* **活用シナリオ**: 不正アクセス試行の検知、特権昇格の監視、マルウェア活動の追跡。

### 2.3. Application(アプリケーション) ログ

* **目的**: インストールされたアプリケーションからのイベント。
* **内容**:
  * アプリケーションエラー、警告、情報メッセージ
  * データベースからのメッセージ
  * Webサーバ(IIS)関連のアプリケーションエラー(IISの個別ログとは別)
* **活用シナリオ**: アプリケーションの障害診断、機能の健全性監視。

### 2.4. Applications and Services Logs(アプリケーションとサービスログ)

* **目的**: 特定のアプリケーションやサービスに特化した詳細なログ。
* **内容**:
  * **Microsoft-Windows-PowerShell/Operational**: PowerShellスクリプトの実行、モジュールロードなど。
  * **Microsoft-Windows-SmbServer/Operational**: SMB共有へのアクセス、エラーなど。
  * **Microsoft-Windows-TerminalServices-LocalSessionManager/Operational**: リモートデスクトップセッションの接続/切断など。
* **活用シナリオ**: 特定のサービスの詳細な動作監視、セキュリティ調査における詳細な証跡。

### 2.5. その他の重要なログファイル

* **IISログ(Internet Information Services)**:
  * `C:\inetpub\logs\LogFiles\` 以下
  * WebサーバへのHTTPリクエストの詳細(クライアントIP、User-Agent、リクエストURL、HTTPステータスコードなど)。
* **Firewallログ(Windows Defender Firewall)**:
  * デフォルトでは有効になっていないが、有効にすると接続試行のログを記録。
  * `C:\Windows\System32\LogFiles\Firewall\` などに保存されることが多い。
* **Sysmonログ(Sysmon)**:
  * Microsoft Sysinternalsのツールで、インストールすると追加の詳細なシステムアクティビティログをSecurityイベントログに記録。
  * プロセス作成、ネットワーク接続、ファイル作成、レジストリ変更など、セキュリティ調査で非常に有用。
  * **イベントID**: 1(Process Create), 3(Network Connection), 11(FileCreate), 13(RegistryEvent)など。

## 3\. ログの閲覧とフィルタリング

Windowsログを効率的に分析するためのツールとテクニック。

### 3.1. イベントビューアー(GUI)

* ログのカテゴリ選択、フィルタリング(イベントID、ソース、レベル、時間範囲)、カスタムビューの作成、エクスポート。

### 3.2. PowerShell

* **`Get-WinEvent`**: イベントログの取得。
  * **基本的な使い方**: `Get-WinEvent -LogName Security`
  * **フィルタリング**: `-FilterHashtable`(イベントID、プロバイダー名、レベル、時間範囲など)
  * **XMLクエリ**: `-FilterXPath`
* **`Where-Object`**: 取得したログオブジェクトのフィルタリング。
* **`Select-Object`**: 必要なプロパティの抽出。
* **`Format-Table` / `Format-List`**: 出力形式の整形。
* **`Export-Csv` / `ConvertTo-Json`**: データのエクスポート。

### 3.3. `wevtutil.exe`(コマンドライン)

* イベントログのクエリ、エクスポート、クリア、設定変更。
* **基本的な使い方**: `wevtutil.exe query-events Security`
* **XMLクエリ**: `wevtutil qe Security /query:"<QueryList>...</QueryList>"`

## 4\. ログの活用シナリオ

インフラ構築・運用、ブルーチーム、レッドチームそれぞれの視点からWindowsログの具体的な活用方法を詳述します。

### 4.1. インフラ構築・運用視点

* **トラブルシューティング**:
  * システムクラッシュ(ブルースクリーン)の原因特定(Systemログのエラーイベント)。
  * サービスが起動しない、停止する問題(Systemログ、Applicationログ)。
  * ディスクエラーやハードウェア障害の検知(Systemログ)。
* **パフォーマンス監視**:
  * アプリケーションの応答性低下の原因調査(Applicationログ)。
  * イベントログの肥大化によるディスク容量圧迫の監視。
* **定常運用における確認**:
  * 定期的なバックアップジョブの成功/失敗。
  * パッチ適用後のシステム安定性確認。
* **PowerShellコマンド例**:

    ```powershell
    # 過去24時間のエラーイベントを取得
    Get-WinEvent -LogName System -FilterHashTable @{Level=2; StartTime=(Get-Date).AddDays(-1)}

    # アプリケーションクラッシュイベント(イベントID 1000) を確認
    Get-WinEvent -LogName Application -ID 1000 | Format-List TimeCreated, Message
    ```

### 4.2. ブルーチーム視点

* **不正アクセス検知**:
  * ログオン失敗試行の監視(Securityログ: イベントID 4625)。
  * リモートデスクトップの不正なログオン(Securityログ: イベントID 4624, Applications and Services Logs: TerminalServices-LocalSessionManager)。
  * 新規ユーザーアカウントの作成、グループメンバーシップの変更(Securityログ: イベントID 4720, 4732など)。
* **マルウェア活動の追跡**:
  * 不審なプロセスの起動(Securityログ: イベントID 4688, Sysmonログ: イベントID 1)。
  * ネットワーク接続の異常(Securityログ: イベントID 5156, Sysmonログ: イベントID 3)。
  * ファイル改ざんやレジストリ変更(Sysmonログ: イベントID 11, 13)。
* **特権昇格の監視**:
  * `PsExec`などの管理ツール利用時のプロセス作成。
  * 管理者グループへのユーザー追加。
* **フォレンジック調査**:
  * 攻撃経路、侵入時刻、実行されたコマンドの特定。
  * 攻撃者が残した痕跡(ファイル、レジストリ)の検出。
* **PowerShellコマンド例**:

    ```powershell
    # ログイン失敗イベントをログオンタイプ別にカウント
    Get-WinEvent -LogName Security -ID 4625 | Group-Object -Property @{Expression={$_.Properties[8].Value}} -NoElement | Sort-Object Count -Descending

    # 新規作成されたプロセスとコマンドラインを確認(監査ポリシーで有効化されている場合)
    Get-WinEvent -LogName Security -ID 4688 | Select-Object TimeCreated, @{N='ProcessName';E={$_.Properties[5].Value}}, @{N='CommandLine';E={$_.Properties[8].Value}}

    # Sysmonのネットワーク接続ログを確認
    Get-WinEvent -LogName Microsoft-Windows-Sysmon/Operational -ID 3 | Select-Object TimeCreated, Image, DestinationIp, DestinationPort, Protocol
    ```

### 4.3. レッドチーム視点

* **情報収集(Reconnaissance)**:
  * イベントログからインストールされているアプリケーション、OSバージョン、ユーザー名などを推測。
  * IISログからWebアプリケーションのパスや脆弱性を探索。
* **痕跡消去(Anti-Forensics)**:
  * イベントログのクリア(例: `wevtutil cl Security`)。
  * `Clear-EventLog` PowerShellコマンドの使用。
  * 特定のイベントの削除(直接は困難だが、ログ転送を妨害するなど)。
* **永続化(Persistence)**:
  * ログに記録されにくい方法でのバックドア設置。
  * 正規のプロセスに偽装した活動。
* **PowerShellコマンド例**:

    ```powershell
    # イベントログのクリア(痕跡消去の例、管理者権限が必要)
    Clear-EventLog -LogName Security
    # または
    # wevtutil cl Security

    # ログ管理設定の確認(ログ容量などを確認し、ログを残りにくくする可能性を検討)
    Get-WinEvent -ListLog * | Where-Object {$_.LogName -eq "Security"} | Select-Object LogName, IsLogEnabled, LogMaxSize, OverflowAction
    ```

* **対応策・緩和策(ブルーチーム視点)**:
  * **監査ポリシーの強化**: セキュリティログが適切に取得されるよう、詳細な監査ポリシーを有効化する。
  * **ログの集中管理**: SIEM(Security Information and Event Management)やEDR(Endpoint Detection and Response)を導入し、ログを一元的に収集、相関分析、長期保存する。
  * **ログの改ざん防止**: ログ転送の暗号化、ログソースの保護、ログのハッシュ化や電子署名による完全性チェック。
  * **ログのリアルタイム監視とアラート**: 不審なイベントが記録された際に即座に通知する仕組みを構築する。
  * **時刻同期**: 正確なフォレンジック分析のためにNTPなどによる時刻同期を徹底する。

## 5\. ログの高度な管理と分析

大規模環境やセキュリティ強化のためのWindowsログ管理について説明します。

### 5.1. 集中ログ管理(SIEM/EDR連携)

* Windows Event Forwarding(WEF)を用いたログの集中転送。
* SIEM(Splunk, Elastic Stack, Microsoft Sentinelなど)やEDR(CrowdStrike, Microsoft Defender for Endpointなど)との連携による高度な分析。

### 5.2. カスタムログとアプリケーションログの設計

* 独自のアプリケーションでログを適切にEvent Logに出力する設計。
* カスタムログの作成と管理。

### 5.3. PowerShellスクリプトによる自動化

* 定期的なログのバックアップ、フィルタリング、レポート生成。
* 不審なイベントの自動検知と対応。

## 6\. 注意点・補足

* **ログの肥大化**: イベントログはディスク容量を消費するため、適切なサイズと保持期間の設定が重要です。
* **ログの解釈**: イベントログは詳細ですが、文脈を理解しないと正しい解釈が難しい場合があります。関連する複数のイベントやシステムの状況を総合的に判断する必要があります。
* **監査ポリシーの影響**: 監査ポリシーを有効化しすぎると、パフォーマンスに影響を与えたり、ログの量が膨大になりすぎたりする可能性があります。必要なイベントに絞って設定することが重要です。

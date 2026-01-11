
# powershell\_index - PowerShellコマンドとシステム管理の主要目次

## 概要

このドキュメントは、PowerShellに関するチートシート全体の目次です。Windowsシステム管理の中核をなすCmdletを中心に、ファイル操作、ネットワーク、プロセス、サービス、ユーザー管理、セキュリティ関連のタスク、スクリプティングまで、PowerShellを効率的に操作・管理・自動化するために必要な情報を網羅します。各項目は詳細なチートシートファイルへのリンクとなっています。

## 1\. 基本操作とコマンドレットのヘルプ

PowerShellの基本概念と、Cmdletの発見・利用方法に関する項目です。

* [`Get-Command`](windows/powershell/Get-Command.md) - 利用可能なコマンドレット、関数、エイリアスなどを取得
* [`Get-Help`](windows/powershell/Get-Help.md) - コマンドレットや関数の詳細なヘルプを表示
* [`Get-Member`](windows/powershell/Get-Member.md) - オブジェクトのプロパティとメソッドを表示(パイプラインで連携)
* [`Get-Alias`](windows/powershell/Get-Alias.md) - エイリアスの一覧表示
* [`Set-Alias`](windows/powershell/Set-Alias.md) - エイリアスの設定
* [`Clear-Host`](windows/powershell/Clear-Host.md) - 画面のクリア
* [`Read-Host`](windows/powershell/Read-Host.md) - ユーザーからの入力を取得
* [`Write-Host`](windows/powershell/Write-Host.md) - コンソールに文字列を出力
* [`Out-File`](windows/powershell/Out-File.md) - コマンドの出力をファイルに保存
* [`Set-Content`](windows/powershell/Set-Content.md) - ファイルに内容を書き込む

## 2\. ファイルとディレクトリ管理

ファイルとディレクトリの操作に関する基本的なCmdlet群です。

* [`Get-ChildItem`](windows/powershell/Get-ChildItem.md) - ファイルやディレクトリの一覧表示(`ls`, `dir`に相当)
* [`Set-Location`](windows/powershell/Set-Location.md) - ディレクトリの移動(`cd`に相当)
* [`Get-Location`](windows/powershell/Get-Location.md) - 現在の作業ディレクトリ表示(`pwd`に相当)
* [`New-Item`](windows/powershell/New-Item.md) - ファイルやディレクトリの作成
* [`Remove-Item`](windows/powershell/Remove-Item.md) - ファイルやディレクトリの削除
* [`Copy-Item`](windows/powershell/Copy-Item.md) - ファイルやディレクトリのコピー
* [`Move-Item`](windows/powershell/Move-Item.md) - ファイルやディレクトリの移動・名前変更
* [`Rename-Item`](windows/powershell/Rename-Item.md) - ファイルやディレクトリの名前変更
* [`Get-Content`](windows/powershell/Get-Content.md) - ファイルの内容を表示
* [`Add-Content`](windows/powershell/Add-Content.md) - ファイルに内容を追記
* [`Clear-Content`](windows/powershell/Clear-Content.md) - ファイルの内容をクリア
* [`Test-Path`](windows/powershell/Test-Path.md) - パスが存在するかどうかをテスト
* [`Get-ItemProperty`](windows/powershell/Get-ItemProperty.md) - アイテムのプロパティを取得(ファイルの属性など)
* [`Set-ItemProperty`](windows/powershell/Set-ItemProperty.md) - アイテムのプロパティを設定(ファイルの属性変更など)

## 3\. プロセスとサービス管理

Windowsで実行中のプロセスやサービスを管理するためのCmdlet群です。

* [`Get-Process`](windows/powershell/Get-Process.md) - 実行中のプロセス一覧表示
* [`Stop-Process`](windows/powershell/Stop-Process.md) - プロセスの終了
* [`Start-Process`](windows/powershell/Start-Process.md) - プロセスの開始
* [`Get-Service`](windows/powershell/Get-Service.md) - Windowsサービスの一覧表示
* [`Start-Service`](windows/powershell/Start-Service.md) - サービスの開始
* [`Stop-Service`](windows/powershell/Stop-Service.md) - サービスの停止
* [`Restart-Service`](windows/powershell/Restart-Service.md) - サービスの再起動
* [`Set-Service`](windows/powershell/Set-Service.md) - サービスの設定変更(スタートアップの種類など)

## 4\. ネットワーク管理と通信

ネットワーク設定の確認、通信、およびトラブルシューティングに関連するCmdlet群です。

* [`Get-NetIPConfiguration`](windows/powershell/Get-NetIPConfiguration.md) - IPアドレス設定の表示(`ipconfig`に相当)
* [`Test-Connection`](windows/powershell/Test-Connection.md) - ネットワーク接続のテスト(`ping`に相当)
* [`Resolve-DnsName`](windows/powershell/Resolve-DnsName.md) - DNSルックアップ(`nslookup`, `dig`に相当)
* [`Get-NetTCPConnection`](windows/powershell/Get-NetTCPConnection.md) - TCP接続情報の表示(`netstat`に相当)
* [`Get-NetAdapter`](windows/powershell/Get-NetAdapter.md) - ネットワークアダプター情報の取得
* [`Set-NetAdapter`](windows/powershell/Set-NetAdapter.md) - ネットワークアダプターの設定変更
* [`New-NetFirewallRule`](windows/powershell/New-NetFirewallRule.md) - ファイアウォールルールの作成
* [`Get-NetFirewallRule`](windows/powershell/Get-NetFirewallRule.md) - ファイアウォールルールの表示
* [`Invoke-WebRequest`](windows/powershell/Invoke-WebRequest.md) - Webコンテンツの取得(`curl`, `wget`に相当)
* [`Invoke-RestMethod`](windows/powershell/Invoke-RestMethod.md) - RESTful APIへのリクエスト送信
* [`New-PSSession`](windows/powershell/New-PSSession.md) - リモートセッションの確立(`ssh`に相当)
* [`Enter-PSSession`](windows/powershell/Enter-PSSession.md) - リモートセッションへの接続
* [`Exit-PSSession`](windows/powershell/Exit-PSSession.md) - リモートセッションからの切断
* [`Copy-ItemProperty`](windows/powershell/Copy-ItemProperty.md) - リモートへのファイルコピー(`scp`に相当)

## 5\. ユーザーとグループ管理

Windowsのユーザーアカウントとグループの管理に関するCmdlet群です。

* [`Get-LocalUser`](windows/powershell/Get-LocalUser.md) - ローカルユーザーアカウントの取得
* [`New-LocalUser`](windows/powershell/New-LocalUser.md) - ローカルユーザーアカウントの作成
* [`Set-LocalUser`](windows/powershell/Set-LocalUser.md) - ローカルユーザーアカウントの設定変更
* [`Remove-LocalUser`](windows/powershell/Remove-LocalUser.md) - ローカルユーザーアカウントの削除
* [`Get-LocalGroup`](windows/powershell/Get-LocalGroup.md) - ローカルグループの取得
* [`New-LocalGroup`](windows/powershell/New-LocalGroup.md) - ローカルグループの作成
* [`Add-LocalGroupMember`](windows/powershell/Add-LocalGroupMember.md) - ローカルグループへのメンバー追加
* [`Remove-LocalGroupMember`](windows/powershell/Remove-LocalGroupMember.md) - ローカルグループからのメンバー削除
* [`Get-ADUser`](windows/powershell/Get-ADUser.md) - Active Directoryユーザーの取得(ADDSモジュールが必要)
* [`Get-ADGroup`](windows/powershell/Get-ADGroup.md) - Active Directoryグループの取得(ADDSモジュールが必要)

## 6\. システム情報と設定

Windowsシステムの各種情報や設定を表示・変更するためのCmdlet群です。

* [`Get-ComputerInfo`](windows/powershell/Get-ComputerInfo.md) - コンピュータのシステム情報
* [`Get-WmiObject`](windows/powershell/Get-WmiObject.md) - WMIオブジェクトの取得
* [`Get-CimInstance`](windows/powershell/Get-CimInstance.md) - CIMインスタンスの取得(`Get-WmiObject`の代替)
* [`Get-EventLog`](windows/powershell/Get-EventLog.md) - イベントログの取得(古いログ用)
* [`Get-WinEvent`](windows/powershell/Get-WinEvent.md) - Windowsイベントログの取得(新しいログ用)
* [`Set-Item`](windows/powershell/Set-Item.md) - レジストリキーや環境変数の設定
* [`Get-Item`](windows/powershell/Get-Item.md) - レジストリキーや環境変数の取得
* [`Restart-Computer`](windows/powershell/Restart-Computer.md) - コンピュータの再起動
* [`Stop-Computer`](windows/powershell/Stop-Computer.md) - コンピュータのシャットダウン
* [`Get-ScheduledTask`](windows/powershell/Get-ScheduledTask.md) - スケジュールされたタスクの取得
* [`Register-ScheduledTask`](windows/powershell/Register-ScheduledTask.md) - スケジュールされたタスクの登録

## 7\. PowerShellスクリプティングの基礎

スクリプト作成に役立つ概念とCmdletです。

* **変数とデータ型**: `$variable = "value"`, `$num = 123`
* **条件分岐**: `If-Else-Elseif`
* **ループ**: `For`, `ForEach`, `While`, `Do-While`
* **関数**: `Function My-Function {}`
* **パイプライン**: `|` を使ったオブジェクトの引き渡し
* **エラー処理**: `try-catch-finally`, `$ErrorActionPreference`
* **パラメーターと高度な関数**: `[CmdletBinding()]`, `param()`
* [`Select-Object`](windows/powershell/Select-Object.md) - オブジェクトのプロパティを選択
* [`Where-Object`](windows/powershell/Where-Object.md) - オブジェクトのフィルタリング
* [`Sort-Object`](windows/powershell/Sort-Object.md) - オブジェクトのソート
* [`Group-Object`](windows/powershell/Group-Object.md) - オブジェクトのグループ化
* [`ConvertFrom-Json`](windows/powershell/ConvertFrom-Json.md) - JSON文字列をオブジェクトに変換
* [`ConvertTo-Json`](windows/powershell/ConvertTo-Json.md) - オブジェクトをJSON文字列に変換
* [`Export-Csv`](windows/powershell/Export-Csv.md) - オブジェクトをCSVファイルにエクスポート
* [`Import-Csv`](windows/powershell/Import-Csv.md) - CSVファイルをオブジェクトとしてインポート

## 8\. セキュリティ関連Cmdletと概念

ブルーチーム・レッドチームの視点から特に重要となるセキュリティ関連のCmdletと概念です。

* [`Get-Acl`](windows/powershell/Get-Acl.md) - アクセス制御リスト(ACL)の取得
* [`Set-Acl`](windows/powershell/Set-Acl.md) - アクセス制御リスト(ACL)の設定
* [`Get-CimInstance Win32_Process`](windows/powershell/Get-CimInstance_Win32_Process.md) - プロセス情報の詳細取得
* [`Get-ScheduledTask`](windows/powershell/Get-ScheduledTask.md) - 不審なタスクの検出
* [`Get-MpThreatDetection`](windows/powershell/Get-MpThreatDetection.md) - Windows Defenderの脅威検出ログ(セキュリティ監視)
* [`Get-DnsClientCache`](windows/powershell/Get-DnsClientCache.md) - DNSキャッシュの表示
* [`Get-ItemProperty HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Run`](windows/powershell/Get-ItemProperty_HKLM.md) - スタートアップ項目の確認
* [`Get-NetIPAddress`](windows/powershell/Get-NetIPAddress.md) - ネットワークインターフェイスとIPアドレス情報の詳細
* **PowerShellロギングとスクリプトブロックロギング**: 監査とフォレンジックのための設定
* **Constrained Language Mode**: スクリプトの実行を制限するセキュリティ機能
* **AppLocker/WDAC (Windows Defender Application Control)**: PowerShellスクリプトの実行制御
* **Malicious PowerShell Script Detection**: AMSI (Antimalware Scan Interface) との連携
* **LOLBAS (Living Off The Land Binaries, Scripts and Libraries) 関連**:
  * `powershell.exe` 自体が悪用されるシナリオ

## 9\. トラブルシューティングとデバッグ

システムの問題を診断し、解決するためのアプローチとCmdletです。

* [`Get-EventLog`](windows/powershell/Get-EventLog.md) / [`Get-WinEvent`](windows/powershell/Get-WinEvent.md) - イベントログからの情報収集
* [`Get-Counter`](windows/powershell/Get-Counter.md) - パフォーマンスカウンターの取得
* [`Get-NetTCPConnection`](windows/powershell/Get-NetTCPConnection.md) - ネットワーク接続の問題
* [`Get-Process`](windows/powershell/Get-Process.md) / [`Get-Service`](windows/powershell/Get-Service.md) - プロセスやサービス関連の問題
* [`Start-Transcript`](windows/powershell/Start-Transcript.md) - コマンドラインセッションのログ記録
* [`Stop-Transcript`](windows/powershell/Stop-Transcript.md) - ログ記録の停止
* [`environment_and_config.md`](windows/powershell/environment_and_config.md) - 環境変数・設定ファイル(PowerShell共通)
* [`troubleshooting_common_errors.md`](windows/powershell/troubleshooting_common_errors.md) - 一般的なトラブルシューティング(PowerShell共通)

## 10\. その他の重要な概念

上記カテゴリに分類されないが、PowerShellを理解する上で重要な概念です。

* **プロバイダー**: ファイルシステム、レジストリ、証明書ストアなどへのアクセス
* **プロファイル**: PowerShell起動時に自動実行されるスクリプト(`Microsoft.PowerShell_profile.ps1`など)
* **リモート処理**: `Invoke-Command`, `Enter-PSSession`によるリモートシステム管理
* **モジュール**: Cmdletや関数をまとめた単位
* **オブジェクト指向のパイプライン**: テキストではなくオブジェクトを渡す概念

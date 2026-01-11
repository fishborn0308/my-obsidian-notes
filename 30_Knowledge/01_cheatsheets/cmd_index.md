
# cmd\_index - CMDコマンドとシステム管理の主要目次

## 概要

このドキュメントは、Windowsコマンドプロンプト(CMD)に関するチートシート全体の目次です。基本的なファイル操作から、ネットワーク設定、システム情報取得、プロセス管理、ユーザー管理まで、CMDを効率的に操作・管理するために必要な情報を網羅します。各項目は詳細なチートシートファイルへのリンクとなっています。

## 1\. 基本操作とファイル・ディレクトリ管理

CMDにおける最も基本的な操作と、ファイルやディレクトリの操作に関するコマンド群です。

* [`dir`](windows/cmd/dir.md) - ファイルやディレクトリの一覧表示
* [`cd`](windows/cmd/cd.md) - ディレクトリの移動
* [`mkdir`](windows/cmd/mkdir.md) - ディレクトリの作成
* [`rmdir`](windows/cmd/rmdir.md) - 空のディレクトリの削除
* [`copy`](windows/cmd/copy.md) - ファイルのコピー
* [`xcopy`](windows/cmd/xcopy.md) - ファイルやディレクトリの高度なコピー(サブディレクトリも含む)
* [`robocopy`](windows/cmd/robocopy.md) - 高度なファイル・ディレクトリのコピー(堅牢なコピー機能)
* [`move`](windows/cmd/move.md) - ファイルやディレクトリの移動・名前変更
* [`del`](windows/cmd/del.md) - ファイルの削除
* [`rd`](windows/cmd/rd.md) - ディレクトリの削除
* [`type`](windows/cmd/type.md) - テキストファイルの内容表示
* [`find`](windows/cmd/find.md) - ファイル内のテキスト検索
* [`findstr`](windows/cmd/findstr.md) - 高度な文字列検索
* [`attrib`](windows/cmd/attrib.md) - ファイル属性の表示・変更
* [`icacls`](windows/cmd/icacls.md) - ファイルやフォルダーのアクセス制御リスト(ACL)の表示・設定
* [`takeown`](windows/cmd/takeown.md) - ファイルやフォルダーの所有権を取得
* [`tree`](windows/cmd/tree.md) - ディレクトリ構造をツリー表示
* [`assoc`](windows/cmd/assoc.md) - ファイル拡張子と関連付けの表示・変更
* [`ftype`](windows/cmd/ftype.md) - ファイルの種類と関連プログラムの表示・変更

## 2\. システム情報と設定

Windowsシステムの各種情報や設定を表示・変更するためのコマンド群です。

* [`systeminfo`](windows/cmd/systeminfo.md) - システムの概要情報表示
* [`hostname`](windows/cmd/hostname.md) - ホスト名の表示
* [`ver`](windows/cmd/ver.md) - Windowsのバージョン表示
* [`date`](windows/cmd/date.md) - 現在の日付の表示・設定
* [`time`](windows/cmd/time.md) - 現在の時刻の表示・設定
* [`driverquery`](windows/cmd/driverquery.md) - インストールされているデバイスドライバーの表示
* [`schtasks`](windows/cmd/schtasks.md) - タスクスケジューラの設定・管理
* [`reg`](windows/cmd/reg.md) - レジストリの操作
* [`sfc`](windows/cmd/sfc.md) - システムファイルチェッカー
* [`chkdsk`](windows/cmd/chkdsk.md) - ディスクのエラーチェックと修復
* [`dism`](windows/cmd/dism.md) - Windowsイメージのサービスと管理ツール
* [`shutdown`](windows/cmd/shutdown.md) - システムのシャットダウン、再起動、ログオフ

## 3\. プロセスとサービス管理

実行中のプロセスやWindowsサービスを管理するためのコマンド群です。

* [`tasklist`](windows/cmd/tasklist.md) - 実行中のプロセス一覧表示
* [`taskkill`](windows/cmd/taskkill.md) - プロセスの終了
* [`sc`](windows/cmd/sc.md) - Windowsサービスの管理
* [`net start`](windows/cmd/net_start.md) - サービスを開始
* [`net stop`](windows/cmd/net_stop.md) - サービスを停止
* [`at`](windows/cmd/at.md) - 特定の時間にコマンドを実行(非推奨、`schtasks`が推奨)

## 4\. ネットワーク管理と通信

ネットワーク設定の確認、通信、およびトラブルシューティングに関連するコマンド群です。

* [`ipconfig`](windows/cmd/ipconfig.md) - IPアドレス設定の表示・更新
* [`ping`](windows/cmd/ping.md) - ネットワーク接続のテスト
* [`tracert`](windows/cmd/tracert.md) - パケット経路の表示
* [`pathping`](windows/cmd/pathping.md) - ルートとパケット損失の診断
* [`netstat`](windows/cmd/netstat.md) - ネットワーク接続、ルーティングテーブル、インターフェイス統計表示
* [`nslookup`](windows/cmd/nslookup.md) - ドメイン名とIPアドレスの解決
* [`netsh`](windows/cmd/netsh.md) - ネットワーク設定の高度な管理
* [`route`](windows/cmd/route.md) - IPルーティングテーブルの表示・設定
* [`ftp`](windows/cmd/ftp.md) - FTPクライアント(非推奨)
* [`telnet`](windows/cmd/telnet.md) - リモート接続(非推奨、デバッグ用)
* [`whoami`](windows/cmd/whoami.md) - 現在のユーザー名とセキュリティ情報を表示

## 5\. ユーザーとグループ管理

Windowsのユーザーアカウントとグループの管理に関するコマンド群です。

* [`net user`](windows/cmd/net_user.md) - ユーザーアカウントの管理
* [`net localgroup`](windows/cmd/net_localgroup.md) - ローカルグループの管理
* [`net group`](windows/cmd/net_group.md) - ドメイングループの管理
* [`runas`](windows/cmd/runas.md) - 別のユーザーとしてプログラムを実行

## 6\. バッチファイルとスクリプト

CMDのバッチファイル作成に役立つコマンドと構文です。

* [`echo`](windows/cmd/echo.md) - メッセージの表示、ファイルへの出力
* [`set`](windows/cmd/set.md) - 環境変数の設定・表示
* [`for`](windows/cmd/for.md) - ファイルセット、コマンド出力、範囲などの反復処理
* [`if`](windows/cmd/if.md) - 条件分岐
* [`goto`](windows/cmd/goto.md) - バッチファイル内のラベルへのジャンプ
* [`call`](windows/cmd/call.md) - 別のバッチファイルを呼び出す
* [`pause`](windows/cmd/pause.md) - コマンドの実行を一時停止
* [`rem`](windows/cmd/rem.md) - コメント行

## 7\. セキュリティ関連コマンドと概念

ブルーチーム・レッドチームの視点から特に重要となるセキュリティ関連のコマンドと概念です。

* [`whoami`](windows/cmd/whoami.md) - 現在のユーザー名とセキュリティ情報を表示
* [`icacls`](windows/cmd/icacls.md) - ファイルやフォルダーのアクセス制御リスト(ACL)の表示・設定
* [`takeown`](windows/cmd/takeown.md) - ファイルやフォルダーの所有権を取得
* [`net use`](windows/cmd/net_use.md) - ネットワークドライブの割り当て・解除(認証情報の確認)
* [`net share`](windows/cmd/net_share.md) - 共有フォルダーの管理
* [`sc`](windows/cmd/sc.md) - Windowsサービスの管理(不審なサービスの検出)
* [`tasklist`](windows/cmd/tasklist.md) - 不審なプロセスの検出
* [`netstat`](windows/cmd/netstat.md) - 不審なネットワーク接続の検出
* [`auditpol`](windows/cmd/auditpol.md) - Windows監査ポリシーの設定
* [`wevtutil`](windows/cmd/wevtutil.md) - イベントログの管理
* [`wmic`](windows/cmd/wmic.md) - Windows管理インストゥルメンテーションコマンドラインユーティリティ
* [`systeminfo`](windows/cmd/systeminfo.md) - パッチレベルやOS情報からの脆弱性評価
* [`vssadmin`](windows/cmd/vssadmin.md) - ボリュームシャドウコピーサービスの管理(ランサムウェア対策として悪用される可能性も)
* **LOLBAS (Living Off The Land Binaries, Scripts and Libraries) 関連**:
  * `Certutil.exe`
  * `Msiexec.exe`
  * `Regsvr32.exe`
  * `Runonce.exe`
  * `bitsadmin.exe`
  * `cmd.exe`

## 8\. トラブルシューティングとデバッグ

システムの問題を診断し、解決するためのアプローチとコマンドです。

* [`eventvwr`](windows/cmd/eventvwr.md) - イベントビューアーの起動
* [`msconfig`](windows/cmd/msconfig.md) - システム構成ユーティリティ
* [`resmon`](windows/cmd/resmon.md) - リソースモニター
* [`taskmgr`](windows/cmd/taskmgr.md) - タスクマネージャの起動
* [`environment_and_config.md`](windows/cmd/environment_and_config.md) - 環境変数・設定ファイル(CMD共通)
* [`troubleshooting_common_errors.md`](windows/cmd/troubleshooting_common_errors.md) - 一般的なトラブルシューティング(CMD共通)

## 9\. その他の重要な概念とファイル

上記カテゴリに分類されないが、CMDを理解する上で重要な概念やファイルです。

* **環境変数**: `%PATH%`, `%TEMP%` など(詳細は`environment_and_config.md`を参照)
* **リダイレクト**: `>`, `>>`, `2>`, `&>`
* **パイプ**: `|`
* **ショートカットとエイリアス**: `doskey`
* **エラーレベル**: コマンドの成否判定

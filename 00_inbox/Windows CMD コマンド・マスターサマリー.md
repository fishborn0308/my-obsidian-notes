---
created: 2026-02-22 08:54
modified: 2026-02-22 09:30
---


---

# Windows CMD コマンド・マスターサマリー (2026/02/22 版)

## 1. 偵察・ネットワーク解析 (Reconnaissance & Networking)

「敵」を知り、己のネットワーク環境を把握するためのコマンド群です。

|**コマンド**|**主な用途**|**ポイント**|
|---|---|---|
|**`ipconfig`**|ネットワーク設定表示|IP、サブネット、ゲートウェイ。`/all` で詳細表示。|
|**`netstat`**|接続・統計情報の表示|どのポートがどのIPと通信しているか。不審な接続の特定に。|
|**`nslookup`**|DNS情報のクエリ|ドメイン名からIPを引く、またはその逆。DNS設定のデバッグ。|
|**`ping` / `tracert`**|疎通確認・経路追跡|相手まで届くか、どこでパケットが止まっているかを確認。|
|**`arp` / `getmac`**|MACアドレス管理|IPと物理アドレスの対応確認。中間者攻撃の検知など。|
|**`net view` / `session`**|共有資源・セッション|ネットワーク内の公開リソースや接続ユーザーを列挙。|

---

## 2. セキュリティ・権限・ID管理 (Security & Identity)

「誰が何にアクセスできるか」を定義・調査し、権限の昇格や維持に関わるカテゴリです。

|**コマンド**|**主な用途**|**ポイント**|
|---|---|---|
|**`net user` / `group`**|アカウント・グループ管理|ユーザーの追加、所属グループの確認。ドメイン環境でも多用。|
|**`icacls` / `cacls`**|アクセス権 (ACL) 操作|ファイルやフォルダに対する詳細なアクセス権限の設定・表示。|
|**`auditpol`**|監査ポリシーの管理|ログに「何を記録するか」の設定。監視の回避や強化に。|
|**`gpresult` / `gpupdate`**|グループポリシー管理|適用されているポリシーの確認と、即時反映。|
|**`assoc` / `ftype`**|拡張子の関連付け|特定の拡張子を開くプログラムを操作。永続化の手法として使われることも。|

---

## 3. システム診断・修復・フォレンジック (Diagnosis & Forensic)

システムの健康状態をチェックし、異常を検知・修復するためのツール群です。

|**コマンド**|**主な用途**|**ポイント**|
|---|---|---|
|**`wevtutil` / `eventvwr`**|イベントログ操作|ログの閲覧、抽出、消去。トラブルの痕跡を探す際に必須。|
|**`dism` / `sfc`**|イメージ修復・整合性|OSファイルの破損修復。`dism` は機能の有効化にも使用。|
|**`chkdsk` / `fsutil`**|ファイルシステム管理|ディスクエラーの修復や、ファイルシステムの低レベル操作。|
|**`driverquery`**|ドライバ情報の列挙|導入済みドライバの特定。脆弱なドライバの探索。|
|**`bcdedit`**|ブート構成の編集|起動設定の変更。セーフモード起動やデバッグ設定に。|

---

## 4. ファイル操作・ストレージ管理 (File & Storage Mastery)

データの複製、圧縮、秘匿、そして破壊を司る実務的なコマンドです。

|**コマンド**|**主な用途**|**ポイント**|
|---|---|---|
|**`robocopy`**|堅牢なファイル複製|ミラーリングや再試行が可能。バックアップの決定版。|
|**`cipher`**|暗号化と空き領域消去|`/w` オプションによるデータの「物理的抹消」が強力。|
|**`compact`**|NTFS 圧縮|容量節約。圧縮属性の表示・変更。|
|**`diskpart`**|パーティション管理|ディスクの初期化、パーティション作成、VHD操作。|
|**`attrib`**|ファイル属性の変更|システム属性 (`+s`) や隠し属性 (`+h`) の付与による秘匿。|
|**`expand`**|圧縮ファイルの展開|キャビネットファイル (.cab) などからのバイナリ抽出。|

---

## 5. 自動化・フロー制御 (Automation & Scripting)

個々のコマンドを組み合わせ、強力なツールへと昇華させるための制御用コマンドです。

|**コマンド**|**主な用途**|**ポイント**|
|---|---|---|
|**`for` / `if`**|ループと条件分岐|バッチファイルの頭脳。繰り返し処理や判定を行う。|
|**`call` / `goto`**|実行制御|外部スクリプトの呼び出しや、ラベルへのジャンプ。|
|**`setlocal` / `endlocal`**|変数スコープ管理|スクリプト内でのみ有効な変数を定義し、環境を汚さない。|
|**`echo`**|メッセージ・ファイル出力|画面表示や、リダイレクトを使ったファイル生成。|
|**`bitsadmin`**|バックグラウンド転送|HTTP/HTTPS経由でのファイルダウンロード。ジョブ管理。|

---

![[Command - Windows - cmd - arp - IPアドレスとMACアドレスの対応情報の表示・管理]]

![[Command - Windows - cmd - assoc - ファイル拡張子の関連付けを表示・修正]]

![[Command - Windows - cmd - at - タスクスケジューラの管理（レガシー）]]

![[Command - Windows - cmd - attrib - ファイル属性の表示・変更]]

![[Command - Windows - cmd - auditpol - 監査ポリシーの表示・設定]]

![[Command - Windows - cmd - bcdedit - ブート構成データ (BCD) の表示・設定]]

![[Command - Windows - cmd - bitsadmin - バックグラウンドでのファイル転送とジョブ管理]]

![[Command - Windows - cmd - break - 拡張 CTRL+C チェックの設定・表示]]

![[Command - Windows - cmd - cacls - ファイルのアクセス制御リスト (ACL) の表示・変更 (レガシー)]]

![[Command - Windows - cmd - call - 外部バッチファイルやラベルの呼び出し]]

![[Command - Windows - cmd - cd - ディレクトリの移動]]

![[Command - Windows - cmd - chcp - コードページの表示・変更]]

![[Command - Windows - cmd - chkdsk - ディスクのチェックと修復]]

![[Command - Windows - cmd - cipher - EFS暗号化の管理と空き領域の消去]]

![[Command - Windows - cmd - cls - 画面の消去]]

![[Command - Windows - cmd - color - コンソールの色の変更]]

![[Command - Windows - cmd - comp - ファイル内容の比較]]

![[Command - Windows - cmd - compact - NTFS 圧縮の表示・変更]]

![[Command - Windows - cmd - control - コントロール パネルの起動と設定]]

![[Command - Windows - cmd - copy - ファイルのコピーと結合]]

![[Command - Windows - cmd - date - システム日付の表示と設定]]

![[Command - Windows - cmd - del (erase) - ファイルの削除]]

![[Command - Windows - cmd - dir - ディレクトリの内容表示と情報の列挙]]

![[Command - Windows - cmd - diskpart - ディスク パーティションの管理]]

![[Command - Windows - cmd - dism - システム イメージの管理と修復]]

![[Command - Windows - cmd - doskey - コマンド履歴とマクロの管理]]

![[Command - Windows - cmd - driverquery - デバイス ドライバーの表示と管理]]

![[Command - Windows - cmd - echo - メッセージの表示と設定]]

![[Command - Windows - cmd - endlocal - 環境変数のローカライズの終了]]

![[Command - Windows - cmd - eventvwr - イベント ビューアーの起動]]

![[Command - Windows - cmd - exit - インタープリターまたはスクリプトの終了]]

![[Command - Windows - cmd - expand - 圧縮ファイルの展開]]

![[Command - Windows - cmd - fc - ファイルの比較と差分表示]]

![[Command - Windows - cmd - find - 文字列の検索]]

![[Command - Windows - cmd - findstr - 高度な文字列検索と正規表現]]

![[Command - Windows - cmd - for - ループ処理の実行]]

![[Command - Windows - cmd - fsutil - ファイルシステムの管理と操作]]

![[Command - Windows - cmd - ftp - ファイル転送プロトコル クライアント]]

![[Command - Windows - cmd - ftype - ファイル型に関連付けられた実行プログラムの表示・修正]]

![[Command - Windows - cmd - getmac - MAC アドレスの取得]]

![[Command - Windows - cmd - goto - 実行の制御（ラベルへのジャンプ）]]

![[Command - Windows - cmd - gpresult - グループ ポリシーの結果表示]]

![[Command - Windows - cmd - gpupdate - グループ ポリシーの更新]]

![[Command - Windows - cmd - help - コマンド ヘルプの表示]]

![[Command - Windows - cmd - hostname - コンピューター名の表示]]

![[Command - Windows - cmd - icacls - ファイルとディレクトリのアクセス制御リストの管理]]

![[Command - Windows - cmd - icacls - ファイルのアクセス制御リスト (ACL) の表示・変更 (現代版)]]

![[Command - Windows - cmd - if - 条件分岐の実行]]

![[Command - Windows - cmd - ipconfig - ネットワーク設定の表示・構成]]

![[Command - Windows - cmd - mkdir - ディレクトリの作成]]

![[Command - Windows - cmd - net - ネットワークサービスとリソースの管理（総合）]]

![[Command - Windows - cmd - net accounts - パスワードとログオンポリシーの管理]]

![[Command - Windows - cmd - net group - ドメイングループの管理]]

![[Command - Windows - cmd - net localgroup - ローカルグループの管理]]

![[Command - Windows - cmd - net session - リモートセッションの表示と管理]]

![[Command - Windows - cmd - net share - ローカル共有リソースの管理]]

![[Command - Windows - cmd - net start／stop - Windows サービスの管理]]

![[Command - Windows - cmd - net use - リモート共有リソースへの接続と管理]]

![[Command - Windows - cmd - net user - ユーザーアカウントの管理]]

![[Command - Windows - cmd - net view - ネットワーク共有リソースの列挙]]

![[Command - Windows - cmd - netstat - ネットワーク接続と統計情報の表示]]

![[Command - Windows - cmd - nslookup - DNS情報のクエリとデバッグ]]

![[Command - Windows - cmd - ping - ネットワーク疎通の確認]]

![[Command - Windows - cmd - robocopy - 堅牢なファイル複製と同期]]

![[Command - Windows - cmd - tracert - ネットワーク経路の追跡]]

![[Command - Windows - cmd - type - テキストファイルの内容表示]]

![[Command - Windows - cmd - wevtutil - Windows イベント ログの管理]]

![[Command - Windows - cmd - Windows コマンド インタープリターの開始]]

![[Command - Windows - cmd - xcopy - ファイルとディレクトリ構造のコピー]]
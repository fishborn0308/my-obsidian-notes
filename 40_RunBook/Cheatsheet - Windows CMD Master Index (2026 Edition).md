---
created: 2026-02-20 12:18
modified: 2026-02-23 09:52
environment: [OS/Windows]
vulnearability: []
runbook_category: Cheatsheet
tools: []
tags:
  - cmd
  - summary
  - cheatsheet
---

# 🏛️ Windows Command Master Index (2026 Edition)

> 「コマンドを知ることは、システムの魂を知ることである。」
>
> ここに集約された 100 以上のコマンドは、現代のインフラ管理、そしてサイバーセキュリティの最前線で戦うための最強の武器庫です。

---

## 1. 🔍 初期調査・偵察 (System Reconnaissance)

侵入直後、あるいはメンテナンス開始時に「ここはどこか」「何が動いているか」を把握するためのツール。

- **OS基本情報**: [[Command - Windows - cmd - ver - OSバージョンの表示|ver]], winver, [[Command - Windows - cmd - systeminfo - システムの詳細構成情報の表示|systeminfo]], [[Command - Windows - cmd - hostname - コンピューター名の表示|hostname]]
- **プロセス・サービス**: [[Command - Windows - cmd - tasklist - 実行中のプロセス一覧の表示|tasklist]], [[Command - Windows - cmd - taskmgr - タスク マネージャーの起動|taskmgr]], [[Command - Windows - cmd - sc - サービス コントロール マネージャーとの通信|sc]], [[Command - Windows - cmd - net start／stop - Windows サービスの管理|net start／stop]]
- **環境と変数**: [[Command - Windows - cmd - whoami - 現在のユーザー・グループ・特権情報の表示|whoami]], [[Command - Windows - cmd - set - 環境変数の表示・設定・削除|set]], [[Command - Windows - cmd - setlocal - 環境変数のローカライズと拡張機能の制御|setlocal]], [[Command - Windows - cmd - endlocal - 環境変数のローカライズの終了|endlocal]]
- **ハードウェア・設定**: [[Command - Windows - cmd - wmic - Windows 管理インストルメンテーション (WMI) コマンドライン|wmic]], [[Command - Windows - cmd - driverquery - デバイス ドライバーの表示と管理|driverquery]], [[Command - Windows - cmd - msconfig - システム構成ユーティリティの起動|msconfig]], [[Command - Windows - cmd - bcdedit - ブート構成データ (BCD) の表示・設定|bcdedit]], [[Command - Windows - cmd - chcp - コードページの表示・変更|chcp]]

---

## 2. 🌐 ネットワーク・通信 (Networking & Connectivity)

「繋がらない」を解決し、通信の裏側を暴くためのツール。

- **IP・ルーティング**: [[Command - Windows - cmd - ipconfig - ネットワーク設定의 表示・構成|ipconfig]], [[Command - Windows - cmd - route - ルーティングテーブルの表示・操作|route]], [[Command - Windows - cmd - arp - IPアドレスとMACアドレスの対応情報の表示・管理|arp]], [[Command - Windows - cmd - getmac - MAC アドレスの取得|getmac]]
- **診断・追跡**: [[Command - Windows - cmd - ping - ネットワーク疎通の確認|ping]], [[Command - Windows - cmd - tracert - ネットワーク経路の追跡|tracert]], [[Command - Windows - cmd - pathping - ネットワーク経路の診断と統計取得|pathping]], tnc (PowerShell)
- **接続状態**: [[Command - Windows - cmd - netstat - ネットワーク接続と統計情報の表示|netstat]], [[Command - Windows - cmd - nbtstat - NetBIOS 統計と名前解決の表示|nbtstat]], [[Command - Windows - cmd - nslookup - DNS情報のクエリとデバッグ|nslookup]], [[Command - Windows - cmd - telnet - リモート端末との双方向テキスト通信|telnet]], [[Command - Windows - cmd - ftp - ファイル転送プロトコル クライアント|ftp]]
- **高度な構成**: [[Command - Windows - cmd - netsh - ネットワーク構成の表示・変更|netsh]], [[Command - Windows - cmd - logman - パフォーマンス カウンターとイベント トレースの管理|logman]]

---

## 3. 🛡️ セキュリティ・権限管理 (Security & Identity)

「誰が、何にアクセスできるか」を制御し、特権を操るためのツール。

- **アカウント管理**: [[Command - Windows - cmd - net user - ユーザーアカウントの管理|net user]], [[Command - Windows - cmd - net localgroup - ローカルグループの管理|net localgroup]], [[Command - Windows - cmd - net group - ドメイングループの管理|net group]], [[Command - Windows - cmd - net accounts - パスワードとログオンポリシーの管理|net accounts]]
- **権限と所有権**: [[Command - Windows - cmd - runas - 別ユーザー権限でのプログラム実行|runas]], [[Command - Windows - cmd - takeown - ファイルまたはディレクトリの所有権の取得|takeown]], [[Command - Windows - cmd - whoami - 現在のユーザー・グループ・特権情報の表示|whoami]] /priv
- **アクセス制御 (ACL)**: [[Command - Windows - cmd - icacls - ファイルのアクセス制御リスト (ACL) の表示・変更 (現代版)|icacls]], [[Command - Windows - cmd - cacls - ファイルのアクセス制御リスト (ACL) の表示・変更 (レガシー)|cacls]] (Legacy)
- **認証・監査**: [[Command - Windows - cmd - cmdkey - ユーザー名とパスワードの保存・表示・削除|cmdkey]], [[Command - Windows - cmd - auditpol - 監査ポリシーの表示・設定|auditpol]], [[Command - Windows - cmd - certutil - 証明書サービスと各種ファイル操作|certutil]]
- **Active Directory**: [[Command - Windows - cmd - ntdsutil - Active Directory データベースの管理|ntdsutil]], [[Command - Windows - cmd - gpresult - グループ ポリシーの結果表示|gpresult]], [[Command - Windows - cmd - gpupdate - グループ ポリシーの更新|gpupdate]]

---

## 4. 📂 ファイル・ディスク管理 (File & Disk Operations)

データの整合性を守り、ストレージを自在に操るためのツール。

- **基本操作**: [[Command - Windows - cmd - dir - ディレクトリの内容表示と情報の列挙|dir]], [[Command - Windows - cmd - cd - ディレクトリの移動|cd]], [[Command - Windows - cmd - copy - ファイルのコピーと結合|copy]], [[Command - Windows - cmd - move - ファイルやディレクトリの移動と名前変更|move]], [[Command - Windows - cmd - rename - ファイルやディレクトリの名前変更|rename]], [[Command - Windows - cmd - del (erase) - ファイルの削除|del]], [[Command - Windows - cmd - mkdir - ディレクトリの作成|mkdir]], [[Command - Windows - cmd - rmdir - ディレクトリの削除|rmdir]]
- **高度な複製**: [[Command - Windows - cmd - robocopy - 堅牢なファイル複製と同期|robocopy]], [[Command - Windows - cmd - xcopy - ファイルとディレクトリ構造のコピー|xcopy]]
- **整合性・属性**: [[Command - Windows - cmd - attrib - ファイル属性の表示・変更|attrib]], [[Command - Windows - cmd - assoc - ファイル拡張子の関連付けを表示・修正|assoc]], [[Command - Windows - cmd - ftype - ファイル型に関連付けられた実行プログラムの表示・修正|ftype]], [[Command - Windows - cmd - fc - ファイルの比較と差分表示|fc]], [[Command - Windows - cmd - comp - ファイル内容の比較|comp]]
- **ファイルシステム**: [[Command - Windows - cmd - chkdsk - ディスクのチェックと修復|chkdsk]], [[Command - Windows - cmd - diskpart - ディスク パーティションの管理|diskpart]], [[Command - Windows - cmd - fsutil - ファイルシステムの管理と操作|fsutil]], [[Command - Windows - cmd - compact - NTFS 圧縮の表示・変更|compact]], [[Command - Windows - cmd - cipher - EFS暗号化の管理と空き領域の消去|cipher]]
- **抽出と転送**: [[Command - Windows - cmd - expand - 圧縮ファイルの展開|expand]], [[Command - Windows - cmd - bitsadmin - バックグラウンドでのファイル転送とジョブ管理|bitsadmin]], [[Command - Windows - cmd - vssadmin - ボリューム シャドウ コピー サービスの管理|vssadmin]]

---

## 5. 🛠️ 運用・自動化・修復 (Automation & Maintenance)

システムの健康状態を維持し、ルーチンを自動化するためのツール。

- **タスクスケジューリング**: [[Command - Windows - cmd - schtasks - タスク スケジューラの管理|schtasks]], [[Command - Windows - cmd - at - タスクスケジューラの管理（レガシー）|at]] (Legacy)
- **システム修復**: [[Command - Windows - cmd - sfc - システム ファイル チェッカー|sfc]], [[Command - Windows - cmd - dism - システム イメージの管理と修復|dism]]
- **パフォーマンス診断**: [[Command - Windows - cmd - perfmon - パフォーマンス モニターの起動|perfmon]], [[Command - Windows - cmd - resmon - リソース モニターの起動|resmon]], [[Command - Windows - cmd - logman - パフォーマンス カウンターとイベント トレースの管理|logman]]
- **イベント管理**: [[Command - Windows - cmd - eventvwr - イベント ビューアーの起動|eventvwr]], [[Command - Windows - cmd - wevtutil - Windows イベント ログの管理|wevtutil]]
- **レジストリ操作**: [[Command - Windows - cmd - reg - レジストリの操作 (CLI版)|reg]], [[Command - Windows - cmd - regedit - レジストリの表示・編集|regedit]]
- **電源制御**: [[Command - Windows - cmd - shutdown - システムのシャットダウン・再起動|shutdown]]

---

## 6. 💻 スクリプト・ロジック (Scripting & Batch Logic)

複数のコマンドを束ね、インテリジェンスを持たせるためのツール。

- **制御フロー**: [[Command - Windows - cmd - if - 条件分岐の実行|if]], [[Command - Windows - cmd - for - ループ処理の実行|for]], [[Command - Windows - cmd - goto - 実行の制御（ラベルへのジャンプ）|goto]], [[Command - Windows - cmd - call - 外部バッチファイルやラベルの呼び出し|call]], [[Command - Windows - cmd - exit - インタープリターまたはスクリプトの終了|exit]]
- **対話・出力**: [[Command - Windows - cmd - echo - メッセージの表示と設定|echo]], [[Command - Windows - cmd - pause - バッチ処理の一時停止とユーザー待機|pause]], [[Command - Windows - cmd - choice - ユーザー入力を待ち受け、選択肢から選択させる|choice]], [[Command - Windows - cmd - cls - 画面の消去|cls]], [[Command - Windows - cmd - color - コンソールの色の変更|color]], prompt (※)
- **検索・フィルタ**: [[Command - Windows - cmd - find - 文字列の検索|find]], [[Command - Windows - cmd - findstr - 高度な文字列検索と正規表現|findstr]]
- **支援**: [[Command - Windows - cmd - help - コマンド ヘルプの表示|help]], [[Command - Windows - cmd - doskey - コマンド履歴とマクロの管理|doskey]]

---

## 🗂️ 全コマンド A-Z インデックス

### A - E

- `[[Command - Windows - cmd - arp - IPアドレスとMACアドレスの対応情報の表示・管理|arp]]`
- `[[Command - Windows - cmd - assoc - ファイル拡張子の関連付けを表示・修正|assoc]]`
- `[[Command - Windows - cmd - at - タスクスケジューラの管理（レガシー）|at]]`
- `[[Command - Windows - cmd - attrib - ファイル属性の表示・変更|attrib]]`
- `[[Command - Windows - cmd - auditpol - 監査ポリシーの表示・設定|auditpol]]`
- `[[Command - Windows - cmd - bcdedit - ブート構成データ (BCD) の表示・設定|bcdedit]]`
- `[[Command - Windows - cmd - bitsadmin - バックグラウンドでのファイル転送とジョブ管理|bitsadmin]]`
- `[[Command - Windows - cmd - break - 拡張 CTRL+C チェックの設定・表示|break]]`
- `[[Command - Windows - cmd - cacls - ファイルのアクセス制御リスト (ACL) の表示・変更 (レガシー)|cacls]]`
- `[[Command - Windows - cmd - call - 外部バッチファイルやラベルの呼び出し|call]]`
- `[[Command - Windows - cmd - cd - ディレクトリの移動|cd]]`
- `[[Command - Windows - cmd - certutil - 証明書サービスと各種ファイル操作|certutil]]`
- `[[Command - Windows - cmd - chcp - コードページの表示・変更|chcp]]`
- `[[Command - Windows - cmd - chkdsk - ディスクのチェックと修復|chkdsk]]`
- `[[Command - Windows - cmd - choice - ユーザー入力を待ち受け、選択肢から選択させる|choice]]`
- `[[Command - Windows - cmd - cipher - EFS暗号化の管理と空き領域の消去|cipher]]`
- `[[Command - Windows - cmd - cls - 画面の消去|cls]]`
- `[[Command - Windows - cmd - cmdkey - ユーザー名とパスワードの保存・表示・削除|cmdkey]]`
- `[[Command - Windows - cmd - color - コンソールの色の変更|color]]`
- `[[Command - Windows - cmd - comp - ファイル内容の比較|comp]]`
- `[[Command - Windows - cmd - compact - NTFS 圧縮の表示・変更|compact]]`
- `[[Command - Windows - cmd - control - コントロール パネルの起動と設定|control]]`
- `[[Command - Windows - cmd - copy - ファイルのコピーと結合|copy]]`
- `[[Command - Windows - cmd - date - システム日付の表示と設定|date]]`
- `[[Command - Windows - cmd - del (erase) - ファイルの削除|del]]`
- `[[Command - Windows - cmd - dir - ディレクトリの内容表示と情報の列挙|dir]]`
- `[[Command - Windows - cmd - diskpart - ディスク パーティションの管理|diskpart]]`
- `[[Command - Windows - cmd - dism - システム イメージの管理と修復|dism]]`
- `[[Command - Windows - cmd - doskey - コマンド履歴とマクロの管理|doskey]]`
- `[[Command - Windows - cmd - driverquery - デバイス ドライバーの表示・管理|driverquery]]`
- `[[Command - Windows - cmd - echo - メッセージの表示と設定|echo]]`
- `[[Command - Windows - cmd - endlocal - 環境変数のローカライズの終了|endlocal]]`
- `[[Command - Windows - cmd - eventvwr - イベント ビューアーの起動|eventvwr]]`
- `[[Command - Windows - cmd - exit - インタープリターまたはスクリプトの終了|exit]]`
- `[[Command - Windows - cmd - expand - 圧縮ファイルの展開|expand]]`

### F - J

- `[[Command - Windows - cmd - fc - ファイルの比較と差分表示|fc]]`
- `[[Command - Windows - cmd - find - 文字列の検索|find]]`
- `[[Command - Windows - cmd - findstr - 高度な文字列検索と正規表現|findstr]]`
- `[[Command - Windows - cmd - for - ループ処理の実行|for]]`
- `[[Command - Windows - cmd - fsutil - ファイルシステムの管理と操作|fsutil]]`
- `[[Command - Windows - cmd - ftp - ファイル転送プロトコル クライアント|ftp]]`
- `[[Command - Windows - cmd - ftype - ファイル型に関連付けられた実行プログラムの表示・修正|ftype]]`
- `[[Command - Windows - cmd - getmac - MAC アドレスの取得|getmac]]`
- `[[Command - Windows - cmd - goto - 実行の制御（ラベルへのジャンプ）|goto]]`
- `[[Command - Windows - cmd - gpresult - グループ ポリシーの結果表示|gpresult]]`
- `[[Command - Windows - cmd - gpupdate - グループ ポリシーの更新|gpupdate]]`
- `[[Command - Windows - cmd - help - コマンド ヘルプの表示|help]]`
- `[[Command - Windows - cmd - hostname - コンピューター名の表示|hostname]]`

### I - N

- `[[Command - Windows - cmd - icacls - ファイルのアクセス制御リスト (ACL) の表示・変更 (現代版)|icacls]]`
- `[[Command - Windows - cmd - if - 条件分岐の実行|if]]`
- `[[Command - Windows - cmd - ipconfig - ネットワーク設定の表示・構成|ipconfig]]`
- `[[Command - Windows - cmd - logman - パフォーマンス カウンターとイベント トレースの管理|logman]]`
- `[[Command - Windows - cmd - mkdir - ディレクトリの作成|mkdir]]`
- `[[Command - Windows - cmd - move - ファイルやディレクトリの移動と名前変更|move]]`
- `[[Command - Windows - cmd - msconfig - システム構成ユーティリティの起動|msconfig]]`
- `[[Command - Windows - cmd - nbtstat - NetBIOS 統計と名前解決の表示|nbtstat]]`
- `[[Command - Windows - cmd - net - ネットワークサービスとリソースの管理（総合）|net]]`
- `[[Command - Windows - cmd - netsh - ネットワーク構成の表示・変更|netsh]]`
- `[[Command - Windows - cmd - netstat - ネットワーク接続と統計情報の表示|netstat]]`
- `[[Command - Windows - cmd - nslookup - DNS情報のクエリとデバッグ|nslookup]]`
- `[[Command - Windows - cmd - ntdsutil - Active Directory データベースの管理|ntdsutil]]`

### P - T

- `[[Command - Windows - cmd - pathping - ネットワーク経路の診断と統計取得|pathping]]`
- `[[Command - Windows - cmd - pause - バッチ処理の一時停止とユーザー待機|pause]]`
- `[[Command - Windows - cmd - perfmon - パフォーマンス モニターの起動|perfmon]]`
- `[[Command - Windows - cmd - ping - ネットワーク疎通の確認|ping]]`
- `[[Command - Windows - cmd - reg - レジストリの操作 (CLI版)|reg]]`
- `[[Command - Windows - cmd - regedit - レジストリの表示・編集|regedit]]`
- `[[Command - Windows - cmd - rem - コメント（注釈）の記述|rem]]`
- `[[Command - Windows - cmd - rename - ファイルやディレクトリの名前変更|rename]]`
- `[[Command - Windows - cmd - resmon - リソース モニターの起動|resmon]]`
- `[[Command - Windows - cmd - rmdir - ディレクトリの削除|rmdir]]`
- `[[Command - Windows - cmd - robocopy - 堅牢なファイル複製と同期|robocopy]]`
- `[[Command - Windows - cmd - route - ルーティングテーブルの表示・操作|route]]`
- `[[Command - Windows - cmd - runas - 別ユーザー権限でのプログラム実行|runas]]`
- `[[Command - Windows - cmd - sc - サービス コントロール マネージャーとの通信|sc]]`
- `[[Command - Windows - cmd - schtasks - タスク スケジューラの管理|schtasks]]`
- `[[Command - Windows - cmd - set - 環境変数の表示・設定・削除|set]]`
- `[[Command - Windows - cmd - setlocal - 環境変数のローカライズと拡張機能の制御|setlocal]]`
- `[[Command - Windows - cmd - sfc - システム ファイル チェッカー|sfc]]`
- `[[Command - Windows - cmd - shutdown - システムのシャットダウン・再起動|shutdown]]`
- `[[Command - Windows - cmd - systeminfo - システムの詳細構成情報の表示|systeminfo]]`
- `[[Command - Windows - cmd - takeown - ファイルまたはディレクトリの所有権の取得|takeown]]`
- `[[Command - Windows - cmd - taskkill - プロセスの終了|taskkill]]`
- `[[Command - Windows - cmd - tasklist - 実行中のプロセス一覧の表示|tasklist]]`
- `[[Command - Windows - cmd - taskmgr - タスク マネージャーの起動|taskmgr]]`
- `[[Command - Windows - cmd - telnet - リモート端末との双方向テキスト通信|telnet]]`
- `[[Command - Windows - cmd - time - システム時刻の表示・設定|time]]`
- `[[Command - Windows - cmd - tracert - ネットワーク経路の追跡|tracert]]`
- `[[Command - Windows - cmd - tree - ディレクトリ構造のグラフィカル表示|tree]]`
- `[[Command - Windows - cmd - type - テキストファイルの内容表示|type]]`

### U - Z

- `[[Command - Windows - cmd - ver - OSバージョンの表示|ver]]`
- `[[Command - Windows - cmd - vssadmin - ボリューム シャドウ コピー サービスの管理|vssadmin]]`
- `[[Command - Windows - cmd - w32tm - Windows タイム サービスの設定と診断|w32tm]]`
- `[[Command - Windows - cmd - wevtutil - Windows イベント ログの管理|wevtutil]]`
- `[[Command - Windows - cmd - whoami - 現在のユーザー・グループ・特権情報の表示|whoami]]`
- `[[Command - Windows - cmd - wmic - Windows 管理インストルメンテーション (WMI) コマンドライン|wmic]]`
- `[[Command - Windows - cmd - xcopy - ファイルとディレクトリ構造のコピー|xcopy]]`

---

### 💡 Obsidianでの活用ポイント

- **ホバープレビュー**: リスト形式なら、コマンド名にマウスを合わせるだけで詳細ノートの内容をサッと確認できます。
- **検索性**: `Ctrl + F` での検索も、表形式よりリスト形式の方がヒットした箇所が分かりやすくなります。
- **モバイル対応**: スマホやタブレット版のObsidianでも、横スクロールが発生せず読みやすいです。

---

## 🚀 Tips: 2026年のエンジニアが意識すべき「コマンドの使い分け」

> [!TIP]
>
> **「非推奨」を恐れない、しかし「代替」を知る**
>
> [[Command - Windows - cmd - wmic - Windows 管理インストルメンテーション (WMI) コマンドライン|wmic]] や [[Command - Windows - cmd - at - タスクスケジューラの管理（レガシー）|at]] は非推奨ですが、隔離環境やレガシーシステムでは依然として唯一の希望です。一方で、日常の運用では Get-CimInstance (PowerShell) や [[Command - Windows - cmd - schtasks - タスク スケジューラの管理|schtasks]] への移行を常に意識しましょう。

---
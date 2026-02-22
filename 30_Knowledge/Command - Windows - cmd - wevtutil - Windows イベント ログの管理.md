---
created: 2026-02-22 08:19
modified: 2026-02-22 09:30
environment: [OS/Windows]
vulnearability: [Information_Disclosure, Defense_Evasion]
knowledge_category: Command
tags:
  - cmd
  - wevtutil
  - event_log
  - forensics
  - anti_forensics
  - knowledge_base
---

# Command - Windows - cmd - wevtutil - Windows イベント ログの管理

## 概要

`wevtutil` (Windows Event Utility) は、イベント ログと発行元に関する情報の取得、ログのアーカイブやエクスポート、およびログの消去を行うためのコマンドライン ツールです。

GUI を使わずに、特定の条件（イベント ID や時間帯）に基づいたログの抽出ができるため、大規模なログ解析や自動化されたインシデント レスポンスにおいて極めて重要です。

(出自: `Windows 標準搭載 - 外部コマンド (wevtutil.exe)`)

## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 (あれば) |
| :--- | :--- | :--- |
| `eventvwr` | GUI ツール。視覚的な閲覧には向くが自動化には不向き。 | 最終確認 |
| `Get-WinEvent` | PowerShell 版。オブジェクトとしてフィルタリングでき、非常に強力。 | 解析効率化 |
| `Get-EventLog` | PowerShell 版（旧式）。WinEvent よりも機能が限定的。 | - |

## よく連携されるコマンド

### シナリオ例: ログのエクスポートとオフライン解析 (ブルーチーム視点)

* **目的**: 調査対象端末のセキュリティ ログを `.evtx` ファイルとして書き出し、フォレンジック端末へ持ち出す。
* **連携コマンド**: `wevtutil`, `eventvwr` (閲覧用)
* **解説**: `epl` (Export-Log) コマンドを使用して、特定のログをファイルに保存します。
* **コマンド例**:
    ```cmd
    REM セキュリティ ログを C:\temp\security_backup.evtx にエクスポート
    wevtutil epl Security C:\temp\security_backup.evtx
    ```

## スイッチ/サブコマンド説明

**※多くの操作には「管理者権限」が必要です。**

| サブコマンド | 説明 |
| :--- | :--- |
| ⭐ `el` | **enum-logs**: すべてのログの名前を列挙する。 |
| ⭐ `qe` | **query-events**: 指定したログからイベントを検索・表示する。 |
| ⭐ `cl` | **clear-log**: 指定したログの全イベントを削除する。 |
| `gl` | **get-log**: ログの構成情報（現在のサイズ、上限など）を表示。 |
| `epl` | **export-log**: ログを外部ファイルに書き出す。 |
| `gli` | **get-log-info**: ログの現在の状態（レコード数、タイムスタンプ）を表示。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: サーバーのディスク容量を節約するため、特定のログの最大サイズ設定を変更する。
* **例**:
    ```cmd
    REM Application ログの最大サイズを約 100MB (104857600 バイト) に設定
    wevtutil sl Application /ms:104857600
    ```

### 2. ブルーチーム視点 (脅威ハンティング)

* **タスク**: 直近の「ログオン成功 (Event ID: 4624)」の記録を最新 5 件だけ取得し、不審なユーザーがいないか確認する。
* **例**:
    ```cmd
    REM XPath 形式のクエリで 4624 を最新から 5 件抽出
    wevtutil qe Security /q:"*[System[(EventID=4624)]]" /f:text /rd:true /c:5
    ```

### 3. レッドチーム視点 (証拠隠滅 - T1070.001)

* **タスク 1: Indicator Removal (痕跡の消去)**
    * **目的**: 侵入後の一連の操作（net user 追加、コマンド実行など）が記録されたログを消去する。
    * **コマンド例**:
        ```cmd
        REM セキュリティ ログを完全に消去
        wevtutil cl Security
        REM システム ログを消去
        wevtutil cl System
        ```

* **タスク 2: System Enumeration (ログの種類の特定)**
    * **目的**: ターゲット環境に、独自のアプリケーション ログ（例：社内ツールのログ）が存在しないか探し、そこに認証情報などが漏れていないか調査する。
    * **コマンド例**:
        ```cmd
        wevtutil el | findstr /i "App"
        ```

## エラーメッセージとトラブルシューティング

1.  **Access is denied. (アクセス拒否)**
    * **原因**: 管理者権限のあるプロンプトで実行していない、またはログ自体が保護されている。
    * **解決策**: 「管理者として実行」を徹底してください。

2.  **The specified channel could not be found.**
    * **原因**: ログの名前（Security, System 等）のスペルミス、または存在しないログ名を指定した。
    * **解決策**: `wevtutil el` で正確なログ名を確認してください。

## セキュリティに関する考慮事項

### 脆弱性と悪用事例

* **ログの全消去**:
    * 攻撃者が `cl` コマンドを使用すると、消去したこと自体が「Event ID 1102」として記録されますが、それ以前のすべての詳細な活動履歴が失われます。
* **情報の露出**:
    * ログには、パスワード忘れによって誤ってユーザー名欄に入力されたパスワードや、スクリプトの引数に含まれた機密情報が残っている場合があり、攻撃者の格好の情報源になります。

### LOLBAS (Living Off The Land Binaries and Scripts) における利用例

* **機能**: `Artifact Removal` (痕跡の消去)
* **参照**: [LOLBAS - wevtutil.exe](https://lolbas-project.github.io/lolbas/Binaries/Wevtutil/)

### 対応策・緩和策 (ブルーチーム視点)

* **Detection (検知)**:
    * **イベントID 1102 (Security ログ消去)** または **イベントID 104 (System ログ消去)** を即座にアラート化する。
    * コマンドライン監査を有効にし、`wevtutil cl` の実行を監視する。
* **Prevention (予防)**:
    * **ログの外部転送**: ログがローカルで消去される前に、即座に Syslog や SIEM へ転送する設定（Event Forwarding）を導入します。

## 注意点・補足

* **XPath クエリ**: `/q` オプションで渡すクエリは強力ですが、構文が複雑です。フィルタリング条件を磨くことで、数ギガバイトのログから数秒で特定の通信記録を探し出すことが可能になります。
* **形式**: デフォルトは XML 形式で出力されますが、人間が読む場合は `/f:text` を指定するのが一般的です。

---
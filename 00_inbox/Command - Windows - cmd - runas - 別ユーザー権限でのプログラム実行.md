---
# --- YAML Frontmatter ---
created: '2026-02-22'
modified: '2026-02-22'
environment: [OS/Windows]
vulnearability: [Privilege_Escalation, Credential_Access]
knowledge_category: Command
tags:
  - 'runas'
  - cmd
  - 'privilege_management'
  - 'lateral_movement'
  - 'knowledge_base'
---

# Command - Windows - cmd - runas - 別ユーザー権限でのプログラム実行

## 概要

`runas` コマンドは、現在のログオン セッションを維持したまま、別のユーザーの資格情報（ユーザー名とパスワード）を使用して特定のツールやプログラムを実行します。
管理者が一般ユーザーとしてログインしている際に、一時的に管理者権限が必要なツール（`regedit` や `cmd` 等）を起動する場合や、レッドチームが奪取した別のアカウント権限でコマンドを試行する際に多用されます。
(出自: Windows標準搭載)



## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 |
| :--- | :--- | :--- |
| `sudo` | Linux用。パスワードをその場で入力する点は似ているが、`runas` はデフォルトでパスワードのパイプ渡しができない。 | Linuxなら `sudo` |
| `Start-Process -Credential` | PowerShell版。資格情報オブジェクトを渡せるため、スクリプト内での自動化が容易。 | `PowerShell` |
| `runascs` | サードパーティ製。`runas` の制限（パスワードの自動入力不可）を解決したペンテスト用ツール。 | ペンテストなら `runascs` |

## よく連携されるコマンド

### シナリオ例: 非管理者シェルから管理者シェルへの昇格 (ブルーチーム)

* **目的**: 一般ユーザーとして作業中に、システム設定変更のために管理者権限のコマンドプロンプトを別ウィンドウで開く。
* **連携コマンド**: `cmd.exe`
* **解説**: `/user` スイッチでローカルの `Administrator` を指定して起動します。
* **コマンド例**:
    ```cmd
    REM 管理者権限で新しいプロンプトを起動（実行後にパスワードが求められる）
    runas /user:Administrator cmd.exe
    ```

## スイッチ/オプション説明

| スイッチ | 説明 |
| :--- | :--- |
| **基本設定** | |
| ⭐ `/user:<UserName>` | プログラムを実行するユーザー名を指定。ドメインユーザーの場合は `user@domain` または `domain\user` 形式。 |
| ⭐ `/noprofile` | **重要。** ユーザーのプロファイルを読み込まずに実行する。アプリケーションの起動が速くなるが、一部の設定が読み込まれない。 |
| ⭐ `/savecred` | **危険。** 入力したパスワードをシステムに保存し、次回以降の入力を省略する。攻撃者に悪用されるリスクが高い。 |
| `/smartcard` | スマートカードを使用して資格情報を提示する場合に使用。 |
| `/showtrustlevels` | 利用可能な信頼レベルを表示する。 |
| `/trustlevel:<Level>` | ソフトウェアを実行する信頼レベルを指定。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: ドメイン管理者権限での Active Directory 管理ツールの起動
* **解説**: 自分の PC には一般ドメインユーザーでログインし、AD 操作時のみドメイン管理者の権限を借ります。
* **例**:
    ```cmd
    REM ドメイン管理者権限で AD ユーザーとコンピューターを起動
    runas /user:DOMAIN\AdminUser "mmc dsa.msc"
    ```

### 2. ブルーチーム視点

* **タスク**: `/savecred` の悪用調査
* **解説**: ユーザーが安易に `/savecred` を使って管理者パスワードを保存していないか、保存された資格情報を確認します。
* **例**:
    ```cmd
    REM 保存されている資格情報をリスト表示（cmdkeyを使用）
    cmdkey /list
    ```

### 3. レッドチーム視点 (OSCP / Lateral Movement)

* **タスク**: 奪取した平文パスワードを使用した横展開
* **解説**: `lsass` のダンプやフィッシングで得た別ユーザーのパスワードを使い、そのユーザーとしてコマンドを実行して権限の範囲を広げます。
* **例**:
    ```cmd
    REM 取得した db_admin の権限でネットワーク共有を列挙
    runas /noprofile /user:CORP\db_admin "cmd.exe /c net view \\fileserver"
    ```

## エラーメッセージとトラブルシューティング

### よくあるトラブル

1.  **現象**: パスワードを正しく入力しているのに「1326: ログオン失敗」が出る。
    * **考えられる原因**: 
        - ユーザー名にドメイン名が含まれていない（または間違っている）。
        - ローカルのアカウントポリシーで、空のパスワードでの `runas` が制限されている。
    * **解決策**: アカウント名を `.\Administrator` (ローカル) や `domain\user` (ドメイン) の形式で正確に指定する。

2.  **現象**: 「1058: 指定されたサービスは無効であるか...」が出る。
    * **原因**: `Secondary Logon` サービスが停止している。
    * **解決策**: `sc config seclogon start= auto` を実行後、`sc start seclogon` でサービスを開始する。

## 実務ログ例

```cmd
C:\Users\John> runas /noprofile /user:LAB\AdminUser "cmd.exe"
LAB\AdminUser のパスワードを入力してください:  (※ここでは文字が表示されない)
cmd.exe を ユーザー "LAB\AdminUser" として開始しています...

[新しいウィンドウが LAB\AdminUser 権限で起動する]
```

## セキュリティに関する考慮事項

### 脆弱性と悪用事例

- **悪用シナリオ**:
    
    - **Credential Replay**: 管理者が `/savecred` オプションを使用してログインしたことがある場合、攻撃者はパスワードを知らなくても `runas /savecred /user:Admin cmd.exe` を実行するだけで管理者権限を取得できてしまいます。
        
    - **Lateral Movement**: 侵害した端末内に残っている他のユーザーのプロセスやトークンを悪用し、`runas` 経由で他サーバーへアクセスを試みる。
        

### LOLBAS (Living Off The Land Binaries and Scripts) における利用例

- **機能**: `Execute`
    
- **解説**: 別のユーザーコンテキストで任意のコードを実行するために使用される。
    
- **参照**: [LOLBAS - runas.exe](https://www.google.com/search?q=https://lolbas-project.github.io/lolbas/Binaries/Runas/)
    

### 対応策・緩和策 (ブルーチーム視点)

- **Prevention (予防)**:
    
    - グループポリシー (GPO) で **「ネットワーク パスワードの保存を許可しない」** 設定を有効にし、`/savecred` の悪用を防ぐ。
        
    - `Secondary Logon` サービスを必要最小限の端末以外で無効化する。
        
- **Detection (検知)**:
    
    - **イベントID 4624 (Logon)**: ログオンタイプ 2 (Interactive) または 9 (NewCredentials) を監視し、不審なユーザー切り替えを検知する。
        
    - **イベントID 4688 (Process Creation)**: `runas.exe` の実行ログを監視。
        

## 注意点・補足

- **対話が必要**: `runas` は実行時にパスワードの入力を求めるプロンプトが出るため、完全な自動化スクリプトには向きません（標準入力からのリダイレクトを受け付けない仕様です）。
    
- **GUI と CUI**: `runas` で起動したアプリは通常、新しいウィンドウとして開きます。
    

---
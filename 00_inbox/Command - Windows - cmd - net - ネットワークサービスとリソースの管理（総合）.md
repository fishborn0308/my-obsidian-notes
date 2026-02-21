---
created: '2026-02-21'
modified: '2026-02-21'
environment: [OS/Windows, Network/Active_Directory]
vulnearability: [Reconnaissance, Privilege_Escalation, Lateral_Movement]
knowledge_category: Command
tags:
  - 'cmd'
  - 'net'
  - 'active_directory'
  - 'reconnaissance'
  - 'knowledge_base'
---

# Command - Windows - cmd - net - ネットワークサービスとリソースの管理（総合）

## 概要

`net` コマンドは、Windows におけるユーザーアカウント、グループ、ネットワーク共有、サービス、ネットワーク接続などを管理するための非常に多機能なコマンド群です。
インフラ管理における日常的な運用から、ペンテスト（レッドチーム）におけるドメイン情報の列挙まで、Windows ネットワーク操作の「スイスアーミーナイフ」として機能します。

(出自: `Windows 標準搭載 - net.exe`)



## サブコマンド・サマリー

`net` コマンドの後に続く主要なサブコマンドの一覧です。詳細は各詳細ドキュメントを参照してください。

| サブコマンド | 役割の概要 | 詳細リンク |Command - Windows - cmd - net localgroup - ローカルグループの管理
| **ユーザー・グループ管理** | | |
| `user` | ローカル/ドメインユーザーの作成、削除、照会。 | [[Command - Windows - cmd - net user - ユーザーアカウントの管理]] |
| `localgroup` | ローカルグループの管理（管理権限の付与など）。 | [[Command - Windows - cmd - net localgroup - ローカルグループの管理]] |
| `group` | ドメイングループの管理（AD環境専用）。 | [[Command - Windows - cmd - net group - ドメイングループの管理]]] |
| `accounts` | パスワードポリシーやログオン制限の設定確認。 | [[Command - Windows - cmd - net accounts - パスワードとログオンポリシーの管理]]] |
| **リソース・共有管理** | | |
| `view` | ネットワーク上のコンピュータや共有リソースの列挙。 | [net_view.md](./net_view.md) |
| `share` | 自端末の共有フォルダの作成、管理。 | [net_share.md](./net_share.md) |
| `use` | リモート共有リソースへの接続（ドライブマウント）。 | [net_use.md](./net_use.md) |
| `session` | 自端末に接続しているセッションの表示・切断。 | [net_session.md](./net_session.md) |
| **サービス・その他** | | |
| `start` / `stop` | Windows サービスの開始と停止。 | [net_service.md](./net_service.md) |
| `time` | サーバーとの時刻同期。 | - |

## 共通の業務シナリオ

### 1. ブルーチーム：不審なアカウント作成の監視
* **タスク**: 新しく作成されたユーザーが管理権限グループ (`Administrators`) に追加されていないか確認する。
* **主要コマンド**: `net localgroup administrators`

### 2. レッドチーム：内部偵察 (Enumeration)
* **タスク**: 侵入した端末からアクセス可能なファイル共有サーバーを見つけ出す。
* **主要コマンド**: `net view`, `net share`, `net use`

## セキュリティに関する考慮事項

### 脆弱性と悪用事例
* **LOLBAS**: `net.exe` は Windows 標準の強力な管理ツールであるため、攻撃者が「追加ツールを持ち込まずに」偵察や権限昇格を行うために必ずと言っていいほど利用されます。
* **悪用シナリオ**: 
    * `net user /domain` によるドメインユーザーリストの取得。
    * `net localgroup administrators <User> /add` による権限昇格。

### 対応策・緩和策 (ブルーチーム視点)
* **Prevention**: 最小権限の原則に基づき、一般ユーザーによる `net.exe` の実行を監視、または一部制限する。
* **Detection**: コマンドラインロギング（Event ID 4688）を有効にし、`net user` や `net localgroup` の実行パターンを SIEM で監視する。

## 注意点・補足
* **AD環境の `/domain`**: 多くのサブコマンドは `/domain` スイッチを付けることで、ローカルではなく Active Directory のデータベースに対して操作を行います。
* **エラーコード**: `net helpmsg <error_number>` を実行することで、エラーコードの詳細を確認できます。

---
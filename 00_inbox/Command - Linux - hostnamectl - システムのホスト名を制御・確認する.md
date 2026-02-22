---
# --- YAML Frontmatter ---
created: '2026-02-23'
modified: '2026-02-23'
environment: [OS/Linux (systemd)]
vulnearability: [Information_Disclosure, Unauthorized_System_Modification]
knowledge_category: Command
tags:
  - 'hostnamectl'
  - 'systemd'
  - 'administration'
  - 'networking'
  - 'knowledge_base'
---

# Command - Linux - hostnamectl - システムのホスト名を制御・確認する

## 概要
`hostnamectl` は、システムのホスト名を永続的に設定したり、関連するメタデータ（シャーシ型、アイコン名、デプロイ環境など）を管理するためのツールです。`systemd-hostnamed` サービスと対話し、`/etc/hostname` などのファイルを直接触ることなく、安全かつ即座に設定を反映させます。
(出自: `systemd` プロジェクト)



## 3種類の「ホスト名」
`hostnamectl` は以下の3つの異なる名前を管理できます：

1.  **Static (静的)**: `/etc/hostname` に保存される、再起動後も維持される伝統的な名前。
2.  **Transient (動的)**: DHCP や mDNS によって一時的に割り当てられた名前。
3.  **Pretty (外観用)**: "Alice's Laptop" のような、スペースや特殊文字を含めることができる人間向けの表示用名。

## 類似・関連コマンド
| コマンド | 差異 / 使い分けのポイント | 推奨 |
| :--- | :--- | :--- |
| `hostname` | 古典的なツール。一時的な変更に向くが、永続化には別途ファイル編集が必要。 | 簡易的な確認 |
| `nmcli general hostname` | NetworkManager 経由でホスト名を変更する。 | ネットワーク管理統合環境 |
| `uname -a` | カーネルやハードウェア情報を表示するが、ホスト名変更はできない。 | システム詳細確認 |
| `hostnamectl` | 永続設定、OS情報確認、Pretty名の設定を一括で行える。 | **モダンな Linux 管理** |

## よく連携されるコマンド
### シナリオ例: サーバーの「デプロイ環境」をタグ付け (運用視点)
* **目的**: 多数のサーバーがある中で、それが「本番 (production)」か「開発 (development)」かという属性をシステムに持たせる。
* **解説**: `set-deployment` サブコマンド（環境による）等でメタデータを付与できます。
* **コマンド例**:
    ```bash
    # ホスト名を設定し、同時に「本番環境」であることを記録
    sudo hostnamectl set-hostname web-prod-01
    ```

## サブコマンドとオプション
※設定変更には **root 権限** が必須です。

| サブコマンド | 説明 |
| :--- | :--- |
| ⭐️ `status` | 現在のホスト名とシステム情報（OS, Kernel, Arch, ID等）を表示する（デフォルト）。 |
| ⭐️ `set-hostname <NAME>` | ホスト名を `<NAME>` に変更する。 |
| `--static`, `--transient`, `--pretty` | 変更または表示するホスト名の種類を指定する。 |
| `set-chassis <TYPE>` | マシンの種類（vm, container, laptop, desktop 等）を設定する。 |
| `set-deployment <ENV>` | デプロイ環境（development, staging, production 等）を設定する。 |

## よく使われる業務シナリオ

### 1. インフラ構築・運用視点
* **タスク**: 新規サーバーの初期セットアップ（ホスト名の永続化）。
* **解説**: 設定ファイルをエディタで開く手間を省き、一行で確実に再起動後も有効な名前を設定します。
* **例**:
    ```bash
    # 永続的なホスト名を一発で設定
    sudo hostnamectl set-hostname database-server-01
    ```

### 2. ブルーチーム視点
* **タスク**: システムの詳細情報のクイック収集。
* **解説**: インシデント発生時、`hostnamectl` を叩くだけで OS の正確なバージョン、カーネル、仮想化環境（Virtualization）の種類を特定でき、初動調査のスピードが上がります。

### 3. レッドチーム視点
* **タスク**: 偵察（Reconnaissance）。
* **解説**: `hostnamectl` の出力から「Virtualization: oracle」や「Virtualization: vmware」といった情報を得て、それがサンドボックス環境か、あるいは攻略対象の本番仮想マシンかを判別します。また、OS のバージョンから既知のカーネル脆弱性を探ります。

## エラーメッセージとトラブルシューティング

1.  **「Could not set property: Access denied」**:
    * **原因**: 権限不足。
    * **解決策**: `sudo` を付けて実行する。

2.  **「Invalid hostname ...」**:
    * **原因**: 静的なホスト名にスペースや許可されていない記号を使おうとした。
    * **解決策**: 静的な名前には英数字とハイフンのみを使用し、スペースを使いたい場合は `--pretty` オプションを利用する。

## セキュリティに関する考慮事項

### 脆弱性とリスク
* **情報の露出**: `hostnamectl` は `Machine ID` や `Boot ID` も表示します。これらはシステムを一意に特定する識別子であり、ログの突合などで攻撃者に利用される可能性があります。

### 対応策・緩和策 (ブルーチーム視点)
* **監視**: 一般ユーザーが頻繁に `hostnamectl` を実行してシステム構成を探っていないか、また root によるホスト名変更が予期せぬタイミングで行われていないかをログ監視します。

## 注意点・補足
* **即時反映**: `hostnamectl` で名前を変えると、`/etc/hostname` は即座に書き換えられますが、既に実行中のアプリケーションやシェルプロンプトには、再起動やセッションの開き直しまで古い名前が表示されることがあります。
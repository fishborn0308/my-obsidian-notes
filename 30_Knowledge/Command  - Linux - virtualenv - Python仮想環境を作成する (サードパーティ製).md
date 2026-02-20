---
tags:
  - virtualenv
  - python
  - virtual_environment
  - dependency_management
  - legacy
  - venv
  - pipenv
  - Poetry
  - pip
created: 2025-06-29 15:02
modified: 2026-02-20 17:36
environment: [OS/Linux]
vulnearability: []
knowledge_category: Command
---

# Command  - Linux - virtualenv - Python仮想環境を作成する (サードパーティ製)

## 概要

`virtualenv` は、Pythonプロジェクトごとに隔離された**仮想環境**を作成するための、サードパーティ製のツールです。Python 3.3で標準ライブラリに `venv` が導入される以前から、長年にわたってPythonコミュニティの標準的なツールとして利用されてきました。

`venv` と同様に、プロジェクトごとに独立したPythonの実行環境とライブラリ (`site-packages`) を持つディレクトリを作成し、依存関係のコンフリクトを防ぎます。

(出自: `virtualenv` パッケージ (PyPI経由でインストール))

## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 |
| :--- | :--- |:---|
| ⭐ **`venv`** | `venv` はPython 3.3+ の**標準ライブラリ**です。`virtualenv` は `pip` でインストールが必要な**サードパーティ製パッケージ**です。`virtualenv` は `venv` よりも対応するPythonのバージョン範囲が広く、環境作成も高速な場合があります。 | `venv` (モダンなPython) |
| **`pipenv` / `Poetry`**| `virtualenv` や `venv` を内部的に利用し、より高レベルな依存関係管理とワークフローを自動化するツールです。 | `pipenv` / `Poetry` |

## よく連携されるコマンド

`virtualenv` の利用は、「作成 → 有効化 → 利用 → 無効化」という一連のワークフローで行います。

### シナリオ例: Pythonプロジェクトの基本的なセットアップ (インフラ構築・運用視点)

* **目的**: 新しいPythonプロジェクトを開始するにあたり、依存関係を隔離するための仮想環境をセットアップする。
* **連携コマンド**: `virtualenv`, `source`, `pip`, `deactivate`
* **解説**: プロジェクトディレクトリ内で仮想環境を作成し、それを有効化してから `pip` でパッケージをインストールするのが定石です。
* **コマンド例**:

		```bash
		# このコマンドシーケンスで仮想環境をセットアップし、利用する
		# 1. virtualenvをインストール (初回のみ)

		pip install virtualenv

		# 2. プロジェクトディレクトリに移動し、仮想環境を作成

		cd my-project

		virtualenv venv

		# 3. 仮想環境を有効化 (プロンプトが変わる)

		source venv/bin/activate

		# 4. (venv)環境内でパッケージをインストール

		pip install requests

		# 5. 作業終了後、仮想環境を無効化

		deactivate

		```

## オプション説明

| オプション | 説明 |
| :--- | :--- |
| **環境作成** | |
| ⭐ `<DIR>` | 仮想環境を作成するディレクトリの名前。（例: `venv`, `.venv`） |
| ⭐ `-p <PYTHON_EXE>`| **重要なオプション**。仮想環境のベースとして使用するPythonインタプリタのパスや名前を指定します。（例: `-p python2.7`） |
| `--system-site-packages`| 仮想環境から、システムのグローバルな `site-packages` にインストールされたライブラリも利用できるようにします。 |

## よく使われるオプション組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: モダンなOS上で、古いPython 2.7でしか動作しないレガシーアプリケーションの実行環境を構築する。
* **組み合わせ**: `virtualenv -p <python_version> <env_name>`
* **解説**: `-p` オプションでベースとなるPythonインタプリタを指定することで、システムに複数のPythonバージョンが共存している場合でも、特定のバージョンの隔離環境を確実に作成できます。
* **例**:

		```bash
		# Python 2.7 の仮想環境 'legacy_env' を作成

		virtualenv -p /usr/bin/python2.7 legacy_env

		# 有効化して、古いライブラリをインストール

		source legacy_env/bin/activate

		pip install Fabric==1.14.0

		```

## 2. ブルーチーム視点

* **タスク**: Python 2で書かれたマルウェアの疑いがあるスクリプトを、安全なサンドボックス内で分析する。
* **組み合わせ**: `virtualenv -p python2.7 <analysis_env>`
* **解説**: `venv` と同様に、分析専用の仮想環境を作成しますが、対象がPython 2であるため `virtualenv` を使用します。これにより、モダンなシステム上でも安全にレガシーなスクリプトの依存関係をインストールし、動的解析を行うことができます。
* **例**:

		```bash
		# マルウェア分析用のPython 2仮想環境を作成

		virtualenv -p python2.7 malware_analysis_env

		```

## 3. レッドチーム視点

* **タスク**: **活動の隠蔽 (Defense Evasion)**。侵入したホスト上で、攻撃ツールをシステムの `pip` を使わずに導入する。
* **組み合わせ**: `virtualenv .hidden_env`
* **解説**: 攻撃者は、書き込み権限のあるディレクトリに隠し仮想環境を作成し、その中で攻撃に必要なライブラリを `pip` でインストールします。これにより、管理者が `sudo pip list` を実行しても、システムのグローバル環境には何もインストールされていないように見せかけます。
* **例**:

		```bash
		# /tmp に隠し仮想環境を作成

		virtualenv /tmp/.my-tools

		# 隠し環境のpipを使ってライブラリをインストール

		/tmp/.my-tools/bin/pip install impacket

		```

## エラーメッセージとトラブルシューティング

* 一般的なエラーは [Linux共通のトラブルシューティング](OS%20%20-%20Linux%20-%20troubleshooting_common_errors%20-%20Linux共通エラー対応ガイド.md) を参照。

1. **エラーメッセージ例 1**: `bash: virtualenv: command not found`
		* **考えられる原因**: `virtualenv` がインストールされていません。
		* **解決策**: `pip install virtualenv` を実行してインストールしてください。

## 環境変数と設定ファイル

* `virtualenv` の動作に直接影響する環境変数は通常ありません。
* 一般的な環境変数・設定ファイルは [Linux共通の環境変数・設定ファイル](./environment_and_config.md) を参照。

## セキュリティに関する考慮事項

### 脆弱性と悪用事例

* **脆弱性**: `virtualenv` はセキュリティサンドボックスでは**ありません**。
* **悪用シナリオ**: `virtualenv` が提供するのは**依存関係の隔離**であり、**セキュリティの隔離**ではありません。仮想環境内で実行されたスクリプトは、実行ユーザーの権限でファイルシステムやネットワークに自由にアクセスできます。

### GTFOBins / LOLBAS における利用例

`virtualenv` は、`python` を実行するための環境をセットアップするツールであり、それ自体が直接的な権限昇格機能を持つわけではないため、GTFOBinsにはリストされていません。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: 信頼できないPythonスクリプトを実行しない。
* **Detection (検知)**: `ps` コマンドの出力で、プロセスの実行パスが仮想環境内 (`/path/to/venv/bin/python`) になっていることを確認する。不審なディレクトリに作成された仮想環境がないか `find` で探索する。

## 注意点・補足

* **`venv` を第一候補に**: 新規のPython 3プロジェクトでは、標準ライブラリに含まれており、追加のインストールが不要な `venv` を使うのが一般的です。`virtualenv` は、`venv` では対応できない特定の要件（古いPythonバージョンの利用など）がある場合の選択肢となります。
* **`.gitignore` への追加**: `venv` と同様に、`virtualenv` で作成した仮想環境のディレクトリは、必ずプロジェクトの `.gitignore` ファイルに追加してください。

---


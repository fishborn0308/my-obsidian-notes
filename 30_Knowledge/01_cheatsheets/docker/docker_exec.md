---
tags:
  - 'docker_exec'
  - 'docker'
  - 'container'
  - 'troubleshooting'
  - 'cheatsheet'
title: 'docker exec - 実行中のコンテナ内でコマンドを実行する'
summary: 'すでに実行中のコンテナの内部で、新しいコマンドや対話的なシェルを起動するためのコマンドで、デバッグや調査に不可欠です。'
related:
  - 'docker_run'
  - 'docker_attach'
  - 'docker_logs'
  - 'ssh'
---

# `docker exec` - 実行中のコンテナ内でコマンドを実行する

## 概要

`docker exec` は、**すでに実行中のコンテナ**の内部で、新しいコマンドを実行するためのコマンドです。主に、稼働中のアプリケーションのデバッグ、設定ファイルの確認・変更、一時的なメンテナンス作業など、コンテナを停止させることなく内部の状態を調査・操作するために利用されます。とくに `-it` オプションを付けてシェル (`/bin/bash` など) を起動することで、コンテナ内部に**対話的に入る**ことができます。

(出自: `docker` パッケージに含まれる)

## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 |
| :--- | :--- | :--- |
| ⭐ **`docker run`** | **新しいコンテナを作成して**コマンドを実行します。`exec` は**既存の稼働中コンテナ**に対してコマンドを実行する点が根本的に異なります。 | - |
| **`docker attach`** | 実行中のコンテナの**メインプロセス(PID 1)**の標準入出力に、現在のターミナルを接続します。`exec` はコンテナ内で**新しいプロセス**を起動します。 | `docker logs` (ログ確認) |
| **`ssh`** | リモートホストに接続するためのプロトコルです。`docker exec` は、ローカルホスト上のコンテナという隔離環境に入るための操作です。 | - |

## よく連携されるコマンド

`docker exec` は、`docker ps` で対象コンテナのIDや名前を特定した後に実行するのが一般的なワークフローです。

### シナリオ例: 稼働中コンテナのデバッグ (インフラ構築・運用視点)

* **目的**: 動作がおかしいWebアプリケーションコンテナの中に入り、内部の状態を確認する。
* **連携コマンド**: `docker ps`, `docker exec`
* **解説**: まず `docker ps` で対象コンテナの正確な名前またはIDを確認します。次に `docker exec -it` でコンテナ内部のシェルを起動し、`ps` や `ls`, `cat` などのコマンドを使って調査します。
* **コマンド例**:

    ```bash
    # 1. 稼働中のコンテナ 'my-webapp' のIDを確認
    docker ps

    # 2. コンテナ内で対話的なbashシェルを起動
    docker exec -it my-webapp /bin/bash
    ```

## オプション説明

| オプション | 説明 |
| :--- | :--- |
| **実行モード** | |
| ⭐ `-i`, `--interactive` | 対話的な操作を可能にします (stdin を開いたままにする)。シェルを操作する場合に必須です。 |
| ⭐ `-t`, `--tty` | 擬似ターミナル (pseudo-TTY) を割り当てます。`-i` とセットで `-it` として使うのが一般的です。 |
| `-d`, `--detach` | コマンドをバックグラウンドで実行します。 |
| **ユーザーと環境** | |
| ⭐ `-u`, `--user <user>` | コマンドを実行するユーザー名またはUIDを指定します。（例: `-u www-data`） |
| `-w`, `--workdir <dir>` | コマンドを実行するワーキングディレクトリをコンテナ内で指定します。 |
| `-e`, `--env <KEY=VALUE>` | コマンド実行時に環境変数を設定します。 |

## よく使われるオプション組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: データベースコンテナ内で `mysqldump` を実行し、バックアップを取得する。
* **組み合わせ**: `docker exec <db_container> <command> > <backup_file>`
* **解説**: コンテナ内でコマンドを実行し、その標準出力をホストマシン上のファイルにリダイレクトしてバックアップを取得します。
* **例**:

    ```bash
    # データベースコンテナ 'my-database' のバックアップを取得
    docker exec my-database mysqldump -u user -p'password' dbname > backup.sql
    ```

### 2. ブルーチーム視点

* **タスク**: 侵害された疑いのあるコンテナを停止させずに、内部のプロセスリストを確認する。
* **組み合わせ**: `docker exec <suspicious_container> ps aux`
* **解説**: **ライブフォレンジック調査**。`exec` を使って `ps`, `netstat`, `ls -l /tmp` などのコマンドをコンテナ内で実行し、不審なプロセスやファイルがないかを確認し、インシデントの状況を把握します。
* **例**:

    ```bash
    # `compromised-container` 内のネットワーク接続を確認
    docker exec compromised-container netstat -tulpn
    ```

### 3. レッドチーム視点

* **タスク**: コンテナ内の `root` ユーザーとしてシェルを奪取する。
* **組み合わせ**: `docker exec -it -u 0 <container_name> /bin/sh`
* **解説**: `-u 0` オプションで、コンテナ内の `root` ユーザーとしてシェルを起動します。コンテナが特権ユーザーで稼働している場合、`exec` を使ってコンテナ内の `root` シェルを奪取し、内部ネットワーク上の他のコンテナへの攻撃の足がかりとします。
* **例**:

    ```bash
    # 特権コンテナにrootとして侵入
    docker exec -it --user root privileged-container /bin/sh
    ```

## エラーメッセージとトラブルシューティング

* 一般的なエラーは [Linux共通のトラブルシューティング](../linux/troubleshooting_common_errors.md) を参照。

1. **エラーメッセージ例 1**: `Error response from daemon: Container <id> is not running`
    * **考えられる原因**: `docker exec` は実行中のコンテナに対してのみ機能します。対象のコンテナが停止しています。
    * **解決策**: `docker start <container>` で起動してから `exec` を実行します。

2. **エラーメッセージ例 2**: `OCI runtime exec failed: exec failed: unable to start container process: exec: "bash": executable file not found in $PATH`
    * **考えられる原因**: コンテナ内に `bash` がインストールされていません。`alpine` ベースのイメージでは `sh` のみが存在することが多いです。
    * **解決策**: `/bin/sh` を試してください (`docker exec -it <container> /bin/sh`)。

## 環境変数と設定ファイル

* `docker exec` の動作に直接影響する環境変数や設定ファイルは通常ありません。
* 一般的な環境変数・設定ファイルは [Linux共通の環境変数・設定ファイル](../linux/environment_and_config.md) を参照。

## セキュリティに関する考慮事項

### 脆弱性と悪用事例

* **脆弱性**: **実質的なroot権限**。Dockerソケットへのアクセス権を持つユーザーは、`docker exec` を通じてホストのroot権限を容易に奪取できます。
* **悪用シナリオ**: `docker` グループに所属するユーザーは、`docker exec -u 0` を使って任意のコンテナのroot権限を奪取できます。もしコンテナがホストのディレクトリを特権的にマウントしていれば、ホスト自体のroot権限を奪取することも可能です。

### GTFOBins / LOLBAS における利用例

* **機能**: **Privilege Escalation**
* **解説**: `docker` コマンド自体が、Dockerソケットにアクセスできるユーザーにとっての権限昇格の手法としてGTFOBinsで認識されています。`docker exec` はその権限を実際に行使するための最も直接的なコマンドの1つです。
* **参照**: `https://gtfobins.github.io/gtfobins/docker/`

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: **`docker` グループへのユーザー追加は、そのユーザーにroot権限を与えることと実質的に同義とみなし、慎重に行う**。
* **Detection (検知)**: `auditd` で `docker` コマンドの実行、特に `docker exec` の実行を監視する。

## 注意点・補足

* **`-it` の重要性**: 対話的なシェルを操作する場合、`-i` (stdinを接続) と `-t` (擬似TTYを割り当て) はほぼ常にセットで使います。`-t` がないとコマンドプロンプトが表示されず、`-i` がないとコマンドを入力できません。

---
[インデックスに戻る](../docker_index.md)

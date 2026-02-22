---
# --- YAML Frontmatter ---
created: '2026-02-22'
modified: '2026-02-22'
environment: [OS/Linux, Virtualization/Container]
vulnearability: [Chroot_Escape, Privilege_Escalation]
knowledge_category: Command
tags:
  - 'chroot'
  - 'jail'
  - 'sandboxing'
  - 'post-exploitation'
  - 'knowledge_base'
---

# Command - Linux - chroot - ルートディレクトリを変更して隔離環境でコマンドを実行する

## 概要
`chroot` は、現在実行中のプロセスとその子プロセスに対して、ルートディレクトリ (`/`) を指定したパスに変更するコマンドです。これにより、プロセスは指定されたディレクトリ外のファイルにアクセスできなくなります。この隔離された環境は一般に「chroot監獄 (chroot jail)」と呼ばれます。
(出自: `coreutils` パッケージに含まれる)



## 類似コマンドと差異
| 類似コマンド | 差異 / 使い分けのポイント | 推奨 |
| :--- | :--- | :--- |
| `pivot_root` | カーネルレベルでルートファイルシステムを入れ替える。主にシステム起動時やコンテナ起動時に使用。 | コンテナ実装・低レイヤ |
| `docker / lxc` | `chroot` に加え、名前空間 (Namespace) や Cgroups を使い、ネットワークやプロセスも完全に分離する。 | 現代的なアプリケーション隔離 |
| `systemd-nspawn` | `chroot` に似ているが、OS全体のブートを模倣できる、より高度なツール。 | 軽量なOSコンテナ |

## よく連携されるコマンド
### シナリオ例: 起動不能になったシステムの修復 (運用・レスキュー視点)
* **目的**: ライブUSB等で起動し、マウントしたHDD内のOS環境に入って `grub` の再インストールやパスワードリセットを行う。
* **連携コマンド**: `mount`, `grub-install`
* **解説**: 物理ディスク上の `/`, `/proc`, `/sys`, `/dev` をライブ環境にバインドマウントした状態で `chroot` します。
* **コマンド例**:
    ```bash
    # ターゲットシステムをマウント
    mount /dev/sda1 /mnt
    # 必要な仮想ファイルシステムをバインドマウント
    for i in /dev /dev/pts /proc /sys /run; do mount -B $i /mnt$i; done
    # 隔離環境へ入る
    sudo chroot /mnt /bin/bash
    ```

## オプション説明
| オプション | 説明 |
| :--- | :--- |
| **基本操作** | |
| ⭐️ `chroot <NEWROOT> [<COMMAND>]` | 新しいルートディレクトリを指定し、そこでコマンドを実行する（省略時はシェルが起動）。 |
| `--userspec=<USER:GROUP>` | 隔離環境内で実行する際のユーザーとグループを指定する。 |
| `--groups=<G_LIST>` | 補助グループを指定する。 |

## よく使われるオプション組み合わせと業務シナリオ

### 1. インフラ構築・運用視点
* **タスク**: 依存関係を汚染させないクリーンなパッケージビルド環境の構築。
* **組み合わせ**: `debootstrap` + `chroot`
* **解説**: 最小限の OS 環境を特定のディレクトリに作成し、そこに入って作業することでホスト環境を清潔に保ちます。
* **例**:
    ```bash
    # Ubuntuの最小環境を作成して入る
    sudo debootstrap focal ./my-jail
    sudo chroot ./my-jail
    ```

### 2. ブルーチーム視点
* **タスク**: 特定のネットワークサービスのサンドボックス化（ハニーポット構築など）。
* **組み合わせ**: `--userspec`
* **解説**: 万が一サービスが侵害されても、攻撃者がルートディレクトリ外（ホストの重要ファイル）にアクセスできないように制限します。
* **例**:
    ```bash
    # nobody 権限で隔離環境内のアプリを実行
    sudo chroot --userspec=nobody:nogroup /var/lib/api_jail /usr/bin/python3 app.py
    ```

### 3. レッドチーム視点
* **タスク**: 制限された環境からの脱出 (Chroot Escape)。
* **組み合わせ**: C言語等によるシステムコール呼び出し
* **解説**: `chroot` はプロセス分離が不完全なため、root 権限があれば容易に脱出可能です。これを理解しておくことは、コンテナエスケープの基礎となります。
* **例 (脱出のロジック)**:
    ```c
    // 概念コード: 監獄内で新しい監獄を作り、外へcdする
    mkdir("out", 0755);
    chroot("out");
    for(int i=0; i<100; i++) chdir("..");
    chroot(".");
    system("/bin/bash");
    ```

## エラーメッセージとトラブルシューティング

1.  **エラーメッセージ例 1**: `chroot: failed to run command '/bin/bash': No such file or directory`
    * **考えられる原因**: 隔離環境内に `/bin/bash` や、それが必要とする共有ライブラリ (`ldd` で確認可能) が存在しない。
    * **解決策**: ホスト側から必要なバイナリとライブラリをコピーする。

2.  **エラーメッセージ例 2**: `chroot: cannot change root directory to '...': Operation not permitted`
    * **考えられる原因**: 一般ユーザーで `chroot` を実行しようとした。
    * **解決策**: `sudo` を使用する（`chroot` は root 権限が必須）。

## 環境変数と設定ファイル
* `chroot` 実行後、環境変数（`PATH` 等）は継承されますが、バイナリが見つからないケースが多いため、環境内で改めて `export PATH=/bin:/usr/bin` 等を行う必要があります。

## セキュリティに関する考慮事項

### 脆弱性と悪用事例
* **脆弱性**: `chroot` は**セキュリティ境界ではない**。
* **悪用シナリオ**: 攻撃者が root 権限で `chroot` 環境に閉じ込められた場合、ファイル記述子を維持したまま移動したり、前述の `mkdir/chroot/cd ..` テクニックを使ってホストのファイルシステムに復帰したりします。

### GTFOBins における利用例
`chroot` が `sudo` で実行可能な場合、任意のディレクトリをルートに見せかけて実行できるため、実質的に特権昇格や制限回避が可能です。
* **機能**: `Sudo`
* **コマンド例**:
    ```bash
    # sudo chroot を使って / (ルート) に入り直す（制限回避）
    sudo chroot / /bin/sh
    ```
* **参照**: `https://gtfobins.github.io/gtfobins/chroot/`

### 対応策・緩和策 (ブルーチーム視点)
* **Prevention (予防)**: 隔離が目的であれば `chroot` ではなく、名前空間による分離を行う Docker や Podman を使用する。
* **Detection (検知)**: 意図しない `chroot` 命令の実行を `auditd` で監視し、特に Web サーバー等のサービスユーザーが実行した場合は即座に遮断する。

## 注意点・補足
* **ライブラリの依存関係**: 単にバイナリを置くだけでは動きません。`ldd /bin/bash` を実行して表示される `.so` ファイルも、相対的に同じパス（隔離環境内の `/lib` 等）に配置する必要があります。
* **デバイスファイル**: `/dev/null` や `/dev/random` が必要なアプリの場合、隔離環境内で `mknod` を使うか、ホストからバインドマウントする必要があります。
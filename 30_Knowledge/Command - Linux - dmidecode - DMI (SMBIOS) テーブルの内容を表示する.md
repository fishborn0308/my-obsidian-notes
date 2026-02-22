---
# --- YAML Frontmatter ---
created: '2026-02-22'
modified: '2026-02-22'
environment: [OS/Linux, Architecture/x86_64, Architecture/ARM]
vulnearability: [Information_Disclosure, Anti-VM_Techniques]
knowledge_category: Command
tags:
  - 'dmidecode'
  - 'hardware'
  - 'bios'
  - 'smbios'
  - 'inventory'
  - 'knowledge_base'
---

# Command - Linux - dmidecode - DMI (SMBIOS) テーブルの内容を表示する

## 概要
`dmidecode` は、コンピュータの BIOS/UEFI が保持している **DMI** (Desktop Management Interface) または **SMBIOS** (System Management BIOS) テーブルを解析し、人間が読みやすい形式で表示するツールです。OS のカーネルが認識している情報ではなく、ハードウェア（ファームウェア）側が自己申告している情報を取得します。
(出自: `dmidecode` パッケージに含まれる)



## 類似コマンドと差異
| 類似コマンド | 差異 / 使い分けのポイント | 推奨 |
| :--- | :--- | :--- |
| `lshw` | ハードウェア全体をツリー形式で表示。ソフトウェア的な認識状況も含む。 | 総合的な構成確認 |
| `lspci / lsusb` | PCI/USBバスに接続されたデバイスを列挙。デバイスIDの特定に強い。 | 接続デバイスの調査 |
| `lscpu` | CPU のアーキテクチャや機能フラグに特化。 | CPU 詳細の確認 |
| `dmidecode` | BIOS/マザーボードが持つ「物理的な」情報を抽出。シリアル番号等に強い。 | **物理構成・資産管理** |

## よく連携されるコマンド
### シナリオ例: メモリスロットの空き状況確認 (運用視点)
* **目的**: サーバのメモリ増設を検討する際、わざわざデータセンターに行かずに、スロット数と現在の装着状況を確認する。
* **連携コマンド**: `grep`, `less`
* **解説**: DMI タイプ 17 (Memory Device) を指定して抽出します。
* **コマンド例**:
    ```bash
    # メモリスロットごとの詳細情報を表示
    sudo dmidecode -t memory
    ```

## オプション説明
※実行には **root 権限** が必須です。

| オプション | 説明 |
| :--- | :--- |
| **情報の抽出** | |
| ⭐️ `-t <type>`, `--type` | 特定の DMI タイプを指定して表示（例: `bios`, `system`, `baseboard`, `chassis`, `processor`, `memory`, `cache`, `connector`, `slot`）。 |
| ⭐️ `-s <keyword>`, `--string` | 特定のキーワードに関連する値のみを単一行で表示する（スクリプト利用に最適）。 |
| `-u`, `--dump` | 処理されていない生のバイナリデータをダンプする（デバッグ用）。 |
| **その他** | |
| `--no-sysfs` | `/sys` 経由ではなく、直接 `/dev/mem` から読み取る（古いシステム用）。 |

### 主要な DMI タイプ番号
* **0**: BIOS 情報
* **1**: システム情報（メーカー、製品名、シリアル番号）
* **2**: ベースボード（マザーボード）情報
* **3**: シャーシ（筐体）情報
* **4**: プロセッサ情報
* **17**: メモリデバイス情報

## よく使われるオプション組み合わせと業務シナリオ

### 1. インフラ構築・運用視点
* **タスク**: サーバのシリアル番号（サービスキー）の取得。
* **組み合わせ**: `dmidecode -s system-serial-number`
* **解説**: メーカーサポートに連絡する際などに、物理的なラベルを見ることなく正確なシリアルを取得します。
* **例**:
    ```bash
    # システムのシリアル番号のみを表示
    sudo dmidecode -s system-serial-number
    ```

### 2. ブルーチーム視点
* **タスク**: ハードウェアレベルでの資産整合性チェック。
* **組み合わせ**: `dmidecode -t baseboard`
* **解説**: 物理マシンのマザーボードが予期せず交換されていないか、あるいは「本来あるはずのない」物理的な接続ポートがないかを確認します。
* **例**:
    ```bash
    # マザーボードのメーカーとバージョンを確認
    sudo dmidecode -t 2
    ```

### 3. レッドチーム視点
* **タスク**: 仮想環境（サンドボックス）の検知。
* **組み合わせ**: `dmidecode -s system-manufacturer`
* **解説**: 多くの仮想化ツールは、DMI 情報に自身の名前を残します。マルウェアなどが解析を逃れるために、ここを見て動作を止める手法（Anti-VM）によく使われます。
* **例**:
    ```bash
    # 製造元が "VMware", "Oracle", "QEMU" 等でないか確認
    sudo dmidecode -s system-product-name
    ```

## エラーメッセージとトラブルシューティング

1.  **エラーメッセージ例 1**: `/dev/mem: Permission denied`
    * **原因**: root 権限（sudo）なしで実行している。
    * **解決策**: `sudo dmidecode` を使用する。

2.  **エラーメッセージ例 2**: `# No SMBIOS nor DMI entry point found, sorry.`
    * **原因**: 実行環境が DMI/SMBIOS に対応していない（一部の ARM ボードや、古いコンテナ環境など）。
    * **解決策**: `lscpu` や `/proc/cpuinfo` など、他の手段で情報を収集する。

## セキュリティに関する考慮事項

### 脆弱性と悪用事例
* **情報漏洩**: `dmidecode` の出力には、UUID やシリアル番号といった、ハードウェアをユニークに特定できる情報が含まれます。これが一般ユーザーに漏れると、デバイスの追跡や特定のハードウェアを標的にした攻撃の足掛かりになります。

### GTFOBins における利用例
`dmidecode` に `sudo` 権限がある場合、本来読み取れないファイル（`/dev/mem`）の一部をダンプする機能などを介して、メモリ上の機密情報を収集できる可能性があります。
* **機能**: `Sudo`
* **参照**: `https://gtfobins.github.io/gtfobins/dmidecode/`

### 対応策・緩和策 (ブルーチーム視点)
* **Prevention (予防)**: 一般ユーザーに `sudo dmidecode` を許可しない。
* **Detection (検知)**: 不自然なハードウェア情報の収集（特に仮想環境検知を目的としたもの）を `auditd` で監視する。

## 注意点・補足
* **情報の信頼性**: DMI の内容は BIOS ベンダーが書き込んだものであり、100% 正確とは限りません（特に安価なマザーボードでは、シリアルが `Default string` になっていることもあります）。
* **コンテナ内**: Docker などのコンテナ内からは、通常特権を与えない限り DMI 情報にはアクセスできません。
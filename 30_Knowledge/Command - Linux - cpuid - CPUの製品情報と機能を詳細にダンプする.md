---
# --- YAML Frontmatter ---
created: 2026-02-22 17:02
modified: 2026-02-23 09:52
environment: [OS/Linux, Architecture/x86_64]
vulnearability: [Side_Channel_Attack, Anti-VM_Techniques]
knowledge_category: Command
tags:
  - cpuid
  - cpu
  - hardware-info
  - forensics
  - knowledge_base
---

# Command - Linux - cpuid - CPUの製品情報と機能を詳細にダンプする

## 概要

`cpuid` は、x86プロセッサの `CPUID` 命令を実行し、CPUの詳細なアーキテクチャ情報、サポートされている機能フラグ（AES-NI, AVX, SGX等）、キャッシュ構成、トポロジなどを表示するツールです。`/proc/cpuinfo` よりも遥かに詳細な生データにアクセスできます。

(出自: `cpuid` パッケージに含まれる)

## 類似コマンドと差異

| 類似コマンド | 差異 / 使い分けのポイント | 推奨 |
| :--- | :--- | :--- |
| `lscpu` | 人間が読みやすい形式でCPUの概要を表示する。一般的な用途に最適。 | 概要の確認 |
| `cat /proc/cpuinfo` | カーネルが認識しているCPU情報をテキスト形式で表示。OS標準。 | インストール不要の確認 |
| `cpuid` | 全ての「リーフ（情報の階層）」をダンプする。特定のハードウェア機能の有無を厳密に調べる際に必要。 | 詳細なハードウェア解析 |

## よく連携されるコマンド

### シナリオ例: 特定のハードウェア機能の有無を確認 (運用・セキュリティ視点)

* **目的**: 仮想化支援機能や暗号化支援機能が物理CPUで有効かを確認する。
* **連携コマンド**: `grep`
* **解説**: `cpuid` の膨大な出力から、特定の機能フラグ（例: `vmx`, `aes`）を抽出します。
* **コマンド例**:
    ```bash
    # Intel VT-x (仮想化支援) がサポートされているか確認
    cpuid -1 | grep -i vmx
    # AES-NI (暗号化加速) がサポートされているか確認
    cpuid -1 | grep -i aes
    ```

## オプション説明

| オプション | 説明 |
| :--- | :--- |
| **表示・出力** | |
| ⭐️ `-1`, `--one-cpu` | 最初のCPU（CPU 0）の情報のみを表示する。通常はこれで十分。 |
| ⭐️ `-r`, `--raw` | 16進数の生データ（レジスタの値）を出力する。解析ツールに渡す際に有用。 |
| `-f`, `--file=<file>` | `cpuid` の生出力を記録したファイルから情報を読み取って表示する。 |
| **特定情報の抽出** | |
| `-l <leaf>` | 指定したリーフ（階層）の情報を表示する。 |
| `-s <subleaf>` | 指定したサブリーフの情報を表示する。 |

## よく使われるオプション組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: ハイパーバイザ（KVM/VMware）構築前のハードウェア要件確認。
* **組み合わせ**: `cpuid -1`
* **解説**: 物理サーバが EPT (Extended Page Tables) などの高度な仮想化機能をサポートしているか、BIOSで有効化されているかを最終確認します。
* **例**:
    ```bash
    # EPTのサポートを確認
    sudo cpuid -1 | grep -i ept
    ```

### 2. ブルーチーム視点

* **タスク**: サイドチャネル攻撃（Spectre/Meltdown等）に対するハードウェアレベルの対策状況の調査。
* **組み合わせ**: `cpuid -1`
* **解説**: CPUが不変な緩和策（IBRS, STIBP 等のフラグ）をハードウェアレベルで持っているかを確認し、パッチ適用の優先順位を判断します。
* **例**:
    ```bash
    # 投機的実行に関連するセキュリティ機能フラグを確認
    cpuid -1 | grep -E "IBRS|IBPB|STIBP"
    ```

### 3. レッドチーム視点

* **タスク**: マルウェアの「サンドボックス検知・回避」ロジックのデバッグ。
* **組み合わせ**: `cpuid -1`
* **解説**: 仮想マシン（VM）内では、特定の CPUID リーフがハイパーバイザ特有の値（例: `hypervisor bit`）を返します。自身のコードが正しく環境を識別できるか確認します。
* **例**:
    ```bash
    # ハイパーバイザの存在を示すビット（31bit of ECX for leaf 1）を確認
    cpuid -1 -l 1 | grep "hypervisor guest status"
    ```

## 実務ログ例 (cpuid -1 の一部)

```text
   feature information (1/edx):
      x87 FPU on chip                        = true
      virtual-8086 mode enhancement          = true
      debugging extensions                   = true
      page size extensions                   = true
      time stamp counter                     = true
      ...
      AES instruction extensions             = true
      hypervisor guest status                = false
````

> **解説**: `AES instruction extensions = true` なのでAES暗号化が高速。`hypervisor guest status = false` なので、このプロセスは（少なくとも自己申告上は）物理マシン上で動いています。

## エラーメッセージとトラブルシューティング

1. **エラーメッセージ例 1**: `bash: cpuid: command not found`
    
    - **考えられる原因**: パッケージがインストールされていない。
    - **解決策**: `sudo apt install cpuid` (Debian系) または `sudo yum install cpuid` (RHEL系) でインストール。
        
2. **出力が /proc/cpuinfo と異なる**:
    
    - **考えられる原因**: 仮想化環境（VMware/VirtualBox等）では、ハイパーバイザが特定の CPUID 命令をインターセプトし、値を偽装している。
    - **解決策**: 物理マシンの情報を得たい場合は、ホストOS上で実行する。

## セキュリティに関する考慮事項

### 脆弱性と悪用事例

- **脆弱性**: **Side-Channel Attacks**。`CPUID` を介して正確なCPUモデルやステッピングを特定されることで、特定のハードウェア脆弱性を突くエクスプロイトの標的にされやすくなります。
- **悪用シナリオ**: マルウェアが `CPUID` を実行し、環境が Cuckoo Sandbox や特定の EDR 隔離環境であると判断した場合、悪意ある挙動を停止して解析を回避する（Anti-Analysis）。

### 対応策・緩和策 (ブルーチーム視点)

- **Prevention (予防)**: 仮想化環境の設定で「CPUID Masking」や「CPUID Virtualization」を適切に構成し、ゲストOSに見せる情報を制限する。
- **Detection (検知)**: 不自然な頻度で CPUID 命令を発行するプロセスを監視する（ただし、正規のアプリも起動時に頻繁に呼ぶため、検知は困難）。

## 注意点・補足

- **アーキテクチャ依存**: このコマンドは主に x86/x86_64 アーキテクチャ用です。ARM 環境では `lscpu` や `/proc/cpuinfo` が主な手段となり、`cpuid` コマンドは動作しないか、異なる挙動をします。


---
created: 2025-10-04 08:22
modified: 2026-02-22 09:30
---

# あなたの役割

あなたは、ネットワーク技術、セキュリティ分野、ペンテスト分野に精通したAIです。

# 前提

私は現在IT関連の仕事をしています。内容としては、インフラ構築については、ネットワークインフラの構築、各種サービスのサーバ構築、dockerやvmwareでの軽易な検証環境の構築です。キャリアアップのため、ブルーチームやレッドチームについても学んでいるところです。

ブルーチームとしては、ログの取得と解析要領、セキュアなサーバ構築、ファイヤーウォールの構築要領、WAFやIDSの構築要領とそのルール設計

レッドチームとしては、hack the boxやtry hack meによる実習、SANSの504や542へ向けての学習をしています。

最終的な学習目標は、OSCPの合格と中小企業規模のネットワーク設計、構築です。

# 出力

windows - powershell - [ここにコマンド名を入力] コマンドについて

`Command - <LinuxやWindows、Web_Server、Git等の依存する環境> - powershell - <コマンド名> - <コマンドの簡潔な概要>.md`を作成してください。

# 条件

- ドキュメントは下記の構成に従って作成する
  - インデックスの項目構成
  - コマンド詳細ファイルの項目構成
  - 参考にするサイト
- 概念説明だけで終わらせないこと
- 実務ログ例を含めること
- マークダウン形式で作成する
- 日本語で記述すること
- **追加の依頼（特定のセクションの深掘り、修正など）の際は、出力はマークダウンのコードブロックで記述し、変更箇所のみを出力すること。**

## インデックスの項目構成

```yaml
---
# --- YAML Frontmatter ---
# ドキュメントのライフサイクルや環境、脆弱性の種類を定義します。

created: 'YYYY-MM-DD' # ドキュメントの作成日を記録します。情報の鮮度を確認する際に役立ちます。
modified: 'YYYY-MM-DD' # 最終更新日を記録します。更新履歴の管理に利用します。

environment: [] 
# 実行環境や対象システムを指定します。
# 例: [OS/Linux, Web/Nginx, DB/PostgreSQL]
# 特定のOSやソフトウェアに依存する情報をフィルタリングするために重要です。

vulnearability: [] 
# 関連する脆弱性の種類や攻撃手法を記録します。
# 例: [RCE, SQLi, IDOR, Broken_Auth]
# 先ほど英語化したような攻撃手法をタグ付けし、後から特定の攻撃に関する情報を逆引きできます。

knowledge_category: Command
# 情報の性質を分類します。
# 例: 'Command', 'OS', 'Server', 'Network', 'Web-arch', 'Vuln-theory', 'Exploit-technique', 'Coding', 'Osint', 'Training', 'Language', 'Security'

tags:
  - '<command>' # コマンド名（例: whoami, ping, ipconfig）
  - '<shell>' # シェル名（例: cmd, powershell, bash）
  - '<Specific_Tag>' # 具体的な要素（例: nmap, metasploit, active_directory）
  - 'knowledge_base' # ドキュメントの種類を識別する共通タグ
---
```



## コマンド詳細ファイルの項目構成　PowerShell

```markdown
---
created:
modified:
environment: [] # 例: [OS/Windows11, Web/Apache2, DB/MySQL]
vulnearability: [] # 例: [SQLi, SSXi, OSCommandi]
knowledge_category:  Command
tags:
---

# Command - Windows - powershell - <コマンド名> - <コマンドの簡潔な概要>

## 概要
`<コマンドレット名>` の基本的な役割、目的を説明します。PowerShellのオブジェクトパイプラインでどのように機能するかに焦点を当てます。

## 類似コマンドレット/コマンドとの差異
| 類似コマンドレット/コマンド | 差異 / 使い分けのポイント |
| :--- | :--- |
| `<例: Get-Content>` | `<例: cat (type)はテキスト行を文字列として出力するが、Get-Contentはそれをオブジェクトとしてパイプラインに渡すことができる。>` |
| `<例: ls (dir)>` | `<例: dirはファイル/ディレクトリ情報をテキストで表示するが、Get-ChildItemはファイルシステムオブジェクトを返し、後続のコマンドレットでプロパティを操作できる。>` |

## よく連携されるコマンドレット

### シナリオ例: <具体的なタスク名> (<視点>)
* **目的**: <この連携で達成したい具体的なゴールを記述>
* **連携コマンドレット**: `<Cmdlet1>`, `<Cmdlet2>`, ...
* **解説**: <オブジェクトがパイプラインを通じてどのように渡され、処理されるかを解説>
* **コマンド例**:
    ```powershell
    # このコマンドが何を実行するのかを簡潔に説明
    Get-Process | Where-Object { $_.CPU -gt 100 } | Stop-Process -Confirm
    ```

## パラメータ説明
| パラメータ | 型 | 説明 |
| :--- | :--- | :--- |
| **<機能グループ1>** | | |
| ⭐ `-Path` | `String[]` | <特によく使われるパラメータの説明> |
| `-Filter` | `String` | <パラメータの説明> |

## よく使われる組み合わせと業務シナリオ
（CMDと同様の構成）

## エラーメッセージとトラブルシューティング
（CMDと同様の構成、エラーメッセージはPowerShell特有のものに）

## セキュリティに関する考慮事項
### 悪用事例
* **悪用シナリオ**: <例: `Invoke-Expression` (IEX) を使ったペイロードのメモリ上での実行、PowerShell Remotingによるラテラルムーブメントなど>
### LOLBASにおける利用例
（CMDと同様の構成）
### 対応策・緩和策 (ブルーチーム視点)
* **Prevention (予防)**: <例: Constrained Language Modeの適用、PowerShellスクリプト実行ポリシーの強化(Signedが望ましい)>
* **Detection (検知)**: <例: PowerShellスクリプトブロックロギングやモジュールロギングの有効化と監視>

## 注意点・補足
* **PowerShellのバージョンによる違い**: <例: PowerShell 5.1 と PowerShell 7での挙動の違い>
* **オブジェクトの操作**: <例: `Select-Object`, `Where-Object` との連携の重要性>

---
```

## 参考にするサイト

<https://github.com/FavioVazquez/ds-cheatsheets>

<https://github.com/Kitsun3Sec/Pentest-Cheat-Sheets>

<https://lolbas-project.github.io/>

<https://gtfobins.github.io/>

<https://book.hacktricks.wiki/en/index.html>

<https://swisskyrepo.github.io/PayloadsAllTheThings/>

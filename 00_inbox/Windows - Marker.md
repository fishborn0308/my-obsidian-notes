# Marker 公式仕様準拠リファレンス (v0.4.0+)

[datalab-to/marker](https://github.com/datalab-to/marker) の最新ドキュメントに基づいたコマンド構成です。

## 1. 単一ファイルの変換 (`marker_single`)

特定のPDFファイルを1つだけ変換する場合に使用します。

### 基本構文

```powershell
marker_single "入力ファイルパス" --output_dir "出力先フォルダ" --langs Japanese

```

### 主要オプション

| オプション | 説明 | 指定例 |
| --- | --- | --- |
| `--output_dir` | 変換結果（md、画像、メタデータ）の保存先。 | 必須 |
| **`--langs`** | OCRで使用する言語。カンマ区切りで複数指定。 | `Japanese` または `Japanese,English` |
| `--max_pages` | 処理する最大ページ数。 | `10` |
| `--force_ocr` | テキストレイヤーがある場合でも強制的にOCRを実行。 | フラグのみ |
| `--use_llm` | レイアウト修正やデータ抽出にLLM（OpenAI等）を使用。 | フラグのみ |

---

## 2. フォルダ一括変換 (`marker`)

大量のPDFファイルを一括で処理する際に使用します。

### 基本構文

```powershell
marker "入力フォルダパス" "出力フォルダパス" --langs Japanese --workers 4

```

### 主要オプション

| オプション | 説明 | 指定例 |
| --- | --- | --- |
| **`--workers`** | 並列処理するプロセス数。 | `4` (CPUコア数に合わせて調整) |
| `--langs` | 使用する言語。 | `Japanese` |
| `--metadata_file` | 設定等を記述したJSONファイルを指定。 | `config.json` |

---

## 3. GitHub仕様に基づく実例

### 実例1：標準的な日本語PDFの変換

公式ドキュメントでは言語名は `Japanese` のようにフルネームで指定することが推奨されています。

```powershell
marker_single "sample.pdf" --output_dir "./output" --langs Japanese

```

### 実例2：16コアCPUを活かした高速一括処理

Minisforum H285のマルチコア性能を活かす場合、`--workers` を指定します。

```powershell
marker "./input_pdfs" "./output_mds" --langs Japanese --workers 4

```

### 実例3：特定のページ範囲のみテスト

大規模なPDFの冒頭だけを確認したい場合です。

```powershell
marker_single "heavy_book.pdf" --output_dir "./test" --langs Japanese --max_pages 5

```

---
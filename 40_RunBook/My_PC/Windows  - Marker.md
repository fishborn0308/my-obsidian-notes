# 【完全版】PDF→Markdown変換環境「Marker」構築マニュアル

このマニュアルは、Windows環境において、C++ビルドエラーを回避しつつ高精度なPDF解析環境を構築するための手順です。

## 1. Python環境の準備

1. [python.org](https://www.python.org/) からインストーラーをダウンロード。
    
2. **重要:** インストール時に **「Add Python.exe to PATH」** に必ずチェックを入れる。
    
3. 作業フォルダを作成し、仮想環境を構築する。
    
    PowerShell
    
    ```
    mkdir pdf-to-md
    cd pdf-to-md
    python -m venv venv
    .\venv\Scripts\activate
    ```
    
    ※ 成功するとプロンプトに `(venv)` と表示されます。
    

## 2. Tesseract OCR（光学文字認識）の導入

スキャン済みPDFの文字を読み取るために必要です。

1. [Tesseract OCR Installer](https://www.google.com/search?q=https://github.com/UB-Mannheim/tesseract/wiki) を実行。
    
2. 「Additional language data」で **Japanese** を選択。
    
3. **環境変数の設定:** `C:\Program Files\Tesseract-OCR` をシステムの **Path** に追加する。
    
4. 確認コマンド: `tesseract --version`
    

## 3. C++ ビルドツールの導入（今回の最重要ステップ）

Pythonライブラリ（Pillow, regex等）のビルドエラーを解消します。

1. [Visual Studio Build Tools](https://www.google.com/search?q=https://visualstudio.microsoft.com/visual-cpp-build-tools/) をダウンロードして実行。
    
2. インストーラー内で **「C++ によるデスクトップ開発」** にチェックを入れる。
    
3. 右側の詳細オプションで「MSVC v143...」や「Windows 11 SDK」が選択されていることを確認し、インストール。
    
4. **完了後、必ずPCを再起動する。**
    

## 4. Markerのインストール

ビルドツール導入後、仮想環境 `(venv)` を再度有効化してから実行します。

PowerShell

```
# pip自体を最新にする
python -m pip install --upgrade pip

# Markerをインストール
pip install marker-pdf
```

## 5. 変換の実行方法

インストールが完了したら、以下のコマンドでPDFを変換できます。

PowerShell

```
# 単一ファイルの変換（日本語指定）
marker_single "入力ファイル.pdf" --output_dir ./output --lang_load japanese
```

---

### 注意事項

- **パスの競合:** `marker_single` が見つからない場合は、`python -m marker.convert_single` を代わりに使用してください。
    
- **メモリ活用:** 64GBのメモリがあるため、大量のファイルを扱う際は `marker` コマンドで `--workers 4` 以上を指定すると高速化します。
    

---

### 次のステップ

まずは **Visual Studio Build Tools** のインストールを完了させ、PCを再起動してください。

再起動後、改めて `pip install marker-pdf` を実行した結果（成功したか、あるいは別のエラーが出たか）を教えていただけますか？そこを乗り越えれば、ついに変換作業が始められます！
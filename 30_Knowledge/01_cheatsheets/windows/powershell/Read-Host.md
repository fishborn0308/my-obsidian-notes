---
tags:
  - 'Read-Host'
  - 'powershell'
  - 'cheatsheet'
title: 'Read-Host - コンソールから入力を読み取る'
summary: 'コンソール（ホスト）から一行の入力を読み取ります。ユーザーに対話的に情報を問い合わせるために使用されます。'
related:
  - 'Write-Host'
  - 'Get-Credential'
  - 'set /p'
---

# `Read-Host` - コンソールから入力を読み取る

## 概要

`Read-Host` コマンドレットは、PowerShellコンソールでスクリプトの実行を一時停止し、ユーザーからのキーボード入力を待ち受けます。入力された一行のテキストは、文字列または `SecureString` オブジェクトとして返されます。

このコマンドレットは、スクリプトに対話性を持たせるための基本的な手段であり、ユーザーにファイルパス、サーバー名、確認（Y/N）、そしてパスワードなどを安全に入力させるために使用されます。

## 類似コマンドレット/コマンドとの差異

| 類似コマンドレット/コマンド | 差異 / 使い分けのポイント |
| :--- | :--- |
| **`set /p` (CMD)** | CMDの `set /p` と同じくユーザー入力を変数に格納します。`Read-Host` は、`-AsSecureString` を使ってパスワードなどの機密情報を安全に読み取れる点で優れています。 |
| **`Get-Credential`** | `Get-Credential` は、ユーザー名とパスワードの両方を要求する標準的なGUIダイアログを表示し、`PSCredential` オブジェクトを返します。`Read-Host` はコンソール内で直接入力を受け取ります。 |

## よく連携されるコマンドレット

### シナリオ例: ユーザーに確認を求めてから処理を実行する (インフラ構築・運用視点)

* **目的**: 破壊的な操作（サービスの停止など）を実行する前に、ユーザーに "Y" の入力を求めることで、誤操作を防ぐ。
* **連携コマンドレット**: `if`, `Stop-Service`
* **解説**: `Read-Host` でユーザーからの確認を取得し、その結果を `if` 文で評価します。入力が "Y" (大文字小文字を問わず) の場合にのみ、後続の処理を実行します。
* **コマンド例**:

    ```powershell
    # ユーザーに確認を求める
    $confirmation = Read-Host -Prompt "Are you sure you want to stop the 'Spooler' service? (Y/N)"

    # 入力が 'Y' の場合のみサービスを停止
    if ($confirmation -eq 'Y') {
        Stop-Service -Name "Spooler" -WhatIf
        Write-Host "Service stop command issued."
    } else {
        Write-Host "Operation cancelled."
    }
    ```

## パラメータ説明

| パラメータ | 型 | 説明 |
| :--- | :--- | :--- |
| ⭐ **`-Prompt`** | `Object` | ユーザーに表示するメッセージ（プロンプト）を指定します。 |
| ⭐ **`-AsSecureString`** | `SwitchParameter` | 入力されたテキストを、コンソールに表示せずに `SecureString` オブジェクトとして返します。パスワードの入力に必須です。 |

## よく使われる組み合わせと業務シナリオ

### 1. インフラ構築・運用視点

* **タスク**: スクリプトの実行対象となるサーバー名を、ユーザーに対話的に入力させる。
* **組み合わせ**: `Read-Host`
* **解説**: スクリプト内にサーバー名をハードコードする代わりに `Read-Host` を使うことで、スクリプトの再利用性を高めます。
* **例**:

    ```powershell
    # ユーザーからコンピュータ名を取得
    $computerName = Read-Host -Prompt "Enter the computer name to check services"

    # 入力されたコンピュータのサービスを取得
    Get-Service -ComputerName $computerName
    ```

### 2. ブルーチーム視点

* **タスク**: ガイド付きのインシデント対応スクリプトを作成する。
* **組み合わせ**: `Read-Host`
* **解説**: アナリストがインシデント対応を行う際に、次に行うべき操作（例: 「不審なプロセスのPIDを入力してください」「ネットワーク接続を隔離しますか？　Y/N」）を `Read-Host` で問いかけ、手順をガイドするスクリプトを作成します。
* **例**:

    ```powershell
    $pidToInvestigate = Read-Host -Prompt "Enter the Process ID to investigate"
    Get-Process -Id $pidToInvestigate | Select-Object *
    ```

### 3. レッドチーム視点

* **タスク**: **ソーシャルエンジニアリング**。偽のプロンプトを表示して、ユーザーに認証情報を入力させる。
* **組み合わせ**: `Read-Host -AsSecureString`
* **解説**: 攻撃者は、正規のアプリケーションやシステムメッセージに見せかけた偽のプロンプト（例: 「セッションがタイムアウトしました。パスワードを再入力してください:」）を表示するスクリプトを作成します。`Read-Host -AsSecureString` で入力させることで、画面上には `****` と表示され、ユーザーは正規のパスワード入力であると誤信しやすくなります。窃取された `SecureString` は、その後平文に戻されて悪用されます。
* **例**:

    ```powershell
    # 偽のプロンプトでパスワードを窃取
    $securePassword = Read-Host -Prompt "Enter your network password to continue" -AsSecureString
    # (この後、$securePassword を平文に変換して外部に送信する処理が続く)
    ```

## エラーメッセージとトラブルシューティング

* `Read-Host` はユーザーの入力を待つだけのコマンドレットなので、エラーが発生することは稀です。

## セキュリティに関する考慮事項

### 悪用事例

* **認証情報の窃取**: レッドチームのシナリオで述べた通り、`Read-Host` は、ユーザーを騙して認証情報を入力させるためのフィッシングスクリプトを作成するのに利用されます。

### LOLBASにおける利用例

* **機能**: **認証情報アクセス (Credential Access)**
* **解説**: `Read-Host` は PowerShell の標準機能であり、攻撃者がユーザーとの対話を通じて、検知されにくい形で認証情報を窃取するために利用されます。

### 対応策・緩和策 (ブルーチーム視点)

* **Prevention (予防)**: 不明なスクリプトを実行しない、安易に認証情報を入力しない、といったユーザー教育が最も重要です。
* **Detection (検知)**: PowerShellのスクリプトブロックロギング (イベントID 4104) を有効化します。これにより、`Read-Host` を含む不審なスクリプトの実行が記録されます。特に、`Read-Host -AsSecureString` の直後に、`System.Net.WebClient` や `Invoke-RestMethod` のようなネットワーク通信コマンドが続く場合は、認証情報窃取の試みである可能性が高いです。

## 注意点・補足

* **`SecureString`**: `-AsSecureString` で取得したパスワードは、メモリ上で暗号化された `SecureString` オブジェクトとして扱われます。スクリプト内で平文のパスワードを直接扱うよりも安全ですが、平文に戻す方法も存在するため、完全な銀の弾丸ではないことを理解しておく必要があります。

---
[PowerShellインデックスに戻る](../../powershell_index.md)

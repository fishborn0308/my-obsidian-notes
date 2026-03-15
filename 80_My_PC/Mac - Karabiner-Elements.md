---
created: 2026-01-05 20:19
modified: 2026-02-20 16:15
tags:
  - Karabiner-Elements
  - config
---

# Mac - Karabiner-Elements

## 1. 最優先設定：RDP時の「Cmd → Ctrl」変換

Microsoft Remote Desktop（Beta）を使用中、Macの `Command` キーを Windowsの `Control` に、`Option` キーを `Windows（Start）` キーとして機能させる設定です。

### 【設定方法】

Karabiner-Elementsの「Complex Modifications（複雑な変更）」で、以下のルールを導入します。

- **目的:** Windows上でも「Command + C」でコピーができるようにする。
- **ルール名例:** `Exchange Command and Control (only in Remote Desktop)`

> [!IMPORTANT]
>
> なぜ単純な入れ替えではダメなのか？
>
> 単純な「Simple Modification」で入れ替えると、Mac自体の操作（SpotlightやBTTジェスチャ）まで狂ってしまいます。**「RDPアプリが最前面にある時だけ入れ替える」**という条件付き設定がベストです。

---

## 2. 日本語入力の最適化（MX Keys S / Winキーボード用）

Magic Keyboard以外のキーボード（MX Keys S等）を使用する場合、Mac標準の「英数」「かな」キーがないことがあります。これを解決します。

- **設定案:**

		- **左Command（単押し）:** `英数` (EISU) に変換
				
		- **右Command（単押し）:** `かな` (KANA) に変換
				
- **メリット:** スペースキーの左右を叩くだけで入力モードを確実に切り替えられます。これはMacユーザーの間で最も人気のあるカスタマイズの一つです。

---

## 3. セキュリティ・ペンテスト作業用のカスタマイズ

ターミナル（iTerm2）やWSL2を多用するユーザー様向けに、特定のキーに高度な役割を持たせます。

### ① Caps Lock の有効活用

- **設定:** `Caps Lock` を `F19`（または別の修飾キー）に変更し、BTTやRaycastの「ハイパーキー（全修飾キー同時押し）」として利用。
- **用途:** `Caps Lock + T` で即座にターミナルを起動するなど、ショートカットの競合を避けた独自コマンドを作成できます。

### ② Controlキーの配置

- **設定:** `Caps Lock` を単純に `Control` へ変更。
- **理由:** Linux/Unix操作では `Ctrl` キーを多用するため、小指の移動距離を最短にします。

---

## 4. 設定ファイルの保存と再現（Maintenance）

Karabinerの設定はすべて **`~/.config/karabiner/karabiner.json`** という一つのファイルに集約されています。

### バックアップ・フローへの追加

1. **保存先:** `/work/configs/karabiner/karabiner.json`
2. **自動化案:** シンボリックリンクを貼ることで、設定を変更するたびに自動的にバックアップ対象のフォルダが更新されるようにします。

		Bash
		
		```

		ln -s ~/.config/karabiner/karabiner.json ~/Documents/work/configs/karabiner/karabiner.json

		```
		

---

## 5. 導入手順のアドバイス

1. **公式サイトからインストール:** [Karabiner-Elements](https://karabiner-elements.pqrs.org/)
2. **Complex Modifications の追加:** 設定画面の「Complex Modifications」>「Add rule」>「Import more rules from the internet」をクリックし、**「Windows Shortcuts on macOS」** などの既成ルールをインポートして自分好みに微調整するのが最も早いです。

## JSONで設定する場合

### 1. Karabiner-Elements 用 JSON 定義

以下の内容をコピーして、後述の手順で設定ファイルに追加してください。

JSON

```
{
  "description": "RDP特化: CommandをControlに変換 (Microsoft Remote Desktop専用)",
  "manipulators": [
    {
      "type": "basic",
      "from": {
        "key_code": "left_command",
        "modifiers": { "optional": ["any"] }
      },
      "to": [
        {
          "key_code": "left_control"
        }
      ],
      "conditions": [
        {
          "type": "frontmost_application_if",
          "bundle_identifiers": [
            "^com\\.microsoft\\.rdc\\.macos$",
            "^com\\.microsoft\\.rdc\\.osx\\.beta$",
            "^com\\.microsoft\\.rdc\\.appstore$"
          ]
        }
      ]
    },
    {
      "type": "basic",
      "from": {
        "key_code": "right_command",
        "modifiers": { "optional": ["any"] }
      },
      "to": [
        {
          "key_code": "right_control"
        }
      ],
      "conditions": [
        {
          "type": "frontmost_application_if",
          "bundle_identifiers": [
            "^com\\.microsoft\\.rdc\\.macos$",
            "^com\\.microsoft\\.rdc\\.osx\\.beta$",
            "^com\\.microsoft\\.rdc\\.appstore$"
          ]
        }
      ]
    }
  ]
}
```

---

### 2. 設定の反映手順

1. **Karabiner-Elements を開く:**

		- `Settings` 画面の `Complex Modifications` タブを選択します。
				
2. **JSONの挿入画面を開く:**

		- `Add your own rule`をクリック
				
3. **JSONの挿入:**

		- `profiles` > `complex_modifications` > `rules` の配列の中に、上記の JSON オブジェクトを貼り付けます。
				
4. **保存して有効化:**

		- ファイルを保存すると即座に反映されます。`Microsoft Remote Desktop` をアクティブにして、挙動を確認してください。
				

---

### 3. この定義のポイント（インフラエンジニア的視点）

- **条件付き実行 (`frontmost_application_if`):**

		- この設定は、RDPアプリが「最前面」にある時だけ動作します。そのため、Macのデスクトップに戻れば、Commandキーは通常のMacの挙動に戻ります。
				
- **Beta版への対応:**

		- `bundle_identifiers` に `com.microsoft.rdc.osx.beta` を含めているため、ユーザー様が使用予定のBeta版でも確実に動作します。
				
- **修飾キーの継承:**

		- `"modifiers": { "optional": ["any"] }` を入れているため、「Command + Shift + V」などの組み合わせ操作も、Windows側で「Control + Shift + V」として正しく認識されます。
				

### 4. 応用：さらにWindowsに寄せたい場合

もし「MacのOptionキーをWindowsキーとして使いたい」場合は、上記と同様の構造で `from: left_option` を `to: left_gui` (Windowsキー) に変換する Manipulator を追加すればOKです。

まずはこの「Command → Control」を導入して、RDPフル画面環境でのコピー＆ペーストの快適さをテストしてみてください。これで「Macの皮を被ったWindows」環境の操作感は完成です。

### ⚙️ アプリ別・初期設定チェックリスト

#### 🍎 Mac：操作性と開発環境の最適化

- [ ] **1Password**
    
    - [ ] ブラウザ拡張機能の有効化。
        
    - [ ] **SSH Agent** を有効化（`~/.ssh/config` の管理を 1Password に統合）。
        
- [ ] **VS Code**
    
    - [ ] **Settings Sync** を有効化（GitHub/Microsoftアカウントで設定同期）。
	    - 同期に失敗した場合は[[02-VSCode・Obsidian]]から        
- [ ] **Raycast**
    
    - [ ] ホットキーを `Option + Space`（または `Cmd + Space`）に変更。
        
    - [ ] よく使うWebサイトを **Quicklinks** に登録。
        
- [ ] **Karabiner-Elements**
    
    - [ ] `Complex Modifications` で「Caps Lock を Ctrl または Esc」に変更（HHKB/エンジニア向け定番設定）。
        
- [ ] **iTerm2**
    
    - [ ] `Natural Text Editing` を有効化（`Option + 矢印` での単語移動を可能に）。
        
    - [ ] `Keepa-alive` 設定（SSH接続が切れないようにする）。
        
- [ ] **Little Snitch 6**
    
    - [ ] 最初は **Alert Mode** で運用し、不要なアウトバウンド通信を特定してブロックルールを作成。
        
- [ ] **Tailscale**
    
    - [ ] ログインし、他のデバイス（Windows/スマホ）との疎通確認。
        

#### 💻 Windows：仮想化と診断環境の構築

- [ ] **WSL2 (Kali Linux)**
    
    - [ ] `sudo apt update && sudo apt upgrade` の実行。
        
    - [ ] 日本語環境（フォント/ロケール）のセットアップ。
        
    - [ ] Macと共有するSSH公開鍵の配置。
        
- [ ] **VMware / GNS3**
    
    - [ ] `Virtual Network Editor` でホストオンリー/NATネットワークのセグメントを固定。
        
    - [ ] GNS3 VM のインポートと疎通確認。
        
- [ ] **Firefox (Pentest用)**
    
    - [ ] **Burp Suite** の CA証明書をインポート（HTTPS通信のデコードに必須）。
        
    - [ ] **FoxyProxy** で `127.0.0.1:8080` (Burp) を設定。
        
- [ ] **MobaXterm**
    
    - [ ] 以前の環境から `Sessions.mxtpro` をインポート。
        
    - [ ] フォントを `Cascadia Code` や `Hack` などの等幅フォントに変更。
        
- [ ] **Microsoft PowerToys**
    
    - [ ] **FancyZones** で画面分割レイアウトを設定（大画面での作業効率化）。
        
    - [ ] **PowerToys Run**（MacのRaycastに近い操作感）のショートカット確認。
        

---

### 💾 バックアップ・復旧の初期設定（最重要）

- [ ] **Carbon Copy Cloner (CCC)**
    
    - [ ] バックアップ元（`~/Documents/work` など）と先（500GB HDD）を指定したタスクを作成。
        
    - [ ] スケジュールを「毎週」に設定。
        
- [ ] **Macrium Reflect (Windows)**
    
    - [ ] 「レスキューメディア（USBメモリ）」を作成（OSが起動しない時にこれが必要になります）。
        
    - [ ] 初回の「クリーンイメージ」を500GB HDDに保存。
        
- [ ] **1Password Emergency Kit**
    
    - [ ] エマージェンシーキット（PDF）を印刷、またはオフライン（物理HDD等）に保存。
        

---

### 🌐 ブラウザ拡張機能の同期

- [ ] **Brave (Mac/Windows両方)**
    
    - [ ] **Sync（同期チェーン）** を作成し、ブックマークと拡張機能の設定を同期。
        
    - [ ] **uBlock Origin** (標準機能で足りない場合) や **Dark Reader** の設定を調整。
        

---

### 💡 運用へのアドバイス

このリストを **Obsidian** の「環境構築」フォルダに保存しておき、PCを新調するたびに上からなぞるようにすると、セットアップが数時間で終わるようになります。

特に、**「1Password の SSH Agent」** と **「VS Code の Settings Sync」** は、MacとWindowsの垣根をなくす強力な設定ですので、真っ先に行うことをおすすめします。

次は、**「各アプリの設定ファイルを効率的にエクスポートしてバックアップフォルダに集約するスクリプト」**などを作成しましょうか？（例えば `cp` コマンドで設定ファイルを `/work/configs/` に集めるようなものです）
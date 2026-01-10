##  VM環境再構築・構成定義書テンプレート

###  1. 基本情報

- **VM名:** (例: Kali_Linux_Pentest_01)
    
- **ベースOSイメージ:** (例: kali-linux-2023.4-installer-amd64.iso)
    
- **最終更新日:** 202X/MM/DD
    
- **用途:** (例: Webアプリ脆弱性診断用)
    

### 2. リソース割り当て (Hardware Specs)

- **CPU:** (例: 4コア)
    
- **メモリ:** (例: 8GB)
    
- **ストレージ:** (例: 80GB / 薄型プロビジョニング)
    
- **ネットワークアダプタ:**
    
    - NIC1: NAT (ホスト経由通信用)
        
    - NIC2: Host-Only (実験セグメント用)
        
- **その他:** (例: USB 3.1コントローラー有効 / Logi Boltパススルー設定)
    

### 3. 初期設定・OSレイヤー (OS Provisioning)

- **ホスト名:** `kali-lab`
    
- **ユーザー/パスワード:** (1Password参照)
    
- **IPアドレス:** (例: 192.168.56.101 / 固定)
    
- **SSH設定:** `Port 22` / `PermitRootLogin no`
    

### 4. インストール済みツール・パッケージ

> [!TIP]
> 
> apt list --installed や pip freeze の結果をここに貼るか、インストールスクリプトのパスを記載します。

- **GUI関連:** (例: Xfce)
    
- **セキュリティツール:**
    
    - [ ] Burp Suite Community (vX.X)
        
    - [ ] Metasploit Framework
        
    - [ ] ffuf / nmap / dirsearch
        
- **独自スクリプト:** `/home/user/scripts/auto-recon.sh` (GitHub `infra-repo` 参照)
    

### 5. 特記事項・カスタマイズ (Custom Config)

- **プロキシ設定:** (例: FoxyProxy用設定済み)
    
- **ブラウザ拡張:** (例: Wappalyzer, Hack-Tools)
    
- **その他:**
    
    - `~/.zshrc` に `alias ls='ls -la'` を追加
        
    - `/etc/hosts` に標的サーバ `10.0.0.5 target.lab` を追記済み
        

### 6. スナップショット履歴

- **Golden Image:** [202X/MM/DD] 初期設定完了
    
- **Point A:** [202X/MM/DD] Burp設定・証明書インストール完了
    

---

### このテンプレートを運用するコツ

1. **設定ファイルはGitへ:** VM内の `.zshrc` やツールのConfigは、VMの中に置いたままにせず、Mac/Windows側のGit管理下のフォルダ (`/work/configs/vm/`) にシンボリックリンクを貼るか、コピーして保存しておきます。
    
2. **構築コマンドをメモする:** 「〇〇をインストール」と書くよりも、`sudo apt install -y xxx yyy` とコマンド形式で残しておくと、コピペだけで復旧が終わります。
    
3. **1Passwordとの連携:** パスワードや鍵情報は、このテンプレートには書かず、「1Passwordの〇〇というアイテムを参照」とだけ記載することでセキュリティを担保します。

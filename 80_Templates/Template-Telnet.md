## Telnet (Port 23)

### 🔎 Basic Info

- Version:
```
<貼り付け>
```
- Banner:
```
<貼り付け>
```

---

### 🚀 Phase 1: Initial Check（1〜2分以内）

#### 接続確認

- [ ] 実行
```bash
telnet $TARGET_IP 23
```

#### プロンプト確認

-  login promptあり
    ```
    <貼り付け>
    ```
-  password promptあり
    ```
    <貼り付け>
    ```
-  banner取得
    ```
    <貼り付け>
    ```

#### エラーメッセージ差分

- 内容：
    ```
    <貼り付け>
    ```

#### 接続結果分類

* [ ] login promptあり → 認証攻撃へ
* [ ] password promptあり
* [ ] 直接shell → Phase 6へ
* [ ] 何も返らない → サービス再確認
* [ ] 別プロトコルの可能性 → Phase 5へ

---

### 🔑 Phase 2: Default / Empty Auth（最優先）

#### 空パス試行

- [ ] 実行
```text
root:
admin:
user:
guest:
```

#### デフォルト認証

- [ ] 実行
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

#### 結果：

- [ ] 成功
    
- [ ] 失敗
    

---

### 🔄 Phase 3: Credential Reuse（最重要）

#### 他サービス

- [ ] FTP
    
- [ ] SMB
    
- [ ] HTTP
    
- [ ] NFS
    

#### 取得したクレデンシャル

| user | pass | source | tested |

| ---- | ---- | ------ | ------ |

| | | | [ ] |

#### Telnet再試行

```bash
telnet $TARGET_IP 23
```

##### 結果：

- [ ] 成功
- [ ] 失敗

---

### 🔍 Phase 3.5: User Enumeration（可能な場合）

#### レスポンス差分確認

* valid user:
```
<貼り付け>
```
* invalid user:
```
<貼り付け>
```

#### 有効ユーザー抽出

* 方法:
* 結果:

---

### 💣 Phase 4: Light Attack（制御付き）

#### ユーザーリスト

- source:
  ```
  <getlistの結果貼り付け>
  ```
    
- getlistは`$WORDLISTS/wordlist`に使用するﾜｰﾄﾞﾘｽﾄのｼﾝﾎﾞﾘｯｸﾘﾝｸを貼る
- `$WORDLISTS/wordlist` を使用

#### Password Spray

- [ ] 実行
```bash
hydra -L "$WORDLISTS/wordlist" -p Password123 -t 4 -W 3 -f telnet://$TARGET_IP
```

#### Empty Password Spray

- [ ] 実行
```bash
hydra -L "$WORDLISTS/wordlist" -p "" -t 4 -W 3 -f telnet://$TARGET_IP
```

* `-t 4`：負荷制御（安定性）
* `-W 3`：待機（ロックアウト回避）
* `-f`：1件ヒットで停止（OSCP向け）

---

### 🧠 Phase 5: Service Validation（例外処理）

#### 他ポート確認

- [ ] 実行
```bash
telnet $TARGET_IP 25
telnet $TARGET_IP 6379
telnet $TARGET_IP 80
```

#### 結果

- [ ] SMTP
    ```
    <貼り付け>
    ```
- [ ] Redis
    ```
    <貼り付け>
    ```
- [ ] HTTP
    ```
    <貼り付け>
    ```

---

### ❌ Decision（重要）

-  有効クレデンシャルなし → 一旦保留
-  他サービス優先
-  時間制限超過 → 捨て

---

### 📝 Notes

- Telnet = 認証の受け皿
- 単体で深追いしない
- creds待ちが本命

---

### 📊 Priority

|項目|優先度|
|---|---|
|Credential Reuse|★★★★★|
|Empty Password|★★★★☆|
|Default Auth|★★★☆☆|
|Spray|★★☆☆☆|
|Exploit|★☆☆☆☆|
# <% tp.file.title %>

## Target Info
- IP: <% tp.file.title %>
- Hostname: 
- URL: 

---
## Port Scan

### Primary
```zsh
scaninit
````

> scaninit の概要
>
> * Rustscan で高速ポート確認を試す
> * 結果が不安定な場合は `nmap --top-ports 1000` にフォールバックする
> * 開いているポートに対して service scan を行う
> * 保険として full port scan をバックグラウンドで開始する
>
> 失敗時の確認ポイント
>
> * `$TARGET_IP` が正しいか
> * `result/` 配下に `nmap_initial.*` または `nmap_top1000.*` が生成されているか
> * `result/nmap_service.*` が生成されているか
> * `result/nmap_full.*` と `log/nmap_full.log` が出ているか
> * Rustscan が `filtered` 多発または open 0 件になっていないか
>
> 想定フロー
>
> * 初動は scaninit の結果を見て、すぐ次の列挙に入る
> * full scan は裏で進める
> * autorecon は必要に応じて後から起動する
> * UDP は必要性が見えた時だけ追加する

### Optional

```zsh
# UDP が必要そうなとき
nmap -sU --top-ports 100 "$TARGET_IP" -oA "$OUT/nmap_udp"

# autorecon を後から回すとき
nohup autorecon "$TARGET_IP" -o "$OUT/autorecon" > "$OUT/autorecon/autorecon.log" 2>&1 &
```

> Optional の考え方
>
> * UDP は初動ではなく、必要性が見えてから回す
> * autorecon は初動列挙開始後に必要なら追加する
> * `nmap -p-` と autorecon を同時に重ねすぎると重くなるので注意する

## Scan Status

* [ ] initial scan
* [ ] service scan
* [ ] full port scan (bg)
* [ ] autorecon
* [ ] udp scan

---

## Open Ports

### Initial (AUTO)
```text
<auto:init>
```

### Service (AUTO)
```text
<auto:service>
```

### Full Delta (AUTO)
```text
<auto:full>
```

### UDP(Manual)
```text
<貼り付け>
```

### AutoRecon(Manual)
```text
<差異のみ記入>
```
---

## Services (AUTO)

<!-- auto_services_start --> <!-- auto_services_end -->

---

## Notes

* どのポートから攻めるか
* 気になるポイント

---

## Evidence


## Background Jobs

* [ ] full scan:
* [ ] autorecon:

## Troubleshooting Notes

* Rustscan failed:
* fallback used:
* filtered / timeout:
* retry notes:

## Next Actions

### Auto

```text
<auto_actions>
```

### Manual

```text
<manual>
```

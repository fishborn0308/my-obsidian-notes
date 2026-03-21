# <% tp.file.title %>

## Target Info
- IP: <% tp.file.title %>
- Hostname: 
- URL: 

---

## Port Scan

### Initial Scan

```zsh
rustscan -a $TARGET_IP --ulimit 5000 -- -sS -Pn -n -oA $OUT/nmap_initial
```

### Service Scan

```zsh
nmap -sC -sV -p $TARGET_PORT $TARGET_IP -oA $OUT/nmap_service
```

### Full Scan -BG

```zsh
nmap -p- $TARGET_IP -oA $OUT/nmap_full &
```

### Auto Scan

```zsh
nohup autorecon "$TARGET_IP" -o "$OUT" > "$OUT/autorecon.log" 2>&1 &
```

### UDP Scan

```zsh
nmap -sU --top-ports 100 $TARGET_IP -oA $OUT/nmap_UDP
```

## Open Port

```zsh
grep -E "open" $OUT/nmap_initial.nmap
grep -E "open|Service|Version" $OUT/nmap_service.nmap
```
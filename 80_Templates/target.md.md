# <% tp.file.title %>

## Target Info
- IP: <% tp.file.title %>
- Hostname: 
- URL: 

---

## Open Ports

```zsh
rustscan -a $TARGET_IP --ulimit 5000 -- -sC -sV -oA $OUT/nmap_initial
grep -E "open|Service|Version" $OUT/nmap_initial.nmap
```
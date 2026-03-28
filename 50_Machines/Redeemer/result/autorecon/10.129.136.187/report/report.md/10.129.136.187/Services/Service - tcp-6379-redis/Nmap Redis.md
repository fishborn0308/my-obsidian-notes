```bash
nmap -vv --reason -Pn -T4 -sV -p 6379 --script="banner,redis-info" -oN "/home/kali/Vault/Target/10.129.136.187/result/autorecon/10.129.136.187/scans/tcp6379/tcp_6379_redis_nmap.txt" -oX "/home/kali/Vault/Target/10.129.136.187/result/autorecon/10.129.136.187/scans/tcp6379/xml/tcp_6379_redis_nmap.xml" 10.129.136.187
```

[/home/kali/Vault/Target/10.129.136.187/result/autorecon/10.129.136.187/scans/tcp6379/tcp_6379_redis_nmap.txt](file:///home/kali/Vault/Target/10.129.136.187/result/autorecon/10.129.136.187/scans/tcp6379/tcp_6379_redis_nmap.txt):

```
# Nmap 7.98 scan initiated Sat Mar 28 09:06:50 2026 as: /usr/lib/nmap/nmap --privileged -vv --reason -Pn -T4 -sV -p 6379 --script=banner,redis-info -oN /home/kali/Vault/Target/10.129.136.187/result/autorecon/10.129.136.187/scans/tcp6379/tcp_6379_redis_nmap.txt -oX /home/kali/Vault/Target/10.129.136.187/result/autorecon/10.129.136.187/scans/tcp6379/xml/tcp_6379_redis_nmap.xml 10.129.136.187
Nmap scan report for Redeemer (10.129.136.187)
Host is up, received user-set (0.24s latency).
Scanned at 2026-03-28 09:06:50 EDT for 22s

PORT     STATE SERVICE REASON         VERSION
6379/tcp open  redis   syn-ack ttl 63 Redis key-value store 5.0.7 (64 bits)
| redis-info: 
|   Version: 5.0.7
|   Operating System: Linux 5.4.0-77-generic x86_64
|   Architecture: 64 bits
|   Process ID: 751
|   Used CPU (sys): 1.372208
|   Used CPU (user): 1.285252
|   Connected clients: 1
|   Connected slaves: 0
|   Used memory: 839.48K
|   Role: master
|   Bind addresses: 
|     0.0.0.0
|     ::1
|   Client connections: 
|_    10.10.16.40

Read data files from: /usr/share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sat Mar 28 09:07:12 2026 -- 1 IP address (1 host up) scanned in 22.65 seconds

```

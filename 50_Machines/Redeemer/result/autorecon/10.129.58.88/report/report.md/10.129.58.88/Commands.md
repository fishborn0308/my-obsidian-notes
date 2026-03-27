```bash
nmap -vv --reason -Pn -T4 -sV -sC --version-all -A --osscan-guess -oN "/home/kali/Vault/Target/10.129.58.88/result/autorecon/10.129.58.88/scans/_quick_tcp_nmap.txt" -oX "/home/kali/Vault/Target/10.129.58.88/result/autorecon/10.129.58.88/scans/xml/_quick_tcp_nmap.xml" 10.129.58.88

nmap -vv --reason -Pn -T4 -sV -sC --version-all -A --osscan-guess -p- -oN "/home/kali/Vault/Target/10.129.58.88/result/autorecon/10.129.58.88/scans/_full_tcp_nmap.txt" -oX "/home/kali/Vault/Target/10.129.58.88/result/autorecon/10.129.58.88/scans/xml/_full_tcp_nmap.xml" 10.129.58.88

nmap -vv --reason -Pn -T4 -sV -p 6379 --script="banner,redis-info" -oN "/home/kali/Vault/Target/10.129.58.88/result/autorecon/10.129.58.88/scans/tcp6379/tcp_6379_redis_nmap.txt" -oX "/home/kali/Vault/Target/10.129.58.88/result/autorecon/10.129.58.88/scans/tcp6379/xml/tcp_6379_redis_nmap.xml" 10.129.58.88

redis-cli -p 6379 -h 10.129.58.88 INFO

redis-cli -p 6379 -h 10.129.58.88 CONFIG GET '*'

redis-cli -p 6379 -h 10.129.58.88 CLIENT LIST


```
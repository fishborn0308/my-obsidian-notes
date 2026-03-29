```bash
nmap -vv --reason -Pn -T4 -sV -sC --version-all -A --osscan-guess -oN "/home/kali/Vault/Target/10.129.62.25/result/autorecon/10.129.62.25/scans/_quick_tcp_nmap.txt" -oX "/home/kali/Vault/Target/10.129.62.25/result/autorecon/10.129.62.25/scans/xml/_quick_tcp_nmap.xml" 10.129.62.25

nmap -vv --reason -Pn -T4 -sV -sC --version-all -A --osscan-guess -p- -oN "/home/kali/Vault/Target/10.129.62.25/result/autorecon/10.129.62.25/scans/_full_tcp_nmap.txt" -oX "/home/kali/Vault/Target/10.129.62.25/result/autorecon/10.129.62.25/scans/xml/_full_tcp_nmap.xml" 10.129.62.25

nmap -vv --reason -Pn -T4 -sV -p 21 --script="banner,(ftp* or ssl*) and not (brute or broadcast or dos or external or fuzzer)" -oN "/home/kali/Vault/Target/10.129.62.25/result/autorecon/10.129.62.25/scans/tcp21/tcp_21_ftp_nmap.txt" -oX "/home/kali/Vault/Target/10.129.62.25/result/autorecon/10.129.62.25/scans/tcp21/xml/tcp_21_ftp_nmap.xml" 10.129.62.25


```
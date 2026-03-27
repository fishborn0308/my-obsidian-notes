```bash
nmap -vv --reason -Pn -T4 -sV -sC --version-all -A --osscan-guess -oN "/home/kali/Vault/Target/10.129.58.98/result/autorecon/10.129.58.98/scans/_quick_tcp_nmap.txt" -oX "/home/kali/Vault/Target/10.129.58.98/result/autorecon/10.129.58.98/scans/xml/_quick_tcp_nmap.xml" 10.129.58.98

nmap -vv --reason -Pn -T4 -sV -sC --version-all -A --osscan-guess -p- -oN "/home/kali/Vault/Target/10.129.58.98/result/autorecon/10.129.58.98/scans/_full_tcp_nmap.txt" -oX "/home/kali/Vault/Target/10.129.58.98/result/autorecon/10.129.58.98/scans/xml/_full_tcp_nmap.xml" 10.129.58.98

nmap -vv --reason -Pn -T4 -sV -p 23 --script="banner,telnet-encryption,telnet-ntlm-info" -oN "/home/kali/Vault/Target/10.129.58.98/result/autorecon/10.129.58.98/scans/tcp23/tcp_23_telnet-nmap.txt" -oX "/home/kali/Vault/Target/10.129.58.98/result/autorecon/10.129.58.98/scans/tcp23/xml/tcp_23_telnet_nmap.xml" 10.129.58.98


```
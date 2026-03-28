```bash
nmap -vv --reason -Pn -T4 -sV -sC --version-all -A --osscan-guess -oN "/home/kali/Vault/Target/10.129.136.187/result/autorecon/10.129.136.187/scans/_quick_tcp_nmap.txt" -oX "/home/kali/Vault/Target/10.129.136.187/result/autorecon/10.129.136.187/scans/xml/_quick_tcp_nmap.xml" 10.129.136.187
```

[/home/kali/Vault/Target/10.129.136.187/result/autorecon/10.129.136.187/scans/_quick_tcp_nmap.txt](file:///home/kali/Vault/Target/10.129.136.187/result/autorecon/10.129.136.187/scans/_quick_tcp_nmap.txt):

```
# Nmap 7.98 scan initiated Sat Mar 28 08:51:38 2026 as: /usr/lib/nmap/nmap --privileged -vv --reason -Pn -T4 -sV -sC --version-all -A --osscan-guess -oN /home/kali/Vault/Target/10.129.136.187/result/autorecon/10.129.136.187/scans/_quick_tcp_nmap.txt -oX /home/kali/Vault/Target/10.129.136.187/result/autorecon/10.129.136.187/scans/xml/_quick_tcp_nmap.xml 10.129.136.187
Nmap scan report for Redeemer (10.129.136.187)
Host is up, received user-set (0.22s latency).
Scanned at 2026-03-28 08:51:38 EDT for 6s
All 1000 scanned ports on Redeemer (10.129.136.187) are in ignored states.
Not shown: 1000 closed tcp ports (reset)
Too many fingerprints match this host to give specific OS details
TCP/IP fingerprint:
SCAN(V=7.98%E=4%D=3/28%OT=%CT=1%CU=38574%PV=Y%DS=2%DC=T%G=N%TM=69C7CEE0%P=x86_64-pc-linux-gnu)
SEQ(CI=Z)
SEQ(CI=Z%II=I)
T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)
T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)
T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)
U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)
IE(R=Y%DFI=N%T=40%CD=S)

Network Distance: 2 hops

TRACEROUTE (using port 995/tcp)
HOP RTT       ADDRESS
1   175.71 ms 10.10.16.1
2   248.21 ms Redeemer (10.129.136.187)

Read data files from: /usr/share/nmap
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sat Mar 28 08:51:44 2026 -- 1 IP address (1 host up) scanned in 5.60 seconds

```

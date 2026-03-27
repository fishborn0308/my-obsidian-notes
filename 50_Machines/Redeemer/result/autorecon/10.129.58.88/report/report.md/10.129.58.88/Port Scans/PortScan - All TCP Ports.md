```bash
nmap -vv --reason -Pn -T4 -sV -sC --version-all -A --osscan-guess -p- -oN "/home/kali/Vault/Target/10.129.58.88/result/autorecon/10.129.58.88/scans/_full_tcp_nmap.txt" -oX "/home/kali/Vault/Target/10.129.58.88/result/autorecon/10.129.58.88/scans/xml/_full_tcp_nmap.xml" 10.129.58.88
```

[/home/kali/Vault/Target/10.129.58.88/result/autorecon/10.129.58.88/scans/_full_tcp_nmap.txt](file:///home/kali/Vault/Target/10.129.58.88/result/autorecon/10.129.58.88/scans/_full_tcp_nmap.txt):

```
# Nmap 7.98 scan initiated Fri Mar 27 06:18:19 2026 as: /usr/lib/nmap/nmap --privileged -vv --reason -Pn -T4 -sV -sC --version-all -A --osscan-guess -p- -oN /home/kali/Vault/Target/10.129.58.88/result/autorecon/10.129.58.88/scans/_full_tcp_nmap.txt -oX /home/kali/Vault/Target/10.129.58.88/result/autorecon/10.129.58.88/scans/xml/_full_tcp_nmap.xml 10.129.58.88
Increasing send delay for 10.129.58.88 from 0 to 5 due to 1992 out of 4979 dropped probes since last increase.
Increasing send delay for 10.129.58.88 from 5 to 10 due to 11 out of 16 dropped probes since last increase.
Nmap scan report for Redeemer (10.129.58.88)
Host is up, received user-set (0.30s latency).
Scanned at 2026-03-27 06:18:19 EDT for 898s
Not shown: 65534 closed tcp ports (reset)
PORT     STATE SERVICE REASON         VERSION
6379/tcp open  redis   syn-ack ttl 63 Redis key-value store 5.0.7
Device type: general purpose
Running: Linux 4.X|5.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5
OS details: Linux 4.15 - 5.19
TCP/IP fingerprint:
OS:SCAN(V=7.98%E=4%D=3/27%OT=6379%CT=1%CU=37546%PV=Y%DS=2%DC=T%G=Y%TM=69C65
OS:CED%P=x86_64-pc-linux-gnu)SEQ(SP=108%GCD=1%ISR=10A%TI=Z%CI=Z%II=I%TS=A)O
OS:PS(O1=M542ST11NW7%O2=M542ST11NW7%O3=M542NNT11NW7%O4=M542ST11NW7%O5=M542S
OS:T11NW7%O6=M542ST11)WIN(W1=FE88%W2=FE88%W3=FE88%W4=FE88%W5=FE88%W6=FE88)E
OS:CN(R=Y%DF=Y%T=40%W=FAF0%O=M542NNSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F
OS:=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5
OS:(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z
OS:%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=
OS:N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40%
OS:CD=S)

Uptime guess: 3.286 days (since Mon Mar 23 23:41:08 2026)
Network Distance: 2 hops
TCP Sequence Prediction: Difficulty=264 (Good luck!)
IP ID Sequence Generation: All zeros

TRACEROUTE (using port 443/tcp)
HOP RTT       ADDRESS
1   377.43 ms 10.10.16.1
2   166.79 ms Redeemer (10.129.58.88)

Read data files from: /usr/share/nmap
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Fri Mar 27 06:33:17 2026 -- 1 IP address (1 host up) scanned in 898.82 seconds

```

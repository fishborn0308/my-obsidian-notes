```bash
nmap -vv --reason -Pn -T4 -sV -sC --version-all -A --osscan-guess -p- -oN "/home/kali/Vault/Target/10.129.58.98/result/autorecon/10.129.58.98/scans/_full_tcp_nmap.txt" -oX "/home/kali/Vault/Target/10.129.58.98/result/autorecon/10.129.58.98/scans/xml/_full_tcp_nmap.xml" 10.129.58.98
```

[/home/kali/Vault/Target/10.129.58.98/result/autorecon/10.129.58.98/scans/_full_tcp_nmap.txt](file:///home/kali/Vault/Target/10.129.58.98/result/autorecon/10.129.58.98/scans/_full_tcp_nmap.txt):

```
# Nmap 7.98 scan initiated Fri Mar 27 06:37:20 2026 as: /usr/lib/nmap/nmap --privileged -vv --reason -Pn -T4 -sV -sC --version-all -A --osscan-guess -p- -oN /home/kali/Vault/Target/10.129.58.98/result/autorecon/10.129.58.98/scans/_full_tcp_nmap.txt -oX /home/kali/Vault/Target/10.129.58.98/result/autorecon/10.129.58.98/scans/xml/_full_tcp_nmap.xml 10.129.58.98
Nmap scan report for Meow (10.129.58.98)
Host is up, received user-set (0.32s latency).
Scanned at 2026-03-27 06:37:20 EDT for 931s
Not shown: 65534 closed tcp ports (reset)
PORT   STATE SERVICE REASON         VERSION
23/tcp open  telnet  syn-ack ttl 63 Linux telnetd
Device type: general purpose
Running: Linux 4.X|5.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5
OS details: Linux 4.15 - 5.19
TCP/IP fingerprint:
OS:SCAN(V=7.98%E=4%D=3/27%OT=23%CT=1%CU=43544%PV=Y%DS=2%DC=T%G=Y%TM=69C6618
OS:3%P=x86_64-pc-linux-gnu)SEQ(SP=105%GCD=1%ISR=10A%TI=Z%CI=Z%II=I%TS=A)OPS
OS:(O1=M542ST11NW7%O2=M542ST11NW7%O3=M542NNT11NW7%O4=M542ST11NW7%O5=M542ST1
OS:1NW7%O6=M542ST11)WIN(W1=FE88%W2=FE88%W3=FE88%W4=FE88%W5=FE88%W6=FE88)ECN
OS:(R=Y%DF=Y%T=40%W=FAF0%O=M542NNSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=A
OS:S%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(R
OS:=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F
OS:=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N%
OS:T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40%CD
OS:=S)

Uptime guess: 16.122 days (since Wed Mar 11 03:57:05 2026)
Network Distance: 2 hops
TCP Sequence Prediction: Difficulty=261 (Good luck!)
IP ID Sequence Generation: All zeros
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

TRACEROUTE (using port 1723/tcp)
HOP RTT       ADDRESS
1   375.60 ms 10.10.16.1
2   167.83 ms Meow (10.129.58.98)

Read data files from: /usr/share/nmap
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Fri Mar 27 06:52:51 2026 -- 1 IP address (1 host up) scanned in 930.79 seconds

```

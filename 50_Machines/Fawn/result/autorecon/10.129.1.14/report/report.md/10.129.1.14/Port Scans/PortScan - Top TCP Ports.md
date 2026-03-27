```bash
nmap -vv --reason -Pn -T4 -sV -sC --version-all -A --osscan-guess -oN "/home/kali/Vault/Target/10.129.1.14/result/autorecon/10.129.1.14/scans/_quick_tcp_nmap.txt" -oX "/home/kali/Vault/Target/10.129.1.14/result/autorecon/10.129.1.14/scans/xml/_quick_tcp_nmap.xml" 10.129.1.14
```

[/home/kali/Vault/Target/10.129.1.14/result/autorecon/10.129.1.14/scans/_quick_tcp_nmap.txt](file:///home/kali/Vault/Target/10.129.1.14/result/autorecon/10.129.1.14/scans/_quick_tcp_nmap.txt):

```
# Nmap 7.98 scan initiated Fri Mar 27 02:07:58 2026 as: /usr/lib/nmap/nmap --privileged -vv --reason -Pn -T4 -sV -sC --version-all -A --osscan-guess -oN /home/kali/Vault/Target/10.129.1.14/result/autorecon/10.129.1.14/scans/_quick_tcp_nmap.txt -oX /home/kali/Vault/Target/10.129.1.14/result/autorecon/10.129.1.14/scans/xml/_quick_tcp_nmap.xml 10.129.1.14
adjust_timeouts2: packet supposedly had rtt of -602747 microseconds.  Ignoring time.
adjust_timeouts2: packet supposedly had rtt of -602747 microseconds.  Ignoring time.
adjust_timeouts2: packet supposedly had rtt of -513500 microseconds.  Ignoring time.
adjust_timeouts2: packet supposedly had rtt of -513500 microseconds.  Ignoring time.
adjust_timeouts2: packet supposedly had rtt of -589685 microseconds.  Ignoring time.
adjust_timeouts2: packet supposedly had rtt of -589685 microseconds.  Ignoring time.
adjust_timeouts2: packet supposedly had rtt of -614798 microseconds.  Ignoring time.
adjust_timeouts2: packet supposedly had rtt of -614798 microseconds.  Ignoring time.
adjust_timeouts2: packet supposedly had rtt of -363547 microseconds.  Ignoring time.
adjust_timeouts2: packet supposedly had rtt of -363547 microseconds.  Ignoring time.
adjust_timeouts2: packet supposedly had rtt of -314662 microseconds.  Ignoring time.
adjust_timeouts2: packet supposedly had rtt of -314662 microseconds.  Ignoring time.
Nmap scan report for Fawn (10.129.1.14)
Host is up, received user-set (0.19s latency).
Scanned at 2026-03-27 02:07:58 EDT for 11s
Not shown: 999 closed tcp ports (reset)
PORT   STATE SERVICE REASON         VERSION
21/tcp open  ftp     syn-ack ttl 63 vsftpd 3.0.3
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.10.16.40
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 4
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rw-r--r--    1 0        0              32 Jun 04  2021 flag.txt
Device type: general purpose
Running: Linux 4.X|5.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5
OS details: Linux 4.15 - 5.19
TCP/IP fingerprint:
OS:SCAN(V=7.98%E=4%D=3/27%OT=21%CT=1%CU=42284%PV=Y%DS=2%DC=T%G=Y%TM=69C61EC
OS:9%P=x86_64-pc-linux-gnu)SEQ(SP=F8%GCD=1%ISR=101%TI=Z%CI=Z%TS=A)OPS(O1=M5
OS:42ST11NW7%O2=M542ST11NW7%O3=M542NNT11NW7%O4=M542ST11NW7%O5=M542ST11NW7%O
OS:6=M542ST11)WIN(W1=FE88%W2=FE88%W3=FE88%W4=FE88%W5=FE88%W6=FE88)ECN(R=Y%D
OS:F=Y%T=40%W=FAF0%O=M542NNSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=AS%RD=0
OS:%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(R=Y%DF=
OS:Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%
OS:RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N%T=40%I
OS:PL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40%CD=S)

Uptime guess: 7.645 days (since Thu Mar 19 10:38:56 2026)
Network Distance: 2 hops
TCP Sequence Prediction: Difficulty=248 (Good luck!)
IP ID Sequence Generation: All zeros
Service Info: OS: Unix

TRACEROUTE (using port 3389/tcp)
HOP RTT       ADDRESS
1   201.53 ms 10.10.16.1
2   249.70 ms Fawn (10.129.1.14)

Read data files from: /usr/share/nmap
OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Fri Mar 27 02:08:09 2026 -- 1 IP address (1 host up) scanned in 11.18 seconds

```

```bash
nmap -vv --reason -Pn -T4 -sV -p 23 --script="banner,telnet-encryption,telnet-ntlm-info" -oN "/home/kali/Vault/Target/10.129.61.6/result/autorecon/10.129.61.6/scans/tcp23/tcp_23_telnet-nmap.txt" -oX "/home/kali/Vault/Target/10.129.61.6/result/autorecon/10.129.61.6/scans/tcp23/xml/tcp_23_telnet_nmap.xml" 10.129.61.6
```

[/home/kali/Vault/Target/10.129.61.6/result/autorecon/10.129.61.6/scans/tcp23/tcp_23_telnet-nmap.txt](file:///home/kali/Vault/Target/10.129.61.6/result/autorecon/10.129.61.6/scans/tcp23/tcp_23_telnet-nmap.txt):

```
# Nmap 7.98 scan initiated Sat Mar 28 08:05:06 2026 as: /usr/lib/nmap/nmap --privileged -vv --reason -Pn -T4 -sV -p 23 --script=banner,telnet-encryption,telnet-ntlm-info -oN /home/kali/Vault/Target/10.129.61.6/result/autorecon/10.129.61.6/scans/tcp23/tcp_23_telnet-nmap.txt -oX /home/kali/Vault/Target/10.129.61.6/result/autorecon/10.129.61.6/scans/tcp23/xml/tcp_23_telnet_nmap.xml 10.129.61.6
Nmap scan report for Meow (10.129.61.6)
Host is up, received user-set (0.20s latency).
Scanned at 2026-03-28 08:05:06 EDT for 26s

PORT   STATE SERVICE REASON         VERSION
23/tcp open  telnet  syn-ack ttl 63 Linux telnetd
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Read data files from: /usr/share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Sat Mar 28 08:05:32 2026 -- 1 IP address (1 host up) scanned in 26.04 seconds

```

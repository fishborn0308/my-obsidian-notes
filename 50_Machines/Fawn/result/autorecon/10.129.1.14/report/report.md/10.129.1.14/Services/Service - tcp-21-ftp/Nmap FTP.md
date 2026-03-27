```bash
nmap -vv --reason -Pn -T4 -sV -p 21 --script="banner,(ftp* or ssl*) and not (brute or broadcast or dos or external or fuzzer)" -oN "/home/kali/Vault/Target/10.129.1.14/result/autorecon/10.129.1.14/scans/tcp21/tcp_21_ftp_nmap.txt" -oX "/home/kali/Vault/Target/10.129.1.14/result/autorecon/10.129.1.14/scans/tcp21/xml/tcp_21_ftp_nmap.xml" 10.129.1.14
```

[/home/kali/Vault/Target/10.129.1.14/result/autorecon/10.129.1.14/scans/tcp21/tcp_21_ftp_nmap.txt](file:///home/kali/Vault/Target/10.129.1.14/result/autorecon/10.129.1.14/scans/tcp21/tcp_21_ftp_nmap.txt):

```
# Nmap 7.98 scan initiated Fri Mar 27 02:08:09 2026 as: /usr/lib/nmap/nmap --privileged -vv --reason -Pn -T4 -sV -p 21 "--script=banner,(ftp* or ssl*) and not (brute or broadcast or dos or external or fuzzer)" -oN /home/kali/Vault/Target/10.129.1.14/result/autorecon/10.129.1.14/scans/tcp21/tcp_21_ftp_nmap.txt -oX /home/kali/Vault/Target/10.129.1.14/result/autorecon/10.129.1.14/scans/tcp21/xml/tcp_21_ftp_nmap.xml 10.129.1.14
Nmap scan report for Fawn (10.129.1.14)
Host is up, received user-set (0.23s latency).
Scanned at 2026-03-27 02:08:09 EDT for 5s

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
|      At session startup, client count was 3
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rw-r--r--    1 0        0              32 Jun 04  2021 flag.txt
|_banner: 220 (vsFTPd 3.0.3)
Service Info: OS: Unix

Read data files from: /usr/share/nmap
Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
# Nmap done at Fri Mar 27 02:08:14 2026 -- 1 IP address (1 host up) scanned in 5.59 seconds

```

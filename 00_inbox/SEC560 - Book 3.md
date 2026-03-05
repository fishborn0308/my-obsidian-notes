#### **SEC560 | ENTERPRISE PENETRATION TESTING GIAC Penetration Tester (GPEN)**

**560.3**

Post-Exploitation

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

**SEC560.3**

![](_page_2_Picture_1.jpeg)

# **Post-Exploitation**

ENTERPRISE PENETRATION TESTING

Welcome to Section 3 of SEC560: Post-Exploitation.

In this section, we shift focus from gaining access to maintaining and expanding control. We start with the basics of common password hashing algorithms and authentication protocols. You'll learn how to extract and analyze password hashes using tools like Metasploit's various hash dumping features and Mimikatz, then move into password cracking with Hashcat.

Next, we explore the Assume Breach testing methodology and dive into command-and-control

We'll follow C2s with details to build custom payloads for penetration testing. We'll examine situational awareness techniques across both Windows and Linux environments, use Seatbelt to streamline Windows host information gathering, and practice these concepts in a lab.

The section concludes with privilege escalation in both Linux and Windows, reinforced through a dedicated Privilege Escalation lab on your Windows VM.

Let's get going!

![](_page_3_Figure_0.jpeg)

![](_page_4_Figure_0.jpeg)

#### 4

![](_page_5_Picture_2.jpeg)

#### **Course Roadmap**

Section 1: Miniature Engagement, Recon, and Scanning

Section 2: Scanning and Initial Access

Section 3: Post-Exploitation

Section 4: Domain Privilege Escalation and Lateral Movement

Section 5: Persistence and Evading Controls

Section 6: CTF and Next Steps

![](_page_5_Picture_10.jpeg)

- Passwords and Credential Access
- Password Representations
- Obtaining Password Hashes

Lab 3.1: MSF psexec, hashdump, and Mimikatz

• Hashcat

Lab 3.2: Cracking Passwords with Hashcat

- Assumed Breach
- Command and Control (C2)

•

• Payloads

- Lab 3.4: Payloads
- Post-Exploitation
- Situational Awareness
- Linux Situational Awareness
- Windows Situational Awareness
- Seatbelt
  - Lab 3.5: Seatbelt
- Privilege Escalation
- Linux Privilege Escalation
- Windows Privilege Escalation
  - Lab 3.6: Windows Privilege Escalation

# **Password Guessing vs. Password Cracking**

- Password guessing (online)
  - − Guess and attempt to log in to target systems
  - − May generate a lot of network traffic and logs
  - − May lock out accounts, a dangerous DoS possibility
  - − Slower than password cracking
- Password cracking (offline)
  - − Steal hashed passwords and guess a password, hash that guess, and compare it against the stolen hash
  - − Happens on the attacker's own hardware, thus stealthier
  - − Does not lock out accounts
  - − Many orders of magnitude faster than guessing

![](_page_6_Picture_13.jpeg)

The phrases "password guessing" and "password cracking" are often used interchangeably by information security professionals. But these terms do have different meanings and profoundly different implications for testers.

Password guessing involves formulating a guess for a password and then trying to use that guess to actually log in to a target system, either locally or across the network. In contrast, in a password cracking attack, the attacker gets a copy of the encrypted or hashed password representations either from a target machine or by sniffing them off of the network. The attacker then cracks the passwords by formulating a guess, encrypting or hashing the guess, and then comparing the result to the actual password's encrypted or hashed value. If the encrypted guess equals the encrypted password, the attacker now has a usable password.

Given the different methods of these attacks, they have different properties. Password guessing tends to generate a lot of log entries if the system is logging failed login attempts, as well as network traffic that an Intrusion Detection System analyst may see. Password guessing could also lock out accounts if the system is configured to disable accounts after a threshold of bad login attempts. Also, password guessing tends to be many orders of magnitude slower than password cracking. The timing of password guessing depends on the speed of the target system in processing login attempts, as well as the performance of the network. Some target systems even throttle their authentication speeds when a single connection or user tries too many passwords in a short time. The speed of password guessing attacks may range from approximately one guess per minute to one guess per second.

In contrast, password cracking happens on the attacker's own turf: on one or more systems controlled by the attacker. It generates far less information in logs and in network captures (although there may be some records of the theft of the encrypted/hashed passwords, depending on how the attacker snags them). Password cracking does not lock out accounts because the attacker is not using the password guesses to attempt to log in. Also, password cracking attacks can go through huge numbers of guesses quickly. The

| attacker may cycle through tens of thousands to many millions (or more) of guess/encrypt/compare<br>actions every second. |  |  |  |  |  |
|---------------------------------------------------------------------------------------------------------------------------|--|--|--|--|--|
|                                                                                                                           |  |  |  |  |  |
|                                                                                                                           |  |  |  |  |  |
|                                                                                                                           |  |  |  |  |  |
|                                                                                                                           |  |  |  |  |  |
|                                                                                                                           |  |  |  |  |  |
|                                                                                                                           |  |  |  |  |  |
|                                                                                                                           |  |  |  |  |  |
|                                                                                                                           |  |  |  |  |  |
|                                                                                                                           |  |  |  |  |  |
|                                                                                                                           |  |  |  |  |  |
|                                                                                                                           |  |  |  |  |  |
|                                                                                                                           |  |  |  |  |  |
|                                                                                                                           |  |  |  |  |  |
|                                                                                                                           |  |  |  |  |  |
|                                                                                                                           |  |  |  |  |  |
|                                                                                                                           |  |  |  |  |  |
|                                                                                                                           |  |  |  |  |  |
|                                                                                                                           |  |  |  |  |  |
|                                                                                                                           |  |  |  |  |  |
|                                                                                                                           |  |  |  |  |  |
|                                                                                                                           |  |  |  |  |  |
|                                                                                                                           |  |  |  |  |  |
|                                                                                                                           |  |  |  |  |  |
|                                                                                                                           |  |  |  |  |  |
|                                                                                                                           |  |  |  |  |  |
|                                                                                                                           |  |  |  |  |  |
|                                                                                                                           |  |  |  |  |  |
|                                                                                                                           |  |  |  |  |  |
|                                                                                                                           |  |  |  |  |  |

![](_page_8_Picture_1.jpeg)

# **Synced Passwords**

- Users manually synchronize their passwords across authentication boundaries (like AD domains, standalone Linux, password managers, etc.)
- Passwords from one system might be useful in another environment or domain
- Every account you can compromise could be valuable
  - − It might not be important on the machine where you first discover it, but that user may have higher privileges or more interesting access on other machines with accounts using the same userID and password
- Crack passwords even from machines that you've already conquered with UID 0 or SYSTEM privileges

As soon as you can get hashes, grab them. Then crack them! ABC: Always Be Cracking.

We will now discuss several tips for effective password attacks that penetration testers and ethical hackers should keep in mind. First, remember that users manually synchronize their passwords. If user Alice has a password of Apple1234 on one machine, there is a high probability that she set exactly the same password for an account on another machine or in another application. What's more, some organizations synchronize passwords or provide single sign-on functionality that automates the synchronization of passwords among systems. Thus, carefully record all passwords that are successfully cracked during a test and try to use them for accessing other machines in the test's scope.

It's important to realize that every account you can compromise (always operating within scope and following the Rules of Engagement) could be valuable to you in a penetration test. Even if you compromise an unimportant account on an uninteresting system, gaining the userID and password, you may use those same credentials to access other, more interesting machines, perhaps even those with higher privileges, where the given user has synchronized his password between the unimportant account and an account that has far more value.

One somewhat subtle implication of this synchronization of user passwords involves cracking passwords even from systems that the tester has already conquered. Consider this scenario: An attacker exploits one system, gaining UID 0 privileges on the box. With these privileges, the attacker can grab the encrypted passwords from the machine, move them to the attacker's system, and crack them. "But," you might think, "why bother, given that the attacker already has complete control over that box with UID 0?" The answers involve password synchronization. Any passwords cracked from accounts (even accounts without UID 0) on the machine the tester has already conquered could be useful in accessing other systems. In fact, those accounts may have limited privileges on the system the attacker first conquered, but have superuser privileges on other systems. Thus, whenever testers get a file of encrypted/hashed passwords, they should start running a password cracking tool to maximize the time that can be spent analyzing those passwords. Having separate systems dedicated to password cracking is a useful resource for professional penetration testers and ethical hackers.

![](_page_9_Picture_1.jpeg)

#### **Dictionaries**

- Build a comprehensive word list from free dictionaries
  - − Ron Bowes has several lists of leaked password files available for free download at https://www.skullsecurity.org/wiki/Passwords
  - − Crackstation.net offers a large word list
- Cracking Large list
- Guessing Smaller, more focused list
- Create a custom dictionary tuned to your target environment
  - − Crawl their websites and then create a dictionary
  - − Tailor word guesses according to policy (if you have it)
  - − Repeats just waste time; remove duplicates from your dictionary
  - − Remove passwords that don't meet the password policy (length and complexity)

```
$ pw-inspector -m 8 -c 3 -lunps -i wordlist.txt | sort -u > dictionary.txt
```

Additional tips for password attacks are associated with the dictionaries used to formulate guesses. Build a comprehensive dictionary in advance. We'll cover some sites for getting free word lists later, but one of the best sources of actual passwords that have leaked from various sources is Ron Bowes's list at https://www.skullsecurity.org/wiki/Passwords. Get several of these password lists and put them together into a single large file that you keep handy for password guessing and password cracking attacks. Given the different speeds at which the two types of attacks operate, you may want to develop separate word lists for your password cracking attacks versus your password guessing attacks. The password cracking word list might have millions of entries, whereas the password guessing list may have hundreds or thousands of focused attempts.

At the outset of a testing project, you should augment your dictionary with specialized words used by the target organization or business unit, including its product and service names, people's names associated with the organization, terms of art for the given industry, and so on. In effect, you should create a customized dictionary specially crafted for each project. One of the most effective ways to do this is to use software to crawl the target's websites, creating a local mirror of its pages. Then, use a program or script to create a word list of all words in the files you've gathered from the websites. Take this word list and prepend it to your normal word list to create a tailored dictionary. Then, if you can get a copy of its password policy, customize your word list to match its policy (minimum number of characters, special character requirement, and such). We'll cover specific tools and commands for doing this shortly.

Whenever you create or update a dictionary file for password cracking or guessing, make sure it contains only unique words. Any duplicated words only waste valuable time, slowing down your tool or making you do more work. A simple UNIX/Linux command to eliminate duplicate words from a file involves the "uniq" command, which removes redundant words from a sorted file, used as follows:

```
$ cat wordlist.txt | sort | uniq > dictionary.txt
```

![](_page_10_Picture_1.jpeg)

# **Making Good Guesses with a Custom Dictionary**

- With password cracking (offline), we can try many more passwords than with (online) password guessing
  - − No lockout
  - − No network limitations
- Remember complexity requirements (560.2)
- Use the same common passwords as we did for guessing
  - − <Season><Year>
  - − Orgname1-99
  - − Welcome1-99
  - − Password1-99
- "Mangling" of existing words; e.g., Pass -> Pass1, Pa55, P@ss, P@ss1
- Crawl a website for content to use as passwords with Cewl.rb

The size of your dictionary will depend on the type of password attack you are engaged in. As mentioned on the previous page, password cracking attacks are much faster than password guessing attacks. This means that the number of passwords that can be attempted in a given time frame will vary greatly. The size of the dictionary used for password cracking can be huge, while the dictionary size for password guessing needs to be smaller than the lockout threshold.

The number of passwords used in a password guessing attack is very small. Often, you can only safely use a few passwords before you encroach on the lockout mechanism. The passwords used here must be very targeted.

Common bad passwords used for password guessing include CompanyName01 and Password1. Both of these passwords will pass most of the password complexity rules that require an uppercase letter, a lowercase letter, and a digit. Another very good guess is derived from the organization's password rotation policy. Users get tired of coming up with new passwords and use a scheme to remember passwords. If the password rotation policy requires a change every 90 days, then users will many times use a password of <season><year>; if monthly, then <month><year>. These schemes will pass most password complexity requirements and thwart password repeat detection. Also, the previous season/month as well as the next season/month may work, as you won't know when users were last required to change their password.

Mangling rules are used to modify or "mangle" words. For example, the word password could be mangled into Password, password1, Password1, p@ssw0rd, or P@ssw0rd1. The way the word is mangled depends on the rules that are used for mangling. Penetration testers will often use a large dictionary (like Rockyou or Crackstation) combined with output from CeWL; then, that entire word list will be mangled to create a very large dictionary to be used in cracking. The CeWL tool is used to extract all the words from a target website to generate a target-specific set of words for use in your password attacks. When used with "mangling" rules, this can create an extremely effective dictionary.

Robin Wood's Custom Word List generator (CeWL) spiders a target website and builds a list of unique words. This list is better for password cracking than (online) password guessing. CeWL is written by Robin "DigiNinja" Wood and is available at http://www.digininja.org/projects/cewl.php.

![](_page_12_Picture_0.jpeg)

# **Update Your Dictionary**

- Update dictionary file with newly cracked passwords while the test occurs
  - − Be careful with cleanup after the project
- You can use one organization's passwords to help you in other penetration tests

As your password cracking tool runs, it may successfully crack some passwords. Take these results and add them to your dictionary for later password cracking work in this project. You might crack a password from one system and add it to your dictionary. Then, a user may have a subtle variation of that same word on another system. Most password cracking tools can be configured to start with a dictionary word and make alterations to it as guesses for the cracking attack. The process of formulating alterations of dictionary words is sometimes called hybrid guessing.

With the user's successfully cracked password from one machine now in your word list, you have a much better chance of determining the variations to the passwords that the same user applied on other systems. Although this technique is immensely helpful, it does have some post-project implications. Your dictionary file now includes successfully cracked passwords for the target organization. That's sensitive information and should be disposed of at the end of the project. If you choose to keep the dictionary, be aware that it is very sensitive. As you compile lists of cracked passwords of various targets, this will become a valuable repository of working passwords. Before you do so, make sure you are allowed to per your rules of engagement.

![](_page_13_Figure_0.jpeg)

A tip for improving speed is to use commercial cloud resources to crack passwords. Amazon and other cloud vendors allow users to quickly and easily spin up systems for a variety of tasks, including password cracking.

Amazon's EC2 service provides 1 compute unit (approximately the performance of a 1 GHz CPU) for approximately US\$0.10 per hour. (That's the price for a Linux instance; Windows machines cost slightly more due to the commercial software license of the operating system.) Or you could order a CPUintensive instance of Linux with approximately 5 compute units for 20 cents an hour. Amazon also offers GPU instances with the equivalent of 33 compute units featuring two NVIDIA GPUs for US\$2.00 per hour. Other cloud providers have different offerings worth considering.

NPK is "a distributed hash-cracking platform built entirely of serverless components in AWS including Cognito, DynamoDB, and S3". It is relatively easy to set up, and you can granularly control the costs of cracking. Per the documentation, "you can crank out as much as 1.2TH/s [1.2 billion hashes per second] of NTLM for a mere \$14.70/hr."

NPK is available at https://github.com/c6fc/npk.

![](_page_14_Picture_0.jpeg)

![](_page_14_Picture_1.jpeg)

# **Passwords without Cracking**

You can get passwords outside of guessing and cracking:

- Sniffing cleartext protocols such as Telnet, FTP, HTTP, SMTP
- Keystroke logging—most C2s offer this feature
- Discovering plaintext credentials inside file shares
- Pass-the-Hash works for Windows/Domain and some applications
  - − Attempt to crack all hashes, but feel free to pass-the-hash while you wait (and regardless of whether you're successful)

In addition, although we focus on password guessing and cracking to determine passwords, remember that there are other forms of password attack. An attacker could sniff passwords off the network if they are passed via cleartext protocols such as Telnet, FTP, and HTTP. Alternatively, passwords could be gathered through keystroke logging software or hardware installed on a machine the attacker has conquered. When users log in at the console, the keystroke software will gather their password, either writing it to a file or sending it to the attacker. But be careful before deploying keystroke loggers. They could be dangerous and violate the law. Make absolutely sure that their use is within the project's Rules of Engagement, doublechecking with the people who scoped the project before installing the keystroke logger. Also, when the project is complete, make sure you remove the keystroke logging software. Otherwise, it will continue gathering sensitive information that could be retrieved by a criminal attacker at some point in the future.

Some versions of the Metasploit Meterpreter include keystroke logging functionality, invoked through the keyscan\_start, keyscan\_stop, and keyscan\_dump commands inside of a Meterpreter prompt.

And finally, sometimes you don't need to actually crack a password but can instead use its hash to access a target system. Later in class, we'll cover these "pass-the-hash" techniques, which are effective for some operating systems and applications. In particular, Windows systems, with their LANMAN and NT hashes, are ideal targets for pass-the-hash attacks.

14

![](_page_15_Picture_1.jpeg)

# **Be Careful**

- Do not crack passwords on the target system
- Be careful not to leave copies of password files behind
  - − /etc/passwd and /etc/shadow from Linux and UNIX
  - − SAM backup files from Windows
  - − ntds.dit files from Active Directory
- Grab a copy of the password file
  - − Be careful with opening the original password file(s) in an editor on the target machine and then saving them
  - − Be careful not to "move" the file
- Securely transfer hashes (when possible)
  - − Linux/UNIX: Secure Shell
  - − Windows: Dumping hashes across an encrypted C2 session

When cracking passwords, you have to remember to tread lightly and have as little impact on the target machines as possible. Do not crack passwords on the target machines because your password cracking tools could significantly slow down the target machines if you run them there. Instead, move a copy of the password representations to a tester's machine and crack them on your own systems.

When conducting password attacks, testers must be careful to maintain control over password files and cracked password results at all times. Do not leave a copy of password hashes on production machines. The only hashes in the environment should be those used for actual authentication, as well as a single copy made for the password cracking tool (or multiple copies if multiple machines are used for password cracking). Leave no extraneous copies around because criminal attackers often look for these extra password files.

Password-related files that must be carefully guarded include any copies of the /etc/passwd and /etc/shadow files taken from Linux or UNIX machines. We also need to guard our copies of SAM database dumps, including the one stored by default on some Windows machines in c:\windows\repair. Remove any copies you make of the ntds.dit file, which stores Active Directory password representations in a Windows environment.

Another gotcha to avoid in password attacks involves leaking password information to other organizations. Some articles have been published about the effectiveness of using Google searches for a password hash to determine the original password. Several organizations have websites with comprehensive LANMAN, MD5, and other password hash lists or even files that happen to have just one password hash in them. By doing a Google search of just the hash, you may find a web page that tells you the original password.

When you get a copy of the original password file, be careful not to alter the file in any way. Also, remember that the encrypted or hashed passwords are sensitive information, even though the passwords aren't cleartext. You should strive to move them across the network in an encrypted fashion if possible. On Linux or UNIX, you can move passwords around with Secure Shell (assuming you have shell/terminal access to the target). On Windows, you can dump hashes using the Meterpreter, and they'll be moved across the encrypted Meterpreter session using TLS.

![](_page_17_Picture_0.jpeg)

![](_page_17_Picture_1.jpeg)

# **At the Completion of the Test**

- Report on the types of passwords you found
  - − Lots of eight-character passwords?
  - − Is SeasonYear common? Local sports team?
  - − What percentage cracked?
  - − How many sensitive account passwords were cracked?
- Have users change all cracked passwords
- Carefully clean up the passwords and hashes

#### **Don't forget to redact passwords and hashes in the report!**

Here are some final tips for password cracking attacks particularly associated with the completion of a project.

When passwords are successfully cracked, record the type of passwords. Were there a lot of eightcharacter passwords? Was the SeasonYear password used? Is there another common theme in the passwords, such as the name of a local sports team? What percentage of the passwords were cracked? Were any sensitive accounts cracked, such as domain administrators or service accounts?

When the project is complete, make sure you advise the target organization to have users change all passwords that were successfully cracked. Otherwise, the testing team would know the value of current user passwords, impacting the nonrepudiation properties of the authentication mechanisms in the organization. A system administrator or security team member should configure these accounts to require the users to change them at the next login.

In addition, when the project is complete, make sure you securely delete all password files and cracked results that you've gathered. This is sensitive information and must be disposed of thoroughly.

When reporting on passwords, don't forget to properly redact passwords and password hashes. The common exception to this rule is when the password is default or blank, or matches the username.

![](_page_18_Picture_2.jpeg)

#### **Course Roadmap**

Section 1: Miniature Engagement, Recon, and Scanning

Section 2: Scanning and Initial Access

Section 3: Post-Exploitation

Section 4: Domain Privilege Escalation and Lateral Movement

Section 5: Persistence and Evading Controls

Section 6: CTF and Next Steps

![](_page_18_Picture_10.jpeg)

- Passwords and Credential Access
- Password Representations
- Obtaining Password Hashes Lab 3.1: MSF psexec, hashdump, and Mimikatz
- Hashcat
  - Lab 3.2: Cracking Passwords with Hashcat
- Assumed Breach
- Command and Control (C2)

•

• Payloads

Lab 3.4: Payloads

- Post-Exploitation
- Situational Awareness
- Linux Situational Awareness
- Windows Situational Awareness
- Seatbelt

Lab 3.5: Seatbelt

- Privilege Escalation
- Linux Privilege Escalation
- Windows Privilege Escalation
  - Lab 3.6: Windows Privilege Escalation

![](_page_19_Picture_2.jpeg)

# **Windows Password Representations in the SAM**

- In the SAM database, Windows can store passwords in two forms:
  - − LANMAN
  - − NT Hash
- By default, both are stored in the SAM database in Windows NT, 2000, XP, and 2003
  - − LANMAN hashes are not stored in SAM by default for later versions of Windows (although that can be altered)
  - − Given current password cracking speeds, consider LANMAN hashes effectively plaintext (a single 4090 can crack them all in <1 minute)

On a Windows machine, local accounts are stored in the Security Account Manager (SAM) database. By default, older versions of Windows store two representations of each user's password: the LANMAN hash and the NT hash.

The LANMAN hash is extremely weak, for reasons we shall soon see, whereas the NT hash is stronger but not great. Windows NT, 2000, XP, and 2003 store both hashes by default in the SAM database, whereas Windows Vista, Windows 7, Windows 2008, Windows 2012, and Windows 8/8.1 store only the NT hash in the SAM database. The Windows Vista, 7, 2008, 2012, 8/8.1/10/11, and Windows Server 2016/2019/2022 configuration can be altered, however, to make it store the LANMAN hash for backward compatibility with older infrastructures, but such a configuration is not common.

![](_page_20_Picture_0.jpeg)

19

![](_page_20_Picture_1.jpeg)

# **Windows Password Representations in AD**

- With Active Directory, domain controllers store account information, including LANMAN and NT hashes, in C:\Windows\ntds\ntds.dit
- This file can get quite large, as it stores account information for the whole domain in complex schemas
- With admin privileges and physical access, a user can boot to a special domain admin recovery mode to get this file
  - − We'll discuss other methods for retrieving it shortly
- Extract password hashes with secretsdump.py from Impacket
  - − https://github.com/SecureAuthCorp/impacket/blob/master/examples/secretsdump.py

The SAM database stores local Windows password representations. In Active Directory, password representations are stored in the file \$env:SYSTEMROOT\ntds\ntds.dit (normally, that'd be C:\Windows\ntds\ntds.dit). This file is stored on all domain controllers and can grow quite large (50 or more megabytes, even if there are only a few accounts on the machine). Even though it has a lot of empty space, the ntds.dit file contains various databases with relatively complex schemas for Active Directory.

The ntds.dit file is locked on a running domain controller machine, so it can't be directly accessed using traditional file-reading mechanisms. However, with admin privileges and physical access to a domain controller machine, a user can boot to a special domain admin recovery mode and get a copy of this file. We'll discuss other methods for gaining access to a copy of this file shortly.

With the ntds.dit file, a penetration tester can use secretsdump.py from the Impacket project located at https://github.com/SecureAuthCorp/impacket/blob/master/examples/secretsdump.py.

![](_page_21_Figure_0.jpeg)

Let's analyze the two methods used to formulate password representations for storage in the SAM database: LANMAN and NT hashes. The LANMAN hash algorithm is notoriously weak, designed more than two decades ago. It takes various cryptographic shortcuts that make it particularly easy to crack. It should be briefly noted that the so-called LANMAN hash is not a hash at all but a cryptographic one-way function (sometimes abbreviated OWF) that relies on the DES algorithm. Despite the more appropriate term LANMAN OWF, we use the common terminology nearly everyone applies in the information security industry of LANMAN hashes.

If an account has a password that is 14 characters or less (that is, if the password is less than 15 characters), it gets a LANMAN hash on Windows NT, 2000, XP, and 2003 systems, unless they have been configured not to store LANMAN hashes. If the password is greater than 14 characters, Windows simply stores encrypted padding for that user's LANMAN hash, making the LANMAN hash for that account useless to an attacker.

To formulate the LANMAN hash of a password, Windows takes the less-than-15-character password and pads it to make it exactly 14 characters long. Fixed padding is used. All alphabetic characters are converted to uppercase, weakening the password representation because the attacker doesn't need to guess the case properly to crack the password. The 14-character result is then broken into two 7-character pieces, a colossal problem because an attacker can crack the two halves independently of each other. Instead of trying to break one 14-character password, the attacker needs to break only two 7-character passwords, a much easier feat. Then, each of those 7-character pieces is used as a DES key to encrypt a constant of KGS!@#\$%. The results are concatenated together and stored in the SAM database.

![](_page_22_Figure_0.jpeg)

The NT algorithm is simultaneously simpler and far stronger than LANMAN. The user's password is hashed using a straight MD4 hash algorithm. Passwords of up to 127 characters are supported on modern Windows machines. The NT hash algorithm preserves the alphabetic case of a password and doesn't do any of the splitting actions of LANMAN.

It is worthwhile to note that neither the LANMAN nor the NT hash creation process uses a salt. A salt is a bit of randomness folded into a crypto algorithm to make attacks against it more difficult. Because an attacker does not know what a given account's salt will be in advance, they will have to work harder to crack a password when the password hashes are stolen. With salted password hashing algorithms, such as those used on Linux and UNIX, the guess-encrypt-compare cycle of password cracking is more difficult to do in advance to create a precomputed dictionary because the attacker would not know which salt to fold into the encryption step in advance.

The authoritative claim on maximum length is from Steve Syfuhs, who works for Microsoft and whose responsibilities include NTLMv1/NTLMv2.

#### Reference:

• https://x.com/SteveSyfuhs/status/1187546331939667968

![](_page_23_Picture_2.jpeg)

# **Windows Challenge/Response on the Network**

- From a network perspective, Windows supports multiple forms of cryptographic authentication
  - − LANMAN Challenge/Response
  - − NTLMv1
  - − NTLMv2
  - − Microsoft Kerberos
- Each is generated from stored LANMAN and/or NT hash in SAM or AD

Although the LANMAN and NT hashes are stored in the SAM database and in Active Directory ntds.dit file, Windows uses a variety of challenge-response authentication protocols for authentication across the network derived from these hashes, including LANMAN Challenge/Response, NTLMv1, NTLMv2, and Microsoft Kerberos. Do not get confused on this point. LANMAN and LANMAN Challenge/Response are not identical. The former is the method used to store passwords in the SAM database on the end system. The latter is a network authentication protocol that clients use to authenticate to a domain or an individual server. LANMAN Challenge/Response is derived using the LANMAN hash, but it is a different thing.

Likewise, NT hashes are not the same as NTLMv1 and NTLMv2. The suffixes of v1 and v2 indicate different protocols used for network authentication. The NT hash algorithm creates the hash for the SAM database, whereas NTLMv1 and NTLMv2 are across-the-network authentication protocols that rely on the NT hash. Microsoft also supports its own interpretation of the Kerberos authentication scheme as well.

Now, let's explore these network authentication schemes used by Windows in more detail.

![](_page_24_Picture_0.jpeg)

# **LANMAN Challenge/Response**

- Client initiates authentication
- Server sends a challenge
- Client formulates a response from the challenge by:
  - − Padding LANMAN hash to 21 bytes
  - − Splitting LANMAN hash into three 7-byte pieces
  - − Using each piece as a DES key to encrypt the challenge
- NTLMv1 does the same thing, except it uses NT hash as a starting point for this operation

![](_page_24_Picture_9.jpeg)

SEC560 | Enterprise Penetration Testing

For LANMAN Challenge/Response authentication, a client indicates to a server (such as a domain controller or an individual file server) that it wants to authenticate. The server responds with a randomly generated challenge. The client formulates its response by using the account's LANMAN hash to transform mathematically the challenge into a response, which is sent back across the network to the server. This mathematical transformation relies on padding, splitting, and encrypting using DES in a sequence we'll cover in more detail shortly.

It's important to note that both the LANMAN Challenge/Response and the NTLMv1 response use exactly the same padding, splitting, and encrypting steps. The only difference is that the LANMAN Challenge/Response starts with the LANMAN hash, whereas the NTLMv1 challenge/response starts with the NT hash.

![](_page_25_Figure_0.jpeg)

In the LANMAN Challenge/Response protocol, the server sends a challenge to the client. The client starts with the LANMAN hash, which is 16 bytes long. Note that we are talking about the LANMAN hash here, which is 16 bytes long, not the original LANMAN password, which is up to 14 characters long. The 16 bytes of the LANMAN hash are padded with fixed padding to make them exactly 21 bytes long. They are then broken into three seven-character pieces, which we can call LM part 1, LM part 2, and LM part 3. The third part consists only of the last 2 bytes of a user's password hash with some padding. Each of these three 7-byte pieces is used as a DES key to encrypt the challenge, resulting in Response parts 1, 2, and 3. The Response parts are concatenated together and sent to the server.

The exact same process applies for NTLMv1, but it starts with the 16-byte NT hash instead of the 16-byte LANMAN hash.

With either LANMAN Challenge/Response or NTLMv1, an attacker could sniff both the challenge and the response off of the network and try to crack them—guessing, encrypting, and comparing which passwords would yield the sniffed response from the sniffed challenge. Note that cracking these challenges is more work than cracking LANMAN hashes stored in the SAM database because more cryptographic operations are required with the DES algorithm applied to three different piece parts of either the LANMAN or NT hashes.

![](_page_26_Picture_0.jpeg)

- from server challenge by: − Creating the HMAC-MD5 of username and domain name with NT hash as the key
- − The result is called the NTLMv2 One-Way Function (OWF)
- − Then the response is created from the HMAC-MD5 of the server challenge, timestamp, client challenge, and other items, using the NTLMv2 OWF as the key

NTLMv2 was devised as a response to password cracking tools released in the late 1990s, including L0phtCrack. It is a more sophisticated form of authentication than NTLMv1 and is more difficult to crack. The protocol includes a server challenge and a client challenge, introducing more potential randomness into the system. Furthermore, two stages of hashing are applied, with the results of one hash algorithm feeding into another, making the crypto operations more complex.

Cracking sniffed NTLMv2 challenge/response exchanges is still possible, but it tends to be considerably slower than cracking LANMAN and NT hashes from the SAM or LANMAN Challenge/Response and NTLMv1 sniffed from the network, often an order of magnitude slower. Still, for a patient tester in an all-NTLMv2 environment, these hashes can be cracked.

![](_page_27_Figure_0.jpeg)

The NTLMv2 challenge/response algorithm is based on a user's NT hash stored in the SAM database or cached for that user's account. The client sends an authentication request to the server, including the username, which responds with a challenge. The software on the client then starts with the user's NT hash, which it uses as a key in the keyed hash algorithm HMAC-MD5 to hash the user account name and the domain name. This result (the username/domain name hashed with the key of the user's NT hash) is then used as a key in another round of HMAC-MD5 hashing, this time with the hash applied to the server challenge and a Client Challenge Blob (Binary Large OBject). The blob is comprised of a number of elements, including the domain to authenticate against, the timestamp, and a cryptographically unique nonce. This result is called the NTLMv2 response, which is sent to the server along with the client challenge. The server applies the same operations to determine if the client has demonstrated that it is in possession of the account's NT hash.

Note that cracking NTLMv2 requires sniffing the server challenge and the response, which includes the client challenge. Furthermore, when cracking, the algorithm must fold the username and domain name into the algorithm, along with a timestamp of when the exchange occurred. Clearly, there is a lot more cryptographic work in cracking NTLMv2 challenge responses than the earlier authentication mechanisms from Microsoft.

![](_page_28_Picture_2.jpeg)

# **CAC and Smartcards**

- If the account is enabled for smartcard use and still has a valid password, the NT hash in LSASS process memory is the hash of the user's password
- If the account has been configured with the attribute Smart Card *required* for interactive logon, then the NT hash is a random value calculated when that attribute was enabled for the account
  - − This password hash is provided to the client during the logon by the DC
  - − The hash is generated once when the attribute is set and does not change

If the account is enabled for smartcard use and still has a valid password, the NT hash in LSASS process memory is the hash of the user's password. If the account has been configured with the attribute Smart Card required for interactive logon, then the NT hash is a random value calculated when that attribute was enabled for the account.

This password hash is provided to the client computer during the smartcard logons process by the domain controller. This password hash that is automatically generated when the attribute is set does not change.

#### References:

- https://learn.microsoft.com/en-us/windows/security/identity-protection/smart-cards/smart-cardarchitecture
- https://download.microsoft.com/download/7/7/a/77abc5bd-8320-41af-863c-6ecfb10cb4b9/mitigating%20pass-thehash%20(pth)%20attacks%20and%20other%20credential%20theft%20techniques\_english.pdf

# **Linux and UNIX Password Representations**

- Most rely on underlying crypt(3) function of operating system
  - − Input: User's password and a pseudorandom salt
  - − Output: Text string, suitable for storage in /etc/shadow
- The crypt routine used to formulate passwords varies on different variants of UNIX and Linux
  - − **Traditional DES-based schemes**: Some systems still use
  - − **md5crypt**: Still common, hashed password starts with **\$1\$**
  - − **BSDi Extended DES**: Hashed password starts with **\_**
  - − **Blowfish-based**: Hashed passwords start with **\$2\$** or **\$2a\$**
  - − **sha256crypt**: Used by some Linux distros, starts with **\$5\$**
  - − **sha512crypt**: Used by other Linux distros, starts with **\$6\$**
  - − **yescrypt**: Used by latest Linux distros, starts with **\$y\$**

Let's analyze a couple more popular Linux and UNIX password formats. Most Linux and UNIX systems create their password representations using the crypt(3) routine, called crypt(3), to differentiate it from crypt(1), based on the section of the man pages in which each resides. The crypt(1) notation refers to the crypt command included in Section 1 of the man pages, which describes general commands. This crypt(1) command is not used for passwords and is not included on most modern Linux systems. It can encrypt data but is generally avoided.

The crypt(3) library, described in Section 3 of the man pages, which covers C library functions, is used on most Linux and UNIX variants to create password representations and is stored in either /etc/passwd or /etc/shadow. The crypt function takes as its input a user's password and a random salt. The length of the salt differs for different versions of Linux and UNIX. The crypt function outputs a text string using standard ASCII characters.

The particular algorithm used by crypt(3) varies based on Linux and UNIX system type. Historically, many UNIX flavors relied on a traditional DES-based password scheme. Some systems still use this method today, and we'll cover that shortly. A common alternative to the traditional DES scheme relies on the MD5 algorithm. In /etc/passwd or /etc/shadow, password representations using MD5 are prefaced by \$1\$, followed by the salt, followed by the hashed (in multiple rounds) salt/password combination. We'll analyze that process in more detail as well. Other routines used by some Linux and UNIX variants include BSDi's extended DES (in which the password hashes in /etc/passwd or /etc/shadow start with \_ (an underscore character)) and a Blowfish-based routine created by Niels Provos and David Mazieres, whose password hashes begin with a \$2\$ or \$2a\$. A \$5\$ prefix indicates password representations created using the SHA-256 algorithm, while \$6\$ indicates SHA-512. Likely, \$y\$ indicates yescrypt, which is in use as of the latest Linux distros (for example, Ubuntu 24.04 and Fedora 35+).

![](_page_30_Figure_0.jpeg)

The process starts with a user's password, which can be any length, and a salt. The length of the salt depends on the exact hashing algorithm. The password and salt are hashed together using the MD5 algorithm (for md5crypt), creating an interim result. This result is then hashed again along with the original password and salt, creating a new interim result, which is again hashed with the original password and salt. This process is applied iteratively a thousand times (and more on some Linux systems that implement a variable number of iterations). In a given round, the interim result, password, and salt are interleaved in different orders for each round. The resulting password representation is stored in the password field of /etc/passwd or /etc/shadow, preceded by a \$1\$, followed by the salt, followed by a \$ and the password representation, as in:

**Result**

#### \$1\$ramiBOEO\$FADztBw4avQt/Z/BUwObU1

strategy but with a different algorithm and

5,000 rounds by default

sha256crypt and sha512crypt hashing algorithms are supported in some Linux distributions and use a similar strategy of mixing in the salt over multiple rounds but use a different hash algorithm (SHA-256 or SHA-512, of course) and a different number of rounds. The default number of rounds for SHA-256 and SHA-512 for Linux passwords is 5,000.

![](_page_31_Picture_2.jpeg)

#### **Course Roadmap**

Section 1: Miniature Engagement, Recon, and Scanning

Section 2: Scanning and Initial Access

Section 3: Post-Exploitation

Section 4: Domain Privilege Escalation and Lateral Movement

Section 5: Persistence and Evading Controls

Section 6: CTF and Next Steps

- Passwords and Credential Access
- Password Representations
- Obtaining Password Hashes
- Lab 3.1: MSF psexec, hashdump, and Mimikatz • Hashcat
- Lab 3.2: Cracking Passwords with Hashcat
- Assumed Breach
- Command and Control (C2)

•

• Payloads

Lab 3.4: Payloads

- Post-Exploitation
- Situational Awareness
- Linux Situational Awareness
- Windows Situational Awareness
- Seatbelt

Lab 3.5: Seatbelt

- Privilege Escalation
- Linux Privilege Escalation
- Windows Privilege Escalation
  - Lab 3.6: Windows Privilege Escalation

# **Obtaining Linux/UNIX Password Representations**

- Grab a copy of /etc/shadow
  - − Contains password representations, security settings, and more
  - − Readable only by accounts with UID 0 or group shadow
- Grab a copy of /etc/passwd
  - − Contains login names, UID numbers, and possibly descrypt hashes
  - − Readable by any account on system
- If the Linux system is AD-integrated, then hashes won't be stored locally (though we may be able to steal Kerberos tickets)

Upon gaining access to a Linux or UNIX system, the attacker can grab its password representations from the file system. On some older UNIX or Linux systems, the password representations are stored in the /etc/passwd file, which is world-readable. Thus, if the password representations are stored in /etc/passwd, the attacker can get a copy of /etc/passwd with any privileges whatsoever, move it to the attacker's machine, and start cracking it.

Because of the inherent weaknesses of storing passwords in world-readable /etc/passwd, most modern Linux and UNIX machines move the password hashes to another file, called a shadow password file. On many (but not all) Linux and UNIX machines, this file is /etc/shadow. It is readable only by accounts with UID 0. Machines with a shadowed password file still have /etc/passwd, but ironically, that file doesn't store passwords anymore. Instead, user account information is stored in /etc/passwd (including UID and GID numbers), but the password representations themselves have been moved into /etc/shadow.

A penetration tester or ethical hacker who gains limited account access to a Linux or UNIX machine can read /etc/passwd to determine if password representations are stored in it simply by looking at the second field of each line of this colon-delimited file. If there is an "x" or an "\*" or a "!!" in this entry, the password for the given account is not present, possibly because it is not set or is located in /etc/shadow. If the passwords are located in /etc/shadow, the attacker then must access the target with UID 0 privileges to get a copy of /etc/shadow for cracking.

![](_page_33_Picture_2.jpeg)

# **Obtaining Windows Password Representations**

Testers have many options for getting hashes from a target Windows environment.

- Use a C2 framework's hashdumping capability
  - − Advantage: No Windows file and print sharing protocol access needed
- Use Mimikatz to dump them from memory (possibly in cleartext!)
  - − Either with mimikatz.exe or with C2 features
- On a Domain Controller, use Volume Shadow Copy Service (VSS) to retrieve ntds.dit
  - − Advantage: Much safer than extracting hashes from memory
- Sniff challenge/response from the network
  - − LANMAN Challenge/Response, NTLMv1, NTLMv2, MS Kerberos

Penetration testers and ethical hackers have numerous options for extracting password hashes from a target Windows environment, implemented in a menagerie of tools.

To extract hashes from a Windows machine, an attacker could use the hashdump feature included in the priv module of the Metasploit Meterpreter. A big benefit of using this method is that it rides over the attacker-to-Meterpreter communications channel (possibly using an arbitrary port chosen by the attacker) and thus does not rely on the ports and protocols associated with Windows file and print sharing (the NetBIOS over TCP ports; TCP 135-139; and the Server Message Block port, TCP/UDP 445). These ports may be blocked, so having an alternative method for extracting hashes that doesn't rely on access through these ports is quite helpful.

The Mimikatz tool pulls hashes and possibly even cleartext passwords from memory, combing through a Windows machine's LSASS process in various areas, looking for stored authentication credentials. Mimikatz is a standalone EXE, and it has also been integrated into most C2 frameworks.

Or on a target domain controller, a penetration tester could use the Volume Shadow Copy Service (abbreviated by Microsoft as "VSS") to create a copy of the ntds.dit file, which could then be parsed using the suite of parsing and analysis tools from Impacket.

Finally, a tester could also sniff Microsoft challenge/response authentication from the network as a user mounts a file share or authenticates to a domain. Such authentication could occur via a variety of Microsoft protocols, including LANMAN Challenge/Response, NTLMv1, NTLMv2, and Microsoft's implementation of Kerberos.

# **Dumping Hashes with Meterpreter**

• Requires the Meterpreter to run with SYSTEM privileges

(NTDS.DIT) and standalone

meterpreter > **run post/windows/gather/smart\_hashdump** Pulls from disk, both DC

(SAM and Syskey)

meterpreter > **run post/windows/gather/hashdump** Pulls from Registry

As discussed earlier, the Metasploit Meterpreter has Windows password hash dump features. The Meterpreter can dump hashes by using multiple methods using the following commands:

meterpreter > run post/windows/gather/smart\_hashdump

Pulls from disk. This will retrieve the SAM/SYSKEY from a standalone system or NTDS.dit from a domain controller

meterpreter > run post/windows/gather/hashdump

Same as "run hashdump"

Note: Some of these commands will fail on different systems. If one fails, try others to see if they work!

Regardless of whether hashes are dumped using the hashdump command or the hashdump script, let's analyze their characteristics in more detail now to see their advantages for penetration testers.

To extract hashes, the Meterpreter must be running inside of a process with administrator or local SYSTEM privileges. The priv module SAM extraction does not require copying anything to the target machine's file system.

Instead, the priv hashdump extractor runs from within the memory of the exploited process. This brings some significant advantages for attackers. First, it doesn't require the attacker to have NetBIOS or SMB protocol access to the target machine. Instead, the attacker extracts the hashes using the communications session established between the attacker and the Meterpreter running inside the memory of a target machine process. Furthermore, the priv module doesn't copy anything to the target machine's file system. It is entirely memory resident, running as a DLL inside of the victim process. Thus, it provides less evidence for forensic investigators to recover.

![](_page_35_Picture_2.jpeg)

# **Dumping Hashes from Domain Controllers**

- Windows has exclusive file locks as a feature
  - − Linux and macOS only have advisory locks, which don't block reads/writes
- When AD is running, ntds.dit is locked, so we can't read it
- How can we get hashes from a DC?
  - − Ignore filesystem layer and parse bytes in the disk ( Invoke-NinjaCopy)
  - − Make a backup copy of critical files ( ntdsutil)
  - − Make a filesystem snapshot (Volume Shadow Copy, vssown.vbs)
  - − Replicate them over the network, as other DCs do (DCSync / MS-DRSR)

Using Volume Shadow Copies (VSS) for hash extraction from a domain controller involves creating backups of system files, even if write-protected. This method, described by Tim Tomes and Mark Baggett, employs the VSSOwn tool, enabling penetration testers to manage VSS via the command line. After gaining shell access to the target system, the tester runs vssown.vbs, creates a backup, and extracts crucial files (ntds.dit along with the SYSTEM and SAM registry hives) using the copy command. These steps allow the use of secretsdump.py from the Impacket toolkit to extract hashes from the local files.

![](_page_36_Picture_2.jpeg)

#### **NTDSUtil**

- We can use the "Install From Media" (IFM) feature
  - − Used to make setting up a new domain controller easier over slow links
  - − Extract domain backup, fly to new site, restore, sync new changes
- IFM can be used to extract hashes for cracking, too!

```
C:\> ntdsutil
activate instance ntds
ifm
create full C:\mybackup
quit
quit
```

• One liner:

```
C:\> ntdsutil "ac in ntds" "ifm" "cr fu c:\mybackup" q q
```

We can also extract password hashes using "ntdsutil.exe" and its "Install From Media" feature. This feature was more relevant when it was common to have slow site-to-site links. With a slow link and a large domain, it can take a tremendous amount of time and bandwidth to set up a new domain controller. To make it easier, Microsoft allows you to back up the NTDS database. Someone could then fly, drive, or skateboard to the new location, set up a new server, and import the database. When the new server is brought online, it only needs to sync the changes, thereby saving bandwidth (and time). The commands to backup the database are:

```
C:\Users\Administrator> ntdsutil
ntdsutil: activate instance ntds
Active instance set to "ntds".
ntdsutil: ifm
ifm: create full C:\mybackup
Creating snapshot...
...snipped for brevity...
Copying registry files...
Copying C:\ntdsutil\registry\SYSTEM
Copying C:\ntdsutil\registry\SECURITY
Snapshot {bc8f90f5-1f08-4085-9763-716e9082907b} unmounted.
IFM media created successfully in C:\ntdsutil
ifm: quit
ntdsutil: quit
```

As an attacker, we can use this to grab the domain database and the hashes inside it! We need the files:

- backup\_directory/Active Directory/ntds.dit
- backup\_directory/registry/SECURITY
- backup\_directory/registry/SYSTEM

What's even better is that this series of commands can be reduced to a one-liner:

C:\> ntdsutil "ac in ntds" "ifm" "cr fu c:\mybackup" q q

# **Dumping Creds from Memory with Mimikatz**

- Authored by Benjamin Delpy
- Gathers passwords and hashes of currently logged-on users from LSASS.EXE memory
  - − This is a consequence of Single Sign-On (SSO); credentials *must* be in memory for SSO to work
- Created as a separate executable (**mimikatz.exe**) to load on a target machine and run
- Implemented in many C2 frameworks

Another method for harvesting hashes from a target Windows machine is to use a tool called Mimikatz, created by Benjamin Deply, a programmer from France. Mimikatz pulls authentication information from target Windows machines by scouring the memory of the LSASS process, searching for hashes and even cleartext passwords.

Originally released as a separate executable (mimikatz.exe) with an associated DLL, the tool has been adapted into the Metasploit framework as a Meterpreter module, extending the functionality of the Meterpreter. The latest version of Mimikatz is called Kiwi, and we'll use it in our next lab.

Let's look at an example of Mimikatz in action in a screenshot. (We'll do a hands-on lab on it shortly as well.) On this slide, we can see a Meterpreter prompt from a compromised target Windows system. The pen tester then loads the Mimikatz Kiwi module into the Meterpreter by running load kiwi. The Meterpreter's capabilities are extended, including a new command called creds\_all. This command pulls cleartext passwords from the LSASS process, where they are stored for various Windows features. After running creds\_all, we can see the cleartext password on the screen of sansnight.

![](_page_39_Picture_1.jpeg)

# **MSF psexec, hashdump, and Mimikatz**

Refer to the lab workbook for instructions.

![](_page_40_Picture_2.jpeg)

#### **Course Roadmap**

Section 1: Miniature Engagement, Recon, and Scanning

Section 2: Scanning and Initial Access

Section 3: Post-Exploitation

Section 4: Domain Privilege Escalation and Lateral Movement

Section 5: Persistence and Evading Controls

Section 6: CTF and Next Steps

- Passwords and Credential Access
- Password Representations
- Obtaining Password Hashes

Lab 3.1: MSF psexec, hashdump, and Mimikatz

• Hashcat

Lab 3.2: Cracking Passwords with Hashcat

- Assumed Breach
- Command and Control (C2)

•

• Payloads

Lab 3.4: Payloads

- Post-Exploitation
- Situational Awareness
- Linux Situational Awareness
- Windows Situational Awareness
- Seatbelt

Lab 3.5: Seatbelt

- Privilege Escalation
- Linux Privilege Escalation
- Windows Privilege Escalation
  - Lab 3.6: Windows Privilege Escalation

40

# **Cracking with Hashcat**

- Hashcat is a cracking tool for CPUs and GPUs
  - − Speeds of millions of c/s (CPU) and billions of c/s (GPU)
  - − Available at www.hashcat.net, free and open source
- Implements 470+ password algorithms:
  - − LANMAN, NT, md5crypt, sha512crypt, and many others
- Uses OpenCL implementation to run on CPUs and graphics cards, but you need the specific drivers
- Available for Windows and Linux, with similar speeds
- Has a bit of a painful learning curve
  - − Does exactly as you ask, for better or worse
  - − Complex command line
  - − With many options for fine-tuning attacks

![](_page_41_Picture_14.jpeg)

Hashcat is a multithreaded tool for cracking passwords on CPUs and GPUs. It can achieve speeds in the billions of combinations per second for unsalted hashes on a CPU (tested with an AMD 5700G) and approximately 300 billion combinations per second on an RTX 4090 for the NT hashing algorithm, implementing more than 470 different password hash algorithms, including LANMAN, salted MD5, and more. That is a HUGE number of password algorithms.

Although most current GPU password cracking tools focus on CUDA on graphic cards, Hashcat takes advantage of the cross-platform capabilities of the Open Computing Language (OCL) and runs on CPUs and graphics cards where systems have the appropriate OCL drivers installed.

Hashcat runs on Windows and Linux, and Hashcat performance between the two operating systems is virtually identical.

![](_page_42_Picture_2.jpeg)

# **Hashcat: Specifying Hash Types**

- You'll usually specify the hash type using **-m** followed by a number
  - − The hash type is generally hinted at by the source (\$6\$, length, file format, source OS/app)
  - − Autodetection (no **-m**) is supported but requires a unique hash format
- To see supported algorithms: \$ **hashcat --help**
- The Hashcat wiki also lists the hash types, using **hashcat** as the password for each example hash

| #<br>  Name                                                                                                                                                                                                                 | Category                                                                                                                          |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------|
| ======+=================+=================<br>900   MD4<br>0   MD5<br>100   SHA1<br>3000   LM<br>1000   NTLM<br>1800   sha512crypt \$6\$   Operating System<br>25300   MS Office 2016<br>23400   Bitwarden<br>11600   7-Zip | Raw Hash<br>  Raw Hash<br>  Raw Hash<br>  Operating System<br>  Operating System<br>  Document<br>  Password Manager<br>  Archive |

When launching Hashcat, the user must invoke it with a command line argument of -m followed by a digit associated with the specific hash type. Common hash types include 500 for salted MD5 (md5crypt) used in many Linux implementations, 3000 for Windows LANMAN, and 1800 for salted SHA512 (sha512crypt) used in some Linux versions.

Unfortunately, there is no guaranteed way to know exactly what type of hashes you have by merely inspecting them. A 32-character hexadecimal value could be numerous different types of hashes, and without any other hints, it is impossible to know for sure what type it is. That said, we often have some hints. First off, if the hash is prepended with a dollar sign and a digit, such as \$6, that's a great indication that it is a salted SHA512 hash.

Alternatively, we can look at the length of the hash to get some possibilities of which group of hashes it comes from. Also, we can look at the file format. A pwdump file from a Windows machine has a very distinct format, with each line showing USERNAME:RID:LMHASH:NTHASH:::. A Linux /etc/shadow file likewise has a distinctive appearance. And finally, the best way to know the types of hashes we are dealing with is to look at the type of system from which they were extracted. 245

To see the multitude of hash types supported by Hashcat, you could simply invoke the tool with the --help option (or -h). It will show you over 470 different algorithms supported and their associated Hash-mode number.

The list on the slide above is an excerpt from the Hashcat wiki at https://hashcat.net/wiki/doku.php?id=example\_hashes.

![](_page_43_Picture_2.jpeg)

#### **Hashcat Potfile, Show, and Restore**

#### Hashcat has these features:

- **hashcat.potfile**: List of cracked hashes and cleartext passwords
- **--show** option: Compare hashes against potfile contents *instead* of cracking
- Hashcat will *not* crack hashes that are already cracked in the potfile
- **--restore**: Pick up where Hashcat left off last time

# **cat hashcat.potfile** 758424858d2c9f9e:A 3eacdee7e4395079:INTERNE af83dbf0052ee471:VIRGINI 09eeab5aa415d6e4:NEWPASS # **hashcat -m 3000 --show sam.txt** 38064695fe1449ebaad36435b51404ee:[notfound] a5c67174b2a219d1aad3b435b51404ee:[notfound] 09eeab5aa415d6e4aad3b435b51404ee:NEWPASS af83dbf0052ee4717584248b8d2c9f9e:VIRGINIA

Hashcat includes a potfile that contains all the hashes and passwords Hashcat has cracked so far, but its name is hashcat.potfile. This is a sensitive file because it includes cleartext passwords. As you might expect, Hashcat will *not* attempt to crack any hashes that are already cracked and included in the hashcat.potfile. Therefore, Hashcat includes a --show option to display which hashes from a given hash file have already been cracked because they appear in the potfile. In the screenshot above, you can see that Hashcat has already cracked the NEWPASS and VIRGINIA hashes, but it has "notfound" the other two hashes above it. For the INTERNE password, Hashcat has cracked the first seven characters of this LANMAN password, but the second seven characters remain "notfound".

Hashcat also includes a --restore feature so that you can resume execution where you last left off if you need to interrupt a given Hashcat run and start it again later.

![](_page_44_Figure_0.jpeg)

A really nice feature of Hashcat is the ability to support multiple dictionary files right at the command line by just placing the name of each file one after another. That way, we don't have to merge word list files and can keep them separate if they represent different types of information for our password guesses. It's important to note that Hashcat does *not* utilize usernames and GECOS fields to formulate its password guesses for the guess/encrypt/compare cycle of password cracking. But we can create a dictionary file of usernames and GECOS fields and feed them into Hashcat as a second and third dictionary file so that it will attempt those names as potential passwords. We'll do that in the next lab for usernames.

rules are multiplicative, not additive (100 rules x 100 rules = 10,000 variations of each password)

Hashcat also supports word mangling rules, but Hashcat comes with a multitude of rules files. In the next lab, we'll look at one of the most useful of those rulesets, called best66.rule.

At the Hashcat command line, we can specify one or more rules files by using the -r option, followed by the list of rules files we want Hashcat to utilize.

![](_page_45_Picture_2.jpeg)

### **Masks**

Hashcat allows masking and allows you to easily append or prepend letters, numbers, or other characters.

• Much simpler than writing custom rules

• Append with **-a 6**

• Prepend with **-a 7**

• Lowercase: **?l**

• Uppercase: **?u**

• Numbers: **?d**

• Special: **?s**

• All: **?a**

| Attack<br>mode<br>(-a) | Mode                                       |
|------------------------|--------------------------------------------|
| 0                      | Straight – no changes                      |
| 1                      | Combination – combine two dictionaries     |
| 3                      | Brute-force – all combinations in keyspace |
| 6                      | Hybrid Wordlist + Mask                     |
| 7                      | Hybrid Mask + Wordlist                     |
| 9                      | Association – Similar to JtR "Single"      |

Hashcat offers masking, which allows us to prepend or append characters before or after our password, respectively. We can use this with mode 6 (append) and mode 7 (prepend). Hashcat offers other attack modes as described above.

Hashcat has predefined character sets for use in mask attacks:

• Lowercase: **?l** • Uppercase: **?u** • Numbers: **?d** • Special: **?s** • All: **?a**

We can also define our own character sets with --custom-charset1 or -1 (one, not lowercase L). You can change the digit at the end to define more sets. For example, if you wanted to define a set of all uppercase, lowercase, and digits, you would use either of these options:

#### **-1 ?u?l?d**

**--custom-charset1 ?u?l?d**

45

# **Mask Examples**

- Append four digits at the end of each guess: **-a 6 ?d?d?d?d**
- Prepend four digits in front of each guess: **-a 7 ?d?d?d?d**
- Append two digits and a special character: **-a 6 ?d?d?s**
- Use the **--increment** option to vary the length
  - − By default ?d?d?d?d will try exactly four digits (0000-9999)
  - − This option will try 0-9, 00-99, 000-999, and 0000-9999

As discussed, we can use masks to easily make modifications to our passwords. Note that this will not replace characters (a → @). For that, we have to use rules.

To append four digits at the end of each guess, we would use: **-a 6 ?d?d?d?d**

To prepend four digits before each guess, we would use: **-a 7 ?d?d?d?d**

To append two digits and a special character to the end of each guess (a common format), use: **-a 6 ?d?d?s**

The mask options will use all the specified length, so ?d?d?d?d would try numbers in the range of 0000- 9999. It would not try 492 because that is only three digits in length. However, we can use the **- increment** option to vary the length. With this option and the mask of d?d?d?d, Hashcat will append 0- 9, 00-99, 000-999, and 0000-9999 to the end of each guess.

As you can see, this gives us a very simple way to modify our guesses and, ideally, crack more passwords!

46

![](_page_47_Picture_1.jpeg)

#### **Hashcat Status and Temp Sensor**

- As it is running, Hashcat provides a status indicator periodically or when you hit the **s** key
- Hashcat provides an estimate of how long the entire run will take in its status
- If your hardware supports a temperature sensor, Hashcat can abort if the system gets too hot

![](_page_47_Figure_6.jpeg)

As Hashcat runs, it provides a periodic status indicator, showing the percentage of progress it has made through the current run, as well as an estimate of its completion time for iterating through all the rules against the given dictionary file(s). By hitting the s key, you can also manually get the status from Hashcat. And if you hit the p key, Hashcat will cease operation, allowing you to invoke it again later with the --restore feature.

Also, if your system has an internal temperature sensor and installed drivers to interface with it, Hashcat can abort its run if it senses the machine heating up too much. That way, the entire system won't shut down if it overheats, but just Hashcat will stop running.

![](_page_48_Picture_2.jpeg)

#### **Pipal and Reporting on Cracked Password**

- In our report (discussed further in 560.4) we want to characterize the password we have successfully cracked
- Important password characteristics:
  - − Lengths of cracked passwords
  - − Common base words, such as password starting with "password", "qwerty", or the organization name
- Pipal from DigiNinja (Robin Wood) will perform this analysis.
  - − https://digi.ninja/projects/pipal.php
- You need to have already cracked passwords for mask generation to be more useful, such as a domain NTDS.DIT dump

Cracking passwords is a crucial part of penetration testing. We must not forget that we need to let the target organization know the common weaknesses in those passwords. Useful information includes the length of cracked passwords and any commonalities in the passwords, such as common passwords. Many users will select the same base word, such as "password", "qwerty", or the organization name.

To perform this analysis, we can use a tool such as Pipal. Pipal is written by DigiNinja (Robin Wood) and is available at https://digi.ninja/projects/pipal.php. For the analysis to be useful, we need to crack a significant number of passwords, usually by dumping the hashes from a domain controller and cracking a portion of those passwords.

![](_page_49_Picture_0.jpeg)

![](_page_49_Picture_1.jpeg)

# **Cracking Passwords with Hashcat**

Refer to the lab workbook for instructions.

![](_page_50_Picture_2.jpeg)

#### **Course Roadmap**

Section 1: Miniature Engagement, Recon, and Scanning

Section 2: Scanning and Initial Access

Section 3: Post-Exploitation

Section 4: Domain Privilege Escalation and Lateral Movement

Section 5: Persistence and Evading Controls

Section 6: CTF and Next Steps

- Passwords and Credential Access
- Password Representations
- Obtaining Password Hashes Lab 3.1: MSF psexec, hashdump, and Mimikatz
- Hashcat

Lab 3.2: Cracking Passwords with Hashcat

- Assumed Breach
- Command and Control (C2)

•

• Payloads

Lab 3.4: Payloads

- Post-Exploitation
- Situational Awareness
- Linux Situational Awareness
- Windows Situational Awareness
- Seatbelt

Lab 3.5: Seatbelt

- Privilege Escalation
- Linux Privilege Escalation
- Windows Privilege Escalation
  - Lab 3.6: Windows Privilege Escalation

![](_page_51_Picture_0.jpeg)

![](_page_51_Picture_1.jpeg)

#### **Assumed Breach**

In an Assumed Breach test, the target cedes access.

- The target gives the testers access to a single system
  - − Often with "degraded sensors"
- Saves time on the initial access
  - − Exploitation is dependent on the exploits that are currently available
  - − AV/EDR bypasses change almost daily (removes this from the scenario)
- Phishing can take a long time
  - − Is the phish not successful because of a payload issue, mail filtering, egress filtering, the user didn't click, or is the user out of office?
  - − There are far more internal services, more attack surface, and more findings

The Assumed Breach paradigm is a significant change in the way modern pen tests are performed. In this type of test, the defenders cede access to one of the systems on the network. This initial access is often on a system with "degraded sensors," where AV/EDR and other tools are turned off so that the initial payload works easily. This allows the testers to get access to one system and not have to worry about bypassing defensive tooling, even though they will, to pivot.

Initial access can take quite a while, and working exploits are much more difficult to come by. The lifetime of an exploit is much shorter these days due to better patching. Also, modern operating systems, runtimes, and compilers are much better at preventing a lot of the reliably exploitable vulnerabilities seen in years past.

The ceded access allows the testers to fast-forward to the interesting part of the test since initial access can be quite slow. Phishing can take a very long time. The testers may not know why a phish is unsuccessful. It could be due to a payload issue, mail filtering, egress filtering, the user not clicking, or the user simply being out of the office.

#### **What About Initial Access?**

- Initial access is important, but it can take a lot of time
- Fast forward to get the important results
  - − Saves time and money
- Focus on the organization's risk!
- Common counterarguments:
  - − But we have a hard outer shell!
  - − We have AV to prevent the phishing email from working!
  - − We have email filters!
  - − We train users not to click on links or give out sensitive information!

Testing the initial access mechanisms, which we discussed in 560.1, is still important. Similarly, the initial scanning can take a lot of time, too. You may encounter pushback by the target org with comments similar to:

- But we have a hard outer shell!
- We have AV to prevent the phishing email from working!
- We have email filters!
- We train users not to click on links or give out sensitive information!

Those are great defenses, but it only takes a single failure to end up with catastrophic results. If we assume one of these will fail, we can fast forward the test to save time and money, with the goal of more efficiently determining the risk to the organization.

![](_page_53_Picture_1.jpeg)

# **Access via 0-Day**

Focus on defending against initial access can be a bit misguided.

- "But you need to get in the real way"
- Focuses on the shell of the egg, not the yolk
- Attackers are in 5-6 months before they are detected
- What are you attempting to test?
- What if the red team doesn't get in?

There are more efficient ways to test many of these protections and detection methods

Focusing on defense against initial access is a bit misguided. Just because the protections prevent a pen tester today doesn't mean that will always be the case against all attackers. The world of security is very dynamic. New vulnerabilities and protection bypasses are regular occurrences. Real attackers may have more time and resources than allotted to the pen testers, so they may be able to bypass the defenses the pen testers could not.

The real reason this position is misguided is that attackers are routinely inside the organization for months. As pen testers, we need to test controls beyond these surface controls.

Ultimately, we need to go back to the real reason we are testing the security of the organization! What are the goals? Are the goals to test AV/EDR? Is it to test other preventive controls? Is it to test the detection capability of the blue team? We cannot answer these questions without input from the target organization, which is why penetration testing is always a collaborative effort.

![](_page_54_Picture_2.jpeg)

# **Test Assumptions**

| Action in Traditional Test          | Underlying Assumption                                      |
|-------------------------------------|------------------------------------------------------------|
| Plug attacker system into network   | Attacker has physical network access and<br>no credentials |
| Scan - Network and/or Vulnerability | Attackers are scanning                                     |
| Exploit                             | Attackers are pivoting using exploits                      |

A traditional pen test is useful, but doesn't always model the real risk

In a traditional internal penetration test, the testers are given access to the internal network via a VM, Dropbox, or laptop. From there, the testers scan the network looking for available services, and then the testers exploit those services.

Each of these actions presumes that an attacker has this type of access and will work in this way. In a traditional test, the testers plug into the network and do not have access. Is this a fair assumption? How often is an attacker going to have physical network access but not have credentials?

The next common step in a traditional pen test is scanning. The question is, how often and to what degree are attackers scanning? It should be pretty self-evident that attackers aren't running a full-blown vulnerability scanner on our network, meaning there is a difference in the way a real attacker and a tester are going to operate.

Finally, since the testers aren't given access or credentials, they need to exploit a system to gain initial access. The real question is, how often are malicious attackers doing this?

There is absolutely a need for a traditional penetration test, especially in less mature organizations. The question is, how can we change our test to make it even more valuable and to help organizations make better security decisions?

![](_page_55_Picture_0.jpeg)

54

![](_page_55_Picture_1.jpeg)

# **Analyzing Modern Attacks**

Focusing on defending against initial access can be a bit misguided

- "**Phishing** remains one of the top action varieties in breaches"
- "Phishing continues to walk hand-in-hand with use of **stolen credentials** as it has in the past"
- Around 20% of attacks involve **insiders**
  - − "This pattern is almost entirely insiders using their access maliciously to cause breaches"

In each of these cases, the attacker is effectively starting with access!

One of the best resources we have available on what attackers are doing is the Verizon Data Breach Investigations Report (DBIR). As a pen tester, our goal is to model the actions taken by real attackers. For years, the DBIR has shown that the most common way attackers are breaching networks is through phishing and stolen credentials. On top of that, around 20% of the breaches in North America and Europe involve an insider (in Asia, it is 6%).

In each of the three cases (phishing, stolen credentials, an insider), the attacker effectively starts with access. The intruder has valid credentials (or token) and access to one of the organization's systems.

#### References:

- 2021 Verizon DBIR: https://www.verizon.com/business/resources/reports/2021-data-breachinvestigations-report.pdfx
- 2022 Verizon DBIR: https://www.verizon.com/business/resources/reports/dbir/
- 2023 Verizon DBIR: https://www.verizon.com/about/news/mediaresources/attachment?fid=65e1e3213d633293cd82b8cb
- 2024 Verizon DBIR: https://www.verizon.com/business/resources/reports/2024-dbir-data-breachinvestigations-report.pdf

# **Moving On to Post-Exploitation**

- At this point, we have access
  - − Assumed Breach (client cedes initial access)
  - − Initial access on our own
- How we get here is part of the test design
  - − Some tests start without any access and then change to ceded access after a predefined duration
- Either way, after we have access, we move to post-exploitation
- Attackers will pivot once they have access to one system, and we must do the same to demonstrate risk
- Let's move on to Post-Ex!

After we have access, whether it be through ceded access or through traditional means, we begin the postexploitation phase. How we get here is part of the test design and should be part of the initial discussion. Some tests begin without any access and then change to ceded access if access is not gained within a predefined period of time. This is not a loss for the testers. Remember, the goal is to help the organization identify issues that could be exploited by an attacker.

Now that we have access, let's move on to post-exploitation!

![](_page_57_Picture_2.jpeg)

#### **Course Roadmap**

Section 1: Miniature Engagement, Recon, and Scanning

Section 2: Scanning and Initial Access

Section 3: Post-Exploitation

Section 4: Domain Privilege Escalation and Lateral Movement

Section 5: Persistence and Evading Controls

Section 6: CTF and Next Steps

- Passwords and Credential Access
- Password Representations
- Obtaining Password Hashes

Lab 3.1: MSF psexec, hashdump, and Mimikatz

• Hashcat

Lab 3.2: Cracking Passwords with Hashcat

- Assumed Breach
- Command and Control (C2)

•

• Payloads

Lab 3.4: Payloads

- Post-Exploitation
- Situational Awareness
- Linux Situational Awareness
- Windows Situational Awareness
- Seatbelt

Lab 3.5: Seatbelt

- Privilege Escalation
- Linux Privilege Escalation
- Windows Privilege Escalation
  - Lab 3.6: Windows Privilege Escalation

#### **What Is a C2 Framework?**

Command and Control (C2 or C&C) is used to remotely interact with compromised targets.

- Typically, a client/server model for communicating with a client
- Tends to feature the following:
  - − Maintain communications via HTTP(S), DNS, and other protocols
  - − Collect data from the target, such as hashes, passwords, and system info
  - − Identify additional targets
  - − Utilize lateral movement techniques
  - − Execute shell commands
  - − Deploy persistence mechanisms
- Often asynchronous (polling vs. persistent TCP connection)
  - − Beacons on regular intervals (often with jitter options)

Command and Control, also known as C&C or C2, is used to remotely interact with compromised targets. The tools run in a client/server model, where the clients (compromised systems) communicate back to a central server. The attacker uses the central server to send commands to and interact with the compromised systems. The communication can take place over any protocol, but most C2 uses HTTP(S) or DNS. C2 often includes additional tooling to collect data from targets such as hashes, passwords, files, network communication, and system information. The C2 often includes additional tooling to identify other targets and lateral movement options to allow an attacker to pivot to other systems. The lateral movement options use techniques described previously in this section.

The client (also "implant") has different names depending on the tools used. For example, Cobalt Strike

referred to as an implant or a Beacon (thanks to the prominence of Cobalt Strike).

The clients allow us to execute commands and shellcode of our choosing. Typical C2 also includes persistence mechanisms so the attacker can maintain access to the system upon reboot or if the implant dies.

Modern C2 uses asynchronous communication, meaning that the client connects back to the server at regular intervals to respond to requests and receive new tasks. To prevent the beaconing from being too regular (and more detectable), the C2 often includes "jitter," which allows for variance in the beaconing interval.

# **The C2 Matrix**

The C2 Matrix is a review of public C2, including a list of features, and allows you to select the best framework for your test.

- Select a C2 that best fits your adversary emulation plan and the target environment
- Not all C2 have the same features; you will sometimes need to make a tradeoff
  - − Example: Merlin supports HTTP3, but not multiplayer
- Available at thec2matrix.com
  - − Ask the Matrix: ask.thec2matrix.com

![](_page_59_Picture_10.jpeg)

The C2 Matrix is an attempt to review all the publicly available C2 Frameworks. It includes public (free) C2 as well as paid products. The goal is to help you select the best C2 for your needs—needs based on the target organization, target types, C2 features, communication methods, and test design. Not all C2 will have the features you need, and this tool helps you make the best tradeoff decisions. For example, at the time of this writing, no C2 supports HTTP3 and multiplayer (multiple team members sharing the server). Therefore, you need to make a choice. If you really need HTTP3, then Merlin is your only option, but it does not support multiplayer.

The Matrix includes an "ask" feature where you can select the options you need, and you'll see the list update with C2 that meets your requirements: ask.thec2matrix.com

![](_page_60_Picture_0.jpeg)

The data behind the C2 Matrix is stored in a Google Sheet. The original sheet was put together by SANS Instructor Jorge Orchilles (Verizon), Bryson Bort (Scythe), and Adam Mashinchi (CrowdStrike). Each C2 is reviewed by various evaluators. Not all C2 is fully vetted. If you'd like to contribute to the project, contact the author of this course (@JeffMcJunkin on Twitter) or Jorge Orchilles (@jorgeorchilles).

#### Reference:

• https://docs.google.com/spreadsheets/d/1b4mUxa6cDQuTV2BPC6aA-GR4zGZi0ooPYtBe4IgPsSc

![](_page_61_Picture_2.jpeg)

#### **Course Roadmap**

Section 1: Miniature Engagement, Recon, and Scanning

Section 2: Scanning and Initial Access

Section 3: Post-Exploitation

Section 4: Domain Privilege Escalation and Lateral Movement

Section 5: Persistence and Evading Controls

Section 6: CTF and Next Steps

- Passwords and Credential Access
- Password Representations
- Obtaining Password Hashes

Lab 3.1: MSF psexec, hashdump, and Mimikatz

• Hashcat

Lab 3.2: Cracking Passwords with Hashcat

- Assumed Breach
- Command and Control (C2)

•

• Payloads

Lab 3.4: Payloads

- Post-Exploitation
- Situational Awareness
- Linux Situational Awareness
- Windows Situational Awareness
- Seatbelt

Lab 3.5: Seatbelt

- Privilege Escalation
- Linux Privilege Escalation
- Windows Privilege Escalation
  - Lab 3.6: Windows Privilege Escalation

![](_page_62_Figure_0.jpeg)

Over the past several years, security researchers have released several different toolsets that give penetration testers post-exploitation capabilities for establishing persistence, escalating privileges, and performing lateral movement. Unfortunately, antivirus companies also have access to these tools and rapidly create detections for the payloads. Ronan Kervella and Joe Demesy of BishopFox set out to create a framework that could run on all major operating systems using Golang while also creating options to fully obfuscate implant payloads. Additionally, they open-sourced the entire project to encourage customization and crowd-sourced feature additions.

implant payloads. The C2 server is the central point for communications with the implants, the operator clients allow the penetration tester to interact with and issue commands to the implants, and the implants are the foothold on each victim machine. Testers can run the server and client on the same machine during tests, which would be very similar to how the Metasploit Framework msfconsole operates. Additionally, if

Separating the C2 server and operator client gives the tester more flexibility in how the post-exploitation framework is configured. Implant payloads operate in a similar fashion to Meterpreter payloads and can be produced in many file and binary formats, giving the penetration tester a wide variety of options.

- Cryptographically secure communication between agent on victim machine and C2 server on penetration tester's machine
- Supports multiple egress protocols TCP, mTLS, HTTP(S), and DNS
- Per-binary dynamic code generation and obfuscation
- A focus on pen test / red team operations

![](_page_63_Figure_6.jpeg)

pairs are created for each instance. This cryptographic communication prevents someone from determining commands and responses you issue to the implants and, even more importantly, does not allow a blue teamer to decrypt all implant communications if one implant is reverse-engineered and the RSA key is disclosed.

The implants can be configured to talk over TCP, mutual authentication TLS, HTTP, and HTTPS, as well as DNS.

implant will work through the list in order.

implant code will be dynamically generated to help get around antivirus signatures, which can help once code execution is achieved. The implants can also be obfuscated dynamically to further help with signature evasion. Both of these features are great when the blue team starts scanning their network, looking for additional hosts where the payload may have been used. Since each implant is unique, the chances of evading detection are higher.

# Image reference:

•

![](_page_64_Picture_2.jpeg)

#### **Features Supporting Offensive Operations**

- Payloads are named for easier tracking
- Secure communications over plaintext protocols
- Command and control via HTTP or HTTPS (proxy aware)
- Execute .NET Assemblies in memory
- Payload can contain full .NET Assemblies as modules
- Logs all commands and matches them with payload
- Team member management
- DNS Canaries to detect blue team interception of payload

transmission before embedding it in the plaintext protocol. If the blue team starts dissecting the network traffic, they will still encounter issues viewing the contents of the message due to the encryption.

environment variables relating to proxy configurations and configures the network traffic to conform to the proxy settings.

Another nice feature is the ability to execute .Net assemblies in memory through the implant. The assembly will be uploaded to the process space of the implant or whichever process you have migrated into and execute the assembly.

payload, giving the tester immediate access to the functionality of the assembly when the implant calls back. This feature can help when network communications to the implant need to remain small to fly under the radar.

command. Now the tester can precisely repeat actions during, for example, a purple team exercise. Individual operator accounts can be created and revoked at any time. Implants are also capable of also logs all commands run and their output for reporting purposes.

| the implant payload. Once the DNS record is implemented, any queries for that subdomain will notify the<br>tester that the payload has been burned. |  |  |  |  |
|-----------------------------------------------------------------------------------------------------------------------------------------------------|--|--|--|--|
|                                                                                                                                                     |  |  |  |  |
|                                                                                                                                                     |  |  |  |  |
|                                                                                                                                                     |  |  |  |  |
|                                                                                                                                                     |  |  |  |  |
|                                                                                                                                                     |  |  |  |  |
|                                                                                                                                                     |  |  |  |  |
|                                                                                                                                                     |  |  |  |  |
|                                                                                                                                                     |  |  |  |  |
|                                                                                                                                                     |  |  |  |  |
|                                                                                                                                                     |  |  |  |  |
|                                                                                                                                                     |  |  |  |  |
|                                                                                                                                                     |  |  |  |  |
|                                                                                                                                                     |  |  |  |  |
|                                                                                                                                                     |  |  |  |  |
|                                                                                                                                                     |  |  |  |  |
|                                                                                                                                                     |  |  |  |  |
|                                                                                                                                                     |  |  |  |  |
|                                                                                                                                                     |  |  |  |  |

![](_page_66_Picture_1.jpeg)

- Windows
  - − Portable Executable or Service Executable
  - − Dynamic Link Library (DLL)
  - − Shellcode
- Linux
  - − Executable Linkable Format (ELF) Executable
  - − Shared Dynamic Library
- MacOS
  - − Mach-O Executable
  - − Shared Dynamic Library

For Windows targets, both portable and service executables can be created. Service executables are created in such a way that the Windows Service Control Manager can start and stop as needed. These are necessary for using PSExec as well as creating binaries that can be used when creating Windows services for persistence/privilege escalation.

Windows DLL files can also be created for when you have DLL hijacking opportunities or need to simply invoke them with RunDLL32. Finally, position-independent shellcode can be created for any number of

embed the shellcode in application whitelisting bypass attacks.

to the target Linux host. Additionally, they can be used when you have write access to a service executable Linux and macOS.

or put in place of a service executable once you have access.

- Built-in Windows detection evasion features
- Execution Guard Rails
  - − Date and Time (execution before a set date and time)
  - − Domain Joined (checks the host for target domain)
  - − File Exists (checks for existence of file on target host at execution)
  - − Hostname (checks for configured hostname before allowing execution)
  - − Username (checks for correct username before allowing execution)
- Pivot configurations
  - − SMB Named Pipe
  - − TCP Pivot

There are also options to ensure that the payload is run by the intended target on the intended hosts within restricted access to the internet.

involves looking at the process that invoked a payload. For example, when Microsoft Word is executing PowerShell, a security analyst will often be alerted and will investigate. If we can spoof our parent process to show that a different application invoked PowerShell, we may be able to blend in with normal,

process in an attempt to remove antivirus or EDR hooks. AV and EDR hook Windows API calls that are often used in malicious activities to both block and alert on suspicious behavior. One way of removing these hooks is to load the native NTDLL in the current process so that the antivirus hooks are no longer present.

the who, when, and where of payload execution. The date and time limits are often used to make sure that

allowed to execute the payload based on if the host is domain joined, if the hostname configured during payload creation is correct, and if a particular file exists. For example, if the payload was executed by the blue team in a non-domain joined sandbox environment, the binary would not execute. The payload execution guard rails can also be configured to ensure that the correct user is executing the payload. Again, this would help ensure that the correct target executes the payload and not someone with whom the payload was shared or if the blue team had intercepted the file.

SMB named pipes or TCP. A common scenario for using this feature would entail gaining code execution

existing implant and tunnel through to the C2 server in a daisy-chain fashion. While this configuration depends on the initial implant maintaining a connection to the C2 server, chaining implant communications will cut down on the amount of traffic egressing to the C2 server, helping the tester's activities to blend in.

- **backdoor** Inject shellcode into target binary
- **getsystem** Escalate from high integrity to SYSTEM
- **make-token** Create logon session for given credentials
- **psexec** Execute binary on remote host
- **spawndll** Reflectively load DLL in target process
- **msf/msf-inject** Inject MSF payload in current/remote process
- **procdump** Create memory dump of target process
- **sideload** Execute shared/dynamic library or binary in target process

run. For example, you could backdoor Notepad.exe and then create a startup registry key to execute the backdoored binary each time the user logs in.

**getsystem** - While Meterpreter has a similar feature, the stock getsystem command is heavily signatured payload in the context of NT\_AUTHORITY\SYSTEM while also flying under the radar.

**make-token** - Meterpreter uses the Incognito module to view and steal tokens of current users on the

Windows functions will not work without a valid logon session.

**psexec** - Once you have compromised administrator-level credentials through memory dumps, migrating

each payload, chances are high that you will be able to gain code execution on the target while bypassing antivirus.

**spawndll** - SpawnDLL allows the tester to reflectively load a DLL into a target process. Whereas the sideload feature performs a similar task, SpawnDLL does not use RunDLL32 to execute the DLL. Instead, the DLL is loaded into the target process through a small bootstrap shellcode and then executed programmatically. In some cases, reflectively loading a DLL is stealthier than injecting a DLL and executing with RunDLL32.

injection into a target process for when you need a Meterpreter session's capabilities.

**procdump** - The procdump command can create a memory dump of any process once the correct permission levels are achieved. For example, once your session is in the context of NT\_AUTHORITY\SYSTEM, you can dump lsass.exe process memory and extract NTLM or plaintext credentials.

**sideload** - Sideload converts a shared/dynamic library or binary into shellcode, loads into the target process, and executes the shellcode. This can help with antivirus evasion when the sideloaded payload would be caught if it were to be written to disk.

# **Multiplayer**

Multiple team members can access the server and implants

- Server runs as a daemon when installed with the installer
- Use the executable to add new players
  - − In **/root** by default with installer; in Slingshot, it is in
- Generate a .cfg file, share it with the user, and then import

• Connect by running and select the team server

automatically allows multiplayer. You need to configure a user before you can remotely connect to the server. You need to run the executable to create a user. The installer puts the file in /root, but in Slingshot, the file is in . Change to the appropriate directory with cd and then run this command:

You must specify the username and the IP address of the server. You can optionally specify a filename to save the file. If you don't use this option, the file name is based on the username and the IP address, such as name\_1.2.3.4.cfg.

Securely transfer the configuration file to the user, then import the configuration.

Next, run the and select the target system.

# **Generating Payloads**

- **generate** command supports Windows PE, Windows DLL, Windows Shellcode, Mach-O (MacOS), and ELF (Linux)
- Includes Guardrails to limit scope or evade sandboxes

**-x, --limit-domainjoined** Domain-joined machines **-F, --limit-fileexists** Hosts must have this file in the filesystem **-z, --limit-hostname** Limit execution to specified hostname

**-y, --limit-username** Limit execution to specified username

Implants are obfuscated and encrypted, and it can take a few minutes to build a payload. To skip obfuscation, we'll use **--skip-symbols** or **-l** in the lab to be more efficient.

VPN connection is dynamic, we can't set up DNS for the VMs. As such, we can't use HTTP(S) or DNS for our communications. That's fine since the TCP communication method works just fine.

To generate a payload, we use the generate command. We can build a payload for Windows (PE, DLL, shellcode), macOS (Mach-O), and Linux (ELF).

scope as well as evading less sophisticated AV sandbox technology. The guardrails include:

-x, --limit-domainjoined Limit execution to domain-joined machines

-F, --limit-fileexists Limit execution to hosts with this file in the filesystem

-z, --limit-hostname Limit execution to the specified hostname

-y, --limit-username Limit execution to the specified username

The generated payloads are encrypted and obfuscated in an attempt to evade AV/EDR. The obfuscation routines can take a few minutes to run. While this obfuscation is useful in a real pen test, it will slow down the lab exercise. To make the lab more efficient, we will use the **--skip-symbols, --l**(lowercase L) option to disable obfuscation. You probably don't want to use this option in the real world; however, it is nice to use when learning the tool in a lab.

•

- (20+) and extensions (100+)
- Packages are downloaded from GitHub, so internet is required
- and *not* shared between clients when using multiplayer mode
- Experimental support for self-hosted armories

100+ extensions to provide more features and capabilities to the framework, such as Beacon Object File (BOF) and Common Object File Format (COFF) support.

and stored in .

individual package. If a second client wants to use the same alias or extension, that client would need to install the package, as well.

References:

• •

![](_page_74_Picture_2.jpeg)

- Armory packages are either extensions or aliases
- Extensions work in Windows implants by reflectively loading a DLL that can pass callbacks to the C2 server
- Aliases are wrappers around the sideload and execute-assembly commands
- To speed installation, extensions and aliases are grouped into bundles, which can be installed with armory install <bundle>

```
[*] Installing extension 'coff-loader' (v1.0.14) ... done!
[*] Installing extension 'scshell' (v0.0.1) ... done!
[*] Installing extension 'bof-servicemove' (v0.0.1) ... 
done!
[*] Installing extension 'winrm' (vo.0.1) ... done!
```

#### **TIP**

Use aliases rm and extensions rm to remove installed packages

the implant process through reflective loading. This technique allows for dynamic loading of the extension at runtime, enhancing the functionality and capabilities of the implant and allowing it to pass callbacks to the C2 server.

sideload or execute-assembly commands. Aliases

The sideload command enables loading and executing shared libraries directly into memory within a remote process on Windows and Linux. On macOS, it requires dropping a temporary file to disk. When done in memory, it can help bypass endpoint security controls like antivirus. The Windows capability is based on the sRDI utility.

The execute-assembly command executes .Net assemblies as a subprocess to the implant without writing to disk; however, the command only works on Windows. So, tools like Rubeus.exe or Seatbealt.exe, which normally trigger antivirus alerts, can be used with less risk of detection and without the need to obfuscate or recompile their code since they can be executed without touching the disk.

#### References:

•

• • https://github.com/monoxgas/sRDI

73

|                                                                                                                       | SEC560   Enterprise Penetration Testing | 74 |
|-----------------------------------------------------------------------------------------------------------------------|-----------------------------------------|----|
|                                                                                                                       |                                         |    |
| •<br>parameter parser<br>•                                                                                            |                                         |    |
|                                                                                                                       |                                         |    |
| •<br>The execute-assembly<br>command can accomplish the same result but<br>requires the path to the local executable: |                                         |    |
|                                                                                                                       |                                         |    |
|                                                                                                                       |                                         |    |
|                                                                                                                       |                                         |    |
|                                                                                                                       |                                         |    |

Remember that aliases are just wrappers around the sideload and execute-assembly commands. So, it's possible to use an alias or the command the alias wraps to accomplish the same task. Both

Reference:

•

![](_page_76_Picture_0.jpeg)

![](_page_77_Picture_2.jpeg)

#### **Course Roadmap**

Section 1: Miniature Engagement, Recon, and Scanning

Section 2: Scanning and Initial Access

Section 3: Post-Exploitation

Section 4: Domain Privilege Escalation and Lateral Movement

Section 5: Persistence and Evading Controls

Section 6: CTF and Next Steps

- Passwords and Credential Access
- Password Representations
- Obtaining Password Hashes

Lab 3.1: MSF psexec, hashdump, and Mimikatz

- Hashcat
  - Lab 3.2: Cracking Passwords with Hashcat
- Assumed Breach
- Command and Control (C2)

•

• Payloads

Lab 3.4: Payloads

- Post-Exploitation
- Situational Awareness
- Linux Situational Awareness
- Windows Situational Awareness
- Seatbelt

Lab 3.5: Seatbelt

- Privilege Escalation
- Linux Privilege Escalation
- Windows Privilege Escalation

Lab 3.6: Windows Privilege Escalation

![](_page_78_Picture_2.jpeg)

# **Features (Payloads), Not Bugs**

Attackers are using malicious payloads that aren't exploits to gain access to users' systems.

- Phishing using built-in functionality and social engineering is common for initial access, especially for targeted attacks
- Traditional exploits are less common than they once were for initial access
- Phishing and social engineering in conjunction with good pretext is highly effective
- Much of modern hacking is using existing features for remote access
  - − Features are often long lasting; bugs are not
  - − For example, Macros have been part of Microsoft Office for decades
- Payloads using built-in features are increasingly used by attackers
- This topic is covered later in this course

There is a growing trend of attackers using malicious payloads, instead of traditional exploits, to gain access to users' systems. The most common methods for initial access are phishing and social engineering, particularly when the attacker has a good pretext. Modern hacking often involves using existing features for remote access, as these are long-lasting, unlike bugs. Attackers are increasingly using built-in features,

a reason, and it can be difficult or impossible to remove them.

# **Common Payload Types**

- Office Macros
- Office Auto DDE
- ISO
- Zip file
- LNK file with rundll32

Payloads used by malicious attackers and penetration testers are most commonly:

**Office Macros** – Microsoft Office includes functionality to run VBA and custom code on the target user's system. Macros often need to be enabled on the user's system, which is why social engineering is used as part of the ruse to trick a user into running the macros

**Office Auto DDE** – Similar to Macros, but using a different technology

**ISO files** – ISO files are essentially zip files containing any files of our choosing. Modern user operating systems allow the user to double-click the file and mount the files. The mounted location can contain any number or types of other payloads.

**Zip file** – Similar to the ISO file, this zip file, commonly password-protected to bypass scanners, can contain any payload of the attacker's choosing

**LNK file with rundll32** – An LNK file doesn't include the malicious code but instead is a link to existing executables on the user's system with parameters to run the code of the attacker's choosing.

#### Reference:

• https://github.com/bhdresh/SocialEngineeringPayloads

![](_page_80_Picture_0.jpeg)

# **Using Macros**

- Macro-enabled Office 2007+ files end in "m" (docm vs. docx)
- Alternately, attackers may use Office 2003 formats (doc, xls)
- By default, macros are not blocked but can be blocked via GPO or ADMX
- Social engineering is commonly used to trick the user into enabling macros

![](_page_80_Picture_6.jpeg)

Macros are included in Microsoft Office as a tool for automation within all of the Office products, including Word, Excel, and PowerPoint. Despite the potential security risks, Macros are not blocked by default and must be explicitly disabled by administrators, usually through GPO. This feature is valuable for many organizations, particularly in certain departments such as finance. However, Macros can also be used by attackers to embed payloads and gain remote access. This often requires social engineering to convince a target to open the document and enable the Macros. Macro-enabled Office files from 2007 or later have the extension .docm or .xlsm, while Office 2003 formats use .doc or .xls.

To disable Macros, follow the guidance from Microsoft here:

"If you have Windows Server and Active Directory Domain Services (AD DS) deployed in your organization, you can configure policies by using Group Policy. To use Group Policy, download the most current Administrative Template files (ADMX/ADML) for Office, which include the policy settings for Microsoft 365 Apps for the enterprise. After you copy the Administrative Template files to AD DS, you can use the Group Policy Management Console to create Group Policy Objects (GPOs) that include policy settings for your users and for domain joined devices."

#### Reference:

• https://learn.microsoft.com/en-us/deployoffice/security/internet-macros-blocked

![](_page_81_Figure_0.jpeg)

Macros are written in VBA (Visual Basic for Applications). Macros used by attackers are commonly designed to load shellcode and execute it. The payload can be exported from various Command and Control (C2) frameworks, or a custom loader may be used with the shellcode. The default loader templates are often detected by AV/EDR and require custom changes to bypass these technologies.

![](_page_82_Figure_0.jpeg)

• As of 2017, some products don't support DDE anymore, but Excel and Outlook do still by default!

![](_page_82_Picture_2.jpeg)

The Dynamic Data Exchange (DDE) protocol enables applications to exchange or share data. DDE can also be used to execute arbitrary commands. As of 2017, some products no longer support DDE; however, Excel and Outlook still do by default. We can use this capability to run code of our choosing without a Macro or having the document renamed to the "m" version (xslm vs. xlsx).

#### Reference:

• https://dmcxblue.gitbook.io/red-team-notes/initial-acces/spear-phishing/macroless

![](_page_83_Figure_0.jpeg)

![](_page_83_Figure_1.jpeg)

The ISO file format was originally used to store uncompressed copies of optical drives, such as CDs or DVDs, and can be used to hold files of our choosing. Modern operating systems allow users to access the contents of an ISO file by simply double-clicking it. Attackers often take advantage of this by putting executables in the ISO file and tricking a user into opening it. The benefit to attackers is that this may not

![](_page_84_Picture_1.jpeg)

# **Zip File**

- Malicious zip files often contain executable format files
- Zip files may be encrypted, too, so email filtering software cannot read the contents of the file
  - − Downside: The user must take an extra step to access the files
- Similar attacks using other file formats, such as 7-Zip and RAR

Zip files are used to spread malware because they can be easily shared and executed. Additionally, the compressed nature of zip files, especially when encrypted, can make it easier for attackers to conceal the malicious code within, making it harder for security tools to detect and prevent the spread of the malware. They often contain executable format files and may be encrypted to evade detection by security tools. However, this also requires the user to take an extra step to access the files.

It is important to exercise caution when opening zip files from untrusted sources and to regularly update and use reputable defensive software to help protect against potential threats.

Similar attacks can also occur using other file formats such as 7-Zip and RAR.

![](_page_85_Figure_0.jpeg)

Attackers are using LNK (shortcut) files by embedding malicious code into them and distributing them through various means, such as email attachments, removable drives, and malicious websites. When the user clicks on the LNK file, it can launch an executable that infects the user's computer with malware. This type of attack is particularly dangerous because LNK files are often associated with trusted applications and can be easily disguised to look legitimate. To help protect against LNK-based attacks, it is important to be cautious when opening files from untrusted sources and to keep your security software up to date.

#### References:

- https://www.mcafee.com/blogs/other-blogs/mcafee-labs/rise-of-lnk-shortcut-files-malware/
- https://www.trendmicro.com/en\_us/research/18/j/malware-targeting-brazil-uses-legitimate-windowscomponents-wmi-and-certutil-as-part-of-its-routine.html (image credit)
- https://medium.com/@threathuntingteam/msxsl-exe-and-wmic-exe-a-way-to-proxy-code-execution-8d524f642b75

![](_page_86_Picture_0.jpeg)

![](_page_87_Picture_2.jpeg)

#### **Course Roadmap**

Section 1: Miniature Engagement, Recon, and Scanning

Section 2: Scanning and Initial Access

Section 3: Post-Exploitation

Section 4: Domain Privilege Escalation and Lateral Movement

Section 5: Persistence and Evading Controls

Section 6: CTF and Next Steps

- Passwords and Credential Access
- Password Representations
- Obtaining Password Hashes

Lab 3.1: MSF psexec, hashdump, and Mimikatz

• Hashcat

Lab 3.2: Cracking Passwords with Hashcat

- Assumed Breach
- Command and Control (C2)

•

• Payloads

Lab 3.4: Payloads

- Post-Exploitation
- Situational Awareness
- Linux Situational Awareness
- Windows Situational Awareness
- Seatbelt
  - Lab 3.5: Seatbelt
- Privilege Escalation
- Linux Privilege Escalation
- Windows Privilege Escalation
  - Lab 3.6: Windows Privilege Escalation

87

# **Post-Exploitation Activities**

- You've seen various methods for gaining access to targets
  - − These often result in some form of shell access, such as cmd.exe, Meterpreter, or /bin/sh
- Now what? Post-exploitation!
- You now analyze how to interact with that shell effectively so that you can explore the target system and use its resources to pivot and attack other systems
  - − Collectively, these activities are known as post-exploitation
  - − The penetration tester's goal is to better understand the business risk posed by the vulns that we've exploited successfully
  - − The pen test doesn't end when you get shell … that's just when things start to get interesting

The exploitation methods we've discussed so far are designed primarily to allow the penetration tester to gain some form of shell access to a target machine, such as cmd.exe, Meterpreter, or /bin/sh access. But after you have that access to targets, what do you do with it?

Collectively, the activities performed by a penetration tester after successfully exploiting a system are known as post-exploitation activities. They describe how to use shell access effectively so that you can explore the target machine and use its resources to attack other systems included in the scope of the project.

Our goal for post-exploitation activities is to gain a better understanding of the business risk posed by the vulnerabilities we've discovered and exploited. Remember, however, that you must always stay within scope and follow the Rules of Engagement for the project, and this is especially important in postexploitation. Do not get tempted to go outside of the scope or violate the Rules of Engagement.

A crucial idea to keep in mind when conducting penetration tests is that the test doesn't end when you get shell access to a target machine. Actually, that's just when the test gets far more interesting because you now have a chance to apply post-exploitation activities to better understand the business risk and significantly add value to your penetration test.

# **Post-Exploitation Tactics**

- Situational Awareness
- Persistence
- Privilege Escalation
- Defense Evasion: AV/EDR are getting much better
- Credential Access: Password and hash attacks
- Discovery: Find new targets
- Lateral Movement: Move to objectives
- Collection: Access data to help with objectives
  - − Remember, don't be the leak!
- Exfiltration: Use sample data; do *not* take real data!

Access to our first system is not the end of the test. It is only the beginning! So, what next, then?

We need to think back to the goals of penetration testing and the goals of our specific test. Not every test is the same, so you may need to do things differently on different tests. That said, let's go over the tactics that we may use in offensive operations and pen testing.

**Situational Awareness**—Understanding the network, domain, compromised host, and other systems on the network

**Persistence**—We use persistence to maintain access to a system should the system reboot, the service restart, or because a defender attempts to kick us out. Maintaining access like this is less common in a pen test and more common in a red team engagement.

**Privilege Escalation**—Use our existing access to escalate on a target system or in the domain

**Defense Evasion**—AV and EDR are getting much better at detecting and killing our payloads. We often need to tailor or modify tools to bypass these defensive tools.

**Credential Access**—This is a very broad category of password and hash attacks. We'll cover this in depth later in this course.

**Discovery**—As testers, we often need to move to gain access to our objectives, but first, we need to find the targets.

**Lateral Movement**—To complete our objectives, we need to move to other systems.

**Collection**—We need to gain access to goal data to demonstrate the risk. Demonstrate access to the data, but don't actually take the data. Remember, don't be a breach!

**Exfiltration**—Similar to above, we want to demonstrate the risk to the organization. We are sometimes tasked with exfiltrating data from the organization to see if the defenders can detect the leak. As stated before, don't be the breach! If you are going to exfiltrate data, make sure it is sample data; do *not* take real data!

# **Moving Files to a Target: Using File Transfer Services**

| HTTP(S)<br>TCP Port 80 or 443            | Common      | Command-line browser helpful, like<br>wget, Lynx, HTTrack, or<br>PowerShell's WebClient/wget |
|------------------------------------------|-------------|----------------------------------------------------------------------------------------------|
| SCP<br>part of SSH suite<br>TCP Port 22  | Common      | Often allowed outbound, using TCP<br>port 22 by default                                      |
| FTP<br>TCP 20 (data)<br>TCP 21 (control) | Common      | New OSes lack clients                                                                        |
| TFTP<br>UDP Port 69                      | Semi-common | Most systems include TFTP client                                                             |

To move files to a target machine, testers could rely on various services and their associated protocols that are designed to transfer files. Some of the most common mechanisms used to move files during penetration tests and ethical hacking follow:

**HTTP or HTTPS:** These protocols are almost always allowed outbound, using at least TCP ports 80 and 443. Even if they are sent through a web proxy, they can still be used to carry files. As testers, we often invoke text-based browsers on a compromised victim machine, using that browser to fetch files from the attacker's system and moving them back onto the target machine. Some useful text-based browser-style programs include wget, Lynx, and HTTrack. Also, PowerShell includes a WebClient feature and an alias of wget to pull files.

**SCP (Secure Copy):** This program is part of the Secure Shell (SSH) suite and transfers files using TCP port 22 by default. It is an ideal candidate for file transfers, given that a) it encrypts all authentication information and data in transit, b) most networks allow outbound SSH, and c) many Linux and UNIX systems have an SCP client built in.

**FTP:** This familiar service conveniently moves files using two connections: an FTP data connection associated with TCP port 20 and an FTP control connection associated with TCP port 21. FTP, when used in ASCII mode, corrects some issues with moving text files between different operating systems, as we'll discuss shortly.

**TFTP (Trivial File Transfer Protocol):** This stripped-down service moves files with no authentication between a tftp client and tftpd using UDP port 69.

# **Moving Files to a Target: Additional File Transfer Services**

- Additional services and protocols for moving files
  - − Windows File Sharing—NetBIOS / SMB
    - It could be useful to have the target machine mount a share on the pen tester's box, provided that outbound SMB is allowed from target to pen tester
    - With this approach, you can have the target access files without pushing them onto the target's hard drive
  - − NFS mounts
  - − Netcat
    - Is it installed? If not, this is a chicken-and-egg problem

![](_page_92_Picture_10.jpeg)

- − Others
- Must have appropriate client and server installed

Some additional file transfer services used by testers include:

**Windows File Sharing:** Of course, most Windows machines can use this means to move files across the NetBIOS protocol and/or the SMB protocol (TCP ports 135–139 or 445). Furthermore, Linux and UNIX machines support this kind of access using Samba, with commands such as smbclient, smbmount, and the Samba Daemon (smbd). It could be useful to have the target machine mount a share on the pen tester's box, provided that the network allows outbound SMB (or even NFS) access from the target to the pen tester's machine. With this approach, you can have the target system access files (such as scripts or executables) without pushing them onto the target's hard drive. Instead, the target just runs the given programs from their location on the mounted file share, giving the pen tester a much smaller footprint on the target machine.

**NFS (Network File System):** This protocol is most commonly used to move files between UNIX/Linux systems, although there are also Windows NFS implementations. By default, it uses TCP and UDP 204, although it may involve other ports as well.

**Netcat:** Netcat can move files back and forth between systems (among other functions) using arbitrary TCP or UDP ports. Unfortunately, to use Netcat to move a file, you first have to get the Netcat executable on the target machine. If it's already there, the tester can start using it. If it is not, you have to move Netcat's file first to use it to move additional files, resulting in a chicken-and-egg condition.

There are other mechanisms to move files, as well, but these are the most common and popular.

Note that to use any of these mechanisms, the target machine must have the appropriate client or server software installed, and the attacker's machine must have the other side (server or client) installed.

#### **Alternative Methods for File Transfer: Meterpreter, Paste, and Echo**

Use Meterpreter functions to move, view, or edit files:

```
meterpreter > upload local_filename
meterpreter > download remote_filename
meterpreter > cat remote_filename
meterpreter > edit remote_filename
```

- edit opens file in your Linux system's default editor (usually vim)
- Wrap Windows paths inside single quotes; otherwise, you need to escape backslashes

```
meterpreter > ls C:\Program Files\desktop.ini
  [-] stdapi_fs_stat: Operation failed: The system cannot find the file specified.
meterpreter > ls C:\\Program\ Files\\desktop.ini
100666/rw-rw-rw- 174 fil 2019-03-19 04:49:34 +0000 C:\Program Files\\desktop.ini
meterpreter > ls 'C:\Program Files\\desktop.ini'
100666/rw-rw-rw- 174 fil 2019-03-19 04:49:34 +0000 C:\Program Files\\desktop.ini
```

Beyond those traditional file transfer mechanisms, testers may also move files using less conventional means.

One helpful way to move files involves compromising a system using Metasploit to exploit some buffer overflow or other flaw and then loading the Meterpreter as a payload. The Meterpreter, as we discussed earlier, is a small shell environment. In recent versions of Metasploit, the Meterpreter includes several built-in commands for moving files, including **upload** and **download**, to send files to or from a compromised machine. The Meterpreter's **cat** command dumps a file to Standard Output on the screen. The **edit** command of the Meterpreter grabs the file and opens it in the default editor of your Linux machine, which is typically vim.

Even with a limited shell that doesn't implement a terminal, you can still create files by using the **echo** command to append things to a file, building a file line by line with >> redirects, as we covered earlier in our discussion of the terminal versus raw shell dilemma.

#### **Copy/Paste to Move Files**

- Simply **cat/type** the file, copy, then paste to your machine
- Binary data can't be copy/pasted this way, so use base64

```
$ cat file | base64
RmlsZSB3aXRoIG5ld2xpbmVzCnRhYnMJYW5kIGJ5dGVzOiCZmJcK 
$ echo "RmlsZSB3aXRoIG5ld2xpbmVzCnRhYnMJYW5kIGJ5dGVzOiCZmJcK" |
base64 -d > file
$ cat file
File with newlines
tabs and special bytes: <99><98><97>
                                                                   Source
                                                                   Destination
$ cat bigfile | gzip | base64
$ echo "long base64 string" | base64 -d | gzip -d > bigfile
                                                                   With 
                                                                   Compression
```

Copy/paste seems so obvious, but many forget about it because it is so simple. If the file to be transferred is a text file, simply display the contents of the file with **cat**, **type**, or any text editor and then copy and paste. It really is that simple!

Copy/paste works great for text files, but won't work with binary files, such as executables. In this case, we need to encode the file in a text format that can be copied and pasted. We can use the base64 command in Linux to accomplish this:

# \$ **cat** *file* **| base64**

RmlsZSB3aXRoIG5ld2xpbmVzCnRhYnMJYW5kIGJ5dGVzOiCZmJcK

The text output can then be copied and pasted into the command below:

### \$ **echo "RmlsZSB3aXRoIG5ld2xpbmVzCnRhYnMJYW5kIGJ5dGVzOiCZmJcK" | base64 -d > file**

We can then view the file and confirm that the special characters made it through:

# \$ **cat** *file*

File with newlines

tabs and special bytes: <99><98><97>

If the file is large, we may want to compress it before we copy it:

#### \$ **cat** *large\_file* **| gzip | base64**

H4sIAMKfUV8AA6TbC3xTZZYA8NsXFBEN7/KYIUUeBbG0vCyomCmgBRUjMlBxNUmTmyY0j0 ty

...truncated for brevity...

nRHeBpP4MzS93n6UsjvccsR3aYnref0XhZ1ykljaAQA=

Copy the long string and then decompress it, like this:

#### \$ **echo "***paste long base64 string here***" | base64 -d | gzip -d >** *largefile*

PowerShell can also be used to accomplish this task, but the commands are more verbose and are not shown here.

![](_page_96_Picture_2.jpeg)

#### **Course Roadmap**

Section 1: Miniature Engagement, Recon, and Scanning

Section 2: Scanning and Initial Access

Section 3: Post-Exploitation

Section 4: Domain Privilege Escalation and Lateral Movement

Section 5: Persistence and Evading Controls

Section 6: CTF and Next Steps

- Passwords and Credential Access
- Password Representations
- Obtaining Password Hashes

Lab 3.1: MSF psexec, hashdump, and Mimikatz

• Hashcat

Lab 3.2: Cracking Passwords with Hashcat

- Assumed Breach
- Command and Control (C2)

•

• Payloads

Lab 3.4: Payloads

- Post-Exploitation
- Situational Awareness
- Linux Situational Awareness
- Windows Situational Awareness
- Seatbelt

Lab 3.5: Seatbelt

- Privilege Escalation
- Linux Privilege Escalation
- Windows Privilege Escalation
  - Lab 3.6: Windows Privilege Escalation

96

#### **Situational Awareness Overview**

Situational awareness is understanding the target environment.

- Systems you have access to
- Systems you may move to
- Defenses such as AV/EDR, endpoint agents, and network defenses
- Monitoring policies and tools
- Domain settings and policies

Situational awareness is understanding the target environment. Once we gain access to our first system, we can learn a lot about the target environment. Some of the most important information to a pen tester is knowledge about the system we have access to since that system is likely going to be similar to other systems on the network. This helps us understand the environment when we need to move to other systems (lateral movement).

If one system has a specific AV/EDR or endpoint agent, it is likely that other systems will have the same. This can help us better evade these tools as well as understand which tools and techniques we should avoid or which may be more effective. We may adjust our techniques to be stealthier against specific defensive and monitoring tools.

![](_page_98_Picture_0.jpeg)

![](_page_98_Picture_1.jpeg)

# **File Pilfering**

When you compromise a machine, pilfer it and look for the goal data!

- Remember the goals discussed prior to beginning the test!
- Users will often copy sensitive data to their desktop or documents directory
- Look at shares accessible by the user
- Remember, we only need to pivot or escalate if we have to
- Source code
  - − Look for passwords and keys!
  - − Especially interesting for web servers; locally, we can analyze it for vulns
- Users' left-behind password.txt, docx, xlsx files on user desktops

Upon gaining access to a server, the first thing we should look for is the goal data. Remember, the first question we ask prior to conducting a penetration test is, "What data or process, if lost, stolen, compromised, or destroyed, would have the greatest impact to your organization?" The goal is demonstrating access to this data, not pivoting and escalating. We only need to pivot and escalate to get closer to the data. Look in the user's home, desktop, and documents directories. Also, look around shares that the user has access to. Remember, be careful not to download data such as PHI, PII, and other protected data. Even if you don't have access to goal data, there are plenty of other interesting things to look for.

If we gain access to a server, especially a web server, it may contain source code. We can look through that code for passwords and keys. We could, if time allows, analyze the code for security vulnerabilities. Be careful, this is a path that can take a lot of time and, if you aren't careful, could eat up valuable time in your pen test.

If you have access to a user system, look at the files on the user's desktop and documents for passwords. It is, unfortunately, all too common for users to store passwords in office documents on their desktop or documents directory.

![](_page_99_Picture_2.jpeg)

# **More Stuff to Pilfer: Targeting Information**

• Machines with which the compromised system has recently communicated

| Windows                                                      | Linux                                                              |
|--------------------------------------------------------------|--------------------------------------------------------------------|
| C:\> netstat -na<br>C:\> arp -a<br>C:\> ipconfig /displaydns | # netstat -natu<br># ss -t state established<br># arp –a<br># ip n |

- Additional system-specific information
  - − DNS servers: Zone files
  - − Web servers: Document root, especially local scripts
  - − Mail servers: Email address inventory, address aliases, sample of email that tester sent to it
  - − Many more possibilities here

On a compromised machine, a pen tester can also issue various commands to determine other systems with which the compromised machine has recently communicated. Such systems are more likely to be in scope, making these commands a handy way to inventory additional potential targets. On Windows, the tester could run the netstat -na command to see current TCP and UDP port usage, indicating which machines have an established TCP connection or have recently communicated with the box. The arp -a command dumps the system's ARP cache, showing the machines on the same subnet that the system has sent packets to in the last 10 minutes or so. Finally, the Windows ipconfig /displaydns command dumps the Windows DNS cache, showing recently resolved names, with a display including the remaining DNS

Linux and UNIX, the tester can run netstat -natu to see all TCP and UDP port usage, as well as arp -a to dump the ARP cache. Linux machines typically do not maintain an operating system-wide DNS cache.

Additional information items that a pen tester may want to consider grabbing include the zone files of a DNS server, which include information about names, IP addresses, and other tidbits. On a web server, the tester may want to grab all files and directories under the document root. On mail servers, the tester may consider grabbing files that contain an inventory of email addresses and aliases. The tester could even get a copy of an email the tester sends to an example account on the server to demonstrate the successful compromise of the mail server.

There are many more possibilities here, and penetration testers should think carefully about information resources to grab when they've successfully compromised a target machine.

![](_page_100_Picture_2.jpeg)

#### **Course Roadmap**

Section 1: Miniature Engagement, Recon, and Scanning

Section 2: Scanning and Initial Access

Section 3: Post-Exploitation

Section 4: Domain Privilege Escalation and Lateral Movement

Section 5: Persistence and Evading Controls

Section 6: CTF and Next Steps

- Passwords and Credential Access
- Password Representations
- Obtaining Password Hashes

Lab 3.1: MSF psexec, hashdump, and Mimikatz

• Hashcat

Lab 3.2: Cracking Passwords with Hashcat

- Assumed Breach
- Command and Control (C2)

•

- Payloads
  - Lab 3.4: Payloads
- Post-Exploitation
- Situational Awareness
- Linux Situational Awareness
- Windows Situational Awareness
- Seatbelt
  - Lab 3.5: Seatbelt
- Privilege Escalation
- Linux Privilege Escalation
- Windows Privilege Escalation
  - Lab 3.6: Windows Privilege Escalation

![](_page_101_Picture_2.jpeg)

#### **Accounts**

- Look at accounts in /etc/passwd
  - − Readable by all users (i.e., "world readable")
- Accounts may exist on other systems, useful for password guessing

```
$ cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
...
jason:x:1001:1001::/home/alex:/bin/bash
mike:x:1002:1002::/home/mike:/bin/bash
corey:x:1003:1003::/home/corey:/bin/bash
$ cat /etc/passwd | cut -d: -f1 > users.txt
```

One of the first things you should do when you access a Linux system is to look at /etc/passwd. It contains all the accounts on the system, and the file is readable by any user ("world readable"). You can use these account names for password guessing on other systems. Also, you may be able to escalate to one of the users on the compromised host.

# **Groups**

- Look at groups in /etc/group
  - − Readable by all users (i.e., "world readable")
- Look for users in interesting groups, such as root, sudo, and wheel

\$ **cat /etc/group** root:x:0: daemon:x:1: bin:x:2: sys:x:3: adm:x:4:syslog tty:x:5: ... sudo:x:27:corey

Groups are used to provide access to certain items on the system. Look specifically for users in groups like "sudo" and "wheel." You may find a user in the "root" group, but that would be rare and a likely security issue.

The sudo group allows users to use the sudo command, which effectively makes the user a root account. On some systems, the "wheel" group is used to allow access to the su command. Remember that the sudo command requires the current user's password, and su requires the target account (typically root) password.

![](_page_103_Picture_2.jpeg)

# **Find Interesting Files (1)**

- Look for interesting files (see 560.1 for more details)
- SETUID and/or SETGID files

```
find / -perm -4000 -o -perm -2000 -ls 2>/dev/null
```

• Writable configuration files

```
find /etc -perm -2
```

• Readable bash history files

```
find /home -name .bash_history -perm -4 2>/dev/null
```

The find command allows us to search the file system for objects matching our filter. We can look for SETUID and SETGID files with the following command:

```
$ find / -perm -4000 -o -perm -2000 -ls 2>/dev/null
```

The above command looks for files with SETUID (4000) and SETGUID (2000) permissions. The dash (-) before the numbers means to use the permissions as a mask, instead of the default that requires all the bits to be set exactly like the filter. The -ls shows the long listing of the file, and 2>/dev/null discards any error messages.

To find writable files, look for files with the "other" (world) read set (-2):

\$ find /etc -perm -2

We can also look in the /home directory for readable (-perm -4) .bash\_history files with this command:

\$ find /home -name .bash\_history -perm -4 2>/dev/null

![](_page_104_Picture_2.jpeg)

# **Find Interesting Files (2)**

• Files containing **passw** (password and passwd), **secret**, or **key**

|  |  |  |  | grep -Inri -e passw -e secret –e key /etc 2>/dev/null |
|--|--|--|--|-------------------------------------------------------|
|  |  |  |  |                                                       |

**I** Text files (no binary) **n** Show line number **r** Recursive search **i** Case insensitive **-e passw** Search string (matches password and passwd)

**-e secret** Search string **-e key** Search string

**/etc** Starting location, the /etc directory

We can also look around the files system for files containing specific strings (text). The grep command can be used to search text files only (I), show the line number of matches (n), perform a recursive search (f), and perform the search in a case-insensitive manner (i). We need to specify the search strings, in this case, "passw" (which matches both "password" and "passwd"), "secret", or "key". Finally, we specify the starting location and the files to look through (everything) with /etc.

\$ grep -Inri -e passw -e secret –e key /etc 2>/dev/null

104

![](_page_105_Picture_1.jpeg)

# **Local File Pilfering**

- Password representations (requires elevated permissions)
  - − UNIX/Linux: /etc/passwd and /etc/shadow or variants
  - − Windows: SAM database and cached credentials using Meterpreter hashdump
- Crypto keys
  - − SSH keys for ssh clients and sshd: Public and private keys
  - − PGP and GnuPG keys: Public and secret rings (check Rules of Engagement)

Only take data you need to use to achieve your objectives! If you don't need it, don't take it!

Password files can be especially useful so an attacker can crack them and use the resulting passwords to access other systems included in the project's scope. The Linux and UNIX /etc/passwd and /etc/shadow files can be immensely helpful, as is the Windows SAM database. We'll look at extracting these password files in far more detail later in this course.

Crypto keys stored on an end system can, likewise, be useful in demonstrating the risk of a vulnerability. The pen tester may access stored SSH public and private keys for clients and servers. Similarly, the public and secret keyrings of a Pretty Good Privacy (PGP) or Gnu Privacy Guard (GnuPG) installation may prove useful. Of course, the attacker would need the victim's passphrase associated with such keys to use them. However, because many users manually synchronize the passwords for their crypto keys with their operating system password, the tester may have already determined the associated password.

![](_page_106_Picture_2.jpeg)

#### **Course Roadmap**

Section 1: Miniature Engagement, Recon, and Scanning

Section 2: Scanning and Initial Access

Section 3: Post-Exploitation

Section 4: Domain Privilege Escalation and Lateral Movement

Section 5: Persistence and Evading Controls

Section 6: CTF and Next Steps

- Passwords and Credential Access
- Password Representations
- Obtaining Password Hashes

Lab 3.1: MSF psexec, hashdump, and Mimikatz

- Hashcat
  - Lab 3.2: Cracking Passwords with Hashcat
- Assumed Breach
- Command and Control (C2)

•

• Payloads

Lab 3.4: Payloads

- Post-Exploitation
- Situational Awareness
- Linux Situational Awareness
- Windows Situational Awareness
- Seatbelt

Lab 3.5: Seatbelt

- Privilege Escalation
- Linux Privilege Escalation
- Windows Privilege Escalation
  - Lab 3.6: Windows Privilege Escalation

| See all environment variables                    | C:\> set               |
|--------------------------------------------------|------------------------|
| See a specific variable                          | C:\> set variable_name |
| Similar (but not identical) to Linux/UNIX whoami | C:\> set username      |
| Show folders Windows searches for executables    | C:\> set path          |

When analyzing a system, penetration testers should note the environment variables that are set within the shell they use. Depending on how the shell was created (via an exploit, a remotely launched job, ssh, telnet, and such), it may or may not have these variables set. However, if they are set, this information is incredibly useful in understanding the machine in more detail. To see all environment variables set for the shell, you can simply type:

C:\> set

To see a specific variable's value, you can run:

C:\> set variable\_name

Two important environment variables are username and path. As its name implies, the username variable shows you your current logon name. The following is the closest thing we have to the Linux/UNIX whoami command on Windows:

C:\> set username

The path environment variable shows you where the shell searches in the file system to find the commands that you type. The value of your path can also give you a feel for ancillary packages installed on the machine, such as Python and QuickTime, which usually alter the default path settings. Check the path with:

C:\> set path

![](_page_108_Picture_1.jpeg)

# **Searching the File System**

• Search for a file in the file system:

C:\> **dir /b /s** *directory***\***file*

- No spaces between *directory*, **\**, and *file*
- **/s** means recurse subdirectories
- **/b** means bare form of output (do not look at ., .., and other items) and prints full path when used with **/s**
- Wildcards supported with **\***
- Example: To find **hosts** file anywhere under **C:\Windows**

C:\> **dir /b /s C:\Windows\hosts**

Penetration testers and ethical hackers often need to scour the file system to find a file with a given name. To accomplish this, you can use the humble dir command but with some specific syntax. Suppose you want to search for a file named *file* and see if it exists anywhere in the directory structure underneath *directory*, recursively going through that directory and its subdirectories. You could run:

C:\> dir /b /s *directory*\*file*

It is important to note that there are no spaces between the *directory*, the \, and the *file*. It's just one after the other. It may appear that such an invocation would look only for *file* in that one *directory*, but the /s makes it recurse subdirectories.

The /b tells dir to use the bare form of the output, not displaying the . link that each directory has to itself or the .. link it has to its parent. When used with /s, however, /b has an important function for us. It displays the full path to each file, something important to us when looking for a file. As you might expect, the filename can have a \* in it to act as a wildcard.

For example, look for the hosts file inside the systemroot using this syntax:

C:\> dir /b /s %systemroot%\hosts

This hosts file is important because it might give clues about other machines with which the given system communicates.

# **Managing Windows Accounts and Groups**

| List local users                       | net user                                         |
|----------------------------------------|--------------------------------------------------|
| List local groups                      | net localgroup                                   |
| List members of local admin<br>group   | net localgroup administrators                    |
| Add a user                             | net user logon_name password /add                |
| Add a user to the local admin<br>group | net localgroup administrators<br>logon_name /add |

With the ability to run commands on a target machine, penetration testers and ethical hackers often want to get a list of local users and groups on the target system, possibly adding new user accounts and manipulating the membership of various groups, such as the administrators group. To get a list of all local users defined on the machine, you can run:

C:\> net user

To see which local groups have been created on the machine, you could use:

C:\> net localgroup

Now, here's where things get interesting. You can see which accounts are members of the local administrators group with:

C:\> net localgroup administrators

The local administrator accounts on a given machine might be interesting because those same accounts might appear locally on other nearby systems, a useful thing for penetration testers and ethical hackers to analyze.

You can add a user to the system with this command:

C:\> net user *logon\_name password* /add

Then, you can add that user to the local administrators group with:

C:\> net localgroup administrators *logon\_name* /add

![](_page_110_Picture_0.jpeg)

The first command that can give us a surprising amount of information is net user *USERNAME* /domain. The output from this command will list the domain groups where our user is currently enrolled. Not only will the group names give away the employee's work function in the company, but they can also point you to fileshares and resources where your user will have access. Another interesting bit of information is the logon script location. In larger organizations, the logon script location can be many folders deep in the SYSVOL share. As we will see later, the SYSVOL share holds a lot of potential for credentials in plaintext files. Also, the logon script file extension can help with determining whitelisted file types for payload execution. Logon scripts usually use the file extensions CMD, BAT, and VBS but can also include whitelisted executables and user-writable locations. Finally, the output from net user can also reveal where user data is backed up. In virtual environments and commonly in larger organizations, we find that the user data folders are backed up to a central file share. We can check for access to other users' data folders here as well as access to backup files.

net1.exe was added as a new binary to address some issues with calling "net user/times" in older versions of Windows. While net.exe was fixed in future releases of Windows, net1.exe is still maintained as a compatible copy of net.exe's functionality for backwards compatibility with any scripts calling it explicitly. As such, one tradecraft decision could be to call net1.exe explicitly to avoid any potential detective controls for users calling net.exe. Of course, an explicit call of net1.exe could trigger another, more specific detective control! At a certain level of adversary capability, tradecraft decisions are tradeoffs, not a clear win.

#### Reference:

• https://web.archive.org/web/20140830150320/http://support.microsoft.com/kb/240195

![](_page_111_Picture_2.jpeg)

# **Local Groups**

- **net localgroup administrators**
  - − High-value domain group names
  - − High-value domain usernames
  - − Local groups or usernames
    - Local accounts are often set in gold image and copied throughout the domain

Another useful net command is checking localgroup memberships such as Administrators or Remote Desktop Users. We can quickly identify high-value domain users and group names in the administrators or RDP user local groups without having to send a query to the domain. Additionally, our list of target accounts and groups will start to develop here. We can also look for non-default local users or groups in these groups. Often, companies create a gold image to put in all of their virtual machines and physical hosts. These images will have default local accounts set with the same password throughout the domain. Once we escalate privileges, we can disclose the credentials and potentially perform lateral movement.

![](_page_112_Picture_2.jpeg)

# **Domain Groups**

# **net group** *GroupName* **/domain**

• List all users in the Domain Admin group "Domain Admins"

**net group "Domain Admins" /domain**

• List all groups

**net group /domain**

• Add a user to group (with Domain Admin or similar permissions)

**net group "Domain Admins"** *username* **/domain /add**

Similar to local groups, there are groups at the domain level. To get this information, we can use the net group command. To get a list of all groups, we can use:

net group /domain

To get the users in a group, we can specify the group name. For example, to get all the users in the "Domain Admins" group, use this command:

net group "Domain Admins" /domain

These commands may fail in some organizations, but it is still useful to attempt them.

![](_page_113_Picture_2.jpeg)

# **Deleting Windows Users and Accounts (Clean Up)**

- Maintain a written inventory of all changes you make on a system
  - − And remember to clean up after you finish!
- To remove a user from a group:

**net localgroup** *group logon\_name* **/del**

• To delete an account:

**net user** *logon\_name* **/del**

This is not part of situational awareness, but it's included here to better group the commands

As penetration testers and ethical hackers perform their work, they should make changes to the target systems only if the given Rules of Engagement for the project allow them to do so. Also, if changes are allowed, the testers need to document all changes carefully for each system and clean them up after testing is completed. The previous slide showed how to create users and add them to groups. To clean out such changes, a tester should first remove the users from the groups to which they were added by using this command:

C:\> net localgroup *group logon\_name* /del

Then, after all group memberships have been revoked, the account can be deleted with:

C:\> net user *logon\_name* /del

![](_page_114_Picture_0.jpeg)

![](_page_114_Picture_1.jpeg)

#### **Analyzing a System: Determining Firewall Settings**

- **netsh** interacts with the network settings of the machine
  - − Many contexts, view them in detail with **netsh /?**
  - − Includes such useful items as wlan, ipsec, dhcpclient, and more
- For penetration testers, the firewall context is important
- See the whole configuration of the built-in Windows firewall:

# **netsh advfirewall show allprofiles**

Another important aspect of a Windows system from the perspective of a penetration tester or ethical hacker is the built-in Windows host-based firewall. The firewall can be controlled at the command line using the powerful netsh tool. This command can control almost all aspects of Windows networking, including IP addresses, bridging, routing, and so on. To get an overview of its capabilities, you can run:

#### C:\> netsh /?

You can run netsh either as a standalone command with all its options on a single line or as an interactive console shell. To invoke the shell, simply type netsh. Then, you can type any of its contexts (such as firewall or interface) and enter /? to get details within that context.

We'll zoom in on the netsh advfirewall configuration because it is so vital to us as penetration testers and ethical hackers. To view the complete settings of the firewall, including allowed inbound ports and programs allowed to speak on the network, you could run:

# C:\> netsh advfirewall show allprofiles

Note that the netsh command shows only the settings of the built-in Windows firewall, not other thirdparty firewalls that may be installed on the system. The process for viewing the configuration of thirdparty firewalls is completely dependent on the particular package in use.

# **Analyzing a System: Displaying and Scraping Through Files**

| Print file contents on Standard<br>Output | type file                        |
|-------------------------------------------|----------------------------------|
| Look at multiple files                    | type *.txt<br>type file1 file2 … |
| Display output one page at a<br>time      | more file                        |
| Search for a string within a file         | type file   find /i "string"     |
| Search for regular expressions            | type file   findstr regex        |

Penetration testers and ethical hackers often have to display files on target machines and scrape through their contents, looking for specific items. We can accomplish these tasks with the type, more, find, and findstr commands.

The type command displays the contents of a file on Standard Output. It is the rough equivalent of the UNIX/Linux cat command and is used as follows:

C:\> type file

You can see the contents of multiple files by either using a wildcard of \* to refer to the files or listing the files one after the other in the type command line invocation, as follows:

C:\> type \*.txt

C:\> type file1 file2

Most interactions with the file system use \* as a wildcard to match any string.

To look at the contents of a file one page of output at a time, you can use the more command:

C:\> more file

Note that Windows does not have a less command built in, so the handy page up/page down and search features of less are unavailable to us using only built-in tools on Windows.

To look for lines of Standard Output that contain a given string, you can use the find command. By default, find looks for strings and is case-sensitive. With the /i option, it becomes case-insensitive. To search for a given string in a file, you could run:

C:\> type file | find /i "string"

Finally, if you are familiar with formulating regular expressions—powerful and flexible pattern-matching syntax—you can use the built-in findstr command, as follows:

C:\> type file | findstr regex

![](_page_117_Picture_2.jpeg)

# **Analyzing Windows: Interacting with the Registry**

• Reg command lets us interact with the Registry (including remotely!)

| Read                                           | C:\> reg query KeyName                            |
|------------------------------------------------|---------------------------------------------------|
| Change a reg key                               | C:\> reg add KeyName /v ValueName /t type /d Data |
| Export settings to a<br>text-based .reg file   | C:\> reg export KeyName filename.reg              |
| Import settings from a<br>text-based .reg file | C:\> reg import filename.reg                      |
| Save registry hive to<br>a binary hive file    | C:\> reg save HKLM\SAM C:\Windows\Temp\SAM.hive   |

- Run remotely by prepending **\\***MachineName* before *KeyName*
- Requires admin-level SMB session

Most Windows settings are stored in the Registry, so interacting with it from a command line is important to a penetration tester or ethical hacker working in Windows. The reg command allows for various kinds of read and write actions to and from the Registry. To read Registry keys, you could use the reg command, as follows:

#### C:\> reg query KeyName

To change Registry keys, you could use the add functionality of the reg command. If the key and value already exist, the reg add command will overwrite the value with our new value.

# C:\> reg add KeyName /v ValueName /t type /d Data

To grab a series of settings from the Registry, such as all keys and values defined underneath a given Registry key, you could use reg export, as follows:

# C:\> reg export KeyName filename.reg

It is a commonly observed convention to store these settings in a filename with a suffix of ".reg", although that is optional. Any suffix will do.

To import Registry key settings from a reg file, you could use:

# C:\> reg import filename.reg

To make any of these commands work remotely, we prepend \\MachineName before the KeyName. Such remote Registry access does require us to have an admin-level SMB session open.

117

![](_page_118_Picture_1.jpeg)

#### **PowerView**

- PowerView is part of PowerSploit
- Download a full list of AD Objects
  - − Users
  - − Computers
  - − Groups
- Offensive PowerShell scripts are often caught by AV when run as-is
  - − Obfuscate with Chameleon: https://github.com/klezVirus/chameleon
  - − **net** command equivalents are available if PowerShell is off the table

https://github.com/PowerShellMafia/PowerSploit/blob/master/Recon/PowerView.ps1

A great tool for anyone's toolbox is PowerView. This tool is written in PowerShell and should be considered the Swiss Army knife of Active Directory reconnaissance. For situational awareness, PowerView can pull a full list of Active Directory user, computer, and group objects with all attributes. While this can be noisy and is often caught by AV/EDR, the output can really help while trying to plan your next move. If PowerShell is not available due to application whitelisting restrictions, there are equivalent net commands. However, the net commands are not nearly as verbose.

Many PowerShell scripts used by pen testers are heavily signatured by defensive products. To use these tools, you may need to obfuscate the code. A tool like Chameleon will obfuscate the PowerShell script. According to the documentation, the tool does the following:

- Comment deletion/substitution
- String substitution (variables, functions, data types)
- Variable concatenation
- Indentation randomization
- Semi-random backticks insertion
- Case randomization
- Encoding

#### References:

- PowerView: https://github.com/PowerShellMafia/PowerSploit/blob/master/Recon/PowerView.ps1
- Chameleon: https://github.com/klezVirus/chameleon

![](_page_119_Picture_0.jpeg)

118

![](_page_119_Picture_1.jpeg)

# **Active Directory Explorer (AD Explorer)**

- AD Explorer is created and signed by Microsoft (via Sysinternals)
- All domain accounts can request full Active Directory layout
- Dump may contain passwords in notes/description fields
- AD Explorer has its own proprietary format, but it's been reverse-engineered for other tools (such as BloodHound, discussed later)
- Use in pen test: upload exe, execute, export, download file, then analyze locally

https://docs.microsoft.com/en-us/sysinternals/downloads/adexplorer

Another great tool for mapping out Active Directory is Sysinternals ADExplorer. First, Sysinternals tools are signed by Microsoft, so they are more likely to bypass application restrictions on your testing host. Also, any authenticated domain account can request a full dump of AD for offline review. Once the AD dump is available, ADExplorer can review the information in a GUI similar to AD Users and Computers. One thing to look for is credentials in the notes or description fields. We very commonly find passwords for service accounts in ADExplorer output. However, much like PowerView scans, ADExplorer dumps are very noisy and should be avoided if your assumed breach test is more on the stealthy side.

The output file from AD Explorer is not a common format. It has, however, been reverse-engineered to provide BloodHound-style data using ADExplorerSnapshot.py. This means that to use this on a test, you have to upload the executable, run it, export the results, pull the results back to your testing systems, and then analyze them with a local instance of AD Explorer.

#### References:

- https://docs.microsoft.com/en-us/sysinternals/downloads/adexplorer
- https://github.com/c3c/ADExplorerSnapshot.py

![](_page_120_Picture_2.jpeg)

#### **Course Roadmap**

Section 1: Miniature Engagement, Recon, and Scanning

Section 2: Scanning and Initial Access

Section 3: Post-Exploitation

Section 4: Domain Privilege Escalation and Lateral Movement

Section 5: Persistence and Evading Controls

Section 6: CTF and Next Steps

- Passwords and Credential Access
- Password Representations
- Obtaining Password Hashes

Lab 3.1: MSF psexec, hashdump, and Mimikatz

• Hashcat

Lab 3.2: Cracking Passwords with Hashcat

- Assumed Breach
- Command and Control (C2)

•

• Payloads

Lab 3.4: Payloads

- Post-Exploitation
- Situational Awareness
- Linux Situational Awareness
- Windows Situational Awareness

• Seatbelt

Lab 3.5: Seatbelt

- Privilege Escalation
- Linux Privilege Escalation
- Windows Privilege Escalation
  - Lab 3.6: Windows Privilege Escalation

![](_page_121_Picture_2.jpeg)

#### **Seatbelt and Overview**

- Seatbelt is a C# project that:
  - − *performs a number of security-oriented host-survey "safety checks" relevant from both offensive and defensive security perspectives.*
- Seatbelt is part of GhostPack and includes:
  - − Seatbelt
  - − SharpUp: C# port of various PowerUp cmdlets
  - − Certify: Enumerate and abuse misconfigurations in Active Directory Certificate Services (AD CS)
  - − SharpDump: C# port of PowerSploit's Out-Minidump.ps1
  - − SafetyKatz: Modified version of Mimikatz and PE Loader
  - − SharpWMI: C# implementation of various WMI functionality
  - − Rubeus: Toolset for raw Kerberos interaction and abuses
  - − More tools listed here: https://specterops.gitbook.io/ghostpack/

![](_page_121_Picture_15.jpeg)

Seatbelt is a C# project that "performs a number of security-oriented host-survey 'safety checks' relevant from both offensive and defensive security perspectives." Seatbelt is part of GhostPack, which includes the following tools:

- **SharpUp**—C# port of various PowerUp cmdlets
- **Certify**—Used to enumerate and abuse misconfigurations in Active Directory Certificate Services (AD CS)
- **SharpDump**—C# port of PowerSploit's Out-Minidump.ps1
- **SafetyKatz**—Modified version of Mimikatz and PE Loader
- **SharpWMI**—C# implementation of various WMI functionality
- **Rubeus**—A toolset for raw Kerberos interaction and abuses

Information on GhostPack can be found at https://specterops.gitbook.io/ghostpack/.

Seatbelt is licensed under the BSD 3-Clause license and is available at https://github.com/GhostPack/Seatbelt.

![](_page_122_Picture_1.jpeg)

# **Executing Checks**

• You can execute individual, multiple, or groups of checks

| Run one (or more) checks | Seatbelt.exe Command [Command2 …]                                 |
|--------------------------|-------------------------------------------------------------------|
| argument                 | Use quotes to run a check with an Seatbelt.exe "Command argument" |
| Run a group of checks    | Seatbelt.exe -group=groupname                                     |

- Quiet mode with **-q**
- Save text output with **-outputfile=***filename.txt*
  - − Saves JSON output if the extension is .json

We can execute an individual command using the command name, such as:

# seatbelt.exe AntiVirus

Or we can run multiple commands at the same time by putting them one after another, like this:

#### seatbelt.exe AntiVirus TCPConnections

If a particular command requires a parameter, we need to wrap the command and parameter in quotes so it is grouped together. For example, the LogonEvents command finds EventID 4624 (logon event) and displays it.

We can limit the number of days searched by specifying a number. The command below will show logon events within the last 30 days.

# seatbelt.exe "LogonEvents 30"

The commands are also combined into "groups." We can run a group of commands using -group and the group name. The command below will run all the commands in the "user" group.

# seatbelt.exe -group=user

By default, when you run Seatbelt, it will show an ASCII art seatbelt. This can take up a lot of terminal space. To suppress this, run the command with -q.

We can save the output by using -outputfile="filename.txt". If we change the extension to .json, the tool will save the output in JSON format.

![](_page_123_Picture_0.jpeg)

Seatbelt groups offer us a simple way to run a number of checks that target the same type of information. Below is a list of the groups and the information the checks target:

**System**: Runs the checks that find interesting information regarding the current system

**User**: Runs the checks that find interesting information regarding either the current user or all users, depending on the current access level.

**Elevated access**: checks on all users on the system

**Non-elevated access**: checks on the currently logged-in user only

**Remote**: Runs the checks targeting a remote system. Often used with the following options:

- -computername=host.domain.tld
- -username=domain\user
- -password=Password1

**Chrome**: Runs checks related to Google Chrome, including checking if Chrome is installed

**Slack**: Runs checks on Slack downloads, interesting files, and workspaces

**Misc**: Miscellaneous checks, not grouped by target. This includes checks on Outlook, Firefox, printers, the registry, Slack, PowerShell, and others

**All**: Executes all checks

![](_page_124_Picture_0.jpeg)

![](_page_125_Picture_2.jpeg)

#### **Course Roadmap**

Section 1: Miniature Engagement, Recon, and Scanning

Section 2: Scanning and Initial Access

Section 3: Post-Exploitation

Section 4: Domain Privilege Escalation and Lateral Movement

Section 5: Persistence and Evading Controls

Section 6: CTF and Next Steps

- Passwords and Credential Access
- Password Representations
- Obtaining Password Hashes

Lab 3.1: MSF psexec, hashdump, and Mimikatz

- Hashcat
  - Lab 3.2: Cracking Passwords with Hashcat
- Assumed Breach
- Command and Control (C2)

•

- Payloads
  - Lab 3.4: Payloads
- Post-Exploitation
- Situational Awareness
- Linux Situational Awareness
- Windows Situational Awareness
- Seatbelt
  - Lab 3.5: Seatbelt
- Privilege Escalation
- Linux Privilege Escalation
- Windows Privilege Escalation
  - Lab 3.6: Windows Privilege Escalation

![](_page_126_Picture_1.jpeg)

# **Why PrivEsc?**

Escalate on a system to extract more useful data.

- Extract password hashes or passwords
- Sniffing requires escalation
- Certificates can only be extracted with privileges
- Token attacks and extraction
- Impersonation of other users

In short, we escalate privileges because some goals require it!

We often need to escalate privileges on a system so we can access more useful information on a system. The following information is useful to penetration testers:

**Password hashes** – We need to escalate to gain password hashes and other password representations.

**Sniffing** – We may want to sniff data coming to or leaving the system to extract credentials, hashes, keys, and similar items. We can only sniff if we have elevated permissions.

**Certificates** – We may need a certificate to authenticate to another system; this can only be accessed as a privileged user.

**Token attacks** – We may want to steal a token to impersonate a user or system to gain access to another system.

**Impersonation** – If we have root/system level access, we can gain access to other users and the systems and data they have access to.

![](_page_127_Picture_2.jpeg)

#### **Course Roadmap**

Section 1: Miniature Engagement, Recon, and Scanning

Section 2: Scanning and Initial Access

Section 3: Post-Exploitation

Section 4: Domain Privilege Escalation and Lateral Movement

Section 5: Persistence and Evading Controls

Section 6: CTF and Next Steps

- Passwords and Credential Access
- Password Representations
- Obtaining Password Hashes

Lab 3.1: MSF psexec, hashdump, and Mimikatz

• Hashcat

Lab 3.2: Cracking Passwords with Hashcat

- Assumed Breach
- Command and Control (C2)

•

• Payloads

Lab 3.4: Payloads

- Post-Exploitation
- Situational Awareness
- Linux Situational Awareness
- Windows Situational Awareness
- Seatbelt

Lab 3.5: Seatbelt

- Privilege Escalation
- Linux Privilege Escalation
- Windows Privilege Escalation
  - Lab 3.6: Windows Privilege Escalation

Privilege escalation techniques vary by operating system. We'll now look at privilege escalation on Linux.

![](_page_128_Picture_1.jpeg)

# **Why Linux?**

Linux system vulns often exist longer than on Windows.

- Appliances often run Linux, and patches only come from the vendor
- Many organizations have a plan for managing/patching Windows systems, but planning isn't as robust for Linux systems
- Linux is often thought of as "not a real target"
  - − Often not integrated into Active Directory
- Malware for Linux is nearly non-existent (compared to Windows)

Linux systems are often thought of as less interesting targets by pen testers. Don't be afraid of these systems. We can still get a lot of useful information from them!

Many organizations treat Linux systems differently than Windows systems, especially if they are primarily a "Windows company." If the administrators don't have the same expertise on Linux systems, then they may neglect them. Linux is often thought of as "not a real target" since the quantity of malware for these systems is nearly non-existent when compared to Windows. The Linux systems are sometimes "one-off" systems designed for one specific task and are integrated into the organization's patching and vulnerability management program. These systems are often not part of the Active Directory and implement single sign-on (SSO). Of course, all this very much depends on the organization.

128

![](_page_129_Picture_1.jpeg)

# **Kernel Exploits**

Exploits in the Kernel lead to root-level access.

- Kernel runs with elevated permissions
- Trick the kernel into using bad data
- Match the kernel version with available exploits
  - − Searchsploit: https://www.exploit-db.com/searchsploit
  - − linux-exploit-suggester: https://github.com/mzet-/linux-exploit-suggester
- DirtyCow was a race condition that led to a privilege escalation

The Linux Kernel runs with elevated permissions. If you can exploit a flaw in the kernel, it will often lead to elevated access. We somehow need to give the kernel "bad data" that will trigger the vulnerability and lead to elevated access. To identify such an issue, we can use one of a number of tools that cross-reference a kernel version with known issues. We can run the tool on the system and get a list, or we can extract the version (with uname -a) and then find one or more vulnerabilities and possibly exploit code. Examples of such tools are:

- searchsploit: https://www.exploit-db.com/searchsploit
- linux-exploit-suggester: https://github.com/mzet-/linux-exploit-suggester

The DirtyCow (Dirty copy-on-write) vulnerability in 2017 was a flaw that led to privilege escalation in a very large number of Linux systems. This issue was due to a race condition in the copy-on-write mechanism in the kernel's memory subsystem. A common version of this exploit added a new user to the /etc/passwd file, which would allow an attacker to switch to the highly permissioned user.

129

# **Services Running as root**

We can escalate by exploiting a service running as root.

- Web, mail, file, and database servers sometimes run as root
  - − Especially on appliances and IoT devices
- Identify services running as root
  - − Exploit the service?
  - − Modify the configuration?
  - − Run as the service?

mysql> **create function do\_system return integer soname 'evil.so';**

**\$ ps aux | grep root**

• Less dangerous than kernel exploit since services will often auto-restart

We can escalate to root if we can cause a service running as root to execute a command on our behalf. Many services run as root, especially on IOT devices. To identify services running as root, we can look at the list of processes and filter for the "root":

\$ ps -aux | grep root

We can also check to see if we can modify the configuration file, commonly in /etc. We can also see if we can somehow interact with the service and have it execute an action of our choosing. In the example above, we are using the mysql shell to load a shared object (the Linux version of a DLL).

Once we identify the service, we can search for known vulnerabilities in the service. This is typically less dangerous than a kernel exploit since it won't crash the system, and some services will automatically restart if they encounter an error.

#### **World Writeable Files**

Look for editable configuration files.

- Sometimes an admin or installer will improperly set permissions on a configuration file, allowing any user to edit the file
- Configuration files are typically located in the **/etc** directory
- Look for files that are editable by the current user, throwing away all errors with this command:

# **find /etc –writable 2>/dev/null**

As mentioned on the previous page, we may be able to modify the configuration file of a privileged service and escalate. The configuration files are most commonly stored in the /etc directory. We can identify writeable configuration files with the following command:

#### \$ find /etc –writable

This command identifies files that are writable by the current user.

![](_page_132_Picture_2.jpeg)

#### **SETUID**

Exploit SUID executables to elevate access.

- SETUID executables run as the owner of the file
- SETGID works the same as SETUID, but for the group ID
- Denoted by an **s** instead of **x** for the execute bit (3rd location)
  - **-rwsr-sr-x 1 root root 51464 Feb 20 2018 /usr/bin/at**
- More recent kernels will ignore these bits when applied to scripts
- Find files with the SETUID bit set

```
$ find / -perm -4000 2>/dev/null
```

When a file is run that has the SETUID bit set, the execution runs with the privileges of the owner of the file. The SETGID bit works the same way, but with the group ID associated with the file. Most of the time, the owner of the file is the powerful root user, but not always. These files can be useful to escalate privileges if they can be exploited or tricked into executing a file or command of our choosing.

These files are denoted with an "s" instead of an "x" for the execute bit (3rd location). For example, the executable below has both the SETUID and the SETGID bits set.

```
$ ls -al /usr/bin/at
-rwsr-sr-x 1 root root 51464 Feb 20 2018 /usr/bin/at
```

To find files with the SETUID bit set, use the following command.

```
$ find / -perm -4000 2>/dev/null
```

The key is -perm -4000. Notice the dash before the 4000. The leading dash will search based on the mask, meaning the search does not care about any of the other bits. If you did not use the leading dash, the find command would look for files that only had the SETUID bit and no other permissions (no read, no write, no execute), which would be a very odd setting. Also, we are using the 2>/dev/null to discard error messages. When searching the entire file system, we will encounter portions that we, as a regular user, do not have access to. STDERR (file descriptor 2) is redirected to the /dev/null, discarding the output.

You can look for SETGID files with this command:

```
$ find / -perm -2000 2>/dev/null
```

Notice that the SETGID is one bit to the right (4 is bit position 2, 2 is bit position 1). If we want to look for files with both SETUID and SETGID, we can use this command with 6000 (4+2=6):

\$ find / -perm -6000 2>/dev/null

To look for a file with either (logical OR), we need to use a slightly more complex syntax and the logical OR (-o):

\$ find / -perm -4000 -o -perm -2000 2>/dev/null

![](_page_134_Picture_2.jpeg)

#### **GTFOBins**

GTFOBins is a curated list of executables (not exploits) that can be used to bypass local security restrictions in misconfigured systems.

- "The project collects legitimate functions of Unix binaries that can be abused to break out restricted shells, escalate or maintain elevated privileges, transfer files, spawn bind and reverse shells, and facilitate the other post-exploitation tasks."
- This is not a list of exploits
- Example with **find**
- GTFOBins can be found at https://gtfobins.github.io

| Break out of a restricted environment                                                                  | findexec /bin/sh \; -quit      |
|--------------------------------------------------------------------------------------------------------|--------------------------------|
| If the executable has the SETUID bit set it will not drop<br>privileges and can be used to get a shell | findexec /bin/sh \; -quit      |
| If the executable can be run with sudo, gain a privileged shell                                        | sudo findexec /bin/sh \; -quit |

Per the website, "GTFOBins is a curated list of Unix binaries that can be used to bypass local security restrictions in misconfigured systems. The project collects legitimate functions of Unix binaries that can be abused to <redacted> break out [of] restricted shells, escalate or maintain elevated privileges, transfer files, spawn bind and reverse shells, and facilitate the other post-exploitation tasks." This is not a list of exploits but rather files and creative ways to use the files that may be useful to a penetration tester.

Let's take a look at **find**.

You can use find to break out of a restricted environment with:

**find . -exec /bin/sh \; -quit**

If the executable has the SETUID bit set, it will not drop privileges and can be used to get a shell with:

**find . -exec /bin/sh \; -quit**

If the executable can be run with sudo, gain a privileged shell with:

**sudo find . -exec /bin/sh \; -quit**

#### Reference:

• https://gtfobins.github.io

![](_page_135_Picture_2.jpeg)

#### **Course Roadmap**

Section 1: Miniature Engagement, Recon, and Scanning

Section 2: Scanning and Initial Access

Section 3: Post-Exploitation

Section 4: Domain Privilege Escalation and Lateral Movement

Section 5: Persistence and Evading Controls

Section 6: CTF and Next Steps

- Passwords and Credential Access
- Password Representations
- Obtaining Password Hashes

Lab 3.1: MSF psexec, hashdump, and Mimikatz

• Hashcat

Lab 3.2: Cracking Passwords with Hashcat

- Assumed Breach
- Command and Control (C2)

•

• Payloads

Lab 3.4: Payloads

- Post-Exploitation
- Situational Awareness
- Linux Situational Awareness
- Windows Situational Awareness
- Seatbelt

Lab 3.5: Seatbelt

- Privilege Escalation
- Linux Privilege Escalation
- Windows Privilege Escalation
  - Lab 3.6: Windows Privilege Escalation

![](_page_136_Picture_0.jpeg)

![](_page_136_Picture_2.jpeg)

#### **Common Windows Privilege Escalation Flaws**

There are many Windows misconfigurations that can allow unprivileged users to obtain administrative privileges:

- DLL search order hijacking
- Unquoted paths with spaces
- Windows services where you can point to a new service executable
- Windows services whose files you can overwrite
- "AlwaysInstallElevated" registry key
- Unattended install files
- Group Policy Preferences

Even when Windows environments are configured according to a "least privilege" principle and the number of administrative accounts is restricted, a number of common Windows privilege escalation flaws could possibly allow an unprivileged user to obtain administrative privileges. Local privilege escalation issues could be caused by outdated/unpatched software that exposes the core OS to vulnerabilities. More often, however, they are the result of a number of misconfigurations or mistakes that adversaries can abuse. Below is a non-exhaustive list of some commonly used attack strategies:

- DLL search order hijacking
- Unquoted paths with spaces
- Windows services where you can point to a new service executable
- Windows services whose files you can overwrite
- "AlwaysInstallElevated" registry key
- Unattended install files
- Group Policy Preferences

#### **Unattended Install Files**

- Potentially contain credentials
  - − Local Administrator
  - − Domain credentials (to join Active Directory)
- Often, the credentials are stripped after setup (not always!)
- Presence of file indicates potential local user reuse
- Take a look for the original unattended install files on network shares, too!

**C:\Windows\sysprep\sysprep.xml**

**C:\Windows\sysprep\sysprep.inf**

**C:\Windows\sysprep.inf**

**C:\Windows\Panther\Unattended.xml**

**C:\Windows\Panther\Unattend.xml**

**C:\Windows\Panther\Unattend\Unattend.xml**

**C:\Windows\Panther\Unattend\Unattended.xml**

**C:\Windows\System32\Sysprep\unattend.xml**

**C:\Windows\System32\Sysprep\Panther\unattend.xml**

The first place to look when assessing the local file system is for unattend.xml files in C:\Windows\Panther. These files are used during an automated setup process and will often contain local administrator or domain credentials with administrator-level privileges to your host. Unfortunately for us, these credentials are often stripped out after the computer is finished setting up, but not always. Also, the presence of this file is a good indicator that the testing host was provisioned from a gold image and tells us that the local credentials are probably used elsewhere in the network.

![](_page_138_Picture_0.jpeg)

Unattended installs are ideal in larger organizations where it would be too time-consuming to perform wide-scale deployments manually. If Windows administrators fail to properly clean up after this process, an XML file called "Unattend.xml" is left on the local system. An example of such a file is included above.

As you can see, it includes the password in a base64 encoded format, which means it can be very easily decoded.

![](_page_139_Picture_2.jpeg)

# **Group Policy Preference Files**

- Group Policy Preferences (GPP) files will change a setting if there is no change, but can be overridden (unlike Group Policy Objects, GPO)
- Hosted on SYSVOL share of domain controller, which all domain accounts can read
- Can contain administrative credentials
- Credentials are encrypted, but Microsoft published the key!
  - − Plus, it's embedded into every copy of Windows since XP/2003

Microsoft's MS14-025 patch prevents the creation of new GPPs with passwords, but existing passwords are not removed.

In Windows domains, Group Policy helps to automate implementing user and host restrictions or permissions at logon. The Group Policy Preferences (GPP) files are hosted on the SYSVOL share of the domain controller and are accessible by any authenticated domain user. Microsoft implemented a way to insert administrator credentials in the GPP files without revealing the plaintext passwords by encrypting the credentials with an AES key. These credentials can be found by searching for the field "cpassword". The good thing for us, though, is that Microsoft released the decryption key in 2014. Multiple utilities exist for recovering the plaintext passwords for accounts stored in GPP files.

GPPs were used to allow administrators to create domain policies with embedded credentials. These policies allowed administrators to, for example, change local accounts or embed credentials for the purposes of mapping drives.

While highly useful, the storage mechanism used for such credentials is insecure: The GPPs are stored in XML files on the SYSVOL share (Windows domain share accessible to all domain users), and the password is stored encrypted with a known 32-byte AES key (it was published by Microsoft in an MSDN article at https://msdn.microsoft.com/en-us/library/cc422924.aspx).

Although Microsoft addressed this vulnerability in MS14-025, existing GPPs with passwords were not removed (this has to be performed manually). Let's see how we can find these!

![](_page_140_Figure_0.jpeg)

In order to find these passwords in an environment, you could run the following from any domainauthenticated user session:

PS C:\> Get-ChildItem -Path "\$env:LOGONSERVER\sysvol" -Filter \*.xml - Recurse | Select-String "cpassword"

This one-line command will search for the string "cpassword" in any .xml file under the domain controller's publicly accessible sysvol network share.

The screenshots on the slide provide some additional context:

The first screenshot shows an identified XML file with a cpassword value when it is opened using a normal text editor. Note the value for the "cpassword".

On the bottom left, we can see the encryption key used by Microsoft, which is published in an MSDN article.

Finally, on the bottom right, we see the GPP Metasploit module automatically extracting and decrypting the password.

Next to Metasploit modules, several PowerShell scripts exist that will do the same thing, which, of course, reduces the detection rate of such tools.

![](_page_141_Picture_0.jpeg)

Unquoted paths with spaces can be a serious issue in Windows Services configurations. Let's analyze an example.

The picture on the left is a screenshot of a recent VMware Tools service installed on a Windows 10 host. Note the "Path to executable" setting:

# "C:\Program Files\Vmware\Vmware Tools\vmtoolsd.exe"

The picture on the right is a screenshot of Video Stream server installed on a Windows 11 host. Note the "Path to executable" setting:

# C:\Program Files\VideoStream\1337 Log\checklog.exe

The VMware Tools service is clearly **not vulnerable** to the issue, as the executable path is properly delimited using quotes. The Video Stream service, however, raises some questions. Can you think of how we could abuse this?

![](_page_142_Figure_0.jpeg)

If the service path contains spaces and is not surrounded by quotation marks, then Windows has to guess where to find the service executable. Spaces in the command could either be part of the file path or indicate command-line arguments!

So, let's have a look at that Video Stream service. Windows tries to start this service "automatically" (so on boot). But how does Windows try to start the service? Given the space in the path name, there's an ambiguity here.

#### **Option 1**

*Logic:* Windows considers the space at the end of the filename and assumes everything that follows is an argument passed to that executable.

*File Path:* C:\Program

*Arguments:* Files\VideoStream\1337 Log\checklog.exe

#### **Option 2**

File Path: C:\Program Files\VideoStream\1337

Arguments: Log\checklog.exe

#### **Option 3**

*Logic:* The expected logic, Windows considers the space as part of the file path and finds the correct executable.

*File Path:* C:\Program Files\VideoStream\1337 Log\checklog.exe

*Arguments:* None

Windows will first attempt options 1 and 2. If we could thus write to "C:Program Files\VideoStream", we could create a program called "1337.exe" that would subsequently be executed with the privileges of the Video Stream service. In other words, every unquoted space in a path creates a new opportunity for Windows to stop parsing the path and use the remaining part of the path as one or more arguments.

Here is an excellent command line to find these types of services on our own machine (originally posted by Danial Compton):

wmic service get name,displayname,pathname,startmode |findstr /i "Auto" | findstr /i /v "C:\Windows\\" | findstr /i /v """

![](_page_144_Figure_0.jpeg)

Microsoft has continued to improve the security in each version of Windows and, as a result, has reduced the operating system's attack surface. One of the most common and widespread security holes has been the granting of administrator privileges to otherwise standard users, often to resolve application and configuration issues. User Account Control (UAC) is a security component that enables users to perform common tasks as non-administrators or as administrators without having to switch users, log off, or use Run As. User accounts that are members of the local Administrators group run most applications as a standard user. By separating user and administrator functions, UAC helps users move toward using standard user rights by default. So, if a user is logged on as a local administrator, then UAC disables a user's administrator rights and prompts the user when their administrator rights are required. If a user logs on as a standard user, they are asked to provide the credentials of an administrator account if they attempt to perform any task that requires administrator rights. Ultimately, the user has control over when to use administrator privileges, as UAC does not have the ability to be controlled via centralized policy.

By default, standard users and administrators access resources and run apps in the security context of standard users. When a user logs on to a computer, the system creates an access token for that user. The access token contains information about the level of access that the user is granted, including specific security identifiers (SIDs) and Windows privileges. With the built-in UAC elevation component, standard users can easily perform an administrative task by entering valid credentials for a local administrator account. The default built-in UAC elevation component for standard users is the credential prompt.

When an administrator logs on, two separate access tokens are created for the user: A standard user access token and an administrator access token. The standard user access token contains the same user-specific information as the administrator access token, but the administrative Windows privileges and SIDs are removed. The standard user access token is used to start apps that do not perform administrative tasks (standard user apps). The standard user access token is then used to display the desktop (explorer.exe). Explorer.exe is the parent process from which all other user-initiated processes inherit their access token. As a result, all apps run as a standard user unless a user provides consent or credentials to approve an app to use a full administrative access token.

![](_page_145_Picture_2.jpeg)

#### **UAC Levels**

Windows 7 and later offer four UAC levels.

| High            | Always notify – The user is notified before changes that require<br>administrative permissions are performed. |
|-----------------|---------------------------------------------------------------------------------------------------------------|
| Medium          | Only notify when programs/apps try to make changes to the<br>computer, but not when the user makes changes.   |
| Low             | Similar to the medium level, but the screen is not dimmed, and<br>programs can interfere with the UAC prompt. |
| Never<br>Notify | The UAC prompt will never notify when an app is trying to install or<br>make changes.                         |

Unlike Windows Vista, where you had only two options—UAC turned On or Off—in Windows 7 and above, there are four levels to choose from. The differences between them are the following:

High: Always notify. At this level, you are notified before applications and users make changes that require administrative permissions. When a UAC prompt shows up, the desktop is dimmed. You must choose Yes or No before you can do anything else on the computer. Security Impact: This is the most secure setting and the most intruding as well.

Medium: Notify me only when programs/apps try to make changes to my computer. This is the default level, and UAC notifies you only before programs make changes that require administrative permissions. If you manually make changes to Windows, then a UAC prompt is not shown. This level is less intruding, as it doesn't stop the user from making changes to the system; it only shows prompts if an application wants to make changes. When a UAC prompt is shown, the desktop is dimmed, and you must choose Yes or No before you can do anything else on your computer. Security Impact: This is less secure than the first setting because malicious programs can be created to simulate the keystrokes or mouse movements made by a user and change Windows settings.

Low: Notify me only when programs/apps try to make changes to my computer (do not dim my desktop). This level is identical to the one above, except that when a UAC prompt is shown, the desktop is not dimmed, and other programs are able to interfere with it. Security Impact: This level is even less secure, as it makes it easy for malicious programs to simulate keystrokes or mouse moves that interfere with the UAC prompt.

Never notify: At this level, UAC is turned off, and it doesn't offer any protection against unauthorized system changes. Security Impact: If you don't have a good security solution, you are very likely to encounter security issues with your PC. With UAC turned off, it is much easier for malicious programs to infect your computer and take control.

#### Reference:

• https://www.digitalcitizen.life/uac-why-you-should-never-turn-it-off/

# **UAC Bypass Techniques**

There is a lot of active research on UAC bypasses:

- Windows 7 and above (including Windows 11 25H2), iscsicpl.exe is vulnerable to a DLL search order hijacking vulnerability
- Many C2 frameworks have several bypass UAC techniques available, sometimes including social engineering: spawning a UAC window in a loop asking for admin rights
- An interesting tool that tries 30+ UAC bypass techniques can be found at https://github.com/hfiref0x/UACME

Next to these "high-level" configurations, group policies allow very fine-grained configuration of UAC. It's important to note that UAC can typically be bypassed:

In Windows 7, sysprep.exe (used to configure a Windows installation upon installation) is vulnerable to a DLL search order hijacking vulnerability, which is exploited by the Metasploit framework "bypassuac" module (for Windows 7).

PowerShell Empire uses several bypass UAC techniques. In hardened Windows 10 environments (without vulnerabilities), it will spawn a UAC window asking the user to "Permit" a legitimate looking system component from obtaining admin credentials (success rate depends on the UAC settings).

An interesting tool that tries 30+ UAC bypass techniques can be found at https://github.com/hfiref0x/UACME.

![](_page_148_Picture_2.jpeg)

# **Privilege Escalation Tools**

- **BeRoot**: Checks for common misconfigurations to find a way to escalate privileges (Windows, Linux, and macOS)
- **Watson**: .NET tool designed to enumerate missing KBs and suggest PrivEsc exploits (Windows only)
- **PowerSploit's PowerUp**: A pure-PowerShell script that will use the techniques mentioned previously (and much more) to try to escalate privileges (Windows only)!

The following three tools are very easy to run and use in order to find vulnerabilities in an environment.

BeRoot is a post-exploitation tool used to check common misconfigurations to find a way to escalate our privilege. It runs on Windows, Linux, and macOS. The GitHub repository is available at https://github.com/AlessandroZ/BeRoot.

Watson was developed by security researcher RastaMouse; it's a .NET tool designed to enumerate missing KBs and suggest exploits for useful privilege escalation vulnerabilities. Watson is available at https://github.com/rasta-mouse/Watson.

PowerSploit (and its PowerUp component) is a pure-PowerShell script that uses the techniques mentioned above (and much more) to try to escalate privileges.

148

# **PowerUp**

PowerUp is a collection of PowerShell modules primarily used for privilege escalation:

- Modifiable Service Binaries
- Writeable Registry Keys
- Autologon Credentials
- DLL Hijacking

Another great PowerShell utility from Will Schroeder (harmj0y) is PowerUp. This tool searches for common misconfigurations locally and reports on how to exploit them. For example, PowerUp searches for user-modifiable service binaries, writable registry keys where binaries would be executed automatically, autologon credentials stored in the registry, and DLL hijacking opportunities. While not all of these misconfigurations will lead to privilege escalation, they will, at the very least, give you persistence on the host. We use this tool on every test and, if we get stuck during the attack chain, on hosts where we've moved laterally.

![](_page_150_Picture_2.jpeg)

#### **LOLBAS**

Living Off The Land Binaries, Scripts, and Libraries (LOLBAS) is the Windows equivalent of GTFOBins.

• LOLBAS can be found at https://lolbas-project.github.io/.

| Download file | bitsadmin /create 1 bitsadmin /addfile 1<br>c:\data\playfolder\autoruns.exe bitsadmin /RESUME 1 bitsadmin<br>/complete 1                                                                                            |
|---------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Copy          | bitsadmin /create 1 & bitsadmin /addfile 1<br>c:\windows\system32\cmd.exe c:\data\playfolder\cmd.exe &<br>bitsadmin /RESUME 1 & bitsadmin /Complete 1 & bitsadmin /reset                                            |
| Execute       | bitsadmin /create 1 & bitsadmin /addfile 1<br>c:\windows\system32\cmd.exe c:\data\playfolder\cmd.exe &<br>bitsadmin /SetNotifyCmdLine 1 c:\data\playfolder\cmd.exe NULL &<br>bitsadmin /RESUME 1 & bitsadmin /Reset |

LOLBAS is the Windows equivalent of GTFOBins discussed earlier. GTFOBins can be found at https://gtfobins.github.io/.

Let's take a look at the bitsadmin executable and how it can be used by an attacker.

We can use bitsadmin to download a file:

bitsadmin /create 1 bitsadmin /addfile 1

bitsadmin /RESUME 1 bitsadmin /complete 1

Or to copy a file:

bitsadmin /create 1 & bitsadmin /addfile 1 c:\windows\system32\cmd.exe c:\\playfolder\cmd.exe & bitsadmin /RESUME 1 & bitsadmin /Complete 1 & bitsadmin /reset

To execute a file:

bitsadmin /create 1 & bitsadmin /addfile 1 c:\windows\system32\cmd.exe c:\playfolder\cmd.exe & bitsadmin /SetNotifyCmdLine 1 c:\playfolder\cmd.exe NULL & bitsadmin /RESUME 1 & bitsadmin /Reset

Or to execute a file in an alternate data stream:

bitsadmin /create 1 bitsadmin /addfile 1 c:\windows\system32\cmd.exe c:\playfolder\cmd.exe bitsadmin /SetNotifyCmdLine 1 c:\playfolder\1.txt:cmd.exe NULL bitsadmin /RESUME 1 bitsadmin /complete 1

![](_page_151_Picture_0.jpeg)

![](_page_151_Picture_1.jpeg)

# **Windows Privilege Escalation**

Refer to the lab workbook for instructions.

![](_page_152_Picture_0.jpeg)

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